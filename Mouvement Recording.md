# Current objective

With our first prototype, we wanted to record users' positions in png images.
The aim of creating these textures would be to use an AI model to generate content once users have finished interacting with the system. We planned to use a lightweight-gan model to compare images and find the one with the most similarities (or maybe either to enlarge the image and thus predict a possible continuation of the movement, or to generate a new image to have a virtual double).

# Mouvement Recording Technique #1

For this first prototype, it was arbitrarily chosen to capture the position of a person's face captured by the camera and to retranscribe this data onto a 64x64 pixel png image.

We thus obtain this type of images, where the red and green pixel colors are equivalent to the x and y position of the face on the video coordinates (normalized from 0 to 1). Finally, the blue color is equivalent to the model's detection score (the closer the value is to 1, the more certain the model is of having detected a face). 

![capture3](https://github.com/Cosamentale/TemporalSpace_Documentation/assets/43936968/01de0a0f-d746-45a3-92fb-c39c658735ea)
![capture2](https://github.com/Cosamentale/TemporalSpace_Documentation/assets/43936968/b466c456-1067-400a-b6ec-9202b0534a13)
![capture1](https://github.com/Cosamentale/TemporalSpace_Documentation/assets/43936968/4739447e-264e-4d7c-952e-eda9a1999fef)

This system is designed to capture real-time information on the movement of people in the interactive space, for use later in the experience.
However, to make it easier to test and generate content in advance, a version capturing the same data in relation to a video has been implemented. 

below is an example video of the system (mocap with PosNet):

https://github.com/Cosamentale/TemporalSpace_Documentation/assets/43936968/00593fda-fde7-4d97-b9a9-c7c8fc1c6246

Data is recorded along the x coordinates of the image, and once a line is completed, writing shifts by one pixel on y coordinates until the entire image is recorded.

Below you can see a vizualization in red of the order in which the pixels are currently read in the app.
![readingPattern](https://github.com/Cosamentale/TemporalSpace_Documentation/assets/43936968/9a4c3631-8357-4487-86c9-67dd8cab6a9a)

Below is an example of a script for reading data over time :
``` HLSL
// _resx, _resy are respectivly the resolution on x and y of the resulte image
// actually the _time float is not equal to the application time but to the frameCount wich is clamped at 60 fps

float2 u2 = float2(frac(_time  / _resx), frac((_time )/(_resx* _resyx)));
float3 result = tex2D(_PosTex, u2).xyz;
```

And here there is the code wich is use to write the texture :
``` HLSL
#pragma kernel CSMain
Texture2D<float4> reader; 
RWTexture2D<float4> writer;
SamplerState _pointClamp;
// _resx, _res, _time are the same that in the previous script
float _resx;
float _resy;
float _time;
// _pos contain the information of the x,y position of the head and on z the score of the detection (w is equal at 0)
float4 _pos;
[numthreads(8,8,1)]
void CSMain (uint2 id : SV_DispatchThreadID) 
{
	// coordonate of the pixels
	float2 f = float2(id.x,id.y);
	// resolution of the image
	float2 res=float2(_resx, _resy);
	// normalize coordonate
	float2 uv = f / res;
	// mask of the positon on x of the currently written pixel
	float mask1 = step(frac(_time / _resx), uv.x);
	// mask positon on y of the currently written pixel
	float mask2 = step(frac((_time -(_resx-1.)) / (_resx* _resy)),uv.y+1./_resy);
	// previous coordonates writed on the buffer
	float4 previous = reader.SampleLevel(_pointClamp, uv + 0.5 / res, 0);
	// adding the current coordonates the previous ones accordingly to the mask
	float4 result = lerp(previous, _pos,mask1*mask2);
	writer[id] = result;
}

```

# current problematics with this method

- At the moment, given that the values are recorded raw without any smoothing, it's probably pointless to record at 60 fps, as webcams have a lower fps.
  
- The image resolution (64x64 pixels) has been chosen arbitrarily, for the sole reason that it allows you to create a large set of images more quickly. With this resolution, it takes around 1min 14s to capture (64*64/60fps), compared with 4min 55s for a 128x128 pixel image (note that this time can double if you switch to 30 fps capture).
  Finally, one of the main questions that remains to be resolved before capturing a large set of images is which positions we want to use to train this model, as only the head seems too few, but conversely all the data ("nose", "leftShoulder", "rightShoulder", "leftElbow", "rightElbow", "leftWrist", "rightWrist", "leftHip", "rightHip", "leftKnee", "rightKnee", "leftAnkle", "rightAnkle") can make each image represent a much shorter time or be much larger.

- urrently, each member's position is relative to screenspace. This should probably be relative to the position of the center of the body, so that the position in space is not too 
mportant in relation to the type of movement (same for sacle).

With the aim of not getting stuck later, we're going to make a new 128 or 256 pixel image grouping all 13 members with relative positions
(seems more practical than separating all members into their own texture)

# Mouvement Recording Technique #2

This time, all the members are captured on the same texture of 256x256 pixels in a layout like this.

![capture1](https://github.com/Cosamentale/TemporalSpace_Documentation/assets/43936968/06b91177-a97a-46a0-a04c-e111d5ad953d)
![capture3](https://github.com/Cosamentale/TemporalSpace_Documentation/assets/43936968/deb82b0e-8851-4677-83ec-e4a786578b50)
![capture2](https://github.com/Cosamentale/TemporalSpace_Documentation/assets/43936968/608c032e-f87b-440b-b5ad-00d4c423b4f2)

with pixels read in this way (each color is a different member).

![readingPattern02](https://github.com/Cosamentale/TemporalSpace_Documentation/assets/43936968/6bd6b2d4-0a24-4059-b762-0a7a2b451738)

And positions are now all correlated to the pelvis, to avoid the frame position taking on too much importance in relation to the movement. 

https://github.com/Cosamentale/TemporalSpace_Documentation/assets/43936968/2a6e1d7c-8155-41d4-b6ae-b7aaaf34634a
Each white point is captured in real time, and the red lines are read in relation to the texture.

Each frame represents around 1min20 of movement (still at 60 fps for now). 



# Mouvement Recording 

For a first prototype, it was arbitrarily chosen to capture the position of a person's face captured by the camera and to retranscribe this data onto a 64x64 pixel png image.

We thus obtain this type of images, where the red and green pixel colors are equivalent to the x and y position of the face on the video coordinates (normalized from 0 to 1). Finally, the blue color is equivalent to the model's detection score (the closer the value is to 1, the more certain the model is of having detected a face). 

![capture3](https://github.com/Cosamentale/TemporalSpace_Documentation/assets/43936968/01de0a0f-d746-45a3-92fb-c39c658735ea)
![capture2](https://github.com/Cosamentale/TemporalSpace_Documentation/assets/43936968/b466c456-1067-400a-b6ec-9202b0534a13)
![capture1](https://github.com/Cosamentale/TemporalSpace_Documentation/assets/43936968/4739447e-264e-4d7c-952e-eda9a1999fef)

This system is designed to capture real-time information on the movement of people in the interactive space, for use later in the experience.
However, to make it easier to test and generate content in advance, a version capturing the same data in relation to a video has been implemented. 

below is an example video of the system:

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

At the moment, given that the values are recorded raw without any smoothing, it's probably pointless to record at 60 fps, as webcams have a lower fps.

# Current objective

The aim of creating these textures would be to use an AI model to generate content once users have finished interacting with the system.
We planned to use a lightweight-gan model either to enlarge the image and thus predict a possible continuation of the movement, or to generate a new image to have a virtual double.

At the moment, the image resolution (64x64 pixels) has been chosen arbitrarily, for the sole reason that it allows you to create a large set of images more quickly. With this resolution, it takes around 1min14s to capture (64*64/60fps), compared with 4 min55s for a 128x128 pixel image (note that this time can double if you switch to 30 fps capture).

Finally, one of the main questions that remains to be resolved before capturing a large set of images is which positions we want to use to train this model, as only the head seems too few, but conversely all the data ("nose", "leftShoulder", "rightShoulder", "leftElbow", "rightElbow", "leftWrist", "rightWrist",
"leftHip", "rightHip", "leftKnee", "rightKnee", "leftAnkle", "rightAnkle") can make each image represent a much shorter time or be much larger.

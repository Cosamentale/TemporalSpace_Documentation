
# Mouvement Recording 

For a first prototype, it was arbitrarily chosen to capture the position of a person's face captured by the camera and to retranscribe this data onto a 64x64 pixel png image.

We thus obtain images of this type, where the red and green pixel colors are equivalent to the x and y position of the face on the video coordinates( normalized from 0 to 1). Finally, the blue color is equivalent to the model's detection score (the closer the value is to 1, the more certain the model is of having detected a face). 

![capture3](https://github.com/Cosamentale/TemporalSpace_Documentation/assets/43936968/01de0a0f-d746-45a3-92fb-c39c658735ea)
![capture2](https://github.com/Cosamentale/TemporalSpace_Documentation/assets/43936968/b466c456-1067-400a-b6ec-9202b0534a13)
![capture1](https://github.com/Cosamentale/TemporalSpace_Documentation/assets/43936968/4739447e-264e-4d7c-952e-eda9a1999fef)

This system is designed to capture real-time information on the movement of people in the interactive space, for use later in the experience.
However, to make it easier to test and generate content in advance, a version capturing the same data in relation to a video has been implemented. 

ci-dessous une vidéo d'exemple du système:




https://github.com/Cosamentale/TemporalSpace_Documentation/assets/43936968/00593fda-fde7-4d97-b9a9-c7c8fc1c6246



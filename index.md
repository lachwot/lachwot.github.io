## *Lachlan Wotherspoon, Akansel Cosgun, Pamela Carreno-Medrano, Wesley Chan, Nicole Robinson and Dana Kulic*

Welcome to the main page for the Monash Tidy Tabletops Dataset. This dataset was developed by [Lachlan Wotherspoon](https://github.com/lachwot) as a part of the 2020-2021 Summer Research Program at Monash University, in our paper titled: "Tidy Tables: Generating Scenes Using Geometric and Semantic Preferences".

The dataset is currently being hosted on the Monash Bridges platform and is available for download [here](https://google.com)

### The Dataset
This dataset contains a total of 6000 pairs of RGB color images, of 10 tabletop objects arranged in random positions. Each pair of scenes contains either 2,3,4 or 5 objects of random classes. Between the pairs of scenes the pose of a single object is changed while the rest remain in the same positions. The dataset generator was produced using the [PyRep](https://github.com/stepjam/PyRep) API's for the [CoppeliaSim](https://www.coppeliarobotics.com/) robotics simulator.

Scene A | Scene B
| --- | --- |
![Example Scene 1](https://raw.githubusercontent.com/lachwot/lachwot.github.io/main/pair_5_31_1_scene.jpg) | ![Example Scene 2](https://raw.githubusercontent.com/lachwot/lachwot.github.io/main/pair_5_31_2_scene.jpg)
|Example Scenes|


### What can it be used for?
The aim of the dataset is to provide a stepping stone into the currently unexplored field of robotic object rearrangement (we've called it the "tidy up problem"). Most algorithms require both training data and validation data in order to gague and measure their performance. Each of the pairs of scenes has been given user ratings as to which of the two scenes they believe is tidier than the other, and each have metadata files relevant to the individual scenes. These ratings were the result of an [Amazon Mechanical Turk](https://www.mturk.com/) crowdsourcing survey involving all scenes in the dataset. The metadata files contain the classes of each object as well as the 6-DOF ground truth poses with respect to the world frame of each scene. The 6-DOF pose of the camera with respect to the world frame in each of those scenes is also included.

The 10 objects used also have pairwise _semantic_ preference ratings between each of them, which is a value between 0 and 1. 0 indicates a weak preference for a given pair of objects being together on the bench (i.e. the users do not prefer to pair them together), whereas 1 indicates a strong preference for the two objects being paired (they like having those two objects next to each other).

Any project requiring the use of labled data relating to which scenes are considered tidier than others, that require

---
## How can I download and use this dataset?
As mentioned earlier the dataset is available for public download from the Monash Bridges Platform: [(To be added)](https://google.com)
Under the root of the folder, the structure should be as follows (Add structure in here)

### Using the datset
To perform data analysis, you can use the `scene_parser.py` python file.
This file assumes the default directory structure (from the Monash Bridges download), so be sure to avoid changing that. 
The `scene_parser.py` file allows you to read the metadata files. Per object in each scene, the data is as follows:
- `class`: The class of the object, this will match one of the names of the objects in the object set.
- `position`: The 3DOF XYZ position of the object.
- `orientation`: The 3DOF Roll, Pitch, Yaw of the object.  

By default, this file will loop through each individual scenes (in the process looping through the _object count_,_pair number_, _scene number_ and all objects within the given scene. This can be modified to your needs. The crucial components are knowing the structure of the JSON object that is parsed from the `*_meta.json` files.

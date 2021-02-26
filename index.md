## *Lachlan Wotherspoon, Akansel Cosgun, Pamela Carreno-Medrano, Wesley Chan, Nicole Robinson and Dana Kulic*

You can use the [editor on GitHub](https://github.com/lachwot/lachwot.github.io/edit/main/index.md) to maintain and preview the content for your website in Markdown files.

Welcome to the main page for the Monash Tidy Tabletops Dataset. This dataset was developed by [Lachlan Wotherspoon](https://github.com/lachwot) as a part of the 2020-2021 Summer Research Program at Monash University, in our paper titled: "Tidy Tables: Generating Scenes Using Geometric and Semantic Preferences".

The dataset is currently being hosted on the Monash Bridges platform and is available for download [here](https://google.com)

### The Dataset
This dataset contains a total of 6000 pairs of RGB color images, of 10 tabletop objects arranged in random positions. Each pair of scenes contains either 2,3,4 or 5 objects of random classes. Between the pairs of scenes the pose of a single object is changed while the rest remain in the same positions. The dataset generator was produced using the [PyRep](https://github.com/stepjam/PyRep) API's for the [CoppeliaSim](https://www.coppeliarobotics.com/) robotics simulator.

Scene A | Scene B
| --- | --- |
![Example Scene 1](https://raw.githubusercontent.com/lachwot/lachwot.github.io/main/pair_5_31_1_scene.jpg) | ![Example Scene 2](https://raw.githubusercontent.com/lachwot/lachwot.github.io/main/pair_5_31_2_scene.jpg)

The aim of the dataset is to provide a stepping stone into the currently unexplored field of robotic object rearrangement (we've called it the "tidy up problem"). Most algorithms require both training data and validation data in order to gague and measure their performance. Each of the pairs of scenes has been given user ratings as to which of the two scenes they believe is tidier than the other, and each have metadata files relevant to the individual scenes. These ratings were the result of an [Amazon Mechanical Turk](https://www.mturk.com/) crowdsourcing survey involving all scenes in the dataset.

---
# How can I download and use this dataset?
As mentioned earlier the dataset is available for public download from the Monash Bridges Platform: [(To be added)](https://google.com)
Under the root of the folder, the structure should be as follows (Add structure in here)

Install the [CoppeliaSim robotics](https://www.coppeliarobotics.com/) simulator from the official [website](https://www.coppeliarobotics.com/downloads). Choose the version that best suits you and extract the tar file into your home directory.

Install [PyRep](https://www.coppeliarobotics.com/helpFiles/en/PyRep.htm), the Python interface for Coppeliasim from github:
```bash
# In your home directory,
git clone https://github.com/stepjam/PyRep.git
cd PyRep

# Adding to the '~/.bashrc' file
export COPPELIASIM_ROOT=EDIT/ME/PATH/TO/COPPELIASIM/INSTALL/DIR
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$COPPELIASIM_ROOT
export QT_QPA_PLATFORM_PLUGIN_PATH=$COPPELIASIM_ROOT

# Source your ~/.bashrc file and install the python library:
pip3 install -r requirements.txt
pip3 install .
```

### Using the datset
To regenerate scenes and perform data analysis, you can use the `scene_regenerator.py` python file. This is shown here:
This file assumes the default directory structure (from the Monash Bridges download), so be sure to avoid changing that. 

```python
# Scene regenerator script
# Lachlan Wotherspoon, Summer Research Program Monash Univerity

# Imports
import os
from os.path import dirname, join, abspath
from pyrep import PyRep
from pyrep.robots.arms.panda import Panda
from pyrep.objects.shape import Shape
from pyrep.objects.vision_sensor import VisionSensor
from pyrep.const import PrimitiveShape, RenderMode
from pyrep.errors import ConfigurationPathError
import numpy as np
import math
import time
import cv2
import random
import copy
from scipy.spatial.transform import Rotation
import json

# Preparing the simulator CoppeliaSim world file
SCENE_FILE = join(dirname(abspath(__file__)), 'SRP_Tidy_Tabletop_Scene.ttt')

# Launch the simulator interface PyRep
pr = PyRep()
pr.launch(SCENE_FILE)
pr.start()

# Setting the correct file paths
script_dir = os.path.abspath(os.path.dirname(__file__))

# Loading class names 
class_names_fname = script_dir + "/class_names.txt"
class_names_f = open(class_names_fname, 'r')
class_names = class_names_f.read() # Reads in alphabetical order
class_names = class_names.split("\n")
class_names_f.close()

# Determine number of objects in the scene, along with the pair number
num_objects = 5     # Between 2 and 5, inclusive
pair_number = 100   # Between 0 and 1000. Up to 3000 for two-object pairs/scenes. Inclusive.
scene_a_or_b = 1    # Either 1 or 2

meta_file = script_dir + f"/batch_data/batch_data_{num_objects}_meta/pair_{num_objects}_{pair_number}_{scene_a_or_b}_meta.json"

with open(meta_file) as json_file:
    scene_data = json.load(json_file)

    # Camera in the scene
    camera_res_x = scene_data['camera_config']['res_x']                         # Camera X resolution
    camera_res_y = scene_data['camera_config']['res_y']                         # Camera Y resolution
    camera_position = scene_data['camera_config']['position']                   # Camera world position
    camera_orientation = scene_data['camera_config']['orientation']             # Camera world orientation
    camera_render_mode = RenderMode.EXTERNAL                                    # Setting the render mode
    obj_camera = VisionSensor.create(resolution=[camera_res_x, camera_res_y],
                                use_local_lights=False,
                                position=camera_position,
                                orientation=camera_orientation,
                                render_mode = camera_render_mode
                            )                                                   # Generating the camera sensor

    # Table in the scene:
    table_position = scene_data['table']['position']    # Table world position
    table = Shape.import_shape(filename = script_dir + "/scene_objects/SRP_table.obj",
                                    scaling_factor=1,
                                    keep_identical_vertices=False,
                                    ignore_color=False,
                                    ignore_texture=False,
                                    reorient_bounding_box=False,
                                    ignore_up_vector=True)
    table.set_position([0.5, 0.375, 0.3]) # Setting the pose of the object far away
    table.set_respondable(True)
    table.set_detectable(True)
    table.set_collidable(True)
    table.set_renderable(True)
    table.set_dynamic(False)

    # Objects in the scene
    generated_objects = []
    for obj in scene_data["objects"]:
        obj_class = obj['class']                # Class of generated object
        obj_position = obj['position']          # Position of object
        obj_orientation = obj['orientation']    # Orientation of object
        generated_object = Shape.import_shape(filename= script_dir + "/object_set/" + obj_class + "/" + obj_class + ".obj",
                                        scaling_factor=1,
                                        keep_identical_vertices=False,
                                        ignore_color=False,
                                        ignore_texture=False,
                                        reorient_bounding_box=True,
                                        ignore_up_vector=True
                                    )           # Generating the object
        generated_object.set_position(obj_position)
        generated_object.set_orientation(obj_orientation)
        generated_object.set_renderable(True)
        generated_objects.append(generated_object)

    print("Scene regenerated.")

    # -------------------------------------------------------------
    # YOUR CODE HERE
    # All objects have been loaded at this stage.
    # You can perform analysis on the scene here.
    # -------------------------------------------------------------

    # --------------------------        
    # Can be removed if need be
    for i in range(100):
        pr.step()
    #  -------------------------


# Removing the objects and closing the simulation after performing analysis
for obj in generated_objects:
    obj.remove()
pr.stop()
pr.shutdown()   
```



### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/lachwot/lachwot.github.io/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://support.github.com/contact) and we’ll help you sort it out.

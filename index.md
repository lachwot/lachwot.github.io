## *Lachlan Wotherspoon, Akansel Cosgun and Dana Kulic*

You can use the [editor on GitHub](https://github.com/lachwot/lachwot.github.io/edit/main/index.md) to maintain and preview the content for your website in Markdown files.

Welcome to the main page for the Monash Tidy Tabletops Dataset. This dataset was developed by [Lachlan Wotherspoon](https://github.com/lachwot) as a part of the 2020-2021 Summer Research Program at Monash University.

The dataset is currently being hosted on the Monash Bridges platform and is available for download [here](https://google.com)

### The Dataset
This dataset contains a total of 6000 pairs of RGB color images, of 10 tabletop objects arranged in random positions. Each pair of scenes contains either 2,3,4 or 5 objects of random classes. Between the pairs of scenes the pose of a single object is changed while the rest remain in the same positions. The dataset generator was produced using the [PyRep](https://github.com/stepjam/PyRep) API's for the [CoppeliaSim](https://www.coppeliarobotics.com/) robotics simulator.

Scene A | Scene B
| --- | --- |
![Example Scene 1](https://raw.githubusercontent.com/lachwot/lachwot.github.io/main/pair_5_31_1_scene.jpg) | ![Example Scene 2](https://raw.githubusercontent.com/lachwot/lachwot.github.io/main/pair_5_31_2_scene.jpg)

The aim of the dataset is to provide a stepping stone into the currently unexplored field of robotic object rearrangement (we've called it the "tidy up problem"). Most algorithms require both training data and validation data in order to gague and measure their performance. Each of the pairs of scenes has been given user ratings as to which of the two scenes they believe is tidier than the other, and each have metadata files relevant to the individual scenes. These ratings were the result of an [Amazon Mechanical Turk](https://www.mturk.com/) crowdsourcing survey involving all scenes in the dataset.

---
# How can I download and use this dataset?
As mentioned earlier the dataset is available for public download from the Monash Bridges Platform: (To be added)



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

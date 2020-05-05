# Motivation
Studies of how human brain processes visual information (visual psychophysics, visual neuroscience) often use simple visual stimuli (lines, simple shapes, etc.). Although there are studies on more naturalistic stimuli such as face or natural scenes, they are pretty limited because it is difficult to manipulate, quantify and control the complex high-dimensional attributes of those stimuli. In this project, I aim to solve several of those problems using the state-of-the-arts deep learning methods. I will focus on face stimuli although this can be applied readily to other kinds of stimuli. Here's the summary of the problems:
* Create random look-like-real fake faces. In visual experiments, people either use real face images or synthetic images. For real face images, that does not meet the gold standard of randomization in experiment. For synthetic images, they often don't look like real.
* Manipulate a certain feature in the image (e.g. gender) while keeping all other features the same. Again, this is the gold standard of experimental design but it's really hard to do with the standard image processing techniques (think of Photoshop or even GIMP).
* Quantify the attributes in the images.

# Problem 1: Create random look-like-real face images
To generate fake face images, we use the [style-GAN](https://github.com/NVlabs/stylegan) pretrained network from Nvidia group. The details can be found in their paper and the github page. Here I just provide brief summary of key points.
![](/figures/stylegan_base.PNG)

![](/figures/example_fake_face.PNG)

# Problem 2: Manipulate a feature while keeping others constant
![](/figures/face_manipulation.PNG)

![](/figures/stylegan_high.PNG)
![](/figures/stylegan_low.PNG)

![](/figures/example_face_mixing.png)

# Problem 3: Quantify the attributes in the image
## Low-level attributes
![](/figures/example_pixelDiff_pca.png)
![](/figures/example_lowlevel_pca_more.png)
![](/figures/l2_lowChange.png)
![](/figures/histogram_low_high_flip.png)

## High-level attributes
![](/figures/facenet_architecture.PNG)
![](/figures/example_facenet_pca.png)
![](/figures/example_facenet_pca_more.png)
![](/figures/l2_highChange.png)





# Motivation
Studies of how human brain processes visual information (visual psychophysics, visual neuroscience) often use simple visual stimuli (lines, simple shapes, etc.). Although there are studies on more naturalistic stimuli such as face or natural scenes, they are pretty limited because it is difficult to manipulate, quantify and control the complex high-dimensional attributes of those stimuli. In this project, I aim to solve several of those problems using the state-of-the-arts deep learning methods. I will focus on face stimuli although this can be applied readily to other kinds of stimuli. Here's the summary of the problems:
* Create random look-like-real fake faces. In visual experiments, people either use real face images or synthetic images. For real face images, that does not meet the gold standard of randomization in experiment. For synthetic images, they often don't look like real.
* Manipulate a certain feature in the image (e.g. gender) while keeping all other features the same. Again, this is the gold standard of experimental design but it's really hard to do with the standard image processing techniques (think of Photoshop or even GIMP).
* Quantify the attributes in the images.

# Problem 1: Create random look-like-real face images
To generate fake face images, we use the [style-GAN](https://github.com/NVlabs/stylegan) pretrained network from Nvidia group. The details can be found in their [paper](https://arxiv.org/abs/1812.04948) and the github page. Here I just provide brief summary of key points. A key point in this GAN network is that the latent vector **z** (1x256) is not fed directly to the network but it first goes through a mapping network (fully-connected dense layers) that outputs an embedding representation **w** (18x256). Importantly, each row of **w** is fed directly to 18 layers of the generator. Because the generator was trained in such a way that the layers separately represent high-level to low-level features ([progressive GAN](https://arxiv.org/abs/1710.10196)), we can separately control for low- or high-level feature (more on this in the next section). Here is a sketch of style-GAN taken from the original paper: 
![](/figures/stylegan_base.PNG)

So basically, we just need to feed the netword a random vector **z** and it will output an image. Here are some *good* examples which look really impressive:
![](/figures/example_fake_face.png)

Also, note that some images generated by the networks have some artifacts so I had to carefully examine the images to make sure they are generally ok. Recently, the same group published an updated version that deals with those issues. If you are interested, you can find it [here](https://github.com/NVlabs/stylegan2).

# Problem 2: Manipulate a feature while keeping others constant
Ok now we have really good fake images, the next question is how can we manipulate certain feature while keeping others constant. Note that here for simplicity I just want to deal with either low or high-level changes. Here is a good example of what we want to achieve:
![](/figures/face_manipulation.PNG)

We start with an image and want to change only low-level features (skin tone, hair or eye color, etc.) while keeping high-level feature (e.g. identity) the same or vice versa. As mentioned in the previous section, the generator has separate layers that represent a hierarchy of high to low-level features. Therefore, if we have two images A and B and we want to mix a high-level feature of B into A, we can just "switch the high-level layers" while keeping other layers the same. Note that in the true implementation, we don't switch the layer itself but instead we switch the rows of embedding **w**.
![](/figures/stylegan_high.PNG)

We can do the same to manipulate low-level feature while keeping high-level features the same.
![](/figures/stylegan_low.PNG)

An implementation detail: there are 18 rows in the embedding **w** so in theory we have 18 levels of representation from high to low. However, first the layers come in pair (you can see in the image above that there are 2 style arrows in each box). So we are left with only 8 degrees of freedom. Moreover, I tried it out and actually most intermediate layers don't do much. So here I show the mixing result for only some combinations that I found work well. 
![](/figures/example_face_mixing.png)

The first column is the original image and the first row is the mixing image that I want to mix a low- or high-level feature into the original image. In the second row, I mix low-level features of the mixing image into the original image (layers 8-17). In the third row, I mix high-level features (layers 2-3). In the fourth row, I used layers 0-3, also for high-level feature change. We see that the mixing works pretty well. One thing I note is that it seems like the last 2 layers (0-1) control for the pose. So for high-level change, I decided to use only layers 2-3 and for low-level change I used layers 8-17.

# Problem 3: Quantify the magnitude of change in the image
Now we see that by visual inspection, the manipulation of low- or high-level features work pretty well. However, we want a more quantitative metric to measure how much change we have made to the original image. Importantly, we want to quantify either a low-level or high-level change. The general problem here is what image representation space we should use to compute the measure. 

## Low-level change
For low-level change, a natural space is simply the original space of the raw image, i.e. the pixel intensity. For the high-resolution images we use here (1024x1024x3), the space is really large (3 million dimensional). So let's do some PCA to reduce the dimensionality to 3 so we can make some visualization. In the example below, I show the result for 1 original image (green dot), the low-level-changed images (blue dots) and high-level-changed images (orange dots).
![](/figures/example_pixelDiff_pca.png)

The result is pretty much the ideal case that we expect. More specifically, the high-level-changed images stay pretty close to the original and they tend to clutter around a small region. On the other hand, the low-level-changed images are farther away from the original and they tend to spread out a lot. It indicates that in the pixel space, the high-level change only alters high-level features, not low-level ones while it is the opposite for the low-level change. However, this pattern is not that clear when we look at more examples:
![](/figures/example_lowlevel_pca_more.png)

It seems like the only thing that seems to hold is that low-level-changed images tend to spread out more and lie on more complex manifolds than the high-level-changed images. Now let's look at a summary of the result. The histogram below shows distribution of distance between a low- or high-level-changed image and the original image. Here the distance is the mean absolute deviation.

![](/figures/l2_lowChange.png)

The result is consistent with our above visualization. On average, low-level-changed images are slightly farther away from the original images and tend to spread out more compared to high-level-changed images.

So the result is not as clear-cut as we expect but still it's in the right direction we expect to see. Here's two ideas on how to improve:
* First, it could be that the pixel space is not appropriate for quantifying low-level change. In fact, what we often think of in terms of low-level change are things like skin tone, hair color, etc. That's definitely not what the pixel space is about. So alternatively, we can use the activation in the first few layers of a neural network (say, InceptionV3) that is pretrained on a big dataset (e.g. ImageNet).
* Second, it could be that the low-level change we make was not big enough. So we can try something more radical. After all, it is relatively easy to make a low-level change. Here I try one simple example. I take the original image and flip it horizontally. So what I have is exactly the same face but we just change the pose, position. Here's the histogram of distance between the flip and the original image (low and high-level-changed are included for comparison):

![](/figures/histogram_low_high_flip.png)

It seems like the flip manipulation does result in more pixel-level change as expected.

## High-level change
![](/figures/facenet_architecture.PNG)
![](/figures/example_facenet_pca.png)
![](/figures/example_facenet_pca_more.png)
![](/figures/l2_highChange.png)





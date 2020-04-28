# DL-styleGAN-faceForVisualExperiment
Create fake but look-like-real face images for visual experiment (e.g. visual psychophysics, visual neuroscience).

For walkthrough of basic workings and example results, see the file "slides.pdf".

## Manipulate a low-level (skin tone, hair color, eye color) or high-level features (identity) while keeping the others constant
Key point: use a pretrained style-GAN model to manipulate a low or high-level feature of face images while keeping other features constant.

Note that the generation code download pretrained model from the official style-GAN github. Otherwise, use the dnnlib.

- generate_stimulus_1: display generated images for exploration
- generate_stimulus_2: save generated images to google drive

![](/figures/example_face.png)

## Quantifying the magnitude of high-level change with FaceNet 
Then the question is how to quantify the magnitude of low vs high-level change
- image_preprocessing: Compute the pixel-level (low-level) differences of low and high-level changes. Also, try contrast normalization and flip. Note that the images are assumed to be in "Images" folder.
- FaceNet: Quantify high-level differences (presumably identity) by FaceNet. I use an InceptionResnetV1 pretrained on MS-CELEB-1M dataset (10 million face images) with triplet loss (the main idea behind FaceNet). The output of FaceNet is an embedding vector (128 dimensions) that is supposed to represent high-level features.

![](/figures/example_faceNet.png)

- PCA: explore how much information needed to represent the face images.

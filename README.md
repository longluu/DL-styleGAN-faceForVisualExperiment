# DL-styleGAN-faceForVisualExperiment
Create fake face images for visual experiment.
Key point: use a pretrained style-GAN model to manipulate a low or high-level feature of face images while keeping other features constant.
Note that the generation code download pretrained model from the official style-GAN github. Otherwise, use the dnnlib.
- generate_stimulus_1: display generated images for exploration
- generate_stimulus_2: save generated images to google drive
Then the question is how to quantify the magnitude of low vs high-level change
- image_preprocessing: Compute the pixel-level (low-level) differences of low and high-level changes. Also, try contrast normalization and flip. Note that the images are assumed to be in "Images" folder.
- FaceNet: Quantify high-level differences (presumably identity) by FaceNet. The output of FaceNet is an embedding vector that is supposed to represent high-level features.
- PCA: explore how much information needed to represent the face images.

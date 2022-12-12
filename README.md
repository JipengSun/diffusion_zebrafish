# Zebrafish Brain Volume Generation with Cascaded Guided Diffusion
------
Jipeng Sun, Liqian Ma, Preetham Pareddy



## Motivation

The human brain is one of the most enigmatic things known to mankind which has been an active area of research for many decades now. But studying similar and simpler brains is beneficial for understanding the way the human brain works. The zebrafish brain is one such system that is being analyzed to understand the brain. There has been a huge rise in the demand for quality zebrafish brain volume data for many research and analytics purposes like extracting neuron positions, observing reaction of the brain to different stimuli etc. For this analysis, a method called Light Field Microscopy is used to extract the volume information of the brain, in the form of 2D images. These images can be used by researchers and also deep learning models to study the zebrafish brain better. But this data is expensive and is not easy to produce, and as deep learning models can only work in a framework where there is a huge amount of data, this project aims to generate the 2D images of the zebrafish brain, so that these images can be used as the data for the deep learning based deconvolution algorithms

## Problem Statement

In this project we want to generate 2D images of zebrafish brain, given the condition of depth. In this context depth refers the different cross sections of the zebrafish brain volume. These images should be high fidelity and high resolution. We tackle this problem using diffusion models as they are the state of art in generation currently.

## Dataset

The dataset for this generation task is collected from the neuroscience lab at Northwestern. It contains the volume data of 52 zebrafish. The volume of each zebrafish brain contains 14 2d slices of images from different depths. An example is shown below.

![](https://lh6.googleusercontent.com/pL1ekl6bgEcpzWUfRvIJJUL1THEDFYyP28qtQIKAdkscfyhXomQWinsNtAs4A2TqK4NOkXFWv8WxzbXURTeMeNvP8vaAy2PNin0OtGSrF9bp3dtV5E8t3irm8nx6BZQBiOkTVKLKNgZ7cXyXFIjqPl7Ft8tMXZXcd9g6EiTB8PXbGE-A28TBV0qnIhOYYi2o)                        *Figure 1 - Slices of the same zebrafish brain at different depths.*

Each image has a resolution of 2048*2048 which makes it expensive to process them. The dataset is limited to 52 because it is difficult to create such high resolution data at a high magnification level. Sophisticated microscopes and cameras need to be used for this purpose ( because they have to be accurate at the scale of a few micro meters) and therefore it is expensive to create. 
   

## Methodology

**Diffusion model**

 - **Forward Process** : This is the step in which noise is added to the image. Given an image and the timestep as inputs, a noisier version of it is obtained as the output. Since timestep is also an input in this process, the noise can be modeled for each timestep separately instead of it being sequential. Note that no model is required for this step. The noise levels/ variances can be precomputed. The below image shows an example of what this process looks like.

![](https://lh4.googleusercontent.com/lIfbpfrXlGXATHBvfdO93j_zuKnJiICmXPlbUsXWgxP7L0HEq34qx5rX32r6nAZvKaO-8mchl3QJcwaYcOKnebM5mBYfgHaHAjX4UJWvU7vfh4KFjnn10_121muckGJeBn-gUhwtim0bQFgMwUK8x-AUWNKVACcq3isd6qDBLXWdl-JxJ0_Xci7vHkzcvNBi)*Figure 2 - Diffusion model forward pass* 
 
 - **Backward Process** : This is the step in which the model predicts the noise in the image. A simple U-net is used for this purpose. The input to the model is the noisy image and the output is the noise in that image (the mean of the noise to be more exact as variance is fixed). As the parameters are shared across all the timesteps, it is important to give the timestep to the network. Note that the timesteps are encoded in the sinusoidal embedding format that is used in transformers. Each image is further labelled as shown in the bottom left of the image. Since there are 14 depth slices, each image has a 14 length long vector which contains a 1 at the particular depth position and 0 everywhere else. This additional information is what makes this process condition diffusion

![](https://lh6.googleusercontent.com/4ZkKwaRpRIlStOIwrzEMvB3vDqnNDuBkRKtYeBp_6KlAt8VYisCvoe45p3PnuFv9YGUvm0bxmvlQVgs_DWngB6Ip4tBGjjuECuvAaWYNAuzaDJJA76d0mkk4gljAvFHBj8bG2edvJqodqr3i320ZD3JMOLHYxcEWL10F2Oija2zpqJJk8jSOtXvDgynZ3Ap6)*Figure 3 - U-net is used to predict the noise in an image*

**Cascaded Diffusion model**


![](https://lh3.googleusercontent.com/0CGog3por1g-ahTc5t3c4QXHAa5UlR8y3a4ZvwOdl3rL8WcAoS_J93muC6p8x4ZonKStDItCS1eibNvlepHwQsOeaVBpdNZTbQBgb0wGQ7GsI1CyIUM_GqfQTZb_dHhwItMLbK3sBd5ibWmvC-xERa5eL1gSKIPnty-9H82WujBDxfJM7asz9lCOY6Db24w1)*Figure 4 - Cascaded Diffusion approach*

We experimented with another approach which is based on the classifier-free diffusion model. The model treats each depth of the volume as an independent class and uses the diffusion model to generate the corresponding image based on the given depth condition. For example, the model uses all the 80 micro meter slices to generate 80 micro meter images but not other depth. This approach guarantees better dependency on the depth which will help in the overall volume structure coherence. To match the high resolution requirement for the microscopy deconvolution usage, we then designed super-resolution module to gradually upsample the images generated from the previous low-resolution diffusion model. The beginning resolution diffusion is 64 × 64, after three upsampling diffusion models, the final generated images are in the resolution of 512 × 512. But since we have data for only 52 zebrafish brain, the training data for one depth model would contain only 52 images which is too less. 

**Coherence**

In the context of generating 2D images given depth, it is important for the model to learn what the higher depth and lower depth image would look like because that information is important to gain volume information. For example, if the current image has a depth label of 80 micrometers, it is important to have representation of 70 micrometers depth label images and 90 micrometers depth label images because otherwise the model would not learn the overall structure (volume) of the brain. Therefore, it is advantageous to add previous depth slice and the next depth slice as additional information to the input.

## Results

 
 **Evaluation Metrics**
 
The loss function used is L1 loss between the predicted noise and original noise. It doesn't seem to be doing a very good job if you look at figure 5 because even though the final loss is less (0.4), the generated image is not very similar to the original image structure. So it is better to experiment on other loss functions like L2, L-infinity etc.
Manual judgment would be the most accurate in determining the quality of the images.  Measuring structural coherence with the brain volume is a more complex task. Relying on human judgement, and softwares like blender which helps build 3D models by using 2D images is the best course of action to determine quality of the volume generated.

**Diffusion Model**

![](https://lh5.googleusercontent.com/C9MrXRtzgY7SY4R-Slhbat2xsKJjER2cZOxocC7oGMgwCxcm6x9VL7a2NkRgnJvrJqPIbevp-KHO5HBb8F1kRTaA5tV2MxUlzdRfYevMc9-CrAE4Bec9bcBLTbCfIYuswrZ84tNKgWQ6pOUC85oRaWcaCz0gyktTQU22FR-FBV0T7YshrSpXhdZ68VXzYL47)*Figure 5 - Results of the diffusion model at epoch 300*

**Cascaded Diffusion model**

![](https://lh6.googleusercontent.com/LhqK3aEy3Kqvyo6d5AJCfRqRZh8s6e_ShUOB4OL6QDgWF8YSM84Hy6hHsdLHf5ZzfALTqF-_D4OGloQF5BkZKQG4esEDuR2p-t68iYTHvAZsbf5QFJomXBo00pX5Uo4t_3mS7FRHQchEY_N2HxVA0f0MLN2pAedOPNl0AEA04I25vN93PPcDHrruAXSQh49p)*Figure 6 - Results of cascaded diffusion model at epoch 495*


## Future Work
Some of the things we want to do going into the future are:
 - Increase the size of the dataset by using data augmentation techniques and getting more data from the neuroscience lab
 - Condition the model on the previous depth slice. This helps to calibrate the model better and align the generated images of different depths. This will also help the model learn the difference between different focal lengths.
 - Experiment with other loss functions. We are currently using L1 loss which is not doing such a good job of representing the generation loss. We want to try other norms like L infinity etc.
 - Add attention blocks and residual blocks to our current architecture for robust results 

           

	 

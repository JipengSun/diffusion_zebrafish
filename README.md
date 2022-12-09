# Zebrafish brain image generation using Diffusion model
------
Jipeng Sun, Liqian Ma, Preetham Pareddy

**

## Motivation
Zebrafish Brain Volume Data Generation Project Proposal

The demand for large zebrafish brain volume dataset is drastically rising these days. The usage for such datasets not only limits to extracting accurate neuron positions for neuroscience study purpose, but also used as the training dataset for developing deep learning based deconvolution algorithms for zebrafish microscopy study. However, due to the experimental difficulties of 3D imaging the zebrafish brain, there is no publicly available zebrafish volume dataset and the amount of data collected by individual lab is limited. As a result, the development of the training-based zebrafish microscopy algorithms is almost stagnant.

## Dataset

The dataset which we have contains the volume data of 52 zebrafish. Each zebrafish`s volume data contains 14 2d slices of images from different depths. An example is shown below.

**![](https://lh6.googleusercontent.com/pL1ekl6bgEcpzWUfRvIJJUL1THEDFYyP28qtQIKAdkscfyhXomQWinsNtAs4A2TqK4NOkXFWv8WxzbXURTeMeNvP8vaAy2PNin0OtGSrF9bp3dtV5E8t3irm8nx6BZQBiOkTVKLKNgZ7cXyXFIjqPl7Ft8tMXZXcd9g6EiTB8PXbGE-A28TBV0qnIhOYYi2o)**                             Figure 1

Each image has a resolution of 2048*2048 which makes it expensive to process them.      
 ## Problem Statement
In this project we want to generate 2D images given the condition of depth. These images should be high fidelity and high resolution. We tackle this problem using diffusion models as they are the state of art in generation currently.

 ## Methodology

*Forward Process*
**![](https://lh4.googleusercontent.com/lIfbpfrXlGXATHBvfdO93j_zuKnJiICmXPlbUsXWgxP7L0HEq34qx5rX32r6nAZvKaO-8mchl3QJcwaYcOKnebM5mBYfgHaHAjX4UJWvU7vfh4KFjnn10_121muckGJeBn-gUhwtim0bQFgMwUK8x-AUWNKVACcq3isd6qDBLXWdl-JxJ0_Xci7vHkzcvNBi)**Figure 2


*Conditional Diffusion - Backward Process*

**![](https://lh6.googleusercontent.com/4ZkKwaRpRIlStOIwrzEMvB3vDqnNDuBkRKtYeBp_6KlAt8VYisCvoe45p3PnuFv9YGUvm0bxmvlQVgs_DWngB6Ip4tBGjjuECuvAaWYNAuzaDJJA76d0mkk4gljAvFHBj8bG2edvJqodqr3i320ZD3JMOLHYxcEWL10F2Oija2zpqJJk8jSOtXvDgynZ3Ap6)**Figure 3

*Cascaded Diffusion*
**![](https://lh3.googleusercontent.com/0CGog3por1g-ahTc5t3c4QXHAa5UlR8y3a4ZvwOdl3rL8WcAoS_J93muC6p8x4ZonKStDItCS1eibNvlepHwQsOeaVBpdNZTbQBgb0wGQ7GsI1CyIUM_GqfQTZb_dHhwItMLbK3sBd5ibWmvC-xERa5eL1gSKIPnty-9H82WujBDxfJM7asz9lCOY6Db24w1)**Figure 4

Zebrafish Brain Volumetric Data Generation with Classifier-Free Cascaded Diffusion

Overall, the proposed model is based on the classifier-free diffusion model.[4] The model treats each depth of the volume as an independent class and uses the diffusion model to generate the corresponding image based on the given depth condition. To match the high resolution requirement for the microscopy deconvolution usage, we then designed super-resolution module to gradually upsample the images generated from the previous low-resolution diffusion model. The beginning resolution diffusion is 64 × 64, after three upsampling diffusion models, the final generated images are in the resolution of 512 × 512.
 ## Results

*Unconditioned Diffusion*

**![](https://lh5.googleusercontent.com/C9MrXRtzgY7SY4R-Slhbat2xsKJjER2cZOxocC7oGMgwCxcm6x9VL7a2NkRgnJvrJqPIbevp-KHO5HBb8F1kRTaA5tV2MxUlzdRfYevMc9-CrAE4Bec9bcBLTbCfIYuswrZ84tNKgWQ6pOUC85oRaWcaCz0gyktTQU22FR-FBV0T7YshrSpXhdZ68VXzYL47)**Figure 5

*Diffusion for a specific depth slices*

**![](https://lh6.googleusercontent.com/LhqK3aEy3Kqvyo6d5AJCfRqRZh8s6e_ShUOB4OL6QDgWF8YSM84Hy6hHsdLHf5ZzfALTqF-_D4OGloQF5BkZKQG4esEDuR2p-t68iYTHvAZsbf5QFJomXBo00pX5Uo4t_3mS7FRHQchEY_N2HxVA0f0MLN2pAedOPNl0AEA04I25vN93PPcDHrruAXSQh49p)**Figure 6


## Future Work

 - Increase the size of the dataset by using data augmentation techniques and getting more data from the neuroscience lab
 - Condition the model on the previous depth slice. This helps to calibrate the model better and align the generated images of different depths. This will also help the model learn the difference between different focal lengths.
 - Try using other loss functions. We are currently using L1 loss which is not doing such a good job of representing the generation loss. We want to try other norms like L infinity etc.
 - We want to add attention blocks and residual blocks to our current architecture for robust results 

           

	 

﻿# Zebrafish brain image generation using Diffusion model
------
Jipeng Sun, Liqian Ma, Preetham Pareddy

**

## Motivation
Zebrafish Brain Volume Data Generation Project Proposal

The demand for large zebrafish brain volume dataset is drastically rising these days. The usage for such datasets not only limits to extracting accurate neuron positions for neuroscience study purpose, but also used as the training dataset for developing deep learning based deconvolution algorithms for zebrafish microscopy study. However, due to the experimental difficulties of 3D imaging the zebrafish brain, there is no publicly available zebrafish volume dataset and the amount of data collected by individual lab is limited. As a result, the development of the training-based zebrafish microscopy algorithms is almost stagnant.

## Dataset

The dataset which we have contains the volume data of 52 zebrafish. Each zebrafish`s volume data contains 14 2d slices of images from different depths. An example is shown below.

![Figure 1](https://drive.google.com/drive/u/2/my-drive)
   Each image has a resolution of 2048*2048 which makes it expensive to process them.      
 ## Problem Statement
In this project we want to generate 2D images given the condition of depth. These images should be high fidelity and high resolution. We tackle this problem using diffusion models as they are the state of art in generation currently.




           

	 

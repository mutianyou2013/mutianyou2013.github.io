---
layout: post
read_time: true
show_date: true
title: "Comparison between CE-TinNet and LCNN"
date: 2021-09-02
img: posts/20210902/CE.png
tags: [Machine Learning, Cluster Expansion, Academia]
author: Tianyou Mou
description: "Machine learning for computational catalysis."
---

## Overview

The lattice convolutional neural network (LCNN) is a Python library for deep learning of lattice system developed by the Vlachos group at the University of Delaware and Jung group at KAIST. The model has been built in hope to improve upon cluster expansion methods, linear regression based on the clusters in the lattice. The LCNN performs better than the cluster expansion and cluster expansion based machine learning methods with sufficient number of data points.

I employed this LCNN to train the Cluster expansion model and then compare with the CE-TinNet model. 

Lym et al. developed a novel lattice convolutional neural network (LCNN) that improved the formation energy prediction compared to state-of-the-art CE methods by 20-30% (Figure 4a). Featurization process extracts features of each site’s neighbor lists using one-hot encoding. The extracted information then undergoes lattice convolutional (LConv) layers which are built to construct the local environment by considering the nearest neighbors and including permutations to account for the symmetry of the lattice. Activation and summation are followed for new site features. Once all convolution operations are performed, the representation of the lattice is obtained. Followed by the site-wise activation and linear multiplication, the formation energy of each image can be predicted. They applied the LCNN model to study the coverage of O and NO adsorption on Pt(111) surface, the model captures the local environment of adsorbates, and a one-hot encoding input and the predicted site formation energy distribution are shown in Figure 4b and c, respectively. Compared to CE models, the LCNN achieves the best performance with a test root mean squared error (RMSE) of 4.4 meV/site using less training data. The effect of van der Waals (vdW) forces on lateral interactions have also been studied by comparing the formation energy with and without vdW forces for an example of all unique configurations in a unit cell containing up to 12 adsorption sites (1,581,607 in total). The convex hull diagrams of LCNN computed formation energies trained using PBE-D3 (with vdW forces) and PBE (without vdW forces) datasets are shown in Figure 4d and e, respectively. They found that the trend in the convex hull has changed by the vdW interactions. Particularly, the global ground state configuration for PBE is observed at 0.78 monolayer (ML) of NO, whereas it is 1.0 ML of NO for PBE-D3. 

<center><img src="./assets/img/posts/20210902/Figure4.jpg" width="800"></center>

## Comparison

CE-TinNet is a ML based model that considers the lateral interactions between the adsorbates and surface atoms. It outperforms the LCNN when considering the adsorbate-adsorbate interactions for NO and O adsorption on Platinum. 

More studies have been down on different surfaces with various adsorbates, and I'll share more results when it's ready to be published.
 


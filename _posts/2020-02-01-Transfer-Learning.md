---
layout: post
read_time: true
show_date: true
title: "Predicting material properties using transfer learning"
date: 2020-02-01
img: posts/20200201/TLnew2.png
tags: [Python, Machine Learning]
author: Armando Maynez
description: "Midlife career change: a disaster or an opportunity?"
---

## Background

The evolution of the research workflow in computational chemistry is about 3 generations. The first-generation approach is to calculate the physical properties of an input structure, which is often performed via an approximation to the Schrödinger equation  For example, Density functional theory. In the second-generation approach, by using global optimization (for example, an evolutionary algorithm), an input of chemical composition is mapped to an output that contains predictions of the structure or ensemble of structures 
that the combination of elements are likely to adopt. The emerging third-generation approach is to use machine-learning techniques with the ability to predict composition, structure and properties. However, you have to provide sufficient data and an appropriate model.

Also, there are four stages of training a machine-learning model: Data collection, Representation(like preprocessing), type of learning, and Model selection. A very important factor is that you have to provide sufficient amount of accurate data to train your machine learning model. 

With years of hard work, many DFT computational databases have been setup. Most of them are very large. But the problem is most calculations are calculated by basic exchange correlation functionals, like gga. So the accuracy is relative low. At the same time, there are some experimental databases with very high accuracy. But since lots of cases you couldn’t really conduct the experiments. So the size of the dataset is limited. We can also obtain some other quantum-mechanical databases like coupled cluster quantum method database. Although they have a better accuracy, all of them are computational expensive. 

So, right now we don’t have a proper database with high accuracy and large size. 
We can take a look at some examples.  OQMD is A large dataset containing DFT-computed materials properties for 341K materials. But Thermo-Calc Software is An experimental data set containing only 1963 samples. So, here comes the question: machine learning needs large amount of data to be accurate, can we use limited amounts of data to achieve a high accuracy?

There is a Machined Learning framework called “ transfer learning ” which has considerable potential to overcome the problem of limited amounts of data. Transfer learning relies on the concept that various property types, such as physical, chemical, electronic, thermodynamics are physically interrelated. 

Previously, if we want to predict a target property with small dataset, we just train this small dataset, although the dataset is accurate, with limited amount of data, we couldn’t get a good accuracy. For transfer learning, we can use our exist big dataset to train a model first, and save the common features that are same for different materials, and then use this model to train another small dataset, and then predict a target property with a higher accuracy. 

Then, let’s focus on how does this transfer learning work. There are two commonly applied procedures, one is frozen featurizer, another one is called fine-tuning techniques. I’m gonna mainly talk about this fine-tuning techniques. We still have two steps, the first step is using big dataset to pretrain a model. The second step is using this model to predict a target property. We first input big data, and here we train this deep neural networks, common features are saved in these layers. Then we can use this model, but freeze those layers since we don’t want to lose those common features. We just let the last several layers to be optimized. Then we can get a good prediction of our target property. 

<center><img src="./assets/img/posts/20200201/TLnew1.png" width="620"></center>

Then, let’s take a look at one example on how to use transfer learning to predict materials properties. The input feature here is the element fractions. For example, for OH, fraction of O is 0.5, and fraction of H is also 0.5. And the target property is the formation energy of materials. The pretraining dataset is a DFT dataset with 341K materials. And they trained the model.Then they used a small database with only 2000 experimental samples to do transfer learning to predict the formation energy. 

Let’s give a look at the training performance. 

First, let’s focus on these two values because both are trained from scratch, which means for both cases, they used a random model first, then input data to train the model and got a performance.  

You can see if they have a large dataset, obviously, they’ll get a lower MAE which means a better performance. But since this OQMD dataset itself is not as accurate as experimental data, so we couldn’t really believe this result. 

Then, let’s compare these two values. The first one is to use the model from large dataset, directly predict the experimental values. The MAE is higher. This transfer learning one is still using the model from large dataset, but do a transfer learning process, basically freeze most layers, retrained last several layers and get a predicting performance. This one is much better. 

So the only difference between them are the OQMD-SC is a direct prediction by the large dataset model, and the transfer learning one has a transfer learning procedure based this large dataset model. 

<center><img src="./assets/img/posts/20200201/TL.png" width="620"></center>

<center><img src="./assets/img/posts/20200201/result.png" width="620"></center>

At last, we can compare these two, we can see with transfer learning applied, we can get a much better performance even though we both just use the same experimental dataset to train the model. That’s because the transfer learning method use the pretrained large dataset  model first, and save the common features in the hidden layers. This helps to improve the performance. 

In summary, A transfer learning model shows an enhanced performance compared with traditional machine learning model 

Also, Transfer learning can be further applied to other area with limited amount of accurate data. 
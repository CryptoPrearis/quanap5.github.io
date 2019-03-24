---
layout: post
title: Day 00: Survey
subtitle: ứng dụng detect blink của eyes
tags: [eye, land mark]
comments: false
---


### Introduction

Target: Detect or predict alarm event of industrial system based on the time series data (sequence of observation over time line): For example, monitoring signal from sensor devices such as temperature, humidity, pressure, variation sensor, ... for warning about the anomaly situation.

Method: Using deep learning based method on time series data.
 
### Survey

We can form our problem as Classification or Prediction.
Classification: We have to training data in both situation normal and anomaly (alarm event) to support for training deep learning based classifier.

![LSTM01](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/LSTM00_01.JPG)

Prediction: We require training data on the normal situation only. These normal data will be used for training deep learning based predictor model. To detect or forecast alarm event, we consider the comparison the actual value and predicted value. Alarm usually when actual value very different from the predicted value.

![LSTM02](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/LSTM00_02.JPG)

### Approaches
There are some different approaches that can be used with time series data as following.

### Learning from raw data: 

Multi-layer Perceptron (MLP), Convolutional Neural Network (CNN), and Recurrent Neural Network(RNN) can be used.
- Transforming data before entering neural network is common practice.
- Feature extracting: 
- Filtering method: remove trend, seasonality, low-pass, high-pass, ...
- Change domain: Time domain, frequency domain, wavelet transform (time-frequency domain)

### Consider time series as image

- Encode time series data as image using methods: Recurrent Plot, Gramian Angular Summation Field (GAF), Gramian Angular Difference Field (GADF), Markov Transition Field,.... to utilize deep learning model from computer vision.

![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/LSTM00_03.JPG)

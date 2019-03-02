---
layout: post
title: Face detection
subtitle: Su dung OpenCV va thu vien deep learning de detect khuon mat
bigimg: /img/post1.1.PNG
tags: [opencv, facedetection, deeplearning]
comments: true
---

## Su dung deep learning trong OpenCV de detec khuon mat

- Cho mot anh dau vao
Day la snip code:

~~~
# import the necessary packages
import numpy as np
import argparse
import cv2
 
# construct the argument parse and parse the arguments
ap = argparse.ArgumentParser()
ap.add_argument("-i", "--image", required=True,
	help="path to input image")
ap.add_argument("-p", "--prototxt", required=True,
	help="path to Caffe 'deploy' prototxt file")
ap.add_argument("-m", "--model", required=True,
	help="path to Caffe pre-trained model")
ap.add_argument("-c", "--confidence", type=float, default=0.5,
	help="minimum probability to filter weak detections")
args = vars(ap.parse_args())
~~~

## Demo nhieu khuon mat
[Demo](https://github.com/quanap5/quanap5.github.io/blob/master/img/20190303_021246.mp4)

---
layout: post
title: Sử dụng thư viện dlib, OpenCV and python để làm quen với facial landmark
subtitle: tìm hiểu vể facial landmark trong xử lý ảnh
tags: [faceial landmark, image processing]
comments: false
---

### Giới thiệu

Hôm nay chúng ta sẽ sử dụng thư viện [dlib]() và OpenCV để detect facial lankmark trong bức ảnh đầu vào.

![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/intro_facelandmark.JPG)

Nói đến facial landmark là việc xác định vị trí của các bộ phận sau trên khuôn mặt.
- Eyes
- Eysebrows
- Nose
- Mouth
- Jawline

Facial landmark cũng được ứng dụng nhiều trong việc tìm hướng khuôn mặt, ứng dụng detect buồn ngủ, ...etc.

Cơ bản về facial landmark bao gồm:
1. Cách facial lankmark hoạt động
2. Làm thế nào để detect dc facial landmark tuwf các thư viện dlib, opencv và python

### Facial landmark với cac thư viện trên

Phần đầu chúng ta sẽ tìm hiểuvà thảo luận về facial landmark và tại sao chúng lại được sử dụng trong Computer Vision.

Từ đó chúng ta có ứng dụng demo mẫu.

Cuối cùng là giới thiệu một số ứng dụng của facial landmark detection.

### Landmark là gi?
![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/landmark2.JPG)

### Code

```python
# import the necessary packages
from imutils import face_utils
import numpy as np
import argparse
import imutils
import dlib
import cv2

# construct the argument parser and parse the arguments
ap = argparse.ArgumentParser()
ap.add_argument("-p", "--shape-predictor", required=True,
    help="path to facial landmark predictor")
ap.add_argument("-i", "--image", required=True,
    help="path to input image")
args = vars(ap.parse_args())

# initialize dlib's face detector (HOG-based) and then create
# the facial landmark predictor
detector = dlib.get_frontal_face_detector()
predictor = dlib.shape_predictor(args["shape_predictor"])

# load the input image, resize it, and convert it to grayscale
image = cv2.imread(args["image"])
image = imutils.resize(image, width=500)
gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)

# detect faces in the grayscale image
rects = detector(gray, 1)

# loop over the face detections
for (i, rect) in enumerate(rects):
    # determine the facial landmarks for the face region, then
    # convert the facial landmark (x, y)-coordinates to a NumPy
    # array
    shape = predictor(gray, rect)
    shape = face_utils.shape_to_np(shape)

    # convert dlib's rectangle to a OpenCV-style bounding box
    # [i.e., (x, y, w, h)], then draw the face bounding box
    (x, y, w, h) = face_utils.rect_to_bb(rect)
    cv2.rectangle(image, (x, y), (x + w, y + h), (0, 255, 0), 2)

    # show the face number
    # cv2.putText(image, "Face #{}".format(i + 1), (x - 10, y - 10),
    # 	cv2.FONT_HERSHEY_SIMPLEX, 0.5, (0, 255, 0), 2)

    # loop over the (x, y)-coordinates for the facial landmarks
    # and draw them on the image
    for (x, y) in shape:
        cv2.circle(image, (x, y), 1, (0, 0, 255), -1)

    for i in range(0, len(shape)-1):
        if (i >= 0) and (i <= 15):
            cv2.line(image, (shape[i][0], shape[i][1]), (shape[i + 1][0], shape[i + 1][1]), (0, 0, 255), 2)
        if (i >= 17) and (i <= 20):
            cv2.line(image, (shape[i][0], shape[i][1]), (shape[i + 1][0], shape[i + 1][1]), (0, 0, 255), 2)
        if (i >= 22) and (i <= 25):
            cv2.line(image, (shape[i][0], shape[i][1]), (shape[i + 1][0], shape[i + 1][1]), (0, 0, 255), 2)
        if (i >= 36) and (i <= 40):
            cv2.line(image, (shape[i][0], shape[i][1]), (shape[i + 1][0], shape[i + 1][1]), (0, 0, 255), 2)
        if (i >= 42) and (i <= 46):
            cv2.line(image, (shape[i][0], shape[i][1]), (shape[i + 1][0], shape[i + 1][1]), (0, 0, 255), 2)
        if (i >= 27) and (i <= 34):
            cv2.line(image, (shape[i][0], shape[i][1]), (shape[i + 1][0], shape[i + 1][1]), (0, 0, 255), 2)
        if (i >= 48) and (i <= 66):
            cv2.line(image, (shape[i][0], shape[i][1]), (shape[i + 1][0], shape[i + 1][1]), (0, 0, 255), 2)


# show the output image with the face detections + facial landmarks
cv2.imshow("Output", image)
cv2.waitKey(0)
```

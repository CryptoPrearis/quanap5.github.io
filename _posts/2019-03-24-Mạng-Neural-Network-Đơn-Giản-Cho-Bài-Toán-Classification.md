---
layout: post
title: Mạng Neural Network đưn giản với thư viện Keras 
subtitle: classification
tags: [CNN, classification]
comments: false
---

### Giới thiệu

Keras là một framework deep learning được rất nhiều người phát rất ưu chuộng và sử dụng rộng rãi bởi sự tiện lợi của [nó](https://keras.io/). Đặc biệt là các lập trình về deep learning để tham gia các cuộc thi trên kangle. Rất nhanh trong việc thiết kế model với việc sử dụng API.

Keras cũng dễ dàng sử dụng với các gói thư viện trong Python. Trong bài học của tôi hôm nay tôi sẽ demo một mạng neural đơn giản sử dụng Python , keras và ứng dụng vào bài toán classification.

### Các bước thực hiện

![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/cnn01.JPG)

 Để bắt đầu bài học này chúng ta sẽ xem qua kiến trúc của mootjmangj rất phổ biến đó là feedward network như hình trên.
 
 Chúng ta cũng sẽ tìm hiểu kỹ hơn ở phần sau qua từng đoạn code và cách định nghĩa feedward neural network và ứng dụng vào bài toán phân loại ảnh chó và mèo. Mục tiêu cuối cùng ta đưa ra một ảnh và xác định ảnh đấy là về con chó hay con mèo :) Hiểu nôm na là thế nhé.
 
 Có thể bài hocj gồm hai phần chính. Training model và test model sử dụng python script.
 
 
### Feedforward neural network

Thực tế có rất nhiều kiến trúc cho mạng neural network nhưng phổ biến nhất vẫn là kiểu feedforward neural network.

![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/cnn01.JPG)

 Ta nên hiểu loại mạng này chỉ cho phép sự kết nối giữa layer i và layer i+1 nên gọi là feedforward. Ko có kết nối ở chiều ngược lại (backward).
 
 Hơn nữa các nodes trong layer i sẽ fullconnected với các nodes của layer i+1. Cái này có ngĩa là mỗi một node trong layer i đều kết nối với tất cả các nodes trong layer i+1.
 
 Chúng ta thường sử dụng chuỗi các số nguyên để biểu diễn các node trong mạng neural.
 
 - Layer 0 là input layer
 - Layer 1 và layer 2 là hidden layer
 - Layer 3 là output layer. lớp này chính là lớp chúng ta sẽ nhận kết quả cuối cùng của classification.
 

### Thực hiện network sử dung python và keras

Full code.

```python
# import the necessary packages
from sklearn.preprocessing import LabelEncoder
from sklearn.model_selection import train_test_split
from keras.models import Sequential
from keras.layers import Activation
from keras.optimizers import SGD
from keras.layers import Dense
from keras.utils import np_utils
from imutils import paths
import numpy as np
import argparse
import cv2
import os

def image_to_feature_vector(image, size=(32, 32)):
	# resize the image to a fixed size, then flatten the image into
	# a list of raw pixel intensities
	return cv2.resize(image, size).flatten()

# construct the argument parse and parse the arguments
ap = argparse.ArgumentParser()
ap.add_argument("-d", "--dataset", required=True,
	help="path to input dataset")
ap.add_argument("-m", "--model", required=True,
	help="path to output model file")
args = vars(ap.parse_args())

# grab the list of images that we'll be describing
print("[INFO] describing images...")
imagePaths = list(paths.list_images(args["dataset"]))

# initialize the data matrix and labels list
data = []
labels = []

# loop over the input images
for (i, imagePath) in enumerate(imagePaths):
	# load the image and extract the class label (assuming that our
	# path as the format: /path/to/dataset/{class}.{image_num}.jpg
	image = cv2.imread(imagePath)
	label = imagePath.split(os.path.sep)[-1].split(".")[0]

	# construct a feature vector raw pixel intensities, then update
	# the data matrix and labels list
	features = image_to_feature_vector(image)
	data.append(features)
	labels.append(label)

	# show an update every 1,000 images
	if i > 0 and i % 1000 == 0:
		print("[INFO] processed {}/{}".format(i, len(imagePaths)))

# encode the labels, converting them from strings to integers
le = LabelEncoder()
labels = le.fit_transform(labels)

# scale the input image pixels to the range [0, 1], then transform
# the labels into vectors in the range [0, num_classes] -- this
# generates a vector for each label where the index of the label
# is set to `1` and all other entries to `0`
data = np.array(data) / 255.0
labels = np_utils.to_categorical(labels, 2)

# partition the data into training and testing splits, using 75%
# of the data for training and the remaining 25% for testing
print("[INFO] constructing training/testing split...")
(trainData, testData, trainLabels, testLabels) = train_test_split(
	data, labels, test_size=0.25, random_state=42)

# define the architecture of the network
model = Sequential()
model.add(Dense(768, input_dim=3072, init="uniform",
	activation="relu"))
model.add(Dense(384, activation="relu", kernel_initializer="uniform"))
model.add(Dense(2))
model.add(Activation("softmax"))

# train the model using SGD
print("[INFO] compiling model...")
sgd = SGD(lr=0.01)
model.compile(loss="binary_crossentropy", optimizer=sgd,
	metrics=["accuracy"])
model.fit(trainData, trainLabels, epochs=50, batch_size=128,
	verbose=1)

# show the accuracy on the testing set
print("[INFO] evaluating on testing set...")
(loss, accuracy) = model.evaluate(testData, testLabels,
	batch_size=128, verbose=1)
print("[INFO] loss={:.4f}, accuracy: {:.4f}%".format(loss,
	accuracy * 100))

# dump the network architecture and weights to file
print("[INFO] dumping architecture and weights to file...")
model.save(args["model"])
```

Tìm hiểu từng đoạn code thôi nào.

```python
# import the necessary packages
from sklearn.preprocessing import LabelEncoder
from sklearn.model_selection import train_test_split
from keras.models import Sequential
from keras.layers import Activation
from keras.optimizers import SGD
from keras.layers import Dense
from keras.utils import np_utils
from imutils import paths
import numpy as np
import argparse
import cv2
import os
```

Đoạn code trên mục đích là import các thư viện các gói cần thiết bao gồm: keras, sklearn, numpy, opencv,...

```python
# construct the argument parse and parse the arguments
ap = argparse.ArgumentParser()
ap.add_argument("-d", "--dataset", required=True,
	help="path to input dataset")
ap.add_argument("-m", "--model", required=True,
	help="path to output model file")
args = vars(ap.parse_args())
 
# grab the list of images that we'll be describing
print("[INFO] describing images...")
imagePaths = list(paths.list_images(args["dataset"]))
 
# initialize the data matrix and labels list
data = []
labels = []
```
 
 Đoạn code trên dùng để xử lý phần command interface trong python script. Bạn có thể tìm hiểu sâu hơn tại [đây]().
 
 
 Bây giờ là đoạn code để load ảnh từ ổ đĩa, convert ảnh vể feature vector và update lên biến dât và label đã khai báo ở trên.
 
 ```python
 # loop over the input images
for (i, imagePath) in enumerate(imagePaths):
	# load the image and extract the class label (assuming that our
	# path as the format: /path/to/dataset/{class}.{image_num}.jpg
	image = cv2.imread(imagePath)
	label = imagePath.split(os.path.sep)[-1].split(".")[0]

	# construct a feature vector raw pixel intensities, then update
	# the data matrix and labels list
	features = image_to_feature_vector(image)
	data.append(features)
	labels.append(label)

	# show an update every 1,000 images
	if i > 0 and i % 1000 == 0:
		print("[INFO] processed {}/{}".format(i, len(imagePaths)))
		
 ```
 
 Còn lúc này là thời gian dành cho việc định nghĩa model sử dung keras qua api.
 
```python
# define the architecture of the network
model = Sequential()
model.add(Dense(768, input_dim=3072, init="uniform",
	activation="relu"))
model.add(Dense(384, activation="relu", kernel_initializer="uniform"))
model.add(Dense(2))
model.add(Activation("softmax"))

```

Ta có một neural network với kiên trúc 3072-768-384-2.

Input layer có 3072 nodes, tương với mỗi node là mỗi raw pixel của 32x32x3 được flatten từ ảnh đầu vào.

Tiếp theo là hai lớp hidden tương ứng 768 và 384 nodes. Những node được xác định thông qua kỹ thuật hyperparametter tuning với việc thử tét, thử test trên thí nghiệm.

Lớp cuối cùng có 2 node là "dog" và "cat".


### Training

Sau khi có model đã được định nghĩa chúng ta se cấu hình model với các thuật toán cũng nhưng version các phương pháp optimize cho qua trình học (training) như sau.

```python

# train the model using SGD
print("[INFO] compiling model...")
sgd = SGD(lr=0.01)
model.compile(loss="binary_crossentropy", optimizer=sgd,
	metrics=["accuracy"])
model.fit(trainData, trainLabels, epochs=50, batch_size=128,
	verbose=1)
```

Đoạn code trên mô tả, cho phép trainning trên tập data với epoch = 50. sử dung hàm mất mát là crossentropy.

### Test model

Sau khi trên training model trên tập dât đã được labeled. Phần tiếp theo là chũng ta viết đoạn python script sử dụng model vừa được trained xong để tét với data từ tập test.

```python
# import the necessary packages
from __future__ import print_function
from keras.models import load_model
from imutils import paths
import numpy as np
import argparse
import imutils
import cv2

def image_to_feature_vector(image, size=(32, 32)):
	# resize the image to a fixed size, then flatten the image into
	# a list of raw pixel intensities
	return cv2.resize(image, size).flatten()

# construct the argument parse and parse the arguments
ap = argparse.ArgumentParser()
ap.add_argument("-m", "--model", required=True,
	help="path to output model file")
ap.add_argument("-t", "--test-images", required=True,
	help="path to the directory of testing images")
ap.add_argument("-b", "--batch-size", type=int, default=32,
	help="size of mini-batches passed to network")
args = vars(ap.parse_args())

# initialize the class labels for the Kaggle dogs vs cats dataset
CLASSES = ["cat", "dog"]

# load the network
print("[INFO] loading network architecture and weights...")
model = load_model(args["model"])
print("[INFO] testing on images in {}".format(args["test_images"]))

# loop over our testing images
for imagePath in paths.list_images(args["test_images"]):
	# load the image, resize it to a fixed 32 x 32 pixels (ignoring
	# aspect ratio), and then extract features from it
	print("[INFO] classifying {}".format(
		imagePath[imagePath.rfind("/") + 1:]))
	image = cv2.imread(imagePath)
	features = image_to_feature_vector(image) / 255.0
	features = np.array([features])

	# classify the image using our extracted features and pre-trained
	# neural network
	probs = model.predict(features)[0]
	prediction = probs.argmax(axis=0)

	# draw the class and probability on the test image and display it
	# to our screen
	label = "{}: {:.2f}%".format(CLASSES[prediction],
		probs[prediction] * 100)
	cv2.putText(image, label, (10, 35), cv2.FONT_HERSHEY_SIMPLEX,
		1.0, (0, 255, 0), 3)
	 # cv2.imshow("Image", image)
	cv2.imwrite(imagePath[imagePath.rfind("/") + 1:], image)
	cv2.waitKey(0)
```

### Kết quả

![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/cnn02.JPG)

![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/cnn03.JPG)


---
layout: post
title: Face detection
subtitle: Sử dụng OpenCV và deep learning trong detect khuôn mặt
bigimg: /img/post1.1.PNG
tags: [opencv, facedetection, deeplearning]
comments: false
---

## Sử dụng deep learning trong OpenCV để detect khuôn mặt

Hôm nay tôi sẽ demo về việc sử dụng deep learning based face detector được tich hợp trong OpenCV trong việc xác định khuôn mặ người. Chúng ta có thể mở rộng cho các objects khác bắng việc training lại model trên training data set với label mong muốn.

Tôi viết blog này vừa để ôn tập và ghi nhớ những điều hay ho trong mảng Image Processing cũng như ứng dụng của Deep learning trong xử lý ảnh, xử lý video. Rõ ràng, việc detect object hay detect face sẽ rất hữu ích trong nhiều ứng sau này (nhận diện,....). Tôi sẽ tận dụng model đã trên sẽ (pre-trained model) để demo và tạo sự cảm hứng cho những người mới bắt đầu làm quen với image processing cũng như deep learning.

### Bài viết gồm 3 nội dung chính
- Thảo luận về face detection model trên OpenCV
- Tìm hiểu về code để demo việc sử dụng model vào ảnh đơn
- Cuối cùng là tạo một úng dụng dang streamming video (~real-time)

## Deep learning với OpenCV
OpenCV 3.3 hỗ trợ một số framework cho deeplearning như Cafe, TensorFlow, và Torch/Pytorch. Các API này có thể tương thích với C++ và python bindings. Nguyên tắc cơ bản bao gồm việc: Load model có sắn, tiền sử lí đầu vào và đưa ảnh tiền xử lí vào mạng neural network để classifications OpenCV không cung cấp tools cho việc trainning network vì có rất nhiều framework được đầu tư và làm tốt việc trainning. Việc của chúng ta là sử dụng model đã được trained từ framework khác trong OpenCV script để phục vụ ứng dụng một cách hiệu quả nhất (accuracy, real-time, release product,..)

Một số network tương thích với OpenCV bao gồm các mạng phổ biến như.
- GoogleLeNet
- AlexNet
- SqueezeNet
- VGGNet
- ResNet.

Phần lớn người dùng OpenCV ít biết rằng có face detector đủ chính xác sử dụng deep learning [here](https://github.com/opencv/opencv/tree/master/samples/dnn/face_detector).

![như hình](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/face_detector.jpg)

Khi sử dụng deep learning model trong OpenCV với Caffe models chúng ta cần lưu ý hai files:
- File .prototxt chứa model architecture (layers,...)
- File .caffemodel chứa trọng số của các layer (tạo ra từ trainning phase)

## Face detection trên ảnh đơn với OpenCV và deep learning

Ở ví dụ này, chúng ta sẽ apply lên một ảnh đơn trước. Và mục tiếp theo sẽ là ứng dụng với video, webcam.

```python
# import the necessary packages
import numpy as np  #gói cơ bản dùng để tính toán với python
import argparse     #dùng để viết command-line interface
import cv2          #OpenCV2

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

# load our serialized model from disk
print("[INFO] loading model...")
net = cv2.dnn.readNetFromCaffe(args["prototxt"], args["model"])

# load the input image and construct an input blob for the image
# by resizing to a fixed 300x300 pixels and then normalizing it
image = cv2.imread(args["image"])
(h, w) = image.shape[:2]
blob = cv2.dnn.blobFromImage(cv2.resize(image, (300, 300)), 1.0,
	(300, 300), (104.0, 177.0, 123.0))

# pass the blob through the network and obtain the detections and
# predictions
print("[INFO] computing object detections...")
net.setInput(blob)
detections = net.forward()

# loop over the detections
for i in range(0, detections.shape[2]):
	# extract the confidence (i.e., probability) associated with the
	# prediction
	confidence = detections[0, 0, i, 2]

	# filter out weak detections by ensuring the `confidence` is
	# greater than the minimum confidence
	if confidence > args["confidence"]:
		# compute the (x, y)-coordinates of the bounding box for the
		# object
		box = detections[0, 0, i, 3:7] * np.array([w, h, w, h])
		(startX, startY, endX, endY) = box.astype("int")
 
		# draw the bounding box of the face along with the associated
		# probability
		text = "{:.2f}%".format(confidence * 100)
		y = startY - 10 if startY - 10 > 10 else startY + 10
		cv2.rectangle(image, (startX, startY), (endX, endY),
			(0, 0, 255), 2)
		cv2.putText(image, text, (startX, y),
			cv2.FONT_HERSHEY_SIMPLEX, 0.45, (0, 0, 255), 2)

# show the output image
cv2.imshow("Output", image)
cv2.waitKey(0)
```


### Giải thích qua về code:

Từ code ta thấy có 3 tham số bắt buộc cần được truyền vào.

- image: đường dẫn ảnh đầu vào
- prototxt: đường dẫn đến Caffe prototxt file
- model: đường dẫn đến file trọng số và một tham số tùy chọn là confidence.
 
Đầu tiên phải load model và lưu vào biến net sử dụng
```python
net = cv2.dnn.readNetFromCaffe(args["prototxt"], args["model"])
```

Để mạng có thể perform ảnh đầu vào chúng ta phải tiền xử lý bằng cách chuyển về dạng khả dụng với model. Chúng ta hiểu chuyển ảnh về dang  blob  sử dụng với các công việc liên quan đến chiều cao, chiều rộng, chuẩn hóa, scale,.... [Tìm hiểu thêm](https://www.pyimagesearch.com/2017/11/06/deep-learning-opencvs-blobfromimage-works) về blobFromImage
```python
blob = cv2.dnn.blobFromImage
```

Mọi thứ đã sẵn sàng, tiếp theo chúng ta apply detect face bằng đoạn code.
```python
net.setInput(blob)
detections = net.forward()
```

Để visual kết quả chúng ta sử dụng đoạn code sau với các hàm để vẽ hình chữ nhật cũng như thông tin về confidences.
```python
cv2.rectangle
cv2.putText
```
Sử dụng vòng loop để xử lý nhiều đối tượng khuôn mặt và code đầy đủ như trên.

### Chạy command
```python
python detect_faces.py --image face_quan.jpg --prototxt deploy.prototxt.txt --model res10_300x300_ssd_iter_140000.caffemodel
```
Chúng ta có kết quả như sau:
![ketqua](https://quanap5.github.io/img/demoOnsingleImage.jpg)

[Demo](https://github.com/quanap5/quanap5.github.io/blob/master/img/20190303_021246.mp4)

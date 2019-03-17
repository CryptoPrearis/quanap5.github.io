---
layout: post
title: Tracking trong xử lý ảnh
subtitle: tìm hiểu về tracking vật thể trong xử lý ảnh
tags: [object tracking, image processing]
comments: false
---

### Giới thiệu về tracking 

Ở bài viết này chúng ta sẽ xây dựng một ứng dụng tracking object gồm 2 bước như sau:
- Bước 1: Xác định vật thể dựa vào màu sắc
- Bước 2: Tracking vật thể đó trong chuỗi frame của video hoặc stream

### Demo ứng dụng nho nhỏ

![intro](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/demo.JPG)

### Code

Đây là đoạn code để tracking vật thể màu xanh lá cây từ video có sẵn hoặc từ webcam.

```python
# import the necessary packages
from collections import deque
from imutils.video import VideoStream
import numpy as np
import argparse
import cv2
import imutils
import time

# construct the argument parse and parse the arguments
ap = argparse.ArgumentParser()
ap.add_argument("-v", "--video",
	help="path to the (optional) video file")
ap.add_argument("-b", "--buffer", type=int, default=64,
	help="max buffer size")
args = vars(ap.parse_args())

# define the lower and upper boundaries of the "green"
# ball in the HSV color space, then initialize the
# list of tracked points
greenLower = (29, 86, 6)
greenUpper = (64, 255, 255)
pts = deque(maxlen=args["buffer"])

# if a video path was not supplied, grab the reference
# to the webcam
if not args.get("video", False):
	vs = VideoStream(src=0).start()

# otherwise, grab a reference to the video file
else:
	vs = cv2.VideoCapture(args["video"])

# allow the camera or video file to warm up
time.sleep(2.0)

# keep looping
while True:
	# grab the current frame
	frame = vs.read()

	# handle the frame from VideoCapture or VideoStream
	frame = frame[1] if args.get("video", False) else frame

	# if we are viewing a video and we did not grab a frame,
	# then we have reached the end of the video
	if frame is None:
		break

	# resize the frame, blur it, and convert it to the HSV
	# color space
	frame = imutils.resize(frame, width=600)
	blurred = cv2.GaussianBlur(frame, (11, 11), 0)
	hsv = cv2.cvtColor(blurred, cv2.COLOR_BGR2HSV)

	# construct a mask for the color "green", then perform
	# a series of dilations and erosions to remove any small
	# blobs left in the mask
	mask = cv2.inRange(hsv, greenLower, greenUpper)
	mask = cv2.erode(mask, None, iterations=2)
	mask = cv2.dilate(mask, None, iterations=2)

	# find contours in the mask and initialize the current
	# (x, y) center of the ball
	cnts = cv2.findContours(mask.copy(), cv2.RETR_EXTERNAL,
		cv2.CHAIN_APPROX_SIMPLE)
	cnts = imutils.grab_contours(cnts)
	center = None

	# only proceed if at least one contour was found
	if len(cnts) > 0:
		# find the largest contour in the mask, then use
		# it to compute the minimum enclosing circle and
		# centroid
		c = max(cnts, key=cv2.contourArea)
		((x, y), radius) = cv2.minEnclosingCircle(c)
		M = cv2.moments(c)
		center = (int(M["m10"] / M["m00"]), int(M["m01"] / M["m00"]))

		# only proceed if the radius meets a minimum size
		if radius > 10:
			# draw the circle and centroid on the frame,
			# then update the list of tracked points
			cv2.circle(frame, (int(x), int(y)), int(radius),
				(0, 255, 255), 2)
			cv2.circle(frame, center, 5, (0, 0, 255), -1)

	# update the points queue
	pts.appendleft(center)

	# loop over the set of tracked points
	for i in range(1, len(pts)):
		# if either of the tracked points are None, ignore
		# them
		if pts[i - 1] is None or pts[i] is None:
			continue

		# otherwise, compute the thickness of the line and
		# draw the connecting lines
		thickness = int(np.sqrt(args["buffer"] / float(i + 1)) * 2.5)
		cv2.line(frame, pts[i - 1], pts[i], (0, 0, 255), thickness)

	# show the frame to our screen
	cv2.imshow("Frame", frame)
	key = cv2.waitKey(1) & 0xFF

	# if the 'q' key is pressed, stop the loop
	if key == ord("q"):
		break

# if we are not using a video file, stop the camera video stream
if not args.get("video", False):
	vs.stop()

# otherwise, release the camera
else:
	vs.release()

# close all windows
cv2.destroyAllWindows()
```

### Tìm hiểu code

Ngoài các gói quen thuộc chúng ta nên để ý gói deque, cái này dùng để lưu các giá trị location của object trong quá khứ. Hay nói cách khác là vị trị của object trong các frame trước. Có thể tìm hiểu thêm về cấu trúc của deque tai [đây] (https://docs.python.org/2/library/collections.html#collections.deque). Việc duy trì giá trị trong một hàng đợi giúp chúng ta vẽ được hiệu ứng tracking theo vật thể giúp ảo diệu hơn :-smile:

Lần đầu chạy bạn có thể sử dung tham số truyền vào là --video path_of_your_video. Sau đó chuyển sang chạy với webcam.

Ngoài ra có tham số bufer bạn có thể thay đổi. bufer la giá trị mã của deque. Cái hàng đợi chúng ta dùng để lưu các giá trị trước. Ta sẽ dùng cái này để vẽ cái đuôi tracking cho vật thể. Buffer cang to thì cái đuôi tracking càng dài và ngược lại.

![long](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/longlongTracking.JPG)

![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/longTracking.JPG)

![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/shortTracking.JPG)

Chúng ta dựa vào màu sắc để detect object vì vậy chúng ta cần định nghĩa khoảng giá trị greenLower và greenUpper như trong code.

```python
# define the lower and upper boundaries of the "green"
# ball in the HSV color space, then initialize the
# list of tracked points
greenLower = (29, 86, 6)
greenUpper = (64, 255, 255)
```

Một hàm range-detector từ gói imutils được sử dụng để dettect vật thể màu green từ 2 giá trị color boundaries đã được cung cấp.

Tiếp theo là đoạn code để extract frame từ video hoặc là từ webcam đựa vào tham số --video có hay không.

```python
 if a video path was not supplied, grab the reference
# to the webcam
if not args.get("video", False):
	vs = VideoStream(src=0).start()

# otherwise, grab a reference to the video file
else:
	vs = cv2.VideoCapture(args["video"])

# allow the camera or video file to warm up
time.sleep(2.0)
```

Tiếp theo là một vòng lặp để xử lý hết frame của video hoặc là cho đến khi chúng ta nhấn "q" để thoát chương trình (code ở phần sau).

Chúng ta để ý phần tiền xử lý video với các xử lý như: resize() để tính toán nhanh hơn, blur để giảm noise tần số cao, và chuyển đổi không gian màu để kết quả được ổn định hơn.

```python
# keep looping
while True:
	# grab the current frame
	frame = vs.read()

	# handle the frame from VideoCapture or VideoStream
	frame = frame[1] if args.get("video", False) else frame

	# if we are viewing a video and we did not grab a frame,
	# then we have reached the end of the video
	if frame is None:
		break

	# resize the frame, blur it, and convert it to the HSV
	# color space
	frame = imutils.resize(frame, width=600)
	blurred = cv2.GaussianBlur(frame, (11, 11), 0)
	hsv = cv2.cvtColor(blurred, cv2.COLOR_BGR2HSV)

	# construct a mask for the color "green", then perform
	# a series of dilations and erosions to remove any small
	# blobs left in the mask
	mask = cv2.inRange(hsv, greenLower, greenUpper)
	mask = cv2.erode(mask, None, iterations=2)
	mask = cv2.dilate(mask, None, iterations=2)

```

Chúng ta thực sự đi tìm vị trí của vật thể tử dòng code cv2.inRange(hsv, greenLower, greenupper). Output của hàm inRange là mask dạng binary kiểu như sau.

![mask](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/mask_tracking.JPG)

Ký thuật ăn mòn và bồi đắp có thể được sử dụng để loại bỏ những blob nhỏ từ mask.

Sau đây là đoạn code xử lý trên frame để vẽ outline của vật thể.

```python
# find contours in the mask and initialize the current
	# (x, y) center of the ball
	cnts = cv2.findContours(mask.copy(), cv2.RETR_EXTERNAL,
		cv2.CHAIN_APPROX_SIMPLE)
	cnts = imutils.grab_contours(cnts)
	center = None

	# only proceed if at least one contour was found
	if len(cnts) > 0:
		# find the largest contour in the mask, then use
		# it to compute the minimum enclosing circle and
		# centroid
		c = max(cnts, key=cv2.contourArea)
		((x, y), radius) = cv2.minEnclosingCircle(c)
		M = cv2.moments(c)
		center = (int(M["m10"] / M["m00"]), int(M["m01"] / M["m00"]))

		# only proceed if the radius meets a minimum size
		if radius > 10:
			# draw the circle and centroid on the frame,
			# then update the list of tracked points
			cv2.circle(frame, (int(x), int(y)), int(radius),
				(0, 255, 255), 2)
			cv2.circle(frame, center, 5, (0, 0, 255), -1)

	# update the points queue
	pts.appendleft(center)
```

Bắt đàu với hàm tìm contour rất hay nói trong mấy bài viết gần đây. Và khởi tạo biến là trong tâm của vật thể = none.

Tiếp theo check ít nhất phải có một contour và ta sẽ tìm contour lớn nhất. Tính đường tròn nhỏ nhất bao được blob này. Và tính center thông qua hàm moment() support bởi openCV.

Ta nên check đọ lớn của bán kính hình tròn vừa tìm được để đảm bảo nó đủ lơn và vẽ lên frame.

Hàm cuối cùng là nhét điểm vào pts để vẽ đuôi tracking.

Bước cuối cùng là ta vẽ đuôi tracking vật thể. 

```python
# loop over the set of tracked points
	for i in range(1, len(pts)):
		# if either of the tracked points are None, ignore
		# them
		if pts[i - 1] is None or pts[i] is None:
			continue

		# otherwise, compute the thickness of the line and
		# draw the connecting lines
		thickness = int(np.sqrt(args["buffer"] / float(i + 1)) * 2.5)
		cv2.line(frame, pts[i - 1], pts[i], (0, 0, 255), thickness)
```


### Kết thúc


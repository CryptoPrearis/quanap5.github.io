---
layout: post
title: Ứng dụng chấm thi trắc nghiệm
subtitle: Chấm thi trắc nghiệm sử dụng xử lý ảnh
tags: [document, scanner, multi-choise]
comments: false
---



### Ứng dụng chấm điểm trắc nghiệm sử dụng xử lý ảnh

Trong bài viết này chúng ta sẽ học làm thế nào để thữ hiện một ứng dụng chấm điểm trắc nghiệm. Ký thuật được dùng gọi là Opticak Mark Recognition (OMR). Để hoàn thành công việc này một số kỹ thuật của những bài học trước như lấy đường bao, kỹ thuật blur ảnh,...

### OMR là gì?

Optical Mark Recognition hay gọi tắt là OMR là quá trình nhận diện đánh dấu và nội suy được ý nghĩa có nó.

Và ứng dụng đơn giản là chấm điểm trắc nghiệm. 

### Tiến hành thực hiện bằng code Python, OpenCV

Đây là ví dụ về tờ trả lời trong thi trắc nghiệm. 
![vidusheet](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/intro_tracnghiem.JPG)

Các bước để xử lý:
- Bước 1: Xác định tờ trả lời
- Bước 2: Áp dụng phép yển đổi (perspective transform) để trích xuất tờ trả lời theo hứng top-down view.
- Bước 3: Trích xuất "bubbed" cho từng dòng trả lời
- Bước 4: Sắp xếp 
- Bước 5: Xác định câu trả lời cho từng dòng
- Bước 6: Đối chiếu câu trả lời chính xác
- Bước 7: Lặp lại cho các câu hỏi khác trong bài test.

Phần tiếp theo sẽ là hướng dẫn về quá trình thực hiện.

 ### code
 
 ```python
 # import the necessary packages
from imutils.perspective import four_point_transform
from imutils import contours
import numpy as np
import argparse
import imutils
import cv2

# construct the argument parse and parse the arguments
ap = argparse.ArgumentParser()
ap.add_argument("-i", "--image", required=True,
	help="path to the input image")
args = vars(ap.parse_args())

# define the answer key which maps the question number
# to the correct answer
ANSWER_KEY = {0: 1, 1: 4, 2: 0, 3: 3, 4: 1}
 ```
 
Chắc chắn là chúng ta đã cài OpenCV và Numpy. Bên cạnh đó gói imtils cũng cần thiết, gói xử lý ảnh này tôi đã giới thiệu ở các bài viết trước.

Chúng ta cần lưu ý ANSWER_KEY là thông tin về câu trả lời đúng với vị trí của "bubbed".

Trong trường hợp này key 0 có nghĩa là là câu hỏi đầu tiên có đáp án đúng là B. Trong khi câu hỏi thứ hai (key 1) sẽ có đáp án đúng là E.

Theo quy luật này ta có bảng đáp án đúng là.

|Câu số| Đáp án|
|------|-------|
|#1| B|
|#2| E|
|#3| A|
|#4| D|
|#5| B|

Tiếp theo chúng ta sẽ tiền xử lý ảnh đầu vào với các xử lý như đọc ảnh (imread), chuyển ảnh về dạng gray (cvtColor), blur(GaussianBlur), tìm biên với thuật toán Canny.


```python
# load the image, convert it to grayscale, blur it
# slightly, then find edges
image = cv2.imread(args["image"])
gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
blurred = cv2.GaussianBlur(gray, (5, 5), 0)
edged = cv2.Canny(blurred, 75, 200)
```

Ta có kết quả nhẹ:

![preprocessing_tracnghiem](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/preprocessing_tracnghiem.JPG)

Tiếp dựa trên hình ảnh của đường biên. Chúng ta sẽ làm một việc khá quan trọng đó là tìm outline của tờ trả lời bằng kỹ thuật lấy contour.

```python
# find contours in the edge map, then initialize
# the contour that corresponds to the document
cnts = cv2.findContours(edged.copy(), cv2.RETR_EXTERNAL,
	cv2.CHAIN_APPROX_SIMPLE)
cnts = imutils.grab_contours(cnts)
docCnt = None

# ensure that at least one contour was found
if len(cnts) > 0:
	# sort the contours according to their size in
	# descending order
	cnts = sorted(cnts, key=cv2.contourArea, reverse=True)

	# loop over the sorted contours
	for c in cnts:
		# approximate the contour
		peri = cv2.arcLength(c, True)
		approx = cv2.approxPolyDP(c, 0.02 * peri, True)

		# if our approximated contour has four points,
		# then we can assume we have found the paper
		if len(approx) == 4:
			docCnt = approx
			break

cv2.drawContours(image, [docCnt],-1, (255,0,0), 2)
cv2.imshow("Contour", image)
```

 Áp dụng tìm contour bawgf hàm cv2.findContours có trong cv. Sau đó những contour này được sort từ diện tích lớn nhất cho đến nhỏ nhất (cnts = sorted(cnts, key=cv2.contourArea, reverse=True)) sau khi chúng ta chắc chắn có ít nhất 1 contuor dc phát hiên (len() >0). Kết quả của sort() chúng ta sẽ có contour của tờ trả lời trác nghiệm sẽ ở đầu của danh sách cac contour. Tuy nhiên điều kiện về contour và dienj tích cũng có thể là chưa đủ, nên chúng ta sẽ check thêm một điều kiện về số điểm của approxPolyDP(). Đây là hàm ước lượng hình thù của contour [more here](https://docs.opencv.org/2.4/modules/imgproc/doc/structural_analysis_and_shape_descriptors.html).

Kết quả.

![contour](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/warp_tracnghiem.JPG)

Như vậy chúng ta đã tìm được outline của phần tờ trả lời. Và đến lúc chúng ta dùng phép chuyển đổi rất hay trong xử lý ảnh là perspective transform để nhận đc hình ảnh nhìn từ trên xuống.

```python
# apply a four point perspective transform to both the
# original image and grayscale image to obtain a top-down
# birds eye view of the paper
paper = four_point_transform(image, docCnt.reshape(4, 2))
warped = four_point_transform(gray, docCnt.reshape(4, 2))
```
Bạn có thể tìm hiểu thêm về four_point_transform tại [đây](https://quanap5.github.io). 

Kết quả là:

![warpedimage](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/warp_tracnghiem.JPG)

Rất là giống kết quả của bài scan document (ứng scan tài liệu) hôm trước phải không? Nhưng mục tiêu cuối cùng là phải check được câu đúng câu sai trong tờ trả lời. Ví vậy chúng ta cần đoạn code.

```python
# apply Otsu's thresholding method to binarize the warped
# piece of paper
thresh = cv2.threshold(warped, 0, 255,
	cv2.THRESH_BINARY_INV | cv2.THRESH_OTSU)[1]

# find contours in the thresholded image, then initialize
# the list of contours that correspond to questions
cnts = cv2.findContours(thresh.copy(), cv2.RETR_EXTERNAL,
	cv2.CHAIN_APPROX_SIMPLE)
cnts = imutils.grab_contours(cnts)
questionCnts = []

# loop over the contours
for c in cnts:
	# compute the bounding box of the contour, then use the
	# bounding box to derive the aspect ratio
	(x, y, w, h) = cv2.boundingRect(c)
	ar = w / float(h)

	# in order to label the contour as a question, region
	# should be sufficiently wide, sufficiently tall, and
	# have an aspect ratio approximately equal to 1
	if w >= 20 and h >= 20 and ar >= 0.9 and ar <= 1.1:
		questionCnts.append(c)

```

Đầu tiên là kỷ thuật binary. với kết quả thu được.

![binary_tracnghiem](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/binary_tracnghiem.JPG)

Một lần nữa chúng ta lại phải đi tìm contour trên ảnh binary để phân biệt background và foreground. Đoạn code còn lại trên là nhằm mục đich này. Để xác định đâu là những khoang tròn "bubbed" chúng ta tính bounding box (cv2.boundingRect(c)) suy ra đc tỷ lệ và ta chon tỷ lệ (0.9-1.1) để đảm bảo là hình vuông. Đồng thời chiều chiều cao và chiều rộng ít nhất là 20 pixels. Ta có kết quá trên cả tuyệt vời như sau.

![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/dapan_tracnghiem.JPG)

### Tính điểm

Tiếp theo chúng ta sẽ thực hiên để tính điểm của bài thi trắc nghiệm. Bước trên chúng ta đã lọc được các vùng đánh dấu như kết quả (highlight bởi khoanh tròn màu đen).

```python
# sort the question contours top-to-bottom, then initialize
# the total number of correct answers
questionCnts = contours.sort_contours(questionCnts,
	method="top-to-bottom")[0]
correct = 0

# each question has 5 possible answers, to loop over the
# question in batches of 5
for (q, i) in enumerate(np.arange(0, len(questionCnts), 5)):
	# sort the contours for the current question from
	# left to right, then initialize the index of the
	# bubbled answer
	cnts = contours.sort_contours(questionCnts[i:i + 5])[0]
	bubbled = None
```

Chúng ta phải sort questionCnts từ trên xuống dưới (top-to-down). Mục đích của việc này là đảm bảo thự các contour sẽ đúng thưs tự các câu hỏi.

Chúng ta cũng khai báo biến corect =0 để track số lượng câu đúng.

Bắt đầu ở vòng lặp, mỗi câu hỏi có 5 đáp án (A-B-C-D-E). Ta sẽ dùng mảng NumPy và sort current question từ trái sang phải. Việc này là cần thiết vì trước đó ta đã sort các bubbed từ trên xuống nhưng chưa thể chắc chắn cac bubbed này sắp xếp từ trái sang phải.

Đê xem sắp xếp của chúng ta thế nào hãy xem kết qua sau.

![sorttop_down](https://media.giphy.com/media/5zw9WiS9isqUoYbyOC/giphy.gif) ![](https://media.giphy.com/media/DZYtMs93LUhFZjZ1zd/giphy.gif)

Với kết quả đã thực hiên được ở tren, chúng ta tiếp theo cần xác định vị trí bubbed nào được đánh dấu. Bằng việc sử dụng kỷ thuật thresh và đếm số lượng pixel khác không. 

```python
	for (j, c) in enumerate(cnts):
		# construct a mask that reveals only the current
		# "bubble" for the question
		mask = np.zeros(thresh.shape, dtype="uint8")
		cv2.drawContours(mask, [c], -1, 255, -1)

		# apply the mask to the thresholded image, then
		# count the number of non-zero pixels in the
		# bubble area
		mask = cv2.bitwise_and(thresh, thresh, mask=mask)
		total = cv2.countNonZero(mask)

		# if the current total has a larger number of total
		# non-zero pixels, then we are examining the currently
		# bubbled-in answer
		if bubbled is None or total > bubbled[0]:
			bubbled = (total, j)
```

Đây là vòng lặp trên mỗi dòng bubbed. Chúng ta tạo một mặt nạ cho dòng đang được xét và đếm số lượng pixel khác không.

Kết quả.

![](https://media.giphy.com/media/7JEV4BVzQvJOLfJuYF/giphy.gif)

Rõ ràng ta thấy những đáp án được đánh dấu sẽ là phần có nhiều pixel khác không nhất.
Phần tiếp theo chúng ta sẽ tìm đáp án và cập nhật số lượng câu trả lời đúng.

```python
# initialize the contour color and the index of the
	# *correct* answer
	color = (0, 0, 255)
	k = ANSWER_KEY[q]

	# check to see if the bubbled answer is correct
	if k == bubbled[1]:
		color = (0, 255, 0)
		correct += 1
		
	# draw the outline of the correct answer on the test
	cv2.drawContours(paper, [cnts[k]], -1, color, 3)
```

Dựa đáp án được chọn là đúng hay sai. 

![ketqua](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/results.JPG)

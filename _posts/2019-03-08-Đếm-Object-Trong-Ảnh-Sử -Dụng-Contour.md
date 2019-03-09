---
layout: post
title: Đếm objects với OpenCV
subtitle: Xây dựng một application trong Image Processing
tags: [opencv, image processing, contour, counting]
comments: false
---

## Giới thiệu về nội dung

Ở bài trước chúng ta đã học qua các khái niệm cơ bản cũng như những hàm cơ bản cần thiết cho quá trình học OpenCV. Và hôm nay sẽ là việc xây dựng một ứng dụng nho nhỏ sử dụng một kỹ thuật là lấy đương bao và đếm số lượng object có trong bức ảnh. Kỹ thuật lấy đường bao cũng là một trong những bước tiền xử lý rất quan trong cho nhiều ứng dụng phức tap hơn. Nào bắt đầu thôi.

### Code
```python
# argument) and display the image to our screen
image = cv2.imread(args["image"])
cv2.imshow("Image", image)
cv2.waitKey(0)

# convert the image to grayscale
gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
cv2.imshow("Gray", gray)
cv2.waitKey(0)

# applying edge detection we can find the outlines of objects in
# images
edged = cv2.Canny(gray, 30, 150)
cv2.imshow("Edged", edged)
cv2.waitKey(0)

# threshold the image by setting all pixel values less than 225
# to 255 (white; foreground) and all pixel values >= 225 to 255
# (black; background), thereby segmenting the image
thresh = cv2.threshold(gray, 225, 255, cv2.THRESH_BINARY_INV)[1]
cv2.imshow("Thresh", thresh)
cv2.waitKey(0)

# find contours (i.e., outlines) of the foreground objects in the
# thresholded image
cnts = cv2.findContours(thresh.copy(), cv2.RETR_EXTERNAL,
                        cv2.CHAIN_APPROX_SIMPLE)
cnts = imutils.grab_contours(cnts)
output = image.copy()

# loop over the contours
for c in cnts:
    # draw each contour on the output image with a 3px thick purple
    # outline, then display the output contours one at a time
    cv2.drawContours(output, [c], -1, (240, 0, 159), 3)
    cv2.imshow("Contours", output)
    cv2.waitKey(0)

# draw the total number of contours found in purple
text = "I found {} objects!".format(len(cnts))
cv2.putText(output, text, (10, 25), cv2.FONT_HERSHEY_SIMPLEX, 0.7,
            (240, 0, 159), 2)
cv2.imshow("Contours", output)
cv2.waitKey(0)

# we apply erosions to reduce the size of foreground objects
mask = thresh.copy()
mask = cv2.erode(mask, None, iterations=5)
cv2.imshow("Eroded", mask)
cv2.waitKey(0)

# similarly, dilations can increase the size of the ground objects
mask = thresh.copy()
mask = cv2.dilate(mask, None, iterations=5)
cv2.imshow("Dilated", mask)
cv2.waitKey(0)

# a typical operation we may want to apply is to take our mask and
# apply a bitwise AND to our input image, keeping only the masked
# regions
mask = thresh.copy()
output = cv2.bitwise_and(image, image, mask=mask)
cv2.imshow("Output", output)
cv2.waitKey(0)
```

Bây giờ lúc chúng ta cũng tìm hiểu các bước của ứng dung này. Xem có gì hay ho.
Chúng ta sẽ sử dụng đoạn script Python trên cùng với OpenCV để đểm số object có trong bước ảnh sau.
![input](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/tetris_blocks.png).

Các kỹ thuật được sử dụng bao gồm:
- Chuyển ảnh đầu vào sang grayscale với OpenCV
- Kỹ thuật edge detection (lấy đường biên)
- Binary với thresholding
- Tìm, vẽ và đếm đường bao (Contour)
- Kỹ thuật ăn mòn và mở rộng (Erosion, dilation)
- Masking an image

Chúng ta có thể học thêm về: Python, argparse and command line argument tại [đây]()

### Chuyển ảnh sang ảnh xám (GrayScale)
```python
# load the input image (whose path was supplied via command line
# argument) and display the image to our screen
image = cv2.imread(args["image"])
cv2.imshow("Image", image)
cv2.waitKey(0)

# convert the image to grayscale
gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
cv2.imshow("Gray", gray)
cv2.waitKey(0)
```

Chúng ta dùng hàm imread để load ảnh vào bộ nhớ, và hiện thị bằng hàm imshow được support bởi thư viện OpenCV. Trong quá trình xử lý yêu cầu phải detect đường biên và binarty dùng thresholding nên chúng ta cần chuyển ảnh ban đầu về dạng ảnh xảm thông qua hàm gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY).

Đây là kết quả.

![grayscale](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/gray.JPG)

### Edge detection

Kỹ thuật này cũng rất hữu ích trong việc tìm đường bao và segmentation of object.
```python
# applying edge detection we can find the outlines of objects in
# images
edged = cv2.Canny(gray, 30, 150)
cv2.imshow("Edged", edged)
cv2.waitKey(0)
```
Kết quả.

![grayscale](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/edge.JPG)

### Thresholding
Phép thresholding cũng là một kỹ thuật quan trọng khác nhằm loại bỏ những vùng sáng hơn hoặc vùng tối hơn trong bức ảnh. Giúp cho việc contour được dễ dàng hơn.
```python
# threshold the image by setting all pixel values less than 225
# to 255 (white; foreground) and all pixel values >= 225 to 255
# (black; background), thereby segmenting the image
thresh = cv2.threshold(gray, 225, 255, cv2.THRESH_BINARY_INV)[1]
cv2.imshow("Thresh", thresh)
cv2.waitKey(0)
```
Chúng ta cùng chú ý vào dòng code cv2.threshold(gray, 225, 255, cv2.THRESH_BINARY_INV). Tất cả các điểm ảnh có giá trị gray lớn hơn 225 sẽ chuyển thành 255 (màu trắng) còn lại những điểm ảnh có giá trị gray nhỏ hơn 225 sẽ chuyển về giá trị 0 (màu đen). Màu đen sẽ tương ứng với background và màu trắng là foreground. Các bạn có thể tìm hiểu cụ thể và chi tiết hơn tại [link](https://docs.opencv.org/3.4.0/d7/d1b/group__imgproc__misc.html#ggaa9e58d2860d4afa658ef70a9b1115576a19120b1a11d8067576cc24f4d2f03754).

Kết quả là.

![grayscale](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/thresholding.JPG)

### Xác định và vẽ đường bao
```python
# find contours (i.e., outlines) of the foreground objects in the
# thresholded image
cnts = cv2.findContours(thresh.copy(), cv2.RETR_EXTERNAL,
                        cv2.CHAIN_APPROX_SIMPLE)
cnts = imutils.grab_contours(cnts)
output = image.copy()

# loop over the contours
for c in cnts:
    # draw each contour on the output image with a 3px thick purple
    # outline, then display the output contours one at a time
    cv2.drawContours(output, [c], -1, (240, 0, 159), 3)
    cv2.imshow("Contours", output)
    cv2.waitKey(0)

# draw the total number of contours found in purple
text = "I found {} objects!".format(len(cnts))
cv2.putText(output, text, (10, 25), cv2.FONT_HERSHEY_SIMPLEX, 0.7,
            (240, 0, 159), 2)
cv2.imshow("Contours", output)
cv2.waitKey(0)
```
Kết quả như sau.

![animation](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/Webp.net-gifmaker.gif)

Kết quả khác.

![grayscale](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/counting2.JPG)

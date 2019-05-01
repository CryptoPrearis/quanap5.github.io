---
layout: post
title: Thực hiện Maxpool sử dụng ArrayFire
subtitle: tự học, maxpooling
tags: [Convnet]
comments: false
---

### Giới thiệu

Chúng ta đã biết ConvNet có thể gồm các layer sau: Convolution, Pooling,  Fully Connected, and Softmax. Mặc dù nó có thể không đúng với các mạng được phát triển gần đây.

Nhưng conv và pooling layer vẫn đóng vai trò quan trọng. Ở bài trước chúng ta đã tìm hiểu cách thự hiện lớp convolution. Hôm nay tôi sẽ tìm hiểu và thực hiện lớp maxPooling.

### Vì sao chúng ta cần lớp Pooling

Trong khi lớp convolution thì thực hiện các bộ lọc convolution lên tín hiệu đầu vào: ví dụ ảnh thì lớp pooling có mục đích là giảm chiều output. Giảm chiều (kích thước) của ảnh đầu vào rất cần thiết để đối phó với kiểu dữ liệu nhiều chiều. Bởi giữ nguyên kích thước thì khối lướng tính toán sẽ rất lớn và kích thước model cũng rất lớn.

Để hiểu pooling chúng ta có thể hình dung giống lớp convolution nhưng thay vì thực hiện phép convolution lên mỗi local patch thì bây giờ chúng ta sẽ giảm kích thước như down sampling bằng cách lấy giá trị mã hoặc min hoặc average trong local window. Thậm chí median value cũng có thể được sử dụng.

Các loại pooling layer:
- Max pooling
- Min pooling
- Average pooling

Bạn có thể tìm hiểu và đọc thêm tại [đây](http://cs231n.github.io/convolutional-networks/#pool)

### Minh họa

![](http://cs231n.github.io/assets/cnn/maxpool.jpeg)

### Cách thực hiện

![](https://leonardoaraujosantos.gitbooks.io/artificial-inteligence/content/image_folder_4/MaxPoolingLayer.png)
 

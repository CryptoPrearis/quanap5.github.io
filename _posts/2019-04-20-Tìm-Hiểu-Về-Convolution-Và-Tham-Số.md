---
layout: post
title: Giới thiệu về các lọa Convolution dùng trong DeepLearning
subtitle: lập trình, học tập
tags: [Dêp learning, convolution, CNN]
comments: false
---

### Giới thiệu

Hôm nay tôi sẽ tìm hiểu về các lọa convolution và cac đặc điểm cũng như tính chất của nó thông qua các hình ảnh minh họa tôi biết qua các tài liệu trên mạng. Để đơn giản và dễ hiểu chúng ta sẽ chỉ tâp trung vào 2D convolution.

![minhhoa](https://cdn-images-1.medium.com/max/750/1*1okwhewf5KCtIPaFib4XaA.gif)

### Convolution

Trước hết chúng ta cần hiểu qa một số tham số liên quan đến phep toán convolution của hình minh trên. Những thông số ấy bao gồm.
- Kernel Size: Tham số này cho ta biết trường quan sát của convolution (field ò view). Ví dụ như, 3x3, 5x5,...
- Stride: Tham số này định nghĩa bước nhảy của của sổ convolution trong quá trình chạy trên dữ liệu đầu vào.Bình thường chúng ta thường bắt gặp stride = 1. Nhưng để tính toán nhanh hơn về sau, kiêu như phép downsampling chúng ta có thể thiết lập stride >= 2.
- Padding: đây là tham số để dịnh nghĩa chúng ta có đường bao để đảm bảo kich thước output sau khi convolution sẽ bằng với input hay không?
- Channel: Một tham số nữa rất hay bị quên và khó hình dung là channels. Việc convolution layer xử lý một đầu vào với nhiều kênh (channel).

# Các kiểu phân lọa Convolution

Chúng ta có thể phan loại thành convolution 1D, Concolution 2D hay Concolution 3D

# Dilated convolution

Tôi đặc biệt thích thú với kiểu dilated convolution. Nếu bạn chưa hình dung được một tham số khác có tên là dilated rate thi hãy cùng tìm hiểu nhé.

Hình sau đay minh họa cho lạo dilated convolution này.

![](https://cdn-images-1.medium.com/max/750/1*SVkgHoFoiMZkjy54zM_SUw.gif)

Để hiểu dilation rate chúng ta có thể hình dung dựa vào hình minh họa như trên. Ví dụ một concolution với kernel 3x3 và dilation rate = 2 thì nó tương tự như phép convolution với của sổ 5x5 nhưng chúng ta chỉ quan tâm 9 phần tử in đậm.

Loại convolution này sẽ làm rộng (field of view) nhưng vẫn giữ nguyên khối lượng tính toán. Dilated convolutions được sử dụng nhiều trong các ứng dụng real-time segmentation.

# Transposed convolution

# Separable Convolution

Lọa này có nghĩa là thay vì chúng ta convolution tren một kernel thi chũng ta sẽ convolution tren 2 kernel khac nhau. Ví dụ 2D convolution bang cách thực hiên 2 convolution 1D.

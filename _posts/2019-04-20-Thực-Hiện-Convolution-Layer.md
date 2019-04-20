---
layout: post
title: Thực hiện Convolution layer với thư viện Numpy
subtitle: tự học, deep learning
tags: [Deep learning, convolution, CNN, implement]
comments: false
---

### Giới thiệu

Mạng Convolution Neural Network hay ngắn gọn gọi là CNN, sử dụng rất rỗng rãi mọi nơi. Hầu hết các hệ thống computer vision gần đây đều xây dựng dựa trên CNN cho các mạng deep learning bên trong hệ thống.

Chúng ta có thể hiểu Convolution layer là một bộ lọc đẻ feature extractor. Ngày này chúng ta có thể sử dụng dễ dàng sử dụng lớp convolution với nhiều thư viện hỗ trợ cho phát triển deep learning nhử tensorflow, mxnet, keras, cntk...

Tuy nhiên để hiểu rõ thì hôm nay chúng ta cùng tham khảo bài viết [link](https://wiseodd.github.io/techblog/2016/07/16/convnet-conv-layer/) về việc thực hiên lớp convolution như thế nào chỉ sử dụng numpy.

### Conv layer

Như chúng ta đã biết, mỗi layer trong mạng neural network bao gồm 2 quá trinh tính toán chính là forward và backpropagation. Và lớp convolution cũng thế và chúng cũng cần được thực hiện dựa trên hai quá trình đó.

Trước khi bắt đầu chúng ta cần có hiểu biết về Convolution. Để hiểu được lý thuyết này chúng ta có thể đọc qua cource này nhé [link](http://cs231n.github.io/convolutional-networks/).

![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/CourceCS231.PNG)

### Convolution forward process

Như trong tài liệu trê, chúng ta có thể xem convolution như một phép nhân ma trận. Tại mỗi một local path chúng ta cần áp dụng mmotj bộ filter nào đó lên image bằng cách nhân (dot product). 

Định nghĩa hàm conv_forward:

```python
def conv_forward(X, W, b, stride=1, padding=1):
    pass
```

Hàm này của lớp convolution sẽ chấp nhận an input in X: DXCXHXW, với input filter X: NFXCXHFXHW and bias b: Fx1
- D : số lượng đầu vào
- C: Số lượng kênh đầu vào
- H: Chiều cao đầu vào
- W: Chiều rộng đầu vào
- NF: số lượng filter
- HF: Chiều cao filter
- HW: chiều rộng filter

Các tham số quan trọng của convolution nữa là Stride và Padding. Như đã nói ở trên chúng ta sẽ thực hiện forward cho lớp convolution dựa vào phép nhân ma trận giữa input và weight. Để làm được điều này chúng ta sẽ tận dụng hàm im2col, hàm này cần thiết để unwrap ảnh đầu vào theo kích thước filter, stride và chiều rộng. 

Học thêm [here](https://www.mathworks.com/help/images/ref/im2col.html)

Ví dụ với một ảnh đầu vào 1x1x10x10 và một filter có kich thước 3x3 (1x1x3x3 là kích thước của filter đóng vai trò weight). Chúng ta sẽ sử dụng stride =1 , padding = 1.

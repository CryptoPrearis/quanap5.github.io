---
layout: post
title: Review bài báo về Convolution dùng trong DeepLearning
subtitle: lập trình, học tập
tags: [Deep learning, convolution, CNN]
comments: false
---

### Giới thiệu nội dung

Hôm nay tôi sẽ review cũng như nêu những điểm hay ho về Convolution ở trong bài báo [link](https://arxiv.org/pdf/1603.07285.pdf).

![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/GuideCNN.PNG)

### Nội dung chính

-Convolution:
-Pooling:

![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/ContentCNN.PNG)

### Chi tiết (Điểm hay ho)

Mạng Deep Convolutional neural network (CNNs) đang trở thành trái tim của nhiều mạng deep learning. Mặc dù CNN đã được sử dụng rất lâu rồi nhưng hiện tại nó vẫn tỏ ra rất quang trong và sử dụng phổ biến trong nhiều nghiên cứu gần đay của mạng deep learning.

Hôm nay tôi sẽ điểm lại các mối quan hệ giữa các tham số của Convolution layer. Và cung xấp hình ảnh (tôi crop từ bài báo gốc) để giúp bạn hình dung được vấn đề.

Khi làm quen với neural network chugs ta đã biết về phép afine transformation: Được hiểu là một vector sẽ dc xem như input và được nhân với một matrix để đưa ra output (có thể cộng thêm bias vector). Phép biến đổi này có thể dc áp dụng cho bất kỳ lọai input nào, có thể là image, soud, audio, ... Chúng có cùng một số tính chất như sau:
- có thể biểu diễn như ma trận nhiều chiều
- Có một hay nhiều axes (chiều dài chiều rông cho đối tuwowngi image, time cho sound,..)
- Có thể có thêm một axis gọi là kênh ví dụ trong image có thể có 3 kênh (R,G,B), trong stereo audio có thể có 2 kênh (left, right)
### pooling

![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/pooling1.PNG)

Để giảm tính toán nhưng vẫn giữ được thông tin chính của đối tượng.

### Convolution

Chúng ta gói gọn vào 2D convolution với các các giả thiết sau:

-2D discrete convolution (2D)
- Square input (i1=i2=i)
- Square kernel size (k1=k2=k)
- Same stride along both axes (s1=s2=s)
- same zero padding along both axes (p1=p2=p)


Convolution với N=2, i =5, k=3, s=2 và p=1;
![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/Cnn1.PNG)


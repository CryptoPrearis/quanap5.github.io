---
layout: post
title: Review paper: Batch Normalization Accelerate Deep Network Training by Reducing Internal Covariate Shift
subtitle: selfstudy
tags: [Review, BatchNormal, Deep learning]
comments: false
---

### Tóm tắt

- Việc training các model Deep Learning thường rất phức tạp bởi sự thật rằng  phân bố dữ liệu đầu vào của mỗi lớp cũng thay đổi trong quá trình trainning., khi mà parametter của các layer phía trước nó thay đổi.
- Chính việc này làm thời gian trainning trỏe nên dài vì chúng ta phải chọn learning rate thấp và rất cẩn thận trong khâu tạo giátrij khởi tạo.
- Bài báo này đề xuất một lớp cho Deep Learning gọi là Batch Normalization.
- BN cho phép chúng ta có thể sử dụng learning rate lớn hơn và không cần quan tâm nhiều đến giá trị khởi tạo của weight. BN hoạt động như một regularizer.
- Vì thế đôi lúc chúng ta không cần sử dụng DropOut layer.


Kết luận từ bài báo:

- Batch Normalization đạt kết quả tương đương nhưng tốc đọ nhanh hơn 14 lần.

### Giới thiệu

- Mục đích của Batch Normalization là để giải quyết vấn đề sau: Sự thay đổi các trọng số trong model  trong quá trình training sẽ làm thay đổi phân bố của output tại mỗi layers. Điều này có nghĩa các layer phía sau cần phải tương thích với sự thay đổi này (hiểu như là noisy).
- Chúng ta thay đổi mini-batch để mean gần với 0 và phương sai gần với 1.

### Reduce Internal Covariate Shift

### Normalization với Minibatch Statistics

##Training
##Inference

###Ref

![question](https://www.quora.com/How-does-batch-normalization-behave-differently-at-training-time-and-test-time)

![](https://arxiv.org/pdf/1502.03167v3.pdf)

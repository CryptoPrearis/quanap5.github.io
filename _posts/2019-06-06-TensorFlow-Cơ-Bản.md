---
layout: post
title: TensorFlow Cơ Bản: Tensor, Shape, Graph, Session và Operation
subtitle: Backend Deep
tags: [neural network, RNN, tensorflow]
comments: false
---


### Tensor là gì?

Cái tên Tensorflow xuất phát từ cái cốt lõi của framework này là Tensor và Flow (Graph). Tất cả tính toán điều liên quan đến một đối tượng gọi là Tensor. Một tensor là một vector ta đã quen thuộc hoạc là matrix với N chiều, dùng để thể hiện tất cả các loại data cần thiết .Do vậy Tensor cũng đặc  trưng bới loại data và shape của nó. Shape là size các chiều của array hay matrix.

Tensor có thể được dùng như là input hoặc là kết quả của một phép tính toán giữa các tensors với nhau. Ở tensorflow, các operations được thực hiện bên trong  một khái niệm Grap (flow). Graph là tập hợp các phép tính liên tiếp nhau. Mỗi phép tính có thể hiểu như một op node và liên kết với nhau theo một hướng nào đó.

Đây là ví dụ về Graph.

![](https://raw.githubusercontent.com/SciSharp/TensorFlow.NET/master/docs/assets/tensors_flowing.gif)

Graph biểu diễn các tính toán và kết nối giữa chúng song có không giữ giá trị gì hết. Cái giữ giá trị chính là đối tượng ở hai đầu mỗi kết nối. Kết nối ta hiểu là luồng của tính toán thôi nhé. 

Trong MachineLearning, models được input với nhiều object gọi là feature vector. Feature vector là nguyên liệu cơ sở để tạo tensor.Các giá trị của feature vector sẽ đẩy vào các node thông qua các tensor. Và kết quả của tính toán cũng được trả ra đưới dạng tensor. Như vậy ta có thể view tất các phép toán và luồng dữ liệu chạy trong model qua graph.

### Các loại Tensor

Như đã biết các tính toán đều thông qua một hay nhiều tensor. Và một tensor có thể đặc trưng bởi.

- Name
- Shape
- Dtype

Tất nhiên mỗi phép toán sẽ cần xử lý và trình diễn trên tensor. Và mỗi tensor bằng 4 cách.

-tf.Variable
-tf.Constant
-tf.placeholder
-tf.SparseTensor

Lưu ý:

```
import tensorflow as tf
test_var = tf.Variable([111, 11, 1])
sess = tf.Session()
sess.run(test_var)

# Error!

sess.run(tf.global_variables_initializer())  # initialize variables
sess.run(test_var)
# array([111, 11, 1], dtype=int32)
```


---
layout: post
title: Sử dụng BatchSize trong quá trình Training và Predict với model LSTM
subtitle: Backend Deep
tags: [neural network, LSTM]
comments: false
---

### Mở đầu

Thư viện Keras sử dụng kỹ thuật Symbolic Mathematic để tính toán Gradient mà  không cần gọi hàm một cách tường minh. Thư viện support kỹ thuật này như Tensorflow, Pytorch và Theano.

Một mặt hạn chế của những thư viện này là kích thước  của data phải được định nghĩa trước và luôn luôn cố định trong cả quá trình training và prediction.

Trong bài toán sequence prediction, cũng rất có thể chúng ta sẽ chọn batch size rất lớn khi training model sử dung RNN layer nhưng chỉ muốn sử dụng batchsize = 1 example (observation) khi thực hiện prediction.

Từ thực tế yêu cầu của thư viện cũng như nhu cầu trong quá trình sử dụng LSTM*RNN) trong bài này tôi sẽ timf hiểu về vấn đề vừa nêu và các giai pháp để handle vấn đề này.

### Nội dung chính

Sau khi đọc xong nội dung này chúng ta có thể hình dung.

- Cách design một model để giai quyết bài toán sequence prediction sử dụng LSTM
- Thay đổi cấu hình cho LSTM trong phương pháp Online learning và Batch-based learning cũng như prediction
- Thay đổi giá trị batch size trong training và prediction
- 

Tôi sẽ chia nội dụng thành 6 phần nhỏ:

1. Batch size
2. Mô tả bài toán sequence prediction
3. LSTM model
4. Solution 1: Online learning (batch size = 1)
5. Solution 2: Batch Forecasting (Batch size = N)
6. Solution 3: Copy weights


### Environment

Sử dụng dịch vụ Cloud Colab của Google. [here](https://colab.research.google.com)

### On batch size

Một điểm thuận tiện của thư viện Keras đố là nó được xay dựng dựa trên thư viện tính toán Tennsorflow,... nơi symbolic algorithm được support để tự động tính toán backward (gradient, dẻivative calculate). Điểm này phát huy tác dụng với những model cồng kềnh phức tạp.

Nhưng điều bất tiện lại đến từ việc cấu hình khích thước trước lúc training cũng như predict sẽ làm cho chúng ta cảm thấy hơi confusing. Đặc biệt là batch size.

Batch size là số lượng sample data đi vào network model trước khi bản thân model được cập nhât ( cá tham số như weight, bias dc updated). Cái này đều tồn tại trong cả quá trình training và predicting.

Ta cũng nên hiểu batch size chỉ ra có bao nhiêu predicting cần được thực hiện trong 1 thời điẻm nào đó.

Thực batch size không là vấn đề quá lớn nếu batch size là giống nhau trong training và predicting nhưng điều chúng ta muốn thì lại khác. Thỉnh thoảng batch size cần thiết phải khác nhau hoàn toàn để cho phù hợp với bài toan thực tế. Ví dụ, chúng ta có thể có kết quả rất tôt với batchsize rất lớn thậm chí bàng số lượng mẫu training. Nhưng chỉ yêu cầu tính toán predicting chỉ trên 1 mẫu(1 obsersvation). 

Chính lý do này nên chúng ta thường custom batch size để fitting model trong quá tring training hơn là khi predicting.

Các phương để giải quyết vần đề vừa đưa ra.

### Mô tả Sequence prediction problem

Chúng ta sẽ minh họa với một bài toán đơn giản của sequence prediction để thấy được các giai pháp điều chỉnh batch size phù hợp cho cả quá trình training và quá trình prediction.

Ta có chuỗi data như sau.

```python
[0.0, 0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9]
```

Chúng ta có thể tạo chuỗi trên bằng dong code sau trên python

```
from pandas import concat
from pandas import DataFrame
# tạo sequence
length = 10;
sequence = [i/ float(length) for i in range(length)]
print(sequence)
```
Kết quả in ra:

```
[0.0, 0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9]
```

Bây giờ chúng ta cần convert chuỗi đã tạo ra thành bài toán supervised learning. Bằng cách tạ cặp input-output bằng đoạn code sau.
 ```
from pandas import concat
from pandas import DataFrame
# tạo sequence
length = 10;
sequence = [i/ float(length) for i in range(length)]
# tạo căp data input- output để training
df = DataFrame(sequence)
df = concat([df, df.shift(1)], axis=1)
df.dropna(inplace = True)
print(df)
 ```
 
 Kết quả:
 
 ```
      0    0
1  0.1  0.0
2  0.2  0.1
3  0.3  0.2
4  0.4  0.3
5  0.5  0.4
6  0.6  0.5
7  0.7  0.6
8  0.8  0.7
9  0.9  0.8
 ```
 
 Chúng ta sẽ sử dụng một lạo recurrent nẻual network gọi là LSTM để học chuỗi data trên. Vì thế, chúng ta cần transform input từ 2D array sang mảng 3D bao gồm [rows, timestamp, columns], timestamp = 1 bởi vf chỉ có 1 timestamp trên 1 observation.
 
 Tiếp theo chúng ta cần reshape lại data như sau.
 
 ```
from pandas import concat
from pandas import DataFrame
# tạo sequence
length = 10;
sequence = [i/ float(length) for i in range(length)]
# tạo căp data input- output để training
df = DataFrame(sequence)
df = concat([df, df.shift(1)], axis=1)
df.dropna(inplace = True)
# chuyển data về dạng có thể hiểu bới LSTM
values = df.values
X, y = values[:, 0], values[:, 1]
X = X.reshape(len(X), 1, 1)
print(X.shape, y.shape)
```

Kết quả kích thức vừa reshape().

```
(9, 1, 1) (9,)
```

### LSTM model và giá trị Batch Size

Trong phần này chũng ta sẽ thiết kế một mạng LSTM đơn giản cho bài toán vừa mô tả ở trên.

Training batch size sẽ là toàn bộ data set (batch learning) và và prediction sẽ được cấu hình trên từng thời điểm .



### Colab source

[Sourcecode](https://colab.research.google.com/drive/1BVGwC1xeFE0_97tURm9-5pAzRIBJwDOT?authuser=1#scrollTo=PYd8bvuISI2A)




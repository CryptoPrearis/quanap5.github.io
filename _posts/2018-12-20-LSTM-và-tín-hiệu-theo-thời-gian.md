---
layout: post
title: Làm quen với Deep Learning trong time series data
subtitle: tìm hiểu RNN, LSTM, ...
tags: [deep learning, Lstm, time series data]
comments: false
---

### Giới thiệu

Hôm nay tôi sẽ tìm hiểu về time series forecasting. Các bài toán liên quan đến dự đoán với chuỗi data theo thời gian thường được xử bằng các phương pháp tuyến tính bởi sự hiệu quả của nó trong trường hợp đơn giản. Nhưng đối với bài toán dự doán phức tạp hơn thì chúng ta cần những lỷ thuật mới.

Hi vọng những bài học của tôi sẽ giúp các bạn mới bắt đầu có cái nhìn tổng quan hơn trong topic này.

### Bắt đầu với MLP

Chung quy thì neural network giống như Multilayer Perceptron hay là MLP chính là khởi nguồn của Neural Network với các đặc tính như sau.

- Robust to noise: Các mạng neural network rất linh động với nhiễu của data đầu vào. Thậm chí nó còn có thể dự doán những giá trị bị khuyết, bị mất.
- Non-linear: Mạng neural network không cần đảm bảo có về mapping function (có thể là tuyến tính hoặc là không tuyến tính). Mạng có thể tự học và tìm dược đúng hoặc gàn đúng mapping function giữa input và output.
- Multivariate inputs. Số lượng feature (chiều feature) bất kỳ có thể được support cho việc dự đoán theo thời gian.
- Multi-step Forecast: Số lượng output bất kỳ cũng được support (chiều dài output)

Như vậy, MLP có thể được ứng dụng vào tín hiệu dạng time series data để dự đoán. Cái thử thách đầu tiên của time series data có thể đến từ việc chuẩn bị dữ liệu. Đặc biệt chúng ta cần chuyển chúng về dạng vector có thể hiểu được bởi các model machine learning. Trong bài đầu tiên tôi sẽ học về cách để phát triển một model MLP chuẩn cho ứng dụng time series forecasting.
- Cách để phát triển model MLP để dự đoán cho tín hiêu 1 chiều
- Cách để phát triển model MLP để dự đoán cho tín hiệu nhiều chiều
- Cách để phát triển model MLP để dự đoán trước nhiều step


### Tín hiệu 1 chiều


MLP có thể được sử dụng trong bài toán dự đoán tín hiệu 1 chiều theo thời gian. Tín hiệu này gồm một chuỗi đơn các observation theo thứ tự thời gian. Model MLP sẽ học và đoán gia trị tiếp theo của chuôi data.

Chúng ta tập trung vào:

- Chuẩn bị data
- MLP model

### Chuẩn bị data

Ví dụ về chuối tín hiệu 1 chiều như sau.

[10, 20, 30, 40, 50, 60, 70, 80, 90]

Chúng ta có thể chia chuỗi này thành các mẫu dạng multiple input/ output gọi là các mẫu (sample) với time steps =3 được sử dụng như là X và one-step cho đầu ra (predict).

|X|y|
|--|--|
|10,20,30,|40|
|20,30,40,|50|
|30,40,50,|60|
|...|..|

Và ta có hàm split_sequence() dưới đây được thược hiện để tách bóc ra các sample với định dạng trên dựa vào số step tại đầu vào và đầu ra coi như =1.

```python
def split_sequence(sequence, n_steps):
    X, y = list(), list()
    for i in range(len(sequence)):
        # find the end of this pattern
        end_ix = i + n_steps
        # check if we are beyond the sequence
        if end_ix > len(sequence) - 1:
            break
        # gather input and output  parts of the pattern
        seq_x, seq_y = sequence[i:end_ix], sequence[end_ix]
        X.append(seq_x)
        y.append(seq_y)

    return array(X), array(y)
```

Ta sẽ chạy demo với đoạn code đầy đủ sau.

```python
from numpy import array


# split a univariate sequence into samples
def split_sequence(sequence, n_steps):
    X, y = list(), list()
    for i in range(len(sequence)):
        # find the end of this pattern
        end_ix = i + n_steps
        # check if we are beyond the sequence
        if end_ix > len(sequence) - 1:
            break
        # gather input and output  parts of the pattern
        seq_x, seq_y = sequence[i:end_ix], sequence[end_ix]
        X.append(seq_x)
        y.append(seq_y)

    return array(X), array(y)


# define input sequence
raw_seq = [10, 20, 30, 40, 50, 60, 70, 80, 90]
# choose a number of time steps
n_steps = 3
# run the funcrion
X, y = split_sequence(raw_seq, n_steps)
# summarize the data

for i in range(len(X)):
    print(X[i], y[i])

```

Kết quả sẽ là:

![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/mlp_01.JPG)

### MLP model

Một MLP đơn giản là một hidden layer và nó được định ngĩa như sau.

```python

#define model
model = Sequential()
model.Add(Dense(100, activation='relu', input_dim=n_steps))
model.Add(Dense(1))
model.compile(optimizer='adam', loss='mse')
```

Điều quan trọng trong định nghĩa này chúng ta cần chú ý shape của đầu vào. Cái này chính là chiều của đầu vào bằng đúng time step mà chúng ta đã định nghĩa ở phan split data. Về mặt kỹ thuật model sẽ nhìn mỗi input đầu vào dưới dạng các feature thay cho các giá trị các cac time step khác nhau của chuỗi.

Chúng ta luôn có nhiều sample, vì vậy model mong muốn đàu vào của tập training phải có dạng hay la shape: [samples, features]. Quay lại phần trước ở khâu chuẩn bị data, ta có X thỏa mãn định dạng này và sẵn sàng cho việc học và modeling. Tiếp theo là model được fit với việc sử dụng adam vesion (thuật toán stochatic gradient descent rất hiệu quả) và với phương pháp tối uuw là 'mse' mean square eror đóng vai trò là hàm loss function.

Một khi model MLP đã được định nghĩa, chúng ta có thể fit nó vào tập training data.

```python
# fit model
model.fit(X, y, epochs=2000, verbose=0)
```

Sau khi model được fit(), chúng ta có thể dùng nó để dự đoán. Ví dụ, chúng ta dự đoán giá trị tiếp theo của chuối [70, 80, 90]. Chúng ta hi vọng model sẽ đoán cái gì đó gần bằng 100. Đầu ra model sẽ có dạng [samples, feature] vì vậy chúng ta phải reshape một mẫu đàu vào trước khi dự đoán. Ví dụ [1, 3] là 1 sample và 3 time steps sử dụng như input features.

![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/mlp_02.JPG)

Mot so ket qua khac:

![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/mlp_03.JPG)

![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/mlp_04.JPG)

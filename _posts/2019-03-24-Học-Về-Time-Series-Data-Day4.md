---
layout: post
title: CNN trong Time Series data
subtitle: Ngày 04 Xây dựng model CNN
tags: [time series data]
comments: false
---

### Giới thiệu

Bài hôm nay tôi tìm hiểu về CNN model trong ứng dụng với time series data để dự doán giá tri tương lai dựa vào các giá trị lich sử. 

Đối tương của bài hôm nay vẫn là dữ liệu time series 1 chiều. Chúng ta có thể định nghĩa bài toán là một chuỗi cac số integer, ta sẽ fit chuỗi này với một model CNN. Model được train trên tập data đã được chuẩn bị ở bài trước để có khả năng dự đoán giá trị tiếp theo của chuỗi.

Chúng ta sẽ coi đầu vào là 3 giá tri time step liên tiếp và cho ra 1 output. Ví dụ [10,20, 30] là input thi out sẽ là [40].

Một điểm khác với MLP model là đầu vào của CNN model có 3 input với shape [samples, timesteps, features] nên ta phải định nghĩa lại data dưới dạng [sample, timesteps] và reshape. 

Cụ thể là timestep bằng 3, trong khi feature bằng 1 thông qua input_shape ở lớp hiden layer đầu tiên.

Tiếp theo là lớp Convolutional layer theo sau bới một lớp poolingmax layer. Các giá trị output thu được sẽ flatten trước khi vào lớp dến để prediction.

Model sư dụng thuật tán Stochatic gradient descent với version adam (vì nó hiệu quả) và sử dụng tối uuw la mse cho loss function (hàm mất mát).

### Thực hiện trên code
Định ngĩa model CNN với các lớp như sau:

```python
# univariate cnn example
from numpy import array
from keras.models import Sequential
from keras.layers import Dense
from keras.layers import Flatten
from keras.layers.convolutional import Conv1D
from keras.layers.convolutional import MaxPooling1D
# define dataset
X = array([[10, 20, 30], [20, 30, 40], [30, 40, 50], [40, 50, 60]])
y = array([40, 50, 60, 70])
# reshape from [samples, timesteps] into [samples, timesteps, features]
X = X.reshape((X.shape[0], X.shape[1], 1))
# define model
model = Sequential()
model.add(Conv1D(filters=64, kernel_size=2, activation='relu', input_shape=(3, 1)))
model.add(MaxPooling1D(pool_size=2))
model.add(Flatten())
model.add(Dense(50, activation='relu'))
model.add(Dense(1))
model.compile(optimizer='adam', loss='mse')
# fit model
model.fit(X, y, epochs=1000, verbose=0)
# demonstrate prediction
x_input = array([50, 60, 70])
x_input = x_input.reshape((1, 3, 1))
yhat = model.predict(x_input, verbose=0)
print(yhat)
```

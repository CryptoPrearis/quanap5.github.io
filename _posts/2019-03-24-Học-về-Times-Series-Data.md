---
layout: post
title: Xây dụng modle MLP đơn giản
subtitle: Ngày 03 Xây dựng model MLP
tags: [time series data]
comments: false
---

### Giới thiệu

Bài hôm nay tôi sẽ học vể MLP trên tin hiệu time series data 1 chiều (Univariate time series data). 

### Thực hiện trên code
Định ngĩa model MLP với các lớp như sau:

```python
# define model
model = Sequential();
model.add(Dense(100, activation='relu', input_dim=n_input))
model.add(Dense(1))
model.compile(optimizer='adam', loss='mse')
# summarize model
model.summary()
# draw structure of model
plot_model(model, to_file='model_plot.png', show_shapes = True, show_layer_names=True)

```

![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/mlp02_03.png)

Kết quả chạy demo trên GoogleColab.

![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/mlp02_04.JPG)


### Kết luận

Vẫn đang học chưa kết luận gì :)

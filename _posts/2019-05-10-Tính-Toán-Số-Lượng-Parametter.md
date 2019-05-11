---
layout: post
title: Số lượng Params trong Neural Network
subtitle: tự học, maxpooling
tags: [Convnet]
comments: false
---

### Giới thiệu

Hôm nay chúng ta sẽ tìm hiểu về số lượng các parametter trong một model deep và convolutional neural network nói riêng. Trong keras thì chúng ta dùng hàm summary() để visualize kiến trúc cũng như các thông số liên quan như sau.

### Tạo model trên Keras

Keras là thư viện để dev model trên ngôn ngữ Python.

```python
model = Sequential()
#convolution-pooling layers
model.add(Convolution2D(32, 5, 5, input_shape=input_shape))
model.add(Activation('relu'))
model.add(MaxPooling2D(pool_size=(2, 2)))
model.add(Convolution2D(64, 5, 5))
model.add(Activation('relu'))
model.add(MaxPooling2D(pool_size=(2, 2)))
model.add(Dropout(0.25))
#dense layers
model.add(Flatten()) 
model.add(Dense(100))
model.add(Activation('relu'))
model.add(Dropout(0.5))
model.add((Dense(2)))
model.add(Activation('softmax'))
#optimizer
sgd = SGD(lr=1e-3, decay=1e-6, momentum=0.9, nesterov=True) 
model.compile(loss='categorical_crossentropy',
              optimizer = sgd,
              metrics=['accuracy'])
print model.summary()
```


Chúng ta sẽ có kết quả như sau:

![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/summary.PNG)


### Giải thích kết quả

 Cách tính Outputshape của mỗi layer sẽ phụ thuộc vào vào input shape và các tham số của layer hiện tại như: Kernel , stride, padding,...
 
 Cách tính số lượng param sẽ dựa vào công thức:
 
 Total_params = (filter_height* filter_width*input_image_channel +1)* number_of_filters
 
 Như vậy ở lớp đầu tiên:
 
 H=5;
 W=5;
 C= 1;
 F=32;
 => params = (5*5*1+1)*32 = 382
 
 Tương tự ở lớp conv thứ 2 ta có:
 
 H=5;
 W=5;
 C=32;
 F=64;
 => params = (5*5*32+1)*64 = 51264;
 
 

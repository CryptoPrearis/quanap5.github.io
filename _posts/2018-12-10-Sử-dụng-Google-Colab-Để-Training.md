---
layout: post
title: Sử dụng Google Colab
subtitle: cài đặt và sử dụng dịch vụ Google Colab để training
bigimg: /img/header.JPG
tags: [google, trainning, cloud]
comments: false
---

## Lời nói đầu

Vấn đề lớn nhất của chúng ta khi học machinelearning và deep learning là khả năng để mua phần cứng là không cho phép. Đặc biệt đối với sinh viên nghèo. Và hôm nay tôi sẽ giới thiệu một dich vụ Cloud computing của Google sẽ giúp bạn phần nào trong việc training các model với yêu cầu tính toán lớn. 

Google Colab là Colaboratory notebook kiểu như Jupyter notebook. Nếu bạn đã làm việc trên Jupyter rồi thì bài viết này cũng sẽ rất dễ dàng với bạn. Chúng ta chỉ cần chú ý phần insatll và setting thôi.

Tất nhiên, bạn vẫn lo âu về các thư viện có thể hỗ trợ trên Google Colab như thế nào. Về cơ bản thì mình biết là Google hỗ trợ hầu hết các thư viện trong Python, pytorch, cv2,.. Nên các bạn cũng yên tâm nhé.

Một số thắc mắc khác bạn có thể tìm hiểu ở [đây](https://research.google.com/colaboratory/faq.html).


### Bắt đầu để sử dung Google Colab
### Tạo folder trên Google Drive

Tại vì GoogleColab làm việc trên Google drive nên trước tiên chúng ta cần chỉ folder chúng ta sẽ làm việc trên đó. Ví dụ chúng ta tạo một folder kiểu AI_COLAB_LEARNING. Nhảy vào thư mục mới tạo ra.

![folder](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/createFolder.JPG)

Chuột phải chọn More (ứng dụng khác), tiếp chọn Kết nối ứng dung khác. Cửa sổ hiện lên tìm kiếm Colab trong ô tìm kiếm và cài đặt extension này nhé.

![extension](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/install%20Colab.JPG)

![extension](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/install2.JPG)

Sau khi hoàn thành cài đặt chúng ta sẽ tạo một notebook google colab bằng cách chọn: Chuột phải -> ứng dụng khác --> Colaboratory.
![note](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/notebokCreat.JPG)

Như vậy bạn đã có một notebook đẻ chạy code và có thể rename ở phần đâu bên trái tùy thích.

![note](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/notebook22.JPG)

### Cài đặt notebook với GPU

Mặc định notebook vừa tạo ra sẽ chạy với CPU. Bạn nên cài đặt lại trong mục: Edit -> Notebook setting hoặc Runtime -> Change runtime type và chọn GPU.

![settingGPU](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/setting.JPG)

### Chạy code python với Google Colab

Nào chúng ta bắt đầu chạy thử code trên này xem sao?

Dường như mọi thứ đã như mong muốn.

### Kết nối với Drive
Chúng ta cần chạy hai dòng code sau để cài đặt các thư viện cần thiết cũng như cấp quyền truy cập vào folder cho google colab.

```python
from google.colab import drive
drive.mount('/content/gdrive/')
```

Khi chạy thì bạn sẽ thấy một đường link xuất hiên. Click vào link đó với việc đăng google account và copy token hiện lên dán vào ô trống trên notebook rồi Enter. Như vậy bạn đã cấp quyền truy cập xong.

![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/authoritory.JPG)

Nhìn sang của sổ bên trái click vào File và Refresh lại. Bạn sẽ thấy thư mục trong driver của bạn đã dc kéo vào /content/gdrive/ (working directory)

![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/authority2.JPG)

### Thử training với MNIST

Sau khi cấp quyền truy cập ở trên thì Google có thể truy cập vào folder driver của bạn để lấy file script hoặc data.

Cài đặt thư viện Keras như sau.
```python
!pip install -q keras
```

Upload file mnist_cnn.py lên folder chúng ta tạo ở phần đầu và thử chạy.

Nội dung code.
```python
'''Trains a simple convnet on the MNIST dataset.

Gets to 99.25% test accuracy after 12 epochs
(there is still a lot of margin for parameter tuning).
16 seconds per epoch on a GRID K520 GPU.
'''

from __future__ import print_function
import keras
from keras.datasets import mnist
from keras.models import Sequential
from keras.layers import Dense, Dropout, Flatten
from keras.layers import Conv2D, MaxPooling2D
from keras import backend as K

batch_size = 128
num_classes = 10
epochs = 12

# input image dimensions
img_rows, img_cols = 28, 28

# the data, split between train and test sets
(x_train, y_train), (x_test, y_test) = mnist.load_data()

if K.image_data_format() == 'channels_first':
    x_train = x_train.reshape(x_train.shape[0], 1, img_rows, img_cols)
    x_test = x_test.reshape(x_test.shape[0], 1, img_rows, img_cols)
    input_shape = (1, img_rows, img_cols)
else:
    x_train = x_train.reshape(x_train.shape[0], img_rows, img_cols, 1)
    x_test = x_test.reshape(x_test.shape[0], img_rows, img_cols, 1)
    input_shape = (img_rows, img_cols, 1)

x_train = x_train.astype('float32')
x_test = x_test.astype('float32')
x_train /= 255
x_test /= 255
print('x_train shape:', x_train.shape)
print(x_train.shape[0], 'train samples')
print(x_test.shape[0], 'test samples')

# convert class vectors to binary class matrices
y_train = keras.utils.to_categorical(y_train, num_classes)
y_test = keras.utils.to_categorical(y_test, num_classes)

model = Sequential()
model.add(Conv2D(32, kernel_size=(3, 3),
                 activation='relu',
                 input_shape=input_shape))
model.add(Conv2D(64, (3, 3), activation='relu'))
model.add(MaxPooling2D(pool_size=(2, 2)))
model.add(Dropout(0.25))
model.add(Flatten())
model.add(Dense(128, activation='relu'))
model.add(Dropout(0.5))
model.add(Dense(num_classes, activation='softmax'))

model.compile(loss=keras.losses.categorical_crossentropy,
              optimizer=keras.optimizers.Adadelta(),
              metrics=['accuracy'])

model.fit(x_train, y_train,
          batch_size=batch_size,
          epochs=epochs,
          verbose=1,
          validation_data=(x_test, y_test))
score = model.evaluate(x_test, y_test, verbose=0)
print('Test loss:', score[0])
print('Test accuracy:', score[1])
```

Chúng ta thấy kết quả trên notebook như sau.
Đang training.
![training](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/training1.JPG)

Kết thúc.
![end](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/end.JPG)

### Kết luận

Như vậy là chúng ta có thể chạy code như bình thường nhé. Training model rất là tiện lợi và các thư viện cũng support. Rất hay phải và sẽ là một sự lựa chọn không tồi nếu bạn không muốn training trên máy tính cá nhân quá chậm chạp nhé. Thanks.

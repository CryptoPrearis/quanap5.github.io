---
layout: post
title: MLP với tín hiệu nhiều chiều
subtitle: MLP dơn giản dự đoán tín hiệu nhiều chiều  trên time
tags: [MLP, Lstm, time series data]
comments: false
---

### Tín hiệu nhiều chiều


Tín hiệu nhiều chiều có nghĩa là data có hơn một observation tại mỗi time step. Có hai models chúng ta có thể cần để xử lý
loại tín hiệu này bao gồm:
1. Multiple Input Series
2. Multiple Parallel Series

Chúng ta sẽ cùng xem xét chúng lần lượt

### Chuẩn bị data

Một vấn đề cũng có thể có hai hay nhiều time series data song song và một output phụ thuộc vào tín hiệu đầu vào. Tín hiệu đàu vào gọi là song song vì mỗi chuỗi đầu vào có một obsservation riêng tại mội time step. Chúng ta sẽ demo một ví dụ đơn giản với 2 chuỗi đầu vào song song và 1 output như sau.

```python
# define input sequence
in_seq1 = array([10, 20, 30, 40, 50, 60, 70, 80, 90])
in_seq2 = array([15, 25, 35, 45, 55, 65, 75, 85, 95])

out_seq = array([in_seq1[i] + in_seq2[i] for i in range(len(in_seq1))])
```

Chúng ta nên reshape lại các mảng này như sau.

Giống với chuỗi 1 chiều, đây chúng ta cũng phải xây dựng data thành mẫu input/output sample. Chúng ta cần chia data phải giữ nguyên thứ tự các observation của hai chuỗi. Nếu giả xử ta chọn của sổ với 3 time steps ta có input như sau.

Input:

[]

Output:

[]

Giống với chuỗi 1 chiều, đây chúng ta cũng phải xây dựng data thành mẫu input/output sample. 
Chúng ta cần chia data phải giữ nguyên thứ tự các observation của hai chuỗi. 
Nếu giả xử ta chọn của sổ với 3 time steps ta có input như sau.


```python
# split a multivariate sequence into samples
def split_sequences(sequences, n_steps):
    X, y = list(), list()
    for i in range (len(sequences)):
        # find the end of this pattern
        end_ix = i + n_steps
        # check if we are beyond the dataset
        if end_ix > len(sequences):
            break

        # gather input and output parts of the pattern
        seq_x, seq_y = sequences[i:end_ix, :-1], sequences[end_ix-1, -1]
        X.append(seq_x)
        y.append(seq_y)
    return array(X), array(y)
```

![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/mlp02_01.JPG)

Chúng ta có thể test các hàm split và data được covert dể phù hợp với input/output bằng full code như sau.

```python
from numpy import  array
from numpy import hstack
# from keras.models import  Sequential
# from keras.layers import  Dense

# split a multivariate sequence into samples
def split_sequences(sequences, n_steps):
    X, y = list(), list()
    for i in range (len(sequences)):
        # find the end of this pattern
        end_ix = i + n_steps
        # check if we are beyond the dataset
        if end_ix > len(sequences):
            break

        # gather input and output parts of the pattern
        seq_x, seq_y = sequences[i:end_ix, :-1], sequences[end_ix-1, -1]
        X.append(seq_x)
        y.append(seq_y)
    return array(X), array(y)

# define input sequence
in_seq1 = array([10, 20, 30, 40, 50, 60, 70, 80, 90])
in_seq2 = array([15, 25, 35, 45, 55, 65, 75, 85, 95])
out_seq = array([in_seq1[i] + in_seq2[i] for i in range(len(in_seq1))])

# convert to [rows, columns] structure
in_seq1 = in_seq1.reshape((len(in_seq1), 1))
in_seq2 = in_seq2.reshape((len(in_seq2), 1))
out_seq = out_seq.reshape((len(out_seq), 1))
# horizontally stack collumn
dataset = hstack((in_seq1, in_seq2, out_seq))
print(dataset)
# choose a number of time steps
n_steps = 3
# convert into input/output
X, y = split_sequences(dataset, n_steps)
# summarize the data
for i in range(len(X)):
    print(X[i], y[i])
```

Kết quả:

![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/mlp02_02.JPG)

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


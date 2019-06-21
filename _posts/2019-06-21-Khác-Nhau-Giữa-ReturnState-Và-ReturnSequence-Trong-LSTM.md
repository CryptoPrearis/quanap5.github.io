---
layout: post
title: Khác nhau giữa Return Sequences và Return State trong LSTM
subtitle: Backend Deep
tags: [neural network, LSTM]
comments: false
---

### Giới thiệu


Thư viện Keras là thư viện API để dành cho phát triển các mô hình deep learning. Keras cũng cung cấp các layer cho LSTM hay RNN, dạng neural network thích hợp cho  time series data hoặc sequence.

Với việc sử dụng thư viên thông qua các API đã đóng gói, đôi lúc vì nhiều argument nên nhầm lẫn hoặc hiểu sai ý nghĩa của từng tham số truyền vào nên hom nay tôi sẽ học về ý nghĩa của 2 tham số: Return sequence và Return state.

Chúng ta sẽ cùng nhau cố gắng hiểu được các vấn đề sau trong bài hôm nay:

- Return sequences 
- Return sequences sẽ trả về ouput của hidden state và cell state của input time step cuối cùng
- Cả hai argument này có thể được sử dụng cùng nhau.


### Long short term memory

LSTM là một loại recuurent neural network tạo bởi LSTM cell và trong cell cấu tạo bới các Gate để hạn chế và tránh hiện tượng vanish gradient ở loai RNN sơ khai.

Trong thư viện Keras thì LSTM layer cũng có thể  tạo bởi lớp LSTM class như [document](https://keras.io/layers/recurrent/#lstm)

### Return Sequence

Mỗi một LSTMCell sẽ trả ra output gọi là hidden state và thường được kí hiệu là h cho mỗi input time step đầu vào.

```python
from keras.layers import LSTM
h = LSTM(x)
```

Chúng ta có thể demo với một model nhỏ sử dụng thư viện Keras với một layer LSTM và đồng thời chỉ một LSTMCell bên trong nó.

Trong ví dụ sau, chúng ta sẽ có một sample với 3 time step và mỗi time step chưa 1 feature.

```python
t1= 0.1
t2=0.2
t3=0.3
```

Đoạn code được hoàn thành như sau:

```python
from keras.models import Model
from keras.layers import Input
from keras.layers import LSTM
from numpy import array

# define model
inputs1 = Input(shape=(3,1))
lstm1 = LSTM(1)(inputs1)
model = Model(inputs = inputs1, outputs =lstm1)
# define input data
data = array([0.1, 0.2, 0.3]).reshape((1,3,1))
# make and show prediction
last_hidden_state = model.predict(data)
print(last_hidden_state)
```

```python
[[0.08076988]]
```

Khi chạy ví dụ trên, kết quả trả về là single hidden state tương ứng với đầu vào 3 time step. Làm sao để tuy cập vào hidden state tại mỗi time step. Chúng ta có thể làm được điều này bằng cách setting thuộc tính return_sequence thành True khi định nghĩa LSTM layer.

```python
LSTM(1, return_sequences= True)
```

Bây giờ chúng ta sẽ update đoạn code trên với sự thay đổi này. Full code như sau:

```python
from keras.models import Model
from keras.layers import Input
from keras.layers import LSTM
from numpy import array
# define model
inputs1 = Input(shape=(3,1))
lstm1 = LSTM(1, return_sequences= True)(inputs1)
model = Model(inputs = inputs1, outputs= lstm1)
# define input data
data = array([0.1, 0.2, 0.3]).reshape((1,3,1))
# make and show prediction
full_hidden_state = model.predict(data)
print(full_hidden_state)
```

```python
[[[0.02098767]
  [0.05613788]
  [0.1008185 ]]]
```

Chạy code ta thu được kết như trên là chuỗi 3 giá trị tương ứng với hidden state tại mỗi time step của LSTM cell trong LSTM layer. Lưu ý trong khi phát triển các model stacking LSTM layer, chúng ta phải set return_sequence = true để layer thứ hai có thể tiếp nhận đàu vào 3 chiều.

### Return State

Output của một layer LSTM hoặc một LSTM Cell đều được gọi là hidden state Cái này hơi bị dễ nhầm lẫn, bởi vì LSTMCell duy trì mộ trạng thái khác gọi là cell state và kí hiệu viết tắt là c.

Thường thì chúng ta không truy cập vào cell satte nếu như một lúc nào đso chúng ta cần nó để xây dựng một model phức tạp hơn. Khi mà các layer tiếp theo có thể cần các trạng thái của cell cái được thiết lập cộng với final cell satte của layer khác. Ví dụ như encode-decoder model.

Keras cung cấp return_state để có thể giúp chúng ta truy cập vào hidden state and cell state (state_h) và (state_c).

```python
lstm1, state_h, state_c = LSTM(1, return_state = True)
```

Rõ ràng lstm1 và state_h đều tham chiếu đến hidden state. Có thể gây hiểu nhàm ở đay. Đây là lí do chúng ta cùng tìm hiểu đoạn code sau.

```python
from keras.models import Model
from keras.layers import Input
from keras.layers import LSTM
from numpy import array
# define model
inputs1 = Input(shape=(3,1))
lstm1, state_h, state_c = LSTM(1, return_state= True)(inputs1)
model = Model(inputs = inputs1, outputs = [lstm1, state_h, state_c])
# define input data
data = array([0.1, 0.2, 0.3]).reshape((1,3,1))
# make and show prediction
full_hidenAndcell_state = model.predict(data)
print(full_hidenAndcell_state)
```

```python
[array([[0.11308797]], dtype=float32), array([[0.11308797]], dtype=float32), array([[0.21255058]], dtype=float32)]
```

Kết quả trả về lần lượt là.

LSTM hidden state output của last time step
LSTM hidden state output của last time step
LSTM cell state cho last time step
Hidden state và cell state có thể được sử dụng để thiết lập ban đầu cho các LSTM layer khác với cùng số lượng cells.

Return state và sequence

```python
from keras.models import Model
from keras.layers import Input
from keras.layers import LSTM
from numpy import array
# define model
inputs1 = Input(shape=(3,1))
lstm1, state_h, state_c = LSTM(1, return_sequences=True, return_state = True)(inputs1)
model = Model(inputs = inputs1, outputs=[lstm1, state_h, state_c])
# define input data
data = array([0.1, 0.2, 0.3]).reshape((1, 3, 1))
# make and show prediction
both_state_sequence = model.predict(data)
print(both_state_sequence)
```

Chú ý vào kết quả, bây giờ thì chúng ta đã hiểu việc phân chia hidden state, hidden state output và cell state

### Kết luận

Trong bài học này chúng ta có thể hiểu được là:

Return sequence sẽ trả vef hidden state tại mỗi time step
Return state sẽ trả về hiden state và cell state tại time step cuối cùng
Chúng ta có thể sử dụng đồng yhowif hai argument này với LSTM layer


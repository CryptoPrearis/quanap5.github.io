convolution layer xử lý một đầu vào với nhiều kênh (channel).

# Các kiểu phân lọa Convolution

Chúng ta có thể phan loại thành convolution 1D, Concolution 2D hay Concolution 3D

# Dilated convolution

Tôi đặc biệt thích thú với kiểu dilated convolution. Nếu bạn chưa hình dung được một tham số khác có tên là dilated rate thi hãy cùng tìm hiểu nhé.

Hình sau đay minh họa cho lạo dilated convolution này.

![](https://cdn-images-1.medium.com/max/750/1*SVkgHoFoiMZkjy54zM_SUw.gif)

Để hiểu dilation rate chúng ta có thể hình dung dựa vào hình minh họa như trên. Ví dụ một concolution với kernel 3x3 và dilation rate = 2 thì nó tương tự như phép convolution với của sổ 5x5 nhưng chúng ta chỉ quan tâm 9 phần tử in đậm.

Loại convolution này sẽ làm rộng (field of view) nhưng vẫn giữ nguyên khối lượng tính toán. Dilated convolutions được sử dụng nhiều trong các ứng dụng real-time segmentation.

# Transposed convolution

# Separable Convolution

Lọa này có nghĩa là thay vì chúng ta convolution tren một kernel thi chũng ta sẽ convolution tren 2 kernel khac nhau. Ví dụ 2D convolution bang cách thực hiên 2 convolution 1D.

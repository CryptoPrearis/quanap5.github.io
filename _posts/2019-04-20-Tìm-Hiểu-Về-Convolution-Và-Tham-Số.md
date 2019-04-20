---
layout: post
title: Giới thiệu về các loại Convolution dùng trong DeepLearning
subtitle: lập trình, học tập
tags: [Deep learning, convolution, CNN]
comments: false
---

### Giới thiệu

Hôm nay tôi sẽ tìm hiểu về các lọa convolution và cac đặc điểm cũng như tính chất của nó thông qua các hình ảnh minh họa tôi biết qua các tài liệu trên mạng. Để đơn giản và dễ hiểu chúng ta sẽ chỉ tâp trung vào 2D convolution.

![minhhoa](https://cdn-images-1.medium.com/max/750/1*1okwhewf5KCtIPaFib4XaA.gif)

### Convolution

Trước hết chúng ta cần hiểu qa một số tham số liên quan đến phep toán convolution của hình minh trên. Những thông số ấy bao gồm.
- Kernel Size: Tham số này cho ta biết trường quan sát của convolution (field ò view). Ví dụ như, 3x3, 5x5,...
- Stride: Tham số này định nghĩa bước nhảy của của sổ convolution trong quá trình chạy trên dữ liệu đầu vào.Bình thường chúng ta thường bắt gặp stride = 1. Nhưng để tính toán nhanh hơn về sau, kiêu như phép downsampling chúng ta có thể thiết lập stride >= 2.
- Padding: đây là tham số để dịnh nghĩa chúng ta có đường bao để đảm bảo kich thước output sau khi convolution sẽ bằng với input hay không?

Same padding có nghĩa là size của output sẽ bằng kích thước đầu vào (giả sử là stride = 1). Ví du, nếu đầu vào có n channel với kích thước feature-map  la 28x28, thì kết quả đầu ra khi ta sử dụng padding = "same" cũng sẽ có kích thước 28x28.

Bây giờ làm sao để làm được điều trên tại vì các tham số khác như kernel cũng ảnh hưởng đến kich thước output. Nếu kernel (filter) có kích thước kxk thì padding size nên được chọn là p = (k-1)/2.

Để hiểu hơn về công thức chúng ta cùng xem hình minh họa sau.

![](https://qph.fs.quoracdn.net/main-qimg-9e3419cfcd8535fb289bb1b710920d2f)

![](https://qph.fs.quoracdn.net/main-qimg-bdfb9cffbabddca398207120de1618d1.webp)

Cùng xết input có kích thước 10x10 và sẽ được convolution với kernel kich thước 3x3. Để làm cho output có cùng kích thước với input, thì tam của cửa sổ convolution phải suất phát từ local path trên cùng bên trái. Và việc này yêu cầu chúng ta phải padding thêm các giá trị không có cho input với giá trị bằng 0 gọi là zero padding. Với kernel = 3 thi chúng ta phải pad p = (3-1)/2 = 1 (như hình vẽ). Trong khi hình tiếp theo kernel = 5 thi p = (5-1)/2 = 2 ( như hình vẽ tiếp).

Một cách để xác định nữa là ta giả thiết ta có công thức p = (k-1)/2 rồi. Theo cách xác định của [tài liệu](https://arxiv.org/pdf/1603.07285.pdf), ta có output size = (i-k)+2p +1. Cùng check nhé. Với hình đầu tiên, k= 3, p =1 suy ra output size = 10-3 +2x1 +1 = 10 (giống kích thước đầu vào nhé, tin chưa)

- Channel: Một tham số nữa rất hay bị quên và khó hình dung là channels. Việc convolution layer xử lý một đầu vào với nhiều kênh (channel).

# Các kiểu phân loại Convolution

Chúng ta có thể phan loại thành convolution 1D, Concolution 2D hay Concolution 3D

# Dilated convolution

Tôi đặc biệt thích thú với kiểu dilated convolution. Nếu bạn chưa hình dung được một tham số khác có tên là dilated rate thi hãy cùng tìm hiểu nhé.

Hình sau đay minh họa cho lạo dilated convolution này.

![](https://cdn-images-1.medium.com/max/750/1*SVkgHoFoiMZkjy54zM_SUw.gif)

Để hiểu dilation rate chúng ta có thể hình dung dựa vào hình minh họa như trên. Ví dụ một concolution với kernel 3x3 và dilation rate = 2 thì nó tương tự như phép convolution với của sổ 5x5 nhưng chúng ta chỉ quan tâm 9 phần tử in đậm.

Loại convolution này sẽ làm rộng (field of view) nhưng vẫn giữ nguyên khối lượng tính toán. Dilated convolutions được sử dụng nhiều trong các ứng dụng real-time segmentation.

# Transposed convolution

![](https://raw.githubusercontent.com/vdumoulin/conv_arithmetic/master/gif/padding_strides_transposed.gif)

Loại này có thể gọi là deconvolution nhưng thực tế phép này hoàn toàn khác với deconvolution. Xin mời tìm hiểu ở paper trên.

[link](https://arxiv.org/abs/1603.07285)

# Separable Convolution

Lọa này có nghĩa là thay vì chúng ta convolution tren một kernel thi chũng ta sẽ convolution tren 2 kernel khac nhau. Ví dụ 2D convolution bang cách thực hiên 2 convolution 1D.

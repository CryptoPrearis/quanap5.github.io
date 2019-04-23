---
layout: post
title: Transpose với ArayFire
subtitle: tự học, transpose
tags: [Data manipulation]
comments: false
---


### Transpose với arayFire

Transpose là một phép chuyển hóa data (data manipulation)
 rất hay dùng cùng với phép reshape để tạo data theo format phù hợp với input của mạng neural network.
 
 Hôm nay tôi sẽ tìm hiểu về các đọc data từ aray của thư viên [ArrayFire](https://arrayfire.com/).
 
 
 ### Các bước thực hiện
 
 Bước 1: Tạo data test
 
 ```C#
  float[] trans = new float[]
            {
                0.1f, 0.2f, 0.3f, 0.4f, 0.5f, 0.6f,
                2.1f, 2.2f, 2.3f, 2.4f, 2.5f, 2.6f,
                1.1f, 1.2f, 1.3f, 1.4f, 1.5f, 1.6f,
                3.1f, 3.2f, 3.3f, 3.4f, 3.5f, 3.6f
            };
 ```
 
 ![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/trans.PNG)
 
 Bước 2: Transpose
 
 ```C#
 Tensor Trans = BackendX.FromArray(trans);
            Trans = Trans.Reshape(new long[] { 2, 3, 2, 2 });
            var Trans2 = Trans.Transpose(1, 0, 2, 3);  
            var Trans3 = Trans2.Transpose(1, 0, 2, 3);
            var Trans4 = Trans.Transpose(0, 1, 3, 2);  
 ```
 
 ![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/trans02.PNG)
 
 Bước 3: Debug để thấy kết quả
 
 Kết quả của  Trans2:
 
Dữ liêu:

![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/trans03.PNG)

Shape:

![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/debugZ02.PNG)
 
  Kết quả của  Trans3:

Dữ liêu:

![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/trans03.PNG)

Shape:

![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/debugZ03.PNG) 
  
   Kết quả của  Trans4:

Dữ liêu:

![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/trans04.PNG)

Shape:

![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/debugZ04.PNG) 
  

Mô tả ở đây:

![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/IMG_6538.jpg)


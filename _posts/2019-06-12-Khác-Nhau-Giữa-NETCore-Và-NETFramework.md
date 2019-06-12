---
layout: post
title: Khác nhau giữa .NetFramework và .Net Core
subtitle: Backend Deep
tags: [neural network, RNN, tensorflow]
comments: false
---

### Giới thiệu

Hôm nay tỗi sẽ tìm hiểu về sự khác nhau giữa .NET cỏe và .NET Framework. Khi nào chúng ta nên chọn cái này hay cái kia. .Net Core  là nền tảng cross-platform để xây dựng các ứng dụng bao gồm Windows, Mac và Linux. Vậy nên dựa vào tiêu chí gì để chọn trong quá trình phát triển và xây dựng ứng dụng.

### Ưu điểm của .NET Framework

Nếu bạn là là một .Net dev người cần xây dựng và phát hành ứng dụng một cách nhanh chóng trong khi bản thân không có nhiều thời gian để học .NET Core cho nên .Net Framework là sự lựa chọn tốt. .Net Core thực sự cần nhiều thứ cần phải học hơn.

Nếu bạn muốn bảo trì và nâng cáp một ứng dụng .Net app, thì .NET Framewwork cũng là lựa chộn tốt nhất. Bới vì để chuyển một ứng dụng .NET sang .NetCore cần nhiều công việc hơn.

Để tìm một công việc tốt, tốt nhất bạn nên tập trụng vào vào .Net Framework.

### Ưu điểm của .Net Core

Nếu bạn đang chuẩn bị xây dựng một ứng dụng và phân vân giữa .NET Core và .NET Framework.

Hiện tại .NET Core 3.0 đang được improved nếu bạn muốn học những cái mới và có thời gian để học thì lựa chọn .Net core cũng sẽ không tồi. Hơn nữa nó hỗ trợ lâp trình đa nền tảng giữa UWP, WPF và window form. Giúp cho người lập trình rất linh động.

Khi bạn ưu tiên high performance và scalable system thì .NET Core hỗ trợ rất tốt. Bạn có thể dễ dàng chạy nhiều version và ứng dụng trên cùng một máy.

### Kiến trúc chung

Sau đay là sơ đồ và kiến trúc của hệ sinh thái .NET.

![](https://csharpcorner-mindcrackerinc.netdna-ssl.com/article/difference-between-net-framework-and-net-core/Images/DotNet-Architecture.jpg)


Như bạn có thể thấy từ hình trên. Có 3 thành phần chính trong hệ sinh thái .NET bao gồm:

- .NET FRamework
- .NET Core
- Xamarin

Xamarin thì không cần cạnh tranh với 2 cái còn lại. Khi bạn muốn xây dựng ứng dụng cho iOS, android hoạc window phone sử dụng C# Xamarin là lựa chọn duy nhất.

.Net Framework hỗ trợ lập trình window và ứng dụng web. Ngày nay, chúng ta có thể sử dụng Window form, Wpf và Uwp để xxay dựng ứng dụng window trên .Net framework. ASP.NET MVC được sử dụng để xây dựng ứng dụng web.

.NET Core là mã nguồn mở  và đa nền tảng để xây dựng các ứng dụng cho cả window, linux, và Mac.


### Tiêu chí để chọn một trong hai

.NET Framework là lựa chọn tốt nếu:

- Bạn không có thời gian để học các kỹ thuật mới
- Cần môi trường ổn định để phát triển
- Có kê hoach release trong thời gian sớm
- Muốn mở rộng một ứng dụng đã có sẵn
- Có team có kinh nghiệm xây dưng ứng dụng cho .NET
- Không muốn liên tục cập nhật
- Xây dựng Window client sử dụng winform hoaawcj Wpf

.NET Core phù hợp hơn nếu :
- Mục tiêu chạy trên cả Window, Linux, và Mac hệ điều hành
- Không ngại học hỏi
- Yêu thích Open source


Bạn có thể tham khảo thêm bảng này để lựa chọn nền tảng phù hợp với project sẽ phát triển nhé

![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/bang.PNG)





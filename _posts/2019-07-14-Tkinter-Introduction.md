---
layout: post
title: Tkinter cho người mới bắt đầu
subtitle: Backend Deep
tags: [ConvLSTM]
comments: false
---



### Nội dung chính

- GUI là gì
- Những thư viện GUI trong Python
- Thư viện tích hợp sẵn TKinter
- Cơ bản các thánh phân của module Tkinter
- Quản lý Geometry
- Tổ chức Layout và Widgets
- Binding function
- Image và Icon


### GUI (Graphic User Interface)

GUI chỉ là phần mềm với giao diện đồ họa trên Desktop để giúp người dùng tương tác với chương một cách thân thiện và dễ dàng hơn thôi.

- GUI như Text-editor
- GUI như trình duyệt web
- GUI như phần mềm chạy nhạc

Có rất nhiều loại GUI apps khác nhau mà chúng ta đã rất quen thuộc. Và hôm nay chúng ta sẽ học cách làm một GUI app đơn giản sử dụng ngôn ngữ lập trình Python.

Thực tế có rất nhiều người thich GUI hơn là command line. Tại việc tương tác với đồ họa thông qua các nut bấm, các text các hình ảnh sẽ thích thú hơn nhiều.

### Thư viện GUI trong python

Sau đây là 4 thư viện chính được sửd ụng nhiều nhất

-Kivy
-Python Qt
-WxPython
-Tkiinter

Trong số này thì Tkinter sẽ được lựa chọn cho bài học hôm nay vì nó sẽ dùng và được sủ dụng nhiều bởi nhiều nhà lập trình vì sự đơn gian của nó. Đồng thời module này cũng có sẵn trong python, chúng ta không cần cài đặt gì thêm.

### Tkinter

Tkinter đã được tích hợp trong Python.

Chúng ta không cần phải bận tâm về việc cài đặt thư viên này nhé.


### Fundamentals của Tkinter

![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/FudamentalTkinter.PNG)

Để bắt đầu viết một chưng trihf GUI với thư viện Tkinter. Chúng ta phải import modeule đó. Tiếp theo sau đó là tạo một cửa sổ chính (mainwwindow). Trên cửa sổ này chúng ta sẽ có các hiện thự cũng như các operation. Cuối cùng chúng ta sẽ chèn thêm các Widgets để làm sinh động GUI phục vụ các tương tác với người dung. Và để chương trình chạy được chúng ta cần có một main event loop.

```python
// import necceesary module
from tkinter import *
// create a main window
root = Tk()
// add widgets


// enter a event loop
root.mainloop()
```

### Tkinter widget

Chúng ta sẽ hỏi widgets là gì vậy?

Cùng hình vào hình dưới này.

![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/WidgetsTkinter.PNG)

-Canvas: Sử dụng để vẽ hình, đò họa, màu sắc lên GUI
-Button: Sử dụng để tạo Button click để người dungj tương tác với các bindding function
-Checkbutton: Sử dụng để tạo check button
-Entry: tạo input field cho GUI
-Frame: Tạo container trong Tkinter
-Label: Sử dụng để tạo các line tẽt, images
-Menu: Sử dụng để tạo Menu trên GUI (File, edit, about,...)

Đây là những widgets để giúp người lập trình có thể thoải mái tạo ra những GUI app theo ý muốn. Đây cũng là lí do dể Tkinter trở nên pgoor biến. 

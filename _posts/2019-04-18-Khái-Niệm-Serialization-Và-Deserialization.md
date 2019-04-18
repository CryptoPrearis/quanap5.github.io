---
layout: post
title: Tìm hiểu về Serialize và Derserialize
subtitle: lập trình
tags: [C#, Serialize, Deserialize]
comments: false
---

### Giới thiệu

Khái niệm về Serialization và Derserialization được sử dụng khi dữ liệu của đối tượng nào đó cần phải chuyển từ ứng dụng này sang ứng dụng khác. Serialization được sử dụng để xuất dự liệu nói trên ra file. Và ứng dụng đích sẽ phải sử dụng kỹ thuật derserialization để trich xuất dự liệu từ file ra để xử lý.

Để hiểu hơn chúng ta cùng nhìn vào hình mình qua của hai quá trình Serialization và Derserialization như sau. Là hai quá trình đối lập nhau như việc từ trứng nở ra con gà hay ngược lại con gà đẻ ra quả trứng.

Cũng có thể nói, quá trình Serialization là quá trình chuyển các dữ liệu trên bộ nhớ (in-memory instance) của các đối tượng qua network hoặc lưu trữ ở đâu đó trên ổ cứng (file hệ thống) hay database. Còn quá trình Derserialization thì chúng ta có thề đoán là chuyển ngược từ file thành in-memory instance của các đối tượng gốc.

![minhhoa](https://skrift.io/media/1205/chicken-egg.png)


### Vì sao Derserialization thường gặp khó khăn?

Serialization một đối tượng hay một class nào đó có thể thực hiên đẽ dàng, thâm chí là class implement một abstact hay interface. Trong khi đó, deserialization file json thành một trong những đối tượng trên thỉnh thoảng gặp đôi khó khăn và đòi hỏi chúng ta cần có những kỹ thuật riêng. Để tìm hiểu nhiều hơn, chúng ta có thể tham khảo thư viện Json.Net [newtonsoftJson](https://www.newtonsoft.com/json).


### Thực hiện một interface deserialization đơn giản

```c#
public class Person
{
    public IProfession Profession { get; set; }
}

public interface IProfession
{
    string JobTitle { get; }
}

public class Programming : IProfession
{
    public string JobTitle => "Software Developer";
    public string FavoriteLanguage { get; set; }
}

public class Writing : IProfession
{
    public string JobTitle => "Copywriter";
    public string FavoriteWord { get; set; }
}

public class Samples
{
    public static Person GetProgrammer()
    {
        return new Person()
        {
            Profession = new Programming()
            {
                FavoriteLanguage = "C#"
            }
        };
    }
}
```

```C#
class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello World!");
            Person q = new Person()
            {
                Profession = new Programming()
                {
                    FavoriteLanguage = "C#"
                }
            };
            //q.manager = new Person()
            //{
            //    Profession = new Writing()
            //    {
            //        FavouriteWord = "HiHiHi"
            //    }
            //};
            //save
            q.SaveModel("q.json");
            //load
            Person model2 = Person.LoadModel("q.json");
        }
    }
```

### Kết luận

![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/SerDer.PNG)

Còn nhiều thứ hay ho nữa, tôi vẫn đang tìm hiểu.

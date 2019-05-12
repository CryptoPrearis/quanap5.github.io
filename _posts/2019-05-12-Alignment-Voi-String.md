---
layout: post
title: ALign String trong C#
subtitle: Summary
tags: [neural network]
comments: false
---

### Giới thiệu

Hôm nay sẽ tìm hiểu về align string trong C# sử dụng String.Format.

Để căn lề bên trái bên phải chúng ta sẽ sử dụng String.Format với tham số như sau.

```C#
foreach (var layer in Layers)
            {
                var nParams = 0;
                var shapeI = "(";
                var shapeO = "(";
                foreach (var p in layer.Params)
                {
                    var param = p.Value;
                    nParams = nParams + (int)param.Data.ElementCount;
                }
                TotalParams = TotalParams + nParams;
                foreach (var item in layer.InputShape)
                {
                    shapeI = shapeI + item.ToString()+",";
                }
                foreach (var item in layer.OutputShape)
                {
                    shapeO = shapeO + item.ToString() + ",";
                }
                shapeI = shapeI + ")";
                shapeO = shapeO + ")";
                Console.WriteLine(String.Format("{0,-20} | {1,-20} | {2,20} | {3,20} |", layer.Name!=null?layer.Name:"unknown", shapeI, shapeO, nParams));
                Console.WriteLine("------------------------------------------------------------------------------------------+");
            }
            Console.WriteLine("Total params: " + TotalParams);
```

Ta có kết quả trên console như sau.

![](https://raw.githubusercontent.com/quanap5/quanap5.github.io/master/img/outputR.PNG)

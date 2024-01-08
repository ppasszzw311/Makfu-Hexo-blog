---
title: .NET要學習的物件導向概念名詞
date: 2024-01-06 07:51:02
tags: [NET]
---

## 物件導向的三大特性
### 封裝(Encapsulation)
餐廳裡的食物都在廚房已經都做好了，我們只要點餐然後拿來吃就好

封裝可以防止程式碼被篡改，提高程式碼的**穩定性**與**安全性**，但要真正實現這件事情，就需要對於方法設定權限，

#### 存/取控制修飾子
在宣告類別(class)、建構子(constructor)、介面(interface)或函式(function)中使用
* public: 公開的，可以被同一類別或物件中的成員存取使用，當然其他人享用也可以
* private：私人的，只有同一類別的的物件可以使用，其他人不可以用。
* protected：受到保護的，除了同一類別的物件可以用之外，如果繼承了該物件，同樣也可以使用
* default

https://hackmd.io/@metal35x/r14R56nXU

### 繼承
餐廳裡的食物，如果嫌味道不夠，那就再多加點調味料，可以符合自己的需求。




### 多型
同樣是義大利麵，白醬義大利麵跟青醬義大利麵的口味就不同
定義是：不同的物件可以做出一樣的行為，但必須由他們自己的程式碼來實作
-- 一樣的事，不同做法

> 可以先釐清這幾種概念
> * 多載(Overload)
> * 覆寫(Override)
> * 多型(Polymorphism)
>

1. 建立一個abstract的Door類別，裡面有Open(開門)與Close(關門)這兩個虛擬方法可以用
```C#
abstract class Door
{
    public virtual void Open()
    {
        Console.WriteLine("Open Door");
    }
    public virtual void Close()
    {
        Console.WriteLine("Close Door");
    }
}
```
 
2. 建立一個HorizontalDoor類別，它繼承了原先的Door，所以它也有Open與Close這兩個方法可以用

```C#
class HorizontalDoor : Door { }
```

3. 建立一個VerticalDoor類別，它也繼承了Door，可是我在裡面override了原先的Open與Close，另外實作其方法
```C#
class VerticalDoor : Door
{
    public override void Open()
    {
        Console.WriteLine("Open vertically");
    }
    public override void Close()
    {
        Console.WriteLine("Close vertically");
    }
}
```
 
4. 建立一個IAlarm介面，裡面定義了Alert這個方法

```C#
interface IAlarm
{
    void Alert();
}
```


5. 建立一個Alarm類別，它繼承了IAlarm，並在裡面實作Alert
```C#
class Alarm : IAlarm
{
    public void Alert()
    {
        Console.WriteLine("Ring ~~");
    }
}
```


 
6. 建立一個AlarmDoor類別，它繼承了Door，同時在裡面使用了Alarm類別的Alert方法
```C#
class AlarmDoor : Door
{
    private IAlarm _alarm;

    public AlarmDoor()
    {
        _alarm = new Alarm();
    }

    public void Alert()
    {
        _alarm.Alert();
    }
}
```

7. 建立一個AutoAlarmDoor類別，它繼承了AlarmDoor，並覆寫了原本的Open方法，裡面呼叫base.Open方法並接著呼叫Alert方法

```C#
class AutoAlarmDoor : AlarmDoor
{
    public override void Open()
    {
        base.Open();
        Alert();
    }
}
```

8. 建立一個DoorController類別，這個類別是用來控制管理所有的Door
```C#
class DoorController
{
    protected List<Door> _dootList = new List<Door>();

    public void AddDoor(Door Door)
    {
        _dootList.Add(Door);
    }

    public void OpenDoor()
    {
        foreach (var item in _dootList)
        {
            item.Open();
        }
    }
}
```


 
完成 !!
```C#
static void Main(string[] args)
{
    DoorController dc = new DoorController();
    dc.AddDoor(new HorizontalDoor());
    dc.AddDoor(new VerticalDoor());
    dc.AddDoor(new AlarmDoor());
    dc.AddDoor(new AutoAlarmDoor());

    dc.OpenDoor();

    Console.ReadLine();
}
```

* 注意事項
  * abstract method 不會有程式內容
  * abstract method 繼承後，一定要 override
  * virtual method 一定要有程式內容
  * 宣告為 virtual 的 method，繼承後才可以進行 override
  * 設定為 virtual 的 method，沒有一定要 override


```c#
public class Programmer
{
  public string WriteCSharp()
  {
    /* 寫Ｃ＃ */
  }
  public string WriteSQL()
  {
    /* 寫 SQL */
  }
  public string WriteJavascript()
  {
    /* 寫 Javascript */
  }
}
```

後來來了兩個人，一個寫的程式碼真的不怎麼樣...

```C#
public class Ling : Programmer
{
  public string WriteCSharp()
  {
    return "ShitCode";
  }
  public string WriteSQL()
  {
    return "ShitCode";
  }
  public string WriteJavascript()
  {
    return "shitCode";
  }
}
```

然後另外一個人來了，他除了會寫以上那三個東西，他還會做資料分析

```C#
public class Ming : Programmer
{
  public string WriteCSharp()
  {
    return "CleanCode";
  }
  public string WriteSQL()
  {
    return "CleanCode";
  }
  public string WriteJavascript()
  {
    return "CleanCode";
  }
  public DataMinding MingsMinding()
  {
    return new Tea(dataName: "Ming Data");
  }
}
```
再一起工作的情境，就會出現這樣

```C#
public void newProject()
{
  Programmer programmer01 = new Ling();
  Programmer programmer02 = new Ming();

  programmer02.WriteCSharp(); // "CleanCode"
  programmer01.WriteCSharp(); // "ShitCode"
}
```

也就是多型的核心概念，用子類別實作出各式不同的方法，藉此讓父類別的方法延伸與多樣化的效果，這樣的好處是，可以各自時做自己的方法，卻不影響到父類別

--------------
實際有可能碰到的案例，如資料庫連線，我可以讓CommSqlDBConnect與ProjectSqlDBConnect都繼承了DBConnect，但各自保有對應不同資料庫的實作，物件中的其他物件也不用管DBConnect，只要確保能夠連線即可。

這邊要注意的是
當我將一個物件(例如小明)宣告為工程師的時候，他對老闆來說就只是工程師，而不是小明，所以像是小明會資料探勘，很抱歉，在上班的時候做這件事是不被允許的...

**當子類別被以父類別的名義建立出來時，他就只能夠表現出父類別的樣子。**


## SOILD 
## 依賴反轉原則
## 介面

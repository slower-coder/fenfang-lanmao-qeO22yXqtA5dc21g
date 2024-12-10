
### 一、前言


大家好！我是付工。


十年前，刚开始学C\#编程的时候，被委托困扰了很久。


今天跟大家分享一下关于委托的那些事儿。


### 二、委托原理


什么是委托？


抛开编程，委托是一个汉语词语，指的是把事情托付给别人或别的机构办理。


为什么会有委托？什么时候使用委托？


有些事情我们直接干不了，需要找人来帮忙。


比如：


我们需要在主窗体中刷新子窗体的控件，


我们需要在多线程中刷新主线程的控件。


我们需要在某个窗体中执行另一个窗体的方法。


总之，当我们直接完成不了的时候，就可以考虑使用委托，如果可以直接完成，就没有委托什么事了。


### 三、委托案例


今天，我们结合一个案例来了解委托的前世今生。


这个错误应该每个人都遇到过，代码很简单，一运行就报错。


![](https://pic2.zhimg.com/80/v2-e62b1dacd2d1f94fbe58580329c57b47_720w.webp)
我们来分析一下错误提示：线程间操作无效：从不是创建控件“FrmMain”线程访问它。


这里的线程间意味着涉及两个线程，一个就是我们开的线程，另一个就是主线程。


从不是创建控件“FrmMain”线程，指的是什么线程呢？


我们知道程序运行之后，就会有一个主线程，又叫UI线程，通常用于处理用户界面相关的逻辑，如创建和显示窗体、处理用户输入、更新UI等。


所以，创建控件"FrmMain"的线程就是主线程。


不是创建控件“FrmMain”线程指的就是我们开的那个线程。


因此这个错误的意思就是不能在多线程里直接访问主线程的控件。


这个我们做牛马的应该都能理解，比如你花了半个月写了一个很牛的程序，你的同事想要"窃取"，你是否愿意？


![动图封面](https://pica.zhimg.com/v2-5b4bfd22f80051c23ee2028ab050f9e2_b.jpg)
 

主线程辛辛苦苦创建了控件，多线程想直接给它赋值，主线程自然也不愿意，一怒之下，就给出了一个错误警告。


![](https://pic3.zhimg.com/80/v2-2810195f3564b2878a682283510d51d0_720w.webp)
### 四、解决方案


我们回到这个问题上：你花了半个月写了一个很牛的程序，你的同事想要"窃取"，你不愿意给，他怎么办呢？


于是，聪明的他开始搬救兵，找到了你的领导，表示他有一个类似的项目，利润很高，客户很急，需要借用你的程序参考一下。


于是，你的领导跟你画了一张饼，毋庸置疑，你同意了。


你的同事就是那个线程，而你就是主线程，你的领导就是委托。


我们再回到这个错误上来，既然直接不能访问主线程的控件，那么就采用"委托"来实现。


想要使用委托，必然要先学会委托。


在C\#中，委托是一种类型，它定义了方法的签名，即方法的参数类型和返回值类型。


![动图封面](https://pic1.zhimg.com/v2-45f837586d427dbc50afb2c56fb2aa6e_b.jpg)
 

这句话如果没看明白，就不用管它了。


我们先来看看如何使用委托，这里总结了委托的五步法：


1、声明委托


声明委托需要根据最终执行方法来确定参数与返回值类型，然后根据参数和返回值来声明。


我们目的是给lbl\_Time控件赋值当前时间，因此参数和返回值均为空。


声明委托代码如下




```
//【1】声明委托
public delegate void SetTimeDelegate();

```


2、创建委托对象


委托是一种类型，就像类class一样，我们都知道如果要创建某个类的对象的写法，那么创建委托对象是一样的。


创建委托对象代码如下：




```
//【2】创建委托对象
private SetTimeDelegate setTime;

```


3、创建委托方法


委托对象就像领导一样，它是不干活的，最终干活的还得是下面的牛马。


因此我们需要编写一个最终干活的方法，我们这个活很简单，所以委托方法也很简单。


创建委托方法代码如下：




```
//【3】创建委托方法
private void setTimeMethod()
{
   this.lbl_Time.Text = DateTime.Now.ToString("HH:mm:ss");
}

```


4、委托绑定


领导有了，牛马有了，如何将这两者联系起来呢？


我们需要进行关系绑定，这就需要进行委托绑定。


委托绑定代码如下：




```
//【4】委托绑定
this.setTime = this.setTimeMethod;

```


5、委托调用


如果不涉及多线程，直接就像调用方法一样调用委托对象即可。


但是这里涉及到了多线程，也就是我们这里最终仍然需要主线程来调用。


怎么通过主线程来调用这个委托对象呢？


![](https://pic1.zhimg.com/80/v2-b17fb361442b7af7e1f246332eb38130_720w.webp)
Control类中提供了一个Invoke方法，这个方法的含义是在拥有此控件的基础窗体句柄的线程上执行指定的委托。


委托调用的代码如下：




```
//多线程方法
private void TaskMethod()
{
   //【5】调用委托
   this.lbl_Time.Invoke(setTime);
}
```


这样就基于委托解决了跨线程访问的问题。


我们运行一下程序，效果如下：


![](https://pic1.zhimg.com/80/v2-ffdf8497e4993d91319c444143ec2fac_720w.webp)
这时候，我们在看这句话，是不是就豁然开朗了呢？


委托是一种类型，它定义了方法的签名，即方法的参数类型和返回值类型。


如果我们接触过C\+\+编程，委托类似于C\+\+中的指针。


![动图封面](https://pic4.zhimg.com/v2-8da5f5a9628d898f669e4cc6d1e295d7_b.jpg)
 

### 五、委托今生


.Net Framework3\.5之后开始有了Action和Func，Action和Func是内置委托，也叫系统委托，就是微软的工程师帮我们在底层写好了委托声明，这样我们就不需要声明委托。


Action委托针对无返回值情况，具有Action、Action、Action、Action……Action多达16个参数的形式，其中传入参数均采用泛型T，涵盖了几乎所有可能存在的无返回值的委托类型。


Func委托针对有返回值情况，具有Func、Func……Func17种类型重载，T1……T16为参数，Tresult为返回类型。


于是我们开始简化我们的代码：


第一步简化：使用Action，不需要声明委托，创建的时候直接绑定




```
 //多线程方法
  private void TaskMethod()
  {
      //创建并绑定
      Action action = new Action(setTimeMethod);
      //调用委托
      this.lbl_Time.Invoke(action);
  }

```


第二步简化：action对象只使用一次，直接调用即可




```
   //多线程方法
   private void TaskMethod()
   {
       //创建委托、绑定委托、调用委托
       this.lbl_Time.Invoke(new Action(setTimeMethod));
   }

```


第三步简化：使用Lambda表达式替换委托方法




```
//多线程方法
private void TaskMethod()
{
    //创建委托、委托方法、绑定委托、调用委托
    this.lbl_Time.Invoke(new Action(()=>
    {
        this.lbl_Time.Text = DateTime.Now.ToString("HH:mm:ss");
    }));
}

```


这个最终简化的代码是不是非常熟悉呢？


![动图封面](https://pic3.zhimg.com/v2-d2b5c9db207d4baf319d5a81289f2eb2_b.jpg)
 本博客参考[slower加速器](https://jisuanqi.org)。转载请注明出处！

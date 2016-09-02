title: Java 多态实现原理
author: Cyno
category: Java
tag: 多态
date: 2015-07-02
toc: true

---
   
   既然多态是面向对象的三大本质特征之一（其它两个是数据抽象和继承），那么 C++为什么不将方法调用的默认方式设置为动态绑定，而要通过关键字virtual进行标记呢？Bruce Eckel在《Thinking in C++》中提到，这是由于历史原因造成的，C++是从C发展而来的，而C程序员最为关心的是性能问题，由于动态绑定比静态绑定多几条指令，性能有所下降， 如果将动态绑定设定为默认方法调用方式，那么很多C程序员可能不会接受，因此，C++就将动态绑定定位成可选的，并且作出保证：If you don't use it, you don't pay for it（Stroustrup）。

   但是，Java作为一个全新的完全面向对象的语言，并不存在向下兼容的问题，同时，Java的设计者也认为多态作为面向面向对象的核心，面向对象语言应该提供内置的支持，因此，Java将动态绑定作为方法调用的默认方式。

   下面，我们就详细地来了解一下Java是如何为多态提供支持的。 与C++一样，Java中也有一个存放实例方法地址的数据结构，在C++中，我们把它叫做VTable，而在java中方法表（Method Table），但是两者有很多相同之处：
     1、它们的作用是相同的，同样用来辅助实现方法的动态绑定。
     2、同样是类级别的数据结构，一个类的所有对象共享一个方法表。
     3、都是通过偏移量在该数据结构中查找某一个方法。
     4、同样保证所有派生类中继承于基类的方法在方法表中的偏移量跟该方法在基类方法表中的偏移量保持一致。
     5、方法表中都只能存放多态方法（Java中的实例方法，C++中是vitual方法）。
   但是归根结底，C++是一门编译型的语言，而Java更加偏向于解析型的，因此上述数据结构的生成和维护是有所不同的，表现在：
    1、C++中VTable和vptr是在编译阶段由编译器自动生成的，也就是说，在C++程序载入内存以前，在.obj（.o）文件中已经有这些结构的信 息；Java中的方法表是由JVM生成的，因此，使用javac命令编译后生成的.class文件中并没有方法表的信息。只有等JVM把.class文件 载入到内存中时，才会为该.class文件动态生成一个与之关联的方法表，放置在JVM的方法区中。
    2、C++中某个方法在VTable的索引号是在编译阶段已经明确知道的，并不需要在运行过程中动态获知；Java中的方法初始时都只是一个符号，并不是 一个明确的地址，只有等到该方法被第一次调用时，才会被解析成一个方法表中的偏移量，也就是说，只有在这个时候，实例方法才明确知道自己在方发表中的偏移 量了，在这之前必须经历一个解析的过程。
    此外，Java中不支持多重继承，也就不会像C++那样在这个泥潭中纠缠不清了，但Java也引入了新的概念，那就是接口，Interface。使用Interface调用一个实例方法跟使用一个Class来调用的过程是不一样的：
public class  Zoo
{
 public static void main(String[] args)
 {
   Pet p1 = new Dog();
   Pet p2 = new Dog();
   p1.say(); //首先解析一次，得到偏移量，调用方法
   p2.say(); //不用解析，直接使用上次的得到的偏移量，调用

  Cute c1 = new Dog();  
  Cute c2 = new Dog();
  c1.cute();  //这里使用接口来调用实例方法，首先同样会解析一次，得到偏移量，调用相应方法
  c2.cute(); //这里虽然上次已经解析过了，但是还是得重新跟上次一样重新解析一次，得到偏移量，调用
 }
}
interface Cute
{
 public void cute();
}
class Pet
{
  public void say(){ System.out.println("Pet say");  }
}
class Dog extends Pet implements Cute
{
     public void cute(){ System.out.println("Dog cute"); }
     public void say(){ System.out.println("Dog say");  }
}
    为什么会有这样的区别呢?这是因为实现同一个接口的类并不能保证都是从同一个超类继承的，而且这个超类也同样实现相同的接口。因此，该接口声明的方法并不能都保证处于方法表中的同一个位置上。如，可以定义下面的类：
class Cat  implements Cute
{
     public void cute(){ System.out.println("Cat cute"); }
}
    那么，Dog跟Cat同样都实现了接口Cute，因此都能够用Cute接口进行调用，但是方法cute在Dog方法表中的位置并不能保证该方法在Cat方法表中的位置是一样的。因此，对于接口调用方法，我们只好每次都重新解析一道，获得准确的偏移量，再进行调用了。这也导致了使用接口调用方法的效率要比使 用类调用实例方法低。当然，这仅仅是相对而言，JVM在实现上会予以优化，我们不能说因为接口效率低就不使用了，相反由于在面向对象作用中接口的强大作 用，java是提倡使用接口的，这一点我们是需要注意的。
    还有一点，虽然java不支持类的多重继承，但是是可以实现多个接口的，那么，在Java中会不会要像C++的多重继承那样进行必要的转换呢?这个问题， 我们只需想一下两者调用的具体过程，就能知道，Java的接口方法每次调用前都是需要解析的，在这里才会取得真正的偏移量，这跟C++中编译期间取得偏移 量是不一样，因此，在Java中是不需要进行所谓的转换的。
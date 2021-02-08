## 一、多线程的引入

## 1、什么是线程？

  线程是程序执行的一条路径，一个进程可以包含多个线程
  多线程开发执行可以提高程序的效率，可以同时完成多项工作

## 2、多线程的应用场景

   红蜘蛛同事共享屏幕给多个电脑
   迅雷开启多条线程一起下载
   QQ同时和多个人一起视频
   服务器同时处理多个客户端请求  

## 3、多线程并行和并发的区别

  并行就是两个任务同时进行，就是甲任务进行同时，乙任务也在进行（需要多核CPU）  
  并发是指两个任务都请求运行，而处理器只能接受一个任务，就把这两个任务安排轮流进行，由于时间间隔比较短，使人感觉两个任务都在运行

## 4、Java的启动是多线程吗？

 A ：Java程序运行原理
      Java命令启动java虚拟机，启动JVM，等于启动了一个应用程序，也就是启动了一个进程，该进程会自动启动一个线程，然后主线程的去调用某个类的main方法
 B ：JVM启动是多线程的吗？
      JVM启动至少启动了垃圾回收线程和主线程，所以是多线程 

## 5、Thread和Runnable

  查看源码的区别：
     a、继承Thread：由于子类重写了Thread类的run()，当调用start()方法时，直接找子类的run方法
     b、实现了Runnable：构造函数中传入了Runnable的引用，成员变量记住了它，start()调用Run方法时内部判断成员变量Runable的引用是否为空，不为空编译时，看的是Runable的run(),运行时执行的是子类的run()方法。
  继承Thread
     好处：可以直接使用Thread类中的方法，代码简单
	 弊端：如果已经有了父类，就不能用这种方法
  实现Runnable接口
     好处：即使自己定义的线程类有了父类也没关系，因为有了父类也可以实现接口，而且接口是可以多实现的
	 弊端：不能直接使用Thread中的方法需要先获取到线程对象后，才能得到Thread的方法，代码复杂

## 6、多线程（同步代码块）

###    1、什么情况下需要同步

​         当多线程并发时，有多段代码同时执行时，我们希望某一块代码执行的过程中CPU不要切换到其他线程工作，这时就需要同步

###    2、同步代码块

​         使用synchronized关键字加上一个锁对象来定义一段代码，这就叫同步代码块
​         多个同步代码块如果使用相同的锁对象，那么他们就是同步的		   

## 7、多线程（以前的线程安全的类回顾）

 A、回顾以前说过的线程安全问题
   看源码：Vector StringBuffer Hashtable Collections synchronized
   Vector是线程安全的，ArrayList是线程不安全的
   StringBuffer是线程安全的，StringBuilder是线程不安全的
   Hashtable是线程安全的，HashMap是线程不安全的

## 8、多线程的优势

​    1、线程在程序中是独立的，并发的执行流，但是，与分隔的进程相比，进程中的线程之间的隔离程度要小。它们共享内存，文件句柄和其他
每个进程应有的状态。

​    2、线程比进程具有更高的性能，这是由于同一个进程中的线程都有共性:多个线程将共享同一个进程虚拟空间。线程共享的环境包括：
进程代码段，进程的公有数据等。利用这些共享的数据等,线程很容易实现相互之间的通信。

​    3、当操作系统创建一个进程时，必须为进程分配独立的内存空间，并分配大量相关资源：但创建一个线程则简单很多，因此使用多线程来实现
并发比使用多进程实现并发的性能要高得多。

​    4、总结起来，使用多线程编程包含如下几个优点：

​      （1）进程间不能共享内存，但线程之间可以共享内存非常容易。

​      （2）系统创建进程需要为该进程重新分配系统资源，但创建线程则代价小的多，因此使用多线程来实现多任务并发比多进程的效率高。

​      （3）Java语言内置多线程功能支持，而不是单纯地作为底层操作系统的调度方式，从而简化了Java的多线程编程。

# 二、Java中序列化的好处及意义

## 1、序列化是干什么的？

​     简单说就是为了保存在内存中的各种对象的状态，并且可以把保存的对象状态再读出来。虽然你可以用你自己的各种各样的方法来保存Object States，
　但是Java给你提供一种应该比你自己好的保存对象状态的机制,那就是序列化。

## 2、什么情况下需要序列化 

　　当你想把的内存中的对象保存到一个文件中或者数据库中时候；
　　当你想用序列化在网络上传送对象的时候；
　　当你想通过RMI传输对象的时候； 
        RMI:远程方法调用(Remote Method Invocation)，一种用于实现远程过程调用(RPC)(Remote procedure call)的Java API，
	     能直接传输序列化后的Java对象和分布式垃圾收集。它的实现依赖于Java虚拟机(JVM)，因此它仅支持从一个JVM到另一个JVM的调用。

## 3、当对一个对象实现序列化时，究竟发生了什么？

　　在没有序列化前，每个保存在堆（Heap）中的对象都有相应的状态（state），即实例变量（instance ariable）比如：　　

```java
1 Foo myFoo = new Foo(); 
2 myFoo .setWidth(37); 
3 myFoo.setHeight(70); 
　　/**当通过下面的代码序列化之后，MyFoo对象中的width和Height实例变量的值（37，70）都被保存到foo.ser文件中，这样以后又可以把它 
　　从文件中读出来，重新在堆中创建原来的对象。
　　保存时候不仅仅是保存对象的实例变量的值，JVM还要保存一些小量信息，比如类的类型等以便恢复原来的对象。
　　*/
1 FileOutputStream fs = new FileOutputStream("foo.ser"); 
2 ObjectOutputStream os = new ObjectOutputStream(fs); 
3 os.writeObject(myFoo); 
```

## 4、实现序列化（保存到一个文件）的步骤

```java
Make a FileOutputStream 
1 FileOutputStream fs = new FileOutputStream("foo.ser"); 
2 
Make a ObjectOutputStream 
ObjectOutputStream os = new ObjectOutputStream(fs); 
write the object

write the object
os.writeObject(myObject1);
os.writeObject(myObject2); 
os.writeObject(myObject3); 
close the ObjectOutputStream java 代码 os.close(); 
```

## 5、举例说明 

```java
import java.io.*;
public class Box implements Serializable {
     private int width;
     private int height;
        public void setWidth(int width){
            this.width = width;
        }
        public void setHeight(int height){
            this.height = height;
        }
        public static void main(String[] args){
           Box myBox = new Box();
           myBox.setWidth(50);
           myBox.setHeight(30);
           try{
                    FileOutputStream fs = new FileOutputStream("foo.ser");    
                    ObjectOutputStream os = new ObjectOutputStream(fs);
                    os.writeObject(myBox);
                    os.close();
                }catch(Exception ex){
                    ex.printStackTrace();
                }
         }
}             
```

##  6、相关注意事项 

　　a）当一个父类实现序列化，子类自动实现序列化，不需要显式实现Serializable接口； 
　　b）当一个对象的实例变量引用其他对象，序列化该对象时也把引用对象进行序列化； 
　　c）并非所有的对象都可以序列化，,至于为什么不可以，有很多原因了
　　比如： 
   　1、安全方面的原因，比如一个对象拥有private，public等field，对于一个要传输的对象，比如写到文件，或者进行rmi传输 等等，在序列化进行传输的过程中，这个对象的private等域是不受保护的。 

​       2、资源分配方面的原因，比如socket，thread类，如果可以序列化，进行传输或者保存，
　也无法对他们进行重新的资源分配，而且，也是没有必要这样实现
把一个对象完全转成字节序列，方便传输。
就像你寄一箱饼干，因为体积太大，就全压成粉末紧紧地一包寄出去，这就是序列化的作用。
只不过JAVA的序列化是可以完全还原的。

## 7、java类的实例化

```java
package com.tongtech.ser;
import java.io.Serializable;
/**
 * @author M.hj
 * @version 1.0
 * @date 2021/1/21 9:47
 */
public class Student implements Serializable{
    private String name;
    private int age;
    private String sex;
    public Student() {
    }
    public Student(String name, int age, String sex) {
        this.name = name;
        this.age = age;
        this.sex = sex;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public int getAge() {
        return age;
    }
    public void setAge(int age) {
        this.age = age;
    }
    public String getSex() {
        return sex;
    }

    public void setSex(String sex) {
        this.sex = sex;
    }
    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", sex='" + sex + '\'' +
                '}';
    }
}
```

```java
package com.tongtech.ser;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.ObjectOutputStream;
/**
 * @author M.hj
 * @version 1.0
 * @date 2021/1/21 9:54
 */
public class SerializableStudent {
    public static void main(String[] args) {
        try {
            //将对象Student序列化
            ObjectOutputStream objOutputStream = new ObjectOutputStream(new FileOutputStream("G:\\obj.txt"));
            Student student = new Student("mihaijun", 26, "男");
            objOutputStream.writeObject(student);
        } catch (IOException e) {
            e.printStackTrace();
        }
         /*
        * 将对象反序列化
        * */
        try {
            ObjectInputStream objectInputStream = new ObjectInputStream(new FileInputStream("G:\\obj.txt"));
            try {
                System.out.println(((Student)objectInputStream.readObject()).toString());
            } catch (ClassNotFoundException e) {
                e.printStackTrace();
            }
        } catch (IOException e) {
            e.printStackTrace();
        }   
    }
}
```


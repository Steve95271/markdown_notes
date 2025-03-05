# 进程与线程



## 什么是进程？

**定义：**

- 进程是操作系统中程序的一次运行实例（例如运行了微信这个应用程序就相当一个进程）。
- 它是资源分配的基本单位，拥有独立的内存空间（代码段，数据段，堆和栈）。

**特点：**

- 每个进程都有自己的地址空间，相互独立。
- 操作系统为每个进程分配必要的资源，如CPU时间、内存、文件句柄等。
- 进程之间的通讯需要通过特定的机制（管道、信号、共享内存），因为它们的内存是独立的。



## 什么是线程？

**定义：**

- 线程是进程中的一个执行单元，也被称为轻量级进程。
- 它是CPU调度的基本单位，属于进程的一部分。

**特点：**

- 同一进程的线程共享地址空间（代码、数据、文件句柄等）。
- 线程有自己的栈、寄存器和程序计数器。
- 多个线程可以并发地运行，提高程序的执行效率。



在 Java 当中，当启动main程序时，相当于启动了一个JVM的进程（一个进程可以有多个线程），而main函数所在的线程就是这个程序的**主线程**。





# 并行与并发



**并发与并行的区别**

- **并发**：两个及两个以上的程序在同一 **时间段** 内执行。
- **并行**：两个及两个以上的程序在同一 **时刻** 执行。

最关键的点是：是否是 **同时** 执行。



# Java开启线程的方式



**Java开启线程有三种方式。**



**方式一：**继承Thread类

- 重写run()方法
- 在run()方法中编写需要多线程执行的代码
- 创建对象并使用start()方法开启线程

**方式二：**实现Runnable接口

- 重写run()方法
- 创建实现Runnable接口类的对象
- 把实现Runnable接口类的对象作为参数传入到Thread类的构造器中创建Thread对象
- 调用Thread的start()方法开启线程

**方式三：**实现Callable接口 (有返回值使用方式三)

- 使用实现Callable接口的方式开启线程能够**获得线程执行完后的结果**
- 需要创建FutureTask对象用于管理结果
- 想要获得多个结果需要创建多个FutureTask对象



**推荐使用扩展性更好的方式二、方式三来开启线程（因为继承Thread后就不能继承其它类）。**



# 线程安全

**当开启多线程后，线程内的资源需要被保护。**

例如：

实现Runnable的方式开启线程后，如果不使用同步代码块(synchronized 关键字)进行上锁，在这个例子中tickets会被重复卖出多份。因为Java的线程调度使用的是**抢占式调度**。



使用**synchronized**关键字进行上锁后，同步代码块中的代码在执行完之前锁是不会解开的，这样就能够确保同步代码块中的代码不会执行到一半时执行权被其它线程抢去。

```java
class TicketRunnable implements Runnable {

    private int tickets = 2000;
    private Object object = new Object();

    @Override
    public void run() {
        while(true) {
            synchronized (object) {
                if (tickets == 0) {
                    break;
                }

                System.out.println(Thread.currentThread().getName() + ": " + "卖出了第" + tickets + "张票");
                tickets--;
            }
        }
    }
}
```



**使用继承Thread类的方式开启多线程时需要注意的地方**

- 由于继承了Thread类，开启线程需要new多个继承Thread类的对象，这样会导致每个对象独享一份线程资源（各个对象有各个对象的成员变量资源）。
- 解决这个问题可以给需要被共享资源的成员变量加上**static**关键字。

```java
class TicketInheritThread extends Thread {

    /*
    由于直接继承了Thread类，在开启多个线程需要new多个本类对象。
    这会导致成员变量在各个对象中都独有一份。意思是，在这个例子中会有三分票(tickets)。
    所以就算使用了synchronized锁也都会卖重复的票。

    解决方法：
    对成员变量加static关键字。
    加上static关键字后，成员变量在内存空间中无论new多少个对象，都会共享一份。
     */

    private static int tickets = 2000;
    private static Object object = new Object();

    @Override
    public void run() {
        while(true) {
            synchronized (object) {
                if (tickets == 0) {
                    break;
                }

                System.out.println(Thread.currentThread().getName() + ": " + "卖出了第" + tickets + "张票");
                tickets--;
            }
        }
    }
}
```



**synchronized() 关键字传入的对象建议使用字节码文件对象，因为它是唯一且编译后不可被修改**

```java
class TicketInheritThread extends Thread{

    private static int tickets = 2000;
    private static Object object = new Object();

    @Override
    public void run() {
        while(true) {
          	// 类名.class
            synchronized (TicketInheritThread.class) {
                if (tickets == 0) {
                    break;
                }

                System.out.println(Thread.currentThread().getName() + ": " + "卖出了第" + tickets + "张票");
                tickets--;
            }
        }
    }
}
```



**同步方法**

在方法上加上synchronized关键字可以实现和同步代码块一样的上锁效果。

但是使用同步方法后，无法像同步代码块那样自己传锁对象参数。

细节：

- **非静态方法**使用的是this关键字，本类对象作为锁。
- **静态方法**使用的是字节码对象来作为锁，因为静态方法没有this关键字。

```java
// 非静态，使用this关键字，也就是本类对象作为锁
public synchronized void calculate() {
  System.out.println(1 + 1);
}

// 静态，使用字节码对象作为锁
public static synchronized void calculate() {
  System.out.println(1 + 1);
}
```





**ReentrantLock方式实现线程安全，它是一种互斥锁**

```java
class TicketReentrantLock implements Runnable {

    private int tickets = 2000;
    private ReentrantLock lock = new ReentrantLock();

    @Override
    public void run() {
        while(true) {
            try {
                lock.lock();
                if (tickets == 0) {
                    break;
                }

                System.out.println(Thread.currentThread().getName() + ": " + "卖出了第" + tickets + "张票");
                tickets--;
            } finally {
                lock.unlock();
            }
        }
    }
}
```

在这里要使用try/finally确保解锁执行。

不然在tickets等于0的时候会直接跳出while循环，解锁的代码不会被执行。
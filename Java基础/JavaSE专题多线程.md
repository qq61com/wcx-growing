非本人记录，引用

# 1.程序、进程、线程

**程序(program)**：程序是静态的概念，程序是为了解决某种问题，使用一种编程语言书写的一堆代码和指令的集合，例如网上下载的王者荣耀就是一个程序。

**进程(process)**：进程是动态的概念，就是指运行在内容中的程序。例如计算机中的进程。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190529225806991.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydWNlTGl1X2NvZGU=,size_16,color_FFFFFF,t_70)**线程(thread)** 进程可以再一步进行细化，进程中每一个小的执行单元，称为一个线程。
进程中包含线程，那么如果一个进程中包含多个线程，就是多线程程序。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190529230006517.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydWNlTGl1X2NvZGU=,size_16,color_FFFFFF,t_70)
> 简而言之：一个程序运行后至少有一个进程，一个进程中可以包含多个线程。
# 2.多线程的使用场合

    在Java程序中要同时执行多个任务，例如多线程下载
    程序需要实现一些需要等待的任务时，如用户输入、文件读写操作、网络操作、搜索等。
    后台运行的一些任务也会用到多线程.
    ........
# 3.创建多线程方式
## 3.1.继承Thread，重写父类的run()方法
Java使用`java.lang.Thread`类代表**线程**，所有的线程对象都必须是Thread类或其子类的实例。每个线程的作用是完成一定的任务，实际上就是执行一段程序流即一段顺序执行的代码。Java使用线程执行体来代表这段程序流。Java中通过继承Thread类来**创建**并**启动多线程**的步骤如下：

```
1. 定义Thread类的子类，并重写该类的run()方法，该run()方法的方法体就代表了线程需要完成的任务,因此把run()方法称为线程执行体。
2. 创建Thread子类的实例，即创建了线程对象
3. 调用线程对象的start()方法来启动该线程
```
代码如下：
**自定义线程类**
```java
/**
 * @author bruceliu
 * @create 2019-05-29 23:15
 * @description 继承Thread，重写父类的run()方法
 */
public class MyThread extends Thread {

    /*
     * 利用继承中的特点
     * 将线程名称传递 进行设置
     */
    public MyThread(String name) {
        super(name);
    }

    /*
     * 重写run方法
     * 定义线程要执行的代码
     */
    public void run() {
        for (int i = 0; i < 20; i++) {
            //getName()方法 来自父亲
            System.out.println(getName() + i);
        }
    }
}
```
 **测试类**
```java
/**
 * @author bruceliu
 * @create 2019-05-29 23:17
 * @description 继承Thread，重写父类的run()方法
 */
public class TestMyThread {

    public static void main(String[] args) {
        System.out.println("这里是main线程");
        MyThread mt = new MyThread("小强");
        mt.start();//开启了一个新的线程
        for (int i = 0; i < 20; i++) {
            System.out.println("旺财:"+i);
        }
    }
}
```
 **流程图**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190529232247220.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydWNlTGl1X2NvZGU=,size_16,color_FFFFFF,t_70)
## 3.2.实现Runnable接口,重写run方法
采用 java.lang.Runnable 也是非常常见的一种，我们只需要重写run方法即可。和继承自Thread类差不多，不过实现Runnable后，还是要通过一个Thread来启动：

```
1. 定义Runnable接口的实现类，并重写该接口的run()方法，该run()方法的方法体同样是该线程的线程执行体。
2. 创建Runnable实现类的实例，并以此实例作为Thread的target来创建Thread对象，该Thread对象才是真正
的线程对象。
3. 调用线程对象的start()方法来启动线程。
```
**自定义线程类**
```java
/**
 * @author bruceliu
 * @create 2019-05-30 11:37
 * @description 实现Runnable接口,重写run方法
 */
public class MyRunnable implements Runnable{
    @Override
    public void run() {
        for (int i = 0; i < 20; i++) {
            System.out.println(Thread.currentThread().getName()+" "+i);
        }
    }
}
```
**线程测试类**
```java
/**
 * @author bruceliu
 * @create 2019-05-30 11:39
 * @description 实现Runnable接口,重写run方法
 */
public class TestMyRunnable {

    public static void main(String[] args) {
        //创建自定义类对象 线程任务对象
        MyRunnable mr = new MyRunnable();
        //创建线程对象
        Thread t = new Thread(mr, "小强");
        t.start();
        for (int i = 0; i < 20; i++) {
            System.out.println("旺财 " + i);
        }
    }
}
```
通过实现Runnable接口，使得该类有了多线程类的特征。run()方法是多线程程序的一个执行目标。所有的多线程代码都在run方法里面。Thread类实际上也是实现了Runnable接口的类。
在启动的多线程的时候，需要先通过Thread类的构造方法Thread(Runnable target) 构造出对象，然后调用Thread对象的start()方法来运行多线程代码。

实际上所有的多线程代码都是通过运行Thread的start()方法来运行的。因此，不管是继承Thread类还是实现Runnable接口来实现多线程，最终还是通过Thread的对象的API来控制线程的，熟悉Thread类的API是进行多线程编程的基础。

## 3.3.使用匿名内部类方式

使用线程的内匿名内部类方式，可以方便的实现每个线程执行不同的线程任务操作。
使用匿名内部类的方式实现Runnable接口，重新Runnable接口中的run方法：

**代码如下：**
```java
/**
 * @author bruceliu
 * @create 2019-09-05 10:45
 * @description
 * 内部类：
 *    局部内部类
 *    匿名内部类
 *    静态内部类
 *    成员内部类
 */
public class Demo1 {

    public static void main(String[] args) {
        System.out.println("我是主线程........");

        //匿名内部类
        Runnable r=new Runnable() {
            @Override
            public void run() {
                for (int i = 0; i <100 ; i++) {
                    System.out.println(Thread.currentThread().getName()+"---->"+i);
                }
            }
        };
        new Thread(r,"我是子线程A").start();


        //开启了一个子线程
        Thread t=new Thread(){
            @Override
            public void run() {
                for (int i = 0; i <100 ; i++) {
                    System.out.println(Thread.currentThread().getName()+"---->"+i);
                }
            }
        };
        t.setName("我是线程B");
        t.start();

        //主线程输出
        for (int i = 0; i <100 ; i++) {
            System.out.println(Thread.currentThread().getName()+"---->"+i);
        }

    }
}
```
## 3.4.Thread和Runnable的区别
**相同点：**

    都是重写run方法，调用start方法启动

**不同点：**

    继承Thread类方式，那么这个类就不能再继承别的类，有局限性
    实现runnable接口，还可以继承别的类，避免了单继承的局限性，所以推荐使用实现接口的方式
    
    实现Runnable接口，还可以方便的实现线程间资源的共享!
```java
/**
 * @author bruceliu
 * @create 2019-09-05 10:21
 * @description 实现Runabble接口
 */
public class MyRunable extends Object implements Runnable, Serializable {

    @Override
    public void run() {
        for (int i = 0; i < 20; i++) {
            // Thread.currentThread().getName() 获取当前线程的线程名
            // 默认的线程名是 Thread-0
            // 如果开第二个线程名字  Thread-1
            System.out.println(Thread.currentThread().getName()+" "+i);
        }
    }

}
```

# 4.线程的生命周期(面试题)

    1. 新建(new)：新创建了一个线程对象。
    2. 就绪态(runnable)：线程对象创建后，其他线程(比如main线程）调用了该对象的start()方法。该状态的线程位于可运行线程池中，等待被线程调度选中，获取cpu 的使用权 。
    3. 运行(running)：可运行状态(runnable)的线程获得了cpu 时间片（timeslice） ，执行程序代码。
    4. 阻塞(block)：阻塞状态是指线程因为某种原因放弃了cpu 使用权，也即让出了cpu timeslice，暂时停止运行。直到线程进入可运行(runnable)状态，才有机会再次获得c pu timeslice 转到运行(running)状态
    5. 死亡(dead)：线程run()、main() 方法执行结束，或者因异常退出了run()方法，则该线程结束生命周期。死亡的线程不可再次复生。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190904233336138.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydWNlTGl1X2NvZGU=,size_16,color_FFFFFF,t_70)
# 5.线程的调度
## 5.1.Join方法
Thread提供了让一个线程等待另一个线程完成的方法join()方法。当在某个程序执行流中调用其他线程的join()方法时，调用线程将被阻塞，直到被join()方法加入的join线程执行完为止。join()方法通常由使用线程的程序调用，以将大问题划分成许多小问题，每个小问题分配一个线程。当所有的小问题都得到处理后，再调用主线程来进一步操作。例如当在主线程当中执行到t1.join()方法时，就认为主线程应该把执行权让给t1。

**代码演示**：
```java
/**
 * @author bruceliu
 * @create 2019-06-01 23:12
 * @description 当在主线程当中执行到t1.join()方法时，就认为主线程应该把执行权让给t1
 */
public class JoinThread extends Thread{

    // 提供一个有参数的构造器，用于设置该线程的名字
    public JoinThread(String name) {
        super(name);
    }

    // 重写run方法，定义线程执行体
    public void run() {
        for (int i = 0; i < 100; i++) {
            System.out.println(getName() + "" + i);
        }
    }

    public static void main(String[] args) throws Exception {
        // 启动子线程
        new JoinThread("新线程------>").start();
        for (int i = 0; i < 100; i++) {
            if (i == 20) {
                JoinThread jt = new JoinThread("被Join的线程");
                jt.start();
                // main线程调用了jt线程的join()方法，main线程
                // 必须等jt执行结束才会向下执行
                jt.join();
            }
            System.out.println(Thread.currentThread().getName() + "======>" + i);
        }
    }
}
```
**结果分析**：
上面程序中一共有3个线程，主方法开始时就启动了名为"新线程"的子线程，该子线程将会和main线程并发执行。当主线程的循环变量i等于20时启动了名为"被Join的线程"的线程，该线程不会和main线程并发执行。main线程必须等该线程执行结束后才可以向下执行。在名为"被Join的线程"的线程执行时，实际上只有2个子线程并发执行，而主线程处于等待状态。运行上面程序。从上面的运行结果可以看出，主线程执行到i=20时启动，并join了名为"被Join的线程"的线程，所以主线程将一直处于阻塞状态，直到名为"被Join的线程"的线程执行完成。
## 5.2.线程优先级

```
在任意时刻，当有多个线程处于可运行状态时，运行系统总是挑选一个优先级最高的线程执行，只有当线程停止、退出或者由于某些原因不执行的时候，低优先级的线程才可能被执行.

Java运行系统的线程调度算法是抢占式（preemptive）的，当更高优先级的线程出现并处于Runnable状态时，运行系统将选择高优先级的线程执行.
```

>注意: 不能单纯的依靠调整优先级来决定执行的先后顺序，只不过优先级高抢占CPU的概率要大写。但是不能依靠线程的优先级来决定线程的执行顺序.

Java线程的优先级是一个整数，其取值范围是1 (Thread.MIN_PRIORITY ） - 10 （Thread.MAX_PRIORITY ）。
Thread源代码里对NORM_PRIORITY (数值为5） 的注释是“线程默认的优先级”
>public static final int MIN_PRIORITY = 1;
    public static final int NORM_PRIORITY = 5;
    public static final int MAX_PRIORITY = 10;

   **代码示例：**
  ```java
package com.bruceliu.demo10;

/**
 * @author bruceliu
 * @create 2019-06-01 23:29
 * @description 线程的优先级
 */
public class PriorityExample{

    public static void main(String[] args){
        Thread a = new PThread("A");
        Thread b = new PThread("B");

        // 注意设置了优先级， 不代表每次都一定会被执行。 只是CPU调度会优先分配
        a.setPriority(10); //设置优先级
        a.setPriority(1);

        b.start();
        a.start();
    }
}

class PThread extends Thread{
    public PThread(String n){
        super(n);
    }

    public void run(){
        for(int i=0; i<10; i++){
            if(i%2 == 0){
                System.out.print(getName());
            }
        }
    }
}
  ```
## 5.3.线程睡眠-sleep方法
如果需要让当前正在执行的线程暂停一段时，并进入阻塞状态，则可以通过调用Thread类的静态sleep()方法来实现。当前线程调用sleep()方法进入阻塞状态后，在其睡眠时间段内，该线程不会获得执行的机会，即使系统中没有其他可执行的线程，处于sleep()中的线程也不会执行，因此sleep()方法常用来暂停程序的执行。

下面程序调用sleep()方法来暂停主线程的执行，因为该程序只有一个主线程，当主线程进入睡眠后，系统没有可执行的线程，所以可以看到程序在sleep()方法处暂停.
**示例代码：**
```java
/**
 * @author bruceliu
 * @create 2019-06-01 23:36
 * @description sleep方法
 */
public class SleepTest {
    public static void main(String[] args) throws Exception {
        for (int i = 0; i < 10; i++) {
            System.out.println("当前时间: " + new Date());
            // 调用sleep方法让当前线程暂停1s。
            Thread.sleep(1000);
        }
    }
}
```
上面程序中sleep()方法将当前执行的线程暂停1秒，运行上面程序，看到程序依次输出10条字符串，输出2条字符串之间的时间间隔为1秒.

## 5.4.线程让步-yield方法
yield()方法是一个和sleep()方法有点相似的方法，它也是Threard类提供的一个静态方法，它也可以让当前正在执行的线程暂停，但它不会阻塞该线程，它只是将该线程转入就绪状态。yield()只是让当前线程暂停一下，让系统的线程调度器重新调度一次，完全可能的情况是：当某个线程调用了yield()方法暂停之后，线程调度器又将其调度出来重新执行。

实际上，当某个线程调用了yield()方法暂停之后，只有优先级与当前线程相同，或者优先级比当前线程更高的处于就绪状态的线程才会获得执行的机会。下面程序使用yield()方法来让当前正在执行的线程暂停。

yield()让当前正在运行的线程回到可运行状态，以允许具有相同优先级的其他线程获得运行的机会。因此，使用yield()的目的是让具有相同优先级的线程之间能够适当的轮换执行。但是，实际中无法保证yield()达到让步的目的，因为，让步的线程可能被线程调度程序再次选中。

 **代码示例：**
```java
package com.bruceliu.demo12;

/**
 * @author bruceliu
 * @create 2019-06-01 23:42
 * @description 线程让步---yield()
 */
public class YieldTest extends Thread {

    public YieldTest(String name) {
        super(name);
    }

    // 定义run方法作为线程执行体
    public void run() {
        for (int i = 0; i < 50; i++) {
            System.out.println(getName() + "" + i);
            // 当i等于20时，使用yield方法让当前线程让步
            if (i == 20) {
                Thread.yield();
            }
        }
    }

    public static void main(String[] args) throws Exception {
        // 启动两条并发线程
        YieldTest yt1 = new YieldTest("高级-------->");
        // 将ty1线程设置成最高优先级
        //yt1.setPriority(Thread.MAX_PRIORITY);
        yt1.start();
        YieldTest yt2 = new YieldTest("低级");
        // 将yt2线程设置成最低优先级
        //yt2.setPriority(Thread.MIN_PRIORITY);
        yt2.start();
    }
}
```

## 5.5.yield方法和sleep方法对比
- sleep()方法暂停当前线程后，会给其他线程执行机会，不会理会其他线程的优先级；但yield()方法只会给优先级相同，或优先级更高的线程执行机会

- sleep()方法会将线程转入阻塞状态，直到经过阻塞时间才会转入就绪状态：而yield()不会将线程转入阻塞状态，它只是强制当前线程进入就绪状态。因此完全有可能某个线程调用yield()方法暂停之后，立即再次获得处理器资源被执行

- sleep()方法声明抛出了InterruptcdException异常，所以调用sleep()方法时要么捕捉该异常，要么显式声明抛出该异常；而yield()方法则没有声明抛出任何异常

- sleep()方法比yield()方法有更好的可移植性，通常不建议使用yield()方法来控制并发线程的执行

## 5.6.面试题
- 有T1、T2、T3三个线程，你怎样保证T2在T1执行完后执行，T3在T2执行完后执行
 **代码示例：**
```java
/**
 * @author bruceliu
 * @create 2019-06-01 23:52
 * @description
 */
public class JoinThreadDemo02 {

    public static void main(String[] args) {
        Thread t1 = new Thread(new Runnable() {
            public void run() {
                for (int i = 0; i < 20; i++) {
                    System.out.println("t1,i:" + i);
                }
            }
        });
        Thread t2 = new Thread(new Runnable() {
            public void run() {
                try {
                    t1.join();
                } catch (Exception e) {
                    // TODO: handle exception
                }
                for (int i = 0; i < 20; i++) {
                    System.out.println("t2,i:" + i);
                }
            }
        });
        Thread t3 = new Thread(new Runnable() {
            public void run() {
                try {
                    t2.join();
                } catch (Exception e) {
                    // TODO: handle exception
                }
                for (int i = 0; i < 20; i++) {
                    System.out.println("t3,i:" + i);
                }
            }
        });
        t1.start();
        t2.start();
        t3.start();
    }
}
```

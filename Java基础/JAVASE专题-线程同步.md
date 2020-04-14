# 1.今日任务

    线程安全
    线程同步
    死锁
    Lock锁
    等待唤醒机制
# 2.线程安全
## 2.1多线程访问临界资源
如果有多个线程在同时运行，而这些线程可能会同时运行这段代码。程序每次运行结果和单线程运行的结果是一样的，而且其他的变量的值也和预期的是一样的，就是线程安全的。

我们通过一个案例，演示线程的安全问题：

电影院要卖票，我们模拟电影院的卖票过程。假设要播放的电影是 “肖申克的救赎”，本次电影的座位共100个(本场电影只能卖100张票)。
我们来模拟电影院的售票窗口，实现多个窗口同时卖 “肖申克的救赎”这场电影票(多个窗口一起卖这100张票)需要窗口，采用线程对象来模拟；需要票，Runnable接口子类来模拟。

**电影票类**

```java
/**
 * @author bruceliu
 * @Description
 */
public class Ticket implements Runnable {

	// 共100票
	int ticket = 100;

	@Override
	public void run() {
		while (ticket > 0) {
			// 同步代码块结合对象锁
			if (ticket <= 0) {
				return;
			}
			try {
				Thread.sleep(100);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
			ticket--;
			System.out.println("售票员" + Thread.currentThread().getName() + "售出一张票，剩余:" + ticket);
		}
	}
}
```
**测试类**

```java
/**
 * @author bruceliu
 * @Description
 */
public class ThreadDemo {

	public static void main(String[] args) {
		
		// 创建票对象
		Ticket ticket = new Ticket();

		// 创建3个窗口
		Thread t1 = new Thread(ticket, "窗口小姐姐A");
		Thread t2 = new Thread(ticket, "窗口小姐姐B");
		Thread t3 = new Thread(ticket, "窗口小姐姐C");

		t1.start();
		t2.start();
		t3.start();
	}
}
```
运行结果发现：上面程序出现了问题

```
票出现了重复的票
其实，线程安全问题都是由全局变量及静态变量引起的。若每个线程中对全局变量、静态变量只有读操作，而无写操作，一般来说，这个全局变量是线程安全的；若有多个线程同时执行写操作，一般都需要考虑线程同步，否则的话就可能影响线程安全
```
**本质原因：**
有多个线程在同时访问一个资源，如果一个线程在取值的过程中，时间片又被其他线程抢走了，临界资源问题就产生了。

**解决办法：**
一个线程在访问临界资源的时候，如果给这个资源“上一把锁”，这个时候如果其他线程也要访问这个资源，就得在“锁”外面等待

## 2.2.线程同步（线程安全处理Synchronized）
java中提供了线程同步机制，它能够解决上述的线程安全问题。
线程同步的方式有两种：

    方式1：同步代码块
    方式2：同步方法
### 2.2.1.同步代码块
同步代码块: 在代码块声明上 加上synchronized   加锁有性能差 安全性高！

```
synchronized (锁对象) {
	可能会产生线程安全问题的代码
}
```

**说明：**
a.程序走到代码段中，就用锁来锁住了临界资源，这个时候，其他线程不能执行代码段中的代码，只能在锁外边等待
b.执行完代码段中的这段代码，会自动解锁。然后剩下的其他线程开始争抢cpu时间片
c.一定要保证不同的线程看到的是同一把锁，否则同步代码块没有意义。

使用同步代码块，对电影票案例中Ticket类进行如下代码修改

```java
/**
 * @author bruceliu
 * @Description
 */
public class Ticket implements Runnable {

	// 共100票
	int ticket = 10;

	// 定义锁对象,任何对象都可以充当一个对象锁
	// Object lock = new Object();

	String str = new String();

	@Override
	public void run() {
		while (ticket > 0) {
			// 同步代码块结合对象锁
			synchronized (str) {
				if (ticket <= 0) {
					return;
				}
				try {
					Thread.sleep(100);
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
				ticket--;
				System.out.println("售票员" + Thread.currentThread().getName() + "售出一张票，剩余:" + ticket);
			}
		}
	}
}
```
当使用了同步代码块后，上述的线程的安全问题，解决了。

### 2.2.2.同步方法
同步方法：在方法声明上加上synchronized

```
public synchronized void method(){
   	可能会产生线程安全问题的代码
}
```
使用同步方法，对电影票案例中Ticket类进行如下代码修改：

```java
/**
 * @author bruceliu
 * @Description
 */
public class Ticket implements Runnable {

	// 共100票
	int ticket = 10;

	@Override
	public void run() {
		while (ticket > 0) {
			sellTickets();
		}
	}

	// 同步方法，作用和同步代码块一样
	public synchronized void sellTickets() {
		if (ticket <= 0) {
			return;
		}
		ticket--;
		// 在锁中进行sleep， 不会释放锁标记，其他线程仍然访问不了
		try {
			Thread.sleep(100);
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
		System.out.println("售票员" + Thread.currentThread().getName() + "售出一张票，剩余为" + ticket);
	}

}
```
同步代码块优于同步方法？？？为什么？

    同步方法把整个方法都加锁了！粒度太大！！
    同步代码块可以只同步想要加锁的内容，粒度更细致！ 锁的粒度越小越好！！

## 2.3 死锁
同步锁使用的弊端：当线程任务中出现了多个同步(多个锁)时，如果同步中嵌套了其他的同步。这时容易引发一种现象：程序出现无限等待，这种现象我们称为死锁。这种情况能避免就避免掉。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190905150114387.png)
经典的“哲学家就餐问题”，5个哲学家吃中餐，坐在圆卓子旁。有5根筷子（不是5双），每两个人中间放一根，哲学家时而思考，时而进餐。每个人都需要一双筷子才能吃到东西，吃完后将筷子放回原处继续思考，如果每个人都立刻抓住自己左边的筷子，然后等待右边的筷子空出来，同时又不放下已经拿到的筷子，这样每个人都无法得到1双筷子，无法吃饭都会饿死，这种情况就会产生死锁：每个人都拥有其他人需要的资源，同时又等待其他人拥有的资源，并且每个人在获得所有需要的资源之前都不会放弃已经拥有的资源。

**不同的线程分别占用对方需要的同步资源不放弃，都在等待对方放弃自己需要的同步资源，就形成了线程的死锁。这种情况能避免就避免掉。**

```
synchronzied(A锁){
	synchronized(B锁){
         
    }
}
```
**示例:**

```
public class Chinese extends Thread {

	@Override
	public void run() {
		synchronized (Resouce.daocha) {
			System.out.println(Thread.currentThread().getName()+"说:给我筷子~~");
			try {
				Thread.sleep(3000);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
			// 想 同步 筷子资源
			synchronized (Resouce.kuaizi) {
				System.out.println(Thread.currentThread().getName()+"说:把刀叉给你~~");
			}
		}
		
	}
}
```

```java
public class English extends Thread {

	@Override
	public void run() {
		synchronized (Resouce.kuaizi) {
			System.out.println(Thread.currentThread().getName() + "说:给我刀叉~~");
			try {
				Thread.sleep(3000);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
			//代表想同步 刀叉资源
			synchronized (Resouce.daocha) {
				System.out.println(Thread.currentThread().getName() + "说:把筷子给你~~");
			}
		}

	}
}
```

```java
/*
 * 资源
 */
public class Resouce {

	public static String kuaizi="筷子";
	
	public static String daocha="刀叉";
}
```

```java
/*
 * 测试类 
 */
public class Test {

	public static void main(String[] args) {

		Chinese c = new Chinese();
		c.setName("中国人");
		c.start();

		English e = new English();
		e.setName("美国人");
		e.start();
	}
}
```

## 2.4 Lock接口
查阅API，查阅Lock接口描述，Lock 实现提供了比使用 synchronized 方法和语句可获得的更广泛的锁定操作。
**Lock接口中的常用方法**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190905150248684.png)
Lock提供了一个更加面对对象的锁，在该锁中提供了更多的操作锁的功能。通过使用ReentrantLock这个类来进行锁的操作,它实现了Lock接口，使用ReentrantLock可以显式地加锁、释放锁

我们使用Lock接口，以及其中的lock()方法和unlock()方法替代同步，对卖票案例中Ticket类进行如下代码修改：

**案例一：模拟售票**

```java
/**
 * @author bruceliu
 * @Description
 */
public class ReentrantLockDemo01 implements Runnable {

	// 需求：100张
	// 临界资源
	int count = 100;

	// 定义一个ReentrantLock类的对象
	ReentrantLock lock = new ReentrantLock();

	@Override
	public void run() {
		while (count > 0) {
			// 加锁
			// lock()
			lock.lock();
			if (count <= 0) {
				return;
			}
			try {
				Thread.sleep(100);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
			count--;
			System.out.println("售票员" + Thread.currentThread().getName() + "售出一张票，剩余为" + count);
			// 解锁
			// unlock()
			lock.unlock();
			// 注意：lock（）和unlock（）都是成对出现的
		}
	}
}
```

```java
/**
 * @author bruceliu
 * @Description 测试类
 */
public class TestLock {

	public static void main(String[] args) {
		
		ReentrantLockDemo01 r=new ReentrantLockDemo01();
		
		Thread t0 = new Thread(r, "喜羊羊");
		Thread t1 = new Thread(r, "沸羊羊");
		Thread t2 = new Thread(r, "灰太狼");
		Thread t3 = new Thread(r, "小灰灰");

		t0.start();
		t1.start();
		t2.start();
		t3.start();

	}

}
```
## 2.5等待唤醒机制
在开始讲解等待唤醒机制之前，有必要搞清一个概念——线程之间的通信：多个线程在处理同一个资源，但是处理的动作（线程的任务）却不相同。通过一定的手段使各个线程能有效的利用资源。而这种手段即—— 等待唤醒机制。

等待唤醒机制所涉及到的方法：

```
wait() :等待，将正在执行的线程释放其执行资格 和 执行权，并存储到线程池中。
notify()：唤醒，唤醒线程池中被wait（）的线程，一次唤醒一个，而且是任意的。
notifyAll()： 唤醒全部：可以将线程池中的所有wait() 线程都唤醒。
```

其实，所谓唤醒的意思就是让 线程池中的线程具备执行资格。必须注意的是，这些方法都是在 同步中才有效。同时这些方法在使用时必须标明所属锁，这样才可以明确出这些方法操作的到底是哪个锁上的线程。

仔细查看JavaAPI之后，发现这些方法 并不定义在 Thread中，也没定义在Runnable接口中，却被定义在了Object类中，为什么这些操作线程的方法定义在Object类中？
因为这些方法在使用时，必须要标明所属的锁，而锁又可以是任意对象。能被任意对象调用的方法一定定义在Object类中。
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019090515040783.png)
接下里，我们先从一个简单的示例入手:

**例子1： 线程通信的示例**

```java
public class Demo1 {

	public static void main(String[] args) {
		Test1 thread = new Test1();
		
		Thread ta = new Thread(thread);
		ta.setName("线程AAAA");  
		ta.start(); //开子线程AAA
		
		Thread tb = new Thread(thread);
		tb.setName("线程BBBB");
		tb.start(); //开子线程BBBB
	}
}

class Test1 implements Runnable {

	@Override
	synchronized public void run() {
		for (int i = 0; i < 5; i++) {
			System.out.println(Thread.currentThread().getName() + "===" + i);
			if (i == 2) {
				try {
					wait(); // 退出运行状态，放弃资源锁，进入到等待队列状态
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
			}
			if (i == 1) {
				notify(); // 从等待的序列中唤起 一个线程
			}
			if (i == 4) {
				notify();
			}
		}
	}
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190905150441896.png)
**例子2： 两个线程交替打印1-100的数字**

```java
public class Demo7 {

	public static void main(String[] args) {
		Communication com = new Communication();

		Thread t1 = new Thread(com);
		Thread t2 = new Thread(com);

		t1.start();
		t2.start();
	}
}

class Communication implements Runnable {
	int i = 1;
	synchronized public void run() {
		while (true) {
			notify();
			if (i <= 100) {
				System.out.println(Thread.currentThread().getName() + ":" + i++);
			} else {
				break;
			}
			try {
				wait();
			} catch (InterruptedException e) {

			}
		}
	}
}
```


# 多线程学习

## 1.什么是多线程

### 1.1多线程引入

```java
public class Demo {
    public static void main(String[] args) {
        // 代码1
        show();
        // 代码2
    }

    public static void show() {
        // 代码11
        method1();
        method2();
        // 代码22
    }

    public static void method1() {
    	// 代码111...
    }

    public static void method2() {
    	// 代码222...
    }
}

```

如果程序只有一条执行路径，那么该程序就是单线程程序

如果程序有多条执行路径，那么该程序就是多线程程序

![image-20210407013009872](https://gitee.com/codeluojay/TyproaImage/raw/master/images/image-20210407013009872.png)

### 1.2进程的概述

进程：正在执行的应用程序
		线程：进程的执行单元，执行路径
		单线程：一个应用程序只有一条执行路径
		多线程：一个应用程序有多条执行路径

### 1.3多进程的意义和理解

#### 1.3.1多进程的意义

1. 单进程的计算机只能做一件事情，而我们现在的计算机都可以做多件事情。
2. 举例：一边玩游戏(游戏进程)，一边听音乐(音乐进程)。
3. 也就是说现在的计算机都是支持多进程的，可以在一个时间段内执行多个任务。
4. 并且呢，可以提高CPU的使用率。

#### 1.3.2多个进程点同时运行的背后的理解

- 一边玩游戏，一边听音乐是同时进行的吗?
- 不是的。因为单CPU在某一个时间点上只能做一件事情。
- 而我们在玩游戏，或者听音乐的时候，是CPU在做着程序间的高效切换让我们觉得是同时进行的。

### 1.4多线程举例

- 迅雷下载中，同一个任务开多个线程去下载不同分片的东西，下载完成整合一个任务。
- 360杀毒软件一键优化电脑时候，开多个线程分别执行杀毒和清理等任务。

### 1.5并行和并发的区别

- 并行：

  - 是逻辑上的同时发生，指在某一个时间内同时运行多个程序

- 并发：

  - 是物理上的同时发生，指在某一个时间点同时运行多个程序

  并发在生活中的例子比较多，比如购物网站的秒杀是在同一个时间点内服务器运行多个程序（多线程）处理多个人购物的案例

  并行的例子可以理解为，我们在一个时间段内打开浏览器开两个窗口浏览购物网站，先上淘宝后上京东，这段时间同时购物的行为可以理解为并行。

## 2.多线程在Java中的示例

### 2.1Java程序的运行原理

实际运行Java应用比如运行一个SpringBoot应用项目时是以Java命令去运行，比如`java -jar SpringBoot.jar `，那么Java程序运行原理从进程和线程角度来描述如下：

Java命令去启动Java虚拟机(JVM)，启动JVM等于启了一个应用程序，也就是启动了一个进程，该进程会启动一个“主线程”，然后主线程调用类的main方法，所以main方法运行在主线程中，在此之前的所有程序都是单线程的。

### 2.2JVM的启动是多线程的吗

JVM的启动是多线程的，因为它最低有两个线程启动了，主线程和垃圾回收线程，垃圾回收会清理回收无用的内存对象，例如我们new出来的对象，用完后就没有引用，就可能将会被GC回收。

### 2.3Java多线程的实现方案

由于线程是依赖进程而存在的，所以我们应该先创建一个进程出来。而进程是由操作系统创建的，所以我们应该去调用操作系统创建一个进程。

Java是不能直接调用操作系统的，所以Java没有办法直接实现多线程程序，但是Java可以调用C或者C++写好的程序来实现多线程。

因为C或者C++可以去调用系统功能创建进程，Java调用C或者C++写好的创建进程程序，然后Java再提供一些类给Java程序使用，那么就Java就可以实现多线程程序。

Java提供的类是Thread和Runnable，但是Runnable是一个接口（本质上其实也是一个类），再通过查看Java官方API，里面提供两种方式去实现多线程。

#### 2.3.1继承Thread类方式实现多线程

- 自定义一个类继承Thread类，该子类重写Run方法
- 调用该子类创建实例并用该实例启动start方法

```java

/*
 * 自定义一个类继承Thread类，该子类重写Run方法
 */
public class MyThread extends Thread {

	@Override
	public void run() {
		// 自己写代码
		// 一般来说，被线程执行的代码肯定是比较耗时的。所以我们用循环改进
		for (int x = 0; x < 200; x++) {
			System.out.println(x);
		}
	}

}

/*
 * 调用该子类创建实例并用该实例启动start方法
 */
public class MyThreadDemo {
	public static void main(String[] args) {
		// 创建线程对象
		MyThread my = new MyThread();
		// 启动线程
		my.start();
	}
}
```

#### 2.3.2获取和设置线程对象名字

- 获取继承Thread的自定义类线程对象的名字
  - `getName()`和`setName()`，获取和设置线程名称，其中通过setName给线程设置名称，源码是调用Thread的无参构造方法+setName方法
  - 通过构造方法给线程设置名字，需要自定义类提供无参构造，并且有参构造方法调用父类的有参构造方法，传进线程名字即可

- 获取main方法所在的线程对象的名字

  - 通过Thread提供的静态方法`currentThread()`先获取到正在执行的线程的对象，然后再通过线程对象getName（）获取线程名字

  ```java
  // public static native Thread currentThread(); 返回正在执行的线程对象
  public class MyThreadDemo {
  	public static void main(String[] args) {
  		System.out.println(Thread.currentThread().getName());
  	}
  }
  ```

#### 2.3.3实现Runnable接口方式实现多线程

```java
/*
 * 方式2：实现Runnable接口
 * 步骤：
 * 		A:自定义类MyRunnable实现Runnable接口
 * 		B:重写run()方法
 * 		C:创建MyRunnable类的对象
 * 		D:创建Thread类的对象，并把C步骤的对象作为构造参数传递
 */
public class MyRunnable implements Runnable {

	@Override
	public void run() {
		for (int x = 0; x < 100; x++) {
			// 由于实现接口的方式就不能直接使用Thread类的方法了,但是可以间接的使用
			System.out.println(Thread.currentThread().getName() + ":" + x);
		}
	}

}
public class MyRunnableDemo {
	public static void main(String[] args) {
		// 创建MyRunnable类的对象
		MyRunnable my = new MyRunnable();

		// 创建Thread类的对象，并把C步骤的对象作为构造参数传递
		// Thread(Runnable target)
		// Thread t1 = new Thread(my);
		// Thread t2 = new Thread(my);
		// t1.setName("林青霞");
		// t2.setName("刘意");

		// Thread(Runnable target, String name)
		Thread t1 = new Thread(my, "林青霞");
		Thread t2 = new Thread(my, "刘意");

		t1.start();
		t2.start();
	}
}
```

#### 2.3.4两种实现多线程方式比较

方式1：继承Thread类

```markdown
A：自定义类yThread继承Thread类
B：在MyThread类中重写run()。
C：创建MyThread类的对象。
D：启动线程对象。
```

方式2：实现Rumnable接口

```markdown
A：自定义类NyRunnab1e实现Runnable接口。
B：在MyRunmable里面重写run()。
C：创建MyRunnable类的对象。
D：创建Thread类的对象，并把C步骤的对象作为构造参数传递。
```

#### 2.3.5多线程实现方式问题探究

> 为什么要重写run()方法？

run()单面封装的是被线程执行的代码b

> 启动线程对象用的是哪个方法？

start()

> run()和start()方法的区别？

run()直接调用仅仅是普通方法,start()先启动线程，再由JVM调用run()方法

> 用实现Runnable接口来实现多线程有什么好处？

1. 可以避免由于Java单继承带来的局限性,接口可以实现多个。
2. 适合多个相程序的代去处理同一个资源的情况，因为实现了多个线程共享同一个实现Runnable接口的实现类成员数据。

### 2.4线程的调度和优先级

假如我们的计算机只有一个 CPU，那么 CPU 在某一个时刻只能执行一条指令，线程只有得到 CPU时间片，也就是使用权，才可以执行指令。那么Java是如何对线程进行调用的呢？

线程有两种调度模型，一种是**分时调度**模型  所有线程轮流使用 CPU 的使用权，平均分配每个线程占用 CPU 的时间片。

另一种是**抢占式调度**模型  优先让优先级高的线程使用 CPU，如果线程的优先级相同，那么会随机选择一个，优先级高的线程获取的 CPU 时间片相对多一些。

Java采用的是该调度方式，所以在多个线程同时执行的情况下，我们可以通过获取线程优先级让某个线程优先于其他线程执行，默认的优先级是5，优先级的范围是1-10。

Thread中获取和设置优先级的方法

```markdown
获取优先级： public final int getPriority(); 
设置优先级： public final void setPriority(int newPriority);
//	两个方法都被不可更改的final修饰
最小优先级： 	public final static int MIN_PRIORITY = 1;
默认优先级：  public final static int NORM_PRIORITY = 5;
最大优先级：  public final static int MAX_PRIORITY = 10;

# 线程优先级高仅仅表示线程获取的 CPU时间片的几率高，但是要在次数比较多，或者多次运行的时候才能看到比较好的效果。
```

### 2.5线程的控制

- 休眠线程，是让线程休眠一段时间

  ```java
  public static native void sleep(long millis) throws InterruptedException;
  ```

- 加入线程，是让其他线程让该线程执行完后才能抢占CPU执行去执行

  ```java
  public class ThreadJoin extends Thread {
  	@Override
  	public void run() {
  		for (int x = 0; x < 100; x++) {
  			System.out.println(getName() + ":" + x);
  		}
  	}
  }
  /*
   * public final void join():等待该线程终止。 
   * 李渊是李世民和李元霸他爹，应该先有李渊再有李世民和李元霸（执行顺序·）
   */
  public class ThreadJoinDemo {
  	public static void main(String[] args) {
  		ThreadJoin tj1 = new ThreadJoin();
  		ThreadJoin tj2 = new ThreadJoin();
  		ThreadJoin tj3 = new ThreadJoin();
  
  		tj1.setName("李渊");
  		tj2.setName("李世民");
  		tj3.setName("李元霸");
  
  		tj1.start();
  		try {
  			tj1.join();
  		} catch (InterruptedException e) {
  			e.printStackTrace();
  		}
  		
  		tj2.start();
  		tj3.start();
  	}
  }
  ```

- 礼让线程（暂停线程），是让当前正在执行的线程暂停，并执行其他线程

  ```java
  /*
   * public static void yield():暂停当前正在执行的线程对象，并执行其他线程。 
   * 让多个线程的执行更和谐，但是不能靠它保证一人一次。
   */
  public class ThreadYieldDemo {
  	public static void main(String[] args) {
  		ThreadYield ty1 = new ThreadYield();
  		ThreadYield ty2 = new ThreadYield();
  
  		ty1.setName("林青霞");
  		ty2.setName("刘意");
  
  		ty1.start();
  		ty2.start();
  	}
  }
  ```

- 后台线程（也叫守护线程）

  ```java
  public class ThreadDaemon extends Thread {
  	@Override
  	public void run() {
  		for (int x = 0; x < 100; x++) {
  			System.out.println(getName() + ":" + x);
  		}
  	}
  }
  /*
   * public final void setDaemon(boolean on):将该线程标记为守护线程或用户线程。
   * 当正在运行的线程都是守护线程时，Java 虚拟机退出。 该方法必须在启动线程前调用。 
   * 
   * 游戏：坦克大战。
   */
  public class ThreadDaemonDemo {
  	public static void main(String[] args) {
  		ThreadDaemon td1 = new ThreadDaemon();
  		ThreadDaemon td2 = new ThreadDaemon();
  
  		td1.setName("关羽");
  		td2.setName("张飞");
  
  		// 设置收获线程
  		td1.setDaemon(true);
  		td2.setDaemon(true);
  
  		td1.start();
  		td2.start();
  
  		Thread.currentThread().setName("刘备");
  		for (int x = 0; x < 5; x++) {
  			System.out.println(Thread.currentThread().getName() + ":" + x);
  		}
  	}
  }
  ```

  上述的例子中，为了比较形象地理解守护线程，将mian线程的名字设置为刘备，另外两个线程的名字改为张飞和关羽，关羽和张飞是来守护刘备打天下的，如果刘备死了，他们就没有意义也会自杀。

  上述代码执行过程：

  主线程刘备执行到第五次输出后就结束运行，那么设置为守护线程张飞和关羽是不会执行到第一百次输出的。因为mian方法所在的主线程执行完毕后，剩下的都是守护线程，那么Java虚拟机会退出执行。（守护线程不是立即结束，还有抢到CPU执行权再执行一两次再退出，反正是绝对不会输出到100）

  还有更好的案例理解就是坦克大战

  ![image-20210408011119270](https://gitee.com/codeluojay/TyproaImage/raw/master/images/image-20210408011119270.png)

- 中断线程(掌握)

```java
public class ThreadStop extends Thread {
	@Override
	public void run() {
		System.out.println("开始执行：" + new Date());

		// 我要休息10秒钟，亲，不要打扰我哦
		try {
			Thread.sleep(10000);
		} catch (InterruptedException e) {
			// e.printStackTrace();
			System.out.println("线程被终止了");
		}

		System.out.println("结束执行：" + new Date());
	}
}
/*
 * public final void stop():让线程停止，过时了，但是还可以使用。
 * public void interrupt():中断线程。 把线程的状态终止，并抛出一个InterruptedException。
 */
public class ThreadStopDemo {
	public static void main(String[] args) {
		ThreadStop ts = new ThreadStop();
		ts.start();

		// 你超过三秒不醒过来，我就干死你
		try {
			Thread.sleep(3000);
			// ts.stop();
			ts.interrupt();
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
	}
}
```

stop和interrupt的区别在于是否有程序终止掉，调用stop方法后，线程休眠会中断，但同时程序也会终止；调用interrupt方法后线程休眠也会中断同时还会抛出异常，但是程序不会终止。上面的例子如果调用interrupt会输`线程被终止了`和`结束执行`正面程序不是中断

### 2.6线程的生命周期

- 新建
- 就绪
- 运行
- 阻塞
- 死亡

![image-20210408013945325](https://gitee.com/codeluojay/TyproaImage/raw/master/images/image-20210408013945325.png)



## 3.多线程经典案例

多线程比较经典的案例有两个：卖票案例和生产者消费者案例

卖票案例又可以划分两种情况：

- 三个窗口一共卖100张票
- 三个窗口同时单独卖100张票，总共300张票

### 3.1卖票案例

#### 3.1.1Thread类实现卖票案例

```java
public class SellTicket extends Thread {

	@Override
	public void run() {
		// 定义100张票
		int tickets = 100;

		// 是为了模拟一直有票
		while (true) {
			if (tickets > 0) {
				System.out.println(getName() + "正在出售第" + (tickets--) + "张票");
			}
		}
	}
}
public class SellTicketDemo {
	public static void main(String[] args) {
		// 创建三个线程对象
		SellTicket st1 = new SellTicket();
		SellTicket st2 = new SellTicket();
		SellTicket st3 = new SellTicket();

		// 给线程对象起名字
		st1.setName("窗口1");
		st2.setName("窗口2");
		st3.setName("窗口3");

		// 启动线程
		st1.start();
		st2.start();
		st3.start();
	}
}

```

这个案例中，三个窗口每次执行Run方法，票数都是新的内存变量100，所以实现的效果是三个窗口独立卖100张票，总共卖出300张票。

如果把`int tickets = 100;`换成`private static int tickets = 100;`放在Run方法外的变成静态成员变量，那么票数将会变成共享数据，就会变成三个窗口共卖100张票的案例。

#### 3.1.2Runnable实现卖票案例

```java
public class SellTicket implements Runnable {
	// 定义100张票
	private int tickets = 100;

	@Override
	public void run() {
		while (true) {
			if (tickets > 0) {
				System.out.println(Thread.currentThread().getName() + "正在出售第"
						+ (tickets--) + "张票");
			}
		}
	}
}
/*
 * 实现Runnable接口的方式实现
 */
public class SellTicketDemo {
	public static void main(String[] args) {
		// 创建资源对象
		SellTicket st = new SellTicket();

		// 创建三个线程对象
		Thread t1 = new Thread(st, "窗口1");
		Thread t2 = new Thread(st, "窗口2");
		Thread t3 = new Thread(st, "窗口3");

		// 启动线程
		t1.start();
		t2.start();
		t3.start();
	}
}
```

这是实现三个窗口同时卖100张票的卖票案例

与Thread类实现的方式比较，实现Runnable接口的实现方式只需要创建一个实现类示例，把同一个实例作为不同Thread实例的参数传递。这种方式的好处体现在它的类成员天生就是共享数据，因为多个线程都是操作同一个实例对象的成员数据。

你可以看出`private int tickets = 100`在Runnable接口中可以实现数据共享，而在Thread类的实现方式中，需要加上`static`关键字修饰。

这便又可以加深一步对Runnable接口的理解：如果是操作同一个数据，那么应该优先选择实现Runnable接口的方式实现多线程。

### 3.2改进的卖票案例

我们前面讲解过电影院售票程序，从表面上看不出什么问题，但是在真实生活中，售票时网络是不能实时传输的，总是存在延迟的情况，所以，在出售一张票以后，需要一点时间的延迟

改进实现接口方式的卖票程序，要求每次卖票延迟100毫秒

```java
public class SellTicket implements Runnable {
    // 定义100张票
    private int tickets = 100;
    @Override
    public void run() {
        while (true) {
            // t1,t2,t3三个线程
            // 这一次的tickets = 1;
            if (tickets > 0) {
                // 为了模拟更真实的场景，我们稍作休息
                try {
                    Thread.sleep(100); //延迟100毫秒
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }

                System.out.println(Thread.currentThread().getName() + "正在出售第"
                        + (tickets--) + "张票");
            }
        }
    }
}
public class SellTicketDemo {
    public static void main(String[] args) {
        // 创建资源对象
        SellTicket st = new SellTicket();

        // 创建三个线程对象
        Thread t1 = new Thread(st, "窗口1");
        Thread t2 = new Thread(st, "窗口2");
        Thread t3 = new Thread(st, "窗口3");

        // 启动线程
        t1.start();
        t2.start();
        t3.start();
    }
}
```

出现的问题1：卖出负票，超卖现象

```markdown
窗口1正在出售第0张票
窗口2正在出售第-1张票
窗口3正在出售第-2张票
```

出现的问题2：同时卖出同一张票，同票多卖现象

```markdown
窗口1正在出售第100张票
窗口2正在出售第100张票
窗口3正在出售第100张票
```

### 3.3卖票案例出现问题分析

#### 3.3.1同票多卖现象问题分析

```java
public void run()(
	while(true){
    //t1，t2，t3三个线程
    //这一次的tickets=100;
    if(tickets>0){
    	//为了硬拟更真实的场景，我们稍作休息
    try{
        Thread.sleep(100);//t1稍作休息，t2也稍作休息，t3也稍作休息
    }catch(InterruptedException e){
		e.pxintStackTrace();
        system.out.printin(Thread.currentThread().getName()+"正在出售第”
+(tickets--)+"张票");
    }
//	理想状态：
//	窗口1正在出苦第100张票
//	窗口2正在出售第99张票
//	窗口2正在出售第98张票

//	但是呢？
//	CPU的每一次执行必须是一个原子性(最简单基本的)的操作。
//	先记录以前的值
//	接着把ticket--
//	然后输出以前的值(t2来了)//ticket的就变成了99

//	窗口1正在出告第100张票
//	窗口2正在出售第100张票
//	窗口3正在出售第100张票
}
```

理想状态下，多个线程按照顺序把票卖出，但是由于多线程需要争抢CPU执行权来获取更多时间分片来执行以及多线程执行的内容是多步操作，导致同票多卖现象。

如何理解呢？t1,t2,t3在一开始时刻，都不同时间抢到执行权进入`tickets>0`的代码块中，并且又同时在`Thread.sleep(100)`处休眠，然后t1休眠完后,继续执行输出代码块

```java
 system.out.printin(Thread.currentThread().getName()+"正在出售第”
+(tickets--)+"张票");
```

这里其实可以分为三个步骤：

1. 记录ticket的值
2. 输出ticket的值
3. 进行自减操作

而恰巧在t1执行完1,2步骤后这一刻，t2抢到执行权，也同时执行1,2步骤，又刚刚好这一刻，t3抢到执行权，也同时执行1,2步骤。所以就出现同票多卖现象

#### 3.3.2卖出负票现象分析

```java
@Override
public void run() {
    while (true) {
        // t1,t2,t3三个线程
        // 这一次的tickets = 1;
        if (tickets > 0) {
            // 为了模拟更真实的场景，我们稍作休息
            try {
                Thread.sleep(100); //t1进来了并休息，t2进来了并休息，t3进来了并休息，
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            System.out.println(Thread.currentThread().getName() + "正在出售第"
                               + (tickets--) + "张票");
            //窗口1正在出售第1张票,tickets=0
            //窗口2正在出售第0张票,tickets=-1
            //窗口3正在出售第-1张票,tickets=-2
        }
    }
}
```

在`tickets = 1`时刻，t1,t2,t3在不同时间抢到执行权进入`tickets>0`的代码块中，并且又同时在`Thread.sleep(100)`处休眠。

然后t1执行完1,2,3步骤后即`ticket=0`后，t2才抢到CPU执行权并执行完1,2,3步骤即`ticket=-1`后。t3才抢到执行权，执行完1,2,3步骤，最终`ticket=-2`。

出现负票的问题是因为CPU执行权是随机分配的，并且还可能有一定的延迟性。也就是t1完整执行1,2,3步骤后，其他线程才抢到执行权，导致延迟问题。

> 对于加入延迟后卖票问题出现的小结：
>
> - 相同的票卖了多次
>
>   是CPU的一次操作不是原子性的导致的
>
>  - B:出现了负数票
>
>    是CPU执行权随机性和CPU执行延迟导致的

## 4.线程安全问题

### 4.1什么是线程安全问题

按照我自己的理解，如果一段相同程序在多线程环境下运行多次，每次程序运行的结果都出现不一样的效果，那么这段程序就会有线程安全问题。

比如上面说的卖票的案例，在多线程环境下运行两次，会出现卖负数票和同票三个窗口同时卖出的问题，这就是很典型的存在线程安全问题的程序。

### 4.2什么条件导致程序产生线程安全问题

线程安全问题是需要在特定的条件下才会产生，而且这些条件也是以后我们判断一个程序是否会有线程安全问题的标准。

- A:程序在多线程环境运行
- B:程序中有共享数据
- C:程序有多条语句操作共享数据

那么我们可以来分析一下卖票案例的是否符合这些条件

```markdown
A:是否是多线程环境  -->开了三个线程模拟
B:是否有共享数据	 -->100张票是共享数据
C:是否有多条语句操作共享数据	---> ticket-- 两条语句操作共享数据
```

### 4.3如何解决线程安全问题

理论上来讲，只要把满足上述产生线程安全问题的条件问题破环其中一个就不会产生线程安全问题，但是以卖票为例，破坏A和B的条件是不能改变的，那么我们只好从C条件入手去改变。

如何改变C条件呢？我们可以把多条语句操作共享数据的代码给包成一个整体，让某个线程在执行的时候，别人不能来执行，那么程序执行整体时就像执行一条语句，那么就不会存在多条语句操作共享数据问题

问题是我们不知道怎么包啊?其实我也不知道，但是Java给我们提供了：**同步机制**

同步机制就是线程同步解决线程安全问题，比较官方的说法就是**把多个语句操作共享数据的代码给锁起来，让任意时刻只能有一个线程执行即可**

### 4.4线程同步机制实现

#### 4.4.1同步代码块实现线程同步机制

同步代码块格式

```java
synchronized(对象){
	//	需要同步的代码;
}
```

使用同步代码块需要考虑两个问题

```markdown
A:对象是什么呢?
	我们可以随便创建一个对象试试。
B:需要同步的代码是哪些呢?
	把多条语句操作共享数据的代码的部分给包起来
注意：
    同步可以解决安全问题的根本原因就在那个对象上。该对象如同锁的功能。
    多个线程必须是同一把锁。
```

同步代码块

```java
public class SellTicket implements Runnable {
	// 定义100张票
	private int tickets = 100;
	//创建锁对象
	private Object obj = new Object();

//		注释掉的代码不是共用同一个锁对象的例子	
//    @Override
//	public void run() {
//		while (true) {
//			synchronized(new Object()){
//				if (tickets > 0) {
//					try {
//						Thread.sleep(100); 
//					} catch (InterruptedException e) {
//						e.printStackTrace();
//					}
//					System.out.println(Thread.currentThread().getName() + "正在出售第"
//							+ (tickets--) + "张票");
//				}
//			}
//		}
//	}
	
    //	共用同一个锁对象的例子
	@Override
	public void run() {
		while (true) {
			synchronized (obj) {
				if (tickets > 0) {
					try {
						Thread.sleep(100);
					} catch (InterruptedException e) {
						e.printStackTrace();
					}
					System.out.println(Thread.currentThread().getName()
							+ "正在出售第" + (tickets--) + "张票");
				}
			}
		}
	}
}
```

上述的同步代码块中，虽然可以指定任意对象作为锁，但是要注意多个线程要共用一个锁对象才能起到线程同步的作用，就好像三个人上厕所，必须厕所是同一把锁锁住厕所门，后面的人才不能进入，否则谁可以打开厕所门那就尴尬了。

上述注释掉的代码就是多个线程用不同的对象作为锁，所以还是会出现线程安全问题。在类的内部定义成员变量`private Object obj = new Object();`，同时`synchronized`使用同一个对象的引用`obj`，那么同一对象做为锁，就可以保证线程安全问题。

#### 4.4.2同步方法实现线程同步机制

在引入同步方法之前，不妨来看一下两段重复的同步代码块会不会出问题的

```java
public class SellTicket implements Runnable {
    private int ticket = 100;
    private Object object = new Object();
    private int x;

    @Override
    public void run() {
        while (true) {
            // ticket = 2;
            if(x%2==0){
                //  t1,t2,t3  争抢之处一
                synchronized (object) {
                    if (ticket > 0) {
                        try {
                            //   t1休眠
                            Thread.sleep(100);
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                        System.out.println(Thread.currentThread().getName() + ":正在出售第" + ticket-- + "张票");
                    }
                }
            }else{
                //  t1,t2,t3  争抢之处二
                synchronized (object) {
                    if (ticket > 0) {
                        try {
                            Thread.sleep(100);
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                        System.out.println(Thread.currentThread().getName() + ":正在出售第" + ticket-- + "张票");
                    }
                }
            }
            x++;
        }
    }
}
```

这个例子和上面一样共用`SellTicketDemo`,运行后是不会出问题的。虽然加入`if-else`和另外一个共享数据`private int x`来影响视线。但是思考点都是不变的，可以思考`t1,t2,t3`可以同时争抢出现的位置，无非都是`synchronized`两处，但是无论哪个线程去争抢，两个同步代码块都是用同一个锁，相当于谁抢到对象就等同于谁抢到钥匙，但是只能开其中分支一个同步代码块的锁，另外分支的一个同步代码块是不能同时开的。

而刚好卖票的三个执行步骤所在同步代码块内，可以保证被线程一次性完整执行。所以上述的卖票程序是不会出现问题的。而置于会不会有多个线程去操作共享数据`x`，这个其实也很好理解。

两个分支的同步代码块只有一个线程能执行完并释放锁，才会到达`x`的位置，假设是`t1`到达`x`位置时,`t2,t3`线程还在争抢`t1`释放的锁，所以顺序上不会同时操作x

上述有重复的代码内容，无疑可以抽离出独立成一个方法，供多次调用。

```java
	//	将分支代码抽离出来
    private void sellTicket() {
        synchronized (object) {
            if (ticket > 0) {
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(Thread.currentThread().getName() + ":正在出售第" + ticket-- + "张票");
            }
        }
    }
```

抽离后的代码

```java
public class SellTicket implements Runnable {
    private int ticket = 100;
    private Object object = new Object();
    private int x;
    //  代码优化
    @Override
    public void run() {
        while (true) {
            // ticket = 2;
            if(x%2==0){
                synchronized (object) {
                    if (ticket > 0) {
                        try {
                            //   t1休眠
                            Thread.sleep(100);
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                        System.out.println(Thread.currentThread().getName() + ":正在出售第" + ticket-- + "张票");
                    }
                }
            }else{
                sellTicket();
            }
            x++;    
        }

    }
    //  如果一个方法一进来就看到同步修饰代码块，那么就可以考虑用同步代码块,同步代码块没有对象
    private  void sellTicket() {
		synchronized{
            if (ticket > 0) {
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(Thread.currentThread().getName() + ":正在出售第" + ticket-- + "张票");
            }
		}
    }
}
```

然后运行程序，发现也是没有问题的。如果一个方法一进来就看到同步修饰代码块，那么就可以考虑将`synchronized`放在方法上修饰方法，变成同步方法`private synchronized void sellTicket()`。在方法上我们时加不了`object`对象的，那么这样运行是不是会出错呢？

很明显会出错，因为对象内部的方法都有一个隐藏的对象`this`，它是指向当前对象。而上面的代码块还是用`object`，两个不同对象当然是会出现问题。

那么只要我们把同步代码块的对象也改成`this`，运行程序应该就没有问题了，所以可以优化成下面的代码

```java
public class SellTicket implements Runnable {
    private int ticket = 100;
    private Object object = new Object();
    private int x;
    
    @Override
    public void run() {
        while (true) {
            // ticket = 2;
            if(x%2==0){
                synchronized (this) {
                    if (ticket > 0) {
                        try {
                            //   t1休眠
                            Thread.sleep(100);
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                        System.out.println(Thread.currentThread().getName() + ":正在出售第" + ticket-- + "张票");
                    }
                }
            }else{
                sellTicket();
            }
            x++;
        }

    }
    //  如果一个方法一进来就看到同步修饰代码块，那么就可以考虑用同步代码块,同步代码块没有对象
    private synchronized void sellTicket() {

            if (ticket > 0) {
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(Thread.currentThread().getName() + ":正在出售第" + ticket-- + "张票");
            }

    }
}
```

这样一运行程序，结果是没有出现问题的，就证明了同步方法用的锁对象是当前对象`this`

除此之外，还可以再优化为静态同步方法`private static synchronized void sellTicket()`,但是静态同步方法的锁对象是谁呢？谁能保证锁对象是唯一的呢？

答案就是当前类的字节码对象，因为类在被加载的时候，都会生成一个`Class`对象，这个对象是唯一的。

而静态方法的默认锁对象就是当前类的静态字节码文件对象，如`SellTicket`就对应是`SellTicket.class`

所以优化后的代码应该如下

```java
public class SellTicket implements Runnable{
    private static int tickets = 100;
    private int x;

    @Override
    public void run() {
        while (true){
            if (x%2==0) {
                synchronized(this){
                    if (tickets>0){
                        try {
                            Thread.sleep(100);
                            System.out.println(Thread.currentThread().getName()+"正在出售第"+tickets--+"票");
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                    }
                }
                x++;
            }else{
                    sellTicket();
                    x++;

            }
        }

    }
    private static synchronized void sellTicket(){
        if (tickets>0) {
            try {
                Thread.sleep(100);
                System.out.println(Thread.currentThread().getName() + "正在出售第" + tickets-- + "票");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

> 同步内容小结：
>
> 同步代码块解决线程安全问题：
>
> ​		锁对象是任意对象，但这个任意对象必须被多个线程必须唯一
>
> 同步方法解决线程安全问题：
>
> ​		锁对象是当前对象this
>
> 静态同步方法解决线程安全问题：
>
> ​		锁对象是当前对象的字节码文件
>
> 另外：条件判断语句一定要被同步包含，因为线程就像人一样，永远会处在离同步最近的地方
>
> 也就是synchronized的一语句，如果不包含条件判断，则都会拼命统一涌进条件语句块内
>
> 也就是很容易出现负票和同票现象

#### 4.4.3同步的特点

- 同步的前提：
  多个线程



- 同步解决问题的时候要注意：
  多个线程使用的是同一个锁对象。



- 同步的好处
  同步的出现解决了多线程的安全问题。



- 同步的弊端
  当线程相当多时，因为每个线程都会去判断同步上的锁这是很耗费资源，无形中会降低程序的运行效率。

  如果线程同步处理不恰当，很容易产生死锁问题。

#### 4.4.4相关线程安全类的源码分析

`java.util`和`java.lang`下有一些线程安全的类如`StringBuffer`,`Vector`,`Hashtable`，为何它们都是线程安全的呢？

Hashtable部分源码

![image-20210415005612073](https://gitee.com/codeluojay/TyproaImage/raw/master/images/image-20210415005612073.png)

StringBuffer部分源码

![image-20210415005709763](https://gitee.com/codeluojay/TyproaImage/raw/master/images/image-20210415005709763.png)

Vector部分源码

![image-20210415005817476](https://gitee.com/codeluojay/TyproaImage/raw/master/images/image-20210415005817476.png)

不难看出，它们都是有`synchronized`修饰的，也就是说它们默认是使用同步的方法保证线程安全。

#### 4.4.5如何使用线程安全集合

即使有线程安全的类，我们在实际工作中也很少用这些线程安全的类。因为执行效率真的太慢了

![image-20210415011731336](https://gitee.com/codeluojay/TyproaImage/raw/master/images/image-20210415011731336.png)

在`java.util`下有一个集合工具类，这个类提供许多能够让我们线程安全地使用集合，并且执行效率会比较快的一些类，比如`SynchronizedList`,`SynchronizedMap`,`SynchronizedSortedMap`等等

举个简单的例子

```java
        List<String> strings = new ArrayList<>();
        List<String> stringList = Collections.synchronizedList(strings);
```

#### 4.4.6Lock和ReetrantLock

虽然我们可以理解同步代码块和同步方法的锁对象问题，但是我们并没有直接看到在哪里加上了锁，在哪里释放了锁，为了更清晰的表达如何加锁和释放锁,JDK5以后提供了一个新的锁对象Lock。

Lock是一个接口，里面核心的方法是`lock()`和`unLock()`，分别代表的是加锁和解锁。在使用的接口的方法的时候，一般用它的实现类的`lock()`和`unLock()`,`ReetrantLock`是`Lock`的实现类，里面实现加锁和解锁的方法，所以可以通过这两个方法去改造卖票案例。

```java
public class SellTicket implements Runnable {
    private int tickets = 100;
    private ReentrantLock lock = new ReentrantLock();
    @Override
    public void run() {
        while(true){
            try {
                //  显示加锁
                lock.lock();
                if(tickets>0){
                    Thread.sleep(100);
                    int i = 0;
                    int result = 100/i;
                    System.out.println(Thread.currentThread().getName()+"正在出售第"+tickets--+"张票");
                }
            } catch (Exception e) {
                e.printStackTrace();
                tickets--;
            } finally {
                // 显式解锁
                lock.unlock();
            }

        }
    }
}
```

在上面的案例中，除了创建一个`ReetrantLock`和加上`lock()`以及`unlock()`之外，还用到`try-catch-finally`去释放锁对象，因为`ReetrantLock`中的锁一旦单个线程发生异常，是不会自动释放锁对象，所以我们需要捕获异常，并最终释放锁对象，才能让其他线程获取到锁继续执行

## 5.多线程死锁问题

 死锁：

 * 		两个或两个以上的线程在争夺资源的过程中，发生的一种相互等待的现象。

死锁举例：

中国人，美国人吃饭案例。
 * 		正常情况：中国人需要筷子两支，才能夹菜吃东西，美国人需要刀和叉才能切牛排吃东西
 * 		现在情况：中国人手上持有筷子一支，刀一把，美国人持有筷子一支，叉一把

双方都想先吃上东西，都互相持有对方的想吃东西的工具资源，彼此不想让出手上的东西，导致相互等待

下面就用代码来实现一下死锁的问题

```java
public class MyLock {
    //  创建两把锁
    public static final Object objA = new Object();
    public static final Object objB = new Object();
}
```

```java
public class DieLock extends Thread {
    private boolean flag;

    public DieLock() {

    }

    public DieLock(boolean flag) {
        this.flag = flag;
    }

    @Override
    public void run() {
        if (flag) {
            synchronized (MyLock.objA) {
                System.out.println("if objA");
                synchronized (MyLock.objB) {
                    System.out.println("i have MyLock.objB");
                }
            }
        } else {
            synchronized (MyLock.objB) {
                System.out.println("else objB");
                synchronized (MyLock.objA) {
                    System.out.println("i have MyLock.objA");
                }
            }
        }
    }
}
```

```java
public class DieLockDemo {
    public static void main(String[] args) {
        DieLock t1 = new DieLock(true);
        DieLock t2 = new DieLock(false);

        t1.setName("thread1");
        t2.setName("thread2");
        t1.start();
        t2.start();

    }
}
```

代码分析：`MyLock`创建静态不可变两个对象，`static`是为了方便用锁对象，`final`是为了保证锁对象不能被更改。`DieLock`类中继承`Thread`类，重写`Run()`方法，并在`if-else`分支编写同步嵌套。

按照理想状态：两个线程都依次获取对象A和对象B的锁，输出完整`if`分支代码块内容再输出`else`分支代码块内容，又或者输出完整`else`分支代码块内容再输出`if`分支代码块内容

```markdown
# 完整if-else的执行语句
if objA
i have MyLock.objB
else objB
i have MyLock.objA
# 完整else-if的执行语句
else objB
i have MyLock.objA
if objA
i have MyLock.objB
```

但是在多次执行过程中，还会出现以下死锁状况

```markdown
# 线程t2先抢到CPU执行权和对象B的锁，进入else分支，接着线程t1抢到CPU执行权和线程A的锁,进入if分支
else objB
if objA
// 缺少输出i hava MyLock...语句
# 线程t1先抢到CPU执行权和对象A的锁，进入if分支，接着线程t1抢到CPU执行权和线程A的锁，进入else分支
if objA
else objB
```

这就是死锁现象。

## 6.线程间的通信

### 6.1什么是生产者和消费者模型问题？

举个例子：早餐店卖包子，顾客来买一个包子，商家就生产一个包子，顾客和商家都基于同一个包子来进行交易,那么顾客就可以被称为消费者，商家可以被称为生产者，包子就是共同的资源。

那么在多线程方面来解释的上面的现象就是：有一个消费者线程、一个生产者线程，还有两个线程共同操作同一个的资源的模型。

![image-20210417143313998](https://gitee.com/codeluojay/TyproaImage/raw/master/images/image-20210417143313998.png)

### 6.2如何用多线程实现生产者和消费者的模型呢？

实现生产者和消费者模型的模型拆解就是：

- 必须是生产者生产出一个包子，消费者就消费一个包子。
- 如果生产者没有生产包子，消费者只能一直等待生产者生产出来才能消费。
- 如果消费者没有消费完包子，生产者只能一直等待消费者消费完出来才能生产。

### 6.3Java实现多线程的生产者和消费者模型

#### 6.3.1代码初步实现生产者和消费者模型

以学生类为共同资源，初步实现Java生产者和消费者模型。

思路：

- 资源类：Student

 * 		设置学生数据:SetThread(生产者) 生产一个学生
 * 		获取学生数据：GetThread(消费者) 消费一个学生
 * 		测试类:StudentDemo

```java
// 生产者:生产一个学生
public class SetThread implements Runnable {

	@Override
	public void run() {
		Student s = new Student();
		s.name = "林青霞";
		s.age = 27;
	}
}
```

```java
// 消费者:消费一个学生
public class GetThread implements Runnable {
	@Override
	public void run() {
		Student s = new Student();
		System.out.println(s.name + "---" + s.age);
	}

}
```

```java
public class Student {
	String name;
	int age;
}
```

```java
public class StudentDemo {
	public static void main(String[] args) {
		//创建资源
		Student s = new Student();
		
		//设置和获取的类
		SetThread st = new SetThread(s);
		GetThread gt = new GetThread(s);

		//线程类
		Thread t1 = new Thread(st);
		Thread t2 = new Thread(gt);

		//启动线程
		t1.start();
		t2.start();
	}
}
```

#### 6.3.2代码初步实现产生的问题

这就是生产者和消费者的初步编程思路实现，但是运行起来会有问题，控制台会打印输出

```markdown
# 输出打印为学生类对象的名字和年龄为null和0
null-----0
# 问题分析
`SetThread` 和 `GetThread`重写的`run`方法中都是新建的对象，不是同一个对象
导致消费者只会消费自己新建的对象，没有经过生产者赋值操作的流程
```

那有没有什么办法保证让生产者和消费者之间都用同一个的学生对象？

答案：在外界把这个数据创建出来，通过构造方法传递给其他的类。

#### 6.3.3代码初步实现改进后产生的线程安全问题

那么将对上面的例子的`SetThread`和`GetThread`进行改造一下，其余保持不变。

```java
// 生产者改造，去掉run方法中的新建对象，提供构造方法和成员变量
public class SetThread implements Runnable {

	private Student s;

	public SetThread(Student s) {
		this.s = s;
	}

	@Override
	public void run() {
		// Student s = new Student();
		s.name = "林青霞";
		s.age = 27;
	}

}
```

```java
public class GetThread implements Runnable {
	private Student s;

	public GetThread(Student s) {
		this.s = s;
	}

	@Override
	public void run() {
		// Student s = new Student();
		System.out.println(s.name + "---" + s.age);
	}

}
```

经过上面的改造，多次运行程序，会发现有一两次是出现

```markdown
# 问题1
林青霞---0
# 问题2
null---0
# 原因分析
问题1：t1先抢到CPU执行权，设置名字为林青霞后，t2抢到CPU执行权，对学生信息输出，此时年龄为默认值0
问题2：t2先抢到执行权，直接输出学生信息，此时学生还没赋值，默认名字为null，年龄为0
```

#### 6.3.4代码初步实现产生的线程安全问题放大

这其实就是转化为之前线程安全问题，为了更加放大这个问题，又对程序作出如下改进，对`Run`方法进行

```java
//  生产者：设置循环赋值，同时两个分支，设置不同赋值语句
public class SetThread implements Runnable {

	private Student s;
	private int x = 0;

	public SetThread(Student s) {
		this.s = s;
	}

	@Override
	public void run() {
		while (true) {
				if (x % 2 == 0) {
					s.name = "林青霞";//刚走到这里，就被别人抢到了执行权
					s.age = 27;
				} else {
					s.name = "刘意"; //刚走到这里，就被别人抢到了执行权
					s.age = 30;
				}
				x++;
		}
	}
}
```

```java
// 消费者：与生产者同步设置循环取值，生产者循环生产多个，消费者也循环消费多个
public class GetThread implements Runnable {
	private Student s;

	public GetThread(Student s) {
		this.s = s;
	}

	@Override
	public void run() {
		while (true) {
				System.out.println(s.name + "---" + s.age);
		}
	}
}
```

再次出现线程安全问题，并且放大该问题，问题及分析如下

```markdown
# 问题描述
A:同一个数据出现多次
B:姓名和年龄不匹配
# 有问题的输出
刘意---27
刘意---27
刘意---27
林青霞---27
# 问题的分析
A:同一个数据出现多次
	CPU的一点点时间片的执行权，就足够你执行很多次。
B:姓名和年龄不匹配
	线程运行的随机性
# 问题的解决方案
加锁。
 		注意：
 		A:不同种类的线程都要加锁。
		B:不同种类的线程加的锁必须是同一把。
```

#### 6.3.5解决代码初步实现产生的线程安全问题

只需要采用同一个对象作为锁，使用同步代码块改造一下，即可以解决线程安全问题，对`run`方法改造一下

```java
	// 生产者 SetThread
	@Override
	public void run() {
		while (true) {
			synchronized (s) {
				if (x % 2 == 0) {
					s.name = "林青霞";//刚走到这里，就被别人抢到了执行权
					s.age = 27;
				} else {
					s.name = "刘意"; //刚走到这里，就被别人抢到了执行权
					s.age = 30;
				}
				x++;
			}
		}
	}
```

```java
	// 消费者 GetThread
	@Override
	public void run() {
		while (true) {
			synchronized (s) {
				System.out.println(s.name + "---" + s.age);
			}
		}
	}
```

### 6.4生产者消费者之等待唤醒机制图解

虽然上面的例子解决的线程安全问题（数据出现名字和年龄不匹配的问题），但是还是没有达到预期的效果，

生产者生产一个，消费者就消费一个。所以Java提供唤醒等待机制来帮助实现生产者消费者模型。

也就是说：虽然数据安全了，但是呢，一次一大片不好看，我就想依次的一次一个输出。

那应该怎么实现呢？通过Java提供的等待唤醒机制解决。

Java中的等待唤醒是通过Object类中提供了三个方法来实现的
 * 			wait():线程等待
 * 			notify():唤醒单个线程
 * 			notifyAll():唤醒所有线程

> 为什么这些方法不定义在Thread类中呢?

 * 			这些方法的调用必须通过锁对象调用，而我们刚才使用的锁对象是任意锁对象。
 * 			所以，这些方法必须定义在Object类中。

![image-20210417155502988](https://gitee.com/codeluojay/TyproaImage/raw/master/images/image-20210417155502988.png)

#### 6.4.1生产者消费者之等待唤醒机制代码实现

```java
// 资源实体类 学生 加入默认的一个变量标识资源有无被更改过
public class Student {
	String name;
	int age;
	boolean flag; // 默认情况是没有数据，如果是true，说明有数据
}
```

```java
//	生产者线程 
public class SetThread implements Runnable {

	private Student s;
	private int x = 0;

	public SetThread(Student s) {
		this.s = s;
	}

	@Override
	public void run() {
		while (true) {
			synchronized (s) {
				//判断有没有
				if(s.flag){
					try {
						s.wait(); //t1等着，释放锁
					} catch (InterruptedException e) {
						e.printStackTrace();
					}
				}
				
				if (x % 2 == 0) {
					s.name = "林青霞";
					s.age = 27;
				} else {
					s.name = "刘意";
					s.age = 30;
				}
				x++; //x=1
				
				//修改标记
				s.flag = true;
				//唤醒线程
				s.notify(); //唤醒t2,唤醒并不表示你立马可以执行，必须还得抢CPU的执行权。
			}
			//t1有，或者t2有
		}
	}
}
```

```java
// 消费者线程
public class GetThread implements Runnable {
	private Student s;

	public GetThread(Student s) {
		this.s = s;
	}

	@Override
	public void run() {
		while (true) {
			synchronized (s) {
				if(!s.flag){
					try {
						s.wait(); //t2就等待了。立即释放锁。将来醒过来的时候，是从这里醒过来的时候
					} catch (InterruptedException e) {
						e.printStackTrace();
					}
				}
				
				System.out.println(s.name + "---" + s.age);
				//林青霞---27
				//刘意---30
				
				//修改标记
				s.flag = false;
				//唤醒线程
				s.notify(); //唤醒t1
			}
		}
	}
}
```

```java
// 测试类
public class StudentDemo {
	public static void main(String[] args) {
		//创建资源
		Student s = new Student();
		
		//设置和获取的类
		SetThread st = new SetThread(s);
		GetThread gt = new GetThread(s);

		//线程类
		Thread t1 = new Thread(st);
		Thread t2 = new Thread(gt);

		//启动线程
		t1.start();
		t2.start();
	}
}
```

#### 6.4.2生产者消费者之等待唤醒机制代码分析

唤醒等待机制代码思路：

- 正常的思路：
  - A：生产者
    先看是否有数据，有就等待；没有就生产，生产完之后通知消费者来消费数据
  - B：消费者
    先看是否有数据，有就消费；没有就等待，通知生产者生产数据

- 代码思路分析：
  1. 在资源类中`Student`中添加`boolean flag`标识位，默认是`false`没有数据，如果是true，说明有数据
  2. 线程的执行顺序存在随机性，这里以消费者`t2`优先抢到CPU执行权为例，讲解执行流程
  3. `t2`抢到执行权，进入到`synchronized (s)`代码块内部，判断`if(!s.flag)`，`flag`代表资源对象是否有数据，默认值为false，取反是`true`，证明消费者第一次没有数据消费，执行`wait`
  4. 当消费者调用`wait`方法后，会立即释放当前锁对象，并进入等待阻塞状态，下次被唤醒，将会在`wait`所在的代码行开始执行
  5. 消费者`t2`阻塞后，生产者`t1`可以抢到CPU执行权，进入到`synchronized (s)`代码块内部，判断`if(s.flag)`，`flag`代表资源对象是否有数据，默认值为false，不进去`if`分支
  6. 生产者`t1`往下执行，执行资源对象`name`和`age`赋值及`x++`后即完成生产对象，执行完`s.flag`后资源状态改变，代表 生产对象完毕，唤醒消费者线程`t1`
  7. 接着两个线程又可以同时开始争抢到线程，如果消费者`t1`抢到，则会去消费资源对象，然后再进入阻塞状态，如果生产者`t2`再次抢到，则会进入到`if(s.flag)`分支，调用`wait`方法后，并进入等待阻塞状态。

#### 6.4.3线程的转换图及常见的执行情况

前面我们学习了线程的的生命周期，现在可以来回顾一下，并且又线程生命周期再进一步细化线程状态转换和执行情况。

![image-20210418163239814](https://gitee.com/codeluojay/TyproaImage/raw/master/images/image-20210418163239814.png)

##### 线程生命周期

- 新建
- 就绪
- 运行
- 阻塞
- 死亡

##### 线程状态转换

1. 新建--就绪--运行--死亡
2. 新建--就绪--运行--就绪--运行--死亡
3. 新建--就绪--运行--其他阻塞--就绪--运行--死亡
4. 新建--就绪--运行--同步阻塞--就绪--运行--死亡
5. 新建-就绪--运行---等待阻塞--同步阻塞--就绪--运行--死亡

### 6.5线程组的概述和使用

线程组： 把多个线程组合到一起。它可以对一批线程进行分类管理，Java允许程序直接对线程组进行控制。

#### 6.5.1线程组方法概述

- `public final String getName()`获取线程组名字
- `public final ThreadGroup getThreadGroup()`获取线程组
- `ThreadGroup(String name)`设置线程组名字

#### 6.5.2线程组方法使用代码

```java
// 实现Runnable的线程
public class MyRunnable implements Runnable{
    @Override
    public void run() {
        for (int x = 0; x < 100; x++) {
            System.out.println(Thread.currentThread().getName() + ":" + x);
        }
    }
}
```

```java
// 线程组方法具体使用
public class ThreadGroupDemo {
    public static void main(String[] args) {
        // method1();

        // 我们如何修改线程所在的组呢?
        // 创建一个线程组
        // 创建其他线程的时候，把其他线程的组指定为我们自己新建线程组
        method2();

        // t1.start();
        // t2.start();
    }
    private static void method1() {
        MyRunnable my = new MyRunnable();
        Thread t1 = new Thread(my, "林青霞");
        Thread t2 = new Thread(my, "刘意");

        // 我不知道他们属于那个线程组,我想知道，怎么办
        // 线程类里面的方法：public final ThreadGroup getThreadGroup()
        ThreadGroup tg1 = t1.getThreadGroup();
        ThreadGroup tg2 = t2.getThreadGroup();

        // 线程组里面的方法：public final String getName()
        String name1 = tg1.getName();
        String name2 = tg2.getName();
        System.out.println(name1);
        System.out.println(name2);

        // 通过结果我们知道了：线程默认情况下属于main线程组
        // 通过下面的测试，你应该能够看到，默任情况下，所有的线程都属于同一个组
        System.out.println(Thread.currentThread().getThreadGroup().getName());
    }
    private static void method2() {
        // ThreadGroup(String name)
        ThreadGroup tg = new ThreadGroup("这是一个新的组");

        MyRunnable my = new MyRunnable();
        // Thread(ThreadGroup group, Runnable target, String name)
        Thread t1 = new Thread(tg, my, "林青霞");
        Thread t2 = new Thread(tg, my, "刘意");

        System.out.println(t1.getThreadGroup().getName());
        System.out.println(t2.getThreadGroup().getName());

        //通过组名称设置后台线程，表示该组的线程都是后台线程
        tg.setDaemon(true);
    }
}
```

#### 6.5.3线程组方法使用代码分析

在创建线程时候如何获取线程所在的线程组？

- 通过线程类里面的方法`ThreadGroup getThreadGroup()`获取线程组对象，再调用 线程组里面的方法` getName()`

- 线程默认情况下新创建的线程属于main线程组，除非我们给线程设定某个分组

如何给线程设置一个线程组

1. 通过构造方法 `ThreadGroup tg = new ThreadGroup("这是一个新的组");`新建一个线程组
2. 通过制定线程组的构造方法`Thread t1 = new Thread(tg, my, "林青霞");`创建线程，线程组作为参数传递到创建线程的方法中设置

#### 6.5.4使用线程组有什么好处

1. 可以统一将线程组内的线程设置为守护线程（即后台线程），通过`ublic final void setDaemon(boolean daemon)`设置
2. 可以统一销毁线程组内的线程，通过`public final void destroy()`销毁

> 好处：对线程组内的线程批量处理，如批量设置优先级，批量设置成后台线程，批量销毁等等

### 6.6生产者消费者之等待唤醒机制代码优化

之前的线程代码中，资源类实体`Student`的成员属性是默认的属性，当前的包下都可以访问到，很容易被串改。一般在设置`JavaBean`的时候我们都设置属性私有，然后通过提供实例调用的方式才能改变值才会相对安全一

些。所以要对上面的唤醒机制代码进行优化。

```java
// 设置成员属性为私有属性，提供get和set方法
public class Student {
    private String name;
    private int age;
    //  设置一个资源的标识位置，当有资源时用来唤醒线程 true-有资源 false-没有资源
    private boolean flag;

    public synchronized void set(String name,int age){
        //  生产者
        if (this.flag){
            try {
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        // 设置数据
        this.name = name;
        this.age = age;
        // 修改标记，唤醒线程
        this.flag = true;
        this.notify();
    }

    public synchronized void get(){
        //  消费者
        this.flag = flag;
        if (!this.flag){
            try {
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        //  模拟消费者消费对象
        System.out.println(this.name+"---"+this.age);
        //  修改标记，唤醒线程
        this.flag = false;
        this.notify();
    }
}
```

```java
//	生产者
public class SetThread implements Runnable{
    // 需要被多线程操作的同一个资源
    private Student s;
    private int x = 0;
    public SetThread(Student s){
        this.s = s;
    }
    @Override
    public void run() {
        while (true) {
            if (x%2==0) {
                s.set("林清霞",27);
            } else {
                s.set("刘意",30);
            }
            x++;
        }
    }
}
```

```java
// 消费者
public class GetThread implements Runnable{
    private Student s;
    public GetThread(Student s){
        this.s = s;
    }
    @Override
    public void run() {
        while (true)
               s.get();
        }
    }
}
```
> 小结，最终版代码中：
>
> 把Student的成员变量给私有的了。
>
> 把设置和获取的操作给封装成了功能，并加了同步。
>
> 设置或者获取的线程里面只需要调用方法即可。

### 6.7线程池的概述和使用

#### 6.7.1线程池的概述

程序启动一个新线程成本是比较高的，因为它涉及到要与操作系统进行交互。而使用**线程池**可以很好的提高性能，尤其是当程序中要创建大量生存期很短的线程时，更应该考虑使用**线程池**。

#### 6.7.2线程池的方法

JDK5新增了一个Executors工厂类来产生线程池，有如下几个方法

- public static ExecutorService newCachedThreadPool() 创建一个具有缓存功能的线程池
- public static ExecutorService newFixedThreadPool(int nThreads)创建一个可重用的，具有固定线程数的线程池
- public static ExecutorService newSingleThreadExecutor()创建一个只有单线程的线程池，相当于上个方法的参数是1

这些方法的返回值是ExecutorService对象，该对象表示一个线程池，可以执行Runnable对象或者Callable对象代表的线程。它提供了如下方法:

- Future<?> submit(Runnable task)
- <T> Future<T> submit(Callable<T> task)

Runnable对象已经学过代表的线程，但是Callable对象代表的线程

#### 6.7.3线程池使用思路

1. 创建一个线程池对象，控制要创建几个线程对象。

   public static ExecutorService newFixedThreadPool(int nThreads)

2. 这种线程池的线程可以执行：

   可以执行Runnable对象或者Callable对象代表的线程

   由于学过Runnable，所以做一个类实现Runnable接口。

3. 调用如下方法即可

   Future<?> submit(Runnable task)

   <T> Future<T> submit(Callable<T> task)

4. 可选择调用shutdown()结束多线程



```java
//	实现Runnable接口
public class MyRunnable implements Runnable {

	@Override
	public void run() {
		for (int x = 0; x < 100; x++) {
			System.out.println(Thread.currentThread().getName() + ":" + x);
		}
	}

}
```

```java
// 线程池使用
public class ExecutorsDemo {
    public static void main(String[] args) {
        // 调用线程池的newFixedThreadPool创建指定数量的线程，并返回赋值给一个ExecutorService对象
        ExecutorService executorService = Executors.newFixedThreadPool(2);
        // 调用ExecutorService对象的submit方法去执行多线程的run方法，里面需要传递一个Runnable对象
        MyRunnable myRunnable = new MyRunnable();
        executorService.submit(myRunnable);
         //  可以选择调用shutdown方法结束线程
        executorService.shutdown();
    }
}
```

#### 6.7.4线程池的好处

线程池里的每一个线程代码结束后，并不会死亡，而是再次回到线程池中成为空闲状态，等待下一个对象来使用。

### 6.8多线程方式之Callable接口实现

前面学习了`Thread`的两种实现多线程方式，分别是继承`Thread`，重写`Run`方法和实现`Runnable`接口作为参数传递给`Thread`的构造函数，同时重写`Run`方法。这两种方式都是基于`Thread`来实现的。

除了这两种方式，还可以通过`Callable`实现接口的方式，重写`call`方法之后，传递给`ExecutorService`的`submit`执行多个线程。

#### 6.8.1多线程方式之Callable接口实现思路

1. 自定义一个类`MyCallable`实现`Callable`，重写`call`方法
2. 利用线程池的工厂模式，创建线程池对象
3. 创建自定义类的实例，将实例作为参数传递到线程池对象的`submit`方法执行

#### 6.8.2多线程方式之Callable接口代码实现

```java
// 自定义类实现Callable
public class MyCallable implements Callable {
    @Override
    public Object call() throws Exception {
        for (int x = 0; x < 100; x++) {
            System.out.println(Thread.currentThread().getName() + ":" + x);
        }
        // 不需要返回值就返回空值
        return null;
    }
}
```

```java
// 测试类
public class CallableDemo {
    public static void main(String[] args) {
        // 通过线程池的静态方法创建线程池服务对象
        ExecutorService executorService = Executors.newFixedThreadPool(2);
        // 创建Callable接口实现类的实例对象
        MyCallable m1 = new MyCallable();
        MyCallable m2 = new MyCallable();
        // 调用executorService.submit方法执行传进Callable实例执行多线程
        executorService.submit(m1);
        executorService.submit(m2);
        //  关闭多线程
        executorService.shutdown();

    }
}
```

#### 6.8.3多线程方式之Callable接口实现代码分析

` Executors.newFixedThreadPool()`实际上使用工厂模式去创建线程池对象，可以进去按住Ctrl+鼠标点击看看`newFixedThreadPool()`

![image-20210418212208914](https://gitee.com/codeluojay/TyproaImage/raw/master/images/image-20210418212329167.png)

再对`ThreadPoolExecutor`的继承和实现关系图展开，便可以清楚看到对应的类和接口节点图

![image-20210418212329167](https://gitee.com/codeluojay/TyproaImage/raw/master/images/image-20210418212329167.png)

由类的节点关系图可以看出，`ThreadPoolExecutor`间接`ExecutorService`和`Executor`，所以`ExecutorService`调用`submit`方法最终还是调用`Executor`接口的`executor`方法实现。

#### 6.9多线程Callable接口实现求和案例

开两个线程分别去求1-100和1-200的累加和，打印输出。

实现思路：

1. 使用Callable接口实现类去做，在实现类中的构造方法中传递每个线程要计算的总数
2. 通过重写`call`方法返回求和的总数
3. `ExecutorService`调用`submit`时返回的是`Future`对象
4. 通过`Future`对象实例的`get`方法获取返回值打印输出

代码实现：

```java
// 求和的自定义类
public class MyCallableSum implements Callable<Integer> {
    private int number;
    public MyCallableSum(int number){
        this.number = number;
    }
    @Override
    public Integer call() throws Exception {
        int sum = 0;
        for (int i = 0; i <= number; i++) {
            sum += i;
        }
        return sum;
    }
}
```

```java
// 求和测试类
public class CallableSumDemo {
    public static void main(String[] args) {
        ExecutorService executorService = Executors.newFixedThreadPool(2);
        Future<Integer> f1 = executorService.submit(new MyCallableSum(100));
        Future<Integer> f2 = executorService.submit(new MyCallableSum(200));
        try {
            System.out.println(f1.get());
            System.out.println(f2.get());
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
    }
}
```

## 7.多线程的一些拓展

### 7.1匿名内部类方式实现多线程程序

### 7.2定时器的概述和使用

### 7.3定时任务的多次执行代码体现

### 7.4定时删除指定带内容目录

### 7.5多线程常见的面试题





[三个线程循环打印ABC](https://blog.csdn.net/hefenglian/article/details/82596072?dist_request_id=1331303.604.16182469483967587&depth_1-)


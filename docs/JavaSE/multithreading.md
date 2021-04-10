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

### 2.6线程的生命周期interrupt

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
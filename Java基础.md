## 多线程

进程：对应一个运行中的程序

线程：运行中的进程中的一条或多条执行路径

### 线程创建方式

#### 继承Thread类

1. 创建一个继承于Thread类的子类
2. 重新其run()，将此线程要执行的操作，声明在此方法体中
3. 创建当前Thread的子类的对象
4. 通过对象调用start()

```java
public class SingleThreadTest {
    public static void main(String[] args) {
        PrintNumber printNumber = new PrintNumber();
        printNumber.start();
        
        //使用匿名子类创建
        new Thread(){
            @Override
        	public void run(){
            	//...
        	}
        }.start();
        
        //main线程所执行的操作
        for (int i=1;i<=100;i++){
            if(i%2 == 0){
                System.out.println(i+"************");
            }
        }
    }
}
class PrintNumber extends Thread{
        @Override
        public void run() {
            //新建线程所执行的操作
            for (int i=1;i<=100;i++){
            if(i%2 == 0){
                System.out.println(i);
            	}
        	}
        }
}
```

#### 实现Runnable接口

1. 创建一个实现Runnable接口的类
2. 实现其run()方法
3. 创建实现类的对象
4. 将此对象作为参数传递到Thread类的构造器中，创建Thread类的实例
5. Thread类的实例调用start()

```java
public class SingleThreadTest {
    public static void main(String[] args) {
        PrintNumber p = new PrintNumber();
        Thread thread = new Thread(p);
        thread.start();
        
        //使用匿名子类创建
        new Thread(new Runnable() {
            @Override
            public void run() {
                //...
            }
        }).start();
        //lambda表达式
        new Thread(()->{
            //...
        }).start();
        
        //main线程所执行的操作
        for (int i=1;i<=100;i++){
            if(i%2 == 0){
                System.out.println(i+"************");
            }
        }
    }
}

class PrintNumber implements Runnable{
        @Override
        public void run() {
            for (int i=1;i<=1000;i++){
                if(i%2 == 0){
                    System.out.println(Thread.currentThread().getName()+":"+i);
                }
            }
        }
}
```

#### 两种方式比较

建议使用第二种，避免类的单继承性的局限，更适合处理有共享数据的线程

### 线程的常用结构

1. 线程的构造器

   public Thread()：分配一个新的线程对象

   public Thread(String name)：分配一个指定名字的线程对象

   public Thread(Runnable target)：指定创建线程的目标对象，它实现了Runnable接口中的run方法

   public Thread(Runnable target,String name)：...

2. 线程中的常用方法

   start()：启动线程，调用线程的run()

   run()：将线程要执行的操作声明在其中

   currentThread()：获取当前线程

   getName()：获取线程名

   setName()：设置线程名

   sleep(long mills)：线程休眠，不会释放同步监视器

   **wait()**：线程进入等待状态，会释放同步监视器

   **notify()**：一旦执行此方法，就会唤醒进入wait线程中优先级**最高的一个**(如果有多个最高的优先级线程，随机唤醒一个)

   唤醒的线程从wait()的位置继续执行

   **notifyAll()**：唤醒所有线程

   **注意：wait()，notify()，notifyAll()三个方法必须使用在同步代码块或者同步方法中(synchronized),调用者必需是同步监视器**

   yield()：一旦执行此方法，就释放CPU的执行权

   join()：在线程a中调用线程b的join()方法，线程a会进入阻塞状态，直到线程b执行完毕

   isAlive()：判断线程是否存活

   过时方法：

   stop()：强行结束线程，不建议使用

   suspend()/resume()：可能造成死锁，不建议使用

3. 线程的优先级

   getPriority()：获取线程的优先级

   setPriority()：设置线程的优先级(1 - 10)

### 线程的安全问题

当使用多个线程访问同一资源时，若多个线程中对资源有读和写操作，就容易出现线程安全问题。

案例：模拟火车站买票，三个窗口卖100张票

```java
public class WindowsTest {

    public static void main(String[] args) {
        SaleTicket saleTicket = new SaleTicket();
        Thread thread = new Thread(saleTicket,"窗口1");
        Thread thread2 = new Thread(saleTicket,"窗口2");
        Thread thread3 = new Thread(saleTicket,"窗口3");
        thread.start();
        thread2.start();
        thread3.start();
    }
    static class SaleTicket implements Runnable{
        int ticket = 100;
        @Override
        public void run() {
            while (true){

                if(ticket>0){
                    try {
                        Thread.sleep(10);
                    } catch (InterruptedException e) {
                        throw new RuntimeException(e);
                    }
                    System.out.println(Thread.currentThread().getName() + "售票，票号为："+ticket);
                    ticket--;
                }else {
                    break;
                }
            }
        }
    }

}
```

结果出现重复票号，负数票号。原因：一个线程在操作票数时，其他线程也同时操作

解决方案：必须保证一个线程操作票数时，其他线程等待该线程操作完成

方式1：同步代码块

```java
synchronized(同步监视器){
	//需要被同步的代码
}
/*
需要被同步的代码，即操作共享数据的代码
共享数据：即多个线程都需要操作的数据，如ticket
需要被同步的代码被synchroized包裹后，就使得一个线程在操作这些代码的过程中，其他线程必须等待
同步监视器：俗称锁，哪个线程获取了锁，哪个线程就能执行需要被同步的代码
同步监视器可以使用任何一个类的对象充当。但是多个线程必需使用同一个同步监视器
*/
```

方式2：同步方法

```java
public synchronized void func(){//同步监视器m
    //...
}
```

### 单例之懒汉式的线程安全问题

```java
class Bank{
	private Bank(){};
    private static volatile Bank instance = null;//声明volatile为了避免出现指令重排
    public static synchronized Bank getInstancek(){
        if(instance == null){
            Thread.sleep(1000);
            instance = new Bank();
        }
        return instance;
    }
}
//方式2
class Bank{
	private Bank(){};
    private static volatile Bank instance = null;
    public static  Bank getInstancek(){
        if(instance == null){//新建一次实例后就不用再进入同步代码串行，效率更高
            synchronized(Bank.class){
                Thread.sleep(1000);
             	if(instance == null){
            		instance = new Bank();
        	 	}
        	}
        }
        return instance;
    }
}
```

### 死锁问题

不同的线程分别占用对方需要的同步资源不放弃，都在等待对方放弃自己需要的同步资源，就形成了线程的死锁。

编写程序时，要避免出现死锁。

死锁例子：

```java
StringBuilder s = new StringBuilder();
StringBuilder s2 = new StringBuilder();
new Thread(){
    synchronized(s){
        s.append("1");
        s2.append("a");
    }
    Thread.sleep(100);
     synchronized(s2){
        s.append("2");
        s2.append("b");
    }
}.start();
new Thread(){
    synchronized(s2){
        s.append("3");
        s2.append("c");
    }
    Thread.sleep(100);
     synchronized(s){
        s.append("4");
        s2.append("d");
    }
}.start();
```

### jdk5新特性Lock

```java
private static final ReentrantLock lock = new ReentrantLock();//Lock的实现类，需确保多个线程使用同一个lock
lock.lock();
//需要同步的代码块
lock.unlock();//为了确保执行需要放入try-catch的finallyl

```

synchronized同步的方式与Lock的对比：

Lock通过两个方法控制代码块的同步，更灵活

Lock做为接口，提供了多种实现类，适合更多更复杂的场景，效率更高

### 线程的通信

生产者消费者问题

### 线程创建方式拓展

#### 实现Callable接口

```java
class NewThread implement Callable{
    @Override
    public Object call(){
        //...
        return null;
    }
}
//使用
NewThread newThread = new NewThread();
FutrueTask futrueTask = new FutrueTask(newThread);
Thread t = new Thread(futrueTask);
t.start();
```

#### 线程池(重点)

```java
ExcutorsService excutorsService = Excutors.newFixedThreadPool(10);
ThreadPoolExcutor service = (ThreadPoolExcutor) excutorsService;
service.setMaximumPoolSize(50);//
service.excute(new Runnable{run(){...}});//执行
service.shutdown();
```

线程池的生命周期：

从诞生到死亡经历RUNNING、SHUTDOWN、STOP、TIDYING、TERMINATED五个生命周期状态。

- RUNNING 表示线程池处于运行状态，能够接受新提交的任务且能对已添加的任务进行处理。RUNNING状态是线程池的初始化状态，线程池一旦被创建就处于RUNNING状态。
- SHUTDOWN 线程处于关闭状态，不接受新任务，但可以处理已添加的任务。RUNNING状态的线程池调用shutdown后会进入SHUTDOWN状态。
- STOP 线程池处于停止状态，不接收任务，不处理已添加的任务，且会中断正在执行任务的线程。RUNNING状态的线程池调用了shutdownNow后会进入STOP状态。
- TIDYING 当所有任务已终止，且任务数量为0时，线程池会进入TIDYING。当线程池处于SHUTDOWN状态时，阻塞队列中的任务被执行完了，且线程池中没有正在执行的任务了，状态会由SHUTDOWN变为TIDYING。当线程处于STOP状态时，线程池中没有正在执行的任务时则会由STOP变为TIDYING。
- TERMINATED 线程终止状态。处于TIDYING状态的线程执行terminated()后进入TERMINATED状态。

## 泛型

类似于生活中的标签

### 引入

在Java中，我们声明方法时，当在完成方法功能时如果有未知的数据需要参与，这些未知的数据需要在调用方法时才能确定，那么我们把这样的数据通过形参表示，在方法体中，用这个形参名来代表那个未知的数据，而调用者在调用时传入对应的实参(类型是不可变的)。而泛型则是一种类型参数。

概念：所谓泛型，就是允许在定义类、接口时通过一个标识表示类中某个属性的类型或者是某个方法的返回值或参数的的类型。这个类型参数将在使用时确定。

```java
//jdk7新特性，类型推断
HashMap<String,Integer> map = new HashMap<>();//右边泛型可省略，但<>不能省略
//jdk10新特性
var entrySet = map.e
```


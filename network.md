# 网络与线程
--------------------------------------------------------------------------------
## 网络
客户端与服务器之间进行通信需要几个步骤
1、连接：用户通过建立socket连接来连接服务器
2、传送：用户送出消息给服务器
3、接收：用户从服务器接收消息
### 建立Socket连接
要连接到其他的机器上，我们会需要Socket的连接。Socket是个代表两台机器之间连接的对象(java.net.Socket)
代码实现如下：
```java
Socket s = new Socket("127.0.0.1",6001);  //需要传入服务器端的IP地址以及端口号
```
> Socket连接的建立代表两台机器之间存在对方的信息，包括网络地址和TCP的端口号

### 使用BufferedReader从Socket上读取数据
用串流来通过Socket连接来沟通。
步骤：
- 建立对服务器的Socket连接
```java
Socket s = new Socket("127.0.0.1",6001);
```
- 建立连接到Socket上低层输入串流的InputStreamReader
```java
InputStreamReader streamReader = new InputStreamReader(s.getInputStream());   //括号内的参数是从Socket取得输入串流
```
- 建立BufferedReader来读取
```java
BufferedReader reader = new BufferedReader(stream);   //将BufferedReader链接到InputStreamReader
String message = reader.readLine();
```
### 用PrintWriter写数据到Socket上
- 对服务器建立Socket连接
```java
Socket s = new Socket("127.0.0.1",6001);
```
- 建立链接到Socket的PrintWriter
```java
PrintWriter writer = new PrintWriter(s.getOutputStream());    //参数来自Socket
```
PrintWriter是字符数据和字节间转换的桥梁，可以衔接String和Socket两端
- 写入数据
```java
writer.println("message to send");    //println()会在送出的数据后面加上换行
writer.print("another message");    //print不会加上换行
```

### 服务器程序
编写服务器程序需要一对Socket，它们是一个会等待用户请求(当用户创建Socket时)的ServerSocket以及与用户通信用的Socket
#### 工作方式
- 服务器应用程序对特定端口创建出ServerSocket
```java
ServerSocket serverSocket = new ServerSocket(6001);   //让服务器监听来自6001端口的客户端请求
```
- 客户端对服务器应用程序建立Socket连接
```java
Socket sock = new Socket("127.0.0.1",6001);
```
- 服务器创建出与客户端通信的新Socket
```java
Socket sock = serverSock.accept();
```
accept()方法会在等待用户的Socket连接时闲置着，当用户连上来时，此方法会返回一个Socket(在不同的端口上)以便与客户端通信。Socket与ServerSocket的端口不同，因此ServerSocket可以空出来等待其他用户

### 要点
- 客户端与服务器的应用程序通过Socket连接来沟通
- Socket代表两个应用程序之间的连接，它们可能会是在不同的机器上执行的
- 客户端必须知道服务器应用程序的IP地址(或网域名称)和端口号
- TCP端口号是个16位的值，用来指定特定的应用程序。它能够让用户连接到服务器上各种不同的应用程序
- 从0-1023的端口号是保留给HTTP、FTP、SMTP等已知的服务
- 客户端通过建立Socket来连接服务器
- 一旦建立了连接，客户端可以从socket取得低层串流
  sock.getInputStream();
- 建立BufferedReader链接InputStreamReader与来自Socket的输入串流以读取服务器的文本数据
- InputStreamReader是个转换字节成字符的桥梁。它主要是用来链接BufferedReader与低层Socket输入串流
- 建立** 直接链接 **Socket输出串流的PrintWriter请求print()方法或println()方法来送出String给服务器
- 服务器可以使用ServerSocket来等待用户对特定端口的请求
- 当ServerSocket接到请求时，它会做一个Socket连接来接受客户端的请求
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
## 线程
### Java的multithreading
Java在语言中就要内置多线程的功能，建立新的线程来执行是很简单的
```java
Thread t = new Thread();
t.start();
```
建立出新的线程对象就会启动新的线程，它是个独立的调用执行空间
当你需要启动新的线程时就建立Thread的实例
> 线程是独立的线程。它代表独立的执行空间。
Thread是Java中用来表示线程的类
要建立线程就得创建Thread

### 如何启动新的线程
1. 建立Runnable对象(线程的任务)
```java
Runnable threadJob = new MyRunnable();
```
你会编写实现Runnable的类，而此类就是你对线程要执行的任务的定义，也就是说此方法会在线程的执行空间运行
2. 建立Thread对象(执行工人)并赋值Runnable(任务)
```java
Thread myThread = new Thread(threadJob);
```
把Runnable对象传给Thread的构造函数，这会告诉Thread对象要把哪个方法放在执行空间去运行——Runnable的run()方法
3. 启动Thread
```java
myThread.start();
```
在还没有调用Thread的start()方法之前什么也不会发生。这是你在只有一个Thread实例来建立新的线程时会发生的事情。当新的线程启动以后，它会把Runnable对象的方法摆到新的执行空间中
> 对Thread而言，它是个工人，而Runnable就是这个工人的工作。Runnable带有会放在执行空间的第一项方法：run()

#### 要点
- 以小写t描述的thread是个独立的线程
- Java中的每个线程都有独立的执行空间
- 大写T的Thread是java.lang.Thread这个类，它的对象是用来表示线程
- Thread需要任务，任务是实现过Runnable的实例
- Runnable这个接口只有一个方法
- run()会是新线程所执行的第一项方法
- 要把Runnable传给Thread的构造函数才能启动新的线程
- 线程在初始化以后还没有调用start()之前处于新建立的状态
- 调用Thread对象的start()方法之后，会建立出新的执行空间，它处于可执行的状态等待被挑出来执行
- 当java虚拟机的调度器选择某个线程之后它就处于执行中的状态，单处理器的机器只能有一个执行中的线程
- 有时线程会因为某些原因而被堵塞
- 调度不能保证任何的执行时间和顺序，所以你不能期待它会完全地平均分配执行，你最多也只能影响sleep的最小保证时间

锁不是配在方法上，而是在对象上
每个Java对象都有一个锁，每个锁只有一把钥匙，通常对象都没上锁，也没有人在乎这件事，但如果对象有同步化的方法，则线程只有在取得钥匙的情况下进入线程，也就是说并没有其他线程已经进入的情况下才能进入

#### 要点
- Thread.sleep()这个静态方法可以强制线程进入等待状态到过了设定时间为止，例如Thread.sleep(200)会睡上200个毫秒
- 可以调用sleep()让所有的线程都有机会运行
- sleep()方法可能会抛出InterruptedException异常，所以要在try/catch，或者把它声明出来
- 你可以用setName()方法给线程命名，通常是来除错的
- 如果两个或两个以上的线程存取堆上相同的对象可能会出现严重的问题
- 如果两个或两个以上的线程存取相同的对象可能会引发数据的损毁
- 要让对象在线程上有足够的安全性，就要判断出哪些指令不能被分割执行
- 使用synchronized这个关键词修饰符可以防止两个线程同时进入同一对象的同一方法
- 每个对象都有单一的锁，单一的钥匙，这只会在对象带有同步化方法时才有实际的用途
- 线程尝试要进入同步化过的方法时必须要取得对象的钥匙，如果因为已经被别的线程拿走了，那就得等
- 对象就算是有多个同步化过的方法，也还是只有一个锁，一旦某个线程进入该对象的同步化方法，其他线程就无法进入该对象上的任何同步化线程

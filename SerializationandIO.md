# 序列化和文件的输入输出
--------------------------------------------------------------------
### 将序列化对象写入文件
步骤：
- 创建出FileOutputStream
```java
FileOutputStream fileStream = new FileOutputStream("MyGame.ser");
```
- 创建ObjectOutputStream
```java
ObjectOutputStream os = new ObjectOutputStream(fileStream);
```
- 写入对象
```java
os.writeObject(characterOne);
os.writeObject(characterTwo);
os.writeObject(characterThree);
```
- 关闭ObjectOutputStream
```java
os.close();
```

### 对象的序列化要点
- 你可以通过序列化来存储对象的状态
- 使用ObjectOutputStream来序列化对象(java.io.*)
- Stream是连接串流或是链接用的串流
- 连接串流用来表示源或目的地、文件、网络套接字连接
- 链接用串流用来衔接连接串流
- 用FileOutputStream来链接ObjectOutputStream来将对象序列化到文件上
- 调用ObjectOutputStream的writeObject(theObject)来将对象序列化，不需调用FileOutputStream的方法
- 对象必须实现序列化的接口才能被序列化。如果父类实现序列化，那么子类也就自动的实现，不管是否有明确的声明
- 当对象被序列化时，整个对象版图都会被序列化。这代表它的实例变量所引用的对象也会被序列化
- 如果有不能被序列化的对象，执行期间就会被抛出异常
- 除非该实例变量被标记为transient。否则该变量在还原的时候会被赋予null或primitive主数据类型的默认值
- 在解序列化时(deserialization),所有的类都必须能让java虚拟机找到
- 读取对象的顺序必须与写入的顺序相同
- readObject()的返回类型是Object，因此解序列化回来的对象还需要转换成原来的类型
- 静态变量不会被序列化，因为所有对象都是共享同一份静态变量值。


### 文件输入输出要点
- 用FileWriter这个连接串流来写入文本文件
- 将FileWriter链接到BufferedWriter可以提升效率。
- File对象代表文件路径而不是文件本身
- 你可以用File对象来创建、浏览和删除目录
- 用到String文件名的串流大部分都可以用File对象来代替String
- 用FileReader来读取文本文件
- 将FileReader链接到BufferedReader可以提升效率
- 通常我们会使用特殊的字符来分隔文本数据中的不同元素
- 使用split()方法可以把String拆开，其中的分隔字符不会被当成数据来看待

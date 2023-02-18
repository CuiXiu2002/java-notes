# Java语言进阶

## 异常

异常：程序执行中发生的不正常情况

### 异常的体系结构

![image-20230207105710800](https://cuicy-1314839020.cos.ap-shanghai.myqcloud.com/typora-user-images/image-20230207105710800.png)

### 异常处理

#### 抓抛模型

![image-20230207105923424](https://cuicy-1314839020.cos.ap-shanghai.myqcloud.com/typora-user-images/image-20230207105923424.png)

#### try-catch-finally

```java
try{
    //可能出现异常的代码
}catch(异常类型1 变量名1){
    //处理异常的方式1
}catch(){
    //处理异常的方式2
}finally{
    //一定会被执行的代码
    //数据库连接、输入输出流、网络编程socket等资源的释放
}
```

- 一定要考虑编译时异常的处理，通常不处理运行时异常
- 像数据库连接、输入输出流、网络编程socket等资源，JVM不会自动回收，需要我们在finally中主动释放
- finally是可选的

#### throws

```java
public void method() throws ArrayIndexOutOfBoundsException
```

- 只是将异常抛出给了method()方法的调用者，没有真正处理异常，最终还是需要try-catch

#### throw

生成异常对象，而不是处理

```java
class student{
    int id;
    public void regist(int id){
        if(id<0){
            throw new RuntimeException("id不能为负数");
        }
    }
}
```

#### 自定义异常类

![image-20230207111411522](https://cuicy-1314839020.cos.ap-shanghai.myqcloud.com/typora-user-images/image-20230207111411522.png)

## 多线程

线程之间共享方法区和堆的数据

<img src="https://cuicy-1314839020.cos.ap-shanghai.myqcloud.com/typora-user-images/image-20230208113949504.png" style="zoom:67%;" />

### java的调度策略

- 同优先级的采用“先到先服务”策略和时间片策略
- 高优先级优先调度的抢占式策略（仍然遵循时间片，只是概率更高）

```java
MAX_PRIORITY=10;
NORM_PRIORITY=5;          //默认
MIN_PRIORITY=1;
```

### 线程的生命周期

<img src="https://cuicy-1314839020.cos.ap-shanghai.myqcloud.com/typora-user-images/image-20230207164919215.png" alt="image-20230207164919215" style="zoom: 67%;" />

### 方法一：继承Thread类

继承thread类，重写run方法

```java
public class ThreadTest {
    public static void main(String[] args) {
        new Thread(){                              //匿名子类方式
            @Override
            public void run() {
                super.run();
            }
        }.start();
    }
}

```

#### Thread类常用方法

- start() : 启动当前线程；调用当前线程的run()
- run() : 通常要重写Thread中的此方法，将要执行的操作写在此方法中
- currentThread() : 静态方法，返回当前执行代码的线程Thread
- getName() : 获取当前线程的名称
- setName() : 设置当前线程的名称
- yield() : 释放当前cpu的执行权
- join() : 在线程a中调用线程b的join方法，则线程a进入阻塞状态，直到线程b完全执行完以后，线程a才结束阻塞状态
- sleep(long millitime) : 让当前线程阻塞millitime毫秒
- isAlive() : 判断当前进程是否存活（run方法是否执行结束）
- getPriority() : 获取当前线程的优先级
- setPriority(int p) :  设置当前线程的优先级

### 方法二：实现Runnable接口

实现runnable接口，重写run方法

```java
public class InstanceTest {
    public static void main(String[] args) {
        new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println(Thread.currentThread().getName());
            }
        }).start();
    }
}
```

#### 相比方法一的优点

实现的方式没有类的单继承性的局限性

### 方法三：实现Callable接口（可以有返回值、可以抛出异常、支持泛型）

```java
class ThreadTest implements Callable {
    @Override
    public Object call() {
        return new Object();
    }
}
public class InstanceTest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        FutureTask futureTask=new FutureTask(new ThreadTest());
        new Thread(futureTask).start();
        Object sum=futureTask.get();          //返回值
    }
}
```

### 方法四：线程池

```java
class CallTest implements Callable {
    @Override
    public Object call() {
        return new Object();
    }
}
class RunTest implements Runnable {
    @Override
    public void run() {

    }
}
public class InstanceTest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ExecutorService service= Executors.newFixedThreadPool(10);
        /* 执行 */
        RunTest runTest = new RunTest();
        CallTest callTest = new CallTest();
        service.execute(runTest); //run
        service.submit(callTest); //call
        /* 关闭 */
        service.shutdown();
    }
}
```



### 线程同步

共享数据：多个线程共同操作的变量

多个线程对共享数据同时操作，造成数据的不安全

解决方法：线程同步（同一时刻只有一个线程可以执行代码块）

#### 同步代码块

```java
synchronized(同步监视器){
    //需要被同步的代码
}
```

- 操作共享数据的代码，即为需要被同步的代码
- 同步监视器，俗称：锁。任何一个类的对象，都可以充当锁；多个线程必须要用同一把锁

#### 同步方法

如果操作共享数据的代码完整声明在一个方法中，我们不妨将此方法声明为同步的

#### lock锁

```java
class LockTest implements Runnable{
    ReentrantLock lock =new ReentrantLock();
    @override
    public void run(){
        lock.lock();
        //需要被同步的代码
        lock.unlock();
    }
}
```

手动释放

### 线程通信

object类的方法，调用者必须为同步监视器，必须使用在同步代码块或同步方法中

- wait() : 使当前线程进入阻塞状态，同时释放同步监视器
- notify() : 唤醒被wait的一个进程（优先级）
- notifyAll() : 唤醒被wait的全部进程

## 常用类

### 字符串类

#### String类

##### 内存解析

###### 理解不可变性

<img src="https://cuicy-1314839020.cos.ap-shanghai.myqcloud.com/typora-user-images/image-20230209103943860.png" alt="image-20230209103943860" style="zoom:80%;" />

- 字符串常量池不会存储相同内容的字符串（final char[]）
- 当对字符串重新赋值（包括replace、拼接），会改变栈里引用的值（地址值），而不是改变常量池的内容

###### 实例化方式的对比

<img src="https://cuicy-1314839020.cos.ap-shanghai.myqcloud.com/typora-user-images/image-20230209104508766.png" alt="image-20230209104508766" style="zoom: 67%;" />

###### 不同拼接方式的对比

- 常量与常量的拼接结果就在常量池
- 只要有一个是变量，结果就在堆中
- 调用intern方法，返回的结果在常量池中

##### 常用方法

- int length()
- char charAt(int index)
- boolean isEmpty() 
- String toLowerCase()
- String toUpperCase()
- String trim() : 忽略前导空白和尾部空白
- boolean equals(Object obj) 
- boolean equalsIgnoreCase(String str) : 忽略大小写比较
- String concat(String str) : 连接字符串，与“+”相同
- int compareTo(String str) : 比较字符串的大小，返回 this-str
- String substring(int beginIndex)
- String substring(int beginIndex, int endIndex)
- boolean endswith(String suffix)
- boolean startswith(String prefix)
- boolean startswith(String prefix, int toffset) : 测试字符串是否从指定索引开始以指定字符串开始
- boolean contains(String str)
- int indexOf(String str) 
- int indexOf(String str, int fromIndex) : 从fromindex开始找str的索引
- int lastIndexOf(String str) : 倒着找
- int lastIndexOf(String str, int fromIndex) 
- String replace(char oldChar, char newChar) : 全部替换
- String replace(String target, String replacement) : 全部替换
- String replaceAll(String regex, String replacement) : 用regex正则表达式匹配字符串，然后用replacement替换全部
- String replaceFirst(String regex, String replacement) : 用regex正则表达式匹配字符串，然后用replacement替换第一个
- boolean matches(String regex) : 是否匹配正则表达式
- String[] split(String regex) : 根据匹配的正则表达式拆分字符串
- String[] split(String regex, int limit) : 最多拆分limit个，如果多了 ，全部合并到最后一个

##### String与字符数组、字节数组的转换

String --> char[] : 调用String的toCharArray()

String --> byte[] : 调用String的getBytes()

#### StringBuffer类

##### String、StringBuffer、StringBuilder类的对比（源码）

- String：不可变的字符序列，改变value需要重新申请数组（final char[])
- StringBuffer：可变的字符序列，改变value在原有数组上改变，线程安全（同步方法）(char[])
- StringBuilder：可变的字符序列，改变value在原有数组上改变(char[])

##### StringBuffer常用方法

- StringBuffer append(xxx)
- StringBuffer delete(int start, int end) 
- StringBuffer insert(int offset, xxx)
- StringBuffer reverse()
- StringBuffer replace(int start, int end, String str) : 将[start,end)的内容替换为str

### 日期类

#### 时间戳

System.currentTimeMillis() : 返回当前时间与1970年1月1日0时0分0秒之间的毫秒数

#### Date类

java.util.Date

​     --|java.sql.Date

##### 实例化方式

- Date() : 当前date
- Date(long millis) : 毫秒数初始化

##### 常用方法

- toString()
- long getTime() : 返回毫秒数

#### SimpleDateFormat类

String与Date类的互转工具类

##### 实例化方式

- SimpleDateFormat(String format)

  ​	按照格式实例化，例如"yyyy-MM-dd hh:mm:ss"

##### 常用方法

- format : Date转String
- parse : String转Date

```java
public static void main(String[] args) throws ExecutionException, InterruptedException, ParseException {
        Date date=new Date();
        String time="2023-02-09 00:00:00";
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd hh:mm:ss");
        System.out.println(sdf.parse(time));
        System.out.println(sdf.format(date));
    }
```

#### Calendar类

##### 实例化方式

```java
Calendar c=Calendar.getInstance();
```

##### 常用方法

```java
/*  GET */
c.get(Calendar.DAY_OF_MONTH);
c.get(Calendar.DAY_OF_WEEK);
c.get(Calendar.DAY_OF_YEAR);
/* SET */
c.set(Calendar.DAY_OF_MONTH,18);
/* setTime */
Date date=new Date();
c.setTime(date);
/* getTime */
Date date=c.getTime();
```

#### LocalDateTime类（JDK8.0之后）

##### 实例化方式

```java
LocalDateTime now = LocalDateTime.now();
LocalDateTime of = LocalDateTime.of(2023, 2, 9, 15, 45, 00);
```

##### 常用方法

```java
/* get */
int dayOfMonth = now.getDayOfMonth();
/* set */
LocalDateTime localDateTime = of.withDayOfMonth(20);
```

#### DateTimeFormatter（JDK8.0之后）

和SimpleDateFormat一样,不过将Date类换成了LocalDateTime类

```java
 public static void main(String[] args) throws ExecutionException, InterruptedException, ParseException {
        LocalDateTime now = LocalDateTime.now();
        LocalDateTime of = LocalDateTime.of(2023, 2, 9, 15, 45, 00);
        DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("yyyy-MM-dd hh:mm:ss");
        System.out.println(dateTimeFormatter.format(now));
    }
```

### 比较器

#### 实现Comparable接口

- 实现Comparable接口，重写compareTo方法

- 调用Arrays.sort(Object obj)方法进行排序
- compareTo：如果要升序，则返回this-obj；否则返回obj-this

#### 实现Comparator接口

```java
 public static void main(String[] args) throws ExecutionException, InterruptedException, ParseException {
        String a[]=new String[5];
        Arrays.sort(a, new Comparator<String>() {
            @Override
            public int compare(String o1, String o2) {
                return 0;
            }
        });
    }
```

## 枚举类与注解

### 枚举类

一种特殊的类

#### 原理

与单例模式类似，将构造方法声明为private（不可以自定义对象）；将对象声明在类内（static final）

#### 用法

```java
enum Family{
    cyf("崔宇帆"),        //实际上是一个对象
    cyh("崔宇恒"),        //实际上是一个对象
    cxy("崔鑫悦");        //实际上是一个对象
    private int name;
    private Family(String name){
        this.name=name;
    }
}
```

### 注解（Annotation）

注解是代码里的特殊标记，这些标记在编译、类加载、运行时被读取，并做特殊处理

#### 自定义注解

参照@SuppressWarnings()的定义

```java
@Target({TYPE,FIELD,METHOD,PARAMETER,CONSTRUCTOR,LOCAL_VARIABLE,TYPE_PARAMETER,TYPE_USE})           //声明范围
@Retention(RetentionPolicy.CLASS)       //生命周期
public @interface MyAnnotation {
    String value() default "cuicy";
}
```

#### 获取注解信息

- 通过反射获取

- 要获取，必须把声明周期声明为RetentionPolicy.RUNTIME

## 集合

java集合可分为Collection和Map两种体系

### Collection接口

单列数据，定义了存取一组方法的集合

<img src="https://cuicy-1314839020.cos.ap-shanghai.myqcloud.com/typora-user-images/image-20230210102035853.png" alt="image-20230210102035853" style="zoom: 67%;" />

<center>实线表示继承，虚线表示实现</center>

#### 常用方法

- add(Object e) : 
- addAll(Collection coll1) : 将集合coll1中的元素加入当前的集合中
- size()
- isEmpty() 
- clear() : 清空集合中的元素
- contains(Object e) 
- containsAll(Collection coll1) : 检查是否有coll1的所有元素
- remove(Object e)
- removeAll(Collection coll1) : 移除coll1与本集合的交集
- retainAll(Collection coll1) : 保留coll1与本集合的交集
- equals(Object obj) : 判断两个集合是否相等
- hashCode() : 返回当前对象的哈希值
- toArray() : 返回数组
- Arrays.asList() :数组转换为list

#### 遍历方法

##### iterator

```java
    public static void main(String[] args) throws ExecutionException, InterruptedException, ParseException {
       List<Integer> a= Arrays.asList(new Integer[]{1,2,3});
        Iterator iterator=a.iterator();
        while (iterator.hasNext()){
            System.out.println(iterator.next());
        }
    }
```

##### for each

```java
    public static void main(String[] args) throws ExecutionException, InterruptedException, ParseException {
       List<Integer> a= Arrays.asList(new Integer[]{1,2,3});
       for(Object obj:a){
           System.out.println(obj);
       }
    }
```

#### List

有序的、可以重复的集合

##### ArrayList（线程不安全）

底层：数组，初始化时创建一个大小为10的数组（jdk7）

建议开发中采用带参构造器，直接定义好大小（提高效率）

##### LinkedList

底层：双向链表

```java
private static Node<T>{
    T item;
    Node<T> prev;
    Node<T> next;
}
```

##### 常用方法（添加）

add(int index, Object e) : 将e添加在index的位置

indexof(Object e)

get(int index)

set(int index, Object ele)

remove(int index)

subList(int fromIndex, int toIndex)

<img src="https://cuicy-1314839020.cos.ap-shanghai.myqcloud.com/typora-user-images/image-20230210152650340.png" alt="image-20230210152650340" style="zoom:67%;" />

#### Set

无序的、不可重复的集合

- 向set中添加数据的类型，一定要重写equals和hashCode方法
- equals判断相等的对象，hashCode也一定要相同

##### HashSet（线程不安全）

- 无序性：不等于随机性。数据并非按照索引添加，而是按照数据的哈希值添加
- 不重复性：按照equals()判断是否重复

###### 添加数据的过程

1. HashSet底层以数组形式存储，添加数据时，首先用hashCode()方法计算数据的哈希值，哈希值根据某种算法得到对应的索引。

2. - 如果对应的索引没有数据，则直接添加
   - 如果对应的索引有数据，首先判断数据的哈希值是否相同
     - 若不同，则以链表形式添加
     - 若相同，则用equals()判断值是否相同
       - 若不同，则以链表形式添加
       - 若相同，则不添加

<img src="https://cuicy-1314839020.cos.ap-shanghai.myqcloud.com/typora-user-images/image-20230210161120644.png" style="zoom:50%;" />

<center>数组+链表</center>

##### LinkedHashSet

作为HashSet的子类，在添加数据的同时，每个数据还维护了一对结点，记录添加的顺序

对于频繁的遍历操作，效率更高

<img src="https://cuicy-1314839020.cos.ap-shanghai.myqcloud.com/typora-user-images/image-20230210162422369.png" alt="image-20230210162422369" style="zoom: 67%;" />

##### TreeSet

- 向TreeSet中添加的数据，必须是相同类对象
- 自动排序
- 实现Comparable接口或Comparator
- 底层使用红黑树

### Map接口

映射，双列数据，保存具有映射关系“key-value”对的集合（高中数学“函数”）

<img src="https://cuicy-1314839020.cos.ap-shanghai.myqcloud.com/typora-user-images/image-20230210102116955.png" alt="image-20230210102116955" style="zoom:67%;" />

#### 常用方法

- put(Object key, Object value) 
- putAll(Map m)
- remove(Object key) : 移出key并返回value
- clear() 
- get(Object key) :返回key的value
- containsKey(Object key) 
- containsValue(Object value) 
- isEmpty() 
- size()
- equals(Object obj)

#### 遍历方法

```java
public static void main(String[] args) throws ExecutionException, InterruptedException, ParseException {
    HashMap test=new HashMap();
    test.put("cyf","软件工程");
    test.put("xhx","法学");
    test.put("txy","网络工程");
    Set keySet = test.keySet();                     //获取keySet
    Collection values = test.values();				//获取value
    Set entrySet = test.entrySet();					//获取entrySet
    System.out.println(keySet);
    System.out.println(values);
    System.out.println(entrySet);
}
```

#### 结构理解

- key：无序的、不可重复的，采用set存储，重写hashCode和equals
- value：无序的、可重复的，采用collection存储，重写equals
- entry：一个key-value对构成一个entry对象，无序的、不可重复的，采用set存储

#### HashMap

##### 添加数据的过程（jdk7）

- 在实例化之后，创建了长度为16的entry数组table

- 添加数据key-value，用hashCode()方法计算数据key的哈希值，哈希值根据某种算法得到对应的索引。
  - 如果对应的索引没有数据，则直接添加
  - 如果对应的索引有数据，首先判断数据的哈希值是否相同
    - 若不同，则以链表形式添加
    - 若相同，则用equals()判断值是否相同
      - 若不同，则以链表形式添加
      - 若相同，则以添加值的value替代原来的value

##### 添加数据的过程（jdk8）

- new HashMap() : 底层没有创建数组
- 首次调用put时创建长度为16的数组
- 底层数组为node[]
- 底层结构：数组+链表+红黑树
  - 当数组的长度>64且某一位置上的链表长度>8时，此时此位置上的所有数据以红黑树存储（提高查找效率）

##### 一些参数

- DEFAULT_INITIAL_CAPACITY : HashMap的默认初始化容量，为16
- DEFAULT_LOAD_FACTOR : 默认加载因子，为0.75
- threshold : 扩容的边界值，容量×加载因子，12
- TREEIFY_THRESHOLD : 链表中长度的临界值，默认为8（大于8转换为红黑树）
- MIN_TREEIFY_CAPACITY : 数组的临界值，默认为64（大于64转换为红黑树）

#### LinkedHashMap

与LinkedHashSet一样，添加了before和after两个指针

#### TreeMap

自动排序，参考TreeSet

#### Properties

继承于HashTable，用于读取工具类（name=崔宇帆...）

- getProperties : 获取属性的值

### Collections工具类

用来操作Collection和Map

- reverse(List list) : 反转list
- shuffle(List list) : 随机排序list
- sort(List list) 
- sort(List list, Comparator comparator) 
- swap(List list, int i, int j)
- max(List list) 
- max(List list, Comparator comparator) 
- min(List list) 
- min(List list, Comparator comparator) 

- frequency(List list, Object obj) : 返回obj出现的次数
- copy(List dest, List src) : 将src中的内容复制到dest中
- replaceAll(List list, Object oldVal,Object newVal)
- synchronizedList(List list) : 将线程不安全的list转换为线程安全的list（返回）

## 泛型

## 文件File类

## IO流

### 流的分类

<img src="https://cuicy-1314839020.cos.ap-shanghai.myqcloud.com/typora-user-images/image-20230216141020768.png" alt="image-20230216141020768" style="zoom:67%;" />

- 字节流：操作字节（图片、音频等）（InputStream、OutputStream）

- 字符流：操作char，文本居多（Reader、Writer）

- 节点流：直接从数据流向程序或程序流向数据

- 处理流：对流进行处理的流

### 流的体系结构

<img src="https://cuicy-1314839020.cos.ap-shanghai.myqcloud.com/typora-user-images/image-20230216141336051.png" alt="image-20230216141336051" style="zoom:67%;" />

- 节点流：访问文件的文件流

### 文件流

- FileReader：文本文件读取，常用方法
  - new FileReader(File file)
  - read()：读取一个字符，读不到时返回-1
  - read(char[] offset)：读取多个字符，读不到时返回-1
- FileWriter：文本文件写入
  - new FileWriter(File file,boolean append) ，append表示是否为追加模式
  - write(String str)：写入字符串
  - write(char[] offset)：写入字符数组
  - write(char[] offset,int off, int len)：从offset的off开始写入长度len的字符
- FileInputStream和FileOutputStream用于非文本文件（图片视频音频等）的读取和写入，方法类似不再赘述
- 由于流必须有关闭操作，所以异常处理采用try-catch-finally结构

```java
	public static void main(String[] args){
        FileReader in= null;
        FileWriter fileWriter = null;
        try {
            //1.造文件
            File test = new File("in.txt");
            File outf = new File("out.txt");
            //2.造流
            in = new FileReader(test);
            fileWriter = new FileWriter(outf);
            char[] off=new char[1024];
            int len=0;
            //3. 读取/写入
            while((len=in.read(off))!=-1){
                fileWriter.write(off,1,len-1);
            }
        } catch (IOException e) {
            throw new RuntimeException(e);
        } finally {
            try {
                if(in!=null)
                    //4.关闭
                    in.close();
            } catch (IOException e) {
                throw new RuntimeException(e);
            }
            try {
                if(fileWriter!=null)
                    fileWriter.close();
            } catch (IOException e) {
                throw new RuntimeException(e);
            }
        }
    }
```

### 缓冲流

提高节点流的读写速度

- BufferedInputStream
- BufferedOutputStream
- BufferedReader
  - 多了一个readLine（读取行）
- BufferedWriter
- 处理流，就是套接在已有的流之上（方法也可套用）

```java
BufferedReader bufferedReader = new BufferedReader(new Reader(new File("hello.txt")));
```

### 转换流

提供字符流与字节流之间的转换

- InputStreamReader(inputStream, String charset) ：将字符流转换成字节流
- OutputStreamWriter(writer, String charset) ： 将字节流转换成字符流

<img src="https://cuicy-1314839020.cos.ap-shanghai.myqcloud.com/typora-user-images/image-20230216161334850.png" alt="image-20230216161334850" style="zoom:67%;" />

### 标准输入输出流

- System.in：标准输入流，默认从键盘输入
- System.out：标准输出流，默认从显示器输出
- 实现Scanner类：先用InputStreamReader转换为字节流，再用BufferedReader包起来，调用readLine方法即可
- 改变输出路径：System.setOut(writer)

### 打印流

System.out：PrintStream

### 数据流

用于读取或写入基本数据类型/字符串（读取顺序与写入顺序一致）

- DataOutputStream(outputStream)，常用方法
  - writeInt(18)
  - writeUTF("asddas")
- DataInputStream
  - readUTF()
  - readInt()

### 对象流

用于读取或写入基本数据类型/对象的处理流

- 序列化：用ObjectOutputStream来保存基本数据类型/对象的机制，把java中的对象以二进制形式保存到磁盘中或通过网络传输出去
- 反序列化：用ObjectInputStream来保存基本数据类型/对象的机制，将磁盘文件中的对象还原为内存中的对象
- 实现序列化机制的关键点：
  - 类实现Serializable接口
  - 类内定义public static final long serialVersionUID

```java
public class Person implements Serializable {
    public static final long serialVersionUID = 4254756168L;
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
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

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```

```java
public class InstanceTest {
    public static void main(String[] args){
        //序列化
        ObjectOutputStream objectOutputStream = null;
        try {
            objectOutputStream = new ObjectOutputStream(new FileOutputStream("test.dat"));
            objectOutputStream.writeObject(new Person("崔秀",20));
        } catch (IOException e) {
            throw new RuntimeException(e);
        } finally {
            try {
                if(objectOutputStream!=null)
                    objectOutputStream.close();
            } catch (IOException e) {
                throw new RuntimeException(e);
            }
        }
        //反序列化
        ObjectInputStream objectInputStream = null;
        try {
            objectInputStream = new ObjectInputStream(new FileInputStream("test.dat"));
            System.out.println((Person) objectInputStream.readObject());
        } catch (IOException e) {
            throw new RuntimeException(e);
        } catch (ClassNotFoundException e) {
            throw new RuntimeException(e);
        } finally {
            try {
                if(objectInputStream!=null)
                    objectInputStream.close();
            } catch (IOException e) {
                throw new RuntimeException(e);
            }
        }
    }
}
```

### 文件读写流

可以实现文件的读、写、覆盖、插入操作（字节流）

```java
RandomAccessFile rw = new RandomAccessFile("in.txt", "rw");
```

方法：

- read
- write : 从头开始写
- seek(int index) : 将指针移动到index处

### NIO.2 中Path、Paths、Files类

File类的升级版，暂时不用了解

### 第三方jar包

有很多现成的api可以用

## 网络编程

- 如何准确地定位网络上的一台或多台主机；定位主机上特定的应用
  - IP : 网络中唯一的一台主机
  - 端口号 : 区分主机上不同的应用程序
- 找到主机后进行可靠高效的数据传输
  - 协议

### IP和端口号

#### IP

<img src="https://cuicy-1314839020.cos.ap-shanghai.myqcloud.com/typora-user-images/image-20230217160206176.png" alt="image-20230217160206176" style="zoom:67%;" />

在java中使用InetAddress类来表示

##### 实例化

```java
InetAddress localHost = InetAddress.getLocalHost();
InetAddress ia1=InetAddress.getByName("192.168.0.1");
InetAddress ia2=InetAddress.getByName("www.atguigu.com");
```

##### 方法

```java
System.out.println(localHost.getHostName());
System.out.println(localHost.getHostAddress());
```

#### 端口号

<img src="https://cuicy-1314839020.cos.ap-shanghai.myqcloud.com/typora-user-images/image-20230217160831671.png" alt="image-20230217160831671" style="zoom:67%;" />

### TCP/IP协议簇

![](https://cuicy-1314839020.cos.ap-shanghai.myqcloud.com/typora-user-images/image-20230217115812469.png)

<img src="https://cuicy-1314839020.cos.ap-shanghai.myqcloud.com/typora-user-images/image-20230217120146889.png" alt="image-20230217120146889" style="zoom: 67%;" />

<center>传输过程：包装快递拆快递</center>

#### TCP和UDP的对比

<img src="https://cuicy-1314839020.cos.ap-shanghai.myqcloud.com/typora-user-images/image-20230217161109686.png" alt="image-20230217161109686" style="zoom:67%;" />

<center>tcp更像打电话，udp更像发短信</center>

#### TCP编程示例

三次握手建立连接，四次挥手释放连接

实现：客户端向接收端发送文件，接收端保存并返回“收到”，客户端打印“收到”

理解流的传输过程

- server(接收端)

```java
    ServerSocket serverSocket = null;
    Socket socket = null;
    InputStream inputStream = null;
    FileOutputStream fileOutputStream = null;
    try {
        //1.实例化serverSocket、socket（定义端口号）
        serverSocket = new ServerSocket(65535);
        socket = serverSocket.accept();
        //2.定义输入流
        inputStream = socket.getInputStream();
        fileOutputStream = new FileOutputStream("test.jpg");
        byte[] b=new byte[1024];
        int len;
        //3.输入
        while((len=inputStream.read(b))!=-1){
            fileOutputStream.write(b,0,len);
        }
        OutputStream outputStream = socket.getOutputStream();
        outputStream.write("收到".getBytes());

    } catch (IOException e) {
        throw new RuntimeException(e);
    } finally {
        //4.关闭流
        if(fileOutputStream!=null){
            try {
                fileOutputStream.close();
            } catch (IOException e) {
                throw new RuntimeException(e);
            }
        }
        if(inputStream!=null){
            try {
                inputStream.close();
            } catch (IOException e) {
                throw new RuntimeException(e);
            }
        }
        if(socket!=null){
            try {
                socket.close();
            } catch (IOException e) {
                throw new RuntimeException(e);
            }
        }
        if(fileOutputStream!=null){
            try {
                serverSocket.close();
            } catch (IOException e) {
                throw new RuntimeException(e);
            }
        }
    }
```

- 客户端（发送端）

```java
        Socket socket = null;
        OutputStream outputStream = null;
        FileInputStream fileInputStream = null;
        try {
            InetAddress localHost = InetAddress.getLocalHost();
            //1.实例化socket（定义接收端的ip和端口号）
            socket = new Socket(localHost, 65535);
            //2.实例化输出流
            outputStream = socket.getOutputStream();
            fileInputStream = new FileInputStream("in.jpg");
            byte[] b=new byte[1024];
            int len;
            //3.输出
            while((len=fileInputStream.read(b))!=-1){
                outputStream.write(b,0,len);
            }
            socket.shutdownOutput();
            InputStreamReader inputStreamReader = new InputStreamReader(socket.getInputStream());
            char[] chars=new char[10];

            while((len=inputStreamReader.read(chars))!=-1)
                System.out.println(chars);;
        } catch (IOException e) {
            throw new RuntimeException(e);
        } finally {
            //4.关闭流
            if(fileInputStream!=null) {
                try {
                    fileInputStream.close();
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }
            if(outputStream!=null){
                try {
                    outputStream.close();
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }
            if(socket!=null){
                try {
                    socket.close();
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }
        }
```

#### UDP编程示例

接收端

```java
    DatagramSocket datagramSocket = null;
    try {
        //1.定义socket
        datagramSocket = new DatagramSocket(9999);
        //2.定义数据
        byte[] bytes = new byte[1024];
        DatagramPacket datagramPacket = new DatagramPacket(bytes, 0, 1024);
        //3.接收数据
        datagramSocket.receive(datagramPacket);
        System.out.println(new String(datagramPacket.getData(),0,datagramPacket.getLength()));
    } catch (IOException e) {
        throw new RuntimeException(e);
    } finally {
        //4.关闭流
        datagramSocket.close();
    }
```

发送端

```java
    DatagramSocket datagramSocket = null;
    try {
        //1.定义socket
        datagramSocket = new DatagramSocket();
        //2.定义数据
        byte[] bytes ="udp".getBytes();
        DatagramPacket datagramPacket = new DatagramPacket(bytes,0,bytes.length, InetAddress.getLocalHost(),9999);
        //3.发送数据
        datagramSocket.send(datagramPacket);
    } catch (IOException e) {
        throw new RuntimeException(e);
    } finally {
        //4.关闭流
        datagramSocket.close();
    }
```

#### URL编程示例

```java
    //1.定义url
	URL url = new URL("http:127.0.0.1");
    //2.建立连接
	HttpURLConnection urlConnection = url.openConnection();
    urlConnection.connect();
    //3.获取输入流
	InputStream inputStream = urlConnection.getInputStream();
    /******
     * 4.流输入代码
     */
    //5.关闭连接、关闭流
	inputStream.close();
    urlConnection.disconnect();
```

## 反射机制

反射：程序在执行期间获取任意类的内部信息，并能直接操作任意对象的内部属性及方法

### 类的加载过程

<img src="https://cuicy-1314839020.cos.ap-shanghai.myqcloud.com/typora-user-images/image-20230218130002699.png" alt="image-20230218130002699" style="zoom: 67%;" />

<img src="https://cuicy-1314839020.cos.ap-shanghai.myqcloud.com/typora-user-images/image-20230218130251951.png" alt="image-20230218130251951" style="zoom:67%;" />

类加载器

<img src="https://cuicy-1314839020.cos.ap-shanghai.myqcloud.com/typora-user-images/image-20230218130357542.png" alt="image-20230218130357542" style="zoom:67%;" />

<img src="https://cuicy-1314839020.cos.ap-shanghai.myqcloud.com/typora-user-images/image-20230218130440430.png" alt="image-20230218130440430" style="zoom:67%;" />

### 获取Class类的实例

Class类：描述类的类

```java
	//1.调用类的属性
    Class<Person> personClass = Person.class;
    //2.调用Class类的静态方法（最常用）
    Class person = Class.forName("Person");
	//3.调用对象的方法
    Person person1 = new Person();
    Class<? extends Person> aClass = person1.getClass();
```

### 调用Class实例的方法来操作对象

#### 创建运行时类的对象

```java
	Person p =(Person) personClass.newInstance();
```

#### 调用对象的属性、方法、构造器

```java
    Field name = aClass.getDeclaredField("name");
    name.setAccessible(true);
    name.set(p,"cuicy");
```

还可以获取类、方法的注解、接口等等，使用getXxx方法即可，不再赘述
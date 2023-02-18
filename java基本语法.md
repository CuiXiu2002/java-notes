# Java基本语法

## 标识符命名规范

- 包名：全部小写，例如xxxyyyzzz
- 类名、接口名：大驼峰，XxxYyyZzz
- 变量名、方法名：小驼峰，xxxYyyZzz
- 常量名：下划线式，XXX_YYY_ZZZ

## 变量

- 整型还包括byte 1字节
- float变量定义时必须以f结尾
- char变量占两个字节（Unicode编码，中英文都可以）
- boolean变量只能取true或false（不能取0 1）

## 运算

- 当byte、char、short三种类型的变量做运算时，结果是int
- char+char 的结果是int

## 编码

- ASCII码：存英文字母和标点符号，1字节
- unicode：包括英文字母、标点符号以及其他国家的符号（中国），一般2字节。对于ASCII码在前面补0。没有落地
- utf-8：变长编码（1-6字节），保留了ASCII码,unicode的编码实现方式之一
- 在计算机内存中，统一使用Unicode编码，当需要保存到硬盘或者需要传输的时候，就转换为UTF-8编码。

<img src="https://cuicy-1314839020.cos.ap-shanghai.myqcloud.com/typora-user-images/image-20230216162430829.png" alt="image-20230216162430829" style="zoom: 67%;" />

## 获取键盘输入

使用scanner类来获取键盘输入。示例：

```java
Scanner scan=new Scanner(System.in);
int age=scan.nextInt()
```

## String常用方法

- charAt(int index)：获取指定位置的字符

## 流程控制

- switch-case 中 switch后面可以跟String类型的变量
- 带标签的break和continue：break flag;

## 数组

### 一维数组的初始化

- 静态初始化

```java
int[] arr=new int[]{1,2,3,4};
```

- 动态初始化

```java
int[] arr=new int[4];
```

- 错误情况

```java
int[] arr=new int[4]{1,2,3,4};
```

### 一维数组内存解析（JVM）

- 内存结构

<img src="https://cuicy-1314839020.cos.ap-shanghai.myqcloud.com/typora-user-images/image-20230129151557080.png" alt="image-20230129151557080" style="zoom:50%;" />

- 数组的内存结构

<img src="https://cuicy-1314839020.cos.ap-shanghai.myqcloud.com/typora-user-images/image-20230129151653072.png" alt="image-20230129151653072" style="zoom:50%;" />

### 二维数组初始化

- 静态初始化（每行的元素个数可以不同）

```java
int[][] arr=new int[][]{{1,2},{1,2,3},{1,2,3,4}};
```

- 动态初始化

```java
int[][] arr=new int[3][4];
int[][] arr2=new int[3][];
```

### 二维数组内存解析

<img src="https://cuicy-1314839020.cos.ap-shanghai.myqcloud.com/typora-user-images/image-20230129160147245.png" alt="image-20230129160147245" style="zoom:50%;" />

### Arrays工具类

- 常用方法

```java
boolean equals(a,b);
String toString(arr);
void sort(arr);
int binarySearch(arr,key)
```

- 使用示例

```java
boolean isEqual=Arrays.equals(a,b);
```


# Java面向对象

## 面向过程与面向对象

- 面向过程：强调的是功能行为，以函数为最小单位，考虑怎么做
- 面向对象：强调的是具备了功能的对象，以类、对象为最小单位，考虑谁来做

<img src="https://cuicy-1314839020.cos.ap-shanghai.myqcloud.com/typora-user-images/image-20230130124848857.png" alt="image-20230130124848857" style="zoom:50%;" />

## 内存解析

### 对象的内存解析

<img src="https://cuicy-1314839020.cos.ap-shanghai.myqcloud.com/typora-user-images/image-20230130165043608.png" alt="image-20230130165043608" style="zoom:50%;" />

引用类型的变量，只可能存储两类值：null或地址值

### 对象数组的内存解析

<img src="https://cuicy-1314839020.cos.ap-shanghai.myqcloud.com/typora-user-images/image-20230130172435276.png" alt="image-20230130172435276" style="zoom:50%;" />

### 赋值操作

- 基本数据类型

```java
int a=1;
int b=a;
```

直接将a的值赋给b，b和a没有关系

- 引用数据类型

```java
Object a=new Object("asd");
Object b=a;
```

将a所指向的对象地址赋给b，也就是说对象b改变，对象a也改变

## 匿名对象

```java
class a(){
    void test(){
    	System.out.println(1);
    }
}
class b(){
    void test(a a1){
    	System.out.println(1);
    }
}
public static void main(){
    b b1=new b();
    b1.test(new a());
}
```

用法：一般用于传入参数（理解内存解析）

## 可变个数形参

看源码时方便理解

```java
public void show(String ... args){
    System.out.println(args[0]);
}
```

只能放在最后一个参数

## 三大特性

### 封装性

#### 理解

- 不暴露私有的方法

- 对于用户而言，不是 “不能看见”，而是“不需要看见”，不需要知道内部的细节。便于用户调用，从而提高系统的可扩展性、可维护性。

#### 四种权限修饰符

<img src="https://cuicy-1314839020.cos.ap-shanghai.myqcloud.com/typora-user-images/image-20230131083337720.png" alt="image-20230131083337720" style="zoom:50%;" />

- 四种权限都能用来修饰类及类的内部结构
- 类的修饰符只能用缺省/public

### 继承性

#### 继承性的理解

```java
public class A extends B{}
```

一旦子类A继承父类B以后，子类A就获取了B中的所有属性和方法

特别地，父类中声明为private的属性或方法，子类继承父类以后，仍然认为获取了父类私有的结构，只是由于封装性的影响，子类不能直接调用父类的私有结而已

#### 方法的重写

子类继承父类以后，可以对父类中同名同参数的方法，进行覆盖操作 

规定：

- 子类中重写的方法的权限修饰符应该不小于父类中被重写的方法的权限修饰符（子类不能重写父类中private的方法）
- 子类中重写的方法抛出的异常应该不大于父类中被重写的方法的权限修饰符
- 父类被重写的方法的返回值是void，则子类重写的方法的返回值是void
- 父类被重写的方法的返回值是a类，则子类重写的方法的返回值可以是a类或a类的子类（扩大化）
- 父类被重写的方法的返回值是基本数据类型，则子类重写的方法的返回值必须相同
- 子类和父类中同名同参数的方法要么都是static的，要么都是非static的

#### super关键字

在子类的方法或构造器中，显式调用父类的方法或属性（类似于this）

```java
super.();             //调用方法或属性
super(...)				//调用构造器   
```

在构造器的首行，没有显式的调用this(...)或super(...)，则默认调用父类的无参构造super()

#### 子类对象实例化的全过程

当我们通过子类的构造器创建子类对象时，我们一定会直接或间接地调用其父类的构造器，进而调用父类的父类的构造器，直到调用过java.lang.object类中空参的构造器。正是因为加载过所有的父类的结构，所以才可以看到内存中有父类的结构。

![image-20230205101003202](https://cuicy-1314839020.cos.ap-shanghai.myqcloud.com/typora-user-images/image-20230205101003202.png)

#### 内存理解

- 若子类重写了父类方法，那么子类里定义的方法彻底覆盖了父类里的同名方法，系统将不可能把父类里的方法转移到子类中（多态性）
- 对于实例变量，子父类中定义的变量可以同时存在于内存中

### 多态性

#### 多态性的理解

```java
Person person=new Man();
```

父类的引用指向子类的对象

#### 多态性的使用

当调用子父类同名同参数的方法时，实际调用的是子类重写父类中的方法                --虚拟方法调用

有了对象的多态性之后，我们在编译期，只能调用父类中声明的方法；但在运行期，我们实际执行的是子类重写父类的方法。

总结：编译看左边，运行看右边

多态性是运行时行为

#### 多态性的使用举例

多用在方法的实参形参中

```java
class AnimalTest{
    public void show(Animal animal){
        animal.eat();      //调用猫或狗的吃方法
        animal.sleep();
    }
}
```

#### 向下转型

```java
Person person=new Man();        //多态
if(person instanceof Man)		//判断person是否是Man类型
	Man man=(Man)person;           //向下转型
```

## java bean

## object类的理解

如果我们没有显式声明一个类的父类的话，则此类直接继承于java.lang.Object类

即 Object类是所有java类的根父类

### ==和equals的比较

#### ==：

- 如果比较的是基本数据类型，比较两个变量保存的数据是否相等（不考虑数据类型）

- 如果比较的是引用数据类型，比较两个对象的地址值是否相同，即比较两个引用是否指向同一个实体

#### equals：

- 适用于引用数据类型
- Object类中定义的equals与==的作用是相同的，即比较地址值
- 像String、Date、文件类、包装类等都重写了equals方法，改为了比较两个对象的实体内容是否相同

### toString

- 当我们输出一个对象的引用，实际就是调用该对象的toString方法

- 像String、Date、文件类、包装类等都重写了toString方法，使得在调用时返回实体内容信息

## 包装类

### 理解

针对八种基本数据类型定义对应的引用数据类型

<img src="https://cuicy-1314839020.cos.ap-shanghai.myqcloud.com/typora-user-images/image-20230205170233896.png" alt="image-20230205170233896" style="zoom:50%;" />

### 基本数据类型、包装类、String之间的转换

<img src="https://cuicy-1314839020.cos.ap-shanghai.myqcloud.com/typora-user-images/image-20230205171025650.png" alt="image-20230205171025650"  />

- 自动装箱

```java
int num=10;
Integer in=num;
```

- 自动拆箱

```java
int num2=in;
```

- 有了自动装箱和自动拆箱之后，不需要考虑基本数据类型和包装类的转换
- 基本数据类型/包装类转String

```java
int a=1;
String stra=String.valueof(a);
```

- String转基本数据类型/包装类

```java
String a="1";
int inta=Integer.parseInt(a);
```

## 关键字

### static

static：静态的

关于静态变量、方法的使用，从生命周期的角度去理解

#### 静态变量（类变量）

- 我们创建了类的多个对象，多个对象共享同一个静态变量，在内存中存在唯一一份静态变量（存在方法区的静态域中）。

- 随着类的加载而加载

- 可以通过“类.静态变量”来调用

  > System.out  Math.PI

- 类变量 VS 实例变量

#### 静态方法

- 随着类的加载而加载

- 可以通过“类.静态方法”来调用

  > Math.sqrt()  Arrays.

- 静态方法中，只可以调用静态的变量和方法

- 操作静态变量的方法通常定义为静态方法

### final

final：最终的

#### final修饰类

此类不能被其他类所继承

#### final修饰方法

此方法不能被重写

#### final修饰变量

此时的变量变为常量（const）

static final修饰属性：全局常量

### abstract

抽象的

#### abstract修饰类

- 抽象类：此类不能再实例化
- 通过子类继承

#### abstract修饰方法

- 抽象方法只有方法的声明，没有方法体
- 包含抽象方法的类一定是抽象类
- 若子类重写了父类中所有的抽象方法，则此子类可以实例化；相反，则此子类也是抽象类

#### 抽象类的匿名子类

匿名的目的：省事

```java
//Person是抽象类，有抽象方法eat，method调用匿名子类
method(new Person(){
    @Override
    public void eat(){
        System.out.println("学生吃饭");
    }
});
```

#### 模板方法设计模式

固定的步骤在父类中写好，易变的部分声明为抽象方法，由子类实现

### interface

接口体现了多态性，可以用于方法的形参

#### 如何定义接口

JDK7之前：只能定义全局常量和抽象方法（书写时可以省略）

JDK8及以后：可以定义静态方法及默认方法

	- 接口中定义的静态方法，只能通过接口来调用
	- 通过类实现的对象，可以调用接口中的默认方法

#### 使用

开发中，通常用类实现（implements）接口

- 如果实现类实现了接口中所有的抽象方法，则此实现类可以实例化
- 如果实现类没有实现接口中所有的抽象方法，则此实现类仍为抽象类
- 一个类可以实现多个接口（弥补了单继承性的局限性）
- 接口与接口之间可以多继承

#### 使用举例

<img src="https://cuicy-1314839020.cos.ap-shanghai.myqcloud.com/typora-user-images/image-20230206221944090.png" alt="image-20230206221944090" style="zoom:50%;" />

## 类的特殊成员

### 代码块

#### 静态代码块

- 随着类的加载而执行，只执行一次
- 作用：初始化类的信息(static的属性)

```java
class block{
    static{
        
    }
}
```

#### 非静态代码块

- 执行在构造器之前

- 随着对象的加载而执行，每创建一个就执行一次
- 作用：初始化对象的属性等

```java
class block{
    {
        
    }
}
```

### 内部类

不难


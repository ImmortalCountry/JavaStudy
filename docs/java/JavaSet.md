# Java 综合知识点


## String，StringBuilder，StringBuffer

- String

```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    private final char value[];

    /** Cache the hash code for the string */
    private int hash; // Default to 0

    /** use serialVersionUID from JDK 1.0.2 for interoperability */
    private static final long serialVersionUID = -6849794470754667710L;

    /**
     * Class String is special cased within the Serialization Stream Protocol.
     *
     * A String instance is written into an ObjectOutputStream according to
     * <a href="{@docRoot}/../platform/serialization/spec/output.html">
     * Object Serialization Specification, Section 6.2, "Stream Elements"</a>
     */
    private static final ObjectStreamField[] serialPersistentFields =
        new ObjectStreamField[0];

    /**
     * Initializes a newly created {@code String} object so that it represents
     * an empty character sequence.  Note that use of this constructor is
     * unnecessary since Strings are immutable.
     */
    public String() {
        this.value = "".value;
    }

    /**
     * Initializes a newly created {@code String} object so that it represents
     * the same sequence of characters as the argument; in other words, the
     * newly created string is a copy of the argument string. Unless an
     * explicit copy of {@code original} is needed, use of this constructor is
     * unnecessary since Strings are immutable.
     *
     * @param  original
     *         A {@code String}
     */
    public String(String original) {
        this.value = original.value;
        this.hash = original.hash;
    }
}
```

- AbstractStringBuilder

```java
abstract class AbstractStringBuilder implements Appendable, CharSequence {
    char[] value;
    int count;
    AbstractStringBuilder() {
    }
    AbstractStringBuilder(int capacity) {
        value = new char[capacity];
}
```

>AbstractStringBuilder 中采用一个char数组来保存字符串，char 数组有一个初始大小（16），当 append 的字符串长度超过当前 char 数组容量时，则对 char 数组进行动态扩容，然后将当前char 数组拷贝到新的位置，因为重新分配内存并拷贝的开销比较大，所以每次都是进行固定扩容2 倍的方式（使用JNI方法 System.arraycopy() ）。
所以在构造 StirngBuffer 或 StirngBuilder 时应尽可能指定它们的容量。

**问：String为什么不可变 ？**

由上图源码可知 String 类 被 final 修饰，所以不可继承。而且 `char value[]` 被 final 修饰，所以不可以改变。而 str = strA + strB 实际上是在堆上生成 str 然后将 StrA 与 StrB 做参数给 str，且 StrA 与 StrB 也是在堆上开辟了空间的。

**问：线程安全 ？**

由于String对象不可变，所以可以理解为是**线程安全**的。

AbstractStringBuilder 是 StringBuilder 与 StringBuffer 的公共父类，定义了一些字符串的基本操作，如 expandCapacity、append、insert、indexOf 等公共方法。

StringBuffer 对**方法加了同步锁**或者**对调用的方法加了同步锁**，所以是**线程安全**的。

StringBuilder 并没有对方法进行加同步锁，所以是**非线程安全**的。　

**使用选择**

- 当单线程操作大量数据时，使用 StringBuilder，速度更快（10%~15%左右的性能提升），无同步性能开销。
- 多线程操作大量数据时，使用 StringBuffer。可用于全局变量中。
- String 不要 使用 + ，因为这样会新生成对象，消耗性能。
- 当无法判断是否应用于多线程或者性能原因主要不是在 StringBuffer 最好还是使用StringBuffer。

## String 例子。

```java
public class StringDemo {
    public static void main(String[] args) {
        String a = "abc";
        String b = "abc";
        String c = new String("abc");
        String d = "ab" + "c";
        String e = new String(a);
        System.out.println(a == b); // true
        System.out.println(a == c); // false
        System.out.println(a == e); // false
        System.out.println(a == d); // true
        System.out.println(c == e); // false
        // equals 比较内容
    }
}
```

## Java 基础 类型与其对应的装箱类

| **数据类型** | 字节大小                | 包装器    | **默认值** |
| :----------- | ----------------------- | --------- | :--------- |
| byte         | 1                       | Byte      | 0          |
| short        | 2                       | Short     | 0          |
| int          | 4                       | Integer   | 0          |
| long         | 8                       | Long      | 0L         |
| float        | 4                       | Float     | 0.0f       |
| double       | 8                       | Double    | 0.0d       |
| char         | 2                       | Character | 'u0000'    |
| boolean      | 只有true和false两个取值 | Boolean   | false      |

- 十六进制整型常量：以十六进制表示时，需以 0x 或 0X 开头，如 0xff, 0X9A。
- 八进制整型常量：八进制必须以 0 开头，如 0123，034。
- 长整型：长整型必须以 L 作结尾，如 9L, 342L。
- 浮点数常量：由于小数常量的默认类型是 double 型，所以 float 类型的后面一定要加 f(F)。同样带小数的变量默认为 double 类型。

**Java会自动将 基本类型 和 他们对应的 引用类型 装箱与拆箱，即 int ---> Integer；Integer --->int**

## 在 Java 中定义一个不做事且没有参数的构造方法的作用

Java 程序在执行子类的构造方法之前，如果没有用 super() 来调用父类特定的构造方法，则会调用父类中 “没有参数的构造方法”。因此，如果父类中只定义了有参数的构造方法，而在子类的构造方法中又没有用 super() 来调用父类中特定的构造方法，则编译时将发生错误，因为 Java 程序在父类中找不到没有参数的构造方法可供执行。解决办法是在父类里加上一个不做事且没有参数的构造方法。 　

## final，static，this，super，finalize()，NaN

- final

1. 方法和类都可以用 final 修饰，final 修饰的类是不能被继承的。
2. final 修饰的方法是不能被子类重写。
3. final 修饰的属性就是常量。
4. 对于一个final变量，如果是基本数据类型的变量，则其数值一旦在初始化之后便不能更改；如果是引用类型的变量，则在对其初始化之后便不能再让其指向另一个对象。
5. 当用final修饰一个类时，表明这个类不能被继承。final类中的所有成员方法都会被隐式地指定为final方法。
6. 使用 final 方法的原因有两个。第一个原因是把方法锁定，以防任何继承类修改它的含义；第二个原因是效率。在早期的Java实现版本中，会将 final 方法转为内嵌调用。但是如果方法过于庞大，可能看不到内嵌调用带来的任何性能提升（现在的Java版本已经不需要使用final方法进行这些优化了）。类中所有的private方法都隐式地指定为final。

**初始化：**

1. 在定义变量时直接赋值
2. 声明完变量后在构造方法中为其赋值

- static

1. 属性和方法都可以用 static 修饰。
2. 被 static 修饰的属于类。可直接使用类名.属性和方法名。  
3. 只有内部类可以使用 static 关键字修饰，调用直接使用类名.内部类类名进行调用。   
static可以独立存在，静态块： 静态代码块定义在类中方法外, 静态代码块在非静态代码块之前执行(静态代码块—>非静态代码块—>构造方法)。 该类不管创建多少对象，静态代码块只执行一次。
4. 静态内部类与非静态内部类之间存在一个最大的区别: 非静态内部类在编译完成之后会隐含地保存着一个引用，该引用是指向创建它的外围类，但是静态内部类却没有。没有这个引用就意味着：1. 它的创建是不需要依赖外围类的创建。2. 它不能使用任何外围类的非static成员变量和方法。

- this

**this 关键字用于引用类的当前实例**

- super

**super关键字用于从子类访问父类的变量和方法**

**注意**

1. 在构造器中使用 super（） 调用父类中的其他构造方法时，该语句必须处于构造器的首行，否则编译器会报错。另外，this 调用本类中的其他构造方法时，也要放在首行。
2. this、super不能用在 static 方法中。

- NaN

NaN：不是一个数字。 0/0 == NaN
检查是否是数字：Double.isNaN(12.1)。

- finalize()

Object 里的方法。调用 gc 时会自动执行。可用于释放资源。

## 重载与重写

- 重载： 发生在同一个类中。方法名一样，参数类型不同和个数不同、顺序不同，方法返回值和访问修饰符可以不同，无法以返回值类型作为重载函数的区分标准。发生在编译时。　
- 重写： 发生在父子类中，具有相同的方法名、返回类型和参数表。如果父类方法访问修饰符为 private 则子类就不能重写该方法。新方法将覆盖原有的方法。如需父类中原有的方法，可使用super关键字，该关键字引用了当前类的父类。
- 父类的私有属性和构造方法并不能被继承，构造器不能被重写，但可以 重载。
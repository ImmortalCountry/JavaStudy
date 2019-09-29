# 集合总体概括

![Collection](https://raw.githubusercontent.com/ImmortalCountry/BlogPhotos/master/%E6%8E%A5%E5%8F%A3.png)

## Collection

所有集合类的基本接口

Collection是最基本的集合接口，声明了适用于JAVA集合（只包括Set和List）的通用方法。 Set 、Queue和List 都继承了Conllection。 



### 接口的方法：

```java
boolean add(Object o) ：向集合中加入一个对象的引用
void clear()：删除集合中所有的对象，即不再持有这些对象的引用
boolean isEmpty()：判断集合是否为空
boolean contains(Object o) ：判断集合中是否持有特定对象的引用
Iterartor iterator() ：返回一个Iterator对象，可以用来遍历集合中的元素
boolean remove(Object o) ：从集合中删除一个对象的引用
int size() ：返回集合中元素的数目
Object[] toArray()：返回一个数组，该数组中包括集合中的所有元素
```

**注意：Iterator() 和toArray() 方法都用于集合的所有的元素，前者返回一个Iterator对象，后者返回一个包含集合中所有元素的数组。**

### Iterator接口声明了如下方法

```java
hasNext()：判断集合中元素是否遍历完毕，如果没有，就返回true
next() ：返回下一个元素
remove()：从集合中删除上一个有next()方法返回的元素
```

### 特点

* 不允许重复。
* 有序
# Set

Set是集合。
Set继承Collection接口，Set具有与Collection完全一样的接口，因此没有任何额外的功能。Set很简单，集合中的对象无序。无序既是：存入顺序与具体内部存储顺序无关，简单来说就是取出的时候和你取顺序的不一样。

## Set 的add()方法是如何判断对象是否已经存放在集合中？

```java
Iterator iterator = set.iterator();
while(it.hasNext()){
    String oldStr = it.next();
    if(newStr.equals(oldStr)){
    	isExists=true;
    }
}
```

## Set的功能方法：

```java
Set：存入Set的每个元素都必须是唯一的，因为Set不保存重复元素。加入Set的元素必须定义equals()方法以确保对象的唯一性。Set与Collection有完全一样的接口。Set接口不保证维护元素的次序。
HashSet：为快速查找设计的Set。存入HashSet的对象必须定义hashCode()。
TreeSet： 保存次序的Set, 底层为树结构。使用它可以从Set中提取有序的序列。
LinkedHashSet：具有HashSet的查询速度，且内部使用链表维护元素的顺序(插入的次序)。于是在使用迭代器遍历Set时，结果会按元素插入的次序显示
```

## 特点：

- 无序，即插入顺序与读取顺序不同。
- 元素不重复，但是其实现类有的有序。

## 实现类

### HashSet

### LinkedHashSet

### TreeSet
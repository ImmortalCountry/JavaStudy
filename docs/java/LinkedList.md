# `LinkedList`

开篇前，说一下 `Collection.toArray()`
这个方法很重要，不管是 `ArrayList`、`LinkedList` 在批量 `add` 的时候，都会先转化成数组去做。 因为数组可以用for循环直接遍历。比较方便高效

## 简介
1. `LinkedList` 是一个实现了 `List` 接口和 `Deque` 接口的双端链表。
2. `LinkedList` 是线程不安全的，允许元素为 `null` 的双向链表。
3. 其底层数据结构是双向链表，它实现 `List<E>`,` Deque<E>`, `Cloneable`, `java.io.Serializable` 接口。
4. 它实现了 `Deque<E>`，使得 `LinkedList` 类也具有队列的特性，所以它也可以作为一个双端队列。
5. 和 `ArrayList` 比，没有实现 `RandomAccess` ，所以随机访问元素速度较慢。需要随机访问元素时，时间效率很低，虽然底层在根据下标查询 `Node` 的时候，会根据 `index` 判断目标 `Node` 在前半段还是后半段，然后决定是顺序还是逆序查询，以提升时间效率。不过随着n的增大，总体时间效率依然很低。
6. `LinkedList` 不是线程安全的，如果想使 `LinkedList` 变成线程安全的，可以调用静态类 `Collections` 类中的 `synchronizedList` 方法。
```java
List list=Collections.synchronizedList(new LinkedList(...));
```
7. 当每次增、删时，都会修改 `modCount`

## 方法

`LinkedList`类扩展了`AbstractSequentialList`类并实现了`List`接口。它提供了一个链表数据结构。

以下是`LinkedList`类支持的构造函数。

| 编号 | 构造函数                   | 描述                                                    |
| ---- | -------------------------- | ------------------------------------------------------- |
| 1    | `LinkedList()`             | 此构造函数构建一个空链表。                              |
| 2    | `LinkedList(Collection c)` | 此构造函数构建一个链表，它使用集合`c`的元素进行初始化。 |

除了从父类继承的方法之外，`LinkedList`还定义了以下方法 - 

| 编号 | 方法                                      | 描述                                                         |
| ---- | ----------------------------------------- | ------------------------------------------------------------ |
| 1    | `void add(int index, Object element)`     | 将指定元素插入此列表中的指定位置索引。如果指定的索引超出范围(`index<0 或 index> size()`)，则抛出`IndexOutOfBoundsException`异常。 |
| 2    | `boolean add(Object o)`                   | 将指定的元素追加到此列表的末尾。                             |
| 3    | `boolean addAll(Collection c)`            | 将指定集合中的所有元素按指定集合的迭代器返回的顺序附加到此列表的末尾。如果指定的集合为`null`，则抛出`NullPointerException`异常。 |
| 4    | `boolean addAll(int index, Collection c)` | 从指定位置开始，将指定集合中的所有元素插入此列表。如果指定的集合为`null`，则抛出`NullPointerException`异常。 |
| 5    | `void addFirst(Object o)`                 | 在此列表的开头插入给定元素。                                 |
| 6    | `void addLast(Object o)`                  | 将给定元素追加到此列表的末尾。                               |
| 7    | `void clear()`                            | 从此列表中删除所有元素。                                     |
| 8    | `Object clone()`                          | 返回此`LinkedList`的浅表副本。                               |
| 9    | `boolean contains(Object o)`              | 如果此列表包含指定的元素，则返回`true`。当且仅当此列表包含至少一个元素`e`时才返回`true`，即，`(o == null？e == null：o.equals(e))`。 |
| 10   | `Object get(int index)`                   | 返回此列表中指定位置的元素。如果指定的索引超出范围(`index < 0 或 index> = size()`)，则抛出`IndexOutOfBoundsException`异常。 |
| 11   | `Object getFirst()`                       | 返回此列表中的第一个元素。如果此列表为空，则抛出`NoSuchElementException`异常。 |
| 12   | `Object getLast()`                        | 返回此列表中的最后一个元素。如果此列表为空，则抛出`NoSuchElementException`异常。 |
| 13   | `int indexOf(Object o)`                   | 返回指定元素第一次出现的列表中的索引，如果列表不包含此元素，则返回`-1`。 |
| 14   | `int lastIndexOf(Object o)`               | 返回指定元素最后一次出现的列表中的索引，如果列表不包含此元素，则返回`-1`。 |
| 15   | `ListIterator listIterator(int index)`    | 从列表中的指定位置开始，返回此列表中元素的列表迭代器(按正确顺序)。如果指定的索引超出范围(`index < 0 或 index> = size()`)，则抛出`IndexOutOfBoundsException`异常。 |
| 16   | `Object remove(int index)`                | 删除此列表中指定位置的元素。如果此列表为空，则抛出`NoSuchElementException`异常。 |
| 17   | `boolean remove(Object o)`                | 删除此列表中第一次出现的指定元素。如果此列表为空，则抛出`NoSuchElementException`异常。如果指定的索引超出范围(`index < 0 或 index >= size()`)，则抛出`IndexOutOfBoundsException`异常。 |
| 18   | `Object removeFirst()`                    | 从此列表中删除并返回第一个元素。如果此列表为空，则抛出`NoSuchElementException`异常。 |
| 19   | `Object removeLast()`                     | 从此列表中删除并返回最后一个元素。如果此列表为空，则抛出`NoSuchElementException`异常。 |
| 20   | `Object set(int index, Object element)`   | 用指定的元素替换此列表中指定位置的元素。如果指定的索引超出范围(`index < 0 或 index >= size()`)，则抛出`IndexOutOfBoundsException`异常。 |
| 21   | `int size()`                              | 返回此列表中的元素数量。                                     |
| 22   | `Object[] toArray()`                      | 以正确的顺序返回包含此列表中所有元素的数组。如果指定的数组为`null`，则抛出`NullPointerException`异常。 |
| 23   | `Object[] toArray(Object[] a)`            | 以正确的顺序返回包含此列表中所有元素的数组; 返回数组的运行时类型是指定数组的运行时类型。 |

## 源码分析

```java
public class LinkedList<E>
    extends AbstractSequentialList<E>
    implements List<E>, Deque<E>, Cloneable, java.io.Serializable
{
	// 集合元素数量
    transient int size = 0;

    /**
     * 链表头节点
     * Invariant: (first == null && last == null) ||
     *            (first.prev == null && first.item != null)
     */
    transient Node<E> first;

    /**
     * 链表尾节点
     * Invariant: (first == null && last == null) ||
     *            (last.next == null && last.item != null)
     */
    transient Node<E> last;

    /**
     * Constructs an empty list.
     */
    public LinkedList() {
    }
}
```

### 内部 `Node` 类

```java
private static class Node<E> {
	// 元素值
	E item;
	// 后置节点
	Node<E> next;
	// 前置节点
	Node<E> prev;

	Node(Node<E> prev, E element, Node<E> next) {
		this.item = element;
		this.next = next;
		this.prev = prev;
	}
}
```
### `add`方法

```java
/**
* 将元素添加到链表尾部
* @param e element to be appended to this list
* @return {@code true} (as specified by {@link Collection#add})
*/
public boolean add(E e) {
	linkLast(e);
    return true;
}

/**
  * 链接使e作为最后一个元素。
  */
void linkLast(E e) {
	// 记录原尾部节点
    final Node<E> l = last;
    // 以原尾部节点为新节点的前置节点
    final Node<E> newNode = new Node<>(l, e, null);
    // 新建节点，更新尾部节点
    last = newNode;
    // 如果原链表为空链表，需要额外更新头结点
    if (l == null)
        first = newNode;
    else
        // 否则指向后继元素也就是指向下一个元素
        l.next = newNode;
    size++;
    modCount++;
}
```

### `add(int index,E e)` 

```java
	// 在指定位置添加元素
    public void add(int index, E element) {
    	// 检查索引是否处于[0-size]之间
        checkPositionIndex(index);
		
		// 如果是最后一个元素
        if (index == size)
        	// 添加在链表尾部
            linkLast(element);
        else
        	// 否则添加在链表中间
            linkBefore(element, node(index));
    }
```

**`linkBefore` 方法需要给定两个参数，一个插入节点的值，一个指定的 `node`，所以我们又调用了 `Node(index)` 去找到 `index` 对应的`node`**

### `addAll`

```java
// 批量添加元素到尾部
public boolean addAll(int index, Collection<? extends E> c) {
		// 检查是否越界【0-size】
        checkPositionIndex(index);
        // 目标集合数组
        Object[] a = c.toArray();
        // 新增元素数量
        int numNew = a.length;
        // 如果数量为0，就不添加了
        if (numNew == 0)
            return false;
		// index节点的前置节点和后继节点
        Node<E> pred, succ;
        // 在链表尾部追加数据
        if (index == size) {
        	// size（队尾）节点的后置节点一定为null
            succ = null;
            // 前置节点就是size（原队尾）
            pred = last;
        } else {
        	// index 节点作为后置节点
            succ = node(index);
            // 前置节点是 index 节点的前一个节点
            pred = succ.prev;
        }
		// 链表批量增加，是靠for循环遍历原数组，依次执行插入节点操作。对比ArrayList是通过System.arraycopy完成批量增加的
        for (Object o : a) {
            @SuppressWarnings("unchecked") E e = (E) o;
            // 以前置节点 和 元素值e，构建new一个新节点，
            Node<E> newNode = new Node<>(pred, e, null);
			// 如果前置节点是空
            if (pred == null)
            	// 头结点
                first = newNode;
            else
            	// 否则插在前置节点后面
                pred.next = newNode;
            // 指向下一个节点再添加
            pred = newNode;
        }
		// 如果后继节点是 null。 说明此时是在队尾append的。
        if (succ == null) {
            last = pred;
        } else {
        	// 否则是在队中插入的节点 ，更新前置节点 后继节点
            pred.next = succ;
            // 更新后继节点的前置节点
            succ.prev = pred;
        }
		// size  变大
        size += numNew;
        modCount++;
        return true;
    }
    
//根据 index 查询出 Node
Node<E> node(int index) {
// assert isElementIndex(index);
// 通过下标获取某个node 的时候，（增、查 ），会根据 index 处于前半段还是后半段 进行一个折半，以提升查询效率
    if (index < (size >> 1)) {
        Node<E> x = first;
        for (int i = 0; i < index; i++)
            x = x.next;
        return x;
    } else {
        Node<E> x = last;
        for (int i = size - 1; i > index; i--)
            x = x.prev;
        return x;
    }
}

// 检查是否越界
private void checkPositionIndex(int index) {
    if (!isPositionIndex(index))
        throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
}

// 检查 是不是正确的索引
private boolean isPositionIndex(int index) {
	return index >= 0 && index <= size;  // 插入时的检查，下标可以是size [0, size]
}

```

#### 小结：

- 链表批量增加，是靠for循环遍历原数组，依次执行插入节点操作。对比 `ArrayList` 是通过 `System.arraycopy` 完成批量增加的
- 通过下标获取某个 `node` 的时候，`（add select）` ，会根据 `index` 处于前半段还是后半段进行一个折半，以提升查询效率。
- `addAll` 步骤

	1. 检查 `index` 范围是否在 `size` 之内
	2. `toArray()`方法把集合的数据存到对象数组中
	3. 得到插入位置的前驱和后继节点
	4. 遍历数据，将数据插入到指定位置

### `addFirst(E e)`

```java
// 将元素添加到链表头部
public void addFirst(E e) {
	linkFirst(e);
}
private void linkFirst(E e) {
    final Node<E> f = first;
    // 新建节点，以头节点为后继节点
    final Node<E> newNode = new Node<>(null, e, f);
    first = newNode;
    // 如果链表为空，last节点也指向该节点
    if (f == null)
        last = newNode;
    // 否则，将头节点的前驱指针指向新节点，也就是指向前一个元素
    else
        f.prev = newNode;
    size++;
    modCount++;
}

```

### `addLast(E e)`

```java
// 将元素添加到链表尾部，与 add(E e) 方法一样
public void addLast(E e) {
        linkLast(e);
}
```

### 删除方法

**`remove()`** ,**`removeFirst()`,`pop()`:** 删除头节点

```
public E pop() {
        return removeFirst();
    }
public E remove() {
        return removeFirst();
    }
public E removeFirst() {
        final Node<E> f = first;
        if (f == null)
            throw new NoSuchElementException();
        return unlinkFirst(f);
}
```

**`removeLast(),pollLast()`:** 删除尾节点

```
public E removeLast() {
        final Node<E> l = last;
        if (l == null)
            throw new NoSuchElementException();
        return unlinkLast(l);
    }
public E pollLast() {
        final Node<E> l = last;
        return (l == null) ? null : unlinkLast(l);
    }
```

**区别：** `removeLast()`在链表为空时将抛出`NoSuchElementException`，而`pollLast()`方法返回null。



###  `remove`

```java

public E remove(int index) {
	// 检查是否越界 下标[0,size)
    checkElementIndex(index);
    // 从链表上删除某节点
    return unlink(node(index));
}
// 从链表上删除x节点
E unlink(Node<E> x) {
    // assert x != null;
    // 当前节点的元素值
    final E element = x.item;
    // 当前节点的后置节点
    final Node<E> next = x.next; 
    // 当前节点的前置节点
    final Node<E> prev = x.prev;

	// 如果前置节点为空(说明当前节点原本是头结点)
    if (prev == null) { 
        // 则头结点等于后置节点 
        first = next;  
    } else { 
        prev.next = next;
        // 将当前节点的 前置节点置空
        x.prev = null; 
    }
	// 如果后置节点为空（说明当前节点原本是尾节点）
    if (next == null) {
       // 则尾节点为前置节点
        last = prev;
    } else {
        next.prev = prev;
        // 将当前节点的 后置节点置空
        x.next = null;
    }
	// 将当前元素值置空
    x.item = null; 
    size--; 
    modCount++;  
    // 返回取出的元素值
    return element; 
}
private void checkElementIndex(int index) {
    if (!isElementIndex(index))
        throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
    }
// 下标[0,size)
private boolean isElementIndex(int index) {
    return index >= 0 && index < size;
}

```

### 删除链表中指定节点

```java
// 因为要考虑 null元素，也是分情况遍历
public boolean remove(Object o) {
	//  如果要删除的是 null 节点(从 remove 和 add 里可以看出，允许元素为null)
    if (o == null) {
   	    // 遍历每个节点 对比
        for (Node<E> x = first; x != null; x = x.next) {
            if (x.item == null) {
                unlink(x);
                return true;
            }
        }
    } else {
            for (Node<E> x = first; x != null; x = x.next) {
                if (o.equals(x.item)) {
                    unlink(x);
                    return true;
                }
            }
        }
        return false;
}
// 将节点 x ，从链表中删除
E unlink(Node<E> x) {
    // assert x != null;
    // 元素值，供返回
    final E element = x.item;
    // 保存当前节点的后置节点
    final Node<E> next = x.next;
    // 前置节点
    final Node<E> prev = x.prev;
	// 前置节点为null，
    if (prev == null) {
    	// 则首节点为next
        first = next;
    } else {
    	// 否则 更新前置节点的后置节点
        prev.next = next;
        // 记得将要删除节点的前置节点置null
        x.prev = null;
    }
    // 如果后置节点为null，说明是尾节点
    if (next == null) {
        last = prev;
     // 否则更新 后置节点的前置节点
    } else {
        next.prev = prev;
        // 记得删除节点的后置节点为null
        x.next = null;
    }
    // 将删除节点的元素值置 null，以便 GC 
    x.item = null;
    size--;
    modCount++;
    // 返回删除的元素值
    return element;
}

```

**删也一定会修改`modCount`。 按下标删，也是先根据index找到Node，然后去链表上`unlink`掉这个`Node`。 按元素删，会先去遍历链表寻找是否有该`Node`，考虑到允许`null`值，所以会遍历两遍，然后再去`unlink`它。**

### `set`

```java
public E set(int index, E element) {
	// 检查越界[0,size)
    checkElementIndex(index);
    // 取出对应的Node
    Node<E> x = node(index);
    // 保存旧值 供返回
    E oldVal = x.item;
    // 用新值覆盖旧值
    x.item = element;
    // 返回旧值
    return oldVal;
}

```

**改也是先根据index找到Node，然后替换值，改不修改modCount**

### `get`

```java
// 根据 index 查询节点
public E get(int index) {
	// 判断是否越界 [0,size)
    checkElementIndex(index);
    // 调用 node() 方法 取出 Node节点，
    return node(index).item; 
}


public int indexOf(Object o) {
    int index = 0;
    // 如果目标对象是null
    if (o == null) {
    // 遍历链表
        for (Node<E> x = first; x != null; x = x.next) {
            if (x.item == null)
                return index;
            index++;
        }
    } else {
        // 遍历链表
        for (Node<E> x = first; x != null; x = x.next) {
            if (o.equals(x.item))
                return index;
            index++;
        }
    }
    return -1;
}

// 从尾至头遍历链表，找到目标元素值为o的节点
public int lastIndexOf(Object o) {
    int index = size;
    if (o == null) {
        for (Node<E> x = last; x != null; x = x.prev) {
            index--;
            if (x.item == null)
                return index;
        }
    } else {
        for (Node<E> x = last; x != null; x = x.prev) {
            index--;
            if (o.equals(x.item))
                return index;
        }
    }
    return -1;
}

```

### 获取头结点的方法

```java
public E getFirst() {
        final Node<E> f = first;
        if (f == null)
            throw new NoSuchElementException();
        return f.item;
    }
public E element() {
        return getFirst();
    }
public E peek() {
        final Node<E> f = first;
        return (f == null) ? null : f.item;
    }

public E peekFirst() {
        final Node<E> f = first;
        return (f == null) ? null : f.item;
}

```

#### 区别： 
`getFirst(),element(),peek(),peekFirst() `这四个获取头结点方法的区别在于对链表为空时的处理，是抛出异常还是返回 null，其中`getFirst()` 和 `element()` 方法将会在链表为空时，抛出异常

`element()`方法的内部就是使用`getFirst()`实现的。它们会在链表为空时，抛出`NoSuchElementException`

### 获取尾节点的方法:

```java
 public E getLast() {
        final Node<E> l = last;
        if (l == null)
            throw new NoSuchElementException();
        return l.item;
    }
 public E peekLast() {
        final Node<E> l = last;
        return (l == null) ? null : l.item;
}
```

#### 两者区别
`getLast() `方法在链表为空时，会抛出`NoSuchElementException`，而`peekLast() `则不会，只是会返回 null。


**查不修改`modCount`**

### `toArray()`

```java
public Object[] toArray() {
    // new 一个新数组 然后遍历链表，将每个元素存在数组里，返回
    Object[] result = new Object[size];
    int i = 0;
    for (Node<E> x = first; x != null; x = x.next)
        result[i++] = x.item;
    return result;
}

```

### 总结

- 链表批量增加，是靠for循环遍历原数组，依次执行插入节点操作。对比`ArrayList`是通过`System.arraycopy`完成批量增加的。增加一定会修改`modCount`。
- 通过下标获取某个`node` 的时候，`（add select）`，会根据index处于前半段还是后半段 进行一个折半，以提升查询效率
- 删也一定会修改`modCount`。 按下标删，也是先根据`index`找到`Node`，然后去链表上`unlink`掉这个`Node`。 按元素删，会先去遍历链表寻找是否有该Node，如果有，去链表上`unlink`掉这个`Node`。
- 改也是先根据`index`找到`Node`，然后替换值。改不修改`modCount`。
- 查本身就是根据`index`找到`Node`。
- 所以它的`CRUD`操作里，都涉及到根据`index`去找到`Node`的操作。

## Demo

```java
import java.util.Iterator;
import java.util.LinkedList;

public class LinkedListDemo {

    public static void main(String[] args) {
        LinkedList<Integer> list = new LinkedList<>();
        list.add(1);
        list.addFirst(2);
        list.addLast(3);

        System.out.println("最初始的list " + list);

        System.out.println("getFirst() 获取第一个元素 " + list.getFirst());
        System.out.println("getLast() 获取最后一个元素 " + list.getLast());
        System.out.println("removeFirst() 删除第一个元素并返回 " + list.removeFirst());
        System.out.println("removeLast() 删除最后一个元素并返回 " + list.removeLast());
        System.out.println("After remove 1: " + list);
        System.out.println("contains() 方法判断列表是否包含 1 这个元素: " + list.contains(1));
        System.out.println("该list的大小 : " +list.size());

        // 删除所有
        list.clear();

        list.add(1);
        list.addFirst(2);
        list.addLast(3);

        System.out.println("remove(int index) 删除第 2 个元素 " + list.remove(2));
        System.out.println("remove(Object o) 删除 o 元素 " + list.remove((Integer)2));

        System.out.println("After remove 2: " + list);

        /************************** Search操作 ************************/
        System.out.println("-----------------------------------------");
        list.add(3);
        // 返回此列表中首次出现的指定元素的索引
        System.out.println("indexOf(3): " + list.indexOf(3)); 
        // 返回此列表中最后出现的指定元素的索引
        System.out.println("lastIndexOf(3): " + list.lastIndexOf(3));
        
        /************************** Queue操作 ************************/
        System.out.println("-----------------------------------------");
        // 获取但不移除此列表的头
        System.out.println("peek(): " + list.peek()); 
        // 获取但不移除此列表的头
        System.out.println("element(): " + list.element()); 
        // 获取并移除此列表的头
        list.poll(); 
        System.out.println("After poll():" + list);
        list.remove();
        // 获取并移除此列表的头
        System.out.println("After remove():" + list); 
        list.offer(4);
        // 将指定元素添加到此列表的末尾
        System.out.println("After offer(4):" + list); 

        /************************** Deque操作 ************************/
        System.out.println("-----------------------------------------");
        // 在此列表的开头插入指定的元素
        list.offerFirst(2); 
        System.out.println("After offerFirst(2):" + list);
        // 在此列表末尾插入指定的元素
        list.offerLast(5); 
        System.out.println("After offerLast(5):" + list);
        // 获取但不移除此列表的第一个元素
        System.out.println("peekFirst(): " + list.peekFirst()); 
        // 获取但不移除此列表的第一个元素
        System.out.println("peekLast(): " + list.peekLast()); 
        // 获取并移除此列表的第一个元素
        list.pollFirst(); 
        System.out.println("After pollFirst():" + list);
        // 获取并移除此列表的最后一个元素
        list.pollLast(); 
        System.out.println("After pollLast():" + list);
        // 将元素推入此列表所表示的堆栈（插入到列表的头）
        list.push(2); 
        System.out.println("After push(2):" + list);
        // 从此列表所表示的堆栈处弹出一个元素（获取并移除列表第一个元素）
        list.pop();
        System.out.println("After pop():" + list);
        list.add(3);
        // 从此列表中移除第一次出现的指定元素（从头部到尾部遍历列表）
        list.removeFirstOccurrence(3); 
        System.out.println("After removeFirstOccurrence(3):" + list);
        // 从此列表中移除最后一次出现的指定元素（从尾部到头部遍历列表）
        list.removeLastOccurrence(3); 
        System.out.println("After removeFirstOccurrence(3):" + list);

        /************************** 遍历操作 ************************/
        System.out.println("-----------------------------------------");
        list.clear();
        for (int i = 0; i < 100000; i++) {
            list.add(i);
        }
        // 迭代器遍历
        long start = System.currentTimeMillis();
        Iterator<Integer> iterator = list.iterator();
        while (iterator.hasNext()) {
            iterator.next();
        }
        long end = System.currentTimeMillis();
        System.out.println("Iterator：" + (end - start) + " ms");

        // 顺序遍历(随机遍历)
        start = System.currentTimeMillis();
        for (int i = 0; i < list.size(); i++) {
            list.get(i);
        }
        end = System.currentTimeMillis();
        System.out.println("for：" + (end - start) + " ms");

        // 另一种for循环遍历
        start = System.currentTimeMillis();
        for (Integer i : list)
            ;
        end = System.currentTimeMillis();
        System.out.println("for2：" + (end - start) + " ms");

        // 通过pollFirst()或pollLast()来遍历LinkedList
        LinkedList<Integer> temp1 = new LinkedList<>();
        temp1.addAll(list);
        start = System.currentTimeMillis();
        while (temp1.size() != 0) {
            temp1.pollFirst();
        }
        end = System.currentTimeMillis();
        System.out.println("pollFirst()或pollLast()：" + (end - start) + " ms");

        // 通过removeFirst()或removeLast()来遍历LinkedList
        LinkedList<Integer> temp2 = new LinkedList<>();
        temp2.addAll(list);
        start = System.currentTimeMillis();
        while (temp2.size() != 0) {
            temp2.removeFirst();
        }
        end = System.currentTimeMillis();
        System.out.println("removeFirst()或removeLast()：" + (end - start) + " ms");
    }

}

```

[源码](LinkedList源码.md)


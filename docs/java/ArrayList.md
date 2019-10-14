# ArrayList

## 简介

1. ArrayList 的底层是**数组队列**，相当于动态数组。在添加大量元素前，应用程序可以**使用ensureCapacity操作来增加 ArrayList 实例的容量**。这可以减少递增式再分配的数量。
2. 允许元素为null
3. ArrayList具有数组的**随机访问**功能，适合读取，但是不适合删除、插入。因为需要移动数组。
4. 插入删除元素的时间复杂度为**O（n）**,求表长以及增加元素，取第 i 元素的时间复杂度为**O（1）**
5. 它继承于 **AbstractList**，实现了 List, RandomAccess, Cloneable, java.io.Serializable 这些接口。
6. ArrayList 继承了**AbstractList**，实现了List。它是一个数组队列，提供了相关的添加、删除、修改、遍历等功能。
7. ArrayList 实现了**RandomAccess** 接口， RandomAccess 是一个标志接口，此接口只有声明、没有方法体。表明实现这个这个接口的 List 集合是支持快速随机访问的。
8. ArrayList 实现了**Cloneable** 接口，此接口只有声明、没有方法体。即覆盖了函数 clone()，能被克隆。
9. ArrayList 实现**java.io.Serializable** 接口，这意味着ArrayList支持序列化，能通过序列化去传输。此接口只有声明、没有方法体、表示ArrayList支持序列化、即可以将ArrayList以流的形式通过ObjectInputStream/ObjectOutputStream来写/读。**只用实现了这个接口，类才可以在网络上传输然后反序列化。如分布式系统的pojo，如果不实现Serializable接口，那么就无法在多个系统之间传递。**
10. 每次修改结构时，增加导致扩容，或者删，都会修改modCount。
11. 线程操作不安全，建议在单线程中使用。多线程中可以用Vector 或者 CopyOnWriteArrayList，但Vector已经被边缘化了。

## API

```java
// Collection中定义的API
boolean             add(E object)
boolean             addAll(Collection<? extends E> collection)
void                clear()
boolean             contains(Object object)
boolean             containsAll(Collection<?> collection)
boolean             equals(Object object)
int                 hashCode()
boolean             isEmpty()
Iterator<E>         iterator()
boolean             remove(Object object)
boolean             removeAll(Collection<?> collection)
boolean             retainAll(Collection<?> collection)
int                 size()
<T> T[]             toArray(T[] array)
Object[]            toArray()
// AbstractList中定义的API
void                add(int location, E object)
boolean             addAll(int location, Collection<? extends E> collection)
E                   get(int location)
int                 indexOf(Object object)
int                 lastIndexOf(Object object)
ListIterator<E>     listIterator(int location)
ListIterator<E>     listIterator()
E                   remove(int location)
E                   set(int location, E object)
List<E>             subList(int start, int end)
// ArrayList新增的API
Object               clone()
void                 ensureCapacity(int minimumCapacity)
void                 trimToSize()
void                 removeRange(int fromIndex, int toIndex)
```

## 源码分析

```Java
package java.util;

import java.util.function.Consumer;
import java.util.function.Predicate;
import java.util.function.UnaryOperator;
import sun.misc.SharedSecrets;

public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
{
    private static final long serialVersionUID = 8683452581122892189L;

    /**
     * 默认初始容量为10
     */
    private static final int DEFAULT_CAPACITY = 10;

    /**
	 * 用于空实例的共享空数组实例
     */
    private static final Object[] EMPTY_ELEMENTDATA = {};

    /**
	 * 我们将其与EMPTY_ELEMENTDATA区分开来，以了解添加第一个元素时的膨胀程度。
     */
    private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};

    /**
	 * 保存ArrayList数据的数组
     */
    transient Object[] elementData; // 非私有以简化嵌套类访问

    /**
	 * ArrayList 所包含的实际元素数量
     */
    private int size;

    /**
     * 带初始容量的构造函数
     *
     * @param  initialCapacity  列表的初始容量
     * @throws IllegalArgumentException if the specified initial capacity
     *         is negative 参数是负的
     */
    public ArrayList(int initialCapacity) {
        if (initialCapacity > 0) {
			// 创建initialCapacity大小的数组
            this.elementData = new Object[initialCapacity];
        } else if (initialCapacity == 0) {
			// 指向空数组
            this.elementData = EMPTY_ELEMENTDATA;
        } else {
			// 负的抛异常
            throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
        }
    }

    /**
	 * 默认构造函数，DEFAULTCAPACITY_EMPTY_ELEMENTDATA 为0.初始化为10，
	 * 也就是说初始其实是空数组 当添加第一个元素的时候数组容量才变成10
     */
    public ArrayList() {
        this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
    }

    /**
	 * 构造一个包含指定集合的元素的列表，按照它们由集合的迭代器返回的顺序。
     *
     * @param c 提供的集合
     * @throws NullPointerException if the specified collection is null
     */
    public ArrayList(Collection<? extends E> c) {
        elementData = c.toArray();
		// 如果指定集合元素个数不为0
        if ((size = elementData.length) != 0) {
            // c.toArray 可能返回的不是Object类型的数组所以加上下面的语句用于判断
			// 这里用到了反射里面的getClass()方法
            if (elementData.getClass() != Object[].class)
                elementData = Arrays.copyOf(elementData, size, Object[].class);
        } else {
            // 用空数组代替
            this.elementData = EMPTY_ELEMENTDATA;
        }
    }

    /**
	 * 修改这个ArrayList实例的容量是列表的当前大小。 
	 * 应用程序可以使用此操作来最小化ArrayList实例的存储。 
     */
    public void trimToSize() {
        modCount++;
        if (size < elementData.length) {
            elementData = (size == 0)
              ? EMPTY_ELEMENTDATA
              : Arrays.copyOf(elementData, size);
        }
    }

    /**
	 * ArrayList 扩容机制
     * @param   minCapacity   所需最小容量
     */
    public void ensureCapacity(int minCapacity) {
        int minExpand = (elementData != DEFAULTCAPACITY_EMPTY_ELEMENTDATA)
            // any size if not default element table
            ? 0
            // larger than default for default empty table. It's already
            // supposed to be at default size.
            : DEFAULT_CAPACITY;

        if (minCapacity > minExpand) {
            ensureExplicitCapacity(minCapacity);
        }
    }

    private static int calculateCapacity(Object[] elementData, int minCapacity) {
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
            return Math.max(DEFAULT_CAPACITY, minCapacity);
        }
        return minCapacity;
    }
	// 得到最小扩容量
    private void ensureCapacityInternal(int minCapacity) {
        ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
    }
	// 判断是否需要扩容
    private void ensureExplicitCapacity(int minCapacity) {
        modCount++;

        // overflow-conscious code 有溢出意识的代码
		// 最小容量大于你本身长度
        if (minCapacity - elementData.length > 0)
			// 调用grow方法进行扩容，调用此方法代表已经开始扩容了
            grow(minCapacity);
    }

    /**
     *  要分配的最大数组大小
     */
    private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;

    /**
	 * ArrayList扩容的核心方法。
     * @param minCapacity 所需的最小容量
     */
    private void grow(int minCapacity) {
        // overflow-conscious code
		// 旧的容量
        int oldCapacity = elementData.length;
		// 新的容量扩容 1.5 倍
        int newCapacity = oldCapacity + (oldCapacity >> 1);
		// 如果扩容后的新容量还是比传进来的小，那么新容量就是minCapacity
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
		// 再检查新容量是否超出了ArrayList所定义的最大容量，
		// 若超出了，则调用hugeCapacity()来比较minCapacity和 MAX_ARRAY_SIZE，
		// 如果minCapacity大于MAX_ARRAY_SIZE，则新容量则为Interger.MAX_VALUE，
		// 否则，新容量大小则为 MAX_ARRAY_SIZE。
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
    }


    private static int hugeCapacity(int minCapacity) {
        if (minCapacity < 0) // overflow
            throw new OutOfMemoryError();
        return (minCapacity > MAX_ARRAY_SIZE) ?
            Integer.MAX_VALUE :
            MAX_ARRAY_SIZE;
    }

    /**
     * @return 返回列表实际数据大小
     */
    public int size() {
        return size;
    }

    /**
   
     * @return 返回列表是否为空
     */
    public boolean isEmpty() {
        return size == 0;
    }

    /**
     * @param o 要检查其在此列表中是否存在的元素
     * @return 返回列表是否包含指定的元素。
     */
    public boolean contains(Object o) {
        return indexOf(o) >= 0;
    }

    /**
     * 返回此列表中指定元素的首次出现的索引，如果此列表不包含此元素，则为-1
     */
    public int indexOf(Object o) {
        if (o == null) {
            for (int i = 0; i < size; i++)
                if (elementData[i]==null)
                    return i;
        } else {
            for (int i = 0; i < size; i++)
                if (o.equals(elementData[i]))
                    return i;
        }
        return -1;
    }

    /**
     * 返回列表中指定元素的最后一次出现的索引，如果此列表不包含元素，则返回-1
     */
    public int lastIndexOf(Object o) {
        if (o == null) {
            for (int i = size-1; i >= 0; i--)
                if (elementData[i]==null)
                    return i;
        } else {
            for (int i = size-1; i >= 0; i--)
                if (o.equals(elementData[i]))
                    return i;
        }
        return -1;
    }

    /**
     * @return 返回此ArrayList实例的浅拷贝。（元素本身不被复制。） 
     */
    public Object clone() {
        try {
            ArrayList<?> v = (ArrayList<?>) super.clone();
            v.elementData = Arrays.copyOf(elementData, size);
            v.modCount = 0;
            return v;
        } catch (CloneNotSupportedException e) {
            // this shouldn't happen, since we are Cloneable
            throw new InternalError(e);
        }
    }

    /**
     * 以正确的顺序（从第一个到最后一个元素）返回一个包含此列表中所有元素的数组。 
     * 返回的数组将是“安全的”，因为该列表不保留对它的引用。 （换句话说，这个方法必须分配一个新的数组）。
     * 因此，调用者可以自由地修改返回的数组。 此方法充当基于阵列和基于集合的API之间的桥梁。
     */
    public Object[] toArray() {
        return Arrays.copyOf(elementData, size);
    }

    /**
     * 以正确的顺序返回一个包含此列表中所有元素的数组（从第一个到最后一个元素）; 
     * 返回的数组的运行时类型是指定数组的运行时类型。 如果列表适合指定的数组，则返回其中。 
     * 否则，将为指定数组的运行时类型和此列表的大小分配一个新数组。 
     * 如果列表适用于指定的数组，其余空间（即数组的列表数量多于此元素），则紧跟在集合结束后的数组中的元素设置为null 。
     *（这仅在调用者知道列表不包含任何空元素的情况下才能确定列表的长度。）
     *
     * @param 一个列表元素要存储到的数组（如果足够大）；
	 * 否则，将为此目的分配一个具有相同运行时类型的新数组。
     * @return 包含列表元素的数组
     * @throws ArrayStoreException如果指定数组的运行时类型不是此列表中每个元素的运行时类型的超类型
     * @throws NullPointerException if the specified array is null
     */
    @SuppressWarnings("unchecked")
    public <T> T[] toArray(T[] a) {
        if (a.length < size)
            // Make a new array of a's runtime type, but my contents:
            return (T[]) Arrays.copyOf(elementData, size, a.getClass());
        System.arraycopy(elementData, 0, a, 0, size);
        if (a.length > size)
            a[size] = null;
        return a;
    }

    // Positional Access Operations

    @SuppressWarnings("unchecked")
    E elementData(int index) {
        return (E) elementData[index];
    }

    /**
     * 返回此列表中指定位置的元素。
     *
     * @param  索引
     * @return 元素
     * @throws IndexOutOfBoundsException {@inheritDoc}索引越界
     */
    public E get(int index) {
		// 检查索引是否越界
        rangeCheck(index);

        return elementData(index);
    }

    /**
     * 用指定的元素替换此列表中指定位置的元素。 
     *
     * @param 要替换的位置
     * @param 元素
     * @return 被替换的元素
     * @throws IndexOutOfBoundsException {@inheritDoc}
     */
    public E set(int index, E element) {
        rangeCheck(index);

        E oldValue = elementData(index);
        elementData[index] = element;
        return oldValue;
    }

    /**
     * 将指定的元素追加到此列表的末尾。 
     *
     * @param 要添加的元素
     * @return <tt>true</tt> (as specified by {@link Collection#add})
     */
    public boolean add(E e) {
        ensureCapacityInternal(size + 1);  // Increments（增加） modCount!!
        elementData[size++] = e;
        return true;
    }

    /**
	 * 在此列表中的指定位置插入指定的元素。 
     * @param index index at which the specified element is to be inserted
     * @param element element to be inserted
     * @throws IndexOutOfBoundsException {@inheritDoc}
     */
    public void add(int index, E element) {
        rangeCheckForAdd(index);

        ensureCapacityInternal(size + 1);  // Increments modCount!!
		// 将从index开始之后的所有成员后移一个位置；
		// 将element插入index位置；最后size加1。
		// arraycopy()这个实现数组之间复制的方法一定要看一下，
		// 下面就用到了arraycopy()方法实现数组自己复制自己
        System.arraycopy(elementData, index, elementData, index + 1,
                         size - index);
        elementData[index] = element;
        size++;
    }

    /**
     * 删除该列表中指定位置的元素。 将任何后续元素移动到左侧（从其索引中减去一个元素）。 
     * @param index the index of the element to be removed
     * @return 从列表中删除的元素 
     * @throws IndexOutOfBoundsException {@inheritDoc}
     */
    public E remove(int index) {
        rangeCheck(index);

        modCount++;
        E oldValue = elementData(index);

        int numMoved = size - index - 1;
        if (numMoved > 0)
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
        elementData[--size] = null; // clear to let GC do its work

        return oldValue;
    }

    /**
     * 从列表中删除指定元素的第一个出现（如果存在）。 如果列表不包含该元素，则它不会更改。
     * @param o element to be removed from this list, if present
     * @return 如果此列表包含指定的元素 返回true
     */
    public boolean remove(Object o) {
        if (o == null) {
            for (int index = 0; index < size; index++)
                if (elementData[index] == null) {
                    fastRemove(index);
                    return true;
                }
        } else {
            for (int index = 0; index < size; index++)
                if (o.equals(elementData[index])) {
                    fastRemove(index);
                    return true;
                }
        }
        return false;
    }

    /*
     * 私有删除方法，跳过边界检查并且不
     * 返回删除的值
     */
    private void fastRemove(int index) {
        modCount++;
        int numMoved = size - index - 1;
        if (numMoved > 0)
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
        elementData[--size] = null; // clear to let GC do its work
    }

    /**
     * 从列表中删除所有元素。 
     */
    public void clear() {
        modCount++;

        // clear to let GC do its work
        for (int i = 0; i < size; i++)
            elementData[i] = null;

        size = 0;
    }

    /**
     * 按指定集合的Iterator返回的顺序将指定集合中的所有元素追加到此列表的末尾。
     * @param c collection containing elements to be added to this list
     * @return <tt>true</tt> if this list changed as a result of the call
     * @throws NullPointerException if the specified collection is null
     */
    public boolean addAll(Collection<? extends E> c) {
        Object[] a = c.toArray();
        int numNew = a.length;
        ensureCapacityInternal(size + numNew);  // Increments modCount
        System.arraycopy(a, 0, elementData, size, numNew);
        size += numNew;
        return numNew != 0;
    }

    /**
     * 将指定集合中的所有元素插入到此列表中，从指定的位置开始。
     * @param index index at which to insert the first element from the
     *              specified collection
     * @param c collection containing elements to be added to this list
     * @return <tt>true</tt> if this list changed as a result of the call
     * @throws IndexOutOfBoundsException {@inheritDoc}
     * @throws NullPointerException if the specified collection is null
     */
    public boolean addAll(int index, Collection<? extends E> c) {
        rangeCheckForAdd(index);

        Object[] a = c.toArray();
        int numNew = a.length;
        ensureCapacityInternal(size + numNew);  // Increments modCount

        int numMoved = size - index;
        if (numMoved > 0)
            System.arraycopy(elementData, index, elementData, index + numNew,
                             numMoved);

        System.arraycopy(a, 0, elementData, index, numNew);
        size += numNew;
        return numNew != 0;
    }

    /**
     * 从此列表中删除所有索引为fromIndex （含）和toIndex之间的元素。
     * 将任何后续元素移动到左侧（减少其索引）。
     * @throws IndexOutOfBoundsException if {@code fromIndex} or
     *         {@code toIndex} is out of range
     *         ({@code fromIndex < 0 ||
     *          fromIndex >= size() ||
     *          toIndex > size() ||
     *          toIndex < fromIndex})
     */
    protected void removeRange(int fromIndex, int toIndex) {
        modCount++;
        int numMoved = size - toIndex;
        System.arraycopy(elementData, toIndex, elementData, fromIndex,
                         numMoved);

        // clear to let GC do its work
        int newSize = size - (toIndex-fromIndex);
        for (int i = newSize; i < size; i++) {
            elementData[i] = null;
        }
        size = newSize;
    }

    /**
     * 检查索引是否越界
     */
    private void rangeCheck(int index) {
        if (index >= size)
            throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
    }

    /**
     * add和addAll使用的rangeCheck的一个版本
     */
    private void rangeCheckForAdd(int index) {
        if (index > size || index < 0)
            throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
    }

    /**
     * 返回IndexOutOfBoundsException细节信息
     */
    private String outOfBoundsMsg(int index) {
        return "Index: "+index+", Size: "+size;
    }

    /**
     * 从此列表中删除指定集合中包含的所有元素。 
     */
    public boolean removeAll(Collection<?> c) {
        Objects.requireNonNull(c);
		// 如果此列表被修改则返回true
        return batchRemove(c, false);
    }

    /**
     * 仅保留此列表中包含在指定集合中的元素。
     * 即：从此列表中删除其中不包含在指定集合中的所有元素。 
     */
    public boolean retainAll(Collection<?> c) {
        Objects.requireNonNull(c);
        return batchRemove(c, true);
    }

    private boolean batchRemove(Collection<?> c, boolean complement) {
        final Object[] elementData = this.elementData;
        int r = 0, w = 0;
        boolean modified = false;
        try {
            for (; r < size; r++)
                if (c.contains(elementData[r]) == complement)
                    elementData[w++] = elementData[r];
        } finally {
            // Preserve behavioral compatibility with AbstractCollection,
            // even if c.contains() throws.
            if (r != size) {
                System.arraycopy(elementData, r,
                                 elementData, w,
                                 size - r);
                w += size - r;
            }
            if (w != size) {
                // clear to let GC do its work
                for (int i = w; i < size; i++)
                    elementData[i] = null;
                modCount += size - w;
                size = w;
                modified = true;
            }
        }
        return modified;
    }

    /**
     * Save the state of the <tt>ArrayList</tt> instance to a stream (that
     * is, serialize it).
     *
     * @serialData The length of the array backing the <tt>ArrayList</tt>
     *             instance is emitted (int), followed by all of its elements
     *             (each an <tt>Object</tt>) in the proper order.
     */
    private void writeObject(java.io.ObjectOutputStream s)
        throws java.io.IOException{
        // Write out element count, and any hidden stuff
        int expectedModCount = modCount;
        s.defaultWriteObject();

        // Write out size as capacity for behavioural compatibility with clone()
        s.writeInt(size);

        // Write out all elements in the proper order.
        for (int i=0; i<size; i++) {
            s.writeObject(elementData[i]);
        }

        if (modCount != expectedModCount) {
            throw new ConcurrentModificationException();
        }
    }

    /**
     * Reconstitute the <tt>ArrayList</tt> instance from a stream (that is,
     * deserialize it).
     */
    private void readObject(java.io.ObjectInputStream s)
        throws java.io.IOException, ClassNotFoundException {
        elementData = EMPTY_ELEMENTDATA;

        // Read in size, and any hidden stuff
        s.defaultReadObject();

        // Read in capacity
        s.readInt(); // ignored

        if (size > 0) {
            // be like clone(), allocate array based upon size not capacity
            int capacity = calculateCapacity(elementData, size);
            SharedSecrets.getJavaOISAccess().checkArray(s, Object[].class, capacity);
            ensureCapacityInternal(size);

            Object[] a = elementData;
            // Read in all elements in the proper order.
            for (int i=0; i<size; i++) {
                a[i] = s.readObject();
            }
        }
    }

    /**
     * 从列表中的指定位置开始，返回列表中的元素（按正确顺序）的列表迭代器。
     * 指定的索引表示初始调用将返回的第一个元素为next。 
	 * 初始调用previous将返回指定索引减1的元素。 
     * 返回的列表迭代器是fail-fast 。 
     * @throws IndexOutOfBoundsException {@inheritDoc}
     */
    public ListIterator<E> listIterator(int index) {
        if (index < 0 || index > size)
            throw new IndexOutOfBoundsException("Index: "+index);
        return new ListItr(index);
    }

    /**
     * 返回列表中的列表迭代器（按适当的顺序）。 
     * 返回的列表迭代器是fail-fast 。
     */
    public ListIterator<E> listIterator() {
        return new ListItr(0);
    }
	/**
		* 以正确的顺序返回该列表中的元素的迭代器。 
		* 返回的迭代器是fail-fast 。 
		*/
        public Iterator<E> iterator() {
            return listIterator();
        }
```

[更多关于transient关键字的解释](https://www.cnblogs.com/lanxuezaipiao/p/3369962.html)

## System.arraycopy()和Arrays.copyOf()方法

1. `System.arraycopy(Object src, int srcPos, Object dest, int destPos, int length)`

- src - 源数组。
- srcPos - 源数组中的起始位置。
- dest - 目标数组。
- destPos - 目标数据中的起始位置。
- length - 要复制的数组元素的数量。
**该方法是用了native关键字，调用的为C++编写的底层函数，可见其为JDK中的底层函数。**

2. Arrays.copyOf()

- original - 要复制的数组
- newLength - 要返回的副本的长度
- newType - 要返回的副本的类型

### 总结：
1. copyOf()的实现是用的是arrayCopy();
2. arrayCopy()需要目标数组，对两个数组的内容进行可能不完全的合并操作。
3. copyOf()在内部新建一个数组，调用arrayCopy()将original内容复制到copy中去，并且长度为newLength。返回copy;

## 扩容机制

### 注意

1. Java 中的length 属性是针对数组说的，表示数组长度。
2. Java 中的length()方法是针对字 符串String说的，表示字符串长度。
3. Java 中的size()方法是针对泛型集合说的，表示集合实际元素个数。

## 内部类

```java
1. private class Itr implements Iterator<E>  
2. private class ListItr extends Itr implements ListIterator<E>  
3. private class SubList extends AbstractList<E> implements RandomAccess
4. static final class ArrayListSpliterator<E> implements Spliterator<E> 
```

ArrayList有四个内部类，其中的 Itr 是实现了 Iterator 接口，同时重写了里面的 hasNext() ， next()， remove()  等方法；其中的 ListItr 继承 Itr，实现了 ListIterator 接口，同时重写了hasPrevious()， nextIndex()， previousIndex()， previous()， set(E e)， add(E e) 等方法，所以这也可以看出了 Iterator 和 ListIterator 的区别: ListIterator 在 Iterator的基础上增加了添加对象，修改对象，逆向遍历等方法，这些是Iterator不能实现的。

## Demo

```java
import java.util.ArrayList;
import java.util.Iterator;

public class ArrayListDemo {

    public static void main(String[] args) {
        ArrayList<String> list = new ArrayList<>();
        System.out.println("最初始大小: " + list.size());

        list.add("a");
        list.add("b");
        list.add("c");
        list.add("d");

        System.out.println("添加元素后的大小: " + list.size());

        // 遍历打印
        System.out.println("for each循环遍历");
        for (String item : list) {
            System.out.print(item + " ");
        }

        System.out.println("\nfor 索引循环遍历");

        for (int i = 0; i < list.size(); i++) {
            System.out.print(list.get(i) + " ");
        }

        System.out.println("\n通过迭代器");

        Iterator<String> it = list.iterator();
        while(it.hasNext()){
            System.out.print(it.next() + " ");
            // it.remove(); 删除的是扫描过的上一个。
        }

        // toArray用法

        // 第一种方式(最常用)
        String[] charList = list.toArray(new String[0]);

        // 第二种方式(容易理解)
        String[] charList1 = new String[list.size()];
        String[] strings = list.toArray(charList1);

        // 抛出异常，java不支持向下转型
        //Integer[] integer2 = new Integer[arrayList.size()];
        //integer2 = arrayList.toArray();

        // 在指定位置添加元素
        list.add(2,"f");
        // 删除指定位置上的元素
        list.remove(1);
        // 删除指定元素
        list.remove("a");
        // 判断arrayList是否包含5
        System.out.println("\nArrayList contains b is: " + list.contains("b"));

        for (String item : list) {
            System.out.print(item + " ");
        }
        // 清空ArrayList
        list.clear();
        // 判断ArrayList是否为空
        System.out.println("\nArrayList is empty: " + list.isEmpty());

    }

}
```

[源码](ArrayList源码.md)
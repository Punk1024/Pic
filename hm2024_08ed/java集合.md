# 从数据结构到Java常用集合 #

数据结构想必大家都不会陌生，对于一个成熟的程序员而言，熟悉和掌握数据结构和算法也是基本功之一。数据结构本身其实不过是数据按照特点关系进行存储或者组织的集合，特殊的结构在不同的应用场景中往往会带来不一样的处理效率。

常用的数据结构可根据数据访问的特点分为线性结构和非线性结构。线性结构包括常见的链表、栈、队列等，非线性结构包括树、图等。数据结构种类繁多，本篇内容将通过图解的方式对常用的数据结构以及在java中的实现进行介绍和讲解，以方便大家更深入的掌握常用数据结构和集合的基本知识。

**数据结构组织图**

![å¨è¿éæå¥å¾çæè¿°](https://img-blog.csdnimg.cn/20200526095818285.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JlZXdvcmtzaG9w,size_16,color_FFFFFF,t_70) 

**Java集合组织图**

![img](assets\999804-20170918091025493-1734810124.png) 

# 一 数组 #

数组可以说是最基本最常见的数据结构。数组一般用来存储相同类型的数据，可通过数组名和下标进行数据的访问和更新。数组中元素的存储是按照先后顺序进行的，同时在内存中也是按照这个顺序进行连续存放。数组相邻元素之间的内存地址的间隔一般就是数组数据类型的大小。

###  ![å¨è¿éæå¥å¾çæè¿°](assets\watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JlZXdvcmtzaG9w,size_16,color_FFFFFF,t_7099)案例ArrayList ###

#### 1 ArrayList概述 ####

　　1）ArrayList是可以动态增长和缩减的索引序列，它是基于数组实现的List类。

　　2）该类封装了一个动态再分配的Object[]数组，每一个类对象都有一个capacity属性，表示它们所封装的Object[]数组的长度，当向ArrayList中添加元素时，该属性值会自动增加。

　　　　如果想ArrayList中添加大量元素，可使用ensureCapacity方法一次性增加capacity，可以减少增加重分配的次数提高性能。

　　3）ArrayList的用法和Vector向类似，但是Vector是一个较老的集合，具有很多缺点，不建议使用。

　　　　另外，ArrayList和Vector的区别是：ArrayList是线程不安全的，当多条线程访问同一个ArrayList集合时，程序需要手动保证该集合的同步性，而Vector则是线程安全的。

　　4）ArrayList和Collection的关系：

　　　　　　![img](assets\999804-20171018145151287-1709338098.png)

#### 2 ArrayList的数据结构 ####

　　分析一个类的时候，数据结构往往是它的灵魂所在，理解底层的数据结构其实就理解了该类的实现思路，具体的实现细节再具体分析。

　　ArrayList的数据结构是：

　　　　![img](assets\999804-20171018145259474-886553593.png)

　　说明：底层的数据结构就是数组，数组元素类型为Object类型，即可以存放所有类型数据。我们对ArrayList类的实例的所有的操作底层都是基于数组的。

#### 3 ArrayList源码分析 ####

##### 3.1、继承结构和层次关系 #####

　　![img](assets\999804-20171018145449709-655616177.png)

　　![img](assets\999804-20171018145519068-1615466435.png)

　　我们看一下ArrayList的继承结构：

　　　　　　　　ArrayList extends AbstractList

　　　　　　　　AbstractList extends AbstractCollection 

　　所有类都继承Object 所以ArrayList的继承结构就是上图这样。

　　分析：

　　　　1）为什么要先继承AbstractList，而让AbstractList先实现List<E>？而不是让ArrayList直接实现List<E>？这里是有一个思想，接口中全都是抽象的方法，而抽象类中可以有抽象方法，还可以有具体的实现方法，正是利用了这一点，让AbstractList是实现接口中一些通用的方法，而具体的类，如ArrayList就继承这个AbstractList类，拿到一些通用的方法，然后自己在实现一些自己特有的方法，这样一来，让代码更简洁，就继承结构最底层的类中通用的方法都抽取出来，先一起实现了，减少重复代码。所以一般看到一个类上面还有一个抽象类，应该就是这个作用。

　　　　2）ArrayList实现了哪些接口？

　　　　　　List<E>接口：开发这个collection 的作者说。这其实是一个mistake，因为他写这代码的时候觉得这个会有用处，但是其实并没什么用，但因为没什么影响，就一直留到了现在。[ http://stackoverflow.com/questions/2165204/why-does-linkedhashsete-extend-hashsete-and-implement-sete](http://stackoverflow.com/questions/2165204/why-does-linkedhashsete-extend-hashsete-and-implement-sete)

　　　　　　RandomAccess接口：这个是一个标记性接口，通过查看api文档，它的作用就是用来快速随机存取，有关效率的问题，在实现了该接口的话，那么使用普通的for循环来遍历，性能更高，例如arrayList。而没有实现该接口的话，使用Iterator来迭代，这样性能更高，例如linkedList。所以这个标记性只是为了让我们知道我们用什么样的方式去获取数据性能更好。

　　　　　　Cloneable接口：实现了该接口，就可以使用Object.Clone()方法了。

　　　　　　Serializable接口：实现该序列化接口，表明该类可以被序列化，提供了能够和字节流相互转化的功能。

##### 3.2、类中的属性 #####

```java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
{
    // 版本号
    private static final long serialVersionUID = 8683452581122892189L;
    // 缺省容量
    private static final int DEFAULT_CAPACITY = 10;
    // 空对象数组
    private static final Object[] EMPTY_ELEMENTDATA = {};
    // 缺省空对象数组
    private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
    // 元素数组
    transient Object[] elementData;
    // 实际元素大小，默认为0
    private int size;
    // 最大数组容量
    private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;
}
```

##### 3.3、构造方法 #####

　　ArrayList有三个构造方法：

　　　　![img](assets\999804-20171018150642912-329826692.png)

　　1）无参构造方法　　

```java
/**
    * Constructs an empty list with an initial capacity of ten.　　这里就说明了默认会给10的大小，所以说一开始arrayList的容量是10.
    */
　　　　//ArrayList中储存数据的其实就是一个数组，这个数组就是elementData，在123行定义的 private transient Object[] elementData;
　　 public ArrayList() {　　
        super();        //调用父类中的无参构造方法，父类中的是个空的构造方法
        this.elementData = EMPTY_ELEMENTDATA;//EMPTY_ELEMENTDATA：是个空的Object[]， 将elementData初始化，elementData也是个Object[]类型。空的Object[]会给默认大小10，等会会解释什么时候赋值的。
    }
```

　　　备注：

　　　　　　![img](assets\999804-20171018151107834-18136797.png)

　　　　　　![img](assets\999804-20171018151047193-1603738126.png)

　　2）有参构造函数一

```java
/**
     * Constructs an empty list with the specified initial capacity.
     *
     * @param  initialCapacity  the initial capacity of the list
     * @throws IllegalArgumentException if the specified initial capacity
     *         is negative
     */
    public ArrayList(int initialCapacity) {
        super(); //父类中空的构造方法
        if (initialCapacity < 0)    //判断如果自定义大小的容量小于0，则报下面这个非法数据异常
            throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
        this.elementData = new Object[initialCapacity]; //将自定义的容量大小当成初始化elementData的大小
    }
```

　　3）有参构造方法三(不常用)

```java
//这个构造方法不常用，举个例子就能明白什么意思
    /*
        Strudent exends Person
         ArrayList<Person>、 Person这里就是泛型
        我还有一个Collection<Student>、由于这个Student继承了Person，那么根据这个构造方法，我就可以把这个Collection<Student>转换为ArrayList<Sudent>这就是这个构造方法的作用 
    */
     public ArrayList(Collection<? extends E> c) {
        elementData = c.toArray();    //转换为数组
        size = elementData.length;   //数组中的数据个数
        // c.toArray might (incorrectly) not return Object[] (see 6260652)
        if (elementData.getClass() != Object[].class) //每个集合的toarray()的实现方法不一样，所以需要判断一下，如果不是Object[].class类型，那么久需要使用ArrayList中的方法去改造一下。
            elementData = Arrays.copyOf(elementData, size, Object[].class);
    }　　　　
```

　　总结：arrayList的构造方法就做一件事情，就是初始化一下储存数据的容器，其实本质上就是一个数组，在其中就叫elementData。

##### 3.4、核心方法 #####

###### 1、add()方法（有四个） ######

　　　　![img](https://images2017.cnblogs.com/blog/999804/201710/999804-20171018162356787-2047160796.png)

　　　　1）boolean add(E)；//默认直接在末尾添加元素

```java
/**
     * Appends the specified element to the end of this list.添加一个特定的元素到list的末尾。
     *
     * @param e element to be appended to this list
     * @return <tt>true</tt> (as specified by {@link Collection#add})
     */
    public boolean add(E e) {    
    //确定内部容量是否够了，size是数组中数据的个数，因为要添加一个元素，所以size+1，先判断size+1的这个个数数组能否放得下，就在这个方法中去判断数组.length是否够用了。
        ensureCapacityInternal(size + 1);  // Increments modCount!!
     //在数据中正确的位置上放上元素e，并且size++
        elementData[size++] = e;
        return true;
    }
```

　　　　分析：

　　　　　　ensureCapacityInternal(xxx);　确定内部容量的方法　　　

```java
private void ensureCapacityInternal(int minCapacity) {
        if (elementData == EMPTY_ELEMENTDATA) { //看，判断初始化的elementData是不是空的数组，也就是没有长度
    //因为如果是空的话，minCapacity=size+1；其实就是等于1，空的数组没有长度就存放不了，所以就将minCapacity变成10，也就是默认大小，但是带这里，还没有真正的初始化这个elementData的大小。
            minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
        }
    //确认实际的容量，上面只是将minCapacity=10，这个方法就是真正的判断elementData是否够用
        ensureExplicitCapacity(minCapacity);
    }
```

　　　　　　ensureExplicitCapacity(xxx)；

```java
private void ensureExplicitCapacity(int minCapacity) {
    	//记录修改次数
        modCount++;

        // overflow-conscious code
//minCapacity如果大于了实际elementData的长度，那么就说明elementData数组的长度不够用，不够用那么就要增加elementData的length。这里有的同学就会模糊minCapacity到底是什么呢，这里给你们分析一下

/*第一种情况：由于elementData初始化时是空的数组，那么第一次add的时候，minCapacity=size+1；也就minCapacity=1，在上一个方法(确定内部容量ensureCapacityInternal)就会判断出是空的数组，就会给
　　将minCapacity=10，到这一步为止，还没有改变elementData的大小。
　第二种情况：elementData不是空的数组了，那么在add的时候，minCapacity=size+1；也就是minCapacity代表着elementData中增加之后的实际数据个数，拿着它判断elementData的length是否够用，如果length
不够用，那么肯定要扩大容量，不然增加的这个元素就会溢出。
*/


        if (minCapacity - elementData.length > 0)
    //arrayList能自动扩展大小的关键方法就在这里了
            grow(minCapacity);
    }
```

　　　　　　grow(xxx); arrayList核心的方法，能扩展数组大小的真正秘密。

```java
private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;  //将扩充前的elementData大小给oldCapacity
        int newCapacity = oldCapacity + (oldCapacity >> 1);//newCapacity就是1.5倍的oldCapacity
        if (newCapacity - minCapacity < 0)//这句话就是适应于elementData就空数组的时候，length=0，那么oldCapacity=0，newCapacity=0，所以这个判断成立，在这里就是真正的初始化elementData的大小了，就是为10.前面的工作都是准备工作。
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)//如果newCapacity超过了最大的容量限制，就调用hugeCapacity，也就是将能给的最大值给newCapacity
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
    //新的容量大小已经确定好了，就copy数组，改变容量大小咯。
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
```

　　　　　hugeCapacity();  

```java
//这个就是上面用到的方法，很简单，就是用来赋最大值。
    private static int hugeCapacity(int minCapacity) {
        if (minCapacity < 0) // overflow
            throw new OutOfMemoryError();
//如果minCapacity都大于MAX_ARRAY_SIZE，那么就Integer.MAX_VALUE返回，反之将MAX_ARRAY_SIZE返回
        return (minCapacity > MAX_ARRAY_SIZE) ?
            Integer.MAX_VALUE :
            MAX_ARRAY_SIZE;
    }
```

　　　　2）void add(int，E)；在特定位置添加元素，也就是插入元素

```java
public void add(int index, E element) {
        rangeCheckForAdd(index);//检查index也就是插入的位置是否合理。

//跟上面的分析一样，具体看上面
        ensureCapacityInternal(size + 1);  // Increments modCount!!
//这个方法就是用来在插入元素之后，要将index之后的元素都往后移一位，
        System.arraycopy(elementData, index, elementData, index + 1,
                         size - index);
//在目标位置上存放元素
        elementData[index] = element;
        size++;//size增加1
    } 
```

　　　　分析：

　　　　　　rangeCheckForAdd(index)　　

```java
    private void rangeCheckForAdd(int index) {
        if (index > size || index < 0)   //插入的位置肯定不能大于size 和小于0
//如果是，就报这个越界异常
            throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
    }
```

　　　　　　System.arraycopy(...)：就是将elementData在插入位置后的所有元素往后面移一位。查看api文档　arraycopy

　　**总结**：

　　　　正常情况下会扩容1.5倍，特殊情况下（新扩展数组大小已经达到了最大值）则只取最大值。

　　　　当我们调用add方法时，实际上的函数调用如下：

　　　　　　![img](assets\999804-20171018160516646-1985692158.png)

　　　　说明：程序调用add，实际上还会进行一系列调用，可能会调用到grow，grow可能会调用hugeCapacity。

　　举例说明一：　　　　

```java
　　List<Integer> lists = new ArrayList<Integer>(6);
　　lists.add(8);
```

　　　　说明：初始化lists大小为0，调用的ArrayList()型构造函数，那么在调用lists.add(8)方法时，会经过怎样的步骤呢？下图给出了该程序执行过程和最初与最后的elementData的大小。

　　　　　　![img](assets\999804-20171018161219756-1707907201.png)

　　　　说明：我们可以看到，在add方法之前开始elementData = {}；调用add方法时会继续调用，直至grow，最后elementData的大小变为10，之后再返回到add函数，把8放在elementData[0]中。

　　举例说明二：　　　

```java
　　List<Integer> lists = new ArrayList<Integer>(6);
　　lists.add(8);
```

　　　　说明：调用的ArrayList(int)型构造函数，那么elementData被初始化为大小为6的Object数组，在调用add(8)方法时，具体的步骤如下：

　　　　　　![img](assets\999804-20171018161329396-659259302.png)

　　　　说明：我们可以知道，在调用add方法之前，elementData的大小已经为6，之后再进行传递，不会进行扩容处理。

###### 2、删除方法 ######

　　　　其实这几个删除方法都是类似的。我们选择几个讲，其中fastRemove(int)方法是private的，是提供给remove(Object)这个方法用的。

　　　　![img](assets\999804-20171018154558896-1270971770.png)![img](assets\999804-20171018154624115-1149460965.png)![img](assets\999804-20171018154652162-1480448120.png)

　　　　1）remove(int)：删除指定位置上的元素

```java
public E remove(int index) {
        rangeCheck(index);//检查index的合理性

        modCount++;//增加修改次数
        E oldValue = elementData(index);//通过索引直接找到该元素

        int numMoved = size - index - 1;//计算要移动的位数。
        if (numMoved > 0)
//这个方法也已经解释过了，就是用来移动元素的。
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
//将--size上的位置赋值为null，让gc(垃圾回收机制)更快的回收它。
        elementData[--size] = null; // clear to let GC do its work
//返回删除的元素。
        return oldValue;
    }
```

　　　　2）remove(Object)：这个方法可以看出来，arrayList是可以存放null值得。

```java
//通过元素来删除该元素，就依次遍历，如果有这个元素，就将该元素的索引传给fastRemobe(index)，使用这个方法来删除该元素，
//fastRemove(index)方法的内部跟remove(index)的实现几乎一样，这里最主要是知道arrayList可以存储null值
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
```

　　　　3）clear()：将elementData中每个元素都赋值为null，等待垃圾回收将这个给回收掉，所以叫clear

```java
public void clear() {
        modCount++;

        // clear to let GC do its work
        for (int i = 0; i < size; i++)
            elementData[i] = null;

        size = 0;
    }
```

　　　　4）removeAll(collection c)：

```java
     public boolean removeAll(Collection<?> c) {
         return batchRemove(c, false);//批量删除
     }
```

　　　　5）batchRemove(xx,xx)：用于两个方法，一个removeAll()：它只清楚指定集合中的元素，retainAll()用来测试两个集合是否有交集。　

```java
//这个方法，用于两处地方，如果complement为false，则用于removeAll如果为true，则给retainAll()用，retainAll（）是用来检测两个集合是否有交集的。
   private boolean batchRemove(Collection<?> c, boolean complement) {
        final Object[] elementData = this.elementData; //将原集合，记名为A
        int r = 0, w = 0;   //r用来控制循环，w是记录有多少个交集
        boolean modified = false;  
        try {
            for (; r < size; r++)
//参数中的集合C一次检测集合A中的元素是否有，
                if (c.contains(elementData[r]) == complement)
//有的话，就给集合A
                    elementData[w++] = elementData[r];
        } finally {
            // Preserve behavioral compatibility with AbstractCollection,
            // even if c.contains() throws.
//如果contains方法使用过程报异常
            if (r != size) {
//将剩下的元素都赋值给集合A，
                System.arraycopy(elementData, r,
                                 elementData, w,
                                 size - r);
                w += size - r;
            }
            if (w != size) {
//这里有两个用途，在removeAll()时，w一直为0，就直接跟clear一样，全是为null。
//retainAll()：没有一个交集返回true，有交集但不全交也返回true，而两个集合相等的时候，返回false，所以不能根据返回值来确认两个集合是否有交集，而是通过原集合的大小是否发生改变来判断，如果原集合中还有元素，则代表有交集，而元集合没有元素了，说明两个集合没有交集。
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
```

　　总结：：remove函数用户移除指定下标的元素，此时会把指定下标到数组末尾的元素向前移动一个单位，并且会把数组最后一个元素设置为null，

　　　　　　这样是为了方便之后将整个数组不被使用时，会被GC，可以作为小的技巧使用。

###### 3、set()方法 ######

```java
public E set(int index, E element) {
        // 检验索引是否合法
        rangeCheck(index);
        // 旧值
        E oldValue = elementData(index);
        // 赋新值
        elementData[index] = element;
        // 返回旧值
        return oldValue;
    }
```

　　说明：设定指定下标索引的元素值

###### 4、indexOf()方法 ######

```java
// 从首开始查找数组里面是否存在指定元素
    public int indexOf(Object o) {
        if (o == null) { // 查找的元素为空
            for (int i = 0; i < size; i++) // 遍历数组，找到第一个为空的元素，返回下标
                if (elementData[i]==null)
                    return i;
        } else { // 查找的元素不为空
            for (int i = 0; i < size; i++) // 遍历数组，找到第一个和指定元素相等的元素，返回下标
                if (o.equals(elementData[i]))
                    return i;
        } 
        // 没有找到，返回空
        return -1;
    }
```

　　说明：从头开始查找与指定元素相等的元素，注意，是可以查找null元素的，意味着ArrayList中可以存放null元素的。与此函数对应的lastIndexOf，表示从尾部开始查找。

###### 5、get()方法 ######

```java
public E get(int index) {
        // 检验索引是否合法
        rangeCheck(index);

        return elementData(index);
    }
```

　　说明：get函数会检查索引值是否合法（只检查是否大于size，而没有检查是否小于0），值得注意的是，在get函数中存在element函数，element函数用于返回具体的元素，具体函数如下：

```java
E elementData(int index) {
        return (E) elementData[index];
    }
```

　　说明：返回的值都经过了向下转型（Object -> E），这些是对我们应用程序屏蔽的小细节。

### 总结 ###

1）arrayList可以存放null。
2）arrayList本质上就是一个elementData数组。
3）arrayList区别于数组的地方在于能够自动扩展大小，其中关键的方法就是gorw()方法。
4）arrayList中removeAll(collection c)和clear()的区别就是removeAll可以删除批量指定的元素，而clear是全是删除集合中的元素。
5）arrayList由于本质是数组，所以它在数据的查询方面会很快，而在插入删除这些方面，性能下降很多，有移动很多数据才能达到应有的效果
6）arrayList实现了RandomAccess，所以在遍历它的时候推荐使用for循环。

# 二 链表 #

链表相较于数组，除了数据域，还增加了指针域用于构建链式的存储数据。链表中每一个节点都包含此节点的数据和指向下一节点地址的指针。由于是通过指针进行下一个数据元素的查找和访问，使得链表的自由度更高。

这表现在对节点进行增加和删除时，只需要对上一节点的指针地址进行修改，而无需变动其它的节点。不过事物皆有两极，指针带来高自由度的同时，自然会牺牲数据查找的效率和多余空间的使用。

一般常见的是有头有尾的单链表，对指针域进行反向链接，还可以形成双向链表或者循环链表。

![在这里插入图片描述](assets\watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JlZXdvcmtzaG9w,size_16,color_FFFFFF,t_701)

**链表类型**

　　　　1.1）单向链表：

　　　　　　element：用来存放元素

　　　　　　next：用来指向下一个节点元素

　　　　　　通过每个结点的指针指向下一个结点从而链接起来的结构，最后一个节点的next指向null。

　　　　　　![img](assets\999804-20171018163705615-546509635.png)

　　　　1.2）单向循环链表

　　　　　　element、next 跟前面一样

　　　　　　在单向链表的最后一个节点的next会指向头节点，而不是指向null，这样存成一个环

　　　　　　![img](assets\999804-20171018163849318-946854643.png)

　　　　1.3）双向链表

　　　　　　element：存放元素

　　　　　　pre：用来指向前一个元素

　　　　　　next：指向后一个元素

　　　　　　双向链表是包含两个指针的，pre指向前一个节点，next指向后一个节点，但是第一个节点head的pre指向null，最后一个节点的tail指向null。

　　　　　　![img](assets\999804-20171018164025990-1035906814.png)

　　　　1.4）双向循环链表

　　　　　　element、pre、next 跟前面的一样

　　　　　　第一个节点的pre指向最后一个节点，最后一个节点的next指向第一个节点，也形成一个“环”。

　　　　　　![img](assets\999804-20171018164246521-25397602.png)

**链表和数组对比**

链表和数组在实际的使用过程中需要根据自身的优劣势进行选择。链表和数组的异同点也是面试中高频的考察点之一。这里对单链表和数组的区别进行了对比和总结。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200526095942491.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JlZXdvcmtzaG9w,size_16,color_FFFFFF,t_70)



### 案例LinkedList ###

#### 1 LinkedList概述 ####

　　![img](assets\999804-20171018185451506-2136799134.png)

　　LinkedList是一种可以在任何位置进行高效地插入和移除操作的有序序列，它是基于双向链表实现的。

　　LinkedList 是一个继承于AbstractSequentialList的双向链表。它也可以被当作堆栈、队列或双端队列进行操作。
　　LinkedList 实现 List 接口，能对它进行队列操作。
　　LinkedList 实现 Deque 接口，即能将LinkedList当作双端队列使用。
　　LinkedList 实现了Cloneable接口，即覆盖了函数clone()，能克隆。
　　LinkedList 实现java.io.Serializable接口，这意味着LinkedList支持序列化，能通过序列化去传输。
　　LinkedList 是非同步的。

#### 2 LinkedList的数据结构 ####

　　　　![img](assets\999804-20171018164511396-1020210450.png)

　　　　如上图所示，LinkedList底层使用的双向链表结构，有一个头结点和一个尾结点，双向链表意味着我们可以从头开始正向遍历，或者是从尾开始逆向遍历，并且可以针对头部和尾部进行相应的操作。

#### 3 LinkedList的特性 ####

　　在我们平常中我们只知道一些常识性的特点：

　　　　1）是通过链表实现的，

　　　　2）如果在频繁的插入，或者删除数据时，就用linkedList性能会更好。

　　那我们通过API去查看它的一些特性

　　　　1）Doubly-linked list implementation of the `List` and `Deque` interfaces. Implements all optional list operations, and permits all elements (including `null`).

　　　　　　这告诉我们，linkedList是一个双向链表，并且实现了List和Deque接口中所有的列表操作，并且能存储任何元素，包括null，

　　　　　　这里我们可以知道linkedList除了可以当链表使用，还可以当作队列使用，并能进行相应的操作。

　　　　2）All of the operations perform as could be expected for a doubly-linked list. Operations that index into the list will traverse the list from the beginning or the end, whichever is closer to the specified index.

　　　　　　这个告诉我们，linkedList在执行任何操作的时候，都必须先遍历此列表来靠近通过index查找我们所需要的的值。通俗点讲，这就告诉了我们这个是顺序存取，

　　　　　　每次操作必须先按开始到结束的顺序遍历，随机存取，就是arrayList，能够通过index。随便访问其中的任意位置的数据，这就是随机列表的意思。

　　　　3）api中接下来讲的一大堆，就是说明linkedList是一个非线程安全的(异步)，其中在操作Interator时，如果改变列表结构(add\delete等)，会发生fail-fast。

　　通过API再次总结一下LinkedList的特性：　　

　　　　1）异步，也就是非线程安全

　　　　2）双向链表。由于实现了list和Deque接口，能够当作队列来使用。

　　　　　　链表：查询效率不高，但是插入和删除这种操作性能好。

　　　　3）是顺序存取结构（注意和随机存取结构两个概念搞清楚）



#### 4 LinkedList源码分析 ####

##### 4.1 LinkedList的继承结构以及层次关系 #####

　　![img](assets\999804-20171018170001771-1514436408.png)

　　分析：

　　　　我们可以看到，linkedList在最底层，说明他的功能最为强大，并且细心的还会发现，arrayList只有四层，这里多了一层AbstractSequentialList的抽象类，为什么呢？

　　　　通过API我们会发现：

　　　　　　1）减少实现顺序存取（例如LinkedList）这种类的工作，通俗的讲就是方便，抽象出类似LinkedList这种类的一些共同的方法

　　　　　　2）既然有了上面这句话，那么以后如果自己想实现顺序存取这种特性的类(就是链表形式)，那么就继承这个AbstractSequentialList抽象类，

　　　　　　　　如果想像数组那样的随机存取的类，那么就去实现AbstracList抽象类。

　　　　　　3）这样的分层，就很符合我们抽象的概念，越在高处的类，就越抽象，往在底层的类，就越有自己独特的个性。自己要慢慢领会这种思想。

　　　　　　4）LinkedList的类继承结构很有意思，我们着重要看是Deque接口，Deque接口表示是一个双端队列，

　　　　　　　　那么也意味着LinkedList是双端队列的一种实现，所以，基于双端队列的操作在LinkedList中全部有效。　　

 AbstractSequentialList

　　实现接口分析：

　　　　![img](assets\999804-20171018171102756-1197666604.png)

　　　　　　1）List接口：列表，add、set、等一些对列表进行操作的方法

　　　　　　2）Deque接口：有队列的各种特性，

　　　　　　3）Cloneable接口：能够复制，使用那个copy方法。

　　　　　　4）Serializable接口：能够序列化。

　　　　　　5）应该注意到没有RandomAccess：那么就推荐使用iterator，在其中就有一个foreach，增强的for循环，其中原理也就是iterator，我们在使用的时候，使用foreach或者iterator都可以。

##### 4.2 类的属性 #####

```java
public class LinkedList<E>
    extends AbstractSequentialList<E>
    implements List<E>, Deque<E>, Cloneable, java.io.Serializable
{
    // 实际元素个数
    transient int size = 0;
    // 头结点
    transient Node<E> first;
    // 尾结点
    transient Node<E> last;
}
```

　　LinkedList的属性非常简单，一个头结点、一个尾结点、一个表示链表中实际元素个数的变量。注意，头结点、尾结点都有transient关键字修饰，这也意味着在序列化时该域是不会序列化的。

##### 4.3 LinkedList的构造方法 #####

　　两个构造方法(两个构造方法都是规范规定需要写的）

　　1）空参构造函数

```java
    /**
     * Constructs an empty list.
     */
    public LinkedList() {
    }
```

　　2）有参构造函数

```java
/**
     * Constructs a list containing the elements of the specified
     * collection, in the order they are returned by the collection's
     * iterator.
     *
     * @param  c the collection whose elements are to be placed into this list
     * @throws NullPointerException if the specified collection is null
     */
   //将集合c中的各个元素构建成LinkedList链表。
    public LinkedList(Collection<? extends E> c) {
     // 调用无参构造函数
        this();
        // 添加集合中所有的元素
        addAll(c);
}
```

　　说明：会调用无参构造函数，并且会把集合中所有的元素添加到LinkedList中。　　　

##### 4.4 内部类（Node） #####

```java
//根据前面介绍双向链表就知道这个代表什么了，linkedList的奥秘就在这里。
private static class Node<E> {
        E item; // 数据域（当前节点的值）
        Node<E> next; // 后继（指向当前一个节点的后一个节点）
        Node<E> prev; // 前驱（指向当前节点的前一个节点）
        
        // 构造函数，赋值前驱后继
        Node(Node<E> prev, E element, Node<E> next) {
            this.item = element;
            this.next = next;
            this.prev = prev;
        }
    }
```

　　说明：内部类Node就是实际的结点，用于存放实际元素的地方。　　　　　

#####  4.5 核心方法 #####

###### 1 add()方法 ######

　　　　![img](assets\999804-20171018180421865-139109156.png)

　　　　1）add(E)

```java
 　　 public boolean add(E e) {
       　　 // 添加到末尾
        　　linkLast(e);
        　　return true;
  　　  }
```

　　　　说明：add函数用于向LinkedList中添加一个元素，并且添加到链表尾部。具体添加到尾部的逻辑是由linkLast函数完成的。

　　　　分析：

　　　　　　LinkLast(XXXXX)

```java
/**
     * Links e as last element.
     */
    void linkLast(E e) {
        final Node<E> l = last;    //临时节点l(L的小写)保存last，也就是l指向了最后一个节点
        final Node<E> newNode = new Node<>(l, e, null);//将e封装为节点，并且e.prev指向了最后一个节点
        last = newNode;//newNode成为了最后一个节点，所以last指向了它
        if (l == null)    //判断是不是一开始链表中就什么都没有，如果没有，则newNode就成为了第一个节点，first和last都要指向它
            first = newNode;
        else    //正常的在最后一个节点后追加，那么原先的最后一个节点的next就要指向现在真正的最后一个节点，原先的最后一个节点就变成了倒数第二个节点
            l.next = newNode;
        size++;//添加一个节点，size自增
        modCount++;
    }
```

　　　　说明：对于添加一个元素至链表中会调用add方法 -> linkLast方法。

　　　　举例一：

```java
　　　　List<Integer> lists = new LinkedList<Integer>();
　　　　lists.add(5);
　　　　lists.add(6);
```

　　　　　首先调用无参构造函数，之后添加元素5，之后再添加元素6。具体的示意图如下：

　　　　　　![img](assets\999804-20171018180943006-1444550237.png)

　　　　　　上图的表明了在执行每一条语句后，链表对应的状态。

###### 2 addAll方法 ######

　　　　addAll有两个重载函数，addAll(Collection<? extends E>)型和addAll(int, Collection<? extends E>)型，我们平时习惯调用的addAll(Collection<? extends E>)型会转化为addAll(int, Collection<? extends E>)型。

　　　　1）addAll(c);

```java
    public boolean addAll(Collection<? extends E> c) {
　　　　//继续往下看
        return addAll(size, c);
    }
```

　　　　2）addAll(size，c)：这个方法，能包含三种情况下的添加，我们这里分析的只是构造方法，空链表的情况(情况一)看的时候只需要按照不同的情况分析下去就行了。

```java
//真正核心的地方就是这里了，记得我们传过来的是size，c
    public boolean addAll(int index, Collection<? extends E> c) {
//检查index这个是否为合理。这个很简单，自己点进去看下就明白了。
        checkPositionIndex(index);
//将集合c转换为Object数组 a
        Object[] a = c.toArray();
//数组a的长度numNew，也就是由多少个元素
        int numNew = a.length;
        if (numNew == 0)
//集合c是个空的，直接返回false，什么也不做。
            return false;
//集合c是非空的，定义两个节点(内部类)，每个节点都有三个属性，item、next、prev。注意：不要管这两个什么含义，就是用来做临时存储节点的。这个Node看下面一步的源码分析，Node就是linkedList的最核心的实现，可以直接先跳下一个去看Node的分析
        Node<E> pred, succ;
//构造方法中传过来的就是index==size
        if (index == size) {
//linkedList中三个属性：size、first、last。 size：链表中的元素个数。 first：头节点  last：尾节点，就两种情况能进来这里

//情况一、：构造方法创建的一个空的链表，那么size=0，last、和first都为null。linkedList中是空的。什么节点都没有。succ=null、pred=last=null

//情况二、：链表中有节点，size就不是为0，first和last都分别指向第一个节点，和最后一个节点，在最后一个节点之后追加元素，就得记录一下最后一个节点是什么，所以把last保存到pred临时节点中。
            succ = null;
            pred = last;
        } else {
//情况三、index！=size，说明不是前面两种情况，而是在链表中间插入元素，那么就得知道index上的节点是谁，保存到succ临时节点中，然后将succ的前一个节点保存到pred中，这样保存了这两个节点，就能够准确的插入节点了
 //举个简单的例子，有2个位置，1、2、如果想插数据到第二个位置，双向链表中，就需要知道第一个位置是谁，原位置也就是第二个位置上是谁，然后才能将自己插到第二个位置上。如果这里还不明白，先看一下文章开头对于各种链表的删除，add操作是怎么实现的。
            succ = node(index);
            pred = succ.prev;
        }
//前面的准备工作做完了，将遍历数组a中的元素，封装为一个个节点。
        for (Object o : a) {
            @SuppressWarnings("unchecked") E e = (E) o;
//pred就是之前所构建好的，可能为null、也可能不为null，为null的话就是属于情况一、不为null则可能是情况二、或者情况三
            Node<E> newNode = new Node<>(pred, e, null);
//如果pred==null，说明是情况一，构造方法，是刚创建的一个空链表，此时的newNode就当作第一个节点，所以把newNode给first头节点
            if (pred == null)
                first = newNode;
            else
//如果pred！=null，说明可能是情况2或者情况3，如果是情况2，pred就是last，那么在最后一个节点之后追加到newNode，如果是情况3，在中间插入，pred为原index节点之前的一个节点，将它的next指向插入的节点，也是对的
                pred.next = newNode;
//然后将pred换成newNode，注意，这个不在else之中，请看清楚了。
            pred = newNode;
        }
        if (succ == null) {
//如果succ==null，说明是情况一或者情况二，
//情况一、构造方法，也就是刚创建的一个空链表，pred已经是newNode了，last=newNode，所以linkedList的first、last都指向第一个节点。
//情况二、在最后节后之后追加节点，那么原先的last就应该指向现在的最后一个节点了，就是newNode。
            last = pred;
        } else {
//如果succ！=null，说明可能是情况三、在中间插入节点，举例说明这几个参数的意义，有1、2两个节点，现在想在第二个位置插入节点newNode，根据前面的代码，pred=newNode，succ=2，并且1.next=newNode，
//1已经构建好了，pred.next=succ，相当于在newNode.next = 2； succ.prev = pred，相当于 2.prev = newNode， 这样一来，这种指向关系就完成了。first和last不用变，因为头节点和尾节点没变
            pred.next = succ;
//。。
            succ.prev = pred;
        }
//增加了几个元素，就把 size = size +numNew 就可以了
        size += numNew;
        modCount++;
        return true;
    }
```

　　　　说明：参数中的index表示在索引下标为index的结点（实际上是第index + 1个结点）的前面插入。在addAll函数中，addAll函数中还会调用到node函数，get函数也会调用到node函数，此函数是根据索引下标找到该结点并返回，具体代码如下：

```java
Node<E> node(int index) {
        // 判断插入的位置在链表前半段或者是后半段
        if (index < (size >> 1)) { // 插入位置在前半段
            Node<E> x = first; 
            for (int i = 0; i < index; i++) // 从头结点开始正向遍历
                x = x.next;
            return x; // 返回该结点
        } else { // 插入位置在后半段
            Node<E> x = last; 
            for (int i = size - 1; i > index; i--) // 从尾结点开始反向遍历
                x = x.prev;
            return x; // 返回该结点
        }
    }
```

　　　　说明：在根据索引查找结点时，会有一个小优化，结点在前半段则从头开始遍历，在后半段则从尾开始遍历，这样就保证了只需要遍历最多一半结点就可以找到指定索引的结点。

　　　　举例说明调用addAll函数后的链表状态：

```java
　　　　List<Integer> lists = new LinkedList<Integer>();
　　　　lists.add(5);
　　　　lists.addAll(0, Arrays.asList(2, 3, 4, 5));
```

　　　　　　上述代码内部的链表结构如下：

　　　　　　　　![img](assets\999804-20171018183422099-2095962383.png)

　　**addAll()中的一个问题**：

　　　　在addAll函数中，传入一个集合参数和插入位置，然后将集合转化为数组，然后再遍历数组，挨个添加数组的元素，但是问题来了，为什么要先转化为数组再进行遍历，而不是直接遍历集合呢？

　　　　从效果上两者是完全等价的，都可以达到遍历的效果。关于为什么要转化为数组的问题，我的思考如下：1. 如果直接遍历集合的话，那么在遍历过程中需要插入元素，在堆上分配内存空间，修改指针域，

　　　　这个过程中就会一直占用着这个集合，考虑正确同步的话，其他线程只能一直等待。2. 如果转化为数组，只需要遍历集合，而遍历集合过程中不需要额外的操作，

　　　　所以占用的时间相对是较短的，这样就利于其他线程尽快的使用这个集合。说白了，就是有利于提高多线程访问该集合的效率，尽可能短时间的阻塞。

###### 3 remove(Object o) ######

```java
/**
     * Removes the first occurrence of the specified element from this list,
     * if it is present.  If this list does not contain the element, it is
     * unchanged.  More formally, removes the element with the lowest index
     * {@code i} such that
     * <tt>(o==null&nbsp;?&nbsp;get(i)==null&nbsp;:&nbsp;o.equals(get(i)))</tt>
     * (if such an element exists).  Returns {@code true} if this list
     * contained the specified element (or equivalently, if this list
     * changed as a result of the call).
     *
     * @param o element to be removed from this list, if present
     * @return {@code true} if this list contained the specified element
     */
//首先通过看上面的注释，我们可以知道，如果我们要移除的值在链表中存在多个一样的值，那么我们会移除index最小的那个，也就是最先找到的那个值，如果不存在这个值，那么什么也不做
    public boolean remove(Object o) {
//这里可以看到，linkedList也能存储null
        if (o == null) {
//循环遍历链表，直到找到null值，然后使用unlink移除该值。下面的这个else中也一样
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
```

　　　　unlink(xxxx)

```java
/**
     * Unlinks non-null node x.
     */
//不能传一个null值过，注意，看之前要注意之前的next、prev这些都是谁。
    E unlink(Node<E> x) {
        // assert x != null;
//拿到节点x的三个属性
        final E element = x.item;
        final Node<E> next = x.next;
        final Node<E> prev = x.prev;

//这里开始往下就进行移除该元素之后的操作，也就是把指向哪个节点搞定。
        if (prev == null) {
//说明移除的节点是头节点，则first头节点应该指向下一个节点
            first = next;
        } else {
//不是头节点，prev.next=next：有1、2、3，将1.next指向3
            prev.next = next;
//然后解除x节点的前指向。
            x.prev = null;
        }

        if (next == null) {
//说明移除的节点是尾节点
            last = prev;
        } else {
//不是尾节点，有1、2、3，将3.prev指向1. 然后将2.next=解除指向。
            next.prev = prev;
            x.next = null;
        }
//x的前后指向都为null了，也把item为null，让gc回收它
        x.item = null;
        size--;    //移除一个节点，size自减
        modCount++;
        return element;    //由于一开始已经保存了x的值到element，所以返回。
    }
```

###### 4 get(index) ######

　　　　get(index)查询元素的方法

```java
/**
     * Returns the element at the specified position in this list.
     *
     * @param index index of the element to return
     * @return the element at the specified position in this list
     * @throws IndexOutOfBoundsException {@inheritDoc}
     */
//这里没有什么，重点还是在node(index)中
    public E get(int index) {
        checkElementIndex(index);
        return node(index).item;
    }
```

　　　　node(index)

```java
/**
     * Returns the (non-null) Node at the specified element index.
     */
//这里查询使用的是先从中间分一半查找
    Node<E> node(int index) {
        // assert isElementIndex(index);
//"<<":*2的几次方 “>>”:/2的几次方，例如：size<<1：size*2的1次方，
//这个if中就是查询前半部分
         if (index < (size >> 1)) {//index<size/2
            Node<E> x = first;
            for (int i = 0; i < index; i++)
                x = x.next;
            return x;
        } else {//前半部分没找到，所以找后半部分
            Node<E> x = last;
            for (int i = size - 1; i > index; i--)
                x = x.prev;
            return x;
        }
    }
```

###### 5 indexOf(Object o) ######

```java
//这个很简单，就是通过实体元素来查找到该元素在链表中的位置。跟remove中的代码类似，只是返回类型不一样。
   public int indexOf(Object o) {
        int index = 0;
        if (o == null) {
            for (Node<E> x = first; x != null; x = x.next) {
                if (x.item == null)
                    return index;
                index++;
            }
        } else {
            for (Node<E> x = first; x != null; x = x.next) {
                if (o.equals(x.item))
                    return index;
                index++;
            }
        }
        return -1;
    }
```

#### 5 LinkedList的迭代器 ####

　　在LinkedList中除了有一个Node的内部类外，应该还能看到另外两个内部类，那就是ListItr，还有一个是DescendingIterator。

##### 5.1 ListItr内部类 #####

　　　　![img](assets\999804-20171018184723521-1419677680.png)

　　　　看一下他的继承结构，发现只继承了一个ListIterator，到ListIterator中一看：

　　　　![img](assets\999804-20171018184923443-1278868049.png)

　　　　看到方法名之后，就发现不止有向后迭代的方法，还有向前迭代的方法，所以我们就知道了这个ListItr这个内部类干嘛用的了，就是能让linkedList不光能像后迭代，也能向前迭代。

 　　　看一下ListItr中的方法，可以发现，在迭代的过程中，还能移除、修改、添加值得操作。

　　　　![img](assets\999804-20171018184651412-1550358605.png)

##### 5.2 DescendingIterator内部类　　　　 #####

```java
/**
     * Adapter to provide descending iterators via ListItr.previous
     */
　　　　//看一下这个类，还是调用的ListItr，作用是封装一下Itr中几个方法，让使用者以正常的思维去写代码，例如，在从后往前遍历的时候，也是跟从前往后遍历一样，使用next等操作，而不用使用特殊的previous。
    private class DescendingIterator implements Iterator<E> {
        private final ListItr itr = new ListItr(size());
        public boolean hasNext() {
            return itr.hasPrevious();
        }
        public E next() {
            return itr.previous();
        }
        public void remove() {
            itr.remove();
        }
    }
```

## 总结 ##

　　1）linkedList本质上是一个双向链表，通过一个Node内部类实现的这种链表结构。
　　2）能存储null值
　　3）跟arrayList相比较，就真正的知道了，LinkedList在删除和增加等操作上性能好，而ArrayList在查询的性能上好
　　4）从源码中看，它不存在容量不足的情况
　　5）linkedList不光能够向前迭代，还能像后迭代，并且在迭代的过程中，可以修改值、添加值、还能移除值。
　　6）linkedList不光能当链表，还能当队列使用，这个就是因为实现了Deque接口。



# 三 跳表 #

在juc部分我们已经了解过这个数据结构,复习一下

Skip List ，称之为跳表，它是一种可以替代平衡树的数据结构，其数据元素默认按照key值升序，天然有序。Skip list让已排序的数据分布在**多层链表**中，以0-1随机数决定一个数据的向上攀升与否，通过“空间来换取时间”的一个算法，在每个节点中增加了向前的指针，在插入、删除、查找时可以忽略一些不可能涉及到的结点，从而提高了效率。

我们先看一个简单的链表，如下：

![img](D:/讲义/十次方--加密/十次方--加密/day09/文档/assets/2018120824001.png)

如果我们需要查询9、21、30，则需要比较次数为3 + 6 + 8 = 17 次，那么有没有优化方案呢？有！我们将该链表中的某些元素提炼出来作为一个比较“索引”，如下：

![img](D:/讲义/十次方--加密/十次方--加密/day09/文档/assets/2018120824002.png)

我们先与这些索引进行比较来决定下一个元素是往右还是下走，由于存在“索引”的缘故，导致在检索的时候会大大减少比较的次数。当然元素不是很多，很难体现出优势，当元素足够多的时候，这种索引结构就会大显身手。



我们将上图再做一些扩展就可以变成一个典型的SkipList结构：

![1559181479264](D:/讲义/十次方--加密/十次方--加密/day09/文档/assets/1559181479264.png)



**SkipListd的查找**

对于上面我们我们要查找元素21，其过程如下：

1. 比较3，大于，往后找（9），
2. 比9大，继续往后找（25），但是比25小，则从9的下一层开始找（16）
3. 16的后面节点依然为25，则继续从16的下一层找
4. 找到21


![1559181804985](D:/讲义/十次方--加密/十次方--加密/day09/文档/assets/1559181804985.png)

绿线代表查询路径



**SkipList的插入**

SkipList的插入操作主要包括：

1. 查找合适的位置。这里需要明确一点就是在确认新节点要占据的层次K时，采用丢硬币的方式，完全随机。如果占据的层次K大于链表的层次，则重新申请新的层，否则插入指定层次
2. 申请新的节点
3. 调整指针

假定我们要插入的元素为23，经过查找可以确认她是位于25后，9、16、21前。当然需要考虑申请的层次K。

如果层次K > 3，需要申请新层次（Level 4）

![1559182212446](D:/讲义/十次方--加密/十次方--加密/day09/文档/assets/1559182212446.png)



如果层次 K = 2，直接在Level 2 层插入即可

![1559182289418](D:/讲义/十次方--加密/十次方--加密/day09/文档/assets/1559182289418.png)



**SkipList的删除**

删除节点和插入节点思路基本一致：找到节点，删除节点，调整指针。

比如删除节点9，如下：

![1559182495579](D:/讲义/十次方--加密/十次方--加密/day09/文档/assets/1559182495579.png)

## **案例ConcurrentSkipListMap** ##

### 1. 继承结构 ###



```java
    public class ConcurrentSkipListMap<K,V> extends AbstractMap<K,V>
    implements ConcurrentNavigableMap<K,V>, Cloneable, Serializable {
```

ConcurrentSkipListMap继承了AbstractMap，该抽象类提供了接口的一些常规实现；而继承了ConcurrentNavigableMap接口，该接口可以获取Map的某一部分元素或者子元素；另外，该类支持克隆和序列化操作。

### 2. 属性 ###

来看一下它的常用属性：



```php
/**
 * 最底层的头节点的索引
 */
private static final Object BASE_HEADER = new Object();

/**
 * 最顶层头节点索引
 */
private transient volatile HeadIndex<K,V> head;

/**
 * 比较器，如果使用自然排序，该值为null
 */
final Comparator<? super K> comparator;

/** key集合 */
private transient KeySet<K> keySet;
/** entry集合 */
private transient EntrySet<K,V> entrySet;
/** value集合 */
private transient Values<V> values;
/** 降序键集合 */
private transient ConcurrentNavigableMap<K,V> descendingMap;
```

其中比较重要的是比较器，因为ConcurrentSkipListMap 会根据key进行排序，如果传为null，则表示是根据元素的自然顺序进行排序。

### 3. 内部类 ###

ConcurrentSkipListMap 中有三个比较重要的内部类，分别是**Node**，**Index**， **HeadIndex** 这三个类。Node表示最底层的链表节点，Index类表示基于Node类的索引层，而HeadIndex则是用来维护索引的层次。先来看下Node类：



```java
static final class Node<K,V> {
    final K key;
    volatile Object value;
    volatile Node<K,V> next;
}
```

可以看到，Node这个类中包含了Map的key和value，还包含了一个指向下一节点的指针next，并且这里使用的是单链表结构。然后再来看下Index这个类：



```java
static class Index<K,V> {
    // Node节点的引用
    final Node<K,V> node;
    // 下层Index的引用
    final Index<K,V> down;
    // 右侧Index的引用
    volatile Index<K,V> right;
}
```

可以看出，Index类作为索引节点，共包含了三个属性，一个是Node节点的引用，一个是指向下一层的索引节点的引用，一个是指向右侧索引节点的引用。接下来再来看下HeadIndex这个类：



```dart
static final class HeadIndex<K,V> extends Index<K,V> {
    final int level;
    HeadIndex(Node<K,V> node, Index<K,V> down, Index<K,V> right, int level) {
        super(node, down, right);
        this.level = level;
    }
}
```

可以看出，HeadIndex继承自Index，扩展了一个level属性，表示当前索引节点Index的层级。

### 4. 构造方法 ###

ConcurrentSkipListMap共包含了4个构造方法，我们来简单看下：



```java
public ConcurrentSkipListMap() {
    this.comparator = null;
    initialize();
}

public ConcurrentSkipListMap(Comparator<? super K> comparator) {
    this.comparator = comparator;
    initialize();
}
```

这两个构造方法，一个是默认的方法，表示按照自然顺序进行排序（也就是ke y必须实现了Comparable接口）；另一个是按照给定的比较器进行排序的构造方法；

------



```dart
public ConcurrentSkipListMap(Map<? extends K, ? extends V> m) {
    this.comparator = null;
    initialize();
    putAll(m);
}
```

这个构造方法表示从给定的Map构造一个ConcurrentSkipListMap对象，并按照key的自然顺序进行排序；

------



```cpp
public ConcurrentSkipListMap(SortedMap<K, ? extends V> m) {
    this.comparator = m.comparator();
    initialize();
    buildFromSorted(m);
}
```

这个构造方法表示从给定的Map构造一个ConcurrentSkipListMap对象，而顺序则是按照SortedMap的顺序来进行排序。

------

这里都调用到了 `initialize`这个方法来初始化对象，我们来看下：



```csharp
private void initialize() {
    keySet = null;
    entrySet = null;
    values = null;
    descendingMap = null;
    // 构造跳跃表的头节点head
    head = new HeadIndex<K,V>(new Node<K,V>(null, BASE_HEADER, null),
                              null, null, 1);
}
```

可以看到，这里构造了跳跃表的头节点head，构造完成之后，大概如下：

![img](https:////upload-images.jianshu.io/upload_images/5689815-d480e85f05800bcd.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/626/format/webp)

### 5. 方法 ###

由于该类的方法比较多，所以这里只介绍几个常用的方法。

#### 5.1 put方法 ####



```csharp
public V put(K key, V value) {
    // value不能为空
    if (value == null)
        throw new NullPointerException();
    return doPut(key, value, false);
}
```

可以看到，put方法内部调用的是`doPut`方法，在看doPut方法前，我们先来看下该方法中调用到的另一个方法：`findPredecessor`。

**findPredecessor** 方法表示查询key应该插入位置的前驱节点（如果遇到需要删除的节点，那么进行辅助性删除），从最高层的head节点一直向右方向进行遍历，知道右侧的节点为null或者Node的key大于当前key为止，然后再向下寻找，依次重复该过程，直到down为null，这时候就找到了前驱节点。



```kotlin
private Node<K,V> findPredecessor(Object key, Comparator<? super K> cmp) {
    if (key == null)
        throw new NullPointerException(); // don't postpone errors
    for (;;) {
        // 从head节点开始遍历，q和r作为临时的head节点和head的右节点
        for (Index<K,V> q = head, r = q.right, d;;) {
            // 如果head节点的右节点存在
            if (r != null) {
                // 获取head节点的右节点
                Node<K,V> n = r.node;
                // 获取右节点的key
                K k = n.key;
                // 如果右节点的value为空，说明右侧已经没节点了，该节点已经被删除了
                if (n.value == null) {
                    // 通过unlink方法移除该节点
                    if (!q.unlink(r))
                        break;           // restart
                    // 如果unlink方法返回了false，也就是head的右节点已经有值了
                    // 那就重新赋值，重新操作
                    r = q.right;         // reread r
                    continue;
                }
                // 通过比较器对key进行比较，如果key大于r节点的key，则继续向后遍历
                if (cpr(cmp, key, k) > 0) {
                    // 节点后移，q和r整体后移
                    q = r;
                    r = r.right;
                    continue;
                }
            }
            // 如果q.down == null，表示指针已经到最下层了，直接返回该节点
            if ((d = q.down) == null)
                return q.node;
            // 进入下层遍历
            q = d;
            r = d.right;
        }
    }
}
```

接下来，我们来看下doPut方法：



```csharp
private V doPut(K key, V value, boolean onlyIfAbsent) {
    Node<K,V> z;             // added node
    // key不能为空
    if (key == null)
        throw new NullPointerException();
    // 获取到比较器
    Comparator<? super K> cmp = comparator;
    // 无限循环
    outer: for (;;) {
        // 先找到应该插入位置的前驱节点，b表示待插入位置的前驱节点，n是前驱节点的后继节点
        for (Node<K,V> b = findPredecessor(key, cmp), n = b.next;;) {
            if (n != null) {
                Object v; int c;
                Node<K,V> f = n.next;
                // 防止多线程下数据已经修改
                if (n != b.next)               // inconsistent read
                    break;
                // 如果节点n已经逻辑删除，这里进行辅助性物理删除
                if ((v = n.value) == null) {   // n is deleted
                    n.helpDelete(b, f);
                    break;
                }
                // 如果b已经被删除，结束本层查询
                if (b.value == null || v == n) // b is deleted
                    break;
                // 如果节点key 大于 n节点的key，继续往后遍历
                if ((c = cpr(cmp, key, n.key)) > 0) {
                    b = n;
                    n = f;
                    continue;
                }
                // 如果节点key与n节点的key相等
                if (c == 0) {
                    // 比较并交换值，也就是替换值
                    if (onlyIfAbsent || n.casValue(v, value)) {
                        @SuppressWarnings("unchecked") V vv = (V)v;
                        return vv;
                    }
                    // 如果竞争失败，重试
                    break; // restart if lost race to replace value
                }
                // else c < 0; fall through
            }
            // 新创建一个节点，next指向n
            z = new Node<K,V>(key, value, n);
            // 比较并交换，也就是插入节点；如果竞争失败，重试；成功，则跳出循环
            if (!b.casNext(n, z))
                break;         // restart if lost race to append to b
            break outer;
        }
    }
    // 获取随机种子
    int rnd = ThreadLocalRandom.nextSecondarySeed();
    // 测试最低和最高位，用于判断是否需要添加level
    if ((rnd & 0x80000001) == 0) { // test highest and lowest bits
        int level = 1, max;
        // 确定level的级别
        while (((rnd >>>= 1) & 1) != 0)
            ++level;
        Index<K,V> idx = null;
        HeadIndex<K,V> h = head;
        // 如果level小于最大层，就在对应层次以及小于该level的层次进行节点新增处理
        if (level <= (max = h.level)) {
            for (int i = 1; i <= level; ++i)
                // 为节点生成对应的Index节点，并从下往上依次赋值，并且赋值了Index节点的down节点
                idx = new Index<K,V>(z, idx, null);
        }
        // 否则，需要新增一层
        else { // try to grow by one level
            level = max + 1; // hold in array and later pick the one to use
            // 使用数组来保存Index节点
            @SuppressWarnings("unchecked")Index<K,V>[] idxs =
                (Index<K,V>[])new Index<?,?>[level+1];
            for (int i = 1; i <= level; ++i)
                // 从下往上生成Index结点，并赋值down节点，这里数组的第一个值idxs[0]应该是没用到
                idxs[i] = idx = new Index<K,V>(z, idx, null);
            for (;;) {
                // 保存头节点
                h = head;
                // 保存之前的level
                int oldLevel = h.level;
                // 如果线程发生了竞争失败（其他线程改变了该跳跃表），重新来过
                if (level <= oldLevel) // lost race to add level
                    break;
                HeadIndex<K,V> newh = h;
                Node<K,V> oldbase = h.node;
                // 为新生成的一层 生成一个新的头节点
                for (int j = oldLevel+1; j <= level; ++j)
                    newh = new HeadIndex<K,V>(oldbase, newh, idxs[j], j);
                if (casHead(h, newh)) {
                    // 更新head节点，比较并替换
                    // h赋值为最高层的头节点
                    h = newh;
                    // idx赋值为之前层级的头结点x，并将level赋值为之前的层级
                    idx = idxs[level = oldLevel];
                    break;
                }
            }
        }
        // find insertion points and splice in
        // 上述操作只是生成了对应的索引节点，但是并没有将这些节点插入到对应的层之中，下面这些代码是插入Index节点
        // 从level层开始操作
        splice: for (int insertionLevel = level;;) {
            // 保存新表的层级
            int j = h.level;
            for (Index<K,V> q = h, r = q.right, t = idx;;) {
                // 如果头结点或者idx结点为空，跳出这层循环
                if (q == null || t == null)
                    break splice;
                // 如果头节点右侧节点不为空
                if (r != null) {
                    Node<K,V> n = r.node;
                    // compare before deletion check avoids needing recheck
                    // key进行比较
                    int c = cpr(cmp, key, n.key);
                    // 需要删除的节点
                    if (n.value == null) {
                        if (!q.unlink(r))
                            break;
                        r = q.right;
                        continue;
                    }
                    // 大于0，向右继续查找
                    if (c > 0) {
                        q = r;
                        r = r.right;
                        continue;
                    }
                }
                // 找到节点进行插入的位置，这里准备进行插入
                if (j == insertionLevel) {
                    // 插入，也就是将r结点插入到q与t之间；失败重试
                    if (!q.link(r, t))
                        break; // restart
                    if (t.node.value == null) {
                        findNode(key);
                        break splice;
                    }
                    // 如果到达最底层，跳出循环
                    if (--insertionLevel == 0)
                        break splice;
                }
                // 向下进入其他层进行查找
                if (--j >= insertionLevel && j < level)
                    t = t.down;
                q = q.down;
                r = q.right;
            }
        }
    }
    return null;
}
```

doPut方法内容比较多，我们来梳理下该方法的操作流程：

- 根据key从跳跃表的左上方开始，向右或者向下查找到需要插入位置的前驱Node节点，查找过程中会删除一些已经标记为删除状态的节点；
- 然后判断跳跃表中是否已经存在了该key，如果存在比较并替换；然后生成节点，插入到最底层链表中；
- 然后根据随机值来判断是否生成索引层以及生成索引层的层次；
- 如果需要创建索引层，则判断索引层是否超过最大的level，如果大于，需要创建HeadIndex索引层；否则只需要创建Index索引层即可；
- 从head开始进行遍历，将每一层的新添加的Index插入到对应的位置；

可以看出，该方法还是比较复杂的，大家有时间可以多看两遍。

#### 5.2 get方法 ####

get方法内部调用doGet方法，还有一个 **getOrDefault** 方法也是类似的：



```kotlin
public V get(Object key) {
    return doGet(key);
}

public V getOrDefault(Object key, V defaultValue) {
    V v;
    return (v = doGet(key)) == null ? defaultValue : v;
}
```

接下来主要来看下doGet方法的实现：



```kotlin
private V doGet(Object key) {
    // key不为空
    if (key == null)
        throw new NullPointerException();
    // 获取比较器
    Comparator<? super K> cmp = comparator;
    outer: for (;;) {
        // 同样还是先找到对应的前驱节点
        for (Node<K,V> b = findPredecessor(key, cmp), n = b.next;;) {
            Object v; int c;
            if (n == null)
                break outer;
            Node<K,V> f = n.next;
            // 跳跃表发生了变化，重试
            if (n != b.next)                // inconsistent read
                break;
            // 物理删除对应的节点
            if ((v = n.value) == null) {    // n is deleted
                n.helpDelete(b, f);
                break;
            }
            // 如果b已经被删除，结束本层查询
            if (b.value == null || v == n)  // b is deleted
                break;
            // 查找到返回
            if ((c = cpr(cmp, key, n.key)) == 0) {
                @SuppressWarnings("unchecked") V vv = (V)v;
                return vv;
            }
            // 小于说明不存在该值，结束循环
            if (c < 0)
                break outer;
            // 向右接着遍历（有可能其他线程添加了数据）
            b = n;
            n = f;
        }
    }
    return null;
}
```

相比doPut方法，doGet方法就比较简单了。先找到对应的前驱节点，然后一直向右查找即可，中间如果有某个节点需要删除，顺手删除即可。当然如果发现跳跃表数据结构被其它线程改变，会重新尝试获取其前驱。

#### 5.3 remove方法 ####

remove有两个重载方法，一个是根据key进行删除，另一个是根据key和value一并进行删除，当然内部调用的都是doRemove方法：



```csharp
public boolean remove(Object key, Object value) {
    if (key == null)
        throw new NullPointerException();
    return value != null && doRemove(key, value) != null;
}

public V remove(Object key) {
    return doRemove(key, null);
}
```

接下来我们来看下doRemove方法：



```kotlin
final V doRemove(Object key, Object value) {
    if (key == null)
        throw new NullPointerException();
    Comparator<? super K> cmp = comparator;
    outer: for (;;) {
        // 先获取前驱节点
        for (Node<K,V> b = findPredecessor(key, cmp), n = b.next;;) {
            Object v; int c;
            if (n == null)
                break outer;
            Node<K,V> f = n.next;
            if (n != b.next)                    // inconsistent read
                break;
            if ((v = n.value) == null) {        // n is deleted
                n.helpDelete(b, f);
                break;
            }
            // 如果b已经被删除，结束本层查询
            if (b.value == null || v == n)      // b is deleted
                break;
            // 如果不存在该key，结束循环
            if ((c = cpr(cmp, key, n.key)) < 0)
                break outer;
            // 向右进行查找
            if (c > 0) {
                b = n;
                n = f;
                continue;
            }
            // 判断完key之后，再判断value是否相等，value不相等，退出
            if (value != null && !value.equals(v))
                break outer;
            // 将v进行比较并设置为null(逻辑删除)，如果多线程下失败，则重试
            if (!n.casValue(v, null))
                break;
            // 先添加一个用于删除标记的节点，然后比较并更新b的next节点
            if (!n.appendMarker(f) || !b.casNext(n, f))
                findNode(key);                  // retry via findNode
            else {
                // 调用该方法 辅助清除key对应的索引层
                findPredecessor(key, cmp);      // clean index
                // 如果该层已经没有节点，删除该层
                if (head.right == null)
                    tryReduceLevel();
            }
            @SuppressWarnings("unchecked") V vv = (V)v;
            return vv;
        }
    }
    return null;
}
```

doRemove方法也不是太难，我们来简单梳理下它的流程：

- 先查找到对应的前驱节点，并且查找过程中会删除一些已经标记为删除状态的节点；
- 查询到要删除的节点，通过CAS操作把value设置为null（这样其他线程可以感知到这个节点状态，协助完成删除工作），然后在该节点后面添加一个marker节点作为删除标志位，若添加成功，则将该结点的前驱的后继设置为该结点之前的后继（也就是删除该节点操作），这样可以避免丢失数据；
- 如果该层已经没有了其他节点，调用tryReduceLevel方法移除该层；

> 这里可能需要说明下，因为ConcurrentSkipListMap是支持并发操作的，因此在删除的时候可能有其他线程在该位置上进行插入，这样有可能导致数据的丢失。在ConcurrentSkipListMap中，会在要删除的节点后面添加一个特殊的节点进行标记，然后再进行整体的删除，如果不进行标记，那么如果正在删除的节点，可能其它线程正在此节点后面添加数据，造成数据丢失。

这个方法中间涉及到了两个小方法，首先是`helpDelete`方法，用于帮助删除节点的方法，来简单看下：



```csharp
void helpDelete(Node<K,V> b, Node<K,V> f) {
    /*
     * Rechecking links and then doing only one of the
     * help-out stages per call tends to minimize CAS
     * interference among helping threads.
     */
    if (f == next && this == b.next) {
        // 如果没有添加删除标记节点，那么添加删除标记
        if (f == null || f.value != f) // not already marked
            casNext(f, new Node<K,V>(f));
        else
            // 执行删除操作
            b.casNext(this, f.next);
    }
}
```

另外，是减少层级的方法`tryReduceLevel`方法：



```csharp
private void tryReduceLevel() {
    HeadIndex<K,V> h = head;
    HeadIndex<K,V> d;
    HeadIndex<K,V> e;
    if (h.level > 3 &&
        (d = (HeadIndex<K,V>)h.down) != null &&
        (e = (HeadIndex<K,V>)d.down) != null &&
        e.right == null &&
        d.right == null &&
        h.right == null &&
        casHead(h, d) && // try to set
        h.right != null) // recheck
        casHead(d, h);   // try to backout
}
```

这个方法，是针对最上面的三层进行操作，如果最上面的三层HeadIndex的right节点都为空，则减少level的层数，并设置head为之前head的下一层；然后再判断之前的head的right域是否为null，这里是重新校验下，如果为null，则减少层级成功，否则再次将head设置为h（这里为什么是三层，可以看下这个方法的注释介绍）。

#### 5.4 containsValue方法 ####

由于该类的方法特别多，这里我们分析了常用的增加，修改，删除方法外，再随便找个containsValue方法来看下：



```csharp
public boolean containsValue(Object value) {
    if (value == null)
        throw new NullPointerException();
    // 查找最底层链表的第一个元素
    for (Node<K,V> n = findFirst(); n != null; n = n.next) {
        // 获取值并进行比较
        V v = n.getValidValue();
        if (v != null && value.equals(v))
            return true;
    }
    return false;
}
```

这里调用了 **findFirst** 方法获取Map中第一个元素，然后获取到后就一直往右侧进行遍历比较操作，比较简单，再来简单看下findFirst方法：



```kotlin
final Node<K,V> findFirst() {
    for (Node<K,V> b, n;;) {
        // 获取第一个保存实际元素的节点，也就是BASE_HEADER节点之后的第一个节点
        if ((n = (b = head.node).next) == null)
            return null;
        if (n.value != null)
            return n;
        // 帮助删除
        n.helpDelete(b, n.next);
    }
}
```

> 不过可能需要注意的是，ConcurrentSkipListMap的size方法与大多数集合不同，它的size方法不是常量操作，因为它并没有维护一个全局变量来统计元素的个数，而是每次调用该方法的时候都需要去遍历，因此如果在遍历期间该集合发生了修改（也就是多线程情况下），则可能出现不准确的情况。

## 案例ConcurrentSkipListSet ##

看完了ConcurrentSkipListMap，我们顺便来看下ConcurrentSkipListSet，这个类比较简单，我们在这里一并看了。ConcurrentSkipListSet 是基于ConcurrentSkipListMap实现的并发的Set实现，和通用Set一样，该类元素无法重复，但元素是有序的，可以按照他们的自然顺序进行排序，也可以按照给定的比较器进行排序。



```dart
private final ConcurrentNavigableMap<E,Object> m;

/**
 * Constructs a new, empty set that orders its elements according to
 * their {@linkplain Comparable natural ordering}.
 */
public ConcurrentSkipListSet() {
    m = new ConcurrentSkipListMap<E,Object>();
}
```

内部维护了一个ConcurrentSkipListMap的实现，所有对该Set的操作都是基于对该Map的操作，Set中的元素是保存在Map中的key上，因为ConcurrentSkipListMap中key是不重复的，而该Map对应得value则全是 `Boolean.TRUE`。我们通过一个例子来看下该类中常用的方法：



```cpp
// 构造ConcurrentSkipListSet之后，依次添加数据：1 2 3 4 5 6 7 8 9 10
ConcurrentSkipListSet<Integer> skipListSet = new ConcurrentSkipListSet<>(); 
// 后续以4来进行举例
System.out.println(skipListSet.lower(4));

lower(E)  //返回小于给定值的最大值，示例中返回：3
floor(E)  //返回小于或者等于给定值的最大值，示例中返回：4
ceiling(E) //返回大于或等于给定值的最小值，示例中返回：4
higher(E) // 返回大于给定值的最小值，示例中返回：5
pollFirst() // 返回并删除最小值，示例中返回：1
pollLast() // 返回并删除最大值，示例中返回：10
first() // 返回集合中的第一个值，也就是最小的：2
last() // 返回集合中的最后一个值，也就是最大的：9
```

## 总结 ##

ConcurrentSkipListMap是一个线程安全的基于跳跃表实现的非阻塞的Map，它要求Map中的key和value都不能为null，并且可以通过key来进行排序(创建map的时候传入对key的排序的Compare实现类，或者key本身实现Compare)。内部的实现则是通过多层有序链表来实现的，它使用空间换时间的方式，使得链表也能实现类似二分查找的功能。

而它的应用场景，Redis中的有序集合SortedSet就是基于散列表和跳跃表来实现的。

# 四 栈 #

栈是一种比较简单的数据结构，常用一句话描述其特性，后进先出。栈本身是一个线性表，但是在这个表中只有一个口子允许数据的进出。这种模式可以参考腔肠动物…即进食和排泄都用一个口…

栈的常用操作包括入栈push和出栈pop，对应于数据的压入和压出。还有访问栈顶数据、判断栈是否为空和判断栈的大小等。由于栈后进先出的特性，常可以作为数据操作的临时容器，对数据的顺序进行调控，与其它数据结构相结合可获得许多灵活的处理。

![在这里插入图片描述](assets\20200526101351156.gif)

## 案例Stack ##

​		Stack是栈。它的特性是：先进后出(FILO, First In Last Out)。java工具包中的Stack是继承于Vector(矢量队列)的，由于Vector是通过数组实现的，这就意味着，Stack也是通过数组实现的，而非链表。当然，我们也可以将LinkedList当作栈来使用。Stack的继承关系

![image-20200620035809710](assets\image-20200620035809710.png)

```java
package java.util;

public
class Stack<E> extends Vector<E> {
    // 版本ID。这个用于版本升级控制，这里不须理会！
    private static final long serialVersionUID = 1224463164541339165L;
// 构造函数
public Stack() {
}

// push函数：将元素存入栈顶
public E push(E item) {
    // 将元素存入栈顶。
    // addElement()的实现在Vector.java中
    addElement(item);
    return item;
}

// pop函数：返回栈顶元素，并将其从栈中删除
public synchronized E pop() {
    E   obj;
    int  len = size();
    obj = peek();
    // 删除栈顶元素，removeElementAt()的实现在Vector.java中
    removeElementAt(len - 1);
    return obj;
}

// peek函数：返回栈顶元素，不执行删除操作
public synchronized E peek() {
    int  len = size();
    if (len == 0)
        throw new EmptyStackException();
    // 返回栈顶元素，elementAt()具体实现在Vector.java中
    return elementAt(len - 1);
}

// 栈是否为空
public boolean empty() {
    return size() == 0;
}

// 查找“元素o”在栈中的位置：由栈底向栈顶方向数
public synchronized int search(Object o) {
    // 获取元素索引，elementAt()具体实现在Vector.java中
    int i = lastIndexOf(o);
    if (i >= 0) {
        return size() - i;
    }
    return -1;
}
}
```


# 五 队列 #

队列是栈的兄弟结构，与栈的后进先出相对应，队列是一种先进先出的数据结构。顾名思义，队列的数据存储是如同排队一般，先存入的数据先被压出。常与栈一同配合，可发挥最大的实力。

![在这里插入图片描述](assets\20200526101424265.gif)

我们在juc已经讲过阻塞队列和非阻塞队列，现在回顾一下

# 六 散列表 #

散列表也叫哈希表，是一种通过键值对直接访问数据的机构。在初中，我们就学过一种能够将一个x值通过一个函数获得对应的一个y值的操作，叫做映射。散列表的实现原理正是映射的原理，通过设定的一个关键字和一个映射函数，就可以直接获得访问数据的地址，实现O(1)的数据访问效率。在映射的过程中，事先设定的函数就是一个映射表，也可以称作散列函数或者哈希函数。

![在这里插入图片描述](assets\watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JlZXdvcmtzaG9w,size_16,color_FFFFFF,t_7016)
散列表的实现最关键的就是散列函数的定义和选择。一般常用的有以下几种散列函数：

直接寻址法：取关键字或关键字的某个线性函数值为散列地址。

数字分析法：通过对数据的分析，发现数据中冲突较少的部分，并构造散列地址。例如同学们的学号，通常同一届学生的学号，其中前面的部分差别不太大，所以用后面的部分来构造散列地址。

平方取中法：当无法确定关键字里哪几位的分布相对比较均匀时，可以先求出关键字的平方值，然后按需要取平方值的中间几位作为散列地址。这是因为：计算平方之后的中间几位和关键字中的每一位都相关，所以不同的关键字会以较高的概率产生不同的散列地址。

取随机数法：使用一个随机函数，取关键字的随机值作为散列地址，这种方式通常用于关键字长度不同的场合。

除留取余法：取关键字被某个不大于散列表的表长 n 的数 m 除后所得的余数 p 为散列地址。这种方式也可以在用过其他方法后再使用。该函数对 m 的选择很重要，一般取素数或者直接用 n。

确定好散列函数之后，通过某个key值的确会得到一个唯一的value地址。但是却会出现一些特殊情况。即通过不同的key值可能会访问到同一个地址，这个现象称之为冲突。

冲突在发生之后，当在对不同的key值进行操作时会使得造成相同地址的数据发生覆盖或者丢失，是非常危险的。所以在设计散列表往往还需要采用冲突解决的办法。

常用的冲突处理方式有很多，常用的包括以下几种：

开放地址法（也叫开放寻址法）：实际上就是当需要存储值时，对Key哈希之后，发现这个地址已经有值了，这时该怎么办？不能放在这个地址，不然之前的映射会被覆盖。这时对计算出来的地址进行一个探测再哈希，比如往后移动一个地址，如果没人占用，就用这个地址。如果超过最大长度，则可以对总长度取余。这里移动的地址是产生冲突时的增列序量。

再哈希法：在产生冲突之后，使用关键字的其他部分继续计算地址，如果还是有冲突，则继续使用其他部分再计算地址。这种方式的缺点是时间增加了。

链地址法：链地址法其实就是对Key通过哈希之后落在同一个地址上的值，做一个链表。其实在很多高级语言的实现当中，也是使用这种方式处理冲突的。

公共溢出区：这种方式是建立一个公共溢出区，当地址存在冲突时，把新的地址放在公共溢出区里。

目前比较常用的冲突解决方法是链地址法，一般可以通过数组和链表的结合达到冲突数据缓存的目的。

![在这里插入图片描述](assets\watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JlZXdvcmtzaG9w,size_16,color_FFFFFF,t_7017)
左侧数组的每个成员包括一个指针，指向一个链表的头。每发生一个冲突的数据，就将该数据作为链表的节点链接到链表尾部。这样一来，就可以保证冲突的数据能够区分并顺利访问。
考虑到链表过长造成的问题，还可以使用红黑树替换链表进行冲突数据的处理操作，来提高散列表的查询稳定性。



# 七 树 #

树作为一种树状的数据结构，其数据节点之间的关系也如大树一样，将有限个节点根据不同层次关系进行排列，从而形成数据与数据之间的父子关系。常见的数的表示形式更接近“倒挂的树”，因为它将根朝上，叶朝下。

树的数据存储在结点中，每个结点有零个或者多个子结点。没有父结点的结点在最顶端，成为根节点；没有非根结点有且只有一个父节点；每个非根节点又可以分为多个不相交的子树。

这意味着树是具备层次关系的，父子关系清晰，家庭血缘关系明朗；这也是树与图之间最主要的区别。

![在这里插入图片描述](assets\watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JlZXdvcmtzaG9w,size_16,color_FFFFFF,t_705)
别看树好像很高级，其实可看作是链表的高配版。树的实现就是对链表的指针域进行了扩充，增加了多个地址指向子结点。同时将“链表”竖起来，从而凸显了结点之间的层次关系，更便于分析和理解。

树可以衍生出许多的结构，若将指针域设置为双指针，那么即可形成最常见的二叉树，即每个结点最多有两个子树的树结构。二叉树根据结点的排列和数量还可进一度划分为完全二叉树、满二叉树、平衡二叉树、红黑树等。

![在这里插入图片描述](assets\watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JlZXdvcmtzaG9w,size_16,color_FFFFFF,t_706)
**完全二叉树**：除了最后一层结点，其它层的结点数都达到了最大值；同时最后一层的结点都是按照从左到右依次排布。

**满二叉树**：除了最后一层，其它层的结点都有两个子结点。

**平衡二叉树**
平衡二叉树又被称为AVL树，它是一棵二叉排序树，且具有以下性质：它是一棵空树或它的左右两个子树的高度差的绝对值不超过1，并且左右两个子树都是一棵平衡二叉树。

**二叉排序树**：是一棵空树，或者：若它的左子树不空，则左子树上所有结点的值均小于它的根结点的值；若它的右子树不空，则右子树上所有结点的值均大于它的根结点的值；它的左、右子树也分别为二叉排序树。

**树的高度**：结点层次的最大值

**平衡因子**：左子树高度 - 右子树高度

二叉排序树意味着二叉树中的数据是排好序的，顺序为左结点<根节点<右结点，这表明二叉排序树的**中序遍历**结果是有序的。

**二叉树的遍历**（是指从根结点出发，按照某种次序依次访问二叉树中所有的结点，使得每个结点被访问依次且仅被访问一次。
四种遍历方式分别为：前/先序遍历、中序遍历、后序遍历、层序遍历。

- ![img](assets\1542838-20190722222821663-1408544995.png)

1.前序遍历：先访问根节点，前序遍历左子树，前序遍历右子树【中->左->右】

![img](D:\课程研发\项目三加强课程\jdk集合\assets\watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDg0NjkzMw==,size_16,color_FFFFFF,t_70)

2.中序遍历：中序遍历左子树->根节点->中序遍历右子树【从根节点开始（注意并不是先访问根节点）【左中右】】

![img](D:\课程研发\项目三加强课程\jdk集合\assets\watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDg0NjkzMw==,size_16,color_FFFFFF,t_7099)

3.后序遍历：从左到右先叶子后节点的方式遍历访问左右子树，最后访问根节点。【逆时针【左右中】】

![img](D:\课程研发\项目三加强课程\jdk集合\assets\watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDg0NjkzMw==,size_16,color_FFFFFF,t_7090)

4.层序遍历：一层层滴不多说了

![img](D:\课程研发\项目三加强课程\jdk集合\assets\watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDg0NjkzMw==,size_16,color_FFFFFF,t_7098)

![在这里插入图片描述](assets\watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JlZXdvcmtzaG9w,size_16,color_FFFFFF,t_707)

平衡二叉树的产生是为了解决二叉排序树在插入时发生线性排列的现象。由于二叉排序树本身为有序，当插入一个有序程度十分高的序列时，生成的二叉排序树会持续在某个方向的字数上插入数据，导致最终的二叉排序树会退化为链表，从而使得二叉树的查询和插入效率恶化。

![在这里插入图片描述](assets\watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JlZXdvcmtzaG9w,size_16,color_FFFFFF,t_708)
平衡二叉树的出现能够解决上述问题，但是在构造平衡二叉树时，却需要采用不同的调整方式，使得二叉树在插入数据后保持平衡。主要的四种调整方式有LL（左旋）、RR（右旋）、LR（先左旋再右旋）、RL（先右旋再左旋）。这里先给大家介绍下简单的单旋转操作，左旋和右旋。LR和RL本质上只是LL和RR的组合。

在插入一个结点后应该沿搜索路径将路径上的结点平衡因子进行修改，当平衡因子大于1时，就需要进行平衡化处理。从发生不平衡的结点起，沿刚才回溯的路径取直接下两层的结点，如果这三个结点在一条直线上，则采用单旋转进行平衡化，如果这三个结点位于一条折线上，则采用双旋转进行平衡化。

左旋：S为当前需要左旋的结点，E为当前结点的父节点。

![在这里插入图片描述](assets\watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JlZXdvcmtzaG9w,size_16,color_FFFFFF,t_709)
左旋的操作可以用一句话简单表示：将当前结点S的左孩子旋转为当前结点父结点E的右孩子，同时将父结点E旋转为当前结点S的左孩子。可用动画表示：

![在这里插入图片描述](assets\20200526101513982.gif)
右旋：S为当前需要左旋的结点，E为当前结点的父节点。右单旋是左单旋的镜像旋转。

![在这里插入图片描述](assets\watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JlZXdvcmtzaG9w,size_16,color_FFFFFF,t_7010)
左旋的操作同样可以用一句话简单表示：将当前结点S的左孩子E的右孩子旋转为当前结点S的左孩子，同时将当前结点S旋转为左孩子E的右孩子。可用动画表示：

![在这里插入图片描述](assets\20200526101547284.gif)
红黑树
平衡二叉树（AVL）为了追求高度平衡，需要通过平衡处理使得左右子树的高度差必须小于等于1。高度平衡带来的好处是能够提供更高的搜索效率，其最坏的查找时间复杂度都是O(logN)。但是由于需要维持这份高度平衡，所付出的代价就是当对树种结点进行插入和删除时，需要经过多次旋转实现复衡。这导致AVL的插入和删除效率并不高。

为了解决这样的问题，能不能找一种结构能够兼顾搜索和插入删除的效率呢？这时候红黑树便申请出战了。

红黑树具有五个特性：

- 每个结点要么是红的要么是黑的。
- 根结点是黑的。
- 每个叶结点（叶结点即指树尾端NIL指针或NULL结点）都是黑的。
- 如果一个结点是红的，那么它的两个儿子都是黑的。
- 对于任意结点而言，其到叶结点树尾端NIL指针的每条路径都包含相同数目的黑结点。

![在这里插入图片描述](assets\watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JlZXdvcmtzaG9w,size_16,color_FFFFFF,t_7011)
红黑树通过将结点进行红黑着色，使得原本高度平衡的树结构被稍微打乱，平衡程度降低。红黑树不追求完全平衡，只要求达到部分平衡。这是一种折中的方案，大大提高了结点删除和插入的效率。HashMap用到红黑树作为底层的数据结构。

示意图：(红黑树首先是一颗搜索树，所以左子树点小于根结点，右子树大于根节点，等于根结点的按照自己的需要放左边和右边都是可以的）（红黑树不仅是一颗搜索树还是一颗非严格的平衡树）

图1

[![红黑树](D:\课程研发\项目三加强课程\从数据结构到Java常用集合\assets\1301290-20190418213128042-1536399603.jpg)

现在有个问题：红黑树是怎么通过上面5条性质保证任意结点到空的叶子结点的所有路径中，没有一条路径会大于其他路径的两倍的呢？（换句话说就是它是如何确保任何一个结点的左右子树的高度差不会超过二者中较低那个的一倍的呢？）

先结合红黑树的5个性质分析一下红黑树的一些操作，我们可能就明白了！

 

三.红黑树的基本操作之旋转

红黑树的基本操作是添加和删除，在对红黑树进行添加和删除之后，都会用到旋转方法，为什么呢？道理很简单，因为添加或者删除红黑树中的结点之后，红黑树就发生了变化，可能不满足上面的5条性质了，这个时候就需要通过旋转操作来保证它依旧是一棵红黑树，旋转分为左旋和右旋

（旋转操作仅仅只是用来调节结点的位置的，就是为了满足红黑树的性质5）

1.左旋

图2

![](D:\课程研发\项目三加强课程\从数据结构到Java常用集合\assets\939998-20160517212009529-1958413310.png)

左旋是将X的右子树绕X逆时针旋转，使得X的右子树成为X的父亲，同时修改相关结点的引用，旋转之后，要求二叉查找树的属性依然满足

2.右旋

图3

![](D:\课程研发\项目三加强课程\从数据结构到Java常用集合\assets\939998-20160517212020498-954534792.png)

右旋是将X的左子树绕X顺时针旋转，使得X的左子树成为X的父亲，同时注意修改相关结点的引用，旋转之后要求仍然满足搜索树的属性

四.红黑树的基本操作之添加元素

添加操作宏观过程：首先将红黑树当作一颗查找树一样将结点插入，然后将结点着为红色，最后通过旋转和重新着色的方法使之重新成为红黑树

将新加入的结点涂成红色的原因：

 

1）不违背红黑树的性质5：从任意一个结点出发到空叶子结点，经过的黑色结点个数相同

2）按照红黑树的性质4我们知道红黑树中黑结点的个数至少是红结点个数的两倍，所以新增结点的父亲结点是黑结点的概率比较大，如果新增结点的父节点为黑色，那么此时不需要再去进行任何调整操作，因此效率很高，所以新结点应该涂成红色

 

少违背一条性质，意味着我们后续的旋转和重新着色操作会简单很多

现在我们来看看新增一个红色的结点会违背红黑树的5条性质中的哪些？

> 1）每个结点或红或黑
>
> 2）根结点是黑色
>
> 3）空叶子结点是黑色
>
> 4）如果一个结点是红色，那么它的子节点是黑色
>
> 5）从任意一个结点出发到空的叶子结点经过的黑结点个数相同

1.显然没有违背

2.根据查找树的特定，插入操作不好改变根结点，所以也没有违背

3.插入的肯定不是空叶子结点，所以也没有违背

4.有可能违背！！！

5.插入结点涂成红色就是为了不违背第5条性质

现在我们来分析一下新增的结点（红色）插入之后可能面临的几种情况，以及他们的处理措施

 

1.插入的结点为根结点

将新插入的红色结点变成黑色结点，满足根结点为黑色结点的要求！

 

2.父亲结点为黑色结点

这个时候不需要进行任何调整操作，此时的树仍然是一颗标准的红黑树

 

3.父亲结点为红色结点的情况下，叔叔结点为红色结点（不用考虑左右）

解决方案：将叔叔和父亲结点改为黑色，爷爷结点改为红色，未完

然后又将爷爷结点当作插入结点看待，一直进行上面的操作，直到当前结点为根结点，然后将根结点变成黑色

原图：

图4

[![红黑树](D:\课程研发\项目三加强课程\从数据结构到Java常用集合\assets\1301290-20190418213129039-1750834042.jpg)

插入一个125的结点：

图5

[![红黑树](D:\课程研发\项目三加强课程\从数据结构到Java常用集合\assets\1301290-20190418213129777-667737935.jpg)

现在125结点和130结点都是红色的，显然违背了规则4，所以将新插入结点的父亲130结点和插入结点的叔叔结点150变成黑色，并将新插入结点的爷爷结点140变成红色，图如下：

图6

[![红黑树](D:\课程研发\项目三加强课程\从数据结构到Java常用集合\assets\1301290-20190418213130624-539000870.jpg)

然后又将140结点当作新插入结点处理（因为140结点和新插入结点面临的情况都是一样的：和父亲结点都是红色），也就是做如下处理：将140结点的父亲结点120和140的叔叔结点60变成黑色结点，将140结点的爷爷结点变成红色，因为遍历到了根结点，要满足根结点是黑色的性质要求，所以又将140的爷爷结点也就是根结点变成黑色，图如下：

图7

[![红黑树](D:\课程研发\项目三加强课程\从数据结构到Java常用集合\assets\1301290-20190418213131667-1276564969.jpg)

到这里，为新插入结点125所做的某些结点重新着色的操作就完成了，现在该树是标准的红黑树了！

 

4.新插入的结点的父亲结点为红色，其叔叔结点为黑色，

1）父亲结点为爷爷结点的左孩子，新插入结点为父节点的左孩子（左左情况）

2）父亲结点为爷爷结点的右孩子，新插入结点为父亲结点的右孩子（右右情况）

上述两种情况都是同一个处理办法

比如下图，新插入结点为25，其父亲结点30为红色，其叔叔结点为空黑色叶子结点，且新插入结点和其父节点都是左孩子：

图8

[![红黑树](D:\课程研发\项目三加强课程\从数据结构到Java常用集合\assets\1301290-20190418213133079-1448372531.jpg)

我们将其父亲结点和爷爷结点颜色互换，然后针对爷爷结点进行一次左旋，图如下：

图9

[![红黑树](D:\课程研发\项目三加强课程\从数据结构到Java常用集合\assets\1301290-20190418213134136-940254682.jpg)

现在这颗树完全满足红黑树的5个性质了（最好自己对照5个性质看一下）

现在又一个问题，我们为什么要进行旋转？

假设我们只将新增结点的父亲结点和其爷爷结点的颜色互换了，图如下：

图10

[![Inked红黑树_LI](D:\课程研发\项目三加强课程\从数据结构到Java常用集合\assets\1301290-20190418213136770-843083460.jpg)

我们发现上述两条到叶子结点的路径经过的黑色结点数量不一样！！！，所以它不满足红黑树的第5条性质，所以这就是我们旋转的意义所在！！！（因为无论你这么旋转都没有改变结点颜色，改变的是结点的位置，而这位置改变刚好能使得树满足红黑树的第5条性质！）

 

5.新插入的结点的父亲结点是红色，其叔叔结点是黑色

1）插入结点是右结点，父节点是左结点

2）插入结点是左结点，父亲结点是右结点

上述两种情况都是同一个处理办法

比如下图，新插入结点是126，其父结点125为红色，其叔叔结点为空的黑色结点，而且插入结点是右结点，父结点是左结点

图11

[![红黑树](D:\课程研发\项目三加强课程\从数据结构到Java常用集合\assets\1301290-20190418213137634-1958102851.jpg)

我们将父亲结点125看作当前结点进行左旋，旋转结果如下：

图12

[![红黑树](D:\课程研发\项目三加强课程\从数据结构到Java常用集合\assets\1301290-20190418213139039-597178902.jpg)

现在我们的当前结点是125，现在125的处境和上面的情况4是一样的（父节点为红，叔叔结点为黑，插入结点为左结点，父亲结点也为左孩子）现在我们继续按照情况4的处理办法处理上述情况（措施和情况4一样，父亲结点和爷爷结点互换颜色，然后针对爷爷结点进行左旋），处理后情况如下：

图13

[![红黑树](D:\课程研发\项目三加强课程\从数据结构到Java常用集合\assets\1301290-20190418213140154-225773995.jpg)

现在树就是一颗标准的红黑树了！

我们现在总结一下插入结点面临的几种情况以及采取的措施：

> 1.树为空，插入的结点为根结点
>
> 直接将插入的结点变成黑色
>
> 2.父亲结点为黑色结点
>
> 不需要任何操作
>
> 3.父亲结点为红色结点的情况下：
>
>   3.1 叔叔结点也为红色结点
>
>   将叔叔和父亲结点改为黑色，爷爷结点改为红色，未完，然后又将爷爷结点当作插入结点看待，一直进行上
>
>   面的操作，直到当前结点为根结点，然后将根结点变成黑色
>
>   3.2 叔叔结点为黑色结点的情况下：
>
> ​    3.2.1 （父亲结点为左孩子，插入结点也为左孩子）||（父亲结点为右孩子，插入结点也为右孩子）
>
> ​    将父亲结点和爷爷结点的颜色互换，然后针对爷爷结点进行一次左旋
>
> ​    3.2.2 （父亲结点为左孩子，插入结点为右孩子）||（父亲结点为右孩子，插入结点为左孩子）
>
> ​    针对父结点进行左旋，此时左旋后的情况必定是3.2.1的情况，然后按照3.2.1的情况处理

现在我们来讨论一下，为什么插入的情况只有上面这些：

> 1.爷爷结点为红色结点的情况下，父亲结点只能为黑色（红黑树的性质4），处理操作：上面情况2
>
> 2.爷爷结点为黑色的情况下，父亲结点和叔叔结点：可以为红色，也可以为黑色
>
> 2.1 父亲结点为黑，叔叔结点为黑：处理操作：上面情况2
>
> 2.2 父亲结点为黑，叔叔结点为红：处理操作：上面情况2
>
> 2.3 父亲结点为红，叔叔结点为红：处理操作：上面情况3.1
>
>    （上面3种情况都是不用考虑左右的）
>
> 2.4 父亲结点为红，叔叔结点为黑：
>
>    2.4.1 父亲结点为左孩子，叔叔结点为左孩子：处理操作：上面情况3.2.1
>
>    2.4.2 父亲结点为右孩子，叔叔结点为右孩子：处理操作：上面情况3.2.1
>
>    2.4.3 父亲结点为左孩子，插入结点为右孩子：处理操作：上面情况3.2.2
>
>    2.4.4 父亲结点为右孩子，插入结点为左孩子：处理操作：上面情况3.2.2

总结：可以发现我们没有遗漏任何情况，所有可能面临的情况我们都处理了

 

五.红黑树之删除结点

先说一个删除结点的过程原理：首先将红黑树当作一个二叉查找树，将该结点从二叉查找树种删除，然后通过一些列重新着色操作等一系列措施来修正该树，使之重新成为一颗红黑树

删除结点其实很容易，难的是如何使得删除结点后的树重新成为一个红黑树

我们可以根据删除结点N的儿子个数分为三种情况：

1.删除结点没有儿子

2.删除结点有1个儿子

3.删除结点有2个儿子

接下来我们又可以对以上三种情况继续进行细分

> 一.删除结点没有儿子的情况：
>
> 1）删除结点为红色
>
> 2）删除结点为黑色，其兄弟结点没有儿子
>
> 3）删除结点为黑色，其兄弟结点有一个孩子不空，并且该孩子为右孩子
>
> 4）删除结点为黑色，其兄弟结点有一个孩子不空，并且该孩子为左孩子
>
> 5）删除结点为黑色，其兄弟结点有两个孩子，而且兄弟结点为红色
>
> 6）删除结点为黑色，其兄弟结点有两个孩子，而且兄弟结点为黑色
>
>  
>
> 二.删除结点只有一个儿子的情况：
>
> 1）删除结点为黑色，其唯一的儿子结点为红色（必定是红色，要不然不符合红黑树的第5条性质）
>
> 2）删除结点为红色，其儿子结点只能为黑：红黑树中不存在这种情况，要不然无法满足红黑树第5条性质
>
>  
>
> 三.删除结点有两个儿子的情况：

 

现在我们就具体分析一下面临不同的操作到达该这么操作：

一.删除结点没有儿子的情况：

1）删除结点为红色

直接删除，比如下图，想要删除130结点

图14

[![红黑树2](D:\课程研发\项目三加强课程\从数据结构到Java常用集合\assets\1301290-20190418213143002-1972356300.jpg)

直接删除130结点，结果图如下：

图15

[![红黑树2](D:\课程研发\项目三加强课程\从数据结构到Java常用集合\assets\1301290-20190418213143907-1237419880.jpg)

因为删除的是红色结点，不会影响红黑树第5条性质，所以可以直接删除

 

2）删除结点为黑色，其兄弟结点没有儿子

这种情况下其兄弟结点也肯定是黑色的（要满足红黑树第5条性质），假设现在要删除的是150这个结点，原图如下：

图16

[![红黑树2](D:\课程研发\项目三加强课程\从数据结构到Java常用集合\assets\1301290-20190418213144640-886967587.jpg)

先删除结点150，然后将兄弟结点126变成红色，父亲结点140变成黑色，结果如下：
图17

[![红黑树2](D:\课程研发\项目三加强课程\从数据结构到Java常用集合\assets\1301290-20190418213145413-74677869.jpg)

这样做的目的是为了满足红黑树的第5条性质，要不然根到最右边的叶子结点经过的黑色结点只有3个，而其他路径有4个

 

3）删除结点为黑色，其兄弟结点有一个孩子不空，并且该孩子和兄弟结点在同一边（同为左子树或者同为右子树）

假设现在要删除的结点为110，其兄弟结点140只有一个孩子150，而且都是右子树，满足上述条件，原图如下：

图18

[![红黑树2](D:\课程研发\项目三加强课程\从数据结构到Java常用集合\assets\1301290-20190418213146047-331676828.jpg)

先把需要删除的结点110删除，然后这个时候需要交换兄弟结点140和父亲结点120的颜色，并且把父亲结点120涂成黑色，把兄弟结点的子节点150涂成黑色

1.如果兄弟结点和兄弟结点的儿子都在右子树的话：对父亲结点进行左旋

2.如果兄弟结点和兄弟结点的儿子都在左子树的话：对父亲结点进行右旋

上图是第一种情况，所以对父结点120进行左旋，结果如下：

图19

[![红黑树2](D:\课程研发\项目三加强课程\从数据结构到Java常用集合\assets\1301290-20190418213146883-809046894.jpg)

通过对某些结点重新着色和旋转，又将该树变成了一个标准的红黑树了

 

4）删除结点为黑色，其兄弟结点有一个孩子不空，并且该孩子和兄弟结点不在同一边（右左或者左右的情况）

（这种情况下，兄弟结点的儿子50结点只能为红色，要不然满足不了红黑树的第5条性质）

假设我们现在要删除的结点是80结点，其兄弟结点只有一个儿子，而且兄弟结点和兄弟结点的儿子是左右的情况（兄弟结点为左结点，兄弟结点的儿子为右结点），符合上述要求，原图如下：

图20

[![红黑树2](D:\课程研发\项目三加强课程\从数据结构到Java常用集合\assets\1301290-20190418213147585-336052673.jpg)

现在我们先将需要删除的80结点删除，然后将兄弟结点和兄弟结点的儿子结点颜色互换

如果兄弟结点是左子树，兄弟结点的儿子结点是右子树：对兄弟结点进行左旋

如果兄弟结点是右子树，兄弟结点的儿子结点是左子树：对兄弟结点进行右旋

上图的情况是进行左旋，也就是对兄弟结点30进行左旋，结果如下图：

图21

[![红黑树2](D:\课程研发\项目三加强课程\从数据结构到Java常用集合\assets\1301290-20190418213148409-1345914502.jpg)

注意！！，现在还没有结束变换，我们发现变换之后的红黑树情况和情况3中的情况很相似，兄弟结点50和兄弟结点的子节点30处在同一边，我们可以按照情况3的处理办法进行处理：

交换兄弟结点50和父亲结点60的颜色，把父亲结点60和兄弟结点的子节点30涂成黑色

1.如果兄弟结点和兄弟结点的儿子都在右子树的话：对父亲结点进行左旋

2.如果兄弟结点和兄弟结点的儿子都在左子树的话，对父亲结点进行右旋

上图的情况是第2中，所以对父亲结点60进行右旋，结果如下：

图22

[![红黑树2](D:\课程研发\项目三加强课程\从数据结构到Java常用集合\assets\1301290-20190418213826098-454745347.jpg)

 

5)删除结点为黑色，其兄弟结点有两个孩子，兄弟结点为黑色而且两个孩子结点也为黑色

现在我们假设要删除的结点是130结点，其兄弟结点有两个孩子（可以把空的叶子结点看成黑色的儿子结点），而且兄弟结点和兄弟结点的儿子结点都是黑色，符合上述情况，原图如下：

图23

[![红黑树2](D:\课程研发\项目三加强课程\从数据结构到Java常用集合\assets\1301290-20190418213826841-45465719.jpg)

先直接删除需要删除的结点130，然后将父亲结点140和兄弟结点150颜色互换即可，结果如下：

图24

[![红黑树2](D:\课程研发\项目三加强课程\从数据结构到Java常用集合\assets\1301290-20190418213827464-669837359.jpg)

 

6)删除结点为黑色，其兄弟结点有两个孩子，而且兄弟结点为红色

假设我们要删除的结点是110，其兄弟结点140为红色而且有两个孩子，原图如下：

图25

[![红黑树2](D:\课程研发\项目三加强课程\从数据结构到Java常用集合\assets\1301290-20190418213828198-1874419200.jpg)

我们先交换兄弟结点140和父亲结点120的颜色

1.被删除的元素为左子树：对父亲结点左旋

2.被删除的元素为右子树：对父亲结点右旋

上图的情况是第一种情况，所以我们对父亲结点140进行左旋，按照上面操作之后（未完），结果如下：

图26

[![红黑树2](D:\课程研发\项目三加强课程\从数据结构到Java常用集合\assets\1301290-20190418213828905-466826326.jpg)

我们发现完成上述操作之后树还不是一个标准的红黑树（到叶子结点的一条路径黑色结点只有3个，而其他的路径有4个），我们发现现在红黑树的情况又和情况5的很像，所以我们按照情况5的做法继续：

我们要需要删除的结点还没有被删除（我特意留到最后删除的，就是为了在这里表示父亲结点是谁的父亲结点...）,现在我们将父亲结点120和兄弟结点130的颜色互换即可，结果如下：

图27

[![红黑树2](D:\课程研发\项目三加强课程\从数据结构到Java常用集合\assets\1301290-20190418213829608-385779842.jpg)

 

我们现在对删除结点没有儿子结点的6种删除情况进行一下总结：

删除结点没有儿子结点：

> 1）删除结点为红色：
>
> 直接删除
>
>  
>
> 2）删除结点为黑色，其兄弟结点没有儿子：
>
> 兄弟结点变红，父亲结点变黑，然后将父亲结点当作当前结点按照这几种情形处理，直到当前结点为根结点
>
>  
>
> 3）删除结点为黑色，其兄弟结点有一个孩子不空，并且该孩子和兄弟结点在同一边（同为左子树或者同为右子树）：
>
> 1.不管是括号中那种情况，先交换兄弟结点和父亲结点的颜色，并且把父亲结点和兄弟结点的子结点涂成黑色
>
> 2.1如果兄弟结点和兄弟结点的儿子都在右子树的话：对父亲结点进行左旋
>
> 2.2如果兄弟结点和兄弟结点的儿子都在左子树的话：对父亲结点进行右旋
>
>  
>
> 4）删除结点为黑色，其兄弟结点有一个孩子不空，并且该孩子和兄弟结点不在同一边（右左或者左右的情况）：
>
> 1.先将兄弟结点和兄弟结点的儿子结点颜色互换
>
> 2.1如果兄弟结点是左子树，兄弟结点的儿子结点是右子树：对兄弟结点进行左旋
>
> 2.2如果兄弟结点是右子树，兄弟结点的儿子结点是左子树：对兄弟结点进行右旋
>
> 3.将后续变换按照第3条处理
>
>  
>
> 5)删除结点为黑色，其兄弟结点有两个孩子，兄弟结点为黑色而且两个孩子结点也为黑色：
>
> 1.将父亲结点和兄弟结点颜色互换
>
>  
>
> 6）删除结点为黑色，其兄弟结点有两个孩子，而且兄弟结点为红色：
>
> 1.将兄弟结点和父亲结点的颜色互换
>
> 2.1 被删除的元素为左子树：对父亲结点左旋
>
> 2.2 被删除的元素为右子树：对父亲结点右旋
>
> 3.将后续变换按照第5条进行处理

以上6种情况讨论的都是删除结点没有儿子的情况（空叶子结点不算儿子结点）

 

现在我们来看看删除结点仅有一个儿子结点的情况！

 

二.删除结点仅有一个儿子结点的情况

1）删除结点为黑色，儿子结点无论左右都可以

比如我们要删除的结点是120结点，删除结点为黑色，唯一的儿子结点130为红色（必须是红色，不然违背红黑树第5条性质）原图如下：

图28

[![红黑树2](D:\课程研发\项目三加强课程\从数据结构到Java常用集合\assets\1301290-20190418213830431-836031235.jpg)

我们将需要删除的结点120删除，然后将子节点130涂黑放到被删除结点120的位置，结果如下：

图29

[![红黑树2](D:\课程研发\项目三加强课程\从数据结构到Java常用集合\assets\1301290-20190418213831046-1031506610.jpg)

 

2）删除结点为红色：其儿子结点只能为黑，红黑树中不存在这种情况，要不然无法满足红黑树第5条性质

 

总结一下删除结点只有一个儿子的情况：

> 1）删除结点为黑色，儿子结点无论左右都可以
>
> 将儿子结点涂成黑色放到被删除结点的位置

 

下面我们来看看删除结点有两个儿子结点的情况

三.删除结点有两个儿子结点

找到删除结点的右子树中最左的结点，两两值交换，然后删除结点的情况就变成了上面两种情况中的一种了

1.删除结点只有一个儿子的情况

2.删除结点没有儿子的情况

比如下图

图30

[![红黑树2](D:\课程研发\项目三加强课程\从数据结构到Java常用集合\assets\1301290-20190418213831789-1881553616.jpg)

假设要删除的结点是120，先找到结点120右子树中最左的结点125，交换两者的值，图如下：

图31

[![红黑树2](D:\课程研发\项目三加强课程\从数据结构到Java常用集合\assets\1301290-20190418213832474-2085751065.jpg)

现在120仍然是要删除的结点，我们发现删除结点120没有一个儿子，而且其兄弟结点也没有儿子，那么其对应的情况为：

> 2）删除结点为黑色，其兄弟结点没有儿子：
>
> 兄弟结点变红，父亲结点变黑

经过上面的变形，结果如下：

图32

[![红黑树2](D:\课程研发\项目三加强课程\从数据结构到Java常用集合\assets\1301290-20190418213833448-224931959.jpg)

经过变换，该树变成了一颗标准的红黑树

所以当删除结点右两个儿子结点的时候，我们只需要按照搜索二叉树的删除方法替换删除值，这样就可以将情况变成删除结点没有儿子结点或者1个儿子结点的情况处理了

**红黑树VS平衡二叉树**

![在这里插入图片描述](assets\watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JlZXdvcmtzaG9w,size_16,color_FFFFFF,t_7014)

## 案例 HashMap ##

### 1 HashMap概述 ###

　　HashMap是基于哈希表的Map接口实现的，它存储的是内容是键值对<key,value>映射。此类不保证映射的顺序，假定哈希函数将元素适当的分布在各桶之间，可为基本操作(get和put)提供稳定的性能。

　　在API中给出了相应的定义：

````java
//1、哈希表基于map接口的实现，这个实现提供了map所有的操作，并且提供了key和value可以为null，(HashMap和HashTable大致上是一样的除了hashmap是异步的和允许key和value为null)，
这个类不确定map中元素的位置，特别要提的是，这个类也不确定元素的位置随着时间会不会保持不变。
Hash table based implementation of the Map interface. This implementation provides all of the optional map operations, and permits null values and the null key. 
(The HashMap class is roughly equivalent to Hashtable, except that it is unsynchronized and permits nulls.) This class makes no guarantees as to the order of the map;
 in particular, it does not guarantee that the order will remain constant over time. 

//假设哈希函数将元素合适的分到了每个桶(其实就是指的数组中位置上的链表)中，则这个实现为基本的操作(get、put)提供了稳定的性能，迭代这个集合视图需要的时间跟hashMap实例(key-value映射的数量)的容量(在桶中)
成正比，因此，如果迭代的性能很重要的话，就不要将初始容量设置的太高或者loadfactor设置的太低，【这里的桶，相当于在数组中每个位置上放一个桶装元素】
This implementation provides constant-time performance for the basic operations (get and put), assuming the hash function disperses the elements properly among the buckets.
 Iteration over collection views requires time proportional to the "capacity" of the HashMap instance (the number of buckets) plus its size (the number of key-value mappings
). Thus, it's very important not to set the initial capacity too high (or the load factor too low) if iteration performance is important.

//HashMap的实例有两个参数影响性能，初始化容量(initialCapacity)和loadFactor加载因子，在哈希表中这个容量是桶的数量【也就是数组的长度】，一个初始化容量仅仅是在哈希表被创建时容量，在
容量自动增长之前加载因子是衡量哈希表被允许达到的多少的。当entry的数量在哈希表中超过了加载因子乘以当前的容量，那么哈希表被修改(内部的数据结构会被重新建立)所以哈希表有大约两倍的桶的数量
An instance of HashMap has two parameters that affect its performance: initial capacity and load factor. The capacity is the number of buckets in the hash table, 
and the initial capacity is simply the capacity at the time the hash table is created. The load factor is a measure of how full the hash table is allowed to get before
 its capacity is automatically increased. When the number of entries in the hash table exceeds the product of the load factor and the current capacity, the hash table 
is rehashed (that is, internal data structures are rebuilt) so that the hash table has approximately twice the number of buckets.

//通常来讲，默认的加载因子(0.75)能够在时间和空间上提供一个好的平衡，更高的值会减少空间上的开支但是会增加查询花费的时间（体现在HashMap类中get、put方法上），当设置初始化容量时，应该考虑到map中会存放
entry的数量和加载因子，以便最少次数的进行rehash操作，如果初始容量大于最大条目数除以加载因子，则不会发生 rehash 操作。

As a general rule, the default load factor (.75) offers a good tradeoff between time and space costs. Higher values decrease the space overhead but increase the lookup
 cost (reflected in most of the operations of the HashMap class, including get and put). The expected number of entries in the map and its load factor should be taken 
into account when setting its initial capacity, so as to minimize the number of rehash operations. If the initial capacity is greater than the maximum number of
 entries divided by the load factor, no rehash operations will ever occur.

//如果很多映射关系要存储在 HashMap 实例中，则相对于按需执行自动的 rehash 操作以增大表的容量来说，使用足够大的初始容量创建它将使得映射关系能更有效地存储。
If many mappings are to be stored in a HashMap instance, creating it with a sufficiently large capacity will allow the mappings to be stored more efficiently than letting 
it perform automatic rehashing as needed to grow the table
复制代码
 
````

### 2 HashMap在JDK1.8以前数据结构和存储原理 ###

　　1）链表散列

　　　　首先我们要知道什么是链表散列？通过数组和链表结合在一起使用，就叫做链表散列。这其实就是hashmap存储的原理图。

　　　　![img](assets\999804-20171020095001240-1758940988.png)

　　2）HashMap的数据结构和存储原理

　　　　HashMap的数据结构就是用的链表散列。那HashMap底层是怎么样使用这个数据结构进行数据存取的呢？分成两个部分：

　　　　第一步：HashMap内部有一个entry的内部类，其中有四个属性，我们要存储一个值，则需要一个key和一个value，存到map中就会先将key和value保存在这个Entry类创建的对象中。

```java
  　　　　 static class Entry<K,V> implements Map.Entry<K,V> {
      　　　　  final K key;    //就是我们说的map的key
       　　　　 V value;    //value值，这两个都不陌生
       　　　　 Entry<K,V> next;//指向下一个entry对象
       　　　　 int hash;//通过key算过来的你hashcode值。
```

　　　　Entry的物理模型图：

　　　　　　　　![img](assets\999804-20171020095553412-640392318.png)

　　　　第二步：构造好了entry对象，然后将该对象放入数组中，如何存放就是这hashMap的精华所在了。

　　　　　　大概的一个存放过程是：通过entry对象中的hash值来确定将该对象存放在数组中的哪个位置上，如果在这个位置上还有其他元素，则通过链表来存储这个元素。

　　　　　　![img](assets\999804-20171020100326084-97317885.png)

　　3）Hash存放元素的过程

　　　　通过key、value封装成一个entry对象，然后通过key的值来计算该entry的hash值，通过entry的hash值和数组的长度length来计算出entry放在数组中的哪个位置上面，

　　　　每次存放都是将entry放在第一个位置。在这个过程中，就是通过hash值来确定将该对象存放在数组中的哪个位置上。

### 3 JDK1.8后HashMap的数据结构 ###

　　![img](assets\999804-20171020102900959-842879038.png)

　　上图很形象的展示了HashMap的数据结构（数组+链表+红黑树），桶中的结构可能是链表，也可能是红黑树，红黑树的引入是为了提高效率。

### 4 HashMap的属性 ###

　　HashMap的实例有两个参数影响其性能。

　　初始容量：哈希表中桶的数量

　　加载因子：哈希表在其容量自动增加之前可以达到多满的一种尺度

　　当哈希表中条目数超出了当前容量*加载因子(其实就是HashMap的实际容量)时，则对该哈希表进行rehash操作，将哈希表扩充至两倍的桶数。

　　Java中默认初始容量为16，加载因子为0.75。

```java
  static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16
  static final float DEFAULT_LOAD_FACTOR = 0.75f;
```

　　1）loadFactor加载因子

　　　　定义：loadFactor译为装载因子。装载因子用来衡量HashMap满的程度。loadFactor的默认值为0.75f。计算HashMap的实时装载因子的方法为：size/capacity，而不是占用桶的数量去除以capacity。loadFactor加载因子是控制数组存放数据的疏密程度，loadFactor越趋近于1，那么数组中存放的数据(entry)也就越多，也就越密，也就是会让链表的长度增加，loadFactor越小，也就是趋近于0，那么数组中存放的数据也就越稀，也就是可能数组中每个位置上就放一个元素。那有人说，就把loadFactor变为1最好吗，存的数据很多，但是这样会有一个问题，就是我们在通过key拿到我们的value时，是先通过key的hashcode值，找到对应数组中的位置，如果该位置中有很多元素，则需要通过equals来依次比较链表中的元素，拿到我们的value值，这样花费的性能就很高，如果能让数组上的每个位置尽量只有一个元素最好，我们就能直接得到value值了，所以有人又会说，那把loadFactor变得很小不就好了，但是如果变得太小，在数组中的位置就会太稀，也就是分散的太开，浪费很多空间，这样也不好，所以在hashMap中loadFactor的初始值就是0.75，一般情况下不需要更改它。

```java
   static final float DEFAULT_LOAD_FACTOR = 0.75f;
```

　　2）桶

　　　　根据前面画的HashMap存储的数据结构图，你这样想，数组中每一个位置上都放有一个桶，每个桶里就是装一个链表，链表中可以有很多个元素(entry)，这就是桶的意思。也就相当于把元素都放在桶中。

　　3）capacity

　　　　capacity译为容量代表的数组的容量，也就是数组的长度，同时也是HashMap中桶的个数。默认值是16。  一般第一次扩容时会扩容到64，之后好像是2倍。总之，**容量都是2的幂**。

```java
   static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16
```

　　4）size的含义

　　　　size就是在该HashMap的实例中实际存储的元素的个数

　　5）threshold的作用

　　　　threshold = capacity * loadFactor，当Size>=threshold的时候，那么就要考虑对数组的扩增了，也就是说，这个的意思就是衡量数组是否需要扩增的一个标准。注意这里说的是考虑，因为实际上要扩增数组，除了这个size>=threshold条件外，还需要另外一个条件。什么时候会扩增数组的大小？在put一个元素时先size>=threshold并且还要在对应数组位置上有元素，这才能扩增数组。

​				我们通过一张HashMap的数据结构图来分析：

　　　　![img](assets\999804-20171020102005084-969177556.png)

### 5 HashMap的源码分析 ###

#### 5.1 HashMap的层次关系与继承结构 ####

　　1）HashMap继承结构

　　　　![img](assets\999804-20171020103233662-1996355541.png)

　　上面就继承了一个abstractMap，也就是用来减轻实现Map接口的编写负担。

　　2）实现接口

　　　　![img](assets\999804-20171020103354834-374455750.png)

　　　   Cloneable：能够使用Clone()方法，在HashMap中，实现的是浅层次拷贝，即对拷贝对象的改变会影响被拷贝的对象。

　　　　Serializable：能够使之序列化，即可以将HashMap对象保存至本地，之后可以恢复状态。

#### 5.2 HashMap类的属性 ####

````java
public class HashMap<K,V> extends AbstractMap<K,V> implements Map<K,V>, Cloneable, Serializable {
    // 序列号
    private static final long serialVersionUID = 362498820763181265L;    
    // 默认的初始容量是16
    static final int DEFAULT_INITIAL_CAPACITY = 1 << 4;   
    // 最大容量
    static final int MAXIMUM_CAPACITY = 1 << 30; 
    // 默认的填充因子
    static final float DEFAULT_LOAD_FACTOR = 0.75f;
    // 当桶(bucket)上的结点数大于这个值时会转成红黑树
    static final int TREEIFY_THRESHOLD = 8; 
    // 当桶(bucket)上的结点数小于这个值时树转链表
    static final int UNTREEIFY_THRESHOLD = 6;
    // 桶中结构转化为红黑树对应的table的最小大小
    static final int MIN_TREEIFY_CAPACITY = 64;
    // 存储元素的数组，总是2的幂次倍
    transient Node<k,v>[] table; 
    // 存放具体元素的集
    transient Set<map.entry<k,v>> entrySet;
    // 存放元素的个数，注意这个不等于数组的长度。
    transient int size;
    // 每次扩容和更改map结构的计数器
    transient int modCount;   
    // 临界值 当实际大小(容量*填充因子)超过临界值时，会进行扩容
    int threshold;
    // 填充因子
    final float loadFactor;
}
````

#### 5.3 HashMap的构造方法 ####

　　有四个构造方法，构造方法的作用就是记录一下16这个数给threshold（这个数值最终会当作第一次数组的长度。）和初始化加载因子。注意，hashMap中table数组一开始就已经是个没有长度的数组了。

　　构造方法中，并没有初始化数组的大小，数组在一开始就已经被创建了，构造方法只做两件事情，一个是初始化加载因子，另一个是用threshold记录下数组初始化的大小。注意是记录。

　　![img](assets\999804-20171020103751849-329344119.png)

　　1）HashMap()

```java
//看上面的注释就已经知道，DEFAULT_INITIAL_CAPACITY=16，DEFAULT_LOAD_FACTOR=0.75
//初始化容量：也就是初始化数组的大小
//加载因子：数组上的存放数据疏密程度。
    public HashMap() {
        this(DEFAULT_INITIAL_CAPACITY, DEFAULT_LOAD_FACTOR);
    }
```

　　2）HashMap(int)

```java
    public HashMap(int initialCapacity) {
        this(initialCapacity, DEFAULT_LOAD_FACTOR);
    }
```

　　3）HashMap(int,float)

```java
public HashMap(int initialCapacity, float loadFactor) {
    // 初始容量不能小于0，否则报错
    if (initialCapacity < 0)
        throw new IllegalArgumentException("Illegal initial capacity: " +
                                            initialCapacity);
    // 初始容量不能大于最大值，否则为最大值
    if (initialCapacity > MAXIMUM_CAPACITY)
        initialCapacity = MAXIMUM_CAPACITY;
    // 填充因子不能小于或等于0，不能为非数字
    if (loadFactor <= 0 || Float.isNaN(loadFactor))
        throw new IllegalArgumentException("Illegal load factor: " +
                                            loadFactor);
    // 初始化填充因子                                        
    this.loadFactor = loadFactor;
    // 初始化threshold大小
    this.threshold = tableSizeFor(initialCapacity);    
}
```

　　4）HashMap(Map<? extends K, ? extends V> m)

```java
public HashMap(Map<? extends K, ? extends V> m) {
    // 初始化填充因子
    this.loadFactor = DEFAULT_LOAD_FACTOR;
    // 将m中的所有元素添加至HashMap中
    putMapEntries(m, false);
} 
```

 　putMapEntries(Map<? extends K, ? extends V> m, boolean evict)函数将m的所有元素存入本HashMap实例中

```java
final void putMapEntries(Map<? extends K, ? extends V> m, boolean evict) {
    int s = m.size();
    if (s > 0) {
        // 判断table是否已经初始化
        if (table == null) { // pre-size
            // 未初始化，s为m的实际元素个数
            float ft = ((float)s / loadFactor) + 1.0F;
            int t = ((ft < (float)MAXIMUM_CAPACITY) ?
                    (int)ft : MAXIMUM_CAPACITY);
            // 计算得到的t大于阈值，则初始化阈值
            if (t > threshold)
                threshold = tableSizeFor(t);
        }
        // 已初始化，并且m元素个数大于阈值，进行扩容处理
        else if (s > threshold)
            resize();
        // 将m中的所有元素添加至HashMap中
        for (Map.Entry<? extends K, ? extends V> e : m.entrySet()) {
            K key = e.getKey();
            V value = e.getValue();
            putVal(hash(key), key, value, false, evict);
        }
    }
}
```

#### 5.4 主要方法 ####

　　这里我们来看一下我们常用的一些方法的源码

##### 1 put方法 #####

　　1）put(K key,V value)

```java
   public V put(K key, V value) {
        return putVal(hash(key), key, value, false, true);
    }
```

　　2）putVal(int hash, K key, V value, boolean onlyIfAbsent,boolean evict)

```java
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
    Node<K,V>[] tab; Node<K,V> p; int n, i;
    // table未初始化或者长度为0，进行扩容
    if ((tab = table) == null || (n = tab.length) == 0)
        n = (tab = resize()).length;
    // (n - 1) & hash 确定元素存放在哪个桶中，桶为空，新生成结点放入桶中(此时，这个结点是放在数组中)
    if ((p = tab[i = (n - 1) & hash]) == null)
        tab[i] = newNode(hash, key, value, null);
    // 桶中已经存在元素
    else {
        Node<K,V> e; K k;
        // 比较桶中第一个元素(数组中的结点)的hash值相等，key相等
        if (p.hash == hash &&
            ((k = p.key) == key || (key != null && key.equals(k))))
                // 将第一个元素赋值给e，用e来记录
                e = p;
        // hash值不相等，即key不相等；且为红黑树结点
        else if (p instanceof TreeNode)
            // 放入树中
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
        // 为链表结点
        else {
            // 在链表最末插入结点
            for (int binCount = 0; ; ++binCount) {
                // 到达链表的尾部
                if ((e = p.next) == null) {
                    // 在尾部插入新结点
                    p.next = newNode(hash, key, value, null);
                    // 结点数量达到阈值，转化为红黑树
                    if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                        treeifyBin(tab, hash);
                    // 跳出循环
                    break;
                }
                // 判断链表中结点的key值与插入的元素的key值是否相等
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    // 相等，跳出循环
                    break;
                // 用于遍历桶中的链表，与前面的e = p.next组合，可以遍历链表
                p = e;
            }
        }
        // 表示在桶中找到key值、hash值与插入元素相等的结点
        if (e != null) { 
            // 记录e的value
            V oldValue = e.value;
            // onlyIfAbsent为false或者旧值为null
            if (!onlyIfAbsent || oldValue == null)
                //用新值替换旧值
                e.value = value;
            // 访问后回调
            afterNodeAccess(e);
            // 返回旧值
            return oldValue;
        }
    }
    // 结构性修改
    ++modCount;
    // 实际大小大于阈值则扩容
    if (++size > threshold)
        resize();
    // 插入后回调
    afterNodeInsertion(evict);
    return null;
} 

final TreeNode<K,V> putTreeVal(HashMap<K,V> map, Node<K,V>[] tab,
                               int h, K k, V v) {
    // map  当前Hashmap对象
    //tab   Hashmap对象中的table数组
    //h      hash值
    // K     key
    // V     value
    Class<?> kc = null;
    boolean searched = false;  	//标识是否被收索过
    TreeNode<K,V> root = (parent != null) ? root() : this; // 找到root根节点
    for (TreeNode<K,V> p = root;;) {	//从根节点开始遍历循环
        int dir, ph; K pk;
        // 根据hash值 判断方向
        if ((ph = p.hash) > h)
            // 大于放左侧
            dir = -1;
        else if (ph < h)
            // 小于放右侧
            dir = 1;
        // 如果key 相等  直接返回该节点的引用 外边的方法会对其value进行设置
        else if ((pk = p.key) == k || (k != null && k.equals(pk)))
            return p;

        /**
				*下面的步骤主要就是当发生冲突 也就是hash相等的时候
				* 检验是否有hash相同 并且equals相同的节点。
				* 也就是检验该键是否存在与该树上
				*/
        //说明进入下面这个判断的条件是 hash相同 但是equal不同
        // 没有实现Comparable<C>接口或者 实现该接口 并且 k与pk Comparable比较结果相同
        else if ((kc == null &&
                  (kc = comparableClassFor(k)) == null) ||
                 (dir = compareComparables(kc, k, pk)) == 0) {
            //在左右子树递归的寻找 是否有key的hash相同  并且equals相同的节点
            if (!searched) {
                TreeNode<K,V> q, ch;
                searched = true;
                if (((ch = p.left) != null &&
                     (q = ch.find(h, k, kc)) != null) ||
                    ((ch = p.right) != null &&
                     (q = ch.find(h, k, kc)) != null))
                    //找到了 就直接返回
                    return q;
            }


            //通过再hash的方式对比key的hash值
            dir = tieBreakOrder(k, pk);
        }
        //下列操作进行插入节点
        //xp 保存当前节点
        TreeNode<K,V> xp = p;
        //找到要插入节点的位置
        if ((p = (dir <= 0) ? p.left : p.right) == null) {
            Node<K,V> xpn = xp.next;
            //创建出一个新的节点
            TreeNode<K,V> x = map.newTreeNode(h, k, v, xpn);
            if (dir <= 0)
                //小于父亲节点  新节点放左孩子位置
                xp.left = x;
            else
                //大于父亲节点  放右孩子位置
                xp.right = x;

            //维护双链表关系
            xp.next = x;
            x.parent = x.prev = xp;
            if (xpn != null)
                ((TreeNode<K,V>)xpn).prev = x;
            //将root移到table数组的i 位置的第一个节点
            //插入操作过红黑树之后 重新调整平衡。
            moveRootToFront(tab, balanceInsertion(root, x));
            return null;
        }
    }
}

static int tieBreakOrder(Object a, Object b) {
       int d;
       //新增节点的key为空
       //或当前节点的key为空
       //或a和b的类型相同
       if (a == null || b == null ||
           (d = a.getClass().getName().
            compareTo(b.getClass().getName())) == 0)
           d = (System.identityHashCode(a) <= System.identityHashCode(b) ?
                -1 : 1);
       return d;
   }


//红黑树平衡的过程参照这个blog地址 https://www.cnblogs.com/yinbiao/p/10732600.html
static <K,V> TreeNode<K,V> balanceInsertion(TreeNode<K,V> root,TreeNode<K,V> x) {
            //新插入树节点默认红色                                        
            x.red = true;
            //x是新插入节点、xp是新插入节点的父节点、xpp是新插入节点的祖父节点、
            //xppl是新插入节点的左叔叔节点、xppr是新插入节点的右叔叔节点
            for (TreeNode<K,V> xp, xpp, xppl, xppr;;) {
            	//1.空树
                if ((xp = x.parent) == null) {
                	//新插入节点颜色变为黑色
                    x.red = false;
                    return x;
                }
                //2.父节点黑色或祖父节点为空
                else if (!xp.red || (xpp = xp.parent) == null)
                    return root;
                //3.父节点红色
                //3.1父节点是祖父节点的左儿子
                if (xp == (xppl = xpp.left)) {
                	//3.1.1叔叔节点红色
                    if ((xppr = xpp.right) != null && xppr.red) {
                        xppr.red = false;
                        xp.red = false;
                        xpp.red = true;
                        x = xpp;
                    }
                    //3.1.2叔叔节点不存在
                    else {
                    	//3.1.2.1新插入节点是父节点右儿子
                        if (x == xp.right) {
                        	//左旋
                            root = rotateLeft(root, x = xp);
                            xpp = (xp = x.parent) == null ? null : xp.parent;
                        }
                        //3.1.2.2新插入节点是父节点左儿子
                        if (xp != null) {
                            xp.red = false;
                            if (xpp != null) {
                                xpp.red = true;
                                //右旋
                                root = rotateRight(root, xpp);
                            }
                        }
                    }
                }
                //3.2父节点是祖父节点右儿子
                else {
                	//3.2.1叔叔节点红色
                    if (xppl != null && xppl.red) {
                        xppl.red = false;
                        xp.red = false;
                        xpp.red = true;
                        x = xpp;
                    }
                    //3.2.2叔叔节点不存在
                    else {
                    	//3.2.2.1新插入节点是父节点左儿子
                        if (x == xp.left) {
                        	//右旋
                            root = rotateRight(root, x = xp);
                            xpp = (xp = x.parent) == null ? null : xp.parent;
                        }
                        //3.2.2.2新插入节点是父节点右儿子
                        if (xp != null) {
                            xp.red = false;
                            if (xpp != null) {
                                xpp.red = true;
                                //左旋
                                root = rotateLeft(root, xpp);
                            }
                        }
                    }
                }
            }
        }


 static <K,V> void moveRootToFront(Node<K,V>[] tab, TreeNode<K,V> root) {
            int n;
            //基本判空条件
            if (root != null && tab != null && (n = tab.length) > 0) {
            	// 获得当红黑树所处在 table数组的哪一个位置   (n - 1) & root.hash效果与取模相同
                int index = (n - 1) & root.hash;
                // 获得改位置的节点对象 也就是链表的第一个节点
                TreeNode<K,V> first = (TreeNode<K,V>)tab[index];
                //如果这个位置不是 root节点 那么就要进行调整了
                if (root != first) {
                //定义一个临时Node 变量
                    Node<K,V> rn;
                    //直接将链表的第一个位置 指向了root节点 
                    tab[index] = root;


                    // 获得root节点的前驱
                    //获得root后继 
                    //将前驱和后继相连接 root节点从原链表中脱离出来了
                    TreeNode<K,V> rp = root.prev;
                    if ((rn = root.next) != null)
                        ((TreeNode<K,V>)rn).prev = rp;
                    if (rp != null)
                        rp.next = rn;

					// 将root与原来双链表的第一个节点相连
					//这样root又回到双链表当中 并且在双链表的第一个位置上
                    if (first != null)
                        first.prev = root;
                    root.next = first;
                    //root节点的前驱节点设置为null  因为他双链表的第一个节点 没有前驱
                    root.prev = null;


                }
                assert checkInvariants(root);
            }
        }
```

　　HashMap并没有直接提供putVal接口给用户调用，而是提供的put函数，而put函数就是通过putVal来插入元素的。　　

　

##### 2 get方法 #####

　　1）get(Object key)

```java
public V get(Object key) {
        Node<K,V> e;
        return (e = getNode(hash(key), key)) == null ? null : e.value;
    }
```

　　2）getNode(int hash,Pbject key)

```java
final Node<K,V> getNode(int hash, Object key) {
    Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
    // table已经初始化，长度大于0，根据hash寻找table中的项也不为空
    if ((tab = table) != null && (n = tab.length) > 0 &&
        (first = tab[(n - 1) & hash]) != null) {
        // 桶中第一项(数组元素)相等
        if (first.hash == hash && // always check first node
            ((k = first.key) == key || (key != null && key.equals(k))))
            return first;
        // 桶中不止一个结点
        if ((e = first.next) != null) {
            // 为红黑树结点
            if (first instanceof TreeNode)
                // 在红黑树中查找
                return ((TreeNode<K,V>)first).getTreeNode(hash, key);
            // 否则，在链表中查找
            do {
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    return e;
            } while ((e = e.next) != null);
        }
    }
    return null;
}
```

　　HashMap并没有直接提供getNode接口给用户调用，而是提供的get函数，而get函数就是通过getNode来取得元素的。

##### 3 resize方法 #####

```java
final Node<K,V>[] resize() {
    // 当前table保存
    Node<K,V>[] oldTab = table;
    // 保存table大小
    int oldCap = (oldTab == null) ? 0 : oldTab.length;
    // 保存当前阈值 
    int oldThr = threshold;
    int newCap, newThr = 0;
    // 之前table大小大于0
    if (oldCap > 0) {
        // 之前table大于最大容量
        if (oldCap >= MAXIMUM_CAPACITY) {
            // 阈值为最大整形
            threshold = Integer.MAX_VALUE;
            return oldTab;
        }
        // 容量翻倍，使用左移，效率更高
        else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
            oldCap >= DEFAULT_INITIAL_CAPACITY)
            // 阈值翻倍
            newThr = oldThr << 1; // double threshold
    }
    // 之前阈值大于0
    else if (oldThr > 0)
        newCap = oldThr;
    // oldCap = 0并且oldThr = 0，使用缺省值（如使用HashMap()构造函数，之后再插入一个元素会调用resize函数，会进入这一步）
    else {           
        newCap = DEFAULT_INITIAL_CAPACITY;
        newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
    }
    // 新阈值为0
    if (newThr == 0) {
        float ft = (float)newCap * loadFactor;
        newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                  (int)ft : Integer.MAX_VALUE);
    }
    threshold = newThr;
    @SuppressWarnings({"rawtypes","unchecked"})
    // 初始化table
    Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
    table = newTab;
    // 之前的table已经初始化过
    if (oldTab != null) {
        // 复制元素，重新进行hash
        for (int j = 0; j < oldCap; ++j) {
            Node<K,V> e;
            if ((e = oldTab[j]) != null) {
                oldTab[j] = null;
                if (e.next == null)
                    newTab[e.hash & (newCap - 1)] = e;
                else if (e instanceof TreeNode)
                    ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                else { // preserve order
                    Node<K,V> loHead = null, loTail = null;
                    Node<K,V> hiHead = null, hiTail = null;
                    Node<K,V> next;
                    // 将同一桶中的元素根据(e.hash & oldCap)是否为0进行分割，分成两个不同的链表，完成rehash
                    do {
                        next = e.next;
                        if ((e.hash & oldCap) == 0) {
                            if (loTail == null)
                                loHead = e;
                            else
                                loTail.next = e;
                            loTail = e;
                        }
                        else {
                            if (hiTail == null)
                                hiHead = e;
                            else
                                hiTail.next = e;
                            hiTail = e;
                        }
                    } while ((e = next) != null);
                    if (loTail != null) {
                        loTail.next = null;
                        newTab[j] = loHead;
                    }
                    if (hiTail != null) {
                        hiTail.next = null;
                        newTab[j + oldCap] = hiHead;
                    }
                }
            }
        }
    }
    return newTab;
}
```

　　进行扩容，会伴随着一次重新hash分配，并且会遍历hash表中所有的元素，是非常耗时的。在编写程序中，要尽量避免resize。

　　在resize前和resize后的元素布局如下:

　　　　![img](assets\999804-20171020110308881-1563067628.png)

　　　　上图只是针对了数组下标为2的桶中的各个元素在扩容后的分配布局，其他各个桶中的元素布局可以以此类推。

### 总结 ###

#### 1、关于数组扩容 ####

　　从putVal源代码中我们可以知道，当插入一个元素的时候size就加1，若size大于threshold的时候，就会进行扩容。假设我们的capacity大小为32，loadFator为0.75,则threshold为24 = 32 * 0.75，

　　此时，插入了25个元素，并且插入的这25个元素都在同一个桶中，桶中的数据结构为红黑树，则还有31个桶是空的，也会进行扩容处理，其实，此时，还有31个桶是空的，好像似乎不需要进行扩容处理，

　　但是是需要扩容处理的，因为此时我们的capacity大小可能不适当。我们前面知道，扩容处理会遍历所有的元素，时间复杂度很高；前面我们还知道，经过一次扩容处理后，元素会更加均匀的分布在各个桶中，

　　会提升访问效率。所以说尽量避免进行扩容处理，也就意味着，遍历元素所带来的坏处大于元素在桶中均匀分布所带来的好处。　

#### 2、总结 ####

　　1）要知道hashMap在JDK1.8以前是一个链表散列这样一个数据结构，而在JDK1.8以后是一个数组加链表加红黑树的数据结构。

　　2）通过源码的学习，hashMap是一个能快速通过key获取到value值得一个集合，原因是内部使用的是hash查找值得方法。





# 八 堆 #

了解完二叉树，再来理解堆就不是什么难事了。堆通常是一个可以被看做一棵树的数组对象。堆的具体实现一般不通过指针域，而是通过构建一个一维数组与二叉树的父子结点进行对应，因此堆总是一颗完全二叉树。

对于任意一个父节点的序号n来说（这里n从0算），它的子节点的序号一定是2n+1，2n+2，因此可以直接用数组来表示一个堆。

不仅如此，堆还有一个性质：堆中某个节点的值总是不大于或不小于其父节点的值。将根节点最大的堆叫做最大堆或大根堆，根节点最小的堆叫做最小堆或小根堆。

![在这里插入图片描述](assets\watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JlZXdvcmtzaG9w,size_16,color_FFFFFF,t_7015)
堆常用来实现优先队列，在面试中经常考的问题都是与排序有关，比如堆排序、topK问题等。由于堆的根节点是序列中最大或者最小值，因而可以在建堆以及重建堆的过程中，筛选出数据序列中的极值，从而达到排序或者挑选topK值的目的。

## 案例PriorityBlockingQueue中二叉堆的使用 ##

### put过程： ###

````java
public void put(E e) {
        offer(e); // never need to block
    }


 public boolean offer(E e) {
        if (e == null)
            throw new NullPointerException();
        final ReentrantLock lock = this.lock;
        lock.lock();//加锁
        int n, cap;
        Object[] array;
     	//如果元素个数大于等于数组长度，开始扩容
        while ((n = size) >= (cap = (array = queue).length))
            tryGrow(array, cap);
        try {
            //一开始构建队列的时候传入comparator
            Comparator<? super E> cmp = comparator;
            if (cmp == null)
                //使用元素自带的comparator进行比较，并排序
                siftUpComparable(n, e, array);
            else
                //使用comparator进行比较，并排序
                siftUpUsingComparator(n, e, array, cmp);
            size = n + 1;//增加元素个数
            notEmpty.signal();//唤醒take线程
        } finally {
            lock.unlock();//解锁
        }
        return true;
    }

//新元素堆内上浮实现

    private static <T> void siftUpComparable(int k, T x, Object[] array) {
        Comparable<? super T> key = (Comparable<? super T>) x; //尝试转化插入对象为Comparable实例
        while (k > 0) {
            int parent = (k - 1) >>> 1; // 新元素x的数组下标为k，对应的父节点的下标为(k-1)/2
            Object e = array[parent];
            if (key.compareTo((T) e) >= 0) //如果子节点已经比父节点还要大，不需要再跟上层节点比较，新元素上浮结束
                break;
            array[k] = e; //如果子节点已经比父节点小，父节点下沉，新元素上浮一次
            k = parent; //新元素上浮后继续与新的父节点比较大小，直到k=0或者新的父节点小于新元素
        }
        array[k] = key;//新元素在堆中插入正确的位置。
    }

 private void tryGrow(Object[] array, int oldCap) {
     	//扩容期间需要解锁，能够让其他线程继续执行
        lock.unlock(); // must release and then re-acquire main lock
        Object[] newArray = null;//新数组
     	//cas标识，如果为0，表示当前没有线程对数组扩容
        if (allocationSpinLock == 0 &&
            //cas方式设置值0->1
            UNSAFE.compareAndSwapInt(this, allocationSpinLockOffset,
                                     0, 1)) {
            try {
                //开始扩容
                //计算新的数组长度
                int newCap = oldCap + ((oldCap < 64) ?
                                       (oldCap + 2) : // grow faster if small
                                       (oldCap >> 1));
                //计算容量不能超过最大大小
                if (newCap - MAX_ARRAY_SIZE > 0) {    // possible overflow
                    int minCap = oldCap + 1;
                    if (minCap < 0 || minCap > MAX_ARRAY_SIZE)
                        throw new OutOfMemoryError();
                    newCap = MAX_ARRAY_SIZE;
                }
                if (newCap > oldCap && queue == array)
                    newArray = new Object[newCap];//创建新的数组用来保存元素
            } finally {
                allocationSpinLock = 0;//重置标识，表示扩容完毕
            }
        }
        if (newArray == null) // back off if another thread is allocating
            Thread.yield();
        lock.lock();//重新加锁
     	//如果数组还是原来的数组
        if (newArray != null && queue == array) {
            queue = newArray;//设置为新数组
            System.arraycopy(array, 0, newArray, 0, oldCap);//转移元素到新数组
        }
    }
````

### take过程 ###

````java
 public E take() throws InterruptedException {
        final ReentrantLock lock = this.lock;
        lock.lockInterruptibly();//加锁
        E result;
        try {
            while ( (result = dequeue()) == null)
                notEmpty.await();
        } finally {
            lock.unlock();
        }
        return result;
    }

private E dequeue() {
        int n = size - 1;
        if (n < 0)
            return null;//队列为空
        else {
            Object[] array = queue;
            E result = (E) array[0];//取出第一个,二叉堆中第一个最小
            E x = (E) array[n];
            array[n] = null;//设置为空
            Comparator<? super E> cmp = comparator;
            if (cmp == null)
                siftDownComparable(0, x, array, n);
            else
                siftDownUsingComparator(0, x, array, n, cmp);
            size = n;
            return result;
        }
    }

//空元素堆内下沉实现
    private static <T> void siftDownComparable(int k, T x, Object[] array, int n) {
        if (n > 0) {
            Comparable<? super T> key = (Comparable<? super T>) x;
            int half = n >>> 1; // loop while a non-leaf  half最后一个有子节点的父节点下标
            while (k < half) { 
                int child = (k << 1) + 1; // assume left child is least
                Object c = array[child];
                int right = child + 1;
                if (right < n
                        && ((Comparable<? super T>) c)
                                .compareTo((T) array[right]) > 0)
                    c = array[child = right];  //比较出左右子节点更小的那个子节点
                if (key.compareTo((T) c) <= 0) //如果左右子节点的最小值大于数组末尾的值，那么数组末尾的值直接放到父节点，空节点下沉结束
                    break;
                array[k] = c; // 如果子节点最小值小于数据末尾的值，子节点上浮到父空节点
                k = child; //空节点下滑到最小子节点的位置
            }
            array[k] = key; // 最后空节点填充数组最后的值
        }
    }
````


# 集合
---
## 概述
### Java集合两大接口：**Collection、Map**
![Java集合框架](https://guide-blog-images.oss-cn-shenzhen.aliyuncs.com/github/javaguide/java/collection/java-collection-hierarchy.png)

### List、Set、Queue、Map区别
* List：（对付顺序好帮手）有序，可重复
* Set：（元素独一无二）无序，不可重复
* Queue：（实现排队功能的叫号机）按特定排队规则来确定先后顺序，有序，可重复
* Map：（键值对存储）key无序，不可重复；value无序，可重复

### 集合的数据结构
#### List
* ArrayList：Object[]数组
* LinkedList：双向链表
* Vector：Object[]数组

#### Set
* HashSet：底层采用HashMap来存储数据
* TreeSet：红黑树
* LinkedHashSet：LinkedHashMap

#### Map
* HashMap:jdk1.8之前数组+链表，1.8之后数组+链表+红黑树
* LinkedHashMap：LinkedHashMap 继承自 HashMap，所以它的底层仍然是基于拉链式散列结构即由数组和链表或红黑树组成。另外，LinkedHashMap 在上面结构的基础上，增加了一条双向链表，使得上面的结构可以保持键值对的插入顺序。同时通过对链表进行相应的操作，实现了访问顺序相关逻辑。
* Hashtable：数组+链表
* TreeMap：红黑树

### 如何选择集合
主要根据**集合和数据的特点**来决定。存储键值对数据可以使用Map接口下的集合，需要排序用TreeMap，
不需要排序用HashMap，需要线程安全用ConcurrentHashMap

当只存放元素时选择Collection接口的集合，保证唯一元素用Set，不需要用List，具体用那种集合根据集合特点来选用

### 为什么使用集合
我们存储一组相同数据类型，可以使用数组来进行存储，但在开发过程中，数据类型多种多样，于是出现集合来存储多个数据
<br>数组一旦申明长度不可变，同时，声明数组时的数据类型也决定了该数组存储的数据的类型；而且，数组存储的数据是有序的、可重复的，特点单一。 但是集合提高了数据存储的灵活性，Java 集合不仅可以用来存储不同类型不同数量的对象，还可以保存具有映射关系的数据。

## Collection接口的List
### ArrayList和Vector的区别
* ArrayList：底层Object[]数组，有序，可重复，线程不安全，查询效率高
* Vector：古老的集合，底层Object[]数组，有序，可重复，线程安全，效率低

### ArrayList和LinkedList的区别
* 线程都不安全
* 底层数组、双向列表
* 插入和删除是否受元素位置影响：<br>
  **ArrayList** 采用数组存储，所以插入和删除元素的时间复杂度受元素位置的影响。 比如：执行add(E e)方法的时候， ArrayList 会默认在将指定的元素追加到此列表的末尾，这种情况时间复杂度就是 O(1)。但是如果要在指定位置 i 插入和删除元素的话（add(int index, E element)）时间复杂度就为 O(n-i)。因为在进行上述操作的时候集合中第 i 和第 i 个元素之后的(n-i)个元素都要执行向后位/向前移一位的操作。
  <br>**LinkedList**采用链表存储，所以，如果是在头尾插入或者删除元素不受元素位置的影响（add(E e)、addFirst(E e)、addLast(E e)、removeFirst() 、 removeLast()），时间复杂度为 O(1)，如果是要在指定位置 i 插入和删除元素的话（add(int index, E element)，remove(Object o)）， 时间复杂度为 O(n) ，因为需要先移动到指定位置再插入。
* 是否支持快速随机访问：<br>
ArrarList支持，可以根据索引对数据进行操作，LinkedList不支持
* 内存占用：<br>
  ArrayList的空间浪费主要在list的末尾要预留一部分空间，而LinkedList要在每个元素存放指针，要比AraayList占用更多的空间
>  ArrayList扩容机制（https://javaguide.cn/java/collection/arraylist-source-code.html#_3-1-%E5%85%88%E4%BB%8E-arraylist-%E7%9A%84%E6%9E%84%E9%80%A0%E5%87%BD%E6%95%B0%E8%AF%B4%E8%B5%B7）
## Collection接口的Set
### comparable 和 Comparator 的区别
* comparable 接口实际上是出自java.lang包 它有一个 compareTo(Object obj)方法用来排序
* comparator接口实际上是出自 java.util 包它有一个compare(Object obj1, Object obj2)方法用来排序
> 一般我们需要对一个集合使用自定义排序时，我们就要重写compareTo()方法或compare()方法，当我们需要对某一个集合实现两种排序方式，比如一个 song 对象中的歌名和歌手名分别采用一种排序方法的话，我们可以重写compareTo()方法和使用自制的Comparator方法或者以两个 Comparator 来实现歌名排序和歌星名排序，第二种代表我们只能使用两个参数版的 Collections.sort()
#### Comparator 定制排序
        List<Integer> arrayList = new ArrayList();
        arrayList.add(-1);
        arrayList.add(6);
        arrayList.add(4);
        arrayList.add(-3);
        arrayList.add(9);
        arrayList.add(0);
        System.out.println("原始集合："+arrayList);

        Collections.reverse(arrayList);
        System.out.println("反转后集合: "+arrayList);

        Collections.sort(arrayList);
        System.out.println("自然排序后集合：" + arrayList);

        Collections.sort(arrayList, new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                return o2.compareTo(o1);
            }
        });
        System.out.println("定制排序后集合： " + arrayList);
#### 重写 compareTo 方法实现按年龄来排序
      @Data
      public class Person implements Comparable<Person> {
      private int age;
      private String name;
      public Person(String name, int age){
        super();
        this.name = name;
        this.age = age;
        }
      @Override
      public int compareTo(Person o) {
        if (this.age < o.getAge()){
            return -1;
        }
        if (this.age > o.getAge()){
            return 1;
        }
            return 0;
            }

      public static void main(String[] args) {
        TreeMap<Person,String> data = new TreeMap<Person,String>();
        data.put(new Person("张三",12),"zhangsan");
        data.put(new Person("李四", 23), "lisi");
        data.put(new Person("黄二",9),"huanger");
        data.put(new Person("大飞", 24), "dafei");
        data.put(new Person("刘盲",6),"liumang");
        data.put(new Person("景华", 18), "jinghua");
        Set<Person> set = data.keySet();
        for (Person key : set){
            System.out.println(key.getAge() +"-"+ key.getName());
        }
        }
      }

### 比较HashSet，LinkedHashSet，TreeSet
* HashSet，LinkedHashSet，TreeSet都是Set接口下的集合，**都能保证元素的唯一，线程都不安全**
* 从**数据结构**方面：HashSet底层数据结构哈希表（基于HashMap实现）；LinkedHashSet底层基于HashMap和链表，元素取出和插入满足FIFO；
TreeSet底层是红黑树，支持对元素的自然排序和自定义排序
* 数据结构的不同又导致三种集合的**应用场景**不同，不需要排序可以使用HashSet；需要保证元素插入和取出顺序满足FIFO使用LinkedHashSet；需要对元素进行排序用TreeSet

## Collection接口的Queue
* Queue是单端队列，支持从一段插入元素，从另一端删除元素，实现上一般遵循FIFO规则
* Deque是双端队列，是对Queue的扩展，增加了在队首和队尾插入和删除元素的方法；Deque还提供了push()和pop()
方法可以用来模拟栈

### ArrayDeque和LinkedList的区别
ArrayDeque和LinkedList都实现了Deque接口，二者都具有队列功能
* ArrayDeque是基于数组和双指针实现的，LinkedList是基于链表实现
* ArrayDeque不支持存储NULL数据，但LinkedList支持
* ArrayDeque插入时可能需要存在扩容过程，但均摊过后的插入操作依然为O(1)；LinkedList不需要扩容，但每次插入数据需要需要申请新的堆空间，均摊性能比ArrayDeque更慢
> 从性能上，ArrayDeque实现队列要比LinkedList要好，并且ArrayDeque可以实现栈

### PriorityQueue
PriorityQueue 是在 JDK1.5 中被引入的, 其与 Queue 的区别在于元素出队顺序是与优先级相关的，即总是优先级最高的元素先出队。

这里列举其相关的一些要点：
* PriorityQueue 利用了二叉堆的数据结构来实现的，底层使用可变长的数组来存储数据
* PriorityQueue 通过堆元素的上浮和下沉，实现了在 O(logn) 的时间复杂度内插入元素和删除堆顶元素。
* PriorityQueue 是非线程安全的，且不支持存储 NULL 和 non-comparable 的对象。
* PriorityQueue 默认是小顶堆，但可以接收一个 Comparator 作为构造参数，从而来自定义元素优先级的先后。
* PriorityQueue 在面试中可能更多的会出现在手撕算法的时候，典型例题包括堆排序、求第K大的数、带权图的遍历等，所以需要会熟练使用才行。

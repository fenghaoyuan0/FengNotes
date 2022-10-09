## Map接口
---
### HashMap和HashTable的区别
* **线程安全：**HashMap线程不安全，HashTable线程安全，但Hashtable基本所有方法都是用synchronized修饰，效率很低，如果想使用线程安全的Map可以使用ConcurrentHashMap
* **数据结构：**HashMap在1.8之后在处理哈希冲突方面做了很大改变，当链表长度达到阈值（默认是8）时，会将链表转为红黑树（再转为红黑树之前会判断数组长度是否小于64，if是会先对数组经行扩容而不是将链表转为红黑树），以减少搜索时间
* **初始容量和扩容大小：**
  1. Hashtable初始容量是11，每次扩容容量会变为原来的2n+1；HHashMap初始容量16，每次扩容会变为原来的2倍
  2. Hashtable创建时给定了初始值，那么Hashtable会直接使用你给定的初值；而HashMap会扩充为2的幂次。
* **Null：**HashMap允许存在null key和null value，null key只允许存在唯一一个；Hashtable的key和value都不允许为空，否则会抛出NullPointerException

### HashMap和HashSet的区别
* HashSet底层维护一个HashMap，除个别方法不得不实现其他都是直接调用HashMap的方法；
* 这两个集合分别实现Map和Set接口；
* map存储键值对，set存储对象；map调用put()方法添加元素，set调用add()方法来添加元素；
* map使用key来计算hashcode，set使用成员对象来计算hashcode

### HashMap和TreeMap的区别
TreeMap 和HashMap 都继承自AbstractMap ，但是需要注意的是TreeMap它还实现了NavigableMap接口和SortedMap 接口。
![TreeMap](https://guide-blog-images.oss-cn-shenzhen.aliyuncs.com/github/javaguide/java/collection/treemap_hierarchy.png)
实现 NavigableMap 接口让 TreeMap 有了对集合内元素的搜索的能力。

实现SortedMap接口让 TreeMap 有了对集合中的元素根据键排序的能力。默认是按 key 的升序排序，不过我们也可以指定排序的比较器。
> 综上，相比于HashMap来说 TreeMap 主要多了对集合中的元素根据键排序的能力以及对集合内元素的搜索的能力。

### HashSet如何检查重复元素
引入《Head first java》第二版：
>当你把对象加入HashSet时，HashSet 会先计算对象的hashcode值来判断对象加入的位置，同时也会与其他加入的对象的 hashcode 值作比较，如果没有相符的 hashcode，HashSet 会假设对象没有重复出现。但是如果发现有相同 hashcode 值的对象，这时会调用equals()方法来检查 hashcode 相等的对象是否真的相同。如果两者相同，HashSet 就不会让加入操作成功。

### HashMap的底层实现
jdk1.8之前底层使用数组和链表结合，也就是列表散列，也叫“拉链法”，HashMap通过key的hashcode经扰动函数（hash方法）处理得到hash值，然后通过(n - 1) & hash判断数据存储位置，如果当前位置存在元素，会判断key是否相同，如相同则覆盖，否则通过拉链法解决哈希冲突问题。

比较jdk1.7和1.8HashMap：
* 数据结构：1.7数组+链表；1.8数组+链表+红黑树  
* 扰动函数：1.7四次；1.8一次

putVal 方法中执行链表转红黑树的判断逻辑。链表的长度大于 8 的时候，就执行 treeifyBin （转换红黑树）的逻辑。↓

```java
    for (int binCount = 0; ; ++binCount) {
      // 遍历链表到最后一个节点
      if ((e = p.next) == null) {
          p.next = newNode(hash, key, value, null);
          // 如果链表元素个数大于等于TREEIFY_THRESHOLD（8）
          if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
              // 红黑树转换（并不会直接转换成红黑树）
              treeifyBin(tab, hash);
              break;
    }
      if (e.hash == hash &&
          ((k = e.key) == key || (key != null && key.equals(k))))
          break;
          p = e;
        }
```
treeifyBin 方法中判断是否真的转换为红黑树。↓
```java
    final void treeifyBin(Node<K,V>[] tab, int hash) {
      int n, index; Node<K,V> e;
      // 判断当前数组的长度是否小于 64
      if (tab == null || (n = tab.length) < MIN_TREEIFY_CAPACITY)
          // 如果当前数组的长度小于 64，那么会选择先进行数组扩容
          resize();
          else if ((e = tab[index = (n - 1) & hash]) != null) {
            // 否则才将列表转换为红黑树
            TreeNode<K,V> hd = null, tl = null;
            do {
              TreeNode<K,V> p = replacementTreeNode(e, null);
            if (tl == null)
                hd = p;
            else {
                p.prev = tl;
                tl.next = p;
            }
            tl = p;
        } while ((e = e.next) != null);
        if ((tab[index] = hd) != null)
            hd.treeify(tab);
          }
        }
```
### HashMap 的长度为什么是 2 的幂次方
为了能让 HashMap 存取高效，尽量较少碰撞，也就是要尽量把数据分配均匀。我们上面也讲到了过了，Hash 值的范围值-2147483648 到 2147483647，前后加起来大概 40 亿的映射空间，只要哈希函数映射得比较均匀松散，一般应用是很难出现碰撞的。但问题是一个 40 亿长度的数组，内存是放不下的。所以这个散列值是不能直接拿来用的。用之前还要先做对数组的长度取模运算，得到的余数才能用来要存放的位置也就是对应的数组下标。这个数组下标的计算方法是“ (n - 1) & hash”。（n 代表数组长度）。这也就解释了 HashMap 的长度为什么是 2 的幂次方。
#### 这个算法应该如何设计呢？
我们首先可能会想到采用%取余的操作来实现。但是，重点来了：“取余(%)操作中如果除数是 2 的幂次则等价于与其除数减一的与(&)操作（也就是说 hash%length==hash&(length-1)的前提是 length 是 2 的 n 次方；）。” 并且 采用二进制位操作 &，相对于%能够提高运算效率，这就解释了 HashMap 的长度为什么是 2 的幂次方。

### HashMap的七种遍历方式
```java
    @Benchmark
    public void entrySet(){
        Iterator<Map.Entry<Integer, String>> iterator = map.entrySet().iterator();
        while (iterator.hasNext()){
            Map.Entry<Integer, String> entry = iterator.next();
            Integer k = entry.getKey();
            String v = entry.getValue();
        }
    }
    @Benchmark
    public void keySet(){
        Iterator<Integer> iterator = map.keySet().iterator();
        while (iterator.hasNext()) {
            Integer k = iterator.next();
            String v = map.get(k);
        }
    }
    @Benchmark
    public void forEachEntrySet(){
        for (Map.Entry<Integer, String> entry : map.entrySet()) {
            Integer k = entry.getKey();
            String v = entry.getValue();
        }
    }
    @Benchmark
    public void forEachKeySet(){
        for(Integer key : map.keySet()){
            Integer k = key;
            String v = map.get(k);
        }
    }
    @Benchmark
    public void lambda(){
        map.forEach((key, value) ->{
            Integer k = key;
            String v = value;
        });
    }
    @Benchmark
    public void streamApi(){
        map.entrySet().stream().forEach((entry) ->{
            Integer k = entry.getKey();
            String v = entry.getValue();
        });
    }
    @Benchmark
    public void parallelStreamApi(){
        map.entrySet().parallelStream().forEach((entry) ->{
            Integer k = entry.getKey();
            String v = entry.getValue();
        });
    }
```
> **entrySet 效率最高，keySet效率最低，建议使用entrySet遍历Map**

## ConcurrentHashMap和HashTable的区别
* 底层数据结构： JDK1.7 的 ConcurrentHashMap 底层采用 分段的数组+链表 实现，JDK1.8 采用的数据结构跟 HashMap1.8 的结构一样，数组+链表/红黑二叉树。Hashtable 和 JDK1.8 之前的 HashMap 的底层数据结构类似都是采用 数组+链表 的形式，数组是 HashMap 的主体，链表则是主要为了解决哈希冲突而存在的；
* 实现线程安全的方式（重要）：
<br>
 在 JDK1.7 的时候，ConcurrentHashMap 对整个桶数组进行了分割分段(Segment，分段锁)，每一把锁只锁容器其中一部分数据（下面有示意图），多线程访问容器里不同数据段的数据，就不会存在锁竞争，提高并发访问率。<br><br>
到了 JDK1.8 的时候，ConcurrentHashMap 已经摒弃了 Segment 的概念，而是直接用 Node 数组+链表+红黑树的数据结构来实现，并发控制使用 synchronized 和 CAS 来操作。（JDK1.6 以后 synchronized 锁做了很多优化） 整个看起来就像是优化过且线程安全的 HashMap，虽然在 JDK1.8 中还能看到 Segment 的数据结构，但是已经简化了属性，只是为了兼容旧版本；<br><br>
Hashtable(同一把锁) :使用 synchronized 来保证线程安全，效率非常低下。当一个线程访问同步方法时，其他线程也访问同步方法，可能会进入阻塞或轮询状态，如使用 put 添加元素，另一个线程不能使用 put 添加元素，也不能使用 get，竞争会越来越激烈效率越低。

### ConcurrentHashMap 线程安全的具体实现方式/底层具体实现
<br>

**jdk1.8之前:**
![before1.8](https://guide-blog-images.oss-cn-shenzhen.aliyuncs.com/github/javaguide/java/collection/java7_concurrenthashmap.png)
* 1.8之前采用**分段锁**的形式来保证线程安全，将数据分成一段一段（segmen）来存放HashEntry(链表结构)，**segment继承了ReentrantLock**，所以segment是一种重入锁，扮演锁的角色来保证线程安全，HashEntry来存储键值对数据；<br>
* 一个 ConcurrentHashMap 里包含一个 Segment 数组，Segment 的个数一旦初始化就不能改变。 Segment 数组的大小默认是 16，也就是说默认可以同时支持 16 个线程并发写。

**jdk1.8之后：**
![after1.8](https://guide-blog-images.oss-cn-shenzhen.aliyuncs.com/github/javaguide/java/collection/java8_concurrenthashmap.png)
* 1.8以后摒弃了分段锁的，采用**Node+synchronized+CAS**来实现线程安全
* Java 8 中，锁粒度更细，synchronized 只锁定当前链表或红黑二叉树的首节点，这样只要 hash 不冲突，就不会产生并发，就不会影响其他 Node 的读写，效率大幅提升。


### ConcurrentHashMap在jdk1.7和1.8的改变
* 线程安全实现方式 ：JDK 1.7 采用 Segment 分段锁来保证安全， Segment 是继承自 ReentrantLock。JDK1.8 放弃了 Segment 分段锁的设计，采用 Node + CAS + synchronized 保证线程安全，锁粒度更细，synchronized 只锁定当前链表或红黑二叉树的首节点。
* Hash 碰撞解决方法 : JDK 1.7 采用拉链法，JDK1.8 采用拉链法结合红黑树（链表长度超过一定阈值时，将链表转换为红黑树）。
* 并发度 ：JDK 1.7 最大并发度是 Segment 的个数，默认是 16。JDK 1.8 最大并发度是 Node 数组的大小，并发度更大。

## Collections 工具类（不重要）

### Collections 工具类常用方法:
* 排序
* 查找,替换操作
* 同步控制(不推荐，需要线程安全的集合类型时请考虑使用 JUC 包下的并发集合)
#### 排序操作
```java
void reverse(List list)//反转
void shuffle(List list)//随机排序
void sort(List list)//按自然排序的升序排序
void sort(List list, Comparator c)//定制排序，由Comparator控制排序逻辑
void swap(List list, int i , int j)//交换两个索引位置的元素
void rotate(List list, int distance)//旋转。当distance为正数时，将list后distance个元素整体移到前面。当distance为负数时，将 list的前distance个元素整体移到后面
```
#### void reverse(List list)//反转
```java
void shuffle(List list)//随机排序
void sort(List list)//按自然排序的升序排序
void sort(List list, Comparator c)//定制排序，由Comparator控制排序逻辑
void swap(List list, int i , int j)//交换两个索引位置的元素
void rotate(List list, int distance)//旋转。当distance为正数时，将list后distance个元素整体移到前面。当distance为负数时，将 list的前distance个元素整体移到后面
```
#### 同步控制(不推荐使用)
```java
synchronizedCollection(Collection<T>  c) //返回指定 collection 支持的同步（线程安全的）collection。
synchronizedList(List<T> list)//返回指定列表支持的同步（线程安全的）List。
synchronizedMap(Map<K,V> m) //返回由指定映射支持的同步（线程安全的）Map。
synchronizedSet(Set<T> s) //返回指定 set 支持的同步（线程安全的）set。
```

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

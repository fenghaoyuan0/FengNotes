## Map接口
### HashMap和HashTable的区别
* 线程安全：HashMap线程不安全，HashTable线程安全，但HashMap基本所有方法都是用synchronized修饰，效率很低，如果想使用线程安全的Map可以使用ConcurrentHashMap
* 数据结构：HashMap在1.8之后在处理哈希冲突方面做了很大改变，当链表长度达到阈值（默认是8）时，会将链表转为红黑树（再转为红黑树之前会判断数组长度是否小于64，if是会先对数组经行扩容而不是将链表转为红黑树），以减少搜索时间
* 1. 初始容量和扩容大小：Hashtable初始容量是11，每次扩容容量会变为原来的2n+1；HHashMap初始容量16，每次扩容会变为原来的2倍
  2. Hashtable创建时给定了初始值，那么Hashtable会直接使用你给定的初值；而HashMap会扩充为2的幂次。

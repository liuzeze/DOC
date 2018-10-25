## HashMap

HashMap 内部是数组加链表的形式,jdk1.8 之后在链表长度大于8 会转换为红黑树存储,他是线程不安全的 允许null  会将value村粗到0 的位置   这两个特性区别于hashTable    想要线程安全需要使用 ConCurrentHashMap  或者使用 Collections.synchronizedMap(new HashMap())  jdk1.8 引入红黑树优化了 hash值就算不均匀时的冲突性能   linkedhashMap  内部是个双向链表保证了顺序



1. 构造   

   可以传入负载因子 和容量    负载因子和空间使用和查询速度和冲突几率有关系

2. put方法

   ①.判断键值对数组table[i]是否为空或为null，否则执行resize()进行扩容；

   ②.根据键值key计算hash值得到插入的数组索引i，如果table[i]==null，直接新建节点添加，转向⑥，如果table[i]不为空，转向③；

   ③.判断table[i]的首个元素是否和key一样，如果相同直接覆盖value，否则转向④，这里的相同指的是hashCode以及equals；

   ④.判断table[i] 是否为treeNode，即table[i] 是否是红黑树，如果是红黑树，则直接在树中插入键值对，否则转向⑤；

   ⑤.遍历table[i]，判断链表长度是否大于8，大于8的话把链表转换为红黑树，在红黑树中执行插入操作，否则进行链表的插入操作；遍历过程中若发现key已经存在直接覆盖value即可；

   ⑥.插入成功后，判断实际存在的键值对数量size是否超多了最大容量threshold，如果超过，进行扩容。

   ```
    ``6` `final` `V putVal(``int` `hash, K key, V value, ``boolean` `onlyIfAbsent,
    ``7`                `boolean` `evict) {
    ``8`     `Node<K,V>[] tab; Node<K,V> p; ``int` `n, i;
    ``9`     `// 步骤①：tab为空则创建
   10`     `if` `((tab = table) == ``null` `|| (n = tab.length) == ``0``)
   11`         `n = (tab = resize()).length;
   12`     `// 步骤②：计算index，并对null做处理 
   13`     `if` `((p = tab[i = (n - ``1``) & hash]) == ``null``) 
   14`         `tab[i] = newNode(hash, key, value, ``null``);
   15`     `else` `{
   16`         `Node<K,V> e; K k;
   17`         `// 步骤③：节点key存在，直接覆盖value
   18`         `if` `(p.hash == hash &&
   19`             `((k = p.key) == key || (key != ``null` `&& key.equals(k))))
   20`             `e = p;
   21`         `// 步骤④：判断该链为红黑树
   22`         `else` `if` `(p ``instanceof` `TreeNode)
   23`             `e = ((TreeNode<K,V>)p).putTreeVal(``this``, tab, hash, key, value);
   24`         `// 步骤⑤：该链为链表
   25`         `else` `{
   26`             `for` `(``int` `binCount = ``0``; ; ++binCount) {
   27`                 `if` `((e = p.next) == ``null``) {
   28`                     `p.next = newNode(hash, key,value,``null``);
                           ``//链表长度大于8转换为红黑树进行处理
   29`                     `if` `(binCount >= TREEIFY_THRESHOLD - ``1``) ``// -1 for 1st  
   30`                         `treeifyBin(tab, hash);
   31`                     `break``;
   32`                 `}
                       ``// key已经存在直接覆盖value
   33`                 `if` `(e.hash == hash &&
   34`                     `((k = e.key) == key || (key != ``null` `&& key.equals(k))))                                            ``break``;
   36`                 `p = e;
   37`             `}
   38`         `}
   39
   40`         `if` `(e != ``null``) { ``// existing mapping for key
   41`             `V oldValue = e.value;
   42`             `if` `(!onlyIfAbsent || oldValue == ``null``)
   43`                 `e.value = value;
   44`             `afterNodeAccess(e);
   45`             `return` `oldValue;
   46`         `}
   47`     `}
   ```

   ```
   48`     `++modCount;
   49`     `// 步骤⑥：超过最大容量 就扩容
   50`     `if` `(++size > threshold)
   51`         `resize();
   52`     `afterNodeInsertion(evict);
   53`     `return` `null``;
   54` `}
   
   ```

3. 扩容 

   扩容不会保证之前顺序,jdk1.8 之后的优化会将一个链表的冲突拆分开导不同的数组元素上  2倍扩容, 创建新的数组  赋值就数据

   ```
   final` `Node<K,V>[] resize() {
    ``2`     `Node<K,V>[] oldTab = table;
    ``3`     `int` `oldCap = (oldTab == ``null``) ? ``0` `: oldTab.length;
    ``4`     `int` `oldThr = threshold;
    ``5`     `int` `newCap, newThr = ``0``;
    ``6`     `if` `(oldCap > ``0``) {
    ``7`         `// 超过最大值就不再扩充了，就只好随你碰撞去吧
    ``8`         `if` `(oldCap >= MAXIMUM_CAPACITY) {
    ``9`             `threshold = Integer.MAX_VALUE;
   10`             `return` `oldTab;
   11`         `}
   12`         `// 没超过最大值，就扩充为原来的2倍
   13`         `else` `if` `((newCap = oldCap << ``1``) < MAXIMUM_CAPACITY &&
   14`                  `oldCap >= DEFAULT_INITIAL_CAPACITY)
   15`             `newThr = oldThr << ``1``; ``// double threshold
   16`     `}
   17`     `else` `if` `(oldThr > ``0``) ``// initial capacity was placed in threshold
   18`         `newCap = oldThr;
   19`     `else` `{               ``// zero initial threshold signifies using defaults
   20`         `newCap = DEFAULT_INITIAL_CAPACITY;
   21`         `newThr = (``int``)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
   22`     `}
   23`     `// 计算新的resize上限
   24`     `if` `(newThr == ``0``) {
   25
   26`         `float` `ft = (``float``)newCap * loadFactor;
   27`         `newThr = (newCap < MAXIMUM_CAPACITY && ft < (``float``)MAXIMUM_CAPACITY ?
   28`                   `(``int``)ft : Integer.MAX_VALUE);
   29`     `}
   30`     `threshold = newThr;
   31`     `@SuppressWarnings``({``"rawtypes"``，``"unchecked"``})
   32`         `Node<K,V>[] newTab = (Node<K,V>[])``new` `Node[newCap];
   33`     `table = newTab;
   34`     `if` `(oldTab != ``null``) {
   35`         `// 把每个bucket都移动到新的buckets中
   36`         `for` `(``int` `j = ``0``; j < oldCap; ++j) {
   37`             `Node<K,V> e;
   38`             `if` `((e = oldTab[j]) != ``null``) {
   39`                 `oldTab[j] = ``null``;
   40`                 `if` `(e.next == ``null``)
   41`                     `newTab[e.hash & (newCap - ``1``)] = e;
   42`                 `else` `if` `(e ``instanceof` `TreeNode)
   43`                     `((TreeNode<K,V>)e).split(``this``, newTab, j, oldCap);
   44`                 `else` `{ ``// 链表优化重hash的代码块
   45`                     `Node<K,V> loHead = ``null``, loTail = ``null``;
   46`                     `Node<K,V> hiHead = ``null``, hiTail = ``null``;
   47`                     `Node<K,V> next;
   48`                     `do` `{
   49`                         `next = e.next;
   50`                         `// 原索引
   51`                         `if` `((e.hash & oldCap) == ``0``) {
   52`                             `if` `(loTail == ``null``)
   53`                                 `loHead = e;
   54`                             `else
   55`                                 `loTail.next = e;
   56`                             `loTail = e;
   57`                         `}
   58`                         `// 原索引+oldCap
   59`                         `else` `{
   60`                             `if` `(hiTail == ``null``)
   61`                                 `hiHead = e;
   62`                             `else
   63`                                 `hiTail.next = e;
   64`                             `hiTail = e;
   65`                         `}
   66`                     `} ``while` `((e = next) != ``null``);
   67`                     `// 原索引放到bucket里
   68`                     `if` `(loTail != ``null``) {
   69`                         `loTail.next = ``null``;
   70`                         `newTab[j] = loHead;
   71`                     `}
   72`                     `// 原索引+oldCap放到bucket里
   73`                     `if` `(hiTail != ``null``) {
   74`                         `hiTail.next = ``null``;
   75`                         `newTab[j + oldCap] = hiHead;
   76`                     `}
   77`                 `}
   78`             `}
   79`         `}
   80`     `}
   81`     `return` `newTab;
   82` `}
   
   ```

4. 线程安全

   在扩容时 会使链表重组, node的next指向会有变动 出现死循环

5. get方法

   计算key的hash值 重table中查找如果有就next

   循环比较链表上或红黑树上的node hash值  查找到直接返回

   ```
   final Node<K,V> getNode(int hash, Object key) {
       Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
       if ((tab = table) != null && (n = tab.length) > 0 &&
           (first = tab[(n - 1) & hash]) != null) {
           if (first.hash == hash && // always check first node
               ((k = first.key) == key || (key != null && key.equals(k))))
               return first;
           if ((e = first.next) != null) {
               if (first instanceof TreeNode)
                   return ((TreeNode<K,V>)first).getTreeNode(hash, key);
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

## LinkedList 

是双向链表的形式,内部类 node 会记录next 和pre  指向  

```
/**
 * Links e as last element.
 */
void linkLast(E e) {

//上一个数据
    final Node<E> l = last;
    //创建新的并且 将l  指向 新node
    final Node<E> newNode = new Node<>(l, e, null);
    last = newNode;
    if (l == null)
        first = newNode;
    else
        l.next = newNode;
        
        //数量和修改数量加1
    size++;
    modCount++;
}
```

get获取数据   会循环索引  去除next  直到正确位置

```
/**
 * Returns the (non-null) Node at the specified element index.
 */
Node<E> node(int index) {
    // assert isElementIndex(index);

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
```



## ConcurrentHashMap

安全的HashMap  区别于  HashTable  内部直接加锁效率比较低   jdk1.7   这个 map 内部使用了segment  方式存储  一个segement 类似于一个hashTable  细化了锁的范围,   jdk1.8  是 数组 加链表红黑树 

```
/** Implementation for put and putIfAbsent */
final V putVal(K key, V value, boolean onlyIfAbsent) {
    if (key == null || value == null) throw new NullPointerException();
    int hash = spread(key.hashCode());
    int binCount = 0;
    for (Node<K,V>[] tab = table;;) {
        Node<K,V> f; int n, i, fh;
        //tab是null  则创建 tab
        if (tab == null || (n = tab.length) == 0)
            tab = initTable();
            //查看如果tab中没有node  直接创建
        else if ((f = tabAt(tab, i = (n - 1) & hash)) == null) {
            if (casTabAt(tab, i, null,
                         new Node<K,V>(hash, key, value, null)))
                break;                   // no lock when adding to empty bin
        }
        //如果正在扩容,得到新的 tab 返回
        else if ((fh = f.hash) == MOVED)
            tab = helpTransfer(tab, f);
        else {
        //追加节点
            V oldVal = null;
            synchronized (f) {
            //链表
                if (tabAt(tab, i) == f) {
                    if (fh >= 0) {
                        binCount = 1;
                        for (Node<K,V> e = f;; ++binCount) {
                            K ek;
                            if (e.hash == hash &&
                                ((ek = e.key) == key ||
                                 (ek != null && key.equals(ek)))) {
                                oldVal = e.val;
                                if (!onlyIfAbsent)
                                    e.val = value;
                                break;
                            }
                            Node<K,V> pred = e;
                            if ((e = e.next) == null) {
                                pred.next = new Node<K,V>(hash, key,
                                                          value, null);
                                break;
                            }
                        }
                    }
                    //红黑树
                    else if (f instanceof TreeBin) {
                        Node<K,V> p;
                        binCount = 2;
                        if ((p = ((TreeBin<K,V>)f).putTreeVal(hash, key,
                                                       value)) != null) {
                            oldVal = p.val;
                            if (!onlyIfAbsent)
                                p.val = value;
                        }
                    }
                    else if (f instanceof ReservationNode)
                        throw new IllegalStateException("Recursive update");
                }
            }
            if (binCount != 0) {
            //链表转红黑树
                if (binCount >= TREEIFY_THRESHOLD)
                    treeifyBin(tab, i);
                if (oldVal != null)
                    return oldVal;
                break;
            }
        }
    }
    addCount(1L, binCount);
    return null;
}
```

get方法

    public V get(Object key) {
        Node<K,V>[] tab; Node<K,V> e, p; int n, eh; K ek;
        int h = spread(key.hashCode());// 定位到table[]中的i
        if ((tab = table) != null && (n = tab.length) > 0 &&
            (e = tabAt(tab, (n - 1) & h)) != null) {// 若table[i]存在
            if ((eh = e.hash) == h) {// 比较链表头部
                if ((ek = e.key) == key || (ek != null && key.equals(ek)))
                    return e.val;
            }
            else if (eh < 0)// 若为红黑树，查找树
                return (p = e.find(h, key)) != null ? p.val : null;
            while ((e = e.next) != null) {// 循环链表查找
                if (e.hash == h &&
                    ((ek = e.key) == key || (ek != null && key.equals(ek))))
                    return e.val;
            }
        }
        return null;// 未找到
    }
## ArrayList

数组形式 自动扩容  线程不安全   可以使用Collections.synchronizedArrayList(new ArrayList())  或者使用  CopyOnWriteArrayList() 

初始化构造时会初始化一个空的数组  ,在add的时候给数组设置大小  ,并且判断是否需要扩容(1.5*  +1),然后将数据赋值到数组当中去  ,每次索引添加元素会调用 system.arrayCopy  之后的元素  效率比较慢    所以 数组增删慢 查询快. 

## TreeMap  TreeSet

红黑树 保证自然顺序  值不接重复

## 排序

1. 选择排序  

   `//选择排序
       public void selectSort(int[] array) {
           int min;
           int tmp = 0;
           for (int i = 0; i < array.length; i++) {
               min = array[i];
               for (int j = i; j < array.length; j++) {
                   if (array[j] < min) {
                       min = array[j];//最小值
                       tmp = array[i];
                       array[i] = min;
                       array[j] = tmp;
                   }
               }
           }
       }`

2. 冒泡排序

   `//冒泡
       private void pubbleSort(int[] numbers) {
           int temp;//记录临时变量
           int size = numbers.length;//数组大小
           for (int i = 0; i < size - 1; i++) {
               for (int j = i + 1; j < size; j++) {//索引不同的两层for循环
                   if (numbers[i] < numbers[j]) {//交互数据从大到小排列顺序  大的放前面
                       temp = numbers[i];
                       numbers[i] = numbers[j];
                       numbers[j] = temp;
                   }
               }
           }
       }`

3. 插入排序

   `//直接插入
       private void InsertSort(int[] a) {
           long t1 = System.nanoTime();
           //直接插入排序
           for (int i = 1; i < a.length; i++) {
               //待插入元素
               int temp = a[i];
               int j;
               for (j = i - 1; j >= 0; j--) {
                   //将大于temp的往后移动一位
                   if (a[j] > temp) {
                       a[j + 1] = a[j];
                   } else {
                       break;
                   }
               }
               a[j + 1] = temp;//插入进来
           }
       }`

4. 二分查找

   `
       public static int binSearch(int srcArray[], int key) {
           int mid;
           int start = 0;
           int end = srcArray.length - 1;
           while (start <= end) {
               mid = (end - start) / 2 + start;
               if (key < srcArray[mid]) {
      end = mid - 1;
               } else if (key > srcArray[mid]) {
      start = mid + 1;
               } else {
      return mid;
               }
           }
           return -1;
       }`






---
title: HashMap源码解析
date: 2020-05-18 23:45:42
categories:
- 源码阅读
tags: Collection
---

<!-- more -->

![img](https://gitee.com/inag0000/image-bed/raw/master/blog-image/1200.png)

# 常量介绍

`default_initial_capacity`:默认初始容量，1<<4,即16。

> 根据上图可以看出，hashMap由最上层是一个数组，数组中每一个元素是一个链表或者一棵红黑树。链表的每一个节点或者红黑树的节点才是我们put进去的元素，即hashmap的容量=所有被占用的数组位下的链表的和

`default_load_factory`:默认加载因子。当哈希表条目数量超过加载因子与容量的乘积，哈希表容量扩大为原来的2倍，且内部条目被重新映射。加载因子过大会导致链表过长，查询时间成本增加；过小会导致频繁扩容。默认是0.75

`threshold`：扩容阀值。值是加载因子与容量的乘积。

`TREEIFY_THRESHOLD`:树化阀值。数组元素最初是构建成链表的结构，当一个链表的长度超过了`TREEIFY_THRESHOLD`后，hashmap会将这个链表转化为一个红黑树，默认是8

`UNTREEIFY_THRESHOLD`:非树化结构，在hashmap一个已经转化的红黑树上，如果因为remove等操作导致树的节点数量小于`UNTREEIFY_THRESHOLD`，那么这个红黑树将会被退化为链表。默认是6.

`MIN_TREEIFY_CAPACITY`：hashmap树化的最小容量。hashmap将链表转化为红黑树，还需要满足容量大于`MIN_TREEIFY_CAPACITY`。默认是64。

# 数据结构

## interface Entry<K,V>

数组元素Node都是此接口的实现类，从上图可以看出，hashMap实际上是个Node的数组，数组的每一个元素要么是个链表，要么是个树，因此node也可以理解为进入数组元素中的链表或者树的入口

## Node<K,V>[] table

hashmap主要是通过维护这样一个Node的数组来实现map接口的各个方法。

table只有在第一次使用时才会被初始化，大小永远是2次幂。

## Node<K,V> implements Map.Entry<K，V>

hashmap最初构造的数组元素结构

```java
static class Node<K,V> implements Map.Entry<K,V> {
    final int hash;
    final K key;
    V value;
    Node<K,V> next;
    ……
}
```

## TreeNode<K,V> extends LinkedHashMap.Entry<K,V>

```java
static final class TreeNode<K,V> extends LinkedHashMap.Entry<K,V> {
    TreeNode<K,V> parent;  // red-black tree links
    TreeNode<K,V> left;
    TreeNode<K,V> right;
    TreeNode<K,V> prev;    // needed to unlink next upon deletion
    boolean red;
    ……
}
```

LinkedHashMap.Entry<K,V> extends Map.Entry<K，V>

# 构造方法

```java

public HashMap(int initialCapacity, float loadFactor) {
    // 指定期望初始容量小于0将会抛出非法参数异常
   if (initialCapacity < 0)
       throw new IllegalArgumentException("Illegal initial capacity: " +
                                          initialCapacity);
   // 期望初始容量不可以大于最大值 2^30  实际上我们也不会用到这么大的容量                                      
   if (initialCapacity > MAXIMUM_CAPACITY)
       initialCapacity = MAXIMUM_CAPACITY;
  // 加载因子必须大于0 不能为无穷大   
   if (loadFactor <= 0 || Float.isNaN(loadFactor))
       throw new IllegalArgumentException("Illegal load factor: " +
                                          loadFactor);
   this.loadFactor = loadFactor;//初始化全局加载因子变量
   this.threshold = tableSizeFor(initialCapacity);//根据初始容量计算计算扩容阈值
}
```

tableSizeFor方法的作用是将用户输入的初始大小变为2次幂

```java
static final int tableSizeFor(int cap) {
    int n = cap - 1;
    n |= n >>> 1;
    n |= n >>> 2;
    n |= n >>> 4;
    n |= n >>> 8;
    n |= n >>> 16;
    return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
}
```

要将cap变为2次幂，从二进制来看，只需把最高位的1底下的所有位都变为1，然后再加1，即可得到大于cap且与cap最接近的2次幂。

例如当cap=18时，二进制是10010。那么我们的目标是得到100000，即32。

因为cap>0,即cap的二进制必定存在一位是1，我们只需要找到最左边的1。

右移1位，10010|01001 = 11011

此时原最高位的1及其右边一位也变为了1

右移2位，11011|00110=11111

此时原最高位1及其右边三位都变为1，如此即可将原来出现的最高位1右边的数字都变为1.

最后再加上1=100000,即为2次幂.

最初的cap-1，避免了当cap原来就是2次幂，导致最后根据上面的方法算出2*cap。即当cap是2次幂时，把最高位1右移一位。

# hash方法

```java
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

> 1、为什么要无符号右移16位
>
> 将h无符号右移16为相当于将高区16位移动到了低区的16位，再与原hashcode做异或运算，**可以将高低位二进制特征混合起来**
>
> 混合的目的是后续做与运算计算槽位的时候，避免丢失高位区特征。
>
> 2、使用异或运算的原因
>
> 异或运算能更好的保留各部分的特征
>
> 采用&运算计算出来的值会向0靠拢
>
> 采用|运算计算出来的值会向1靠拢

## 将计算的hash值映射到数组

`````java
int index = (n - 1) & root.hash
`````

n是table数组的大小

>1、为什么table数组的大小要是2次幂
>
>`````X % 2^n = X & (2^n - 1)`````,一个数对2的n次方取模等于这个数与（2的n次方-1）做与运算
>
>(**当 b = 2^n 时，a % b = a & (b - 1)** )
>
>计算哈希值的在数组下标的映射，可以理解为对table数组长度取模，而只有当长度是2次幂，才能是上面的等式成立。应为与运算的效率远高于取模运算，所以table数组的长度就需要保持2次幂

## 常见的哈希算法

### 直接定址法

直接以关键字k或者k加上某个常数（k+c）作为哈希地址（H(k)=ak+b）。

### 数字分析法

提取关键字中取值比较均匀的数字作为哈希地址（如一组出生日期，相较于年-月，月-日的差别要大得多，可以降低冲突概率）

 ### 分段叠加法

按照哈希表地址位数将关键字分成位数相等的几部分，其中最后一部分可以比较短。然后将这几部分相加，舍弃最高进位后的结果就是该关键字的哈希地址。

### 平方取中法

如果关键字各个部分分布都不均匀的话，可以先求出它的平方值，然后按照需求取中间的几位作为哈希地址。

 ### 伪随机数法

选择一随机函数，取关键字的随机值作为散列地址，通常用于关键字长度不同的场合。

 ### 除留余数法

用关键字k除以某个不大于哈希表长度m的数p，将所得余数作为哈希表地址（H(k)=k%p, p<=m; p一般取m或素数）。

## 常用解决hash冲突方法

### 链地址法

将哈希表的每个单元作为链表的头结点，所有哈希地址为 i 的元素构成一个同义词链表。即发生冲突时就把该关键字链在以该单元为头结点的链表的尾部。

### 开放定址法

即发生冲突时，去寻找下一个空的哈希地址。只要哈希表足够大，总能找到空的哈希地址。

###  再哈希法

即发生冲突时，由其他的函数再计算一次哈希值。

###  建立公共溢出区

将哈希表分为基本表和溢出表，发生冲突时，将冲突的元素放入溢出表。

# resize()

初始化或者将table数组扩容2倍。

```java
final Node<K,V>[] resize() {
    Node<K,V>[] oldTab = table;
    int oldCap = (oldTab == null) ? 0 : oldTab.length;
    int oldThr = threshold;
    int newCap, newThr = 0;
    /*********************计算新table的容量******************************************/
    if (oldCap > 0) {
        // 如果当前容量大于允许的最大容量，将容量阀值设置成最大，直接返回原table，不在进行扩容
        if (oldCap >= MAXIMUM_CAPACITY) {
            threshold = Integer.MAX_VALUE;
            return oldTab;
        }
        else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                 oldCap >= DEFAULT_INITIAL_CAPACITY)
            newThr = oldThr << 1; // double threshold
    }
    else if (oldThr > 0) // initial capacity was placed in threshold
        newCap = oldThr;
    else {               // zero initial threshold signifies using defaults
        newCap = DEFAULT_INITIAL_CAPACITY;
        newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
    }
    if (newThr == 0) {
        float ft = (float)newCap * loadFactor;
        newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                  (int)ft : Integer.MAX_VALUE);
    }
    threshold = newThr;
    /********************将旧table的元素转移到新table***************************/
    @SuppressWarnings({"rawtypes","unchecked"})
        Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
    table = newTab;
    if (oldTab != null) {
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

方法分两部分功能，

1、计算新的容量

2、将原table的元素一致扩充后的table

## 将原table的元素移动到扩充后的table

### 红黑树的移动

### 链表的移动

对原链表上的元素进行分组

`````(e.hash & oldCap) == 0`````的元素分为一组，组成由loHead和loTail指向头尾节点的新链表，放原位置

其他元素分为一组，组成由hiHead和hiTail指向头尾节点的新链表，放原位置+oldCap

该分组方法与重新计算每个元素在对新table长度取模的方法等价。

> `````(e.hash & oldCap) == 0`````因为oldCap是2次幂，即此时1处在扩容位上，与hash值此位的值做与运算，0位置不变，1位置翻倍

# 线程不安全原因
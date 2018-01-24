---
title: 浅谈JAVA中HashMap的实现
tags: [源码解析]
categories: "数据结构"
author: yip6
---

#### 什么是Map ####
先来看一下JAVA源码中对于Map的定义
````java
/*
* An object that maps keys to values.  A map cannot contain duplicate keys;
* each key can map to at most one value.
*/
````
Map是一个将唯一`Key`映射到`Value`的对象
#### HashMap的实现 ####
日常工作中经常使用，这一行代码一定十分熟悉
````
Map<String,Object> result = new HashMap();
````
在`HashMap`的代码中可以看到一个名为`Node`的内部类
````java
static class Node<K,V> implements Map.Entry<K,V> {
    final int hash;
    final K key;
    V value;
    Node<K,V> next;
    
    /* 省略getter setter constructor */
}
````
不禁让人联想到`LinkedList`的结构	。从我们最经常使用的`put(K,V)`着手，看看在添加一个映射的时候发生了什么。
````java
/**
 * The table, initialized on first use, and resized as
 * necessary. When allocated, length is always a power of two.
 * (We also tolerate length zero in some operations to allow
 * bootstrapping mechanics that are currently not needed.)
 */
transient Node<K,V>[] table;
````

这一个用来装载`Node`的数组，具体如何操作这个数组，继续看下面的操作。

````java
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
               boolean evict) {
    Node<K,V>[] tab; Node<K,V> p; int n, i;
    if ((tab = table) == null || (n = tab.length) == 0)
        //初始化table数组
        n = (tab = resize()).length;
        //计算table的最后一个index & hash的值，判定该node放进哪一个bin中    
    if ((p = tab[i = (n - 1) & hash]) == null)
        tab[i] = newNode(hash, key, value, null);    
    else {
        Node<K,V> e; K k;
        if (p.hash == hash &&
            ((k = p.key) == key || (key != null && key.equals(k))))
            e = p;
        else if (p instanceof TreeNode)
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
        else {
        		//遍历bin中的linkedList，将该元素追加至最后
            for (int binCount = 0; ; ++binCount) {
                if ((e = p.next) == null) {
                    p.next = newNode(hash, key, value, null);
                    if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                        treeifyBin(tab, hash);
                    break;
                }
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    break;
                p = e;
            }
        }
        if (e != null) { // existing mapping for key
            V oldValue = e.value;
            if (!onlyIfAbsent || oldValue == null)
                e.value = value;
            afterNodeAccess(e);
            return oldValue;
        }
    }
    ++modCount;
    if (++size > threshold)
        resize();
    afterNodeInsertion(evict);
    return null;
}

````
如果`table`未初始化，则先给定其长度。添加一个映射对的时候，会先根据`Key`的`Hash值`与`table`的最后一个索引值做`按位与`运算，决定改映射对添加到数组的哪个位置。如果该位置为空，那么直接放入即可，如果该位置不为空，那么就遍历这条`LinkedList`，将这个元素追加到最后。如果出现重复的`Key`则覆盖`Node`的`Value`。

![hash map](https://wx3.sinaimg.cn/mw690/b17c16cbly1fns2sqb302j21kw0y24qq.jpg)

#### 延伸 ####
细心的话会在代码中发现`TREEIFY_THRESHOLD`，这是一个阈值，值为8，代表着链长超出了该阈值，则出于效率考虑，会转换为`balanced tree`。这部分内容会在后续进行探讨。




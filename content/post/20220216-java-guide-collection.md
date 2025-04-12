---
title: "Java Guide 阅读笔记（2）集合"
tags: ["java","java guide","note"]
# series: ["Java Guide 阅读笔记"]
# categories: ["Java"]
date: 2022-02-16
toc: true
draft: false
---

## Java 集合概览

### 说说 List, Set, Queue, Map 四者的区别？

list有序可重复集合，set无序不可重复集合，queue是队列，有序可重复，map是键值对

### 集合框架底层数据结构总结

#### List

- ArrayList：Object[]
- LinkedList：链表
- Vector：Object[]

#### Set

- HashSet：底层HashMap
- LinkedHashSet：LinkedHashMap
- TreeSet：底层红黑树

#### Queue

- PriorityQueue：Object[]
- ArrayQueue：Object[]

#### Map

- HashMap：数组+链表/红黑树
- LinkedHashMap：数组+链表/红黑树+双向链表
- TreeMap：红黑树
- Hashtable：数组+链表

## 如何选用集合?

是否重复，是否排序

## Collection 子接口之 List

### Arraylist 和 Vector 的区别?

ArrayList与Vector底层都是Object[]，Vector线程安全

### Arraylist 与 LinkedList 区别?

ArrayList是Object[]，LinkedList是链表

### 说一说 ArrayList 的扩容机制吧

默认数组容量10，扩容1.5倍左右

### HashMap扩容机制

初始容量16，扩容2倍

![HashMap](https://uploadfiles.nowcoder.com/images/20220224/4107856_1645688900795/18330EB2310CB83A25FA317E65ED60EB)

## Collection 子接口之 Set

### comparable 和 Comparator 的区别

- comparable：对象.compareTo(Object obj)，java.lang
- Comparator：compare(Object a, Object b)，java.util

#### Comparator 定制排序

```java
// 定制排序的用法
Collections.sort(arrayList, new Comparator<Integer>() {
    @Override
    public int compare(Integer o1, Integer o2) {
    return o2.compareTo(o1);
    }
});
```

#### 重写 compareTo 方法实现按年龄来排序

```java
public  class Person implements Comparable<Person> {
    ...
    @Override
    public int compareTo(Person o) {
        if (this.age > o.getAge()) {
            return 1;
        }
        if (this.age < o.getAge()) {
            return -1;
        }
        return 0;
    }
}
```

### 无序性和不可重复性的含义是什么

无序性不等于随机性，而是不按照数组索引的顺序添加

不可重复是指对象内容不相等，即调用equals()方法返回结果为false

### 比较 HashSet、LinkedHashSet 和 TreeSet 三者的异同

三者都线程不安全

HashSet底层哈希表，LinkedHashSet底层链表+哈希表，TreeSet底层红黑树，自然排序

## Collection 子接口之 Queue

### Queue 与 Deque 的区别

- Queue单端队列只能一头插入，一头取出
- Deque双端队列，两端都可插入取出

### ArrayDeque 与 LinkedList 的区别

都实现了Deque接口，都具有队列功能，ArrayDeque底层数组，LinkedList底层链表

### 说一说 PriorityQueue

是具有优先级的队列，优先级越大，越早出队列，线程不安全

------------------------------------------------------

## Map 接口

### HashMap 和 Hashtable 的区别

HashMap线程不安全，Hashtable线程安全，HashMap支持Null key，Hashtable不支持

### HashMap 和 HashSet 区别

HashSet底层是由HashMap实现的

### HashMap 和 TreeMap 区别

HashMap底层数组+链表或红黑树，TreeMap底层红黑树，且支持排序

### HashSet 如何检查重复

hashCode和equals方法

### HashMap 的底层实现

#### JDK1.8 之前

#### JDK1.8 之后

### HashMap 的长度为什么是 2 的幂次方

### HashMap 多线程操作导致死循环问题

### HashMap 有哪几种常见的遍历方式?

### ConcurrentHashMap 和 Hashtable 的区别

### ConcurrentHashMap 线程安全的具体实现方式/底层具体实现

#### JDK1.7（上面有示意图）

#### JDK1.8 （上面有示意图）

## Collections 工具类

### 排序操作

### 查找,替换操作

### 同步控制

### 集合判空

isEmpty

### 集合转 Map

Value为null时，会抛异常

### 集合遍历

不能在foreach循环里对元素进行add/remove操作，若需remove，则用Iterator，并发时，Iterator需加锁

### 集合去重

利用集合快速去重

### 集合转数组

toArray(T[] array)

### 数组转集合

Java8用Arrays.stream(myArray).collect(Collectors.toList());

Java9用List.of(myArray)

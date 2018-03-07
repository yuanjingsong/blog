---
title: Java中的ArrayList
date: 2018-03-07
---

#### Java中的ArrayList

-------
Java提供了一个动态处理输入不定数量参数的机制```ArrayList```，即我们并不知道用户可能会输入几个数据，而定义的一种动态的类似数组的列表。

```ArrayList```是一个采用了类型参数(type parameter)的泛型类(generic class)。基本语法如下

##### 定义

`````java
ArrayList <reference name> name = new ArrayList<>();
`````

```ArrayLis``` 只针对引用类型使用，不能直接定义基本类型的`ArrayList`。如果想要存放基本类型也应定义为基本类型对应的引用类型，如Integer, Float, Double。

##### 基本语法

`````java
ArrayList<E> name = new ArrayList <>(int initialCapacity)
name.size(); //返回数组列表中当前数量
name.trimToSize(); // 将数组列表的储存容量削减到当前数量
name.add(x);//向后拓展以为并赋为x
name.set(int index, E obj) //设置数组列表指定位置的元素值，覆盖原有的内容
name.get(int index) //访问指定位置的元素值
name.add(int index, E obj) //插入位置，位置之后的所有元素向后挪一位 
`````

#### 下面讨论如何将ArrayList 与array进行转换

对于我们定义的class类型，如果将`ArrayList`转换成数组类型可以直接进行转换

````java
ArrayList <> list = new ArrayList <>();
X[] array = new X [list.size()];
list.toArray(array);
````

但如果将array转换成`ArrayList`可以使用较为繁杂的for方法进行依次赋值，达到转换的目的，但此处给出一种叫简单的方法进行转换(默认数组元素已经初始化)

````java
ArrayList<X> list = new ArrayList<>(Arrays.asList(array));
````

一行代码即可转化。

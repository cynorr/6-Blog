title: Java中HashMap容量与内存解析
date: 2014-01-07
category: Java Pro
tag: HashMap原理
author: 张端风
---
##运行方式
首先建立默认容量大小的数组,然后对对象key哈希值做运算,映射到数组中的位置上.从key到数组位置分两步计算.第一步对key的hashcode计算得到hash,第二步将hash进一步运算得到数组位置.
```java
int hash = hash(key.hashCode());
static int hash(int h) {
    return useNewHash ? newHash(h) : oldHash(h);
    }

#newHash的只始终为false,其实上面的newHash的判断结果是oldHash.
private static final boolean useNewHash;
    static { useNewHash = false; }
```

##扩容方式
* 默认容量:16 (Hash最大能存储的尺寸,并不是实际数据大小)
* 最大容量:约5亿(1<<30)
* 默认加载因子:0.75
* 容量极限:容量x加载因子

因为Hash是一种散列,所以HashMap的并不能将容量完全应用,实际的容量极限与默认容量成比例,这个比例即加载因子.当put进去的对象数量达到容量极限时,HashMap自动进行扩容.扩容首先新建原用量2倍的数组,然后对所有元素重新Hash,重新Hash所有元素非常消耗CPU资源,并且会引起线程安全问题.所以新建HashMap时必须初始化容量.

##初始化方法
* 初始化函数:
```java
//构造函数1
    public HashMap(int initialCapacity, float loadFactor) {

        int capacity = 1;
        while (capacity < initialCapacity)
            capacity <<= 1;                                 //容量以2的倍数增加
  
        this.loadFactor = loadFactor;
        threshold = (int)(capacity * loadFactor); //阈值
        table = new Entry[capacity];
        init();
    }

//构造函数2
public HashMap(int initialCapacity) {
        this(initialCapacity, DEFAULT_LOAD_FACTOR);  //用默认的加载加载因子调用构造函数1,默认加载因子为.0.75
    }

```
* 例子:
假设对象实际大小为size,则初始化函数为:
```java
HashMap<...> hashmap = new HashMap<...>(size/0.75); //0.75为默认加载因子
```

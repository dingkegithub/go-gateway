>
>
>
>

<h2 id='1'> 1. Antispam </h2>

风控模块主要功能

    - 黑名单: 黑面对往往是通过olap计算得到，然后放入缓存，风控模块更新到本地


<h2 id='2'> 2. bloom filter </h2>


<br>Bloom filter 是一个数据结构，它可以用来判断某个元素是否在集合内，具有运行快速，内存占用小的特点。

Bloom Filter 是一个基于概率的数据结构：它只能告诉

<br><br><br><br>**`一个元素绝对不在集合内或可能在集合内`**

Bloom filter 的基础数据结构是一个 比特向量

```
+---+---+---+---+---+---+---+---+---+---+---+---+---+
|   |   |   |   |   |   |   |   |   |   |   |   |   |
+---+---+---+---+---+---+---+---+---+---+---+---+---+
| 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 |10 |11 |12 |
+---+---+---+---+---+---+---+---+---+---+---+---+---+
```

bloom 过滤器由三个概念

    - 哈希函数k: k 个哈希函数， Bloom filter 里的哈希函数需要是彼此独立且均匀分布。同时，它们也需要尽可能的快

    - 大小m: 也就是有多少位,Bloom filter 的一个优良特性就是可以修改过滤器的错误率。一个大的过滤器会拥有比一个小的过滤器更低的错误率

    ```
        # k: 哈希函数个数
        # m: bloom 过滤器的大小
        # n: 存储数据量大小
        错误率 = 1-e(-kn/m)(k)
    ```

    > 所以你只需要先确定可能插入的数据集的容量大小 n, 然后再调整 k 和 m 来为你的应用配置过滤器

Bloom filter 使用的哈希函数越多运行速度就会越慢。但是如果哈希函数过少，又会遇到误判率高的问题,

对于给定的 m 和 n ，有一个函数可以帮我们确定最优的 k 值: (m/n)ln(2)

通过以下的步骤来确定 Bloom filter 的大小:

    - 确定 n 的变动范围
    - 选定 m 的值
    - 计算 k 的最优值
    - 对于给定的n, m, and k计算错误率。如果这个错误率不能接收，那么回到第二步，否则结束


Bloom filter 的时间复杂度和空间复杂度

对于一个 m 和 k 值确定的 Bloom filter，插入和测试操作的时间复杂度都是 O(k)。这意味着每次你想要
插入一个元素或者查询一个元素是否在集合中，只需要使用 k 个哈希函数对这个元素求值，然后将对应的比
特位标记或者检查对应的比特位

Bloom filter 的空间复杂度更难以概述，它取决于你可以忍受的错误率。同时也取决于输入元素的范围，
如果这个范围是有限的，那么一个确定的比特向量就可以很好的解决问题。如果你甚至不能很好的估计输
入元素的范围，那么你最好选择一个哈希表或者一个可拓展的 Bloom filter
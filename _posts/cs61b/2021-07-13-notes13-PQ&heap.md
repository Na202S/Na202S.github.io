---
title: Berkeley CS61b：13. Priority Queues and Heaps
categories: [Courses, Berkeley CS61b]
tags: [data structures]
author: Na
math: true
img_path: /../assets/img/cs61b/note13
---

## 13.1 PQ Interface

### The Priority Queue Interface

(最小)优先队列：允许获取和移除队列中的最小项。在需要追踪”smallest”，“largest”，“best”时很有用。接口如下：添加项、获取最小项、删除最小项、队列大小。

```java
public interface MinPQ<Item> {
    public void add(Item x);
    public Item getSmallest();
    public Item removeSmallest();
    public int size();
}
```

### Using Priority

应用场景：每天追踪用户对话，报告 $M$ 条最有害的信息。

```java
public List<String> unharmoniousTexts(Sniffer sniffer, int M) {
	Comparator<String> cmptr = new HarmoniousnessComparator();
	/* 用PQ来存放嗅探到的信息 */
	MinPQ<String> unharmoniousTexts = new HeapMinPQ<Transaction>(cmptr);
	for (Timer timer = new Timer(); timer.hours() < 24; ) {
		unharmoniousTexts.add(sniffer.getNextMessage());
		/* 在24小时之内，一旦PQ存储的信息多于M条 */
		if (unharmoniousTexts.size() > M) {
			/* 就将PQ中最无害的信息删去 */
			unharmoniousTexts.removeSmallest();
		}
	}

	ArrayList<String> textlist = new ArrayList<String>();
	while (unharmoniousTexts.size() > 0) {
		/* 将PQ中的信息依次加入List中 */
		textlist.add(unharmoniousTexts.removeSmallest());
	}
	return textlist;
}
```

## 13.2 Heaps

### Heap Structure

BST 可以用来实现 PQ，但是将会很 bushy，而且不便处理优先级相同的项。取而代之，可以采用**二叉最小堆**（Binary min-Heap），即具有以下两个特性的二叉树：

- **min-heap**：每个节点都小于或等于它的左、右子节点。
- **complete**：只在最低层有缺失的节点，且最低一层的节点全都尽量靠左。

![heap](heap.png)

### Heap Operations

如何用二叉最小堆实现 PQ？

- add(x)：为保证**complete**的特性，自然地想到可以先将 `x` 放到最低层的最左边的空位；但这样并不能保证**min-heap**的特性，故需要进一步的调整。
  - swim：不断地比较 `x` 和它的 parent 大小，假如 `x` 更小，就和 parent 进行交换，直到 `x` 放置到合适的位置。

<img src="insert.png" alt="swim" style="zoom:150%;" />

- getSmallest：由**min-heap**的特性可知，返回根节点即可。

- removeSmallest：为保证**complete**的特性，自然地想到先将根节点和最末尾的节点进行交换，将换过位置以后的它删掉；但这样并不能保证**min-heap**的特性，故需要进一步的调整。
  - sink：不断地比较 `x` 和它的左、右节点大小，和二者中较小的节点进行交换，直到 `x` 放置到合适的位置。

<img src="remove.png" alt="sink" style="zoom:80%;" />

### Tree Representation

在 Java 中，如何表示树结构？

<img src="tree.png" alt="tree" style="zoom:50%;" />

#### Approach 1a, 1b, and 1c

建立节点到孩子节点的映射。1a：固定的 link 数；1b：用数组存放 link；1c：首孩子/sibling 的 link。

![ap1digrams](ap1digrams.png)

![ap1codes](ap1codes.png)

#### Approach 2

数组 `keys` 存放键值，表示每个索引 `i` 对应到哪个键值；再用数组 `parents` 存放 parent 的索引。即，1 个数组存放键值，1 个数组存放树的结构。

<img src="ap2-1.png" alt="ap2-1" style="zoom: 50%;" />

一个更复杂的例子：

![ap2-2](ap2-2.png)

`e` 为 `b` 的 parent，在 `keys` 数组中，和 `e` 对应的索引为**1**、和 `b` 对应的索引为**3**；在`parents` 数组中，将 `parents`[**3**] 设为 **1**。

#### Approach 3

在 Approach2 中观察到，当按照层次遍历的顺序给二叉树编号时，假如树具有**complete**的特性，parent 和 child 的编号是有规律可循的：编号为 `k` 的节点所对应的 parent 编号为`(k-1)/2`，即 `parent(k) == (k-1)/2`。

进一步地，假如将 0th 的位置空出，则有以下规律：

- `leftChild(k)` $= k*2$

- `rightChild(k)` $= k*2 + 1$

- `parent(k)` = $k/2$

![ap3](ap3.png)

因此，只需要一个 `keys` 数组存放键值即可，无需另一个数组存放树的结构。

## 13.3 Implementation Consideration

### The Implementation

- You may find the [Princeton implementation of a heap](http://algs4.cs.princeton.edu/24pq/MinPQ.java.html) useful. [Here](https://algs4.cs.princeton.edu/24pq/) is a more detailed explanation of each of the methods.

- My implementation based on Sp18 lab10 is [Here](https://github.com/nsang202/skeleton-sp18/blob/master/lab10/ArrayHeap.java).
- A record of Sp18 lab10 will be released soon!

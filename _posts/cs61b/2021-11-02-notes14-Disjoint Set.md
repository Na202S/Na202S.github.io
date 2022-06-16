---
title: Berkeley CS61b：14. Disjoint Sets

categories: [Courses, Berkeley CS61b]

tags: [data structures]

author: Na

math: true

img_path: /../assets/img/cs61b/notes14 disjoint sets
---

## The Disjoint Sets Interface

```java
public interface DisjointSets {
	/** Connects two items P and Q. */
	void connect(int p, int q);

	/** Checks to see if two items are connected. */
	boolean isConnected(int p, int q);
}
```

## Representation

**connect(5, 2)**

![set representation](set representation.png)

给每个节点分配一个 parent，用树的形式表示。

- Find(x): 返回 x 的根节点，可以用 path compression 优化；（一般都优化）
- Union(x, y): 将 x 和 y 所属的树合并为一棵，方法是让 Find(x) 成为 Find(y) 的孩子。可以用 rank 优化，即总是让元素个数少的树连接到元素个数多的树下面。（一般不用优化）

两个优化都是为了让树的高度尽可能低。

### Find

- The “disjoint set” mainly uses the `find` function to **find the root node of a given vertex**.

#### Path Compression

Draw the tree after we call

**isConnected(14, 13)**

![path compression1](path compression1.png)

在寻找根节点的时候，将一路上经过的节点都连接到最终的根节点之后，使得它们成为根节点的孩子。

![path compression2](path compression2.png)

![path compression3](path compression3.png)

### Union

- The “disjoint set” mainly uses the `union` function to **connect two vertices, `x`, and `y`, by equating their root node**.

#### Basic Union

![basic union](basic union.png)

**connect(5, 2)**

- Find root(5). // returns 3

- Find root(2). // returns 0

- Set root(5)’s value equal to root(2).

通过 find 找到根节点，union 的时候，将 p 的根节点连接到 q 的根节点之后，使得 root(p) 成为 root(q) 的孩子。

![basic union2](basic union2.png)

#### Union by Rank (**Weighted Quick Union**)

Modify quick-union to avoid tall trees.

- Track tree size (**number** of elements).

- New rule: Always link root of **smaller** tree **to** **larger** tree.

**connect(3, 8)**

![wqu1](wqu1.png)

![wqu2](wqu2.png)

为什么用元素的个数，而不是树的高度？

因为最坏的情况下都是 $ Θ(\log(N)) $，而使用高度会使得代码更复杂。

## Code

### Implementation of the “disjoint set”

```python
class UnionFind:
    # Constructor of Union-find. The size is the length of the root array.
    def __init__(self, size):
    def find(self, x):
    def union(self, x, y):
    def connected(self, x, y):
```

- The `find` function – optimized with path compression:

  ```python
  def find(self, x):
      if self.parent[x] == x:
          return x
      self.parent[x] = self.find(self.parent[x])
      return self.parent[x]
  ```

- A basic implementation of the `union` function:

  ```python
  def union(self, x, y):
      rootX = self.find(x)
      rootY = self.find(y)
      if rootX != rootY:
          self.parent[rootY] = rootX
  ```

- The `union` function – Optimized by union by rank: (一般不用)

  ```python
  def union(self, x, y):
      rootX = self.find(x)
      rootY = self.find(y)
      if rootX != rootY:
          if self.rank[rootY] < self.rank[rootX]:
              self.parent[rootY] = rootX
          elif self.rank[rootX] < self.rank[rootY]:
              self.parent[rootX] = rootY
          else:
              self.parent[rootY] = rootX
              self.rank[rootX] += 1
  ```

- The connected function checks if two vertices, `x` and `y`, are connected

  ```python
  def connected(self, x, y):
      return self.find(x) == self.find(y)
  ```

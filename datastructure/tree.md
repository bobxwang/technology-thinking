#### 红黑树
> 红黑树是二叉树的一种，二叉树是一种有序的树形结构，优势在于查找，插入的时间复杂度只有O(log n),它的特性如下
> 
* 任意节点最多含有两个子节点
* 任意节点的左，右节点都可以看做一棵二叉树
* 如果任意节点的左子树不为空，那么左子树上的所有节点的值均小于它的根节点的值
* 如何任意节点的右子树不为空，那么右子树上的所有节点的值均大于它的根节点的值
* 任意节点的key都是不同的 

二叉树有可能会发生倾斜，此时可能就会退化成一个普通的链表，查找操作的时间复杂度变为O(n)，为了解决这个问题，所以引入自平衡二叉树，红黑树就是这样的一棵树。

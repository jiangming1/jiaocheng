###深度优先和广度优先
目录：
>1. 网站的树结构
2. 深度优先算法和实现
3. 广度优先算法和实现

网站url树结构:
分层设计
>子域名：
>- bogbole.com
    - blog.bogbole.com
    - python.bogbole.com
        - python.bogbole.com/123

环路链接：
>从首页到下面节点。
但是下面的链接节点又会有链接指向首页

所以：我们需要对于链接进行去重

**1. 深度优先**
**2. 广度优先**

>跳过已爬取的链接
对于二叉树的遍历问题

深度优先(递归实现)：
    顺着一条路，走到最深处。然后回头

广度优先(队列实现):
    分层遍历：遍历完儿子辈。然后遍历孙子辈

Python实现深度优先过程code：
```python
def depth_tree(tree_node):
    if tree_node is not None:
        print (tree_node._data)
        if tree_node._left is not None:
            return depth_tree(tree_node.left)
        if tree_node._right is not None:
            return depth_tree(tree_node,_right)
```
Python实现广度优先过程code：
```python
def level_queue(root):
    #利用队列实现树的广度优先遍历
    if root is None:
        return
    my_queue = []
    node = root
    my_queue.append(node)
    while my_queue:
        node = my_queue.pop(0)
        print (node.elem)
        if node.lchild is not None:
            my_queue.append(node.lchild)
        if node.rchild is not None:
            my_queue.append(node.rchild)
```

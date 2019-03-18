##在链表中穿针引线

链表和数组都是线性结构，但是链表和数组的不同在于数组可以随机的对于数据进行访问。给出索引。可以以O(1)的时间复杂度迅速访问到该元素。
链表只能从头指针开始。

next指针指向哪里？

**206. Reverse Linked List**

反转一个链表

![链表反转](http://upload-images.jianshu.io/upload_images/1779926-03961d5eae19960b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

不能改变链表值。操作每个节点的next指针。
5的指针指向空的。变成指向4的。

![next改变示意图](http://upload-images.jianshu.io/upload_images/1779926-c1e9e45b6e5b4edd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![把1指向NUll之前要保存next指针](http://upload-images.jianshu.io/upload_images/1779926-857a72edfdea5e20.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![添加pre指针保存上一个节点](http://upload-images.jianshu.io/upload_images/1779926-9e39d087d58a2e79.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```c
// 时间复杂度: O(n)
// 空间复杂度: O(1)
class Solution {
public:
    ListNode* reverseList(ListNode* head) {

        ListNode* pre = NULL;
        ListNode* cur = head;
        while( cur != NULL ){
            ListNode* next = cur->next;
            cur->next = pre;
            pre = cur;
            cur = next;
        }

        return pre;
    }
};
```
**92. Reverse Linked List II**

反转一个链表从m到n的元素。

- 如对于链表 1->2->3->4->5->NULL, m = 2 , n = 4
- 则返回链表 1->4->3->2->5->NULL
	- m和n超过链表范围怎么办？
	- m > n 怎么办？

翻转m到n的元素。

####测试链表程序。

写根据数组创建链表和打印链表两个函数

```c
/// LinkedList Test Helper Functions
ListNode* createLinkedList(int arr[], int n){

    if( n == 0 )
        return NULL;

    ListNode* head = new ListNode(arr[0]);
    ListNode* curNode = head;
    for( int i = 1 ; i < n ; i ++ ){
        curNode->next = new ListNode(arr[i]);
        curNode = curNode->next;
    }

    return head;
}

void printLinkedList(ListNode* head){

    ListNode* curNode = head;
    while( curNode != NULL ){
        cout << curNode->val << " -> ";
        curNode = curNode->next;
    }

    cout<<"NULL"<<endl;

    return;
}

void deleteLinkedList(ListNode* head){

    ListNode* curNode = head;
    while( curNode != NULL ){
        ListNode* delNode = curNode;
        curNode = curNode->next;
        delete delNode;
    }

    return;
}
```
main.cpp:

```c
int main(){

    int arr[] = {1, 2, 3, 4, 5};
    int n = sizeof(arr)/sizeof(int);

    ListNode* head = createLinkedList(arr, n);
    printLinkedList(head);

    ListNode* head2 = Solution().reverseList(head);
    printLinkedList(head2);

    deleteLinkedList(head2);

    return 0;
}
```
![运行结果](http://upload-images.jianshu.io/upload_images/1779926-2de78072ec51af0c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**83. Remove Duplicates from Sorted List**

给出一个有序链表，删除其中所有重复元素，使得每个元素只保留一次。

- 如 1->1->2，返回1->2
- 如 1->1->2->3->3，返回1->2->3

**86. Partition List**

给出一个链表以及一个数x，将链表重新整理，使得小于x的元素在前；大于等于x的元素在后。

- 如 1->4->3->2->5->2，x=3
- 返回 1->2->2->4->3->5

**328. Odd Even Linked List**

给出一个链表，将链表重新整理，使得所有索引为奇数的节点排在索引为偶数的节点前面。

- 如 1->2->3->4->5->NULL
- 返回 1->3->5->2->4->NULL
- 第一个节点的索引为1
- 奇数索引的节点和偶数索引的节点在重新整理后要保持相对顺序。

**2. Add Two Numbers**

给出两个非空链表，表示两个非负整数。其中每一个整数的各位数字以逆序存储，返回这两个整数相加所代表的链表。

- 如 342 + 465 = 807
- 则给出 2->4->3 和 5->6->4，返回7->0->8

>数字中是否有前置的0。（除0以外，没有前置0）
负数？

**445. Add Two Numbers II**

给出两个非空链表，表示两个非负整数。其中每一个整数的各位数字以顺序存储，返回这两个整数相加所代表的链表。

- 如 342 + 465 = 807
- 则给出 3->4->2 和 4->6->5，返回8->0->7

>如果不允许修改输入的链表呢？
使用辅助数据结构

####设立链表的虚拟头结点

**203. Remove Linked List Elements**

在链表中删除值为val的所有节点

- 如 1->2->6->3->4->5->6->NULL，要求删除值为6的节点
- 返回 1->2->3->4->5->NULL


![删除节点](http://upload-images.jianshu.io/upload_images/1779926-57b53f230e18adda.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

该逻辑对删除最后一个元素依然适用

该逻辑对删除第一个元素不适用

```c
// 不使用虚拟头结点
class Solution {
public:
    ListNode* removeElements(ListNode* head, int val) {

        while( head != NULL && head->val == val ){

            ListNode* node = head;
            head = head->next;
            delete node;
        }

        if( head == NULL )
            return head;

        ListNode* cur = head;
        while( cur->next != NULL ){

            if( cur->next->val == val ){
                ListNode* delNode = cur->next;
                cur->next = delNode->next;
                delete delNode;
                //delNode -> next = NULL;
            }
            else
                cur = cur->next;
        }

        return head;
    }
};
```

![虚拟头结点](http://upload-images.jianshu.io/upload_images/1779926-093e65d68a174ec5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

具体实现：

```c
// 使用虚拟头结点
class Solution {
public:
    ListNode* removeElements(ListNode* head, int val) {

        ListNode* dummyHead = new ListNode(0);
        dummyHead->next = head;

        ListNode* cur = dummyHead;
        while( cur->next != NULL ){

            if( cur->next->val == val ){
                ListNode* delNode = cur->next;
                cur->next = delNode->next;
                delete delNode;
            }
            else
                cur = cur->next;
        }

        ListNode* retNode = dummyHead->next;
        delete dummyHead;

        return retNode;
    }
};

int main() {

    int arr[] = {1, 2, 6, 3, 4, 5, 6};
    int n = sizeof(arr)/sizeof(int);

    ListNode* head = createLinkedList(arr, n);
    printLinkedList(head);

    Solution().removeElements( head, 6);
    printLinkedList(head);

    deleteLinkedList( head );

    return 0;
}
```

**82. Remove Duplicates from Sorted List II**

给定一个有序链表，将其中有重复的元素全部删除。

- 如1->2->3->3->4->4->5，返回1->2->5
- 如1->1->1->2->3，返回2->3

**21. Merge Two Sorted Lists**

merge两个有序的链表

####复杂的链表操作

**24. Swap Nodes in Pairs**

给定一个链表，对于每两个相邻的节点，交换其位置。

- 如：链表为 1->2->3->4->NULL
- 返回：2->1->4->3->NULL
- 只能对节点进行操作，不能修改节点的值

![虚拟头结点](http://upload-images.jianshu.io/upload_images/1779926-df328d56f4226af1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![虚拟头结点](http://upload-images.jianshu.io/upload_images/1779926-df1055189d35a33c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![next指针调整](http://upload-images.jianshu.io/upload_images/1779926-b41514357f6150da.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

核心在于创建几个指针预先保留。

```c
class Solution {
public:
    ListNode* swapPairs(ListNode* head) {

        ListNode* dummyHead = new ListNode(0);
        dummyHead->next = head;

        ListNode* p = dummyHead;
        while( p->next && p->next->next ){
            ListNode* node1 = p->next;
            ListNode* node2 = node1->next;
            ListNode* next = node2->next;
            node2->next = node1;
            node1->next = next;
            p->next = node2;
            p = node1;
        }

        ListNode* retHead = dummyHead->next;
        delete dummyHead;

        return retHead;
    }
};

int main() {

    int arr[] = {1, 2, 3, 4};
    int n = sizeof(arr)/sizeof(int);

    ListNode* head = createLinkedList(arr, n);
    printLinkedList(head);

    head = Solution().swapPairs( head );
    printLinkedList(head);

    deleteLinkedList(head);

    return 0;
}
```
思考：可不可以不用next指针？

**25. Reverse Nodes in k-Group**

给定一个链表，每k个节点为一组，反转每一组的k个节点。k为正整数且小于等于链表长度。如果链表长度不是k的整数倍，剩余部分不需要进行反转。如： 1->2->3->4->5->NULL

- 若 k = 2，则结果为：2->1->4->3->5->NULL
- 若 k = 3，则结果为：3->2->1->4->5->NULL

**147. Insertion Sort List**

为一个链表进行插入排序

**148. Sort List**

写一个排序算法，用O(nlogn)的时间复杂度为一个链表进行排序

归并排序不需要使用数组索引：自底向上。

####不仅仅是穿针引线

**237. Delete Node in a Linked List**

给定链表中的一个节点，删除该节点

```c
class Solution {
public:
    void deleteNode(ListNode* node) {
        
    }
};

```

![3赋值上4，然后把4的next保存。让第一个4指向第二个四的next](http://upload-images.jianshu.io/upload_images/1779926-35bb74aaabbb9dc7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>3赋值上4，然后把4的next保存。让第一个4指向第二个四的next

```c
class Solution {
public:
    void deleteNode(ListNode* node) {

        assert(node != NULL && node->next != NULL);

        node->val = node->next->val;
        ListNode* delNode = node->next;
        node->next = delNode->next;

        delete delNode;

        return;
    }
};
```

>特殊情况改变节点的值来实现我们需要的功能。

####双指针技术

**19. Remove Nth Node From End of List**

给定一个链表，删除倒数第n个节点

- 如：1->2->3->4->5->NULL, n = 2
- 返回：1->2->3->5

>n从0计还是从1计
n不合法，负数或者大于链表长度如何处理（保证n合法）

解法1：先遍历一遍计算链表长度；再遍历一遍删除倒数第n个节点
遍历两遍链表。能否只遍历一遍链表？

![pq长度固定](http://upload-images.jianshu.io/upload_images/1779926-a4306876391fa77f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

具体实现：

```c
// 先记录链表总长度
// 需要对链表进行两边遍历
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {

        ListNode* dummyHead = new ListNode(0);
        dummyHead->next = head;

        int length = 0;
        for(ListNode* cur = dummyHead->next ; cur != NULL ; cur = cur->next )
            length ++;
        //cout<<length<<endl;

        int k = length - n;
        assert( k >= 0 );
        ListNode* cur = dummyHead;
        for( int i = 0 ; i < k ; i ++ )
            cur = cur -> next;

        ListNode* delNode = cur->next;
        cur->next = delNode->next;
        delete delNode;

        ListNode* retNode = dummyHead->next;
        delete dummyHead;
        return retNode;
    }
};
```

使用双指针：

```c
// 使用双指针, 对链表只遍历了一遍
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {

        ListNode* dummyHead = new ListNode(0);
        dummyHead->next = head;

        ListNode* p = dummyHead;
        ListNode* q = dummyHead;
        for( int i = 0 ; i < n + 1 ; i ++ ){
            assert(q);
            q = q->next;
        }

        while( q ){
            p = p->next;
            q = q->next;
        }

        ListNode* delNode = p->next;
        p->next = delNode->next;
        delete delNode;

        ListNode* retNode = dummyHead->next;
        delete dummyHead;

        return retNode;
    }
};
```

**61. Rotate List**

给定一个链表，让这个链表向右旋转k位。其中k为非负数。

- 如：1->2->3->4->5->NULL, k = 2
- 第一次旋转：5->1->2->3->4->NULL
- 第二次旋转：4->5->1->2->3->NULL

**143. Reorder List**

给定一个链表 L(0) -> L(1) -> L(2) -> … -> L(n-1) -> L(n)
将其变为 L(0) -> L(n) -> L(1) -> L(n-1) -> L(2) -> L(n-2)…
的形式

- 链表无法随机访问数据，如何获得中间的元素？
- 两次遍历？一次遍历？

**234. Palindrome Linked List**

给一个链表，判断这个链表是否为回文（正看反看）链表。

- 能否使用O(1)的空间复杂度解决问题？

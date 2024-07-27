# 反转链表

[[题目](https://leetcode.cn/problems/reverse-linked-list/description/)](https://leetcode.cn/problems/reverse-linked-list/description/)：给你单链表的头节点 `head` ，请你反转链表，并返回反转后的链表。

```html
输入：head = [1,2,3,4,5]
输出：[5,4,3,2,1]
```

### 题解：双指针法

```cpp
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        ListNode* temp; // 保存cur的下一个节点
        ListNode* cur = head;
        ListNode* pre = NULL;
        while(cur) {
            temp = cur->next;  // 保存一下 cur的下一个节点，因为接下来要改变cur->next
            cur->next = pre; // 翻转操作
            // 更新pre 和 cur指针
            pre = cur;
            cur = temp;
        }
        return pre;
    }
};
```

**注意点：**

1.注意循环的条件为cur！第一次写的时候我写成了cur->next  != nullptr，最后陷入死循环

2.记得返回的是新的头结点pre

### 题解二：递归法

```cpp
class Solution {
public:
    ListNode* reverse(ListNode* pre,ListNode* cur){
        if(cur == NULL) return pre;
        ListNode* temp = cur->next;
        cur->next = pre;
        // 可以和双指针法的代码进行对比，如下递归的写法，其实就是做了这两步
        // pre = cur;
        // cur = temp;
        return reverse(cur,temp);
    }
    ListNode* reverseList(ListNode* head) {
        // 和双指针法初始化是一样的逻辑
        // ListNode* cur = head;
        // ListNode* pre = NULL;
        return reverse(NULL, head);
    }

};
```

# 两两交换链表中的节点

[[题目](https://leetcode.cn/problems/swap-nodes-in-pairs/description/)](https://leetcode.cn/problems/swap-nodes-in-pairs/description/)：给你一个链表，两两交换其中相邻的节点，并返回交换后链表的头节点。你必须在不修改节点内部的值的情况下完成本题（即，只能进行节点交换）。

### 题解：看图说话

![24 两两交换链表中的节点2](https://github.com/user-attachments/assets/728b84d1-2f4e-4f4e-a33c-08efa022d378)

```cpp
class Solution {
public:
    ListNode* swapPairs(ListNode* head) {
        ListNode* dummyHead = new ListNode(0); // 设置一个虚拟头结点
        dummyHead->next = head; // 将虚拟头结点指向head，这样方便后面做删除操作
        ListNode* cur = dummyHead;
        while(cur->next != nullptr && cur->next->next != nullptr) {
            ListNode* tmp = cur->next; // 记录临时节点
            ListNode* tmp1 = cur->next->next->next; // 记录临时节点

            cur->next = cur->next->next;    // 步骤一
            cur->next->next = tmp;          // 步骤二
            cur->next->next->next = tmp1;   // 步骤三

            cur = cur->next->next; // cur移动两位，准备下一轮交换
        }
        ListNode* result = dummyHead->next;
        delete dummyHead;
        return result;
    }
};
```

**难点与易错点：**

while循环条件为cur->next != nullptr && cur->next->next != nullptr是因为

​	**如果想对一个节点的后两个节点进行操作时，必须有个指针指向这个节点**；e.g. 3->2->1我想调换2和1的位置，则需要有个指针指向3

​	当该链表是奇数时，cur->next->next != nullptr结束，偶数时则cur->next != nullptr结束

​	**并且cur->next != nullptr && cur->next->next != nullptr顺序不能变**，如果是cur->next->next != nullptr && cur->next != nullptr并且该链表是偶数时，最后cur->next就已经是空指针，那么cur->next->next != nullptr就是对空指针的操作，会报错



# 删除链表的倒数第N个节点

[[题目](https://leetcode.cn/problems/remove-nth-node-from-end-of-list/description/)](https://leetcode.cn/problems/remove-nth-node-from-end-of-list/description/)：给你一个链表，删除链表的倒数第 `n` 个结点，并且返回链表的头结点。

双指针法流程图：[[见网页](https://www.programmercarl.com/0019.%E5%88%A0%E9%99%A4%E9%93%BE%E8%A1%A8%E7%9A%84%E5%80%92%E6%95%B0%E7%AC%ACN%E4%B8%AA%E8%8A%82%E7%82%B9.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)](https://www.programmercarl.com/0019.%E5%88%A0%E9%99%A4%E9%93%BE%E8%A1%A8%E7%9A%84%E5%80%92%E6%95%B0%E7%AC%ACN%E4%B8%AA%E8%8A%82%E7%82%B9.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)

**大致流程：fast指针先走n+1步，这个时候slow与fast永远相隔n+1，那么fast与slow同时向后移动，当fast到达nullptr，说明slow到达倒数n+1的位置（要删除的节点的前一个节点）**

### 题解：双指针法

```cpp
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        ListNode* dummyHead = new ListNode(0);
        dummyHead->next = head;
        ListNode* slow = dummyHead;
        ListNode* fast = dummyHead;
        while(n-- && fast != NULL) {
            fast = fast->next;
        }
        fast = fast->next; // fast再提前走一步，因为需要让slow指向删除节点的上一个节点
        while (fast != NULL) {
            fast = fast->next;
            slow = slow->next;
        }
        slow->next = slow->next->next; 
        
        // ListNode *tmp = slow->next;  C++释放内存的逻辑
        // slow->next = tmp->next;
        // delete tmp;
        
        return dummyHead->next;
    }
};
```

**易错点：用虚拟头节点后一定要返回的是dummyHead->next而不是直接删除dummyHead返回head**

感悟：虚拟头结点主要是**方便于处理对于头结点的操作**，如果一道题中如果没有对于链表头结点操作，则不需要虚拟头结点



# 环形链表II

[[题目](https://leetcode.cn/problems/linked-list-cycle-ii/description/)](https://leetcode.cn/problems/linked-list-cycle-ii/description/)：给定一个链表的头节点  `head` ，返回链表开始入环的第一个节点。 *如果链表无环，则返回 `null`。*

如果链表中有某个节点，可以通过连续跟踪 `next` 指针再次到达，则链表中存在环。 为了表示给定链表中的环，评测系统内部使用整数 `pos` 来表示链表尾连接到链表中的位置（**索引从 0 开始**）。如果 `pos` 是 `-1`，则在该链表中没有环。**注意：`pos` 不作为参数进行传递**，仅仅是为了标识链表的实际情况。**不允许修改** 链表。	

**难点：弄清楚为什么最后只需要两个指针（一个从head出发一个从相遇点出发）同时移动，这两个指针相遇点即入口点**

### 流程：

#### 1.判断链表是否有环

可以使用快慢指针法，分别定义 fast 和 slow 指针，从头结点出发，**fast指针每次移动两个节点**，**slow指针每次移动一个节点**，如果 fast 和 slow指针在途中相遇 ，说明这个链表有环。

动画如下：
![141 环形链表](https://github.com/user-attachments/assets/8a6fc65a-ba76-405b-ad05-0f453fafec6c)


#### 2.如果有环，如何找到这个环的入口

**此时已经可以判断链表是否有环了，那么接下来要找这个环的入口了。**

假设从头结点到环形入口节点 的节点数为x。 环形入口节点到 fast指针与slow指针相遇节点 节点数为y。 从相遇节点 再到环形入口节点节点数为 z。 如图所示：
<img width="742" alt="20220925103433" src="https://github.com/user-attachments/assets/d7d0e9b0-e18c-473e-85b6-72b6823a3f22">



那么相遇时： slow指针走过的节点数为: `x + y`， fast指针走过的节点数：`x + y + n (y + z)`，n为fast指针在环内走了n圈才遇到slow指针， （y+z）为 一圈内节点的个数A。

因为fast指针是一步走两个节点，slow指针一步走一个节点， 所以 fast指针走过的节点数 = slow指针走过的节点数 * 2：
$$
(x + y) * 2 = x + y + n (y + z)
$$
两边消掉一个（x+y）: 
$$
x + y = n (y + z)
$$
因为要找环形的入口，那么要求的是x，因为x表示 头结点到 环形入口节点的的距离。

所以要求x ，将x单独放在左面：
$$
x = n (y + z) - y
$$


再从n(y+z)中提出一个 （y+z）来，整理公式之后为如下公式：
$$
x = (n - 1) (y + z) + z
$$
注意这里n一定是大于等于1的，因为 fast指针至少要多走一圈才能相遇slow指针。

**这个公式说明什么呢？**

先拿n为1的情况来举例，意味着fast指针在环形里转了一圈之后，就遇到了 slow指针了。

当 n为1的时候，公式就化解为 `x = z`，

这就意味着，**从头结点出发一个指针，从相遇节点 也出发一个指针，这两个指针每次只走一个节点， 那么当这两个指针相遇的时候就是 环形入口的节点**。

也就是在相遇节点处，定义一个指针index1，在头结点处定一个指针index2。

让index1和index2同时移动，每次移动一个节点， 那么他们相遇的地方就是 环形入口的节点。

**那么 n如果大于1是什么情况呢**，就是fast指针在环形转n圈之后才遇到 slow指针。

其实这种情况和n为1的时候 **效果是一样的**，一样可以通过这个方法找到 环形的入口节点，只不过，index1 指针在环里 多转了(n-1)圈，然后再遇到index2，相遇点依然是环形的入口节点。

动画如下：

![142 环形链表II（求入口）](https://github.com/user-attachments/assets/0975e1e3-41cc-402d-9a28-cc9b1615767b)




#### 题解：快慢指针法

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        ListNode* fast = head;
        ListNode* slow = head;
        while(fast != NULL && fast->next != NULL) {
            slow = slow->next;
            fast = fast->next->next;
            // 快慢指针相遇，此时从head 和 相遇点，同时查找直至相遇
            if (slow == fast) {
                ListNode* index1 = fast;
                ListNode* index2 = head;
                while (index1 != index2) {
                    index1 = index1->next;
                    index2 = index2->next;
                }
                return index2; // 返回环的入口
            }
        }
        return NULL;
    }
};
```
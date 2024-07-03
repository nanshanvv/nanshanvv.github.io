## 二分搜索法

### [[题目：Leetcode.704](https://leetcode.cn/problems/binary-search/submissions/543800734/)](https://leetcode.cn/problems/binary-search/submissions/543800734/)

### 视频讲解：[[代码随想录](https://www.bilibili.com/video/BV1fA4y1o715/?vd_source=a693917376813c5b1608ec0a4132e636)](https://www.bilibili.com/video/BV1fA4y1o715/?vd_source=a693917376813c5b1608ec0a4132e636)

### 重点：

1. 对于`while(left < right)` 还是 `while(left <= right)`的判断
2. 对于是`right = middle`还是`right = middle - 1`的判断



**关注点：主要是对于区间的定义，是[left,right] , [left,right)**

#### 1.闭区间写法

```c++
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int left = 0;
        int right = nums.size() - 1; // 定义target在左闭右闭的区间里，[left, right]
        while (left <= right) { // 当left==right，区间[left, right]依然有效，所以用 <=
            int middle = left + ((right - left) / 2);// 防止溢出 等同于(left + right)/2
            if (nums[middle] > target) {
                right = middle - 1; // target 在左区间，所以[left, middle - 1]
            } else if (nums[middle] < target) {
                left = middle + 1; // target 在右区间，所以[middle + 1, right]
            } else { // nums[middle] == target
                return middle; // 数组中找到目标值，直接返回下标
            }
        }
        // 未找到目标值
        return -1;
    }
};
```

**笔记：**

​	**1.因为是闭区间，所以while(left <= right)**

​	如果该数组为[8]，因为闭区间 left = right 存在，所以用 while(left <= right)

​	**2.因为是if(num[middle] > target), 所以 right = middle -1 （middle+1同理）**

​	因为 if(num[middle] > target) 已经限制 num[middle]！=target ，所以直接 right = middle -1



#### 2.左闭右开写法

```c++
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int left = 0;
        int right = nums.size(); // 定义target在左闭右开的区间里，即：[left, right)
        while (left < right) { // 因为left == right的时候，在[left, right)是无效的空间，所以使用 <
            int middle = left + ((right - left) >> 1);
            if (nums[middle] > target) {
                right = middle; // target 在左区间，在[left, middle)中
            } else if (nums[middle] < target) {
                left = middle + 1; // target 在右区间，在[middle + 1, right)中
            } else { // nums[middle] == target
                return middle; // 数组中找到目标值，直接返回下标
            }
        }
        // 未找到目标值
        return -1;
    }
};
```

**笔记：**

​	**1.因为是左闭右开区间，所以while(left < right)**

​	如果有数组为[8），因为闭区间 left = right 不存在，所以用 while(left < right) 

​	**2.因为是if(num[middle] > target)且左闭右开, 所以 right = middle **

​	因为是左闭右开且 target 在左区间，所以 right 直接取 middle 后区间为 [left,middle) ,下一个查询区间不会去比较 nums[middle]，所以 right = middle; 如果 right = middle -1 的话区间就成 [left , middle - 1), 假如 middle-1 就是 target 就比不到了

​	**注意：当target在右区间，left = middle + 1** 







## 移除元素

### [[题目：Leetcode.27](https://leetcode.cn/problems/remove-element/)](https://leetcode.cn/problems/remove-element/)

### 视频讲解：[[代码随想录](https://www.bilibili.com/video/BV12A4y1Z7LP/?spm_id_from=333.788&vd_source=a693917376813c5b1608ec0a4132e636)](https://www.bilibili.com/video/BV12A4y1Z7LP/?spm_id_from=333.788&vd_source=a693917376813c5b1608ec0a4132e636)

### **重点：双指针法**



### 我使用的暴力解法

```c++
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        int n = 0;
        int m[100] = {};
        for (int i = 0; i < nums.size(); i++) {
            if (nums[i] != val) {
                m[n] = nums[i];
                n++;
            }
        }

        for (int i = 0; i < n; i++) {
            nums[i] = m[i];
        }
        return n;
    }
};
```



### 双指针法（快慢指针法）
![27 移除元素-双指针法](https://github.com/nanshanvv/nanshanvv.github.io/assets/77189526/ee7d5620-b500-4504-b036-89f5aed99512)

- 快指针：寻找新数组的元素 ，新数组就是不含有目标元素的数组
- 慢指针：指向更新 新数组下标的位置



```c++
// 时间复杂度：O(n)
// 空间复杂度：O(1)
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        int slowIndex = 0;
        for (int fastIndex = 0; fastIndex < nums.size(); fastIndex++) {
            if (val != nums[fastIndex]) {
                nums[slowIndex++] = nums[fastIndex];
            }
        }
        return slowIndex;
    }
};
```
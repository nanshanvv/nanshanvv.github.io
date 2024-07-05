# 有序数组的平方(双指针法)

### [[题目：Leetcode.977](https://leetcode.cn/problems/squares-of-a-sorted-array/description/)](https://leetcode.cn/problems/squares-of-a-sorted-array/description/)

### 视频讲解：[[代码随想录](https://www.bilibili.com/video/BV1QB4y1D7ep/)](https://www.bilibili.com/video/BV1QB4y1D7ep/)

**重点：理解什么时候该用双指针法**



## [[复习vector](https://www.runoob.com/w3cnote/cpp-vector-container-analysis.html)](https://www.runoob.com/w3cnote/cpp-vector-container-analysis.html)

- vector():创建一个空vector
- vector(int nSize):创建一个vector,元素个数为nSize
- vector(int nSize,const t& t):创建一个vector，元素个数为nSize,且值均为t
- vector(const vector&):复制构造函数
- vector(begin,end):复制[begin,end)区间内另一个数组的元素到vector中

## 双指针法

数组其实是有序的， 只不过负数平方之后可能成为最大数了。

那么数组平方的**最大值**就在数组的两端，不是最左边就是最右边，不可能是中间。

此时可以考虑双指针法了，i指向起始位置，j指向终止位置。

定义一个新数组result，和A数组一样的大小，让k指向result数组终止位置。

如果`A[i] * A[i] < A[j] * A[j]` 那么`result[k--] = A[j] * A[j];` 。

如果`A[i] * A[i] >= A[j] * A[j]` 那么`result[k--] = A[i] * A[i];` 。

![977 有序数组的平方](https://github.com/nanshanvv/nanshanvv.github.io/assets/77189526/ec2f8391-a16e-4577-8dc8-835fff27ffd6)

```c++
class Solution {
public:
    vector<int> sortedSquares(vector<int>& A) {
        int k = A.size() - 1;
        vector<int> result(A.size(), 0);
        for (int i = 0, j = A.size() - 1; i <= j;) { // 注意这里要i <= j，因为最后要处理两个元素
            if (A[i] * A[i] < A[j] * A[j])  {
                result[k--] = A[j] * A[j];
                j--;
            }
            else {
                result[k--] = A[i] * A[i];
                i++;
            }
        }
        return result;
    }
};
```

**注意的问题：**

为什么新建的数组只能从大到小填？

答：原数组只能确定平方后最大值！





# 长度最小的子数组(滑动窗口法)

### [[题目：Leetcode.209](https://leetcode.cn/problems/minimum-size-subarray-sum/description/)](https://leetcode.cn/problems/minimum-size-subarray-sum/description/)

### 视频讲解：[[代码随想录](https://www.bilibili.com/video/BV1QB4y1D7ep/)](https://www.bilibili.com/video/BV1QB4y1D7ep/)

**重点：**

**1.如何确定结束位置**

**2.如何确定起始位置**



### 滑动窗口

**大致过程：**

首先，整个过程是有结束位置决定（for循环即结束位置的变化）;

其次，起始位置在窗口值满足题中条件后才往后移；

**我总结出：结束位置在窗口中的值不满足时才向后移，当满足题中条件后，结束位置停止移动，起始位置向后移，并判断是不是仍然满足题意；满足：起始位置继续向后移，不满足：结束位置向后移**

![209 长度最小的子数组](https://github.com/nanshanvv/nanshanvv.github.io/assets/77189526/5a843b17-7be4-48d8-ad25-4c8c8c62130e)










```c++
class Solution {
public:
    int minSubArrayLen(int s, vector<int>& nums) {
        int result = INT32_MAX;
        int sum = 0; // 滑动窗口数值之和
        int i = 0; // 滑动窗口起始位置
        int subLength = 0; // 滑动窗口的长度
        for (int j = 0; j < nums.size(); j++) {
            sum += nums[j];
            // 注意这里使用while，每次更新 i（起始位置），并不断比较子序列是否符合条件
            while (sum >= s) {
                subLength = (j - i + 1); // 取子序列的长度
                result = result < subLength ? result : subLength;
                sum -= nums[i++]; // 这里体现出滑动窗口的精髓之处，不断变更i（子序列的起始位置）
            }
        }
        // 如果result没有被赋值的话，就返回0，说明没有符合条件的子序列
        return result == INT32_MAX ? 0 : result;
    }
};
```



**易错点：**

**1.一定要把result初始化为`INT32_MAX`或者`INT_MAX`**

如果不result初始化很大的话，result一直就都是最小的，那while循环就永远不会更新result的值

**2.起始位置是while循环决定的，终止位置是for循环决定的**



# 螺旋矩阵II

### [[题目：Leetcode.59](https://leetcode.cn/problems/spiral-matrix-ii/description/)](https://leetcode.cn/problems/spiral-matrix-ii/description/)

### 视频讲解：[[代码随想录](https://www.bilibili.com/video/BV1SL4y1N7mV/)](https://www.bilibili.com/video/BV1SL4y1N7mV/)

**重点：**

1.理解如何用左闭右开的思想去转圈

2.当输入n返回一个n*n的矩阵，需要转n/2圈，其中如果n为偶数，则需要我们手动将最后一个值赋值到中间

3.如何控制每一圈的终止位置（offset）
<img width="731" alt="20220922102236" src="https://github.com/nanshanvv/nanshanvv.github.io/assets/77189526/7570f4c5-3a0f-45a1-b507-d13a2c7d4deb">



```cpp
class Solution {
public:
    vector<vector<int>> generateMatrix(int n) {
        vector<vector<int>> res(n, vector<int>(n, 0)); // 使用vector定义一个二维数组
        int startx = 0, starty = 0; // 定义每循环一个圈的起始位置
        int loop = n / 2; // 每个圈循环几次，例如n为奇数3，那么loop = 1 只是循环一圈，矩阵中间的值需要单独处理
        int mid = n / 2; // 矩阵中间的位置，例如：n为3， 中间的位置就是(1，1)，n为5，中间位置为(2, 2)
        int count = 1; // 用来给矩阵中每一个空格赋值
        int offset = 1; // 需要控制每一条边遍历的长度，每次循环右边界收缩一位
        int i,j;
        while (loop --) {
            i = startx;
            j = starty;

            // 下面开始的四个for就是模拟转了一圈
            // 模拟填充上行从左到右(左闭右开)
            for (j; j < n - offset; j++) {
                res[i][j] = count++;
            }
            // 模拟填充右列从上到下(左闭右开)
            for (i; i < n - offset; i++) {
                res[i][j] = count++;
            }
            // 模拟填充下行从右到左(左闭右开)
            for (; j > starty; j--) {
                res[i][j] = count++;
            }
            // 模拟填充左列从下到上(左闭右开)
            for (; i > startx; i--) {
                res[i][j] = count++;
            }

            // 第二圈开始的时候，起始位置要各自加1， 例如：第一圈起始位置是(0, 0)，第二圈起始位置是(1, 1)
            startx++;
            starty++;

            // offset 控制每一圈里每一条边遍历的长度
            offset += 1;
        }

        // 如果n为奇数的话，需要单独给矩阵最中间的位置赋值
        if (n % 2) {
            res[mid][mid] = count;
        }
        return res;
    }
};
```
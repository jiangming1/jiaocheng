####数组中的问题其实最常见。

>排序：选择排序；插入排序；归并排序；快速排序
查找：二分查找法
数据结构：栈；队列；堆
……

如何写出正确的程序
建立一个基础的框架，什么是正确的程序

二分查找法:
	- 二分查找法的思想在1946年提出。
	- 第一个没有bug的二分查找法在1962年才出现。

对于有序数列，才能使用二分查找法 （排序的作用）

![二分查找：前提排序](http://upload-images.jianshu.io/upload_images/1779926-3e014b7f740b7c79.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

会用自然语言描述谁都会，但是具体实现就会遇到问题。

- 边界问题
- 索引的指向

```c
template<typename T>
int binarySearch( T arr[], int n, T target ){

    int l = 0, r = n-1; // 在[l...r]的范围里寻找target：前闭后闭
    while( l <= r ){    // 只要还有可以查找的内容。当 l == r时,区间[l...r]依然是有效的
        int mid = l + (r-l)/2;
        if( arr[mid] == target ) return mid;
        //mid已经判断过了
        if( target > arr[mid] )
            l = mid + 1;  // target在[mid+1...r]中; [l...mid]一定没有target
        else    // target < arr[mid]
            r = mid - 1;  // target在[l...mid-1]中; [mid...r]一定没有target
    }

    return -1;
}
```

循环不变量。声明不变。控制边界。

```c
int l = 0, r = n-1; // 在[l...r]的范围里寻找target：前闭后闭
```
main.cpp:

```c
int main() {

    int n = pow(10,7);
    int* data = MyUtil::generateOrderedArray(n);

    clock_t startTime = clock();
    for( int i = 0 ; i < n ; i ++ )
        assert( i == binarySearch(data, n, i) );
    clock_t endTime = clock();

    cout<<"binarySearch test complete."<<endl;
    cout<<"Time cost: "<<double(endTime - startTime)/CLOCKS_PER_SEC<<" s"<<endl;
    return 0;
}
```

####改变变量的定义，依然可以写出正确的算法

```c
template<typename T>
int binarySearch( T arr[], int n, T target ){

    int l = 0, r = n; // target在[l...r)的范围里，这样设置才能保证长度为n
    while( l < r ){    // 当 l == r时,区间[l...r)是一个无效区间 [42,43)
        int mid = l + (r-l)/2;
        if( arr[mid] == target ) return mid;
        if( target > arr[mid] )
            l = mid + 1;    // target在[mid+1...r)中; [l...mid]一定没有target
        else// target < arr[mid]
            r = mid;        // target在[l...mid)中; [mid...r)一定没有target
    }

    return -1;
}
```

>边界量的推导。

```c
int mid = (l+r)/2
int mid = l + (r-l)/2;
```
>直接相加存在整型溢出的风险。

l加上范围长度的一半。

如何写出正确的程序？

- 明确变量的含义
- 循环不变量
- 小数据量调试（4到6个数据）空集，边界。
- 大数据量测试（性能）

耐心找到bug。定位错误发生的位置。小数据集上代码如何运作的。

####LeetCode上的283 Move Zeros

https://leetcode.com/problems/move-zeroes/

公司：Facebook & Bloomberg

Given an array nums, write a function to move all 0's to the end of it while maintaining the relative order of the non-zero elements.

For example, given nums = [0, 1, 0, 3, 12], after calling your function, nums should be [1, 3, 12, 0, 0].

Note:
You must do this in-place without making a copy of the array.
Minimize the total number of operations.

>给定一个数组nums，写一个函数，将数组中所有的0挪到数组的末尾，而维持其他所有非0元素的相对位置。

>举例： nums = [0, 1, 0, 3, 12]，函数运行后结果为[1, 3, 12, 0, 0]

**解题的模板**

```c
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        //书写函数的逻辑
    }
};
```

不要纠结语言，完全可以使用自己熟悉的语言。

最直观思路：

![拿出非0元素](http://upload-images.jianshu.io/upload_images/1779926-5d797f817f54f656.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

将非0元素拿出来，然后空位补0.

```c
class Solution {
public:
    // 时间复杂度 O(n)
    // 空间复杂度 O(n) 新创建数组
    void moveZeroes(vector<int>& nums) {

        vector<int> nonZeroElements;

        // 将vec中所有非0元素放入nonZeroElements中
        for( int i = 0 ; i < nums.size() ; i ++ )
            if( nums[i] )
                nonZeroElements.push_back( nums[i] );

        // 将nonZeroElements中的所有元素依次放入到nums开始的位置
        for( int i = 0 ; i < nonZeroElements.size() ; i ++ )
            nums[i] = nonZeroElements[i];

        // 将nums剩余的位置放置为0
        for( int i = nonZeroElements.size() ; i < nums.size() ; i ++ )
            nums[i] = 0;
    }
};

int main() {

    int arr[] = {0, 1, 0, 3, 12};
    //根据生成的数据创建vector：传入头指针和尾指针
    vector<int> vec(arr, arr + sizeof(arr)/sizeof(int));

    Solution().moveZeroes(vec);

    for( int i = 0 ; i < vec.size() ; i ++ )
        cout<<vec[i]<<" ";
    cout<<endl;

    return 0;
}
```

运行结果：

![运行结果](http://upload-images.jianshu.io/upload_images/1779926-06e63457f7cf110a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![基本测试用例测试](http://upload-images.jianshu.io/upload_images/1779926-cd571ab845c34b1f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![accept](http://upload-images.jianshu.io/upload_images/1779926-d97127987356be08.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![测试细节](http://upload-images.jianshu.io/upload_images/1779926-3f4344a4dded7c8f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>21个测试用例都通过了。

图表展示了效率之间的差异。

![击败了97.71](http://upload-images.jianshu.io/upload_images/1779926-e3ca6ef6395906a4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####即使简单的算法也能进一步优化。

因为非0的一定小于等于有0的。

![不要开辟新空间](http://upload-images.jianshu.io/upload_images/1779926-f27da04f8a1f960f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

1直接放到0的位置。k指向空位置。
k - [0…k)中保存所有当前遍历过的非0元素

```c
class Solution {
public:
    // 时间复杂度 O(n)
    // 空间复杂度 O(1)
    void moveZeroes(vector<int>& nums) {

        int k = 0; // nums中, [0...k)的元素均为非0元素

        // 遍历到第i个元素后,保证[0...i]中所有非0元素
        // 都按照顺序排列在[0...k)中
        for(int i = 0 ; i < nums.size() ; i ++ )
            if( nums[i] )
                nums[k++] = nums[i];

        // 将nums剩余的位置放置为0
        for( int i = k ; i < nums.size() ; i ++ )
            nums[i] = 0;
    }
};

int main() {

    int arr[] = {0, 1, 0, 3, 12};
    vector<int> vec(arr, arr + sizeof(arr)/sizeof(int));

    Solution().moveZeroes(vec);

    for( int i = 0 ; i < vec.size() ; i ++ )
        cout<<vec[i]<<" ";
    cout<<endl;

    return 0;
}
```
**进一步优化**

非0的赋值不用操作了。

非0的与0直接互换。

```c
class Solution {
public:
    // 时间复杂度 O(n)
    // 空间复杂度 O(1)
    void moveZeroes(vector<int>& nums) {

        int k = 0; // nums中, [0...k)的元素均为非0元素

        // 遍历到第i个元素后,保证[0...i]中所有非0元素
        // 都按照顺序排列在[0...k)中
        // 同时, [k...i] 为0
        for(int i = 0 ; i < nums.size() ; i ++ )
            if( nums[i] )
                swap( nums[k++] , nums[i] );

    }
};
```

极端情况：如果都为非0，则每个都自己和自己交换。

```c
class Solution {
public:
    // 时间复杂度 O(n)
    // 空间复杂度 O(1)
    void moveZeroes(vector<int>& nums) {

        int k = 0; // nums中, [0...k)的元素均为非0元素

        // 遍历到第i个元素后,保证[0...i]中所有非0元素
        // 都按照顺序排列在[0...k)中
        // 同时, [k...i] 为0
        for(int i = 0 ; i < nums.size() ; i ++ )
            if( nums[i] )
            	//
                if( k != i )
                    swap( nums[k++] , nums[i] );
                else// i == k
                    k ++;
    }
};

```

**27：Remove Element**

Given an array and a value, remove all instances of that value in place and return the new length.
Do not allocate extra space for another array, you must do this in place with constant memory.
The order of elements can be changed. It doesn't matter what you leave beyond the new length.

>Example:
Given input array nums = [3,2,2,3], val = 3
Your function should return length = 2, with the first two elements of nums being 2.

给定一个数组nums和一个数值val，将数组中所有等于val的元素删除，并返回剩余的元素个数。

如nums = [3, 2, 2, 3], val = 3；
返回2，且nums中前两个元素为2

- 如何定义删除？从数组中去除？还是放在数组末尾？
- 剩余元素的排列是否要保证原有的相对顺序？
- 是否有空间复杂度的要求？ O(1)

**26：Remove Duplicates from Sorted Array**

公司：Facebook & Microsoft & Bloomberg

Given a sorted array, remove the duplicates in place such that each element appear only once and return the new length.

Do not allocate extra space for another array, you must do this in place with constant memory.

For example,
Given input array nums = [1,1,2],

Your function should return length = 2, with the first two elements of nums being 1 and 2 respectively. It doesn't matter what you leave beyond the new length.

>给定一个有序数组，对数组中的元素去重，使得原数组的每个元素只有一个。返回去重后数组的长度值。

- 如 nums = [1, 1, 2]，
- 结果应返回2，且nums的前两个元素为1和2

- 如何定义删除？从数组中去除？还是放在数组末尾？
- 剩余元素的排列是否要保证原有的相对顺序？
- 是否有空间复杂度的要求？ O(1)

**80 Remove Duplicated from Sorted Array II**

Facebook

Follow up for "Remove Duplicates":
What if duplicates are allowed at most twice?

For example,
Given sorted array nums = [1,1,1,2,2,3],

Your function should return length = 5, with the first five elements of nums being 1, 1, 2, 2 and 3. It doesn't matter what you leave beyond the new length.

给定一个有序数组，对数组中的元素去重，使得原数组的每个元素最多保留两个。返回去重后数组的长度值。

>如 nums = [1, 1, 1, 2, 2, 3]，
结果应返回5，且nums的前五个元素为1, 1, 2, 2, 3

####基础算法思路的应用

**75 Sort Colors**

FaceBook Microsoft PocketGems

Given an array with n objects colored red, white or blue, sort them so that objects of the same color are adjacent, with the colors in the order red, white and blue.

Here, we will use the integers 0, 1, and 2 to represent the color red, white, and blue respectively.

Note:
You are not suppose to use the library's sort function for this problem.

click to show follow up.

Follow up:
A rather straight forward solution is a two-pass algorithm using counting sort.
First, iterate the array counting number of 0's, 1's, and 2's, then overwrite array with total number of 0's, then 1's and followed by 2's.

Could you come up with an one-pass algorithm using only constant space?

>给定一个有n个元素的数组，数组中元素的取值只有0, 1, 2三种可能。为这个数组排序。

- 可以使用任意一种排序算法
- 没有使用上题目中给出的特殊条件

所有元素取值只有0/1/2这三种。

数出数组中共有多少元素，然后排序。计数排序：分别统计0, 1, 2的元素个数。

![统计](http://upload-images.jianshu.io/upload_images/1779926-9dab34c4cbe679fd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**基数排序法**

```c
// 时间复杂度: O(n)
// 空间复杂度: O(k), k为元素的取值范围
// 对整个数组遍历了两遍
class Solution {
public:
    void sortColors(vector<int> &nums) {

        int count[3] = {0};    // 存放0,1,2三个元素的频率
        for( int i = 0 ; i < nums.size() ; i ++ ){
            assert( nums[i] >= 0 && nums[i] <= 2 );
            count[nums[i]] ++;
        }

        int index = 0;
        for( int i = 0 ; i < count[0] ; i ++ )
            nums[index++] = 0;
        for( int i = 0 ; i < count[1] ; i ++ )
            nums[index++] = 1;
        for( int i = 0 ; i < count[2] ; i ++ )
            nums[index++] = 2;

        // 小练习: 更加自使用的计数排序
    }
};

int main() {

    int nums[] = {2, 2, 2, 1, 1, 0};
    vector<int> vec = vector<int>( nums , nums + sizeof(nums)/sizeof(int));

    Solution().sortColors( vec );
    for( int i = 0 ; i < vec.size() ; i ++ )
        cout<<vec[i]<<" ";
    cout<<endl;

    return 0;
}
```

可以只扫描一遍么？

![一次三路快排](http://upload-images.jianshu.io/upload_images/1779926-becdb8f80c3a4e53.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>设置三个索引：zero two i 

![三路快排](http://upload-images.jianshu.io/upload_images/1779926-61999d430b0fce48.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```c
// 时间复杂度: O(n)
// 空间复杂度: O(1)
// 对整个数组只遍历了一遍
class Solution {
public:
    void sortColors(vector<int> &nums) {

        int zero = -1;          // [0...zero] == 0
        int two = nums.size();  // [two...n-1] == 2
        for( int i = 0 ; i < two ; ){
            if( nums[i] == 1 )
                i ++;
            else if ( nums[i] == 2 )
                swap( nums[i] , nums[--two]);
            else{ // nums[i] == 0
                assert( nums[i] == 0 );
                swap( nums[++zero] , nums[i++] );
            }
        }
    }
};

int main() {

    int nums[] = {2, 2, 2, 1, 1, 0};
    vector<int> vec = vector<int>( nums , nums + sizeof(nums)/sizeof(int));

    Solution().sortColors( vec );
    for( int i = 0 ; i < vec.size() ; i ++ )
        cout<<vec[i]<<" ";
    cout<<endl;

    return 0;
}
```

**88 Merge Sorted Array**

Facebook & Microsoft & Bloomberg

给定两个有序整型数组nums1, nums2，将nums2的元素归并到nums1中

**215 Kth Largest Element in an Array**

>在一个整数序列中寻找第k大的元素
如给定数组 [3, 2, 1, 5, 6, 4], k = 2， 结果为5
利用快排partition中，将pivot放置在了其正确的位置上的性质

利用快排partition中，将pivot放置在了其正确的位置上的性质

![快排分区](http://upload-images.jianshu.io/upload_images/1779926-e858905891bcba39.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####双索引技术 Two Pointer

**167 Two Sum II - Input array is sorted**

>给定一个有序整型数组和一个整数target，在其中寻找两个元素，使其和为target。返回这两个数的索引。
如numbers = [2, 7, 11, 15], target = 9
返回数字2，7的索引1, 2 (索引从1开始计算)

- 如果没有解怎样？保证有解
- 如果有多个解怎样？返回任意解

最直接的思考：暴力解法。双层遍历，O(n^2)

暴力解法没有充分利用原数组的性质 —— 有序：有序？二分搜索？

![有序的二分搜索](http://upload-images.jianshu.io/upload_images/1779926-cfa841e72ca3980a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![初始化的ij](http://upload-images.jianshu.io/upload_images/1779926-4915d4b9990fbd5a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

一般会是大于或者小于。
如果大i++ 小 j--
两个索引在往中间走。对撞指针。

```c
// 时间复杂度: O(n^2)
// 空间复杂度: O(1)
class Solution {
public:
    vector<int> twoSum(vector<int>& numbers, int target) {

        assert( numbers.size() >= 2 );
        // assert( isSorted(numbers) );

        for( int i = 0 ; i < numbers.size() ; i ++ )
            for( int j = i+1 ; j < numbers.size() ; j ++ )
                if( numbers[i] + numbers[j] == target ){
                    int res[2] = {i+1, j+1};
                    return vector<int>(res, res+2);
                }


        throw invalid_argument("the input has no solution");
    }

};

int main() {

    int nums[] = {2, 7, 11, 15};
    vector<int> vec(nums, nums + sizeof(nums)/sizeof(int));
    int target = 9;

    vector<int> res = Solution().twoSum( vec, target );
    for( int i = 0 ; i < res.size() ; i ++ )
        cout<<res[i]<<" ";
    cout<<endl;

    return 0;
}
```

对撞指针代码实现如下：

```c
// 时间复杂度: O(n)
// 空间复杂度: O(1)
class Solution {
public:
    vector<int> twoSum(vector<int>& numbers, int target) {

        assert( numbers.size() >= 2 );
        // assert( isSorted(numbers) );

        int l = 0, r = numbers.size()-1;
        while( l < r ){

            if( numbers[l] + numbers[r] == target ){
                int res[2] = {l+1, r+1};
                return vector<int>(res, res+2);
            }
            else if( numbers[l] + numbers[r] < target )
                l ++;
            else // numbers[l] + numbers[r] > target
                r --;
        }

        throw invalid_argument("the input has no solution");
    }

};
```

错误处理:

```c
throw invalid_argument("the input has no solution");
```

三路快排也是对撞指针思路

对撞指针思路：

**125 Valid Palindrome**

>给定一个字符串，只看其中的数字和字母，忽略大小写，判断这个字符串是否为回文串？

“A man, a plan, a canal; Panama” - 是回文串
“race a car” - 不是回文串

回文串：正着看和反着看是一样的。

- 空字符串如何看？
- 字符的定义？
- 大小写问题

**344 Reverse String**

>给定一个字符串，返回这个字符串的倒序字符串。

>- 如“hello”，返回”olleh”
- 类似：翻转一个数组

**345 Reverse Vowels of a String**

>给定一个字符串，将该字符串中的元音字母翻转
如：给出 ”hello”，返回”holle”
如：给出“leetcode”，返回“leotcede”
元音不包含y

**11 Container With Most Water**

>给出一个非负整数数组 a1,a2,a3,…,an；每一个整数表示一个竖立在坐标轴x位置的一堵高度为ai的“墙”，选择两堵墙，和x轴构成的容器可以容纳最多的水。

![任取两墙](http://upload-images.jianshu.io/upload_images/1779926-c22fea3e77698a6d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

盛水为最大值。

####双索引技术 Two Pointer

滑动窗口

**Minimum Size Subarray Sum**

>给定一个整型数组和一个数字s，找到数组中最短的一个连续子数组，使得连续子数组的数字和sum >= s，返回这个最短的连续子数组的长度值
如，给定数组[2, 3, 1, 2, 4, 3], s = 7
答案为[4, 3]，返回2

什么叫子数组？

![一般不要求连续](http://upload-images.jianshu.io/upload_images/1779926-9688eeb0ad39ffb1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

而这个题目中规定了子数组要连续这样的特性。

- 什么叫子数组
- 如果没有解怎么办？返回0

>暴力解：遍历所有的连续子数组[i...j]
计算其和sum，验证sum >= s
时间复杂度O(n^3)

![如果当前子数组不到就往后再看一个](http://upload-images.jianshu.io/upload_images/1779926-30a6d2a73eb06084.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

窗口不停向前滑动。

```c
// 滑动窗口的思路
// 时间复杂度: O(n)
// 空间复杂度: O(1)
class Solution {
public:
    int minSubArrayLen(int s, vector<int>& nums) {

        int l = 0 , r = -1; // nums[l...r]为我们的滑动窗口
        int sum = 0;
        int res = nums.size()+1;

        while( l < nums.size() ){   // 窗口的左边界在数组范围内,则循环继续

            if( r + 1 < nums.size() && sum < s )
                sum += nums[++r];
            else // r已经到头 或者 sum >= s
                sum -= nums[l++];

            if( sum >= s )
                res = min(res, r-l+1);
        }

        if( res == nums.size() + 1 )
            return 0;
        return res;
    }
};

int main() {

    int nums[] = {2, 3, 1, 2, 4, 3};
    vector<int> vec( nums, nums + sizeof(nums)/sizeof(int) );
    int s = 7;

    cout<<Solution().minSubArrayLen(s, vec)<<endl;

    return 0;
}
```

####另一个滑动窗口的例子

**3 Longest Substring Without Repeating Characters**

在一个字符串中寻找没有重复字母的最长子串，返回其长度。

- 如”abcabcbb”，则结果为”abc”，长度为3
- 如”bbbbb”，则结果为”b”，长度为1
- 如”pwwkew”，则结果为”wke”，长度为3

考虑：

>字符集？只有字母？数字+字母？ASCII？
大小写是否敏感？

![往后++](http://upload-images.jianshu.io/upload_images/1779926-5c3e9355ecae97ee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

j++如果没有重复元素。继续加加。

![i++去除重复](http://upload-images.jianshu.io/upload_images/1779926-f8f40349d6d48db2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如何记录重复字符？freq[256]

实现代码：

```c
class Solution {
public:
    int lengthOfLongestSubstring(string s) {

        int freq[256] = {0};

        int l = 0, r = -1; //滑动窗口为s[l...r]
        int res = 0;

        // 整个循环从 l == 0; r == -1 这个空窗口开始
        // 到l == s.size(); r == s.size()-1 这个空窗口截止
        // 在每次循环里逐渐改变窗口, 维护freq, 并记录当前窗口中是否找到了一个新的最优值
        while( l < s.size() ){

            if( r + 1 < s.size() && freq[s[r+1]] == 0 )
                freq[s[++r]] ++;
            else    //r已经到头 || freq[s[r+1]] == 1
                freq[s[l++]] --;

            res = max( res , r-l+1);
        }

        return res;
    }
};

int main() {

    cout << Solution().lengthOfLongestSubstring( "abcabcbb" )<<endl;
    cout << Solution().lengthOfLongestSubstring( "bbbbb" )<<endl;
    cout << Solution().lengthOfLongestSubstring( "pwwkew" )<<endl;
    cout << Solution().lengthOfLongestSubstring( "" )<<endl;

    return 0;
}
```

**438 Find All Anagrams in a String**

>给定一个字符串s和一个非空字符串p，找出p中的所有是s的anagrams字符串的子串，返回这些子串的起始索引。
如 s = "cbaebabacd" p = “abc”，返回[0, 6]
如 s = "abab" p = “ba”，返回[0, 1, 2]

字符集范围？英文小写字母
返回的解的顺序？任意。

**76 Minimum Window Substring**

给定一个字符串S和T，在S中寻找最短的子串，包含T中的所有字符

- 如 S = “ADOBECODEBANC" ; T = "ABC"
- 结果为 "BANC"

>字符集范围
若没有解？ 返回“”
若有多个解？保证只有一个解
什么叫包含所有字符？S = “a”，T = “aa”

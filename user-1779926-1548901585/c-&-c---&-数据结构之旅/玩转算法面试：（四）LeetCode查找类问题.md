##查找问题

两类查找问题

- 查找有无：元素’a’是否存在？set；集合
- 查找对应关系（键值对应）：元素’a’出现了几次？map；字典

通常语言的标准库中都内置set和map

>- 容器类
- 屏蔽实现细节
- 了解语言中标准库里常见容器类的使用

常见操作：

>- insert
- find
- erase:删除
- change (map)

**349. Intersection of Two Arrays**

给定两个数组nums，求两个数组的公共元素。

- 如nums1 = [1, 2, 2, 1], nums2 = [2, 2]
- 结果为[2]
- 结果中每个元素只能出现一次
- 出现的顺序可以是任意的

```c
// 349. Intersection of Two Arrays
// 时间复杂度：O(nlogn)
// 空间复杂度：O(n)
class Solution {
public:
    vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {
        //O(nlogn)

        set<int> record;
        for( int i = 0 ; i < nums1.size() ; i ++ )
            record.insert(nums1[i]);
        //O(nlogn)
        set<int> resultSet;
        for( int i = 0 ; i < nums2.size() ; i ++ )
            if( record.find( nums2[i] ) != record.end() )
                resultSet.insert( nums2[i] );
        //o(n)
        vector<int> resultVector;
        for(set<int>::iterator iter = resultSet.begin() ; iter != resultSet.end() ; iter ++ )
            resultVector.push_back( *iter );

        return resultVector;
    }
};

int main() {

    int nums1[] = {1, 2, 2, 1};
    vector<int> vec1(nums1, nums1 + sizeof(nums1)/sizeof(int));

    int nums2[] = {2, 2};
    vector<int> vec2(nums2, nums2 + sizeof(nums2)/sizeof(int));

    vector<int> res = Solution().intersection(vec1, vec2);
    for(int i = 0 ; i < res.size() ; i ++ )
        cout<<res[i]<<" ";
    cout<<endl;

    return 0;
}
```

改写程序:

```c
// 349. Intersection of Two Arrays
class Solution {
public:
    vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {

        set<int> record(nums1.begin(), nums1.end());

        set<int> resultSet;
        for( int i = 0 ; i < nums2.size() ; i ++ )
            if( record.find( nums2[i] ) != record.end() )
                resultSet.insert( nums2[i] );

        return vector<int>(resultSet.begin(), resultSet.end());
    }
};
```

语言库提供的便利。

**350. Intersection of Two Arrays**

给定两个数组nums，求两个数组的交集。
如nums1 = [1, 2, 2, 1], nums2 = [2, 2]
结果为[2, 2]
出现的顺序可以是任意的

```c
// 350. Intersection of Two Arrays II
// 时间复杂度：O(nlogn)
// 空间复杂度：O(n)
class Solution {
public:
    vector<int> intersect(vector<int>& nums1, vector<int>& nums2) {

        map<int, int> record;
        //O(nlogn)
        for( int i = 0 ; i < nums1.size() ; i ++ )
            record[nums1[i]] += 1;

        //o(nlogn)
        vector<int> resultVector;
        for( int i = 0 ; i < nums2.size() ; i ++ )
            if( record[ nums2[i] ] > 0 ){
                resultVector.push_back( nums2[i] );
                record[nums2[i]] --;
            }

        return resultVector;
    }
};

int main() {

    int nums1[] = {1, 2, 2, 1};
    vector<int> vec1(nums1, nums1 + sizeof(nums1)/sizeof(int));

    int nums2[] = {2, 2};
    vector<int> vec2(nums2, nums2 + sizeof(nums2)/sizeof(int));

    vector<int> res = Solution().intersect(vec1, vec2);
    for(int i = 0 ; i < res.size() ; i ++ )
        cout<<res[i]<<" ";
    cout<<endl;

    return 0;
}
```

c++语言中map有默认值0.但是默认值0不代表不存在。

```c
int main() {

    map<int,int> myMap;
    if( myMap.find( 42 ) == myMap.end() )
        cout<<"Can not find element 42"<<endl;
    else
        cout<<"Element 42 is in the map"<<endl;

    cout<<myMap[42]<<endl;
    if( myMap.find( 42 ) == myMap.end() )
        cout<<"Can not find element 42"<<endl;
    else
        cout<<"Element 42 is in the map"<<endl;

    myMap[42] ++;
    cout<<myMap[42]<<endl;
    if( myMap.find( 42 ) == myMap.end() )
        cout<<"Can not find element 42"<<endl;
    else
        cout<<"Element 42 is in the map"<<endl;

    myMap[42] --;
    cout<<myMap[42]<<endl;
    if( myMap.find( 42 ) == myMap.end() )
        cout<<"Can not find element 42"<<endl;
    else
        cout<<"Element 42 is in the map"<<endl;

    myMap.erase( 42 );
    if( myMap.find( 42 ) == myMap.end() )
        cout<<"Can not find element 42"<<endl;
    else
        cout<<"Element 42 is in the map"<<endl;

    return 0;
}
```

运行结果：

![运行结果](http://upload-images.jianshu.io/upload_images/1779926-6e65cb93ca5fa3da.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>c++中如果访问过一次。虽然默认赋值0.但是也把他直接加入了map中。

改进版：将值为0的删除掉。

```c
/// 350. Intersection of Two Arrays II
class Solution {
public:
    vector<int> intersect(vector<int>& nums1, vector<int>& nums2) {

        map<int, int> record;
        for( int i = 0 ; i < nums1.size() ; i ++ )
            if( record.find(nums1[i]) == record.end() )
                record.insert( make_pair(nums1[i],1));
            else
                record[nums1[i]] += 1;

        vector<int> resultVector;
        for( int i = 0 ; i < nums2.size() ; i ++ )
            if( record.find(nums2[i]) != record.end() &&
                record[ nums2[i] ] > 0 ){
                resultVector.push_back( nums2[i] );
                record[nums2[i]] --;
                if( record[nums2[i]] == 0 )
                    record.erase( nums2[i] );
            }

        return resultVector;
    }
};

```
使用c++语言我们推荐第一种。

思考:一旦数组有序该如何做？

set 和 map：

常见操作：

- insert
- find
- erase
- change (map)

set和map可以有不同的底层实现

![底层实现](http://upload-images.jianshu.io/upload_images/1779926-b167da61ea9bbc7d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![哈希表](http://upload-images.jianshu.io/upload_images/1779926-405ebd21f45a6f92.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>哈希表不管查找、插入、删除都是O(1)的时间复杂度。

哈希表的缺点是失去了数据的顺序性

数据的顺序性

- 数据集中的最大值和最小值
- 某个元素的前驱和后继
- 某个元素的floor和ceil
- 某个元素的排位rank
- 选择某个排位的元素select

>map和set的底层实现为平衡二叉树
`unordered_map`和`unordered_set`的底层实现为哈希表

```c
#include <unordered_set>
using namespace std;

// 349. Intersection of Two Arrays
// 时间复杂度：O(n)
// 空间复杂度：O(n)
class Solution {
public:
    vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {
        // O(n)
        unordered_set<int> record(nums1.begin(), nums1.end());
        // O(n)
        unordered_set<int> resultSet;
        for( int i = 0 ; i < nums2.size() ; i ++ )
            if( record.find( nums2[i] ) != record.end() )
                resultSet.insert( nums2[i] );
        // O(n)
        return vector<int>(resultSet.begin(), resultSet.end());
    }
};

int main() {

    int nums1[] = {1, 2, 2, 1};
    vector<int> vec1(nums1, nums1 + sizeof(nums1)/sizeof(int));

    int nums2[] = {2, 2};
    vector<int> vec2(nums2, nums2 + sizeof(nums2)/sizeof(int));

    vector<int> res = Solution().intersection(vec1, vec2);
    for(int i = 0 ; i < res.size() ; i ++ )
        cout<<res[i]<<" ";
    cout<<endl;

    return 0;
}
```

改写过使用hash表实现底层的unorder_map

```c
#include <unordered_map>
using namespace std;

/// 350. Intersection of Two Arrays II
// 时间复杂度：O(n)
// 空间复杂度：O(n)
class Solution {
public:
    vector<int> intersect(vector<int>& nums1, vector<int>& nums2) {
        // O(n)
        unordered_map<int, int> record;
        for( int i = 0 ; i < nums1.size() ; i ++ )
            record[nums1[i]] += 1;
        // O(n)
        vector<int> resultVector;
        for( int i = 0 ; i < nums2.size() ; i ++ )
            if( record[ nums2[i] ] > 0 ){
                resultVector.push_back( nums2[i] );
                record[nums2[i]] --;
            }
        // O(n)
        return resultVector;
    }
};
```

**242. Valid Anagram**

Anagram：一个字符串中的字母调整顺序后与原字符串一致。

- 空串
- 字符集？

**202. Happy Number**

判断一个数是否为happy number。happy number是指，一个数，将其替换为其各位数字的平方和，重复这个过程，如果最终能得到1，这是happy number，如果这个过程陷入了一个不包含1的循环，则不是happy number

判断一个数是否为happy number。以19为例：

1^2 + 9^2 = 82

8^2 + 2^2 = 68

6^2 + 8^2 = 100

1^2 + 0^2 + 0^2 = 1  Happy Number!

**290. Word Pattern**

给出一个模式（pattern）以及一个字符串，判断这个字符串是否符合模式？

- 如pattern=“abba”，str=“dog cat cat dog”，返回true
- 如pattern=“abba”，str=“dog cat cat fish”，返回false

- 字符集？
- 空串符合任意模式？还是不符合任意模式？

**205. Isomorphic Strings**

判断两个字符串是否同构？
如果我们能够寻找到一个字符集到字符集的映射，使得通过这个字符集的映射，s可以转变为t，则称为s和t同构。

- 如 egg 和 add，返回true
- 如 foo 和 bar，返回false
- 如 paper 和 title，返回true

>字符集？
空串
是否可以一个字母映射到自己？

**451 Sort Characters By Frequency**

给定一个字符串，按照字母出现频率的倒序重组整个字符串

- 如“tree”，返回“eert”
- 如“cccaaa”，返回“cccaaa”
- 如“Aabb”，返回“bbAa”
- 对于相同频次的字母，顺序任意。大小写敏感。

####一个使用查找表的经典问题

**1. Two Sum**

给出一个整型数组nums。返回这个数组中两个数字的索引值i和j，使得nums[i] + nums[j] 等于一个给定的target值。两个索引不能相等。

- 如 nums = [2, 7, 11, 15], target = 9
- 返回 [0, 1] 

>索引从0开始计算还是从1开始计算？
没有解怎么办？
有多个解怎么办？ 保证有唯一解

1. Two Sum:

- 暴力解法：O(n^2)

遍历所有数据对。判断是否等于target。

- 排序后，使用双索引对撞：O(nlogn) + O(n) = O(nlogn)


>查找表。将所有元素放入查找表，之后对于每一个元素a，查找 target - 
a 是否存在。存在的话索引是谁。

```c
//时间复杂度:O(n)
//空间复杂度：O(n)
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {

        unordered_map<int,int> record;
        for( int i = 0 ; i < nums.size() ; i ++ ){

            int complement = target - nums[i];
            if( record.find(complement) != record.end() ){
                int res[] = {i, record[complement]};
                return vector<int>(res, res + 2);
            }

            record[nums[i]] = i;
        }

        throw invalid_argument("the input has no solution");
    }
};


int main() {

    const int nums[] = {0,4,3,0};
    vector<int> nums_vec( nums, nums + sizeof(nums)/sizeof(int) );
    int target = 0;

    vector<int> res = Solution().twoSum(nums_vec, target);
    cout<<res[0]<<" , "<<res[1]<<endl;

    return 0;
}
```
Two Sum - 将所有元素放入查找表 - 不行

![只把v前面的放入查找表](http://upload-images.jianshu.io/upload_images/1779926-f1580f8de4c77b66.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**15. 3Sum**

>给出一个整形数组，寻找其中的所有不同的三元组(a,b,c)，使得a+b+c=0
如 nums = [-1, 0, 1, 2, -1, -4]
结果为[ [-1, 0, 1], [-1,-1,2] ]

- 不同的三元组？
- 如果有多个解，解的顺序？
- 如果没有解？

**18. 4Sum**

>给出一个整形数组，寻找其中的所有不同的四元组(a,b,c,d)，使得a+b+c+d 等于一个给定的数字target。
如 nums = [1, 0, -1, 0, -2, 2]，target = 0
结果为[ [-1, 0, 0, 1], [-2, -1, 1, 2], [-2, 0, 0, 2] ]


**16. 3Sum Closest**
给出一个整形数组，寻找其中的三个元素a,b,c，使得a+b+c的值最接近另外一个给定的数字target

- 如 nums = [-1, 2, 1, -4]，target = 1
- 结果为2 ( -1 + 2 + 1 = 2 )

>- 如果有多个解，其和target值的接近程度一样怎么办？
- 如果没解？（可不可能没解？）

####查找合适的元素

**454. 4Sum II**

>给出四个整形数组A,B,C,D，寻找有多少i,j,k,l的组合，使得A[i] + B[j] + C[k] + D[l] == 0。其中，A,B,C,D中均含有相同的元素个数N，且0<=N<=500。

暴力解法：O(n^4)

500^4 = 625,0000,0000

将D中的元素放入查找表：O(n^3)

500^3 = 1,2500,0000

将C+D的每一种可能放入查找表：O(n^2)

500^2 = 25,0000

>要记录每一种和出现了多少次，所以使用map

代码实现：

```c
//时间复杂度O(n^2)
//空间复杂度O(n^2)
class Solution {
public:
    int fourSumCount(vector<int>& A, vector<int>& B, vector<int>& C, vector<int>& D) {

        assert( A.size() == B.size() && B.size() == C.size() && C.size() == D.size() );
        unordered_map<int,int> hashtable;
        for( int i = 0 ; i < C.size() ; i ++ )
            for( int j = 0 ; j < D.size() ; j ++ )
                hashtable[C[i]+D[j]] += 1;

        int res = 0;
        for( int i = 0 ; i < A.size() ; i ++ )
            for( int j = 0 ; j < B.size() ; j ++ )
                if( hashtable.find(-A[i]-B[j]) != hashtable.end() )
                    res += hashtable[-A[i]-B[j]];

        return res;
    }
};

int main() {

    int a[] = {1, 2};
    int b[] = {-2, -1};
    int c[] = {-1, 2};
    int d[] = {0, 2};
    vector<int> a_vec = vector<int>( a , a + sizeof(a)/sizeof(int));
    vector<int> b_vec = vector<int>( b , b + sizeof(b)/sizeof(int));
    vector<int> c_vec = vector<int>( c , c + sizeof(c)/sizeof(int));
    vector<int> d_vec = vector<int>( d , d + sizeof(d)/sizeof(int));

    cout<<Solution().fourSumCount( a_vec, b_vec, c_vec, d_vec )<<endl;

    return 0;
}
```

将A+B和C+D的每一种可能放入两个查找表：O(n^2)

**49. Group Anagrams**

给出一个字符串数组，将其中所有可以通过颠倒字符顺序产生相同结果的单词进行分组。

- 如 [ “eat”, “tea”, “tan”, “ate”, “nat”, “bat”]
- 返回[ [“ate”, “eat”, “tea”], [“nat”, “tan”], [“bat”] ]

>字符集
大小写敏感

####查找合适的元素

**447. Number of Boomerangs**

给出一个平面上的n个点，寻找存在多少个由这些点构成的三元组（i, j, k），使得i,  j两点的距离等于i, k两点的距离。其中n最多为500，且所有的点坐标的范围在[-10000, 10000]之间。

- 如[ [0,0] , [1,0] , [2,0] ]，则结果为2
- 两个结果为 [ [1,0], [0,0], [2,0] ] 和 [ [1,0], [2,0], [0,0] ]

>暴力解法：O(n^3)
三重循环枚举所有的可能性。

观察到 i 是一个“枢纽”，对于每个点i，遍历其余点到i的距离
O(n^2)

![查找表找出其他点到i的距离](http://upload-images.jianshu.io/upload_images/1779926-aa3289f858b3648e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

查找表找出其他点到i的距离

```c
//时间复杂度：O(n^2)
//空间复杂度：O(n)
class Solution {
public:
    int numberOfBoomerangs(vector<pair<int, int>>& points) {

        int res = 0;
        for( int i = 0 ; i < points.size() ; i ++ ){

            // record中存储 点i 到所有其他点的距离出现的频次
            unordered_map<int, int> record;
            for( int j = 0 ; j < points.size() ; j ++ )
                if( j != i )
                    record[dis(points[i], points[j])] += 1;

            for( unordered_map<int, int>::iterator iter = record.begin() ; iter != record.end() ; iter ++ )
                res += (iter->second)*(iter->second-1);
        }
        return res;
    }

private:
    int dis( const pair<int,int> &pa, const pair<int,int> &pb){
        return (pa.first - pb.first) * (pa.first - pb.first) +
               (pa.second - pb.second) * (pa.second - pb.second);
    }
};

int main() {

    vector<pair<int,int>> vec;
    vec.push_back( make_pair(0, 0) );
    vec.push_back( make_pair(1, 0) );
    vec.push_back( make_pair(2, 0) );

    cout<<Solution().numberOfBoomerangs( vec )<<endl;

    return 0;
}
```

**149. Max Points on a Line**

>给出2D平面上的n个点，求出最多有多少个点在一条直线上？

- 点坐标的范围
- 点坐标的表示（整数？浮点数？浮点误差？）

####滑动窗口 + 查找表

**219 Contains Duplicate II**

给出一个整形数组nums和一个整数k，是否存在索引i和j，使得nums[i] == nums[j] 且i和j之间的差不超过k

暴力解法：O(n^2)

![滑动窗口](http://upload-images.jianshu.io/upload_images/1779926-9adeb164e82770ec.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![范围](http://upload-images.jianshu.io/upload_images/1779926-32148a7794db14c4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

结合使用滑动窗口和查找表，不断查找当前滑动窗口内有没有重复值。

```c
// 时间复杂度: O(n)
// 空间复杂度: O(k)
class Solution {
public:
    bool containsNearbyDuplicate(vector<int>& nums, int k) {

        if( nums.size() <= 1 )
            return false;

        if( k <= 0 )
            return false;

        unordered_set<int> record;
        for( int i = 0 ; i < nums.size() ; i ++ ){

            if( record.find( nums[i] ) != record.end() )
                return true;

            record.insert( nums[i] );

            // 保持record中最多有k个元素
            // 因为在下一次循环中会添加一个新元素,使得总共考虑k+1个元素
            if( record.size() == k + 1 )
                //删除掉最左侧元素
                record.erase( nums[i-k] );
        }

        return false;
    }
};

int main() {

    int nums[] = {1,2,1};
    vector<int> vec(nums, nums + sizeof(nums)/sizeof(int));
    int k = 1;

    if( Solution().containsNearbyDuplicate(vec, k))
        cout<<"true"<<endl;
    else
        cout<<"false"<<endl;

    return 0;
}
```

**217. Contains Duplicate**

给出一个整形数组，若数组中存在相同的元素，则返回true，否则返回false.

####map有序性的使用

**220. Contains Duplicate III**

给出一个整形数组nums，是否存在索引i和j，使得nums[i]和nums[j]之间的差别不超过给定的整数t，且i和j之间的差别不超过给定的整数k。

![寻找满足的元素](http://upload-images.jianshu.io/upload_images/1779926-1db9e76d921709cd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![ceil & floor](http://upload-images.jianshu.io/upload_images/1779926-ecb130e76d6f1d22.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

lower_bound(v-t)

```c
// 时间复杂度: O(nlogn)
// 空间复杂度: O(k)
class Solution {
public:
    bool containsNearbyAlmostDuplicate(vector<int>& nums, int k, int t) {

        set<long long> record;
        for( int i = 0 ; i < nums.size() ; i ++ ){

            if( record.lower_bound( (long long)nums[i] - (long long)t ) != record.end() &&
                *record.lower_bound( (long long)nums[i] - (long long)t ) <= (long long)nums[i] + (long long)t )
                return true;

            record.insert( nums[i] );

            if( record.size() == k + 1 )
                record.erase( nums[i-k] );
        }

        return false;
    }
};

int main() {

    int nums[] = {-2147483648,-2147483647};
    vector<int> vec( nums, nums + sizeof(nums)/sizeof(int));
    int k = 3;
    int t = 3;

    if( Solution().containsNearbyAlmostDuplicate(vec, k, t) )
        cout<<"true"<<endl;
    else
        cout<<"false"<<endl;

    return 0;
}
```

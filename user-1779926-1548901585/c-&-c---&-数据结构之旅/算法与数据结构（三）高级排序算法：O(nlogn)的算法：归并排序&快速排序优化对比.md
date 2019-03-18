##O(nlogn)的算法

![o(nlogn)比O(n^2)快多少](http://upload-images.jianshu.io/upload_images/1779926-e48b86145c40a61d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

优化改进的算法要比笨的算法快太多。

###归并排序：Merge Sort

![把数组分成左右两半](http://upload-images.jianshu.io/upload_images/1779926-ef22be21ddb1a306.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![直到分成一个一个的为止：开始归并](http://upload-images.jianshu.io/upload_images/1779926-37d7992d810809a5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

然后从下向上逐层归并。8个元素每次2分，会被分成3级。（0/1/2/3）
log 以2为底的8. log(N)这个数量级的。向上取整。
如果每一层可以使用n的复杂度来拍好。那么这就是一个nlogn的算法。

两个已经排好自身顺序的数组，如何才能归并在一起
此时我们需要开辟一个同等大小的空间。

归并排序的缺点：多使用了存储的空间

![辅助空间](http://upload-images.jianshu.io/upload_images/1779926-70085df6e36fd01a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

使用三个索引在数组内进行追踪。

![三个追踪的指针](http://upload-images.jianshu.io/upload_images/1779926-bea657f040acbb54.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

蓝色箭头为最终顺序，红色箭头为对比项。2和1对比。1比2小。把1填入蓝色箭头所指向位置。

![已归并与最终指针后移](http://upload-images.jianshu.io/upload_images/1779926-813e5d45974e3a12.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

然后考虑2和4谁更小。蓝色箭头和更小数箭头一直向后后移。

>思想简单，就是两个排好序的数组当前头部不停比较，谁小谁上正式服。
实现中三个索引。i,j,k.
i,j 为当前两排好序的数组正指向的元素位置。k为可以放入正式排列的位置（下一个要放的位置）。

![i,j,k三个索引的位置](http://upload-images.jianshu.io/upload_images/1779926-6625e18149de542f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

前闭后闭 L(left) r(right) 第一个数组最后一个元素位置为m(middle)

####归并排序的代码实现。

```c
#include <iostream>
#include "SortTestHelper.h"
#include "InsertionSort.h"

using namespace std;


// 将arr[l...mid]和arr[mid+1...r]两部分进行归并
template<typename  T>
void __merge(T arr[], int l, int mid, int r){

    // 经测试,传递aux数组的性能效果并不好
    T aux[r-l+1];
    for( int i = l ; i <= r; i ++ )
        aux[i-l] = arr[i];//aux是从0开始的，而我们要处理的arr是从l开始的。
        //l数值的偏移量
    int i = l, j = mid+1;//i指向左边数组开头位置，j指向右边数组开头位置
    for( int k = l ; k <= r; k ++ ){

//        if (aux[i-l] < aux[j-l])
//        {
//            arr[k] = aux[i-l];
//            i++;
//        }

        if( i > mid )   { arr[k] = aux[j-l]; j ++;}
        else if( j > r ){ arr[k] = aux[i-l]; i ++;}
            //先判断索引的合法性。
        else if( aux[i-l] < aux[j-l] ){ arr[k] = aux[i-l]; i ++;}
        else                          { arr[k] = aux[j-l]; j ++;}
    }
}

// 递归使用归并排序,对arr[l...r]的范围进行排序：
// 前闭后闭。这里的r是最好一个元素的位置而不是最后一个元素后面的位置。（>=）
template<typename T>
void __mergeSort(T arr[], int l, int r){

    if( l >= r )
        return;

    int mid = (l+r)/2;//当l和r特别大可能会产生错误
    __mergeSort(arr, l, mid);//左边不断分割
    __mergeSort(arr, mid+1, r);//右边不断分割
    __merge(arr, l, mid, r);//最终归并
}

template<typename T>
void mergeSort(T arr[], int n){

    __mergeSort( arr , 0 , n-1 );
    //当前要处理数据段的开始位置和结束位置
    //下划线下划线开头表示私有。借鉴python。
}


int main() {

    int n = 50000;

    // 测试1 一般性测试
    cout<<"Test for Random Array, size = "<<n<<", random range [0, "<<n<<"]"<<endl;
    int* arr1 = SortTestHelper::generateRandomArray(n,0,n);
    int* arr2 = SortTestHelper::copyIntArray(arr1, n);

    SortTestHelper::testSort("Insertion Sort", insertionSort, arr1, n);
    SortTestHelper::testSort("Merge Sort",     mergeSort,     arr2, n);

    delete[] arr1;
    delete[] arr2;

    cout<<endl;


    // 测试2 测试近乎有序的数组
    int swapTimes = 100;
    cout<<"Test for Random Nearly Ordered Array, size = "<<n<<", swap time = "<<swapTimes<<endl;
    arr1 = SortTestHelper::generateNearlyOrderedArray(n,swapTimes);
    arr2 = SortTestHelper::copyIntArray(arr1, n);

    SortTestHelper::testSort("Insertion Sort", insertionSort, arr1, n);
    SortTestHelper::testSort("Merge Sort",     mergeSort,     arr2, n);

    delete(arr1);
    delete(arr2);

    return 0;
}
```

运行结果：

![运行结果](http://upload-images.jianshu.io/upload_images/1779926-52331b60544b5b56.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

归并算法总体快于插入算法。

####优化归并排序和插入排序。

改进情况，当第一个数组的最大值比第二个数组最小值大时才归并。
否则可以直接就是结果。

```c
    if( arr[mid] > arr[mid+1] )
        __merge(arr, l, mid, r);
```

递归到底时候的优化。对于数据规模小到一定程度使用插入。

```c
  // 对于小规模数组,使用插入排序
    if( r - l <= 15 ){
        insertionSort(arr, l, r);
        return;
    }
```

```c
//
// Created by liuyubobobo on 7/16/16.
//

#ifndef INC_03_MERGE_SORT_ADVANCE_INSERTIONSORT_H
#define INC_03_MERGE_SORT_ADVANCE_INSERTIONSORT_H

#include <iostream>
#include <algorithm>

using namespace std;


template<typename T>
void insertionSort(T arr[], int n){

    for( int i = 1 ; i < n ; i ++ ) {

        T e = arr[i];
        int j;
        for (j = i; j > 0 && arr[j-1] > e; j--)
            arr[j] = arr[j-1];
        arr[j] = e;
    }

    return;
}

// 对arr[l...r]范围的数组进行插入排序
template<typename T>
void insertionSort(T arr[], int l, int r){

    for( int i = l+1 ; i <= r ; i ++ ) {

        T e = arr[i];
        int j;
        for (j = i; j > l && arr[j-1] > e; j--)
            arr[j] = arr[j-1];
        arr[j] = e;
    }

    return;
}

#endif //INC_03_MERGE_SORT_ADVANCE_INSERTIONSORT_H

```

main.cpp:

```c
#include <iostream>
#include "SortTestHelper.h"
#include "InsertionSort.h"
#include "MergeSort.h"

using namespace std;


// 递归使用归并排序,对arr[l...r]的范围进行排序
template<typename T>
void __mergeSort2(T arr[], int l, int r){

    // 对于小规模数组,使用插入排序
    if( r - l <= 15 ){
        insertionSort(arr, l, r);
        return;
    }

    int mid = (l+r)/2;
    __mergeSort2(arr, l, mid);
    __mergeSort2(arr, mid+1, r);
    // 对于arr[mid] <= arr[mid+1]的情况,不进行merge
    // 对于近乎有序的数组非常有效,但是对于一般情况,有一定的性能损失
    if( arr[mid] > arr[mid+1] )
        __merge(arr, l, mid, r);
}

template<typename T>
void mergeSort2(T arr[], int n){

    __mergeSort2( arr , 0 , n-1 );
}


int main() {

    int n = 50000;

    // 测试1 一般性测试
    cout<<"Test for Random Array, size = "<<n<<", random range [0, "<<n<<"]"<<endl;
    int* arr1 = SortTestHelper::generateRandomArray(n,0,n);
    int* arr2 = SortTestHelper::copyIntArray(arr1, n);
    int* arr3 = SortTestHelper::copyIntArray(arr1, n);

    SortTestHelper::testSort("Insertion Sort", insertionSort, arr1, n);
    SortTestHelper::testSort("Merge Sort",     mergeSort,     arr2, n);
    SortTestHelper::testSort("Merge Sort 2",   mergeSort2,    arr3, n);

    delete[] arr1;
    delete[] arr2;
    delete[] arr3;

    cout<<endl;


    // 测试2 测试近乎有序的数组
    int swapTimes = 10;
    cout<<"Test for Random Nearly Ordered Array, size = "<<n<<", swap time = "<<swapTimes<<endl;
    arr1 = SortTestHelper::generateNearlyOrderedArray(n,swapTimes);
    arr2 = SortTestHelper::copyIntArray(arr1, n);
    arr3 = SortTestHelper::copyIntArray(arr1, n);

    SortTestHelper::testSort("Insertion Sort", insertionSort, arr1, n);
    SortTestHelper::testSort("Merge Sort",     mergeSort,     arr2, n);
    SortTestHelper::testSort("Merge Sort 2",   mergeSort2,    arr3, n);

    delete[] arr1;
    delete[] arr2;
    delete[] arr3;

    return 0;
}
```

运行结果：

![运行结果](http://upload-images.jianshu.io/upload_images/1779926-0670768eedb0dfb9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

使用递归实现自顶向下的归并排序

####自底向上归并排序MergeSort。

不需要递归。只需要迭代就可以完成。

![从底向上](http://upload-images.jianshu.io/upload_images/1779926-84f2119b41746a99.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2-4-8分段处理

代码：

自底向上的归并排序没有使用到数组的索引获取元素。所以对于链表也有好的适用效果。

```c
#include <iostream>
#include "SortTestHelper.h"
#include "MergeSort.h"


using namespace std;

template <typename T>
void mergeSortBU(T arr[], int n){

//    for( int sz = 1; sz <= n ; sz += sz )
    //每次加上自身也就是乘以2
//        for( int i = 0 ; i + sz< n ; i += sz+sz )
    //i的位置平移两个size。对0-size，size~2size-1
//            // 对 arr[i...i+sz-1] 和 arr[i+sz...i+2*sz-1] 进行归并
//            __merge(arr, i, i+sz-1, min(i+sz+sz-1,n-1) );

    // Merge Sort Bottom Up 优化
    for( int i = 0 ; i < n ; i += 16 )
        insertionSort(arr,i,min(i+15,n-1));

    for( int sz = 16; sz <= n ; sz += sz )
        for( int i = 0 ; i < n - sz ; i += sz+sz )
            if( arr[i+sz-1] > arr[i+sz] )
                __merge(arr, i, i+sz-1, min(i+sz+sz-1,n-1) );
}


int main() {

    int n = 100000;

    cout<<"Test for Random Array, size = "<<n<<", random range [0, "<<n<<"]"<<endl;
    int* arr1 = SortTestHelper::generateRandomArray(n,0,n);
    int* arr2 = SortTestHelper::copyIntArray(arr1, n);

    SortTestHelper::testSort("Merge Sort", mergeSort, arr1, n);
    SortTestHelper::testSort("Merge Sort Bottom Up", mergeSortBU, arr2, n);

    delete(arr1);
    delete(arr2);

    cout<<endl;


    // 测试2 测试近乎有序的数组
    int swapTimes = 100;
    cout<<"Test for Random Nearly Ordered Array, size = "<<n<<", swap time = "<<swapTimes<<endl;
    arr1 = SortTestHelper::generateNearlyOrderedArray(n,swapTimes);
    arr2 = SortTestHelper::copyIntArray(arr1, n);

    SortTestHelper::testSort("Merge Sort", mergeSort, arr1, n);
    SortTestHelper::testSort("Merge Sort Bottom Up", mergeSortBU, arr2, n);

    delete(arr1);
    delete(arr2);

    return 0;
}
```

运行结果：

![从顶向下的算法更优](http://upload-images.jianshu.io/upload_images/1779926-848fade7811580ea.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####快速排序

20世纪最伟大的算法之一。QuickSort（快速排序）

基本思想：

![以4为基点](http://upload-images.jianshu.io/upload_images/1779926-6200664e7aa58e1c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>以选定的元素为基点，把4挪到属于它的位置。然后就变成了4前面的都小于4,4后面的都大于4.

![4划分出的区间](http://upload-images.jianshu.io/upload_images/1779926-627c9ea1f067925f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

逐渐递归下去。

**重点:** 如何快速的把选定的元素挪到合适的位置。
该过程被称为Partition。

![partition](http://upload-images.jianshu.io/upload_images/1779926-35f72288ed06c827.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 一般以数组第一个元素作为分界点（L）。
- 逐渐整理<V & >v 分界点：j
- 当前访问的元素我们叫做i。

下面我们讨论i这个元素。e>v的那么这种情况很简单。直接融入>v的这部分。然后我们去讨论i++

![e>v](http://upload-images.jianshu.io/upload_images/1779926-576801638b245f08.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`当e<v时我们只需要把>v的第一个元素与e互换。`

![e<v](http://upload-images.jianshu.io/upload_images/1779926-b2120bd36910a308.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

j++。然后i++。考查下一个元素

最终数组被分为三部分。

![最终分布](http://upload-images.jianshu.io/upload_images/1779926-b5fc743e84104629.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

最后将l位置的元素与j位置元素进行交换。

![最终终极分布](http://upload-images.jianshu.io/upload_images/1779926-b4d3c491ecd101a2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####快速排序

递归方式.

```c
#include <iostream>
#include <algorithm>
#include "SortTestHelper.h"
#include "MergeSort.h"
#include "InsertionSort.h"

using namespace std;

// 对arr[l...r]部分进行partition操作
// 返回p,使得arr[l...p-1] < arr[p] ; arr[p+1...r] > arr[p]
template <typename T>
int __partition(T arr[], int l, int r){

    T v = arr[l];
    //前b后开。因为i这个元素就是我们当前要考察的元素
    int j = l; // arr[l+1...j] < v ; arr[j+1...i) > v
//    初始条件下j=1则此时区间为空，因为第0个为标准值
    //j +1 = 2 此时i指向1.
    for( int i = l + 1 ; i <= r ; i ++ )
        if( arr[i] < v ){
            j ++;
            swap( arr[j] , arr[i] );
        }

    swap( arr[l] , arr[j]);


    return j;
}

// 对arr[l...r]部分进行快速排序
template <typename T>
void __quickSort(T arr[], int l, int r){

    if( l >= r )
        return;
    //对l到r进行一个partition操作。将返回一个索引值
    int p = __partition(arr, l, r);
    __quickSort(arr, l, p-1 );
    __quickSort(arr, p+1, r);
}

template <typename T>
void quickSort(T arr[], int n){

    __quickSort(arr, 0, n-1);
}


int main() {

    int n = 1000000;

    cout<<"Test for Random Array, size = "<<n<<", random range [0, "<<n<<"]"<<endl;
    int* arr1 = SortTestHelper::generateRandomArray(n,0,n);
    int* arr2 = SortTestHelper::copyIntArray(arr1,n);

    SortTestHelper::testSort("Merge Sort", mergeSort, arr1, n);
    SortTestHelper::testSort("Quick Sort", quickSort, arr2, n);

    delete[] arr1;
    delete[] arr2;

    return 0;
}
```

运行结果：

![快排与归并](http://upload-images.jianshu.io/upload_images/1779926-9eb1a31bbfdcf105.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

快速排序算法的优化：

```c
void _quickSort(T arr[], int l, int r){

//    if( l >= r )
//        return;
    if( r - l <= 15 ){
        insertionSort(arr,l,r);
        return;
    }

    int p = _partition(arr, l, r);
    _quickSort(arr, l, p-1 );
    _quickSort(arr, p+1, r);
}
```


>快速排序的重要优化。对于几乎有序的数组。

归并排序

![logn层，每层n](http://upload-images.jianshu.io/upload_images/1779926-2bf02c01cc345373.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![不稳定](http://upload-images.jianshu.io/upload_images/1779926-d3a5764b997abcd5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

他的层数不一定就是logn。因为很大可能分布不均匀。

快速排序最差情况（整个数组近乎有序）——退化为O(n^2)

![整棵树高度为n，每层处理n](http://upload-images.jianshu.io/upload_images/1779926-a12fe567a824281b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


尽可能想选一个靠近中间的，那么当我们随机选取一个而不是取第一个。那么整体的期望是nlogn

随机选取时-我们获得最坏结果的可能性非常小：得每一层都随机到最小的。

```c
void quickSort(T arr[], int n){

//    srand(time(NULL));//新增的随机化种子
    _quickSort(arr, 0, n-1);
}

int _partition(T arr[], int l, int r){

    swap( arr[l] , arr[rand()%(r-l+1)+l] );
//随机选择一个元素放在原1位置
    T v = arr[l];
    int j = l;
    for( int i = l + 1 ; i <= r ; i ++ )
        if( arr[i] < v ){
            j ++;
            swap( arr[j] , arr[i] );
        }

    swap( arr[l] , arr[j]);

    return j;
}
```

此时我们的快速排序算法已经成为一个随机算法，最坏的情况仍然是O(n^2)
但是期望也就是平均情况已经变成了O(nlogn)

####对于大量重复值的优化。

![判断e是大于v还是小于v](http://upload-images.jianshu.io/upload_images/1779926-e1967285e85decc1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>我们的算法是对于每个e进行>v小于v的判定。没有讨论e=v的情况。

![我们将=v放入大于或小于都会引起不平衡](http://upload-images.jianshu.io/upload_images/1779926-8a3b7f39d0e4cb91.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

放左放右都不好，我们从两边开始判定。比较均匀

![>v放一边。`<v`放一边。`=v`放中间](http://upload-images.jianshu.io/upload_images/1779926-5ddeb6fd54284871.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![直到遇到e>v.ij互换](http://upload-images.jianshu.io/upload_images/1779926-e343b38e22de4a73.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

然后i,j互换

![下一步i,j互换](http://upload-images.jianshu.io/upload_images/1779926-313687da53ebdd72.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>最后直到i和j重合。

```c
int _partition2(T arr[], int l, int r){

    swap( arr[l] , arr[rand()%(r-l+1)+l] );
    T v = arr[l];

    // arr[l+1...i) <= v; arr(j...r] >= v
    int i = l+1, j = r;
    while( true ){
        while( i <= r && arr[i] < v )
            i ++;
    //i从左往右扫描
        while( j >= l+1 && arr[j] > v )
            j --;
    //j从右往左扫描

        if( i > j )
            break;
    //循环结束条件
        swap( arr[i] , arr[j] );
        i ++;
        j --;
    }

    swap( arr[l] , arr[j]);

    return j;
}
```

运行结果：

![运行结果](http://upload-images.jianshu.io/upload_images/1779926-4d426fb659f5cf8f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>通过对于大量重复值的数据，我们的快速排序也可以得到比较好的结果。

####三路快速排序

将整个数组分为三部分：>v & < v & =v

![三部分：三路](http://upload-images.jianshu.io/upload_images/1779926-b06bafc49d3ccdbb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

对于当前元素正好等于v。直接合并进==v的蓝色部分。i++.

![e=v](http://upload-images.jianshu.io/upload_images/1779926-e6b22621fdb0a5ff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

对于`e<v`的情况：和等于v的第一个元素进行互换：lt++和i++

![e<v](http://upload-images.jianshu.io/upload_images/1779926-f6ffda6c14672409.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

对于e>v的情况。和gt-1的位置互换。gt-1 i不用变化,仍处于原位置。指向刚换过来的。

![e>v](http://upload-images.jianshu.io/upload_images/1779926-94781767c4fda1dc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

最终示例：

![最终示例](http://upload-images.jianshu.io/upload_images/1779926-e59ce14af952bd19.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

i和gt重合为终止条件。

![l和lt交换位置真正三部分](http://upload-images.jianshu.io/upload_images/1779926-f2ad4fde5a131175.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

对于>v,`<v`进行处理。而=v已经进入正常位置。

```c
template <typename T>
void __quickSort3Ways(T arr[], int l, int r){
    // 对于小规模数组, 使用插入排序进行优化
    if( r - l <= 15 ){
        insertionSort(arr,l,r);
        return;
    }
    //partition
    swap( arr[l], arr[rand()%(r-l+1)+l ] );

    T v = arr[l];

    int lt = l;     // arr[l+1...lt] < v
    int gt = r + 1; // arr[gt...r] > v
    int i = l+1;    // arr[lt+1...i) == v
    //i是正在考察的元素不放入区间内
    //终止条件：i<gt
    while( i < gt ){
        //三种情况
        if( arr[i] < v ){
            swap( arr[i], arr[lt+1]);
            i ++;
            lt ++;
        }
        else if( arr[i] > v ){
            swap( arr[i], arr[gt-1]);
            gt --;
        }
        else{ // arr[i] == v
            i ++;
        }
    }

    swap( arr[l] , arr[lt] );

    __quickSort3Ways(arr, l, lt-1);
    __quickSort3Ways(arr, gt, r);
}

template <typename T>
void quickSort3Ways(T arr[], int n){

    srand(time(NULL));
    __quickSort3Ways( arr, 0, n-1);
}
```

运行结果：


![运行结果](http://upload-images.jianshu.io/upload_images/1779926-40f01a4075c75e45.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>可以看出三路的快速排序在最后一种大量重复值的情况下是要好于合并与前面简单从两头扫描的排序的。

####Merge Sort 和 Quick Sort 的衍生问题

Merge Sort 和 Quick Sort 都使用了分治算法。

分治算法：

>顾名思义，分而治之，就是将原问题，分割成同等结构的子问题，之后将子问题逐一解决后，原问题也就得到了解决

归并排序：没有做太多考虑，一刀切。分完之后归并起来。

快速排序：我们费很大功夫在分上。合的时候不用做很多工作。

经典算法实现 与 分治算法。

####求一个数组中逆序对的数量。


![2&3顺序对。2&1逆序对](http://upload-images.jianshu.io/upload_images/1779926-45ce6540219c4668.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

逆序对数量可以用来衡量数组的有序程度。比如对于完全有序的数组，逆序对数量为0.

暴力解法：考察每一个数对。使用双重循环，来比较每一个数对+1.

而归并排序的思路求逆序对的个数，可以达到O(nlogn)

关键在归并的过程。1比2小。

![1比2小那么有4个数比1大还在1前面，逆序数+4](http://upload-images.jianshu.io/upload_images/1779926-3ce1cec6148a443a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2比4小。组成顺序对，不管。 当4比6小。则4与6/8构成逆序，逆序数+2.
以此类推。不考虑一个一个，而是直接考虑一组一组的数值。

####取数组中第m大的元素

取数组中的最大值，最小值。

>遍历。算法复杂度：O(n)

取数组第n大的元素。

>排序后，根据索引取值。算法复杂度:O(nlogn)

quick sort的思路来实现O(n)级别的算法。

![快速排序标定点](http://upload-images.jianshu.io/upload_images/1779926-c87f7760ce52b607.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

合适的位置。4也就是第四名。

算法复杂度：

![等比数列求和](http://upload-images.jianshu.io/upload_images/1779926-e474b883a9a52f11.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

O(2n).

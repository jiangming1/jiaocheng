#排序基础
>O(n^2)的算法虽然简单，但也实用！让我们从最简单的基础排序算法开始，打开我们的算法大门！

##排序算法

###O(n^2)的排序算法

最优的时间复杂度为O(nlogn)的算法。

为什么要学习O(n^2)的排序算法。

>- 基础解法。先通过简单解法找思路
- 编码简单，易于实现，是一些简单 场景的首选
- 在特殊情况下，简单排序算法更有效
- 简单的排序算法思想能衍生出复杂的排序算法（希尔排序：插入排序的优化）
- 作为子过程，改进更复杂的排序算法

####选择排序（selection sort)

>在整个数组中找到应该第一名的数和目前位于第一名的数互换。

![互换前](http://upload-images.jianshu.io/upload_images/1779926-1535596d88b267ad.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![互换后](http://upload-images.jianshu.io/upload_images/1779926-4831d79119e41eee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

再找到应该第二名的数和目前位于第二名的互换。

![2互换后](http://upload-images.jianshu.io/upload_images/1779926-c78fe65ef41a3e92.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

再找到应该第几名的数和目前位于第几名的互换
七和七，八和八。自身换。

![互换](http://upload-images.jianshu.io/upload_images/1779926-68d96925c28c8bbf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**具体代码**

```c
#include <iostream>
#include <algorithm>
//老的标准里swap.新的c++11就在std中。

using namespace std;

void selectionSort(int arr[], int n){

    for(int i = 0 ; i < n ; i ++){
        // 寻找[i, n)区间里的最小值
        int minIndex = i;
        //存放当前最小值的索引
        for( int j = i + 1 ; j < n ; j ++ )
            if( arr[j] < arr[minIndex] )
                minIndex = j;

        swap( arr[i] , arr[minIndex] );//标准库函数
    }

}

int main() {

    int a[10] = {10,9,8,7,6,5,4,3,2,1};
    selectionSort(a,10);
    for( int i = 0 ; i < 10 ; i ++ )
        cout<<a[i]<<" ";
    cout<<endl;

    return 0;
}
```

运行结果：

![运行结果](http://upload-images.jianshu.io/upload_images/1779926-acfbae2d88d973b8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**使用模板函数增加更多类型支持**

main.cpp:

```c
#include <iostream>
#include <algorithm>
#include <string>
#include "Student.h"

using namespace std;

template<typename T>//声明函数为模板函数。类型T
void selectionSort(T arr[], int n){

    for(int i = 0 ; i < n ; i ++){

        int minIndex = i;
        for( int j = i + 1 ; j < n ; j ++ )
            if( arr[j] < arr[minIndex] )
                minIndex = j;

        swap( arr[i] , arr[minIndex] );
    }
}

int main() {

    // 测试模板函数，传入整型数组
    int a[10] = {10,9,8,7,6,5,4,3,2,1};
    selectionSort( a , 10 );
    for( int i = 0 ; i < 10 ; i ++ )
        cout<<a[i]<<" ";
    cout<<endl;

    // 测试模板函数，传入浮点数数组
    // float b[4] = {4.4,3.3,2.2,1.1};
    //double到float会被截断。
//    double b[4] = {4.4,3.3,2.2,1.1};
//    selectionSort(b,4);
//    for (int j = 0; j < 4 ; ++j) {
//        cout << b[j] <<" ";
//    }
//    cout<<endl;

    // 测试模板函数，传入字符串数组
    string c[4] = {"D","C","B","A"};
    selectionSort(c,4);
    for( int i = 0 ; i < 4 ; i ++ )
        cout<<c[i]<<" ";
    cout<<endl;

    // 测试模板函数，传入自定义结构体Student数组
    Student d[4] = { {"D",90} , {"C",100} , {"B",95} , {"A",95} };
    selectionSort(d,4);
    for( int i = 0 ; i < 4 ; i ++ )
        cout<<d[i];
    cout<<endl;

    return 0;
}
```

student.h:

```c
#ifndef INC_02_SELECTION_SORT_USING_TEMPLATE_STUDENT_H
#define INC_02_SELECTION_SORT_USING_TEMPLATE_STUDENT_H

#include <iostream>
#include <string>

using namespace std;

struct Student{

    string name;
    int score;

    bool operator<(const Student& otherStudent){
        return score != otherStudent.score ?
               score > otherStudent.score : name < otherStudent.name;
        //小和大自己来定义。
        // 成绩是否等于传入的成绩。如果不等于那么将返回是否大于传入成绩的bool值
        //如果等于，那么将返回名字是否小于的bool值
    }

    friend ostream& operator<<(ostream &os, const Student &student){

        os<<"Student: "<<student.name<<" "<<student.score<<endl;
        return os;
    }
};

#endif //INC_02_SELECTION_SORT_USING_TEMPLATE_STUDENT_H

```

运行结果：

![运行结果](http://upload-images.jianshu.io/upload_images/1779926-22fd79af9e95d2bd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####使用辅助函数生成测试用例

```c
#include <iostream>
#include "SortTestHelper.h"

using namespace std;

template<typename T>
void selectionSort(T arr[], int n){

    for(int i = 0 ; i < n ; i ++){

        int minIndex = i;
        for( int j = i + 1 ; j < n ; j ++ )
            if( arr[j] < arr[minIndex] )
                minIndex = j;

        swap( arr[i] , arr[minIndex] );
    }
}

int main() {

    // 测试排序算法辅助函数
    int N = 10000;
    int *arr = SortTestHelper::generateRandomArray(N,0,N);
    selectionSort(arr,N);
    SortTestHelper::printArray(arr,N);
    delete[] arr;

    return 0;
}
```

SortTestHelper.h

```c
#ifndef INC_03_SELECTION_SORT_GENERATE_TEST_CASES_SORTTESTHELPER_H
#define INC_03_SELECTION_SORT_GENERATE_TEST_CASES_SORTTESTHELPER_H

#include <iostream>
#include <ctime>
#include <cassert>

using namespace std;


namespace SortTestHelper {

    // 返回一个生成有n个元素的随机数组,每个元素的随机范围为[rangeL, rangeR]
    //返回指向数组第一个元素的指针
    int *generateRandomArray(int n, int rangeL, int rangeR) {

        assert(rangeL <= rangeR);//assert函数限制L要小于等于R.include<cassert>

        int *arr = new int[n];

        srand(time(NULL));//随机种子的设置。include <ctime>
        for (int i = 0; i < n; i++)
            arr[i] = rand() % (rangeR - rangeL + 1) + rangeL;
            //rand()返回随机整数。随机整数求余范围长度（没有加之前是0-长度范围内值） + rangL的偏移
        return arr;
    }

    template<typename T>
    void printArray(T arr[], int n) {

        for (int i = 0; i < n; i++)
            cout << arr[i] << " ";
        cout << endl;

        return;
    }

};
#endif //INC_03_SELECTION_SORT_GENERATE_TEST_CASES_SORTTESTHELPER_H

```

![排序结果](http://upload-images.jianshu.io/upload_images/1779926-76c2025d03bae5ca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####算法运行时间测试函数

main.cpp:

```c
#include <iostream>
#include "SortTestHelper.h"

using namespace std;

template<typename T>
void selectionSort(T arr[], int n){

    for(int i = 0 ; i < n ; i ++){

        int minIndex = i;
        for( int j = i + 1 ; j < n ; j ++ )
            if( arr[j] < arr[minIndex] )
                minIndex = j;

        swap( arr[i] , arr[minIndex] );
    }
}

int main() {

    int n = 10000;
    int *arr = SortTestHelper::generateRandomArray(n,0,n);
    SortTestHelper::testSort("Selection Sort", selectionSort, arr, n);
    delete[] arr;

    return 0;
}
```

sorthelper.h

```c
#ifndef INC_03_SELECTION_SORT_DETECT_PERFORMANCE_SORTTESTHELPER_H
#define INC_03_SELECTION_SORT_DETECT_PERFORMANCE_SORTTESTHELPER_H

#include <iostream>
#include <ctime>
#include <cassert>
#include <string>

using namespace std;


namespace SortTestHelper {

    // 生成有n个元素的随机数组,每个元素的随机范围为[rangeL, rangeR]
    int *generateRandomArray(int n, int rangeL, int rangeR) {

        assert(rangeL <= rangeR);

        int *arr = new int[n];

        srand(time(NULL));
        for (int i = 0; i < n; i++)
            arr[i] = rand() % (rangeR - rangeL + 1) + rangeL;
        return arr;
    }

    template<typename T>
    void printArray(T arr[], int n) {

        for (int i = 0; i < n; i++)
            cout << arr[i] << " ";
        cout << endl;

        return;
    }


// 测试数组是否有序
    template<typename T>
    bool isSorted(T arr[], int n) {

        for (int i = 0; i < n - 1; i++)
            if (arr[i] > arr[i + 1])
                return false;

        return true;
    }

    template<typename T>
    //传入参数：1.排序算法名称。2.函数指针。要写明函数的返回类型，需要传入的参数。
    //3.传入测试用例4.数组元素个数
    void testSort(const string &sortName, void (*sort)(T[], int), T arr[], int n) {

        clock_t startTime = clock();//开始时间.include<ctime>
        sort(arr, n);
        clock_t endTime = clock();

//        assert(isSorted(arr, n));//限定arr是否真的有序了。如果排序不正确会自动中断
        cout << sortName << " : " << double(endTime - startTime) / CLOCKS_PER_SEC << " s" << endl;
        //CLOCKS_PER_SEC标准库中宏定义。每秒钟所运行的时钟周期。

        return;
    }

};
#endif //INC_03_SELECTION_SORT_DETECT_PERFORMANCE_SORTTESTHELPER_H

```

####插入排序

>类似于生活中整理扑克牌。

![插入排序](http://upload-images.jianshu.io/upload_images/1779926-3f5f72685b539018.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

看后面的牌，把它插入合适的位置。直到最后一张牌放在合适位置。手里的牌就完成了。

![原数组](http://upload-images.jianshu.io/upload_images/1779926-c578c2a7f577a72e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

对于8来说不用动。对于6来说应该放到8前面去。
2和8比应该放到8前面。

![2的第一次互换](http://upload-images.jianshu.io/upload_images/1779926-7bf69a282f9b9afd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2和6相比应该放到6前面：

![2的第二次互换](http://upload-images.jianshu.io/upload_images/1779926-bd0c1a7a83460174.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3要和8,6依次换位置，直到前面遇到2停止。

SortTestHelper.h:

```c
#ifndef INC_04_INSERTION_SORT_SORTTESTHELPER_H
#define INC_04_INSERTION_SORT_SORTTESTHELPER_H

#include <iostream>
#include <algorithm>
#include <string>
#include <ctime>
#include <cassert>

using namespace std;


namespace SortTestHelper {

    int *generateRandomArray(int n, int range_l, int range_r) {

        int *arr = new int[n];

        srand(time(NULL));
        for (int i = 0; i < n; i++)
            arr[i] = rand() % (range_r - range_l + 1) + range_l;
        return arr;
    }

    int *generateNearlyOrderedArray(int n, int swapTimes){

        int *arr = new int[n];
        for(int i = 0 ; i < n ; i ++ )
            arr[i] = i;

        srand(time(NULL));
        for( int i = 0 ; i < swapTimes ; i ++ ){
            int posx = rand()%n;
            int posy = rand()%n;
            swap( arr[posx] , arr[posy] );
        }

        return arr;
    }

    int *copyIntArray(int a[], int n){

        int *arr = new int[n];
        copy(a, a+n, arr);//std中的copy函数（1.原数组头指针2.原数组尾指针3.要拷贝到目的地址的头指针）
        return arr;
    }

    template<typename T>
    void printArray(T arr[], int n) {

        for (int i = 0; i < n; i++)
            cout << arr[i] << " ";
        cout << endl;

        return;
    }

    template<typename T>
    bool isSorted(T arr[], int n) {

        for (int i = 0; i < n - 1; i++)
            if (arr[i] > arr[i + 1])
                return false;

        return true;
    }

    template<typename T>
    void testSort(const string &sortName, void (*sort)(T[], int), T arr[], int n) {

        clock_t startTime = clock();
        sort(arr, n);
        clock_t endTime = clock();
        cout << sortName << " : " << double(endTime - startTime) / CLOCKS_PER_SEC << " s"<<endl;

        assert(isSorted(arr, n));

        return;
    }

};

#endif //INC_04_INSERTION_SORT_SORTTESTHELPER_H

```

main.cpp:

```c
#include <iostream>
#include <algorithm>
#include "SortTestHelper.h"
#include "SelectionSort.h"

using namespace std;

template<typename T>
void insertionSort(T arr[], int n){
    //i从1开始是因为第0个元素可以不用考虑
    for( int i = 1 ; i < n ; i ++ ) {

        // 寻找元素arr[i]合适的插入位置
        // 写法1（向前找）
//        最多考查到j=1
//        for( int j = i ; j > 0 ; j-- )
//            if( arr[j] < arr[j-1] )
//                swap( arr[j] , arr[j-1] );
//            else
//                break;

        // 写法2
        for( int j = i ; j > 0 && arr[j] < arr[j-1] ; j -- )
            swap( arr[j] , arr[j-1] );

    }

    return;
}

int main() {

    int n = 10000;
    cout<<"Test for Random Array, size = "<<n<<", random range [0, "<<n<<"]"<<endl;
    int *arr1 = SortTestHelper::generateRandomArray(n,0,n);
    int *arr2 = SortTestHelper::copyIntArray(arr1, n);

    //插入排序理论上更快一些，因为不像选择排序从头到尾扫描。
    SortTestHelper::testSort("Insertion Sort", insertionSort,arr1,n);
    SortTestHelper::testSort("Selection Sort", selectionSort,arr2,n);

    delete[] arr1;
    delete[] arr2;

    cout<<endl;

    return 0;
}
```

selectionSort.h

```c
#ifndef INC_04_INSERTION_SORT_SELECTIONSORT_H
#define INC_04_INSERTION_SORT_SELECTIONSORT_H

#include <iostream>
#include <algorithm>

using namespace std;


template<typename T>
void selectionSort(T arr[], int n){

    for(int i = 0 ; i < n ; i ++){

        int minIndex = i;
        for( int j = i + 1 ; j < n ; j ++ )
            if( arr[j] < arr[minIndex] )
                minIndex = j;

        swap( arr[i] , arr[minIndex] );
    }
}

#endif //INC_04_INSERTION_SORT_SELECTIONSORT_H

```

运行结果：

![运行结果](http://upload-images.jianshu.io/upload_images/1779926-41cf500f3dd51a73.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>根据理论推测我们认为插入排序因为会提前中止循环，不是像选择一样，每一遍都扫描全部，所以应该快一点。但是根据实际的结果发现并不是这样。选择排序更快。

####优化插入排序 & 性质

```c
   for( int j = i ; j > 0 && arr[j] < arr[j-1] ; j -- )
            swap( arr[j] , arr[j-1] );
```

在遍历的同时也在不停的交换。交换是要比简单的比较还要耗时的。
会有三次赋值。对于数组还有访问数组相应元素的时间。

改进让他在内层循环只交换一次。

![复制副本不轻易交换](http://upload-images.jianshu.io/upload_images/1779926-37100bcd55ef3541.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>更加真实的扑克特效，复制一份6出来，不与前一个元素不停互换。而是直到找到该它存放的位置在插入。就好像扑克抽出来，直接插入该插的位置。

![2的插入](http://upload-images.jianshu.io/upload_images/1779926-ee20428fdd924a03.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

把2拷贝一个副本：根前面的8比较，2比8小，则把原来2的位置赋值8.然后开始比较2与6的大小。2比6还小。原来8的位置赋值6.第0个位置了，直接赋值。

3-保存副本。然后3的位置赋值8,8的位置赋值6.6的位置发现3不再小于前面数。第一个6的位置赋值3.

![第一个6位置赋值3](http://upload-images.jianshu.io/upload_images/1779926-09ecb470fbde4d3c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

一次交换就是三次赋值。把交换用赋值取代

**插入排序在几乎有序的情况下甚至比nlogn算法效果还好**
系统日志的某几个无序日志。

如果是一个完全有序的数组。插入排序变成O(n)级别的算法。
发现就是合适位置，直接进入下一层循环。

####选择排序 & 插入排序
选择排序

>简单：缺点明显。两层循环。每一层循环都得完全执行完成。

插入排序

>最差情况也是O(n^2).但是数组近乎有序，插入排序效率相当高。

####冒泡排序（Bubble Sort）

理解过程。优化冒泡排序。适用情况。

希尔排序（shell sort） 就是插入排序整体思路的延伸。

插入每次和之前一个元素进行比较。而shell排序和之前h个元素进行比较。
当h递减到1.

O(n^3/2).实现相对简单。最优O(nlogn)

优化分析理解算法。

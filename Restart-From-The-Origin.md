---
title: Restart From The Origin
date: 2017-09-16 07:23:32
categories : 编程
tags: [basic programming,java]

---

> 2017年9月15日，参加华为面试。 本科生的项目的确太水，没有内涵，是一味的形式化。没有任何的思想内涵和技巧在其中。本人痛定思痛重新开启了基础知识巩固。

# 排序

## 内部排序

### 选择排序

常用的选择排序有两种：直接选择排序和堆排序。直接选择排序简单直观，但性能略差；堆排序是一种较为高效的选择排序方法，但实现起来略微复杂

#### 直接选择

思路较为清晰：n个数字进行排序，需要执行 n-1 次循环

第一次循环比较：将记录定位在第一个数据上，使用第一个数据依次和它后面每个数字进行比较。如果第一个数据==大于==后面某个数据，则将其交换，依次类推。经过di第一轮循环，这组数中最小的数据被选出，被排放在第一位。

第二次循环比较：将记录定位在第二个数据上，使用第二个数据依次和它后面每个数字进行比较。如果第二个数据==大于==后面某个数据，则将其交换，依次类推。经过第二轮循环，这组数中第二小的数据被选出，被排放在第二位。

……

按上述规则进行 ==n-1== 次循环比较，这组数据中第 n-1 小(第二大) 的数据被选出，并被排在第 n-1 位；剩余即为最大的数据，被排在最后

**算法1.10**

```java
    public static void selectSort(int[] unsorted){
        for(int i=0;i<unsorted.length-1;i++){
            for(int j=i+1;j<unsorted.length;j++){
                if(unsorted[i]>unsorted[j]){
                    //交换
                    int temp = unsorted[j];
                    unsorted[j] = unsorted[i];
                    unsorted[i] = temp;
                }
            }
        }
    }
```

问题：在每次循环比较中，程序一旦发现某个数据比第一位小，就立即交换它们。这保证了每次循环比较中，第一位数据永远是最小的，但是这增加了交换的次数，导致算法效率降低。

对上面的直接选择排序算法进行改进

**算法 1.11**

```java
    public static void improvedSelectSort(int[] unsorted){
        for(int i=0;i<unsorted.length-1;i++){
            int minIndex = i;//最小索引
            for(int j=i+1;j<unsorted.length;j++){
                if(unsorted[minIndex]>unsorted[j]){
                    minIndex = j;
                }
            }
            if(minIndex!=i){
                int temp = unsorted[i];
                unsorted[i] = unsorted[minIndex];
                unsorted[minIndex] = temp;
            }
        }
    }
```

在改进后的排序算法中，每次循环比较的目的只是找出本次循环中最小数据的索引。

对于直接选择排序算法而言，假设有 n 个数据，数据交换的次数最多有 n-1 次，但程序比较次数较多。时间复杂度 : **O(n^2^)**

直接选择排序算法空间效率很高，仅一个附加程序单元用于交换，空间复杂度 : **O(1)**

#### 堆排序

### 交换排序

#### 冒泡排序



**算法 2.10**

```java
   public static void bubbleSort(int[] unsorted){
        for(int i=0;i<unsorted.length-1;i++){
            boolean flag = false;
            for(int j=0;j<unsorted.length;j++){
                if(unsorted[j]>unsorted[j+1]){
                    int temp = unsorted[j];
                    unsorted[j] = unsorted[j+1];
                    unsorted[j+1] = temp;
                    flag = true;
                }
            }
            //如果某次循环未发生交换则数组已有序
            if(!flag)break;
        }
    }
```



#### 快速排序

一个速度非常快的交换排序算法，基本思路：从待排序的数据序列中任取一个数据作为分界值，所有比它小的数据元素一律放在左边，所有比它大的数据元素一律放在右边。一趟遍历，该序列形成左、右两个子序列，左边序列中数据元素的值逗比分界值小，右边序列中数据元素的值都比分界值大。

然后对左、右两个子序列进行递归排序，对两个子序列重新选择中心元素并依此规则调整，直到每个子序列元素只剩一个，排序完成。

1. 定义一个 i 变量，i 变量从左边第一个索引开始，找大于分界值的元素的索引，并用 i 来记录它。
2. 定义一个 j 变量，j 变量从右边第一个索引开始，找小于分界值的元素的索引，并用 j 来记录它。
3. 如果 i < j ，交换 i , j 索引处的元素

**算法 3.10**

```java
    public static void subSort(int unsorted[],int start,int end){
        if(start<end){
            //以第一个元素作为分界值
            int mid = unsorted[start];
            //i从左边开始搜索，搜索大于分界值的元素
            int leftCur = start;
            //rightCur从右边开始搜索，搜索小于分界值的元素
            int rightCur = end+1;
            while(true){
                //找到大于分界值的元素的索引，或leftCur已经到了end处
                while(leftCur<end&&unsorted[++leftCur]<mid);
                //找到小于分界值元素的索引，或rightCur已经到了start处
                while(rightCur>start&&unsorted[--rightCur]>mid);
                if(leftCur<rightCur){
                    int temp = unsorted[leftCur];
                    unsorted[leftCur] = unsorted[rightCur];
                    unsorted[rightCur] = unsorted[leftCur];
                }
                else{
                    break;
                }
            }
          	//交换 分界 数 与 j的位置
          	int temp = unsorted[j];
          	unsorted[j] = unsorted[start];
          	unsorted[start] = temp;
          
          	//递归左边子序列
          	subSort(unsorted,start,j);
          	//递归右边子序列
          	subSort(unsorted,j+1,end);5
          
        }
    }
```



### 插入排序

非常常见的排序算法，包含直接插入排序，Shell排序和折半插入排序

#### 直接插入

思路：依次将待排序的数据元素按其关键字值的大小插入前面的有序序列。插入排序由N-1趟排序组成。对于 p=1 到 N-1 趟，插入排序保证从位置 0 到 位置 p 上的元素为已排序状态。

```java
    /**
     * Simple insertion sort.
     * @param a     an array of Comparable items.
     * @param <AnyType>
     */
    public static <AnyType extends Comparable<? super AnyType>> void insertionSort(AnyType []a){
        int j;
        //对从 1--a.length 中每一项进行插入，
        for(int p=1;p<a.length;p++){
            AnyType tmp = a[p];
            //为a[p]寻找合适位置: 从a[p]开始往前搜索，比较如果小于则往前搜索，大于赋值
            for(j=p;j>0&&tmp.compareTo(a[j-1])<0;j--)a[j] = a[j-1];
            a[j] = tmp;
        }
    }

```



#### 折半插入

##### 折半查找

```java
int a[N];
int low=0;
int high=length;
int mid=0;
while(low<=high){
	mid=(low+high)/2;
  if(a[mid]==x) break;
  else if(a[mid]>x)high=mid;
  else low=mid
}
```



#### Shell排序

### 归并排序

### 筒式排序

### 基数排序



## 外部排序

# 树




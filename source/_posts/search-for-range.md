---
title: search_for_range
date: 2016-04-20 15:36:06
tags: 算法_二分查找
---
### 二分查找是必须要掌握的技能
+ 可以用它来查找某一个数
+ 可以用于查找某一个范围。如《二分查找有序数组中某个数的所在范围 Search for a Range》
+ 可以用它来查找两个有序数组的中位数。
+ 本文中，二分查找又多了一项新的本领，可以用它在循环有序数组中查找某个数。
————————

循环有序数组：
指的是，将一个有序数组循环左/右移动若干距离之后变成的数组。如，[1,2,3,4,5]循环右移3位，就成为[4,5,1,2,3]。该数组的特点是，其中包含着一个转折点。转折点左右两侧的子数组都是有序的，并且左侧的子数组整体都比右侧的子数组大。


查找的方法：
<strong>不要想着定位到转折点,只需要知道转折点在中间点的哪一侧就行</strong>
> A[left]<=A[mid],那么转折点一定不在转折点左侧，意味着从left到mid的整个左半部分是严格递增的，因此可以判断key是否在左半部分。
> 如果A[left]>A[mid] 那么A[mid] 一定在转折点的左侧，意味着从 mid到right的整个右半部分是严格递增的，因此可以判断key是否在右半部分。


第一种方法（数组中不包含重复元素）
```cpp
class Solution
{
  public:
  search(int [] A,int n,int target)
  {
      if(n<=0)
         return -1;
      int left=0, right=n-1;
      while(left<=right)
      {
           int mid=(left++right)/2;
           if(A[mid]==target) return mid;
           
           if(A[left]<=A[mid]) //转折点在右半部分
           {
                if(A[left]<=target&&target<A[mid])
                    right=mid-1;
                else 
                   left=mid+1;
           }
         else{ //转折点在左半部分
               if(A[mid]<target &&target<=A[right])
                 left=mid+1;
                else  right=mid-1;
           
           }
      }
          return -1;
  }

  
};
```
第二种 （数组中可能出现重复的元素）
```cpp
class Solution{
  public:
   bool search(int A[],int n,int target)
   {
      return bisearch(A,0,n-1target);
   }
   bool bisearch(int A[],int left,int right,int target)
   {
       if(left>right)
           return false;
       int mid=(left+right)/2;
       if(target==A[mid])
           return true;
       if(A[mid]>A[left]&&A[mid]<A[right])
       {
           if(target<A[mid])
             return bisearch(A,left,mid-1,target);
           else
             return bisearch(A,mid+1,right,target);
       }
       else if(A[mid]<A[left]&&A[mid]<A[right]) //转折在左侧
       {
            if(target>A[mid]&&target<=A[right])
               return bisearch(A,mid+1,right,target);
             else return bisearch(A,left,mid-1,target);
       }
       // 3 5 1  
            else if(A[mid] > A[left] && A[mid] > A[right]) //转折在右侧  
            {  
                if(target < A[mid] && target >= A[left])  
                    return bisearch(A, left, mid-1, target);  
                else  
                    return bisearch(A, mid+1, right, target);  
            }  
            // 3 3 3  
            else //只有这种左中右都相等的情况下没有办法判断  
            {  
                return bisearch(A, left, mid-1, target) || bisearch(A, mid+1, right, target);  
            }  
  
}
```
---
layout: post
title: 快速排序QuickSort
date: 2013-03-17 18:28:58
categories:
- algorithm
tags:
---

快速排序[最好O(nlogn) 平均O(nlogn) 最坏O(n^2)，不稳定]分治的策略，选基准数，小在其左，大在右。递归。

![快速排序](http://upload.wikimedia.org/wikipedia/commons/6/6a/Sorting_quicksort_anim.gif)


递归法：

```c
#include <stdio.h>

int quickSort(int a[], int left, int right)
{
	if(left >= right) return 0;
	int low = left, high = right;
	int key = a[low];
	while(low < high) {
		while(low < high && a[high] > key) high--;
		a[low] = a[high];
		while(low < high && a[low] < key) low++;
		a[high] = a[low];
	}
	a[low] = key;
	quickSort(a, left, low - 1);
	quickSort(a, low + 1, right);
}

void printArray(int a[], int n)
{
	for(int i = 0; i < n; i++) {
		printf("%d ", a[i]);
	}
	printf("\n");
}

int main()
{
	int a[] = {4,6,3,2,7,9,1,5};
	printArray(a, 8);
	quickSort(a, 0, 7);
	printArray(a, 8);
}
```

非递归：用栈实现
```c++
#include <iostream>
#include <stack>

using namespace std;

int partion(int* a, int low, int high)
{
    int part=a[low];
    while(low<high) {
        while(low < high && raot[high] > part) high--;
        a[low] = a[high];
        while(low < high && a[low] < part) low++;
        a[high] = a[low];
    }
    a[low] = part;
    return low;
}

void quickSort2(int* root, int low, int high)
{
    stack<int> st;
    int k;
    if(low < high) {
        st.push(low);
        st.push(high);
        while(!st.empty()) {
            int j = st.top();
            st.pop();
            int i = st.top();
            st.pop();

            k=partion(root, i, j);

            //其实就是用栈保存每一个待排序子串的首尾元素下标，下一次while循环时取出这个范围，对这段子序列进行partition操作
            if(i < k-1) {
                st.push(i);
                st.push(k-1);
            }
            if(k+1 < j) {
                st.push(k+1);
            	st.push(j);
            }
        }
    }
}

int main()
{
    int a[8]={4,2,6,7,9,5,1,3};
    quickSort2(a, 0, 7);
    
    int i;
    for(i=0; i<8; i++)
        cout << a[i] << " ";
    cout << endl;

    return 0;
}
```

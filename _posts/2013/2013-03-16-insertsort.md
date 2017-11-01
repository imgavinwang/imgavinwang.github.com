---
layout: post
title: 插入排序InsertSort
date: 2013-03-16 18:28:58
categories:
- algorithm
tags:
---

插入排序[O(n^2)，稳定]，不断插入到有序序列。

{% highlight c++ %}
#include <stdio.h>

void printArray(int *a, int n)
{
	for(int i=0;i<n;i++) {
		printf("%d ", a[i]);
	}
	printf("\n");
}

void insertSort(int a[], int n)
{
	int i,j;
	int temp;
	for(i=1;i<n;i++)
	{
		temp=a[i];
		for(j=i; j>0 && a[j-1] > temp; j--)
		{
			a[j]=a[j-1];
		}
		a[j]=temp;
	}
}

int main()
{
	int a[] = {5,1,7,3,1,6,9,4};
	printArray(a, 8);
	insertSort(a, 8);
	printArray(a, 8);
}
{% endhighlight %}
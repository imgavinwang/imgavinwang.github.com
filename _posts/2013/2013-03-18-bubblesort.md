---
layout: post
title: 冒泡排序BubbleSort
date: 2013-03-18 18:28:58
categories:
- algorithm
tags:
---

冒泡排序[O(n^2),稳定]，不断下沉大数。

{% highlight c++ %}
#include <stdio.h>

void printArray(int a[], int n)
{
	for(int i=0; i<n; i++) {
		printf("%d ", a[i]);
	}
	printf("\n");
}

void bubbleSort(int a[], int n)
{
	for(int i=0; i<n-1; i++) {
		for(int j=0; j<n-1-i; j++) {
			if(a[j] > a[j+1]) {
				int tmp = a[j];
				a[j] = a[j+1];
				a[j+1] = tmp;
			}
		}
	}
}

int main()
{
	int a[] = {1,8,4,5,7,2};
	printArray(a, 6);
	bubbleSort(a, 6);
	printArray(a, 6);
}
{% endhighlight %}
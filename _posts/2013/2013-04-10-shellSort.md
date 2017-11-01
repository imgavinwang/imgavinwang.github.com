---
layout: post
title: 希尔排序ShellSort
date: 2013-04-10 18:28:58
categories:
- algorithm
tags:
---

希尔排序的实质就是分组插入排序[平均时间 O(nlogn) 最差时间O(n^s) 1<s<2，不稳定]，该方法又称缩小增量排序，因DL．Shell于1959年提出而得名。

{% highlight c %}
#include <stdio.h>

void printArray(int a[], int n)
{
	for(int i = 0; i < n; i++) {
		printf("%d ", a[i]);
	}
	printf("\n");
}

int shellSort(int a[], int n)
{
	int tmp, k;
	for(int gap = n / 2; gap > 0; gap /= 2) {
		for(int i = 0; i < n; i++) {
			for(int j = i + gap; j < n; j += gap) {
				tmp = a[j];
				for(k = j; k > 0 && a[k - gap] > tmp; k--) {
					a[k] = a[k - 1];
				}
				a[k] = tmp;
			}
		}
	}
}

int main()
{
	int a[] = {4,6,3,2,7,9,1,5};
	printArray(a, 8);
	shellSort(a, 8);
	printArray(a, 8);
}
{% endhighlight %}
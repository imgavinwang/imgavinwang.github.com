---
layout: post
title: 双向链表doublelink
date: 2014-01-07 18:28:58
categories:
- algorithm
tags:
---

```
typedef struct _DOUBLE_LINK_NODE
{
	int data;
	struct _DOUBLE_LINK_NODE* prev;
	struct _DOUBLE_LINK_NODE* next;
}DOUBLE_LINK_NODE;

DOUBLE_LINK_NODE* create_double_link_node(int value)
{
	DOUBLE_LINK_NODE* pDLinkNode = NULL;
	pDLinkNode = (DOUBLE_LINK_NODE*)malloc(sizeof(DOUBLE_LINK_NODE));
	assert(NULL != pDLinkNode);

	memset(pDLinkNode, 0, sizeof(DOUBLE_LINK_NODE));
	pDLinkNode->data = value;
	return pDLinkNode;
}

void delete_all_double_link_node(DOUBLE_LINK_NODE** pDLinkNode)
{
	DOUBLE_LINK_NODE* pNode;
	if(NULL == *pDLinkNode)
		return ;

	pNode = *pDLinkNode;
	*pDLinkNode = pNode->next;
	free(pNode);
	delete_all_double_link_node(pDLinkNode);
}

DOUBLE_LINK_NODE* find_data_in_double_link(const DOUBLE_LINK_NODE* pDLinkNode, int data)
{
	DOUBLE_LINK_NODE* pNode = NULL;
	if(NULL == pDLinkNode)
		return NULL;

	pNode = (DOUBLE_LINK_NODE*)pDLinkNode;
	while(NULL != pNode){
		if(data == pNode->data)
			return pNode;
		pNode = pNode ->next;
	}
	
	return NULL;
}

STATUS insert_data_into_double_link(DOUBLE_LINK_NODE** ppDLinkNode, int data)
{
	DOUBLE_LINK_NODE* pNode;
	DOUBLE_LINK_NODE* pIndex;

	if(NULL == ppDLinkNode)
		return FALSE;

	if(NULL == *ppDLinkNode){
		pNode = create_double_link_node(data);
	    assert(NULL != pNode);
		*ppDLinkNode = pNode;
		(*ppDLinkNode)->prev = (*ppDLinkNode)->next = NULL;
		return TRUE;
	}

	if(NULL != find_data_in_double_link(*ppDLinkNode, data))
		return FALSE;

	pNode = create_double_link_node(data);
	assert(NULL != pNode);

	pIndex = *ppDLinkNode;
	while(NULL != pIndex->next)
		pIndex = pIndex->next;

	pNode->prev = pIndex;
	pNode->next = pIndex->next;
	pIndex->next = pNode;
	return TRUE;
}

STATUS delete_data_from_double_link(DOUBLE_LINK_NODE** ppDLinkNode, int data)
{
	DOUBLE_LINK_NODE* pNode;
	if(NULL == ppDLinkNode || NULL == *ppDLinkNode)
		return FALSE;

	pNode = find_data_in_double_link(*ppDLinkNode, data);
	if(NULL == pNode)
		return FALSE;

	if(pNode == *ppDLinkNode){
		if(NULL == (*ppDLinkNode)->next){
			*ppDLinkNode = NULL;
		}else{
			*ppDLinkNode = pNode->next;
			(*ppDLinkNode)->prev = NULL;
		}

	}else{
		if(pNode->next)
		    pNode->next->prev = pNode->prev;
	    pNode->prev->next = pNode->next;
	}

	free(pNode);
	return TRUE;
}

int count_number_in_double_link(const DOUBLE_LINK_NODE* pDLinkNode)
{
	int count = 0;
	DOUBLE_LINK_NODE* pNode = (DOUBLE_LINK_NODE*)pDLinkNode;

	while(NULL != pNode){
		count ++;
		pNode = pNode->next;
	}
	return count;
}

void print_double_link_node(const DOUBLE_LINK_NODE* pDLinkNode)
{
	DOUBLE_LINK_NODE* pNode = (DOUBLE_LINK_NODE*)pDLinkNode;

	while(NULL != pNode){
		printf("%d\n", pNode->data);
		pNode = pNode ->next;
	}
}

```
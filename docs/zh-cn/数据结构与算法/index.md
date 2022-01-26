> 01 线性表

(一)线性表的定义和基本操作

(二)线性表的实现

1.顺序存储

2.链式存储

3.线性表的应用

> 02 栈、队列和数组

(一)栈和队列的基本概念

(二)栈和队列的顺序存储结构

(三)栈和队列的链式存储结构

(四)栈和队列的应用

(五)特殊矩阵的压缩存储

> 03 树与二叉树

(一)树的基本概念

(二)二叉树

1.二叉树的定义及其主要特征

2.二叉树的顺序存储结构和链式存储结构

3.二叉树的遍历

4.线索二叉树的基本概念和构造

(三)树、森林

1.树的存储结构

2.森林与二叉树的转换

3.树和森林的遍历

(四)树与二叉树的应用

1.二叉排序树

2.平衡二叉树

3.哈夫曼(Huffman)树和哈夫曼编码

> 04 图

(一)图的基本概念

(二)图的存储及基本操作

1.邻接矩阵法

2.邻接表法

3.邻接多重表、十字链表

(三)图的遍历

1.深度优先搜索

2.广度优先搜索

(四)图的基本应用

1.最小(代价)生成树

2.最短路径

3.拓扑排序

4.关键路径

> 05 查找

(一)查找的基本概念

(二)顺序查找法

(三)分块查找法

(四)折半查找法

(五)B树及其基本操作、B+树的基本概念

(六)散列(Hash)表

(七)字符串模式匹配

(八)查找算法的分析及应用

> 06 排序

(一)排序的基本概念

(二)插入排序

1.直接插入排序

2.折半插入排序

(三)气泡排序(bubble sort)

(四)简单选择排序

(五)希尔排序(shell sort)

(六)快速排序

(七)堆排序

(八)二路归并排序(merge sort)

(九)基数排序

(十)外部排序

(十一)各种内部排序算法的比较

(十二)排序算法的应用

# 排序算法(C++实现)

```c
#include <iostream>
using namespace std;

/*插入排序*/
void InsertSort(int a[],int n){
	int i,j,temp;
	for(i=1;i<n;i++){
		temp=a[i];
		j=i-1;
		while(j>=0&&temp<a[j]){
			a[j+1]=a[j];
			j--;
		}
		a[j+1]=temp;
	}
	
	for(int k=0;k<n;k++){
		cout<<a[k];
	}
	
}
//选择排序 
void selectSort(int a[],int n){
	int i,j,k;
	int temp;
	for(i=0;i<n;i++){
		k=i;
		for(j=i+1;j<n;j++)
		 	if(a[k]>a[j])
		  	  k=j;
		temp=a[i];
		a[i]=a[k];
		a[k]=temp;
	}
	for(int k=0;k<n;k++){
		cout<<a[k];
	}
} 
//冒泡排序 
void bubleSort(int a[],int n){
	int i,j,flag;
	int temp;
	for(i=n-1;i>=1;i--){
		flag=0;
		for(j=1;j<=i;j++)
			if(a[j-1]>a[j]){
				temp=a[j];
				a[j]=a[j-1];
				a[j-1]=temp;
				
				flag=1;
			}
		if(flag==0)
		 {
			for(int k=0;k<n;k++){
				cout<<a[k];
			}	
		 }
	} 
}
//希尔排序 
void shellSort(int a[],int n){
	int j;
	for(int gap=n/2;gap>0;gap=gap/2){
		for(int i=gap;i<n;i++){
			int tmp=a[i];
			for(j=i;j>=gap&&tmp<a[j-gap];j=j-gap){
				a[j]=a[j-gap];
			}
			a[j]=tmp;
		}
	}
		for(int k=0;k<n;k++){
				cout<<a[k];
			}
} 

int main(){
	
	int a[]={1,5,6,4,7,8,9,3};
	int b[]={4,5,7,8,6,5,1,2,3};
	int c[]={4,5,7,8,6,5,1,2,3};
	int d[]={4,5,7,8,6,5,1,2,3};
	InsertSort(d,9);
	cout<<endl;
	shellSort(b,9);
	cout<<endl;
	selectSort(c,9);
	cout<<endl;
	bubleSort(a,8);

	system("pause");
	return 0;
} 

```
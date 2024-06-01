### 二叉树中搜索一个指定的x值

【问题描述】首先利用二叉树的前序遍历建立一棵二叉树，然后输入一个x值，在二叉树中搜索结点值为x的结点，找到函数返回该结点的指针，并输出success，找不到函数返回NULL，输出failure，该函数定义为二叉树类的一个成员函数，函数声明可以参考下面: 

BinTreeNode<T> * Find (BinTreeNode <T> *root,T item) ; //在二叉树中搜索item

【输入形式】假设二叉树的结点为字符型，输入“#”表示空结点，输入有多行，第一行为建立二叉树用的字符串，具体见Sample Input，二叉树见教材P202页，图5.15，后面每一行为一个要搜索的x值，最后输入一个字符 '#' 结束

【输出形式】输出为多行，搜索x成功则输出success，搜索失败输出failure

【样例输入】

ABC##DE#G##F###

A

D

F

H

\#

【样例输出】

success

success

success

failure

#### 代码如下:

```c++
#include<iostream>

using namespace std;

template<class T>
struct BinTreeNode
{
	T data;
	BinTreeNode<T>* leftchild;
	BinTreeNode<T>* rightchild;
	BinTreeNode()
	{
		leftchild = NULL;
		rightchild = NULL;
	}
	BinTreeNode(T x)
	{
		data = x;
		leftchild = NULL;
		rightchild = NULL;
	}
};

template<class T>
class Tree
{
public:
	Tree();
	~Tree();
	void CreateTree(BinTreeNode<T>*& t);
	void deleTree(BinTreeNode<T>* t);
	BinTreeNode<T>* Find(BinTreeNode <T>* root, T item);  //在二叉树中搜索item
	BinTreeNode<T>* root;
};

template<class T>
Tree<T>::Tree()
{
	root = NULL;
}

template<class T>
Tree<T>::~Tree()
{

}

template<class T>
void Tree<T>::CreateTree(BinTreeNode<T>*& t)
{
	T x;
	cin >> x;
	if (x == '#')
	{
		t = NULL;
		return;
	}
	t = new BinTreeNode<T>(x);
	if (t == NULL)
		return;
	CreateTree(t->leftchild);
	CreateTree(t->rightchild);
}

template<class T>
void Tree<T>::deleTree(BinTreeNode<T>* t)
{
	if (t == NULL)
		return;
	deleTree(t->leftchild);
	deleTree(t->rightchild);
	delete t;
}

template<class T>
BinTreeNode<T>* Tree<T>::Find(BinTreeNode <T>* root, T item)
{
	BinTreeNode<T>* temp = NULL;
	if (root == NULL)
		return NULL;
	if (root->data == item)
		return root;
	temp = Find(root->leftchild, item);
	if (temp)
		return temp;
	temp = Find(root->rightchild, item);
	if (temp)
		return temp;
	return NULL;
}

int main()
{
	Tree<char> temp;
	temp.CreateTree(temp.root);
	char ch;
	BinTreeNode<char>* t;
	while (cin >> ch && ch != '#')
	{
		t = temp.Find(temp.root, ch);
		if (t)
			cout << "success" << endl;
		else
			cout << "failure" << endl;
	}
	temp.deleTree(temp.root);
	return 0;
}
```


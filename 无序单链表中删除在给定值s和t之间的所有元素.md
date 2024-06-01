### 无序单链表中删除在给定值s和t之间的所有元素

【问题描述】有一个带头结点的无序单链表（表中可能有重复元素），输入2个给定值s和t（s≤t），从单链表中删除其值在s与t之间的所有元素结点（包括s和t），输出删除后的单链表（有可能出现删除全部结点或者没有删除一个结点的特殊情况）；注意,如果s或t的值不合理（s>t ），则输出“s or t error!”并退出运行；如果。假设单链表中的元素值均为正整数，建立链表时，输入-1时停止创建新结点。

【输入形式】第1行输入若干个正整数（有重复），各个整数之间用空格分开，最后输入-1。第2行输入s和t的值，两个整数中间用空格分开

【输出形式】第1行输出原链表，第2行输出删除后的链表，如果链表的结点全部删除，则输出“NULL”，具体格式见样例输出。

【样例输入】

7 1 2 4 5 7 8 3 -1

5 9

【样例输出】

7-->1-->2-->4-->5-->7-->8-->3

1-->2-->4-->3

#### 代码如下:

```c++
#include<iostream>
#include<iomanip>

using namespace std;

struct LinkNode
{
	int data;
	LinkNode* link;
	LinkNode()
	{
		link = NULL;
	}
	LinkNode(int x)
	{
		data = x;
		link = NULL;
	}
};

class LinkList
{
public:
	LinkList()
	{
		head = new LinkNode;
	}
	void creat()
	{
		LinkNode* current=head;
		int n;
		while (cin >> n && n != -1)
		{
			LinkNode* temp;
			temp = new LinkNode(n);
			current->link = temp;
			current = temp;
		}
	}
	void display()
	{
		LinkNode* p = head;
		if (p->link != NULL)
		{
			while (p->link != NULL)
			{
				p = p->link;
				if (p->link == NULL)
					cout << p->data << endl;
				else
					cout << p->data << "-->";
			}
		}
		else
		{
			cout << "NULL" << endl;
		}
	}
	void delete_values(int s,int t)
	{
		LinkNode *p=NULL,*left=head,*right=head->link;
		while (right != NULL)
		{
			if (right->data >= s && right->data <= t)
			{
				p = right;
				left->link = right->link;
				right = right->link;
				delete p;
			}
			else
			{
				left = right;
				right = right->link;
			}
		}
	}
private:
	LinkNode* head;
};

int main()
{
	int m, n;
	LinkList t;
	t.creat();
	t.display();
	cin >> m >> n;
	if (m > n)
	{
		cout<< "s or t error!" << endl;
	}
	else
	{
		t.delete_values(m, n);
		t.display();
	}
	return 0;
}
```


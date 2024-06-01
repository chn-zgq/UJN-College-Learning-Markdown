﻿## UJN_DS实验和作业(仅供参考)

### 作业1 线性表的应用

#### 将顺序表中保存的序列循环左移p个位置

【问题描述】设将n（n>1）个整数存放到一维数组R中。试设计一个在时间和空间两方面都尽可能高效的算法，将R中保存的序列循环左移p（0<p<n）个位置，即将R中的数据由（x0, x1,……,xn-1）变换为（xp,xp+1,……xn-1,x0,……,xp-1）。
【输入形式】

3

1 3 6 8 9 0 2
【输出形式】

8 9 0 2 1 3 6 

##### 代码如下

```c++
#include"iostream"

using namespace std;

bool change_site(int x[], int n, int p);

int main()
{
    int p;
    cin >> p;
    int array[100];
    int n = 0;
    char mid;
    while (cin >>array[n])
    {
        cin >> noskipws >> mid;
        n++;
        if (mid == '\n')
            break;
    }
    change_site(array, n, p);
    for (int i = 0; i < n-1; i++)
        cout << array[i] << ' ';
    cout << array[n - 1] << endl;
    return 0;
}

bool change_site(int x[], int n, int p)
{
    int* s;//建立一个数组指针
    s = new int[p];//分配空间
    if (s == NULL)
    {
        cerr << "内存空间分配错误！";
            return false;//错误判断
    }
    for (int i = 0; i < p; i++)
        s[i] = x[i];//存储前P个位置的值
    for (int i = 0; i < n; i++)
    {
        if (i < n - p)
            x[i] = x[p + i];//将后面的n-P个的值依次赋值在前面的位置（移动P个位置）
        else
            x[i] = s[i - n + p];//将后面的P个位置依次赋值上前面P个数的值
    }
    delete[] s;//释放空间
    return true;//执行成功返回True
}
```



####  输出单链表倒数第K个结点值

【问题描述】输入一个单向链表，输出该链表中倒数第k个结点，链表的最后一个结点是倒数第1个节点。
【输入形式】输入第一位为K值，其后接一串以空格分隔的整型值，输入-1时停止建立链表。
【输出形式】输出为倒数第K个结点的值，若无，则输出Not Found
【样例输入】3 13 45 54 32 1 4 98 2 -1
【样例输出】4
【样例说明】K值为3，则输出链表倒数第3个结点的值，为4；数据输入间以空格隔开
【评分标准】本题要综合输出正确性及使用的数据结构。需由输入数据构建单链表。不使用链表的将不得分。

##### 代码如下

```c++
#include "iostream"

using namespace std;

struct LinkNode
{
	int data;
	LinkNode* link;
};

class List
{
public:
	List()
	{
		head = new LinkNode;
		head->link = NULL;
	}List(int x)
	{
		head = new LinkNode;
		head->data = x;
		head->link = NULL;
	}
	~List()
	{

	}
	bool creat(int x)
	{
		if (head == NULL)
			return false;
		LinkNode* current;
		current = head;
		LinkNode* newNode = new LinkNode;
		newNode->data = x;
		while (current != NULL)
		{
			if (current->link == NULL)
			{
				current->link = newNode;
				newNode->link = NULL;
				break;
			}
			else
			{
				current = current->link;
			}
		}
		return true;
	}
	bool search_k(int k, int& num)
	{
		LinkNode* left, * right;
		left = head;
		right = head;
		int count = 0;
		left = left->link;
		while (count < k)
		{
			right = right->link;
			if (right == NULL)
			{
				return false;
			}
			count++;
		}
		while (right != NULL)
		{
			if (right->link == NULL)
			{
				num = left->data;
				return true;
			}
			right = right->link;
			left = left->link;
		}
	}
private:
	LinkNode* head;
};
int main()
{
	List SingleList;
	int k;
	cin >> k;
	int num;
	while (cin >> num && num != -1)
	{
		SingleList.creat(num);
	}
	int m;
	if (k >= 1)
	{
		if (SingleList.search_k(k, m))
		{
			cout << m << endl;
		}
		else
		{
			cout << "Not Found" << endl;
		}
	}
	else
	{
		cout << "Not Found" << endl;
	}
	return 0;
}
```



#### 单链表中找出最大元素作表尾结点

【问题描述】有一个带头结点的无序单链表（表中无重复元素），在链表中找到最大元素的结点，将其插入到链表的表尾作为尾结点，假设单链表中的元素值均为正整数，建立链表时，输入-1时停止创建新结点。要求：定义一个独立函数（即非类的成员函数）实现。

【输入形式】输入若干个正整数（无重复），各个整数之间用空格分开，最后输入-1。

【输出形式】第1行输出原链表，第2行输出修改后的链表，具体格式见样例输出。

【样例输入】

7 2 4 5 8 1 -1

【样例输出】

7-->2-->4-->5-->8-->1

7-->2-->4-->5-->1-->8

##### 代码如下

```c++
#include "iostream"

using namespace std;

struct LinkNode
{
	int data;
	LinkNode* link;
};

class List
{
public:
	List()
	{
		head = new LinkNode;
		head->link = NULL;
	}List(int x)
	{
		head = new LinkNode;
		head->data = x;
		head->link = NULL;
	}
	~List()
	{

	}
	bool creat(int x)
	{
		if (head == NULL)
			return false;
		LinkNode* current;
		current = head;
		LinkNode* newNode = new LinkNode;
		newNode->data = x;
		while (current != NULL)
		{
			if (current->link == NULL)
			{
				current->link = newNode;
				newNode->link = NULL;
				break;
			}
			else
			{
				current = current->link;
			}
		}
		return true;
	}
	bool search_k(int k, int& num)
	{
		LinkNode* left, * right;
		left = head;
		right = head;
		int count = 0;
		left = left->link;
		while (count < k)
		{
			right = right->link;
			if (right == NULL)
			{
				return false;
			}
			count++;
		}
		while (right != NULL)
		{
			if (right->link == NULL)
			{
				num = left->data;
				return true;
			}
			right = right->link;
			left = left->link;
		}
	}
	void max_change()
	{
		LinkNode* current = head;
		LinkNode* Max = head;
		while (current->link != NULL)
		{
			if ((current->link)->data > (Max->link)->data)
			{
				Max = current;
			}
			current = current->link;
			if (current->link == NULL)
			{
				if (Max->link == current)
				{
					continue;
				}
				else
				{
					LinkNode* t = Max->link;
					Max->link = t->link;
					current->link = t;
					t->link = NULL;
					current = current->link;
				}
			}
		}
	}
	void display()
	{
		LinkNode* p = head;
		p = p->link;
		while (p != NULL)
		{
			if (p->link != NULL)
			{
				cout << p->data << "-->";
			}
			else
			{
				cout << p->data << endl;
			}
			p = p->link;
		}
	}
private:
	LinkNode* head;
};


int main()
{
	List SingleList;
	int num;
	while (cin >> num && num != -1)
	{
		SingleList.creat(num);
	}

	SingleList.display();
	SingleList.max_change();
	SingleList.display();
	return 0;
}
```

### 实验1 线性表的应用 

#### 顺序表中移动零元素

【问题描述】在一个顺序表中，存在多个零元素，编写函数将顺序表中所有的零元素都移动到表的末尾，即顺序表的前部为非零元素，而顺序表的后部均为零元素。要求：该函数作为类的成员函数，移动在原顺序表的存储空间中进行，不允许使用辅助的数组。

【输入形式】输入共2行，第1行输入顺序表中元素的个数，第2行依次输入元素的值，元素之间用空格分开

【输出形式】以集合形式顺序表，输出共2行，第1行输出原顺序表，第2行输出移动零元素后的顺序表，元素之间用一个空格分隔，元素与{ }间也有一个空格，具体格式见样例输出。

【样例输入】

8

4 0 1 3 0 6 0 8

【样例输出】

{ 4 0 1 3 0 6 0 8 }

{ 4 1 3 6 8 0 0 0 }

##### 代码如下

```c++
#include<iostream>

using namespace std;

void creat(int t[], int& n)
{
	for (int i = 0; i < n; i++)
		cin >> t[i];
}
void move(int t[], int n)
{
	for (int i = 0; i < n ; i++)
	{
		if (t[i] == 0)
		{
			int temp;
			for (int j = i + 1; j < n; j++)
			{
				if (t[j] != 0)
				{
					temp = t[j ];
					t[j ] = t[i];
					t[i] = temp;
					break;
				}
			}
		}
	}
}
void display(int t[], int& n)
{
	cout << "{ ";
	for (int i = 0; i < n; i++)
		cout << t[i] << " ";
	cout << "}" << endl;
}
int main()
{
	int* t;
	int n;
	cin >> n;
	t = new int[n];
	creat(t, n);
	display(t, n);
	move(t, n);
	display(t, n);
	return 0;
}
```



#### 单链表中找到最小元素作首元结点

【问题描述】有一个带头结点的无序单链表（表中无重复元素），在链表中找到最小元素的结点，将其插入到链表的表头作为首元结点，假设单链表中的元素值均为正整数，建立链表时，输入-1时停止创建新结点。要求：定义一个独立函数（即非类的成员函数）实现。

【输入形式】输入若干个正整数（无重复），各个整数之间用空格分开，最后输入-1。

【输出形式】第1行输出原链表，第2行输出修改后的链表，具体格式见样例输出。

【样例输入】

7 2 4 5 1 8 -1

【样例输出】

7-->2-->4-->5-->1-->8

1-->7-->2-->4-->5-->8

##### 代码如下

```c++
#include "iostream"

using namespace std;

struct LinkNode
{
	int data;
	LinkNode* link;
};

class List
{
public:
	List()
	{
		head = new LinkNode;
		head->link = NULL;
	}List(int x)
	{
		head = new LinkNode;
		head->data = x;
		head->link = NULL;
	}
	~List()
	{

	}
	bool creat(int x)
	{
		if (head == NULL)
			return false;
		LinkNode* current;
		current = head;
		LinkNode* newNode = new LinkNode;
		newNode->data = x;
		while (current != NULL)
		{
			if (current->link == NULL)
			{
				current->link = newNode;
				newNode->link = NULL;
				break;
			}
			else
			{
				current = current->link;
			}
		}
		return true;
	}
	bool search_k(int k, int& num)
	{
		LinkNode* left, * right;
		left = head;
		right = head;
		int count = 0;
		left = left->link;
		while (count < k)
		{
			right = right->link;
			if (right == NULL)
			{
				return false;
			}
			count++;
		}
		while (right != NULL)
		{
			if (right->link == NULL)
			{
				num = left->data;
				return true;
			}
			right = right->link;
			left = left->link;
		}
	}
	LinkNode* get_head()
	{
		return this->head;
	}
	void display()
	{
		LinkNode* p = head;
		p = p->link;
		while (p != NULL)
		{
			if (p->link != NULL)
			{
				cout << p->data << "-->";
			}
			else
			{
				cout << p->data << endl;
			}
			p = p->link;
		}
	}
private:
	LinkNode* head;
};
void min_change(List& temp)
	{
	LinkNode* head = temp.get_head();
		LinkNode* current = head;
		LinkNode* Min = head->link;
		LinkNode* t = head, * p;
		while (current->link != NULL)
		{
			p = current;
			current = current->link;
			if (current->data < Min->data)
			{
				t = p;
				Min = current;
			}
			if (current->link == NULL)
			{
				if (Min == head->link)
				{
					continue;
				}
				else
				{
					t->link = Min->link;
					Min->link = head->link;
					head->link = Min;
				}
			}
		}
	}

int main()
{
	List SingleList;
	int num;
	while (cin >> num && num != -1)
	{
		SingleList.creat(num);
	}
	SingleList.display();
	min_change(SingleList);
	SingleList.display();
	return 0;
}
```



#### 在无序单链表中删除在给定值s和t之间的所有元素

【问题描述】有一个带头结点的无序单链表（表中可能有重复元素），输入2个给定值s和t（s≤t），从单链表中删除其值在s与t之间的所有元素结点（包括s和t），输出删除后的单链表（有可能出现删除全部结点或者没有删除一个结点的特殊情况）；注意,如果s或t的值不合理（s>t ），则输出“s or t error!”并退出运行；如果。假设单链表中的元素值均为正整数，建立链表时，输入-1时停止创建新结点。

【输入形式】第1行输入若干个正整数（有重复），各个整数之间用空格分开，最后输入-1。第2行输入s和t的值，两个整数中间用空格分开

【输出形式】第1行输出原链表，第2行输出删除后的链表，如果链表的结点全部删除，则输出“NULL”，具体格式见样例输出。

【样例输入】

7 1 2 4 5 7 8 3 -1

5 9

【样例输出】

7-->1-->2-->4-->5-->7-->8-->3

1-->2-->4-->3

##### 代码如下

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



### 作业2 栈和队列
![在这里插入图片描述](https://img-blog.csdnimg.cn/13a1da7f6c0944d8972a4a96a9b0c154.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/271c8a6652e2465da844b1141cadf49f.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/00125f089a0946af95347325688b489f.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/cb211ca8019b47fda44805f2fb1e18ca.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/12049d4a7bab40ec9984d7b7f0479cf4.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/f94ece4ad17449aeb60d11d9edfe3761.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/8f31b401ab734c4eac3de0c2ce39f6a7.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/6c53fc14c70a467f8d0d950eacee45f5.png)
**PS:此题应该选D!!!**

#### 利用栈判断一个字符串是否是回文

问题描述】编写一个程序，判断一个字符串是否为回文;（顺读和倒读都一样的字符串称为回文）。
【输入形式】长度小于100的任意字符串
【输出形式】如果输入字符串是回文，则输出"yes"；如果字符串不是回文，则输出"no"
【样例1输入】abcdcba
【样例1输出】yes

【样例输入2】abcdfsfdsg

【样例2输出】no
【样例说明】样例1中，字符串abcdcba是回文，因此输出"yes"；样例2中，abcdfsfdsg不是回文，因此输出"no"

##### 代码如下

```c++
#include<iostream>
#include<iomanip>
#include<cstdlib>
#include<cstring>

using namespace std;

template<class T>
class StackList
{
public:
	StackList()
	{
		top = -1;
		
	}
	StackList(int n)
	{
		top = -1;
		stack_t = new T[n+1];
	}
	~StackList()
	{
		delete[] stack_t;
	}
	bool judege(T s[],int n)
	{
			int temp = n / 2;
			for (int i = 0; i < temp; i++)
			{
				stack_t[++top] = s[i];
			}
			for (int i = n - temp; i < n; i++)
			{
				if (stack_t[top] == s[i])
				{
					top--;
				}
				else
				{
					break;
				}
			}
			if (top == -1&&n!=0)
				return true;
			else
				return false;
	}
private:
	int top;
	T* stack_t;
};


int main()
{
	char s[101];
	cin.getline(s, 101);
	int n = strlen(s);
	StackList<char> t(n);
	if (t.judege(s, n))
	{
		cout << "yes" << endl;
	}
	else
	{
		cout << "no" << endl;
	}
	return 0;
}
```



#### 入栈和出栈操作的合法序列判定

【问题描述】假设以I和O分别表示入栈和出栈操作，栈的初态和终态均为空。入栈和出栈的操作序列表示为仅由I和O组成的序列。请编写程序判断一个给定序列是否合法。

【输入形式】给定一个小于10以内的操作序列。
【输出形式】如果输入序列是合法序列，输出yes；如果输入序列是不合法序列，则输出no。
【样例输入1】IOOIOIIO
【样例输出1】no

【样例输入2】IIOOIOIO
【样例输出2】yes

##### 代码如下

```c++
#include<iostream>

using namespace std;


class Stack
{
public:
	Stack()
	{
		t = new char[10];
		top = -1;
	}
	bool push(char c)
	{
		if (top >= 9)
			return false;
		else
		{
			t[++top] = c;
			return true;
		}
	}
	bool pop()
	{
		if (top == -1)
		{
			return false;
		}
		else
		{
			top--;
			return true;
		}
	}
	bool judge()
	{
		char temp;
		while (cin >> noskipws >> temp && temp != '\n' && top >= -1)
		{
			if (temp == 'I')
			{
				if (push(temp))
					continue;
				else
					return false;
			}
			if (temp == 'O')
			{
				if (pop())
					continue;
				else
					return false;
			}
		}
		if (top == -1)
			return true;
		else
			return false;
	}
private:
	char* t;
	int top;
};
int main()
{
	Stack t;
	if (t.judge())
	{
		cout << "yes" << endl;
	}
	else
	{
		cout << "no" << endl;
	}
	return 0;
}
```



#### 【递归】数的计数

【问题描述】

我们要求找出具有下列性质数的个数（包括输入的自然数n）。先输入一个自然数n（n≤1000），然后对此自然数按照如下方法进行处理： 

不作任何处理； 在它的左边加上一个自然数，但该自然数不能超过原数的一半； 加上数后，继续按此规则进行处理，直到不能再加自然数为止。

例如输入6，满足条件的数为 6，16，26，126，36，136。

【输入形式】自然数n（n≤1000）

【输出形式】满足条件的数的个数

【样例输入】

6
【样例输出】

6

##### 代码如下

```c++
#include<iostream>
#include"iomanip"

using namespace std;

int  count(int n,int f[])
{
	if (f[n] !=0)
	{
		return f[n];
		
	}
	else
	{
		if (n % 2 == 0)
		{
			f[n] = f[n - 1] + f[n / 2];
			return count(n - 1, f) + count(n / 2, f);
		}
		else
		{
			f[n] = f[n - 1];
			return count(n - 1, f);
		}
	}
}


int main()
{
	int f[1001] = {};
	f[1] = 1;
	int n;
	cin >> n;
	cout << count(n,f) << endl;
	return 0;
}
```

### 实验2 栈和队列应用 

#### 周末舞会

【问题描述】假设在周末舞会上，男士们和女士们进入舞厅时，各自排成一队。跳舞开始时，依次从男队和女队的队头上各出一人配成舞伴。规定每个舞曲能有一对跳舞者。若两队初始人数不相同，则较长的那一队中未配对者等待下一轮舞曲。现要求写一个程序，模拟上述舞伴配对问题。(0<m,n,k<1000)

【输入形式】第一行男士人数m和女士人数n； 第二行舞曲的数目k。

【输出形式】共k行，每行两个数，表示配对舞伴的序号，男士在前，女士在后。

【样例输入】

2 4

6

【样例输出】

1 1

2 2

1 3

2 4

1 1

2 2

##### 代码如下

```c++
#include<iostream>

using namespace std;

struct Node
{
	int data;
	Node* link;
	Node()
	{
		link = NULL;
	}
	Node(int x)
	{
		data = x;
		link = NULL;
	}
};


class queue
{
public:
	queue()
	{
		front = new Node;
		rear = front;
	}
	~queue()
	{

	}
	void rudui(int x)
	{
		Node* newnode;
		newnode = new Node(x);
		rear->link = newnode;
		rear = newnode;
	}
	void chudui(int& x)
	{
		Node* p = front->link;
		if (p == rear)
		{
			front->link = p->link;
			rear = front;
			x = p->data;
			delete p;
		}
		front->link = p->link;
		x = p->data;
		delete p;
	}

private:
	Node* front;
	Node* rear;
};

int main()
{
	int m, n, k;
	cin >> m >> n >> k;
	queue man, woman;
	int count = 1;
	while (m--)
	{
		man.rudui(count++);
	}
	count = 1;
	while (n--)
	{
		woman.rudui(count++);
	}
	while (k--)
	{
		int s, t;
		man.chudui(s);
		woman.chudui(t);
		cout << s << " " << t << endl;
		man.rudui(s);
		woman.rudui(t);
	}
	return 0;
}
```



#### 借助栈实现单链表的原地逆置

【问题描述】首先建立一个单链表，通过栈实现该链表的原地逆置，注意仅使用链表中的原有的结点空间，结点的数据成员为int型。注意这个题需要单链表和栈两个类。

【输入形式】输入只有一行，依次输入若干个整数，整数之间用一个空格分开，最后以-1结束，用表尾添加结点的方法建立一个单链表。

【输出形式】输出有2行，第1行为原链表数据，第2行为逆置后的链表数据，具体格式见样例输出

【样例输入】

 1 2 3 4 5 -1
【样例输出】

 1-->2-->3-->4-->5

 5-->4-->3-->2-->1

##### 代码如下

```c++
#include<iostream>

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
	~LinkList()
	{
	}
	void creat()
	{
		int num = -1;
		LinkNode* NewNode = NULL, * current = head;
		while (cin >> num && num != -1)
		{
			NewNode = new LinkNode(num);
			current->link = NewNode;
			current = NewNode;
		}
	}
	LinkNode* get_head()
	{
		return head;
	}
	void display()
	{
		LinkNode* p = head->link;
		while (p->link != NULL)
		{
			cout << p->data << "-->";
			p = p->link;
		}
		cout << p->data << endl;
	}
private:
	LinkNode* head;
};

class Stack
{
public:
	Stack()
	{
		top = NULL;
	}
	~Stack()
	{

	}
	void all_push(LinkNode* T)
	{
		top = T;
		LinkNode* current=T->link;
		while (current != NULL)
		{
			T = current;
			current = T->link;
			T->link = top;
			top = T;
		}
	}
	void all_pop(LinkNode* head)
	{
		while (top->link != head)
		{
			cout << top->data << "-->";
			top = top->link;
		}
		cout << top->data << endl;
	}
private:
	LinkNode* top;
};

void change(LinkList& s,Stack& temp)
{
	LinkNode* t = s.get_head();
	temp.all_push(t);
	temp.all_pop(t);
}
int main()
{
	LinkList s;
	s.creat();
	s.display();
	Stack temp;
	change(s,temp);
	return 0;
}
```



### 作业3 数组与广义表
![在这里插入图片描述](https://img-blog.csdnimg.cn/33647f40c4694327b090c434c01920fd.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/e6d6e5798d4e4ad986c08b2523ba8895.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/51389c079e284dfe95e38961bee5a9f4.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/727aca8f76584047b9add99668d75948.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/be5c56bb171d4c6aa44485cf979d9732.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/b899c1d10e404028a0ee478257a653cf.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/ead233db5b844ed184f82cb1e0706348.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/5fd9ce07f9344d9fa50b78b0276a3789.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/b7f17038a053483f96c57d08a9e7dcc1.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/0b6f64b1fdcf459b9ff21e7952e97b21.png)
### 作业4 树及其应用
![在这里插入图片描述](https://img-blog.csdnimg.cn/d476f69a1cc74a40969f308ce86b02eb.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/128e7b863abb40d3aec1e5f220625999.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/cdb624689f5a4288a8dd35846480e1e2.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/91e4a04f0e414a6289b63949bdca0bdc.png)


#### 树转二叉树

![image-20220101202047541](https://img-blog.csdnimg.cn/img_convert/e369b01da41bb4f6d99ab121637431be.png)

![](https://img-blog.csdnimg.cn/img_convert/596de6443524ada16495b64ca7becddd.png)

#### 根据后序序列和中序序列画出对应的二叉树
![在这里插入图片描述](https://img-blog.csdnimg.cn/cd4bb04485f245fb89282057212c1ffd.png)


![](https://img-blog.csdnimg.cn/img_convert/2327e9ed9b10956382e983d4a7ee1979.png)

#### 根据先序序列和中序序列画出对应的二叉树
![在这里插入图片描述](https://img-blog.csdnimg.cn/b22d67f32ee04e44a1dceea89e308ef8.png)


![](https://img-blog.csdnimg.cn/img_convert/01bce14c2815129331cfa2272b566595.png)

#### Huffman编码
![在这里插入图片描述](https://img-blog.csdnimg.cn/eca6e83145a9435aa82af8eb058fb1be.png)


![](https://img-blog.csdnimg.cn/img_convert/676c46bb2361f79a7b11e0d1f20d2c8b.png)

#### 二叉树的遍历

【问题描述】首先利用二叉树的前序遍历建立一棵二叉树，然后分别进行前序、中序、后序遍历并输出结果

【输入形式】假设二叉树的结点为字符型，输入“#”表示空结点，输入为一行字符串，具体见样例输入，二叉树见殷人昆教材P202页，图5.15
![在这里插入图片描述](https://img-blog.csdnimg.cn/e73d03028684428b8e69b1e82fe4473b.png)


【输出形式】输出有3行，分别是二叉树的前序序列，中序序列，后序序列，输出结点字符值时用一个空格隔开，注意最后一个字符后也有一个空格，具体格式见样例输出

【样例输入】

ABC##DE#G##F###

【样例输出】

A B C D E G F

C B E G D F A

C G E F D B A 

代码如下

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
class BinTree
{
public:
	BinTreeNode<T>* root;
	BinTree();
	~BinTree();
	void CreateTree(BinTreeNode<T>*& t);
	void PreTra(BinTreeNode<T>* t);
	void InodTra(BinTreeNode<T>* t);
	void PosodTra(BinTreeNode<T>* t);
	void deleTree(BinTreeNode<T>* t);
};

template<class T>
BinTree<T>::BinTree()
{
	root = NULL;
}

template<class T>
BinTree<T>::~BinTree()
{

}

template<class T>
void BinTree<T>::CreateTree(BinTreeNode<T>*& t)
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
void BinTree<T>::PreTra(BinTreeNode<T>* t)
{
	if (t != NULL)
	{
		cout << t->data << ' ';
		PreTra(t->leftchild);
		PreTra(t->rightchild);
	}
}

template<class T>
void BinTree<T>::InodTra(BinTreeNode<T>* t)
{
	if (t != NULL)
	{
		InodTra(t->leftchild);
		cout << t->data << ' ';
		InodTra(t->rightchild);
	}
}

	template<class T>
	void BinTree<T>::PosodTra(BinTreeNode<T>* t)
	{
		if (t != NULL)
		{
			PosodTra(t->leftchild);
			PosodTra(t->rightchild);
			cout << t->data << ' ';
		}
	}

template<class T>
void BinTree<T>::deleTree(BinTreeNode<T>* t)
{
	if (t == NULL)
		return;
	deleTree(t->leftchild);
	deleTree(t->rightchild);
	delete t;
}

int main()
{
	BinTree<char> temp;
	temp.CreateTree(temp.root);
	temp.PreTra(temp.root);
	cout << endl;
	temp.InodTra(temp.root);
	cout << endl;
	temp.PosodTra(temp.root);
	cout << endl;
	temp.deleTree(temp.root);
	return 0;
}
```



#### 在二叉树中搜索一个指定的x值

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

##### 代码如下

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



#### 求二叉树某个结点的父结点和左右子女结点

【问题描述】

首先利用二叉树的前序遍历建立一棵二叉树，然后输入一个x值，在二叉树中搜索结点值为x的父结点和x的左、右子女结点，请分别编写三个二叉树类的成员函数，如果找到x的父结点和左、右子女结点 ，输出结点的数据值，若无父结点和子女结点则输出 "NULL”，二叉树类的成员函数声明可以参考下面: 

 BinTreeNode<T> * Parent (BinTreeNode <T> *root,T item);    //返回item的父结点指针   
 BinTreeNode<T> * LeftChild (BinTreeNode<T> *root,T item);   //返回item的左孩子指针   
 BinTreeNode<T> * RightChild (BinTreeNode<T> *root,T item);  //返回item的右孩子指针

【输入形式】假设二叉树的结点为字符型，输入“#”表示空结点，输入有2行，第一行为建立二叉树用的字符串，具体见样例输入，二叉树见教材P197页，图5.13，第2行为一个要搜索的x值

【输出形式】输出有3行，第1行为x的父结点的数据值，第2行为x的左孩子结点的数据值，第3行为x的右孩子结点的数据值，如果父结点为空（例如根结点），或左、右孩子结点为空（例如叶子结点），则输出NULL

【样例输入】

ABD##EG###C#F##

B

【样例输出】

A

D

E

【样例说明】

注意特殊情况，例如只有一个根结点的二叉树，或者输入的x值在二叉树中不存在

其他测试数据：

输入：

A##

A

输出：

NULL

NULL

NULL



输入：

ABD##EG###C#F##

F

输出：

C

NULL

NULL



输入：

ABD##EG###C#F##

C

输出：

A

NULL

F



输入：

ABD##EG###C#F##

H

输出：

NULL

NULL

NULL

##### 代码如下

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
class BinTree
{
public:
	BinTreeNode<T>* root;
	BinTree();
	~BinTree();
	void CreateTree(BinTreeNode<T>*& t);
	void deleTree(BinTreeNode<T>* t);
	BinTreeNode<T>* Parent(BinTreeNode <T>* root, T item);       //返回item的父结点指针     
	BinTreeNode<T>* LeftChild(BinTreeNode<T>* root, T item);     //返回item的左孩子指针     
	BinTreeNode<T>* RightChild(BinTreeNode<T>* root, T item);   //返回item的右孩子指针
};

template<class T>
BinTree<T>::BinTree()
{
	root = NULL;
}

template<class T>
BinTree<T>::~BinTree()
{

}

template<class T>
void BinTree<T>::CreateTree(BinTreeNode<T>*& t)
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
void BinTree<T>::deleTree(BinTreeNode<T>* t)
{
	if (t == NULL)
		return;
	deleTree(t->leftchild);
	deleTree(t->rightchild);
	delete t;
}


template<class T>
BinTreeNode<T>* BinTree<T>::Parent(BinTreeNode <T>* root, T item)
{
	BinTreeNode<T>* temp = NULL;
	if (root == NULL)
		return NULL;
	if (root->leftchild != NULL)
	{
		if (root->leftchild->data == item)
			return root;
	}
	if (root->rightchild != NULL)
	{
		if (root->rightchild->data == item)
			return root;
	}
	temp = Parent(root->leftchild, item);
	if (temp)
		return temp;
	temp = Parent(root->rightchild, item);
	if (temp)
		return temp;
	return NULL;
}

template<class T>
BinTreeNode<T>* BinTree<T>::LeftChild(BinTreeNode<T>* root, T item)
{
	BinTreeNode<T>* temp;
	if (root == NULL)
		return NULL;
	if (root->data == item)
		return root->leftchild;
	temp = LeftChild(root->leftchild, item);
	if (temp)
		return temp;
	temp = LeftChild(root->rightchild, item);
	if (temp)
		return temp;
}

template<class T>
BinTreeNode<T>* BinTree<T>::RightChild(BinTreeNode<T>* root, T item)
{
	BinTreeNode<T>* temp;
	if (root == NULL)
		return NULL;
	if (root->data == item)
		return root->rightchild;
	temp = RightChild(root->leftchild, item);
	if (temp)
		return temp;
	temp = RightChild(root->rightchild, item);
	if (temp)
		return temp;
}

int main()
{
	BinTree<char> temp;
	temp.CreateTree(temp.root);
	char ch;
	cin >> ch;
	BinTreeNode<char>* a, *b, *c;
	a = temp.Parent(temp.root, ch);
	if (a)
		cout << a->data << endl;
	else
		cout << "NULL" << endl;
	b = temp.LeftChild(temp.root, ch);
	if (b)
		cout << b->data << endl;
	else
		cout << "NULL" << endl;
	c = temp.RightChild(temp.root, ch);
	if (c)
		cout << c->data << endl;
	else
		cout << "NULL" << endl;
	temp.deleTree(temp.root);
	return 0;
}
```



### 实验3 二叉树

#### 分别计算二叉树度为0,1,2的结点个数

【问题描述】首先利用二叉树的前序遍历建立一棵二叉树，分别用3个递归函数统计度为0，度为1和度为2的结点个数，并输出结果，这3个函数可以定义为二叉树的成员函数

【输入形式】假设二叉树的结点为字符型，输入“#”表示空结点，输入为一行字符串，具体见样例输入，二叉树见教材P202页，图5.15



【输出形式】输出只有1行，依次输出度为0的结点个数，度为1的结点个数，度为2的结点个数，3个数之间用空格分开，最后一个数据后面有一个空格，具体格式见样例输出

【样例输入】

ABC##DE#G##F###

【样例输出】

3 2 2

##### 代码如下

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
class BinTree
{
public:
	BinTree();
	~BinTree();
	void CreateTree(BinTreeNode<T>*& t);
	void deleTree(BinTreeNode<T>* t);
	void count(BinTreeNode<T>* root, int& i, int& j, int& k);
	BinTreeNode<T>* Get_root()
	{
		return root;
	}
private:
	BinTreeNode<T>* root;
};

template<class T>
BinTree<T>::BinTree()
{
	root = NULL;
}

template<class T>
BinTree<T>::~BinTree()
{

}

template<class T>
void BinTree<T>::CreateTree(BinTreeNode<T>*& t)
{
	T x;
	cin >> noskipws >> x;
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
void BinTree<T>::deleTree(BinTreeNode<T>* t)
{
	if (t == NULL)
		return;
	deleTree(t->leftchild);
	deleTree(t->rightchild);
	delete t;
}

template<class T>
void BinTree<T>::count(BinTreeNode<T>* root, int& i, int& j, int& k)
{
	if (root != NULL)
	{
		if (root->leftchild == NULL && root->rightchild == NULL)
			k++;
		if (root->leftchild == NULL && root->rightchild != NULL || root->leftchild != NULL && root->rightchild == NULL)
			j++;
		if (root->leftchild != NULL && root->rightchild != NULL)
			i++;
		count(root->leftchild, i, j, k);
		count(root->rightchild, i, j, k);
	}
}

int main()
{
	BinTree<char> temp;
	BinTreeNode<char>* head = temp.Get_root();
	int i = 0, j = 0, k = 0;
	temp.CreateTree(head);
	temp.count(head, i, j, k);
	temp.deleTree(head);
	cout << k << ' ' << j << ' ' << i <<' ';
	return 0;
}
```



#### 二叉树层次遍历(C++)

【问题描述】首先利用二叉树的前序遍历建立一棵二叉树，然后利用队列实现二叉树的层次遍历并输出结果

【输入形式】假设二叉树的结点为字符型，输入“#”表示空结点，输入为一行字符串，具体见样例输入，二叉树见殷人昆教材P202页，图5.15
![在这里插入图片描述](https://img-blog.csdnimg.cn/3af2820064854cdcb4859e6a7ab5aeb2.png)


【输出形式】输出只有一行，二叉树的层次遍历结果，输出结点字符值时用一个空格隔开，注意最后一个字符后也有一个空格，具体格式见样例输出

【样例输入】

ABC##DE#G##F###



【样例输出】

A B C D E F G 

##### 代码如下

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
struct QueneNode
{
	BinTreeNode<T>* data;
	QueneNode<T>* link;
	QueneNode()
	{
		data = NULL;
		link = NULL;
	}
	QueneNode(BinTreeNode<T>* x)
	{
		data = x;
		link = NULL;
	}
};


template<class T>
class BinTree
{
public:
	BinTree();
	~BinTree();
	BinTreeNode<T>* Get_root()
	{
		return root;
	}  //返回根结点
	void CreateTree(BinTreeNode<T>*& root);  //创建二叉树
	void DeleTree(BinTreeNode<T>* root);  //销毁二叉树
protected:
	BinTreeNode<T>* root;
};

template<class T>
class Quene
{
public:
	Quene();  //构造函数
	bool EnQuene(BinTreeNode<T>* x);  //入队
	bool DeQuene(BinTreeNode<T>*& x);  //出队
	bool Is_Empty();  //判断队列是否为空
private:
	QueneNode<T>* front;  //队头指针
	QueneNode<T>* rear;  //队尾指针
};

template<class T>
BinTree<T>::BinTree()
{
	root = NULL;
}

template<class T>
BinTree<T>::~BinTree()
{
	DeleTree(root);
}

template<class T>
void BinTree<T>::CreateTree(BinTreeNode<T>*& root)
{
	T x;
	cin >> x;
	if (x == '#')
	{
		root = NULL;
		return;
	}
	root = new BinTreeNode<T>(x);
	if (root == NULL)
		return;
	CreateTree(root->leftchild);
	CreateTree(root->rightchild);
}

template<class T>
void BinTree<T>::DeleTree(BinTreeNode<T>* root)
{
	if (root == NULL)
		return;
	DeleTree(root->leftchild);
	DeleTree(root->rightchild);
	delete root;
}

template<class T>
Quene<T>::Quene()
{
	front = new QueneNode<T>;
	rear = front;
}


template<class T>
bool Quene<T>::EnQuene(BinTreeNode<T>* x)
{
	QueneNode<T>* newnode;
	newnode = new QueneNode<T>(x);
	if (newnode == NULL)
		return false;
	rear->link = newnode;
	rear = newnode;
	return true;
}

template<class T>
bool Quene<T>::DeQuene(BinTreeNode<T>*& x)
{
	QueneNode<T>* p = front->link;
	if (front == rear)
		return false;
	if (p == rear)
		rear = front;
	front->link = p->link;
	x = p->data;
	delete p;
	p = NULL;
	return true;
}

template<class T>
bool Quene<T>::Is_Empty()
{
	if (front == rear)
	{
		return true;
	}
	else
	{
		return false;
	}
}

void Level_Tra(BinTreeNode<char>* root)
{
	BinTreeNode<char>* p = root;
	if (root == NULL)
	{
		return;
	}
	Quene<char> t;
	t.EnQuene(p);
	while (!t.Is_Empty())
	{
		t.DeQuene(p);
		cout << p->data << ' ';
		if (p->leftchild != NULL)
			t.EnQuene(p->leftchild);
		if (p->rightchild != NULL)
			t.EnQuene(p->rightchild);
	}
}

int main()
{
	BinTree<char> tree;
	BinTreeNode<char>* head = tree.Get_root();
	tree.CreateTree(head);
	Level_Tra(head);
	return 0;
}
```



### 作业5 图的应用

#### Dijkstra 算法求最短路径
![在这里插入图片描述](https://img-blog.csdnimg.cn/ee23d5def5ec4226b21078538eba6729.png)


![](https://img-blog.csdnimg.cn/img_convert/ad22251f8790dad3d1416afd08a99a99.png)

#### 图的深搜和广搜
![在这里插入图片描述](https://img-blog.csdnimg.cn/da1ccc7e2d74496e8ff1586a17005d02.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/df8ecb269f714b56a5ae09235304bddb.png)


#### 图的生成树或生成森林
![在这里插入图片描述](https://img-blog.csdnimg.cn/1237c4395d004f6aa8dbdd0ce272181b.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/0fa8ba06b15140f3b05c5a8ff35a0794.png)


#### 用Prim算法画出图的最小生成树
![在这里插入图片描述](https://img-blog.csdnimg.cn/8e3a71d63f0746f6a46e5a2ac8a6ba46.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/872d0c6bca784fcd8971d55152da97c9.png)


#### 用Kruskal算法画出图的最小生成树
![在这里插入图片描述](https://img-blog.csdnimg.cn/add51435085b463a868d342bd51c68d6.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/72533d7a98144eaa9c0fdc852f775926.png)


### 实验4 图的应用

#### 非带权无向图的广度优先遍历

【问题描述】输入一个不带权值的无向图的信息，用邻接表存储图，输出邻接表，然后输入一个起始顶点，从该顶点开始进行广度优先遍历，输出遍历的顶点序列

【输入形式】输入有多行，第1行输入两个整数m, n，第1个m为顶点数，第2个n为边数，第2行输入全部顶点名字（一般用大写字母表示），后面还有n行数据，分别是每一条边的2个顶点的名字，最后一行输入广度优先遍历起始顶点的名字

【输出形式】输出图的邻接表，具体格式见Sample Output，最后一行输出图得广度优先遍历的顶点序列，各个顶点之间用一个空格隔开，最后一个顶点后也有一个空格，不用单独处理

【样例输入】

9 10

ABCDEFGHI

AD

AC

AB

BE

BC

CF

DF

EG

FH

HI

A

【样例输出】

0 A-->1-->2-->3
1 B-->2-->4-->0
2 C-->5-->1-->0
3 D-->5-->0
4 E-->6-->1
5 F-->7-->3-->2
6 G-->4
7 H-->8-->5
8 I-->7
A B C D E F G H I

##### 代码如下

```c++
#include<iostream>

using namespace std;

//邻接边的结点定义
struct EdgeNode
{
	int Ver_position;//邻接结点的位置
	string Edge_info;//邻接边的信息
	EdgeNode* Next_edge;//下一个邻接边
	EdgeNode()
	{
		Ver_position = -1;
		Edge_info = "";
		Next_edge = NULL;
	}
	EdgeNode(int x, string y)
	{
		Ver_position = x;
		Edge_info = y;
		Next_edge = NULL;
	}
};

//顶点的结点定义
template<class T>
struct VertexNode
{
	T data;//顶点的值
	EdgeNode* Adj_edge;//顶点的第一条邻接边的信息
	VertexNode()
	{
		data = 0;
		Adj_edge = NULL;
	}
};

//队列结点定义
template<class T>
struct QueneNode
{
	VertexNode<T>* data;//以顶点指针为数据域
	QueneNode* link;//下一个队列结点
	QueneNode()
	{
		data = NULL;
		link = NULL;
	}
	QueneNode(VertexNode<T>* x)
	{
		data = x;
		link = NULL;
	}
};

//无向图的类定义
template<class T>
class UndirGraph
{
public:
	UndirGraph(int x, int y);//构造函数
	int Judge_ver(T x);//判断顶点的位置
	char output_ver(int x)//输出某个位置的顶点的值
	{
		return array[x].data;
	}
	void Insert_edge(string x);//插入边
	void display();//输出邻接边
	VertexNode<T>* Get_ver()//取得顶点数组的首地址
	{
		return array;
	}
private:
	VertexNode<T>* array;//顶点数组指针
	int Len;//顶点的个数
};

//队列类的定义
template<class T>
class Quene
{
public:
	Quene();//构造函数
	bool Join_quene(VertexNode<T>* x);//入队
	bool Leave_quen(VertexNode<T>*& x);//出队
	bool Is_Empty();//判断队列是否为空
private:
	QueneNode<T>* front;//队头指针
	QueneNode<T>* rear;//队尾指针
};

//无向图的构造函数,构造邻接表
template<class T>
UndirGraph<T>::UndirGraph(int x, int y)//x为顶点数目,y为边的数目
{
	Len = x;
	array = new VertexNode<T>[x];//构造顶点数组
	for (int i = 0; i < Len; i++)
		cin >> array[i].data;//顶点赋值
	string temp = "";//边的定义
	for (int i = 0; i < y; i++)
	{
		cin >> temp;//输入边
		Insert_edge(temp);//插入边
		temp = "";
	}
}

//判断顶点为某值的顶点的位置
template<class T>
int UndirGraph<T>::Judge_ver(T x)
{
	for (int i = 0; i < Len; i++)
	{
		if (array[i].data == x)//值对应,则返回顶点位置
		{
			return i;
		}
	}
}

//插入邻接边
template<class T>
void UndirGraph<T>::Insert_edge(string str)
{
	EdgeNode* newedgeleft = NULL, * newedgeright = NULL;//无向图一条边属于两个结点,所以两个结点后都要插入边
	int num, pos;
	pos = Judge_ver(str[0]);//获取第一个顶点的位置
	num = Judge_ver(str[1]);//获取邻接顶点的位置
	newedgeleft = new EdgeNode(num, str);//构造邻接顶点的边
	newedgeleft->Next_edge = array[pos].Adj_edge;
	array[pos].Adj_edge = newedgeleft;//第一个顶点后面插入边
	pos = Judge_ver(str[1]);//获取邻接顶点的位置
	num = Judge_ver(str[0]);//获取第一个顶点的位置
	newedgeright = new EdgeNode(num, str);//构造第一个顶点的边
	newedgeright->Next_edge = array[pos].Adj_edge;
	array[pos].Adj_edge = newedgeright;//邻接顶点后面插入边
}

//输出邻接表
template<class T>
void UndirGraph<T>::display()
{
	EdgeNode* current = NULL;//边遍历指针
	for (int i = 0; i < Len; i++)
	{
		cout << i << ' ' << array[i].data;//输出顶点值
		current = array[i].Adj_edge;
		while (current != NULL)//遍历输出边值
		{
			cout << "-->" << current->Ver_position;
			current = current->Next_edge;
		}
		cout << endl;
	}
}

//队列构造函数
template<class T>
Quene<T>::Quene()
{
	front = new QueneNode<T>;//空头结点
	rear = front;//尾指针最开始向头结点
}

//入队
template<class T>
bool Quene<T>::Join_quene(VertexNode<T>* x)
{
	QueneNode<T>* newquenenode = NULL;//队列指针定义
	newquenenode = new QueneNode<T>(x);//队列结点的新建
	if (newquenenode == NULL)//分配空间失败
	{
		return false;
	}
	//队尾入队
	newquenenode->link = rear->link;//新结点的指针指向rear的指针域(NULL)
	rear->link = newquenenode;//rear的指针域指向新结点
	rear = newquenenode;//队尾指针指向新结点
	return true;
}

//出队
template<class T>
bool Quene<T>::Leave_quen(VertexNode<T>*& x)
{
	QueneNode<T>* p = front->link;//队头结点的指针定义
	if (front == rear)//判断队列是否为空
	{
		return false;
	}
	if (p == rear)//front指针域指向的是rear
		rear = front;//删除p时,避免rear成为野指针
	front->link = p->link;//头结点的指针域指向队头结点的指针域
	x = p->data;//传值
	delete p;//删除队列结点
	p = NULL;//避免野指针
	return true;
}

//判断队列是否为空
template<class T>
bool Quene<T>::Is_Empty()
{
	if (front == rear)//判空条件
	{
		return true;
	}
	else
	{
		return false;
	}
}

//无向图的广度遍历
void Graph_tra(UndirGraph<char> t, int n)
{
	char x;//遍历开始的顶点
	cin >> x;//输入遍历开始的顶点
	VertexNode<char>* p = NULL;//顶点遍历指针
	VertexNode<char>* temp = t.Get_ver();//顶点数组
	EdgeNode* current = NULL;//边遍历指针
	bool* Judge_ver_tra;//顶点是否被访问数组
	Judge_ver_tra = new bool[n];
	for (int i = 0; i < n; i++)
		Judge_ver_tra[i] = false;//被访问顶点所在位置为true,否者为false
	Quene<char> s;//队列
	if (temp == NULL)//如果顶点数组为空,不进行遍历
	{
		return;
	}
	s.Join_quene(&temp[t.Judge_ver(x)]);//入队开始遍历的顶点,通过顶点值找到顶点结点
	while (!s.Is_Empty())//队列为空,结束循环
	{
		int num;//顶点位置
		s.Leave_quen(p);//出队
		num = t.Judge_ver(p->data);//顶点位置赋值
		if (!Judge_ver_tra[num])//如果没有被访问输出
		{
			cout << p->data << ' ';//输出顶点
			Judge_ver_tra[num] = true;//访问数组对应值改变为已经被访问
		}
		current = p->Adj_edge;//边遍历指针赋值
		while (current != NULL)//边遍历指针为空,停止循环
		{
			if (!Judge_ver_tra[current->Ver_position])//如果边邻接的顶点未被访问,入队
				s.Join_quene(&temp[current->Ver_position]);
			current = current->Next_edge;//边遍历指针改变下一条边
		}
	}
	cout << endl;//保证格式
}

int main()
{
	int m, n;
	cin >> m >> n;//输入顶点数,边数
	UndirGraph<char> t(m, n);//构造无向图邻接表
	t.display();//输出无向图邻接表
	Graph_tra(t, m);//无向图的广度遍历
}
```


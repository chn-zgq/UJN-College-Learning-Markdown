## 十进制正整数转换为B进制数(利用栈实现)

#### 代码如下:

```c++
#include<iostream>
#include<iomanip>
#include<cmath>

using namespace std;

struct stacknode
{
	int data;
	stacknode* link;
	stacknode()
	{
		data = 0;
		link = NULL;
	}
	stacknode(int x)
	{
		data = x;
		link = NULL;
	}
};

class stack
{
public:
	stack()
	{
		top = NULL;
	}
	void creat(int num, int B)
	{
		if (num != 0)//零元素特殊处理
		{
			while (num != 0)
			{
				push(num, B);
			}
		}
		else
		{
			push(num, B);
		}
	}
	bool push(int& num, int n)//取余操作
	{
		stacknode* p;
		int temp;
		temp = num % n;
		num /= n;
		p = new stacknode(temp);
		if (p == NULL)
			return false;
		if (top == NULL)
			top = p;
		else
		{
			p->link = top;
			top = p;
		}
		return true;
	}
	void empty_pop()//输出所有余数
	{
		stacknode* t;
		while (top != NULL)
		{
			t = top;
			cout << t->data;
			top = t->link;
			delete t;
		}
	}
private:
	stacknode* top;//栈顶指针
};

int main()
{
	int number;
	int X;
	cin >> X >> number;
	stack t;
	t.creat(number, X);
	t.empty_pop();
	return 0;
}
```


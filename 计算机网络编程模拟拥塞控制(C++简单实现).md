## 计算机网络编程模拟拥塞控制(C++简单实现)

编程实现四种拥塞控制方法。要求：

① 程序具备必要的输入提示、输出提示。

② 程序输入：初始门限值、传输轮次。

③ 根据“发生超时”或“收到三次重复确认”实施拥塞控制。

④ 程序输出：各传输轮次的拥塞窗口值。

⑤ 可查询单个传输轮次的拥塞窗口值。

⑥ 最好考虑到实际的TCP拥塞窗口以字节为单位。

### 代码如下:

```c++
#include<iostream>
#include<ctime>

#define SMSS 1600//

using namespace std;

//传输控制函数
void control();

//输入ssthresh和传输轮次
void input(int& max_ssthresh, int& max_time);

//慢启动函数
bool Slow_strart(int& cwnd, int& max_ssthresh, int max_count, int& count);

//拥塞避免函数
bool Congestion_avoidance(int& cwnd, int& max_ssthresh, int max_count, int& count);

//快速重传函数
void Fast_retransmit(int& cwnd, int& max_ssthresh);

//查询某个轮次cwnd
void search_x(int* p);

bool Slow_strart(int& cwnd, int& max_ssthresh, int max_count, int& count)//慢启动
{
	srand(time(NULL));//随机数种子
	int len = cwnd / SMSS;//一个轮次发送的SMSS个数
	for (int i = 1; i <= len; i++)
	{
		int reset = rand() % 100;//发生重传的概率定义
		if (reset >= 10)//发生重传概率设置为10%
		{
			if (cwnd < max_ssthresh)
			{
				int flag = rand() % 105;//发生超时的概率定义
				if (flag < 100)//超时概率设置为5%
				{
					cwnd += SMSS;//cwnd加一
				}
				else
				{
					count++;//轮次加一
					cout << "发生超时前的cwnd和ssthresh:" << cwnd << ' ' << max_ssthresh << '.' << endl;
					max_ssthresh = cwnd / 2;//ssthresh为当前cwnd的一半
					if (max_ssthresh < SMSS)
						max_ssthresh = SMSS;
					cwnd = SMSS;//cwnd设置为一个SMSS
					cout << "慢启动执行时:" << endl;
					cout << "发生超时!!!" << endl;
					return false;
				}
			}
			else//正常结束慢启动
			{
				count++;
				cout << "慢启动完成." << endl;
				return true;
			}
		}
		else
		{
			cout << "慢启动执行时:" << endl;
			cout << "发生重传前的cwnd和ssthresh:" << cwnd << ' ' << max_ssthresh << '.' << endl;
			Fast_retransmit(cwnd, max_ssthresh);//调用快速重传函数
			count++;
			return false;
		}
	}
	cout << "慢启动执行完整一次." << endl;//慢启动完整完成一次
	count++;
	return true;
}

bool Congestion_avoidance(int& cwnd, int& max_ssthresh, int max_count, int& count)//拥塞避免
{
	srand(time(NULL));//设置随机数种子
	int len = cwnd / SMSS;//一个轮次发送的SMSS个数
	for (int i = 1; i <= len; i++)
	{
		int reset = rand() % 100;//发生重传的概率定义
		if (reset >= 10)//发生重传概率设置为10%
		{
			int flag = rand() % 105;//发生超时的概率定义
			if (flag < 100)//超时概率设置为5%
			{
				cwnd += SMSS / len;//cwnd的增加量

			}
			else
			{
				count++;
				cout << "发生超时前的cwnd和ssthresh:" << cwnd << ' ' << max_ssthresh << '.' << endl;
				max_ssthresh = cwnd / 2;//ssthresh为当前cwnd的一半
				if (max_ssthresh < SMSS)
					max_ssthresh = SMSS;
				cwnd = SMSS;//cwnd设置为一个SMSS
				cout << "拥塞避免执行时:" << endl;
				cout << "发生超时!!!" << endl;
				return false;
			}
		}
		else
		{
			cout << "拥塞避免执行时:" << endl;
			cout << "发生重传前的cwnd和ssthresh:" << cwnd << ' ' << max_ssthresh << '.' << endl;
			Fast_retransmit(cwnd, max_ssthresh);//调用快速重传函数
			count++;
			return false;
		}
	}
	cout << "拥塞避免执行完整一次." << endl;
	count++;
	return true;
}

void Fast_retransmit(int& cwnd, int& max_ssthresh)
{
	cout << "收到三次确认,启动快速重传!!!" << endl;
	cwnd = cwnd / 2;//cwnd为当前cwnd的一半
	if (cwnd < SMSS)
		cwnd = SMSS;
	max_ssthresh = cwnd;//ssthresh也为当前cwnd的一半
}

void input(int& max_ssthresh, int& max_time)
{
	cout << "请输入ssthresh(单位为字节):" << endl;
	cin >> max_ssthresh;//输入ssthresh
	cout << "请输入传输轮次:" << endl;
	cin >> max_time;//输入传输轮次
}

void search_x(int* p)//查询某个轮次cwnd
{
	cout << "输入你想查询的轮次:" << endl;
	int count;
	cin >> count;//输入某个轮次
	cout << "该轮次cwnd为:" << p[count - 1] << endl;//输出该轮次的cwnd值
}

void control()
{
	int count = 1;//计数传输轮次
	int cwnd = SMSS;//cwnd初始化
	int max_ssthresh, max_count;//定义ssthresh和传输轮次
	int* search_count;//定义查询数组
	int search_flag;//查询标志
	input(max_ssthresh, max_count);//输入ssthresh和传输轮次
	search_count = new int[max_count];
	cout << "*******************************************" << endl;
	cout << "第" << count << "轮传输结束,cwnd为 " << cwnd << ",ssthresh为" << max_ssthresh << '.' << endl;
	search_count[count - 1] = cwnd;
	cout << "*******************************************" << endl;
	while (count < max_count)//传输轮次范围内
	{
		if (cwnd < max_ssthresh)//cwnd小于ssthresh时
		{
			Slow_strart(cwnd, max_ssthresh, max_count, count);//调用慢启动函数
			cout << "第" << count << "轮传输结束,cwnd为 " << cwnd << ",ssthresh为" << max_ssthresh << '.' << endl;//输出单个轮次的cwnd
			cout << "*******************************************" << endl;
			search_count[count - 1] = cwnd;
		}
		else//cwnd大于等于ssthresh时
		{
			Congestion_avoidance(cwnd, max_ssthresh, max_count, count);//调用拥塞避免函数
			cout << "第" << count << "轮传输结束,cwnd为 " << cwnd << ",ssthresh为" << max_ssthresh << '.' << endl;//输出单个轮次的cwnd
			cout << "*******************************************" << endl;
			search_count[count - 1] = cwnd;
		}
	}
	cout << "到达传输轮次!!!" << endl;//结束传输
	cout << "如果你想查询某个轮次,请输入1,否侧输入0" << endl;
	while (cin >> search_flag && search_flag == 1)
	{
		search_x(search_count);//调用查询函数
		cout << "如果你还想查询某个轮次,请输入1,否侧输入0" << endl;
	}
}

int main()
{
	while (1)
	{
		control();//调用传输控制函数
		bool end;
		cout << "如果你想结束请输入1.否则输入0." << endl;
		cin >> end;
		if (end)//结束标志
		{
			cout << "程序执行结束!!!" << endl;
			break;

		}
		cout << "进行下一次传输." << endl;
	}
	return 0;
}
```


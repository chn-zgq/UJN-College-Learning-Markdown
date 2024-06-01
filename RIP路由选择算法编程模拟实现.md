## RIP路由选择算法编程模拟实现

### 题目意义：

**ARPANET是第一个分组交换网，刚出现的时候是单个网络，后来发展成互联网，网络之所以能够互联，是因为路由器在网络之间进行分组转发。作为网络管理员，只有掌握路由算法的工作原理才能更好地进行网络管理和故障排除。**

### 设计内容与要求：

**(1) 调研ARPANET网络所使用的路由选择算法。**

**(2) 掌握该路由选择算法的工作原理。**

**(3) 参照图1中的网络拓扑编程实现该路由选择算法。要求：**

**① 程序具备必要的输入提示、输出提示。**

**② 路由表的结构<目的网络，跳数，下一跳>。**

**③ 可交互选择是否输出路由器更新过程中的中间结果，如：在每次更新周期后的路由表等。**

**④ 输出每个路由器最终收敛后的路由表。**

**⑤ 可查询单个路由器的路由表信息。**

**⑥ 当某个网络出现问题时，可更新路由。**

### 网络拓扑:

![img](https://s2.loli.net/2021/12/22/8pc9qfbKhJm7YIy.jpg)

### 测试数据:

```
6
A
B
C
D
E
F
主机1
1.0.0.1
255.255.255.0
1.0.0.254
1
主机1-1
1.0.0.2
255.255.255.0
1.0.0.254
1
主机2
2.0.0.1
255.255.255.0
2.0.0.254
0
主机3
3.0.0.1
255.255.255.0
3.0.0.254
0
主机4
4.0.0.1
255.255.255.0
4.0.0.254
0
主机5
5.0.0.1
255.255.255.0
5.0.0.254
0
主机6
6.0.0.1
255.255.255.0
6.0.0.254
0
主机7
13.0.0.1
255.255.255.0
13.0.0.254
0
A
E
7.0.0.1
7.0.0.2
1
A
D
8.0.0.1
8.0.0.2
1
A
B
9.0.0.1
9.0.0.2
1
B
C
10.0.0.1
10.0.0.2
1
D
E
11.0.0.1
11.0.0.2
1
D
F
11.0.0.3
11.0.0.4
1
E
F
11.0.0.5
11.0.0.6
1
C
F
12.0.0.1
12.0.0.2
0

```

### 代码如下:

```c++
#include<iostream>
#include<sstream>
#include<string>

using namespace std;

//路由表结构体
struct RIP;

//相邻路由器结构体
struct AdjNode;

//相邻主机结构体
struct AdjHost;

//路由器结构体
struct RouteNode;

//菜单
void menu(RouteNode*& route, int& n);

//初始化路由器
void Input(RouteNode*& route, int& n);

// 计算网络地址
string Calc_network(string ip, string mask);

//查询某个路由器的位置
RouteNode* Search_route(RouteNode* t, string name);

//判断是否加入到路由表
bool Judge_rip(RIP* t, string des_network, string route_name, string route_ip, int n);

//单个路由器添加路由表
void Add_rip(RouteNode* t, RouteNode* route);

//距离向量算法
void Dis_vector(RouteNode* route, int num);

//输入所有路由器的路由表
void Display_all(RouteNode* route);

//输出单个路由器的路由表
void Display_route(RouteNode* route);

//路由器的某个接口出现故障
void delete_route(RouteNode* route);


struct RIP//路由表
{
	string des_network;//目的网络地址
	string des_mask;//目的网络子网掩码
	string next_name;//下一跳路由器的名字
	string next_ip;//下一跳的IP地址
	int rid_count;//跳数
	RIP* rip_link;//下一个路由表指针

	RIP()//默认构造函数
	{
		des_mask = "";
		des_network = "";
		next_name = "";
		next_ip = "";
		rid_count = 0;
		rip_link = NULL;
	}

	RIP(string mask, string network)//直连主机的子网掩码和网络地址
	{
		des_mask = mask;
		des_network = network;
		next_name = "无";
		next_ip = "路由器直连";
		rid_count = 0;
		rip_link = NULL;
	}

	RIP(string mask, string network, string route_name, string route_network, int n)//相邻路由器的路由表的传入
	{
		des_mask = mask;
		des_network = network;
		next_name = route_name;
		next_ip = route_network;
		rid_count = n;
		rip_link = NULL;
	}
};

struct AdjNode//相邻路由器
{
	string adj_name;//相邻路由器名
	string adj_ip;//相邻路由器的接口地址
	string route_ip;//本路由器的接口地址
	AdjNode* adj_link;//下一个相邻的路由器

	AdjNode()//默认构造函数
	{
		adj_name = "";
		adj_ip = "";
		route_ip = "";
		adj_link = NULL;
	}

	AdjNode(string a_name, string a_ip, string r_ip)//传入相邻路由器的信息
	{
		adj_name = a_name;
		adj_ip = a_ip;
		route_ip = r_ip;
		adj_link = NULL;
	}
};

struct AdjHost//直连主机
{
	string host_name;//直连主机名
	string host_network;//直连主机的网络地址
	string host_ip;//直连主机的IP地址
	string host_mask;//直连主机的子网掩码
	string route_ip;//与路由器相连的接口的IP地址
	AdjHost* host_link;//下一个相邻的主机

	AdjHost()//默认构造函数
	{
		host_name = "";
		host_ip = "";
		host_network = "";
		route_ip = "";
		host_link = NULL;
	}

	AdjHost(string h_name, string h_ip, string h_mask, string r_ip)//传入直连主机的信息
	{
		host_name = h_name;
		host_ip = h_ip;
		route_ip = r_ip;
		host_mask = h_mask;
		host_network = Calc_network(h_ip, h_mask);
		host_link = NULL;
	}
};

struct RouteNode//路由器
{
	string route_name;//路由器名
	RIP* ip_rip;//路由器的路由表
	AdjNode* adj_route;//路由器的相邻的路由器
	AdjHost* adj_host;//路由器的直连主机
	RouteNode* route_link;//下一个路由器

	RouteNode()//默认构造函数
	{
		route_name = "";
		ip_rip = NULL;
		adj_route = NULL;
		adj_host = NULL;
		route_link = NULL;
	}

	RouteNode(string name)//传入路由器名的构造函数
	{
		route_name = name;
		ip_rip = NULL;
		adj_route = NULL;
		adj_host = NULL;
		route_link = NULL;
	}
};

string Calc_network(string ip, string mask)//计算主机的网络地址
{
	string t = "";//局部网络地址
	int ip_len = ip.length();//IP地址的长度
	int mask_len = mask.length();//子网掩码的长度
	char flag = '.';//flag值为小数点
	int ip_count = 0;//IP地址的计数
	int mask_count = 0;//子网掩码的计数
	int ip_p = 0;//搜索IP地址flag字段的位置
	int mask_p = 0;//搜索mask字段的位置

	//网络地址生成函数
	while (ip_count < ip_len || mask_count < mask_len || ip_p != -1 || mask_p != -1)
	{
		int m = 0, n = 0;//用作与运算的局部变量

		ip_p = ip.find(flag, ip_count);//获取IP地址的flag的位置,从IP计数位置开始到搜索到flag的位置
		if (ip_p == -1)//如果位置为-1,表明已经到十进制的最后一个数字字段
		{
			m = stoi(ip.substr(ip_count, ip_len - 1));//字符串转换为整形
			ip_count = ip_len;
		}
		else
		{
			m = stoi(ip.substr(ip_count, ip_p - ip_count));//字符串转换为整形
			ip_count = ip_p + 1;
		}

		mask_p = mask.find(flag, mask_count);//获取mask地址的flag的位置,从mask计数位置开始到搜索到flag的位置
		if (mask_p == -1)//如果位置为-1,表明已经到十进制的最后一个数字字段
		{
			n = stoi(mask.substr(mask_count, mask_count - 1));//字符串转换为整形
			mask_count = mask_len;
		}
		else
		{
			n = stoi(mask.substr(mask_count, mask_p - mask_count));//字符串转换为整形
			mask_count = mask_p + 1;
		}

		t += to_string((m & n));//进行与远算并且加入到局部网络地址变量的后面
		t += ".";//添加小数点
	}
	t[t.length() - 1] = '\0';//去除最后多余的小数点

	return t;//返回网络地址
}

RouteNode* Search_route(RouteNode* t, string name)//搜索某个路由器的位置
{
	while (t != NULL)//循环遍历条件
	{
		if (t->route_name == name)//判断条件,找到该路由器,返回其位置
		{
			return t;
		}
		t = t->route_link;//变换遍历指针
	}

	return NULL;//没有找到返回空指针
}

void Input(RouteNode*& route, int& n)
{
	cout << "请输入该自治系统路由器数目:" << endl;
	int route_count = 0;//路由器的数目
	cin >> route_count;
	n = route_count;//获取路由器的数目
	string name = "";//路由器名
	RouteNode* current = NULL;//路由器的遍历指针

	cout << "请输入第1个路由器名字:" << endl;
	cin >> name;
	route = new RouteNode(name);//第一个路由器单独处理
	if (route == NULL)//分配空间失败
	{
		cout << "创建失败!!!" << endl;
		return;
	}

	current = route;
	
	for (int i = 1; i < route_count; i++)//遍历执行输入每一个路由器名
	{
		name = "";
		RouteNode* newroute;
		cout << "请输入第" << i + 1 << "个路由器名字:" << endl;
		cin >> name;
		newroute = new RouteNode(name);
		if (route == NULL)//分配空间失败
		{
			cout << "创建失败!!!" << endl;
			return;
		}

		current->route_link = newroute;//改变路由器链表的指针指向,该方法为后插法
		current = newroute;
	}

	cout << "接下来请构造路由器的直连网络和相邻路由器!!!" << endl;
	current = route;
	
	while (current != NULL)//输入路由器的直连主机
	{
		while (1)
		{
			AdjHost* new_adj = NULL;//直连主机指针
			cout << "请输入" << current->route_name << "路由器的直连主机IP地址和对应路由器的接口的IP地址:" << endl;
			string h_name = "", h_ip = "", h_mask = "", r_ip = "";//直连主机名,直连主机的IP地址,直连主机的子网掩码,与直连主机相连的路由器的接口地址
			cout << "直连主机名字为:" << endl;
			cin >> h_name;
			cout << "主机IP地址为:" << endl;
			cin >> h_ip;
			cout << "主机的子网掩码为:" << endl;
			cin >> h_mask;
			cout << "与本路由器接口所连的接口IP地址为:" << endl;
			cin >> r_ip;

			new_adj = new AdjHost(h_name, h_ip, h_mask, r_ip);//创建直连主机
			new_adj->host_link = current->adj_host;//变换指针,前插法
			current->adj_host = new_adj;

			cout << "请问你是否结束输入???结束请输入0,否则输入其他任意字符!!!" << endl;
			int flag = 0;//结束输入标志
			cin >> flag;
			if (flag == 0)//判断结束标志
			{
				break;
			}
		}

		current = current->route_link;//改变路由器的遍历指针
	}

	while (1)//输入相邻路由器的信息
	{
		cout << "请输入相邻路由器各自的名字以及各自的接口IP地址,以换行结束输入." << endl;
		string route_name_a, route_name_b, rout_a_ip, route_b_ip;//各自路由器名和各自接口的IP地址
		RouteNode* t1 = NULL, * t2 = NULL;//搜索路由器的指针
		cout << "第一个路由器名字为:" << endl;

		while (1)//路由器名非法检验
		{
			cin >> route_name_a;
			t1 = Search_route(route, route_name_a);//搜索该路由器的位置
			if (t1 == NULL)
			{
				cout << "非法输入,不存在该路由器!!!" << endl;
				route_name_a = "";
				cout << "请重新输入!!!" << endl;
			}
			else
			{
				break;
			}
		}

		cout << "另外一个路由器的名字:" << endl;

		while (1)//路由器名非法检验
		{
			cin >> route_name_b;
			t2 = Search_route(route, route_name_b);//搜索路由器的位置
			if (t2 == NULL)
			{
				cout << "非法输入,不存在该路由器!!!" << endl;
				route_name_b = "";
				cout << "请重新输入!!!" << endl;
			}
			else
			{
				break;
			}
		}

		cout << "请输入" << route_name_a << "路由器接口IP地址为:" << endl;
		cin >> rout_a_ip;
		cout << "请输入" << route_name_b << "路由器接口IP地址为:" << endl;
		cin >> route_b_ip;

		AdjNode* new_adj_a = NULL, * new_adj_b = NULL;//相邻路由器指针

		new_adj_a = new AdjNode(route_name_b, route_b_ip, rout_a_ip);//分配空间
		new_adj_a->adj_link = t1->adj_route;//改变指针指向,前插法
		t1->adj_route = new_adj_a;

		new_adj_b = new AdjNode(route_name_a, rout_a_ip, route_b_ip);//分配空间
		new_adj_b->adj_link = t2->adj_route;//改变指针指向,前插法
		t2->adj_route = new_adj_b;

		cout << "请问你是否结束输入???结束请输入0,否则输入其他任意字符!!!" << endl;
		int flag = 0;//结束标志
		cin >> flag;
		if (flag == 0)//判断结束标志
		{
			break;
		}

	}
}

bool Judge_rip(RIP* t, string des_network, string route_name, string route_ip, int n)//单个路由器添加路由表的判断
{
	while (t != NULL)//路由表遍历
	{
		
		if (t->des_network == des_network)//网络地址判断
		{
			if (t->next_name == route_name)//下一跳名判断
			{
				//相同更新路由表
				t->next_ip = route_ip;
				t->rid_count = n;
				return false;
			}
			else
			{
				//不相同判断跳数
				if (t->rid_count <= n)//跳数大,不加入
				{
					return false;
				}
				else//跳数小,加入,改变路由表的信息
				{
					t->next_name = route_name;
					t->next_ip = route_ip;
					t->rid_count = n;
					return false;
				}
			}
		}

		t = t = t->rip_link;//路由表的遍历指针
	}

	return true;//没有这个网络地址返回true
}

void Add_rip(RouteNode* t, RouteNode* route)//单个路由器的路由表的修改和更新
{
	AdjNode* newadj = t->adj_route;//相邻路由器遍历指针
	RIP* newrip = NULL;//路由表的遍历指针

	while (newadj != NULL)//遍历该路由器额相邻路由器
	{
		RouteNode* adj_t = NULL;//局部变量路由器指针
		adj_t = Search_route(route, newadj->adj_name);//搜索该路由器的指针
		RIP* adj_rip = adj_t->ip_rip;//相邻路由器的路由表指针

		while (adj_rip != NULL)//本路由表的遍历修改和更新
		{
			if (Judge_rip(t->ip_rip, adj_rip->des_network, adj_t->route_name, newadj->adj_ip, adj_rip->rid_count + 1))//判断本路由表中是否存在该网络地址
			{
				newrip = new RIP(adj_rip->des_mask, adj_rip->des_network, newadj->adj_name, newadj->adj_ip, adj_rip->rid_count + 1);//分配空间
				newrip->rip_link = t->ip_rip;//改变路由表的指针指向,前插法
				t->ip_rip = newrip;
			}
			adj_rip = adj_rip->rip_link;//改变相邻路由器的路由表遍历指针指向
		}

		newadj = newadj->adj_link;//改变相邻路由器的遍历指针指向
	}
}

void Dis_vector(RouteNode* route, int num)//距离向量算法
{
	RouteNode* current = route;//路由器的遍历指针

	while (current != NULL)//路由器的遍历
	{
		AdjHost* newhost = current->adj_host;//直连主机指针

		while (newhost != NULL)//遍历路由器的直连主机
		{
			RIP* newrip = NULL;//路由表指针
			
			if (Judge_rip(current->ip_rip,newhost->host_network,"无", "路由器直连", 0))
			{
				newrip = new RIP(newhost->host_mask, newhost->host_network);//分配空间
				newrip->rip_link = current->ip_rip;//改变路由表的指针指向
				current->ip_rip = newrip;
			}
		    
			newhost = newhost->host_link;//遍历下一台直连主机
		}

		current = current->route_link;//遍历下一台路由器
	}

	int n = num;//距离向量算法的执行次数
	cout << "本人执行距离向量算法次数为路由器的数目" << endl;

	while (n--)//距离向量算法执行
	{
		cout << "第" << num - n << "次执行距离向量算法" << endl;
		current = route;//路由器遍历指针

		while (current != NULL)//循环结束条件
		{
			Add_rip(current, route);//修改和更新路由器的路由表
			current = current->route_link;//遍历下一台路由器
		}

		cout << "第" << num - n << "次距离向量算法执行完成" << endl;
		
		string flag = "";//输入单词距离向量算法的标志
		cout << "请问您是否想要查看此次距离向量算法执行完成的路由表?(是:1,否:其他任意字符)" << endl;
		cin >> flag;
		if (flag == "1")//判断单次距离向量算法是否输出
		{
			Display_all(route);//输出所有路由器的路由表
		}

	}
}

void Display_all(RouteNode* route)//输出所有路由器的路由表
{
	RouteNode* current = route;//遍历指针

	while (current != NULL)//路由器遍历结束条件
	{
		RIP* t = current->ip_rip;//路由表指针
		cout << "路由器" << current->route_name << "路由表结构为:" << endl;

		while (t != NULL)//路由表遍历结束条件
		{
			cout << "目的网络地址为: " << t->des_network << "  目的网络的子网掩码为: " << t->des_mask << "  下一跳为: " << t->next_name << "  下一跳IP地址为: " << t->next_ip << "  跳数为: " << t->rid_count << endl;
			t = t->rip_link;//改变路由表的指针指向
		}

		current = current->route_link;//改变路由器的指针指向
	}
}

void Display_route(RouteNode* route)//输出单个路由器的路由表
{
	RouteNode* current = route;//路由器指针
	RIP* t = current->ip_rip;//路由表遍历指针
	cout << "路由器" << current->route_name << "路由表结构为:" << endl;

	while (t != NULL)//路由表遍历结束条件
	{
		cout << "目的网络地址为: " << t->des_network << "  目的网络的子网掩码为: " << t->des_mask << "  下一跳为: " << t->next_name << "  下一跳IP地址为: " << t->next_ip << "  跳数为: " << t->rid_count << endl;
		t = t->rip_link;//改变路由表的指针指向
	}
}

void delete_route(RouteNode* route)//路由器故障
{
	cout << "我只设置了一种故障,即某个路由器的接口损坏" << endl;
	cout << "请输入那条链路的路由器发生故障." << endl;
	string route_name1 = "", route_name2 = "";//路由器名
	cout << "链路上发生故障的路由器的名字为:" << endl;
	RouteNode* t1 = NULL;//路由器搜索指针

	while (1)//判断非法输入
	{
		cin >> route_name1;
		t1 = Search_route(route, route_name1);//搜索路由器位置
		if (t1 == NULL)
		{
			cout << "不存在该路由器!!!请重新输入." << endl;
		}
		else
		{
			break;
		}
	}

	cout << "链路上另外一个相连的路由器的名字为:" << endl;
	RouteNode* t2 = NULL;//路由器搜索指针

	while (1)//判断非法输入
	{
		cin >> route_name2;
		t2 = Search_route(route, route_name2);//搜索路由器位置
		if (t2 == NULL)
		{
			cout << "不存在该路由器!!!请重新输入." << endl;
		}
		else
		{
			break;
		}
	}

	cout << "路由器" << t1->route_name << "发生故障前的接口IP地址为: ";
	string adj_ip = "";//新街口IP地址
	AdjNode* fault_route = t1->adj_route;//故障的路由器的相邻路由器指针

	while (fault_route != NULL)//遍历寻找故障接口
	{
		if (fault_route->adj_name == route_name2)//寻找接口条件
		{
			cout << fault_route->route_ip << endl;
			cout << "请输入新的接口的IP地址:" << endl;
			fault_route->route_ip = "";//清空原IP地址
			cin >> fault_route->route_ip;//输入新的IP地址
			break;
		}
		fault_route = fault_route->adj_link;//改变遍历指向
	}

	AdjNode* t_route = t2->adj_route;//另外一个路由器的相领的路由器指针

	while (t_route != NULL)//遍历寻找故障街口
	{
		if (t_route->adj_name == route_name1)//寻找故障接口
		{
			t_route->adj_ip = "";//清空原IP地址
			t_route->adj_ip = fault_route->route_ip;//输入新接口的IP地址
		}
		t_route = t_route->adj_link;//改变遍历指向
	}

	fault_route = NULL;//消除野指针
	t_route = NULL;//消除野指针

	cout << "重新执行距离向量算法!!!" << endl;
	
	RouteNode* current = route;//路由器遍历指针

	while (current != NULL)//执行单次距离向量算法
	{
		Add_rip(current, route);//修改和更新路由表
		current = current->route_link;//改变路由器遍历指向
	}

	cout << "距离向量算法执行完毕." << endl;
	
	string flag = "";//距离向量算法输出标志
	cout << "是否输出执行完毕的路由表???(是:1,否:其他任意字符)" << endl;
	cin >> flag;
	if (flag == "1")//判断是否输出更新完毕的路由表
	{
		Display_all(route);//输出所有路由器的路由表
	}
}

void menu(RouteNode*& route, int& n)//菜单
{
	cout << "初始化路由拓扑." << endl;
	cout << "请输入以下信息." << endl;
	Input(route, n);//输入路由器的拓扑图
	cout << "执行距离向量算法." << endl;
	Dis_vector(route, n);//执行距离向量算法
	cout << "初始化路由表完成!!!" << endl;

	while (1)//菜单输出
	{
		cout << "菜单" << endl;
		string choice = "";//功能选择序号
		cout << "1.输出所有路由器的路由表" << endl;
		cout << "2.输出单个路由的路由表" << endl;
		cout << "3.迫使某个路由发生故障" << endl;
		cout << "请输入功能序号:" << endl;

		while (1)//功能执行以及非法输入检测
		{
			cin >> choice;
			if (choice == "1")//功能1
			{
				Display_all(route);//输出所有路由器的路由表
				break;
			}
			else
			{
				if (choice == "2")//功能2
				{
					cout << "请输入你要查询的路由器的名字:" << endl;
					string name = "";//路由器名

					while (1)//路由器名非法检查
					{
						cin >> name;
						RouteNode* t = Search_route(route, name);//搜索路由器的位置
						if (t == NULL)
						{
							cout << "没有该路由器,请重新输入!!!" << endl;
						}
						else
						{
							Display_route(t);//输出该路由器的路由表
							break;
						}
					}
					break;
				}
				else
				{
					if (choice == "3")//功能3
					{
						delete_route(route);//设置路由器故障
						break;
					}
					else
					{
						cout << "非法输入,请重新输入!!!" << endl;
					}
				}
			}
		}

		cout << "您是否进行其他的操作???进行输入其他任意字符串,不进行请输入0." << endl;
		string flag = "";//结束循环标志
		cin >> flag;
		if (flag == "0")//判断循环结束标志
		{
			break;
		}
	}
	cout << "程序执行结束,谢谢使用!!!" << endl;
}

int main()
{
	RouteNode* route_head = NULL;//路由器的头指针
	int num = 0;//路由器的数目
	menu(route_head, num);//菜单
	return 0;
}
```
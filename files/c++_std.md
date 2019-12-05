						C++


声明：使得名字为程序所知，如果想使用该变量，则必须实现包含其声明。声明会确定变量的名字和类型。
      如果想声明一个变量而不想定义它，那么可以在变量名前加extern关键字。
定义：创建于名字关联的实体。定义会申请存储空间，可能会赋予初始值。


字符串的前后缀：
前缀：
	u	unicode16字符
	U	unicode32字符
	L	宽字符
	u8	utf-8
后缀：
	u/U	表示该字面值为无符号类型
	l/L	表示该字面值的类型至少为long
	ll/LL	表示该字面值的类型至少为long long
	f/F	表示该字面值为float类型
字符用单引号'',字符串双引号""


默认初始化问题：
三条性质：1、定义在函数体外的变量会被初始化为0
	  2、定义在函数体内部的变量不会被初始化
	  3、类的对象未被初始化，则初值由类决定


引用即别名，引用对象的初始值必须是int型对象，其中一个的值发生改变时另一个也改变，
引用只能绑定在对象上，而不能与某个字面值或某个表达式的计算结果绑定在一起，引用本身
不是一个对象，所以不能定义引用的引用。

指针也实现了对其他对象的间接访问，指针本身就是一个对象，允许对指针赋值和拷贝，而且在指针的生命周期内它可以先后指向几个不同的对象；指针无需在定义时赋初值。指针存放某个对象的地址，要想获取该地址，需要使用取地址符(&)，指针的类型要和它所指向的对象严格匹配。如果指针指向来一个对象，则允许使用解引用符(*)来访问该对象，
viod*指针可以存放任意对象的地址。
写法：  int* a;		int *a, *b;
	int* b;

const限定符：定义的变量不能被修改，默认状态下，const对象仅在文件内有效，如果想在多个文件中共享const对象，必须在变量的定义前添加extern关键字。const定义的变量必须初始化(可以在运行时初始化)；对常量的引用。


指针与const：
	1：指向常量的指针：要想存放变量的地址，必须使用指向常量的指针：const int *p
	2:常量指针：指针自身是一个对象，不可变。int *const p = &pi   （常量指针必须初始化！！！）
	3：指向常量的常量指针。const  int *const p (不仅指针本身不可变，指针所指对象也不可变)
引用与const：
	将引用定义为常量：const int &i

用名词顶层const表示指针本身是个常量，可以表示任意的对象是常量；底层const表示指针所指的对象是一个常量，
与指针和引用等符合类型的基本类型部分有关。
eg：
	int i = 0;
	int *const p1 = &i; //不能改变p1的值，这是一个顶层const(常量指针)
	const int ci = 42;  //不能改变ci的值，这是一个顶层const
	const int *p2 = &ci; //允许改变p2的值，这是一个底层const(指向常量的指针)
	const int *const p3 = p2; //靠右的const是顶层const，靠左的是底层const
	const int &r = ci;   //用于声明引用的const都是底层const
知识点：顶层const的拷贝不受限制，但是底层const的拷贝的对象必须具有相同的底层const资格。一般来说：非常量可以赋值给常量，反之则不行
如果认定变量是一个常量表达式，那就把它声明成constexpr类型   字面值类型
constexpr仅对指针有效，与指针所指的对象无关：
	const int *p = nullptr;		//p是一个指向整型常量的指针(顶层const)
	constexpr int *q = nullptr;	//q是一个指向整数的常量指针(底层const)

··············································································
顶层const和底层const的理解：
	1、指向常量的指针，即底层const，不能通过解引用符改变它所指的内容
		int a= 1;
		int const *p_a = &a;	//底层const
		*p_a = 2;	//错误
	2、指针常量，代表指针本身是常量，声明时必须初始化，const放在*后面，顶层const
		int b = 1;
		int *const p_b = &b;	//顶层const
		p_b = &a;		//错误，常量指针不能改变储存的地址值
···············································································


类型别名：typedef
	typedef double wzj;
     
	typedef char *pstring;
	const pstring cstr=0; //const pstring，pstring是指向char的指针，const pstring就是指向char的常量指针，而非指向常量字符的指针。
别名声明：using
	using new_name old_name;


auto  一般会忽略顶层const，而底层const会保留下来；auto定义的变量必须有初始值

decltype()	decltype(f()) sum = x;  //sum的类型就是函数f的返回类型
	const int ci = 0, &cj = ci;
	decltype(ci) x = 0;	//x的类型是const int
	decltype(cj) y = x;	//y的类型是const int&，y绑定到变量x
	->decltype(cj) z;    	//错误：z是一个引用，必须初始化
decltype和引用  //decltype的结果可以是引用类型
	int i = 42, *p = &i, &r = i;
	->decltype(r + 0) b;    //正确，加法的结果是int，因此b是一个(未初始化的)int
	decltype(*p) c;      //错误，c是int&，必须初始化
->区别：1.因为r是一个引用，因此decltype(r)的结果是引用类型;r+0表达式的结果将是一个具体值而非一个引用
	2.如果表达式的内容是解引用操作，则decltype将得到引用类型，decltype(*p)的结果类型就是int&而非int
	3.decltype((variable))的结果永远是引用(必须初始化)，而decltype(variable)结果只有当variable本身就是一个引用时才是引用
赋值的表达式语句本身是一种引用

decltype与auto区别：
	1：如果使用引用类型，auto会识别为其所指对象的类型，decltype则会识别为引用的类型。
	2：decltype(())双括号的差别。


预处理器：#include ××  预处理变量无视c++语言中关于作用域的规则
头文件保护符号：
	#ifndef ××_××_×
	#define ××_××_×
	/*
	 *
	*/
	#endif
头文件不应包含using声明

string：
	getline
	empty size
string::size_type类型：
	auto len = line.size(); //len的类型是string::size_type   unsigned
	如果一条表达式中已经有了size()函数就不要再使用int了，避免混用int与unsigned
eg：size函数返回的是一个无符号整型数，假如n是一个具有负值的int，则表达式s.size()<n的判断结果几乎肯定是true，因为负值n会自动转换成一个比较大的无符号值。
字符串字面值与string是不同的类型，两个字符串字面值不能直接相加
eg：string s1="hello", s2="world";
    string s5="hello"+","  //错误
    string s6=s1+","+"world" //正确
    string s7="hello"+","+s2  //错误


(
c++11 条款19：使用std::shared_ptr来进行共享所有权的资源管理
1.std::shared_ptr提供了对共享所有权的任意资源的生命周期的很方便的管理。

2.和std::unique_ptr相比，std::shared_ptr尺寸增加了一倍，同时因为控制块增加了负担，并且需要原子操作的引用计数。

3.默认的资源释放是通过delete，但是也支持定制删除器。删除器的类型对std::shared_ptr本身的类型无影响。

4.避免从原始指针变量去构造std::shared_ptr。)


c++中的static_cast和dynamic_cast强制类型转换：
一、static_cast(编译时类型检查)：static_cast<type-id>(expression),该运算把expression转化为type-id类型
（1）用于基本数据类型之间的转换，如把int转换为char，把int转换成enum，但这种转换的安全性需要开发者自己保证（这可以理解为保证数据的精度，即程序员能不能保证自己想要的程序安全），如在把int转换为char时，如果char没有足够的比特位来存放int的值（int>127或int<-127时），那么static_cast所做的只是简单的截断，及简单地把int的低8位复制到char的8位中，并直接抛弃高位。

（2）把空指针转换成目标类型的空指针

（3）把任何类型的表达式类型转换成void类型

（4）用于类层次结构中父类和子类之间指针和引用的转换。
对于以上第（4）点，存在两种形式的转换，即上行转换（子类到父类）和下行转换（父类到子类）。对于static_cast，上行转换时安全的，而下行转换时不安全的，为什么呢？因为static_cast的转换时粗暴的，它仅根据类型转换语句中提供的信息（尖括号中的类型）来进行转换，这种转换方式对于上行转换，由于子类总是包含父类的所有数据成员和函数成员，因此从子类转换到父类的指针对象可以没有任何顾虑的访问其（指父类）的成员。而对于下行转换为什么不安全，是因为static_cast只是在编译时进行类型检查，没有运行时的类型检查，具体原理在dynamic_cast中说明。
二、dynamic_cast关键字(运行时类型检查)








标准库类型vector：属于类模板(c++还有函数模板)
	表示对象的集合，其中所有对象的类型都相同，也常被称作容器 #include <vector>，编辑器根据模板创建类或函数的过程称为实例化
初始化：拷贝初始化和列表初始化
	1、拷贝初始化只能提供一个初始值
	2、类内初始值只能使用拷贝初始化或使用花括号的形式初始化
	3、初始元素值为列表，只能使用花括号进行列表初始化而不能使用圆括号
	vector<string> svec(10,"hi!");  //10个string类型的元素，每个都被初始化为"hi!"
	vector<int> vi=10;	//错误，必须使用直接初始化的形式指定向量的大小
	vector<int> v1(10);	//v1有10个元素，每个的值都是0
	vector<int> v2{10};	//v2有1个元素，值为10
	vector<int> v3(10,1);	//v3有10个元素，每个的值都是1
知识点：vector的初始化：
1：引用不可以成为vector的元素，因为其不是对象。
2：可以用花括号初始化每一个值。
3：可以用括号指定元素个数或相同的元素值。
4：只能使用直接初始化，不可以使用拷贝初始化（vector之间的拷贝是可行的，但要保证类型相同）
通常定义一个空的vector，使用push_back方式向其添加元素

范围for语句体内不应该改变其遍历序列的大小
vector对象(以及string对象)的下标运算符可用于访问已存在的元素，而不能用于添加元素
通过下标访问不存在的元素会导致缓冲区溢出，确保下标合法的一种有效手段就是尽可能使用范围for语句






迭代器：
	1、所有标准库容器都可以使用迭代器，但是其中只有少数几种才同时支持下标运算符；
	2、有效的迭代器或者指向某个元素，或者指向容器中尾元素的下一位置，其他所有情况都属于无效；
	3、严格来说，string对象不属于容器类型，但是string支持很多与容器类型类似的操作；
	4、如果容器为空，则begin和end返回的是同一个迭代器，都是尾后迭代器。
因为end返回的迭代器并不实际指示某个元素，所以不能对其进行递增或解引用的操作
迭代器的运算符：
	*iter			返回迭代器iter所指元素的引用
	iter->mem		解引用iter并获取该元素的名为mem的成员，等价于(*iter).mem
	++iter			令iter指示容器的下一个元素
	--iter			......
	iter1==iter2		判断两个迭代器是否相等(不相等)，如果两个迭代器指示的是同一个元素
	iter1!=iter2		或者它们是同一个容器的尾后迭代器，则相等；反之，不相等
for循环中，c++程序员习惯性地使用!=，其原因和他们更愿意使用迭代器而非下标的原因一样

迭代器类型：
	拥有迭代器的标准库类型使用iterator和const_iterator来表示迭代器的类型
	vector<int>::iterator it;	//it能读写vector<int>的元素
	string::iterator it2;		//it2能读写string对象中的字符
	vertor<int>::const_iterator it3	//it3只能读元素，不能写元素
	string::const_iterator it4	//......
每个容器类定义了一个名为iterator的类型，该类型支持迭代器概念所规定的一套操作

begin和end运算符：
	vector<int> v;
	const vector<int> cv;
	auto it1 = v.begin();		//it1的类型是vector<int>::iterator
	auto it2 = cv.begin();		//it2的类型是vector<int>::const_iterator
为了便于专门得到const_iterator类型的返回值，C++11新标准引入了两个新函数，分别是cbegin和cend

it->mem和(*it).mem表达的意思相同(it是一个迭代器)，箭头运算符(->)把解引用和成员访问两个操作结合在一起

*****但凡是使用了迭代器的循环体，都不要向迭代器所属的容器添加元素
只要两个迭代器指向的是同一个容器中的元素或者尾元素的下一位置，就能将其相减，所得的结果是两个迭代器的距离
所谓距离指的是右侧的迭代器向前移动多少位置就能追上左侧的迭代器，其类型是名为difference_type带符号整数型






数组：
一种类似于标准库类型vector的数据结构；
	相同点：也是存放类型相同的对象的容器，这些对象本身没有名字，通过其所在的位置访问
	不同点：数组的大小固定不变，不能随意向数组中增加元素
	tip ->   如果不清楚元素的确切个数，使用vector

WARNING ->  和内置类型的变量一样，如果在函数内部定义了某种内置类型的数组，那么默认初始化会令数组含有
	    未定义的值
定义数组的时候必须指定数组的类型，不允许用auto关键字由初始值的列表推断类型，另外和vector一样，数组的
元素应为对象，因此不存在引用的数组

字符数组的特殊性：
	用字符串字面值初始化数组时，一定要注意字符串字面值的结尾处还有一个空字符，这个空字符也会像
	字符串的其他字符一样被拷贝到字符数组中去
列表初始化：
	char a1[]={'c', 'b', 'f'};		//列表初始化，没有空字符   维度为3
	char a2[]={'c', 'b', 'f', '\0'};	//列表初始化，含有显示的空字符  维度为4
字符串字面值初始化：
	char a3[]="c++";			//自动添加表示字符串结束的空字符  维度为4
	char a4[6]="Daniel";			//错误，没有空间可存放空字符

数组之间不允许拷贝和赋值：
	int a[] = {0, 1, 2};
	int a2[] = a;		//错误，不允许使用一个数组初始化另一个数组
	a2 = a;			//错误，不能把一个数组直接赋值给另一个数组
一些编译器支持数组的赋值，这就是所谓的 编译器扩展 ，但一般来说最好避免使用非标准特性
理解数组声明的含义，最好的办法是从数组的名字开始按照由内向外，从右到左的顺序阅读，左边为类型
使用数组下标时通常将其定义为size_t类型，size_t是一种机器相关的无符号类型




指针和数组：
在大多数表达式中，使用数组类型的对象其实是使用一个指向该数组首元素的指针
int ia[] = {0,1,2,3,4,5,6,7,8,9};	//ia是一个含有10个整数的数组
auto ia2(ia);				//ia2是一个整型指针，指向ia的第一个元素
ia2 = 42;				//错误，ia2是一个指针，不能用int值给指针赋值
当使用decltype关键字时上述转换不会发生，decltype(ia)返回的类型是由10个整数构成的数组

指针也是迭代器：
	vector与string的迭代器支持的运算，数组的指针全都支持；遍历数组时需要先获取指向数组第一个
元素的指针和指向数组尾元素的下一位置的指针，与尾后迭代器一样，尾后指针也不指向具体的元素，因此，
不能对尾后指针执行解引用或递增的操作。

标准库函数begin和end：   定义在iteraor头文件中
	这两个函数与容器中的两个同名成员功能类似，不过数组毕竟不是类类型，因此这两个函数不是成员函数，
	正确的使用形式是将数组作为它们的参数
	int ia[] = {0,1,2,3};
	int *beg = begin(ia);	//指向ia首元素的指针
	int *last = end(ia);	//指向arr尾元素的下一位置的指针

constexpr size_t sz = 5;
int arr[sz] = {1,2,3,4,5};
int *ip = arr;			//等价于int *ip = &arr[0]
int *ip2 = ip + 4;		//ip2指向arr的尾元素arr[4]
	//正确，arr转换成指向它首元素的指针，p指向arr尾元素的下一位置
int *p = arr + sz;		//使用警告：不要解引用
int *p2 = arr + 10;		//错误，arr只有5个元素，p2的值未定义
	两个指针相减的结果的类型是一种名为ptrdiff_t的标准库类型，和size_t一样也是定义在cstddef头文件
	中的机器相关的类型，带符号类型

解引用与指针运算的交互：
	int ia[] = {0,2,4,6,8};	//含有5个整数的数组
	int last = *(ia + 4);	//把last初始为8,也就是ia[4]的值,ia前进4个元素后的新地址，然后解引用
	int last2 = *ia + 4;	//last2初始为4等价于ia[0]+4
内置的下标运算符所用的索引值不是无符号类型，这一点与vector和string不一样

字符串字面值即C风格字符串，按此习惯书写的字符串存放在字符串数组中并以空字符结束('\0')，一般利用指针来操作这些字符串，C风格字符串的函数：strlen  strcmp  strcat  strcpy
C++风格的字符串比较是字符串本身的比较，C风格的字符串比较是字符串首地址的比较

Tip -> 对大多数应用来说，使用标准库string要比使用C风格字符串更安全、更高效。

不能用string对象直接初始化指向字符的指针，为了完成该功能，string专门提供了一个名为c_str的成员函数：
	string s("hello world");
	char *str = s;			//错误，不能用string对象初始化char*
	const char *str = s.c_str();	//正确
WARNING：如果执行完c_str()函数后程序想一直都能使用其返回的数组，最好将该数组重新拷贝一份

**使用数组初始化vector对象
	只需要指明要拷贝区域的首元素地址和尾元素地址
	int int_arr[] = {0,1,2,3,4,5};
	vector<int> ivec(begin(int_arr), end(int_arr));
	vector<int> ivec(int_arr, int_arr+6);			//与上一句等价
	vector<int> subvec(int_arr+1, int_arr+4);		//拷贝三个元素，int_arr[1] [2] [3]


***现代的C++程序应当尽量使用vector和迭代器，避免使用内置数组和指针；应当尽量使用string，避免使用C风格
    的基于数组的字符串。



多维数组：	tip    多维数组其实是数组的数组
C++11新标准中新增了范围for语句
	constexpr size_t rowCnt = 3, colCnt = 4;
	int ia[rowCnt][colCnt];
	for (size_t i = 0; i != rowCnt; i++)
	{
	    for (size_t j = 0; j != colCnt; j++)
	    	{
		    ia[i][j] = i * colCnt + j;
		}
	}
等价于：
	size_t cnt = 0;
	for (auto &row : ia)	//对于最外层数组的每一个元素，仍然为数组，使用引用避免row被自动转成指针
	    for (auto &col : row)
	    {
		col = cnt;
		++cnt;
	    }

***Note ->    要使用范围for语句处理多维数组，除了最内层的循环外，其他所有循环的控制变量都应该是引用类型



****指针和多维数组(始终记住多维数组其实是数组的数组)
	int ia[3][4];
	int (*p)[4] = ia;
	p = &ia[2];
	for (auto p = ia; p !=ia+3; ++p)
	{
	    for (auto q = *p; q != *p+4; ++q)
		cout << *q << ' ';
	    cout << endl;
	}
for循环可使用标准库函数begin和end实现
	for (auto p = begin(ia); p != end (ia); ++p)
	{
	    for (auto q = begin(*p); q != end(*p); ++q)
		cout << *q << ' ';
	    cout << endl;
	}
类型别名简化多维数组的指针

左值和右值：
	左值可以位于赋值语句的左侧，右值则不能；当一个对象被用作右值的时候，用的是对象的值(内容)，当对象被用作左值的时候，用的是对象的身份(在内存中的位置)。在需要右值的地方可以用左值来替代，但是不能把右值当成左值使用，当一个左值被当成右值使用时，实际使用的是它的内容。
	1、取地址符作用于一个左值运算对象，返回一个指向该运算对象的指针，这个指针是个右值
？？？	2、使用decltype时，如果表达式的求值结果是左值，decltype作用于该表达式(不是变量)得到一个
	   引用类型 --> p的类型是int*，因为解引用运算符生成左值，所以decltype(*p)的结果是int&；因为取
	   地址符生成右值，所以decltype(&p)的结果是int**，指向整型指针的指针。

优先级：算数 > 关系 > 逻辑
***Note  因为赋值运算符的优先级低于关系运算符的优先级，所以在条件语句中，赋值部分通常应该加上括号
***WARNING    进行比较运算时除非比较的对象是布尔类型，否则不要使用布尔字面值true和false作为运算对象
	    if (val) {/*...*/}		如果val不是bool类型的if (val == true) {/*...*/} 相当于
	    if (!val) {/*...*/}		if (val == 1) {/*...*/}


*****递增和递减运算符
	除非必须，否则不用递增递减运算符的后置版本
	在一条语句中混用解引用和递增运算符
	auto pbeg = v.begin();
	while (pbeg != v.end && *beg >= 0)
	    cout << *pbeg++ << endl;
	//后置递增运算符的优先级高于解引用运算符，*pbeg++等价于*(pbeg++)

运算对象可按任意顺序求值：
	for (auto it = s.begin; it != s.end() && !isspace(*it); ++it)
		*it = toupper(*it);	//将当前字符改为大写字符

	while (beg != s.end() && !isspace(*beg))
		*beg = toupper(*beg++);	//错误，该赋值语句未定义
	//赋值运算符左右两端都用到了beg的值，求值顺序不一定



***成员访问运算符
	ptr->mem等价于(*ptr).mem
	string s1 = "a string", *p = &s1;
	auto n = s1.size();	//运行string对象s1的size成员
	n = (*p).size();	//运行p所指对象的size成员
	n = p -> size();	//等价于(*p).size()
**解引用运算符的优先级低于点运算符：*p.size()	//错误，p是一个指针，它没有名为size的成员

WARNING   随着条件运算嵌套层数的增加，代码的可读性急剧下降，因此条件运算的嵌套最好别超过两到三层
	  条件运算符的优先级非常低，通常需要在它两端加上括号





*****位运算符
WARNING	    	关于符号位如何处理没有明确的规定，所以强烈建议仅将位运算符用于处理无符号类型


*****sizeof运算符   满足右结合率  所得的值是size_t类型
	sizeof (type)
	sizeof expr    并不实际计算运算对象的值
	返回一个类型名字或一条表达式所占的字节数
	Sales_data data, *p;
	sizeof *p;		等价于sizeof (*p),因为sizeof并不实际求运算对象的值，所以p是一个
				无效的指针该表达式仍然是一种安全的行为,不需要解引用就能知道其类型
	
	对数组执行sizeof运算得到整个数组所占空间的大小，等价于对数组中所有的元素各执行一次sizeof运算
	并将所得到的结果求和，sizeof运算不会把数组转换指针来处理，可以用数组的大小除以单个元素的大小
	得到数组中元素的个数
	constexor size_t sz = sizeof(ia)/sizeof(*ia);
	int arr2[sz];		//正确，sizeof返回一个常量表达式
	

	//sizeof对数组使用，返回的是数组的大小；对指针使用，返回的是指针本身的大小
	  结果sizeof(x)=10*4  sizeof(*x)=4  sizeof(p)=8(64位机器)  sizeof(*p)=sizeof(int)=4
	int x[10]; int *p = x;
	cout << sizeof(x)/sizeof(*x) << endl;
	cout << sizeof(p)/sizeof(*p) << endl;

***逗号运算符
	经常用于for循环中



*****类型转换
算术转换 -> 整型提升
其他隐式类型转换
	数组转换成指针：大多数用到数组的表达式中，数组自动转换成指向数组首元素的指针，当数组被用作
			decltype关键字的参数，或者取地址符(&)、sizeof及typeid等运算符的运算对象时，
			转换不会发生，用一个引用来初始化数组转换也不会发生。
	指针的转换：常量整数值0或者字面值nullptr能转换成任意指针类型；指向任意非常量的指针能转换成void*;
		    指向任意对象的指针能转换成const void*。
	转换成布尔类型：如果指针或算术类型的值为0,转换结果是false；否则为true
	转换成常量：
		int i;
		const int &j = i;	//非常量转换成const int的引用
		const int *p = &i;	//非常量的地址转换成const的地址
		int &r = j, *q = p;	//错误，不允许const转换成非常量

命名的强制类型转换：cast-name<type>(expression);
	type是转换的目标类型而expression是要转换的值，如果type是引用类型，则将结果是左值
	cast-name是static_cast,dynamic_cast,const_cast和reinterpret_cast中的一种，
	dynamic_cast支持运行时类型识别

	static_cast：任何具有明确定义的类型转换，只要不包含底层const，都可以使用static_cast
		-->把一个较大的算术类型赋值给较小的类型时
		-->对于编译器无法自动执行的类型转换

	const_cast：只能改变运算对象的底层const
		const char *pc;
		char *p = const_cast<char*>(pc);	//正确，但是通过p写值是未定义的行为
		
		const char *cp;
		//错误，static_cast不能转换掉const性质
		char *q = static_cast<char*>(cp);
		static_cast<string>(cp);	//正确，字符串字面值转换成string类型
		const_cast<string>(cp);		//错误，const_cast只能改变常量的属性
	
	reinterpret_cast：通常为运算对象的位模式提供较低层次上的重新解释
旧式的强制类型转换：
	type(expr);	//函数形式的强制类型转换
	(type) expr;	//C语言风格的强制类型转换
WARNING：建议避免使用强制类型转换




*****第五章  语句
****简单语句
    表达式语句、空语句
	//重复读入数据直至达到文件末尾或某次输入的值等于sought
	while (cin >> s && s != sought)
		;	//空语句

	//额外的分号，循环体是那条空语句
	while (iter != svec.end()) ;	//while循环体是那条空语句
		++iter;			//递增运算不属于循环的一部分

	使用空语句时应加上注释，从而令读这段代码的人知道该语句是有意省略的；多余的空语句并非总是无害的

    复合语句(块)：用花括号括起来的语句和声明的序列，复合语句也称作块，不以分号作为结束

****语句的作用域

****条件语句
    ***if语句
	为了避免块的作用域出错，通常在if和else之后必须写上花括号(while和for语句也一样)
    悬垂else：当一个if语句嵌套在另一个if语句内部时，很可能if分支会多于else分支，我们怎么知道某个
	      给定的else是和哪个if匹配，C++规定else与其最近的尚未匹配的if匹配。
	//错误，实际上执行过程并非像缩进格式显示的那样，else分支匹配的是内层if语句
	if (grade % 10 >= 3)
		if (grade % 10 > 7)
			lettergrade += '+';  //末尾是8或者9的成绩添加一个加号
	else
		lettergrade += '-';	     //末尾是3、4、5、6或者7的成绩添加一个减号！
	这里的else其实是内层if语句的一部分
	解决：使用花括号控制执行路径
	if (grade % 10 >= 3)
	{
		if (grade % 10 > 7)
			lettergrade += '+';	//末尾是8或者9的成绩添加一个加号
	}
	else
		lettergrade += '-';		//末尾是0、1或者2的成绩添加一个减号
    ***switch语句
	case关键字和它对应的值一起被称为case标签
	-> case标签必须是整型常量表达式
	-> 任何两个case标签的值不能相同
	-> 如果某个case标签匹配成功，将从该标签开始往后顺序执行所有case分支，除非程序显示地中断了这
	    这一过程，否则直到switch的结尾处才会停下来，一般下一个case标签之前应该有一条break语句
	//如果ch是一个元音字母，将相应的计数值加1
	switch (ch)
	{
		case 'a': case 'e': case 'i': case 'o': case 'u':
		    ++vowelCnt;
		    break;
		default:
		    ++otherCnt;
		    break;
	}
	标签不应该孤零零地出现，它后面必须跟上一条语句或者另外一个case标签，如果switch结构以一个空的
	default标签作为结束，则该default标签后面必须跟上一条空语句或一个空块。

****迭代语句
   ***while语句
	while (condition)
	    statement
	定义在while条件部分或者while循环体内的变量每次迭代都经历从创建到销毁的过程
	当不确定到底要迭代多少次时，或者想在循环结束后访问循环控制变量，使用while循环比较合适
   ***传统的for语句
	for (init-statemen; condition; expression)
	    statement
	init-statemen必须是以下三种形式中的一种：声明语句、表达式语句或者空语句
   ***范围for语句
	C++11新标准引入了一种更简单的for语句，这种语句可以遍历容器或其他序列的所有元素
	for (declaration : expression)
	    statement
	expression表示的必须是一个序列，比如用花括号括起来的初始值列表、数组或者vector或string
	等类型的对象，这些类型的共同特点是拥有能返回迭代器的begin和end成员。
	vector<int> v = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};
	//范围变量必须是引用类型，这样才能对元素执行写操作
	for (auto &r : v)	//对于v中的每一个元素
	r *= 2;			//将v中每个元素的值翻倍
	等价于传统for语句：
	for (auto beg = v.begin(), end = v.end(); beg != end; ++beg)
	{
		auto &r = *beg;		//r必须是引用类型，这样才能对元素执行写操作
		r *= 2;			//将v中每个元素的值翻倍
	}
	这就是为什么不能通过范围for语句增加vector对象(或其他容器)的元素的原因，范围for语句中，
	预存了end()的值，一旦在序列中添加(删除)元素，end函数的值就可能变得无效了。
   ***do while语句
	do
	    statement
	while (condition);
	do while语句和while语句非常相似，惟一的区别是，do while语句先执行循环体后检查条件，不管条件
	的值如何，至少执行循环一次。
	do while语句应该在括号包围起来的条件后面用一个分号表示语句结束
	condition不能为空，condition使用的变量必须定义在循环体之外

****跳转语句
	C++提供了4种跳转语句：break、continue、goto、return
     ***break语句
	负责终止离它最近的while、do while、for或swith语句，并从这些语句之后的第一条语句开始继续执行
     ***continue语句
	只能出现在for、while和do while循环的内部，或者嵌套在此类循环里的语句或块的内部，只有当switch
	语句嵌套在迭代语句内部时，才能在switch里使用continue
     ***goto语句
	从goto语句无条件跳转到同一函数内的另一条语句
	(不要在程序中使用goto语句，因为它使得程序既难理解又难修改)
	goto label;
	其中label是带标签语句，如end: return;	//end可以作为goto的目标
	goto语句和控制权转向的那条带标签的语句必须位于同一个函数体内
	向前跳转时注意之间的变量初始化的定义也被跳过了，向后跳转则销毁该变量然后重新创建它

****try语句块和异常处理
    ***throw表达式
    ***try语句块
    ***标准异常
	C++标准库定义了一组类，用于报告标准库函数遇到的问题，定义在4个头文件中：
	1、exception头文件定义了最通用的异常类exception
	2、stdexcept头文件定义了几种常用的异常类
	    exception,runtime_error,range_error,overflow_error,underflow_error,logic_error,
	    domain_error,invalid_argument,length_error,out_of_range
	3、new头文件定义了bad_cast异常类型
	4、type_info头文件定义了bad_cast异常类型
	异常类型只定义了一个名为what的成员函数，该函数没有任何参数，返回值是一个指向C风格字符串的
	const char*。what函数返回的C风格字符串的内容与异常对象的类型有关，如果异常类型有一个字符串
	初始值，则what返回该字符串；对于其他无初始值的异常类型来说，what返回由编译器决定。



*****第6章  函数

****函数基础
	一个典型的函数定义包括以下部分：返回类型、函数名字、由0个或者多个形参组成的列表以及函数体，
	其中，形参以逗号隔开，形参列表位于一对圆括号内。
      **形参和实参	
      **函数的形参列表
	为了与C语言兼容，使用void关键字表示函数没有形参
	void f1() {/*...*/}	//隐式地定义空形参列表
	void f2(void) {/*...*/}	//显示地定义空形参列表
	每个形参都是含有一个声明符的声明，即使类型一样也要写出来：	
		int f3(int v1, int v2) {/*...*/}
      **函数的返回类型
	void表示函数不返回任何值，函数的返回类型不能是数组类型或函数类型，但可以是指向数组或函数的指针
    ***局部对象
	C++语言中名字有作用域，对象有生命周期
      **自动对象
      **局部静态对象	static
    ***函数声明
	函数只能定义一次，但可以声明多次，函数的三要素(返回类型，函数名，形参列表)描述了函数的接口，说明
	了调用该函数所需的全部信息。函数声明也称作函数原型
      **在头文件中进行函数声明
	含有函数声明的头文件应该被包含到定义函数的源文件中
    ***分离式编程
	允许把程序分割到几个文件中去，每个文件独立编译

****参数传递
	形参初始化的机理和变量初始化一样，当形参是引用类型时，我们说它对应的实参被引用传递或者函数被
	传引用调用；当实参的值被拷贝给形参时，形参和实参是两个相互独立的对象，我们说这样的实参被值传递
	或者函数被传值调用。
    ***传值参数(拷贝和指针形参)
	熟悉C的程序员常常使用指针类型的形参访问函数外部的对象。在C++中，建议使用引用类型的形参替代指针，
    ***传引用参数
	使用引用避免拷贝，如果函数无须改变引用形参的值，最好将其声明为常量引用。
	使用引用形参返回额外信息，让函数返回多个值的方法：
		1、定义一个新的数据类型，包含多个参数
		2、给函数传入额外的参数，令其保存额外的参数，隐式的返回
    ***const形参和实参
	指针或引用形参与const
	可使用非常量初始化一个底层const对象，但是反过来不行，一个普通的引用必须用同类型的对象初始化
	int i = 42;
	const int *cp = &i;	//正确，但是cp不能改变i
	const int &r = i;	//正确，但是r不能改变i
	const int &r2 = 42;	//正确，可以使用字面值初始化常量引用
	int *p = cp;		//错误，p的类型和cp的类型不匹配
	int &r3 = r;		//错误，r3的类型和r的类型不匹配
	int &r4 = 42;		//错误，不能使用字面值初始化一个非常量引用
	适用于函数参数的传递
	形参定义尽量使用常量引用，使用非常量引用会极大限制函数所能接受的实参类型，不能把const对象、
	字面值或者需要类型转换的对象传递给普通的引用形参。
    ***数组形参
	数组的两个特殊性质：1.不允许拷贝数组；2.使用数组时(通常)会将其转换成指针
	因为不能拷贝数组，所以不能以值传递的方式使用数组参数；传递的是指针所以函数一开始不知道数组的大小
WARNING：和其他使用数组的代码一样，以数组作为形参的函数也必须确保使用数组时不会越界
	管理指针形参有三种常用技术：
	1.使用标记指定数组长度
		数组本身包含一个结束标记，如C风格字符串，储存在数组中，以空字符结束
	2.使用标准库规范
		传递指向数组首元素和尾后元素的指针，标准库函数begin/end
	3.显示传递一个表示数组大小的形参
      **数组形参和const
      **数组引用形参(引用的数组与数组的引用)
		f(int &arr[10])		//错误，将arr声明成了引用的数组
		f(int (&arr)[10])	//正确，arr是具有10个整数的整型数组的引用
      **传递多维数组
	传递的同样是数组的首元素，多元数组的首元素本身就是数组
    ***main：处理命令行选项
	int main(int argc, char **argv) {...}
	其中argv是一个数组，指向char*,argv的第一个元素指向程序的名字或者一个空字符串，接下来的元素
	依次传递命令行提供的实参。最后一个指针之后的元素值保证为0
    WARNING：当使用argv中的实参时，一定要记得可选的实参从argv[1]开始；argv[0]保存程序的名字，而非
	     用户的输入
    ***含有可变形参的函数
	为了编写能处理不同数量实参的函数，C++11新标准提供了两种主要的方法：
	1.如果所有的实参类型相同，可以传递一个名为initializer_list的标准库类型
	2.类型不同，可以编写一种特殊的函数，即所谓的可变参数模板
	C++还有一种特殊的形参类型(即省略符)，可以用它传递可变数量的实参，这种功能一般只用于与C函数
	交互的接口程序。
      **initializer_list形参
	initializer_list类型定义在同名的头文件中
	initializer_list<T> lst;	默认初始化；T类型元素的空列表
	initializer_list<T> lst{a,b,c...};lst的元素数量和初始值一样多；lst的元素是对应初始值的副本；
					  列表中的元素是const
	lst2(lst)	拷贝或赋值一个initializer_list对象不会拷贝列表中的元素；拷贝后
	lst2 = lst	原始列表和副本共享元素
	lst.size()	列表中的元素数量
	lst.begin()	返回指向lst中首元素的指针
	lst.end()	返回指向lst中尾元素下一位值的指针
	和vector一样，initializer_list也是一种模板类型，定义其对象时，必须说明列表中所含元素的类型；
	和vector不一样的是，initializer_list对象中的元素永远是常量值，无法改变initializer_list对象
	中元素的值。
	如果想向initializer_list形参中传递一个值的序列，则必须把序列放在一对花括号内
	viod error_msg(ErrCode e, initializer_list<string> il)
	{

		cout << e.msg() << ": ";
		for (const auto &elem : il)
			cout << elem << " ";
		cout << endl;
	}
	为了调用这个版本的error_msg函数，需要额外传递一个ErrCode实参：
	if (expected != actual)
		error_msg(ErrCode(42),{"functionX", expected, actual});
	else
		error_msg(ErrCode(0), {"functionX", "okay"});
      **省略符形参
	WARNING 仅仅用于C和C++通用的类型，特别应该注意的是，大多数类类型的对象在传递给省略符形参
		时都无法正确拷贝
	viod foo(parm_list, ...);
	viod foo(...);
	
****返回类型和return语句                              ------------------------------------------------------------------------>>>>here
	return语句终止当前正在执行的函数并将控制权返回到调用该函数的地方，形式：
	return;
	return expression;
    ***无返回值函数
	即viod函数中，该函数最后一句后面会隐式地执行return。如果想在函数中间提前退出，可以使用return
	语句，有点类似于break语句退出循环；一个返回类型是viod的函数也能使用return语句的第二种形式，不过
	此时return语句的expression必须是另一个返回viod的函数。
    ***有返回值的函数
	在含有return语句的循环后面应该也有一条return语句，如果没有的话该程序就是错误的。很多编译器都无法发现
	此类错误
      **值是如何被返回的
	返回一个值的方式和初始化一个变量或形参的方式完全一样;返回的值用于初始化调用点的一个临时量，该临时量
	就是函数调用的结果。
	同其他引用类型一样，如果函数返回引用，则该引用仅是它所引对象的一个别名。
	//挑出两个string对象中较短的那个，返回其引用
	const string &shorterString(const string &s1, const string &s2)
	{
		return s1.size() <= s2.size() ? s1 : s2;
	}
	其中形参和返回类型都是const string的引用，不管是调用函数还是返回结果都不会真正拷贝string对象
      **不要返回局部对象的引用或指针
	函数完成后，它所占用的储存空间也随之被释放掉，因此，函数终止意味着局部变量的引用将指向不再有效的内存区域
	//严重错误：这个函数试图返回局部对象的引用
	const string &manip()
	{
		string ret;
		//以某种方式改变一下ret
		if (!ret.empty())
			return ret;	//错误，返回局部对象的引用
		else
			return "Empty";	//错误，"Empty"是一个局部临时量
	}
	Tip：要想确保返回值安全，需要知道：引用所引的是在函数之前已经存在的哪个对象？
      **返回类类型的函数和调用运算符
      **引用返回左值
	函数的返回类型决定函数调用是否是左值。调用一个返回引用的函数得到左值，其他返回类型得到右值。可以像使用
	其他左值那样来使用返回引用的函数的调用，特别是，我们能为返回类型是非常量引用的函数的结果赋值：
	char &get_val(string &str, string::size_type ix)
	{
		return str[ix];		//get_val假定索引值是有效的
	}
	int main()
	{
		string s("a value");
		cout << s << endl;	//输出a value
		get_val(s,0) = 'A';
		cout << s << endl;	//输出A value
		return 0;
	}
      **列表初始化返回值
	C++11新标准规定，函数可以返回花括号包围的值的列表
      **主函数main的返回值
	如果函数的返回类型不是viod，那么它必须返回一个值，例外：我们允许main函数没有return语句直接结束，编译器
	将隐式地插入一条返回0的return语句。0表示执行成功，返回其他值表示执行失败，为了使返回值与机器无关，
	cstdlib头文件定义了两个预处理变量
	int main()
	{
		if (some_failure)
			return EXIT_FAILYRE;	//定义在cstdlib头文件中
		else
			return EXIT_SUCCESS;
	}
	因为它们是预处理变量，所以既不能在前面加上std::，也不能在using声明中出现。
      **递归
	函数调用了它本身，我们有时候会说这种函数含有递归循环
	Note:main函数不能调用它自己
    ***返回数组指针
	因为数组不能拷贝，所以函数不能返回数组，不过，函数可以返回数组的指针或引用；定义一个返回数组的指针或引用
	的函数比较烦琐，简化方法最直接的方法是使用类型别名
	typedef int arrT[10];	arrT是一个类型别名，它表示的类型是含有10个
	using arrT = int[10];	arrT的等价声明
	arrT* func(int i);	func返回一个指向含有10个整数的数组的指针
      **声明一个返回数组指针的函数
	如果声明func时不使用类型别名
	int arr[10];	arr是一个含有10个整数的数组
	int *p1[10];	p1是一个含有10个指针的数组
	int (*p2)[10] = &arr;	p2是一个指针，它指向含有10个整数的数组
	返回数组指针的函数形式：
		Type (*function(parameter_list)) [dimension]
	Type表示元素的类型，dimension表示数组的大小，(*function(parameter_list))两端的括号必须存在。
	int (*func(int i)) [10];
	1.func(int i)表示调用func函数时需要一个int类型的实参
	2.(*func(int i))意味着我们可以对函数调用的结果执行解引用操作
	3.(*func(int i)) [10]表示解引用func的调用将得到一个大小是10的数组
	4.int (*func(int i)) [10]表示数组中的元素是int类型
      **使用尾置返回类型
	任何函数的定义都能使用尾置返回，这种形式对于返回类型比较复杂的函数最有效。尾置返回类型跟在形参列表后面
	并以一个->符号开头。为了表示函数真正的返回类型跟在形参列表之后，我们在本应该出现返回类型的地方放置一个
	auto：
	//func接受一个int类型的实参，返回一个指针，该指针指向含有10个整数的数组
	auto func(int i) -> int (*)[10];
      **使用decltype
	如果我们知道函数返回的指针将指向哪个数组，就可以使用decltype关键字声明返回类型
	int odd[] = {1,3,5,7,9};
	int even[] = {0,2,4,6,8};
	//返回一个指针，该指针指向含有5个整数的数组
	decltype(odd) *arrPtr(int i)
	{
		return (i % 2) ? &odd : &even;	//返回一个指针数组的指针
	}
	C++11：arrPtr使用关键字decltype表示它返回类型是个指针，并且该指针所指的对象与odd的类型一致。
	因为odd是数组，所以arrPtr返回一个指向含有5个整数的数组的指针。需要注意的是：decltype并不负责把数组
	类型转换成对应的指针，所以decltype的结果是个数组，要想表示arrPtr返回指针还必须在函数声明时加一个*符号。

****函数重载
	如果同一作用域内的几个函数名字相同但形参列表不同，称之为重载函数。函数的名字仅仅是让编译器知道它调用的
	是哪个函数，而函数重载可以在一定程度上减轻程序员起名字、记名字的负担。main函数不能重载
    ***定义重载函数
	有一种典型的数据库应用，需要创建几个不同的函数分别根据名字、电话、账户号码等信息查找记录：
	Record lookup(const Account&);	//根据Account查找记录
	Record lookup(const Phone&);	//根据Phone查找记录
	Record lookup(const Name&);	//根据Name查找记录
	Account acct;
	Phone phone;
	Record r1 = lookup(acct);	//调用接受Account的版本
	Record r2 = lookup(phone);	//调用接受Phone的版本
	在形参数量或形参类型上有所不同，不允许两个函数除了返回类型外其他所有的要素都相同
	Record lookup(const Account&);
	bool lookup(const Account&);	//错误，与上一个函数相比只有返回类型不同
    ***判断两个形参的类型是否相异
	//每对声明的是同一个函数
	Record lookup(const Account &acct);
	Record lookup(const Account&);	//省略了形参的名字，与上一个相同

	typedef Phone Telno;
	Record lookup(const Phone&);
	Record lookup(const Telno&);	//Telno与Phone的类型相同
    ***重载和const形参
	顶层const不影响传入函数的对象，一个拥有顶层const的形参无法和另一个没有顶层const的形参区分开来：
	Record lookup(Phone);
	Record lookup(const Phone);	//重复声明

	Record lookup(Phone*);
	Record lookup(Phone* const);	//重复声明
	如果形参是某种类型的指针或者引用，则通过区分其指向的是常量对象还是非常量对象可以实现函数重载，
	此时的const是底层的：
	Record lookup(Account&);
	Record lookup(const Account&);	//新函数
	
	Record lookup(Account*);
	Record lookup(const Account*);	//新函数
	const不能转换成其他类型，所以只能把const对象(或指向const的指针)传递给const形参；非常量可以转换成const
	所以上面四个函数都能作用于非常量或者指向非常量对象的指针。当传递一个非常量对象或指向非常量对象的指针时，
	编译器优先选择非常量版本。
    ***const_cast和重载
	const_cast在重载函数的情景中最有用。
****特殊用途语言特性

****函数匹配

****函数指针

	

----------------------------------------------------------------------------------------
new
new operator	operator new	placement new
new operator:
	1.获得一块内存空间   malloc
	2.调用构造函数	    ->
	3.返回正确的指针     return
	第一步分配内存实际上是通过调用operator new来完成的，可以重载operator new设置我们希望的行为，
	全局的operator new也是可以重载的，但这样一来就不能再递归的使用new来分配内存了，只能使用malloc；
	相应的，delete也有delete operator和operator delete之分，后者也可以重载，如果重载了operator new，
	就应该也相应的重载operator delete。
placement new：
	用来实现定位构造的，因此也可以实现new operator三步操作中的第二步，
	#include<new.h>
	void main()
	{
		char s[sizeof(A)];
		A* p = (A*)s;
		new(p) A(3);	//p->A::A(3)
		p->Say();
	}
	new(p) A(3)这种写法便是placement new
	最好不要使用placement new，使用new operator地编译器会自动生成对placement new的调用的代码，因此
	也会相应的生成使用delete时调用析构函数的代码，
	如果像上面在栈上使用了placement new，则必须手工调用析构函数，这也是显式调用析构函数的唯一情况：
				p->~A();
	当我们觉得默认的new operator对内存的管理不能满足我们的需要，而希望自己手工的管理内存时，
	placement new就有用了。

	堆与栈：
		栈区：储存程序运行时分配的局部变量
		堆区：储存程序员申请的内存空间
		可读写区：用于储存全局变量和静态变量
--------------------------------------------------------------------------------------------------------


<char与int比较时，char自动转换为int进行大小比较>










































	















# 基础知识

C++内置类型包括：整数，浮点数及两者的多变体（基本类型）；数组，字符串，指针，引用，结构体和共用体（复合类型）。

内存分区：堆，栈，全局（静态）存储区，文字常量区，程序代码区。

### 数组

**初始化**：数组元素若为内置类型，若未被显示赋值，全局数组会被全部初始化为0，局部数组则不被初始化，除非初始化了部分元素，其后的其他元素才会被初始化为0；如果不是内置类型，则会调用默认构造函数进行初始化。

**字符数组**：字符数组末尾有空字符'\0'，用char数组申请空间时需要考虑这个字符；另外，strlen函数也是以'\0'结束符去进行字符串长度判断。

**二维数组的动态声明**：用new先申请行，再遍历行new每行的元素个数。静态声明的地址连续，可以直接用地址访问元素，如b\[i][j]=b[i*n+j]，而动态声明地址可能不连续，只能用数组或者指针方式进行访问，如a\[i][j] or \*(\*(a+i)+j)。另外，动态声明一定要进行delete。

**指针数组**：元素是指针的一个数组。定义为：int *a[10];

**数组指针**：指一个指向数组的指针。定义为：int (*p)[10];

**线性表**：一维数组可用来实现线性表的顺序存储，线性表是一种逻辑结构，表示元素之后一对一的相邻关系，而顺序表则是线性表对应的存储结构。链表也是一种存储结构。线性表的插入删除及查找的平均时间复杂度都为O(n).

### 字符串

**子串与子序列**：子串是字符串中连续的字符组成的子序列；而子序列是与字符串中顺序一致，但是不要求连续。

**字符串处理函数**：strlen(s)，返回s的长度，不包括结束符null;strcmp(s1, s2)，比较两个字符串是否相同，相同返回0，s1>s2返回正数，s1<s2返回负数；strcat(s1, s2)，将s2接到s1后，并返回s1；strcpy(s1,s2)将s2复制给s1，并返回s1;strncat(s1, s2, n)，将s2的前n个字符连接到s1后，并返回s1；strncat(s1, s2, n)将s2前n个字符复制给s1，并返回s1。

memcpy(void *dest, const void *src, size_t n)，指从源src所指的内存地址起拷贝n个字节到目标所指内存地址，函数返回dest的指针。strcpy与该函数不同的地方在于1.复制内容不同，前者只能复制字符串；2.复制的方法不同，前者不需要指定长度，因为字符串通过‘\0’判断字符串是否结束；3.用途不同，通常复制字符串时用strcpy，复制其他类型数据用memcpy。

**串的模式匹配算法**：1.BF(Brute-Force )算法，循环遍历，时间复杂度O((n-m)*m)；2.[KMP算法](<https://blog.csdn.net/v_july_v/article/details/7041827> )，时间复杂度O(m+n)；KMP算法本质是求NextValue[i]，等同于当前i字符之前的公共最长前缀后缀的个数(已知的与主串肯定有匹配的个数)，然后利用NextValue数组进行模式串每次的移动，以此同时减少主串S的回溯，降低时间复杂度。

**应用**：字符串包含问题(串的模式匹配)；字符串转换为数字；字符串中单词逆转；删除规定字符；找第一个只出现一次的字符(用hashtable)；判断一个字符串中字符是否都不相同(用数组 or bitmap or 排序后判断)；

### 结构体、共用体与枚举

**结构体**：类似class，但是class默认访问权限是private，而struct默认是public。结构体的定义中中可以包含自身的指针，但不能包含自身的递归定义。

**共用体**：语法类似结构体，但是union只存放一个被选中的成员。用于数据项使用多种格式(但不会同时使用)时，可节省空间。两者的成员都是从低地址开始存放。(大端存储格式即字数据的高字节存储在低地址中，小端存储则相反)

**枚举**：语法类似结构体，enum提供另一种创建符号常量的方式，可以代替const。枚举表是枚举常量的集合，可自己赋初值也可由系统从0开始依次赋值默认初值。

**sizeof**:是单目运算符而非函数，且发生在编译时刻(可当作常量表达式使用)，会忽略()中的各种计算。指针类型的字节大小为4。注意sizeof计算字符串时会将结束符‘\0’计入，而strlen不计入。用sizeof计算数组时，如果是数组名，则可以直接计算数组包含的字节大小；但是如果是指针，则计算为指针大小4，而数组作为形参时，即使是数组名称也会被当做指针使用，因此在函数内部对形参传入的数组名称进行sizeof操作，得到的仍是指针大小。

**struct内存空间**：遵从1.总空间是占用空间最大成员类型的字节的整数倍(若是32位Linux+gcc环境，若最大类型超过4，则按照4计算)。2.数据对齐原则——按结构体成员先后顺序，每个成员存放时其前面已摆放的空间大小必须时该成员类型的整数倍(若成员类型大小超过4，32位Linux+gcc环境下对齐时仍然按照4对齐即可)。

- 注意：若成员变量是数组，则按照元素一一摆放，不视为一个整体。 
- 空结构体大小为1。
- union空间是每个sizeof的最大值，也需要考虑对齐；enum是一个常量集合，里面没有元素，但是类型都是int，所以sizeof为4 。

### 运算符及优先级

**赋值语句**：赋值运算符“=”左操作数必须是非const的左值(这里我们把一个名字多代表的地址称为该名的左值，把一个名字的值称为该名的右值)。

**自增自减**：前缀运算是“先变后用”，后缀是“先用后变”。前置自增返回操作数本身，课用作左值，而后置自增返回的是一个临时变量。优先级与+，-运算符相同，且都是自右向左结合。

**逻辑操作符**："&&"、"||"只有在左操作数无法确定该逻辑表达式的结果时2，才会求解其右操作数。

**异或运算**：两个相同的数异或后的结果为0，且满足交换律。这一性质可用来解：找出成对出现的数中缺失的一个数；交换两个变量的值；二进制加法(^和&)；两个数的平均值(x&y)+((x^y)>>2)。

**表达式运算顺序**：考虑 运算符的优先级 && 运算符的结合性。

### C预处理器、作用域、static、const以及内存管理

**C预处理**：宏定义与宏替换；文件包含；条件编译。

- 宏定义与宏替换：分为符号常量的宏定义与带参数的宏定义。本质——文本替换；作用——方便作一些定义和拓展；不足——编译前进行，不做语法检查，不做计算，不做表达式求解，不分配内存。另外，宏替换与函数对比：后者只有一个返回值，后者可得到多个值；后者事源程序变长；前者占运行时间(分配内存、保留现场、值传递、返回值)，后者只占编译时间。 应尽量少使用宏替换，C++中，宏替换实现的符号常量可有const、enum代替，带参数的宏替换可有模板内联函数代替。
- 可以使用条件编译来保证头文件只处理过一次。

**全局变量**：在函数外部定义的变量，作用域是整个源程序。不同文件引用一个已经定义过的全局变量时，采取：引用头文件，或者extern关键字。若将变量写错，前者会在编译期间报错，后者在连接期间报错。同名的局部变量会屏蔽全局变量。

**static**：对于全局变量和函数来说具有隐藏作用，即未加static的全局变量和函数在另一个文件中是可见的，但加了之后就是不可见的；对于变量来说，static变量初始化为0；另外能保持局部变量内容的持久，即生命周期为整个源程序，但是作用域仍然只在函数中。

- 类中static变量或函数：表示属于这一类的共同的变量和函数，存在全局(静态)存储区，是所有类对象的共享数据成员。

  静态数据成员必须在类定义体外的外部定义(除了基本整型const static)

  静态成员函数同上，由于其不与类的对象关联，因此他不具备this指针。因而它无法访问属于类对象的非静态数据成员及非静态成员函数，只能调用其余静态成员函数与静态数据成员。static成员函数不能被声明为const，因为被声明为const的意义是该函数不修改所属对象，而static函数不属于任何对象。

**const**:C和C++中的const用法稍有区别。C++中的const可用来替换C中的#define的值替换功能。const对比#define，前者有类型安全检查；前者产生更小的目标代码；前者可以执行常量折叠(即编译时可通过必要的计算将一个复杂的常量表达式缩减成简单的)。

**指针和const修饰符**：

- const double *cptr; cptr是一个指向const double的指针，指针可指向任何东西，但指向的东西不能被改变.double * cptr = &d; cptr是一个const指针，必须初始化并且不能改变，但指向的值可以改变。
- const修饰返回值形如 const A()，表示返回值是常量；const修饰函数参数，表示参数是常量，常用于传地址且在函数中不允许改变的参数。

**const在类中的应用**：

-  const成员函数，形如 void A() const，函数末尾的const改变了隐含的this指针的类型，是this指向的对象为const类型，意味着该函数不能修改该对象。
- const int a; a只能在构造函数初始化列表初始化；static int b;b在类的实现文件中定义并初始化(定义不用加static)；const static int c; 若c是整型，则可在类声明时初始化，但c都需要在类定义体外定义(加const不加static)。

**C++内存管理**：

- int *pi = new int; 与 int *pi = new int()；前者没有初始化，后者被初始化为0。
- malloc/free与new/delete都可以用于申请动态内存和释放内存，但区别：1. 前者是C/C++语言的标准库函数，后者是C++的运算符；2. malloc需要自己计算字节数，new可自动计算；3. new是类型安全的，即编译时可指出错误，而malloc不行；4. new和delete可对应调用构造析构函数，而前者不能；5. 前者需要库文件支持，后者不需要。

### 函数

**参数传递**：C有值传递，指针传递；C++有值传递，指针传递，引用传递。值传递和指针传递都会给形参分配存储单元，引用传递形参相当于别名，不会创建实参的副本；指针传递和引用传递都能起到改变实参的作用。

**内联函数**：inline函数，作用是进行内敛扩展（即在编译时，调用函数的地方，不进行函数调用，而是使用函数体替换掉用处的函数名），从而消除函数调用时的时间开销。类中定义的成员函数默认为inline函数，或者普通函数声明或定义之前加inline成为内联函数。可用来替代宏，区别是宏定义是在预处理阶段进行代码替换，inline函数是在编译阶段；前者是没有类型检查，后者有类型检查。

**可变参数**：用“...”作为占位符。

**函数重载**：在同一作用域内，一组具有相同函数名，不同参数列表的函数。

**泛型编程**：以独立于特定类型的方式编写代码。模板是泛型编程的基础。

**模板**：函数模板和类模板都用以下形式定义模板：template <typename/class T>。

**函数的递归**：必须满足：1）递归表达式(递归体)；2）边界条件(递归出口)。其精髓是在于能否将原始问题转换为属性相同但规模较小的问题。缺点是可能包含重复的计算导致效率低下，及递归次数过多导致栈溢出。

### 指针与引用

**指针状态**：保存一个特定对象的地址，指向某个对象后面的另一个对象；0值。0值表明不指向任何一个对象。

**函数指针**：指向某个特定的函数类型（由其返回类型及形参表决定，而与函数名无关）的指针，即该指针可指向任何一个函数类型满足的函数。定义：bool  (\*pf) (const string &, const string &)，可用typedef进行简化: typedef bool (\*cmpFcn) (const string &, const string &)，使用的时候直接使用cmpFcn即可。

**引用和指针**：前者必须被初始化，初始化后不能改变为对另一个对象的引用，不可能有NULL的引用；sizeof(引用)得到的是所指对象的大小，而sizeof(指针)得到的是指针本身大小；引用是一个对象的别名，不需要解引用，而指针需要解引用；如果返回动态分配的对象或内存，必须使用指针，引用可能引起内存泄漏；使用&分别取地址时，前者是所引用变量的地址，后者是指针变量的地址。

**const引用**：一般可被用于函数的形参列表，既避免传递副本的开销，又避免实参在调用函数中被意外改变。

**引用做类的数据成员**：和const作为数据成员一样，必须在构造函数的初始化列表中中被初始化。

### 类

**构造函数**

- 如果没有定义构造函数，则系统会隐式构造一个无参的构造函数；但是若存在一个构造函数，则系统将不再构造默认构造函数，因此需要用户自己定义一个默认构造函数。

- 构造函数体内初始化和直接利用初始化列表初始化，前者效率更低。因为系统会先调用类的默认构造函数进行初始化，再在函数体中赋值。
- 必须使用构造函数初始化列表初始化的成员数据：没有默认构造函数的类类型的成员，const类型以及引用类型的成员变量。

**复制构造函数**（拷贝构造函数）：用于：1）初始化同类型得对象；2）形参或返回值为类类型时；3）初始化顺序容器中的元素；4）元素初始化式列表初始化数组元素。

**深复制与浅复制**：前者仅仅复制所考虑的对象，而不复制所引用的对象，比如类中有一个指针，复制的对象的指针仍然指向被复制的对象所指内存；而深复制则会重新依据指针所指的内存重新开辟一块内存，再将旧值拷贝过来。

**析构函数**：用于释放对象构造时或在对象的生命周期中获取的资源，不管是否定义了析构函数，编译器都会自动执行类中非static数据成员的析构函数。构造函数不能为虚函数，但析构函数可以为虚函数(类中定义了虚函数时析构函数也应为虚函数)。若类定义了自己的析构函数，在析构函数结束之后会自动调用合成析构函数，用来按对象创建时的逆序调用每个非static成员的析构函数撤销成员对象。

**不能重载的操作符**："::", ".*", ".", "?:"，带点的。

**操作符重载**：

- 赋值操作符重载需注意：1）返回值类型声明为该类型的引用，并且函数体结束前返回实例自身(\*this)的引用；2）传入参数类型声明为常量引用；3）分配新内存前释放实例自身已有的内存；4）首先判断传入参数与当前的实例(\*this)是否是同一个实例，是的话就直接返回实例。

- 复制构造函数与赋值运算符区别：看是否有新对象产生，前者只有对象实例化时才会被调用，后者是一个现存对象被赋予新的值时被调用。

- operator new 与 operator delete重载：这两个函数与C语言中的malloc和free对应，只负责分配及释放空间，可以被重载。

  new：先利用operator new分配足够空间；再运行构造函数初始化；最后返回新分配并构造的对象的指针。

  delete：首先，对sp指向的对象运行适当的析构函数；然后，调用operator delete释放所有对象所用内存。

**重载、覆盖和隐藏**：类中同名成员函数来说，这三者的区别是：重载是和普通函数重载一样，在同一类定义中不同参数列表（返回值不要求）的同名函数（水平关系）；覆盖是子类重新定义基类的虚函数（垂直关系）；隐藏是子类函数屏蔽父类的同名函数，包括：1）两个函数参数相同，但基类函数不是虚函数；2）两个函数参数不同，无论基类函数是不是虚函数。

### 面向对象编程

**继承**：

- 继承机制，利用已有的数据类型定义新的数据类型，并保留基类的成员。
- 继承访问权限。外部包括派生类的对象。![继承方式访问权限](L:\0 文档补充\我的坚果云\找工作准备\秋招\blog\C++\pictures\继承方式访问权限.png)
- 公有继承方式下，派生类的对象/对象指针/对象引用可以隐式转换到基类的对象/对象指针/对象引用上(因为派生类包含了基类的所有信息)，反过来则不行，但可以强制转换。一个指向基类的指针可以用来指向公有派生类的所有对象，这是实现多态的关键。
- 多基继承的基类同名函数的二义性(即派生类不知道该调用哪一个基类的函数)，应采取成员名限定(如A::func())或者派生类中实现同名函数的隐藏。
- 菱形继承导致新类中存在重复的子对象，也会带来类似多基继承的二义性问题。使用虚函数解决。

**多态**：

- 多态是面向对象的精髓，可简单概括为“一个接口，多种方法”，分为静态多态（函数重载和运算符重载）和动态多态（虚函数）。

- C++中的函数调用默认不使用动态绑定，若要触发动态绑定，需满足：1）调用的是虚函数；2）必须通过基类类型的引用或指针进行函数调用。
- 构造函数不能为虚函数，原因：由于继承的性质，创建子类对象时，构造函数执行顺序是，先构造父类，再构造子类，若构造函数是虚函数，那么只构造子类，父类不被构造，产生矛盾。
- 构造函数和析构函数内部避免调用虚函数。因为编译器会将其解释为静态联编，可能与使用者意图不符。

**虚函数表指针**(vptr)：每一个类产生一堆指向virtual functions的指针，放在称为virtual table(vtbl)的表中；每一个对象被添加了一个指针，指向相关的virtual table，这个指针为vptr(虚函数表指针)

- ![C++对象模型](L:\0 文档补充\我的坚果云\找工作准备\秋招\blog\C++\pictures\C++对象模型.png)
- 多继承时，有多少个基类就有多少个vptr。

**虚基类表指针**(bptr):虚拟继承基类的子类中，子类会增加某种指针，指向虚基类子对象，或者指向相关的表格(其中存放虚基类子对象的地址或偏移量)，该指针被称为bptr。如下菱形继承：B、C虚拟继承A，D普通继承B、C。同时存在vptr和bptr时，某些编译器会将合并为一个指针。

- ![菱形继承对象布局](L:\0 文档补充\我的坚果云\找工作准备\秋招\blog\C++\pictures\菱形继承对象布局.png)
- 虚拟继承的情况下，基类不管被派生多少次，永远只存在一个实体。
- 上述菱形继承的构造函数形式应如下：B(总参数表): A(参数表)；C(总参数表): A(参数表)；D(总参数表): B(参数表)，C(参数表)，A(参数表)。这样保证虚基类的构造函数必须且只能被调用一次。

**纯虚函数**：将实现留给派生类去做。凡是含有纯虚函数的类为抽象类，抽象类不能实例化对象。

**RTTI**：运行时类型识别，程序能够使用基类的指针或引用来检索该指针或引用所指对象的实际类型。C++通过下面两个操作符提供RTTI：1）typeid；2）dynamic_cast。

**显示转换**：

- 命名的强制类型转换一般形式：cast-name\<type>(expression)。
- reinterpret_cast: 与用圆括号强制转换效果相同。
- const_cast: 将const性质删除掉。
- static_cast: 能隐式转换的类型转化都可以用static_cast显示完成。
- dynamic_cast: 将expression转换成type类型的对象，type必须是类的指针、引用或者void*。与其他强制转换的区别是，该转换符涉及运行时类型检查，需要运行时类型信息，该信息存在宇虚函数表中。因此，有虚函数表的类才能使用该强制转换。

### 分治法、动态规划与贪心算法

**分治法问题**：1）该问题规模缩小到一定程度就可以容易的解决；2）可分解为若干规模较小的相同问题，即具有最优子结构性质；3）利用分解出的子问题的解可以合并为该问题的解；4）分解出的各个子问题都是相互独立的，即子问题间不包含公共子问题。若满足1），2）则可以考虑贪心法或动态规划；还满足3）4），则可用分治法；若4）不满足，虽也可分治法，但用则用动态规划更佳。

**动态规划**：思想类似分治法，也是通过组合子问题的解来解决整个问题。不同的是分治法将问题划分为相互独立的子问题，递归求解子问题后合并；动态规划适用于经分解得到的子问题往往不是相互独立的。

- 基本思路：用一个表记录所有子问题的答案，不管以后是否被用到，只要被计算过，就将其填入表中。(递归是自上而下，dp是自下而上，用以避免大量的重复计算)。
- 包含要素：1）最优子结构：问题的最优解所包含的子问题的解也是最优的；2）重复子问题。
- 经典题：LCS问题(最长公共子序列问题)。

**贪心算法**：关键两点：1）贪心选择性质；2）最优子结构

- 一个全局最优解可以通过局部最优(贪心)选择得到，这也是与动态规划的区别。必须保证每一步所作的贪心选择最终能产生一个全局最优解。
- 经典题：最小生成树，迪杰斯特拉的单源最短路径算法，哈夫曼树。

**背包问题**：0-1背包问题用动态规划，部分背包问题(即每个物体可以装一部分)则采用贪心算法。如果要求恰好装满背包，则初始化f\[0][0]初始化0，f\[0][1~V]均初始化负无穷，这样就能保证一定是装满背包的解最大。

### 链表

**类别**：单链表，双链表和循环链表。

**单链表**：每个节点包含信息域和指针域，可以解决顺序表需要大量的连续存储空间的缺点，但指针域也带来了浪费存储空间的缺点。插入，删除和读取时间复杂度都为O(n)。

**与顺序表比较**：1）存取方式：顺序表可以顺序存取，随机存取，链表只能从表头开始顺序存取；2）查找、插入和删除：无序情况下，两者的时间复杂度都是O(n)，有序情况下，顺序表采用折半查找为O(logn)，若按序号查找，则为O(1)，而链表的平均复杂度都为O(n)。但进行以上操作，链表只需修改相关结点的指针域，顺序表需要平均移动半个表长元素；3）空间分配：链表在需要的时候再申请分配，更高效灵活。

**快慢指针**：判断是否有环(快慢指针是否相遇)；寻找环的入口点(一个从头，一个从相遇点走，再次相遇的地方为入口)；利用行走差找中位数，或找倒数第K个结点。

**双链表**：在单链表的基础上增加一个前驱指针。相比于单链表，访问前驱节点节约了时间。插入和删除操作，需要保证修改的过程中不断链。

**循环链表**：表中最后一个结点的指针不是NULL，而是指向链表头。

### 栈与队列

**概念**：两种操作受限的线性表，它们拥有和线性表相同的逻辑结构。栈是“后进先出”，队列是“先进先出”。



## 参考资料

《王道程序员求职宝典》
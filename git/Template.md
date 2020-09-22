# 3.2 类模版stack的使用 #
## 类模板使用 ##
    为了使用类模版对象，必须显式指定模板参数；
例如
`stack<int> intstack;`
        int为显式指定的模板参数，通过声明类型stack<T>,在类模板内部就可以用int实例化T；
        因此intstack是一个创建自stack<int>的对象,
        如果类模板中函数含有静态成员，那么用来实例化的每种类型，都会实例化这些静态成员；

     注意：
         1、只有那些被调用的成员函数，才会产生这些函数的实例化代码;
         2、对于类模板，成员函数只有在被使用的情况下才会被实例化；
         
          优点：
          第一个好处:可以节省空间和时间；
          另外一个好处是：对于那些“未能提供所有成员函数中所有操作的”类型，你也可以使用该类型来实例化模板，只要对那些未提供某些操作的成员函数不在模板内部不使用就可以；

          例如：
              某些类模板的成员函数会使用operator<来进行元素排序，但对于不支持operator<的类型;
              false:如果进行调用operator<是会报错的;
              true: 如果不调用operator<那么也可以用此类型用来实例化该类模板；
## 模板实参 ##
模板实参可以是任何类型     
**(唯一的要求是：该类型必须提供被调用的所有操作),支持相关操作，否则模板实例化将失败。**     

    stack<float*> floatPtrStack; // 元素为浮点型指针栈     
    stack<Stack<int> > intstackstack; // 元素类型为int栈的栈
    需要在两个靠在一起的尖括号(<<或>>)之间留一个空格，否则编译器会认为你是在使用operator>>,而这可能将导致一个语法错误。
# 3.3 类模板的特化 #
    你可以用模板实参来特化类模板，通过特化类模板，
    一、可以优化基于某种特定类型的实现；
    二、克服某种特定类型在实例化类模板所出现的不足
    注：如果要特化一个类模板，你还有特化该类模板的所有成员函数,虽然也可以只特化某个成员函数；
       但这个做法并没有特化整个类，也就没有特化整个类模板。
## 实现 ##
为了特化一个类模板，你必须在起始处声明一个template<>，接下来声明用来特化类模板的类型，这个类型被用作模板实参，且必须在类名的后直接指定:   
template<>  
class stack<std::string>{...}   

    通俗的理解：相当于template<typename T>的模板参数T从参数列表中抽离至且显式指定类型为std::string类型，class stack<std::string>，所以声明处只剩下template<>，
    参数已经没有了。
## 类模板特化 ##
    注意：进行类模板特化时，每个成员函数都必须重新定义为普通函数，原来的模板函数中的每一个T也相应的被进行特化的类型取代:
    void stack<std::string>::push(std::string cons& elem)
    {
        elems.push_back(elem);
    }
#3.4 局部特化
##一、类模板可以被局部特化

    你可以在特定的环境下指定类模板的特定实现，并且要求某些模板参数仍然必须由用户来定义。
几种局部特化情况：
##二、类模板代码
template<typename T1, typename T2>      
class Myclass   
{  
    ...     
};
###1、两个模板参数具有相同的类型
    template<typename T>    
    class Myclass<T,T>  
    {   
        ...     
    };

###2、第2个模板参数的类型是int
    template<typename T>    
    class Myclass<T,int>  
    {   
        ...     
    };

###3、第2个模板参数的类型是bool
    template<typename T>    
    class Myclass<bool,T>  
    {   
        ...     
    };
    通过class Myclass<bool,T>特化相当告诉编译器特化的模板参数：
    1、第一个模板参数为bool；
    2、第二个模板参数为任意类型；
    只要第一个模板参数为bool,第二模板参数为任意类型，在没有意外的情况下：
    如：Myclass<bool,int>
    编译器会自动匹配到此特化模板，自动将第二个将显式指定的int类型传递至模板参数T进行实例化。

###4、两个模板参数都是指针类型
    template<typename T1，typename T2>    
    class Myclass<T1*, T2*>  
    {   
        ...     
    };

##
    Myclass<int,float> mif;     //使用Myclass<T1,T2>
    Myclass<float,float> mff;   //使用Myclass<T,T>
    Myclass<float,int> mfi;     //使用Myclass<T,int>
    Myclass<bool,char> mbc;     //使用Myclass<bool,T>
    Myclass<int*,float*> mp;    //使用Myclass<T1*, T2*>

##三、二义性
>如果**多个局部特化***同等程度***匹配到某个声明**，那么就称该声明具有二义性，编译器不知到使用那个特化;
>
    例：
    Myclass<int,int> m; //错误：同等程度地匹配到Myclass<T,T>和Myclass<T,int>
    Myclass<int*,int*> m; //错误：同等程度地匹配到Myclass<T,T>和Myclass<T1*,T2*>
**解决第2种二义性**可以提供一个指向相同类型指针的特化：  
    template< typename T >     
    class Myclass<T *, T *>  
    {   
        ...     
    };
    
#3.5 缺省模板实参#
>概念：对于类模板，可以为模板参数定义缺省值，这些值被称为缺省模板实参，还可以引用之前的的模板参数；、
## code ##
    template<typename T,typename CONT = std::vector<T> >
    class Stack{
    private:
        CONT elems;
    public:
        void push(T const&);
        void pop();
        T top()const;
        bool empty()const;
    };

### 注意 ###
    1、我们的类模板中含有两个模板参数，因此每个成员函数的定义必须具有这两个参数，缺省模板实参一般需要在模板成员函数的定义中省去，不做实现；
    如：
    tempelate<typename T, typename CONT>
    void Stack<T, CONT>::push(T const& elem)
    {
        elems.push_back(elem);
    }
    
    2、如果只传递一个类型实参给这个模板类，那么将会利用vector来管理stack的元素；
    如：
    Stack<int> intstack;


### 小结 ###
1、类模板具有如下性质，在类实现中，可以有一个类型或多个类型没有被指定；    
2、为了使用类模板，你可以传入某个具体类型作为模板实参，然后编译器将会基于此类型来实例化类模板；    
3、对于类模板而言，只有那些被调用的成员函数才会被实例化；   
4、可以使用某种特定类型特化类模板；  
5、可以使用某种特定类型局部特化类模板；    
6、可以为类模板的参数定义缺省值，这些值还可以引用之前的模板参数：template<typename T,typename CONT = std::vector<T> >;T就是缺省值std::vector<T>的前一个模板参数，并被引用；

# 非类型模板参数 #
对于函数模板和类模板，模板参数并不局限于类型，普通值也可以作为模板参数；

背景：在基于类型参数的模板中，你定义了一些具体细节未加确定的代码，直到代码被调用时这些细节才会被正在的确定，当使用基于值的的模板时，才能够对模板进行实例化，并获得最终的代码。

    template<typename T, int MAXSIZE>
    class Stack{
    private:
        T Telems[MAXSIZE];
        int numElems;
    public:
        Stack();
        void push(T const&);
        void pop();
        T top()const;
        bool empty()const;
        bool full()const;
    }；
注：MAXSIZE是新加入的第二模板参数，类型为int：它指定了数组最多可包含的栈元素的个数  
template Stack<typename T, int MAXSIZE>     
class Stack{    
private:    
    T elems[MAXSIZE];   
    ...
};


Stack<int,20>   int20Stack;     
Stack<int,40>   int40Stack;

可以看出每一个模板实例都具有自己的类型；    
因此int20Stack与int40Stack属于不同的类型；
而且类型之间也不存在隐式或显式的类型转换，所以它们不能相互替换，更不能互相复制；

## 非类型的函数模板参数 ##
可以为函数模板定义非类型参数
如：  
template<typename T, int VAL>   
T addValue(T const& x)  
{   
    return X+VAL;   
}   

## 非类型模板参数的限制 ##
非类型模板参数是有限制的，通常而言他们可以是常整数（包含枚举值）或是指向外部链接的对象的指针（外部对象是指不是本文件的对象）；

注意：浮点数和类对象以及全局指针是不允许作为非类型模板参数的：   
template<double VAL>  //error   
double process(double V)    
{   
return VAL*V;
}   


template<std::string name>  
class Myclass   
{   
    ...     
};

之所以不能使用浮点数作为模板实参是有历史原因的，该特性的实现并不存在很大的技术障碍；由于字符串文字是内部链接对象（因为两个具有相同名称但处于不同的模块的名称但处于不同的模块的字符串，是两个完全不同的对象，所以不能使用它们来作为模板实参）；

小结：
>模块可以具有值模板参数，而不仅仅是类型模板参数；
>
>对于非类型模板参数，你不能使用浮点数。class类型的对象和内部链接对象。

# 技巧性基础知识 #
关键字typename
在c++标准化过程中，引入关键字typename是为了说明：模板内部的标识符可以是一个类型：
如：  
template<typename T>    
class Myclass{  
private:    
typename T::Subtype *ptr;   
    ...     
};  
第二个typename被用来说明：SubType是定于类T内部的一种类型，因此ptr是一个指向T::SubType类型的指针。     
注意：如果不使用typename,SubType会被认为是一个静态成员，那么它应该是一个具体的变量或对象；上述会被看成类T的静态成员SubType和ptr的乘积
### 通常而言，当某个依赖于模板参数的名称是一个类型时，就应该使用typename。 ###+

## 使用this-> ##
对于具有基类的类模板，自身名称使用X并不一定等同于this->x.即使该x是从基类继承获得的，也是如此；    
如

    template<typename T>    
    class Base{     
        public:     
            void exit();    
    };
    template<typename Y>
    class Derived:public Base<Y>
    {
     public:
        void foo(){
            exit();
        }
    };
    根据上述例子，在foo()内部决定要调用哪个exit(),并不会考虑基类中定义的exit().
    因此不是获得一个错误就是调用另一个exit();
    自我理解，可能是基类是个模板类，如果外部有定义的exit()，那么调用会更安全准确。所以编译器对此出理，在不确定的情况不会调用基类的，除非显式指定。

## 记住规则 ##
**对于那些在基类中声明，并且依赖于模板参数的符号（函数或变量等），你应该在它们面前使用this->或者Base<Y>::限定符限定。**   
如果希望完全避免不确定性，你可以使用this->和Base<Y>::限定所有的成员访问。


# 成员模板 #
##  ##
类成员也可以是模板，嵌套类和成员函数都可以作为模板   
**背景;栈之间只有类型完全相同时才能相互赋值其中类型是指元素的类型，就是说对于元素类型不同的栈不能对它们进行相互复制，即使两种类型之间存在隐式转换**

    template<typename T>
    class Stack{
    private:
        std::deque<T> elems;

    public:
        void push(T const&);
        void pop();
        T top()const;
        bool empty()const;
        //使用元素类型T的栈进行赋值
        template<T2>
        Stack<T>& operator=(const Stack<T2> &);
    }；

两处改动：       
1、我们增加了一个复制运算符的声明，它可以把元素类型为T2的栈赋值给原来的栈；     
2、栈现在改用deque作为元素的内容容器，是为了满足新复制运算符实现的要求；

新运算符的实现如下：      
template< typename T >  
tempelate< typename T2 >    
Stack<T>& operator=(const T2& op2)  
{   
    if((void *)this == (void *)&op2)
        return *this;   
    Stack<T2> tmp(op2);     
    elems.clear();  //删除现存元素    
    while(!tmp.empty()){    
    elems.push_front(tmp.top());    
    tmp.top();  
    }   
    return *this;  
}


### 定义成员模板的语法：在定义有模板参数T的模板内部，还定义了一个含有模板参数的T2的内部模板 ###
template< typename T >      
template< typaneme T2>      

# 凡是在函数模板中用到的模板参数必须要体现出来，否则类似于未定义，未能识别 #
对于类模板而言，只有那些被调用的成员函数才会被实例化。

# 模板的模板参数 #
### 在Stack例子中，如果使用一个和缺省值不同的内部容器，程序员必须两次指定元素类型。也就说为了指定内部容器的类型，你需要同时传递容器的类型和它所含的元素类型 ###
Stack<int,std::vector<int> > VStack;


### 借助于模板的模板参数，你可以只指定容器的类型而不需要指定所含元素的类型 ###
如： Stack<int, std::vector> VStack;

    template < typename T, template <typename ELEM > class CONT = std::deque>
    class Stack{
    private:
        CONT<T> elems;   //会调用模板类template<typename ELEM> class CONT; 
                         //类似于模板类CONT，通过模板参数T进行实例化等价于CONT<T> elems;
                         //生成一个类型为CONT<T> 的elems实例。

    public:
        void push(T const&);
        void pop();
        T top()const;
        bool empty()const;
        //使用元素类型T的栈进行赋值
        template<T2>
        Stack<T>& operator=(const Stack<T2> &);
    }；

不同之处在于：     
1、第二个模板参数现在被声明为一个类模板template<typename ELEM> class CONT;     
2、缺省值也从std::deque<T>变成std::deque;

## 特别注意 ##
**在使用时第二个参数必须是类模板并且是第一个参数传递进来的类型进行实例化**     
如： Stack<int, std::vector> VStack;  
CONT<T> elems;  
在进行实例化的时候CONT是个类模板，所以第二个参数必须是类模板；这样得到一个结果   
CONT = std::vector;     
T = int;  
那么CONT<T> elems等价于std::vector < int > elems;    
**在上面的那个例子中：使用了第一个模板参数作为第二个模板参数的实例化类型**     
你可以使用**类模板**（如std::vector、std::deque等来类模板）内部的任何类型来实例化模板的模板参数    

##  ##
1、作为目标参数的声明，通常可以使用typename来替代关键字class,然而上面的CONT是为了定义一个类，因此只能使用关键字class;     
2、由于我们并不会用到"模板的模板参数"的模板参数（即ELEM），不用则可省略不写：      
如：    
template < typename T, template <typename> class CONT = std::deque>     
class Stack{    
...     
};
3、同样适用成员函数的实现；  
template < typename T, template <typename> class CONT = std::deque>     
void STack < T, CONT>::push(T const &elem)      
{   
...     
}   
4、函数模板并支持模板的模板参数；

### 模板的模板实参匹配 ###
如果你尝试使用新版本的Stack,你会获得一个错误信息：缺省值std::deque和模板的模板参数并不匹配；      
问题在于不匹配：模板的模板实参（std::deque）是一个具有参数A的模板，它将替换模板的模板实参（CONT），而模板的模板参数是一个具有参数B的模板；匹配过程要求参数A和参数B必须完全匹配，然而在这里没有考虑模板的模板实参的缺省模板参数，从而也就使B中缺少这些缺省参数值，当然就不能获得精确的匹配。


针对上述例子存在的问题在于标准库中的std::deque模板还具有另外一个参数：即分配器参数，它含有一个缺省值，但在匹配std::deque的参数和CONT的参数时，并没有考虑到这个缺省值。

解决办法：重写类的声明     
template< typename T,   
template< typename ELEM , typename ALLOC = std::allocator < ELEM >> 
class CONT = std::deque >

class Stack{
private:    
    CONT < T > elems;   
};



零初始化
对于基本类型int,double或指针，并不存在一个有用的缺省值来对它们进行初始化的默认构造函数，反而，任何未初始化的局部变量都有一个不确定的随机值。

假如你希望模板类型的变量都已经用缺省值初始化完毕，那么自定义类型并不能满足要求     
template < typename T>
void foo()
{
    T x; //如果T是自定义类型，那么X本来就不是一个确定值
}

由于这个原因，我们应该显式地调用自定义类型的缺省构造函数，并把缺省值设为0；

解决：我们可以确保对象已经执行适当的缺省初始化，即使对自定义类型对象也是如此：

    template < typename T>   
    void foo()  
    {   
        T x= T();   
    }   


    template< typename T>
    class MyClass
    {
    priavet:
        T x;
    public:
        MyClass():x() 
    }

### 使用字符串作为函数模板的实参 ###
字符串：由于长度的区别，字符串会根据长度属于不同的数组类型；

对于非应用类型的参数，在实参演绎的过程中，会出现数组到指针的类型转换（这种转换通常被成为decay）


# 模板实战 #
模板代码和普通代码是由区别的  
常用组织源代码的模型：包含模型


**链接器错误**：C和C++程序员会这样组织它们的非模板代码：

* 类和其他；类和其他类型都放在一个头文件中；
* 对于全局变量和（非内联）函数，只有声明放在头文件中，定义则放在dot-C文件；
这一切都可以正常运作了，所需的类型定义在整个程序中都是可见的；并且对于变量和函数而言，链接器也不会给出重复定义的错误。

对于模板而言：牢记这种约定之后，刚开始接触模板的程序员却总会对这种约定发出抱怨，因为它会链接器产生一个错误。
以下是实例

    //basics/myfirst.hpp
    #ifndef MYFIRST_HPP
    #define MYFISRT_HPP

    //模板声明
    template<typename T>
    void printf_typeof(T const&);
该模板函数的实现放在dot-C中

    #include"myfirst.hpp"
    #include<iostream>
    #include<typeinfo>

    template<typename T>
    void printf_typeof(T const&)
    {
        std::cout<<typeid(x).name()<<std::endl;
    }

main

    #include"nyfirst.hpp"
    
    int main()
    {
        double ice = 3.0;
        printf_typeof(ice);

        return 0;
    }

大多数C++编译器会顺利的接受这个程序：但是链接器可能会报错，提示找不到函数printf_typeof的定义

错误原因：函数模板printf_typeof的定义还没被实例化，为了使模板真正得到实例化；   
编译器必须知道：

* **应该实例化哪个定义；**
* **基于哪个模板实参来进行实例化；**

遗憾的是：在前面的例子里。这两部分位于分开编译的不同文件里，因此：

* **当编译器看到printf_typeof的调用，但还没有看到基于double实例化的函数定义的时候，它只是假设在别处提供了这个定义；**
* **另一方面，编译器处理文件myfirst.cpp的时候，它并没有指出：编译器必须基于特定实参对所包含的模板定义进行实例化；**


## 头文件中的模板 ##
对于上述问题：采取对待宏或内联函数的解决办法：将模板定义也包含在声明模板的头文件中-----即让模板定义和声明放在同一个头文件中，我们称模板的这种组织方式为包含模型。

# 显式实例化 #
包含模型能够确保所需要的模板都已经实例化；因为：**当需要进行实例化的时候，编译器会自动产生所对应的实例化体。**

C++标准还提供了一种实例化模板的机制：显式实例化指示符
显式化实例化指示符由关键字template和紧接其后的我们所需要的实例化的实体（可以是类、函数、成员函数等）声明组成，而且该声明式一个已经用实参完全替换 参数之后的声明；

如：  
//基于int显式实例化的Myclass<>的构造函数  
template Myclass< int >::Myclass();

//基于int显式实例化函数模板max()
template int const& max(int const&,int const&);

**对于每一个不同实体，在一个程序中最多只能有一个显式实例化体；**
换句话说：你可以同时显式实例化printf_typeof<int>,printf_typeof<double>,但在同一个程序中每个指示符都只能出现一个；
如果不遵守上述规则，通常都会导致链接错误，链接器会报告：发现实例化实体的重复定义；  

人工实例化有一个显著的缺点：我们必须仔细跟踪每一个需要实例化的实体，对于大型项目，这是一个巨大的负担。

# 分离模型 #
标准还给出了另外一种机制：导出模板（export template）,这种机制被称为c++模板的分离模型；

# 模板术语 #
* 类类型：在c++中，类和联合都被称为类类型；
* 类：如果不加限定，我们通常所说的“类”是指：关键字class和struct引入的类类型；

特别注意：类类型包含联合，但是类不包含联合。

## 关于术语 ##
* 类模板：该类是一个模板，他代表的是：整个类家族的参数化描述；
* 模板类：1、作为类模板的同义词；2、从模板产生的类；3、具有一个template-id名称的类；

### 实例化和特化 ###
模板实例化：模板实例化是一个通过具体值替换模板实参，从模板产生出普通类、函数或成员函数的过程。这个过程最后获得实体就是我们通常所说的特化。

1、在C++中：实例化过程并不是产生特化的唯一方式，程序员可以使用其他机制来显式指定某个声明，该声明对模板参数进行特定的特化。

如：

    template< typename T1, typename T2> // 基本的模板类型
    class MyClass{
        ...
    };

    // 显式特化
    template<> //通过应用一个template<>来获得特化
    clacc MyClass<std::string, float>{
        ...
    };
上述就是显式特化（区别于实例化特化或者其他方式产生的特化）

2、局部特化：对于仍然具有模板参数的特化，称之为局部特化。

    template< typename T1, typename T2> // 基本的模板类型
    class MyClass{
        ...
    };

    // 参数个数局部特化
    template<typename T>
    MyClass<T,T>{
        ...
    };

    //参数局部特化
    template<typename T>
    MyClass<bool,T>{
        ...
    };

**当谈及（显式或隐式）特化的时候，我们把普通模板称为基本模板。**


## 声明和定义 ##
声明：是一种C++构造，它引入（或重新引入）一个名称到某个C++作用域中，这种引入通常都包含对所引入名称的一个局部分类，有效的声明并不要求包含引入对象的细节。

定义：对于类模板或者函数模板的声明，如果本身具有代码实体，我们就称之为定义；


## 虚成员函数 ##
**1、成员函数不能被声明为虚函数、这是一种需要强制执行的限制。**
原因：因为虚函数调用机制的普遍实现是通过使用一个固定大小的虚表，每个虚函数都对应了虚表的一个入口。然而**成员虚函数的实例化个数（根据模板实参进行实例化，类型越多，虚函数类型也就越多）要等到整个程序编译完毕才能够确定，这就和虚表的大小是固定的发生了冲突**。

相反类模板的普通成员可以是虚函数，因为当类被实例化之后，它们的个数是固定的。

    template<typename T>
    class Dynamic{
    public:
        virtual ~Dynamic(); // ok：每一个Dynamic只对应一个析构函数
        template<typename T2>
        virtual void copy(const T2&); //错误在Dynamic确定的情况下，无法知道copy()的个数，因为T2是个模板参数的原因，个数无法确定，直至所有源代码编译完成，才能确认
    };

## 基本模板 ##
基本模板：如果模板声明的是一个普通的声明，我们就称它为基本模板。    
**注：这类模板声明是：没有在模板名称后面添加尖括号<>（和里面实参）的声明；**

    template<typename T>
    class Box;  //正确,基本模板

    template<typename T>
    class Box<T>;  //错误

    template<typename T>
    void translate(T*); // 正确，基本模板

    template<typename T>
    void translate<T>(T*); // 错误
显然，当声明局部特化时，声明的就是非基本模板，另外函数模板必须是基本模板


## 模板的链接 ##
**每个模板都必须有一个名字，而且在它所属的作用下，该名字必须是唯一的：除非函数模板可以被重载，特别是类模板不能和领娃一个实体共享一个名称；**

    int X;
    template<typename T>
    class X;  // 错误和变量X冲突


模板名字是具有链接的，但它们不具有C链接；   
模板通常具有外部链接(不能在函数内部声明模板)，唯一的例外就是前面有static修饰的名字空间作用下的函数模板：

    template<typename T>
    static void internal();


# 模板参数 #
三种模板参数
1、类型参数（使用最多）
2、非类型参数
3、模板的模板参数

注：模板声明要引入参数化子句，模板参数就是在该子句中声明的；  
1、这类声明可以把模板参数的名称省略不写（前提在后面不会引用到该名称）；

    template<typename, int>
    class X;
2、如果模板声明后面需要引用参数名称，那么这些参数名称是一定要写上的；
3、在同一对尖括号内部，位于后面的模板参数声明可以引用前面的模板参数名称，但后面的不能被前面的引用；

    template<typename T, T* boot, template<T *>class Buf>
    class Structure;

## 类型参数 ##
类型参数是通过关键字typename或class引入的：它们两者几乎等同，关键字后面必须是一个简单的标识符，后面用逗号隔开下一个参数声明，等号（=）代表接下来的是缺省模板实参，一个封闭的尖括号（>）表示参数化子句的结束；

注意：
1、在模板声明内部，类型参数的作用类似于typedef(类型定义)名称；   
2、如果T是一个模板参数，就不能使用class T等形式的修饰名称，即使T是一个要被class类型替换的参数也不可以。

    如：
    template<typename Allocator>  //传递的实参就class Allocator; list<Allocator> all;
    class List{
        class Allocator * allocator;  // 错误
        friend class Allocator;  // 错误
        ...
    };

## 非类型参数 ##
非类型参数表示的是：在编译期或链接期可以确定的常值；

注意：
非类型参数类型限定

    1、整型或枚举类型；
    2、指针类型（包含普通对象的指针类型，函数指针类型、指向成员的指针类型）;
    3、应用类型（指向对象或者指向函数的应用都是允许的）;

其他类型均不能使用

### 非模板参数的声明也可以使用关键字typename ###

    typename<typename T, // 类型参数
     typename T::Allocator* Allocator>  //非类型参数
    class list;

    这两种参数区分很容易：第一个参数typename的后面是一个简单的标识符（模板类型参数），而第二个typename的后面是一个受限的名称类型（受限于类型参数T，后面的使用了前面的类型参数，表示的是类型Allocator在属于T里面的类型）

>1、函数和数组类型也可以被指定为非模板参数，但它们要把它们先隐式转换为指针类型，这种转型称为decay;

>2、非类型模板参数的声明和变量声明很相似，但它们不能具有static和mutable等修饰符；只能具有const和volatile限定符，但如果两个限定符的是最外层的参数类型，编译器将忽略它们

>    template < int const lenth > class Buf;
>    等价于
>    template < int lenth > class Buf;
> 3、非类型模板参数只能是右值：它们不能被取值，也不能被赋值；


###  模板的模板参数 ###
模板的模板参数是代表类模板的占位符，它的声明和类模板很相似，但不能使用关键字struct和union;

1、在它们的声明中的作用域中，模板的模板参数的用法和类模板的用法很相似;    
2、模板的模板参数的参数可以具有缺省模板实参，只有再调用没有指定该参数的情况下，才会应用缺省模板实参;

    template<template<typename T,
                        typename A = MyAllocator>>class Container>
    class Adaptation{
        Container<int> storage; //隐式等同于Container<int,MyAllocator> storage;
        ...
    };

3、对于模板的模板参数而言，**它的参数名称只能被自身其他参数的声明使用**；

    template<template<typename T, T*> class Buf>  //前者被后者使用  正确
    class Lexer{
        static char storage[5];
        Buf<char,&Lexer<Buf>::storage[0]> buf;
        ...
    };

    template<template<typename T> class List>
    class Node{
        static T*storage;//错误：模板的模板参数在这里不能使用，作用域不一样
    };

4、通常而言，模板的模板参数的名称并不会被后面用到，因此该参数也经常被省略不屑，既没有命名。
如：

    template<template<typename T,
    typename = MyAllocator>>class Container>
    class Adaptation{
        Container<int> storage; //隐式等同于Container<int,MyAllocator> storage;
        ...
    };

### 缺省模板实参 ###
只有类模板声明才能具有缺省模板实参，任何类型的模板参数都可以拥有一个缺省实参，只要该缺省实参能够匹配这个参数就可以，显然缺省实参不能依赖于自身的参数，但可以依赖前面的参数；
如：

    template<typename T, typename T Allocator = allocator<T> >
    class List; // 就是说 allocator<T>不能依赖于本身参数Allocator;但是能依赖于前面的参数T

与缺省的函数调用参数的约束一样；    
1、对于任意一个模板参数，只有在之后的模板参数都提供了缺省实参的前提下，才具有缺省模板实参；  
2、后面的缺省值通常是在同一个模板声明中提供的，但也可以在前面的模板中提供；  
3、缺省实参不能重复声明；

    template<typename T1, typename T2, typename T3, typename T4 = char, typename T5 = char>
    class Quintuple;  //正确

    template<typename T1, typename T2, typename T3 = char, typename T4, typename T5 >
    class Quintuple; //正确，根据前面的模板声明，T4和T5已经具有缺省值

    template<typename T1 = char, typename T2, typename T3, typename T4, typename T5 >
    class Quintuple; // 错误T1还不能具有缺省实参，因为T2还没有缺省实参


    template<typename T = void>
    class Value; 

    template<typename T = void>
    class Value;   //错误：重复出现的缺省实参

## 模板实参 ##
模板实参是指：在实例化模板时，用来替换模板参数的值，可以用下面几种不同的机制来确定这些值。  
    1、显式模板实参：紧跟在模板名称后面，在一对尖括号内部的显式模板实参值，所组成的整个实体称为template-id;  
    2、注入式类名称：对具有模板参数P1、P2....的类模板X，在它的作用域中，模板名称等同于template-id:X<P1,P2,...>；     
    3、缺省模板实参：如果提供缺省模板实参的话，在类模板实例中就可以省略显式模板实参，然而，即使所有的模板参数都具有缺省值，一对尖括号还是不能省略（即使尖括号内部为空也要保留尖括号）；
    4、实参演绎：对于不是显式指定的函数模板实参，可以在函数调用语句中，根据函数调用实参的类型来演绎出函数模板实参，实参演绎还可以在其他几种情况下出现，另外，如果所有的模板实参都可以通过演绎获得，那么函数模板名称后面就不需要指定尖括号；


### 模板函数实参 ###
对于函数模板的模板实参     
1、可以显式指定它们；     
2、借助于模板的使用方式对它们进行实参演绎

    //max.cpp
    template<typename T>
    inline T const& max(T const& a,T const& b)
    {
        return a<b?b:a;
    }
 
    int main()
    {
        max<double>(1.0, -3.0); // 显式指定模板实参
        mxa(1.0,-3.0); // 模板实参被隐式演绎成double
        max<int>(1.0, 3.0); // 显式的<int>禁止了演绎，因此返回结果是int类型

        return 0;
    }


然而，某些模板实参永远得不到演绎的机会------>我们最好把这些实参所对应的参数放在模板参数列表的最开始处，从而可以显式指定这些参数，而其他的参数仍然可以进行实参演绎。

    如：
    template<typename DstT,typename SrcT>
    inline DstT implicit_cast(SrcT const & x) //srcT可以被演绎，但DstT不可以参与演绎
    {
        return x;
    }

    int main()
    {
        double value = implicit_cast<double>(-1);   // double--->DstT int----->SrcT
        return 0;
    }


如果调换例子中的模板参数顺序（template<typename SrcT, typename DstT>）那么**调用implicit_cast就必须显示指定两个模板实参。**
    template<typename SrcT,typename DstT>
    inline DstT implicit_cast(SrcT const & x) //srcT可以被演绎，但DstT不可以参与演绎
    {
        return x;
    }

    int main()
    {
        double value = implicit_cast<double>(-1);   // double--->SrcT int----->SrcT,DstT永远得到不到演绎，也就意味着此类调用无法实例化，编译器会报错，除非显示指定两个模板实参
        return 0;
    }


**由于函数模板可以被重载，所以对于函数模板而言，显式提供所有的实参并不足以标识每个函数**


##SFINAE原则保护的只是：允许视图创建无效的类型，但并不允许试图计算无效的表达式；

错误的C++例子：

    template<int I>
    void f(int (&)[24/(4-I)]);

    int main()
    {
        &f<4>; // 错误，替换后第一个除数等于0（不能应用SFINAE）不允许试图计算无效的表达式
        return 0;
    }


并不会在模板参数表达式的绑定中出现，下面的例子是合法的：

    template<int N> int g(){return N;}
    template<int* P> int g(){return P;}

    int main()
    {
        return g<1>(); //虽然数字1不能被绑定到int*的参数，但是应用了SFINAE原则
    }

### 类型实参 ###
模板的类型实参是一些用来指定模板类型参数的值，大多数类型都可以被用作模板的类型实参，两种情况例外：   
1、局部类和局部枚举(在函数定义内部声明的类型)不能作为模板的类型实参；    
2、未命名的class类型或者未命名的枚举类型不能作为模板的类型实参；     
3、通过typedef声明给出未命名类和枚举是可以作为模板类型实参的；     

    template<typename T>class List{
        ...
    };

    typedef struct{
        double x, y, z;
    }Point;

    typedef enum{red, green, blue}* ColorPtr;

    int main()
    {
        struct Association{
            int *p;
            int *q;
        };
    List<Association*> error1; // 错误，模板实参使用了局部类型
    List<ColorPtr> error2; // *ColorPtr，并非ColorPtr
    List<Point> ok; //正确，通过使用typedef定义的未命名类型
    
        return 0;
    }

注：
通常而言，尽管其他类型都可以用作模板实参，但前提是该类型提花模板参数之后获得的构造必须是有效的。

    template<typename T>
    void clear(T p)
    {
        *p = 0;
    }

    int main()
    {
        int a;
        clear(a); //错误：int类型并不支持单目运算符
    }



### 非类型实参 ###
非类型模板实参是那些替换非类型参数的值

    1、某一个具有正确类型的非类型模板参数；
    2、一个编译期整型常值（或枚举值），在参数类型和值的类型能够进行匹配，或者值的类型可以隐式地转换为参数类型的前提下，才是合法的；
    3、前面有单目运算符&(即取址)的外部变量或函数的名称，对于函数或数组变量&，运算符可以省略，这类模板实参可以匹配指针类型的非类性参数；
    4、对于引用类型的非类型模板实参，前面没有&运算符的外部变量和外部函数也是可取的；
    5、一个指向成员的指针常量，类似于&C::m的表达式，其中C是一个class类型，m是一个非静态成员，这类实参只能匹配类型为“成员指针”的非类型参数；

记住：     
使用情况：当实参匹配“指针类型或者引用类型的参数”

**1、用户定义的类型转换（单参数的转换构造函数和重载类型转换运算符）和由派生类到基类的类型转换。都是不会被考虑的；**    
**2、即使在其他的情况下，这些隐式类型转换是有效的，但使用当实参匹配“指针类型或者引用类型的参数”都是无效的；**  
**3、隐式类型转换的唯一应用只能是：给实参加上关键字const和volatile;**





###模板实参的一个普遍约束，在程序创建的时候，编译器或者链接器要能够确定实参的值，如果实参的值要等到程序运行时才能够确定（局部变量的地址），就不符合“模板是在程序创建的时候进行实例化的概念了

有些常值不能作为有效的非类型实参：
1、空指针常量；
2、浮点值；
3、字符串；


### 模板的模板的实参 ###
**“模板的模板实参”必须是一个类模板，它本身具有参数，该参数必须精确匹配它“所替换的模板的模板参数”本身的参数。**在匹配的过程中，“模板的模板实参”的缺省模板实参将不会被考虑；

    #include<memory>
    template<typename T1, typename T2, 
        template<typename T, typename = std::allocator<T> > class Container>
    class Relation{
    public:
        ...
    private:
        Container<T1> dom1;
        Container<T2> dom2;
    };



### 实参的等价型 ###
当每个对应实参值都相等时，那么这两组模板实参相等。       
1、对于类型实参，typedef名称并不会对等价性产生影响，就是说，最后比较的还是typedef原本的类型；    
2、对于非类型的整型实参，进行比较的是实参的值；    
3、从模板函数产生出来的函数一定不会等于普通函数，即便这两个函数具有相同的类型和名称；

    引申两点结论
    1、从成员函数模板产生的函数永远不会改写一个虚函数（因为成员函数模板不能是一个虚函数）；
    2、从构造函数模板产生的构造函数一定不会是缺省的拷贝构造函数

### 友元 ###
**友元声明的基本概念很简单：授予“某个类或者函数访问友元声明所在的类”的权力**

以下两个事实：使得简单概念变得更复杂

    （1）、友元声明可能是某个实体的唯一声明；
    （2）、友元函数的声明可以是一个定义；

**友元类的声明不能是类定义**，因此友元类通常不会出现问题；     
引入模板之后，友元类声明的唯一变化只是：**可以命名一个特定的类模板实例为友元**

    template<typename T>
    class Node;

    template<typename T>
    class tree{
        friend class Node<T>;
        ...
    };
**根据上述例子可知，类模板的实例声明为其他类或者类模板的友元，该类模板在声明的地方必须是可见的，对于一个普通类，就没有这样的要求**

    template<typename T>
    class Tree{
        friend class Factory; // 正确，即使是Factory的首次声明
        friend class Node<T>; // Node在此不可见，错误
    };

**可见：visible这里的意思是要求类模板有前置声明或者声明处能看到（之前）的定义**

**友元函数**
**通过确认紧接在友元函数名称后面的是一对尖括号；可以把函数模板的实例声明为友元。**

    1、尖括号可以包含模板实参，也可以通过调用参数来演绎出实参；
    2、如果全部实参都能通过演绎获得，那么尖括号可以为空；


例：
    template<typename T1,typename T2>
    void combine(T1,T2);

    class Mixer{
        friend void combine<>(int&,int&);
        //正确：T1 = int& ，T2 = int&
        
        frined void combine<int,int>(int,int);
        //正确 T1 = int, T2 = int

        friend void combine<char>(char,int);
        //正确 T1 = char, T2 = int

        friend void combine<char>(char&,int)
        //错误，不能匹配上面的combine模板

        friend void coombine<>(long,long){...}
        //错误，友元声明不允许出现定义
    };


实例
    
    #include <iostream>
    #include <cstddef>
    #include <typeinfo>
    
    using namespace std;
    
    class alloc
    {
    };
    
    template <class T, class Alloc = alloc, size_t BufSiz = 0>
    class deque
    {
    public:
        deque()
        {
            cout << "deque" << endl;
        }
    };
    
    template <class T, class Sequence>
    class stack;
    
    template <class T, class Sequence>
    bool operator==(const stack<T, Sequence> &x, const stack<T, Sequence> &y);
    
    template <class T, class Sequence>
    bool operator<(const stack<T, Sequence> &x, const stack<T, Sequence> &y);
    
    template <class T, class Sequence = deque<T> >
    class stack
    {
        /* friend bool operator==<T>(const stack<T> &, const stack<T> &);
        friend bool operator< <T>(const stack<T> &, const stack<T> &); */
        /* friend bool operator==<T>(const stack &, const stack &);
        friend bool operator< <T>(const stack &, const stack &); */
        /* friend bool operator==<T>(const stack&, const stack&);
        friend bool operator< <T>(const stack&, const stack&); */
        /* friend bool operator==<T>(const stack &, const stack &);
        friend bool operator< <T>(const stack &, const stack &); */
        friend bool operator==<>(const stack &, const stack &);
        friend bool operator< <>(const stack &, const stack &);
    
    public:
        stack()
        {
            cout << "stack" << endl;
        }
    
    private:
        Sequence c;
    };
    
    template <class T, class Sequence>
    bool operator==(const stack<T, Sequence> &x, const stack<T, Sequence> &y)
    {
        cout << typeid(x).name() << endl;
        cout << "operator == " << endl;
        return true;
    }
    
    template <class T, class Sequence>
    bool operator<(const stack<T, Sequence> &x, const stack<T, Sequence> &y)
    {
        cout << typeid(x).name() << endl;
        cout << "operator <" << endl;
        return true;
    }
    
    int main()
    {
        stack<int> x;
        stack<int> y;
    
        cout << (x == y) << endl;
        cout << (x < y) << endl;
        return 0;
    }


我们不能在友元声明中定义一个模板实例（我们最多只能定义一个特化），因此，命名一个实例的友元声明是不能作为定义的；

**在类模板中可以出现三种友元声明：  
（1）普通非模板类或函数的友元声明，将友元关系授予明确指定的类或函数。     
（2）类模板或函数模板的友元声明，授予对友元所有实例的访问权。     
（3）只授予对类模板或函数模板的特定实例的访问权的友元声明。**

**如果名称后面没有紧跟一对尖括号，下面两种情况是合法的：**

    1、如果名称不是受限的（没有包含一个形如双冒号的域运算符），那么该名称一定不是（也不能）引用一个模板实例；
    如果在友元声明的地方，还看不到所匹配的非函数模板（普通函数），那么这个友元声明就是函数声明的首次声明，那么该声明是可以定义；

    2、如果名称是受限的（有双冒号::），那么该名称必须引用一个在此之前的声明的函数或函数模板，在匹配的过程中，匹配的函数要优先于匹配的函数模板，这里的友元声明不能是定义；

    void multiply(void *); //普通函数
    
    template<typename T>
    void multiply(T); //函数模板

    class Comrades{
        friend void multiply(int){} //定义了一个新的函数，非受限函数名称，不能引用模板实例
        friend void ::multiply(void *); //引用上面的普通函数，不会引用函数模板
        friend void ::multiply(int); //引用一个模板实例
        friend void ::multiply<double*>(double*); // 受限名还可以具有一堆尖括号，但模板在此必须可见
        friend void ::error(){}; //错误。受限的友元不能是一个定义
    };


**如果需要在类模板里面声明友元函数，前面的规则依旧是适用的，唯一的区别是：可以使用模板参数来标识友元函数**

    template<typename T>
    class Node{
        Node<T*> allocate();
        ...
    };
    template<typename T>
    class List{
        friend Node<T*> allocate();
        ...
    };

注意：
>如果我们在类模板中定义了一个友元函数，那么将会出现一个很有趣的现象：
>**因为对于任何只在模板内部声明的实体，都要等到模板被实例化之后，才会是一个具体的实体，在此之前该实体是不存在的，类模板的友元函数也是如此；**

    template<typename T>
    class Creator{
        friend void appear(){...} //一个新函数::apear(),但要等到Creator被实例化后才存在
        ...
    };

    Creator<void> miracle; //这时才生成::apear();
    Creator<double> oops; //错误::apear()再次生成，重复定义
    
    注：两个不同的实例化过程生成了两个完全相同的定义（即apear()），违法了ODR原则

因为我们必须确定：在模板内部定义的友元函数的类型定义中，必须包含类模板的模板参数；

    template<typename T>
    class Creator{
        friend void appear(Creator<T>*){...} //一个新函数::apear(),但要等到Creator被实例化后才存在
        ...
    };

    Creator<void> one; //这时才生成::apear(Creator<void>*);
    Creator<double> two; //::apear(Creator<double>*)
    在这个例子中每个Creator的实例生成一个一个不同的appear函数，另外我们只：尽管这些函数作为模板的一部分被生成，但函数本身仍然是普通函数，而不是模板的实例

最后一点就是：由于函数的实体处于类定义的内部，所以这些函数是内联函数，因此在两个不同的翻译单元中可以生成相同的函数；

**友元模板**
通常的声明的友元只是：函数模板的实例或者类模板的实例，我们指定的友元也只是特定的实体；
     
**然而我们有时候需要让模板的所有实例都成为友元，这就需要声明友元模板**

    例如：
    class Manager{
        template<typename T>
            friend class Task;
        template<typename T>
            friend void Schedule<T>::dispatch(Task<T>*);
        template<typename T>
            friend int ticket(){
                return ++Manager::counter;
            }
        static int counter;
    };

**需要让模板的所有实例都成为友元，这就需要声明友元模板，友元使用与类不同的模板形参，T可以是任意合法标志符，友元函数可以访问A类的任何类实例的数据**
    
    #include <iostream>
    #include <cstddef>
    #include <typeinfo>
    
    using namespace std;
    
    class alloc
    {
    };
    
    template <class T, class Alloc = alloc, size_t BufSiz = 0>
    class deque
    {
    public:
        deque()
        {
            cout << "deque" << endl;
        }
    };
    
    template <class T, class Sequence>
    class stack;
    
    template <class T, class Sequence>
    bool operator==(const stack<T, Sequence> &x, const stack<T, Sequence> &y);
    
    template <class T, class Sequence>
    bool operator<(const stack<T, Sequence> &x, const stack<T, Sequence> &y);
    
    template <class T, class Sequence = deque<T> >
    class stack
    {
        /* friend bool operator==<T>(const stack<T> &, const stack<T> &);   //ok
        friend bool operator< <T>(const stack<T> &, const stack<T> &); */   //ok
        /* friend bool operator==<T>(const stack &, const stack &);          //ok
        friend bool operator< <T>(const stack &, const stack &); */          //ok
        friend bool operator==<>(const stack &, const stack &);  // const stack<int,deque<int,alloc,0>> &  特化后的类型   //ok
        friend bool operator< <>(const stack &, const stack &); // const stack<int,deque<int,alloc,0>> &                   //ok
    
    public:
        stack()
        {
            cout << "stack" << endl;
        }
    
    private:
        Sequence c;
    };
    
    template <class T, class Sequence>
    bool operator==(const stack<T, Sequence> &x, const stack<T, Sequence> &y)
    {
        cout << typeid(x).name() << endl;
        cout << "operator == " << endl;
        return true;
    }
    
    template <class T, class Sequence>
    bool operator<(const stack<T, Sequence> &x, const stack<T, Sequence> &y)
    {
        cout << typeid(x).name() << endl;
        cout << "operator <" << endl;
        return true;
    }
    
    int main()
    {
        stack<int> x;
        stack<int> y;
    
        cout << (x == y) << endl;
        cout << (x < y) << endl;
        return 0;
    }


和普通友元声明一样，只有在友元模板声明的是一个非受限的函数缪你刚才，并且后面没有紧跟尖括号的情况下，该友元模板声明才能成为定义；

友元模板声明的只是基本模板和基本模板的成员，当进行这些声明后，与该基本模板相对应的模板局部特化和显式特化都会自动地看成友元；

# 实例化 #
**模板实例化**
模板实例化是一个过程，它根据泛型的模板定义，生成具体的类型或者函数；  
**模板实例化的复杂性**：主要原因在于产生自模板的实体（指具体的类型或函数），它们的定义已经不再局限于源代码中的单一位置，**模板本身的位置、使用模板的位置、定义模板实参的位置都会对是（产生自模板的类型或函数）实体的含义产生一定的影响。**

## On-demand(按需)实例化 ##
情形：**当C++编译器遇到模板特化使用时，它会利用所给的实参提换对应的模板参数，从而产生该模板特化，**这个**过程是编译器自动进行的**，并**不需要客户端代码来引导**（或者不需要模板的定义来引导）;     

特化这个概念用于代表一个实体，该实体是模板（特殊化）的一个特殊实例，但并不是显式特化机制

    on-demand实例化这个特性也使得c++模板和其他编译型语言的相似功能大区别;   
    on-demand实例化有时也被称为隐式实例化或自动实例化;

on-demand实例化表明：在使用模板（特化）的地方，编译器通常需要访问模板和某些模板成员的整个定义，仅有声明是不够的：

    template<typename T> class C; // 声明  （1）
    C<int> * p = 0; //正确，并不需要C<int>的定义      （2）
    ******************************************


    template<typename T>
    class C{
    public:
        void f();   // 成员声明 （3）
    };              // 类模板定义结束  （4）
    void g(C<int>& c)  //只使用了类模板声明 （5）
    {
        c.f();   //使用类模板的成员定义，需要C<int>::f()的定义 （6）
    }

    在（1）处，只有模板声明是可见的，模板定义不可见，称为前置声明，与普通类一样；
    在（2）处声明了一个指针或应用，那么在声明的作用域中，你并不需要看到该类模板定义，如果期望知道模板的大小，或者访问该特化的成员，那么整个模板的定义就需要位于作用域中；
    在（6）需要模板的定义，因为如果看不见这个模板定义的话，编译器就不能确定成员f()是否存在且可访问；

    C<void>* p = new C<void>;
    针对上面的表达式：因为编译器需要知道C<void>的大小。
    你可能会认为：用任何类型的实参X替换参数T之后，都不会影响模板特化的大小；
    
    原因：
    1、因为在任何情况下，C<X>都是一个空类，然而编译器并不会检测它是否空；
    2、为了确定C<void>是否具有可访问的缺省构造函数，并确认C<void>没有声明私有的operator new或者operator delete,我们需要进行实例化。


在源代码中，有时候需要访问类模板成员，但在源代码中，这种需要并不总是显式可见：

**C++的重载解析规则会要求：如果候选函数的参数是class类型，那么该类型所对应的类必须是可见的**

    例如:
    template<typename T>
    class C{
    public:
        C(int); // 具有单参数的构造函数
                // 可以被用于隐式类型转换
    };

    void cnadidate(C<double> const &); //(1)
    void candidate(int){};             //(2)

    int main()
    {   
        candidate(42);   //前面两个函数声明都可以被调用
    }

    1、调用candidate(42)将会采用（2）处的重载声明
    2、然而，编译器仍然可以实例化（1）处的声明，来检查产生的实例能否成为该调用的一个有效候选函数（原因：因为在这个例子中，单参数的构造函数可以将42隐式转换为一个C<double>类型的右值）；
    3、即使不进行实例，编译器也可以解析这个调用，即调用（2）的声明；
    4、但是编译器不会拒绝这种实例化，它是允许执行这种时候的：它并不需要（也不会选择）(1)处的声明，因为一个精确的匹配要优于显式转型所获得的匹配；
    5、C<double>实例化可能会引发一个错误；

## 延迟实例化 ##
背景：使用非目标类时的一些约束并没有本质区别同模板类，比如非模板类的许多用法会要求class类型的定义是完整的，编译器也会根据类模板的定义，产生这个完整的定义。

问题：模板的实例化程度是怎么样的？   
对于这个问题，一个模糊的回答：
**只对确实需要的部分进行实例化，换句话说，编译器会延迟模板的实例化**

**细究延迟的具体含义：**  
当隐式实例化模板时，同时也实例化了**该模板的每个成员声明**，但并没有实例化相应的定义；

    例外：
    1、首先，如果类模板包含一个匿名的union，那么该union定义的成员也被实例化；
    2、虚函数：作为实例化类模板的结果，虚函数的可能被实例化，也有可能没有被实例化，要依赖具体的实现，实际上，许多实现都会实例化（虚函数）这个定义，因为“实现虚函数调用机制的内部结构”要求虚函数（的定义）作为链接实体存在。

**当实例化模板的时候，缺省的函数调用实参是分开考虑的**：
解释：只有这个被调用的函数（或成员函数）确实使用了缺省实参，才会实例化该实参，如果这个函数（或成员函数）不适用缺省调用实参，而是使用显式实参来进行调用，那么就不会实例化缺省实参。

    template<typename T>
    class Safe{
    };

    template<int N>
    class Danger{
    public:
        typedef char Block[N];  //如果N<= 0将会报错
    };

    template<typename T, int N>
    class Tricky{
        public:
            virtual ~Tricky(){
            }
        void no_body_here(Safe<T> = 3);
        void inclass(){
            Danger<N> no_boom_yet;
        }
        // void error(){Danger<0> boom;}
        // unsafe(T (*p) [N]);
        T operator->();
        //virtual Safe<T> suspect();
        struct Nested{
            Danger<N> pfew;
        };
        union{
         int aglin;
         Safe<T anonymous>   
        };
    };

    int main()
    {
        Tricky<int ,0> ok;
        return 0;
    }

    标准C++编译通常会编译这段模板定义，来检查语法约束和一般语义约束，然而检查涉及到模板参数的约束，编译器会假设该参数“处于最理想的情况”：
    
    例一、在模板Danger中，用于成员Block的typedef(类型定义)的参数N可能会是0或者是负数；，但编译器会假设最理想的情况：即参数不会是0或者负数而是正整数；
    例二、类似在成员no_body_here()声明中的缺省实参规范（=3）也是可疑的，因为不一定能够使用整数对模板Safe进行初始化，但编译器会假定：对于Safe<T>的泛型定义，并不会用到该缺省实参；
    例三、对于成员error(),如果没有注释掉，那么在编译模板的时候，它将会引发一个错误，因为使用Danger<0>会被要求给出类Danger<0>的完整定义，而产生这个类的定义会试图typedef一个元素个数为0的数组即Block[0],因此即使成员error()没有被使用，并因此而不会被实例化，但仍然会引发一个错误，这个错误是在泛型模板的处理过程中引发的，然而与error()相反的是成员unsafe(T (*p)[N])的声明，在N还没有被模板参数替换之前，该声明是不会产生错误的；


    main函数后会出现什么样的结果：
它会使编译器提换模板Tricky的参数：用int替换T，用0替换N；
>1、实际上，这里并不需要Tricky中所有成员的定义，但缺省的构造函数（在这里是隐式声明的）和析构函数是肯定会被调用的，因此定义必须存在。  
>2、需要提供虚拟成员（如虚函数）的定义，否则的话可能就会引发一个链接期错误，比如我们既没有注释掉虚函数成员suspect的声明，也没有提供它的定义，链接器就会给出这类错误。  
>3、对于成员inclass和Nestd的定义，它们会要求一个完整的Danger<0>类型，该完整类型包含一个无效的typedef
>
    一、但因为程序中并不会用到这两个成员的定义（SFINAE原则保护），因此不会产生它们的定义，从而也就不会引发错误；
    二、所有的成员声明都是会被生成的，，这些声明将可能会包含无效类型，而这是不被允许的；
    如unsafa(T (*p)[N])声明，我们将会创建一个元素个数为0的数组类型，同样会引发一个错误；
>4、在匿名union中，如果我们用Danger<N>替换Safe<T>
>,也会引发一个错误，因为类型Danger并不是完整的，也是无效的；



考察operator->，这个运算符必须返回一个指针类型或者另一个应用于这个operator->的class类型，这就意味着main函数中的Ticky<int,0>应该会引发一个错误，因为它声明了一个返回类型为int的operator->，因为某些常见的类模板定义实现了（返回类型为T或T*的）定义，所以语言规则就变得更加灵活，于是通过重载解析规则选择了用户定义的operator->，那么这个自定义的opertor->只能返回一个类型，而且此类型是应用其他（内置类型）operator->的类型，在模板之外的代码也是使用的，因此即使使用int作为返回类型，operator->声明也不会发生错误。


##模板中的名称
在大多数程序设计语言中，名称都是一个很基本的概念，借助名称，程序员可以引用前面已经构造完毕的是腿，当C++编译器遇到一个名称时，它会查找该名称，来确认它引用的是那个实体，就名称而言，C++是一门相当棘手的语言；

    譬如：C++语句x*y，
    如果x和y都是变量的名称，那么这个语句代表的是一个乘积；
    如果x是一个类型的名称，那么这个语句声明y是一个指向x实体的指针；

####C++(C)是一种上下文相关的语言：对于C++的一个构造不能脱离它的上下文来理解它，但这又和模板有哪些联系呢？事实上：模板也是一种构造，它必须处理多种上下文相关信息：

    1、模板出现的上下文；
    2、模板实例化的上下文；
    3、用来实例化模板的模板实参的上下文
    因此在C++中小心处理各种（上下文的）名称的做法就不足为奇。


**名称的分类**

熟悉下面两个主要的命名概念，就可以深入理解大多数模板话题：

    1、如果一个名称使用域解析运算符（::）或者成员访问运算符（即.或->）来显示表明它所属的作用域，我们就称该名称为受限名称；
    例：this->count就是一个受限名，而count则不是(即使前面没有符合，count实际上也是一个类成员)
    
    2、如果一个名称（以某种方式）依赖于模板参数，我们称它为依赖型名称；
    例：如果T是一个模板参数，std::vector<T>::iterator就是一个依赖名称；但如果T是一个已知的typedef(类型定义，如int)，那么std::vector<T>::iterator就不是一个依赖名称。


**名称查找**
1、受限名称的名称查找是在一个受限作用域内部进行的，该受限作用域由一个限定的构造锁具欸的那个，如果该作用域是一个类那么查找范围可以到达它的基类，但不会考虑它的外围作用域；

    int x;
    
    class B{
    public:
        int i;
    };

    class D:public B{
    };

    void f(D* pd)
    {
        pd->i = 3;  // 找到B::i
        D::x = 2;   //错误：并不能找到外围作用域中的::x
    }

2、非受限名称的查找则相反，它可以（由内到外）在所有外围类中逐层进行查找（但在某个类内部定义的成员函数定义中，它会先查找该类和基类的作用域，然后才查找外围类作用域），这种查找方式也被称为普通查找；对于非受限名称的查找，增加了一项新的查找机制除了前面的普通查找，非受限名称可以依赖参数的查找（argument-dependent lookup, ADL）

    extern int count;  // (1)
    int lookup_example(int count)  //(2)
    {
        if(count < 0) {
            int count = 1;  //(3)
            lookup_example(conut); //引用(3)
        }
    return count+::count; // 在第一个（非受限）count将引用（2），第二个受限的count引用（1）
    }

***ADL(Argument-Dependent Lookup)***

* .**ADL只能应用于非受限名称**
* 在函数调用中，这些名看起像是非成员函数，对于成员函数名称或者类型名称。如果普通函数能找到该名称，那么将不会应用ADL,**如果被调用函数的名称用圆括号括起来，也不会使用ADL.可以理解没有参数**

否则，如果名称后面的括号里面（一个或多个）实参表达式，那么ADL将会查找这些实参的associated class(关联类)和associated namespace(关联名字空间)     
**规则：对于给定类型，对于associated class和associated namespace所组成的集合的准确定义，我们可以通过下列规则来确定。**

    1、对于基本类型，该集合为空寂；
    2、对于指针和数组类型，该集合是所应用类型（对于指针而言，它所用的类型是指针所指的对象的类型）的associated class和associated namespace；
    3、对于枚举类型，associated namespace指的是枚举声明所在的namespace，对于类成员，associated class指的是它所在的类；
    4、对于class类型（包含联合类型），associated class集合包含：该class类型本身、它的外围类型、直接基类和间接基类。associated namespace集合是每个associated class所在的namespace。如果这个类是一个类模板实例化体，那么还包含：模板类型实参本身的类型、声明模板的模板实参所在的class和namespace.
    5、对于函数类型，该集合包含所有参数类型和返回类型associated class和associated namespace；
    6、对于类X的成员指针类型，除了包含成员相关的associated class和associated namespace，该集合还包括与X相关的associated class和associated namespace；

ADL在所有的associated class和associated namespace中依次查找，就好像依次直接使用这些名字空间进行限定一样。**唯一的例外情况就是：它会忽略using directives（using指示符）**。


**解析模板**
大多数程序设计语言的编译都包含两个最基本的流程：

    1、符号标记---和解析，扫描过程把源代码当作字符串序列读入，然后根据该序列生成一系列标记；
        例如：当看到字符串序列int* 怕= 0；时扫描器会生成这样标记来描述：关键字int/一个符号运算符、一个标识符p、一个符号运算符=、一个整数0和一个符号/运算符；(分号)
    2、解析器会递归地减少标记，或者把前面已经找到地模式结合更高层次地构造，从而在标记序列中不断对应已知模式。
        例如：标记0是一个有效表达式，*和后面p地组合也是一个有效的声明，而该声明和后面的“=”、再后面的表达式"0"也组成了一个更长有效的声明。最后，关键字int是一个已知类型名称。因此，当后面跟随声明*P = 0时，你实际上进行的是：初始化p的声明

## C++的实例化模型 ##
实例化过程：根据相应的模板实体，适当的替换模板参数，从而获得一个普通类或者函数，但在实际运用中遵循许多细节。

当对模板进行解析的时候。编译器并不能解析依赖型名称，于是编译器会在POI（实例化点）再次查找这些依赖型名称。  
另一方面非依赖型名称实在首次看到模板的时候就进行查找，，因此在第一次查找的时候就可以诊断错误信息。于是就有两阶段查找这个概念：

第1阶段发生在模板的解析阶段：

    在第1阶段，但是用普通的查找规则（在适当的情况下也会使用ADL）对模板进行解析，就会查找非依赖型名称，另外、非受限的依赖型名（如函数调用中的函数名称，之所以之所以说它是依赖型，因为该名称具有一个依赖型实参）也会在这个阶段进行查找，但它大的查找结果是不完整（就是说查找没有结束），在实例化模板的时候，还会再次进行查找。

第2阶段发生在模板的实例化阶段：

    第2阶段发生在模板被实例化的时候，也称此时发生的地点（或者源代码的某个位置）为一个实例化点POI。依赖型受限名称就是在此阶段进行查找的（查找的模板是：运用模板实参代替模板参数之后所获得的特定实例化），另外，非受限的依赖型名称也会再次执行ADL查找。

非模板中的上下文相关性
解析理论主要是面向上下文无关语言的，而我们在前面已经知道C++是上下文相关的语言，为了解决这个相关性，C++编译器会使用一张符号表和解析器结合起来，当解析某个声明的时候，该声明就会添加到表中，当扫描器找到一个标识符时，它会在符号表中进行查找，如果发现该标识符是一个类型，就会注释这个所获得标记（标识符）


##POI
**C++编译器会在模块客户端代码中的某些位置访问模块实体的声明或者定义，当某些代码构造引用了模块特化，而且为了生成这个完整的特化，需要实例化相应模板的定义式，就会在源代码中产生一个实例化点（POI），POI是位于源代码中的一个点，在该点会插入替换后的模板实例。**

    例如：
    class MyInt{
        public:
            MyInt();
    };

    MyInt operator-(MyInt const&);
    bool operator>(MyInt const &, MyInt const &);
    typedef MyInt Int;

    template<typename T>
    void f(T i)
    {
        if(i > 0)
            g(-i);
    }

    //(1)
    void g(Int)
    {
        //(2)
        f<Int>(42);
        //(3)
    }
    //(4)


    当C++编译器看到调用f<Int>(42),它知道需要用MyInt替换T来实例化模板f:即生成一个POI；
    (2)处和(3)处临近点调用点的两个地方，但它们不能作为POI,因为C++并不允许我们把::f<Int>(Int)的定义在这里插入；
    (1)和(4)的本质区别在于：在(4)处函数g(Int)是可见的，而在(1)处不是；因此在(4)处函数g(-i)可以被解析，然而如果我们假定(1)处作为POI，那么调用g(-i)将不能被解析，因为g(Int)在(1)处是不可见的，幸运的是：对于指向非类型特化的引用------C++把它的POI定义在“包含这个引用的定义或声明之后的最近名字的空间域”

**对于指向非类型特化的引用------C++把它的POI定义在“包含这个引用的定义或声明之后的最近名字的空间域”**

疑惑：我们为什么在里中使用另行MyInt，而不直接使用简单的int类型：
    这主要因为：在POI执行的第2次查找（指g(-i)）只使用了ADL。而基本类型并没有关联名字空间，因此如果使用int类型，就不会发生ADL查找，也就不能找到函数g,所以你用下面typedef int Int;
    前面的例子将不能通过的编译；

类特化(POI)    
**（6）和（7）不能作为POI，因为名字空间域S<int>的定义，不能出现在这个两个位置（模板是不能出现在函数作用域内部的）**

    template<typename T>
    class S{
    public:
        T m;
    };

    //(5)
    unsigned long h()
    {
        //(6)
        return (unsigned long)sizeof(S<int>);
        //(7)
    }
    //(8)

    1、如前所述，我们知道位置（6）和（7）不能作为POI，因为名字空间域S<int>的定义，不能出现在这个两个位置（模板是不能出现在函数作用域内部的）如果我们采用前面非类型实例的规则；
    2、那么POI应该在8处，但这样的话，表达式sizeof(S<int>)会是无效的。因为要等到在编译到（8），才能确定S<int>处的大小，而代码sizeof(S<int>)位于（8）处前面。会出现问题；
    3、对于指向产生自模块的类实例的引用，它的POI只能定义在“包含这个实例引用的定义或声明之前的最近的名字空间域，在这个例子中是指位置（5）”

**对于指向产生自模块的类实例的引用，它的POI只能定义在“包含这个实例引用的定义或声明之前的最近的名字空间域;**

在实例化模板的时候，可能还需要进行某些附带的实例化：
    例如：
    template<typename T>
    class{
        public:
            typedef int I;
    };

    //(1)
    template<typename T>
    void f()
    {
        S<char>::I var1 = 41;
        typename S<T>::var2 = 42;
    }

    int main()
    {
        f<double>();
        return 0;
    }

    //(2):2a,2b

    1、我们知道f<double>的POI的会在（2）处；
    2、但在这个例子中，函数模板f()引用了一个类特化S<char>,从前面看，该类特化的POI应该在（1）处；
    3、函数模板f()还引用了S<T>,因为S<T>是依赖型的，所以我们泵像S<char>那样来确定它的POI；
    4、然而，如果在(2)处实例化了f<double>,我们知道同时实例化S<double>的定义。对于类型实体和非类型，这种第二次（或者传递）POI（指f<double>）的位置相同，对于类型实体。二次POI的位置位于POI位于(2b)处，而在它的紧前处即(2a)处--就是二次POI(即S<double>的POI)的位置，现在我们就知道S<double>和S<char>的POI是不同的。
### Policy-Based Design


#### What it is


#### Why use it

考虑如下情形：

我们将要实现类Test中的两类方法，每类方法都对应有好多的实现方法，这时候该怎么办？

类Test如下所示

'''c++
class Test{

public:
    void method_one(){
    }

    void method_two(){
    }
};
'''

第一种解决方案：

'''c++
class P1{
   virtual void func(int)=0;
};

class P2{
   virtual void func(int,double)=0;
}

class Test{
private:
    P1* p1_;
    P2* p2_;
}
'''
将两类方法分别变成基类**P1**和**P2**，然后各个实现方法变成子类，分别实现对应的虚函数；

在**Test**内部，则使用基类指针在运行时进行动态分发，选择对应的实现

第二种解决方案：

'''c++
class Test{

public:
    void method_one(){
        //use p1;
    }

    void method_two(){
        //use p2;
    }

priveta:
   std::function<void(int)>p1;
   std::function<void(int,double)>p2;
}
'''

此种主要是利用函数对象，将*method_one*和*method_two*的多种对应实现用**function object**来抽象剥离出来；

然后在*Test*初始化实例的时候决定使用哪种方法

注：

1.类比可以使用函数指针，大体跟使用**function object**类似


第三种解决方案：

'''c++
template<typename PolicyOne,typename PolicyTwo>
class Test:public PolicyOne,PolicyTwo{
}

typedef Test<P1,P2> test;//P1和P2分别对应PolicyOne和PolicyTwo的实现方法之一
'''


这三种解决方案都可以达到目的：

第一种方案基于多态，缺点是会增加运行时开销和类的体积而且会有对象初始化的负担

第二种方案基于策略模式，通过函数指针/函数对象形式来实现，方便进行各种实现方法的切换

第三种方案基于策略类，将每种实现方式抽象为一个策略类，通过模板实例化，在编译期就可以知道具体选择了哪种实现方法

同时各个策略类可以进一步泛化，如下：
'''
template <typename T>
class P1{
};

template <typename T>
class P2{
};

template <typename T1,typename T2,template<class>class PolicyOne,template<class>class PolicyTwo>
class Test:public PolicyOne<T1>,PolicyTwo<T2>{
};
'''

也就是说，依靠模板可以进一步泛化


总结，第三种方案相对于第一种方案在于可以在编译器进行方法选择，没有运行时期的开销；相对于第二种方案，是其强大的泛化能力

#### When to use

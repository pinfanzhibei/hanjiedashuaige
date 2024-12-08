**关键字**

`<stdio.h>`输入输出。C风格。

`inline`（修饰函数）用来建议编译器将函数内联展开（即相当于将调用函数嵌入调用位置），理论上可以减少函数调用的开销。

`static`（修饰函数或变量）可以改变函数链接属性即被修饰的函数只能定义在他的源文件内被访问，对于其他源文件来说是不可见的。

```inline static```同时具备以上属性。

`explicit`用来修饰构造函数，禁止构造函数进行隐式转换`（=）`强制要求在创建对象时显示地调用构造函数`(p(value))`

`noexcept`表示函数不会产生异常，给予编译器更大的优化空间。

`cerr`标准输出流对象之一，代表标准错误输出流。内容大多数会输出到终端屏幕。

`typedef`给数据类型起别名。

`std::ref()`引用，给函数起别名

`auto`自动推导自变量类型

**头文件**

**`<time.h>`**C风格的用于处理时间和日期的头文件。它提供一些函数和数据类型用以处理时间。

数据类型`time_t`用来储存时间戳。

函数`time`用来获取当前系统时间的时间戳，函数用法```time(current_time)```

```c
time_t timer;//储存时间日期
struct tm *Now;//储存时间日期
time(&timer);//获取返回当前时间日期
Now = localtime(&timer);//获取
printf("当前的本地时间和日期：%s",asctime(Now));//转成字符串
```

以上等价`ctime`函数.`difftime`函数获取时间差。

**`<chrono>`**是C++11引入的用于处理时间和时间间隔的头文件，高精度算法，类型安全。

定义时间点`std::chrono::time_point`包含`time_since_epoch()`函数，用来获得1970年1月1日到time_point对象中记录的时间经过的时间间隔（duration）。

定义时间段`std::chrono::duration<数据类型，ratio>`其中为了方便定义了一些标准的时间段如`std::chrono::seconds`等。

```c++
std::chrono::duration<int,ratio<1000>> ks(3);//3000秒

std::chrono::duration<int> s(1);//1秒
```

定义时钟`clocks`都包含`now()`函数，用以获取当下时间。

定义了三种不同时钟用来获取当前的时间，如同手表`std::chrono::system_clock`依据系统当前时间(不稳定)。

```c++
std::chrono::system_clock::time_point t = std::chrono::system_clock::now()//获取当前时间
time_t tm = std::chrono::system_clock::to_time_t(t)//转换为time_t时间类型
cout<<std::ctime(&tm)<<std::endl;//输出时间字符串
```

`std::chrono::steady_clock`以统一的速率运行（不能被调整），用来时。

```c++
auto start = std::chrono::steady_clock::now();//获取开始时间点
......
auto end = std::chrono::steady_clock::now();//获取结束时间点
auto = dt = end - start;//计算时间差，这里的auto是duration类。
std::cout<<dt.count()<<"纳秒"<<std::endl;//count函数，用来获取时间段，单位为纳秒。
```

`std::chrono::high_resolution_clock`提供最高精度的计时周期。使用方法与上同。

`std::chrono::duration_cast`函数模板，可以对`duration`类对象内部的时钟周期和次数进行修改，即对上毫秒。

```c++
auto mdt == duration_cast<chrono::milliscends>(dt);//转化
std::cout<<mdt.count()<<std::endl;
```

`<mutex>`主要用于多线程编程场景的资源同步于互斥访问控制。防止多线程数据混乱。

```c++
int num = 0;//被保护对象
std::mutex mutex_;//创建互斥锁
void slow_increment(int i){
  for(int i = 0;i < 3;i++){
    mutex_.lock();//枷锁
    num++;
    mutex_.unlock();//解锁
  }
}
int main(){
    std::thread t1(slow_increment,0);
    std::thread t2(slow_increment,1);
    t1.join();
    t2.join();
}
//简化以上操作用std::lock_guard,优点：在定义域内自动上锁解锁。缺点：临界区代码太大，会降低程序效率。
for(int i = 0;i < 3;i++){
    std::lock_guard<mutex> lock(mutex_);
    num++;
    std::cout<<num<<std::endl;
    std::this_thread::sleep_for(std::chrono::seconds(1));
}
```

```c
//在类中，如若多个函数同时对一个变量进行操作，会造成死锁，因此要用到std::recursive_mutex递归互斥锁，允许同一线程多次获得互斥锁
struct Calculate
{
    Calculate() : m_i(6) {}

    void mul(int x)
    {
        lock_guard<recursive_mutex> locker(m_mutex);
        m_i *= x;
    }

    void div(int x)
    {
        std::lock_guard<recursive_mutex> locker(m_mutex);
        m_i /= x;
    }

    void both(int x, int y)
    {
        std::lock_guard<recursive_mutex> locker(m_mutex);
        mul(x);
        div(y);
    }

    int m_i;
    recursive_mutex m_mutex;
};

int main()
{
    Calculate cal;
    cal.both(6, 3);
    std::cout<< "cal.m_i = " << cal.m_i <<std::endl;
    return 0;
}
```

还有一个`std::time_mutex`超时独占互斥锁。

`<array>`提供了`<std::array>`容器类型用于表示固定大小数组。

`<condition_variable>`用于C++多线程编程。

`<thread>`提供了对线程进行操作的相关类和函数。`APL`(程序编程接口)它是一组定义好的规则和协议，用于不同软件组件之间的通信。

```c++
void func(a,b){};
std::thread t(func,a,b);//创建子线程,a,b,分别是函数的参数。
std::cout<<t.get_id()<<std::endl;//获取子线程ID
//上面的程序会出现bug是因为主线程结束后，子线程没有结束，因此要用到join()函数，用以阻塞当前线程，等待子线程的任务执行完。
t.join();
t1.join();//阻塞主线程。也可以使用detach()，进行线程分离。函数joinable()用来判断函数与主线程是否处于关联状态，返回值的类型为布尔值。
```

`std::this_thread`线程的命名空间,包含对当前线程的操作。

```c++
std::cout<<std::this_thread::get_id()<<std::endl;//获取当前线程ID。
std::this_thread::aleep_for(std::chrono::seconds(1))//休眠函数，让当前线程从运行态变为阻塞态，让出CPU资源。
auto now = std::chrono::system_clock::now();//获取当前时间
std::chrono::seconds sec(2);//间隔2s
std::this_thread::sleep_until(now+sec);//从当前时间休息两秒
//std::this_thread::yield()让出已抢到的当前CPU时间片。通常用于避免一个线程长时间占用CPU资源，从而导致多线程处理性能下降
```

`<future>`用于处理异步操作的结果。

```c++
std::promise<int> pr;//创建对象，用来接收数据
std::thread t([](std::promise<int>&p){
    p.set_value(100);//设置要传出线程的数据
    std::this_thread::sleep_for(std::chrono::seconds(3));
},std::ref(pr));//创建子线程，传入匿名函数，ref()是引用
std::future<int> f = pr.get_future();//取出promise中的数据
int value = f.get()//得到数据
t.join();
```

```c++
std::package_task<int(int)>task([](int x){
    return x += 100;
})//package_task作用与promise相似，但他是包装函数，储存一个函数
std::thread t(std::ref(task),100);//传入引用
std::future<int> f = task.get_future();
int value = f.get();
t.join();
```

```c++
std::future<int> f = std::async([](int x){
    return x += 100;
},100);//std::async()直接启动一个子线程并在其执行对应任务，完成后返回一个std::future对象
std::future_status status;//判断线程状态
do{
    status = f.wait_for(std::chrono::seconds(1));//wait等方法是用来阻塞当前主线程，等待子线程
    if(status == std::future_ststue::deferred){
        f.wait();//继续等待
}//函数未启动
    else if(status == future_status::ready){
        f.get();//返回结果  
}//结果就绪
    else if(status == future_status::timeout){
        
}//指定等待时长已用完，超时
}while(status != std::future_status::ready);
```



`<memory>`提供了一系列用于管理内存的工具和类型，来实现更安全，高效，灵活的内存管理。智能指针头文件。

```c++
std::shared_ptr<int> ptr(new int(520));//使用智能指针管理一块int型堆内存
ptr.use_count()//返回ptr管理的内存引用计数
————————————————————————————————————————
std::shared_ptr<int> ptr = std::make_shared<int>(520);//通过使用std::make_shared进行初始化
ptr.reset();//通过reset方法来初始化，当智能指针中有值的时候，调用reset会使引用计数减1
char* add = ptr.get();//得到指针的原始地址，可以对其修改变量和对象中的值
```

```c++
std::unique_ptr<int> ptr1(new int(10));//初始化
std::unique_ptr<int> ptr2 = std::move(ptr1);//独占智能指针，不允许共享，可以用std::move转让所有权
ptr2.reset();//解除对原始内存的管理
ptr1.reset(new int(250));//重新指定智能指针管理的原始内存
```

`weak_ptr`弱指针。用来监测`shared_ptr`。

`<winsock2.h>`和`<ws2def.h>`用于Windows平台下的网络编程。

`<arpa/inet.h>`

``<netinet/in.h>`

`<sys/socket.h>`

`<unistd.h>`包含`sleep`函数

`<functional>`可调用对象包装器

```c++
int add(int a,int b){
  return a + b;
}
class T1{
  public:
    static int sub(int a,int b){
      return a - b;
    }
};
class T2{
  public:
    int operator()(int a,int b){
      return a * b;
    }
};
int main(void){
  //std::function的用法
  std::function<int(int,int)> f1 = add;//绑定普通函数
  std::function<int(int,int)> f2 = T1::sub;//绑定静态类成员函数，非静态除外
  T2 t;
  std::function<int(int,int)> f3 = t;//绑定仿函数
  std::cout<<f1(9,3)<<" "<<f2(9,3)<<" "<<f3(9,3)<<std::endl;
  return 0;
}
```

```c++
void callFunc(int x,const std::function<void(int)>&f){
  if(x % 2 == 0){
    f(x);
  }
}
void output(int x){
  std::cout<<x<<" ";
}
void output_add(int x){
  std::cout<<x +10<<" ";
}
int main(void){
    //std::bind的用法。
  auto f1 = std::bind(output,std::placeholders::_1);//返回一个std::function，直接用auto自动识别
  for(int i = 0;i < 10;i++){
    callFunc(i,f1);
  }
  std::cout<<std::endl;
  auto f2 = std::bind(output_add,std::placeholders::_1);
  for(int i = 0;i <10;i++){
    callFunc(i,f2);
  }
  std::cout<<std::endl;
  return 0;
}
```



`<limits>`  `numeric_limits<数据类型>::max()`计算最大`::min()`最小。`std::numeric_limits<T>::epsilon()`计算机所能分辨的最小值。

`<utility>`定义了多种工具类和函数，如：`std::move`.

`<random>`  由随机数引擎（生成伪随机数），随机数分布（定义分布类型），种子（初始化随机数引擎，确保唯一性）

`<csignal>`函数`signal`用于捕获信号，可指定信号处理方式。

`raise`产生一个信号，并向当前正在执行的程序发送该信号。可以发送系统接受的，也可以发送自己设置接受。

`<exception>`标准异常。

`<ratio>std::ratio<int,int>`分数形式的模板类,分数运算。

``atomic``定义原子变量，变量不需要加互斥锁就可以达到其效果。``auto<int> value = 0``

**语法**

左移运算符`<<`来计算`2`的`i`次幂运算:```（（uint32_t）value）<<i```,`uint32_t`强转为32位无符号整数。

```(value != 0)&&(value & (value - 1)) == 0``用于判断`value`是否为2的幂次方数。

深拷贝与浅拷贝问题。若数据成员中有指针时，拷贝必须用深拷贝，用来重新申请一块内存来储存数据。浅拷贝则是共享一块内存，进行析构函数后会对内存非法操作。

移动构造函数传入的参数是右值，用`&&`标出。函数内部的变量是临时变量，当函数调用完毕即被析构。析构函数不会析构指向空的指针。

函数指针

`c++`宏的使用。

`lambda表达式`

```c++
[capture](params) opt -> ret {body;};
//其中capture是捕获列表，params是参数列表，opt是函数选项，ret是返回值类型，body是函数体。
int a = 10;
auto f = [=](){
    return a;
}//[=]只读，获取外部变量
auto f = [&](){
    return a + 10;
}//[&]可读可写，获取并修改
```

三目运算符`<表达式1>?<表达式2>:<表达式3>`，原理：表达式1是否为真，如是，执行表达式2，否则执行3。

委托构造函数，减少冗余代码。




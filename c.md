# c语言函数如果不用指针呢能不能返回结构体

可以！

可以把struct ABC理解当做一个基本类型char,int等传值调用，【不要】当做是返回局部变量，myfun()函数内生成的局部变量x结构体会在函数结束后被覆盖(函数调用，局部变量在栈内)，myfun()函数定义了【返回值类型】为结构体，所以会把x结构体放到返回值位置(因为函数定义了返回值类型，所以知道返回值大小，不会因为函数结束，栈弹出而消失)，在main内把返回值&quot;赋值&quot;给了.

你理解的结果是完全正确的，但中间过程我不以为然——并不是因为函数写了返回结构体就不会将栈中的结构体实体释放了，而是因为返回语句return x;在函数结束之前执行，return x;把栈中的x拷贝到的相同类型的结构体变量y中(如果没有接收变量就不拷贝)后，栈中的局部结构体同样被释放了，但它的“值”已经一一对应地拷贝到主调函数中的接收变量y中了。

```c
能。主调函数必须用相bai同类型的结构体变量接du收！举例代码如下：
//#include "stdafx.h"//If the vc++6.0, with this line.
#include "stdio.h"
#include "string.h"
struct ABC{
    char name[20];
    int n;
};
struct ABC myfun(void){
    struct ABC x={"Lining",99};//声明一个结构体局zhi部变量x并初始化
    return x;//返回dao局部变量结构体x
}
int main(void){
    struct ABC y=myfun();//声明一个同类型结构体变量y并将函数返回值赋给它
    printf("%s %d\n",y.name,y.n);//打出来看看
    return 0;
}
```


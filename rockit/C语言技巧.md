##  内存对齐

```java
#define RtAlign2(x)     (((x) + 1) >> 1 << 1)
#define RtIsAlign2(x)   (0 == ((x) & 1))

#define RtAlign4(x)     (((x) + 3) >> 2 << 2)
#define RtIsAlign4(x)   (0 == ((x) & 3))

#define RtAlign8(x)     (((x) + 7) >> 3 << 3)
#define RtIsAlign8(x)   (0 == ((x) & 7))

#define MKTAG(a, b, c, d) ((a) | ((b) << 8) | ((c) << 16) | ((unsigned)(d) << 24))
```

## forcc

```java
static void MakeFourCCString(uint32_t x, char *s) {
    s[0] = x & 0xff;
    s[1] = (x >> 8) & 0xff;
    s[2] = (x >> 16) & 0xff;
    s[3] = x >> 24;
    s[4] = '\0';
}
```

## 结构体成员在结构体重的偏移量

typeof( ((type *)0)->member )

NSI C标准允许值为0的常量被强制转换成任何一种类型的指针，并且转换的结果是个NULL，因此`((type *)0)`的结果就是一个类型为`type *`的NULL指针.

如果利用这个NULL指针来访问type的成员当然是非法的，但&( ((type *)0)->field )的意图仅仅是计算field字段的地址。

聪明的编译器根本就不生成访问type的代码，而仅仅是根据type的内存布局和结构体实例首址在编译期计算这个（常量）地址，这样就完全避免了通过NULL指针访问内存的问题。
又因为首址为0，所以这个地址的值就是字段相对于结构体基址的偏移。
以上方法避免了实例化一个type对象，并且求值在编译期进行，没有运行期负担。



\1. ( (TYPE *)0 ) 将零转型为TYPE类型指针; 
\2. ((TYPE *)0)->MEMBER 访问结构中的数据成员; 
\3. &( ( (TYPE *)0 )->MEMBER )取出数据成员的地址; 这个实现相当于获取到了 MEMBER 成员相对于其所在结构体的偏移，也就是其在对应结构体中的什么位置。
4.(size_t)(&(((TYPE*)0)->MEMBER))结果转换类型。巧妙之处在于将0转 换成(TYPE*)，结构以内存空间首地址0作为起始地址，则成员地址自然为偏移地址；

```c
struct apple{
    int weight;
    int color;
};

int main(int argc, char *argv[])
{
    int weight = 5;
    typeof(((struct apple *)0)->weight) *ap1 = &weight;//定义一个指针变量ap1, ap1的类型为apple成员weight的类型。
    printf("ap1's value:%d\n", *ap1);//5
}
```

```c
在#include <stddef.h>头文件定义
#define offset_of(type, memb) \
        ((unsigned long)(&((type *)0)->memb))
        
        比如想知道sruct apple中的color是在结构体中的哪个位置，可以这么做：
printf("colore offset addr:%d\n", offset_of(struct apple, color));
1
[root@xxx c_base]# ./a.out
colore offset addr:4
```

例子2

```c
include <stddef.h>
#include <stdio.h>

struct address {
   char name[50];
   char street[50];
   int phone;
};
int main()
{
   printf("address 结构中的 name 偏移 = %d 字节。\n",
   offsetof(struct address, name));
   
   printf("address 结构中的 street 偏移 = %d 字节。\n",
   offsetof(struct address, street));
   
   printf("address 结构中的 phone 偏移 = %d 字节。\n",
   offsetof(struct address, phone));

   return(0);
} 
address 结构中的 name 偏移 = 0 字节。
address 结构中的 street 偏移 = 50 字节。
address 结构中的 phone 偏移 = 100 字节。
```





## 结构体成员地址求 结构体地址

```c
#define container_of(ptr, type, member) ({      \  
    const typeof( ((type *)0)->member ) *__mptr = (ptr);    \  
    (type *)( (char *)__mptr - offsetof(type,member) );})
```


```c
/* linux-2.6.38.8/include/linux/compiler-gcc4.h */
#define __compiler_offsetof(a,b) __builtin_offsetof(a,b)
 
/* linux-2.6.38.8/include/linux/stddef.h */
#undef offsetof
#ifdef __compiler_offsetof
#define offsetof(TYPE,MEMBER) __compiler_offsetof(TYPE,MEMBER)
#else
#define offsetof(TYPE, MEMBER) ((size_t) &((TYPE *)0)->MEMBER)
#endif
 
/* linux-2.6.38.8/include/linux/kernel.h *
 * container_of - cast a member of a structure out to the containing structure
 * @ptr: the pointer to the member.
 * @type:	the type of the container struct this is embedded in.
 * @member:    the name of the member within the struct.
 *
 */
#define container_of(ptr, type, member) ({	    \
	const typeof( ((type *)0)->member ) *__mptr = (ptr);    \
	(type *)( (char *)__mptr - offsetof(type,member) );})
 
#include <stdio.h>
 
struct test_struct {
    int num;
    char ch;
    float fl;
};
 
int main(void)
{
    char real_ch = 'A';
    char *char_ptr = &real_ch;
 
    struct test_struct *test_struct = container_of(char_ptr, struct test_struct, ch);
 
    printf(" char_ptr = %p  test_struct = %p\n\n", char_ptr, test_struct);
 
    printf(" test_struct->num = %d\n test_struct->ch = %c\n test_struct->fl = %f\n", 
	    test_struct->num, test_struct->ch, test_struct->fl);
    
    return 0;
}
 char_ptr = 0xbfb72d7f  test_struct = 0xbfb72d7b
 test_struct->num = -1511000897
 test_struct->ch = A
 test_struct->fl = 0.000000
         注意，由于这里并没有一个具体的结构体变量，所以成员num和fl的值是不确定的。
```

##   vsnprintf

```
头文件:
#include <stdio.h>
函数声明:
int _vsnprintf(char* str, size_t size, const char* format, va_list ap);
```



```c
#include <stdio.h>
#include <stdarg.h>
int mon_log(char* format, ...)
{
char str_tmp[50];
int i=0;
va_list vArgList;                            //定义一个va_list型的变量,这个变量是指向参数的指针.
va_start (vArgList, format);                 //用va_start宏初始化变量,这个宏的第二个参数是第一个可变参数的前一个参数,是一个固定的参数
i=_vsnprintf(str_tmp, 50, format, vArgList); //注意,不要漏掉前面的_
va_end(vArgList);                            //用va_end宏结束可变参数的获取
return i;                                    //返回参数的字符个数中间有逗号间隔
}
//调用上面的函数
void main()　
{
    int i=mon_log("%s,%d,%d,%d","asd",2,3,4);
    printf("%d\n",i);
}
```

输出 9。

asd,2,3,4

123456789 （共9个字符，间隔符逗号计算在内） 

## (void)变量

void真正发挥的做用在于： 
（1） 对函数返回的限定； 
（2） 对函数参数的限定。 

这只是一种防止编译器编译时报警告的用法。有些变量如果未曾使用，在编译时是会报错，从而有些导致编译不过，所以才会出现这种用法。而此语句在代码中没有具体意义，只是告诉编译器该变量已经使用了。



## 1. 函数行参不够

如果函数传入的数字大于函数的形参， 这时候传入的数值就溢出了；写代码的时候最好参数据类型写最大

```c

void fun(unsigned char data)
{
   unsigned int b = data+1 ;
}
```

## 2. memset 参数入错误（死机）

   ```CQL
   int enciperLen =1;
   int b =3;
   memeset(buff, 0, enciperLen-b);
   ```

## 3. keil单片机扩大堆栈

在start*.s里面修改

```c
Stack_Size EQU 0x00004000
```

## 4.死机log

我们log是通过缓存用中断发出的，死机时候可能存在一部分缓存没有发送出来，导致部分log无法发出，我们无法定位死机的位置

```c
static void debug_send(const char *string)
{
 Serial_Send((consat unsigend char *) string, strlen(string))
 while(!Seril_SendOK());//在这个加个判断，强制每句话必须打出来，这个死机前就能完整的把log打印出来
}
```



## 5内存泄露

1、在线程里面运行一次申请一次内存空间，但是没有释放。

场景：我用cjson库封装了一条电量信息上传服务器，每次电量变化的时候上传一次信息，我忘记释放掉这个申请的malloc空间。发现出现了内存泄露，每次出现60字节或是64字节的内存泄露。

char * string =malloc(("报文长度"); 

```
{
 "battery": "100",
 "mid" : "EK22062019102803-1001-1001-1001"
}
```

电量变化100变99

```
{
 "battery": "99",
 "mid" : "EK22062019102803-1001-1001-1001"
}
```



1、为什么内存泄露不马上死机？ 

内存足够大，但是达到一定量可能会死机。

2、为什么每次泄露的字节数不一样，相差4字节。

这是由于内存对齐，实际上面报文99和数字100只差1个字节，但是由于内存4字节对齐的原因，所以相差4



2、函数申请空间后，return  -1；

举例： 函数里面生成信号量成功，由于其他原因返回了，这样就造成了不断生成信号量没有释放。

```
void fun(int a)
{
  taskSempaher（）;//	申请信号了
  if(a<0)
  return -1; //返回失败，误以为申请成功  

}
```


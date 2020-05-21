1、 ps  -a 这里PID TTY          TIME CMD。啥意思

 1> tty(终端设备的统称):
tty一词源于Teletypes，或teletypewriters，原来指的是电传打字机，是通过串行线用打印机键盘通过阅读和发送信息的东西，后来这东西被键盘和显示器取代，所以现在叫终端比较合适。
终端是一种字符型设备，他有多种类型，通常使用tty来简称各种类型的终端设备。
2> pty（虚拟终端):
但是假如我们远程telnet到主机或使用xterm时不也需要一个终端交互么？是的，这就是虚拟终端pty(pseudo-tty)
3> pts/ptmx(pts/ptmx结合使用，进而实现pty):
pts(pseudo-terminal slave)是pty的实现方法，和ptmx(pseudo-terminal master)配合使用实现pty。 

```shell
u@u:~$ ps -a
  PID TTY          TIME CMD
 1014 tty1     00:00:03 Xorg
 1037 tty1     00:00:00 gnome-session-b
 1196 tty1     00:00:21 gnome-shell
 1239 tty1     00:00:00 ibus-daemon
 1375 tty1     00:00:00 gsd-mouse
 1393 tty1     00:00:00 gsd-printer
 1437 tty1     00:00:01 nautilus-deskto
 1443 tty1     00:00:00 gsd-disk-utilit
 1557 tty1     00:00:00 ibus-engine-sim
 1632 tty1     00:00:00 ibus-engine-lib
 1639 tty1     00:00:00 update-notifier
 1641 tty1     00:00:04 gnome-software
 1797 tty1     00:00:00 deja-dup-monito
 2166 pts/0    00:08:11 a
 2167 pts/0    00:08:11 a
 2168 pts/0    00:08:11 a
 2169 pts/0    00:08:11 a
 2759 pts/1    00:00:00 life
 2760 pts/1    00:00:00 life
 2830 pts/1    00:04:50 a.out
 2831 pts/1    00:00:00 a.out
 2880 pts/2    00:00:00 ps
```

```shell
u@u:~$ ls /dev/t*
/dev/tty    /dev/tty16  /dev/tty24  /dev/tty32  /dev/tty40  /dev/tty49  /dev/tty57  /dev/tty8       /dev/ttyS14  /dev/ttyS22  /dev/ttyS30
/dev/tty0   /dev/tty17  /dev/tty25  /dev/tty33  /dev/tty41  /dev/tty5   /dev/tty58  /dev/tty9       /dev/ttyS15  /dev/ttyS23  /dev/ttyS31
/dev/tty1   /dev/tty18  /dev/tty26  /dev/tty34  /dev/tty42  /dev/tty50  /dev/tty59  /dev/ttyprintk  /dev/ttyS16  /dev/ttyS24  /dev/ttyS4
/dev/tty10  /dev/tty19  /dev/tty27  /dev/tty35  /dev/tty43  /dev/tty51  /dev/tty6   /dev/ttyS0      /dev/ttyS17  /dev/ttyS25  /dev/ttyS5
/dev/tty11  /dev/tty2   /dev/tty28  /dev/tty36  /dev/tty44  /dev/tty52  /dev/tty60  /dev/ttyS1      /dev/ttyS18  /dev/ttyS26  /dev/ttyS6
/dev/tty12  /dev/tty20  /dev/tty29  /dev/tty37  /dev/tty45  /dev/tty53  /dev/tty61  /dev/ttyS10     /dev/ttyS19  /dev/ttyS27  /dev/ttyS7
/dev/tty13  /dev/tty21  /dev/tty3   /dev/tty38  /dev/tty46  /dev/tty54  /dev/tty62  /dev/ttyS11     /dev/ttyS2   /dev/ttyS28  /dev/ttyS8
/dev/tty14  /dev/tty22  /dev/tty30  /dev/tty39  /dev/tty47  /dev/tty55  /dev/tty63  /dev/ttyS12     /dev/ttyS20  /dev/ttyS29  /dev/ttyS9
/dev/tty15  /dev/tty23  /dev/tty31  /dev/tty4   /dev/tty48  /dev/tty56  /dev/tty7   /dev/ttyS13     /dev/ttyS21  /dev/ttyS3
u@u:~$ 

u@u:~$ ls /dev/pts/*
/dev/pts/0  /dev/pts/1  /dev/pts/2  /dev/pts/ptmx
```

```
Q：/dev/console 是什么？
A：/dev/console即控制台，是和操作系统交互的设备，系统将一些信息直接输出到控制台上。现在只有在单用户模式下，才允许用户登录控制台。
Q:/dev/tty是什么？
A：tty设备包括虚拟控制台，串口连同伪终端设备。
/dev/tty代表当前tty设备，在当前的终端中输入 echo “hello” > /dev/tty ，都会直接显示在当前的终端中。
Q:/dev/ttyS*是什么？
A:/dev/ttyS*是串行终端设备。
Q:/dev/pty*是什么？
A:/dev/pty*即伪终端，所谓伪终端是逻辑上的终端设备，多用于模拟终端程式。例如，我们在X Window下打开的终端，连同我们在Windows使用telnet 或ssh等方式登录Linux主机，此时均在使用pty设备(准确的说在使用pty从设备)。
Q：/dev/tty0和/dev/tty1 …/dev/tty63是什么？他们之间有什么区分？
A：/dev/tty0代表当前虚拟控制台，而/dev/tty1等代表第一个虚拟控制台，例如当使用ALT+F2进行转换时，系统的虚拟控制台为/dev/tty2 ，当前的控制台则指向/dev/tty2
Q：怎样确定当前所在的终端（或控制台）？
A：使用tty命令能够确定当前的终端或控制台。
Q：/dev/console是到/dev/tty0的符号链接吗？
A:
现在的大多数文本中都称/dev/console是到/dev/tty0的链接（包括《Linux内核源代码情景分析》），但是这样说是不确切的。根据内
核文档，在2.1.71之前，/dev/console根据不同系统的设定能够链接到/dev/tty0或其他tty＊上，在2.1.71版本之后则完
全由内核控制。现在，只有在单用户模式下能够登录/dev/console（能够在单用户模式下输入tty命令进行确认）。
Q：/dev/tty0和/dev/fb*有什么区分？
A: 在Framebuffer设备没有启用的系统中，能够使用/dev/tty0访问显卡。
Q：关于终端和控制台的区分能够参考哪些文本
A:
能够参考内核文档中的 Documents/devices.txt 中关于”TERMINAL DEVICES”
的章节。另外，《Linux内核源代码情景分析》的8.7节 连同《Operating Systems : Design and
Implementation》中的3.9节(第3版中为3.8节)都对终端设备的概念和历史做了很好的介绍。另外在《Modern
Operating system》中也有对终端设备的介绍，由于和《Operating Systems : Design and
Implementation》的作者相同，所以文本内容也大致相同。需要注意的一点是《Operating Systems : Design
and Implementation》中将终端设备分为3类，而《Modern Operating
system》将终端硬件设备分为2类，差别在于前者将 X Terminal作为一个类别。
PS：
只有2410的2.6才叫ttySAC0，9200等的还是叫ttyS0
```


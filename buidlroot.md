#  buildroot快速开发

文件标识： 

发布版本：1.0.0

日期：2020.03

文件密级：

------



------

## **前言**

**概述**

**产品版本**

| **芯片名称** | **内核版本**     |
| ------------ | ---------------- |
| RK2206       | FreeRTOS V10.0.1 |

**读者对象**

本文档（本指南）主要适用于以下工程师：

1. 技术支持工程师
2. 软件开发工程师

**修订记录**

| **日期**   | **版本** | **作者** | **修改说明**           |
| ---------- | -------- | --------  | ---------------------- |
| 2020-02-03 | V1.0.0   | conway    | 初始版本               |

## **目录**

[TOC]



# 0 we问题



Buildroot可以独立地用于这些选项的任何组合（例如，您可以使用现有的交叉编译工具链，并仅通过Buildroot来构建根文件系统）。

特点：

```
- 可以应付一切
交叉编译工具链，根文件系统生成，内核映像编译和引导加载程序编译。

- 很容易
由于其类似内核的menuconfig，gconfig和xconfig配置界面，使用Buildroot构建基本系统非常容易，通常需要15至30分钟。

- 支持数千个包裹
X.org堆栈，Gtk3，Qt 5，GStreamer，W​​ebkit，Kodi，大量与网络相关和与系统相关的实用程序均受支持。
```



Buildroot是一个简单，
该文档可以在docs / manual中找到。您可以使用“ make manual-text”生成文本文档，并读取output / docs / manual / manual.text。
可以在http://buildroot.org/docs.html上找到在线文档。
要构建和使用buildroot的东西，请执行以下操作：
1）运行“ make menuconfig”
 2）选择目标体系结构和要编译的软件包
 3 ）运行'make'
 4）等待编译
 5）在输出/映像中找到内核，引导程序，根文件系统等。
 无需以root身份来构建或运行buildroot。玩得开心！
 Buildroot带有许多板卡的基本配置。运行“ make list-defconfigs”以查看提供的配置列表。请提供建议，错误报告，侮辱和贿赂返回到buildroot邮件列表
：buildroot@buildroot.org您也可以在Freenode IRC的#buildroot上找到我们。如果您想贡献补丁，请阅读https://buildroot.org/manual.html#submitting-patches

2、buildroot的文件结构是怎么样的？
3、如何使用buildroot，怎么编译buildroot，修改后怎么编译，新加包后怎么编译
4、通过buildroot的学习，buildroot的好处是什么

5、怎么移植buildroot，官方资料在哪里

#  1、什么是buildroot，为解决什么问题创造出来？

Buildroot是一个工具，先看Buildroot的广告: Making Embedded Linux easy，所以他是高效且易于使用的工具，
可以使用交叉编译来简化和自动化为嵌入式系统构建完整的Linux系统的过程。

为了实现这一目标，Buildroot能够为您的目标自动生成

```
生成交叉编译工具链
根文件系统
Linux内核映像和引导加载程序
```

# 2、buildroot对宿主机的系统有什么要求?

```
buildroot被设计跑在linux上，它能自动构建大多数主机软件包所需要的编译器，但是还是希望一些相关包期望已经安装在主机了。

尽管Buildroot会自行构建编译所需的大多数主机软件包，但某些标准Linux实用程序实际已安装在主机系统上。
您将在下面找到强制性软件包和可选软件包的概述（请注意，软件包名称在发行版之间可能有所不同）
```

强制包

```


2.1。强制性包
构建工具：
which
sed
make （版本3.81或更高版本）
binutils
build-essential （仅适用于基于Debian的系统）
gcc （4.8版或更高版本）
g++ （4.8版或更高版本）
bash
patch
gzip
bzip2
perl （版本5.8.7或更高版本）
tar
cpio
unzip
rsync
file（必须在/usr/bin/file）
bc
源获取工具：
wget
```

可选包

```
2.2。可选包装
推荐的依赖项：

Buildroot中的某些功能或实用程序（例如legal-info或图生成工具）具有其他依赖性。尽管对于简单构建不是必需的，但仍强烈建议使用：

python （2.7版或更高版本）
配置接口依赖性：

对于这些库，您需要同时安装运行时和开发数据，在许多发行版中，这些数据是单独打包的。开发包通常具有-dev或-devel后缀。

ncurses5使用menuconfig界面
qt5使用xconfig界面
glib2，gtk2并glade2使用gconfig界面
源获取工具：

在官方的树，大部分包源通过使用检索 wget从FTP，HTTP或HTTPS位置。一些软件包仅可通过版本控制系统获得。此外，Buildroot能够通过其他工具（例如rsync或）下载源scp （有关更多详细信息，请参见第19章，下载基础结构）。如果使用以下任何一种方法启用软件包，则需要在主机系统上安装相应的工具：

bazaar
cvs
git
mercurial
rsync
scp
subversion
与Java相关的软件包，如果需要为目标系统构建Java Classpath：

该javac编译器
该jar工具
文档生成工具：

asciidoc，版本8.6.3或更高版本
w3m
python与argparse模块（自动存在于2.7+和3.2+中）
dblatex （仅适用于pdf手册）
图生成工具：

graphviz使用图依赖和<pkg>-图依赖
python-matplotlib使用图构建
```



#  3下载地址

```
Buildroot版本每2个月，2月，5月，8月和11月发布一次。版本号的格式为YYYY.MM，例如2013.02、2014.08。

可以从http://buildroot.org/downloads/获得发行包。

为了方便起见，Buildrootsupport/misc/Vagrantfile源代码树中提供了一个Vagrantfile，可用于快速设置具有所需依赖项的虚拟机。

如果要在Linux或Mac Os X上设置隔离的buildroot环境，请将此行粘贴到终端上：

curl -O https://buildroot.org/downloads/Vagrantfile; 无所事事
如果您使用的是Windows，请将其粘贴到您的Powershell中：

（新对象System.Net.WebClient）.DownloadFile（
“ https://buildroot.org/downloads/Vagrantfile”、“Vagrantfile”）；
无所事事
如果要关注开发，可以使用每日快照或克隆Git存储库。有关更多详细信息，请参考Buildroot网站的“ 下载”页面。
```

# 4快速入门

## 4.1配置工具

无需root用户即可配置和使用Buildroot。

通过以常规用户身份运行所有命令，可以保护系统免受在编译和安装过程中表现异常的软件包的侵害。 

使用Buildroot的第一步是创建配置。Buildroot有一个不错的配置工具，类似于在[Linux内核](http://www.kernel.org/)或[BusyBox中](http://www.busybox.net/)可以找到的工具。

从buildroot目录运行

```
 $ make menuconfig
```

对于原始的基于curses的配置器，或者

```
 $make nconfig
```

用于新的基于curses的配置器，或

```
 $make xconfig
```

基于Qt的配置器，或

```
 $make gconfig
```

用于基于GTK的配置器。

完成所有配置后，配置工具将生成一个`.config`包含整个配置的 文件。该文件将由顶级Makefile读取。

## 4.2 启动构建处理器

要开始构建过程，只需运行：

```
 $make
```

 默认情况下，Buildroot不支持顶级并行构建，因此**`make -jN`不需要运行**。（`make -jN` 在多CPU上编译Linux内核时可以用 make -jn 多个任务并行编译加快速度。 ）

但是，对顶级并行构建有实验性支持，请参见 [第8.11节“顶级并行构建”](http://nightly.buildroot.org/manual.html#top-level-parallel-build)。 

该`make`命令通常将执行以下步骤：

```
- 下载源文件（根据需要）；
- 配置，**构建和安装交叉编译工具链**，或仅导入外部工具链；
- 配置，构建和安装选定的目标软件包；
- 构建内核映像（如果选择）；
- 构建引导加载程序映像（如果选择）；
- 以选定的格式创建根文件系统。
```

Buildroot输出存储在单个目录中`output/`。该目录包含几个子目录：

```
- `images/`存储所有映像（内核映像，引导加载程序和根文件系统映像）的位置。这些是您需要放在目标系统上的文件。
- `build/`构建所有组件的位置（这包括主机上Buildroot所需的工具以及为目标编译的软件包）。该目录为每个组件包含一个子目录。
- `host/`包含为主机构建的工具和目标工具链的sysroot。前者是为主机正确编译Buildroot所需的工具的安装，包括交叉编译工具链。后者是类似于根文件系统层次结构的层次结构。它包含所有用户空间软件包的标头和库，这些用户空间软件包提供并安装其他软件包使用的库。但是，该目录*并非*旨在作为目标的根文件系统：它包含许多开发文件，未剥离的二进制文件和库，这些文件对于嵌入式系统而言太大了。这些开发文件用于为依赖于其他库的目标编译库和应用程序。
- `staging/`是到内部目标工具链sysroot的符号链接 `host/`，为了向后兼容而存在。
- `target/`它*几乎*包含了目标的完整根文件系统：除了设备文件`/dev/`（Buildroot无法创建它们，因为Buildroot不能以root身份运行并且不想以root身份运行）之外，所需的一切都存在。另外，它没有正确的权限（例如，busybox二进制文件的setuid）。因此，***\*不应在target上使用\****此目录 。相反，您应该使用`images/`目录中内置的图像之一。如果您需要提取根文件系统的映像以通过NFS引导，请使用其中生成的tarball映像`images/`并将其提取为root。相比`staging/`，`target/` 仅包含运行所选目标应用程序所需的文件和库：不存在开发文件（标头等），剥离了二进制文件。
```

这些命令`make menuconfig|nconfig|gconfig|xconfig`和和`make`是基本命令，它们使您能够轻松，快速地生成具有所需功能的图像，并启用所有功能和应用程序。

有关“ make”命令用法的更多详细信息，请参见 [第8.1节“ *make*提示”](http://nightly.buildroot.org/manual.html#make-tips)。

# 6 用户指南

这些`make *config`命令还提供了搜索工具。

```
- 在make menuconfig中，通过按调用搜索工具`/`；
- 在make xconfig中，通过按`Ctrl`+ 调用搜索工具`f`。
```

## 6.1 交叉编译工具链

- **编译工具链**

```
编译工具链是允许您为系统编译代码的一组工具。它由一个编译器（在我们的情况下为gcc），二进制utils（例如汇编程序和链接程序）（在我们的情况下为binutils）和一个C标准库（例如 GNU Libc， uClibc-ng）组成。
PC，则您的编译工具链将在x86处理器上运行并为x86处理器生成代码。
Linux系统中，编译工具链使用GNU libc（glibc）作为C标准库。该编译工具链称为“主机编译工具链”。在其上运行并且在其上工作的计算机称为“主机系统” [3]。
```

- **交叉编译**

由于您的嵌入式系统具有不同的处理器，因此您需要交叉编译工具链-一种在您的主机系统上运行但为目标系统（和目标处理器）生成代码的编译工具链。

```
例如，如果主机系统使用x86，而目标系统使用ARM，则主机上的常规编译工具链在x86上运行并为x86生成代码，而交叉编译工具链在x86上运行并为ARM生成代码。
```

- **编译器和buildrot关系**

发行商提供了编译工具链，并且Buildroot与它无关，Buildroot与使用它来构建交叉编译工具链和在开发主机上运行的其他工具。

Buildroot为交叉编译工具链提供了两种解决方案：

1. 所述内部工具链后端，称为Buildroot toolchain在配置接口。

2. 在配置界面中调用 的外部工具链后端External toolchain。

```
可以使用菜单中的Toolchain Type选项在这两种解决方案之间进行选择Toolchain。选择一种解决方案后，将出现许多配置选项，以下各节将详细介绍这些选项。
```

## 6.1.1 Buildroot内部工具链后端

Buildroot内部工具链后端,是Buildroot设目标嵌入式系统的**用户空间应用程序和库**之前，自己编译出来的一个交叉编译工具链，该后端支持多个C库： uClibc-ng， glibc和 musl。

选择此后端后，将显示许多选项。最重要的允许配置如下：

```
1. 更改用于构建工具链的Linux内核标头的版本。这个项目值得一些解释。在构建交叉编译工具链的过程中，正在构建C库。该库提供了用户空间应用程序和Linux内核之间的接口。为了知道如何与Linux内核“对话”，C库需要访问 Linux内核标头（即.h来自内核的文件），该标头定义了用户空间和内核之间的接口（系统调用，数据结构）等）。由于此接口是向后兼容的，因此用于构建工具链的Linux内核标头的版本不需要完全匹配您打算在嵌入式系统上运行的Linux内核的版本。他们只需要具有与要运行的Linux内核相同或更低的版本即可。如果您使用的内核标头比嵌入式系统上运行的Linux内核更新，则C库可能正在使用Linux内核未提供的接口。
2. 更改GCC编译器，binutils和C库的版本。
3. 选择许多工具链选项（仅uClibc）：工具链应具有RPC支持（主要用于NFS），宽字符支持，语言环境支持（用于国际化），C ++支持还是线程支持。根据您选择的选项，在Buildroot菜单中可见的用户空间应用程序和库的数量将发生变化：许多应用程序和库需要启用某些工具链选项。当需要某个工具链选项才能启用那些软件包时，大多数软件包都会显示注释。如果需要，您可以通过运行以下命令进一步优化uClibc配置make uclibc-menuconfig。但是请注意，已针对Buildroot中捆绑的默认uClibc配置对Buildroot中的所有软件包进行了测试：如果通过从uClibc中删除功能而偏离此配置，则某些软件包可能不再构建。
值得注意的是，无论何时修改这些选项之一，都必须重建整个工具链和系统。请参见 第8.2节“了解何时需要完全重建”。
```

- 该后端的优点：

与Buildroot良好集成快速，只构建必要的内容

- 该后端的缺点：

做时需要重建工具链make clean，这需要时间。如果您想减少构建时间，请考虑使用External toolchain backend

## 6.1.2外部工具链后端

在*外部工具链后端*允许**使用现有的预建交叉编译工具链**。Buildroot知道许多著名的交叉编译工具链（来自 ARM的[Linaro](http://www.linaro.org/)，ARM的 [Sourcery CodeBench](http://www.mentor.com/embedded-software/sourcery-tools/sourcery-codebench/editions/lite-edition/)，x86-64，PowerPC和MIPS，并能够**自动下载它们**，**或者可以指向自定义工具链**。 ，可以下载或在本地安装。

然后，您有三种使用外部工具链的解决方案：

- 使用预定义的外部工具链配置文件，并**让Buildroot下载，解压和安装工具链**。Buildroot已经知道一些CodeSourcery和Linaro工具链。只需`Toolchain`从可用的工具链配置文件中选择。这绝对是最简单的解决方案。
- 使用预定义的外部工具链配置文件，而不是让Buildroot下载并解压缩工具链，而是可以告诉Buildroot您的工具链已在系统上安装的位置。只需`Toolchain`在可用的工具链配置文件中选择工具链配置文件，然后取消选择`Download toolchain automatically`，然后`Toolchain path`使用交叉编译工具链的路径填充 文本条目即可。
- 使用完全自定义的外部工具链。这对于使用crosstool-NG或Buildroot本身生成的工具链特别有用。为此，请`Custom toolchain`在`Toolchain`列表中选择解决方案 。您需要填写的`Toolchain path`，`Toolchain prefix`和`External toolchain C library`选项。然后，您必须告诉Buildroot您的外部工具链支持什么。如果您的外部工具链使用*glibc*库，则只需告诉您工具链是否支持C ++，以及它是否具有内置的RPC支持。如果您的外部工具链使用*uClibc* 库，那么您必须告诉Buildroot它是否支持RPC，宽字符，语言环境，程序调用，线程和C ++。在执行开始时，Buildroot会告诉您所选的选项是否与工具链配置不匹配。

我们的外部工具链支持已通过CodeSourcery和Linaro的工具链， [crosstool-NG](http://crosstool-ng.org/)生成的工具链以及Buildroot本身生成的工具链进行了测试。通常，所有支持*sysroot*功能的工具链 都应该起作用。如果没有，请立即与开发人员联系。

我们不支持OpenEmbedded或Yocto生成的工具链或SDK，因为这些工具链不是纯工具链（即仅编译器，binutils，C和C ++库）。相反，这些工具链带有大量预编译的库和程序。因此，Buildroot无法导入工具链的*sysroot*，因为它会包含通常由Buildroot生成的数百兆字节的预编译库。

我们也不支持使用发行版工具链（即发行版中安装的gcc / binutils / C库）作为为目标构建软件的工具链。这是因为您的分发工具链不是“纯”工具链（即仅使用C / C ++库），因此我们无法将其正确导入到Buildroot构建环境中。因此，即使您正在为x86或x86_64目标构建系统，也必须使用Buildroot或crosstool-NG生成交叉编译工具链。

如果要为项目生成自定义工具链（可用作Buildroot中的外部工具链），我们的建议是使用Buildroot本身进行构建（请参见 [第6.1.3节“使用Buildroot构建外部工具链”](http://nightly.buildroot.org/manual.html#build-toolchain-with-buildroot)）或使用 [crosstool-NG](http://crosstool-ng.org/)。

该后端的优点：

- 允许使用众所周知且经过测试的交叉编译工具链。
- 避免了交叉编译工具链的构建时间，这在嵌入式Linux系统的总体构建时间中通常非常重要。

该后端的缺点：

- 如果您预先构建的外部工具链存在错误，那么除非您使用Buildroot或Crosstool-NG自己构建外部工具链，否则可能很难从工具链供应商处获得修复。

### 6.1.3。使用Buildroot构建外部工具链

Buildroot内部工具链选项可用于创建外部工具链。以下是构建内部工具链并将其打包以供Buildroot本身（或其他项目）重用的一系列步骤。

使用以下详细信息创建新的Buildroot配置：

- 为您的目标CPU体系结构 选择适当的***\*目标选项\****
- 在***\*工具链\****菜单，保留默认***\*Buildroot里面工具链\**** 的***\*工具链类型\****，并根据需要配置工具链
- 在***\*系统配置\****菜单中，选择***\*无\****作为***\*初始化系统\****并***\*没有\****如***\*/ bin / sh的\****
- 在“ ***\*目标软件包”\****菜单中，禁用“ ***\*BusyBox”\****
- 在“ ***\*文件系统映像”\****菜单中，禁用***\*tar根文件系统\****

然后，我们可以触发构建，并要求Buildroot生成SDK。这将为我们方便地生成一个包含工具链的压缩包：

```
mkae sdk   #生成一个其他buildroot同样可以使用的交叉工具链
```

这将产生SDK tarball `$(O)/images`，名称类似于 `arm-buildroot-linux-uclibcgnueabi_sdk-buildroot.tar.gz`。

保存此压缩包，**因为它现在是您可以在其他Buildroot项目中用作外部工具链的工具链。**

在其他那些Buildroot项目中，在“ ***\*工具链”\****菜单中：

- 将**工具链类型**设置为**外部工具链**
- 将**工具链**设置为**自定义工具链**
- 设置**工具链起源**于**工具链的下载和安装**
- 将**工具链网址**设置为`file:///path/to/your/sdk/tarball.tar.gz`



## 6.2。/ dev管理

在Linux系统上，该`/dev`目录包含称为*设备文件的*特殊文件，这些 *文件*允许用户空间应用程序访问Linux内核管理的硬件设备。没有这些*设备文件*，即使Linux内核正确识别了硬件设备，您的用户空间应用程序也将无法使用它们。

在之下`System configuration`，`/dev management`Buildroot提供了四种不同的解决方案来处理`/dev`目录：

- 第一个解决方案是***\*使用设备表的静态\****。这是在Linux中处理设备文件的旧经典方法。使用这种方法，设备文件被永久存储在根文件系统中（即，它们在重新引导后仍然存在），并且没有任何东西可以在从系统中添加或删除硬件设备时自动创建和删除这些设备文件。因此，Buildroot使用*设备表*创建一组标准的设备文件，默认文件存储在`system/device_table_dev.txt`Buildroot源代码中。当Buildroot生成最终的根文件系统映像时，将处理此文件， 因此，*设备文件*在`output/target`目录中不可见。的`BR2_ROOTFS_STATIC_DEVICE_TABLE`选项允许更改Buildroot使用的默认设备表，或添加其他设备表，以便Buildroot在构建过程中创建其他*设备文件*。因此，如果使用此方法，并且系统中缺少*设备文件，*则可以例如创建一个`board///device_table_dev.txt`文件，其中包含对其他*设备文件*的描述，然后可以将其设置`BR2_ROOTFS_STATIC_DEVICE_TABLE`为`system/device_table_dev.txt board///device_table_dev.txt`。有关设备表文件格式的更多详细信息，请参见 [第23章，*Makedev语法文档*](http://nightly.buildroot.org/manual.html#makedev-syntax)。
- 第二种解决方案是***\*仅使用devtmpfs进行动态处理\****。*devtmpfs*是Linux内核中的一个虚拟文件系统，已在内核2.6.32中引入（如果使用较旧的内核，则不能使用此选项）。当安装到中时`/dev`，该虚拟文件系统将自动使*设备文件*在添加和从系统中删除硬件设备时显示和消失。该文件系统在重新启动后并不持久：它由内核动态填充。使用*devtmpfs*需要启用以下内核配置选项： `CONFIG_DEVTMPFS`和`CONFIG_DEVTMPFS_MOUNT`。当Buildroot负责为嵌入式设备构建Linux内核时，请确保启用了这两个选项。但是，如果您在Buildroot之外构建Linux内核，则有责任启用这两个选项（如果失败，则Buildroot系统将不会启动）。
- 第三种解决方案是***\*使用devtmpfs + mdev进行动态处理\****。此方法还依赖于*devtmpfs*虚拟文件系统之上（这样的要求进行了详细`CONFIG_DEVTMPFS`而 `CONFIG_DEVTMPFS_MOUNT`在内核配置中启用仍然适用），但增加了`mdev`在它上面的用户空间程序。`mdev` 是BusyBox的程序部分，内核在每次添加或删除设备时都会调用。由于`/etc/mdev.conf` 配置文件的存在，`mdev`可以配置为例如对设备文件设置特定的权限或所有权，在设备出现或消失时调用脚本或应用程序等。基本上，它允许*用户空间*对设备添加和删除事件做出反应。`mdev`例如，可用于在设备出现在系统上时自动加载内核模块。`mdev`如果您的设备需要固件，这也很重要，因为它将负责将固件内容推送到内核。`mdev`是的轻量级实现（功能较少）`udev`。有关`mdev`其配置文件及其语法的更多详细信息，请参见http://git.busybox.net/busybox/tree/docs/mdev.txt。
- 第四个解决方案是***\*使用devtmpfs + eudev的Dynamic\****。此方法还依赖于上面详述的*devtmpfs*虚拟文件系统，但在其上面添加了`eudev`用户空间守护程序。`eudev` 是一个后台运行的守护进程，当从系统中添加或删除设备时，内核会调用该守护程序。与相比`mdev`，它是重量级更大的解决方案，但具有更高的灵活性。 `eudev`是的独立版本`udev`，是大多数桌面Linux发行版中使用的原始用户空间守护程序，该守护程序现在是Systemd的一部分。有关更多详细信息，请参见 http://en.wikipedia.org/wiki/Udev。

Buildroot开发人员的建议是从***\*仅使用devtmpfs\****的***\*Dynamic\****解决方案开始，直到您需要在添加/删除设备或需要固件时通知用户空间，在这种情况下，通常最好***\*使用devtmpfs + mdev进行Dynamic处理。\****解。

请注意，如果`systemd`选择init系统，则/ dev管理将由`udev`提供的程序执行`systemd`。

## 6.3。初始化系统

该*INIT*程序是由内核（它携带PID号1）开始的第一用户空间程序，并且是负责启动用户空间服务和程序（例如：网络服务器，图形应用程序，其他网络服务器等）。

的buildroot允许使用三种不同类型方式的初始化系统，它可以从被选择的`System configuration`，`Init system`：

- 第一个解决方案是***\*BusyBox\****。在许多程序中，BusyBox都有一个基本`init`程序的实现，对于大多数嵌入式系统而言，这已经足够了。启用`BR2_INIT_BUSYBOX`将会确保BusyBox将生成并安装其`init`程序。这是Buildroot中的默认解决方案。BusyBox `init`程序将`/etc/inittab`在引导时读取文件，以了解操作方法。可以在[http://git.busybox.net/busybox/tree/examples/inittab中](http://git.busybox.net/busybox/tree/examples/inittab)找到此文件的语法 （请注意，BusyBox `inittab`语法很特殊：请勿使用`inittab` Internet上的随机文档来了解BusyBox`inittab`）。`inittab`Buildroot中的默认值存储在 `system/skeleton/etc/inittab`。除了挂载一些重要的文件系统之外，默认inittab的主要工作是启动 `/etc/init.d/rcS`Shell脚本并启动`getty`程序（提供登录提示）。
- 第二种解决方案是***\*systemV\****。该解决方案使用旧的传统*sysvinit*程序，该程序包装在的Buildroot中 `package/sysvinit`。这是大多数桌面Linux发行版中使用的解决方案，直到他们切换到更新的替代版本（如Upstart或Systemd）为止。`sysvinit`也可用于`inittab`文件（语法与BusyBox中的语法略有不同）。`inittab`带有此初始化解决方案的默认安装位于中`package/sysvinit/inittab`。
- 第三种解决方案是***\*systemd\****。`systemd`是用于Linux的新一代init系统。它的功能远不止传统的*init* 程序：积极的并行化功能，使用套接字和D-Bus激活来启动服务，按需启动守护程序，使用Linux控制组跟踪进程，支持快照和恢复系统状态，等`systemd`在相对复杂的嵌入式系统上很有用，例如需要D-Bus和彼此通信的服务的系统。值得注意的是，它`systemd` 带来了大量的大型依赖项：`dbus`，`udev` 以及更多。有关的更多详细信息`systemd`，请参见http://www.freedesktop.org/wiki/Software/systemd。

Buildroot开发人员推荐的解决方案是使用 ***\*BusyBox初始化，\****因为它对于大多数嵌入式系统来说已经足够。***\*systemd\****可用于更复杂的情况。



------

[[3\]](http://nightly.buildroot.org/manual.html#idm140327132464304)该术语不同于GNU configure使用的术语，其中主机是运行应用程序的计算机（通常与目标计算机相同）。

# 第7章其他组件的配置

在尝试修改下面的任何组件之前，请确保您已经配置了Buildroot本身，并启用了相应的软件包。

-  BusyBox 

  如果您已经有一个BusyBox配置文件，则可以使用在Buildroot配置中直接指定此文件`BR2_PACKAGE_BUSYBOX_CONFIG`。否则，Buildroot将从默认的BusyBox配置文件开始。要对配置进行后续更改，请使用`make busybox-menuconfig`来打开BusyBox配置编辑器。也可以通过环境变量指定BusyBox配置文件，尽管不建议这样做。有关更多详细信息[，](http://nightly.buildroot.org/manual.html#env-vars)请参见 [第8.6节“环境变量”](http://nightly.buildroot.org/manual.html#env-vars)。

- uClibc

  uClibc的配置与BusyBox的配置相同。用于指定现有配置文件的配置变量为`BR2_UCLIBC_CONFIG`。进行后续更改的命令是`make uclibc-menuconfig`。

- Linux内核

  如果已经有了内核配置文件，则可以使用来在Buildroot配置中直接指定此文件`BR2_LINUX_KERNEL_USE_CUSTOM_CONFIG`。如果还没有内核配置文件，则可以使用Buildroot配置指定defconfig来开始，也可以使用`BR2_LINUX_KERNEL_USE_DEFCONFIG`创建空文件并将其指定为自定义配置文件来开始`BR2_LINUX_KERNEL_USE_CUSTOM_CONFIG`。要对配置进行后续更改，请使用`make linux-menuconfig`来打开Linux配置编辑器。

-  Barebox 裸机

  Barebox的配置与Linux内核的配置相同。相应的配置变量是`BR2_TARGET_BAREBOX_USE_CUSTOM_CONFIG`和`BR2_TARGET_BAREBOX_USE_DEFCONFIG`。要打开配置编辑器，请使用`make barebox-menuconfig`。

- U型靴

  U-Boot（版本2015.04或更高版本）的配置与Linux内核的配置相同。相应的配置变量是`BR2_TARGET_UBOOT_USE_CUSTOM_CONFIG`和`BR2_TARGET_UBOOT_USE_DEFCONFIG`。要打开配置编辑器，请使用`make uboot-menuconfig`。

# 第8章Buildroot的一般用法

## 8.1 要点

这是一系列技巧，可帮助您充分利用Buildroot。

**显示make执行的所有命令：** 

```
 $make V=1 <target>
```

**显示带有defconfig的板列表：** 

```
 $ make list-defconfigs
```

**显示所有可用的目标：** 

```
 $make help
```

并非所有目标都始终可用，`.config`文件中的某些设置可能会隐藏一些目标：

- `busybox-menuconfig`仅在`busybox`启用时有效；
- `linux-menuconfig`并且`linux-savedefconfig`仅在`linux`启用后才能工作 ；
- `uclibc-menuconfig` 仅在内部工具链后端中选择uClibc C库时可用；
- `barebox-menuconfig`并且`barebox-savedefconfig`仅在`barebox`启用引导加载程序时才起作用 。
- `uboot-menuconfig`并且`uboot-savedefconfig`仅在`U-Boot`启用引导加载程序时才起作用 。

**清洁：** 更改任何体系结构或工具链配置选项时，都需要显式清洁。

要删除所有构建产品（包括构建目录，主机host，暂存staging和目标树target trees，映像img和工具链）：

```
 $make clean
```

**生成手册：** 当前的手册源位于*docs / manual*目录中。生成手册：

```
 $ make manual-clean
 $ make manual
```

手动输出将在*output / docs / manual中*生成。

**注意*

- 构建文档需要一些工具（请参阅： [第2.2节“可选软件包”](http://nightly.buildroot.org/manual.html#requirement-optional)）。

**为新目标重置Buildroot：** 删除所有构建产品和配置：

```
 $make distclean
```

**注意**。** 如果`ccache`启用，则运行Buildroot所使用的编译器缓存，`make clean`或`distclean`不会清空它。要删除它，请参见[第8.13.3节“ `ccache`在Buildroot中使用”](http://nightly.buildroot.org/manual.html#ccache)。

**dumping 内部make变量：** 可以dump出来已知要make的变量及其值：

```
 $ make -s printvars VARS ='VARIABLE1 VARIABLE2'
 VARIABLE1 = value_of_variable
 VARIABLE2 = value_of_variable
```

可以使用一些变量来调整输出：

- `VARS` 将列表限制为名称与指定的make-patterns匹配的变量-必须设置此变量，否则不打印任何内容
- `QUOTED_VARS`（如果设置为`YES`）将单引号
- `RAW_VARS`（如果设置为`YES`）将打印未扩展的值

例如：

For example:

```
 $ make -s printvars VARS=BUSYBOX_%DEPENDENCIES
 BUSYBOX_DEPENDENCIES=skeleton toolchain
 BUSYBOX_FINAL_ALL_DEPENDENCIES=skeleton toolchain
 BUSYBOX_FINAL_DEPENDENCIES=skeleton toolchain
 BUSYBOX_FINAL_PATCH_DEPENDENCIES=
 BUSYBOX_RDEPENDENCIES=ncurses util-linux
 $ make -s printvars VARS=BUSYBOX_%DEPENDENCIES QUOTED_VARS=YES
 BUSYBOX_DEPENDENCIES='skeleton toolchain'
 BUSYBOX_FINAL_ALL_DEPENDENCIES='skeleton toolchain'
 BUSYBOX_FINAL_DEPENDENCIES='skeleton toolchain'
 BUSYBOX_FINAL_PATCH_DEPENDENCIES=''
 BUSYBOX_RDEPENDENCIES='ncurses util-linux'
 $ make -s printvars VARS=BUSYBOX_%DEPENDENCIES RAW_VARS=YES
 BUSYBOX_DEPENDENCIES=skeleton toolchain
 BUSYBOX_FINAL_ALL_DEPENDENCIES=$(sort $(BUSYBOX_FINAL_DEPENDENCIES) $(BUSYBOX_FINAL_PATCH_DEPENDENCIES))
 BUSYBOX_FINAL_DEPENDENCIES=$(sort $(BUSYBOX_DEPENDENCIES))
 BUSYBOX_FINAL_PATCH_DEPENDENCIES=$(sort $(BUSYBOX_PATCH_DEPENDENCIES))
 BUSYBOX_RDEPENDENCIES=ncurses util-linux
```

The output of quoted variables can be reused in shell scripts, for example:

```
 $ eval $(make -s printvars VARS=BUSYBOX_DEPENDENCIES QUOTED_VARS=YES)
 $ echo $BUSYBOX_DEPENDENCIES
 skeleton toolchain
```

## 8.2 了解何时需要完全重建

Buildroot里面不尝试检测一下系统的部分应该在系统配置是通过改变重建`make menuconfig`，`make xconfig`或的其他配置工具之一。在某些情况下，Buildroot应该重建整个系统，在某些情况下，仅应重建软件包的特定子集。但是，以完全可靠的方式检测到这一点非常困难，因此Buildroot开发人员已决定不尝试这样做。

相反，用户有责任知道何时需要完全重建。作为提示，这里有一些经验法则可以帮助您了解如何使用Buildroot：

- **更改目标体系结构配置**时，需要完全重建。例如，更改体系结构变体，二进制格式或浮点策略会对整个系统产生影响。
- 当**更改工具链配置**时，通常需要完全重建。更改工具链配置通常涉及更改编译器版本，C库的类型或其配置或其他一些基本配置项，这些更改会影响整个系统。
- 将其他软件包添加到配置时，不一定需要完全重建。Buildroot将检测到从未构建过此软件包，并将对其进行构建。但是，如果此程序包是可以由已构建的程序包选择性使用的库，则Buildroot将不会自动重建它们。您要么知道应该重建哪些软件包，要么可以手动重建它们，或者应该进行完全重建。例如，假设您使用`ctorrent` 包构建了一个系统，但没有`openssl`。您的系统可以运行，但是您意识到希望在中具有SSL支持`ctorrent`，因此您可以`openssl`在Buildroot配置中启用该 软件包并重新开始构建。Buildroot将检测到`openssl`应该构建并将要构建它，但是它不会检测到`ctorrent`应该重新构建以受益于`openssl`添加OpenSSL支持。您要么必须进行完全重建，要么`ctorrent`自行重建。
- 从配置中**删除软件包时**，Buildroot不会执行任何特殊操作。它不会从目标根文件系统或工具链*sysroot中*删除此软件包安装的文件 。需要完全重建才能摆脱此软件包。但是，通常您不必立即删除此软件包：您可以等待下一个午餐休息时间以从头开始重新构建。
- **更改软件包的子选项时，不会自动重建软件包。**进行此类更改后，仅重建此软件包通常就足够了，除非启用package子选项，否则会向该软件包中添加一些功能，这些功能对于已构建的另一个软件包很有用。同样，Buildroot不会跟踪何时应该重新构建软件包：一旦构建了软件包，就不会对其进行重新构建，除非明确要求这样做。
- **更改根文件系统框架**后，需要完全重建。但是，在更改根文件系统覆盖图，进行后生成脚本或后映像脚本时，无需进行完全重建：简单的`make`调用将考虑这些更改。
- `FOO_DEPENDENCIES`重建或删除其中 列出的软件包时`foo`，不会自动重建该软件包。例如，如果在`bar`中列出了一个软件包`FOO_DEPENDENCIES`，`FOO_DEPENDENCIES = bar`并且`bar`更改了软件包的配置，则配置更改不会导致`foo` 自动重建软件包。在这种情况下，您可能需要重建构建中引用`bar`其的任何程序包`DEPENDENCIES`，或者执行完全重建以确保所有`bar`相关程序包都是最新的。

一般而言，当您遇到构建错误并且不确定所做的配置更改可能带来的后果时，请进行完全重建。如果您遇到相同的构建错误，则可以确定该错误与软件包的部分重建无关，并且如果此错误发生在官方Buildroot的软件包中，请立即举报该问题！随着您对Buildroot的了解的发展，您将逐步了解何时真正需要进行完全重建，并且可以节省越来越多的时间。

作为参考，可以通过运行以下命令来进行完全重建：

```
$make clean all
```

## 8.3。了解如何重建软件包，而不全部重建

Buildroot用户提出的最常见问题之一是如何**重建给定的软件包**或如何**删除软件包**而不重新构建所有内容。

Buildroot不支持删除软件包，而无需从头开始重建。这是因为Buildroot不会跟踪哪个软件包在`output/staging`和 `output/target`目录中安装了哪些文件，或者哪个软件包将根据另一个软件包的可用性进行不同的编译。

从头开始重建单个软件包的**最简单方法**是在中删除其构建目录`output/build`。然后，Buildroot将从头开始重新提取，重新配置，重新编译和重新安装此软件包。您可以使用`make <package>-dirclean`命令执行此操作。

另一方面，如果您**只想从编译步骤重新启动软件包的构建过程，**则可以运行`make <package>-rebuild`。这将重新从头开始编译和安装包的，而不是从0开始：它基本上是重新执行`make`和`make install`封装内，这只会重编译有改变的文件。

如果要**从其配置步骤**重新启动软件包的构建过程，可以运行`make <package>-reconfigure`。它将重新启动软件包的配置，编译和安装。

尽管` <package> -rebuild`隐含` <package> -reinstall`和 ` <package> -reconfigure`隐含` <package> -rebuild`，但这些目标以及``仅作用于所述软件包，并且**不会触发重新创建根文件系统映像imag**。如果有必要重新创建根文件系统，则应另外运行`make`或`make all`。

在内部，Buildroot创建所谓的*stamp文件，*以跟踪每个软件包已完成的构建步骤。它们存储在包构建目录， `output/build/<package>-<version>/`并命名为 `.stamp_<step-name>`。上面详细介绍的命令仅操作这些标记文件即可强制Buildroot重新启动软件包构建过程的一组特定步骤。

有关包装特殊制造目标的更多详细信息，请参见[第8.13.5节“特定](http://nightly.buildroot.org/manual.html#pkg-build-steps)于 [包装的](http://nightly.buildroot.org/manual.html#pkg-build-steps)[*制造*](http://nightly.buildroot.org/manual.html#pkg-build-steps)[目标”](http://nightly.buildroot.org/manual.html#pkg-build-steps)。

## 8.4。离线版本

如果您打算进行脱机构建，而只想下载先前在配置器中选择的所有源（*menuconfig*，*nconfig*，*xconfig*或*gconfig*），则发出：

```
 $make source
```

现在，您可以断开`dl` 目录的连接或将目录的内容复制到构建主机。

## 8.5。建立树外构建

树内构建：默认Buildroot生成的所有内容都存储在Buildroot工作目录下的`output`目录。

树外构建：可以指定Buildroot生成的所有内容都存储在buildroot目录外的其他目录，用相对路径多绝对路径表示。

Buildroot还支持使用类似于Linux内核的语法可以重工作树外中构建。要使用它，请添加`O=`到make命令行中：

```
 $ make O = / tmp / build
```

要么：

```
$ cd /tmp/build; make O=$PWD -C path/to/buildroot
```

所有输出文件将位于下`/tmp/build`。如果该`O` 路径不存在，则Buildroot将创建它。

***\*注意：\****该`O`路径可以是绝对路径，也可以是相对路径，但是如果将其作为相对路径传递，则必须注意，它是相对于主Buildroot源目录（***\*而不是\****当前工作目录）进行解释的。

使用树外构建时，Buildroot `.config`和临时文件也存储在输出目录中。这意味着只要它们使用唯一的输出目录，就可以使用同一源代码树安全地并行运行多个构建。

为了易于使用，Buildroot在输出目录中生成一个Makefile包装器-因此，**在第一次运行后，您不需要再传递`O=<…>` 和`-C <…>`，**只需运行（在输出目录中）：

```
 $ make <target>
```

## 8.6。环境变量

当将它们传递到`make`环境中或在环境中设置时，Buildroot还尊重一些环境变量：

- `HOSTCXX`，主机C ++编译器
- `HOSTCC`，主机C编译器
-  UCLIBC_CONFIG_FILE=<path/to/.config> ，如果要构建内部工具链，则用于编译uClibc的uClibc配置文件的路径。注意，uClibc配置文件也可以从配置界面进行设置，因此可以通过Buildroot `.config`文件进行设置。这是建议的设置方式。
-  BUSYBOX_CONFIG_FILE=<path/to/.config> ，即BusyBox配置文件的路径。注意，也可以通过Buildroot `.config`文件从配置界面设置BusyBox配置文件。这是建议的设置方式。
- `BR2_CCACHE_DIR` 使用ccache时，将覆盖Buildroot存储缓存文件的目录。
- `BR2_DL_DIR`覆盖Buildroot存储/检索下载文件的目录请注意，也可以从Buildroot `.config`文件通过配置界面设置Buildroot下载目录。有关如何设置下载目录的更多详细信息[，](http://nightly.buildroot.org/manual.html#download-location)请参见[第8.13.4节“下载包的位置”](http://nightly.buildroot.org/manual.html#download-location)。
- `BR2_GRAPH_ALT`（如果已设置且为非空），则在构建时图表中使用其他颜色方案
- `BR2_GRAPH_OUT`设置生成的图形的文件类型`pdf`（默认设置）或`png`。
- `BR2_GRAPH_DEPS_OPTS`将额外的选项传递给依赖图；有关可接受的选项 [，](http://nightly.buildroot.org/manual.html#graph-depends)请参见 [第8.8节“绘制软件包之间的依赖关系图”](http://nightly.buildroot.org/manual.html#graph-depends)
- `BR2_GRAPH_DOT_OPTS`逐字作为选项传递给`dot`实用程序以绘制依赖图。
- `BR2_GRAPH_SIZE_OPTS`将额外的选项传递给尺寸图；有关可接受的选项[，](http://nightly.buildroot.org/manual.html#graph-size)请参见 [第8.10节“图形化软件包的文件系统大小贡献”](http://nightly.buildroot.org/manual.html#graph-size)。

使用位于顶层目录和$ HOME中的配置文件的示例：

```
 $ make UCLIBC_CONFIG_FILE = uClibc.config BUSYBOX_CONFIG_FILE = $ HOME / bb.config
```

如果要使用默认`gcc` 或`g`++ 以外的其他编译器在主机上构建帮助程序二进制文件，请执行

```
 $ make HOSTCXX = g ++-4.3-HEAD HOSTCC = gcc-4.3-HEAD
```

## 8.7。有效处理文件系统映像

文件系统映像可能会变得非常大，具体取决于您选择的文件系统，程序包的数量，是否配置了可用空间……但是，文件系统映像中的某些位置可能只是*空的*（例如，长时间 *为零*）。这样的文件称为*稀疏*文件。

大多数工具可以有效地处理稀疏文件，并且只会存储或写入稀疏文件中不为空的部分。

例如：

- `tar`接受`-S`告诉它仅存储稀疏文件的非零块的选项：
  - `tar cf archive.tar -S [files…]` 将有效地将稀疏文件存储在tarball中
  - `tar xf archive.tar -S` 将有效地存储从压缩包中提取的稀疏文件
- `cp`接受该`--sparse=WHEN`选项（`WHEN`是一个`auto`， `never`或`always`）：
  - `cp --sparse=always source.file dest.file``dest.file`如果`source.file`长期运行零， 将 `dest.file`  生成一个稀疏文件

其他工具可能具有类似的选项。请查阅各自的手册页。

如果需要存储文件系统映像（例如，从一台计算机传输到另一台计算机），或者需要将它们发送（例如，发送给问答团队），则可以使用稀疏文件。

但是请注意，在使用的稀疏模式时将文件系统映像刷新到设备`dd`可能会导致文件系统损坏（例如，ext2文件系统的块位图可能已损坏；或者，如果文件系统中的文件稀疏，则这些部分可能不会损坏）。读回时为全零）。您仅应在构建计算机上处理文件时使用稀疏文件，而不是在将文件传输到将在目标计算机上使用的实际设备时使用稀疏文件。

## 8.8。绘制软件包之间的依赖关系

Buildroot的工作之一是了解软件包之间的依赖关系，并确保它们以正确的顺序构建。这些依赖有时可能非常复杂，对于给定的系统，通常不容易理解为什么Buildroot将这样的软件包引入了构建。

为了帮助理解依赖性，从而更好地理解嵌入式Linux系统中不同组件的作用，Buildroot能够生成依赖性图。

要生成已编译的**整个系统的依赖关系图**，只需运行：

```
make graph-depends
```

您将在中找到生成的图形 `output/graphs/graph-depends.pdf`。

如果您的系统很大，则依赖关系图可能太复杂且难以阅读。因此，可以**仅针对给定的包**生成依赖关系图：

```
make <pkg>-graph-depends
```

您将在中找到生成的图形 `output/graph/-graph-depends.pdf`。

请注意，依赖关系图是使用*Graphviz*项目中的`dot`工具生成的，该工具必须已安装在系统上才能使用此功能。在大多数发行版中，它都作为软件包`graphviz`提供。

默认情况下，依赖关系图以PDF格式生成。但是，通过传递`BR2_GRAPH_OUT`环境变量，可以切换到其他输出格式，例如PNG，PostScript或SVG。`dot`工具的`-T`选项支持设置所有格式。

```
BR2_GRAPH_OUT=svg make graph-dependsBR2_GRAPH_OUT = svg
```

该`graph-depends`行为可以通过设置选项来控制 `BR2_GRAPH_DEPS_OPTS`环境变量。可接受的选项是：

- `--depth N`，`-d N`以限制依赖深度`N`的水平。默认值为`0`表示没有限制。
- `--stop-on PKG`，，`-s PKG`以停止绘制软件包上的图形`PKG`。 `PKG`可以是实际的软件包名称，全局名称，关键字*virtual* （在虚拟软件包上停止）或关键字*host*（在主机软件包上停止）。该程序包仍然存在于图上，但不存在依赖关系。
- `--exclude PKG`，`-x PKG`一样`--stop-on`，也省略了`PKG`从图。
- `--transitive`，`--no-transitive`绘制（或不绘制）传递依赖关系。默认为不绘制传递依赖。
- `--colors R,T,H`，以逗号分隔的颜色列表，用于绘制根包（`R`），目标包（`T`）和主机包（`H`）。默认为：`lightblue,grey,gainsboro`

```
BR2_GRAPH_DEPS_OPTS='-d 3 --no-transitive --colors=red,green,blue' make graph-depends
```

## 8.9。绘制构建持续时间

当系统构建需要很长时间时，了解哪些软件包是构建时间最长的软件包，看看是否可以采取任何措施来加快构建速度，有时会很有用。为了帮助进行此类构建时间分析，Buildroot收集了每个软件包每个步骤的构建时间，并允许从该数据生成图形。

要在构建后生成构建时间图，请运行：

```
make graph-build
```

这将在中生成一组文件`output/graphs`：

- `build.hist-build.pdf`，每个包的构建时间的直方图，按构建顺序排序。
- `build.hist-duration.pdf`，每个软件包构建时间的直方图，按持续时间排序（最长的时间）
- `build.hist-name.pdf`，每个软件包的构建时间的直方图，按软件包名称排序。
- `build.pie-packages.pdf`，每个包的构建时间的饼图
- `build.pie-steps.pdf`，这是在软件包构建过程的每个步骤中花费的全球时间的饼图。

这个`graph-build`目标需要安装Python的Matplotlib和numpy的库（`python-matplotlib`和`python-numpy`大多数发行版），也是`argparse`，如果您使用的是Python的版本低于2.7（模块`python-argparse`上大多数发行版）。

默认情况下，图形的输出格式为PDF，但是可以使用`BR2_GRAPH_OUT`环境变量选择其他格式。支持的唯一其他格式是PNG：

```
BR2_GRAPH_OUT=png make graph-build
```

## 8.10。用图形表示软件包的文件系统大小

当目标系统增长时，了解每个Buildroot软件包对整个根文件系统大小的贡献有时会很有用。为了帮助进行此类分析，Buildroot收集有关每个软件包安装的文件的数据，并使用此数据，生成图形和CSV文件，详细说明不同软件包的大小。

要在构建后生成这些数据，请运行：

```
make graph-size
```

这将产生：

- `output/graphs/graph-size.pdf`，是每个包对整个根文件系统大小的贡献的饼图
- `output/graphs/package-size-stats.csv`，一个CSV文件，给出每个包对整个根文件系统大小的大小贡献
- `output/graphs/file-size-stats.csv`，是一个CSV文件，提供每个已安装文件对其所属软件包的大小以及整个文件系统大小的贡献。

此`graph-size`目标要求安装Python Matplotlib库（`python-matplotlib`在大多数发行版上），并且`argparse`如果您使用的Python版本低于2.7（`python-argparse`在大多数发行版上），则还 需要安装模块。

就像持续时间图一样，`BR2_GRAPH_OUT`支持环境变量来调整输出文件格式。 有关此环境变量的详细信息[，](http://nightly.buildroot.org/manual.html#graph-depends)请参见[第8.8节“绘制软件包之间的依赖关系图”](http://nightly.buildroot.org/manual.html#graph-depends)。

另外，可以设置环境变量`BR2_GRAPH_SIZE_OPTS` 以进一步控制所生成的图。可接受的选项是：

- `--size-limit X`，，`-l X`会将单个贡献低于`X`百分比的所有软件包分组 到图中标记为“ *其他”*的单个条目。默认情况下，`X=0.01`这意味着每个贡献少于1％的软件包将归类为“ *其他”*。可接受的值在范围内`[0.0..1.0]`。
- `--iec`，`--binary`，`--si`，`--decimal`，使用IEC（二进制，1024功率）或SI（十进制，1000权力;默认值）前缀。
- `--biggest-first`，以按降序而不是按升序对包进行排序。

**注意。** 仅在完全干净的重建之后，收集的文件系统大小数据才有意义。一定要运行`make clean all`使用前`make graph-size`。

要比较两个不同Buildroot编译的根文件系统大小，例如在调整配置后或切换到另一个Buildroot版本时，请使用`size-stats-compare`脚本。它需要两个 `file-size-stats.csv`文件（由产生`make graph-size`）作为输入。有关更多详细信息，请参考此脚本的帮助文本：

```
utils / size-stats-compare -h
```

## 8.11。顶级并行构建

**注意。** 本节介绍了一个非常实验性的功能，该功能在某些非常规情况下会达到收支平衡。使用风险自负。

Buildroot始终能够在每个软件包的基础上使用并行构建：每个软件包都是由Buildroot使用`make -jN`（或非基于Make的构建系统的等效调用）构建的。缺省情况下，并行级别是CPU数量+ 1，但是可以使用`BR2_JLEVEL`配置选项进行调整。

但是，直到2020.02为止，Buildroot都是以串行方式构建软件包：每个软件包都是一个接一个地构建的，而没有并行化软件包之间的构建。从2020.02开始，Buildroot对***\*顶级并行构建\****提供了实验性支持，***\*通过并行构建\****不具有依赖关系的软件包，可以节省大量的构建时间。但是，此功能被标记为实验性功能，已知在某些情况下不起作用。

为了使用顶级并行构建，必须执行以下操作：

1. `BR2_PER_PACKAGE_DIRECTORIES`在Buildroot配置中 启用该选项
2. `make -jN`在启动Buildroot构建时 使用

在内部，`BR2_PER_PACKAGE_DIRECTORIES`会启用一种称为***\*per-package directory\****的机制，这将产生以下效果：

- 代替所有软件包通用的全局*目标*目录和全局*主机*目录，将分别在和中 使用每个软件包的*目标*目录和*主机*目录。在构建开始时，将从软件包依赖项的相应文件夹中填充这些文件夹。因此，编译器和所有其他工具将只能查看和访问由明确列出的依赖项安装的文件。 `$(O)/per-package//target/``$(O)/per-package//host/`````
- 在构建结束，全球*目标*和*主*目录将被填充，位于`$(O)/target`和`$(O)/host` 分别。这意味着在构建期间，这些文件夹将为空，并且仅在构建的最后才填充它们。

## 8.13 高级用法

### 8.13.1。在Buildroot外部使用生成的工具链

您可能希望针对编译自己的目标，编译自己的程序或编译一些包，不用在Buildroot打包。为此，**您可以使用Buildroot生成的工具链。**

Buildroot生成的工具链默认位于中 `output/host/`。使用它的最简单方法是添加`output/host/bin/`到您的PATH环境变量，然后使用`ARCH-linux-gcc`，`ARCH-linux-objdump`，`ARCH-linux-ld`，等。

或者，**Buildroot也可以将工具链和所有选定软件包的开发文件导出为SDK**， 通过运行命令`make sdk`。这将生成主机目录内容的压缩包，该主机 `output/host/`名的名称`_sdk-buildroot.tar.gz`（可以通过设置环境变量进行覆盖`BR2_SDK_PREFIX`）位于输出目录中`output/images/`。

将该压缩包能发布给应用程序开发人员。当他们要开发应用，且不是打包为Buildroot软件包时。

提取SDK压缩文件后，用户必须运行脚本 `relocate-sdk.sh`（位于SDK的顶部目录），以确保所有路径更新为最小路径|。

另外，如果您只想准备SDK而没有生成tarball（例如，因为您将只是移动`host`目录，或者将自己生成tarball），则Buildroot还可以让您准备SDK，`make prepare-sdk`而无需实际生成压缩包。

### 8.13.2。`gdb`在Buildroot中使用

Buildroot允许进行交叉调试，其中调试器在构建计算机上运行，并`gdbserver`在目标计算机上与之通信以控制程序的执行。

为达到这个：

- 如果您使用的是*内部工具链*（由Buildroot里面建），您必须启用`BR2_PACKAGE_HOST_GDB`，`BR2_PACKAGE_GDB`和 `BR2_PACKAGE_GDB_SERVER`。这样可以确保交叉gdb和gdbserver均已构建，并且gdbserver已安装到目标。
- 如果您使用的是*外部工具链*，则应启用`BR2_TOOLCHAIN_EXTERNAL_GDB_SERVER_COPY`，这会将外部工具链附带的gdbserver复制到目标。如果您的外部工具链没有跨gdb或gdbserver，也可以通过启用与*内部工具链后端*相同的选项，让Buildroot来构建它们。

现在，要开始调试名为的程序`foo`，您应该在目标上运行：

```
gdbserver：2345 foo
```

这将导致`gdbserver`在TCP端口2345上侦听来自交叉gdb的连接。

然后，在主机上，应该使用以下命令行启动交叉gdb：

```
buildroot>/output/host/bin/<tuple>-gdb -x <buildroot>/output/staging/usr/share/buildroot/gdbinit foo
```

当然，`foo`必须在当前目录中可用，并带有调试符号。通常，您从`foo`构建目录开始此命令（而不是从`output/target/`剥离该目录中的二进制文件时开始）。

该`  <buildroot> /output/staging/usr/share/buildroot/gdbinit`文件将告诉交叉gdb在哪里找到目标库。

最后，从交叉gdb连接到目标：

```
(gdb) target remote <target ip address>:2345
```

### 8.13.3。`ccache`在Buildroot中使用

[ccache](http://ccache.samba.org/)是编译器缓存。它存储每个编译过程产生的目标文件，并能够通过使用预先存在的目标文件来跳过将来对同一源文件（具有相同的编译器和相同的参数）的编译。从头开始进行几乎完全相同的构建时，它可以很好地加快构建过程。

`ccache`支持集成在Buildroot中。您只需要启用 `Enable compiler cache`中`Bu  ild options`。这将自动构建`ccache`并将其用于每个主机和目标编译。

缓存位于中`$HOME/.buildroot-ccache`。它存储在Buildroot输出目录之外，以便可以由单独的Buildroot构建共享。如果要摆脱缓存，只需删除此目录。

您可以通过运行来获取有关缓存的统计信息（其大小，命中次数，未命中次数等）`make ccache-stats`。

生成目标`ccache-options`和`CCACHE_OPTIONS`变量提供对ccache的更通用的访问。例如

```
＃设置缓存限制大小
使CCACHE_OPTIONS =“-max-size = 5G” ccache-options

＃零个统计计数器
使CCACHE_OPTIONS =“-zero-stats” ccache-options
```

`ccache`对源文件和编译器选项进行哈希处理。如果编译器选项不同，则将不使用缓存的目标文件。但是，许多编译器选项都包含登台目录的绝对路径。因此，在不同的输出目录中构建将导致许多高速缓存未命中。

为避免此问题，buildroot具有`Use relative paths`选项（`BR2_CCACHE_USE_BASEDIR`）。这会将指向输出目录内部的所有绝对路径重写为相对路径。因此，更改输出目录不再导致高速缓存未命中。

相对路径的一个缺点是它们最终还会成为目标文件中的相对路径。因此，例如，除非您先cd到输出目录，否则调试器将不再找到该文件。

有关此绝对路径的重写的更多详细信息，请参见[ccache手册的“在不同目录中编译”部分](https://ccache.samba.org/manual.html#_compiling_in_different_directories)。

### 8.13.4。下载包的位置

Buildroot下载的各种压缩文件都存储在中`BR2_DL_DIR`，默认情况下是`dl`目录。如果要保留一个完整版本的Buildroot，该版本可以与相关的tarball一起使用，则可以制作此目录的副本。这将允许您使用完全相同的版本重新生成工具链和目标文件系统。

如果维护多个Buildroot树，则最好具有共享的下载位置。这可以通过将`BR2_DL_DIR`环境变量指向目录来实现 。如果设置了该值，`BR2_DL_DIR`则Buildroot配置中的值将被覆盖。以下行应添加到中`<~/.bashrc>`。

```
 导出BR2_DL_DIR = <共享的下载位置>
```

也可以`.config`使用`BR2_DL_DIR`选项在文件中 设置下载位置。与.config文件中的大多数选项不同，此值被`BR2_DL_DIR`环境变量覆盖。

### 8.13.5。指定包构建目标

运行`make <package>  `将构建并安装该特定程序包及其依赖项。

对于依赖Buildroot基础结构的软件包，有许多特殊的make目标，可以像这样独立调用：

```
make <package>-<target>
```

软件包构建目标是（按照它们执行的顺序）：

| 命令/目标         | 描述                                                         |
| ----------------- | ------------------------------------------------------------ |
| `source`          | 获取源代码（下载压缩包，克隆源代码仓库等）                   |
| `depends`         | 构建并安装构建软件包所需的所有依赖项                         |
| `extract`         | 将源代码放入软件包的构建目录中（提取压缩包，复制源代码等）   |
| `patch`           | 应用补丁（如果有）                                           |
| `configure`       | 运行configure命令（如果有）                                  |
| `build`           | 运行编译命令                                                 |
| `install-staging` | ***\*目标软件包：\****如有必要，请在登台目录中运行***\*软件包\****的安装 |
| `install-target`  | ***\*目标软件包：\****如有必要，在目标目录中运行***\*软件包\****的安装 |
| `install`         | ***\*目标软件包：\****运行前面的2个安装命令***\*主机软件包：\****在主机目录中运行***\*软件包\****的安装 |

此外，还有一些其他有用的make目标：

| 命令/目标                 | 描述                                                         |
| ------------------------- | ------------------------------------------------------------ |
| `show-depends`            | 显示构建程序包所需的一阶依赖关系                             |
| `show-recursive-depends`  | 递归显示构建程序包所需的依赖项                               |
| `show-rdepends`           | 显示包的一阶反向依赖关系（即直接依赖于它的包）               |
| `show-recursive-rdepends` | 递归显示包的反向依赖关系（即直接或间接依赖于它的包）         |
| `graph-depends`           | 在当前Buildroot配置的上下文中，生成程序包的依赖关系图。有关依赖关系图的更多详细信息[，](http://nightly.buildroot.org/manual.html#graph-depends)请参见 [本节](http://nightly.buildroot.org/manual.html#graph-depends) [第8.8节“](http://nightly.buildroot.org/manual.html#graph-depends)绘制[软件包之间的依赖关系”](http://nightly.buildroot.org/manual.html#graph-depends)。 |
| `graph-rdepends`          | 生成此程序包反向依赖关系的图形（即直接或间接依赖于它的程序包） |
| `dirclean`                | 删除整个软件包构建目录                                       |
| `reinstall`               | 重新运行安装命令                                             |
| `rebuild`                 | 重新运行编译命令-仅在使用`OVERRIDE_SRCDIR`功能或直接在构建目录中修改文件时才有意义 |
| `reconfigure`             | 重新运行configure命令，然后重新`OVERRIDE_SRCDIR`构建-仅在使用功能或直接在构建目录中修改文件时才有意义 |

### 8.13.6。在开发过程中使用Buildroot

Buildroot的正常操作是下载一个tarball，解压缩，配置，编译和安装该tarball中的软件组件。源代码在`output/build/<package>-<version>`的临时目录中提取 ：无论何时`make clean`使用，该目录都会被完全删除，并在下一次`make`调用时重新创建。即使将Git或Subversion存储库用作包源代码的输入，Buildroot也会从中创建一个tarball，然后像对待tarball一样正常工作。

当Buildroot主要用作集成工具来**构建和集成嵌入式Linux系统的所有组件时**，此行为非常适合。但是，如果在系统的某些组件的开发过程中使用Buildroot，则此行为不是很方便：例如希望对一个软件包的源代码进行少量更改，并能够使用Buildroot快速重建系统。 

在  ` output/build/<package>-<version>`直接进行更改源文件是不合适的，因为`make clean`会删除该目录。

因此，Buildroot针对此用例提供了一种特定的机制：该  <pkg>_OVERRIDE_SRCDIR 机制。Buildroot读取一个*覆盖* 文件，该文件允许用户告诉Buildroot某些软件包的源码位置。

覆盖文件的默认位置是`$(CONFIG_DIR)/local.mk`，由`BR2_PACKAGE_OVERRIDE_FILE`配置选项定义。 `$(CONFIG_DIR)`是Buildroot 的`.config`文件的位置，因此 `local.mk`默认情况下与该`.config`文件并存，这意味着：

- 在树内构建的顶级Buildroot源目录中（  i.e., when `O=` is not used ）
- 在树外构建目录中（ ， i.e., when `O=` is used ）

如果需要的位置不同于这些默认值，则可以通过`BR2_PACKAGE_OVERRIDE_FILE`配置选项指定它。

在此覆盖的文件中，Buildroot希望找到以下形式的行：

```
<pkg1> _OVERRIDE_SRCDIR = / path / to / pkg1 / sources
<pkg2> _OVERRIDE_SRCDIR = / path / to / pkg2 / sources
```

例如：

```
LINUX_OVERRIDE_SRCDIR = / home / bob / linux /
BUSYBOX_OVERRIDE_SRCDIR = / home / bob / busybox /
```

当Buildroot发现给定软件包的 `_OVERRIDE_SRCDIR`定义时，它将不再尝试下载，提取和修补软件包。相反，它将直接使用指定目录中可用的源代码，并且`make clean` 不会涉及该目录。这允许将Buildroot指向您自己的目录，该目录可以由Git，Subversion或任何其他版本控制系统进行管理。为此，Buildroot将使用*rsync*将组件的源代码从指定的复制`_OVERRIDE_SRCDIR`到`output/build/-custom/`。

此机制最好与`make -rebuild`和`make -reconfigure`目标结合使用。甲`make -rebuild all`序列将*rsync的*从源代码 `_OVERRIDE_SRCDIR`到`output/build/-custom`（到感谢 *rsync的*，仅修改过的文件被复制），并重新启动只是这个包的构建过程。

在上述`linux`软件包的示例中，开发人员可以随后更改源代码`/home/bob/linux`，然后运行：

```
make linux-rebuild all
```

并在几秒钟内获得更新的Linux内核映像 `output/images`。同样，可以在`/home/bob/busybox`和之后对BusyBox源代码进行更改：

```
make busybox-rebuild all
```

根文件系统映像中`output/images`包含更新的BusyBox。

大型项目的源代码树通常包含成百上千的文件，这些文件对于构建是不需要的，但是会减慢使用*rsync*复制源代码的过程。（可选）可以定义`_OVERRIDE_SRCDIR_RSYNC_EXCLUSIONS`为跳过源树中的某些文件。例如，在处理`webkitgtk` 软件包时，以下内容将从本地WebKit源树中排除测试和树内构建：

```
WEBKITGTK_OVERRIDE_SRCDIR = /home/bob/WebKit
WEBKITGTK_OVERRIDE_SRCDIR_RSYNC_EXCLUSIONS = \
        --exclude JSTests --exclude ManualTests --exclude PerformanceTests \
        --exclude WebDriverTests --exclude WebKitBuild --exclude WebKitLibraries \
        --exclude WebKit.xcworkspace --exclude Websites --exclude Examples
```

默认情况下，Buildroot跳过VCS工件（例如***\*.git\****和 ***\*.svn\****目录）的同步。一些软件包更喜欢在构建过程中提供这些VCS目录，例如，用于自动确定版本信息的精确提交参考。要以较慢的速度撤消此内置过滤，请重新添加以下目录：

```
LINUX_OVERRIDE_SRCDIR_RSYNC_EXCLUSIONS = --include .git
```

### 8.13.6。在开发过程中使用Buildroot（不方便是怎么解决的）

Buildroot的正常操作是下载一个tarball，解压缩，配置，编译和安装该tarball中的软件组件。源代码在`output/build/-`的临时目录中提取 ：无论何时`make clean`使用，该目录都会被完全删除，并在下一次`make`调用时重新创建。即使将Git或Subversion存储库用作包源代码的输入，Buildroot也会从中创建一个tarball，然后像对待tarball一样正常工作。

当Buildroot主要用作集成工具来构建和集成嵌入式Linux系统的所有组件时，此行为非常适合。但是，如果在系统的某些组件的开发过程中使用Buildroot，则此行为不是很方便：而是希望对一个软件包的源代码进行少量更改，并能够使用Buildroot快速重建系统。 。

直接进行更改`output/build/-`不是合适的解决方案，因为该目录已在上删除`make clean`。

**因此，Buildroot针对此用例提供了一种特定的机制**：该`_OVERRIDE_SRCDIR`机制。Buildroot读取一个*覆盖* 文件，该文件允许用户告诉Buildroot某些软件包的源位置。

覆盖文件的默认位置是`$(CONFIG_DIR)/local.mk`，由`BR2_PACKAGE_OVERRIDE_FILE`配置选项定义。 `$(CONFIG_DIR)`是Buildroot `.config`文件的位置，因此 `local.mk`默认情况下与该`.config`文件并存，这意味着：

- 在树内构建的顶级Buildroot源目录中（即，`O=`不使用when时）
- 在树外构建目录中（例如，何时 `O=`使用）

如果需要的位置不同于这些默认值，则可以通过`BR2_PACKAGE_OVERRIDE_FILE`配置选项指定它。

在此*替代*文件中，Buildroot希望找到以下形式的行：

```
<pkg1> _OVERRIDE_SRCDIR = / path / to / pkg1 / sources
<pkg2> _OVERRIDE_SRCDIR = / path / to / pkg2 / sources
```

例如：

```
LINUX_OVERRIDE_SRCDIR = / home / bob / linux /
BUSYBOX_OVERRIDE_SRCDIR = / home / bob / busybox /
```

当Buildroot发现给定软件包的 `_OVERRIDE_SRCDIR`定义时，它将不再尝试下载，提取和修补软件包。相反，它将直接使用指定目录中可用的源代码，并且`make clean` 不会涉及该目录。这允许将Buildroot指向您自己的目录，该目录可以由Git，Subversion或任何其他版本控制系统进行管理。为此，Buildroot将使用*rsync*将组件的源代码从指定的复制`_OVERRIDE_SRCDIR`到`output/build/-custom/`。

此机制最好与`make <pkg>-rebuild`和` make <pkg>-reconfigure `目标结合使用。甲`make <pkg>-rebuild all `序列将*rsync的*从源代码 ` <pkg>_OVERRIDE_SRCDIR`到`output/build/-custom`（到感谢 *rsync的*，仅修改过的文件被复制），并重新启动只是这个包的构建过程。

在上述`linux`软件包的示例中，开发人员可以随后更改源代码`/home/bob/linux`，然后运行：

```
make linux-rebuild all
```

并在几秒钟内获得更新的Linux内核映像 `output/images`。同样，可以在`/home/bob/busybox`和之后对BusyBox源代码进行更改：

```
make busybox-rebuild all
```

根文件系统映像中`output/images`包含更新的BusyBox。

大型项目的源代码树通常包含成百上千的文件，这些文件对于构建是不需要的，但是会减慢使用*rsync*复制源代码的过程。（可选）可以定义`_OVERRIDE_SRCDIR_RSYNC_EXCLUSIONS`为跳过源树中的某些文件。例如，在处理`webkitgtk` 软件包时，以下内容将从本地WebKit源树中排除测试和树内构建：

```
WEBKITGTK_OVERRIDE_SRCDIR = /home/bob/WebKit
WEBKITGTK_OVERRIDE_SRCDIR_RSYNC_EXCLUSIONS = \
        --exclude JSTests --exclude ManualTests --exclude PerformanceTests \
        --exclude WebDriverTests --exclude WebKitBuild --exclude WebKitLibraries \
        --exclude WebKit.xcworkspace --exclude Websites --exclude Examples
```

默认情况下，Buildroot跳过VCS工件（例如***\*.git\****和 ***\*.svn\****目录）的同步。一些软件包更喜欢在构建过程中提供这些VCS目录，例如，用于自动确定版本信息的精确提交参考。要以较慢的速度撤消此内置过滤，请重新添加以下目录：

```
LINUX_OVERRIDE_SRCDIR_RSYNC_EXCLUSIONS = --include .git
```

## 第9章特定于项目的定制

对于给定的项目，您可能需要执行的典型操作是：

- 配置Buildroot（包括构建选项和工具链，引导程序，内核，软件包和文件系统映像类型选择）
- 配置其他组件，例如Linux内核和BusyBox
- 定制生成的目标文件系统
  - 在目标文件系统上添加或覆盖文件（使用 `BR2_ROOTFS_OVERLAY`）
  - 修改或删除目标文件系统上的文件（使用 `BR2_ROOTFS_POST_BUILD_SCRIPT`）
  - 在生成文件系统映像之前运行任意命令（使用`BR2_ROOTFS_POST_BUILD_SCRIPT`）
  - 设置文件的权限和所有权（使用 `BR2_ROOTFS_DEVICE_TABLE`）
  - 添加自定义设备节点（使用 `BR2_ROOTFS_STATIC_DEVICE_TABLE`）
- 添加自定义用户帐户（使用`BR2_ROOTFS_USERS_TABLES`）
- 在生成文件系统映像后运行任意命令（使用`BR2_ROOTFS_POST_IMAGE_SCRIPT`）
- 将某些项目的特定补丁添加到某些软件包中（使用 `BR2_GLOBAL_PATCH_DIR`）
- 添加特定于项目的软件包

有关此类*特定于项目的*自定义项的重要说明：请仔细考虑哪些更改确实是特定于项目的，哪些更改对项目外的开发人员也有用。Buildroot社区强烈建议并鼓励上游改进，软件包和董事会对Buildroot官方项目的支持。当然，由于更改是高度特定或专有的，因此有时上游不可能或不希望这样做。

本章介绍了如何在Buildroot中进行此类特定于项目的自定义，以及如何以一种可重现的方式构建同一映像的方式存储它们，即使在运行*make clean之后也是如此*。通过遵循推荐的策略，您甚至可以使用同一Buildroot树来构建多个不同的项目！

## 9.1。推荐目录结构

为你的项目自定义Buildroot时，您将创建一个或多个需要存储在某处的特定于项目的文件。虽然大多数这些文件可以放置在*任何*位置，因为它们的路径将在Buildroot配置中指定，但是Buildroot开发人员建议使用本节中描述的特定目录结构。

与该目录结构正交，您可以选择将其本身放置*在何处*：在Buildroot树中还是在其外部（使用br2-external树）。这两个选项均有效，选择取决于您。

```
+-- board/
|   +-- <company>/
|       +-- <boardname>/
|           +-- linux.config
|           +-- busybox.config
|           +-- <other configuration files>
|           +-- post_build.sh
|           +-- post_image.sh
|           +-- rootfs_overlay/
|           |   +-- etc/
|           |   +-- <some file>
|           +-- patches/
|               +-- foo/
|               |   +-- <some patch>
|               +-- libbar/
|                   +-- <some other patches>
|
+-- configs/
|   +-- <boardname>_defconfig
|
+-- package/
|   +-- <company>/
|       +-- Config.in (if not using a br2-external tree)
|       +-- <company>.mk (if not using a br2-external tree)
|       +-- package1/
|       |    +-- Config.in
|       |    +-- package1.mk
|       +-- package2/
|           +-- Config.in
|           +-- package2.mk
|
+-- Config.in (if using a br2-external tree)
+-- external.mk (if using a br2-external tree)
+-- external.desc (if using a br2-external tree)
```

本章将进一步提供上面显示的文件的详细信息。

注意：如果您选择将此结构放置在Buildroot树之外但在br2-external树中，则<company>以及可能的<boardname>组件可能是多余的，可以省略。

## 9.2。将定制保留在Buildroot之外

如[第9.1节“推荐的目录结构”所述](http://nightly.buildroot.org/manual.html#customize-dir-structure)，您可以在两个位置放置特定于项目的自定义项：

- 直接在Buildroot树中，通常使用版本控制系统中的分支来维护它们，以便轻松升级到较新的Buildroot版本。
- 使用*br2-external*机制在*Buildroot*树之外。这种机制允许将软件包配方，电路板支持和配置文件保留在Buildroot树之外，同时仍将它们很好地集成到构建逻辑中。我们称此位置为*br2-外部树*。本节说明如何使用br2外部机制以及在br2外部树中提供的内容。

通过将`BR2_EXTERNAL`make变量设置为要使用的br2外部树的路径，可以告诉Buildroot使用一棵或多棵br2外部树。可以将其传递给任何Buildroot `make`调用。它会自动保存在`.br2-external.mk`输出目录中的隐藏文件中。因此，无需`BR2_EXTERNAL`每次`make`调用都通过。但是，可以随时通过传递新值来更改它，也可以通过传递空值来删除它。

**注意。** br2外部树的路径可以是绝对路径，也可以是相对路径。如果将它作为相对路径传递，则必须注意，它是相对于主Buildroot源目录***\*而不是\****相对于Buildroot输出目录进行解释的。

**注意：** 如果使用Buildroot 2016.11之前的br2-external树，则需要先对其进行转换，然后才能将其用于Buildroot 2016.11及更高版本。有关这样做的帮助[，](http://nightly.buildroot.org/manual.html#br2-external-converting)请参见 [第25.1节“迁移到2016.11”](http://nightly.buildroot.org/manual.html#br2-external-converting)。

一些例子：

```
buildroot/ $ make BR2_EXTERNAL=/path/to/foo menuconfig
```

从现在开始，`/path/to/foo`将使用br2-external树中的定义：

```
buildroot/ $ make
buildroot/ $ make legal-info
```

我们可以随时切换到另一棵br2-external树：

```
buildroot/ $ make BR2_EXTERNAL=/where/we/have/bar xconfig
```

我们还可以使用多个br2外部树：

```
buildroot/ $ make BR2_EXTERNAL=/path/to/foo:/where/we/have/bar menuconfig
```

或禁用任何br2-external树的用法：

```
buildroot/ $ make BR2_EXTERNAL= xconfig
```

### 9.2.1。Layout of a br2-external tree

 A br2-external tree  必须至少包含这三个文件，以下各章对此进行了介绍：

- `external.desc`
- `external.mk`
- `Config.in`

除了那些强制性文件之外，br2外部树中可能还存在其他内容和可选内容，例如`configs/` 或`provides/`目录。在以下各章中还将对它们进行描述。

稍后还将介绍完整的 A br2-external tree  布局示例。

#### 该`external.desc`文件

该文件描述了br2外部树：该br2外部树的*名称*和*描述* 。

该文件的格式基于行，每行以关键字开头，后跟冒号和一个或多个空格，后跟分配给该关键字的值。当前识别出两个关键字：

- `name`（必填）定义该br2外部树的名称。该名称只能在集合中使用ASCII字符`[A-Za-z0-9_]`；禁止使用其他任何字符。Buildroot将变量设置为`BR2_EXTERNAL_$(NAME)_PATH`br2外部树的绝对路径，以便您可以使用它来引用br2外部树。该变量在Kconfig中都可用，因此您可以使用它来为Kconfig文件（请参见下文）和Makefile提供源，以便可以使用它来包含其他Makefile（请参见下文）或引用其他文件（如数据文件） ）从您的br2外部树中。

  **注意：** 由于可以一次使用多个br2外部树，因此Buildroot使用此名称为每个树生成变量。该名称用于标识您的br2外部树，因此请尝试提供一个真正描述您的br2外部树的名称，以使其相对唯一，以免与其他br2的其他名称冲突。 -外部树，尤其是当您计划以某种方式与第三方共享br2外部树或使用第三方的br2外部树时。

- `desc`（可选）提供该br2外部树的简短描述。它应该放在一行上，并且大部分是自由格式的（请参见下文），并且在显示有关br2-外部树的信息时使用（例如，在defconfig文件列表上方，或作为menuconfig中的提示）；因此，它应该相对简短（40个字符可能是一个很好的上限）。该描述在`BR2_EXTERNAL_$(NAME)_DESC` 变量中可用。

名称和相应`BR2_EXTERNAL_$(NAME)_PATH` 变量的示例：

- `FOO` → `BR2_EXTERNAL_FOO_PATH`
- `BAR_42` → `BR2_EXTERNAL_BAR_42_PATH`

在以下示例中，假定名称设置为`BAR_42`。

**注：** 这两个`BR2_EXTERNAL_$(NAME)_PATH`和`BR2_EXTERNAL_$(NAME)_DESC`是在的Kconfig文件和Makefile文件可用。它们也可以在环境中导出，因此可以在构建后，图像后和fakeroot脚本中使用。

#### 在`Config.in`和`external.mk`文件

这些文件（其可以各自是空的）可以被用于定义包的配方（即，`foo/Config.in`与`foo/foo.mk`像在Buildroot里面本身捆绑包）或其他定制的配置选项或化妆逻辑。

Buildroot自动包含`Config.in`来自每个br2外部树的，以使其出现在顶层配置菜单中，并且包括`external.mk`来自每个br2外部树的，以及其余的makefile逻辑。

此方法的主要用途是存储包装配方。推荐的方法是编写一个如下所示的`Config.in`文件：

```
source "$BR2_EXTERNAL_BAR_42_PATH/package/package1/Config.in"
source "$BR2_EXTERNAL_BAR_42_PATH/package/package2/Config.in"
```

然后，拥有一个如下所示的`external.mk`文件：

```
include $(sort $(wildcard $(BR2_EXTERNAL_BAR_42_PATH)/package/*/*.mk))
```

然后`$(BR2_EXTERNAL_BAR_42_PATH)/package/package1`和`$(BR2_EXTERNAL_BAR_42_PATH)/package/package2`创建普通Buildroot里面包的食谱，在解释的[第17章，*添加新的包Buildroot里面*](http://nightly.buildroot.org/manual.html#adding-packages)。如果愿意，还可以将软件包分组在名为<boardname>的子目录中，并相应地调整上述路径。

您还可以在中定义自定义配置选项，`Config.in`并在中定义自定义生成逻辑`external.mk`。

#### 该`configs/`目录

可以将Buildroot defconfigs存储`configs`在br2-external树的子目录中。Buildroot将在它们的输出中自动显示它们，`make list-defconfigs`并允许使用常规`make _defconfig`命令加载它们。它们将在*make list-defconfigs*输出中可见，在 `External configs`包含其定义的br2-external树名称的标签下。

**注意：** 如果多个br2外部树中存在defconfig文件，则使用最后一个br2外部树中的一个。因此，可以覆盖Buildroot或另一个br2-external树中捆绑的defconfig。

#### 该`provides/`目录

对于某些软件包，Buildroot提供了两个（或更多）与API兼容的此类软件包的实现方式的选择。例如，可以选择libjpeg或jpeg-turbo，也可以选择openssl或libressl。最后，可以选择一种已知的，预配置的工具链。

br2-external可以通过提供一组定义这些选择的文件来扩展这些选择：

- `provides/toolchains.in` 定义预配置的工具链，然后将其列在工具链选择中；
- `provides/jpeg.in` 定义替代的libjpeg实现；
- `provides/openssl.in` 定义替代的openssl实现。

#### 自由格式的内容

可以在其中存储所有主板特定的配置文件，例如内核配置，根文件系统覆盖图或Buildroot允许为其设置位置的其他任何配置文件（通过使用`BR2_EXTERNAL_$(NAME)_PATH`变量）。例如，您可以按以下方式将路径设置为全局补丁目录，rootfs覆盖图和内核配置文件的路径（例如，通过运行 `make menuconfig`并填写以下选项）：

```
BR2_GLOBAL_PATCH_DIR = $（BR2_EXTERNAL_BAR_42_PATH）/补丁/
BR2_ROOTFS_OVERLAY = $（BR2_EXTERNAL_BAR_42_PATH）/ board / <boardname> / overlay /
BR2_LINUX_KERNEL_CUSTOM_CONFIG_FILE = $（BR2_EXTERNAL_BAR_42_PATH）/ board / <板名> /kernel.config
```

#### 其他Linux内核扩展

通过将其他Linux内核扩展存储在br2-external树的根目录中，可以添加其他Linux内核扩展（请参见[第17.20.2节“ linux-kernel-extensions”](http://nightly.buildroot.org/manual.html#linux-kernel-ext)）`linux/`。

#### 示例布局

这是一个使用br2-external的所有功能的布局示例（示例内容显示在其上方的文件中，当与br2外部树的解释相关时；所有这些都完全是出于说明目的而组成的）。课程）：

```
 /path/to/br2-ext-tree/
  |- external.desc
  |     |name: BAR_42
  |     |desc: Example br2-external tree
  |     `----
  |
  |- Config.in
  |     |source "$BR2_EXTERNAL_BAR_42_PATH/toolchain/toolchain-external-mine/Config.in.options"
  |     |source "$BR2_EXTERNAL_BAR_42_PATH/package/pkg-1/Config.in"
  |     |source "$BR2_EXTERNAL_BAR_42_PATH/package/pkg-2/Config.in"
  |     |source "$BR2_EXTERNAL_BAR_42_PATH/package/my-jpeg/Config.in"
  |     |
  |     |config BAR_42_FLASH_ADDR
  |     |    hex "my-board flash address"
  |     |    default 0x10AD
  |     `----
  |
  |- external.mk
  |     |include $(sort $(wildcard $(BR2_EXTERNAL_BAR_42_PATH)/package/*/*.mk))
  |     |include $(sort $(wildcard $(BR2_EXTERNAL_BAR_42_PATH)/toolchain/*/*.mk))
  |     |
  |     |flash-my-board:
  |     |    $(BR2_EXTERNAL_BAR_42_PATH)/board/my-board/flash-image \
  |     |        --image $(BINARIES_DIR)/image.bin \
  |     |        --address $(BAR_42_FLASH_ADDR)
  |     `----
  |
  |- package/pkg-1/Config.in
  |     |config BR2_PACKAGE_PKG_1
  |     |    bool "pkg-1"
  |     |    help
  |     |      Some help about pkg-1
  |     `----
  |- package/pkg-1/pkg-1.hash
  |- package/pkg-1/pkg-1.mk
  |     |PKG_1_VERSION = 1.2.3
  |     |PKG_1_SITE = /some/where/to/get/pkg-1
  |     |PKG_1_LICENSE = blabla
  |     |
  |     |define PKG_1_INSTALL_INIT_SYSV
  |     |    $(INSTALL) -D -m 0755 $(PKG_1_PKGDIR)/S99my-daemon \
  |     |                          $(TARGET_DIR)/etc/init.d/S99my-daemon
  |     |endef
  |     |
  |     |$(eval $(autotools-package))
  |     `----
  |- package/pkg-1/S99my-daemon
  |
  |- package/pkg-2/Config.in
  |- package/pkg-2/pkg-2.hash
  |- package/pkg-2/pkg-2.mk
  |
  |- provides/jpeg.in
  |     |config BR2_PACKAGE_MY_JPEG
  |     |    bool "my-jpeg"
  |     `----
  |- package/my-jpeg/Config.in
  |     |config BR2_PACKAGE_PROVIDES_JPEG
  |     |    default "my-jpeg" if BR2_PACKAGE_MY_JPEG
  |     `----
  |- package/my-jpeg/my-jpeg.mk
  |     |# This is a normal package .mk file
  |     |MY_JPEG_VERSION = 1.2.3
  |     |MY_JPEG_SITE = https://example.net/some/place
  |     |MY_JPEG_PROVIDES = jpeg
  |     |$(eval $(autotools-package))
  |     `----
  |
  |- provides/toolchains.in
  |     |config BR2_TOOLCHAIN_EXTERNAL_MINE
  |     |    bool "my custom toolchain"
  |     |    depends on BR2_some_arch
  |     |    select BR2_INSTALL_LIBSTDCPP
  |     `----
  |- toolchain/toolchain-external-mine/Config.in.options
  |     |if BR2_TOOLCHAIN_EXTERNAL_MINE
  |     |config BR2_TOOLCHAIN_EXTERNAL_PREFIX
  |     |    default "arch-mine-linux-gnu"
  |     |config BR2_PACKAGE_PROVIDES_TOOLCHAIN_EXTERNAL
  |     |    default "toolchain-external-mine"
  |     |endif
  |     `----
  |- toolchain/toolchain-external-mine/toolchain-external-mine.mk
  |     |TOOLCHAIN_EXTERNAL_MINE_SITE = https://example.net/some/place
  |     |TOOLCHAIN_EXTERNAL_MINE_SOURCE = my-toolchain.tar.gz
  |     |$(eval $(toolchain-external-package))
  |     `----
  |
  |- linux/Config.ext.in
  |     |config BR2_LINUX_KERNEL_EXT_EXAMPLE_DRIVER
  |     |    bool "example-external-driver"
  |     |    help
  |     |      Example external driver
  |     |---
  |- linux/linux-ext-example-driver.mk
  |
  |- configs/my-board_defconfig
  |     |BR2_GLOBAL_PATCH_DIR="$(BR2_EXTERNAL_BAR_42_PATH)/patches/"
  |     |BR2_ROOTFS_OVERLAY="$(BR2_EXTERNAL_BAR_42_PATH)/board/my-board/overlay/"
  |     |BR2_ROOTFS_POST_IMAGE_SCRIPT="$(BR2_EXTERNAL_BAR_42_PATH)/board/my-board/post-image.sh"
  |     |BR2_LINUX_KERNEL_CUSTOM_CONFIG_FILE="$(BR2_EXTERNAL_BAR_42_PATH)/board/my-board/kernel.config"
  |     `----
  |
  |- patches/linux/0001-some-change.patch
  |- patches/linux/0002-some-other-change.patch
  |- patches/busybox/0001-fix-something.patch
  |
  |- board/my-board/kernel.config
  |- board/my-board/overlay/var/www/index.html
  |- board/my-board/overlay/var/www/my.css
  |- board/my-board/flash-image
  `- board/my-board/post-image.sh
        |#!/bin/sh
        |generate-my-binary-image \
        |    --root ${BINARIES_DIR}/rootfs.tar \
        |    --kernel ${BINARIES_DIR}/zImage \
        |    --dtb ${BINARIES_DIR}/my-board.dtb \
        |    --output ${BINARIES_DIR}/image.bin
        `----
```

br2-external树然后将在menuconfig中可见（布局已展开）：

```
External options  --->
    *** Example br2-external tree (in /path/to/br2-ext-tree/)
    [ ] pkg-1
    [ ] pkg-2
    (0x10AD) my-board flash address
```

如果您使用了不止一棵br2-external树，则它看起来像（展开了布局，第二棵树的名称是，`FOO_27`但没有 `desc:`字段`external.desc`）：

```
External options  --->
    Example br2-external tree  --->
        *** Example br2-external tree (in /path/to/br2-ext-tree)
        [ ] pkg-1
        [ ] pkg-2
        (0x10AD) my-board flash address
    FOO_27  --->
        *** FOO_27 (in /path/to/another-br2-ext)
        [ ] foo
        [ ] bar
```

此外，jpeg提供程序将在jpeg选择中可见：

```
Target packages  --->
    Libraries  --->
        Graphics  --->
            [*] jpeg support
                jpeg variant ()  --->
                    ( ) jpeg
                    ( ) jpeg-turbo
                        *** jpeg from: Example br2-external tree ***
                    (X) my-jpeg
                        *** jpeg from: FOO_27 ***
                    ( ) another-jpeg
```

对于工具链也是如此：

```
Toolchain  --->
    Toolchain ()  --->
        ( ) Custom toolchain
            *** Toolchains from: Example br2-external tree ***
        (X) my custom toolchain
```

**注意。** 中的工具链选项`toolchain/toolchain-external-mine/Config.in.options` 不会出现在`Toolchain`菜单中。必须在br2-external的顶层中将它们明确包含，`Config.in`并因此出现在`External options`菜单中。

## 9.3。存储Buildroot配置

可以使用以下命令存储Buildroot配置 `make savedefconfig`。

通过删除默认值的配置选项，可以简化Buildroot配置。结果存储在名为**的文件`defconfig`。**

如果要将其保存在其他位置，请更改`BR2_DEFCONFIG`Buildroot配置本身中的 选项，或使用调用

```
make savedefconfig BR2_DEFCONFIG=<path-to-defconfig>.
```

推荐的存储defconfig的位置是 `configs/<boardname>_defconfig`。如果遵循此建议，配置将列在`make help`命令结果中显示，并可以通过运行再次进行设置` make <boardname>_defconfig`。

或者，您可以将文件复制到任何其他位置并使用进行重建 

```
make defconfig BR2_DEFCONFIG=<path-to-defconfig-file>.
```



## 9.4。存储其他组件的配置

BusyBox，Linux内核，Barebox，U-Boot和uClibc的配置文件存储位置应该同样便于改变。对于每个组件，都存在一个Buildroot配置选项以指向输入配置文件，例如`BR2_LINUX_KERNEL_CUSTOM_CONFIG_FILE`。要存储它们的配置，请将这些配置选项设置为要保存配置文件的路径，然后使用下面描述的帮助程序目标来实际存储配置。

如[第9.1节“推荐的目录结构”](http://nightly.buildroot.org/manual.html#customize-dir-structure)中所述，存储这些配置文件的推荐路径为`board///foo.config`。

确保*在*更改`BR2_LINUX_KERNEL_CUSTOM_CONFIG_FILE`等选项*之前*创建配置文件。否则，Buildroot将尝试访问该配置文件，该配置文件尚不存在，并且将失败。您可以通过运行`make linux-menuconfig`etc 创建配置文件 。

Buildroot提供了一些帮助程序目标，以简化配置文件的保存。

- `make linux-update-defconfig`将linux配置保存到指定的路径`BR2_LINUX_KERNEL_CUSTOM_CONFIG_FILE`。它通过删除默认值来简化配置文件。但是，这仅适用于从2.6.33开始的内核。对于较早的内核，请使用`make linux-update-config`。
- `make busybox-update-config`将busybox配置保存到指定的路径`BR2_PACKAGE_BUSYBOX_CONFIG`。
- `make uclibc-update-config`将uClibc配置保存到所指定的路径`BR2_UCLIBC_CONFIG`。
- `make barebox-update-defconfig`将裸箱配置保存到所指定的路径`BR2_TARGET_BAREBOX_CUSTOM_CONFIG_FILE`。
- `make uboot-update-defconfig`将U-Boot配置保存到所指定的路径`BR2_TARGET_UBOOT_CUSTOM_CONFIG_FILE`。
- 对于at91bootstrap3，不存在任何帮助程序，因此您必须将配置文件手动复制到`BR2_TARGET_AT91BOOTSTRAP3_CUSTOM_CONFIG_FILE`。

## 9.5。自定义生成的目标文件系统

除了通过更改配置外`make *config`，还有其他几种方法可以自定义生成的目标文件系统。

可以共存的两种推荐方法是根文件系统覆盖  root filesystem overlay(s) 和编译后脚本  post build script(s). 。

- 根文件系统覆盖（`BR2_ROOTFS_OVERLAY`）

  a  filesystem overlay 是一棵文件树，在编译后，直接复制到目标文件系统  target filesystem 上。要启用此功能，请将配置选项`BR2_ROOTFS_OVERLAY`（在`System configuration`菜单中） to the root of the overlay 。您甚至可以指定多个overlay。你能指定相对路径，这个路径是相对于Buildroot树根。版本控制系统，就像隐藏目录`.git`，`.svn`，`.hg`等，文件名为`.empty`和文件中以`~`结束，被排除在复制。

  

  当`BR2_ROOTFS_MERGED_USR`被启用，则overlay不包括/ bin，/ lib目录或/ sbin目录的目录，如Buildroot在/ usr里面将创建为符号链接在相关文件夹中的。在这种情况下，如果叠加层具有任何程序或库，则应将它们放在/ usr / bin，/ usr / sbin和/ usr / lib中。

  如[第9.1节“推荐的目录结构”所示](http://nightly.buildroot.org/manual.html#customize-dir-structure)，此覆盖的推荐路径为`board/<company>/<boardname>/rootfs-overlay`。

- 编译后脚本（`BR2_ROOTFS_POST_BUILD_SCRIPT`）

  生成后脚本是*在* Buildroot生成所有选定软件之后但*在*组装rootfs映像*之前*调用的shell脚本。要启用此功能，请在config选项`BR2_ROOTFS_POST_BUILD_SCRIPT`（在`System configuration`菜单中）中指定以空格分隔的后生成脚本列表。如果指定相对路径，则它将相对于Buildroot树的根。使用构建后脚本，可以删除或修改目标文件系统中的任何文件。但是，您应谨慎使用此功能。每当发现某个程序包生成错误或不需要的文件时，都应修复该程序包，而不要使用某些生成后的清理脚本来解决它。如[第9.1节“推荐的目录结构”所示](http://nightly.buildroot.org/manual.html#customize-dir-structure)，此脚本的推荐路径为`board///post_build.sh`。构建后脚本以Buildroot主树作为当前工作目录运行。目标文件系统的路径作为第一个参数传递给每个脚本。如果config选项 `BR2_ROOTFS_POST_SCRIPT_ARGS`不为空，则这些参数也将传递给脚本。所有脚本都将传递完全相同的参数集，不可能将不同的参数集传递给每个脚本。此外，您还可以使用以下环境变量：`BR2_CONFIG`：Buildroot .config文件的路径`HOST_DIR`，`STAGING_DIR`，`TARGET_DIR`：见 [第17.5.2，“ `generic-package`参考”](http://nightly.buildroot.org/manual.html#generic-package-reference)`BUILD_DIR`：提取和构建软件包的目录`BINARIES_DIR`：所有二进制文件（也称为图像）的存储位置`BASE_DIR`：基本输出目录

下面介绍了三种自定义目标文件系统的方法，但不建议使用。

- 直接修改目标文件系统

  对于临时修改，您可以直接修改目标文件系统并重建映像。可在下找到目标文件系统`output/target/`。进行更改后，请运行`make`以重建目标文件系统映像。此方法允许您对目标文件系统执行任何操作，但是如果您需要使用清理Buildroot树`make clean`，这些更改将丢失。在某些情况下，必须进行此类清洁，有关详细信息，请参见[第8.2节“了解何时需要完全重建”](http://nightly.buildroot.org/manual.html#full-rebuild)。因此，该解决方案仅对快速测试有用：*更改不会在`make clean` 命令中*保留。确认更改后，您应确保`make clean`使用根文件系统覆盖或构建后脚本在以后保留它们。

- 自定义目标框架（`BR2_ROOTFS_SKELETON_CUSTOM`）

  根文件系统映像是从目标框架创建的，所有软件包都在其上面安装文件。在`output/target`构建和安装任何软件包之前，将框架复制到目标目录。默认的目标框架提供了标准的Unix文件系统布局以及一些基本的初始化脚本和配置文件。如果默认框架（位于下方`system/skeleton`）不符合您的需求，则通常会使用根文件系统覆盖或构建后脚本来适应它。但是，如果默认框架与您所需的框架完全不同，则使用自定义框架可能更合适。要启用此功能，请启用config选项 `BR2_ROOTFS_SKELETON_CUSTOM`并设置`BR2_ROOTFS_SKELETON_CUSTOM_PATH` 为自定义框架的路径。`System configuration`菜单中都提供了这两个选项 。如果指定相对路径，则它将相对于Buildroot树的根。自定义框架不需要包含*/ bin*，*/ lib*或*/ sbin* 目录，因为它们是在构建过程中自动创建的。当`BR2_ROOTFS_MERGED_USR`启用，然后将自定义骨架不能包含*/ bin中*，*/ lib目录*或*/ sbin目录*的目录，如Buildroot里面将创建为符号链接在相关文件夹中*的/ usr*。在这种情况下，如果框架具有任何程序或库，则应将它们放在*/ usr / bin*，*/ usr / sbin*和 */ usr / lib中*。不建议使用此方法，因为它会复制整个框架，这会阻止利用Buildroot更高版本中对默认框架带来的修复或改进。

- fakeroot脚本（`BR2_ROOTFS_POST_FAKEROOT_SCRIPT`）

  汇总最终映像时，过程的某些部分需要root权限：在中创建设备节点`/dev`，设置文件或目录的权限或所有权...为了避免需要实际的root权限，Buildroot会使用`fakeroot`模拟root权限。这并不能完全代替实际成为root用户，但足以满足Buildroot的需求。假根目录后脚本是外壳脚本，它们在fakeroot阶段*结束*时即在调用文件系统映像生成器*之前*被调用。因此，它们在fakeroot上下文中被调用。如果需要调整文件系统以执行通常仅对root用户可用的修改，则后fakeroot脚本会很有用。**注意：** 建议使用现有机制来设置文件权限或在中创建条目`/dev`（请参见[第9.5.1节“设置文件许可权和所有权并添加自定义设备节点”](http://nightly.buildroot.org/manual.html#customize-device-permission)）或创建用户（请参见[第9.6节“添加自](http://nightly.buildroot.org/manual.html#customize-users)[定义设备”](http://nightly.buildroot.org/manual.html#customize-device-permission)）[用户帐户”](http://nightly.buildroot.org/manual.html#customize-users)）**注意：生成** 后脚本（上面）和fakeroot脚本之间的区别在于，在fakeroot上下文中不调用生成后脚本。**注意;。** 使用`fakeroot`并不能完全代替实际成为root。 `fakeroot`仅伪造文件访问权限和类型（常规，块或字符设备…）和uid / gid；这些是在内存中模拟的。

### 9.5.1。设置文件权限和所有权并添加自定义设备节点

有时需要在文件或设备节点上设置特定的权限或所有权。例如，某些文件可能需要由root拥有。由于构建后脚本不是以root身份运行的，因此除非您在构建后脚本中使用显式的falseroot，否则您无法从那里进行此类更改。

而是，Buildroot提供对所谓的*权限表的支持*。要使用此功能，请将config选项设置`BR2_ROOTFS_DEVICE_TABLE`为以空格分隔的权限表列表，这些权限表是遵循[makedev语法](http://nightly.buildroot.org/manual.html#makedev-syntax) [第23章，*Makedev语法文档的*](http://nightly.buildroot.org/manual.html#makedev-syntax)常规文本文件。

如果使用的是静态设备表（即不使用`devtmpfs`， `mdev`或`(e)udev`），则可以使用相同的语法在所谓的*设备表中*添加设备节点。要使用此功能，请将config选项设置`BR2_ROOTFS_STATIC_DEVICE_TABLE`为以空格分隔的设备表列表。

如[第9.1节“推荐的目录结构”所示](http://nightly.buildroot.org/manual.html#customize-dir-structure)，此类文件的推荐位置为`board///`。

应该注意的是，如果特定的权限或设备节点与特定的应用程序相关，则应该在包的文件中设置变量`FOO_PERMISSIONS`和变量 （请参见[第17.5.2节“ ](http://nightly.buildroot.org/manual.html#generic-package-reference)[参考”](http://nightly.buildroot.org/manual.html#generic-package-reference)）。`FOO_DEVICES``.mk`[`generic-package`](http://nightly.buildroot.org/manual.html#generic-package-reference)

## 9.6。添加自定义用户帐户

有时需要在目标系统中添加特定用户。为了满足此要求，Buildroot提供了对所谓的 *users表的支持*。要使用此功能，请`BR2_ROOTFS_USERS_TABLES`按照[makeusers语法](http://nightly.buildroot.org/manual.html#makeuser-syntax) [第24章，*Makeusers语法文档*](http://nightly.buildroot.org/manual.html#makeuser-syntax)，将config选项设置 为以空格分隔的用户表列表和常规文本文件。

如[第9.1节“推荐的目录结构”所示](http://nightly.buildroot.org/manual.html#customize-dir-structure)，此类文件的推荐位置为`board///`。

应该注意的是，如果自定义用户与特定的应用程序相关，则应该`FOO_USERS`在程序包的`.mk` 文件中设置变量（请参见[第17.5.2节“ `generic-package`参考”](http://nightly.buildroot.org/manual.html#generic-package-reference)）。

## 9.7。创建图像*后*进行自定义

在构建 文件系统映像，内核和引导加载程序*之前*运行构建后脚本（[第9.5节“自定义生成的目标文件系统”](http://nightly.buildroot.org/manual.html#rootfs-custom)）时，可以在创建所有映像*后*使用***\*映像后脚本\****执行某些特定操作。

例如，映像后脚本可用于在NFS服务器导出的位置中自动提取根文件系统tarball，或创建捆绑根文件系统和内核映像的特殊固件映像，或项目所需的任何其他自定义操作。

要启用此功能，请在config选项`BR2_ROOTFS_POST_IMAGE_SCRIPT`（在`System configuration`菜单中）中指定以空格分隔的后映像脚本列表。如果指定相对路径，则它将相对于Buildroot树的根。

就像生成后脚本一样，运行后映像脚本时会将Buildroot主树作为当前工作目录。`images` 输出目录的路径作为第一个参数传递给每个脚本。如果config选项`BR2_ROOTFS_POST_SCRIPT_ARGS`不为空，则这些参数也将传递给脚本。所有脚本都将传递完全相同的参数集，不可能将不同的参数集传递给每个脚本。

同样，就像在生成后的脚本，这些脚本可以访问的环境变量`BR2_CONFIG`，`HOST_DIR`，`STAGING_DIR`， `TARGET_DIR`，`BUILD_DIR`，`BINARIES_DIR`和`BASE_DIR`。

映像后脚本将以执行Buildroot的用户身份执行，该用户通常*不*应该是root用户。因此，在这些脚本之一中需要root权限的任何操作都需要特殊处理（使用fakeroot或sudo），该处理留给脚本开发人员。

## 9.8。添加特定于项目的补丁

在Buildroot中提供的补丁程序之上，有时将*额外的*补丁程序应用于软件包很有用。例如，这可能用于支持项目中的自定义功能，或者在使用新体系结构时。

的`BR2_GLOBAL_PATCH_DIR`配置选项可用于指定一个空间分隔为包补丁一个或多个目录的列表。

对于``特定软件包 的特定版本``，可通过`BR2_GLOBAL_PATCH_DIR`以下方式应用补丁：

1. 对于``存在于中的 每个目录-- `BR2_GLOBAL_PATCH_DIR`，``其确定如下：
   - `///` 目录是否存在。
   - 否则，`/`如果目录存在。
2. 然后将通过以下方式应用补丁``：
   - 如果`series`软件包目录中存在文件，则根据`series`文件应用补丁；
   - 否则，将按`*.patch`字母顺序应用补丁文件匹配。因此，为确保按正确的顺序应用它们，强烈建议将补丁文件命名为： `-.patch`，其中``指的是 *应用顺序*。

有关如何将补丁应用于软件包的信息，请参见 [第18.2节“如何应用补丁”。](http://nightly.buildroot.org/manual.html#patch-apply-order)

该`BR2_GLOBAL_PATCH_DIR`选项是为软件包指定自定义补丁目录的首选方法。它可用于为buildroot中的任何软件包指定补丁程序目录。还应使用它代替可用于软件包（例如U-Boot和Barebox）的自定义补丁程序目录选项。这样，它将允许用户从一个顶级目录管理其补丁。

`BR2_GLOBAL_PATCH_DIR`指定自定义补丁的首选方法是例外`BR2_LINUX_KERNEL_PATCH`。`BR2_LINUX_KERNEL_PATCH`应该用于指定URL上可用的内核补丁。***\*注意：\****`BR2_LINUX_KERNEL_PATCH`指定在Linux中可用的补丁程序之后应用的内核补丁程序，`BR2_GLOBAL_PATCH_DIR`这是通过Linux软件包的补丁程序后挂钩完成的。

## 9.9。添加特定于项目的软件包

通常，任何新软件包都应直接添加到`package` 目录中，然后提交给Buildroot上游项目。一般情况下，如何在Buildroot中添加软件包的详细信息，请参见 [第17章，*在Buildroot中添加新软件包，*](http://nightly.buildroot.org/manual.html#adding-packages)此处不再赘述。但是，您的项目可能需要一些无法上游的专有软件包。本节将说明如何将此类特定于项目的软件包保存在特定于项目的目录中。

如[第9.1节“推荐的目录结构”所示](http://nightly.buildroot.org/manual.html#customize-dir-structure)，针对特定项目的软件包的推荐位置为`package//`。如果您正在使用br2-external树功能（请参见[第9.2节“](http://nightly.buildroot.org/manual.html#outside-br-custom)在Buildroot [之外保留定制”](http://nightly.buildroot.org/manual.html#outside-br-custom)），建议的位置是将它们放在`package/`br2-external树中命名的子目录中。

但是，除非我们执行一些其他步骤，否则Buildroot将无法识别此位置的软件包。如 [第17章所述，*将新软件包添加到Buildroot中*](http://nightly.buildroot.org/manual.html#adding-packages)，[*Buildroot*](http://nightly.buildroot.org/manual.html#adding-packages)中的软件包基本上由两个文件组成：一个`.mk`文件（描述如何构建软件包）和一个 `Config.in`文件（描述此软件包的配置选项）。

Buildroot将自动将`.mk`文件包括在目录的第一级子目录中`package`（使用模式`package/*/*.mk`）。如果我们希望Buildroot包含`.mk`来自更深子目录（例如`package//package1/`）的`.mk`文件，则只需在包含这些附加`.mk`文件的第一级子目录中添加文件。因此，请创建一个`package//.mk`具有以下内容的文件 （假设您在下方仅具有一个额外的目录级别`package//`）：

```
include $(sort $(wildcard package/<company>/*/*.mk))
```

对于`Config.in`文件，创建一个`package//Config.in` 包含`Config.in`所有软件包文件的文件。由于kconfig的source命令不支持通配符，因此必须提供详尽的列表。例如：

```
source "package/<company>/package1/Config.in"
source "package/<company>/package2/Config.in"
```

`package//Config.in`从中 包含此新文件`package/Config.in`，最好将其包含在公司特定的菜单中，以使与以后的Buildroot版本的合并更加容易。

如果使用br2-external树，请参见[第9.2节“](http://nightly.buildroot.org/manual.html#outside-br-custom)在Buildroot [之外保留定制”](http://nightly.buildroot.org/manual.html#outside-br-custom)以了解如何填写这些文件。

## 9.10。项目自定义存储快速指南

在本章的前面，已经描述了进行特定于项目的自定义的不同方法。现在，本节将通过提供有关存储特定于项目的自定义设置的分步说明来总结所有这一切。显然，与项目无关的步骤可以跳过。

1. `make menuconfig` 配置工具链，软件包和内核。
2. `make linux-menuconfig` 更新内核配置，类似于其他配置，例如busybox，uclibc等。
3. `mkdir -p board/<manufacturer>/<boardname>`
4. 将以下选项设置为`board/<manufacturer>/<boardname>/<package>.config` （只要相关）：
   - `BR2_LINUX_KERNEL_CUSTOM_CONFIG_FILE`
   - `BR2_PACKAGE_BUSYBOX_CONFIG`
   - `BR2_UCLIBC_CONFIG`
   - `BR2_TARGET_AT91BOOTSTRAP3_CUSTOM_CONFIG_FILE`
   - `BR2_TARGET_BAREBOX_CUSTOM_CONFIG_FILE`
   - `BR2_TARGET_UBOOT_CUSTOM_CONFIG_FILE`
5. 编写配置文件：
   - `make linux-update-defconfig`
   - `make busybox-update-config`
   - `make uclibc-update-config`
   - `cp /build/at91bootstrap3-*/.config board///at91bootstrap3.config`
   - `make barebox-update-defconfig`
   - `make uboot-update-defconfig`
6. ` board/<manufacturer>/<boardname>/rootfs-overlay/` 创建文件，你需要存储在rootfs里面的。例如 `board///rootfs-overlay/etc/inittab`. 设置 `BR2_ROOTFS_OVERLAY` to `board///rootfs-overlay`. 
7. 创建一个构建后脚本 `board/<manufacturer>/<boardname>/post_build.sh`。设置`BR2_ROOTFS_POST_BUILD_SCRIPT`于`board/<manufacturer>/<boardname>/post_build.sh`
8. 如果必须设置其他setuid权限或必须创建设备节点，请创建`board/<manufacturer>/<boardname>/device_table.txt` 该路径并将其添加到中`BR2_ROOTFS_DEVICE_TABLE`。
9. 如果必须创建其他用户帐户，请创建`board///users_table.txt`该路径并将其添加到中`BR2_ROOTFS_USERS_TABLES`。
10. 要将自定义补丁添加到某些程序包，请在程序包命名的子目录中将设置`BR2_GLOBAL_PATCH_DIR` 为，`board/<manufacturer>/<boardname>/patches/`并为每个程序包添加补丁程序。每个补丁应称为` <packagename>-<num>-<description>.patch `。
11. 专门针对Linux内核，还有一个选项 `BR2_LINUX_KERNEL_PATCH`，其主要优点是它还可以从URL下载补丁。如果不需要此， `BR2_GLOBAL_PATCH_DIR`则是首选。U-Boot，Barebox，at91bootstrap和at91bootstrap3也有单独的选项，但是它们没有提供任何优势`BR2_GLOBAL_PATCH_DIR`，将来可能会被删除。
12. 如果需要添加特定于项目的程序包，` package/<manufacturer> `请在目录中创建 并放置您的程序包。创建一个` <manufacturer>.mk `包含`.mk`所有软件包文件的整体文件。创建一个整体 `Config.in`文件，以获取`Config.in`所有软件包的文件。`Config.in`从Buildroot的 `package/Config.in`文件中包括该文件。
13. `make savedefconfig` 保存buildroot配置。
14. `cp defconfig configs/_defconfig`

## 第10章常见问题和故障排除

## 10.1。*启动网络*后，引导挂起*…*

如果启动过程似乎在以下消息后挂起（消息不一定完全相似，具体取决于所选软件包的列表）：

```
释放初始化内存：3972K
初始化随机数生成器...完成。
正在启动网络...
启动dropbear sshd：生成rsa密钥...生成dsa密钥...确定
```

那么这意味着您的系统正在运行，但没有在串行控制台上启动外壳程序。为了使系统在串行控制台上启动外壳，必须进入Buildroot配置，在 子菜单中`System configuration`修改`Run a getty (login prompt) after boot` 和设置适当的端口和波特率`getty options`。这将自动调整`/etc/inittab`生成的系统的文件，以便外壳程序在正确的串行端口上启动。

## 10.2。为什么目标上没有编译器？

已经决定从Buildroot-2012.11版本中停止*对目标本地编译器的*支持，因为：

- 此功能既未维护也未测试，并且经常损坏；
- 此功能仅适用于Buildroot工具链；
- Buildroot主要针对板载资源有限（CPU，内存，大容量存储）的*小型*或*小型*目标硬件，在目标上进行编译没有太大意义；
- Buildroot旨在简化交叉编译，从而无需在目标上进行本机编译。

如果仍然需要在目标计算机上使用编译器，则Buildroot不适合您的目的。在这种情况下，您需要一个*实际的发行版，*并且应该选择以下内容：

- [嵌入式](http://www.openembedded.org/)
- [约克托](https://www.yoctoproject.org/)
- [Emdebian](http://www.emdebian.org/)
- [软呢帽](https://fedoraproject.org/wiki/Architectures)
- [openSUSE ARM](http://en.opensuse.org/Portal:ARM)
- [Arch Linux ARM](http://archlinuxarm.org/)
- …

## 10.3。为什么目标上没有开发文件？

由于目标上没有可用的编译器（请参见 [第10.2节“为什么目标上没有编译器？”](http://nightly.buildroot.org/manual.html#faq-no-compiler-on-target)），所以浪费头文件或静态库的空间是没有意义的。

因此，自Buildroot-2012.11发行以来，这些文件始终从目标中删除。

## 10.4。为什么目标上没有文档？

由于Buildroot主要针对板载资源有限（CPU，内存，大容量存储）的*小型*或*小型*目标硬件，因此浪费文档数据空间是没有意义的。

如果仍然需要有关目标的文档数据，则Buildroot不适合您的目的，您应该寻找一个*真正的发行版*（请参阅：[第10.2节“为什么目标上没有编译器？”](http://nightly.buildroot.org/manual.html#faq-no-compiler-on-target)）。

## 10.5。为什么有些软件包在Buildroot配置菜单中不可见？

如果某个软件包存在于Buildroot树中，并且没有出现在config菜单中，则最有可能意味着未满足某些软件包的依赖关系。

要了解有关软件包依赖性的更多信息，请在配置菜单中搜索软件包符号（请参见[第8.1节“ *制作*技巧”](http://nightly.buildroot.org/manual.html#make-tips)）。

然后，您可能必须递归地启用几个选项（与未满足的依赖项相对应）才能最终选择包。

如果由于某些未满足的工具链选项而导致该包不可见，那么您当然应该运行完整的重建（有关更多说明[，](http://nightly.buildroot.org/manual.html#make-tips)请参见[第8.1节“ ](http://nightly.buildroot.org/manual.html#make-tips)[*制作*](http://nightly.buildroot.org/manual.html#make-tips)[技巧”](http://nightly.buildroot.org/manual.html#make-tips)）。

## 10.6。为什么不将目标目录用作chroot目录？

有许多原因***\*不\****使用chroot目标目录，其中包括：

- 在目标目录中未正确设置文件所有权，模式和权限；
- 设备节点未在目标目录中创建。

由于这些原因，使用目标目录作为新根目录通过chroot运行的命令很可能会失败。

如果要在chroot中或作为NFS根目录运行目标文件系统，请使用生成的tarball映像`images/`并将其提取为root。

## 10.7。为什么Buildroot不生成二进制软件包（.deb，.ipkg…）？

Buildroot列表上经常讨论的一项功能是“程序包管理”的一般主题。总而言之，该想法将是增加对哪个Buildroot软件包安装哪些文件的跟踪，目标是：

- 当从menuconfig中取消选择该软件包时，能够删除该软件包安装的文件；
- 能够生成可以安装在目标上的二进制软件包（ipk或其他格式），而无需重新生成新的根文件系统映像。

通常，大多数人认为这很容易做到：只需跟踪安装了哪个软件包，然后在取消选择该软件包时将其删除即可。但是，它比这复杂得多：

- 它不仅与`target/`目录有关，而且与目录中的sysroot `host//sysroot`和`host/`目录本身有关。必须跟踪各种软件包在这些目录中安装的所有文件。
- 从配置中取消选择软件包时，仅删除其安装的文件是不够的。还必须删除其所有反向依赖关系（即依赖它的程序包）并重建所有这些程序包。例如，程序包A可选地依赖于OpenSSL库。两者都被选中，并且构建了Buildroot。软件包A使用OpenSSL进行加密支持。稍后，从配置中取消选择OpenSSL，但是保留了程序包A（因为OpenSSL是可选的依赖项，所以可以这样做。）如果仅删除OpenSSL文件，则由程序包A安装的文件将被破坏：它们使用的库是不再出现在目标上。尽管从技术上讲这是可行的，但它为Buildroot增加了很多复杂性，这与我们一直坚持的简单性背道而驰。
- 除了前面的问题之外，Buildroot甚至还不了解可选依赖项。例如，版本1.0中的程序包A从未使用过OpenSSL，但在版本2.0中，它会自动使用OpenSSL（如果有）。如果尚未更新Buildroot .mk文件以将其考虑在内，则程序包A将不属于OpenSSL的反向依赖关系，并且在删除OpenSSL时也不会删除和重建。可以肯定，软件包A的.mk文件应固定为提及此可选的依赖关系，但与此同时，您可以具有不可复制的行为。
- 该请求还允许将menuconfig中的更改应用于输出目录，而不必从头开始重新构建所有内容。但是，以可靠的方式很难做到这一点：更改包的子选项时会发生什么（我们必须检测到这一点，并从头开始构建包，并可能重新构建所有的反向依赖关系），如果使用工具链选项会发生什么情况目前，Buildroot所做的工作既清晰又简单，因此其行为非常可靠，并且易于支持用户。如果在下一次制作之后应用menuconfig中所做的配置更改，则它必须在所有情况下都能正确正确地工作，并且不会出现一些异常情况。风险是获取错误报告，例如“我启用了程序包A，B和C，然后运行make，

由于所有这些原因，得出的结论是，添加跟踪已安装文件以在未选择软件包时将其删除，或生成二进制软件包的存储库是很难可靠实现的，并且会增加很多复杂性。

在此问题上，Buildroot开发人员发表以下立场声明：

- Buildroot努力使生成根文件系统变得容易（顺便说一下，因此得名）。这就是我们要使Buildroot擅长的：构建根文件系统。
- Buildroot并不是要成为发行版（或更确切地说是发行版生成器）。大多数Buildroot开发人员都认为这不是我们应该追求的目标。我们相信，还有比Buildroot更适合生成发行版的其他工具。例如， [Open Embedded](http://openembedded.org/)或[openWRT](https://openwrt.org/)就是这样的工具。
- 我们倾向于将Buildroot推向易于（甚至更容易）生成完整根文件系统的方向。这就是Buildroot在人群中脱颖而出的原因（当然还有其他事情！）
- 我们认为，对于大多数嵌入式Linux系统，二进制软件包不是必需的，并且可能有害。使用二进制软件包时，意味着可以部分升级系统，这将产生大量可能的软件包版本组合，应在嵌入式设备上进行升级之前进行测试。另一方面，通过立即升级整个根文件系统映像来进行完整的系统升级，可以确保部署到嵌入式系统的映像确实是经过测试和验证的映像。

## 10.8。如何加快构建过程？

由于Buildroot通常涉及对整个系统进行完全重建，这可能会很长，因此我们在下面提供了一些技巧来帮助减少构建时间：

- 使用预构建的外部工具链，而不是默认的Buildroot内部工具链。通过使用预构建的Linaro工具链（在ARM上）或Sourcery CodeBench工具链（用于ARM，x86，x86-64，MIPS等），您将在每次完整重建时节省该工具链的构建时间，大约可节省15到15分钟。 20分钟。请注意，在系统的其余部分正常工作后，临时使用外部工具链不会阻止您切换回内部工具链（这可能会提供更高级别的自定义）。
- 使用`ccache`编译器缓存（请参见：[第8.13.3节“ `ccache`在Buildroot中使用”](http://nightly.buildroot.org/manual.html#ccache)）；
- 了解有关仅重建您实际关心的少数软件包的信息（请参见[第8.3节“了解如何重建软件包”](http://nightly.buildroot.org/manual.html#rebuild-pkg)），但是请注意，有时仍然需要完全重建（请参见[第8.2节“了解何时需要完全重建”](http://nightly.buildroot.org/manual.html#full-rebuild)）；
- 确保您没有在用于运行Buildroot的Linux系统上使用虚拟机。众所周知，大多数虚拟机技术都会对I / O产生显着的性能影响，这对于构建源代码确实非常重要。
- 确保仅使用本地文件：请勿尝试通过NFS进行构建，这会大大减慢构建速度。在本地拥有Buildroot下载文件夹也有帮助。
- 购买新硬件。SSD和大量RAM是加快构建速度的关键。
- 试用顶级并行构建，请参见 [第8.11节“顶级并行构建”](http://nightly.buildroot.org/manual.html#top-level-parallel-build)。

## 第11章已知问题

- `BR2_TARGET_LDFLAGS` 如果此类选项包含`$`符号 ，则无法通过这些选项。例如，以下内容被打破：`BR2_TARGET_LDFLAGS="-Wl,-rpath='$ORIGIN/../lib'"`
- `libffi`SuperH 2和ARC体系结构不支持 该软件包。
- 该`prboom`软件包使用Sourcery CodeBench 2012.09版的SuperH 4编译器触发编译器故障。

# 第三部分 开发人员指南

## 第14章Buildroot的工作方式

如上所述，Buildroot基本上是一组Makefile，可以使用正确的选项下载，配置和编译软件。它还包括各种软件包补丁-主要参与的交叉编译工具链的那些（`gcc`，`binutils`和 `uClibc`）。

每个软件包基本上只有一个Makefile，并且以`.mk`扩展名命名。Makefile分为许多不同的部分。

- 该`toolchain/`目录包含Makefile文件和相关文件的有关交叉编译工具链的所有软件：`binutils`，`gcc`，`gdb`， `kernel-headers`和`uClibc`。
- 该`arch/`目录包含Buildroot支持的所有处理器体系结构的定义。
- 该`package/`目录包含Buildroot可以编译并添加到目标根文件系统的所有用户空间工具和库的Makefile和相关文件。每个软件包有一个子目录。
- 该`linux/`目录包含Linux内核的Makefile和相关文件。
- 该`boot/`目录包含Buildroot支持的Bootloader的Makefile和相关文件。
- 该`system/`目录包含对系统集成的支持，例如目标文件系统框架和初始化系统的选择。
- 该`fs/`目录包含与目标根文件系统映像的生成相关的软件的Makefile和相关文件。

每个目录至少包含2个文件：

- `something.mk`是下载，配置，编译和安装软件包的Makefile `something`。
- `Config.in`是配置工具描述文件的一部分。它描述了与软件包有关的选项。

主Makefile执行以下步骤（一旦完成配置）：

- 创建所有的输出目录：`staging`，`target`，`build`等在输出目录（`output/`默认情况下，可以使用指定的另一个值`O=`）
- 生成工具链目标。当使用内部工具链时，这意味着生成交叉编译工具链。使用外部工具链时，这意味着检查外部工具链的功能并将其导入Buildroot环境。
- 生成`TARGETS`变量中列出的所有目标。该变量由所有单个组件的Makefile填充。生成这些目标将触发用户空间包（库，程序），内核，引导加载程序的编译以及根文件系统映像的生成，具体取决于配置。

## 15.1`Config.in`文件

`Config.in` 文件包含几乎所有在Buildroot中可配置的条目。

条目具有以下模式：

```
config BR2_PACKAGE_LIBFOO
        bool "libfoo"
        depends on BR2_PACKAGE_LIBBAZ
        select BR2_PACKAGE_LIBBAR
        help
          This is a comment that explains what libfoo is. The help text
          should be wrapped.

          http://foosoftware.org/libfoo/
```

- 的`bool`，`depends on`，`select`和`help`行缩进与一个选项卡。
- 帮助文本本身应缩进一个标签和两个空格。
- 帮助文本应换行以适合72列，其中制表符为8，因此文本本身为62个字符。

这些`Config.in`文件是*Buildroot中*使用的配置工具（即常规*Kconfig）的输入*。有关*Kconfig*语言的更多详细信息，请参阅 http://kernel.org/doc/Documentation/kbuild/kconfig-language.txt。

## 15.2。该`.mk`文件

- 标头：文件以标头开头。它包含模块名称，最好用小写字母，并包含在由80个哈希组成的分隔符之间。标头后必须有一个空白行：

  ```
  ################################################ #############################
  ＃
  ＃libfoo
  ＃
  ################################################ #############################
  ```

- 分配：`=`在前面加上一个空格：

  ```
  LIBFOO_VERSION = 1.0
  LIBFOO_CONF_OPTS += --without-python-support
  ```

  不要对准`=`标志。

- 缩进：仅使用制表符：

  ```
  define LIBFOO_REMOVE_DOC
          $(RM) -fr $(TARGET_DIR)/usr/share/libfoo/doc \
                  $(TARGET_DIR)/usr/share/man/man3/libfoo*
  endef
  ```

  注意，`define`块内的命令应始终以制表符开头，因此*make*会将其识别为命令。

- 可选依赖项：

  - 首选多行语法。

    是：

    ```
    ifeq ($(BR2_PACKAGE_PYTHON),y)
    LIBFOO_CONF_OPTS += --with-python-support
    LIBFOO_DEPENDENCIES += python
    else
    LIBFOO_CONF_OPTS += --without-python-support
    endif
    ```

    没有：

    ```
    LIBFOO_CONF_OPTS += --with$(if $(BR2_PACKAGE_PYTHON),,out)-python-support
    LIBFOO_DEPENDENCIES += $(if $(BR2_PACKAGE_PYTHON),python,)
    ```

  - 使配置选项和依赖项保持紧密联系。

- 可选的挂钩：如果一个代码块将挂钩的定义和分配保持在一起。

  是：

  ```
  ifneq ($(BR2_LIBFOO_INSTALL_DATA),y)
  define LIBFOO_REMOVE_DATA
          $(RM) -fr $(TARGET_DIR)/usr/share/libfoo/data
  endef
  LIBFOO_POST_INSTALL_TARGET_HOOKS += LIBFOO_REMOVE_DATA
  endif
  ```

  没有：

  ```
  define LIBFOO_REMOVE_DATA
          $(RM) -fr $(TARGET_DIR)/usr/share/libfoo/data
  endef
  
  ifneq ($(BR2_LIBFOO_INSTALL_DATA),y)
  LIBFOO_POST_INSTALL_TARGET_HOOKS += LIBFOO_REMOVE_DATA
  endif
  ```

## 15.3。文档

该文档使用 [asciidoc](http://www.methods.co.nz/asciidoc/)格式。

有关[asciidoc](http://www.methods.co.nz/asciidoc/) 语法的更多详细信息，请参见http://www.methods.co.nz/asciidoc/userguide.html。

## 15.4。支持脚本

`support/`和`utils/`目录中的某些脚本是用Python编写的，应遵循 [PEP8 Style Guide for Python Code的要求](https://www.python.org/dev/peps/pep-0008/)。

## 第16章为特定板添加支持

Buildroot包含几个公开可用的硬件板的基本配置，因此该板的用户可以轻松地构建已知可以正常工作的系统。也欢迎您为Buildroot添加对其他板的支持。

为此，您需要创建一个普通的Buildroot配置，该配置为硬件构建一个基本系统：工具链，内核，引导加载程序，文件系统和一个仅BusyBox专用的用户空间。不应选择特定的程序包：配置应尽可能少，并且应仅为目标平台构建可运行的基本BusyBox系统。当然，您可以为内部项目使用更复杂的配置，但是Buildroot项目将仅集成基本的电路板配置。这是因为软件包选择是高度特定于应用程序的。

有了已知的工作配置后，请运行`make savedefconfig`。这将`defconfig`在Buildroot源树的根目录下生成一个最小文件。将此文件移到`configs/` 目录中，然后重命名`_defconfig`。

建议使用尽可能多的Linux内核和引导程序上游版本，并使用尽可能多的默认内核和引导程序配置。如果它们对您的电路板不正确或不存在默认值，我们建议您将修复程序发送到相应的上游项目。

但是，与此同时，您可能需要存储特定于目标平台的内核或引导程序配置或补丁。为此，创建一个目录`board/`和一个子目录 `board//`。然后，您可以将修补程序和配置存储在这些目录中，并从Buildroot主配置中引用它们。有关更多详细信息[，](http://nightly.buildroot.org/manual.html#customize)请参见[第9章，](http://nightly.buildroot.org/manual.html#customize)[*特定*](http://nightly.buildroot.org/manual.html#customize)于[*项目的自定义*](http://nightly.buildroot.org/manual.html#customize)。
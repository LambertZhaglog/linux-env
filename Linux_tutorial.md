[TOC]



# Git 和GitHub的使用

github 的项目在国内 clone 特别慢，可以使用 gitclone.com代理。用法如下

```shell
git clone https://gitclone.com/github.com/xxx 
```

[常用 Git 命令](https://blog.csdn.net/ThinkWon/article/details/101450420)

## 概念

Git 把数据看作是对小型文件系统的一组快照，每次你提交更新，或在Git中保存项目状态时，它对当时的全部文件制作一个快照并保存这个快照的索引。

Git 操作只往Git数据库中添加数据，很难让Git 执行任何不可逆操作。

Git 有三种状态，文件可能处于其中之一

- 已提交（committed）：已提交表示数据已经安全的保存在本地数据库中
- 已修改（modified）：已修改表示修改了文件，但还没有保存到数据库中
- 已暂存（staged）：已暂存表示对一个已修改文件的当前版本做了标记，使之包含在下次提交的快照中。

由此引入Git 项目的三个工作区域的概念

- Git仓库（.git directory (repository) ）：Git 仓库目录是Git用来保存项目的元数据和对象数据库的地方。从其他计算机克隆仓库时，拷贝的就是这里的数据
- 工作目录（Working Directory）：工作目录是对项目的某个版本独立提取出来的内容。这些从Git 仓库提取出来的文件，放在磁盘上供你使用或修改
- 暂存区域（staging area）：保存了下次将提交的文件列表信息

## Git 分支理解

在进行提交操作时，Git会保存一个提交对象（commit object）。知道了Git保存数据的方式，我们可以很自然的想到--该提交对象包含一个指向暂存内容快照的指针。不仅仅是这样，该提交对象还包含作者的姓名和邮箱、提交时输入的信息以及指向它的父对象的指针。首次提交产生的提交对象没有父对象，普通提交操作产生的提交对象有一个父对象，而由多个分支合并产生的提交对象有多个父对象。

暂存操作会为每一个文件计算校验和，然后会把当前版本的文件快照保存到Git仓库中，最终将校验和加入暂存区域等待提交。

当使用 `git commit` 进行提交操作时，Git会先计算每一个子目录的校验和，然后在Git仓库中这些校验和保存为树对象。随后，Git便会创建一个提交对象，这个提交对象包含这个树对象（项目根目录）的指针。如此一来，Git就可以在需要的时候重现此次保存的快照。

**Git的分支，其本质上仅仅是指向提交对象的可变指针**。因此Git 创建新分支，只是创建了一个可以移动的新的指针。Git如何知道当前工作目录在哪一个分支上呢？也很简单，它有一个名为HEAD的特殊指针

要切换到一个已存在的分支，只需要使用 `git checkout` 命令，该命令使得HEAD指针指向指定的分支上，并把工作目录的内容切换为此分支对应的内容。

`git merge` 执行的逻辑，比如当前HEAD 在分支A上，此时执行 `git merge B` 会把B分支的内容和A分支的内容合并，将结果保存对A分支上，HEAD在A分支上前移。

关于git merge 如何实现的理解

- 三方合并的实现，即把B对A的修改，C对A的修改合并起来，最终得到D；具体实现方式为，分别求算出B-A, C-A。 逐行生成D，方式是，同时查阅A，B-A, C-A，对于生成的新行，要同时满足A，B-A, C-A，这三个约束条件。比如对于A的第一行，B-A, C-A均未提出对其的修改，则把A的第一行copy到D文件中；对于A的第二行，B-A表示对其进行替换，C-A不对其进行任何操作，则把B-A 替换后的行copy到D文件中；对于A的第三行，C-A对其进行删除，B-A不对其进行任何操作，则按照C-A 的方式，不把A的第三行copy到D文件中；对于A的第四行，C-A表示将其进行替换，B-A表示对其删除，则`merge`指令无法判断正确的操作，因此终止执行，交由程序员选择正确的操作。

git 变基：无论通过变基，还是通过三方合并，整合的最终结果所指向的快照始终是一样的，只不过提交历史不同罢了。变基是将一系列提交按照原有次序依次应用到另一个分支上，而合并是把最终结果合在一起。 变基操作的实质是丢弃一些现有的提交，然后相应地新建一些内容一样但实际上不同的提交。

###### 小练习

[仓库](https://github.com/sauronalexander/cs110-1.git) 是一个已完成的作业，提取其中的文件，恢复为作业的starter code

# Linux文本操控工具与管道

比如 cat，cut，head，tail，grep，diff，sed，awk，sort，wc，comm，nl ……都是文本工具，流式处理，可以使用管道连接起来使用。

所谓的流式处理，是以 “行”，或者 “文本行”为最小单位的。流不是以字符，字节，单词为单位的。这每个命令用C语言实现时，都在需要CSAPP的system IO一章实现string buffer，提供了对文本的按行输出。

| 命令 | 作用                                                         |
| ---- | ------------------------------------------------------------ |
| cat  | 把文本文件转换为“行流”                                       |
| cut  | 按照 指定分隔符把 一行分隔为若干个部分                       |
| head | 等同于cat，但是输出时会计数判断                              |
| tail | 类似于 head                                                  |
| grep | 把含有指定 关键字 或者模式的 行输出，不含有的，被过滤掉      |
| diff | 把两个流做比较                                               |
| sed  | 对流中的第n行，做指定的处理                                  |
| sort | 对流做插入排序                                               |
| wc   | 对流计数                                                     |
| nl   | 把流中的某行的首位拼接行号                                   |
| awk  | 对于每一行这个流中的最小单位，分栏，分栏的依据是空格+制表符，然后对每一栏进行操作 |
| tr   | 检测流中某些字符进行替换或转换                               |

把这些简单的基础命令组合起来，能够实现对于文本行任意的操作、格式化的操作。处理文本写 C 函数进行复杂的编译链接执行过程了，直接拿套件进行组合。

# 远程连接

## ssh

- -X 选项实现 X  window forwarding 
- -L 或 -R 选项实现端口映射
- `ssh-copy-id usename@host_ip` 以后连接不需要输密码

### autossh—内网穿透

参考 [知乎](https://zhuanlan.zhihu.com/p/112227542)

持久化连接，不会断

#### 步骤

配置公网主机 sshd_config, uncomment the following line

```
GatewayPorts yes
```

重启sshd

```
sudo service sshd restart
```

配置实验室电脑10.1

```sh
autossh -M 4010 -fCNR 6666:localhost:22 lambert@ip
```

配置实验室电脑10.12

```shell
autossh -M 4020 -fCNR 10012:localhost:22 lambert@ip
```

## scp

文件上传下载

## sshfs

挂在服务器上的目录到本地，从而操作服务器上的文件就像操作本地文件一样

```shell
#新建本地挂载点
$ mkdir local-file
#挂载
$ sshfs user@hostname:/absolute/path/to/document local-file
#解挂载
$ umount local-file
```

# docker 使用

[docker 常用命令](https://www.cnblogs.com/me115/p/5539047.html)

```shell
#以下命令，进行文件映射并进入 docker
sudo docker run -it -v `pwd`:/data hap.py /bin/bash
# 进入 docker 之后
/opt/hap.py/bin/hap.py -r /data/hs37d5.fasta /data/best_ts_somatic.vcf /data/sentieon_ts_somatic.vcf -o /data/test &>/data/run.log
#暂停 docker 执行，并退出 docker 界面
exit # or Ctrl-d
# 查看刚退出的容器名
sudo docker ps -a
# 启动 docker 执行（后台）
sudo docker start [CONTAINER ID]
# 进入后台运行的 docker
sudo docker attach [CONTAINER ID]

PATH=$PATH:/opt/hap.py/bin
export PATH
```

# Tmux 使用

[CheatSheet](https://tmuxcheatsheet.com/)

快捷键有前缀 `Ctrl+B`

三个基本概念：会话（session），窗口（window），面板（Panel）

tmux 一个 session 可以有多个窗口，一个窗口可以有多个面板，一个窗口占满一整块屏幕

## session

`tmux new -s <session-name>` 新建 session

`Ctrl-B d` detatch ，退出，可以用 tmux attach 召回

`tmux attach -t <session-name>` 进入已有 session

`Ctrl d` 退出 session 不可以召回

## window

`Ctrl-B c` 新建窗口

`Ctrl-B &` 关闭窗口

`Ctrl-B ,` 重命名窗口

## panel

`Ctrl-B %` 横向分割面板

`Ctrl-B "` 纵向分割面板

`Ctrl-B x` 关闭 panel

## 支持鼠标交互

1. 在~/.tmux.conf中加入：

```shell
# 老版tmux 如下设置
setw -g mouse-resize-pane on
setw -g mouse-select-pane on
setw -g mouse-select-window on
setw -g mode-mouse on
# 新版tmux 如下设置
setw -g mouse on
```

这几行的作用分别是:

开启用鼠标拖动调节pane的大小（拖动位置是pane之间的分隔线）
开启用鼠标点击pane来激活该pane
开启用鼠标点击来切换活动window（点击位置是状态栏的窗口名称）
开启window/pane里面的鼠标支持（也即可以用鼠标滚轮回滚显示窗口内容，此时还可以用鼠标选取文本）

2. `source ~/.tmux.conf`



Mac 上，开启鼠标交互后，命令行中无法自由的选中和复制粘贴，选中可以通过 fn，鼠标，鼠标左键配合的方式完成

# Emacs的使用

## 必杀技

`C-u number command` 将命令command重复执行number次

`C-g` 取消command输入

`M-x xterm-mouse-mode` 支持鼠标，包括切换pane，选中区域，选中位置，鼠标滑动

## 移动光标

`C-V` 把显示区域往下移动一整屏

`M-V` 把显示区域往上移动一整屏

`C-a` 把光标移动到行首

`C-e` 把光标移动到行尾

`C-M-f` `C-M-b` move forward/backward over a balanced expression

## 复制粘贴删除

`C-@` 开始标记区域，此时光标移动会选中区域

`C-w` 删除选中区域

`M-w` 复制选中区域

`C-y` 把刚删除或者复制的区域 粘贴到光标所在处

`C-M-k` 删除 balanced expression

`C-M-@` 选中 balanced expression

## 查找替换

`C-s` 正向查找

`C-r` 反向查找

替换命令

1. 输入 `M-%` 
2. 输入查找字符串，回车
3. 输入替换字符串，回车
4. 按 `y` 确认替换当前字符串并移动到下一个匹配串
   按 `n` 确认替换当前字符串并移动到下一个匹配串
   按 `！` 进行全局替换

## 多窗口

`C-x 2` 把屏幕分成上下两个窗口

`C-x 3` 把屏幕分成左右两个窗口

`C-x 0` 删除当前窗口

`C-x 1` 当前窗口独占屏幕

`C-x o` 移动光标到其他窗口

## 编程支持

1. 把显示切换到指定的语言模式 `M-x <language>-mode` 
2. `M-;` 在当前行插入注释符号对（/* */）
3. 选中区域 
   -  `M-x comment-region`  将内容转为注释
   - `M-x uncomment-region`  将注释解除
4.  `M-x compile` 编译整个目录，要求当前工作目录下有Makefile
5. ~~使用 `find -name "*.[ch]" | xargs etags -` 可以创建TAG文件~~ tag 索引导航用 GNU Global 更智能、更友好
6. `M-.` `M-,` 可以根据TAG文件跳转到定义或者跳转到声明，并跳转回来
7. `M-/`自动补全，多按几次可以有一个补全队列
8. `M-x line-number-mode` 读代码的时候显示行号
9. `C-c g s` 跳转到reference

## emacs lisp

1. 在scratch buffer 中可以输入 emacs lisp 指令，然后通过`M-x eval-buffer` 来执行这些函数
2. `M-x ielm` 呼出interactive lisp evaluate environment, a lisp interpreter 

## 其他

1. `M-x shell`、`M-x gdb` 可以呼出shell窗口和gdb调试环境
2. 国内 elpa 源访问太慢，切换成[清华](https://mirrors.tuna.tsinghua.edu.cn/help/elpa/)的，或[腾讯的源](https://mirrors.cloud.tencent.com/elpa/)
3. `f3`, `f4` 录制操作并回放
4. 使用 tuhdo 提供好的 C/C++ ~/.emacs.d/ 需要手动删除本地的.emacs 文件
5. add-[hook](https://tuhdo.github.io/emacs-tutor3.html#orgheadline8) 是一种固化 emacs 配置的方式，还可以使用其他的方式，比如修改.emacs 文件加入`(custom-set-variables '(xterm-mouse-mode) t)` 设置 xterm-mouse-mode 开

解决 Could not query gcc for defines. Maybe g++ is not installed

# 编译器

**GCC 工具链** 可以编译C、C++、Java、Go 等各种语言。`gcc`、`g++` 等可视为针对特定语言的，把GCC工具链相应工具连接起来的一个脚本

**Clang**是编译器的前端，性能并没有比GCC更好。Clang 更好的把程序员编写的代码和代码生成树联系起来，使得程序员可以交互式的检查语法错误。

**LLVM**是编译器的中端和后端，进行中间层优化和汇编代码生成。

# shit mountain analysis — deprecated

必备，graphviz 是画一切关系图的基础

静态 call graph 

- 缺陷：对继承多态无能为力
- 原理：编译原理得到AST
- 工具 Doxygen

动态 call graph

- 原理 1：gcc 编译加入 instrument，每个函数被调用和调用返回执行特定代码输出到文件，see [gcc manual](https://gcc.gnu.org/onlinedocs/gcc-10.2.0/gcc/Instrumentation-Options.html#Instrumentation-Options) : `-finstrument-function`。而后对文件进行数据处理，根据`-g` 信息解析函数名，使用 graphviz 生成调用图。需要编译器的支持
- 猜测：原理 2：程序运行在虚拟机，host 和 guest 相同的 ABI，虚拟机补货所有 call 类，ret类指令，记录下来。仅需要`-g` 的支持

## 静态调用图

`doxygen -g`，得到 Doxygen 文件

编辑 Doxygen 文件，着重以下默认不开启或不设置的地方

```shell
# The format is:
# TAG = value [value, ...]
# For lists, items can also be appended using:
# TAG += value [value, ...]
# Values that contain spaces should be placed between quotes (\" \").
#项目名
PROJECT_NAME = "bwa"
#指定待生成文档的路径
OUTPUT_DIRECTORY = /home/lambert/Documents/doxygen_bwa
#现实子类继承父类的成员，方法
INLINE_INHERITED_MEMB = YES
# 针对特定语言，Doxygen 会给予优化，比如如果项目是 纯 C，则
OPTIMIZE_OUTPUT_FOR_C
# 对 CPP，开启对 STL class 的支持
BUILIN_STL_SUPPORT = YES

EXTRACT_STATIC = YES 
#设定需要解析的源码文件或路径
INPUT = /src/programs /src/bwa
#递归分析 INPUT 指定的路径
RECURSIVE = YES 
#document 中包括源码
SOURCE_BROWSER = YES
INLINE_SOURCES = YES
#html 生成树形导航
GENERATE_TREEVIEW = YES
# 生成 static call graph
HAVE_DOT = YES
CLASS_GRAPH = YES
CALL_GRAPH = YES
CALLER_GRAPH = YES
#控制 dot graph 中节点的最大个数，比如
DOT_GRAPH_MAX_NODES = 100
```

`doxygen` 得到静态 call graph 文档

## 动态调用图

使用 callgrind 跟踪程序执行调用信息，使用[gprof2dot](https://github.com/jrfonseca/gprof2dot/blob/master/sample.png) 把调用信息转化为 dot Domain Special Language，使用dot 将前述 dot 文件转化为 svg 图片

1. compile your program with `-g` option
2. `valgrind --tool=callgrind [callgrind options] your-program [program options]`
3. `a_path/gprof2dot.py -f callgrind callgrind.out.[pid] | dot -Tsvg -o callgraph.svg`

如果是 C++ 代码中间可以插入 c++filt，对函数名进行美化

注意：call graph 关注调用次数，而不是调用时间。热点区域才需要关注执行时间，而且 callgrind 的执行时间压根就不准

## 火焰图

- 简介：一种 stack trace 可视化的手段

# 计划任务

## 单次执行

`at` 命令，需要 atd daemon 的支持

```shell
# 确保 atd 的状态为 enabled & running
$ systemctl enable atd
$ systemctl status atd
#一个使用示例
$ at now + 1 minutes
warning: commands will be executed using /bin/sh
at> echo "at hahah " >at.txt
at> <EOT>
job 2 at Fri Jan 15 08:21:00 2021
```

`at` 是在指定时间执行， `batch` 是等系统负载不高时执行

## 周期执行

### 用户自定义 cron

`crontab -e` 编辑周期执行的文件，看注释关于周期命令的表示方式`分 时 日 月 周 命令`。注意一下特殊字符

```
* 表示任意时间
，表示并列的时间
- 表示时间段
/ 表示每个
```

### 系统配置文件

手动编辑 /etc/cron.d/ 下的文件。注意格式`分 时 日 月 周 用户 命令`

# System monitor

## 原理 

操作系统的实现细节。把 **system**, **per-device**, **per-process**, **per-thread** 的 kernel data structures 导出为 pseudo-filesystem。两个filesystem，`/proc` 存储随时间变化的信息，`/sys` 存储静态信息。两个文件系统中的有些文件是可以修改的，对应就是更改配置。

所以最重要的是对操作系统有清晰的认识，我写过了什么模块，这里就能理解什么东西

## sar 工具

安装和启用

1. 修改配置文件 `/etc/default/sysstat` :设置 `ENABLE="true"`
2. `sudo service sysstat restart`

使用

Linux kernel 根据 `/etc/cron.d/sysstat`文件的配置周期性的执行 `/usr/lib/sysstat/debain-sa1 ` 脚本，`debain-sa1`脚本是脚本`/usr/lib/sysstat/sa1` 的 wrapper，脚本`sa1` 是直接调用`sadc` 

修改`/etc/cron.d/sysstat` 文件，内容如下

```shell
# 设置每隔两分钟记录一次系统状态
*/2 * * * * root command -v debian-sa1 > /dev/null && debian-sa1 1 1
```

修改`/usr/lib/sysstat/sa1`文件，内容如下

```shell
#修改 sadc 的参数，从而记录所有能够记录all possible activity
-- exec ${ENDIR}/sadc -F -L ${SADC_OPTIONS}
++ exec ${ENDIR}/sadc -F -L -S XALL ${SADC_OPTIONS}
```

图示化示例

```shell
# -g 输出格式为 svg，保存到文件 sadf.svg
sadf -g > sadf.svg
sadf -g -s 开始时间 -e 结束时间 -P 筛选 processor 的信息去展示 -O 控制生成 svg 文件的格式 -- 添加 sar 的参数 选择/var/log/sysstat/saX 选择当月X号的内容去展示 > sadf.svg
# sar 的参数设置通过快速浏览 `man sar` 或者 system performance Appendix C
# 展示所有 activity
sadf -g -- -A > sadf.svg
# 展示昨天下午一点到晚上 10 点的 CPU 利用率、disk IO、内存占用，结果以 svg 格式保存到 sadf.svg 文件中，svg 格式自动收缩、同一个 activity的多个 metric、生成目录
sadf -g -s 13:00 -e 22:00 -1 -O autoscale,packed,showtoc -- -ubr -h > sadf.svg
```

sar 的缺陷，只能展示整个系统的性能，最多对每个CPU core 进行区分，不能细化到进程或线程的颗粒度

# Makefile

主要参见 GNU/LINUX applicate programming  2nd edition by M.Tim Jones

## 问题与解释

在编译C语言项目时，h文件会作为gcc编译时的参数值输入吗？不会，因为c文件include h文件，gcc的预编译器会自动进行替换。但是写Makefile的时候，填写依赖关系却要填写清楚，方便make工具检查时间。

@ for suppress the printing of the command line before it executes 什么叫做suppress

## 理解

Makefile 可以理解为脱胎于shell script的语言，他有

- 语法规则：目标、依赖、命令；时间检查执行
- 内置函数：比如subst, patsubst, filter, sort, words, word, wordlist等
- 内置符号或变量

## 特殊符号和变量

```makefile
%.o:%.c
	gcc -g -o $@ $
```

| 符号  | 含义                                                         |
| ----- | ------------------------------------------------------------ |
| $@    | contains the filename matched for the **left** side of the variable |
| $     | contains the filename matched for the **right** side of the variable |
| VPATH | provide the pattern-matching rules with a list of search directories to use when the right side of a rule isn't found in the current directory |

## 例程

automatic dependency tracking mechanism proposed in the GNU make manual

```makefile
SRC_FILES=$(shell find src/ -type f -name *.c)
OBJ_FILES=$(patsubst %.c, %.o, ${SRC_FILES})
DEP_FILES=$(patsubst %.c %.dep, ${SRC_FILES})

VPATH=src

CFLAGS = -c -g
LDFLAGS = -g 
appexp: ${OBJ_FILES}
		gcc ${LDFLAGS} -o appexp ${OBJ_FILES}
		
%.o:%.c
		gcc ${CFLAGS} -o $@ $
		
clean:
		rm *.o appexp
		
include ${DEP_FILES}

%.dep:%.c
		@set -e; rm -f $@;\
		gcc -MM ${CFLAGS} $< > $@.$$$$; \
		sed 's,\($*\)\.o[ :]*,\1.o $@ : ,g' < $@.$$$$ >$@;\
		rm -f $@.$$$$
```

## Autotools

automake, autoconf, alocal, libtool...

参见[链接](https://zhuanlan.zhihu.com/p/32369152)理解GNU Build System

# 实验室服务器重装系统 Ubuntu

选择 Ubuntu 系统的原因，相较 centos 更激进，apt 源支持的软件得到及时的更新

唯二参考资料：装系统 BIOS [后的设置](https://ubuntu.com/tutorials/install-ubuntu-server#1-overview)，装好系统后的[配置](https://ubuntu.com/server/docs/network-configuration)

涉及的最重要的任务是网络配置和用户管理，参考手册里都有

## 配置网络要点

临时添加 IP 和路由

```shell
#为eno1 网卡新加 IP
sudo ip addr add 10.0.0.12/24 dev eno1
#使eno1 网卡新加 IP 有效
sudo ip link set dev eno1 up
#添加网关，也就是路由器
sudo ip route add default via 10.0.0.1
```

固化配置

新建 `/etc/netplan/99_config.yaml` 内容为

```
network:
  version: 2
  renderer: networkd
  ethernets:
    eno1:
      addresses:
        - 10.0.0.12/24
      gateway4: 10.0.0.1
      nameservers:
          search: [mydomain, otherdomain]
          addresses: [10.0.0.1, 114.114.114.114]
 
```

在 `/etc/hosts` 添加以下内容

```
10.0.0.1  dell-r720
10.0.0.2 gpu-server2
10.0.0.3 gpu-server3
10.0.0.4 gpu-server4
10.0.0.5 gpu-server5
10.0.0.6 gpu-server6
10.0.0.7 gpu-server7
10.0.0.10 dell-r730-0
10.0.0.11 dell-r730-1
10.0.0.12 dell-r730-2
10.0.0.13 dell-r730-3
10.0.0.14 dell-r730-4
10.0.0.15 dell-r730-5
10.0.0.16 dell-r730-6
10.0.0.17 dell-r730-7

10.0.0.100 storage-1
10.0.0.101 storage-2
```

# 对工具的想法

把工具分两类

- 处理文本的
- 处理数据的

为了解决问题，很多时候我们需要的不是编程语言，是强大的工具。工具不必覆盖所有，只要覆盖高频次的需求。当我们遇到问题时，能够借助工具最低成本的解决问题。

想看到每条指令执行的结果，由反馈进行及时的调整 —> 交互式的命令行执行环境

- 处理文本的 shell 环境
- 处理数据的Python或matlab的交互式命令行环境

当把解决问题的路子走通了，并且问题多次出现，那么就需要有工具把琐碎的命令集中起来，不关注命令执行的中间过程，只追求解决问题得到结果 —> 各式脚本

处理完成之后还需要进行图示化

- 对文本进行关系图示化
- 对数据进行统计和函数图示化


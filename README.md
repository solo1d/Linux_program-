# Linux_program-
Linux程序设计
# GCC

##### 头文件

**依赖于特定Linux版本的头文件, 通常可在目录 `/usr/include/sys` 和 `/usr/include/linux`中找到.**
**编译时使用 `-I 路径` 来指定特定的头文件目录.**

##### 库文件

**`库`是一组预先编译好的,函数的集合,都是使用 可重用 规则来编写的.**
**`系统标准库文件` 一般都存在于 `/lib` 或 `/usr/lib` 以及 `/usr/lib64` 目录中.**
**库文件的名字总是以 `lib` 开头, 随后的部分指明这是什么库.**

- **`.a`  : 静态库**
- **`.so`: 动态库**
- **`ko` :驱动**
  **编译时使用 `-L 路径  -l 库名` 来进行库目录的指定,要么指定库中的所有文件,要么指定某一个库.**

```bash
首先创建三个文件,   free.c  ,fool.c   he.h   里面各包含一个函数.并且包含头文件,头文件内有这两个函数的定义
$gcc  -c  free.c  fool.c     #会生成两个文件  free.o   fool.o 

创建静态库文件  libfoo.a
$ar crv  libfoo.a   free.o  fool.o     #会生成一个  libfoo.a  静态库文件

编译时的参数
$gcc   main.c   -L .   -lfoo     -o main

查看 程序文件 或 静态库文件 内使用的函数都有哪些.
$nm  libfoo.a
```

**使用 `ldd` 命令来查看可执行程序运行需要的动态库.**

```bash
$ldd     main
```

**生成动态库   -shared 和 -fpic 选项·**

```bash
$gcc -shared -fpic -o libfoo.so     free.o  fool.o
#-shared  表示生成静态库
#-fpic  表示生成与位置无关代码
```

# shell

**使用变量时, `${变量} 可以正确输出变量的值`, `'${变量}'` 会输出 ${变量} 这个字符串,并不会发生替换.**

```bash
系统的自带变量值
$HOME      当前用户的家目录
$PATH	     路径
$PS1       命令提示符格式,通常就是一个 $ 符号
$PS2       二级提示符,用来提示后续的输出. 就是 > 字符
$IFS       输入域分隔符, 当shell 读取输入时,他会给出用来分割单词的一组字符,他们通常时空格,tab之类的
$0         shell 脚本的名字
$1         运行脚本时传递进来的参数的第一个
$2         运行脚本时传递进来的参数的第二个
$#         传递给脚本的参数个数
$@	       列出运行shell时传入的所有参数. $* 也是同样的效果,但是效果不好
$$	       当前进程的进程号,PID, 也就当前脚本的进程PID
```

**使用 `env` 和`export`命令可以得到变量变量列表.**

 ##### test 与 [  测试结构

```bash
字符串比较,  [] 与 test 是相同的命令,效果相同,参数相同
if [ "${string1}" = "${string2}" ]  ; then  ... ; fi    #相等时返回真 ,进入then中
if [ -n "${string}"  ]  ;  then  ... ; fi          #字符串不为空时 返回真
if [ -z "${string}"  ]  ;  then  ... ; fi          #字符串为空串(null) 返回真

算术比较   a=10 , b=20 , c=30 , ab=$((${a}+${b}))  , bc=$((${b}+${c}))
if [ ${ab} -eq  ${bc} ]  ; then ... ; fi   #两个表达式相等  返回真
if [ ${ab} -ne  ${bc} ]  ; then ... ; fi   #两个表达式不相等 返回真
if [ ${ab} -gt  ${bc} ]  ; then ... ; fi   #ab 大于BC  返回真
if [ ${ab} -ge  ${bc} ]  ; then ... ; fi   #ab 大于等于BC  返回真
if [ ${ab} -lt  ${bc} ]  ; then ... ; fi   #ab 小于BC  返回真
if [ ${ab} -le  ${bc} ]  ; then ... ; fi   #ab 小于等于BC  返回真
if [ ! ${ab} ] ; then  ... fi   #取反

文件条件测试.
if [ -d "${file}"  ] ; then ... ; fi   #如果文件是一个目录 返回真
if [ -e "${file}"  ] ; then ... ; fi   #如果文件或目录存在  返回真
if [ -f "${file}"  ] ; then ... ; fi   #如果文件是一个普通文件 返回真
if [ -g "${file}"  ] ; then ... ; fi   #如果文件的SGID 被设置  返回真
if [ -r "${file}"  ] ; then ... ; fi   #如果文件可读 返回真
if [ -s "${file}"  ] ; then ... ; fi   #如果文件的大小不为0  返回真
if [ -u "${file}"  ] ; then ... ; fi   #如果文件的SUID 被设置 返回真
if [ -w "${file}"  ] ; then ... ; fi   #如果文件可写 返回真
if [ -x "${file}"  ] ; then ... ; fi   #如果文件可执行 返回真
```

##### 控制结构

```bash
if  [ 测试 ] ; then   ...   ; fi
if  [ 测试 ] ; then   ...   ; else ... ; fi
if  [ 测试 ] ; then   ...   ; elif [ 测试 ] ; then ...  ; else ...  ; fi
```

##### 循环结构

```bash
可以使用 break; 跳出循环
continue 跳到下次循环

这个是有限循环.
foo="zero"
for  foo in one tow three four five ; do
	  echo ${foo}
done
# echo 会循环输出 foo, 每次 foo都会被赋予后面的数值,也就是字符串
# 输出:  one tow three four five 
# 注意,这里并不会输出 foo 的原有值,会直接被覆盖. 循环结束后 foo 的值会是 five


这个是不定循环
while  [ -n  "${foo}"  ]  ; do
    echo ${foo}
    foo=${NULL}
done
```

##### 选择结构

```bash
foo="a"
case foo in
  "b")  echo 'b' ;;   #后面两个;; 是分隔符, 也是必须要存在的东西
  "c" | "d" )  echo 'c and d' ;; 
  "a")  echo 'a' ;; 
esac
```

##### 函数

```bash
格式:
函数名 () { 
	函数体
}

返回值:
  使用一个全局变量来返回. 将返回值写入一个全局变量,然后在读出来就好了,很方便,但也麻烦
  使用 echo 打印返回值, 然后捕捉起来. ( foo(){ echo 'a' }  retu="$(foo)" ). 会优先执行名称相同的函数,而不是命令
  默认情况下,函数会返回最后一条指令执行后的退出码.
  使用函数 return 1  来制定返回值是多少,这里写的是返回1

传入函数的参数:
  使用 $1 $2 $3 .. 分别表示传入的第一个参,第二个参数....

调用函数和传入参数:
  foo          #调用 没有参数
  foo  1 2 3   #调用 并且传入三个参数

区分本地变量与全局变量:
  local  变量名=值         #本地变量,只可以在该函数体内使用,并且屏蔽同名的全局变量.
  变量名=值                #全局变量
```

##### 信号处理

```bash
trap 命令
tarp   'command'  signal 
	#command 为处理信号的函数或指令, 也可设置为 -  ,表示默认行为
	#signal 是信号, 忽略SIG 前缀.
		 HUP(1)       挂起,通常因终端掉线或用户退出而引发
		 INT(2)   	  中断 , crtl+c
		 QUIT(3)	    退出 , crtl+\
		 ABRT(6)	    中止. 通常是发生严重错误而引发
		 ALRM(14)	    警报, 通常是处理超时
		 TERM(15)	    终止, 通常在系统关机时发送
```

#### 调试脚本程序

```bash
sh -n 脚本        #只检查语法错误,不执行命令
sh -v 脚本        #在执行命令前回显它们
sh -x 脚本        #在处理完命令之后回显它们
sh -u 脚本        #如果使用了未定义的变量,就给出错误消息.
```

##### 在脚本被中断或结束时,清除残留的临时文件

```bash
脚本执行时,会在/tmp 目录下生成一个临时文件,文件名为 脚本名+PID, 脚本被中断会异常结束,应该进行清理.
#例如, 当前脚本名为 teno_file, PID为 111
trap  'rm -fr /tmp/teno_file$$' INT   #发生中断信号时清理
rm -fr /tmp/teno_file$$          #在脚本所有命令执行完毕后执行清理动作.
```



# 文件

```c
/dev/console   :系统控制台,错误和诊断信息都会发送到这个设备.

#include <unistd.h>
#include <sys/types.h>
#include <sys/uio.h>
size_t  write(int fildes, const void* buf, zise_t nbytes);

ssize_t read(int fildes, void *buf, size_t nbyte);

#include <fcntl.h>
#include <sys/stat.h>
int open(const char *path, int oflag);
int open(const char *path, int oflag, mode_t mode);
	/* 
		 oflag:   O_APPEND 结尾追加,    O_TRUNC:清空文件内容, 
						  O_CREAT:创建文件     ,O_EXCL:与O_CREAT一起使用,确保调用者创建出文件  
						  O_RDONLY 只读	   O_WRONLY 只写   O_RDWR 可读可写
  */
  /* 
  	 mode :  S_IRUSR  读.拥有者   S_IWUSR 写.拥有者   S_IXUSR  执行.拥有者
  					 S_IRGRP  读.所属组
  					 S_IROTH  读.其他人 
  		 设置的属性会被 umask 影响.
  */

#include <unistd.h>
int close(int fildes);
	/* 成功返回0, 出错返回 -1 ,  返回值很重要,需要查看 errno  这个错误值*/

#include <sys/ioctl.h>
#include <unistd.h>
int ioctl(int fildes, unsigned long request, ...);
	/*把控制信息传递给设备驱动程序,系统调用,可以控制硬件的行为, 特殊文件, acter专用文件（例如终端）可以通过ioctl（）进行控制 */
	/*   fildes 是已经打开的 设备描述符(和文件描述符一样). */

#include <unistd.h>
#include <sys/types.h>
off_t lseek(int fildes, off_t offset , int whence); 
	/* 文件中指针的位置 */
	/* offset 指定位置, 就是个数值 */
	/* whence : SEEK_SET 表示offset是一个绝对位置,  SEEK_CUR 表示offset是相对于当前位置的相对位置
					    SEEK_END 表示offset是相对于文件结尾的相对位置
	*/
```

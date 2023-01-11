# Linux常用命令

## 1. 文件管理
### *ls – 显示指定工作目录下的内容及属性信息
> ​	ls 命令是Linux下最常用的指令之一。ls命令为英文单词 list 的缩写，正如英文单词 list 的意思，其功能是列出指定目录下的内容及其相关属性信息。
>
> ​	默认状态下，ls命令会列出当前目录的内容。而带上参数后，我们可以用ls做更多的事情。作为最基础同时又是使用频率很高的命令，我们很有必要搞清楚ls命令的用法

语法格式:` ls [选项] [文件]`

```shell
# 常用参数
-a ：全部的档案，连同隐藏档( 开头为 . 的档案) 一起列出来～ 
-A ：全部的档案，连同隐藏档，但不包括 . 与 .. 这两个目录，一起列出来～ 
-d ：仅列出目录本身，而不是列出目录内的档案数据 
-f ：直接列出结果，而不进行排序 (ls 预设会以档名排序！) 
-F ：根据档案、目录等信息，给予附加数据结构，例如： 
*：代表可执行档； /：代表目录； =：代表 socket 档案； |：代表 FIFO 档案； 
-h ：将档案容量以人类较易读的方式(例如 GB, KB 等等)列出来； 
-i ：列出 inode 位置，而非列出档案属性； 
-l ：长数据串行出，包含档案的属性等等数据； 
-n ：列出 UID 与 GID 而非使用者与群组的名称 (UID与GID会在账号管理提到！) 
-r ：将排序结果反向输出，例如：原本档名由小到大，反向则为由大到小； 
-R ：连同子目录内容一起列出来； 
-S ：以档案容量大小排序！ 
-t ：依时间排序 
--color=never ：不要依据档案特性给予颜色显示； 
--color=always ：显示颜色 
--color=auto ：让系统自行依据设定来判断是否给予颜色 
--full-time ：以完整时间模式 (包含年、月、日、时、分) 输出 
--time={atime,ctime} ：输出 access 时间或 改变权限属性时间 (ctime) 
而非内容变更时间 (modification time)  
# 参考实例
# 列出所有文件(包括隐藏文件)：
[root@admin~]# ls -a
# 列出文件的详细信息：
[root@admin~]# ls -l
[root@admin~]# ll #简写
# 列出根目录(/)下的所有目录：
[root@admin~]# ls /
# 列出当前工作目录下所有名称是 “s” 开头的文件 :
[root@admin~]# ls -ltr s*
# 列出 /bin 目录下的所有目录及文件的详细信息 :
[root@admin~]# ls -lR /bin
# 列出当前工作目录下所有文件及目录并以文件的大小进行排序 :
[root@admin~]# ls -AS
```

### *cp – 复制文件或目录

> ​	cp命令可以理解为英文单词copy的缩写，其功能为复制文件或目录。 
>
> ​	cp命令可以将多个文件复制到一个具体的文件名或一个已经存在的目录下，也可以同时复制多个文件到一个指定的目录中。

语法格式：`cp [参数] [文件]`

```shell
# 常用参数
-a ：将文件的特性一起复制，等价于选项"-dpr"
-p ：连同文件的属性一起复制，而非使用默认方式，与-a相似，常用于备份
-i ：若目标文件已经存在时，在覆盖时会先询问操作的进行
-r ：递归持续复制，用于目录的复制行为
-u ：目标文件与源文件有差异时才会复制
-d ：当复制符号连接时，把目标文件或目录也建立为符号连接，并指向与源文件或目录连接的原始文件或目录
-l ：对源文件建立硬连接，而非复制文件
-s ：对源文件建立符号连接，而非复制文件
# 参考实例
# 复制目录：
[root@admin~]# cp -R dir1 dir2/
# 将文件test1改名为test2：
[root@admin~]# cp -f test1 test2
# 复制多个文件：
[root@admin~]# cp -r file1 file2 file3 dir
# 交互式地将目录 /usr/linuxcool 中的所有.c文件复制到目录 dir 中：
[root@admin~]# cp -r /usr/linuxcool/*.c dir
# 复制test目录下的file.txt文件到test1目录下
[root@localhost~]# cp /tmp/test/file.txt /tmp/test1
# 移动test目录下的file.txt文件到test1目录下
[root@localhost~]# mv test/file.txt test1
```

### *mkdir – 创建目录

> ​	mkdir命令是“make directories”的缩写，用来创建目录。
>
> ​	注意：默认状态下，如果要创建的目录已经存在，则提示已存在，而不会继续创建目录。 所以在创建目录时，应保证新建的目录与它所在目录下的文件没有重名。 mkdir命令还可以同时创建多个目录，删除目录时，可以使用rmdir指令，或者rm -r

语法格式 : `mkdir [参数] [目录]`

```shell
## 常用参数
-m, --mode	创建目录的同时设定权限(类似 chmod)，而不是 rwxrwxrwx 减 umask
-p, --parents  可以是一个路径名称。此时若路径中的某些目录尚不存在,加上此选项后,系统将自动建立好那些尚不存在的目录,即一次可以建立多个目录; 
-v, --verbose  每次创建新目录都显示信息
-R	强制创建目录
# 参考实例
# 在工作目录下，建立一个名为 dir 的子目录：
[root@admin~]# mkdir dir
# 在目录/usr/linuxcool下建立子目录dir，并且设置文件属主有读、写和执行权限，其他人无权访问
[root@admin~]# mkdir -m 700 /usr/linuxcool/dir
# 同时创建子目录dir1，dir2，dir3：
[root@admin~]# mkdir dir1 dir2 dir3
# 递归创建目录：
[root@admin~]# mkdir -p linuxcool/dir
# 当前目录下创建a1,a2,a5
[root@admin~]# mkdir a{1..5}
# 创建多层目录（没有父目录）
[root@admin~]# mkdir -R /tmp/h//d/b/d
```


### *mv – 移动或改名文件

> ​	mv命令是“move”单词的缩写，其功能大致和英文含义一样，可以移动文件或对其改名。
>
> ​	这是一个使用频率超高的文件管理命令，我们需要特别留意它与复制的区别：mv与cp的结果不同。mv命令好像文件“搬家”，文件名称发生改变，但个数并未增加。而cp命令是对文件进行复制操作，文件个数是有增加的。

语法格式：`mv [参数]`

```shell
# 常用参数
-i	若存在同名文件，则向用户询问是否覆盖
-f	覆盖已有文件时，不进行任何提示
-b	当文件存在时，覆盖前为其创建一个备份
-u	当源文件比目标文件新，或者目标文件不存在时，才执行移动此操作
# 参考实例
# 将文件file_1重命名为file_2：
[root@admin~]# mv file_1 file_2
# 将文件file移动到目录dir中 ：
[root@admin~]# mv file /dir
# 将目录dir1移动目录dir2中（前提是目录dir2已存在，若不存在则改名)：
[root@admin~]# mv /dir1 /dir2
# 将目录dir1下的文件移动到当前目录下：
[root@admin~]# mv /dir1/* .
```

### *rm - 移除文件或目录

> ​	rm是常用的命令，该命令的功能为删除一个目录中的一个或多个文件或目录，它也可以将某个目录及其下的所有文件及子目录均删除。对于链接文件，只是删除了链接，原有文件均保持不变。 rm也是一个危险的命令，使用的时候要特别当心，尤其对于新手，否则整个系统就会毁在这个命令（比如在/（根目录）下执行rm * -rf）。所以，我们在执行rm之前最好先确认一下在哪个目录，到底要删除什么东西，操作时保持高度清醒的头脑。

语法格式： `rm [参数] [文件]`

```shell
# 常用参数
-f	忽略不存在的文件，不会出现警告信息
-i	删除前会询问用户是否操作
-r/R	递归删除
-v	显示指令的详细执行过程
# 参考实例
# 删除前逐一询问确认：
[root@admin~]# rm -i test.txt.bz2
rm: remove regular file `test.txt.bz2’?
# 直接删除，不会有任何提示：
[root@admin~]# rm -f test.txt.bz2
# 递归删除目录及目录下所有文件：
[root@admin~]# mkdir /data/log
[root@admin~]# rm -rf /data/log
# 删除当前目录下所有文件：
[root@admin~]# rm -rf *
# 清空系统中所有的文件（？）：
[root@admin~]# rm -rf /*
```

### *find - 查找和搜索文件

> find命令可以根据给定的路径和表达式查找的文件或目录。find参数选项很多，并且支持正则，功能强大。和管道结合使用可以实现复杂的功能，是系统管理者和普通用户必须掌握的命令。
>
> find如不加任何参数，表示查找当前路径下的所有文件和目录，如果服务器负载比较高尽量不要在高峰期使用find命令，find命令模糊搜索还是比较消耗系统资源的。

语法格式：`find [参数] [路径] [查找和搜索范围]`

```shell
#常用参数：
-name	按名称查找
-size	按大小查找
-user	按属性查找
-type	按类型查找
-iname	忽略大小写
# 参考实例
# 使用-name参数查看/etc目录下面所有的.conf结尾的配置文件：
[root@admin~]# find /etc -name "*.conf
# 使用-size参数查看/etc目录下面大于1M的文件：
[root@admin~]# find /etc -size +1M
# 查找当前用户主目录下的所有文件：
[root@admin~]# find $HOME -print
# 列出当前目录及子目录下所有文件和文件夹：
[root@admin~]# find .
# 在/home目录下查找以.txt结尾的文件名：
[root@admin~]# find /home -name "*.txt"
# 在/var/log目录下忽略大小写查找以.log结尾的文件名：
[root@admin~]# find /var/log -iname "*.log"
# 搜索超过七天内被访问过的所有文件：
[root@admin~]# find . -type f -atime +7
# 搜索访问时间超过10分钟的所有文件：
[root@admin~]# find . -type f -amin +10
# 找出/home下不是以.txt结尾的文件：
[root@admin~]# find /home ! -name "*.txt"
```

#### 其它搜索指令

**whereis命令**

> ​	whereis命令只能用于程序名的搜索，而且只搜索二进制文件（参数-b）、man说明文件（参数-m）和源代码文件（参数-s）。如果省略参数，则返回所有信息。和find相比，whereis查找的速度非常快，这是因为linux系统会将 系统内的所有文件都记录在一个数据库文件中，当使用whereis和locate时，会从数据库中查找数据，而不是像find命令那样，通过遍历硬盘来查找，效率自然会很高。 但是该数据库文件并不是实时更新，默认情况下时一星期更新一次，因此，我们在用whereis和locate 查找文件时，有时会找到已经被删除的数据，或者刚刚建立文件，却无法查找到，原因就是因为数据库文件没有被更新。 

 语法格式：`whereis [-bmsu] [BMS 目录名 -f 文件名]`

```shell
# 常用参数
-b 定位可执行文件。
-m 定位帮助文件。
-s 定位源代码文件。
-u 搜索默认路径下除可执行文件、源代码文件、帮助文件以外的其它文件。
-B 指定搜索可执行文件的路径。
-M 指定搜索帮助文件的路径。
-S 指定搜索源代码文件的路径。
```

**locate命令**

> ​	locate命令可以在搜寻数据库时快速找到档案，数据库由updatedb程序来更新，updatedb是由cron daemon周期性建立的，locate命令在搜寻数据库时比由整个由硬盘资料来搜寻资料来得快，但较差劲的是locate所找到的档案若是最近才建立或 刚更名的，可能会找不到，在内定值中，updatedb每天会跑一次，可以由修改crontab来更新设定值。(etc/crontab)

 语法格式：`Locate [选择参数] [样式]`

```shell
# 常用参数
-e   将排除在寻找的范围之外。
-1  如果 是 1．则启动安全模式。在安全模式下，使用者不会看到权限无法看到 的档案。这会始速度减慢，因为 locate 必须至实际的档案系统中取得档案的 权限资料。
-f   将特定的档案系统排除在外，例如我们没有到理要把 proc 档案系统中的档案 放在资料库中。
-q  安静模式，不会显示任何错误讯息。
-n 至多显示 n个输出。
-r 使用正规运算式 做寻找的条件。
-o 指定资料库存的名称。
-d 指定资料库的路径
-h 显示辅助讯息
-V 显示程式的版本讯息
```

**which命令** 

> which 会在PATH变量指定的路径中，搜索某个系统命令的位置，并且返回第一个搜索结果：

语法格式：`which [可执行文件名称]`

```shell
# 常用参数
-n 　指定文件名长度，指定的长度必须大于或等于所有文件中最长的文件名。
-p 　与-n参数相同，但此处的包括了文件的路径。
-w 　指定输出时栏位的宽度。
-V 　显示版本信息
```

### *pwd – 显示当前路径

> ​	pwd命令是“print working directory”中每个单词的首字母缩写，其功能正如所示单词一样，为打印工作目录，即显示当前工作目录的绝对路径。
>
> ​	在实际工作中，我们经常会在不同目录之间进行切换，为了防止“迷路”，我们可以使用pwd命令快速查看当前我们所在的目录路径。

语法格式: `pwd [参数]`

```shell
# 常用参数
-L	显示逻辑路径，默认显示的是逻辑路径
-P	显示实际路径，而非使用链接的路径
# 参考实例
# 查看当前工作目录路径：
[root@admin~]# pwd
```


### head - 输出文件数据

> ​	输出文件最前面的几行数据

语法格式: `head [参数] [文件]`

```shell
# 常用参数
-c<数目> 显示的字节数
-n<行数> 显示的行数，默认情况下带参数-n 10
# 参考实例
# 显示file.txt文件的前两行内容
[root@localhost~]# head -2 file.txt
```

### touch - 创建文件

> 使用指令touch时，如果指定的文件不存在，则将创建一个新的空白文件
> 使用指令touch时，如果指定的文件存在，则变更文件的日期属性

```shell
创建一个file文本文件
[root@localhost~]# touch file.txt
批量文件
[root@localhost~]# touch file{1..5}.txt
```

## 2.文档编辑

### more - 显示文本文件内容

> ​	more命令用于将内容较长的文本文件内容（不能在一屏显示完）进行分屏显示，并且支持在显示时定位关键字。而对于内容较少的文本文件内容则推荐使用cat命令查看。

```shell
# 常用参数
-num	指定每屏显示的行数
-l	more在通常情况下把 ^L 当作特殊字符, 遇到这个字符就会暂停,-l选项可以阻止这种特性
-f	计算实际的行数，而非自动换行的行数
-p	先清除屏幕再显示文本文件的剩余内容
-c	与-p相似，不滚屏，先显示内容再清除旧内容
-s	多个空行压缩成一行显示
-u	禁止下划线
+/pattern	在每个文档显示前搜寻该字(pattern)，然后从该字串之后开始显示
+num	从第 num 行开始显示
# 内部命令操作
Space键：显示文本的下一屏内容
Enter键：向下n行，需要定义，默认为1行
斜线符\：接着输入一个模式，可以在文本中寻找下一个相匹配的模式
H键：显示帮助屏
B键：显示上一屏内容
Q键：退出more命令
Ctrl+F、空格键：向下滚动一屏
Ctrl+B：返回上一屏
=： 输出当前的行号
：f：输出文件名和当前的行号
V：调用vi编辑器
!：调用Shell，并执行命令
# 参考示例
# 显示文件file的内容，显示之前先清屏，附已显示的百分比：
[root@admin~]# more -dc file
# 显示文件file的内容，每10行显示一次，而且在显示之前先清屏：
[root@admin~]# more -c -10 file
# 显示文件file的内容，每5行显示一次，而且在显示之后再清屏：
[root@admin~]# more -p -5 file
# 逐页显示 file 文档内容，如有连续两行以上空白行则以一行空白行显示 ：
[root@admin~]# more -s file
# 从第 20 行开始显示 file 之文档内容 ：
[root@admin~]# more +20 file
```

### less – 分页显示工具

> ​	浏览文字档案的内容，用less命令显示文件时，PageUp键向上翻页，PageDown键向下翻页，要退出less程序，应按Q键。
>
> ​	less的作用与more十分相似，不同点为less命令允许用户向前或向后浏览文件，而more命令只能向前浏览 

```shell
# 常用参数
-b	置缓冲区的大小
-e	当文件显示结束后，自动离开
-f	强迫打开特殊文件，例如外围设备代号、目录和二进制文件
-g	只标志最后搜索的关键词
-i	忽略搜索时的大小写
-m	显示类似more命令的百分比
-N	显示每行的行号
-o	将less 输出的内容在指定文件中保存起来
-Q	不使用警告音
-s	显示连续空行为一行
-S	在单行显示较长的内容，而不换行显示
-x	将TAB字符显示为指定个数的空格字符
# 命令内部操作：
b 向后翻一页
d 向后翻半页
h 显示帮助界面
Q 退出less 命令
u 向前滚动半页
y 向前滚动一行
空格键 滚动一页
回车键 滚动一行
# 参考示例
# 查看文件 ：
[root@admin~]# less test.php
# ps查看进程信息并通过less分页显示：
[root@admin~]# ps -ef |less
# 查看命令历史使用记录并通过less分页显示：
[root@admin~]# history | less
# 浏览多个文件 ：
[root@admin~]# less log2018.log log2019.log
# 当正在浏览一个文件时，也可以使用 :e命令 打开另一个文件：
[root@admin~]# less file1
:e file2
```

### *cat – 在终端设备上显示文件内容

> ​	Linux系统中有很多个用于查看文件内容的命令，每个命令又都有自己的特点，比如这个cat命令就是用于查看内容较少的纯文本文件的。
>
> ​	注意：当文件内容较大时，文本内容会在屏幕上快速闪动（滚屏），用户往往看不清所显示的具体内容。因此对于较长文件内容可以按Ctrl+S键，停止滚屏；以及Ctrl+Q键可以恢复滚屏；而按Ctrl+C（中断）键则可以终止该命令的执行。或者对于大文件，干脆用more命令吧！

**语法格式：**`cat [参数] [文件]`

```shell
# 常用参数
-n	显示行数（空行也编号）
-s	显示行数（多个空行算一个编号）
-b	显示行数（空行不编号）
-E	每行结束处显示$符号
-T	将TAB字符显示为 ^I符号
-v	使用 ^ 和 M- 引用，除了 LFD 和 TAB 之外
-e	等价于”-vE”组合
-t	等价于”-vT”组合
-A	等价于 -vET组合
# 参考实例
# 查看文件内容
[root@admin~]# cat filename.txt
# 查看文件的内容，并显示行数编号：
[root@admin~]# cat -n filename.txt
# 查看文件的内容，并添加行数编号后输出到另外一个文件中：
[root@admin~]# cat -n linuxcool.log > linuxprobe.log
# 清空文件的内容：
[root@admin~]# cat /dev/null > /root/filename.txt
# 持续写入文件内容，碰到EOF符后结束并保存：
[root@admin~]# cat > filename.txt <<EOF
Hello, World 
Linux!
EOF
# 将软盘设备制作成镜像文件：
[root@linuxcool ~]# cat /dev/fd0 > fdisk.iso
```


### *echo – 输出字符串或提取Shell变量的值

> ​	echo命令用于在终端设备上输出字符串或变量提取后的值，这是在Linux系统中最常用的几个命令之一，但操作却非常简单。	
>
> 人们一般使用在变量前加上符号的方式提取出变量的值 ，例如：$PATH，然后再用echo命令予以输出。或者直接使用echo命令输出一段字符串到屏幕上，起到给用户提示的作用。

语法格式： echo [参数] [字符串]

```shell
# 常用参数
-n	不输出结尾的换行符
-e “\a”	发出警告音
-e “\b”	删除前面的一个字符
-e “\c”	结尾不加换行符
-e “\f”	换行，光标扔停留在原来的坐标位置
-e “\n”	换行，光标移至行首
-e “\r”	光标移至行首，但不换行
-E	禁止反斜杠转移，与-e参数功能相反
# 参考实例
# 输出一段字符串：
[root@admin~]# echo 'admin.com'
admin.com
# 输出变量提取后的值：
[root@admin~]# echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin
# 对内容进行转义，不让$符号的提取变量值功能生效：
[root@admin~]# echo \$PATH
$PATH
# 结合输出重定向符，将字符串信息导入文件中：
[root@admin~]# echo "It is a test" > admin
# 使用反引号符执行命令，并输出其结果到终端：
[root@admin~]# echo `date
# 输出带有换行符的内容：
[root@admin~]# echo -e "a\nb\nc"
a
b
c
# 输出信息中删除某个字符，注意看数字3消失了：
[root@admin~]# echo -e "123\b456"
12456
```


### tail – 查看文件尾部内容

> ​	tail用于显示文件尾部的内容，默认在屏幕上显示指定文件的末尾10行。如果给定的文件不止一个，则在显示的每个文件前面加一个文件名标题。如果没有指定文件或者文件名为“-”，则读取标准输入。

语法格式：`tail [参数]`

```shell
# 常用参数
–retry	即是在tail命令启动时，文件不可访问或者文件稍后变得不可访问，都始终尝试打开文件。使用此选项时需要与选项“——follow=name”连用
-c或——bytes=	输出文件尾部的N（N为整数）个字节内容
-f<name/descriptor> --follow：	显示文件最新追加的内容
-F	与选项“-follow=name”和“–retry”连用时功能相同
-n< N>或——line=< N>	输出文件的尾部N（N位数字）行内容
–pid=<进程号>	与“-f”选项连用，当指定的进程号的进程终止后，自动退出tail命令
-f	循环读取
-q	不显示文件名信息
-v	显示文件名信息
# 参考实例
# 显示文件file的最后10行：
[root@admin~ ] tail file
# 显示文件file的内容，从第20行至文件末尾：
[root@admin~ ] tail +20 file
# 显示文件file的最后10个字符：
[root@admin~ ] tail -c 10 file
# 一直变化的文件总是显示后10行：
[root@admin~ ] tail -f 10 file
```

### *grep – 强大的文本搜索工具

> ​	grep是“global search regular expression and print out the line”的简称，意思是全面搜索正则表达式，并将其打印出来。这个命令可以结合正则表达式使用，它也是linux使用最为广泛的命令。grep命令的选项用于对搜索过程的补充，而其命令的模式十分灵活，可以是变量、字符串、正则表达式。需要注意的是：一当模式中包含了空格，务必要用双引号将其引起来。
>
> ​	linux系统支持三种形式的grep命令，大儿子就是grep，标准，模仿的代表。二儿子兴趣爱好多-egrep，简称扩展grep命令，其实和grep -E等价，支持基本和扩展的正则表达式。小儿子跑的最快-fgrep，简称快速grep命令，其实和grep -F等价，不支持正则表达式，按照字符串表面意思进行匹配。

语法格式：`grep [选项][查找内容][文件]`

```shell
# 常用参数
-i	搜索时，忽略大小写
-c	只输出匹配行的数量
-l	只列出符合匹配的文件名，不列出具体的匹配行
-n	列出所有的匹配行，显示行号
-h	查询多文件时不显示文件名
-s	不显示不存在、没有匹配文本的错误信息
-v	显示不包含匹配文本的所有行
-w	匹配整词
-x	匹配整行
-r	递归搜索
-q	禁止输出任何结果，已退出状态表示搜索是否成功
-b	打印匹配行距文件头部的偏移量，以字节为单位
-o	与-b结合使用，打印匹配的词据文件头部的偏移量，以字节为单位
# 参考实例
# 支持多文件查询并支持使用通配符：
[root@admin~]# grep zwx file_* /etc/hosts
file_1:zwx
file_1:zwxddkjflkdjfdlkfjlsdkj
# 输出匹配字符串行的数量：
[root@admin~]$ grep -c zwx file_*
file_1:2
file_2:1
file_3:0
# 列出所有的匹配行，并显示行号：
[root@admin~]# grep -n zwx file_*
file_1:1:zwx
file_1:10:zwxddkjflkdjfdlkfjlsdkj
file_2:2:zwx
file_4:4:zwx djfkdjf
file_4:5:zwxedkfgj
# 反向查找，显示文件名中包含file_的文件中不包含test的行：
[root@admin~]# grep-v test file_*
# 只列出符合匹配的文件名，不列出具体匹配的行：
[root@admin~]# grep -l zwx file_*
# 递归搜索，不仅搜索当前目录，还搜索子目录：
[root@admin~]# grep -r zwx file_2 *
# 匹配整词，以字面意思去解释他，相当于精确匹配：
[root@admin~]# grep zw* file_1
```

### *vim - 编辑文本

> ​	vi是Linux系统上的第一个全屏模式编辑器，它用法简单，而且 所占空间不大，操作灵活无比。vim 即 vi IMproved，在 vi 的基础上做了很多改进，如：增加了多级撤销、多窗口操作、崩溃后也可以恢复、增加了稳定性、关键字自动补全、上下文自动补全等功能。关于vim的详细内容，可以参考[这里](vim命令大全.md)

## 3. 系统管理

### 软件包管理器

​	大多数现代的类 Unix 操作系统都提供了一种中心化的机制用来搜索和安装软件。软件通常都是存放在存储库中，并通过包的形式进行分发。处理包的工作被称为包管理。包提供了操作系统的基本组件，以及共享的库、应用程序、服务和文档。关于包管理工具的详细内容，可以参考[这里](Linux 包管理基础.md)

### sudo - 获取管理员权限

> ​	Linux sudo命令以系统管理者的身份执行指令，也就是说，经由 sudo 所执行的指令就好像是 root 亲自执行。

### uname – 显示系统信息

​	uname命令的英文全称即“Unix name”。

​	用于显示系统相关信息，比如主机名、内核版本号、硬件架构等。

​	如果未指定任何选项，其效果相当于执行”uname -s”命令，即显示系统内核的名字。

语法格式：`uname [参数]`

```shell
# 常用参数
-a	显示系统所有相关信息
-m	显示计算机硬件架构
-n	显示主机名称
-r	显示内核发行版本号
-s	显示内核名称
-v	显示内核版本
-p	显示主机处理器类型
-o	显示操作系统名称
-i	显示硬件平台
参考实例
# 显示系统主机名、内核版本号、CPU类型等信息：
[root@admin~]# uname -a
Linux admin3.10.0-123.el7.x86_64 #1 SMP Mon May 5 11:16:57 EDT 2022 x86_64 x86_64 x86_64 GNU/Linux
```

### user*命令

#### useradd – 创建新用户
> ​	useradd命令 用于Linux中创建的新的系统用户。useradd可用来建立用户帐号。帐号建好之后，再用passwd设定帐号的密码．而可用userdel删除帐号。使用useradd指令所建立的帐号，实际上是保存在/etc/passwd文本文件中。
>
> ​	在Slackware中，adduser指令是个script程序，利用交谈的方式取得输入的用户帐号资料，然后再交由真正建立帐号的useradd命令建立新用户，如此可方便管理员建立用户帐号。在Red Hat Linux中， adduser命令 则是useradd命令的符号连接，两者实际上是同一个指令。

语法：`useradd [选项][参数]`

#### usermod – 修改用户的基本信息

> ​	usermod命令 用于修改用户的基本信息。usermod 命令不允许你改变正在线上的使用者帐号名称。当 usermod 命令用来改变user id，必须确认这名user没在电脑上执行任何程序。你需手动更改使用者的 crontab 档。也需手动更改使用者的 at 工作档。采用 NIS server 须在server上更动相关的NIS设定。

语法：`usermod [选项][参数]`

#### userdel – 删除给定的用户以及与用户相关的文件
> ​	userdel命令 用于删除给定的用户，以及与用户相关的文件。若不加选项，则仅删除用户帐号，而不删除相关文件。

语法：`userdel [选项][参数]`

选项

- -f	强制删除用户，即使用户当前已登录；
- -r	删除用户的同时，删除与用户相关的所有文件。

​	请不要轻易用-r选项；他会删除用户的同时删除用户所有的文件和目录，切记如果用户目录下有重要的文件，在删除前请备份。其实也有最简单的办法，但这种办法有点不安全，也就是直接在/etc/passwd中删除您想要删除用户的记录；但最好不要这样做，/etc/passwd是极为重要的文件，可能您一不小心会操作失误。

#### usernetctl – 被允许时操作指定的网络接口

> usernetctl命令在用于被允许时操作指定的网络接口。在使用上和”ifup”、”ifon”命令有些类似。

语法格式：`usernetctl [参数] [网络接口]`

常用参数：

- up	激活网络接口
- down	禁用网络接口
- report	报告网络接口状态


### ch*命令
#### chage – 修改帐号和密码的有效期限
语法：`chage [选项][参数] 用户名`

```shell
# 常用参数
-m	密码可更改的最小天数。为零时代表任何时候都可以更改密码。
-M	密码保持有效的最大天数。
-w	用户密码到期前，提前收到警告信息的天数。
-E	帐号到期的日期。过了这天，此帐号将不可用。
-d	上一次更改的日期。
-i	停滞时期。如果一个密码已过期这些天，那么此帐号将不可用。
-l	例出当前的设置。由非特权用户来确定他们的密码或帐号何时过期。
```

> 可以编辑/etc/login.defs来设定几个参数，以后设置口令默认就按照参数设定为准：
>
> ```shell
> PASS_MAX_DAYS   99999
> PASS_MIN_DAYS   0
> PASS_MIN_LEN    5
> PASS_WARN_AGE   7
> ```
>
> 通过修改配置文件，能对之后新建用户起作用，而目前系统已经存在的用户，则直接用chage来配置。

#### chattr – 改变文件属性
```shell
# 常用参数
a	让文件或目录仅供附加用途；
b	不更新文件或目录的最后存取时间；
c	将文件或目录压缩后存放；
d	将文件或目录排除在倾倒操作之外；
i	不得任意更动文件或目录；
s	保密性删除文件或目录；
S	即时更新文件或目录；
u	预防意外删除。
-R	递归处理，将指令目录下的所有文件及子目录一并处理；
-v<版本编号>	设置文件或目录版本；
-V	显示指令执行过程；
+<属性>	开启文件或目录的该项属性；
-<属性>	关闭文件或目录的该项属性；
=<属性>	指定文件或目录的该项属性。
# 参考实例
# 用chattr命令防止系统中某个关键文件被修改：
[root@admin~]# chattr +i /etc/fstab
# 然后试一下rm、mv、rename等命令操作于该文件，都是得到Operation not permitted的结果。
# 让某个文件只能往里面追加内容，不能删除，一些日志文件适用于这种操作：
[root@admin~]# chattr +a /data1/user_act.log
```

#### chown命令 – 变更文件或目录的拥有者或所属群组

> ​	chown命令 改变某个文件或目录的所有者和所属的组，该命令可以向某个用户授权，使该用户变成指定文件的所有者或者改变文件所属的组。用户可以是用户或者是用户D，用户组可以是组名或组id。文件名可以使由空格分开的文件列表，在文件名中可以包含通配符。
>
> ​	只有文件主和超级用户才可以便用该命令。

```shell
# 常用参数
-c或——changes	效果类似“-v”参数，但仅回报更改的部分；
-f或–quite或——silent	不显示错误信息；
-h或–no-dereference	只对符号连接的文件作修改，而不更改其他任何相关文件；
-R或——recursive	递归处理，将指定目录下的所有文件及子目录一并处理；
-v或——version	显示指令执行过程；
# 参考实例
# 将目录/usr/meng及其下面的所有文件、子目录的文件组改成 liu：
[root@admin~]# chown -R liu /usr/meng
# 将当前目录下所有文件的拥有者都改为 linuxcool，用户组改为 linuxcoolgroup:
[root@admin~]# chown -R linuxcool:linuxcoolgroup *
```


#### chgrp – 更改文件用户组

> chgrp是英语单词“change group”的缩写，命令的作用和其中文释义一样，为用于变更文件或目录的所属群组。

​	语法格式: `chgrp [参数] [目录]`

#### chmod命令 – 改变文件或目录权限

> ​	chmod命令的英文原意是“change the permissions mode of a file”，我们简称为“change mode”，意为用来改变文件或目录权限的命令，但是只有文件的属主和超级用户root才能执行这个命令。有两种模式，一种是采用权限字母和操作符表达式；另一种是采用数字。
>
> ​	Linux/Unix 的文件调用权限分为三级 : 文件所有者（Owner）、用户组（Group）、其它用户（Other Users）。分别用u, g, o表示，用a可以表示所有用户。

语法格式：`chmod [选项] ([ugoa...][[+-=][rwxX]...][,...]) [文件]`

文件具有三种权限：

- r 读取
- w 写入
- x 运行

这三个权限分别用一个二进制数表示，如权限为`rwx`，对应的二进制数就是`111`。

```shell
# 参考实例
# 将文件 file1.txt 设为所有人皆可读取 :
[root@admin~]# chmod a+r file1.txt
# 为 ex1.py 文件拥有者增加可执行权限:
[root@admin~]# chmod u+x ex1.py
# 此外chmod也可以用三个数字来表示权限，下面表示所有用户都拥有rwx权限
[root@admin~]# chmod 777 file
```



4.磁盘管理
4.1.df命令 – 显示磁盘空间使用情况
df命令的英文全称即“Disk Free”，顾名思义功能是用于显示系统上可使用的磁盘空间。默认显示单位为KB，建议使用“df -h”的参数组合，根据磁盘容量自动变换合适的单位，更利于阅读。

日常普遍用该命令可以查看磁盘被占用了多少空间、还剩多少空间等信息。

语法格式： df [参数] [指定文件]

常用参数：

参数名	解释
-a	显示所有系统文件
-B <块大小>	指定显示时的块大小
-h	以容易阅读的方式显示
-H	以1000字节为换算单位来显示
-i	显示索引字节信息
-k	指定块大小为1KB
-l	只显示本地文件系统
-t	<文件系统类型> 只显示指定类型的文件系统
-T	输出时显示文件系统类型
– -sync	在取得磁盘使用信息前，先执行sync命令
参考实例

显示磁盘分区使用情况：
[root@admin~]# df
文件系统                             1K-块    已用     可用   已用% 挂载点
devtmpfs                           1980612       0  1980612    0% /dev
tmpfs                              1994756       0  1994756    0% /dev/shm
tmpfs                              1994756    1040  1993716    1% /run
tmpfs                              1994756       0  1994756    0% /sys/fs/cgroup
/dev/mapper/fedora_linuxhell-root 15718400 2040836 13677564   13% /
tmpfs                              1994756       4  1994752    1% /tmp
/dev/sda1                           999320  128264   802244   14% /boot
tmpfs                               398948       0   398948   0% /run/user/0
1
2
3
4
5
6
7
8
9
以容易阅读的方式显示磁盘分区使用情况：
[root@admin~]# df -h
文件系统                           容量   已用   可用  已用% 挂载点
 devtmpfs                           1.9G     0  1.9G    0% /dev
 tmpfs                              2.0G     0  2.0G    0% /dev/shm
 tmpfs                              2.0G  1.1M  2.0G    1% /run
 tmpfs                              2.0G     0  2.0G    0% /sys/fs/cgroup
 /dev/mapper/fedora_linuxhell-root   15G  2.0G   14G   13% /
 tmpfs                              2.0G  4.0K  2.0G    1% /tmp
 /dev/sda1                          976M  126M  784M   14% /boot
 tmpfs                              390M     0  390M    0% /run/user/0
1
2
3
4
5
6
7
8
9
显示指定文件所在分区的磁盘使用情况：
[root@admin~]# df /etc/dhcp
文件系统                             1K-块    已用     可用   已用% 挂载点
/dev/mapper/fedora_linuxcool-root 15718400 2040836 13677564   13% /
显示文件类型为ext4的磁盘使用情况：

[root@admin~]# df -t ext4
文件系统        1K-块   已用   可用    已用% 挂载点
/dev/sda1      999320 128264 802244   14% /boot
1
2
3
4
5
6
7
4.2.lsblk命令 – 查看系统的磁盘
lsblk命令的英文是“list block”，即用于列出所有可用块设备的信息，而且还能显示他们之间的依赖关系，但是它不会列出RAM盘的信息。

lsblk命令包含在util-linux-ng包中，现在该包改名为util-linux。

语法格式：lsblk [参数]

常用参数：

参数名	解释
-a	显示所有设备
-b	以bytes方式显示设备大小
-d	不显示 slaves 或 holders
-D	print discard capabilities
-e	排除设备
-f	显示文件系统信息
-h	显示帮助信息
-i	use ascii characters only
-m	显示权限信息
-l	使用列表格式显示
-n	不显示标题
-o	输出列
-P	使用key=”value”格式显示
-r	使用原始格式显示
-t	显示拓扑结构信息
参考实例

lsblk命令默认情况下将以树状列出所有块设备：
[root@admin~ ]# lsblk
lsblk NAME   MAJ:MIN rm  SIZE RO type mountpoint
sda      8:0    0 232.9G  0 disk 
├─sda1   8:1    0  46.6G  0 part / 
├─sda2   8:2    0     1K  0 part  
├─sda5   8:5    0   190M  0 part /boot 
├─sda6   8:6    0   3.7G  0 part [SWAP] 
├─sda7   8:7    0  93.1G  0 part /data 
└─sda8   8:8    0  89.2G  0 part /personal 
sr0     11:0    1  1024M  0 rom
1
2
3
4
5
6
7
8
9
默认选项不会列出所有空设备：
[root@admin~]# lsblk -a
也可以用于列出一个特定设备的拥有关系，同时也可以列出组和模式：
[root@admin~]# lsblk -m
要获取SCSI设备的列表，你只能使用-S选项，该选项是用来以颠倒的顺序打印依赖的：
[root@admin~]# lsblk -S
例如，你也许想要以列表格式列出设备，而不是默认的树状格式。可以将两个不同的选项组合，以获得期望的输出：
[root@admin~]# lsblk -nl
5.文件传输
5.1.tftp命令 – 上传及下载文件
tftp命令用于传输文件。ftp让用户得以下载存放于远端主机的文件，也能将文件上传到远端主机放置。

tftp是简单的文字模式ftp程序，它所使用的指令和ftp类似。

语法格式：tftp [参数]

常用参数：

参数名	解释
connect	连接到远程tftp服务器
mode	文件传输模式
put	上传文件
get	下载文件
quit	退出
verbose	显示详细的处理信息
trace	显示包路径
status	显示当前状态信息
binary	二进制传输模式
ascii ascii	传送模式
rexmt	设置包传输的超时时间
timeout	设置重传的超时时间
help	帮助信息
?	帮助信息
参考实例

连接远程服务器”218.28.188.288″：
[root@linuxcool ~]# tftp 218.28.188.288
远程下载file文件：
tftp> get file                            
getting from 218.28.188.288 to /dir  
Recived 168236 bytes in 1.5 seconds[112157 bit/s] 
1
2
3
退出tftp：
tftp> quit
5.2.curl命令 – 文件传输工具
curl命令是一个利用URL规则在shell终端命令行下工作的文件传输工具；它支持文件的上传和下载，所以是综合传输工具，但按传统，习惯称curl为下载工具。

作为一款强力工具，curl支持包括HTTP、HTTPS、ftp等众多协议，还支持POST、cookies、认证、从指定偏移处下载部分文件、用户代理字符串、限速、文件大小、进度条等特征；做网页处理流程和数据检索自动化。

语法格式：curl [参数] [网址]

常用参数：

参数名	解释
-O	把输出写到该文件中，保留远程文件的文件名
-u	通过服务端配置的用户名和密码授权访问
参考实例

将下载的数据写入到文件，必须使用文件的绝对地址：
[root@admin~]# curl https://www.linuxcool.com/abc.txt --silent -O
访问需要授权的页面时，可通过-u选项提供用户名和密码进行授权：
[root@admin~]# curl -u root https://www.linuxprobe.com/
 Enter host password for user 'root':
1
5.3.ftpwho命令 – 显示ftp会话信息
tpwho命令用于显示当前所有以FTP登入的用户会话信息。

执行该命令可得知当前用FTP登入系统的用户有哪些人，以及他们正在进行的操作。

语法格式：ftpwho [参数]

常用参数：

参数名	解释
-h	显示帮助信息
-v	详细模式，输出更多信息
参考实例

查询当前正在登录FTP 服务器的用户：
[root@admin~]# ftpwho
在详细模式下，查询当前正在登录FTP 服务器的用户：
[root@admin~]# ftpwho -v
显示帮助信息：
[root@admin~]# ftpwho -h
6.网络通信
6.1.ssh命令 – 安全连接客户端
语法格式: ssh [参数] [远程主机]

常用参数：

参数名	解释
-1	强制使用ssh协议版本1
-2	强制使用ssh协议版本2
-4	强制使用IPv4地址
-6	强制使用IPv6地址
-A	开启认证代理连接转发功能
-a	关闭认证代理连接转发功能
-b<IP地址>	使用本机指定的地址作为对位连接的源IP地址
-C	请求压缩所有数据
-F<配置文件>	指定ssh指令的配置文件，默认的配置文件为“/etc/ssh/ssh_config”
-f	后台执行ssh指令
-g	允许远程主机连接本机的转发端口
-i<身份文件>	指定身份文件（即私钥文件）
-l<登录名>	指定连接远程服务器的登录用户名
-N	不执行远程指令
-o<选项>	指定配置选项
-p<端口>	指定远程服务器上的端口
-q 静默模式，	所有的警告和诊断信息被禁止输出
-X	开启X11转发功能
-x	关闭X11转发功能
-y	开启信任X11转发功能
参考实例

登录远程服务器：
[root@linuxcool ~]# ssh 202.102.240.88
用test用户连接远程服务器：
[root@linuxcool ~]# ssh -l test 202.102.220.88
查看分区列表：
[root@linuxcool ~]# ssh 202.102.220.88 /sbin/fdisk -l
强制使用ssh协议版本1：
[root@linuxcool ~]# ssh -1
开启认证代理连接转发功能：
[root@linuxcool ~]# ssh -A
6.2.netstat命令 – 显示网络状态
netstat 命令用于显示各种网络相关信息，如网络连接，路由表，接口状态 (Interface Statistics)，masquerade 连接，多播成员 (Multicast Memberships) 等等。

从整体上看，netstat的输出结果可以分为两个部分：一个是Active Internet connections，称为有源TCP连接，其中”Recv-Q”和”Send-Q”指%0A的是接收队列和发送队列。这些数字一般都应该是0。如果不是则表示软件包正在队列中堆积。这种情况只能在非常少的情况见到；另一个是Active UNIX domain sockets，称为有源Unix域套接口(和网络套接字一样，但是只能用于本机通信，性能可以提高一倍)。

语法格式：netstat [参数]

常用参数：

参数名	解释
-a	显示所有连线中的Socket
-p	显示正在使用Socket的程序识别码和程序名称
-u	显示UDP传输协议的连线状况
-i	显示网络界面信息表单
-n	直接使用IP地址，不通过域名服务器
参考实例

显示详细的网络状况：
[root@linuxcool ~]# netstat -a
显示当前户籍UDP连接状况：
[root@linuxcool ~]# netstat -nu
显示UDP端口号的使用情况：
[root@linuxcool ~]# netstat -apu
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address  Foreign Address  State  PID/Program name    
udp        0      0 0.0.0.0:bootpc          0.0.0.0:*      4000/dhclient       
udp        0      0 localhost:323           0.0.0.0:*      3725/chronyd        
udp6       0      0 localhost:323           [::]:*         3725/chronyd 
1
2
3
4
5
显示网卡列表：
[root@linuxcool ~]# netstat -i
Kernel Interface table 
Iface MTU Met  RX-OK  RX-ERR  RX-DRP RX-OVR  TX-OK TX-ERR TX-DRP TX-OVR Flg 
eth0 1500   0  181864   0      0       0     141278   0     0     0    BMRU 
lo   16436  0   3362    0      0       0     3362     0     0     0    LRU
1
2
3
4
显示组播组的关系：
[root@linuxcool ~]# netstat -g
IPv6/IPv4 Group Memberships Interface    
RefCnt Group 
--------------- ------ ---------------------
lo        1   ALL-SYSTEMS.MCAST.NET 
eth0      1   ALL-SYSTEMS.MCAST.NET lo       1   ff02::1 
eth0      1   ff02::1:ff0a:b0c eth0          1   ff02::1
1
2
3
4
5
6
6.3.hostnam命令 — 查看主机名称
查看主机名称
[root@localhosthome]# hostname

临时修改主机名（重启后失效）
[root@localhosthome]# hostname mytest

永久性修改主机名
配置文件路径/etc/sysconfig/network
[root@localhosthome]# vi network

6.4.ping命令
ping命令一般用于检测网络通与不通或者网络连接速度的命令，也叫时延，其 值越大，速度越慢

Linux系统下的ping命令与Windows系统下的ping命令稍有不同。Windows 下运行ping命令一般会发出4个请求就结束运行该命令。
而Linux下不会自动终止，此时需要我们按CTR+C终止
常用参数：

参数名	解释
-d	使用Socket的SO_DEBUG功能
-c	指定发送报文的次数
-i	指定收发信息的间隔时间
-I	使用指定的网络接口送出数据包
-l	设置在送出要求信息之前，先行发出的数据包
-n	只输出数值
-p	设置填满数据包的范本样式
-q	不显示指令执行过程
-R	记录路由过程
-s	设置数据包的大小
-t	设置存活数值TTL的大小
-v	详细显示指令的执行过程
示例
查看本机的连通性
[root@localhosthome]# ping 127.0.0.1
查看百度是否连通
[root@localhosthome]# ping www.baidu.com
连续ping4次查看
[root@localhosthome]# ping -c 4 www.baidu.com

7.设备管理
7.1.sensors命令 – 检测服务器内部温度及电压
sensors命令用于检测服务器内部降温系统是否健康，可以监控主板，CPU的工作电压，风扇转速、温度等数据 。

语法格式：sensors

参考实例

检测cpu工作电压，温度等：

[root@linuxcool ~]# sensors 
coretemp-isa-0000 
Core 0:      +48.0°C  (high = +87.0°C, crit = +97.0°C)   
Core 1:      +46.0°C  (high = +87.0°C, crit = +97.0°C)   
Core 2:      +47.0°C  (high = +87.0°C, crit = +97.0°C)   
Core 3:      +46.0°C  (high = +87.0°C, crit = +97.0°C) 
1
2
3
4
5
6
7.2.lspci命令 – 显示当前设备所有PCI总线信息
lspci命令用于显示当前主机的所有PCI总线信息，以及所有已连接的PCI设备信息。 现在主流设备如网卡储存等都采用PCI总线

语法格式：lspci [参数]

常用参数：

参数名	解释
-n	以数字方式显示PCI厂商和设备代码
-t	以树状结构显示PCI设备的层次关系
-b	以总线为中心的视图
-s	仅显示指定总线插槽的设备和功能块信息
-i	指定PCI编号列表文件，不使用默认文件
-m	以机器可读方式显示PCI设备信息
参考实例

显示当前主机的所有PCI总线信息：
[root@linuxcool ~]# lspci
以树状结构显示PCI设备的层次关系：
[root@linuxcool ~]# lspci -t
8.备份压缩
8.1.zip命令 – 压缩文件
zip程序将一个或多个压缩文件与有关文件的信息(名称、路径、日期、上次修改的时间、保护和检查信息以验证文件完整性)一起放入一个压缩存档中。可以使用一个命令将整个目录结构打包到zip存档中。

对于文本文件来说，压缩比为2：1和3：1是常见的。zip只有一种压缩方法(通缩)，并且可以在不压缩的情况下存储文件。(如果添加了bzip 2支持，zip也可以使用bzip 2压缩，但这些条目需要一个合理的现代解压缩来解压缩。当选择bzip 2压缩时，它将通货紧缩替换为默认方法。)zip会自动为每个要压缩的文件选择更好的两个文件(通缩或存储，如果选择bzip2，则选择bzip2或Store)。

语法格式： zip [参数] [文件]

常用参数：

参数名	解释
-q	不显示指令执行过程
-r	递归处理，将指定目录下的所有文件和子目录一并处理
-z	替压缩文件加上注释
-v	显示指令执行过程或显示版本信息
-n<字尾字符串>	不压缩具有特定字尾字符串的文件
参考实例

将 /home/html/ 这个目录下所有文件和文件夹打包为当前目录下的 html.zip：
[root@admin~]# zip -q -r html.zip /home/html
压缩文件 cp.zip 中删除文件 a.c：
[root@admin~]# zip -dv cp.zip a.c
把/home目录下面的mydata目录压缩为mydata.zip：
[root@admin~]# zip -r mydata.zip mydata
把/home目录下面的abc文件夹和123.txt压缩成为abc123.zip：
[root@admin~]# zip -r abc123.zip abc 123.txt
将 logs目录打包成 log.zip：
[root@admin~]# zip -r log.zip ./logs
8.2.unzip命令 – 解压缩zip文件
unzip命令是用于.zip格式文件的解压缩工具 ，unzip命令将列出、测试或从zip格式存档中提取文件，这些文件通常位于MS-DOS系统上。

默认行为（就是没有选项）是从指定的ZIP存档中提取所有的文件到当前目录（及其下面的子目录）。一个配套程序zip（1L）创建ZIP存档；这两个程序都与PKWARE的PKZIP和PKUNZIP为MS-DOS创建的存档文件兼容，但许多情况下，程序选项或默认行为是不同的。

语法格式：unzip [参数] [文件]

常用参数：

参数名	解释
-l	显示压缩文件内所包含的文件
-v	执行时显示详细的信息
-c	将解压缩的结果显示到屏幕上，并对字符做适当的转换
-n	解压缩时不要覆盖原有的文件
-j	不处理压缩文件中原有的目录路径
参考实例

把/home目录下面的mydata.zip解压到mydatabak目录里面：
[root@admin~]# unzip mydata.zip -d mydatabak
把/home目录下面的wwwroot.zip直接解压到/home目录里面：
[root@admin~]# unzip wwwroot.zip
把/home目录下面的abc12.zip、abc23.zip、abc34.zip同时解压到/home目录里面：
[root@admin~]# unzip abc\*.zip
查看把/home目录下面的wwwroot.zip里面的内容：
[root@admin~]# unzip -v wwwroot.zip
验证/home目录下面的wwwroot.zip是否完整：
[root@admin~]# unzip -t wwwroot.zip
8.3.gzip命令 – 压缩和解压文件
gzip命令的英文是“GNUzip”，是常用来压缩文件的工具，gzip是个使用广泛的压缩程序，文件经它压缩过后，其名称后面会多处“.gz”扩展名。

gzip是在Linux系统中经常使用的一个对文件进行压缩和解压缩的命令，既方便又好用。gzip不仅可以用来压缩大的、较少使用的文件以节省磁盘空间，还可以和tar命令一起构成Linux操作系统中比较流行的压缩文件格式。据统计，gzip命令对文本文件有60%～70%的压缩率。减少文件大小有两个明显的好处，一是可以减少存储空间，二是通过网络传输文件时，可以减少传输的时间。

语法格式：gzip [参数]

常用参数：

参数名	解释
-a	使用ASCII文字模式
-d	解开压缩文件
-f	强行压缩文件
-l	列出压缩文件的相关信息
-c	把压缩后的文件输出到标准输出设备，不去更动原始文件
-r	递归处理，将指定目录下的所有文件及子目录一并处理
-q	不显示警告信息
-num	（1-9压缩比例越来越高）-1或–fas指定的数字num调整压缩的速度t表示最快压缩方法（低压缩比），-9或–best表示最慢压缩方法（高压缩比）
参考实例

把rancher-v2.2.0目录下的每个文件压缩成.gz文件：
[root@rancher-v2.2.0~]# gzip *
把上例中每个压缩的文件解压，并列出详细的信息：
[root@rancher-v2.2.0~]# gzip -dv *
递归地解压目录：
[root@rancher-v2.2.0~]# gzip -dr rancher.gz
解压文件，并列出详细信息
[root@rancher-v2.2.0]# gzip -l *
压缩比例(1-9)
[root@rancher-v2.2.0]# gzip -1 b.txt
8.4.zipinfo命令 – 查看压缩文件信息
zipinfo命令的全称为“zip information”，该命令用于列出压缩文件信息。执行zipinfo指令可得知zip压缩文件的详细信息。

语法格式：zipinfo [参数]

常用参数：

参数名	解释
-1	只列出文件名称
-2	此参数的效果和指定”-1″参数类似，但可搭配”-h”,”-t”和”-z”参数使用
-h	只列出压缩文件的文件名称
-l	此参数的效果和指定”-m”参数类似，但会列出原始文件的大小而非每个文件的压缩率
-m	此参数的效果和指定”-s”参数类似，但多会列出每个文件的压缩率
-M	若信息内容超过一个画面，则采用类似more指令的方式列出信息
-s	用类似执行”ls -l”指令的效果列出压缩文件内容
-t	只列出压缩文件内所包含的文件数目，压缩前后的文件大小及压缩率
-T	将压缩文件内每个文件的日期时间用年，月，日，时，分，秒的顺序列出
-v	详细显示压缩文件内每一个文件的信息
-x<范本样式>	不列出符合条件的文件的信息
-z	如果压缩文件内含有注释，就将注释显示出来
参考实例

显示压缩文件信息：
[root@admin~]# zipinfo file.zip
Archive: file.zip 486 bytes 3 files
-rw-r–r-- 2.3 unx 0 bx stor 24-May-10 18:54 a.c
-rw-r–r-- 2.3 unx 0 bx stor 24-May-10 18:54 b.c
-rw-r–r-- 2.3 unx 0 bx stor 24-May-10 18:54 c.c
3 files, 0 bytes uncompressed, 0 bytes compressed: 0.0%
显示压缩文件中每个文件的信息：
[root@admin~]# zipinfo -v file.zip
只显示压缩包大小、文件数目：
[root@admin~]# zipinfo -h file.zip
Archive: file.zip
Zip file size: 907 bytes, number of entries: 3
生成一个基本的、长格式的列表(而不是冗长的)，包括标题和总计行：
[root@linuxcool ~]# zipinfo -l file
查看存档中最近修改的文件：
[root@linuxcool ~]# zipinfo -T file | sort –nr -k 7 | sed 15q
8.5.gunzip命令 – 解压文件
gunzip命令用于解压文件，它是个使用广泛的解压缩程序，它用于解开被gzip压缩过的文件，这些压缩文件预设最后的扩展名为”.gz”。事实上gunzip就是gzip的硬连接，因此不论是压缩或解压缩，都可通过gzip命令单独完成。

语法格式：gunzip [参数]

常用参数：

参数名	解释
-a或–ascii	使用ASCII文字模式
-c或–stdout或–to-stdout	把解压后的文件输出到标准输出设备
-f或-force	强行解开压缩文件，不理会文件名称或硬连接是否存在以及该文件是否为符号连接
-h或–help	在线帮助。
-l或–list	列出压缩文件的相关信息
-L或–license	显示版本与版权信息
-n或–no-name	解压缩时，若压缩文件内含有远来的文件名称及时间戳记，则将其忽略不予处理
-N或–name	解压缩时， 若压缩文件内含有原来的文件名称及时间戳记，则将其回存到解开的文件上
-q或–quiet 不	显示警告信息
-r或–recursive	递归处理，将指定目录下的所有文件及子目录一并处理
-S<压缩字尾字符串>或–suffix<压缩字尾字符串>	更改压缩字尾字符串
-t或–test	测试压缩文件是否正确无误
-v或–verbose	显示指令执行过程
-V或–version	显示版本信息
参考实例

解压文件：
[root@admin~]# gunzip ab.gz
解压，显示详细执行过程：
[root@admin~]# gunzip -v 1.gz
指定后缀名解压：
[root@admin~]# gunzip -v -S "mygz" 1.mygz
8.6.tar命令–
将许多文件一起保存至一个单独的磁带或磁盘归档，并能从归档中单独还原所需文件

tar [选项…] [FILE]…

参数名	解释
-c或–create	建立新的备份文件
-t或–list	列出备份文件的内容
-x或–extract或–get	从备份文件中还原文件
-z或–gzip或–ungzip	通过gzip指令处理备份文件
-v或–verbose	显示指令执行过程
-f<备份文件>或–file=<备份文件>	指定备份文件
压缩 a.c文件为test.tar.gz
[root@admin~]# tar -czvf test.tar.gz a.c
列出压缩文件内容
[root@admin~]# tar -tzvf test.tar.gz
解压文件
[root@admin~]# tar -xzvf test.tar.gz
从文件 foo 和 bar 创建归档文件 archive.tar
[root@admin~]# tar -cf archive.tar foo bar
详细列举归档文件 archive.tar 中的所有文件
[root@admin~]# tar -tvf archive.tar
展开归档文件 archive.tar 中的所有文件
[root@admin~]# tar -xf archive.tar
将所有.jpg的文件打成一个名为all.tar的包。-c是表示产生新的包，-f指定包的文件名
[root@admin~]# tar -cf all.tar *.jpg
将所有.gif的文件增加到all.tar的包里面去。-r是表示增加文件的意思
[root@admin~]# tar -rf all.tar *.gif
更新原来tar包all.tar中logo.gif文件，-u是表示更新文件的意思
[root@admin~]# tar -uf all.tar logo.gif
列出all.tar包中所有文件，-t是列出文件的意思
[root@admin~]# tar -tf all.tar
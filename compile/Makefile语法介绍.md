# 一、初识makefile

想要掌握makefile，首先需要了解两个概念，⼀个是⽬标（target），另⼀个就是依赖（dependency）。⽬标就是指要⼲什么，或说运⾏ make 后⽣成什么，⽽依赖是告诉 make 如何去做以实现⽬标。在 Makefile 中，⽬标和依赖是通过规则（rule）来表达的。

![image-20211029220036919](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000022605-2135340936.png)

![image-20211029220137655](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000023603-834191796.png)

![image-20211029220150645](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000023890-9102343.png)

## （一）、目标

### 首次编写makefile

```makefile
all:
	echo "Hello world"
```

> 上面Makefile 中的 all 就是我们 的⽬标，⽬标放在‘：’的前⾯，其名字可以是由字⺟和下划线‘_’组成 。echo “Hello World”就是⽣成⽬标的命令，这些命令可以是任何你可以在你的环境中运⾏的命令以及 make 所定义的函数等等。all  ⽬标的定义，其实是定义了如何⽣成 all ⽬标，这我们也称之为规则.

### makefile定义多个目标

```makefile
all:
	echo "Hello world"

test:
	echo "test game"
```

下面是运行的结果

![image-20211030002018306](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000024146-1565670982.png)

由此可见，⼀个 Makefile 中可以定义多个⽬标。调⽤ make  命令时，我们得告诉它我们的⽬标是什么，即要它⼲什么。当没有指明具体的⽬标是什么 时，那么 make 以 Makefile  ⽂件中定义的第⼀个⽬标作为这次运⾏的⽬标。这“第⼀个”⽬标也称之 为默认⽬标（和是不是all没有关系）。当 make  得到⽬标后，先找到定义⽬标的规则，然后运⾏规则中的命令来达到构建⽬标的⽬的。

### makefile中取消多余的命令行显示

在上面的指令中，多了很多的`echo "......"`的内容，这部分不是我们所期望的，如果要去掉，需要对上面的makefile进行一个改动，也就是在命令前加上一个@，这个符号就是告诉make，在运行的时候这一行命令不显示出来。

```makefile
all:
	@echo "Hello world"

test:
	@echo "test game"
```

运行结果：

![image-20211030002918136](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000025461-118180525.png)

紧接着对makefile进行如下的改动，在all的后面加上test

```makefile
all: test
	@echo "Hello world"

test:
	@echo "test game"
```

运行结果如下：

![image-20211030003241775](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000025708-1327539657.png)

如上图所示，此时test也被构建了。

## （二）、依赖

如上面的makefile，all ⽬标后⾯的 test 是告诉 make，all ⽬标依赖 test ⽬标，这⼀依赖⽬标在  Makefile 中⼜被称之为先决条件。出现这种⽬标依赖关系时，make⼯具会按 从左到右的先后顺序先构建规则中所依赖的每⼀个⽬标。如果希望构建 all ⽬标，那么make 会在构建它之 前得先构建 test ⽬标，这就是为什么我们称之为先决条件的原因。

![image-20211030003512234](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000026388-348563007.png)

## （三）、规则

⼀个规则是由⽬标（targets）、先决条件（prerequisites）以及命令（commands）所组成的。
 需要指出的是，⽬标和先决条件之间表达的就是依赖关系（dependency），这种依赖关系指明在构建⽬标之前，必须保证先决条件先满⾜（或构建）。⽽先决条件可以是其它的⽬标，当先决条件是⽬标时，其必须先被构建出来。还有就是⼀个规则中⽬标可以有多个，当存在多个⽬标，且这⼀规则是 Makefile 中的第⼀个规则时，如果我们运⾏ make 命令不带任何⽬标，那么规则中的第⼀个⽬标将被视为是缺省⽬ 标。

规则的功能就是指明 make 什么时候以及如何来为我们重新创建⽬标，在 Hello  World 例⼦中，不论我们 在什么时候运⾏ make 命令（带⽬标或是不带⽬标），其都会在终端上打印出信息来，和我们采⽤ make  进⾏代码编译时的表现好象有些不同。当采⽤ Makefile 来编译程序时，如果两次编译之间没有任何代码 的改动，理论上说来，我们是不希望看到  make 会有什么动作的，只需说“⽬标是最新的”，⽽我们的最终 ⽬标也是希望构建出⼀个“聪明的” Makefile 的。与 Hello  World 相⽐不同的是，采⽤ Makefile 来进⾏ 代码编译时，Makefile 中所存在的先决条件都是具体的程序⽂件，后⾯我们会看到。

![image-20211030004117962](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000027440-1529462979.png)

规则语法：

```python-repl
targets : prerequisites
	command
...
```

如上， all 是⽬标，test 是 all ⽬标的依赖⽬标，⽽@echo “Hello World”则是⽤于⽣成 all ⽬标的命令。

# 二、makefile的原理

foo.c

```c
#include <stdio.h> 

void foo()
{
    printf("this is foo() !\n");
}
```

main.c

```c
extern void foo();

int main()
{
    foo();
    return 0;
}
```

makefile

```makefile
all: main.o foo.o
	gcc -o simple main.o foo.o
main.o: main.c
	gcc -o main.o -c main.c
foo.o: foo.c
	gcc -o foo.o -c foo.c
clean:
	rm simple main.o foo.o
```

上面的三段代码生成的依赖树

![image-20211030102755319](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000028224-1221932858.png)

编译

![image-20211030113557482](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000028724-2111786433.png)

上面的展示了测试结果，注意到了第⼆次编译并没有构建⽬标⽂件的动作吗？但为什么有构建simple可执⾏程序的动作呢？为了明⽩为什么，我们需要了解 make 是如何决定哪些⽬标（这⾥是⽂件）是需要重新编译的。为什么 make会知道我们并没有改变 main.c 和 foo.c  呢？答案很简单，通过⽂件的时间戳！当 make 在运⾏⼀个规则时，我们前⾯已经提到  了⽬标和先决条件之间的依赖关系，make  在检查⼀个规则时，采⽤的⽅法是：如果先决条件中相关的⽂件的时间戳⼤于⽬标的时间戳，即先决条件中的⽂件⽐⽬标更新，则知道有变化，那么需要运⾏规则当中 的命令重新构建⽬标。这条规则会运⽤到所有与我们在 make时指定的⽬标的依赖树中的每⼀个规则。⽐如，对于 simple 项⽬，其依赖树中包括三个规则，make 会检查所有三个规则当中的⽬标（⽂件）与先决条件（⽂件）之间的时间先后关系，从⽽来决定是否要重新创建规则中的⽬标。

## 问题一：第二次构建的时候为什么simple会被重新构建？

是因为simple文件不存在，我们在这次构建的目标是all，而all在我们编译的过成中并不生成，所以第二次make的时候找不到，所以又重新编译了一遍。

修改makefile

```makefile
simple: main.o foo.o
	gcc -o simple main.o foo.o
main.o: main.c
	gcc -o main.o -c main.c
foo.o: foo.c
	gcc -o foo.o -c foo.c
clean:
	rm simple main.o foo.o
```

运行结果

![image-20211030115513134](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000030017-1208590176.png)

一个文件是否改变不是看这个文件的大小是否改变，而是看这个文件的时间戳是否发生了变化。可以直接使用touch指令对文件的时间戳进行修改。

![image-20211031103322031](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000030251-763623087.png)

这时候就会进行重新编译

## 假目标

![image-20211031103810024](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000031114-987188360.png)

如上图，如果我们的创建了一个clean文件之后，继续去运行make clean，这时候不是按照我们前面运行的make clean进行清理文件。

> 为什么出现上面的原因？
>
> 因为这个时候make 将clean当成是一个文件，并且在当前的目录下找到了这个文件，再加上clean目标没有任何的先决条件，这时候进行make clean时，系统会认为clean是最新的

如何解决上面的问题？使用假目标，假目标最从常用clean就是避免所定义的目标和的已经存在文件是从重名的情况，假⽬标可以采⽤.PHONY 关键字来定义，需要注意的是其必须是⼤写字⺟。使用假目标修改makefile

```makefile
.PHONY: clean
simple: main.o foo.o
	gcc -o simple main.o foo.o
main.o: main.c
	gcc -o main.o -c main.c
foo.o: foo.c
	gcc -o foo.o -c foo.c
clean:
	rm simple main.o foo.o
```

运行结果：

![image-20211031104536456](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000031348-142651969.png)

采⽤.PHONY 关键字声明⼀个⽬标后，make 并不会将其当作⼀个⽂件来处理，⽽只是当作⼀个概念上的⽬标。对于假⽬标，我们可以想像的是由于并不与⽂件关联，所以每⼀次 make 这个假⽬标时，其所在的规则中的命令都会被执⾏。

## 变量

先看代码

```makefile
.PHONY: clean
CC = gcc
RM = rm
EXE = simple
OBJS = main.o foo.o
$(EXE): $(OBJS)
	$(CC) -o $(EXE) $(OBJS)
main.o: main.c
	$(CC) -o main.o -c main.c
foo.o: foo.c
	$(CC) -o foo.o -c foo.c
clean:
	$(RM) $(EXE) $(OBJS)
```

运行结果：

![image-20211031105129588](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000031569-2145246152.png)

变量的使用可以提高makefile的可维护性。⼀个变量的定义很简单，就是⼀个名字（变量名）后⾯跟上⼀个等号，然后在等号的后⾯放这个变量所期望的值。对于变量的引⽤，则需要采⽤$(变量名)或者${变量名}这种模式。在这个 Makefile 中，我们引⼊了 CC 和 RM 两个变量，⼀个⽤于保存编译器名，⽽另⼀个⽤于指示删除⽂件的命令是什么。还有就是引⼊了 EXE 和 OBJS  两个变量，⼀个⽤于存放可执⾏⽂件名，可另⼀个则⽤于放置所有的⽬标⽂件名。采⽤变量的话，当我们需要更改编译器时，只需更改变量赋值的地⽅，⾮常⽅便，如果不采⽤变量，那我们得更改每⼀个使⽤编译器的地⽅，很是麻烦。

### 1.自动变量（☆☆☆☆☆）

对于每⼀个规则，⽬标和先决条件的名字会在规则的命令中多次出现，每⼀次出现都是⼀种麻烦，更为麻烦的是，如果改变了⽬标或是依赖的名，那得在命令中全部跟着改。有没有简化这种更改的⽅法呢？这我们需要⽤到 Makefile 中的⾃动变量，最常用包括：

- **$@⽤于表示⼀个规则中的⽬标。当我们的⼀个规则中有多个⽬标时，$@所指的是其中任何造成命令被运⾏的⽬标。**
- **$^则表示的是规则中的所有先择条件。**
- **$<表示的是规则中的第⼀个先决条件。**

```makefile
.PHONY:all
all:first second third
	@echo "\$$@ = $@"
	@echo "\$$^ = $^"
	@echo "\$$< = $<"
 
first second third:
```

运行结果

![image-20211031111256537](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000031780-1518828098.png)

需要注意的是，在 Makefile 中‘$’具有特殊的意思，因此，如果想采⽤ echo 输出‘$’，则必需⽤两个连着的‘$’。还有就是，$@对于 Shell 也有特殊的意思，我们需要在“$$@”之前再加⼀个脱字符‘\’。

修改simple的makefile

```makefile
.PHONY: clean
CC = gcc
RM = rm
EXE = simple
OBJS = main.o foo.o
$(EXE): $(OBJS)
	$(CC) -o $@ $^
main.o: main.c
	$(CC) -o $@ -c $^
foo.o: foo.c
	$(CC) -o $@ -c $^
clean:
	$(RM) $(EXE) $(OBJS)
```

运行结果：

![image-20211031111743327](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000032012-646671063.png)

### 2.特殊变量

#### （1）MAKE变量

它表示的是make 命令名是什么。当我们需要在 Makefile 中调⽤另⼀个 Makefile 时需要⽤到这个变量，采⽤这种⽅式，有利于写⼀个容易移植的 Makefile。

```makefile
.PHONY: clean
all:
	@echo "MAKE = $(MAKE)"
```

运行结果：

![image-20211031112410597](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000112670-23038614.png)

#### （2）MAKECMDGOALS变量

它表示的是当前⽤户所输⼊的 make ⽬标是什么。

```makefile
.PHONY: all clean
all clean:
	@echo "\$$@ = $@"
	@echo "MAKECMDGOALS = $(MAKECMDGOALS)"
```

运行结果：

![image-20211031113116499](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000112883-1251424230.png)

从测试结果看来，MAKECMDGOALS 指的是⽤户输⼊的⽬标，当我们只运⾏ make 命令时，虽然根据
 Makefile 的语法，第⼀个⽬标将成为缺省⽬标，即 all ⽬标，但 MAKECMDGOALS 仍然是空，⽽不是
 all，这⼀点我们需要注意。

### 3.递归扩展变量

示例了使⽤等号进⾏变量定义和赋值，对于这种只⽤⼀个“=”符号定义的变量，我们称之为递归扩展变量（recursively expanded variable）。

```makefile
.PHONY: all
foo = $(bar)
bar = $(ugh)
ugh = Huh?
all:
	@echo $(foo)
```

运行结果：

![image-20211031215623601](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000032200-754426973.png)

除了递归扩展变量还有⼀种变量称之为简单扩展变量（simply expanded variables），是⽤“:=”操作符
 来定义的。对于这种变量，make 只对其进⾏⼀次扫描和替换。

```makefile
.PHONY: all
x = foo
y = $(x) b
x = later
xx := foo
yy := $(xx) b
xx := later
all:
	@echo "x = $(y), xx = $(yy)"
```

运行结果：

![image-20211031220602172](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000032447-704687772.png)

另外还有一种条件赋值符“？=”，条件赋值的意思是当变量以前没有定义时，就定义它并且将左边的值赋值给它，如果已经定义了那么就不再改变其值。条件赋值类似于提供了给变量赋缺省值的功能。

```makefile

.PHONY: all

foo = x

foo ?= y

bar ?= y

all:

	@echo "foo = $(foo), bar = $(bar)"
```

运行结果：

![image-20211031221547312](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000033120-116168448.png)

此外，还有"+="操作符，对变量进⾏赋值的⽅法

```makefile

.PHONY: all

objects = main.o foo.o bar.o utils.o

objects += another.o

all:

	@echo $(objects)
```

运行结果

![image-20211031221910347](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000033407-686154736.png)

### 4.override指令

在设计 Makefile 时，我们并不希望⽤户将我们在 Makefile 中定义的某个变量覆盖掉，那就得⽤ override 指令了。

```makefile

.PHONY: all

override foo = x

all:

	@echo "foo = $(foo)"
```

运行结果：

![image-20211031224204469](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000036684-1779647461.png)

### 5.模式

如果对于每⼀个⽬标⽂件都得写⼀个不同的规则来描述，那会是⼀种“体⼒活”，太繁了！对于⼀个⼤型项⽬，就更不⽤说了。Makefile 中的模式就是⽤来解决我们的这种烦恼的。

```makefile

.PHONY: clean

CC = gcc

RM = rm

EXE = simple

OBJS = main.o foo.o

$(EXE): $(OBJS)

	$(CC) -o $@ $^

%.o: %.c

	$(CC) -o $@ -c $^

clean:

	$(RM) $(EXE) $(OBJS)
```

与 simple 项⽬前⼀版本的 Makefile 相⽐，最为直观的改变就是从⼆条构建⽬标⽂件的规则变成了⼀条。
 模式类似于我们在 Windows 操作系统中所使⽤的通配符，当然是⽤“%”⽽不是“*”。采⽤了模式以后，
 不论有多少个源⽂件要编译，我们都是应⽤同⼀个模式规则的，很显然，这⼤⼤的简化了我们的⼯作。使
 ⽤了模式规则以后，你同样可以⽤这个 Makefile 来编译或是清除 simple 项⽬，这与前⼀版本在功能上是
 完全⼀样的。

### 6.函数

函数是 Makefile 中的另⼀个利器，现在我们看⼀看采⽤函数如何来简化 simple 项⽬的 Makefile。对于
 simple 项⽬的 Makefile，尽管我们使⽤了模式规则，但还有⼀件⽐较恼⼈的事，我们得在这个Makefile
 中指明每⼀个需要被编译的源程序。对于⼀个源程序⽂件⽐较多的项⽬，如果每增加或是删除⼀个⽂件都
 得更新 Makefile，其⼯作量也不可⼩视！

采⽤了 wildcard 和 patsubst 两个函数后 simple 项⽬的 Makefile。可以先⽤它来编译⼀下 simple 项⽬代码以验证其功能性。需要注意的是函数的语法形式很是特别，对于我们来说只要记住其形式就⾏了。

```makefile

.PHONY: clean

CC = gcc

RM = rm

EXE = simple

SRCS = $(wildcard *.c)

OBJS = $(patsubst %.c,%.o,$(SRCS))

$(EXE): $(OBJS)

	$(CC) -o $@ $^

%.o: %.c

	$(CC) -o $@ -c $^

clean:

	$(RM) $(EXE) $(OBJS)
```

现在，我们来模拟增加⼀个源⽂件的情形，看⼀看如果我们增加⼀个⽂件，在 Makefile 不做任
 何更改的情况下其是否仍能正常的⼯作。增加⽂件的⽅式仍然是采⽤ touch 命令，通过 touch 命令
 ⽣成⼀个内容是空的 bar.c 源⽂件，然后再运⾏ make 和 make clean。

![image-20211031225011786](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000036920-1535665161.png)

#### （1）addprefix函数

addprefix 函数是⽤来在给字符串中的每个⼦串前加上⼀个前缀，其形式是：$(addprefix prefix, names...)

```makefile

.PHONY:all

without_dir= foo.c bar.c main.c

with_dir:=$(addprefix objs/, $(without_dir))

all:

	@echo $(with_dir)
```

运行结果

![image-20211031225531634](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000037994-1991433802.png)

#### （2）filter函数

filter 函数⽤于从⼀个字符串中，根据模式得到满⾜模式的字符串，其形式是：$(filter pattern..., text)

```makefile

.PHONY: all

sources = foo.c bar.c baz.s ugh.h

sources := $(filter %.c %.s, $(sources))

all:

	@echo $(sources)
```

运行结果

![image-20211031230050691](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000038204-1264582036.png)

从结果来看，经过 filter 函数的调⽤以后，source变量中只存在.c ⽂件和.s ⽂件了，⽽.h ⽂件则则被过滤掉了。

#### （3）filter-out函数

filter-out 函数⽤于从⼀个字符串中根据模式滤除⼀部分字符串，其形式是：$(filter-out pattern..., text)

```makefile

.PHONY: all

objects = main1.o foo.o main2.o bar.o

result := $(filter-out main%.o, $(objects))

all:

	@echo $(result)
```

![image-20211031230907135](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000039079-1472345464.png)

#### （4）patsubst函数（☆☆☆）

patsubst 函数是⽤来进⾏字符串替换的，其形式是：$(patsubst pattern, replacement, text)

```makefile

.PHONY:all

mixed=foo.c bar.c main.o

objects:=$(patsubst %.c, %.o, $(mixed))

all:

	@echo $(objects)
```

运行结果：

![image-20211102001806473](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000039367-281781999.png)

上述代码中 mixed 变量中包括了.c ⽂件也包括了.o ⽂件，采⽤patsubst 函数进⾏字符串替换时，我们希望将所有的.c ⽂件都替换成.o ⽂件。上图是最后的运⾏结果。

#### （5）strip

strip 函数⽤于去除变量中的多余的空格，其形式是：$(strip string)

```makefile

.PHONY:all

original=foo.c     bar.c 

stripped:=$(strip $(original))

all:

	@echo "original = $(original)"

	@echo "stripped = $(stripped)"
```

运行结果：

![image-20211102002359440](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000040891-816979638.png)

#### （6）wildcard函数（☆☆☆）

wildcard 是通配符函数，通过它可以得到我们所需的⽂件，这个函数类似我们在 Windows 或Linux 命
 令⾏中的“*”。其形式是：$(wildcard pattern)

```makefile

.PHONY:all

SRC=$(wildcard *.c)

all:

	@echo "SRC = $(SRC)"
```

运行结果：

![image-20211102002737336](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000052701-2086593125.png)

# 三、makefile拔高

## 1.创建目录

毫⽆疑问，我们在编译项⽬之前希望⽤于存放⽂件的⽬录先准备好，当然，我们可以在编译之前通过⼿动
 来创建所需的⽬录，但这⾥我们希望采⽤⾃动的⽅式。makefile的依赖树的样子是这样的。

![image-20211102003146882](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000055178-2053421022.png)

这个依赖图从概念上说来是对的，但从 Makefile 的实现上存在⼀些问题。我们说 all 是⼀个⽬标，如果 all 直接依赖 objs 和 exes ⽬录的话，那应该如何创建⽬录呢？首先写一个makefile【注意代码的最后一行不能换行，表示一个依赖】

```makefile
.PHONY:all
MKDIR=mkdir
DIRS=objs exes
all:$(DIRS)
```

运行结果

![image-20211102003647342](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000055442-748640116.png)

改进依赖关系图

![image-20211102003718353](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000057190-1145384725.png)

改进上面的makefile

```makefile
.PHONY:all
MKDIR=mkdir
DIRS=objs exes
all:$(DIRS)
$DIRS:
	$(MKDIR) $@
```

运行结果

![image-20211102004049078](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000059734-1242896205.png)

在这个 Makefile 中，需要注意的是 OBJS 变量即是⼀个依赖⽬标也是⼀个⽬录，在不同的场合其意思是不同的。⽐如，第⼀次 make 时，由于 objs 和 exes ⽬录都不存在，所以 all ⽬标将它们视作是⼀个先决条件或者说是依赖⽬标，接着 Makefile  先根据⽬录构建规则构建 objs 和 exes ⽬标，即Makefile  中的第⼆条规则就被派上了⽤场。构建⽬录时，第⼆条规则中的命令被执⾏，即真正的创建了 objs 和 exes ⽬录。当我们第⼆次进⾏ make  时，此时，make 仍以 objs 和 exes 为⽬标，但从⽬录构建规则中发现，这两个⽬标并没有依赖关系，⽽且能从当前⽬录中找到 objs 和 exes ⽬录，即认为 objs 和 exes ⽬标都是最新的，所以不⽤再运⾏⽬录构建规则中的命令来创建⽬录。

更新后代码的依赖树关系

![image-20211102004304855](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000059992-593750005.png)

接下来也得为 Makefile 创建⼀个 clean ⽬标，专⻔⽤来删除所⽣成的⽬标⽂件和可执⾏⽂件。加 clean 规则还是相当简单，需要再增加了两个变量，⼀个是RM，另⼀个则是 RMFLAGS。

```makefile
.PHONY:all
MKDIR=mkdir
DIRS=objs exes
RM=rm
RMFLAGS=-fr
all:$(DIRS)
$(DIRS):
	$(MKDIR) $@
clean:
	$(RM) $(RMFLAGS) $(DIRS)
```

运行结果

![image-20211102004603256](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000100267-632705051.png)

## 2.增加头文件

```makefile
.PHONY:all clean
MKDIR=mkdir
RM=rm
RMFLAGS=-fr
CC=gcc
EXE=test
DIRS=objs exes
SRCS=$(wildcard *.c)
OBJS=$(SRCS:.c=.o)
all:$(DIRS) $(EXE)
$(DIRS):
	$(MKDIR) $@
$(EXE):$(OBJS)
	$(CC) -o $@ $^
%.o:%.c
	$(CC) -o $@ -c $^
clean:
	$(RM) $(RMFLAGS) $(DIRS) $(EXE) $(OBJS)
```

运行结果

![image-20211102213222152](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000101532-1900562377.png)

## 3.将文件放进目录

为了将⽬标⽂件或是可执⾏程序分别放⼊所创建的 objs 和 exes ⽬录中，我们需要⽤到 Makefile中的⼀
 个函数 —— addprefix。对上面的makefile进行修改。

```makefile
.PHONY:all clean
MKDIR=mkdir
RM=rm
RMFLAGS=-fr
CC=gcc
 
DIR_OBJS=objs
DIR_EXE=exes
DIRS=$(DIR_OBJS) $(DIR_EXE)
EXE=test
EXE:=$(addprefix $(DIR_EXE)/, $(EXE))
SRCS=$(wildcard *.c)
OBJS=$(SRCS:.c=.o)
OBJS:=$(addprefix $(DIR_OBJS)/, $(OBJS))
 
all:$(DIRS) $(EXE)
 
$(DIRS):
	$(MKDIR) $@
$(EXE):$(OBJS)
	$(CC) -o $@ $^
$(DIR_OBJS)/%.o:%.c
	$(CC) -o $@ -c $^
clean:
	$(RM) $(RMFLAGS) $(DIRS)
```

运行结果

![image-20211102221508374](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000103133-1917774500.png)

最⼤的变化除了增加了对于 addprefix  函数的运⽤为每⼀个⽬标⽂件加上“objs/”前缀外，还有⼀个很⼤的变化是，我们需要在构建⽬标⽂件的模式规则中的⽬标前也加上“objs/”前缀，即增加“$(DIR_OBJS)/”前缀。之所以要加上，是因为规则的命令中的-o 选项需要以它作为⽬标⽂件的最终⽣成位置，还有就是因为 OBJS 也加上了前缀，⽽要使得 Makefile  中的⽬标创建规则被运⽤，也需要采⽤相类似的格式，即前⾯有“objs/”。此外，由于改动后的 Makefile 会将所有的⽬标⽂件放⼊ objs  ⽬录当中，⽽我们的 clean 规则中的命令包含将 objs ⽬录删除的操作，所以我们可以去除命令中对 OBJS 中⽂件的删除。这导致的改动就是 Makefile 中的最后⼀⾏中删除了$(OBJS)。同样的方法将 test 放入到 exes 文件夹中。

## 4.更复杂的依赖关系

假设我们对项⽬已经进⾏了⼀次编译（这⼀点⾮常重要，否则你看不到将要说的问题），接着对 foo.h⽂件进⾏了修改，如下

```cpp
// foo.h
#ifndef __FOO_H
#define __FOO_H
void foo(int value);
#endif
```

并不对 foo.c进⾏相应的更改。这样⼀改的话，由于声名与定义不相同，所以理论上编译时应当出错。【先不要进行make clean，直接进行make，否则可能出现的结果和我们预期的不一致】

运行结果

![image-20211102222519183](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000103615-1932935477.png)

通过运行结果可以看到，第一次make的时候并没有报错，但是如果我们事先做了make clean，这个报错信息又会出现，但是我们在正常的工程编译中不能不停的进行make clean，这样太费时。

分析一下第一次make出现异常的原因，下图是makefile所表达的依赖关系书以及规则的映射关系图。

![image-20211102222814990](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000104770-242029004.png)

从依赖关系图中可以发现，其中并没有出现对 foo.h 的依赖关系，这就是为什么我们改动头⽂件时，make ⽆法生效的原因！所以我们需要在构建目标的规则中增加对于foo.h的依赖。这里我们需要使用自动变量 $< 。这个变量与$的区别是，其只表示所有的先决条件中的第⼀个，⽽$则表示全部先决条件。之所以要⽤$<是因为，我们不希望将 foo.h 也作为⼀个⽂件让 gcc 去编译，这样的话会出错。

```makefile
.PHONY:all clean
MKDIR=mkdir
RM=rm
RMFLAGS=-fr
CC=gcc
 
DIR_OBJS=objs
DIR_EXE=exes
DIRS=$(DIR_OBJS) $(DIR_EXE)
EXE=test
EXE:=$(addprefix $(DIR_EXE)/, $(EXE))
SRCS=$(wildcard *.c)
OBJS=$(SRCS:.c=.o)
OBJS:=$(addprefix $(DIR_OBJS)/, $(OBJS))
 
all:$(DIRS) $(EXE)
 
$(DIRS):
	$(MKDIR) $@
$(EXE):$(OBJS)
	$(CC) -o $@ $^
$(DIR_OBJS)/%.o:%.c foo.h
	$(CC) -o $@ -c $<
clean:
	$(RM) $(RMFLAGS) $(DIRS)
```

现在，将 foo.h 改回以前的状态，即去除 foo ()函数中的 int 参数，然后编译，这次编译当然是成功的，
 接着再加⼊ int 参数，再编译。你发现这次真的能发现问题了！更改后的依赖关系图如下图

![image-20211102223430064](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000106218-30864882.png)

编译结果：![image-20211102223341607](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000107522-1301679363.png)

当项⽬复杂时，如果我们要将每⼀个头⽂件都写⼊到 Makefile 相对应的规则中，这将会是⼀个恶梦！所以我们需要找到另⼀种更好的⽅法。

如果有哪⼀个⼯具能帮助我们列出⼀个源程序所包含的头⽂件那就好了，这样的话我们或许可以在 make
 时，动态的⽣成⽂件的依赖关系。还真是存在这么⼀个⼯具！就是我们的编译器 ——gcc。下图列出了采⽤ gcc 的-M 选项和-MM 选项列出  foo.c 对其它⽂件的依赖关系的结果，从结果你可以看出它们会列出 foo.c 中直接或是间接包含的头⽂件。-MM 选项与-M  选项的区别是，-MM选项并不列出对于系统头⽂件的依赖关系，⽐如 stdio.h  就属于系统头⽂件。其道理是，绝⼤多数情况我们并不会改变系统的头⽂件，⽽只会对⾃⼰项⽬的头⽂件进⾏更改。

![image-20211102223757501](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000108766-220728449.png)

对于采⽤ gcc 的-MM 的选项所⽣成的结果，还有⼀个问题，因为我们⽣成的⽬标⽂件是放在 objs⽬录当
 中的，因此，我们希望依赖关系中也包含这⼀⽬录信息，否则，在我们的 Makefile 中，跟本没有办法做
 到将⽣成的⽬标⽂件放到 objs ⽬录中去，这在前⾯的 Makefile 中我们就是这么做的。在使⽤新的⽅法
 时，我们仍然需要实现同样的功能。这时，我们需要⽤到 sed ⼯具了，这是 Linux 中⾮常常⽤的⼀个字符
 串处理⼯具。下图是采⽤ sed 进⾏查找和替换以后的输出结果，从结果中我们可以看到，就是在 foo.o 之前加上了“objs/”前缀。对于 sed 的⽤法说明可能超出了本⽂的范围，如果你不熟悉其功能，可以找⼀些资料看⼀看。

```shell
gcc -MM foo.c | sed 's,\(.*\)\.o[ :]*,objs/\1.o: ,g'
```

执行结果

![image-20211102224023711](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000109107-871747191.png)

gcc 还有⼀个⾮常有⽤的选项是-E，这个命令告诉 gcc 只做预处理，⽽不进⾏程序编译，在⽣成依赖关
 系时，其实我们并不需要 gcc 去编译，只要进⾏预处理就⾏了。这可以避免在⽣成依赖关系时出现没有必
 要的 warning，以及提⾼依赖关系的⽣成效率。

现在，我们已经有了⾃动⽣成依赖关系的⽅法了，那如何将其整合到我们的 Makefile 中呢？显然，⾃动
 ⽣成的依赖信息，不可能直接出现在我们的 Makefile 中，因为我们不能动态的改变 Makefile中的内容，
 那采⽤什么⽅法呢？先别急，第⼀步我们能做的是，为每⼀个源⽂件通过采⽤ gcc 和 sed⽣成⼀个依赖关
 系⽂件，这些⽂件我们采⽤.dep 后缀结尾。从模块化的⻆度来说，我们不希望.dep⽂件与.o ⽂件或是可
 执⾏⽂件混放在⼀个⽬录中。为此，创建⼀个新的 deps ⽬录⽤于存放依赖⽂件似乎更为合理。

```makefile
.PHONY:all clean
MKDIR=mkdir
RM=rm
RMFLAGS=-fr
CC=gcc
 
DIR_OBJS=objs
DIR_EXE=exes
DIR_DEPS=deps
DIRS=$(DIR_OBJS) $(DIR_EXE) $(DIR_DEPS)
EXE=test
EXE:=$(addprefix $(DIR_EXE)/, $(EXE))
SRCS=$(wildcard *.c)
OBJS=$(SRCS:.c=.o)
OBJS:=$(addprefix $(DIR_OBJS)/, $(OBJS))
DEPS = $(SRCS:.c=.dep)
DEPS := $(addprefix $(DIR_DEPS)/, $(DEPS))
 
all:$(DIRS) $(DEPS) $(EXE)
 
$(DIRS):
	$(MKDIR) $@
$(EXE):$(OBJS)
	$(CC) -o $@ $^
$(DIR_OBJS)/%.o:%.c
	$(CC) -o $@ -c $^
$(DIR_DEPS)/%.dep: %.c
	@echo "Making $@ ..."
	@set -e; \
	$(RM) $(RMFLAGS) $@.tmp ; \
	$(CC) -E -MM $^ > $@.tmp ; \
	sed 's,\(.*\)\.o[ :]*,objs/\1.o: ,g' < $@.tmp > $@ ; \
	$(RM) $(RMFLAGS) $@.tmp
clean:
	$(RM) $(RMFLAGS) $(DIRS)
```

运行结果：

![image-20211102232427870](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000110778-176058585.png)

## 5.包含文件

现在依赖⽂件已经有了，那如何为我们的 Makefile 所⽤呢？这需要⽤到 Makefile 中的 include关键字，
 它如同 C/C++中的#include 预处理指令。现在要做的就是在 Makefile 中加⼊对所有依赖⽂件的包含功
 能，更改后的 Makefile 如下所示。

```makefile
.PHONY:all clean
MKDIR=mkdir
RM=rm
RMFLAGS=-fr
CC=gcc
 
DIR_OBJS=objs
DIR_EXE=exes
DIR_DEPS=deps
DIRS=$(DIR_OBJS) $(DIR_EXE) $(DIR_DEPS)
EXE=test
EXE:=$(addprefix $(DIR_EXE)/, $(EXE))
SRCS=$(wildcard *.c)
OBJS=$(SRCS:.c=.o)
OBJS:=$(addprefix $(DIR_OBJS)/, $(OBJS))
DEPS=$(SRCS:.c=.dep)
DEPS:=$(addprefix $(DIR_DEPS)/, $(DEPS))
 
all:$(DIRS) $(DEPS) $(EXE)
 
include $(DEPS)
 
$(DIRS):
	$(MKDIR) $@
$(EXE):$(OBJS)
	$(CC) -o $@ $^
$(DIR_OBJS)/%.o:%.c
	$(CC) -o $@ -c $^
$(DIR_DEPS)/%.dep: %.c
	@echo "Making $@ ..."
	@set -e; \
	$(RM) $(RMFLAGS) $@.tmp ; \
	$(CC) -E -MM $^ > $@.tmp ; \
	sed 's,\(.*\)\.o[ :]*,objs/\1.o: ,g' < $@.tmp > $@ ; \
	$(RM) $(RMFLAGS) $@.tmp
clean:
	$(RM) $(RMFLAGS) $(DIRS)
```

运行结果

![image-20211102232802917](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000111109-1997718689.png)

从上图可以看到，由于 make 在处理Makefile 的 include 命令时，发现找不到 deps/foo.dep 和 deps/main.dep，所以出错了。如何理解这⼀错误呢？从这⼀错误我们可知，make 对于 include 的处理是先于 all ⽬标的构建的，这样的话，由于依赖⽂件是在构建 all ⽬标时才创建的，所以很⾃然 make 在处理 include 指令时，是找不到依赖⽂件的。我们说第⼀次 make 的确没有依赖⽂件，所以 include 出错也是正常的，那能不能让 make忽略这⼀错误呢？可以的，在 Makefile 中，如果在 include 前加上⼀个‘-’号，当 make 处理这⼀包含指示时，如果⽂件不存在就会忽略这⼀错误。除此之外，需要对于 Makefile 中的 include 有更为深⼊的了解。当 make 看到 include  指令时，会先找⼀下有没有这个⽂件，如果有则读⼊。接着，make 还会看⼀看对于包含进来的⽂件，在 Makefile  中是否存在规则来更新它。如果存在，则运⾏规则去更新需被包含进来的⽂件，当更新完了之后再将其包含进来。在我们的这个 Makefile  中，的确存在⽤于创建（或更新）依赖⽂件的规则。那为什么 make 没有帮助我们去创建依赖⽂件，⽽只是抱怨呢？因为 make  想创建依赖⽂件时，deps ⽬录还没有创建，所以⽆法成功的构建依赖⽂件。
 有了这些信息之后，我们需要对 Makefile 的依赖关系进⾏调整，即将 deps ⽬录的创建放在构建依赖⽂
 件之前。其改动就是在依赖⽂件的创建规则当中增加对 deps ⽬录的信赖，且将其当作是第⼀个先决条
 件。采⽤同样的⽅法，我们将所有的⽬录创建都放到相应的规则中去。更改后的 Makefile如下所示。

```makefile
.PHONY:all clean
MKDIR=mkdir
RM=rm
RMFLAGS=-fr
CC=gcc
 
DIR_OBJS=objs
DIR_EXE=exes
DIR_DEPS=deps
DIRS=$(DIR_OBJS) $(DIR_EXE) $(DIR_DEPS)
EXE=test
EXE:=$(addprefix $(DIR_EXE)/, $(EXE))
SRCS=$(wildcard *.c)
OBJS=$(SRCS:.c=.o)
OBJS:=$(addprefix $(DIR_OBJS)/, $(OBJS))
DEPS=$(SRCS:.c=.dep)
DEPS:=$(addprefix $(DIR_DEPS)/, $(DEPS))
 
all: $(EXE)
 
-include $(DEPS)
 
$(DIRS):
	$(MKDIR) $@
$(EXE):$(DIR_EXE) $(OBJS)
	$(CC) -o $@ $(filter %.o, $^)
$(DIR_OBJS)/%.o:$(DIR_OBJS) %.c
	$(CC) -o $@ -c $(filter %.c, $^)
$(DIR_DEPS)/%.dep:$(DIR_DEPS) %.c
	@echo "Making $@ ..."
	@set -e; \
	$(RM) $(RMFLAGS) $@.tmp ; \
	$(CC) -E -MM $(filter %.c, $^) > $@.tmp ; \
	sed 's,\(.*\)\.o[ :]*,objs/\1.o: ,g' < $@.tmp > $@ ; \
	$(RM) $(RMFLAGS) $@.tmp
clean:
	$(RM) $(RMFLAGS) $(DIRS)
```

我们使⽤了 filter 函数将所依赖的⽬录从先决条件中去除，否则的话会出现错误。正如前⾯所提及的，当make 看到 include  指令时，会试图去构建所需包含进来的依赖⽂件，这样⼀来，我们并不需要让 all⽬录依赖依赖⽂件，也就是从 all 规则中去除了对 DEPS  的依赖。6.

## 6.条件语法

当 make 看到条件语法时将⽴即对其进⾏分析，这包括 ifdef、ifeq、ifndef 和 ifneq 四种语句形式。这
 也说明⾃动变量（参⻅ 1.5.1 节）在这些语句块中不能使⽤，因为⾃动变量的值是在命令处理阶段才被赋
 值的。如果⾮得⽤条件语法，那得使⽤ Shell 所提供的条件语法⽽不是 Makefile 的。Makefile  中的条件语法有三种形式，如下所示。其中的 conditional-directive 可以是 ifdef、ifeq、ifndef 和 ifneq 中的任意⼀个。

```makefile
conditional-directive
	text-if-true
endif
```

或

```makefile
conditional-directive
	text-if-true
else
	text-if-false
endif
```

或

```vhdl
conditional-directive
	text-if-one-is-true
else conditional-directive
	text-if-true
else
	text-if-false
endif
```

举例

```makefile
.PHONY: all
sharp = square
desk = square
table = circle
 
ifeq ($(sharp), $(desk))
	result1 = "desk == sharp"
endif
ifneq "$(table)" 'square'
	result2 = "table != square"
endif
all:
	@echo $(result1)
	@echo $(result2)
```

运行结果

![image-20211102235401870](https://img2020.cnblogs.com/blog/1369378/202111/1369378-20211103000111903-1574797968.png)

ifdef和ifndefg示例

```makefile
.PHONY: all
foo = defined
ifdef foo
	result1 = "foo is defined"
endif
ifndef bar
	result2 = "bar is not defined"
endif
all:
	@echo $(result1)
	@echo $(result2)
```

运行结果
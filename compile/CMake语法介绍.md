# CMake语法介绍

## PROJECT 指令

​	用来指定工程的名字和支持的语言，默认支持所有语言

- PROJECT（HELLO） 指定了工程的名字，并且支持所有语言（推荐）
- PROJECT（HELLO CXX） 指定了工程的名字，并且支持的语言是C++
- PROJECT（HELLO C CXX）指定了工程的名字。并且支持的语言是C和C++

​	该关键字隐式定义了两个CMAKE的变量

​	[projectname]_BINARY_DIR，本例中是HELLO_BINARY_DIR

​	[projectname]_SOURCE_DIR，本例中是HELLO_SOURCE_DIR

​	这两个变量现在都指向当前的工作目录

> [projectname]_BINARY_DIR与PROJECT_BINARY_DIR是等价的
>
> projectname可能改变，因此在指定目录时，应该使用PROJECT_BINARY_DIR

## SET

​	用来显式的指定变量

​	`SET(SRC_LIST main1.cpp main2.cpp)`

​	这样，SRC_LIST就包含了main1.cpp和main2.cpp。

## MESSAGE

​	向终端输出用户自定义的信息

​	主要包含三种信息

- SEND_ERROR，产生错误，生成过程被跳过
- STATUS，输出前缀为--的信息
- FATAL_ERROR，立即终止所有cmake过程

## ADD_EXECUTABLE

生成可执行文件

`ADD_EXECUTABLE(hello ${SRC_LIST})` 生成的可执行文件名是hello，源文件读取变量SRC_LIST中的内容也可以直接写`ADD_EXECUTABLE(hello main.cpp)`

> 最简单的CMakelist文件只需要两行：
>
> PROJECT(HELLO) // 指定工程名
>
> ADD_EXECUTABLE(hello main.cpp) // 编译main.cpp，生成可执行文件hello

## 语法基本原则

- 变量使用${}方式取值，但是在IF控制语句中是直接使用变量名
- `指令(参数1 参数2...)`参数使用括弧括起，参数之间使用空格或分号分开(当文件名具有空格时，必须加双引号表示这是一个文件名)
- 指令是大小写无关的，参数和变量区分大小写

> add_executable 可以不写后缀，它会自动寻找扩展名，这样有可能造成冲突

## 内部构建和外部构建

​	之前的例子就是内部构建，他生产的临时文件特别多，不方便清理。

​	外部构建，就会把生成的临时文件放在build目录下，不会对源文件有任何影响

### 外部构建方式

​	新建一个build文件夹，在这个文件夹下对项目进行构建

​	这样做之后，HELLO_SOURCE_DIR 是工程路径，而 HELLO_BINARY_DIR 是build文件夹的路径

## 让hello world 看起来像一个工程

- 为工程添加一个子目录src，用来放置工程的源代码
- 添加一个子目录doc，用来放置这个工程的文档
- 在工程目录添加文本文件，如copyright和readme
- 在工程目录添加一个runhello.sh脚本，用来调用hello二进制
- 将构建后的目标文件放入build目录的bin子目录
- 将doc目录的内用以及copyright/readme安装到/usr/share/doc/cmake/

### ADD_SUBDIRECTORY

`ADD_SUBDIRECTORY(source_dir [binary_dir] [exclude_form_all])`

- 这个指令用于向当前工程添加存放源文件的子目录，并可以指定中间二进制和目标二进制的存放位置。
- EXCLUDE_FORM_ALL函数是将写的目录从编译中排除，如程序中的example
- ADD_SUBDIRECTORY(src bin)：将src子目录加入工程并指定编译输出路径为bin目录，如果不指定bin目录，那么编译结果会存放在src目录下。

### 更改二进制的保存路径

SET指令重新定义EXECUTABLE_OUTPUT_PATH和LIBRAY_OUTPUT_PATH变量来指定最终目标二进制的位置

```cmake
SET(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/bin)
SET(LIBRARY_OUTPUT_PATH ${PROJECT_BINARY_DIR}/lib)
```

## 安装

- 一种是从代码编译后直接 make install 安装
- 一种是打包时指定目录安装
  - make install DESTDIR=/tmp/test
  - ./configure -prefix=/usr

​	安装后，在任何目录下输入程序名即可运行程序

```shell
[cdsoft_zihang@localhost Ctest]$ tree
.
├── build
├── CMakeLists.txt
├── copyright
├── doc
│   └── hello.txt
├── readme.md
├── runhello.sh
└── src
    ├── CMakeLists.txt
    └── main.cpp
```

### 如何安装

​	使用CMake指令：INSTALL

​	INSTAL的安装可以包括：二进制、动态库、静态库以及文件、目录、脚本等

`INSTALL(FILES COPYRIGHT README DESTINATION share/doc/cmake)`

FILES：文件

DESTINATION：可以写绝对路径和相对路径，写相对路径时，实际路径是：`${CMAKE_INSTALL_PREFIX}/<DESTINATION>`。CMAKE_INSTALL_PREFIX默认是在/usr/local。

`cmake -D CMAKE_INSTALL_PREFIX=/usr` 在cmake的时候指定CMAKE_INSTALL_PREFIX变量的值

### 安装脚本runhello.sh

PROGRAMS：非目标文件的可执行程序安装

### 安装doc中的hello.txt

​	有两种方式安装

​	一、通过在doc目录建立CMakeLists.txt，通过install下的file

​	二、直接在工程目录通过`INSTALL(DIRECTORY doc/ DESTINATION share/doc/cmake)`

## 静态库和动态库的构建

用ADD_LIBRARY指令构建库

### ADD_LIBRARY

`ADD_LIBRARY(hello SHARED ${LIBHELLO_SRC})`

- hello：生成的库名，生成的名字前会加上lib，并且根据生成的库类型自动加上后缀名(.a/.lib或.so/.dll)
- SHARED 动态库 STATIC 静态库
- ${LIBHELLO_SRC} 源文件

### 同时构建静态库和动态库

```cmake
# 以下方式不会构建一个动态库，不会构建静态库
ADD_LIBRARY(hello SHARED ${LIBHELLO_SRC})
ADD_LIBRARY(hello STATIC ${LIBHELLO_SRC})
# 修改生成的库的名字，可以生成静态库和动态库，但通常我们希望它们名字相同只是后缀不同
ADD_LIBRARY(hello SHARED ${LIBHELLO_SRC})
ADD_LIBRARY(hello_static STATIC ${LIBHELLO_SRC})
```

### SET_TARGET_PROPERTIES

​	这条指令可以用来设置输出的名称，对于动态库，还可以用来指定动态库版本和API版本。

```cmake
SET(LIBHELLO_SRC hello.cpp)

ADD_LIBRARY(hello_static STATIC ${LIBHELLO_SRC})

SET_TARGET_PROPERTIES(hello_static PROPERTIES OUTPUT_NAME "hello")
SET_TARGET_PROPERTIES(hello_static PROPERTIES CANCEL_DIRECT_OUTPUT 1)

ADD_LIBRARY(hello SHARED ${LIBHELLO_SRC})

SET_TARGET_PROPERTIES(hello PROPERTIES OUTPUT_NAME "hello")
SET_TARGET_PROPERTIES(hello PROPERTIES CANCEL_DIRECT_OUTPUT 1)
# 新版的cmake不用CANDEL_DIRECT_OUTPUT就可重命名
```

### 动态库的版本号

​	一般动态库都有一个版本号的关联

​	`SET_TARGET_PROPERTIES(hello PROPERTIES VERSION 1.2 SOVERSION 1)`

​	VERSION指代动态库版本，SOVERSION指代API版本。

### 安装共享库和头文件

​	将hello共享库安装到<perfix>/lib目录

​	将hello.h安装到<perfix>/include/hello目录

```cmake
# 将文件放在该目录下
INSTALL(FILES hello.h DESTINATION include/hello) # 注意这种写法是相对路径

# 二进制、静态库、动态库安装都用TARGETS
# ARCHIVE--静态库  LIBRARY--动态库  RUNTIME--可执行的目标二进制
INSTALL(TARGETS hello hello_static LIBRARY DESTINATION lib ARCHIVE DESTINATION lib) # hello和hello_static是前面的动态库和静态库
```

### 使用外部共享库和头文件

​	

#### INCLUDE_DIRECTORIES

​	找不到库的问题：

​	这条指令可以用来向工程添加多个特定的头文件搜索路径，路径之间用空格分割

​	在CMakeLists.txt中加入头文件搜索路径

```cmake
INCLUDE_DIRECTORIES(/usr/include/hello) # 添加共享库的安装路径
```

​	找不到引用函数的问题

#### LINK_DIRECTORYS

​	这条指令用于添加非标准库搜索路径

```cmake
# 指定第三方库所在路径
LINK_DIRECTORYS(/home/myproject/libs)
```

#### TARGET_LINK_LIBRARIES

​	添加需要链接的共享库

```cmake
# 只需要给出动态链接库的名字就可以了
# 当然肯定要先能搜索到动态库的路径
TARGET_LINK_LIBRARIES(hello libhello.so) # 前一个变量是生成的二进制文件
```

#### 查看链接情况

```shell
# 查看main的链接情况
[cdsoft_zihang@localhost]ldd main
...
```

## 补充

### 两个linux下的环境变量

​	CMAKE_INCLUDE_PATH和CMAKE_LIBRARY_PATH

```shell
# 作用相当于前面的 INCLUDE_DIRECTORIES(/usr/include/hello)
[cdsoft_zihang@localhost ~] export CMAKE_INCLUDE_PATH=/usr/include/hello
```

#### 生产Debug版本

```shell
[cdsoft_zihang@localhost ~] cmake .. -D CMAKE_BUILD_TYPE
```


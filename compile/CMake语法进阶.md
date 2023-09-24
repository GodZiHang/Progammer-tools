# CMake语法进阶

## LIST

`list (subcommand <list> [args...])`
`subcommand`为具体的列表操作子命令，例如`读取`、`查找`、`修改`、`排序`等。`<list>`为待操作的`列表`变量，`[args...]`为对`列表`变量操作需要使用的参数表，不同的子命令对应的参数也不一致。

cmake的list命令即对列表的一系列操作，cmake中的列表变量是用分号;分隔的一组字符串，创建列表可以使用set命令（参考set命令），例如：`set (var a b c d)`创建了一个列表 "a;b;c;d"，而`set (var "a b c d")`则是只创建了一个变量"a c c d"。

- LENGTH：子命令LENGTH用于读取列表长度
  `list (LENGTH <list> <output variable>)`
  `<output variable>`为新创建的变量，用于存储列表的长度。
- GET：子命令GET用于读取列表中指定索引的的元素，可以指定多个索引。
  `list (GET <list> <element index> [<element index> ...] <output variable>)`
  `<element index>`为列表元素的索引，从0开始编号，索引0的元素为列表中的第一个元素；索引也可以是负数，-1表示列表的最后一个元素，-2表示列表倒数第二个元素，以此类推。注意：当索引（不管是正还是负）超过列表的长度，运行会报错（list index: XX out of range）。 <output  variable>为新创建的变量，存储指定索引元素的返回结果，也是一个列表。
- APPEND：子命令APPEND用于将元素追加到列表。
  `list (APPEND <list> [<element> ...])`
  此命令会改变原列表的值。
- FIND：子命令FIND用于查找列表是否存在指定的元素。
  `list (FIND <list> <value> <output variable>)`
  如果列表`<list>`中存在`<value>`，那么返回`<value>`在列表中的索引，如果未找到则返回-1。

## FILE

`FILE(<GLOB|GLOB_RECURSE> <variable> <pattern>)`

按照正则表达式搜索路径下的文件，比如：

```cmake
file(GLOB SRC_LIST "./src/*.cpp")
```

## AUX_SOURCE_DIRECTORY

`aux_source_directory(<dir> <variable>)`

搜索文件内所有的源文件：CMake 会将当前目录所有源文件的文件名赋值给变量 DIR_SRCS ，再指示变量 DIR_SRCS 中的源文件需要编译成一个名称为 Demo 的可执行文件。

```cmake
# 查找当前目录下的所有源文件
# 并将名称保存到 DIR_SRCS 变量
aux_source_directory(. DIR_SRCS)
# 指定生成目标
add_executable(Demo ${DIR_SRCS})
```

## CONFIGURE_FILE

	在一些情况下，我们需要在我们的项目中标注**项目版本号**、**增加编译选项**等信息，我们可以在项目程序中直接通过手动编写我们的项目信息然后编译打印，也可以通过`CMakeLists.txt`文件的`configure_file`指令，将这些信息设置为变量，再写入`.h.in`文件并通过cmake编译生成`.h`文件，再将该`.h`文件包含进项目代码中，编译到可执行文件或库文件，从而实现项目信息打印等，这样的好处是可以只通过改`CMakeLists.txt`文件就能变更项目信息。

例如在`CMakeLists.txt`文件中定义了项目版本变量：

```cmake
set(Version 1.0)
configure_file(src/build.h.in ${CMAKE_BINARY_DIR}/generated/build.h)
```

`build.h.in`文件中：

```c
#define Project_Version @Version@
```

通过`cmake`编译后生成`build.h`文件，变量名称替换成了变量值：

```c
#define Project_Version 1
```

**configure_file**
 	`configure_file`功能如下：
 	将`<input>`文件里的内容全部复制到`<output>`文件中，并根据输入规则，复制时将`<input>`文件中变量，替换成`@VAR@`或`${VAR}`的变量值，若变量未被定义则替换为空字符串。

```cmake
configure_file(<input> <output>
               [NO_SOURCE_PERMISSIONS | USE_SOURCE_PERMISSIONS |
                FILE_PERMISSIONS <permissions>...]
               [COPYONLY] [ESCAPE_QUOTES] [@ONLY]
               [NEWLINE_STYLE [UNIX|DOS|WIN32|LF|CRLF] ])
```

`<input>`：输入文件，默认路径是在 `CMAKE_CURRENT_SOURCE_DIR`路径下的寻找该文件;输入文件必须是文件而不是文件夹。通常情况下输入文件以`.h.in`为后缀。

`<output>`：输出的文件；默认路径为输出到`CMAKE_CURRENT_BINARY_DIR`路径下，若输出路径中文件已存在则替换。通常情况下输出文件以`.h`为后缀。

`COPYONLY`：只复制文件并**不进行**任何变量替换，在这个选项若指定了`NEWLINE_STYLE`选项时则无效。

`ESCAPE_QUOTES`：躲过任何的反斜杠风格的转义（C风格）。

`@ONLY`：对变量替换进行限制，只对`@VAR@`引用变量进行替换而不对`${VAR}`进行替换。这在配置`${VAR}`语法的脚本时是非常有用的。

`NEWLINE_STYLE <style>`：指定输出文件中的新行格式。UNIX和LF的新行是\n，DOS和WIN32和CRLF的新行格式是\r\n。这个选项在指定了COPYONLY选项时无效。

**示例**

```cmake
# CMakeLists.txt
...
set(VERSION_MAJOR 1)
set(VERSION_MINOR 0)
set(PROJECT_NAME TEST)
set(VAR "var")
set(VARIABLE OFF)

configure_file(
    "${PROJECT_SOURCE_DIR}/build.h.in"
    "${PROJECT_BINARY_DIR}/build.h")
    ...
```

`build.h.in`文件内容：

```cpp
#ifndef _BUILD_H_IN
#define _BUILD_H_IN

#define VERSION_MAJOR @VERSION_MAJOR@
#define VERSION_MINOR @VERSION_MINOR@
#define PROJECT_NAME "${PROJECT_NAME}"
#cmakedefine VAR // CMakeLists.txt 文件中，定义了变量 VAR，那么在转化出来的文件里也会进行定义
                 // 否则，文件中会显示 /*undef VAR*/
#cmakedefine01 VARIABLE // 判断选项是否开启，在转化出的文件里，会根据VAR的值，生成
                   // #define VAR 1 或者 #define VAR 0
#endif
```

那么在执行`cmake`后，`build.h`中的内容为：

```cpp
#ifndef _BUILD_H_IN
#define _BUILD_H_IN

#define VERSION_MAJOR 1
#define VERSION_MINOR 0
#define PROJECT_NAME TEST
#define VAR
#define VARIABLE 0
#endif
```



## 控制流语句

### if 条件

	if 条件: `if`, `elseif`, `else`, `endif`，支持语法如下

```cmake
if(<condition>)
    <commands>
elseif(<condition>) # optional block, can be repeated
    <commands>
else(<condition>)   # optional block
    <commands>
endif()
```

### foreach 循环

- foreach: `foreach(<loop_var> <items>)`
- endforeach: `endforeach([<loop_var>])`

```cmake
# 用法
# 其中 items 是由分号或空格分隔的项目列表，每次迭代开始变量 loop_var 被设置为当前项的值
foreach(<loop_var> <items>)
    <commands>
endforeach()
```

**变体一**
	`foreach(<loop_var> IN [LISTS [<lists>]] [ITEMS [<items]])`
	LISTS A 和 ITEMS ${A} 是等价形式，例如:

```cmake
set(A 0;1)
set(B 2 3)
set(C "4 5")
foreach(X ${A} ${B} ${C}) # 等价于
foreach(X IN LISTS A B C) # 等价于
foreach(X IN LISTS A ITEMS ${B} ${C})
```

**变体二**
	`foreach(<loop_var> RANGE <stop>)`: 遍历数字0, 1, ... 直到(并包括)整数`<stop>`

**变体三**
	`foreach(<loop_var> RANGE <start> <stop> [<step>])`: 由`<start>`到`<end>`遍历数字，并可以设置步长`<step>`

**变体四**
	`foreach(<loop_var>... IN ZIP_LISTS <lists>)`: 可以同时迭代多个列表变量

- 如果给定一个变量 `<loop_var>`，那么它给一系列的 `<loop_var_N>` 变量设置对应列表的当前项。
- 如果给定多个变量，那么列表数目与变量数目匹配。
- 如果列表的长度不一致，较短的列表对应的变量项为空。

```cmake
# 遍历两个长度不一致的列表的项
list(APPEND English one two three four)
list(APPEND Bahasa satu dua tiga)

foreach(num IN ZIP_LISTS English Bahasa)
    message(STATUS "mun_0=${num_0}, num_1=${num_1}")
endforeach()

foreach(en ba IN ZIP_LISTS English Bahasa)
    message(STATUS "en=${en}, ba=${ba}")
endforeach())    
```

### while 循环

- while: `while(<condition>)`
- endwhile: `endwhile(<condition>)`

```cmake
while(<condition>)
    <commands>
endwhile()
```

### 跳出循环

	`break` 和 `continue` 支持 `foreach 循环` 和 `while 循环`。
	
	我们使用 `break()` 命令提前终止循环，而使用 `continue()` 命令可用于立即开始下一次迭代。

> 在前面的控制语句里，退出分支或循环的语句也可以添加`<condition>`，例如
>
> ```cmake
> if(condition_A)
>  <commands>
> elseif(condition_B)
>  <commands>
> endif(condition_A OR condition_B)
> ```
>
> 在上面的示例中，endif语句附加了一个条件，只有在condition_A或condition_B满足时才会执行。这意味着如果condition_A或condition_B至少有一个为镇，那么整个if-elseif-endif块中的代码将被执行。
>
> 尽管这是有效的cmake语法，但在实际使用中，通常将条件放在if()语句中，以使代码更清晰易读。通常来讲，不要在endif()语句里添加条件。

### `<condition>` 条件语句

#### 布尔常量

	True 常量为，1, ON, YES, TRUE, Y, or 非零数字.
	False 常量为，0, OFF, NO, FALSE, N, IGNORE, NOTFOUND, 空字符串, 后缀为 -NOTFOUND。
	
	布尔常量命名不区分大小写。

- `if(<constant>)`
   如果参数不是这些特定常量，则被视为变量或字符串。

  ```cmake
  if(NO)
      message("ON")
  endif()
  
  if(OFF)
      message("OFF")
  endif()
  ```

- `if(<variable|string>)`

  给定的变量被定义为一个非 False 常量，则被视为 True。表示变量被使用。

  ```cmake
  # 判断变量DEBUG是否被定义，以下两种形式都可以
  if(DEBUG)
      message("DEBUG: ${DEBUG}")
  endif()
  
  if(${DEBUG})
      message("DEBUG: ${DEBUG}")
  endif()
  # 运行 cmake .. -DDEBUG=Y -L，会打印信息。
  ```

  > 在if() 命令中，允许直接以`<variable>`表示变量，而非`${<variable>}`。

#### 定义

	cmake提供了一些关键词，用于判断某个值是否已经被定义。

- 判断变量是否存在：`if(DEFINED <name>|CACHE{<name>}|ENV{<name>})`
  	如果给定的名称为被定义的变量、缓存变量或环境变量，则为真。变量值无关紧要。

	与 `if(<variable|string>)` 相比，`if(<variable|string>)` 是用来判断变量的值，而 `if(DEFINED ...)` 用来判断变量是否被定义。

```cmake
# 判断变量是否被定义
if(DEFINED DEBUG)
    message("DEBUG: ${DEBUG}")
endif()
```

- 判断命令是否存在：`if(COMMAND command-name)`
  	如果给定的名称为可调用的命令、宏、函数名，则为真
- 判断策略是否存在：`if(POLICY policy-id)`
   如果给定的名称为现有的策略(形式为 `CMP<NNNN>`)，则为真。
- 判断目标是否存在：`if(TARGET target-name)`
   如果给定的名称为现有的目标，用` add_executable(), add_library(), add_custom_target()` 命令创建，则为真。
- 判断测试是否存在：`if(TARGET target-name)`
   如果给定的名称为现有的目标，用 `add_executable(), add_library(), add_custom_target()` 命令创建，则为真。
- 路径
  - `if(EXISTS path-to-file-or-directory)`
     如果给定的文件或目录存在，则为真。仅适用于完整路径。支持符号链接。
  - `if(file1 IS_NEWER_THAN file2)`
     如果 file1 比 file2 新 或者 两个文件之一不存在，则为真。仅适用于完整路径。
  - `if(IS_DIRECTORY path-to-directory)`
     如果给定名称是目录，则为真。仅适用于完整路径。
  - `if(IS_SYMLINK file-name)`
     如果给定名称是符号链接，则为真。仅适用于完整路径。
  - `if(IS_ABSOLUTE path)`
     如果给定名称是绝对路径，则为真。仅适用于完整路径。

#### 逻辑运算

```cmake
# 否
if(NOT <condition>)
# 与
if(<cond1> AND <cond2>)
# 或
if(<cond1> OR <cond2>)
# 可以使用()改变逻辑运算的优先顺序
```

### 比较和匹配

- 字符串匹配：`if(<variable|string> MATCHES regex)`

- 数字的比较：

  ```cmake
  if(<variable|string> LESS <variable|string>)          # <
  if(<variable|string> GREATER <variable|string>)       # >
  if(<variable|string> EQUAL <variable|string>)         # =
  if(<variable|string> LESS_EQUAL <variable|string>)    # <=
  if(<variable|string> GREATER_EQUAL <variable|string>) # >=
  ```

- 字符串的比较：按字典序对字符串进行比较

  ```cmake
  if(<variable|string> STRLESS <variable|string>)          # <
  if(<variable|string> STRGREATER <variable|string>)       # >
  if(<variable|string> STREQUAL <variable|string>)         # =
  if(<variable|string> STRLESS_EQUAL <variable|string>)    # <=
  if(<variable|string> STRGREATER_EQUAL <variable|string>) # >=
  ```

- 版本的比较：比较版本大小

  版本格式为 `major[.minor[.patch[.tweak]]]`，省略的组件被视为零

  ```cmake
  if(<variable|string> VERSION_LESS <variable|string>)          
  if(<variable|string> VERSION_GREATER <variable|string>)       
  if(<variable|string> VERSION_EQUAL <variable|string>)         
  if(<variable|string> VERSION_LESS_EQUAL <variable|string>)    
  if(<variable|string> VERSION_GREATER_EQUAL <variable|string>) 
  ```

- 元素是否在列表中：`if(<variable|string IN_LIST <variable>)`

  如果给定元素在列表中，则为真

## find_

### find_package

find_package 主要用于查找指定的 package，主要支持两种搜索方法：

- **Config mode**：查找 xxx-config.cmake或 xxxConfig.cmake的文件，如OpenCV库的OpenCVConfig.cmake
- **Module mode**：查找Findxxx.cmake文件，如OpenCV库中的FindCUDA.cmake

这两种模式存在优先级，如果其中一种检索模式没有找到所需模块，就会启用另一种模式。可以通过 `CMAKE_PREFIX_PATH` 设置配置模式的搜索路径，通过 `CMAKE_MODULE_PATH` 设置模块模式的搜索路径。

```cmake
# 命令格式
find_package(<PackageName> [version]
              [EXACT] # 要求version完全匹配
              [QUIET] # 无论找到与否，不产生任何提示信息
              [REQUIRED] # 必须找到 xxx.cmake，否则提示报错
              # 查找指定模块，如果任何一个模块没有找到，则认为整个Package没有找到
              [COMPONENTS [components...]] 
              [OPTIONAL_COMPONENTS [components ...]]
              [MODULE]           # 仅使用模块模式
              [CINFIG|NO_MODULE] # 仅使用配置模式（两种写法是等效的）
              [GLOBAL]
              [NO_POLICY_SCOPE]
              [BYPASS_PROVIDER])
```

find_package 一般都内置了一些变量：

- `<PackageName>_FOUND`: 可以判断是否找到对应的包或者模块
- `<PackageName>_INCLUDE_DIR`: 表示头文件目录（前提是包被找到才会被自动设置）
- `<PackageName>_LIBRARIES`: 表示库文件

### find_library

	find_library 一般直接去查找依赖库文件，和find_package 不一样，find_package 找的是 .cmake 文件，而find_library 直接找 .so 或者 .a 文件。命令的执行结果会默认缓存到 CMakeCache.txt 中。

```cmake
# 命令格式
find_library(<VAR>
             NAMES name1 [name2 ...] # 要查找的库，可以是前缀+库名+后缀，也可以只有库名
             [NAMES_PER_DIR] #表示一个名称遍历查找一次
             [HINTS [path | ENV var] ... ] # 先搜索指定的路径，后搜索系统路径
             [PATHS [path | ENV var] ... ] # 先搜索系统路径，后搜索指定的路径
             ...
             [NO_CAHCE]
             [REQUIRED])
```

	查找成功时，会向变量VAR中添加成功找到头文件的目录，并且当VAR未被清空时，不会重新进行搜索，可以使用`unset(VAR CACHE)`清除变量VAR以及缓存文件中VAR的值。如果没有找到，结果将为-NOTFOUND

### find_path

	`find_path`一般用于在某个目录下查找一个或多个头文件，命令的执行结果会保存在`<VAR>`中。同时命令的执行结果会默认缓存到 CMakeCache.txt 中。

```cmake
# 命令格式
find_path(<VAR>
          NAMES name1 [name2 ...] # 要查找的头文件
          [HINTS [path | ENV var] ... ] # 先搜索指令路径，后搜索系统路径
          [PATHS [path | ENV var] ... ] # 先搜索系统路径，后搜索指定路径
          ...
          [NO_CAHCE]
          [REQUIRED])
```

### find_program

	查找可执行程序，用法与前面类似。

## 自定义指令

    在cmake中，可以通过编写自定义的CMake模块文件或CMake函数/宏来定义并使用不属于CMake自身支持的指令，以适应特定项目或需求，有两种方式可以实现自定义的指令

### 自定义CMake函数或宏

1. 在CMakeLists.txt文件中定义自定义函数或宏

   ```cmake
   function(my_custom_command ARG1 ARG2)
       # 你自己的逻辑
   endfunction()
   # macro() 的用法与function()类似，不同的是，macro在CMake中展开，
   # 类似于C语言中的宏，而函数则是在调用时执行
   ```

2. 然后，你可以在其它地方调用这个函数或宏

### 自定义CMake模块文件

	自定义cmake模块，一般情况下可以简单理解为自定义 Findxxx.cmake 或者 XXXConfig.cmake 文件，然后可以搭配find_package 来一次引入模块中涉及的所有内容。

1. 编写Findxxx.cmake

   Findxxx.cmake支持CMakeLists.txt所使用的语法，但无法主动通过命令执行，find_package匹配了自己所需的模块后，会自动执行Findxxx.cmake

   Findxxx.cmake 的作用：

   - 获取头文件所在路径
   - 获取库文件全路径
   - 自定义控制变量，如 XXX_FOUND 可以用于判断是否成功引入模块（是否获取到了头文件路径和库文件全路径） 

   ```cmake
   # 获取头文件路径
   unset(INCLUDE_DIR CACHE) # 在使用find_path指令时，最好先清空缓存
   find_path(INCLUDE_DIR
             NAME file1.h
             HINTS ${PROJECT_SOURCE_DIR}/src)
   list(APPEND Test_INCLUDE_DIR ${INCLUDE_DIR})
   
   # 获取库文件（含全路径）
   unset(LIBS CACHE)
   find_library(LIBS
                NAMES mylib
                HINTS ${PROJECT_SOURCE_DIR}/build/lib)
   list(APPEND Test_LIBRARIES ${LIBS})
   
   # 设置一个变量来表示是否找到了当前模块
   if(Test_INCLUDE_DIR AND Test_LIBRARIES)
       set(Test_FOUND TRUE)
   endif()
   ```

2. 搭配 find_package 引入自定义cmake模块

   ```cmake
   # 添加检索路径
   # 假设上面定义的.cmake文件放在了./cmake目录下
   set(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake)
   
   # 查找模块文件
   find_package(Test REQUIRED MODULE)
   
   # 判断是否引入
   if(Test_FOUND)
       ...
   endif()
   ```

   
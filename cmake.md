## cmake

`cmake`不直接构建软件，用于生成`Makefile`、`.sln`等构建文件。

`cmake`基于`CMakeLists.txt`文件来生成对应的构建文件。



### 命令行

```bash
cmake # Linux/Unix/MacOS平台上，CMake默认生成标准的makefile；Windows平台上则会检测Visual Studio版本，生成对应的VS .sln项目工程文件

make TARGET
```

### 流程与循环

#### `if`

```cmake
if(expression)
  # then section.
  COMMAND1(ARGS ...)
  COMMAND2(ARGS ...)
  #...
elseif(expression2)
  # elseif section.
  COMMAND1(ARGS ...)
  COMMAND2(ARGS ...)
  #...
else(expression)
  # else section.
  COMMAND1(ARGS ...)
  COMMAND2(ARGS ...)
  #...
endif(expression)
```

if语句中的判断条件有（优先级分三级，前面的优先于后面的）：

```basj
> EXISTS, COMMAND, DEFINED 

> EQUAL, LESS, LESS_EQUAL, GREATER, GREATER_EQUAL, STREQUAL, STRLESS, STRLESS_EQUAL, STRGREATER, STRGREATER_EQUAL, VERSION_EQUAL, VERSION_LESS, VERSION_LESS_EQUAL, VERSION_GREATER, VERSION_GREATER_EQUAL, MATCHES

> NOT,AND,OR
```

例如：

```cmake
if(EXISTS xxx AND NOT xxx)
```

#### `foreach`

```cmake
foreach(loop_var arg1 arg2 ...)
  COMMAND1(ARGS ...)
  COMMAND2(ARGS ...)
  ...
endforeach(loop_var)

# 或者采用RANGE指定范围

foreach(i RANGE 3)
    message(STATUS "current is ${i}")
endforeach(i)

# 或者引入数组结构的数据

foreach(loop_var IN [LISTS [list1 [...]]]
                    [ITEMS [item1 [...]]])
```

#### `while`

```cmake
while(condition)
  COMMAND1(ARGS ...)
  COMMAND2(ARGS ...)
  ...
endwhile(condition)
```

### 宏和函数

#### `marco`

```cmake
macro(<name> [arg1 [arg2 [arg3 ...]]])
  COMMAND1(ARGS ...)
  COMMAND2(ARGS ...)
  ...
endmacro(<name>)
```

#### `function`

```cmake
function(<name> [arg1 [arg2 [arg3 ...]]])
  COMMAND1(ARGS ...)
  COMMAND2(ARGS ...)
  ...
function(<name>)
```

### 配置项

#### `message`

输出信息：

```cmake
message("log") # log
```

#### `cmake_minimum_required`

指定项目支持的cmake最低版本：

```cmake
cmake_minimum_required(VERSION 3.7)
```

#### `project`

指定项目名称及属性，基本用法：

```cmake
project(my_project)
```

#### `set`

定义一些变量、宏的值：

```cmake
# 设置C++语法标准
set(CMAKE_CXX_STANDARD 11) 

# 常见用法
set(num1 10)
message("num1 = ${num1}") # num1 = 10

set(num2 a b c)
message("num2 = ${num2}") # num2 = a;b;c

set(num3)
message("num3 = ${num3}") # num3 =

# 和 if else 配合
if(WIN32)
	set(env win32)
else(WIN32)
	set(env others)
endif(WIN32)

```

#### `add_executable`

构建可执行程序，一般在项目的主`CMakelists.txt`中：

```cmake
add_executable(runtest main.cpp a.cpp) # 生成runtest的可执行文件
```

#### `add_subdirectory`

添加一个需要构建的子目录，子目录中有`CMakelists.txt`：

```cmake
add_subdirectory(sub_dir)
```

#### `find_package`

查找指定的模块：

```cmake
find_package(GTest REQUIRED)

if(NOT GTest_FOUND)
	message(FATAL_ERROR "gTest not found")
endif(NOT GTest_FOUND)
```

#### `find_library`

查找指定库的**路径**并保存到变量中：

```cmake
# 如果 ./mymath 路径下存在  mymath库，则把 ./mymath 存入 libvar
# 否则存入 libvar_NOTFOUNT 变量
find_library (libvar mymath ./mymath)
```

#### `include_directories`

添加头文件路径，更推荐使用`target_include_directories`。

如果`.cpp`文件和对应的`.h`文件不在同一个文件夹，则需要使用`include_directories`将`.h`文件所在的文件夹包含进来。

```cmake
include_directories(dir_name)
```

#### `link_directories`

设置链接库路径，更推荐使用`target_link_directories`：

```cmake
link_directories("/opt/MATLAB/R2012a/bin/glnxa64")
```

#### `aux_source_directory`

将指定目录下的源文件添加到变量中，该命令不会递归子目录：

```cmake
aux_source_directory(./src SRC)
```

#### `file`

当需要手动`add_executable`的命令不断增加的时候，可以通过`file`命令实现自动化的构建程序：

假设目录结构如下：

```bash
├── CmakeLists.txt
├── part1
│   ├── CmakeLists.txt
│   ├── copytest.c
│   ├── groupid.c
│   ├── mycopy.c
│   ├── myerror.c
│   ├── myls.c
│   ├── myshell.c
│   ├── mystdcopy.c
│   ├── newshell.c
│   └── processid.c
├── part11
│   ├── CmakeLists.txt
│   ├── pthread1.c
│   ├── pthread2.c
│   ├── pthread3.c
│   └── pthread4.c
├── part3
│   ├── CmakeLists.txt
│   ├── holetest.c
│   └── seektest.c
├── unp_1
│   ├── CMakeLists.txt
│   ├── daytimetcpcli.c
│   ├── daytimetcpcliv6.c
│   └── daytimetcpsrv.c
└── unp_3
    ├── CMakeLists.txt
    └── byteorder.c
```

那么可以这么写：

```cmake
AUX_SOURCE_DIRECTORY(. PART_THREE)
SET(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/bin)
SET(LIBRARY_OUTPUT_PATH ${PROJECT_BINARY_DIR}/lib)
FOREACH (FILE ${PART_THREE})
    MESSAGE(STATUS ${FILE})
    STRING(REPLACE "./" "" LIB_NAME ${FILE})
    STRING(REPLACE ".c" "" LIB_NAME ${LIB_NAME})
    add_executable(${LIB_NAME} ${FILE})
    target_link_libraries(${LIB_NAME} apue.a)
ENDFOREACH ()
```






















































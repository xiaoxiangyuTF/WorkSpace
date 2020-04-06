## cmake 安装自定义库到指定目录
开发中，我们会开发一些自定义的库或者二次封装的库，如log库，config文件读取库，文件操作库，算法库等，编译后可以按照如下命令进行安装到指定到目录下。默认大家会使用cmake指令。<br>
具体步骤：<br>
1. 编写库文件+CMakeLists.txt
2. 编译到指定目录

### **1.库开发 + CMakeLists.txt编写**
hello.cpp
```
#include "hello.h"
#include <iostream>

namespace tf{
    void print_hello(){
        std::cout<<"hello,world"<<std::endl;
    }
}
```

hello.h
```
#ifndef PROJECT_DEMO_H
#define PROJECT_DEMO_H

namespace tf{
    void print_hello();
}

#endif //PROJECT_DEMO_H

```
CMakeLists.txt
```
cmake_minimum_required(VERSION 3.0)
project(tf_proj)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11" )

# add source directory
aux_source_directory(./ SRC_DIRS)

set(CMAKE_CXX_FLAGS_RELEASE "$ENV{CXXFLAGS} -g -Wall")

include_directories (
        /usr/include
        /usr/local/include
)
# share .so
add_library(hello SHARED ${SRC_DIRS})
# static .a
add_library(hello_static ${SRC_DIRS})
# hello_static.a hello.so use the same hello
SET_TARGET_PROPERTIES(hello_static PROPERTIES OUTPUT_NAME "hello")

# install .so/.a
INSTALL(
    TARGETS hello hello_static
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib
    )
# install .h
INSTALL(FILES hello.h DESTINATION include)
```

### 2. 编译和安装命令
```
cmake -DCMAKE_BUILD_TYPE=RELEASE -DCMAKE_INSTALL_PREFIX=/usr/local/mylocal/hello/hello-0.1.0 ..
make -j4
sudo make install
```

### **trick**
1. 建议安装时按照库名\库名-版本号方式进行存放，可支持多版本的库存放，类似opencv2，opencv3， opencv4等；<br>
2. 在 ~/.bashrc 添加库地址，并且**激活 source ~/.bashrc**。该文件包含专用于你的bash shell的bash信息，当登录时以及每次打开新的shell时，该该文件被读取。因此，需要**重新**打开bash。
    ```
    # HELLO
    export PATH="/usr/local/mylocal/hello/hello-0.1.0:$PATH"
    ```

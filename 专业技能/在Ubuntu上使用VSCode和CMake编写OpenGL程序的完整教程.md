# 在Ubuntu上使用VSCode和CMake编写OpenGL（GLFW和glad）程序的完整教程

# 引言

OpenGL是一个强大的图形编程接口，广泛用于创建交互式的3D图形应用程序和游戏。它提供了一套跨平台的API，可以在不同的操作系统和硬件上进行图形渲染。在Ubuntu上进行OpenGL开发是一个受欢迎的选择，因为Ubuntu是一个流行的开发环境，并且提供了丰富的开发工具和库。

本教程将指导你如何在Ubuntu上使用VSCode和CMake来编写OpenGL程序。VSCode是一个轻量级的代码编辑器，提供了强大的功能和插件生态系统，使得开发过程更加高效。而CMake是一个跨平台的构建工具，可以自动生成用于不同构建系统的构建脚本，简化了项目的构建过程。

在本教程中，我们将首先安装必要的软件和库，然后配置CMakeLists.txt文件，编写OpenGL程序的源代码，并使用CMake进行构建和运行。通过按照本教程的步骤，你将能够快速入门OpenGL开发，并在Ubuntu上构建出可运行的OpenGL程序。

这些内容将帮助你简单理解OpenGL的工作原理，并为你的图形应用程序提供更多的可能性。

让我们开始这个令人兴奋的OpenGL开发之旅吧！

# 安装GLFW和其他依赖

首先，你需要安装一些必要的软件和库

```sh
sudo apt update
sudo apt install libglfw3-dev cmake gcc g++ gdb libgl1-mesa-dev 
```

以下工具的介绍

1. `cmake`：CMake是一个跨平台的构建工具，用于自动生成用于不同构建系统（如Makefile、Ninja等）的构建脚本。它可以简化项目的构建过程，并提供了一种简洁的方式来描述项目的构建配置。
2. `libglfw3-dev`：GLFW是一个用于创建窗口和处理用户输入的开源库，它提供了一个跨平台的API，用于创建OpenGL上下文和处理窗口事件。`libglfw3-dev`是GLFW的开发包，包含了用于开发GLFW程序所需的头文件和库文件。
3. `libgl1-mesa-dev`：Mesa是一个开源的图形库，它提供了OpenGL和其他图形API的实现。`libgl1-mesa-dev`是Mesa的开发包，包含了用于开发OpenGL程序所需的头文件和库文件。

这些工具和库的安装是为了在Ubuntu上进行OpenGL开发所需的基本组件。它们提供了编译、构建和链接OpenGL程序所需的工具和库文件。



# 配置GLAD

GLAD是一个开源的库，GLAD的配置与大多数的开源库有些许的不同，GLAD使用了一个在线服务。在这里我们能够告诉GLAD需要定义的OpenGL版本，并且根据这个版本加载所有相关的OpenGL函数。

打开GLAD的在线服务https://glad.dav1d.de/，将语言(Language)设置为C/C++，在API选项中，选择3.3以上的OpenGL(gl)版本（我们的教程中将使用3.3版本，但更新的版本也能用）。之后将模式(Profile)设置为Core，并且保证选中了生成加载器(Generate a loader)选项。现在可以先（暂时）忽略扩展(Extensions)中的内容。都选择完之后，点击生成(Generate)按钮来生成库文件。

GLAD现在应该提供给你了一个zip压缩文件，包含两个头文件目录，和一个glad.c文件。将两个头文件目录（glad和KHR）复制到你的Include文件夹中（或者增加一个额外的项目指向这些目录），并添加glad.c文件到你的工程中。

# 安装vscode并且安装插件

接下来，你需要安装VSCode和一些插件来进行开发。你可以从VSCode的官方网站下载并安装它。

安装完成后，打开VSCode并在扩展商店中搜索并安装以下插件：

- C/C++：用于提供C/C++语言支持。
- CMake：在VSCode中，CMake是一个用于管理和构建C/C++项目的插件。它与CMake构建系统集成，提供了一种便捷的方式来配置、构建和调试C/C++项目。
- CMake Tools：用于在VSCode中使用CMake进行构建的插件。

# 新建项目

安装好插件后，我们新建一个项目目录叫opengl。安装完vscode的插件之后按F1键，输入CMake，选择CMake快速开始选项，如下图。

![image-20230927163641149](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20230927163641149.png)

然后根据提示一步步进行项目配置。就会得到跟下图一样的结果。

![image-20230927163842824](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20230927163842824.png)

# 配置CMakeLists.txt并且编写程序

在VSCode中打开`CMakeLists.txt`文件，并将以下内容复制到文件中：

```cmake
cmake_minimum_required(VERSION 3.0.0)
project(opengl VERSION 0.1.0 LANGUAGES C CXX)

include(CTest)
enable_testing()


include_directories(${PROJECT_SOURCE_DIR}/include)
add_executable(opengl main.cpp src/glad.c)
find_package(glfw3 REQUIRED)
target_link_libraries(opengl glfw dl)
set(CPACK_PROJECT_NAME ${PROJECT_NAME})
set(CPACK_PROJECT_VERSION ${PROJECT_VERSION})
include(CPack)
```

打开`main.cpp`文件，并将下面的内容复制到文件中

```c++
#include <glad/glad.h>
#include <GLFW/glfw3.h>
#include <iostream>

int main()
{
    glfwInit();
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);

    GLFWwindow *window = glfwCreateWindow(800, 600, "OpenGL", nullptr, nullptr);
    if(!window) {
        std::cout << "Failed initialize glfw window" << std::endl;
        glfwTerminate();
        return -1;
    }
    glfwMakeContextCurrent(window);

    while(!glfwWindowShouldClose(window)) {
        glfwSwapBuffers(window);
        glfwPollEvents();
    }

    return 0;
}
```

# 运行文件

键入`ctrl+shift+b` 构建项目

![image-20230927174256387](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20230927174256387.png)

选择`CMake:build`构建成功会有提示如下图

![image-20230927174412126](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20230927174412126.png)

键入`ctrl+F5`可以运行程序，如果出现一个黑窗口，就说明成功了。

![image-20230927174557379](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20230927174557379.png)
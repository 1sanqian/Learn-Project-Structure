# 解读 Ros2CMakeLists 脚本文件

## 1. CMakeLists.txt

CMakeLists.txt:是 ROS2 包用 CMake 构建系统时的配置脚本。它告诉编译器,要怎么编译你的代码，依赖什么库，最后生成什么可执行程序

借以下文件为例解读代码，了解脚本文件大致结构：

```
cmake_minimum_required(VERSION 3.8)
project(robot)

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# find dependencies
find_package(rclcpp REQUIRED)
add_executable(hello_node src/hello_node.cpp)
ament_target_dependencies(hello_node rclcpp)
# uncomment the following section in order to fill in
# further dependencies manually.
# find_package(<dependency> REQUIRED)

install(TARGETS
  hello_node
  DESTINATION lib/${PROJECT_NAME})


if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  # the following line skips the linter which checks for copyrights
  # comment the line when a copyright and license is added to all source files
  set(ament_cmake_copyright_FOUND TRUE)
  # the following line skips cpplint (only works in a git repo)
  # comment the line when this package is in a git repo and when
  # a copyright and license is added to all source files
  set(ament_cmake_cpplint_FOUND TRUE)
  ament_lint_auto_find_test_dependencies()
endif()

ament_package()

```

## 2. 解读文件

### 1. cmake_minimum_required(VERSION 3.8)

指定编译时需要的 最低 CMake 版本。

这里是 3.8。

👉 确保你系统上的 CMake 版本不低于这个值。

### 2. project(robot)

定义这个工程的名字叫 robot。

以后生成的可执行文件会放到 install/robot/... 下面。

### 3. 编译器选项

```
if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()
```

如果用的是 GNU g++ 或 Clang 编译器，就加上一些编译警告选项：

-Wall → 打开常见的警告。

-Wextra → 打开额外的警告。

-Wpedantic → 严格按照标准检查。

### 4. 查找依赖

find_package(rclcpp REQUIRED)

告诉 CMake：需要 ROS2 的 rclcpp 库（C++ 客户端库）。

REQUIRED 表示找不到就报错。

### 5. 定义可执行文件
   
add_executable(hello_node src/hello_node.cpp)

把 src/hello_node.cpp 编译成一个可执行文件，名字叫 hello_node。

### 6. 关联依赖
   
ament_target_dependencies(hello_node rclcpp)

把 hello_node 这个可执行文件和 rclcpp 库绑定起来。

没这个步骤，代码里用到的 ROS2 API 就会链接失败。

### 7. 安装规则

```
install(TARGETS
  hello_node
  DESTINATION lib/${PROJECT_NAME})
```

指定安装时，把 hello_node 放到 install/robot/lib/robot/ 下。

这样以后就能用命令运行它：

ros2 run robot hello_node

### 8. 测试相关（可选）

```
if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  set(ament_cmake_copyright_FOUND TRUE)
  set(ament_cmake_cpplint_FOUND TRUE)
  ament_lint_auto_find_test_dependencies()
endif()
```

如果开启了测试，会自动检查代码风格、版权声明等.

### 9. ament_package()

必须要有的收尾语句。

表示这是一个 ament 包（ROS2 使用的构建系统）。

它会：

生成依赖信息（让别的包找到你）。

确认安装目标。

把这个包注册到 ROS2 的工作空间里。

👉 可以理解为：告诉 ROS2，“我这个包已经准备好，可以被编译、安装和使用了。”



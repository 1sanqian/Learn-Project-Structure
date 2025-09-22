# ros2 项目的创建

## 1.ros2 项目的结构

```
my_robot_project/
├── CMakeLists.txt        # 构建规则
├── package.xml           # ROS2 包元数据
├── src/                  # 源代码
├── include/              # 头文件
├── launch/               # 启动文件
├── config/               # 配置文件
├── urdf/                 # 机器人模型
├── meshes/               # STL/DAE 网格模型
├── worlds/               # 仿真环境 (Gazebo/Ignition)
├── bringup/              # 一键启动脚本
├── docs/                 # 文档
└── test/                 # 单元测试 / 集成测试
```

## 2. ros2 项目创建

 ### 1）创建一个工作空间
 
 ```
  mkdir -p ~/ros2_ws/src
  cd ~/ros2_ws
 ```

 ### 2）创建ros2 包

 ```
  cd ~/ros2_ws/src
  ros2 pkg create --build-type ament_cmake my_first_pkg
 ```

ros2 pkg create： 是ROS2 自带的命令，用来新建一个包，包是 ROS2 项目的基本单位，相当于一个小模块，里面可以有：
- 你的源代码 (C++/Python)
- 依赖声明
- 启动文件 (launch files)
- 配置文件

--build-type ament_cmake：
- ament_cmake → 如果你写的是 C++ 节点
- ament_python → 如果你写的是 Python 节点

若主要用python，可以换成：ros2 pkg create --build-type ament_python my_first_pkg

⚠️ 如果源代码兼用 python 和 C++ 语言，有两种方法可以实现

1. 分成两个包

```
ros2_ws/src/
├── my_robot_cpp/     # 用 ament_cmake，写 C++ 节点
├── my_robot_py/      # 用 ament_python，写 Python 节点

```

2. 一个包里同时包含 C++ 和 Python

```
my_first_pkg/
├── CMakeLists.txt         # 管 C++ 编译
├── package.xml            # 声明依赖（C++ + Python 都写）
├── src/                   # C++ 源码
├── include/               # C++ 头文件
├── my_first_pkg/          # Python 模块
│   └── __init__.py
└── setup.py               # Python 构建配置
```

my_first_pkg:这是包的名字（自取）

通过 ros2 run my_first_pkg xxx_node 来运行这个包里的程序

 ### 3）编译

 ```
cd ~/ros2_ws
colcon build
source install/setup.bash
```

编译运行之后的项目结构如下：

```
my_first_pkg/
├── CMakeLists.txt        # 编译配置 (C++ 用)
├── package.xml           # 包的依赖/说明
├── include/              # 头文件目录
├── src/                  # 代码目录（你要写 C++ 节点的地方）
└── resource/             # ROS2 的资源文件
```

# hello_node.cpp 解读

1. 一个名为 hello_node 的 ROS2 节点
2. 节点里启动了一个定时器，每秒执行一次动作。
3. 动作就是 → 打印 "Hello, Robot!"。
4. 程序运行后，终端会每秒看到：[INFO] [hello_node]: Hello, Robot!

## 解读

1. 引入 ROS2 C++ API 的头文件

```
#include "rclcpp/rclcpp.hpp"   // ROS2 C++ API
```

引入 ROS2 C++ API 的头文件。

没它就用不了 ROS2 的功能，比如节点、话题、定时器


2. 定义一个类 HelloNode

```
class HelloNode : public rclcpp::Node
```

定义一个类 HelloNode。

它 继承 ROS2 的基础类 rclcpp::Node → 意味着它就是一个 ROS2 节点。

```
public:
    HelloNode() : Node("hello_node")   
```

public: → 公共部分，外部能访问。

HelloNode() → 构造函数（对象创建时自动调用）。

: Node("hello_node") → 调用父类 Node 的构造函数，把节点名字设为 hello_node。

👉 启动后 ROS2 系统里会有一个叫 hello_node 的节点。

3. 创建一个 定时器

```
{
    // 每秒打印一次日志
    timer_ = this->create_wall_timer(
        std::chrono::seconds(1),
        [this]() { RCLCPP_INFO(this->get_logger(), "Hello, Robot!"); });
}
```

创建一个 定时器，周期为 1 秒。

第二个参数是一个 lambda 匿名函数：

[this]() { RCLCPP_INFO(this->get_logger(), "Hello, Robot!"); }

[this] → 捕获当前对象，能用 this->get_logger()。

RCLCPP_INFO(...) → 打印 ROS2 日志，内容是 "Hello, Robot!"。

👉 效果：每 1 秒输出一行日志。

4. 定义一个私有变量

```
private:
    rclcpp::TimerBase::SharedPtr timer_;
```

定义一个私有变量 timer_。

类型是 rclcpp::TimerBase::SharedPtr（定时器的智能指针）。

保存定时器对象，保证它不会被销毁。

👉 没有这个变量，定时器一创建完就没了。

5. C++ 程序的入口函数

```
int main(int argc, char **argv)
```

C++ 程序的入口函数。

argc / argv → 保存命令行参数（比如你用 ros2 run 启动时传入的参数）。

```
rclcpp::init(argc, argv);               // 初始化 ROS2
```

初始化 ROS2 系统。

必须在用 ROS2 API 前调用。

```
rclcpp::spin(std::make_shared<HelloNode>()); // 运行节点
```

std::make_shared<HelloNode>() → 创建一个 HelloNode 节点对象。

rclcpp::spin(...) → 让节点进入循环，等待并执行回调（定时器、订阅等）。

👉 没有 spin，程序会直接退出。

```
rclcpp::shutdown();                     // 关闭
```

优雅地关闭 ROS2，清理资源。

通常在按 Ctrl+C 退出时调用。

```
return 0;
```

正常结束程序。








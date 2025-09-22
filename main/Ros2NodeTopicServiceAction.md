# Ros2 关于 Node、Topic、Service、Action 知识点

## ROS2 节点通信笔记

### 节点（Node）

定义：ROS2 中的最小执行单元，一个节点就是一个独立的进程（比如一个程序）。

作用：负责某一功能，比如“手柄输入节点”、“里程计节点”、“电机驱动节点”。

例子：

节点 A：接收手柄输入并发布速度。

节点 B：订阅速度并驱动电机。

### 话题（Topic）

#### 1. 发布者（Publisher）

节点把数据打包成消息，通过某个话题发布出去。

多个节点可以同时订阅同一个话题。

📌 例子：

```
// 发布者：发布小车速度
publisher_ = this->create_publisher<geometry_msgs::msg::Twist>("cmd_vel", 10);

auto msg = geometry_msgs::msg::Twist();
msg.linear.x = 0.5;
msg.angular.z = 0.0;
publisher_->publish(msg);
```

#### 2. 订阅者（Subscriber）

节点订阅某个话题，接收消息并做处理。

📌 例子：

```
// 订阅者：订阅速度指令
subscription_ = this->create_subscription<geometry_msgs::msg::Twist>(
    "cmd_vel", 10,
    [this](geometry_msgs::msg::Twist::SharedPtr msg) {
        RCLCPP_INFO(this->get_logger(), "Received: linear=%.2f angular=%.2f",
                    msg->linear.x, msg->angular.z);
        // 这里可以加驱动电机的逻辑
    });
```

### 服务（Service）

类似于 函数调用，是 请求-应答模式。

适合 立即执行并返回结果 的操作。

📌 例子：

客户端：请求“重置里程计”。

服务端：收到请求，清零里程计，并返回“已重置”。

```
service_ = this->create_service<std_srvs::srv::Empty>(
    "reset_odometry",
    [this](const std::shared_ptr<std_srvs::srv::Empty::Request> request,
           std::shared_ptr<std_srvs::srv::Empty::Response> response) {
        (void)request; (void)response;
        RCLCPP_INFO(this->get_logger(), "Odometry reset!");
    });
```

### 动作（Action）

用于 需要一段时间才能完成的任务（比服务更强大）。

特点：

可以发送 目标（goal）。

可以收到 执行过程的反馈。

最终有 结果。

📌 例子：

动作：导航机器人到某个点。

客户端：发出“去 (x, y)”的目标。

服务端（导航节点）：不断反馈“当前进度”，最终返回“已到达”。

## 总结
<img width="789" height="196" alt="image" src="https://github.com/user-attachments/assets/440b9ec2-97f3-457e-92b5-0916d58c4201" />

## 示例（机器人小车）

手柄节点：发布 /cmd_vel（话题）。

底盘节点：订阅 /cmd_vel，驱动电机。

重置节点：提供 /reset_odometry 服务。

导航节点：提供 /navigate_to_pose 动作接口。

🤖 ROS2 小车通信流程图

```
   🎮 手柄节点 (joy_node)
          |
          |  发布 /cmd_vel (Twist 消息)
          v
   🚗 底盘控制节点 (base_node)
          |
          |  接收速度指令 -> 控制电机
          |
   ------------------------------
          | 提供服务 reset_odometry
          | (清零里程计)
          |
          | 提供动作 navigate_to_pose
          | (带反馈的导航到目标点)
   ------------------------------
```

手柄节点 (joy_node)

采集手柄输入，转化为速度指令。

发布话题 /cmd_vel（消息类型 geometry_msgs::msg::Twist）。

底盘控制节点 (base_node)

订阅 /cmd_vel，根据指令驱动电机。

同时还可以提供：

服务 /reset_odometry → 调用后清零里程计。

动作 /navigate_to_pose → 机器人执行“导航任务”，中途不断返回反馈，最后告知是否到达。

👉 这样看就很清晰了：

话题 (Topic)：连续数据（手柄 → 小车）。

服务 (Service)：一次性操作（清零里程计）。

动作 (Action)：长时间任务（导航到某点）。

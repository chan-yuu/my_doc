这里就是非常丰富的ros2 与isaacsim的联合空间了。但是今天必须写完。
首先我们学习的内容主要集中在了导入和控制入门，定时，传感器与控制，独立工作流程，ros2堆栈，额外的ros2 ominigraph节点，定制内容，部署，仿真控制等部分。
# URDF导入
之前我们在ros1中也是使用的urdf文件进行仿真的。这个例子里面，我们需要设置一个turtlebot3，使其能够行驶。
如果已经拥有一个关节绑定属性为USD格式的机器人，并且想要直接使用我们的ros2桥接器，需要使用ros2消息驱动turtlebot部分的教程【我非常讨厌这样的介绍，每一节该是什么就应该是什么，而不是每次都要链接链接的】。
## 入门
需要完成ros2安装，并且ros2扩展启用，设置好了必要的环境变量。
对于ros工作空间足够了解。
使用一下命令来安装xacro:
```bash
sudo apt install ros-$ROS_DISTRO-xacro
```
## 导入turtlebot URDF
先克隆turtlebot3的包：
```bash
git clone -b $ROS_DISTRO https://github.com/ROBOTIS-GIT/turtlebot3.git turtlebot3
```
找到urdf对应的文件夹目录
```bash
cd turtlebot3/turtlebot3_description/urdf
```
在同一终端中，预处理urdf文件，手动删除命名空间参数，并保存到tb3_burger_processed.urdf文件中：
```bash
namespace=""
xacro ./turtlebot3_burger.urdf "namespace:=${namespace:+$namespace/}" > tb3_burger_processed.urdf
```
在本系列里面，使用Isaac环境。之后，可以将robot导入到任何环境中。打开环境的方法就是打开isaacsim的内容浏览器，然后点击 **Isaac Sim/Environments/Simple_Room/simple_room.usd**。如果不像使用提供的环境，需要确保环境中包含ground plane和physicsscene，这两个组件都可以在创建->物理中找到。可能需要一些光照，在创建->光照中尝试不同的光照类型，以获得理想的效果。
在新的stage上，将simple_room.usd拖到里面，并通过将变换、树形中所有的平移分量归零，将其放置在原点，可能需要稍微放大才能观察到房间内的桌子。
点击文件，导入，然后找到入洞房文件并选中它。
在提示窗口中，选择参考模型，在链接部分，设置为可移动基座。由于这是一个移动机器人，在关节和驱动部分将wheel_left_joint和wheel_right_joint的目标改为速度，以便稍后可以正确驱动车轮。
需要确认robot的配置和下面的配置相符合：
![](assets/ros2%20tutorials/file-20260319012636351.png)
然后点击导入
将资源导入isaacsim之后，系统就会自动保存一份.usd格式的资源副本，如果要讲资源保存到与.urdf文件所在文件夹不同的文件夹，可以在usd输出中指定保存文件夹。系统会在指定的目录下创建一个与.urdf文件同名的文件夹，并将.usd文件放在该新创建的文件夹中。
turtlebot首次导入时会放在桌子上，使用小工具将其放置在房间地板上方。
按下播放键，确认可以观察到turtlebot掉到了地上。
### 调整机器人
导入urdf文件时，如果材料，物理和关节属性在NVIDIA Isaac sim中可用且类别匹配，则会自动导入这些属
性，但是，如果不存在或匹配的类别，或者两个系统之间的单位不同，则自动填充的内容可能不准确，从而改变机器人的行为。以下就是一些可以调整的属性，用于修正机器人的行为。
### 摩擦特性
如果你的机器人轮子经常打滑，请尝试按照教程2中的步骤，改变轮子的摩擦系数，并可能改变地面的摩擦系数。
### 物理性质
如果没有明确指定质量或者惯性属性，物理引擎会根据几何网格估算它们。要更新质量和惯性属性，请找到包含给定链接刚体的基本体。可以通过在其属性选项卡下查找物理->刚体来验证，如果物理属性选项卡下已经有了质量类别，请相应修改它们，如果尚无质量类别，可以单击属性选项卡顶部的添加按钮，选择物理->质量来添加它。
### 关节属性
如果机器人关节处出现振荡或运动速度过慢，请检查关节的**刚度和阻尼参数**。高刚度会使关节更快、更猛地响应目标位置，而高阻尼会使关节运动更平滑，但也会降低其运动速度。对于**纯位置驱动**，应设置**相对较高的刚度和较低的阻尼**。对于**速度驱动，刚度必须设置为零，但阻尼必须不为零。**
对于这款 Turtlebot 机器人，尝试将阻尼设置为 `10000000.0` 的值， 将刚度设置为 `0.0` 的值。【说明是一个速度驱动的机器人】
URDF 导入完成后，舞台上显示的机器人通常会被加载为[参考对象](https://docs.omniverse.nvidia.com/utilities/latest/common/glossary-of-terms.html#term-Reference "(in Omniverse Utilities)") 。这可以通过舞台树  [![eyecon](https://docs.isaacsim.omniverse.nvidia.com/5.1.0/_images/isaac_reference_eyecon.png)](https://docs.isaacsim.omniverse.nvidia.com/5.1.0/_images/isaac_reference_eyecon.png)中机器人图元上的橙色或蓝色箭头来确认。如果您在更改参数并保存时遇到问题，可以编辑参考对象指向的原始 USD 文件。要查找原始 USD 文件的文件路径，请导航至属性选项卡 ，然后转到 **“参考对象** ”> **“资源路径”** 。
本教程涵盖以下主题：
1. URDF 导入
2. 调整机器人参数

## 使用ros2驱动turtlebot消息
ros2桥接器自带一些常用的rostopic模块，这些模块经过打包，可以方便使用。本文介绍他们的使用步骤。将 NVIDIA Isaac Sim 连接到 ROS 的步骤如下：
- 使用用户界面
- 扩展工作流程中的脚本编写
- 在独立的 Python 工作流程中编写脚本
有关不同工作流程的详细信息，请参阅 [“工作流程”部分](https://docs.isaacsim.omniverse.nvidia.com/5.1.0/introduction/workflows.html#isaac-sim-app-tutorial-intro-workflows) 。本文演示了使用现有 Omnigraph 节点的 UI 方法。其他方法的介绍请参见 [“进阶学习”](https://docs.isaacsim.omniverse.nvidia.com/5.1.0/ros2_tutorials/tutorial_ros2_drive_turtlebot.html#isaac-sim-app-tutorial-ros2-drive-turtlebot-further-reading) 部分。
在这个例子中，你将让 [Turtlebot3](https://emanual.robotis.com/docs/en/platform/turtlebot3/overview) 机器人四处移动，并通过 ROS 网络订阅 Twist 消息。你将学习到：
- 为 Turtlebot3 添加控制器
- 介绍 ROS 2 桥接器和 ROS OmniGraph (OG) 节点
- 设置机器人，使其由 ROS2 Twist 消息驱动。
### 入门
前提条件：
- 拥有已配置的 Turtlebot，或已完成 [URDF 导入：Turtlebot](https://docs.isaacsim.omniverse.nvidia.com/5.1.0/ros2_tutorials/tutorial_ros2_turtlebot.html#isaac-sim-app-tutorial-ros2-turtlebot) 。
- 已完成 [ROS 2 安装](https://docs.isaacsim.omniverse.nvidia.com/5.1.0/installation/install_ros.html#isaac-sim-app-install-ros) ，以便在启动 NVIDIA Isaac Sim 之前设置并加载必要的环境变量，并且已启用 ROS2 扩展。
### 主要概念
#### 驱动机器人
在urdf导入完成后，turtlebot机器人拥有可驱动关节，当给定目标位置或速度时，它可以移动关节以匹配目标。通常，需要控制的是**车辆速度**，而不是单个车轮的速度。因此，需要添加相应的控制器。对于 Turtlebot3 这种双轮轮式机器人，所需的节点是 `Differential Controller` 和 `Articulation Controller` 。 `Differential Controller` 节点将车辆速度转换为车轮速度，而 `Articulation Controller` 节点则向关节驱动器发送指令。
有关如何连接节点的更多说明，请参阅 [Isaac Sim Omnigraph 教程](https://docs.isaacsim.omniverse.nvidia.com/5.1.0/omnigraph/omnigraph_tutorial.html#isaac-sim-app-tutorial-gui-omnigraph) 。
#### 连接到ros2
作为我们 ROS2 桥接器的一部分，其中包含一些订阅和发布特定消息的节点，以及一些实用节点，例如用于跟踪模拟时间和上下文 ID 的节点。您还会找到“辅助节点”，它们是通往更复杂的 OmniGraph 的入口，这些 OmniGraph 对用户来说是抽象化的。
要为特定话题建立 ROS2 桥接，步骤可以概括如下：
- 打开行动图表
- 添加与所需 ROS 2 主题相关的 OG 节点
- 根据需要修改任何属性
- 连接数据管道
ROS2 发布节点负责将 NVIDIA Isaac Sim 数据打包成 ROS 消息并发送到 ROS 网络，而订阅节点则负责接收 ROS2 消息并将其分配给相应的 NVIDIA Isaac Sim 参数。因此，要使用它们，您必须按照每个节点的属性指示，通过管道输入和输出必要的数据。如果您需要发布或订阅超出已提供消息范围的消息，请参阅 [“自定义 Python 节点”](https://docs.isaacsim.omniverse.nvidia.com/5.1.0/omnigraph/omnigraph_custom_python_nodes.html#isaac-sim-app-omnigraph-custom-python-nodes) 或 [“自定义 C++ 节点”](https://docs.isaacsim.omniverse.nvidia.com/5.1.0/omnigraph/omnigraph_custom_cpp_nodes.html#isaac-sim-app-tutorial-advanced-omnigraph-custom-cpp-nodes) 以了解如何集成自定义 OmniGraph 节点。
### 整合一体
#### 构建图表
1. 打开可视化脚本： **窗口 > 图形编辑器 > 动作图** 。 **动作图**窗口将出现在底部，您可以将其停靠在任何方便的位置。
2. 点击**操作图**窗口中间的 **“新建操作图”** 图标。
3. 在 **“操作图”** 窗口的左侧面板中，列出了所有 OmniGraph 节点（或 OG 节点）。所有与 ROS2 相关的 OG 节点都列在 _“Isaac Ros2”_ 下。您也可以按名称搜索节点。要将节点添加到图中，请将其从节点列表中拖到图窗口中。
4. 构建一个与下图相同的图表。
![](assets/ros2%20tutorials/file-20260319014543571.png)
<img src="assets/ros2%20tutorials/file-20260319014552064.png" width="311">
### 图表详解
- **回放滴答节点** ：当模拟处于“播放”状态时，产生一个滴答声。从该节点接收滴答声的节点将在每个模拟步骤中执行其计算函数。
- **ROS2 上下文节点** ：ROS2 使用 DDS 进行中间件通信。DDS 使用[域 ID](https://docs.ros.org/en/humble/Concepts/Intermediate/About-Domain-ID.html) 来允许不同的逻辑网络即使共享物理网络也能独立运行。同一域上的 ROS2 节点可以自由地相互发现和发送消息，而不同域上的 ROS2 节点则不能。ROS2 上下文节点使用给定的域 ID 创建一个上下文。默认情况下，域 ID 设置为 0。如果选中 “使用域 ID 环境变量” ，则会从启动当前 Isaac Sim 实例的环境导入 `ROS_DOMAIN_ID` 。
- **ROS2 订阅 Twist 节点** ：订阅 Twist 消息。在其属性选项卡的 _topicName_ 字段中指定 ROS 2 主题名称 `/cmd_vel` 。
- 订阅节点通常有一个执行输出 _(Exec Out)_ 字段。它的作用类似于时钟信号，当订阅节点收到消息时会发送一个信号。在这种情况下，无论何时收到新命令，差分控制器都必须每帧都触发一次时钟信号。因此，对于这种情况，差分节点的执行输入 _(Exec In)_ 是由_播放时钟信号_节点的输出触发，而不是由订阅节点触发。
- **缩放至/自舞台单位节点** ：将资产或输入转换为舞台单位。
- **分解三维向量节点** ：Twist 订阅节点的输出是线速度和角速度，均为三维向量。但差分控制器节点的输入仅包含前向速度和 z 轴旋转速度，因此必须先分解数组并提取相应的元素，然后才能将其输入到差分控制器节点。
- **差速控制器节点** ：此节点接收期望的车辆速度并计算机器人的车轮速度。它需要车轮半径和车轮间距来进行计算。它还可以接收可选的速度限制参数来限制车轮速度。在属性选项卡中输入车轮半径、车轮间距和车辆的最大线速度（如下表所示），以匹配 Turtlebot。

|                          |       |
| ------------------------ | ----- |
| Max Angular Speed  最大角速度 | 1.0   |
| Max Linear Speed  最大线速度  | 0.22  |
| Wheel Distance  车轮距离     | 0.16  |
| Wheel Radius  车轮半径       | 0.025 |
- **关节控制器节点** ：该节点分配给目标机器人，然后接收需要移动的关节的名称或索引，并根据_位置命令_ 、 _速度命令_或_力命令_中给出的命令移动它们。
- - “Articulation控制器”节点由 _“播放触发”事件_触发。因此，如果没有新的 Twist 消息到达，它将继续执行之前收到的任何命令。
- 要将_关节控制器_节点的目标指定为 Turtlebot，请在属性选项卡中单击 _“添加目标”_ ，然后在弹出框中搜索 Turtlebot 基元。确保您选择的机器人基元也是应用了关节根 API 的位置。有时它是机器人的父基元，但对于移动机器人来说，通常是底盘基元。如果您按照我们之前的教程导入了 URDF，则可以在 `/World/turtlebot3_burger/base_footprint` 上找到关节根 API。有关关节 API 的更多信息，请参阅 [“添加关节”](https://docs.isaacsim.omniverse.nvidia.com/5.1.0/robot_setup_tutorials/tutorial_gui_simple_robot.html#isaac-sim-app-tutorial-gui-simple-robot-articulation) 。如果关节根设置在 base_footprint 基元上，请从 `/World/turtlebot3_burger/base_footprint` 中移除关节根属性，并将[关节根属性添加](https://docs.isaacsim.omniverse.nvidia.com/5.1.0/robot_setup_tutorials/tutorial_gui_simple_robot.html#isaac-sim-app-tutorial-gui-simple-robot-articulation)到 `/World/turtlebot3_burger` 的主机器人基元上。
- 要将车轮关节的名称以数组格式放入，请在每个**常量标记**节点中输入车轮关节的名称，并将名称数组传递给**创建数组**节点。Turtlebot 的关节名称为 `wheel_left_joint` 和 `wheel_right_joint` 。
- 请勿将名称放在常量字符串节点中，因为 OmniGraph 没有字符串数组数据类型。如果节点需要使用数组格式的字符串，则必须使用_token_类型。
### 验证ros链接
1. 按下播放**键**开始运行图表和物理模拟。
2. 在另一个 ROS 终端中，使用 `ros2 topic list` 检查关联的 ROS 2 主题是否存在。验证 `/cmd_vel` 是否与 `/rosout` 和 `/parameter_events` 一起列出。
3. 现在，我们可以向 `/cmd_vel` 主题发布 Twist 消息来控制机器人。让我们用以下命令驱动它前进：
```bash
ros2 topic pub /cmd_vel geometry_msgs/Twist "{'linear': {'x': 0.2, 'y': 0.0, 'z': 0.0}, 'angular': {'x': 0.0, 'y': 0.0, 'z': 0.0}}"
```
要停止机器人移动，请发布零速度指令：
```bash
ros2 topic pub /cmd_vel geometry_msgs/Twist "{'linear': {'x': 0.0, 'y': 0.0, 'z': 0.0}, 'angular': {'x': 0.0, 'y': 0.0, 'z': 0.0}}"
```
为了方便我们移动 Turtlebot，请运行以下命令安装 `teleop_twist_keyboard` ：
```bash
sudo apt-get install ros-$ROS_DISTRO-teleop-twist-keyboard
# 使用键盘启用驾驶模式
ros2 run teleop_twist_keyboard teleop_twist_keyboard
```
### 故障排除
请确保您的机器人位于地面上。桌子的属性不同，机器人很难在其上移动。要更改地面或轮子的属性，请参阅[教程 3：设置基本机器人关节](https://docs.isaacsim.omniverse.nvidia.com/5.1.0/robot_setup_tutorials/tutorial_gui_simple_robot.html#isaac-sim-app-tutorial-gui-simple-robot) 。
本教程涵盖以下主题：
- 使用**差速控制器**和**关节控制器**驱动机器人。
- ROS 2 Bridge OmniGraph 节点简介
- 订阅 ROS2 Twist 消息
## ros2 clock
到了时钟的概念了，这里面我们需要：
- 简要讨论一下 ROS 2 时钟发布者和订阅者
- 将仿真时间作为时钟消息发布到 ROS 2。
- 订阅 ROS 2 时钟消息
- 使用菜单快捷键添加时钟动作图
### 入门
先决条件
完成 [ROS 2 安装](https://docs.isaacsim.omniverse.nvidia.com/5.1.0/installation/install_ros.html#isaac-sim-app-install-ros) 。
同样的，如果使用多个系统，请在启动 Isaac Sim 之前，按照 [ROS 2 安装](https://docs.isaacsim.omniverse.nvidia.com/5.1.0/installation/install_ros.html#isaac-sim-app-install-ros)中的说明设置 `FASTRTPS_DEFAULT_PROFILES_FILE` 环境变量，并在任何将发送或接收 ROS 消息的终端中设置该环境变量，并且启用 ROS 2 扩展。——我终于明白了，为什么需要多机的配置。按照道理来讲，ros的引入其实对训练是比较灾难性的，因为无头的服务器的训俩需要足够的保证。

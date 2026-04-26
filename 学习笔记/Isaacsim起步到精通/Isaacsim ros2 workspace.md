# 安装概要
Isaac sim 仅兼容python3.11，ros2 bridge的启用和接口功能都是使用的内部编译的这个ros2？
## 1.设置ros接口和软件包
1. 如果仅需要使用**默认或者通用的接口**与ros2 bridge进行交互，则可以使用本机安装的默认ros2
比如Ubuntu22，那就是默认安装humble，python3.10版本。
2. 运行Isaac sim时启用**内部**humble库，此时需要进行配置。并且内部库的python是3.11，尝试加载ros的默认安装版本会导致错误
## 2.设置ros工作区
启用ros2 bridge后，可以开始发布和订阅ros话题，此时需要设置Isaac sim的ros工作区
如果是默认的ros2接口，使用设置说明进行（这里的文档是非常不合理的，到处都是参考参考位置）导致整个文档非常混乱。
整个安装就是这两步，但是就如上面所说，非常的混乱，我们接下来根据这一部分的实际内容来重新排布一下。
# 安装ros 2
ros2的安装方式对应的是步骤1中的，决定了可以使用的ros2的功能，Isaac sim自带python 3.11。如果安装的ros2是其他python版本，可以使用rclpy？他已经预先打包在Isaacsim中，并使用python3.11编译？也就是Isaacsim内部的rcl库是吧。更多信息又参考python3.11启用rclpy、自定义ros2软件包和工作区。（对应的是第一种使用自己安装的python3.10的humble的ros2）
ROS_DISTRO环境变量确定是否加载了ros2已经哪个发行版。如果不设置就是使用内部的ros2发行版构建，不同ros2版本之间定义的消息可能有所不同，系统会根据加载的ros2发行版动态加载相应的ros2后端。
使用ros2 bridge时只需运行Isaac sim之前，从终端加载Isaacsim内部库或者ros2安装目录，如果ros2或内部库的加载已经包含在bashrc中，则可以直接运行Isaacsim。
然后安装ros2的直接用官方的方法即可，然后安装一下对应的一些依赖的msg:
```bash
sudo apt install ros-humble-vision-msgs
sudo apt install ros-humble-ackermann-msgs
```
然后又来了一句，对于linux系统，不能在运行Isaac sim 的同一终端中安装此软件包?运行之前，先安装Isaac sim的内部ros2 库（python3.11）
所以上述自己安装ros2的应该是基本作废的，我们需要使用内部的ros2以及python版本。并且是不需要安装的，在 Ubuntu 22.04 中，如果没有加载其他 ROS 库，Isaac Sim 会自动加载**内部的 ROS 2 Humble** 库。直接通过./isaac-sim.sh就是启动了内部的ros了。
# 配置选项并启用内部ros库
运行Isaac sim推荐的是python3.11的ros版本，所以从这句话就可以看出来系统默认的ros并不是官方想要我们使用的。此外，`./isaac-sim.sh`启动时就直接与Ubuntu版本绑定了，会自动加载内部的ros2库。
如果要使用终端来启动，指定特定的ros2库，就需要进行一定的配置，
Isaac Sim 自带一套精简的 ROS 2 库。要使用这些内部 ROS 2 库，您必须在运行 Isaac Sim 之前，在新终端或命令提示符窗口中设置以下环境变量。如果 Isaac Sim 安装在非默认位置，请将 `isaac_sim_package_path` 环境变量替换为 `Isaac Sim 应用程序选择器` 中显示的包路径。
```bash
export isaac_sim_package_path=$HOME/isaacsim

export ROS_DISTRO=humble

export RMW_IMPLEMENTATION=rmw_fastrtps_cpp

# Can only be set once per terminal.
# Setting this command multiple times will append the internal library path again potentially leading to conflicts
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$isaac_sim_package_path/exts/isaacsim.ros2.bridge/humble/lib

# Run Isaac Sim
$isaac_sim_package_path/isaac-sim.sh
```
# 启动ros2 桥
这里主要是介绍ros2的dds的配置，但是从上面的配置来看，这里依然是可以直接使用默认的fastdds的配置的。这里的ros2 bridge是Isaacsim里面的插件，从这个角度来说，可以把它就理解为Isaacsim插件的配置，这里配置依然与版本相关，一般就使用默认的ros2库就行。
isaac sim 和本机的通信是dds的，默认是fastdds不需要进行修改，当然也可以配置其他的dds，比如，使用cyclone_dds。
# 使用cyclone dds启动ros bridge
由于内部ros2这个内容，所以需要使用系统的ros2环境进行使用。然后运行 Isaac Sim 之前，请确保设置 `RMW_IMPLEMENTATION` 环境变量。以后如果任何示例显示将环境变量设置为 `rmw_fastrtps_cpp` ，有了系统的ros2 humble环境之后，切换cyclone dds就很简单了，首先只需要下载安装，然后设置环境变量即可
```bash
sudo apt install ros-humble-rmw-cyclonedds-cpp
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
```
不过不建议替换，因为配置这个就意味着使用了系统安装的ros2环境？但是在后面的配置中，他又指出，Isaac sim ros2 humble 和jazzy内部库中是有python3.11编译的cyclone dds的。运行Isaac sim之前，需要设置RMW_IMPLEMENTATION的环境变量。如果任何显示将环境变量设置为`rmw_fastrtps_cpp`，可以使用rmw_cyclonedds_cpp来替换。ROS 2 桥接器会动态地从您指定的 ROS 2 安装路径中加载相应的 ROS 2 版本。如果您没有指定 ROS 2 安装路径，ROS 2 桥接器将加载桥接器的默认 ROS 2 发行版。
# 禁用ros bridge
关闭的方式就不说了，具体是修改的isaac-sim.sh文件来实现的禁用。
# 设置工作区
如果我没有记错的话这个就是第二步的东西了，怎么配置工作区？构建工作区之前：
- 先确保ros2已经安装（再说一下，这里反复的给参考链接，同时对于内部和系统ros2的界定非常随意，导致就是非常混乱）
- 克隆ros工作区存储库
- 如果是源码安装的ros2，在构建工作区之前先将`source /opt/ros/<ros_distro>/setup.bash` 命令替换为 `source <path_ros2_ws>/install/setup.bash` 。这是什么意思？
总的来说，就是克隆然后编译然后需要source：
git clone https://github.com/isaac-sim/IsaacSim-ros_workspaces.git
cd IsaacSim-ros_workspaces
git submodule update --init --recursive
# 使用python3.11启用rclpy、自定义ros2软件包和工作区
这是之前让我们跳转的位置所在，但是我们要清楚的是，这里到底是想要教会我们什么问题。其实开头就明确了，我们如果想要使用rclpy和自定义的ros2软件包，必须使用python3.11来构建。这里就解释了一个非常关键的东西，dockerfile的意义。这个docker环境就是一个使用了python3.11来构建的ros2环境。
所以很明显，我们必须使用这个docker环境来构建，rclpy是ros2的底层库，它是不是也是在这个docker里面呢？接下来就是介绍怎么构建docker环境了：
```bash
cd IsaacSim-ros_workspaces

./build_ros.sh -d humble -v 22.04

# 打开一个新的终端，并执行 ROS 2 Python 3.11 构建：
source build_ws/humble/humble_ws/install/local_setup.bash
# 在同一终端中，运行已构建的 ROS 2 工作区：
source build_ws/humble/isaac_sim_ros_ws/install/local_setup.bash
# 在同一终端运行 Isaac Sim。加载的工作区包含启用 ROS 2 桥接所需的最小 ROS 2 Humble 依赖项。

# 要运行外部节点，请按照本节中的说明在默认 ROS 发行版中重建工作区： [设置工作区](https://docs.isaacsim.omniverse.nvidia.com/5.1.0/installation/install_ros.html#isaac-ros-workspace) 。

```
# 包含的ros软件包
说实话，到这里我仍没有完全理解整个构成。这里介绍的软件包我不知道是哪里的，可能是工作区，可能是docker，可能是系统，可能是内部库。
不过这里的软件包都是比较实用的，下面是他们的一些简单介绍。
- **carter_navigation** ：包含 NVIDIA Carter 机器人所需的启动文件和 ROS 2 导航参数。
- **cmdvel_to_ackermann** ：包含一个脚本文件和一个启动文件，用于将命令速度消息（Twist msg 类型）转换为 Ackermann Drive 消息（AckermannDriveStamped msg 类型）。
- **custom_message** ：包含 NVIDIA Carter 机器人所需的启动文件和 ROS 2 导航参数。
- **h1_fullbody_controller** ：包含运行 H1 人形机器人全身控制器所需的启动文件、参数和脚本。
- **isaac_moveit** ：包含运行 Isaac Sim 与 MoveIt2 堆栈的启动文件和参数。
- **isaac_ros_navigation_goal** ：用于在 ROS 2 Navigation 中自动设置随机或用户定义的目标姿态。
- **isaac_ros2_messages** ：一组自定义的 ROS 2 服务接口，用于检索姿态、列出图元以及操作其属性。
- **isaacsim** ：包含用于运行和启动 Isaac Sim 作为 ROS 2 节点的启动文件和脚本。
- **isaac_tutorials** ：包含教程系列的启动文件、RViz2 配置文件和脚本。
- **iw_hub_navigation** ：包含 iw.hub 机器人所需的启动文件和 ROS 2 导航参数。
但是要注意，新加的软件包，需要每次都加载ros2工作区，然后在同一终端运行isaacsim，这里说的比较明确了，但是这里的工作区应该就是workspace的位置的环境配置吧。
# docker中运行ros
既然这个docker是用python3.11构建，那么我们可能就只能在这里面添加软件包了。即使它在最后的部分，我们依然是需要好好的认识一下这个的。
首先需要克隆对应的ros工作区。
然后导航到仓库并确保git依赖。
然后运行相应的docker容器，进行仓库的挂载。
```bash
xhost +

docker run -it --rm --net=host --env="DISPLAY" --env="ROS_DOMAIN_ID" -v ~/IsaacSim-ros_workspaces/humble_ws:/humble_ws --name ros_ws_docker osrf/ros:humble-desktop /bin/bash
```
> 注意给正确的路径即可。这里， `--net=host` 允许 Isaac Sim 和 ROS Docker 容器之间进行通信，而 `xhost +` 和 `--env="DISPLAY"` 则有助于传递 DISPLAY 环境变量，从而使 `rviz` 等 GUI 应用程序能够从 Docker 容器中打开。` `--name <container name>` 允许我们使用固定名称来引用容器。

在 Docker 容器内导航至 ROS 工作区。
cd /${ROS_DISTRO}_ws # 注意：既然是这个命令，那就说明docker容器中的文件夹是基本指定好了的。
在 Docker 容器内，按照说明设置 `FASTRTPS_DEFAULT_PROFILES_FILE` 环境变量。？？？这个file又是什么东西，不是只有一个fastdds的export吗，怎么还有其他文件。
安装其他的依赖然后构建工作区：

```bash
cd /${ROS_DISTRO}_ws
apt-get update
git submodule update --init --recursive # If using docker, perform this step outside the container and relaunch the container
rosdep install --from-paths src --ignore-src --rosdistro=$ROS_DISTRO -y
source /opt/ros/$ROS_DISTRO/setup.sh
colcon build
source install/local_setup.bash
```

如果需要打开一个新的终端，请打开现有的 Docker 容器：

```bash
docker exec -it ros_ws_docker /bin/bash -c 'source /opt/ros/$ROS_DISTRO/setup.bash; exec bash'
```
（可选）要测试您的安装，您可以使用 Omnigraph 节点在 Isaac Sim 中设置一个基本的时钟发布器 [Isaac Sim Omnigraph 教程](https://docs.isaacsim.omniverse.nvidia.com/5.1.0/omnigraph/omnigraph_tutorial.html#isaac-sim-app-tutorial-gui-omnigraph) 。
1. 在模拟器中按下播放键 。
2. 打开一个单独的终端，打开 Docker，设置 `FASTRTPS_DEFAULT_PROFILES_FILE` 环境变量。
3. Source ROS 2. 
4. 验证 `ros2 topic echo /clock` 是否打印来自 Isaac Sim 的时间戳。
![](assets/Isaacsim%20ros2%20workspace/file-20260318203743315.png)
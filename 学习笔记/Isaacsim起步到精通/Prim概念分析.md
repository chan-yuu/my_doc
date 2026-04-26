### Prim 是什么？

在 USD（Universal Scene Description，通用场景描述）和 Isaac Sim/Omniverse 中，**Prim（全称 Primitive）** 是场景里最基础的可寻址对象单元，可以理解为场景里的「最小独立对象」或「节点」。

### 核心理解

可以把整个场景看成一棵树，**Prim 就是树上的每一个节点**。就像在文件系统里，`/World/GR1_T1/waist_roll/visuals` 是一个完整路径，每一段 `/` 分隔的都是一个 Prim。

Prim 是 USD 里承载数据的基本容器，它可以包含几何数据（Mesh、Shape）、变换信息（位置/旋转/缩放）、材质、灯光、相机、语义标签、物理属性、自定义数据等。任何你能在场景里看到、选中、编辑的东西，本质上都是一个 Prim。

### 关键特征

**唯一路径（Prim Path）**：每个 Prim 在场景里都有唯一路径，比如 `/World/GR1_T1/waist_roll/visuals`，这个路径就像它的「身份证号」，用来精确引用和操作这个对象。

**层级结构（Hierarchy）**：Prim 可以嵌套形成父子关系，比如 `/World` 是根 Prim，下面有 `/World/GR1_T1`（机器人整体），再下面有 `/World/GR1_T1/waist_roll`（腰部关节），子 Prim 会继承父 Prim 的变换、可见性等属性。

**类型（Kind/Type）**：Prim 有不同类型决定用途：`Mesh` 是网格几何体，`Xform` 是纯变换节点用来组织层级，`Camera` 是相机，`Light` 是灯光，`Material` 是材质。你在属性面板里看到的 `Kind` 就是用来标记 Prim 类型的。

**可扩展属性**：每个 Prim 都可以附加任意属性：位置、材质、语义标签、物理参数等，非常灵活。

> 相关阅读：[[带我学界面——属性编辑]]、[[安装]]
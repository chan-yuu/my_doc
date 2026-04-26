首先在基本的世界中，左键选中，右键旋转，中间拖动。

# content

![image.png](attachment:89ff9efc-ff9d-454f-80bf-4ad5bc68d123:image.png)

content中表征的是可以管理的资产，相当与资源管理器，用于管理3D场景、机器人和配置文件的。isaacsim是核心资产根目录，robots存放机器人模型，environments存放场景环境，props存放场景中的小物件。isaac包含一些底层资产。

omniverse是通用资产，包括一些基础材质、灯光等。

带锁说明的是他们只是只读资产，属性详情中包含的是modify修改时间，size文件大小，checkpoints是版本快照。

# preferences

## animation-动画

![image.png](attachment:3f9269c3-f139-4361-8737-a004ff5c3b4f:image.png)

这里是isaac的动画的设置，首先display time as，时间显示模式，这里用的是帧数的形式。还有smpte和seconds的模式。

playback 回放设置，直接影响物理仿真精度，其中fixed time step是固定时间步长，也就是仿真器每次更新物理状态的时间间隔。常见是0.01s或者0.002或者更小，如果太大，物理计算比较粗糙，可能穿模，如果太小，仿真会变慢，但是物理结果更加精确。

### ▶️ Playback Settings（播放设置）

- **Fixed Time Step**：固定时间步长
    - 勾选后，物理 / 动画以固定间隔更新，保证稳定性
- **Play Every Frame**：逐帧播放
    - 勾选后会强制渲染每一帧，不跳帧，适合慢动作预览
- **Wall Clock Time Delay Compensation**：实际时间延迟补偿
    - 数值：`0.0`，用于修正播放时与真实时间的偏差
- **Steps Per Frame**：每帧步数
    - 数值：`1`，控制每帧内物理 / 动画更新的次数
- **Snap Timeslider to Frame**：时间滑块吸附到帧
    - 勾选后，拖动时间轴会自动对齐到整数帧，方便精准编辑

### 🦴 UsdSkel Settings（USD 骨骼设置）

- **Enable BlendShape**：启用混合形状（BlendShape）
    - 用于面部表情、肌肉变形等顶点变形动画

### 📈 Curve Settings（曲线设置）

- **AutoKey All Transforms**：自动为所有变换打关键帧
    - 勾选后，修改物体位置 / 旋转 / 缩放时自动生成关键帧
- **Default Tangent Type**：默认切线类型
    - 当前：`auto`（自动），控制关键帧间的插值曲线形态
- **Weighted Tangent by Default**：默认使用加权切线
- **Broken Tangent by Default**：默认断开切线（左右切线独立控制）
- **Default Pre-Infinity Type**：关键帧前无穷大插值类型
    - 当前：`constant`（恒定），即关键帧前保持第一个关键帧的值不变
- **Default Post-Infinity Type**：关键帧后无穷大插值类型
    - 当前：`constant`（恒定），即关键帧后保持最后一个关键帧的值不变
- **Delete Empty Curve**：删除空曲线
    - 勾选后，自动清理没有关键帧的动画曲线
- **Run Backwards Compatibility on Folder**：对文件夹运行向后兼容
    - 用于修复旧版本文件兼容性问题

## audio-音频

![image.png](attachment:f18cd067-8df1-43d3-9954-380a121150f0:image.png)

### 🔊 Audio Output（音频输出）

- **Output Device**：输出设备
    - 当前：`Default Device (乱码/IEC958)` → 系统默认音频输出设备（乱码是编码问题，实际是默认声卡 / HDMI 音频）
- **Input Device**：输入设备
    - 当前：`No audio device is connected` → 未连接音频输入设备（如麦克风）
- **Speaker Configuration**：扬声器配置
    - 当前：`auto-detect` → 自动检测（如立体声、5.1 环绕声等）
- **Refresh**：刷新设备列表
- **Apply**：应用设置

---

### 🎛️ Audio Parameters（音频参数）

- **Auto Stream Threshold (in Kilobytes)**：自动流阈值（KB）
    - 当前：`0` → 控制音频流自动加载的大小阈值，0 表示无限制或立即加载

---

### 🎧 Audio Player Parameters（音频播放器参数）

- **Auto Stream Threshold (in Kilobytes)**：自动流阈值（KB）
    - 当前：`256` → 音频播放器的预加载阈值，超过 256KB 时自动开始流式播放
- **Close Audio Player on Stop**：停止时关闭播放器
    - 未勾选 → 停止播放后不自动关闭音频播放器实例

---

### 🔊 Volume Levels（音量级别）

- **Master Volume**：主音量
    - `1.0` → 全局总音量（1.0 为最大，0.0 为静音）
- **USD Volume**：USD 场景音频音量
    - `1.0` → 控制 USD 场景中音频资产的音量
- **Spatial Voice Volume**：空间化语音音量
    - `1.0` → 控制 3D 空间中带方位感的语音 / 音效音量
- **Non-spatial Voice Volume**：非空间化语音音量
    - `1.0` → 控制无方位感的 UI 语音 / 提示音音量
- **UI Audio Volume**：界面交互音效音量
    - `1.0` → 控制按钮点击、弹窗等 UI 音效的音量

---

### 🐞 Debug（调试）

- **Stream Dump Filename**：流转储文件名
    - 用于调试时将音频流数据导出到指定文件
- **Enable Stream Dump**：启用流转储
    - 未勾选 → 不导出音频流数据（仅调试用）

## capture screenshot

![image.png](attachment:cb53f6ee-9510-4bd9-8d2c-0b73186d67d0:image.png)

### 📸 Capture Screenshot（截图设置）

- **Path to save screenshots**：截图保存路径
    - 当前路径：`/home/cyun/Documents/Kit/shared/screenshots/`
    - 作用：指定截图文件的存储位置，右侧文件夹图标可点击浏览并修改路径。
- **Capture only the 3D viewport**：仅截取 3D 视口
    - 已勾选 ✅
    - 作用：截图时只包含 3D 场景视图区域，不会捕获整个软件界面（如菜单栏、属性面板等），适合只需要场景内容的场景。

## Datetime Format

![image.png](attachment:cab66f6e-40d0-4614-b63c-fd0abd74ff14:image.png)

### 📅 Datetime Format（日期时间格式）

- **Display Date As**：日期显示格式
    - 当前选项：`MM/DD/YYYY`
    - 这是美式日期格式，含义为：**月 / 日 / 年**（例如：03/27/2026）。
    - 下拉菜单可切换为其他格式（如 **DD/MM/YYYY** 日 / 月 / 年、**YYYY-MM-DD** 年 - 月 - 日 等），用来控制软件界面中时间、文件名等日期的展示规范。

## developer

![image.png](attachment:676537eb-8786-4ce9-803c-5d8e15a37590:image.png)

### Throttle Rendering 模块

### 🔄 线程与渲染节流

- **Global Thread Synchronization Preset**：全局线程同步预设
    - 用于快速加载预设的多线程同步配置，优化多核 CPU 利用率。
- **Async Rendering**：异步渲染 ✅
    - 启用后，渲染任务与主线程分离，提升界面响应速度，避免操作卡顿。
- **Skip Rendering While Minimized**：最小化时跳过渲染
    - 未勾选 → 窗口最小化时仍会继续渲染；勾选可节省 GPU 资源。
- **Yield 'ms' while in focus**：窗口获得焦点时让出 CPU 时间（毫秒）
    - 当前值：`0` → 不主动让出 CPU，保持最高响应速度。
- **Yield 'ms' while not in focus**：窗口失去焦点时让出 CPU 时间（毫秒）
    - 当前值：`0` → 不主动让出 CPU；若设为正数，可在后台时降低资源占用。

---

### 🎮 FPS 与帧率控制

- **Enable UI FPS Limit**：启用 UI 帧率限制 ✅
    - 限制界面渲染帧率，避免无意义的高帧率消耗资源。
- **UI FPS Limit uses Busy Loop**：UI 帧率限制使用忙等循环
    - 未勾选 → 用休眠方式限制帧率，CPU 占用更低；勾选则用循环等待，帧率更精确。
- **UI FPS Limit**：UI 帧率上限
    - 当前值：`120.0` → 界面渲染最高 120 帧 / 秒。
- **Use Fixed Time Stepping**：使用固定时间步长 ✅
    - 保证物理 / 动画更新间隔固定，提升模拟稳定性。
- **Use Present Thread**：使用独立呈现线程
    - 未勾选 → 渲染与呈现在同一线程；勾选可分离渲染和画面输出，提升效率。
- **Sync Threads and Present Thread**：同步工作线程与呈现线程
    - 未勾选 → 允许异步呈现；勾选可保证画面与逻辑同步，避免撕裂。
- **Present Thread FPS Limit**：呈现线程帧率上限
    - 当前值：`60.0` → 画面输出最高 60 帧 / 秒。
- **Sync Present Thread to Present After Rendering**：同步呈现线程到渲染完成后
    - 未勾选 → 允许提前呈现；勾选可确保画面是完整渲染结果。
- **Vsync**：垂直同步
    - 未勾选 → 不与显示器刷新率同步，可能出现画面撕裂；勾选可消除撕裂但可能增加输入延迟。

---

### 🖼️ Mip Mapping in ui.Image

- **Generate Mips**：生成 Mipmap
    - 未勾选 → 不对 UI 图片生成多级渐远纹理；勾选后可提升缩放时的渲染性能和画质，但会占用更多内存。

## Live

![image.png](attachment:a291c730-9122-4efa-b32d-1e110f567ece:image.png)

- **Quick Join Enabled**：启用快速加入 ✅
    - 开启后，可一键快速加入最近的实时协作会话，无需手动查找和输入会话信息。
- **Session List Select**：会话列表选择
    - 当前选项：`LastSession`（上次会话）
    - 用于指定默认加入的会话，可切换为历史会话列表中的其他会话，方便快速重连之前的协作环境。

## material

![image.png](attachment:33c44cc0-78c6-41f4-9fb6-0730a8e5197f:image.png)

- **Binding Strength**：绑定强度
    - 当前：`weakerThanDescendants`
    - 含义：材质绑定优先级弱于子节点，子对象的材质会覆盖父对象的材质。
- **Drag/Drop Path**：拖放路径
    - 当前：`Relative`
    - 含义：拖放材质时使用相对路径引用，方便场景文件迁移。

### 🧩 渲染上下文 / 材质网络

- **MDL / MaterialX / Default**：材质系统选择
    - 当前选中：`MaterialX`
    - 含义：选择底层材质定义标准，`MaterialX` 是开放的跨渲染器材质格式，`MDL` 是 NVIDIA 的材质定义语言。

### 🗄️ Material Cache（材质缓存）

- 缓存路径：`/home/cyun/cache/ov/Kit/107.3/69cbf6ad/material_cache.json`
- **Clear Cache**：清除缓存
    - 作用：清理已编译的材质缓存，解决材质更新不生效或加载异常的问题。

---

### ⚙️ Properties（属性显示）

- **Display Base Shader Properties on Material Prim** ✅
    - 作用：在材质根节点上直接显示基础着色器属性，方便快速编辑。
- **Force raw colorspace**：强制原始色彩空间
    - 当前：`anisotropy,anisotropy_rotation,metalness,normal,occlusion,opacity`
    - 作用：指定这些材质属性使用线性原始色彩空间，避免 sRGB 转换导致的渲染偏差。

---

### 🧪 MaterialX（MaterialX 专属设置）

> 红色提示：**修改以下选项需要重启应用**

- **Disable Deduplication**：禁用去重
- **Export Document**：导出文档
- **Validate**：启用验证
- **Serial Generation**：序列生成
- **Export Directory**：导出目录
    - 作用：控制 MaterialX 文档的导出、验证和序列化行为，需重启生效。

![image.png](attachment:2c033134-a15c-4d4d-89a1-5b2cbd5be708:image.png)

### 📂 Material Search Path（材质搜索路径）

- **Ignore Standard Paths**：忽略标准路径
    - 未勾选 → 保留默认系统 / 用户材质路径。
- **Standard System Paths**：标准系统路径
- **Standard User Paths**：标准用户路径
- **Additional User Paths**：额外用户路径
- **Renderer Required**：渲染器必需路径
- **Renderer Templates**：渲染器模板路径
- **Custom Paths (requires app restart)**：自定义路径（需重启）
    - 作用：添加自定义材质库目录，扩展软件可识别的材质资源位置。

![image.png](attachment:439abad6-d5df-4f8d-84c1-d6a217567d8b:image.png)

### 📊 Material Graph（材质图节点管理）

- **User Allow List**：用户允许列表
    - 当前：`replicator_definitions`、`infinite_pbr_material`、`project_pbr_material`
    - 作用：仅允许在材质图中使用这些节点库，避免混乱。
- **User Block List**：用户阻止列表
    - 作用：禁止在材质图中使用指定节点，用于规范工作流或屏蔽问题节点。

## metrics assembler

![image.png](attachment:efc417cc-b8a1-4b3b-b58b-c8700e90d20f:image.png)

### 📊 Metrics Assembler（指标汇编器）设置

### 🔧 General（通用设置）

- **Metrics Assembler Operation Mode**：指标汇编器操作模式
    - 当前：`Auto Resolve`（自动解析）
    - 作用：自动处理场景中单位、坐标等度量相关的冲突，确保场景数据一致性。
- **Metrics Assembler Parameters Change Listener Enabled** ✅
    - 作用：启用参数变更监听，当场景度量相关参数修改时，自动触发汇编器更新，保证数据实时同步。
- **Metrics Assembler Conform To XformCommonAPI (do not add unitsResolve xformOps)**
    - 未勾选
    - 作用：若勾选，将严格遵循 `XformCommonAPI` 变换规范，不额外添加单位解析相关的变换操作，兼容旧版 API。

## onshape importer

![image.png](attachment:c0382bf6-fd22-4ad3-ae8d-7a38a0d2c5fb:image.png)

### 📐 Onshape Importer（Onshape 模型导入器）设置

### 🔧 General（通用设置）

- **Use open stage folder for Imported Assembly** ✅
    - 作用：导入装配体时，使用当前打开的场景文件夹作为存放位置，方便资产管理。
- **Default Import Folder**：默认导入文件夹
    - 当前路径：`/home/cyun/Documents`
    - 作用：指定导入模型的默认保存目录，右侧文件夹图标可修改路径。
- **Rig Physics when importing** ✅
    - 作用：导入时自动为模型绑定物理约束，便于直接进行物理模拟。
- **Filter Unsupported document elements**
    - 未勾选
    - 作用：若勾选，将自动过滤 Onshape 文档中不支持的元素（如某些特殊约束或特征），避免导入失败。

---

### 🧩 Tessellation（曲面细分 / 三角化设置）

- **Chord Tolerance (m)**：弦公差（米）
    - 当前：`0.001`
    - 作用：控制曲面细分精度，数值越小，三角面越多，模型越精细但性能开销越大。
- **Angle Tolerance (d)**：角度公差（度）
    - 当前：`15.0`
    - 作用：控制相邻面之间的最大允许角度，角度越小，曲面过渡越平滑。
- **Max Cord (m)**：最大弦长（米）
    - 当前：`0.2`
    - 作用：限制单个三角面的最大边长，避免出现过大面片导致渲染 / 物理问题。

---

### 🔐 Authentication（认证设置）

- **Base Url**：基础地址
    - 当前：`https://cad.onshape.com`
    - 作用：Onshape 云端 CAD 服务的基础访问地址。
- **Auth Url**：认证地址
    - 当前：`https://oauth.onshape.com/oauth/authorize`
    - 作用：OAuth 授权请求地址，用于登录 Onshape 账号。
- **Token Url**：令牌地址
    - 当前：`https://oauth.onshape.com/oauth/token`
    - 作用：获取访问令牌的地址，用于 API 权限验证。
- **Use API Authentication**
    - 未勾选
    - 作用：若勾选，将启用 API 认证模式，可直接通过 API 访问 Onshape 云端模型。

## physics

![image.png](attachment:55d21cc0-46c4-4ad3-b31d-6e7f14933028:image.png)

### 🧪 Physics（物理模拟设置）翻译与解析

---

### ⚙️ General（通用设置）

- **Reset Simulation on Stop** ✅
    - 作用：停止模拟时自动重置场景状态，避免残留数据影响下一次运行。
- **Use Active CUDA Context (Requires Restart)**
    - 未勾选
    - 作用：使用当前活跃的 CUDA 上下文进行物理计算，需重启生效，可提升 GPU 加速效率。
- **Use Physics Scene Multi-GPU Mode**
    - 当前：`Disabled`（禁用）
    - 作用：多 GPU 物理场景模式，可分配物理计算到多张显卡，提升大规模场景性能。
- **Add Menu Selection Prim Limit**
    - 当前：`1000`
    - 作用：限制菜单中可选择的 Prim（场景对象）数量，避免 UI 卡顿。
- **Add Menu Subtree Prim Limit**
    - 当前：`100000`
    - 作用：限制子树结构中可选择的 Prim 数量上限。
- **Enable Attachment Authoring (Requires Stage Reload)** ✅
    - 作用：启用附件创作功能，可编辑物体间的绑定 / 附着关系，需重载场景生效。
- **Enable Particle Authoring (Requires Stage Reload)** ✅
    - 作用：启用粒子系统创作功能，可编辑流体、烟雾等粒子效果，需重载场景生效。
- **Enable Deformable Schema Beta (Requires Restart)**
    - 未勾选
    - 作用：启用可变形体 Schema 测试版，支持软体 / 布料模拟，需重启生效。
- **Default Physics Simulator**
    - 当前：`PhysX`
    - 作用：选择默认物理引擎，`PhysX`是 NVIDIA 的高性能实时物理引擎。
- **Reset Physics Preferences**
    - 作用：一键重置所有物理设置为默认值。

---

### 🚀 Simulator（模拟器设置）

- **Num Simulation Threads**
    - 当前：`8`
    - 作用：指定物理模拟使用的 CPU 线程数，充分利用多核 CPU。
- **Stop simulation after number of (PhysX) errors**
    - 当前：`1000`
    - 作用：当 PhysX 错误数达到阈值时自动停止模拟，防止程序崩溃。
- **Use PhysX CPU Dispatcher**
    - 未勾选
    - 作用：使用 PhysX 自带的 CPU 调度器，精细控制线程分配。
- **Expose PhysX SDK Profiler Data** ✅
    - 作用：暴露 PhysX SDK 性能分析数据，便于调试和优化。
- **Expose USD Prim Names to PhysX SDK Names** ✅
    - 作用：将 USD 场景对象名映射到 PhysX SDK 内部名称，方便调试定位。
- **Simulate empty scene**
    - 未勾选
    - 作用：允许对空场景运行模拟，一般用于测试。

---

### 🗄️ Local Mesh Cache（本地网格缓存）

- **Enable Local Mesh Cache (Takes Effect on Sim Start)** ✅
    - 作用：启用本地网格缓存，预编译碰撞网格，避免重复计算。
- **Local Mesh Cache Size MB (Takes Effect on Sim Start)**
    - 当前：`1024`（1GB）
    - 作用：设置缓存最大容量，平衡内存占用与加载速度。
- **Enable Ujitso Collision Cooking** ✅
    - 作用：启用 Ujitso 碰撞烘焙，并行处理碰撞网格生成，提升导入速度。
- **Ujitso Cooking Max Process Count**
    - 当前：`16`
    - 作用：限制碰撞烘焙的最大并行进程数，避免 CPU 过载。

## property widgets

![image.png](attachment:941af607-fdc3-499c-abfe-a9af1d6b4201:image.png)

### 🧩 Property Widgets（属性面板部件）设置

### 🪟 Property Window（属性窗口）

- **Large selections threshold**
    - 当前值：`100`
    - 作用：当选中的 Prim（场景对象）数量超过 100 时，自动隐藏大部分属性窗口内容，避免界面卡顿或假死。设为`0`可禁用此功能。
- **Raw Usd Properties Widget multi-selection limit**
    - 当前值：`1`
    - 作用：当选中的 Prim 数量超过 1 时，隐藏原始 USD 属性部件的内容，防止大量选择时性能下降。设为`0`可禁用此功能。
- **Update when Enter is pressed** ✅
    - 作用：启用后，仅在按下`Enter`或`Tab`键时才更新属性值，而非每次按键都实时更新，避免频繁输入导致的界面卡顿。
- **Large reference threshold**
    - 当前值：`5`
    - 作用：当 Prim 的引用数量超过 5 时，引用框架自动折叠，防止选择大量引用的对象时界面卡顿。设为`0`可禁用此功能。
- **References check missing paths** ✅
    - 作用：启用后，会检查引用中缺失的路径，但这可能在处理大量引用的 Prim 时导致额外卡顿。

## rendering

![image.png](attachment:90353597-604f-4d30-95a7-e9eceedd95c9:image.png)

### 🎨 Rendering（渲染设置）翻译与解析

---

### 🧵 Fabric Scene Delegate（Fabric 场景代理）

- **Enable Fabric delegate (preview feature, requires scene reload)** ✅
    - 作用：启用预览版 Fabric 场景代理，用于优化场景数据处理与渲染性能，需重载场景生效。
- **Advanced Fabric Scene Delegate Settings**
    - 高级配置入口，可进一步微调 Fabric 场景代理的底层参数。

---

### 🖼️ Texture Streaming（纹理流送）

- **Placeholder Texture Color (requires app restart)**
    - 当前：青色（R=0.0, G=1.0, B=1.0）
    - 作用：纹理未加载完成时显示的占位颜色，需重启应用生效。

---

### 🚀 RTX Geometry Streaming (experimental)（RTX 几何流送，实验性功能）

- **Enable geometry streaming (requires app restart)**
    - 未勾选
    - 作用：启用几何数据流式加载，适合超大型场景，按需加载模型几何，降低内存占用，需重启生效。

---

### 🖥️ Multi-GPU（多 GPU）

- **Multi-GPU**
    - 当前：`Auto`（自动）
    - 作用：自动检测并分配多 GPU 资源用于渲染，提升渲染性能。

---

### 🔍 Opacity MicroMap（不透明度微贴图）

- **Enable Opacity MicroMap**
    - 未勾选
    - 作用：启用不透明度微贴图，优化树叶、毛发等精细透明物体的渲染效率与质量。

---

### ✨ RTX Renderers（RTX 渲染器）

- **Enable RTX - Real-Time 2.0**
    - 未勾选
    - 作用：启用第二代实时光线追踪渲染器。
- **Enable RTX - Real-Time**
    - 未勾选
    - 作用：启用实时光线追踪渲染器。
- **Enable RTX - Interactive (Path Tracing)**
    - 未勾选
    - 作用：启用交互式路径追踪渲染器，用于高质量离线 / 预览渲染。

---

### ⚪ White Mode（白模模式）

- **Material**
    - 当前：`DebugWhite`
    - 作用：将场景所有材质替换为纯白调试材质，用于检查模型拓扑、光照与布局，不受材质影响。
- **Exceptions (Requires Scene Reload)**
    - 当前：`GizmoTex, Gizmo, OmniGlass, SunsetSkyMat`
    - 作用：指定在白模模式下**不被替换**的材质，保留 gizmo、玻璃、天空等关键视觉元素，需重载场景生效。

## resource monitor

![image.png](attachment:82207778-d885-4cad-96a1-4b9569729ef5:image.png)

### 🖥️ Resource Monitor（资源监控器）设置

### ⏱️ 监控基础设置

- **Time Between Queries**
    - 当前值：`100.0`
    - 作用：设置资源查询的时间间隔（单位：毫秒），即每 100ms 采集一次硬件资源数据。

---

### 📱 设备（GPU）内存警告

- **Send Device Memory Warnings** ✅
    - 作用：启用设备（GPU）内存占用警告。
- **Device Memory Warning Threshold (MB)**
    - 当前值：`1`
    - 作用：当 GPU 剩余内存低于此数值（MB）时触发警告。
- **Device Memory Warning Threshold (Fraction)**
    - 当前值：`0.1`
    - 作用：当 GPU 剩余内存占总内存比例低于此分数（10%）时触发警告。

---

### 💻 主机（CPU）内存警告

- **Send Host Memory Warnings** ✅
    - 作用：启用主机（CPU / 系统）内存占用警告。
- **Host Memory Warning Threshold (MB)**
    - 当前值：`1`
    - 作用：当系统剩余内存低于此数值（MB）时触发警告。
- **Host Memory Warning Threshold (Fraction)**
    - 当前值：`0.1`
    - 作用：当系统剩余内存占总内存比例低于此分数（10%）时触发警告。

## stage

![image.png](attachment:a90914ee-13f8-4cae-aa20-445a5d699cb4:image.png)

### 🌟 New Stage（新建场景）

- **Default Up Axis**：默认向上轴
    - 当前：`Z` → 场景默认使用 Z 轴为垂直向上方向（符合多数 3D 软件标准）
- **Default Animation Rate (TimeCodesPerSecond)**：默认动画帧率
    - 当前：`60.0` → 每秒 60 帧的时间码精度
- **Default Meters Per Unit**：默认单位米数
    - 当前：`1.0` → 1 个场景单位 = 1 米，保证物理模拟真实比例
- **Default Time Code Range**：默认时间码范围
    - 当前：`0.0` ~ `1000000.0` → 动画时间轴默认范围
- **Default DefaultPrim Name**：默认根节点名称
    - 当前：`World` → 场景根 Prim 命名为 `World`
- **Interpolation Type**：插值类型
    - 当前：`Linear` → 关键帧动画默认使用线性插值
- **Enable Static Material Network Topology**：启用静态材质网络拓扑
    - 未勾选 → 允许动态修改材质节点结构
- **Start with Transform Op on Prim Creation** ✅
    - 作用：创建 Prim 时自动添加变换操作（便于直接编辑位置 / 旋转 / 缩放）
- **Default Transform Op Type**：默认变换操作类型
    - 当前：`Scale, Orient, Translate` → 变换顺序：缩放 → 朝向 → 平移
- **Default Camera Rotation Order**：默认相机旋转顺序
    - 当前：`YXZ` → 相机旋转按 Y（偏航）→ X（俯仰）→ Z（翻滚）顺序执行
- **Default Rotation Order**：默认物体旋转顺序
    - 当前：`ZYX` → 物体旋转按 Z → Y → X 顺序执行
- **Default Xform Op Order**：默认变换操作顺序
    - 当前：`xformOp:translate, xformOp:orient, xformOp:scale` → 变换操作执行顺序：平移 → 朝向 → 缩放
- **Default Xform Precision**：默认变换精度
    - 当前：`Double` → 使用双精度浮点数存储变换数据，适合大场景

---

### ✍️ Authoring（创作编辑）

- **Keep Prim World Transform When Reparenting**：重设父级时保持世界变换
    - 当前：`Inherit Parent Transform` → 重设父级时继承新父节点的变换
- **Set "Instanceable" When Creating Reference**：创建引用时设为可实例化
    - 未勾选 → 引用默认不启用实例化（可节省内存但需手动开启）
- **Transform Gizmo Manipulates Scale/Rotate/Translate Separately (New)**：变换 gizmo 单独控制缩放 / 旋转 / 平移
    - 未勾选 → 新的 gizmo 交互模式，可独立操作各变换维度
- **Camera Controller Manipulates Scale/Rotate/Translate Separately (New)**：相机控制器单独控制缩放 / 旋转 / 平移
    - 未勾选 → 新的相机交互模式
- **Allow nested gprims authoring**：允许嵌套 gprims 创作
    - 未勾选 → 限制嵌套几何 Prim 创作，避免层级混乱
- **Unicode Normalization Method**：Unicode 标准化方法
    - 当前：`NFC` → 文本使用 NFC 标准化形式，保证跨平台兼容性

---

![image.png](attachment:6c7b84c3-39b7-4eb1-bcc7-e0e588c32277:image.png)

### 📥 Import（导入）

- **Drag & Drop USD Method**：拖放 USD 方式
    - 当前：`payload` → 拖放 USD 文件时以 payload 形式加载（延迟加载，优化性能）

---

### 📝 Logging（日志）

- **Mute USD Coding Error from USD Diagnostic Manager**：屏蔽 USD 诊断管理器的编码错误
    - 未勾选 → 保留 USD 编码错误日志，便于调试

---

### 🤝 Compatibility（兼容性）

- **Support unprefixed UsdLux attributes (USD <= 20.11)** ✅
    - 作用：兼容旧版 USD（≤20.11）中无前缀的 UsdLux 光照属性，避免旧资产加载失败

---

### 💾 Save options（保存选项）

- **Show save options automatically**：自动显示保存选项
    - 未勾选 → 保存时不弹出额外选项，直接按默认设置保存

## template startup

![image.png](attachment:378043f7-290a-4f87-81f8-008902151bce:image.png)

### 🎬 New Stage Template（新建场景模板）

- **Path to user templates**：用户模板路径
    - 当前路径：`${app_documents}/scripts/new_stage`
    - 作用：指定自定义场景模板的存放目录，`${app_documents}` 是应用文档目录的环境变量，方便模板文件的统一管理。
- **Default Template**：默认模板
    - 当前选项：`sunlight`
    - 作用：新建场景时自动加载 `sunlight` 模板，该模板通常包含默认太阳光、天空盒、基础光照等预设配置，可快速进入可渲染场景，无需手动搭建基础环境。

## viewport

![image.png](attachment:d60e0907-5931-4a26-809e-e6725c61cda2:image.png)

### 🎯 Viewport（视口）设置

### 🎬 Stage Open（场景打开）

- **Auto Frame**：自动框选 / 适配
    - 当前：`Off`（关闭）
    - 作用：打开场景时是否自动调整相机视角，使所有对象适配到视口内。
- **Single Camera Only** ✅
    - 作用：仅使用单个相机进行自动框选，避免多相机场景下视角混乱。
- **Implicit Cameras Only** ✅
    - 作用：仅使用场景内隐式相机（默认视角）进行自动框选，不影响用户自定义相机。

---

### ✂️ Selection（选择）

- **Area Select Occluded Objects**
    - 未勾选
    - 作用：框选时是否选中被遮挡的对象，勾选后可选择被其他物体挡住的 Prim。

---

### 🧰 Viewport Toolbar（视口工具栏）

- **Settings** ✅
    - 作用：在视口工具栏显示设置按钮。
- **Renderer** ✅
    - 作用：在视口工具栏显示渲染器切换按钮。
- **Display** ✅
    - 作用：在视口工具栏显示显示模式切换按钮（如线框 / 实体 / 光照等）。
- **Camera** ✅
    - 状态：`Collapsed`（折叠）
    - 作用：在视口工具栏显示相机控制按钮，当前为折叠状态。
- **SyntheticData** ✅
    - 作用：在视口工具栏显示合成数据相关按钮（用于数据集生成等）。
- **Lighting** ✅
    - 作用：在视口工具栏显示光照控制按钮。

## visual scripting

![image.png](attachment:a3902edb-a8ba-495e-9358-78f65f5c7bbe:image.png)

### 🧩 Visual Scripting（可视化脚本）设置

### 🔄 Update Settings（更新设置）

- **Update mesh points to Hydra**
    - 作用：将网格点数据更新同步到 Hydra 渲染引擎，确保可视化脚本中修改的网格数据能实时反映在视口渲染中。
- **Enable deprecated Node.pathChanged callback**
    - 作用：启用已弃用的 `Node.pathChanged` 回调函数，用于兼容旧版可视化脚本，避免历史逻辑报错。
- **Escalate all deprecation warnings to be errors**
    - 作用：将所有弃用警告升级为错误，强制开发者移除过时 API，保证脚本的长期可维护性。
- **Enable auto instancing**
    - 作用：启用自动实例化，对重复节点或对象自动生成实例化对象，优化场景内存占用与渲染性能。

> 相关阅读：[[带我学界面—启动页功能]]、[[带我学界面——属性编辑]]、[[安装]]
title: V2X场景发展和仿真
date: 2023-07-19 08:09:00
categories: selfdriving
tags: []
---
# V2X调研

# 一、Use Case

## 1. 用例分类

国汽车工程学会团体标准《协同智能交通系统；车辆通讯；应用层规范和数据交换标准”（T/CSAE 53-2017），定义了基于车路协同应用功能的17个典型应用场景，分为安全类、效率类和信息服务类，并发布了第二版第二阶段标准（T/CSAE 53-2020）在此基础上，针对安全、效率、信息服务、交通管理、先进智能驾驶等领域新增12个典型应用场景。欧洲电信标准化协会（ETSI）定义了52个车路协同应用场景，涵盖道路安全、交通效率等方面。第三代合作伙伴计划（3GPP）定义了27个基于LTE-V2X的应用场景（3GPP TR22.885）和25个基于5G-V2X的应用场景（3GPP TR22.886）

Day1和Day2是指V2X（Vehicle-to-Everything）技术的应用场景分类。

**Day 1 Use Cases**是指V2X技术的最初应用场景，主要是车辆安全和交通管理。这些应用场景包括：

- 前方障碍物预警
- 侧后方碰撞预警
- 交叉口危险提示
- 交通信号优化
- 交通流量管理
- 车道交替提示
**![](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2023/07/2573423467.png)**
**Day 2 Use Cases**是指V2X技术更加成熟后的应用场景，主要是车辆自主驾驶和智能交通管理。这些应用场景包括：

- 自动泊车
- 自动跟车
- 自动超车
- 智能路况预测
- 智能车辆调度
- 智能交通调度

![](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2023/07/285280660.png)

## [2.Day](http://2.Day) 1 和 Day 2 以外的场景

### (1) 高速公路车道线协调(day2?)

![](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2023/07/834551859.png)

V2P的场景

V2P通过手机、智能穿戴设备（智能手表等）等实现车与行人信号交互，在根据车与人之间速度、位置等信号判断有一定的碰撞隐患时，车辆通过仪表及蜂鸣器，手机通过图像及声音提示注意前方车辆或行人。

**1）道路行人预警**

行人穿越道路时，道路行驶车辆与人进行信号交互，当检测到具有碰撞隐患时，车辆会收到图片和声音提示驾驶员，同样行人收到手机屏幕图像或声音提示，这项技术非常实用，因为目前手机“低头党”非常多，过马路时经常有人只顾盯着手机屏幕，无暇顾及周边环境。

**2）倒车预警**

行人经过正在经过倒车出库的汽车时，由于驾驶员视觉盲区未能及时发现周边的人群（尤其是玩耍的儿童），很容易发生交通事故，这与借助全景影像进行泊车功能类似。

### (2) 远程驾驶
![](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2023/07/3218185360.png)


用户或者云计算服务器通过V2N的网络控制车辆自动驾驶。

### (3) 通过V2X优先紧急车辆的智能交通灯控制

![](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2023/07/143835845.png)

当交通灯系统被黑客攻击时,论文提出的应对措施主要包括:

1. 使用VANET网络使车辆之间进行实时通讯和信息共享,形成去中心化的交通管理,不完全依赖易受攻击的中心化交通控制系统。
2. 利用车载传感器和摄像头等设备检测交通状态,点对点地协调交通,降低对交通信号灯的依赖。
3. 紧急车辆(如救护车)通过车对车(V2V)通信广播优先级消息,要求其他车辆让行。其他车辆接收到后,自动让行或跟随紧急车辆通过。
4. 通过车载GPS和数字地图, Calculates 最优路径供紧急车辆导航,无需依赖交通信号指令。
5. 利用边缘计算服务器协助车辆运算和协调,减轻对车载计算能力的依赖。
6. 警察和政府部门利用管理平台对交通状态进行监控和协调,发布交通指令控制特定地点的车流。
7. 在发生攻击时,向司机发出警告,要求人工驾驶待交通系统恢复。

### (4) 智能安防

智能安防则是目前汽车领域的一个热点，通过V2X[车路协同](https://www.qxwz.com/solutions/autovehicle)系统可以实现车辆和周围环境的实时监控和预警，提高车辆的安全性和防盗性。

# 二、相关仿真

### ⭐️‣

![](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2023/07/999851822.png)

**仿真工具**

仿真工具中的功能模块具备以下特点：

1. 基础功能完善。该平台由Sumo与Carla联合搭建，包含了完善自动驾驶功能模块，如3D环境模块、交通流模块、传感器模块等。
2. 可拓展性强。用户可以依据自己的需求来使用OpenCDA平台。比如只是单纯了解交通性能，在Sumo上就可以完成；但假如在多车协同自动驾驶的基础上还要进一步了解通信模型，那么继续集成ns-3等工具也是可以的。

****合作驾驶系统****

合作驾驶系统中的算法具备以下特点：

1. 基础算法完善。OpenCDA平台包含了自动驾驶大脑的感知、规划、控制等算法。
2. 多车协同算法集成。OpenCDA平台额外添加了一个应用层，主要用于实施多辆自动驾驶车的协同行为。具体算法包括多车协同感知、协同定位、车辆编队等。
3. 模块化。上述所有模块都附带默认算法或协议，用户只需要一行代码即可在不影响其他部分的情况下，用自定义的算法或协议来替换默认设置。从而比较和评估算法性能。

****场景管理器****

场景管理具备以下模块：

1. 场景配置文件。OpenCDA在该部分中分为两个软件的场景定义。Carla中静态元素由xdor文件定义，Sumo中动态元素由yaml文件定义。
2. 场景初始化器。加载上述场景文件后，可以构建仿真环境、指导动态交通行为。
3. 评估函数。当仿真结束后，OpenCDA会分别从Carla的驾驶层级与Sumo的交通层级来分别做出评价。

**运行逻辑**

![](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2023/07/1836118110.png)

1. 用户需要根据 OpenCDA 提供的模板创建 yaml 文件来配置 CARLA 服务器的设置（例如同步模式与异步模式）、流量规格（例如数量）人类驾驶车辆的数量、生成位置）以及每个联网自动车辆的参数（例如，传感器参数、检测模型选择、目标速度）
2. 构建场景（仅限CARLA）。如果模拟只需要CARLA模拟器，那么给出yaml文件后，场景管理器将加载该文件并通过opencda.sim_api构建场景。用户需要首先将yaml文件加载到字典中，并初始化ScenarioManager。

```python
import opencda.scenario_testing.utils.sim_api as sim_api

# Aad yaml file into a dictionary
scenario_params = load_yaml(config_yaml)

# Create CAV world object to store all CAV VehicleManager info.
# this is the key element to achieve cooperation
cav_world = CavWorld(opt.apply_ml)

# create scenario manager
scenario_manager = sim_api.ScenarioManager(scenario_params,
                                           opt.apply_ml,
                                           town='Town06',
                                           cav_world=cav_world
```

之后，将生成排和单个 CAV。

```python
# create a list of platoon
platoon_list = scenario_manager.create_platoon_manager(
        map_helper=map_api.spawn_helper_2lanefree,
        data_dump=False)

# create a list of single CAV
single_cav_list = scenario_manager.create_vehicle_manager(application=['single'])
```

接下来，产生交通流。

```python
# create background traffic under Carla
traffic_manager, bg_veh_list = scenario_manager.create_traffic_carla()
```

最后，创建EvaluationManager

```python
from opencda.scenario_testing.evaluations.evaluate_manager import EvaluationManager
eval_manager = \
    EvaluationManager(scenario_manager.cav_world,
                      script_name='platoon_joining_town06_carla',
                      current_time=scenario_params['current_time'])
```

1. 构建场景（联合仿真）在联合仿真设置下构建场景与仅在 CARLA 中构建场景非常相似。只有两个区别：1）联合仿真需要额外的 Sumo 文件。 2）不使用ScenarioManager，而是使用CoScenarioManager来控制流量。

```python
import opencda.scenario_testing.utils.cosim_api as sim_api

# there should be a Town06.sumocfg, a Town06.net.xml, and a Town06.rou.xml in
# Town06 folder
sumo_cfg = 'Town06'

# create co-simulation scenario manager
scenario_manager = \
    sim_api.CoScenarioManager(scenario_params,
                              opt.apply_ml,
                               town='Town06',
                              cav_world=cav_world,
                              sumo_file_parent_path=sumo_cfg)
```

1. 单步运行
2. 保持模拟循环运行
3. 评估

## 样例

**合作驾驶测试**

1. 合并进入队伍

![https://opencda-documentation.readthedocs.io/en/latest/_images/platoon_joining_2lanefree.gif](https://opencda-documentation.readthedocs.io/en/latest/_images/platoon_joining_2lanefree.gif)

1. 合并进入队伍与SUMO联合仿真

![https://opencda-documentation.readthedocs.io/en/latest/_images/platoon_joining_cosim.gif](https://opencda-documentation.readthedocs.io/en/latest/_images/platoon_joining_cosim.gif)

1. 合并后入列

![https://opencda-documentation.readthedocs.io/en/latest/_images/platoon_joining_town06.gif](https://opencda-documentation.readthedocs.io/en/latest/_images/platoon_joining_town06.gif)

### https://github.com/ai4ce/V2X-Sim

一个智能驾驶协作感知数据集。采用SUMO生成真实的数字交通流，采用CARLA从多辆车和路边单元检索数据流。

V2X-Sim是vehicle-to-everythingsimulation的缩写，是纽约大学AI4CE实验室和上海交通大学MediaBrain团队开发的第一个合成的V2X辅助自动驾驶协作感知数据集，旨在促进多智能体多模态多任务感知研究 。 由于 V2X 的不成熟以及同时运行多辆自动驾驶汽车的成本，为研究社区构建这样一个真实世界的数据集非常昂贵且费力。 因此，我们使用高度真实的 CARLA-SUMO 联合仿真来确保我们的数据集与真实驾驶场景相比的代表性。

V2X-Sim 提供：(1) 来自路边单元 (RSU) 和多辆车辆的良好同步传感器记录，支持多智能体感知；(2) 多模态传感器流，促进多模态感知；(3) 各种经过良好注释的基本事实，支持各种感知任务，包括检测、跟踪和分割。 同时，我们构建了一个开源测试平台，并为检测、跟踪和分割等三个任务上最先进的协作感知算法提供了基准。

### https://github.com/hangqiu/AutoCastSim

AutoCastSim 是一个端到端的协同感知和协同驾驶仿真框架。它建立在 CARLA 模拟器之上，使用车辆-车辆(V2V)通信，以实现传感器共享和车辆协作。AutoCastSim 的一个特性是，它包含针对长尾事件的设计场景，在这些事件中，基于单一车辆的解决方案无法始终如一地做出安全决策。

![](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2023/07/600110548.png)
title: OpenCDA V2X通信相关
date: 2023-09-14 06:19:49
categories: selfdriving
tags: [V2X,OpenCDA]
---
## 一、V2X Manager



### init

1. 变量

   - cav_world(object): OpenCDA的对象，详细可见`core/common/cav_world.py`.定制的世界对象，可以保存所有的CDA车辆信息和管理共享ML模型，在联合仿真中也可以保存Sumo-Carla的ID映射。

   - config_yaml(dict): v2x模块的配置信息字典变量。
   - vid: 相应vehicle manager的uuid。

2. 方法

   ```python
   def __init__(self, cav_world, config_yaml, vid):
           # if disabled, no cooperation will be operated
           # 如果是disabled，协作相关的不会被运用。
           self.cda_enabled = config_yaml['enabled']
           # 读取通信距离
           self.communication_range = config_yaml['communication_range']
   
           # found CAVs nearby
           # 被发现的周边的CAVs
           self.cav_nearby = {}
   
           # used for cooperative perception.
           # 收到数据的缓存, dict
           self._recieved_buffer = {}
   
           # used for platooning communication
           # 队列通信，platooning_plugin是队列通信器件所使用的队列插件
           self.platooning_plugin = PlatooningPlugin(
               self.communication_range, self.cda_enabled)
   		# weakref.ref 是 Python 中的一个内置模块，它提供了一种创建对象的弱引用的方式。
           # 这里的作用应该是让cav_world在不被使用的时候能够及时被回收。
           self.cav_world = weakref.ref(cav_world)()
   
           # ego position buffer. use deque so we can simulate lagging
           # ego car的位置和速度信息缓存，使用deque可以模拟延迟。
           self.ego_pos = deque(maxlen=100)
           self.ego_spd = deque(maxlen=100)
           # used to exclude the cav self during searching
           # 用于搜索周边车辆的时候排除自身
           self.vid = vid
   
           # check if lag or noise needed to be added during communication
           # 添加一些噪声和延迟
           self.loc_noise = 0.0
           self.yaw_noise = 0.0
           self.speed_noise = 0.0
           self.lag = 0
   
           # Add noise to V2X communication if needed.
           # 如果在配置文件中配置，就添加相关的噪声
           if 'loc_noise' in config_yaml:
               self.loc_noise = config_yaml['loc_noise']
           if 'yaw_noise' in config_yaml:
               self.yaw_noise = config_yaml['yaw_noise']
           if 'speed_noise' in config_yaml:
               self.speed_noise = config_yaml['speed_noise']
           if 'lag' in config_yaml:
               self.lag = config_yaml['lag']
   ```

   该方法是来初始化v2x_manager的，相关的逻辑在注释中已经写出。这里大概是通过本身所拥有的cav_nearby字典来保存发现的周围车辆发送的V2X信息。

### update_info

1. 变量
   - ego_pos: 车辆的位置信息，是carla的transform对象
   - ego_spd: 车辆的速度信息，float，km/h
2. 方法

```python
 def update_info(self, ego_pos, ego_spd):
        """
        Update all communication plugins with current localization info.
        """
        # 更新所有的通信插件中的当前位置信息
        self.ego_pos.append(ego_pos)
        self.ego_spd.append(ego_spd)
        # 并且同时搜索周围的车辆信息
        self.search()

        # the ego pos in platooning_plugin is used for self-localization,
        # so we shouldn't add noise or lag.
        self.platooning_plugin.update_info(ego_pos, ego_spd)

```

首先会在本车的`ego_pos`和`ego_spd`的队列中添加当前的位置，然后所有周围的车辆信息，同时更新所有的插件中的当前位置信息。下面我们看看`search()`中是怎么实现的。

### search

1. 变量

   无

2. 方法

   ```python
   def search(self):
           """
           Search the CAVs nearby.
           """
           
           # 获取当前CAV World中的车辆字典
           vehicle_manager_dict = self.cav_world.get_vehicle_managers()
   		
           # 获取所有车辆的vid和vm
           for vid, vm in vehicle_manager_dict.items():
               # avoid the Nonetype error at the first simulation step
               if not vm.v2x_manager.get_ego_pos():
                   continue
               # avoid add itself as the cav nearby
               if vid == self.vid:
                   continue
               # 计算车辆和当前车的距离
               distance = compute_distance(
                   self.ego_pos[-1].location,
                   vm.v2x_manager.get_ego_pos().location)
   			# 如果距离在通信范围内，就将周围cav的获取的信息更新
               if distance < self.communication_range:
                   self.cav_nearby.update({vid: vm})
   ```

   大概的过程就是先从cav world里面获取所有的车辆信息，遍历这些车辆，通过计算车辆和当前车辆的距离信息，如果距离在通信范围内的话，就会将车辆发送的信息加入到`cav_nearby`里面。



### 总结

大概的方法就这些，主要就是实现一个`search`的方法，还有一些方法没有提到比如`get_ego_pos`和`get_ego_speed`这些都是在获取位置和速度信息的时候增加一些噪声。



## 二、RSUManager(鉴定为半成品)

### init

1. 变量

   - carla_world(carla.World): Carla的world对象，我们从中获取蓝图(blueprint)
   - config_yaml(dict): 关于RSU的配置信息
   - carla_map(carla.Map):  carla里面的地图对象
   - cav_world(object): cav世界中的相关信息，包括车辆信息等
   - current_time(str): 仿真开始的时间戳，用于数据导出
   - data_dumping(bool)：在仿真期间是否导出传感器信息

2. 方法

   ```python
   def __init__(
               self,
               carla_world,
               config_yaml,
               carla_map,
               cav_world,
               current_time='',
               data_dumping=False):
   
           self.rid = config_yaml['id']
           # The id of rsu is always a negative int
           # rsu的id永远是一个负整数，这是为了和车辆id进行区分
           if self.rid > 0:
               self.rid = -self.rid
   
           # read map from the world everytime is time-consuming, so we need
           # explicitly extract here
           # 每次都读取地图会很耗时，所以我们在一开始就读取，这样只用一次就好了。
           self.carla_map = carla_map
   
           # retrieve the configure for different modules
           # 检索不同模块的配置信息
           # todo: add v2x module to rsu later
           sensing_config = config_yaml['sensing']
           sensing_config['localization']['global_position'] = \
               config_yaml['spawn_position']
           sensing_config['perception']['global_position'] = \
               config_yaml['spawn_position']
   
           # localization module
           # 获取定位模块
           self.localizer = LocalizationManager(carla_world,
                                                sensing_config['localization'],
                                                self.carla_map)
           # perception module
           # 获取预测模块
           self.perception_manager = PerceptionManager(vehicle=None,
                                                       config_yaml=sensing_config['perception'],
                                                       cav_world=cav_world,
                                                       carla_world=carla_world,
                                                       data_dump=data_dumping,
                                                       infra_id=self.rid)
           # 判断是否需要导出数据
           if data_dumping:
               self.data_dumper = DataDumper(self.perception_manager,
                                             self.rid,
                                             save_time=current_time)
           else:
               self.data_dumper = None
   		# 更新world中的rsu信息
           cav_world.update_rsu_manager(self)
   ```

### update_info（main分支未完成）

1. 变量

   无

2. 方法

   ```python
   def update_info(self):
           """
           Call perception and localization module to
           retrieve surrounding info an ego position.
           """
           # localization
           self.localizer.localize()
   
           ego_pos = self.localizer.get_ego_pos()
           ego_spd = self.localizer.get_ego_spd()
   
           # object detection todo: pass it to other CAVs for V2X percetion
           objects = self.perception_manager.detect(ego_pos)
   ```

   

### run_step

1. 变量

   无

2. 方法

   ```python
    def run_step(self):
           """
           Currently only used for dumping data.
           """
           # dump data
           if self.data_dumper:
               self.data_dumper.run_step(self.perception_manager,
                                         self.localizer,
                                         None)
   ```



### destroy

1. 变量

   无

2. 方法

   ```python
   
       def destroy(self):
           """
           Destroy the actor vehicle
           """
           self.perception_manager.destroy()
           self.localizer.destroy()
   ```

### 总结

这个RSU基本上属于半成品，或者半成品都不到，只是新建文件夹这种。反正在main分支上是这样的，其他分支我还需要后续看看。
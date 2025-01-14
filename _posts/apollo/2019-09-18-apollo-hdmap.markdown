---
layout:     post
title:      "Apollo地图制作"
date:       2019-09-17 18:00:00
author:     "HongF"
header-img: "img/post-bg-miui6.jpg"
tags:
    - apollo
---

## Apollo地图制作

### 方法一：

1. 使用工具生成apollo标注形式的base_map.bin

2. 使用这个[页面](https://github.com/ApolloAuto/apollo/tree/master/modules/map/data)的工具分别生成routing map 和sim_map， 提示工具貌似需要用sudo运行，否则无法生成。

   A bundle of correlated maps are organized as a directory, with a structure like:

   ```
   <map_dir>                        # Defined by FLAGS_map_dir
     |-- base_map.xml               # Defined by FLAGS_base_map_filename
     |-- routing_map.bin            # Defined by FLAGS_routing_map_filename
     |-- sim_map.bin                # Defined by FLAGS_sim_map_filename
     |-- default_end_way_point.txt  # Defined by FLAGS_end_way_point_filename
   ```

   You can specify the map filenames as a list of candidates:

   ```
   --base_map_filename="base.xml|base.bin|base.txt"
   ```

   Then it will find the first available file to load. Generally we follow the extension pattern of:

   ```
   x.xml  # An OpenDrive formatted map.
   x.bin  # A binary pb map.
   x.txt  # A text pb map.
   ```

   ## Difference between base_map, routing_map and sim_map

   - `base_map` is the most complete map that has all roads, lane geometry and labels. The other maps are generated based on `base_map`.

   - `routing_map` has the topology of the lanes in `base_map`. It can be generated by command:

     ```
   dir_name=modules/map/data/demo # example map directory
      ./scripts/generate_routing_topo_graph.sh --map_dir ${dir_name}
     ```
   
   - `sim_map` is a light weight version of `base_map` for dreamview visualization. It has reduced data density for better runtime performance. It can be generated by command:

     ```
  dir_name=modules/map/data/demo # example map directory
     bazel-bin/modules/map/tools/sim_map_generator --map_dir=${dir_name} --output_dir=${dir_name}
     ```
   
   ## Use a different map

   1. [Preferred] Change global flagfile: *modules/common/data/global_flagfile.txt*

      Note that it's the basement of all modules' flagfile, which keeps the whole system in one page.

   2. Pass as flag, which only affects an individual process:

      ```
   <binary> --map_dir=/path/to/your/map
      ```

   3. Override in the module's own flagfile, which is generally located at *modules/<module_name>/conf/<module_name>.conf*
   
      Obviously it also only affects a single module.

      ```
   --flagfile=modules/common/data/global_flagfile.txt
      
   # Override values from the global flagfile.
      --map_dir=/path/to/your/map
      ```

使用gmapview.py生成地图下的路线图，与实际的图进行比较。

3. 最后生成的图所示

   <img class="shadow" src="/img/mapcasia.png" width="260">

### 方法二：

使用apollo提供的工具进行制作

包含下面几个步骤：

1. 录制车道线地图的bag包
2. 用车道线提取根据工具进行车道中心线提取， 生txt文件其中包含道路的utm坐标系。
3. 将数据复制出到csv文件
4. 根据csv文件的车道线制作道路边界及中心线。
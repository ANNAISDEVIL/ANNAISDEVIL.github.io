---
layout:     post
title:      ROS Beginner Commands
subtitle:   Basic ROS useful commands
date:       2022-08-05
author:     Huajian
header-img: img/post-bg-github-cup.jpg
catalog: true
tags:
    - ROS
    # - iOS
    # - ReactiveCocoa
    # - 函数式编程
    # - 开源框架
---
# Source
- 总是先 `source /opt/ros/noetic/setup.bash`
- 一旦类似于`roscd` 找不到package，则使用以下命令:
```
cd ~/catkin_ws
source devel/setup.bash
roscd beginner_tutorials
```

# rospack
**find**
- `rospack find [package_name]` 查找package位置

**depends**
- `rospack depends1 beginner_tutorials` 查找beginner_tutorials的一级依赖
- `rospack depends beginner_tutorials` 查找beginner_tutorials的所有依赖(包括间接依赖)

# roscd 
- `roscd [locationname[/subdir]]` cd到指定位置
- `roscd log` 查看日志

# rosls
- `rosls [locationname[/subdir]]` 直接软件包位置ls

# roscp 
- `roscp [package_name] [file_to_copy_path] [copy_path]` 将文件从一个包复制到另一个包

# Catkin
- 创建catkin软件包  
`catkin_create_pkg <package_name> [depend1] [depend2] [depend3]`  
example:
`catkin_create_pkg beginner_tutorials std_msgs rospy roscpp`
- 构建catkin工作区并使配置生效
```
cd ~/catkin_ws
catkin_make
. ~/catkin_ws/devel/setup.bash # 每次构建都需要source一下`setup.bash`
```

# ROS Node
考虑 ***Publish-Subscribe Model***, Node是整个操作流程中的一个节点  


- `roscore` - 开始ros的第一步  

- `rosnode list` - 查找节点列表  
  产生`/rosout`，用于收集和记录节点的调试输出，所以它总是在运行的
***
- `rosnode info /rosout` 查看节点详细信息
- `rosnode cleanup` 清理节点列表
***
- `rosrun [package_name] [node_name]` 运行给定的软件包中的节点
- `rosrun turtlesim turtlesim_node __name:=my_turtle` 可重命名


# rostopic
- `rostopic` 获取ROS话题的信息 (连续两下tab查看可用指令)
- `rostopic echo [topic]` 获取指定topic的信息。此时运行echo的terminal也subscribe了topic的信息，因此在**rqt-graph**中可以看到rostopic
- `rostopic list -h` 获取所有topic的列表。使用 `-h` 获取更多信息
- `rostopic type [topic]` 获取message的类型
- `rosmsg show [message type]` 获取type的详情  

  以上两者结合: `rostopic type /turtle1/cmd_vel | rosmsg show` 
- `rostopic pub [topic] [msg_type] [args]` publish message 到指定topic  

  example: `rostopic pub -1 /turtle1/cmd_vel geometry_msgs/Twist -- '[2.0, 0.0, 0.0]' '[0.0, 0.0, 1.8]'`

  - `rostopic pub` 发布消息
  - `-1` 发布一条信息
  - `/turtle1/cmd_vel` topic name
  - `geometry_msgs/Twist` message type
  - `--` 声明之后的参数都不是选项
  - `'[2.0, 0.0, 0.0]' '[0.0, 0.0, 1.8]'` value

- `rostopic pub ... -r` 不断发送指令
- `rostopic hz [topic]` 获取发布速率HZ

# rosservice 

- `rosservice list` 输出活跃服务的信息
- `rosservice type [service] 输出service的类型，是否需要参数等等  
  - 最好和`rossrv show`联合使用: `rosservice type /spawn | rossrv show`

- `rosservice call [service] [args]` 用给定的参数调用服务
- `rosservice type`         输出服务的类型
- `rosservice find`         按服务的类型查找服务
- `rosservice uri`          输出服务的ROSRPC uri

# rosparam
- `rosparam set [param_name]`            设置参数

  - set 后需要使用clear生效: `rosservice call /clear`

- `rosparam get [param_name]`            获取参数

  - 获取参数服务器上的所有内容: `rosparam get /`

- `rosparam load [file_name] [namespace]`           从文件中加载参数
- `rosparam dump [file_name] [namespace]`           向文件中转储参数

  - 一般是.yaml文件: `rosparam dump params.yaml`

- `rosparam delete`         删除参数
- `rosparam list`           列出参数名

# rqt
- `rosrun rqt_graph rqt_graph` 打开查看node和topic关系的graph

  - 也可以直接 `rpt` 或者 `rqt_graph`打开

- `rosrun rqt_plot rqt_plot` 打开动态显示topic中数据的plot
- `rosrun rqt_console rqt_console` 查看日志
- `rosrun rqt_logger_level rqt_logger_level` 设置logger_level，如设置Warn，则只会显示Warn以上的消息
  - Fatal （致命）
  - Error （错误）
  - Warn  （警告）
  - Info  （信息）
  - Debug （调试）

# roslaunch 

- `roslaunch [package] [filename.launch]` 启动一个运行脚本(如同时启动多个节点)

# rosed 

- `rosed [package_name] [filename]` 通过vim打开文件
- `rosed [package_name] <tab><tab>` 查看pkg下的所有文件 

# msg & srv
- 在使用msg和srv之前，需要在`package.xml`添加依赖，在`CMakeLists.txt`中调用和添加，见[ROS_Tutorial_10](http://wiki.ros.org/cn/ROS/Tutorials/CreatingMsgAndSrv)
- 根据[ROS_Tutorial_10](http://wiki.ros.org/cn/ROS/Tutorials/CreatingMsgAndSrv)第4节的内容，`.msg`和`.srv`通过以下命令可以生成对应语言的源代码
```
# In your catkin workspace
$ roscd beginner_tutorials
$ cd ../..
$ catkin_make
$ cd -
```

**msg**
- `rosmsg show [message type]` 查看message
  - `rosmsg show beginner_tutorials/Num` -- example1
  - `rosmsg show Num` -- example2
- `rostopic echo [topic]` 获取指定topic的message。**(可用于查看message 数据流)**  
此时运行echo的terminal也subscribe了topic的信息，因此在**rqt-graph**中可以看到rostopic  

**srv**

- `rossrv show [service type]` 查看service
  - `rossrv show beginner_tutorials/AddTwoInts` -- example1  
  - `rossrv show AddTwoInts` -- example2  
可在**rqt**中简单地call service以测试
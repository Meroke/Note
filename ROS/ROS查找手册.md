

## CMakeList

### 最终执行指令：

**` rosrun hello_vscode hello_vscode_c`**

分解：
`hello_vscode`  project name，位于CMakeLists.txt 最上端,又成软件包名（最外层src下的文件夹）

![image-20211116212748552](C:/Users/Meroke/AppData/Roaming/Typora/typora-user-images/image-20211116212748552.png)

`hello_vscode_c` 可执行目标：
对C++，只包含文件名前缀

对python，使用全称，包含后缀

![image-20211116212912543](C:/Users/Meroke/AppData/Roaming/Typora/typora-user-images/image-20211116212912543.png)

add_executable(可执行目标，源文件)

### ROS插件用处：

![image-20211116213053336](C:/Users/Meroke/AppData/Roaming/Typora/typora-user-images/image-20211116213053336.png)

选择src文件夹，右击，选择 Create Catkin Package  生成CMakeList.txt

***rosrun运行文件，必须在运行在项目文件下路径下，不能在子文件夹内。***



c++ 运行修改CMakeList.txt:

```cmake
add_executable(步骤3的源文件名
  src/步骤3的源文件名.cpp
)
target_link_libraries(步骤3的源文件名
  ${catkin_LIBRARIES}
)
```

upython运行修改CMakeList.txt:

```cmake
catkin_install_python(PROGRAMS scripts/自定义文件名.py
  DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)
```



### C++ 中文乱码

`setlocale(LC_ALL,"");`

或者

`setlocale(LC_CTYPE, "zh_CN.utf8");`加入main函数中



**在每次新开终端时，记得执行source ，不然无法运行**



### CMakeList内部联系

![image-20211117161242009](C:/Users/Meroke/AppData/Roaming/Typora/typora-user-images/image-20211117161242009.png)

在这样的文件列表中，我们的 **功能包 **是 plumbing_pub_sub

在执行指令中也有提示： `rosrun plumbing_pub_sub demo01_pub_p.py`

功能包 依赖于 find_package ，编译时依赖

![image-20211117162256383](C:/Users/Meroke/AppData/Roaming/Typora/typora-user-images/image-20211117162256383.png)

find_package 又依赖于catkin_package，运行时依赖

![image-20211117162307237](C:/Users/Meroke/AppData/Roaming/Typora/typora-user-images/image-20211117162307237.png)



---



## ROS通信

### 基础通信

#### C++

发布消息

```c++
ros::NodeHandle nh;
ros::Publisher pub  = nh.advertise<std_msgs::String>("chatter",10);
//添加延时，使其完全注册topic, 以免丢失
ros::Duration(3.0).sleep();
//chatter 是topic 话题

pub.publish(msg);
```

```bash
$ rostopic echo chatter 可查看信息
```

接受信息：

```c++
void doMsg(const std_msgs::String::ConstPtr &msg)
{
    ROS_INFO("我听见:%s",msg->data.c_str());
}

ros::NodeHandle nh;
//                                                                            topic, 消息列表，回调函数
ros::Subscriber sub = nh.subscribe("chatter",10,doMsg);
// 循环接受
ros::spin();
```

#### python

发布消息：

```python
#! /usr/bin/env python

import rospy
from std_msgs.msg import String
if __name__ == "__main__":
    rospy.init_node("sanDai")
    # param:topic data_class, 消息队列（超出指定值，则清理旧数据）
    pub = rospy.Publisher("che",String,queue_size=10)
    # 初始化消息，自带data属性
    msg = String()
    rate = rospy.Rate(1)
    while not rospy.is_shutdown():
        msg.data = "helo"
        pub.publish(msg)
        rate.sleep()
        rospy.loginfo("写出的数据:%s",msg.data)
```



### msg 调用

![image-20211220211002710](https://s2.loli.net/2021/12/20/rqZJ8KLk2HT3Acn.png)

添加cpp、py文件后需要修改 CMakeLists.txt

<details>
    <summary>针对C++需要修改如下这些内容：</summary>
    <img src=https://s2.loli.net/2021/12/20/eZTaIG4MjsWYnwB.png >
</details>

<details>
    <summary>针对python，需要修改如下</summary>
    <img src="https://s2.loli.net/2021/12/20/N8OtbfnuPIxRD6B.png"> 
</details>

​        

### 参数服务器

```cpp
    在 roscpp 中提供了两套 API 实现参数操作
    ros::NodeHandle
        setParam("键",值)
    ros::param
        set("键","值")

    示例:分别设置整形、浮点、字符串、bool、列表、字典等类型参数
        修改(相同的键，不同的值)
```

#### set增加参数

```c++
    //NodeHandle--------------------------------------------------------
    ros::NodeHandle nh;
    nh.setParam("nh_int",10); //整型
   //param--------------------------------------------------------
    ros::param::set("param_int",20);
    ros::param::set("param_double",3.14);
```

#### get 查询参数

```c++
	//nh
	//1
	nh.getParam("nh_int",nh_int_value);
	//2
	nh.getParamCached("nh_int",nh_int_value);
	//3
	std::vector<std::string> param_names1;
    nh.getParamNames(param_names1);
    for (auto &&name : param_names1)
    {
        ROS_INFO("名称解析name = %s",name.c_str());        
    }
	//4
	ROS_INFO("存在 nh_int 吗? %d",nh.hasParam("nh_int"));
	//5
  	std::string key;
    nh.searchParam("nh_int",key);
    ROS_INFO("搜索键:%s",key.c_str());
	// param
	//1
	ros::param::get("param_int",param_int_value);
	//2
    ros::param::getCached("param_int",param_int_value);
	//3 
    std::vector<std::string> param_names2;
    ros::param::getParamNames(param_names2);
    for (auto &&name : param_names2)
    {
        ROS_INFO("名称解析name = %s",name.c_str());        
    }
	//4
	ROS_INFO("存在 param_int 吗? %d",ros::param::has("param_int"));
    //5
	std::string key;
    ros::param::search("param_int",key);
    ROS_INFO("搜索键:%s",key.c_str());
```

#### delete删除参数

```c++
    ros::NodeHandle nh;
    bool r1 = nh.deleteParam("nh_int");
    ROS_INFO("nh 删除结果:%d",r1);

    bool r2 = ros::param::del("param_int");
    ROS_INFO("param 删除结果:%d",r2);
```

### 常用指令

####  rosnode

```
rosnode ping    测试到节点的连接状态
rosnode list    列出活动节点
rosnode info    打印节点信息
rosnode machine    列出指定设备上节点
rosnode kill    杀死某个节点
rosnode cleanup    清除不可连接的节点
```

#### rostopic

```
rostopic bw     显示主题使用的带宽
rostopic delay  显示带有 header 的主题延迟
rostopic echo   打印消息到屏幕
rostopic find   根据类型查找主题
rostopic hz     显示主题的发布频率
rostopic info   显示主题相关信息
rostopic list   显示所有活动状态下的主题
rostopic pub    将数据发布到主题 
rostopic type   打印主题类型
```



```shell
#              pub    topic                      两次TAB补齐
rostopic pub  /chatter_person  hellTopic/Person    "name: 'huluwa'
age: 8
height: 0.8" 
```

```shell
# 每秒10次
rostopic pub -r 10 /chatter_person  hellTopic/Person "name: 'huluwa'
age: 5
height: 10" 
```

#### rosmsg 

```
rosmsg show    显示消息描述
rosmsg info    显示消息信息
rosmsg list    列出所有消息
rosmsg md5    显示 md5 加密后的消息
rosmsg package    显示某个功能包下的所有消息
rosmsg packages    列出包含消息的功能包
```

#### rosservice

```
rosservice args 打印服务参数
rosservice call    使用提供的参数调用服务
rosservice find    按照服务类型查找服务
rosservice info    打印有关服务的信息
rosservice list    列出所有活动的服务
rosservice type    打印服务类型
rosservice uri    打印服务的 ROSRPC uri
```

#### rossrv

rossrv是用于显示有关ROS服务类型的信息的命令行工具，与 rosmsg 使用语法高度雷同。

```
rossrv show    显示服务消息详情
rossrv info    显示服务消息相关信息
rossrv list    列出所有服务信息
rossrv md5    显示 md5 加密后的服务消息
rossrv package    显示某个包下所有服务消息
rossrv packages    显示包含服务消息的所有包
```

### 常用APIs

#### 初始化

```cpp
void init(int &argc, char **argv, const std::string& name, uint32_t options = 0);
```

```c++
// 节点可重复启动，设置ros::init_options::AnonymousName
// 在用户定义的节点名称加上随机数后缀，避免重复名称
ros::init(argc, argv, "NodeName", ros::init_options::AnonymousName) 
```

#### 话题与服务相关对象

```c++
/* 
	latch设置为true的作用？
	以发布静态地图为例子：发布者对象的latch设置为True,每当订阅者连接时，发布者就会发布一次最新的数据，仅新连接时触发一次
*/
ros::Publisher pub = nh.advertise<std_msgs::String>("topicName",10,latch=True)

pub.publish(data)
```

#### 回旋函数

##### 1.spinOnce()

```cpp
/**
 * \brief 处理一轮回调
 *
 * 一般应用场景:
 *     在循环体内，处理所有可用的回调函数
 * 
 */
ROSCPP_DECL void spinOnce();
```

##### 2.spin()

```cpp
/** 
 * \brief 进入循环处理回调 
 */
ROSCPP_DECL void spin();
```

##### 3.二者比较

**相同点:**二者都用于处理回调函数；

**不同点:**ros::spin() 是进入了循环执行回调函数，而 ros::spinOnce() 只会执行一次回调函数(没有循环)，在 ros::spin() 后的语句不会执行到，而 ros::spinOnce() 后的语句可以执行。



### 元功能包

**实现**

**首先:**新建一个功能包

**然后:**修改**package.xml** ,内容如下:

```xml
 <exec_depend>被集成的功能包</exec_depend>
 .....
 <export>
   <metapackage />
 </export>
```

**最后:**修改 CMakeLists.txt,内容如下:

```cmake
cmake_minimum_required(VERSION 3.0.2)
project(demo)
find_package(catkin REQUIRED)
catkin_metapackage()
```

PS:CMakeLists.txt 中不可以有换行。

### launch文件

#### 弃用声明

- `deprecated = "弃用声明"`

  告知用户当前 launch 文件已经弃用

```xml
<launch deprecated ="此文件废弃"  >
</launch>
```

#### node

**属性**

- pkg="包名"

  节点所属的包

- type="nodeType"

  节点类型(与之相同名称的可执行文件)

- name="nodeName"

  节点名称(在 ROS 网络拓扑中节点的名称)

- args="xxx xxx xxx" (可选)

  将参数传递给节点

- machine="机器名"

  在指定机器上启动节点

- respawn="true | false" (可选)

  如果节点退出，是否自动重启

  ```xml
  <node pkg="" type="" name="" respawn=" true"/ >
  ```

- respawn_delay=" N" (可选)

  如果 respawn 为 true, 那么延迟 N 秒后启动节点

- required="true | false" (可选)

  该节点是否必须，如果为 true,那么如果该节点退出，将杀死整个 roslaunch

- ns="xxx" (可选)

  在指定命名空间 xxx 中启动节点

  ```html
  <node pkg="" type="" name=""   ns="hello"/ >
  ```

  ![image-20220116205200490](https://gitee.com/mmeroke/picture_bed/raw/master/img/image-20220116205200490.png)

- clear_params="true | false" (可选)

  在启动前，删除节点的私有空间的所有参数

- output="log | screen" (可选)

  日志发送目标，可以设置为 log 日志文件，或 screen 屏幕,默认是 log

#### include

`include`标签用于将另一个 xml 格式的 launch 文件导入到当前文件

**属性**

- file="$(find 包名)/xxx/xxx.launch"

  要包含的文件路径

- ns="xxx" (可选)

  在指定命名空间导入文件

```xml
<launch>
    <!-- <include file="$(find 功能包名)/launch/具体路径" /> -->
    <include file="$(find hello)/launch/start_turtle.launch" />
</launch>
```

#### remap

用于话题重命名

**属性**

- from="xxx"

  原始话题名称

- to="yyy"

  目标名称

```xml
<launch>
    <!-- 添加执行节点-->
    <!-- rosrun turlesim  turlesim_node-->
    <!-- <node pkg="hello" type="hello" name="hello" output="screen"/> -->
    <node pkg="turtlesim" type="turtlesim_node" name="turtle_GUI" output="screen" >
    <!-- 当使用不同的话题时，通过remap修改话题将两个节点互通 -->
        <remap from="/turtle/cmd_vel" to="/cmd_vel" /> 
    </node>
    <node pkg="turtlesim" type="turtle_teleop_key" name="turtle_key" output="screen" />
</launch> 
```

#### param&rosparam

`<param>`标签主要用于在参数服务器上设置参数，参数源可以在标签中通过 value 指定，也可以通过外部文件加载，在`<node>`标签中时，相当于私有命名空间。

**属性**

- name="命名空间/参数名"

  参数名称，可以包含命名空间

- value="xxx" (可选)

  定义参数值，如果此处省略，必须指定外部文件作为参数源

- type="str | int | double | bool | yaml" (可选)

  指定参数类型，如果未指定，roslaunch 会尝试确定参数类型，规则如下:

  - 如果包含 '.' 的数字解析未浮点型，否则为整型
  - "true" 和 "false" 是 bool 值(不区分大小写)
  - 其他是字符串

```xml
<launch>
    <!-- 添加执行节点-->
    <!-- rosrun turlesim  turlesim_node-->
    <!-- <node pkg="hello" type="hello" name="hello" output="screen"/> -->
    <param name="param_A" type="int" value="1000"/>
    <rosparam command="load" file="$(find hello)/launch/param.yaml" />
    <node pkg="turtlesim" type="turtlesim_node" name="turtle_GUI" output="screen" >
    <!-- 当使用不同话题的节点时，通过remap修改话题将两个节点互通 -->
        <remap from="/turtle/cmd_vel" to="/cmd_vel" /> 
        <param name="param_B" type="double" value="1.5"/>
    </node>
    <node pkg="turtlesim" type="turtle_teleop_key" name="turtle_key" output="screen" />
</launch> 
```

```python
 注意param.yaml这里必须编码(中英文)正确，错误会标红，发生错误如下

 RLException: error loading <rosparam> tag: 
       'ascii' codec can't decode byte 0xef in position 71: ordinal not in range(128)
 XML is <rosparam command="load" file="$(find hello)/launch/param.yaml"/>
 The traceback for the exception was written to the log file
```



```xml
<launch>
<!-- 导出参数 -->
    <rosparam command="dump" file="$(find hello)/launch/params_out.yaml"/>
    <!-- 运行中删除参数 -->
    <rosparam command="delete" param="bg_B" />
</launch>
```



#### group

```xml
<launch>
    <!-- 添加执行节点-->
    <group ns="first">
        <node pkg="turtlesim" type="turtlesim_node" name="turtle_GUI" output="screen" />
        <node pkg="turtlesim" type="turtle_teleop_key" name="turtle_key" output="screen" />
    </group>
    <group ns="second">
        <node pkg="turtlesim" type="turtlesim_node" name="turtle_GUI" output="screen" />
        <node pkg="turtlesim" type="turtle_teleop_key" name="turtle_key" output="screen" />
    </group>
</launch> 
```

![image-20220117133828887](https://gitee.com/mmeroke/picture_bed/raw/master/img/image-20220117133828887.png)

#### arg

```xml
<launch>
    <arg name="car_length" default="0.55"/>
    <param name="A" value="$(arg car_length)"/>
    <param name="B" value="$(arg car_length)" />
</launch>
```


### TF坐标变换

#### 1.geometry_msgs/TransformStamped

命令行键入:`rosmsg info geometry_msgs/TransformStamped`

```
std_msgs/Header header                     #头信息
  uint32 seq                                #|-- 序列号
  time stamp                                #|-- 时间戳
  string frame_id                            #|-- 坐标 ID
string child_frame_id                    #子坐标系的 id
geometry_msgs/Transform transform        #坐标信息
  geometry_msgs/Vector3 translation        #偏移量
    float64 x                                #|-- X 方向的偏移量
    float64 y                                #|-- Y 方向的偏移量
    float64 z                                #|-- Z 方向上的偏移量
  geometry_msgs/Quaternion rotation        #四元数
    float64 x                                
    float64 y                                
    float64 z                                
    float64 w
```

四元数用于表示坐标的相对姿态

#### 2.geometry_msgs/PointStamped

命令行键入:`rosmsg info geometry_msgs/PointStamped`

```
std_msgs/Header header                    #头
  uint32 seq                                #|-- 序号
  time stamp                                #|-- 时间戳
  string frame_id                            #|-- 所属坐标系的 id
geometry_msgs/Point point                #点坐标
  float64 x                                    #|-- x y z 坐标
  float64 y
  float64 z
```

#### 静态坐标转换



```c++
    // pub.cpp
    // 3.创建静态坐标转换广播器
    tf2_ros::StaticTransformBroadcaster broadcaster;
    // 4.创建坐标系信息
    geometry_msgs::TransformStamped ts;
     ts.header.frame_id = "base_link"; //被参考的坐标系，基本是小车主体
    //----设置子级坐标系
    ts.child_frame_id = "laser"; //雷达坐标系
    broadcaster.sendTransform(ts);
    
    
    //sub.cpp
    geometry_msgs::PointStamped point_laser;
    point_laser.header.frame_id = "laser";
    geometry_msgs::PointStamped point_base;
    //将point_laser 转换成 相对base_link 的坐标
    point_base = buffer.transform(point_laser,"base_link");
```

订阅sub是耗时操作，使用launch时，会一开始由于没接受到数据报错 `base_link不存在`

解决方法：

1. 延时休眠
2. 异常处理

**补充1:**

当坐标系之间的相对位置固定时，那么所需参数也是固定的: 父系坐标名称、子级坐标系名称、x偏移量、y偏移量、z偏移量、x 翻滚角度、y俯仰角度、z偏航角度，实现逻辑相同，参数不同，那么 ROS 系统就已经封装好了专门的节点，使用方式如下:

```
rosrun tf2_ros static_transform_publisher x偏移量 y偏移量 z偏移量 z偏航角度 y俯仰角度 x翻滚角度 父级坐标系 子级坐标系
```

示例:`rosrun tf2_ros static_transform_publisher 0.2 0 0.5 0 0 0 /baselink /laser`

针对角度： 1.570795 = pi/2 = 逆时针90度

也建议使用该种方式直接实现静态坐标系相对信息发布。

可尝试将该指令封装进shell启动脚本或py启动脚本

**补充2:**

可以借助于rviz显示坐标系关系，具体操作:

- 新建窗口输入命令:rviz;
- 在启动的 rviz 中设置Fixed Frame 为 base_link;
- 点击左下的 add 按钮，在弹出的窗口中选择 TF 组件，即可显示坐标关系。

#### 动态坐标

```c++
            geometry_msgs::PointStamped ps;
            ps.header.frame_id = "son1"; // 以son1为坐标系原点生成新点ps
            ps.header.stamp = ros::Time::now();
            ps.point.x = 1.0;
            ps.point.y = 2.0;
            ps.point.z = 3.0;
```



```c++
            // son2为父， son1为子，生成一个子son1 相对 父亲son2 的点
			geometry_msgs::TransformStamped tfs = buffer.lookupTransform("son2","son1",ros::Time(0));
            ROS_INFO("Son1 相对于 Son2 的坐标关系:父坐标系ID=%s",tfs.header.frame_id.c_str());
            ROS_INFO("Son1 相对于 Son2 的坐标关系:子坐标系ID=%s",tfs.child_frame_id.c_str());
            ROS_INFO("Son1 相对于 Son2 的坐标关系:x=%.2f,y=%.2f,z=%.2f",
                    tfs.transform.translation.x,
                    tfs.transform.translation.y,
                    tfs.transform.translation.z
                    );
```



```c++
# point_laser 原坐标是turtle， 现在转换成相对world的坐标点
point_base = buffer.transform(point_laser,"world");
```



#### 坐标查询

```shell
rosrun tf2_tools view_frames. y #生成坐标关系pdf
evince frame.pdf # 查看pdf
```

____



### 系统仿真



URDF 文件是一个标准的 XML 文件，在 ROS 中预定义了一系列的标签用于描述机器人模型，机器人模型可能较为复杂，但是 ROS 的  URDF 中机器人的组成却是较为简单，可以主要简化为两部分:连杆(link标签) 与 关节(joint标签)，接下来我们就通过案例了解一下  URDF 中的不同标签:

- robot 根标签，类似于 launch文件中的launch标签
- link 连杆标签
- joint 关节标签
- gazebo 集成gazebo需要使用的标签

关于gazebo标签，后期在使用 gazebo 仿真时，才需要使用到，用于配置仿真环境所需参数，比如: 机器人材料属性、gazebo插件等，但是该标签不是机器人模型必须的，只有在仿真时才需设置

- visual ---> 描述外观(对应的数据是可视的)
  - geometry 设置连杆的形状
    - 标签1: box(盒状)
      - 属性:size=长(x) 宽(y) 高(z)
    - 标签2: cylinder(圆柱)
      - 属性:radius=半径 length=高度
    - 标签3: sphere(球体)
      - 属性:radius=半径
    - 标签4: mesh(为连杆添加皮肤)
      - 属性: filename=资源路径(格式:**package://<packagename>/<path>/文件**)
  - origin 设置偏移量与倾斜弧度
    - 属性1: xyz=x偏移 y便宜 z偏移
    - 属性2: rpy=x翻滚 y俯仰 z偏航 (单位是弧度)
  - metrial 设置材料属性(颜色)
    - 属性: name
    - 标签: color
      - 属性: rgba=红绿蓝权重值与透明度 (每个权重值以及透明度取值[0,1])
- collision ---> 连杆的碰撞属性
- Inertial ---> 连杆的惯性矩阵

```xml
<robot name="mycar">
    <link name ="base_link">
        <visual>
            <geometry>
                <box size="0.3 0.2 0.1" />
                <!-- <cylinder raidus="0.5" length="2"/>
                <sphere radius="1" />
                <mesh filename="package://vr/meshes//.stl" -->
            </geometry>
            <!-- xyz  平移量   rpy 对应轴上旋转角度 1.57=90度，左右旋转取决于z -->
            <origin xyz="3 0 0" rpy="0 0 1.57"/>
        </visual>
    </link>
</robot>
```



#### xcaro

```
注意！！：xcaro文件内不可以存在中文注释，否则会编码报错
```



**属性**

```xml
<xacro:property name="xxxx" value="yyyy" />
```

**宏**

类似于函数实现，提高代码复用率，优化代码结构，提高安全性

**宏定义**

```xml
<xacro:macro name="宏名称" params="参数列表(多参数之间使用空格分隔)">

    .....

    参数调用格式: ${参数名}

</xacro:macro>
```

**宏调用**

```xml
<xacro:宏名称 参数1=xxx 参数2=xxx/>
```

示例：

```xml
<robot name="mycar" xmlns:xacro="http://wiki.ros.org/xacro">
    <!-- 宏定义 -->
    <xacro:macro name="getSum" params="num1 num2">
        <result value="${num1 + num2}"/>
    </xacro:macro>
    <!-- 宏调用 -->
    <xacro:getSum num1="1" num2="2"/>
</robot>
```

![image-20220120150046899](https://gitee.com/mmeroke/picture_bed/raw/master/img/image-20220120150046899.png)

**文件包含**

机器人由多部件组成，不同部件可能封装为单独的 xacro 文件，最后再将不同的文件集成，组合为完整机器人，可以使用文件包含实现

**文件包含**

```xml
<robot name="xxx" xmlns:xacro="http://wiki.ros.org/xacro">
      <xacro:include filename="my_base.xacro" />
      <xacro:include filename="my_camera.xacro" />
      <xacro:include filename="my_laser.xacro" />
      ....
</robot>
```



#### launch启动

textfile

`<param name="robot_description" textfile="$(find vr)/urdf/xacro/all.urdf" />`

```xml
<launch>
    <!-- 参数服务器载入urdf -->
    <param name="robot_description" textfile="$(find vr)/urdf/xacro/all.urdf" />
    <node pkg="rviz" type="rviz" name="rviz" args="-d $(find vr)/config/ros_car.rviz"/>
    <node pkg="joint_state_publisher" type="joint_state_publisher" name="joint_state_publisher" />
    <node pkg="robot_state_publisher" type="robot_state_publisher" name="robot_state_publisher" />

</launch>
```

command

`    <param name="robot_description" command="$(find xacro)/xacro $(find vr)/urdf/xacro/all.urdf.xacro" />`

```xml
<launch>
    <!-- 参数服务器载入urdf -->
    <param name="robot_description" command="$(find xacro)/xacro $(find vr)/urdf/xacro/all.urdf.xacro" />
    <node pkg="rviz" type="rviz" name="rviz" args="-d $(find vr)/config/ros_car.rviz"/>
    <node pkg="joint_state_publisher" type="joint_state_publisher" name="joint_state_publisher" />
    <node pkg="robot_state_publisher" type="robot_state_publisher" name="robot_state_publisher" />

</launch>
```

#### arbotix运动仿真

yaml

```xml
# 该文件是控制器配置,一个机器人模型可能有多个控制器，比如: 底盘、机械臂、夹持器(机械手)....
# 因此，根 name 是 controller
controllers: {
   # 单控制器设置
   base_controller: {
          #类型: 差速控制器
       type: diff_controller,
       #参考坐标
       base_frame_id: base_footprint, 
       #两个轮子之间的间距
       base_width: 0.2,
       #控制频率
       ticks_meter: 2000, 
       #PID控制参数，使机器人车轮快速达到预期速度
       Kp: 12, 
       Kd: 12, 
       Ki: 0, 
       Ko: 50, 
       #加速限制
       accel_limit: 1.0 
    }
}

```

launch启动文件

```xml
<node name="arbotix" pkg="arbotix_python" type="arbotix_driver" output="screen">
     <rosparam file="$(find my_urdf05_rviz)/config/hello.yaml" command="load" />
     <param name="sim" value="true" />
</node>

```



#### 雷达仿真

新建在gazebo文件夹下的laser.xacro，注意将仿真和模型配合起来时， 仿真的reference是针对模型的ling名的

```xml
<robot name="my_sensors" xmlns:xacro="http://wiki.ros.org/xacro">

  <!-- 雷达 -->
    <!-- 这里的reference 是对应前面编写的雷达的link名-->
  <gazebo reference="laser">   
    <sensor type="ray" name="rplidar">
      <pose>0 0 0 0 0 0</pose>
      <visualize>true</visualize>
      <update_rate>5.5</update_rate> <!-- 每秒5.5次-->
      <ray>
        <scan>
          <horizontal>
            <samples>360</samples>  <!-- 旋转一周发射360条射线，或说是采样360个点-->
            <resolution>1</resolution> <!-- 分辨率，每N条射线测距一条-->
            <min_angle>-3</min_angle> <!-- 一周rad=6.28  , 这里是角度范围-3到3-->
            <max_angle>3</max_angle>
          </horizontal>
        </scan>
        <range>
          <min>0.10</min> <!-- 障碍物 必须距离在0.1— 30 m 之间才能被采样-->
          <max>30.0</max>
          <resolution>0.01</resolution> <!-- 0.01m精度-->
        </range>
        <noise>
          <type>gaussian</type>  <!--高斯噪音 模拟测距抖动-->
          <mean>0.0</mean>
          <stddev>0.01</stddev>
        </noise>
      </ray>
      <plugin name="gazebo_rplidar" filename="libgazebo_ros_laser.so">
        <topicName>/scan</topicName>
        <frameName>laser</frameName>
      </plugin>
    </sensor>
  </gazebo>

</robot>

```

#### 摄像头仿真

```xml
<robot name="my_sensors" xmlns:xacro="http://wiki.ros.org/xacro">
  <gazebo reference="camera">
    <sensor type="camera" name="camera_node">
      <update_rate>30.0</update_rate>
      <camera name="head">
        <horizontal_fov>1.3962634</horizontal_fov>
        <image>
          <width>1280</width>
          <height>720</height>
          <format>R8G8B8</format>
        </image>
        <clip>
          <near>0.02</near>
          <far>300</far>
        </clip>
        <noise>
          <type>gaussian</type>
          <mean>0.0</mean>
          <stddev>0.007</stddev>
        </noise>
      </camera>
      <plugin name="gazebo_camera" filename="libgazebo_ros_camera.so">
        <alwaysOn>true</alwaysOn>
        <updateRate>0.0</updateRate>
        <cameraName>/camera</cameraName> <!-- image topic -->
        <imageTopicName>image_raw</imageTopicName><!-- image topic -->
        <cameraInfoTopicName>camera_info</cameraInfoTopicName>
        <frameName>camera</frameName>
        <hackBaseline>0.07</hackBaseline>
        <distortionK1>0.0</distortionK1>
        <distortionK2>0.0</distortionK2>
        <distortionK3>0.0</distortionK3>
        <distortionT1>0.0</distortionT1>
        <distortionT2>0.0</distortionT2>
      </plugin>
    </sensor>
  </gazebo>
</robot>

```





![image-20220125155103401](/home/meroke/.config/Typora/typora-user-images/image-20220125155103401.png)

#### 深度摄像头仿真

```xml
<robot name="my_sensors" xmlns:xacro="http://wiki.ros.org/xacro">
    <gazebo reference="support">  <!-- 这里将摄像头支架设置成了深度摄像头 -->
      <sensor type="depth" name="camera">
        <always_on>true</always_on>
        <update_rate>20.0</update_rate>
        <camera>
          <horizontal_fov>${60.0*PI/180.0}</horizontal_fov>
          <image>
            <format>R8G8B8</format>
            <width>640</width>
            <height>480</height>
          </image>
          <clip>
            <near>0.05</near>
            <far>8.0</far>
          </clip>
        </camera>
        <plugin name="kinect_camera_controller" filename="libgazebo_ros_openni_kinect.so">
          <cameraName>camera</cameraName>
          <alwaysOn>true</alwaysOn>
          <updateRate>10</updateRate>
          <imageTopicName>rgb/image_raw</imageTopicName>
          <depthImageTopicName>depth/image_raw</depthImageTopicName>
          <pointCloudTopicName>depth/points</pointCloudTopicName>
          <cameraInfoTopicName>rgb/camera_info</cameraInfoTopicName>
          <depthImageCameraInfoTopicName>depth/camera_info</depthImageCameraInfoTopicName>
            <!-- 这里还对应坐标系，由于图像和点云坐标系不同，需要手动发布坐标变换-->
          <frameName>support</frameName><!-- 这里将摄像头支架设置成了深度摄像头 -->
          <baseline>0.1</baseline>
          <distortion_k1>0.0</distortion_k1>
          <distortion_k2>0.0</distortion_k2>
          <distortion_k3>0.0</distortion_k3>
          <distortion_t1>0.0</distortion_t1>
          <distortion_t2>0.0</distortion_t2>
          <pointCloudCutoff>0.4</pointCloudCutoff>
        </plugin>
      </sensor>
    </gazebo>

</robot>

```





### 导航

#### 保存地图

将正在运行的rviz地图保存

```xml
<launch>
    <arg name="filename" value="$(find mycar_nav)/map/nav" />
    <node name="map_save" pkg="map_server" type="map_saver" args="-f $(arg filename)" />
</launch>
```

#### 读取地图

```xml
<launch>
    <!-- 设置地图的配置文件 -->
    <arg name="map" default="nav.yaml" />
    <!-- 运行地图服务器，并且加载设置的地图-->
    <node name="map_server" pkg="map_server" type="map_server" args="$(find mycar_nav)/map/$(arg map)"/>
</launch>
```

**注意：yaml中不可以有中文字符** 

```yaml
# nav.yaml
image: /home/meroke/code/course03/src/nav_ma/map/nav.pgm
# 分辨率  m/像素
resolution: 0.0500000
# 相对x y 偏航角度
origin: [-50.000000, -50.000000, 0.000000]
# 黑白取反
negate: 0
# 坐标向素值 > occupied_thresh,视作障碍物
#          < free_thresh，   视作无物
occupied_thresh: 0.65
free_thresh: 0.196
```



#### 局部和静态地图像素精度应保持一致

` resolution: 0.05  ` 统一0.05

```yaml
# local_costmap_params.yaml
local_costmap:
  global_frame: map #里程计坐标系，不要设置成odam（Extrapolation Error）
  robot_base_frame: base_footprint #机器人坐标系

  update_frequency: 4.0 # 默认10.0 代价地图更新频率
  publish_frequency: 3.0 #默认10.0 代价地图的发布频率
  transform_tolerance: 0.5 #等待坐标变换发布信息的超时时间

  #膨胀半径，扩展在碰撞区域以外的代价区域，使得机器人规划路径避开障碍物，local可以设置的比global小
  inflation_radius: 0.07
  #代价比例系数，越大则代价值越小
  cost_scaling_factor: 5.0 # default 5.0


  static_map: false  #不需要静态地图，可以提升导航效果
  rolling_window: true #是否使用动态窗口，默认为false，在静态的全局地图中，地图不会变化
  width: 1 # 默认3，局部地图宽度 单位是 m
  height: 1 # 默认3，局部地图高度 单位是 m
  resolution: 0.05  # deafault 0.05 局部地图分辨率 单位是 m，一般与静态地图分辨率(map/nav.yaml)保持一致
  plugins:
    - {name: obstacle_layer,      type: "costmap_2d::ObstacleLayer"}
    - {name: inflation_layer,     type: "costmap_2d::InflationLayer"}
```



```yaml
# 静态地图 nav.yaml
image: /home/meroke/code/course03/src/nav_ma/map/nav.pgm
resolution: 0.050000 # 地图像素
origin: [-50.000000, -50.000000, 0.000000]
negate: 0
occupied_thresh: 0.65
free_thresh: 0.196
```



## 系统配置流程：

### Python3.6:

问题描述：由于eaibot处理器使用ubuntu14.6 kinetic版本，自带python3.5，但python3.6更符合已有框架，故需要配置下载使用python3.6。

流程:

1. [下载python3.6.9](https://cloud.tencent.com/developer/article/1397411)
2. 为python3.6开启SSL(HTTPS)功能
3. 

### cartographer建图算法配置

[eaibot参考](https://blog.csdn.net/yjboxes/article/details/122647049)

[ubuntu18 Melodic](https://blog.csdn.net/weixin_42159320/article/details/119764669)





### 建图优化

1.直接对地图进行图像处理

- 膨胀 腐蚀 ，开闭模式 配合 Canny 检测（开操作=先腐蚀+再膨胀，针对白色区域）

```python
    kernel_big = cv.getStructuringElement(cv.MORPH_RECT, (5,5 ))
    kernel_small = cv.getStructuringElement(cv.MORPH_RECT, (3,3 ))
    open_img = cv.morphologyEx(img,cv.MORPH_OPEN, kernel_big)

    # Canny Edge Detect
    img_edges = cv.Canny(open_img, 50, 100) 
    resize_imshow(img_edges,"canny")
    close_img = cv.morphologyEx(img,cv.MORPH_OPEN, kernel_small)
    resize_imshow(close_img,"Close")    

```



![image-20220430230756204](/home/meroke/.config/Typora/typora-user-images/image-20220430230756204.png)



![image-20220430230717791](/home/meroke/.config/Typora/typora-user-images/image-20220430230717791.png)





### 识别优化

[图像纠正方法][https://blog.csdn.net/wsp_1138886114/article/details/83374333]





## 经验教训



### QS1:rviz地图与gazebo不同步

详细描述:

工作空间：仿真项目

文件位置 `course03`

启动urdf_gazebo/launch/union.launch （模型启动文件 和 gazebo环境），然后再启动nav_ma/launch/nav.launch（rviz建图），出现rviz中 no map receive , 地图无显示，且无报错。

正常应该是rviz同步显示gazebo地图中被雷达检测到的部分。

问题解决:

发现是更改urdf_gazebo/xacro/my_base.urdf.xacro中的小车底盘时，添加了车轮，从二轮转为四轮，并修改了之前的轮子名称。但没有修改urdf_gazebo/xacro/gazebo/move.xacro小车控制器对应的joint，导致运行出错，节点未正常发布，修改后即可。



### QS2:仿真运动需要保证rviz与gazebo地图相同

详细描述：

工作空间：仿真项目

文件位置 `course03`

仿真运动是指用gazebo环境模拟真实环境，然后通过运动控制模拟导航效果。

因此需要保证rviz的地图与gazebo相同。

一般 gazebo地图 --> rviz 地图

流程：

1. 启动urdf_gazebo/launch/union.launch （模型启动文件 和 gazebo环境）

2. 然后再启动nav_ma/launch/nav.launch（rviz建图），rviz地图与同步gazebo。但此时的rviz只有雷达检测到的部分地图，无法从一开始获取全部地图信息.

3. 因此需要手动移动小车，运行` rosrun teleop_twist_keyboard teleop_twist_keyboard.py`,控制小车前进，通过雷达扫描各处更新地图，最后获取完整地图。
4. 启动 nav_ma/launch/map_save.launch ，即可直接保存完整地图为nav.yaml



### QS3:运动时出现Extrapolation Error

工作空间：仿真项目

问题描述：使用ted_local_planner_params.yaml后，进行仿真运动时出现如下报错。

![image-20220216115920653](https://gitee.com/mmeroke/picture_bed/raw/master/img/image-20220216115920653.png)

解决方法：

此处是*local_costmap_params.yaml*中`global_frame`需要设置为`map`，而不是`odam`

引用：https://answers.ros.org/question/304537/could-not-transform-the-global-plan-to-the-frame-of-the-controller/

QS4：rosdep update urlopen error [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed

![image-20220313152805086](/home/meroke/.config/Typora/typora-user-images/image-20220313152805086.png)

解决方法：

`export SSL_CERT_FILE=/usr/lib/ssl/certs/ca-certificates.crt`

引用：https://answers.ros.org/question/264262/rosdep-update-urlopen-error-ssl-certificate_verify_failed-certificate-verify-failed/

### QS4:手动安装编译movelt，缺少依赖

工作空间：仿真项目

由于需要python3的支持，不能直接使用apt 下载，采用手动编译方式。

在`~/ros_catkin_ws/src`，通过git clone方式从github下载相关包，一般可以使用`rosdep `,批量下载依赖包，但我由于存在一个依赖[libcurl-dev]的包报了cannot-locate-rosdep-definition的错误，由下列链接得知时没有对应key，需要修改对应包的package.xml里的依赖包名，

https://answers.ros.org/question/320734/cannot-locate-rosdep-definition/

[key值查询](https://github.com/ros/rosdistro/blob/0bec75fd1305910808b07776fbd1997a8c315998/rosdep/base.yaml#L4652-L4656)

但由于报错没说明是哪个包的依赖于[libcurl-dev]，故放弃rosdep方法，采用手动git clone下载。大部分包一般多在ros-planning仓库中，部分不在的，手动搜索也可以得到。

提示以下rosdep的用法：

一般是使用`rosdep install --from-paths src --ignore-src -y `自动批量安装src内包对应的依赖包，但由于我们使用python3支持，有些包是基于python2的，需要添加指令屏蔽这些包，完整指令如下：

```shell
rosdep install --from-paths src --ignore-src -y --skip-keys="`rosdep check --from-paths src --ignore-src | grep python | sed -e "s/^apt\t//g" | sed -z "s/\n/ /g"`"
```

**流程：**

[movelt 编译流程](https://blog.csdn.net/Rosen_er/article/details/122415302)

先在`~/ros_catkin_ws/src`，下载 movelt 的包，然后在`~/ros_catkin_ws/`运行`catkin build`，然后报出新错误再下载缺少的包，重复以上操作直至无错误，大约下了十几个包，才编译无误，正常运行。



### QS5：测试仿真机械臂时，需要用到joint_state_publisher_gui

工作空间：仿真项目

运行项目：`~/arm-master/src/arm_description/launch/rviz.launch`

项目环境： python3

出现报错：

![image-20220314131548194](/home/meroke/.config/Typora/typora-user-images/image-20220314131548194.png)

```shell
Traceback (most recent call last):
  File "/home/meroke/ros_catkin_ws/install/lib/joint_state_publisher_gui/joint_state_publisher_gui", line 44, in <module>
    import joint_state_publisher_gui
ImportError: No module named joint_state_publisher_gui
```

在之前已经通过`sudo apt install ros-melodic-joint_state_publisher_gui` 安装过了相关包，因此`/opt/ros/melodic/ ` 和 中查看，发现`/home/meroke/ros_catkin_ws/install`对比包文件，发现一致没有问题，如果不一致可以直接复制文件夹。

后来注意到报错是在python文件，因此 对比 `/opt/ros/melodic/lib/python2.7/dist-packages` 和 `/home/meroke/ros_catkin_ws/install/lib/python3/dist-packages` 发现，后者缺少` joint_state_publisher_gui`的文件夹，因此从前者复制到后者，即可解决问题。

![image-20220314131504978](/home/meroke/.config/Typora/typora-user-images/image-20220314131504978.png)



### QS6:缩小gampping建立的地图范围

工作空间: eaibot机器人

具体文件 `/home/eaibot/2022_jsjds/ros_base/src/dashgo/dashgo_nav/launch/include/imu/gmapping_base_adjusted.launch`

问题描述：gmapping生成的pgm地图，有效范围占比很小。通过修改上述文件部分参数，以及摆正机器人初始位置，可有效减少无用的地图范围。

![image-20220421220312119](https://gitee.com/mmeroke/picture_bed/raw/master/img/image-20220421220312119.png)



**修改重要参数：**

```html
      <param name="maxUrange" value="4.0" />   <!-- maxUrange < real sensor range < maxRange -->
      <param name="maxRange" value="5.0" />   <!--实际设置，如果大于5.0,如13.0， 将导致雷达光线外射，检测范围过大 -->
```

```html
     <!--超级重要， 经过测试，实际使用如下配置有效占比将接近一半-->
     <param name="xmin" value="-1.0" />
      <param name="ymin" value="-1.0" />
      <param name="xmax" value="5.0" />
      <param name="ymax" value="5.0" />	
	<!--注意 机器必须摆在初始位置，否则，倾斜的角度将增大无效地图 -->
```

![image-20220421221041983](https://gitee.com/mmeroke/picture_bed/raw/master/img/image-20220421221041983.png)



### QS7:非节点python文件调用ROS包

工作空间：eaibot机器人

问题描述：在detection2021中的ros文件夹下的ros python包无法调用

因此采用新建ros_packages_2022文件夹 将所需要的包git cloen 入src中，并重新编译，在所需运行的非节点python文件（slide.py）头上添加路径：

`sys.path.append('../ros_packages_2022/devel/lib/python3/dist-packages/')`

并且需要指定去除ROS 默认的python2.7的包文件夹

`sys.path.remove("/opt/ros/kinetic/lib/python2.7/dist-packages")`

![image-20220423201220723](/home/meroke/.config/Typora/typora-user-images/image-20220423201220723.png)



### QS8：Python pip3 开启ssl（HTTPS）

1. 下载openssl，并解压进入文件夹

```
wget http://www.openssl.org/source/openssl-1.1.1l.tar.gz
tar -xf openssl-1.1.1l.tar.gz
cd openssl-1.1.1l
```

2.  编译openssl

  ```
  sudo mkdir /usr/local/ssl 
  sudo ./config --prefix=/usr/local/ssl --openssldir=/usr/local/ssl 
  sudo make  && make install
  echo "/usr/local/ssl/lib" >> /etc/ld.so.conf  # 或者sudo vim /etc/ld.so.conf ,把/usr/local/ssl/lib追加到文件末尾
  sudo ldconfig -v 
  ```

3. .重编译Python

  **注意：这里要重开一个终端，让ssl的设置生效**

  ```
  cd /usr/local/Python-3.6.9
  sudo vim Modules/Setup   # 将红框的内容解注释 209行左右
  ```

![img](https://i0.hdslb.com/bfs/article/e0aa234a23ace10214d197768c8942f7f146ccaa.png@942w_321h_progressive.webp)

然后尝试 sudo make ,会报错（如下类似，图是网上找的，报错是 一致的）

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200411235941660.png)

此处由于/usr/local/lib/ 中没有 libssl.so.1.1，故我们从系统环境中搜索

```
cd ~/
find . -name "libssl.so.1.1"
```

```shell
(base) eaibot@EAI_LEO:~$ find . -name "libssl.so.1.1"
./anaconda3/pkgs/openssl-1.1.1i-h27cfd23_0/lib/libssl.so.1.1
./anaconda3/pkgs/openssl-1.1.1k-h27cfd23_0/lib/libssl.so.1.1
./anaconda3/pkgs/openssl-1.1.1g-h7b6447c_0/lib/libssl.so.1.1
./anaconda3/lib/libssl.so.1.1
./anaconda3/envs/pac_2021/lib/libssl.so.1.1
./anaconda3/envs/pro/lib/libssl.so.1.1
```

部分人可能在`/usr/lib/x86_64-linux-gnu/libssl.so.1.1`中也可找到

```
# 如果有文件，先备份文件,确保修改不会出现问题,如果出错可以返回原来配置，否则可忽略此步
sudo mv /usr/local/lib/libssl.so.1.1 /usr/local/lib/libssl.so.1.1.old
# 拷贝其他文件覆盖
sudo cp /usr/lib/x86_64-linux-gnu/libssl.so.1.1 /usr/local/lib/
```

再次make可能还有类似的错误，处理方法一样，不赘述了。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200411235853737.png)

直至make无误，最后

`make install`

测试 pip3 -V 是否正常显示

**参考链接：**

[(77条消息) mysql mysql: /usr/local/lib/libssl.so.1.1: version `OPENSSL_1_1_1' not found (required by mysql)_senjie_wang的博客-CSDN博客](https://blog.csdn.net/senjie_wang/article/details/105462629)

[(77条消息) 解决 /usr/local/lib/libssl.so.1.1: version OPENSSL_1_1_1 not found_sunhson的博客-CSDN博客_libssl.so.1.1](https://blog.csdn.net/sunhson/article/details/106476185)

[(77条消息) 解决openssl缺失libssl.so.1.1问题_zzwlyly的博客-CSDN博客_libssl.so.1.1](

### QS9:ROS安装 Python3的支持

#### 基础环境配置

首先通过本段链接安装`Melodic desktop full`版本ROS[ROS Melodic Install](http://wiki.ros.org/melodic/Installation/Ubuntu)，以下称其 `py2ROS`

然后按如下教程安装新的ROS[ROS  python3支持 ](https://www.miguelalonsojr.com/blog/robotics/ros/python3/2019/08/20/ros-melodic-python-3-build.html) ,以下称其`py3ROS`

安装两个版本的ROS的原因：

1. `py2ROS` 是运行底层ROS功能包的基础环境
2. `py3ROS`是用来配置python3 与ROS相关功能库的

在实际使用中仍然以`py2ROS`作为主环境，即最终在~/.bashrc中设置

`source /opt/ros/melodic/setup.bash `

基础环境配置完毕！

#### 项目需求环境

方法一：

由于导航运行环境是`py2ROS` ,但Desktop Full 仍不能满足项目运行的所有需要功能包，直接caktin_make项目会失败，需要手动安装部分包。因此可以使用rosdep进行批量安装

`rosdep install --from-paths src --ignore-src --rosdistro=melodic -y `

如有遗漏，再使用 `sudo apt install ros-melodic-[package]`

方法二：

也有想直接使用`py3ROS` 作为主要环境的，这是可以的，但不保证稳定性。对于缺少的包，需要自行从ROS Wiki 搜索，然后找到github源码链接，复制解压到 `py3ROS`的 `ros_catkin_ws/src`文件夹下，最后使用`catkin build package` 单独编译包。

PS：如何知道缺少了什么功能包，直接在项目下catkin_make 后，有缺失的依赖会报错提示的

### QS10：连接新机器时，远程rviz显示无map

问题详述：使用新的jetson nano 完成系统配置后，尝试远程连接控制，端口映射无误、功能包齐全，也无报错。但是打开rviz, 什么也没显示，只有网格。

问题原因：主机和从机的ip地址没有匹配，导致雷达数据没有发送到PC端。

问题解决:

**PC端**

在 ~/.bashrc中修改：

```shell
export ROS_HOSTNAME=meroke-W650KJ1-KK1  # PC 名
export ROS_MASTER_URI=http://192.168.31.157:11311   #  小车IP
```

并在 /etc/hosts 中添加：

```shell
192.168.31.157 tdb2  # 小车 ip
192.168.31.226 meroke-W650KJ1-KK1  # PC ip
```

**小车**

在 ~/.bashrc中添加：

```shell
export ROS_MASTER_URI=http://192.168.31.157:11311
export ROS_HOSTNAME=tdb2
```

并在 /etc/hosts 中添加：

```shell
# ROS host
192.168.31.226 meroke-W650KJ1-KK1
192.168.31.157 tdb2
```



### QS11：单独编译一个功能包



```shell
catkin_make -DCATKIN_WHITELIST_PACKAGES="package1;package2"
```

如果想要再回到那种catkin_make 编译所有包的状态，执行：

```bash
catkin_make -DCATKIN_WHITELIST_PACKAGES=""
```



### QS12: catkin build fatial

```shell
Errors     << qt_gui_cpp:make /home/tdb2/ros_catkin_ws/logs/qt_gui_cpp/build.make.030.log                                                                                                                
In file included from siplibqt_gui_cpp_sipcmodule.cpp:7:0:
sipAPIlibqt_gui_cpp_sip.h:10:10: fatal error: sip.h: No such file or directory
 #include <sip.h>
```

- sudo apt install python3-sip-dev

```
By not providing "FindPCL.cmake"
```

```shell
sudo apt-get install python3-empy \
gnome-devel \
 libgazebo9-dev \
 libgtk2.0-dev \
 libgtk-3-dev \

```

### QS13:ROS python3 支持问题汇总

#### 1.1 

```
Installing python33-rospkg-modules
E: 无法定位软件包 python33-rospkg-modules
Installing python33-catkin-pkg
E: 无法定位软件包 python33-catkin-pkg
Installing python33-nose
E: 无法定位软件包 python33-nose
Installing python33-rospkg
E: 无法定位软件包 python33-rospkg
Installing python3-mock
Installing python3-coverage
Installing python33-numpy
E: 无法定位软件包 python33-numpy
Installing python33-pydot
E: 无法定位软件包 python33-pydot
```



```shell
sudo apt install python3-rospkg-modules 
python3-catkin-pkg
python3-nose
python3-rospkg
python3-mock
python3-coverage
python3-numpy
python3-pydot
```

```
pip3 install empy
```



#### 1.2 catkin build error

- Errro01

```shell
Errors     << genmsg:install /home/eaibot/2022_jsjds/ros_ws/logs/genmsg/build.install.008.log                                                                 
usage: setup.py [global_opts] cmd1 [cmd1_opts] [cmd2 [cmd2_opts] ...]
   or: setup.py --help [cmd1 cmd2 ...]
   or: setup.py --help-commands
   or: setup.py cmd --help

error: option --install-layout not recognized
CMake Error at catkin_generated/safe_execute_install.cmake:4 (message):
  
  execute_process(/home/eaibot/2022_jsjds/ros_ws/build/genmsg/catkin_generated/python_distutils_install.sh)
  returned error code
Call Stack (most recent call first):
  cmake_install.cmake:147 (include)
```



catkin build （python3 支持）可参考指令：

```
catkin build python_orocos_kdl -DPYTHON_EXECUTABLE=/usr/bin/python3 -DSETUPTOOLS_DEB_LAYOUT=OFF -DPYTHON_INCLUDE_DIR=/usr/local/python3/include/python3.6m -DPYTHON_LIBRARY=/usr/local/python3/lib/libpython3.so
```

- Error02

发现 DPYTHON_LIBRARY 没有对应的python动态库.so文件，需要重新手动编译python

<details>
    <summary>手动编译Python生成动态库(Solved)</summary>
    <pre><blockcode>
            引用：https://blog.csdn.net/lien0906/article/details/107361822
            1. 在下载的python文件夹下，执行指令：
            sudo ./configure --prefix=/usr/local/python3 --enable-shared CFLAGS=-fPIC 
            2.  make
            3.  sudo make install
   </blockcode> </pre>
</details>

- Error03

```shell
Assertion failed: check for file existence, but filename (RT_LIBRARY-NOTFOUND) unset. Message: RT Library
```

<details>
    <summary>Solved</summary>
    <pre>
    	<blockcode>
    		catkin clean  # 清理之前得到错误残留
    		catkin build  # 重新编译
    	</blockcode>
    </pre>
</details>



- Error04 

```shell
 Could not find the following Boost libraries:

          boost_python3
```

<details>
    <summary>Solved</summary>
    <pre>
    	<blockcode>
    	//到libboost_python-py35所在文件夹下，建立软连接
        cd /usr/lib/x86_64-linux-gnu/
        sudo ln -s libboost_python-py35.so libboost_python3.so
        sudo ln -s libboost_python-py35.a libboost_python3.a
    	</blockcode>
    </pre>
</details>


- Error05

```

Traceback (most recent call last):
  File "/home/tdb3/ros_catkin_ws/src/gencpp/scripts/gen_cpp.py", line 43, in <module>
    import genmsg.template_tools
  File "/home/tdb3/ros_catkin_ws/src/genmsg/src/genmsg/template_tools.py", line 39, in <module>
    import em
ModuleNotFoundError: No module named 'em'
```

```
pip3 install empy
```





#### 1.3 Could not find SIP

下载[SIP源文件](https://link.zhihu.com/?target=https%3A//sourceforge.net/projects/pyqt/files/sip/sip-4.19.5/sip-4.19.5.tar.gz)，进入下载文件夹，执行以下指令，即可完成

```text
python configure.py
make
sudo make install
```



#### QS14 :雷达偏移全局地图

方法：

确定不是算法问题后，尝试给轮胎增加摩擦力，如贴上胶带



#### QS15:eaibot 接入网线仍然无法上网

尝试重启网络服务

```
sudo service networking restart
```

![image-20220806222435692](/home/meroke/.config/Typora/typora-user-images/image-20220806222435692.png)



#### QS16:小车容易撞到物体，减速不足

主要依靠`penalty_epsilon`,其值应该接近 `max_val_x` 

如  `max_val_x` 设为0.5，则`penalty_epsilon`可设为0.4 ，减速效果良好。

但避免导航产生负值速率，`acc_lim_x `应该大于 `penalty_epsilon` ，可设为 0.401

#### QS17: 小车在目标点前后震荡，不到达目标点

方法：

1. 降低move_base 控制频率
2. 关闭速度平滑器yocs velocity smoothe



##  新机初始配置

### 基础ROS环境

本文安装的版本是 ROS Melodic，使用的系统是 Ubuntu18.04

1. 添加镜像源，我这里设置的是清华的镜像源

```
sudo sh -c '. /etc/lsb-release && echo "deb http://mirrors.tuna.tsinghua.edu.cn/ros/ubuntu/ `lsb_release -cs` main" > /etc/apt/sources.list.d/ros-latest.list'
```

2. 设置密钥

```
sudo apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654
```

3. 更新软件索引：

   ```
   sudo apt update
   ```

4. 安装
   桌面完整版（推荐）： 包含 ROS、rqt、rviz、机器人通用库、2D/3D 模拟器、导航以及 2D/3D 感知包。

   ```
   sudo apt install ros-melodic-desktop-full
   ```





配置好环境后，拉取新的仓库。仓库内一般只有src文件夹，故需要本地重新配置环境，在仓库目录内，执行指令`rosdep install --from-paths src --ignore-src -r -y` 一键安装项目所有需要的功能包。

2.github相关环境

配置github密钥：

1. 本机运行`ssh-keygen` ，一路回车；
2. 使用cat ~/.ssh/id_rsa.pub 查看内容，并复制到github的SSH keys中。

![image-20221031150613109](E:\File\TyporaNote\img\ROS查找手册\image-20221031150613109.png)

![image-20221031150639216](E:\File\TyporaNote\img\ROS查找手册\image-20221031150639216.png)



添加Host：

完成上述的步骤后，尝试拉取仓库，仍会报错无权限或无连接，此处是因为网络连接问题，故修改host可解决。

1. 编辑 `sudo vim /etc/hosts`
2. 在页尾插入[2022年 github被墙最新hosts-每日更新 | 天问博客 (yoqi.me)](http://blog.yoqi.me/lyq/16489.html)中的全部host。

注意此方法是有一定时效性的，需要定期更新host

此时即可正常拉取仓库。


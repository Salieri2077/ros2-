ros2_simulate
1中主要是利用gazebo插件为模型搭载camera、深度camera、激光雷达等，并在rviz2中可以进行可视化观察。
2中则主要实现利用imu和激光雷达进行导航控制、自主扫描等功能。
以下是使用办法：
采用的是Ubuntu22.04安装的是ros2的humble版本，你需要安装gazebo、rviz2软件

# 安装cartographer建图
sudo apt install ros-humble-cartographer
sudo apt install ros-humble-cartographer-ros
# 安装导航包
sudo apt install ros-humble-navigation2
sudo apt install ros-humble-nav2-bringup
# 安装vcstool工具
sudo apt install python3-vcstool
# gazebo ROS相关包安装
sudo apt install ros-humble-gazebo-ros*
wegt https://github.com/Salieri2077/ros2-.git
# 然后将1和2中文件——dev_ws和tb3_ws剪切至主文件夹中进行下述操作
# 文件2：
# 添加环境变量
vcs import src < turtlebot3.repos
colcon build --symlink-install

echo 'source ~/tb3_ws/install/setup.bash' >> ~/.bashrc
echo 'export ROS_DOMAIN_ID=30 #TURTLEBOT3' >> ~/.bashrc
source ~/.bashrc
# 下载gazebo的模型，加速运行gazebo
cd ~/.gazebo/
git clone https://github.com/osrf/gazebo_models models
rm -rf models/.git
# 编译 tb3_ws
 cd ~/tb3_ws
 vcs import src < turtlebot3.repos
 colcon build --symlink-install
# 设置GAZEBO_MODEL_PATH变量, 指定机器人类型为burger
 echo 'export GAZEBO_MODEL_PATH=$GAZEBO_MODEL_PATH:~/tb3_ws/src/turtlebot3/turtlebot3_simulations/turtlebot3_gazebo/models' >> ~/.bashrc
 echo 'export TURTLEBOT3_MODEL=burger' >> ~/.bashrc
 source ~/.bashrc
# 启动Fake Node+键盘控制
cd ~/.gazebo/
ros2 launch turtlebot3_fake_node turtlebot3_fake_node.launch.py
ros2 run turtlebot3_teleop teleop_keyboard

# 启动地图
ros2 launch turtlebot3_gazebo empty_world.launch.py # empty地图
ros2 launch turtlebot3_gazebo turtlebot3_world.launch.py # world地图
ros2 launch turtlebot3_gazebo turtlebot3_house.launch.py # house地图

# gazebo启动
ros2 launch turtlebot3_gazebo turtlebot3_world.launch.py # world地图
# 键盘控制
 ros2 run turtlebot3_teleop teleop_keyboard

# 自走避障
ros2 launch turtlebot3_gazebo turtlebot3_world.launch.py # world地图
ros2 run turtlebot3_gazebo turtlebot3_drive

# rviz2模型显示
ros2 launch turtlebot3_gazebo turtlebot3_house.launch.py # world地图
ros2 launch turtlebot3_bringup rviz2.launch.py # 启动rviz2

# turtlebot3建图 
ros2 launch turtlebot3_gazebo turtlebot3_world.launch.py # world地图
ros2 launch turtlebot3_cartographer cartographer.launch.py use_sim_time:=True # 运行建图
ros2 run turtlebot3_teleop teleop_keyboard # 启动键盘
ros2 run nav2_map_server map_saver_cli -f ~/map # 新开终端，保存地图

# 导航
cd ~/.gazebo
ros2 launch turtlebot3_gazebo turtlebot3_world.launch.py
ros2 launch turtlebot3_navigation2 navigation2.launch.py use_sim_time:=True map:=/home/salieri/tb3_ws/src/turtlebot3/turtlebot3/turtlebot3_navigation2/map/map.yaml
# 需要主要更改为你自己的绝对路径

# 文件1
# 使用rosdep工具自动安装依赖
sudo apt install -y python3-pip
sudo pip3 install rosdepc
sudo rosdepc init
rosdepc update
cd ..
rosdepc install -i --from-path src --rosdistro humble -y
# 进行代码的编译
sudo apt install python3-colcon-ros
cd ~/dev_ws/
colcon build
# 设置环境变量
echo " source ~/dev_ws/install/local_setup.sh" >> ~/.bashrc # 所有终端均生效
# 运行两句命令，第一句启动仿真环境，第二句启动键盘控制节点。
ros2 launch learning_gazebo load_urdf_into_gazebo.launch.py
ros2 run teleop_twist_keyboard teleop_twist_keyboard
# 点击Add，添加PointCloud2，设置订阅的点云话题，还要配置Rviz的参考系是odom，就可以看到点云数据啦，每一个点都是由xyz位置和rgb颜色组成。 
ros2 launch learning_gazebo load_mbot_rgbd_into_gazebo.launch.py
ros2 run rviz2 rviz2
# 该文件夹中的其他load_mbot_xxxx.launch.py也三采用同样的流程

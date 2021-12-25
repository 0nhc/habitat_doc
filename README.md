# Instructions
## 1.NUC的环境是完整的，使用步骤如下：
* 1.1 启动底盘电源 然后按下NUC开机键（如果只是为了开发程序的话建议给NUC外接电源 底盘的电池大概只能供电20分钟）
* 1.2 NUC开机后等待自动连接到一个wifi（名字应该是RM什么的 记不清了）
* 1.3 建图命令（注意，这会自动覆盖以前建好的地图~/.ros/rtabmap.db）：roslaunch ep_navigation rtab_mapping.launch 建图结束后在终端ctrl+C即可，系统会自动保存地图在~/.ros/rtabmap.db 如果地图数据比较重要记得把这个文件拷贝出来
* 1.4 导航指令 roslaunch ep_navigation rtab_navigation.launch 会自动调取~/.ros/rtabmap.db地图数据 注意 这里的initial_pose话题变成了/rtabmap/initial_pose，如果要从rviz窗口里设置initial_pose需要更改rviz发出的topic名(这个launch文件启动的rviz已经被我改好了)

## 2.habitat环境
* 2.1 具体的habitat底层环境是飞哥帮我配置的 我不太会 泽浩应该也会搞 可以follow[这里](https://github.com/0nhc/ros_x_habitat)的readme试试
* 2.2 python环境是要用anaconda来装 具体也是飞哥帮我搞的 其实用anaconda新建一个环境然后运行启动之后看报错慢慢装也可以（python3.6） 不过需要注意的是pyqt的版本不能用最新版 我们装的是5.10才可以
* 2.3 我负责弄好的是habitat和ros的接口 原始版本的ros包有点问题 我fork下来改了一下 所以需要clone我这里的版本 具体操作如下

```sh
cd ~
mkdir habitat_ros_ws
cd habitat_ros_ws
mkdir src
cd src
catkin_init_workspace
git clone --recuirsive https://github.com/0nhc/ros_x_habitat.git
cd ..
rosdep install --from-paths src --ignore-src --rosdistro=${ROS_DISTRO} -y
sudo apt-get update
sudo apt-get install ros-noetic-depthimage-to-laserscan
sudo apt-get install ros-noetic-laser-scan-matcher
sudo apt-get install ros-noetic-joy
sudo apt-get install ros-noetic-rtabmap-ros
catkin_make
echo 'export PYTHONPATH=$PYTHONPATH:~/habitat_ros_ws/src/ros_x_habitat' >> ~/.bashrc
```
然后如果catkin_make报错的话应该就是缺少ros依赖的问题 缺什么装什么了只能 有问题可以问我
然后运行habitat的话具体的配置文件什么的
应该是在~/habitat_ros_ws/src/ros_x_habitat/data
这些可能也会有问题 如果运行报错建议问下飞哥或者泽浩

* 2.4 运行指令
 启动时电脑必须接着PS4手柄 要不然会启动不了
```sh
cd ~/habitat_ros_ws/src/ros_x_habitat
python3 src/scripts/roam_with_joy.py --hab-env-config-path ./configs/roam_configs/pointnav_rgbd_roam_mp3d_test_scenes.yaml --episode-id -1 --scene-id ./data/scene_datasets/mp3d/2t7WUuJeko7/2t7WUuJeko7.glb --video-frame-period 60
```
然后新开一个terminal
建图命令（注意，这会自动覆盖以前建好的地图~/.ros/rtabmap.db）：roslaunch habitat_navigation rtab_mapping.launch 用手柄遥控 建图结束后在终端ctrl+C即可，系统会自动保存地图在~/.ros/rtabmap.db 如果地图数据比较重要记得把这个文件拷贝出来

这里的导航目前还有点问题 因为同时会有手柄节点发送速度指令为0的cmd_vel 而move_base也会发送规划好路径之后的cmd_vel 所以导航起来会一卡一卡的
导航指令 roslaunch habitat_navigation rtab_navigation.launch 会自动调取~/.ros/rtabmap.db地图数据 注意 这里的initial_pose话题变成了/rtabmap/initial_pose，如果要从rviz窗口里设置initial_pose需要更改rviz发出的topic名(这个launch文件启动的rviz已经被我改好了) 

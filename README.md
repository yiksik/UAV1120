# UAV1120
声明：“T265+D435i+px4方案，仅供实验室调试备份使用，无盈利。”

## 无人机配置
机载电脑：Jetson Xavier NX

操作系统：Ubuntu 20.04.6

传感器：T265、D435i

飞控：PX4

realsense、eigen、opencv、glog等等库的版本参考Fast-Drone-250即可，如果当前版本的realsense不兼容T265，就降版本（因为T265已停产，新版本可能已无法使用）

## 使用方法
### 1.克隆代码并编译
1. 在src的上一级文件夹，输入：
```
catkin_make
```
2. 配置环境变量，此后默认工作空间是~/catkin_ws/
```
sudo nano ~/.bashrc
```
将source ~/catkin_ws/devel/setup.bash加到最后
```
source ~/.bashrc
```
### 2.Realsense
1. 进入~/catkin_ws/src/realsense-ros/realsense2_camera/launch/t265_d435i.launch找到：
```
<node pkg="tf" type="static_transform_publisher" name="t265_to_d435" args="0.04 0 -0.03 0 0 0 /$(arg tf_prefix_camera1)_link /$(arg tf_prefix_camera2)_link 100"/>
```
修改两个相机的tf变换，注意是t265在d435i坐标系下的坐标和欧拉角。

2. 启动相机：
```
roslaunch realsense2_camera t265_d435i.launch
```
3. 新开一个终端：
```
rostopic list
```
此时应该存在T265发布的里程计/camera/odom/sample和D435i发布的深度图像/d435/depth/image_rect_raw

验证T265发布的里程计是否正常：
```
rostopic echo /camera/odom/sample
```

验证D435i发布的深度图像是否正常：
```
rviz
```
订阅image话题/d435/depth/image_rect_raw

同时可以使用
```
rostopic hz /topicName
```
来获取当前话题发布频率

### 3.VIO
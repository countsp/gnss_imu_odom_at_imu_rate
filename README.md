参考资料：

https://zhuanlan.zhihu.com/p/627857398?eqid=c665abc3000191590000000364706c54

源码链接：

https://gitee.com/dawanzi98/gnss_imu_odom_at_imu_rate

**安装依赖**

sudo apt-get install libpcl-dev libgeographic-dev

**编译package**
```
mkdir -p ~/eskf/src
cd ~/eskf/src
git clone https://gitee.com/dawanzi98/gnss_imu_odom_at_imu_rate.git
cd ../..
catkin_make
```
**报错1：**

GeographicLib Not Found

解决：

使用命令在Ubuntu上安装Geographiclib库
```
sudo apt_get install libgeographic-dev
```

然后在CMakeLists.txt文件的顶部添加

list(APPEND CMAKE_MODULE_PATH "/usr/share/cmake/geographiclib")

告诉CMake在查找包时也搜索/usr/share/cmake/geographiclib目录。

**报错2：**

fatal error: color_terminal/color_terminal.hpp: No such file or directory

 2 | #include <color_terminal/color_terminal.hpp>
 
解决：

gnss_imu_fusion.cpp中

注释掉

// #include <color_terminal/color_terminal.hpp>

**运行**

~/eskf$ source devel/setup.bash
~/eskf$ roslaunch gnss_imu_fusion kitti.launch


**消息格式**
```
namespace sensor_msgs
{
  struct Imu
  {
    std_msgs::Header header;  // 消息的头部信息（时间戳，帧等）
    geometry_msgs::Quaternion orientation;  // 机器人方向的四元数
    geometry_msgs::Vector3 angular_velocity;  // 角速度
    geometry_msgs::Vector3 linear_acceleration;  // 线性加速度
  };
}


sensor_msgs::NavSatFix
std_msgs::Header header;  // 消息头
NavSatStatus status;      // 定位状态，描述定位的有效性
double latitude;          // 纬度，单位：度
double longitude;         // 经度，单位：度
double altitude;          // 海拔高度，单位：米
double position_covariance[9]; // 协方差矩阵（3x3）
uint8 position_covariance_type; // 协方差矩阵类型

static nav_msgs::Path path;
path.header.frame_id = "map";
geometry_msgs::PoseStamped pose;
pose.pose.position.x = state.position.x();
pose.pose.position.y = state.position.y();
pose.pose.position.z = state.position.z();
path.poses.push_back(pose);
path_pub.publish(path);

auto q = state.rotation;
nav_msgs::Odometry odom_message;
odom_message.header.stamp = ros::Time().fromSec(time);
odom_message.header.frame_id = "map";
odom_message.child_frame_id = "body";
odom_message.pose.pose.position.x = state.position.x();
odom_message.pose.pose.position.y = state.position.y();
odom_message.pose.pose.position.z = state.position.z();
odom_message.pose.pose.orientation.w = q.w();
odom_message.pose.pose.orientation.x = q.x();
odom_message.pose.pose.orientation.y = q.y();
odom_message.pose.pose.orientation.z = q.z();

odom_pub.publish(odom_message);
```

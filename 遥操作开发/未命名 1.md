

当前机器还没有发现 `can0` 和 `can1`。先确认 CAN 驱动已经创建接口：

```
ip -brief link show can0
ip -brief link show can1
```

CAN 存在后启动。

终端一：OpenArmX 真机与位置控制器

```
cd /home/maple/hc_openarmx

ROS_DOMAIN_ID=13 \
USE_CONDA=false \
RIGHT_CAN=can0 \
LEFT_CAN=can1 \
make bringup-forward
```

终端二：OpenArmX 到 HC 标准话题的硬件适配器

```
source /opt/ros/humble/setup.bash
source /home/maple/hc_openarmx/install/setup.bash

ros2 launch openarmx_hc_robot_adapter \
  openarmx_hc_robot_adapter.launch.py \
  ros_domain_id:=13
```

它负责：

```
/joint_states
    → /hc_teleop/joint_states

/hc_teleop/joint_cmd
    → /right_forward_position_controller/commands
    → /left_forward_position_controller/commands
```

终端三：启动 test2 遥操作中间件、VR 和 IK

```
cd /home/maple/test2/HC-teleop-robotic
ROS_DOMAIN_ID=13 ./run.sh teleop
```

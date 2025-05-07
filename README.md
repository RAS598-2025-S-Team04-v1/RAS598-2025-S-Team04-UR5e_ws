# RAS598-2025-S-Team04-UR5e_ws
Workspace for UR5e and Gripper.
The following repository contains the workspace for the UR5 and the Gripper to function based on published OptiTrack values

## Steps to setup the UR5e:

Step 1: Install the UR5e drivers:(Calibration)
<pre><code># Update your package list and install dependencies
sudo apt update
sudo apt install -y ros-humble-ur-client-library ros-humble-ur-msgs ros-humble-control-msgs
Note: To be safe clone the humble branch: git clone -b humble https://github.com/UniversalRobots/Universal_Robots_ROS2_Driver.git
cd ~/ros2_ws
colcon build --symlink-install</pre></code>

Or

<pre><code>sudo apt-get install ros-humble-ur</code></pre>

*Note: This installs both, the driver as well as moveit.
Add <code>launch_rviz:=false</code> at the end of driver launch command to avoid unnecesary rviz: <code>ros2 launch ur_robot_driver ur_control.launch.py ur_type:=ur5e robot_ip:=192.168.1.103 launch_rviz:=false</code>

Step 2: CycloneDDS
<CycloneDDS>
<DDS>
<Domain>
<General>
<DontRoute>true</DontRoute>
<AllowMulticast>true</AllowMulticast>
<EnableMulticastLoopback>true</EnableMulticastLoopback>
</General>
<Discovery>
<ParticipantIndex>auto</ParticipantIndex>
<Peers>
<Peer Address="10.166.219.8"/>
<Peer Address="10.166.219.11"/>
<Peer Address="10.166.219.247"/>
<Peer Address="10.166.219.100"/>
<Peer Address="192.168.1.103"/>
</Peers>
</Discovery>
</Domain>
</DDS>
</CycloneDDS>

Step 3: Install required ROS2 driver/dependencies

<pre><code>sudo apt install -y \
  ros-humble-ros2-control \
  ros-humble-ros2-controllers \
  ros-humble-serial-driver \
  ros-humble-urdf \
  ros-humble-control-msgs \
  libboost-all</code></pre>

Step 4: UR5e launch codes:
  <pre><code>ros2 launch ur_robot_driver ur_control.launch.py ur_type:=ur5e robot_ip:=192.168.1.103 launch_rviz:=false use_external_control:=true</code></pre>


## To setup the gripper:


Step 1: Install required packages

<pre><code>cd ~/ros2_ws/src
git clone -b foxy https://github.com/PickNikRobotics/robotiq_85_gripper.git
cd ~/ros2_ws
rosdep install --from-paths src --ignore-src -r -y
colcon build --symlink-install
sudo adduser $USER dialout</code></pre>

Step 2: In gripper.driver.launch.py

<pre><code>port="/tmp/ttyUR"</code></pre>

Step 3: Launch

<pre><code>ros2 launch ur_robot_driver ur_control.launch.py \
ur_type:=ur5e \
robot_ip:=192.168.1.103 \
launch_rviz:=false \
use_external_control:=true</code></pre>

On the teach pendant:
Navigate to Program → URCaps → External Control and press Play

Step 4: Creating a virtual serial Port

<pre><code>export ROBOT_IP=192.168.1.103
export LOCAL_DEVICE_NAME=/tmp/ttyUR
socat pty,link=${LOCAL_DEVICE_NAME},raw,ignoreeof,waitslave tcp:${ROBOT_IP}:54321</code></pre>

Step 5: Launching the gripper

<pre><code>source ~/ros2_ws/install/setup.bash
ros2 launch robotiq_85_driver gripper_driver.launch.py</code></pre>






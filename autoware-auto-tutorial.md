## Installation

- Install ADE from here https://autowarefoundation.gitlab.io/autoware.auto/AutowareAuto/installation.html
- Setup ADE and project checkout

```console
$ mkdir ~/adehome
$ cd ~/adehome
$ touch .adehome
```
- Checkout the project

```console
$ cd ~/adehome
$ git clone https://gitlab.com/autowarefoundation/autoware.auto/AutowareAuto.git
```

## How to build

```console
$ ade start --update
$ ade enter
ade$ cd AutowareAuto
ade$ vcs import < autoware.auto.$ROS_DISTRO.repos 
ade$ colcon build
ade$ colcon test
ade$ colcon test-result
```

## NDT EKF localization

```console
$ ade start --update
$ ade enter
ade$ cd AutowareAuto
ade$ source /opt/AutowareAuto/setup.bash
ade$ ros2 launch autoware_demos ekf_ndt_smoothing_lgsvl.launch.py
```

## 3D Lidar Perception Stack

### Running the rviz2 visualizer

```console
ade$ source /opt/AutowareAuto/setup.bash
ade$ rviz2 -d /opt/AutowareAuto/share/autoware_auto_examples/rviz2/autoware_perception_stack.rviz
```

### Publishing sensor data

- Download the PCAP file [Dual VLP-16 Hi-Res pcap][https://autoware-auto.s3.us-east-2.amazonaws.com/route_small_loop_rw.pcap]
- Move the downloaded file into `~/adehome/data`
- Replay the Lidar data packages using `udpreplay`

```console
ade$ source /opt/AutowareAuto/setup.bash
ade$ udpreplay -r -1 data/route_small_loop_rw.pcap
```
- Launch the velodyne_node for reading the front lidar
```console
ade$ ros2 run velodyne_node velodyne_cloud_node_exe --model vlp16 __ns:=/lidar_front __params:=/opt/AutowareAuto/share/velodyne_node/param/vlp16_test.param.yaml
```
- Launch the velodyne_node for the rear lidar
```console
ade$ ros2 run velodyne_node velodyne_cloud_node_exe --model vlp16 __ns:=/lidar_rear __params:=/opt/AutowareAuto/share/velodyne_node/param/vlp16_test_rear.param.yaml
```

### Publishing the robot state

```console
ade$ source /opt/AutowareAuto/setup.bash
ade$ ros2 run robot_state_publisher robot_state_publisher /opt/AutowareAuto/share/lexus_rx_450h_description/urdf/lexus_rx_450h_pcap.urdf
```

### Run the point cloud filter transform node

```console 
ade$ source /opt/AutowareAuto/setup.bash
ade$ ros2 run point_cloud_filter_transform_nodes point_cloud_filter_transform_node_exe __ns:=/lidar_front __params:=/opt/AutowareAuto/share/point_cloud_filter_transform_nodes/param/vlp16_sim_lexus_filter_transform.param.yaml __node:=filter_transform_vlp16_front  --remap points_in:=/lidar_front/points_raw
```

### Run the ray ground classifier node

```console
ade$ source /opt/AutowareAuto/setup.bash
ade$ ros2 run ray_ground_classifier_nodes ray_ground_classifier_cloud_node_exe __params:=/opt/AutowareAuto/share/ray_ground_classifier_nodes/param/vlp16_lexus.param.yaml --remap points_in:=/lidar_front/points_filtered
```

### Run the Euclidean cluster node

```console
ade$ source /opt/AutowareAuto/setup.bash
ade$ ros2 run euclidean_cluster_nodes euclidean_cluster_node_exe __params:=/opt/AutowareAuto/share/euclidean_cluster_nodes/param/vlp16_lexus_cluster.param.yaml --remap points_in:=/points_nonground
```

## 安装 orbbec+rtabmap
sudo apt install ros-humble-orbbec-camera ros-humble-orbbec-camera-msgs

sudo apt install ros-humble-rtabmap-ros ros-humble-rtabmap-launch ros-humble-rtabmap-rviz-plugins ros-humble-rtabmap-viz

sudo apt install ros-humble-depth-image-proc ros-humble-image-transport-plugins ros-humble-stereo-image-proc

## 运行

### 加载路径
source /opt/ros/humble/setup.bash

### 启动 Orbbec 相机节点（开一个终端）
ros2 launch orbbec_camera gemini_330_series.launch.py \
    color_width:=640 color_height:=480 color_fps:=15 \
    depth_width:=640 depth_height:=480 depth_fps:=15

ros2 launch orbbec_camera gemini_330_series.launch.py \
    color_width:=320 color_height:=240 color_fps:=30 \
    depth_width:=320 depth_height:=240 depth_fps:=30


### 启动 RTAB‑Map 建图
ros2 launch rtabmap_launch rtabmap.launch.py \
    rtabmap_args:="--delete_db_on_start" \
    depth_topic:=/camera/depth/image_raw \
    rgb_topic:=/camera/color/image_raw \
    camera_info_topic:=/camera/color/camera_info \
    frame_id:=camera_link \
    approx_sync:=true \
    sync_queue_size:=20 \
    topic_queue_size:=20

### 保存点云 / RGB‑D 图
RTAB‑Map 运行时会自动将数据写入 root/.ros/rtabmap.db（SQLite 数据库）。
停止建图后（Ctrl+C 终止节点），可通过以下方式导出：

方式 A：命令行导出（在板端执行）
bash
 只导出关键帧图像
rtabmap-export --images ~/.ros/rtabmap.db

 导出图像 + 点云（生成 .ply / .pcd / .obj）
rtabmap-export --images --cloud ~/.ros/rtabmap.db

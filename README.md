# Fast\-livo2

一种快速、稳健且通用的 LiDAR 惯性里程计框架。 FASTLIO2 建立在高效紧耦合迭代[卡尔曼滤波器](https://zhida.zhihu.com/search?content_id=192992738&content_type=Article&match_order=1&q=%E5%8D%A1%E5%B0%94%E6%9B%BC%E6%BB%A4%E6%B3%A2%E5%99%A8&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3Nzc1MTMzNDMsInEiOiLljaHlsJTmm7zmu6Tms6LlmagiLCJ6aGlkYV9zb3VyY2UiOiJlbnRpdHkiLCJjb250ZW50X2lkIjoxOTI5OTI3MzgsImNvbnRlbnRfdHlwZSI6IkFydGljbGUiLCJtYXRjaF9vcmRlciI6MSwiemRfdG9rZW4iOm51bGx9.dnZRkosAA-e8Qfc8zf9tPuWMjfmNAA2KnKRaE_jq33Q&zhida_source=entity)的基础上，具有两个关键创新，可实现快速、稳健和准确的 LiDAR 导航（和映射）。第一个是直接将原始点注册到地图（并随后更新地图，即映射）而不提取特征。这可以利用环境中的细微特征，从而提高准确性。手动设计的特征提取模块的消除也使其自然适应不同扫描模式的新兴激光雷达；第二个主要创新是通过增量 k\-d 树数据结构 [ikd\-Tree](https://zhida.zhihu.com/search?content_id=192992738&content_type=Article&match_order=1&q=ikd-Tree&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3Nzc1MTMzNDMsInEiOiJpa2QtVHJlZSIsInpoaWRhX3NvdXJjZSI6ImVudGl0eSIsImNvbnRlbnRfaWQiOjE5Mjk5MjczOCwiY29udGVudF90eXBlIjoiQXJ0aWNsZSIsIm1hdGNoX29yZGVyIjoxLCJ6ZF90b2tlbiI6bnVsbH0.iepyb_e-eMRC1AxCwUe8rjIUNaHRp-ri__kc09UACV8&zhida_source=entity) 维护映射，该结构支持增量更新（即点插入、删除）和动态重新平衡。与现有的动态数据结构（octree、R∗\-tree、nanoflann k\-d tree）相比，ikd\-Tree 在整体性能优越的同时自然支持对树的下采样。我们对来自各种开放 LiDAR 数据集的 19 个序列进行了详尽的基准比较。与其他最先进的 LiDAR 惯性导航系统相比，FAST\-LIO2 始终以更低的计算负载实现更高的精度。还对具有小 FoV 的固态 LiDAR 进行了各种真实世界的实验。总体而言，FAST\-LIO2 具有计算效率（例如，在大型室外环境中高达 100 Hz 的里程计和映射）、鲁棒性（例如，在旋转高达 1000 度 = s 的杂乱室内环境中可靠的姿态估计）、多功能（即，适用于多线旋转和[固态激光雷达](https://zhida.zhihu.com/search?content_id=192992738&content_type=Article&match_order=1&q=%E5%9B%BA%E6%80%81%E6%BF%80%E5%85%89%E9%9B%B7%E8%BE%BE&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3Nzc1MTMzNDMsInEiOiLlm7rmgIHmv4DlhYnpm7fovr4iLCJ6aGlkYV9zb3VyY2UiOiJlbnRpdHkiLCJjb250ZW50X2lkIjoxOTI5OTI3MzgsImNvbnRlbnRfdHlwZSI6IkFydGljbGUiLCJtYXRjaF9vcmRlciI6MSwiemRfdG9rZW4iOm51bGx9.V10hpqAFUdbeEq54D0ZGU3x80Rvh2W1zoXZmN-y-6NA&zhida_source=entity)、无人机和手持平台，以及基于英特尔和 ARM 的处理器），同时仍能实现比现有方法更高的精度。

设备 Livox Mid\-360

步骤

1. 安装sophus

```Plain Text
git clone https://gitee.com/beichenlee/sophus.git
cd sophus
mkdir build && cd build && cmake ..
make
sudo make install
```

2. 安装系统级依赖 \(Livox\-SDK2\)

```Bash
# 找个地方下载 SDK (比如主目录)
cd ~
git clone https://github.com/Livox-SDK/Livox-SDK2.git
cd Livox-SDK2
mkdir build && cd build
cmake .. -DCMAKE_BUILD_TYPE=Release
make -j$(nproc)
sudo make install
```

3. 创造工作空间并下载代码

```Plain Text
mkdir colcon && cd colcon
mkdir src && cd src
git clone https://github.com/Robotic-Developer-Road/rpg_vikit.git
git clone https://github.com/Livox-SDK/livox_ros_driver2.git 
git clone https://github.com/Robotic-Developer-Road/FAST-LIVO2.git
```

livox\_ros\_driver2需要修改一下

![Image](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MTM3MzZlNmRlNTE5Y2NlNWJiYWJlYjRkY2ViZjRmMjlfYWQyYjEzZmI3ZjY0MGZjNjQyODJjYzdjNWZlNDFkNmJfSUQ6NzYzMTk1NzQwMjI0OTk1NjI5NF8xNzgwNDg4MjU3OjE3ODA1NzQ2NTdfVjM)

4. 统一编译

```Plain Text
cd ~/colcon
# 安装可能缺少的系统依赖 
rosdep install --from-paths src --ignore-src -r -y  

# 统一构建所有包
colcon build --symlink-install --cmake-args -DROS_EDITION=ROS2 -DDISTRO_ROS=humble
```

5. 下载官方数据集

6. 将ros1数据转换为ros2

```Plain Text
pip install rosbags -i https://pypi.tuna.tsinghua.edu.cn/simple
rosbags-convert --src Retail_Street.bag --dst Retail_Street_ros2
```

```Plain Text
/data/Retail_Street_ros2
```

将metadata\.yaml文件中的 msg type 更改为如下形式：

![Image](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MzdkZGUzM2JkOGUwZTgzZjE0MWQ1MjcxZmQ4ZTU5YTFfZjJkM2QxMDY4ZTkyYThhNzBlYzAyNmMwOGMwYzBlZjNfSUQ6NzYzMTk0MjIzNTIxMDI4ODA4MV8xNzgwNDg4MjU3OjE3ODA1NzQ2NTdfVjM)

如果不行的，且一直有警告

```Plain Text
cd ~/data/Retail_Street_Fixed/
sqlite3 Retail_Street_Fixed.db3 "UPDATE topics SET type='livox_ros_driver2/msg/CustomMsg' WHERE type='livox_ros_driver/msg/CustomMsg';"
```

7. 运行演示

```Plain Text
# 打开一个终端
source ~/colcon/install/setup.bash
ros2 launch fast_livo mapping_aviz.launch.py use_rviz:=True
# 开另一个终端
source ~/colcon/install/setup.bash
ros2 bag play -s sqlite3 Retail_Street_Fixed.db3
```


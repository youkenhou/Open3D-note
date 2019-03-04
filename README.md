# Open3D-note
Note about usage of Open3D

## 基本使用
在anaconda环境下：
```bash
conda install -c open3d-admin open3d
```
Python环境下：
```bash
pip install open3d-python
```
导入Open3d包
```python
imoprt open3d as o3
```
or
```python
from open3d import *
```
## 文件读写
### 点云
```python
from open3d import *

pcd = read_point_cloud("pointcloud.pcd")  #也可以是坐标的txt

write_point_cloud("pointcloud2.pcd", pcd)
```
### Mesh
```python
mesh = read_triangle_mesh("knot.ply")

write_triangle_mesh("copy_of_knot.ply", mesh)
```
### 图片
```python
img = read_image("lena_color.jpg")
 
write_image("copy_of_lena_color.jpg", img)
```
## 基础显示点云
```python
import numpy as np
from open3d import *

pcd = read_point_cloud("pointcloud.pcd")
draw_geometries([pcd])
```

在显示界面中可以用鼠标和键盘进行交互操作：

| 操作              | 效果                           |
| ----------------- | ------------------------------ |
| 鼠标左键拖拽      | 旋转点云                       |
| Ctrl+鼠标左键拖拽 | 平移点云                       |
| 鼠标滚轮          | 缩放点云                       |
| [ / ]             | 减少/增大视野                  |
| R                 | 重置视角为默认                 |
| Ctrl + C          | 复制当前视角信息               |
| Ctrl + V          | 从剪贴板中                     |
| D                 | 保存当前视角的深度图和相机信息 |
| H                 | 在终端显示帮助信息             |

其他更多操作可以通过H查看。

## 自定义点云显示

### 给定相机参数显示相机视角的点云

主要代码参照文件```custom_draw_geometry_with_camera_trajectory.py```

- ```camera_trajectory.json```文件中定义相机的内参矩阵与外参矩阵

- ```json
  "extrinsic" : 
  	[
  		[
  			-0.916167316, 
  			-0.392541922, 
  			-0.0801185459, 
  			0.0, 
  			0.0957805233, 
  			-0.02204062401, 
  			-0.995158689, 
  			0.0, 
  			0.389017009, 
  			-0.919470264, 
  			0.0563606449, 
  			0, 
  			-156.643836, 
  			-65.1722438, 
  			3.19796239, 
  			1
  		]
  	]
  //一个相机的外参矩阵，排列顺序是列优先
  //可以定义多个外参矩阵，会产生一个视角的移动变化
  ```

- ```json
  "intrinsic" : 
  	{
  		"height" : 480,
  		"intrinsic_matrix" : 
  		[ 
  			744.375, 
  			0.0, 
  			0.0, 
  			0.0, 
  			744.375, 
  			0.0, 
  			425.5,
  			239.5, 
  			1.0 
  		],
  		"width" : 852
  	}
  //内参矩阵，列优先
  //倒数第三第二个数正常应该是0，但在这里必须要取代码中宽度和高度的一半再-0.5，不然无法显示点云
  //vis.create_window(width = 852, height = 480)
  ```

生成的rgb图像保存在```image/```中，深度图保存在```depth/```中。

## 创建Open3D格式的点云
假设有一个`N*3`的XYZ数组`points`，和一个`N*3`的RGB数组`colors`，可以用以下代码创建一个Open3D官方格式的点云pcd。
```python
pcd = open3d.PointCloud()
pcd.points = open3d.Vector3dVector(points)
pcd.colors = open3d.Vector3dVector(colors)
#可视化
open3d.draw_geometries([pcd])
```
使用过程中发现对于红绿蓝三种颜色的RGB值，`draw_geometries`可以直接显示出来，但是其他颜色无法显示都是白色，必须将颜色的RGB值除以255之后才可以正常显示。

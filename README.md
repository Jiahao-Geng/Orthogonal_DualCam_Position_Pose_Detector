# 双目视觉立方体位姿识别系统

## 目录
- [项目概述](#项目概述)
- [系统架构](#系统架构)
- [坐标系统](#坐标系统)
- [位姿计算](#位姿计算)
- [数据处理](#数据处理)
- [性能指标](#性能指标)

## 项目概述

### 项目背景
本项目开发一套基于双目视觉的物体位姿识别系统，通过识别立方体表面的 ArUco 标记实现对物体在三维空间中的位置和姿态的精确测量。

### 开发目标
1. **Phase 1**: 单特征位置识别
   - 实现单个特征物位置坐标(XYZ)识别
   - 完成基础硬件平台搭建
   - 实现图像处理基本功能

2. **Phase 2**: 多特征位姿识别
   - 实现单个个特征物完整位姿(XYZ\RPY)识别
   - 优化系统性能和精度
   - 实现可折叠机械结构设计

## 系统架构

### 硬件系统
- 2个工业相机（分辨率：1536×1024）
- 笔记本电脑
- 机械支架
- LED光源

### 软件系统
- 图像采集模块
- ArUco标记检测模块
- 位姿解算模块
- 数据融合模块
- 通信接口模块

## 坐标系统

### 1. 世界坐标系 (World Coordinate System)
采用右手坐标系：
- X轴：指向相机1（12点方向）
- Y轴：指向相机2（3点方向）
- Z轴：指向下方（桌面）

### 2. 相机坐标系 (Camera Coordinate System)
#### 相机1坐标系
- Z轴：指向观察目标（与世界-X轴同向）
- X轴：向右（与世界-Y轴同向）
- Y轴：向下（与世界Z轴同向）

#### 相机2坐标系
- Z轴：指向观察目标（与世界-Y轴同向）
- X轴：向右（与世界X轴同向）
- Y轴：向下（与世界Z轴同向）

### 3. Marker坐标系统
所有Marker采用右手坐标系

##### 当立方体处于初始状态时：
#### Marker 1（前面，面向相机1）
- Z轴：与世界+X同向
- X轴：与世界-Y轴同向
- Y轴：与世界-Z轴同向

#### Marker 3（后面）
- Z轴：与世界-X轴同向
- X轴：与世界+Y轴同向
- Y轴：与世界-Z轴同向

#### Marker 5（右面）
- Z轴：与世界-Y轴同向
- X轴：与世界-X轴同向
- Y轴：与世界-Z轴同向

#### Marker 7（左面，面向相机2）
- Z轵：与世界+Y轴同向
- X轴：与世界+X轴同向
- Y轴：与世界-Z轴同向

#### Marker 9（底面）
- Z轴：与世界+Z轴同向
- X轴：与世界-Y轴同向
- Y轴：与世界+X轴同向

## 位姿计算

### 1. 位置数据 (Position)
输出格式：(X, Y, Z)，单位：毫米(mm)
- X：相机1方向的位移
- Y：相机2方向的位移
- Z：竖直方向的位移

### 2. 姿态数据 (Orientation)
输出格式：(Roll, Pitch, Yaw)，单位：度(°)
- Roll：绕X轴旋转角度
- Pitch：绕Y轴旋转角度
- Yaw：绕Z轴旋转角度

### 3. 初始姿态定义
立方体标准位姿：
- 中心位于世界坐标系原点
- Marker 1 正对+X方向
- Marker 7 正对+Y方向
- Marker 9 正对+Z方向

## 数据处理

### 1. 标准化处理
- 角度值范围：[-180°, 180°]
- 位置精度：小数点后2位
- 角度精度：小数点后2位

### 2. 数据融合策略
#### 位置融合
- X轴数据：优先采用相机2数据
- Y轴数据：优先采用相机1数据
- Z轴数据：两相机测量值平均

#### 姿态融合
1. 角度标准化至[-180°, 180°]
2. 跨周期处理
3. 加权平均计算

### 3. 输出示例
Fused Cube Pose:
Position (mm):
X = 125.47 (get from Camera2)
Y = -45.82 (get from Camera1)
Z = 78.35 (averaged)
Rotation (deg):
Roll = 15.72
Pitch = -8.45
Yaw = 178.93

## 性能指标

### 1. 测量精度
- 位置精度：±0.1mm
- 角度精度：±0.1°

### 2. 系统性能
- 刷新率：30Hz
- 延迟：<50ms
- 工作距离：300-600mm

## 开发工具与依赖

### 1. 开发环境
- Visual Studio 2019
- OpenCV 4.5.0
- ArUco 3.1.0

### 2. 主要依赖库
- OpenCvSharp4
- MvCamCtrl.NET
- System.Numerics

## 注意事项
1. 确保相机标定准确性
2. 定期检查Marker完整性
3. 保持工作环境稳定
4. 定期进行系统校准

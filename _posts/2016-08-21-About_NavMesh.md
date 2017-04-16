---
layout: post
title: "关于NavMesh，我所知道的"
date: 2016-08-21 02:37:42 +0800
categories: Game
keywords: Recast & Detour, NavMesh, Game
---

这是一篇关于NavMesh的资料整理和总结笔记。整理了前段时间收集的NavMesh相关的资料和一个简单的算法描述。

*（文章出现的所有算法描述和资料均来源于互联网，绝无半点个人原创内容，请放心阅读。）*


## Recast & Detour
Recast & Detour [GITHUB PAGE](https://github.com/recastnavigation/recastnavigation) 是一个开源的游戏寻路引擎，被包括Unity，UE4等游戏引擎采用。

![Recast & Detour](/images/About_NavMesh/Image_RecastAndDetour.png)

<!-- more -->

Recast & Detour 包括了两部分，即 Recast 和 Detour。

Recast 通过将输入的场景模型体素化生成相应的寻路网格。其大概包括三个步骤：1. 体素化模型；2. 分割联通区域； 3. 将区域简化为多边形

Detour 通过使用 Recast 生成的 NavMesh 进行寻路，其包括了许多算法，根据不同路径平滑程度和寻路效率要求可做不同选择。

该开源库的作者 Mikko Mononen 在 AiGameDev.com 有一个相关的介绍视频 [Building and Traversing Navigation Meshes with Recast and Detour (Project Video)](http://aigamedev.com/insider/presentations/recast-teaser/)。


Mikko Mononen 在该文章 References 部分贴出了一系列相关的参考文献。

+ **Conservative Voxelization**
Long Zhang, Wei Chen, David S. Ebert, Qunsheng Peng
[PDF](/files/About_NavMesh/P1_Conservative_voxelization.pdf)


+ **Real-time Voxelization for Complex Polygonal Models**
Zhao Dong, Wei Chen, Hujun Bao, Hongxin Zhang, Qunsheng Peng
[PDF](/files/About_NavMesh/P2_Real-time_Voxelization_for_Complex_Models.pdf)


+ **Single-pass GPU Solid Voxelization and Applications**
Elmar Eisemann, Xavier Décoret
[PDF](/files/About_NavMesh/P3_Single-Pass_GPU_Solid_Voxelization_for_Real-Time_Applications.pdf)


+ **GPU Gems 2: Flow Simulation with Complex Boundaries**
Wei Li, Zhe Fan, Xiaoming Wei, Arie Kaufman
[PDF](/files/About_NavMesh/P4_GPU_Gems_2_Chapter_47_Flow_Simulation_with_Complex_Boundaries.pdf)


+ **GPU Gems 3: Real-Time Simulation and Rendering of 3D Fluids**
Keenan Crane, Ignacio Llamas, Sarah Tariq
[PDF](/files/About_NavMesh/P5_GPU_Gems_3_Real-Time_Simulation_and_Rendering_of_3D_Fluids.pdf)


+ **Way-Finder: guided tours through complex walkthrough models**
C. Andújar, P. Vázquez, M. Fairén
[PDF](/files/About_NavMesh/P6_Way-Finder_guided_tours_through_complex_walkthrough_models.pdf)


+ **Volumetric Cell-and-Portal Generation**
Denis Haumont, Olivier Debeir, François Sillion
[PDF](/files/About_NavMesh/P7_Volumetric_cell-and-portal_generation.pdf)

+ **Skeleton Extraction of 3D Objects with Visible Repulsive Force**
Fu-Che Wu, Wan-Chun Ma, Ping-Chou Liou, Rung-Huei Liang, Ming Ouhyoung
[PDF](/files/About_NavMesh/P8_Skeleton_Extraction_of 3D_Objects_with_Visible_Repulsive_Force.pdf)

+ **Automated Static and Dynamic Obstacle Avoidance in Arbitrary 3D Polygonal Worlds**
Edited by Xing-Jian Jing
[PDF](/files/About_NavMesh/P9_Automated_Static_and Dynamic_ObstacleAvoidance_in_Arbitrary_3D_Polygonal_Worlds.pdf)


+ **A Navigation Graph for Real-time Crowd Animation on Multilayered and Uneven Terrain**
Julien Pettré, Jean-Paul Laumond, Daniel Thalmann
[PDF](/files/About_NavMesh/P10_A_navigation_graph_for_real-time_crowd_animation_on_multilayered_and_uneven_terrain.pdf)


+ **Pedestrian Reactive Navigation for Crowd Simulation: a Predictive Approach**
Sébastien Paris, Julien Pettré, Stéphane Donikian
[PDF](/files/About_NavMesh/P11_Pedestrian_Reactive_Navigation_for_Crowd_Pedestrian_Reactive_Navigation_for_Crowd_Simulation_a_Predictive_Approach.pdf)

## Study: Navigation Mesh Generation
Stephen Pratt 在其项目 [《Study: Navigation Mesh Generation》](http://www.critterai.org/projects/nmgen_study/index.html) 中提供了一个 Java 版本的 Recast 实现，并在文档中对算法细节及参数进行了详细的讨论。

![Study: Navigation Mesh Generation](/images/About_NavMesh/Image_NavigationMeshGeneration.png)

其中，

+ [《Differences Between NMGen and Recast》](http://www.critterai.org/projects/nmgen_study/diffs.html)描述了其实现与 Recast & Detour 项目的异同之处；
+ [《Configuration Parameters》](http://www.critterai.org/projects/nmgen_study/config.html)罗列了算法中各参数对生成寻路网格数据的影响。


## Crowds In A Polygon Soup: Next-Gen Path Planning
Mikko Mononen 在 Recast & Detour 中生成NavMesh的基本流程受到 GDC2006 上演讲[《Crowds In A Polygon Soup: Next-Gen Path Planning》](http://gdcvault.com/play/1013192/Crowds-In-A-Polygon-Soup)的启发。

+ **Session:  Crowds In A Polygon Soup: Next-Gen Path Planning**
By David Miles, David Miles, David Miles, David Miles

	+ [Session mp3](/files/About_NavMesh/GDC-06-038.mp3)
	+ [Session ppt](/files/About_NavMesh/gdc2006_miles_david_pathplanning.ppt)


## 谷阿莫带你十分钟看完 NavMesh 生成算法
没有时间看完上面资料的同学可以看下面这个《谷阿莫带你十分钟看完 NavMesh 生成算法》的小抄。

![谷阿莫带你十分钟看完 NavMesh 生成算法](/images/About_NavMesh/Image[1].png)

[谷阿莫带你十分钟看完 NavMesh 生成算法](/2016/08/21/A_Quick_Introduction_to_NavMesh/)

# 实验目的
- 理解消息中间件和响应式编程。
# 实验任务
- 把厦门市的道路路网进行可视化展现（基于前端html页面或者java窗体）；并能展现汽车移动。
- 基于Spring 的 JMS编程模型，完成以上消息发布、消息接收等模拟器编程。

# 环境设置、工具
- java jdk （1.6或者以上）
- IDEA 或者 eclipse 等开发环境
# 具体过程
### 1. 模拟路网PaintingManagerGraphServiceImplicit实现类。
#####a. 先进行简单的读文件、修改id、写文件三步

![读](https://i.loli.net/2019/06/22/5d0e4686bb61a90284.png)
![写](https://i.loli.net/2019/06/22/5d0e4696d51c228889.png
)

#####b. 输出模拟的地图的经纬度范围信息：

![地图](https://i.loli.net/2019/06/22/5d0e479164f2e15868.png
)

#####c. 创建节点类Vertex和边类Edge

![Vertex类](https://i.loli.net/2019/06/22/5d0e489c1aa9b79224.png
)
![Edge类](https://i.loli.net/2019/06/22/5d0e489aaabd054154.png)

#####d. PaintingManager类

在PaintingManager类中，首先读取地图数据

![地图](https://i.loli.net/2019/06/22/5d0e49957b6bd95443.png
)

初始化一定数量的车辆

![初始化](https://i.loli.net/2019/06/22/5d0e4a03efcad99745.png
)

#####e. OrderThread类

![OrderThread类](https://i.loli.net/2019/06/22/5d0e4a737d86038161.png
)
![OrderThread类](https://i.loli.net/2019/06/22/5d0e4b50b4ea591496.png
)

#####f. PaintingThread类

![PaintingThread类](https://i.loli.net/2019/06/22/5d0e4c16adb1446073.png
)

路网绘制得到的结果如下，是一个厦门岛的地图：

![地图](https://i.loli.net/2019/06/22/5d0e4cf56e06d25349.png
)

随机生成500个车辆，根据订单生成概率而出现不同的状态：

![随机生成](https://i.loli.net/2019/06/22/5d0e4d29e4deb54082.png)

随机取两个点，使用Dijkstra算法得到的最短路径，为了验证结果的正确性，将得到的路线用红色标注出来

![结果](https://i.loli.net/2019/06/22/5d0e4dc9e1f3775611.png
)

### 2. 模拟车辆通信

#####a. CarManager类

用于管理车辆模拟的数据，包含所有车辆信息、车辆数以及启动的main函数。

![CarManager类](https://i.loli.net/2019/06/22/5d0e4e6f5f6e240005.png
)
![CarManager类](https://i.loli.net/2019/06/22/5d0e4e6f5d79e10346.png
)

#####b. Car类

包含车辆id、name、状态status、经纬度信息、消息发送的目标Topic、用于发送消息的producer、用于接收不同车辆（不同Topic）消息的多个consumer（设为一个哈希表consumerMap），以及统计收发消息数量的三个变量

![Car类](https://i.loli.net/2019/06/22/5d0e4f0de55eb24550.png
)
![Car类](https://i.loli.net/2019/06/22/5d0e4f0e52f3646843.png
)
![Car类](https://i.loli.net/2019/06/22/5d0e4f0cc159817326.png
)
![Car类](https://i.loli.net/2019/06/22/5d0e4f0cd309748530.png
)
![Car类](https://i.loli.net/2019/06/22/5d0e4f0ce465d12717.png
)

#####c. 车辆发布消息线程类CarPubThread

![CarPubThread](https://i.loli.net/2019/06/22/5d0e4ffb5d86161055.png
)

#####d. 管理车辆间订阅关系的线程类CarSubThread

![CarSubThread](https://i.loli.net/2019/06/22/5d0e502ce3d8e13378.png
)

calDistance方法使用经纬度计算两辆车之间的距离

![距离](https://i.loli.net/2019/06/22/5d0e505c755c372515.png
)

#####e. PerformanceThread线程类

需要有一个表现消息收发效率的指标，这里使用消息发布数量，根据订阅关系，计算预计收到消息数和实际收到消息数，计算差值，即剩余待接收消息数，用这个指标作为判断收发效率的标准，若剩余待接收消息数越多，则越多订阅者为收到消息，即效率越低

![PerformanceThread](https://i.loli.net/2019/06/23/5d0e50ae5ee5548347.png
)

#####f. 运行。

可以看到车辆根据相对距离互相订阅和取消订阅，同时生成了地图窗口，展示了车辆实时位置

![结果](https://i.loli.net/2019/06/23/5d0e519291a1f68044.png
)
![结果](https://i.loli.net/2019/06/23/5d0e5190cf7c329557.png)

关闭地图窗口，车辆消息收发的模拟还在进行中。可以看到车辆在一定距离内可自动互相订阅，并随机发送消息
![结果](https://i.loli.net/2019/06/23/5d0e5234eef0244958.png
)

每个订阅了发布者的接受者，都能接收到发布者发布的消息：

![结果](https://i.loli.net/2019/06/23/5d0e5235095e941220.png
)

当距离超过一定限度时，车辆间自动取消订阅：

![结果](https://i.loli.net/2019/06/23/5d0e52681749a28446.png
)

同时PerformanceThread实时输出当前时间戳的剩余待接收消息数：

![结果](https://i.loli.net/2019/06/23/5d0e52686552673842.png)


# 3D的可抓取性分析（Bin Picking）

因为其难度高，潜在应用场景多，Bin Picking长期以来有机器人操作中的“圣杯问题”之称。直到近几年来，伴随着计算机视觉和运动规划等学术界方面的进展，部分Bin Picking问题才开始出现稳定可靠的解决方案和商业化的可能，于是吸引了大批研究者，大公司和创业公司的注意。
目前绝大多数Bin Picking核心只是一件事：找到合适的抓取点（或者吸盘的吸取点）。之后具体怎么执行只是影响速度，属于运动规划问题了。
找抓取点主流的方法有三大类：Model-based，Half-model-based，以及Model-free的方法。

## Model-based
此类方法要求事先知道所有物体的模型，最好是直接能拿到实物，进行拍照/扫描。方法大概分为以下三步：
1. 离线计算：根据Gripper类型，对每一个物体模型计算局部抓取点。
2. 在线感知：通过RGB或者点云，计算出每一个物体的类型和三位位姿
3. 计算抓取点：把每个物体的抓取点投影到世界坐标系下，然后根据其他要求（碰撞检测等）选一个最优的抓取点。
近年来SSD-6D之类的牛逼方法开始出现，让clutter内多个物体6D位姿识别不再是痴人说梦，完全基于模型的方法在环境相对可控的场合确实变成了最稳妥的方案。

## Half-model-based
此类方法并不需要预先知道抓取的物体，但是需要大量类似的物体来训练图像分割的算法，“物体”的概念在这类方法中仍然很关键。
方法流程:
1. 离线训练图像分割算法，就是把图片里的像素按物体分类。早年此类方法一般得手标图像样本，现在大家都开始多多少少用起了仿真+渲染，效率大增。
2. 在线跑图像分割，在每个物体所属的一块像素里找normal分布合适的点作为抓取（吸取）点。

## Model-free
此类方法并不涉及到“物体”的概念，直接从RGB或点云计算出合适的抓取点。这里仍有区分，一类是基于Geometry的方法，代表作是Andreas Ten Pas的AGILE grasp，通过一些几何约束在点云上高效sample潜在优质抓取点，然后通过supervised learning训练得到的分类器进一步分类。


----------------------
# 方案上的解决办法
1. 用2d分类模型+规则：2d分类模型包括分割/检测等等。

2. 用不同3d模板匹配
3d的得分：完整性overlap；ppf的score；icp的fitness（icp的fitness和完整性overlap是等价的。）

3. 2d-3d
当得到当前物料的pose6d后，先使用pose6d对模板点云进行transform，然后将该3D模型映射到2D图像上，最后在2D图像层面，对比模板点云的面积与当前实际点云的面积。
相当于在2D图像层面，对比无遮挡的groundtruth和当前可视区域。


正常人类使用的箱子对机器人其实是很不友好的，靠近拐角的物体不存在抓取点，靠近边沿的物体也让gripper的运动很受限制。解决方法是使用四周是倾斜的圆盘


## 实例
XYZ星猿哲的demo
https://v.qq.com/x/page/c1345771qvw.html
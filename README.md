# 项目：劣迹艺人擦除
小组成分工：

 - 廖柏皓 PB18061341：调研，抄代码并修改。

 - 黄越  PB18061366：调研。
 
# 问题描述

当前网络娱乐高度发达，获取信息途径繁多，追星现象在青少年甚至成年人非常普遍，偶像的标杆作用非常重要，因此社会加大了对于明星的言论以及道德管控，本着不影响产业的发展，国家对于劣迹艺人所参加的作品并没有完全封锁，而是要求去掉劣迹艺人的身影，传统方法通常需要对于一个个视频帧分别处理，效率低下，如何找到一个稳定、高效的擦除劣迹艺人方法迫在眉睫。	
 
# 基本原理
 神经网络的方法在当前学术以及工业界引起了新的潮流，千奇百怪的网络结构解决了之前计算机视觉一直解决不好的问题，如语义分割、目标识别。成熟的框架使得对于初学者上手非常简单，基于以上理由，我们小组决定使用深度学习的方法，经过调研，我们首先定位了基本需要解决的问题:1.目标追踪与分割。2.目标擦除。这两个问题在github上都有一些经典的深度学习算法，因此我们选取了SiamMask和FGVC这两个知名的算法。
 
# 基本网络架构 

 - 1.SiamMask--目标追踪与分割
 
<div align="center">
  <img src="http://www.robots.ox.ac.uk/~qwang/SiamMask/img/SiamMask.jpg" width="600px" />
</div>
 使用resnet提取模板和图像的特征，使用channnel wise相关运算，得到特征空间的匹配结果，通过多加一路分支在原有物体跟踪上，实现目标分割。
 
  - 2.FGVC

![img](https://github.com/USTC-Computer-Vision-2021/LBH_HY_video-completion/blob/main/example/fgvc_arch.png)

通过边界信息补全光流，使用补全的光流，使用，近邻、非近邻、前向和反向的梯度信息，通过泊松重建得到补全的帧。

##  项目说明
擦除总共分为两个步骤：

物体追踪和实例分割：[Siammask](https://github.com/foolwood/SiamMask)

视频修复：[FGVC](https://github.com/lbh666/FGVC)

由于小组能力受限，项目是在上述现有项目稍加整合。

# 环境配置

打开上面两个项目，分别按照原作者要求配置siammask和FGVC两个conda环境，并分别下载训练好的模型。

一、配置siammask环境：
- 1.创建conda环境
```shell
export SiamMask=$PWD/SiamMask
export fgvc=$PWD/FGVC
export PYTHONPATH=$PWD:$PYTHONPATH

cd $SiamMask
conda create -n siammask python=3.6
source activate siammask
pip install -r requirements.txt
bash make.sh
```
- 2.模型下载
```
cd $SiamMask/experiments/siammask_sharp
wget http://www.robots.ox.ac.uk/~qwang/SiamMask_DAVIS.pth
```

二、配置FGVC环境：
- 1.创建conda环境：
```
conda create -n FGVC
conda activate FGVC
conda install pytorch=1.6.0 torchvision=0.7.0 cudatoolkit=10.1 matplotlib scipy opencv -c pytorch
pip install imageio imageio-ffmpeg scikit-image imutils
```
- 2.模型下载
```
cd $fgvc
chmod +x download_data_weights.sh
./download_data_weights.sh
```

# 运行说明
一、得到追踪的mask
```
cd $SiamMask/experiments/siammask_sharp
export PYTHONPATH=$PWD:$PYTHONPATH
conda activate siammask
python ../../tools/demo.py --base_path [src] --resume SiamMask_DAVIS.pth --config config_davis.json
```
  得到的mask会保留在SiamMask/data/\[src\]_mask文件夹中

二、目标擦除
```
cp -r $SiamMask/data $fgvc/data
conda activaye FGVC
cd $fgvc/tool
python video_completion.py \
       --mode object_removal \
       --path ../data/[src] \
       --path_mask ../data/[src_mask] \
       --outroot ../result/[arc_removal] \
       --seamless
```
# 示例
官方例子tennis

![img](https://github.com/USTC-Computer-Vision-2021/LBH_HY_video-completion/blob/main/example/tennis.gif)

王力宏

![img](https://github.com/USTC-Computer-Vision-2021/LBH_HY_video-completion/blob/main/example/leehom.gif)

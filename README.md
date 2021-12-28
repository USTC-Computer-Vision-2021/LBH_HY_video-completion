# 项目：劣迹艺人擦除
廖柏皓 PB18061341

 黄越  PB18061366
 
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
<center class="half">
     <img src="https://github.com/USTC-Computer-Vision-2021/LBH_HY_video-completion/blob/main/example/tennis_fusion.gif" width="400"/><img src="https://github.com/USTC-Computer-Vision-2021/LBH_HY_video-completion/blob/main/example/tennis_rm.gif" width="400"/></center>
王力宏
<center class="half">
    <img src="https://github.com/USTC-Computer-Vision-2021/LBH_HY_video-completion/blob/main/example/leehom_fusion.gif" width="400"/><img src="https://github.com/USTC-Computer-Vision-2021/LBH_HY_video-completion/blob/main/example/leehom_rm.gif" width="400"/> </center>

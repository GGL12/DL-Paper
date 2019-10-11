# Reading Object Detection Paper

## 一、R-CNN

### 1.1、Algorithm Steps 

①  输入图像

②  每张图像生成1K~2K个候选区域

③  对每个候选区域，使用深度网络提取特征（AlextNet、VGG等CNN都可以）

④  将特征送入每一类的SVM 分类器，判别是否属于该类

⑤  使用回归器精细修正候选框位置

### 1.2、Selective Search

（1）使用一种过分割手段，将图像分割成小区域  

（2）查看现有小区域，合并可能性最高的两个区域，重复直到整张图像合并成一个区域位      置。优先合并以下区域：       - 颜色（颜色直方图）相近的       - 纹理（梯度直方图）相近的       - 合并后总面积小的       - 合并后，总面积在其BBOX中所占比例大的  在合并时须保证合并操作的尺度较为均匀，避免一个大区域陆续“吃掉”其它小区域，保证合并后形状规则。  

（3）输出所有曾经存在过的区域，即所谓候选区域 

### 1.3、Feature Map

使用深度网络提取特征之前，首先把候选区域归一化成同一尺寸227×227，使用CNN模型进行训练提取网络特征。（AlexNex网络模型之类的结构）

### 1.4、Predict Classes

对每一类目标，使用一个线性SVM二类分类器进行判别。输入为深度网络（AlexNet）输出的4096维特征，输出是否属于此类 。

### 1.5、Adjust  Position 

目标检测的衡量标准是重叠面积：许多看似准确的检测结果，往往因为候选框不够准确，重叠面积很小，故需要一个位置精修步骤，对于每一个类，训练一个线性回归模型去判定这个框是否框得完美。 

## 二、Fast R-CNN

### 2.1、Network Model 

![fast-rcnn-network-model](https://github.com/GGL12/DL-Paper/blob/master/images/fast-rcnn-network-model.png) 

### 2.2、Feature Map

问题：R-CNN RP图像多次卷积耗费时间且参数不共享。解决方案：通过Selective Search算法获得原始图像的region proposal坐标值，映射到一次卷积后的feature map上

### 2.3、ROI Pooling Layer 

映射后的feature map RP具有不同大小的size，根据W,H是不同size的RP输出相同size的W*H个feature map。具体做法在原feature map size除以W,H 近似（approximate size）划分W*H个子集，然后使用Max Pooling，最终得到W*H的Feature Map。论文使用VGG16将最后一层max pooling改成能够统一size的ROI pooling layer。

### 2.4、Multi-task loss 

多损失融合（分类损失和回归损失融合），分类采用log loss（即对真实分类的概率取负log，分类输出K+1维），回归的loss为smooth l1函数。

## 三、Faster R-CNN

### 3.1、no Selective Search ?

计算区域候选成为实时的检测的瓶颈，Faster R-CNN摈弃传统的Selective Search算法获得区域建议框，由此提出了RPN。

### 3.2、Region Proposal Networks 

区域建议网络（RPN）将一个图像（任意大小）作为输入，输出矩形目标建议框的集合，每个框有一个objectness得分 

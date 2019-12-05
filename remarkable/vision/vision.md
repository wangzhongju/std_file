#ROI Pooling
- ***ROI***
~~~
全称Region of Interest，特征图上的框;
1) 在Fast RCNN中，ROI是指Selective Search完成后得到的“候选框”在特征图上的映射
2) 在Faster RCNN中，候选框是经过RPN产生的，然后再把各个“候选框”映射到特征图上，得到ROIS
~~~
- ***ROI Pooling的输入***
输入由两部分组成：
1) 特征图：在Fast RCNN中，它位于ROI Pooling之前，在Faster RCNN中，它是与RPN共享那个特征图，通常我们称之为“share_conv”;
2) rois:在Fast RCNN中，指的是Selective Search的输出;在Faster RCNN中指的是RPN的输出，一堆矩形候选框，形状为1×5×1×1(4个坐标+索引index)，其中值得注意的是，坐标的参考系不是针对feature map这张图的，而是针对原图的。
- ***ROI Pooling的输出***
输出是batch个vector，其中batch的值等于ROI的个数，vector的大小为channel×w×h;ROI Pooling的过程就是一个个大小不同的box矩形框，都映射成大小固定(w×h)的矩形框;


#RPN
***Region Proposal Network***
本质是基于滑窗的无类别object检测器

- **RPN所在位置**
![loc](./Pictures/RPN/rpnloc.jpg "loc")

- **tip:**
1) 只有在train时，cls+reg才能得到强监督信息(来源于ground truth)。即ground truth会告诉cls+reg结构，哪些才是真的前景，从而引导cls+reg结构学得正确区分前后景的能力；在reference阶段，就要靠cls+reg自力更生了。
2) 在train阶段，会输出约2000个proposal，但只会抽取其中256个proposal来训练RPN的cls+reg结构；到了reference阶段，则直接输出最高score的300个proposal。此时由于没有了监督信息，所有RPN**并不知道这些proposal是否为前景**，整个过程只是惯性地推送一波无tag的proposal给后面的Fast R-CNN。
3) RPN的运用使得region proposal的额外开销就只有一个两层网络。
- **RPN的组成**
![com](./Pictures/RPN/rpncom.png "com")
1) **RPN头部**，通过以下结构生成anchor(其实就是一堆有编号有坐标的bbox)
![top](./Pictures/RPN/rpntop.jpg "top")
2) **RPN中部**，分类分支(cls)和边框回归分支(bbox reg)分别对这堆anchor进行计算
![mid](./Pictures/RPN/rpnmid.jpg "mid")
3) **RPN末端**
通过对 两个分支的结果进行汇总，来实现对anchor的 初步筛除（先剔除越界的anchor，再根据cls结果通过NMS算法去重）和 初步偏移（根据bbox reg结果），此时输出的都改头换面叫 Proposal 了
**add:**RPN之后，proposal成为ROI，被输入ROIPooling或ROIAlign中进行size上的归一化，**不属于RPN的范围**
**tip:**但是如果只在最后一层上feature map上映射回原图像，且初始产生的anchor被限定了尺寸下限，那么低于最小anchor尺寸的小目标虽然被anchor圈入，在后面的过程中依然容易被漏检。**FPN**的出现大大降低了小目标的漏检率，使得RPN如虎添翼。

- anchor机制
rpn网络的核心，anchor给出一个基准窗大小，按照倍数和长宽得到不同大小的窗，论文中基准窗的大小为16,给了(8,16,32)三种倍数和(0.5,1,2)三种比例，这样能够得到一共9种尺度的anchor，在对60×40的map进行滑窗时(步长为1)，以中心像素为基点构造9种anchor映射到原来的1000×600图像中，映射比例为16倍。那么总共可以得到60×40×9大约2万个anchor。
- 训练
 RPN网络训练，那么就涉及ground truth和loss function的问题。对于左支路，ground truth为anchor是否为目标，用0/1表示。那么怎么判定一个anchor内是否有目标呢？论文中采用了这样的规则：1）假如某anchor与任一目标区域的IoU最大，则该anchor判定为有目标；2）假如某anchor与任一目标区域的IoU>0.7，则判定为有目标；3）假如某anchor与任一目标区域的IoU<0.3，则判定为背景。所谓IoU，就是预测box和真实box的覆盖率，其值等于两个box的交集除以两个box的并集。其它的anchor不参与训练。
 - 联合训练
 四步训练法：
1) 单独训练RPN网络，网络参数由预训练模型载入
2) 单独训练Fast-RCNN网络，将第一步RPN的输出候选区域作为检测网络的输入。具体而言，RPN输出一个候选框，通过候选框截取原图像，并将截取后的图像通过几次conv-pool，然后再通过roi-pooling和fc再输出两条支路，一条是目标分类softmax，另一条是bbox回归。截止到现在，两个网络并没有共享参数，只是分开训练了;
3) 再次训练RPN，此时固定网络公共部分的参数，只更新RPN独有部分的参数；
4) 拿RPN的结果再次微调Fast-RCNN网络，固定网络公共部分的参数，只更新Fast-RCNN独有部分的参数。
![RPN](./Pictures/RPN/RPN.jpg "RPN")


# BBR
***Bounding Box Regression***
边框回归
![BBR](./Pictures/BBR/BBR.jpg  "BBR")
对于上图，绿色的框代表Ground Truth，红色的框为Selective Search提取的Region Proposal。即便红色的框被分类器识别为飞机，但是由于红色的框定位不准(IOU<0.5)，那么这张图片相当于没有正确的检测出飞机，需要对框进行微调。
![BBR](./Pictures/BBR/bbr.jpg  "BBR")
窗口使用四维向量(x,y,w,h)来表示，目标是寻找一种关系使得输入原始的窗口P经过映射得到一个跟真实窗口G更接近的回归窗口G^。边框回归的目的既是：给定(Px,Py,Pw,Ph)寻找一种映射f，使得
$$f(Px,Py,Pw,Ph) = (Ĝx,Ĝy,Ĝw, Ĝh)$$
并且$$(Ĝx,Ĝy,Ĝw, Ĝh) ≈ (Gx,Gy,Gw, Gh)$$

- ***步骤***
平移+尺度放缩
1) 先做平移(Δx,Δy)， Δx=Pwdx(P), Δy=Phdy(P) 这是R-CNN论文的：
$$Ĝx=Pwdx(P)+Px,(1) $$
$$Ĝ y=Phdy(P)+Py,(2) $$
2) 然后再做尺度缩放(Sw,Sh), Sw=exp(dw(P)), Sh=exp(dh(P)), 对应论文中：
$$Ĝ w=Pwexp(dw(P)),(3) $$
$$Ĝ h=Phexp(dh(P)),(4) $$
边框回归学习的就是dx(P),dy(P),dw(P),dh(P)这四个变换
- Input
RegionProposal→P=(Px,Py,Pw,Ph)，这个是什么？ 输入就是这四个数值吗？其实真正的输入是这个窗口对应的 CNN 特征，也就是 R-CNN 中的 Pool5 feature（特征向量）。 (注：训练阶段输入还包括 Ground Truth， 也就是下边提到的t∗=(tx,ty,tw,th)t∗=(tx,ty,tw,th))
- output
需要进行的平移变换和尺度缩放 dx(P),dy(P),dw(P),dh(P)， 或者说是Δx,Δy,Sw,Sh。 我们的最终输出不应该是 Ground Truth 吗？ 是的， 但是有了这四个变换我们就可以直接得到 Ground Truth， 这里还有个问题， 根据(1)*~*(4)我们可以知道， P 经过 dx(P),dy(P),dw(P),dh(P)得到的并不是真实值 G， 而是预测值Ĝ。 的确， 这四个值应该是经过 Ground Truth 和 Proposal 计算得到的真正需要的平移量(tx,ty)和尺度缩放(tw,th)。 
这也就是 R-CNN 中的(6)~(9)： 
$$ tx=(Gx−Px)/Pw,(6) $$
$$ ty=(Gy−Py)/Ph,(7) $$
$$ tw=log(Gw/Pw),(8) $$
$$ th=log(Gh/Ph),(9) $$
那么目标函数可以表示为 d∗(P)=wT∗Φ5(P)，Φ5(P)是输入 Proposal 的特征向量，w∗是要学习的参数（*表示 x,y,w,h， 也就是每一个变换对应一个目标函数） , d∗(P)是得到的预测值。 我们要让预测值跟真实值t∗=(tx,ty,tw,th)差距最小。


# ResNet
***深度残差网络***
![res](./Pictures/ResNet/resnet.png  "res")
![res](./Pictures/ResNet/res.png  "res")
identity mapping "弯弯的曲线"	residual mapping “除曲线那部分”。

- 两种shortcut Connection方式;
- 5种深度的ResNet，分别为18,34,50,101,152
~~~
所有的网络都分成5部分，分别为:conv1,conv2_x,conv3_x,conv4_x,conv5_x
以101-layer为例，首先输出7×7×64的卷积，然后经过3+4+23+3 = 33 个building block，每个block为3层，所以33×3 = 99 层，最后有个fc层(用于分类)，所以1+99+1 = 101， 
tip：101仅指卷积或全连接层，而激活层或者Pooling层并没有计算在内。
50-layer和101-layer不同在于conv4_x，ResNet50有6个block，而ResNet101有23个block，差了17个block，也就是17×3 = 51 层。
~~~

- 基于ResNet101的Faster RCNN
![faster](./Pictures/ResNet/faster.png "faster")
蓝色的部分为ResNet101，可以发现con4_x的最后的输出为RPN和ROI Pooling的共享部分，而conv5_x(共9层网络)都作用于ROI Pooling之后的一堆特征图(14×14×1024)，特征图的大小维度也刚好符合原本的ResNet101中conv5_x的输入;一定要记得最后接一个average pooling，得到2048维特征，分别用于分类和框回归。


# FPN
** Feature Pyramid Networks **
>低层的特征语义信息比较少，但是目标位置准确；高层的特征语义信息比较丰富，但是目标位置比较粗略。另外虽然也有些算法采用多尺度特征融合的方式，但是一般是采用融合后的特征做预测，而本文不一样的地方在于预测是在不同特征层独立进行的。

- **四种利用特征的形式**
![out](./Pictures/FPN/FPNout.jpg "out")
~~~
(a) 图像金字塔，即将图像做成不同的scale，然后不同scale的图像生成对应的不同scale的特征。
(b) 像SPP net，Fast RCNN，Faster RCNN是采用这种方式，即仅采用网络最后一层的特征。
(c) 像SSD（Single Shot Detector）采用这种多尺度特征融合的方式，没有上采样过程，即从网络不同层抽取不同尺度的特征做预测，这种方式不会增加额外的计算量。作者认为SSD算法中没有用到足够低层的特征（在SSD中，最低层的特征是VGG网络的conv4_3），而在作者看来足够低层的特征对于检测小物体是很有帮助的。
(d) 本文作者是采用这种方式，顶层特征通过上采样和低层特征做融合，而且每层都是独立预测的。
~~~
![FPN](./Pictures/FPN/FPN.jpg "FPN")
~~~
上面一个带有skip connection的网络结构在预测的时候是在finest level（自顶向下的最后一层）进行的，简单讲就是经过多次上采样并融合特征到最后一步，拿最后一步生成的特征做预测。而下面一个网络结构和上面的类似，区别在于预测是在每一层中独立进行的。
~~~
![fpn](./Pictures/FPN/fpn.jpg "fpn")
~~~
作者的主网络采用ResNet，算法大致结构如上图，**一个自低向上的线路，一个自顶向下的线路，横向连接(lateral connection)**，图中放大的区域就是横向连接，这里的1×1卷积核的主要作用是减少卷积核的个数，也就是减少feature map的个数，并不改变feature map的尺寸大小。
~~~
**自底向上**其实就是网络的前向过程。

# SppNet
**Spatial Pyramid Pooling** 空间金字塔池化
全连接层需要指定输入层和输出层神经元个数，所以需要规定输入的feature的大小，导致神经网络需要输入经过crop、warp操作的固定尺寸的图片。
ORI: images --> crop/warp --> conv layers --> fc layers --> output
SPP: images --> conv layers --> Spatial Pyramid Pooling --> fc layers --> output
- **整体过程**
![spp](./Pictures/SppNet/SPP.jpg "spp")
~~~
黑色图片代表卷积之后的特征图，接着我们以不同大小的块来提取特征，分别是4×4,2×2,1×1，放到特征图上就可以得到16+4+1=21种不同大小的块(Spatial bins)，从这21个块中，每个块提取一个特征，这样刚好就是我们要提取的21维特征向量，这种以不同的大小格子的组合方式来池化的过程就是空间金字塔池化(SPP)；
输出向量大小为MK，M=#bins，K=#filters，作为全连接层的输入，例如上图，conv5计算出的feature map是任意大小的，经过SPP后，就可以变成固定大小的输出了，上图为(16+4+1)×256的特征传入fc。
~~~

# RetinaNet
detector主要分为以下两大门派：
one stage--> YOLOV1--3,SSD     精度低但速度快
two stage--> R-CNN,SPPNet,Fast/Faster R-CNN    精度高但速度慢
~~~
1) one stage受制于‘类别不平衡’(检测算法早期会生成一大波bbox，绝大多数属于负样本background，即使分类器无脑地把所有的bbox同一归类为background，accuracy也可以刷得很高，于是分类器的训练就失败了)，交叉熵损失(CE)无法抗衡“类别极不平衡”。
2) two stage有RPN，第一阶段的RPN会对anchor进行简单的二分类(只是简单的区分是前景还是背景，并不区别究竟属于那个类)；第二阶段分类器登场，在初筛过后的bbox上进行难度小得多的第二波分类(细分类)
~~~
- focal loss
![FL](./Pictures/RetinaNet/focalloss.jpg "FL")
在原本的交叉熵损失函数前乘上一个权重，实验中发现γ=2，α=0.25的取值组合效果最好。
- RetinaNet:
![RN](./Pictures/RetinaNet/retinanet.jpg "RN")


# FasterRCNN中的SmoothL1Loss
- **Fast RCNN损失函数，多任务损失**
![mt](./Pictures/Loss/multi-task.jpg "mt")
fast rcnn网络有两个同级输出层(cls score和bbox_predict层)，都是全连接层，称为multi-task
1) clsscore层，用于分类，输出k+1维数组p，表示属于k类和背景的概率。对每个ROI输出离散型概率分布：
![p](./Pictures/Loss/p.png "p")，通常，p由k+1类的全连接层利用softmax计算得出。
2) bbox_predict层，用于调整候选区域位置，输出bounding box回归的偏移，输出4*k维数组t，表示分别属于k类时，应该平移缩放的参数：
![t](./Pictures/Loss/t.png "t")
3) loss_cls层评估分类损失函数，loss_bbox评估检测框定位的损失函数，比较真实分类对应的预测平移缩放参数和真实平移缩放参数的参别：
![smoothl1](./Pictures/Loss/smoothl1.jpg "smoothl1")
使得loss对于离群点更加鲁棒。
最后总损失为两者加权和，如果分类为背景则不考虑定位损失。
![loss](./Pictures/Loss/fastLoss.jpg "loss")
艾弗森括号指数函数[u≥1]表示背景候选区域即负样本不参与回归损失，不需要对候选区域进行回归操作。λ控制分类损失和回归损失的平衡。Fast R-CNN论文中，所有实验λ=1。
- **Faster RCNN损失函数**
![loss](./Pictures/Loss/fasterLoss.jpg "loss")
其中：
![1](./Pictures/Loss/1.png "1")
![2](./Pictures/Loss/2.png "2")
![3](./Pictures/Loss/3.png "3")


# GIOU
- ***IOU作为损失存在的问题***
1) 如果两个对象不重叠，IOU值将为零，不论相差多远，即不会反映两个形状彼此之间的距离，如果用IOU用作损耗，则其梯度将为零，无法优化。
2) IOU的值无法反应两个对象之间如何重叠，同一个IOU值两个对象间有多种重叠方式。
- ***GIOU计算***
![cal_GIOU](./Pictures/GIOU/cal_GIOU.jpg "cal_GIOU")
- ***IOU与GIOU对比***
![GIOU](./Pictures/GIOU/IOU_L12.jpg "GIOU")
- ***IOU与GIOU loss计算过程***
![GIOU](./Pictures/GIOU/GIOU.jpg "GIOU")


# RefineDet
Single-Shot Refinement Neural Network for Object Detection(CVPR2018)




# SSD
single shot multibox detector

- **本文提出的SSD算法是一种直接预测bounding box的坐标和类别的object detection算法，没有生成proposal的过程**
- 基本的网络结构是基于VGG16，在ImageNet数据集上预训练完以后用两个新的卷积层代替fc6和fc7，另外对pool5也做了一点小改动，还增加了4个卷积层构成本文的网络。
1) 文章的一个核心是作者同时采用lower和upper的feature maps做检测
2) default box，是指在feature map的每个小格(cell)上都有一系列固定大小的box，如下图有4个(虚线框)，假设每个feature cell有k个default box，那么对与每个default box都需要预测c个类别score和offset，那么如果一个feature map的大小是m×n，也就是有m×n个feature map cell，那么这个feature map就一共有k×m×n个default box，每个default box都需要预测4个坐标相关的值和c+1个类别概率(**实际code中分别用不同数量的3×3卷积核对该层feature map进行卷积，不如卷积核数量为(c+1)×k对应confidence输出，表示每个default box的confidence，就是类别；卷积核数量为4×k对应localization输出，表示每个default box的坐标**)，default box与Faster RCNN中anchor很像，Faster RCNN只用在最后一个卷积层，但SSD中用在多个不同层的feature map上。**下图还有个重要信息：训练阶段，算法在一开始会先将这些default box和ground truth box进行匹配，比如蓝色的两个虚线框和猫的ground truth box匹配上了，即一个ground truth可能对应多个default box;在预测阶段，直接预测每个default box的偏移以及每个类别相应的得分，最后通过NMS得到最终的结果**
![box](./Pictures/SSD/defaultbox.jpg "box")
- **default box的scale(大小)和aspect ratio(纵横比)**
1) scale计算公式：
![scale](./Pictures/SSD/scale.jpg "scale")
这里的smin为0.2，表示最底层的scale大小;smax为0.9，表示最高层的scale大小
2) aspect ratio计算：
![ar](./Pictures/SSD/aspect_ratio.jpg "ar")
因此，对于每个feature map cell而言，一共有6中default box，可以看出这种default box在不同的feature层有不同的scale，在同一个feature层又有不同的aspect ratio，基本上可以覆盖输出图像中各种形状和大小的object！有个这种方式产生的负样本比正样本多得多，作者将负样本按照confidence loss进行排序，然后选择排名靠前的一些负样本作为训练，使得正负样本比例在1：3左右。
3) SSD与YOLO算法结构图对比
![net](./Pictures/SSD/SSD_YOLO.jpg "net")
YOLO算法的输入是448x448x3，输出是7x7x30，这7x7个grid cell一共预测98个bounding box。SSD算法是在原来VGG16的后面添加了几个卷积层来预测offset和confidence（相比之下YOLO算法是采用全连接层(yolo_v1)），算法的输入是300x300x3，采用conv4_3，conv7，conv8_2，conv9_2，conv10_2和conv11_2的输出来预测location和confidence。
~~~
tip: 详细讲一下SSD的结构，可以参看Caffe代码。SSD的结构为conv1_1，conv1_2，conv2_1，conv2_2，conv3_1，conv3_2，conv3_3，conv4_1，conv4_2，conv4_3，conv5_1，conv5_2，conv5_3（512），fc6：3*3*1024的卷积（原来VGG16中的fc6是全连接层，这里变成卷积层，下面的fc7层同理），fc7:1*1*1024的卷积，conv6_1，conv6_2（对应上图的conv8_2），……，conv9_1，conv9_2，loss。然后针对conv4_3（4），fc7（6），conv6_2（6），conv7_2（6），conv8_2（4），conv9_2（4）的每一个再分别采用两个3*3大小的卷积核进行卷积，这两个卷积核是并列的（括号里的数字代表default box的数量，可以参考Caffe代码，所以上图中SSD结构的倒数第二列的数字8732表示的是所有default box的数量，是这么来的38*38*4+19*19*6+10*10*6+5*5*6+3*3*4+1*1*4=8732），这两个3*3的卷积核一个是用来做localization的（回归用，如果default box是6个，那么就有6*4=24个这样的卷积核，卷积后map的大小和卷积前一样，因为pad=1，下同），另一个是用来做confidence的（分类用，如果default box是6个，VOC的object类别有20个，那么就有6*（20+1）=126个这样的卷积核）。如下图是conv6_2的localizaiton的3*3卷积核操作，卷积核个数是24（6*4=24，由于pad=1，所以卷积结果的map大小不变，下同）：这里的permute层就是交换的作用，比如你卷积后的维度是32*24*19*19，那么经过交换层后就变成32*19*19*24，顺序变了而已。而flatten层的作用就是将32*19*19*24变成32*8664，32是batchsize的大小。
~~~









# LT loss总结

## Focal loss

*参考论文：Focal Loss for Dense Object Detection CVPR 2017*

**文章主要是探讨了一阶段的训练能否赶上二阶段训练的效果，甚至更好；文章发现最主要的阻碍一阶段detector的是class imbalance问题，并设计了一种基于交叉熵的新loss——Focal loss**

### 设计原因

类不平衡问题在类似R-CNN的检测器中通过两级级联和采样启发式方法来解决：

1. proposal阶段能迅速缩减candidata object locations的数量（1-2k）
2. classification阶段，用启发式采样，例如使用一个固定的foreground-to-background比例（1:3），或者其他方式

但是在一阶段训练中，detector需要处理大量的candidata object locations，数量级在~100k，覆盖了空间位置、尺寸、长宽比；同样的启发式采样在这里是没有用的，因为数量实在是太多，训练过程仍在是被头部类所主导的。

于是需要一种可以在一阶段训练中就很好解决类不平衡问题的方法

### 解决问题及目标

1. 训练过程中的class imbalance问题
2. 自动降低头部类的权重

### 解决思路

这是原来的ce loss：

![image-20221114174140496](C:\Users\Luo\AppData\Roaming\Typora\typora-user-images\image-20221114174140496.png)

另pt为：

![image-20221114174200666](C:\Users\Luo\AppData\Roaming\Typora\typora-user-images\image-20221114174200666.png)

故原来的ce loss可以写为：

![image-20221114174323990](C:\Users\Luo\AppData\Roaming\Typora\typora-user-images\image-20221114174323990.png)

在这个loss的基础上乘上前边这一项：

![image-20221114174235738](C:\Users\Luo\AppData\Roaming\Typora\typora-user-images\image-20221114174235738.png)

类似一般的balanced loss，可以在前边加上一个α来控制：

![image-20221114174408828](C:\Users\Luo\AppData\Roaming\Typora\typora-user-images\image-20221114174408828.png)

通过式子可知，pt反应了分类的难易程度，pt越大说明分类的置信度越高，即样本越易分；反之就越难分。因此focal loss相当于增加了难分样本在损失函数的权重，使得损失函数倾向于难分的样本，有助于提高难分样本的准确度。与ce loss（蓝色线）对比如下：

![image-20221114211614849](C:\Users\Luo\AppData\Roaming\Typora\typora-user-images\image-20221114211614849.png)

## Label smooth loss

*参考论文：Rethinking the Inception Architecture for Computer Vision*

**文章主要是提出了几个设计原则，根据这些原则来提高卷积神经网络的性能，并基于Inception架构进行了验证**

**主要是cv领域，我们这里参考其解决模型过度自信的方法——label_smooth_loss；**

### 设计原因

z指的是一个样本的*logits*

在交叉熵loss中，我们的目标是使log-likelihood最大化成以下值：

![image-20221112200950679](C:\Users\Luo\AppData\Roaming\Typora\typora-user-images\image-20221112200950679.png)

右端即当k=y时是1，其他情况均为0

这样的最大化当zk是有限的时候是很难的，但是如果当：

![image-20221112202930922](C:\Users\Luo\AppData\Roaming\Typora\typora-user-images\image-20221112202930922.png)

时，就可以很接近这样的最大化结果。

这会导致：

1. 过拟合：因为模型一直去做的就是给每个训练实例的真实标签分配full概率，太绝对了，这被证明是不好的
2. 让类别里最大的logit和其他logit的差距变得很大很大，结合loss函数对zk求偏导的梯度，这会降低模型的适应能力

总结来说：模型对预测结果太自信了

和LT问题的联系：当样本本身的类别间失衡时，这种zy远大于zk的情况是很容易出现的，即模型over-confidence在LT数据集中是常见的

### 解决问题及目标

1. 过拟合：因为模型一直去做的就是给每个训练实例的真实标签分配full概率，太绝对了，这被证明是不好的
2. 让类别里最大的logit和其他logit的差距变得很大很大，结合loss函数对zk求偏导的梯度，这会降低模型的适应能力
3. 总的来说：防止最大的logit变得比其他所有logit大得多

### 解决思路

核心想法：让original ground-truth distribution不再是1或者0，而是进行smooth处理：

![image-20221112214249757](C:\Users\Luo\AppData\Roaming\Typora\typora-user-images\image-20221112214249757.png)
$$
μ(k)是独立于训练实例的label分布
$$
文章设想的是使用标签的先验分布来作为μ(k)的，在实验中，使用的是均匀分布，让：
$$
μ(k)=1/K
$$
所以，上述q的公式变为：
$$
q'(k)=(1-\epsilon)\delta_k,y+\frac{\epsilon}{K}
$$
称之为LSR(label-smoothing regularization)：

![image-20221112215644454](C:\Users\Luo\AppData\Roaming\Typora\typora-user-images\image-20221112215644454.png)

把ce loss的H(q,p)拆成了一对：H(q,p)和H(μ,p)，第二个loss是的分布μ是我们设置的先验分布，若是**label分布p偏移了μ**，那么第二个loss就会对其进行惩罚，这个偏移是可以用KL散度来进行表示的：

![image-20221112220137223](C:\Users\Luo\AppData\Roaming\Typora\typora-user-images\image-20221112220137223.png)

实验参数取值：
$$
\epsilon=0.1,\mu=1/K,K=1000
$$




### 其他

记录下文章提出的几个 design principles（：

1. Avoid representational bottlenecks；也就是说输入信息的内容不能仅仅通过表征的维度来表示，因为它并没有包含一些结构信息等等
2. Higher dimensional representations are easier to process locally within a network. 让网络训练地更快，需要让卷积神经网络中的per tile的activations增加
3. Spatial aggregation can be done over lower dimensional embeddings without much or any loss in representational power.输出被用于空间聚合（Spatial aggregation）的话，输入可以降维，因为相邻的单元之间是具有强关联性的，所以降维并不会损失太多信息，并且可以促进更快的学习
4. Balance the width and depth of the network.宽度深度要平衡一些
5. Although these principles might make sense, it is not straightforward to use them to improve the quality of networks **out of box**. The idea is to use them **judiciously in ambiguous situations only**.

## Equalization loss

*参考论文：Equalization Loss for Long-Tailed Object Recognition CVPR 2020*

**文章主要是提出一个novel的思考角度：一个类别的每个正向样本可以被看作是另一个类别的一个负向样本。使得尾部类获得更多的discouraging gradient（打压梯度），从而导致尾部类的负向梯度norm高于正向梯度，这种情况称之为suppression during learning caused by the inter-class competition。基于这一角度，提出了equalization loss**

### 设计原因

<img src="C:\Users\Luo\AppData\Roaming\Typora\typora-user-images\image-20221113155126473.png" alt="image-20221113155126473" style="zoom: 80%;" />

如上图：头部类正向样本梯度的影响要大于负向样本梯度，而对于尾部类来说就是刚好相反，也就是说更倾向去把尾部类预测为负向

进一步分析：在模型的训练过程中，某一类别的样本在训练过程中一定会对预测其他类别的参数有打压作用，那么尾部类的预测就会被头部类压倒性地压制（有一种尾部类别B，当其艰难的出现时， the predictors for these classes are overwhelmed by the discouraging gradients during network parameters updating.）

### 解决问题及目标

1. 尾部类在训练过程中会被打压，这种打压是由于其他类的正向可以看作是本类的负向，对数量稀少的尾部类来说，就会产生更多的discourageing gradient，从而造成负向梯度norm高于正向，进而导致表现惨淡
2. 把所有类别的负向样本平均梯度降低，如上图蓝色曲线所示

### 解决思路

1. 忽略会影响尾部类的负向样本discouraging gradient
2. 但是，不忽略background类别的负向样本的gradient，但是在我们的问题里，每个样本都是明确属于某一类的，所以不存在这一情况

SEQL(Softmax equalization loss)具体公式：

![image-20221113173601094](C:\Users\Luo\AppData\Roaming\Typora\typora-user-images\image-20221113173601094.png)

![image-20221113173610892](C:\Users\Luo\AppData\Roaming\Typora\typora-user-images\image-20221113173610892.png)

![image-20221113173620105](C:\Users\Luo\AppData\Roaming\Typora\typora-user-images\image-20221113173620105.png)
$$
T_{\lambda}(x)是一个阈值函数，如果x<\lambda,则取1，否则取0
$$

$$
f_{k}是第k类的frequency，\beta是随机的，有\gamma的概率为1，1-\gamma的概率为0
$$

wk是每一类的每一个样本的权重项，主要作用是减少负向样本对尾部类的影响

![image-20221113200147148](C:\Users\Luo\AppData\Roaming\Typora\typora-user-images\image-20221113200147148.png)

原来的情况下，每来一个sample，如果其真实标签不是j类的话，那么对j类来说，就要接受一个discouraging gradient pj，当j类是尾部类时，discouraging gradient 就要远频繁于 encouraging gradient  

添加这样一个权重项，就能把尾部类的discouraging gradient中大部分忽略了，忽略多少由γ来决定

## Influence-Balanced Loss

*参考论文：Influence-Balanced Loss for Imbalanced Visual Classification ICCV 2021*

**文章主要对当下的几种基于loss的方法进行了对比，并提出了一种新的角度：从sample的influence来进行re-weight，这里的influence指的是sample对decision边界的影响大小。**

### 设计原因

1. 根据数量大小反比例地赋予权重的loss方法：例如class-balanced loss，这类方法对同一个类中的sample都赋予一个固定的权重，但是并不是所有的sample都对模型参数更新扮演同样重要的角色
2. sample-wise loss方法：这类方法主要是降低分类良好的samples的权重，赋予分类差的更多的权重；但是这会造成比较高的error。对于头部类和尾部类的重叠region部分的hard samples，模型会产生对它们的过拟合

 In the imbalanced data, most hard samples are majority samples that enforce the decision boundary to be complex and shift to the minority region.（即很多难分的样本其实大部分也是头部类的，这就导致decision边界会往尾部类那边移动，从而造成过拟合以及尾部类表现不佳的问题）

![image-20221115124908248](C:\Users\Luo\AppData\Roaming\Typora\typora-user-images\image-20221115124908248.png)

### 解决问题及目标

1. 降低那些会造成过拟合的样本的权重
2. 如何衡量一个样本对decision边界的influence
3. 总的来说就是让minor class与major class的交界处边界更简单（不会过拟合）

### 解决思路

1. 度量样本对decision边界的影响：Influence function（*该函数可以用来估计当移除一个样本时模型参数的改变*）

   原本的最佳参数：

   

   <img src="C:\Users\Luo\AppData\Roaming\Typora\typora-user-images\image-20221115153600316.png" alt="image-20221115153600316" style="zoom:50%;" />

   <img src="C:\Users\Luo\AppData\Roaming\Typora\typora-user-images\image-20221115153713747.png" alt="image-20221115153713747" style="zoom:50%;" />

   当去掉点（x，y）之后新的参数：

   <img src="C:\Users\Luo\AppData\Roaming\Typora\typora-user-images\image-20221115153819523.png" alt="image-20221115153819523" style="zoom:50%;" />

   <img src="C:\Users\Luo\AppData\Roaming\Typora\typora-user-images\image-20221115155131563.png" alt="image-20221115155131563" style="zoom:50%;" />

   <img src="C:\Users\Luo\AppData\Roaming\Typora\typora-user-images\image-20221115155407433.png" alt="image-20221115155407433" style="zoom:50%;" />

   但是，Hessian矩阵有着庞大的计算量，所以在设计上忽略了H-1的计算：

   <img src="C:\Users\Luo\AppData\Roaming\Typora\typora-user-images\image-20221115160911767.png" alt="image-20221115160911767" style="zoom:50%;" />

   在实际的应用中，只关注深度神经网络中的最后一层全连接层，另h为全连接层的输入，f表示输出，<img src="C:\Users\Luo\AppData\Roaming\Typora\typora-user-images\image-20221115161238776.png" alt="image-20221115161238776" style="zoom:50%;" />，这是其中<img src="C:\Users\Luo\AppData\Roaming\Typora\typora-user-images\image-20221115161326263.png" alt="image-20221115161326263" style="zoom:50%;" />，σ指的是softmax函数，全连接层的权重矩阵为：<img src="C:\Users\Luo\AppData\Roaming\Typora\typora-user-images\image-20221115161506530.png" alt="image-20221115161506530" style="zoom:50%;" />

   这一层的loss梯度可以计算：

   <img src="C:\Users\Luo\AppData\Roaming\Typora\typora-user-images\image-20221115161733912.png" alt="image-20221115161733912" style="zoom:50%;" />

   那么在这一场景下的IB可以表示为：

   <img src="C:\Users\Luo\AppData\Roaming\Typora\typora-user-images\image-20221115161800700.png" alt="image-20221115161800700" style="zoom:50%;" />

   应用到loss上就是，影响越大的loss越小，直接在原有的loss下除以IB：

   <img src="C:\Users\Luo\AppData\Roaming\Typora\typora-user-images\image-20221115161930231.png" alt="image-20221115161930231" style="zoom:50%;" />

   更进一步，针对每一个类别也作出不同的策略，增加一个class-wise re-weighting项：

   <img src="C:\Users\Luo\AppData\Roaming\Typora\typora-user-images\image-20221115162437472.png" alt="image-20221115162437472" style="zoom:50%;" />

   <img src="C:\Users\Luo\AppData\Roaming\Typora\typora-user-images\image-20221115162513487.png" alt="image-20221115162513487" style="zoom:50%;" />

   这里的nk是第k类的样本数量，若样本属于尾部类，则λk就越小，意味着就不down-weight loss太多。这是因为往往是头部类的样本导致了过拟合

### 其他

文章给当前的Class Imbalance Learning分了三类：

1. Data-level approach：

2. Re-weighting approach

3. Meta-learning approach：

   *参考论文：Model-agnostic meta-learning for fast adaptation of deep networks*

   *参考博客：https://blog.csdn.net/qq_40913465/article/details/112608242*

   元学习Meta Learning是什么？

   含义为学会学习，即learn to learn，就是带着这种对人类这种“学习能力”的期望诞生的。Meta Learning希望使得模型获取一种“学会学习”的能力，使其可以在获取已有“知识”的基础上快速学习新的任务

   <img src="https://pic4.zhimg.com/80/v2-23b952fb974edeffa4e28d0065440227_720w.jpg" alt="img" style="zoom:80%;" />

   ​	在元学习中，训练单位分层级了，**第一层训练单位是任务，也就是说，元学习中要准备许多任务来进行学习，第二层训练单位才是每个任务对应的数据**。

   ​	元学习是连原本机器学习的超参都要能自动由机器进行设定，**MAML的目的是获取一组更好的模型初始化参数（即让模型自己学会初始化）**

   以MAML为例：

   <img src="https://pic1.zhimg.com/80/v2-eda1034966a79b9f1c30dc6527b57830_720w.jpg" alt="img" style="zoom: 67%;" />

   meta网络的每一次参数更新都和子任务**第二次更新**的gradient的方向来更新参数：即先用meta网络去训练，用该任务的训练集来优化，用优化好的参数和测试集一起计算梯度，让meta网络的优化方向和该梯度一致

   为了加强理解，可以和pretrain+transfer learning作对比：

   <img src="https://pic3.zhimg.com/80/v2-fa485a14b8e56279f66c88678924385a_720w.jpg" alt="img" style="zoom:67%;" />

   1. MAML是使用子任务的参数，**第二次更新**的gradient的方向来更新参数（所以左图，第一个蓝色箭头的方向与第二个绿色箭头的方向平行；左图第二个蓝色箭头的方向与第二个橘色箭头的方向平行）
   2. 而model pretraining是使用子任务第一步更新的gradient的方向来更新参数(子任务的梯度往哪个方向走，model的参数就往哪个方向走)。

   从sense上直观理解：

   - model pretraining最小化当前的model（只有一个）在所有任务上的loss，所以model pretraining希望找到一个在所有任务（实际情况往往是大多数任务）上都表现较好的一个初始化参数，这个参数要在多数任务上**当前表现较好**。
   - meta learning最小化每一个子任务训练一步之后，第二次计算出的loss，用第二步的gradient更新meta网络，这代表了什么呢？子任务从【状态0】，到【状态1】，我们希望状态1的loss小，说明meta learning更care的是**初始化参数未来的潜力**。

   **一个关注当下，一个关注潜力。**

   ![img](https://pic2.zhimg.com/80/v2-ddb742b8ed1a40a3bf1d3c87bf3deeb9_720w.jpg)

   ## Class dependent temperatures loss（CDT loss）

   参考论文：Identifying and Compensating for Feature Deviation in Imbalanced Deep Learning

   **文章最主要的思路在于，把尾部类的权重特征乘积这一decision value强行拔高**

   ### 设计原因

   1. 利用t-SNE对数据进行分析之后发现，训练数据越少，deviation就会越大，进而导致了过拟合问题：

      <img src="C:\Users\Luo\AppData\Roaming\Typora\typora-user-images\image-20221115164803367.png" alt="image-20221115164803367" style="zoom: 67%;" />

      2.而minor class的不仅deviation严重，并且训练完之后，测试集的decision value甚至会比训练集的更低

      <img src="C:\Users\Luo\AppData\Roaming\Typora\typora-user-images\image-20221115170852145.png" alt="image-20221115170852145" style="zoom:67%;" />

      ### 解决问题及目标

      1. 提高minor class样本的decision value
      2. 模拟这种训练过程中在原本的数据特征偏移基础上发生的进一步decision value的偏移

      ### 解决思路

      1. 基于softmax ce，设计以下这一loss，是一个比较朴素的想法：模拟偏移，相当于原来训出来的decision value是2，那么我给这一类的用来计算loss的decision value除掉10，那么现在训出来的decision value就应该是20

         <img src="C:\Users\Luo\AppData\Roaming\Typora\typora-user-images\image-20221115171103664.png" alt="image-20221115171103664" style="zoom:67%;" />

         2.上式中的a是temperature，类的样本数目越少，a就越大，这样子训练出来的<img src="C:\Users\Luo\AppData\Roaming\Typora\typora-user-images\image-20221115171405815.png" alt="image-20221115171405815" style="zoom:33%;" />就会更大

      

# Project Design

The robotic arm grasping the object block mainly realizes two main functions, the image recognition function of the camera and the planning path function.

## Image Recognition

The position of the target is determined by comparing the template and the actual captured image.

<div align="center">
    <img src="template.jpg" width="100px" display="inline"> 
</div>

Target blocks at different angles

<div align="center">
    <img src="0.jpg" width="300px" display="inline"> 
    <div>
        <p>0°</p>
    </div>
</div>


## 位运算

将一些关键参数转换为 2 的指数幂，那么原来的乘法运算转换为左移操作，除法运算转换为右移操作，求余运算转换为与运算，大大提高了计算速度。

这是受到 JDK 中 HashMap 的启发，HashMap 将数组长度转换为最小的 2 的指数幂，那么在求一个节点所在的数组下标的时候，就可以将原来的求余运算转换为位与运算。

<div align="center">
    <img src="3.gif" width="300px" display="inline"> 
    <img src="4.png"  width="300px" display="inline">
</div>



## 乐观策略

在破坏模型之后需要进行一个分隔分析来判断模型是否被破坏成多个部分，如果是的话，就要为每个部分新建模型。

一种悲观的策略是进行一次全局的深度优先搜索，而我采用的是乐观策略，先对破坏位置进行分析，如果没有被分割成多个部分，那么整体一定没有被分割成多个部分，否则就改为进行一次全局的分析。

这是受到乐观锁的启发，在这里全局的分析相当于加锁，而我们应该尽量避免加锁操作，因为代价很高，所以就先尝试不加锁，只有在发生冲突时才进行加锁。

下图中，左边是全局分隔分析示意图，右边是局部分隔分析示意图。



<div align="center">
    <img src="1.gif" width="300px" display="inline"> 
    <img src="2.gif"  width="300px" display="inline">
</div>

## 递归优化

将原来的递归操作转换为迭代操作，避免 Stack Overflow。

模型分隔分析需要进行一次全局的深度优先搜索。在未优化之前，采用的是递归的方式实现，但是因为需要搜索上百万个节点，因此出现了 StackOverflow。使用迭代改进之后，就不需要将一些数据压入递归栈中，基本不消耗内存。


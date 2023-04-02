## 一、对齐
迭代最近点算法 ICP
方法：
给定一对形状 X 和 Y，迭代：
1. 对任意 xi ∈ X，找到最近点 yi ∈ Y
2. 求解变形参数旋转 R 和平移 t，最小化
$$
\sum_{i=1}^N ||Rx_i+t-y_i||_2^2
$$

问题：非均匀采样
解决：最小化点到切平面的距离
$$
\sum_{i=1}^N ((Rx_i+t-y_i)^Tn_{y_i})^2
$$

## 二、法向估计
方法：
找到切线方向的最佳近似，从而找到法线。
切线特点：到所有点距离最小。
$$
n_{opt}=arg\,min\sum_{i=1}^k((p_i-P)^Tn)^2
$$
使用拉格朗日乘子法求n。

由于采样不均匀，一般使用半径为r的球内的所有点。
如何选择r？
由于曲率，较大的r会导致估计偏差。
由于噪声，较小的r会导致误差。

## 三、去噪
目的：移除不靠近表面的点。

方法：
协方差矩阵
$$
C=\sum_{i=1}^k(p_i-P)(p_i-P)^T
$$
对于任意向量v，其Releigh quotient为
$$
\begin{aligned}
\frac{v^TCv}{v^Tv}&=\sum_{i=1}^k((p_i-P)^Tn)^2\,\,\,if||v||=1 \\
&=\sum_{i=1}^k(||pi-P||\,cos\theta_i)^2
\end{aligned}
$$
含义：对于向量v，求P周围的点pi到v的距离之和。

求解：
协方差矩阵的特征根
$$
Cv=\lambda v
$$
则Releigh quotient就是C的特征根
$$
\frac{v^TCv}{v^Tv}=\lambda
$$
令C的两个特征根为λ1，λ2
$$
\begin{aligned}
min\frac{v^TCv}{v^Tv}=\lambda_{min}=\lambda_1\\
max\frac{v^TCv}{v^Tv}=\lambda_{max}=\lambda_2
\end{aligned}
$$
如果所有点在一条直线上（无噪声）
$$
\begin{aligned}
&\lambda_1=0,\, \lambda_2很大 \\
&\frac{\lambda_1}{\lambda_2}=0
\end{aligned}
$$
如果点随机分布（噪声）
$$
\begin{aligned}
&\lambda_1\approx\lambda_2 \\
&\frac{\lambda_1}{\lambda_2}\approx 1
\end{aligned}
$$
因此可以移除超过一定阈值的点
$$
\frac{\lambda_1}{\lambda_2}\gt\epsilon
$$

## 四、表面重建
<[《GAMES203：三维重建和理解》3 曲面重建（Surface Reconstruction） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/464675628#:~:text=1%20%E7%AC%AC%E4%B8%80%E4%B8%AA%E9%98%B6%E6%AE%B5%E4%BB%8E%E7%82%B9%E4%BA%91%E6%9E%84%E5%BB%BA%E9%9A%90%E5%BC%8F%E6%9B%B2%E9%9D%A2%EF%BC%9A%20%E6%A0%B8%E5%BF%83%E6%80%9D%E6%83%B3%E6%98%AF%E5%AE%9A%E4%B9%89%E8%B7%9D%E7%A6%BB%E5%87%BD%E6%95%B0%20f%3ADto%20mathbb%20%7BR%7D%5En%20%EF%BC%8C%E8%AE%A1%E7%AE%97%E5%88%B0%E9%9A%90%E5%BC%8F%E6%9B%B2%E9%9D%A2%E7%9A%84%E6%9C%89%E7%AC%A6%E5%8F%B7%E5%87%A0%E4%BD%95%E8%B7%9D%E7%A6%BB%EF%BC%8C%E5%85%B6%E4%B8%AD,D%20subset%20mathbb%20%7BR%7D%5E3%20%E6%98%AF%E6%95%B0%E6%8D%AE%E9%99%84%E8%BF%91%E7%9A%84%E5%8C%BA%E5%9F%9F%EF%BC%8C%E5%88%99%E9%9B%B6%E6%B0%B4%E5%B9%B3%E9%9B%86%20Z%20%28f%29%20%E5%8D%B3%E6%98%AF%E6%9E%84%E5%BB%BA%E5%87%BA%E7%9A%84%E9%9A%90%E5%BC%8F%E6%9B%B2%E9%9D%A2%EF%BC%9B)>
### 1. 从点云构建隐式曲面
**隐式曲面**
<[计算机图形学九：隐式曲面(代数形式,CSG, 距离函数,分型几何)与显式曲面 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/144392820)>
例：f(x,y)=x^2+y^2-r^2=0 定义了一个曲面。
当f(x,y)>0，点在曲面外；当f(x,y)<0，点在曲面内；
当f(x,y)=0，点在曲面上。

**点云转换为隐式曲面**
给定空间中的点x，找出点云数据中最近的点p，则x到切平面距离
$$
f(x)=(x-p)^Tn_p
$$
f(x)=0 就是切平面隐式曲面。
注意：需要一致定向的发现，可以尝试局部连接点和固定方向。
（比如下一个切平面的法线有两种方向，则选择和当前法线夹角较小的法线）

**泊松表面重建**
构建曲面的指标函数
$$
\chi_M(p)=
\begin{cases}
1&if\,\,p\in M\\
0&if\,\,p\notin M
\end{cases}
$$
指标函数特点：指标函数的梯度应与表面的法向量场V一致。

求解指标函数：
$$
min_\chi||\nabla\chi-V||^2
$$
采用散度算子转换为：
$$
min_\chi||\Delta\chi-\nabla\cdot V||^2
$$
所以问题转化为了求解泊松方程
$$
\Delta\chi=\nabla\cdot V
$$

### 2. Marching Cubes 算法抽取等值面，重建网格模型
2D
1. 给定一个2D形状，将2D空间划分为一个个像素。
2. 可知哪些格点在图形内部，哪些格点在图形外部。
3. 在内部和外部之间的每个边缘的某地方，原始表面必与网格相交（紫色点）。
4. 在每个正方形内，连接紫色点，得到近似的原始表面。

3D
1. 将空间划分为体素格。
2. 可以制作一个索引表表示从顶点状态到网格连接关系的映射。

点云->网格 总结：
1. 估计法向
2. 去噪
3. 划分体素格
4. 求解泊松方程计算指标函数
5. 根据索引得到网格

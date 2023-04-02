## 一、几何处理流程
1. 输入
2. 表面重建
3. 表面分析
4. 去噪
5. 参数化
6. 网格简化
7. 网格细分

## 二、输入->表面重建
### ICP
1. 选择若干个随机点。
2. 匹配每个随机点在另一个模型中的最近点，可使用k-d树等数据结构。
3. 构建损失函数：
	$$E=\sum||Rp_i+t-q_i||^2 $$
5. 最小化损失函数。
<[(21条消息) ICP算法详解——我见过最清晰的解释_负壹的博客-CSDN博客_icp算法](https://blog.csdn.net/qq_41685265/article/details/107140349)>

## 三、表面重建->表面分析
### 拉普拉斯平滑
diffusion方程
$$
\frac {\partial}{\partial t}x = \mu \Delta x
$$
diffusion方程离散化
$$
\frac {\partial}{\partial t}p_i = \mu \Delta p_i
$$
迭代
$$
p_i' = p_i + \mu dt \Delta p_i
$$

## 四、去噪->参数化
### 1. 纹理映射
<[纹理映射（Texture mapping） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/364045620)>

### 2. 体渲染
有些物体不适合一般的纹理映射，如云彩。
ray marching算法
1. 算法从摄像机开始，向世界空间投射光线，并逐步行进，记录沿途的信息。
2. 沿途不断判断当前点是否在云层中，如果沿途至少有一点在云层中，认为视线和云层相交。

云朵是用没有具体边界的密度函数描述。
每次采样累计云朵的密度，就可以知道当前光线穿越了多厚的云。

RGBA色彩空间，RGB通道存储颜色，A通道为透明度。
背景颜色bgColor，透明覆盖物的颜色cvColor，1.0-cvColor.a为不透明度，最终颜色c
$$
c = bgColor * (1.0-cvColor.a) + cvColor
$$

### 3. 参数化
##### 基本方法
二维：u, v
三维：x, y, z
二维->三维的函数映射
$$
f(u,v)=
\begin{pmatrix}
x(u,v)\\
y(u,v)\\
z(u,v)
\end{pmatrix}
$$
雅各比矩阵：度量映射，反映扭曲
$$
J=
\begin{pmatrix}
x_u & x_v\\
y_u & y_v\\
z_u & z_v
\end{pmatrix}
$$
二维平面上的dU->三维空间上的dX
$$
dX=JdU
$$
关注长度：取二范数
$$
||dX||^2 = dU J^T J dU = dU I dU
$$
曲面第一基本型I
I的特征值 λ1，λ2
1. λ1=λ2=1 等距
2. λ1/λ2=1 保角
3. λ1λ2=1 保面积

##### 双调和映射
f是调和的如果Δf=0
好处：映射比较连续，不会发生剧烈变化，不会很扭曲。
如果已知三维坐标，要求二维坐标。
拉普拉普方程
$$
\left \{ 
\begin{array}{c} 
\Delta u=0 \\ 
\Delta v=0 \\ 
(u,v)_{| \partial \Omega}=(u_0,v_0) 
\end{array} 
\right .
$$
离散化
$$
L(p_i)= \sum_{j\in N_i} w_{ij}(p_j-p_i)=0
$$
其中wij与三维坐标有关。
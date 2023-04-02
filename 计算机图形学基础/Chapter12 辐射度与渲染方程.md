## 一、预备知识
### 1. 光能
光能：一个区域中光子能量的总和Q。(J)

### 2. 光通量
光能并不会停留和储存在某个位置，而是在始终不断运动。
光通量：单位时间穿过截面的光能。(W)
$$
\Phi=\frac{dQ}{dt}
$$

## 3. 辐照度
辐照度：单位面积上的光通量。表示物体表面受光能的影响程度。(W/m^2)
$$
E=\frac{d\Phi}{dA}
$$

## 4.辐照强度
辐照强度：单位立体角上的光通量。表示视点上感受物体某点的发光强度。(W/sr)
$$
I=\frac{d\Phi}{d\omega}
$$

## 5. 辐射率
辐射率：度量微小表面->微小方向的通量，或微小方向->微小表面的通量。
$$
L=\frac{d\Phi}{dA cos\theta d\omega}
$$

## 二、BRDF
### 1. BRDF的定义与性质
##### 定义
BRDF：描述物体表面将光能从任何一个入射方向反射到任何一个视点方向的反射特性。

考虑某一点的反射：
从wi方向发出的辐射->dA接收到的能量E->指向任何其他方向的辐射
入射微分辐照度
$$
dE(\omega_i)=L(\omega_i)cos\theta_id\omega_i
$$
出射微分辐射率
$$
dL_r(\omega_r)
$$
BRDF定义为反射方向的微分辐射率和入射方向的微分辐照度之比。
$$
\begin{aligned}
f(\omega_i \rightarrow \omega_r)
=\frac{dL_r(\omega_r)}{dE_i}
=\frac{dL_r(\omega_r)}{L_i(\omega_i) cos\theta_i d\omega_i}

\end{aligned}
$$
<[基于物理着色：BRDF - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/21376124)>

##### 性质
1. 线性
2. 可逆性
3. 能量守恒性质

### 2. BRDF模型
##### 分类
1. 经验模型：使用基于实验提出的公式对 BRDF 做快速估计。
2. 基于物理的模型：根据物体表面材料的几何以及光学属性建立反射方程，从而计算 BRDF。
3. 数据驱动的模型：将 BRDF 按照实测数据建立查找表，以便于快速的查找和计算。

##### 经验模型
**Lambert模型**
入射光线被均匀地反射到各个方向，沿不同方向的BRDF是一个常数（描述漫反射）。
反射率：反射辐出度与入射辐照度之比。
$$
\rho
=\frac{E_r}{E_i}
=\frac{\int_\Omega L_r(\omega_r) cos\omega_r d\omega_r}{E_i}
=\frac{fE_i\pi}{E_i}
=\pi f
$$
能够很好地用于描述包含纯粹漫反射的物体（如纸张）。
不能表现出材质的镜面反射效果。

**Phong模型**
在Lamber漫反射模型的基础上，添加了镜面反射项，表达在反射角上的镜面反射效果。
$$
f(l\rightarrow v)=\rho_d+\rho_s \frac{(r\cdot v)^s}{(n \cdot l)}
$$
ρd：漫反射反射率；ρs：镜面反射反射率；s：发光指数，描述镜面反射的锐利度。
Phong模型不满足可逆性 f(l->v)≠f(v->l)

**Blinn-Phong模型**
引入入射方向l和视线方向v的角平分线h，h=(v+l)/2，用 n·h 代替 r·v
$$
f(l\rightarrow v)=\rho_d+\rho_s \frac{(n\cdot h)^s}{(n \cdot l)}
$$
满足可逆性，还可以简化运算。

##### 基于物理的模型
从微观角度来看，几乎没有完全光滑的表面。
微观尺度的表面几何是通过一组微平面（microfacets）集合来建模的。
粗糙度通过微平面法向的统计分布来表达。

**Cook-Torrance模型**
$$
f(l,v)=\frac{F(l,h)G(l,v)D(h)}{4 cos\theta_i cos\theta_0}
$$
n：宏观表面法向，h：微观表面法向
F：菲涅尔因子
G：几何衰减因子
D：微平面法向分布函数

## 三、渲染方程
反射方程
$$
L_r(x,\omega_r)
=L_e(x,\omega_r)
+\int_\Omega L_i(x,\omega_i) f(x,\omega_i,\omega_r) cos\theta_i d\omega_i
$$

问题：多个表面相互反射，需要递归，难以得到解析解。
解决：蒙特卡洛方法
对光路进行蒙特卡洛采样，统计估计。

p(w)的选择
1. 半球均匀分布
2. 针对BRDF的重要性采样
3. 针对光源位置的重要性采样

计算近似结果
$$
\frac{1}{N} \sum_{j=1}^N \frac{f_r(p,\omega_j,\omega_r)L_i(p,\omega_j)cos\theta_j}{p(\omega_j)}
$$

<[(21条消息) 【详解+推导！！】蒙特卡洛方法、接受拒绝采样、重要性采样、MCMC方法_志远1997的博客-CSDN博客_重要性采样和mcmc](https://blog.csdn.net/qq_33302004/article/details/115338116)>
<[计算机图形学十五：全局光照(蒙特卡洛路径追踪) - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/146714484)>




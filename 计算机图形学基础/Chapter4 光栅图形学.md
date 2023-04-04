# Chapter4 光栅图形学

## 一、光栅化的概念

如何缓解走样：
1. 硬件：提高分辨率
2. 引入中间颜色
3. 在采样前模糊（预过滤）



光栅化（扫描转换）：确定最佳逼近图形的像素集合，并用指定的颜色和灰度设置像素。



## 二、直线的扫描转换

### 1. 基本增量算法（DDA算法）
直线
$$
y = kx + h
$$
思路：x++，y+=k并取整。

伪代码：
```C
for(x=x1; x<=x2; x++)
{
	y+=k;
	write_pixel(x,round(y));  // round:取整
}
```

问题：当m>1，绘制时会错过一些像素。

解决：利用对称性，交换x与y的角色。



### 2. Bresenham算法

思路：x++，y+=k，d=y-最近网格点，若d>0.5，取上方的像素，否则取下方的像素。



过程：
$$
\begin{aligned}
&d_{init}=0,\,每走一步\,d=d+k\\
&if(d\geq 1),\,d=d-1
\end{aligned}
$$

$$
\left \{
\begin{array}{c}
x_{i+1}=x_i+1\\
y_{i+1}=
\begin{cases}
y_i+1 &if\,\,d>0.5\\
yi    &if\,\,d\leq0.5
\end{cases}
\end{array}
\right.
$$


改进1：0.5是浮点数
$$
\begin{aligned}
&e=d-0.5\\
&e_{init}=-0.5,\,每走一步\,e=e+k\\
&if(e\geq 0.5),\,e=e-1
\end{aligned}
$$

$$
\left \{
\begin{array}{c}
x_{i+1}=x_i+1\\
y_{i+1}=
\begin{cases}
y_i+1 &if\,\,e>0\\
yi    &if\,\,e\leq0
\end{cases}
\end{array}
\right.
$$



改进2：k是浮点数
$$
\begin{aligned}
&e=(d-0.5)*2dx\\
&e_{init}=-dx,\,每走一步\,e=e+2dy\\
&if(e\geq dx),\,e=e-2dx
\end{aligned}
$$

$$
\left \{
\begin{array}{c}
x_{i+1}=x_i+1\\
y_{i+1}=
\begin{cases}
y_i+1 &if\,\,e>0\\
yi    &if\,\,e\leq0
\end{cases}
\end{array}
\right.
$$


算法步骤：

1. 输入直线两端点P0(x0, y0)和P1(x1, y1)
2. 输入初始值dx, dy, e=-dx, x=x0, y=y0
3. 绘制点 (x, y)
4. e更新为e+2dy，判断e的符号，若e>0，则(x, y)更新为(x+1, y+1)，并且将e更新为e-2dx，否则将(x, y)更新为(x+1, y)
6. 当直线没有画完，重复步骤34



### 3. 比较

1. Bresenham算法和DDA算法都是加斜率。
2. DDA算法每次求一个新的y后取整，Bresenham算法判断符号来决定上下两点。
3. Bresenham算法集中了DDA算法的优点，应用广泛。



## 三、圆的扫描转换

思路：
1. 将圆八等分，只需画出一段圆弧，其余七段直接对称获得。
2. 当前(xi, yi)，则在xi+1处时，纵坐标只能是yi或者yi-1
3. 取二者中点(xi+1, yi-0.5)，若其在圆内，取yi，否则取yi-1

$$
\begin{aligned}
d_i = (x_i+1)^2+(y_i-0.5)^2-r^2\\
y_i+1=
\begin{cases}
&yi&d<0\\
&yi-1&d\geq 0\\
\end{cases}
\end{aligned}
$$

递推
$$
\begin{aligned}
&若d_i<0,\,d_{i+1}=(x_i+1)^2+(y_i-0.5)^2-r^2=d_i+2x_i+3\\
&若d_i\geq 0,\,d_{i+1}=(x_i+1)^2+(yi-1.5)^2-r^2=d_i+2(x_i-y_i)+5
\end{aligned}
$$


算法步骤
1. 初始值x0=0, y0=r, d0=1+(r-0.5)^2-r^2=1.25-r
2. 若横坐标x大于纵坐标y，说明脱离八分之一圆弧，退出
3. 绘制点(x, y)
4. 判断d是否小于0，若是，迭代为d+2x+3，否则迭代为d+2(x-y)+5，并将y-1，回到步骤2



## 四、多边形扫描转换

### 1. 多边形的表示方式
1. 顶点表示：用多边形的有序顶点序列表示多边形。
2. 点阵表示：用位于多边形内部的像素集合来表示多边形。



多边形的扫描转换：顶点表示->点阵表示



### 2. 点阵表示的区域填充
##### 内部表示区域种子填充算法
```C
// 内部区域像素原有颜色为G0，需要填充的颜色为G1
// 提供一个种子点(x,y)，颜色为G0
// 利用四连通区域递归
Flood_Fill_4(x,y,G0,G1)
{
	if(GetPixel(x,y)==G0)
	{
		SetPixel(x,y,G1);
		Flood_Fill_4(x-1,y,G0,G1);
		Flood_Fill_4(x,y+1,G0,G1);
		Flood_Fill_4(x+1,y,G0,G1);
		Flood_Fill_4(x,y-1,G0,G1);
		
	}
}
```



##### 边界表示区域种子填充算法

```C
// BoundaryColor: 边界像素颜色
// InteriorColor: 需要填充的内部像素颜色
Fill_Boundary_4_Connected(x,y,BoundaryColor,InteriorColor)
{
	if(GetPixel(x,y)!=BoundaryColor && GetPixel(x,y)!=InteriorColor)
	{
		SetPixel(x,y,InteriorColor);
		Fill_Boundary_4_Connected(x,y+1,BoundaryColor,InteriorColor);
		Fill_Boundary_4_Connected(x,y-1,BoundaryColor,InteriorColor)
		Fill_Boundary_4_Connected(x-1,y,BoundaryColor,InteriorColor)
		Fill_Boundary_4_Connected(x+1,y,BoundaryColor,InteriorColor)
	}
}
```



### 3. 多边形扫描转换

##### 逐点判断算法
思路：逐个像素判断其是否位于多边形内部。

判断一个点是否位于多边形内部，从当前像素发射一条射线，计算射线与多边形的交点个数。

内部：奇数；外部：偶数

伪代码

```C
for(y=0;y<=y_resolution;y++)
	for(x=0;x<=x_resolution;x++)
	{
		if(inside(polygon,x,y))
			setpixel(framebuffer,x,y,polygon_color);
		else
			setpixel(framebuffer,x,y,background_color);
	}
```

问题：奇异情况

解决：看线段位于射线的同侧还是两侧。位于两侧算1次，位于同侧算2次。

不足：
1. 速度慢，几十万几百万像素的多边形内外判断，大量求交、乘除运算。
2. 没有考虑像素之间的联系。



##### 多边形扫描转换算法

连贯性
1. 区域连贯性：逐点判断->区域判断
2. 扫描线连贯性：逐点判断->区间判断
3. 边连贯性：扫描线连贯性+边连贯性=区域连贯性



思路：从下到上扫描，按扫描线顺序，计算扫描线与多边形的相交区间，再用要求的颜色显示这些区间的象素完成填充工作。



算法步骤

1. 确定多边形所占有的最大扫面线数，得到 ymin 和 ymax。
2. 从 ymin 到 ymax 每次用一条扫描线进行填充。
3. 对一条扫描线填充的过程：
	1. 求交点；
	2. 把所有交点按递增顺序排序；
	3. 交点配对（第一个和第二个，第三个和第四个）；
	4. 区间填色。把相交区间内的像素置为多边形颜色，区间外的置为背景色。



问题：扫描线与多边形顶点相交。

解决：
1. 共享顶点的两条边分别落在扫描线两边，交点只能算一个。
2. 共享顶点的两条边分别落在扫描线同一边，检查两条边另外两个端点的y值：
	1. 两端点y值小于交点y值，交点算0个；
	2. 两端点y值大于交点y值，交点算2个。



## 五、隐藏线隐藏面消除算法

### 1. 消隐的基本概念
消隐基本概念

相对于观察者，确定场景中哪些物体是可见的或部分可见的，哪些物体是不可见的。

消隐可以增加图形的真实感。



图像空间消隐
```C
for(图像中每一个像素)
{
	确定由投影点与像素连线穿过的距离观察点最近的物体;
	用适当的颜色绘制该像素;
}
```
特点：在屏幕坐标系中进行的，生成的图像一般受限于分辨率。

算法复杂度O(nN)：场景中每一个物体要和屏幕中每一个像素进行排序比较，n为物体个数，N为像素个数。



物体空间消隐

```C
for(世界坐标系中的每一个物体)
{
	确定未被遮挡的物体或者部分物体;
	用恰当的颜色绘制出可见部分;
}
```
特点：算法精度高，与显示器分辨率无关，适合于精密的CAD工程领域。

算法复杂度O(n^2)：场景中每一个物体都要和场景中其他物体进行排序比较，n为物体个数。



### 2. 图像空间消隐

##### z缓冲器(z-buffer)算法
1. 帧缓冲器中的颜色置为背景颜色
2. z缓冲器中的z值置为最小值（离视点最远）
3. 以任意顺序扫描各多边形
	1. 对于多边形中的每一像素，计算其深度值z(x,y)
	2. 比较z(x,y)与z缓冲器中已有的值zbuffer(x,y)
	3. 如果z(x,y)>zbuffer(x,y)，那么计算该像素(x,y)的光亮值属性并写入帧缓冲器，更新z缓冲器zbuffer(x,y)=z(x,y)



### 3. 物体空间消隐

##### 背面剔除算法
原理：利用视线方向V和物体表面法向N之间的关系。

N·V<0 不可见

N·V>=0 可见



##### 表优先级算法

原理：离视点近的物体可能遮挡视点远的物体。

在物体空间确定物体之间的可见性顺序（物体离视点远近），由远及近地绘制出正确的图像结果——油画家算法。
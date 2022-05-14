# Use plot
```python
import numpy as np
import matplotlib.pyplot as plt
# 数据准备
x=np.linspace(0,10,num=30)
y=np.sin(x)
z=np.cos(x)
 
# 设置画布大小
plt.figure(figsize=(6, 3))
 
# plot 画x与y和x与z的关系图
plt.plot(x,y,label='sin(x)',color='red', linewidth=1,marker='o',markersize=3)
plt.plot(x,z,label='cos(x)',color='blue',linewidth=1,linestyle='dashed',marker='<',markersize=3)
 
# 设置x轴标签、坐标轴范围，坐标轴刻度，坐标轴刻度旋转角度
plt.xlabel('x')
plt.xlim(0,10)
plt.xticks([0,5,7,10],rotation=50) #
 
# 设置y轴标签、坐标轴范围，坐标轴刻度，坐标轴刻度旋转角度
plt.ylabel('y')
plt.ylim(-1,1)
plt.yticks([-1,-0.5,0,1],rotation=0)
 
#
plt.title('Exaple of plt.plot')
 
# 紧凑布局：自动调整图形、坐标轴、标签之间的距离，对于多个子图时尤其有用。
plt.tight_layout()
 
# 设置显示图例，要在plt.plot 时设置 label='xxx'才能显示图例
plt.legend()
 
# 保存图像，可以是任意后缀名，dpi设置图像清晰度
plt.savefig('./fig1.pdf', dpi=600)  #要放在plt.show()之前，否作保存的图像为空白
 
# 显示图像
plt.show()
```

# 基础绘图

### plot()
绘制函数图像
plot(x,y)或者plot(y)
绘制$cos(x)$在$[0,2\pi]$上的图像
```matlab
plot(cos(0:pi/20:2*pi))
```
如果一次要绘制多个图像，要使用hold on语句保存图片
改变绘图的参数，在plot指令后加上指定的字符串
```matlab
%用红色圈绘制图像
plot(cos(0:pi/20:2*pi),'or')
```
### legend()
为绘制的图像添加图例
```matlab
x=0:0.5:4*pi;
y=sin(x);
legend('sin(x)');
plot(x,y);
```
### title()和label()
title()
xlabel()
ylabel()
zlabel()
```matlab
x=0:0.1:2*pi;
y1=sin(x);y(2)=exp(-x);
plot(x,y1,'--*',x,y2,':o');
xlabel('t=0 to t=2\pi');
ylabel('values of sin(t) and e^{-x}');
title('Function Plots of sin(t) and e^{-x}');
legend('sin(t)','e^{-x}');
```
### text()和annotation()
使用LaTex插入文本

```matlab

```
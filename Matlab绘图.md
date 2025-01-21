Matlab绘图

主要根据郭彦甫老师的matlab课程总结
基础的绘图指令这里没有写，主要是介绍课程中进阶部分的各种指令
# 进阶绘图
### Logarithm Plots
对数图
```matlab
x=logspace(-1,1,100);
y=x.^2;
subplot(2,2,1);
title('Plot');

%x轴坐标设置为对数
subplot(2,2,2);
semilogx(x,y);
title('Semilogx');

%y轴坐标设置为对数
subplot(2,2,3);
semilogy(x,y);
title('Semilogy');

%xy轴坐标均设置为对数
subplot(2,2,4);
loglog(x,y);
title('Loglog');
```
<img src="http://image.slugyao.top/MatlabPicture/LogPicture.png" width=400>

### plotyy
有两个y轴的图
图中左边是ylabel1，右边是ylabel2
```matlab
x=0:0.01:20;
y1=200*exp(-0.05*x).*sin(x);
y2=0.8*exp(-0.5*x).*sin(10*x);
%H1表示对应左边y轴的线，H2表示对应右边y轴的线
[AX,H1,H2]=plotyy(x,y1,x,y2);
%设置左边的y轴
set(get(AX(1),'Ylabel'),'String','Left Y-axis');
%设置右边的y轴
set(get(AX(2),'Ylabel'),'String','Left Y-axis');
title('Labeling plotyy');
set(H1,'LineStyle','--','LineWidth',1.2);
set(H2,'LineStyle',':','LineWidth',1.2);
```

<img src="http://image.slugyao.top/MatlabPicture/Plotyy.png" width=400>

### Histogram
用于显示整体分布的情况
```matlab
y=randn(1,1000);
subplot(2,1,1);
hist(y,10);
title('Bins = 10');
subplot(2,1,2);
hist(y,50);
title('Bins = 50');
```
<img src="http://image.slugyao.top/MatlabPicture/Histogram.png" width=400>

### Bar Charts
用于显示个别的情况
```matlab
x=[1 2 5 4 8];
y=[x;1:5];
subplot(1,3,1); bar(x); title('A bargraph of vector x');

subplot(1,3,2); bar(y); title('A bargraph of vector y');
%bar3函数生成的是3D图
subplot(1,3,3); bar3(y); title('A 3D bargraph');
```
<img src="http://image.slugyao.top/MatlabPicture/Bar.png" width=400>

### Stacked and Horizontal Bar Charts
```matlab
x=[1 2 5 4 8];
y=[x;1:5];
subplot(1,2,1);
bar(y,'stacked');
title('Stacked');

subplot(1,2,2);
barh(y);
title('Horizontal'); 
```
<img src="http://image.slugyao.top/MatlabPicture/StackedHorizontal.png" width=400>

### Pie Charts

中间的图片有一部分与其它部分分开了，具体设置是用[0,0,0,1]确定，1代表第四部分分开
pie3()绘制的是3D的饼图
```matlab
a=[10 5 20 30];
subplot(1,3,1);pie(a);
%中间的图片的缝隙用[0,0,0,1]确定
subplot(1,3,2);pie(a,[0,0,0,1]);
subplot(1,3,3);pie3(a,[0,0,0,1]);
```
<img src="http://image.slugyao.top/MatlabPicture/Pie.png" width=400>

### Polar Charts
极坐标图
```matlab
x=1:100; theta=x/10;r=log10(x);
subplot(1,4,1); polar(theta,r);

theta=linspace(0,2*pi); r=cos(4*theta);
subplot(1,4,2); polar(theta,r);

theta=linspace(0,2*pi,6); r=ones(1,length(theta));
subplot(1,4,3); polar(theta,r);

theta=linspace(0,2*pi); r=1-sin(theta);
subplot(1,4,4); polar(theta,r);
```
<img src="http://image.slugyao.top/MatlabPicture/Polar.png" width=400>

### Stairs and Stem Charts
```matlab
x=linspace(0,4*pi,40);
y=sin(x);
subplot(1,2,1);stairs(y);
subplot(1,2,2);stem(y);
```
<img src="http://image.slugyao.top/MatlabPicture/StairStem.png" width=400>

### Boxplot and Error Bar
```matlab
load carsmall
boxplot(MPG,Origin)
```
<omg src="http://image.slugyao.top/MatlabPicture/Boxplot.png" width=400>

```matlab
x=0:pi/10:pi;
y=sin(x);
e=std(y)*ones(size(x));
errorbar(x,y,e);
```
<img src="http://image.slugyao.top/MatlabPicture/ErrorBar.png" width=400>

***
### fill()
在封闭曲线里填充特定颜色

```matlab
t=(1:2:15)'*pi/8;
x=sin(t);
y=cos(t);
fill(x,y,'r'); axis square off;
text(0,0,'STOP','Color','w','FontSize',80,...
'FontWeight','bold','HorizontalAlignment','center);
```
<img src="http://image.slugyao.top/MatlabPicture/STOP.png" width=400>

```matlab
t=(0:4)'*pi/2;
x=sin(t);
y=cos(t);
fill(x,y,'y','LineWidth',5);
text(0,0,'WAIT','Color','k','FontSize',80,...
'FontWeight','bold','HorizontalAlignment','center');
```
<img src="http://image.slugyao.top/MatlabPicture/WAIT.png" width=400>


### imagesc()
用于3D图像颜色的显示
```matlab
[x,y]=meshgrid(-3:.2:3,-3:.2:3);
z=x.^2+x.*y+y.^2;
surf(x,y,z); box on;
set(gca,'FontSize',16);
zlabel('z');
xlim([-4 4]);xlabel('x');
ylim([-4 4]);ylabel('y');
imagesc(z); axis square;
xlabel('x');ylabel('y'); 
```
<img src="http://image.slugyao.top/MatlabPicture/imagesc1.png" width=400>
<img src="http://image.slugyao.top/MatlabPicture/imagesc2.png" width=400>

### Color Bar and Scheme
使用colorbar指令显示色阶
colormap是一个矩阵，矩阵的每个元素都是一种颜色
可以更换不同的配色方案，只需要在原来的绘图代码后加上colormap()就可以更换配色方案
```matlab
%一些配色方案如下
colormap(jet)
colormap(summer)
colormap(winter)
colormap(autumn)
```
比如绘制$z=x^{2}+y^{2}+xy$函数在区间$[-3,3]$的图像
使用winter配色方案

```matlab
[x,y]=meshgrid(-3:.1:3,-3:.1:3);
z=x.^2+x.*y+y.^2;
mesh(x,y,z)
colormap(winter)
```
<img src="http://image.slugyao.top/MatlabPicture/colormap.png" width=400>

***
## 3D Plots

### plot3()
绘制三维的图像，主要用于画线
```matlab
x=0:0.1:3*pi;
z1=sin(x);
z2=sin(2*x);
z3=sin(3*x);
y1=zeros(size(x));
y3=ones(size(x));
y2=y3./2;
plot3(x,y1,z1,'r',x,y2,z2,'b',x,y3,z3,'g');
grid on;
xlabel('x-axis');
ylabel('y-axis');
zlabel('z-axis');
```
<img src="http://image.slugyao.top/MatlabPicture/plot3.png" width=400>

### Surface

创建一个meshgrid
```matlab
x=-2:1:2;
y=-2:1:2;
[X,Y]=meshgrid(x,y)
```
使用mesh()和surf()绘图
mesh()绘制的图形是网格
surf()绘制的图形网格中间填充了颜色
```matlab
x=-3.5:0.2:3.5;
y=-3.5:0.2:3.5;
[X,Y]=meshgrid(x,y);
Z=X.*exp(-X.^2-Y.^2);
subplot(1,2,1); mesh(X,Y,Z);
subplot(1,2,2); surf(X,Y,Z);
```
<img src="http://image.slugyao.top/MatlabPicture/MeahSurf.png" width=400>


### contour()
可以看成绘制把3D图投影到2D平面上的图

```matlab
x=-3.5:0.1:3.5;
y=-3.5:0.1:3.5;
[X,Y]=meshgrid(x,y);
Z=X.*exp(-X.^2-Y.^2);
subplot(1,2,1); mesh(X,Y,Z)
axis square;
subplot(1,2,2); contour(X,Y,Z)
axis square;
```
<img src="http://image.slugyao.top/MatlabPicture/Contour.png" width=400>


contour()的一些变换
```matlab
x=-3.5:0.2:3.5;
y=-3.5:0.2:3.5;
[X,Y]=meshgrid(x,y);
Z=X.*exp(-X.^2-Y.^2);
%线更加紧密
subplot(1,3,1); contour(Z,[-.45:.05:.45]); axis square;
%加上数值
subplot(1,3,2); [C,h]=contour(Z);
clabel(C,h); axis square;
%涂色
subplot(1,3,3); contourf(Z); axis square;
```
<img src="http://image.slugyao.top/MatlabPicture/Contour2.png" width=400>

 
### meshc()和surfc()
在mesh()和surf()绘制的图片下面加上contour()的效果

```matlab
x=-3.5:0.1:3.5;
y=-3.5:0.1:3.5;
[X,Y]=meshgrid(x,y);
Z=X.*exp(-X.^2-Y.^2);
subplot(1,2,1); meshc(X,Y,Z);
subplot(1,2,2); surfc(X,Y,Z);
```
<img src="http://image.slugyao.top/MatlabPicture/MeshcSurfc.png" width=400>

### view()和light()

view()的两个参数分别代表观察时与y轴的夹角和俯视角(与xOy平面的夹角)
light()设置打光的角度
```matlab
sphere(50);
shading flat;
light('Position',[1 3 2]);
light('Position',[-3 -1 3]);
material shiny;
axis vis3d off;
set(gcf,'Color',[1 1 1]);
view(-45,20);
```
<img src="http://image.slugyao.top/MatlabPicture/ViewLight.png" width=400>




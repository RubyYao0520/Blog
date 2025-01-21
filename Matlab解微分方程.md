简单介绍用Matlab求解微分方程

### 有关概念

**常微分方程(ODE)**：只涉及一个自变量及其导数的方程

**偏微分方程(PDE)**：设计多个自变量及其偏导数的方程

**阶数**：微分方程的阶数是方程中最高导数的阶数。例如${y}''+{y}'+y=0$是二阶常微分方程

**符号解**：形式为一个数学表达式，如$y=x^{2}+e^{x}$

**数值解**：形式为一个数值，如$y(0)=4.3423$

**线性微分方程**：所有项都是关于未知函数及其导数的线性组合

**非线性微分方程**：至少有一项是非线性形式

**初值问题(IVP)**：给定函数在某一点的值及其导数值，求微分方程的解

**边值问题(BVP)**：给定在区间两端的函数值，求解满足这些边界条件的微分方程解

***注：对于常微分方程，只有一小部分可以求得解析解，大部分常微分方程无法求得解析解，只能求得数值解。Matlab无法直接求解高阶微分方程或微分方程组的数值解，必须化成一阶微分方程组才能求数值解***

### 求常微分方程的符号解

$$\frac{\mathrm{d} y}{\mathrm{d} x}+3y=8,y|_{x=0}=2$$

```matlab
clear,clc				    %清除变量，清理工作区
syms y(x)				    %定义要求解的变量
dy=diff(y)				    %y的一阶导
y=dsolve(dy+3*y==8,y(0)==2) %求解方程
```

解得结果为
$$y=\frac{8}{3}-\frac{2\,{\mathrm{e}}^{-3\,x}}{3}$$

求解下列方程组
$$
\left
\{\begin{matrix}
 2\frac{\mathrm{d} x}{\mathrm{d} t}+4x+\frac{\mathrm{d} y}{\mathrm{d} t}-y=e^{t},x|_{t=0}=\frac{3}{2} 
 &\\\frac{\mathrm{d} x}{\mathrm{d} t}+3x+y=0,y|_{t=0}=0
\end{matrix}
\right.
$$​


求解代码如下


```
clear,clc;
syms y(t)
dy=diff(y)
```
求解常微分方程组
试求解下列柯西问题

$$
\left
\{\begin{matrix}
  \frac{\mathrm{d} x}{\mathrm{d} t}=Ax
  \\ x(0)=[x_{1}(t),x_{2}(t),x_{3}(t)]^{T}
\end{matrix}\right.$$

<div>
$$
A=\begin{bmatrix}
  3\quad -1\quad 1
    \\2\quad 0\quad -1
    \\1\quad -1\quad 0
\end{bmatrix}
$$
</div>

求解代码如下
```
clear,clc;
syms x(t) [3,1];
A=[3 -1 1;2 0 -1;1 -1 2];
[s1,s2,s3]=dsolve(diff(x)==A*x,x(0)==[1 1 1]);
```


### 求常微分方程的数值解

Matlab中提供了多个解常微分方程数值解的函数，ode45，ode23等，这些函数的区别可以参考Matlab官网给出的表格

#### 初值问题
求微分方程组的数值解，并且画出x(t),y(t)解曲线图形

$$
\left\{\begin{matrix}
  {x}'=-x^3-y,x(0)=1\\
  {y}'=x-y^3,y(0)=0.5
\end{matrix}\right.
0\le t \le 30 
$$


求解代码如下

```
clear,clc;
close all
eq=@(t,z)[-z(1)^3-z(2);z(1)-z(2)^3];
s=ode45(eq,[0,30],[1;0.5])
%subplot用于创建子窗图
subplot(121),fplot(@(t)deval(s,t,1),[0,30],'--','LineWidth',1.5);
hold on
fplot(@(t)deval(s,t,2),[0,30],'LineWidth',1.5)
legend('$x(t)$','$y(t)$','Location','best','Interpreter','Latex')
xlabel('$t$','Interpreter','latex');
subplot(122),fplot(@(t)deval(s,t,1),[0,30],'k')
xlabel('$x$','Interpreter','latex');
ylabel('$y$','Interpreter','latex','Rotation',0)
```

解曲线图形如下
<img src="http://image.slugyao.top/untitled.png" width=500>



#### 边值问题
在Matlab中，我们使用bvp4c和bvpinit来求解常微分方程的两点边值问题
边值问题的求解需要猜测解
bvp4c的调用格式如下

```
sol=bvp4c(@odefun,@bcfun,solinit,options,p1,p2,...);
```

其中solinit是初始猜测结构，sol.x是初始猜测值

求解微分方程组
$$
\left\{\begin{matrix}
   {u}'=0.5u(w-u)/v\\
    {v}'=-0.5(w-u),\\
   {w}'=(0.9-1000(w-y))-0.5w(w-u)/z\\
    {z}'=0.5(w-u)\\
   {y}'=-100(y-w)
\end{matrix}\right.
$$

边界条件为$$u(0)=v(0)=w(0)=1;z(0)=-10;w(1)=y(1)$$

使用以下猜测解

$$
\left\{\begin{matrix}
  u(x)=1\\
   v(x)=-0.5(w-u),\\
   w(x)=-4.5x^2+8.91x+1\\
   z(x)=-10\\
  y(x)=-4.5x^2+9x+0.91
\end{matrix}\right.
$$


代码如下
```
clear,clc;
%eq是方程的匿名函数其中y(1)~y(5)分别代表题目中的u,v,w,z,y
eq=@(x,y)[0.5*y(1)*(y(3)-y(1))/y(2)
-0.5*(y(3)-y(1))
(0.9-1000*(y(3)-y(5))-0.5*y(3)*(y(3)-y(1)))/y(4)
0.5*(y(3)-y(1))
-100*(y(5)-y(3))];
%bd是猜测解的匿名函数
bd=@(ya,yb)[ya(1)=1;ya(2)-1;ya(3)-1;ya(4)+10;yb(3)-yb(5)];
guess=@(x)[1;1;-4.5*x.^2+8.91*x+1;-10;-4.5*x.^2+9*x+0.91]; 
%给出初始猜测解的结构
guess_structure=bvpinit(linspace(0,1,5),guess); 
sol=bvp4c(eq,bd,guess_structure);
plot(sol.x,sol.y(1,:),'-*',sol.x,sol.y(2,:),'-D',sol.x,sol.y(3,:),':S',...
    sol.x,sol.y(4,:),'-.O',sol.x,sol.y(5,:),'--P') 
legend({'$u$','$v$','$w$','$z$','$y$'}, 'Interpreter', 'latex',... 'Location','southwest') 

```

解曲线的图像如下

<img src="http://image.slugyao.top/617.png" width=500>

最后，这片文章里面的方程式排版不是很美观，我还在学习Latex，见谅😥
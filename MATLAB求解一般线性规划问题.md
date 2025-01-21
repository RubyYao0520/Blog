### 一般线性规划问题

求解下列线性规划问题

$$max\ z=2x_{1}+3x_{2}-5x_{3}$$

$$
s.t. \left\{\begin{matrix}
  x_{1}+x_{2}+x_{3}=7
  &  & \\2x_{1}-5x_{2}+x_{3}\ge10 
  &  & \\x_{1}+3x_{2}+x_{3}\le 12
  &  &\\x_{1},x_{2},x_{3}\ge0
\end{matrix}\right.
$$

先化成标准型

标准型要求:
所求表达式是最小值
约束条件都是小于等于

基于问题求解
max表示求最大值问题
optimvar定义变量,x表示变量名，3表示大小，LowerBound代表下界
Objective表示要求解的表达式
Constraints代表约束表达式(等式和不等式)
fval是取得最优解是x的最优值
sol.x是最优解的值

```matlab
clear,clc;
prob=optimproblem('ObjectiveSense','max');
x=optimvar('x',3,'LowerBound',0); 
prob.Objective=2*x(1)+3*x(2)-5*x(3);
prob.Constraints.con1=2*x(1)-5*x(2)+x(3)>=10;
prob.Constraints.con2=x(1)+x(2)+x(3)==7;
prob.Constraints.con3=x(1)+3*x(2)+x(3)<=12;
[sol,fval,flag,out]=solve(prob);
sol.x
fval
```

***

### 含有绝对值的规划问题

$min\ z=|x_{1}|+2|x_{2}|+3|x_{3}|+4|x_{4}|$

$$
s.t.\left\{\begin{matrix}
  x_{1}-x_{2}-x_{3}+x_{4}\le2
  &  &  & \\x_{1}-x_{2}+x_{3}-3x_{4}\le-1
  &  &  &\\x_{1}-x_{2}-2x_{3}+3x_{4}\le-\frac{1}{2}
 \end{matrix}\right.
$$

要对变量进行变换,使得所有变量的取值范围都是非负数
进行如下变换

$u_{i}=\frac{x_{i}+|x_{i}|}{2},v_{i}=\frac{x_{i}-|x_{i}|}{2}$

这样使得$x_{i}=u_{i}+v_{i},|x_{i}|=u_{i}-v_{i}$


```
clear,clc;
c=[1:4]';
a=[1,-1,-1,1;1,-1,1,-3;1,-1,-2,3];
b=[-2;-1;-1/2];
prob=optimproblem;
u=optimvar('u',4,'LowerBound',0);
v=optimvar('v',4,'LowerBound',0);
prob.Objective=sum(c'*(u+v));
prob.Constraints.con=a*(u-v)<=b;
[sol,fval,flag,out]=solve(prob);
x=sol.u-sol.v;
x
fval
```

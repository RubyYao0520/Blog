使用Matlab求解非线性规划问题

求解下列非线性规划

$$min\ f(x)=x_{1}^{2}+x_{2}^{2}-4x_{1}+4$$

$$s.t.\left\{\begin{matrix}
  g_{1}(x)=-x_{1}+x_{2}-2\le 0,
  &  & \\g_{2}(x)=x_{1}^{2}-x_{2}+1\le 0,
  &  &\\x_{1},x_{2}\ge 0
\end{matrix}\right.$$

和求解线性规划不同的是，求解非线性规划问题必须赋初值
基于问题求解(凸优化)

```matlab
clear,clc;
x=optimvar('x',2,'LowerBound',0);
prob=optimproblem;
prob.Objective=x(1)^2+x(2)^2-4*x(1)+4;
prob.Constraints.con=[-x(1)+x(2)-2<=0,x(1)^2-x(2)+1<=0,]
%赋初值
x0.x=rand(2,1);
[sol,fval,flag,out]=solve(prob,x0);
sol.x
```


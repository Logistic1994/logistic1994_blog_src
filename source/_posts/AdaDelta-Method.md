title: AdaDelta Method
date: 2014-10-26 00:10:57
categories: Machine Learning
tags: [Machine Learning, 梯度下降]
---
####一般来说有这么几种计算参数更新值的方法
1. Per-Dimension First Order Methods:
	* Momentum
		$$\Delta x\_t = \rho\Delta x\_{t-1} - \eta g\_t$$
	* ADAGRAD 需要保存所有的$g\_t$值
		$$\Delta x\_t = - \frac{\eta}{\sqrt{\sum\_{\tau = 1}^t g^2_{\tau}}} g\_t$$
2. Methods Using Second Order Information
	* Newton's method 直接计算二价导数矩阵
		$$\Delta x\_t = -\frac{1}{\left| diag(H\_t)\right| +\mu} g_t$$
	* quasi-Newton's method 模拟二价导数矩阵
	* ADAGRAD 优化的Newton's method
		$$\Delta x\_t = -\frac{1}{\left| diag(H\_t)\right|} \frac{E[g\_{t-w:t}]^2}{E[g\_{t-w:t}^2]} g\_t$$
	
本文介绍AdaDelta Method方法是属于ADAGRAD-like的方式

####AdaDelta Method
* *idea1* :Accumulate Over Window
	* 假定使用固定的前$x$个gradients，这样能保证即使学习时间再长学习也不会停下来
	* 具体更新公式为: 
		$$\Delta x\_t = - \frac{\eta}{RMS[g]\_t} g\_t$$
		$$RMS[g]\_t = \sqrt{E[g^2]\_t + \epsilon}$$
		$$E[g^2]\_t = \rho E[g^2]\_{t-1} + (1-\rho)g\_t^2$$
* *idea2* :Correct Units with Hessian Approximation
	* 一般的只用一价导数的更新方法的单位更新如下：
		$$units\ of \Delta x \propto units\ of\ g \propto \frac{\partial f}{\partial x} \propto \frac{1}{units\ of\ x}$$
	*很明显，前后的单位不一致，除非计算的时候没有单位，AdaDelta Method中将$\Delta x\_t$的表达式换成
		$$\Delta x\_t = - \frac{RMS[\Delta x]\_{t-1}}{RMS[g]\_t} g\_t$$
	*即可很好的保证在更新参数过程中单位的一致性。
* 具体算法流程：
	1.Initialize accumulation variables $E[g^2]\_0=0$, $E[\Delta x^2]\_0=0$
	2.for $t=1:T$ do
	3.Compute Gradient:$g\_t$
	4.Accumulate Gradient:$E[g^2]\_t=\rho E[g^2]\_{t-1}+(1-\rho)g\_t^2$
	5.Compute Update:$\Delta x\_t = - \frac{RMS[\Delta x]\_{t-1}}{RMS[g]\_t} g\_t$
	6.Accumulate Updates:$E[\Delta x^2] = \rho E[\Delta x^2]\_{t-1} + (1-\rho)\Delta x\_t^2$
	7.Apply Update:$x\_{t+1}=x\_t+\Delta x\_t$
	8.end for
		

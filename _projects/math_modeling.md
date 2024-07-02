---
layout: page
title: Numerical Schemes in the Context of Differential Equations
description: Implemented in Python
img: assets/img/math_modeling.png
importance: 1
category: numerical methods
related_publications: false
---

The goal of this project is to introduce standard solution schemes in the realm of numerical methods. We start with the following damped harmonic oscillator, 

$$\ddot x + 2 \zeta \dot x + x = 0.$$


$$\textbf{1.}$$ We give a comprehensive treatment of the implicit Euler scheme, as well as the explicit Euler schemes for the model above.  <span style="float:right;">  <i>[notebook](https://elshenawyom.github.io/assets/jupyter/math_modeling/Problem 1)</i>, <i>[pdf](https://elshenawyom.github.io/assets/pdf/math_modeling/Problem 1)</i></span>

<br>
$$\textbf{2.}$$ A detailed construction of the finite differencing & shooting methods is given, before solving intermediate and boundary value problems for the case of $$\zeta = 2.$$ 
<span style="float:right;">  <i>[notebook](https://elshenawyom.github.io/assets/jupyter/math_modeling/Problem 2)</i>, <i>[pdf](https://elshenawyom.github.io/assets/pdf/math_modeling/Problem 2)</i></span>

<br>
$$\textbf{3.}$$ Finally, we numerically solve the heat equation in two dimensions

$$\frac{\partial u}{\partial t} = \alpha \left( \frac{\partial^2 u}{\partial x^2} + \frac{\partial^2 u}{\partial y^2} \right)$$

for the following initial conditions;

$$u(t, x, y) = \begin{cases}0, \quad \quad ~~~~~t = 0; ~~~~~x,y \in (0, 100)\\
0, \quad \quad ~~~~x = 0; ~~~~~~~~~y \in (0, 100)\\0, \quad \quad ~~~~x=100; ~~~~~y \in (0, 100)\\
200, \quad \quad y=0; ~~~~~~~~~x \in [0,100]\\200, \quad \quad y=100; ~~~~~x \in [0, 100]\\
\end{cases}$$

to conclude this nice tutorial. Many thanks to Mohammad Bennani for his contributions. $$\fbox{$\xi$}$$
<span style="float:right;">  <i>[notebook](https://elshenawyom.github.io/assets/jupyter/math_modeling/Problem 3)</i>, <i>[pdf](https://elshenawyom.github.io/assets/pdf/math_modeling/Problem 3)</i></span>







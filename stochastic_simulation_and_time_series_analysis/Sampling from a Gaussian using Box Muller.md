- given:
$$f(x) = \frac{1}{\sqrt{2 \pi}} e^{-\frac{x^2}{2}}$$
- this is a normal distribution with a mean of $0$ and variance of $1$
- why don't we just use the inverse technique?
	- problem is: the cumulative distribution
$$F(x) = \int_{-\infty}^{\infty} e^{-\frac{x'^2}{2}} dx'$$
- cannot be expressed analytically
	- cannot evaluate
	- could discretize it, but would lead to discretization error
		- want something more targeted and more efficient
- we can't use the CDF to select a normally distributed random number, but what if we select a pair $(X,Y)$ of normally distributed random numbers and consider the joint distribution
$$f(x,y) = f_X(x)f_Y(y)$$
$$= \frac{1}{\sqrt{2 \pi}}e^{-\frac{x^2}{2}} * \frac{1}{\sqrt{2 \pi}} e^{-\frac{y^2}{2}}$$
$$ = \frac{1}{2 \pi} e^{-\frac{x^2+y^2}{2}}$$
- also used as a way to find the definite integral of a gaussian

$$f(x,y) = \frac{1}{2 \pi} e^{-\frac{x^2+y^2}{2}}$$
- we want to use the substitution:
$$x = r \cos \Theta ; y = r \sin \Theta$$
- where $0 \leq \Theta \leq 2 \pi$, and $0 \leq r \leq \infty$
$$f(r, \Theta) = f_R(r)f_{\theta}(\Theta)$$
$$= \frac{1}{2 \pi} e^{-\frac{r^2}{2}}$$
> at this point, we have a way to calculate $r$ independent of $\Theta$

$$F(r, \Theta) = \int_0^\Theta \int_o^r \frac{1}{2 \pi} e^{-\frac{r^2}{2}}r'dr'd\theta'$$
> Now we can write two different functions: take the outer integral

$$f_{\Theta}(\Theta) = \frac{\Theta}{2\pi} \rightarrow \Theta = 2\pi U_1$$
$$f_R(r) = \int_0^r e^{-\frac{r'^2}{2}}r'dr'$$
$$y =1-e^{-\frac{r^2}{2}}$$
$$e^{-\frac{r^2}{2}} = 1-y$$
$$\frac{-r^2}{2} = \ln (1-y)$$
$$r = \sqrt{-2 \ln(1-y)}$$

>If $U$ is uniformly distributed, so is $U-1$

$$=r= \sqrt{2 \ln U}$$
- this gives us a way to sample from the second piece of the joint PDF
- we have an easy way to sample $r$ and $\Theta$ independently
	- once we have these:
$$x = r \cos \Theta$$
$$y = r \sin \Theta$$
- gives us two independent normal variables
	- selections from normal distributions

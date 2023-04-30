Download Link: https://assignmentchef.com/product/solved-hl2027-miniproject-1
<br>
<h1>Miniproject 1</h1>

In this mini-project you will implement and test a reconstruction method with a regularisation term for 3D CT data of realistic size (ca. 750MB). More formally: Let <em>x </em>be the image to be reconstructed, and <em>y </em>be the result of the application of the ray transform (forward) operator <em>A </em>to <em>x </em>with additive noise <em>ε</em>, that is <em>y </em>= <em>Ax </em>+ <em>ε</em>. The goal is to estimate <em>x </em>as good as possible.

Since this is an ill-posed problem, one usual alternative is to add a regularisation term. In particular, you are going to use the so-called <em>Huber regularisation</em>. This is a <em>Total Variation</em>-type of regularization, where <em>l</em><sub>1</sub>−norm is replaced with the Huber norm for convenience. Therefore, instead of solving the least-squares problem

<em>,                                                                       </em>(1)

you have to choose a parameter <em>λ &gt; </em>0 and solve the <em>regularised problem</em>

<em>,                                                          </em>(2)

where k · k is the norm on the <em>data space </em>(odl.solvers.L2Norm), H<em><sub>γ </sub></em>is the Huber norm, and ∇(·) is an operator that computes gradient of a function (odl.Gradient). Since in practice images have discrete representation, the gradient is approximated by computing the difference between adjacent pixels/voxels. The Huber norm (odl.solvers.Huber) is defined as

H<em><sub>γ</sub></em>(<em>z</em>) = <sup>X</sup><em>h<sub>γ</sub></em>(k<em>z<sub>i</sub></em>k)                                                                       (3)

<em>i</em>

where <em>h<sub>γ</sub></em>(·) is the Huber function

(4)

The Huber function <em>h<sub>γ</sub></em>(<em>t</em>) is a differentiable approximation of absolute value |<em>t</em>|. It is used instead of the absolute value because differentiable functions are easier to minimize. Usually <em>γ </em>is chosen very small.

Solving eq. (1) leads to noisy pixel values (because of ill-posedness). As shown in the course, we had to make use of early stopping in iterative methods. The idea of Huber regularisation is to force adjacent pixels to have similar values by penalizing large gradients. In addition, using <em>l</em><sub>1</sub>−norm or Huber norm promotes the situation, when most of the gradients are 0. Still, some gradient values are allowed to be large. Thus, this regularization promotes images with large constant regions and sharp borders, e.g. cartoon-like images. Solving eq. (2) amounts to a trade-off between trying to find <em>x </em>that fit the data (e.g., k<em>Ax </em>− <em>b</em>k is small), but are not very noisy, that is, H<em><sub>γ</sub></em>(∇<em>x</em>) cannot be too large.

For a suitable choice of the regularisation parameter <em>λ</em>, there is no need to use early stopping: the issue of semi-convergence is (at least partially) taken care of.

<h1>Tasks</h1>

Write a code for solving the regularised problem. If you want, you can use any optimization method implemented in ODL (odl.solvers.smooth). In any case, you have to make sure that the method converges by providing appropriate hyperparameters.

Test the code with 2D phantoms with added noise. You can find some in ODL (under odl.phantom)

Choose parameter <em>γ </em>for the Huber functional and motivate your choice.

Now for the regularization parameter: we have to find some <em>λ </em>which gives a good compromise between data-fit and regularisation. You should implement one of the methods from the lecture to find a good choice of <em>λ </em>for a phantom with different levels of noise. For a few values of <em>λ </em>and levels of noise, compute the evolution of the error with the number of iterations by comparing the noiseless image to the reconstructed one. Make sure that you adapt the method to the problem in eq. (2) and describe in your own words what you are doing.

ONLY when the code is working in 2D phantoms, test it in the realistic dataset. This is very important as the running time could be very long depending on the number of iterations you set. Describe the effect of using different <em>λ </em>and describe qualitatively the evolution of the reconstructed image. Show some slices of the result. Avoid using too many iterations as this will be very slow.

Compute a reconstruction using Filtered Back-Projection (odl.tomo.fbp op). Try changing filter type and frequency scaling. Frequency scaling is the relative frequency cut-off for the filter, a smoother image can be obtained by choosing smaller values. Compare the results to the method used in the previous tasks.

Document your tests and the results of the problem.

<h1>Things you might consider</h1>

The following are useful dependencies for your code, in particular the last entry provides you with a specific geometry for the data.

<strong>import </strong>numpy as np <strong>import </strong>matplotlib . pyplot as plt <strong>import </strong>odl <strong>import </strong>odl . contrib . tomo

The ray transform is obtained as follows in ODL:

<em># The geometry         is       already        included         in ODL.</em>

geometry = odl . contrib . tomo . elekta icon geometry ()

<em># The reconstruction space </em>reco space = odl . uniform discr ([ =112 , =112, 0] ,

[112 ,  112 ,      224] , (448 , 448 , 448) , dtype=’ float32 ’ ) <em># With     these     data ,     we can compute              the         ray         transform # ( which       is             the         operator A in     the         text        above ).</em>

<em># This        operator        can be        applied        to a           reco space . element ()</em>

<em># and returns a data </em><em>space . element () </em>raytrafo = odl . tomo . RayTransform( reco space , geometry) <em># The data space is the range of the ray transform . </em>data space = ray trafo . <strong>range</strong>

The most simple way to minimize the objective function <em>Q</em>(<em>x</em>) in eq. (2) is to use the steepest descent (gradient descent) method:

1: <em>x</em><sub>0 </sub>arbitrary, <em>r</em><sub>0 </sub>:= −∇<em>Q</em>(<em>x</em><sub>0</sub>), <em>t </em>– step size.

2: <strong>for </strong><em>k </em>:= 0<em>,</em>1<em>, … </em><strong>do</strong>

3: <em>x<sub>k</sub></em>+1 := <em>x<sub>k </sub></em>+ <em>tr<sub>k </sub></em>4: <em>r</em><em>k</em>+1 := −∇<em>Q</em>(<em>x</em><em>k</em>+1)

5: <strong>end for</strong>

Note: the Landweber’s method is a special case of the steepest descent.

Optionally, you may improve the steepest descent by implementing a line search:

1: <em>x</em><sub>0 </sub>arbitrary, <em>r</em><sub>0 </sub>:= −∇<em>Q</em>(<em>x</em><sub>0</sub>), <em>t </em>– step size.

2: <strong>for </strong><em>k </em>:= 0<em>,</em>1<em>, … </em><strong>do</strong>

3:               <em>x<sub>k</sub></em><sub>+1 </sub>:= minima of <em>Q </em>on half-line <em>t </em>7→ <em>x<sub>k </sub></em>+ <em>tr<sub>k</sub></em>

4:             <em>r</em><em>k</em>+1 := −∇<em>Q</em>(<em>x</em><em>k</em>+1)

5: <strong>end for</strong>

The steepest descent is known to converge if the step size 0 <em>&lt; t &lt; </em>2 ∗ k∇<em>Q</em>k.

In ODL multiplication of operators <em>f </em>∗<em>g </em>implements composition <em>f</em>(<em>g</em>(·)). For example, the operator k<em>Ax </em>− <em>b</em>k<sup>2 </sup>can be defined in ODL as follows:

Q = odl . solvers . L2NormSquared( data space ). translated (b) * ray trafo

Also you can easily sum operators and multiply by a scalar. For details address documentation <a href="https://odlgroup.github.io/odl/guide/operator_guide.html">https://odlgroup.github.io/odl/guide/operator_guide.html </a>Use this to define a regularization term as in eq. (2).

The operator ∇<em>Q </em>can be obtained in ODL by using .gradient property of an operator.

The norm of an operator can be estimated using the property .norm(estimate=True).

If it you have a complex operator you might want to use properties (<a href="https://en.wikipedia.org/wiki/Operator_norm">https://en. </a><a href="https://en.wikipedia.org/wiki/Operator_norm">wikipedia.org/wiki/Operator_norm</a><a href="https://en.wikipedia.org/wiki/Operator_norm">)</a> to estimate it’s norm by parts.

Document your code by describing what it does. Structure the functions you use so that they accept general input. Give a meaning to each parameter of your function.

The realistic data is available in the file noisy data.npy where you find some data which fits the data space from above. Use np.load(’/hl2027/noisy data.npy’) to get the data and data space.element to go from a numpy array to an ODL element. The file will be also available in Canvas for those who prefer not to use the server. Compute a reconstruction of the original data. Show some slices of the result. Avoid using too many iterations as this will be very slow.
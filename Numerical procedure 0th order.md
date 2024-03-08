This document aims at giving all necessary details to reproduce exactly how this project computes the numerical solutions to the spherically symmetric part of the neutral ablation cloud around the pellet. This document basically serves as a documentation of the Python code of this project, but without the need to read any of the code. Most of this was done the same way for the "Neutral Gas Shielding model" in [Parks-1978](LiteratureNotes/Parks-1978.md) paper. I don't reference this paper here, but if some details are unclear here, it might help to look at the paper ([https://doi.org/10.1063/1.862088](https://doi.org/10.1063/1.862088)). But since the computing power is so much better nowadays, some other numerical methods are used, a higher precision could be achieved and a much greater parameter scan could be conducted.

To get an overview how this document fits into the whole project, read the [Project Summary](Project%20Summary.md).

# Introduction

The neutral gas cloud around the pellet is modeled as a quasi-steady-state ideal gas starting from vector-valued fluid equations.  The derivation of the differential equations for the radially symmetric physical scalar quantities is in [full_derivation_more_clean](HandwrittenNotes/full_derivation_more_clean.pdf) (and [First_Derivation_of_System_of_Eqs](HandwrittenNotes/First_Derivation_of_System_of_Eqs.pdf)). 
To make both the numerical solution possible and to obtain a semi-analytical model, all quantities are normalized to their values at the sonic radius $r_\star$ (where the fluid velocity reaches the speed of sound). From now onward, all quantities denoted with a star ($\star$) refer to their value at the sonic radius and quantities with a tilde ($\sim$) denote non-normalized quantities. For example the normalized density is $\rho_0=\widetilde{\rho}_0/\rho_\star$. Each of those quantities is a function of the normalized radius $r_0=\widetilde{r}_0/r_\star$. The index 0 is now dropped for convenience, since all quantities in this document are the zeroth order.  The differential equations for the normalized quantities are derived in [normalized_zeroth_order_eqs](HandwrittenNotes/normalized_zeroth_order_eqs.pdf) and are ($w=v^2$):  
$$
\begin{align}
    &\frac{dw}{dr} = \frac{4wT}{(T-w)r}\left(\frac{q\Lambda r}{T\sqrt{w}} -1\right)\\
    &\frac{dT}{dr} = \frac{2\Lambda q}{\sqrt{w}}-\frac{1}{2}(\gamma -1)\frac{dw}{dr}\\
    &\frac{dE}{dr} = 2\lambda _\star\frac{L}{r^2\sqrt{w}}\\
    &\frac{dq}{dr} = \lambda _\star\frac{q\Lambda }{\sqrt{w}r^2},
\end{align}
$$  
Where $\Lambda(E)$ and $L(E)$ are normalized empirical functions for the energy attenuation cross section, i.e. known functions of $E$. However, they depend on $E_\star$, which is an unknown. All other sonic quantities could be combined into a single dimensionless unknown quantity $\lambda_\star = r_\star\Lambda _\star p_\star/T_\star$.
Since we have normalized the quantities, we know their values at the sonic radius ($r=1$): 
$$
w(r=1)=1 \quad,\quad T(r=1)=1 \quad,\quad E(r=1)=1 \quad,\quad q(r=1)=1
$$
Boundary conditions that apply to the normalized quantities are:
$$
T(r_p)=0 \quad,\quad q(r_p)=0 \quad,\quad q(\infty)=q_{bg}/q_\star \quad,\quad E(\infty)=E_{bg}/E_\star \quad,\quad p(\infty)=0
$$
While the non-normalized pellet radius $\widetilde{r}_p$ is a model input parameter, the normalized pellet radius $r_p$ is not known a-priori, since $r_\star$ is not known.

Goal of the procedure described here is to find numerical solutions for $w(r),T(r),E(r),q(r)$ which satisfy both the differential equations and the boundary conditions. For this purpose, for some chosen $\gamma$ and $E_\star$, we optimize $\lambda_\star$ so that $T(r_p) = q(r_p) = 0$ at some radius $r_p<1$ which is then interpreted as the normalized pellet radius. Together with the values $q(r\rightarrow\infty)$ and $E(r\rightarrow\infty)$ all unknown quantities can be determined and the full physical quantities could be calculated. In particular $E_{bg}$ must be computed to find out which $E_\star$ should be used.

# Procedure overview

The most important function in this procedure is called `ode0` and it calculates the left hand side of the differential equations. I.e. given a radius $r$, the quantity vector $y_0(r) = [w,T,q,E]$ and the scalar parameters $\gamma$,$E_\star$,$\lambda_\star$ it calculates $dy_0/dr$ .

This function is then used to iteratively build the solution $y_0(r)$ starting from $r=1$ first downwards (`solve_ode0_down`) and then upwards (`solve_ode0_up`) using [scipy.optimize.solve_ivp](https://docs.scipy.org/doc/scipy/reference/generated/scipy.integrate.solve_ivp.html) for given parameters $\gamma$,$E_\star$,$\lambda_\star$.

To optimize $\lambda_\star$ for chosen $\gamma$,$E_\star$ so that the solution satisfies $T(r_p) = q(r_p) = 0$, the function `find_lambda` uses [scipy.optimize.root](https://docs.scipy.org/doc/scipy/reference/generated/scipy.optimize.root.html).

The found parameters $\lambda_\star, r_p, q(r\rightarrow\infty), E(r\rightarrow\infty)$ and the solution $y_0(r)$ can then be used to calculate any quantities. 

# Calculating the derivatives

Apart from just using the differential equations mentioned above, this part also needs to take a specific form of the energy attenuation cross sections $L(E)$, $\Lambda(E)$ and handle the singularity at $r=1$ where $T=w$ .

The cross sections are normalized as
$$
\Lambda(E) = \frac{\widetilde{\Lambda}(\widetilde{E})}{\widetilde{\Lambda}(E_\star)} \quad,\quad L(E)=\frac{\widetilde{L}(\widetilde{E})}{E_\star \cdot \widetilde{\Lambda}(E_\star)} \quad,\quad \widetilde{E}=E\cdot E_\star
$$  
While the specific form of $\widetilde{L}(\widetilde{E})$ and $\widetilde{\Lambda}(\widetilde{E})$ could be anything which represents the underlying physics of the electron energy loss in a gas, in this project we use the same empirical functions that were used in [Parks-1978](LiteratureNotes/Parks-1978.md) paper, since this paper has proven to be quite accurate. Of the form (dropping the tilde here for non-normalized quantities)
$$
\begin{align}
\Lambda (E) &= \hat{\sigma}_T(E) + \frac{2L(E)}{E}\\
L(E>20\,\mathrm{eV}) &= 8.62\cdot 10^{-15}\cdot \left[\left(\frac{E}{100}\right)^{0.823}+\left(\frac{E}{60}\right)^{-0.125}+\left(\frac{E}{48}\right)^{-1.94}\right]^{-1}\,\mathrm{eV\cdot cm^2}\\
\hat{\sigma}_T (E>100\,\mathrm{eV})&= \frac{8.8\cdot 10^{-13}}{E^{1.71}}-\frac{1.62\cdot 10^{-12}}{E^{1.932}}\,\mathrm{cm^2}\\ 
\hat{\sigma}_T (E<100\,\mathrm{eV}) &= \frac{1.1\cdot 10^{-14}}{E}\,\mathrm{cm^2}
\end{align}
$$  
One important detail is that those functions are undefined for $E \leq 20 \, \mathrm{eV}$ , which means that the solution can only be calculated down to this point.

The singular term $1/(T-w)$ in the differential equation for $dw/dr$ leads to a relation for the sonic quantities used to derive the full system of differential equations, but through L'Hopital's rule, it also leads to an expression to substitute the differential equation near $r=1$.  (see [normalized_zeroth_order_eqs](HandwrittenNotes/normalized_zeroth_order_eqs.pdf) for the derivation) This expression is
$$
\chi_\star = \frac{dw}{dr}|_{r=1} = \frac{2}{\gamma+1}\left( -(\gamma-3) + \sqrt{(\gamma-3)^2 - 2(\lambda_\star + \Psi_\star - 1)(\gamma+1)} \right)
$$
with shorthand that depends on the normalized energy attenuation
$$
\Psi_\star = 2 \lambda_\star L(E=1) \frac{d\Lambda}{dE}|_{E=1}
$$  
Because of finite numerical precision, it is however not enough to use $\chi_\star$ only at exactly $r=1$. Therefore, $\chi_\star$ is used in the interval $|r-1|<\Delta r$ , where $\Delta r=10^{-3}$ is arbitrarily chosen through visual inspection of $w(r\approx1)$ and testing what works. If this interval is chosen too big or too small it could lead to decreased precision in the found solutions, but it does not seem to matter too much. Be aware while varying $\lambda_\star$ that $\chi_\star$ can become complex for too large values of $\lambda_\star$, which might cause code to crash.

For the $\frac{d\Lambda}{dE}|_{E=1}$, finite differences are used as
$$
\frac{d\Lambda}{dE} \approx \frac{\Lambda(E) - \Lambda(E - \Delta E)}{\Delta E}
$$  
with arbitrarily chosen $\Delta E = 10^{-5}$. This works well as long as $E \cdot E_\star > 100 \, \mathrm{eV} + \Delta E\cdot E_\star$ because of the discontinuous derivative of $\hat{\sigma}_T$.

# Solving the ODE from $r=1$

Since the normalized quantities are 1 at the sonic radius, it is the most convenient place to start the integration of the system of differential equations.  [scipy.optimize.solve_ivp](https://docs.scipy.org/doc/scipy/reference/generated/scipy.integrate.solve_ivp.html)  is being used and there are multiple parameters to choose. 

One important choice is where to end the integration, i.e. until which $r$ do we need the solution $y_0(r)$. In scipy, this is implemented via either a fixed value $r$ or via "event" functions which should return a scalar value for each step and the algorithms "triggers" where this event function hits 0, or rather where it switches sign. 

For the downwards integration three different event functions are implemented. All of which mark the radius below which the solutions are either non-physical or not well defined. This radius is then taken as the normalized pellet radius $r_p$. To be exact, $r_p$ is taken to be the radius at which the boundary conditions are fulfilled the best, i.e. where $|T_0(r)|+|q_0(r)|$ is the lowest. 
- Physically, the quantities $[w_0, T_0, q_0, E_0]$ are all strictly positive. Therefore we end the integration where one of them gets negative.
- The singular term $1/(T-w)$ is relevant where $T=w$, which is always the case at $r=1$ and at least for $\lambda_\star$ values larger than the optimal one this also occurs again at some $r<1$. Therefore there is another event function which returns $T-w$.
- As mentioned above, the energy attenuation is only defined for $\widetilde{E}>20\,\mathrm{eV}$. In order to stay above this energy threshold, the third event function returns $E\cdot E_\star - (20 + \epsilon) \,\mathrm{eV}$, where $\epsilon = 0.01$ is chosen arbitrarily. (This is needed especially for the first order solution later to converge to accurate results)
- Additionally, if non of those event functions trigger, the integration ends at $r=0.01$ (arbitrarily chosen), since the found solutions (also in Parks paper) show that $r_p > 0.5$ for all tested values of $\lambda, E_\star$

For the upwards integration, $y_0(r)$ is needed until $q_0$ and $E_0$ are converged to a constant value to estimate $q(r\rightarrow\infty)$ and $E(r\rightarrow\infty)$. Rigorously estimating convergence at infinity is non-trivial. In all tested cases $dq_0/dr$ and $dE_0/dr$ come closer and closer to 0 for growing $r$ but that does not mean it converges. It would be best to end the integration where $q_0$ and $E_0$ don't change between integration steps (up to some precision). However, implementing this in the code is quite complicated since the event functions used by `solve_ivp` get passed only the current values and not the values of the previous step. Therefore, the chosen termination condition is instead based on the derivatives being small enough. The event function returns $dq_0/dr + dE_0/dr - \epsilon$, where currently $\epsilon = 10^{-10}$ leads to solutions until $r \approx 10^4$. However, previously I tested even $\epsilon = 10^{-200}$ and the code still ran in a few seconds. Only after scanning the first order solution for very large values of $E_{rel}/q_{rel}$ (to be introduced in a different document) it became apparent that we could need the zeroth order solution until even larger values of $r$ to get accurate first order results. So if needed, $\epsilon$ could be chosen to be even smaller in the future to extend the maximum $r$ of the solution. Since this termination condition was met in all tested cases so far, no additional restriction on $r$ is chosen in the code, which could potentially be a source of `solve_ivp` never finishing because it can never reach $r=\infty$.

Another important choice is the method to use. [scipy.optimize.solve_ivp](https://docs.scipy.org/doc/scipy/reference/generated/scipy.integrate.solve_ivp.html)  offers several methods to choose from. It is unclear if there is a "best" method. The current choice for this project is `method="BDF"`, which is an "Implicit multi-step variable-order (1 to 5) method based on a backward differentiation formula for the derivative approximation". This choice stems mainly from comparing speed and robustness of finding solutions here and is not a well reasoned choice, since all offered methods seem to work well.

The precision of `solve_ivp` is controlled through several parameters. Testing around what works, is fast enough and seems to be precise enough yielded the following, again arbitrarily chosen parameters:
- for the downwards integration: `max_step=0.01, rtol=1e-7, atol=[1e-10, 1e-10, 1e-10, 1e-3]`
- for the upwards integration: `rtol=1e-5, atol=1e-10`
- for details look into scipy's documentation, but `max_step` is the maximum step in $r$, `rtol` is the ensured relative error tolerance (how many decimal points should be correct), `atol` is the absolute tolerance to make sure small values are precise
- a somewhat large `atol` is chosen for $E_0$ because the diverging derivative made the method fail sometimes and $E_0$ seems not very important to get very accurate for low values (this is purely an assumption)

Even though `solve_ivp` returns a flag whether it succeeded or not, I could not implement a check on that. As mentioned, the derivatives diverge close to $r_p$ and `solve_ivp` often fails with the message "Required step size is less than spacing between numbers.". Which just means that it reached machine precision and should therefore still be a good enough solution. The way I convinced myself that all solutions are accurate enough is by visually inspecting plots of the solutions.

# Optimizing $\lambda_\star$ 





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
Where $\Lambda(E)$ and $L(E)$ are normalized empirical functions for the energy attenuation cross section, i.e. known functions of $E$. However, they depend on $E_\star$, which is an unknown. All sonic quantities could be combined into a single dimensionless unknown quantity $\lambda_\star = r_\star\Lambda _\star p_\star/T_\star$.
Since we have normalized the quantities, we know their values at the sonic radius ($r=1$): 
$$
v(r=1)=1 \quad,\quad T(r=1)=1 \quad,\quad E(r=1)=1 \quad,\quad q(r=1)=1
$$
Boundary conditions that apply to the normalized quantities are:
$$
T(r_p)=0 \quad,\quad q(r_p)=0 \quad,\quad q(\infty)=q_{bg}/q_\star \quad,\quad E(\infty)=E_{bg}/E_\star \quad,\quad p(\infty)=0
$$
While the non-normalized pellet radius $\widetilde{r}_p$ is an input parameter, the normalized pellet radius $r_p$ is not known since $r_\star$ is not known.

Goal of the procedure described here is to find numerical solutions for $w(r),T(r),E(r),q(r)$ which satisfy both the differential equations and the boundary conditions. 

....

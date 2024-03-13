This document aims at giving all details to reproduce exactly how this project computes the numerical solutions to the anisotropic perturbation part of the neutral ablation cloud around the pellet. This document basically serves as a documentation of the Python code of this project, but without the need to read any of the code. 

Since the procedures are similar to solving the zeroth order and the zeroth order solution is a prerequisite here, it is beneficial to have read [Numerical procedure 0th order](Numerical%20procedure%200th%20order.md) to fully understand this document.
To get an overview how this document fits into the whole project, read the [Project Summary](Project%20Summary.md).

# Introduction

The $\theta$-dependence of the neutral ablation cloud fluid dynamics are taken as at perturbation to the zeroth order of the form $T(\vec{r}) = T_0(r) + \sum_l T_l(r) \cdot X_l(\theta)$ , where $X_l(\theta)$ are the [associated Legendre polynomials](https://en.wikipedia.org/wiki/Associated_Legendre_polynomials). Important for the pellet rocket force is only the $l=1$ mode ($X_1(\theta)=\cos\theta$). A system of 6 differential equations describing the 6 physical quantities $\vec{y}_1(r)=\left[P_1(r), T_1(r), U_1(r), V_1(r),Q_1(r),E_1(r) \right]^T$ is derived in [full_derivation_more_clean](HandwrittenNotes/full_derivation_more_clean.pdf). For convenience and consistency with the code, slightly different symbols are used here than in the derivations, for example $E_1$ instead of $\mathcal{E}_1$. 

The quantities are normalized using their values at the sonic radius (denoted with a star) and the relative asymmetry factor of the background heat flux $q_{rel}$ in the normalized form $P_1 = \widetilde{P}_1/p_\star q_{rel}$ and the new system of differential equations is derived in [normalized_first_order_eqs](HandwrittenNotes/normalized_first_order_eqs.pdf). It is written in the matrix-vector form 
$$A(r) \cdot \frac{d \vec{y}_1'}{dr} = B(r) \cdot \vec{y}_1(r) \quad \Leftrightarrow \quad \frac{d \vec{y}_1'}{dr} = C(r) \cdot \vec{y}_1(r)$$  
in [first_order_ode_as_matrix_vector_equation](HandwrittenNotes/first_order_ode_as_matrix_vector_equation.pdf), where $\vec{y}_1$ contains the normalized first order quantities and the matrices $A,B$ depend on the normalized zeroth order $\vec{y}_0$, the heat capacity ratio $\gamma$ and the incoming electron energy at the sonic radius $E_\star$. In combination with the boundary conditions 
$$
\begin{gather}
T_1(r_p) = 0,\quad Q_1(r_p)=0,\quad U_1(r_p)=0,\quad V_1(r_p)=0 \\
P_1(\infty) = 0,\quad Q_1(\infty)=q_0(\infty),\quad E_1(\infty)=E_0(\infty)\cdot\frac{E_{rel}}{q_{rel}}
\end{gather}
$$  
this matrix differential equation fully describes the anisotropic perturbation and can be solved numerically for a choice of $\gamma,E_\star,\frac{E_{rel}}{q_{rel}}$.

Goal of the procedures described here is to find a numerical solution $\vec{y}_1(r)$ which satisfies both the differential equation and the boundary condition. For this purpose the integration starts at $r=1$ and $\vec{y}_1(r=1)$ has to be optimized according to the boundary conditions. Together with the zeroth order quantities and a value of $q_{rel}$ all physical perturbation quantities can be calculated. Especially $\widetilde{P}_1(r_p)$, which is important for the pellet rocket force.

# Procedure overview

Before the numerical calculations can start, several things needed to be calculated analytically ($C=A^{-1}\cdot B$, eliminate one $y_{1,i}(r=1)$, $C_\star=C(r=1)$). Since this part is done in a Jupyter Notebook `derivations_1st_order.ipynb` containing [SymPy](https://www.sympy.org/en/index.html) code it is described in a section of this document. 

These SymPy derived expressions are combined into the function `ode1` which calculates $d\vec{y}_1/dr$ for a given $\vec{y}_1(r)$. Additionally to the scalar parameters $\gamma$,$E_\star$,$\lambda_\star$, the zeroth order solution together with its derivative is needed.

This function is then used to iteratively build the solution $\vec{y}_1(r)$ starting from $r=1$ downwards and upwards in `solve_ode1` using [scipy.optimize.solve_ivp](https://docs.scipy.org/doc/scipy/reference/generated/scipy.integrate.solve_ivp.html). Apart from the zeroth order parameters, the parameters that are unknown here are the first order solution at the sonic radius $\vec{y}_1(r=1)$. Since it is possible to eliminate one of those 6 unknowns, this quantity is expected to be `NaN`. (except for $P_1$ any quantity can be chosen)

Optimizing these 5 unknown sonic quantities so that the solution satisfies the boundary conditions at the pellet radius and at infinity is done in `find_ode1_params` using [scipy.optimize.root](https://docs.scipy.org/doc/scipy/reference/generated/scipy.optimize.root.html). Again, the zeroth order parameters $\gamma$,$E_\star$,$\lambda_\star$ have to be chosen and a corresponding zeroth order solution needs to be provided. The additional free parameter $(E_{rel}/q_{rel})$ can be chosen independently from the zeroth order and is the main parameter of the first order.

All of this can be combined to perform a parameter scan and the results are shown at the end of this document.

# Derivation of $C=A^{-1}\cdot B$ and handling of the sonic singularity

As previously mentioned, the differential equation is of the form $\frac{d \vec{y}_1'}{dr} = C(r) \cdot \vec{y}_1(r)$, where $C=A^{-1}\cdot B$ has to be calculated from the derived expressions of $A$ and $B$ in [first_order_ode_as_matrix_vector_equation](HandwrittenNotes/first_order_ode_as_matrix_vector_equation.pdf). Therefore, the starting expressions put into SymPy are
$$
A(r) = \begin{pmatrix}

-v_0/T_0 & v_0 \cdot \rho_0/T_0 & -\rho_0 & 0 & 0 & 0 \\

-1/\gamma & 0 & -\rho_0 \cdot v_0 & 0 & 0 & 0 \\

0 & 0 & 0 & -\rho_0 \cdot v_0 & 0 & 0 \\

-v_0/(\gamma-1) - 1/2 \cdot v_0^3/T_0 & 1/2 \cdot \rho_0 \cdot v_0^3/T_0 & -k_0-\rho_0 \cdot v_0^2 & 0 & 2/((\gamma-1) \cdot \lambda_\star) & 0 \\

0 & 0 & 0 & 0 & 1 & 0 \\

0 & 0 & 0 & 0 & 0 & 1

\end{pmatrix}
$$
and
$$
B = \begin{pmatrix}

v_0 \cdot \frac{d}{dr}(1/T_0) + (\nabla \cdot \vec{v_0})/T_0 & -v_0 \cdot \frac{d}{dr}(\rho_0/T_0)-(\nabla \cdot \vec{v_0}) \cdot \rho_0/T_0 & \frac{d}{dr}(\rho_0)+2 \cdot \rho_0/r & -2 \cdot \rho_0/r & 0 & 0 \\

v_0 \cdot \frac{d}{dr}(v_0)/T_0 & -v_0 \cdot \frac{d}{dr}(v_0) \cdot \rho_0/T_0 & \rho_0 \cdot \frac{d}{dr}(v_0) & 0 & 0 & 0 \\

1/(\gamma \cdot r) & 0 & 0 & v_0 \cdot \rho_0/r & 0 & 0 \\

(\nabla \cdot \vec{v_0}) \cdot (1/(\gamma-1) + 1/2 \cdot v_0^2/T_0)+1/2 \cdot v_0 \cdot \frac{d}{dr}(v_0^2/T_0) & -(\nabla \cdot \vec{v_0}) \cdot 1/2 \cdot \rho_0 \cdot v_0^2/T_0-1/2 \cdot v_0 \cdot \frac{d}{dr}(\rho_0 \cdot v_0^2/T_0) & \frac{d}{dr}(k_0)+2 \cdot k_0/r+v_0 \cdot \frac{d}{dr}(\rho_0 \cdot v_0)+(\nabla \cdot \vec{v_0}) \cdot \rho_0 \cdot v_0 & -2 \cdot k_0/r & 0 & 0 \\

\lambda_\star \cdot q \cdot \Lambda(E_0)/T_0 & -\lambda_\star \cdot \rho_0 \cdot q \cdot \Lambda(E_0)/T_0 & 0 & 0 & \lambda_\star \cdot \rho_0 \cdot \Lambda(E_0) & \lambda_\star \cdot \rho_0 \cdot q_0 \cdot \Lambda'(E_0) \\

2 \cdot \lambda_\star \cdot L(E_0)/T_0 & -2 \cdot \lambda_\star \cdot \rho_0 \cdot L(E_0)/T_0 & 0 & 0 & 0 & 2 \cdot \lambda_\star \cdot \rho_0 \cdot L'(E_0)

\end{pmatrix}
$$
with the shorthands
$$
k_0 = 1/2\cdot \rho_0 \cdot v_0^2 + p_0/(\gamma-1) \\
$$
$$
\nabla \cdot \vec{v}_0 = \frac{1}{r^2} \cdot \frac{d}{dr}(r^2 \cdot v_0)
$$
and all quantities dependent on $r$ (or $E_0$) are taken to be `Function` objects while $r,\gamma,\lambda_\star$ are `Symbol` objects. In the following calculations performed by SymPy the function `simplify` is used frequently, after each step.

$C=A^{-1}\cdot B$ is computed, and the following substitutions are used to eliminate $\rho_0, p_0, v_0$
$$
\rho_0 = p_0/T_0,\quad p_0=\frac{T_0}{r v_0^2}, \quad v_0 = \sqrt{w} \, .
$$  
The resulting expressions for the $3 \times 3$ sub blocks of 
$$
C = \begin{pmatrix}
C_{11} & C_{12} \\
C_{21} & C_{22}
\end{pmatrix}
$$  
are (dropping the index 0, since only the zeroth order quantities appear, and generating LaTeX via SymPy)
$$
\begin{gather}
C_{11} = \frac{1}{T-v^2} \left[\begin{matrix}- \frac{2 \Lambda \gamma q \sqrt{w}}{T} + \frac{2 \gamma w}{r} & \frac{2 \Lambda \gamma q}{T r^{2}} + \frac{\gamma \frac{d}{d r} w}{2 r^{2} \sqrt{w}} & \frac{\gamma \left(- T \frac{d}{d r} w + w \left(\frac{\gamma \frac{d}{d r} w}{2} + \frac{d}{d r} T - \frac{\frac{d}{d r} w}{2}\right)\right)}{r^{2} w}\\\frac{r \left(- 2 \Lambda \gamma q r w + T \left(2 \Lambda q r + 2 \gamma w^{\frac{3}{2}} - r \sqrt{w} \frac{d}{d r} T - 2 w^{\frac{3}{2}}\right) + r w^{\frac{3}{2}} \frac{d}{d r} T\right)}{T} & - \frac{2 \Lambda q}{\sqrt{w}} + \frac{2 \Lambda \gamma q \sqrt{w}}{T} + \frac{\gamma \frac{d}{d r} w}{2} + \frac{d}{d r} T - \frac{\frac{d}{d r} w}{2} - \frac{w \frac{d}{d r} T}{T} & \frac{- \frac{3 T \gamma \frac{d}{d r} w}{2} - T \frac{d}{d r} T + \frac{3 T \frac{d}{d r} w}{2} + \frac{\gamma^{2} w \frac{d}{d r} w}{2} + \gamma w \frac{d}{d r} T - \frac{\gamma w \frac{d}{d r} w}{2}}{\sqrt{w}}\\\frac{r \left(2 \Lambda q r \sqrt{w} - \frac{T r \frac{d}{d r} w}{2} - 2 T w + \frac{r w \frac{d}{d r} w}{2}\right)}{T} & \frac{- 2 \Lambda q - \frac{\sqrt{w} \frac{d}{d r} w}{2}}{T} & \frac{T \frac{d}{d r} w}{2 w} - \frac{\gamma \frac{d}{d r} w}{2} - \frac{d}{d r} T + \frac{d}{d r} w\end{matrix}\right]
\\C_{12} = \frac{1}{T-v^2} \left[\begin{matrix}- \frac{2 T \gamma}{r^{3}} & - \frac{2 \Lambda \gamma}{r^{2}} & - \frac{2 \gamma q \frac{d}{d E} \Lambda}{r^{2}}\\\frac{2 T \sqrt{w} \left(1 - \gamma\right)}{r} & \frac{2 \Lambda \left(T - \gamma w\right)}{\sqrt{w}} & \frac{2 q \left(T - \gamma w\right) \frac{d}{d E} \Lambda}{\sqrt{w}}\\\frac{2 T}{r} & 2 \Lambda & 2 q \frac{d}{d E} \Lambda\end{matrix}\right]
\\C_{21} =  \left[\begin{matrix}- \frac{r}{\gamma} & 0 & 0\\\frac{\Lambda \lambda q}{T} & - \frac{\Lambda \lambda q}{T r^{2} \sqrt{w}} & 0\\\frac{2 L \lambda}{T} & - \frac{2 L \lambda}{T r^{2} \sqrt{w}} & 0\end{matrix}\right]
\\C_{22} =  \left[\begin{matrix}- \frac{1}{r} & 0 & 0\\0 & \frac{\Lambda \lambda}{r^{2} \sqrt{w}} & \frac{\lambda q \frac{d}{d E} \Lambda}{r^{2} \sqrt{w}}\\0 & 0 & \frac{2 \lambda \frac{d}{d E} L}{r^{2} \sqrt{w}}\end{matrix}\right]
\end{gather}
$$  
Since the numerical procedure needs to generate this matrix often, it is exported as Python code using the SymPy function `lambdify`. Apart from avoiding copying errors, this function also makes the computation more efficient since it can automatically identify common subexpressions (`cse` keyword).

Keeping in mind the zeroth order numerical procedures, it is apparent that the first 3 rows of $C$ are singular for $T=w$, which is the case at the sonic radius $r=1$. To handle this singularity, let us define
$$
D = (T-w) \cdot C
$$ and let $D_1$ be the first 3 rows of $D$. The only way that the derivatives at the sonic radius can be finite then is if $$
D_1(r=1)\cdot \vec{y}_1(r=1) = 0\, ,$$  
because then the right side of the matrix differential equation becomes "$0/0$" which can be finite and evaluated using L'Hopitals rule. To see if this condition is always fulfilled, $D_1(r=1)$ is calculated by inserting the zeroth order quantities at $r=1$. The result is
$$
D_{1}(r=1) =  \left[\begin{matrix}0 & \gamma \left(\frac{\chi}{2} + 2\right) & \gamma \left(2 - \chi\right) & - 2 \gamma & - 2 \gamma & - \frac{\Psi \gamma}{\lambda L{\left(1 \right)}}\\0 & \frac{\chi \left(\gamma - 1\right)}{2} + 2 \gamma - 2 & - \chi \gamma + \chi + 2 \gamma - 2 & 2 - 2 \gamma & 2 - 2 \gamma & \frac{\Psi \left(1 - \gamma\right)}{\lambda L{\left(1 \right)}}\\0 & - \frac{\chi}{2} - 2 & \chi - 2 & 2 & 2 & \frac{\Psi}{\lambda L{\left(1 \right)}}\end{matrix}\right]
$$  
where $\Psi=\Psi_\star$ and $\chi=\chi_\star$ are defined in the zeroth order. This matrix is of rank 1 and requiring $D_1(r=1)\cdot \vec{y}_1(r=1)=0$ can be used to express one unknown of $\vec{y}_1(r=1)$ in terms of the other quantities (except for $P_1(r=1)$).

Now that "$0/0$" for the first 3 rows of $C(r=1)\cdot \vec{y}_1(r=1)$ is ensured, L'Hopitals rule can be used. For convenience, it is applied to all 6 rows of the differential equation  (L'Hopitals rule can also be applied to non-singular terms) through
$$

\begin{align}

\lim_{r\rightarrow 1} \vec{y}' &= \lim_{r\rightarrow 1} \frac{1}{\frac{d}{dr}(T-w)} \frac{d}{dr} \left( D \vec{y} \right) \\

&= \lim_{r\rightarrow 1} \frac{1}{\frac{d}{dr}(T-w)} \left( D' \vec{y} + D\vec{y}' \right) \\

\Leftrightarrow \lim_{r\rightarrow 1} \left(\frac{d}{dr}(T-w)\mathbb{1}_{6x6} - D \right) \vec{y}' &= \lim_{r\rightarrow 1} D' \vec{y} \\

\Leftrightarrow \lim_{r\rightarrow 1} \vec{y}' = \lim_{r\rightarrow 1} \left(\frac{d}{dr}(T-w)\mathbb{1}_{6x6} - D \right)^{-1} D' \vec{y} 
\end{align}
$$

Defining the matrix
$$

C_\star = \lim_{r\rightarrow 1} \left(\frac{d}{dr}(T-w)\mathbb{1}_{6x6} - D \right)^{-1} D' \\

\Rightarrow \vec{y}'(r=1) = C_\star \vec{y}(r=1)

$$  
shows, that it is needed to calculate $D'(r=1)$ and $\left((T-w)\mathbb{1}_{6x6} - D \right)^{-1}|_{r=1}$. The resulting expression is too large to display here and is also for convenience exported as Python code via `lambdify`. The only new expression appearing in $C_\star$ is
$$
\Xi = 4 \lambda L^2|_{E=1} \frac{d^2 \Lambda}{dE^2}|_{E=1}
$$

# Calculating the derivatives numerically

Now that expressions for $C,C_\star$ are found and made usable as Python code returning Numpy arrays the first order differential equation can be calculated for a given $\vec{y}_1(r)$ yielding $\frac{d\vec{y}_1}{dr}$.



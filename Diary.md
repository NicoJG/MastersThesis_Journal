## 2024-01-15

- Reading [Project-Proposal](Project-Proposal.md) again
- Start meeting (explained effect a bit more, overview over project)
- Reading [](Pegourie-2007.md#Introduction)
- Group meeting

## 2024-01-16

- Read [](Pegourie-2007.md#Chapter%202)
- Started reading about deposition dynamics in Oskar's thesis [Vallhagen-Licentiate-2023](Vallhagen-Licentiate-2023.md), tomorrow I'll read [](Pegourie-2007.md#Chapter%203)

## 2024-01-17

- Listened to Konrad Steinvall's talk
- Listened to Andreas Gillgren's licentiate defense
- Reading [](Pegourie-2007.md#Chapter%203)
- read Oskar Chapter 3 to compare with Pegourie [](Vallhagen-Licentiate-2023.md#Chapter%203)

## 2024-01-18 to 2024-01-20

- Setup Mendeley as my reference manager
- Read [Senichenkov-2007](Senichenkov-2007.md)
- Attended EUROfusion runaway meeting
- Restructured note-taking workflow -> Obsidian + Zotero + GitHub (I spent way too much time on this, but it was fun)

## 2024-01-22

- Group meeting
- Supervision meeting
	- talked about planning report
	- went through Per's notes together
- tried to figure out where the initial system of equations comes from in Per's notes (Euler equations for gas dynamics?)
	- I couldn't figure out the energy balance equation

## 2024-01-23

- re-did Per's derivation with a general orthogonal basis for the $\theta$-dependence [First_Derivation_of_System_of_Eqs](First_Derivation_of_System_of_Eqs.pdf)
	- started out with general products, for example $\rho_1(r,\theta)=\rho_r(r) \cdot \rho_{\theta}(\theta)$ but this didn't lead to a separation of the $r$- and $\theta$-dependence
	- tried a cosine- and sine-Fourier series, but same problem
	- I don't understand the reason why Per's choices should work out
	- after Oskar helped me a bit I succeeded with the Legendre polynomials, but there are some inconsistencies with Per's notes. Per's equations B and C are probably wrong. And I probably have a sign error somewhere, because both $\alpha_l = \pm 2$ don't fit Per's version.

## 2024-01-24

- there is no sign mistake. (I didn't take into account that d/dx(cosx)=-sinx)
- had a meeting with Per, Tünde, Istvan, Oskar
	- showed them my derived equations
	- Oskar explained that there needs to be 2 extra equations to calculate the heat flux
	- talked about how Parks approached the topic
	- they tried to make a very rough estimate on the order of magnitude of the rocket acceleration, the result was that the stopping length is on the order of 1m times degree of asymmetry, but it was very unclear if the estimate was good, they used as heat flux into the neutral cloud the energy flux of thermal electrons in a typical tokamak plasma and it might be much lower
- derived the rocket force like Per did but with the Legendre polynomial expansion [derivation_force_from_perturbation](derivation_force_from_perturbation.pdf)
	- apparently the rocket force only depends on the l=1 mode, i.e. Per's derivation was already sufficient
	- my note-taking program Onenote decided to delete all my notes so I had to do most of it twice...

## 2024-01-25

- started to write my [Planning Report](Planning%20Report.md)
- discussed the theta dependence with Oskar and showed him my derivation
- meeting with Per, Tünde, Oskar to continue yesterdays discussion
	- the rough estimate was good enough and even though the order of magnitude can vary quite a bit, there are probably some cases where the effect is very important
	- the boundary conditions on the surface of the neutral cloud will be treated as far away (->$\infty$)
	- the problem is very much related to the solar wind problem (with constant temperature in atmosphere)

## 2024-01-26

- talked with Per about the possibility to go to Greifswald for my PhD
- finished a first draft of the [Planning Report](Planning%20Report.md)
- short meeting with Per, Tünde, Oskar to discuss how we can estimate the external heat flux
- read first part of [Parks-1978](Parks-1978.md) paper

## 2024-01-29

- Group meeting
- Supervision meeting
	- gone through planning report
	- gone through Oskars summary
	- next step is getting all equations correct, the way Oskar has them
	- then normalize the equations
- started to go through the derivation again

## 2024-01-30

- derived the set of equations again a bit more clean [full_derivation_more_clean](full_derivation_more_clean.pdf)
- compared with Oskars notes [Summary_by_Oskar_2024-01-29](Summary_by_Oskar_2024-01-29.pdf) and added a few comments where factors of $m$ are missing and one sign was wrong, but generally we got the same system of equations
- I learned a lot about multivariable calculus, directional derivatives and the Jacobian today, also how to properly linearize equations (with Taylor expansion or the usual way)

## 2024-01-31

- rederived the normalized equations for the spherical part that can be solved numerically, which are in Parks paper [normalized_zeroth_order_eqs](normalized_zeroth_order_eqs.pdf)
	- found a mistake we made when copying the dE/dr equation. He divides by $\langle \cos \theta \rangle = 1/2$ which means we should multiply by 2

## 2024-02-01

- I made myself clear what has to be done in order to solve the set of differential equations numerically
- understand Parks method
- transformed the equations to form a boundary value problem from 0 (pellet radius) to 1 (sonic radius)
- outlined all equations I will need in [numerics_outline_spherical_part](numerics_outline_spherical_part.pdf)

## 2024-02-02 and 2024-02-03

- created a GitHub repository for the code of this project and set up the Python environment https://github.com/NicoJG/MastersThesis_Code
- created functions that represent all the equations of the NGS model necessary to solve the ode system numerically
- tried to solve the boundary value problem $r \in [0,1]$ for the unknown parameters $\lambda_*$ and $\eta_*=r_p/r_*$
	- I had a lot of bugs that I tried to solve, for example "divide by zero" or "invalid value encountered" errors
	- once I got rid of the error messages, the solve_bvp function of SciPy just got stuck
	- It seems like the "shooting method" that Parks used is better than the boundary value problem
- switched the code to solve_ivp (initial value problem)
	- It is unclear how to choose and vary $\lambda_*$ exactly
	- I only got unreasonable results so far, where for example the velocity decreases with radius
	- while trying to figure out what is wrong I found some sign errors and small copying mistakes, but they did not change the numerical solution at all
- I have found out that with the events in the solve_ivp function I can stop the integration once the boundary conditions are met
- while thinking about how to vary $\lambda_*$ and later the parameters in the asymmetric part I noticed that both tensorflow and PyTorch have ode solvers and PyTorch even has the events for stopping as well. This might be useful since there it is very easy to calculate derivatives of the parameters with respect to the ode solution

## 2024-02-05

- I changed the method in [solve_ivp](https://docs.scipy.org/doc/scipy/reference/generated/scipy.integrate.solve_ivp.html) from RK45 to Radau and now it solves it much faster and does not get stuck for some values of $\lambda_*$ anymore
- the solutions still vary sometimes strongly depending on which $\lambda_*$ I choose and there is a whole range of $\lambda_*$ where the boundary conditions at $r=r_p$ are met
- Group meeting
- Supervision meeting:
	- I was asked to put the papers on GitHub for convenience. Now they are in the Literature folder
	- The $\lambda_*$ formulas given in the end of Parks Page 1738 are for $\gamma=5/3$ , keep that in mind when comparing
	- it probably works for such a large range of $\lambda_*$ values because the tolerance for the boundary condition is to large -> lower it
	- look into the step size, maybe it is too large at some places
	- the precision we need is basically determined by which ratios in the first order differential equations are important, Oskar suggested $v^2/T$ to be important
- turns out the $dv^2/dr$  singularities at $r=1$ and $r=r_p$ make the calculation very unstable
	- I wanted to detect the singularities with seeing when the denominator becomes very small, but the result I get depends on how small I let it be

## 2024-02-06 and 2024-02-07

- still trying to make the $r=1$ downward calculation more stable
- I have learned a lot from just playing around with parameters and methods:
	- the singularity at $r=1$ does not matter too much as long as Dv^2 > DT the solution does not diverge
	- if T and v^2 approach the same value at lower r, there is another singularity which makes solve_ivp stop with the message "Required step size is less than spacing between numbers.", but this is ok as this will only happen for wrong $\lambda_*$ values
	- In general to achieve the highest possible precision, solve_ivp often "fails" with the mentioned message, but this is alright because it only happens while trying to find the exact boundary
	- for correct $\lambda_*$ values, all $v^2, T, q, E$ are 0 at the pellet radius. I now doubt the stated 15% energy at the pellet surface as Parks stated it. This is only an artifact of numerics
	- I made the algorithm fail less often by setting the absolute tolerance in solve_ivp quite high for E and very small for v2,T,q, because the slope of E diverges at $r_p$ and it goes vertically to 0
- to find the correct $\lambda_*$, it seems to be best to use scipy.optimize.root together with $T(r_p) - q(r_p)$ 
	- I first used scipy.optimize.minimize with $|T(r_p)|+|q(r_p)|$ but this is not smooth when $\lambda_*$ only changes very slightly and the algorithm gets stuck in one of those local minima that might not even be near 0
	- $T(r_p) - q(r_p)$ starts negative for small $\lambda_*$ ($\approx 0.5$) and has a root where both are 0, then it is positive afterwards. It is however only increasing until around $\lambda_* \approx 1.5$, so the starting guess has to be quite low to find the correct root
	- some $\lambda_*$ scans revealed that there are some spikes where $T(r_p) - q(r_p)$ suddenly jumps from positive to negative, this happens sometimes when the $T=v^2$ singularitiy happens before the boundary conditions are fulfilled. I don't think this will ever cause trouble with finding the root though because it only happens for large $\lambda_*$
- since the triggering of the boundary condition often fails, I use the values of the solution where $|T(r_p)|+|q(r_p)|$ is the smallest as I hope this will be closest to the pellet radius the algorithm can overshoot sometimes a bit
- a scan for $\gamma = 5/3$ is somehow quite unstable even around the important $\lambda_*$ values
	- see the following images where $\lambda_*$ is quite close to each other but the shape of the solution is very different. We want something that looks like the left plot, but the right plot somehow also appears in between
	![ode0_lambda_scan_gamma_5_3_unstable](ode0_lambda_scan_gamma_5_3_unstable.png)
	- also, for high $\lambda_*$ the $T=v^2$ singularities are very close to each other and this lets the algorithm fail with an error

## 2024-02-08

- I can't reproduce this unstable behavior from yesterday
- Now it seems like the solutions slowly change with lambda (not rapidly like yesterday)
- The root finding algorithm seems to work very well. I find $\lambda_*$ values where $|T(r_p)|+|q(r_p)| \approx 10^{-10}$ and it I have tested it for $E_* = 3*10^2$ to $3*10^7$ and for $\gamma = 7/5$ and $5/3$ and the solutions all look fine
- I have implemented the integration from $r=1$ towards $r \rightarrow \infty$ 
	- I trigger convergence where $(dq/dr + dE/dr - \epsilon) = 0$  , and currently I use $\epsilon = 10^{-20}$, but it even works with $\epsilon = 10^{-200}$ and finds convergence in less than a minute
	- What this is supposed to give me is $q(r\rightarrow \infty)$ and $E(r \rightarrow \infty)$ because we need it for further calculations
- it might be a bit too precise, in the sense that it takes quite long (~1 minute) to calculate $\lambda_* , r_p, E(\infty)$ for a given $E_*,\gamma$ 
- started to derive the normalized set of equations for the first order

## 2024-02-09

- Started to write a concise summary of the project in [Project Summary](Project%20Summary.md), where I keep track of what parts of the project are done and where I am working on.
- Division seminar on "Water drop as an analogy for an atom"
- Started to write code to sweep over multiple $E_\star$ and $\gamma$ values, but somehow the algorithm fails sometimes and I cant figure out why

# 2024-02-10 and 2024-02-11

- I finished recreating the figures in [Parks-1978](Parks-1978.md).
- I started to learn to use [seaborn](https://seaborn.pydata.org/) since this is probably a nicer plotting experience than just using matplotlib
- Found some bugs and inconsistencies on the way:
	- The energy attenuation cross-sections $L(E)$ and $\sigma_T(E)$ now have a (hopefully) reasonable behavior for low energies based on figures in the older Parks paper from 1977. $L(E)$ is only fitted for $E>20 \mathrm{eV}$. I set $L(E<20\mathrm{eV} = 0$. $\sigma_T(E)$ diverges for small energies, therefore I just used the formula in Parks' paper $\sigma_T(E<100\mathrm{eV}) \propto 1/E$.
	- The singularity at $r=1$ is not stable for $E_\star \approx 100\mathrm{eV}$ since $d\Lambda/dE$ has a kink there
	- The pellet radius is now defined as the maximum of $dE/dr$, since it diverges there. I choose this since it works the best and the solve_ivp function sometimes overshoots this point.
	- The events in the solve_ivp function now trigger when any one quantity ($v^2,T,q,E$) passes 0 (negative values don't make sense) or if $v^2=T$ since the singularity would stop the integration either way.
	- In the root finding function to find $\lambda_\star$, I set `tol=1e-5, options={"eps":1e-4}` , which fixed that it took too large steps in the beginning landing at negative $\lambda_\star$ values.
	- The tolerances for the solve_ivp from r=1 down are now ` atol=[1e-10, 1e-10, 1e-10, 1e-3], rtol=1e-7,` This seems to be stable and very precise, and still not too slow
- I realized that we will only have the zeroth order solution at some $r$ values which are almost randomly distributed. For the first order we probably need to do interpolation

## 2024-02-12

- Group meeting
- Supervision meeting:
	-  I showed the recreated figures from Parks paper
	- Oskar talked about one possible way to solve the 1st order ode numerically
	- We have boundary conditions at 0, but potentially diverging derivatives and the zeroth order is not very well defined at the pellet radius
	- So we basically want to treat it as a boundary value problem. We start somewhere close to the pellet radius with setting the quantities to some values, which we then vary until all boundary conditions are fulfilled.
	- This sounds like a problem that's too complex, but maybe it will work
	- I will have to stare at the normalized system of equations a bit to see if there are some other smart ways to solve the system
	- Maybe we could even normalize the asymmetric values to their values at the sonic radius so that we know they are 1 there
- I talked with Oskar afterwards, normalizing to the sonic radius is probably not worthwhile since we have no physics relations that could reduce the number of unknown parameters anyway
- I checked Oskars normalized set of equations (see [normalized_first_order_eqs](HandwrittenNotes/normalized_first_order_eqs.pdf)) and get exactly the same (I only found on factor 2 that was too much in Oskars equations) 
- I thought if we can somehow do some clever stuff to either start at the pellet radius by finding ways around the diverging derivatives or start somewhere else but in some smart way. I did not come up with anything yet. I might just try it numerically the brute force way with 6 unknown parameters.

## 2024-02-13

- Still no clever ideas
- I checked the matrix equation form Oskar has brought the first order ode in. I get almost the same and he probably just had some small mistakes.
- Tried to get a simpler matrix form by plugging everything into Mathematica. It spits out something but I don't know if I did everything correct and if the result is correct. 
- I want to try out sympy. Maybe this yields the same or other results than Mathematica.

## 2024-02-14

- Used sympy to calculate the inverse matrix and the matrix product. Out comes a 6x6 matrix with very large expressions in the upper left 3x3 block.
- I noticed that the facto $T-v^2$ appears infront of the first 3 rows of this matrix
- After talking with Oskar we think it might be possible to use this singularity to eliminate 3 of the first order quantities since the derivatives should be finite at the sonic radius. Tomorrow I will try to use sympy to see if the system of equations for the singularity is useful somehow
- Otherwise I will use the sympy expression for the matrix to calculate the matrix in every step
- Had a meeting with Josefine Proll, about a potential PhD in Greifswald
	- She will connect theory and experiment
	- I have to write her a summary of what my skills are, what I expect to learn and what skills I want to use

## 2024-02-15

- Using sympy, I rederived the singularity of the zeroth order ode (using L'Hopitals rule), to check it and to see how I can do it in sympy
- Using sympy, I derived the first 3 rows of C at the sonic radius
	- this matrix has rank 1. This means that applied to the first order quantities and setting it to equal 0 (since $T-v^2$ is 0 and the derivatives should be finite), all 3 equations are linearly dependent
	- this would allow us to eliminate one of the unknown quantities at the sonic radius
	- however, because of this singularity we would need to find an expression for the derivatives at the sonic radius using L'Hoptials rule
- Using sympy, I tried to apply L'Hopitals rule on the first 3 rows of C. I am now unsure if I did it correctly.
	- Lets call the first 3 rows of C : $C_1$ 
	- $C_1$ has a common prefactor $1/(T-v^2)$
	- I removed this prefactor and then calculated the derivative at the sonic radius
	- Afterwards I also calculated the derivative of $T-v^2$ at the sonic radius and divided the matrix by it
	- I did NOT apply the matrix on any vector and I think this is where I did it wrong
	- This way I required that each component of $C_1$ must be finite and that each component times $T-v^2$ is equal to 0
	- However, the matrix applied to the first order quantities vector must be finite not each matrix element itself
- I think the singularity is not possible to solve using L'Hopitals rule, since we don't know the values at the sonic radius. Or at least it might be to complex to do it this way
- I might just start bruteforcing the solution to the first order ode. Even though it might be possible to eliminate one unknown parameter, this reduction of 1 dimension might not be worth figuring out a way to deal with the singularity
- The singularity will obviously still be there, but I might just hope that we don't need to evaluate the derivatives exactly at the sonic radius 
## 2024-02-19 and 2024-02-20

- Supervision meeting gave me some ideas how to solve the first order system numerically
	- I could try to use gradient based minimization methods
	- I should calculate the cost function not exactly at the pellet radius, slightly besides it should be fine as a first estimate
- Now I derived the derivatives at r=1 and a relation to calculate E1 at r=1
- I should really update this Journal a bit more
	- I should give details on how I solved the zeroth order numerically
	- I should give details on the sympy derivations I did for the first order matrix C, it's L'Hopitals rule and the relation to eliminate E1
	- Once I have found methods that work or do not work I should outline them here
- I have discussed the boundary conditions with Oskar
	- we are pretty sure the singularity relation got rid of one boundary condition but we don't know which
	- maybe U1 and V1 actually don't have to go to 0 at the pellet radius
- I have managed to find a solution using root, that seems pretty good, however, V1 does not go to 0
	- I can't replicate finding this solution from starting guesses
	- It might be just a local minimum
	- scipy.minimize does not find anything to converge to
- since I now check also the boundary conditions at infinity, it might be better to not eliminate E1 at the sonic radius, since we have a quite good starting guess for that

## 2024-02-21 and 2024-02-22

- I tested around a lot to get a feel for the minimization process and what accuracy is achievable
- It seems that V1(r_p)=0 is the boundary condition which when enforced also enforces U1(r_p)=0, P1(inf)=0 is always the case
- A lot of what I did was to see how close the first order pellet radius estimate can get to the zeroth order r_p. 
	- It seems to be the best strategy to use method="hybr" with, which can only handle 5 roots, first to get to some solution that is within 1e-4 to the r_p
	- Then I use method="lm", which is least squares and can handle any number of roots, to go as close as 1e-7 to r_p and enfoce that all boundary conditions go to 0
	- It might work to also have the closeness to r_p as a condition with some weight
	- With this method I can get all boundary conditions fulfilled with at least 1e-5 accuracy
	- I tested it for E_rel/q_rel = +-1
	- this method seems to take somewhere between 3 and 10 minutes
## 2024-02-23

- The method seems to be reliable, I have tested it for the values $E_{rel}/q_{rel} = -2,-1.5,-1,-0.5,-0.1,0.1,0.5,1,2$
- the first order solution shows some very interesting features
	- around -1, V1 is just 0, going to higher fractions it becomes negative and lower fractions it becomes positive
	- this causes P1 to become negative for fractions lower than ~(-1) and thus a force in the opposite direction than expected appears
	- for fractions above ~(+0.5), T1, U1 and V1 are all negative, which seems a bit counter intuitive, since higher incoming heat flux and higher electron energy at $\theta=0$ suddenly means the temperature is higher at $\theta=\pi$ . 
		- Maybe this can be explained by V1 being quite large (but negative) and thus the material is accumulated more towards the low flux side
- I have spent some time to make my code run faster.
	- Oskar gave me the tip to precompute the energy attenuation cross sections. This made me find solutions in 2 instead of 5 minutes.
	- Through profiling I noticed that the interpolation and the calculation of matrix C are the most demanding and I made them a bit faster
- I started to organize my code better
- I have to continue organizing my code and I have to write and outline the procedures I use here in this journal

## 2024-02-29

- today I summarized how to calculate the pellet rocket force after solving the first order differential equations. This is in the handwritten notes [force_from_normalized_1st_order](HandwrittenNotes/force_from_normalized_1st_order.pdf)
- this includes deriving the force in terms of the normalized first order quantities
- as well as deriving an expression for $p_\star$ , which Parks only gives already simplified
- I also put at the end a list of all physical parameters our model depends on
- To see if we can just simplify the dimensionless prefactor in $p_\star$ I also plotted it (see [p_star_dependence_on_E_bg](Images/p_star_dependence_on_E_bg.png))
	- it seems to only depend very weakly on $E_\star$ and $\gamma$ a value of 0.04 might be sufficiently accurate
	- the factor $E_{bg}/\Lambda_\star^{1/3}$ on the other hand is highly nonlinear in $E_{bg}$ 
## 2024-01-15

- Reading [[Project-Proposal]] again
- Start meeting (explained effect a bit more, overview over project)
- Reading [[Pegourie-2007#Introduction]]
- Group meeting

## 2024-01-16

- Read [[Pegourie-2007#Chapter 2]]
- Started reading about deposition dynamics in Oskar's thesis [[Vallhagen-Licentiate-2023]], tomorrow I'll read [[Pegourie-2007#Chapter 3]]

## 2024-01-17

- Listened to Konrad Steinvall's talk
- Listened to Andreas Gillgren's licentiate defense
- Reading [[Pegourie-2007#Chapter 3]]
- read Oskar Chapter 3 to compare with Pegourie [[Vallhagen-Licentiate-2023#Chapter 3]]

## 2024-01-18 to 2024-01-20

- Setup Mendeley as my reference manager
- Read [[Senichenkov-2007]]
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

- re-did Per's derivation with a general orthogonal basis for the $\theta$-dependence [[First_Derivation_of_System_of_Eqs.pdf|First_Derivation_of_System_of_Eqs]]
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
- derived the rocket force like Per did but with the Legendre polynomial expansion [[derivation_force_from_perturbation.pdf]]
	- apparently the rocket force only depends on the l=1 mode, i.e. Per's derivation was already sufficient
	- my note-taking program Onenote decided to delete all my notes so I had to do most of it twice...

## 2024-01-25

- started to write my [[Planning Report]]
- discussed the theta dependence with Oskar and showed him my derivation
- meeting with Per, Tünde, Oskar to continue yesterdays discussion
	- the rough estimate was good enough and even though the order of magnitude can vary quite a bit, there are probably some cases where the effect is very important
	- the boundary conditions on the surface of the neutral cloud will be treated as far away (->$\infty$)
	- the problem is very much related to the solar wind problem (with constant temperature in atmosphere)

## 2024-01-26

- talked with Per about the possibility to go to Greifswald for my PhD
- finished a first draft of the [[Planning Report]]
- short meeting with Per, Tünde, Oskar to discuss how we can estimate the external heat flux
- read first part of [[Parks-1978]] paper

## 2024-01-29

- Group meeting
- Supervision meeting
	- gone through planning report
	- gone through Oskars summary
	- next step is getting all equations correct, the way Oskar has them
	- then normalize the equations
- started to go through the derivation again

## 2024-01-30

- derived the set of equations again a bit more clean [[full_derivation_more_clean.pdf]]
- compared with Oskars notes [[Summary_by_Oskar_2024-01-29.pdf]] and added a few comments where factors of $m$ are missing and one sign was wrong, but generally we got the same system of equations
- I learned a lot about multivariable calculus, directional derivatives and the Jacobian today, also how to properly linearize equations (with Taylor expansion or the usual way)

## 2024-01-31

- rederived the normalized equations for the spherical part that can be solved numerically, which are in Parks paper [[normalized_zeroth_order_eqs.pdf]]
	- found a mistake we made when copying the dE/dr equation. He divides by $\langle \cos \theta \rangle = 1/2$ which means we should multiply by 2

## 2024-02-01

- I made myself clear what has to be done in order to solve the set of differential equations numerically
- understand Parks method
- transformed the equations to form a boundary value problem from 0 (pellet radius) to 1 (sonic radius)
- outlined all equations I will need in [[numerics_outline_spherical_part.pdf]]

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
	![[ode0_lambda_scan_gamma_5_3_unstable.png]]
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

- Started to write a concise summary of the project in [[Project Summary]], where I keep track of what parts of the project are done and where I am working on.
- Division seminar on "Water drop as an analogy for an atom"
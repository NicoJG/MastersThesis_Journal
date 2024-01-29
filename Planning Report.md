# Introduction

## Background

The need for energy and the importance of both climate change and running out of fossil fuel is currently growing very rapidly. One possible solution, addressing all three problems, is the development of power plants based on the concept of nuclear fusion. Currently, the most promising technique to harness this energy is magnetic confinement fusion and more specifically tokamak or stellarator designs. Scientists and engineers are hoping to achieve the first demonstration of a net energy gain in a tokamak with the International Thermonuclear Experimental Reactor (ITER) by the middle of the 21st century. The socio-economical and ecological impact of such an achievement would be immense. However, there are still challenges to overcome even on the physics theory side.

One of the big challenges is ensuring the stability of the magnetically confined plasma and finding effective procedures for mitigating so-called disruption events, where instabilities in the plasma grow large enough to hit the reactor walls. Disruptions lead to both loss of plasma confinement and potential damage to vital reactor components. To have a stable plasma, reliable control over the density profile and the temperature of the plasma must be ensured. Both of those goals can be achieved by shooting small pellets of frozen fuel (Deuterium and Tritium) into the hot plasma. The fuelling efficiency depends largely on the penetration depth of the pellet into the plasma. Because of limited space in the center of the toroidal shape of the reactor, the pellet injection is preferably done from the outside, i.e. from the low magnetic field side of the plasma. However, experimental observations show that a pellet injection from the high-field side leads to longer flight paths before the pellet is stopped or fully ablated \[Pégourié 2007\].  The full dynamics of what happens to the pellet and its ablated material are hard to measure experimentally because of the short timescale of around 1 ms of the ablation and homogenization process.  And the complex dynamics inside the plasma further complicate the understanding of the process through physics theory.

One of the phenomena believed to be significant for the pellet trajectory is the so-called rocket acceleration effect.  Asymmetric heating of the neutral gas cloud, which shields the pellet, leads to a higher pressure and higher ablation rate on the high-heat-flux side of the pellet. Consequently, a rocket force is exerted on the pellet, which might affect the pellet's trajectory. An expected asymmetry from the current driven along the toroidal direction would lead to a deflection of the pellet, and an expected asymmetry along the magnetic field gradient would lead to an acceleration of the pellet towards the low-field side. Neither the exact dynamics nor the magnitude of this effect are studied in detail thus far, and it is unclear if this effect might complicate the pellet injection schemes envisioned for ITER.

## Aim

With this master's thesis project, a first modelling of the pellet rocket effect is envisioned.  A starting point is the Neutral Gas Shielding model \[Parks 1978\], which needs to be extended to include asymmetric steady-state dynamics (along one axis) as a perturbation to the spherically symmetric model. In addition to the model-specific system of equations, appropriate boundary conditions describing the external heat flow through the partly ionized cloud into the neutral gas cloud need to be found. After making the equations computationally tractable, numerical methods to solve differential equations need to be applied. The resulting numerical fluid quantities need then to be connected to a net force exerted on the pellet. This semi-analytical model needs to be presented in a usable form, and the magnitude of the acceleration needs to be discussed in the context of other pellet injection phenomena.

## Limitations

The full dynamics cannot be taken into account. Especially for the ionized cloud and beyond, estimates have to be made here. Additionally, the solid pellet will only be treated as a boundary condition and as a source of particles. The asymmetry considered in this model is not fully general but rotationally symmetric around one axis. Implementing and testing this model in a simulation suit for fusion plasma and pellet injections is outside the scope of this project. Furthermore, the lack of experimental data and the time limitations of this project make it unfeasible to validate the model predictions. 


## Specific questions to be answered

- How large is the rocket acceleration in a typical pellet injection scenario, and how much does it affect the penetration depth of the pellet?
- How does the effect depend on the pellet size, composition and background plasma parameters?
- Is this effect relevant in reactor-grade conditions?

# Methodology

Roughly separate but overlapping are the following steps of this project

## Phase 1: Literature review

- Read relevant sections in the review paper on pellet injection \[Pégourié 2007\]
- Read previous studies on the rocket effect (\[Senichenkov 2007\] and \[Szepesi 2009\])
- Read about the Neutral Gas Shielding model \[Parks 2009\]
- Read relevant other literature that helps further understanding of the topic

## Phase 2: Analytical modelling

- Derive a set of self-consistent equations that describe the steady-state dynamics inside the neutral cloud
- Derive the force on the pellet from the neutral cloud dynamics
- Reduce the complexity of the set of equations as much as possible
- Normalize the model and prepare it for numerical calculations
- Find fitting boundary conditions such as the incoming heat flux

## Phase 3: Numerical calculations

- Estimate realistic parameter ranges for all input parameters to the model
- Implement the spherically symmetric part through the Neutral Gas Shielding model
- Choose and implement a numerical method to solve the system of equations
- Calculate the rocket acceleration from the model output
- Compare solutions using different scenarios/input parameters

## Phase 4: Writing the thesis

- Comprehensively summarize the context of the thesis, i.e. magnetic confinement fusion and pellet injection
- Present the derivation of the analytical model
- Present the methodology and results of the numerical calculations
- Discuss the implications of the study

# Schedule

![[Schedule.png.png]]
# References

- Parks, P. B., and R. J. Turnbull. 1978. “Effect of Transonic Flow in the Ablation Cloud on the Lifetime of a Solid Hydrogen Pellet in a Plasma.” The Physics of Fluids 21 (10): 1735–41. https://doi.org/10.1063/1.862088.
- Pégourié, B. 2007. “Review: Pellet Injection Experiments and Modelling.” Plasma Physics and Controlled Fusion 49 (8): R87. https://doi.org/10.1088/0741-3335/49/8/R01.
- Senichenkov, I, V Rozhansky, and P Gusakov. 2007. “The Pellet Rocket Acceleration Caused by B-Induced Drift.” Europhysics Conference Abstracts 31F (34th EPS Conference on Plasma Physics). http://ocs.ciemat.es/EPS2007/html/contents.htm.
- Szepesi, T., S. Kálvin, G. Kocsis, P. T. Lang, and I. Senichenkov. 2009. “Comparison of Pellet Acceleration Model Results to Experimentally Observed Penetration Depths.” Journal of Nuclear Materials 390–391 (1): 507–10. https://doi.org/10.1016/J.JNUCMAT.2009.01.063.

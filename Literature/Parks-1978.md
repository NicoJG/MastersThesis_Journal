Parks, P. B., and R. J. Turnbull. “Effect of Transonic Flow in the Ablation Cloud on the Lifetime of a Solid Hydrogen Pellet in a Plasma.” The Physics of Fluids 21, no. 10 (October 1, 1978): 1735–41. https://doi.org/10.1063/1.862088.
# Introduction

- Studies behavior of the transonic flow when injecting a pellet and uses it to calculate ablation rates
- Improvement of previous work (not repeated in this paper)
- Electrons heat pellet surface, which then emits neutral molecules
- "electrons deposit almost all of their energy in the ablatant through elastic backscattering and inelastic processes" (if $\rho_0 > 10^{12} cm^{-3}$) [Page 2](zotero://open-pdf/library/items/WGAA9JB2?page=2&annotation=LFUM9PTT)
- pellet is almost fully shielded (energy flux) and ablatant dynamics are more important than phase transition at the pellet surface
- main dynamics are heating and radial expansion
- ablatant is continuously accelerated and reaches supersonic speeds at the sonic radius
- shock front forms far away and has no effect on shielding ("nozzling effect")
- ablation process is quasi-steady state because pellet lifetime $~10^{-4} s$ and the ablation cloud is form after $~10^{-7} s$ (pellet radius/ablation cloud velocity) 
- earlier model didn't calculate energy flux for each point, but resulted in similar scaling laws
- assumed at sonic radius $T_* \approx 1-2 eV$ and $\rho_* \approx 10^{19}-10^{20} cm^{-3}$ [Page 2](zotero://open-pdf/library/items/WGAA9JB2?page=2&annotation=S87XLH37)
- **Any asterisk (\*) in this paper means evaluated at the sonic transition point**
- heat conduction and viscocity flow can be ignored
- model neglects ionization and dissociation (limitation of the model)
- a "return-current" is established in the neutral cloud, which could balance incident current and give rise to Ohmic heating
	- electric field and conductivity are estimated at the sonic radius
	- $r_*$ is less than two pellet radii
	- local thermal equilibrium is valid because atomic collisions happens much faster than hydrodynamics
	- voltage is estimated to be $~60 V$, which is negligible and this "return current" can be ignored
	- might be stronger at pellet surface, but is ignored here
- electrons trajectory and magnetic field might be altered because of finite conductivity in the neutral cloud, this might be of importance for pellet larger than $0.1 cm$ and plasma densities larger than $10^{14} cm^{-3}$ [Page 3](zotero://open-pdf/library/items/WGAA9JB2?page=3&annotation=QR9TVEY6)
- states that ionization  occurs at large enough radius so that it does not affect the shielding (this proved to be a wrong assumption)

# Development of the equations

- inelastic processes and elastic scattering of incident electrons leads to heat absorption into the ablation cloud
- assuming Maxwell-Boltzmann statistics for plasma electrons leads to unattenuated electron energy flux [Page 4](zotero://open-pdf/library/items/WGAA9JB2?page=4&annotation=QKWXFTW8) $$q_0 = (n_{e0}v_{e0}/4)E_0$$ 
- the energy loss per unit path length of electrons is described by the loss function (or stopping cross section) $$L(E) = -n^{-1} \frac{dE}{dx}$$
	- Parks uses the empirical electron loss function in molecular hydrogen $H_2$ fitted by [Miles et al. 1972](https://pubs.aip.org/jap/article/43/2/678/502483/Electron-Impact-Cross-Sections-and-Energy)  , which is valid for electron energies in the range 20-1000 eV [Miles Page 684](zotero://open-pdf/library/items/V76Y8JEF?page=8&annotation=DXAUVPCS) (higher energies are extrapolation)
	- It might be worthwhile to search for newer estimates of the electron energy loss function, for example: [Aker 2021](https://link.springer.com/article/10.1140/epjc/s10052-021-09325-z) or [Abdurashitov 2017](https://link.springer.com/article/10.1134/S1547477117060024)
	- Parks version of this: [Page 4](zotero://open-pdf/library/items/WGAA9JB2?page=4&annotation=W4UHUT7D)$$\frac{dE}{dr} = \rho(r) L(E)/m \langle cos \theta \rangle$$
	- assumes a mono-energetic beam along magnetic field line directly along the cloud radius and averages over pitch angles $\langle cos \theta \rangle \approx 1/2$  
- 
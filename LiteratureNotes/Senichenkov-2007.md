Senichenkov, I, V Rozhansky, and P Gusakov. “The Pellet Rocket Acceleration Caused by B-Induced Drift.” _Europhysics Conference Abstracts_ 31F, no. 34th EPS Conference on Plasma Physics (2007). [http://ocs.ciemat.es/EPS2007/html/contents.htm](http://ocs.ciemat.es/EPS2007/html/contents.htm).

- pellet acceleration observed in ASDEX-Upgrade (10⁵ m/s²) [Page 1](zotero://open-pdf/library/items/Q4BXHC3C?page=1&annotation=HETBB5PR)
- assume spherical solid pellet (index 0), spherical neutral cloud (index N), elongated ionized/plasma cloud (index p) and background plasma (index bg)
- assume both expand with speed of sound $c_s=T/M_i$ ($c_{sN}$,$c_{sp}$)
- expansion rate + particle conservation $\dot{N} = n_N 4 \pi r_\text{0}^2 c_{sN}$ (assuming $r_N \approx r_0$) [Page 1](zotero://open-pdf/library/items/Q4BXHC3C?page=1&annotation=7Q8A759I)
- expansion in tube along field lines (in 2 directions) + neutral cloud constant -> $\dot{N} = n_p 2 \pi r_{p,\bot}^2 c_{sp}$
- energy balance in neutral cloud $\beta \frac{n_{bg} T_{bg}^{3/2}}{r_0 m_e^{1/2}} \approx \frac{n_N T_N c_{sN}}{r_0}$
	- Error in exponent (3/2 instead of 1) of $T_N$, equation (2) in paper ?
	- $\beta$ is fraction of background electron flux reaching the neutral gas cloud
    - from incoming energy flux $q_\text{inc} \propto \frac{n_{bg} T_{bg}^{3/2}}{m_e^{1/2}}$ ?
	- and heat released in cloud primarily into expansion -> $\nabla \vec{q}_\text{inc} = q_\text{inc}/r_0 = n_N T_N \nabla \vec{c}_{sN} = n_N T_N \frac{c_{sN}}{r_0}$ ?
-  energy balance in plasma cloud $(1 - \beta) \frac{n_{bg} T_{bg}^{3/2}}{r_{p,\parallel} m_e^{1/2}} \approx \frac{n_p (T_p + E_i) c_{sp}}{r_{p,\parallel}}$
    - $E_i$ is ionization energy
- $\alpha^4 T_{bg}^2 = 4 \pi Z(Z+1) e^4 \Lambda (n_N r_p + n_p r_{p,\parallel})$
    - $\Lambda = \lambda_D / b_\text{min}$ is Coulomb Logarithm (quantifies dominance of small-angle scattering in weakly coupled plasma), $Z$ is atom number of pellet atoms
	-  $V^\infty = \alpha V_T = \alpha \sqrt{2T_{bg}/m_e}$ and $V_{bg} = 0$
- I realized that I'm looking into this paper in too much detail and will now just skim through it
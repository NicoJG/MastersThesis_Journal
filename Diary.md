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
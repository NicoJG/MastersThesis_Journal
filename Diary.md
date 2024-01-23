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
---
title: "Normes et convergence"
tags:
  - mva
  - pre-rentree
  - analyse-fonctionnelle
  - normes
  - convergence
---

# Normes et convergence

[[00-socle-analyse-fonctionnelle|← Retour au socle]] · [[poly-analyse-fonctionnelle|Poly principal]]

Une norme associe à chaque vecteur ou fonction un nombre positif qui mesure sa taille. Elle doit vérifier trois intuitions :

- une taille nulle signifie que l'objet est nul ;
- changer le signe ou multiplier par $3$ change la taille de façon prévisible ;
- la taille d'une somme ne dépasse pas la somme des tailles.

Pour une fonction, il n'existe pas une unique bonne définition de « taille ». Tout dépend de ce que l'on veut contrôler.

Sur $[0,1]$, les normes les plus importantes sont :

$$
\|f\|_{L^1}=\int_0^1 |f(x)|\,dx,
$$

$$
\|f\|_{L^2}=\left(\int_0^1 |f(x)|^2\,dx\right)^{1/2},
$$

et

$$
\|f\|_{L^\infty}=\text{la plus grande valeur essentielle de }|f|.
$$

On peut les lire ainsi :

- $L^1$ mesure la **quantité totale** : une aire sous $|f|$ ;
- $L^2$ mesure une **énergie** : les grandes valeurs comptent davantage parce qu'elles sont mises au carré ;
- $L^\infty$ mesure le **pire cas** : la hauteur maximale, en ignorant les exceptions de mesure nulle.

> [!example] Un pic fin et haut
> Posons $h(x)=10$ sur $[0,0.01]$ et $h(x)=0$ ailleurs. Ses trois tailles sont :

$$
\|h\|_{L^1}=0.1,\qquad
\|h\|_{L^2}=1,\qquad
\|h\|_{L^\infty}=10.
$$

La même fonction est donc « petite » si l'on regarde sa masse totale, beaucoup moins petite si l'on regarde son énergie, et très grande si l'on exige qu'elle ne dépasse jamais une certaine hauteur.

En dimension finie, par exemple dans $\mathbb R^2$, on peut mesurer un vecteur avec la distance euclidienne, la somme des valeurs absolues ou le maximum de ses coordonnées. Les nombres obtenus sont différents, mais une suite converge pour l'une de ces normes si et seulement si elle converge pour les autres. Elles donnent donc la même idée de « se rapprocher ».

En dimension infinie, cette équivalence disparaît. Les normes choisies ne disent plus la même chose.

> [!example] Converger en $L^1$ sans converger en $L^2$
> Posons la suite de pics
>
> $$
> f_n(x)=\sqrt n\,\mathbf 1_{[0,1/n]}(x).
> $$
>
> Chaque $f_n$ vaut $\sqrt n$ sur un intervalle de longueur $1/n$, et $0$ ailleurs. À mesure que $n$ grandit, le pic devient donc **plus étroit**, mais aussi **plus haut**.
>
> Pour mesurer sa masse totale, on calcule sa norme $L^1$ :
>
> $$
> \|f_n\|_{L^1}
> = \int_0^{1/n}\sqrt n\,dx
> = \sqrt n\times\frac1n
> = \frac1{\sqrt n}
> \longrightarrow 0.
> $$
>
> La masse du pic disparaît : $f_n$ converge donc vers $0$ dans $L^1$.
>
> En revanche, la norme $L^2$ donne davantage de poids aux grandes amplitudes :
>
> $$
> \|f_n\|_{L^2}
> = \left(\int_0^{1/n}(\sqrt n)^2\,dx\right)^{1/2}
> = \left(n\times\frac1n\right)^{1/2}
> = 1.
> $$
>
> L'énergie du pic ne diminue pas. Ainsi, $f_n$ **ne converge pas** vers $0$ dans $L^2$, malgré le fait que son support se rétrécisse.
>
> Fixons un point $x>0$. Comme $1/n$ devient arbitrairement petit, on peut choisir un rang $N$ tel que
>
> $$
> \frac1N<x.
> $$
>
> Alors, pour tout $n\ge N$, on a $1/n\le 1/N<x$. Le point $x$ n'appartient donc plus à l'intervalle $[0,1/n]$, qui porte le pic. L'indicatrice vaut alors $0$, et donc $f_n(x)=0$. Autrement dit, quel que soit le point strictement positif où l'on se place, le support finit par se rétrécir avant de l'atteindre.
>
> Il y a une unique exception : en $x=0$, on a $0\in[0,1/n]$ pour tout $n$, donc $f_n(0)=\sqrt n$, qui ne tend pas vers $0$. Comme l'ensemble $\{0\}$ est de mesure nulle, cette exception ne compte pas pour la convergence presque partout. La suite tend donc vers $0$ presque partout, mais pas en tout point. Et même une convergence presque partout ne suffit pas à décider d'une convergence dans une norme donnée.
>
> Plus généralement, pour $1\le p<\infty$,
>
> $$
> \|f_n\|_{L^p}=n^{\,1/2-1/p}.
> $$
>
> Le même pic tend vers $0$ dans $L^p$ si $p<2$, garde une norme constante dans $L^2$, et sa norme explose si $p>2$. Les grandes valeurs sont pénalisées de plus en plus sévèrement lorsque $p$ augmente. Dire « une suite de fonctions converge » n'a donc pas de sens complet tant qu'on n'a pas précisé la norme.



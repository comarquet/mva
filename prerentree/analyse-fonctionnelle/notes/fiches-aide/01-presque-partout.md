---
title: "Presque partout"
tags:
  - mva
  - pre-rentree
  - analyse-fonctionnelle
  - mesure
  - integration
---

# Presque partout

[[00-socle-analyse-fonctionnelle|← Retour au socle]] · [[poly-analyse-fonctionnelle|Poly principal]]

En intégration de Lebesgue, deux fonctions qui ne diffèrent que sur un ensemble de mesure nulle sont considérées comme identiques. Un ensemble de mesure nulle est, intuitivement, un ensemble trop petit pour avoir une longueur, une aire ou un volume.

Le cas le plus simple est celui d'un point isolé. Sur l'intervalle $[0,1]$, considérons

$$
f(x)=0
$$

et la fonction $g$ qui vaut aussi $0$ partout, sauf en $x=1/2$, où elle vaut $10^6$. Les graphes diffèrent au point $1/2$, mais

$$
\int_0^1 f(x)\,dx=\int_0^1 g(x)\,dx=0.
$$

Le pic n'a aucune largeur : il ne produit donc aucune aire. Pour une intégrale, pour les normes $L^p$ et pour la plupart des résultats du cours, $f$ et $g$ racontent exactement la même histoire.

> [!example] Un exemple plus surprenant
> Les nombres rationnels sont très nombreux dans $[0,1]$ : il y en a entre n'importe quels deux nombres réels. Pourtant, ils forment un ensemble de mesure nulle. La fonction qui vaut $1$ sur les rationnels et $0$ sur les irrationnels est donc égale à $0$ presque partout, au sens de Lebesgue. Son intégrale est $0$.

Cette convention ne signifie pas que les valeurs ponctuelles sont toujours inutiles. Elles comptent, par exemple, si l'on impose une condition $f(0)=1$ ou si l'on étudie la continuité. Mais dès que l'on mesure une fonction avec une intégrale, les exceptions sur un ensemble négligeable peuvent être oubliées.

> [!tip] Réflexe
> « $f=g$ presque partout » signifie : il peut exister quelques exceptions, mais elles ne changent aucune quantité calculée par intégration.



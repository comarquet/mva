---
title: "Poly intuitif — Analyse fonctionnelle"
aliases:
  - Analyse fonctionnelle — notions essentielles
tags:
  - mva
  - pre-rentree
  - analyse-fonctionnelle
  - poly
source: "[[../sources/analyse-fonctionnelle-cours.pdf|Support de cours]]"
---

# Analyse fonctionnelle — poly intuitif

> [!abstract] Fil conducteur
> L'analyse fonctionnelle étudie des **espaces de fonctions** comme on étudie des espaces de vecteurs. L'idée décisive est de choisir une notion de taille (une norme) et, lorsque c'est possible, une notion d'angle (un produit scalaire). On peut alors parler de limites, d'approximation, de projection, de dérivée faible et de minimisation d'énergie.

Ce poly reprend les notions du support de manière volontairement intuitive. Les formules sont là pour fixer les idées ; le point important est surtout de savoir **ce qu'elles disent** et **quand les utiliser**.

## 0. Le socle à avoir en tête

Avant de commencer, quelques réflexes reviennent partout.

- Deux fonctions qui ne diffèrent que sur un ensemble de mesure nulle sont considérées comme la même fonction. Modifier une fonction en un point isolé ne change donc ni ses intégrales ni son appartenance à un espace $L^p$.
- Une **norme** mesure la taille d'un vecteur ou d'une fonction. En dimension finie, toutes les normes raisonnables donnent la même notion de convergence. En dimension infinie, ce n'est plus vrai : choisir $L^1$, $L^2$ ou $L^\infty$ change réellement le problème.
- Un espace est **complet** si toute suite qui devrait converger du point de vue de sa norme converge effectivement dans cet espace. C'est la garantie que les procédés d'approximation ne « sortent pas » de l'espace considéré.
- Une fonction est **dense** dans un espace lorsqu'on peut approcher aussi bien qu'on veut n'importe quel élément de l'espace à partir de fonctions de cette famille. Les fonctions lisses à support compact jouent très souvent ce rôle de briques élémentaires.

> [!example] Une image utile
> Dans $\mathbb R^n$, les vecteurs sont des listes de nombres. En analyse fonctionnelle, un « vecteur » peut être une fonction $f(x)$, donc une infinité de valeurs. Une norme résume alors la taille de toutes ces valeurs en un seul nombre.

---

## 1. Intégrer et passer à la limite

### 1.1. Intégrabilité et l'espace $L^1$

Une fonction est dans $L^1(\Omega)$ lorsque son aire totale en valeur absolue est finie :

$$
\int_\Omega |f(x)|\,dx < \infty.
$$

La norme $\|f\|_{L^1}=\int |f|$ mesure donc une **masse totale**. Une fonction peut prendre des valeurs négatives, mais l'intégrabilité demande que ses parties positive et négative ne cachent pas une masse infinie.

> [!example] Deux comportements opposés
> Sur $(0,1)$, la fonction $x\mapsto x^{-1/2}$ est dans $L^1$ : sa singularité près de zéro est assez douce. En revanche, $x\mapsto 1/x$ n'y est pas intégrable : l'aire accumulée près de zéro est infinie.

### 1.2. Pourquoi les théorèmes de convergence sont nécessaires

On aimerait souvent écrire

$$
\lim_{n\to\infty}\int f_n = \int \lim_{n\to\infty} f_n.
$$

Ce passage de limite sous l'intégrale est **faux en général**. Les théorèmes suivants donnent des conditions simples qui l'autorisent.

#### Convergence monotone — Beppo Levi

Si les fonctions $f_n$ sont positives et croissent point par point vers $f$, alors leurs intégrales croissent aussi vers l'intégrale de $f$. Il n'y a pas besoin de majorant : l'absence d'oscillations et de signes négatifs suffit.

> [!example] Approcher une aire par dessous
> Si $f\ge 0$ et que $f_n=\min(f,n)$, alors $f_n\uparrow f$. On peut donc calculer l'intégrale de $f$ comme la limite des intégrales de ses versions « tronquées ».

#### Convergence dominée — Lebesgue

Si $f_n\to f$ presque partout et s'il existe une fonction intégrable $h$ telle que

$$
|f_n(x)|\le h(x) \quad \text{pour presque tout }x \text{ et tout }n,
$$

alors on peut passer à la limite sous l'intégrale. Le majorant $h$ empêche la masse de se concentrer ou de s'échapper pendant la limite.

> [!example] Le rôle concret du majorant
> Les fonctions $f_n(x)=n\,\mathbf 1_{(0,1/n)}(x)$ convergent vers $0$ presque partout sur $(0,1)$, mais $\int_0^1 f_n=1$ pour tout $n$. Elles n'admettent pas de majorant intégrable commun. C'est exactement le phénomène que l'hypothèse de domination exclut.

> [!tip] À retenir
> - Positivité + croissance : penser **Beppo Levi**.
> - Convergence presque partout + une enveloppe intégrable : penser **convergence dominée**.
> - Sans une de ces protections, vérifier le passage à la limite au lieu de le supposer.

### 1.3. Densité des fonctions continues à support compact

Les fonctions de $C_c(\Omega)$ sont continues et nulles en dehors d'une zone compacte de $\Omega$. Elles sont denses dans $L^1$, et plus généralement dans $L^p$ pour $p<\infty$.

L'intuition : même une fonction rugueuse peut être d'abord coupée loin à l'infini, puis lissée à petite échelle. On la remplace ainsi par des fonctions beaucoup plus faciles à manipuler, sans perdre d'information au sens de la norme $L^p$.

### 1.4. Intégrales doubles : Tonelli et Fubini

Pour une fonction $f(x,y)$, deux questions se posent : l'intégrale double existe-t-elle, et peut-on intégrer d'abord en $y$, puis en $x$ ?

- **Tonelli** : si $f\ge 0$, l'ordre des intégrales peut être interverti, même si le résultat vaut $+\infty$. C'est l'analogue continu du fait que l'ordre de sommation de termes positifs ne change pas la somme.
- **Fubini** : si $f$ est intégrable en valeur absolue sur le produit, alors les intégrales itérées existent presque partout, sont intégrables, et donnent la même valeur que l'intégrale double.

> [!warning] Le point à ne pas oublier
> Une fonction peut avoir des intégrales le long de chaque droite horizontale et verticale sans que Fubini s'applique. La condition qui protège réellement l'échange des intégrales est l'intégrabilité de $|f|$. Sans elle, l'ordre de sommation peut modifier le résultat, comme pour une série conditionnellement convergente.

### 1.5. Changement de variables

Un changement de variables remplace une zone compliquée par une zone plus simple. Si $y=\Phi(x)$, le facteur $|\det D\Phi(x)|$ corrige la déformation des volumes :

$$
\int_{\Omega_2} f(y)\,dy
= \int_{\Omega_1} f(\Phi(x))\,|\det D\Phi(x)|\,dx.
$$

> [!example] Les coordonnées polaires
> Dans le plan, $(r,\theta)\mapsto(r\cos\theta,r\sin\theta)$ transforme un disque en rectangle $[0,R]\times[0,2\pi]$. Le jacobien vaut $r$, d'où
> $$
> \int_{B(0,R)} f(x,y)\,dx\,dy
> =\int_0^{2\pi}\int_0^R f(r\cos\theta,r\sin\theta)\,r\,dr\,d\theta.
> $$
> Le $r$ supplémentaire exprime simplement que les couronnes sont de plus en plus longues.

---

## 2. Les espaces $L^p$ : différentes façons de mesurer une fonction

Pour $1\le p<\infty$, on définit

$$
\|f\|_{L^p(\Omega)}=\left(\int_\Omega |f|^p\right)^{1/p}.
$$

Et $\|f\|_{L^\infty}$ est la plus petite borne essentielle de $|f|$ : c'est essentiellement la plus grande hauteur atteinte par $f$, en ignorant les exceptions de mesure nulle.

- $L^1$ privilégie la **masse totale** : on pense à l'aire sous $|f|$.
- $L^2$ privilégie l'**énergie** ou l'amplitude quadratique : on pense à une moyenne quadratique.
- $L^\infty$ privilégie le **pire écart** : on pense à la hauteur maximale, sauf sur un ensemble négligeable.

> [!example] Un pic fin
> Un pic très haut mais très étroit peut avoir une petite norme $L^1$, une norme $L^2$ modérée ou grande, et une énorme norme $L^\infty$. Chaque norme répond donc à une question différente.

### 2.1. Exposants conjugués et inégalité de Hölder

À $p$ est associé son exposant conjugué $p'$, défini par

$$
\frac1p+\frac1{p'}=1.
$$

Par exemple, le conjugué de $2$ est $2$, celui de $1$ est $\infty$. L'inégalité de Hölder dit que multiplier une fonction de $L^p$ et une de $L^{p'}$ produit une fonction intégrable :

$$
\int |fg|\le \|f\|_{L^p}\,\|g\|_{L^{p'}}.
$$

Elle généralise Cauchy-Schwarz, qui est le cas $p=2$. C'est l'outil standard pour montrer qu'un terme intégral est bien défini et contrôlé.

> [!example] Le cas $L^2$
> Pour deux signaux $f$ et $g$, $\int fg$ mesure leur corrélation. Hölder avec $p=2$ assure qu'elle reste finie dès que les deux signaux ont une énergie finie.

### 2.2. Minkowski : l'inégalité triangulaire des fonctions

$$
\|f+g\|_{L^p}\le \|f\|_{L^p}+\|g\|_{L^p}.
$$

Cette formule donne vraiment à $\|\cdot\|_{L^p}$ le statut de norme. Elle dit que l'effet total de deux signaux est au plus la somme de leurs tailles.

### 2.3. Interpolation : être contrôlé à deux échelles

Si $f$ appartient à la fois à $L^s$ et à $L^t$, alors elle appartient aussi à tous les espaces intermédiaires $L^r$, avec $s\le r\le t$. Sa norme $L^r$ est contrôlée par un mélange de ses normes aux deux extrémités.

Intuitivement, une information sur la masse globale et une information sur les très grands pics donnent un contrôle à une échelle intermédiaire. Cette propriété est particulièrement utile pour obtenir des estimées sans recalculer une intégrale à chaque exposant.

> [!warning] Les inclusions dépendent du domaine
> Sur un domaine de mesure finie, $L^q\subset L^p$ si $q>p$ : contrôler les grandes valeurs contrôle aussi la masse. Sur $\mathbb R^d$, aucune inclusion générale de ce type n'est vraie sans hypothèse supplémentaire ; les problèmes peuvent venir soit près d'une singularité, soit à l'infini.

### 2.4. Complétude, dualité et localisation

- Les espaces $L^p$ sont complets : une suite de Cauchy pour $\|\cdot\|_{L^p}$ possède une limite dans $L^p$.
- Une forme linéaire continue sur $L^p$ peut souvent s'écrire comme une intégrale $f\mapsto\int uf$, avec $u\in L^{p'}$. C'est la représentation de Riesz pour les $L^p$ du support (en particulier, $(L^1)'=L^\infty$).
- La notation $L^p_{\mathrm{loc}}(\Omega)$ signifie « dans $L^p$ sur tout compact ». Elle autorise un mauvais comportement à l'infini, mais pas dans une zone bornée.

> [!example] Localement intégrable, mais pas intégrable globalement
> La fonction constante $1$ est dans $L^1_{\mathrm{loc}}(\mathbb R)$, car son intégrale sur chaque intervalle borné est finie. Elle n'est pas dans $L^1(\mathbb R)$, car sa masse totale est infinie.

### 2.5. Convolution : moyenner et lisser

La convolution de deux fonctions sur $\mathbb R^d$ est

$$
(f*g)(x)=\int_{\mathbb R^d} f(x-y)g(y)\,dy.
$$

On peut la lire comme une moyenne pondérée de $f$ autour de $x$, dont le noyau est $g$. Elle apparaît en traitement du signal, en probabilités et dans les équations aux dérivées partielles.

> [!example] Flou d'une image
> Si $g$ est une petite bosse positive de masse $1$, $f*g$ est une version lissée de $f$. Le support rappelle notamment que $L^1*L^1\subset L^1$ et $L^1*L^2\subset L^2$ : moyenner par un noyau intégrable ne détruit pas le contrôle en $L^2$.

---

## 3. Espaces de Hilbert : la géométrie en dimension infinie

Un espace de Hilbert est un espace vectoriel complet muni d'un produit scalaire $\langle f,g\rangle$. Sa norme est induite par ce produit scalaire :

$$
\|f\|=\sqrt{\langle f,f\rangle}.
$$

Les exemples essentiels sont $\mathbb R^n$ et $L^2(\Omega)$, où

$$
\langle f,g\rangle_{L^2}=\int_\Omega f(x)g(x)\,dx.
$$

Dans un Hilbert, les mots géométriques usuels — angle, orthogonalité, projection — continuent d'avoir un sens.

### 3.1. Les identités géométriques

- **Cauchy-Schwarz** : $|\langle f,g\rangle|\le\|f\|\,\|g\|$. Deux vecteurs ne peuvent pas être plus corrélés que ne le permettent leurs tailles.
- **Pythagore** : si $f\perp g$, alors $\|f+g\|^2=\|f\|^2+\|g\|^2$. Les énergies de composantes orthogonales s'additionnent.
- **Identité du parallélogramme** : elle caractérise les normes qui proviennent d'un produit scalaire. Toutes les normes ne viennent donc pas d'une géométrie euclidienne.

> [!example] Pourquoi $L^2$ est spécial
> Dans $L^2$, deux signaux orthogonaux ne se gênent pas : l'énergie du signal total est la somme des énergies. Cela explique l'importance de $L^2$ pour les séries de Fourier et la physique.

### 3.2. Projection sur un convexe fermé

Dans un Hilbert, tout point $f$ possède un unique point le plus proche dans un ensemble convexe fermé non vide $C$. Pour un sous-espace fermé $F$, ce point est la projection orthogonale de $f$ sur $F$.

On obtient la décomposition unique

$$
f=g+h,\qquad g\in F,\quad h\in F^\perp.
$$

La partie $g$ est ce que le sous-espace sait représenter ; le résidu $h$ est l'erreur irréductible, orthogonale à tout ce qu'on a gardé.

> [!example] Moindres carrés
> Ajuster une droite à des données revient à projeter le vecteur des observations sur l'espace engendré par les colonnes du modèle. Le résidu est orthogonal aux directions disponibles : c'est l'origine géométrique des équations normales.

### 3.3. Le théorème de représentation de Riesz

Toute forme linéaire continue $\varphi$ sur un espace de Hilbert s'écrit de façon unique

$$
\varphi(u)=\langle u,f\rangle
$$

pour un certain $f$ de l'espace. Autrement dit, dans un Hilbert, les objets qui « testent » les vecteurs sont eux-mêmes des vecteurs. Le dual et l'espace se confondent naturellement.

### 3.4. Bases de Hilbert et séries de Fourier

Une base hilbertienne $(e_n)$ est une famille orthonormée suffisamment riche pour reconstruire tout élément $f$ :

$$
f=\sum_n \langle f,e_n\rangle e_n.
$$

Les coefficients $\langle f,e_n\rangle$ sont les coordonnées de $f$. Parseval dit que l'énergie se conserve dans ces coordonnées :

$$
\|f\|^2=\sum_n |\langle f,e_n\rangle|^2.
$$

> [!example] Les sinusoïdes
> Dans $L^2([0,T])$, les sinus et cosinus forment la base de Fourier classique. Décomposer un signal dans cette base revient à séparer ses fréquences. Des bases de sinus seules ou de cosinus seules existent aussi ; elles sont adaptées, respectivement, à des conditions de bord de type Dirichlet et Neumann.

### 3.5. Lax-Milgram : minimiser une énergie pour résoudre une équation

Beaucoup d'équations se réécrivent comme la minimisation d'une énergie

$$
E(u)=\frac12a(u,u)-b(u),
$$

où $a$ est bilinéaire, continue, symétrique et **coercive**. La coercivité signifie, en substance, que l'énergie quadratique augmente au moins comme $\|u\|^2$ : elle empêche la minimisation de partir à l'infini.

Le théorème de Lax-Milgram assure alors qu'il existe un unique minimiseur $u$, caractérisé par

$$
a(u,v)=b(v)\qquad \text{pour tout test }v.
$$

> [!example] L'équation de Poisson
> Chercher une fonction $u$ nulle au bord qui résout $-\Delta u=f$ revient à minimiser
> $$
> E(u)=\frac12\int_\Omega |\nabla u|^2-\int_\Omega fu.
> $$
> La première intégrale pénalise les variations rapides ; la seconde représente la force source $f$. Lax-Milgram donne alors existence et unicité de la solution faible dans le bon espace de Sobolev.

---

## 4. Distributions : dériver au-delà des fonctions classiques

Certaines fonctions intéressantes ont des discontinuités, ou même sont concentrées en un point. On ne peut pas toujours les dériver au sens classique. L'idée des distributions est de ne plus regarder directement l'objet, mais la façon dont il agit sur des fonctions tests lisses à support compact.

On note

$$
\mathcal D(\Omega)=C_c^\infty(\Omega),\qquad
\mathcal D'(\Omega)=\text{dual de }\mathcal D(\Omega).
$$

Une distribution $T$ attribue un nombre $\langle T,\varphi\rangle$ à chaque test $\varphi$. Les tests sont lisses et localisés : ils permettent de sonder l'objet sans lui demander des valeurs ponctuelles trop exigeantes.

### 4.1. Exemples fondamentaux

- Une fonction localement intégrable $f$ définit la distribution $T_f$ par
  $$
  \langle T_f,\varphi\rangle=\int_\Omega f(x)\varphi(x)\,dx.
  $$
- La masse de Dirac en $0$ est définie par $\langle\delta_0,\varphi\rangle=\varphi(0)$. Elle modélise une masse ponctuelle idéale.
- La dérivée $\delta_0'$ est donnée par $\langle\delta_0',\varphi\rangle=-\varphi'(0)$. Elle mesure la pente du test au point zéro.
- Le peigne de Dirac additionne les valeurs de $\varphi$ sur un réseau de points : c'est le modèle mathématique d'un échantillonnage périodique.

### 4.2. La dérivée distributionnelle

La dérivée de $T$ est définie par déplacement de la dérivée sur le test :

$$
\langle T',\varphi\rangle=-\langle T,\varphi'\rangle.
$$

Cette formule est une intégration par parties sans terme de bord : le support compact de $\varphi$ fait disparaître ce terme. Si $f$ est dérivable au sens classique, on retrouve bien sa dérivée classique.

> [!example] Le saut devient une Dirac
> La fonction de Heaviside $H(x)=\mathbf 1_{(0,\infty)}(x)$ est constante de chaque côté de zéro, donc sa dérivée classique vaut $0$ là où elle est définie. Pourtant elle possède un saut de taille $1$ en zéro. Sa dérivée distributionnelle est $H'=\delta_0$ : toute la variation est concentrée au point du saut.

### 4.3. Convergence et produits

Une suite de distributions $T_n$ converge vers $T$ si $\langle T_n,\varphi\rangle\to\langle T,\varphi\rangle$ pour toute fonction test $\varphi$. On teste donc la convergence par toutes les mesures lisses et localisées possibles.

On peut multiplier une distribution par une fonction lisse :

$$
\langle fT,\varphi\rangle=\langle T,f\varphi\rangle.
$$

En revanche, le produit de deux distributions n'est **pas défini en général**. Par exemple, tenter de donner un sens à $\delta_0^2$ demande des hypothèses supplémentaires : c'est une limite importante de ce calcul généralisé.

---

## 5. Espaces de Sobolev : assez réguliers pour dériver faiblement

Les espaces de Sobolev réunissent les fonctions dont les dérivées, comprises au sens distributionnel, restent contrôlées dans $L^p$. Pour un entier $k\ge 1$,

$$
W^{k,p}(\Omega)=\{f : f \text{ et toutes ses dérivées faibles jusqu'à l'ordre }k \text{ sont dans }L^p\}.
$$

En dimension un, une norme typique est

$$
\|f\|_{W^{k,p}}
=\left(\|f\|_{L^p}^p+\|f'\|_{L^p}^p+\cdots+\|f^{(k)}\|_{L^p}^p\right)^{1/p}.
$$

On note $H^k=W^{k,2}$. Ces espaces sont particulièrement agréables car les $H^k$ sont des espaces de Hilbert : on y retrouve projections, bases et minimisation d'énergie.

### 5.1. Ce que mesure vraiment $W^{1,p}$

Être dans $W^{1,p}$, ce n'est pas forcément être dérivable partout. Cela veut dire que la fonction a une dérivée faible qui est une vraie fonction de $L^p$, donc que ses variations sont contrôlées en moyenne.

> [!example] Deux fonctions qui se ressemblent, mais pas pour Sobolev
> - $f(x)=|x|$ appartient à $W^{1,p}_{\mathrm{loc}}$ : sa dérivée faible est $\operatorname{sgn}(x)$, qui est bornée. Le coin en zéro est acceptable à l'ordre 1.
> - La fonction de Heaviside $H$ n'appartient pas à $W^{1,p}_{\mathrm{loc}}$ pour $p\ge1$, car sa dérivée faible est une Dirac, et une Dirac n'est pas une fonction de $L^p$. Un saut est donc trop violent à l'ordre 1.

### 5.2. Pourquoi Sobolev est l'espace naturel des EDP

Les équations aux dérivées partielles demandent rarement une solution deux fois dérivable partout. Il suffit souvent de pouvoir intégrer par parties contre tous les tests. Les espaces de Sobolev fournissent exactement ce niveau de régularité faible, tout en restant complets.

Pour le problème de Poisson avec condition de bord nulle, l'espace naturel est généralement $H^1_0(\Omega)$ : fonctions de $H^1$ dont la trace au bord est nulle. Dans cet espace, la quantité $\int|\nabla u|^2$ est l'énergie pertinente, et Lax-Milgram transforme l'équation en un problème de minimisation bien posé.

---

## 6. Carte mentale : quel outil utiliser ?

- **Une limite est sous une intégrale** : chercher Beppo Levi ou la convergence dominée, afin de justifier l'échange limite/intégrale.
- **Une intégrale porte sur deux variables** : utiliser Tonelli si $f\ge0$, Fubini si $|f|\in L^1$, afin de changer l'ordre d'intégration sans erreur.
- **Un produit $fg$ doit être intégrable** : appliquer Hölder et associer $L^p$ à $L^{p'}$.
- **On veut une meilleure approximation** : utiliser la densité de $C_c$ ou $C_c^\infty$, qui remplace une fonction rugueuse par une fonction simple.
- **On cherche le meilleur élément d'un modèle** : projeter dans un Hilbert ; le résidu devient orthogonal au modèle.
- **On cherche une solution d'EDP par énergie** : utiliser Lax-Milgram dans un $H^k$ adapté ; il donne l'existence et l'unicité du minimiseur.
- **Une dérivée classique n'existe pas** : passer aux distributions puis aux Sobolev, c'est-à-dire intégrer par parties contre des fonctions tests.

## 7. Les messages essentiels en une page

1. $L^p$ ne décrit pas seulement des fonctions : il encode la manière dont on accepte de mesurer leur taille.
2. Les passages à la limite sont puissants, mais doivent être protégés par monotonie ou domination.
3. $L^2$ est le lieu de la géométrie : orthogonalité, projections et Fourier y deviennent possibles.
4. Les distributions élargissent la notion de dérivée ; les Sobolev sélectionnent celles dont les dérivées faibles restent contrôlables.
5. Une grande partie des EDP peut se lire comme : « minimiser une énergie dans le bon espace de Hilbert ».

## Pour aller plus loin

Le support recommande notamment :

- H. Brézis, *Analyse fonctionnelle* ;
- L. C. Evans, *Partial Differential Equations* ;
- C. Gasquet et P. Witomski, *Analyse de Fourier et applications*.

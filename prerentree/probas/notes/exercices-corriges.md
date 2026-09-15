---
title: Probabilités - Correction des exercices de pré-rentrée
aliases:
  - Correction du TD de probabilités
  - TD de probabilités corrigé
tags:
  - mva
  - probabilites
  - exercices
  - corrections
date: 2026-09-14
sources:
  - "[[td-enonces-des-exercices.pdf]]"
  - "[[td-corrections-manuscrites-jour-1.pdf]]"
  - "[[td.pdf]]"
course: "[[cours-principal]]"
---
	
# Probabilités - Correction des exercices de pré-rentrée

Cours associé : [[cours-principal]].

## Sommaire

- [[#Exercice 1 - Applications mesurables|1. Applications mesurables]]
- [[#Exercice 2 - Compter avec des indicatrices|2. Compter avec des indicatrices]]
- [[#Exercice 3 - Lois de Poisson|3. Lois de Poisson]]
- [[#Exercice 4 - Lois normales|4. Lois normales]]
- [[#Exercice 5 - Indépendance|5. Indépendance de variables à deux valeurs]]
- [[#Exercice 6 - Lemme de Borel-Cantelli|6. Lemme de Borel-Cantelli]]
- [[#Exercice 7 - Modes de convergence|7. Modes de convergence]]
- [[#Exercice 8 - Convergence de variables aléatoires|8. Convergence de variables aléatoires]]
- [[#Exercice 10 - Lemme de Slutsky|10. Lemme de Slutsky]]
- [[#Exercice 11 - Théorème central limite|11. Théorème central limite]]
- [[#Exercice 14 - Indépendance et vecteurs gaussiens|14. Indépendance et vecteurs gaussiens]]
- [[#Exercice 19 - Marche aléatoire sur un graphe|19. Marche aléatoire sur un graphe]]

---

## Exercice 1 - Applications mesurables

> [!question] Énoncé
> Montrer que la composée de deux applications mesurables est mesurable.

### Correction

Soient trois espaces mesurables $(E,\mathcal E)$, $(F,\mathcal F)$ et $(G,\mathcal G)$, ainsi que deux applications mesurables

$$
f:E\to F,
\qquad
g:F\to G.
$$

Pour montrer que $g\circ f$ est mesurable, prenons un ensemble $A\in\mathcal G$. La propriété essentielle des images réciproques donne

$$
(g\circ f)^{-1}(A)=f^{-1}\big(g^{-1}(A)\big).
$$

Or :

1. $g$ est mesurable, donc $g^{-1}(A)\in\mathcal F$ ;
2. $f$ est mesurable, donc $f^{-1}(g^{-1}(A))\in\mathcal E$.

Ainsi, pour tout $A\in\mathcal G$, $(g\circ f)^{-1}(A)\in\mathcal E$. On conclut que

$$
\boxed{g\circ f\text{ est mesurable}.}
$$

---

## Exercice 2 - Compter avec des indicatrices

> [!question] Énoncé
> Soient $A_1,\ldots,A_m$ des événements quelconques, pas nécessairement indépendants, et
> $$
> N=\sum_{i=1}^m\mathbf 1_{A_i}.
> $$
> Interpréter $N$ et calculer $\mathbb E[N]$.
>
> **Question bonus.** On lance $n$ boules indépendamment et uniformément dans $m$ cases. Quel est le nombre moyen de cases occupées ?

### Correction

Pour chaque issue $\omega$,

$$
\mathbf 1_{A_i}(\omega)=
\begin{cases}
1,&\omega\in A_i,\\
0,&\omega\notin A_i.
\end{cases}
$$

Par conséquent,

$$
N(\omega)=\sum_{i=1}^m\mathbf 1_{A_i}(\omega)
$$

est exactement le **nombre d'événements parmi $A_1,\ldots,A_m$ qui sont réalisés**.

Par linéarité de l'espérance,

$$
\begin{aligned}
\mathbb E[N]
&=\sum_{i=1}^m\mathbb E[\mathbf 1_{A_i}]\\
&=\sum_{i=1}^m\mathbb P(A_i).
\end{aligned}
$$

Donc

$$
\boxed{\mathbb E[N]=\sum_{i=1}^m\mathbb P(A_i).}
$$

> [!important]
> Aucune hypothèse d'indépendance n'est nécessaire : seule la linéarité de l'espérance est utilisée.

### Question bonus

Pour chaque case $j$, notons $B_j$ l'événement « la case $j$ est occupée ». Le nombre de cases occupées est

$$
N_{\mathrm{occ}}=\sum_{j=1}^m\mathbf 1_{B_j}.
$$

Une boule évite la case $j$ avec probabilité $1-1/m$. Les $n$ lancers étant indépendants,

$$
\mathbb P(B_j^c)=\left(1-\frac1m\right)^n,
$$

d'où

$$
\mathbb P(B_j)=1-\left(1-\frac1m\right)^n.
$$

Toutes les cases jouent le même rôle, donc

$$
\boxed{
\mathbb E[N_{\mathrm{occ}}]
=m\left[1-\left(1-\frac1m\right)^n\right].}
$$

---

## Exercice 3 - Lois de Poisson

> [!question] Énoncé
> Soient $X$ et $Y$ deux variables aléatoires indépendantes suivant des lois de Poisson de paramètres respectifs $\lambda,\mu>0$ :
> $$
> \mathbb P(X=k)=e^{-\lambda}\frac{\lambda^k}{k!},
> \qquad k\in\mathbb N.
> $$
>
> 1. Calculer l'espérance et la variance de $X$.
> 2. Donner la loi de $X+Y$.

### Correction

### 1. Espérance et variance

Pour l'espérance,

$$
\begin{aligned}
\mathbb E[X]
&=e^{-\lambda}\sum_{k=1}^{\infty}k\frac{\lambda^k}{k!}\\
&=\lambda e^{-\lambda}\sum_{k=1}^{\infty}\frac{\lambda^{k-1}}{(k-1)!}\\
&=\lambda e^{-\lambda}
\left(
\frac{\lambda^0}{0!}
+\frac{\lambda^1}{1!}
+\frac{\lambda^2}{2!}
+\cdots
\right)\\
&=\lambda e^{-\lambda}\sum_{j=0}^{\infty}\frac{\lambda^j}{j!}
\qquad\text{en posant }j=k-1\\
&=\lambda e^{-\lambda}e^{\lambda}\\
&=\lambda.
\end{aligned}
$$

> [!note] Pourquoi le changement d'indice ?
> L'indice $k$ parcourt $1,2,3,\ldots$, tandis que $k-1$ parcourt $0,1,2,\ldots$. Poser $j=k-1$ ne modifie donc pas les termes de la somme : on les renomme simplement. La dernière série est exactement le développement de $e^\lambda$ :
> $$
> e^\lambda=\sum_{j=0}^{\infty}\frac{\lambda^j}{j!}.
> $$

Pour éviter un calcul plus lourd de $\mathbb E[X^2]$, utilisons le moment factoriel :

$$
\begin{aligned}
\mathbb E[X(X-1)]
&=e^{-\lambda}\sum_{k=2}^{\infty}k(k-1)\frac{\lambda^k}{k!}\\
&=\lambda^2e^{-\lambda}\sum_{k=2}^{\infty}\frac{\lambda^{k-2}}{(k-2)!}\\
&=\lambda^2.
\end{aligned}
$$

Comme $X^2=X(X-1)+X$,

$$
\mathbb E[X^2]=\lambda^2+\lambda.
$$

Finalement,

$$
\boxed{\mathbb E[X]=\lambda,
\qquad
\operatorname{Var}(X)=\lambda.}
$$

### 2. Loi de la somme

Fixons $n\in\mathbb N$. Pour que $X+Y=n$, il faut et il suffit qu'il existe $k\in\{0,\ldots,n\}$ tel que

$$
X=k
\qquad\text{et}\qquad
Y=n-k.
$$

Ainsi, l'événement se décompose en une union disjointe :

$$
\{X+Y=n\}
=\bigsqcup_{k=0}^n\{X=k,\,Y=n-k\}.
$$

Les événements de cette union sont incompatibles : deux valeurs distinctes de $k$ imposeraient deux valeurs distinctes à $X$. On peut donc additionner leurs probabilités :

$$
\mathbb P(X+Y=n)
=\sum_{k=0}^n\mathbb P(X=k,\,Y=n-k).
$$

L'indépendance intervient seulement maintenant, pour factoriser chaque probabilité jointe :

$$
\mathbb P(X=k,\,Y=n-k)
=\mathbb P(X=k)\mathbb P(Y=n-k).
$$

On remplace alors chaque probabilité par sa masse de Poisson :

$$
\mathbb P(X=k)=e^{-\lambda}\frac{\lambda^k}{k!},
\qquad
\mathbb P(Y=n-k)=e^{-\mu}\frac{\mu^{n-k}}{(n-k)!}.
$$

Pour chaque $k$, leur produit vaut donc

$$
\mathbb P(X=k)\mathbb P(Y=n-k)
=e^{-\lambda}e^{-\mu}
\frac{\lambda^k}{k!}\frac{\mu^{n-k}}{(n-k)!}.
$$

Or $e^{-\lambda}e^{-\mu}=e^{-(\lambda+\mu)}$. Ce facteur ne dépend pas de $k$, il est donc commun à tous les termes de la somme et peut être mis devant celle-ci.

Pour réécrire les dénominateurs, on utilise la définition du coefficient binomial :

$$
\binom nk=\frac{n!}{k!(n-k)!}.
$$

En divisant cette égalité par $n!$, on obtient

$$
\frac1{k!(n-k)!}=\frac1{n!}\binom nk.
$$

Ainsi, pour chaque $k$,

$$
\frac{\lambda^k}{k!}\frac{\mu^{n-k}}{(n-k)!}
=\frac1{n!}\binom nk\lambda^k\mu^{n-k}.
$$

Le facteur $1/n!$ ne dépend pas non plus de $k$ ; on peut donc également le sortir de la somme. On obtient alors

$$
\begin{aligned}
\mathbb P(X+Y=n)
&=\sum_{k=0}^n\mathbb P(X=k)\mathbb P(Y=n-k)\\
&=\sum_{k=0}^n e^{-\lambda}e^{-\mu}
\frac{\lambda^k}{k!}\frac{\mu^{n-k}}{(n-k)!}\\
&=e^{-(\lambda+\mu)}
\sum_{k=0}^n\frac{\lambda^k}{k!}\frac{\mu^{n-k}}{(n-k)!}\\
&=\frac{e^{-(\lambda+\mu)}}{n!}
\sum_{k=0}^n\binom nk\lambda^k\mu^{n-k}\\
&=e^{-(\lambda+\mu)}\frac{(\lambda+\mu)^n}{n!}.
\end{aligned}
$$

La dernière égalité est la formule du binôme de Newton :

$$
\sum_{k=0}^n\binom nk\lambda^k\mu^{n-k}
=(\lambda+\mu)^n.
$$

Donc

$$
\boxed{X+Y\sim\mathcal P(\lambda+\mu).}
$$

---

## Exercice 4 - Lois normales

> [!tip] Outil mobilisé
> La preuve ci-dessous utilise le [[changement-de-variable|changement de variable]], en particulier la formule de densité d'une transformation affine.

> [!question] Énoncé
> Soit $X\sim\mathcal N(m,\sigma^2)$, de densité
> $$
> f_X(x)=\frac{1}{\sqrt{2\pi\sigma^2}}
> \exp\!\left(-\frac{(x-m)^2}{2\sigma^2}\right),
> \qquad m\in\mathbb R,\ \sigma>0.
> $$
> Soit $U\sim\mathcal N(0,1)$.
>
> 1. Montrer que $\sigma U+m$ a même loi que $X$.
> 2. Calculer $\mathbb E[X]$ et $\operatorname{Var}(X)$.
> 3. Calculer la densité de $Y=aX+b$, pour $a,b\in\mathbb R$.
> 4. Calculer $\mathbb E[Y]$ et $\operatorname{Var}(Y)$.
> 5. Calculer la fonction caractéristique de $X$.

### Correction

### 1. Centrage et réduction

Posons $Z=\sigma U+m$. Pour tout $x\in\mathbb R$, puisque $\sigma>0$,

$$
\begin{aligned}
F_Z(x)
&=\mathbb P(\sigma U+m\leq x)\\
&=\mathbb P\!\left(U\leq\frac{x-m}{\sigma}\right).
\end{aligned}
$$

Notons $\Phi$ la fonction de répartition de la loi normale centrée réduite et $\varphi$ sa densité :

$$
\Phi(t)=\int_{-\infty}^{t}\varphi(u)\,\mathrm du,
\qquad
\varphi(u)=\frac1{\sqrt{2\pi}}e^{-u^2/2}.
$$

On a donc plus explicitement

$$
F_Z(x)=\Phi\!\left(\frac{x-m}{\sigma}\right).
$$

#### Première méthode - dérivation de la fonction de répartition

La fonction $\Phi$ est dérivable et $\Phi'=\varphi$. Par la règle de dérivation des fonctions composées,

$$
\begin{aligned}
f_Z(x)
=F_Z'(x)
&=\Phi'\!\left(\frac{x-m}{\sigma}\right)
\times\frac{\mathrm d}{\mathrm dx}\left(\frac{x-m}{\sigma}\right)\\
&=\varphi\!\left(\frac{x-m}{\sigma}\right)\times\frac1\sigma\\
&=\frac1{\sqrt{2\pi}\sigma}
\exp\!\left[-\frac12\left(\frac{x-m}{\sigma}\right)^2\right]\\
&=\frac1{\sqrt{2\pi}\sigma}
\exp\!\left(-\frac{(x-m)^2}{2\sigma^2}\right).
\end{aligned}
$$

#### Deuxième méthode - changement de variable dans une probabilité

Soit $A\in\mathcal B(\mathbb R)$. Comme $Z=\sigma U+m$ et $\sigma>0$,

$$
\begin{aligned}
\mathbb P(Z\in A)
&=\mathbb P\!\left(U\in\frac{A-m}{\sigma}\right)\\
&=\int_{(A-m)/\sigma}\varphi(u)\,\mathrm du.
\end{aligned}
$$

Effectuons le changement de variable $y=\sigma u+m$. Alors $u=(y-m)/\sigma$ et $\mathrm du=\mathrm dy/\sigma$. L'ensemble $(A-m)/\sigma$ est envoyé sur $A$, donc

$$
\begin{aligned}
\mathbb P(Z\in A)
&=\int_A\frac1\sigma\,
\varphi\!\left(\frac{y-m}{\sigma}\right)\mathrm dy\\
&=\int_A\frac1{\sqrt{2\pi}\sigma}
\exp\!\left(-\frac{(y-m)^2}{2\sigma^2}\right)\mathrm dy.
\end{aligned}
$$

On identifie donc la densité de $Z$ :

$$
\boxed{
f_Z(x)=\frac{1}{\sqrt{2\pi}\sigma}
\exp\!\left(-\frac{(x-m)^2}{2\sigma^2}\right)=f_X(x).}
$$
 
Ainsi,

$$
\boxed{X\overset{\mathcal L}=\sigma U+m.}
$$

### 2. Espérance et variance

Par symétrie de la densité normale centrée, $\mathbb E[U]=0$. Un calcul classique donne $\mathbb E[U^2]=1$, donc $\operatorname{Var}(U)=1$. Par les règles de transformation affine,

$$
\boxed{\mathbb E[X]=m,
\qquad
\operatorname{Var}(X)=\sigma^2.}
$$

### 3. Densité de $Y=aX+b$

On distingue d'abord le cas $a\neq0$. La formule de densité ne doit pas être retenue sans comprendre son origine : elle vient de la fonction de répartition et de la règle de dérivation des fonctions composées.

#### Cas $a>0$

Pour tout $y\in\mathbb R$,

$$
\begin{aligned}
F_Y(y)
&=\mathbb P(Y\leq y)\\
&=\mathbb P(aX+b\leq y)\\
&=\mathbb P\!\left(X\leq\frac{y-b}{a}\right)\\
&=F_X\!\left(\frac{y-b}{a}\right).
\end{aligned}
$$

En dérivant, on obtient

$$
\begin{aligned}
f_Y(y)
&=F_Y'(y)\\
&=F_X'\!\left(\frac{y-b}{a}\right)
\times\frac{\mathrm d}{\mathrm dy}\left(\frac{y-b}{a}\right)\\
&=f_X\!\left(\frac{y-b}{a}\right)\times\frac1a.
\end{aligned}
$$

#### Cas $a<0$

Diviser une inégalité par un nombre négatif inverse son sens. Ainsi,

$$
\begin{aligned}
F_Y(y)
&=\mathbb P(aX+b\leq y)\\
&=\mathbb P\!\left(X\geq\frac{y-b}{a}\right)\\
&=1-F_X\!\left(\frac{y-b}{a}\right).
\end{aligned}
$$

En dérivant,

$$
f_Y(y)
=-f_X\!\left(\frac{y-b}{a}\right)\times\frac1a
=\frac1{|a|}f_X\!\left(\frac{y-b}{a}\right),
$$

car $-1/a=1/|a|$ lorsque $a<0$.

Les deux cas se regroupent donc dans la formule

$$
\boxed{
f_Y(y)=\frac1{|a|}f_X\!\left(\frac{y-b}{a}\right).}
$$

> [!intuition]
> Le facteur $1/|a|$ compense l'étirement ou la contraction des intervalles. Si $|a|>1$, la transformation étale les valeurs horizontalement ; la densité doit donc être plus basse afin que son intégrale reste égale à $1$.

#### Application à la densité normale

On remplace $f_X$ par sa formule :

$$
\begin{aligned}
f_Y(y)
&=\frac1{|a|}\frac1{\sqrt{2\pi}\sigma}
\exp\!\left[-\frac1{2\sigma^2}
\left(\frac{y-b}{a}-m\right)^2\right].
\end{aligned}
$$

Pour simplifier l'exposant, on met au même dénominateur :

$$
\frac{y-b}{a}-m
=\frac{y-b-am}{a}
=\frac{y-(am+b)}{a}.
$$

Après élévation au carré,

$$
\left(\frac{y-b}{a}-m\right)^2
=\frac{(y-(am+b))^2}{a^2}.
$$

Ainsi,

$$
f_Y(y)=\frac{1}{\sqrt{2\pi a^2\sigma^2}}
\exp\!\left(-\frac{(y-(am+b))^2}{2a^2\sigma^2}\right).
$$

Donc

$$
\boxed{Y\sim\mathcal N(am+b,a^2\sigma^2)}
\qquad(a\neq0).
$$

Si $a=0$, alors $Y=b$ presque sûrement : sa loi est la masse de Dirac $\delta_b$ et elle n'a pas de densité par rapport à la mesure de Lebesgue.

### 4. Moments de $Y$

$$
\boxed{
\mathbb E[Y]=am+b,
\qquad
\operatorname{Var}(Y)=a^2\sigma^2.}
$$

### 5. Fonction caractéristique

Par définition, la fonction caractéristique de $X$ est

$$
\varphi_X(t)=\mathbb E[e^{itX}].
$$

D'après la question 1,

$$
X\overset{\mathcal L}=\sigma U+m,
\qquad U\sim\mathcal N(0,1).
$$

On peut donc calculer la fonction caractéristique à partir de $\sigma U+m$ :

$$
\begin{aligned}
\varphi_X(t)
&=\mathbb E[e^{it(\sigma U+m)}]\\
&=\mathbb E[e^{it\sigma U}e^{itm}].
\end{aligned}
$$

Le terme $e^{itm}$ ne dépend pas de $U$ : c'est une constante, que l'on peut sortir de l'espérance. Ainsi,

$$
\varphi_X(t)=e^{imt}\mathbb E[e^{i(\sigma t)U}].
$$

Par définition de $\varphi_U$, pour tout réel $s$,

$$
\varphi_U(s)=\mathbb E[e^{isU}].
$$

Ici, $s=\sigma t$, donc

$$
\mathbb E[e^{i(\sigma t)U}]=\varphi_U(\sigma t).
$$

La fonction caractéristique de la normale centrée réduite est

$$
\varphi_U(s)=e^{-s^2/2}.
$$

En évaluant cette formule en $s=\sigma t$,

$$
\varphi_U(\sigma t)
=e^{-(\sigma t)^2/2}
=e^{-\sigma^2t^2/2}.
$$

Finalement,

$$
\boxed{
\varphi_X(t)
=e^{imt}e^{-\sigma^2t^2/2}
=\exp\!\left(imt-\frac{\sigma^2t^2}{2}\right).}
$$

> [!intuition]
> Le terme $imt$ vient du décalage de moyenne $m$, tandis que le terme $-\sigma^2t^2/2$ vient de la dispersion $\sigma^2$.

---

## Exercice 5 - Indépendance

> [!question] Énoncé
> Soient $X$ et $Y$ deux variables aléatoires ne pouvant prendre que deux valeurs distinctes chacune. Montrer que $X$ et $Y$ sont indépendantes si et seulement si
> $$
> \mathbb E[XY]=\mathbb E[X]\mathbb E[Y].
> $$

### Correction

Supposons

$$
X\in\{x_0,x_1\},
\qquad
Y\in\{y_0,y_1\},
$$

avec $x_0\neq x_1$ et $y_0\neq y_1$. Posons

$$
p=\mathbb P(X=x_1),
\quad
q=\mathbb P(Y=y_1),
\quad
r=\mathbb P(X=x_1,Y=y_1).
$$

On peut écrire

$$
X=x_0+(x_1-x_0)\mathbf 1_{\{X=x_1\}},
$$

et de même

$$
Y=y_0+(y_1-y_0)\mathbf 1_{\{Y=y_1\}}.
$$

En effet, l'indicatrice $\mathbf 1_{\{X=x_1\}}$ vaut $0$ lorsque $X=x_0$ et $1$ lorsque $X=x_1$. Ainsi,

$$
x_0+(x_1-x_0)\mathbf 1_{\{X=x_1\}}
=
\begin{cases}
x_0 & \text{si }X=x_0,\\
x_1 & \text{si }X=x_1.
\end{cases}
$$

Cette écriture revient à partir de $x_0$ et à ajouter l'écart $x_1-x_0$ exactement sur l'événement $\{X=x_1\}$.

Posons, pour alléger les calculs,

$$
U=\mathbf 1_{\{X=x_1\}},\qquad V=\mathbf 1_{\{Y=y_1\}},
\qquad a=x_1-x_0,\qquad b=y_1-y_0.
$$

Alors $X=x_0+aU$ et $Y=y_0+bV$. Développons la covariance à partir de sa définition :

$$
\operatorname{Cov}(X,Y)=\mathbb E[XY]-\mathbb E[X]\mathbb E[Y].
$$

D'une part,

$$
\begin{aligned}
XY
&=(x_0+aU)(y_0+bV)\\
&=x_0y_0+x_0bV+y_0aU+abUV,
\end{aligned}
$$

et donc

$$
\mathbb E[XY]
=x_0y_0+x_0b\mathbb E[V]+y_0a\mathbb E[U]+ab\mathbb E[UV].
$$

D'autre part,

$$
\begin{aligned}
\mathbb E[X]\mathbb E[Y]
&=(x_0+a\mathbb E[U])(y_0+b\mathbb E[V])\\
&=x_0y_0+x_0b\mathbb E[V]+y_0a\mathbb E[U]
+ab\mathbb E[U]\mathbb E[V].
\end{aligned}
$$

En soustrayant, tous les termes sauf le dernier s'annulent :

$$
\operatorname{Cov}(X,Y)
=ab\bigl(\mathbb E[UV]-\mathbb E[U]\mathbb E[V]\bigr).
$$

Or

$$
\mathbb E[U]=\mathbb P(X=x_1)=p,
\qquad
\mathbb E[V]=\mathbb P(Y=y_1)=q.
$$

De plus, $UV=1$ si et seulement si $X=x_1$ et $Y=y_1$ simultanément ; autrement dit,

$$
UV=\mathbf 1_{\{X=x_1,Y=y_1\}},
\qquad
\mathbb E[UV]=\mathbb P(X=x_1,Y=y_1)=r.
$$

On obtient finalement

$$
\operatorname{Cov}(X,Y)
=(x_1-x_0)(y_1-y_0)(r-pq).
$$

### Sens direct

Si $X$ et $Y$ sont indépendantes, alors $r=pq$. La covariance est donc nulle, ce qui donne

$$
\mathbb E[XY]=\mathbb E[X]\mathbb E[Y].
$$

### Réciproque

Supposons $\mathbb E[XY]=\mathbb E[X]\mathbb E[Y]$. Alors $\operatorname{Cov}(X,Y)=0$. Puisque les valeurs sont distinctes,

$$
(x_1-x_0)(y_1-y_0)\neq0,
$$

la formule de covariance implique nécessairement $r-pq=0$, donc

$$
r=pq.
$$

Autrement dit,

$$
\mathbb P(X=x_1,Y=y_1)
=\mathbb P(X=x_1)\mathbb P(Y=y_1).
$$

Pour conclure à l'indépendance, regardons la **loi jointe** de $(X,Y)$. Chaque case du tableau correspond à la probabilité que $X$ et $Y$ prennent simultanément les deux valeurs indiquées. Les sommes des lignes donnent la loi marginale de $X$ et les sommes des colonnes celle de $Y$ :

$$
\begin{array}{c|cc|c}
 & Y=y_0 & Y=y_1 & \text{Total}\\ \hline
X=x_0 & \mathbb P(X=x_0,Y=y_0) & \mathbb P(X=x_0,Y=y_1) & \mathbb P(X=x_0)=1-p\\
X=x_1 & \mathbb P(X=x_1,Y=y_0) & \mathbb P(X=x_1,Y=y_1) & \mathbb P(X=x_1)=p\\ \hline
\text{Total} & \mathbb P(Y=y_0)=1-q & \mathbb P(Y=y_1)=q & 1
\end{array}
$$

La case $(x_1,y_1)$ vaut $r=pq$. Les trois autres cases sont alors imposées par les totaux des lignes et des colonnes. Par exemple, la ligne $X=x_1$ doit sommer à $p$, d'où

$$
\begin{aligned}
\mathbb P(X=x_1,Y=y_0)&=p-r=p(1-q),\\
\mathbb P(X=x_0,Y=y_1)&=q-r=(1-p)q,\\
\mathbb P(X=x_0,Y=y_0)&=1-p-q+r=(1-p)(1-q).
\end{aligned}
$$

On peut donc écrire le tableau complet :

$$
\begin{array}{c|cc|c}
 & Y=y_0 & Y=y_1 & \text{Total}\\ \hline
X=x_0 & (1-p)(1-q) & (1-p)q & 1-p\\
X=x_1 & p(1-q) & pq & p\\ \hline
\text{Total} & 1-q & q & 1
\end{array}
$$

Chaque case est le produit de la probabilité de sa ligne et de celle de sa colonne. Par exemple,

$$
\mathbb P(X=x_1,Y=y_0)
=p(1-q)
=\mathbb P(X=x_1)\mathbb P(Y=y_0).
$$

Toutes les probabilités jointes se factorisent donc : c'est exactement la définition de l'indépendance. Ainsi,

$$
\boxed{X\perp\!\!\!\perp Y
\quad\Longleftrightarrow\quad
\mathbb E[XY]=\mathbb E[X]\mathbb E[Y]}
$$

dans le cas particulier où chaque variable ne prend que deux valeurs.

> [!warning]
> Cette réciproque est fausse pour des variables générales : une covariance nulle n'implique pas toujours l'indépendance.

---

## Exercice 6 - Lemme de Borel-Cantelli

> [!question] Énoncé
> Pour une suite d'événements $(A_n)_{n\in\mathbb N}$, on note
> $$
> \limsup_n A_n
> =\bigcap_{n=0}^{\infty}\bigcup_{k=n}^{\infty}A_k,
> \qquad
> \liminf_n A_n
> =\bigcup_{n=0}^{\infty}\bigcap_{k=n}^{\infty}A_k.
> $$
> Interpréter ces deux événements, puis démontrer :
>
> 1. si $\sum_{n\geq0}\mathbb P(A_n)<\infty$, alors $\mathbb P(\limsup_n A_n)=0$ ;
> 2. si les $A_n$ sont indépendants et $\mathbb P(\limsup_n A_n)=0$, alors $\sum_{n\geq0}\mathbb P(A_n)<\infty$.

### Correction

### Interprétation

Pour comprendre ces deux définitions, fixons une issue $\omega$. À chaque rang, on peut répondre par **oui** ou **non** à la question : « $\omega\in A_n$ ? ».

Rappel : une union correspond à « **ou** », tandis qu'une intersection correspond à « **et** ».

L'événement

$$
\limsup_n A_n=\bigcap_{n=0}^{\infty}\bigcup_{k=n}^{\infty}A_k
$$

se lit de l'intérieur vers l'extérieur :

- $\displaystyle\bigcup_{k=n}^{\infty}A_k$ signifie : « après le rang $n$, il y a **au moins un** événement $A_k$ qui se produit » ; c'est un « ou » entre $A_n,A_{n+1},\ldots$ ;
- $\displaystyle\bigcap_{n=0}^{\infty}$ impose que cette phrase soit vraie **pour tout** rang de départ $n$ ; c'est un « et » entre tous les rangs de départ.

Ainsi, quel que soit le rang à partir duquel on regarde, on retrouve un $A_k$ qui se produit plus loin. Autrement dit, $A_n$ se produit **une infinité de fois** : il y a une infinité de « oui ».

L'événement

$$
\liminf_n A_n=\bigcup_{n=0}^{\infty}\bigcap_{k=n}^{\infty}A_k
$$

se lit également de l'intérieur vers l'extérieur :

- $\displaystyle\bigcap_{k=n}^{\infty}A_k$ signifie : « tous les événements à partir du rang $n$ se produisent » ; c'est un « et » entre $A_n,A_{n+1},\ldots$ ;
- $\displaystyle\bigcup_{n=0}^{\infty}$ signifie qu'il suffit qu'il existe **un** rang de départ $n$ pour lequel cette phrase soit vraie ; c'est un « ou » entre les rangs possibles.

Donc $\liminf_n A_n$ est l'événement « à partir d'un certain rang, tous les $A_n$ se produisent » : après un certain moment, il n'y a plus que des « oui ».

Par exemple, si $A_n$ se produit exactement lorsque $n$ est pair, la suite de réponses est

$$
\text{non, oui, non, oui, non, oui, }\ldots
$$

Il y a une infinité de « oui », donc on est dans $\limsup_n A_n$, mais jamais uniquement des « oui » à partir d'un rang : on n'est pas dans $\liminf_n A_n$.

En particulier,

$$
\liminf_n A_n\subseteq\limsup_n A_n.
$$

### 1. Première implication

On suppose

$$
\sum_{n\geq0}\mathbb P(A_n)<\infty.
$$

Il faut montrer que la probabilité d'avoir une infinité de « oui » est nulle. Pour cela, on introduit, pour chaque rang $n$,

$$
B_n=\bigcup_{k=n}^{\infty}A_k.
$$

$B_n$ est l'événement : « il se produit encore au moins un $A_k$ après le rang $n$ ». Les événements $(B_n)$ sont décroissants : demander qu'un événement se produise après $n+1$ est plus contraignant que de le demander après $n$.

$$
B_0\supseteq B_1\supseteq B_2\supseteq\cdots
$$

De plus,

$$
\limsup_n A_n=\bigcap_nB_n.
$$

En effet, être dans tous les $B_n$ signifie que, quel que soit le rang à partir duquel on regarde, il reste au moins un événement $A_k$ qui se produit plus loin : les $A_n$ se produisent donc une infinité de fois.

Comme $(B_n)$ décroît, la continuité décroissante de la probabilité donne

$$
\mathbb P(\limsup_nA_n)=\lim_{n\to\infty}\mathbb P(B_n).
$$

D'autre part, l'inégalité de l'union donne

$$
\mathbb P(B_n)
\leq\sum_{k=n}^{\infty}\mathbb P(A_k).
$$

La somme de droite est le **reste de la série** à partir du rang $n$ :

$$
\sum_{k=n}^{\infty}\mathbb P(A_k)
=\mathbb P(A_n)+\mathbb P(A_{n+1})+\mathbb P(A_{n+2})+\cdots.
$$

Puisque la série totale converge, ce reste tend vers $0$ lorsque $n$ tend vers l'infini. On a donc

$$
0\leq\mathbb P(B_n)
\leq\sum_{k=n}^{\infty}\mathbb P(A_k)
\xrightarrow[n\to\infty]{}0.
$$

Par conséquent,

$$
\mathbb P(\limsup_nA_n)=\lim_{n\to\infty}\mathbb P(B_n)=0.
$$

Donc

$$
\boxed{\sum_n\mathbb P(A_n)<\infty
\quad\Longrightarrow\quad
\mathbb P(\limsup_nA_n)=0.}
$$

### 2. Réciproque sous indépendance

On veut montrer :

$$
\text{si les }(A_n)\text{ sont indépendants et }\mathbb P(\limsup_nA_n)=0,
\quad\text{alors}\quad
\sum_n\mathbb P(A_n)<\infty.
$$

Raisonnons par contraposée. On suppose donc

$$
\sum_{n\geq0}\mathbb P(A_n)=\infty.
$$

et l'on va montrer que $\mathbb P(\limsup_nA_n)=1$. On reprend

$$
B_n=\bigcup_{k=n}^{\infty}A_k,
$$

qui signifie : « au moins un événement se produit après le rang $n$ ». Son complémentaire est

$$
B_n^c=\bigcap_{k=n}^{\infty}A_k^c,
$$

c'est-à-dire : « à partir du rang $n$, aucun événement $A_k$ ne se produit ».

Fixons $n$, puis, pour $m\geq n$, posons

$$
C_m=\bigcap_{k=n}^m A_k^c.
$$

$C_m$ est l'événement : « entre les rangs $n$ et $m$, aucun $A_k$ ne se produit ». L'indépendance donne

$$
\mathbb P(C_m)
=\prod_{k=n}^m(1-\mathbb P(A_k)).
$$

Pour toute probabilité $\mathbb P(A_k)\in[0,1]$, on a $1-\mathbb P(A_k)\leq e^{-\mathbb P(A_k)}$. En multipliant ces inégalités, et en utilisant $e^{-a}e^{-b}=e^{-(a+b)}$, on obtient

$$
0\leq\mathbb P(C_m)
\leq\prod_{k=n}^m e^{-\mathbb P(A_k)}
=\exp\!\left(-\sum_{k=n}^m\mathbb P(A_k)\right).
$$

La série étant divergente à termes positifs, même après avoir retiré ses premiers termes, on a

$$
\sum_{k=n}^m\mathbb P(A_k)\xrightarrow[m\to\infty]{}+\infty.
$$

Le majorant exponentiel tend donc vers $0$. Par encadrement,

$$
\mathbb P(C_m)\xrightarrow[m\to\infty]{}0.
$$

Les événements $C_m$ sont décroissants et

$$
\bigcap_{m\geq n}C_m
=\bigcap_{k=n}^{\infty}A_k^c
=B_n^c.
$$

La continuité décroissante donne alors

$$
\mathbb P(B_n^c)
=\lim_{m\to\infty}\mathbb P(C_m)
=0.
$$

Ainsi, pour tout $n$,

$$
\mathbb P(B_n)=1-\mathbb P(B_n^c)=1.
$$

Enfin, $\limsup_n A_n=\bigcap_nB_n$, et les $B_n$ sont décroissants. Une dernière application de la continuité décroissante donne

$$
\mathbb P(\limsup_nA_n)
=\lim_{n\to\infty}\mathbb P(B_n)
=1.
$$

Nous avons démontré la contraposée, donc

$$
\boxed{
\text{indépendance et }\mathbb P(\limsup_nA_n)=0
\quad\Longrightarrow\quad
\sum_n\mathbb P(A_n)<\infty.}
$$

> [!note]
> Sous indépendance, le résultat usuel s'énonce souvent ainsi : si $\sum_n\mathbb P(A_n)=\infty$, alors les événements $A_n$ se produisent infiniment souvent avec probabilité $1$.

---

## Exercice 7 - Modes de convergence

> [!question] Énoncé
> On considère des variables aléatoires réelles.
>
> 1. Montrer que si $X_n\to X$ p.s. (resp. en probabilité), alors, pour toute fonction continue $f:\mathbb R\to\mathbb R$, $f(X_n)\to f(X)$ p.s. (resp. en probabilité).
> 2. Montrer que $(X_n)$ converge en probabilité vers $X$ si et seulement si
>    $$
>    \lim_{n\to\infty}\mathbb E\!\left(\frac{|X_n-X|}{1+|X_n-X|}\right)=0.
>    $$
> 3. Montrer que la convergence dans $L^p$, pour $p\in\mathbb N^*$, implique la convergence en probabilité.
> 4. Montrer que la convergence presque sûre implique la convergence en probabilité.
> 5. Montrer que, si $(X_n)$ converge en probabilité vers $X$, elle possède une sous-suite qui converge presque sûrement vers $X$.

### Correction

### 1. Stabilité par une fonction continue

#### Convergence presque sûre

Supposons que $X_n\to X$ presque sûrement. Par définition, il existe un événement $\Omega_0$ de probabilité $1$ tel que, pour tout $\omega\in\Omega_0$,

$$
X_n(\omega)\longrightarrow X(\omega).
$$

Fixons $\omega\in\Omega_0$. Comme $f$ est continue au point $X(\omega)$, la convergence de la suite réelle $(X_n(\omega))$ entraîne

$$
f(X_n(\omega))\longrightarrow f(X(\omega)).
$$

Cette convergence ayant lieu sur l'événement $\Omega_0$ de probabilité $1$, on obtient

$$
\boxed{X_n\xrightarrow{\mathrm{p.s.}}X
\quad\Longrightarrow\quad
f(X_n)\xrightarrow{\mathrm{p.s.}}f(X).}
$$

### 3. La convergence dans $L^p$ implique la convergence en probabilité

Supposons que $X_n\to X$ dans $L^p$, avec $p\in\mathbb N^*$. Cela signifie

$$
\mathbb E[|X_n-X|^p]\longrightarrow0.
$$

Fixons $\varepsilon>0$. En appliquant l'inégalité de Markov à la variable positive $|X_n-X|^p$, on obtient

$$
\begin{aligned}
\mathbb P(|X_n-X|>\varepsilon)
&=\mathbb P(|X_n-X|^p>\varepsilon^p)\\
&\leq\frac{\mathbb E[|X_n-X|^p]}{\varepsilon^p}
\xrightarrow[n\to\infty]{}0.
\end{aligned}
$$

Donc

$$
\boxed{X_n\xrightarrow{L^p}X
\quad\Longrightarrow\quad
X_n\xrightarrow{\mathbb P}X.}
$$

### 4. La convergence presque sûre implique la convergence en probabilité

Supposons que $X_n\to X$ presque sûrement et fixons $\varepsilon>0$. Posons

$$
I_n=\mathbf 1_{\{|X_n-X|>\varepsilon\}}.
$$

Pour presque toute issue $\omega$, la suite $|X_n(\omega)-X(\omega)|$ tend vers $0$. À partir d'un certain rang, elle est donc inférieure ou égale à $\varepsilon$, ce qui montre que

$$
I_n\longrightarrow0
\qquad\text{presque sûrement}.
$$

De plus, $0\leq I_n\leq1$. Le théorème de convergence dominée donne alors

$$
\mathbb P(|X_n-X|>\varepsilon)
=\mathbb E[I_n]
\longrightarrow0.
$$

Comme ceci vaut pour tout $\varepsilon>0$,

$$
\boxed{X_n\xrightarrow{\mathrm{p.s.}}X
\quad\Longrightarrow\quad
X_n\xrightarrow{\mathbb P}X.}
$$

### 5. Extraction d'une sous-suite convergeant presque sûrement

Supposons que $X_n\to X$ en probabilité. Pour chaque $k\geq1$, on a en particulier

$$
\mathbb P(|X_n-X|>2^{-k})\xrightarrow[n\to\infty]{}0.
$$

On peut donc construire par récurrence une suite strictement croissante d'indices $(n_k)_{k\geq1}$ telle que

$$
\mathbb P(|X_{n_k}-X|>2^{-k})\leq2^{-k}.
$$

Posons

$$
A_k=\{|X_{n_k}-X|>2^{-k}\}.
$$

Alors

$$
\sum_{k=1}^{\infty}\mathbb P(A_k)
\leq\sum_{k=1}^{\infty}2^{-k}
=1<\infty.
$$

Le premier lemme de Borel-Cantelli, démontré à l'exercice 6, s'applique sans hypothèse d'indépendance et donne

$$
\mathbb P(A_k\text{ se produit une infinité de fois})=0.
$$

Ainsi, presque sûrement, il existe un rang $k_0(\omega)$ tel que, pour tout $k\geq k_0(\omega)$,

$$
|X_{n_k}(\omega)-X(\omega)|\leq2^{-k}.
$$

Comme $2^{-k}\to0$, on conclut que

$$
X_{n_k}(\omega)\longrightarrow X(\omega)
$$

pour presque toute issue $\omega$. Par conséquent,

$$
\boxed{
X_n\xrightarrow{\mathbb P}X
\quad\Longrightarrow\quad
\text{il existe }(n_k)\text{ telle que }
X_{n_k}\xrightarrow{\mathrm{p.s.}}X.}
$$

> [!summary] Relations obtenues
> Les questions précédentes établissent notamment
> $$
> X_n\xrightarrow{L^p}X
> \quad\Longrightarrow\quad
> X_n\xrightarrow{\mathbb P}X,
> $$
> et
> $$
> X_n\xrightarrow{\mathrm{p.s.}}X
> \quad\Longrightarrow\quad
> X_n\xrightarrow{\mathbb P}X.
> $$
> La convergence en probabilité n'implique pas en général la convergence presque sûre de toute la suite, mais elle permet toujours d'en extraire une sous-suite qui converge presque sûrement.

---

## Exercice 8 - Convergence de variables aléatoires

> [!question] Énoncé
> Déterminer sans calcul les limites suivantes.
>
> 1. Pour une fonction continue $f:[0,1]\to\mathbb R$,
>    $$
>    \lim_{n\to\infty}
>    \int_{[0,1]^n}
>    f\!\left(\frac{x_1+\cdots+x_n}{n}\right)
>    \mathrm dx_1\cdots\mathrm dx_n.
>    $$
> 2. Pour une fonction continue $f:[0,1]\to\mathbb R$ et $p\in[0,1]$,
>    $$
>    \lim_{n\to\infty}
>    \sum_{k=0}^n\binom nk p^k(1-p)^{n-k}f\!\left(\frac kn\right).
>    $$
> 3. Pour une fonction continue et bornée $f:\mathbb R_+\to\mathbb R$ et $\lambda>0$,
>    $$
>    \lim_{n\to\infty}
>    \sum_{k=0}^{\infty}e^{-\lambda n}
>    \frac{(\lambda n)^k}{k!}f\!\left(\frac kn\right).
>    $$

### Correction

Dans les trois questions, l'expression étudiée est l'espérance d'une fonction continue évaluée en une moyenne empirique. La loi des grands nombres permet donc d'identifier immédiatement la limite.

### 1. Moyenne de variables uniformes

Soit $(U_i)_{i\geq1}$ une suite de variables indépendantes de loi uniforme sur $[0,1]$. La densité jointe de $(U_1,\ldots,U_n)$ vaut $1$ sur $[0,1]^n$, donc l'intégrale s'écrit

$$
I_n
=\mathbb E\!\left[
f\!\left(\frac{U_1+\cdots+U_n}{n}\right)
\right].
$$

Or $\mathbb E[U_1]=1/2$. Par la loi des grands nombres,

$$
\frac{U_1+\cdots+U_n}{n}
\xrightarrow[n\to\infty]{\mathrm{p.s.}}\frac12.
$$

La continuité de $f$ donne alors

$$
f\!\left(\frac{U_1+\cdots+U_n}{n}\right)
\xrightarrow[n\to\infty]{\mathrm{p.s.}}f\!\left(\frac12\right).
$$

Comme $f$ est continue sur le compact $[0,1]$, elle y est bornée. Le théorème de convergence dominée permet donc de passer à l'espérance :

$$
\boxed{I_n\longrightarrow f\!\left(\frac12\right).}
$$

### 2. Moyenne de variables de Bernoulli

Soit $X_n\sim\mathcal B(n,p)$. La somme proposée est exactement

$$
J_n=\mathbb E\!\left[f\!\left(\frac{X_n}{n}\right)\right].
$$

On peut construire une suite $(B_i)_{i\geq1}$ de variables indépendantes de loi de Bernoulli de paramètre $p$ et écrire $X_n=B_1+\cdots+B_n$. La loi des grands nombres donne

$$
\frac{X_n}{n}
=\frac{B_1+\cdots+B_n}{n}
\xrightarrow[n\to\infty]{\mathrm{p.s.}}p.
$$

Par continuité de $f$, puis par convergence dominée puisque $f$ est bornée sur $[0,1]$,

$$
\boxed{J_n\longrightarrow f(p).}
$$

### 3. Moyenne de variables de Poisson

Soit $X_n\sim\mathcal P(\lambda n)$. La série étudiée vaut

$$
K_n=\mathbb E\!\left[f\!\left(\frac{X_n}{n}\right)\right].
$$

La somme de $n$ variables indépendantes de loi $\mathcal P(\lambda)$ suit la loi $\mathcal P(\lambda n)$. On peut donc construire une suite $(Y_i)_{i\geq1}$ telle que

$$
X_n=Y_1+\cdots+Y_n,
\qquad
Y_i\overset{\mathrm{i.i.d.}}{\sim}\mathcal P(\lambda).
$$

Puisque $\mathbb E[Y_1]=\lambda$, la loi des grands nombres donne

$$
\frac{X_n}{n}
=\frac{Y_1+\cdots+Y_n}{n}
\xrightarrow[n\to\infty]{\mathrm{p.s.}}\lambda.
$$

La fonction $f$ étant continue et bornée sur $\mathbb R_+$, la convergence dominée entraîne

$$
\boxed{K_n\longrightarrow f(\lambda).}
$$

---

## Exercice 10 - Lemme de Slutsky

> [!question] Énoncé
>
> 1. En utilisant des fonctions caractéristiques, montrer que si $X_n$ converge en loi vers $X$ et si $Y_n$ converge en loi vers une constante $c$, alors le couple $(X_n,Y_n)$ converge en loi vers $(X,c)$.
> 2. Trouver des suites de variables aléatoires telles que $X_n$ converge en loi vers $X$ et $Y_n$ converge en loi vers une variable aléatoire non constante $Y$, mais où le couple $(X_n,Y_n)$ ne converge pas en loi.

### Correction

### 1. Convergence du couple lorsque la seconde limite est constante

La convergence en loi de $Y_n$ vers la constante $c$ implique sa convergence en probabilité vers $c$.

Pour $(s,t)\in\mathbb R^2$, la fonction caractéristique du couple $(X_n,Y_n)$ est

$$
\varphi_{(X_n,Y_n)}(s,t)
=\mathbb E\!\left[e^{i(sX_n+tY_n)}\right].
$$

La fonction caractéristique de $(X,c)$ vaut

$$
\varphi_{(X,c)}(s,t)
=\mathbb E\!\left[e^{i(sX+tc)}\right]
=e^{itc}\varphi_X(s).
$$

On compare les deux expressions en ajoutant puis en retranchant $e^{itc}\varphi_{X_n}(s)$ :

$$
\begin{aligned}
&\left|
\varphi_{(X_n,Y_n)}(s,t)-e^{itc}\varphi_X(s)
\right|\\
&\quad\leq
\mathbb E\!\left[
\left|e^{itY_n}-e^{itc}\right|
\right]
+\left|\varphi_{X_n}(s)-\varphi_X(s)\right|.
\end{aligned}
$$

Le second terme tend vers $0$ puisque $X_n$ converge en loi vers $X$.

Pour le premier, la continuité de $y\mapsto e^{ity}$ et la convergence en probabilité de $Y_n$ vers $c$ donnent

$$
e^{itY_n}\xrightarrow{\mathbb P}e^{itc}.
$$

De plus, $|e^{itY_n}-e^{itc}|\leq2$. Cette convergence en probabilité, jointe à la borne uniforme, implique

$$
\mathbb E\!\left[
\left|e^{itY_n}-e^{itc}\right|
\right]\longrightarrow0.
$$

En effet, pour tout $\delta>0$, l'inégalité $|e^{ita}-e^{itb}|\leq |t|\,|a-b|$ donne

$$
\mathbb E\!\left[|e^{itY_n}-e^{itc}|\right]
\leq |t|\delta+2\,\mathbb P(|Y_n-c|>\delta),
$$

puis on fait tendre $n$ vers l'infini et enfin $\delta$ vers $0$.

Ainsi, pour tout $(s,t)\in\mathbb R^2$,

$$
\varphi_{(X_n,Y_n)}(s,t)
\longrightarrow
e^{itc}\varphi_X(s)
=\varphi_{(X,c)}(s,t).
$$

Le théorème de continuité de Lévy permet de conclure :

$$
\boxed{(X_n,Y_n)\xrightarrow{\mathcal L}(X,c).}
$$

> [!important]
> Aucune indépendance entre $X_n$ et $Y_n$ n'est utilisée.

### 2. La conclusion échoue pour une limite non constante

Soit $Z\sim\mathcal N(0,1)$ et posons

$$
X_n=Z,
\qquad
Y_n=(-1)^nZ.
$$

Pour tout $n$, $X_n\sim\mathcal N(0,1)$ et, par symétrie de la loi normale, $Y_n\sim\mathcal N(0,1)$. Par conséquent,

$$
X_n\xrightarrow{\mathcal L}Z,
\qquad
Y_n\xrightarrow{\mathcal L}Z.
$$

Cependant, les lois jointes alternent :

$$
(X_{2n},Y_{2n})=(Z,Z),
\qquad
(X_{2n+1},Y_{2n+1})=(Z,-Z).
$$

Ces deux couples n'ont pas la même loi. Par exemple, leurs fonctions caractéristiques sont respectivement

$$
\exp\!\left(-\frac{(s+t)^2}{2}\right)
\qquad\text{et}\qquad
\exp\!\left(-\frac{(s-t)^2}{2}\right).
$$

Les sous-suites paires et impaires ayant deux lois limites différentes, le couple $(X_n,Y_n)$ ne converge pas en loi.

---

## Exercice 11 - Théorème central limite

> [!question] Énoncé
> Soit $(X_n)$ une suite de variables aléatoires i.i.d. d'espérance nulle et de variance $1$. On pose
> $$
> S_n=X_1+\cdots+X_n,
> \qquad
> Y_n=\frac{S_n}{\sqrt n}.
> $$
>
> 1. Donner la fonction caractéristique de $X_1$.
> 2. Calculer sa dérivée et sa dérivée seconde, puis en déduire son développement limité à l'ordre $2$ au voisinage de $0$.
> 3. Exprimer la fonction caractéristique de $Y_n$ en fonction de celle de $X_1$.
> 4. En déduire sa limite lorsque $n\to\infty$ et conclure.

### Correction

### 1. Fonction caractéristique de $X_1$

Notons

$$
\varphi(t)=\varphi_{X_1}(t)
=\mathbb E[e^{itX_1}],
\qquad t\in\mathbb R.
$$

La loi de $X_1$ n'étant pas précisée davantage, cette expression est la forme générale de sa fonction caractéristique.

### 2. Développement limité au voisinage de zéro

Comme $X_1$ possède un moment d'ordre $2$, on peut dériver deux fois sous l'espérance :

$$
\varphi'(t)=i\,\mathbb E[X_1e^{itX_1}],
\qquad
\varphi''(t)=-\mathbb E[X_1^2e^{itX_1}].
$$

En $t=0$,

$$
\varphi(0)=1,
\qquad
\varphi'(0)=i\mathbb E[X_1]=0,
\qquad
\varphi''(0)=-\mathbb E[X_1^2]=-1,
$$

car $\mathbb E[X_1]=0$ et $\operatorname{Var}(X_1)=\mathbb E[X_1^2]=1$. La formule de Taylor donne donc

$$
\boxed{
\varphi(t)=1-\frac{t^2}{2}+o(t^2)
\qquad(t\to0).}
$$

### 3. Fonction caractéristique de $Y_n$

Par indépendance des $X_i$,

$$
\begin{aligned}
\varphi_{Y_n}(t)
&=\mathbb E\!\left[
\exp\!\left(it\frac{X_1+\cdots+X_n}{\sqrt n}\right)
\right]\\
&=\prod_{j=1}^n
\mathbb E\!\left[e^{i(t/\sqrt n)X_j}\right]\\
&=\left[\varphi\!\left(\frac{t}{\sqrt n}\right)\right]^n.
\end{aligned}
$$

### 4. Passage à la limite

Pour $t$ fixé, le développement limité précédent donne

$$
\varphi\!\left(\frac{t}{\sqrt n}\right)
=1-\frac{t^2}{2n}+o\!\left(\frac1n\right).
$$

Par conséquent,

$$
\varphi_{Y_n}(t)
=\left(
1-\frac{t^2}{2n}+o\!\left(\frac1n\right)
\right)^n
\longrightarrow e^{-t^2/2}.
$$

La fonction $t\mapsto e^{-t^2/2}$ est la fonction caractéristique de la loi $\mathcal N(0,1)$. Le théorème de continuité de Lévy donne donc

$$
\boxed{
\frac{X_1+\cdots+X_n}{\sqrt n}
\xrightarrow{\mathcal L}\mathcal N(0,1).}
$$

---

## Exercice 14 - Indépendance et vecteurs gaussiens

> [!question] Énoncé
> Soient $X$ et $Y$ deux variables aléatoires indépendantes de loi $\mathcal N(0,1)$. Montrer que $X+Y$ et $X-Y$ sont indépendantes.

### Correction

Le vecteur $(X,Y)$ est gaussien, car ses composantes sont des variables gaussiennes indépendantes. Toute transformation linéaire d'un vecteur gaussien étant encore un vecteur gaussien,

$$
\begin{pmatrix}
X+Y\\
X-Y
\end{pmatrix}
=
\begin{pmatrix}
1&1\\
1&-1
\end{pmatrix}
\begin{pmatrix}
X\\
Y
\end{pmatrix}
$$

est également un vecteur gaussien.

Calculons la covariance de ses deux composantes :

$$
\begin{aligned}
\operatorname{Cov}(X+Y,X-Y)
&=\operatorname{Var}(X)-\operatorname{Cov}(X,Y)\\
&\quad+\operatorname{Cov}(Y,X)-\operatorname{Var}(Y)\\
&=1-0+0-1\\
&=0.
\end{aligned}
$$

Les composantes d'un vecteur gaussien sont indépendantes si et seulement si elles sont non corrélées. On en déduit

$$
\boxed{X+Y\ \perp\!\!\!\perp\ X-Y.}
$$

> [!warning]
> La covariance nulle suffit ici parce que le couple $(X+Y,X-Y)$ est gaussien. Ce raisonnement serait faux pour un couple quelconque.

---

## Exercice 19 - Marche aléatoire sur un graphe

> [!question] Énoncé
> On considère le graphe non orienté
> $$
> 1\;---\;2\;---\;3.
> $$
> À chaque étape, la marche choisit uniformément l'un des voisins du sommet courant.
>
> 1. Écrire la matrice de transition $P$.
> 2. Deviner puis vérifier une probabilité stationnaire $\pi$.
> 3. En partant du sommet $1$, la loi de $X_n$ converge-t-elle vers $\pi$ ? Expliquer le rôle de la périodicité.
> 4. On rend la chaîne paresseuse : à chaque étape, elle reste sur place avec probabilité $1/2$ et effectue l'étape précédente avec probabilité $1/2$. Écrire
>    $$
>    P_{\mathrm{lazy}}=\frac12(I+P).
>    $$
>    Montrer que $\pi$ est encore stationnaire et expliquer pourquoi on s'attend cette fois à une convergence vers $\pi$.
> 5. Généraliser : pour une marche simple sur un graphe fini non orienté sans sommet isolé, montrer que
>    $$
>    \pi(v)=\frac{\deg(v)}{\sum_u\deg(u)}
>    $$
>    est stationnaire.

> [!note] Portée de la correction manuscrite
> Seules les questions 1 et 2 sont corrigées dans le manuscrit. Les questions 3 à 5 ne sont donc pas traitées ici.

### Correction

### 1. Matrice de transition

Depuis le sommet $1$, la marche va nécessairement au sommet $2$. Depuis le sommet $3$, elle va également nécessairement au sommet $2$. Depuis le sommet $2$, elle choisit chacun de ses deux voisins avec probabilité $1/2$.

En ordonnant les états comme $(1,2,3)$, on obtient

$$
\boxed{
P=
\begin{pmatrix}
0&1&0\\
\tfrac12&0&\tfrac12\\
0&1&0
\end{pmatrix}.}
$$

### 2. Probabilité stationnaire

Une probabilité stationnaire est

$$
\boxed{\pi=\left(\frac14,\frac12,\frac14\right).}
$$

Vérifions que $\pi P=\pi$ :

$$
\begin{aligned}
\pi P
&=\left(\frac14,\frac12,\frac14\right)
\begin{pmatrix}
0&1&0\\
\tfrac12&0&\tfrac12\\
0&1&0
\end{pmatrix}\\
&=\left(\frac14,\frac12,\frac14\right)
=\pi.
\end{aligned}
$$

La condition de stationnarité est donc satisfaite.

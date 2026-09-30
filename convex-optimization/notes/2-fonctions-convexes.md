---
title: Fonctions convexes - poly de cours
tags:
  - optimisation-convexe
  - convexite
  - mva
source: "[[cours-1.pdf]]"
prerequis: "[[1-ensembles-convexes]]"
---

# Fonctions convexes

> [!abstract] Fil directeur
> Une fonction convexe modélise un coût sans faux creux : le coût d'un compromis entre deux décisions ne dépasse pas le compromis de leurs coûts. Cette propriété rend les minima accessibles par des arguments géométriques, des gradients et des règles de composition.

Cette note prolonge [[1-ensembles-convexes]]. Elle s'intéresse aux fonctions objectif, à leurs critères de convexité et aux opérations qui préservent cette structure.

## 0. Notations utiles

- $x,y\in\mathbb{R}^n$ sont des vecteurs, et $\theta\in[0,1]$ est un scalaire qui pondère leur mélange.
- $f:\mathbb{R}^n\to\mathbb{R}$ est une fonction réelle ; son domaine $\operatorname{dom}f$ est l'ensemble des entrées où elle est définie et finie.
- Pour $a,x\in\mathbb{R}^n$, $a^Tx=\sum_{i=1}^n a_ix_i$ est leur produit scalaire. Une matrice $A\in\mathbb{R}^{m\times n}$ transforme $x\in\mathbb{R}^n$ en $Ax\in\mathbb{R}^m$.
- $\lVert\cdot\rVert$ désigne une norme, qui mesure une longueur ou une distance.
- $\mathbb{S}^n$ désigne les matrices symétriques de taille $n\times n$. Pour $X\in\mathbb{S}^n$, $X\succeq0$ signifie que $z^TXz\geq0$ pour tout $z\in\mathbb{R}^n$.

Les notions géométriques utilisées ici, notamment ensembles convexes, épigraphes, cônes et hyperplans de support, sont expliquées dans [[1-ensembles-convexes]].

---

## 1. Coût convexe : la corde est au-dessus de la courbe

### 1.1 Quel comportement veut-on éviter ?

Dans un problème de minimisation, les fonctions non convexes peuvent contenir plusieurs vallées séparées par des crêtes. Un algorithme local peut s'arrêter dans une vallée qui n'est pas la meilleure.

Une fonction convexe interdit ce type de géométrie : entre deux points de son graphe, la corde ne passe jamais sous le graphe. Mélanger deux décisions ne produit pas un coût supérieur au mélange de leurs coûts.

### 1.2 Définition

Soit $f:\mathbb{R}^n\to\mathbb{R}$ une fonction, et soit $\operatorname{dom}f\subseteq\mathbb{R}^n$ son domaine, c'est-à-dire l'ensemble des points où elle est définie et finie. La fonction $f$ est **convexe** si son domaine est convexe et si

$$
f\bigl(\theta x+(1-\theta)y\bigr)
\leq
\theta f(x)+(1-\theta)f(y)
$$

pour tous $x,y\in\operatorname{dom}f$ et tout $\theta\in[0,1]$.

Dans cette formule, $x$ et $y$ sont deux entrées de $\mathbb{R}^n$, $f(x)$ et $f(y)$ sont des scalaires, et $\theta$ est le poids du mélange. Le membre de gauche est le coût de la décision moyenne. Le membre de droite est la moyenne des deux coûts. La convexité affirme que prendre le mélange est au moins aussi favorable que la moyenne des coûts.

Une fonction est **concave** si $-f$ est convexe. Elle se comporte alors comme une colline : sa corde est sous son graphe.

Elle est **strictement convexe** si, pour $x\ne y$ et $0<\theta<1$,

$$
f\bigl(\theta x+(1-\theta)y\bigr)
<
\theta f(x)+(1-\theta)f(y).
$$

La stricte convexité interdit les segments plats sur le graphe. Elle est souvent une voie vers l'unicité du minimiseur, lorsque celui-ci existe.

### 1.3 Exemples élémentaires sur $\mathbb{R}$

| Fonction $f(x)$ | Domaine | Forme |
|---|---:|---|
| $ax+b$ | $\mathbb{R}$ | à la fois convexe et concave : c'est une droite |
| $e^{ax}$ | $\mathbb{R}$ | convexe pour tout $a\in\mathbb{R}$ |
| $x^\alpha$ | $\mathbb{R}_{++}$ | convexe si $\alpha\geq1$ ou $\alpha\leq0$ ; concave si $0\leq\alpha\leq1$ |
| $|x|^p$ | $\mathbb{R}$ | convexe si $p\geq1$ |
| $x\log x$ | $\mathbb{R}_{++}$ | convexe ; c'est la négative de l'entropie usuelle |
| $\log x$ | $\mathbb{R}_{++}$ | concave |

Ici $\mathbb{R}_{++}=\{x\in\mathbb{R}\mid x>0\}$. Le domaine compte autant que l'expression : $\log x$ ou $1/x$ ne sont pas des fonctions sur toute la droite réelle.

### 1.4 Exemples vectoriels et matriciels

Les fonctions affines $f(x)=a^Tx+b$ sont toujours à la fois convexes et concaves : le membre de gauche et le membre de droite de l'inégalité de convexité sont exactement égaux.

Toute norme est convexe. Par exemple,

$$
x\longmapsto \lVert x\rVert_p
=\left(\sum_{i=1}^n|x_i|^p\right)^{1/p},\qquad p\geq1,
$$

est convexe. Ce fait est directement lié à l'inégalité triangulaire.

Pour une matrice variable $X\in\mathbb{R}^{m\times n}$, une fonction affine peut s'écrire

$$
f(X)=\operatorname{Tr}(A^TX)+b
=\sum_{i=1}^m\sum_{j=1}^n A_{ij}X_{ij}+b,
$$

où $A\in\mathbb{R}^{m\times n}$ et $b\in\mathbb{R}$ sont fixés. La trace $\operatorname{Tr}(A^TX)$ joue le rôle de produit scalaire entre matrices.

La norme spectrale d'une matrice $X$ est

$$
\lVert X\rVert_2=\sigma_{\max}(X)
=\bigl(\lambda_{\max}(X^TX)\bigr)^{1/2},
$$

où $\sigma_{\max}(X)$ est sa plus grande valeur singulière et $\lambda_{\max}$ la plus grande valeur propre. Elle est convexe ; elle mesure l'amplification maximale que $X$ peut produire sur un vecteur de norme euclidienne $1$.

---

## 2. Trois critères pour reconnaître la convexité

### 2.1 Regarder toutes les droites

Une fonction $f:\mathbb{R}^n\to\mathbb{R}$ est convexe si et seulement si sa restriction à toute droite est convexe. Pour un point de départ $x\in\operatorname{dom}f$ et une direction $v\in\mathbb{R}^n$, on pose

$$
g(t)=f(x+tv),
\qquad
\operatorname{dom}g=\{t\in\mathbb{R}\mid x+tv\in\operatorname{dom}f\}.
$$

Le scalaire $t$ parcourt une droite dans l'espace des entrées. Vérifier que chaque fonction unidimensionnelle $g$ est convexe revient à vérifier la convexité de $f$ dans toutes les directions possibles.

**Exemple - $\log\det$.** Pour $X\in\mathbb{S}_{++}^n$, la fonction

$$
f(X)=\log\det(X)
$$

est concave. Le déterminant mesure le produit des valeurs propres et, pour une matrice de covariance, est relié au volume de l'ellipsoïde d'incertitude. Le long de la droite $X+tV$, avec $V\in\mathbb{S}^n$, on peut décomposer la fonction en une somme de termes de la forme $\log(1+t\lambda_i)$, qui sont concaves sur leur domaine. Cette réduction explique intuitivement l'origine de la concavité matricielle.

### 2.2 Fonction étendue : intégrer les contraintes au coût

Au lieu de porter séparément une fonction et son domaine, on peut définir son **extension à valeurs étendues** :

$$
\widetilde f(x)=
\begin{cases}
f(x),&x\in\operatorname{dom}f,\\
+\infty,&x\notin\operatorname{dom}f.
\end{cases}
$$

Le symbole $+\infty$ n'est pas une valeur numérique ordinaire. Il signifie que le point est interdit. Ainsi, minimiser $\widetilde f$ sur tout $\mathbb{R}^n$ revient à minimiser $f$ seulement sur son domaine.

Cette convention compacte les deux exigences de la convexité dans une unique inégalité :

$$
\widetilde f\bigl(\theta x+(1-\theta)y\bigr)
\leq
\theta\widetilde f(x)+(1-\theta)\widetilde f(y).
$$

Si $x$ ou $y$ est hors domaine, le membre droit est infini et l'inégalité n'impose rien. Si les deux sont dans le domaine mais que leur mélange n'y est pas, le membre gauche serait infini et l'inégalité échouerait. L'écriture force donc implicitement le domaine à être convexe.

### 2.3 Condition du premier ordre : les tangentes sous-estiment globalement

Supposons que $f$ soit différentiable sur un domaine ouvert et convexe. Son gradient est le vecteur

$$
\nabla f(x)=
\left(
\frac{\partial f(x)}{\partial x_1},\ldots,
\frac{\partial f(x)}{\partial x_n}
\right)^T\in\mathbb{R}^n.
$$

Il rassemble les pentes locales dans toutes les directions coordonnées. La fonction $f$ est convexe si et seulement si

$$
f(y)\geq f(x)+\nabla f(x)^T(y-x)
\quad\text{pour tous }x,y\in\operatorname{dom}f.
$$

Le membre droit est l'approximation affine de $f$ près de $x$. La formule affirme que cette tangente ne se contente pas d'être une approximation locale : elle sous-estime $f$ sur tout le domaine. C'est l'analogue fonctionnel d'un hyperplan de support.

En particulier, si $\nabla f(x^*)=0$, alors

$$
f(y)\geq f(x^*)
\quad\text{pour tout }y\in\operatorname{dom}f.
$$

Le point $x^*$ est alors un minimiseur global. Cette conclusion est l'un des grands avantages de la convexité.

### 2.4 Condition du second ordre : regarder la courbure

Si $f$ est deux fois différentiable, sa **matrice hessienne** est

$$
\nabla^2f(x)\in\mathbb{S}^n,
\qquad
\bigl(\nabla^2f(x)\bigr)_{ij}
=\frac{\partial^2 f(x)}{\partial x_i\partial x_j}.
$$

La diagonale contient les courbures suivant chaque axe ; les termes hors diagonale décrivent les interactions entre coordonnées. Sur un domaine ouvert et convexe,

$$
f\ \text{est convexe}
\quad\Longleftrightarrow\quad
\nabla^2f(x)\succeq0\ \text{pour tout }x\in\operatorname{dom}f.
$$

La condition $\nabla^2f(x)\succeq0$ signifie que toute courbure directionnelle est non négative. Si $\nabla^2f(x)\succ0$ partout, la fonction est strictement convexe.

> [!warning] Ce test demande de la régularité
> L'absence de Hessienne ne signifie pas qu'une fonction n'est pas convexe : $x\mapsto|x|$ est convexe mais n'est pas différentiable en $0$. Dans ce cas, la définition, l'épigraph ou les règles de composition sont plus adaptés.

---

## 3. Exemples détaillés de fonctions convexes et concaves

### 3.1 Fonctions quadratiques

Considérons

$$
f(x)=\frac12x^TPx+q^Tx+r,
$$

où $x\in\mathbb{R}^n$ est la variable, $P\in\mathbb{S}^n$, $q\in\mathbb{R}^n$ et $r\in\mathbb{R}$ sont fixés. Les dérivées sont

$$
\nabla f(x)=Px+q,
\qquad
\nabla^2f(x)=P.
$$

La Hessienne ne dépend pas de $x$. La fonction est donc convexe exactement lorsque $P\succeq0$. Le facteur $1/2$ est choisi pour que la dérivée de $\tfrac12x^TPx$ soit simplement $Px$.

### 3.2 Moindres carrés

La fonction de moindres carrés est

$$
f(x)=\lVert Ax-b\rVert_2^2,
$$

où $A\in\mathbb{R}^{m\times n}$, $b\in\mathbb{R}^m$ et $x\in\mathbb{R}^n$. Elle mesure la somme des carrés des résidus entre les prédictions $Ax$ et les observations $b$.

Ses dérivées sont

$$
\nabla f(x)=2A^T(Ax-b),
\qquad
\nabla^2f(x)=2A^TA.
$$

Pour tout vecteur $z$,

$$
z^T(2A^TA)z=2\lVert Az\rVert_2^2\geq0.
$$

La Hessienne est donc toujours positive semi-définie, quelle que soit la matrice $A$. C'est pourquoi les moindres carrés constituent un problème convexe fondamental.

### 3.3 Quadratique sur linéaire

La fonction

$$
f(x,y)=\frac{x^2}{y},
\qquad y>0,
$$

est convexe sur son domaine. Le numérateur pénalise une amplitude $x$, tandis que le dénominateur $y$ joue un rôle d'échelle ou de ressource. Sa Hessienne se factorise en

$$
\nabla^2 f(x,y)
=\frac{2}{y^3}
\begin{pmatrix}y\\-x\end{pmatrix}
\begin{pmatrix}y\\-x\end{pmatrix}^T
\succeq0.
$$

Cette forme est un produit extérieur multiplié par un scalaire positif. Pour tout vecteur $u$, l'expression $u^T\nabla^2f(x,y)u$ devient un carré, et ne peut pas être négative.

### 3.4 Log-sum-exp : un maximum lissé

La fonction

$$
f(x)=\log\left(\sum_{k=1}^n e^{x_k}\right),
\qquad x\in\mathbb{R}^n,
$$

est convexe. Elle est souvent utilisée comme approximation lisse de $\max_k x_k$, car

$$
\max_k x_k
\leq
\log\left(\sum_{k=1}^n e^{x_k}\right)
\leq
\max_k x_k+\log n.
$$

En posant $z_k=e^{x_k}>0$ et

$$
p_k=\frac{z_k}{\sum_{j=1}^n z_j},
$$

le vecteur $p=(p_1,\ldots,p_n)$ est une distribution de probabilités. La Hessienne de $f$ peut se lire comme une matrice de covariance : pour toute direction $v\in\mathbb{R}^n$,

$$
v^T\nabla^2f(x)v
=\sum_{k=1}^np_kv_k^2-
\left(\sum_{k=1}^np_kv_k\right)^2
\geq0.
$$

Le membre droit est la variance des valeurs $v_k$ sous les probabilités $p_k$. Une variance ne peut pas être négative, ce qui donne une interprétation intuitive de la convexité.

### 3.5 Moyenne géométrique

Sur $\mathbb{R}_{++}^n$, la moyenne géométrique

$$
g(x)=\left(\prod_{k=1}^n x_k\right)^{1/n}
$$

est concave. Elle mesure une performance équilibrée : si une coordonnée s'approche de zéro, le produit entier chute. La concavité signifie qu'un mélange de deux allocations a une moyenne géométrique au moins égale au mélange de leurs moyennes géométriques.

---

## 4. Sous-niveaux, épigraphes et Jensen

### 4.1 Ensembles de sous-niveau

Pour une fonction $f:\mathbb{R}^n\to\mathbb{R}$ et un seuil $\alpha\in\mathbb{R}$, le **sous-niveau** d'ordre $\alpha$ est

$$
C_\alpha=\{x\in\operatorname{dom}f\mid f(x)\leq\alpha\}.
$$

Il rassemble toutes les décisions dont le coût ne dépasse pas le budget $\alpha$. Si $f$ est convexe, $C_\alpha$ est convexe : deux décisions sous le budget conservent un mélange dont le coût est sous le même budget.

La réciproque est fausse. Une fonction peut avoir tous ses sous-niveaux convexes sans être convexe ; elle est alors appelée **quasi-convexe**. Par exemple, $f(x)=x^3$ sur $\mathbb{R}$ n'est pas convexe sur toute la droite, mais chacun de ses sous-niveaux est un intervalle de la forme $(-\infty,c]$, qui est convexe.

### 4.2 Épigraphes : voir une fonction comme un ensemble

L'**épigraphe** de $f$ est l'ensemble des points situés au-dessus de son graphe :

$$
\operatorname{epi}f
=\{(x,t)\in\mathbb{R}^n\times\mathbb{R}\mid x\in\operatorname{dom}f,\ f(x)\leq t\}.
$$

Le vecteur $x$ est l'entrée et le scalaire $t$ est une hauteur autorisée. La contrainte $f(x)\leq t$ dit que le point $(x,t)$ est au-dessus de la courbe ou de la surface.

Le fait clé est

$$
f\ \text{est convexe}
\quad\Longleftrightarrow\quad
\operatorname{epi}f\ \text{est un ensemble convexe}.
$$

Cette équivalence relie directement les deux parties du cours. Elle transforme un problème de minimisation

$$
\min_x f(x)
$$

en un problème géométrique équivalent : chercher la plus petite hauteur $t$ telle que $(x,t)$ appartienne à l'épigraphie.

### 4.3 Inégalité de Jensen

Si $f$ est convexe et si $Z$ est une variable aléatoire à valeurs dans le domaine de $f$, alors

$$
f\bigl(\mathbb{E}[Z]\bigr)\leq\mathbb{E}[f(Z)].
$$

Le symbole $\mathbb{E}$ désigne l'espérance, autrement dit la moyenne pondérée par les probabilités. Cette formule dit que le coût de la décision moyenne est inférieur ou égal au coût moyen des décisions aléatoires.

Le cas à deux valeurs redonne exactement la définition : si $Z=x$ avec probabilité $\theta$ et $Z=y$ avec probabilité $1-\theta$, alors

$$
\mathbb{E}[Z]=\theta x+(1-\theta)y,
\qquad
\mathbb{E}[f(Z)]=\theta f(x)+(1-\theta)f(y).
$$

Jensen est donc la version « mélange aléatoire de plusieurs points » de la convexité. Pour une fonction concave, le sens de l'inégalité est inversé.

---

## 5. Règles de construction pour les fonctions convexes

Ces règles sont souvent la manière la plus rapide et la moins calculatoire de prouver la convexité.

### 5.1 Sommes pondérées et composition affine

Si $f_1,\ldots,f_m$ sont convexes et si $\alpha_1,\ldots,\alpha_m\geq0$, alors

$$
f(x)=\sum_{i=1}^m\alpha_i f_i(x)
$$

est convexe. Les poids doivent être non négatifs : multiplier une inégalité de convexité par un nombre négatif la renverserait.

Si $f:\mathbb{R}^m\to\mathbb{R}$ est convexe et $g(x)=Ax+b$ est affine, alors

$$
x\longmapsto f(Ax+b)
$$

est convexe sur les points où $Ax+b$ appartient au domaine de $f$. L'application affine ne déforme pas les mélanges ; elle les transmet à $f$.

**Exemple - barrière logarithmique.** Pour des contraintes $a_i^Tx<b_i$, on considère

$$
f(x)=-\sum_{i=1}^m\log(b_i-a_i^Tx),
\qquad
\operatorname{dom}f=\{x\mid a_i^Tx<b_i\ \text{pour tout }i\}.
$$

Chaque terme tend vers $+\infty$ lorsqu'on approche la frontière $a_i^Tx=b_i$. La fonction agit comme une barrière douce qui maintient les itérés à l'intérieur des contraintes. Elle est convexe car $-\log$ est convexe et décroissante, tandis que $b_i-a_i^Tx$ est affine.

Un autre exemple immédiat est $x\mapsto\lVert Ax+b\rVert$, pour n'importe quelle norme : c'est une norme convexe composée avec une application affine.

### 5.2 Maximum ponctuel et supremum

Si $f_1,\ldots,f_m$ sont convexes, alors

$$
f(x)=\max\{f_1(x),\ldots,f_m(x)\}
$$

est convexe. Prendre le maximum revient à imposer simultanément plusieurs bornes : son épigraphe est l'intersection des épigraphes de tous les $f_i$.

Un cas important est la fonction affine par morceaux

$$
f(x)=\max_{1\leq i\leq m}(a_i^Tx+b_i).
$$

Elle est constituée de plans affines, mais elle conserve uniquement leur enveloppe supérieure ; cela produit une surface convexe avec des arêtes éventuelles.

Plus généralement, si $F(x,y)$ est convexe en $x$ pour chaque $y\in A$, alors

$$
g(x)=\sup_{y\in A}F(x,y)

$$

est convexe. Le supremum est la plus petite borne supérieure, même s'il n'est pas nécessairement atteint.

Trois exemples importants sont :

$$
s_C(x)=\sup_{y\in C}y^Tx
\quad\text{(fonction support de $C$)},
$$

$$
x\longmapsto\sup_{y\in C}\lVert x-y\rVert
\quad\text{(distance au point le plus éloigné de $C$)},
$$

et, pour $X\in\mathbb{S}^n$,

$$
\lambda_{\max}(X)=\sup_{\lVert y\rVert_2=1}y^TXy.
$$

Dans la dernière formule, chaque expression $y^TXy$ est affine en $X$ lorsque $y$ est fixé. La plus grande valeur propre est donc une fonction convexe de la matrice symétrique $X$.

### 5.3 Composition avec une fonction scalaire

Considérons $g:\mathbb{R}^n\to\mathbb{R}$ puis $h:\mathbb{R}\to\mathbb{R}$, et

$$
f(x)=h(g(x)).
$$

On ne peut pas composer arbitrairement deux fonctions convexes : il faut que le sens de variation de $h$ soit compatible avec l'inégalité produite par $g$.

Deux règles sûres sont :

$$
\begin{array}{c|c|c}
g & h & h\circ g\\
\hline
\text{convexe} & \text{convexe et croissante} & \text{convexe}\\
\text{concave} & \text{convexe et décroissante} & \text{convexe}
\end{array}
$$

La monotonie doit être valide sur tout le domaine étendu de $h$. Par exemple :

- $x\mapsto e^{g(x)}$ est convexe si $g$ est convexe, car l'exponentielle est convexe et croissante ;
- $x\mapsto1/g(x)$ est convexe si $g$ est concave et strictement positive, car $u\mapsto1/u$ est convexe et décroissante sur $u>0$.

**Piège classique.** La fonction $u\mapsto u^2$ est convexe, mais elle n'est pas croissante sur toute $\mathbb{R}$. On ne peut donc pas conclure automatiquement que $g(x)^2$ est convexe dès que $g$ est convexe. Il faut examiner le domaine et la monotonie ; si $g\geq0$, la règle redevient applicable.

### 5.4 Composition vectorielle

Soit $g=(g_1,\ldots,g_k):\mathbb{R}^n\to\mathbb{R}^k$ et $h:\mathbb{R}^k\to\mathbb{R}$. On définit

$$
f(x)=h\bigl(g_1(x),\ldots,g_k(x)\bigr).
$$

La version vectorielle reprend la même logique coordonnée par coordonnée :

- si chaque $g_i$ est convexe, et si $h$ est convexe et croissante dans chacun de ses arguments, alors $f$ est convexe ;
- si chaque $g_i$ est concave, et si $h$ est convexe et décroissante dans chacun de ses arguments, alors $f$ est convexe.

Deux applications utiles :

$$
\sum_{i=1}^m\log g_i(x)
$$

est concave si les $g_i$ sont concaves et positives, tandis que

$$
\log\left(\sum_{i=1}^m e^{g_i(x)}\right)

$$

est convexe si les $g_i$ sont convexes.

### 5.5 Minimisation partielle

Supposons que $F(x,y)$ soit convexe conjointement en $x\in\mathbb{R}^n$ et $y\in\mathbb{R}^p$, et que $C\subseteq\mathbb{R}^p$ soit convexe. La fonction de valeur

$$
g(x)=\inf_{y\in C}F(x,y)
$$

est convexe.

Cette opération élimine une variable auxiliaire $y$ en la choisissant de façon optimale pour chaque $x$. Elle est surprenante à première vue, car un infimum de fonctions convexes quelconques n'est pas nécessairement convexe. La convexité jointe en $(x,y)$ est la condition qui rend la conclusion vraie. Géométriquement, l'épigraphe de $g$ s'obtient par projection d'un ensemble convexe dans un espace de dimension supérieure.

**Exemple - distance à un ensemble convexe.** Si $S\subseteq\mathbb{R}^n$ est convexe, alors

$$
\operatorname{dist}(x,S)=\inf_{y\in S}\lVert x-y\rVert

$$

est convexe en $x$. Le point $y$ représente le point de $S$ le plus proche de $x$ ; la norme de $x-y$ est convexe conjointement en $(x,y)$.

**Exemple - complément de Schur.** Soit

$$
F(x,y)=x^TAx+2x^TBy+y^TCy,
$$

où $A\in\mathbb{S}^n$, $C\in\mathbb{S}^p$, $B\in\mathbb{R}^{n\times p}$, et supposons

$$
\begin{pmatrix}A&B\\B^T&C\end{pmatrix}\succeq0,
\qquad C\succ0.
$$

Pour un $x$ fixé, le minimiseur en $y$ est $y^*=-C^{-1}B^Tx$. Après substitution,

$$
\inf_y F(x,y)=x^T\bigl(A-BC^{-1}B^T\bigr)x.
$$

La fonction de gauche est convexe par minimisation partielle. Sa matrice de courbure est donc positive semi-définie :

$$
A-BC^{-1}B^T\succeq0.
$$

Cette matrice est le **complément de Schur** de $C$. Ce calcul illustre comment une variable peut être éliminée tout en conservant la structure convexe.

---

## 6. Carte mentale et méthode pratique

### 6.1 Le fil logique du chapitre

```text
segments contenus dans les contraintes
            │
            ▼
     ensembles convexes
            │
            ├── intersections, images affines, perspectives
            ├── hyperplans, polyèdres, boules, cônes, matrices PSD
            └── séparation et support
                      │
                      ▼
       épigraphe d'une fonction
                      │
                      ▼
           fonctions convexes
            │
            ├── tangentes sous-estimatrices et Hessiennes PSD
            ├── sous-niveaux convexes et Jensen
            └── sommes, maximums, compositions, minimisation partielle
```

L'épigraphe est le pont essentiel : une fonction convexe est une contrainte convexe déguisée dans un espace avec une coordonnée de hauteur supplémentaire.

### 6.2 Comment prouver qu'un ensemble est convexe ?

1. Identifier s'il s'agit d'un objet standard : demi-espace, boule de norme, ellipsoïde, polyèdre, cône PSD.
2. Chercher une écriture comme intersection de contraintes convexes.
3. Chercher une image réciproque par une application affine, une perspective ou une transformation linéaire-fractionnaire.
4. Si aucune structure n'apparaît, repartir de deux points $x_1,x_2$ et vérifier directement que leur mélange reste admissible.

### 6.3 Comment prouver qu'une fonction est convexe ?

1. Vérifier d'abord son domaine : il doit être convexe.
2. Si la fonction est deux fois dérivable et que la Hessienne est accessible, tester $\nabla^2f(x)\succeq0$.
3. Si elle est différentiable, chercher une inégalité de premier ordre ou un sous-estimateur affine global.
4. Décomposer l'expression avec les règles de somme, maximum, composition affine, composition monotone, supremum ou minimisation partielle.
5. Pour une fonction non lisse, raisonner avec l'épigraphie, les normes, les maximums ou les sous-niveaux plutôt qu'avec la Hessienne.

### 6.4 À ne pas confondre

| Notions proches | Différence essentielle |
|---|---|
| Ensemble affine / convexe | Affine : toute la droite ; convexe : seulement le segment. |
| Combinaison convexe / conique | Convexe : poids positifs de somme $1$ ; conique : poids positifs sans contrainte de somme. |
| Minimum / minimal | Minimum : domine tous les points ; minimal : n'est dominé par aucun autre point. |
| Convexe / quasi-convexe | Convexe : inégalité sur les valeurs ; quasi-convexe : seulement les sous-niveaux sont convexes. |
| Maximum / supremum | Maximum : borne supérieure atteinte ; supremum : borne supérieure qui peut ne pas être atteinte. |
| Positive semi-définie / définie | Semi-définie : courbure nulle autorisée ; définie : courbure strictement positive dans toute direction non nulle. |

---

## 7. L'essentiel à retenir

- La convexité d'un ensemble signifie que tout compromis linéaire entre deux solutions admissibles reste admissible.
- La convexité d'une fonction signifie que le coût d'un compromis ne dépasse pas le compromis des coûts.
- Hyperplans, demi-espaces, boules de norme, polyèdres et cônes PSD sont des briques convexes de base.
- L'intersection et les applications affines préservent la convexité des ensembles ; les épigraphes relient ensembles et fonctions.
- Pour une fonction différentiable convexe, toute tangente est un sous-estimateur global. Un gradient nul certifie alors un minimum global.
- Pour une fonction deux fois différentiable, une Hessienne positive semi-définie est le test de convexité le plus direct.
- Les règles de composition permettent de reconnaître une fonction convexe sans calculer toute sa Hessienne.
- Les cônes généralisent la positivité et les cônes duaux transforment des préférences vectorielles en objectifs scalaires pondérés.

> [!question] Pour préparer la suite
> Les chapitres suivants utiliseront ces objets pour formuler des problèmes d'optimisation, écrire leurs conditions d'optimalité et construire des algorithmes. À chaque nouvelle contrainte ou fonction objectif, les premières questions à poser seront : quel est son domaine ? Est-il convexe ? Quelle règle de ce chapitre permet de le justifier ?

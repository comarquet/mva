---
title: Ensembles convexes - poly de cours
tags:
  - optimisation-convexe
  - convexite
  - mva
source: "[[cours-1.pdf]]"
---

# Ensembles convexes

> [!abstract] Fil directeur
> Les ensembles convexes modélisent des contraintes sans trou ni repli : lorsque deux décisions sont admissibles, le chemin direct qui les relie l'est également. Leur géométrie fournit les objets de base de l'optimisation convexe : demi-espaces, boules, polyèdres, cônes, hyperplans de support et inégalités généralisées.

Cette note explique la géométrie des contraintes convexes et les outils qui permettent de les manipuler. La suite, [[2-fonctions-convexes]], étudie les fonctions objectif convexes construites sur ces ensembles.

L'objectif est de reconnaître les objets, de savoir pourquoi ils sont utiles, et de disposer de règles sûres pour établir la convexité dans les problèmes concrets.

---

## 0. Notations et prérequis minimaux

On travaille surtout dans l'espace vectoriel réel $\mathbb{R}^n$.

- $x=(x_1,\ldots,x_n)\in\mathbb{R}^n$ désigne un **vecteur** colonne à $n$ coordonnées réelles.
- $a^T x=\sum_{i=1}^n a_i x_i$ est le **produit scalaire** de deux vecteurs $a,x\in\mathbb{R}^n$. Il mesure leur alignement.
- $A\in\mathbb{R}^{m\times n}$ est une **matrice** à $m$ lignes et $n$ colonnes. Si $x\in\mathbb{R}^n$, alors $Ax\in\mathbb{R}^m$.
- $I$ désigne la matrice identité, de taille adaptée au contexte.
- Pour deux vecteurs $u,v\in\mathbb{R}^m$, l'écriture $u\preceq v$ signifie ici, sauf indication contraire, $u_i\leq v_i$ pour chaque coordonnée $i$. C'est une comparaison **coordonnée par coordonnée**.

### Normes : mesurer une taille ou une distance

Une norme est une fonction $\lVert\cdot\rVert$ qui associe une longueur à un vecteur. Elle vérifie, pour tous vecteurs $x,y$ et tout scalaire $t\in\mathbb{R}$,

$$
\lVert x\rVert\geq 0,\qquad
\lVert x\rVert=0\iff x=0,
$$

$$
\lVert tx\rVert=|t|\lVert x\rVert,
\qquad
\lVert x+y\rVert\leq \lVert x\rVert+\lVert y\rVert.
$$

La dernière propriété est l'**inégalité triangulaire** : aller directement de l'origine à $x+y$ ne peut pas être plus long que faire deux trajets successifs vers $x$, puis vers $x+y$.

Les normes les plus fréquentes pour $x\in\mathbb{R}^n$ sont

$$
\lVert x\rVert_1=\sum_{i=1}^n |x_i|,
\qquad
\lVert x\rVert_2=\left(\sum_{i=1}^n x_i^2\right)^{1/2},
\qquad
\lVert x\rVert_\infty=\max_{1\leq i\leq n}|x_i|.
$$

- $\lVert x\rVert_1$ additionne les amplitudes de toutes les coordonnées ;
- $\lVert x\rVert_2$ est la distance euclidienne habituelle ;
- $\lVert x\rVert_\infty$ ne retient que la plus grande amplitude.

### Matrices symétriques et positivité

$\mathbb{S}^n$ est l'ensemble des matrices réelles symétriques de taille $n\times n$ :

$$
X\in\mathbb{S}^n \quad\Longleftrightarrow\quad X=X^T.
$$

Une matrice symétrique $X$ est **positive semi-définie**, noté $X\succeq 0$, lorsque

$$
z^T Xz\geq 0\quad\text{pour tout vecteur }z\in\mathbb{R}^n.
$$

Cette expression est une forme quadratique. Intuitivement, elle ne produit jamais de courbure négative. Elle est **positive définie**, noté $X\succ0$, si $z^TXz>0$ pour tout $z\ne0$.

> [!tip] Pourquoi cette notation reviendra souvent ?
> Pour une fonction deux fois dérivable, sa matrice de courbure est sa Hessienne. Dire que la Hessienne est positive semi-définie est précisément une manière de dire que la fonction ne se courbe jamais vers le bas.

---

## 1. Interpoler deux points : affine, segment et convexité

### 1.1 Le problème géométrique

Supposons que $x_1$ et $x_2$ soient deux décisions admissibles. Si l'on mélange ces deux décisions, le mélange reste-t-il admissible ?

Cette question est centrale : beaucoup de méthodes d'optimisation se déplacent progressivement d'une solution vers une autre. La convexité garantit que le chemin direct ne sort pas de l'ensemble autorisé.

### 1.2 La droite affine

La droite passant par deux points distincts $x_1,x_2\in\mathbb{R}^n$ est l'ensemble des points

$$
x=\theta x_1+(1-\theta)x_2,
\qquad \theta\in\mathbb{R}.
$$

Ici, $\theta$ est un scalaire qui indique la position sur la droite.

- Pour $\theta=1$, on retrouve $x_1$.
- Pour $\theta=0$, on retrouve $x_2$.
- Pour $0<\theta<1$, le point est placé entre $x_1$ et $x_2$.
- Pour $\theta<0$ ou $\theta>1$, on prolonge la droite au-delà de l'un des deux points.

Un ensemble $A\subseteq\mathbb{R}^n$ est **affine** lorsqu'il contient toute la droite entre n'importe quels deux de ses points. Formellement,

$$
x_1,x_2\in A,\ \theta\in\mathbb{R}
\quad\Longrightarrow\quad
\theta x_1+(1-\theta)x_2\in A.
$$

Cette formule dit essentiellement que l'ensemble ne possède ni bord ni épaisseur finie dans les directions qu'il autorise : il contient les prolongements complets de ses droites.

**Exemple - systèmes d'équations linéaires.** L'ensemble

$$
\{x\in\mathbb{R}^n\mid Ax=b\}
$$

est affine, où $A\in\mathbb{R}^{m\times n}$ et $b\in\mathbb{R}^m$. En effet, si $Ax_1=b$ et $Ax_2=b$, alors

$$
A\bigl(\theta x_1+(1-\theta)x_2\bigr)
=\theta Ax_1+(1-\theta)Ax_2
=\theta b+(1-\theta)b=b.
$$

La linéarité de la multiplication par $A$ transmet exactement le mélange des solutions au côté gauche de l'équation.

### 1.3 Le segment et les ensembles convexes

Le **segment** entre $x_1$ et $x_2$ ne garde que les valeurs $0\leq\theta\leq1$ :

$$
[x_1,x_2]
=\{\theta x_1+(1-\theta)x_2\mid 0\leq\theta\leq1\}.
$$

Un ensemble $C\subseteq\mathbb{R}^n$ est **convexe** si le segment entre deux de ses points lui appartient entièrement :

$$
x_1,x_2\in C,\quad 0\leq\theta\leq1
\quad\Longrightarrow\quad
\theta x_1+(1-\theta)x_2\in C.
$$

Cette formule exprime une idée simple : si deux choix sont possibles, tout compromis linéaire entre eux reste possible. Une boule pleine, un disque, un rectangle rempli ou un demi-plan sont convexes. Un croissant, un anneau, ou deux îlots séparés ne le sont pas : on peut y choisir deux points dont le segment traverse une zone interdite.

> [!important] Affine ou convexe ?
> Tout ensemble affine est convexe, car il contient en particulier le segment. La réciproque est fausse : le disque unité est convexe, mais il ne contient pas le prolongement infini d'une droite qui traverse le disque.

### 1.4 Combinaisons convexes et enveloppe convexe

Avec plus de deux points $x_1,\ldots,x_k\in\mathbb{R}^n$, une **combinaison convexe** est un point

$$
x=\theta_1x_1+\theta_2x_2+\cdots+\theta_kx_k,
$$

où les poids $\theta_1,\ldots,\theta_k\in\mathbb{R}$ vérifient

$$
\theta_i\geq0\quad\text{pour chaque }i,
\qquad
\sum_{i=1}^k\theta_i=1.
$$

Chaque $\theta_i$ représente une part du mélange. Les poids sont non négatifs et leur somme vaut $1$ : on ne crée ni masse négative, ni amplification artificielle.

**Exemple numérique.** Avec $x_1=(0,0)$, $x_2=(2,0)$ et $x_3=(0,3)$ dans $\mathbb{R}^2$,

$$
0.2x_1+0.5x_2+0.3x_3=(1,0.9).
$$

Les coefficients $0.2$, $0.5$ et $0.3$ forment des poids valides. Le point obtenu est situé à l'intérieur du triangle ayant $x_1,x_2,x_3$ comme sommets.

L'**enveloppe convexe** d'un ensemble $S$, notée $\operatorname{conv}(S)$ ou $\operatorname{co}(S)$, est l'ensemble de toutes les combinaisons convexes d'un nombre fini de points de $S$ :

$$
\operatorname{conv}(S)
=\left\{\sum_{i=1}^k\theta_i x_i\ \middle|\ x_i\in S,\ \theta_i\geq0,\ \sum_{i=1}^k\theta_i=1\right\}.
$$

Elle résout le problème suivant : **quel est le plus petit ensemble convexe qui contient $S$ ?** Pour trois points non alignés, c'est le triangle plein, et non seulement ses trois côtés. En optimisation et en apprentissage, l'enveloppe convexe remplace souvent un ensemble discret difficile par son relâchement convexe, plus maniable.

### 1.5 Combinaisons coniques et cônes convexes

Une **combinaison conique** de deux vecteurs $x_1,x_2\in\mathbb{R}^n$ a la forme

$$
x=\theta_1x_1+\theta_2x_2,
\qquad \theta_1\geq0,\ \theta_2\geq0.
$$

La différence avec une combinaison convexe est importante : on ne demande plus $\theta_1+\theta_2=1$. On peut donc augmenter l'échelle du résultat. Géométriquement, au lieu d'un segment borné, on obtient un secteur qui part de l'origine et s'étend à l'infini.

Un **cône convexe** $K\subseteq\mathbb{R}^n$ contient toute combinaison conique de ses éléments. Équivalemment, si $x,y\in K$ et si $\alpha,\beta\geq0$, alors

$$
\alpha x+\beta y\in K.
$$

Un cône est donc fermé par addition et par multiplication par un scalaire positif. En particulier, il contient l'origine, puisqu'on peut prendre tous les coefficients nuls.

**Pourquoi introduire les cônes ?** Ils servent à généraliser la notion de « positif ». Dans $\mathbb{R}$, être positif signifie appartenir à $\mathbb{R}_+$. Dans un espace de vecteurs ou de matrices, un cône permet de choisir ce que signifie « aller dans une direction positive ».

---

## 2. Exemples fondamentaux d'ensembles convexes

Les exemples suivants reviennent constamment dans les contraintes d'optimisation. Les connaître évite de refaire une preuve de convexité à chaque fois.

### 2.1 Hyperplans et demi-espaces

Un **hyperplan** est un ensemble de la forme

$$
H=\{x\in\mathbb{R}^n\mid a^Tx=b\},
\qquad a\in\mathbb{R}^n\setminus\{0\},\ b\in\mathbb{R}.
$$

Le vecteur $a$ est la **normale** à l'hyperplan : il est perpendiculaire à toutes les directions qui restent dans $H$. La constante $b$ fixe sa position. En dimension $2$, un hyperplan est une droite ; en dimension $3$, c'est un plan.

Le **demi-espace** associé est

$$
\{x\in\mathbb{R}^n\mid a^Tx\leq b\}.
$$

Il conserve un des deux côtés de l'hyperplan, bord compris. Les hyperplans sont affines, et les demi-espaces sont convexes. Pour voir ce dernier point, si $a^Tx_1\leq b$ et $a^Tx_2\leq b$, alors pour $0\leq\theta\leq1$,

$$
a^T\bigl(\theta x_1+(1-\theta)x_2\bigr)
=\theta a^Tx_1+(1-\theta)a^Tx_2
\leq\theta b+(1-\theta)b=b.
$$

Le mélange ne franchit pas le mur défini par l'inégalité.

### 2.2 Boules euclidiennes, boules de norme et ellipsoïdes

La boule euclidienne de centre $x_c\in\mathbb{R}^n$ et de rayon $r\geq0$ est

$$
B_2(x_c,r)=\{x\in\mathbb{R}^n\mid \lVert x-x_c\rVert_2\leq r\}.
$$

Elle rassemble tous les points à distance au plus $r$ du centre. On peut aussi l'écrire

$$
B_2(x_c,r)=\{x_c+ru\mid \lVert u\rVert_2\leq1\}.
$$

Dans cette seconde écriture, $u$ est une direction normalisée, puis $r$ l'agrandit et $x_c$ la translate.

Plus généralement, pour n'importe quelle norme $\lVert\cdot\rVert$, la boule

$$
\{x\in\mathbb{R}^n\mid \lVert x-x_c\rVert\leq r\}
$$

est convexe. L'inégalité triangulaire explique ce fait : le déplacement entre le centre et un mélange est au plus le mélange des deux déplacements, chacun de taille au plus $r$.

Un **ellipsoïde** centré en $x_c$ s'écrit

$$
E=\left\{x\in\mathbb{R}^n\ \middle|\ (x-x_c)^TP^{-1}(x-x_c)\leq1\right\},
$$

où $P\in\mathbb{S}^n$ est positive définie. La matrice $P$ étire et tourne une boule ; ses grandes directions propres correspondent à des axes longs de l'ellipsoïde. On peut aussi écrire

$$
E=\{x_c+Au\mid \lVert u\rVert_2\leq1\},
$$

où $A\in\mathbb{R}^{n\times n}$ est inversible et $P=AA^T$. Cette représentation raconte directement la construction : partir de la boule unité, appliquer la transformation linéaire $A$, puis translater.

### 2.3 Cône de norme et cône du second ordre

Le **cône de norme** associé à une norme sur $\mathbb{R}^n$ est

$$
K_{\lVert\cdot\rVert}
=\{(x,t)\in\mathbb{R}^n\times\mathbb{R}\mid \lVert x\rVert\leq t\}.
$$

Le scalaire $t$ joue le rôle d'un budget de taille pour le vecteur $x$. Le cône est convexe : la norme d'un mélange est contrôlée par l'inégalité triangulaire et l'homogénéité de la norme.

Lorsque la norme est euclidienne, on obtient

$$
\{(x,t)\mid \lVert x\rVert_2\leq t\},
$$

appelé **cône du second ordre** (ou *second-order cone*, SOC). Il est très utilisé car beaucoup de contraintes quadratiques peuvent être reformulées sous cette forme.

### 2.4 Polyèdres : contraintes linéaires en nombre fini

Un **polyèdre** est l'ensemble des solutions d'un nombre fini d'inégalités et d'égalités linéaires :

$$
P=\{x\in\mathbb{R}^n\mid Ax\preceq b,\ Cx=d\},
$$

où $A\in\mathbb{R}^{m\times n}$, $b\in\mathbb{R}^m$, $C\in\mathbb{R}^{p\times n}$ et $d\in\mathbb{R}^p$.

La première contrainte signifie $a_i^Tx\leq b_i$ pour chaque ligne $a_i^T$ de $A$. Un polyèdre est l'intersection d'un nombre fini de demi-espaces et d'hyperplans. Il est donc convexe.

En dimension $2$, il peut ressembler à un polygone rempli, mais il n'est pas forcément borné : une bande ou un demi-plan sont aussi des polyèdres.

### 2.5 Cône des matrices positives semi-définies

Le cône positif semi-défini est

$$
\mathbb{S}_+^n
=\{X\in\mathbb{S}^n\mid X\succeq0\}
=\{X\in\mathbb{S}^n\mid z^TXz\geq0\ \text{pour tout }z\in\mathbb{R}^n\}.
$$

Il est convexe : si $X,Y\succeq0$ et $0\leq\theta\leq1$, alors, pour tout $z$,

$$
z^T\bigl(\theta X+(1-\theta)Y\bigr)z
=\theta z^TXz+(1-\theta)z^TYz\geq0.
$$

**Cas $2\times2$.** Pour une matrice symétrique

$$
X=\begin{pmatrix}x&y\\y&z\end{pmatrix},
$$

la condition $X\succeq0$ équivaut à

$$
x\geq0,\qquad z\geq0,\qquad xz-y^2\geq0.
$$

Le terme $xz-y^2$ est le déterminant de $X$. Cette condition montre que la zone des triplets $(x,y,z)$ admissibles est incurvée, mais reste convexe.

---

## 3. Construire de nouveaux ensembles convexes

Face à une contrainte complexe, il est souvent plus efficace de la décomposer en objets connus que d'appliquer directement la définition avec deux points arbitraires.

### 3.1 Intersection

L'intersection d'une famille, même infinie, d'ensembles convexes est convexe :

$$
C=\bigcap_{\alpha\in I} C_\alpha,
\qquad C_\alpha\ \text{convexe pour tout }\alpha.
$$

En effet, deux points de $C$ sont simultanément dans chaque $C_\alpha$. Leur segment est dans chaque $C_\alpha$, donc dans leur intersection.

**Exemple - contrainte sur toute une courbe.** Soit $x=(x_1,\ldots,x_m)\in\mathbb{R}^m$ et

$$
p_x(t)=x_1\cos(t)+x_2\cos(2t)+\cdots+x_m\cos(mt).
$$

L'ensemble des coefficients qui vérifient $|p_x(t)|\leq1$ pour tout $t\in[-\pi/3,\pi/3]$ est convexe. Pour chaque valeur fixée de $t$, l'inégalité s'écrit

$$
-1\leq a(t)^Tx\leq1,
$$

où $a(t)=(\cos t,\cos 2t,\ldots,\cos mt)$. C'est l'intersection de deux demi-espaces. La contrainte « pour tout $t$ » est l'intersection de toutes ces contraintes, indexées par $t$.

### 3.2 Images et images réciproques d'une application affine

Une fonction $f:\mathbb{R}^n\to\mathbb{R}^m$ est **affine** si elle s'écrit

$$
f(x)=Ax+b,
$$

avec $A\in\mathbb{R}^{m\times n}$ et $b\in\mathbb{R}^m$.

Elle préserve les mélanges :

$$
f\bigl(\theta x_1+(1-\theta)x_2\bigr)
=\theta f(x_1)+(1-\theta)f(x_2).
$$

Cette identité est la raison profonde de deux règles utiles.

- Si $S\subseteq\mathbb{R}^n$ est convexe, son **image**

  $$
  f(S)=\{f(x)\mid x\in S\}
  $$

  est convexe.

- Si $C\subseteq\mathbb{R}^m$ est convexe, son **image réciproque**

  $$
  f^{-1}(C)=\{x\in\mathbb{R}^n\mid f(x)\in C\}
  $$

  est convexe.

La différence mérite d'être retenue. L'image transforme les points déjà présents ; l'image réciproque décrit les entrées dont la sortie satisfait une contrainte.

**Exemples.** Une translation, une mise à l'échelle et une projection sur certaines coordonnées sont des applications affines. Le système d'inégalités $Ax\preceq b$ est l'image réciproque du demi-espace produit $\{u\in\mathbb{R}^m\mid u\preceq b\}$ par l'application $x\mapsto Ax$.

Une **inégalité matricielle linéaire** $LMI$ a la forme

$$
x_1A_1+\cdots+x_mA_m\preceq B,
$$

où $x\in\mathbb{R}^m$ est la variable et $A_1,\ldots,A_m,B\in\mathbb{S}^p$ sont fixées. Elle est convexe car elle demande à une application affine de prendre ses valeurs dans le cône convexe $\mathbb{S}_+^p$ :

$$
B-\sum_{i=1}^m x_iA_i\succeq0.
$$

### 3.3 Perspective : normaliser par une variable positive

La **perspective** est l'application

$$
P:\mathbb{R}^{n+1}\to\mathbb{R}^n,
\qquad
P(x,t)=\frac{x}{t},
\qquad t>0,
$$

où $x\in\mathbb{R}^n$ et $t\in\mathbb{R}$ est un scalaire strictement positif.

Elle répond au problème suivant : comment représenter une direction $x$ à une échelle relative $t$ ? Diviser par $t$ « remet à l'échelle » les coordonnées. Malgré son apparence non linéaire, la perspective préserve la convexité des images et des images réciproques de manière appropriée.

L'intuition est que deux ratios issus de deux points peuvent être réécrits comme un mélange des ratios, avec des poids réajustés par les dénominateurs positifs. La condition $t>0$ est indispensable : traverser $t=0$ introduirait une singularité et détruirait ce mécanisme de mélange.

### 3.4 Transformations linéaires-fractionnaires

Une fonction **linéaire-fractionnaire** est

$$
f(x)=\frac{Ax+b}{c^Tx+d},
\qquad
\operatorname{dom}f=\{x\in\mathbb{R}^n\mid c^Tx+d>0\},
$$

où $A\in\mathbb{R}^{m\times n}$, $b\in\mathbb{R}^m$, $c\in\mathbb{R}^n$ et $d\in\mathbb{R}$. Le numérateur $Ax+b$ est un vecteur de $\mathbb{R}^m$ et la division par le scalaire positif $c^Tx+d$ se fait coordonnée par coordonnée.

Cette forme est une application affine suivie d'une perspective. Ses images et images réciproques d'ensembles convexes restent convexes.

**Exemple en une dimension.** La fonction

$$
f(x)=\frac{x}{x+1},\qquad x>-1,
$$

déforme une droite en comprimant les points lorsque $x+1$ est grand. Elle n'est pas affine, mais elle conserve les intervalles convexes situés dans son domaine en des intervalles convexes.

---

## 4. Cônes propres et inégalités généralisées

### 4.1 Pourquoi généraliser l'ordre usuel ?

Sur la droite réelle, $x\leq y$ signifie que $y-x\geq0$. Dans $\mathbb{R}^n$, plusieurs manières de dire qu'un vecteur est « plus grand » sont utiles. Par exemple, on peut vouloir que toutes ses coordonnées soient plus grandes, ou comparer des matrices par leur courbure.

Un cône $K\subseteq\mathbb{R}^n$ est dit **propre** s'il est :

- **fermé** : il contient ses points frontières ;
- **solide** : son intérieur n'est pas vide ;
- **pointé** : il ne contient aucune droite entière, ce qui revient à $K\cap(-K)=\{0\}$.

La propriété « pointé » évite que deux vecteurs opposés soient tous les deux jugés positifs. Sans elle, une relation d'ordre perdrait sa capacité à distinguer les directions.

Exemples de cônes propres :

$$
\mathbb{R}_+^n=\{x\in\mathbb{R}^n\mid x_i\geq0\ \text{pour }i=1,\ldots,n\},
$$

le cône $\mathbb{S}_+^n$, et l'ensemble des vecteurs de coefficients de polynômes non négatifs sur $[0,1]$ :

$$
\left\{x\in\mathbb{R}^n\ \middle|\ x_1+x_2t+\cdots+x_nt^{n-1}\geq0\ \text{pour tout }t\in[0,1]\right\}.
$$

### 4.2 Ordre induit par un cône

Un cône propre $K$ définit la relation

$$
x\preceq_K y
\quad\Longleftrightarrow\quad
y-x\in K.
$$

La relation stricte est

$$
x\prec_K y
\quad\Longleftrightarrow\quad
y-x\in\operatorname{int}K,
$$

où $\operatorname{int}K$ désigne l'intérieur de $K$.

La première formule dit : $y$ est au moins aussi grand que $x$ dans toutes les directions que le cône considère positives. La seconde demande une amélioration qui reste loin de la frontière du cône.

Deux cas doivent devenir naturels :

$$
x\preceq_{\mathbb{R}_+^n}y
\quad\Longleftrightarrow\quad
x_i\leq y_i\ \text{pour chaque }i,
$$

et, pour $X,Y\in\mathbb{S}^n$,

$$
X\preceq_{\mathbb{S}_+^n}Y
\quad\Longleftrightarrow\quad
Y-X\succeq0.
$$

Les deux notations sont si courantes qu'on omet souvent l'indice du cône lorsqu'il est évident dans le contexte. Les propriétés usuelles de l'addition restent valables : si $x\preceq_K y$ et $u\preceq_K v$, alors

$$
x+u\preceq_K y+v.
$$

En effet, $(y-x)+(v-u)\in K$ puisque $K$ est fermé par addition.

### 4.3 Élément minimum et élément minimal

Dans un ordre partiel, deux points peuvent être incomparables. Avec l'ordre coordonnée par coordonnée dans $\mathbb{R}^2$, $(1,3)$ et $(2,2)$ ne dominent ni l'un ni l'autre : le premier est meilleur sur la première coordonnée, le second sur la deuxième.

Soit $S\subseteq\mathbb{R}^n$.

- Un point $x\in S$ est un **minimum** de $S$ pour $\preceq_K$ si

  $$
  y\in S\quad\Longrightarrow\quad x\preceq_K y.
  $$

  Il est meilleur ou égal à tous les autres points suivant l'ordre choisi.

- Un point $x\in S$ est **minimal** si

  $$
  y\in S,\quad y\preceq_K x
  \quad\Longrightarrow\quad y=x.
  $$

  Aucun autre point ne le domine strictement, mais il peut rester incomparable à de nombreux autres.

Un minimum, s'il existe, est unique et minimal. Un élément minimal n'est pas nécessairement un minimum.

**Exemple.** Pour $K=\mathbb{R}_+^2$ et

$$
S=\{(t,1-t)\mid 0\leq t\leq1\},
$$

chaque point est minimal : diminuer une coordonnée force l'autre à augmenter. En revanche, il n'y a pas de minimum qui soit inférieur aux autres sur les deux coordonnées. C'est la géométrie de base des compromis de type Pareto.

---

## 5. Hyperplans séparateurs et hyperplans de support

### 5.1 Séparer deux ensembles convexes

Un résultat géométrique fondamental affirme que si deux ensembles convexes disjoints $C,D\subseteq\mathbb{R}^n$ ne se rencontrent pas, il existe un vecteur non nul $a\in\mathbb{R}^n$ et un scalaire $b\in\mathbb{R}$ tels que

$$
a^Tx\leq b\quad\text{pour tout }x\in C,
\qquad
a^Tx\geq b\quad\text{pour tout }x\in D.
$$

L'hyperplan $\{x\mid a^Tx=b\}$ est un **hyperplan séparateur**. Il place $C$ d'un côté et $D$ de l'autre, éventuellement avec certains points sur le bord.

L'idée est précieuse en optimisation : un hyperplan peut être vu comme un certificat linéaire indiquant qu'une région ne contient pas un point ou ne peut pas interférer avec une autre région. Pour obtenir une séparation stricte, avec un espace non nul entre les deux ensembles, il faut des hypothèses supplémentaires, par exemple un ensemble fermé et l'autre réduit à un point extérieur.

### 5.2 Soutenir un ensemble convexe à sa frontière

Soit $C\subseteq\mathbb{R}^n$ convexe et soit $x_0$ un point de sa frontière. Un **hyperplan de support** en $x_0$ est un hyperplan qui passe par $x_0$ et garde tout l'ensemble du même côté :

$$
\{x\in\mathbb{R}^n\mid a^Tx=a^Tx_0\},
\qquad a\ne0,
$$

avec

$$
a^Tx\leq a^Tx_0\quad\text{pour tout }x\in C.
$$

Le théorème de support garantit l'existence d'un tel hyperplan en chaque point frontière d'un ensemble convexe. Pour une boule, c'est le plan tangent. Pour un polyèdre, il peut être l'une de ses faces.

Ce résultat annonce déjà la condition du premier ordre pour les fonctions convexes : le plan tangent au graphe d'une fonction convexe se situe sous le graphe entier.

---

## 6. Cônes duaux : tester la positivité avec des produits scalaires

### 6.1 Définition et intuition

Le **cône dual** d'un cône $K\subseteq\mathbb{R}^n$ est

$$
K^*=\{y\in\mathbb{R}^n\mid y^Tx\geq0\ \text{pour tout }x\in K\}.
$$

Le vecteur $y$ appartient au cône dual s'il attribue une valeur non négative, via le produit scalaire, à toutes les directions considérées positives dans $K$.

Il transforme ainsi une notion géométrique, « $x$ est dans le cône », en une famille de tests linéaires, « $y^Tx\geq0$ pour tout test $y$ admissible ». Pour les matrices, on emploie le produit scalaire de Frobenius

$$
\langle Y,X\rangle=\operatorname{Tr}(Y^TX),
$$

à la place du produit $y^Tx$ entre vecteurs.

### 6.2 Exemples à retenir

Les cônes suivants sont **auto-duaux**, c'est-à-dire égaux à leur dual :

$$
(\mathbb{R}_+^n)^*=\mathbb{R}_+^n,
\qquad
(\mathbb{S}_+^n)^*=\mathbb{S}_+^n,
$$

et le cône du second ordre est lui aussi auto-dual. En revanche, le cône défini par la norme $\ell_1$ a pour dual le cône défini par la norme $\ell_\infty$ :

$$
\{(x,t)\mid \lVert x\rVert_1\leq t\}^*
=\{(y,s)\mid \lVert y\rVert_\infty\leq s\}.
$$

Cette correspondance reflète l'inégalité fondamentale $|y^Tx|\leq\lVert y\rVert_\infty\lVert x\rVert_1$.

Le dual d'un cône propre est encore un cône propre. Il donne une lecture particulièrement utile de l'ordre induit par $K$ :

$$
x\succeq_K0
\quad\Longrightarrow\quad
y^Tx\geq0\ \text{pour tout }y\succeq_{K^*}0.
$$

### 6.3 Scalariser un problème multi-critère

Les inégalités généralisées permettent de formaliser plusieurs objectifs. Les cônes duaux permettent de les ramener à une fonction scalaire.

Si $\lambda\in K^*$, minimiser $\lambda^Tz$ sur $S$ revient à donner des poids non négatifs aux directions de préférence. En particulier :

- si $x$ minimise $\lambda^Tz$ sur $S$ pour un $\lambda\succ_{K^*}0$, alors $x$ est minimal pour $\preceq_K$ ;
- si $S$ est convexe et que $x$ est minimal, un vecteur non nul $\lambda\succeq_{K^*}0$ peut soutenir $S$ en $x$ et faire de $x$ un minimiseur de $\lambda^Tz$.

La deuxième affirmation est une application de l'idée d'hyperplan de support. Elle explique pourquoi les sommes pondérées sont une méthode naturelle pour explorer le front de Pareto d'un problème convexe.

---

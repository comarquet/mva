---
title: Optimisation convexe - cours principal, intuitions et exemples
aliases:
  - Optimization Reminders
  - Rappels d'optimisation convexe
tags:
  - mva
  - optimisation-convexe
  - convexite
  - projection
sources:
  - "[[prerentree/optimisation-convexe/sources/cours-optimisation-convexe-1.pdf|Optimization Reminders - Part I]]"
  - "[[prerentree/optimisation-convexe/sources/cours-optimisation-convexe-2.pdf|Optimization Reminders - Part II]]"
---

# Optimisation convexe - cours principal, intuitions et exemples

> [!info] Sources et mode d'emploi
> Note fondée sur *Optimization Reminders - Part I* et *Part II* de Jean-Christophe Pesquet : [[prerentree/optimisation-convexe/sources/cours-optimisation-convexe-1.pdf|partie 1]] et [[prerentree/optimisation-convexe/sources/cours-optimisation-convexe-2.pdf|partie 2]]. Les numéros indiqués sont ceux des diapositives, qui peuvent être répétées dans les PDF à cause des animations. Commence par les idées et les exemples ; reviens ensuite aux démonstrations.

## Le fil conducteur

On veut résoudre $\min_{x\in C}f(x)$ : choisir un point $x$ autorisé par les contraintes $C$ qui rend le coût $f(x)$ aussi petit que possible. Avant de calculer une solution, il faut répondre à quatre questions différentes :

1. **Existence :** le minimum est-il atteint ? Un *infimum* peut exister sans point qui l'atteigne.
2. **Unicité :** peut-il y avoir plusieurs solutions ? La stricte convexité permet souvent de répondre.
3. **Caractérisation :** quelle équation ou inégalité vérifie une solution ? Le gradient, puis la projection, servent ici.
4. **Calcul :** peut-on résoudre explicitement ? Ce cours pose surtout les bases théoriques ; les algorithmes viendront ensuite.

> [!tip] Pour une première lecture
> Pense à $H=\mathbb R^n$ avec le produit scalaire usuel. Les mêmes résultats s'énoncent dans des espaces de Hilbert plus généraux, mais les exemples ci-dessous ne demandent que de l'algèbre linéaire, des dérivées et quelques limites.

## 1. Les outils de départ : géométrie et dérivées (diapositives 4 à 7)

### Produit scalaire, norme, espace de Hilbert

Dans $\mathbb R^n$, le **produit scalaire** et la **norme** sont

$$\langle x,y\rangle=\sum_{i=1}^n x_i y_i,\qquad \|x\|=\sqrt{\langle x,x\rangle}. $$

$\langle x,y\rangle$ mesure notamment si deux directions vont dans le même sens : il est positif pour un angle aigu, nul pour des directions perpendiculaires. La norme mesure la longueur. Un espace de Hilbert ajoute la propriété de **complétude** : une suite dont les termes finissent par être arbitrairement proches les uns des autres converge dans l'espace. $\mathbb R^n$ possède cette propriété. Le professeur cite aussi $L^2(\mathbb R)$, un espace de fonctions dont le carré est intégrable, utile pour modéliser des signaux. Pour cette note, tu peux commencer en lisant « espace de Hilbert » comme « $\mathbb R^n$ avec une géométrie de produit scalaire ».

Une application linéaire $L:\mathbb R^n\to\mathbb R^m$ est représentée par une matrice. Sa norme d'opérateur $\|L\|=\sup_{\|x\|\leq1}\|Lx\|$ donne le facteur maximal d'agrandissement d'un vecteur. En dimension finie, cette valeur est toujours finie : toutes les applications linéaires sont continues.

L'**adjoint** $L^*$ est défini par

$$\langle Lx,y\rangle=\langle x,L^*y\rangle.$$

Dans $\mathbb R^n$, c'est la transposée $L^\top$. Exemple : si $L(x_1,x_2)=x_1+2x_2$, alors $L^*(t)=(t,2t)$, car $(x_1+2x_2)t=\langle(x_1,x_2),(t,2t)\rangle$. Cette identité sera utile pour dériver les moindres carrés : $\nabla\bigl(\tfrac12\|Ax-b\|^2\bigr)=A^\top(Ax-b)$.

### Gradient et Hessienne : le minimum de calcul à connaître

Pour une fonction différentiable $f:\mathbb R^n\to\mathbb R$, le gradient $\nabla f(x)$ regroupe les dérivées partielles. À très petite échelle,

$$f(x+h)\approx f(x)+\langle\nabla f(x),h\rangle.$$

La direction $-\nabla f(x)$ fait donc diminuer $f$ au premier ordre. La **Hessienne** $\nabla^2f(x)$ est la matrice des dérivées secondes : elle décrit la courbure locale. Par exemple, pour $f(x_1,x_2)=x_1^2+3x_2^2$, on a $\nabla f=(2x_1,6x_2)$ et $\nabla^2f=\operatorname{diag}(2,6)$. La Hessienne est **positive semi-définie** si $z^\top\nabla^2f(x)z\geq0$ pour tout $z$ ; elle est **positive définie** si l'inégalité est stricte dès que $z\ne0$.

## 2. Mettre les contraintes dans la fonction (diapositives 9 à 14)

### Fonctions à valeurs dans $\mathbb R\cup\{+\infty\}$

On autorise $f(x)=+\infty$ pour dire « ce point est interdit ». Le **domaine effectif** est $\operatorname{dom}f=\{x:f(x)<+\infty\}$. Une fonction est **propre** si ce domaine n'est pas vide : au moins un candidat est faisable.

L'**indicatrice convexe** d'un ensemble $C$ vaut

$$\iota_C(x)=\begin{cases}0&\text{si }x\in C,\\+\infty&\text{sinon.}\end{cases}$$

Ainsi $\min_{x\in C}f(x)$ se réécrit $\min_{x\in\mathbb R^n}\bigl(f(x)+\iota_C(x)\bigr)$. L'intérêt est d'utiliser une seule écriture pour l'objectif et les contraintes. Exemple : minimiser $(x-3)^2$ sous $0\leq x\leq1$ revient à minimiser $(x-3)^2+\iota_{[0,1]}(x)$ sur $\mathbb R$ ; le minimiseur est $x=1$.

> [!warning] Ne pas confondre deux « indicatrices »
> En probabilités, une indicatrice vaut souvent $1$ sur $C$ et $0$ ailleurs. Ici, en optimisation convexe, elle vaut $0$ sur $C$ et $+\infty$ ailleurs.

### Liminf, épigraphe et semi-continuité inférieure

La **limite inférieure** $\liminf a_n$ est la limite des plus petites valeurs encore possibles après le rang $n$. Si $a_n=(-1)^n$, alors $\liminf a_n=-1$ et $\limsup a_n=1$. Ces notions restent utiles quand la suite n'a pas de limite.

Une fonction $f$ est **semi-continue inférieurement** (s.c.i.) si, pour chaque suite $x_k\to x$,

$$f(x)\leq\liminf_{k\to\infty}f(x_k).$$

**Intuition :** en approchant $x$, on ne doit pas découvrir que la valeur en $x$ saute *au-dessus* des valeurs proches. Une pointe vers le bas est permise ; une pointe isolée vers le haut ne l'est pas. Cette propriété est exactement celle qui permet de passer à la limite dans une suite de points presque optimaux.

L'**épigraphe** de $f$ est la région située au-dessus de son graphe : $\operatorname{epi}f=\{(x,t):f(x)\leq t\}$. La fonction est s.c.i. **si et seulement si** son épigraphe est fermé. Une fonction continue est s.c.i., mais la réciproque est fausse.

**Exemple décisif.** $\iota_C$ est s.c.i. si et seulement si $C$ est fermé. Pour $C=(0,1)$, les points $x_k=1/k$ sont autorisés et tendent vers $0$, qui est interdit : $\iota_C(x_k)=0$ mais $\iota_C(0)=+\infty$. L'inégalité de semi-continuité échoue. Pour $C=[0,1]$, elle tient.

Les sommes finies de fonctions s.c.i. et les bornes supérieures de familles de fonctions s.c.i. restent s.c.i. **Vérifie tout de même que la fonction obtenue est propre** avant d'appliquer un théorème d'existence.

## 3. Savoir si un minimum existe (diapositives 15 à 20)

Un **minimum global** $x^*$ vérifie $f(x^*)\leq f(x)$ pour tout candidat $x$. Un **minimum local** ne compare $x^*$ qu'aux candidats voisins. *Strict* signifie que l'inégalité est stricte pour tout autre candidat considéré.

Le théorème de **Weierstrass** dit qu'une fonction propre et s.c.i. sur un ensemble non vide **compact** atteint son minimum. Dans $\mathbb R^n$, « compact » signifie « fermé et borné ».

**Pourquoi chaque hypothèse compte :**

- Sur $(0,1]$, $f(x)=x$ a pour infimum $0$, mais aucun minimiseur : le domaine n'est pas fermé.
- Sur $\mathbb R$, $f(x)=e^x$ a aussi pour infimum $0$ non atteint : le domaine n'est pas borné et la fonction laisse partir les bons candidats vers $-\infty$.
- Pour $f(0)=1$ et $f(x)=x^2$ si $x\ne0$ sur $[-1,1]$, l'infimum $0$ n'est pas atteint : cette fonction n'est pas s.c.i. en $0$.

Quand le domaine n'est pas borné, une autre protection est la **coercivité** : $f(x)\to+\infty$ dès que $\|x\|\to\infty$. Elle empêche une suite de bons candidats de s'enfuir. En dimension finie, une fonction propre, s.c.i. et coercive sur $\mathbb R^n$ a un ensemble de minimiseurs non vide et compact. Exemple : $f(x)=x^2-2x=(x-1)^2-1$ est coercive et atteint son minimum $-1$ en $x=1$.

La coercivité est une **condition suffisante**, pas nécessaire : $f(x,y)=x^2$ a tous les points $(0,y)$ comme minimiseurs, bien qu'elle ne soit pas coercive quand $|y|\to\infty$.

## 4. Ce que les dérivées disent avant la convexité (diapositives 22 à 24)

### Prérequis : lire une dérivée et une contrainte en dimension 1

Avant de lire les formules vectorielles, regarde une fonction d'une seule variable. On cherche par exemple à rendre aussi petit que possible

$$f(x)=(x-2)^2.$$

Sa dérivée est $f'(x)=2(x-2)$. La dérivée donne l'effet d'une toute petite augmentation de $x$ :

- si $f'(x)>0$, avancer vers la droite augmente $f$ ;
- si $f'(x)<0$, avancer vers la droite diminue $f$ ;
- si $f'(x)=0$, la courbe est localement horizontale. C'est un candidat pour un minimum ou un maximum.

Ici, $f'(x)<0$ pour $x<2$ : aller vers la droite rapproche du fond du bol. $f'(2)=0$, et $x=2$ est le minimum sans contrainte. À lui seul, un dérivée nulle ne garantit toutefois rien : $g(x)=x^3$ vérifie $g'(0)=0$, alors que $0$ n'est pas un minimum.

Supposons maintenant que $x$ doit appartenir à $C=[0,1]$. Le point idéal $2$ est interdit. Le meilleur point autorisé est $x^*=1$, le bord droit de l'intervalle. Pourtant,

$$f'(1)=-2\ne0.$$

Ce n'est pas une contradiction. Ce signe négatif dit qu'**augmenter** $x$ ferait baisser $f$, mais les nombres plus grands que $1$ ne sont pas autorisés. Depuis $1$, les seuls déplacements faisables vont vers la gauche, par exemple vers $y=0.8$. Le nombre $y-x^*=y-1$ représente ce déplacement : il est négatif ou nul pour tout $y\in[0,1]$.

Pour savoir si ce déplacement fait augmenter ou diminuer $f$, on multiplie :

$$f'(x^*)(y-x^*).$$

Dans notre exemple, pour $y=0.8$,

$$f'(1)(0.8-1)=(-2)(-0.2)=0.4>0.$$

Le résultat positif signifie qu'aller un peu de $1$ vers $0.8$ fait monter $f$. Plus généralement, $(-2)(y-1)\geq0$ pour tout $y\in[0,1]$ : aucun déplacement autorisé depuis $1$ ne fait baisser la fonction.

### L'inégalité d'Euler

Si $x^*$ est un minimum local intérieur et $f$ est différentiable, alors $\nabla f(x^*)=0$. **Attention :** un gradient nul est nécessaire, pas suffisant en général. Pour $f(x)=x^3$, le gradient est nul en $0$, mais $0$ n'est pas un minimum.

Au bord d'un ensemble de contraintes $C$, le gradient peut être non nul. La condition nécessaire est l'**inégalité d'Euler** : pour toute direction vers un point faisable $y$ telle que le segment $[x^*,y]$ reste dans $C$,

$$\langle\nabla f(x^*),y-x^*\rangle\geq0.$$

Elle dit qu'aucune petite avancée faisable depuis $x^*$ ne fait baisser $f$ au premier ordre. C'est la version en plusieurs dimensions de $f'(x^*)(y-x^*)\geq0$ vu juste avant :

- le **gradient** $\nabla f(x^*)$ remplace la dérivée $f'(x^*)$ ; il indique la direction où $f$ augmente le plus vite ;
- $y-x^*$ est le vecteur qui va du candidat $x^*$ vers un autre point autorisé $y$ ;
- le produit scalaire $\langle\nabla f(x^*),y-x^*\rangle$ remplace le produit ordinaire des deux nombres. Son signe indique si ce déplacement fait monter ou descendre $f$, à très petite échelle.

Si ce produit scalaire était négatif, un petit pas vers $y$ diminuerait $f$, ce qui contredirait le fait que $x^*$ est un minimum. Il doit donc être positif ou nul. Lorsque $C$ est convexe, le segment $[x^*,y]$ est automatiquement contenu dans $C$ pour tous $x^*,y\in C$.

Si $f$ est deux fois différentiable près d'un minimum local **intérieur**, sa Hessienne au point est positive semi-définie. Une Hessienne positive **définie** au point critique garantit un minimum local strict. Une Hessienne seulement semi-définie au point ne suffit pas : $x^3$ a $f'(0)=f''(0)=0$. Ces critères restent *locaux* ; c'est la convexité qui permettra une conclusion globale.

## 5. Ensembles et fonctions convexes (diapositives 26 à 35)

### Ensembles convexes

Un ensemble $C$ est **convexe** si le segment entre deux de ses points reste dans $C$ :

$$x,y\in C,\ \alpha\in[0,1]\quad\Longrightarrow\quad \alpha x+(1-\alpha)y\in C.$$

### Prérequis : que décrit la formule du segment ?

Dans $\mathbb R^n$, un point est une liste de coordonnées, par exemple $(2,1)$ dans le plan. Un ensemble $C$ est la collection des points autorisés par une contrainte. Pour deux points $x$ et $y$, les points du segment droit qui les relie ont la forme

$$\alpha x+(1-\alpha)y,\qquad \alpha\in[0,1].$$

Le coefficient $\alpha$ règle la position entre les deux extrémités : $\alpha=1$ donne $x$, $\alpha=0$ donne $y$, et $\alpha=\tfrac12$ donne leur milieu. Avec $x=(0,0)$, $y=(4,2)$ et $\alpha=\tfrac14$, on trouve

$$\tfrac14(0,0)+\tfrac34(4,2)=(3,1.5).$$

Un ensemble convexe contient donc les deux extrémités **et** tous les points intermédiaires. Il n'a pas de trou ou de séparation capable de couper un segment entre deux de ses points.

### Exemples et contre-exemples

- L'intervalle $[0,1]$ est convexe : tout nombre entre deux nombres de l'intervalle y reste.
- La boule pleine $B(c,r)=\{x:\|x-c\|\leq r\}$ est convexe. En revanche, le cercle, qui ne contient que le bord de cette boule, ne l'est pas : un segment entre deux points du cercle passe généralement à l'intérieur.
- Un **demi-espace** est un côté d'une droite ou d'un plan : $C=\{x:\langle u,x\rangle\leq\delta\}$. Dans le plan, avec $u=(1,1)$ et $\delta=1$, cette contrainte est $x_1+x_2\leq1$. Deux points qui respectent cette inégalité et le segment entre eux restent du même côté de la droite, donc l'ensemble est convexe.
- Un **simplexe** est une généralisation du triangle plein. Par exemple, $\{(x_1,x_2):x_1\geq0,\ x_2\geq0,\ x_1+x_2\leq1\}$ est le triangle plein de sommets $(0,0)$, $(1,0)$ et $(0,1)$. Le simplexe $\{p\in\mathbb R^n:p_i\geq0,\ \sum_i p_i=1\}$ représente les vecteurs de probabilités.
- Un anneau n'est pas convexe : un segment entre deux points opposés traverse son trou. Deux disques séparés ne le sont pas non plus, car le segment entre un point de chaque disque traverse l'espace vide qui les sépare.

### Combiner des ensembles convexes

L'**intersection** $C_1\cap C_2$ signifie « respecter les deux contraintes ». Si $C_1$ et $C_2$ sont convexes, deux points qui respectent les deux contraintes ont un segment qui reste dans $C_1$ et dans $C_2$ ; il reste donc dans leur intersection. C'est pourquoi l'on peut combiner plusieurs contraintes convexes sans perdre la convexité.

Le **produit cartésien** rassemble deux choix indépendants :

$$C_1\times C_2=\{(x_1,x_2):x_1\in C_1,\ x_2\in C_2\}.$$

Par exemple, $[0,1]\times[0,2]$ est un rectangle plein, donc convexe. La **somme de Minkowski** rassemble les sommes possibles de deux points :

$$C_1+C_2=\{x_1+x_2:x_1\in C_1,\ x_2\in C_2\}.$$

Elle conserve aussi la convexité. En dimension $1$, $[0,1]+[2,4]=[2,5]$.

### Enveloppe convexe : remplir ce qui manque entre les points

L'**enveloppe convexe** $\operatorname{conv}(C)$ est le plus petit convexe contenant $C$. On peut l'imaginer comme la forme obtenue en tendant un élastique autour des points de $C$. Elle est constituée des **moyennes pondérées**

$$\sum_{i=1}^k\alpha_i x_i,\qquad x_i\in C,\quad \alpha_i\geq0,\quad \sum_{i=1}^k\alpha_i=1.$$

Les poids $\alpha_i$ sont des proportions : ils sont positifs ou nuls et leur somme vaut $1$. Avec deux points, cette formule redonne le segment. Avec trois points non alignés dans le plan, elle donne le triangle plein.

> [!example] Exemple calculé d'enveloppe convexe
> Prends $A=(0,0)$, $B=(4,0)$ et $D=(0,2)$, et pose $S=\{A,B,D\}$. Ce n'est pas un ensemble convexe : le milieu $\tfrac12A+\tfrac12B=(2,0)$ n'est pas dans $S$. Son enveloppe convexe est le triangle plein de sommets $A$, $B$ et $D$.
>
> Par exemple, avec les poids $\alpha=\tfrac12$, $\beta=\tfrac14$ et $\gamma=\tfrac14$, qui somment bien à $1$,
>
> $$\alpha A+\beta B+\gamma D=\tfrac12(0,0)+\tfrac14(4,0)+\tfrac14(0,2)=(1,0.5).$$
>
> Le point $(1,0.5)$ appartient donc à $\operatorname{conv}(S)$ sans appartenir à $S$. On peut aussi décrire exactement ce triangle par $\operatorname{conv}(S)=\{(x,y):x\geq0,\ y\geq0,\ x+2y\leq4\}$.

### Fonctions convexes et épigraphes

Une fonction est **convexe** si elle a une forme de bol, éventuellement avec un fond plat ou un angle. Pour la tester, prends deux points sur son graphe, puis trace le segment droit qui les relie. La courbe doit rester sous ce segment.

La formule

$$f(\alpha x+(1-\alpha)y)\leq\alpha f(x)+(1-\alpha)f(y),\qquad \alpha\in[0,1]$$

dit exactement cela. $\alpha x+(1-\alpha)y$ est un point entre $x$ et $y$. Par exemple, avec $\alpha=\tfrac12$, c'est le milieu $(x+y)/2$. Le membre de droite est la hauteur du segment droit au même endroit.

**Exemple avec $f(t)=t^2$.** Prends $x=0$ et $y=2$. Au milieu, $t=1$. La courbe vaut $f(1)=1$, alors que le milieu du segment a pour hauteur $(f(0)+f(2))/2=(0+4)/2=2$. On a bien $1\leq2$ : la parabole est sous sa corde.

Les fonctions $t\mapsto t^2$, $t\mapsto|t|$ et $t\mapsto\max(t,0)$ sont convexes. Une droite l'est aussi, car elle coïncide avec toutes ses cordes. $|t|$ n'est pas **strictement** convexe : entre $1$ et $2$, son graphe est déjà une droite. Une fonction strictement convexe reste, elle, strictement sous la corde dès qu'on choisit deux points distincts. C'est ce qui aide à obtenir un minimiseur unique.

L'**épigraphe** est la zone au-dessus du graphe. Dire que $f$ est convexe revient à dire que cette zone est convexe : si tu choisis deux points au-dessus de la courbe, tout le segment entre eux reste aussi au-dessus. C'est une autre façon de représenter la même idée ; la formule avec la corde est souvent plus simple pour commencer.

### Domaine, contraintes et la notation $\Gamma_0(H)$

Le domaine $\operatorname{dom}f$ est l'ensemble des entrées pour lesquelles $f$ donne une vraie valeur numérique, donc une valeur différente de $+\infty$. Par exemple,

$$f(x)=\begin{cases}x^2&\text{si }x\geq0,\\+\infty&\text{si }x<0\end{cases}$$

a pour domaine $[0,+\infty[$. Les nombres négatifs sont exclus.

Pour une fonction convexe, le domaine est forcément convexe. En effet, si $x$ et $y$ sont dans le domaine, alors $f(x)$ et $f(y)$ sont finis. L'inégalité de convexité impose que $f(\alpha x+(1-\alpha)y)$ soit aussi fini. Tout point entre $x$ et $y$ est donc encore dans le domaine.

Pour un ensemble de contraintes $C$, l'indicatrice $\iota_C$ vaut

$$\iota_C(x)=\begin{cases}0&\text{si }x\in C,\\+\infty&\text{si }x\notin C.\end{cases}$$

Elle encode une contrainte : minimiser $g(x)$ sous la condition $x\in C$ revient à minimiser $g(x)+\iota_C(x)$ sans écrire de contrainte à côté. Hors de $C$, le coût est $+\infty$, donc aucun point interdit ne peut être une solution.

L'indicatrice est convexe exactement quand l'ensemble $C$ est convexe. Pour $C=[0,1]$, deux nombres autorisés, par exemple $0$ et $1$, ont tous leurs intermédiaires autorisés. À l'inverse, pour $C=\{-1,1\}$, le milieu $0$ est interdit : $\iota_C$ ne peut pas être convexe.

Enfin, $\Gamma_0(H)$ est le nom de la famille des fonctions qui réunissent trois propriétés utiles en optimisation : elles sont convexes, semi-continues inférieurement et propres. Pour une indicatrice $\iota_C$, cela revient simplement à demander que $C$ soit non vide, fermé et convexe :

- **convexe** : les segments entre points autorisés restent autorisés ;
- **fermé** : on ne perd pas un point limite sur le bord ;
- **non vide** : au moins un point est autorisé.

Par exemple, $C=(0,1)$ est convexe et non vide, mais il n'est pas fermé : les points autorisés $1/k$ convergent vers $0$, qui est interdit. C'est précisément une situation où l'on peut approcher un minimum sans jamais l'atteindre.

### Pourquoi la convexité change le problème

Pour une fonction convexe, **tout minimum local est global**. Si $x^*$ était local mais si un point $y$ avait une valeur plus basse, les points du segment $x^*+\alpha(y-x^*)$, arbitrairement proches de $x^*$ pour $\alpha>0$ petit, auraient eux aussi une valeur plus basse : contradiction.

L'ensemble des minimiseurs est convexe. Si $f$ est **strictement convexe**, il y a **au plus un** minimiseur : deux minimiseurs distincts auraient un milieu de valeur encore plus basse. « Au plus un » ne garantit pas l'existence : $e^x$ est strictement convexe sur $\mathbb R$ et n'atteint pas son infimum.

La **forte convexité** quantifie la courbure. Pour $\beta>0$, $f$ est $\beta$-fortement convexe si $x\mapsto f(x)-\tfrac\beta2\|x\|^2$ est convexe. Elle implique la stricte convexité. Exemple : $x\mapsto x^2$ est $2$-fortement convexe. Pour $\beta=0$, on retrouve simplement la convexité ; ce cas limite ne donne pas l'unicité. L'identité utile est

$$\|\alpha x+(1-\alpha)y\|^2=\alpha\|x\|^2+(1-\alpha)\|y\|^2-\alpha(1-\alpha)\|x-y\|^2.$$

Elle montre directement pourquoi la norme au carré est strictement convexe et d'où vient le terme quadratique supplémentaire dans l'inégalité de forte convexité. Le cours parle aussi de **faible convexité** pour certains paramètres $\beta<0$ : ajouter une quantité suffisante de $\|x\|^2$ rend alors la fonction convexe, mais la fonction de départ n'est pas nécessairement convexe.

> [!tip] Existence + unicité
> Pour conclure « il existe une unique solution », fais **deux vérifications** : existence en combinant semi-continuité inférieure avec compacité ou coercivité ; unicité par stricte convexité. La stricte convexité seule ne suffit pas.

## 6. Convexité et calcul différentiel (diapositives 37 à 44)

Sur un domaine ouvert convexe, une fonction différentiable est convexe **si et seulement si** chacune de ses tangentes reste sous le graphe :

$$f(y)\geq f(x)+\langle\nabla f(x),y-x\rangle.$$

Pour $f(t)=t^2$, le membre de gauche moins celui de droite vaut $(y-x)^2\geq0$. Si l'inégalité est stricte pour $x\ne y$, la fonction est strictement convexe.

Une autre caractérisation est la **monotonie du gradient** :

$$\langle\nabla f(y)-\nabla f(x),y-x\rangle\geq0.$$

En dimension 1, cela dit simplement que la dérivée ne décroît pas. Si $f$ est deux fois différentiable, la convexité équivaut à $\nabla^2f(x)$ positive semi-définie en tout point du domaine. Une Hessienne positive définie partout implique la stricte convexité, mais n'est pas nécessaire : $f(t)=t^4$ est strictement convexe alors que $f''(0)=0$.

### Critère d'optimalité avec contraintes

Si $f$ est **convexe et différentiable** sur un domaine ouvert contenant l'ensemble **convexe** $C$, alors $x^*\in C$ est un minimiseur global sur $C$ **si et seulement si**

$$\boxed{\forall y\in C,\quad\langle\nabla f(x^*),y-x^*\rangle\geq0.}$$

### Comment lire cette condition ?

Si l'on part de $x^*$ vers un autre point faisable $y$, le vecteur $y-x^*$ est la direction dans laquelle on se déplace. Le produit scalaire

$$\langle\nabla f(x^*),y-x^*\rangle$$

indique ce qui arriverait à $f$ au début de ce déplacement : il est négatif si cette direction fait d'abord baisser $f$, positif si elle la fait monter, et nul si $f$ ne varie pas au premier ordre. La condition du théorème signifie donc simplement : **aucun déplacement autorisé depuis $x^*$ ne fait baisser le coût.**

Pour une fonction quelconque, ce constat est seulement local : la fonction pourrait remonter puis redescendre plus loin. La convexité empêche ce comportement. Sa tangente est sous son graphe partout, donc

$$f(y)\geq f(x^*)+\langle\nabla f(x^*),y-x^*\rangle\geq f(x^*).$$

Ainsi, l'absence de direction faisable descendante suffit à garantir que $x^*$ est le minimum sur **tout** $C$. C'est la suffisance. La nécessité est plus intuitive encore : s'il existait un $y\in C$ avec un produit scalaire négatif, un tout petit déplacement de $x^*$ vers $y$ diminuerait $f$, ce qui est impossible au minimum.

Si $x^*$ est intérieur à $C$, on peut se déplacer un peu dans n'importe quelle direction $v$, mais aussi dans la direction opposée $-v$. Les deux inégalités imposent alors $\langle\nabla f(x^*),v\rangle=0$ pour tout $v$, ce qui donne $\nabla f(x^*)=0$. Au bord, certaines directions utiles peuvent être interdites : le gradient n'a donc pas besoin d'être nul.

> [!example] Exemple calculé : un minimum au coin d'une contrainte
> Minimise
>
> $$f(x_1,x_2)=(x_1-3)^2+(x_2+1)^2$$
>
> sur $C=[0,1]\times[0,+\infty)$. Sans contrainte, le minimum serait $(3,-1)$, car les deux carrés y sont nuls. Ce point est interdit : il ne respecte ni $x_1\leq1$, ni $x_2\geq0$. L'objectif est la distance au carré à $(3,-1)$ ; parmi les points autorisés, le meilleur candidat est donc le coin $x^*=(1,0)$.
>
> Son gradient vaut $\nabla f(x^*)=(-4,2)$. La première composante, négative, dit qu'augmenter $x_1$ ferait diminuer $f$, mais on est déjà bloqué par la frontière $x_1=1$. La seconde, positive, dit qu'augmenter $x_2$ ferait augmenter $f$ ; descendre pourrait aider, mais est interdit par $x_2\geq0$.
>
> Pour tout $y=(a,b)\in C$, on a $a\leq1$ et $b\geq0$, donc
>
> $$\langle\nabla f(x^*),y-x^*\rangle=-4(a-1)+2b=4(1-a)+2b\geq0.$$
>
> Aucun déplacement faisable ne fait baisser $f$. Comme $f$ est convexe, $(1,0)$ est le minimum global. Il est même unique, car la Hessienne $\nabla^2f=2I$ est positive définie : $f$ est strictement convexe.

**Version forte.** Si $f$ est $\beta$-fortement convexe et différentiable, la tangente vérifie $f(y)\geq f(x)+\langle\nabla f(x),y-x\rangle+\frac\beta2\|y-x\|^2$. Le dernier terme mesure combien le graphe se tient *au-dessus* de la tangente.

## 7. Projections : le cas fondamental (diapositives 46 à 50)

Pour un ensemble $C$ **non vide, fermé et convexe**, la projection $P_C(x)$ est l'unique point de $C$ le plus proche de $x$ :

$$P_C(x)=\operatorname*{argmin}_{y\in C}\tfrac12\|y-x\|^2.$$

La formule signifie simplement : parmi tous les points autorisés $y\in C$, on cherche celui qui minimise sa distance à $x$. Le carré et le facteur $\tfrac12$ ne changent pas le point trouvé ; ils simplifient les dérivées plus tard. Ainsi, si $C=[0,1]$ et $x=3$, le point le plus proche est $P_C(3)=1$.

Pourquoi ce point existe-t-il ? En dimension finie, les candidats très loin de $x$ ne peuvent pas être les meilleurs, car $\|y-x\|^2$ devient alors énorme. On peut donc se limiter à une grande boule autour de $x$. Dans cette boule, l'intersection avec $C$ est fermée et bornée, donc compacte : la distance au carré y atteint sa plus petite valeur. La fermeture de $C$ est essentielle. Par exemple, sur $C=(0,1)$, le point de $C$ le plus proche de $x=0$ n'existe pas : on peut s'approcher de $0$ autant qu'on veut, mais $0$ est exclu.

Pourquoi est-il unique ? Supposons que deux points distincts $p$ et $q$ de $C$ soient tous deux les plus proches de $x$. Puisque $C$ est convexe, leur milieu $m=\tfrac12(p+q)$ appartient encore à $C$. Or ce milieu est strictement plus près de $x$ que $p$ et $q$ : cela contredit le fait qu'ils étaient minimaux. Géométriquement, sur un ensemble convexe, il ne peut pas y avoir deux « pieds » différents depuis $x$. La convexité est donc indispensable : pour $C=\{-1,1\}$ et $x=0$, les deux points sont à la même distance de $x$, mais $C$ n'est pas convexe car son milieu $0$ n'est pas dans $C$.

La formule suivante est utile pour les démonstrations et les calculs, mais commence par la lire en dimension $1$. Reprenons $C=[0,1]$, $x=3$ et $p=1$ :

```text
0 -------- p=1 -------- x=3
|--------- C ---------|
```

Depuis $p=1$, tous les autres points autorisés $y\in C$ sont à gauche. Il est impossible d'avancer vers $x=3$ sans sortir de $C$. C'est exactement ce qu'exprime le calcul

$$\underbrace{x-p}_{3-1=2}\underbrace{(y-p)}_{y-1\leq0}=2(y-1)\leq0.$$

En plusieurs dimensions, $x-p$ est la flèche qui part de $p$ vers $x$, et $y-p$ est la flèche qui part de $p$ vers n'importe quel autre point autorisé. La formule dit que tous les déplacements possibles dans $C$ vont sur le côté ou dans le sens opposé à $x$. Aucun ne peut se diriger vers $x$ ; c'est donc bien que $p$ est le point autorisé le plus proche.

La caractérisation à retenir est

$$\boxed{p=P_C(x)\quad\Longleftrightarrow\quad p\in C\ \text{et}\ \forall y\in C,\ \langle x-p,y-p\rangle\leq0.}$$

Le produit scalaire est négatif ou nul lorsque les deux flèches forment un angle obtus ou droit. Si tu rencontres cette formule dans une preuve, retiens donc l'image simple : depuis la projection $p$, on ne peut plus aller vers $x$ tout en restant dans $C$.

Le **cône normal** $N_C(p)=\{u:\langle u,y-p\rangle\leq0\ \forall y\in C\}$ regroupe ces directions tournées vers l'extérieur. La condition de projection devient $x-p\in N_C(p)$. Si $p$ est intérieur à $C$, le cône normal se réduit à $\{0\}$.

Si $C$ est un sous-espace vectoriel, alors $N_C(p)=C^\perp$ pour tout $p\in C$. L'orthogonal $C^\perp$ rassemble les vecteurs perpendiculaires à **tous** les vecteurs de $C$. Prenons l'axe horizontal $C=\{(a,0):a\in\mathbb R\}$ : son orthogonal est l'axe vertical $C^\perp=\{(0,b):b\in\mathbb R\}$. Les directions normales sont donc verticales, vers le haut comme vers le bas.

Voici pourquoi. Comme $p,y\in C$, le vecteur $v=y-p$ appartient encore à $C$. La définition du cône normal impose $\langle u,v\rangle\leq0$ pour tout $v\in C$. Mais un sous-espace contient aussi le vecteur opposé $-v$. On a donc simultanément

$$\langle u,v\rangle\leq0\qquad\text{et}\qquad\langle u,-v\rangle=-\langle u,v\rangle\leq0.$$

Cela force $\langle u,v\rangle=0$. Ainsi, $u$ est perpendiculaire à chaque vecteur $v$ de $C$, ce qui est précisément la définition de $u\in C^\perp$.

### Quatre projections à savoir calculer

Dans tous les exemples suivants, projeter signifie : **prendre le point autorisé le plus proche**.

1. **Une droite.** Considère l'axe horizontal : tous les points de la forme $(a,0)$. Pour projeter $(a,b)$ sur cet axe, on garde sa position horizontale $a$ et on ramène sa hauteur à $0$. Donc $P_C(a,b)=(a,0)$. Exemple : $(3,5)$ devient $(3,0)$.

2. **Un intervalle.** Si l'on doit choisir un nombre entre $a$ et $b$, on laisse $x$ tranquille lorsqu'il est déjà dans l'intervalle. S'il est trop petit, on le remplace par $a$ ; s'il est trop grand, par $b$. Exemple : la projection de $7$ sur $[0,4]$ est $4$, et celle de $-2$ est $0$. La formule compacte est $P_{[a,b]}(x)=\min(b,\max(a,x))$.

3. **Des coordonnées positives.** L'ensemble $[0,+\infty)^n$ impose que chaque coordonnée soit positive ou nulle. On remplace donc chaque coordonnée négative par $0$ et on garde les autres. Exemple : $(-2,3,-1)$ devient $(0,3,0)$.

4. **Un côté d'une droite.** Dans le plan, $C=\{(z_1,z_2):z_1+z_2\leq1\}$ est toute la région située sous la droite $z_1+z_2=1$. Le point $(2,1)$ n'est pas autorisé car $2+1>1$. Le point autorisé le plus proche est $(1,0)$, qui est sur la droite : on déplace $(2,1)$ tout droit vers cette frontière. En revanche, un point déjà du bon côté, comme $(0,0)$, ne bouge pas.

   En général, pour $C=\{z:\langle u,z\rangle\leq\delta\}$, la direction $u$ est perpendiculaire à la frontière. La formule ci-dessous retire juste la quantité nécessaire dans cette direction ; le $\max(\cdot,0)$ signifie « ne rien retirer si le point est déjà autorisé » :

   $$P_C(x)=x-\frac{\max(\langle u,x\rangle-\delta,0)}{\|u\|^2}u.$$

   Dans l'exemple, $u=(1,1)$ et $\delta=1$. Comme $\langle u,(2,1)\rangle-\delta=2$ et $\|u\|^2=2$, on retire $(1,1)$ : $(2,1)-(1,1)=(1,0)$.

La projection ne peut pas amplifier les distances : $\|P_C(x)-P_C(y)\|\leq\|x-y\|$. Le cours donne la propriété plus forte de **ferme non-expansivité** :

$$\|P_C(x)-P_C(y)\|^2\leq\langle x-y,P_C(x)-P_C(y)\rangle.$$

On l'obtient en écrivant la caractérisation de $P_C(x)$ avec le candidat $P_C(y)$, puis celle de $P_C(y)$ avec $P_C(x)$, et en additionnant. Par Cauchy-Schwarz, elle implique la première inégalité. Ainsi, une petite perturbation de $x$ ne fait pas sauter sa projection. La distance à l'ensemble, $d_C(x)=\|x-P_C(x)\|$, est elle aussi continue.

## Méthode de résolution à garder sous la main

Pour un nouveau problème $\min_{x\in C}f(x)$ :

1. **Vérifie la faisabilité :** $C\cap\operatorname{dom}f\ne\varnothing$.
2. **Cherche l'existence :** $f$ propre et s.c.i. sur $C$ fermé et borné, ou $f$ propre, s.c.i. et coercif sur $C$ fermé. En dimension finie, ces critères sont directement utilisables.
3. **Cherche la convexité :** $C$ convexe et $f$ convexe. Alors un critère local donnera une réponse globale.
4. **Écris l'optimalité :** $\nabla f(x^*)=0$ à l'intérieur ; au bord, utilise $\langle\nabla f(x^*),y-x^*\rangle\geq0$ pour tout $y\in C$.
5. **Vérifie l'unicité :** la stricte convexité de $f$ sur $C$ suffit dès que l'existence est établie.
6. **Si $f(y)=\frac12\|y-x\|^2$ :** reconnais une projection et utilise une formule connue ou le cône normal.

### Erreurs fréquentes

- $\nabla f(x^*)=0$ ne s'impose pas au bord d'une contrainte.
- Hessienne positive semi-définie **en un seul point** ne prouve pas que la fonction est convexe partout, ni que ce point est un minimum.
- Convexité seule n'assure pas l'existence ; stricte convexité seule n'assure pas non plus l'existence.
- Fermeture du domaine et convexité du domaine sont deux propriétés différentes.
- Pour une fonction à valeurs infinies, $+\infty$ signifie « interdit », pas « coût très élevé mais fini ».

> [!summary] Les trois formules centrales
> **Tangente sous le graphe :** $f(y)\geq f(x)+\langle\nabla f(x),y-x\rangle$ pour une fonction convexe différentiable.
> 
> **Optimalité sous contrainte convexe :** $\langle\nabla f(x^*),y-x^*\rangle\geq0$ pour tout $y\in C$.
> 
> **Projection :** $p=P_C(x)$ si et seulement si $\langle x-p,y-p\rangle\leq0$ pour tout $y\in C$.

> [!note] Exercices
> Les énoncés et corrections des deux parties sont regroupés dans [[prerentree/optimisation-convexe/notes/exercices-corriges|Exercices corrigés - optimisation convexe]].

## Partie II - contraintes générales et algorithmes

La première partie répondait surtout à « qu'est-ce qu'un problème convexe bien posé ? ». La seconde répond à deux questions pratiques : comment gérer des contraintes décrites par des équations ou inégalités, puis comment calculer effectivement une solution.

## 8. Trouver seulement un point faisable : projections alternées (diapositives 3 à 5)

Parfois, le premier objectif n'est pas de minimiser un coût, mais simplement de satisfaire plusieurs contraintes convexes : trouver un point de

$$C_1\cap\cdots\cap C_m,$$

où chaque $C_i$ est fermé et convexe et où l'intersection est supposée non vide. C'est un **problème de faisabilité**.

L'algorithme **POCS** (*Projection Onto Convex Sets*) consiste à corriger une contrainte à la fois. À partir de $x_0$, on projette cycliquement :

$$x_{n+1}=P_{C_{i_n}}(x_n),\qquad i_n=1+(n\bmod m).$$

On peut aussi ne faire qu'une fraction de la correction :

$$x_{n+1}=x_n+\omega_n\bigl(P_{C_{i_n}}(x_n)-x_n\bigr),$$

avec $\omega_n\in[\varepsilon_1,2-\varepsilon_2]$, où $\varepsilon_1,\varepsilon_2>0$ et $\varepsilon_1+\varepsilon_2<2$. Les pas restent donc uniformément à l'écart de $0$ et de $2$. Le cas $\omega_n=1$ est la projection entière ; avec $0<\omega_n<1$, on avance plus prudemment vers la contrainte satisfaite.

> [!example] Image à garder en tête
> Imagine deux ou trois zones autorisées qui se chevauchent. Si le point courant viole la première, on le ramène au point le plus proche de cette zone ; puis on fait de même pour la seconde, etc. Une projection peut faire ressortir légèrement d'une contrainte corrigée auparavant, mais les corrections cycliques convergent vers un point commun lorsque l'intersection n'est pas vide (en dimension finie).

Cette idée est très générale : dès que les projections $P_{C_i}$ se calculent facilement, les contraintes peuvent être traitées séparément. La projection sur une boule et un exemple complet de POCS figurent dans la note [[prerentree/optimisation-convexe/notes/exercices-corriges|d'exercices]].

## 9. Contraintes générales : multiplicateurs et dualité (diapositives 7 à 14)

### Écrire les contraintes

On considère maintenant

$$\min_{x\in H} f(x)\quad\text{sous}\quad
g_i(x)=0\ (i=1,\ldots,m),\qquad h_j(x)\leq0\ (j=1,\ldots,q).$$

Un point est **faisable** lorsqu'il est dans $\operatorname{dom}f$ et respecte toutes ces contraintes. Les égalités sont des contraintes rigides ; les inégalités possèdent une marge. Par exemple, $h(x)=x-1\leq0$ autorise tout $x\leq1$, et elle est **active** au bord $x=1$.

### Le Lagrangien : faire entrer les contraintes dans le coût

On introduit un multiplicateur libre $\mu_i\in\mathbb R$ pour chaque égalité, et un multiplicateur positif $\omega_j\geq0$ pour chaque inégalité :

$$L(x,\mu,\omega)=f(x)+\sum_{i=1}^m\mu_i g_i(x)+\sum_{j=1}^q\omega_jh_j(x).$$

Pourquoi $\omega_j$ doit-il être positif ? Si $h_j(x)>0$, la contrainte est violée ; le terme $\omega_jh_j(x)$ doit pouvoir pénaliser cette violation, jamais la récompenser. À l'inverse, une égalité peut être violée des deux côtés, d'où un coefficient de signe libre.

> [!example] Une contrainte active crée un multiplicateur
> Pour minimiser $(x-3)^2$ sous $x\leq1$, prends $h(x)=x-1$ et $L(x,\omega)=(x-3)^2+\omega(x-1)$, avec $\omega\geq0$. La stationnarité donne $2(x-3)+\omega=0$. Si la contrainte était inactive, $\omega=0$ donnerait $x=3$, impossible. Elle est donc active : $x^*=1$, puis $\omega^*=4$. Le multiplicateur mesure ici la « force » avec laquelle la frontière empêche le minimum non contraint d'être atteint.

### Point selle et dualité

Un triplet $(x^*,\mu^*,\omega^*)$ est un **point selle** du Lagrangien lorsque

$$L(x^*,\mu,\omega)\leq L(x^*,\mu^*,\omega^*)\leq L(x,\mu^*,\omega^*)$$

pour tout $x$, tout $\mu$ et tout $\omega\geq0$. On minimise donc par rapport à $x$ et l'on maximise par rapport aux multiplicateurs. Cette opposition est la dualité : le primal cherche une solution faisable bon marché, le dual cherche les pénalités qui rendent toute autre solution moins attractive.

On peut la résumer par deux fonctions :

$$d(\mu,\omega)=\inf_x L(x,\mu,\omega)\quad\text{(fonction duale)},\qquad
p(x)=\sup_{\mu,\,\omega\geq0}L(x,\mu,\omega)\quad\text{(fonction primale lagrangienne)}.$$

Un point selle fournit un minimiseur primal. Il donne aussi la **complémentarité**

$$\boxed{\omega_j^*h_j(x^*)=0\quad\text{pour tout }j.}$$

Cette formule ne dit pas que tous les multiplicateurs sont nuls : elle dit qu'une inégalité est soit inactive ($h_j(x^*)<0$, donc $\omega_j^*=0$), soit active ($h_j(x^*)=0$, auquel cas son multiplicateur peut être positif).

### Cas convexe : quand les conditions deviennent suffisantes

Supposons $f$ convexe, les $g_i$ affines et les $h_j$ convexes. La **condition de Slater** demande un point qui respecte les égalités et rend toutes les inégalités strictes :

$$g_i(\bar x)=0,\qquad h_j(\bar x)<0,$$

avec $\bar x$ dans l'intérieur du domaine de $f$. Intuitivement, il doit exister une petite marge de manœuvre, pas seulement un ensemble faisable réduit à une frontière fragile.

Sous cette condition, $x^*$ minimise le problème contraint **si et seulement si** il existe des multiplicateurs pour lesquels $(x^*,\mu^*,\omega^*)$ est un point selle. En pratique, on minimise alors $L(\cdot,\mu^*,\omega^*)$ par rapport à $x$, puis on combine la stationnarité avec la complémentarité pour déterminer les multiplicateurs.

### KKT : la version différentielle à utiliser au calcul

En dimension finie, si les fonctions sont continûment différentiables et qu'une qualification de contraintes est satisfaite, tout minimum local vérifie les conditions de **Karush-Kuhn-Tucker** :

$$
\begin{aligned}
&g_i(x^*)=0,\quad h_j(x^*)\leq0 &&\text{(faisabilité primale)},\\
&\omega_j^*\geq0 &&\text{(faisabilité duale)},\\
&\omega_j^*h_j(x^*)=0 &&\text{(complémentarité)},\\
&\nabla f(x^*)+\sum_i\mu_i^*\nabla g_i(x^*)+\sum_j\omega_j^*\nabla h_j(x^*)=0 &&\text{(stationnarité).}
\end{aligned}
$$

La qualification de Mangasarian-Fromovitz du cours évite les contraintes dégénérées : les gradients des égalités sont indépendants et il existe une direction tangentielle aux égalités qui diminue strictement toutes les inégalités actives. Dans le cas convexe avec Slater, KKT n'est plus seulement nécessaire : c'est un certificat d'optimalité globale.

> [!example] Sur une sphère
> Maximiser $t^3-\tfrac12t^2$ où $t=x_N$ sous $\|x\|=1$ revient à minimiser $\tfrac12x_N^2-x_N^3$ avec l'égalité $\|x\|^2-1=0$. KKT montre que les candidats ont $x_i=0$ pour $i<N$ et $x_N\in\{-1,0,\tfrac13,1\}$. Comparer les valeurs donne le maximum $x^*=(0,\ldots,0,1)$. KKT produit les candidats ; il reste à comparer leurs valeurs lorsqu'il ne s'agit pas d'un problème convexe.

## 10. Calculer une solution : méthodes du premier ordre (diapositives 16 à 21)

### Descente de gradient et gradient projeté

L'approximation locale

$$f(x)\simeq f(x_n)+\langle\nabla f(x_n),x-x_n\rangle$$

dit que $-\nabla f(x_n)$ est la direction où $f$ baisse le plus vite à court terme. La descente de gradient est donc

$$x_{n+1}=x_n-\gamma_n\nabla f(x_n),\qquad\gamma_n>0.$$

Si $x$ doit rester dans un convexe $C$, on corrige le pas par projection :

$$\boxed{x_{n+1}=x_n+\theta_n\bigl(P_C(x_n-\gamma_n\nabla f(x_n))-x_n\bigr),}$$

où $\gamma_n$ est le pas de descente et $\theta_n\in]0,1]$ une relaxation. La projection garantit la faisabilité ; la relaxation évite éventuellement de faire toute la correction d'un coup.

Un point fixe de cette itération vérifie exactement

$$x\in C\quad\text{et}\quad\langle\nabla f(x),y-x\rangle\geq0\quad\forall y\in C.$$

Pour $f$ convexe, c'est le critère d'optimalité de la première partie : les points fixes sont donc précisément les minimiseurs globaux. Quand $C=H$ et $\theta_n=1$, on retrouve la descente de gradient usuelle.

### Ce que garantit le théorème de convergence

Si $f$ est convexe, possède un gradient $\rho$-Lipschitzien,

$$\|\nabla f(x)-\nabla f(y)\|\leq\rho\|x-y\|,$$

et a au moins un minimiseur sur $C$, le gradient projeté converge si les pas restent bornés comme suit :

$$0<\inf_n\gamma_n\leq\sup_n\gamma_n<\frac2\rho,\qquad
0<\inf_n\theta_n\leq\sup_n\theta_n\leq1.$$

Le seuil $2/\rho$ formalise l'intuition « ne pas faire un pas trop grand dans une vallée courbée ». Si $f$ est fortement convexe, le minimiseur est unique et la convergence est **linéaire** : l'erreur est multipliée à chaque itération par un facteur $\zeta<1$. Sans convexité, ces garanties globales disparaissent ; avec un gradient Lipschitzien et un pas assez petit, on sait au moins contrôler la décroissance des valeurs, pas forcément atteindre un minimum global.

### Uzawa : descendre en $x$, monter en multiplicateurs

Pour chercher un point selle $L(x,\omega)$ avec des multiplicateurs d'inégalité, Uzawa alterne :

1. minimiser $L(\cdot,\omega_n)$ par rapport à $x$ pour obtenir $x_n$ ;
2. faire une ascension de gradient sur $\omega$, puis projeter sur l'orthant positif :

$$\omega_{n+1}=\omega_n+\theta_n\left(P_{\mathbb R_+^q}\!\bigl(\omega_n+\gamma_n\nabla_\omega L(x_n,\omega_n)\bigr)-\omega_n\right).$$

Le signe est ici positif parce que le dual se **maximise**. Les multiplicateurs des contraintes violées tendent ainsi à augmenter.

### Frank-Wolfe : éviter une projection difficile

Projeter sur un polytope compliqué peut coûter cher. Frank-Wolfe ne projette pas : à $x_n$, il minimise la linéarisation sur $C$,

$$z_n\in\operatorname*{argmin}_{z\in C}\langle\nabla f(x_n),z\rangle,$$

puis avance sur le segment faisable :

$$x_{n+1}=(1-\gamma_n)x_n+\gamma_n z_n,\qquad\gamma_n\in]0,1].$$

Si $C$ est compact convexe et $x_0\in C$, toute itération reste automatiquement dans $C$. Pour un gradient Lipschitzien et $\gamma_n=2/(n+2)$, l'écart d'objectif est de l'ordre de $1/n$. C'est souvent plus lent que le gradient projeté, mais l'« oracle linéaire » $z_n$ est parfois beaucoup plus simple qu'une projection.

## 11. Utiliser la courbure : Newton, quasi-Newton et sous-espaces (diapositives 22 à 25)

La descente de gradient ne voit que la pente. Si $f$ est deux fois différentiable, Newton utilise également la courbure locale :

$$f(x)\simeq f(x_n)+\langle\nabla f(x_n),x-x_n\rangle
+\tfrac12\langle x-x_n,\nabla^2f(x_n)(x-x_n)\rangle.$$

Minimiser ce modèle quadratique donne

$$\boxed{x_{n+1}=x_n-[\nabla^2f(x_n)]^{-1}\nabla f(x_n),}$$

lorsque la Hessienne est définie positive. Cette méthode est spectaculaire près d'un minimum non dégénéré : l'erreur devient proportionnelle au **carré** de l'erreur précédente (convergence quadratique). En revanche, chaque pas impose de former ou résoudre un système avec la Hessienne, et le comportement loin du minimum peut être mauvais.

On emploie souvent un Newton amorti ou régularisé,

$$x_{n+1}=x_n-\gamma_n[\nabla^2f(x_n)+\eta_nI]^{-1}\nabla f(x_n),$$

pour stabiliser une Hessienne mal conditionnée. Les méthodes **quasi-Newton** remplacent ensuite la Hessienne inverse par une approximation définie positive $H_n^{-1}$, d'où

$$x_{n+1}=x_n-H_n^{-1}\nabla f(x_n).$$

Enfin, les méthodes à **sous-espace** cherchent le pas dans quelques directions mémorisées plutôt que dans tout l'espace :

$$x_{n+1}=x_n-\gamma_n^{(1)}\nabla f(x_n)+\gamma_n^{(2)}d_n,$$

par exemple avec $d_n=x_n-x_{n-1}$. On optimise les deux coefficients, exactement ou grâce à un modèle quadratique. C'est l'idée derrière des méthodes à mémoire comme L-BFGS : conserver assez d'information sur la courbure pour accélérer, sans stocker une Hessienne dense.

## Fil de résolution - version complète

Pour un problème nouveau, suis l'ordre suivant :

1. Identifie $f$, le domaine et le type de contraintes : ensemble $C$, égalités $g_i=0$ ou inégalités $h_j\leq0$.
2. Établis existence et unicité avant de lancer un calcul, lorsque les hypothèses le permettent.
3. Si les projections sont simples, utilise gradient projeté ; si l'objectif manque, POCS traite la faisabilité.
4. Si les contraintes sont différentiables, écris le Lagrangien et les conditions KKT. Vérifie Slater ou une qualification avant de les invoquer comme certificat.
5. Choisis l'algorithme selon le coût dominant : gradient pour la simplicité, Frank-Wolfe si la projection est difficile, Newton ou quasi-Newton si la courbure est accessible.

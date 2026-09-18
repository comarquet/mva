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

> [!example] Exemple calculé dans le plan : deux droites
> Posons
>
> $$C_1=\{(u,0):u\in\mathbb R\},\qquad C_2=\{(u,u):u\in\mathbb R\}.$$
>
> $C_1$ est l'axe horizontal, $C_2$ est la diagonale $y=x$, et leur unique point commun est $(0,0)$. Partons de $x_0=(2,3)$ et projetons alternativement sur $C_1$, puis sur $C_2$.
>
> - La projection sur $C_1$ annule la seconde coordonnée : $P_{C_1}(a,b)=(a,0)$. Ainsi, $x_1=(2,0)$.
> - La projection sur $C_2$ remplace les deux coordonnées par leur moyenne : $P_{C_2}(a,b)=\bigl(\tfrac{a+b}{2},\tfrac{a+b}{2}\bigr)$. Ainsi, $x_2=(1,1)$.
>
> Les itérations sont donc
>
> $$\begin{aligned}
> (2,3)&\to(2,0)\to(1,1)\to(1,0)\\
> &\to\left(\tfrac12,\tfrac12\right)\to\left(\tfrac12,0\right)
> \to\left(\tfrac14,\tfrac14\right)\to\cdots
> \end{aligned}$$
>
> Chaque projection satisfait exactement une droite, mais peut faire quitter l'autre. Les corrections alternées se rapprochent néanmoins de $(0,0)$, qui satisfait les deux contraintes. Avec une sous-relaxation, par exemple $\omega_0=\tfrac12$, le premier pas s'arrête à mi-chemin de la projection :
>
> $$x_1=(2,3)+\tfrac12\bigl((2,0)-(2,3)\bigr)=(2,1.5).$$
>
> La correction est alors plus prudente, souvent plus lente.

> [!example] Visualiser la relaxation sur une seule contrainte
> Prenons seulement l'axe horizontal $C=\{(u,0):u\in\mathbb R\}$ et partons de $x_0=(2,4)$. La projection exacte est toujours $P_C(2,v)=(2,0)$.
>
> Avec une **sous-relaxation** constante $\omega_n=\tfrac12$, on ne parcourt que la moitié du chemin vers la projection :
>
> $$\begin{aligned}
> x_0&=(2,4),\\
> x_1&=(2,4)+\tfrac12\bigl((2,0)-(2,4)\bigr)=(2,2),\\
> x_2&=(2,2)+\tfrac12\bigl((2,0)-(2,2)\bigr)=(2,1),\\
> x_3&=(2,0.5),\quad\ldots
> \end{aligned}$$
>
> La distance à l'axe est divisée par deux à chaque étape : $(2,4)\to(2,2)\to(2,1)\to(2,0.5)\to\cdots\to(2,0)$. Avec $\omega_n=1$, on arriverait à $(2,0)$ en une seule itération.
>
> Avec une **sur-relaxation**, par exemple $\omega_n=\tfrac32$, on dépasse l'axe tout en convergeant : $(2,4)\to(2,-2)\to(2,1)\to(2,-0.5)\to\cdots\to(2,0)$. L'amplitude des oscillations diminue tant que $0<\omega_n<2$ ; au seuil $2$, elle ne diminue plus.

Cette idée est très générale : dès que les projections $P_{C_i}$ se calculent facilement, les contraintes peuvent être traitées séparément. La projection sur une boule et un exemple complet de POCS figurent dans la note [[prerentree/optimisation-convexe/notes/exercices-corriges|d'exercices]].

## 9. Contraintes générales : multiplicateurs et dualité (diapositives 7 à 14)

### Écrire les contraintes

On considère maintenant

$$\min_{x\in H} f(x)\quad\text{sous}\quad
g_i(x)=0\ (i=1,\ldots,m),\qquad h_j(x)\leq0\ (j=1,\ldots,q).$$

Un point est **faisable** lorsqu'il est dans $\operatorname{dom}f$ et respecte toutes ces contraintes. Les égalités sont des contraintes rigides ; les inégalités possèdent une marge. Par exemple, $h(x)=x-1\leq0$ autorise tout $x\leq1$, et elle est **active** au bord $x=1$.

> [!example] Exemple calculé : égalité, inégalités et contrainte active
> Dans $H=\mathbb R^2$, cherchons à minimiser
>
> $$f(u,v)=u^2+(v-1)^2$$
>
> sous les contraintes
>
> $$g(u,v)=u+v-1=0,\qquad h_1(u,v)=-u\leq0,\qquad h_2(u,v)=v-0.6\leq0.$$
>
> L'égalité impose $v=1-u$. Les inégalités disent respectivement $u\geq0$ et $v\leq0.6$ ; en remplaçant $v$ par $1-u$, la seconde donne $1-u\leq0.6$, donc $u\geq0.4$. L'ensemble faisable se réduit ainsi à $u\geq0.4$ avec $v=1-u$.
>
> Sur cette droite, le coût vaut
>
> $$f(u,1-u)=u^2+\bigl((1-u)-1\bigr)^2=2u^2.$$
>
> Comme $u\geq0.4>0$, la dérivée $4u$ est positive : $2u^2$ augmente avec $u$. Son minimum parmi les valeurs autorisées est donc atteint à la plus petite, $u^*=0.4$. Ainsi $v^*=1-u^*=0.6$ et
>
> $$\boxed{(u^*,v^*)=(0.4,0.6).}$$
>
> Au point optimal, $h_1(u^*,v^*)=-0.4<0$ est **inactive** : on n'est pas au bord $u=0$. En revanche, $h_2(u^*,v^*)=0$ est **active** : l'optimum est bloqué sur la frontière $v=0.6$.

### Le Lagrangien : faire entrer les contraintes dans le coût

On introduit un multiplicateur libre $\mu_i\in\mathbb R$ pour chaque égalité, et un multiplicateur positif $\omega_j\geq0$ pour chaque inégalité :

$$L(x,\mu,\omega)=f(x)+\sum_{i=1}^m\mu_i g_i(x)+\sum_{j=1}^q\omega_jh_j(x).$$

Pourquoi $\omega_j$ doit-il être positif ? Si $h_j(x)>0$, la contrainte est violée ; le terme $\omega_jh_j(x)$ doit pouvoir pénaliser cette violation, jamais la récompenser. À l'inverse, une égalité peut être violée des deux côtés, d'où un coefficient de signe libre.

> [!example] Une contrainte active crée un multiplicateur
> Pour minimiser $(x-3)^2$ sous $x\leq1$, prends $h(x)=x-1$ et $L(x,\omega)=(x-3)^2+\omega(x-1)$, avec $\omega\geq0$. La stationnarité donne $2(x-3)+\omega=0$. Si la contrainte était inactive, $\omega=0$ donnerait $x=3$, impossible. Elle est donc active : $x^*=1$, puis $\omega^*=4$. Le multiplicateur mesure ici la « force » avec laquelle la frontière empêche le minimum non contraint d'être atteint.
>
> **Comment trouve-t-on $x^*=1$ ?** La parabole $f(x)=(x-3)^2$ a son minimum sans contrainte en $x=3$, mais ce point viole $x\leq1$. Sur toutes les valeurs autorisées, se déplacer vers la droite rapproche de $3$ et diminue le coût : le meilleur choix est donc la plus grande valeur autorisée, $x^*=1$. Par exemple, $f(0)=9$ et $f(1)=4$.
>
> **Que signifie $-4+4=0$ ?** À $x=1$, la dérivée du coût seul vaut $f'(1)=2(1-3)=-4$. Le signe négatif dit qu'augmenter un peu $x$ ferait encore baisser le coût ; mais ce déplacement est interdit par la contrainte. Dans la stationnarité du Lagrangien,
>
> $$2(x^*-3)+\omega^*=0,$$
>
> le terme de contrainte doit donc apporter $+4$, d'où $\omega^*=4$. Ce n'est pas une force physique : c'est une manière algébrique de dire que le « mur » $x=1$ compense exactement la tendance de l'objectif à aller vers $3$.

### Point selle et dualité

Un triplet $(x^*,\mu^*,\omega^*)$ est un **point selle** du Lagrangien lorsque

$$L(x^*,\mu,\omega)\leq L(x^*,\mu^*,\omega^*)\leq L(x,\mu^*,\omega^*)$$

pour tout $x$, tout $\mu$ et tout $\omega\geq0$. On minimise donc par rapport à $x$ et l'on maximise par rapport aux multiplicateurs.

Le **problème primal** est le problème de départ : choisir directement un $x$ faisable qui minimise $f(x)$. Le **problème dual** cherche plutôt les multiplicateurs $(\mu,\omega)$ qui produisent la meilleure borne inférieure possible du coût optimal. Les deux problèmes parlent donc du même optimum, mais depuis deux points de vue : le primal construit une solution ; le dual construit un certificat indiquant qu'aucune solution faisable ne peut faire mieux.

On peut la résumer par deux fonctions :

$$d(\mu,\omega)=\inf_x L(x,\mu,\omega)\quad\text{(fonction duale)},\qquad
p(x)=\sup_{\mu,\,\omega\geq0}L(x,\mu,\omega)\quad\text{(fonction primale lagrangienne)}.$$

Un point selle fournit un minimiseur primal. Il donne aussi la **complémentarité**

$$\boxed{\omega_j^*h_j(x^*)=0\quad\text{pour tout }j.}$$

Cette formule ne dit pas q²ue tous les multiplicateurs sont nuls : elle dit qu'une inégalité est soit inactive ($h_j(x^*)<0$, donc $\omega_j^*=0$), soit active ($h_j(x^*)=0$, auquel cas son multiplicateur peut être positif).

> [!example] Point selle, primal et dual sur une droite
> Reprenons le problème primal
>
> $$\min_x (x-3)^2\qquad\text{sous }x\leq1.$$
>
> Sans contrainte, le meilleur point serait $3$, mais il est interdit. Le meilleur point faisable est donc $x^*=1$. Avec $h(x)=x-1$, le Lagrangien est
>
> $$L(x,\omega)=(x-3)^2+\omega(x-1),\qquad\omega\geq0.$$
>
> Pour $\omega^*=4$,
>
> $$L(x,4)=(x-3)^2+4(x-1)=(x-1)^2+4.$$
>
> Cette quantité est minimale en $x=1$. Inversement, si l'on fixe $x=1$, alors $L(1,\omega)=4$ pour tout $\omega\geq0$, car la contrainte est exactement saturée : $\omega(1-1)=0$. Ainsi
>
> $$L(1,\omega)\leq L(1,4)\leq L(x,4).$$
>
> C'est le point selle : $x^*=1$ minimise le Lagrangien lorsque le multiplicateur est fixé à $4$, et aucun choix de multiplicateur ne peut augmenter $L$ lorsque $x=1$.
>
> Pour comprendre $p(x)=\sup_{\omega\geq0}L(x,\omega)$, fixe d'abord $x$, puis laisse $\omega$ choisir la valeur qui rend $L(x,\omega)$ la plus grande possible.
>
> - **Point autorisé :** avec $x=0$, on a
>
>   $$L(0,\omega)=9+\omega(0-1)=9-\omega.$$
>
>   Les valeurs sont $9$ pour $\omega=0$, $7$ pour $\omega=2$, puis $-91$ pour $\omega=100$. La plus grande est donc $9$, obtenue avec $\omega=0$. Ainsi $p(0)=9=(0-3)^2$. Plus généralement, si $x\leq1$, le terme $\omega(x-1)$ est négatif ou nul : le plus grand choix est toujours $\omega=0$, et $p(x)=(x-3)^2$.
>
> - **Point interdit :** avec $x=2$, on a
>
>   $$L(2,\omega)=1+\omega(2-1)=1+\omega.$$
>
>   Les valeurs sont $1$ pour $\omega=0$, $11$ pour $\omega=10$ et $1001$ pour $\omega=1000$. On peut donc rendre $L(2,\omega)$ aussi grand que l'on veut : $p(2)=+\infty$. Plus généralement, pour tout $x>1$, le terme $\omega(x-1)$ est positif et tend vers $+\infty$ lorsque $\omega$ grandit.
>
> Donc $p$ transforme exactement la contrainte $x\leq1$ en une règle très simple : « si $x$ est interdit, son coût est infini ».
>
> $$p(x)=
> \begin{cases}
> (x-3)^2,&x\leq1,\\
> +\infty,&x>1.
> \end{cases}$$
>
> Minimiser $p$ revient donc exactement à chercher le plus petit coût parmi les seuls $x\leq1$. Pour la fonction duale, on fixe au contraire $\omega$ puis on minimise en $x$ :
>
> $$d(\omega)=\inf_xL(x,\omega).$$
>
> Ici, fixer $\omega$ transforme le Lagrangien en une simple parabole en $x$ :
>
> $$L(x,\omega)=x^2+(\omega-6)x+9-\omega.$$
>
> Son point le plus bas est atteint en
>
> $$x_\omega=3-\frac{\omega}{2},$$
>
> et sa valeur minimale est
>
> $$d(\omega)=2\omega-\frac{\omega^2}{4}.$$
>
> Quelques valeurs permettent de voir le mécanisme :
>
> - avec $\omega=0$, on obtient $d(0)=0$ et le minimum est en $x_0=3$, qui est interdit ;
> - avec $\omega=2$, on obtient $d(2)=3$ et le minimum est en $x_2=2$, encore interdit ;
> - avec $\omega=4$, on obtient $d(4)=4$ et le minimum est en $x_4=1$, qui est exactement la solution faisable ;
> - avec $\omega=6$, on obtient $d(6)=3$ et le minimum est en $x_6=0$ : la pénalité est devenue trop forte, donc la borne se dégrade.
>
> Pourquoi est-ce toujours une borne inférieure du coût optimal primal ? Pour tout $x$ faisable, $x-1\leq0$. Puisque $\omega\geq0$,
>
> $$\omega(x-1)\leq0\qquad\Longrightarrow\qquad L(x,\omega)\leq (x-3)^2=f(x).$$
>
> De plus, $d(\omega)$ est le minimum de $L(\cdot,\omega)$ sur **tous** les $x$, donc
>
> $$d(\omega)\leq L(x,\omega)\leq f(x)\qquad\text{pour tout }x\leq1.$$
>
> En particulier, $d(\omega)$ ne peut jamais dépasser le meilleur coût faisable, qui vaut ici $4$. Le problème dual consiste à choisir $\omega\geq0$ pour rendre cette borne aussi haute que possible :
>
> $$\max_{\omega\geq0}d(\omega)
> =\max_{\omega\geq0}\left(2\omega-\frac{\omega^2}{4}\right).$$
>
> Cette parabole atteint son maximum en $\omega^*=4$, avec $d(4)=4$. Le dual atteint donc exactement la valeur du primal : le multiplicateur $4$ est une pénalité juste assez forte pour rendre $x=1$ optimal.

### Cas convexe : quand les conditions deviennent suffisantes

Supposons $f$ convexe, les $g_i$ affines et les $h_j$ convexes. La **condition de Slater** demande un point qui respecte les égalités et rend toutes les inégalités strictes :

$$g_i(\bar x)=0,\qquad h_j(\bar x)<0,$$

avec $\bar x$ dans l'intérieur du domaine de $f$. Intuitivement, il doit exister une petite marge de manœuvre, pas seulement un ensemble faisable réduit à une frontière fragile.

> [!example] Slater = « il existe de la place », pas « la solution est à l'intérieur »
> Considère les contraintes
>
> $$x+y=1,\qquad x\geq0,\qquad y\geq0.$$
>
> L'égalité force les points faisables à rester sur la droite $x+y=1$. Elle doit donc être satisfaite exactement. En revanche, le point $\bar x=(\tfrac12,\tfrac12)$ vérifie strictement les deux inégalités : $x>0$ et $y>0$. Slater est satisfaite, car il existe de la marge **le long de la droite imposée par l'égalité**.
>
> À l'inverse, les contraintes $x\leq0$ et $x\geq0$ n'autorisent que $x=0$. Ce point touche les deux frontières : aucun $x$ ne peut rendre les deux inégalités strictes simultanément. Slater échoue. Cela n'implique pas qu'il n'y ait pas de solution ; cela signifie seulement que le théorème de dualité/KKT ne fournit plus automatiquement le même certificat.
>
> Enfin, Slater ne demande pas que le minimiseur ait une marge. Dans $\min (x-3)^2$ sous $x\leq1$, le point $0$ vérifie Slater, alors que le minimiseur est $x^*=1$, sur la frontière.

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

### Pourquoi la qualification MFCQ est demandée ?

Le théorème KKT affirme qu'un minimum local possède des multiplicateurs. Mais cette conclusion peut devenir fragile lorsque les contraintes sont redondantes ou se touchent sans laisser de marge. La qualification de **Mangasarian-Fromovitz** (MFCQ) exclut ces situations dégénérées.

Elle demande deux choses au point faisable $x^*$ :

1. Les gradients des égalités $\bigl(\nabla g_i(x^*)\bigr)_i$ sont linéairement indépendants. Chaque égalité apporte donc une vraie information. Par exemple, $x_1=0$ et $2x_1=0$ sont deux écritures de la même contrainte : leurs gradients ne sont pas indépendants.

   S'il n'y a qu'une seule égalité $g(x)=0$, cette condition devient simplement $\nabla g(x^*)\neq0$. En effet, une famille contenant un seul vecteur est linéairement indépendante si et seulement si ce vecteur n'est pas nul : si $a\nabla g(x^*)=0$ et $\nabla g(x^*)\neq0$, alors nécessairement $a=0$. À l'inverse, si le gradient est nul, prendre $a=1$ donne déjà une relation non triviale.

2. Il existe une direction $d$ qui reste tangentielle aux égalités et rentre strictement à l'intérieur de toutes les inégalités actives :

   $$\langle\nabla g_i(x^*),d\rangle=0\quad\forall i,\qquad
   \langle\nabla h_j(x^*),d\rangle<0\quad\text{pour tout }j\text{ tel que }h_j(x^*)=0.$$

La première égalité signifie qu'un petit déplacement dans la direction $d$ ne change pas les égalités au premier ordre. La seconde inégalité signifie qu'il fait diminuer chaque contrainte qui est exactement au bord : si $h_j(x^*)=0$, alors $h_j(x^*+\varepsilon d)$ devient négatif pour un petit $\varepsilon>0$. Les inégalités déjà strictes n'ont pas besoin d'être vérifiées ici : elles possèdent déjà une marge.

> [!example] Une situation saine, puis une situation dégénérée
> Avec $x_1=0$ et $x_2\leq0$ au point $(0,0)$, la direction $d=(0,-1)$ convient : elle préserve l'égalité et fait entrer strictement dans $x_2<0$.
>
> À l'inverse, avec $x_2=0$ et $x_2\leq0$, toute direction qui préserve l'égalité a forcément $d_2=0$. Elle ne peut donc pas faire décroître strictement l'inégalité active. Celle-ci est redondante et MFCQ échoue.

MFCQ ne dit pas qu'un problème sans cette propriété n'a pas de solution. Elle garantit seulement que le théorème fournit bien des multiplicateurs KKT à un minimum local. Dans le cas convexe avec Slater, KKT n'est plus seulement nécessaire : c'est un certificat d'optimalité globale.

> [!example] Sur une sphère
> Écrivons $x=(x_1,\ldots,x_N)\in\mathbb R^N$. La notation $x_N$ désigne sa dernière coordonnée. On cherche à maximiser
>
> $$q(x)=x_N^3-\frac12x_N^2$$
>
> parmi les vecteurs situés sur la sphère unité, c'est-à-dire vérifiant $\|x\|=1$. L'objectif ne dépend que de la dernière coordonnée ; posons donc $t=x_N$. La contrainte implique $t\in[-1,1]$, mais les autres coordonnées restent importantes pour compléter la norme lorsque $|t|<1$.
>
> KKT est formulé pour une minimisation. On minimise donc l'opposé
>
> $$f(x)=-q(x)=\frac12x_N^2-x_N^3.$$
>
> C'est le même problème : rendre $q$ aussi grand que possible revient exactement à rendre $-q$ aussi petit que possible. La contrainte est réécrite sous la forme attendue par KKT,
>
> $$g(x)=\|x\|^2-1=0.$$
>
> Elle est équivalente à $\|x\|-1=0$, mais elle est plus commode : $\nabla g(x)=2x$ est défini partout. Sur la sphère, $x\neq0$, donc $\nabla g(x)\neq0$. Pour une seule égalité, c'est précisément la condition de régularité demandée : la sphère a un vrai vecteur normal, et KKT peut s'appliquer.
>
> Le Lagrangien est
>
> $$L(x,\mu)=\frac12x_N^2-x_N^3+\mu(\|x\|^2-1).$$
>
> Sa stationnarité donne
>
> $$2\mu x_i=0\quad(i<N),\qquad x_N-3x_N^2+2\mu x_N=0,$$
>
> auxquels il faut ajouter la faisabilité $\|x\|=1$. Si $\mu\neq0$, les premières équations imposent $x_i=0$ pour $i<N$, puis la sphère donne $x_N=\pm1$. Si $\mu=0$, la dernière équation donne $x_N=0$ ou $x_N=\tfrac13$ ; pour ces deux valeurs, les autres coordonnées peuvent compléter la norme (car $N\geq2$).
>
> Il reste à comparer les valeurs de l'objectif original :
>
> $$q(-1)=-\frac32,\qquad q(0)=0,\qquad q\left(\frac13\right)=-\frac1{54},\qquad q(1)=\frac12.$$
>
> La plus grande est $q(1)=\tfrac12$. Avec $\|x\|=1$ et $x_N=1$, toutes les autres coordonnées doivent être nulles :
>
> $$\boxed{x^*=(0,\ldots,0,1).}$$
>
> Dans cet exemple non convexe, KKT fournit des candidats nécessaires, mais ne dit pas lequel est optimal : la comparaison finale des valeurs est indispensable.

## 10. Calculer une solution : méthodes du premier ordre (diapositives 16 à 21)

### Descente de gradient et gradient projeté

L'approximation locale

$$f(x)\simeq f(x_n)+\langle\nabla f(x_n),x-x_n\rangle$$

dit que $-\nabla f(x_n)$ est la direction où $f$ baisse le plus vite à court terme. La descente de gradient est donc

$$x_{n+1}=x_n-\gamma_n\nabla f(x_n),\qquad\gamma_n>0.$$

Si $x$ doit rester dans un convexe $C$, on corrige le pas par projection :

$$\boxed{x_{n+1}=x_n+\theta_n\bigl(P_C(x_n-\gamma_n\nabla f(x_n))-x_n\bigr),}$$

où $\gamma_n$ est le pas de descente et $\theta_n\in]0,1]$ une relaxation. La projection garantit la faisabilité ; la relaxation évite éventuellement de faire toute la correction d'un coup.

> [!example] Exemple pas à pas : un minimum bloqué par un intervalle
> On minimise
>
> $$f(x)=(x-3)^2\qquad\text{sur}\qquad C=[0,1].$$
>
> Sans contrainte, le minimum est $3$, car $f(3)=0$. Mais $3$ n'appartient pas à $[0,1]$ : le meilleur point autorisé sera donc le bord droit, $1$. Partons de $x_0=0$.
>
> **1. Calculer la direction de descente.** En dimension $1$, le gradient est la dérivée :
>
> $$f'(x)=2(x-3),\qquad f'(0)=-6.$$
>
> Le signe négatif dit qu'aller vers la droite fait baisser $f$. Avec $\gamma_0=\tfrac14$, la descente de gradient *sans contrainte* proposerait
>
> $$x_0-\gamma_0f'(x_0)=0-\tfrac14(-6)=1.5.$$
>
> Le choix $\gamma_0=\tfrac14$ est ici pédagogique : il rend le calcul lisible. Il n'est pas complètement arbitraire dans un algorithme réel : il doit être suffisamment petit pour stabiliser la suite. Ici, le gradient est $2$-Lipschitzien, donc tout pas constant dans $]0,1[$ convient ; $\tfrac14$ est donc un choix sûr.
>
> **2. Projeter sur l'ensemble autorisé.** Le candidat $1.5$ est interdit. La projection cherche le point de $[0,1]$ le plus proche de $1.5$ :
>
> $$P_{[0,1]}(1.5)=\operatorname*{argmin}_{z\in[0,1]}|z-1.5|^2=1.$$
>
> Géométriquement, $1.5$ est à droite de tout l'intervalle, donc l'extrémité droite est le point autorisé le plus proche. Plus généralement,
>
> $$P_{[a,b]}(u)=
> \begin{cases}
> a,&u<a,\\
> u,&a\leq u\leq b,\\
> b,&u>b.
> \end{cases}$$
>
> **3. Appliquer la relaxation.** La formule générale peut se lire comme « partir de $x_n$ et avancer d'une fraction $\theta_n$ vers le point projeté ». Ici, $x_0=0$, le point projeté est $1$ et $\theta_0=1$ :
>
> $$x_1=x_0+\theta_0(P_C(1.5)-x_0)=0+1(1-0)=1.$$
>
> Avec $\theta_0=\tfrac12$, on n'aurait parcouru que la moitié du trajet :
>
> $$x_1=0+\tfrac12(1-0)=0.5.$$
>
> Dans les deux cas, on reste dans $C$, car on prend un point entre deux points de l'intervalle. Enfin, au point optimal $x^*=1$, la dérivée vaut encore $f'(1)=-4$, et ce n'est pas un problème : aller vers la droite aiderait, mais est interdit. Pour tout $y\in[0,1]$, $y-1\leq0$, donc
>
> $$f'(1)(y-1)=(-4)(y-1)\geq0.$$
>
> Aucun déplacement **autorisé** ne diminue le coût : c'est bien le minimum contraint.

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

> [!tip] Lire les bornes sur les pas
> Les notations $\inf_n\gamma_n$ et $\sup_n\gamma_n$ désignent respectivement la plus grande borne inférieure et la plus petite borne supérieure de tous les pas $\gamma_n$. Pour appliquer le théorème, il faut pouvoir choisir deux constantes fixes telles que
>
> $$0<\gamma_{\min}\leq\gamma_n\leq\gamma_{\max}<\frac2\rho\qquad\text{pour tout }n.$$
>
> C'est exactement une autre manière d'écrire
>
> $$0<\inf_n\gamma_n\leq\sup_n\gamma_n<\frac2\rho.$$
>
> En clair, le pas de descente $\gamma_n$ ne doit jamais devenir presque nul (sinon l'algorithme peut pratiquement s'immobiliser), ni dépasser le seuil de stabilité $2/\rho$. Avec $\rho=2$, par exemple, la suite $0.2,0.7,0.2,0.7,\ldots$ convient : tous ses termes sont entre $0.2$ et $0.7<1$. En revanche, $\gamma_n=1/n$ ne satisfait pas l'hypothèse car ses pas se rapprochent de $0$.
>
> De la même façon,
>
> $$0<\theta_{\min}\leq\theta_n\leq1\qquad\text{pour tout }n$$
>
> équivaut à $0<\inf_n\theta_n\leq\sup_n\theta_n\leq1$. La relaxation $\theta_n$ indique quelle fraction de la correction projetée est appliquée : $\theta_n=1$ applique toute la correction, $\theta_n=\tfrac12$ la moitié. La borne inférieure positive évite que les corrections deviennent négligeables.

> [!warning] Ce que garantit réellement la convexité
> Sous **toutes** les hypothèses du théorème — $f$ convexe, $C$ convexe fermé non vide, gradient $\rho$-Lipschitzien, au moins un minimiseur existant et pas admissibles — le gradient projeté converge vers un minimiseur **global** sur $C$. La convexité est la raison essentielle : pour une fonction convexe, tout minimum local est déjà global.
>
> Sans convexité, l'algorithme peut encore décroître et, sous des conditions appropriées, converger vers un point stationnaire ; mais il peut s'agir d'un minimum local ou d'un point qui n'est pas un minimum. Atteindre le minimum global reste possible dans certains problèmes particuliers, mais n'est plus une garantie générale. La forte convexité ajoute l'unicité du minimum global et une vitesse de convergence linéaire.

> [!example] Exemple très simple : pourquoi $\rho=1$ et d'où vient la mise à jour
> Prends $C=\mathbb R$ et
>
> $$f(x)=\frac12x^2.$$
>
> Il n'y a pas de contrainte effective, donc la projection sur $C$ ne change rien. En dimension $1$, le gradient est simplement la dérivée :
>
> $$\nabla f(x)=f'(x)=x.$$
>
> Pour déterminer la constante de Lipschitz, on compare deux gradients :
>
> $$|\nabla f(x)-\nabla f(y)|=|x-y|=1\cdot|x-y|.$$
>
> La condition $\|\nabla f(x)-\nabla f(y)\|\leq\rho\|x-y\|$ est donc vraie avec $\rho=1$ ; c'est la plus petite constante possible. Autre raccourci en dimension $1$ : si $|f''(x)|\leq\rho$ partout, alors le gradient est $\rho$-Lipschitzien. Ici, $f''(x)=1$.
>
> La descente de gradient générale est
>
> $$x_{n+1}=x_n-\gamma\nabla f(x_n).$$
>
> Comme $\nabla f(x_n)=x_n$ dans cet exemple, on remplace simplement le gradient :
>
> $$x_{n+1}=x_n-\gamma x_n=(1-\gamma)x_n.$$
>
> Le seuil devient $2/\rho=2$. Si $x_0=8$ et $\gamma=\tfrac12$, alors
>
> $$8\longrightarrow4\longrightarrow2\longrightarrow1\longrightarrow\cdots.$$
>
> L'erreur par rapport au minimiseur $x^*=0$ est divisée par deux à chaque étape :
>
> $$|x_n-x^*|=\left(\frac12\right)^n|x_0-x^*|.$$
>
> C'est une convergence linéaire. À l'inverse, avec $\gamma=2$, la suite alterne $8,-8,8,\ldots$ ; avec $\gamma>2$, son amplitude augmente.

### Uzawa : descendre en $x$, monter en multiplicateurs

Pour chercher un point selle $L(x,\omega)$ avec des multiplicateurs d'inégalité, Uzawa alterne :

1. minimiser $L(\cdot,\omega_n)$ par rapport à $x$ pour obtenir $x_n$ ;
2. faire une ascension de gradient sur $\omega$, puis projeter sur l'orthant positif :

$$\omega_{n+1}=\omega_n+\theta_n\left(P_{\mathbb R_+^q}\!\bigl(\omega_n+\gamma_n\nabla_\omega L(x_n,\omega_n)\bigr)-\omega_n\right).$$

Le signe est ici positif parce que le dual se **maximise**. Les multiplicateurs des contraintes violées tendent ainsi à augmenter.

> [!example] Exemple détaillé - Uzawa sous la contrainte $x\leq1$
>
> Considérons
>
> $$\min_{x\in\mathbb R}\frac{1}{2}(x-2)^2\qquad\text{sous la contrainte}\qquad x\leq1.$$
>
> Sans contrainte, le minimum est $x=2$, qui dépasse la limite. Posons
>
> $$h(x)=x-1\leq0,\qquad L(x,\omega)=\frac{1}{2}(x-2)^2+\omega(x-1),\qquad\omega\geq0.$$
>
> On peut lire $\omega$ comme le **prix de la violation** : si $x>1$, le terme $\omega(x-1)$ ajoute un coût.
>
> Pour voir son effet, développons et complétons le carré :
>
> $$
> \begin{aligned}
> L(x,\omega)
> &=\frac{1}{2}x^2+(\omega-2)x+2-\omega\\
> &=\frac{1}{2}\bigl(x-(2-\omega)\bigr)^2+\omega-\frac{\omega^2}{2}.
> \end{aligned}
> $$
>
> Le dernier terme ne dépend pas de $x$. Pour un prix $\omega$ fixé,
>
> $$\boxed{x(\omega)=2-\omega.}$$
>
> Si $\omega$ augmente de $0{,}1$, alors $x(\omega+0{,}1)=x(\omega)-0{,}1$ : le fond du bol se déplace vers la région faisable.
>
> À la solution, KKT donne
>
> $$x^*=2-\omega^*,\qquad\omega^*(x^*-1)=0.$$
>
> $\omega^*=0$ donnerait $x^*=2$, qui n'est pas faisable. La contrainte est donc active :
>
> $$\boxed{\omega^*=1,\qquad x^*=1.}$$
>
> Cette solution vient d'une résolution directe des conditions KKT, en forme fermée. En général, on ne dispose pas d'une telle formule explicite : c'est pourquoi on introduit maintenant l'**algorithme d'Uzawa**, une méthode itérative qui retrouve ce même point $(x^*,\omega^*)$ sans résoudre KKT directement, et dont on peut ensuite étudier la vitesse de convergence.
>
> **Une itération Uzawa.** Ici $\nabla_\omega L(x,\omega)=h(x)=x-1$. Avec $\theta_n=1$,
>
> $$\boxed{\omega_{n+1}=\max\bigl(0,\omega_n+\gamma_n(x_n-1)\bigr),\qquad x_n=2-\omega_n.}$$
>
> 1. Calculer $x_n$ à partir de $\omega_n$.
> 2. Mesurer la violation $x_n-1$.
> 3. Augmenter ou diminuer $\omega_n$, puis le projeter sur $[0,+\infty[$.
>
> Avec $\gamma_n=\tfrac{1}{2}$ et $\omega_0=0$ :
>
> $$
> \begin{aligned}
> \omega_0=0&\Rightarrow x_0=2 &&\Rightarrow \omega_1=0{,}5,\\
> \omega_1=0{,}5&\Rightarrow x_1=1{,}5 &&\Rightarrow \omega_2=0{,}75,\\
> \omega_2=0{,}75&\Rightarrow x_2=1{,}25 &&\Rightarrow \omega_3=0{,}875.
> \end{aligned}
> $$
>
> Ainsi $x_n$ converge vers $1$. Avec un pas constant $\gamma$, tant que la projection est inactive, on part de la règle de mise à jour $\omega_{n+1}=\max(0,\omega_n+\gamma(x_n-1))$ et l'on y substitue $x_n=2-\omega_n$, donc $x_n-1=1-\omega_n$ :
>
> $$
> \begin{aligned}
> \omega_{n+1}
> &=\max\bigl(0,\ \omega_n+\gamma(1-\omega_n)\bigr)\\
> &=(1-\gamma)\omega_n+\gamma,\\
> \omega_{n+1}-1
> &=(1-\gamma)(\omega_n-1).
> \end{aligned}
> $$
>
> La relation $\omega_{n+1}-1=(1-\gamma)(\omega_n-1)$ fait de $(\omega_n-1)$ une suite géométrique de raison $(1-\gamma)$ :
>
> $$\omega_n-1=(1-\gamma)^n(\omega_0-1).$$
>
> Elle tend vers $0$ si et seulement si $|1-\gamma|<1$, c'est-à-dire $0<\gamma<2$. Donc la suite converge pour $0<\gamma<2$ :
>
> - si $0<\gamma<1$, la raison $1-\gamma\in(0,1)$ donne une convergence monotone ($\gamma=\tfrac{1}{2}$ divise l'erreur par deux à chaque itération) ;
> - si $\gamma=1$, la raison est nulle : $\gamma=1$ atteint ici la solution en une itération ;
> - si $1<\gamma<2$, la raison $1-\gamma\in(-1,0)$ fait osciller le signe de l'erreur, mais son amplitude diminue tout de même vers $0$ ;
> - si $\gamma\leq0$ ou $\gamma\geq2$, $|1-\gamma|\geq1$ et la suite ne converge plus.
>
> (Ce calcul suppose la projection $\max(0,\cdot)$ inactive, c'est-à-dire $\omega_n\geq0$ tout du long — vrai ici puisque $\omega_n\to1>0$.)

### Frank-Wolfe : éviter une projection difficile

Frank-Wolfe est une alternative au gradient projeté lorsque la projection sur l'ensemble de contraintes est difficile. L'idée est la suivante : plutôt que de faire un pas de gradient puis de le ramener dans $C$, on cherche directement une direction faisable qui paraît faire baisser le coût.

Près de $x_n$, la fonction est approchée par sa tangente :

$$f(z)\approx f(x_n)+\langle\nabla f(x_n),z-x_n\rangle.$$

Le terme $f(x_n)$ est fixe. Pour minimiser ce modèle linéaire sur $C$, on cherche donc

$$
\begin{aligned}
z_n
&\in\operatorname*{argmin}_{z\in C}\langle\nabla f(x_n),z-x_n\rangle\\
&=\operatorname*{argmin}_{z\in C}\langle\nabla f(x_n),z\rangle.
\end{aligned}
$$

La seconde ligne est équivalente à la première car $-\langle\nabla f(x_n),x_n\rangle$ ne dépend pas de $z$. Le produit scalaire ne calcule pas la future valeur exacte de $f$ : il compare les **pentes prédites** par la linéarisation. Un produit scalaire négatif avec un déplacement indique que ce déplacement est, au premier ordre, descendant.

On avance ensuite seulement sur le segment entre le point courant et ce point prometteur :

$$\boxed{x_{n+1}=(1-\gamma_n)x_n+\gamma_nz_n,\qquad0\leq\gamma_n\leq1.}$$

> [!example] Exemple sur le simplexe à deux coordonnées
> Prends $C=\{(x_1,x_2):x_1\geq0,\ x_2\geq0,\ x_1+x_2=1\}$, le segment entre $(1,0)$ et $(0,1)$. Supposons $x_n=(1,0)$ et $\nabla f(x_n)=(1.6,-1.6)$.
>
> Les deux sommets donnent
>
> $$\langle(1.6,-1.6),(1,0)\rangle=1.6,\qquad
> \langle(1.6,-1.6),(0,1)\rangle=-1.6.$$
>
> Frank-Wolfe choisit donc $z_n=(0,1)$. Le déplacement correspondant est $z_n-x_n=(-1,1)$ et
>
> $$\langle\nabla f(x_n),z_n-x_n\rangle=-3.2<0.$$
>
> Il est bien descendant. Avec $\gamma_n=\tfrac12$, on ne saute pas au sommet : on prend le milieu
>
> $$x_{n+1}=\tfrac12(1,0)+\tfrac12(0,1)=(0.5,0.5).$$

La faisabilité est automatique : si $x_n,z_n\in C$ et si $C$ est convexe, toute moyenne $(1-\gamma_n)x_n+\gamma_nz_n$ appartient encore à $C$.

Le choix $\gamma_n=\tfrac12$ dans l'exemple est seulement pédagogique. En pratique, on peut prendre le pas théorique $\gamma_n=2/(n+2)$, rechercher le meilleur pas sur le segment,

$$\gamma_n\in\operatorname*{argmin}_{0\leq\gamma\leq1}
f\bigl((1-\gamma)x_n+\gamma z_n\bigr),$$

ou employer une règle propre au problème. Un petit pas est prudent mais lent ; un grand pas avance plus vite, avec le risque que l'approximation linéaire soit moins fidèle.

#### Pourquoi cela évite une projection coûteuse ?

Le gradient projeté formerait d'abord le point libre $u_n=x_n-\eta_n\nabla f(x_n)$, puis calculerait

$$P_C(u_n)=\operatorname*{argmin}_{x\in C}\|x-u_n\|^2.$$

Cette **projection** renvoie un point de $C$ le plus proche de $u_n$. Elle ne doit pas être confondue avec un produit scalaire, qui ne renvoie qu'un nombre et mesure l'alignement de deux vecteurs. Le produit scalaire intervient d'ailleurs dans la projection sur une droite :

$$\operatorname{proj}_{v}(a)=\frac{\langle a,v\rangle}{\|v\|^2}v\qquad(v\ne0),$$

mais ce n'est pas, à lui seul, une projection.

Pour une boîte ou un simplexe, $P_C$ se calcule vite. En revanche, si $C=\{x:Ax\leq b\}$ comporte de nombreuses contraintes, calculer $P_C(u_n)$ demande de résoudre un problème quadratique contraint à chaque itération. Frank-Wolfe ne demande à la place que l'« oracle linéaire »

$$\operatorname*{argmin}_{z\in C}\langle\nabla f(x_n),z\rangle.$$

Sur un polytope décrit par ses sommets, cela revient souvent à choisir un sommet : c'est parfois beaucoup plus simple. Cet avantage dépend de la structure de $C$ ; il n'est pas automatique.

Si $C$ est compact convexe, $x_0\in C$ et $f$ a un gradient Lipschitzien, le choix $\gamma_n=2/(n+2)$ donne un écart d'objectif de l'ordre de $1/n$. Frank-Wolfe est donc souvent plus lent que le gradient projeté, mais peut être nettement moins coûteux par itération.

## 11. Utiliser la courbure : Newton, quasi-Newton et sous-espaces (diapositives 22 à 25)

La descente de gradient ne regarde que la **pente** : elle indique où descendre, mais ne sait pas si la vallée est très plate dans une direction et très raide dans une autre. Si $f$ est deux fois différentiable, Newton ajoute cette information de **courbure** grâce à la Hessienne.

Près du point courant $x_n$, il remplace $f$ par un modèle quadratique, c'est-à-dire un petit bol :

$$f(x)\simeq f(x_n)+\langle\nabla f(x_n),x-x_n\rangle
+\tfrac12\langle x-x_n,\nabla^2f(x_n)(x-x_n)\rangle.$$

Le gradient donne l'inclinaison du sol ; la Hessienne indique à quelle vitesse le sol se redresse. Minimiser ce modèle donne le pas de Newton

$$x_{n+1}=x_n-[\nabla^2f(x_n)]^{-1}\nabla f(x_n),$$

lorsque la Hessienne est définie positive. Elle garantit alors que le modèle local est bien un bol avec un fond, et non une selle ou une bosse.

### Exemple : un bol plat dans une direction, raide dans l'autre

Considère

$$f(x,y)=(x-2)^2+10(y-1)^2.$$

Son minimum est $(2,1)$. Le coefficient $10$ signifie que le bol est dix fois plus raide dans la direction $y$ que dans la direction $x$. Depuis $x_0=(0,0)$,

$$\nabla f(x_0)=\begin{pmatrix}-4\\-20\end{pmatrix},
\qquad
\nabla^2f(x_0)=\begin{pmatrix}2&0\\0&20\end{pmatrix}.$$

Une descente de gradient avec un unique pas $\gamma$ proposerait

$$x_1=(0,0)-\gamma(-4,-20)=(4\gamma,20\gamma).$$

Il faudrait donc un compromis : un pas assez petit pour ne pas trop dépasser dans la direction raide $y$, mais pas trop petit dans la direction plate $x$. Newton corrige chaque direction selon sa courbure : il fait un grand déplacement là où le bol est plat et un plus petit là où il est raide.

### En pratique, on résout un système, on ne calcule pas une inverse

L'écriture avec $[\nabla^2f(x_n)]^{-1}$ est compacte, mais on ne fabrique en général pas cette grande matrice inverse. On cherche plutôt le déplacement $p_n$ qui minimise le modèle quadratique :

$$x_{n+1}=x_n+p_n.$$

En posant $x=x_n+p$ dans le modèle, on obtient

$$q_n(p)=f(x_n)+\langle\nabla f(x_n),p\rangle
+\frac12\langle p,\nabla^2f(x_n)p\rangle.$$

Au minimum de $q_n$, son gradient par rapport à $p$ doit être nul :

$$\nabla_p q_n(p_n)=\nabla f(x_n)+\nabla^2f(x_n)p_n=0.$$

D'où le système linéaire fondamental de Newton :

$$\boxed{\nabla^2f(x_n)p_n=-\nabla f(x_n).}$$

Dans l'exemple, ce système est

$$
\begin{pmatrix}2&0\\0&20\end{pmatrix}
\begin{pmatrix}p_1\\p_2\end{pmatrix}
=\begin{pmatrix}4\\20\end{pmatrix}.
$$

Il revient à résoudre $2p_1=4$ et $20p_2=20$, donc $p_n=(2,1)$ et

$$x_1=x_0+p_n=(0,0)+(2,1)=(2,1).$$

Newton atteint le minimum en une itération ici parce que $f$ est exactement quadratique : son modèle local est la fonction elle-même. En grande dimension, le système est résolu par un algorithme linéaire adapté, ce qui est moins coûteux et plus stable que de calculer l'inverse complète de la Hessienne.

> [!example] Lecture en une dimension
> Pour $q(p)=a+bp+\tfrac12cp^2$, le minimum satisfait $q'(p)=b+cp=0$, soit $cp=-b$. L'équation de Newton est exactement la même relation : le coefficient $c$ devient la Hessienne et $b$ le gradient.

### Pourquoi Newton devient très rapide près du minimum

Notons $x^*$ le minimum et $e_n=\|x_n-x^*\|$ l'erreur. Quand on est déjà proche de $x^*$, le vrai paysage ressemble très bien au bol quadratique utilisé par Newton. Il reste une petite différence, mais elle est d'ordre trois dans le développement de Taylor ; après minimisation du modèle, elle donne une nouvelle erreur d'ordre deux :

$$e_{n+1}\leq C e_n^2.$$

Le carré est décisif. Si $e_n=0.1$ et, pour l'image, $C\simeq1$, les erreurs deviennent

$$0.1\longmapsto0.01\longmapsto0.0001\longmapsto0.00000001.$$

C'est la **convergence quadratique**. Pour comparaison, une descente de gradient bien réglée a souvent une convergence linéaire $e_{n+1}\simeq\rho e_n$ avec $0<\rho<1$ : avec $\rho=1/2$, on obtient $0.1\mapsto0.05\mapsto0.025\mapsto0.0125$. Newton est donc spectaculaire près du fond du bon bol, mais son modèle peut être trompeur loin de ce fond.

### Newton amorti ou régularisé : une ceinture de sécurité

Pour stabiliser la méthode loin du minimum ou face à une Hessienne mal conditionnée, on utilise souvent

$$x_{n+1}=x_n-\gamma_n[\nabla^2f(x_n)+\eta_nI]^{-1}\nabla f(x_n),$$

avec $\gamma_n\in]0,1]$ et $\eta_n>0$. Le facteur $\gamma_n$ raccourcit un pas trop ambitieux ; le terme $\eta_nI$ ajoute de la courbure positive, ce qui rend l'inversion plus sûre. Près du minimum, on peut relâcher ces précautions et retrouver l'accélération de Newton.

### Quasi-Newton : apprendre la courbure

Calculer la Hessienne peut être coûteux. Les méthodes **quasi-Newton** construisent donc, à partir des déplacements et des changements de gradient observés, une approximation définie positive $H_n^{-1}$ de son inverse :

$$x_{n+1}=x_n-H_n^{-1}\nabla f(x_n).$$

Elles apprennent ainsi quelles directions sont plutôt plates ou raides sans stocker une Hessienne dense. BFGS et L-BFGS sont les méthodes classiques de cette famille ; L-BFGS ne conserve que quelques informations récentes.

### Sous-espaces : mélanger quelques directions utiles

Enfin, les méthodes à **sous-espace** ne cherchent pas dans toutes les directions possibles. Elles combinent par exemple la direction de gradient et le déplacement précédent $d_n=x_n-x_{n-1}$ :

$$x_{n+1}=x_n-\gamma_n^{(1)}\nabla f(x_n)+\gamma_n^{(2)}d_n,$$

en choisissant les deux coefficients $\gamma_n^{(1)}$ et $\gamma_n^{(2)}$ exactement ou avec un modèle quadratique. L'idée est de tirer parti de l'élan des derniers pas sans devoir explorer tout l'espace.

> [!summary] À retenir
> - **Gradient** : suit seulement la pente.
> - **Newton** : utilise pente et courbure exacte.
> - **Quasi-Newton** : apprend une approximation de la courbure.
> - **Sous-espace** : choisit un bon mélange de quelques directions mémorisées.

## Fil de résolution - version complète

Pour un problème nouveau, suis l'ordre suivant :

1. Identifie $f$, le domaine et le type de contraintes : ensemble $C$, égalités $g_i=0$ ou inégalités $h_j\leq0$.
2. Établis existence et unicité avant de lancer un calcul, lorsque les hypothèses le permettent.
3. Si les projections sont simples, utilise gradient projeté ; si l'objectif manque, POCS traite la faisabilité.
4. Si les contraintes sont différentiables, écris le Lagrangien et les conditions KKT. Vérifie Slater ou une qualification avant de les invoquer comme certificat.
5. Choisis l'algorithme selon le coût dominant : gradient pour la simplicité, Frank-Wolfe si la projection est difficile, Newton ou quasi-Newton si la courbure est accessible.

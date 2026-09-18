---
title: Optimisation convexe - exercices corrigés
aliases:
  - Exercices corrigés d'optimisation convexe
tags:
  - mva
  - optimisation-convexe
  - exercices
sources:
  - "[[prerentree/optimisation-convexe/sources/cours-optimisation-convexe-1.pdf|Optimization Reminders - Part I]]"
  - "[[prerentree/optimisation-convexe/sources/cours-optimisation-convexe-2.pdf|Optimization Reminders - Part II]]"
---

# Optimisation convexe - exercices corrigés

Cette note rassemble les exercices des deux parties du cours. Les corrections des exercices 2 et 3 de la partie I, ainsi que celle de l'exercice 3 de la partie II, suivent les pistes présentes dans les annotations manuscrites des PDF ; les calculs sont rédigés ici de façon complète. Pour les définitions et les théorèmes utilisés, voir [[prerentree/optimisation-convexe/notes/cours-principal|le cours principal]].

> [!tip] Méthode de travail
> Essaie d'abord d'identifier le théorème pertinent (compacité, convexité, KKT, gradient projeté, etc.). Lis ensuite la correction en vérifiant que chaque hypothèse est utilisée : c'est plus utile que de mémoriser les formules finales.

# Partie I - bases de l'optimisation convexe

## Exercice 1 - prolonger une fonction au bord du simplexe

Soit $\varphi:]0,+\infty[\to\mathbb R$ continue, avec $\lim_{\xi\downarrow0}\varphi(\xi)=0$. La fonction initiale est la somme $\sum_i\varphi(x_i)$ lorsque toutes les coordonnées sont strictement positives, et vaut $+\infty$ dès qu'une coordonnée est négative.

1. La prolonger pour qu'elle soit semi-continue inférieurement sur $\mathbb R^N$.
2. Étudier l'existence d'un minimiseur sur le simplexe

$$\Delta_N=\left\{x\in[0,+\infty[^N:\sum_{i=1}^N x_i=1\right\}.$$

### Correction

Le seul point manquant dans la définition est le bord $x_i=0$. La limite imposée suggère la prolongation naturelle

$$
\bar\varphi(t)=
\begin{cases}
\varphi(t),&t>0,\\
0,&t=0,\\
+\infty,&t<0.
\end{cases}
\qquad\text{et}\qquad
\bar f(x)=\sum_{i=1}^N\bar\varphi(x_i).
$$

Sur $[0,+\infty[$, $\bar\varphi$ est continue, y compris en $0$, grâce à l'hypothèse de limite. Lorsqu'on approche un nombre négatif depuis n'importe où, la valeur $+\infty$ ne crée pas une chute interdite ; $\bar\varphi$ est donc s.c.i. sur $\mathbb R$. Une somme finie de fonctions s.c.i. est s.c.i., donc $\bar f$ l'est aussi.

Le simplexe $\Delta_N$ est non vide, fermé et borné ; il est donc compact dans $\mathbb R^N$. La restriction de $\bar f$ à ce compact est propre et s.c.i. Par Weierstrass, elle atteint son minimum :

$$\operatorname{Argmin}_{x\in\Delta_N}\bar f(x)\ne\varnothing.$$

On ne peut pas conclure à l'unicité sans une hypothèse supplémentaire, par exemple la stricte convexité de $\varphi$. L'intérêt du prolongement est précisément de ne pas perdre les solutions qui vivent sur le bord du simplexe.

## Exercice 2 - norme au carré et $\beta$-convexité

Soit $H$ un espace de Hilbert.

1. Montrer que $x\mapsto\|x\|^2$ est strictement convexe.
2. Une fonction $f$ est $\beta$-convexe si $g(x)=f(x)-\tfrac\beta2\|x\|^2$ est convexe. Montrer que toute fonction fortement convexe ($\beta>0$) est strictement convexe.
3. Montrer que $f$ est $\beta$-convexe si et seulement si, pour $x,y\in\operatorname{dom}f$ et $\alpha\in[0,1]$,

$$f(\alpha x+(1-\alpha)y)+\frac{\beta}{2}\alpha(1-\alpha)\|x-y\|^2
\leq \alpha f(x)+(1-\alpha)f(y).$$

### Correction

Les annotations manuscrites partent de l'identité fondamentale

$$\|\alpha x+(1-\alpha)y\|^2
=\alpha\|x\|^2+(1-\alpha)\|y\|^2-\alpha(1-\alpha)\|x-y\|^2.\tag{1}$$

**1.** Si $x\ne y$ et $0<\alpha<1$, le dernier terme de (1) est strictement négatif. Ainsi,

$$\|\alpha x+(1-\alpha)y\|^2
<\alpha\|x\|^2+(1-\alpha)\|y\|^2,$$

ce qui est exactement la stricte convexité. En dimension finie, on peut aussi reconnaître que la Hessienne est $2I\succ0$, mais l'identité fonctionne dans tout espace de Hilbert.

**2.** Écrivons $f=g+\tfrac\beta2\|\cdot\|^2$, avec $g$ convexe. En combinant la convexité de $g$ et (1), on obtient

$$f(\alpha x+(1-\alpha)y)
\leq\alpha f(x)+(1-\alpha)f(y)
-\frac\beta2\alpha(1-\alpha)\|x-y\|^2.$$

Pour $\beta>0$, $x\ne y$ et $0<\alpha<1$, le terme soustrait est strictement positif : l'inégalité de convexité est stricte. Donc $f$ est strictement convexe.

**3.** La ligne précédente prouve le sens direct. Réciproquement, si l'inégalité demandée est vraie, on soustrait $\tfrac\beta2\|\alpha x+(1-\alpha)y\|^2$ aux deux membres et l'on utilise (1). On retrouve

$$g(\alpha x+(1-\alpha)y)
\leq\alpha g(x)+(1-\alpha)g(y).$$

Autrement dit $g$ est convexe, donc $f$ est $\beta$-convexe. Cette preuve est celle esquissée dans les pages manuscrites : ramener systématiquement la question à la convexité de $g=f-\tfrac\beta2\|\cdot\|^2$.

## Exercice 3 - caractérisation différentielle de la $\beta$-convexité

Soit $f:H\to\mathbb R$ différentiable. Montrer que $f$ est $\beta$-convexe si et seulement si

$$\boxed{f(y)\geq f(x)+\langle\nabla f(x),y-x\rangle
+\frac\beta2\|y-x\|^2\quad\forall x,y\in H.}$$

### Correction

Les notes manuscrites indiquent de nouveau de poser

$$g(x)=f(x)-\frac\beta2\|x\|^2.$$

Par définition, $f$ est $\beta$-convexe exactement lorsque $g$ est convexe. Or, pour une fonction différentiable, la convexité équivaut à l'inégalité de la tangente :

$$g(y)\geq g(x)+\langle\nabla g(x),y-x\rangle.$$

Comme $\nabla g(x)=\nabla f(x)-\beta x$, cette inégalité devient

$$f(y)-\frac\beta2\|y\|^2
\geq f(x)-\frac\beta2\|x\|^2
+\langle\nabla f(x)-\beta x,y-x\rangle.$$

Il suffit de rassembler les termes quadratiques. L'identité

$$\|y\|^2-\|x\|^2-2\langle x,y-x\rangle=\|y-x\|^2$$

donne exactement l'encadré. Toutes les étapes sont réversibles, donc on a bien une équivalence.

> [!tip] Lecture géométrique
> Pour $\beta>0$, la courbe ne reste pas seulement au-dessus de sa tangente : elle reste au-dessus avec une marge quadratique. Cette marge est ce qui stabilise l'unicité et accélère plusieurs algorithmes.

## Exercice 4 - log-sum-exp

Montrer que

$$F(x)=\log\left(\sum_{i=1}^N e^{x_i}\right)$$

est convexe sur $\mathbb R^N$, et déterminer si elle est strictement convexe.

### Correction

Posons

$$p_i(x)=\frac{e^{x_i}}{\sum_{j=1}^N e^{x_j}}.$$

Les $p_i$ sont positifs et leur somme vaut $1$ : c'est une distribution de probabilité. On trouve

$$\nabla F(x)=p(x),\qquad
\nabla^2F(x)=\operatorname{diag}(p)-pp^\top.$$

Pour tout vecteur $v$,

$$v^\top\nabla^2F(x)v
=\sum_i p_i v_i^2-\left(\sum_i p_i v_i\right)^2
=\operatorname{Var}_{p}(v_i)\geq0.$$

La Hessienne est donc positive semi-définie : $F$ est convexe. Elle n'est pas strictement convexe lorsque $N\geq2$, car pour $\mathbf 1=(1,\ldots,1)$,

$$F(x+t\mathbf 1)=t+F(x).$$

Le long de cette direction, la fonction est affine. Autrement dit, $\nabla^2F(x)\mathbf 1=0$.

## Auto-vérification - trois questions courtes

1. Sur $C=[-1,2]$, minimiser $f(x)=(x-4)^2$.
   - **Réponse :** $x^*=2$. On a $f'(2)=-4\ne0$, ce qui est normal au bord. Pour tout $y\in C$, $-4(y-2)\geq0$.
2. $f(x)=|x|$ est-elle strictement convexe ?
   - **Réponse :** non : entre $1$ et $2$, son graphe est un segment. Elle a néanmoins un unique minimiseur, $0$ ; la stricte convexité est suffisante pour l'unicité, pas nécessaire.
3. Pourquoi $f(x)=e^x$ n'a-t-elle pas de minimiseur sur $\mathbb R$ ?
   - **Réponse :** $e^x>0$ pour tout $x$, mais $e^x\to0$ lorsque $x\to-\infty$. Son infimum est $0$, non atteint.

## Exercice 5 - projection après une transformation orthogonale

Soit $D\subset\mathbb R^N$ non vide, fermé et convexe, $L\in\mathbb R^{N\times N}$ orthogonale, et

$$C=\{x\in\mathbb R^N:Lx\in D\}.$$

1. Montrer que la projection sur $C$ est bien définie.
2. Montrer que $P_C=L^\top P_D L$.
3. Donner l'expression quand $D=[0,+\infty[^N$.

### Correction

**1.** Comme $L$ est continue, $C=L^{-1}(D)$ est fermé. Il est non vide car $L$ est bijective. Enfin, l'image réciproque d'un convexe par une application linéaire est convexe. Ainsi $C$ est non vide, fermé et convexe : le théorème de projection garantit que $P_C$ est bien définie et univoque.

**2.** Pour projeter $x$ sur $C$, écrivons $z=Lx$. Tout $u\in C$ s'écrit $u=L^\top v$ avec $v\in D$. Puisque $L$ est orthogonale, elle préserve les normes :

$$\|u-x\|=\|L^\top v-x\|=\|v-Lx\|=\|v-z\|.$$

Minimiser à gauche sur $u\in C$ revient donc à minimiser à droite sur $v\in D$. Le minimiseur est $v^*=P_D(Lx)$, d'où

$$\boxed{P_C(x)=L^\top P_D(Lx).}$$

**3.** La projection sur l'orthant positif remplace chaque coordonnée négative par zéro. Ainsi,

$$P_C(x)=L^\top\bigl(\max((Lx)_1,0),\ldots,\max((Lx)_N,0)\bigr).$$

# Partie II - faisabilité, dualité et algorithmes

## Exercice 1 - projection sur une boule et POCS

Soit $H$ un espace de Hilbert réel.

1. Donner la projection sur la boule fermée $B(c,r)$ de centre $c$ et de rayon $r>0$.
2. Trois boules fermées ont un point commun. Proposer un algorithme qui calcule un tel point.

### Correction

**1.** Si $x$ est déjà dans la boule, la projection ne le modifie pas. Sinon, le point projeté est sur le rayon allant de $c$ vers $x$, à distance $r$ de $c$ :

$$\boxed{P_{B(c,r)}(x)=c+\min\left(1,\frac r{\|x-c\|}\right)(x-c).}$$

La formule couvre aussi le cas $x=c$, car le minimum vaut alors $1$ par continuité de l'expression géométrique (ou l'on traite séparément $P_{B(c,r)}(c)=c$).

**2.** Avec $B_1,B_2,B_3$ les trois boules, choisir un point initial $x_0$ puis appliquer POCS :

$$x_{n+1}=P_{B_{1+(n\bmod3)}}(x_n).$$

Par exemple, on projette successivement sur $B_1$, $B_2$, $B_3$, puis on recommence. Les boules sont fermées et convexes et leur intersection est non vide ; le théorème POCS assure la convergence vers un point de $B_1\cap B_2\cap B_3$. Une version relaxée remplace la mise à jour par

$$x_{n+1}=x_n+\theta_n\bigl(P_{B_{1+(n\bmod3)}}(x_n)-x_n\bigr),$$

avec $\theta_n\in[\varepsilon_1,2-\varepsilon_2]$, où $\varepsilon_1,\varepsilon_2>0$ et $\varepsilon_1+\varepsilon_2<2$.

## Exercice 2 - minimiser une somme d'exponentielles sur le simplexe

Pour $N>1$, minimiser

$$f(x)=\sum_{i=1}^N e^{x_i}$$

sous les contraintes $\sum_i x_i=1$ et $x_i\geq0$ pour tout $i$.

1. Discuter existence et unicité.
2. Appliquer la méthode des multiplicateurs de Lagrange.

### Correction

L'ensemble faisable est le simplexe $\Delta_N$. Il est non vide, fermé et borné, donc compact. Comme $f$ est continue, le minimum existe. Sa Hessienne vaut

$$\nabla^2f(x)=\operatorname{diag}(e^{x_1},\ldots,e^{x_N})\succ0,$$

donc $f$ est strictement convexe. Il y a donc un **unique** minimiseur sur le convexe $\Delta_N$.

Pour les contraintes $-x_i\leq0$, écrivons

$$L(x,\mu,\nu)=\sum_{i=1}^Ne^{x_i}+\mu\left(\sum_{i=1}^N x_i-1\right)-\sum_{i=1}^N\nu_i x_i,
\qquad \nu_i\geq0.$$

Les KKT sont

$$e^{x_i}+\mu-\nu_i=0,\qquad \nu_i x_i=0,\qquad x_i\geq0,\qquad\sum_i x_i=1.$$

Le candidat symétrique $x_i^*=1/N$ est strictement intérieur ; il vérifie aussi la condition de Slater. La complémentarité impose alors $\nu_i^*=0$, et la stationnarité devient

$$e^{1/N}+\mu^*=0.$$

Ainsi $\mu^*=-e^{1/N}$ et les KKT sont satisfaites. Slater rend ces conditions suffisantes, et la stricte convexité assure l'unicité :

$$\boxed{x^*=\left(\frac1N,\ldots,\frac1N\right).}$$

On peut aussi reconnaître l'inégalité de Jensen : la moyenne des $e^{x_i}$ est au moins $e^{\text{moyenne des }x_i}=e^{1/N}$, avec égalité seulement quand toutes les coordonnées sont égales.

## Exercice 3 - moindres carrés régularisés

Soient $H,G$ deux espaces de Hilbert réels, $L\in\mathcal B(H,G)$, $y\in G$ et $\alpha>0$. On veut minimiser

$$f(x)=\frac12\|Lx-y\|^2+\frac\alpha2\|x\|^2.$$

1. Donner la descente de gradient adaptée.
2. Écrire la méthode de Newton.
3. Lorsque $H=\mathbb R^N$, étudier la convergence de la descente de gradient par diagonalisation de $L^\top L$.

### Correction

Les premières lignes de la correction manuscrite observent que le terme $\tfrac\alpha2\|x\|^2$ rend $f$ fortement convexe et coercive. Par conséquent, $f$ admet un unique minimiseur $x^*$.

La règle de dérivation de la composition avec $L$ donne

$$\nabla f(x)=L^*(Lx-y)+\alpha x
=(L^*L+\alpha I)x-L^*y,$$

et la Hessienne constante est

$$\nabla^2f=L^*L+\alpha I=:A\succeq\alpha I\succ0.$$

**1. Descente de gradient.** Comme dans les notes manuscrites,

$$\boxed{x_{n+1}=x_n-\gamma_n\bigl[L^*(Lx_n-y)+\alpha x_n\bigr].}$$

Le gradient est Lipschitzien de constante

$$B=\|A\|=\|L^*L+\alpha I\|=\|L\|^2+\alpha.$$

Ainsi, par exemple, des pas vérifiant $0<\inf_n\gamma_n\leq\sup_n\gamma_n<2/B$ assurent la convergence vers l'unique minimiseur. Avec un pas constant $\gamma\in]0,2/B[$, la convergence est linéaire.

**2. Newton.** La correction manuscrite exploite que $A$ est inversible :

$$
\begin{aligned}
x_{n+1}
&=x_n-A^{-1}\nabla f(x_n)\\
&=x_n-A^{-1}(Ax_n-L^*y)\\
&=A^{-1}L^*y.
\end{aligned}
$$

Newton atteint donc immédiatement le minimiseur,

$$\boxed{x^*=(L^*L+\alpha I)^{-1}L^*y,}$$

en une itération, quel que soit $x_0$. Ce raccourci est propre aux objectifs quadratiques à Hessienne constante.

**3. Diagonalisation.** En dimension finie, écrire

$$L^\top L=Q\operatorname{diag}(\lambda_1,\ldots,\lambda_N)Q^\top,
\qquad \lambda_i\geq0,$$

où $Q$ est orthogonale. Pour l'erreur $e_n=x_n-x^*$, la descente à pas constant satisfait

$$e_{n+1}=(I-\gamma(L^\top L+\alpha I))e_n.$$

Dans la base des vecteurs propres, $\tilde e_n=Q^\top e_n$, chaque coordonnée évolue indépendamment :

$$\tilde e_{n+1,i}=\bigl(1-\gamma(\lambda_i+\alpha)\bigr)\tilde e_{n,i}.$$

Elle tend vers zéro si et seulement si $|1-\gamma(\lambda_i+\alpha)|<1$ pour tout $i$, soit

$$\boxed{0<\gamma<\frac2{\lambda_{\max}+\alpha}.}$$

Le facteur de convergence est $\max_i|1-\gamma(\lambda_i+\alpha)|$. Le pas constant le plus favorable est

$$\gamma_{\mathrm{opt}}=\frac2{\lambda_{\min}+\lambda_{\max}+2\alpha},$$

et son facteur vaut

$$\frac{\lambda_{\max}-\lambda_{\min}}
{\lambda_{\max}+\lambda_{\min}+2\alpha}<1.$$

La régularisation $\alpha$ augmente toutes les valeurs propres : elle rend le problème mieux conditionné et la descente plus stable.

## Checklist avant de valider une correction

- Pour une conclusion d'existence : ai-je montré compacité ou coercivité, ainsi que la semi-continuité/continuité ?
- Pour une conclusion d'unicité : ai-je identifié la stricte ou forte convexité ?
- Pour KKT : ai-je écrit faisabilité, signe des multiplicateurs, complémentarité et stationnarité ?
- Pour un algorithme : ai-je donné la mise à jour, les conditions sur le pas et ce vers quoi la suite converge ?

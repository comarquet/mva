---
title: Statistiques - Correction du TD de pré-rentrée
aliases:
  - Correction du TD de statistiques
  - TD de statistiques corrigé
tags:
  - mva
  - prerentree
  - statistiques
  - exercices
  - corrections
date: 2026-09-22
sources:
  - "[[../sources/td-stats.pdf|TD de statistiques annoté]]"
course: "[[cours-principal]]"
---

# Statistiques - Correction du TD de pré-rentrée

Cours associé : [[cours-principal]].

> [!abstract] Périmètre de la note
> Cette note reprend seulement les exercices qui comportent une correction manuscrite dans le [[../sources/td-stats.pdf|TD annoté]] : les exercices 1 à 4, puis les questions 1(a), 1(b) et 1(c) de l'exercice 5. Les questions sans trace de correction manuscrite sont volontairement laissées de côté.

## Sommaire

- [[#Exercice 1 - Fréquences empiriques et données catégorielles|1. Fréquences empiriques]]
- [[#Exercice 2 - Divergence de Bregman, KL et entropie croisée|2. Divergence de Bregman et KL]]
- [[#Exercice 3 - ACP|3. ACP]]
- [[#Exercice 4 - Méthode des moments et maximum de vraisemblance|4. Moments contre vraisemblance]]
- [[#Exercice 5 - Estimateurs du maximum de vraisemblance|5. Estimateur de Bernoulli]]

---

## Exercice 1 - Fréquences empiriques et données catégorielles

On observe $X_1,\ldots,X_n$ i.i.d. à valeurs dans $\{1,\ldots,K\}$, avec $\mathbb P(X_i=k)=p_k$. Pour chaque observation,

$$
Z_i=\bigl(\mathbf 1_{\{X_i=1\}},\ldots,\mathbf 1_{\{X_i=K\}}\bigr)^\top,
\qquad
\widehat p=\frac1n\sum_{i=1}^n Z_i.
$$

### 1. Interpréter $\widehat p_k$ et montrer que $\widehat p$ est un vecteur de probabilités

> [!question] Question
> Interpréter $\widehat p_k$. Montrer que $\widehat p$ est un vecteur de probabilités.

La coordonnée $k$ vaut

$$
\widehat p_k
=\frac1n\sum_{i=1}^n\mathbf 1_{\{X_i=k\}}.
$$

Chaque indicatrice vaut $1$ quand l'observation est dans la catégorie $k$, et $0$ sinon. Leur somme compte donc les observations de catégorie $k$ ; après division par $n$, $\widehat p_k$ est leur **proportion observée**.

Toutes les coordonnées sont positives ou nulles. De plus,

$$
\sum_{k=1}^K\widehat p_k
=\frac1n\sum_{i=1}^n\sum_{k=1}^K\mathbf 1_{\{X_i=k\}}
$$

Regardons la somme intérieure pour une observation fixée $X_i$. Puisque $X_i$ prend une unique valeur dans $\{1,\ldots,K\}$, il existe une unique catégorie, notée $k_i$, telle que $X_i=k_i$. On a donc

$$
\mathbf 1_{\{X_i=1\}}+\cdots+\mathbf 1_{\{X_i=k_i\}}+\cdots+\mathbf 1_{\{X_i=K\}}
=0+\cdots+1+\cdots+0
=1.
$$

Autrement dit,

$$
\sum_{k=1}^K\mathbf 1_{\{X_i=k\}}=1.
$$

Cette égalité est vraie séparément pour chaque $i$. On peut donc la remplacer dans la somme extérieure :

$$
\frac1n\sum_{i=1}^n\sum_{k=1}^K\mathbf 1_{\{X_i=k\}}
=\frac1n\sum_{i=1}^n 1
=1.
$$

Ainsi $\widehat p$ est bien un vecteur de probabilités.

### 2. Espérance, variance et covariance des fréquences

> [!question] Question
> Calculer $\mathbb E[\widehat p_k]$, $\operatorname{Var}(\widehat p_k)$ et, si $k\ne\ell$, $\operatorname{Cov}(\widehat p_k,\widehat p_\ell)$. Interpréter le signe de cette covariance.

Le nombre d'observations dans la catégorie $k$ est

$$
n\widehat p_k=\sum_{i=1}^n\mathbf 1_{\{X_i=k\}}\sim\operatorname{Bin}(n,p_k).
$$

Pour une loi binomiale $B\sim\operatorname{Bin}(n,p_k)$, on connaît les deux moments

$$
\mathbb E[B]=np_k,
\qquad
\operatorname{Var}(B)=np_k(1-p_k).
$$

Or $\widehat p_k=B/n$. Diviser une variable aléatoire par $n$ divise son espérance par $n$ et sa variance par $n^2$. Donc

$$
\begin{aligned}
\mathbb E[\widehat p_k]
&=\mathbb E\left[\frac Bn\right]
=\frac1n\mathbb E[B]
=\frac1n(np_k)
=p_k,\\
\operatorname{Var}(\widehat p_k)
&=\operatorname{Var}\left(\frac Bn\right)
=\frac1{n^2}\operatorname{Var}(B)
=\frac1{n^2}np_k(1-p_k)
=\frac{p_k(1-p_k)}n.
\end{aligned}
$$

Pour $k\ne\ell$, une même observation ne peut pas être à la fois dans les catégories $k$ et $\ell$. Ainsi,

$$
\mathbf 1_{\{X_i=k\}}\mathbf 1_{\{X_i=\ell\}}=0.
$$

En appliquant la définition de la covariance,

$$
\begin{aligned}
\operatorname{Cov}\bigl(\mathbf 1_{\{X_i=k\}},\mathbf 1_{\{X_i=\ell\}}\bigr)
&=\mathbb E\bigl[\mathbf 1_{\{X_i=k\}}\mathbf 1_{\{X_i=\ell\}}\bigr]
-\mathbb E[\mathbf 1_{\{X_i=k\}}]\mathbb E[\mathbf 1_{\{X_i=\ell\}}]\\
&=0-p_kp_\ell\\
&=-p_kp_\ell.
\end{aligned}
$$

Pour passer des indicatrices aux fréquences, repartons de leurs définitions :

$$
\widehat p_k=\frac1n\sum_{i=1}^n\mathbf 1_{\{X_i=k\}},
\qquad
\widehat p_\ell=\frac1n\sum_{j=1}^n\mathbf 1_{\{X_j=\ell\}}.
$$

On utilise deux propriétés générales de la covariance : les constantes se mettent en facteur, et la covariance est linéaire dans chacune de ses deux entrées. Pour des variables $A_i$ et $B_j$,

$$
\operatorname{Cov}\left(\sum_iA_i,\sum_jB_j\right)
=\sum_i\sum_j\operatorname{Cov}(A_i,B_j).
$$

Cette dernière égalité vient directement de la définition $\operatorname{Cov}(A,B)=\mathbb E[AB]-\mathbb E[A]\mathbb E[B]$ et de la linéarité de l'espérance. En remplaçant $A_i$ et $B_j$ par les indicatrices ci-dessus, on obtient donc

$$
\begin{aligned}
\operatorname{Cov}(\widehat p_k,\widehat p_\ell)
&=\operatorname{Cov}\left(
\frac1n\sum_{i=1}^n\mathbf 1_{\{X_i=k\}},
\frac1n\sum_{j=1}^n\mathbf 1_{\{X_j=\ell\}}
\right)\\
&=\frac1{n^2}\sum_{i=1}^n\sum_{j=1}^n
\operatorname{Cov}\bigl(\mathbf 1_{\{X_i=k\}},\mathbf 1_{\{X_j=\ell\}}\bigr).
\end{aligned}
$$

Si $i\ne j$, les deux indicatrices concernent deux observations indépendantes, donc leur covariance est nulle. Il ne reste que les $n$ termes diagonaux $i=j$, tous égaux à $-p_kp_\ell$. Ainsi,

$$
\begin{aligned}
\operatorname{Cov}(\widehat p_k,\widehat p_\ell)
&=\frac1{n^2}\sum_{i=1}^n(-p_kp_\ell)\\
&=\boxed{-\frac{p_kp_\ell}{n}.}
\end{aligned}
$$

> [!intuition]
> La covariance est négative parce que les fréquences doivent se partager une masse totale égale à $1$. Si une catégorie est surreprésentée dans l'échantillon, les autres ont tendance à être sous-représentées.

### 3. Convergence vers les vraies probabilités

> [!question] Question
> Montrer que $\widehat p$ converge presque sûrement vers $p=(p_1,\ldots,p_K)^\top$.

Fixons une catégorie $k$ et posons

$$
I_i^{(k)}=\mathbf 1_{\{X_i=k\}}.
$$

Cette variable ne retient de $X_i$ qu'une information : « l'observation est-elle dans la catégorie $k$ ? ». Elle suit une loi de Bernoulli :

$$
\mathbb P\bigl(I_i^{(k)}=1\bigr)=\mathbb P(X_i=k)=p_k,
\qquad
\mathbb P\bigl(I_i^{(k)}=0\bigr)=1-p_k.
$$

Les $X_i$ sont i.i.d. et l'on leur applique tous la même fonction, $x\mapsto\mathbf 1_{\{x=k\}}$. Les $I_i^{(k)}$ sont donc elles aussi i.i.d. Elles sont intégrables car elles ne prennent que les valeurs $0$ et $1$. Enfin,

$$
\mathbb E\bigl[I_i^{(k)}\bigr]
=1\times p_k+0\times(1-p_k)
=p_k.
$$

> [!theorem] Rappel — loi forte des grands nombres
> Si $Y_1,Y_2,\ldots$ sont des variables i.i.d. telles que $\mathbb E[|Y_1|]<+\infty$, alors
>
> $$
> \frac1n\sum_{i=1}^nY_i
> \xrightarrow[n\to\infty]{\mathrm{p.s.}}
> \mathbb E[Y_1].
> $$
>
> La moyenne empirique converge donc presque sûrement vers l'espérance de la variable observée.

On peut appliquer ce résultat aux $I_i^{(k)}$, car elles sont i.i.d. et bornées, donc intégrables :

$$
\widehat p_k
=\frac1n\sum_{i=1}^n I_i^{(k)}
\xrightarrow[n\to\infty]{\mathrm{p.s.}}p_k.
$$

La notation « presque sûrement » signifie qu'avec probabilité $1$, une fois que l'on a tiré une suite infinie d'observations, sa fréquence empirique $\widehat p_k$ se rapproche de $p_k$ et y reste aussi proche que l'on veut à partir d'un certain rang.

Cela est vrai pour chacune des $K$ coordonnées. Comme il y en a un nombre fini,

$$
\widehat p\xrightarrow[n\to\infty]{\mathrm{p.s.}}p.
$$

Autrement dit, avec beaucoup de données, les proportions observées finissent presque sûrement par révéler les proportions de la population.

---

## Exercice 2 - Divergence de Bregman, KL et entropie croisée

Pour une fonction $F$ différentiable et strictement convexe,

$$
D_F(p,q)=F(p)-F(q)-\langle\nabla F(q),p-q\rangle.
$$

> [!intuition]
> $D_F(p,q)$ mesure l'écart entre la vraie valeur de $F$ en $p$ et ce qu'aurait prédit l'approximation linéaire de $F$ construite autour de $q$.

> [!example] Commencer par une fonction d'une variable
> Si $F:\mathbb R\to\mathbb R$, on peut dessiner sa courbe. Le point de la courbe d'abscisse $q$ est $(q,F(q))$. La **droite tangente en $q$** est la droite qui passe par ce point et qui a la même pente que la courbe à cet endroit. Cette pente est $F'(q)$, et l'équation de la droite est
>
> $$
> T_q(x)=F(q)+F'(q)(x-q).
> $$
>
> Ainsi, $F(q)$ n'est pas une « hauteur » au sens physique : c'est simplement la valeur de sortie de la fonction au point $q$. C'est l'ordonnée du point de contact et, par conséquent, la valeur de départ de la droite tangente.

Quand $F$ dépend de plusieurs variables, on ne peut plus représenter son graphe par une courbe dans un plan. La droite tangente devient alors un **plan tangent** : c'est la généralisation de cette droite, qui passe toujours par le point $(q,F(q))$ et possède les mêmes pentes locales que $F$ en $q$.

On note cette approximation affine $T_q$. Son évaluation en un point quelconque $x$ est

$$
T_q(x)=F(q)+\langle\nabla F(q),x-q\rangle.
$$

Ici, le gradient $\nabla F(q)$ joue le rôle de la pente $F'(q)$ : il rassemble les pentes selon toutes les directions. En particulier, à la position $p$, cette approximation prédit

$$
T_q(p)=F(q)+\langle\nabla F(q),p-q\rangle.
$$

La divergence de Bregman est alors simplement

$$
\begin{aligned}
D_F(p,q)
&=F(p)-\left[F(q)+\langle\nabla F(q),p-q\rangle\right]\\
&=F(p)-T_q(p).
\end{aligned}
$$

Les éléments de la formule ont donc les rôles suivants :

- $F(p)$ est la vraie valeur de la fonction au point que l'on veut atteindre ;
- $F(q)$ est la vraie valeur de la fonction au point de départ $q$ ; la tangente ou le plan tangent passe donc par $(q,F(q))$ ;
- $p-q$ est le déplacement de $q$ vers $p$ ;
- $\langle\nabla F(q),p-q\rangle$ est la variation que prédit la pente locale de $F$ le long de ce déplacement.

En dimension $1$, le produit scalaire devient un produit ordinaire et l'on retrouve exactement la formule de la droite tangente :

$$
F(p)\approx F(q)+F'(q)(p-q).
$$

Pour une fonction convexe, le plan tangent est toujours sous le graphe :

$$
F(p)\geq T_q(p).
$$

Ainsi $D_F(p,q)\geq0$. L'ordre des arguments compte : on évalue la tangente construite en $q$ au point $p$, ce qui explique pourquoi, en général, $D_F(p,q)$ et $D_F(q,p)$ ne coïncident pas.

### 1. Cas de la norme euclidienne au carré

> [!question] Question
> Montrer que, pour $F(x)=\lVert x\rVert_2^2$, on a $D_F(p,q)=\lVert p-q\rVert_2^2$.

Ici $\nabla F(x)=2x$. En remplaçant dans la définition,

$$
\begin{aligned}
D_F(p,q)
&=\lVert p\rVert_2^2-\lVert q\rVert_2^2-\langle2q,p-q\rangle\\
&=\lVert p\rVert_2^2-\lVert q\rVert_2^2-2\langle q,p-q\rangle\\
&=\lVert p\rVert_2^2-\lVert q\rVert_2^2-2\bigl(\langle q,p\rangle-\langle q,q\rangle\bigr)\\
&=\lVert p\rVert_2^2-\lVert q\rVert_2^2-2\langle p,q\rangle+2\lVert q\rVert_2^2\\
&=\lVert p\rVert_2^2+\lVert q\rVert_2^2-2\langle p,q\rangle\\
&=\lVert p-q\rVert_2^2.
\end{aligned}
$$

Cette identité explique le mot « généralise » : en choisissant la fonction particulière $F(x)=\lVert x\rVert_2^2$, la divergence de Bregman retrouve exactement le carré de l'écart euclidien. Avec une autre fonction convexe $F$, on remplace cette mesure quadratique de l'écart par l'écart entre $F$ et sa tangente ; la manière de mesurer la différence entre $p$ et $q$ s'adapte alors à la géométrie de $F$.

> [!example] Un exemple à un seul nombre
> Prenons $F(x)=x^2$, $q=1$ et $p=3$. La tangente construite en $q=1$ est
>
> $$
> T_1(x)=F(1)+F'(1)(x-1)=1+2(x-1)=2x-1.
> $$
>
> Au point $p=3$, la vraie courbe vaut $F(3)=9$, tandis que sa tangente prédit seulement $T_1(3)=5$. L'écart entre les deux est donc
>
> $$
> D_F(3,1)=9-5=4=(3-1)^2.
> $$
>
> Dans ce cas précis, la règle de Bregman donne exactement le carré de la distance euclidienne. « Généraliser » signifie ici : garder une recette qui fonctionne pour cette distance particulière, tout en autorisant d'autres choix de fonction $F$ pour définir d'autres écarts.

Une divergence de Bregman conserve deux propriétés utiles de l'écart quadratique :

$$
D_F(p,q)\geq0,
\qquad
D_F(q,q)=0.
$$

Lorsque $F$ est strictement convexe, $D_F(p,q)=0$ implique même $p=q$. En revanche, elle n'est généralement pas une distance au sens mathématique :

- elle peut être non symétrique, avec $D_F(p,q)\ne D_F(q,p)$ ;
- elle ne vérifie pas nécessairement l'inégalité triangulaire.

> [!note]
> Même $\lVert p-q\rVert_2^2$ n'est pas une distance au sens strict : c'est son carré qui intervient ici, et le carré ne satisfait pas en général l'inégalité triangulaire. La distance euclidienne elle-même est $\lVert p-q\rVert_2$.

La divergence de Bregman est donc plutôt une **mesure directionnelle d'écart** : elle compare $p$ à l'approximation de $F$ construite autour de $q$.

### 2. La divergence KL comme divergence de Bregman

> [!question] Question
> Pour $H(p)=-\sum_{i=1}^Kp_i\log p_i$ et $\operatorname{KL}(p,q)=\sum_{i=1}^Kp_i\log(p_i/q_i)$, montrer que $\operatorname{KL}$ est la divergence de Bregman associée à $-H$.

L'entropie d'un vecteur de probabilités $r=(r_1,\ldots,r_K)$ est définie par

$$
H(r)=-\sum_{i=1}^Kr_i\log r_i.
$$

Pour construire une divergence de Bregman, on veut utiliser une fonction convexe. Or c'est $-H$, et non $H$, qui est convexe. On choisit donc

$$
F=-H.
$$

Pour le vérifier, regardons un terme de l'entropie, défini pour $x>0$ par

$$
h(x)=-x\log x.
$$

Ses dérivées sont

$$
\begin{aligned}
h'(x)
&=-\frac{\mathrm d}{\mathrm dx}\bigl(x\log x\bigr)\\
&=-\left(1\times\log x+x\times\frac1x\right)\\
&=-(1+\log x).
\end{aligned}
$$

$$
h''(x)=-\frac1x<0.
$$

La deuxième égalité utilise la règle du produit : la dérivée de $x$ est $1$, et celle de $\log x$ est $1/x$.

Une dérivée seconde strictement négative signifie que la courbe est tournée vers le bas : $h$ est strictement concave. Comme

$$
H(r)=\sum_{i=1}^Kh(r_i),
$$

l'entropie $H$ est elle aussi strictement concave sur l'intérieur du simplexe. Elle n'est donc pas convexe, sauf dans le cas dégénéré où l'on se limiterait à un seul point. Changer le signe retourne la courbure : $-H$ est strictement convexe, ce qui est précisément la propriété requise pour définir une divergence de Bregman.

Le signe moins de $F=-H$ annule celui qui apparaît dans la définition de $H$ :

$$
\begin{aligned}
F(r)
&=-H(r)\\
&=-\left(-\sum_{i=1}^Kr_i\log r_i\right)\\
&=\sum_{i=1}^Kr_i\log r_i.
\end{aligned}
$$

La formule est valable pour tout vecteur de probabilités $r$ dont les coordonnées sont strictement positives. Dans la définition de $D_F(p,q)$, il faut notamment calculer $F(q)$ ; on remplace donc simplement $r$ par $q$ :

$$
F(q)=\sum_{i=1}^K q_i\log q_i.
$$

Sa $i$-ème dérivée est $1+\log q_i$. On développe alors :

$$
\begin{aligned}
D_{-H}(p,q)
&=\sum_i p_i\log p_i-\sum_i q_i\log q_i
-\sum_i(1+\log q_i)(p_i-q_i)\\
&=\sum_i p_i\log p_i-\sum_i q_i\log q_i
-\sum_i\bigl(p_i+p_i\log q_i-q_i-q_i\log q_i\bigr)\\
&=\sum_i p_i\log p_i-\sum_i q_i\log q_i
-\sum_i p_i-\sum_i p_i\log q_i+\sum_i q_i+\sum_i q_i\log q_i\\
&=\sum_i p_i\log p_i-\sum_i q_i\log q_i
-1-\sum_i p_i\log q_i+1+\sum_i q_i\log q_i\\
&=\sum_i p_i\log p_i-\sum_i p_i\log q_i\\
&=\sum_i p_i\log\left(\frac{p_i}{q_i}\right)\\
&=\operatorname{KL}(p,q).
\end{aligned}
$$

Les termes linéaires disparaissent parce que $\sum_i p_i=\sum_iq_i=1$.

### 3. Positivité de la divergence KL

> [!question] Question
> Montrer que $\operatorname{KL}(p,q)\geq0$, avec égalité si et seulement si $p=q$.

La fonction $-H$ est strictement convexe sur l'intérieur du simplexe. Son graphe est donc toujours au-dessus de sa tangente en $q$ :

$$
(-H)(p)
\geq(-H)(q)+\langle\nabla(-H)(q),p-q\rangle.
$$

Pour isoler l'écart entre les deux membres, on soustrait le membre de droite aux deux côtés :

$$
(-H)(p)-\left[(-H)(q)+\langle\nabla(-H)(q),p-q\rangle\right]
\geq0.
$$

En développant les crochets, le membre de gauche devient

$$
(-H)(p)-(-H)(q)-\langle\nabla(-H)(q),p-q\rangle.
$$

C'est exactement la définition de la divergence de Bregman associée à la fonction $F=-H$ :

$$
D_{-H}(p,q)
=(-H)(p)-(-H)(q)-\langle\nabla(-H)(q),p-q\rangle.
$$

La question précédente a ensuite montré, par calcul direct, que

$$
D_{-H}(p,q)=\operatorname{KL}(p,q).
$$

La convexité de $-H$ donne donc

$$
\operatorname{KL}(p,q)\geq0.
$$

La stricte convexité donne l'égalité uniquement lorsque $p=q$.

> [!warning]
> Malgré sa positivité, la divergence KL n'est pas une distance : en général $\operatorname{KL}(p,q)\ne\operatorname{KL}(q,p)$, et l'inégalité triangulaire n'est pas satisfaite.

### 4. Excès d'objectif et divergence de Bregman

> [!question] Question
> Soit $w^\star$ un minimiseur de $R$ sur un ouvert convexe. Montrer que $E(w)=R(w)-R(w^\star)=D_R(w,w^\star)$ et préciser la condition d'optimalité utilisée.

Comme $w^\star$ est un minimiseur intérieur d'une fonction différentiable,

$$
\nabla R(w^\star)=0.
$$

C'est la condition d'optimalité du premier ordre. Elle annule le terme linéaire dans la divergence :

$$
\begin{aligned}
D_R(w,w^\star)
&=R(w)-R(w^\star)-\langle\nabla R(w^\star),w-w^\star\rangle\\
&=R(w)-R(w^\star)\\
&=E(w).
\end{aligned}
$$

Ainsi, lorsque l'on compare un point à un minimum intérieur, la divergence de Bregman est exactement l'excès de critère.

### 5. Entropie croisée et meilleur classifieur probabiliste

> [!question] Question
> Pour la perte $\ell(q,Y)=-\log q_Y$, montrer que le risque $R(q)=\mathbb E[\ell(q,Y)]$ vaut $H(p)+\operatorname{KL}(p,q)$. En déduire la prédiction optimale dans la population et l'excès de risque.

Si la vraie classe $Y$ suit la loi $p$, alors

$$
R(q)=-\sum_{k=1}^Kp_k\log q_k.
$$

On ajoute et retranche $\sum_kp_k\log p_k$ :

$$
\begin{aligned}
R(q)
&=-\sum_kp_k\log p_k
+\sum_kp_k\log\left(\frac{p_k}{q_k}\right)\\
&=H(p)+\operatorname{KL}(p,q).
\end{aligned}
$$

Le terme $H(p)$ ne dépend pas de la prédiction $q$. Comme la KL est minimale et nulle lorsque $q=p$, la prédiction optimale est

$$
q^\star=p.
$$

Le risque minimal est $H(p)$ et l'excès de risque est

$$
R(q)-R(q^\star)=\operatorname{KL}(p,q).
$$

> [!intuition]
> Avec la cross-entropie, annoncer les vraies probabilités conditionnelles est optimal. La divergence KL mesure exactement le prix payé quand les probabilités annoncées s'en écartent.

---

## Exercice 3 - ACP

Les observations $x_1,\ldots,x_n\in\mathbb R^p$ sont centrées. Leur matrice de covariance empirique est

$$
\widehat\Sigma=\frac1n\sum_{i=1}^n x_ix_i^\top.
$$

On note $\lambda_1\geq\cdots\geq\lambda_p\geq0$ ses valeurs propres et $v_1,\ldots,v_p$ des vecteurs propres orthonormés associés.

### 1. Première direction principale

> [!question] Question
> Montrer que $v_1\in\arg\max_{\lVert v\rVert_2=1}\frac1n\sum_{i=1}^n(v^\top x_i)^2$.

La variance empirique des données projetées sur une direction unitaire $v$ est

$$
\frac1n\sum_{i=1}^n(v^\top x_i)^2=v^\top\widehat\Sigma v.
$$

Écrivons $v=\sum_{j=1}^pa_jv_j$. Comme $\lVert v\rVert_2=1$, on a $\sum_ja_j^2=1$. Alors

$$
v^\top\widehat\Sigma v
=\sum_{j=1}^p\lambda_ja_j^2
\leq\lambda_1\sum_{j=1}^pa_j^2
=\lambda_1.
$$

L'égalité est atteinte avec $v=v_1$. La première direction principale est donc la direction qui conserve la plus grande variance possible après projection sur une droite.

### 2. Les $k$ premières directions

> [!question] Question
> Expliquer comment obtenir les $k$ premières directions principales.

On diagonalise $\widehat\Sigma$ et on garde les $k$ vecteurs propres associés aux $k$ plus grandes valeurs propres :

$$
v_1,\ldots,v_k.
$$

Ils sont orthogonaux les uns aux autres. Leur espace engendré,

$$
V_k=\operatorname{span}(v_1,\ldots,v_k),
$$

est le sous-espace de dimension $k$ retenu par l'ACP.

### 3. Part de variance expliquée

> [!question] Question
> Montrer que la fraction de variance expliquée par les $k$ premières composantes est $\frac{\lambda_1+\cdots+\lambda_k}{\lambda_1+\cdots+\lambda_p}$.

La variance totale vaut la trace de la covariance empirique :

$$
\operatorname{tr}(\widehat\Sigma)=\sum_{j=1}^p\lambda_j.
$$

Chaque direction principale $v_j$ explique une quantité de variance égale à $\lambda_j$. En gardant les $k$ premières, on conserve donc $\sum_{j=1}^k\lambda_j$ sur un total de $\sum_{j=1}^p\lambda_j$, soit

$$
\boxed{\frac{\lambda_1+\cdots+\lambda_k}
{\lambda_1+\cdots+\lambda_p}.}
$$

### 4. L'ACP comme meilleure approximation linéaire

> [!question] Question
> Montrer que le sous-espace d'ACP de dimension $k$ minimise $\frac1n\sum_{i=1}^n\lVert x_i-P_Vx_i\rVert_2^2$ parmi tous les sous-espaces $V$ de dimension $k$.

Pour tout sous-espace $V$, le théorème de Pythagore donne

$$
\lVert x_i\rVert_2^2
=\lVert P_Vx_i\rVert_2^2
+\lVert x_i-P_Vx_i\rVert_2^2.
$$

Comme le premier membre ne dépend pas de $V$, minimiser l'erreur de reconstruction revient à maximiser la variance conservée par la projection. Pour un espace de dimension $k$, cette variance maximale est obtenue en gardant les directions associées aux $k$ plus grandes valeurs propres. Ainsi,

$$
V_k=\operatorname{span}(v_1,\ldots,v_k)
$$

minimise l'erreur quadratique moyenne de reconstruction.

> [!intuition]
> L'ACP ne cherche pas des axes « jolis » : elle cherche le plan de dimension $k$ le plus proche du nuage de points au sens des moindres carrés.

### 5. Lien avec la SVD

> [!question] Question
> Si $X=USV^\top$ est une SVD de la matrice de données centrées, relier les directions principales à cette décomposition.

Les lignes de $X$ sont les $x_i^\top$, donc

$$
\widehat\Sigma=\frac1nX^\top X.
$$

Avec $X=USV^\top$,

$$
\widehat\Sigma
=V\frac{S^\top S}{n}V^\top.
$$

Les colonnes de $V$ sont donc les directions principales. Si $s_j$ est la $j$-ème valeur singulière, la valeur propre correspondante est

$$
\lambda_j=\frac{s_j^2}{n}.
$$

La SVD permet ainsi de calculer l'ACP directement, même lorsque $X$ n'est pas une matrice carrée.

---

## Exercice 4 - Méthode des moments et maximum de vraisemblance

On suppose

$$
X_1,\ldots,X_n\overset{\mathrm{i.i.d.}}{\sim}\mathcal U([0,\theta]),
\qquad \theta>0.
$$

On pose $\overline X_n=\frac1n\sum_iX_i$ et $M_n=\max(X_1,\ldots,X_n)$.

### 1. Estimateur des moments

> [!question] Question
> Calculer l'estimateur des moments $\widehat\theta_{\mathrm{MM}}$.

Pour une loi uniforme sur $[0,\theta]$,

$$
\mathbb E_\theta[X_i]=\frac\theta2.
$$

La méthode des moments remplace l'espérance théorique par la moyenne observée :

$$
\overline X_n=\frac\theta2.
$$

On résout cette égalité en $\theta$ :

$$
\boxed{\widehat\theta_{\mathrm{MM}}=2\overline X_n.}
$$

### 2. Estimateur du maximum de vraisemblance

> [!question] Question
> Montrer que l'estimateur du maximum de vraisemblance existe, est unique, et le calculer.

La densité d'une observation est

$$
f_\theta(x)=\frac1\theta\mathbf 1_{[0,\theta]}(x).
$$

Donc la vraisemblance de l'échantillon est

$$
L(\theta)=\theta^{-n}\mathbf 1_{\{\theta\geq M_n\}}.
$$

Si $\theta<M_n$, la vraisemblance est nulle : un tel paramètre ne peut pas avoir produit la plus grande observation. Sur $[M_n,+\infty)$, la fonction $\theta\mapsto\theta^{-n}$ est strictement décroissante. Son maximum unique est donc atteint au plus petit paramètre admissible :

$$
\boxed{\widehat\theta_{\mathrm{MV}}=M_n.}
$$

> [!intuition]
> Le maximum de vraisemblance choisit exactement la plus petite borne supérieure compatible avec toutes les données. Toute borne plus grande dilue inutilement la densité uniforme.

### 3. Loi, biais et variance de l'estimateur du maximum

> [!question] Question
> Montrer que $\widehat\theta_{\mathrm{MV}}/\theta\sim\operatorname{Beta}(n,1)$, puis en déduire son biais et sa variance.

Pour $x\in[0,1]$,

$$
\begin{aligned}
\mathbb P\left(\frac{M_n}{\theta}\leq x\right)
&=\mathbb P(X_1\leq\theta x,\ldots,X_n\leq\theta x)\\
&=x^n.
\end{aligned}
$$

La densité correspondante est $nx^{n-1}\mathbf 1_{[0,1]}(x)$ : c'est bien une loi $\operatorname{Beta}(n,1)$. Ses moments donnent

$$
\mathbb E[M_n]=\frac{n}{n+1}\theta,
\qquad
\operatorname{Var}(M_n)=\frac{n}{(n+1)^2(n+2)}\theta^2.
$$

Ainsi,

$$
\operatorname{Biais}(\widehat\theta_{\mathrm{MV}})
=-\frac{\theta}{n+1}.
$$

L'estimateur sous-estime donc systématiquement $\theta$, ce qui est naturel : le maximum observé reste inférieur à la borne réelle avec probabilité $1$ pour un échantillon fini.

### 4. Biais, variance et comparaison des risques quadratiques

> [!question] Question
> Calculer le biais et la variance de $\widehat\theta_{\mathrm{MM}}$. Comparer les erreurs quadratiques moyennes des deux estimateurs.

Comme $\mathbb E[X_i]=\theta/2$ et $\operatorname{Var}(X_i)=\theta^2/12$,

$$
\mathbb E[\widehat\theta_{\mathrm{MM}}]=\theta,
\qquad
\operatorname{Var}(\widehat\theta_{\mathrm{MM}})
=4\operatorname{Var}(\overline X_n)
=\frac{\theta^2}{3n}.
$$

L'estimateur des moments est donc sans biais, et

$$
\operatorname{MSE}(\widehat\theta_{\mathrm{MM}})
=\frac{\theta^2}{3n}.
$$

Pour le maximum de vraisemblance, on utilise $\operatorname{MSE}=\operatorname{Var}+\operatorname{Biais}^2$ :

$$
\operatorname{MSE}(\widehat\theta_{\mathrm{MV}})
=\frac{2\theta^2}{(n+1)(n+2)}.
$$

Les deux MSE sont égales pour $n=1$ et $n=2$. À partir de $n=3$,

$$
\operatorname{MSE}(\widehat\theta_{\mathrm{MV}})
<\operatorname{MSE}(\widehat\theta_{\mathrm{MM}}).
$$

Le maximum, bien que biaisé, devient préférable parce que son erreur décroît en ordre $1/n^2$, contre $1/n$ pour l'estimateur des moments.

### 5. Corriger le biais du maximum

> [!question] Question
> Construire un estimateur sans biais à partir de $\widehat\theta_{\mathrm{MV}}$. Calculer sa MSE et la comparer à celle de $\widehat\theta_{\mathrm{MM}}$.

Puisque $\mathbb E[M_n]=\frac{n}{n+1}\theta$, il suffit de corriger ce facteur :

$$
\widetilde\theta=\frac{n+1}{n}M_n.
$$

Alors $\mathbb E[\widetilde\theta]=\theta$. L'estimateur étant sans biais, sa MSE est sa variance :

$$
\operatorname{MSE}(\widetilde\theta)
=\operatorname{Var}(\widetilde\theta)
=\frac{\theta^2}{n(n+2)}.
$$

Pour $n>1$,

$$
\frac{\theta^2}{n(n+2)}
<\frac{\theta^2}{3n}
=\operatorname{MSE}(\widehat\theta_{\mathrm{MM}}).
$$

La correction du biais conserve donc l'avantage du maximum sur la méthode des moments, sauf au cas limite $n=1$ où les deux coïncident.

### 6. Comportement asymptotique non standard

> [!question] Question
> Montrer que $n\left(1-\widehat\theta_{\mathrm{MV}}/\theta\right)$ converge en loi vers $\mathcal E(1)$. Quelle hypothèse usuelle de régularité du MLE échoue ici ?

Pour $x\geq0$,

$$
\begin{aligned}
\mathbb P\left(n\left(1-\frac{M_n}{\theta}\right)>x\right)
&=\mathbb P\left(\frac{M_n}{\theta}<1-\frac xn\right)\\
&=\left(1-\frac xn\right)^n
\xrightarrow[n\to\infty]{}e^{-x}.
\end{aligned}
$$

La limite est la fonction de survie d'une loi exponentielle de paramètre $1$. Donc

$$
n\left(1-\frac{\widehat\theta_{\mathrm{MV}}}{\theta}\right)
\xrightarrow[n\to\infty]{\mathcal L}\mathcal E(1).
$$

L'hypothèse de régularité qui échoue est celle d'un **support fixe** : ici,

$$
\operatorname{Supp}(X_i)=[0,\theta]
$$

dépend du paramètre. Cette dépendance explique la vitesse en $n$, plutôt qu'en $\sqrt n$, et la limite exponentielle plutôt que normale.

---

## Exercice 5 - Estimateurs du maximum de vraisemblance

On considère d'abord $X_1,\ldots,X_n\overset{\mathrm{i.i.d.}}{\sim}\operatorname{Ber}(p)$, avec $p\in[0,1]$, et on note $S=\sum_{i=1}^nX_i$.

### 1(a). MLE du paramètre de Bernoulli

> [!question] Question
> Calculer le MLE $\widehat p_{\mathrm{MV}}$, y compris lorsque toutes les observations valent $0$ ou toutes valent $1$.

La vraisemblance s'écrit

$$
L(p)=\prod_{i=1}^np^{X_i}(1-p)^{1-X_i}
=p^S(1-p)^{n-S}.
$$

Lorsque $0<S<n$, on maximise la log-vraisemblance

$$
\ell(p)=S\log p+(n-S)\log(1-p).
$$

Son équation $\ell'(p)=0$ donne $S(1-p)=p(n-S)$, d'où

$$
\widehat p_{\mathrm{MV}}=\frac Sn=\overline X_n.
$$

La même formule couvre les cas limites :

$$
S=0\Rightarrow\widehat p_{\mathrm{MV}}=0,
\qquad
S=n\Rightarrow\widehat p_{\mathrm{MV}}=1.
$$

La fréquence observée de succès est donc le MLE de la probabilité de succès.

### 1(b). Information de Fisher

> [!question] Question
> En supposant $p\in(0,1)$, calculer l'information de Fisher pour une observation puis pour l'échantillon.

Le score d'une observation est

$$
\frac{\partial}{\partial p}\log f_p(X_1)
=\frac{X_1-p}{p(1-p)}.
$$

Son carré moyen vaut

$$
\begin{aligned}
I_{X_1}(p)
&=\mathbb E\left[\left(\frac{X_1-p}{p(1-p)}\right)^2\right]\\
&=\frac{\operatorname{Var}(X_1)}{p^2(1-p)^2}\\
&=\frac1{p(1-p)}.
\end{aligned}
$$

L'information s'additionne pour des observations indépendantes :

$$
\boxed{I_n(p)=\frac{n}{p(1-p)}.}
$$

### 1(c). Loi asymptotique et borne de Cramér-Rao

> [!question] Question
> Calculer la loi asymptotique de $\sqrt n(\widehat p_{\mathrm{MV}}-p)$ et comparer sa variance asymptotique à la borne de Cramér-Rao.

Comme $\widehat p_{\mathrm{MV}}=\overline X_n$ et $\operatorname{Var}(X_i)=p(1-p)$, le théorème central limite donne

$$
\sqrt n\bigl(\widehat p_{\mathrm{MV}}-p\bigr)
\xrightarrow[n\to\infty]{\mathcal L}
\mathcal N\bigl(0,p(1-p)\bigr).
$$

Ainsi, la variance de l'estimateur est asymptotiquement $p(1-p)/n$. Or l'inverse de l'information de Fisher de l'échantillon est

$$
\frac1{I_n(p)}=\frac{p(1-p)}n.
$$

La variance asymptotique atteint donc la borne de Cramér-Rao : la fréquence empirique est asymptotiquement efficace.

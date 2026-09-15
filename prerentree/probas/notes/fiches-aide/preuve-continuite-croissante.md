---
title: Continuité croissante d'une probabilité - preuve
aliases:
  - Preuve de la continuité croissante
  - Continuité par en dessous
  - Continuité par le bas
tags:
  - mva
  - probabilites
  - mesure
  - preuve
parent: "[[cours-principal]]"
---

# Continuité croissante d'une probabilité - preuve

> [!abstract] Théorème
> Soit $(A_n)_{n\in\mathbb N}$ une suite croissante d'événements :
> $$
> A_0\subseteq A_1\subseteq A_2\subseteq\cdots.
> $$
> Si
> $$
> A=\bigcup_{n\in\mathbb N}A_n,
> $$
> alors
> $$
> \boxed{\mathbb P(A)=\lim_{n\to\infty}\mathbb P(A_n)}.
> $$

Cette propriété est appelée **continuité croissante**, **continuité par en dessous** ou **continuité par le bas**.

Retour au cours : [[cours-principal#Suite croissante d'événements|Continuité des probabilités - suite croissante]].

## 1. Ce qu'il faut démontrer

Deux objets différents interviennent :

- les ensembles $A_n$, qui croissent vers l'ensemble $A$ ;
- les nombres $\mathbb P(A_n)$, qui doivent converger vers $\mathbb P(A)$.

La croissance des ensembles entraîne déjà, par monotonie de la probabilité,

$$
\mathbb P(A_0)\leq\mathbb P(A_1)\leq\mathbb P(A_2)\leq\cdots.
$$

La suite réelle $(\mathbb P(A_n))_n$ est donc croissante. Comme chaque terme appartient à $[0,1]$, elle est majorée par $1$ et possède une limite.

> [!important]
> Cela prouve que la limite existe, mais pas encore qu'elle est égale à $\mathbb P(A)$. Pour identifier cette limite, on décompose $A$ en couches disjointes.

## 2. Transformer les ensembles emboîtés en couches disjointes

On pose

$$
B_0=A_0
$$

et, pour $n\geq1$,

$$
\boxed{B_n=A_n\setminus A_{n-1}}.
$$

La couche $B_n$ contient les nouvelles issues qui apparaissent lorsque l'on passe de $A_{n-1}$ à $A_n$.

Ainsi,

$$
\begin{aligned}
B_0&=A_0,\\
B_1&=A_1\setminus A_0,\\
B_2&=A_2\setminus A_1,\\
&\ \vdots
\end{aligned}
$$

### Pourquoi les couches sont-elles disjointes ?

Prenons $m<n$. Puisque $m$ et $n$ sont des entiers, cette inégalité implique

$$
m\leq n-1.
$$

Or, une suite croissante vérifie $A_i\subseteq A_j$ dès que $i\leq j$. En choisissant $i=m$ et $j=n-1$, on obtient

$$
A_m\subseteq A_{n-1}.
$$

De plus, $B_m\subseteq A_m$. En effet, pour $m\geq1$,

$$
B_m=A_m\setminus A_{m-1}
=\{\omega\in A_m:\omega\notin A_{m-1}\}
\subseteq A_m,
$$

et pour $m=0$, l'égalité $B_0=A_0$ donne immédiatement le même résultat. Ainsi,

$$
B_m\subseteq A_m\subseteq A_{n-1}.
$$

Mais

$$
B_n=A_n\setminus A_{n-1}
$$

ne contient aucun élément de $A_{n-1}$. Par conséquent,

$$
B_m\cap B_n=\varnothing.
$$

Les événements $(B_n)_{n\in\mathbb N}$ sont donc deux à deux disjoints.

## 3. Reconstruire chaque $A_N$

Les couches s'assemblent sans recouvrement :

$$
A_1=A_0\sqcup(A_1\setminus A_0)=B_0\sqcup B_1,
$$

puis

$$
A_2=B_0\sqcup B_1\sqcup B_2,
$$

et ainsi de suite. Pour tout $N\in\mathbb N$,

$$
\boxed{A_N=\bigsqcup_{n=0}^{N}B_n}.
$$

Le symbole $\bigsqcup$ indique que l'union est disjointe.

Par additivité sur cette union finie,

$$
\boxed{\mathbb P(A_N)=\sum_{n=0}^{N}\mathbb P(B_n)}.
$$

## 4. Reconstruire l'union infinie $A$

Puisque chaque $A_N$ est formé des $N+1$ premières couches,

$$
\begin{aligned}
A
&=\bigcup_{N\in\mathbb N}A_N\\
&=\bigcup_{N\in\mathbb N}\left(\bigsqcup_{n=0}^{N}B_n\right)\\
&=\bigsqcup_{n=0}^{\infty}B_n.
\end{aligned}
$$

Les $B_n$ étant deux à deux disjoints, la $\sigma$-additivité donne

$$
\boxed{\mathbb P(A)=\sum_{n=0}^{\infty}\mathbb P(B_n)}.
$$

## 5. Développer précisément le passage à la limite

Définissons la somme partielle

$$
S_N=\sum_{n=0}^{N}\mathbb P(B_n).
$$

D'après la reconstruction de $A_N$,

$$
\boxed{S_N=\mathbb P(A_N)}.
$$

### 5.1 La série infinie est définie par les sommes partielles

Par définition,

$$
\sum_{n=0}^{\infty}\mathbb P(B_n)
=\lim_{N\to\infty}\sum_{n=0}^{N}\mathbb P(B_n)
=\lim_{N\to\infty}S_N.
$$

Il n'y a donc pas ici d'interversion mystérieuse entre une limite et une somme : **la somme d'une série est elle-même définie comme la limite de ses sommes partielles**.

### 5.2 Pourquoi cette limite existe-t-elle ?

Tous les termes sont positifs :

$$
\mathbb P(B_n)\geq0.
$$

Par conséquent,

$$
S_{N+1}=S_N+\mathbb P(B_{N+1})\geq S_N.
$$

La suite $(S_N)_N$ est croissante. De plus,

$$
S_N=\mathbb P(A_N)\leq1.
$$

Elle est donc croissante et majorée, ce qui garantit sa convergence dans $\mathbb R$.

### 5.3 Identifier la limite

On dispose maintenant des trois égalités suivantes :

$$
\mathbb P(A_N)=S_N,
$$

$$
\lim_{N\to\infty}S_N
=\sum_{n=0}^{\infty}\mathbb P(B_n),
$$

et, par $\sigma$-additivité,

$$
\sum_{n=0}^{\infty}\mathbb P(B_n)=\mathbb P(A).
$$

En les enchaînant,

$$
\begin{aligned}
\lim_{N\to\infty}\mathbb P(A_N)
&=\lim_{N\to\infty}S_N\\
&=\sum_{n=0}^{\infty}\mathbb P(B_n)\\
&=\mathbb P\!\left(\bigsqcup_{n=0}^{\infty}B_n\right)\\
&=\mathbb P(A).
\end{aligned}
$$

D'où le résultat :

$$
\boxed{\lim_{N\to\infty}\mathbb P(A_N)=\mathbb P(A)}.
$$

## 6. Lecture télescopique

Comme $A_{n-1}\subseteq A_n$, pour $n\geq1$,

$$
\mathbb P(B_n)
=\mathbb P(A_n\setminus A_{n-1})
=\mathbb P(A_n)-\mathbb P(A_{n-1}).
$$

Ainsi,

$$
\begin{aligned}
\sum_{n=0}^{N}\mathbb P(B_n)
&=\mathbb P(A_0)
+\sum_{n=1}^{N}\big(\mathbb P(A_n)-\mathbb P(A_{n-1})\big)\\
&=\mathbb P(A_N).
\end{aligned}
$$

En écrivant les premiers et les derniers termes,

$$
\mathbb P(A_0)
+\big(\mathbb P(A_1)-\mathbb P(A_0)\big)
+\big(\mathbb P(A_2)-\mathbb P(A_1)\big)
+\cdots
+\big(\mathbb P(A_N)-\mathbb P(A_{N-1})\big)
=\mathbb P(A_N).
$$

Chaque terme $+\mathbb P(A_k)$, pour $k<N$, est annulé par le terme $-\mathbb P(A_k)$ qui suit. Cette lecture met en évidence que les couches $B_n$ représentent exactement les incréments successifs de probabilité.

## 7. Exemple concret

Prenons $X\sim\mathcal U([0,1])$ et

$$
A_n=\left\{X\leq1-\frac{1}{n+1}\right\},
\qquad n\geq1.
$$

Comme

$$
1-\frac{1}{n+1}=\frac{n}{n+1},
$$

on peut aussi écrire

$$
A_n=\left\{X\leq\frac{n}{n+1}\right\}.
$$

> [!note] Événement et intervalle
> $A_n$ est un événement dans l'univers $\Omega$. Plus précisément,
> $$
> A_n=X^{-1}\!\left(\left(-\infty,\frac{n}{n+1}\right]\right).
> $$
> Il contient les issues $\omega$ pour lesquelles $X(\omega)\leq n/(n+1)$. Comme la loi de $X$ est portée par $[0,1]$, le calcul de sa probabilité revient à mesurer l'intervalle $[0,n/(n+1)]$.

### Les premiers événements

| $n$ | Événement $A_n$ | $\mathbb P(A_n)$ |
|---:|---|---:|
| $1$ | $\{X\leq\frac12\}$ | $\frac12$ |
| $2$ | $\{X\leq\frac23\}$ | $\frac23$ |
| $3$ | $\{X\leq\frac34\}$ | $\frac34$ |
| $4$ | $\{X\leq\frac45\}$ | $\frac45$ |

Les seuils vérifient

$$
\frac12<\frac23<\frac34<\frac45<\cdots<1.
$$

Par conséquent,

$$
A_1\subseteq A_2\subseteq A_3\subseteq\cdots.
$$

La suite $(A_n)_n$ est donc bien croissante.

### Quelle est l'union des $A_n$ ?

Toute valeur $x\in[0,1)$ finit par être inférieure à l'un des seuils $n/(n+1)$. Par exemple,

$$
x=0{,}9=\frac9{10}
$$

vérifie le seuil de $A_9=\{X\leq9/10\}$. Autrement dit, si $X(\omega)=0{,}9$, alors $\omega\in A_9$.

En revanche, la valeur $1$ n'appartient à aucun de ces domaines, car, pour tout entier fini $n$,

$$
\frac{n}{n+1}<1.
$$

Ainsi,

$$
\boxed{\bigcup_{n\geq1}A_n=\{X<1\}}.
$$

L'union n'est donc pas l'événement $\{X\leq1\}$ : le point $1$ n'est jamais atteint par les seuils, même s'ils s'en approchent arbitrairement.

### Calcul des probabilités

Pour une loi uniforme sur $[0,1]$,

$$
\mathbb P(X\leq t)=t,
\qquad 0\leq t\leq1.
$$

Par conséquent,

$$
\mathbb P(A_n)
=\mathbb P\!\left(X\leq\frac{n}{n+1}\right)
=\frac{n}{n+1}.
$$

Lorsque $n$ tend vers l'infini,

$$
\lim_{n\to\infty}\mathbb P(A_n)
=\lim_{n\to\infty}\frac{n}{n+1}
=1.
$$

D'autre part, la loi uniforme est continue, donc un point isolé a une probabilité nulle :

$$
\mathbb P(X=1)=0.
$$

Comme $X$ prend ses valeurs dans $[0,1]$,

$$
\mathbb P(X<1)
=1-\mathbb P(X=1)
=1.
$$

On retrouve finalement la continuité croissante :

$$
\boxed{
\lim_{n\to\infty}\mathbb P(A_n)
=1
=\mathbb P(X<1)
=\mathbb P\!\left(\bigcup_{n\geq1}A_n\right)
}.
$$

## 8. Portée du résultat

La preuve ne dépend pas du fait que la masse totale vaut $1$. Pour toute mesure positive $\mu$, même infinie,

$$
A_n\uparrow A
\quad\Longrightarrow\quad
\mu(A_n)\uparrow\mu(A).
$$

Aucune hypothèse de finitude n'est requise pour la continuité croissante. La limite peut éventuellement valoir $+\infty$.

> [!warning] À ne pas confondre avec le cas décroissant
> Si $A_n\downarrow A$, l'égalité
> $$
> \mu(A_n)\downarrow\mu(A)
> $$
> demande en général que $\mu(A_0)<\infty$. Cette condition est automatique pour une probabilité.

## À retenir

La preuve suit quatre mouvements :

$$
\boxed{
A_n\text{ emboîtés}
\longrightarrow
B_n\text{ disjoints}
\longrightarrow
\text{sommes partielles}
\longrightarrow
\text{limite de la série}
}
$$

Le passage final peut se résumer sans ellipse par

$$
\lim_{N\to\infty}\mathbb P(A_N)
=\lim_{N\to\infty}\sum_{n=0}^{N}\mathbb P(B_n)
=\sum_{n=0}^{\infty}\mathbb P(B_n)
=\mathbb P(A).
$$

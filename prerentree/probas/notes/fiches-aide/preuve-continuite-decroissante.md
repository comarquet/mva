---
title: Continuité décroissante d'une probabilité - preuve
aliases:
  - Preuve de la continuité décroissante
  - Continuité par le haut
tags:
  - mva
  - probabilites
  - mesure
  - preuve
parent: "[[cours-principal]]"
---

# Continuité décroissante d'une probabilité - preuve

> [!abstract] Théorème
> Soit $(A_n)_{n\in\mathbb N}$ une suite décroissante d'événements :
> $$
> A_0\supseteq A_1\supseteq A_2\supseteq\cdots.
> $$
> Si
> $$
> A=\bigcap_{n\in\mathbb N}A_n,
> $$
> alors
> $$
> \boxed{\mathbb P(A)=\lim_{n\to\infty}\mathbb P(A_n)}.
> $$

Cette propriété est appelée **continuité décroissante** ou **continuité par le haut**.

Retour au cours : [[cours-principal#Suite décroissante d'événements|Continuité des probabilités - suite décroissante]].

La preuve utilise la [[preuve-continuite-croissante|continuité croissante]] appliquée aux événements complémentaires.

## 1. Ce que l'on sait déjà sur la limite

Puisque la suite d'événements est décroissante,

$$
A_{n+1}\subseteq A_n.
$$

Par monotonie de la probabilité,

$$
\mathbb P(A_{n+1})\leq\mathbb P(A_n).
$$

La suite réelle $(\mathbb P(A_n))_n$ est donc décroissante. Elle est minorée par $0$, puisque toute probabilité est positive. Elle possède par conséquent une limite $L\in[0,1]$ :

$$
L=\lim_{n\to\infty}\mathbb P(A_n).
$$

> [!important]
> La décroissance prouve que la limite existe. Il reste à montrer que cette limite est précisément $\mathbb P(A)$.

## 2. Passer aux complémentaires

Posons

$$
B_n=A_n^c.
$$

Le passage au complémentaire inverse les inclusions. De

$$
A_{n+1}\subseteq A_n,
$$

on déduit

$$
A_n^c\subseteq A_{n+1}^c,
$$

c'est-à-dire

$$
B_n\subseteq B_{n+1}.
$$

La suite $(B_n)_n$ est donc croissante :

$$
B_0\subseteq B_1\subseteq B_2\subseteq\cdots.
$$

> [!intuition]
> Lorsque les $A_n$ perdent progressivement des éléments, leurs complémentaires $B_n$ gagnent exactement ces éléments.

## 3. Identifier l'union des complémentaires

Les [[preuve-lois-de-de-morgan|lois de De Morgan]] donnent

$$
\bigcup_{n\in\mathbb N}A_n^c
=\left(\bigcap_{n\in\mathbb N}A_n\right)^c.
$$

Comme $B_n=A_n^c$ et $A=\bigcap_n A_n$, cela devient

$$
\boxed{\bigcup_{n\in\mathbb N}B_n=A^c}.
$$

Cette égalité peut aussi être vérifiée point par point :

$$
\begin{aligned}
\omega\in\bigcup_{n\in\mathbb N}B_n
&\iff \exists n,\ \omega\in A_n^c\\
&\iff \exists n,\ \omega\notin A_n\\
&\iff \omega\notin\bigcap_{n\in\mathbb N}A_n\\
&\iff \omega\in A^c.
\end{aligned}
$$

## 4. Appliquer la [[preuve-continuite-croissante|continuité croissante]]

La suite $(B_n)_n$ est croissante et son union vaut $A^c$. La [[preuve-continuite-croissante|continuité croissante]] donne donc

$$
\boxed{
\lim_{n\to\infty}\mathbb P(B_n)
=\mathbb P(A^c)
}.
$$

En revenant aux $A_n$,

$$
\lim_{n\to\infty}\mathbb P(A_n^c)
=\mathbb P(A^c).
$$

## 5. Développer précisément le passage à la limite

Pour tout événement $C$,

$$
\mathbb P(C^c)=1-\mathbb P(C).
$$

On peut donc remplacer les probabilités des complémentaires :

$$
\mathbb P(A_n^c)=1-\mathbb P(A_n)
$$

et

$$
\mathbb P(A^c)=1-\mathbb P(A).
$$

L'égalité obtenue à l'étape précédente devient

$$
\lim_{n\to\infty}\big(1-\mathbb P(A_n)\big)
=1-\mathbb P(A).
$$

La limite commute avec la soustraction par une constante :

$$
\lim_{n\to\infty}\big(1-\mathbb P(A_n)\big)
=1-\lim_{n\to\infty}\mathbb P(A_n).
$$

Ainsi,

$$
1-\lim_{n\to\infty}\mathbb P(A_n)
=1-\mathbb P(A).
$$

En soustrayant $1$ des deux côtés, puis en multipliant par $-1$,

$$
\boxed{
\lim_{n\to\infty}\mathbb P(A_n)
=\mathbb P(A)
}.
$$

La chaîne complète est donc

$$
\begin{aligned}
1-\lim_{n\to\infty}\mathbb P(A_n)
&=\lim_{n\to\infty}\big(1-\mathbb P(A_n)\big)\\
&=\lim_{n\to\infty}\mathbb P(A_n^c)\\
&=\mathbb P\!\left(\bigcup_{n\in\mathbb N}A_n^c\right)\\
&=\mathbb P(A^c)\\
&=1-\mathbb P(A).
\end{aligned}
$$

## 6. Exemple concret

Considérons l'espace probabilisé uniforme $([0,1],\mathcal B([0,1]),\mathbb P)$ et les événements

$$
A_n=\left[0,\frac{1}{n+1}\right],
\qquad n\in\mathbb N.
$$

Les premiers ensembles sont

$$
\begin{aligned}
A_0&=[0,1],\\
A_1&=\left[0,\frac12\right],\\
A_2&=\left[0,\frac13\right],\\
A_3&=\left[0,\frac14\right].
\end{aligned}
$$

Ils sont emboîtés dans le sens décroissant :

$$
A_0\supseteq A_1\supseteq A_2\supseteq\cdots.
$$

### Quelle est leur intersection ?

Le point $0$ appartient à tous les $A_n$. En revanche, tout nombre $x>0$ finit par être exclu : il suffit de choisir $n$ assez grand pour que

$$
\frac{1}{n+1}<x.
$$

Par conséquent,

$$
\boxed{\bigcap_{n\in\mathbb N}A_n=\{0\}}.
$$

### Comparer les probabilités

Sous la loi uniforme sur $[0,1]$, la probabilité d'un intervalle est sa longueur. Ainsi,

$$
\mathbb P(A_n)=\frac{1}{n+1}.
$$

Donc

$$
\lim_{n\to\infty}\mathbb P(A_n)
=\lim_{n\to\infty}\frac{1}{n+1}
=0.
$$

Le singleton $\{0\}$ a lui aussi une probabilité nulle :

$$
\mathbb P(\{0\})=0.
$$

On retrouve bien

$$
\boxed{
\lim_{n\to\infty}\mathbb P(A_n)
=0
=\mathbb P\!\left(\bigcap_{n\in\mathbb N}A_n\right)
}.
$$

> [!note]
> L'intersection n'est pas vide : elle contient $0$. Sa probabilité est néanmoins nulle, car un singleton a une probabilité nulle sous une loi continue.

## 7. Version pour une mesure générale

Soit $\mu$ une mesure positive et supposons

$$
A_n\downarrow A
\qquad\text{et}\qquad
\mu(A_0)<\infty.
$$

Pour éviter d'utiliser le complémentaire dans tout l'univers, qui pourrait avoir une mesure infinie, posons

$$
C_n=A_0\setminus A_n.
$$

Comme les $A_n$ décroissent, les $C_n$ croissent. De plus, par les [[preuve-lois-de-de-morgan|lois de De Morgan]],

$$
\bigcup_{n\in\mathbb N}C_n
=A_0\setminus\bigcap_{n\in\mathbb N}A_n
=A_0\setminus A.
$$

La [[preuve-continuite-croissante|continuité croissante]] donne

$$
\lim_{n\to\infty}\mu(C_n)
=\mu(A_0\setminus A).
$$

Puisque $A_n\subseteq A_0$, et puisque $\mu(A_0)$ est finie,

$$
\mu(C_n)=\mu(A_0)-\mu(A_n).
$$

De même,

$$
\mu(A_0\setminus A)=\mu(A_0)-\mu(A).
$$

Ainsi,

$$
\mu(A_0)-\lim_{n\to\infty}\mu(A_n)
=\mu(A_0)-\mu(A),
$$

d'où

$$
\boxed{\lim_{n\to\infty}\mu(A_n)=\mu(A)}.
$$

## 8. Pourquoi l'hypothèse de finitude est-elle nécessaire ?

Considérons $\mathbb N$ muni de la mesure de comptage $\mu$, définie par le nombre d'éléments d'un ensemble. Posons

$$
A_n=\{n,n+1,n+2,\ldots\}.
$$

La suite est décroissante et

$$
\bigcap_{n\in\mathbb N}A_n=\varnothing,
$$

car aucun entier n'appartient à tous les $A_n$. Pourtant, chaque $A_n$ est infini, donc

$$
\mu(A_n)=+\infty
$$

pour tout $n$, tandis que

$$
\mu\!\left(\bigcap_{n\in\mathbb N}A_n\right)
=\mu(\varnothing)
=0.
$$

On obtient donc

$$
\lim_{n\to\infty}\mu(A_n)=+\infty\neq0=\mu(A).
$$

La continuité décroissante échoue ici parce que

$$
\mu(A_0)=+\infty.
$$

> [!warning] Le problème avec $+\infty$
> Sans l'hypothèse $\mu(A_0)<\infty$, la preuve conduirait à des expressions du type
> $$
> +\infty-\mu(A_n),
> $$
> et notamment à la forme indéterminée $+\infty-(+\infty)$.

## À retenir

Pour une probabilité,

$$
\boxed{
A_n\downarrow A
\Longrightarrow
A_n^c\uparrow A^c
\Longrightarrow
\mathbb P(A_n^c)\uparrow\mathbb P(A^c)
\Longrightarrow
\mathbb P(A_n)\downarrow\mathbb P(A)
}.
$$

Pour une mesure générale,

$$
\boxed{
A_n\downarrow A
\quad\text{et}\quad
\mu(A_0)<\infty
\Longrightarrow
\mu(A_n)\downarrow\mu(A)
}.
$$

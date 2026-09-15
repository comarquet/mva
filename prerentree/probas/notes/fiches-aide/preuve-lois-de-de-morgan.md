---
title: Lois de De Morgan et stabilité par intersection
aliases:
  - Stabilité par intersection dénombrable
  - De Morgan pour une tribu
tags:
  - mva
  - probabilites
  - tribu
  - theorie-des-ensembles
parent: "[[cours-principal]]"
---

# Lois de De Morgan et stabilité par intersection

> [!abstract] Résultat
> Si $\mathcal F$ est une tribu et si $A_n\in\mathcal F$ pour tout $n\in\mathbb N$, alors
> $$
> \bigcap_{n\in\mathbb N}A_n\in\mathcal F.
> $$
> La stabilité par intersection dénombrable n'est pas un nouvel axiome : elle découle de la stabilité par complémentaire et par union dénombrable.

Retour au cours : [[cours-principal#Propriétés déduites|Propriétés déduites d'une tribu]].

## La formule à comprendre

Les lois de De Morgan donnent

$$
\boxed{
\bigcap_{n\in\mathbb N}A_n
=\left(\bigcup_{n\in\mathbb N}A_n^c\right)^c
}.
$$

Cette égalité se lit ainsi :

> « Appartenir à tous les $A_n$ » équivaut à « ne manquer aucun des $A_n$ ».

Or « manquer au moins un des $A_n$ » signifie appartenir à l'union des complémentaires $A_n^c$. Il faut donc prendre le complémentaire de cette union.

## Démonstration décomposée

Supposons que

$$
\forall n\in\mathbb N,\qquad A_n\in\mathcal F.
$$

### Étape 1 - Prendre chaque complémentaire

Une tribu est stable par complémentaire. Pour chaque $n$,

$$
A_n\in\mathcal F
\quad\Longrightarrow\quad
A_n^c\in\mathcal F.
$$

On obtient donc une nouvelle suite d'événements mesurables :

$$
(A_n^c)_{n\in\mathbb N}.
$$

### Étape 2 - Réunir les complémentaires

Une tribu est stable par union dénombrable. Par conséquent,

$$
\bigcup_{n\in\mathbb N}A_n^c\in\mathcal F.
$$

Cette union est l'événement suivant :

> « Il existe au moins un indice $n$ pour lequel $A_n$ ne se réalise pas. »

En logique,

$$
\omega\in\bigcup_{n\in\mathbb N}A_n^c
\quad\Longleftrightarrow\quad
\exists n\in\mathbb N,\ \omega\notin A_n.
$$

### Étape 3 - Prendre une nouvelle fois le complémentaire

La stabilité par complémentaire donne

$$
\left(\bigcup_{n\in\mathbb N}A_n^c\right)^c\in\mathcal F.
$$

Ce complémentaire correspond à l'événement :

> « Il n'existe aucun indice $n$ pour lequel $A_n$ ne se réalise pas. »

Autrement dit,

$$
\forall n\in\mathbb N,\qquad \omega\in A_n.
$$

### Étape 4 - Reconnaître l'intersection

Dire que $\omega$ appartient à chaque $A_n$ revient exactement à dire que $\omega$ appartient à leur intersection :

$$
\omega\in\bigcap_{n\in\mathbb N}A_n.
$$

On a donc montré à la fois que

$$
\bigcap_{n\in\mathbb N}A_n
=\left(\bigcup_{n\in\mathbb N}A_n^c\right)^c
$$

et que cet ensemble appartient à $\mathcal F$. Finalement,

$$
\boxed{\bigcap_{n\in\mathbb N}A_n\in\mathcal F}.
$$

## Preuve ponctuelle de l'égalité de De Morgan

Pour vérifier rigoureusement que deux ensembles sont égaux, on montre qu'un élément quelconque appartient au premier si et seulement s'il appartient au second :

$$
\begin{aligned}
\omega\in\left(\bigcup_{n\in\mathbb N}A_n^c\right)^c
&\iff \omega\notin\bigcup_{n\in\mathbb N}A_n^c\\
&\iff \forall n\in\mathbb N,\ \omega\notin A_n^c\\
&\iff \forall n\in\mathbb N,\ \omega\in A_n\\
&\iff \omega\in\bigcap_{n\in\mathbb N}A_n.
\end{aligned}
$$

## Traduction logique

Les opérations ensemblistes correspondent à des connecteurs logiques :

| Ensembles | Logique |
|---|---|
| $\omega\in A^c$ | « $\omega\in A$ » est faux |
| $\omega\in\bigcup_n A_n$ | il existe $n$ tel que $\omega\in A_n$ |
| $\omega\in\bigcap_n A_n$ | pour tout $n$, $\omega\in A_n$ |

La formule de De Morgan traduit alors la négation d'un quantificateur :

$$
\neg(\exists n,\ \omega\notin A_n)
\quad\Longleftrightarrow\quad
\forall n,\ \omega\in A_n.
$$

## Exemple concret

On lance indéfiniment une pièce et l'on pose

$$
A_n=\{\text{le }n\text{-ième lancer donne Pile}\}.
$$

Alors

$$
\bigcap_{n\in\mathbb N}A_n
$$

est l'événement « tous les lancers donnent Pile ».

Pour qu'il ne se réalise pas, il suffit qu'au moins un lancer donne Face. Cet événement contraire est

$$
\bigcup_{n\in\mathbb N}A_n^c
=\{\text{au moins un lancer donne Face}\}.
$$

Ainsi,

$$
\{\text{tous les lancers donnent Pile}\}
=\{\text{au moins un lancer donne Face}\}^c.
$$

## Pourquoi parle-t-on d'intersection dénombrable ?

L'axiome d'une tribu garantit la stabilité pour une suite $(A_n)_{n\in\mathbb N}$, donc pour une famille indexée par les entiers. La preuve précédente donne exactement la même portée pour les intersections.

Pour une intersection finie, on peut compléter artificiellement la suite avec $\Omega$ :

$$
A_1\cap\cdots\cap A_k
=A_1\cap\cdots\cap A_k\cap\Omega\cap\Omega\cap\cdots.
$$

La stabilité par intersection dénombrable implique donc la stabilité par intersection finie.

> [!warning] Familles non dénombrables
> Une tribu n'est pas nécessairement stable par une union ou une intersection indexée par une famille non dénombrable. Les axiomes garantissent seulement les opérations finies ou dénombrables.

## À retenir

$$
\boxed{
\text{complémentaire}
+\text{ union dénombrable}
\Longrightarrow
\text{intersection dénombrable}
}
$$

Le mécanisme est toujours le même :

$$
A_n
\longmapsto A_n^c
\longmapsto \bigcup_n A_n^c
\longmapsto \left(\bigcup_n A_n^c\right)^c
=\bigcap_n A_n.
$$

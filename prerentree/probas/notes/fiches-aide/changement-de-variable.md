---
title: Changement de variable
aliases:
  - Méthode de substitution
  - Transformer une densité
tags:
  - mva
  - probabilites
  - analyse
  - densites
up: "[[cours-principal]]"
---

# Changement de variable

> [!abstract] Idée centrale
> On remplace une variable $x$ par une autre variable $y=T(x)$ pour rendre un calcul plus simple. Il faut alors aussi remplacer le différentiel : c'est lui qui corrige l'échelle.

## 1. La méthode générale dans une intégrale

On veut calculer une intégrale en $x$ et on choisit une substitution

$$
y=T(x).
$$

La méthode est toujours la même.

1. **Choisir la nouvelle variable** $y=T(x)$, de façon à simplifier l'expression.
2. **Calculer le différentiel** : $\mathrm dy=T'(x)\,\mathrm dx$, donc
   $$
   \mathrm dx=\frac{\mathrm dy}{T'(x)}.
   $$
3. **Exprimer $x$ en fonction de $y$**, en résolvant $y=T(x)$ : $x=T^{-1}(y)$.
4. **Changer les bornes** si l'intégrale est définie sur un intervalle.
5. Remplacer partout $x$ et $\mathrm dx$ par leurs expressions en $y$, puis intégrer en $y$.

> [!warning] À ne jamais oublier
> Un changement de variable transforme trois choses : l'expression, le différentiel **et** les bornes. Oublier l'une des trois donne presque toujours une réponse fausse.

### Formule compacte

Si $T$ est bijective et dérivable sur l'intervalle considéré, alors

$$
\boxed{\int_a^b g(x)\,\mathrm dx
=\int_{T(a)}^{T(b)}g(T^{-1}(y))\left|(T^{-1})'(y)\right|\,\mathrm dy.}
$$

Le module est particulièrement pratique lorsque $T$ est décroissante : il garantit que l'élément de longueur reste positif.

## 2. Exemple d'intégrale

Calculer

$$
\int_0^1 2x\,e^{x^2}\,\mathrm dx.
$$

On reconnaît la dérivée de $x^2$, donc on pose $y=x^2$.

$$
\mathrm dy=2x\,\mathrm dx.
$$

Les bornes deviennent : $x=0\Rightarrow y=0$ et $x=1\Rightarrow y=1$. Ainsi,

$$
\int_0^1 2x\,e^{x^2}\,\mathrm dx
=\int_0^1e^y\,\mathrm dy
=e-1.
$$

Ici, on n'a pas besoin de calculer l'inverse : le facteur $2x\,\mathrm dx$ est déjà exactement $\mathrm dy$.

## 3. Application aux densités

Supposons que $X$ ait une densité $f_X$, et posons

$$
Y=T(X).
$$

Pour trouver la densité de $Y$, on part de la probabilité d'un ensemble $A$ :

$$
\mathbb P(Y\in A)
=\mathbb P(T(X)\in A)
=\mathbb P(X\in T^{-1}(A)).
$$

On écrit alors cette probabilité avec la densité de $X$, puis on fait le changement de variable $y=T(x)$. Si $T$ est bijective sur le support de $X$, on obtient :

$$
\boxed{f_Y(y)=f_X(T^{-1}(y))\left|(T^{-1})'(y)\right|.}
$$

### Recette pour une densité

1. Écrire $Y=T(X)$.
2. Trouver les valeurs de $x$ qui donnent $y$ : résoudre $y=T(x)$.
3. Calculer la dérivée $T'(x)$.
4. Pour chaque solution $x$ de $T(x)=y$, ajouter
   $$
   \frac{f_X(x)}{|T'(x)|}.
   $$
5. Préciser pour quelles valeurs de $y$ la densité est non nulle.

Cette recette donne, dans le cas général,

$$
\boxed{f_Y(y)=\sum_{x:\,T(x)=y}\frac{f_X(x)}{|T'(x)|}.}
$$

La somme signifie : « ajouter une contribution pour chaque antécédent de $y$ ». Si $T$ est injective, il n'y en a qu'un : on retrouve la formule précédente.

## 4. Exemple avec une transformation non affine : $Y=X^2$

Supposons que $X$ ait une densité $f_X$ et posons $Y=X^2$.

Pour $y>0$, il y a deux valeurs de $X$ qui donnent $y$ :

$$
x=\sqrt y\qquad\text{et}\qquad x=-\sqrt y.
$$

Comme $T(x)=x^2$, on a $T'(x)=2x$. Donc

$$
\boxed{
f_Y(y)=\frac{f_X(\sqrt y)+f_X(-\sqrt y)}{2\sqrt y}
\quad(y>0).}
$$

Et $f_Y(y)=0$ pour $y<0$, puisque le carré ne peut pas être négatif.

> [!important] Le piège principal
> La formule $f_Y(y)=f_X(T^{-1}(y))|(T^{-1})'(y)|$ ne s'applique directement que si $T$ est injective. Pour $x^2$, il faut bien additionner les deux branches $\sqrt y$ et $-\sqrt y$.

## 5. Cas affine : un raccourci utile

Pour $Y=aX+b$ avec $a\neq0$, il n'y a qu'un antécédent :

$$
x=\frac{y-b}{a}.
$$

La formule générale devient donc

$$
\boxed{f_Y(y)=\frac1{|a|}f_X\!\left(\frac{y-b}{a}\right).}
$$

Le passage de $U\sim\mathcal N(0,1)$ à $\sigma U+m$ dans [[exercices-corriges#Exercice 4 - Lois normales|l'exercice 4 du TD]] est exactement ce cas particulier.

Si $a=0$, $Y=b$ est constante : elle n'a pas de densité par rapport à Lebesgue.

## 6. Quand est-ce utilisé dans tes notes ?

- [[exercices-corriges#Exercice 4 - Lois normales|Exercice 4 du TD]] : changement de variable pour obtenir la densité de $\sigma U+m$, puis de $aX+b$.
- [[cours-principal#8. Mesure de Lebesgue et densités|Cours — densités]] : c'est le cadre qui permet d'écrire les probabilités sous forme d'intégrales.
- [[cours-principal#13. Loi d'une variable aléatoire|Cours — loi image]] : c'est l'identité de départ $\mathbb P(T(X)\in A)=\mathbb P(X\in T^{-1}(A))$.
- [[cours-principal#Transformation affine|Cours — transformation affine]] : même idée, vue au niveau des fonctions caractéristiques.
- [[exercices-corriges#Exercice 14 - Indépendance et vecteurs gaussiens|Exercice 14]] : $(X,Y)\mapsto(X+Y,X-Y)$ est un changement de variables en deux dimensions ; le TD utilise une autre méthode, les fonctions caractéristiques.

## 7. Vérification rapide avant de finir

- Ai-je changé les bornes ?
- Ai-je remplacé correctement $\mathrm dx$ ?
- Ai-je indiqué le support de la nouvelle variable ?
- Si $T$ n'est pas injective, ai-je compté tous les antécédents ?
- La densité finale est-elle positive et son intégrale vaut-elle $1$ ?

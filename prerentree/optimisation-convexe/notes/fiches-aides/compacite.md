---
title: Compacité - intuition et usage en optimisation
aliases:
  - Compact
  - Ensemble compact
tags:
  - mva
  - topologie
  - analyse
  - optimisation-convexe
---

# Compacité

> [!summary] À retenir en premier
> Dans $\mathbb R^n$, un ensemble est **compact si et seulement s'il est fermé et borné**. Cette propriété garantit qu'une fonction continue définie sur cet ensemble atteint son minimum et son maximum.

## L'idée intuitive

Un ensemble compact est un ensemble dans lequel une suite de points ne peut pas « disparaître » : si ses points restent dans l'ensemble, on peut toujours en extraire une sous-suite qui converge vers un point encore dans l'ensemble.

Dans $\mathbb R^n$, cela veut dire deux choses très concrètes :

1. L'ensemble ne part pas à l'infini : il est **borné**.
2. Il contient son bord : il est **fermé**.

Ces deux conditions empêchent les deux manières usuelles de perdre une limite.

## Les deux ingrédients dans $\mathbb R^n$

### Être borné : ne pas partir à l'infini

Un ensemble $C\subset\mathbb R^n$ est borné s'il existe un rayon $R>0$ tel que

$$C\subset\{x\in\mathbb R^n:\|x\|\leq R\}.$$

Autrement dit, tous ses points tiennent dans une boule de rayon fini autour de l'origine.

- $[0,1]$ est borné.
- Le disque $\{(x,y):x^2+y^2\leq1\}$ est borné.
- $[0,+\infty)$ et $\mathbb R^n$ ne sont pas bornés.

Une suite comme $x_k=k$ montre ce qui peut mal se passer : elle reste dans $[0,+\infty)$ mais n'a aucune sous-suite qui converge dans $\mathbb R$.

### Être fermé : garder ses points limites

Un ensemble est fermé lorsqu'il contient les limites des suites de ses points. Formellement, si $x_k\in C$ et $x_k\to x$, alors $x\in C$.

- $[0,1]$ est fermé : il contient $0$ et $1$.
- $(0,1)$ n'est pas fermé : $x_k=1/k$ appartient à $(0,1)$ et converge vers $0$, qui n'appartient pas à l'ensemble.
- La boule fermée $\{x:\|x\|\leq1\}$ est fermée ; la boule ouverte $\{x:\|x\|<1\}$ ne l'est pas.

> [!tip] Image mentale
> « Fermé » veut dire que le bord est inclus. « Borné » veut dire que l'ensemble tient dans une boîte de taille finie. Dans $\mathbb R^n$, compact veut dire les deux à la fois.

## Le critère pratique

Le théorème de Heine-Borel donne, en dimension finie,

$$\boxed{C\subset\mathbb R^n\text{ est compact}\quad\Longleftrightarrow\quad C\text{ est fermé et borné}.}$$

Exemples utiles :

- $[0,1]$ est compact : fermé et borné.
- $(0,1)$ n'est pas compact : il est borné, mais non fermé.
- $[0,+\infty)$ n'est pas compact : il est fermé, mais non borné.
- La boule fermée $\{x\in\mathbb R^n:\|x\|\leq1\}$ est compacte.
- La boule ouverte $\{x\in\mathbb R^n:\|x\|<1\}$ n'est pas compacte.
- $\mathbb R^n$ n'est pas compact, car il n'est pas borné.

## Pourquoi cela garantit un minimum

Le théorème de Weierstrass dit : si $C$ est non vide et compact, et si $f:C\to\mathbb R$ est continue, alors il existe $x^*\in C$ tel que

$$f(x^*)=\min_{x\in C}f(x).$$

La différence entre **minimum** et **infimum** est importante :

- l'infimum est la plus petite valeur que l'on peut approcher ;
- le minimum est une valeur réellement atteinte par un point autorisé.

**Exemple.** Sur $[0,1]$, la fonction $f(x)=x$ atteint son minimum : $\min_{x\in[0,1]}x=0$.

Sur $(0,1)$, elle a le même infimum, $\inf_{x\in(0,1)}x=0$, mais pas de minimum : aucun $x>0$ ne vaut $0$. Le problème vient de l'absence du bord $0$ ; l'ensemble n'est pas fermé, donc pas compact.

## Lien avec la projection sur un convexe fermé

Pour projeter un point $x$ sur un ensemble $C$, on minimise la distance au carré :

$$P_C(x)=\operatorname*{argmin}_{y\in C}\frac12\|y-x\|^2.$$

Si $C$ est déjà compact, l'existence de $P_C(x)$ suit directement de Weierstrass, car $y\mapsto\frac12\|y-x\|^2$ est continue.

Mais un ensemble fermé convexe n'est pas forcément borné : par exemple, une droite entière ou un demi-espace. Pour ces ensembles, on utilise le fait que la distance au carré devient très grande lorsque $\|y\|$ devient très grand. Les candidats qui peuvent être minimaux restent donc dans une boule assez grande. L'intersection de cette boule fermée avec $C$ est compacte dans $\mathbb R^n$, ce qui ramène le problème au cas précédent.

> [!warning] Dimension infinie
> La règle « fermé et borné implique compact » est propre à la dimension finie. Dans un espace de Hilbert infini, elle est fausse. Par exemple, une suite de vecteurs unitaires orthogonaux reste bornée mais ne possède pas de sous-suite convergente pour la norme. La projection sur un fermé convexe existe tout de même, mais sa preuve utilise la topologie faible.

## Mini-checklist

Face à un ensemble $C\subset\mathbb R^n$, demande-toi :

1. Peut-on aller arbitrairement loin en restant dans $C$ ? Si oui, il n'est pas borné.
2. Peut-on construire une suite dans $C$ qui tend vers un point exclu ? Si oui, il n'est pas fermé.
3. Si les réponses sont « non », alors $C$ est compact.

[[optimisation-convexe/notes/cours-principal|Retour au cours principal d'optimisation convexe]].

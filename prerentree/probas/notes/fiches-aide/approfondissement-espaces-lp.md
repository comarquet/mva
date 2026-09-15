---
title: Espaces Lp - carte détaillée
aliases:
  - Espaces Lp
  - L1 L2 Linfini
tags:
  - mva
  - probabilites
  - integrabilite
  - espaces-lp
up: "[[cours-principal]]"
---

# Espaces $L^p$ - carte détaillée

> [!abstract] Idée en une phrase
> Dire que $X\in L^p$ signifie que la moyenne de $|X|^p$ est finie :
> $$
> \mathbb E[|X|^p]<\infty.
> $$
> Cela mesure à quel point les grandes valeurs de $X$ sont contrôlées.

Retour au poly : [[cours-principal#18. Espaces $L^p$|section 18 du cours]].

## 1. D'où vient cette notion ?

Une variable aléatoire est une fonction

$$
X:\Omega\longrightarrow\mathbb R.
$$

Pour chaque issue $\omega$, elle produit une valeur $X(\omega)$. Ces valeurs peuvent parfois être très grandes.

La question derrière les espaces $L^p$ est :

> **Les grandes valeurs de $X$ sont-elles assez rares pour que leur moyenne pondérée reste finie ?**

On répond à cette question en calculant

$$
\mathbb E[|X|^p].
$$

Cette expression se lit en trois étapes :

1. on prend la taille de la valeur, $|X|$ ;
2. on élève cette taille à la puissance $p$ ;
3. on fait la moyenne probabiliste avec $\mathbb E$.

## 2. Pourquoi la valeur absolue et la puissance $p$ ?

### La valeur absolue

Sans valeur absolue, les valeurs positives et négatives pourraient se compenser.

Par exemple, une variable peut prendre de très grandes valeurs positives et négatives. La moyenne $\mathbb E[X]$ pourrait sembler nulle par compensation, alors que la taille moyenne de $X$ est énorme.

La quantité

$$
\mathbb E[|X|]
$$

mesure la taille moyenne sans permettre cette compensation.

### La puissance $p$

La puissance détermine la sévérité avec laquelle on pénalise les grandes valeurs.

Pour une valeur $|X|=10$ :

| Puissance | Contribution |
|---:|---:|
| $p=1$ | $10$ |
| $p=2$ | $100$ |
| $p=4$ | $10\,000$ |

Plus $p$ est grand, plus les valeurs extrêmes pèsent lourd dans l'espérance.

## 3. Définition de $L^p$

> [!definition] Espace $L^p$
> Pour $p>0$,
> $$
> L^p(\Omega,\mathcal F,\mathbb P)
> =\left\{X:\Omega\to\mathbb R\text{ mesurable}:
> \mathbb E[|X|^p]<\infty\right\}.
> $$

En pratique, l'appartenance à $L^p$ se teste ainsi :

$$
X\in L^p
\quad\Longleftrightarrow\quad
\mathbb E[|X|^p]<\infty.
$$

> [!important]
> (L^p) ne décrit pas la valeur exacte de (X). Il indique seulement que son moment absolu d'ordre (p) est fini.

## 4. Calculer $\mathbb E[|X|^p]$

### Variable discrète

Si $X$ prend des valeurs $x_1,x_2,\ldots$, alors

$$
\mathbb E[|X|^p]
=\sum_i |x_i|^p\mathbb P(X=x_i).
$$

Ainsi,

$$
X\in L^p
\quad\Longleftrightarrow\quad
\sum_i |x_i|^p\mathbb P(X=x_i)<\infty.
$$

### Variable à densité

Si $X$ possède une densité $f_X$, le théorème de transfert donne

$$
\mathbb E[|X|^p]
=\int_{\mathbb R}|x|^p f_X(x)\,\mathrm dx.
$$

Ainsi,

$$
X\in L^p
\quad\Longleftrightarrow\quad
\int_{\mathbb R}|x|^p f_X(x)\,\mathrm dx<\infty.
$$

> [!note] Lien avec le théorème de transfert
> On applique le théorème de transfert à la fonction $g(x)=|x|^p$ :
> $$
> \mathbb E[g(X)]
> =\int g(x)\,\mathrm d\mathbb P_X(x).
> $$

## 5. Les trois espaces les plus utilisés

### $L^1$ : espérance absolue finie

$$
X\in L^1
\quad\Longleftrightarrow\quad
\mathbb E[|X|]<\infty.
$$

On dit alors que $X$ est **intégrable**.

Cette condition garantit que l'espérance $\mathbb E[X]$ est une quantité réelle finie et bien définie.

> [!example]
> Si $X$ est le résultat d'un dé, alors $|X|\leq6$, donc
> $$
> \mathbb E[|X|]\leq6<\infty.
> $$
> Ainsi, $X\in L^1$.

### $L^2$ : carré intégrable

$$
X\in L^2
\quad\Longleftrightarrow\quad
\mathbb E[X^2]<\infty.
$$

On dit que $X$ est **de carré intégrable**.

Cette condition garantit notamment que la variance est finie :

$$
\operatorname{Var}(X)
=\mathbb E[X^2]-\mathbb E[X]^2<\infty.
$$

L'espace $L^2$ est particulièrement important car il possède un produit scalaire :

$$
\langle X,Y\rangle=\mathbb E[XY].
$$

La quantité

$$
\|X\|_2=\sqrt{\mathbb E[X^2]}
$$

peut donc être interprétée comme une longueur.

### $L^\infty$ : variable essentiellement bornée

On écrit $X\in L^\infty$ s'il existe une constante finie $M$ telle que

$$
\mathbb P(|X|\leq M)=1.
$$

Cela signifie que $|X|$ ne dépasse jamais $M$, sauf éventuellement sur un événement de probabilité nulle.

On définit

$$
\|X\|_\infty
=\operatorname*{ess\,sup}|X|,
$$

c'est-à-dire la plus petite borne valable presque sûrement.

> [!example]
> Pour le résultat $X$ d'un dé,
> $$
> |X|\leq6
> $$
> pour toutes les issues. Donc $X\in L^\infty$ et $\|X\|_\infty=6$.

## 6. « Presque sûrement »

> [!definition] Égalité presque sûre
> Deux variables $X$ et $Y$ sont égales presque sûrement si
> $$
> \mathbb P(X=Y)=1.
> $$
> On note alors $X=Y$ p.s.

Elles peuvent différer sur certaines issues, à condition que l'ensemble de ces issues ait une probabilité nulle.

### Exemple

Supposons que $\Omega=[0,1]$ soit muni de la loi uniforme et posons

$$
X(\omega)=0
$$

pour tout $\omega$, tandis que

$$
Y(\omega)=
\begin{cases}
1000,&\omega=1/2,\\
0,&\omega\neq1/2.
\end{cases}
$$

Le singleton $\{1/2\}$ a une probabilité nulle. Par conséquent,

$$
X=Y\quad\text{presque sûrement}.
$$

Les deux variables ont les mêmes espérances et les mêmes normes $L^p$.

> [!note] Pourquoi identifier ces variables ?
> Les intégrales ne voient pas les modifications effectuées sur un ensemble de probabilité nulle. Du point de vue de $L^p$, $X$ et $Y$ représentent donc le même objet.

## 7. Norme $L^p$

Une **norme** est une façon de mesurer la taille d'un objet. Pour un vecteur de $\mathbb R^n$, par exemple, la norme euclidienne mesure sa longueur. La norme $L^p$ joue le même rôle pour une variable aléatoire : elle mesure sa taille moyenne, en tenant compte de la probabilité de ses différentes valeurs.

Pour $p\geq1$, on définit

$$
\boxed{\|X\|_p
=\left(\mathbb E[|X|^p]\right)^{1/p}}.
$$

### Lire la formule étape par étape

Pour calculer $\|X\|_p$, on suit toujours la même recette :

1. prendre $|X|$ pour ne regarder que la taille de $X$, sans son signe ;
2. élever cette taille à la puissance $p$, donc donner plus de poids aux grandes valeurs ;
3. calculer l'espérance $\mathbb E[|X|^p]$, c'est-à-dire la moyenne pondérée par les probabilités ;
4. prendre la puissance $1/p$, qui ramène le résultat à la même unité que $X$.

> [!example] Exemple numérique
> Supposons que $X$ vaille $1$ ou $3$, avec probabilité $1/2$ pour chaque valeur.
>
> Pour $p=1$,
> $$
> \|X\|_1
> =\mathbb E[|X|]
> =\frac{1+3}{2}
> =2.
> $$
> C'est la taille moyenne de $X$.
>
> Pour $p=2$,
> $$
> \|X\|_2
> =\sqrt{\mathbb E[X^2]}
> =\sqrt{\frac{1^2+3^2}{2}}
> =\sqrt5\simeq2{,}24.
> $$
> La valeur $3$ pèse davantage, car elle est mise au carré avant de faire la moyenne.

### Ce que change le choix de $p$

Plus $p$ est grand, plus la norme réagit fortement aux grandes valeurs de $X$.

| Valeur de $|X|$ | Contribution pour $p=1$ | Contribution pour $p=2$ | Contribution pour $p=4$ |
|---:|---:|---:|---:|
| $2$ | $2$ | $4$ | $16$ |
| $10$ | $10$ | $100$ | $10\,000$ |

Donc :

- $\|X\|_1$ mesure une taille moyenne ;
- $\|X\|_2$ mesure une taille quadratique moyenne et pénalise davantage les valeurs extrêmes ;
- les normes avec $p$ grand sont encore plus sensibles aux valeurs très rares mais très grandes.

À l'autre extrême,

$$
\|X\|_\infty
=\operatorname*{ess\,sup}|X|
$$

est la plus grande taille que $X$ peut prendre, en ignorant les exceptions de probabilité nulle.

### Pourquoi prendre la racine $1/p$ ?

La quantité $\mathbb E[|X|^p]$ seule est bien un indicateur de taille, mais elle est exprimée dans l'unité de $X$ élevée à la puissance $p$.

Si $X$ représente une longueur en mètres, $\mathbb E[X^2]$ est exprimée en mètres carrés. La racine carrée dans $\|X\|_2$ revient à une quantité exprimée en mètres.

Elle assure aussi une propriété naturelle de changement d'échelle :

$$
\boxed{\|aX\|_p=|a|\,\|X\|_p}.
$$

Ainsi, doubler toutes les valeurs de $X$ double sa norme. Sans la racine $1/p$, la quantité $\mathbb E[|X|^p]$ serait multipliée par $|a|^p$, ce qui ne se comporterait pas comme une longueur.

### Les propriétés qui font de cette quantité une norme

Une norme vérifie notamment :

$$
\|X\|_p\geq0,
$$

et l'inégalité triangulaire

$$
\|X+Y\|_p\leq\|X\|_p+\|Y\|_p.
$$

La dernière inégalité dit que la taille du total ne dépasse pas la somme des tailles. C'est l'analogue probabiliste de l'inégalité triangulaire pour les vecteurs.

> [!warning] Cas $0<p<1$
> La formule $(\mathbb E[|X|^p])^{1/p}$ existe encore, mais elle ne définit pas une norme car l'inégalité triangulaire n'est plus satisfaite. On parle de **quasi-norme**.

## 8. Inclusion des espaces $L^p$

Sur un espace probabilisé, si

$$
0<p\leq q\leq\infty,
$$

alors

$$
\boxed{L^q\subseteq L^p}.
$$

Pour $q<\infty$, on a également

$$
\boxed{\|X\|_p\leq\|X\|_q}.
$$

Pour les espaces les plus courants :

$$
L^\infty\subseteq L^2\subseteq L^1.
$$

### Pourquoi le sens de l'inclusion paraît-il inversé ?

La condition $X\in L^q$ avec un grand $q$ est plus exigeante : les grandes valeurs sont élevées à une puissance plus forte.

Par exemple, si $|X|=100$, alors cette valeur contribue :

$$
100
\quad\text{à }\mathbb E[|X|],
$$

mais

$$
10\,000
\quad\text{à }\mathbb E[X^2].
$$

Si cette pénalisation plus sévère reste intégrable, la pénalisation plus faible le sera aussi.

> [!proof]- Preuve de $\|X\|_p\leq\|X\|_q$
> Supposons $0<p\leq q<\infty$. Posons $Y=|X|^q$ et $r=p/q\leq1$.
>
> La fonction $u\mapsto u^r$ est concave sur $\mathbb R_+$. L'inégalité de Jensen donne
> $$
> \mathbb E[Y^r]\leq\mathbb E[Y]^r.
> $$
> Comme $Y^r=|X|^p$,
> $$
> \mathbb E[|X|^p]
> \leq\mathbb E[|X|^q]^{p/q}.
> $$
> En prenant la puissance $1/p$,
> $$
> \|X\|_p\leq\|X\|_q.
> $$

> [!warning] Rôle de la masse totale
> Cette inclusion utilise $\mathbb P(\Omega)=1$. Elle n'est pas vraie en général sur un espace de mesure infinie, par exemple sur $\mathbb R$ muni de la mesure de Lebesgue.

## 9. Exemple où $L^1$ ne suffit pas pour être dans $L^2$

Considérons une variable entière positive telle que

$$
\mathbb P(X=n)=\frac{c}{n^3},
\qquad n\geq1,
$$

où $c>0$ est la constante qui rend la somme des probabilités égale à $1$.

Son moment d'ordre $1$ vaut

$$
\mathbb E[X]
=\sum_{n\geq1}n\frac{c}{n^3}
=c\sum_{n\geq1}\frac1{n^2}<\infty.
$$

Donc $X\in L^1$.

Mais son moment d'ordre $2$ vaut

$$
\mathbb E[X^2]
=\sum_{n\geq1}n^2\frac{c}{n^3}
=c\sum_{n\geq1}\frac1n=+\infty.
$$

Donc $X\notin L^2$.

Cet exemple montre que

$$
L^2\subsetneq L^1.
$$

> [!intuition]
> La variable possède une moyenne finie, mais ses grandes valeurs sont encore trop fréquentes pour que son carré ait une moyenne finie.

## 10. Conséquences utiles dans le cours

### Espérance

Pour manipuler une espérance finie, on demande généralement

$$
X\in L^1.
$$

### Variance

Pour avoir une variance finie, on demande

$$
X\in L^2.
$$

Comme $L^2\subseteq L^1$ sur un espace probabilisé, l'espérance de $X$ est alors automatiquement finie.

### Covariance

Si $X,Y\in L^2$, l'inégalité de Cauchy-Schwarz donne

$$
\mathbb E[|XY|]
\leq\sqrt{\mathbb E[X^2]\mathbb E[Y^2]}<\infty.
$$

La covariance

$$
\operatorname{Cov}(X,Y)
=\mathbb E[XY]-\mathbb E[X]\mathbb E[Y]
$$

est donc bien définie et finie.

## 11. Tableau récapitulatif

| Espace | Condition | Interprétation | Conséquence typique |
|---|---|---|---|
| $L^1$ | $\mathbb E[|X|]<\infty$ | taille moyenne finie | espérance finie |
| $L^2$ | $\mathbb E[X^2]<\infty$ | carré moyen fini | variance finie |
| $L^p$ | $\mathbb E[|X|^p]<\infty$ | moment absolu d'ordre $p$ fini | contrôle des grandes valeurs |
| $L^\infty$ | $|X|\leq M$ p.s. | variable essentiellement bornée | appartient à tous les $L^p$ finis |

## 12. Méthode pour résoudre un exercice

Pour déterminer si $X\in L^p$ :

1. identifier la loi de $X$ ;
2. écrire $\mathbb E[|X|^p]$ comme une somme ou une intégrale ;
3. étudier la convergence de cette somme ou intégrale ;
4. conclure : valeur finie signifie $X\in L^p$, divergence signifie $X\notin L^p$.

## 13. Pièges classiques

- $X\in L^1$ n'implique pas $X\in L^2$.
- Une espérance $\mathbb E[X]$ apparemment compensée ne garantit pas $\mathbb E[|X|]<\infty$.
- Pour $p<1$, $\|X\|_p$ n'est pas une véritable norme.
- « Bornée presque sûrement » autorise des exceptions sur un ensemble de probabilité nulle.
- L'inclusion $L^q\subseteq L^p$ dépend du fait que la mesure totale est finie, ici égale à $1$.

## 14. Questions rapides

> [!question]- Si $X$ est bornée, appartient-elle à $L^2$ ?
> Oui. Si $|X|\leq M$ presque sûrement, alors
> $$
> \mathbb E[X^2]\leq M^2<\infty.
> $$

> [!question]- Si $X\in L^2$, appartient-elle à $L^1$ ?
> Oui, sur un espace probabilisé :
> $$
> \mathbb E[|X|]\leq\sqrt{\mathbb E[X^2]}.
> $$

> [!question]- Si $X\in L^1$, sa variance est-elle nécessairement finie ?
> Non. Il faut en plus que $X\in L^2$.

> [!question]- Deux variables différentes sur une seule issue sont-elles différentes dans $L^p$ ?
> Cela dépend de la probabilité de cette issue. Si elle est nulle, les deux variables représentent le même élément de $L^p$.

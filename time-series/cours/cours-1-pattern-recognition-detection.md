---
title: "Machine Learning for Time Series — Cours 1 : reconnaissance et détection de motifs"
aliases:
  - "Pattern recognition and detection"
tags:
  - mva
  - time-series
  - machine-learning
  - pattern-detection
  - dtw
source: "[[cours-1-pattern-recognition-detection.pdf]]"
---

# Reconnaissance et détection de motifs dans les séries temporelles

> [!abstract] Idée directrice
> Dans une série temporelle longue, on veut reconnaître des formes. Parfois la forme est déjà connue et l'on cherche **où elle apparaît**. Parfois elle est inconnue et l'on cherche **quelles formes se répètent**. Tout le cours repose sur une question préalable : *qu'est-ce que cela veut dire, pour deux portions de signal, d'avoir la même forme ?*

Cette note reconstruit le premier cours de *Machine Learning for Time Series* (Laurent Oudre, MVA 2026-2027). Elle privilégie le sens des objets et des formules. Les notations sont rappelées régulièrement afin que la note reste lisible sans les slides.

## 1. Vue d'ensemble : deux problèmes très proches, mais différents

Une **série temporelle** est une suite de mesures ordonnées dans le temps. Par exemple :

- la puissance électrique consommée par un foyer chaque seconde ;
- le signal d'un électrocardiogramme (ECG) ;
- l'accélération enregistrée par une montre ;
- le nombre de requêtes reçues par un serveur à chaque minute.

Dans beaucoup de signaux réels, certaines formes courtes ont une signification. Sur une courbe de consommation électrique, une forme peut correspondre à l'allumage d'une bouilloire. Sur un ECG, une forme peut correspondre à un battement normal ou à une arythmie. La même forme n'est toutefois presque jamais reproduite à l'identique : elle peut être décalée, plus haute, plus courte, légèrement déformée ou bruitée.

Le cours sépare deux tâches.

| Tâche | Ce qui est connu au départ | Ce que l'on cherche |
| --- | --- | --- |
| **Détection de motifs** (*pattern detection*) | Un ou plusieurs gabarits | Les instants où chaque gabarit apparaît dans un long signal |
| **Extraction de motifs** (*pattern extraction* ou *motif discovery*) | Seulement la ou les séries | Des formes récurrentes, puis leurs occurrences |

La détection est une tâche de reconnaissance : un dictionnaire de formes est fourni. L'extraction est non supervisée : aucune forme n'est fournie, mais on suppose qu'une forme intéressante revient plusieurs fois.

> [!example] Deux lectures d'un ECG
> Si un cardiologue a déjà fourni un exemple de battement anormal, on peut balayer un nouvel ECG pour retrouver ce gabarit : c'est de la **détection**. Si aucun exemple n'est annoté et que l'on veut découvrir automatiquement les formes de battements qui reviennent dans l'enregistrement, c'est de l'**extraction**.

La progression du cours suit naturellement cette dépendance :

1. définir une comparaison entre deux signaux courts ;
2. rendre cette comparaison assez rapide pour balayer un long signal ;
3. l'utiliser pour repérer les répétitions ;
4. apprendre directement un ensemble de motifs à partir du signal.

---

## 2. Préliminaires et notations

### 2.1 Série univariée, série multivariée et fenêtre

On note une série temporelle univariée

$$
x = (x[1],x[2],\ldots,x[N])^\top \in \mathbb{R}^{N}.
$$

Ici :

- $x$ est un **vecteur** réel de longueur $N$ ;
- $N \in \mathbb{N}$ est le nombre d'échantillons ;
- $x[n] \in \mathbb{R}$ est une valeur scalaire, observée à l'indice temporel $n$ ;
- $\top$ indique que l'on écrit le vecteur sous forme de colonne.

La numérotation commence à $1$ dans ce cours. Dans du code Python, elle commencera souvent à $0$ : il faut simplement décaler les indices, sans changer l'idée mathématique.

Une série peut aussi être **multivariée**. À chaque date $n$, on observe alors un vecteur :

$$
x[n] \in \mathbb{R}^{D},
$$

où $D$ est le nombre de canaux. Un accéléromètre à trois axes a par exemple $D=3$. La plupart des formules de la note sont présentées pour $D=1$, car l'intuition est plus nette ; pour $D>1$, on combine en général les écarts sur les canaux.

Une **sous-séquence** ou **fenêtre** de longueur $L$ qui commence en $n$ s'écrit

$$
x[n:n+L-1]
   = (x[n],x[n+1],\ldots,x[n+L-1])^\top \in \mathbb{R}^{L}.
$$

Cette notation désigne un vecteur, pas un seul échantillon. Elle n'est définie que si $1 \leq n \leq N-L+1$.

### 2.2 De « même motif » à « suffisamment similaire »

Un motif n'est pas un objet défini de façon universelle. Dans ce cours, c'est une forme qui réapparaît et que l'on choisit de considérer comme la même malgré certaines variations. Ce choix dépend de l'application.

Deux occurrences d'un même phénomène peuvent différer de plusieurs façons :

| Variation | Forme mathématique indicative | Exemple |
| --- | --- | --- |
| Décalage vertical | $y=x+\beta\mathbf{1}$ | Le capteur a une ligne de base différente |
| Changement d'amplitude | $y=\alpha x$, $\alpha>0$ | Le même geste, effectué plus fortement |
| Dérive linéaire | $y[n]=x[n]+an+b$ | Un capteur chauffe progressivement |
| Décalage temporel | $y[n]=x[n-\tau]$ | Le motif commence un peu plus tard |
| Dilatation ou contraction | durée plus longue ou plus courte | Deux personnes prononcent le même mot à des vitesses différentes |
| Déformation locale du temps | étirements non uniformes | Une phase d'un mouvement dure exceptionnellement longtemps |
| Bruit ou valeurs aberrantes | perturbation additive ponctuelle ou diffuse | Mesure capteur imprécise ou pic isolé |

Dans ce tableau, $\mathbf{1}\in\mathbb{R}^N$ est le vecteur dont toutes les composantes valent $1$, $\beta\in\mathbb{R}$ est un décalage constant, $\alpha\in\mathbb{R}_{>0}$ est un facteur d'échelle, $a,b\in\mathbb{R}$ décrivent une droite, et $\tau$ est un décalage exprimé en nombre d'échantillons.

> [!warning] Le bon invariant est une décision de modélisation
> Une mesure de similarité utile doit ignorer les variations sans intérêt pour le problème, tout en restant sensible aux variations importantes. Si l'amplitude d'un battement ECG porte une information clinique, la supprimer par normalisation peut être une mauvaise idée. Si cette amplitude dépend surtout du gain d'un capteur, l'ignorer est au contraire souhaitable.

Il n'existe pas une distance « correcte » pour toutes les séries temporelles. Le reste du cours compare plusieurs compromis.

---

# Partie I — Comparer deux séries temporelles

## 3. Distance euclidienne : comparer point à point

### 3.1 Quel problème résout-elle ?

On dispose de deux fenêtres de même longueur, par exemple deux vecteurs $x,y\in\mathbb{R}^{N}$. On veut quantifier leur écart en comparant la valeur prise au même instant relatif : le premier point de $x$ avec le premier point de $y$, le deuxième avec le deuxième, et ainsi de suite.

Cette hypothèse est forte : elle suppose que les deux chronologies sont parfaitement synchronisées. Lorsqu'elle est justifiée, la distance euclidienne est simple, interprétable et rapide.

### 3.2 Formule et lecture

La **distance euclidienne** entre $x$ et $y$ est

$$
d_{\mathrm{EUC}}(x,y)
= \sqrt{\sum_{n=1}^{N}\bigl(x[n]-y[n]\bigr)^2}.
$$

Les symboles sont les suivants :

- $x[n]$ et $y[n]$ sont les deux valeurs comparées à l'indice $n$ ;
- $x[n]-y[n]$ est leur erreur ponctuelle ;
- le carré rend chaque contribution positive et pénalise fortement les grosses erreurs ;
- la somme additionne les erreurs sur les $N$ instants ;
- la racine carrée remet le résultat dans l'unité des données.

Cette formule dit essentiellement que l'on mesure la longueur du vecteur d'erreur $x-y$. Une petite valeur signifie que les deux courbes sont proches point par point ; une valeur nulle signifie qu'elles sont exactement identiques.

> [!example] Calcul sur trois points
> Prenons $x=(1,2,3)^\top$ et $y=(1,3,2)^\top$. Les écarts sont $0$, $-1$ et $1$. Ainsi,
>
> $$
> d_{\mathrm{EUC}}(x,y)
> =\sqrt{0^2+(-1)^2+1^2}
> =\sqrt{2}.
> $$
>
> Les deux séries ont les mêmes valeurs, mais dans un ordre temporel différent. La distance n'est pas nulle car la distance euclidienne compare des instants alignés, pas seulement des formes globales.

### 3.3 Ce que la distance euclidienne considère comme différent

La distance euclidienne est sensible :

- à un décalage vertical : si $y=x+\beta\mathbf{1}$, chaque terme diffère de $\beta$ ;
- à une variation d'amplitude : si $y=0.7x$, les valeurs point à point changent ;
- à un décalage temporel, même minuscule : une montée de $x$ peut être comparée à une zone plate de $y$ ;
- à une durée différente et aux déformations de la chronologie ;
- aux valeurs aberrantes, car une erreur isolée élevée est mise au carré.

Un bruit additif blanc de faible amplitude peut être toléré approximativement : il ajoute de petites erreurs réparties dans la somme. Cette robustesse reste limitée, car le carré favorise les erreurs les plus fortes.

> [!intuition] Pourquoi un petit décalage temporel peut coûter cher ?
> Imaginez deux pics très étroits et identiques, dont l'un est décalé d'un seul échantillon. Au sommet du premier pic, le second signal peut déjà être redescendu. La comparaison point à point oppose alors un grand nombre à un petit nombre. La forme visuelle paraît similaire, mais l'alignement imposé ne l'est pas.

### 3.4 Quand l'utiliser ?

La distance euclidienne convient lorsque :

- les fenêtres ont la même longueur ;
- le temps a une signification commune et elles sont bien alignées ;
- les niveaux et amplitudes absolus sont informatifs ou comparables ;
- on souhaite une mesure simple, rapide et munie de bonnes propriétés mathématiques.

La limitation sur les amplitudes et les offsets motive la normalisation. La limitation sur la chronologie motivera ensuite DTW.

---

## 4. Distance euclidienne normalisée : comparer la forme plutôt que le niveau

### 4.1 Le problème : même forme, échelle différente

Supposons qu'un capteur fournisse les signaux $x=(2,4,6)^\top$ et $y=(20,40,60)^\top$. La seconde série est dix fois plus grande, pourtant les deux évoluent exactement de la même manière. Si seule la forme relative compte, la distance euclidienne brute répond à la mauvaise question.

On commence alors par centrer et mettre chaque fenêtre à l'échelle.

### 4.2 Rappel : moyenne et écart-type

Pour une série $x\in\mathbb{R}^{N}$, sa moyenne est

$$
\mu_x=\frac{1}{N}\sum_{n=1}^{N}x[n].
$$

$\mu_x\in\mathbb{R}$ représente son niveau moyen. Son écart-type, avec la convention utilisée dans les slides, est

$$
\sigma_x
=\sqrt{\frac{1}{N}\sum_{n=1}^{N}\bigl(x[n]-\mu_x\bigr)^2}.
$$

$\sigma_x\in\mathbb{R}_{\geq 0}$ mesure la dispersion typique des valeurs autour de leur moyenne. Une série constante a $\sigma_x=0$.

La **z-normalisation** transforme chaque échantillon en

$$
\widetilde{x}[n]
=\frac{x[n]-\mu_x}{\sigma_x}.
$$

Le tilde indique une version normalisée. Soustraire $\mu_x$ déplace la série autour de zéro. Diviser par $\sigma_x$ lui donne une dispersion égale à $1$.

> [!warning] Cas d'une fenêtre presque constante
> Si $\sigma_x=0$, la z-normalisation n'est pas définie. Si $\sigma_x$ est très petit, elle amplifie fortement le moindre bruit : une fenêtre presque plate peut alors prendre l'apparence d'une forme très fluctuante après normalisation. Il faut traiter ce cas explicitement dans une implémentation.

### 4.3 Définition de la distance normalisée

La **distance euclidienne normalisée** est

$$
d_{\mathrm{nEUC}}(x,y)
=\sqrt{\sum_{n=1}^{N}\bigl(\widetilde{x}[n]-\widetilde{y}[n]\bigr)^2}.
$$

Ici, $\widetilde{x},\widetilde{y}\in\mathbb{R}^N$ sont les versions z-normalisées de $x$ et $y$. Cette formule applique exactement la distance euclidienne, mais après avoir retiré le niveau moyen et l'échelle propre à chaque série.

Elle répond à la question : « les variations relatives autour de leur moyenne ont-elles la même forme ? »

> [!example] Invariance à l'offset et à l'amplitude positive
> Supposons que
>
> $$
> y=\alpha x+\beta\mathbf{1},
> \qquad \alpha>0.
> $$
>
> Cette relation se lit composante par composante :
>
> $$
> y[n]=\alpha x[n]+\beta.
> $$
>
> Le scalaire $\alpha$ multiplie toutes les hauteurs du signal par la même quantité. Il étire donc $x$ verticalement sans déplacer ses pics, ses creux ou leurs instants d'apparition. Le scalaire $\beta$ ajoute la même quantité à tous les échantillons : il translate toute la courbe vers le haut si $\beta>0$, ou vers le bas si $\beta<0$. Dans les deux cas, la forme relative est préservée.
>
> Vérifions ce que la z-normalisation retire. La moyenne de $y$ vaut
>
> $$
> \begin{aligned}
> \mu_y
> &=\frac{1}{N}\sum_{n=1}^{N}y[n]\\
> &=\frac{1}{N}\sum_{n=1}^{N}\bigl(\alpha x[n]+\beta\bigr)\\
> &=\alpha\mu_x+\beta.
> \end{aligned}
> $$
>
> Le décalage $\beta$ se retrouve entièrement dans la moyenne. Après centrage, il disparaît :
>
> $$
> y[n]-\mu_y
> =\alpha x[n]+\beta-(\alpha\mu_x+\beta)
> =\alpha\bigl(x[n]-\mu_x\bigr).
> $$
>
> L'écart-type de $y$ devient
>
> $$
> \begin{aligned}
> \sigma_y
> &=\sqrt{\frac{1}{N}\sum_{n=1}^{N}
> \bigl(y[n]-\mu_y\bigr)^2}\\
> &=\sqrt{\frac{1}{N}\sum_{n=1}^{N}
> \alpha^2\bigl(x[n]-\mu_x\bigr)^2}\\
> &=|\alpha|\sigma_x.
> \end{aligned}
> $$
>
> L'hypothèse $\alpha>0$ implique $|\alpha|=\alpha$. La valeur z-normalisée de $y$ est alors
>
> $$
> \widetilde{y}[n]
> =\frac{y[n]-\mu_y}{\sigma_y}
> =\frac{\alpha(x[n]-\mu_x)}{\alpha\sigma_x}
> =\frac{x[n]-\mu_x}{\sigma_x}
> =\widetilde{x}[n].
> $$
>
> Chaque échantillon normalisé est le même dans les deux séries. La distance normalisée est donc
>
> $$
> d_{\mathrm{nEUC}}(x,y)
> =\sqrt{\sum_{n=1}^{N}
> \bigl(\widetilde{x}[n]-\widetilde{y}[n]\bigr)^2}
> =0.
> $$
>
> Exemple numérique : si $x=(2,4,6)^\top$, $\alpha=3$ et $\beta=5$, alors $y=(11,17,23)^\top$. La seconde courbe est trois fois plus haute et décalée de $5$, mais ses valeurs z-normalisées sont exactement celles de $x$.
>
> Si $\alpha<0$, le signal est retourné verticalement. La z-normalisation conserve ce retournement : ce n'est généralement pas la même forme.

### 4.4 Lien avec la corrélation de Pearson

Pour deux fenêtres de même longueur $N$, développer le carré de la distance normalisée donne

$$
d_{\mathrm{nEUC}}^2(x,y)=2N(1-\rho),
$$

où

$$
\rho
= \frac{\sum_{n=1}^{N}x[n]y[n]-N\mu_x\mu_y}
        {N\sigma_x\sigma_y}
$$

est le **coefficient de corrélation de Pearson**.

Dans cette formule :

- $\rho\in[-1,1]$ mesure la similarité linéaire entre les variations de $x$ et de $y$ ;
- $\rho=1$ signifie que les deux séries ont exactement la même forme après mise à l'échelle positive ;
- $\rho=0$ signifie qu'aucune relation linéaire nette ne ressort ;
- $\rho=-1$ correspond à une forme parfaitement inversée.

Cette identité dit que minimiser la distance euclidienne z-normalisée revient exactement à maximiser la corrélation de Pearson. Ce n'est pas seulement une analogie : les deux critères classent les candidats dans le même ordre.

> [!intuition] D'où vient ce lien ?
> Après normalisation, chaque série a une moyenne nulle et une énergie fixée. Dans le développement de $\|\widetilde{x}-\widetilde{y}\|_2^2$, les deux termes d'énergie deviennent constants. Le seul terme qui peut encore distinguer deux candidats est leur produit scalaire, c'est-à-dire la corrélation.

### 4.5 Retirer aussi une dérive linéaire

#### Pourquoi retirer une pente ?

La z-normalisation retire un **décalage constant**. Elle traite bien le cas $y[n]=x[n]+\beta$, où la seconde série est simplement placée plus haut ou plus bas que la première.

Elle ne retire pas une variation qui augmente progressivement avec le temps. Une telle tendance s'écrit

$$
y[n]=x[n]+a_0n+b_0.
$$

Les symboles de cette formule sont les suivants :

- $n\in\{1,\ldots,N\}$ : indice temporel dans une fenêtre de longueur $N$ ;
- $x[n],y[n]\in\mathbb{R}$ : valeurs observées au même indice $n$ ;
- $a_0\in\mathbb{R}$ : pente ajoutée. Quand $n$ augmente d'une unité, le terme $a_0n$ augmente de $a_0$ ;
- $b_0\in\mathbb{R}$ : décalage vertical constant.

Le terme $a_0n+b_0$ est une **tendance affine**. Si $a_0>0$, la ligne de base monte ; si $a_0<0$, elle descend. Deux capteurs ou deux occurrences d'un même phénomène peuvent avoir la même oscillation locale, mais des tendances affines différentes. L'idée est d'estimer, pour chaque fenêtre, la droite qui explique le mieux sa tendance globale, puis de comparer uniquement ce qui reste.

> [!example] Image mentale
> Une suite telle que $(7,9,11,13)$ augmente de $2$ à chaque instant : c'est une droite discrète. La suite $(8,8,10,14)$ contient aussi une hausse globale, mais avec une petite déformation autour de cette droite. Si l'on cherche la déformation, la pente globale ne doit pas dominer la comparaison.

#### Une tendance affine comme vecteur

On introduit deux vecteurs de $\mathbb{R}^{N}$ :

$$
\mathbf{1}
=
\begin{pmatrix}
1\\1\\\vdots\\1
\end{pmatrix}
\in\mathbb{R}^{N},
\qquad
t
=
\begin{pmatrix}
1\\2\\\vdots\\N
\end{pmatrix}
\in\mathbb{R}^{N}.
$$

Le vecteur $\mathbf{1}$ contient uniquement des $1$. Le vecteur $t$ contient les indices temporels. Pour deux scalaires $a,b\in\mathbb{R}$,

$$
\begin{aligned}
at+b\mathbf{1}
&=
a\begin{pmatrix}1\\2\\\vdots\\N\end{pmatrix}
+
b\begin{pmatrix}1\\1\\\vdots\\1\end{pmatrix}\\
&=
\begin{pmatrix}
a+b\\
2a+b\\
\vdots\\
Na+b
\end{pmatrix}.
\end{aligned}
$$

La $n$-ième composante de ce vecteur est

$$
(at+b\mathbf{1})[n]=an+b.
$$

Ainsi, $at+b\mathbf{1}$ représente une droite discrète de pente $a$ et d'ordonnée à l'origine $b$.

L'ensemble de toutes les tendances affines possibles est

$$
\begin{aligned}
A
&=\operatorname{span}\{\mathbf{1},t\}\\
&=\left\{
at+b\mathbf{1}
\;:\;
a\in\mathbb{R},\ b\in\mathbb{R}
\right\}\\
&\subset\mathbb{R}^{N}.
\end{aligned}
$$

La notation $\operatorname{span}\{\mathbf{1},t\}$ signifie « ensemble de toutes les combinaisons linéaires de $\mathbf{1}$ et $t$ ». Le symbole $:$ se lit « tel que ». L'ensemble $A$ est donc un sous-espace vectoriel de $\mathbb{R}^{N}$ contenant exactement les vecteurs qui représentent une tendance affine.

#### Quelle droite est la meilleure approximation du signal ?

Un signal $x\in\mathbb{R}^{N}$ n'est généralement pas exactement dans $A$. On cherche la pente et l'ordonnée à l'origine qui rendent la droite aussi proche que possible de $x$ :

$$
(\widehat a,\widehat b)
=\underset{(a,b)\in\mathbb{R}^{2}}{\operatorname{argmin}}
\sum_{n=1}^{N}
\bigl(x[n]-(an+b)\bigr)^2.
$$

Les chapeaux dans $\widehat a$ et $\widehat b$ indiquent des valeurs **estimées à partir du signal**. Le symbole $\operatorname{argmin}$ renvoie le couple $(a,b)$ qui minimise l'expression ; il ne renvoie pas la valeur minimale elle-même.

Pour chaque $n$, l'expression $x[n]-(an+b)$ est l'écart vertical entre l'observation et la droite candidate. Les carrés rendent ces erreurs positives et donnent davantage de poids aux erreurs importantes. Cette procédure est la régression linéaire par **moindres carrés**.

#### Écriture matricielle et calcul de la projection

On définit la matrice et le vecteur suivants :

$$
B
=
\begin{pmatrix}
1 & 1\\
2 & 1\\
\vdots & \vdots\\
N & 1
\end{pmatrix}
=
\begin{pmatrix}
t & \mathbf{1}
\end{pmatrix}
\in\mathbb{R}^{N\times2},
\qquad
\theta
=
\begin{pmatrix}
a\\b
\end{pmatrix}
\in\mathbb{R}^{2}.
$$

$B$ a $N$ lignes et deux colonnes. Sa première colonne est $t$, sa seconde est $\mathbf{1}$. Le vecteur $\theta$ rassemble les deux paramètres $a$ et $b$.

Le produit matriciel $B\theta$ redonne la droite :

$$
\begin{aligned}
B\theta
&=
\begin{pmatrix}
1 & 1\\
2 & 1\\
\vdots & \vdots\\
N & 1
\end{pmatrix}
\begin{pmatrix}a\\b\end{pmatrix}\\
&=
\begin{pmatrix}
1\cdot a+1\cdot b\\
2\cdot a+1\cdot b\\
\vdots\\
N\cdot a+1\cdot b
\end{pmatrix}\\
&=
at+b\mathbf{1}.
\end{aligned}
$$

La fonction à minimiser peut maintenant s'écrire

$$
\begin{aligned}
J(\theta)
&=\lVert x-B\theta\rVert_2^2\\
&=(x-B\theta)^\top(x-B\theta).
\end{aligned}
$$

Ici, $J:\mathbb{R}^{2}\to\mathbb{R}_{\geq0}$ associe une erreur à chaque droite candidate. Le symbole $\top$ désigne la transposée. La norme euclidienne vérifie

$$
\lVert v\rVert_2^2
=\sum_{n=1}^{N}v[n]^2
=v^\top v
\qquad
\text{pour tout }v\in\mathbb{R}^{N}.
$$

Développons $J$ sans sauter les produits :

$$
\begin{aligned}
J(\theta)
&=(x-B\theta)^\top(x-B\theta)\\
&=\bigl(x^\top-\theta^\top B^\top\bigr)(x-B\theta)\\
&=x^\top x-x^\top B\theta-\theta^\top B^\top x+\theta^\top B^\top B\theta.
\end{aligned}
$$

Les termes $x^\top B\theta$ et $\theta^\top B^\top x$ sont des scalaires. Un scalaire est égal à sa transposée, ce qui donne

$$
\begin{aligned}
x^\top B\theta
&=(x^\top B\theta)^\top\\
&=\theta^\top B^\top x.
\end{aligned}
$$

On peut donc simplifier :

$$
\begin{aligned}
J(\theta)
&=x^\top x-\theta^\top B^\top x-\theta^\top B^\top x+\theta^\top B^\top B\theta\\
&=x^\top x-2\theta^\top B^\top x+\theta^\top B^\top B\theta.
\end{aligned}
$$

Pour minimiser cette fonction quadratique, on utilise le **gradient** par rapport à $\theta$. La notation

$$
\nabla_\theta J(\theta)
=
\begin{pmatrix}
\dfrac{\partial J}{\partial a}\\
\dfrac{\partial J}{\partial b}
\end{pmatrix}
\in\mathbb{R}^{2}
$$

désigne le vecteur des deux dérivées partielles de $J$ : l'une mesure comment l'erreur change lorsque l'on modifie la pente $a$, l'autre lorsqu'on modifie l'ordonnée à l'origine $b$.

Les règles de dérivation matricielle utilisées sont les suivantes :

$$
\nabla_\theta(c^\top\theta)=c,
\qquad
\nabla_\theta(\theta^\top Q\theta)=2Q\theta,
$$

où $c\in\mathbb{R}^{2}$ est un vecteur constant et $Q\in\mathbb{R}^{2\times2}$ une matrice symétrique. La matrice $B^\top B$ est symétrique, elle peut donc jouer le rôle de $Q$. Le gradient de $J$ se calcule terme par terme :

$$
\begin{aligned}
\nabla_\theta J(\theta)
&=\nabla_\theta(x^\top x)
  -\nabla_\theta(2\theta^\top B^\top x)
  +\nabla_\theta(\theta^\top B^\top B\theta)\\
&=0-2B^\top x+2B^\top B\theta.
\end{aligned}
$$

Au minimum, ce gradient est nul. En remplaçant $\theta$ par l'estimateur $\widehat\theta$, les étapes sont :

$$
\begin{aligned}
\nabla_\theta J(\widehat\theta)&=0\\
-2B^\top x+2B^\top B\widehat\theta&=0\\
2B^\top B\widehat\theta&=2B^\top x\\
B^\top B\widehat\theta&=B^\top x.
\end{aligned}
$$

La dernière égalité est appelée **équation normale**. Pour $N\geq2$, les colonnes de $B$ sont indépendantes ; $B^\top B\in\mathbb{R}^{2\times2}$ est alors inversible. On peut multiplier à gauche par $(B^\top B)^{-1}$ :

$$
\begin{aligned}
(B^\top B)^{-1}(B^\top B)\widehat\theta
&=(B^\top B)^{-1}B^\top x\\
I_2\widehat\theta
&=(B^\top B)^{-1}B^\top x\\
\widehat\theta
&=(B^\top B)^{-1}B^\top x.
\end{aligned}
$$

$I_2\in\mathbb{R}^{2\times2}$ est la matrice identité. La première ligne utilise la définition d'une matrice inverse : $(B^\top B)^{-1}(B^\top B)=I_2$.

La droite estimée, écrite comme un vecteur de longueur $N$, vaut alors

$$
\begin{aligned}
\Pi_Ax
&=B\widehat\theta\\
&=B(B^\top B)^{-1}B^\top x.
\end{aligned}
$$

On appelle

$$
\Pi_A
=B(B^\top B)^{-1}B^\top
\in\mathbb{R}^{N\times N}
$$

la **projection orthogonale sur $A$**. Appliqué à $x$, cet opérateur renvoie la tendance affine de $A$ la plus proche de $x$ au sens des moindres carrés.

#### Retirer la tendance : le résidu

Le résidu de $x$, après retrait de sa meilleure tendance affine, est

$$
\begin{aligned}
r_x
&=x-\Pi_Ax\\
&=x-B(B^\top B)^{-1}B^\top x\\
&=I_Nx-\Pi_Ax\\
&=(I_N-\Pi_A)x.
\end{aligned}
$$

Les symboles sont :

- $r_x\in\mathbb{R}^{N}$ : partie de $x$ qui n'est pas expliquée par une droite ;
- $I_N\in\mathbb{R}^{N\times N}$ : matrice identité, qui vérifie $I_Nx=x$ ;
- $I_N-\Pi_A\in\mathbb{R}^{N\times N}$ : opérateur qui retire la composante affine.

Le mot « orthogonale » possède une conséquence utile. Avec le produit scalaire

$$
\langle u,v\rangle
=u^\top v
=\sum_{n=1}^{N}u[n]v[n],
$$

le résidu vérifie

$$
\langle r_x,\mathbf{1}\rangle=0,
\qquad
\langle r_x,t\rangle=0.
$$

La première égalité dit que le résidu n'a plus de composante constante. La seconde dit qu'il n'a plus de composante linéaire. C'est le sens mathématique de « retirer le niveau et la pente ».

> [!example] Exemple exact sur quatre instants
> Posons
>
> $$
> r=
> \begin{pmatrix}1\\-1\\-1\\1\end{pmatrix},
> \qquad
> t=
> \begin{pmatrix}1\\2\\3\\4\end{pmatrix},
> \qquad
> \mathbf{1}=
> \begin{pmatrix}1\\1\\1\\1\end{pmatrix}.
> $$
>
> Le vecteur $r$ ne contient ni constante ni pente, car
>
> $$
> \begin{aligned}
> \langle r,\mathbf{1}\rangle
> &=1-1-1+1\\
> &=0,
> \end{aligned}
> \qquad
> \begin{aligned}
> \langle r,t\rangle
> &=1\cdot1+(-1)\cdot2+(-1)\cdot3+1\cdot4\\
> &=1-2-3+4\\
> &=0.
> \end{aligned}
> $$
>
> Construisons $x=r+2t+5\mathbf{1}$ et $y=r-t+12\mathbf{1}$. En développant :
>
> $$
> \begin{aligned}
> x
> &=
> \begin{pmatrix}1\\-1\\-1\\1\end{pmatrix}
> +2\begin{pmatrix}1\\2\\3\\4\end{pmatrix}
> +5\begin{pmatrix}1\\1\\1\\1\end{pmatrix}\\
> &=
> \begin{pmatrix}8\\8\\10\\14\end{pmatrix},
> \end{aligned}
> \qquad
> \begin{aligned}
> y
> &=
> \begin{pmatrix}1\\-1\\-1\\1\end{pmatrix}
> -\begin{pmatrix}1\\2\\3\\4\end{pmatrix}
> +12\begin{pmatrix}1\\1\\1\\1\end{pmatrix}\\
> &=
> \begin{pmatrix}12\\9\\8\\9\end{pmatrix}.
> \end{aligned}
> $$
>
> Les tendances diffèrent, mais le résidu est le même : $r_x=r_y=r$.

#### Normaliser le résidu et comparer les formes

Après retrait de tendance, deux résidus peuvent avoir la même forme mais des amplitudes différentes. On les met à norme $1$ :

$$
\begin{aligned}
x_{\mathrm{LT}}
&=\frac{r_x}{\lVert r_x\rVert_2}\\
&=\frac{(I_N-\Pi_A)x}{\lVert(I_N-\Pi_A)x\rVert_2}.
\end{aligned}
$$

Le suffixe $\mathrm{LT}$ signifie *linear-trend normalization*. Le dénominateur est un scalaire. La norme du vecteur normalisé vaut bien $1$ :

$$
\begin{aligned}
\lVert x_{\mathrm{LT}}\rVert_2
&=
\left\lVert\frac{r_x}{\lVert r_x\rVert_2}\right\rVert_2\\
&=
\frac{\lVert r_x\rVert_2}{\lVert r_x\rVert_2}\\
&=1.
\end{aligned}
$$

Cette formule n'est définie que si $r_x\neq0$. Un signal qui est exactement une droite a un résidu nul ; sa forme sans tendance ne possède alors pas de direction à normaliser.

La distance après retrait de tendance est

$$
d_{\mathrm{LT}}(x,y)
=\lVert x_{\mathrm{LT}}-y_{\mathrm{LT}}\rVert_2.
$$

Le membre de gauche est la distance recherchée. Le membre de droite compare les deux parties résiduelles, après qu'elles ont été débarrassées de leur niveau, de leur pente et de leur amplitude globale.

#### Vérifier l'invariance, ligne par ligne

Supposons d'abord que l'on ajoute seulement une tendance affine :

$$
y=x+a_0t+b_0\mathbf{1}.
$$

Le vecteur $a_0t+b_0\mathbf{1}$ appartient à $A$. La projection est linéaire et laisse inchangé tout vecteur déjà dans $A$. Ainsi,

$$
\begin{aligned}
\Pi_Ay
&=\Pi_A\bigl(x+a_0t+b_0\mathbf{1}\bigr)\\
&=\Pi_Ax+\Pi_A(a_0t+b_0\mathbf{1})\\
&=\Pi_Ax+a_0t+b_0\mathbf{1}.
\end{aligned}
$$

Le résidu de $y$ est alors

$$
\begin{aligned}
r_y
&=y-\Pi_Ay\\
&=\bigl(x+a_0t+b_0\mathbf{1}\bigr)
  -\bigl(\Pi_Ax+a_0t+b_0\mathbf{1}\bigr)\\
&=x+a_0t+b_0\mathbf{1}
  -\Pi_Ax-a_0t-b_0\mathbf{1}\\
&=x-\Pi_Ax\\
&=r_x.
\end{aligned}
$$

Les résidus sont identiques, ce qui entraîne

$$
x_{\mathrm{LT}}=y_{\mathrm{LT}},
\qquad
d_{\mathrm{LT}}(x,y)=0.
$$

Le même mécanisme ignore aussi un changement d'amplitude positive. Si

$$
y=\alpha x+a_0t+b_0\mathbf{1},
\qquad
\alpha>0,
$$

alors

$$
\begin{aligned}
r_y
&=y-\Pi_Ay\\
&=\alpha x+a_0t+b_0\mathbf{1}
 -\bigl(\alpha\Pi_Ax+a_0t+b_0\mathbf{1}\bigr)\\
&=\alpha x-\alpha\Pi_Ax\\
&=\alpha r_x.
\end{aligned}
$$

La normalisation retire ensuite ce facteur :

$$
\begin{aligned}
y_{\mathrm{LT}}
&=\frac{r_y}{\lVert r_y\rVert_2}\\
&=\frac{\alpha r_x}{\lVert\alpha r_x\rVert_2}\\
&=\frac{\alpha r_x}{\alpha\lVert r_x\rVert_2}\\
&=\frac{r_x}{\lVert r_x\rVert_2}\\
&=x_{\mathrm{LT}}.
\end{aligned}
$$

La troisième ligne utilise la propriété $\lVert\alpha r_x\rVert_2=|\alpha|\lVert r_x\rVert_2$ et l'hypothèse $\alpha>0$.

#### Lien précis avec la z-normalisation

La z-normalisation suit la même idée, mais enlève uniquement l'espace des constantes :

$$
A_{\mathrm{const}}=\operatorname{span}\{\mathbf{1}\}.
$$

La projection sur cet espace vaut

$$
\begin{aligned}
\Pi_{A_{\mathrm{const}}}x
&=\frac{\langle x,\mathbf{1}\rangle}
        {\langle\mathbf{1},\mathbf{1}\rangle}\mathbf{1}\\
&=\frac{\sum_{n=1}^{N}x[n]}
        {\sum_{n=1}^{N}1^2}\mathbf{1}\\
&=\frac{\sum_{n=1}^{N}x[n]}{N}\mathbf{1}\\
&=\mu_x\mathbf{1}.
\end{aligned}
$$

Le résidu est donc $x-\mu_x\mathbf{1}$. Sa norme est reliée à l'écart-type :

$$
\begin{aligned}
\lVert x-\mu_x\mathbf{1}\rVert_2^2
&=\sum_{n=1}^{N}\bigl(x[n]-\mu_x\bigr)^2\\
&=N\left(
\frac{1}{N}\sum_{n=1}^{N}\bigl(x[n]-\mu_x\bigr)^2
\right)\\
&=N\sigma_x^2.
\end{aligned}
$$

Lorsque $\sigma_x>0$,

$$
\begin{aligned}
\frac{x-\mu_x\mathbf{1}}
     {\lVert x-\mu_x\mathbf{1}\rVert_2}
&=\frac{x-\mu_x\mathbf{1}}{\sqrt{N}\sigma_x}\\
&=\frac{1}{\sqrt{N}}
\frac{x-\mu_x\mathbf{1}}{\sigma_x}\\
&=\frac{1}{\sqrt{N}}\widetilde{x}.
\end{aligned}
$$

La normalisation de tendance linéaire reprend donc exactement le mécanisme de la z-normalisation, avec un espace plus grand : au lieu d'enlever seulement le niveau moyen, elle retire aussi la meilleure pente linéaire.

### 4.6 Ce que la normalisation ne résout pas

La distance normalisée devient robuste aux offsets et aux changements d'amplitude positive. Elle reste sensible :

- aux décalages temporels ;
- aux dilatations et contractions ;
- aux déformations locales de vitesse ;
- au bruit et aux valeurs aberrantes ;
- aux fenêtres presque constantes, qui constituent un cas délicat.

Pour relâcher la contrainte « l'instant $n$ doit correspondre à l'instant $n$ », il faut changer la manière même d'aligner les deux séries.

---

## 5. Dynamic Time Warping (DTW) : laisser la chronologie se déformer

### 5.1 Le problème : les mêmes événements, à des vitesses différentes

La distance euclidienne impose l'association

$$
x[n] \longleftrightarrow y[n].
$$

Cela suppose que les deux signaux progressent au même rythme. Or, une personne peut effectuer un geste plus lentement au milieu du mouvement, ou prononcer une syllabe plus longtemps. Une forme peut rester la même tout en occupant des positions temporelles légèrement différentes.

**Dynamic Time Warping** (DTW) cherche un alignement souple entre les échantillons. Il peut associer $x[i_k]$ à $y[j_k]$, où les indices $i_k$ et $j_k$ ne progressent pas forcément à la même vitesse.

### 5.2 Un chemin d'alignement

Soient :

$$
x\in\mathbb{R}^{M}
\quad\text{et}\quad
y\in\mathbb{R}^{N},
$$

deux séries qui peuvent avoir des longueurs différentes $M$ et $N$. Un **chemin d'alignement** est une suite de paires d'indices

$$
P=((i_1,j_1),\ldots,(i_{K_P},j_{K_P})).
$$

Les objets de cette écriture sont :

- $K_P\in\mathbb{N}$ : le nombre de couples visités par le chemin ;
- $i_k\in\{1,\ldots,M\}$ : indice choisi dans $x$ à l'étape $k$ ;
- $j_k\in\{1,\ldots,N\}$ : indice choisi dans $y$ à l'étape $k$ ;
- $(i_k,j_k)$ : l'affirmation que $x[i_k]$ est mis en correspondance avec $y[j_k]$.

On représente ce chemin dans une grille $M\times N$. L'axe horizontal parcourt un signal, l'axe vertical l'autre. La diagonale correspond à l'alignement point à point ordinaire.

Pour éviter des alignements absurdes, DTW impose trois règles.

1. **Bornes** :

   $$
   (i_1,j_1)=(1,1),
   \qquad
   (i_{K_P},j_{K_P})=(M,N).
   $$

   Le chemin commence par les premiers échantillons et termine par les derniers.

2. **Monotonie** :

   $$
   i_{k-1}\leq i_k,
   \qquad
   j_{k-1}\leq j_k.
   $$

   Le temps ne revient jamais en arrière dans l'un ou l'autre signal.

3. **Continuité** : d'une étape à la suivante, on ne peut avancer que d'une case au plus sur chaque axe. Les prédécesseurs possibles de $(i,j)$ sont

   $$
   (i-1,j),\qquad(i,j-1),\qquad(i-1,j-1).
   $$

   Un pas horizontal ou vertical répète temporairement un échantillon d'une série face à plusieurs échantillons de l'autre. Un pas diagonal fait avancer les deux séries ensemble.

> [!example] Interprétation d'un pas non diagonal
> Si le chemin passe de $(i,j)$ à $(i,j+1)$, la valeur $x[i]$ est comparée à $y[j]$, puis à $y[j+1]$. Cela modélise le cas où un court instant de $x$ correspond à une portion plus longue de $y$.

### 5.3 Coût d'un chemin et distance DTW

Pour un chemin $P$, le coût accumulé est

$$
w(P)
=\sum_{k=1}^{K_P}\bigl(x[i_k]-y[j_k]\bigr)^2.
$$

Chaque paire alignée apporte son erreur quadratique. Un chemin qui associe souvent des valeurs semblables a un coût faible.

La distance DTW choisit le meilleur chemin admissible :

$$
d_{\mathrm{DTW}}(x,y)
=\sqrt{\min_{P\in\mathcal{P}}w(P)}.
$$

Ici, $\mathcal{P}$ est l'ensemble des chemins respectant les trois règles précédentes. Cette formule dit essentiellement que DTW cherche l'alignement temporel monotone qui rend les deux signaux aussi proches que possible.

Si $M=N$, que l'on interdit tous les pas hors diagonale et que l'on impose $i_k=j_k=k$, DTW redevient exactement la distance euclidienne. La distance euclidienne est donc un cas particulier qui fixe un seul chemin possible.

### 5.4 Pourquoi une programmation dynamique ?

Énumérer tous les chemins possibles dans une grille devient rapidement impossible. La propriété importante est la suivante : pour atteindre une case $(i,j)$, le dernier pas vient nécessairement de l'une de ses trois cases voisines autorisées.

On définit

$$
C(i,j)
$$

comme le coût minimal d'un chemin admissible allant de $(1,1)$ à $(i,j)$. On définit aussi le coût local

$$
D(i,j)=(x[i]-y[j])^2.
$$

Le coût optimal jusqu'à $(i,j)$ satisfait alors, pour $i,j\geq 2$,

$$
C(i,j)
=D(i,j)+\min\left\{
  C(i-1,j-1),\,
  C(i-1,j),\,
  C(i,j-1)
\right\}.
$$

Cette récurrence additionne l'écart de la dernière paire, $D(i,j)$, au meilleur coût parmi les trois façons autorisées d'arriver à cette paire. Elle ne suppose pas que le chemin précédent soit connu : $C$ a déjà mémorisé son coût optimal.

L'initialisation des bords est

$$
\begin{aligned}
C(1,1)&=D(1,1),\\
C(1,j)&=D(1,j)+C(1,j-1) && \text{pour } j\geq 2,\\
C(i,1)&=D(i,1)+C(i-1,1) && \text{pour } i\geq 2.
\end{aligned}
$$

À la fin,

$$
d_{\mathrm{DTW}}(x,y)=\sqrt{C(M,N)}.
$$

> [!example] Une toute petite matrice de coûts
> Pour atteindre $(2,2)$, le dernier alignement compare $x[2]$ à $y[2]$. Le chemin a pu venir de $(1,1)$, $(1,2)$ ou $(2,1)$. Le meilleur coût est donc
>
> $$
> C(2,2)=(x[2]-y[2])^2+
> \min\{C(1,1),C(1,2),C(2,1)\}.
> $$
>
> Répéter cette opération case après case remplit toute la matrice sans énumérer les chemins.

L'algorithme classique coûte $O(MN)$ en temps, car il remplit une grille de $M\times N$ cases. Il utilise $O(MN)$ mémoire si l'on conserve la matrice entière, notamment pour reconstruire le chemin ; le coût seul peut être calculé avec moins de mémoire en ne conservant que les lignes nécessaires.

### 5.5 Contraindre la déformation : bande de Sakoe-Chiba

DTW très libre peut faire correspondre des portions temporelles excessivement éloignées. Cela augmente le calcul et peut produire un alignement peu crédible.

Si l'on suppose que les chronologies sont proches, on ne calcule que les cases vérifiant

$$
|i-j|\leq\lambda,
$$

où $\lambda\in\mathbb{N}$ est la largeur maximale de décalage autorisée en nombre d'échantillons. Cette région autour de la diagonale s'appelle une **bande de Sakoe-Chiba**.

Une petite valeur de $\lambda$ rend DTW plus rapide et plus strict. Une grande valeur laisse davantage de liberté, mais peut aligner des phases qui ne devraient pas l'être. Le paramètre doit refléter la déformation temporelle jugée plausible.

### 5.6 Atouts et limites

DTW traite beaucoup mieux les dilatations, contractions et déformations locales du temps que la distance euclidienne. Ses points limites restent importants :

- l'alignement forcé du premier et du dernier échantillon gêne les décalages temporels globaux ; des variantes relâchent cette contrainte ;
- DTW est sensible aux offsets et changements d'amplitude, sauf si l'on normalise les fenêtres avant de l'appliquer ;
- DTW est sensible au bruit et aux valeurs aberrantes ;
- sa version classique n'est pas une distance métrique complète : elle est symétrique et nulle sur deux entrées identiques, mais elle ne satisfait pas nécessairement l'inégalité triangulaire ;
- l'opération de minimum sur les chemins rend DTW non différentiable aux changements de chemin optimal. On ne peut pas l'utiliser directement comme perte lisse pour entraîner un réseau neuronal. Des relaxations comme *soft-DTW* remplacent le minimum dur par une version lissée.

> [!summary] Comparaison rapide
>
> | Variation à ignorer | Euclidienne | Euclidienne normalisée | DTW | DTW après normalisation |
> | --- | --- | --- | --- | --- |
> | Offset constant | Non | Oui | Non | Oui |
> | Amplitude positive | Non | Oui | Non | Oui |
> | Décalage temporel | Non | Non | Partiellement | Partiellement |
> | Dilatation / contraction | Non | Non | Oui | Oui |
> | Bruit | Sensible | Sensible | Sensible | Sensible |
> | Valeur aberrante | Sensible | Sensible | Sensible | Sensible |

Le mot « partiellement » pour le décalage temporel rappelle que les extrémités sont encore alignées dans le DTW classique.

---

# Partie II — Détecter un motif connu dans un long signal

## 6. Le profil de distance : balayer toutes les fenêtres

### 6.1 Formulation de la détection

On connaît un motif ou gabarit

$$
p=(p[1],\ldots,p[N_p])^\top\in\mathbb{R}^{N_p},
$$

où $N_p$ est sa longueur. On observe une longue série

$$
x\in\mathbb{R}^{N},
\qquad N>N_p.
$$

Pour savoir si $p$ apparaît à l'instant $n$, on compare $p$ à la fenêtre de $x$ de même longueur :

$$
d[n]
=d\bigl(p,\;x[n:n+N_p-1]\bigr),
\qquad
1\leq n\leq N-N_p+1.
$$

$d[n]\in\mathbb{R}_{\geq0}$ est la distance obtenue pour un début de fenêtre $n$. La suite

$$
d\in\mathbb{R}^{N-N_p+1}
$$

s'appelle le **profil de distance** (*distance profile*).

Cette formule dit essentiellement que l'on fait glisser le gabarit tout au long du signal et que l'on garde une mesure de ressemblance à chaque position. Les occurrences candidates correspondent aux **minima locaux** du profil, car une petite distance signifie une bonne correspondance.

> [!example] Exemple conceptuel
> Si $p$ est la forme d'un pic de bouilloire de durée $30$ secondes et que $x$ couvre une journée, $d[600]$ compare le gabarit aux $30$ secondes commençant à la seconde $600$. Un creux net de $d$ indique que le signal autour de cet instant ressemble à la bouilloire.

Le calcul naïf évalue environ $N$ distances, chacune sur $N_p$ points. Sa complexité est $O(NN_p)$. Elle devient coûteuse pour des séries très longues, ce qui motive les méthodes rapides.

## 7. Profil euclidien rapide : sommes cumulées et FFT

### 7.1 Décomposer le carré de la distance

#### Le problème concret derrière ce calcul

Dans la détection, le motif $p$ reste fixe tandis que l'on le compare à toutes les fenêtres possibles de la longue série $x$. Pour une seule position $n$, calculer une distance euclidienne est simple. La difficulté vient de la répétition de ce même calcul pour $n=1$, puis $n=2$, et ainsi de suite.

L'objectif de cette section est de réécrire le carré de la distance sous une forme qui sépare :

1. ce qui ne change jamais parce que le motif $p$ est fixé ;
2. ce qui dépend seulement de sommes calculées sur une fenêtre de $x$ ;
3. ce qui mesure l'alignement entre le motif et cette fenêtre.

Cette séparation préparera les accélérations des sections suivantes. Pour l'instant, il faut surtout comprendre d'où vient chaque terme.

#### Notations utilisées dans le développement

On considère :

- $x=(x[1],\ldots,x[N])^\top\in\mathbb{R}^{N}$ : la longue série temporelle observée, de longueur $N\in\mathbb{N}$ ;
- $p=(p[1],\ldots,p[N_p])^\top\in\mathbb{R}^{N_p}$ : le motif ou gabarit fixé, de longueur $N_p\in\mathbb{N}$ ;
- $n\in\{1,\ldots,N-N_p+1\}$ : l'indice temporel auquel commence la fenêtre examinée dans $x$ ;
- $i\in\{1,\ldots,N_p\}$ : un indice **local** à l'intérieur du motif ou de la fenêtre. Il ne représente pas directement un instant global de la longue série ;
- $x[n:n+N_p-1]\in\mathbb{R}^{N_p}$ : la fenêtre de $x$ qui commence à $n$ et contient exactement $N_p$ échantillons ;
- $x[n+i-1]$ : le $i$-ième élément de cette fenêtre. Le terme $n+i-1$ convertit l'indice local $i$ en indice global dans $x$ ;
- $d_{\mathrm{EUC}}(u,v)\in\mathbb{R}_{\geq0}$ : distance euclidienne entre deux vecteurs réels $u$ et $v$ de même longueur ;
- $\sum_{i=1}^{N_p}$ : somme des termes obtenus pour tous les indices entiers $i=1,2,\ldots,N_p$.

Pour alléger la lecture, notons temporairement la fenêtre

$$
w_n
=x[n:n+N_p-1]
=\bigl(x[n],x[n+1],\ldots,x[n+N_p-1]\bigr)^\top
\in\mathbb{R}^{N_p}.
$$

$w_n$ est un vecteur de longueur $N_p$. En particulier, pour tout indice local $i\in\{1,\ldots,N_p\}$,

$$
w_n[i]=x[n+i-1].
$$

Cette dernière égalité mérite attention : si $i=1$, elle donne $w_n[1]=x[n]$ ; si $i=N_p$, elle donne $w_n[N_p]=x[n+N_p-1]$. Elle décrit précisément la fenêtre, du premier au dernier échantillon.

#### Commencer par un petit exemple numérique

Prenons un motif de longueur $N_p=3$ et une fenêtre déjà extraite :

$$
p=(1,2,0)^\top,
\qquad
w_n=(1,2,4)^\top.
$$

La distance euclidienne au carré vaut

$$
\begin{aligned}
d_{\mathrm{EUC}}^2(p,w_n)
&=(p[1]-w_n[1])^2
 +(p[2]-w_n[2])^2
 +(p[3]-w_n[3])^2\\
&=(1-1)^2+(2-2)^2+(0-4)^2\\
&=0+0+16\\
&=16.
\end{aligned}
$$

On peut obtenir le même résultat en séparant les trois types de termes :

$$
\begin{aligned}
\sum_{i=1}^{3}w_n[i]^2
&=1^2+2^2+4^2=21,\\
\sum_{i=1}^{3}p[i]^2
&=1^2+2^2+0^2=5,\\
\sum_{i=1}^{3}w_n[i]p[i]
&=1\times1+2\times2+4\times0=5.
\end{aligned}
$$

En les combinant,

$$
\begin{aligned}
\sum_{i=1}^{3}w_n[i]^2
+\sum_{i=1}^{3}p[i]^2
-2\sum_{i=1}^{3}w_n[i]p[i]
&=21+5-2\times5\\
&=16.
\end{aligned}
$$

Le dernier terme est soustrait. Lorsque les grandes valeurs du motif et de la fenêtre tombent aux mêmes positions, leur produit est grand, ce qui diminue la distance. C'est exactement le comportement recherché.

#### Développement général, ligne par ligne

Revenons maintenant à la fenêtre générale $w_n=x[n:n+N_p-1]$. Par définition de la distance euclidienne, la distance au carré entre $p$ et $w_n$ est la somme des écarts au carré :

$$
d_{\mathrm{EUC}}^2(p,w_n)
=\sum_{i=1}^{N_p}\bigl(p[i]-w_n[i]\bigr)^2.
$$

Le membre de gauche est le carré d'un nombre réel non négatif : il quantifie l'écart total entre le motif et la fenêtre. Le membre de droite additionne cet écart pour les $N_p$ positions relatives.

Comme $w_n[i]=x[n+i-1]$, on peut remplacer chaque composante de la fenêtre par l'échantillon correspondant de la longue série :

$$
d_{\mathrm{EUC}}^2\bigl(p,x[n:n+N_p-1]\bigr)
=\sum_{i=1}^{N_p}\bigl(p[i]-x[n+i-1]\bigr)^2.
$$

On applique ensuite, à chaque terme de la somme, l'identité algébrique

$$
(a-b)^2=a^2-2ab+b^2,
$$

avec $a=p[i]$ et $b=x[n+i-1]$. Cela donne d'abord :

$$
\begin{aligned}
d_{\mathrm{EUC}}^2\bigl(p,x[n:n+N_p-1]\bigr)
&=\sum_{i=1}^{N_p}
  \left(
    p[i]^2
    -2\,p[i]\,x[n+i-1]
    +x[n+i-1]^2
  \right).
\end{aligned}
$$

La somme d'une addition est égale à l'addition des sommes. Cette propriété de **linéarité de la somme** permet de séparer les trois termes :

$$
\begin{aligned}
d_{\mathrm{EUC}}^2\bigl(p,x[n:n+N_p-1]\bigr)
&=\sum_{i=1}^{N_p}p[i]^2
  +\sum_{i=1}^{N_p}\bigl(-2\,p[i]\,x[n+i-1]\bigr)\\
&\quad+\sum_{i=1}^{N_p}x[n+i-1]^2.
\end{aligned}
$$

Le facteur constant $-2$ peut sortir de la somme :

$$
\begin{aligned}
d_{\mathrm{EUC}}^2\bigl(p,x[n:n+N_p-1]\bigr)
&=\sum_{i=1}^{N_p}p[i]^2
  -2\sum_{i=1}^{N_p}p[i]\,x[n+i-1]\\
&\quad+\sum_{i=1}^{N_p}x[n+i-1]^2.
\end{aligned}
$$

Enfin, le produit de deux nombres réels est commutatif :

$$
p[i]\,x[n+i-1]
=x[n+i-1]\,p[i].
$$

On peut donc réordonner les trois sommes pour obtenir la forme utilisée par la suite du cours :

$$
\begin{aligned}
d_{\mathrm{EUC}}^2\bigl(p,x[n:n+N_p-1]\bigr)
&=\sum_{i=1}^{N_p}x[n+i-1]^2\\
&\quad+\sum_{i=1}^{N_p}p[i]^2\\
&\quad-2\sum_{i=1}^{N_p}x[n+i-1]p[i].
\end{aligned}
$$

Il ne s'agit pas d'une nouvelle approximation : cette dernière expression est exactement la même distance que la définition de départ. Seule son écriture a changé.

#### Sens des trois sommes

Pour éviter qu'elles deviennent de simples symboles, nommons les trois quantités :

$$
\begin{aligned}
E_x[n]
&=\sum_{i=1}^{N_p}x[n+i-1]^2,\\
E_p
&=\sum_{i=1}^{N_p}p[i]^2,\\
r[n]
&=\sum_{i=1}^{N_p}x[n+i-1]p[i].
\end{aligned}
$$

Dans ces définitions :

- $E_x[n]\in\mathbb{R}_{\geq0}$ est l'**énergie** de la fenêtre commençant à $n$ : c'est la somme de ses valeurs au carré ;
- $E_p\in\mathbb{R}_{\geq0}$ est l'énergie du motif ; elle ne dépend pas de $n$, puisque $p$ est fixé ;
- $r[n]\in\mathbb{R}$ est le **produit scalaire glissant** entre la fenêtre et le motif.

La formule précédente devient plus compacte :

$$
d_{\mathrm{EUC}}^2\bigl(p,x[n:n+N_p-1]\bigr)
=E_x[n]+E_p-2r[n].
$$

Le produit scalaire $r[n]$ peut aussi s'écrire avec la notation $\langle\cdot,\cdot\rangle$ :

$$
r[n]
=\left\langle x[n:n+N_p-1],p\right\rangle.
$$

Pour deux vecteurs $u,v\in\mathbb{R}^{N_p}$, la notation

$$
\langle u,v\rangle
=\sum_{i=1}^{N_p}u[i]v[i]
$$

désigne leur produit scalaire. Dans notre cas, il compare les deux vecteurs position par position et additionne leurs produits.

> [!intuition] Lire $E_x[n]+E_p-2r[n]$
> $E_x[n]$ mesure la taille de la fenêtre observée. $E_p$ mesure la taille du motif et reste constante pendant tout le balayage. $r[n]$ mesure leur alignement. Si motif et fenêtre ont des valeurs de même signe et de grande amplitude aux mêmes positions, $r[n]$ augmente et la distance diminue. Si leurs formes s'opposent, $r[n]$ peut diminuer ou devenir négatif, ce qui augmente la distance.

#### Pourquoi cette décomposition rend le balayage rapide

La formule

$$
d_{\mathrm{EUC}}^2\bigl(p,x[n:n+N_p-1]\bigr)
=E_x[n]+E_p-2r[n]
$$

isole les seules quantités nécessaires à chaque position $n$ :

1. $E_p$ se calcule une fois, puisque le motif $p$ ne bouge pas ;
2. $E_x[n]$ peut être obtenu rapidement pour toutes les fenêtres à l'aide de sommes cumulées, comme l'explique la section 7.2 ;
3. $r[n]$ peut être calculé rapidement pour toutes les positions grâce à une convolution et à la FFT, comme l'explique la section 7.3.

La réécriture ne réduit pas le coût pour une seule fenêtre isolée. Son intérêt apparaît lorsqu'il faut comparer le même motif à un grand nombre de fenêtres : elle révèle les calculs qui peuvent être mutualisés au lieu d'être recommencés intégralement.

### 7.2 Calculer des statistiques glissantes en temps constant

On définit les sommes cumulées, avec $c_x[0]=c_x^{(2)}[0]=0$ :

$$
c_x[t]=\sum_{i=1}^{t}x[i],
\qquad
c_x^{(2)}[t]=\sum_{i=1}^{t}x[i]^2.
$$

Ces deux tableaux se calculent une seule fois en $O(N)$. Une somme sur n'importe quelle fenêtre se récupère par différence :

$$
s_x[n]
=\sum_{i=n}^{n+N_p-1}x[i]^2
=c_x^{(2)}[n+N_p-1]-c_x^{(2)}[n-1].
$$

La moyenne et l'écart-type de cette fenêtre valent

$$
\mu_x[n]
=\frac{c_x[n+N_p-1]-c_x[n-1]}{N_p},
$$

$$
\sigma_x[n]
=\sqrt{\frac{s_x[n]}{N_p}-\mu_x[n]^2}.
$$

Ici, $\mu_x[n]$ et $\sigma_x[n]$ ne sont pas des constantes globales : ils changent avec la position de la fenêtre. La différence de deux sommes cumulées évite de recalculer $N_p$ termes à chaque déplacement d'une case.

### 7.3 Le produit scalaire glissant devient une convolution

Le dernier terme coûteux dans le calcul du profil de distance est le **produit scalaire glissant**

$$
r[n]
=\sum_{i=1}^{N_p}x[n+i-1]p[i],
\qquad
1\leq n\leq N-N_p+1.
$$

Les symboles sont :

- $x=(x[1],\ldots,x[N])^\top\in\mathbb{R}^{N}$ : le long signal ;
- $N\in\mathbb{N}$ : son nombre d'échantillons ;
- $p=(p[1],\ldots,p[N_p])^\top\in\mathbb{R}^{N_p}$ : le motif ;
- $N_p\in\mathbb{N}$ : le nombre d'échantillons du motif, avec $N_p\leq N$ ;
- $n\in\{1,\ldots,N-N_p+1\}$ : début de la fenêtre étudiée ;
- $i\in\{1,\ldots,N_p\}$ : indice à l'intérieur du motif ;
- $r[n]\in\mathbb{R}$ : score obtenu lorsque le motif est placé à $n$.

La fenêtre de $x$ qui commence en $n$ vaut

$$
x[n:n+N_p-1]
=\bigl(x[n],x[n+1],\ldots,x[n+N_p-1]\bigr)^\top
\in\mathbb{R}^{N_p}.
$$

Le membre de gauche est le produit scalaire entre cette fenêtre et le motif. Le membre de droite développe ce produit composante par composante :

$$
\begin{aligned}
\left\langle x[n:n+N_p-1],p\right\rangle
&=x[n]p[1]+x[n+1]p[2]+\cdots+x[n+N_p-1]p[N_p]\\
&=\sum_{i=1}^{N_p}x[n+i-1]p[i]\\
&=r[n].
\end{aligned}
$$

Dans la somme, $n+i-1$ prend successivement les valeurs $n,n+1,\ldots,n+N_p-1$. Un grand $r[n]$ indique que les valeurs du motif tombent, dans l'ensemble, en face de valeurs de même signe dans la fenêtre.

> [!example] Exemple fil rouge : les produits scalaires glissants
> Prenons exactement
>
> $$
> x=(2,1,3,4,0)^\top,
> \qquad
> p=(1,2,-1)^\top.
> $$
>
> L'idée de départ consiste à faire glisser $p$ le long de $x$, puis à calculer un produit scalaire à chaque position. Pour $n=1$, la fenêtre est $(2,1,3)^\top$ :
>
> $$
> \begin{aligned}
> r[1]
> &=2\times1+1\times2+3\times(-1)\\
> &=1.
> \end{aligned}
> $$
>
> Pour $n=2$, la fenêtre est $(1,3,4)^\top$ :
>
> $$
> \begin{aligned}
> r[2]
> &=1\times1+3\times2+4\times(-1)\\
> &=3.
> \end{aligned}
> $$
>
> Pour $n=3$, la fenêtre est $(3,4,0)^\top$ :
>
> $$
> \begin{aligned}
> r[3]
> &=3\times1+4\times2+0\times(-1)\\
> &=11.
> \end{aligned}
> $$
>
> On cherche donc à obtenir exactement
>
> $$
> \boxed{r=(1,3,11)^\top}
> $$
>
> sans refaire ces trois produits scalaires séparément. La convolution va les réunir dans une seule suite de valeurs.

#### Retourner le motif

Passons temporairement à des indices commençant à $0$. Choisissons une longueur $Q\in\mathbb{N}$ telle que $Q\geq N+N_p-1$, puis prolongeons les vecteurs par des zéros. Pour $0\leq q\leq N-1$, posons $\bar{x}[q]=x[q+1]$ ; au-delà, $\bar{x}[q]=0$. Pour $0\leq q\leq N_p-1$, posons $\bar{p}^{\leftarrow}[q]=p[N_p-q]$ ; au-delà, $\bar{p}^{\leftarrow}[q]=0$.

Ici, $\bar{x},\bar{p}^{\leftarrow}\in\mathbb{R}^{Q}$ et $q\in\{0,\ldots,Q-1\}$. La barre signifie « prolongé par des zéros » et l'exposant $\leftarrow$ signifie « ordre renversé ». Ainsi, $\bar{p}^{\leftarrow}[0]=p[N_p]$ et $\bar{p}^{\leftarrow}[N_p-1]=p[1]$.

#### De la convolution au produit scalaire glissant

La **convolution linéaire** de $\bar{x}$ et $\bar{p}^{\leftarrow}$ est la suite $c$ définie par

$$
c[t]
=\sum_{q=0}^{Q-1}
\bar{x}[q]\bar{p}^{\leftarrow}[t-q].
$$

$t\in\mathbb{Z}$ est un indice de sortie et $\mathbb{Z}$ est l'ensemble des entiers relatifs. Par convention, les vecteurs prolongés valent $0$ hors de $\{0,\ldots,Q-1\}$.

Pour un $n$ valide, prenons l'indice $t=N_p+n-2$. Cet indice est la position, dans la sortie de convolution $c$, où se termine l'alignement entre la fenêtre de $x$ qui commence à $n$ et le motif de longueur $N_p$. Avec la numérotation à partir de $0$, la première fenêtre ($n=1$) apparaît donc à $t=N_p-1$ ; chaque décalage de la fenêtre d'une position dans $x$ augmente $t$ d'une unité. Les étapes sont :

$$
\begin{aligned}
c[N_p+n-2]
&=\sum_{q=0}^{Q-1}\bar{x}[q]\bar{p}^{\leftarrow}[N_p+n-2-q]\\
&=\sum_{q=n-1}^{n+N_p-2}
\bar{x}[q]\bar{p}^{\leftarrow}[N_p+n-2-q]\\
&=\sum_{q=n-1}^{n+N_p-2}
x[q+1]p\Bigl[N_p-(N_p+n-2-q)\Bigr]\\
&=\sum_{q=n-1}^{n+N_p-2}x[q+1]p[q-n+2].
\end{aligned}
$$

La deuxième ligne retire les termes nuls. En effet, le second facteur est non nul seulement si

$$
0\leq N_p+n-2-q\leq N_p-1,
$$

ce qui équivaut à

$$
n-1\leq q\leq n+N_p-2.
$$

La troisième ligne utilise la définition des vecteurs prolongés. La quatrième simplifie l'indice :

$$
\begin{aligned}
N_p-(N_p+n-2-q)
&=N_p-N_p-n+2+q\\
&=q-n+2.
\end{aligned}
$$

Posons $i=q-n+2$, ce qui revient à écrire $q=n+i-2$. Quand $q=n-1$, $i=1$ ; quand $q=n+N_p-2$, $i=N_p$. Le changement d'indice donne :

$$
\begin{aligned}
c[N_p+n-2]
&=\sum_{q=n-1}^{n+N_p-2}x[q+1]p[q-n+2]\\
&=\sum_{i=1}^{N_p}x[(n+i-2)+1]p[i]\\
&=\sum_{i=1}^{N_p}x[n+i-1]p[i]\\
&=r[n].
\end{aligned}
$$

On a ainsi établi l'identité importante :

$$
\boxed{r[n]=c[N_p+n-2].}
$$

La convolution contient tous les produits scalaires glissants à des indices consécutifs. Le retournement du motif est précisément ce qui fait apparaître $p[i]$, et non $p[N_p-i+1]$, dans la dernière somme.

> [!example] Vérification complète avec la convolution
> Reprenons $x=(2,1,3,4,0)^\top$ et $p=(1,2,-1)^\top$. La convolution utilise naturellement un indice de la forme $p^{\leftarrow}[t-q]$. C'est la raison géométrique du retournement :
>
> $$
> p=(1,2,-1)^\top
> \qquad\longmapsto\qquad
> p^{\leftarrow}=(-1,2,1)^\top.
> $$
>
> En indices commençant à $0$,
>
> $$
> \begin{aligned}
> x[0]&=2, &x[1]&=1, &x[2]&=3, &x[3]&=4, &x[4]&=0,\\
> p^{\leftarrow}[0]&=-1, &
> p^{\leftarrow}[1]&=2, &
> p^{\leftarrow}[2]&=1.
> \end{aligned}
> $$
>
> Pour $t=2$, les trois termes non nuls sont :
>
> $$
> \begin{aligned}
> c[2]
> &=x[0]p^{\leftarrow}[2]
> +x[1]p^{\leftarrow}[1]
> +x[2]p^{\leftarrow}[0]\\
> &=2\times1+1\times2+3\times(-1)\\
> &=1\\
> &=r[1].
> \end{aligned}
> $$
>
> Pour $t=3$ :
>
> $$
> \begin{aligned}
> c[3]
> &=x[1]p^{\leftarrow}[2]
> +x[2]p^{\leftarrow}[1]
> +x[3]p^{\leftarrow}[0]\\
> &=1\times1+3\times2+4\times(-1)\\
> &=3\\
> &=r[2].
> \end{aligned}
> $$
>
> Pour $t=4$ :
>
> $$
> \begin{aligned}
> c[4]
> &=x[2]p^{\leftarrow}[2]
> +x[3]p^{\leftarrow}[1]
> +x[4]p^{\leftarrow}[0]\\
> &=3\times1+4\times2+0\times(-1)\\
> &=11\\
> &=r[3].
> \end{aligned}
> $$
>
> Ainsi,
>
> $$
> \boxed{r[1]=c[2],\qquad r[2]=c[3],\qquad r[3]=c[4].}
> $$
>
> Ici, $N_p=3$, si bien que $N_p+n-2=3+n-2=n+1$. Les positions utiles de la convolution sont donc exactement $c[2]$, $c[3]$ et $c[4]$.

Visuellement, les trois produits scalaires correspondent aux trois alignements suivants :

$$
\begin{array}{ccccc}
2 & 1 & 3 & 4 & 0\\
1 & 2 & -1 & &
\end{array}
\qquad
\begin{array}{ccccc}
2 & 1 & 3 & 4 & 0\\
& 1 & 2 & -1 &
\end{array}
\qquad
\begin{array}{ccccc}
2 & 1 & 3 & 4 & 0\\
& & 1 & 2 & -1
\end{array}
$$

La règle à retenir est donc :

$$
\boxed{\text{produit scalaire glissant}
=\text{convolution avec le motif retourné}.}
$$

La FFT intervient seulement après cette identification. Elle ne change ni les fenêtres comparées ni les valeurs $r[n]$ ; elle accélère le calcul de la convolution :

$$
x*p^{\leftarrow}
=\operatorname{IFFT}\left(
\operatorname{FFT}(x)\odot
\operatorname{FFT}(p^{\leftarrow})
\right).
$$

On peut résumer mentalement la construction ainsi :

$$
\boxed{\text{faire glisser }p}
\quad\Longrightarrow\quad
\boxed{\text{calculer beaucoup de produits scalaires}}
\quad\Longrightarrow\quad
\boxed{\text{convolution avec }p\text{ retourné}}
\quad\Longrightarrow\quad
\boxed{\text{calcul rapide par FFT}.}
$$

#### Transformer une convolution en produits simples

La FFT calcule la transformée de Fourier discrète. Pour $u=(u[0],\ldots,u[Q-1])^\top\in\mathbb{C}^{Q}$, cette transformée est le vecteur $\widehat{u}\in\mathbb{C}^{Q}$ défini par

$$
\widehat{u}[k]
=\sum_{t=0}^{Q-1}
u[t]\exp\left(-\frac{2\pi\mathrm{i}kt}{Q}\right),
\qquad 0\leq k\leq Q-1.
$$

- $\mathbb{C}$ est l'ensemble des nombres complexes ;
- $k$ est l'indice de fréquence ;
- $\mathrm{i}$ est l'unité imaginaire, vérifiant $\mathrm{i}^2=-1$ ; elle est distincte de l'indice $i$ ;
- $\exp$ est la fonction exponentielle ; pour un réel $\theta$, $\exp(\mathrm{i}\theta)=\cos(\theta)+\mathrm{i}\sin(\theta)$ ;
- le chapeau dans $\widehat{u}$ indique que l'on est dans le domaine fréquentiel.

La transformée inverse est

$$
u[t]
=\frac{1}{Q}\sum_{k=0}^{Q-1}
\widehat{u}[k]\exp\left(\frac{2\pi\mathrm{i}kt}{Q}\right).
$$

Pour utiliser la DFT, on emploie la convolution circulaire

$$
c_Q[t]
=\sum_{q=0}^{Q-1}
\bar{x}[q]\bar{p}^{\leftarrow}[(t-q)\bmod Q].
$$

Le symbole $a\bmod Q$ est le reste de la division entière de $a$ par $Q$, dans $\{0,\ldots,Q-1\}$. Comme $Q\geq N+N_p-1$ et que les vecteurs sont complétés par des zéros, cette convolution circulaire égale la convolution linéaire aux indices utiles.

Le théorème de convolution discret affirme, pour tout $k\in\{0,\ldots,Q-1\}$,

$$
\begin{aligned}
\widehat{c_Q}[k]
&=\sum_{t=0}^{Q-1}c_Q[t]\exp\left(-\frac{2\pi\mathrm{i}kt}{Q}\right)\\
&=\sum_{t=0}^{Q-1}
\left(\sum_{q=0}^{Q-1}
\bar{x}[q]\bar{p}^{\leftarrow}[(t-q)\bmod Q]\right)
\exp\left(-\frac{2\pi\mathrm{i}kt}{Q}\right)\\
&=\widehat{\bar{x}}[k]\widehat{\bar{p}^{\leftarrow}}[k].
\end{aligned}
$$

Après le changement d'indice $s=(t-q)\bmod Q$, le passage final se détaille ainsi :

$$
\begin{aligned}
\widehat{c_Q}[k]
&=\sum_{q=0}^{Q-1}
\bar{x}[q]\exp\left(-\frac{2\pi\mathrm{i}kq}{Q}\right)
\sum_{s=0}^{Q-1}
\bar{p}^{\leftarrow}[s]\exp\left(-\frac{2\pi\mathrm{i}ks}{Q}\right)\\
&=\left(
\sum_{q=0}^{Q-1}
\bar{x}[q]\exp\left(-\frac{2\pi\mathrm{i}kq}{Q}\right)
\right)
\left(
\sum_{s=0}^{Q-1}
\bar{p}^{\leftarrow}[s]\exp\left(-\frac{2\pi\mathrm{i}ks}{Q}\right)
\right)\\
&=\widehat{\bar{x}}[k]\widehat{\bar{p}^{\leftarrow}}[k].
\end{aligned}
$$

La propriété $\exp(a+b)=\exp(a)\exp(b)$ sépare les facteurs qui dépendent de $q$ de ceux qui dépendent de $s$. La deuxième ligne factorise alors le produit de deux sommes ; la troisième reconnaît les deux définitions de DFT.

Si $\odot$ désigne le produit composante par composante, défini par

$$
(a\odot b)[k]=a[k]b[k],
$$

le théorème s'écrit sous forme vectorielle :

$$
\widehat{c_Q}
=\mathrm{FFT}_Q(\bar{x})
\odot
\mathrm{FFT}_Q\bigl(\bar{p}^{\leftarrow}\bigr).
$$

En appliquant la transformée inverse aux deux membres :

$$
\begin{aligned}
c_Q
&=\mathrm{IFFT}_Q\left(
\mathrm{FFT}_Q(\bar{x})
\odot
\mathrm{FFT}_Q\bigl(\bar{p}^{\leftarrow}\bigr)
\right),\\
r[n]
&=c_Q[N_p+n-2],
\qquad 1\leq n\leq N-N_p+1.
\end{aligned}
$$

$\mathrm{FFT}_Q$ et $\mathrm{IFFT}_Q$ sont les algorithmes rapides pour la DFT et son inverse de taille $Q$. Le calcul direct demande environ $(N-N_p+1)N_p$ opérations. Les deux FFT, le produit $\odot$ et la FFT inverse coûtent $O(Q\log Q)$. Comme $Q$ est de l'ordre de $N+N_p$, ce coût devient $O(N\log N)$ lorsque $N_p\leq N$.

> [!intuition] L'idée à retenir
> La convolution ne change pas le produit scalaire recherché : elle range tous ses décalages dans une même suite. La FFT accélère ce calcul groupé en remplaçant une somme de produits décalés dans le temps par un produit simple pour chaque fréquence. Les très petites parties imaginaires parfois visibles en calcul numérique viennent des arrondis : le résultat final est réel, puisque $x$ et $p$ sont réels.

### 7.4 Version normalisée

La recherche euclidienne rapide précédente compare les valeurs absolues du motif et de chaque fenêtre. Elle échoue lorsque le même motif revient à un niveau ou à une amplitude différente. La version normalisée répond à une autre question :

> *La fenêtre et le motif ont-ils la même forme après retrait de leur niveau moyen et de leur échelle propre ?*

L'objectif reste de calculer un score pour **toutes** les fenêtres de $x$, sans z-normaliser chacune d'elles explicitement. Une normalisation explicite demanderait de créer $N-N_p+1$ vecteurs de longueur $N_p$, ce qui annulerait une partie du gain de calcul.

#### Notations locales

Pour un indice de départ

$$
1\leq n\leq N-N_p+1,
$$

on note la fenêtre courante

$$
w_n
=x[n:n+N_p-1]
\in\mathbb{R}^{N_p}.
$$

Les symboles $w_n$, $\mu_{w_n}$ et $\sigma_{w_n}$ désignent respectivement :

- $w_n$ : le vecteur des $N_p$ échantillons de $x$ qui commencent à l'indice $n$ ;
- $\mu_{w_n}\in\mathbb{R}$ : la moyenne de cette fenêtre ;
- $\sigma_{w_n}\in\mathbb{R}_{\geq0}$ : son écart-type ;
- $p\in\mathbb{R}^{N_p}$ : le motif fixe ;
- $\mu_p$ et $\sigma_p$ : la moyenne et l'écart-type du motif.

Les statistiques de la fenêtre sont exactement celles déjà obtenues par sommes cumulées :

$$
\mu_{w_n}=\mu_x[n],
\qquad
\sigma_{w_n}=\sigma_x[n].
$$

Le changement de notation sert seulement à faire apparaître plus clairement que l'on compare ici deux vecteurs courts, $w_n$ et $p$.

#### Définition du score normalisé

La z-normalisation de la fenêtre et du motif est

$$
\widetilde{w_n}[i]
=\frac{w_n[i]-\mu_{w_n}}{\sigma_{w_n}},
\qquad
\widetilde{p}[i]
=\frac{p[i]-\mu_p}{\sigma_p},
\qquad
1\leq i\leq N_p.
$$

L'indice $i$ repère une position **à l'intérieur d'un motif ou d'une fenêtre**, et non un instant absolu dans la longue série $x$ :

- $p[i]$ est le $i$-ième échantillon du motif $p$ ;
- $w_n[i]$ est le $i$-ième échantillon de la fenêtre qui commence en $n$ ;
- puisque $w_n=x[n:n+N_p-1]$, on a précisément

  $$
  w_n[i]=x[n+i-1].
  $$

Par exemple, si $n=100$ et $i=3$, $w_{100}[3]=x[102]$. On compare ainsi $p[3]$, le troisième point du gabarit, à $x[102]$, le troisième point de la fenêtre candidate. Le motif $p$ et la fenêtre $w_n$ ont tous les deux $N_p$ composantes, ce qui rend cette comparaison composante par composante possible.

La distance normalisée à la position $n$ est alors

$$
d_{\mathrm{nEUC}}[n]
=\left\lVert\widetilde{w_n}-\widetilde{p}\right\rVert_2.
$$

Cette formule dit que l'on compare les deux formes après avoir forcé chacune à avoir une moyenne nulle et un écart-type égal à $1$. Une petite valeur indique une forme proche, indépendamment d'un offset ou d'un changement d'amplitude positif.

#### Pourquoi l'énergie d'un vecteur normalisé vaut $N_p$

Ici, l'**énergie** d'un vecteur $v\in\mathbb{R}^{N_p}$ est simplement le carré de sa norme euclidienne :

$$
\lVert v\rVert_2^2
=\sum_{i=1}^{N_p}v[i]^2.
$$

Elle additionne les carrés des composantes. Ce mot vient du traitement du signal, où cette somme joue le rôle d'une énergie discrète.

Prenons le motif normalisé $\widetilde p$. En remplaçant sa définition dans l'énergie, on obtient

$$
\begin{aligned}
\left\lVert\widetilde p\right\rVert_2^2
&=\sum_{i=1}^{N_p}
\left(
\frac{p[i]-\mu_p}{\sigma_p}
\right)^2\\
&=\frac{1}{\sigma_p^2}
\sum_{i=1}^{N_p}\bigl(p[i]-\mu_p\bigr)^2.
\end{aligned}
$$

Or la définition de l'écart-type, avec la convention du cours, est

$$
\sigma_p^2
=\frac{1}{N_p}
\sum_{i=1}^{N_p}\bigl(p[i]-\mu_p\bigr)^2.
$$

En multipliant cette dernière égalité par $N_p$, on trouve

$$
\sum_{i=1}^{N_p}\bigl(p[i]-\mu_p\bigr)^2
=N_p\sigma_p^2.
$$

En la reportant dans le calcul de l'énergie :

$$
\left\lVert\widetilde p\right\rVert_2^2
=\frac{N_p\sigma_p^2}{\sigma_p^2}
=N_p.
$$

Le même raisonnement donne $\lVert\widetilde{w_n}\rVert_2^2=N_p$. Cette propriété est valable lorsque l'écart-type n'est pas nul. Une fenêtre constante ne peut pas être z-normalisée.

Ainsi, pour deux vecteurs z-normalisés de longueur $N_p$,

$$
\left\lVert\widetilde{w_n}\right\rVert_2^2
=
\left\lVert\widetilde{p}\right\rVert_2^2
=N_p.
$$

Développons le carré de la distance :

$$
\begin{aligned}
d_{\mathrm{nEUC}}^2[n]
&=
\left\lVert\widetilde{w_n}-\widetilde{p}\right\rVert_2^2\\
&=
\left\lVert\widetilde{w_n}\right\rVert_2^2
+
\left\lVert\widetilde{p}\right\rVert_2^2
-2\left\langle\widetilde{w_n},\widetilde{p}\right\rangle\\
&=
2N_p-2\left\langle\widetilde{w_n},\widetilde{p}\right\rangle.
\end{aligned}
$$

#### D'où vient la corrélation locale ?

La corrélation de Pearson entre deux vecteurs $a,b\in\mathbb{R}^{N_p}$ est, avec la convention de normalisation utilisée ici,

$$
\operatorname{corr}(a,b)
=
\frac{
\frac{1}{N_p}
\sum_{i=1}^{N_p}\bigl(a[i]-\mu_a\bigr)\bigl(b[i]-\mu_b\bigr)
}{
\sigma_a\sigma_b
}.
$$

Le numérateur est une **covariance** : il est grand et positif lorsque les deux vecteurs sont simultanément au-dessus ou simultanément au-dessous de leurs moyennes. Les dénominateurs $\sigma_a$ et $\sigma_b$ retirent l'effet de leurs amplitudes.

On applique cette définition à la fenêtre $w_n$ et au motif $p$. Comme $w_n[i]=x[n+i-1]$, la corrélation locale est

$$
\rho[n]
=
\frac{
\frac{1}{N_p}
\sum_{i=1}^{N_p}
\bigl(x[n+i-1]-\mu_x[n]\bigr)
\bigl(p[i]-\mu_p\bigr)
}{
\sigma_x[n]\sigma_p
}.
$$

Développons uniquement le numérateur, afin de comprendre le terme compact qui apparaît dans la note :

$$
\begin{aligned}
&\sum_{i=1}^{N_p}
\bigl(x[n+i-1]-\mu_x[n]\bigr)
\bigl(p[i]-\mu_p\bigr)\\
={}&
\sum_{i=1}^{N_p}x[n+i-1]p[i]
-\mu_p\sum_{i=1}^{N_p}x[n+i-1]\\
&-\mu_x[n]\sum_{i=1}^{N_p}p[i]
+\sum_{i=1}^{N_p}\mu_x[n]\mu_p.
\end{aligned}
$$

Les deux sommes suivantes sont les définitions mêmes des moyennes :

$$
\sum_{i=1}^{N_p}x[n+i-1]=N_p\mu_x[n],
\qquad
\sum_{i=1}^{N_p}p[i]=N_p\mu_p.
$$

Le dernier terme répète $N_p$ fois la même constante :

$$
\sum_{i=1}^{N_p}\mu_x[n]\mu_p
=N_p\mu_x[n]\mu_p.
$$

Après substitution, les trois termes contenant les moyennes se simplifient en un seul :

$$
\begin{aligned}
&-\mu_p(N_p\mu_x[n])
-\mu_x[n](N_p\mu_p)
+N_p\mu_x[n]\mu_p\\
={}&-N_p\mu_x[n]\mu_p.
\end{aligned}
$$

On obtient donc

$$
\rho[n]
=\frac{
\sum_{i=1}^{N_p}x[n+i-1]p[i]
-N_p\mu_x[n]\mu_p
}{
N_p\,\sigma_x[n]\,\sigma_p
}.
$$

Autrement dit, la formule ne tombe pas de nulle part : elle est simplement la covariance centrée, développée puis divisée par les deux écarts-types.

#### De la corrélation à la distance normalisée

Par définition des vecteurs normalisés,

$$
\begin{aligned}
\left\langle\widetilde{w_n},\widetilde p\right\rangle
&=
\sum_{i=1}^{N_p}
\frac{x[n+i-1]-\mu_x[n]}{\sigma_x[n]}
\frac{p[i]-\mu_p}{\sigma_p}\\
&=
\frac{
\sum_{i=1}^{N_p}
\bigl(x[n+i-1]-\mu_x[n]\bigr)
\bigl(p[i]-\mu_p\bigr)
}{
\sigma_x[n]\sigma_p
}.
\end{aligned}
$$

Pour voir précisément le lien, introduisons le scalaire

$$
S[n]
=
\sum_{i=1}^{N_p}
\bigl(x[n+i-1]-\mu_x[n]\bigr)
\bigl(p[i]-\mu_p\bigr).
$$

$S[n]$ est la somme des produits des écarts à la moyenne. C'est le numérateur « centré » commun aux deux formules.

Le produit scalaire des deux vecteurs normalisés vaut

$$
\left\langle\widetilde{w_n},\widetilde p\right\rangle
=\frac{S[n]}{\sigma_x[n]\sigma_p}.
$$

La corrélation locale utilise la même quantité, mais elle en prend d'abord la moyenne sur les $N_p$ positions :

$$
\begin{aligned}
\rho[n]
&=
\frac{\frac{1}{N_p}S[n]}
{\sigma_x[n]\sigma_p}\\
&=
\frac{S[n]}
{N_p\,\sigma_x[n]\sigma_p}.
\end{aligned}
$$

En comparant les deux lignes, le produit scalaire contient $S[n]/(\sigma_x[n]\sigma_p)$, tandis que $\rho[n]$ contient cette même quantité divisée par $N_p$. Autrement dit,

$$
\rho[n]
=\frac{1}{N_p}
\left\langle\widetilde{w_n},\widetilde p\right\rangle.
$$

Il suffit de multiplier les deux membres par $N_p$ pour isoler le produit scalaire :

$$
\left\langle\widetilde{w_n},\widetilde p\right\rangle
=N_p\rho[n].
$$

> [!example] Vérification sur une fenêtre de longueur $N_p=2$
> Si les deux formes normalisées sont identiques, par exemple $\widetilde{w_n}=\widetilde p=(-1,1)^\top$, leur produit scalaire vaut $(-1)(-1)+1(1)=2$. La corrélation vaut $2/2=1$. La relation donne bien $2=N_p\rho[n]=2\times1$.

On reprend maintenant le développement déjà obtenu pour le carré de la distance :

$$
d_{\mathrm{nEUC}}^2[n]
=2N_p-2\left\langle\widetilde{w_n},\widetilde p\right\rangle.
$$

En remplaçant le produit scalaire par $N_p\rho[n]$ :

$$
\begin{aligned}
d_{\mathrm{nEUC}}^2[n]
&=2N_p-2N_p\rho[n]\\
&=2N_p\bigl(1-\rho[n]\bigr).
\end{aligned}
$$

Enfin, une distance est positive ou nulle. On prend donc la racine carrée :

$$
\boxed{
d_{\mathrm{nEUC}}[n]
=\sqrt{2N_p\bigl(1-\rho[n]\bigr)}
}.
$$

Cette identité donne une lecture particulièrement utile du profil :

- $\rho[n]=1$ : la fenêtre a exactement la même forme normalisée que le motif, et $d_{\mathrm{nEUC}}[n]=0$ ;
- $\rho[n]=0$ : aucune similarité linéaire de forme ne ressort, et $d_{\mathrm{nEUC}}[n]=\sqrt{2N_p}$ ;
- $\rho[n]=-1$ : la fenêtre a la forme exactement inversée du motif, et $d_{\mathrm{nEUC}}[n]=2\sqrt{N_p}$.

Le profil de distance normalisée est donc borné :

$$
0\leq d_{\mathrm{nEUC}}[n]\leq2\sqrt{N_p}.
$$

Chercher les minima de $d_{\mathrm{nEUC}}$ revient exactement à chercher les maxima de $\rho$. Selon l'application, il peut être plus intuitif de visualiser le profil de corrélation, car « proche de $1$ » se lit naturellement comme « forme très semblable ».

#### Éviter de normaliser chaque fenêtre

La formule de $\rho[n]$ contient trois quantités qui dépendent de $n$ :

1. la moyenne glissante $\mu_x[n]$ ;
2. l'écart-type glissant $\sigma_x[n]$ ;
3. le produit scalaire glissant

   $$
   r_p[n]
   =\sum_{i=1}^{N_p}x[n+i-1]p[i].
   $$

Les deux premières se calculent par sommes cumulées et la troisième par convolution et FFT, comme dans les sections 7.2 et 7.3. On dispose donc déjà de tous les ingrédients de $\rho[n]$.

Il existe une formulation encore plus pratique. On z-normalise le motif une seule fois :

$$
q=\widetilde{p}
=\frac{p-\mu_p\mathbf{1}}{\sigma_p}
\in\mathbb{R}^{N_p}.
$$

$q$ est un vecteur fixe : il ne dépend pas de la position $n$ à laquelle on place le motif dans la série $x$. Cette distinction est essentielle pour le calcul rapide. On peut normaliser $p$ une fois, puis utiliser exactement le même $q$ dans le produit scalaire glissant calculé par FFT.

Les deux propriétés de $q$ viennent directement de cette normalisation. Pour sa somme :

$$
\begin{aligned}
\sum_{i=1}^{N_p}q[i]
&=
\sum_{i=1}^{N_p}\frac{p[i]-\mu_p}{\sigma_p}\\
&=
\frac{1}{\sigma_p}
\left(
\sum_{i=1}^{N_p}p[i]-N_p\mu_p
\right)\\
&=
\frac{1}{\sigma_p}
\left(
N_p\mu_p-N_p\mu_p
\right)
=0.
\end{aligned}
$$

Le motif normalisé est donc **centré** : ses valeurs positives et négatives se compensent exactement. Sa somme de carrés vaut :

$$
\begin{aligned}
\sum_{i=1}^{N_p}q[i]^2
&=
\sum_{i=1}^{N_p}
\left(
\frac{p[i]-\mu_p}{\sigma_p}
\right)^2\\
&=
\frac{1}{\sigma_p^2}
\sum_{i=1}^{N_p}\bigl(p[i]-\mu_p\bigr)^2\\
&=
\frac{N_p\sigma_p^2}{\sigma_p^2}
=N_p.
\end{aligned}
$$

En résumé,

$$
\sum_{i=1}^{N_p}q[i]=0,
\qquad
\sum_{i=1}^{N_p}q[i]^2=N_p.
$$

On calcule ensuite le produit scalaire glissant entre la fenêtre brute et le motif normalisé :

$$
r[n]
=\sum_{i=1}^{N_p}x[n+i-1]q[i].
$$

$r[n]$ est précisément la quantité que la convolution et la FFT calculent rapidement pour toutes les positions $n$. À première vue, ce produit utilise la fenêtre brute $x[n:n+N_p-1]$ plutôt que sa version centrée. La propriété $\sum_iq[i]=0$ permet pourtant de retirer sa moyenne sans modifier le résultat :

$$
\begin{aligned}
r[n]
&=
\sum_{i=1}^{N_p}x[n+i-1]q[i]\\
&=
\sum_{i=1}^{N_p}
\Bigl(
x[n+i-1]-\mu_x[n]+\mu_x[n]
\Bigr)q[i]\\
&=
\sum_{i=1}^{N_p}
\bigl(x[n+i-1]-\mu_x[n]\bigr)q[i]
+
\mu_x[n]\sum_{i=1}^{N_p}q[i]\\
&=
\sum_{i=1}^{N_p}
\bigl(x[n+i-1]-\mu_x[n]\bigr)q[i].
\end{aligned}
$$

La dernière ligne utilise $\sum_iq[i]=0$. C'est le point clé : le produit avec un motif centré est automatiquement insensible à l'offset de la fenêtre.

Par définition de la fenêtre normalisée,

$$
\widetilde{w_n}[i]
=\frac{x[n+i-1]-\mu_x[n]}{\sigma_x[n]}.
$$

On réarrange cette égalité :

$$
x[n+i-1]-\mu_x[n]
=\sigma_x[n]\widetilde{w_n}[i].
$$

En la substituant dans l'expression de $r[n]$ :

$$
\begin{aligned}
r[n]
&=
\sum_{i=1}^{N_p}
\sigma_x[n]\widetilde{w_n}[i]q[i]\\
&=
\sigma_x[n]
\sum_{i=1}^{N_p}\widetilde{w_n}[i]q[i].
\end{aligned}
$$

Comme $q=\widetilde p$, la somme de droite est le produit scalaire des deux formes normalisées :

$$
\sum_{i=1}^{N_p}\widetilde{w_n}[i]q[i]
=
\left\langle\widetilde{w_n},\widetilde p\right\rangle.
$$

On a donc

$$
\left\langle\widetilde{w_n},\widetilde p\right\rangle
=\frac{r[n]}{\sigma_x[n]}.
$$

La section précédente a établi que la corrélation locale est le produit scalaire normalisé divisé par $N_p$ :

$$
\rho[n]
=\frac{1}{N_p}
\left\langle\widetilde{w_n},\widetilde p\right\rangle.
$$

En remplaçant le produit scalaire par $r[n]/\sigma_x[n]$, on obtient

$$
\boxed{
\rho[n]
=\frac{r[n]}{N_p\,\sigma_x[n]}
}.
$$

Enfin, le développement de la distance normalisée donne

$$
d_{\mathrm{nEUC}}^2[n]
=2N_p-2
\left\langle\widetilde{w_n},\widetilde p\right\rangle.
$$

Une dernière substitution fournit :

$$
\begin{aligned}
d_{\mathrm{nEUC}}^2[n]
&=
2N_p-2\frac{r[n]}{\sigma_x[n]}\\
&=
2\left(
N_p-\frac{r[n]}{\sigma_x[n]}
\right).
\end{aligned}
$$

Puisque la distance est positive ou nulle :

$$
\boxed{
d_{\mathrm{nEUC}}[n]
=
\sqrt{
2\left(
N_p-\frac{r[n]}{\sigma_x[n]}
\right)
}
}.
$$

Cette formulation semble plus complexe à lire que la formule générale de Pearson, mais elle est plus efficace à calculer. On n'effectue jamais la z-normalisation explicite de toutes les fenêtres : le terme $r[n]$ vient d'une FFT, et $\sigma_x[n]$ vient des sommes cumulées.

Cette dernière écriture est celle qui est généralement implémentée. Elle ne contient :

- qu'une FFT pour obtenir tous les $r[n]$ ;
- deux tableaux de sommes cumulées pour obtenir tous les $\mu_x[n]$ et $\sigma_x[n]$ ;
- quelques opérations scalaires pour chaque position $n$.

> [!note] Attention à la définition de $r[n]$
> Dans la formule encadrée ci-dessus, $r[n]$ est le produit scalaire avec le **motif déjà normalisé** $q=\widetilde p$. Si l'on utilise à la place le produit scalaire avec le motif brut $p$, il faut revenir à la formule générale de $\rho[n]$, qui fait intervenir $\mu_p$ et $\sigma_p$. Les deux écritures sont équivalentes, mais elles ne réutilisent pas exactement le même $r[n]$.

#### Petit exemple numérique

Prenons un motif de longueur $N_p=2$ :

$$
p=(0,1)^\top.
$$

Sa moyenne est $\mu_p=0.5$ et son écart-type est $\sigma_p=0.5$. Son motif normalisé vaut donc

$$
q=\widetilde p=(-1,1)^\top.
$$

Considérons la fenêtre

$$
w_n=(10,14)^\top.
$$

Elle a une moyenne $\mu_x[n]=12$ et un écart-type $\sigma_x[n]=2$. Le produit scalaire avec $q$ est

$$
r[n]=10(-1)+14(1)=4.
$$

La corrélation locale vaut

$$
\rho[n]=\frac{4}{2\times2}=1,
$$

et la distance normalisée est

$$
d_{\mathrm{nEUC}}[n]
=\sqrt{2(2-4/2)}=0.
$$

La fenêtre est plus haute et plus étendue verticalement que le motif, mais sa forme est la même : « bas puis haut ».

À l'inverse, pour la fenêtre $w_n=(14,10)^\top$, le produit scalaire vaut $r[n]=-4$. On obtient $\rho[n]=-1$ et

$$
d_{\mathrm{nEUC}}[n]
=\sqrt{2(2-(-4)/2)}
=2\sqrt{2}.
$$

La forme est ici l'inverse exact du motif : « haut puis bas ».

#### Déroulé de l'algorithme

Pour calculer tout le profil normalisé :

1. vérifier que le motif n'est pas constant, c'est-à-dire $\sigma_p>0$ ;
2. calculer $q=(p-\mu_p\mathbf{1})/\sigma_p$ ;
3. retourner $q$, le compléter par des zéros, puis utiliser une FFT pour obtenir $r[n]=\langle x[n:n+N_p-1],q\rangle$ pour toutes les positions ;
4. calculer une fois les sommes cumulées de $x$ et de $x^2$ ;
5. en déduire $\mu_x[n]$ et $\sigma_x[n]$ pour chaque fenêtre ;
6. calculer $d_{\mathrm{nEUC}}[n]$ avec la dernière formule encadrée, seulement lorsque $\sigma_x[n]>0$ ;
7. sélectionner les minima significatifs du profil, puis regrouper ou filtrer les minima appartenant à une même occurrence.

Le coût dominant reste la FFT :

$$
O(N\log N).
$$

La normalisation ne modifie pas cet ordre de grandeur. Elle ajoute un pré-calcul linéaire et un nombre constant d'opérations par fenêtre.

#### Cas délicats en pratique

**Fenêtre constante ou presque constante.** Si $\sigma_x[n]=0$, la z-normalisation de la fenêtre n'existe pas. Une fenêtre presque constante possède un $\sigma_x[n]$ très petit : diviser par lui amplifie le bruit. Une implémentation fixe un seuil $\varepsilon>0$ et traite séparément les fenêtres telles que $\sigma_x[n]\leq\varepsilon$. La politique choisie dépend du sens métier : on peut les déclarer incomparables à un motif non constant, ou leur attribuer une distance élevée.

**Erreur d'arrondi.** En théorie, $r[n]/\sigma_x[n]\leq N_p$, de sorte que l'expression sous la racine est positive. En calcul flottant, une valeur comme $-10^{-14}$ peut apparaître à cause des arrondis. Il est prudent de calculer

$$
d_{\mathrm{nEUC}}[n]
=\sqrt{
\max\left(
2\left(N_p-\frac{r[n]}{\sigma_x[n]}\right),
0
\right)
}.
$$

**Amplitude informative.** Une distance normalisée donnera un score nul à deux copies dont l'une est dix fois plus grande que l'autre. C'est la propriété recherchée si l'amplitude est une nuisance. Si l'amplitude fait partie de la signature du phénomène, il faut conserver aussi le profil brut, ou utiliser $\mu_x[n]$ et $\sigma_x[n]$ comme informations supplémentaires.

> [!warning] Recherche de minima et occurrences qui se chevauchent
> Un seul événement peut créer plusieurs petites distances pour des fenêtres voisines. Pour retourner des détections distinctes, une application impose souvent une distance minimale entre deux instants détectés ou une règle de non-recouvrement. Le profil fournit les scores ; la règle de décision finale dépend du problème et des erreurs que l'on veut éviter.

---

## 8. Détecter avec DTW sans calculer DTW partout

### 8.1 Pourquoi le problème est plus difficile

DTW entre deux fenêtres de longueur approximative $N_p$ demande typiquement $O(N_p^2)$ opérations. Le recalculer pour toutes les positions serait beaucoup plus lourd que le balayage euclidien.

Dans de nombreux cas, on ne cherche pas le profil DTW complet. On cherche seulement la meilleure occurrence :

$$
d^\star
=\min_{1\leq n\leq N-N_p+1}
d_{\mathrm{DTW}}\bigl(p,x[n:n+N_p-1]\bigr).
$$

$d^\star$ est la plus petite distance DTW observée entre le motif $p$ et une fenêtre de $x$. L'idée est de ne calculer DTW exactement que pour les candidats qui peuvent encore battre le meilleur score courant.

### 8.2 Principe général des bornes inférieures

Une **borne inférieure** $LB(x,y)$ pour DTW vérifie

$$
LB(x,y)\leq d_{\mathrm{DTW}}(x,y).
$$

Si l'on connaît déjà une meilleure distance $d^\star$ et que

$$
LB\bigl(p,x[n:n+N_p-1]\bigr)>d^\star,
$$

alors la vraie distance DTW de cette fenêtre est forcément plus grande que $d^\star$. Cette fenêtre ne peut pas être la meilleure : on la rejette sans calculer son DTW exact.

Une borne est utile si elle satisfait deux qualités opposées :

- elle est **très peu coûteuse** à calculer ;
- elle est **assez serrée** pour éliminer beaucoup de candidats.

Pendant un calcul de borne ou de DTW, on peut aussi abandonner dès que le coût partiel dépasse le meilleur coût actuel : les termes restants sont positifs, ils ne pourront jamais faire redescendre la somme.""

### 8.3 Borne très simple : LB Kim First/Last

Dans le DTW classique, les premiers points et les derniers points sont nécessairement alignés. Pour $x\in\mathbb{R}^M$ et $y\in\mathbb{R}^N$,

$$
LB_{\mathrm{KimFL}}(x,y)
=\sqrt{
  \bigl(x[1]-y[1]\bigr)^2+
  \bigl(x[M]-y[N]\bigr)^2
}.
$$

Cette borne ne regarde que les deux extrémités. Elle est valide parce que leurs deux contributions appartiennent à tout chemin DTW admissible ; le coût total, qui contient en plus les autres contributions positives, ne peut pas être plus petit.

Elle est très rapide, mais souvent peu précise. Elle constitue une première barrière : même une borne faible peut éliminer de nombreux candidats une fois qu'un bon $d^\star$ a été trouvé.

### 8.4 Borne LB Keogh : enveloppe autorisée par la contrainte DTW

Supposons un DTW contraint par une bande de largeur $\lambda$. Pour chaque position $j$ de $x$, on calcule l'enveloppe :

$$
u[j]=\max_{j-\lambda\leq i\leq j+\lambda}x[i],
\qquad
\ell[j]=\min_{j-\lambda\leq i\leq j+\lambda}x[i].
$$

Les indices hors des bornes du signal sont simplement ignorés. $u[j]$ est le plafond local et $\ell[j]$ le plancher local que peut atteindre un échantillon de $x$ susceptible d'être apparié à $y[j]$.

Si $y[j]$ se trouve dans l'intervalle $[\ell[j],u[j]]$, il existe au moins une valeur admissible de $x$ à la même hauteur : cette position ne force pas d'erreur minimale positive. Si $y[j]$ est sous l'enveloppe ou au-dessus, tout alignement autorisé paiera au moins l'écart à l'enveloppe.

On obtient

$$
LB_{\mathrm{Keogh}}(x,y)
=\sqrt{
  \sum_{\substack{1\leq j\leq N\\y[j]<\ell[j]}}
  \bigl(\ell[j]-y[j]\bigr)^2
  +
  \sum_{\substack{1\leq j\leq N\\y[j]>u[j]}}
  \bigl(u[j]-y[j]\bigr)^2
}.
$$

Cette formule additionne uniquement les portions de $y$ qui sortent de l'enveloppe autorisée autour de $x$. Les portions situées à l'intérieur contribuent $0$ à la borne, car elles peuvent en principe être alignées sans écart obligatoire.

> [!intuition] Image mentale de LB Keogh
> Dessinez autour de $x$ un couloir formé par son minimum et son maximum locaux. DTW contraint peut faire glisser les correspondances, mais pas au-delà de ce couloir. Chaque morceau de $y$ qui dépasse le couloir doit au minimum revenir jusqu'à sa frontière : cette distance inévitable forme la borne.

### 8.5 Schéma de recherche exact accéléré

Pour trouver une occurrence optimale avec DTW :

1. calculer une borne bon marché pour chaque fenêtre candidate ;
2. initialiser $d^\star$ à $+\infty$, puis calculer DTW exact pour une ou quelques fenêtres afin d'obtenir rapidement une première référence ;
3. éliminer tout candidat dont la borne est supérieure au $d^\star$ courant ;
4. évaluer DTW uniquement pour les candidats survivants, en abandonnant tôt les calculs déjà perdants ;
5. mettre à jour $d^\star$ lorsqu'une meilleure fenêtre est trouvée, ce qui permet d'éliminer davantage de candidats.

Les implémentations spécialisées combinent plusieurs bornes, un ordre de traitement judicieux et des calculs de normalisation optimisés. Le résultat est qu'une proportion extrêmement grande des calculs DTW peut être évitée tout en conservant une recherche exacte.

---

# Partie III — Extraire des motifs lorsque le dictionnaire est inconnu

## 9. Du gabarit donné au motif découvert

Jusqu'ici, le motif $p$ était connu. En extraction non supervisée, on connaît seulement le signal $x$ et une longueur typique $L$ pour les motifs recherchés.

Une première idée est directe : chaque fenêtre

$$
q_n=x[n:n+L-1]
$$

peut être considérée comme un gabarit candidat. Si son profil de distance possède plusieurs minima bas à des positions éloignées, elle ressemble à d'autres fenêtres et peut correspondre à un motif.

Le danger principal est le **match trivial** : une fenêtre est exactement identique à elle-même, et presque identique aux fenêtres qui la chevauchent. Ces correspondances ne prouvent aucune répétition dans le signal.

## 10. Matrix Profile : trouver le voisin non recouvrant le plus proche

### 10.1 Définition

Pour chaque position $n$, le **matrix profile** associe à la fenêtre $x[n:n+L-1]$ la distance vers sa fenêtre non recouvrante la plus proche :

$$
m[n]
=\min_{\substack{i\leq n-L\\\text{ou }i\geq n+L}}
d\Bigl(
  x[n:n+L-1],
  x[i:i+L-1]
\Bigr).
$$

Les notations sont :

- $L\in\mathbb{N}$ : longueur commune fixée pour les motifs ;
- $n$ : début de la fenêtre examinée ;
- $i$ : début d'une autre fenêtre candidate ;
- $m[n]\in\mathbb{R}_{\geq0}$ : distance de la fenêtre $n$ à son meilleur voisin non trivial ;
- $d$ : distance choisie, souvent euclidienne z-normalisée.

La condition $i\leq n-L$ ou $i\geq n+L$ garantit que les fenêtres ne se chevauchent pas. Une zone d'exclusion légèrement plus large peut être retenue en pratique, selon le problème.

Cette formule dit essentiellement que l'on demande à chaque fenêtre : « existe-t-il ailleurs dans le signal une fenêtre réellement séparée qui lui ressemble beaucoup ? »

### 10.2 Lire le profil

Une petite valeur de $m[n]$ signifie qu'une autre fenêtre non recouvrante ressemble fortement à la fenêtre commençant en $n$. Les plus petits creux du matrix profile indiquent des **paires de motifs** candidates.

Une grande valeur indique au contraire que la fenêtre a peu de voisins similaires. Cette observation peut aussi servir à explorer des portions atypiques, mais une grande distance seule ne suffit pas à conclure à une anomalie : tout dépend de la distance et du contexte.

> [!example] Pourquoi l'exclusion est indispensable
> Soit $L=100$. La fenêtre qui commence en $n=500$ recouvre presque entièrement celle qui commence en $n=501$. Elles auront une petite distance même si la forme ne revient jamais ailleurs. Imposer $|i-n|\geq L$ oblige à trouver une occurrence séparée dans le temps.

### 10.3 Motif pair et motif set

Le matrix profile résout naturellement le problème de la **paire de motifs** : il met en évidence deux sous-séquences non recouvrantes très similaires.

Les applications demandent souvent un **ensemble de motifs** : toutes les occurrences d'une même forme. Une stratégie consiste à :

1. choisir une paire particulièrement proche ;
2. calculer le profil de distance de l'une de ses occurrences ;
3. retenir les fenêtres sous un seuil de distance ;
4. écarter ces occurrences ou leur voisinage, puis recommencer pour trouver un autre groupe.

Cette stratégie introduit deux choix difficiles :

- le **seuil** décide de ce qui ressemble « assez » au motif ;
- le **nombre d'ensembles** décide quand arrêter l'extraction.

Ces paramètres ont une forte influence lorsque les motifs sont bruités, de durées variables ou présents rarement. Le motif set général reste un problème plus difficile que la simple paire de motifs.

### 10.4 Lien avec la recherche rapide précédente

Le matrix profile semble demander la comparaison de toutes les paires de fenêtres, ce qui paraît prohibitif. Les mêmes idées que pour le profil de distance, notamment les produits scalaires glissants calculés par FFT et les statistiques cumulées, rendent les calculs beaucoup plus efficaces.

Le changement conceptuel est important : en détection, un gabarit est fixé et on balaie le signal. Avec le matrix profile, chaque fenêtre peut tour à tour jouer le rôle du gabarit.

---

## 11. Apprendre un dictionnaire de motifs par modèle convolutif

### 11.1 Pourquoi changer d'approche ?

La recherche par distance cherche des occurrences qui se ressemblent directement. Elle est très naturelle pour découvrir une paire ou un groupe de copies approximatives d'une même forme.

Un signal réel peut toutefois contenir plusieurs motifs qui se superposent ou se mélangent. Par exemple, une mesure peut contenir simultanément une oscillation de fond et des événements brefs. Une seule fenêtre de référence ne modélise pas bien cette superposition.

L'**apprentissage de dictionnaire convolutif** (*convolutional dictionary learning*, CDL) adopte un modèle génératif : le signal est reconstruit comme une somme de motifs courts placés à différents instants, avec différentes amplitudes.

### 11.2 Modèle de reconstruction

On cherche $K$ motifs, appelés aussi **atomes de dictionnaire** :

$$
d_k\in\mathbb{R}^{L},
\qquad 1\leq k\leq K,
$$

où :

- $K\in\mathbb{N}$ est le nombre de motifs à apprendre ;
- $L\in\mathbb{N}$ est la longueur commune de chaque motif ;
- $d_k$ est le $k$-ième motif court.

Pour chaque motif, on apprend un signal d'activation :

$$
z_k\in\mathbb{R}^{N-L+1}.
$$

Une valeur $z_k[n]\neq 0$ signifie que le motif $d_k$ est activé vers l'instant $n$. Sa valeur indique aussi son amplitude et son signe. Avec la convention de convolution choisie, une activation $z_k[n]=a$ ajoute une copie de $a\,d_k$ au signal à partir de cette position.

Le modèle est

$$
x[n]
=\sum_{k=1}^{K}(z_k*d_k)[n]+e[n].
$$

Dans cette formule :

- $x[n]$ est l'échantillon observé du signal ;
- $(z_k*d_k)[n]$ est la contribution du motif $k$ à l'instant $n$ ;
- $\sum_{k=1}^{K}$ additionne les contributions de tous les motifs ;
- $e[n]$ est le résidu : bruit, erreurs de modèle ou structure non expliquée.

Cette formule dit que le signal peut être vu comme une superposition de quelques formes courtes, chacune réapparaissant à certains instants. Les détails précis de bord de la convolution, par exemple le *padding*, dépendent de l'implémentation ; l'interprétation « une activation place un motif » reste la même.

> [!example] Un signal de consommation
> Un atome $d_1$ peut représenter la montée puis l'arrêt d'une bouilloire. Un atome $d_2$ peut représenter un cycle plus lent. Si $z_1[500]$ est non nul, le modèle place une bouilloire vers l'instant $500$. Si $z_1[500]$ et $z_2[520]$ sont non nuls, les deux phénomènes peuvent contribuer simultanément au signal observé.

### 11.3 Problème d'optimisation

On apprend simultanément les motifs $(d_k)_{k=1}^{K}$ et les activations $(z_k)_{k=1}^{K}$ en minimisant

$$
\min_{\substack{(d_k),(z_k)\\
                \forall k,\;\lVert d_k\rVert_2^2\leq 1}}
\underbrace{
\left\lVert
x-\sum_{k=1}^{K}z_k*d_k
\right\rVert_2^2
}_{\text{erreur de reconstruction}}
+
\lambda
\underbrace{
\sum_{k=1}^{K}\lVert z_k\rVert_1
}_{\text{parcimonie des activations}}.
$$

Les nouveaux symboles sont :

- $\lVert v\rVert_2^2=\sum_i v[i]^2$ : énergie quadratique d'un vecteur $v$ ;
- $\lVert z_k\rVert_1=\sum_n |z_k[n]|$ : somme des amplitudes absolues des activations ;
- $\lambda\geq0$ : paramètre qui règle l'importance donnée à la parcimonie ;
- $\lVert d_k\rVert_2^2\leq1$ : contrainte de norme sur chaque motif.

Le premier terme exige que la reconstruction ressemble au signal observé. Le second pousse une grande partie des coefficients $z_k[n]$ vers zéro : le modèle préfère expliquer le signal avec peu d'activations nettes plutôt qu'avec de petites contributions partout.

### 11.4 Pourquoi contraindre les motifs ?

Sans contrainte sur $d_k$, une ambiguïté d'échelle apparaît. Pour tout $\alpha>0$,

$$
z_k*d_k
=\left(\frac{z_k}{\alpha}\right)*(\alpha d_k).
$$

La reconstruction ne change pas. En revanche, si l'on agrandit $d_k$ et que l'on réduit $z_k$, la pénalité $\lVert z_k\rVert_1$ diminue artificiellement. Le problème pourrait favoriser des motifs de norme arbitrairement grande.

La contrainte $\lVert d_k\rVert_2^2\leq1$ fixe une échelle de référence. Elle rend les activations comparables et évite cette instabilité numérique.

### 11.5 Le rôle de $\lambda$

Le paramètre $\lambda$ contrôle le compromis :

- si $\lambda$ est petit, le modèle privilégie une reconstruction très fine et peut activer de nombreux motifs ;
- si $\lambda$ est grand, le modèle exige des activations plus rares et accepte davantage de résidu.

Un $\lambda$ trop grand peut manquer des occurrences réelles. Un $\lambda$ trop petit peut transformer les atomes en outils peu interprétables utilisés presque continuellement. Ce paramètre ne possède pas une valeur universelle : on le choisit selon le niveau de bruit, l'échelle des données et le degré de rareté attendu.

## 12. Résoudre l'apprentissage : alternance, gradients et proximal

### 12.1 Pourquoi alterner entre dictionnaire et activations ?

L'objectif n'est pas convexe lorsque les motifs et les activations sont inconnus simultanément : leurs produits convolutifs les couplent. Optimiser les deux blocs d'un coup peut conduire à plusieurs minima locaux.

En revanche :

- si les activations $Z=(z_1,\ldots,z_K)$ sont fixées, chercher le dictionnaire $D=(d_1,\ldots,d_K)$ est un sous-problème convexe sous contrainte ;
- si le dictionnaire $D$ est fixé, chercher les activations $Z$ est un problème de codage parcimonieux convexe.

On utilise donc une **résolution alternée** :

1. fixer temporairement les activations et mettre à jour les motifs ;
2. fixer temporairement les motifs et mettre à jour les activations ;
3. répéter jusqu'à stabilisation ou jusqu'à un nombre d'itérations choisi.

Cette stratégie ne garantit pas le meilleur optimum global. L'initialisation des motifs et les hyperparamètres peuvent influencer le résultat final.

> [!note] Où intervient la convexité ?
> Le problème complet est non convexe, car il contient des produits entre deux variables inconnues, comme $z_k*d_k$. Dans le cas jouet d'un seul scalaire, le terme $(zd-x)^2$ illustre déjà ce couplage entre $z$ et $d$.
>
> Dès que l'on fixe $Z$, le signal reconstruit dépend linéairement de $D$ ; l'erreur quadratique est alors convexe en $D$. De même, lorsque l'on fixe $D$, elle est convexe en $Z$, et la pénalité $\lVert Z\rVert_1$ est elle aussi convexe. L'alternance ne rend pas le problème global convexe, mais elle permet de résoudre successivement deux sous-problèmes beaucoup mieux structurés.

> [!summary] La boucle mentale de l'apprentissage alterné
> Les notions de convolution, de résidu, de gradient, de FFT et de proximal ne désignent pas des techniques séparées. Elles décrivent les étapes d'une même boucle :
>
> $$
> (D,Z)
> \longrightarrow
> \text{reconstruction}
> \longrightarrow
> \text{résidu}
> \longrightarrow
> \text{gradient}
> \longrightarrow
> \text{mise à jour}
> \longrightarrow
> \text{contrainte ou proximal}
> \longrightarrow
> \text{nouvelle itération}.
> $$
>
> On fixe d'abord $Z$ pour améliorer les motifs $D$, puis on fixe le nouveau dictionnaire $D$ pour améliorer les activations $Z$. La FFT sert uniquement à accélérer la reconstruction et le calcul des gradients au centre de cette boucle.

### 12.2 Pourquoi travailler parfois dans le domaine de Fourier ?

Posons l'erreur de reconstruction

$$
f(Z,D)
=\left\lVert
x-\sum_{k=1}^{K}z_k*d_k
\right\rVert_2^2.
$$

Cette expression est facile à comprendre, mais coûteuse à manipuler naïvement. À chaque itération d'optimisation, il faudrait calculer les $K$ convolutions $z_k*d_k$, les additionner, comparer la reconstruction à $x$, puis calculer les gradients par rapport à tous les $d_k$ et tous les $z_k$. Pour un long signal, répéter ces convolutions dans le domaine temporel devient le principal coût de calcul.

L'outil qui simplifie ce calcul est la transformée de Fourier discrète, ou **DFT**. Avant de l'utiliser, on prolonge chaque signal par des zéros jusqu'à une même longueur $T$. Avec les conventions du modèle, une activation a longueur $N-L+1$ et un motif a longueur $L$ ; leur convolution linéaire a longueur $N$. Il suffit donc de choisir $T\geq N$. Ce *zero-padding* évite que la DFT transforme par erreur une convolution linéaire en convolution circulaire, où la fin du signal reviendrait artificiellement au début.

Pour un vecteur réel ou complexe $v\in\mathbb{C}^{T}$, sa DFT est le vecteur complexe $\widehat v\in\mathbb{C}^{T}$ dont la composante d'indice $\omega\in\{0,\ldots,T-1\}$ vaut

$$
\widehat v[\omega]
=\sum_{t=0}^{T-1}
v[t]\exp\left(-\frac{2\pi\mathrm{i}\omega t}{T}\right).
$$

Dans cette formule :

- $t$ est un indice temporel ;
- $\omega$ est un indice de fréquence discrète ;
- $\mathrm{i}^2=-1$ est l'unité imaginaire ;
- $\widehat v[\omega]\in\mathbb{C}$ décrit la contribution de l'oscillation de fréquence $\omega$ au signal $v$.

Il n'est pas nécessaire d'interpréter chaque coefficient de Fourier pour utiliser cette propriété. L'idée utile est qu'une DFT réexprime le même signal dans une base d'oscillations, et que certains calculs deviennent beaucoup plus simples dans cette base.

#### Le théorème de convolution

Dans le domaine temporel, une convolution mélange de nombreuses paires d'échantillons. Après DFT, elle devient un produit composante par composante :

$$
\widehat{z_k*d_k}
=\widehat{z_k}\odot\widehat{d_k}.
$$

Le symbole $\odot$ désigne le produit terme à terme. Autrement dit, pour chaque fréquence $\omega$,

$$
\widehat{z_k*d_k}[\omega]
=\widehat{z_k}[\omega]\widehat{d_k}[\omega].
$$

Cette formule dit essentiellement que, à une fréquence donnée, l'effet d'une activation et celui d'un motif se combinent par une simple multiplication de deux nombres complexes. Il n'y a plus de somme glissante sur les positions temporelles.

En utilisant ce résultat dans le modèle de reconstruction, on obtient

$$
\widehat{x}
\approx
\sum_{k=1}^{K}
\widehat{z_k}\odot\widehat{d_k}.
$$

À la fréquence $\omega$, cette égalité devient

$$
\widehat{x}[\omega]
\approx
\sum_{k=1}^{K}
\widehat{z_k}[\omega]\widehat{d_k}[\omega].
$$

Cette dernière écriture ressemble à une petite régression linéaire complexe : le coefficient observé $\widehat{x}[\omega]$ est expliqué par la somme des $K$ produits associés aux atomes. La séparation par fréquence concerne le terme de fidélité aux données. Les contraintes de support temporel des motifs et de norme $\lVert d_k\rVert_2\leq 1$ sont, elles, appliquées après retour dans le domaine temporel ; elles reconnectent les fréquences entre elles.

#### Le théorème de Parseval

Le point essentiel de Parseval est simple : si deux signaux sont proches dans le domaine temporel, leurs représentations de Fourier sont aussi proches. La réciproque est vraie. Autrement dit, la DFT permet de changer de coordonnées sans perdre l'information contenue dans une erreur quadratique.

Cette propriété complète le théorème de convolution. Fourier a déjà rendu les convolutions faciles à calculer ; Parseval permet d'y mesurer directement l'erreur de reconstruction, sans changer le problème d'optimisation.

Repartons de la reconstruction proposée par le dictionnaire :

$$
\widetilde x
=\sum_{k=1}^{K}z_k*d_k.
$$

Le symbole $\widetilde x\in\mathbb{R}^{N}$ désigne ici le signal reconstruit. Le résidu est la partie de $x$ qui reste inexpliquée :

$$
r
=x-\widetilde x
=x-\sum_{k=1}^{K}z_k*d_k.
$$

Ainsi, l'erreur de reconstruction est tout simplement l'énergie du résidu :

$$
f(Z,D)
=\lVert r\rVert_2^2.
$$

> [!example] Résidu et erreur quadratique
> Si
>
> $$
> x=
> \begin{pmatrix}
> 3\\
> 5\\
> 2
> \end{pmatrix},
> \qquad
> \widetilde x=
> \begin{pmatrix}
> 2\\
> 5\\
> 4
> \end{pmatrix},
> $$
>
> alors
>
> $$
> r=
> \begin{pmatrix}
> 1\\
> 0\\
> -2
> \end{pmatrix}
> \qquad\text{et}\qquad
> \lVert r\rVert_2^2
> =1^2+0^2+(-2)^2
> =5.
> $$
>
> L'erreur quadratique répond donc à la question : « quelle quantité totale d'erreur reste-t-il après reconstruction ? » Les écarts les plus grands contribuent davantage, car ils sont mis au carré.

On applique maintenant la DFT au résidu :

$$
r
\xrightarrow{\mathrm{DFT}}
\widehat r.
$$

Le vecteur $\widehat r\in\mathbb{C}^{T}$ contient des coefficients complexes. Un coefficient, par exemple

$$
\widehat r[\omega]=3+4\mathrm{i},
$$

se lit à l'aide de son module :

$$
\left|\widehat r[\omega]\right|
=\sqrt{3^2+4^2}
=5.
$$

Son carré $\left|\widehat r[\omega]\right|^2=25$ mesure la contribution de cette fréquence à l'énergie du résidu. Il ne faut pas interpréter ce $25$ comme une erreur temporelle localisée : il décrit la force d'une composante oscillante dans l'erreur globale.

Si l'on utilise la convention de DFT écrite plus haut, le théorème de Parseval donne

$$
\lVert r\rVert_2^2
=\frac{1}{T}\lVert\widehat r\rVert_2^2,
$$

où $r\in\mathbb{R}^{T}$ est un résidu temporel et $\widehat r\in\mathbb{C}^{T}$ sa DFT. La norme complexe signifie ici

$$
\lVert\widehat r\rVert_2^2
=\sum_{\omega=0}^{T-1}
\left|\widehat r[\omega]\right|^2.
$$

Le module $|\widehat r[\omega]|$ joue le même rôle que la valeur absolue pour un nombre réel. La formule complète de Parseval est donc

$$
\sum_{t=0}^{T-1}\left|r[t]\right|^2
=\frac{1}{T}
\sum_{\omega=0}^{T-1}
\left|\widehat r[\omega]\right|^2.
$$

Cette formule dit que l'énergie totale de l'erreur peut être calculée de deux manières équivalentes :

- dans le temps, en additionnant les erreurs $r[t]^2$ à chaque instant ;
- dans Fourier, en additionnant l'énergie $\left|\widehat r[\omega]\right|^2$ de chaque fréquence.

> [!intuition] Une rotation de coordonnées
> Un vecteur du plan peut être décrit dans les axes usuels par $(a,b)$ ou dans des axes tournés par $(a',b')$. Sa longueur est conservée :
>
> $$
> a^2+b^2={a'}^2+{b'}^2.
> $$
>
> La DFT joue un rôle analogue sur un signal. Elle remplace la description par échantillons $r[0],r[1],\ldots$ par une description en oscillations $\widehat r[0],\widehat r[1],\ldots$. Parseval garantit que la quantité globale d'erreur reste la même, à un facteur de normalisation près.

Le facteur $1/T$ dépend de la convention de normalisation choisie pour la DFT. Il change la valeur numérique de l'objectif et l'amplitude de son gradient, mais ne change pas le minimiseur : $T$ est une constante strictement positive. Par exemple, minimiser $u^2$ ou $100u^2$ conduit dans les deux cas à $u=0$.

En posant le résidu fréquentiel

$$
\widehat r
=\widehat x-
\sum_{k=1}^{K}
\widehat{z_k}\odot\widehat{d_k},
$$

l'erreur de reconstruction devient exactement

$$
f(Z,D)
=\frac{1}{T}
\left\lVert
\widehat x-
\sum_{k=1}^{K}
\widehat{z_k}\odot\widehat{d_k}
\right\rVert_2^2.
$$

On écrit fréquemment cette relation sous la forme plus compacte

$$
f(Z,D)
\propto
\left\lVert
\widehat x-
\sum_{k=1}^{K}
\widehat{z_k}\odot\widehat{d_k}
\right\rVert_2^2.
$$

Le symbole $\propto$ signifie « égal à une constante multiplicative positive près ». Ici, on omet le facteur fixe $1/T$. Cette écriture ne prétend pas que les deux valeurs numériques sont égales ; elle indique qu'elles ont exactement le même minimum en fonction de $Z$ et $D$.

> [!summary] Les deux propriétés qui rendent Fourier utile
>
> $$
> z_k*d_k
> \xrightarrow{\mathrm{Fourier}}
> \widehat{z_k}\odot\widehat{d_k}
> $$
>
> transforme une convolution coûteuse en multiplication terme à terme, tandis que
>
> $$
> \left\lVert
> x-\sum_k z_k*d_k
> \right\rVert_2^2
> \xrightarrow{\mathrm{Parseval}}
> \frac{1}{T}
> \left\lVert
> \widehat x-\sum_k
> \widehat{z_k}\odot\widehat{d_k}
> \right\rVert_2^2
> $$
>
> garantit que l'on peut mesurer l'erreur dans le domaine fréquentiel sans modifier le problème que l'on cherche à résoudre.

#### Pourquoi les gradients deviennent eux aussi rapides

Pour améliorer un motif $d_k$, il faut mesurer comment le résidu $r$ est lié à son activation $z_k$. Dans le domaine temporel, cela revient à calculer une **corrélation** entre le résidu et l'activation. De façon analogue, pour améliorer $z_k$, on corrèle le résidu avec le motif $d_k$.

#### D'où vient la corrélation dans le gradient ?

Concentrons-nous sur un coefficient précis $d_k[u]$ du motif $d_k$, où $u$ est un indice temporel à l'intérieur de ce motif. En supposant les signaux prolongés par zéro hors de leurs bornes, la contribution de l'atome $k$ à l'instant $t$ est

$$
(z_k*d_k)[t]
=\sum_v z_k[t-v]d_k[v].
$$

Le coefficient $d_k[u]$ apparaît uniquement dans le terme où $v=u$. Modifier ce coefficient change donc le résidu selon

$$
\frac{\partial r[t]}{\partial d_k[u]}
=-z_k[t-u].
$$

Le signe moins vient de la définition $r=x-\sum_k z_k*d_k$. Si l'on augmente $d_k[u]$ et que l'activation $z_k[t-u]$ est positive, la reconstruction augmente à l'instant $t$ ; le résidu diminue.

L'objectif est une somme d'erreurs quadratiques :

$$
f(Z,D)
=\sum_t r[t]^2.
$$

La règle de dérivation en chaîne donne alors

$$
\begin{aligned}
\frac{\partial f}{\partial d_k[u]}
&=\sum_t
\frac{\partial r[t]^2}{\partial r[t]}
\frac{\partial r[t]}{\partial d_k[u]}\\
&=\sum_t 2r[t]\bigl(-z_k[t-u]\bigr)\\
&=-2\sum_t r[t]z_k[t-u].
\end{aligned}
$$

Cette dernière somme est une corrélation : elle mesure si le résidu ressemble à l'activation $z_k$ autour de la position $u$. Un résidu élevé aux instants où l'atome est activé indique que le motif doit être modifié.

Pour relier cette expression à une convolution, on définit le retournement temporel, avec prolongement par zéro,

$$
z_k^{\leftarrow}[s]
=z_k[-s].
$$

En effectuant un changement d'indice dans la convolution, on obtient

$$
\bigl(z_k^{\leftarrow}*r\bigr)[u]
=\sum_t z_k[t-u]r[t].
$$

Le gradient précédent peut donc s'écrire

$$
\frac{\partial f}{\partial d_k[u]}
=-2\bigl(z_k^{\leftarrow}*r\bigr)[u].
$$

Le même raisonnement appliqué à un coefficient d'activation donne un gradient qui corrèle le résidu avec le motif. C'est la raison profonde pour laquelle les deux gradients ont la forme de convolutions avec un signal retourné.

En ignorant les détails de bord et les constantes de normalisation, les gradients ont la forme intuitive

$$
\nabla_{d_k} f
\propto
-\,z_k^{\leftarrow}*r,
\qquad
\nabla_{z_k} f
\propto
-\,d_k^{\leftarrow}*r,
$$

où $z_k^{\leftarrow}$ et $d_k^{\leftarrow}$ désignent les versions retournées dans le temps de $z_k$ et $d_k$. Le premier gradient indique comment modifier la forme de l'atome pour réduire le résidu aux endroits où il est activé. Le second indique comment modifier l'intensité de l'activation aux endroits où la forme de l'atome explique bien ou mal le signal.

Or une corrélation est elle aussi une convolution avec un signal retourné. Elle se calcule donc efficacement par FFT. Dans le domaine fréquentiel, les mêmes gradients s'écrivent, à une constante positive près,

$$
\widehat{\nabla_{d_k} f}
\propto
-\,\overline{\widehat{z_k}}\odot\widehat r,
\qquad
\widehat{\nabla_{z_k} f}
\propto
-\,\overline{\widehat{d_k}}\odot\widehat r.
$$

#### Pourquoi le conjugué complexe apparaît-il ?

La barre $\overline{\phantom{x}}$ désigne le **conjugué complexe**. Si $c=a+\mathrm{i}b$, alors $\overline c=a-\mathrm{i}b$. Dans le plan complexe, prendre le conjugué reflète le nombre par rapport à l'axe réel.

Le conjugué apparaît parce qu'une corrélation retourne l'un des deux signaux. Avec une DFT de longueur $T$, le retournement exactement adapté aux opérations circulaires de la DFT est

$$
z_k^{\mathrm{rev}}[t]
=z_k[-t\operatorname{mod}T].
$$

Il faut distinguer ce retournement circulaire du retournement habituel d'une liste. Par exemple, si $T=4$ et $z=(a,b,c,d)$, alors

$$
z_k^{\mathrm{rev}}
=(a,d,c,b),
$$

et non $(d,c,b,a)$. Le premier élément reste fixe car l'indice $0$ est son propre opposé sur un cercle de longueur $T$.

Partons de la définition de la DFT du signal retourné :

$$
\widehat{z_k^{\mathrm{rev}}}[\omega]
=\sum_{t=0}^{T-1}
z_k[-t\operatorname{mod}T]
\exp\left(-\frac{2\pi\mathrm{i}\omega t}{T}\right).
$$

En remplaçant l'indice $t$ par son opposé circulaire $s=-t\operatorname{mod}T$, le signe dans l'exponentielle change :

$$
\widehat{z_k^{\mathrm{rev}}}[\omega]
=\sum_{s=0}^{T-1}
z_k[s]
\exp\left(+\frac{2\pi\mathrm{i}\omega s}{T}\right).
$$

Lorsque $z_k$ est réel, cette expression est exactement le conjugué de sa DFT :

$$
\overline{\widehat{z_k}[\omega]}
=\sum_{s=0}^{T-1}
z_k[s]
\exp\left(+\frac{2\pi\mathrm{i}\omega s}{T}\right).
$$

On a donc

$$
\widehat{z_k^{\mathrm{rev}}}
=\overline{\widehat{z_k}}.
$$

Le théorème de convolution transforme alors la corrélation temporelle en

$$
\widehat{z_k^{\mathrm{rev}}*r}
=\overline{\widehat{z_k}}\odot\widehat r.
$$

C'est exactement le terme qui apparaît dans le gradient fréquentiel. Le raccourci à retenir est :

$$
\text{convolution}
\longleftrightarrow
\text{multiplication en Fourier},
\qquad
\text{corrélation}
\longleftrightarrow
\text{multiplication avec un conjugué}.
$$

Dans la pratique, les bibliothèques FFT réalisent ce calcul avec des tableaux de longueur $T$ complétés par des zéros. Le retournement linéaire habituel et le retournement circulaire peuvent différer d'un décalage, donc d'un facteur de phase dans Fourier. Une implémentation doit employer des conventions cohérentes de padding, de décalage et de découpage du résultat. Après la transformée de Fourier inverse, on conserve la portion correspondant aux indices valides du motif ou de l'activation. Lorsque les signaux d'origine sont réels, une éventuelle petite partie imaginaire restante provient seulement des erreurs d'arrondi.

> [!example] Une itération vue comme un aller-retour
> Pour calculer les gradients de tous les atomes et de toutes les activations :
>
> 1. appliquer une FFT à $x$, aux $d_k$ et aux $z_k$ ;
> 2. former $\widehat r=\widehat x-\sum_k\widehat{z_k}\odot\widehat{d_k}$ ;
> 3. multiplier $\widehat r$ par les conjugués appropriés pour obtenir les gradients fréquentiels ;
> 4. appliquer une iFFT pour retrouver les gradients temporels ;
> 5. effectuer les étapes proximales de la section suivante.
>
> L'étape coûteuse n'est plus une longue boucle de convolutions pour chaque position. Elle devient un petit nombre de FFT, dont le coût est de l'ordre de $O(T\log T)$, plus des produits terme à terme de taille $T$ pour chacun des $K$ atomes.

> [!summary] À retenir
> La FFT n'est pas utilisée parce que les motifs seraient nécessairement « fréquentiels ». Le modèle reste interprété dans le temps : les atomes sont des formes courtes et les activations indiquent leurs occurrences. La FFT est un changement de coordonnées temporaire qui accélère les convolutions et les corrélations nécessaires à l'optimisation.

### 12.3 Descente de gradient proximale

Pour un objectif composé d'une partie lisse et d'une contrainte ou pénalité non lisse, une itération de méthode proximale a deux moments :

1. une étape de gradient réduit l'erreur de reconstruction ;
2. une étape **proximale** impose la contrainte ou encourage la structure recherchée.

> [!intuition] Deux objectifs à concilier
> Une étape de gradient répond à la question : « quelle petite modification explique mieux le signal ? » Elle ne sait pas, à elle seule, imposer que les motifs restent de taille limitée ou que les activations soient rares. L'étape proximale répond à la seconde question : « parmi les modifications qui améliorent la reconstruction, lesquelles respectent la règle de structure choisie ? »
>
> Il n'est pas nécessaire de maîtriser toute l'optimisation convexe pour suivre l'algorithme. On peut retenir : **améliorer l'ajustement, puis corriger pour respecter la règle voulue**.

Pour le dictionnaire, avec $Z$ fixé, une étape s'écrit

$$
D\leftarrow D-\alpha\nabla_D f(Z,D),
$$

puis, pour chaque atome,

$$
d_k\leftarrow
\operatorname{proj}_{\lVert\cdot\rVert_2\leq1}(d_k).
$$

Ici, $\alpha>0$ est le pas de gradient et $\nabla_D f(Z,D)$ est le gradient de l'erreur par rapport aux motifs. Le gradient améliore la reconstruction ; la projection remet ensuite chaque motif dans la boule de norme autorisée.

La projection d'un vecteur $y$ sur cette boule vaut

$$
\operatorname{proj}_{\lVert\cdot\rVert_2\leq1}(y)
=\frac{y}{\max(\lVert y\rVert_2,1)}.
$$

Si $y$ a déjà une norme inférieure ou égale à $1$, le dénominateur vaut $1$ et rien ne change. Si sa norme est trop grande, on le redimensionne exactement sur la frontière de la boule.

Cette projection est le proximal de la contrainte de norme. Pour le voir de manière formelle, on définit l'ensemble autorisé

$$
C=
\left\{
D=(d_1,\ldots,d_K)
\;:\;
\lVert d_k\rVert_2\leq1
\text{ pour tout }k
\right\},
$$

puis la fonction indicatrice

$$
\iota_C(D)
=
\begin{cases}
0 & \text{si }D\in C,\\
+\infty & \text{sinon}.
\end{cases}
$$

Cette fonction représente une règle stricte : toute solution hors de $C$ reçoit un coût infini. Son opérateur proximal est exactement la projection :

$$
\operatorname{prox}_{\iota_C}(D)
=\operatorname{proj}_C(D).
$$

On peut laisser cette écriture de côté lors d'une première lecture. Elle sert surtout à montrer que la projection des motifs et le seuillage doux des activations, étudié ci-dessous, relèvent du même cadre mathématique.

Pour les activations, avec $D$ fixé, l'objectif prend la forme

$$
F(Z)
=
\underbrace{
\left\lVert
x-\sum_{k=1}^{K}z_k*d_k
\right\rVert_2^2
}_{f(Z,D)\text{ : erreur de reconstruction}}
+
\underbrace{
\lambda\sum_{k=1}^{K}\lVert z_k\rVert_1
}_{\text{pénalité de parcimonie}}.
$$

Le premier terme $f(Z,D)$ est lisse : de petites variations de $Z$ produisent des variations continues de son gradient. Le second contient des valeurs absolues. Pour un scalaire $a\in\mathbb{R}$,

$$
\frac{\mathrm{d}|a|}{\mathrm{d}a}
=
\begin{cases}
-1 & \text{si }a<0,\\
+1 & \text{si }a>0,
\end{cases}
$$

mais cette dérivée n'est pas définie en $a=0$. Or, ce point est précisément important : on veut que beaucoup d'activations deviennent exactement nulles. Au lieu d'essayer de dériver naïvement tout l'objectif $F$, ISTA traite les deux termes séparément.

Avec $D$ fixé, l'algorithme ISTA effectue

$$
Z\leftarrow Z-\alpha\nabla_Z f(Z,D),
$$

puis

$$
Z\leftarrow S_{\lambda\alpha}(Z),
$$

où $S_\gamma$ est l'opérateur de **seuillage doux** :

$$
S_\gamma(y)[n]
=\operatorname{sign}(y[n])
\max\bigl(|y[n]|-\gamma,0\bigr).
$$

Pour un coefficient scalaire $a$, la même règle peut se lire morceau par morceau :

$$
S_\gamma(a)
=
\begin{cases}
a-\gamma & \text{si }a>\gamma,\\
0 & \text{si }|a|\leq\gamma,\\
a+\gamma & \text{si }a<-\gamma.
\end{cases}
$$

Pour chaque coefficient $y[n]$ :

- si $|y[n]|\leq\gamma$, il devient exactement $0$ ;
- s'il dépasse le seuil, son amplitude diminue de $\gamma$ sans changer de signe.

Le seuillage doux produit naturellement des activations clairsemées. Il formalise l'idée qu'un petit coefficient, insuffisamment utile pour réduire l'erreur, ne mérite pas d'être conservé.

> [!example] Seuillage doux
> Avec $\gamma=0.5$, les valeurs $(0.2,\;1.3,\;-0.8)$ deviennent
>
> $$
> (0,\;0.8,\;-0.3).
> $$
>
> La première contribution disparaît. Les deux autres restent actives, mais leur amplitude est réduite.

Le seuillage doux n'est pas une règle choisie arbitrairement. Il résout, pour chaque coefficient intermédiaire $y$, le petit problème d'optimisation

$$
\operatorname{prox}_{\gamma|\cdot|}(y)
=
\underset{a\in\mathbb{R}}{\operatorname{argmin}}
\left[
\frac{1}{2}(a-y)^2+\gamma|a|
\right].
$$

Dans cette formule, le premier terme demande de rester proche de $y$, qui est la valeur suggérée par le gradient. Le second préfère des coefficients de petite amplitude, idéalement nuls. Si $y$ est déjà faible, il coûte peu de le ramener à zéro et l'on supprime entièrement sa pénalité. Si $y$ est grand, le ramener à zéro serait trop éloigné de la valeur utile à la reconstruction : le meilleur compromis le réduit seulement de $\gamma$.

> [!example] Une étape ISTA, coefficient par coefficient
> Supposons qu'avant mise à jour, une activation vaille $z=1.0$, que son gradient de reconstruction soit $-0.6$ et que le pas soit $\alpha=0.5$. L'étape de gradient seule donne
>
> $$
> z_{\mathrm{intermédiaire}}
> =1.0-0.5(-0.6)
> =1.3.
> $$
>
> Le gradient indique donc qu'une activation plus forte améliorerait la reconstruction. Si $\gamma=\lambda\alpha=0.5$, le seuillage doux applique ensuite
>
> $$
> S_{0.5}(1.3)
> =1.3-0.5
> =0.8.
> $$
>
> La chaîne complète est $1.0\rightarrow1.3\rightarrow0.8$. Le gradient propose une activation utile ; la pénalité de parcimonie en retire une partie. Pour une valeur intermédiaire $0.2$, le même seuil donnerait directement $S_{0.5}(0.2)=0$ : c'est ainsi que de vrais zéros apparaissent.

On peut résumer ISTA par

$$
Z^{(q)}
\xrightarrow{\text{gradient}}
\widetilde Z
=Z^{(q)}-\alpha\nabla_Z f
\xrightarrow{\text{seuillage doux}}
Z^{(q+1)}
=S_{\lambda\alpha}(\widetilde Z),
$$

où $q\in\mathbb{N}$ est le numéro d'itération. La première flèche cherche une meilleure reconstruction ; la seconde conserve seulement les activations qui justifient leur coût de parcimonie.

### 12.4 Une itération complète sur un exemple minuscule

Le but de cet exemple est de relier concrètement les objets introduits jusque-là. On va effectuer une mise à jour du motif $d$ avec une activation $z$ fixée, puis constater que l'erreur diminue.

Pour alléger les indices de convolution, cette sous-section numérote les échantillons à partir de $0$. Cela ne change rien au modèle : c'est seulement une convention de calcul locale.

On prend un signal cible de longueur $4$ :

$$
x=
\begin{pmatrix}
3\\
2\\
1\\
0
\end{pmatrix}.
$$

On cherche à l'expliquer avec un seul motif de longueur $2$ et une seule activation :

$$
d=
\begin{pmatrix}
1\\
0
\end{pmatrix},
\qquad
z=
\begin{pmatrix}
2\\
1\\
0
\end{pmatrix}.
$$

Le vecteur $d$ décrit la forme que l'on souhaite apprendre. Le coefficient $z[t]$ indique à quel point cette forme est déposée à la position $t$. La longueur de $z$ vaut ici $4-2+1=3$, conformément à la convention expliquée précédemment.

#### 1. Reconstruire le signal

La reconstruction est la convolution

$$
\widetilde x=z*d.
$$

Par définition,

$$
(z*d)[t]
=\sum_v z[t-v]d[v].
$$

Le motif $d$ ne possède que deux coefficients, $d[0]=1$ et $d[1]=0$. La somme se réduit donc à

$$
(z*d)[t]
=z[t]d[0]+z[t-1]d[1]
=z[t]\times1+z[t-1]\times0.
$$

On prolonge $z$ par zéro hors de ses bornes. Les quatre positions de la convolution complète donnent :

$$
\begin{aligned}
(z*d)[0]&=z[0]\times1+z[-1]\times0=2,\\
(z*d)[1]&=z[1]\times1+z[0]\times0=1,\\
(z*d)[2]&=z[2]\times1+z[1]\times0=0,\\
(z*d)[3]&=z[3]\times1+z[2]\times0=0.
\end{aligned}
$$

La reconstruction actuelle vaut donc

$$
\widetilde x=
\begin{pmatrix}
2\\
1\\
0\\
0
\end{pmatrix}.
$$

Une autre lecture, souvent plus intuitive, consiste à regarder les copies du motif déposées par les activations :

$$
z[0]d=
\begin{pmatrix}
2\\
0
\end{pmatrix}
\quad\text{est placé à l'indice }0,
\qquad
z[1]d=
\begin{pmatrix}
1\\
0
\end{pmatrix}
\quad\text{est placé à l'indice }1.
$$

Après décalage et addition, ces copies donnent bien $(2,1,0,0)^\top$. La convolution signifie donc littéralement : « placer des copies pondérées du motif aux endroits indiqués par l'activation, puis les additionner ».

#### 2. Calculer le résidu et l'erreur

Le modèle reconstruit un signal trop petit au début. Le résidu est

$$
r=x-\widetilde x
=
\begin{pmatrix}
3\\
2\\
1\\
0
\end{pmatrix}
-
\begin{pmatrix}
2\\
1\\
0\\
0
\end{pmatrix}
=
\begin{pmatrix}
1\\
1\\
1\\
0
\end{pmatrix}.
$$

L'erreur quadratique associée est

$$
f=\lVert r\rVert_2^2
=1^2+1^2+1^2+0^2
=3.
$$

Le résidu positif aux trois premiers instants signifie que la reconstruction sous-estime le signal cible à ces endroits.

#### 3. Calculer le gradient du motif

On fixe $z$ et on ne met à jour que $d$. Pour un coefficient $d[u]$, le gradient est

$$
\frac{\partial f}{\partial d[u]}
=-2\sum_t r[t]z[t-u].
$$

Pour le premier coefficient, $u=0$ :

$$
\begin{aligned}
\frac{\partial f}{\partial d[0]}
&=-2\bigl(
r[0]z[0]+r[1]z[1]+r[2]z[2]+r[3]z[3]
\bigr)\\
&=-2\bigl(1\times2+1\times1+1\times0+0\times0\bigr)\\
&=-6.
\end{aligned}
$$

Pour le second coefficient, $u=1$ :

$$
\begin{aligned}
\frac{\partial f}{\partial d[1]}
&=-2\bigl(
r[0]z[-1]+r[1]z[0]+r[2]z[1]+r[3]z[2]
\bigr)\\
&=-2\bigl(1\times0+1\times2+1\times1+0\times0\bigr)\\
&=-6.
\end{aligned}
$$

Le gradient complet est donc

$$
\nabla_d f=
\begin{pmatrix}
-6\\
-6
\end{pmatrix}.
$$

Son signe négatif a une interprétation directe. Là où l'activation $z$ place le motif, le résidu est positif : le modèle prédit trop peu. Augmenter les deux coefficients de $d$ réduira localement ce résidu.

#### 4. Faire un pas de gradient

Prenons un pas d'apprentissage $\eta=0.05$. Avant de projeter le motif sur la contrainte de norme, le candidat obtenu par descente de gradient est

$$
\begin{aligned}
d_{\mathrm{candidat}}
&=d-\eta\nabla_d f\\
&=
\begin{pmatrix}
1\\
0
\end{pmatrix}
-0.05
\begin{pmatrix}
-6\\
-6
\end{pmatrix}\\
&=
\begin{pmatrix}
1.3\\
0.3
\end{pmatrix}.
\end{aligned}
$$

Sa reconstruction est

$$
\widetilde x_{\mathrm{candidat}}
=
\begin{pmatrix}
2.6\\
1.9\\
0.3\\
0
\end{pmatrix},
$$

car les valeurs successives sont $2\times1.3$, puis $1\times1.3+2\times0.3$, puis $1\times0.3$, puis $0$.

Le nouveau résidu et la nouvelle erreur, avant projection, sont

$$
r_{\mathrm{candidat}}
=
\begin{pmatrix}
0.4\\
0.1\\
0.7\\
0
\end{pmatrix},
\qquad
f_{\mathrm{candidat}}
=0.4^2+0.1^2+0.7^2
=0.66.
$$

L'erreur est passée de $3$ à $0.66$. C'est une illustration concrète du rôle du gradient : il indique une direction qui améliore localement la reconstruction.

> [!warning] La projection fait partie de la vraie mise à jour
> Dans cet exemple, $\lVert d_{\mathrm{candidat}}\rVert_2=\sqrt{1.3^2+0.3^2}>1$. Le candidat ne respecte donc pas encore la contrainte imposée aux atomes. L'algorithme réel applique ensuite la projection de la section 12.3 avant de conserver le nouveau motif. Le calcul précédent isole volontairement l'effet du pas de gradient ; il ne remplace pas l'étape proximale.

#### 5. Fermer la boucle alternée

Après avoir mis à jour puis projeté $d$, on le garde fixe et l'on met à jour $z$. Son gradient répond à la question complémentaire : « à quels instants et avec quelles amplitudes ce motif devrait-il être activé pour expliquer le résidu restant ? »

L'étape d'activation applique ensuite le seuillage doux, qui peut annuler les activations trop faibles. Une itération complète suit ainsi l'ordre

$$
\begin{array}{c}
\text{choisir }D,Z\\
\downarrow\\
\text{reconstruire }\widetilde x=\sum_k z_k*d_k\\
\downarrow\\
\text{calculer }r=x-\widetilde x\\
\downarrow\\
\text{mettre à jour et projeter }D\text{ avec }Z\text{ fixe}\\
\downarrow\\
\text{mettre à jour et seuiller }Z\text{ avec }D\text{ fixe}\\
\downarrow\\
\text{recommencer}.
\end{array}
$$

Sur ce minuscule exemple, les convolutions et corrélations se calculent à la main. Pour des signaux longs, la même boucle utilise les FFT de la section 12.2 pour accélérer les opérations centrales, sans modifier le principe de l'algorithme.

### 12.5 Ce que CDL apporte par rapport au matrix profile

| Matrix profile / distances | Dictionnaire convolutif |
| --- | --- |
| Cherche des fenêtres semblables dans le signal | Explique le signal par une somme de motifs appris |
| Met naturellement en évidence des paires, puis des groupes | Apprend plusieurs atomes et leurs activations |
| Très interprétable pour les répétitions directes | Représente mieux les mélanges et superpositions |
| Dépend d'une distance et d'une longueur $L$ | Dépend de $K$, $L$, $\lambda$, de l'initialisation et de l'optimisation |

Ces méthodes ne répondent pas exactement à la même version du problème. La première demande « quelles fenêtres se ressemblent ? ». La seconde demande « quelles briques réutilisables reconstruisent le signal ? ».

---

# Partie IV — Synthèse opérationnelle

## 13. Choisir une méthode : chemin de décision

1. **Le motif est-il connu ?**
   - Oui : calculer un profil de distance pour le détecter.
   - Non : chercher des répétitions avec le matrix profile ou apprendre un dictionnaire.

2. **Les occurrences sont-elles alignées et de même durée ?**
   - Oui : la distance euclidienne est un point de départ solide.
   - Non, mais les déformations temporelles sont limitées : envisager DTW contraint.

3. **Le niveau ou l'amplitude absolus sont-ils sans intérêt ?**
   - Oui : z-normaliser les fenêtres avant la comparaison.
   - Non : conserver les valeurs originales, car la normalisation effacerait une information potentiellement utile.

4. **Le signal est-il très long ?**
   - Recherche euclidienne : utiliser FFT et sommes cumulées.
   - Recherche DTW : utiliser des bornes inférieures, une contrainte de déformation et l'abandon précoce.

5. **Plusieurs phénomènes peuvent-ils se mélanger ?**
   - Oui : le modèle additif de dictionnaire convolutif devient particulièrement pertinent.

## 14. Pièges fréquents

### Confondre distance faible et détection validée

Une faible distance est un score de ressemblance, pas une preuve sémantique. Un signal peut contenir par hasard une forme proche du gabarit. Il faut fixer un seuil, vérifier les faux positifs et tenir compte des coûts d'erreur de l'application.

### Normaliser sans regarder ce que l'on retire

Z-normaliser retire exactement le niveau et l'amplitude globale de chaque fenêtre. Si une alarme dépend d'une amplitude anormalement élevée, cette information disparaît de la distance normalisée. Une solution peut être de conserver en parallèle les statistiques $\mu_x[n]$ et $\sigma_x[n]$ comme variables complémentaires.

### Choisir une longueur de fenêtre arbitraire

La longueur $N_p$ en détection ou $L$ en extraction définit l'échelle temporelle de la forme recherchée. Une fenêtre trop courte coupe la structure ; une fenêtre trop longue mélange le motif avec son contexte. Le cours simplifie l'extraction en supposant toutes les occurrences de même longueur, hypothèse parfois restrictive.

### Donner trop de liberté à DTW

Un DTW non contraint peut faire correspondre des segments éloignés ou répéter fortement certaines valeurs. La bande de Sakoe-Chiba encode une connaissance utile sur la vitesse relative des phénomènes et réduit le risque de sur-alignement.

### Oublier les matches triviaux dans la recherche de motifs

Une fenêtre et son voisin immédiat sont presque identiques par construction. Sans zone d'exclusion, l'algorithme découvre seulement la continuité du signal, pas des motifs récurrents.

### Croire que CDL renvoie une vérité unique

Le dictionnaire appris dépend de $K$, $L$, $\lambda$, de l'initialisation et de l'algorithme. Deux exécutions peuvent fournir des atomes différents mais des reconstructions comparables. Il faut inspecter à la fois les formes apprises, leurs activations et le résidu.

## 15. Vocabulaire à retenir

- **Motif / pattern** : forme courte d'intérêt dans une série.
- **Gabarit / template** : exemple de motif déjà connu.
- **Occurrence** : emplacement où un motif apparaît.
- **Fenêtre / sous-séquence** : portion contiguë d'une série.
- **Profil de distance** : distance entre un gabarit fixé et toutes les fenêtres d'un signal.
- **Z-normalisation** : centrage par la moyenne et division par l'écart-type.
- **DTW** : alignement dynamique qui autorise des déformations monotones du temps.
- **Chemin d'alignement** : paires d'indices qui définissent les correspondances sous DTW.
- **Borne inférieure** : quantité inférieure ou égale à la vraie distance, utilisée pour rejeter vite des candidats.
- **Matrix profile** : pour chaque fenêtre, distance vers son voisin non recouvrant le plus proche.
- **Motif pair** : deux occurrences très semblables et séparées.
- **Motif set** : ensemble des occurrences d'un même motif.
- **Dictionnaire convolutif** : ensemble de motifs courts réutilisés à différents instants.
- **Activation** : coefficient qui indique où et avec quelle amplitude un atome est utilisé.
- **Parcimonie** : préférence pour un petit nombre de coefficients non nuls.
- **Opérateur proximal** : étape qui impose une contrainte ou une pénalité après un pas de gradient.

## 16. À retenir en une page

> [!summary]
> - Comparer deux séries commence par décider quelles déformations doivent être ignorées.
> - La distance euclidienne compare les échantillons à temps égal. Elle est rapide, mais exige un alignement strict.
> - La z-normalisation rend la comparaison invariante au niveau et à l'amplitude positive ; elle peut amplifier le bruit des fenêtres quasi constantes.
> - DTW cherche le meilleur chemin d'alignement monotone. Il gère les variations de vitesse, au prix d'un calcul plus lourd et de propriétés métriques moins favorables.
> - La détection d'un motif connu consiste à calculer un profil de distance et à chercher ses minima. FFT et sommes cumulées rendent le cas euclidien scalable.
> - Pour DTW, les bornes comme LB Kim et LB Keogh évitent de calculer la distance exacte pour des candidats qui ne peuvent pas gagner.
> - Le matrix profile découvre des paires de sous-séquences récurrentes sans gabarit préalable, à condition d'exclure les recouvrements triviaux.
> - L'apprentissage de dictionnaire convolutif explique une série comme une somme de motifs activés de manière parcimonieuse. Il est adapté aux motifs multiples et aux superpositions, mais demande des hyperparamètres et une optimisation alternée.

## 17. Ressources citées dans les slides

Les slides renvoient notamment à :

- Berndt & Clifford (1994), *Using Dynamic Time Warping to Find Patterns in Time Series* ;
- Sakoe & Chiba (1978), contrainte de bande pour DTW ;
- Mueen et al. (2015), recherche rapide de sous-séquences sous distance euclidienne ;
- Rakthanmanon et al. (2012), recherche DTW à très grande échelle ;
- Keogh & Ratanamahatana (2004), LB Keogh ;
- Yeh et al. (2016), *Matrix Profile* ;
- Grosse et al. (2007) et Wohlberg (2014), codage parcimonieux convolutif.

Pour une mise en pratique, les ressources mentionnées dans le cours sont notamment [[https://www.aeon-toolkit.org|aeon]] pour plusieurs distances élastiques, [[https://tslearn.readthedocs.io|tslearn]] pour DTW et ses variantes, [[https://stumpy.readthedocs.io|stumpy]] pour le matrix profile, et [[https://alphacsc.github.io|alphacsc]] pour le codage parcimonieux convolutif.

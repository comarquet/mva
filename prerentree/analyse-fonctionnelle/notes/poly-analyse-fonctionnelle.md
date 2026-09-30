---
title: "Poly intuitif — Analyse fonctionnelle"
aliases:
  - Analyse fonctionnelle — notions essentielles
tags:
  - mva
  - pre-rentree
  - analyse-fonctionnelle
  - poly
source: "[[../sources/analyse-fonctionnelle-cours.pdf|Support de cours]]"
---

# Analyse fonctionnelle — poly intuitif

> [!abstract] Fil conducteur
> L'analyse fonctionnelle étudie des **espaces de fonctions** comme on étudie des espaces de vecteurs. L'idée décisive est de choisir une notion de taille (une norme) et, lorsque c'est possible, une notion d'angle (un produit scalaire). On peut alors parler de limites, d'approximation, de projection, de dérivée faible et de minimisation d'énergie.

Ce poly reprend les notions du support de manière volontairement intuitive. Les formules sont là pour fixer les idées ; le point important est surtout de savoir **ce qu'elles disent** et **quand les utiliser**.

## 0. Le socle à avoir en tête

Les prérequis sont organisés en notes séparées pour pouvoir les relire indépendamment :

- [[fiches-aide/00-socle-analyse-fonctionnelle|Vue d’ensemble du socle]]
- [[fiches-aide/01-presque-partout|Presque partout : ensembles négligeables et égalité presque partout]]
- [[fiches-aide/02-normes-et-convergence|Normes et convergence : ce que signifie « être proche »]]
- [[fiches-aide/03-completude|Complétude : suites de Cauchy et limites]]
- [[fiches-aide/04-densite|Densité : approcher avec des fonctions simples]]

---

## 1. Intégrer et passer à la limite

### 1.1. Intégrabilité et l'espace $L^1$

Une fonction est dans $L^1(\Omega)$ lorsque son aire totale en valeur absolue est finie :

$$
\int_\Omega |f(x)|\,dx < \infty.
$$

La valeur absolue est essentielle. Une intégrale sans valeur absolue peut faire disparaître deux grandes contributions de signes opposés ; la norme $L^1$, elle, compte tout ce qui est présent, sans compensation. On l'interprète donc comme une **masse totale**, une quantité totale de matière, ou encore l'aire totale comprise entre le graphe et l'axe des abscisses.

> [!example] L'intégrale signée peut être trompeuse
> Soit $f=1$ sur $(0,1)$, $f=-1$ sur $(1,2)$, et $f=0$ ailleurs. Alors
> $$
> \int_{\mathbb R}f(x)\,dx=0,
> \qquad
> \|f\|_{L^1(\mathbb R)}=\int_{\mathbb R}|f(x)|\,dx=2.
> $$
> Le bilan net est nul, mais il y a bien une unité de masse positive et une unité de masse négative. La norme $L^1$ mesure l'intensité totale, pas seulement le bilan.

#### Parties positive et négative

Pour distinguer ce qui est ajouté de ce qui est retiré, on écrit

$$
f^+(x)=\max(f(x),0),
\qquad
f^-(x)=\max(-f(x),0).
$$

Ainsi, $f=f^+-f^-$ et $|f|=f^++f^-$. Dire que $f\in L^1(\Omega)$ revient à demander que **les deux masses**

$$
\int_\Omega f^+(x)\,dx
\qquad \text{et} \qquad
\int_\Omega f^-(x)\,dx
$$

soient finies. Dans ce cas seulement, l'intégrale signée a un sens non ambigu :

$$
\int_\Omega f(x)\,dx
=
\int_\Omega f^+(x)\,dx-\int_\Omega f^-(x)\,dx.
$$

Cette précaution évite l'expression indéterminée « $\infty-\infty$ ». Une fonction peut avoir autant de masse positive que négative, mais si ces deux masses sont infinies, on ne peut pas conclure que son intégrale vaut $0$.

#### Lire la norme $L^1$

La norme

$$
\|f\|_{L^1(\Omega)}=\int_\Omega |f(x)|\,dx
$$

possède plusieurs lectures équivalentes :

- en physique, c'est la masse totale quand $f\geq0$ ;
- pour une densité d'erreur, c'est l'erreur totale accumulée ;
- en traitement du signal, elle pénalise l'amplitude sur toute la durée du signal ;
- géométriquement, c'est l'aire entre le graphe de $f$ et l'axe horizontal.

Elle ignore les modifications sur un ensemble de mesure nulle. Modifier $f$ en un point, ou même sur un ensemble dénombrable, ne change ni son intégrale ni sa norme $L^1$.

> [!tip] Un test de bon sens
> Pour vérifier qu'une fonction est dans $L^1$, il faut examiner les zones où elle peut accumuler une aire infinie : près d'une singularité, à l'infini, ou sur une région de grande mesure. Une fonction bornée n'est pas nécessairement dans $L^1$ sur un domaine infini : la constante $1$ appartient à $L^\infty(\mathbb R)$, mais pas à $L^1(\mathbb R)$.

#### Deux comportements opposés près d'une singularité

Sur $(0,1)$, les fonctions $x\mapsto x^{-\alpha}$ illustrent le seuil critique :

$$
x^{-\alpha}\in L^1(0,1)
\quad\Longleftrightarrow\quad
\alpha<1.
$$

En effet, pour $\alpha<1$,

$$
\int_0^1x^{-\alpha}\,dx
=
\frac{1}{1-\alpha},
$$

qui est fini. La fonction $x\mapsto x^{-1/2}$ est donc dans $L^1(0,1)$, et sa norme vaut $2$. Sa valeur explose bien quand $x$ tend vers $0$, mais pas assez vite pour que l'aire devienne infinie.

À l'inverse, pour $f(x)=1/x$,

$$
\int_\varepsilon^1\frac{1}{x}\,dx
=
-\log(\varepsilon)
\xrightarrow[\varepsilon\to0^+]{}+\infty.
$$

La singularité de $1/x$ est juste assez forte pour produire une aire infinie : $1/x\notin L^1(0,1)$.

> [!example] Ce qui se passe à l'infini
> Sur $(1,+\infty)$, le seuil s'inverse :
> $$
> x^{-\beta}\in L^1(1,+\infty)
> \quad\Longleftrightarrow\quad
> \beta>1.
> $$
> Par exemple, $1/x^2$ décroît assez vite pour avoir une masse totale finie, tandis que $1/x$ garde trop de masse dans sa longue traîne.

#### Intégrable au sens de Lebesgue ou seulement par compensation ?

On ne parle pas, en général, de « fonction impropre », mais d'**intégrale impropre**. C'est une intégrale qui ne peut pas être calculée directement sur son domaine parce que :

- le domaine est infini, comme $(1,+\infty)$ ;
- ou la fonction devient non bornée à une extrémité ou en un point du domaine, comme $1/x$ au voisinage de $0$.

On lui donne un sens en coupant d'abord la partie problématique, puis en faisant tendre la coupure vers la limite. Par exemple,

$$
\int_1^{+\infty} f(x)\,dx
=
\lim_{R\to+\infty}\int_1^R f(x)\,dx,
$$

à condition que cette limite existe et soit finie. De même, si $f$ explose en $a$,

$$
\int_a^b f(x)\,dx
=
\lim_{\varepsilon\to0^+}\int_{a+\varepsilon}^b f(x)\,dx.
$$

La condition $f\in L^1$ est plus forte que le fait qu'une intégrale impropre signée converge. Par exemple,

$$
\int_1^{+\infty}\frac{\sin(x)}{x}\,dx
$$

converge grâce aux oscillations de $\sin(x)$, mais $\sin(x)/x$ n'est pas dans $L^1(1,+\infty)$ :

$$
\int_1^{+\infty}\frac{|\sin(x)|}{x}\,dx=+\infty.
$$

Cette distinction est importante en analyse fonctionnelle. L'intégrabilité absolue donne des résultats robustes : on peut notamment contrôler les erreurs en norme $L^1$, appliquer les théorèmes de convergence et utiliser Fubini dans les bonnes conditions. Les compensations de signe seules sont plus fragiles.

### 1.2. Pourquoi les théorèmes de convergence sont nécessaires

On aimerait souvent écrire

$$
\lim_{n\to\infty}\int_\Omega f_n(x)\,dx
=
\int_\Omega \left(\lim_{n\to\infty}f_n(x)\right)\,dx.
$$

Ici, $x$ et $n$ jouent deux rôles très différents :

- $x$ est la **position** dans le domaine $\Omega$ : une fois $x$ choisi, $f_n(x)$ est un nombre ;
- $n$ est le **numéro de l'étape** dans une suite de fonctions : $f_1,f_2,f_3,\ldots$ sont des fonctions différentes.

Ainsi, $f_n$ ne désigne pas une nouvelle variable : c'est la $n$-ième fonction de la suite. La fonction $f$ est la **fonction limite**. Elle est définie point par point, lorsque la limite existe, par

$$
f(x)=\lim_{n\to\infty}f_n(x).
$$

> [!example] Une suite de fonctions vue en un point
> Sur $(0,1)$, si $f_n(x)=x^n$, alors $f_1(x)=x$, $f_2(x)=x^2$, $f_3(x)=x^3$, etc. Pour un $x$ fixé strictement entre $0$ et $1$, les valeurs $x,x^2,x^3,\ldots$ deviennent de plus en plus petites. La fonction limite est donc $f(x)=0$ sur $(0,1)$. Ici, $f$ n'est pas « $f_n$ sans l'indice » : c'est la fonction obtenue après avoir laissé $n$ tendre vers l'infini.

On peut lire cette égalité comme deux recettes différentes.

**Recette de gauche : aire, puis limite.**

1. Pour chaque $n$, on calcule l'aire signée entière sous la courbe $f_n$ : $I_n=\int_\Omega f_n(x)\,dx$.
2. On obtient une suite de nombres $I_1,I_2,I_3,\ldots$.
3. On regarde la limite de ces nombres : $\lim_{n\to\infty}I_n$.

**Recette de droite : limite de la courbe, puis aire.**

1. On choisit un point $x$ et on observe la suite de hauteurs $f_1(x),f_2(x),f_3(x),\ldots$.
2. Si ces hauteurs ont une limite, on la note $f(x)$.
3. On recommence pour chaque $x$ : cela fabrique la courbe limite $f$.
4. On calcule ensuite son aire : $\int_\Omega f(x)\,dx$.

> [!example] Le contre-exemple à visualiser
> Sur $(0,1)$, posons
> $$
> f_n(x)=n\,\mathbf 1_{(0,1/n)}(x).
> $$
> La courbe $f_n$ est un rectangle très haut, de hauteur $n$, mais très fin, de largeur $1/n$. Son aire vaut toujours
> $$
> \int_0^1 f_n(x)\,dx=n\times\frac1n=1.
> $$
> La recette de gauche donne donc $1$.
>
> Maintenant, fixons un point précis, par exemple $x=0{,}1$. Pour $n=2$, le rectangle occupe $(0,1/2)$ : il contient $0{,}1$, donc $f_2(0{,}1)=2$. Pour $n=20$, il n'occupe plus que $(0,1/20)=(0,0{,}05)$ : il ne contient plus $0{,}1$, donc $f_{20}(0{,}1)=0$. Il en sera de même pour tous les rangs suivants.
>
> Le rectangle ne se déplace pas : il reste collé à $0$, devient de plus en plus étroit et de plus en plus haut.
>
> C'est toujours le même mécanisme. Si $x>0$ est fixé, on peut choisir un entier $N$ tel que $1/N<x$. Dès que $n\geq N$, on a aussi $1/n<x$, donc $x\notin(0,1/n)$. L'indicatrice $\mathbf 1_{(0,1/n)}(x)$ vaut alors $0$, et par conséquent $f_n(x)=0$. Ainsi, pour chaque point fixe de $(0,1)$, la suite $f_n(x)$ finit par être constamment nulle ; elle converge donc vers $0$.
>
> La courbe limite est donc $f=0$ sur $(0,1)$. Oui, dans la recette de droite, on peut alors remplacer $f(x)$ par $0$ dans l'intégrale : ce n'est pas une approximation, mais une égalité, puisque $f(x)=0$ pour tout $x\in(0,1)$. On obtient
> $$
> \int_0^1f(x)\,dx=\int_0^1 0\,dx=0.
> $$
>
> En revanche, on ne peut pas remplacer $f_n(x)$ par $0$ dans les intégrales $\int_0^1f_n(x)\,dx$ avant de les calculer : pour tout $n$, $f_n$ n'est pas la fonction nulle et son aire vaut $1$. C'est exactement la différence entre les deux recettes.
>
> Ici, « limite puis aire » donne $0$, tandis que « aire puis limite » donne $1$. La masse ne disparaît pas : elle se concentre juste tout près de $0$, dans une zone que chaque point fixe finit par ne plus voir.

Écrire une égalité entre les deux recettes revient à **échanger une limite et une intégrale**. Cet échange est faux en général ; les théorèmes suivants donnent des hypothèses qui empêchent la masse de se concentrer ainsi ou de s'échapper à l'infini.

#### Convergence monotone — Beppo Levi

Le théorème de convergence monotone s'applique lorsque les fonctions s'empilent **par dessous** :

$$
0\le f_1(x)\le f_2(x)\le\cdots
\qquad \text{et} \qquad
f_n(x)\xrightarrow[n\to\infty]{}f(x)
$$

pour presque tout $x$. Alors

$$
\int_\Omega f_n(x)\,dx
\xrightarrow[n\to\infty]{}
\int_\Omega f(x)\,dx.
$$

L'égalité est même valable si les deux membres tendent vers $+\infty$. Si les intégrales de $f_n$ restent bornées, la limite $f$ est intégrable et l'on obtient une véritable convergence dans $L^1$.

L'intuition est très simple : chaque étape ajoute de la masse positive, sans jamais en retirer. Il n'y a donc ni annulation de signes, ni masse qui disparaît mystérieusement ; l'aire limite est exactement l'aire obtenue en laissant les aires croissantes s'accumuler.

> [!example] Des intervalles qui remplissent $(0,1)$
> Sur $(0,1)$, posons $f_n=\mathbf 1_{(1/n,1)}$. Pour tout $x>0$, la suite vaut finalement $1$ ; elle croît donc vers la fonction constante $f=1$. De plus,
> $$
> \int_0^1 f_n(x)\,dx=1-\frac1n
> \xrightarrow[n\to\infty]{}1
> =
> \int_0^1f(x)\,dx.
> $$
> Le théorème formalise ici une évidence géométrique : les intervalles $(1/n,1)$ remplissent progressivement presque tout $(0,1)$.

> [!example] Tronquer une fonction positive
> Si $f\geq0$, on définit, pour chaque $n$,
> $$
> f_n(x)=\min(f(x),n).
> $$
> Autrement dit,
> $$
> f_n(x)=
> \begin{cases}
> f(x) & \text{si }f(x)\leq n,\\
> n & \text{si }f(x)>n.
> \end{cases}
> $$
> La partie de la courbe située sous la hauteur $n$ est inchangée ; seule la partie qui dépasse est aplatie. Quand $n$ augmente, le plafond monte : on ne retire jamais rien, donc $f_n(x)\leq f_{n+1}(x)\leq f(x)$. Pour tout point où $f(x)$ est fini, dès que $n\geq f(x)$, le plafond est au-dessus de la courbe et $f_n(x)=f(x)$. C'est pourquoi $f_n(x)\to f(x)$.
>
> Chaque $f_n$ est bornée par $n$. Sur un domaine de mesure finie, cela la rend automatiquement intégrable, même lorsque la fonction de départ possède un pic difficile à manipuler.
>
> Prenons $f(x)=1/\sqrt{x}$ sur $(0,1)$. Elle explose près de $0$, mais est dans $L^1(0,1)$. Sa troncature vaut
> $$
> f_n(x)=
> \begin{cases}
> n & \text{si }0<x<1/n^2,\\
> 1/\sqrt{x} & \text{si }1/n^2\leq x<1.
> \end{cases}
> $$
> Le très haut pic près de $0$ est donc remplacé par un petit rectangle de hauteur $n$ et de largeur $1/n^2$. L'aire totale de $f_n$ se découpe en deux morceaux :
>
> - sur $(0,1/n^2)$, l'aire du rectangle vaut $n\times\frac1{n^2}=\frac1n$ ;
> - sur $(1/n^2,1)$, la courbe n'est pas modifiée : son aire est celle de $1/\sqrt{x}$.
>
> On obtient donc
> $$
> \int_0^1f_n(x)\,dx
> =
> \frac1n+\int_{1/n^2}^1\frac{1}{\sqrt{x}}\,dx
> =
> \frac1n+\left[2\sqrt{x}\right]_{1/n^2}^1
> =
> \frac1n+2\left(1-\frac1n\right)
> =
> 2-\frac1n
> \xrightarrow[n\to\infty]{}2.
> $$
> Le rectangle devient de plus en plus haut, mais son aire $1/n$ tend vers $0$. En même temps, la partie non tronquée de la courbe commence de plus en plus près de $0$ : elle reconstitue progressivement toute l'aire sous $1/\sqrt{x}$.
>
> L'aire de la courbe initiale se calcule comme une intégrale impropre. Le nombre $\varepsilon>0$ évite d'abord le point $0$, où $1/\sqrt{x}$ explose. Sur l'intervalle $[\varepsilon,1]$, la fonction est continue et possède la primitive
> $$
> F(x)=2\sqrt{x},
> \qquad \text{car} \qquad
> F'(x)=\frac1{\sqrt{x}}.
> $$
> On peut donc calculer l'intégrale ordinaire :
> $$
> \int_\varepsilon^1\frac1{\sqrt{x}}\,dx
> =
> \left[2\sqrt{x}\right]_\varepsilon^1
> =
> 2\sqrt{1}-2\sqrt{\varepsilon}
> =
> 2(1-\sqrt{\varepsilon}).
> $$
> Enfin, quand $\varepsilon\to0^+$, on a $\sqrt{\varepsilon}\to0$. Par définition de l'intégrale impropre,
> $$
> \int_0^1x^{-1/2}\,dx
> =
> \lim_{\varepsilon\to0^+}\int_\varepsilon^1\frac1{\sqrt{x}}\,dx
> =
> \lim_{\varepsilon\to0^+}2(1-\sqrt{\varepsilon})
> =
> 2.
> $$
> Ici, le calcul donne explicitement la réponse. Beppo Levi donne la raison générale : comme les $f_n$ sont positives et croissent vers $f$, leurs aires doivent tendre vers l'aire de $f$, même lorsqu'un calcul aussi direct n'est pas disponible.
>
> Si, au contraire, $f$ n'est pas intégrable, la même construction reste valable. Prenons par exemple
> $$
> f(x)=\frac1x
> \qquad \text{sur }(0,1).
> $$
> Cette fonction n'est pas dans $L^1(0,1)$, car son aire explose près de $0$. Pourtant, ses troncatures
> $$
> f_n(x)=\min\left(\frac1x,n\right)
> $$
> sont bornées par $n$ et donc intégrables sur $(0,1)$. Pour déterminer laquelle des deux valeurs est le minimum, on compare $1/x$ à $n$. Comme $x>0$,
> $$
> \frac1x>n
> \quad\Longleftrightarrow\quad
> 1>nx
> \quad\Longleftrightarrow\quad
> x<\frac1n.
> $$
> Ainsi, si $0<x<1/n$, la valeur $1/x$ est plus grande que $n$ : le minimum est donc $n$. À l'inverse, si $1/n\leq x<1$, on a $1/x\leq n$ : le minimum est $1/x$. Elles valent donc
> $$
> f_n(x)=
> \begin{cases}
> n & \text{si }0<x<1/n,\\
> 1/x & \text{si }1/n\leq x<1.
> \end{cases}
> $$
> Leur aire se calcule de la même façon :
> $$
> \int_0^1f_n(x)\,dx
> =
> n\times\frac1n+\int_{1/n}^1\frac1x\,dx
> =
> 1+\log n
> \xrightarrow[n\to\infty]{}+\infty.
> $$
> Les $f_n$ sont toujours positives et croissent point par point vers $f$. Beppo Levi affirme donc bien
> $$
> \lim_{n\to\infty}\int_0^1f_n(x)\,dx
> =
> \int_0^1f(x)\,dx
> =
> +\infty.
> $$
> Le théorème ne dit pas ici que $f$ appartient à $L^1$ ; il décrit correctement le fait que sa masse totale est infinie. Pour conclure que la limite est dans $L^1$, il faut en plus savoir que les intégrales des $f_n$ restent uniformément bornées.

#### Convergence dominée — Lebesgue

Le théorème de convergence dominée ne demande ni positivité ni monotonie. Il suffit que $f_n$ converge vers $f$ presque partout et qu'une même fonction intégrable $h$ contrôle toutes les fonctions de la suite :

$$
|f_n(x)|\le h(x) \quad \text{pour presque tout }x \text{ et tout }n,
$$

avec $h\in L^1(\Omega)$. Alors $f\in L^1(\Omega)$ et

$$
\int_\Omega f_n(x)\,dx
\xrightarrow[n\to\infty]{}
\int_\Omega f(x)\,dx.
$$

Plus fort encore, les fonctions convergent en norme $L^1$ :

$$
\int_\Omega|f_n(x)-f(x)|\,dx
\xrightarrow[n\to\infty]{}0.
$$

Le majorant $h$ fournit un budget global fini : quelle que soit la valeur de $n$, $f_n$ ne peut pas cacher une masse plus grande que celle de $h$. Il empêche donc la masse de se concentrer ou de s'échapper pendant la limite.

> [!example] Une suite décroissante, mais dominée
> Sur $(0,1)$, prenons $f_n(x)=x^n$. Pour tout $x\in(0,1)$, on a $x^n\to0$. De plus,
> $$
> |x^n|\le1
> \qquad \text{et} \qquad
> 1\in L^1(0,1).
> $$
> La convergence dominée donne donc
> $$
> \int_0^1x^n\,dx=\frac1{n+1}
> \xrightarrow[n\to\infty]{}0
> =
> \int_0^1 0\,dx.
> $$
> Cette suite décroît sur $(0,1)$ ; elle ne relève donc pas de Beppo Levi, mais elle relève parfaitement de la convergence dominée.

> [!example] Le rôle concret du majorant
> Considérons
> $$
> f_n(x)=n\,\mathbf 1_{(0,1/n)}(x).
> $$
> Chaque $f_n$ est un rectangle de hauteur $n$ et de largeur $1/n$, collé à $0$. Son aire vaut toujours
> $$
> \int_0^1f_n(x)\,dx=n\times\frac1n=1.
> $$
> Les aires ne tendent donc pas vers $0$.
>
> **Ce que voit un point fixe.** Prenons par exemple $x=0{,}2$. Pour $n=2$, le rectangle occupe $(0,0{,}5)$, donc il contient $0{,}2$ et $f_2(0{,}2)=2$. Pour $n=6$, il n'occupe plus que $(0,1/6)$, qui s'arrête avant $0{,}2$ : $f_6(0{,}2)=0$. À partir de là, tous les rectangles suivants sont encore plus étroits et la valeur reste $0$.
>
> C'est le même raisonnement pour n'importe quel $x>0$. On choisit un rang assez grand, par exemple un entier $N>1/x$. Pour tout $n\geq N$, on a $1/n\leq1/N<x$. Le point $x$ est donc hors de l'intervalle $(0,1/n)$, et $f_n(x)=0$. La suite $f_n(x)$ finit ainsi par être nulle pour chaque $x\in(0,1)$ : elle converge point par point vers $0$.
>
> **Ce qu'exigerait un majorant commun.** Un majorant est une seule fonction $h$, qui ne dépend pas de $n$ et qui devrait vérifier $f_n(x)\leq h(x)$ pour toutes les valeurs de $n$. Il ne suffit donc pas de majorer chaque rectangle séparément ; $h$ doit recouvrir tous les rectangles à la fois.
>
> Supposons qu'une fonction $h\in L^1(0,1)$ vérifie $f_n\leq h$ pour tout $n$. Sur l'intervalle
> $$
> \left(\frac1{n+1},\frac1n\right),
> $$
> chaque point $x$ vérifie $0<x<1/n$. Il appartient donc au support $(0,1/n)$ de $f_n$. L'indicatrice $\mathbf 1_{(0,1/n)}(x)$ vaut alors $1$, et
> $$
> f_n(x)=n\times1=n.
> $$
> Comme $h$ doit être au-dessus de $f_n$, on doit avoir $h(x)\geq n$ sur tout cet intervalle. Par exemple, $h$ devrait être au moins égale à $2$ sur $(1/3,1/2)$, à $3$ sur $(1/4,1/3)$, à $4$ sur $(1/5,1/4)$, et ainsi de suite.
>
> Ces intervalles sont choisis parce qu'ils sont disjoints et s'empilent vers $0$. On peut donc additionner leurs aires minimales sans compter deux fois la même zone. L'aire minimale que $h$ doit avoir sur le $n$-ième intervalle est sa hauteur minimale $n$ multipliée par sa largeur :
>
> $$
> n\left(\frac1n-\frac1{n+1}\right)=\frac1{n+1}.
> $$
> En additionnant ces aires minimales sur tous les intervalles disjoints, on obtient
> $$
> \int_0^1h(x)\,dx
> \geq
> \sum_{n=1}^{+\infty}\frac1{n+1}
> =
> +\infty.
> $$
> La série obtenue est
> $$
> \frac12+\frac13+\frac14+\frac15+\cdots.
> $$
> C'est la série harmonique à laquelle il manque seulement le premier terme $1$ ; elle diverge donc elle aussi. Pour le voir sans formule avancée, on peut regrouper ses termes :
> $$
> \frac12
> +\left(\frac13+\frac14\right)
> +\left(\frac15+\frac16+\frac17+\frac18\right)
> +\cdots.
> $$
> Le deuxième groupe est au moins $2\times\frac14=\frac12$, le troisième est au moins $4\times\frac18=\frac12$, et chaque groupe suivant contient deux fois plus de termes, chacun au moins deux fois plus petit. Chaque groupe vaut donc au moins $1/2$. En ajoutant indéfiniment des groupes d'au moins $1/2$, la somme dépasse n'importe quel nombre : elle vaut $+\infty$.
>
> C'est impossible puisque $h$ était censée être intégrable. Toute enveloppe qui couvre ces rectangles doit elle aussi devenir trop haute près de $0$ et possède une aire infinie.
>
> Ce contre-exemple oppose donc deux points de vue : chaque point fixe finit par voir $0$, mais l'aire totale de chaque rectangle reste égale à $1$. La convergence point par point vers $0$ ne suffit pas à conclure que les intégrales tendent vers $0$ ; la domination par une enveloppe intégrable est précisément ce qui interdit cette concentration de masse.

> [!tip] À retenir
> - Positivité + croissance : penser **Beppo Levi**.
> - Convergence presque partout + une enveloppe intégrable : penser **convergence dominée**.
> - Sans une de ces protections, vérifier le passage à la limite au lieu de le supposer.

### 1.3. Densité des fonctions continues à support compact

La notation

$$
C_c(\Omega)
$$

désigne les fonctions définies sur $\Omega$ qui sont à la fois **continues** et à **support compact**. Le $C$ vient de « continue » ; le $c$ en indice vient de « compact ».

#### Qu'est-ce qu'une fonction à support compact ?

Le **support** d'une fonction $g$ est, intuitivement, la région où elle n'est pas nulle. Dire que $g$ est à support compact signifie qu'il existe une zone compacte $K$ contenue dans $\Omega$ telle que

$$
g(x)=0
\qquad \text{dès que }x\notin K.
$$

Dans $\mathbb R^d$, une zone compacte peut se représenter sans difficulté comme une région **fermée et bornée** : elle tient dans une grande boîte ou une grande boule, et elle contient son bord. Sur $\mathbb R$, un intervalle fermé et borné comme $[-R,R]$ est compact. Ainsi, une fonction à support compact est exactement nulle suffisamment loin ; lorsque $\Omega$ est ouvert, elle est aussi nulle dans un voisinage de son bord.

> [!example] Une fonction « chapeau »
> Sur $\mathbb R$, la fonction
> $$
> g(x)=\max(1-|x|,0)
> $$
> est continue. Elle est non nulle seulement sur $[-1,1]$ et nulle en dehors : son support est donc compact. Elle appartient à $C_c(\mathbb R)$. On peut imaginer sa courbe comme un chapeau triangulaire posé entre $-1$ et $1$.

Ces fonctions sont particulièrement confortables pour les calculs : leur continuité évite les sauts, et leur support compact évite de devoir gérer ce qui se passe à l'infini. Lors d'une intégration par parties, par exemple, elles ne créent pas de terme de bord à l'infini.

#### Que signifie « dense dans $L^1$ » ?

Dire que $C_c(\Omega)$ est dense dans $L^1(\Omega)$ signifie que toute fonction $f\in L^1(\Omega)$ peut être approchée aussi bien que l'on veut, **au sens de l'aire de l'erreur**. Formellement, pour tout $\varepsilon>0$, on peut trouver une fonction $g\in C_c(\Omega)$ telle que

$$
\|f-g\|_{L^1(\Omega)}
=
\int_\Omega|f(x)-g(x)|\,dx
<\varepsilon.
$$

Il ne s'agit pas forcément d'avoir $g(x)$ proche de $f(x)$ en chaque point : le critère porte sur l'erreur totale. Une grande erreur sur une zone très petite peut être acceptable en norme $L^1$.

Le même principe vaut dans $L^p$ pour $1\leq p<\infty$, en remplaçant la norme $L^1$ par la norme $L^p$.

En revanche, l'approximation par des fonctions continues ne donne pas en général une approximation arbitrairement bonne pour la norme $L^\infty$. Cette norme regarde la **pire erreur ponctuelle** :

$$
\|f-g\|_{L^\infty}
=
\text{la plus grande valeur essentielle de }|f(x)-g(x)|.
$$

Pour une fonction qui possède un saut, une fonction continue doit forcément effectuer une transition entre les deux hauteurs. Même si l'on concentre cette transition sur un intervalle minuscule, il existe toujours un point où la fonction continue est à mi-chemin entre les deux valeurs : l'erreur y est grande.

> [!example] Lisser une marche : petit en $L^1$, impossible en $L^\infty$
> Reprenons $f=\mathbf 1_{(0,1/2)}$ sur $(0,1)$. Une fonction continue $g$ qui cherche à imiter $f$ doit passer progressivement de $1$ à gauche de $1/2$ à $0$ à droite. Elle doit donc prendre une valeur proche de $1/2$ pendant sa transition.
>
> À ce point, $f$ vaut soit $1$, soit $0$, tandis que $g$ vaut environ $1/2$. L'erreur vaut donc environ $1/2$. En fait, toute fonction continue $g$ vérifie
> $$
> \|f-g\|_{L^\infty(0,1)}\geq\frac12.
> $$
> Il est impossible de rendre cette pire erreur plus petite que $1/2$, quelle que soit la finesse de la transition.
>
> En $L^1$, c'est différent : si la transition n'a lieu que sur un intervalle de largeur $2\delta$, l'erreur n'existe que dans cette bande. Son aire est petite et tend vers $0$ quand $\delta\to0$. Ainsi, on peut approcher la marche en $L^1$, mais pas en $L^\infty$ par des fonctions continues.

#### Qu'est-ce qu'une fonction « rugueuse » ?

Le mot **rugueuse** n'est pas une définition technique unique. Il désigne ici une fonction qui n'a pas la régularité pratique souhaitée : elle peut avoir un saut, un coin, des oscillations brusques ou simplement ne pas être continue.

> [!example] Une marche
> La fonction indicatrice $\mathbf 1_{(0,1/2)}$ sur $(0,1)$ vaut $1$ à gauche de $1/2$ et $0$ à droite. Elle possède un saut en $1/2$, donc elle n'est pas continue. On peut remplacer ce saut par une pente douce sur un très petit intervalle autour de $1/2$. Les deux fonctions ne diffèrent alors que sur cette petite zone, de sorte que leur distance $L^1$ devient aussi petite que souhaité.

#### Comment construire l'approximation ?

L'intuition se déroule en deux opérations.

1. **Couper les queues.** Si $f$ est définie sur un domaine infini, on choisit une grande région bornée $K_R$ et l'on remplace $f$ par une fonction qui coïncide avec elle au centre et devient nulle loin à l'extérieur. Comme $f\in L^1$, la masse laissée dans les queues peut être rendue arbitrairement petite.
2. **Lisser localement.** On remplace ensuite les sauts, coins et petites oscillations par des transitions douces sur des zones très petites. L'erreur ajoutée reste petite dans la norme choisie.

On obtient ainsi une fonction continue, nulle hors d'une zone compacte, mais quasiment indistinguable de $f$ en norme $L^1$ ou $L^p$. C'est ce qui permet de prouver d'abord une formule pour des fonctions faciles à manipuler, puis de l'étendre aux fonctions intégrables générales par passage à la limite.

> [!info] Pour approfondir
> La note [[fiches-aide/04-densite|Densité et familles d’approximation]] détaille la coupure d'une gaussienne, le lissage et la façon dont une propriété se transmet à la limite.

### 1.4. Intégrales doubles : Tonelli et Fubini

Pour une fonction $f(x,y)$, deux questions se posent : l'intégrale double existe-t-elle, et peut-on intégrer d'abord en $y$, puis en $x$ ?

- **Tonelli** : si $f\ge 0$, l'ordre des intégrales peut être interverti, même si le résultat vaut $+\infty$. C'est l'analogue continu du fait que l'ordre de sommation de termes positifs ne change pas la somme.
- **Fubini** : si $f$ est intégrable en valeur absolue sur le produit, alors les intégrales itérées existent presque partout, sont intégrables, et donnent la même valeur que l'intégrale double.

> [!example] Tonelli : l'aire d'un triangle
> Considérons la fonction indicatrice du triangle
> $$
> T=\{(x,y)\in[0,1]^2:x+y\leq1\},
> \qquad
> f(x,y)=\mathbf 1_T(x,y).
> $$
> La fonction est positive. Si l'on fixe $x$, les points du triangle ont $0\leq y\leq1-x$ ; intégrer d'abord en $y$ donne donc
> $$
> \int_0^1\left(\int_0^1f(x,y)\,dy\right)\,dx
> =
> \int_0^1(1-x)\,dx
> =
> \frac12.
> $$
> Si l'on fixe plutôt $y$, on obtient symétriquement $0\leq x\leq1-y$, donc
> $$
> \int_0^1\left(\int_0^1f(x,y)\,dx\right)\,dy
> =
> \int_0^1(1-y)\,dy
> =
> \frac12.
> $$
> Tonelli garantit que ces deux calculs donnent la même aire, ici celle du triangle $T$.

> [!example] Tonelli accepte aussi une aire infinie
> Sur $(0,+\infty)^2$, prenons
> $$
> f(x,y)=\frac1{(1+x+y)^2}.
> $$
> Cette fonction est positive. En intégrant d'abord en $y$, on trouve
> $$
> \int_0^{+\infty}f(x,y)\,dy
> =
> \frac1{1+x}.
> $$
> Puis
> $$
> \int_0^{+\infty}\frac1{1+x}\,dx=+\infty.
> $$
> Tonelli affirme que l'intégrale double et l'intégrale dans l'autre ordre valent elles aussi $+\infty$. On n'a pas besoin d'une intégrabilité préalable : la positivité suffit.

> [!example] Fubini : une fonction qui change de signe
> Sur le carré $[0,1]^2$, considérons
> $$
> f(x,y)=x-y.
> $$
> Cette fonction est positive sous la diagonale $y=x$, où $x>y$, et négative au-dessus. On peut appliquer Fubini car son intégrale absolue est finie. Pour la calculer, on sépare le carré selon cette diagonale. Les deux triangles obtenus sont symétriques ; il suffit donc de calculer l'aire absolue sous la diagonale, puis de multiplier par $2$ :
> $$
> \int_0^1\int_0^1|x-y|\,dy\,dx
> =
> 2\int_0^1\int_0^x(x-y)\,dy\,dx
> =
> 2\int_0^1\left[xy-\frac{y^2}{2}\right]_{y=0}^{y=x}\,dx
> =
> 2\int_0^1\left(x^2-\frac{x^2}{2}\right)\,dx
> =
> \int_0^1x^2\,dx
> =
> \left[\frac{x^3}{3}\right]_0^1
> =
> \frac13.
> $$
> L'intégrale de $|f|$ est donc finie : on peut utiliser Fubini.
>
> **Premier ordre : intégrer d'abord en $y$.** On considère $x$ comme une constante. Une primitive de $x-y$ par rapport à $y$ est $xy-y^2/2$, donc
> $$
> \int_0^1\left(\int_0^1(x-y)\,dy\right)\,dx
> =
> \int_0^1\left[xy-\frac{y^2}{2}\right]_{y=0}^{y=1}\,dx
> =
> \int_0^1\left(x-\frac12\right)\,dx
> =
> \left[\frac{x^2}{2}-\frac{x}{2}\right]_0^1
> =
> 0,
> $$
>
> **Second ordre : intégrer d'abord en $x$.** Cette fois, $y$ est une constante. Une primitive de $x-y$ par rapport à $x$ est $x^2/2-xy$, donc
> $$
> \int_0^1\left(\int_0^1(x-y)\,dx\right)\,dy
> =
> \int_0^1\left[\frac{x^2}{2}-xy\right]_{x=0}^{x=1}\,dy
> =
> \int_0^1\left(\frac12-y\right)\,dy
> =
> \left[\frac{y}{2}-\frac{y^2}{2}\right]_0^1
> =
> 0.
> $$
> La valeur nulle vient ici d'une compensation entre la moitié positive et la moitié négative. L'intégrabilité de $|f|$ garantit que cette compensation est légitime.

> [!warning] Le point à ne pas oublier
> Une fonction peut avoir des intégrales le long de chaque droite horizontale et verticale sans que Fubini s'applique. Le fait que l'on puisse calculer séparément
> $$
> \int f(x,y)\,dy
> \qquad \text{et} \qquad
> \int f(x,y)\,dx
> $$
> ne contrôle pas, à lui seul, la masse totale positive et la masse totale négative de $f$.
>
> La condition qui protège réellement l'échange des intégrales est
> $$
> \iint_{\Omega_1\times\Omega_2}|f(x,y)|\,dx\,dy<+\infty.
> $$
> Elle signifie que toute la masse de $f$, sans compensation de signes, est finie. Les parties positive et négative ont alors chacune une masse finie : il est impossible de masquer une expression du type $+\infty-\infty$.
>
> Sans cette hypothèse, les contributions positives et négatives peuvent être infinies séparément, mais se compenser de façon différente selon que l'on additionne d'abord le long des lignes horizontales ou verticales. C'est l'analogue continu d'une **série conditionnellement convergente** : changer l'ordre d'addition de termes positifs et négatifs peut modifier le résultat.
>
> Réflexe pratique : avant d'inverser deux intégrales, vérifier l'une des deux conditions suivantes :
>
> - $f\geq0$ : appliquer Tonelli ;
> - $\iint|f|<+\infty$ : appliquer Fubini.

### 1.5. Changement de variables

Un changement de variables remplace une zone compliquée par une zone plus simple. Si $y=\Phi(x)$, le facteur $|\det D\Phi(x)|$ corrige la déformation des volumes :

$$
\int_{\Omega_2} f(y)\,dy
= \int_{\Omega_1} f(\Phi(x))\,|\det D\Phi(x)|\,dx.
$$

#### Lire la notation terme à terme

On suppose que $\Phi$ transforme une région $\Omega_1$ en une région $\Omega_2$ :

$$
\Phi:\Omega_1\longrightarrow\Omega_2,
\qquad
y=\Phi(x).
$$

Dans cette formule :

- $\Omega_2$ est la région de départ, exprimée avec les coordonnées $y$ ;
- $f(y)$ est la quantité que l'on souhaite intégrer, évaluée au point $y$ ;
- $dy$ désigne un petit élément de longueur, d'aire ou de volume dans les coordonnées $y$ ;
- $\Omega_1$ est la région, souvent plus simple, que l'on utilise après changement de variables ;
- $f(\Phi(x))$ est la même fonction $f$, mais évaluée au point de $\Omega_2$ correspondant au point $x$ ;
- $D\Phi(x)$ est la matrice des dérivées de $\Phi$, appelée matrice jacobienne ;
- $|\det D\Phi(x)|$ mesure le facteur local par lequel $\Phi$ agrandit ou rétrécit les longueurs, aires ou volumes ;
- $dx$ désigne le petit élément de volume dans les nouvelles coordonnées.

Le déterminant est nécessaire parce qu'un petit carré de coordonnées $x$ n'est généralement pas envoyé sur un carré de même aire dans les coordonnées $y$. Le facteur $|\det D\Phi(x)|$ rétablit exactement l'aire ou le volume correct.

> [!tip] Recette de changement de variables
> 1. Écrire la relation $y=\Phi(x)$.
> 2. Transformer la région $\Omega_2$ en une région $\Omega_1$ décrite avec les variables $x$.
> 3. Remplacer $f(y)$ par $f(\Phi(x))$.
> 4. Multiplier par $|\det D\Phi(x)|$ avant d'intégrer avec $dx$.

> [!example] Les coordonnées polaires
> Dans le plan, on pose
> $$
> \Phi(r,\theta)=(r\cos\theta,r\sin\theta).
> $$
> La variable $r$ est la distance à l'origine et $\theta$ est l'angle. Il faut distinguer deux lectures de cette même application :
>
> - géométriquement, $\Phi$ part des coordonnées polaires $(r,\theta)$ et produit les coordonnées cartésiennes $(x,y)$ ; le rectangle des paramètres est donc envoyé sur le disque ;
> - pour calculer l'intégrale, on fait l'opération inverse dans la description : au lieu de parcourir le disque avec $(x,y)$, on le parcourt avec les paramètres $(r,\theta)$, qui vivent dans un rectangle.
>
> Le disque ne « devient » donc pas réellement un rectangle : c'est la **même région**, décrite par deux systèmes de coordonnées différents. Le disque
> $$
> B(0,R)=\{(x,y):x^2+y^2\leq R^2\}
> $$
> correspond au rectangle de paramètres
> $$
> 0\leq r\leq R,
> \qquad
> 0\leq\theta\leq2\pi.
> $$
> En effet, tout point du disque, sauf l'origine où l'angle n'est pas unique, possède une distance $r$ comprise entre $0$ et $R$ et un angle $\theta$ compris entre $0$ et $2\pi$. Ces ambiguïtés de bord ne changent pas une intégrale, car elles concernent des ensembles d'aire nulle.
> Pour trouver le facteur de changement d'aire, on commence par écrire les deux coordonnées de sortie :
> $$
> x(r,\theta)=r\cos\theta,
> \qquad
> y(r,\theta)=r\sin\theta.
> $$
> La matrice jacobienne rassemble leurs dérivées partielles. Ses lignes correspondent aux coordonnées de sortie $(x,y)$ et ses colonnes aux variables d'entrée $(r,\theta)$ :
> $$
> D\Phi(r,\theta)
> =
> \begin{pmatrix}
> \dfrac{\partial x}{\partial r} & \dfrac{\partial x}{\partial\theta}\\
> \dfrac{\partial y}{\partial r} & \dfrac{\partial y}{\partial\theta}
> \end{pmatrix}.
> $$
> On calcule les quatre dérivées une par une :
> $$
> \frac{\partial x}{\partial r}=\cos\theta,
> \qquad
> \frac{\partial x}{\partial\theta}=-r\sin\theta,
> $$
> $$
> \frac{\partial y}{\partial r}=\sin\theta,
> \qquad
> \frac{\partial y}{\partial\theta}=r\cos\theta.
> $$
> En les plaçant dans la matrice, on obtient
> $$
> D\Phi(r,\theta)
> =
> \begin{pmatrix}
> \cos\theta & -r\sin\theta\\
> \sin\theta & r\cos\theta
> \end{pmatrix}.
> $$
> Son déterminant vaut
> $$
> \det D\Phi(r,\theta)
> =
> r\cos^2\theta+r\sin^2\theta
> =
> r.
> $$
>
> Voici maintenant chaque remplacement effectué dans l'intégrale :
>
> - le point cartésien $(x,y)$ est remplacé par le point polaire $(r\cos\theta,r\sin\theta)$ ;
> - la fonction $f(x,y)$ devient donc $f(r\cos\theta,r\sin\theta)$ ;
> - le disque $B(0,R)$ devient le rectangle $[0,R]\times[0,2\pi]$ ;
> - un tout petit rectangle de côtés $dr$ et $d\theta$ dans les coordonnées polaires devient un petit parallélogramme d'aire $|\det D\Phi(r,\theta)|\,dr\,d\theta=r\,dr\,d\theta$ dans le plan.
>
> Ainsi,
> $$
> \int_{B(0,R)} f(x,y)\,dx\,dy
> =
> \int_0^{2\pi}\left(
> \int_0^R f(r\cos\theta,r\sin\theta)\,r\,dr
> \right)\,d\theta.
> $$
> La première intégrale additionne les contributions lorsque l'on s'éloigne du centre le long d'un angle fixé ; la seconde additionne ensuite tous les angles entre $0$ et $2\pi$.
>
> Le facteur $r$ a une interprétation géométrique immédiate. Une couronne de rayon $r$ et d'épaisseur très petite $dr$ a approximativement pour aire
> $$
> \text{longueur de son cercle}\times\text{épaisseur}
> =
> 2\pi r\,dr.
> $$
> Les couronnes éloignées du centre sont plus longues ; elles occupent donc plus d'aire. Le $r$ supplémentaire encode exactement ce phénomène.
>
> Pour retrouver l'aire du disque, il suffit de prendre $f=1$. En effet, intégrer la fonction constante $1$ sur une région revient à compter son aire :
> $$
> \operatorname{aire}(B(0,R))
> =
> \int_0^{2\pi}\int_0^R r\,dr\,d\theta
> =
> \int_0^{2\pi}\left[\frac{r^2}{2}\right]_{r=0}^{r=R}\,d\theta
> =
> \int_0^{2\pi}\frac{R^2}{2}\,d\theta
> =
> \frac{R^2}{2}\left[\theta\right]_{\theta=0}^{\theta=2\pi}
> =
> \pi R^2.
> $$
> Pour chaque angle fixé, l'intégrale intérieure additionne de petits secteurs depuis le centre jusqu'au rayon $R$ ; l'intégrale extérieure parcourt ensuite tous les angles. Géométriquement, cela revient à additionner toutes les couronnes du disque. Le résultat $\pi R^2$ est bien la formule habituelle de son aire.
>
> Pour un exemple d'intégrale, prenons $f(x,y)=x^2+y^2$. En coordonnées polaires,
> $$
> x^2+y^2
> =
> (r\cos\theta)^2+(r\sin\theta)^2
> =
> r^2(\cos^2\theta+\sin^2\theta)
> =
> r^2.
> $$
> Dans la formule de changement de variables, le facteur $r^2$ venant de la fonction doit encore être multiplié par le facteur jacobien $r$. L'intégrande devient donc $r^3$ :
> $$
> \int_{B(0,R)}(x^2+y^2)\,dx\,dy
> =
> \int_0^{2\pi}\int_0^R r^3\,dr\,d\theta
> =
> \int_0^{2\pi}\left[\frac{r^4}{4}\right]_{r=0}^{r=R}\,d\theta
> =
> \int_0^{2\pi}\frac{R^4}{4}\,d\theta
> =
> \frac{R^4}{4}\left[\theta\right]_{\theta=0}^{\theta=2\pi}
> =
> \frac{\pi R^4}{2}.
> $$
> Cette intégrale mesure la somme des carrés des distances à l'origine sur tout le disque. Les points éloignés du centre contribuent davantage, car leur contribution est proportionnelle à $r^2$.

---

## 2. Les espaces $L^p$ : différentes façons de mesurer une fonction

Pour $1\le p<\infty$, on définit

$$
\|f\|_{L^p(\Omega)}=\left(\int_\Omega |f|^p\right)^{1/p}.
$$

Et $\|f\|_{L^\infty}$ est la plus petite borne essentielle de $|f|$ : c'est essentiellement la plus grande hauteur atteinte par $f$, en ignorant les exceptions de mesure nulle.

- $L^1$ privilégie la **masse totale** : on pense à l'aire sous $|f|$.
- $L^2$ privilégie l'**énergie** ou l'amplitude quadratique : on pense à une moyenne quadratique.
- $L^\infty$ privilégie le **pire écart** : on pense à la hauteur maximale, sauf sur un ensemble négligeable.

> [!example] Un pic fin
> Un pic très haut mais très étroit peut avoir une petite norme $L^1$, une norme $L^2$ modérée ou grande, et une énorme norme $L^\infty$. Chaque norme répond donc à une question différente.

### 2.1. Exposants conjugués et inégalité de Hölder

À $p$ est associé son exposant conjugué $p'$, défini par

$$
\frac1p+\frac1{p'}=1.
$$

Par exemple, le conjugué de $2$ est $2$, celui de $1$ est $\infty$. L'inégalité de Hölder dit que multiplier une fonction de $L^p$ et une de $L^{p'}$ produit une fonction intégrable :

$$
\|fg\|_{L^1}
=
\int_\Omega |f(x)g(x)|\,dx
\leq
\|f\|_{L^p(\Omega)}\,\|g\|_{L^{p'}(\Omega)}.
$$

La formule se lit en quatre étapes.

1. On multiplie les deux fonctions **point par point** : au point $x$, on obtient le nombre $f(x)g(x)$.
2. On prend la valeur absolue et on additionne sur tout le domaine :
   $$
   \|fg\|_{L^1}
   =
   \int_\Omega|f(x)g(x)|\,dx.
   $$
   Cette quantité est la masse totale du produit.
3. On évalue séparément la taille de $f$ avec la norme $L^p$ et celle de $g$ avec la norme $L^{p'}$.
4. Hölder affirme que la masse totale du produit ne dépasse jamais le produit de ces deux tailles.

Ainsi, Hölder ne dit pas que $f(x)g(x)$ est petit en chaque point. Il dit que son **aire totale** reste finie. Dès que $\|f\|_{L^p}$ et $\|g\|_{L^{p'}}$ sont finies, le membre de droite est un nombre fini ; l'intégrale de $|fg|$ est alors forcément finie aussi. C'est exactement ce que signifie « $fg\in L^1$ ».

Les exposants $p$ et $p'$ sont appelés conjugués parce qu'ils vérifient

$$
\frac1p+\frac1{p'}=1.
$$

Pour $1<p<\infty$, on peut écrire $p'=p/(p-1)$. Par exemple, si $p=3$, alors $p'=3/2$, car

$$
\frac13+\frac1{3/2}
=
\frac13+\frac23
=
1.
$$

> [!example] Un calcul concret avec $p=p'=2$
> Sur $(0,1)$, prenons
> $$
> f(x)=g(x)=x^{-1/4}.
> $$
> Les deux fonctions sont dans $L^2(0,1)$, car
> $$
> \int_0^1|f(x)|^2\,dx
> =
> \int_0^1x^{-1/2}\,dx
> =
> 2.
> $$
> Attention : ce nombre $2$ est l'intégrale du carré, pas encore la norme. Par définition,
> $$
> \|f\|_{L^2(0,1)}
> =
> \left(\int_0^1|f(x)|^2\,dx\right)^{1/2}
> =
> \sqrt2.
> $$
> Comme $g=f$, on a également $\|g\|_{L^2(0,1)}=\sqrt2$.
>
> Leur produit vaut $f(x)g(x)=x^{-1/2}$, qui est bien dans $L^1(0,1)$ :
> $$
> \int_0^1|f(x)g(x)|\,dx
> =
> \int_0^1x^{-1/2}\,dx
> =
> 2.
> $$
> Hölder prédit exactement cette borne :
> $$
> \|fg\|_{L^1}
> \leq
> \|f\|_{L^2}\,\|g\|_{L^2}
> =
> \sqrt2\times\sqrt2
> =
> 2.
> $$

> [!example] Le cas $p=1$ et $p'=\infty$
> Si $f\in L^1$ et $g\in L^\infty$ est bornée, Hölder devient
> $$
> \int_\Omega|f(x)g(x)|\,dx
> \leq
> \|f\|_{L^1(\Omega)}\,\|g\|_{L^\infty(\Omega)}.
> $$
> Cette version est la plus facile à voir directement. Posons
> $$
> M=\|g\|_{L^\infty(\Omega)}.
> $$
> Cela signifie que, sauf éventuellement sur un ensemble négligeable, $|g(x)|\leq M$. En multipliant cette inégalité par $|f(x)|$, qui est positive, on obtient pour presque tout $x$
> $$
> |f(x)g(x)|
> =
> |f(x)|\,|g(x)|
> \leq
> M|f(x)|.
> $$
> On peut alors intégrer les deux membres :
> $$
> \int_\Omega|f(x)g(x)|\,dx
> \leq
> M\int_\Omega|f(x)|\,dx
> =
> \|g\|_{L^\infty(\Omega)}\,\|f\|_{L^1(\Omega)}.
> $$
>
> L'idée est donc simple : $f$ apporte une masse totale finie, tandis que $g$ ne peut jamais amplifier localement cette masse par un facteur supérieur à $M$.
>
> Sur $(0,1)$, prenons
> $$
> f(x)=\frac1{\sqrt{x}},
> \qquad
> g(x)=x.
> $$
> On a
> $$
> \|f\|_{L^1(0,1)}
> =
> \int_0^1\frac1{\sqrt{x}}\,dx
> =
> 2,
> \qquad
> \|g\|_{L^\infty(0,1)}=1.
> $$
> Leur produit est $f(x)g(x)=\sqrt{x}$, et
> $$
> \int_0^1|f(x)g(x)|\,dx
> =
> \int_0^1\sqrt{x}\,dx
> =
> \frac23
> \leq
> 2\times1.
> $$
> Même si $f$ devient très grande près de $0$, le facteur $g(x)=x$ est toujours borné par $1$. Leur produit reste donc intégrable.

#### Pourquoi Cauchy-Schwarz est le cas $p=2$

Voir aussi [[poly-analyse-fonctionnelle#3.1. Les identités géométriques|Espaces de Hilbert — Cauchy-Schwarz]], où cette inégalité est formulée à l'aide du produit scalaire. Il n’existe pas de fiche séparée sur Cauchy-Schwarz dans les notes actuelles.

Lorsque $p=2$, l'équation qui définit l'exposant conjugué donne

$$
\frac12+\frac1{p'}=1,
\qquad \text{donc} \qquad
p'=2.
$$

Hölder se spécialise alors en

$$
\int_\Omega|f(x)g(x)|\,dx
\leq
\|f\|_{L^2(\Omega)}\,\|g\|_{L^2(\Omega)}.
$$

Dans $L^2$, le produit scalaire est

$$
\langle f,g\rangle_{L^2}
=
\int_\Omega f(x)g(x)\,dx.
$$

Comme la valeur absolue d'une intégrale est au plus l'intégrale de la valeur absolue, on obtient

$$
|\langle f,g\rangle_{L^2}|
=
\left|\int_\Omega f(x)g(x)\,dx\right|
\leq
\int_\Omega|f(x)g(x)|\,dx
\leq
\|f\|_{L^2(\Omega)}\,\|g\|_{L^2(\Omega)}.
$$

C'est exactement l'inégalité de Cauchy-Schwarz. Hölder est donc sa version adaptée à tous les espaces $L^p$, tandis que Cauchy-Schwarz est la version géométrique particulière à $L^2$.

> [!example] Le cas $L^2$
> Pour deux signaux $f$ et $g$, le nombre
> $$
> \langle f,g\rangle_{L^2}
> =
> \int_\Omega f(x)g(x)\,dx
> $$
> mesure leur **corrélation** ou leur alignement :
>
> - si $f$ et $g$ sont souvent de même signe et grands aux mêmes endroits, le nombre est positif ;
> - s'ils sont souvent de signes opposés, il est négatif ;
> - s'ils se compensent exactement, il vaut $0$ : les signaux sont alors orthogonaux au sens $L^2$.
>
> L'énergie de $f$ est
> $$
> E(f)=\int_\Omega|f(x)|^2\,dx=\|f\|_{L^2(\Omega)}^2.
> $$
> Attention : l'énergie est le **carré** de la norme $L^2$. La norme elle-même est donc $\|f\|_{L^2}=\sqrt{E(f)}$.
>
> Si les énergies de $f$ et de $g$ sont finies, Cauchy-Schwarz — donc Hölder avec $p=2$ — garantit que leur corrélation est finie et vérifie
> $$
> \left|\int_\Omega f(x)g(x)\,dx\right|
> \leq
> \sqrt{\int_\Omega|f(x)|^2\,dx}\,
> \sqrt{\int_\Omega|g(x)|^2\,dx}.
> $$
> Cette inégalité dit que deux signaux ne peuvent pas être plus corrélés que ne le permettent leurs énergies.
>
> Si $g=f$, alors
> $$
> \int_\Omega f(x)g(x)\,dx
> =
> \int_\Omega|f(x)|^2\,dx
> =
> \|f\|_{L^2}^2.
> $$
> C'est le cas d'égalité : un signal est parfaitement aligné avec lui-même.
>
> Pour un exemple d'orthogonalité, sur $[0,2\pi]$, prenons $f(x)=\sin x$ et $g(x)=\cos x$. Alors
> l'identité trigonométrique d'angle double donne
> $$
> \sin(2x)
> =
> \sin(x+x)
> =
> \sin x\cos x+\cos x\sin x
> =
> 2\sin x\cos x.
> $$
> En divisant par $2$, on obtient
> $$
> \sin x\cos x=\frac12\sin(2x).
> $$
> On remplace donc le produit dans l'intégrale ; le facteur constant $1/2$ peut sortir de l'intégrale :
> $$
> \int_0^{2\pi}\sin x\cos x\,dx
> =
> \frac12\int_0^{2\pi}\sin(2x)\,dx
> =
> 0.
> $$
> Les deux signaux possèdent une énergie non nulle, mais leur corrélation totale est nulle : les portions positives et négatives se compensent exactement.

### 2.2. Minkowski : l'inégalité triangulaire des fonctions

$$
\|f+g\|_{L^p}\le \|f\|_{L^p}+\|g\|_{L^p}.
$$

Cette formule donne vraiment à $\|\cdot\|_{L^p}$ le statut de norme. Elle dit que l'effet total de deux signaux est au plus la somme de leurs tailles.

### 2.3. Interpolation : être contrôlé à deux échelles

Si une fonction appartient à la fois à $L^s$ et à $L^t$, elle appartient aussi à tous les espaces intermédiaires $L^r$, avec $s\leq r\leq t$. L'idée est qu'elle est contrôlée à deux échelles : une norme regarde la masse répartie sur tout le domaine, l'autre pénalise davantage les grands pics. Entre les deux, il n'y a pas de nouvelle difficulté.

La formule à retenir est la suivante. Si $\theta\in[0,1]$ est choisi de sorte que

$$
\frac1r
=
\frac{\theta}{s}+\frac{1-\theta}{t},
$$

alors

$$
\|f\|_{L^r(\Omega)}
\leq
\|f\|_{L^s(\Omega)}^{\theta}
\|f\|_{L^t(\Omega)}^{1-\theta}.
$$

Autrement dit, la norme intermédiaire est bornée par un mélange des deux normes que l'on connaît déjà. C'est particulièrement utile pour estimer une norme $L^r$ sans recalculer son intégrale.

> [!example] Entre $L^1$ et $L^\infty$
> Prenons un pic rectangulaire : $f(x)=10$ sur $(0,0{,}01)$ et $f(x)=0$ ailleurs. Sa hauteur maximale est $10$, donc
> $$
> \|f\|_{L^\infty}=10.
> $$
> Sa masse totale vaut seulement
> $$
> \|f\|_{L^1}=10\times0{,}01=0{,}1.
> $$
> Pour obtenir $L^2$ à partir de $L^1$ et $L^\infty$, on prend $s=1$, $r=2$ et $t=\infty$. La relation entre les exposants devient
> $$
> \frac12
> =
> \frac{\theta}{1}+\frac{1-\theta}{\infty}
> =\theta,
> $$
> puisque $1/\infty=0$. Il faut donc choisir $\theta=1/2$. La formule d'interpolation devient alors
> $$
> \|f\|_{L^2}
> \leq
> \|f\|_{L^1}^{1/2}\|f\|_{L^\infty}^{1/2}
> $$
> On remplace enfin les deux normes connues par leurs valeurs :
> $$
> \|f\|_{L^2}
> \leq
> (0{,}1)^{1/2}\,10^{1/2}
> =\sqrt{0{,}1}\sqrt{10}
> =\sqrt{0{,}1\times10}
> =1.
> $$
> Ici, la borne est même exacte : $\|f\|_{L^2}=1$. Le pic est très haut, mais si étroit que sa masse reste faible ; la norme $L^2$ traduit précisément ce compromis.

> [!warning] Les inclusions dépendent du domaine
> **But de cet encadré :** savoir si une information $f\in L^q(\Omega)$ permet automatiquement de conclure que $f\in L^p(\Omega)$, sans refaire de calcul. La réponse dépend de la taille du domaine.
>
> Pour comprendre ce point, commençons par le cas le plus simple : « bornée » signifie appartenir à $L^\infty$, et « masse totale finie » signifie appartenir à $L^1$.
>
> Sur $(0,1)$, une fonction bornée est automatiquement intégrable. Si $|f(x)|\leq M$, alors le graphe de $|f|$ reste sous un rectangle de hauteur $M$ et de largeur $1$. Son aire ne peut donc pas dépasser $M$ :
> $$
> \int_0^1|f(x)|\,dx\leq\int_0^1M\,dx=M.
> $$
> C'est cela que veut dire « le domaine est de mesure finie » : il n'y a qu'une largeur finie sur laquelle une fonction peut accumuler de la masse. Le résultat général est le même phénomène : si $q>p$, alors sur un tel domaine $L^q(\Omega)\subset L^p(\Omega)$.
> $$
> \|f\|_{L^p(\Omega)}
> \leq
> \mu(\Omega)^{1/p-1/q}\|f\|_{L^q(\Omega)}.
> $$
> Voici comment lire cette formule, de droite à gauche :
>
> - $\|f\|_{L^q(\Omega)}$ est l'information dont on dispose déjà, à l'exposant le plus grand $q$ ;
> - $\mu(\Omega)$ est le volume du domaine : sa longueur en dimension $1$, son aire en dimension $2$, son volume en dimension $3$ ;
> - le facteur $\mu(\Omega)^{1/p-1/q}$ corrige cette information selon la taille du domaine ;
> - le résultat contrôle $\|f\|_{L^p(\Omega)}$, la norme plus faible que l'on cherche à estimer.
>
> Par exemple, prenons $\Omega=(0,4)$, $p=1$, $q=2$ et $f(x)=1$. La formule devient
> $$
> \|f\|_{L^1(0,4)}
> \leq
> \mu((0,4))^{1-1/2}\|f\|_{L^2(0,4)}
> =4^{1/2}\|f\|_{L^2(0,4)}.
> $$
> Or la masse de $f$ vaut $\|f\|_{L^1(0,4)}=4$, tandis que
> $$
> \|f\|_{L^2(0,4)}
> =\left(\int_0^4 1^2\,dx\right)^{1/2}
> =\sqrt4=2.
> $$
> Le membre de droite vaut donc $4^{1/2}\times2=2\times2=4$. On obtient $4\leq4$ : la borne est exacte pour cette fonction constante. Le facteur $\sqrt4=2$ est précisément ce qui tient compte du fait que le domaine a une longueur $4$, et non une longueur $1$.
>
> Sur $(0,+\infty)$, le rectangle peut avoir une largeur infinie. La fonction constante $f(x)=1$ est bien bornée, donc dans $L^\infty$, mais elle n'est pas dans $L^1$, car
> $$
> \int_0^{+\infty}1\,dx=+\infty.
> $$
> Ainsi, sur $\mathbb R^d$, être contrôlé pour les grandes valeurs ne suffit plus à contrôler la masse : la fonction peut être petite mais s'étaler sans fin.
>
> L'autre difficulté concerne les pics. Sur $(0,1)$, prenons
> $$
> f(x)=x^{-2/3}
> $$
> qui devient très grande lorsque $x$ se rapproche de $0$. Malgré ce pic,
> $$
> \int_0^1|f(x)|\,dx
> =\int_0^1x^{-2/3}\,dx=3,
> $$
> donc $f\in L^1(0,1)$. Mais, pour regarder $L^2$, il faut élever la fonction au carré :
> $$
> \int_0^1|f(x)|^2\,dx
> =\int_0^1x^{-4/3}\,dx=+\infty.
> $$
> Le carré rend le pic trop coûteux. Donc $f\notin L^2(0,1)$, même si le domaine est borné.
>
> Enfin, sur $(1,+\infty)$, reprenons la même expression
> $$
> g(x)=x^{-2/3}
> $$
> mais regardons cette fois ce qui se passe loin de $0$. Son carré décroît assez vite :
> $$
> \int_1^{+\infty}|g(x)|^2\,dx
> =\int_1^{+\infty}x^{-4/3}\,dx=3.
> $$
> Donc $g\in L^2(1,+\infty)$. En revanche,
> $$
> \int_1^{+\infty}|g(x)|\,dx
> =\int_1^{+\infty}x^{-2/3}\,dx=+\infty,
> $$
> donc $g\notin L^1(1,+\infty)$. Ici, le problème n'est pas un pic : c'est une queue qui décroît, mais trop lentement, sur une longueur infinie.
>
> À retenir : sur un domaine fini, le volume limite la masse. Sur un domaine infini, il faut vérifier séparément les pics près des singularités et les queues à l'infini.

### 2.4. Complétude, dualité et localisation

- Les espaces $L^p$ sont complets : une suite de Cauchy pour $\|\cdot\|_{L^p}$ possède une limite dans $L^p$.

#### Dualité : tester une fonction par une autre fonction

Avant de parler de fonctions, pensons à un vecteur $v=(v_1,\ldots,v_n)\in\mathbb R^n$. Pour en extraire un nombre de manière linéaire, on choisit des poids $u=(u_1,\ldots,u_n)$ et l'on calcule

$$
u\cdot v=u_1v_1+\cdots+u_nv_n.
$$

Les poids $u_i$ disent quelles composantes de $v$ nous intéressent. La **dualité** consiste à étudier toutes les manières continues de poser une question numérique linéaire à un objet. Pour les vecteurs, ces questions sont les produits scalaires avec un vecteur de poids $u$.

Dans un espace de fonctions, on remplace la somme par une intégrale. Une **forme linéaire** sur $L^p$ est donc une règle qui reçoit une fonction $f$ et renvoie un nombre, tout en respectant les additions et les multiplications par un scalaire. Elle est **continue** si une petite perturbation de $f$ en norme $L^p$ ne peut produire qu'une petite perturbation du nombre obtenu.

L'exemple fondamental est de choisir une fonction $u$ et de poser

$$
\ell_u(f)=\int_\Omega u(x)f(x)\,dx.
$$

La fonction $u$ joue le rôle d'un **test** : elle donne beaucoup de poids à certaines zones de $\Omega$ et peu de poids à d'autres. Si $u\in L^{p'}(\Omega)$, où $p'$ est l'exposant conjugué de $p$, Hölder garantit que cette intégrale est bien définie et que

$$
|\ell_u(f)|
\leq
\|u\|_{L^{p'}(\Omega)}\|f\|_{L^p(\Omega)}.
$$

Voici l'application de Hölder, étape par étape. Hölder dit que si $g\in L^a(\Omega)$ et $h\in L^b(\Omega)$, avec

$$
\frac1a+\frac1b=1,
$$

alors

$$
\int_\Omega|g(x)h(x)|\,dx
\leq
\|g\|_{L^a(\Omega)}\|h\|_{L^b(\Omega)}.
$$

Ici, on fait simplement les choix

$$
g=u,
\qquad
h=f,
\qquad
a=p',
\qquad
b=p.
$$

Ils conviennent car $p'$ est précisément l'exposant conjugué de $p$, donc $1/p'+1/p=1$. Hölder donne alors

$$
\int_\Omega|u(x)f(x)|\,dx
\leq
\|u\|_{L^{p'}(\Omega)}\|f\|_{L^p(\Omega)}.
$$

Enfin, l'intégrale $\int uf$ peut contenir des signes positifs et négatifs, mais sa valeur absolue ne dépasse jamais l'intégrale des valeurs absolues :

$$
|\ell_u(f)|
=
\left|\int_\Omega u(x)f(x)\,dx\right|
\leq
\int_\Omega|u(x)f(x)|\,dx.
$$

En combinant ces deux inégalités, on obtient la borne annoncée. Comme le membre de droite est fini, $uf\in L^1(\Omega)$ : l'intégrale qui définit $\ell_u(f)$ a donc bien un sens.

Autrement dit, $u\in L^{p'}$ fabrique automatiquement une forme linéaire continue sur $L^p$.

> [!example] Mesurer la masse dans une fenêtre
> Supposons $f\in L^1(\mathbb R)$ et choisissons comme test $u=\mathbf 1_{[0,1]}$. Alors $u$ vaut $1$ sur $[0,1]$ et $0$ ailleurs, donc
> $$
> \ell_u(f)
> =\int_{\mathbb R}\mathbf 1_{[0,1]}(x)f(x)\,dx
> =\int_0^1f(x)\,dx.
> $$
> Cette forme linéaire répond à la question : « quel est le bilan de $f$ dans la fenêtre $[0,1]$ ? » Le test $u$ est borné, donc $u\in L^\infty$, ce qui est exactement le bon espace dual de $L^1$.

Jusqu'ici, Hölder a montré un sens de l'histoire : en partant d'un poids $u\in L^{p'}$, on fabrique une forme linéaire continue

$$
f\longmapsto\int_\Omega u(x)f(x)\,dx.
$$

Le théorème de représentation de Riesz pour les espaces $L^p$ donne la réciproque : dans le cadre usuel des espaces de Lebesgue et pour $1\leq p<\infty$, si l'on nous donne **n'importe quelle** forme linéaire continue $\ell$ sur $L^p$, alors il existe une fonction $u\in L^{p'}$ telle que, pour toute fonction $f\in L^p$,

$$
\ell(f)=\int_\Omega u(x)f(x)\,dx.
$$

Autrement dit, toute question numérique qui est à la fois linéaire et stable vis-à-vis de la norme $L^p$ est nécessairement une question de la forme « intégrer $f$ contre un poids $u$ ». Ce poids est unique à modification sur un ensemble de mesure nulle près.

On résume ce résultat par

$$
(L^p)'=L^{p'}.
$$

Le symbole $(L^p)'$ désigne le **dual** de $L^p$, c'est-à-dire l'ensemble des formes linéaires continues sur $L^p$. Cette égalité ne signifie donc pas que $L^p$ et $L^{p'}$ contiennent les mêmes fonctions. Elle signifie que chaque élément du dual, qui est au départ une règle $\ell$ agissant sur les fonctions $f$, correspond exactement à une fonction-poids $u\in L^{p'}$.

> [!example] Ce que la réciproque apporte
> Si une forme linéaire continue $\ell$ mesure une certaine information sur toute fonction $f\in L^1(\mathbb R)$, le théorème affirme qu'il existe une fonction bornée $u\in L^\infty(\mathbb R)$ telle que
> $$
> \ell(f)=\int_{\mathbb R}u(x)f(x)\,dx.
> $$
> Même si la règle $\ell$ nous a été donnée sous une forme abstraite, elle est donc toujours, au fond, une moyenne pondérée de $f$.

> [!important] La continuité est essentielle
> Une règle comme « prendre la valeur $f(0)$ » est linéaire, mais ce n'est pas une forme linéaire continue sur $L^p$. Dans $L^p$, deux fonctions qui diffèrent seulement en $0$ représentent le même élément, et l'on peut aussi concentrer une très grande hauteur près de $0$ tout en gardant une norme $L^p$ petite. Cette règle ne peut donc pas s'écrire sous la forme $\int uf$ avec $u\in L^{p'}$. Le théorème ne classe que les questions qui restent stables pour la norme considérée.

> [!example] Deux cas à garder en tête
> **Le cas $p=2$.** L'exposant conjugué de $2$ est encore $2$, car
> $$
> \frac12+\frac12=1.
> $$
> Une fonction $u\in L^2$ peut donc tester une autre fonction $f\in L^2$ par
> $$
> \ell_u(f)=\int_\Omega u(x)f(x)\,dx=\langle u,f\rangle_{L^2}.
> $$
> C'est exactement le produit scalaire de $L^2$. Le test mesure dans quelle mesure $f$ ressemble à la direction $u$ : s'ils sont grands aux mêmes endroits et de même signe, l'intégrale est grande et positive ; s'ils se compensent, elle est proche de $0$. La continuité est donnée par Cauchy-Schwarz :
> $$
> |\langle u,f\rangle_{L^2}|
> \leq
> \|u\|_{L^2}\|f\|_{L^2}.
> $$
> C'est pourquoi $L^2$ est particulièrement géométrique : son dual est le même espace, $(L^2)'=L^2$.
>
> **Le cas $p=1$.** Cette fois, l'exposant conjugué est $p'=\infty$. Pour tester une fonction intégrable $f$, il faut donc choisir un poids borné $u$. Si $|u(x)|\leq M$, alors
> $$
> \left|\int_\Omega u(x)f(x)\,dx\right|
> \leq
> \int_\Omega|u(x)||f(x)|\,dx
> \leq
> M\int_\Omega|f(x)|\,dx
> =M\|f\|_{L^1}.
> $$
> Le poids $u$ peut sélectionner une zone, changer les signes ou donner plus d'importance à une partie du domaine, mais sa hauteur doit rester contrôlée. C'est le sens de $(L^1)'=L^\infty$.
>
> Pourquoi ne pas prendre un poids non borné ? Sur $(0,1)$, posons
> $$
> u(x)=x^{-1/2},
> \qquad
> f(x)=x^{-1/2}.
> $$
> La fonction $f$ est bien dans $L^1(0,1)$, car $\int_0^1x^{-1/2}\,dx=2$. Mais le produit vaut $u(x)f(x)=1/x$, et
> $$
> \int_0^1\frac1x\,dx=+\infty.
> $$
> Ce poids non borné ne permet donc même pas de définir $\int uf$ pour toutes les fonctions de $L^1$.

> [!warning] Le cas $L^\infty$
> L'identification simple $(L^p)'=L^{p'}$ ne se prolonge pas telle quelle à $p=\infty$ : le dual de $L^\infty$ peut contenir davantage d'objets que les seules fonctions de $L^1$. Dans ce poly, on utilisera surtout les cas $1\leq p<\infty$.

#### Espaces locaux : contrôler la fonction dans chaque région bornée

La notation $L^p_{\mathrm{loc}}(\Omega)$ signifie que $f$ appartient à $L^p$ sur toute partie compacte de $\Omega$. Concrètement, pour chaque région fermée et bornée $K$ contenue dans $\Omega$, on demande

$$
\int_K|f(x)|^p\,dx<\infty.
$$

On ne demande donc pas que la masse soit finie sur tout $\Omega$ à la fois : on demande seulement qu'elle soit finie dans chaque fenêtre bornée. Cette notion sépare les problèmes **locaux** — un pic ou une singularité près d'un point — du comportement **global** à l'infini.

> [!example] Localement intégrable, mais pas intégrable globalement
> La fonction constante $1$ est dans $L^1_{\mathrm{loc}}(\mathbb R)$ : sur chaque intervalle borné $[-R,R]$, sa masse vaut $2R$. Elle n'est pas dans $L^1(\mathbb R)$, car quand on laisse $R$ tendre vers l'infini, cette masse devient infinie.

> [!example] Une singularité locale reste interdite si elle est trop forte
> La fonction $x\mapsto1/x$ appartient à $L^1_{\mathrm{loc}}((0,1))$ : les compacts de $(0,1)$ restent à distance positive de $0$, où la fonction est régulière. La singularité est ici située au bord, donc hors du domaine. En revanche, si le domaine contient $0$, par exemple $(-1,1)$, la fonction $x\mapsto1/|x|$ n'est pas dans $L^1_{\mathrm{loc}}((-1,1))$, car son intégrale est infinie dans toute fenêtre autour de $0$.


### 2.5. Convolution : moyenner et lisser

La convolution de deux fonctions sur $\mathbb R^d$ est

$$
(f*g)(x)=\int_{\mathbb R^d} f(x-y)g(y)\,dy.
$$

On peut la lire comme une moyenne pondérée de $f$ autour de $x$, dont le noyau est $g$. Elle apparaît en traitement du signal, en probabilités et dans les équations aux dérivées partielles.

> [!example] Flou d'une image
> Si $g$ est une petite bosse positive de masse $1$, $f*g$ est une version lissée de $f$. Le support rappelle notamment que $L^1*L^1\subset L^1$ et $L^1*L^2\subset L^2$ : moyenner par un noyau intégrable ne détruit pas le contrôle en $L^2$.

---

## 3. Espaces de Hilbert : la géométrie en dimension infinie

Un espace de Hilbert est un espace vectoriel complet muni d'un produit scalaire $\langle f,g\rangle$. Sa norme est induite par ce produit scalaire :

$$
\|f\|=\sqrt{\langle f,f\rangle}.
$$

Les exemples essentiels sont $\mathbb R^n$ et $L^2(\Omega)$, où

$$
\langle f,g\rangle_{L^2}=\int_\Omega f(x)g(x)\,dx.
$$

Dans un Hilbert, les mots géométriques usuels — angle, orthogonalité, projection — continuent d'avoir un sens.

### 3.1. Les identités géométriques

**Cauchy-Schwarz** : $|\langle f,g\rangle|\le\|f\|\,\|g\|$. Deux vecteurs ne peuvent pas être plus corrélés que ne le permettent leurs tailles.

**Pythagore** : si $f\perp g$, alors $\|f+g\|^2=\|f\|^2+\|g\|^2$. Les énergies de composantes orthogonales s'additionnent.

#### L'identité du parallélogramme : reconnaître une géométrie euclidienne

Dans un espace euclidien, les deux diagonales d'un parallélogramme sont les vecteurs $u+v$ et $u-v$. L'identité du parallélogramme affirme que

$$
\|u+v\|^2+\|u-v\|^2
=
2\|u\|^2+2\|v\|^2.
$$

Elle dit que la somme des carrés des longueurs des deux diagonales est égale à la somme des carrés des longueurs des quatre côtés. Dans un espace muni d'un produit scalaire, cette formule découle simplement du développement des carrés :

$$
\|u+v\|^2
=
\langle u+v,u+v\rangle
=
\|u\|^2+2\langle u,v\rangle+\|v\|^2,
$$

et

$$
\|u-v\|^2
=
\|u\|^2-2\langle u,v\rangle+\|v\|^2.
$$

En additionnant, les termes qui mesurent l'angle entre $u$ et $v$ s'annulent. C'est pourquoi l'identité reste vraie quel que soit cet angle.

Le fait remarquable est la réciproque : sur un espace vectoriel réel, si une norme vérifie cette identité pour tous les vecteurs $u$ et $v$, alors elle provient nécessairement d'un produit scalaire. On peut même retrouver ce produit scalaire à partir de la norme grâce à la formule de polarisation

$$
\langle u,v\rangle
=
\frac14\left(\|u+v\|^2-\|u-v\|^2\right).
$$

Ainsi, l'identité est un **test de géométrie euclidienne** : si elle est vraie, la norme possède une notion cohérente d'angle et d'orthogonalité ; si elle échoue, on peut toujours mesurer des longueurs, mais pas les interpréter comme dans le plan euclidien.

> [!example] Pourquoi la norme $L^1$ n'est pas euclidienne
> Dans $\mathbb R^2$ muni de la norme $\|\cdot\|_1$, prenons $u=(1,0)$ et $v=(0,1)$. On a
> $$
> \|u\|_1=\|v\|_1=1,
> $$
> tandis que
> $$
> \|u+v\|_1=\|(1,1)\|_1=2,
> \qquad
> \|u-v\|_1=\|(1,-1)\|_1=2.
> $$
> Le membre de gauche de l'identité vaut donc $2^2+2^2=8$, alors que le membre de droite vaut $2\times1^2+2\times1^2=4$. L'identité échoue : la norme $\|\cdot\|_1$ ne peut pas être obtenue à partir d'un produit scalaire.
>
> À l'inverse, la norme $L^2$ vérifie l'identité. C'est la raison profonde pour laquelle $L^2$ admet projections orthogonales, angles et théorème de Pythagore, contrairement à $L^1$ ou $L^\infty$.

> [!example] Pourquoi $L^2$ est spécial
> Dans $L^2$, deux signaux orthogonaux ne se gênent pas : l'énergie du signal total est la somme des énergies. Cela explique l'importance de $L^2$ pour les séries de Fourier et la physique.

### 3.2. Projection sur un convexe fermé

Dans un Hilbert, tout point $f$ possède un unique point le plus proche dans un ensemble convexe fermé non vide $C$. Pour un sous-espace fermé $F$, ce point est la projection orthogonale de $f$ sur $F$.

On obtient la décomposition unique

$$
f=g+h,\qquad g\in F,\quad h\in F^\perp.
$$

La partie $g$ est ce que le sous-espace sait représenter ; le résidu $h$ est l'erreur irréductible, orthogonale à tout ce qu'on a gardé.

> [!example] Moindres carrés
> Ajuster une droite à des données revient à projeter le vecteur des observations sur l'espace engendré par les colonnes du modèle. Le résidu est orthogonal aux directions disponibles : c'est l'origine géométrique des équations normales.

### 3.3. Le théorème de représentation de Riesz

Une **forme linéaire** sur $H$ est une application qui prend un vecteur $u\in H$ et renvoie un nombre, noté $\varphi(u)$. Elle est linéaire si, pour tous vecteurs $u,v\in H$ et tous scalaires $a,b$,

$$
\varphi(au+bv)=a\varphi(u)+b\varphi(v).
$$

Elle est **continue** si une petite perturbation de $u$ produit une petite perturbation de $\varphi(u)$. Pour une application linéaire, cela revient exactement à l'existence d'une constante $C\geq0$ telle que

$$
|\varphi(u)|\leq C\|u\|_H
\qquad\text{pour tout }u\in H.
$$

Cette inégalité se lit ainsi : si le vecteur d'entrée a une taille au plus $\varepsilon$, alors la sortie vérifie automatiquement

$$
|\varphi(u)|\leq C\varepsilon.
$$

Par exemple, si $C=10$ et si $\|u\|_H\leq0{,}001$, alors

$$
|\varphi(u)|\leq10\times0{,}001=0{,}01.
$$

Pour rendre la sortie plus petite qu'un seuil donné $\eta>0$, il suffit donc de demander que l'entrée vérifie

$$
\|u\|_H\leq\frac{\eta}{C}.
$$

C'est exactement le sens de la continuité en $0$ : en prenant $u$ suffisamment proche de $0$, on force $\varphi(u)$ à être aussi proche de $0$ que souhaité.

À l'inverse, si aucune constante $C$ n'existait, les rapports

$$
\frac{|\varphi(u)|}{\|u\|_H}
$$

pourraient devenir arbitrairement grands. On pourrait alors construire des vecteurs $v_n$ tels que

$$
\|v_n\|_H\leq\frac1n
\qquad\text{mais}\qquad
|\varphi(v_n)|=1.
$$

Les vecteurs $v_n$ tendraient vers le vecteur nul, mais leurs images ne tendraient pas vers $0$. C'est précisément ce qui signifie qu'une forme linéaire n'est pas continue : des entrées de plus en plus petites produisent encore une sortie de taille fixe.

Pour mesurer la taille intrinsèque de $\varphi$, on normalise les entrées en ne regardant que les vecteurs de norme au plus $1$. La norme de $\varphi$ est la plus grande valeur absolue que la forme peut produire sur cette boule unité :

$$
\|\varphi\|_{H'}
=
\sup_{\|u\|_H\leq1}|\varphi(u)|.
$$

Le symbole $\sup$ signifie « plus grande valeur possible », ou plus exactement la plus petite borne supérieure si cette valeur n'est pas atteinte. Cette quantité est aussi la plus petite constante $C$ qui fonctionne dans l'inégalité précédente.

> [!example] Une forme linéaire dans le plan
> Sur $\mathbb R^2$, posons
> $$
> \varphi(x,y)=3x-2y.
> $$
> Pour tester la taille de $\varphi$, on ne laisse pas $(x,y)$ devenir arbitrairement grand : sinon, par linéarité, la sortie deviendrait elle aussi arbitrairement grande. On impose donc
> $$
> \|(x,y)\|_2=\sqrt{x^2+y^2}\leq1.
> $$
> Cauchy-Schwarz donne alors
> $$
> |3x-2y|
> \leq
> \sqrt{3^2+(-2)^2}\sqrt{x^2+y^2}
> \leq
> \sqrt{13}.
> $$
> La plus grande sortie possible pour une entrée de norme au plus $1$ est donc au plus $\sqrt{13}$. Pour voir que cette valeur est effectivement atteinte, partons du vecteur $(3,-2)$. Sa longueur est
> $$
> \|(3,-2)\|_2
> =\sqrt{3^2+(-2)^2}
> =\sqrt{13}.
> $$
> Un vecteur **unitaire** est un vecteur de longueur $1$. Pour transformer $(3,-2)$ en vecteur unitaire sans changer sa direction, on le divise par sa longueur :
> $$
> (x,y)=\frac1{\sqrt{13}}(3,-2).
> $$
> Vérifions sa longueur :
> $$
> \left\|\frac1{\sqrt{13}}(3,-2)\right\|_2
> =\frac1{\sqrt{13}}\|(3,-2)\|_2
> =1.
> $$
> Calculons maintenant la sortie correspondante :
> $$
> \varphi\left(\frac3{\sqrt{13}},\frac{-2}{\sqrt{13}}\right)
> =3\times\frac3{\sqrt{13}}-2\times\frac{-2}{\sqrt{13}}
> =\frac{9+4}{\sqrt{13}}
> =\sqrt{13}.
> $$
> On a donc trouvé une entrée de longueur $1$ qui produit exactement la sortie $\sqrt{13}$. Cauchy-Schwarz disait qu'aucune entrée de longueur au plus $1$ ne pouvait produire davantage : la plus grande sortie est bien $\sqrt{13}$.
> Ainsi,
> $$
> \|\varphi\|_{(\mathbb R^2)'}=\sqrt{13}.
> $$
> Cela signifie, pour tout vecteur $(x,y)$, que
> $$
> |\varphi(x,y)|\leq\sqrt{13}\,\|(x,y)\|_2.
> $$
> Le nombre $\sqrt{13}$ est le plus petit facteur qui rend cette inégalité vraie pour tous les vecteurs. Il mesure donc la réponse maximale de la forme à une entrée de longueur $1$.

Dans un espace de Hilbert $H$, toute forme linéaire continue $\varphi$ peut être vue comme un produit scalaire avec un vecteur de $H$. Plus précisément, il existe un unique vecteur $f\in H$ tel que, pour tout $u\in H$,

$$
\varphi(u)=\langle u,f\rangle
$$

et l'on a même

$$
\|\varphi\|_{H'}=\|f\|_H.
$$

Cette égalité est plus forte que le simple fait que $\varphi$ soit continue : elle donne exactement la taille de la forme linéaire. En effet, si $\varphi(u)=\langle u,f\rangle$, Cauchy-Schwarz donne, pour tout $u\in H$,

$$
|\varphi(u)|
=
|\langle u,f\rangle|
\leq
\|u\|_H\|f\|_H.
$$

Cette inégalité est vraie pour tous les vecteurs $u$. En particulier, regardons seulement ceux qui vérifient $\|u\|_H\leq1$, car c'est exactement l'ensemble utilisé dans la définition de $\|\varphi\|_{H'}$. Rappelons cette définition :

$$
\|\varphi\|_{H'}
=
\sup_{\|u\|_H\leq1}|\varphi(u)|.
$$

$$
|\varphi(u)|
\leq
\|u\|_H\|f\|_H
\leq
1\times\|f\|_H
=
\|f\|_H.
$$

Toutes les valeurs $|\varphi(u)|$ considérées dans le supremum sont donc inférieures ou égales à $\|f\|_H$. Leur supremum l'est aussi :

$$
\|\varphi\|_{H'}\leq\|f\|_H.
$$

Si $f\ne0$, on peut tester la forme sur le vecteur unitaire qui a exactement la même direction que $f$ :

$$
u=\frac{f}{\|f\|_H}.
$$

Alors

$$
\varphi(u)
=
\left\langle\frac{f}{\|f\|_H},f\right\rangle
=
\frac{\|f\|_H^2}{\|f\|_H}
=
\|f\|_H.
$$

On a donc trouvé une entrée de norme $1$ qui produit une sortie de taille $\|f\|_H$. Or la norme $\|\varphi\|_{H'}$ est le supremum des sorties obtenues avec des entrées de norme au plus $1$. Elle est donc au moins aussi grande que cette sortie particulière :

$$
\|\varphi\|_{H'}\geq\|f\|_H.
$$

Auparavant, Cauchy-Schwarz avait donné l'inégalité opposée :

$$
\|\varphi\|_{H'}\leq\|f\|_H.
$$

Un même nombre ne peut pas être à la fois plus petit ou égal et plus grand ou égal à $\|f\|_H$ sans lui être égal. Les deux inégalités encadrent donc exactement la norme :

$$
\|\varphi\|_{H'}=\|f\|_H.
$$

Si $f=0$, la forme est la forme nulle, et les deux normes valent directement $0$ : la même conclusion reste vraie.

La notation $H'$ désigne le dual de $H$, c'est-à-dire l'ensemble des formes linéaires continues sur $H$. La correspondance du théorème est l'application

$$
R:H\longrightarrow H',
\qquad
f\longmapsto\varphi_f,
\qquad
\varphi_f(u)=\langle u,f\rangle.
$$

Elle est :

- **injective** : deux vecteurs $f$ différents donnent deux formes différentes ;
- **surjective** : Riesz affirme que toute forme linéaire continue est obtenue à partir d'un certain $f$ ;
- **isométrique** : elle conserve exactement les normes, puisque $\|\varphi_f\|_{H'}=\|f\|_H$.

On écrit cela sous la forme

$$
H'\simeq H.
$$

Le symbole $\simeq$ signifie ici « isomorphe de manière naturelle et en conservant les tailles ». Cette écriture ne signifie pas que le vecteur $f$ et la forme $\varphi_f$ sont littéralement le même objet : $f$ est un vecteur, tandis que $\varphi_f$ est une règle qui prend un vecteur $u$ en entrée et renvoie le nombre $\langle u,f\rangle$. Le théorème permet simplement de passer de l'un à l'autre sans perdre d'information.

#### La lecture géométrique

La valeur $\varphi(u)=\langle u,f\rangle$ mesure la composante de $u$ dans la direction de $f$ : elle est grande si $u$ est bien aligné avec $f$, négative s'ils pointent plutôt dans des directions opposées, et nulle si $u$ est orthogonal à $f$.

Ainsi,

$$
\varphi(u)=0
\qquad\Longleftrightarrow\qquad
u\perp f.
$$

Le noyau de $\varphi$, c'est-à-dire l'ensemble des vecteurs auxquels elle associe $0$, est donc l'hyperplan perpendiculaire à $f$. Le vecteur qui représente la forme linéaire est sa **normale géométrique**.

> [!example] Dans le plan euclidien
> Sur $\mathbb R^2$, considérons la forme linéaire
> $$
> \varphi(x,y)=3x-2y.
> $$
> Elle est représentée par le vecteur $f=(3,-2)$, car
> $$
> \varphi(x,y)
> =3x-2y
> =\langle(x,y),(3,-2)\rangle.
> $$
> Les vecteurs auxquels $\varphi$ associe $0$ vérifient $3x-2y=0$ : ils forment la droite perpendiculaire à $(3,-2)$. La taille de cette forme est
> $$
> \|\varphi\|=\|(3,-2)\|=\sqrt{13}.
> $$
> Cauchy-Schwarz donne alors, pour tout $(x,y)$,
> $$
> |3x-2y|
> \leq
> \sqrt{13}\sqrt{x^2+y^2}.
> $$

#### Le cas important $L^2$

Dans $L^2(\Omega)$, le produit scalaire est

$$
\langle u,f\rangle_{L^2}
=
\int_\Omega u(x)f(x)\,dx.
$$

Le théorème dit donc que toute forme linéaire continue sur $L^2$ est de la forme

$$
u\longmapsto\int_\Omega u(x)f(x)\,dx
$$

pour une unique fonction $f\in L^2(\Omega)$, à égalité presque partout près. C'est le cas $p=2$ de la dualité des espaces $L^p$ : ici, l'espace des tests est encore $L^2$, ce qui explique la géométrie particulièrement riche de cet espace.

> [!example] Mesurer la masse sur une région
> Soit $A\subset\Omega$ une région de mesure finie. La règle
> $$
> \varphi(u)=\int_Au(x)\,dx
> $$
> ne regarde que la partie de la fonction $u$ située dans $A$. Si $u\geq0$, elle mesure la masse de $u$ dans cette région ; si $u$ change de signe, elle mesure son bilan signé dans $A$.
>
> Pour l'écrire comme une intégrale sur tout le domaine $\Omega$, on utilise la fonction indicatrice de $A$ :
> $$
> \mathbf 1_A(x)=
> \begin{cases}
> 1 & \text{si }x\in A,\\
> 0 & \text{si }x\notin A.
> \end{cases}
> $$
> Multiplier $u(x)$ par $\mathbf 1_A(x)$ conserve donc $u(x)$ dans $A$ et le remplace par $0$ ailleurs. C'est pourquoi
> $$
> \varphi(u)
> =\int_\Omega u(x)\mathbf 1_A(x)\,dx
> =\langle u,\mathbf 1_A\rangle_{L^2}.
> $$
> Le vecteur représentant est donc $f=\mathbf 1_A$. Il appartient bien à $L^2(\Omega)$ parce que $A$ a une mesure finie :
> $$
> \|\mathbf 1_A\|_{L^2}^2
> =\int_\Omega|\mathbf 1_A(x)|^2\,dx
> =\int_A1\,dx
> =\mu(A).
> $$
> En prenant la racine carrée, on trouve
> $$
> \|\mathbf 1_A\|_{L^2}=\sqrt{\mu(A)}.
> $$
> Riesz donne alors la norme de la forme :
> $$
> \|\varphi\|_{(L^2)'}=\|\mathbf 1_A\|_{L^2}=\sqrt{\mu(A)}.
> $$
> En particulier, Cauchy-Schwarz donne, pour toute fonction $u\in L^2(\Omega)$,
> $$
> \left|\int_Au(x)\,dx\right|
> \leq
> \sqrt{\mu(A)}\,\|u\|_{L^2(\Omega)}.
> $$
>
> Prenons par exemple $\Omega=\mathbb R$ et $A=(0,4)$. Alors $\mu(A)=4$, donc la norme de la forme $u\mapsto\int_0^4u(x)\,dx$ vaut $\sqrt4=2$. Si $\|u\|_{L^2}\leq1$, son intégrale sur $(0,4)$ ne peut pas dépasser $2$ en valeur absolue. Cette borne est atteinte avec $u=\frac12\mathbf 1_{(0,4)}$ : cette fonction a une norme $L^2$ égale à $1$ et son intégrale sur $(0,4)$ vaut $2$.

#### Pourquoi ce résultat est naturel

L'idée de la preuve est géométrique. Si $\varphi$ n'est pas nulle, son noyau

$$
\ker\varphi=\{u\in H:\varphi(u)=0\}
$$

est un hyperplan fermé. On choisit un vecteur unitaire $e$ perpendiculaire à cet hyperplan. Tout vecteur $u$ se décompose alors en une partie dans le noyau et une composante dans la direction de $e$ :

$$
u=v+\langle u,e\rangle e,
\qquad
v\in\ker\varphi.
$$

La forme $\varphi$ s'annule sur $v$ ; elle ne voit donc que la composante selon $e$. C'est exactement le comportement d'un produit scalaire avec un vecteur parallèle à $e$. Le théorème formalise cette intuition et assure l'unicité du vecteur représentant.

### 3.4. Bases de Hilbert et séries de Fourier

Dans $\mathbb R^2$, tout vecteur se décompose sur les deux directions perpendiculaires de la base canonique :

$$
(x,y)=x(1,0)+y(0,1).
$$

Les nombres $x$ et $y$ sont ses coordonnées. Une base hilbertienne généralise cette idée à un espace qui peut avoir une infinité de directions, comme un espace de fonctions.

Une famille $(e_n)_{n\geq1}$ est **orthonormée** si chaque vecteur a une norme $1$ et si deux vecteurs différents sont orthogonaux :

$$
\|e_n\|=1,
\qquad
\langle e_n,e_m\rangle=0
\quad\text{si }n\ne m.
$$

Elle est une **base hilbertienne** si ces directions sont suffisamment nombreuses pour approcher n'importe quel élément de l'espace aussi bien que l'on veut. On peut alors reconstruire tout élément $f$ sous la forme d'une série

$$
f=\sum_n \langle f,e_n\rangle e_n.
$$

Les coefficients

$$
c_n=\langle f,e_n\rangle
$$

sont les coordonnées de $f$ dans cette base. Le produit scalaire avec $e_n$ isole la composante de $f$ dans la direction $e_n$, exactement comme la coordonnée $x$ isole la composante selon $(1,0)$ dans le plan.

La somme peut être infinie. Cela signifie que les sommes partielles

$$
S_N=\sum_{n=1}^N\langle f,e_n\rangle e_n
$$

approchent $f$ en norme :

$$
\|f-S_N\|\xrightarrow[N\to\infty]{}0.
$$

Il ne s'agit pas nécessairement d'une convergence point par point : pour une base hilbertienne, ce qui est garanti est que l'énergie de l'erreur tend vers zéro. Ici, le mot **énergie** désigne simplement le carré de la norme :

$$
\text{énergie de l'erreur après }N\text{ termes}
=
\|f-S_N\|^2.
$$

Dire que $S_N$ converge vers $f$ en norme revient donc à dire

$$
\|f-S_N\|^2\xrightarrow[N\to\infty]{}0.
$$

Parseval dit que l'énergie de $f$ se retrouve exactement dans ses coordonnées :

$$
\|f\|^2=\sum_n |\langle f,e_n\rangle|^2.
$$

Voici pourquoi cette formule est naturelle. Posons

$$
c_n=\langle f,e_n\rangle.
$$

Comme les vecteurs $e_1,\ldots,e_N$ sont orthonormés, Pythagore donne pour la somme partielle

$$
\|S_N\|^2
=
\left\|\sum_{n=1}^Nc_ne_n\right\|^2
=
\sum_{n=1}^N|c_n|^2.
$$

Développons cette égalité dans le cas réel. Par définition de la norme associée au produit scalaire,

$$
\|v\|=\sqrt{\langle v,v\rangle}
\qquad\text{et donc}\qquad
\|v\|^2=\langle v,v\rangle.
$$

On applique cette définition au vecteur

$$
v=\sum_{n=1}^Nc_ne_n.
$$

On obtient

$$
\left\|\sum_{n=1}^Nc_ne_n\right\|^2
=
\left\langle\sum_{n=1}^Nc_ne_n,\sum_{m=1}^Nc_me_m\right\rangle.
$$

On utilise ensuite la linéarité du produit scalaire pour développer toutes les paires de termes :

$$
\left\langle\sum_{n=1}^Nc_ne_n,\sum_{m=1}^Nc_me_m\right\rangle
=
\sum_{n=1}^N\sum_{m=1}^Nc_nc_m\langle e_n,e_m\rangle.
$$

Deux cas se présentent :

- si $n\ne m$, l'orthogonalité donne $\langle e_n,e_m\rangle=0$ ; tous les termes croisés disparaissent ;
- si $n=m$, la normalisation donne $\langle e_n,e_n\rangle=\|e_n\|^2=1$.

Il ne reste donc que les termes de la diagonale :

$$
\sum_{n=1}^N\sum_{m=1}^Nc_nc_m\langle e_n,e_m\rangle
=
\sum_{n=1}^Nc_n^2
=
\sum_{n=1}^N|c_n|^2.
$$

Par exemple, avec seulement deux directions orthonormées,

$$
\|c_1e_1+c_2e_2\|^2
=
c_1^2\|e_1\|^2
+2c_1c_2\langle e_1,e_2\rangle
+c_2^2\|e_2\|^2
=
c_1^2+c_2^2.
$$

Le terme central disparaît parce que $e_1$ et $e_2$ sont orthogonaux. C'est exactement le théorème de Pythagore, répété avec $N$ directions.

Voyons aussi pourquoi l'erreur est orthogonale aux directions déjà utilisées. Pour $k\leq N$, on calcule

$$
\langle f-S_N,e_k\rangle
=
\left\langle f-\sum_{n=1}^Nc_ne_n,e_k\right\rangle
=
\langle f,e_k\rangle-\sum_{n=1}^Nc_n\langle e_n,e_k\rangle.
$$

Par définition, $c_k=\langle f,e_k\rangle$. Dans la somme, tous les termes sont nuls sauf celui où $n=k$, car les $e_n$ sont orthogonaux. Ce terme restant vaut

$$
c_k\langle e_k,e_k\rangle=c_k,
$$

puisque $\|e_k\|=1$. On obtient donc

$$
\langle f-S_N,e_k\rangle=c_k-c_k=0.
$$

L'erreur $f-S_N$ est ainsi orthogonale à chaque vecteur $e_1,\ldots,e_N$. Or $S_N$ est une combinaison de ces vecteurs. Par linéarité,

$$
\langle f-S_N,S_N\rangle
=
\left\langle f-S_N,\sum_{k=1}^Nc_ke_k\right\rangle
=
\sum_{k=1}^Nc_k\langle f-S_N,e_k\rangle
=0.
$$

L'erreur est donc orthogonale à $S_N$ lui-même. On peut alors encore appliquer Pythagore :

$$
\|f\|^2
=
\|S_N\|^2+\|f-S_N\|^2
=
\sum_{n=1}^N|c_n|^2+\|f-S_N\|^2.
$$

Cette identité donne une lecture très concrète : après les $N$ premières coordonnées, la somme $\sum_{n=1}^N|c_n|^2$ est l'énergie déjà expliquée par la reconstruction, tandis que $\|f-S_N\|^2$ est l'énergie qui reste dans l'erreur.

Lorsque $N$ tend vers l'infini, une base hilbertienne garantit que l'énergie restante $\|f-S_N\|^2$ tend vers $0$. Il reste alors exactement

$$
\|f\|^2=\sum_{n=1}^{+\infty}|c_n|^2.
$$

La quantité $|\langle f,e_n\rangle|^2=|c_n|^2$ est donc l'énergie portée par la direction $e_n$. Parseval affirme qu'il n'y a ni perte ni création d'énergie lorsqu'on remplace $f$ par la liste de ses coordonnées.

> [!example] Une décomposition avec des sinus
> Dans $L^2(0,1)$, les fonctions
> $$
> e_n(x)=\sqrt2\sin(n\pi x),
> \qquad n\geq1,
> $$
> forment une base hilbertienne. Elles sont orthonormées : deux sinusoïdes de fréquences différentes sont orthogonales, et le facteur $\sqrt2$ assure que chacune a une norme $L^2$ égale à $1$.
>
> Vérifions ces deux propriétés. Si $n\ne m$, leur produit scalaire vaut
> $$
> \langle e_n,e_m\rangle_{L^2(0,1)}
> =2\int_0^1\sin(n\pi x)\sin(m\pi x)\,dx.
> $$
> On utilise l'identité trigonométrique
> $$
> \sin(a)\sin(b)
> =\frac12\bigl(\cos(a-b)-\cos(a+b)\bigr).
> $$
> Ici, elle transforme l'intégrale en une différence d'intégrales de cosinus de fréquences non nulles :
> $$
> \langle e_n,e_m\rangle_{L^2(0,1)}
> =\int_0^1\cos((n-m)\pi x)\,dx
> -\int_0^1\cos((n+m)\pi x)\,dx.
> $$
> Détaillons cette annulation. Pour tout entier non nul $k$,
> $$
> \int_0^1\cos(k\pi x)\,dx
> =\left[\frac{\sin(k\pi x)}{k\pi}\right]_{x=0}^{x=1}
> =\frac{\sin(k\pi)-\sin(0)}{k\pi}
> =0,
> $$
> car $\sin(k\pi)=0$ pour tout entier $k$. Dans notre calcul, les entiers sont $k=n-m$ et $k=n+m$ ; ils sont tous deux non nuls lorsque $n\ne m$. Les deux intégrales de cosinus sont donc nulles. Ainsi,
> $$
> \langle e_n,e_m\rangle_{L^2(0,1)}=0
> \qquad\text{si }n\ne m.
> $$
>
> Pour la norme, on utilise
> $$
> \sin^2(a)=\frac{1-\cos(2a)}2.
> $$
> En prenant $a=n\pi x$ et en intégrant, on obtient
> $$
> \int_0^1\sin^2(n\pi x)\,dx
> =\frac12\int_0^1\bigl(1-\cos(2n\pi x)\bigr)\,dx
> =\frac12\left(
> \left[x\right]_{x=0}^{x=1}
> -
> \left[\frac{\sin(2n\pi x)}{2n\pi}\right]_{x=0}^{x=1}
> \right)
> =\frac12(1-0)
> =\frac12.
> $$
> Rappelons que, pour toute fonction $h$,
> $$
> \|h\|_{L^2(0,1)}
> =\left(\int_0^1|h(x)|^2\,dx\right)^{1/2}.
> $$
> En prenant $h(x)=\sin(n\pi x)$ et en utilisant le calcul précédent, on trouve
> $$
> \|\sin(n\pi x)\|_{L^2(0,1)}
> =\left(\frac12\right)^{1/2}
> =\frac1{\sqrt2}.
> $$
> Sans le facteur $\sqrt2$, la sinusoïde n'aurait donc pas une norme égale à $1$. En multipliant la fonction par $\sqrt2$, son carré est multiplié par $2$ :
> $$
> \|e_n\|_{L^2(0,1)}^2
> =\int_0^1|\sqrt2\sin(n\pi x)|^2\,dx
> =2\times\frac12
> =1.
> $$
> Ainsi, les $e_n$ sont à la fois orthogonaux entre eux et de norme $1$ : ils sont orthonormés.
>
> Prenons la fonction
> $$
> f=3e_1-e_2.
> $$
> Elle ne possède que deux coordonnées non nulles :
> $$
> \langle f,e_1\rangle=3,
> \qquad
> \langle f,e_2\rangle=-1,
> \qquad
> \langle f,e_n\rangle=0\quad\text{pour }n\geq3.
> $$
> La reconstruction est donc
> $$
> f=3e_1-e_2+0e_3+0e_4+\cdots.
> $$
> Parseval donne immédiatement son énergie :
> $$
> \|f\|_{L^2(0,1)}^2
> =|3|^2+|-1|^2
> =10.
> $$
> Au lieu de développer et intégrer le carré de la fonction, il suffit donc d'additionner les carrés de ses coordonnées.

> [!example] Les sinusoïdes
> Dans $L^2([0,T])$, les sinus et cosinus forment la base de Fourier classique. Décomposer un signal dans cette base revient à séparer ses fréquences. Des bases de sinus seules ou de cosinus seules existent aussi ; elles sont adaptées, respectivement, à des conditions de bord de type Dirichlet et Neumann.

### 3.5. Lax-Milgram : minimiser une énergie pour résoudre une équation

Lax-Milgram est un théorème qui transforme un problème d'équation en un problème de géométrie dans un espace de Hilbert. Au lieu de chercher directement une fonction qui vérifie une équation point par point, on cherche une fonction $u$ qui vérifie une identité contre tous les vecteurs tests $v$.

On se donne donc un espace de Hilbert $H$, une application bilinéaire

$$
a:H\times H\longrightarrow\mathbb R,
$$

ainsi qu'une forme linéaire continue $b:H\longrightarrow\mathbb R$. La forme $a$ est supposée :

- **continue** : il existe $M>0$ tel que
  $$
  |a(u,v)|\leq M\|u\|_H\|v\|_H
  $$
  pour tous $u,v\in H$ ;
- **coercive** : il existe $\alpha>0$ tel que
  $$
  a(v,v)\geq\alpha\|v\|_H^2
  $$
  pour tout $v\in H$.

La continuité dit que $a$ ne réagit pas de façon instable à une petite variation de ses deux arguments. La coercivité est la condition essentielle : elle impose un coût au moins quadratique dès que la norme de $v$ devient grande.

Le théorème de Lax-Milgram affirme alors qu'il existe un unique $u\in H$ tel que

$$
a(u,v)=b(v)
\qquad\text{pour tout }v\in H.
$$

Cette formulation est dite **faible** : plutôt que d'imposer une égalité point par point, on demande qu'elle soit vraie après intégration contre chaque test $v$. C'est précisément le bon langage lorsque la solution n'est pas assez régulière pour posséder toutes les dérivées classiques attendues.

#### Le point de vue de l'énergie

Si $a$ est de plus symétrique, on peut définir l'énergie

$$
E(u)=\frac12a(u,u)-b(u).
$$

Le terme $a(u,u)$ joue le rôle d'une énergie interne, tandis que $b(u)$ représente l'action d'une source extérieure. La coercivité implique, en substance,

$$
E(u)\geq\frac\alpha2\|u\|_H^2-\|b\|_{H'}\|u\|_H.
$$

Quand $\|u\|_H$ devient très grand, le terme quadratique finit par dominer le terme linéaire : l'énergie ne peut pas diminuer indéfiniment en partant à l'infini. Elle possède donc un unique minimiseur. En calculant la variation de $E$ dans toute direction $v$, on retrouve exactement l'équation faible

$$
a(u,v)=b(v)
\qquad\text{pour tout }v\in H.
$$

> [!example] L'équation de Poisson sur un intervalle
> Considérons le problème
> $$
> -u''(x)=g(x)
> \qquad\text{sur }(0,1),
> \qquad
> u(0)=u(1)=0.
> $$
> Les conditions aux bords sont incluses en travaillant dans l'espace
> $$
> H=H_0^1(0,1),
> $$
> dont les fonctions ont une dérivée faible dans $L^2$ et s'annulent aux extrémités. On pose
> $$
> a(u,v)=\int_0^1u'(x)v'(x)\,dx,
> \qquad
> b(v)=\int_0^1g(x)v(x)\,dx.
> $$
> La formulation faible consiste à chercher $u\in H_0^1(0,1)$ telle que
> $$
> \int_0^1u'(x)v'(x)\,dx
> =
> \int_0^1g(x)v(x)\,dx
> \qquad\text{pour tout }v\in H_0^1(0,1).
> $$
> Cette identité est ce que l'on obtient en multipliant formellement $-u''=g$ par $v$ puis en intégrant par parties. Lax-Milgram garantit qu'elle possède une unique solution faible, même sans supposer au départ que $u$ est deux fois dérivable.
>
> L'énergie associée est
> $$
> E(w)
> =\frac12\int_0^1|w'(x)|^2\,dx
> -\int_0^1g(x)w(x)\,dx.
> $$
> Le premier terme pénalise les variations rapides ; le second pousse la fonction dans la direction imposée par la source $g$.
>
> Si $g(x)=1$, la solution classique est
> $$
> u(x)=\frac{x(1-x)}2.
> $$
> Elle s'annule bien en $0$ et en $1$, et
> $$
> -u''(x)=1.
> $$
> Lax-Milgram affirme surtout qu'une unique solution existe déjà dans $H_0^1(0,1)$, avant même d'avoir trouvé cette formule explicite.

---

## 4. Distributions : dériver au-delà des fonctions classiques

Certaines fonctions intéressantes ont des discontinuités, ou même sont concentrées en un point. On ne peut pas toujours les dériver au sens classique. L'idée des distributions est de ne plus regarder directement l'objet, mais la façon dont il agit sur des fonctions tests lisses à support compact.

On note

$$
\mathcal D(\Omega)=C_c^\infty(\Omega),\qquad
\mathcal D'(\Omega)=\text{dual de }\mathcal D(\Omega).
$$

Une distribution $T$ attribue un nombre $\langle T,\varphi\rangle$ à chaque test $\varphi$. Les tests sont lisses et localisés : ils permettent de sonder l'objet sans lui demander des valeurs ponctuelles trop exigeantes.

### 4.1. Exemples fondamentaux

- Une fonction localement intégrable $f$ définit la distribution $T_f$ par
  $$
  \langle T_f,\varphi\rangle=\int_\Omega f(x)\varphi(x)\,dx.
  $$
- La masse de Dirac en $0$ est définie par $\langle\delta_0,\varphi\rangle=\varphi(0)$. Elle modélise une masse ponctuelle idéale.
- La dérivée $\delta_0'$ est donnée par $\langle\delta_0',\varphi\rangle=-\varphi'(0)$. Elle mesure la pente du test au point zéro.
- Le peigne de Dirac additionne les valeurs de $\varphi$ sur un réseau de points : c'est le modèle mathématique d'un échantillonnage périodique.

### 4.2. La dérivée distributionnelle

La dérivée de $T$ est définie par déplacement de la dérivée sur le test :

$$
\langle T',\varphi\rangle=-\langle T,\varphi'\rangle.
$$

Cette formule est une intégration par parties sans terme de bord : le support compact de $\varphi$ fait disparaître ce terme. Si $f$ est dérivable au sens classique, on retrouve bien sa dérivée classique.

> [!example] Le saut devient une Dirac
> La fonction de Heaviside $H(x)=\mathbf 1_{(0,\infty)}(x)$ est constante de chaque côté de zéro, donc sa dérivée classique vaut $0$ là où elle est définie. Pourtant elle possède un saut de taille $1$ en zéro. Sa dérivée distributionnelle est $H'=\delta_0$ : toute la variation est concentrée au point du saut.

### 4.3. Convergence et produits

Une suite de distributions $T_n$ converge vers $T$ si $\langle T_n,\varphi\rangle\to\langle T,\varphi\rangle$ pour toute fonction test $\varphi$. On teste donc la convergence par toutes les mesures lisses et localisées possibles.

On peut multiplier une distribution par une fonction lisse :

$$
\langle fT,\varphi\rangle=\langle T,f\varphi\rangle.
$$

En revanche, le produit de deux distributions n'est **pas défini en général**. Par exemple, tenter de donner un sens à $\delta_0^2$ demande des hypothèses supplémentaires : c'est une limite importante de ce calcul généralisé.

---

## 5. Espaces de Sobolev : assez réguliers pour dériver faiblement

Les espaces de Sobolev réunissent les fonctions dont les dérivées, comprises au sens distributionnel, restent contrôlées dans $L^p$. Pour un entier $k\ge 1$,

$$
W^{k,p}(\Omega)=\{f : f \text{ et toutes ses dérivées faibles jusqu'à l'ordre }k \text{ sont dans }L^p\}.
$$

En dimension un, une norme typique est

$$
\|f\|_{W^{k,p}}
=\left(\|f\|_{L^p}^p+\|f'\|_{L^p}^p+\cdots+\|f^{(k)}\|_{L^p}^p\right)^{1/p}.
$$

On note $H^k=W^{k,2}$. Ces espaces sont particulièrement agréables car les $H^k$ sont des espaces de Hilbert : on y retrouve projections, bases et minimisation d'énergie.

### 5.1. Ce que mesure vraiment $W^{1,p}$

Être dans $W^{1,p}$, ce n'est pas forcément être dérivable partout. Cela veut dire que la fonction a une dérivée faible qui est une vraie fonction de $L^p$, donc que ses variations sont contrôlées en moyenne.

> [!example] Deux fonctions qui se ressemblent, mais pas pour Sobolev
> - $f(x)=|x|$ appartient à $W^{1,p}_{\mathrm{loc}}$ : sa dérivée faible est $\operatorname{sgn}(x)$, qui est bornée. Le coin en zéro est acceptable à l'ordre 1.
> - La fonction de Heaviside $H$ n'appartient pas à $W^{1,p}_{\mathrm{loc}}$ pour $p\ge1$, car sa dérivée faible est une Dirac, et une Dirac n'est pas une fonction de $L^p$. Un saut est donc trop violent à l'ordre 1.

### 5.2. Pourquoi Sobolev est l'espace naturel des EDP

Les équations aux dérivées partielles demandent rarement une solution deux fois dérivable partout. Il suffit souvent de pouvoir intégrer par parties contre tous les tests. Les espaces de Sobolev fournissent exactement ce niveau de régularité faible, tout en restant complets.

Pour le problème de Poisson avec condition de bord nulle, l'espace naturel est généralement $H^1_0(\Omega)$ : fonctions de $H^1$ dont la trace au bord est nulle. Dans cet espace, la quantité $\int|\nabla u|^2$ est l'énergie pertinente, et Lax-Milgram transforme l'équation en un problème de minimisation bien posé.

---

## 6. Carte mentale : quel outil utiliser ?

- **Une limite est sous une intégrale** : chercher Beppo Levi ou la convergence dominée, afin de justifier l'échange limite/intégrale.
- **Une intégrale porte sur deux variables** : utiliser Tonelli si $f\ge0$, Fubini si $|f|\in L^1$, afin de changer l'ordre d'intégration sans erreur.
- **Un produit $fg$ doit être intégrable** : appliquer Hölder et associer $L^p$ à $L^{p'}$.
- **On veut une meilleure approximation** : utiliser la densité de $C_c$ ou $C_c^\infty$, qui remplace une fonction rugueuse par une fonction simple.
- **On cherche le meilleur élément d'un modèle** : projeter dans un Hilbert ; le résidu devient orthogonal au modèle.
- **On cherche une solution d'EDP par énergie** : utiliser Lax-Milgram dans un $H^k$ adapté ; il donne l'existence et l'unicité du minimiseur.
- **Une dérivée classique n'existe pas** : passer aux distributions puis aux Sobolev, c'est-à-dire intégrer par parties contre des fonctions tests.

## 7. Les messages essentiels en une page

1. $L^p$ ne décrit pas seulement des fonctions : il encode la manière dont on accepte de mesurer leur taille.
2. Les passages à la limite sont puissants, mais doivent être protégés par monotonie ou domination.
3. $L^2$ est le lieu de la géométrie : orthogonalité, projections et Fourier y deviennent possibles.
4. Les distributions élargissent la notion de dérivée ; les Sobolev sélectionnent celles dont les dérivées faibles restent contrôlables.
5. Une grande partie des EDP peut se lire comme : « minimiser une énergie dans le bon espace de Hilbert ».

## Pour aller plus loin

Le support recommande notamment :

- H. Brézis, *Analyse fonctionnelle* ;
- L. C. Evans, *Partial Differential Equations* ;
- C. Gasquet et P. Witomski, *Analyse de Fourier et applications*.

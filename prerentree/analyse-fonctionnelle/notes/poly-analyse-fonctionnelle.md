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
> Si $f\geq0$, la suite $f_n=\min(f,n)$ augmente point par point vers $f$. Cette approximation coupe seulement les pics trop hauts et les rétablit progressivement. C'est l'usage typique de Beppo Levi : approcher une fonction positive éventuellement compliquée par des fonctions plus contrôlables.

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
> Les fonctions $f_n(x)=n\,\mathbf 1_{(0,1/n)}(x)$ convergent vers $0$ presque partout sur $(0,1)$, mais $\int_0^1 f_n=1$ pour tout $n$. Elles n'admettent pas de majorant intégrable commun. C'est exactement le phénomène que l'hypothèse de domination exclut.

> [!tip] À retenir
> - Positivité + croissance : penser **Beppo Levi**.
> - Convergence presque partout + une enveloppe intégrable : penser **convergence dominée**.
> - Sans une de ces protections, vérifier le passage à la limite au lieu de le supposer.

### 1.3. Densité des fonctions continues à support compact

Les fonctions de $C_c(\Omega)$ sont continues et nulles en dehors d'une zone compacte de $\Omega$. Elles sont denses dans $L^1$, et plus généralement dans $L^p$ pour $p<\infty$.

L'intuition : même une fonction rugueuse peut être d'abord coupée loin à l'infini, puis lissée à petite échelle. On la remplace ainsi par des fonctions beaucoup plus faciles à manipuler, sans perdre d'information au sens de la norme $L^p$.

### 1.4. Intégrales doubles : Tonelli et Fubini

Pour une fonction $f(x,y)$, deux questions se posent : l'intégrale double existe-t-elle, et peut-on intégrer d'abord en $y$, puis en $x$ ?

- **Tonelli** : si $f\ge 0$, l'ordre des intégrales peut être interverti, même si le résultat vaut $+\infty$. C'est l'analogue continu du fait que l'ordre de sommation de termes positifs ne change pas la somme.
- **Fubini** : si $f$ est intégrable en valeur absolue sur le produit, alors les intégrales itérées existent presque partout, sont intégrables, et donnent la même valeur que l'intégrale double.

> [!warning] Le point à ne pas oublier
> Une fonction peut avoir des intégrales le long de chaque droite horizontale et verticale sans que Fubini s'applique. La condition qui protège réellement l'échange des intégrales est l'intégrabilité de $|f|$. Sans elle, l'ordre de sommation peut modifier le résultat, comme pour une série conditionnellement convergente.

### 1.5. Changement de variables

Un changement de variables remplace une zone compliquée par une zone plus simple. Si $y=\Phi(x)$, le facteur $|\det D\Phi(x)|$ corrige la déformation des volumes :

$$
\int_{\Omega_2} f(y)\,dy
= \int_{\Omega_1} f(\Phi(x))\,|\det D\Phi(x)|\,dx.
$$

> [!example] Les coordonnées polaires
> Dans le plan, $(r,\theta)\mapsto(r\cos\theta,r\sin\theta)$ transforme un disque en rectangle $[0,R]\times[0,2\pi]$. Le jacobien vaut $r$, d'où
> $$
> \int_{B(0,R)} f(x,y)\,dx\,dy
> =\int_0^{2\pi}\int_0^R f(r\cos\theta,r\sin\theta)\,r\,dr\,d\theta.
> $$
> Le $r$ supplémentaire exprime simplement que les couronnes sont de plus en plus longues.

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
\int |fg|\le \|f\|_{L^p}\,\|g\|_{L^{p'}}.
$$

Elle généralise Cauchy-Schwarz, qui est le cas $p=2$. C'est l'outil standard pour montrer qu'un terme intégral est bien défini et contrôlé.

> [!example] Le cas $L^2$
> Pour deux signaux $f$ et $g$, $\int fg$ mesure leur corrélation. Hölder avec $p=2$ assure qu'elle reste finie dès que les deux signaux ont une énergie finie.

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

- **Cauchy-Schwarz** : $|\langle f,g\rangle|\le\|f\|\,\|g\|$. Deux vecteurs ne peuvent pas être plus corrélés que ne le permettent leurs tailles.
- **Pythagore** : si $f\perp g$, alors $\|f+g\|^2=\|f\|^2+\|g\|^2$. Les énergies de composantes orthogonales s'additionnent.
- **Identité du parallélogramme** : elle caractérise les normes qui proviennent d'un produit scalaire. Toutes les normes ne viennent donc pas d'une géométrie euclidienne.

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

Toute forme linéaire continue $\varphi$ sur un espace de Hilbert s'écrit de façon unique

$$
\varphi(u)=\langle u,f\rangle
$$

pour un certain $f$ de l'espace. Autrement dit, dans un Hilbert, les objets qui « testent » les vecteurs sont eux-mêmes des vecteurs. Le dual et l'espace se confondent naturellement.

### 3.4. Bases de Hilbert et séries de Fourier

Une base hilbertienne $(e_n)$ est une famille orthonormée suffisamment riche pour reconstruire tout élément $f$ :

$$
f=\sum_n \langle f,e_n\rangle e_n.
$$

Les coefficients $\langle f,e_n\rangle$ sont les coordonnées de $f$. Parseval dit que l'énergie se conserve dans ces coordonnées :

$$
\|f\|^2=\sum_n |\langle f,e_n\rangle|^2.
$$

> [!example] Les sinusoïdes
> Dans $L^2([0,T])$, les sinus et cosinus forment la base de Fourier classique. Décomposer un signal dans cette base revient à séparer ses fréquences. Des bases de sinus seules ou de cosinus seules existent aussi ; elles sont adaptées, respectivement, à des conditions de bord de type Dirichlet et Neumann.

### 3.5. Lax-Milgram : minimiser une énergie pour résoudre une équation

Beaucoup d'équations se réécrivent comme la minimisation d'une énergie

$$
E(u)=\frac12a(u,u)-b(u),
$$

où $a$ est bilinéaire, continue, symétrique et **coercive**. La coercivité signifie, en substance, que l'énergie quadratique augmente au moins comme $\|u\|^2$ : elle empêche la minimisation de partir à l'infini.

Le théorème de Lax-Milgram assure alors qu'il existe un unique minimiseur $u$, caractérisé par

$$
a(u,v)=b(v)\qquad \text{pour tout test }v.
$$

> [!example] L'équation de Poisson
> Chercher une fonction $u$ nulle au bord qui résout $-\Delta u=f$ revient à minimiser
> $$
> E(u)=\frac12\int_\Omega |\nabla u|^2-\int_\Omega fu.
> $$
> La première intégrale pénalise les variations rapides ; la seconde représente la force source $f$. Lax-Milgram donne alors existence et unicité de la solution faible dans le bon espace de Sobolev.

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

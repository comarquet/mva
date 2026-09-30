---
title: Analyse de Fourier - note de cours intuitive
tags:
  - mathematiques
  - analyse-de-fourier
  - traitement-du-signal
source: "[[../sources/cours-fourier.pdf|cours-fourier.pdf]]"
---

# Analyse de Fourier

## L'idée en une phrase

L'analyse de Fourier consiste à décomposer un signal en **oscillations simples**. Au lieu de regarder « comment le signal varie dans le temps ou l'espace », on regarde **quelles fréquences le composent et avec quelle intensité**.

On peut la voir comme un changement de repère : de même qu'un vecteur se décompose sur une base orthonormée, une fonction se décompose sur des ondes $e^{i\omega x}$ (ou, de manière équivalente, des sinus et cosinus).

> [!example] Image mentale
> Un son est une variation de pression dans le temps. Sa transformée de Fourier indique les notes graves et aiguës présentes, ainsi que leur amplitude. Pour une image, les basses fréquences décrivent les grandes zones lisses et les hautes fréquences les détails et les contours.

## Ressources d'aide

- [Guide interactif de la transformée de Fourier — BetterExplained](https://betterexplained.com/articles/an-interactive-guide-to-the-fourier-transform/?utm_source=chatgpt.com)
- [Vidéo : les séries de Fourier, une introduction visuelle](https://www.youtube.com/watch?v=r6sGWTCMz2k)
- [Vidéo : la transformée de Fourier, une introduction visuelle](https://www.youtube.com/watch?v=spUNpyF58BY)

## 1. Les trois versions de Fourier

- **Transformée de Fourier** : signal défini sur toute la droite réelle, par exemple un son continu de durée théorique infinie.
- **Série de Fourier** : signal périodique, par exemple une vibration qui se répète à l'identique.
- **Transformée de Fourier discrète (TFD / DFT)** : un nombre fini d'échantillons, donc un vecteur. C'est la version utilisée en calcul numérique.

Le même principe se retrouve à chaque fois : les ondes complexes forment une base, et les coefficients de Fourier sont les coordonnées du signal dans cette base.

---

## 2. Transformée de Fourier sur $\mathbb{R}$

### Définition et lecture intuitive

Pour une fonction intégrable $f : \mathbb{R} \to \mathbb{C}$, on adopte la convention du support :

$$
\widehat f(y) = \int_{\mathbb{R}} f(x)e^{-ixy}\,dx.
$$

- $x$ est la variable du signal : temps, position, etc.
- $y$ est une **fréquence angulaire**.
- $\widehat f(y)$ mesure à quel point l'oscillation $e^{iyx}$ est présente dans $f$.

Le symbole complexe $e^{iyx}$ est surtout un raccourci efficace : grâce à $e^{i\theta}=\cos\theta+i\sin\theta$, il rassemble cosinus et sinus dans une seule écriture. Pour un signal réel, on peut toujours l'interpréter en termes de cosinus et de sinus.

La formule inverse reconstruit le signal à partir de toutes ses fréquences :

$$
f(x) = \frac{1}{2\pi}\int_{\mathbb{R}} \widehat f(y)e^{ixy}\,dy.
$$

Ainsi, $f$ est une superposition continue d'ondes. Le facteur $1/(2\pi)$ vient uniquement de la convention choisie ; d'autres livres répartissent ce facteur différemment entre la transformée et son inverse.

> [!tip] Ne pas confondre fréquence et fréquence angulaire
> Ici, la fréquence est $y$ en radians par unité de $x$. La fréquence ordinaire, en cycles par unité, vaut $y/(2\pi)$.

### Conservation de l'énergie : Plancherel

La quantité $\int |f(x)|^2\,dx$ représente souvent l'énergie du signal. La transformée ne la fait pas disparaître : elle la redistribue entre les fréquences.

$$
\int_{\mathbb{R}} |f(x)|^2\,dx
= \frac{1}{2\pi}\int_{\mathbb{R}} |\widehat f(y)|^2\,dy.
$$

Le membre de gauche mesure l'énergie du signal dans le domaine direct : pour un son, on additionne son intensité à chaque instant. Le membre de droite mesure exactement cette même énergie, mais répartie par fréquences : $\widehat f(y)$ indique la quantité d'oscillation de fréquence $y$ présente dans le signal, et $|\widehat f(y)|^2$ mesure la contribution de cette fréquence à l'énergie.

L'idée est la même qu'en algèbre linéaire. Si un vecteur est écrit dans une base orthonormée,

$$
v=a_1e_1+\cdots+a_ne_n,
$$

alors sa norme satisfait

$$
\|v\|^2=|a_1|^2+\cdots+|a_n|^2.
$$

Changer de base modifie les coordonnées $a_i$, mais pas la longueur du vecteur. La transformée de Fourier est la version continue de ce changement de base : le signal $f$ joue le rôle du vecteur, les ondes $e^{iyx}$ sont les directions élémentaires, et $\widehat f(y)$ est la coordonnée associée à la fréquence $y$.

> [!tip] À retenir
> Dans le domaine direct, l'énergie est répartie selon les positions $x$ ; dans le domaine fréquentiel, elle est répartie selon les fréquences $y$. La quantité totale reste la même. Le facteur $1/(2\pi)$ vient uniquement de la convention de normalisation retenue pour la transformée et son inverse.

Cette identité est très utile : on peut analyser ou filtrer un signal dans le domaine fréquentiel sans perdre de contrôle sur son énergie.

### Convolution : mélanger localement devient multiplier

La convolution de $f$ et $g$ est définie par

$$
(f * g)(t) = \int_{\mathbb{R}} f(x)g(t-x)\,dx.
$$

Pour chaque position $t$, on fait glisser $g$, on le superpose à $f$, puis on additionne les contributions. C'est le modèle naturel d'un flou, d'une moyenne glissante ou de la réponse d'un système à une entrée.

La règle fondamentale est :

$$
\widehat{f * g} = \widehat f\,\widehat g.
$$

Autrement dit, une opération qui semble compliquée dans le domaine direct devient un simple produit fréquence par fréquence. Réciproquement,

$$
\widehat{fg} = \frac{1}{2\pi}\,\widehat f * \widehat g.
$$

> [!example] Flou d'un signal
> Convoler un signal par une petite cloche lisse revient à atténuer ses hautes fréquences. Les variations rapides disparaissent : le signal devient plus lisse.

La **corrélation** ressemble à la convolution, mais sert plutôt à mesurer une ressemblance après décalage :

$$
(f \star g)(t) = \int_{\mathbb{R}} \overline{f(x)}g(t+x)\,dx.
$$

Elle intervient par exemple pour rechercher un motif dans un signal.

### Règles à connaître

#### Linéarité

La linéarité est immédiate :

$$
\widehat{\lambda f+\mu g}=\lambda\widehat f+\mu\widehat g.
$$

**Démonstration.** On développe simplement l'intégrale et on utilise sa linéarité :

$$
\begin{aligned}
\widehat{\lambda f+\mu g}(y)
&=\int_{\mathbb{R}}\bigl(\lambda f(x)+\mu g(x)\bigr)e^{-ixy}\,dx \\
&=\lambda\int_{\mathbb{R}}f(x)e^{-ixy}\,dx
+\mu\int_{\mathbb{R}}g(x)e^{-ixy}\,dx \\
&=\lambda\widehat f(y)+\mu\widehat g(y).
\end{aligned}
$$

Les autres règles se retiennent mieux par leur sens. Dans les démonstrations, $\mathcal{F}[h]$ désigne la transformée de Fourier de $h$.

#### Décalage temporel ou spatial

Décaler $f$ ne change pas les amplitudes de ses fréquences ; cela change seulement leur phase.

  $$
  f(x-a) \quad\longleftrightarrow\quad e^{-iay}\widehat f(y).
  $$

  **Démonstration.** Posons $u=x-a$, donc $x=u+a$ et $dx=du$. Le décalage apparaît alors comme un facteur qui ne dépend plus de $u$ :

  $$
  \begin{aligned}
  \mathcal{F}\bigl[f(x-a)\bigr](y)
  &=\int_{\mathbb{R}}f(x-a)e^{-ixy}\,dx \\
  &=\int_{\mathbb{R}}f(u)e^{-i(u+a)y}\,du \\
  &=e^{-iay}\int_{\mathbb{R}}f(u)e^{-iuy}\,du \\
  &=e^{-iay}\widehat f(y).
  \end{aligned}
  $$

  Il faut lire ce résultat à fréquence $y$ fixée. Si l'on note $h_a(x)=f(x-a)$ le signal décalé, le calcul précédent affirme que

  $$
  \widehat{h_a}(y)=e^{-iay}\widehat f(y).
  $$

  Le coefficient du nouveau signal, $\widehat{h_a}(y)$, est donc l'ancien coefficient $\widehat f(y)$ multiplié par le nombre complexe $e^{-iay}$.

  Pour comprendre le module, rappelons que si $z=p+iq$ est un nombre complexe, alors sa longueur dans le plan complexe est

  $$
  |z|=\sqrt{p^2+q^2}.
  $$

  La formule d'Euler donne $e^{-iay}=\cos(ay)-i\sin(ay)$. Sa partie réelle est donc $\cos(ay)$ et sa partie imaginaire est $-\sin(ay)$ ; ainsi

  $$
  \left|e^{-iay}\right|
  =\sqrt{\cos^2(ay)+\bigl(-\sin(ay)\bigr)^2}
  =\sqrt{\cos^2(ay)+\sin^2(ay)}
  =1.
  $$

  Écrivons maintenant l'ancien coefficient sous forme polaire $\widehat f(y)=r(y)e^{i\varphi(y)}$, où $r(y)=|\widehat f(y)|$ est son amplitude et $\varphi(y)$ sa phase. En remplaçant $\widehat f(y)$ dans la formule précédente, on obtient

  $$
  \begin{aligned}
  \widehat{h_a}(y)
  &=e^{-iay}\widehat f(y) \\
  &=e^{-iay}\,r(y)e^{i\varphi(y)} \\
  &=r(y)e^{i\left(\varphi(y)-ay\right)}.
  \end{aligned}
  $$

  La dernière égalité utilise $e^\alpha e^\beta=e^{\alpha+\beta}$. Pour voir précisément pourquoi l'amplitude est préservée, on prend le module des deux côtés :

  $$
  \left|\widehat{h_a}(y)\right|
  =\left|e^{-iay}\widehat f(y)\right|
  =\left|e^{-iay}\right|\,\left|\widehat f(y)\right|
  =\left|\widehat f(y)\right|.
  $$

  La deuxième égalité utilise la règle $|zw|=|z|\,|w|$ pour les nombres complexes, et la dernière utilise $|e^{-iay}|=1$. Géométriquement, $e^{-iay}$ est sur le cercle unité : le multiplier par $\widehat f(y)$ le fait tourner d'un angle $-ay$, sans l'agrandir ni le rétrécir. L'amplitude reste donc $r(y)$, tandis que la phase devient $\varphi(y)-ay$. Un décalage dans le domaine direct n'altère pas la quantité de chaque fréquence ; il décale seulement l'alignement de ses oscillations.

#### Changement d'échelle

Étirer un signal dans le temps le concentre en fréquence, et inversement.

  $$
  f(x/a) \quad\longleftrightarrow\quad |a|\widehat f(ay).
  $$

  **Démonstration pour $a>0$.** Posons $u=x/a$, donc $x=au$ et $dx=a\,du$. Lorsque $x$ parcourt $\mathbb{R}$, $u$ le parcourt dans le même sens :

  $$
  \begin{aligned}
  \mathcal{F}\bigl[f(\cdot/a)\bigr](y)
  &=\int_{\mathbb{R}}f(x/a)e^{-ixy}\,dx \\
  &=a\int_{\mathbb{R}}f(u)e^{-i(au)y}\,du \\
  &=a\widehat f(ay).
  \end{aligned}
  $$

  **Cas $a<0$.** La formule est presque la même, mais $x=au$ inverse le sens de parcours : quand $x$ va de $-\infty$ à $+\infty$, $u$ va de $+\infty$ à $-\infty$. Comme $dx=a\,du$ et $a<0$, on obtient

  $$
  \begin{aligned}
  \int_{-\infty}^{+\infty}f(x/a)e^{-ixy}\,dx
  &=\int_{+\infty}^{-\infty}f(u)e^{-i(au)y}\,a\,du \\
  &=-a\int_{-\infty}^{+\infty}f(u)e^{-iuy a}\,du \\
  &=|a|\widehat f(ay).
  \end{aligned}
  $$

  Le facteur $-1$ vient uniquement de l'inversion des bornes. Comme $a<0$, on a $-a=|a|$. La formule générale est donc bien $\mathcal{F}[f(\cdot/a)](y)=|a|\widehat f(ay)$.

  > [!example] Pourquoi « étirer » concentre le spectre
  > Si $a=2$, le signal devient $f(x/2)$ : il est deux fois plus étalé sur l'axe des $x$. Sa transformée vaut $2\widehat f(2y)$. Pour retrouver une même valeur de $\widehat f$, il faut prendre une fréquence $y$ deux fois plus petite : le spectre est donc comprimé vers $0$. Inversement, $f(2x)=f(x/(1/2))$ est comprimé dans le domaine direct et sa transformée vaut $\tfrac12\widehat f(y/2)$, qui est étalée en fréquence.

  **Exemple numérique.** Prenons $f(x)=e^{-x^2}$. Sa transformée est $\widehat f(y)=\sqrt{\pi}e^{-y^2/4}$. Après étirement par $a=2$, on a $h(x)=f(x/2)=e^{-x^2/4}$ et

  $$
  \widehat h(y)=2\widehat f(2y)=2\sqrt{\pi}e^{-y^2}.
  $$

  Les maxima sont atteints en $y=0$ : $\widehat f(0)=\sqrt{\pi}$ et $\widehat h(0)=2\sqrt{\pi}$. Le facteur $2$ provient du changement d'échelle de l'intégrale ; pour comparer uniquement la **largeur** des spectres, on divise donc chaque spectre par son maximum :

  $$
  \frac{\widehat f(y)}{\widehat f(0)}=e^{-y^2/4},
  \qquad
  \frac{\widehat h(y)}{\widehat h(0)}=e^{-y^2}.
  $$

  La largeur à mi-hauteur est l'écart entre les deux fréquences où le spectre normalisé vaut $1/2$. Pour $f$, on résout

  $$
  e^{-y^2/4}=\frac12
  \quad\Longleftrightarrow\quad
  -\frac{y^2}{4}=\ln\!\left(\frac12\right)=-\ln 2
  \quad\Longleftrightarrow\quad
  |y|=2\sqrt{\ln 2}\approx 1{,}67.
  $$

  Les deux points de demi-hauteur sont donc environ $-1{,}67$ et $+1{,}67$, soit une largeur totale d'environ $3{,}33$. Pour $h$, le même calcul donne

  $$
  e^{-y^2}=\frac12
  \quad\Longleftrightarrow\quad
  |y|=\sqrt{\ln 2}\approx 0{,}83.
  $$

  Ses points de demi-hauteur sont environ $-0{,}83$ et $+0{,}83$, soit une largeur totale d'environ $1{,}67$. Le spectre de $h$ est donc deux fois moins large : étirer le signal par $2$ a bien comprimé ses fréquences par $2$.

#### Dérivation

Dériver renforce les hautes fréquences, car une onde rapide varie fortement.

  $$
  f'(x) \quad\longleftrightarrow\quad iy\widehat f(y).
  $$

  **Démonstration.** Une intégration par parties donne, si $f(x)$ tend vers $0$ aux deux infinis :

  $$
  \begin{aligned}
  \widehat{f'}(y)
  &=\int_{\mathbb{R}}f'(x)e^{-ixy}\,dx \\
  &=\left[f(x)e^{-ixy}\right]_{-\infty}^{+\infty}
    -\int_{\mathbb{R}}f(x)(-iy)e^{-ixy}\,dx \\
  &=iy\int_{\mathbb{R}}f(x)e^{-ixy}\,dx \\
  &=iy\widehat f(y).
  \end{aligned}
  $$

  Le facteur $|y|$ explique directement l'amplification des hautes fréquences.

#### Multiplication par la position

Multiplier par la position correspond à dériver dans le domaine fréquentiel.

  $$
  -ixf(x) \quad\longleftrightarrow\quad \frac{d}{dy}\widehat f(y).
  $$

  **Démonstration.** En dérivant l'exponentielle par rapport à $y$, on fait apparaître $-ix$ :

  $$
  \begin{aligned}
  \frac{d}{dy}\widehat f(y)
  &=\frac{d}{dy}\int_{\mathbb{R}}f(x)e^{-ixy}\,dx \\
  &=\int_{\mathbb{R}}f(x)\frac{d}{dy}\bigl(e^{-ixy}\bigr)\,dx \\
  &=\int_{\mathbb{R}}\bigl(-ixf(x)\bigr)e^{-ixy}\,dx \\
  &=\mathcal{F}[-ixf](y).
  \end{aligned}
  $$

  Cette étape suppose que l'on peut dériver sous le signe intégral, ce qui est notamment vrai si $xf(x)$ est intégrable.

#### Symétries

Si $f$ est réelle, alors $\widehat f(-y)=\overline{\widehat f(y)}$. Si $f$ est réelle et paire, sa transformée est réelle et paire ; si $f$ est réelle et impaire, sa transformée est imaginaire et impaire.

  **Démonstration pour un signal réel.** On part de la définition $\widehat f(y)=\int_{\mathbb{R}}f(x)e^{-ixy}\,dx$ et on prend son conjugué complexe. La conjugaison peut passer sous l'intégrale, elle transforme un produit en produit des conjugués, et $\overline{e^{-ixy}}=e^{ixy}$ :

  $$
  \begin{aligned}
  \overline{\widehat f(y)}
  &=\overline{\int_{\mathbb{R}}f(x)e^{-ixy}\,dx} \\
  &=\int_{\mathbb{R}}\overline{f(x)}\,\overline{e^{-ixy}}\,dx \\
  &=\int_{\mathbb{R}}f(x)e^{ixy}\,dx \\
  &=\int_{\mathbb{R}}f(x)e^{-ix(-y)}\,dx \\
  &=\widehat f(-y).
  \end{aligned}
  $$

  La troisième ligne utilise le fait que $f$ est réelle, donc $\overline{f(x)}=f(x)$. La dernière ligne est exactement la définition de la transformée de Fourier, mais évaluée à la fréquence opposée $-y$.

  **Démonstration pour une fonction paire.** Si $f(-x)=f(x)$, un changement de variable $u=-x$ donne

  $$
  \begin{aligned}
  \widehat f(-y)
  &=\int_{\mathbb{R}}f(x)e^{ixy}\,dx \\
  &=\int_{\mathbb{R}}f(-u)e^{-iuy}\,du \\
  &=\widehat f(y).
  \end{aligned}
  $$

  La transformée est donc paire. Si $f$ est aussi réelle, on combine cette égalité avec $\widehat f(-y)=\overline{\widehat f(y)}$ :

  $$
  \widehat f(y)=\widehat f(-y)=\overline{\widehat f(y)}.
  $$

  Pour interpréter cette relation, écrivons $\widehat f(y)=a+ib$, avec $a,b\in\mathbb{R}$. Son conjugué vaut $\overline{\widehat f(y)}=a-ib$. L'égalité $a+ib=a-ib$ impose $b=0$ : le coefficient $\widehat f(y)$ est donc réel.

  **Démonstration pour une fonction impaire.** Si $f(-x)=-f(x)$, le même changement de variable donne $\widehat f(-y)=-\widehat f(y)$ : la transformée est impaire. Si $f$ est réelle, alors

  $$
  \overline{\widehat f(y)}=\widehat f(-y)=-\widehat f(y),
  $$

  Écrivons à nouveau $\widehat f(y)=a+ib$, avec $a,b\in\mathbb{R}$. La relation précédente devient

  $$
  a-ib=-a-ib.
  $$

  Elle impose $a=0$. Le coefficient est donc de la forme $ib$ : $\widehat f(y)$ est imaginaire pur.

> [!example] Pourquoi la dérivée fait ressortir le bruit
> Une petite oscillation de fréquence $y$ est multipliée par $iy$ après dérivation. Plus $|y|$ est grand, plus elle est amplifiée. C'est pourquoi une dérivée numérique peut amplifier le bruit.

### Exemples classiques

Dans chaque formule, le terme de gauche est le signal vu selon $x$ ; celui de droite décrit les fréquences $y$ qu'il contient. La valeur du spectre en $y=0$ est particulièrement parlante :

$$
\widehat f(0)=\int_{\mathbb{R}}f(x)\,dx.
$$

Quand le signal est positif, c'est son aire totale. Les paramètres $a$ ci-dessous sont positifs ; ils contrôlent la largeur ou la vitesse de décroissance du signal.

#### Porte : une coupure brutale produit des oscillations

La fonction indicatrice $\mathbf{1}_{[-a,a]}(x)$ vaut $1$ entre $-a$ et $a$, et $0$ ailleurs. C'est une « porte » de largeur $2a$. On pose $\operatorname{sinc}(u)=\sin(u)/u$, avec la valeur $1$ en $u=0$ par continuité.

$$
\mathbf{1}_{[-a,a]}(x)
\quad\longleftrightarrow\quad
2a\,\operatorname{sinc}(ay).
$$

Le calcul est direct : l'intégrale ne porte que sur l'intervalle où la porte vaut $1$.

$$
\begin{aligned}
\widehat f(y)
&=\int_{-a}^{a}e^{-ixy}\,dx \\
&=\frac{e^{-iay}-e^{iay}}{-iy} \\
&=\frac{2\sin(ay)}{y}
=2a\,\operatorname{sinc}(ay).
\end{aligned}
$$

Au centre, $\widehat f(0)=2a$, qui est bien l'aire de la porte. Pour comprendre les zéros, on regarde

$$
\widehat f(y)=\frac{2\sin(ay)}{y}.
$$

Hors de $y=0$, cette quantité est nulle exactement lorsque $\sin(ay)=0$. Or le sinus s'annule pour $ay=k\pi$, où $k$ est un entier non nul. Les zéros du spectre sont donc

$$
y=\frac{k\pi}{a},
\qquad
k=\pm1,\pm2,\ldots
$$

Entre deux zéros successifs, le sinus change de signe : le spectre oscille donc alternativement au-dessus et au-dessous de $0$. Son amplitude diminue lentement, comme $1/|y|$, car $|\sin(ay)|\leq1$ tandis que le dénominateur est $|y|$.

Le premier zéro positif est situé en $y=\pi/a$. Si $a=1$, il est à $\pi\approx3{,}14$. Si l'on divise la largeur de la porte par deux en prenant $a=1/2$, ce premier zéro se déplace à $2\pi\approx6{,}28$. Une porte deux fois plus étroite possède donc un lobe principal deux fois plus large dans son spectre.

L'idée générale est la suivante : les bords de la porte sont instantanés, donc très peu lisses. Pour reproduire un changement aussi brutal avec des ondes lisses, il faut additionner des oscillations de plus en plus rapides. C'est pourquoi le spectre reste présent loin de $y=0$ : une coupure nette dans le domaine direct exige beaucoup de hautes fréquences.

#### Exponentielle causale : un signal qui démarre à $0$

Le signal $e^{-ax}\mathbf{1}_{[0,+\infty)}(x)$ vaut $0$ avant $x=0$, puis décroît exponentiellement. Le mot « causal » signifie ici qu'il ne commence pas avant l'instant $0$.

$$
e^{-ax}\mathbf{1}_{[0,+\infty)}(x)
\quad\longleftrightarrow\quad
\frac{1}{a+iy}.
$$

Pour $a>0$, le terme $e^{-ax}$ assure que l'intégrale converge :

$$
\begin{aligned}
\widehat f(y)
&=\int_0^{+\infty}e^{-ax}e^{-ixy}\,dx \\
&=\int_0^{+\infty}e^{-(a+iy)x}\,dx \\
&=\frac{1}{a+iy}.
\end{aligned}
$$

Pour mieux lire ce résultat, on élimine le nombre complexe du dénominateur en multipliant par son conjugué $a-iy$ :

$$
\widehat f(y)
=\frac{1}{a+iy}
=\frac{a-iy}{(a+iy)(a-iy)}
=\frac{a}{a^2+y^2}-i\frac{y}{a^2+y^2}.
$$

La partie réelle vaut $a/(a^2+y^2)$ et la partie imaginaire vaut $-y/(a^2+y^2)$. Le module s'obtient avec $|p+iq|=\sqrt{p^2+q^2}$ :

$$
\begin{aligned}
|\widehat f(y)|
&=\sqrt{\left(\frac{a}{a^2+y^2}\right)^2
+\left(\frac{-y}{a^2+y^2}\right)^2} \\
&=\frac{1}{\sqrt{a^2+y^2}}.
\end{aligned}
$$

Ce module est maximal en $y=0$, où il vaut $1/a$, puis il diminue lorsque $|y|$ augmente. C'est le sens précis de « les basses fréquences sont les plus présentes ». Par exemple, si $a=1$, l'amplitude vaut $1$ à la fréquence $y=0$, environ $0{,}71$ pour $y=1$, puis environ $0{,}32$ pour $y=3$.

Pour comprendre d'où vient la partie imaginaire, séparons l'exponentielle complexe en cosinus et sinus :

$$
\widehat f(y)
=\int_{\mathbb{R}}f(x)\cos(xy)\,dx
-i\int_{\mathbb{R}}f(x)\sin(xy)\,dx.
$$

Supposons d'abord que $f$ soit paire, c'est-à-dire $f(-x)=f(x)$. Pour une fréquence $y$ fixée, la fonction sinus est impaire par rapport à $x$ :

$$
\sin((-x)y)=-\sin(xy).
$$

Le produit $g(x)=f(x)\sin(xy)$ vérifie donc

$$
g(-x)=f(-x)\sin((-x)y)=f(x)\bigl(-\sin(xy)\bigr)=-g(x).
$$

Il est impair. Pour voir précisément l'annulation, on découpe d'abord l'intégrale sur la droite en ses moitiés négative et positive :

$$
\int_{-\infty}^{+\infty}g(x)\,dx
=\int_{-\infty}^{0}g(x)\,dx
+\int_{0}^{+\infty}g(x)\,dx.
$$

Dans la première intégrale, on pose $u=-x$. Lorsque $x$ va de $-\infty$ à $0$, $u$ va de $+\infty$ à $0$, et $dx=-du$. On obtient

$$
\begin{aligned}
\int_{-\infty}^{0}g(x)\,dx
&=\int_{+\infty}^{0}g(-u)(-du) \\
&=\int_{0}^{+\infty}g(-u)\,du.
\end{aligned}
$$

En renommant $u$ en $x$, on peut alors réunir les deux moitiés :

$$
\int_{-\infty}^{+\infty}g(x)\,dx
=\int_0^{+\infty}\bigl(g(x)+g(-x)\bigr)\,dx
$$

Comme $g$ est impaire, $g(-x)=-g(x)$. À chaque $x>0$, le terme entre parenthèses vaut donc $g(x)-g(x)=0$, d'où

$$
\int_{-\infty}^{+\infty}g(x)\,dx=0.
$$

La partie imaginaire de la transformée, qui est $-\int f(x)\sin(xy)\,dx$, est donc nulle. Il ne reste que la partie cosinus, réelle : la transformée d'un signal réel et pair est réelle.

Pour le signal causal, on a au contraire

$$
f(x)=
\begin{cases}
e^{-ax} & \text{si } x\geq0,\\
0 & \text{si } x<0.
\end{cases}
$$

Cette écriture décrit deux comportements différents. Avant l'instant $0$, le signal est exactement nul : il n'existe pas encore. À l'instant $0$, il vaut $f(0)=1$. Après $0$, il décroît selon $e^{-ax}$. Le paramètre $a>0$ contrôle la vitesse de décroissance : plus $a$ est grand, plus le signal s'éteint vite. En particulier,

$$
f\!\left(\frac{1}{a}\right)=e^{-1}\approx0{,}37.
$$

Le temps $1/a$ est donc une échelle naturelle : au bout de ce temps, le signal a déjà perdu environ $63\%$ de sa valeur initiale.

Cette fonction n'est pas paire. Pour tout $x>0$,

$$
f(x)=e^{-ax}
\qquad\text{mais}\qquad
f(-x)=0.
$$

Il n'y a pas de copie de la partie droite du signal à gauche de $0$. Pour $x>0$, le terme $f(x)\sin(xy)=e^{-ax}\sin(xy)$ est en général non nul, tandis qu'au point opposé $-x$, on a $f(-x)=0$ : il n'existe donc aucune contribution opposée pour l'annuler.

Pour calculer cette contribution, on utilise $e^{ixy}=\cos(xy)+i\sin(xy)$. Alors

$$
\int_0^{+\infty}e^{-ax}e^{ixy}\,dx
=\int_0^{+\infty}e^{-ax}\cos(xy)\,dx
+i\int_0^{+\infty}e^{-ax}\sin(xy)\,dx.
$$

Mais le membre de gauche est une intégrale exponentielle :

$$
\begin{aligned}
\int_0^{+\infty}e^{-ax}e^{ixy}\,dx
&=\int_0^{+\infty}e^{-(a-iy)x}\,dx \\
&=\frac{1}{a-iy} \\
&=\frac{a+iy}{a^2+y^2}.
\end{aligned}
$$

La partie imaginaire de cette dernière expression est $y/(a^2+y^2)$. On en déduit

$$
\int_0^{+\infty}e^{-ax}\sin(xy)\,dx
=\frac{y}{a^2+y^2}.
$$

Dans la transformée de Fourier, l'exponentielle est $e^{-ixy}=\cos(xy)-i\sin(xy)$. La partie imaginaire porte donc un signe moins :

$$
-\int_0^{+\infty}e^{-ax}\sin(xy)\,dx
=-\frac{y}{a^2+y^2}.
$$

Par exemple, pour $a=1$ et $y=1$, cette partie imaginaire vaut $-1/2$. Elle ne s'annule que si $y=0$ : pour toute fréquence non nulle, l'absence de symétrie gauche-droite laisse une trace dans la phase du spectre.

On retrouve ainsi le résultat déjà calculé :

$$
\widehat f(y)=\frac{a}{a^2+y^2}-i\frac{y}{a^2+y^2},
$$

dont la partie imaginaire vaut $-y/(a^2+y^2)$. Comme $a>0$, le dénominateur ne s'annule jamais ; cette partie imaginaire est donc nulle uniquement pour $y=0$.

Tout nombre complexe non nul peut s'écrire sous forme polaire. On écrit donc

$$
\widehat f(y)=|\widehat f(y)|e^{i\varphi(y)},
\qquad
\varphi(y)=-\arctan\!\left(\frac{y}{a}\right).
$$

Cette expression de la phase vient du fait que $a+iy$ a pour angle $\arctan(y/a)$ dans le plan complexe. Prendre son inverse, $1/(a+iy)$, change le signe de cet angle. Ainsi, pour $y>0$, la phase est négative ; pour $y<0$, elle est positive ; et pour $y=0$, elle vaut $0$.

Le module décrit combien de la fréquence $y$ est présente ; la phase $\varphi(y)$ indique le décalage de son oscillation par rapport à une référence. Par exemple, avec $a=1$ et $y=1$,

$$
\widehat f(1)=\frac{1-i}{2}
=\frac{1}{\sqrt2}e^{-i\pi/4}.
$$

La fréquence $1$ a donc une amplitude $1/\sqrt2$ et une phase $-\pi/4$. À la fréquence opposée,

$$
\widehat f(-1)=\frac{1+i}{2}
=\frac{1}{\sqrt2}e^{i\pi/4}.
$$

Plus généralement, tout signal réel vérifie $\widehat f(-y)=\overline{\widehat f(y)}$. Si $\widehat f(y)=r(y)e^{i\varphi(y)}$, alors

$$
\widehat f(-y)=\overline{\widehat f(y)}=r(y)e^{-i\varphi(y)}.
$$

Les fréquences $+y$ et $-y$ ont donc toujours la même amplitude $r(y)$, mais des phases opposées. C'est le mécanisme qui permet à leur somme de former un signal réel.

#### Exponentielle bilatérale : une version symétrique

Le signal $e^{-a|x|}$ est une pointe centrée en $0$ qui décroît de la même façon à gauche et à droite. Il est réel et pair ; sa transformée doit donc être réelle et paire.

$$
e^{-a|x|}
\quad\longleftrightarrow\quad
\frac{2a}{a^2+y^2}.
$$

La symétrie permet de ne calculer que le côté $x\geq0$ :

$$
\begin{aligned}
\widehat f(y)
&=2\int_0^{+\infty}e^{-ax}\cos(xy)\,dx \\
&=\frac{2a}{a^2+y^2}.
\end{aligned}
$$

Le spectre est maximal en $y=0$, avec la valeur $2/a$, puis décroît comme $1/y^2$ loin de l'origine. Cette décroissance est plus lente que celle d'une gaussienne : la pointe $e^{-a|x|}$ possède un angle en $x=0$, donc elle est moins lisse et contient davantage de hautes fréquences.

#### Gaussienne : l'exemple le plus équilibré

$$
e^{-ax^2}
\quad\longleftrightarrow\quad
\sqrt{\frac{\pi}{a}}e^{-y^2/(4a)}.
$$

Une gaussienne reste une gaussienne après transformation. Si $a$ augmente, $e^{-ax^2}$ devient plus étroite autour de $0$. En contrepartie, le terme $e^{-y^2/(4a)}$ décroît moins vite en $y$ : le spectre devient plus large. Une fonction ne peut donc pas être très concentrée à la fois en position et en fréquence.

La gaussienne évite les cassures et les coupures nettes : son spectre décroît lui aussi de façon très régulière et très rapide. C'est pourquoi elle est centrale en filtrage et en traitement du signal.

#### Sinusoïdes pures : une fréquence idéale

Les sinusoïdes pures ne sont pas intégrables sur toute la droite : elles oscillent pour toujours et leur énergie totale est infinie. On les décrit alors avec des distributions. La distribution de Dirac $\delta(y-a)$ n'est pas une fonction ordinaire : elle représente un pic idéalement concentré en $y=a$.

$$
e^{iax}
\quad\longleftrightarrow\quad
2\pi\delta(y-a).
$$

Un cosinus contient deux exponentielles complexes, l'une de fréquence $+a$ et l'autre de fréquence $-a$ :

$$
\cos(ax)
\quad\longleftrightarrow\quad
\pi\delta(y-a)+\pi\delta(y+a).
$$

Cette formule formalise l'intuition la plus simple : un cosinus pur ne contient qu'une seule fréquence positive et son opposée négative, sans aucune autre composante fréquentielle.

---

## 3. Séries de Fourier : le cas périodique

La transformée de Fourier précédente décrit un signal qui vit sur toute la droite. Mais de nombreux phénomènes recommencent sans cesse : une corde qui vibre, une roue qui tourne, un battement régulier. Pour un tel signal, il suffit de connaître ce qui se passe pendant une période. Ici, on choisit une période de longueur $2\pi$.

On note alors $\mathbb{T}=\mathbb{R}/2\pi\mathbb{Z}$ : deux angles qui diffèrent d'un multiple de $2\pi$ représentent le même point du cycle. Une fonction $f$ sur $\mathbb{T}$ est donc simplement une fonction $2\pi$-périodique sur $\mathbb{R}$.

> [!example] Lire le quotient comme un cercle
> Dans $\mathbb{T}$, les réels $0$, $2\pi$, $-2\pi$ et $4\pi$ désignent tous le **même point**, car leurs différences sont des multiples de $2\pi$. De même, $\pi/3$ et $7\pi/3=\pi/3+2\pi$ sont le même angle. On peut imaginer que l'on enroule la droite réelle autour d'un cercle : après un tour complet, on revient exactement au point de départ. Ainsi, au lieu de considérer tous les réels séparément, $\mathbb{T}$ ne retient que leur position dans un tour, par exemple dans l'intervalle $[0,2\pi[$.

### Les seules oscillations qui referment le cycle

L'onde $e^{iy\theta}$ revient à sa valeur de départ après une période exactement si

$$
e^{iy(\theta+2\pi)}=e^{iy\theta}.
$$

Après simplification, cette condition devient $e^{i2\pi y}=1$. Elle est vraie précisément lorsque $y$ est un entier. C'est pourquoi, dans le monde périodique, les fréquences possibles sont

$$
\ldots,-2,-1,0,1,2,\ldots
$$

et les ondes de base sont $e^{in\theta}$ avec $n\in\mathbb{Z}$. La fréquence $0$ correspond à une constante ; $n=1$ réalise un tour pendant une période ; $n=3$ en réalise trois ; $n=-3$ correspond au même rythme, mais avec l'orientation complexe opposée.

### Décomposer le signal : sa recette en fréquences

La série de Fourier affirme qu'un signal périodique peut être décrit par la somme de toutes ces oscillations élémentaires :

$$
f(\theta)=\sum_{n\in\mathbb{Z}}\widehat f(n)e^{in\theta}.
$$

Le nombre $\widehat f(n)$ est le poids de la fréquence $n$. L'égalité se lit donc ainsi : « pour reconstruire $f$, on additionne une onde de chaque fréquence, multipliée par la quantité correspondante ».

### Comment isoler une seule fréquence

La question est maintenant : comment retrouver $\widehat f(n)$ à partir de $f$ ? On multiplie le signal par l'onde opposée $e^{-in\theta}$, puis on fait la moyenne sur une période :

$$
\widehat f(n)=\frac{1}{2\pi}\int_0^{2\pi}f(\theta)e^{-in\theta}\,d\theta.
$$

Voici comment lire chaque terme, en fixant un entier $n$ :

- $\theta\in[0,2\pi]$ est la position dans un cycle (un angle). C'est la variable que l'on fait parcourir pendant l'intégrale.
- $f(\theta)$ est la valeur du signal à cette position.
- $n\in\mathbb{Z}$ est la fréquence que l'on cherche à mesurer : $n=0$ donne la composante constante (la moyenne), $n=1$ correspond à une oscillation par tour, $n=2$ à deux oscillations par tour, etc. Les entiers négatifs correspondent aux ondes complexes qui tournent dans l'autre sens.
- $e^{-in\theta}$ est l'onde de fréquence opposée. La multiplier par $f(\theta)$ « annule » l'oscillation de fréquence $n$ présente dans $f$ ; cette partie devient constante, alors que les autres continuent d'osciller.
- $\int_0^{2\pi}\cdots\,d\theta$ additionne ce produit sur un tour complet. Les oscillations restantes s'y compensent.
- Le facteur $1/(2\pi)$ transforme cette somme intégrale en **moyenne** sur un tour. Il garantit notamment que, si $f(\theta)=e^{in\theta}$, alors $\widehat f(n)=1$.

Ainsi, $\widehat f(n)$ est le coefficient — en général complexe — qui mesure la part de la fréquence $n$ dans le signal.

Pourquoi cela marche-t-il ? Supposons que le signal contienne un terme $c_ke^{ik\theta}$. Après multiplication par $e^{-in\theta}$, il devient

$$
c_ke^{i(k-n)\theta}.
$$

Ici, $c_k$ est le **coefficient de Fourier de fréquence $k$** : il indique combien d'onde $e^{ik\theta}$ est présente dans le signal. C'est un nombre complexe. Son module $|c_k|$ donne l'amplitude de cette composante, et son argument donne son décalage de phase. Par exemple, si $f(\theta)=3e^{i2\theta}$, alors $c_2=3$ ; si $f(\theta)=2i\,e^{i2\theta}$, alors $c_2=2i$, de module $2$ et avec une phase de $\pi/2$.

Si $k=n$, ce terme devient simplement $c_n$, une constante : sa moyenne est elle-même. Si $k\neq n$, l'onde continue d'osciller pendant la période et ses contributions positives et négatives se compensent exactement. Formellement,

$$
\frac{1}{2\pi}\int_0^{2\pi}e^{i(k-n)\theta}\,d\theta
=
\begin{cases}
1 & \text{si } k=n,\\
0 & \text{si } k\neq n.
\end{cases}
$$

Cette règle est l'orthogonalité des ondes périodiques. C'est l'analogue continu de la manière dont on extrait la coordonnée d'un vecteur en faisant un produit scalaire avec un vecteur de base.

### Exemple : lire un signal composé

Considérons

$$
f(\theta)=\cos(3\theta)+2\sin(\theta).
$$

On utilise les identités

$$
\cos(3\theta)=\frac12e^{i3\theta}+\frac12e^{-i3\theta},
$$

et

$$
2\sin(\theta)=-ie^{i\theta}+ie^{-i\theta}.
$$

Les seuls coefficients non nuls sont donc

$$
\widehat f(3)=\widehat f(-3)=\frac12,
\qquad
\widehat f(1)=-i,
\qquad
\widehat f(-1)=i.
$$

Tous les autres coefficients sont nuls : le signal ne contient que les rythmes $1$ et $3$. Les fréquences $+n$ et $-n$ apparaissent par paires pour les signaux réels ; leurs coefficients sont conjugués l'un de l'autre.

### Parseval : l'énergie ne dépend pas du point de vue

La version périodique de Plancherel, appelée identité de Parseval, est

$$
\int_0^{2\pi}|f(\theta)|^2\,d\theta
=2\pi\sum_{n\in\mathbb{Z}}|\widehat f(n)|^2.
$$

À gauche, on mesure l'énergie du signal au fil d'une période. À droite, on additionne l'énergie portée par chaque fréquence. C'est la même idée que pour Plancherel sur $\mathbb{R}$, mais ici les fréquences sont une liste d'entiers : une intégrale devient une somme.

Cette identité permet notamment de calculer des sommes de séries en choisissant astucieusement une fonction périodique.

---

## 4. Transformée de Fourier discrète (TFD)

La TFD est la version finie de la série de Fourier. On part de $N$ valeurs $v_0,\ldots,v_{N-1}$, vues comme un signal périodique discret : les indices sont pris modulo $N$.

Les ondes discrètes

$$
e_n(k)=e^{2\pi ikn/N},\qquad k,n\in\{0,\ldots,N-1\},
$$

forment une base orthogonale de $\mathbb{C}^N$. Les coefficients de Fourier discrets sont donc les coordonnées du vecteur dans cette base :

$$
\widehat v_n=\frac{1}{N}\sum_{k=0}^{N-1}v_k e^{-2\pi ikn/N}.
$$

La reconstruction est

$$
v_k=\sum_{n=0}^{N-1}\widehat v_n e^{2\pi ikn/N}.
$$

> [!example] Une oscillation discrète pure
> Avec $N=8$ et $v_k=\cos(2\pi\cdot 2k/8)$, le spectre n'a que deux pics : $\widehat v_2=\widehat v_6=1/2$. L'indice $6$ représente la fréquence $-2$ modulo $8$.

> [!warning] Conventions logicielles
> Le signe dans l'exponentielle et le facteur $1/N$ peuvent être placés différemment selon les bibliothèques. Toujours vérifier la convention avant de comparer une formule théorique à une FFT de logiciel.

---

## 5. Échantillonnage : relier continu, périodique et discret

Supposons qu'un signal périodique soit une somme finie de fréquences :

$$
f(\theta)=\sum_{n=0}^{N-1}a_n e^{in\theta}.
$$

Si on l'évalue aux $N$ points régulièrement espacés $\theta_k=2\pi k/N$, alors les valeurs $v_k=f(\theta_k)$ contiennent exactement l'information nécessaire pour retrouver les coefficients $a_n$ : ils sont la TFD du vecteur $v$.

L'échantillonnage est donc le pont entre une description par fonction et une description par tableau de nombres. La FFT est simplement un algorithme rapide pour calculer cette TFD.

---

## 6. Aliasing : quand les fréquences se confondent

Avec seulement $M$ points d'échantillonnage, deux fréquences qui diffèrent d'un multiple de $M$ donnent les mêmes valeurs aux points échantillonnés :

$$
e^{i(n+qM)\theta_k}=e^{in\theta_k},
\qquad \theta_k=\frac{2\pi k}{M}.
$$

L'échantillonneur ne peut donc pas les distinguer. C'est l'**aliasing** : une fréquence trop élevée apparaît comme une fréquence plus basse.

> [!example] Même suite d'échantillons, fréquences différentes
> Si $M=8$, alors $\cos(2\pi\cdot 9k/8)=\cos(2\pi k/8)$ pour tout entier $k$. Sur les huit échantillons, la fréquence $9$ est indiscernable de la fréquence $1$.

Pour représenter naturellement un signal réel, on centre généralement les fréquences autour de zéro. Si $N=2P+1$, on utilise les fréquences $-P,\ldots,P$ plutôt que $0,\ldots,N-1$. Les fréquences positives et négatives viennent alors par paires conjuguées.

### Trois régimes à distinguer

- Avec le nombre de points adapté au nombre de coefficients, on retrouve exactement le signal dans le modèle considéré.
- Avec davantage de points, le **zero-padding** ajoute des coefficients nuls avant de reconstruire sur une grille plus fine. Cela donne une interpolation plus dense du même signal ; cela ne crée pas de nouvelle information.
- Avec trop peu de points, les coefficients dont les indices sont égaux modulo $M$ se replient les uns sur les autres et s'additionnent : c'est le sous-échantillonnage et l'aliasing.

> [!tip] Réflexe pratique
> Avant d'échantillonner, retirer les fréquences supérieures à la moitié de la fréquence d'échantillonnage avec un filtre passe-bas. C'est l'idée opérationnelle derrière Nyquist-Shannon.

---

## 7. Transformée de Fourier en plusieurs dimensions

Pour $f : \mathbb{R}^d\to\mathbb{C}$, la formule est la même, en remplaçant le produit par le produit scalaire :

$$
\widehat f(y)=\int_{\mathbb{R}^d}f(x)e^{-i x\cdot y}\,dx,
$$

et

$$
f(x)=\frac{1}{(2\pi)^d}\int_{\mathbb{R}^d}\widehat f(y)e^{i x\cdot y}\,dy.
$$

En pratique, c'est essentiel pour les images : $x=(x_1,x_2)$ repère un pixel et $y=(y_1,y_2)$ une fréquence selon les deux directions. La séparation des variables permet de calculer une TFD 2D en appliquant successivement une TFD sur les lignes puis sur les colonnes.

> [!example] Image et fréquences 2D
> Une image uniforme n'a qu'une composante de fréquence nulle. Des rayures verticales donnent des fréquences horizontales marquées. Un flou gaussien atténue les hautes fréquences dans toutes les directions.

---

## À retenir

- Fourier décompose un signal en ondes élémentaires ; le spectre indique leurs coefficients.
- La convolution devient un produit en fréquence : c'est la clé du filtrage rapide.
- Dériver accentue les hautes fréquences ; lisser les atténue.
- La série de Fourier traite les signaux périodiques, la TFD leurs versions échantillonnées.
- Des fréquences différentes peuvent donner les mêmes échantillons : il faut échantillonner assez vite pour éviter l'aliasing.
- La même théorie fonctionne en dimension $d$, notamment pour les images.

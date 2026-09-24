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

La linéarité est immédiate :

$$
\widehat{\lambda f+\mu g}=\lambda\widehat f+\mu\widehat g.
$$

Les autres règles se retiennent mieux par leur sens.

- **Décalage temporel ou spatial** : décaler $f$ ne change pas les amplitudes de ses fréquences ; cela change seulement leur phase.

  $$
  f(x-a) \quad\longleftrightarrow\quad e^{-iay}\widehat f(y).
  $$

- **Changement d'échelle** : étirer un signal dans le temps le concentre en fréquence, et inversement.

  $$
  f(x/a) \quad\longleftrightarrow\quad |a|\widehat f(ay).
  $$

- **Dérivation** : dériver renforce les hautes fréquences, car une onde rapide varie fortement.

  $$
  f'(x) \quad\longleftrightarrow\quad iy\widehat f(y).
  $$

- **Multiplication par la position** : elle correspond à dériver dans le domaine fréquentiel.

  $$
  -ixf(x) \quad\longleftrightarrow\quad \frac{d}{dy}\widehat f(y).
  $$

- **Symétries** : si $f$ est réelle, alors $\widehat f(-y)=\overline{\widehat f(y)}$. Si $f$ est réelle et paire, sa transformée est réelle et paire ; si $f$ est réelle et impaire, sa transformée est imaginaire et impaire.

> [!example] Pourquoi la dérivée fait ressortir le bruit
> Une petite oscillation de fréquence $y$ est multipliée par $iy$ après dérivation. Plus $|y|$ est grand, plus elle est amplifiée. C'est pourquoi une dérivée numérique peut amplifier le bruit.

### Exemples classiques

On pose $\operatorname{sinc}(u)=\sin(u)/u$, avec la valeur $1$ en $u=0$ par continuité.

- Une porte de largeur $2a$ :

  $$
  \mathbf{1}_{[-a,a]}(x)
  \quad\longleftrightarrow\quad
  2a\,\operatorname{sinc}(ay).
  $$

  Couper brutalement un signal dans le domaine direct produit donc une réponse étalée et oscillante en fréquence.

- Une exponentielle causale, pour $a>0$ :

  $$
  e^{-ax}\mathbf{1}_{[0,+\infty)}(x)
  \quad\longleftrightarrow\quad
  \frac{1}{a+iy}.
  $$

- Une exponentielle bilatérale, pour $a>0$ :

  $$
  e^{-a|x|}
  \quad\longleftrightarrow\quad
  \frac{2a}{a^2+y^2}.
  $$

- Une gaussienne, pour $a>0$ :

  $$
  e^{-ax^2}
  \quad\longleftrightarrow\quad
  \sqrt{\frac{\pi}{a}}e^{-y^2/(4a)}.
  $$

  Une gaussienne reste une gaussienne après transformation : elle est très bien localisée à la fois dans le domaine direct et en fréquence, ce qui explique son rôle central en filtrage.

Les sinusoïdes pures ne sont pas intégrables sur toute la droite. On les traite alors au sens des distributions : leur spectre est concentré sur une ou deux fréquences précises. C'est exactement l'intuition attendue : un cosinus pur ne contient qu'une seule fréquence positive et son opposée négative.

---

## 3. Séries de Fourier : le cas périodique

On considère maintenant une fonction $2\pi$-périodique, c'est-à-dire définie sur le cercle $\mathbb{T}=\mathbb{R}/2\pi\mathbb{Z}$. À cause de la périodicité, seules les fréquences entières peuvent « boucler » correctement : $e^{in\theta}$ avec $n\in\mathbb{Z}$.

$$
f(\theta)=\sum_{n\in\mathbb{Z}}\widehat f(n)e^{in\theta}.
$$

Les coefficients se calculent en projetant $f$ sur chaque onde :

$$
\widehat f(n)=\frac{1}{2\pi}\int_0^{2\pi}f(\theta)e^{-in\theta}\,d\theta.
$$

> [!example] Lire les coefficients d'une combinaison de sinusoïdes
> Pour $f(\theta)=\cos(3\theta)+2\sin(\theta)$, les seuls coefficients non nuls sont
> $\widehat f(3)=\widehat f(-3)=1/2$, $\widehat f(1)=-i$ et $\widehat f(-1)=i$.
> Les fréquences $\pm 3$ correspondent au cosinus ; les fréquences $\pm 1$ correspondent au sinus.

La version périodique de Plancherel, souvent appelée identité de Parseval, est

$$
\int_0^{2\pi}|f(\theta)|^2\,d\theta
=2\pi\sum_{n\in\mathbb{Z}}|\widehat f(n)|^2.
$$

Elle permet entre autres de calculer des sommes de séries en choisissant astucieusement une fonction périodique.

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

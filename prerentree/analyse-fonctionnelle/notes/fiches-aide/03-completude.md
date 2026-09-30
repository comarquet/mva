---
title: "Complétude"
tags:
  - mva
  - pre-rentree
  - analyse-fonctionnelle
  - completude
  - convergence
---

# Complétude

[[00-socle-analyse-fonctionnelle|← Retour au socle]] · [[poly-analyse-fonctionnelle|Poly principal]]

Dire qu'un espace est **complet** ne signifie pas que toute suite y converge. Par exemple, la suite $f_n(x)=n$ ne converge dans aucun espace $L^p$. La complétude parle seulement des suites dont les termes finissent par devenir arbitrairement proches les uns des autres.

Une telle suite est appelée **suite de Cauchy**. Pour des fonctions, cela signifie que, dès que $n$ et $m$ sont assez grands, la norme de la différence $\|f_n-f_m\|_{L^p}$ est aussi petite que l'on veut :

$$
\forall\varepsilon>0,\ \exists N,\ \forall n,m\ge N,
\qquad
\|f_n-f_m\|_{L^p}<\varepsilon.
$$

L'idée est simple : on ne connaît pas nécessairement encore la limite, mais les approximations semblent se stabiliser. La question est alors : **l'objet vers lequel elles tendent appartient-il toujours à l'espace où l'on travaille ?**

> [!example] Pourquoi $\mathbb Q$ n'est pas complet
> On peut approcher $\sqrt2$ par les rationnels :

$$
1,\quad 1.4,\quad 1.41,\quad 1.414,\quad \ldots
$$

Les termes deviennent de plus en plus proches entre eux : c'est une suite de Cauchy dans $\mathbb Q$. Pourtant, sa limite est $\sqrt2$, qui n'est pas rationnel. La suite cherche une limite qui n'existe pas dans l'espace où elle vit.

L'ensemble $\mathbb R$, lui, contient cette limite. On dit donc que $\mathbb R$ est complet, alors que $\mathbb Q$ ne l'est pas.

Les espaces $L^p$ jouent le rôle de $\mathbb R$ pour les fonctions : toute suite de Cauchy pour la norme $L^p$ possède une limite qui est encore une fonction de $L^p$.

> [!example] Pourquoi choisir le bon espace est important
> Construisons réellement cette approximation. La fonction que l'on cherche à approcher est le saut de Heaviside
>
> $$
> H(x)=
> \begin{cases}
> 0 & \text{si }x\le 1/2,\\
> 1 & \text{si }x>1/2.
> \end{cases}
> $$
>
> Elle vaut $0$ à gauche de $1/2$ et $1$ à droite. Le problème est son saut brutal au milieu : $H$ n'est pas continue.
>
> Pour chaque $n\ge2$, remplaçons ce saut par une petite rampe de largeur $1/n$ :
>
> $$
> g_n(x)=
> \begin{cases}
> 0 & \text{si }x\le 1/2,\\
> n(x-1/2) & \text{si }1/2\le x\le 1/2+1/n,\\
> 1 & \text{si }x\ge 1/2+1/n.
> \end{cases}
> $$
>
> La fonction $g_n$ est continue : elle part de $0$, monte régulièrement jusqu'à $1$, puis reste égale à $1$. Lorsque $n$ augmente, la montée se fait sur une zone de plus en plus étroite. En dehors de l'intervalle $[1/2,1/2+1/n]$, les fonctions $g_n$ et $H$ sont exactement égales.
>
> Regardons cette erreur en détail. La norme $L^1$ ne regarde pas la plus grande différence entre deux fonctions : elle additionne l'erreur sur tout l'intervalle,
>
> $$
> \|g_n-H\|_{L^1}
> =\int_0^1 |g_n(x)-H(x)|\,dx.
> $$
>
> À gauche de $1/2$, les deux fonctions valent $0$. À droite de $1/2+1/n$, elles valent toutes les deux $1$. Ces deux grandes zones ne contribuent donc pas du tout à l'intégrale. Il reste seulement l'intervalle $[1/2,1/2+1/n]$.
>
> Dans cet intervalle, $H(x)=1$, tandis que $g_n(x)=n(x-1/2)$ monte de $0$ à $1$. L'écart vaut donc
>
> $$
> |g_n(x)-H(x)|
> =1-n(x-1/2).
> $$
>
> Au début de la rampe, cet écart vaut $1$ ; à la fin, il vaut $0$. Son graphe est donc un triangle de hauteur $1$ et de base $1/n$. L'intégrale est l'aire de ce triangle :
>
> $$
> \|g_n-H\|_{L^1}
> =\frac12\times\frac1n\times1
> =\frac1{2n}.
> $$
>
> On peut aussi retrouver ce résultat directement par le calcul :
>
> $$
> \int_{1/2}^{1/2+1/n}
> \bigl(1-n(x-1/2)\bigr)\,dx
> =\frac1{2n}.
> $$
>
> Comme $1/(2n)$ tend vers $0$, l'erreur totale entre $g_n$ et $H$ devient aussi petite que souhaité. C'est exactement la définition de la convergence de $g_n$ vers $H$ dans $L^1$.
>
> Pour comparer $g_n$ à $g_m$, on pourrait calculer directement leur différence. Mais leurs rampes n'ont pas la même largeur, ce qui imposerait de distinguer plusieurs intervalles. Il est plus simple de les comparer toutes les deux au même objet $H$.
>
> L'idée vient de l'inégalité triangulaire ordinaire : pour trois nombres ou trois points $a$, $b$ et $c$, la distance directe de $a$ à $b$ est au plus la distance de $a$ à $c$, plus celle de $c$ à $b$. Un détour peut être plus long, jamais plus court. Ici, $H$ joue le rôle du point intermédiaire.
>
> Pour chaque point $x$, on écrit
>
> $$
> g_n(x)-g_m(x)
> =\bigl(g_n(x)-H(x)\bigr)+\bigl(H(x)-g_m(x)\bigr).
> $$
>
> Oui : $g_n$, $g_m$ et $H$ sont des **fonctions**, mais dès que l'on fixe un point $x$, leurs valeurs $g_n(x)$, $g_m(x)$ et $H(x)$ sont de simples nombres réels. L'égalité ci-dessus est donc l'égalité algébrique très ordinaire
>
> $$
> a-b=(a-c)+(c-b),
> $$
>
> appliquée avec $a=g_n(x)$, $b=g_m(x)$ et $c=H(x)$. On a seulement ajouté puis retranché le même nombre $H(x)$ :
>
> $$
> \bigl(g_n(x)-H(x)\bigr)+\bigl(H(x)-g_m(x)\bigr)
> =g_n(x)-H(x)+H(x)-g_m(x)
> =g_n(x)-g_m(x).
> $$
>
> L'inégalité triangulaire est bien, à ce stade, une inégalité pour les nombres réels : $|u+v|\le |u|+|v|$. En prenant
>
> $$
> u=g_n(x)-H(x),
> \qquad
> v=H(x)-g_m(x),
> $$
>
> elle donne, pour ce point précis $x$,
>
> $$
> |g_n(x)-g_m(x)|
> \le |g_n(x)-H(x)|+|H(x)-g_m(x)|.
> $$
>
> On fait ensuite cela pour **tous** les points $x$ de l'intervalle, puis on additionne les erreurs : c'est exactement l'intégrale. Ainsi l'inégalité pour les nombres devient une inégalité entre les longueurs des fonctions :
>
> $$
> \|g_n-g_m\|_{L^1}
> \le \|g_n-H\|_{L^1}+\|H-g_m\|_{L^1}.
> $$
>
> C'est pourquoi on parle de « distance » entre fonctions :
>
> $$
> d_{L^1}(f,g)=\|f-g\|_{L^1}.
> $$
>
> Les fonctions sont les points de cet espace, la différence $f-g$ est le vecteur qui les relie, et la norme $L^1$ donne la longueur de ce vecteur. L'inégalité triangulaire dit alors exactement la même chose qu'en géométrie : aller directement de $f$ à $g$ ne peut pas coûter plus que passer par une fonction intermédiaire.
>
> Les deux termes de droite sont déjà connus :
>
> $$
> \|g_n-H\|_{L^1}=\frac1{2n},
> \qquad
> \|H-g_m\|_{L^1}=\frac1{2m}.
> $$
>
> D'où
>
> $$
> \|g_n-g_m\|_{L^1}
> \le \frac1{2n}+\frac1{2m}.
> $$
>
> Le point important est que $H$ n'a pas besoin d'être continue pour servir d'intermédiaire : elle appartient à $L^1$, donc toutes les distances utilisées ci-dessus ont bien un sens.
>
> Voyons maintenant précisément le rôle de $N$. On se donne une erreur maximale $\varepsilon>0$ que l'on accepte. On choisit un entier $N$ tel que
>
> $$
> \frac1N<\varepsilon.
> $$
>
> Alors, pour tous $n,m\ge N$, on a
>
> $$
> \frac1{2n}+\frac1{2m}
> \le\frac1{2N}+\frac1{2N}
> =\frac1N
> <\varepsilon.
> $$
>
> Par conséquent,
>
> $$
> \|g_n-g_m\|_{L^1}<\varepsilon
> \qquad\text{dès que }n,m\ge N.
> $$
>
> C'est exactement la définition d'une suite de Cauchy : quel que soit le niveau de précision demandé, on peut aller assez loin dans la suite pour que **toutes** les fonctions restantes soient deux à deux aussi proches que souhaité.
>
> Voici l'image essentielle. Chaque $g_n$ est une **rampe** : elle passe continûment de $0$ à $1$, mais la largeur de la montée est $1/n$. Pour $n=10$, la montée occupe une largeur $0{,}1$ ; pour $n=1\,000$, elle n'occupe plus qu'une largeur $0{,}001$. Les rampes sont toujours continues, mais la zone où elles ne ressemblent pas au saut devient si fine que son aire tend vers $0$.
>
> La distance $L^1$ ne demande pas si le graphe est abrupt. Elle ne regarde que l'**aire totale** entre les deux graphes. Pour elle, une erreur de hauteur $1$ sur une largeur qui tend vers $0$ devient négligeable. Elle voit donc les rampes se rapprocher du mur vertical $H$.
>
> Ce mur vertical n'est pas continu : à gauche de $1/2$, $H$ vaut $0$ ; immédiatement à droite, elle vaut $1$. Changer la valeur de $H$ seulement au point $1/2$ ne résout rien : le problème ne vient pas du point lui-même, mais du passage instantané de $0$ à $1$.
>
> Pourquoi ne pourrait-on pas avoir une autre limite $q$ qui, elle, serait continue ? Parce qu'une fonction continue qui va de $0$ à $1$ doit forcément faire sa transition sur une **vraie petite zone de largeur positive**. Elle prend notamment des valeurs proches de $1/2$ sur un petit intervalle. Mais dans cet intervalle, le saut $H$ vaut soit $0$, soit $1$ ; $q$ et $H$ y diffèrent donc d'une quantité non négligeable. Cette différence crée une aire positive, donc une distance $L^1$ non nulle.
>
> Autrement dit, une fonction continue ne peut pas coïncider avec $H$ presque partout : pour rester continue, elle doit étaler son changement de valeur ; $H$, elle, ne l'étale sur aucune largeur.
>
> On peut résumer l'argument ainsi :
>
> 1. Les fonctions $g_n$ sont continues.
> 2. Leur transition s'écrase sur une largeur qui tend vers $0$.
> 3. En distance $L^1$, elles convergent donc vers le saut $H$.
> 4. Le saut $H$ n'est pas continu, et aucune fonction continue ne peut être à distance $L^1$ nulle de $H$.
>
> La limite en $L^1$ est bien unique. Si $g_n$ convergait à la fois vers $H$ et vers une fonction continue $q$, l'inégalité triangulaire imposerait
>
> $$
> \|H-q\|_{L^1}
> \le \|H-g_n\|_{L^1}+\|g_n-q\|_{L^1}
> \longrightarrow0,
> $$
>
> ce qui dirait que $q$ coïncide presque partout avec $H$.
>
> **Pourquoi est-ce impossible pour une fonction continue ?** Supposons qu'une telle fonction continue $q$ existe.
>
> - Sur tout l'intervalle situé strictement à gauche de $1/2$, $H$ vaut $0$. Si $q$ valait, par exemple, $0{,}1$ en un point de cette zone, la continuité obligerait $q$ à rester proche de $0{,}1$ sur un petit intervalle autour de ce point. Sur tout ce petit intervalle, $q$ différerait de $H=0$. La différence ne serait donc pas limitée à quelques points négligeables : elle aurait une largeur positive. C'est incompatible avec « $q=H$ presque partout ».
> - Il faut donc que $q$ soit exactement égale à $0$ partout à gauche de $1/2$. De même, puisqu'à droite $H$ vaut $1$, il faut que $q$ soit exactement égale à $1$ partout à droite de $1/2$.
> - Mais alors $q$ devrait passer de $0$ à $1$ instantanément en $1/2$. C'est précisément un saut, donc $q$ ne serait pas continue.
>
> Cela répond à la question : il n'existe pas de limite continue cachée. La seule limite possible en distance $L^1$ est le saut $H$, à une modification près sur quelques points isolés ; modifier quelques points ne peut jamais rendre ce saut continu.
>
> On a ainsi une suite $(g_n)$ dont tous les termes sont continus, qui est de Cauchy pour la distance $L^1$, mais qui n'a **aucune limite dans l'espace des fonctions continues**. C'est exactement ce que signifie le fait que $C([0,1])$, muni de la norme $L^1$, n'est pas complet.
>
> **Notation : que signifie $C([0,1])$ ?** Le symbole $C$ signifie « continu ». Ainsi,
>
> $$
> C([0,1])
> =\{f:[0,1]\to\mathbb R\ ;\ f\text{ est continue}\}.
> $$
>
> On peut de même écrire $C((0,1))$ pour les fonctions continues définies seulement sur l’intervalle ouvert $(0,1)$. Les crochets ou parenthèses indiquent simplement le domaine : $[0,1]$ contient les extrémités $0$ et $1$, tandis que $(0,1)$ les exclut. La notation usuelle est donc $C([0,1])$, avec des parenthèses autour du domaine.
>
> Le fait que $[0,1]$ soit compact apporte une propriété utile — toute fonction continue sur cet intervalle est bornée, donc intégrable — mais ce n’est pas la raison pour laquelle $H$ n’appartient pas à $C([0,1])$. La seule raison est son saut en $1/2$.
>
> **Pourquoi $H$ appartient-elle à $L^1([0,1])$ ?** Par définition, une fonction appartient à $L^1$ lorsque l'aire sous sa valeur absolue est finie. Ici, $H$ ne prend que les valeurs $0$ et $1$, donc $|H|=H$. Elle vaut $0$ sur la moitié gauche de l'intervalle, et $1$ sur la moitié droite. Son intégrale est donc simplement l'aire d'un rectangle de hauteur $1$ et de largeur $1/2$ :
>
> $$
> \|H\|_{L^1}
> =\int_0^1 |H(x)|\,dx
> =0\times\frac12+1\times\frac12
> =\frac12<\infty.
> $$
>
> Ainsi, $H$ est bien une fonction intégrable. Plus généralement, toute fonction bornée sur un intervalle de longueur finie appartient à $L^1$.
>
> L'espace $L^1([0,1])$ est complet, il accepte donc naturellement cette limite. La complétude évite que les limites de nos approximations disparaissent simplement parce que l'espace choisi était trop petit.

> [!tip] À quoi cela sert en pratique ?
> Dans une preuve ou un algorithme, on construit souvent une suite d'approximations $f_1,f_2,\ldots$ : fonctions lissées, sommes partielles d'une série de Fourier, itérations numériques, etc. On montre d'abord qu'elles forment une suite de Cauchy. La complétude donne alors automatiquement une limite dans l'espace, sans avoir à deviner sa formule explicite.



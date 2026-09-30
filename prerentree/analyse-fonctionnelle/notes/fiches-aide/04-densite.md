---
title: "Densité et familles d’approximation"
tags:
  - mva
  - pre-rentree
  - analyse-fonctionnelle
  - densite
  - approximation
---

# Densité et familles d’approximation

[[00-socle-analyse-fonctionnelle|← Retour au socle]] · [[poly-analyse-fonctionnelle|Poly principal]]

Une famille $A$ est dense dans un espace $E$ si tout élément de $E$ peut être approché aussi précisément que l'on veut par des éléments de $A$.

L'exemple de base est le suivant : les nombres rationnels sont denses dans $\mathbb R$. Même si $\pi$ est irrationnel, on peut trouver des rationnels aussi proches de $\pi$ que souhaité, par exemple $3.1$, puis $3.14$, puis $3.141$, etc.

Pour les fonctions, les familles d'approximation importantes sont souvent les suivantes.

- **Les fonctions continues.** Elles permettent de remplacer les sauts par des transitions progressives. Par exemple, la marche $\mathbf 1_{[0,1/2]}$ n'est pas continue, mais on peut la remplacer par une fonction qui vaut $1$ jusqu'à $1/2-\varepsilon$, descend régulièrement jusqu'à $0$ entre $1/2-\varepsilon$ et $1/2+\varepsilon$, puis reste nulle. L'erreur n'existe que dans une bande de largeur $2\varepsilon$ ; elle devient petite dans $L^1$ ou $L^2$ quand $\varepsilon$ tend vers $0$.

- **Les fonctions lisses.** Une fonction lisse possède des dérivées de tous ordres, ce qui rend les calculs de dérivation et d'intégration par parties très confortables. La fonction $x\mapsto |x|$ est continue, mais a un coin en $0$ et n'est pas dérivable en ce point. Les fonctions

  $$
  h_\varepsilon(x)=\sqrt{x^2+\varepsilon^2}
  $$

  sont lisses et ressemblent de plus en plus à $|x|$ lorsque $\varepsilon$ devient petit. Elles « arrondissent » le coin sur une toute petite zone.

- **Les fonctions à support compact.** Dire qu'une fonction a un support compact signifie qu'elle est exactement nulle en dehors d'une région bornée. On peut alors faire les calculs comme si l'on travaillait sur un grand intervalle fini : rien ne se passe au-delà. C'est particulièrement utile pour éviter les termes de bord à l'infini lors des intégrations par parties.

  Prenons la gaussienne

  $$
  f(x)=e^{-x^2}.
  $$

  **Version très intuitive.** Imagine que l’on dessine la courbe de la gaussienne et que l’on pose un grand cache sur ses extrémités.

  - À l’intérieur de $[-R,R]$, le cache est transparent : on garde exactement la fonction $f$.
  - Loin à l’extérieur, après $R+1$, le cache est opaque : on remplace $f$ par $0$.
  - Entre les deux, on fait passer le cache progressivement de transparent à opaque, afin de ne pas créer un nouveau saut.

  La fonction obtenue, notée $f_R$, est donc identique à la gaussienne au centre et exactement nulle loin du centre. Elle n’est pas égale à $f$ partout : on a supprimé ses queues. Mais ces queues sont déjà très petites, et plus on place le cache loin — donc plus $R$ est grand — moins on enlève d’aire.

  Pour traduire ce cache en mathématiques, on utilise une fonction $\eta_R$ qui vaut $1$ là où l’on veut conserver $f$ et $0$ là où l’on veut l’éteindre. On peut prendre la fonction de coupure continue

  $$
  \eta_R(x)=
  \begin{cases}
  1 & \text{si }|x|\le R,\\
  R+1-|x| & \text{si }R<|x|<R+1,\\
  0 & \text{si }|x|\ge R+1.
  \end{cases}
  $$

  La fonction $\eta_R$ vaut exactement $1$ près du centre, puis descend de $1$ à $0$ dans une mince couronne, et reste nulle à l'extérieur. On définit

  $$
  f_R(x)=f(x)\eta_R(x)=e^{-x^2}\eta_R(x).
  $$

  Ainsi, $f_R=f$ sur $[-R,R]$, tandis que $f_R=0$ dès que $|x|\ge R+1$. Son support est donc contenu dans $[-R-1,R+1]$ : il est compact.

  Où se trouve l'erreur ? Dans la région $|x|>R$ uniquement. Sur $[-R,R]$, $\eta_R=1$, donc $f_R=f$ et l’erreur est exactement nulle. Ailleurs, on a simplement atténué la gaussienne, sans jamais la rendre plus grande : $0\le\eta_R\le1$. Ainsi,

  $$
  |f(x)-f_R(x)|
  =e^{-x^2}|1-\eta_R(x)|
  \le e^{-x^2}.
  $$

  La norme $L^1$ additionne cette erreur sur toute la droite. Comme l’erreur vaut $0$ au centre, son aire totale est au plus l’aire de la gaussienne que l’on a laissée à l’extérieur :

  $$
  \|f-f_R\|_{L^1}
  \le \int_{|x|>R}e^{-x^2}\,dx.
  $$

  Le membre de droite est l’aire des deux queues de la gaussienne, au-delà de $-R$ et de $R$. Il ne s’agit pas de l’aire sous toute la courbe, mais seulement de ce qui reste après avoir retiré le grand intervalle central $[-R,R]$.

  Pour voir pourquoi cette aire tend vers $0$, on utilise la symétrie de la gaussienne :

  $$
  \int_{|x|>R}e^{-x^2}\,dx
  =2\int_R^{+\infty}e^{-x^2}\,dx.
  $$

  Sur la partie droite, lorsque $x\ge R>0$, on a $1\le x/R$. On peut donc écrire

  $$
  e^{-x^2}
  \le \frac{x}{R}e^{-x^2}.
  $$

  Cette majoration est utile parce que $x e^{-x^2}$ s’intègre facilement. En effet,

  $$
  \int_R^{+\infty} x e^{-x^2}\,dx
  =\frac12e^{-R^2}.
  $$

  On obtient alors

  $$
  \int_{|x|>R}e^{-x^2}\,dx
  =2\int_R^{+\infty}e^{-x^2}\,dx
  \le 2\int_R^{+\infty}\frac{x}{R}e^{-x^2}\,dx
  =\frac{e^{-R^2}}{R}
  \longrightarrow0.
  $$

  Même sans retenir ce calcul, l’idée est la suivante : la gaussienne diminue extrêmement vite quand on s’éloigne de $0$ ; les morceaux de courbe que l’on retire sont donc de plus en plus plats, et leur aire totale finit par être négligeable.

  Ainsi, pour toute erreur souhaitée $\varepsilon>0$, il suffit de choisir $R$ assez grand pour que l’aire des queues soit inférieure à $\varepsilon$. C’est exactement ce qui signifie que $f_R$ converge vers $f$ dans $L^1$.

  La stratégie pratique est donc : on prouve d’abord une propriété pour $f_R$, qui est nulle loin à l’infini et ne crée aucun terme de bord à l’infini ; puis on fait tendre $R$ vers l’infini. Mais ce dernier passage n’est pas automatique : il faut que la propriété soit **stable pour la convergence choisie**, ici la convergence dans $L^1$.

  Par exemple, les intégrales sont stables. Fixons une fonction $\varphi$ bornée : cela veut dire qu’il existe un nombre $M$ tel que

  $$
  |\varphi(x)|\le M
  \qquad\text{pour tout }x.
  $$

  On peut prendre $M=\|\varphi\|_{L^\infty}$, c’est-à-dire la plus grande taille prise par $\varphi$. Comparons maintenant les deux nombres

  $$
  I_R=\int_{\mathbb R}f_R(x)\varphi(x)\,dx
  \qquad\text{et}\qquad
  I=\int_{\mathbb R}f(x)\varphi(x)\,dx.
  $$

  Leur différence est

  $$
  I_R-I
  =\int_{\mathbb R}(f_R(x)-f(x))\varphi(x)\,dx.
  $$

  Voici toutes les étapes de l’inégalité. On utilise d’abord le fait général que la valeur absolue d’une intégrale est au plus l’intégrale de la valeur absolue :

  $$
  |I_R-I|
  =\left|\int_{\mathbb R}(f_R-f)\varphi\right|
  \le \int_{\mathbb R}|f_R(x)-f(x)|\,|\varphi(x)|\,dx.
  $$

  Ensuite, $\varphi$ est bornée par $M$. Autrement dit, à chaque point $x$,

  $$
  |f_R(x)-f(x)|\,|\varphi(x)|
  \le M|f_R(x)-f(x)|.
  $$

  En intégrant cette inégalité point par point, on obtient

  $$
  |I_R-I|
  \le M\int_{\mathbb R}|f_R(x)-f(x)|\,dx
  =M\|f_R-f\|_{L^1}.
  $$

  Enfin, on choisit $M=\|\varphi\|_{L^\infty}$. Cette norme est simplement le plus grand facteur par lequel $\varphi$ peut amplifier l’erreur. Cela donne bien

  $$
  |I_R-I|
  \le \|\varphi\|_{L^\infty}\,\|f_R-f\|_{L^1}.
  $$

  Or $\|f_R-f\|_{L^1}$ tend vers $0$. Donc $|I_R-I|$ tend aussi vers $0$ : les nombres $I_R$ convergent vers le nombre $I$.

  Voici maintenant ce que signifie l’hypothèse

  $$
  I_R=\int_{\mathbb R}f_R(x)\varphi(x)\,dx=0
  \qquad\text{pour tout }R,
  $$

  $I_R$ est juste un nom pour le nombre obtenu après avoir calculé l’intégrale avec le paramètre $R$. L’égalité ci-dessus dit que, quel que soit le rayon de coupure choisi, ce nombre vaut $0$. Par exemple :

  $$
  I_1=0,\qquad I_{10}=0,\qquad I_{100}=0,\qquad\ldots
  $$

  Si l’on fait tendre $R$ vers l’infini — par exemple en prenant $R=1,2,3,\ldots$ — on a donc une suite de nombres qui vaut toujours $0$. Sa limite est forcément $0$.

  Mais le paragraphe précédent a montré que ces mêmes nombres $I_R$ se rapprochent de $I$. Il n’y a qu’une seule limite possible : $I$ doit donc lui aussi valoir $0$. Ainsi,

  $$
  I=\int_{\mathbb R}f(x)\varphi(x)\,dx=0.
  $$

  C’est cela, « transmettre une propriété à la limite » : l’identité obtenue pour chaque fonction coupée devient une identité pour $f$ parce que les deux intégrales se rapprochent.

  En revanche, une propriété qui porte sur les dérivées ne se transmet pas forcément avec la seule convergence $L^1$. Par exemple, sur $[0,2\pi]$, les fonctions

  $$
  u_n(x)=\frac{\sin(nx)}{n}
  $$

  tendent vers $0$ dans $L^1$, car leur amplitude est au plus $1/n$. Pourtant leurs dérivées sont

  $$
  u_n'(x)=\cos(nx),
  $$

  et ces dérivées ne tendent pas vers $0$ dans $L^1$ : elles continuent à osciller avec une taille moyenne non nulle. Connaître la convergence des fonctions ne suffit donc pas à connaître le comportement de leurs dérivées. Il faut aussi contrôler les dérivées dans une norme adaptée.

  Le mot à retenir est donc : **vérifier la bonne notion de convergence avant de passer à la limite**.

  Enfin, la coupure précédente avait des coins aux endroits $|x|=R$ et $|x|=R+1$ : elle est continue, mais pas lisse. On peut arrondir ces coins. Plus précisément, on choisit une fonction lisse $\eta$ telle que

  $$
  0\le\eta\le1,\qquad
  \eta(x)=1\ \text{si }|x|\le1,\qquad
  \eta(x)=0\ \text{si }|x|\ge2,
  $$

  puis on pose

  $$
  \eta_R(x)=\eta(x/R).
  $$

  Cette nouvelle coupure vaut $1$ sur $[-R,R]$ et $0$ en dehors de $[-2R,2R]$, mais elle est lisse entre les deux. Comme la gaussienne est elle-même lisse, le produit $f_R=f\eta_R$ est alors lisse et à support compact.

  La notation

  $$
  C_c^\infty(\mathbb R)
  $$

  désigne précisément les fonctions définies sur $\mathbb R$ qui sont à la fois continues, dérivables autant de fois que l’on veut, et nulles hors d’un intervalle borné. Ce sont des approximants particulièrement pratiques : on peut les dériver et intégrer sans difficulté, et elles ne créent aucun problème à l’infini.

- **Les fonctions en marches.** Elles sont constantes par morceaux. Pour approcher $f(x)=x^2$ sur $[0,1]$, on découpe l'intervalle en $N$ petits morceaux et on remplace $x^2$ par une constante sur chaque morceau. Plus les morceaux sont fins, plus l'escalier suit la courbe. Ces approximations sont simples à intégrer, car une intégrale devient une somme d'aires de rectangles.

- **Les sinusoïdes et les combinaisons finies de fonctions simples.** Sur un intervalle, une somme de sinus et cosinus peut approcher un signal périodique : c'est l'idée des séries de Fourier. Une vibration compliquée peut ainsi être décrite comme l'addition d'ondes simples, chacune avec une fréquence et une amplitude. Les sommes partielles ne gardent qu'un nombre fini de fréquences et donnent des approximations de plus en plus fines.

Ces familles se combinent souvent. Les fonctions de $C_c^\infty$, à la fois lisses et à support compact, sont par exemple des approximants particulièrement pratiques : elles sont assez régulières pour tous les calculs usuels, et assez localisées pour ne rien imposer à l'infini.

> [!example] Approcher une marche par une pente douce
> La fonction indicatrice $\mathbf 1_{[0,1/2]}$ saute brutalement de $1$ à $0$ en $x=1/2$. Elle n'est pas continue. Pourtant, on peut remplacer le saut par une petite pente sur l'intervalle $[1/2-\varepsilon,1/2+\varepsilon]$. Plus $\varepsilon$ est petit, plus la zone où les deux fonctions diffèrent est mince ; l'erreur en $L^1$ ou en $L^2$ devient alors aussi petite que souhaité.

La densité est précieuse parce que les fonctions lisses sont faciles à dériver, intégrer et utiliser comme fonctions tests. On peut d'abord prouver une formule pour elles, puis l'étendre à une fonction plus générale en la remplaçant par une suite de fonctions lisses qui l'approchent.

> [!tip] Réflexe
> Pour montrer une propriété sur tout un espace de fonctions, chercher d'abord une famille dense sur laquelle le calcul est simple. Il faudra ensuite vérifier que la propriété se conserve quand on passe à la limite.



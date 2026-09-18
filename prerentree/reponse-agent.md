# Newton, quasi-Newton et sous-espaces, simplement

La descente de gradient ne regarde que la **pente** sous ses pieds. Elle sait dans quel sens descendre, mais pas si la vallée est très plate ou très raide.

Newton regarde aussi la **courbure** : il essaie de reconnaître la forme du bol dans lequel il se trouve. Cela lui permet de choisir non seulement une direction, mais aussi une distance adaptée dans chaque direction.

## L'idée avec un bol en deux dimensions

Considérons

$$f(x,y)=(x-2)^2+10(y-1)^2.$$

Le minimum est au fond du bol, en $(2,1)$. Le coefficient $10$ signifie que le bol est beaucoup plus raide dans la direction $y$ que dans la direction $x$.

Partons de $(x_0,y_0)=(0,0)$. Le gradient vaut

$$\nabla f(x,y)=\bigl(2(x-2),20(y-1)\bigr),$$

donc

$$\nabla f(0,0)=(-4,-20).$$

La descente de gradient dirait seulement : « aller vers la droite et vers le haut ». Avec un même pas $\gamma$, elle ferait

$$
(x_1,y_1)=(0,0)-\gamma(-4,-20)=(4\gamma,20\gamma).
$$

Le problème est qu'un pas adapté à $y$ peut être trop petit pour $x$, et un pas adapté à $x$ peut être trop grand pour $y$. La descente de gradient doit donc souvent avancer prudemment.

Newton utilise aussi la Hessienne :

$$\nabla^2f(x,y)=
\begin{pmatrix}
2&0\\
0&20
\end{pmatrix}.
$$

Elle décrit la raideur du bol : $2$ dans la direction $x$, $20$ dans la direction $y$. Le pas de Newton est

$$
\begin{aligned}
(x_1,y_1)
&=(x_0,y_0)-[\nabla^2f(x_0,y_0)]^{-1}\nabla f(x_0,y_0)\\
&=(0,0)-
\begin{pmatrix}
1/2&0\\
0&1/20
\end{pmatrix}
\begin{pmatrix}
-4\\
-20
\end{pmatrix}\\
&=(0,0)-( -2,-1)\\
&=(2,1).
\end{aligned}
$$

Newton arrive ici au minimum en **un seul pas** parce que la fonction est exactement quadratique : son « modèle local » est en fait la vraie fonction partout.

## Que signifie la formule de Newton ?

Près de $x_n$, Newton remplace la fonction par un petit modèle en forme de parabole :

$$f(x)\approx f(x_n)
+\langle\nabla f(x_n),x-x_n\rangle
+\frac12\langle x-x_n,\nabla^2f(x_n)(x-x_n)\rangle.$$

- Le gradient indique l'inclinaison du sol.
- La Hessienne indique si le sol se redresse vite ou lentement.
- Newton minimise cette parabole approximative, ce qui donne

$$x_{n+1}=x_n-[\nabla^2f(x_n)]^{-1}\nabla f(x_n).$$

L'inverse de la Hessienne ne doit pas être lu comme « calculer une grosse matrice inverse » : en pratique, on résout plutôt le système linéaire

$$\nabla^2f(x_n)p_n=-\nabla f(x_n),$$

puis on pose $x_{n+1}=x_n+p_n$.

### Exemple : résoudre le système plutôt qu'inverser une matrice

Reprenons

$$f(x,y)=(x-2)^2+10(y-1)^2$$

au point $x_0=(0,0)$. On connaît déjà le gradient et la Hessienne :

$$\nabla f(x_0)=
\begin{pmatrix}
-4\\
-20
\end{pmatrix},
\qquad
\nabla^2f(x_0)=
\begin{pmatrix}
2&0\\
0&20
\end{pmatrix}.
$$

Au lieu de calculer explicitement

$$
\begin{pmatrix}
2&0\\
0&20
\end{pmatrix}^{-1},
$$

on cherche directement le déplacement $p_0=(p_1,p_2)$ qui vérifie

$$
\begin{pmatrix}
2&0\\
0&20
\end{pmatrix}
\begin{pmatrix}
p_1\\
p_2
\end{pmatrix}
=-
\begin{pmatrix}
-4\\
-20
\end{pmatrix}
=
\begin{pmatrix}
4\\
20
\end{pmatrix}.
$$

Cela donne simplement les deux équations

$$2p_1=4,\qquad20p_2=20,$$

donc

$$p_0=(2,1).$$

Enfin,

$$x_1=x_0+p_0=(0,0)+(2,1)=(2,1).$$

Ici le système est diagonal, donc il se résout à la main. En grande dimension, le principe est identique, mais on utilise un solveur de systèmes linéaires adapté à la Hessienne. On évite ainsi de fabriquer l'inverse complet : cela demande moins de calculs et est en général plus stable numériquement.

### D'où vient l'équation $\nabla^2f(x_n)p_n=-\nabla f(x_n)$ ?

Newton ne cherche pas directement le meilleur prochain point dans la vraie fonction $f$, qui peut être compliquée. Il cherche le meilleur **petit déplacement** $p$ dans le modèle quadratique construit autour de $x_n$ :

$$q_n(p)
=f(x_n)+\langle\nabla f(x_n),p\rangle
+\frac12\langle p,\nabla^2f(x_n)p\rangle.$$

Ici, on a simplement remplacé $x-x_n$ par $p$. Ainsi, le point candidat est

$$x_{n+1}=x_n+p.$$

Pour choisir le meilleur $p$, on minimise $q_n(p)$. Comme $q_n$ est une fonction quadratique de $p$, on annule son gradient par rapport à $p$ :

$$\nabla_p q_n(p)=\nabla f(x_n)+\nabla^2f(x_n)p.$$

Le premier terme vient de la pente locale. Le second vient de la dérivée du terme quadratique : la Hessienne joue le même rôle que le coefficient devant $p^2$ dans une parabole.

Au minimum du modèle, le gradient doit être nul :

$$\nabla_p q_n(p_n)=0.$$

Donc

$$\nabla f(x_n)+\nabla^2f(x_n)p_n=0,$$

et, en déplaçant le premier terme de l'autre côté,

$$\boxed{\nabla^2f(x_n)p_n=-\nabla f(x_n).}$$

> [!example] En une dimension
> Si $q(p)=a+bp+\tfrac12cp^2$, alors $q'(p)=b+cp$. Son minimum vérifie $b+cp=0$, donc $cp=-b$. L'équation de Newton est exactement la même formule, avec $b=\nabla f(x_n)$ et $c=\nabla^2f(x_n)$.

La Hessienne doit être définie positive près du minimum : cela garantit que le modèle local est bien un bol avec un fond, et non une selle ou une bosse.

## Pourquoi dit-on que Newton est très rapide près du minimum ?

Notons $x^*$ le vrai minimum et

$$e_n=\|x_n-x^*\|$$

la distance entre notre approximation et ce minimum. Dire que Newton converge quadratiquement signifie que, **lorsqu'on est déjà assez près de $x^*$**, on a approximativement

$$e_{n+1}\approx C e_n^2,$$

où $C$ est une constante qui ne change pas d'une itération à l'autre.

Le point important est le carré. Si l'erreur actuelle est $e_n=0.1$ et si, pour simplifier, $C\simeq1$, alors

$$0.1\longmapsto 0.01\longmapsto0.0001\longmapsto0.00000001.$$

Chaque itération fait donc rapidement gagner beaucoup de chiffres corrects.

### Pourquoi le carré apparaît-il ?

Près du minimum, la vraie fonction ressemble à son modèle quadratique. Newton choisit exactement le minimum de ce modèle.

Il reste bien une différence entre la vraie fonction et le modèle, car la fonction n'est en général pas une parabole parfaite. Mais cette différence est d'ordre trois par rapport au déplacement :

$$f(x_n+p)=q_n(p)+\text{une erreur très petite, d'ordre }\|p\|^3.$$

Après avoir dérivé pour trouver le minimum, cette erreur se traduit par une erreur sur le nouveau point d'ordre $\|p\|^2$. Or, près du minimum, le déplacement de Newton $p_n$ est lui-même de l'ordre de l'erreur actuelle $e_n$. On obtient donc

$$e_{n+1}\approx C\|p_n\|^2\approx C e_n^2.$$

Il n'est pas nécessaire de retenir la démonstration complète : retiens l'image suivante.

> [!tip] Image mentale
> Newton remplace le terrain autour de lui par un bol. Près du fond, ce faux bol ressemble tellement au vrai terrain que son fond est presque exactement le vrai minimum. Plus on est près du minimum, plus cette approximation devient fiable — et l'erreur restante diminue comme son carré.

### Comparaison avec la descente de gradient

Pour une descente de gradient bien réglée, on obtient plutôt une convergence **linéaire** :

$$e_{n+1}\approx \rho e_n,\qquad 0<\rho<1.$$

Par exemple, avec $\rho=1/2$ :

$$0.1\longmapsto0.05\longmapsto0.025\longmapsto0.0125.$$

On divise l'erreur par deux à chaque pas : c'est fiable, mais bien moins spectaculaire que les carrés successifs de Newton.

On peut résumer le résultat théorique par

$$\|x_{n+1}-x^*\|\leq C\|x_n-x^*\|^2.$$

Cette vitesse apparaît seulement lorsque :

1. on est déjà assez près du bon minimum ;
2. la Hessienne au minimum est définie positive, donc le minimum ressemble réellement au fond d'un bol ;
3. la Hessienne ne varie pas trop brutalement autour de ce point.

Loin du minimum, le petit modèle quadratique peut être trompeur : son minimum peut indiquer un pas trop long ou une mauvaise zone. C'est pour cela que les implémentations utilisent souvent Newton amorti ou régularisé avant de laisser Newton accélérer près de la solution.

## Newton amorti ou régularisé : une ceinture de sécurité

Pour éviter de faire un pas trop ambitieux, on utilise souvent

$$x_{n+1}=x_n-\gamma_n[\nabla^2f(x_n)+\eta_n I]^{-1}\nabla f(x_n).$$

- $\gamma_n\in]0,1]$ réduit la longueur du pas. Si le modèle est peu fiable, on avance seulement dans sa direction.
- $\eta_n I$ ajoute de la courbure positive. Cela évite d'inverser une Hessienne presque singulière ou qui ne ressemble pas à un bol.

Quand $\eta_n$ est grand, le comportement se rapproche davantage d'une descente de gradient prudente. Quand on approche du minimum, on peut diminuer $\eta_n$ et reprendre un vrai pas de Newton.

## Quasi-Newton : apprendre la courbure sans calculer la Hessienne

Calculer la Hessienne peut être très coûteux, surtout en grande dimension. Les méthodes quasi-Newton ne la calculent donc pas exactement. Elles construisent progressivement une approximation $H_n^{-1}$ de son inverse à partir des déplacements et des changements de gradient observés au cours des itérations.

La mise à jour devient

$$x_{n+1}=x_n-H_n^{-1}\nabla f(x_n).$$

L'idée est : « les dernières étapes m'ont appris quelle direction est plate et quelle direction est raide ; je vais réutiliser cette information ». BFGS et L-BFGS sont des exemples célèbres de cette famille. L-BFGS conserve seulement quelques souvenirs récents : c'est beaucoup moins coûteux qu'une matrice Hessienne dense.

## Méthodes à sous-espace : choisir le meilleur mélange de quelques directions

La descente de gradient ne propose qu'une direction : $-\nabla f(x_n)$. Une méthode à sous-espace en ajoute quelques-unes, par exemple la direction du déplacement précédent

$$d_n=x_n-x_{n-1}.$$

Elle cherche alors le prochain point dans le petit plan engendré par ces directions :

$$x_{n+1}=x_n-\gamma_n^{(1)}\nabla f(x_n)+\gamma_n^{(2)}d_n.$$

Au lieu de chercher parmi toutes les directions possibles de l'espace, on choisit les deux nombres $\gamma_n^{(1)}$ et $\gamma_n^{(2)}$. C'est une recherche beaucoup plus simple, mais qui peut exploiter l'élan et la courbure suggérés par les itérations précédentes.

En résumé :

- **gradient** : suit la pente ;
- **Newton** : utilise pente et courbure exacte ;
- **quasi-Newton** : apprend une approximation de la courbure ;
- **sous-espace** : choisit un bon mélange de quelques directions mémorisées.

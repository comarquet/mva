---
title: "Socle d’analyse fonctionnelle"
tags:
  - mva
  - pre-rentree
  - analyse-fonctionnelle
  - prerequis
---

# Socle d’analyse fonctionnelle

Cette note sert de point d’entrée pour les notions à maîtriser avant le reste du poly.

- [[01-presque-partout|Presque partout]]
- [[02-normes-et-convergence|Normes et convergence]]
- [[03-completude|Complétude]]
- [[04-densite|Densité et familles d’approximation]]

## Vue d’ensemble

Dans $\mathbb R^n$, un vecteur est une liste finie de nombres :

$$
(x_1,x_2,\ldots,x_n).
$$

Une fonction $f$ peut se voir comme une liste de valeurs $f(x)$, une pour chaque point $x$ du domaine. Cette liste possède une infinité de coordonnées. L'analyse fonctionnelle fournit le langage pour la manipuler sans devoir regarder chaque valeur une par une.

La norme résume la taille de cette liste ; la convergence dit si deux fonctions deviennent indiscernables pour cette mesure ; la complétude garantit que les approximations ont une limite utilisable ; la densité permet de remplacer les fonctions compliquées par des fonctions faciles.

> [!summary] Les quatre idées à retenir
> - **Presque partout** : les exceptions de mesure nulle ne comptent pas pour les intégrales.
> - **Norme** : il faut préciser ce que « petit » ou « proche » veut dire.
> - **Complétude** : une approximation qui se stabilise garde sa limite dans l'espace.
> - **Densité** : les fonctions simples peuvent suffire à approcher toutes les autres.


Analyse des programmes et sémantiques
=====================

Notes de cours - Version 1.0 | Nadarajah Mag-Stellon


----------

Sommaire
---------
[TOC]



Introduction
---------

$\lambda$-calcul
---------

### Règles de base

Lorsque l'on a plusieurs expressions, on applique l'associativité à gauche :

$$E_{1} E_{2} E_{3} = ((E_{1} E_{2}) E_{3}) $$

La portée d'une définition de fonction est maximale à droite :

$$ \lambda x.E_{1} E_{2} E_{3} = \lambda x.(E_{1} E_{2} E_{3}) $$

Une $\beta$-redex est une expression de définition de fonction associée à une expression.
Ainsi tout $(\lambda v.E)E_{1}$ est un $\beta$-redex.


### Réductions

Deux méthodes existent pour la réduction par $\beta$-reduction :

-   **ordre applicatif** : réduire toujours le plus à gauche et le plus profond
-   **ordre normal (Méthode à privilégier)** : réduire toujours le plus à gauche et le plus extérieur

#### Applications

Réduire l'expression $(\lambda x.x)(\lambda y z u.y(zu))abc$

> **Notes**
>
> En suivant l'associativité à gauche, l'expression est équivalente à $(((((\lambda x.x)(\lambda y z u.y(zu)))a)b)c)$
>
> On identifie tous les $\beta$-redex et on applique une technique de réduction.


Ordre applicatif 
:   $(\lambda x.x)(\lambda y z u.y(zu))abc$
    $\underset{\beta}{\Rightarrow} (\lambda y z u.y(zu))abc$
    $\underset{\beta}{\Rightarrow}((((\lambda y z u.y(zu))a)b)c)$
    $\underset{\beta}{\Rightarrow} a(bc)$

    
Ordre normal 
:   $(\lambda x.x)(\lambda y z u.y(zu))abc$
    $\underset{\beta}{\Rightarrow} (\lambda y z u.y(zu))abc$
    $\underset{\beta}{\Rightarrow}((((\lambda y z u.y(zu))a)b)c)$
    $\underset{\beta}{\Rightarrow} a(bc)$

Des fois, l'ordre normal et applicatif effectuent les mêmes réductions ...

#### Récursions
 Le $\lambda$-calcul ne propose pas de syntaxe pour exprimer la récursion.
 Il faut donc ruser en utilisant un **combinateur de point fixe**.

> **Notes**
>
> Il n'est pas utile de savoir comment il fonctionne 
> mais connaître son existence est important.


Sémantique dénotationnelle avec BOPL
---------

En sémantique dénotationnelle, il n'est plus permis d'utiliser la dénotation d'autres constructions.
Par exemple, on ne peut pas exprimer la récursion du *while* par le *while* (En SOS, on l'a pourtant fait mais **maintenant c'est interdit**)

En sémantique dénotationnelle (SD), il faut définir trois notions :

- La syntaxe abstraite : exprimer la forme des programmes, les instructions, les expressions ...
- La syntaxe sémantique : exprimer les domaines sémantiques et les opérations associées qui sont la dénotation de la syntaxe.
- Ecrire des equations qui font la connexion entre les syntaxes précédentes.


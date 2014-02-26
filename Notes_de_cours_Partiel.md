Analyse des programmes et sémantiques
=====================

Notes de cours - Version 2.0 | Nadarajah Mag-Stellon


----------

Sommaire
---------
[TOC]



Introduction
---------

#### Spécification des langages de programmation

On essaye de donner une définition formel du langage en montrant les propriétés du langage.
> **Explication**
> - **Définition formel** : Utilisation du formalisme de la logique (calcul de séquent, prémice, consequent ...)
> - **Propriétés du langage** : Les calculs effectués par le programme, les types du langage ...

#### Grammaire et syntaxe

**La syntaxe concrète**
:   La syntaxe concrète se concentre sur l'agencement des "mots" du langage.

**La syntaxe abstraite**
:   La syntaxe abstraite se focalise sur comment est "construit" le langage.

**La grammaire concrète** 
:   La grammaire concrète sert à écrire un programme bien former car on s'occupe de l'agencement des mots (pour écrire du BOPL, on utilisera donc la grammaire concrète de BOPL).

**La grammaire abstraite** 
:  La grammaire abstraite sert à écrire un AST qui représente le programme (Pour la vérification de type en BOPL, on s'aide de la grammaire abstraite de BOPL).

#### Sémantique

On utilise dans ce cours la **sémantique opérationnelle structuelle (SOS)** pour décrire la sémantique du langage.
**SOS** permet de décrire un langage par le biais de **la logique de première ordre**.  

> **Explication**
> - **Sémantique** : C'est "expliquer un langage"
> - **Pourquoi Prologue ?** : SOS utilise la logique et est facilement transposable en *Prolog*.


#### BOPL

Un programme **BOPL** est formé de la manière suivante :

-   L'ensemble des déclarations de classes.
-   La déclaration des variables du programme.
-   Le corps du programme.

> **Note**
>
> - Voir la grammaire concrète de BOPL


```
/* Exemple de programme BOPL commenté */

program
    // Ensemble des classes du programme
    // Classe Point
    class Point is
    vars
        // Attributs de la classe Point
        Int x, y ;
        // Methodes
        methods
            // Methode add de la classe Point
            // Type Retour, Nom methode, Paramètres
            Point add(Point p)
            let
                // Variable local de la méthode add
                Point ret ;
            in
            // Corps de la méthode add
            begin
                ret := new Point ;
                ret.x := (self.x) + (p.x) ;
                ret.y := (self.y) + (p.y) ;
                return ret
            end
        end
    let
        // Variable local du programme
        Point p1, p2, p3 ;
    in
    // Corps du programme
    begin
        p1 := new Point ;
        p1.x := 1 ;
        p1.y := 2 ;
        p2 := new Point ;
        p2.x := 5 ;
        p2.y := 10 ;
        p3 := p1.add(p2) ;
```


Analyses statiques et vérification des types
---------

Un programme peut être représenté par un AST contenant les expressions du programme *(cf grammaire abstraite)*.

La vérification de type est possible par la transformation : 

-  AST d'un programme -> AST du même programme dans lequel chaque noeud est annoté par un type.

> **Note**
>
> - On aura donc une grammaire abstraite typée de BOPL
> - L'annotation de type est écrit en majuscule. Par exemple, le type annoté pour l'entier est noté **INT**. 
> - Les noeuds seront écrit en minuscule. Par exemple, le noeud entier est noté **int**.

Les classes
:   Les noms de classes sont représentés **cexp**. Par exemple, on a *new cexp* ou *instanceof cexp*.

> **A noter**
>Dans les noeuds de classe, il faudra donc rajouter un attribut **aType**. Lorsque l'on >fait un *aType(NoeudTypé)*, alors on a l'attribut *aType* du noeudTypé.
>La double flèche vers le bas extrait la valeur du *aType* soit la classe du noeud >typé.
>On fait donc la différence entre la classe "normal" et la classe typé. Par exemple, on >a la classe Point et le type associé idPoint. 

Les listes 
:   Flèche vers le haut d'une liste $\to$ premier de la liste 
Flèche vers le bas d'une liste $\to$ reste de la liste
element clé de Sol Liste $\to$ concatène l'élement avec Liste 

#### Les contextes

> **Le contexte de typages **
>
> - Symbole : $\Gamma$ 
> - Définition : Le contexte contient les informations des types des toutes les variables et des méthodes. Pour éviter les collisions des noms de variables d'instance et de méthodes, on va précéder ces derniers par leur classe (classe@id).
> - Formalisme : $\Gamma(id) \to Type$
> - Domaine : $id \cup idClasse@id \to id$ 

> **Le contextes des relations d'héritages **
>
> - Symbole : $\Delta$ 
> - Définition : Le contexte contient toutes les informations sur les héritages de toutes les classes. 
> - Formalisme : $\Delta(idFils) -> idPere$
> - Domaine : $id \to id$ 

> **Le type du retour courant **
>
> - Symbole : $\tau$ 
> - Définition : On doit être dans une méthode, le contexte contient le type de retour de la méthode
> - Formalisme : $\tau \to id$
> - Domaine : $ \{id\}$ 

> **L'identifiant de la classe courante**
>
> - Symbole : $\delta$ 
> - Définition : On doit être dans une classe, le contexte contient un équivalent de *this*
> - Formalisme : $\delta \to idClasse$
> - Domaine : $ \{id\}$ 


#### La logique pour la vérification de types

> **Mémento**
>
> - Le mot clé **id** correspond à un nom.
> - Pour étendre $\Gamma$, on fait $\Gamma[idType/nomClass@id]$ ou $\Gamma[idType/id]$  
> - Lorsque l'on modifie un contexte, on met le contexte dans le tuple du calcul du séquent.
> - Lorsque l'on ne modifie pas le contexte, on le met dans les hypothèses.


#### Syntaxes et fonctions utiles

method id var1* cepx var2* inst : formalTypes(tvar1*)
:   id $\to$ nom de la méthode
    var1* $\to$ liste des paramètres de la méthode
    cepx $\to$ type de retour
    var2* $\to$ liste des variables locales de la méthode
    inst $\to$ corps de la méthode
    formalTypes(tvar1*) $\to$ produit cartésien de la liste des types tvars1*

typeOf 
:    retourne le type associé à un AST typé.

formalTypes 
: extrait les types d’une liste de variables typées pour les retourner sous la forme $T1*..Tn$.

inheritedMethod 
: vrai s’il existe une méthode héritée du nom demandé à partir de la classe donnée.

inhMethodType 
: retourne le type de la méthode héritée, s’il y en a une.

contravariant 
: vrai si les types fonctionnels sont contravariants.

conformsTo 
: vrai si les types sont conformes.

actualsConformTo 
: vrai si les types des paramètres réels sont conformes aux types des paramètres formels d’une méthode.

fieldType 
: retourne le type du premier champ du nom donné déclaré ou hérité à partir de la classe donnée.


Sémantiques opérationnelles structurelles
--------

#### Les valeurs de BOPL

Entier
:   Tout les nombres que l'on peut représenter dans $\mathbb{Z}$

Booléens
:   Les booléeens **true** et **false** représentés par $T = \{true,false\}$

Identifiants d'objets
:   Un équivalent de **Oid** en Java

Identifiant de classe
:   Un **id** qui identifie les classes du programme



#### Les contextes

> **Le contexte d'environnement **
>
> - Symbole : $\rho$ 
> - Définition : Ce contexte contient les adresses mémoires des ids. 
Il y a **Classes** car les classes ne sont pas modifiables.
Il y a **Oid** car à l'execution d'une méthode, on peut trouver **self** et **super**. La valeur de ces mots clés ne changent pas dans une méthode alors on l'ajoute à $\rho$
> - Formalisme : $\rho(id) \to Adresse$
> - Domaine : $id  \to Classes \cup Adresse \cup Oid \cup V$ 


> **Le store **
>
> - Symbole : $\sigma$ 
> - Définition : Ce contexte contient les valeurs des adresses mémoires. 
Il y a **Object** car les objets sont modifiables.
> - Formalisme : $\sigma(Adresse) \to V \cup Object$
> - Domaine : $Adresse  \to \mathbb{Z} \cup T \cup Oid \cup id \cup Object$ 

#### Fonctions utiles

#####Mémoire
$def : V \to T$

$undef : V \to T$


$allocate : \sigma \to Address$ 
:   permet d’allouer un nouvel espace mémoire et retourne l’adresse de ce dernier.

$createOid : Address \to Oid$ 
:   permet de créer un identifiant d’objet à partir de l’adresse à laquelle l’objet correspondant va être mémorisé.

$@ : Oid \to Address$ 
:   permet de retrouver l’adresse correspondant à un identifiant d’objets.

$allocateAll : id^* * \rho * \sigma \to \rho * \sigma $
:   alloue les variables et retourne les environnements et mémoires après allocations.

$storeAll : id^* * V^* * \rho * \sigma \to \rho * \sigma $
:   alloue les variables et les initialise avec les valeurs passées en argument.

$+$ 
:   Permet de faire la concatenation des chaînes de caractères.

#####Classe

$lookup : id * id * \rho \to Methode$
:   retourne la méthodes associée à une classe

$getSuper : id * \rho \to id $
:   retourne l’identifiant de la superclasse d’une classe.

$inheritsFrom : id * id * \rho \to T$
:   retourne vrai si une classe (premier identifiant) hérite directement ou indirectement de la seconde (second identifiant).

$inheritedFields : id * \rho \to id^* $
:   retourne la liste des identifiants des champs hérités par la classe.

$getOwnedFields : Id * \rho \to id^*$ 
:   donne les champs déclarés et hérités par une classe.

#####Objet

$classOf : oid * \rho \to id$ 
:   retourne l’identifiant de la classe d’instantiation d’un objet

$allocateFields : id^* \to id \to V $
:   crée la fonction fields à partir des identifiants des champs

$fieldsOf : Object \to Id  \to V $
:   retourne l’association des champs à leurs valeurs d’un objet.

$writeField : oid * id * v * \rho \to \rho$
:   rend une nouvelle mémoire où la valeur d’un champ dans un objet a été modifiée pour prendre une nouvelle valeur.


#### Syntaxe utiles

$class(id,id_s,id^*,md)$
:   $id$ est l'id de classe
    $id_s$ est l'id de la superclasse
    $id^*$ est la liste des variables déclaréees et héritées par la classe
    $md$ est un dictionnaire des méthodes de la classe

$object(oid,id,fields)$
:   $oid$ est l'id de l'objet
    $id$ est l'id de la classe d'instanciation
    $fields : id \to V$ donne les valeurs associées aux champs

$method(id,id_1^*,id_2^*,inst)$
:   $id$ est l'id de la classe qui déclare la méthode
    $id_1^*$ est la liste d'id des paramètres
    $id_2^*$ est la liste d'id des variables locales
    $inst$ est le corps de la méthode

Applications
---------

#### Solution à un problème

- Comprendre la question grâce à la présence de certains mots-clés.
- Essaye de trouver une solution aux problème
- Formaliser le problème avec la logique

#### Exemples

##### Méthodes récursives

- **Problème** : Une méthode ne peut pas s'appeller elle-même
    > **Exemple** :
    >
    > ```  method A (x,y,z){ A(1,2,3); } ```
- **Solution** : Lorsqu'une méthode s'appelle elle-même, il faudrait que cette dernière se connaisse. On va donc étendre $\Gamma$ avec l'ensemble des méthodes du programme avant toute analyse du programme.

- **Formalisme** : Voir TD 2 Question 2

##### Analyse statique

- **Problème** : Vérifier que le corps d'une méthode se termine par *return*

- **Solution** : On peut avoir une valeur booléenne pour chaque instruction afin de savoir si l'instruction retourne ou pas un *return*. 
Par exemple, on peut clairement voir qu'un *assign* rend faux.
Mais pour une instruction *if*, c'est impossible de savoir si on va rentrer dans le corps du *if* ou du *else* parceque l'on fait de l'analyse statique. On va donc en conclure, que la valeur booléenne est $\mathbb{valeurbooléenne(if) \ and \  valeurbooléenne(else) }$
Concernant l'instruction *while*, on ne sait même pas si on va rentrer dans la boucle. Par pessimisme, on va dire que *while* rend faux.

- **Formalisme** : Voir TD 3 Question 1


##### Extension du langage

- **Problème** : Rajouter la boucle *for* à BOPL

- **Solution** : Penser à comment fonction le *for* dans un langage de programmation.
Ne pas oublier que la variable d'initialisation du *for* est connu dans le corps du *for*. Donc, il faut étendre $\Gamma$ avec cette variable. 

- **Formalisme** : Voir TD 3 Question 1

##### Prologue et les listes

- **Problème** : Ecrire les prédicats prologue pour trouver les nombres impairs d'une liste.

- **Solution** : 

```
/* Prédicats Prologue */

pair(X) :- number(X), 0 is X rem 2.
impair(X) :- number(X), 1 is X rem 2.

liste_impair([E],[E]):- impair(E).
liste_impair([E],[]):- pair(E).

liste_impair([T|Q],[T|R]) :- impair(T), liste_impair(Q,R).
liste_impair([T|Q],R) :- pair(T), liste_impair(Q,R).
  
```

- **Problème** : Ecrire les prédicats prologue qui parcourt cette liste, traite les saute(n) en sautant les n éléments suivants de la liste, et conserve les autres éléments.

- **Solution** : 

```
/* Prédicats Prologue */

mouton([],[]).

mouton([saute(0)|Q],R):- mouton(Q,R).
mouton([saute(X),_|Q],R):- X\=0, Y is X-1, mouton([saute(Y)|Q],R).

mouton([T|Q],[T|R]) :- mouton(Q,R).
```
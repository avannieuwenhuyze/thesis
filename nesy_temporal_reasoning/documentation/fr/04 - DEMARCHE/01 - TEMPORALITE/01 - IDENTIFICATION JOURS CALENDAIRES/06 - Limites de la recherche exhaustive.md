## Introduction

Dans le chapitre précédent, nous avons montré qu'il est possible de retrouver automatiquement les paramètres :

$$  
T  
$$

et :

$$  
\phi  
$$

à partir des données cliniques.

Une recherche exhaustive sur une grille de valeurs permet d'identifier le couple maximisant les performances du modèle.

Les expériences réalisées sur le dataset MIMIC montrent que le maximum est obtenu pour :

$$  
T = 24\ h  
$$

et :

$$  
\phi = 0  
$$

ce qui correspond exactement à la définition NHSN du jour calendaire.

Cette première validation démontre que les informations nécessaires sont bien présentes dans les données.

Cependant, cette approche présente plusieurs limitations importantes.


# Principe de la recherche exhaustive

La méthode consiste à tester un grand nombre de couples :

$$  
(T,\phi)  
$$

Pour chaque combinaison :

1. calculer :

$$  
N(T,\phi)  
$$

2. produire une prédiction :

$$  
\hat{Y}  
$$

3. mesurer la performance obtenue.

Le meilleur couple est alors défini par :

$$
(T^*,\phi^*)
=
\arg\max_{T,\phi}
Score(T,\phi)
$$


Cette approche est simple à mettre en œuvre et permet d'explorer directement le paysage de recherche.


# Première limite : explosion combinatoire

La recherche exhaustive nécessite d'évaluer toutes les combinaisons possibles.

Par exemple :

|Paramètre|Nombre de valeurs|
|---|---|
|$T$|100|
|$\phi$|100|

correspond déjà à :

10,000  évaluations.

Cette complexité augmente rapidement lorsque de nouveaux paramètres sont introduits.

Par exemple :

|   |
|---|
|Paramètre|
|$T$|
|$\phi$|
|seuil clinique|
|température|
|durée minimale|
|fenêtre temporelle|

L'espace de recherche devient alors extrêmement important.



# Deuxième limite : absence d'apprentissage

La recherche exhaustive ne construit aucun modèle.

Elle teste simplement un grand nombre de possibilités jusqu'à trouver la meilleure solution.

Autrement dit :

```
Recherche exhaustive :
paramètres → score
```

mais elle ne permet pas d'apprendre une représentation du phénomène étudié.

Le résultat obtenu est uniquement :

$$
(T^{*},\phi^{*})
$$

sans mécanisme de généralisation.


# Troisième limite : absence de différentiabilité

La recherche exhaustive repose sur une succession de tests indépendants.

Aucune fonction de perte n'est optimisée.

Aucun gradient n'est calculé.

Par conséquent, cette approche ne peut pas être intégrée directement dans un pipeline d'apprentissage profond.

Il devient notamment impossible de :

- partager une fonction de perte ;
- entraîner plusieurs modules simultanément ;
- apprendre conjointement des représentations temporelles et structurelles.

Cette limitation est particulièrement importante dans notre contexte où l'objectif final consiste à intégrer l'information temporelle dans un graphe patient analysé par un GNN.


# Quatrième limite : difficulté d'extension

Dans le cas NHSN, nous avons cherché :

$$  
(T,\phi)  
$$

car nous connaissions déjà la structure générale du problème.

Cependant, dans un contexte plus complexe, d'autres paramètres pourraient être inconnus :

$$  
(T,\phi,k)  
$$

où :

$$  
k  
$$

représente par exemple un seuil temporel.

Ou encore :

$$  
(T,\phi,k,\theta)  
$$

avec :

$$  
\theta  
$$

représentant des paramètres cliniques supplémentaires.

Dans ce cas, la recherche exhaustive devient rapidement impraticable.


# Analyse du paysage de recherche

Malgré ses limites, la recherche exhaustive présente un intérêt scientifique majeur.

Elle permet de visualiser le paysage de recherche associé aux paramètres :

$$  
(T,\phi)  
$$

Les cartes de chaleur obtenues montrent :

- l'existence d'un maximum clairement identifiable ;
- la présence d'une structure organisée ;
- l'absence d'un comportement purement aléatoire.

Ces observations suggèrent que le problème possède une fonction objectif exploitable par des méthodes d'optimisation.

Autrement dit, les paramètres :

$$  
T  
$$

et :

$$  
\phi  
$$

ne sont pas récupérés par hasard.

Ils correspondent à un optimum réel du problème.

Cette propriété ouvre la voie à l'utilisation de méthodes d'apprentissage basées sur le gradient.


# Vers un modèle paramétrique

Les limitations précédentes motivent l'introduction d'un modèle paramétrique capable d'apprendre directement les paramètres temporels.

L'objectif n'est plus de tester toutes les combinaisons possibles mais de faire évoluer progressivement :

$$  
T  
$$

et :

$$  
\phi  
$$

afin de maximiser les performances observées.

Cette approche permet :

- d'éviter l'exploration exhaustive ;
- d'améliorer le passage à l'échelle ;
- de généraliser à de nouveaux paramètres ;
- d'intégrer l'apprentissage temporel dans un système plus complexe.


# Transition vers le PINN temporel

Les résultats obtenus montrent que :

1. la structure temporelle recherchée existe réellement ;
2. les paramètres optimaux peuvent être identifiés ;
3. le paysage de recherche possède un optimum exploitable.

La question suivante devient alors :

> Peut-on apprendre automatiquement les paramètres temporels $(T,\phi)$ sans explorer exhaustivement toutes les combinaisons possibles ?

Pour répondre à cette question, nous introduisons dans le chapitre suivant un modèle de découverte de paramètres temporels inspiré du Scientific Machine Learning et des Physics-Informed Neural Networks.
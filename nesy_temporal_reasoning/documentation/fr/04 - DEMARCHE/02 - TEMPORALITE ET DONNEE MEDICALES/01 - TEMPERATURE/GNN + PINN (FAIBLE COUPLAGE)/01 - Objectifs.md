# Objectifs du système à couplage faible PINN → GNN

## Introduction

L'architecture développée dans cette première phase de recherche repose sur un couplage faible entre deux mécanismes complémentaires :

```text
Données cliniques

        ↓

PINN

        ↓

Connaissance temporelle

        ↓

Ontologie enrichie

        ↓

GNN

        ↓

Raisonnement clinique
```

Dans cette architecture, le modèle temporel et le modèle relationnel sont entraînés indépendamment.

Le PINN produit une connaissance temporelle qui est ensuite intégrée dans le graphe utilisé par le GNN.

Cette séparation volontaire permet d'étudier individuellement le rôle de chaque composant tout en analysant leur contribution au raisonnement global.


# Objectif médical

L'objectif médical n'est pas uniquement de prédire la présence d'une infection urinaire associée aux cathéters (CAUTI).

L'objectif est de comprendre comment des informations cliniques dispersées dans le temps peuvent être combinées afin de reconstruire un raisonnement cohérent avec les critères NHSN.

Les règles NHSN reposent sur plusieurs types de connaissances :

- temporelles ;
    
- cliniques ;
    
- microbiologiques ;
    
- logiques.
    

Une partie de ces connaissances n'est pas directement observable dans les données.

L'objectif médical du système est donc de reconstruire progressivement les éléments nécessaires à ce raisonnement plutôt que de simplement reproduire une décision finale.

Dans cette perspective, le système cherche à répondre à la question suivante :

```text
Comment retrouver les contraintes
utilisées par les experts médicaux
à partir des seules observations
cliniques disponibles ?
```

La prédiction de la CAUTI devient alors un moyen d'évaluer la qualité des connaissances apprises plutôt qu'une finalité en soi.


# Objectif informatique

L'objectif informatique consiste à étudier comment une connaissance temporelle implicite peut être découverte puis exploitée dans un processus de raisonnement relationnel.

Cette problématique est directement issue des défis identifiés dans l'état de l'art du raisonnement neuro-symbolique temporel.

Plus précisément, le système doit répondre à trois questions fondamentales :

### 1. Découverte d'une connaissance temporelle

Peut-on apprendre automatiquement une information temporelle qui n'est pas directement disponible dans les données ?

Le premier composant du système est chargé d'estimer une représentation temporelle interprétable à partir des événements observés.

### 2. Intégration dans une représentation relationnelle

Une fois cette connaissance obtenue, comment l'intégrer dans une représentation structurée du domaine médical ?

L'information apprise doit pouvoir être ajoutée au graphe sans dégrader sa cohérence sémantique.


### 3. Reconstruction d'un raisonnement

Le système est-il capable d'utiliser cette nouvelle connaissance afin de retrouver des contraintes proches de celles utilisées par les experts ?

Par exemple :

```text
Durée > seuil

ET

Température > seuil
```

sans que ces règles soient explicitement fournies.


# Rôle du PINN dans le couplage faible

Dans cette première architecture, le PINN joue le rôle d'un mécanisme de découverte de connaissances.

Son objectif n'est pas de prédire une CAUTI.

Son objectif est de produire une représentation temporelle exploitable à partir des données disponibles.

Le PINN agit donc comme un générateur de connaissances intermédiaires.

```text
Observations temporelles

        ↓

PINN

        ↓

Connaissance temporelle
```

Cette connaissance devient ensuite une nouvelle information disponible pour le système.


# Rôle du GNN dans le couplage faible

Le GNN intervient dans un second temps.

Son objectif est d'exploiter le graphe enrichi afin de reconstruire des relations entre les différentes informations présentes dans le domaine médical.

Le modèle doit déterminer quelles combinaisons de connaissances sont utiles à la décision.

Le GNN agit ainsi comme un mécanisme de raisonnement relationnel.

```text
Graphe enrichi

        ↓

GNN

        ↓

Relations pertinentes

        ↓

Décision clinique
```


# Pourquoi un couplage faible ?

Le choix d'un couplage faible répond à un objectif méthodologique.

Avant d'envisager un apprentissage conjoint, il est nécessaire de comprendre :

- ce que le modèle temporel apprend ;
    
- ce que le modèle relationnel apprend ;
    
- comment l'information circule entre les deux composants.
    

Cette séparation permet :

- d'analyser les connaissances produites ;
    
- d'évaluer leur utilité ;
    
- d'étudier leur impact sur le raisonnement final.
    

Le couplage faible constitue ainsi une étape expérimentale permettant de valider les hypothèses fondamentales du projet.


# Positionnement dans la feuille de route scientifique

Cette architecture répond directement à plusieurs défis identifiés dans l'état de l'art.

|Défi|Réponse apportée|
|---|---|
|Découverte de connaissances temporelles|PINN|
|Représentation explicite des connaissances|Ontologie enrichie|
|Intégration neuro-symbolique|Enrichissement du graphe|
|Raisonnement relationnel|GNN|
|Interprétabilité|Analyse des connaissances apprises|

Le système constitue ainsi une première étape vers une architecture neuro-symbolique temporelle plus intégrée.


# Limite actuelle

Dans cette première version, le PINN et le GNN sont entraînés séparément.

Le flux d'information est unidirectionnel :

```text
PINN

   ↓

Connaissance temporelle

   ↓

Graphe enrichi

   ↓

GNN
```

Le GNN bénéficie des connaissances produites par le PINN mais n'influence pas leur apprentissage.

Cette architecture reste donc un système à couplage faible.


# Perspective

L'étape suivante consistera à passer d'un simple transfert de connaissances à un apprentissage conjoint.

L'objectif sera alors de permettre au raisonnement relationnel d'influencer directement la construction des connaissances temporelles.

Le système évoluera vers une architecture :

```text
PINN

   ↕

GNN
```

où découverte de connaissances et raisonnement seront optimisés simultanément.

Cette évolution constitue la continuité naturelle de la feuille de route identifiée dans l'état de l'art du raisonnement neuro-symbolique temporel.
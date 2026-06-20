# Construction de l'ontologie

## Introduction

L'ontologie constitue le socle conceptuel de l'ensemble du projet.

Son objectif est de représenter explicitement les connaissances nécessaires à l'identification des infections urinaires associées aux cathéters (CAUTI) selon les définitions du NHSN.

Afin de garantir la cohérence de cette représentation, la construction de l'ontologie ne s'est pas limitée à une simple liste de concepts médicaux. Elle s'appuie sur une démarche méthodologique inspirée de l'approche proposée par Grüninger & Fox pour l'ingénierie ontologique.

[[P0-grninger95methodology.pdf]]

L'objectif est de passer progressivement :

```text
Connaissances métier

        ↓

Questions de compétence

        ↓

Concepts

        ↓

Relations

        ↓

Ontologie formelle
```


# 1. Définition du domaine

Le domaine étudié est celui de la surveillance des infections urinaires associées aux cathéters (CAUTI).

Les règles NHSN reposent sur la combinaison de plusieurs dimensions :

- observations cliniques ;
    
- résultats microbiologiques ;
    
- événements temporels ;
    
- règles de surveillance.
    

L'analyse du domaine a mis en évidence une caractéristique importante :

> Une CAUTI ne correspond pas à un événement unique mais à un raisonnement construit à partir d'un ensemble de contraintes interdépendantes.

Cette observation a servi de point de départ à la modélisation ontologique.


# 2. Élaboration des Questions de Compétence

La première étape consiste à définir les questions auxquelles l'ontologie devra être capable de répondre.

Ces questions sont appelées Questions de Compétence (Competency Questions).

Elles représentent les exigences fonctionnelles du système.

Exemples :

```text
Le patient possède-t-il un cathéter depuis au moins deux jours ?

Le cathéter est-il présent au moment du Date of Event ?

La culture urinaire est-elle valide ?

Le patient satisfait-il les critères d'une CAUTI ?
```

Quarante Questions de Compétence ont été définies afin de couvrir les principaux mécanismes décisionnels utilisés dans les définitions NHSN.


# 3. Identification des grands piliers fonctionnels

L'analyse des Questions de Compétence a permis d'identifier quatre grandes dimensions du domaine.

```text
Expositions aux risques

Preuves microbiologiques

Signes cliniques et temporalité

Contexte et diagnostic différentiel
```

Ces piliers servent à vérifier que l'ontologie couvre l'ensemble des connaissances nécessaires au raisonnement clinique.

Ils constituent un outil d'analyse des besoins plutôt qu'une structure ontologique définitive.


# 4. Extraction des concepts du domaine

Une fois les Questions de Compétence définies, les termes utilisés dans ces questions ont été extraits de manière systématique.

Cette extraction a été réalisée sans interprétation préalable.

L'objectif était de capturer l'ensemble du vocabulaire mobilisé dans les scénarios NHSN.

Les termes identifiés incluent notamment :

```text
Patient

Catheter

Body Temperature

Urine Culture

Date of Event

Infection Window Period

Hospital Unit

CAUTI
```

Cette étape produit un inventaire lexical brut qui servira de base à la conceptualisation.


# 5. Structuration modulaire

Les termes extraits ont ensuite été regroupés en modules conceptuels cohérents.

Les principaux modules retenus sont :

```text
Contexte hospitalier

Exposition au dispositif

Microbiologie

Manifestations cliniques

Épisodes infectieux

Temporalité
```

Contrairement aux piliers fonctionnels, ces modules correspondent à une organisation ontologique du domaine.

Ils décrivent la structure conceptuelle de l'ontologie et les relations entre les différentes entités.


# 6. Typologisation ontologique

Chaque concept a ensuite été caractérisé selon sa nature.

Plusieurs catégories ont été utilisées :

```text
Classe du domaine

Événement

État

Paramètre numérique

Entité temporelle

Règle de surveillance
```

Cette étape permet de distinguer clairement :

```text
Le monde réel

        ↕

Les règles NHSN
```

Par exemple :

```text
Patient
```

est une entité du domaine.

Alors que :

```text
CAUTI Rule
```

représente une règle normative de surveillance.

Cette distinction est essentielle pour éviter de confondre données observées et connaissances réglementaires.


# 7. Définition des relations

Une fois les concepts identifiés, les relations ont été définies.

Chaque propriété est décrite par :

- son domaine ;
    
- son range ;
    
- son intention sémantique.
    

Exemples :

```text
hasCatheter

hasTemperature

hasUrineCulture

occursDuring

hasDateOfEvent
```

À ce stade, les relations décrivent uniquement la structure du domaine.

Les règles NHSN elles-mêmes ne sont pas encore représentées sous forme d'axiomes logiques.

Cette étape constitue une phase pré-axiomatique.


# 8. Représentation explicite du temps

Une caractéristique importante du domaine CAUTI est la présence de nombreuses contraintes temporelles.

Afin de représenter explicitement ces notions, l'ontologie s'appuie sur OWL-Time.

Cette ontologie fournit les concepts nécessaires pour représenter :

```text
Instants

Intervalles

Durées

Relations temporelles
```

L'utilisation d'OWL-Time permet de modéliser formellement des notions telles que :

```text
Date of Event

Infection Window Period

Repeat Infection Timeframe

Durée de cathétérisation
```

Cette approche est détaillée dans la publication :

> A Conceptual Model for Discovering Implicit Temporal Knowledge in Clinical Data


# 9. Une ontologie évolutive

L'ontologie développée dans ce projet n'est pas considérée comme un artefact figé.

Elle évolue au fur et à mesure des expérimentations.

De nouveaux concepts peuvent être ajoutés lorsque de nouvelles hypothèses de recherche émergent.

Cette évolution progressive permet à l'ontologie de rester alignée avec les besoins scientifiques du projet tout en conservant une cohérence conceptuelle globale.


# Conclusion

La construction de l'ontologie repose sur une démarche méthodologique structurée inspirée de Grüninger & Fox.

Cette démarche conduit progressivement :

```text
Questions de compétence

        ↓

Concepts

        ↓

Relations

        ↓

Ontologie
```

L'ontologie obtenue constitue une représentation explicite du domaine CAUTI intégrant les dimensions cliniques, microbiologiques et temporelles nécessaires au raisonnement étudié.

Elle fournit ainsi le cadre conceptuel utilisé par l'ensemble des travaux présentés dans cette thèse.
# Définition formelle d'une infection urinaire associée à un cathéter (CAUTI)

## 1. Introduction

Une ==Catheter-Associated Urinary Tract Infection (CAUTI)== est une infection urinaire associée à la présence récente d'un cathéter urinaire.

Dans le cadre du NHSN, une CAUTI ne constitue pas une nouvelle catégorie d'infection distincte mais correspond à une infection urinaire (UTI) satisfaisant simultanément :

- les critères diagnostiques d'une infection urinaire ;
- les critères d'association au cathéter.

La détection d'une CAUTI nécessite donc la combinaison de contraintes :

- temporelles ;
- cliniques ;
- microbiologiques.

# 2. Structure générale de décision

Une CAUTI peut être définie comme l'intersection de deux ensembles de conditions :

==CAUTI = UTI ∩ DeviceAssociation==

Autrement dit :

1. le patient doit satisfaire la définition NHSN d'une infection urinaire ;
2. cette infection doit être associée à un cathéter urinaire selon les règles NHSN.


# 3. Contraintes temporelles

Une infection ne peut être considérée comme une CAUTI que si :

- le cathéter est présent depuis plus de deux jours calendaires ;
- il est encore présent au DOE ;

ou

- il a été retiré la veille du DOE.

Ces contraintes permettent d'établir un lien temporel entre l'exposition au dispositif et l'apparition de l'infection.


# 4. Types de CAUTI

Le NHSN distingue deux catégories principales :

## 4.1 Symptomatic Urinary Tract Infection (SUTI)

La SUTI correspond à une infection urinaire symptomatique.

La définition repose sur :

- une culture urinaire positive ;
- au moins un signe ou symptôme compatible.

Parmi les symptômes possibles :

- fièvre ;
- dysurie ;
- urgence mictionnelle ;
- fréquence urinaire augmentée ;
- douleur sus-pubienne ;
- douleur costo-vertébrale.


## 4.2 Asymptomatic Bacteremic Urinary Tract Infection (ABUTI)

L'ABUTI correspond à une infection urinaire sans symptôme urinaire documenté.

La définition repose sur :

- une culture urinaire positive ;
- une hémoculture positive contenant le même microorganisme.

Cette catégorie permet d'identifier certaines infections urinaires sévères malgré l'absence de symptomatologie urinaire classique.


# 5. Vision algorithmique

La logique NHSN peut être résumée par l'algorithme suivant :

SI

```
AssociationCathéter = Vrai
```

ET

```
(SUTI = Vrai
 OU
 ABUTI = Vrai)
```

ALORS

```
CAUTI = Vrai
```

SINON

```
CAUTI = Faux
```



# 6. Décomposition des connaissances

L'analyse de cette définition met en évidence quatre familles de connaissances :

## Connaissances temporelles

- jours calendaires ;
- date of event ;
- durée de présence du cathéter.

## Connaissances cliniques

- température ;
- dysurie ;
- urgence mictionnelle ;
- fréquence urinaire ;
- douleurs urinaires.

## Connaissances microbiologiques

- culture urinaire ;
- identification des microorganismes ;
- hémocultures.

## Connaissances logiques

- combinaison des contraintes ;
- satisfaction simultanée des critères ;
- décision finale CAUTI.



# 7. Conséquences pour notre architecture

Cette décomposition suggère naturellement une séparation des rôles au sein de l'architecture proposée.

Le module temporel est chargé d'apprendre les contraintes liées au temps :

- jours calendaires ;
- durée d'exposition ;
- position temporelle des événements.

Le graphe clinique représente les entités médicales :

- patient ;
- cathéter ;
- température ;
- culture ;
- microorganismes.
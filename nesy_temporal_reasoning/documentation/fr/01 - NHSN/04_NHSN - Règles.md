# Construction progressive de la définition d'une CAUTI

## 1. Introduction

Une infection urinaire associée à un cathéter (Catheter-Associated Urinary Tract Infection – CAUTI) n'est pas définie par un unique critère.

La définition NHSN résulte de l'accumulation progressive de plusieurs contraintes :

- temporelles ;
    
- cliniques ;
    
- microbiologiques ;
    
- logiques.
    

L'objectif de ce document est de décomposer cette définition en une succession d'étapes élémentaires permettant de comprendre comment une situation clinique évolue progressivement vers une CAUTI déclarable selon les critères NHSN.


# Niveau 0 : Patient hospitalisé

Point de départ.

```text
Patient hospitalisé
```

Aucune hypothèse concernant une infection n'est encore formulée.


# Niveau 1 : Présence d'un cathéter urinaire

Premier élément observé :

```text
Cathéter urinaire présent
```

À ce stade :

- aucune infection n'est identifiée ;
    
- aucune relation causale n'est établie.
    

La simple présence d'un cathéter ne permet pas de conclure à une CAUTI.


# Niveau 2 : Exposition prolongée au cathéter

Le NHSN exige que le cathéter soit présent depuis plus de deux jours calendaires.

```text
catheter_days > 2
```

Cette condition est nécessaire pour pouvoir considérer ultérieurement qu'une infection est associée au dispositif.

À ce stade :

```text
Patient exposé au cathéter
```


# Niveau 3 : Apparition d'un signe clinique compatible

Chez les patients âgés de plus d'un an, au moins un des signes ou symptômes suivants peut contribuer à la définition d'une infection urinaire :

```text
Fièvre > 38°C
Urgence mictionnelle
Fréquence urinaire augmentée
Dysurie
Douleur sus-pubienne
Douleur costo-vertébrale
```

Chez les patients âgés d'un an ou moins :

```text
Fièvre
Hypothermie
Apnée
Bradycardie
Léthargie
Vomissements
Douleur sus-pubienne
```

À ce stade :

```text
UTI_possible
```

L'infection reste seulement suspectée.


# Niveau 4 : Détermination du Date of Event (DOE)

Le Date of Event correspond à la date du premier élément utilisé pour satisfaire un critère NHSN.

Exemple :

```text
Fièvre observée le 12 janvier

⇒ DOE = 12 janvier
```

Le DOE devient alors la référence temporelle utilisée pour l'ensemble des vérifications ultérieures.


# Niveau 5 : Vérification de l'association au cathéter

Le NHSN exige que le cathéter soit :

```text
Présent le jour du DOE
```

ou

```text
Retiré la veille du DOE
```

La règle devient :

```text
catheter_days > 2

ET

(
Cathéter présent au DOE
OU
Cathéter retiré la veille du DOE
)
```

À ce stade :

```text
Association au cathéter valide
```


# Niveau 6 : Mise en évidence microbiologique

Une infection urinaire nécessite une culture urinaire satisfaisant les critères NHSN.

Conditions :

```text
Nombre d'espèces ≤ 2
```

ET

```text
Au moins une bactérie
```

ET

```text
Concentration ≥ 10⁵ CFU/mL
```

La présence d'une culture conforme apporte une preuve microbiologique de l'infection.


# Niveau 7 : Cohérence temporelle de l'épisode infectieux

Les éléments utilisés doivent appartenir à la même Infection Window Period (IWP).

L'IWP correspond à :

```text
3 jours avant
+
jour du test diagnostique
+
3 jours après
```

Tous les signes, symptômes et résultats microbiologiques utilisés doivent être compatibles avec cette fenêtre temporelle.


# Niveau 8 : Validation d'une UTI

Lorsque les critères cliniques, microbiologiques et temporels sont satisfaits :

```text
UTI = Vrai
```

Le patient présente alors une infection urinaire répondant à la définition NHSN.


# Niveau 9 : Transformation de l'UTI en CAUTI

La définition finale est obtenue en combinant :

```text
UTI
```

avec

```text
Association valide au cathéter
```

Ce qui donne :

```text
CAUTI
```


# Niveau 10 : Classification de la CAUTI

Une fois la CAUTI identifiée, deux catégories principales sont distinguées.

## SUTI (Symptomatic Urinary Tract Infection)

Présence :

- d'une culture urinaire valide ;
    
- d'au moins un signe ou symptôme compatible.
    

Exemples :

```text
Fièvre > 38°C
Dysurie
Urgence mictionnelle
Douleur sus-pubienne
```


## ABUTI (Asymptomatic Bacteremic Urinary Tract Infection)

Présence :

- d'une culture urinaire valide ;
    
- d'une hémoculture positive ;
    
- du même microorganisme dans les deux prélèvements ;
    
- absence des critères permettant de classer l'événement comme SUTI.
    

# Résumé de la construction

```text
Patient hospitalisé

→ Cathéter présent

→ Cathéter > 2 jours calendaires

→ Apparition d'un signe clinique

→ Détermination du DOE

→ Cathéter présent au DOE
   ou retiré la veille

→ Culture urinaire valide

→ Cohérence temporelle (IWP)

→ UTI

→ Association au cathéter

→ CAUTI

→ SUTI ou ABUTI
```

Cette décomposition met en évidence la nature cumulative de la définition NHSN d'une CAUTI. Une infection urinaire associée à un cathéter n'est pas déterminée par un seul critère mais par la satisfaction progressive d'un ensemble de contraintes interdépendantes.
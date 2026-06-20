# Confrontation entre les seuils appris par le GNN et les seuils métiers NHSN

## Objectif

L'expérience précédente a permis d'extraire une représentation logique du comportement du GNN à partir de ses propres seuils appris.

Le modèle avait identifié les seuils suivants :

```text
Température > 37.20°C

N̂ > 1.13
```

Une nouvelle question apparaît alors :

> Les seuils appris par le GNN sont-ils réellement ceux qui expliquent le mieux les données cliniques ?

Autrement dit :

```text
Seuils appris par le GNN

        VS

Seuils métiers NHSN
```

Cette expérience vise à mettre en concurrence les connaissances découvertes par le modèle et les connaissances utilisées par les experts médicaux.

L'objectif n'est plus d'expliquer le comportement du GNN mais de comprendre quelle famille de règles possède le plus fort pouvoir explicatif.

# Construction de l'espace de confrontation

Deux familles de règles sont introduites dans le système.

## Règles apprises par le GNN

Issues directement des paramètres optimisés durant l'apprentissage :

```text
Température > 37.20°C

N̂ > 1.13
```

Ces seuils correspondent aux connaissances découvertes automatiquement par le réseau.

Les variables logiques associées sont :

```text
temp_gt_37_20

nhat_gt_1_13
```

## Règles métiers NHSN

Issues des critères utilisés pour la définition des CAUTI :

```text
Température > 38.00°C

N̂ > 2.00
```

Les variables logiques associées sont :

```text
temp_gt_38_00

nhat_gt_2_00
```

Chaque patient est alors représenté simultanément par les quatre variables :

```text
temp_gt_37_20

temp_gt_38_00

nhat_gt_1_13

nhat_gt_2_00
```

L'ensemble constitue un espace de confrontation entre connaissances apprises et connaissances expertes.

# Principe de la compétition

L'algorithme RIPPER reçoit l'ensemble des variables.

Il n'est informé ni de leur origine ni de leur signification clinique.

Son unique objectif consiste à trouver :

```text
La règle la plus simple

et

la plus prédictive
```

Les variables sont donc mises en compétition.

Par exemple :

```text
temp_gt_37_20

VS

temp_gt_38_00
```

Si deux variables expliquent la même information, RIPPER conserve uniquement celle qui améliore le plus la qualité globale de la règle.

Le même mécanisme est appliqué aux seuils temporels.

# Deux analyses complémentaires

Afin d'étudier la relation entre le GNN et la réalité clinique, deux expériences sont réalisées.

## Expérience 2.A : Explication du label réel

Dans cette première analyse, la cible correspond à la vérité terrain :

```text
Patient

↓

Label réel CAUTI
```

RIPPER cherche alors la règle expliquant le mieux les données cliniques observées.

Le résultat obtenu est :

```text
[temp_gt_38_00 = 1
 ^
 nhat_gt_2_00 = 1]
```

soit :

```text
Température > 38°C

ET

N̂ > 2
```

## Expérience 2.B : Explication du comportement du GNN

Dans cette seconde analyse, la cible n'est plus le label réel mais la décision du GNN.

```text
Patient

↓

Prédiction GNN
```

RIPPER cherche alors à reconstruire la logique décisionnelle du modèle.

Le résultat obtenu est :

```text
[temp_gt_38_00 = 1
 ^
 nhat_gt_2_00 = 1]
```

soit exactement la même règle.

# Résultat principal

Les deux analyses conduisent à la même conclusion :

```text
Température > 38°C

ET

N̂ > 2
```

Cette règle explique à la fois :

- la réalité clinique ;
    
- les prédictions du GNN.
    

La concordance observée est particulièrement forte.

# Interprétation

À première vue, ce résultat pourrait laisser penser que le GNN a directement appris les seuils NHSN.

Ce n'est pourtant pas ce que montre l'expérience.

Le modèle a effectivement appris :

```text
37.20°C

1.13
```

mais lorsque ces seuils sont mis en concurrence avec les critères NHSN, RIPPER sélectionne systématiquement :

```text
38°C

2 jours
```

La raison est simple.

==Les seuils NHSN produisent une séparation plus nette entre patients positifs et négatifs.==

==Ils constituent donc une représentation logique plus parcimonieuse du problème.==

# Ce que cette expérience valide

Cette expérience valide plusieurs points importants.

## Validation de la cohérence globale du GNN

Le comportement du modèle reste cohérent avec les connaissances médicales utilisées pour construire le dataset.

Le réseau ne semble pas s'appuyer sur des artefacts ou sur des corrélations non pertinentes.

La logique globale reconstruite est compatible avec les définitions NHSN.

## Validation du passage Network → Logic

L'expérience démontre qu'il est possible :

```text
GNN

↓

Décisions

↓

Règles logiques
```

La logique du modèle peut donc être auditée et comparée à des connaissances expertes.

## Validation de la méthodologie

La procédure complète :

```text
GNN

↓

Extraction des seuils

↓

Discrétisation

↓

RIPPER

↓

Règle logique
```

produit des résultats cohérents et interprétables.

# Ce que cette expérience ne valide pas

==Il est important de souligner que cette expérience ne démontre pas que :==

```text
Le GNN a redécouvert les règles NHSN
```

En réalité, les seuils appris par le modèle sont différents :

```text
37.20°C

1.13
```

L'expérience montre simplement que les règles NHSN expliquent mieux le dataset que les seuils appris lorsque les deux familles de règles sont mises en compétition.

Cette nuance est essentielle pour l'interprétation scientifique des résultats.

# Conclusion

La confrontation entre les connaissances apprises par le GNN et les connaissances métier conduit systématiquement à la sélection de la règle :

```text
Température > 38°C

ET

N̂ > 2 jours
```

Cette règle explique à la fois :

- les labels réels ;
    
- les décisions du GNN.
    

Le résultat montre que la logique globale du modèle reste cohérente avec les critères médicaux utilisés pour construire le dataset.

Cette expérience constitue une étape importante dans l'analyse explicable du modèle.

Elle permet de comparer directement les connaissances découvertes automatiquement par le réseau avec les connaissances formalisées par les experts médicaux.
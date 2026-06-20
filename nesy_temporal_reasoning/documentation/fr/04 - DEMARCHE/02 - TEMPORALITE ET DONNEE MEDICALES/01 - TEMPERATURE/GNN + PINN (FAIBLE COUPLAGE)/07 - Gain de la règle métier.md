# Pourquoi la règle métier l'emporte-t-elle ? Limites du dataset et intérêt du GNN

## Introduction

Les deux expériences précédentes ont conduit à un résultat surprenant.

L'analyse des paramètres du GNN a montré que le modèle apprend les seuils :

```text
Température > 37.20°C

N̂ > 1.13
```

Pourtant, lorsque ces seuils sont mis en concurrence avec les critères NHSN, l'algorithme RIPPER sélectionne systématiquement :

```text
Température > 38°C

ET

N̂ > 2 jours
```

Cette observation soulève une question importante :

> Si les règles métiers expliquent mieux les données, quel est alors l'intérêt du GNN ?

Pour répondre à cette question, il est nécessaire d'analyser la nature même du dataset utilisé dans cette étude.

# L'illusion du dataset

## Un circuit fermé

Le dataset utilisé dans cette expérimentation possède une caractéristique particulière.

Les labels positifs et négatifs ont été construits à partir des critères NHSN.

Autrement dit :

```text
Règles NHSN

        ↓

Construction du dataset

        ↓

Labels

        ↓

Apprentissage
```

Les données utilisées par le système sont donc déjà structurées par les règles que l'on cherche à retrouver.

Lorsque RIPPER extrait :

```text
Température > 38°C

ET

N̂ > 2 jours
```

il retrouve simplement la logique utilisée pour construire le dataset.

Dans ce contexte, le résultat est attendu.

Il ne s'agit pas d'une découverte.

Il s'agit d'une validation.

## Ce que valide réellement l'expérience

L'expérience montre que :

```text
GNN

↓

Extraction logique

↓

Règle NHSN
```

est possible.

La méthodologie est donc capable de retrouver une connaissance connue.

Cette étape joue le rôle d'un test de cohérence.

Avant de chercher à découvrir de nouvelles connaissances, il est nécessaire de vérifier que le système est capable de reconstruire celles déjà établies.

# Pourquoi le GNN reste intéressant

Le résultat précédent pourrait laisser penser que le GNN est inutile.

Cette conclusion serait pourtant incorrecte.

Le rôle du GNN n'est pas de remplacer une règle médicale déjà connue.

Son rôle est d'apprendre dans des situations où aucune règle n'est disponible.

# Découverte de nouvelles connaissances

## Lorsque les experts ne disposent pas de règle

Les critères NHSN existent pour les CAUTI.

Mais de nombreux problèmes médicaux ne disposent pas de règles aussi clairement définies.

Par exemple :

```text
Infections liées à d'autres dispositifs

Complications postopératoires

Dégradation hémodynamique

Évolution vers un sepsis
```

Dans ces situations, il n'existe pas toujours de règle du type :

```text
Si A et B alors maladie
```

Les connaissances sont souvent incomplètes ou controversées.

Un moteur de règles classique ne peut pas inventer une règle qu'on ne lui a jamais fournie.

Le GNN, en revanche, peut explorer les interactions présentes dans les données.

# Découverte de relations complexes

Considérons un exemple hypothétique.

Le système pourrait découvrir que le risque augmente lorsque :

```text
Plaquettes ↓ 15 %

ET

Créatinine ↑ 1.4

ET

Température > 37.6°C
```

Aucune règle métier ne décrit nécessairement cette combinaison.

Elle pourrait pourtant être présente dans les données.

Le GNN peut apprendre cette relation.

Une fois découverte, RIPPER ou un moteur ILP peut la transformer en règle explicable.

Le processus devient alors :

```text
Données

↓

GNN

↓

Connaissance implicite

↓

RIPPER

↓

Règle explicable
```

Le GNN joue le rôle de découvreur de connaissances.

Le moteur logique joue le rôle de traducteur.

# Vers une détection plus précoce

Une autre observation intéressante concerne les seuils appris :

```text
37.20°C

1.13
```

Ces seuils apparaissent avant les seuils NHSN :

```text
38.00°C

2.00
```

Le modèle semble donc commencer à réagir avant que les critères cliniques formels ne soient atteints.

Cela ne démontre pas encore une capacité de prédiction précoce.

En revanche, cela suggère que le réseau détecte une évolution progressive du risque.

Cette hypothèse devra être vérifiée sur des données longitudinales réelles.

Si elle est confirmée, le système pourrait être utilisé comme mécanisme d'alerte précoce.

# Gestion du flou et des données imparfaites

Les règles symboliques sont intrinsèquement discrètes.

Par exemple :

```text
37.99°C

↓

Pas de fièvre
```

et

```text
38.00°C

↓

Fièvre
```

La transition est brutale.

Le monde réel est rarement aussi net.

Les mesures sont bruitées.

Les données peuvent être incomplètes.

Les capteurs peuvent être imprécis.

Les réseaux neuronaux permettent une représentation continue de l'information.

Dans notre cas :

```text
37.2°C

↓

Activation faible

37.6°C

↓

Activation modérée

38.0°C

↓

Activation forte
```

Le système conserve ainsi une information progressive qui disparaît lors de la discrétisation logique.

Cette propriété constitue un avantage important des approches neuronales.

# Positionnement dans la feuille de route scientifique

Cette expérimentation ne doit pas être interprétée comme une tentative de remplacer les règles NHSN.

Elle constitue une validation méthodologique.

L'objectif est de démontrer que le pipeline :

```text
GNN

↓

Connaissance implicite

↓

Règle logique
```

fonctionne correctement.

Une fois cette étape validée, la même méthodologie pourra être appliquée à des problèmes pour lesquels aucune règle experte n'existe.

C'est dans ce contexte que le potentiel scientifique du système devient réellement intéressant.

# Conclusion

Les règles NHSN l'emportent dans cette expérimentation parce qu'elles ont servi à construire les labels du dataset.

Le résultat obtenu est donc cohérent avec la structure même des données.

Cette observation ne diminue pas l'intérêt du GNN.

Au contraire.

Elle valide la capacité du pipeline à reconstruire automatiquement une connaissance connue avant de l'utiliser pour explorer des domaines où aucune connaissance explicite n'est disponible.

Dans cette perspective, le GNN n'est pas conçu pour remplacer les experts.

Il constitue un outil permettant de découvrir des connaissances potentielles à partir de données complexes et hétérogènes.

Le moteur logique intervient ensuite pour transformer ces connaissances implicites en règles interprétables par les experts médicaux.

Cette complémentarité entre apprentissage neuronal et formalisation symbolique constitue l'un des objectifs centraux du raisonnement neuro-symbolique étudié dans cette thèse.
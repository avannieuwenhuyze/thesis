# Notion de frontière temporelle

## Introduction

L'analyse des résultats obtenus avec le modèle basé uniquement sur la durée a mis en évidence une limitation importante.

==Bien que la durée de cathétérisation constitue un facteur explicatif pertinent, elle ne permet pas d'expliquer l'ensemble des décisions observées.==

L'étude des faux négatifs révèle notamment l'existence d'intervalles relativement courts qui sont néanmoins classés positivement dans les données de référence.

Ces observations suggèrent que la décision ne dépend pas uniquement de la quantité de temps écoulée, mais également de la manière dont cet intervalle est positionné dans le calendrier.


# Une première intuition

Considérons les deux intervalles suivants.

## Cas A

```text
03/08 21:13
↓
05/08 14:00
```

Durée :

$$
\Delta t = 40.78\,\mathrm{h}
$$

Jours traversés :

```text
03/08
04/08
05/08
```

Soit :

$N = 3$ jours calendaires.


## Cas B

```text
03/08 08:00
↓
04/08 23:00
```

Durée :

$$
\Delta t = 39\,\mathrm{h}
$$

Jours traversés :

```text
03/08
04/08
```

Soit :

$N = 2$ jours calendaires.


## Observation

Les deux intervalles possèdent des durées comparables :

$$
40.78\,\mathrm{h}
\approx
39\,\mathrm{h}
$$

Pourtant :

$$
N_A = 3
$$

et

$$
N_B = 2
$$

Cette différence ne peut pas être expliquée par la durée seule.


# Apparition d'une nouvelle variable

Les exemples précédents suggèrent que le phénomène observé dépend non seulement du temps écoulé ==mais également du nombre de changements de jour rencontrés au cours de l'intervalle.==

Autrement dit, la variable importante semble être :

$$
N
$$

où :

$$
N = \text{nombre d'unités temporelles traversées}
$$

La question devient alors :

> Qu'est-ce qui détermine le passage d'une unité temporelle à la suivante ?


# Définition d'une frontière temporelle

Une frontière temporelle est un instant particulier qui sépare deux unités temporelles consécutives.

==Dans le cas des jours calendaires, cette frontière correspond au passage de minuit.==

$$
23:59:59
\rightarrow
00:00:00
$$

Chaque franchissement de cette frontière provoque un changement de jour calendaire.


# Exemple

Considérons l'intervalle suivant :

```text
03/08 21:13
↓
05/08 14:00
```

Le temps s'écoule de la manière suivante :

```text
03/08 21:13
      ↓
04/08 00:00
      ↓
05/08 00:00
      ↓
05/08 14:00
```

Les instants :

```text
04/08 00:00
05/08 00:00
```

correspondent à des frontières temporelles.

L'intervalle traverse donc deux frontières calendaires.


# Visualisation

```text
Jour 1       Jour 2       Jour 3

|------------|------------|

      ↑             ↑

  frontière     frontière
```

Les frontières découpent le temps continu en unités discrètes.


# Temps continu et temps discret

La durée appartient au domaine du temps continu.

Elle peut prendre une infinité de valeurs :

$$
\Delta t \in \mathbb{R}^{+}
$$

Par exemple :

$$
17.7\,h , 40.78\,h, 191.75\,h
$$


Le nombre de jours calendaires appartient quant à lui au domaine discret :

$$
N \in \mathbb{N}
$$

Par exemple :

$$
N = 1 , N=2, N=3, N=4
$$

==La décision semble donc davantage liée à une quantité discrète qu'à une quantité continue.==


# Conséquence

Deux intervalles de durée similaire (cf #observations) peuvent produire des valeurs différentes de :

$$
N
$$

où $N$ représente le nombre d'unités temporelles traversées.


# Généralisation

Les jours calendaires constituent un exemple particulier de découpage temporel.

De nombreuses autres frontières peuvent exister :

- changement d'heure ;
- début de semaine ;
- début de mois ;
- début d'année ;
- cycle biologique ;
- cycle circadien.

De manière générale, une frontière temporelle sépare deux intervalles successifs d'un même cycle.


# Conclusion

L'étude des faux négatifs ([[01 - Pourquoi la durée ne suffit pas]]) montre que la durée seule ne permet pas d'expliquer correctement les décisions observées.

Les erreurs apparaissent précisément lorsque des intervalles relativement courts traversent plusieurs jours calendaires.

Cette observation conduit à introduire la notion de frontière temporelle, c'est-à-dire les instants qui découpent le temps continu en unités discrètes.

La question suivante devient alors :

> Comment découvrir automatiquement ces frontières temporelles à partir des données sans les fournir explicitement au modèle ?

==La réponse à cette question nécessite d'introduire la notion de période temporelle.==
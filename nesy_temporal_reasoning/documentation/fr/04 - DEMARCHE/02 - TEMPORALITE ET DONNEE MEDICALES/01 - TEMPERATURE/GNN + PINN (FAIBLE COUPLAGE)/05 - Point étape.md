# Conclusion et perspectives

## Bilan des travaux réalisés

Cette recherche avait pour objectif d'explorer une problématique centrale de la surveillance des infections associées aux soins : comment apprendre automatiquement une règle clinique temporelle à partir de données médicales hétérogènes représentées sous forme de graphe.

Le cas d'étude retenu concerne les infections urinaires associées aux cathéters (CAUTI), dont la définition NHSN repose notamment sur une contrainte temporelle explicite : la présence d'un cathéter pendant plus de deux jours calendaires combinée à des critères cliniques tels qu'une température supérieure à 38°C.

L'originalité de notre approche repose sur la combinaison de deux mécanismes d'apprentissage complémentaires :

1. un modèle temporel de type PINN ;
2. un modèle relationnel de type GNN.

Contrairement à une approche supervisée classique où la règle médicale serait directement injectée dans le système, nous avons cherché à reconstruire cette règle de manière émergente à partir des données.

### Étape 1 : découverte d'une variable temporelle latente

La première contribution consiste à entraîner un PINN afin d'estimer une variable temporelle latente :

$$
\hat{N}
$$

correspondant au nombre de jours calendaires traversés entre l'insertion et le retrait du cathéter.

Cette information n'existe pas explicitement dans les données RDF initiales.

Le PINN agit donc comme un mécanisme de découverte de connaissances capable de transformer une succession d'événements temporels en une représentation clinique interprétable.

À ce stade, aucune règle CAUTI n'est fournie au modèle.

Le système apprend uniquement à reconstruire une information temporelle pertinente.

### Étape 2 : enrichissement sémantique du graphe

La variable temporelle estimée est ensuite injectée dans le graphe médical.

Le graphe n'est pas enrichi par une décision mais uniquement par une connaissance supplémentaire :

```text
Catheterization
      ↓
     N̂
```

Cette étape constitue une forme de raisonnement neuro-symbolique faible :

- le réseau neuronal découvre une connaissance ;
- cette connaissance devient un élément du graphe ;
- le système relationnel reste libre de l'utiliser ou non.

### Étape 3 : apprentissage relationnel par Graph Neural Network

Le graphe enrichi est ensuite analysé par un modèle GraphSAGE.

Le GNN ne reçoit jamais explicitement la règle :

```text
Température > 38°C
ET
N̂ > 2 jours
```

Il doit découvrir seul quelles structures du graphe permettent de différencier les patients CAUTI des patients non CAUTI.

Les résultats obtenus montrent que :

- la température seule est insuffisante ;
- la durée seule est insuffisante ;
- la combinaison des deux est nécessaire pour retrouver le comportement clinique attendu.

Cette observation constitue un premier indice que le système a appris une représentation cohérente du phénomène étudié plutôt qu'une simple corrélation statistique.

# Positionnement dans l'état de l'art

Le survey réalisé sur le raisonnement neuro-symbolique temporel met en évidence une évolution majeure du domaine.

Les travaux récents ne cherchent plus uniquement à améliorer la performance prédictive mais également à produire des raisonnements :

- explicables ;
- auditables ;
- vérifiables ;
- cohérents temporellement.

L'un des constats majeurs du survey est que le temps ne peut être réduit à une simple dimension séquentielle.

Les systèmes doivent être capables de raisonner sur :

- des trajectoires temporelles ;
- des dépendances non locales ;
- des contraintes explicites ;
- des structures évolutives.

Notre approche s'inscrit directement dans cette vision.

Le PINN ne cherche pas uniquement à prédire une valeur numérique.

Il reconstruit une variable temporelle interprétable.

Le GNN ne cherche pas uniquement à classer un patient.

Il apprend à exploiter une connaissance temporelle intégrée dans une structure relationnelle.

L'objectif n'est donc pas uniquement la prédiction mais la reconstruction d'un raisonnement clinique compatible avec les connaissances médicales existantes.


# Correspondance avec la feuille de route du survey

Le survey propose une progression des systèmes neuro-symboliques temporels selon plusieurs niveaux de maturité.

## Niveau 1 : Représentation temporelle explicite

Objectif :

```text
Apprendre des représentations temporelles interprétables.
```

État actuel :

- Atteint grâce au PINN.

Le modèle découvre une variable temporelle explicite :

$$
\hat{N}
$$

qui possède une signification clinique directe.


## Niveau 2 : Intégration neuro-symbolique

Objectif :

```text
Injecter les connaissances apprises
dans une structure symbolique.
```

État actuel :

- Atteint.

La variable temporelle est intégrée dans le graphe RDF.

Le graphe devient porteur d'une connaissance temporelle construite par apprentissage.


## Niveau 3 : Raisonnement relationnel

Objectif :

```text
Utiliser les connaissances temporelles
pour réaliser une tâche décisionnelle.
```

État actuel :

- Atteint.

Le GNN exploite les informations du graphe enrichi afin de distinguer les patients CAUTI des patients non CAUTI.

## Niveau 4 : Auditabilité du raisonnement

Objectif :

```text
Comprendre pourquoi le système
prend une décision.
```

État actuel :

- Partiellement atteint.

L'analyse des embeddings et des sous-graphes permet d'étudier le comportement du modèle.

Cependant le raisonnement ==n'est pas encore exprimé sous forme de règles symboliques explicites.==


## Niveau 5 : Extraction de connaissances symboliques

Objectif :

```text
Transformer les représentations apprises
en règles logiques explicites.
```

État actuel :

🔴 Non atteint.

C'est la prochaine étape naturelle du projet.


## Niveau 6 : Boucle neuro-symbolique fermée

Objectif :

```text
Apprentissage ↔ Raisonnement ↔ Vérification
```

État actuel :

🔴 Non atteint.

Le système actuel reste séquentiel :

```text
PINN
 ↓
N̂
 ↓
Graphe enrichi
 ↓
GNN
```

Aucun mécanisme de rétroaction n'existe encore entre les deux modèles.


# Perspectives scientifiques

## 1. Induction automatique de règles logiques

La première évolution consistera à transformer les connaissances apprises par le GNN en règles explicites.

L'objectif est de passer de :

```text
Patient → CAUTI
```

à :

```text
IF Temperature > 38
AND N̂ > 2
THEN CAUTI
```

Cette étape permettra :

- d'améliorer l'explicabilité ;
- de comparer automatiquement les règles apprises aux règles NHSN ;
- de mesurer la fidélité du modèle à la connaissance clinique.

Cette perspective correspond directement à la branche :

```text
Network → Logic
```

de la taxonomie identifiée dans le survey.

Le réseau ne sera plus uniquement prédictif.

Il deviendra producteur de connaissances symboliques.

## 2. Couplage fort PINN ↔ GNN

Le système actuel repose sur un couplage faible.

```text
PINN
 ↓
N̂
 ↓
GNN
```

La prochaine étape consiste à entraîner simultanément les deux composantes.

```text
          ┌──────────┐
          │   PINN   │
          └────┬─────┘
               │
               ▼
          Variable N̂
               │
               ▼
          ┌──────────┐
          │   GNN    │
          └────┬─────┘
               ▲
               │
      rétropropagation
```

Une fonction de coût commune permettrait au GNN d'influencer directement l'apprentissage temporel du PINN.

Le système évoluerait alors vers une architecture :

```text
Network ↔ Logic
```

telle qu'identifiée dans la feuille de route du survey.

## 3. Vérification symbolique du raisonnement

Une fois les règles extraites, celles-ci pourront être confrontées aux connaissances médicales officielles.

L'objectif sera de vérifier :

- leur cohérence ;
- leur stabilité ;
- leur conformité aux règles NHSN.

Nous passerions alors d'un système prédictif à un système capable de justifier et de vérifier ses propres raisonnements.

Cette étape répond directement aux enjeux d'auditabilité et de vérifiabilité identifiés comme centraux dans le survey.


## Vision à long terme

À terme, le système visé peut être représenté comme une boucle neuro-symbolique temporelle complète :

```text
Données cliniques
        │
        ▼
      PINN
        │
        ▼
Variable temporelle N̂
        │
        ▼
 Graphe enrichi
        │
        ▼
       GNN
        │
        ▼
 Extraction de règles
        │
        ▼
 Vérification symbolique
        │
        ▼
 Rétroaction vers PINN et GNN
```

Cette architecture dépasse la simple classification.

Elle vise la construction d'un système capable :

- d'apprendre ;
- de raisonner ;
- d'expliquer ;
- de vérifier ;
- d'améliorer ses propres connaissances.


# Conclusion générale

Cette première preuve de concept démontre qu'il est possible d'articuler apprentissage temporel, représentation relationnelle et raisonnement clinique au sein d'une même architecture.

La contribution principale ne réside pas dans la performance prédictive obtenue mais dans la démonstration qu'une connaissance temporelle latente peut être :

1. découverte automatiquement ;
2. intégrée dans un graphe médical ;
3. exploitée par un GNN ;
4. utilisée pour reconstruire une règle clinique connue.

Au regard de la feuille de route proposée dans le survey, cette recherche positionne le projet à la frontière entre les niveaux d'intégration neuro-symbolique et de raisonnement relationnel.

Les prochaines étapes:  induction de règles logiques, couplage fort PINN ↔ GNN et vérification symbolique constituent la progression naturelle vers une véritable architecture de raisonnement neuro-symbolique temporel, capable non seulement de prédire mais également d'expliquer, d'auditer et de contrôler ses propres processus décisionnels.
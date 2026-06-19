


## Courbe d'apprentissage du Weak Temporal GNN

![[Pasted image 20260619161439.png]]

Cette figure présente l'évolution conjointe de la fonction de perte sur les jeux d'entraînement (*train loss*) et de validation (*validation loss*) au cours des 30 époques d'apprentissage.

On observe une diminution progressive de la perte d'entraînement, qui passe d'environ **0.82** à **0.17**, traduisant une amélioration continue de la capacité du modèle à ajuster ses paramètres aux données d'apprentissage.

La perte de validation suit une tendance similaire et diminue de **0.50** à environ **0.10**, indiquant que les performances du modèle s'améliorent également sur des données non vues. L'absence d'écart important entre les deux courbes suggère que le modèle ne présente pas de phénomène marqué de surapprentissage (*overfitting*).

Une phase de transition apparaît entre les époques **10 et 15**, période durant laquelle les pertes chutent rapidement. Cette rupture correspond vraisemblablement au moment où le modèle commence à exploiter efficacement les représentations temporelles et thermiques apprises.

La ligne verticale pointillée indique l'**époque 28**, correspondant au meilleur modèle retenu selon la perte de validation. À cette étape, le modèle atteint son minimum de généralisation avec :

- Validation loss : **0.0969**
- Accuracy : **96.80 %**
- Precision : **91.59 %**
- Recall : **96.08 %**
- F1-score : **93.78 %**

Cette courbe montre que l'introduction des paramètres de recentrage appris (*temperature threshold* et *N̂ threshold*) stabilise fortement l'apprentissage et permet au modèle de converger vers une solution performante sans injection explicite des seuils cliniques NHSN.

## Évolution des performances du modèle au cours de l'apprentissage

![[Pasted image 20260619161613.png]]

Cette figure présente l'évolution des principales métriques de classification sur le jeu de validation au cours des 30 époques d'entraînement :

- **Accuracy** : proportion globale de prédictions correctes ;
- **Precision** : capacité à limiter les faux positifs ;
- **Recall** : capacité à détecter les cas positifs ;
- **F1-score** : compromis entre précision et rappel.

Au début de l'apprentissage, le modèle présente un comportement déséquilibré. Le rappel (*recall*) est déjà élevé (> 0.75), ce qui indique que le modèle détecte une grande partie des cas positifs, mais la précision reste faible (~0.40 à 0.50), traduisant un nombre important de faux positifs.

À partir des époques **10 à 15**, une amélioration rapide est observée sur l'ensemble des métriques. Cette phase correspond à la diminution brutale de la fonction de perte observée précédemment et marque l'émergence d'une représentation plus discriminante des informations thermiques et temporelles.

Le **F1-score**, qui constitue la métrique principale pour ce problème déséquilibré, progresse continuellement :

```text
Epoch 1  : F1 ≈ 0.61
Epoch 10 : F1 ≈ 0.71
Epoch 20 : F1 ≈ 0.90
Epoch 28 : F1 ≈ 0.94
```

L'époque **28** (ligne verticale pointillée) correspond au meilleur compromis entre précision et rappel :

- Accuracy : **96.80 %**
- Precision : **91.59 %**
- Recall : **96.08 %**
- F1-score : **93.78 %**

On observe également une stabilisation progressive des métriques après l'époque 20, signe que le modèle a convergé vers une solution robuste et qu'il ne dépend plus fortement des fluctuations stochastiques de l'entraînement.

Cette courbe confirme que l'introduction des paramètres de recentrage appris (*temperature threshold* et *N̂ threshold*) améliore significativement la capacité du modèle à identifier les cas CAUTI tout en conservant un excellent niveau de généralisation.

## Évolution des seuils de recentrage appris

![[Pasted image 20260619161928.png]]

![[Pasted image 20260619162210.png]]

![[Pasted image 20260619162028.png]]

Ces figures présente l'évolution des deux paramètres de recentrage appris automatiquement par le modèle au cours de l'entraînement :

- **Temperature threshold** : seuil de recentrage appliqué aux températures maximales ;
- **N̂ threshold** : seuil de recentrage appliqué aux durées estimées de cathétérisation.

Ces paramètres interviennent dans les transformations :

```python
temp_alpha * (temp_max - temp_threshold)

nhat_alpha * (nhat_max - nhat_threshold)
```

Contrairement à une approche basée sur des règles expertes, les seuils ne sont pas fixés a priori. Ils sont optimisés conjointement avec l'ensemble des poids du réseau afin de maximiser les performances de classification.

On observe que les deux paramètres convergent progressivement vers des valeurs stables :

| Paramètre             | Initialisation | Valeur finale |
| --------------------- | -------------- | ------------- |
| Temperature threshold | 37.0           | ≈ 37.23       |
| N̂ threshold          | 1.0            | ≈ 1.22        |

La convergence rapide de ces paramètres est particulièrement intéressante. Malgré l'absence de connaissance clinique explicite dans le processus d'apprentissage, le modèle découvre spontanément des points de recentrage cohérents permettant de séparer plus efficacement les patients positifs et négatifs.

==Il est important de noter que ces valeurs ne doivent pas être interprétées directement comme des seuils cliniques NHSN :==

```text
NHSN :
    Température > 38 °C
    Durée > 2 jours

Modèle :
    Température ≈ 37.23 °C
    N̂ ≈ 1.22 jour
```

Les paramètres appris correspondent davantage à des **points de recentrage optimaux pour l'espace latent** qu'à des règles décisionnelles explicites. Leur rôle est de faciliter l'apprentissage en mettant en évidence les écarts significatifs autour de régions discriminantes des données.

Cette expérience constitue un résultat important de cette étude : les performances du modèle augmentent fortement lorsque les variables continues sont recentrées autour de valeurs apprises automatiquement, ce qui suggère que la représentation des signaux cliniques joue un rôle plus important que l'architecture GraphSAGE elle-même dans la découverte des motifs CAUTI.


## Matrice de confusion du meilleur modèle

![[Pasted image 20260619162527.png]]

Cette figure présente la matrice de confusion obtenue pour le meilleur modèle sélectionné durant l'entraînement (époque 28).

La matrice de confusion permet d'analyser précisément les types d'erreurs commises par le modèle :

| Réalité | Prédiction | Nombre |
|----------|------------|---------:|
| Négatif | Négatif | 295 |
| Négatif | Positif | 9 |
| Positif | Négatif | 4 |
| Positif | Positif | 98 |

On observe que le modèle classe correctement :

- **295 patients négatifs** (*True Negatives*) ;
- **98 patients positifs** (*True Positives*).

Les erreurs restent limitées :

- **9 faux positifs** (*False Positives*) ;
- **4 faux négatifs** (*False Negatives*).

Cette répartition est particulièrement intéressante dans le contexte clinique des CAUTI. Les faux négatifs correspondent à des patients infectés non détectés par le système, tandis que les faux positifs correspondent à des alertes injustifiées.

Le modèle présente ici une très faible proportion de faux négatifs :

```text
False Negatives = 4
```

ce qui explique le rappel (*Recall*) élevé observé précédemment :

```text
Recall = 96.08 %
```

La précision reste également très élevée :

```text
Precision = 91.59 %
```

ce qui signifie que la majorité des alertes générées correspondent effectivement à des cas positifs.

La matrice de confusion confirme ainsi que le modèle atteint un équilibre satisfaisant entre détection des infections et limitation des fausses alertes, avec seulement **13 erreurs sur 406 patients évalués**, soit :

```text
393 prédictions correctes
13 prédictions incorrectes
Accuracy = 96.80 %
```

Cette figure constitue une validation importante du comportement du modèle. Elle montre que les améliorations observées sur les métriques globales (Accuracy, Precision, Recall et F1-score) se traduisent effectivement par une réduction simultanée des faux positifs et des faux négatifs.

## Analyse des embeddings patients projetés par PCA

![[Pasted image 20260619180214.png]]

Cette figure représente la projection des embeddings des nœuds *Patient* dans un espace bidimensionnel obtenu par **Analyse en Composantes Principales (PCA)**.

L'objectif de cette visualisation est d'examiner la structure de l'espace latent appris par la branche GraphSAGE du modèle et d'évaluer si les patients positifs et négatifs deviennent naturellement séparables après propagation des messages dans le graphe.

### Observations

La projection révèle plusieurs caractéristiques intéressantes :

1. **Structure non aléatoire de l'espace latent**

   Les embeddings ne sont pas distribués uniformément dans le plan PCA. Plusieurs regroupements apparaissent, suggérant que le GNN a appris à organiser les patients selon certaines caractéristiques communes présentes dans le graphe.

2. **Présence de sous-groupes distincts**

   Plusieurs amas de points sont visibles :

   - un groupe dense situé dans la partie inférieure gauche ;
   - plusieurs groupes intermédiaires dispersés ;
   - quelques points fortement éloignés du centre de gravité de la population.

   Ces regroupements suggèrent que certains patients possèdent des motifs structurels particuliers dans leur représentation graphique.

3. **Présence d'observations atypiques**

   Plusieurs points apparaissent très éloignés du nuage principal, notamment pour :

   ```text
   PCA1 > 120
   PCA2 > 40
   ```

   Ces patients constituent des cas atypiques fortement distingués par le modèle.

   Dans le contexte du graphe CAUTI, ces observations peuvent correspondre à :

   - des durées de cathétérisation particulièrement élevées ;
   - des structures relationnelles rares ;
   - des combinaisons inhabituelles de mesures cliniques.

### Limitation actuelle de la figure

La coloration par le label clinique ne permet pas encore de distinguer clairement les classes positives et négatives.

La quasi-totalité des points apparaît avec une couleur identique, ce qui indique probablement :

- soit une forte domination de la classe négative dans cet échantillon ;
- soit un problème de visualisation lié à la palette utilisée ;
- soit que seuls quelques patients positifs sont présents dans les premiers graphes analysés.

Avant toute interprétation clinique, il est donc nécessaire de vérifier :

```python
df_pred["label"].value_counts()
```

et éventuellement d'utiliser une palette discrète :

```python
cmap="coolwarm"
```

ou

```python
cmap="bwr"
```

afin de rendre les classes plus visibles.

### Interprétation scientifique

À ce stade, cette figure montre principalement que le GNN construit une représentation latente structurée des patients. Cependant, elle ne permet pas encore de conclure à une séparation explicite entre les classes CAUTI positives et négatives.

L'intérêt principal de cette visualisation est de préparer les analyses suivantes :

1. Projection des embeddings finaux après fusion des branches :
   - Graphe ;
   - Température ;
   - N̂.

2. Analyse de la séparation des classes dans l'espace latent complet.

3. Identification des faux positifs et faux négatifs dans cet espace.

Ces analyses permettront de déterminer si la règle clinique apprise par le modèle est effectivement encodée dans les représentations internes du réseau.

## Espace clinique : Température maximale versus durée estimée de cathétérisation (N̂)

![[Pasted image 20260619180430.png]]

Cette figure représente l'ensemble des patients projetés dans l'espace clinique défini par :

- la température maximale observée (*Temperature max*) ;
- la durée maximale estimée de cathétérisation (*N̂ max*).

Les couleurs correspondent au label réel du patient :

- classe négative ;
- classe positive.

Les lignes pointillées matérialisent les seuils cliniques de référence issus des critères NHSN :

- **38 °C** pour la température ;
- **2 jours calendaires** pour la durée de cathétérisation.

### Observations générales

La première observation est la forte concentration des patients autour de la température clinique critique :

```text
37°C – 40°C
```

La majorité des observations se situent dans cette zone, ce qui confirme que la température constitue un facteur discriminant majeur du problème étudié.

On observe également une forte dispersion de la variable temporelle :

```text
N̂ ∈ [1 ; 48]
```

ce qui montre que la durée de cathétérisation apporte une information beaucoup plus variable que la température.

### Relation avec la règle clinique NHSN

Selon la règle métier simplifiée étudiée dans cette thèse :

```text
Température > 38°C
ET
Durée > 2 jours
```

les patients positifs devraient se concentrer dans le quadrant supérieur droit de la figure.

L'analyse visuelle montre effectivement une augmentation importante de la densité des patients positifs lorsque :

```text
Température ≈ 38°C ou plus
```

et

```text
N̂ > 2 jours
```

Cependant, la frontière réelle observée dans les données n'est pas parfaitement rectangulaire.

Cette observation suggère que :

- certains patients possédant une température élevée restent négatifs ;
- certains patients avec une durée longue restent négatifs ;
- la combinaison des deux variables est nécessaire pour expliquer correctement le phénomène clinique.

### Présence de cas atypiques

Plusieurs observations particulières apparaissent :

#### Température nulle

Un petit groupe de patients est situé à :

```text
Temperature max = 0
```

==Ces observations correspondent vraisemblablement à des patients pour lesquels aucune mesure de température n'est disponible dans le graphe.==

Le modèle doit donc apprendre à gérer explicitement les données manquantes.

#### Durées très élevées

Plusieurs patients présentent :

```text
N̂ > 30 jours
```

voire :

```text
N̂ > 45 jours
```

Ces durées constituent des cas extrêmes qui peuvent jouer un rôle important dans l'apprentissage de la règle temporelle.

### Intérêt pour l'analyse du modèle

Cette figure représente l'espace clinique brut fourni au système neuro-symbolique :

```text
PINN
    ↓
N̂ estimé

GNN
    ↓
Découverte de la règle
```

Elle constitue donc le point de départ de l'analyse.

Les figures suivantes permettront de répondre à une question essentielle :

```text
Comment le GNN transforme-t-il cet espace clinique
pour construire sa frontière de décision ?
```

En particulier :

- la projection des probabilités prédites ;
- la visualisation de la frontière de décision apprise ;
- l'analyse des faux positifs et des faux négatifs ;

permettront de déterminer si le modèle a effectivement redécouvert une forme de la règle NHSN à partir du graphe enrichi.

## Confiance du modèle dans l'espace clinique

![[Pasted image 20260619180558.png]]

Cette figure représente les mêmes patients que précédemment dans l'espace clinique :

- température maximale observée ;
- durée maximale estimée de cathétérisation (*N̂ max*).

Cependant, la coloration ne correspond plus au label réel mais à la **probabilité prédite par le modèle**.

La palette de couleurs traduit le niveau de confiance du GNN :

- violet foncé : faible probabilité de CAUTI ;
- jaune : forte probabilité de CAUTI.

Cette visualisation permet d'observer directement comment le modèle interprète l'espace clinique et où il situe sa frontière de décision.

### Apparition d'une frontière thermique nette

L'observation la plus marquante est l'apparition d'une transition brutale autour de :

```text
Température ≈ 38°C
```

On observe clairement deux régions :

- à gauche de cette frontière, les probabilités restent faibles ;
- à droite, les probabilités augmentent rapidement jusqu'à atteindre des valeurs proches de 1.

Cette transition est particulièrement intéressante car aucun seuil clinique explicite n'a été injecté dans le modèle.

Le réseau a uniquement reçu :

- les températures observées ;
- les durées estimées N̂ ;
- les relations du graphe.

La proximité de cette frontière avec le seuil NHSN suggère que le modèle a spontanément redécouvert l'importance clinique de la température.

### Influence de la durée N̂

L'effet de N̂ apparaît également dans la structure verticale des couleurs.

Pour une température donnée, la confiance du modèle augmente progressivement lorsque :

```text
N̂ augmente
```

Les patients présentant des durées de cathétérisation élevées sont associés à des probabilités plus importantes, en particulier lorsqu'ils se situent dans la région thermique critique.

Cette observation confirme que le modèle ne repose pas uniquement sur la température mais combine effectivement les deux informations.

### Cas particuliers

#### Patients sans température

Un petit groupe de patients apparaît à :

```text
Température = 0
```

Ces observations correspondent probablement à des données manquantes.

Le modèle leur attribue des probabilités intermédiaires plutôt que des décisions extrêmes.

Ce comportement est cohérent avec une stratégie prudente face à l'absence d'information clinique.

#### Patients à température élevée mais faible durée

Plusieurs patients situés à :

```text
Température > 38°C
N̂ ≈ 1 jour
```

reçoivent encore des probabilités relativement faibles.

Cela indique que le modèle ne se contente pas d'utiliser un simple seuil thermique mais intègre également la composante temporelle.

### Interprétation scientifique

Cette figure constitue l'une des validations les plus importantes du système proposé.

Elle montre que le pipeline :

```text
PINN
    ↓
Estimation de N̂

Enrichissement du graphe
    ↓

GNN
    ↓

Prédiction CAUTI
```

aboutit à la construction d'un espace décisionnel cohérent avec les connaissances médicales.

Le résultat est particulièrement intéressant car :

- les seuils NHSN n'ont pas été codés explicitement ;
- la règle n'est jamais fournie au modèle ;
- la décision émerge uniquement de l'apprentissage supervisé.

La transition observée autour de 38°C suggère que le GNN a identifié de manière autonome une région clinique fortement associée aux infections urinaires liées au cathéter.

Cette figure constitue ainsi un premier indice visuel que le modèle redécouvre une forme de la règle clinique recherchée à partir du graphe enrichi.

## Analyse des erreurs du modèle dans l'espace clinique

![[Pasted image 20260619180744.png]]

Cette figure représente la distribution des erreurs du modèle dans l'espace clinique défini par :

- la température maximale observée ;
- la durée estimée maximale de cathétérisation (*N̂ max*).

Les patients correctement classés apparaissent en bleu clair, tandis que :

- les **faux positifs** sont représentés par des croix orange ;
- les **faux négatifs** sont représentés par des triangles verts.

Les lignes pointillées matérialisent les seuils cliniques de référence :

```text
Température = 38°C
Durée = 2 jours
```

### Répartition générale des erreurs

La première observation est que la grande majorité des patients est correctement classée.

Les erreurs représentent une proportion très faible de l'ensemble des observations, ce qui est cohérent avec les performances obtenues :

- Accuracy ≈ 96.8 %
- F1-score ≈ 93.8 %

Cette figure permet cependant d'identifier précisément les régions cliniques dans lesquelles le modèle reste incertain.


### Analyse des faux négatifs

Les faux négatifs apparaissent sous la forme de triangles verts.

Ils sont presque exclusivement localisés dans une zone très spécifique :

```text
Température ≈ 38°C
N̂ ≈ 1 à 3 jours
```

Ces patients sont particulièrement intéressants car ils se situent exactement à proximité des seuils décisionnels.

Autrement dit :

- ils satisfont partiellement la règle clinique ;
- ils présentent des caractéristiques proches des patients négatifs ;
- ils constituent des cas frontières difficiles à distinguer.

Ce comportement est attendu dans un système apprenant à partir des données, puisque la frontière réelle n'est jamais parfaitement nette.

La concentration des faux négatifs autour des seuils suggère que le modèle a correctement appris la structure globale du problème et ne commet des erreurs que sur les cas les plus ambigus.


### Analyse des faux positifs

Les faux positifs sont plus nombreux et apparaissent sous forme de croix orange.

Ils se répartissent principalement dans deux régions :

#### Région principale

```text
Température ≈ 38°C
N̂ > 2 jours
```

Ces patients ressemblent fortement aux cas positifs selon la règle clinique simplifiée.

Dans cette zone, les faux positifs peuvent correspondre à :

- des patients présentant une fièvre importante mais non liée à une CAUTI ;
- des patients possédant une longue durée de cathétérisation sans infection ;
- des cas cliniquement limites.

Le comportement du modèle est compréhensible puisqu'il s'agit précisément de la région où le risque est le plus élevé.

#### Région des données manquantes

Un second groupe de faux positifs apparaît à :

```text
Température = 0
```

avec :

```text
N̂ ≈ 3 à 10 jours
```

Ces observations correspondent très probablement à des patients ne possédant aucune mesure de température dans le graphe.

Le modèle doit alors prendre une décision à partir d'une information incomplète.

Cette concentration d'erreurs suggère que la gestion des données manquantes constitue actuellement l'une des principales limites du système.


### Enseignement principal

L'aspect le plus important de cette figure est que les erreurs ne sont pas distribuées aléatoirement.

Au contraire, elles se concentrent dans des régions cliniquement cohérentes :

```text
Zone frontière autour de 38°C

ou

Patients avec données manquantes
```

Cette propriété est généralement observée lorsque le modèle a effectivement appris la structure du problème et non lorsqu'il effectue des prédictions aléatoires.

Les erreurs restantes semblent donc davantage liées à l'ambiguïté intrinsèque de certains cas cliniques qu'à une incapacité du GNN à capturer la relation entre température, durée de cathétérisation et risque de CAUTI.


### Perspectives

Cette figure ouvre plusieurs pistes d'amélioration :

1. Analyse détaillée des faux positifs afin d'identifier d'éventuels sous-groupes cliniques particuliers.

2. Amélioration de la gestion des températures manquantes dans le graphe.

3. Étude des cas proches de la frontière décisionnelle afin de comprendre quelles informations relationnelles influencent encore la décision du GNN.

Ces analyses permettront de mieux caractériser les limites du modèle et de guider les futures évolutions du système neuro-symbolique.

## Espace décisionnel latent appris par le modèle

![[Pasted image 20260619180915.png]]

Cette figure représente une approximation de l'espace décisionnel construit par le modèle à partir des deux variables cliniques principales :

- la température maximale observée ;
- la durée maximale estimée de cathétérisation (*N̂*).

Les couleurs correspondent au score latent appris après application des transformations :

```python
temp_alpha * (Température - Température_threshold)

nhat_alpha * (N̂ - N̂_threshold)
```

Les lignes pointillées représentent les seuils cliniques de référence issus des critères NHSN :

- **38 °C** pour la température ;
- **2 jours** pour la durée de cathétérisation.


### Structure de l'espace appris

La surface obtenue présente un gradient continu allant :

```text
Bas gauche
    ↓
Faible score

Haut droit
    ↓
Fort score
```

Le modèle considère donc que le risque augmente progressivement lorsque :

- la température augmente ;
- la durée de cathétérisation augmente.

Cette observation est cohérente avec les connaissances médicales et confirme que les deux variables contribuent positivement à la décision finale.


### Influence conjointe des deux variables

Contrairement à une règle binaire de type :

```python
if temp > 38 and N > 2:
    CAUTI
```

le modèle apprend ici une représentation continue.

Par exemple :

```text
Température très élevée
mais
N̂ faible
```

peut produire un score intermédiaire.

Inversement :

```text
N̂ très élevé
mais
Température modérée
```

peut également contribuer au risque.

Cette propriété est caractéristique des modèles neuronaux qui apprennent des fonctions de décision continues plutôt que des règles strictement booléennes.

### Comparaison avec les seuils NHSN

Les lignes rouges et bleues montrent que les seuils NHSN se situent dans une région de transition importante du score latent.

Cette observation est particulièrement intéressante car :

```text
Les seuils NHSN n'ont jamais été codés explicitement.
```

Le modèle a uniquement appris à partir :

- du graphe enrichi ;
- des labels CAUTI ;
- des variables cliniques observées.

Le fait que l'augmentation du score soit particulièrement marquée dans la région :

```text
Température > 38°C

et

N̂ > 2 jours
```

constitue un indice fort que le système a retrouvé une structure proche de la règle clinique recherchée.


### Interprétation des seuils appris

Il est important de noter que le modèle ne travaille pas directement avec les seuils NHSN.

Il apprend ses propres paramètres de recentrage :

```text
Température_threshold ≈ 37.2°C

N̂_threshold ≈ 1.2 jour
```

Ces valeurs servent uniquement à améliorer la représentation interne des données.

Le rôle de ces paramètres n'est pas de reproduire les critères médicaux mais de faciliter l'optimisation du réseau.

Ainsi :

```text
Seuil appris ≠ seuil clinique
```

tout en permettant au modèle d'atteindre des performances très élevées.


### Limite de cette visualisation

Cette figure ne représente pas encore la véritable frontière de décision du GNN.

Elle correspond uniquement à la contribution directe des transformations :

```python
alpha * (x - threshold)
```

avant leur combinaison avec :

- les embeddings du graphe ;
- les embeddings thermiques ;
- les embeddings temporels.

La frontière réelle utilisée pour la prédiction est donc plus complexe.

Une étape future consistera à reconstruire la véritable surface de probabilité du classifieur final afin de visualiser exactement comment le réseau combine les différentes représentations latentes.


### Résultat principal

Cette figure apporte néanmoins un résultat important :

```text
Le modèle a appris que :

Température ↑
et
Durée ↑

⇒ Risque CAUTI ↑
```

sans qu'aucune règle explicite ne lui soit fournie.

Elle constitue ainsi une première démonstration visuelle que le pipeline :

PINN → Enrichissement du graphe → GNN

permet effectivement de faire émerger une représentation cohérente du risque clinique à partir des données observées.


## Projection des patients dans l'espace décisionnel latent appris

![[Pasted image 20260619181046.png]]

Cette figure superpose les patients réels à l'espace décisionnel latent précédemment reconstruit.

L'arrière-plan coloré représente le score latent appris par le modèle à partir des variables :

- température maximale ;
- durée maximale estimée de cathétérisation (*N̂*).

Les points correspondent aux patients du jeu de données projetés dans cet espace clinique.

Les lignes pointillées matérialisent les seuils de référence NHSN :

- **38°C** ;
- **2 jours de cathétérisation**.


### Cohérence entre les données et l'espace latent

La première observation est la remarquable cohérence entre :

```text
Structure des données
```

et

```text
Structure du score appris
```

Les patients se répartissent naturellement le long du gradient latent construit par le modèle.

Les individus situés dans les régions associées à un faible score sont principalement localisés dans la partie inférieure gauche du graphique, tandis que les observations situées dans les régions de score élevé se concentrent dans la partie supérieure droite.

Cette organisation suggère que le modèle a appris une représentation monotone du risque clinique :

```text
Température ↑

et/ou

Durée ↑

⇒ Risque ↑
```


### Concentration des patients autour des seuils cliniques

La majorité des patients est regroupée dans une zone relativement restreinte :

```text
37°C – 40°C
```

pour la température,

et :

```text
1 – 20 jours
```

pour N̂.

Cette concentration est particulièrement intéressante car elle correspond précisément à la région où les décisions cliniques deviennent difficiles.

Le modèle ne dispose donc pas d'une séparation triviale entre les classes mais doit apprendre à distinguer des patients très proches dans l'espace clinique.

### Cas extrêmes

Plusieurs patients apparaissent dans la partie supérieure du graphique :

```text
N̂ > 30 jours
```

voire :

```text
N̂ > 45 jours
```

Ces observations se situent dans la région de score maximal.

Le modèle les considère comme fortement compatibles avec le profil associé au risque de CAUTI.

Ces patients constituent probablement des cas particulièrement informatifs pour l'apprentissage de la composante temporelle.

### Patients avec température absente

Un petit groupe de patients apparaît à :

```text
Température = 0
```

Ces observations correspondent vraisemblablement à des données manquantes.

Leur position dans la région de faible score est cohérente avec le comportement observé précédemment lors de l'analyse des erreurs.

Le modèle semble traiter l'absence d'information thermique comme un signal peu favorable à la prédiction d'une infection.

### Ce que révèle cette figure

Cette visualisation permet de relier trois niveaux du système :

```text
Données cliniques
        ↓
Variables enrichies (N̂)
        ↓
Représentation latente du GNN
```

On observe que les patients ne sont pas répartis aléatoirement dans l'espace latent appris.

Au contraire, ils occupent des régions cohérentes avec la structure de risque identifiée par le modèle.

Cette propriété constitue un indice supplémentaire que le pipeline neuro-symbolique proposé :

```text
PINN
    ↓
Estimation de N̂

Enrichissement du graphe
    ↓

GNN
    ↓

Découverte de la règle
```

parvient à construire une représentation interne alignée avec les facteurs cliniques connus.

### Limitation actuelle

Cette figure reste fondée sur une approximation du score latent utilisant uniquement :

```python
temp_alpha * (Température - seuil_temp)

nhat_alpha * (N̂ - seuil_nhat)
```

Elle ne prend pas encore en compte :

- les embeddings du graphe patient ;
- les embeddings thermiques ;
- les embeddings temporels ;
- les poids du classifieur final.

La véritable frontière de décision du GNN est donc plus complexe.

Une étape future consistera à projeter directement les sorties du classifieur afin de reconstruire la surface de probabilité réelle du modèle dans l'espace clinique.

Cette visualisation permettra alors d'observer précisément comment les représentations latentes du graphe modifient la décision finale.

## Analyse des faux positifs et faux négatifs

L'analyse des erreurs montre une asymétrie marquée entre les faux positifs et les faux négatifs.

### Faux négatifs

Le modèle ne produit que 5 faux négatifs.

Ces patients présentent :

- Température moyenne : 38.95°C
- N̂ moyen : 2.01 jours

Ces valeurs sont particulièrement révélatrices car elles se situent exactement au voisinage des seuils cliniques NHSN :

```text
Température > 38°C
N̂ > 2 jours
```

Les faux négatifs correspondent donc à des cas frontières.

Le modèle attribue à ces patients une probabilité moyenne de seulement 0.12, ce qui indique qu'il les considère comme fortement négatifs malgré leur proximité avec la règle clinique.

Cela suggère que d'autres informations présentes dans le graphe compensent partiellement les signaux thermiques et temporels.

Cette observation est cohérente avec le rôle attendu du GNN : la décision finale ne repose pas uniquement sur la température et N̂ mais également sur le contexte relationnel appris dans le graphe.

### Faux positifs

Le modèle produit davantage de faux positifs (38 cas).

Les caractéristiques moyennes sont :

- Température moyenne : 23.2°C
- N̂ moyen : 6.97 jours

La température moyenne extrêmement faible révèle immédiatement la présence d'un nombre important de patients sans mesure thermique disponible :

```text
Température = 0
```

==Ces patients sont néanmoins associés à des durées de cathétérisation relativement élevées.==

Le modèle semble alors considérer la durée seule comme un facteur de risque suffisant pour déclencher une prédiction positive.

Cette hypothèse est renforcée par :

```text
N̂ max = 37 jours
```

observé parmi les faux positifs.

Le réseau attribue une probabilité moyenne de 0.71 à ces cas, indiquant une confiance relativement élevée dans ses prédictions erronées.

### Enseignement principal

L'analyse révèle que :

- les faux négatifs sont concentrés autour de la frontière clinique réelle ;
- les faux positifs sont principalement liés à l'absence de température ou à des durées très élevées.

Ainsi, les erreurs du modèle ne sont pas distribuées aléatoirement.

Elles reflètent deux situations cliniques difficiles :

1. des patients très proches de la règle NHSN ;
2. des patients présentant des informations incomplètes mais une forte composante temporelle.

Cette observation constitue un indice supplémentaire que le modèle a effectivement appris une représentation cohérente du risque CAUTI.
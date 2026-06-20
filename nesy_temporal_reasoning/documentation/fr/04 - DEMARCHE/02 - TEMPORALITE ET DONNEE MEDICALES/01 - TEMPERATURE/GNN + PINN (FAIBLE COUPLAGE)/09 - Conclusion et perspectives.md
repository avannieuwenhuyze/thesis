# Conclusion et perspectives

## Bilan des travaux réalisés

Cette recherche avait pour objectif d'explorer une problématique centrale du raisonnement médical assisté par l'intelligence artificielle :

> Comment apprendre automatiquement une connaissance temporelle clinique à partir de données médicales hétérogènes puis l'utiliser pour reconstruire un raisonnement médical explicable ?

Le cas d'étude retenu concerne les infections urinaires associées aux cathéters (CAUTI), dont les définitions NHSN reposent sur une combinaison de contraintes :

- temporelles ;
    
- cliniques ;
    
- microbiologiques ;
    
- logiques.
    

Parmi ces contraintes figure notamment la notion de durée de cathétérisation supérieure à deux jours calendaires, combinée à des critères cliniques tels qu'une température supérieure à 38°C.

L'objectif n'était pas d'implémenter directement ces règles dans un moteur d'inférence mais d'étudier si certaines de ces connaissances pouvaient être redécouvertes automatiquement à partir des données.

L'approche proposée repose sur la combinaison de trois mécanismes complémentaires :

```text
PINN

↓

Connaissance temporelle

↓

Graphe enrichi

↓

GNN

↓

Règles logiques
```

Cette chaîne constitue le fil conducteur de l'ensemble des expérimentations réalisées.

## Étape 1 : découverte d'une connaissance temporelle latente

La première contribution du projet concerne l'apprentissage d'une variable temporelle latente :

$$  
\hat{N}  
$$

correspondant au nombre estimé de jours calendaires traversés entre l'insertion et le retrait d'un cathéter.

Cette information n'est pas explicitement présente dans les données RDF initiales.

Le modèle temporel agit donc comme un mécanisme de découverte de connaissances capable de reconstruire une représentation temporelle interprétable à partir d'événements cliniques observés.

À ce stade, aucune connaissance relative aux CAUTI n'est injectée dans le modèle.

Le système apprend uniquement à représenter une propriété temporelle pertinente.

Cette étape répond directement au premier niveau de la feuille de route identifiée dans le survey :

```text
Apprentissage de représentations temporelles explicites
```

## Étape 2 : enrichissement sémantique du graphe médical

La variable :

$$  
\hat{N}  
$$

est ensuite intégrée dans le graphe médical.

Le graphe n'est pas enrichi par une décision clinique mais uniquement par une nouvelle connaissance :

```text
Catheterization

      ↓

      N̂
```

Cette approche constitue une première forme d'intégration neuro-symbolique.

Le réseau neuronal découvre une information.

Cette information devient ensuite un élément de connaissance disponible dans la représentation relationnelle.

Le système reste libre d'utiliser ou non cette information lors de l'apprentissage.

Cette étape correspond au second niveau de la feuille de route du survey :

```text
Intégration de connaissances apprises
dans une structure symbolique
```

## Étape 3 : apprentissage relationnel par GNN

Le graphe enrichi est ensuite analysé par un modèle Graph Neural Network de type GraphSAGE.

Le GNN ne reçoit jamais explicitement les règles NHSN.

Aucune règle de la forme :

```text
Température > 38°C

ET

N̂ > 2 jours
```

n'est codée dans l'architecture.

Le modèle doit apprendre seul quelles combinaisons de relations permettent de distinguer les patients positifs des patients négatifs.

Les analyses réalisées montrent que :

- la température seule est insuffisante ;
    
- la temporalité seule est insuffisante ;
    
- la combinaison des deux apporte l'information la plus discriminante.
    

Cette observation suggère que le modèle exploite effectivement plusieurs sources d'information plutôt qu'une simple corrélation isolée.

Cette étape correspond au troisième niveau du survey :

```text
Raisonnement relationnel
sur connaissances temporelles
```

## Étape 4 : auditabilité du raisonnement

Une étape importante a consisté à étudier le comportement interne du modèle.

Plusieurs analyses ont été réalisées :

- visualisation des embeddings ;
    
- étude des seuils appris ;
    
- projection des représentations latentes ;
    
- analyse des faux positifs et faux négatifs ;
    
- extraction des paramètres décisionnels.
    

Ces analyses ont montré que le modèle apprend notamment :

```text
Température ≈ 37.20°C

N̂ ≈ 1.13
```

comme seuils internes d'activation.

Le raisonnement du système devient ainsi partiellement observable.

Cette étape répond au quatrième niveau identifié dans le survey :

```text
Auditabilité du raisonnement
```

## Étape 5 : extraction de règles symboliques

La contribution la plus récente du projet concerne l'extraction de règles logiques à partir des connaissances apprises par le GNN.

Les seuils appris sont transformés en prédicats logiques puis analysés à l'aide d'un moteur d'induction de règles de type RIPPER.

Les expérimentations montrent qu'il est possible de reconstruire une représentation symbolique du comportement du modèle :

```text
GNN

↓

Seuils appris

↓

RIPPER

↓

Règle logique
```

Cette étape constitue une première démonstration expérimentale du passage :

```text
Network

↓

Logic
```

identifié comme un enjeu majeur du raisonnement neuro-symbolique moderne.

Les règles extraites peuvent ensuite être confrontées aux connaissances médicales existantes.

## Analyse critique des résultats

Les expérimentations ont montré que lorsque les seuils appris par le GNN sont mis en compétition avec les seuils NHSN, les règles médicales sont systématiquement sélectionnées par RIPPER.

Ce résultat peut sembler surprenant.

En réalité, il est cohérent avec la construction du dataset.

Les labels ayant été générés à partir des critères NHSN, les règles :

```text
Température > 38°C

ET

N̂ > 2 jours
```

constituent naturellement l'explication la plus parcimonieuse des données.

Autrement dit :

```text
NHSN

↓

Labels

↓

Apprentissage

↓

RIPPER

↓

NHSN
```

Cette observation ne remet pas en cause l'intérêt du GNN.

Elle montre simplement que le système est capable de retrouver une connaissance déjà présente dans les données.

L'expérience joue donc le rôle d'une validation méthodologique.

Avant de chercher à découvrir de nouvelles connaissances, il est nécessaire de vérifier que le pipeline est capable de reconstruire correctement des connaissances connues.

## Positionnement dans l'état de l'art

Le survey réalisé sur le raisonnement neuro-symbolique temporel met en évidence plusieurs tendances majeures :

- apprentissage de connaissances temporelles interprétables ;
    
- intégration dans des structures symboliques ;
    
- auditabilité des décisions ;
    
- transformation des représentations neuronales en règles logiques ;
    
- construction de boucles neuro-symboliques fermées.
    

Les travaux réalisés s'inscrivent directement dans cette trajectoire.

Le projet ne cherche pas uniquement à améliorer une métrique de classification.

Il cherche à reconstruire progressivement un raisonnement médical explicable à partir de connaissances apprises automatiquement.

L'objectif final n'est donc pas seulement :

```text
Patient

↓

CAUTI
```

mais :

```text
Patient

↓

Connaissances apprises

↓

Raisonnement

↓

Règle explicable

↓

Décision
```

## Correspondance avec la feuille de route du survey

| Niveau                              | Objectif                                            | État                        |
| ----------------------------------- | --------------------------------------------------- | --------------------------- |
| Représentation temporelle explicite | Apprendre une connaissance temporelle interprétable | Atteint                     |
| Intégration neuro-symbolique        | Injecter la connaissance dans le graphe             | Atteint                     |
| Raisonnement relationnel            | Utiliser cette connaissance pour la décision        | Atteint                     |
| Auditabilité                        | Comprendre les décisions du modèle                  | Partiellement atteint       |
| Extraction symbolique               | Produire des règles explicites                      | Première validation obtenue |
| Boucle neuro-symbolique fermée      | Couplage apprentissage ↔ raisonnement               | Non atteint                 |

Le projet se situe aujourd'hui à la frontière entre l'auditabilité des modèles et l'induction automatique de connaissances symboliques.

## Perspectives scientifiques

### Généralisation de l'induction logique

Les expérimentations réalisées ont montré qu'il est possible d'extraire des règles logiques à partir des connaissances apprises par le GNN.

La prochaine étape consiste à généraliser cette approche à des graphes beaucoup plus riches intégrant :

- microbiologie ;
    
- biologie clinique ;
    
- traitements ;
    
- événements hospitaliers ;
    
- scores de gravité ;
    
- données longitudinales.
    

L'objectif n'est plus uniquement de reconstruire une règle connue mais de permettre la découverte de connaissances médicales nouvelles.

### Couplage fort PINN ↔ GNN

L'architecture actuelle repose sur un couplage faible :

```text
PINN

↓

N̂

↓

GNN
```

Une évolution naturelle consiste à entraîner les deux composantes simultanément.

Le GNN pourrait alors influencer directement l'apprentissage de la représentation temporelle.

Cette évolution permettrait de construire une véritable architecture neuro-symbolique intégrée.

### Vérification symbolique automatique

Une fois les règles extraites, celles-ci pourront être confrontées automatiquement :

- aux définitions NHSN ;
    
- aux recommandations médicales ;
    
- aux connaissances ontologiques.
    

Le système deviendrait alors capable non seulement d'apprendre mais également de vérifier ses propres raisonnements.

### Découverte de nouvelles connaissances médicales

À plus long terme, le système pourra être appliqué à des situations où aucune règle métier claire n'existe.

Le rôle du GNN ne sera alors plus de retrouver une règle connue mais de découvrir des régularités inédites dans les données.

Les moteurs logiques permettront ensuite de transformer ces connaissances implicites en règles compréhensibles par les experts.

## Vision à long terme

L'architecture visée peut être représentée sous la forme suivante :

```text
Données cliniques

        ↓

      PINN

        ↓

Connaissance temporelle

        ↓

 Graphe enrichi

        ↓

       GNN

        ↓

Extraction de règles

        ↓

Vérification symbolique

        ↓

Rétroaction

        ↓

Amélioration du modèle
```

Cette architecture dépasse la simple prédiction.

Elle vise la construction d'un système capable :

- d'apprendre ;
    
- de raisonner ;
    
- d'expliquer ;
    
- de vérifier ;
    
- d'enrichir ses propres connaissances.
    

## Conclusion générale

Cette preuve de concept démontre qu'il est possible d'articuler apprentissage temporel, représentation relationnelle, induction logique et raisonnement clinique au sein d'une même chaîne de traitement.

Les expérimentations montrent qu'une connaissance temporelle latente peut être :

1. découverte automatiquement ;
    
2. intégrée dans un graphe médical ;
    
3. exploitée par un GNN ;
    
4. transformée en règles symboliques interprétables ;
    
5. confrontée aux connaissances expertes NHSN.
    

L'extraction de règles par RIPPER constitue une première validation expérimentale du passage :

```text
Network

↓

Logic
```

identifié comme un défi majeur du raisonnement neuro-symbolique contemporain.

Au regard de la feuille de route proposée dans le survey, le projet dépasse désormais le simple apprentissage relationnel et entre dans une phase d'explicabilité symbolique où les connaissances apprises peuvent être analysées, comparées et auditées.

Les prochaines étapes concerneront principalement :

- la généralisation de l'induction logique ;
    
- le couplage fort PINN ↔ GNN ;
    
- la vérification symbolique automatique ;
    
- la découverte de nouvelles connaissances médicales.
    

L'ambition à long terme est la construction d'une boucle neuro-symbolique temporelle complète capable non seulement d'apprendre et de prédire, mais également d'expliquer, de vérifier et d'améliorer ses propres connaissances.
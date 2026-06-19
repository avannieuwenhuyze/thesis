## Objectif

L'objectif du modèle Weak Temporal GNN est de découvrir automatiquement des motifs décisionnels associés aux infections urinaires liées au cathéter (CAUTI) à partir d'un graphe enrichi par un modèle temporel.

Le modèle ne reçoit jamais explicitement les règles cliniques :

```
Température > 38°C
Durée de cathétérisation > 2 jours
```

Ces connaissances doivent être retrouvées automatiquement à partir des données et de la structure du graphe.


## Principe Général

L'architecture repose sur une stratégie à faible couplage 

Le processus complet est le suivant :

```
Données cliniques        
│        
▼     
PINN        
│        
▼ 
Estimation de N_hat        
│        
▼ 
Enrichissement RDF        
│        
▼ 
Construction du graphe        
│
▼ 
Weak Temporal GNN        
│        
▼ 
Prédiction CAUTI
```

Le PINN estime une variable temporelle latente :

```
N_hat
```

correspondant au nombre estimé de jours calendaires traversés par une période de cathétérisation.

Cette information est ensuite injectée dans le graphe sous la forme d'un nœud :

```
TemporalPattern
```

Le GNN doit ensuite déterminer si cette information participe ou non à la décision clinique.

## Vue d'ensemble du modèle

Le modèle final est composé de trois branches complémentaires.

```
                           Patient Graph                                  
                                │                             
                            GraphSAGE                                  
                                │                             
                            patient_emb                                  
		                        │
	  ┌─────────────────────────┼─────────────────────────┐        
      │                         │                         │                          
      ▼                         ▼                         ▼   
      Température             N_hat temporel          Structure RDF     
      Encoder                    Encoder                                
      │                            │ 
      Max Pool                  Max Pool                               
      │                             │
      temp_emb                  nhat_emb        
      
      └─────────────── concat ───────────────┘                           
                         │                     
                    Classifier                           
                         │                        
                        CAUTI
```

Chaque branche produit une représentation latente spécialisée.

Ces représentations sont ensuite fusionnées afin de produire une prédiction patient.

## Branche Graphe

La première branche repose sur GraphSAGE.

Elle exploite la structure relationnelle du graphe RDF :

```
Patient
├── BodyTemperature
├── Catheterization
├── TemporalPattern
├── CathInsertion
├── CathRemoval
└── TemporalInterval
```

Les couches GraphSAGE réalisent plusieurs étapes de message passing.

Chaque couche permet à un nœud de recevoir l'information de ses voisins.

Avec :

```
num_layers = 3
```

le patient peut exploiter des informations situées à trois sauts dans le graphe.

Cette branche produit :

```
patient_emb
```

qui représente le contexte structurel complet du patient.

## Branche Température

La seconde branche traite spécifiquement les températures corporelles.

Chaque mesure est encodée par un réseau de neurones dédié :

```
Température brute        
│        
▼     
Encoder        
│        
▼ 
Embedding thermique
```

Les différentes températures d'un même patient sont ensuite agrégées par :

```
global_max_pool
```

Le modèle conserve ainsi les activations thermiques les plus importantes.

Cette branche produit :

```
temp_emb
```
## Branche Temporelle

La troisième branche traite les valeurs :

```
N_hat
```

issues du PINN.

Chaque valeur temporelle est encodée indépendamment :

```
N_hat  
│  
▼
Encoder  
│  
▼
Embedding temporel
```

Les représentations sont ensuite agrégées au niveau patient via :

```
global_max_pool
```

Cette branche produit :

```
nhat_emb
```
## Fusion des représentations

Les trois représentations sont concaténées :

```
final_emb = torch.cat(    
[        patient_emb,        
         temp_emb,        
         nhat_emb,    
],    
dim=1,)
```

La représentation finale contient simultanément :

- le contexte relationnel du graphe ;
- l'information thermique ;
- l'information temporelle issue du PINN.

## Découverte automatique des seuils

Une difficulté importante est apparue durant les expérimentations.

Le modèle apprenait difficilement lorsque les valeurs brutes étaient utilisées directement :

```
Température
N_hat
```

Les performances augmentaient fortement lorsqu'un recentrage était effectué autour de seuils cliniques.

Afin d'éviter l'injection explicite des règles métier, le modèle final apprend lui-même :

```
temp_threshold
nhat_threshold
```

ainsi que leur importance :

```
temp_alpha
nhat_alpha
```

Le recentrage devient alors :

```
temp_alpha * (temp - temp_threshold)
nhat_alpha * (nhat - nhat_threshold)
```

Les seuils sont donc découverts automatiquement au cours de l'entraînement.


## Architecture retenue

Configuration finale :

```
hidden_channels = 64
num_layers = 3
dropout = 0.0
batch_size = 8
learning_rate = 1e-3
```

Performances observées :

```
Accuracy  : 97.29 %
Precision : 93.33 %
Recall    : 96.08 %
F1-score  : 94.69 %
```

Cette architecture constitue la version actuelle du modèle **Weak Temporal GNN**, servant de base aux travaux futurs sur l'apprentissage conjoint PINN-GNN.
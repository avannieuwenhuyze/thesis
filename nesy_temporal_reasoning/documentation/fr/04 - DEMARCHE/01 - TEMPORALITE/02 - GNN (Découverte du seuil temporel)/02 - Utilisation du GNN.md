# GNN V0 — Découverte de la règle CAUTI à partir de N̂ uniquement

## Objectif scientifique

Avant d'étudier un graphe clinique enrichi par plusieurs sources d'information, nous avons souhaité répondre à une question fondamentale :

> La seule information temporelle estimée par le PINN est-elle suffisante pour prédire une infection urinaire associée au cathéter (CAUTI) ?

Cette expérience constitue la première étape de validation du pipeline neuro-symbolique proposé.

L'objectif n'est pas d'obtenir les meilleures performances possibles, mais de mesurer la valeur intrinsèque de la variable temporelle :

$$  
\hat{N}  
$$

estimée par le PINN.

# Hypothèse de travail

Les critères NHSN imposent notamment que le cathéter soit présent depuis plus de deux jours calendaires avant la survenue de l'infection.

La variable :

$$  
\hat{N}  
$$

correspond précisément à cette notion temporelle.

Nous faisons donc l'hypothèse que :

```text
N̂ contient déjà une partie de l'information
nécessaire à la décision clinique.
```

L'expérience consiste alors à vérifier si un GNN peut apprendre ce signal à partir du graphe uniquement.


# Suppression volontaire du Data Linkage

## Pourquoi ?

Lors des premières expérimentations, le graphe complet contenait de nombreux types de nœuds :

- Patient
    
- Catheterization
    
- Temperature
    
- Symptom
    
- Culture
    
- Observation
    
- etc.
    

Dans un tel contexte, le modèle pourrait apprendre des corrélations indirectes non souhaitées.

Par exemple :

```text
Type de nœud
        ↓
Structure du graphe
        ↓
Prédiction
```

sans réellement utiliser la variable temporelle.

Cette situation est connue sous le nom de :

```text
Data Leakage
```

ou plus précisément ici :

```text
Graph Structural Leakage
```

où la structure du graphe révèle involontairement le label.


## Stratégie adoptée

Afin d'isoler la contribution réelle de N̂, nous avons construit un sous-graphe minimal.

Seuls trois types de nœuds sont conservés :

```text
Patient
    │
    ▼
Catheterization
    │
    ▼
TemporalPattern
```

Le nœud :

```text
TemporalPattern
```

porte la valeur :

$$  
\hat{N}  
$$

estimée par le PINN.

Tous les autres types de nœuds sont supprimés lors de la construction du dataset GNN.

Les fichiers RDF originaux restent inchangés.

Le filtrage est effectué uniquement lors de la génération du dataset PyTorch Geometric.



# Architecture du graphe

## Graphe positif

![Graph Patient Positif|154](https://chatgpt.com/g/g-p-698f15cc63b48191ba9d16ff90d77bff/c/positive_graph.png)

Cette figure présente un exemple de patient positif.

Le graphe est volontairement minimaliste :

```text
Patient
    ↓
Catheterization
    ↓
TemporalPattern(N̂)
```

L'unique information clinique disponible est la durée estimée de cathétérisation.



## Graphe négatif

![Graph Patient Negatif|169](https://chatgpt.com/g/g-p-698f15cc63b48191ba9d16ff90d77bff/c/negative_graph.png)

Cette figure présente un exemple de patient négatif.

La structure est strictement identique.

La seule différence réside dans la valeur portée par le nœud :

```text
TemporalPattern
```

Cette propriété garantit que le modèle ne peut pas s'appuyer sur la topologie du graphe pour reconnaître les classes.



# Architecture du GNN

Le modèle utilisé est un GraphSAGE hétérogène minimal.

Le flux d'information est le suivant :

```text
TemporalPattern
      ↓
Catheterization
      ↓
Patient
```

Chaque couche GraphSAGE effectue une étape de propagation de messages.

Avec :

```python
num_layers = 2
```

l'information temporelle peut remonter du nœud :

```text
TemporalPattern
```

jusqu'au nœud :

```text
Patient
```

qui porte le label de classification.


# Protocole expérimental

Le dataset est divisé en :

```text
80 % entraînement
20 % validation
```

L'entraînement est réalisé avec :

```python
Binary Cross Entropy Loss
Adam Optimizer
```

et une classification binaire au niveau du patient.

L'objectif du modèle est simplement :

```text
Patient
    ↓
CAUTI ?
```

sans aucune autre information clinique.


# Résultats

## Performances obtenues

| Métrique  | Valeur |
| --------- | ------ |
| Accuracy  | 0.99 % |
| Precision | 1 %    |
| Recall    | 0.98 % |
| F1-score  | 0.99 % |

## Analyse

Les résultats montrent que :

```text
N̂ seul possède un pouvoir discriminant réel.
```

Le modèle parvient à distinguer une partie importante des patients positifs et négatifs alors qu'il ne dispose que :

- d'un patient ;
    
- d'un cathéter ;
    
- d'une estimation temporelle.
    

Cette observation confirme que le PINN a effectivement découvert une information clinique pertinente.


# Limites

Malgré ces résultats encourageants, les performances restent inférieures à celles obtenues avec :

```text
N̂ + Température
```

ou avec le modèle Weak Temporal complet.

Cette limitation est attendue.

La définition NHSN ne repose pas uniquement sur une contrainte temporelle.

La température joue également un rôle majeur dans la décision clinique.

Le modèle ne dispose donc pas encore de toutes les informations nécessaires pour reconstruire la règle complète.


# Conclusion

Cette première expérimentation constitue une validation importante du pipeline neuro-symbolique.

Elle démontre que :

1. le PINN produit une variable temporelle exploitable ;
    
2. cette variable contient un signal clinique réel ;
    
3. un GNN est capable d'utiliser ce signal pour réaliser une classification patient ;
    
4. le résultat ne dépend pas d'un data linkage structurel puisque le graphe a été volontairement réduit à son minimum.
    

Cette étape fournit ainsi une preuve expérimentale que la variable :

$$  
\hat{N}  
$$

constitue une connaissance pertinente pouvant être réinjectée dans un système de raisonnement relationnel.

Elle prépare naturellement l'étape suivante :

```text
N̂ seul
      ↓
N̂ + Température
      ↓
Weak Temporal GNN
```

où le modèle devra apprendre à combiner simultanément les informations temporelles et thermiques pour reconstruire progressivement la logique clinique des critères CAUTI.







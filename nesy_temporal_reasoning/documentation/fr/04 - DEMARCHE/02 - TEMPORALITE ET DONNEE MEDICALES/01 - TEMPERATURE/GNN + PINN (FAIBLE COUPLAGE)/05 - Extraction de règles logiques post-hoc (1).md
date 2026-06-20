# Extraction de la règle logique interne du GNN

## Objectif

Après l'entraînement du GNN, plusieurs paramètres interprétables ont été identifiés dans le modèle.

Les plus importants concernent les seuils appris pour :

- la température ;
    
- le motif temporel estimé du cathéter.
    

Les valeurs apprises sont :

```text
Température : 37.20°C

N̂ : 1.13
```

Une question naturelle apparaît alors :

> Ces seuils correspondent-ils réellement à la logique décisionnelle utilisée par le modèle ?

Autrement dit :

```text
GNN

    ↓

Paramètres appris

    ↓

Règle logique
```

Cette expérience vise à reconstruire la règle interne du GNN sans utiliser aucune connaissance métier externe.

# Principe de l'expérience

L'objectif est de placer le GNN dans une situation d'explicabilité post-hoc.

Le réseau est considéré comme une boîte noire :

```text
Graphe patient

        ↓

       GNN

        ↓

 Prédiction CAUTI
```

Nous cherchons ensuite à reconstruire une règle logique expliquant ses décisions.

Pour cela, nous utilisons un algorithme d'induction de règles :

```text
RIPPER
```

qui transforme des observations en règles symboliques compréhensibles.

# Extraction des seuils appris

L'analyse du modèle entraîné montre que les paramètres optimisés par descente de gradient convergent vers :

```text
temp_threshold = 37.2048

nhat_threshold = 1.1364
```

Ces valeurs ne proviennent pas des définitions NHSN.

Elles sont apprises automatiquement à partir des données.

Elles représentent donc la connaissance découverte par le réseau.

Dans la suite de l'expérience, ces valeurs sont arrondies à :

```text
37.20°C

1.13
```

afin de faciliter l'interprétation des résultats.

# Construction du jeu logique

Pour chaque patient, deux variables continues sont extraites :

```text
Température maximale

max_temp
```

et

```text
Motif temporel estimé

N̂
```

Ces variables sont ensuite discrétisées à l'aide des seuils appris :

```text
temp_gt_37_20

nhat_gt_1_13
```

Chaque patient est alors représenté sous forme logique.

Exemple :

```text
Patient A

Température = 38.4°C
N̂ = 3.1

↓

temp_gt_37_20 = 1

nhat_gt_1_13 = 1
```

ou encore :

```text
Patient B

Température = 37.5°C
N̂ = 0.8

↓

temp_gt_37_20 = 1

nhat_gt_1_13 = 0
```

Le problème devient alors un problème d'induction logique classique.

# Apprentissage de la règle

L'algorithme RIPPER reçoit uniquement :

```text
temp_gt_37_20

nhat_gt_1_13
```

Aucune règle métier n'est fournie.

Aucun seuil NHSN n'est utilisé.

L'algorithme doit reconstruire la règle la plus simple permettant d'expliquer les labels présents dans les données.

# Résultat obtenu

La règle extraite est :

```text
[temp_gt_37_20 = 1
 ^
 nhat_gt_1_13 = 1]

OR

[temp_gt_37_20 = 1]
```

Soit :

```text
(temp_gt_37_20 ∧ nhat_gt_1_13)

∨

(temp_gt_37_20)
```

# Simplification logique

Cette expression peut être simplifiée.

En logique propositionnelle :

```text
(A ∧ B) ∨ A
```

est équivalent à :

```text
A
```

La règle finale devient donc :

```text
temp_gt_37_20 = 1
```

ou :

```text
Température > 37.20°C
```

# Interprétation

Cette expérience révèle une propriété importante du modèle.

Lorsque RIPPER ne dispose que des seuils appris par le GNN, la variable dominante devient :

```text
Température > 37.20°C
```

Le critère temporel :

```text
N̂ > 1.13
```

n'apporte pas suffisamment d'information supplémentaire pour être conservé dans la règle simplifiée.

Le moteur de règles le supprime naturellement.

Autrement dit :

```text
37.20°C
```

explique à lui seul une grande partie de la séparation observée entre patients positifs et négatifs.

Il est important de noter que cette règle ne correspond pas nécessairement à la décision complète du GNN.

Elle correspond à la meilleure approximation symbolique obtenue lorsque seules les variables issues des seuils appris sont mises à disposition du moteur logique.

# Ce que cette expérience valide

Cette expérience ne cherche pas à vérifier la cohérence médicale de la règle.

Elle cherche uniquement à reconstruire la logique interne du GNN à partir de ses propres paramètres.

Elle montre que :

```text
GNN

    ↓

Seuils appris

    ↓

Règle logique
```

peut être réalisé automatiquement.

Cette étape constitue une première validation du passage :

```text
Network

    ↓

Logic
```

identifié comme un défi majeur dans l'état de l'art du raisonnement neuro-symbolique.

L'expérience démontre qu'il est possible d'extraire une représentation symbolique directement à partir des paramètres appris par un modèle neuronal.

# Limites

Cette expérience possède néanmoins plusieurs limites.

Les variables disponibles sont extrêmement restreintes :

```text
Température

N̂
```

Le moteur de règles ne peut donc raisonner que sur ces deux informations.

Par ailleurs, RIPPER travaille sur des variables déjà discrétisées.

Toute la richesse des représentations continues apprises par le GNN est donc perdue lors de cette transformation.

Enfin, cette expérience ne permet pas encore de déterminer si les seuils appris correspondent à des connaissances médicales pertinentes.

Elle permet uniquement d'identifier la règle logique la plus proche du comportement observé du modèle.

La question de la cohérence clinique sera étudiée dans l'expérience suivante.

# Conclusion

L'induction logique réalisée à partir des seuls seuils appris par le GNN conduit à la règle :

```text
Température > 37.20°C
```

Cette règle constitue la meilleure approximation symbolique du comportement du modèle lorsque seules les connaissances découvertes automatiquement sont disponibles.

L'expérience montre qu'il est possible d'extraire une représentation logique interprétable à partir d'un réseau neuronal entraîné.

Elle fournit ainsi une première démonstration du passage :

```text
GNN

↓

Connaissances apprises

↓

Règle logique explicable
```

qui constitue l'une des étapes fondamentales du raisonnement neuro-symbolique étudié dans cette thèse.
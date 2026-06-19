# Estimation de $T$ et $\phi$ – Recherhce exhaustive

## Introduction

Les expériences précédentes ont montré que la représentation temporelle basée sur :

$$  
T = 24  
$$

et :

$$  
\phi = 0  
$$

permet de reconstruire parfaitement les labels présents dans le dataset.

Cependant, dans un contexte réel, ces paramètres ne sont généralement pas connus à l'avance.

La question devient alors :

> Est-il possible de retrouver automatiquement la période $T$ et la phase $\phi$ à partir des données seules ?

Cette section présente une première approche fondée sur une recherche exhaustive des paramètres.


# Principe général

Nous disposons des informations suivantes :

- un temps de début $t_s$ ;
    
- un temps de fin $t_e$ ;
    
- un label observé $Y$.
    

Nous supposons que le phénomène étudié repose sur une structure temporelle périodique caractérisée par :

$$  
T  
$$

et :

$$  
\phi  
$$

mais ces paramètres sont inconnus.

L'objectif consiste donc à explorer différentes combinaisons de :

$$  
(T,\phi)  
$$

et à mesurer leur capacité à reproduire les labels observés.


# Calcul du nombre d'unités temporelles

Pour chaque couple :

$$  
(T,\phi)  
$$

le nombre d'unités temporelles traversées est calculé par :

$$  
N(T,\phi)

\left\lfloor  
\frac{t_e-\phi}{T}  
\right\rfloor

\left\lfloor  
\frac{t_s-\phi}{T}  
\right\rfloor  
+1  
$$

où :

- $t_s$ représente le temps de début ;
    
- $t_e$ représente le temps de fin ;
    
- $T$ représente la période ;
    
- $\phi$ représente la phase.
    


# Fonction de prédiction

La règle NHSN impose qu'un cathéter soit présent pendant plus de deux jours calendaires.

Pour généraliser cette règle, nous utilisons :

$$
\hat{Y} =
\begin{cases}
1 & \text{si }  N > 2 \\
0 & \text{sinon}
\end{cases}
$$

Cette règle transforme chaque couple :

$$  
(T,\phi)  
$$

en un modèle de classification.



# Fonction d'évaluation

Pour chaque couple :

$$  
(T,\phi)  
$$

les prédictions sont comparées aux labels observés.

La performance est mesurée par :

$$  
Score(T,\phi)
$$$$
Accuracy(Y,\hat{Y})  
$$

où :

- $Y$ représente les labels réels ;
    
- $\hat{Y}$ représente les labels prédits.
    

L'objectif est alors de rechercher :
$$(T^_,\phi^_) =
\arg\max_{T,\phi}  
Score(T,\phi)  
$$




# Recherche exhaustive

La méthode consiste à explorer systématiquement l'espace des paramètres.

Par exemple :

$$  
T \in [1,48]  
$$

heures

et

$$  
\phi \in [0,T]  
$$

heures.

Pour chaque combinaison :

1. calculer $N(T,\phi)$ ;
    
2. générer les prédictions ;
    
3. calculer le score ;
    
4. mémoriser le résultat.
    

Cette stratégie est appelée **recherche exhaustive** (_brute force search_).

## Notebook

C:\DEVELOPPEMENT\THESE\CAUTI_RESEARCH\P4_TEMPORAL_MODULE\01B_T_AND_PHI\02_SEARCH_T_PHI_BRUT_FORCE.ipynb

# Représentation des résultats

![[Pasted image 20260614204103.png]]


![[Pasted image 20260614204122.png]]


![[Pasted image 20260614204141.png]]

# Hypothèse

Si la structure temporelle recherchée est réellement présente dans les données, ==alors un maximum local doit apparaître dans l'espace== :

$$  
(T,\phi)  
$$

Autrement dit, certaines combinaisons doivent produire des performances significativement supérieures aux autres.



# Résultats obtenus

L'application de cette méthode sur le dataset MIMIC révèle l'existence d'une région de performance maximale centrée autour de :

$$  
T = 24  
$$

et :

$$  
\phi = 0  
$$

Cette observation est cohérente avec la définition clinique des jours calendaires utilisée par le NHSN.

`Meilleur T : 24.0` 
`Meilleur phi : 0.0` 
`Meilleure acc. : 1.0000 (100.00%)`

# Interprétation

Cette expérience démontre qu'il est possible de retrouver automatiquement une structure temporelle cachée à partir des seules observations.

Le résultat est particulièrement intéressant car aucune connaissance explicite des jours calendaires n'est fournie au modèle.

Les seules informations disponibles sont :

- les temps de début ;
    
- les temps de fin ;
    
- les labels.
    

La période de 24 heures émerge alors naturellement comme la solution expliquant le mieux les données.


# Limites de l'approche

Bien que cette méthode permette de retrouver les paramètres recherchés, elle présente plusieurs limites :

- le coût de calcul augmente rapidement avec la taille de l'espace exploré ;
    
- chaque nouveau problème nécessite une nouvelle exploration exhaustive ;
    
- l'approche ne généralise pas facilement à plusieurs périodes simultanées ;
    
- elle n'est pas différentiable et ne peut donc pas être intégrée directement dans un réseau de neurones.
    

Ces limitations motivent la recherche d'une méthode d'apprentissage capable d'estimer directement les paramètres :

$$  
T  
$$

et

$$  
\phi  
$$

à partir des données.

# Conclusion

La recherche exhaustive montre qu'il est possible d'identifier automatiquement la période et la phase expliquant le mieux les labels observés.

Le maximum obtenu autour de :

$$  
T = 24  
$$

et :

$$  
\phi = 0  
$$

confirme l'existence d'une structure temporelle périodique sous-jacente dans les données.

Cette expérience constitue une preuve de concept importante : les paramètres temporels recherchés sont identifiables à partir des observations.

La section suivante étudiera une approche d'apprentissage permettant d'estimer directement ces paramètres à l'aide d'un réseau de neurones.




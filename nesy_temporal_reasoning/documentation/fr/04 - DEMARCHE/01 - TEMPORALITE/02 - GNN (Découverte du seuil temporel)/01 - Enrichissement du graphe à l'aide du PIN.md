
# Pipeline

Clinical Graph
      │
      ▼
Temporal Intervals
(ts , d)
      │
      ▼
PINN
Learn (T, φ)
      │
      ▼
Differentiable Calendar Count
N̂
      │
      ▼
==Graph Enrichment==
      │
      ▼
GNN
      │
      ▼
CAUTI Detection

- Le graphe contient les données.
- On extrait les intervalles temporels.
- Le PINN découvre la structure temporelle.
- Il produit une nouvelle connaissance $\hat{N}$.
- Cette connaissance enrichit le graphe.
- Le GNN exploite le graphe enrichi pour détecter les CAUTI.


![[../../../../figures/Pasted image 20260617113445.png]]
## Principe d'enrichissement du graphe
![[../../../../figures/Pasted image 20260617113445.png]]
L'objectif du module PINN n'est pas de produire directement une décision CAUTI ni de transmettre cette décision au modèle graphe.

Le rôle du PINN consiste à découvrir automatiquement la structure temporelle sous-jacente aux règles NHSN et à produire une variable temporelle latente interprétable :

$$
\hat N
$$

où :

$$
\hat N
=
S(t_e)
-
S(t_s)
+
1
$$

représente le nombre estimé de jours calendaires traversés par un épisode de cathétérisation.

Lors de la phase d'enrichissement, seule cette variable est injectée dans le graphe RDF sous la forme d'une nouvelle propriété associée au concept de cathétérisation.

Le graphe enrichi contient donc une information temporelle supplémentaire, mais ne reçoit aucune connaissance explicite sur la règle de décision NHSN.

En particulier, le PINN n'injecte pas :

- la probabilité de classification ;
- le score de décision ;
- la marge décisionnelle ;
- la règle :

$$
\hat N > 2
$$
![[../../../../figures/Pasted image 20260617113445.png]]
Le modèle de Graph Neural Network doit retrouver lui-même cette relation à partir de la représentation enrichie de la ==cathétérisation==.

==Ainsi, le PINN agit comme un mécanisme de découverte de connaissance temporelle tandis que le GNN conserve la responsabilité d'apprendre la règle de décision à partir du graphe enrichi.==

Cette séparation permet d'évaluer explicitement la capacité du GNN à exploiter une connaissance temporelle découverte automatiquement plutôt qu'une décision déjà calculée.
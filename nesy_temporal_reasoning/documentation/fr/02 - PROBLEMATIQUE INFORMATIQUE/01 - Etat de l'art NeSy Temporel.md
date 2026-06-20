# État de l'art

==Papier en cours de soumission:==
[[nai-paper-982.pdf]]

[https://neurosymbolic-ai-journal.com/paper/temporal-neuro-symbolic-reasoning-architectures-verifiable-and-auditable-systems-0?l=H8hHEF91zMnD707WWA8JtANsAyUIr2ckOE_jT_SMN_s]()
## Le raisonnement temporel en intelligence artificielle

De nombreux problèmes réels reposent sur des phénomènes évoluant dans le temps.

Dans ces situations, produire une prédiction correcte à un instant donné n'est pas suffisant. Le système doit également garantir que cette décision reste cohérente avec l'ensemble de l'historique des événements observés.

Le raisonnement temporel vise ainsi à prendre en compte :

- l'ordre des événements ;
    
- les relations de causalité ;
    
- les durées ;
    
- les contraintes temporelles ;
    
- l'évolution dynamique des situations observées.
    

Le survey montre que cette problématique demeure un défi majeur pour les systèmes d'intelligence artificielle modernes. Les modèles doivent non seulement apprendre à partir des données mais également maintenir une cohérence globale sur l'ensemble d'une trajectoire temporelle.


## Limites des approches purement neuronales

Les méthodes d'apprentissage profond ont démontré leur efficacité pour exploiter de grandes quantités de données séquentielles.

Les architectures récurrentes et les mécanismes d'attention permettent d'apprendre des dépendances complexes entre observations.

Cependant, ces approches reposent principalement sur l'apprentissage de corrélations statistiques.

Le survey souligne que cette nature essentiellement inductive rend difficile la garantie explicite de contraintes telles que :

- l'ordre des événements ;
    
- la causalité ;
    
- le respect de délais critiques ;
    
- la cohérence temporelle globale.
    

Les connaissances apprises restent généralement distribuées dans les paramètres du réseau et sont difficilement interprétables.


## Limites des approches symboliques

À l'inverse, les approches symboliques utilisent des représentations explicites fondées sur des règles logiques et des contraintes temporelles.

Ces méthodes permettent de spécifier précisément des propriétés temporelles et de vérifier leur respect.

Elles offrent ainsi un niveau élevé d'interprétabilité et de contrôle.

Néanmoins, le survey montre que ces approches rencontrent plusieurs difficultés :

- faible robustesse face au bruit ;
    
- difficulté à traiter des données continues ;
    
- coût computationnel important ;
    
- difficulté de passage à l'échelle.
    

Il existe donc un compromis entre capacité d'apprentissage et capacité de raisonnement explicite.


## Émergence des approches neuro-symboliques

Afin de dépasser les limites respectives des approches neuronales et symboliques, de nombreux travaux ont exploré leur combinaison.

L'objectif est de construire des systèmes capables simultanément :

- d'apprendre à partir des données ;
    
- d'utiliser des connaissances explicites ;
    
- de raisonner sur des contraintes temporelles ;
    
- de produire des explications interprétables.
    

Le survey identifie cette direction comme l'un des axes majeurs de recherche actuels en intelligence artificielle temporelle.

Les premiers travaux ont montré qu'il était possible d'intégrer des règles temporelles dans des réseaux neuronaux et, dans certains cas, d'extraire des connaissances symboliques à partir de modèles entraînés.


## Le défi du raisonnement temporel explicite

Malgré les progrès réalisés, le survey met en évidence plusieurs difficultés persistantes.

Même lorsque des contraintes symboliques sont intégrées au système, rien ne garantit systématiquement la cohérence globale des raisonnements produits.

Des travaux récents montrent que les modèles peuvent satisfaire localement certaines contraintes tout en produisant des trajectoires temporelles incohérentes à l'échelle globale.

Cette observation souligne l'importance de comprendre non seulement les prédictions produites mais également les dépendances temporelles réellement utilisées par le système.


## Interprétabilité et extraction de connaissances

L'une des évolutions importantes du domaine concerne la recherche d'explications plus fidèles aux mécanismes de décision.

Le survey souligne qu'une explication plausible n'est pas nécessairement une explication correcte.

Certaines méthodes peuvent produire des justifications convaincantes tout en reposant sur des raccourcis statistiques ou sur des concepts incorrectement appris.

Pour répondre à cette difficulté, plusieurs travaux utilisent des structures explicites telles que :

- des règles ;
    
- des graphes ;
    
- des chaînes d'inférence ;
    
- des traces de raisonnement.
    

Ces structures permettent de rendre visibles les relations causales et temporelles utilisées lors de l'inférence.

L'explication n'est alors plus produite après la décision mais devient une partie intégrante du processus de raisonnement.

## Vers des systèmes auditables

L'évolution actuelle du domaine ne vise plus uniquement l'amélioration des performances prédictives.

Le survey montre l'émergence d'une nouvelle génération de systèmes cherchant à combiner :

- apprentissage ;
    
- raisonnement ;
    
- vérification ;
    
- explicabilité ;
    
- auditabilité.
    

L'objectif est de construire des systèmes capables non seulement de prendre une décision mais également de rendre explicites les connaissances, contraintes et dépendances temporelles ayant conduit à cette décision.

Cette évolution ouvre la voie à des systèmes capables de découvrir, représenter et analyser des connaissances temporelles complexes à partir des données observées.
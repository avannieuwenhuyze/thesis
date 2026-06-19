# Ontologie du domaine CAUTI

## 1. Rôle de l'ontologie

L'ontologie constitue le socle conceptuel de l'ensemble du projet.

Son objectif est de fournir une représentation explicite et formelle des connaissances nécessaires à l'étude des infections urinaires associées aux cathéters (CAUTI).

Cette représentation permet de décrire :

- les patients ;
    
- les dispositifs médicaux ;
    
- les observations cliniques ;
    
- les événements médicaux ;
    
- les relations entre ces différents concepts.
    

L'ontologie sert ainsi de support commun à la représentation des données, à leur enrichissement progressif et à leur exploitation par les différents mécanismes d'analyse développés dans cette recherche.


# 2. Une ontologie construite progressivement

L'ontologie utilisée dans cette thèse n'est pas considérée comme un artefact figé.

Elle évolue au fur et à mesure de l'avancement du projet.

Cette évolution répond à plusieurs objectifs :

- représenter de nouveaux concepts identifiés au cours des travaux ;
    
- intégrer de nouvelles connaissances ;
    
- améliorer la représentation du temps ;
    
- faciliter l'exploration de nouvelles hypothèses scientifiques.
    

L'ontologie doit donc être vue comme une structure vivante qui accompagne le processus de recherche.


# 3. Origine du modèle conceptuel

La première version de l'ontologie a été construite à partir des concepts nécessaires à la représentation des règles NHSN relatives aux infections urinaires associées aux cathéters.

Cette modélisation s'appuie notamment sur les notions de :

- patient ;
    
- cathétérisation ;
    
- température corporelle ;
    
- culture urinaire ;
    
- symptôme ;
    
- infection associée aux soins.
    

L'objectif n'était pas de reproduire l'intégralité d'un système clinique mais de construire un modèle conceptuel suffisamment riche pour représenter les éléments nécessaires au raisonnement étudié.


# 4. Modélisation du temps

La représentation du temps constitue un élément central du projet.

En effet, les définitions NHSN reposent sur de nombreuses contraintes temporelles :

- durée de cathétérisation ;
    
- Date of Event (DOE) ;
    
- Infection Window Period (IWP) ;
    
- ordre d'apparition des événements ;
    
- relations entre événements cliniques.
    

Afin de représenter ces notions de manière explicite, nous nous sommes appuyés sur l'ontologie :
(cf notre publication > _A Conceptual Model for Discovering Implicit Temporal Knowledge in Clinical Data_ )
https://doi.org/10.1007/978-3-032-08620-4_6

```text
OWL-Time
```

développée par le W3C.

OWL-Time fournit un vocabulaire standard permettant de décrire :

- des instants ;
    
- des intervalles ;
    
- des durées ;
    
- des relations temporelles.
    

Cette approche permet d'intégrer la dimension temporelle directement dans la représentation sémantique des données.



# 6. Pourquoi ne pas utiliser SNOMED CT ?

À ce stade des travaux, nous avons volontairement choisi de ne pas intégrer de grandes ontologies médicales de référence telles que :

- SNOMED CT ;
    
- LOINC ;
    
- ICD ;
    
- FHIR Terminology Resources.
    

Cette décision repose sur plusieurs raisons.

## Simplification expérimentale

L'objectif principal de cette phase de recherche est d'étudier les mécanismes de découverte et de représentation des connaissances temporelles.

L'introduction précoce de terminologies médicales très riches aurait considérablement augmenté la complexité du modèle sans apporter d'information directement utile à la question scientifique étudiée.

## Contrôle du modèle conceptuel

Une ontologie plus légère permet de conserver une maîtrise complète des concepts et des relations utilisés dans les expérimentations.

Cette propriété facilite l'analyse des résultats et la compréhension des phénomènes observés.

## Évolution future

L'intégration de terminologies médicales standardisées reste néanmoins une perspective importante du projet.

Une fois les mécanismes fondamentaux validés, l'ontologie pourra être alignée avec des standards médicaux existants afin d'améliorer :

- l'interopérabilité ;
    
- la réutilisabilité ;
    
- l'intégration dans des environnements cliniques réels.
    

# 7. Positionnement de l'ontologie dans le projet

L'ontologie joue plusieurs rôles complémentaires :

### Représentation des connaissances

Elle formalise les concepts médicaux étudiés.

### Représentation du temps

Elle permet d'exprimer explicitement les relations temporelles grâce à OWL-Time.

### Support d'expérimentation

Elle sert de base à la génération des graphes RDF utilisés dans les différentes expérimentations.

### Support d'évolution

Elle accompagne progressivement l'enrichissement des connaissances étudiées au cours de la thèse.

# Conclusion

L'ontologie constitue le socle conceptuel de cette recherche.

Construite initialement pour représenter les concepts nécessaires à l'étude des CAUTI, elle a progressivement évolué vers un modèle intégrant explicitement les dimensions temporelles du raisonnement clinique grâce à OWL-Time.

Cette approche, présentée dans la publication _A Conceptual Model for Discovering Implicit Temporal Knowledge in Clinical Data_, fournit un cadre de représentation adapté à l'étude des connaissances temporelles implicites présentes dans les données cliniques.

L'ontologie reste volontairement légère et contrôlée afin de faciliter les expérimentations actuelles, tout en conservant la possibilité d'un alignement futur avec des standards médicaux tels que SNOMED CT ou LOINC.
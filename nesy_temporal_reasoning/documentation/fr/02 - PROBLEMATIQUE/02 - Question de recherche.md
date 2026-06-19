# Question de recherche

## 1. Positionnement par rapport à l'état de l'art

L'état de l'art réalisé dans cette thèse met en évidence plusieurs défis majeurs du raisonnement neuro-symbolique temporel.

Malgré les progrès récents de l'intelligence artificielle, plusieurs questions demeurent largement ouvertes :

- comment apprendre des connaissances temporelles qui ne sont pas directement observables dans les données ;
    
- comment intégrer ces connaissances dans des représentations structurées ;
    
- comment reconstruire des raisonnements explicables à partir des connaissances apprises ;
    
- comment transformer les représentations neuronales en connaissances interprétables par les experts.
    

Ces défis apparaissent de manière récurrente dans les différentes feuilles de route identifiées dans la littérature.

Les travaux récents convergent vers une même ambition : construire des systèmes capables non seulement de prédire mais également de représenter, expliquer et vérifier les connaissances qu'ils apprennent.

Cette évolution conduit naturellement à s'intéresser aux mécanismes permettant de découvrir des connaissances implicites puis de les réutiliser dans un processus de raisonnement.


## 2. Le domaine médical comme terrain d'expérimentation

Le domaine médical constitue un cadre particulièrement pertinent pour étudier ces questions.

De nombreuses décisions cliniques reposent sur des connaissances qui ne sont pas directement observables dans les données mais qui doivent être reconstruites à partir de multiples observations.

Les infections urinaires associées aux cathéters (CAUTI) constituent un exemple représentatif de cette situation.

Selon les définitions du NHSN, l'identification d'une CAUTI repose sur la combinaison de plusieurs types de connaissances :

- temporelles ;
    
- cliniques ;
    
- microbiologiques ;
    
- logiques.
    

Par exemple :

- présence d'un cathéter ;
    
- durée de cathétérisation ;
    
- apparition d'une fièvre ;
    
- résultats microbiologiques ;
    
- respect de contraintes temporelles spécifiques.
    

La décision finale ne correspond donc pas à une simple classification statistique mais à un véritable raisonnement clinique fondé sur des contraintes interdépendantes.

Le cas des CAUTI fournit ainsi un cadre expérimental permettant d'étudier plusieurs verrous identifiés dans l'état de l'art.


## 3. Verrou scientifique étudié

==L'un des principaux défis identifiés dans le survey concerne la découverte de connaissances temporelles implicites.==

Certaines informations nécessaires au raisonnement ne sont pas directement disponibles dans les données observées.

Elles doivent être reconstruites à partir d'événements distribués dans le temps.

Dans le domaine des CAUTI, la durée calendaire associée à une cathétérisation constitue un exemple représentatif de ce type de connaissance.

Cette information joue un rôle central dans les critères NHSN mais n'est pas toujours directement exploitable sous une forme adaptée aux systèmes d'apprentissage.

Le problème étudié dans cette thèse consiste donc à comprendre comment une connaissance temporelle latente peut être :

1. découverte à partir des données ;
    
2. représentée explicitement ;
    
3. intégrée dans une structure relationnelle ;
    
4. utilisée pour soutenir un raisonnement médical.
    


## 4. Question principale de recherche

La question centrale de cette thèse est la suivante :

==**Comment apprendre automatiquement une connaissance temporelle latente à partir de données cliniques, puis l'intégrer dans une représentation relationnelle afin de reconstruire un raisonnement médical cohérent et interprétable ?**==

Cette question peut être représentée sous la forme suivante :

```text
Observations cliniques

        ↓

Apprentissage

        ↓

Connaissance temporelle latente

        ↓

Représentation relationnelle

        ↓

Raisonnement médical
```

Cette problématique s'inscrit directement dans les axes de recherche identifiés par l'état de l'art concernant :

- la découverte de connaissances temporelles ;
    
- l'intégration neuro-symbolique ;
    
- le raisonnement sur graphes ;
    
- l'interprétabilité des connaissances apprises.
    

## 5. Sous-questions de recherche

### Q1 – Découverte de connaissances temporelles

Une architecture d'apprentissage est-elle capable d'estimer automatiquement une information temporelle non directement observable à partir des données disponibles ?

Dans notre cas :

```text
Observations temporelles

        ↓

Estimation d'une connaissance temporelle
```

Cette question répond directement au défi de découverte de connaissances temporelles identifié dans l'état de l'art.


### Q2 – Intégration dans une représentation relationnelle

Comment incorporer une connaissance temporelle apprise dans une représentation sous forme de graphe médical ?

Cette intégration doit préserver :

- la structure sémantique du domaine ;
    
- les relations entre les entités médicales ;
    
- la cohérence de la représentation.
    

Cette question rejoint les problématiques d'intégration neuro-symbolique étudiées dans la littérature.


### Q3 – Reconstruction du raisonnement médical

Un système de raisonnement sur graphe est-il capable de redécouvrir des contraintes décisionnelles utilisées par les experts à partir d'une représentation enrichie ?

Par exemple :

```text
Durée > seuil

ET

Température > seuil
```

sans que cette règle soit explicitement fournie au modèle.

Cette question s'inscrit dans les travaux visant à rapprocher apprentissage neuronal et raisonnement symbolique.


### Q4 – Interprétabilité et auditabilité

Les connaissances apprises peuvent-elles être transformées en représentations compréhensibles par les experts médicaux ?

L'objectif est de pouvoir analyser :

- les variables utilisées ;
    
- les interactions découvertes ;
    
- les dépendances temporelles apprises ;
    
- les règles émergentes.
    

Cette question répond directement aux enjeux d'explicabilité et d'auditabilité identifiés dans les feuilles de route du domaine.


## 6. Correspondance avec les défis identifiés dans l'état de l'art

Les questions de recherche abordées dans cette thèse correspondent directement aux principaux axes identifiés dans le survey.

|Défi identifié dans l'état de l'art|Question de recherche|
|---|---|
|Découverte de connaissances temporelles|Q1|
|Intégration neuro-symbolique|Q2|
|Raisonnement relationnel|Q3|
|Interprétabilité et auditabilité|Q4|

Le cas des CAUTI est ainsi utilisé comme un terrain d'expérimentation permettant d'étudier concrètement plusieurs défis majeurs du raisonnement neuro-symbolique temporel.


## 7. Hypothèse de recherche

Nous faisons l'hypothèse qu'une connaissance temporelle latente peut être découverte à partir des données cliniques disponibles puis intégrée dans une représentation relationnelle afin de soutenir l'apprentissage d'un raisonnement médical.

Nous supposons également que certaines contraintes utilisées par les experts peuvent émerger naturellement de cette représentation sans être explicitement codées.


## 8. Objectif scientifique

L'objectif de cette thèse n'est pas uniquement de prédire la présence d'une CAUTI.

L'objectif est d'étudier comment des connaissances temporelles implicites peuvent être découvertes, représentées et exploitées dans un cadre neuro-symbolique.

Le problème étudié peut être résumé ainsi :

```text
Données cliniques

        ↓

Découverte de connaissances

        ↓

Représentation relationnelle

        ↓

Raisonnement médical

        ↓

Connaissances interprétables
```

La capacité du système à retrouver des contraintes cohérentes avec les connaissances médicales existantes constitue alors un indicateur de la qualité des connaissances apprises.

Dans cette perspective, le cas des CAUTI n'est pas uniquement un problème clinique mais un cas d'étude permettant d'explorer plusieurs verrous fondamentaux du raisonnement neuro-symbolique temporel identifiés dans l'état de l'art.
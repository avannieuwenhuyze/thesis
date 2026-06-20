# De la validation du processus à l'exploration de la connaissance temporelle

## Une première phase de validation

Les travaux réalisés jusqu'à présent avaient un objectif précis :

> Vérifier qu'il est possible de construire une chaîne complète permettant de transformer une connaissance temporelle implicite en une connaissance symbolique explicable.

Pour cela, nous avons choisi un problème médical dont la structure temporelle est connue :

```text
CAUTI
```

et plus particulièrement la règle NHSN reposant sur :

```text
Température > 38°C

ET

Cathéter présent depuis plus de 2 jours calendaires
```

Ce choix n'avait pas pour objectif de découvrir une nouvelle connaissance médicale.

Au contraire, il permettait de disposer d'une vérité terrain connue afin de valider la méthodologie proposée.

L'ensemble des expérimentations a ainsi permis de démontrer qu'il est possible de réaliser la chaîne suivante :

```text
Événements cliniques

↓

Découverte d'une connaissance temporelle

↓

Intégration dans un graphe

↓

Apprentissage relationnel

↓

Extraction de règles

↓

Comparaison aux connaissances expertes
```

Cette validation constitue le principal résultat obtenu à ce stade de la recherche.

## Ce qui a réellement été démontré

Il serait tentant d'interpréter les résultats obtenus comme une démonstration que le système a redécouvert les règles NHSN.

Ce n'est cependant pas l'élément le plus important.

La véritable contribution de cette première phase réside dans la validation du processus suivant :

```text
Temps implicite

↓

Temps appris

↓

Temps représenté

↓

Temps utilisé

↓

Temps expliqué
```

==Plus précisément, nous avons montré qu'une information temporelle absente des données RDF initiales pouvait être :==

1. ==découverte automatiquement ;==
    
2. ==représentée sous une forme interprétable ;==
    
3. ==injectée dans une structure relationnelle ;==
    
4. ==exploitée par un modèle d'apprentissage ;==
    
5. ==transformée en représentation symbolique explicable.==
    

==Cette démonstration constitue une preuve de faisabilité du cadre conceptuel proposé dans cette thèse.==

## Pourquoi cette étape était indispensable

La règle retrouvée :

```text
Température > 38°C

ET

N̂ > 2 jours
```

n'est pas une découverte scientifique.

Elle est déjà connue des experts médicaux.

Le dataset utilisé dans les expérimentations a lui-même été construit à partir de ces critères.

Dans ce contexte, retrouver cette règle est un résultat attendu.

Cependant, ce résultat possède une valeur méthodologique importante.

Il démontre que l'ensemble de la chaîne :

```text
Apprentissage

↓

Raisonnement

↓

Explication
```

fonctionne correctement.

Avant de chercher à découvrir de nouvelles connaissances temporelles, il était nécessaire de vérifier que le système était capable de reconstruire une connaissance déjà validée par les experts.

Cette étape constitue donc un mécanisme de validation plutôt qu'un objectif final.

## Le véritable sujet de la thèse

L'enjeu scientifique principal n'est pas la reconstruction d'une règle CAUTI.

L'enjeu est beaucoup plus général.

La question centrale devient :

> ==Comment découvrir automatiquement des connaissances temporelles pertinentes à partir de données cliniques ?==

Le nombre de jours calendaires représente seulement un premier exemple de connaissance temporelle.

Il a été choisi car :

- il est interprétable ;
    
- il possède une définition clinique claire ;
    
- il permet une validation expérimentale rigoureuse.
    

Mais il ne constitue qu'un cas particulier.

Le véritable objet de la thèse est l'étude des mécanismes permettant de faire émerger des concepts temporels à partir de données observées.

## Du temps observé au temps appris

Les données cliniques contiennent généralement des événements :

```text
Pose d'un cathéter

Retrait d'un cathéter

Mesure d'une température

Administration d'un traitement

Résultat biologique
```

Ces événements sont observables.

==En revanche, les connaissances temporelles qui relient ces événements sont rarement explicites.==

Par exemple :

```text
Durée d'exposition

Persistance d'un symptôme

Temps cumulé sous traitement

Vitesse d'évolution clinique

Trajectoire physiologique
```

ne sont généralement pas directement disponibles.

L'objectif de la thèse est précisément de transformer :

```text
Événements observés

↓

Connaissances temporelles explicites
```

Cette transformation constitue le cœur scientifique du projet.

## Les perspectives de recherche

La validation obtenue ouvre désormais plusieurs axes de recherche.

### Découverte de nouvelles représentations temporelles dans le cadre CAUTI

==Le nombre de jours calendaires de cathétérisation constitue un premier concept temporel.==

Il a permis de valider le processus général :

```text
événement clinique
    ↓
connaissance temporelle apprise
    ↓
raisonnement
```

Cependant, les définitions CAUTI ne reposent pas uniquement sur cette durée.

Elles mobilisent plusieurs temporalités imbriquées :

- durée de présence du cathéter ;
    
- délai entre retrait du cathéter et apparition des critères ;
    
- position du Date of Event ;
    
- cohérence des critères dans l'Infection Window Period ;
    
- délai entre culture urinaire et symptômes ;
    
- apparition d'un nouvel épisode après le Repeat Infection Timeframe.
    

La suite du travail consistera donc à rester dans le domaine CAUTI, mais à enrichir progressivement l'analyse temporelle.

L'objectif n'est plus seulement d'apprendre :

```text
N̂ = nombre de jours calendaires
```

mais d'étudier si d'autres représentations temporelles utiles au raisonnement CAUTI peuvent émerger des données.

Par exemple :

```text
temps depuis la pose du cathéter
temps depuis le retrait du cathéter
distance temporelle entre symptôme et culture
position d'un événement dans l'IWP
présence ou absence du cathéter au DOE
continuité ou interruption de l'exposition
```

Ces représentations restent directement liées aux règles NHSN, mais elles permettent d'aller au-delà du seul seuil `N̂ > 2`.

### Raisonnement temporel CAUTI sur graphes

Une seconde perspective consiste à enrichir progressivement le graphe CAUTI avec plusieurs connaissances temporelles simultanées.

Le graphe ne représenterait plus uniquement :

```text
Patient
├── Température
└── Durée de cathétérisation
```

mais un ensemble de relations temporelles structurées autour de l'épisode suspect :

```text
Patient
├── Catheterization
│   ├── durée estimée
│   ├── date de pose
│   ├── date de retrait
│   └── présence au DOE
│
├── BodyTemperature
│   ├── valeur maximale
│   └── position dans l'IWP
│
├── UrineCulture
│   ├── date du prélèvement
│   ├── seuil microbiologique
│   └── position dans l'IWP
│
├── Symptom
│   ├── type de symptôme
│   └── distance temporelle au DOE
│
└── InfectionWindowPeriod
    ├── début
    ├── fin
    └── événements inclus
```

Dans cette perspective, le GNN ne chercherait plus seulement à apprendre une combinaison simple :

```text
Température > 38°C
ET
N̂ > 2
```

mais à raisonner sur la cohérence temporelle d'un épisode CAUTI complet.

Il pourrait apprendre à combiner :

- l'exposition au cathéter ;
    
- la position des symptômes ;
    
- la date de culture ;
    
- la fenêtre d'infection ;
    
- la présence du cathéter au moment pertinent ;
    
- les relations entre événements.
    

Cette orientation replace pleinement le projet dans son objectif initial : étudier le raisonnement temporel médical à partir d'un cas d'étude CAUTI, en allant progressivement d'une règle simple vers un raisonnement temporel plus complet.

### Intégration d'OWL-Time

Les premiers travaux de cette thèse se sont déjà appuyés sur les concepts proposés dans :

```text
OWL-Time
```

Une perspective naturelle consiste à renforcer cette intégration afin de représenter explicitement :

- intervalles ;
    
- durées ;
    
- relations temporelles ;
    
- événements ;
    
- contraintes temporelles complexes.
    

Cette évolution rapprocherait davantage le système des approches neuro-symboliques temporelles identifiées dans le survey.

### Découverte automatique de connaissances médicales

À terme, l'objectif n'est plus seulement de retrouver des règles existantes.

Il s'agit de permettre la découverte de connaissances qui ne sont pas encore formalisées.

Le pipeline deviendrait alors :

```text
Événements

↓

Découverte temporelle

↓

Apprentissage relationnel

↓

Extraction logique

↓

Nouvelle connaissance
```

Le système passerait d'un rôle de validation à un rôle de génération de connaissances.

## Positionnement dans la feuille de route scientifique

La première phase de cette recherche peut être résumée par :

```text
Validation du processus
```

La seconde phase peut être formulée comme :

```text
Exploration de la connaissance temporelle
```

Cette transition marque un changement important.

Jusqu'à présent, la question était :

```text
Le cadre proposé fonctionne-t-il ?
```

Les résultats obtenus permettent désormais de répondre positivement.

La question devient maintenant :

```text
Quelles connaissances temporelles
peuvent être découvertes automatiquement ?
```

C'est cette problématique qui constitue le cœur scientifique de la suite de la thèse.

## Conclusion

Les travaux réalisés jusqu'à présent ont permis de valider une chaîne complète allant de la découverte d'une connaissance temporelle jusqu'à son explication sous forme symbolique.

Cette validation représente une étape essentielle mais ne constitue pas l'objectif final du projet.

L'ambition de la thèse est désormais de dépasser la simple reconstruction d'une règle connue pour explorer les mécanismes permettant de découvrir de nouvelles connaissances temporelles à partir de données cliniques complexes.

La suite des travaux se concentrera donc davantage sur la temporalité elle-même :

```text
Temps observé

↓

Temps appris

↓

Temps représenté

↓

Temps raisonné

↓

Temps expliqué
```

Cette progression constitue désormais le fil conducteur de la recherche et le principal axe d'originalité scientifique du projet.
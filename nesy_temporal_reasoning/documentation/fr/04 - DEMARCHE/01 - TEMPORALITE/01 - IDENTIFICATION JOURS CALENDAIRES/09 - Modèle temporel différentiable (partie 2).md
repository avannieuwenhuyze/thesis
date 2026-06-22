
Nous avons entrainer un modèle PINN afin d'étudier son comportement.
Le notebook associé est: C:\DEVELOPPEMENT\THESE\CAUTI_RESEARCH\P4_TEMPORAL_MODULE\02_MODELISATION\notebook\NB_pinn_temporal_mimic.ipynb

## Préparation du dataset d'apprentissage

Le modèle est entraîné à partir du fichier `temporal_dataset.csv`, qui contient pour chaque épisode de cathétérisation les dates de pose et de retrait du cathéter ainsi que le label associé à la règle NHSN.

Une étape de prétraitement est réalisée avant l'apprentissage afin de transformer les dates absolues en variables temporelles plus adaptées à l'optimisation. Les colonnes `t_start` et `t_end` sont d'abord converties au format datetime. Une origine temporelle locale est ensuite définie pour chaque observation en utilisant le minuit correspondant à la date de début de l'épisode :

$$  
t_0 = \text{date}(t_{start})\ 00{:}00  
$$

Le temps de début local est alors calculé par :
$$  
t_{start}^{local}
$$
$$
t_{start}
-
t_0
$$




Cette transformation garantit que :

$$  
0 \leq t_{start}^{local} < 24  
$$

pour l'ensemble des observations du dataset.

Contrairement à une représentation basée sur une date de référence globale, cette approche conserve uniquement l'information pertinente pour l'apprentissage de la structure périodique recherchée. Le modèle n'a ainsi plus à manipuler des dates absolues très éloignées dans le temps mais uniquement la position d'un événement à l'intérieur d'une journée.

La durée de l'épisode est conservée dans la variable :

$$  
duration_h  
$$

exprimée en heures. Le label cible est stocké dans la variable :

$$  
y  
$$

qui correspond à l'application de la règle NHSN fondée sur le nombre de jours calendaires traversés.

Après prétraitement, le dataset utilisé par le PINN contient les variables suivantes :

|Variable|Description|
|---|---|
|`t_start_local`|Heure locale de début de l'épisode|
|`duration_h`|Durée de l'épisode en heures|
|`n_calendar_days`|Nombre exact de jours calendaires traversés|
|`y`|Label cible utilisé pour l'apprentissage|
(n_calendar_days servira par la suite à la stratification du dataset)

Cette représentation fournit au modèle uniquement les informations nécessaires à la découverte des paramètres temporels latents $(T,\phi)$ gouvernant le calcul des jours calendaires NHSN.

## Construction des ensembles d'entraînement et de validation

L'objectif de cette étape est de construire des ensembles d'entraînement et de validation présentant des distributions similaires afin d'évaluer correctement la capacité de généralisation du modèle.

Une première difficulté provient du fait que les classes positives et négatives ne sont pas réparties uniformément selon le nombre de jours calendaires traversés. Un découpage aléatoire classique pourrait conduire à une surreprésentation de certaines situations dans l'ensemble d'entraînement ou dans l'ensemble de validation.

Pour limiter ce risque, une variable de stratification est construite à partir de deux informations :

- le label cible :
    

$$  
y  
$$

- ==le nombre de jours calendaires== :
    

$$  
N  
$$

Les valeurs élevées de :

$$  
N  
$$

sont regroupées dans une même catégorie afin d'éviter la création de groupes contenant trop peu d'observations. Plus précisément :

$$  
N_{group} = 

\min(N,10)  
$$

Chaque observation est ensuite associée à une strate définie par la combinaison :

$$  
(y,N_{group})  
$$

Par exemple :

|Label|Jours calendaires|Strate|
|---|---|---|
|0|1|0_N1|
|0|2|0_N2|
|1|3|1_N3|
|1|8|1_N8|
|1|15|1_N10|

Les strates contenant moins de deux observations sont retirées afin de garantir qu'au moins un échantillon puisse être présent dans chacun des ensembles.

Le découpage final est ensuite réalisé par échantillonnage stratifié avec :

$$  
80%  
$$

des observations pour l'entraînement et :

$$  
20%  
$$

pour la validation.

Cette stratégie garantit que les distributions conjointes des labels et du nombre de jours calendaires restent comparables entre les deux ensembles. Le modèle est ainsi évalué sur des observations représentatives de la diversité présente dans le jeu de données tout en évitant les biais liés à un déséquilibre de distribution.



# Résultats d'apprentissage

## Stratégie d'entraînement multi-start

L'optimisation du modèle repose sur une procédure dite **multi-start**. Cette approche consiste à entraîner plusieurs modèles indépendants à partir d'initialisations différentes des paramètres.

Dans notre expérimentation :

$$  
n_{starts} = 5  
$$

Chaque exécution démarre avec des valeurs initiales différentes pour :

$$  
T  
$$

$$  
\phi  
$$

$$  
\theta  
$$

et :

$$  
\gamma  
$$

Cette stratégie permet de limiter le risque de convergence vers un minimum local défavorable et d'évaluer la robustesse de l'apprentissage.

À l'issue de l'entraînement, le modèle présentant les meilleures performances sur l'ensemble de validation est conservé.

## Meilleur modèle obtenu

Le meilleur résultat a été obtenu pour :

$$  
seed = 0  
$$

Les paramètres appris sont :
(CF C:\DEVELOPPEMENT\THESE\CAUTI_RESEARCH\P4_TEMPORAL_MODULE\02_MODELISATION\notebook\pinn\pinn_metrics.json)

|Paramètre|Valeur|
|---|--:|
|$T_0$|40.69 h|
|$\phi_0$|30.84 h|
|$T$|24.18 h|
|$\phi$|23.91 h|
|$\phi \bmod T$|23.91 h|
|Distance de $\phi$ à 0 modulo $T$|0.27 h|
|$\theta$|2.48|
|$\gamma$|11.62|
|$\beta$|10|


## Découverte de la période temporelle

Le modèle était initialisé avec une période :

$$  
T_0 = 

40.69\ h  
$$

Après apprentissage, celui-ci converge vers :

$$  
T=

24.18\ h  
$$

Cette valeur est extrêmement proche de la période théorique attendue :

$$  
T=

24\ h  
$$

définissant le jour calendaire NHSN.

Ce résultat indique que le modèle est capable de retrouver automatiquement la périodicité sous-jacente présente dans les données sans qu'elle lui soit explicitement fournie.



## Découverte de la phase temporelle

La phase apprise vaut :

$$  
\phi=

23.91\ h  

$$

Cependant, dans un système périodique, les valeurs :

$$  
\phi = 0  
$$

et :

$$  
\phi = T  
$$

sont équivalentes.

La quantité pertinente est donc :

$$  
\phi = 
\bmod T  
$$

La distance entre la phase apprise et une frontière située à minuit est :

$$  
0.27\ h  
$$

soit environ :

$$  
16\ minutes  
$$

Le modèle retrouve ainsi une frontière temporelle pratiquement alignée avec la définition officielle du jour calendaire NHSN.


## Découverte du seuil décisionnel

Le seuil appris vaut :


$$  
\theta=

2.48  
$$

Cette valeur se situe naturellement entre :

$$  
2  
$$

et :

$$  
3  
$$

jours calendaires.

Elle est cohérente avec la règle NHSN utilisée pour construire les labels :

$$  
N > 2  
$$

Le modèle découvre donc automatiquement une frontière de décision compatible avec la logique métier sans que cette valeur ne soit imposée pendant l'apprentissage.


## Évolution de la pente de décision

La pente apprise vaut :

$$  
\gamma =

11.62  
$$

Cette valeur relativement élevée indique que la transition entre les classes positives et négatives devient très abrupte autour du seuil :

$$  
\theta  
$$

Le comportement du modèle se rapproche ainsi progressivement d'une règle de décision discrète tout en restant entièrement différentiable.


## Performances de classification

Les performances obtenues sur l'ensemble de validation sont :

|Métrique|Valeur|
|---|--:|
|Loss|0.0067|
|Accuracy|99.82 %|
|Precision|100.00 %|
|Recall|99.69 %|
|F1-score|99.85 %|

Ces résultats montrent que le modèle est capable de reproduire avec une très grande fidélité les labels générés à partir de la règle NHSN.


## Interprétation scientifique

L'analyse des paramètres appris met en évidence trois résultats majeurs.

Premièrement, le modèle retrouve une période proche de :

$$  
24\ h  
$$

correspondant au jour calendaire NHSN.

Deuxièmement, il identifie une phase équivalente à une frontière située à minuit.

Troisièmement, il apprend un seuil de décision situé entre deux et trois jours calendaires, cohérent avec la règle :

$$  
N > 2  
$$

Ces observations suggèrent que le modèle n'apprend pas uniquement à prédire un label de classification, mais qu'il reconstruit effectivement la structure temporelle sous-jacente utilisée pour le calcul des jours calendaires.

# Analyses

## Figure 1 - Convergence de la période temporelle et de la fonction de perte

![[20260617092523.png]]

Cette figure présente l'évolution simultanée de la fonction de perte et de la période temporelle apprise :

$$  
T  
$$

au cours de l'entraînement du PINN.

La courbe bleue représente la perte d'entraînement tandis que la courbe bleu foncé représente la perte de validation. La courbe rouge correspond à la période temporelle apprise par le modèle. La ligne pointillée noire indique la valeur théorique attendue :

$$  
T = 24\ h  
$$

correspondant à la définition d'un jour calendaire NHSN.

On observe une diminution rapide de la fonction de perte durant les premières itérations d'apprentissage. Cette décroissance s'accompagne d'une convergence progressive de la période temporelle. Initialement, le modèle démarre avec une valeur éloignée de la solution attendue :

$$  
T_0 =

40.69\ h  
$$

Au fil de l'optimisation, cette valeur se rapproche rapidement de :

$$  
24\ h  
$$

puis se stabilise autour de :

$$  
T=

24.18\ h  
$$

La stabilisation simultanée de la perte et du paramètre :

$$  
T  
$$

suggère que la structure temporelle journalière constitue une solution optimale pour expliquer les observations présentes dans les données.

Cette figure montre que le modèle ne se contente pas d'apprendre une frontière de classification. Il découvre progressivement une périodicité proche du jour calendaire utilisé par les règles NHSN, uniquement à partir des intervalles temporels observés et des labels associés.

## Figure 2 - Séparation des classes et apprentissage du seuil décisionnel

![[20260617092640.png]]

Cette figure présente l'évolution conjointe du nombre moyen d'unités temporelles estimées :

$$  
\hat N  
$$

pour les deux classes du problème ainsi que la position du seuil de décision appris :

$$  
\theta  
$$

La courbe rouge correspond à la moyenne de :

$$  
\hat N  
$$

pour les observations négatives :

$$  
Y=0  
$$

tandis que la courbe verte représente la moyenne observée pour les observations positives :

$$  
Y=1  
$$

La courbe noire indique la valeur du seuil appris par le modèle au cours de l'entraînement.

On observe qu'après quelques itérations seulement, les deux populations deviennent clairement séparées. Les observations négatives se stabilisent autour de :

$$  
\hat N  
\approx  
1.8  
$$

alors que les observations positives se situent autour de :

$$  
\hat N  
\approx  
4.6  
$$

Le seuil appris converge quant à lui vers :

$$  
\theta  
\approx  
2.48  
$$

et demeure positionné entre les deux distributions pendant toute la phase de convergence.

Cette observation est particulièrement importante. Le modèle n'apprend pas directement une frontière de classification arbitraire. Il construit d'abord une variable temporelle latente :

$$  
\hat N  
$$

qui représente une estimation continue du nombre de jours calendaires traversés. La séparation entre les classes apparaît alors naturellement dans cet espace latent.

La position du seuil :

$$  
\theta  
\approx  
2.48  
$$

est cohérente avec la règle NHSN utilisée pour générer les labels :

$$  
N > 2  
$$

En effet, le modèle manipule une approximation continue :

$$  
\hat N  
$$

et non le nombre discret exact :

$$  
N  
$$

Il est donc attendu que le seuil optimal se situe légèrement au-dessus de :

$$  
2  
$$

tout en restant compris entre :

$$  
2  
$$

et :

$$  
3  
$$

Cette figure montre que le PINN ne se contente pas de découvrir une période temporelle. ==Il apprend également une représentation latente du comptage des jours calendaires== dans laquelle les classes positives et négatives deviennent naturellement séparables. La règle décisionnelle NHSN émerge alors comme une conséquence de cette représentation plutôt que comme une contrainte imposée explicitement au modèle.

## Figure 3 - Convergence des paramètres physiques et décisionnels

![[20260617092810.png]]

Cette figure présente l'évolution simultanée des quatre paramètres principaux appris par le modèle :

$$  
T  
$$

$$  
\phi  
$$

$$  
\theta  
$$

et :

$$  
\gamma  
$$

Les courbes rouge et orange correspondent respectivement à la période temporelle :

$$  
T  
$$

et à la phase :

$$  
\phi  
$$

apprises par le modèle. La courbe noire représente le seuil de décision :

$$  
\theta  
$$

tandis que la courbe turquoise représente la pente décisionnelle :

$$  
\gamma  
$$

Une ligne pointillée horizontale indique la valeur théorique :

$$  
T = 24\ h  
$$

attendue pour un jour calendaire NHSN.


### Convergence de la période temporelle

Au début de l'entraînement, la période est initialisée à une valeur éloignée de la solution recherchée :


$$  
T_0 =

40.69\ h  
$$

L'optimisation conduit rapidement le modèle vers une solution stable :

$$  
T  
\approx  
24.18\ h  
$$

Cette convergence constitue un premier indice que le modèle découvre effectivement la structure périodique présente dans les données.

### Convergence de la phase

La phase suit une dynamique similaire.

Après quelques dizaines d'itérations, elle se stabilise autour de :

$$  
\phi  
\approx  
23.91\ h  
$$

Cette valeur est particulièrement intéressante car :

$$  
23.91  
\equiv  
-0.27  
\pmod{24.18}  
$$

Autrement dit, la frontière temporelle apprise se situe à environ :

$$  
16\ minutes  
$$

de la frontière théorique correspondant à minuit.

Le modèle retrouve donc simultanément :

- la durée d'un cycle ;
    
- la position des frontières du cycle.
    


### Convergence du seuil décisionnel

La courbe noire montre l'évolution du seuil :

$$  
\theta  
$$

Ce paramètre converge rapidement vers :

$$  
\theta  
\approx  
2.48  
$$

Cette valeur se situe naturellement entre :

$$  
2  
$$

et :

$$  
3  
$$

jours calendaires.

Elle est cohérente avec la règle NHSN utilisée pour construire les labels :

$$  
N > 2  
$$

Le modèle apprend ainsi automatiquement la frontière de décision la plus adaptée à la représentation continue :

$$  
\hat N  
$$

qu'il construit lui-même.


### Évolution de la pente décisionnelle

La courbe turquoise représente :

$$  
\gamma  
$$

qui contrôle la raideur de la fonction de décision :


$$  
score = 

\gamma  
(\hat N-\theta)  
$$

On observe une augmentation progressive de ce paramètre au cours de l'entraînement.

La valeur finale atteint environ :

$$  
\gamma  
\approx  
11.6  
$$

Cette croissance traduit une confiance croissante du modèle dans la séparation entre les classes. Plus :

$$  
\gamma  
$$

augmente, plus la transition autour du seuil :

$$  
\theta  
$$

devient abrupte.

Le comportement du modèle se rapproche alors progressivement d'une règle discrète tout en restant entièrement différentiable.


### Interprétation scientifique

Cette figure montre que les quatre paramètres convergent simultanément vers des valeurs cohérentes avec la connaissance métier NHSN.

Le modèle découvre :

$$  
T  
\approx  
24\ h  
$$

pour la périodicité du jour calendaire,

$$  
\phi  
\approx  
0  
\pmod T  
$$

pour l'alignement des frontières sur minuit,

$$  
\theta  
\approx  
2.5  
$$

pour la frontière de décision correspondant à la règle :

$$  
N > 2  
$$

et une valeur élevée de :

$$  
\gamma  
$$

garantissant une séparation nette entre les classes.

Cette convergence conjointe constitue un argument fort en faveur de l'interprétabilité du modèle. Contrairement à un réseau neuronal classique dont les paramètres internes sont difficiles à interpréter, chaque paramètre appris possède ici une signification temporelle ou décisionnelle directement reliée aux règles cliniques étudiées.

## Figure 4 — Validation de l'approximation continue du comptage NHSN

![[20260617093009.png]]

Cette figure compare le nombre exact de jours calendaires calculé par la règle NHSN :

$$  
N_{exact}  
$$

à l'estimation continue produite par le modèle :

$$  
\hat N  
$$

Chaque point représente une observation du jeu de données.

L'axe horizontal correspond au nombre exact de jours calendaires traversés :

$$  
N_{exact}  
$$

calculé à partir de la formule NHSN :

$$  
N_{exact}=
\left\lfloor  
\frac{t_e-\phi}{T}  
\right\rfloor

\left\lfloor  
\frac{t_s-\phi}{T}  
\right\rfloor  
+  
1  
$$

L'axe vertical représente la quantité différentiable apprise par le modèle :

$$  
\hat N  
$$

Les points verts correspondent aux observations positives :

$$  
Y = 1  
$$

tandis que les points rouges correspondent aux observations négatives :

$$  
Y = 0  
$$

La diagonale noire représente la situation idéale :

$$  
\hat N  
  
\approx  
  
N_{exact}  
$$

### Fidélité de l'approximation continue

On observe que la quasi-totalité des observations se situe directement sur la diagonale.

Autrement dit :

$$  
\hat N  
\approx  
N_{exact}  
$$

pour l'ensemble des valeurs observées dans le jeu de données.

Cette observation est remarquable car :

$$  
N_{exact}  
$$

est construit à partir d'opérations de partie entière :

$$  
\lfloor \cdot \rfloor  
$$

qui sont discontinues et non différentiables.

Le modèle parvient pourtant à reconstruire cette fonction à l'aide d'une approximation entièrement différentiable basée sur une somme de sigmoïdes.

### Validation de la relaxation physique

Le principe fondamental du modèle consiste à remplacer les sauts discrets NHSN par une fonction continue :

$$  
S(t)=

\sum_k  
\sigma  
\left(  
\beta  
(t-(\phi+kT))  
\right)  
$$

et à calculer :

$$  
\hat N =

S(t_e)-S(t_s)+1  
$$

Cette figure démontre expérimentalement que cette relaxation conserve pratiquement toute l'information contenue dans la règle originale.

L'écart entre :

$$  
\hat N  
$$

et :

$$  
N_{exact}  
$$

reste extrêmement faible sur l'ensemble des observations.

Le modèle apprend donc une représentation continue fidèle de la règle discrète NHSN.


### Émergence naturelle du seuil décisionnel

La ligne horizontale représente le seuil appris :

$$  
\theta =

2.48  
$$

On constate que :

- les observations négatives sont majoritairement situées sous ce seuil ;
    
- les observations positives sont situées au-dessus.
    

Le seuil appris apparaît donc naturellement au niveau de la transition entre les épisodes traversant deux jours calendaires et ceux traversant trois jours calendaires.

Cette observation est cohérente avec la règle métier :

$$  
N > 2  
$$

utilisée pour construire les labels.


### Interprétation scientifique

Cette figure constitue une validation directe de l'hypothèse centrale du modèle.

Le PINN ne se contente pas d'apprendre une frontière de classification.

Il découvre une variable latente :

$$  
\hat N  
$$

qui reproduit presque parfaitement le nombre exact de jours calendaires NHSN.

Autrement dit, le réseau apprend à reconstruire la structure temporelle utilisée par la règle clinique avant même de prendre une décision de classification.

Cette propriété distingue le modèle d'un classifieur classique de type boîte noire. La variable :

$$  
\hat N  
$$

possède ici une interprétation clinique explicite et directement vérifiable.

La figure apporte ainsi une preuve expérimentale que le modèle a effectivement découvert le mécanisme de comptage temporel sous-jacent aux décisions NHSN.

## Figure 5 — Relaxation continue de la fonction discrète NHSN


![[20260617093615.png]]

Cette figure illustre le mécanisme central du modèle proposé. Elle compare la fonction de comptage discrète utilisée par la règle NHSN à son approximation continue et différentiable apprise par le PINN.

La courbe noire représente la fonction discrète :

$$  
F(t) =

\left\lfloor  
\frac{t-\phi}{T}  
\right\rfloor  
$$

Cette fonction augmente d'une unité chaque fois qu'une frontière temporelle est franchie.

Les lignes verticales pointillées indiquent les frontières temporelles définies par :

$$  
t = 

\phi + kT  
$$

où :

$$  
k  
\in  
\mathbb{Z}  
$$

Dans le cas présent, le modèle a appris :

$$  
T  
\approx  
24.18\ h  
$$

et :

$$  
\phi  
\approx  
23.91\ h  
$$

Les sauts observés aux alentours de :

$$  
24\ h  
$$

$$  
48\ h  
$$

et :

$$  
72\ h  
$$

correspondent donc aux frontières des jours calendaires découvertes automatiquement par le modèle.


### Approximation continue des frontières

La courbe turquoise représente la fonction continue construite par le réseau :

$$  
S(t) = 

\sum_k  
\sigma  
\Big(  
\beta  
\big(  
t-(\phi+kT)  
\big)  
\Big)  
$$

où :

$$  
\sigma(x) = 

\frac{1}{1+e^{-x}}  
$$

désigne la fonction sigmoïde.

Chaque frontière temporelle est ainsi remplacée par une transition continue.

Lorsque :

$$  
t  
<  
\phi+kT  
$$

la contribution de la sigmoïde est proche de :

$$  
0  
$$

Lorsque :

$$  
t

\phi+kT  
$$

la contribution devient proche de :

$$  
1  
$$

La somme de ces contributions reproduit progressivement le comportement de la fonction discrète.


### Rôle du paramètre β

La qualité de l'approximation est contrôlée par :

$$  
\beta  
$$

qui détermine la raideur des transitions.

Lorsque :

$$  
\beta  
\rightarrow  
+\infty  
$$

la sigmoïde converge vers une fonction échelon et l'on obtient :

$$  
S(t)  
\rightarrow  
\left\lfloor  
\frac{t-\phi}{T}  
\right\rfloor  
$$

Dans notre expérimentation :

$$  
\beta = 

10  
$$

ce qui produit des transitions suffisamment abruptes pour reproduire fidèlement la règle NHSN tout en conservant des gradients exploitables pendant l'apprentissage.


### Pourquoi cette approximation est nécessaire

La fonction partie entière :

$$  
\left\lfloor x \right\rfloor  
$$

est discontinue.

Sa dérivée est nulle presque partout et n'est pas définie aux points de saut.

Par conséquent, il est impossible d'utiliser directement une procédure d'optimisation par descente de gradient pour apprendre :

$$  
T  
$$

et :

$$  
\phi  
$$

à partir de cette formulation.

La relaxation continue remplace ces discontinuités par des sigmoïdes différentiables, permettant au gradient de circuler jusqu'aux paramètres temporels.

Le modèle peut alors ajuster progressivement :

$$  
T  
$$

et :

$$  
\phi  
$$

afin de minimiser l'erreur de classification.


### Validation expérimentale de la relaxation

On observe sur la figure que la courbe continue suit presque parfaitement la fonction discrète.

Les écarts n'apparaissent qu'au voisinage immédiat des frontières temporelles, c'est-à-dire précisément dans les zones où la dérivabilité est nécessaire à l'apprentissage.

Cette observation montre que la relaxation conserve l'information temporelle portée par la règle NHSN tout en transformant un problème discret non optimisable en un problème continu compatible avec les méthodes modernes d'apprentissage profond.


### Interprétation scientifique

Cette figure constitue le cœur mathématique du modèle.

Elle montre comment une règle clinique discrète de type :

$$  
N =

\left\lfloor  
\frac{t_e-\phi}{T}  
\right\rfloor

\left\lfloor  
\frac{t_s-\phi}{T}  
\right\rfloor  
+  
1  
$$

peut être transformée en une représentation différentiable :

$$  
\hat N =

S(t_e)

-

S(t_s)

+

1
$$

tout en conservant son interprétation clinique.

Cette relaxation est précisément ce qui permet au PINN de découvrir automatiquement la structure temporelle sous-jacente aux jours calendaires NHSN par descente de gradient.

# Conclusion

L'objectif de ce travail était de déterminer s'il était possible de retrouver automatiquement la notion de jour calendaire utilisée par les règles NHSN à partir de simples observations temporelles.

Pour répondre à cette question, nous avons formulé le problème comme un problème inverse de découverte de structure temporelle. Plutôt que d'imposer explicitement la définition d'un jour calendaire, nous avons laissé le modèle apprendre directement les paramètres gouvernant cette structure.

Le modèle proposé repose sur une représentation temporelle locale utilisant :

$$
t_s
$$

et :

$$
d
$$

comme variables d'entrée.

À partir de ces informations, le réseau apprend automatiquement :

$$
T
$$

la période fondamentale,

$$
\phi
$$

la position des frontières temporelles,

$$
\theta
$$

le seuil de décision,

et :

$$
\gamma
$$

la pente de la fonction de décision.

La principale difficulté provenait du caractère discret de la règle NHSN. Le calcul exact du nombre de jours calendaires repose sur une fonction de partie entière :

$$
N
=
\left\lfloor
\frac{t_e-\phi}{T}
\right\rfloor
-
\left\lfloor
\frac{t_s-\phi}{T}
\right\rfloor
+
1
$$

qui n'est pas différentiable et ne peut donc pas être optimisée directement par descente de gradient.

Pour contourner cette limitation, nous avons introduit une relaxation continue fondée sur une somme de fonctions sigmoïdes :

$$
S(t)
=
\sum_k
\sigma
\Big(
\beta
\big(
t-(\phi+kT)
\big)
\Big)
$$

permettant de définir une approximation différentiable :

$$
\hat N
=
S(t_e)
-
S(t_s)
+
1
$$

du nombre exact d'unités temporelles traversées.

Les résultats expérimentaux montrent que cette approximation reproduit presque parfaitement la règle discrète NHSN.

Le modèle converge vers :

$$
T
=
24.18\ h
$$

ce qui correspond à la période attendue d'un jour calendaire.

La phase apprise satisfait :

$$
\phi
\equiv
0
\pmod T
$$

avec une erreur inférieure à vingt minutes par rapport à une frontière située à minuit.

Le seuil appris :

$$
\theta
=
2.48
$$

se positionne naturellement entre deux et trois jours calendaires, ce qui est cohérent avec la règle métier utilisée pour générer les labels.

Enfin, les performances obtenues sur l'ensemble de validation atteignent :

$$
F1
=
99.85\%
$$

confirmant la capacité du modèle à reconstruire fidèlement les décisions NHSN.

Au-delà des performances de classification, le principal résultat de ce travail est la découverte explicite d'une structure temporelle interprétable. Contrairement à une approche purement prédictive, le modèle fournit des paramètres directement compréhensibles par un expert métier et reliés à une réalité clinique observable.

Cette propriété rapproche notre approche des travaux de découverte de lois à partir des données (*Data-Driven Discovery*) ainsi que des approches inspirées des *Physics-Informed Neural Networks*. Le modèle ne se contente pas de prédire une décision ; il reconstruit le mécanisme temporel qui la produit.

Ce module constitue la première étape d'une architecture neuro-symbolique plus large. ==Les paramètres temporels découverts pourront ensuite être injectés dans un graphe patient afin d'enrichir la représentation clinique utilisée par les modèles de Graph Neural Networks.== Cette intégration permettra d'exploiter simultanément les relations structurelles entre entités médicales et les régularités temporelles apprises automatiquement.

Ainsi, ce travail démontre qu'il est possible de transformer une règle clinique discrète en un mécanisme différentiable capable de découvrir automatiquement la structure temporelle sous-jacente aux décisions NHSN, ouvrant la voie à des modèles neuro-symboliques temporels plus généraux pour l'analyse des infections associées aux soins.
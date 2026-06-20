# Modèle temporel différentiable

## Objectif

L'objectif du modèle est d'apprendre automatiquement une structure temporelle permettant de transformer un intervalle de cathétérisation en un nombre différentiable d'unités temporelles traversées.

Le modèle reçoit en entrée :

$$
(t_s,d)
$$

où :

- $t_s$ représente l'instant de début ;
- $d$ représente la durée de l'intervalle.

Le temps de fin est reconstruit par :

$$
t_e = t_s + d
$$

Le modèle produit ensuite :

- une estimation différentiable du nombre de jours calendaires :

$$
\hat{N}
$$

- un score de décision temporelle :

$$
score
$$


# Paramètres appris

Le modèle apprend directement quatre paramètres interprétables :

| Paramètre | Rôle                             |
| --------- | -------------------------------- |
| $T$       | période temporelle               |
| $\phi$    | phase de la frontière temporelle |
| ==$\theta$==  | ==seuil de décision sur $\hat{N}$==  |
| $\gamma$  | pente de la fonction de décision |

==$\theta$ est le seuil permettant d'estimer $N$ relatif à l'éligibilité CAUTI==

Contrairement à un réseau neuronal classique, ce modèle ne repose pas sur une succession de couches cachées.

==Il s'agit plutôt d'un modèle paramétrique différentiable de comptage de frontières temporelles.==


# Contraintes sur les paramètres

Les paramètres appris doivent rester dans des domaines admissibles.

Pour cela, le modèle apprend des paramètres non contraints :

| Paramètre brut | Paramètre interprétable |
|---|---|
| $raw_T$ | $T$ |
| $raw_\phi$ | $\phi$ |
| $raw_\theta$ | $\theta$ |
| $raw_\gamma$ | $\gamma$ |

puis les transforme à l'aide de fonctions différentiables.


## Période temporelle $T$

La période est bornée entre une valeur minimale et une valeur maximale :

$$
T
=
T_{\min}
+
(T_{\max}-T_{\min})
\sigma(raw_T)
$$
(On note ici la présence de la sigmoïde)
Dans notre expérimentation :

$$
T_{\min}=1
$$

$$
T_{\max}=48
$$

Ainsi :

$$
1 \leq T \leq 48
$$
Le paramètre $T$ représente la période temporelle fondamentale que le modèle cherche à découvrir. Dans le contexte des règles NHSN, cette période correspond théoriquement à la durée d'un jour calendaire, soit :

$$
T = 24\ h
$$

#### Plus de détails
Cependant, lors de l'apprentissage, cette valeur n'est pas connue du modèle. Il est donc nécessaire de lui permettre d'==explorer différentes périodes possibles tout en évitant des solutions physiquement incohérentes.==

Pour cette raison, le paramètre appris n'est pas directement $T$, mais une variable libre appelée :

$$
raw_T
$$

Cette variable peut prendre n'importe quelle valeur réelle. Une fonction sigmoïde est ensuite utilisée pour la transformer en une période valide comprise entre une borne minimale et une borne maximale :

$$
T
=
T_{\min}
+
(T_{\max}-T_{\min})
\sigma(raw_T)
$$

où :

$$
\sigma(x)
=
\frac{1}{1+e^{-x}}
$$

La fonction sigmoïde produit toujours une valeur comprise entre 0 et 1. Par conséquent, quelle que soit la valeur de $raw_T$, la période apprise reste nécessairement dans l'intervalle :

$$
T_{\min}
\leq
T
\leq
T_{\max}
$$

Dans notre expérimentation :

$$
T_{\min}=1\ h
$$

et :

$$
T_{\max}=48\ h
$$

Le modèle est donc autorisé à explorer des périodes allant d'une heure à deux jours complets.

Ce choix traduit une hypothèse faible : nous savons que la structure temporelle recherchée est probablement liée à une unité de temps clinique comprise dans cet intervalle, mais nous ne supposons pas a priori qu'elle soit égale à 24 heures.

L'apprentissage consiste alors à ajuster progressivement la variable $raw_T$ afin de trouver la période qui minimise l'erreur de classification. Si les données contiennent effectivement une structure journalière, le modèle doit converger naturellement vers :

$$
T \approx 24\ h
$$

sans que cette valeur ne soit explicitement imposée durant l'entraînement.


## Phase $\phi$

La phase est exprimée relativement à la période :

$$
\phi
=
T \cdot \sigma(raw_\phi)
$$

Ainsi :

$$
0 \leq \phi \leq T
$$

Cette contrainte garantit que la phase reste toujours située à l'intérieur d'un cycle temporel.


## Seuil de décision $\theta$

Le seuil de décision  (seuil correspondant aux jours calendaire) est également borné :

$$
\theta
=
\theta_{\min}
+
(\theta_{\max}-\theta_{\min})
\sigma(raw_\theta)
$$

Dans notre expérimentation :

$$
\theta_{\min}=0
$$

$$
\theta_{\max}=10
$$

On contraint alors le modèles à 10 jours calendaires
## Pente de décision $\gamma$

La pente de décision doit rester strictement positive.

Le modèle utilise donc une fonction softplus :

$$
\gamma
=
softplus(raw_\gamma)
+
\epsilon
$$

où :

$$
\epsilon = 10^{-8}
$$

Ainsi :

$$
\gamma > 0
$$

#### Plus de détails:

Le paramètre $\gamma$ contrôle la pente de la fonction de décision finale du modèle. Il intervient dans le calcul du score temporel :

$$
score
=
\gamma
\left(
\hat N - \theta
\right)
$$

Lorsque $\gamma$ est faible, la transition entre les classes positives et négatives est progressive : deux observations situées de part et d'autre du seuil $\theta$ produisent des scores relativement proches. À l'inverse, lorsque $\gamma$ augmente, la frontière de décision devient plus abrupte et le comportement du modèle se rapproche progressivement d'une règle discrète. Afin de garantir une interprétation cohérente du score, la pente doit rester strictement positive. Pour cette raison, le modèle n'apprend pas directement $\gamma$ mais une variable libre $raw_\gamma$ transformée par une fonction *softplus*. Cette transformation garantit que :

$$
\gamma > 0
$$

pendant tout l'apprentissage. Le paramètre $\gamma$ peut ainsi être interprété comme un niveau de confiance du modèle : plus sa valeur est élevée, plus la séparation entre les observations situées avant et après le seuil $\theta$ est marquée.


# Problème de différentiabilité

La formule exacte du nombre de jours calendaires est :

$$
N(T,\phi)
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

Cette formule n'est pas différentiable à cause de la fonction partie entière :

$$
\left\lfloor \cdot \right\rfloor
$$

Elle ne peut donc pas être optimisée directement par descente de gradient.


# Relaxation différentiable du comptage

Pour rendre le comptage différentiable, le modèle remplace le passage brutal d'une frontière temporelle par une ==fonction sigmoïde.==

Une frontière temporelle est définie par :

$$
b_k
=
\phi + kT
$$

où :

- $k$ est un entier ;
- $T$ est la période ;
- $\phi$ est la phase.

Pour chaque frontière, on calcule :

$$
\sigma
\left(
\beta(t-b_k)
\right)
$$

où :

- $\beta$ contrôle la raideur de la transition ;
- $t$ est l'instant évalué ;
- $b_k$ est la position de la frontière.

%% ICI: Représenter une frontiere %%

# Fonction cumulative de frontières

Le modèle additionne les contributions de toutes les frontières :

$$
S(t)
=
\sum_k
\sigma
\left(
\beta(t-b_k)
\right)
$$

Cette fonction approxime le nombre de frontières temporelles déjà franchies à l'instant $t$.


# Calcul différentiable de $\hat{N}$

Le nombre différentiable d'unités temporelles traversées par l'intervalle est alors :

$$
\hat{N}
=
S(t_e)
-
S(t_s)
+
1
$$

Comme :

$$
t_e = t_s + d
$$

on peut également écrire :

$$
\hat{N}
=
S(t_s+d)
-
S(t_s)
+
1
$$

Cette formulation approxime le comptage exact des jours calendaires tout en restant différentiable.


# Fonction de décision

Une fois $\hat{N}$ calculé, le modèle produit un score de décision :

$$
score
=
\gamma
\left(
\hat{N}
-
\theta
\right)
$$

Interprétation :

- si $\hat{N} > \theta$, le score devient positif ;
- si $\hat{N} < \theta$, le score devient négatif ;
- $\gamma$ contrôle la pente de cette transition.


# Prédiction binaire

Le score peut ensuite être transformé en probabilité par une sigmoïde :

$$
P(Y=1)
=
\sigma(score)
$$

La décision binaire est alors :

$$
\hat{Y}
=
\begin{cases}
1 & \text{si } P(Y=1) \geq 0.5 \\
0 & \text{sinon}
\end{cases}
$$


# Fonction de perte

Le modèle peut être entraîné avec une perte de classification binaire :

$$
\mathcal{L}_{BCE}
=
-
\left[
Y\log(\hat{Y})
+
(1-Y)\log(1-\hat{Y})
\right]
$$

Dans l'implémentation, il est préférable d'utiliser directement les logits :

$$
score
=
\gamma(\hat{N}-\theta)
$$

avec une fonction de perte de type :

$$
BCEWithLogitsLoss
$$

Cela évite les instabilités numériques liées au calcul explicite de la sigmoïde.


# Sorties du modèle

Le modèle retourne deux valeurs :

| Sortie    | Signification                                            |
| --------- | -------------------------------------------------------- |
| $score$   | score de décision temporelle                             |
| $\hat{N}$ | estimation différentiable du nombre d'unités temporelles |



# Exemple chiffré

# Exemple chiffré

Considérons un épisode de cathétérisation observé dans les données MIMIC.

| Évènement | Date |
|------------|------------|
| Pose du cathéter | 03/08/2144 21:13 |
| Retrait du cathéter | 05/08/2144 14:00 |

Le modèle a appris les paramètres suivants :

$$
T = 24.1769
$$

$$
\phi = 23.9059
$$

$$
\theta = 2.4803
$$

$$
\gamma = 11.6237
$$

$$
\beta = 10
$$

On note que phi n'est pas égal à 0 (minuit) mais s'en rapproche
## Passage dans le repère temporel local

On choisit comme origine :

$$
t_0
=
03/08/2144\ 00:00
$$

Les instants deviennent :

$$
t_s
=
21.22\ h
$$

$$
t_e
=
62.00\ h
$$

La durée vaut :

$$
d
=
t_e - t_s
=
40.78\ h
$$


## Frontières temporelles apprises

À partir de :

$$
T = 24.1769
$$

et :

$$
\phi = 23.9059
$$

le modèle construit les frontières :

| k | Frontière |
|---|---:|
|0|23.91 h|
|1|48.08 h|
|2|72.26 h|

L'intervalle clinique traverse les deux premières frontières :

```text
21.22 h ------------------------ 62.00 h
         | 23.91 h |
                         | 48.08 h |
```


## Calcul exact NHSN

La formule discrète est :

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

En remplaçant par les valeurs apprises :

$$
N
=
\left\lfloor
\frac{62.00-23.9059}
{24.1769}
\right\rfloor
-
\left\lfloor
\frac{21.22-23.9059}
{24.1769}
\right\rfloor
+
1
$$

Premier terme :

$$
\frac{38.0941}{24.1769}
=
1.5756
$$

donc :

$$
\lfloor 1.5756 \rfloor
=
1
$$

Deuxième terme :

$$
\frac{-2.6859}{24.1769}
=
-0.1111
$$

donc :

$$
\lfloor -0.1111 \rfloor
=
-1
$$

Finalement :

$$
N
=
1
-
(-1)
+
1
$$

$$
N = 3
$$

L'épisode traverse donc trois jours calendaires.


## Approximation différentiable du PINN

La fonction discrète précédente n'est pas différentiable.

Le modèle utilise alors la relaxation continue :

$$
S(t)
=
\sum_k
\sigma
\left(
\beta
\left(
t-(\phi+kT)
\right)
\right)
$$

avec :

$$
\beta = 10
$$

Le nombre différentiable d'unités temporelles est alors :

$$
\hat N
=
S(t_e)-S(t_s)+1
$$

Pour cet exemple :

$$
\hat N
\approx
3.02
$$

Cette valeur est très proche de :

$$
N = 3
$$

mais reste entièrement différentiable.


## Application du seuil appris

Le modèle a appris :

$$
\theta = 2.4803
$$

On calcule :

$$
\hat N - \theta
=
3.02 - 2.4803
$$

$$
=
0.5397
$$

Comme cette quantité est positive, l'observation se situe du côté "éligible" de la frontière de décision.


## Calcul du score temporel

Le score est défini par :

$$
score
=
\gamma
\left(
\hat N-\theta
\right)
$$

avec :

$$
\gamma = 11.6237
$$

On obtient :

$$
score
=
11.6237
\times
0.5397
$$

$$
score
=
6.27
$$


## Probabilité finale

Le score est transformé par une fonction sigmoïde :

$$
p
=
\sigma(score)
$$

$$
p
=
\sigma(6.27)
$$

$$
p
=
0.9981
$$


## Décision du modèle

Comme :

$$
p > 0.5
$$

le modèle prédit :

$$
\hat Y = 1
$$

L'épisode est donc considéré comme éligible selon la règle temporelle apprise.


## Résumé

| Quantité | Valeur |
|-----------|---------:|
| $t_s$ | 21.22 h |
| $t_e$ | 62.00 h |
| $d$ | 40.78 h |
| $T$ appris | 24.1769 h |
| $\phi$ appris | 23.9059 h |
| $N$ exact NHSN | 3 |
| $\hat N$ PINN | 3.02 |
| $\theta$ appris | 2.4803 |
| $\gamma$ appris | 11.6237 |
| Score | 6.27 |
| Probabilité | 99.81 % |
| Décision | Classe 1 (Éligible) |

Cet exemple illustre l'ensemble du fonctionnement du modèle : découverte de la période $T$, apprentissage de la phase $\phi$, reconstruction différentiable du nombre de jours calendaires $\hat N$, puis application d'une frontière de décision apprise automatiquement.
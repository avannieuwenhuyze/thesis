# Fondements scientifiques de la découverte de structures temporelles

## Introduction

L'objectif de notre travail n'est pas uniquement de prédire la présence d'un CAUTI à partir de données cliniques.

Nous cherchons également à découvrir automatiquement une structure temporelle cachée utilisée par les règles NHSN.

Plus précisément, nous avons montré que la durée de cathétérisation seule ne suffit pas à expliquer les décisions observées. Les résultats obtenus sur le dataset MIMIC ont mis en évidence la nécessité d'introduire une représentation discrète du temps basée sur le nombre de jours calendaires traversés :

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

où :

- $T$ représente la période temporelle ;
    
- $\phi$ représente la phase ;
    
- $N$ représente le nombre d'unités temporelles traversées.
    

La question scientifique devient alors :

> Peut-on retrouver automatiquement les paramètres temporels $(T,\phi)$ à partir des observations cliniques ?

Cette problématique s'inscrit dans plusieurs courants de recherche majeurs.


# 1. Découverte de lois à partir des données

Rudy et al. (2017) proposent **PDE-FIND**, une méthode permettant de découvrir automatiquement les équations gouvernant un système dynamique à partir de données observées.

L'idée centrale est que les observations contiennent une structure cachée qu'il est possible de retrouver automatiquement.

Les auteurs montrent qu'il est possible de reconstruire :

- l'équation de diffusion ;
    
- l'équation de Burgers ;
    
- l'équation de Korteweg-de Vries ;
    
- certaines équations de mécanique des fluides ;
    

à partir de simples observations temporelles.

L'objectif n'est donc plus seulement de prédire des valeurs mais de retrouver les mécanismes qui les génèrent.

Cette philosophie est très proche de notre problématique.

Nous observons :

- des dates de pose ;
    
- des dates de retrait ;
    
- des températures ;
    
- des labels CAUTI.
    

Mais nous ne connaissons pas directement la structure temporelle expliquant ces observations.

Notre objectif consiste à découvrir les paramètres permettant de reconstruire :

$$  
N(T,\phi)  
$$

à partir des données.


# 2. Scientific Machine Learning et Physics-Informed Neural Networks

Raissi et al. (2019) introduisent les **Physics-Informed Neural Networks (PINNs)**.

L'idée consiste à intégrer des connaissances expertes dans le processus d'apprentissage afin de réduire l'espace des solutions admissibles.

Dans leur formulation générale, un système est décrit par :

$$  
u_t + \mathcal{N}[u;\lambda] = 0  
$$

où :

- $u$ représente la solution recherchée ;
    
- $\mathcal{N}$ représente la dynamique du système ;
    
- $\lambda$ représente des paramètres inconnus.
    

L'une des contributions majeures des PINNs est de permettre l'apprentissage automatique de paramètres inconnus directement à partir des observations.

Cette idée est particulièrement importante pour notre travail.

Nous ne cherchons pas à apprendre une équation physique, mais à retrouver des paramètres temporels cachés :

$$  
(T,\phi)  
$$

qui gouvernent le calcul des jours calendaires NHSN.

Notre démarche est donc inspirée des PINNs dans le sens où :

- les données fournissent les observations ;
    
- certains paramètres du système sont inconnus ;
    
- ces paramètres doivent être appris automatiquement ;
    
- les connaissances métier guident l'apprentissage.
    

Cependant, notre objectif n'est pas la résolution d'une équation différentielle mais la découverte d'une structure temporelle discrète.

Nous nous inscrivons donc plus largement dans le cadre du **Scientific Machine Learning**.


# 3. Notre problème est un problème inverse

Dans les travaux de Raissi et al., deux grandes familles de problèmes sont distinguées.

## Forward Problem

Les paramètres du système sont connus et l'objectif consiste à prédire son comportement.

Formellement :

$$  
\lambda \rightarrow u  
$$

Les paramètres sont donnés et la solution doit être calculée.

## Inverse Problem

Le comportement du système est observé mais certains paramètres sont inconnus.

L'objectif consiste à retrouver les paramètres expliquant les observations.

Formellement :

$$  
u \rightarrow \lambda  
$$

Les observations sont connues et les paramètres doivent être estimés.

## Positionnement de notre problème

Notre problème appartient clairement à cette seconde catégorie.

Nous observons :

- les dates de pose et de retrait ;
    
- les températures ;
    
- les labels CAUTI ;
    
- les décisions associées aux règles NHSN.
    

Cependant, nous ne supposons pas que le modèle connaisse explicitement :

$$  
T  
$$

et :

$$  
\phi  
$$

qui définissent les frontières temporelles utilisées pour le calcul des jours calendaires.

L'objectif est donc d'apprendre :

$$  
(T,\phi)  
$$

à partir des observations disponibles.

# 4. Paramètres recherchés

Le paramètre :

$$  
T  
$$

définit la périodicité fondamentale du système.

Dans le cas des jours calendaires NHSN :

$$  
T = 24\ h  
$$

Le paramètre :

$$  
\phi  
$$

définit la position des frontières temporelles.

Pour les règles NHSN :

$$  
\phi = 0  
$$

correspond à une frontière située à minuit.

Ces paramètres permettent ensuite de calculer :

$$  
N(T,\phi)  
$$

qui représente le nombre d'unités temporelles traversées par un intervalle.


# 5. Objectif de l'approche proposée

À partir des observations cliniques, notre objectif consiste à apprendre automatiquement :

$$  
(T,\phi)  
$$

afin de reconstruire :

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

Le modèle ne cherche donc pas uniquement à prédire un label CAUTI.

Il cherche à découvrir la structure temporelle sous-jacente expliquant les décisions observées.

Cette démarche peut être vue comme un problème de :

- découverte de structure temporelle ;
    
- découverte de paramètres latents ;
    
- apprentissage guidé par la connaissance métier.
    


# 6. Positionnement scientifique

Nous proposons une approche de découverte de structure temporelle visant à apprendre automatiquement les paramètres :

$$  
(T,\phi)  
$$

gouvernant le calcul des jours calendaires NHSN.

Inspirée des travaux en :

1. Data-Driven Discovery ;
    
2. Scientific Machine Learning ;
    
3. Inverse Problems ;
    

cette approche cherche à reconstruire une représentation temporelle discrète directement à partir des observations cliniques.

Les paramètres appris permettent ensuite de calculer :

$$  
N(T,\phi)  
$$

qui constitue une information essentielle pour l'identification des CAUTI.

Dans un second temps, cette représentation temporelle pourra être intégrée dans un graphe patient afin d'enrichir les modèles de Graph Neural Networks utilisés pour la détection des infections associées aux soins.


# Références bibliographiques utilisées

## PDE-FIND

**Rudy S., Brunton S., Proctor J., Kutz J.**

_Data-driven discovery of partial differential equations_

Science Advances, 2017.

PDF utilisé :

```text
Data-driven discovery of partial differential equations.pdf
```


## Physics-Informed Neural Networks

**Raissi M., Perdikaris P., Karniadakis G.**

_Physics-Informed Neural Networks: A Deep Learning Framework for Solving Forward and Inverse Problems Involving Nonlinear Partial Differential Equations_

Journal of Computational Physics, 2019.

PDF utilisé :

```text
PINN_RPK_2019_1.pdf
```


## Knowledge-Informed Neural Networks

**Knowledge-Informed Neural Networks (KINN)**

PDF consulté :

```text
KINN.pdf
```

Ce document a été utilisé comme source de comparaison conceptuelle mais n'est pas mobilisé directement dans le positionnement scientifique actuel du modèle.
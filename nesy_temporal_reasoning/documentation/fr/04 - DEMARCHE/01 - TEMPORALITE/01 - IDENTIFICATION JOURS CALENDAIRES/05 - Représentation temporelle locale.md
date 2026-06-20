# Représentation temporelle locale

## Introduction

Les données hospitalières sont naturellement exprimées sous forme de dates et d'heures absolues.

Par exemple :

|Début|Fin|
|---|---|
|2144-08-03 21:13|2144-08-05 14:00|

Cependant, les modèles d'apprentissage automatique ne manipulent pas directement des dates. Ils nécessitent une représentation numérique du temps.

La question devient alors :

> Comment représenter le temps de manière à préserver l'information utile tout en facilitant l'apprentissage ?



# Une première idée : utiliser une date de référence fixe

Une approche classique consiste à convertir chaque date en nombre d'heures écoulées depuis une date arbitraire, par exemple :

$$  
t = \text{nombre d'heures depuis le 01/01/1900}  
$$

Dans ce cas (dates issues de MIMIC) :

|Date|Temps (heures depuis 1900)|
|---|--:|
|2144-08-03 21:13|2 144 000+|
|2144-08-05 14:00|2 144 040+|

Cette représentation est valide mathématiquement mais présente plusieurs inconvénients :

- les valeurs deviennent très grandes ;
    
- elles n'apportent aucune information utile sur le phénomène étudié ;
    
- elles compliquent l'optimisation des modèles ;
    
- elles rendent l'interprétation des paramètres plus difficile.
    


# Observation fondamentale

Dans notre problème, la date absolue n'a aucune importance.

Considérons les deux intervalles suivants :

|Début|Fin|
|---|---|
|2144-08-03 21:13|2144-08-05 14:00|
|2160-01-10 21:13|2160-01-12 14:00|

Bien que les dates soient différentes, ces deux intervalles :

- possèdent exactement la même durée ;
    
- traversent les mêmes frontières calendaires ;
    
- produisent le même nombre de jours calendaires.
    

La décision dépend donc uniquement de la position relative des événements dans le temps.

Nous pouvons écrire :

$$  
Y \neq f(\text{date absolue})  
$$


# Changement de repère

Afin de supprimer l'information inutile liée aux dates absolues, nous introduisons un repère temporel local.

Nous définissons :

$$  
t_0  
=  
\min(t_s)  
$$

où :

$$  
t_s  
$$

représente l'ensemble des dates de début du dataset.

Chaque instant est alors exprimé relativement à cette origine :

$$  
t'_s = t_s - t_0  
$$

$$  
t'_e = t_e - t_0  
$$


# Alignement sur minuit

Dans l'implémentation, l'origine est alignée sur minuit :

```python
origin = df["t_start"].min().normalize()
```

Ainsi :

$$  
\phi = 0  
$$

correspond naturellement à une frontière calendaire.

Cette propriété simplifie considérablement l'interprétation des paramètres temporels.


# Exemple chiffré

Considérons l'intervalle :

|Début|Fin|
|---|---|
|2144-08-03 21:13|2144-08-05 14:00|

Supposons que :

$$
t_0
=
\text{2144-08-03 00:00}
$$

Le repère local devient :

|Instant|Temps local|
|---|--:|
|2144-08-03 00:00|0 h|
|2144-08-03 21:13|21.22 h|
|2144-08-04 00:00|24 h|
|2144-08-05 00:00|48 h|
|2144-08-05 14:00|62 h|

Nous obtenons donc :

$$  
t'_s = 21.22  
$$

$$  
t'_e = 62.00  
$$

La durée est :

$$
\Delta t
=
t'_e - t'_s
=
40.78\ h
$$


# Conservation de la structure temporelle

Le changement de repère ne modifie pas les propriétés importantes du problème.

La durée reste identique :

$$
\Delta t
=
t_e - t_s
=
t'_e - t'_s
$$

Les frontières calendaires demeurent espacées de :

$$  
24\ h  
$$

Le calcul du nombre d'unités temporelles reste également inchangé :

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

La seule différence est que les calculs sont désormais réalisés dans un repère beaucoup plus compact.


# Exemple de calcul de N

Prenons :

$$  
T = 24  
$$

et :

$$  
\phi = 0  
$$

Nous obtenons :

$$
N
=
\left\lfloor
\frac{62}{24}
\right\rfloor
-
\left\lfloor
\frac{21.22}{24}
\right\rfloor
+
1
$$

soit :
$$
N
=
2
-
0
+
1
$$


$$  
N = 3  
$$

L'intervalle traverse donc trois jours calendaires :

- 03 août ;
    
- 04 août ;
    
- 05 août.
    



# Pourquoi utiliser $t_{start}$  et $duration$ ?

Une fois le repère local défini, il n'est plus nécessaire de stocker simultanément :

$$  
t_s  
$$

et :

$$  
t_e  
$$

En effet :

$$
t_e
=
t_s + d
$$

où :

$$
d
=
t_e - t_s
$$

représente la durée.

Le problème peut donc être représenté par :

|Variable|Signification|
|---|---|
|$t_s$|instant de début|
|$d$|durée de l'intervalle|

Cette formulation réduit la dimension du problème tout en conservant l'intégralité de l'information temporelle.

# Évolution vers le modèle final

Le repère temporel local a été introduit lors des premières expérimentations afin de simplifier la manipulation des dates et d'étudier le comportement de :

$$
N(T,\phi)
$$

Toutefois, les expériences ont montré que le calcul de :

$$
N(T,\phi)
$$

dépend essentiellement :

- du temps de début ;
- de la durée de l'intervalle ;
- de la position des frontières temporelles.

Le modèle final ne nécessite donc plus l'utilisation explicite de :

$$
t_0
$$

et repose sur la représentation :

$$
d = t_e - t_s
$$

$$
t_e = t_s + d
$$

qui conserve l'intégralité de l'information temporelle utile.

# Exemple avec le modèle final

Considérons l'intervalle étudié précédemment :

| Variable | Valeur |
|-----------|-----------:|
| Début | 2144-08-03 21:13 |
| Fin | 2144-08-05 14:00 |
| Durée | 40.78 h |

Dans le repère local, nous avions :

$$
t_s = 21.22
$$

$$
t_e = 62.00
$$

et :

$$
d
=
t_e - t_s
=
40.78
$$

Le modèle final ne stocke plus explicitement :

$$
t_e
$$

mais uniquement :

$$
t_s
$$

et :

$$
d
$$

L'intervalle est donc représenté par :

| Variable | Valeur |
|-----------|-----------:|
| $t_s$ | 21.22 |
| $d$ | 40.78 |

Le temps de fin est ensuite reconstruit par :

$$
t_e
=
t_s + d
$$

soit :

$$
t_e
=
21.22 + 40.78
$$

$$
t_e
=
62.00
$$

Nous pouvons alors calculer :

$$
N(T,\phi)
=
\left\lfloor
\frac{62.00}{24}
\right\rfloor
-
\left\lfloor
\frac{21.22}{24}
\right\rfloor
+
1
$$

$$
N
=
2
-
0
+
1
$$

$$
N
=
3
$$

Le modèle obtient donc exactement le même résultat que lorsque le temps de fin était fourni explicitement.

Cette représentation présente plusieurs avantages :

- moins de variables à manipuler ;
- meilleure stabilité numérique ;
- interprétation plus simple ;
- compatibilité avec l'apprentissage de :

$$
T
$$

et :

$$
\phi
$$

==Cette méthode permet également d'optimiser les calculs car lorsque l'on utilise une date min de référence, cela contraint le tenseur à créer toutes les dates. Là nous n'avons qu'une toute petite période==
# Conclusion

La représentation temporelle locale permet de supprimer l'information inutile liée aux dates absolues tout en conservant les propriétés essentielles du phénomène étudié.

Elle présente plusieurs avantages :

- réduction de l'échelle numérique ;
    
- meilleure stabilité des calculs ;
    
- interprétation naturelle de la phase ;
    
- simplification de l'apprentissage ;
    
- compatibilité avec les modèles différentiables.
    

Cette représentation constitue le fondement des méthodes présentées dans les chapitres suivants pour l'estimation automatique de la période :

$$  
T  
$$

et de la phase :

$$  
\phi  
$$  
.
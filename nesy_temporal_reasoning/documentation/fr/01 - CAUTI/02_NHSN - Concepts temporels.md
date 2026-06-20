# Concepts temporels du NHSN


Documents associés:
[[MED01- Diagnosis, Prevention, and Treatment - 50-5-625.pdf]]
[[MED02 - Urinary Tract Infection-  ioi90519.pdf]]
[[MED03 - NHSN - Urinary Tract Infection - 7psccauticurrent.pdf]]
[[MED04 - NHSN - General Key Terms.pdf]]
[[MED05 - NHSN - Identifying Healthcare-associated Infections (HAI) for NHSN.pdf]]
[[MED06 - Urinary Tract infection (UTI) Form.pdf]]

## 1. Introduction

Les définitions de surveillance proposées par le National Healthcare Safety Network (NHSN) reposent fortement sur des contraintes temporelles. Contrairement à une approche clinique classique fondée principalement sur les symptômes observés chez le patient, le ==NHSN introduit un ensemble de concepts temporels permettant de déterminer de manière standardisée si une infection est associée aux soins et si elle peut être attribuée à l'utilisation d'un dispositif médical==.

Ces concepts constituent le socle logique des règles de surveillance des infections associées aux cathéters urinaires (CAUTI). Ils jouent également un rôle central dans notre travail de recherche puisque notre approche vise précisément à apprendre et représenter ces contraintes temporelles à l'aide de modèles neuronaux.


# 2. La notion de jour calendaire

Le NHSN définit ==un jour calendaire comme l'intervalle compris entre minuit (00:00) et 23h59==.

Cette définition peut sembler triviale mais elle possède une importance considérable dans la surveillance des infections. En effet, ==les critères NHSN ne reposent pas sur une durée exprimée en heures mais sur un décompte discret de jours calendaires==.

Par exemple :

|Date|Heure|
|---|---|
|01/01|23h50|
|02/01|00h10|

==Bien que seulement vingt minutes séparent ces deux événements, ils appartiennent à deux jours calendaires différents==.

Cette représentation discrète du temps est utilisée dans l'ensemble des calculs NHSN relatifs à la durée d'hospitalisation, à la présence d'un dispositif médical ou à l'attribution d'une infection.


# 3. Date of Event (DOE)

Le Date of Event (DOE) représente l'un des concepts les plus importants du NHSN.

==Le DOE correspond à la date du premier élément permettant de satisfaire un critère d'infection à l'intérieur de la fenêtre d'infection==.

Le DOE ne correspond donc pas nécessairement :

- à la date du diagnostic médical ;
- à la date de prélèvement microbiologique ;
- à la date de déclaration de l'infection.

Il correspond au premier événement qui participe à la satisfaction du critère NHSN.

La détermination correcte du DOE est essentielle car elle permet de déterminer :

- si l'infection est présente à l'admission ;
- si elle est associée aux soins ;
- sa localisation d'attribution ;
- son association éventuelle à un dispositif médical ;
- le début de la période de répétition d'infection.


# 4. Présent à l'admission (POA) versus infection associée aux soins (HAI)

Le NHSN distingue deux situations :

## 4.1 Present On Admission (POA)

Une infection est considérée comme présente à l'admission lorsque son DOE survient :

- le jour de l'admission ;
- dans les deux jours précédant l'admission ;
- ou le lendemain de l'admission.

L'infection est alors considérée comme préexistante.

## 4.2 Healthcare-Associated Infection (HAI)

==Une infection est considérée comme associée aux soins lorsque son DOE survient à partir du troisième jour calendaire d'hospitalisation.==

Cette règle constitue le mécanisme principal permettant d'établir qu'une infection a vraisemblablement été acquise pendant la prise en charge hospitalière.

Cette notion est fondamentale pour la surveillance des CAUTI puisqu'une infection urinaire ne peut être considérée comme une infection associée aux soins que si elle satisfait préalablement cette contrainte temporelle.

# 5. Infection Window Period (IWP)

Le NHSN introduit la notion de fenêtre d'infection (Infection Window Period).

L'IWP correspond à une ==période de sept jours== :

- trois jours avant un test diagnostique ;
- le jour du test diagnostique ;
- trois jours après le test diagnostique.

Cette fenêtre permet de regrouper différents éléments cliniques et biologiques participant à la même infection.

Par exemple :

- fièvre observée deux jours avant le prélèvement ;
- culture urinaire positive le jour du prélèvement ;
- symptôme supplémentaire observé le lendemain.

Ces événements peuvent être utilisés conjointement pour satisfaire un critère NHSN dès lors qu'ils appartiennent à la même IWP.

L'IWP constitue donc un mécanisme de cohérence temporelle entre les observations médicales.

# 6. Repeat Infection Timeframe (RIT)

Après identification d'une infection, le NHSN définit une période appelée Repeat Infection Timeframe (RIT).

Cette période s'étend ==sur quatorze jours à partir du DOE==.

Pendant cette période :

- aucune nouvelle infection du même type ne peut être déclarée ;
- les nouveaux pathogènes identifiés sont rattachés à l'événement initial ;
- la date de l'événement demeure inchangée.

Le RIT évite ainsi le comptage multiple d'un même épisode infectieux.

Pour les infections urinaires, le NHSN considère qu'une seule infection urinaire peut être rapportée à l'intérieur d'une même période RIT.


# 7. Infections associées aux dispositifs médicaux

Le NHSN définit une infection associée à un dispositif médical lorsqu'un dispositif est présent depuis plus de deux jours calendaires au moment de l'événement infectieux.

Dans le cas d'un cathéter urinaire, deux conditions doivent être satisfaites :

1. ==le cathéter doit avoir été présent pendant plus de deux jours calendaires== ;
2. ==il doit être encore présent le jour du DOE ou avoir été retiré la veille==.

Cette définition établit explicitement une relation temporelle entre l'exposition au dispositif et l'apparition de l'infection.

Cette relation constitue le fondement même de la définition d'une CAUTI.



# 8. Conséquences pour notre approche d'intelligence artificielle

L'analyse du NHSN montre que la surveillance des CAUTI ne repose pas uniquement sur des variables cliniques telles que la température ou les résultats microbiologiques.

Elle dépend également d'une structure temporelle complexe fondée sur :

- les jours calendaires ;
- la durée de présence du cathéter ;
- la date d'apparition des symptômes ;
- la fenêtre d'infection ;
- la période de répétition d'infection.

Ces concepts constituent un ensemble de contraintes temporelles explicites.


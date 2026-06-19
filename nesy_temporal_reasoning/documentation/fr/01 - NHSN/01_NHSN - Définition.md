# NHSN: Cadre de surveillance des infections associées aux soins

## 1. Introduction

Le National Healthcare Safety Network (NHSN), développé par le Centers for Disease Control and Prevention (CDC), constitue aujourd'hui le principal système de surveillance des infections associées aux soins (Healthcare-Associated Infections – HAI) aux États-Unis.

Son objectif est de fournir ==un ensemble de définitions standardisées== permettant d'identifier, de surveiller et de comparer les infections acquises au cours d'une prise en charge hospitalière.

Contrairement à une approche purement clinique, les définitions NHSN reposent sur des critères objectifs, reproductibles et mesurables. Ces critères combinent :

- des observations cliniques ;
- des résultats microbiologiques ;
- des contraintes temporelles ;
- l'exposition à certains dispositifs médicaux.

Cette standardisation permet une surveillance homogène entre établissements et constitue un cadre particulièrement intéressant pour le développement de modèles d'intelligence artificielle explicables.

## 2. Les infections associées aux soins (HAI)

Une infection est considérée comme une infection associée aux soins ==lorsqu'elle apparaît au cours de l'hospitalisation et qu'elle ne peut pas être considérée comme présente à l'admission du patient.==
Le NHSN définit précisément cette notion à l'aide du concept de ==Date of Event (DOE)==, correspondant à la date du premier élément permettant de satisfaire les critères diagnostiques de l'infection.

Une infection est considérée comme :

- Présente à l'admission (Present On Admission – POA) lorsque le DOE survient avant l'hospitalisation ou dans les deux jours suivant l'admission.
- Associée aux soins (Healthcare-Associated Infection – HAI) lorsque le DOE survient à partir du troisième jour calendaire d'hospitalisation.

Cette définition introduit une première dimension temporelle essentielle : ==la notion de jour calendaire==.

## 3. Importance de la temporalité dans le NHSN

La majorité des définitions NHSN reposent sur des contraintes temporelles explicites.

Parmi les concepts les plus importants figurent :

- les jours calendaires d'hospitalisation ;
- les jours de présence d'un dispositif médical ;
- la fenêtre d'infection (Infection Window Period) ;
- la période de répétition d'infection (Repeat Infection Timeframe).

Ces notions ne représentent pas simplement des dates mais une véritable structure temporelle permettant d'établir la causalité entre un événement clinique et une exposition antérieure.

Dans le cas des infections urinaires associées aux cathéters, cette dimension temporelle est fondamentale puisque l'==association entre l'infection et le cathéter dépend directement de la durée de présence du dispositif.==

## 4. Intérêt pour l'intelligence artificielle

L'analyse du NHSN révèle que la surveillance des infections repose simultanément sur :

- des observations quantitatives (température, résultats biologiques) ;
- des événements temporels ;
- des relations causales ;
- des règles décisionnelles explicites.

Cette combinaison fait du NHSN un cadre particulièrement adapté aux approches neuro-symboliques.

[[02_NHSN - Concepts temporels]]
[[03_NHSN - Définition CAUTI]]
[[04_NHSN - Règles]]

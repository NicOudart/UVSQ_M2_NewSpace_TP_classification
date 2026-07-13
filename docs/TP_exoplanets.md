# Les différents types d'exoplanètes

![En-tête TP exoplanètes](img/.png)



## Contexte scientifique

En 1995, les astronomes suisses Michel Mayor et Didier Queloz annoncent la découverte d'une planète orbitant autour de l'étoile 51 Pegasi, détectée à partir d'observations de l'Observation de Haute-Provence.
Il s'agit de la 1ère planète connue en dehors du système solaire, ou "**exoplanète**".
Cette découverte leur vaudra le prix Nobel de Physique en 2019.

Depuis, des milliers d'exoplanètes ont été découvertes, avec plusieurs techniques d'observation.

Le projet "**exoplanet.eu**" propose une base de donnée en ligne de toutes les exoplanètes connues.
Sur le site web du projet, il est possible de télécharger au **format CSV** un tableau contenant les caractéristiques de chacune de ces exoplanètes, à des fins d'analyse de données.

Vous pouvez consulter ce site ici : [site de exoplanet.eu](https://exoplanet.eu).

Un extrait de ce tableau a été récupéré en avril 2026 sur le site d'exoplanet.eu, que vous trouverez [ici](https://github.com/NicOudart/UVSQ_M2_NewSpace_TP_classification/blob/master/example/exoplanet_catalogue_2026.csv).

Il contient pour chaque exoplanète les 2 informations suivantes : sa **masse** (en ratio par rapport à la masse de Jupiter) et sa **période de rotation** autour de son étoile (en jours terrestres).

Nous savons qu'il existe différents types de planètes dans le système solaire (telluriques, géantes gazeuses, géantes glacées, etc.), ayant des masses et des périodes de rotation différente.
On peut donc légitimement se poser la question suivante : **Est-il possible de discriminer les différents types d'exoplanètes à partir de ces mêmes informations ?**

## Objectifs

## Importation des données

### Le format CSV

## Analyse des données

![Nuage de points](img/Exoplanets_scatter_points.png)

![Histogramme 2D](img/Exoplanets_2D_histogram.png)

## Partitionnement

### K-moyennes

![Méthode du coude](img/Exoplanets_inertia.png)

![Résultat des K-moyennes](img/Exoplanets_k_means.png)

![Coefficient de silhouette](img/Exoplanets_silhouette.png)

### CAH

![Dendrogramme](img/Exoplanets_dendrogramme.png)

![Résultat de la CAH](img/Exoplanets_CAH.png)

## Interprétation

## Conclusion
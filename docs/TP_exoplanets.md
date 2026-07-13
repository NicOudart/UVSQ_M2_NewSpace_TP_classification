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

Il contient entre autres pour chaque exoplanète les 2 informations suivantes : sa **masse** (en ratio par rapport à la masse de Jupiter) et sa **période de rotation** autour de son étoile (en jours terrestres).

Nous savons qu'il existe différents types de planètes dans le système solaire (telluriques, géantes gazeuses, géantes glacées, etc.), ayant des masses et des périodes de rotation différente.
On peut donc légitimement se poser la question suivante : **Est-il possible de discriminer les différents types d'exoplanètes à partir de ces mêmes informations ?**

## Objectifs

Lors de ce tutoriel, nous allons programmer une **chaîne d'analyse de données** sous la forme d'un **script Python**, que nous utiliserons pour explorer les différents **types d'exoplanètes**.

Ce script Python devra :

* Importer les données du fichier CSV sous la forme d'un "DataFrame" Pandas.

* Appliquer une transformation de mise à l'échelle aux données.

* Réaliser un partitionnement "par partition" avec la méthode des K-moyennes, en choisissant un nombre de classes optimal.

* Réaliser un partitionnement "hiérarchique" avec la méthode de la CAH, en affichant un dendrogramme.

* Analyser les caractéristiques des différentes classes afin de les labéliser.

Votre script se basera sur la bibliothèque Python "**Scikit-Learn**", qui propose de nombreuses méthodes de Machine-Learning.
Nous utiliserons également la bibliothèque "Pandas" pour l'importation et la manipulation des données.

|Nota Bene|
|:-|
|Il est à noter que le problème que nous cherchons à résoudre ici est en réalité déjà résolu : classifier les exoplanètes à partir de leur masse et leur période orbitale est classique en planétologie.|

## Importation des données

### Le format CSV

Pour enregistrer une base de données sous la forme d'un tableau, on utilise couramment le format **CSV** : "Comma Separated Values".

Il s'agit d'un format ouvert, compréhensible par un humain, qui peut être lu par n'importe quel éditeur de texte.
L'extension d'un fichier CSV est simplement "**.csv**".

Comme son nom l'indique, un fichier CSV s'organise de la manière suivante :

* **Chaque ligne correspond à une ligne du tableau**. Les lignes sont séparées par un retour à la ligne "\n".

* Au sein d'une ligne, **les éléments de chaque colonnes sont séparés par des virgules** ",".

* La **première ligne** est souvent considérée comme l'**en-tête** du tableau (le nom des colonnes).

D'où le nom "Comma Separated Values".

En général, les **colonnes** représentent les différentes **variables**, et les **lignes** les différents **individus** de la base de données.

Voici un exemple de tableau : 

|Name     |Mass |Period |
|:-------:|:---:|:-----:|
|109 Psc b|5.743|1075.4 |
|112 Psc c|9.866|36336.7|
|14 Her b |8.900|1767.56|
|14 Her c |7.900|52160.0|
|51 Eri Ab|4.100|10260.0|

Sa conversion en format CSV sera tout simplement :

~~~
Name,Mass,Period
109 Psc b,5.743,1075.4
112 Psc c,9.866,36336.7
14 Her b,8.900,1767.56
14 Her c,7.900,52160.0
51 Eri Ab,4.100,10260.0
~~~

Vous pouvez ouvrir dans un éditeur de texte notre fichier CSV extrait de exoplanet.eu, pour essayer d'en comprendre le contenu.

Il peut être importé par la plupart des logiciels tableurs (Excel, OpenOffice Calc), et sous Python avec la bibliothèque **Pandas**.

### Importation avec Pandas

Pour importer un tableau sous Python à partir d'un fichier CSV, nous allons utiliser la bibliothèque **Pandas**.

Il ne faudra donc pas oublier d'importer `pandas` en début de script :

~~~
import pandas as pd
~~~

Pour importer notre fichier, il faudra utiliser la méthode `read_csv`.
Par exemple, pour un fichier CSV se situant à un chemin `input_path` sur votre ordinateur :

~~~
df_dataset = pd.read_csv(input_path)
~~~

Le tableau sera stocké sous la forme d'un **DataFrame** nommé `df_dataset`.

**Ajoutez à votre script Python l'importation de notre fichier CSV**.

Vous pouvez tester votre script pour vérifier que le DataFrame contient bien le tableau attendu.

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
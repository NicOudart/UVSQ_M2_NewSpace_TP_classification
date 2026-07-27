# Cartographier Puerto Maldonado avec Sentinel

![En-tête TP Sentinel](img/Header_TP_sentinel.png)

_"Le seizième siècle. Des quatre coins de l'Europe, de gigantesques voiliers partent à la conquête du Nouveau Monde. À bord de ces navires, des hommes avides de rêve, d'aventure et d'espace, à la recherche de fortune. Qui n'a jamais rêvé de ces mondes souterrains, de ces mers lointaines peuplées de légendes, ou d'une richesse soudaine qui se conquerrait au détour d'un chemin de la Cordillère des Andes ? Qui n'a jamais souhaité voir le Soleil souverain guider ses pas au coeur du pays Inca, vers la richesse et l'histoire des Mystérieuses Cités d'Or ?"_

**Jean Chalopin, Les Mystérieuses Cités d'Or (1982)**

---

## Contexte scientifique

### Puerto Maldonado et déforestation

**Puerto Maldonado** est la capitale de la région de Madre de Dios au Pérou.

En plein coeur de l'Amazonie péruvienne, la ville est à la confluence des rivières Madre de Dios et Tambopata, affluents de l'Amazone.
Le lieu a attiré tour à tour les conquistadors à la recherche de la cité perdue de Païtiti, les exploitants de caoutchouc, les orpailleurs illégaux, les cultivateurs de noix du Brésil, et aujourd'hui les "éco-touristes". 

La forêt primaire a donc été exploitée depuis des siècles, ce qui implique **déforestation**, **bétonisation** et **plantation d'espèces importées**.
Néanmoins, la création en 2000 de la réserve nationale de Tambopata, au sud-est de Puerto Maldonado, a permis de **préserver une partie de la forêt primaire**, qui constitue un des biotopes les plus riches du monde.

Voici une image de la région, prise par le satellite Sentinel 2 :

![Vue satellite de Puerto Maldonado](img/Sentinel_Puerto_Maldonado.png)

On observe nettement les 2 rivières, la zone urbaine de Puerto Maldonado, les exploitations agricoles, et ce qui reste de la forêt primaire.

Puerto Maldonado est donc un exemple parfait pour étudier **la déforestation en Amazonie**.

### Sentinel 2 et chlorophylle

**Sentinel 2** est une série de satellites d'observation de la Terre faisant partie du programme Copernicus de l'ESA.

Ces satellites ont pour mission de capturer des images optiques de la surface de la Terre, **dans différentes bandes de longueur d'onde**, avec une résolution allant jusqu'à 10 m au sol.

Voici les différentes bandes dans lesquelles les satellites Sentinel 2 sont capables d'acquérir des images :

|Bande|Cible                   |Longueur d'onde|Résolution au sol|
|:---:|:----------------------:|:-------------:|:---------------:|
|B01  |Aérosols                |443 nm         |60 m             |
|B02  |Bleu                    |490 nm         |10 m             |
|B03  |Vert                    |560 nm         |10 m             |
|B04  |Rouge                   |665 nm         |10 m             |
|B05  |Red-edge                |705 nm         |20 m             |
|B06  |Red-edge                |740 nm         |20 m             |
|B07  |Red-edge                |783 nm         |20 m             |
|B08  |Proche infrarouge       |842 nm         |10 m             |
|B08A |Proche infrarouge étroit|865 nm         |20 m             | 
|B09  |Vapeur d'eau            |945 nm         |60 m             |

Deux de ces bandes sont particulièrement utiles pour étuder la **végétation** : B04 et B08.

En effet, la chlorophylle contenue dans le feuillage des plantes **absorbe fortement le rouge**, et **réfléchit fortement le proche-infrarouge**.

Cette propriété est essentielle pour les plantes :
D'une part la photosynthèse nécessite de la lumière dans le rouge.
Et d'autre part le proche-infrarouge chaufferait inutilement les feuilles, les obligeant à transpirer abondement.

Il est donc très courant en télédétection d'utiliser les bandes rouge et proche-infrarouge pour cartographier la végétation d'une région.
Il existe même un score calculé à partir de ces 2 bandes pour indiquer la présence de végétation : le **NDVI**.

### Cartographie

Nous allons essayer de cartographier la région de Puerto Maldonado à partir d'une image satellite de Sentinel 2, acquise dans 4 bandes : B02 (bleu), B03 (vert), B04 (rouge) et B08 (proche-infrarouge).

L'idée sera pour chaque pixel de l'image d'identifier automatiquement à quel type de surface il correspond, à partir de sa couleur (c'est-à-dire la réflectivité perçue dans les différentes bandes).

Nous nous concentrerons sur les 4 types de surface suivants : "eau", "ville", "champs" et "forêt".

_Mais comment entrainer un modèle à identifier le type de pixel associé à chaque pixel à partir de sa couleur ?_

On reconnait dans cette question un problème de **classification supervisée**.

## Objectifs

Lors de ce tutoriel, nous allons programmer une **chaîne de classification supervisée** sous la forme d'un **script Python**, que nous utiliserons pour **cartographier la région de Puerto Maldonado**.

Ce script Python devra :

* Importer les données d'images GeoTIFF sous Python avec la bibliothèque `rasterio`.

* Sélectionner des pixels labélisés pour générer une base de données d'entrainement et une base de données de test.

* Appliquer une transformation de mise à l'échelle aux données.

* Entrainer un classifieur "Naive Bayes" à identifier les pixels de l'image à partir de leur couleur.

* Tester les performances en généralisation de ce classifieur.

* Classifier les pixels de toute l'image.

Votre script se basera sur la bibliothèque Python `Scikit-Learn`, qui propose de nombreuses méthodes de Machine-Learning.
Nous utiliserons également la bibliothèque `rasterio` pour l'importer de GeoTIFF, la bibliothèque `pandas` pour la manipulation des données, et la bibliothèque `seaborn` pour les affichages graphiques.

|Nota Bene|
|:-|
|Il est à noter que le problème que nous cherchons à résoudre ici est en réalité déjà résolu : il existe déjà de nombreux classifieurs d'images Sentinel 2.|
|Aussi, il est à noter que l'approche "par pixel" a ses limites : un classifieur moderne considèrerait les pixels dans leur contexte.|

## Importation des données

### Les images GeoTIFF

Le **TIFF** ("Tagged Image File Format") est un format d'image stockée de manière matricielle ("raster" en anglais).
Comme son nom l'indique, ce format permet d'enregistrer des **métadonnées** en plus de l'image ("tag" en anglais).

Le **GeoTIFF** utilise cette capacité du format TIFF pour ajouter des données de **géoréférencement** à des images.
Ceci en fait le format idéal pour enregistrer des **images satellites** associées à des coordonnées de latitude et de longitude.

Voici 4 fichiers GeoTIFF correspondant à la même image Sentinel 2 de la région de Puerto Maldonado, prise dans 4 bandes différentes :

* B02 (bleu) : [cliquez ici](https://github.com/NicOudart/UVSQ_M2_NewSpace_TP_classification/blob/master/example/2025-08-30-00_00_2025-08-30-23_59_Sentinel-2_L2A_B02_(Raw).tiff).

* B03 (vert) : [cliquez ici](https://github.com/NicOudart/UVSQ_M2_NewSpace_TP_classification/blob/master/example/2025-08-30-00_00_2025-08-30-23_59_Sentinel-2_L2A_B03_(Raw).tiff).

* B04 (rouge) : [cliquez ici](https://github.com/NicOudart/UVSQ_M2_NewSpace_TP_classification/blob/master/example/2025-08-30-00_00_2025-08-30-23_59_Sentinel-2_L2A_B04_(Raw).tiff).

* B08 (proche-infrarouge) : [cliquez ici](https://github.com/NicOudart/UVSQ_M2_NewSpace_TP_classification/blob/master/example/2025-08-30-00_00_2025-08-30-23_59_Sentinel-2_L2A_B08_(Raw).tiff).

Ce format est lisible par certains logiciels de traitement d'image, et par des outils de géographes tels que QGIS.

Pour importer ses images sous Python, nous utiliserons la bibliothèque `rasterio`.

### Importation avec Rasterio

`Rasterio` est une bibliothèque Python faite pour la l'importation et l'exportation de fichiers GeoTIFF sous Python.

Il ne faudra pas oublier de l'importer en début de script :

~~~
import rasterio
~~~

Pour importer chacun de nos GeoTIFF, il faudra utiliser la méthode `open`, puis la méthode `read`.
Par exemple, pour un GeoTIFF situé à un chemin `input_path` sur votre ordinateur :

~~~
input_geotiff = rasterio.open(input_path)
input_img = input_geotiff.read()
~~~

La variable `input_img` contiendra la matrice contenue dans le GeoTIFF.
Il s'agit d'une matrice 3D, la 1ère dimension correspondant à la couleur, la 2nde aux lignes, le 3ème aux colonnes.

Dans notre cas, chaque GeoTIFF contenant une image pour une "couleur" (c'est-à-dire pour une bande), on peut écrire :

~~~
input_img = input_geotiff.read()[0]
~~~

Il est également possible de récupérer la latitude et la longitude des coins de l'image, qui sont contenus dans l'attribut `bounds` :

~~~
input_bounds = input_geotiff.bounds
~~~

On peut alors obtenir la latitude minimale `lat_min`, la latitude maximale `lat_max`, la longitude minimale `lon_min` et la longitude maximale `lon_max` avec :

~~~
lat_min,lat_max,lon_min,lon_max = input_bounds.bottom,input_bounds.top,input_bounds.left,input_bounds.right
~~~

Une fois les GeoTIFF récupérés, il est possible de les afficher pour voir l'image qu'ils contiennent, avec des axes de latitude et de longitude.
Pour ce faire, on peut utiliser la bibliothèque `matplot.pyplot`, importée en tant que `plt`, avec la méthode `imshow` :

~~~
plt.imshow(input_img,extent=[lon_min,lon_max,lat_min,lat_max],origin='upper',cmap='gray')
plt.xlabel('Longitude (°)')
plt.ylabel('Latitude (°)')
~~~

L'image sera affichée en nuances de gris.

Si vous essayez ces commande sur un les GeoTIFF qui vous sont fournis, vous verrez que les contrastes sont mauvais.
En effet, l'intensité des pixels peut varier énormément au sein d'une même image.

Pour résoudre ce problème, on peut réduire la dynamique de l'image au 2ème et au 98ème poucentiles des intensités des pixels.
Ceci aura pour effet de saturer les valeurs très faibles et très élevées, améliorant ainsi le contraste.

Voici un exemple sur une image, en utilisant `numpy` importée en tant que `np` :

~~~
low,high = np.percentile(input_img,(2,98))
input_img = (input_img-low)/(high-low)
input_img = np.clip(input_img,0,1)
~~~

Il est également possible de jouer sur les paramètres `vmin` et `vmax` de la méthode `imshow`, afin de changer les bornes de l'échelle de couleur.
Mettre des bornes plus grandes que le maximum et le minimum d'intensité des pixels de l'image permet de modifier le contraste de l'affichage.

**Ajoutez à votre script Python l'importation des 4 GeoTIFF**.

Si vous affichez les 4 images qu'ils contiennent, vous devriez obtenir un résultat similaire à celui-ci :

![Image Sentinel 2 en 4 bandes](img/Sentinel_color_bands.png)

On voit déjà que certaines ressortent plus ou moins dans les différentes bandes : les rivières, les lacs, la ville, les différents types de végétation, etc.

Pour visualiser une image satellite acquise dans différentes bandes de fréquences, on utilise en général une **représentation RGB**.
On sélectionne 3 bandes, on assigne à chaque bande une couleur, et on superpose les 3 images pour obtenir une seule image en "couleurs".

Pour fusionner 3 matrices `img_red`, `img_green` et `img_blue` correspondant à 3 bandes d'une image que l'on veut afficher en RGB, il suffit de les concaténer avec `numpy` :

~~~
img_rgb = np.stack((img_red,img_green,img_blue),axis=-1)
~~~

Par défaut, l'échelle de couleur de la méthode `imshow` sera la bonne.

Le plus classique est d'afficher en rouge la bande du rouge, en vert la bande du vert, et en bleu la bande du bleu.
C'est ce que l'on appelle une image en **vraies couleurs**, car il se rapproche des couleurs telles que les percevrait notre oeil.

Voici ce que cela donne avec notre image Sentinel 2 :

![Image RGB en vraies couleurs](img/Sentinel_true_colors.png)

On peut également réaliser un affichage **fausses couleurs**.
Le plus commun est de mettre en rouge le proche-infrarouge, en vert le rouge, et en bleu le vert.

En effet, ce type d'affichage permet de bien faire ressortir la végétation des étendues d'eau ou des villes.

Voici ce que cet affichage donne sur notre image Sentinel 2 :

![Image RGB en fausses couleurs](img/Sentinel_false_colors.png)

La végétation ressort nettement en teintes de rouge, alors que les rivières et la ville ressortent en bleu.

|Nota Bene|
|:-|
|Il est possible que les images en "vraie couleurs" vous paraissent avoir des couleurs un peu surnaturelles.|
|C'est normal, les caméras de Sentinel 2 n'aquièrent pas exactement les mêmes longueurs d'onde que les cellules de notre rétine.|
|Si vous étiez dans l'espace, vous ne vériez donc pas tout à fait les mêmes couleurs avec votre oeil.|

Ces quelques affichages semblent confirmer la pertinence d'utiliser la "couleurs" des pixels de l'image dans ces 4 bandes pour classifier les types de surface au sol.

## Classification supervisée

### L'apprentissage supervisée

### Base de données labélisée

### Classifieur Naive Bayes

### Mise à l'échelle

## Entrainement

## Test

## Généralisation

---

# Etude de l'îlot de chaleur urbain et de la variation de température entre espace urbain et rural dans la région d'Île-De-France.

  Au cours du second semestre du master 2 Télédétection et Géomatique Appliquées à l'Environnement (TGAE) en 2019, j'ai eu à réaliser un mini-projet. Celui-ci s'intéresse aux variations de températures liées aux effets de l'îlot de chaleur urbain à partir de données obtenues par des stations météo de particuliers. L'étude a été effectuée avec des logiciels SIG (ArcGIS 10.5.1 et QGIS 2.18.15). De plus, les traitements statistiques ont été faits avec le logiciel RStudio.




## 1. Introduction

  L'agglomération de Paris est touchée comme chaque ville fortement urbanisée, par une élévation des températures. Elle se produit la nuit, lors de l'évaporation de la chaleur, piégée et enmaganisée par la surface du sol durant la journée. Ce phénomène est nommé "[Îlot de Chaleur Urbain](https://www.grandlyon.com/fileadmin/user_upload/media/pdf/voirie/referentiel-espaces-publics/20091201_gl_referentiel_espaces_publics_thematique_lutte_contre_ilots_chaleur_urbains.pdf)" (ICU). L'objectif de cette étude est d'appréhender ces variations de température dû à l'ICU dans la région de l'Île-de-France. 
  
  

![ICU](https://user-images.githubusercontent.com/48625647/57013235-d6291480-6c0a-11e9-85d5-4364b2b2e150.png)
  *Figure 1*




  On observe un ICU pour l'agglomération de Paris comparé à ses alentours (moins urbanisé). Pour simplifier il y a un décalage de température la nuit entre la "ville" et la "campagne" car les caractéristiques de la surface du sol et la morphologie de l'espace sont différentes. En effet les rayons lumineux piégés entre les hauts bâtiments accumulent la chaleur, puis l'absorption et la retention de la chaleur diffèrent selon le type de sol (fig.1). Ce qui est particulièrement intéressant dans cette étude est d'essayer de montrer la variation décroissante de la température, depuis le centre de Paris jusqu'au limite de l'Île-de-France, à partir de données libres acquises par des particuliers.
  
Le choix de la zone d'étude s'est porté sur l'endroit où la position des stations était le plus homogène. Nous avons sélectionné le sud de Paris et les alentours, afin d'avoir une zone tranversale (fig.2). En effet celle-ci se situe à l'intérieur d'une partie de l'agglomération de Paris (ville) et va jusqu'au delà de la petite couronne (zone rurale). 




![zoneetude](https://user-images.githubusercontent.com/48625647/57020473-a0dfef00-6c29-11e9-96e8-e653a7ac3e06.png)
    *Figure 2*


## 2. Méthodologie

### 2.1 Détails des données d'entrée

#### 2.1.1 Données de températures

- 2 jours de données des stations météo: 
    - 1 jour avec un effet de l'ICU élevé (08/05/2018)
    - 1 jour avec un effet de l'ICU faible (13/09/2017)

> **Note**: Les données contiennent l'identification et la localisation de chaque station météo (fig.3), avec une température donnée à chaque heure de la journée. 



![station](https://user-images.githubusercontent.com/48625647/57014438-69b11400-6c10-11e9-9631-de13c6bc9ca3.png)
  *Figure 3*


#### 2.1.2 Données d'emprises du bâtiment

- Base de données Apur :
    - [Emprise du bâtiment Paris](https://www.iau-idf.fr/fileadmin/NewEtudes/Etude_1270/Les_ilots_morphologiques_urbains.pdf)
    - [Emprise du bâtiment Petite Couronne](https://www.apur.org/open_data/EMPRISE_BATIE_PC_OD.pdf)

> **Note**: Les données contiennent une couche "Shapefile" avec les entités de chaque bâtiment de Paris et de la Petite Couronne (fig.4). Les entités sont également accompagnées d'informations sur leur datation et hauteur. 



![emprisbati](https://user-images.githubusercontent.com/48625647/57014436-69187d80-6c10-11e9-9857-ce9f8f13468e.png)
  *Figure 4*

#### 2.1.3 Données d'emprises de l'îlot morphologique urbain

- Base de données de l'Institut d'Aménagement et d'Urbanisme de la région Île-de-France :
    - [Ilôts Morphologiques Urbains (IMU)](https://www.iau-idf.fr/fileadmin/NewEtudes/Etude_1270/Les_ilots_morphologiques_urbains.pdf)

> **Note**: Les données contiennent un "Shapefile" avec les entités de chaque IMU d'Île-de-France . L'IAU s'est inpiré pour parfaire leurs classes, de plusieurs exemples d'îlot et de recherches, tel que la classification Local Climate Zone ([LCZ](https://iainstew.files.wordpress.com/2013/01/seattle.pdf)) de Stewart et Oke . Les îlots morphologiques urbains comprennent différents mode d'occupation du sol avec un indice typo-morphologique de la rugosité urbaine renseigné dans le champ "Classe_IMU" (fig 5). Celui-ci est calculé à partir du coefficient d'emprise au sol, de la densité du bâti volumique et de la hauteur moyenne pondéré du bâti. Si l'indice est élevé la rugosité urbaine est importante, c'est à dire une concentration et des hauteurs de bâtiment fortes. Voir ci-dessous :


![IMU](https://user-images.githubusercontent.com/48625647/57013236-d6c1ab00-6c0a-11e9-9520-510c7ae50765.png)

  *Figure 5*

#### 2.1.4 Données satellites SENTINEL2

- [Image satellite SENTINEL2 de Level-2A](https://earth.esa.int/web/sentinel/technical-guides/sentinel-2-msi/level-2a/algorithm) :
    - Image du 6 mai 2018 
    - Image du 27 août 2018 

> **Note**: Les images satellites SENTINEL2 de Level-2A (fig.6) comportent une correction atmosphérique qui vise à transformer la réflectance Top Of Atmosphere (TOA) en réflectance Bottom Of Atmosphere (BOA). Les images ont des résolutions de 10,20 et 60m. 


   
![sentinel2_2018](https://user-images.githubusercontent.com/48625647/57014437-69187d80-6c10-11e9-8e27-4c608b30befd.png)
  *Figure 6*


### 2.2 Description de la méthodologie

L'étude repose sur les valeurs de températures obtenues de stations météo détenues par des particuliers. Chaque station est localisée par ses coordonnées géographiques. Le but de cette méthodologie est d'utiliser les outils de géomatique afin de caractéristiser l'espace autour de chaque station. En effet, l'environnement autour d'une station pourrait nous permettre de distinguer des types d'espace particuliers qui serait propice à des températures importantes ou non. La position des stations pourrait également nous permettre de vérifier l'effet de l'ICU entre la ville de Paris et ses alentours. Tout d'abord nous allons sélectionner dans notre base de données pour les deux jours, les températures calculées de 19h à 6h. 

> **Note**: Les effets de l'ICU se produisent la nuit et c'est pour cette raison que nous sélectionnons les températures dans cette tranche horaire.

A partir de RStudio nous allons pouvoir directement extraire les horaires des températures qui nous intéressent afin de les sauvegarder en format CSV et les ouvrir ensuite sur ArcGIS. Voir script R ci-dessous : 


```
#Lecture des fichiers pour les deux jours de données
jour1 <- readRDS("C:/Users/Quentin/Desktop/Mini_projet/R/Quentin_ex1.rds", refhook = NULL)
jour2 <- readRDS("C:/Users/Quentin/Desktop/Mini_projet/R/Quentin_ex2.rds", refhook = NULL)

library(dplyr)

#Choix des mesures de température pour la tranche horaire 19h-6h.
DonneesJ1H19_6 <- subset(jour1, jour1$H == 19 | jour1$H == 20 | jour1$H == 21 | jour1$H == 22 | jour1$H == 23 | jour1$H == 0 
| jour1$H == 1 | jour1$H == 2 | jour1$H == 3 | jour1$H == 4 |jour1$H == 5 |jour1$H == 6 )
DonneesJ2H19_6 <- subset(jour2, jour2$H == 19 | jour2$H == 20 | jour2$H == 21 | jour2$H == 22 | jour2$H == 23 | jour2$H == 0 
| jour2$H == 1 | jour2$H == 2 |jour2$H == 3 |jour2$H == 4 |jour2$H == 5 |jour2$H == 6)
DonneesJ1H19_6 <- subset(jour2, jour2$H == 23)
DonneesJ2H19_6 <- subset(jour2, jour2$H == 23)


#Sauvegarde en format CSV.
write.csv(DonneesJ1H19_6, file = "DonneesJ1H19_6.csv",row.names=FALSE)
write.csv(DonneesJ2H19_6, file = "DonneesJ2H19_6.csv",row.names=FALSE)

```


Afin de caractériser l'environnement de chaque station, nous allons créer des [zones tampons](http://desktop.arcgis.com/fr/arcmap/10.3/tools/coverage-toolbox/buffer.htm) de 50, 100 et 200m autour de chaque station météo (ref fig.7) localisé sur notre zone d'étude. Nous prenons trois distances de zone tampon afin de s'assurer un résultat le plus optimum, pour la description de l'espace autour de la station et une possibilité de liaison avec la variation de température. 



![buffer](https://user-images.githubusercontent.com/48625647/57566873-8b10bd80-73d1-11e9-8bbd-abe53beec57b.png)
  *Figure 7*


A l'intérieur de ces zones tampons, nous voulons essayer de caractériser l'environnement par rapport à l'occupation du sol et à la rugosité des bâtiments. Pour cela nous utiliserons nos données d'entrée (ref 2.1) afin de représenter pour chaque zone tampon la surface de:
- l'emprise du bâtiment pour la ville de Paris et la Petite Couronne (ref fig.4). A l'intérieur de cette base, il y a un champ des hauteurs médiannes et moyennes pour chaque emprise de bâtiment qui seront utilisés.
- l'emprise de l'IMU du "bâti résidentiel" (ref fig.5), car elle est majoritaire dans notre zone d'étude. Nous allons plutôt nous intéresser précisement à l'indice typo-morphologique de cet IMU, pour mettre l'accent sur la rugosité urbaine de ces emprises.
- l'emprise de la végétation obtenue grâce au NDVI calculé à partir des images satellites SENTINEL2 (ref fig.6).
- l'emprise des zones où la visualisation du ciel est faible obtenue par un seuillage des valeurs proches de 0 d'un SVF calculé avec l'extension SAGA sur QGIS.

L'étude concernant le géotraitement a été réalisé avec ModelBuilder afin de gagner du temps sur la répétition de la chaîne de traitement, sur les différentes zones tampons pour les deux jours. Cette étude est réalisée en 3 étapes :


#### 2.2.1 Etape 1 : Emprise du bâtiment 


Dans cette partie, nous voulons obtenir la surface et la hauteur du bâti à l'intérieur de chaque zone tampon. Tout d'abord on va [intersecter](http://desktop.arcgis.com/fr/arcmap/10.3/tools/analysis-toolbox/intersect.htm) (fig.8) notre couche d'emprise du bâtiment de Paris et de la Petite Couronne avec nos zones tampons. Voir le résultat ci-dessous :



![inter_empbati](https://user-images.githubusercontent.com/48625647/57386539-d601b980-71b4-11e9-8ff1-c53596bbcea7.png)
  *Figure 8*


Afin d'être rigoureux dans la caractérisation de l'environnement des stations météo, nous allons calculer une hauteur pondérée à l'intérieur de notre zone tampon, au lieu d'estimer une moyenne de la hauteur. En effet la hauteur pondérée permet de prendre en compte la surface du bâti ce qui donne un poid plus ou moins important à la hauteur de celui-ci. 



![exemple2](https://user-images.githubusercontent.com/48625647/57558573-81119f00-737e-11e9-8812-25b39e714905.png)

  *Figure 9*


Dans l'exemple figure 9, le bâtiment bleu à 10 étages avec une surface de 500m² et le bâtiment rouge à 2 étages avec une surface de 100m². On va pondérer le nombre d'étages des bâtiments par leur surface à l'intérieur de la zone tampon. Puis on divise par le total de surface de bâtiment dans la zone tampon. On a :

(10 x 500 + 2 x 100) / 600 = 8,6 m

Le résultat donne une hauteur pondérée de 8,6m. En effet le poids du bâtiment bleu est plus fort, car celui-ci à une emprise plus importante dans la zone tampon que le bâtiment rouge. La chaîne de traitement pour le calcul de la hauteur pondérée est à voir ci-dessous :



![fusion_empbati](https://user-images.githubusercontent.com/48625647/57386538-d601b980-71b4-11e9-98f0-90fea668b8c0.png)
  *Figure 10*


Afin de calculer notre hauteur pondérée, nous allons créer un champ pour le calcul de la hauteur fois la surface. Puis un champ pour le calcul de la surface du bâti dans notre zone tampon. A partir d'ArcGIS nous allons [fusionner](https://desktop.arcgis.com/fr/arcmap/latest/tools/coverage-toolbox/dissolve.htm) notre couche. Cela va nous permettre de sommer nos deux champs nouvellement créés dans notre table attributaire. Après traitement, il suffira de créer et calculer un nouveau champ qui sera notre hauteur pondérée. On divisera nos deux champs précédents afin d'en finir et d'obtenir comme résultat la hauteur pondérée pour chaque zone tampon (fig.10). Donc nous obtenons la surface du bâti, ainsi que la hauteur pondérée à l'intérieur de chaque zone tampon d'une station météo.


#### 2.2.2 Etape 2 : Emprise des IMU


Nous allons nous occuper maintenant des IMU dans notre zone d'étude. Nous nous intéresserons particulièrement au champ "Classe_IMU" ou se trouve l'indice typo-morphologique de rugosité urbaine (ref 2.1.3). Ici la très grande majorité d'indice dans notre zone d'étude est le bâti résidentiel (1). Nous utiliserons donc seulement cette classe. Cela va nous permettre de détailler la rugosité urbaine grâce aux indices 1, 11, 21, 31 et 41 (ref fig.5). 

> **Note**: N'ayant pas de numéro d'indice 51 et 61 dans notre zone étude, nous utiliserons les indices listés précédemment.

De la même manière que pour l'étape précédente, nous allons effectuer une intersection entre les emprises des IMU et nos zones tampons. Ensuite nous devons fusionner notre couche, afin d'obtenir une entité seulement pour chaque indice à l'intérieur de la zone. A l'intérieur de chaque zone tampon de nos stations météos, nous obtiendrons une ou plusieurs emprises IMU avec un indice de rugosité différents (fig 11).



![etape1IMU](https://user-images.githubusercontent.com/48625647/57558310-799dc600-737d-11e9-83e4-2f3916d97236.png)

  *Figure 11*



Le prochain traitement va nous permettre de calculer la surface de chaque emprise correspondant à un indice typo-morphologique, dans la zone tampon (fig 12).



![etape2_IMU](https://user-images.githubusercontent.com/48625647/57558311-799dc600-737d-11e9-9177-3143be7dd10f.png)
  *Figure 12*



L'intérêt d'effectuer la sélection de chaque indice de rugosité est pour la mise en forme de la donnée en sortie. En effet l'on veut avoir les surfaces de chaque indice de rugosité en colonne et non en ligne. Nous effectuons maintenant la [jointure attributaire](http://desktop.arcgis.com/fr/arcmap/10.3/manage-data/tables/joining-attributes-in-one-table-to-another.htm) entre nos couches d'indices et la couche de zone tampon. En sortie, nous avons notre couche de zone tampon avec à l'intérieur de notre table attributaire, la surface des emprises d'indices de rugosité en colonne (fig.13).



![etape3_imu](https://user-images.githubusercontent.com/48625647/57568192-2b6fdd80-73e4-11e9-9b30-a7923c52acfc.png)
  *Figure 13*


A la fin de notre traitement nous obtenons pour chaque zone tampon d'une station météo, la surface des emprises d'indices de rugosité (1,11,21,31 et 41)
> **Rappel**: L'indice typo-morphologique de rugosité urbaine est calculé à partir du coefficient d'emprise au sol, de la densité du bâti volumique et de la hauteur moyenne pondérée du bâti. Si l'indice est élevé la rugosité urbaine est importante, c'est à dire que la concentration et les hauteurs de bâtiments sont élévées.


#### 2.2.3 Etape 3 : Emprise de la végétation

La végétation est un facteur également essentiel pour la caractérisation des emprises. Celle-ci joue un rôle dans la diminution de l'ICU. Ici nous voulons évaluer la surface prise par la végétation dans chacune des zones tampons comportant une station météo. Pour cela nous avons téléchargé deux images satellites SENTINEL2 du 6 mai 2018 et du 27 août 2018. Nous voulons à partir des deux images calculer l'[indice de végétation par différence normalisé](https://www.dronesimaging.com/wp-content/uploads/2013/05/documentation/indice%20de%20v%C3%A9g%C3%A9tation%20NDVI.pdf) (ou NDVI). Les deux images ont un niveau Level-2A, c'est-à-dire que des traitements ont été effectués dessus et notamment une correction atmosphérique. Celle-ci a consisté à transformer la réflectance Top Of Atmosphere (TOA) en réflectance Bottom Of Atmosphere (BOA). Pour le calcul de NDVI, la valeur de réflectance BOA utilisée se rapprochera plus de la valeur mesurée au sol. La précision sur nos valeurs de NDVI n'en sera que plus forte. 
Nous prendrons les images satellites à une résolution de 10 mètres, où plusieurs bandes spectrales à disposition (fig.14).



![image_gallery](https://user-images.githubusercontent.com/48625647/57800626-1b445f00-7752-11e9-885f-a906dff049f6.jpg)

  *Figure 14*



Afin de calculer le NDVI pour les deux dates à partir d'ArcGIS et de la calculatrice raster, nous devons utiliser la bande du rouge (R) et du proche infrarouge (PIR). La formule est "NDVI = (PIR - R) / (PIR + R)". On obtient sur le NDVI pour notre zone d'étude (fig.15).



![NDVI2017](https://user-images.githubusercontent.com/48625647/57802580-97d93c80-7756-11e9-9820-b7fff286e15f.png)
*Figure 15*



A partir du NDVI nous allons pouvoir extraire les emprises de végétation. En effet, plus le NDVI est fort et plus il y a de présence de végétation. C'est pour cela que nous devons prendre un seuil du NDVI qui prendra en compte les emprises de végétation. Dans la littérature scientifique, les formations végétales ont des valeurs NDVI entre 0.1 et 0.7. Néanmoins à une résolution de 10m, la valeur du seuil pris pour l'image du 6 mai 2018 est de 0,5 et celle du 27 août 2017 est de 0,6. Nous avons évalué le résultat des emprises de végétation obtenu avec différents seuils afin de choisir le plus optimum. Néanmoins la valeur de seuil reste subjectif.
Afin d'obtenir les emprises de végétation, nous allons effectuer une [reclassification](http://desktop.arcgis.com/fr/arcmap/10.3/tools/spatial-analyst-toolbox/reclassify.htm). Les pixels de végétation (>0,6) sont classés dans la classe 1. Les autres sont classés en valeur NoData, car elles ne nous intéressent pas. Après ce traitement, nous allons calculer les statistiques à l'intérieur de chaque zone tampon afin d'en déterminer une surface de végétation. En effet, le traitement [statistiques zonales (table)](http://desktop.arcgis.com/fr/arcmap/10.3/tools/spatial-analyst-toolbox/zonal-statistics-as-table.htm) permet d'obtenir un comptage des pixels à l'intérieur de chaque zone tampon. Ensuite  on multiplie le nombre total de pixel obtenu lors du comptage par 100 , afin d'obtenir la surface. 

> **Note**: Connaissant la résolution de l'image de 10m nous savons que chaque pixel fait 10m de côté. La surface d'un carré est égale à côté fois côté, ce qui donne 100m² pour un pixel de 10m.

Après notre jointure attributaire entre notre  table de statistique et la couche des zones tampon, on a en sortie la surface de végétation à l'intérieur de chaque zone tampon (fig.16).



![vegetation_ndvi](https://user-images.githubusercontent.com/48625647/58127853-a45a0b00-7c16-11e9-91eb-549e9972badf.png)
*Figure 16*


#### 2.2.4 Etape 4 : Indice de visualisation : Sky-View-Factor

Le [Sky-View-Factor](https://www.researchgate.net/publication/49620296_Sky-View_Factor_as_a_Relief_Visualization_Technique) (SVF) utilise la partie visible du ciel ou l'hémisphère céleste (fig.17b). D'après l'étude "Sky-View-Factor as a Relief Visualization Technique" de Zaksek *et al.* publié en 2011, l'illumination du relief est corrélée à la portion du ciel visible qui est limité par l'horizon du relief. La méthode consiste à calculer l'angle d'élévation vertical de l'horizon "y1" dans n directions en spécifiant le rayon R (fig.17a).



![svf_l](https://user-images.githubusercontent.com/48625647/58647687-5cb73b80-8308-11e9-8d51-afa629976e1b.png)
*Figure 17*



La mesure du SVF est normalisée et la valeur proche de 1 signifie que l'hémisphère entier est visible comme des plaines ou des pics, tandis que les valeurs proches de 0 représentent les fonds de vallées, où le ciel n'est pratiquement pas visible. De plus, le changement du nombre de direction de recherche et le rayon de recherche maximum influencent la mesure, qui peut être optimisée.

Dans notre étude, nous nous intéressons au "couloir" formé par les bâtiments qui jouent un rôle dans le piège des rayons lumineux. Grâce à la visualisation SVF nous pouvons obtenir les valeurs d'indice qui correspondent aux fonds de vallées (proche de 0), c'est à dire où le ciel n'est pratiquement pas visible, comme entre les hauts bâtiments. Nous allons prendre un seuil pour les faibles valeurs, afin de calculer une surface des endroits où le ciel est moins visible à l'intérieur de chaque zone tampon. Pour cela nous allons avoir besoin d'un Modèle Numérique de Surface (fig.18).



![MNS_dessin](https://user-images.githubusercontent.com/48625647/58646942-c46c8700-8306-11e9-8781-ddfcf3ead701.png)

*Figure 18*



Avec nos données d'entrée, nous pouvons créer notre propre MNS avec la couche d'emprise du bâtiment et la couche d'emprise de la zone d'étude. Sachant que l'on s'intéresse seulement au bâtiment et que nous avons leurs hauteurs médianes, on considérera les zones non bâti comme une hauteur de 0m. On va [agréger](http://desktop.arcgis.com/fr/arcmap/10.3/tools/analysis-toolbox/union.htm) les emprises de bâtiment et l'emprise de la zone d'étude. Après nous allons [rasteriser](http://desktop.arcgis.com/fr/arcmap/10.3/tools/conversion-toolbox/feature-to-raster.htm) notre couche d'entités afin d'obtenir des valeurs de pixel correspondant à nos hauteurs de bâti et non-bâti (fig.19).



![creation_MNS](https://user-images.githubusercontent.com/48625647/58646940-c3d3f080-8306-11e9-8127-491908a04697.png)
*Figure 19*



Le résultat nous donne un MNS (fig.20) où seulement les hauteurs des bâtiments ont été prises en compte, puisqu'ils jouent un rôle important dans les effets de l'ICU.



![MNS](https://user-images.githubusercontent.com/48625647/58647509-06e29380-8308-11e9-8f87-353eb17a1dc5.png)
*Figure 20*



A partir du logiciel QGIS et de son extension SAGA, nous allons calculer l'indice de visualisation "Sky-View-Factor". En résulte un raster, où l'indice de visualisation a été calculé pour chaque pixel (fig.21). Plus l'indice est proche de 1 et plus la part du ciel visible est importante au niveau du pixel. En revanche plus l'indice est proche de 0 et plus la part du ciel visible est faible au niveau du pixel.



![svf_indice](https://user-images.githubusercontent.com/48625647/58759925-bd758c80-8531-11e9-81e1-3fa147b826b5.png)
*Figure 21*



Nous allons seuiller le raster en prenant en compte les endroits où la part du ciel visible est faible, afin de faire ressortir 
des zones où les rayons lumineux pourraient être potentiellement piégés dû à la présence de corridor entre les bâtiments. Après avoir analysé les valeurs de pixel autour des bâtiments, le seuil pris pour les zones où la part du ciel visible est faible sera 0.70. Nous prendrons donc en compte les valeurs inférieures à ce seuil. Nous pouvons déterminer ensuite la surface de ces zones, grâce au comptage des pixels à l'intérieur de chaque zone tampon. Cette étape ressemble à celle utilisée pour les emprises de végétation (2.2.3). Néanmoins comme pour la surface des emprises de végétation, le choix du seuil reste subjectif et un certain recul devra être pris sur les résultats. Nous obtenons à l'intérieur de chaque zone tampon, la surface de la zone où la part du ciel visible est potentiellement faible.

Nous avons terminé nos différentes étapes de la méthodologie. Nous obtenons dans notre table attributaire pour chaque station météo, la moyenne des températures (entre 19h et 6h), la surface de bâti, la hauteur pondérée, la surface de l'IMU batî avec les différents indices de rugosité (1,11,21,31 et 41), la surface de végétation et la surface des zones où la part du ciel visible est faible (fig.22). 


![csv](https://user-images.githubusercontent.com/48625647/59157096-39795280-8aa5-11e9-9b24-b233980af73d.png)
*Figure 22*

Avec ces différentes données obtenues de plusieurs sources, nous allons essayer de mettre en avant les variations de températures et notamment l'effet de l'ICU dans chacune de nos zones tampon à l'intérieur de notre zone d'étude.



## 3. Résultats

Nous nous intéressons à la caractérisation de l'environnement aux alentours de nos stations météo, afin de faire ressortir des espaces types qui expliqueraient les variations de températures dû à l'ICU. Nos différentes données obtenues composant notre tableau attributaire doivent être traitées. Pour cela, nous allons utiliser l'[Analyse en Composantes Principales](https://eric.univ-lyon2.fr/~ricco/cours/slides/ACP.pdf)(ACP), qui est un traitement statistiques multi-variés. Elle permet de résumer des grands tableaux de données en supprimant les informations
redondantes. De plus elle va permettre de faire des groupes de "ressemblances" et d'"oppositions" entre les différents individus (zones tampon des station météo) et les mettre en relation avec les différentes variables obtenues précédement.
Nous allons donc enregistrer en format CSV nos tableaux attributaires des différentes couches de zone tampon 50m, 100m et 200m, afin de les ouvrir sur le logiciel R-studio. 


### 3.1 Analyse en Composantes Principales

Ensuite avec le package FactoMineR , nous allons effectuer une ACP à partir de nos tableaux de données. Sachant que nos données n'ont pas les mêmes unités (mètres, degrés etc), nous devons les standardiser afin de les rendre comparable. Dans FactoMineR , la fonction ACP() par défault va normaliser les données de façon automatique.
La première étape est de faire ressortir les valeurs propres de chaque axe (composante). Sachant qu'il y a autant d'axe que de variable en entrée. Il faudra donc choisir combien de composantes gardées, en se basant sur leur quantité de variance exliqué.

> **Note**: La valeur propre est la quantité de variance expliqué, c'est à dire que la première composante va être l'axe expliquant le plus d'information. Au fur et à mesure la valeur propre décroit jusqu'à la dernière composante. C'est à partir de la valeur propre que nous choissirons le nombre de composantes principales à garder. On va donc visualiser les valeurs propres de chaque composante avec "$eig".


```
install.packages("FactoMineR")

library("FactoMineR")

#ACP pour le jour 2017_09 avec caclul des valeurs propre, du pourcetange de variance expliqué et le cumul du pourcentage de variance.
ACP50m<-PCA(ACPjour2017_50m)
eig.ACP50m <- ACP50m$eig
ACP100m<-PCA(ACPjour2017_100m)
eig.ACP100m <- ACP100m$eig
ACP200m<-PCA(ACPjour2017_200m)
eig.ACP200m <- ACP200m$eig

eig.ACP50m
eig.ACP100m
eig.ACP200m

```
On obtient pour les trois zones tampon des stations météo, leurs valeurs propres (fig.23). On remarque que pour la zone tampon de 200m de la journée 2017, sa première composante principale à une quantité de variance expliqué (33.67522) supérieure aux premières composantes pour l'ACP des zones tampon de 50m (32.76488) et 100m (33.62581). Néanmoins pour la zone tampon de 100m de la journée 2018, sa première composante principale à une quantité de variance expliqué (34.51747) supérieure aux premières composantes pour l'ACP des zones tampon de 50m (33.80514) et 200m (34.29261). Nous utiliserons donc la zone tampon de 100m pour notre analyse sur l'ACP des deux journées, car elle obtient une quantité de variance expliqué majoritairement plus importante. De plus cela permettra de comparer les deux journées avec une zone tampon de dimension égale.


![eig](https://user-images.githubusercontent.com/48625647/59158796-40618e80-8ac0-11e9-8917-f21be3154eaa.png)
*Figure 23*


Ici l'ACP a été effectué sur la journée de 2017. Néanmoins pour la journée de 2018, l'ACP de la zone tampon de 100m obtient une première composante principale avec la plus forte valeur propre par rapport aux autres zones tampons. Nous utiliserons comme pour la journée de 2017 la zone tampon de 100m. Après avoir analysé les valeurs propres, nous allons les visualiser dans le graphique (fig.24) afin de décider du choix du nombre de composante principale à conserver.

```

#Execution du graphique avec ajout de la ligne de valeur.

barplot(eig.ACP100m[, 2], names.arg = 1:nrow(eig.ACP100m),  col ="blue", ylim = c(0,40), main = "Pourcentage de variance expliquée par les composantes principales de l'ACP Jour 2017", xlab = "Composantes Principales", ylab = "Pourcentage de variance" )
lines(x = 1:nrow(eig.ACP100m ), eig.ACP100m[, 2], type = "o", col = "red")


```

![eig_variance](https://user-images.githubusercontent.com/48625647/59159314-3a6fab80-8ac8-11e9-80d2-537f8e0c3fb8.png)
*Figure 24*

Au vue des pourcentages de variance expliqué (fig.24) et des quantités précises et cumulatives du pourcentage de variance expliqué (fig.23), nous allons retenir 6 composantes principales pour la journée de 2017 et 2018. Le pourcentage de variance expliqué est donc de 85% pour 2017 et 2018, il est acceptable. Avant d'analyser le graphique de corrélation, nous allons voir les différents graphiques (fig.25) entre chaque variable afin de détailler les quelques corrélations qui pourrait être observable.

```

#Affichage des plots entre les différentes variables.
pairs(ACPjour2017_100m)

```

![plot](https://user-images.githubusercontent.com/48625647/59181176-039ca280-8b67-11e9-9074-e52cfc7e7377.png)
*Figure 25*


Nous pouvons distinguer quelques tendances entre variables mais leur distribution reste assez flou. Pour cela nous allons ensuite nous intéresser au graphique de corrélation des variables (fig.26) entre les deux premières composantes de la journée de 2017 et 2018.



![acp_var](https://user-images.githubusercontent.com/48625647/59159313-3a6fab80-8ac8-11e9-9825-5cf970f2f5b4.png)
*Figure 26*


> **Note**: Le graphique de corrélation des variables est constitué de deux axes. L'axe des abscisses correspond à la première composante et l'axe des ordonnés correspond à la deuxième composante, avec des valeurs de corrélation comprise entre -1 et 1.
Pour l'interprétation : Les variables situées en haut à droite sont corrélées positivement et au contraire les variables situées en bas à gauche sont corrélées négativement. De plus les variables qui sont le plus loin de l'origine sont les mieux représentées par l'ACP.

Sur le graphique de corrélation des variables de 2017 et 2018, on retrouve pour la première composante principale (axe des abscisses), des variables corrélées positivement que sont les températures moyennes , la surface de bâti, la hauteur pondérée et la surface des zones où la part du ciel visible est faible. L'interprétation de ces variables est que plus la température moyenne est élévée et plus les autres variables citées au dessus le seront à l'intérieur de la zone tampon. Reciproquement, la surface de végétation est corrélée négativement par rapport à la température moyenne. Donc plus la température moyenne est élévée et plus la surface de végétation sera faible. Le résultat paraît logique avec ce que nous avions analysé sur les effets de l'ICU.
Pour la deuxième composante (axe des ordonnées), on retrouve pour la journée de 2017 les variables de surface de l'IMU d'indice 21 notamment corrélées négativement alors que pour la journée de 2018, on a la variable qui est corrélée positivement.Vu l'éloignement de la variable à son origine, cette variable est bien représentée.


### 3.2 Classification Ascendante Hiérarchique

Ayant choisis le nombre de composante principale à garder, nous allons effectuer une [Classification Ascendante Hiérarchique](http://math.agrocampus-ouest.fr/infoglueDeliverLive/digitalAssets/100457_AnaDo_CLASSIF_cours_slides.pdf) (CAH) afin de classer nos stations météo dans des "cluster" et obtenir des espaces caractéristiques de l'ICU à partir de nos variables. A l'issue de cette classification, nous exporterons le tableau avec le numéro de cluster pour chaque individu vers un logiciel SIG afin de spatialiser l'information.
La CAH sera effectuée sur les résultats des ACP pour la zone tampon de 100m et en ayant conservé 6 composantes principales (fig.27).

```
library(FactoMineR)

#ACP (conservation de 6 composantes principales) pour le jour 2017
ACP100m<-PCA(ACPjour2017_100m,ncp = 6,graph = FALSE)
#CAH
CAH100m<-HCPC(ACP100m)

#ACP (conservation de 6 composantes principales) pour le jour 2018
ACP100m2<-PCA(ACPjour2018_100m,ncp = 6,graph = FALSE)
#CAH
CAH100m2<-HCPC(ACP100m2)

```


![CAH](https://user-images.githubusercontent.com/48625647/59183309-0352d600-8b6c-11e9-939d-dc34b18c337b.png)
*Figure 27*


L'interprétation des résultats de la CAH est à lier avec le graphique de corrélation des variables. En effet les groupes situés plus à droite vont être au niveau des variables de surface de bâti, de hauteur pondérée, de température moyenne et de surface de zone où la part du ciel visible est faible. Tandis que les groupes situés plus à gauche seront au niveau de la variable surface de végétation. Pour l'interprétation des résultats de la journée de 2017,  les cluster 4,5 et surtout le 6 serait un environnement très urbain avec des moyennes de température plus élevées. Au contaire les clusters 1,2 serait un environement plus végétal avec des températures moyennes moins importantes. Néanmoins le cluster 3 proche de l'origine est difficile à caractériser. Il en ressort également la même tendance pour la journée de 2018 avec les clusters 3,4 et 5 qui sont plutôt urbains avec une morphologie du bâti importante et des températures moyennes élevées. Alors que le cluster 1 est largement vers les surfaces végétalisées et des températures plus faibles. Puis le cluster 2 assez proche de l'origine qui reste difficile aussi à caractériser. Etant donné le nombre de stations météo (individu), la visibilité sur le détail du graphique de la CAH n'est pas lisible et c'est pour cela que nous allons exporter le résultat sur ArcGIS. En vue de cet export, cela nous permettra de vérifier nos hypothèses sur les différentes caractérisations de ces clusters à partir de leur représentation spatiale.


```

#Enregistrement de la table résultant de la CAH avec la colonne "cluster".

ResJour2017<-CAH100m$data.clust
ResJour2018<-CAH100m2$data.clust

#Export au format csv de la table

write.csv(ResJour2017, file = "ResJour2017.csv",row.names=TRUE)
write.csv(ResJour2018, file = "ResJour2018.csv",row.names=TRUE)

```


Après avoir importé sur ArcGIS nos deux tableaux au format csv, nous allons effectuer une jointure attributaire de notre table jusqu'à notre couche des stations météo. Nous pouvons grâce à la symbologie essayer d'interpréter la répartition spatiale des différents cluster par rapport à l'analyse des résultats de la CAH (fig.28).



![spatiale_cluster](https://user-images.githubusercontent.com/48625647/59196952-5d659280-8b90-11e9-9b83-ef6a8b1af4ee.png)
*Figure 28*



Que ce soit pour la journée de 2017 et de 2018, on voit que les clusters correspondants à du bâti dense et avec des températures moyennes plus élevées se situent à Paris. Plus nous nous éloignons de la capitale et plus les clusters caractérisés par une surface de végétation élevée et des températures moyennes plus faibles sont importantes. L'ICU à Paris est visible dans notre résultat. D'un côté les températures sont plus élevées avec une surface de bâti , une hauteur pondérée et des zones où le ciel est peu visible (succeptible de piégés les rayons) importantes. Au contraire lorsque que l'on regarde aux alentours de Paris les températures moyennes sont plus faibles et avec des surfaces de végétation plus importante. Néanmoins certaines exceptions sont présentent à Paris avec un cluster qui correspondrait à un environnement avec une végétation plus présente par rapport aux clusters alentours.


## 4. Conclusion

Notre étude s'est basée sur des données de température acquisent par des stations météo de particuliers. Afin de les analyser, nous avons acquis différentes données d'entrée libre comme les données d'Apur, de l'IAU et de SENTINEL-2. Nous avons extrait de ces données de différentes sources, des facteurs qui jouent sur les variations de températures et l'effet de l'ICU. En effet les surfaces de bâti ou végétation ont un rôle important. 

Grâce à nos données nous avons pu avoir une visualisation spatiale de l'effet de l'ICU à Paris et ses alentours. En effet à partir de nos résultats statistiques , nous avons pu caractériser les environnements autour de nos stations météo. Le résultat logique que nous retrouvons dans nos résultats est que les surfaces importantes de bâti se trouvent au endroit où les stations météo ont des valeurs de température élévée. A l'inverse les surfaces importantes de végétation se trouvent au endroit où les stations météo ont des valeurs de température faible. Spatialement, nous pouvons voir le contraste entre l'agglomération parisienne et les alentours. Néanmoins certaines de nos stations météo sont localement différentes de celles aux alentours.
Afin de rester critique sur nos résultats, il faut prendre un certain recul, notamment avec le seuillage de nos zones de végétation et de zones où la part du ciel est peu visible, qui ne reflète pas un choix totalement objectif. De plus le temps n'a pas permis de détailler vigoureusement l'intégralité de l'étude et des différents résultats secondaires obtenus lors des traitements statistiques. 

L'étude à permis de visualiser à partir de données libres, l'effet de l'ICU entre l'agglomération de Paris et ses alentours, en mettant l'accent sur les variations de température dû par exemple à un type de surface différent et une urbanisation dense qui à tendance à piéger les rayons qui seront emmaganisés en surface. 







# Etude de l'îlot de chaleur urbain et de la variation de température entre espace urbain et rurale dans la région d'Île-De-France.

  Au cours du second semestre de master 2 Télédétection et Géomatique Appliquées à l'Environnement (TGAE) en 2019, j'ai eu a réaliser un mini-projet. Celui-ci s'intéresse aux variations de températures liés aux effets de l'îlots de chaleur urbain à partir de données obtenus par des stations météo de particuliers. L'étude a été effectué avec des logiciels SIG (ArcGIS 10.5.1 et QGIS 2.18.15). De plus, les traitements statistiques ont été faits avec le logiciel RStudio.




## 1. Introduction

  L'agglomération de Paris est touché comme chaque ville fortement urbanisé, par une élévation des températures. Elle se produit la nuit, lors de l'évaporation de la chaleur, piégée et enmaganisée par la surface du sol durant la journée. Ce phénomène est nommé "Îlot de Chaleur Urbain" (ICU). L'objectif de cette étude est d'appréhender ces variations de température dû à l'ICU dans la région de l'Île-de-France. 
  
![ICU](https://user-images.githubusercontent.com/48625647/57013235-d6291480-6c0a-11e9-85d5-4364b2b2e150.png)
  *Figure 1*
  
  On observe un ICU pour l'agglomération de Paris comparés a ses alentours (moins urbanisé). Pour simplifier il y a un décalage de température la nuit entre la "ville" et la "campagne" car les caractéristiques de la surface du sol et la morphologie de l'espace est différentes. En effet les rayons lumineux piégés entre les hauts bâtiments accumulent la chaleur, puis l'absorption et la retention de la chaleur diffères selon le type de sol (fig.1). Ce qui est particulièrement intéressant dans cette étude est d'essayer de montrer la variation décroissante de la température, depuis le centre de Paris jusqu'au limite de l'Île-de-France, à partir de données libres acquises par des particuliers.
  
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

> **Note**: Les données contiennent un "Shapefile" avec les entités de chaque IMU d'Île-de-France . L'IAU se sont inpirés pour parfaire leurs classes, de plusieurs exemples d'îlot et de recherches, tel que la classification Local Climate Zone ([LCZ](https://iainstew.files.wordpress.com/2013/01/seattle.pdf)) de Stewart et Oke . Les îlots morphologiques urbains comprennent différents mode d'occupation du sol avec un indice typo-morphologique de la rugosité urbaine renseigné dans le champ "Classe_IMU" (fig 5). Celui-ci est calculé à partir du coefficient d'emprise au sol, de la densité du bâti volumique et de la hauteur moyenne pondéré du bâti. Si l'indice est élevé la rugosité urbaine est importante, c'est à dire une concentration et des hauteurs de bâtiment fortes. Voir ci-dessous :



![IMU](https://user-images.githubusercontent.com/48625647/57013236-d6c1ab00-6c0a-11e9-9520-510c7ae50765.png)
  *Figure 5*

#### 2.1.4 Données satellites SENTINEL2

- [Image satellite SENTINEL2 de Level-2A](https://earth.esa.int/web/sentinel/technical-guides/sentinel-2-msi/level-2a/algorithm) :
    - Image du 6 mai 2018 
    - Image du 27 août 2018 

> **Note**: Les images satellites SENTINEL2 de Level-2A (fig.6) comporte une correction atmosphériques qui vise à transformer la réflectance Top Of Atmosphere (TOA) en réflectance Bottom Of Atmosphere (BOA). Les images ont des résolutions de 10,20 et 60m. 


   
![sentinel2_2018](https://user-images.githubusercontent.com/48625647/57014437-69187d80-6c10-11e9-8e27-4c608b30befd.png)
  *Figure 6*


### 2.2 Description de la méthodologie

L'étude repose sur les valeurs de températures obtenus de stations météo détenus par des particuliers. Chaque station est localisée par ses coordonnées géographique. Le but de cette méthodologie est d'utiliser les outils de géomatique afin de caractéristisés l'espace autour de chaque station. En effet, l'environnement autour d'une station pourrait nous permettre de distinguer des types d'espace particuliers qui serait propice à des températures importantes ou non. La position des stations pourrait également nous permettre de vérifier l'effet de l'ICU entre la ville de Paris et ses alentours. 

Tout d'abord nous allons sélectionner dans notre base de donnée pour les deux jours, les températures calculée de 19h à 6h. 
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
- l'emprise du bâtiment pour la ville de Paris et la Petite Couronne (ref fig.4). A l'intérieur de cette base, il y a un champ des hauteurs médianne et moyenne pour chaque emprise de bâtiment qui seront utilisés.
- l'emprise de l'IMU du "bâti résidentiel" (ref fig.5), car elle est majoritaire dans notre zone d'étude. Nous allons plutôt nous intéresser précisement à l'indice typo-morphologique de cet IMU, pour mettre l'accent sur la rugosité urbaine de ces emprises.
- l'emprise de la végétation obtenu grâce au NDVI calculé à partir des images satellites SENTINEL2 (ref fig.6).
(- l'emprise des "fonds de vallées" obtenu (par un seuillage des valeurs proche de 0) d'un SVF calculé avec l'extension SAGA sur QGIS.)

L'étude concernant le géotraitement a été réalisé avec ModelBuilder afin de gagner du temps sur la répétition de la chaîne de traitement, sur les différentes zones tampons pour les deux jours. Cette étude est réalisés en 3 étapes :


#### 2.2.1 Etape 1 : Emprise du bâtiment 


Dans cette partie, nous voulons obtenir la surface et la hauteur du bâti à l'intérieur de chaque zone tampon. Tout d'abord on va [intersecter](http://desktop.arcgis.com/fr/arcmap/10.3/tools/analysis-toolbox/intersect.htm) (fig.8) notre couche d'emprise du bâtiment de Paris et de la Petite Couronne avec nos zones tampons. Voir le résultat ci-dessous :



![inter_empbati](https://user-images.githubusercontent.com/48625647/57386539-d601b980-71b4-11e9-8ff1-c53596bbcea7.png)
  *Figure 8*


Afin d'être rigoureux dans la caractérisation de l'environnement des stations météo, nous allons calculer une hauteur pondéré à l'intérieur de notre zone tampon, au lieu d'estimer une moyenne de la hauteur. En effet la hauteur pondéré permet de prendre en compte la surface du bâti ce qui donne un poid plus ou moins important à la hauteur de celui-ci. 



![exemple2](https://user-images.githubusercontent.com/48625647/57558573-81119f00-737e-11e9-8812-25b39e714905.png)
  *Figure 9*


Dans l'exemple figure 9, le bâtiment bleu à 10 étages avec une surface de 500m² et le bâtiment rouge à 2 étages avec une surface de 100m². On va pondérer le nombre d'étages des bâtiments par leur surface à l'intérieur de la zone tampon. Puis on divise par le total de surface de bâtiment dans la zone tampon. On a :

(10 x 500 + 2 x 100) / 600 = 8,6 m

Le résultat donne une hauteur pondéré de 8,6m. En effet le poid du bâtiment bleu est plus forte, car celui-ci à une emprise plus importante dans la zone tampon que le bâtiment rouge. La chaîne de traitement pour le calcul de la hauteur pondéré est à voir ci-dessous :



![fusion_empbati](https://user-images.githubusercontent.com/48625647/57386538-d601b980-71b4-11e9-98f0-90fea668b8c0.png)
  *Figure 10*


Afin de calculer notre hauteur pondéré, nous allons créer un champ pour le calcul de la hauteur fois la surface. Puis un champ pour le calcul de la surface du bâti dans notre zone tampon. A partir d'ArcGIS nous allons [fusionner](https://desktop.arcgis.com/fr/arcmap/latest/tools/coverage-toolbox/dissolve.htm) notre couche. Cela va nous permettre de sommer nos deux champs nouvellement créé dans notre table attributaire. Après traitement, il suffira de créer et calculer un nouveau champ qui sera notre hauteur pondérée. On divisera nos deux champs précédent afin d'en finir et d'obtenir comme résultat la hauteur pondéré pour chaque zone tampon (fig.10). Donc nous obtenons la surface du bâti, ainsi que la hauteur pondéré à l'intérieur de chaque zone tampon d'une station météo.


#### 2.2.2 Etape 2 : Emprise des IMU


Nous allons nous occuper maintenant des IMU dans notre zone d'étude. Nous nous intéresserons particulièrement au champ "Classe_IMU" ou se trouve l'indice typo-morphologique de rugosité urbaine (ref 2.1.3). Ici la très grande majorité d'indice dans notre zone d'étude est le bâti résidentiel (1). Nous utiliserons donc seulement cette classe. Cela va nous permettre de détailler la rugosité urbaine grâce aux indices 1, 11, 21, 31 et 41 (ref fig.5). 

> **Note**: N'ayant pas de numéro d'indice 51 et 61 dans notre zone étude, nous utiliserons les indices listés précédemment.

De la même manière que pour l'étape précédente, nous allons effectuer une intersection entre les emprises des IMU et nos zones tampons. Ensuite nous devons fusionner notre couche, afin d'obtenir une entité seulement pour chaque indice à l'intérieur de la zone. A l'intérieur de chaque zone tampon de nos stations météos, nous obtiendrons une ou plusieurs emprises IMU avec un indice de rugosité différents (fig 11).



![etape1IMU](https://user-images.githubusercontent.com/48625647/57558310-799dc600-737d-11e9-83e4-2f3916d97236.png)
  *Figure 11*



Le prochain traitement va nous permettre de calculer la surface de chaque emprise correspondant à un indice typo-morphologique, dans la zone tampon (fig 12).



![etape2_IMU](https://user-images.githubusercontent.com/48625647/57558311-799dc600-737d-11e9-9177-3143be7dd10f.png)
  *Figure 12*



L'intérêt d'effectuer la sélection de chaque indice de rugosité est pour la mise en forme de la données en sortie. En effet l'on veut avoir les surfaces de chaque indice de rugosité en colonne et non en ligne. Nous effectuons maintenant la jointure attributaire entre nos couches d'indices et la couche de zone tampon. En sortie, nous avons notre couche de zone tampon avec à l'intérieur de notre table attributaire, la surface des emprises d'indices de rugosité en colonne (fig.13).



![etape3_imu](https://user-images.githubusercontent.com/48625647/57568192-2b6fdd80-73e4-11e9-9b30-a7923c52acfc.png)
  *Figure 13*


Donc nous obtenons pour chaque zone tampon d'une station météo la surface des emprises d'indices de rugosité. 
> **Rappel**: L'indice typo-morphologique de rugosité urbaine est calculé à partir du coefficient d'emprise au sol, de la densité du bâti volumique et de la hauteur moyenne pondéré du bâti. Si l'indice est élevé la rugosité urbaine est importante, c'est à dire que la concentration et les hauteurs de bâtiments sont élévées.


#### 2.2.3 Etape 3 : Emprise de la végétation



## 3. Résultats
Statistiques

## 4. Conclusion
)


# Etude de l'îlot de chaleur urbain et de la variation de température entre espace urbain et rurale dans la région d'Île-De-France.

  Au cours du second semestre de master 2 Télédétection et Géomatique Appliquées à l'Environnement (TGAE) en 2019, j'ai eu a réaliser un mini-projet. Celui-ci s'intéresse aux variations de températures dû à l'îlots de chaleur urbain à partir de données obtenus par des stations météo de particuliers. L'étude a été effectué avec des logiciels SIG (ArcGIS 10.5.1 et QGIS 2.18.15). De plus, les traitements statistiques ont été faits avec le logiciel RStudio.

## 1. Introduction

  L'agglomération de Paris est touché comme chaque ville fortement urbanisé, par une élévation des températures. Elle se produit la nuit, lors de l'évaporation de la chaleur, piégée et enmaganisée par la surface du sol durant la journée. Ce phénomène est nommé "îlot de chaleur urbain" (ICU). L'objectif de cette étude est d'appréhender ces variations de température dû à l'ICU dans la région de l'Île-de-France. 
  
![ICU](https://user-images.githubusercontent.com/48625647/57013235-d6291480-6c0a-11e9-85d5-4364b2b2e150.png)
  
  On observe un ICU pour l'agglomération de Paris comparés a ses alentours (moins urbanisé). Pour simplifier il y a un décalage de température la nuit entre la "ville" et la "campagne" car les caractéristiques de la surface du sol et la morphologie de l'espace est différentes (rayons lumineux piégés entre les hauts bâtiments accumulant la chaleur, absorption et retention de la chaleur par le sol, ...). Ce qui est particulièrement intéressant dans cette étude est d'essayer de montrer la variation décroissante de la température, depuis le centre de Paris jusqu'au limite de l'Île-de-France, à partir de données libres acquises par des particuliers.
  
Le choix de la zone d'étude s'est porté sur l'endroit où la position des stations était le plus homogène. Nous avons sélectionné le sud de Paris et les alentours, afin d'avoir une zone tranversale. En effet celle-ci se situe à l'intérieur d'une partie de l'agglomération de Paris (ville) et va jusqu'au delà de la petite couronne (zone rurale). On peut voir ci-dessous sa localisation :

![zoneetude](https://user-images.githubusercontent.com/48625647/57020473-a0dfef00-6c29-11e9-96e8-e653a7ac3e06.png)
  

## 2. Méthodologie

### 2.1 Détails des données d'entrée

- 2 jour de données des stations météo: 
    - 1 jour avec un effet de l'ICU élevé (08/05/2018)
    - 1 jour avec un effet de l'ICU faible (13/09/2017)

Les données contiennent l'identification et la localisation de chaque station météo, avec une température donnée à chaque heure de la journée. Voir ci-dessous :


![station](https://user-images.githubusercontent.com/48625647/57014438-69b11400-6c10-11e9-9631-de13c6bc9ca3.png)


- Base de données Apur :
    - Emprise du bâtiment PARIS
    - Emprise du bâtiment PETITE COURONNE

Les données contiennent un "Shapefile" de l'emprise des bâtiments de [Paris](https://www.iau-idf.fr/fileadmin/NewEtudes/Etude_1270/Les_ilots_morphologiques_urbains.pdf) et de la [Petite Couronne](https://www.apur.org/open_data/EMPRISE_BATIE_PC_OD.pdf). Leurs délimitations au sol est également accompagnées d'informations sur leur datation et hauteur. Voir ci-dessous :



![emprisbati](https://user-images.githubusercontent.com/48625647/57014436-69187d80-6c10-11e9-9857-ce9f8f13468e.png)


- Base de données IAU :
    - Ilôts morphologiques urbains ILE-DE-FRANCE

Les données contiennent un "Shapefile" de la délimitation des îlots morphologiques urbains ([IMU](https://www.iau-idf.fr/fileadmin/NewEtudes/Etude_1270/Les_ilots_morphologiques_urbains.pdf)) d'Île-de-France . L'IAU se sont inpirés de plusieurs exemples d'îlot et de recherches, tel que la classification Local Climate Zone ([LCZ](https://iainstew.files.wordpress.com/2013/01/seattle.pdf)) de Stewart et Oke . Les îlots morphologiques urbains comprennent différents mode d'occupation du sol avec un indice typo-morphologique de la rugosité urbaine. Celui-ci est calculé à partir du coefficient d'emprise au sol, de la densité du bâti volumique et de la hauteur moyenne pondéré du bâti. Si l'indice est élevé la rugosité urbaine est importante, c'est à dire une concentration et des hauteurs de bâtiment fortes. Voir ci-dessous :



![IMU](https://user-images.githubusercontent.com/48625647/57013236-d6c1ab00-6c0a-11e9-9520-510c7ae50765.png)


- Image satellite SENTINEL2 de Level-2A :
    - Image du 6 mai 2018 ILE-DE-FRANCE
    - Image du 27 août 2018 ILE-DE-FRANCE

Les images satellites SENTINEL2 de [Level-2A](https://earth.esa.int/web/sentinel/technical-guides/sentinel-2-msi/level-2a/algorithm) comporte une correction atmosphériques qui vise à trnasformer la réflectance Top Of Atmosphere (TOA) en réflectance Bottom Of Atmosphere (BOA). Les images ont des résolutions de 10,20 ou 60m. Voir ci-dessous :


   
![sentinel2_2018](https://user-images.githubusercontent.com/48625647/57014437-69187d80-6c10-11e9-8e27-4c608b30befd.png)



### 2.2 Description de la méthodologie

L'étude repose sur les valeurs de températures obtenus de stations météo détenus par des particuliers. Chaque station est localisée par ses coordonnées géographique. Le but de cette méthodologie est d'utiliser les outils de géomatique afin de caractéristisés l'espace autour de chaque station. En effet, l'environnement autour d'une station pourrait nous permettre de distinguer des types d'espace particuliers qui serait propice à des températures importantes ou non. La position des stations pourrait également nous permettre de vérifier l'effet de l'ICU entre la ville de Paris et ses alentours. 

Tout d'abord nous allons sélectionner dans notre base de donnée pour les deux jours, les températures calculée de 19h à 6h. Les effets de l'ICU se produisent la nuit et c'est pour cette raison que nous sélectionnons les températures dans cette tranche horaire. A partir de RStudio nous allons pouvoir directement extraire les horaires des températures qui nous intéressent afin de les sauvegarder en format CSV et les ouvrir ensuite sur ArcGIS. Voir script ci-dessous : 

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

Afin de caractériser l'environnement de chaque station, nous allons créer des zones tampons de 50, 100 et 200m autour de chaque station météo (figure ci-dessous)localisé sur notre zone d'étude. Nous prenons trois distances de zone tampon afin de s'assurer un résultat le plus optimum, pour la description de l'espace autour de la station et une possibilité de liaison avec la variation de température. 


![buffer](https://user-images.githubusercontent.com/48625647/57017363-37f37980-6c1f-11e9-8b59-d4693cde4231.png)


A l'intérieur de ces zones tampons, nous voulons essayer de caractériser l'environnement par rapport à l'occupation du sol et à la rugosité des bâtiments. Pour cela nous utiliserons différentes données (voir 2.1) afin de représenter pour chaque zone tampon la surface de:
- l'emprise de l'IMU du "bâti résidentiel", car elle est majoritaire dans notre zone d'étude. Nous allons plutôt nous intéresser précisement à l'indice typo-morphologique de cet IMU, pour mettre l'accent sur la rugosité urbaine de ces emprises.
- l'emprise du bâtiment pour la ville de Paris et la Petite Couronne. A l'intérieur de cette base, il y a un champ des hauteurs médianne et moyenne pour chaque emprise de bâtiment qui seront utilisés.
- l'emprise de la végétation obtenu grâce au NDVI calculé à partir des images satellites SENTINEL2.
(- l'emprise des "fonds de vallées" obtenu (par un seuillage des valeurs proche de 0) d'un SVF calculé avec l'extension SAGA sur QGIS.)

L'étude concernant le géotraitement a été réalisé avec ModelBuilder afin de gagner du temps sur la répétition de la chaîne de traitement, sur les différentes zones tampons pour les deux jours. Cette étude est réalisés en 3 étapes :


#### 2.2.1 Emprise du bâtiment 

Nous intersectons les zones tampons avec l'emprise du bâti


![inter_empbati](https://user-images.githubusercontent.com/48625647/57386539-d601b980-71b4-11e9-8ff1-c53596bbcea7.png)


Nous fusionons l'meprise bâti afin de sommer les champs "SurfxHmed" et "SurfBâti"


![fusion_empbati](https://user-images.githubusercontent.com/48625647/57386538-d601b980-71b4-11e9-98f0-90fea668b8c0.png)


Nous calculons la hauteur pondéré


![exemple_htpond](https://user-images.githubusercontent.com/48625647/57386537-d5692300-71b4-11e9-9d2b-a883eac091f2.png)



#### 2.2.2 Emprise des IMU



#### 2.2.3 Emprise de la végétation



## 3. Résultats
Statistiques

## 4. Conclusion

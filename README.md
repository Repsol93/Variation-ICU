
# Etude de l'îlot de chaleur urbain et de la variation de température entre ville et zone rurale dans la région d'Île-De-France.

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
Les données contiennent un "Shapefile" de l'emprise des bâtiments de Paris et de la petite couronne. Leurs délimitations au sol est également accompagnées d'informations sur leur datation et hauteur. Voir ci-dessous :



![emprisbati](https://user-images.githubusercontent.com/48625647/57014436-69187d80-6c10-11e9-9857-ce9f8f13468e.png)


- Base de données IAU :
    - Ilôts morphologiques urbains ILE-DE-FRANCE
Les données contiennent un "Shapefile" de la délimitation des îlots morphologiques urbains (IMU) d'Île-de-France. L'IAU se sont inpirés de plusiseurs exemples d'îlot et de recherches, tel que la classification Local Climate Zone (LCZ) de Stewart et Oke. Les îlots morphologiques urbains comprennent différents mode d'occupation du sol avec un indice typo-morphologique de la rugosité urbaine. Celui-ci est calculé à partir du coefficient d'emprise au sol, de la densité du bâti volumique et de la hauteur moyenne pondéré du bâti. Si l'indice est élevé la rugosité urbaine est importante, c'est à dire une concentration et des hauteurs de bâtiment fortes. Voir ci-dessous :



![IMU](https://user-images.githubusercontent.com/48625647/57013236-d6c1ab00-6c0a-11e9-9520-510c7ae50765.png)


- Image satellite SENTINEL2 de Level-2A :
    - Image du 6 mai 2018 ILE-DE-FRANCE
    - Image du 27 août 2018 ILE-DE-FRANCE
Les images satellites SENTINEL2 de Level-2A comporte une correction atmosphériques qui vise à trnasformer la réflectance Top Of Atmosphere (TOA) en réflectance Bottom Of Atmosphere (BOA). Les images ont des résolutions de 10,20 ou 60m. Voir ci-dessous :


   
![sentinel2_2018](https://user-images.githubusercontent.com/48625647/57014437-69187d80-6c10-11e9-8e27-4c608b30befd.png)


- Image du SVF ( à voir si on en parle)


### 2.2 Description générale

L'étude repose sur les valeurs de températures obtenus de stations météo détenus par des particuliers. Chaque station est localisée par ses coordonnées géographique. Le but de cette méthodologie est d'utiliser les géotraitements afin de caractéristisés l'espace autour de chaque station. En effet, l'environnement autour d'une station pourrait nous permettre de distinguer des types d'espace particuliers qui serait propice à des températures importantes ou non. La position des stations pourrait également nous permettre de vérifier l'effet de l'ICU entre la ville de Paris et ses alentours. 

Afin de caractériser l'environnement de chaque station, nous allons créer des zones tampons de 50, 100 et 200m autour de la station (figure ci-dessous). Nous prenons trois distances de zone tampon afin de s'assurer un résultat le plus optimum, pour la description de l'espace autour de la station et une possibilité de liaison avec la variation de température. 


![buffer](https://user-images.githubusercontent.com/48625647/57017363-37f37980-6c1f-11e9-8b59-d4693cde4231.png)


A l'intérieur de ces zones tampons, nous voulons décrire l'environnement par rapport à l'occupation du sol et à la rugosité des infrastructures. Pour cela nous utiliserons différentes données (voir 2.1) afin de représenter pour chaque zone tampon la surface de:
- l'emprise de l'IMU du "bâti résidentiel", car elle est majoritaire dans notre zone d'étude. Nous allons plutôt nous intéresser précisement à l'indice typo-morphologique de cet IMU, pour mettre l'accent sur la rugosité urbaine de ces emprises.
- l'emprise du bâtiment pour la ville de Paris et la Petite Couronne. A l'intérieur de cette base, il y a les hauteurs médianne et moyenne des emprises de bâtiments qui seront utilisés.
- l'emprise de la végétation obtenu grâce au NDVI calculé à partir des images satellites SENTINEL2.
- l'emprise des "fonds de vallées" obtenu (par un seuillage des valeurs proche de 0) d'un SVF calculé avec l'extension SAGA sur QGIS.



## 3. Résultats


## 4. Conclusion

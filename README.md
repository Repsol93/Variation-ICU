## Introduction

  L'agglomération de Paris est touché comme chaque ville fortement urbanisé, par une élévation des températures. Elle se produit la nuit, lors de l'évaporation de la chaleur, piégée et enmaganisée par la surface du sol durant la journée. Ce phénomène est nommé "îlot de chaleur urbain" (ICU). L'objectif de cette étude est d'appréhender ces variations de température dû à l'ICU dans la région de l'Île-de-France. 
  
![ICU](https://user-images.githubusercontent.com/48625647/57013235-d6291480-6c0a-11e9-85d5-4364b2b2e150.png)
  
  On observe un ICU pour l'agglomération de Paris comparés a ses alentours (moins urbanisé). Pour simplifier il y a un décalage de température la nuit entre la "ville" et la "campagne" car les caractéristiques de la surface du sol et la morphologie de l'espace est différentes (rayons lumineux piégés entre les hauts bâtiments accumulant la chaleur, absorption et retention de la chaleur par le sol, ...). Ce qui est particulièrement intéressant dans cette étude est d'essayer de montrer la variation décroissante de la température, depuis le centre de Paris jusqu'au limite de l'Île-de-France, à partir de données libres acquises par des particuliers.
  


## Méthodologie

Détails des données d'entrée:

- 2 jour de données des stations météo: 
    - 1 jour avec un effet de l'ICU élevé (08/05/2018)
    - 1 jour avec un effet de l'ICU faible (13/09/2017)
Les données contiennent pour chaque station localisé et identifié, une température à chaque heure de la journée.

- Base de données Apur :
    - Emprise du bâtiment PARIS
    - Emprise du bâtiment PETITE COURONNE
Les données contiennent un "Shapefile" de l'emprise des bâtiments de Paris et de la petite couronne. Leurs délimitations au sol est également accompagnées d'informations sur leur datation et hauteur.

- Base de données IAU :
    - Ilôts morphologiques urbains ILE-DE-FRANCE
Les données contiennent un "Shapefile" de la délimitation des îlots morphologiques urbains d'Île-de-France. L'IAU se sont inpirés de plusiseurs exemples d'îlot et de recherches, tel que la classification Local Climate Zone (LCZ) de Stewart et Oke. Les îlots morphologiques urbains comprennent différents mode d'occupation du sol avec un indice typo-morphologique de la rugosité urbaine. Celui-ci est calculé à partir du coefficient d'emprise au sol, de la densité du bâti volumique et de la hauteur moyenne pondéré du bâti.


![IMU](https://user-images.githubusercontent.com/48625647/57013236-d6c1ab00-6c0a-11e9-9520-510c7ae50765.png)



- Image satellite SENTINEL2 de Level-2A :
    - Image du 6 mai 2018 ILE-DE-FRANCE
    - Image du 27 août 2018 ILE-DE-FRANCE

    



## Résultats


## Conclusion

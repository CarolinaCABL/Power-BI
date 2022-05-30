# Loyer dans les villes métropolitaines. Analyse sur le marché de logements indien.
### Ecrit par Carolina BORRE.
### M2 CEE Université Lumière Lyon 2. 
### 2021-2022.

Les agglomérations dans les grandes villes sont un phénomène largement étudié en économie dû les conséquences sur le niveau de vie, notamment dans le prix des loyers. Plus grande est une ville, moins de sol est disponible ce qui fait monter les prix par mètre carré à des niveau parfois exorbitants.
Au lieu de se concentrer dans le prix du sol, dans cette analyse nous allons nous concentrer sur prix du loyer, spécifiquement pour 8 villes métropolitaines en Inde (Dehli, Mumbai, Kolkata, Pune, Ahmedabad, Bangalore et Hyberabad). 
La première idée qui vient à l’esprit lorsque l’on parle du prix du bail, est que celui-ci augmente proportionnellement à la surface : le loyer est d'autant plus élevé que logement est grand (area en M2). Pourtant, des autres facteurs peuvent aussi rentrer en jeu comme la division du logement, par exemple, salle de bain et WC séparés, l'équipement compris (meublé ou pas meublé), qu'il soit loué par une agence ou un particulier, et surtout, la localisation de l'immeuble (quartier/ville).
Pour approfondir sur cette idée, nous allons analyser les prix de loyer pour 8 villes métropolitaines en Inde en fonction d'un ensemble de caractéristiques du logement. La base de données est issue de la plateforme web Kaggle (House Rent Prices of Metropolitain Cities in India) et consiste en une extraction de données la page web www.makaan.com avec la technique de web scrapping en utilisant l'extension Instant Data Scraper de Chrome. 

## Les variables
Nous comptons avec une base de données par ville. Chacune de ces bases est composée de 9 variables :

- Type de vendeur (seller_type): Variable catégorielle prenant les valeurs owner, agence et builder.
- Chambres (bedroom) : Variable numerique indiquant le nombre de chambres dans le logement
- Type d'agencement (layout_type) : Variable representant l’organisation de l’appartement. Deux catégories sont définies en Inde : BHK (Bathroom – Hall - Kitchen) et RK (Room - Kitchen).
- Type de propriété (property_type) : Catégorielle indiquant si le logement est un appartement, un logement indépendant, une maison indépendante, un penthouse, un studio ou une villa.
- Localité (Locality) : Localité (quartier) où le logement est situé. 
- Prix (Price) : Variable numérique donnant le prix du bail en roupies indiennes.
- Area : Superficie du logement en pied carrés. 
- Type de mobilier (Furnish type) : Variable prenant 3 catégories : Meublé, semi – meublé et pas meublé (furnished, semi-furnished and unfurnished).

## Préparation des données (nettoyage et transformation)
Nous sommes intéressés en analyser le prix du loyer pour les 8 métropoles simultanément. Pour cela, nous allons créer une base de données qui ressemble les 8 bases de données. Cette opération produit une base de données de 196.868 observations.
Pour pouvoir identifier les logements appartenant à chaque ville après la jointure, il convient de créer une variable "ville" indiquant la ville à laquelle chaque observation appartient. Cela signifie que nous créons une nouvelle variable qui prend les noms : Dehli, Mumbai, Kolkata, Pune, Ahmedabad, Bangalore et Hyberabad. 
### _Nettoyage_
Il y a environ un millier d'observations manquantes pour chaque variable. Nous ne pouvons pas remplacer ces données par la moyenne car la plupart des variables sont qualitatives, donc on les a effacés simplement. Nous avons perdu 3.358 observations. 

## _Transformation_
Dans la base de données compilée, la variable prix est de type object. Pourtant, nous voulons traiter le prix comme une variable continue et donc de type integer. De manière similaire, pour le nombre de chambres on préfère qu'elle soit integer et pas float car les décimales ne sont très parlantes dans ce type de catégorie.  Finalement, pour la variable bathroom il serait plus intéressant de conserver que le chiffre, donc on a commencé par les transformer.

Lors de la transformation, une difficulté particulière est intervenue : Pour certaines observations le nombre de salles de bain ne contenait pas du chiffre numérique. Par exemple, 169 observations avaient comme nombre de salles de bains "Family only" ou "North facing". Aucun chiffre affiché.

Comme solution (et sachant que nous avons suffisants observations) nous avons effacé les variables pour lesquelles bathroom étaient diffèrent d’un chiffre (ou ne contenait pas de chiffres). Ensuite, nous avons regroupé les observations selon le nombre de salles de bain de la manière suivante : La chiffre, par exemple « 2 », indique la présence de 2 salles de bain dans le logement, qu'ils soient au premier ou au deuxième étage. Donc, on ressemble les catégories : "2 bathrooms", "2 of 3floor", "2 of 4floor", "2 of 5floor" dans une même catégorie : 2. (voir Entrée [9] du notebook).

## _Outliers_
En observant le nombre de salles de bains, nous remarquons qu'il y a des maisons énormes (villas avec +15 salles de bain). On peut considérer ces observations comme des valeurs extrêmes, ce qui nous pose de problèmes lors de l'analyse. Donc, comme ce qui nous intéresse dans ce document est de voir s'il existe des variables (outre la superficie) qui peuvent affecter le prix du loyer dans les villes métropolitaines, il n'est pas important de prendre en compte les maisons de luxe. 
A droite, on observe la distribution de la variable prix avant le traitement et à gauche, la distribution post-traitement.

![]('boxp1.png')
![]('boxp2.png')
  
Bien qu'il y ait encore des valeurs aberrantes, nous ne les supprimerons pas car nous avons décidé que les logements dont le loyer est de 60.000 roupies sont couteux mais pas luxueux. Il peut être aussi intéressant savoir si la relation entre prix et d’autres variables est la même pour les appartements coûteux que pour les bons marchés. 

## _Analyse exploratoire_
En ce qui concerne le nombre de logements disponibles à la location, on identifie Mumbai comme la ville avec le plus de logements, suivie de Bangalore et Delhi (Voir Figure 1. dans la section annexes). Ce résultat n'est pas surprenant car Mumbai c'est la ville la plus peuplée de l'Inde, même si elle n'en est pas la capitale. 
Conformément au résultat ci-dessus, nous constatons que Thane West est la localité où les logements sont plus concentrés. Il est important de noter que Thane West est une ville à part entière, pas vraiment un quartier, mais en tant que voisine de Mumbai, Thane West est parfois considéré comme une banlieue de Mumbai.

Par rapport aux caractéristiques du logement, Il semble qu'il est important que le logement soit au moins semi-meublé, puisque 52% des logements proposés à la location sont de ce type. Pour les salles de bains, nous trouvons que le 80% des observations ont 2 ou 1 salle de bain et seulement le 0.1% de logements ont plus de 4. 
Bien que cela nous amène à penser qu'il s'agit de petits logements, les données montrent que seulement 5,5 % des observations sont des RK. C'est intéressant parce qu'en dépit du surpeuplement, la plupart des logements dans les grandes métropoles indiennes ne sont pas des studios, sont des appartements, contrairement à Tokyo par exemple. 
Comme dernier caractéristique, les résultats montrent que les offres faites par le propriétaire directement ne sont pas populaires. En fait, le 76,4% des logements en locations passent par une agence.
Parmi d’autres résultats intéressants, dans la plupart des villes, l'offre de logements locatifs est principalement constituée d'appartements, à l'exception de Dehli où l'offre la plus importante est constituée de logements indépendants . Pour Pune et Mumbai la deuxième catégorie offerte sont les studios, tandis que les autres villes vont offrir plutôt des logements indépendants ou des maisons à loyer. 
Dans le luxe se démarquent Bangalore et Hyderabad, avec 16 et 13 penthouses respectivement, et Ahmedabad avec 370 villas. Il s'agit précisément de villes situées dans le centre-sud de l'Inde (ou à proximité de la côte dans le cas d’Ahmedabad) entourées de grands espaces verts et sources d’eau. Le tableau ci-dessous contient plus d'informations.
 
Les résultats précédents nous permettent de mieux visualiser le marché immobilier en Inde, notamment du côté de l’offre du logement locatif. Du côté de la demande, cependant, nous pouvons être guidés par la densité de population de chaque ville : Plus la ville est dense plus la demande est forte. 
Il est donc pertinent de se demander, les loyers augmentent-ils en fonction de la densité de la ville ? ou plutôt grâce au mobilier présent dans le logement ? La réponse à cette question nécessite une analyse plus approfondie que les statistiques descriptives, car les résultats observés (voir la figure 2) ne donnent pas de réponse définitive. En fait, dans certaines villes comme Dehli et Mumbai, il existe une relation positive entre la densité et le loyer moyen. Cependant, Kolkata est la preuve du contraire.
De plus, dans certaines villes, les loyers sont moins élevés lorsque le bien est loué directement par le propriétaire. Par exemple, dans le cas des appartements semi-meublés le bail est plus chère avec l’agence dans toutes les villes sauf Bangalore. Cela semble évident, étant donné que les agences facturent l'intermédiation. Toutefois, cela change en fonction de la ville, du type d'hébergement et de l'équipement. Lorsqu'il s'agit de logements meublés ou semi-meublés, les propriétaires ne louent pas toujours moins cher que les agences, cherchant probablement à amortir le risque de détérioration du mobilier. 
Cela est mis en évidence en Ahmedabad et en Dehli (mais pas dans les autres villes), où le loyer minimum pour un studio meublé est de 5.500 et 2.000 roupies respectivement, alors qu'un logement avec les mêmes caractéristiques, peut être payé à 5.000 et 1.200 roupies avec une agence.

## Conclusions
•	A la fois les caractéristiques du logement et la densité de la ville peuvent influencer le prix de la location. 
•	Les caractéristiques du logement influent sur le prix du bail de différentes manières selon la ville.
•	Les logements de type BHK (ou T2 en France) sont plus populaires en Inde, malgré la densité de population des métropoles indiennes.
•	Il n'est pas courant que les propriétaires louent directement.
•	Tout porte à croire que la ville où se trouve le logement exerce une forte influence sur le type de logement offert en location, non seulement en raison de la population, mais aussi de sa localisation dans le pays (sud-côte).
•	Il y a sûrement de caractéristiques des logements qui ne sont pas comprises dans cette base de données et que pourraient expliquer la variabilité des prix entre villes, du type d’équipement privilégié et du type de bailleur. Par exemple, des conditions légales de location de chaque ville. 
•	Des méthodes de Maching Learning pourraient être utilisées avec cette base de données dans le but de répondre à la question initiale.


## Annexes 
Figure 1. Distribution des logements disponibles par ville












Figure 2. Relation Bail et densité par ville

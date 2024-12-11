# DataWarehouse

Schéma en star ou schéma en snowflake

exemple de la table de Data DimDate, qui est une table avec tous les format possibles de la date pour chaque jour de l'année.

Un dataWarehouse va agréger des data qui viennent de plusieurs sources
Côté volumétrie il y a beaucoup de data

Le dataWarehouse permet aussi de maintenir l'historique
Slowly changing dimension, champ qui maintient l'historique (ex: si date de fin est nulle alors c'est l'information qui fait foi)
Dans le DataWareHouse, on a un historique de la donnée qui va être modifiée pour savoir quelle ligne était liée à quel traitement au moment où elle avait son Surrogate Key (côté DWH) et Alternate Key (côté CRM)

- Tables de Faits : Utilisez la distribution hash pour les grandes tables de faits afin d'optimiser les performances des jointures et des agrégations. Choisissez une clé de distribution qui répartit uniformément les données. Plus pour l'état final du traitement.
- Tables de Staging : Utilisez la distribution round-robin pour les tables de staging, car elle permet un chargement rapide et simple des données sans nécessiter de jointures complexes. Plus pour les états intermédiaires.
- Tables de Dimensions : Utilisez la distribution répliquée pour les petites tables de dimension qui sont fréquemment jointes avec les tables de faits. Cela améliore les performances des jointures en évitant les mouvements de données.

Dans le domaine des Data Warehouses, une table de faits (ou simplement un fait) est une table centrale qui contient les mesures quantitatives et les données transactionnelles d'un système d'information. Voici quelques caractéristiques et concepts clés associés aux tables de faits :

Caractéristiques des Tables de Faits
Mesures :

Les tables de faits contiennent des mesures ou des métriques quantitatives qui sont analysées. Par exemple, le montant des ventes, le nombre de produits vendus, etc.
Granularité :

La granularité d'une table de faits fait référence au niveau de détail des données stockées. Par exemple, une table de faits peut enregistrer des ventes par jour, par produit, par magasin, etc.
Clés étrangères :

Les tables de faits contiennent des clés étrangères qui se réfèrent aux tables de dimensions. Ces clés permettent de lier les mesures aux dimensions pertinentes (par exemple, produit, client, date).
Additivité :

Les mesures dans les tables de faits sont souvent additives, ce qui signifie qu'elles peuvent être additionnées pour obtenir des totaux. Par exemple, le montant total des ventes peut être calculé en additionnant les montants des ventes individuels.

Types de Tables de Faits
Table de faits transactionnelle :

Enregistre les transactions individuelles. Par exemple, chaque vente réalisée dans un magasin.
Table de faits périodique :

Enregistre des instantanés périodiques des mesures. Par exemple, l'inventaire des stocks à la fin de chaque mois.
Table de faits cumulative :

Enregistre des mesures cumulatives sur une période de temps. Par exemple, les ventes cumulées depuis le début de l'année.
Exemple de Table de Faits
Supposons que vous ayez un Data Warehouse pour une chaîne de magasins. Une table de faits FactSales pourrait ressembler à ceci :

DateKey	| ProductKey	| StoreKey	| SalesAmount	| QuantitySold
---	| ---	| ---	| ---	| ---	
20230101	| 1001	| 10	| 500	| 5
20230101	| 1002	| 10	| 300	| 3
20230102	| 1001	| 11	| 200	| 2

Relations avec les Tables de Dimensions
Les tables de faits sont liées aux tables de dimensions par des clés étrangères. Par exemple, dans la table FactSales ci-dessus :

DateKey se réfère à la table de dimension DimDate.
ProductKey se réfère à la table de dimension DimProduct.
StoreKey se réfère à la table de dimension DimStore.

Les partitions dans les DW sont donc les mêmes que les partitions de fichiers pour les datalakes, c'est pour optimiser les recherches sur les data et n'existent pas encore physiquement
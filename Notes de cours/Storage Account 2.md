# Création du Storage account

## Basics - Les autres éléments de décision

### Service primaire

Ce qui fait tourner les storage dans Azure sont des blobs.

Il y a 3 type de premium pour Azure blob storage
- file shares
- block blobs (meilleur pour ETL/BDD)
- page blobs (meilleur pour les OS, poru les opérations read/write fréquentes)

### Redondance

- Locally-redundant storage (LRS), data stockée 3 fois dans le même datacenter dans 3 rack différents (différent de la norme on-premise on a RAID10 ou RAID5)
- Zone-redundant storage (ZRS), data stockée 1 fois dans les 3 datacenters de la même région
- Geo-redundant storage (GRS), LRS + LRS dans une autre région de la même zone geographique
- Geo-Zone-redundant storage (GZRS), ZRS + LRS dans une autre région de la même zone geographique

La géoredondance concerne surtout les institutions pour lesquelles c'est obligatoire. Car il s'agit surtout d'un système de disaster recovery.
Il faut donc faire une étude de risque.

Il y a moyen d'avoir les data en **always encrypted**

## Advanced

ABFS utilisée par databricks

Enable storage account key access: activer l'accès aux clé de storage account.

Il faut savoir que les autorisations sont héritées et additives.

Les autorisations sont personnalisables dans IAM. Il y a 3 grands rôles dans Azure

- Owner (un contributor qui peut donner des droits)
- Contributor
- Reader

Azure différencie les rôles administratifs et les rôles opérationnels (par défaut les owners n'ont pas nécessairement un rôle opérationnel)

Un management group peut avoir 6 niveaux d'imbrication maximum. (pas les subscription ni les ressources group)
Azure landing zone: périmètre d'un workload (SAP, etc)
[Azure-landing-zone](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/enterprise-scale/media/azure-landing-zone-architecture-diagram-hub-spoke.svg#lightbox)

- Via EntraID on donne les droits d'accès via IAM
- Via access keys (tous ceux qui ont la clé ont donc la permission)

On peut par exemple utiliser une API pour avoir accès aux données avec une access key (mais donne un accès root au compte de stockage).
Avec les clés SAS (Shared access signature) on peut avoir des clés d'accès permettant d'avoir des permissions supplémentaires.

- droits
- adresses ip
- protocol
- signing key (signées avec les access key)
- durée de validité

Il s'agit plus d'un système utilisé pour les workflow automatisés, notammment utilisés par les applications via les API. Les SAS n'ont pas été créés pour que les admin cloud monitorent un par un les access keys.
Il y a 2 clés access keys pour prévoir les rotations de clés sans que tout le système soit down pendant la regénération de l'une des deux. Ça existe pour la compliance.

Donc IAM pour les personnes et SAS pour l'accès des applications


Hierachical namespace: bascule le blob storage en HDFS


Donc en conclusion il faut faire gaffe à:
Redondance, droit, pricing, activer HDFS

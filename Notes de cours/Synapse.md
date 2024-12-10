# Azure Synapse Analytics

File system name = nom du container

Il est conseillé de découpler, ne pas mettre la base de donnée de sauvegarde dans le même workspace qu'Azure Synapse.
Azure définit l'accès général de Azure Synapse, mais dans Synapse on peut gérer les permissions de manière granulaire. (Comme on peut définir les permissions dans Azure pour modifier une VM mais les droits internes sont gérées depuis l'intérieur de la VM elle-même)

Section Manage:
On peut définir les packages à mettre en disponibilité dans **Workspace package** pour pouvoir lock les packages.
Pareil pour **Data flow libraries** mais pour autre chose (à voir quoi????)

Les sections qui nous importent le plus seront:
DATA
DEVELOP: scripts SQL, KQL, Data flow, job Spark, Notebook
INTEGRATE: où on met nos scripts de pipeline

## 

Il y a 2 types de pool SQL dans ASA, serverless SQL (pour les ETL/adhoc ou de la visualization), dedicated SQL pool (pour faire du Data WareHouse)

Format parquet (ressemble au CSV et inclue un mécanisme de compression permettant de gagner x10 en performance et x100 en stockage)

Le serverless sert principalement donc à faire du adhoc ou de l'ETL pour récupérer des datas depuis un datalake et l'exporter dans un datalake (ou dans un fichier)

Typiquement dans le cas d'un ETL

### Extract: 

select * from openrowset() with() as rows et à la fin de l'affichage, les données ne sont déjà plus retenues

Le format parquet récupère des partitions, il n'y a pas que des fichiers à l'extention .parquet qui est fournie dans la partition.

sql/csv => nettoyage => .parquet

Pour les serverless, il y a une table externe à laquelle qu'il faut fournir

Create external table to store the result

### Transform dans le serverless

CETAS: Create External Tables As Select

## Partition

Les partitions servent à découper les données d'une table et sont considérées comme des tables à part entière. Donc facilite l'indexation.

## Lab Transform

Create sales CSV

```sql
SELECT
    TOP 100 *
FROM
    OPENROWSET(
        BULK 'https://datalakemlo0yvg.dfs.core.windows.net/files/sales/csv/**',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',
        HEADER_ROW = TRUE
    ) AS [result]

```

Create sales DB

```sql
-- Database for sales data
CREATE DATABASE Sales
  COLLATE Latin1_General_100_BIN2_UTF8;
  GO;

  Use Sales;
  GO;

  -- External data is in the Files container in the data lake
  CREATE EXTERNAL DATA SOURCE sales_data WITH (
        LOCATION = 'https://datalakemlo0yvg.dfs.core.windows.net/files/'
      );
      GO;

      -- Format for table files
      CREATE EXTERNAL FILE FORMAT ParquetFormat
          WITH (
                FORMAT_TYPE = PARQUET,
                DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
            );
        GO;
```

Create productSalesTotals table

```sql
USE Sales;
GO;
CREATE EXTERNAL TABLE ProductSalesTotals
    WITH (
        LOCATION = 'sales/productsales/',
        DATA_SOURCE = sales_data,
        FILE_FORMAT = ParquetFormat
    )
AS
SELECT Item AS Product,
       SUM(Quantity) AS ItemsSold,
              ROUND(SUM(UnitPrice) - SUM(TaxAmount), 2) AS NetRevenue
              FROM
                  OPENROWSET(
                        BULK 'sales/csv/*.csv',
                        DATA_SOURCE = 'sales_data',
                        FORMAT = 'CSV',
                        PARSER_VERSION = '2.0',
                        HEADER_ROW = TRUE
                    ) AS orders
                GROUP BY Item;
```

sp_GetYearlySales

```sql
USE Sales;
GO;

CREATE PROCEDURE sp_GetYearlySales
AS
BEGIN
IF EXISTS (
    SELECT * FROM sys.external_tables
    WHERE name = 'YearlySalesTotals'
)
DROP EXTERNAL TABLE YearlySalesTotals

CREATE EXTERNAL TABLE YearlySalesTotals
WITH (
    LOCATION = 'sales/yearlysales/',
    DATA_SOURCE = sales_data,
    FILE_FORMAT = ParquetFormat
)
AS
SELECT YEAR(OrderDate) AS CalendarYear,
    SUM(Quantity) AS ItemsSold,
    ROUND(SUM(UnitPrice) - SUM(TaxAmount), 2) AS NetRevenue
FROM
    OPENROWSET(
    BULK 'sales/csv/*.csv',
    DATA_SOURCE = 'sales_data',
    FORMAT = 'CSV',
    PARSER_VERSION = '2.0',
    HEADER_ROW = TRUE
) AS orders
GROUP BY YEAR(OrderDate)
END

EXEC sp_GetYearlySales;
```

Même malgré un `DROP EXTERNAL TABLE` ça ne suffit pas à supprimer entièrement la table.

Note: côté .parquet on n'a plus besoin de faire le `T` de `ETL` car il est déjà en format transformé.
Il est donc possible de reconstruire la table externe si on possède les fichiers .parquet

Synapse n'accepte pas la création d'une table dans un folder qui a déjà les data parquet. On peut aussi migrer les parquets vers un répertoire temporaire. Supprimer le répertoire, et recopier les fichiers parquet du temp vers le répertoire au bon nom. Comme ça, on n'a pas besoin de recréer l'ETL et on sollicite moins de ressources pour READ/WRITE.
Ça peut aussi être une mesure de sécurité pour ne pas supprimer une table externe par erreur ou en cas de suppression du workspace synapse.

```sql
use sales;
go;
create external table myNewTable (
    with(
        LOCATION = 'sales/productsales/',
        DATA_SOURCE = sales_data,
        FILE_FORMAT = ParquetFormat
    )
) AS
select *
from
(
    OPENROWSET(
        BULK 'https://datalakemlo0yvg.dfs.core.windows.net/files/sales/productsales/**',
        FORMAT = 'PARQUET'
            ) AS orders 
)
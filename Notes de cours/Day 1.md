# Get started with Data engineering on Azure

## data engineering concepts

### Operational and analytical 

Différences dans le workflow

OLTP operational: OnLine Transactional Processing, plus pour les workflow d'application, la PROD, les applications de PROD intéragissent avec la base de PROD. Plus de read/write

OLAP analytical: OnLine Analytical Processing, non production mais qui contient les mêmes données, plus pour l'analyse BI, majoritairement READ. Write pour la transformation

Pour optimiser les transactions, les stockages.
Le système de transaction assure le caractère ACID des données (atomicity, consistency, integrity, durability)

#### Transaction

Permet d'assurer le côté ACID
Typiquement dans un système de base de données
Data, log, temp, backup

Ici la log WAL (Write Ahead Log), rend les transactions possibles. 
WAL:
    0. Lock
    1. Fetch initial Data & copy to temp
        Alice = 10K
        Bob = 50K
    2. Execute commands
        Alice = Alice - montant = 5K
        Bob = Bob + montant = 55K
    3. Write & Compare result with prod data
        Final data:
            A = 5K
            B = 55K
    4. Commit
    5. Unlock

La WAL voit le résutlat attendu et émet une erreur si ce n'est pas conforme
Les transactions ne peuvent pas être parallélisées, dans le cas où il y a une discordance de données.
Les transactions effectuent un LOCK pour empêcher les lignes/colonnes/table etc, en read/write pour s'assurer que les données ne peuvent être modifiées en parallèle

On a donc une différence entre OLTP et OLAP pour éviter d'avoir une concurrence des LOCKS.
On a aussi une optimisation différente pour chaque données.

### Streaming Data

Perpetual, real-time data feeds

Streaming car un système écoute les query en continu et de manière ponctuelle
- Streaming en continu (real time)
- Streaming en batch (exemple banque centrale qui gère les opérations)

Timed series data, les bases de données classiques ont plus de mal à cause de l'indexation de la BDD, le poids des transactions.

### Data pipeline

Ensemble d'activités pour transférer et transformer la data

### Data Lake

Le Data Lake c'est HDFS
ADLS ou Azure Data Lake Storage

#### NOSql

Une base de données de documents, pas de colonnes fixes
- Pour les DocumentDB (MongoDB) au format principalement JSON, API et non pas SQL pour communiquer, Schemaless
- GraphDB (neo4J, Gremlin), composé de noeuds et de liens servant de relations (vertices), modélise mieux les liens (on peut combiner l'usage de SQL et avec une GraphDB), Schemaless
- DistributedDB (cassandra), format table classique, api SQL, Schema-onRead, Schema-onWrite (le schéma est flexible), permet donc de faire de l'horizontal scale, et de distribuer la data sur plusieurs instances. Pour faire celà il faut se défaire du concept ACID (notamment C consistent), et est Eventually Consistent (AeCID?). Tous les noeuds sont des noeuds primaires pas comme dans le SQL
- Stockage objet/Hadoop (Le socle HDFS, hadoop distributed filesystem), framework permettant de manipuler les données du Big Data, permet la scalabilité horizontale (localement), et permet donc de faire de la parallélisation de calcul

"Le meilleur moyen de gérér une table avec 1Md d'entrées et de ne pas gérer une table avec 1Md d'entrées"

##### 1

Le Sharding c'est une base composée de plusieurs BDD. 

##### 2

Big Data (VVV)
- Volume (pas de seuil nécessaire mais plus une manière d'appréhender la problématique du volume)
- Velocity (débit d'ingestion de données)
- Variety (pluralité de données)

Permet de faire de la gestion distribuée de base de données, donc de faire des requêtes distribuées de manière parallélisées.
Paramètre de réplication à fournir à HDFS pour le compromis parallélisation/stockage

### Data Warehouse (DWH)

Elle répond à une problématique, fusionner les data de l'entreprise. C'est une SOURCE DE VÉRITÉ.
C'est une base relationnelle sans l'utilisation de FOREIGN KEY.
C'est donc UNE BASE qui réunit toutes les données et qui optimise les query sur les données hétérogènes. (ERP, RH, CRM par exemple)
Elle nécessite une travail de conception.

Peut garder les historiques

### Apache Spark

HDFS parallélise le stockage (le // est le système de fichier SQL)
HDFS client
Apache Spark parallélise l'exécution (le // est le moteur SQL)

Spark a une API, PySpark, R, Scala va communiquer avec Spark pour mettre en // les calculs

### Lake House


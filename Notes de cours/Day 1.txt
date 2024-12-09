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

### Data Warehouse (DWH)

Elle répond à une problématique, fusionner les data de l'entreprise. C'est une SOURCE DE VÉRITÉ.
C'est une base relationnelle sans l'utilisation de FOREIGN KEY.
C'est donc UNE BASE qui réunit toutes les données et qui optimise les query sur les données hétérogènes. (ERP, RH, CRM par exemple)
Elle nécessite une travail de conception

Peut garder les historiques

### Apache Spark
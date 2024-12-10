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
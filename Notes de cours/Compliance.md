# Compliance

Pour la compliance portal permettait de gérer la compliance dans 365 (word, excel, teams, outlook/exchange), la plateforme est devenue Azure Purview.

On va pouvoir labéliser les data et d'assigner des accès et des autorisations des personnes aux bons label et dans le bon contexte (ordi, appareil etc)

Purview se connecte à la data et wux workspaces (synapse etc) et donne une vue globale de ce qui se passe sur les data.

Purview et Compliance ont fusionnés sur purview.microsoft.com

La facturation de purview est variable (liée à la subscription azure), compliance est flat.

Purview va basiquement détecter les data et les flag en fonction des classifications

On peut le visualiser grâce au datamap

On peut le connecter à 
    Data officer
        data policy => agis pour faire respecter les règles
        data estate insight => access data estate health
    Data owner and consumers
        data sharing => gère le partage avec des tiers par ex
        data catalog => découverte/scan des data


# Streaming

## Introduction to Data streams

Ce sont des data ajoutées du moment pendant lequel les événements ont été enregistrés.
Ce n'est pas une notion de transmission continue mais plutôt la présence d'un daemon/listener

## Azure Stream Analytics

ERP => ETL => ADLS => Azure Stream Analytics => Stockage/Visualisation/Az Functions

IoTHub possède aussi des 
- Add-ons permettant de faire des updates over-the-air (possible de faire de l'asservissement)
- Defender for IoT (côté IoTHub)

Il est possible de définir des fenêtres de temps pour process la data

### tumbling window

Fenetre de taille fixe et qui ne se chevauche pas

```sql
select windowStart, windowEnd, Reading
into [output]
from [input] TIMESTAMP BY EventProcessedUtcTime
group by tumblingWindow(second, 60)
```

### hopping window

Fenetre à intervalle fixe et de taille fixe qui peuvent se chevaucher

### sliding window

Fenetre qui va capturer une durée de temps à partir de la réception d'un événement

### session window

Fenetre de temps variable avec un timeout

### snapshot window

Fenetre à un instant T (pas vraiment une fenetre pour le coup)
latence extremement faible et puissance de calcul très performante

### Stream ingestion scenario

device => eventhub => azure stream analytics => database/blob/powerBI => azure synapse

Il y a possibilité de remplacer le EventHub par Azure Service Bus (un peu plus managé que eventhub et permet aussi de mieux visualiser le système topic/subscription et de voir dans les différentes subscriptions et leurs messages)


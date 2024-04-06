# Scenario

Una scuola vuole monitorare la qualità dell'aria per attuare eventuali azioni di riduzione dell'inquinamento.

## Utenti e dimensionamento

Individuiamo i seguenti utenti del servizio:

- Responsabili qualità dell'aria (personale della scuola, docenti o ATA): circa 6
- Data scientists (docenti e studenti): circa 3

Ipotizziamo inoltre che le stazioni di monitoraggio all'interno della scuola saranno circa 6.

## Dispositivi e casi d'uso

Individuiamo i seguenti dispositivi e casi d'uso:

- i responsabili qualità dell'aria useranno principalmente uno smartphone con una pagina web per la visualizzazione dei dati, collegati ad internet tramite Wi-Fi, della scuola o personale
- i data scientists useranno un computer fisso con delle applicazioni locali (es. programma in Python), possono collegarsi sia dai computer di scuola che di casa

Per quanto riguarda la stazione di monitoraggio, ipotizziamo l'uso di una scheda ESP32 connessa direttamente con i sensori per il monitoraggio ambientale e alla rete scolastica tramite Wi-Fi. Come estensione futura, possiamo pensare ad una connessione LoraWAN, a maggior raggio e minori consumi.

## Architettura di rete

- WebServer
- Application Server (NodeRED)
- DB SQL (MySQL)
- DB NoSQL (MongoDB)



- 



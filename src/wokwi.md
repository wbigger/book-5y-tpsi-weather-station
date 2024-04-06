# Wokwi

Creiamo una stazione di monitoraggio ambientale virtuale con Wokwi ed MQTT:

- accedere a [wokwi.com](https://wokwi.com)
- fare la copia di [questo](https://wokwi.com/projects/322577683855704658) progetto
- cambiare `MQTT_CLIENT_ID` con qualcosa di personale ed unico
- cambiare `MQTT_TOPIC` in `marconi-stazione-2024`
- premere il tasto Play e la nostra stazione è pronta!

Per testare che sta andando tutto bene, andare su [https://www.hivemq.com/demos/websocket-client/](https://www.hivemq.com/demos/websocket-client/), lasciare le impostazioni di default e premere "Connect". Sottoscriversi allo stesso topic del simulatore, quindi provare a cambiare i valori ambientali su wokwi cliccando sul sensore e variando i dati di temperatura ed umidità, dovreste vedere arrivare i messaggi sul vostro client web.

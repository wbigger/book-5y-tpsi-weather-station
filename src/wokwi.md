# Wokwi

Per la simulazione della scheda, useremo Wokwi, che permette una simulazione molto fedele, è semplice da usare e la parte gratuita permette di fare tutto quello che ci serve.

Come detto nello scenario, useremo una [ESP32](https://docs.wokwi.com/guides/esp32); per semplicità la qualità dell'aria viene monitorata con il solo sensore di temperatura ed umidità [DHT22](https://docs.wokwi.com/parts/wokwi-dht22).

L'ESP32 è un ottima scelta per IoT perché permette, in un progetto reale, di criptare facilmente i dati e risparmiare energia con la funzionalità deep sleep.

Creiamo una stazione di monitoraggio ambientale virtuale con Wokwi ed MQTT:

- accedere a [wokwi.com](https://wokwi.com)
- fare la copia di [questo](https://wokwi.com/projects/394399380832847873) progetto
- cambiare `MQTT_CLIENT_ID` con qualcosa di personale ed unico
- cambiare `MQTT_TOPIC` in `marconi-stazione-2024`
- opzionale: cambiare `SENSOR_LOCATION` scegliendo tra "Atrio", "Parcheggio" e "Giardino" 
- premere il tasto Play e la nostra stazione è pronta!

Note sul progetto:

- il protocollo NTP permette di leggere l'ora UTC reale, opzionalmente si potrà in futuro aggiungere anche il fuso orario
- il messaggio varierà sempre perché cambia il timestamp, il controllo per l'aggiornamento serve principalmente per evitare di mandare più volte per errore lo stesso messaggio
- per debug ora l'intervallo di invio è 10 secondi, nel progetto reale si può fare un intervallo molto più ampio (es. 2 ore) e usare la funzionalità deep sleep per ridurre il consumo energetico in questo intervallo

Per testare che sta andando tutto bene, andare su [https://www.hivemq.com/demos/websocket-client/](https://www.hivemq.com/demos/websocket-client/), lasciare le impostazioni di default e premere "Connect". Sottoscriversi allo stesso topic del simulatore, quindi provare a cambiare i valori ambientali su wokwi cliccando sul sensore e variando i dati di temperatura ed umidità, dovreste vedere arrivare i messaggi sul vostro client web.

## Riferimento codice

Metto qui sotto lo stesso codice usato nel progetto copiato sopra, come riferimento.

```python
"""
MicroPython IoT Weather Station Example for Wokwi.com

To view the data:

1. Go to http://www.hivemq.com/demos/websocket-client/
2. Click "Connect"
3. Under Subscriptions, click "Add New Topic Subscription"
4. In the Topic field, type "wokwi-weather" then click "Subscribe"

Now click on the DHT22 sensor in the simulation,
change the temperature/humidity, and you should see
the message appear on the MQTT Broker, in the "Messages" pane.

Copyright (C) 2022, Uri Shaked

https://wokwi.com/arduino/projects/322577683855704658
"""

import network
import time
import ntptime
from machine import Pin
import dht
import ujson
from umqtt.simple import MQTTClient

# MQTT Server Parameters
MQTT_CLIENT_ID = "micropython-weather-capobianco-ac516af1"
MQTT_BROKER    = "broker.mqttdashboard.com"
MQTT_USER      = ""
MQTT_PASSWORD  = ""
MQTT_TOPIC     = "marconi-stazione-2024"

SENSOR_LOCATION = "Giardino"

sensor = dht.DHT22(Pin(15))

print("Connecting to WiFi", end="")
sta_if = network.WLAN(network.STA_IF)
sta_if.active(True)
sta_if.connect('Wokwi-GUEST', '')
while not sta_if.isconnected():
  print(".", end="")
  time.sleep(0.1)
print(" Connected!")

print("Connecting to MQTT server... ", end="")
client = MQTTClient(MQTT_CLIENT_ID, MQTT_BROKER, user=MQTT_USER, password=MQTT_PASSWORD)
client.connect()
print("Connected!")

print("Sync time with NTP... ", end="")
ntptime.settime()
print("Sync!")

prev_weather = ""
while True:
  print("Measuring weather conditions... ", end="")
  sensor.measure() 
  anno, mese, giorno, ora, minuti, secondi, giorno_settimana, anno_settimana = time.localtime()
  message = ujson.dumps({
    "location" : SENSOR_LOCATION,
    "temp": sensor.temperature(),
    "humidity": sensor.humidity(),
    "timestamp": f"{anno}-{mese:02d}-{giorno:02d}T{ora:02d}:{minuti:02d}:{secondi:02d}"
  })

  if message != prev_weather:
    print("Updated!")
    print("Reporting to MQTT topic {}: {}".format(MQTT_TOPIC, message))
    client.publish(MQTT_TOPIC, message)
    prev_weather = message
  else:
    print("No change")
  time.sleep(10)
```

Sempre per riferimento, inserisco anche il `diagram.json`.

```json
{
  "version": 1,
  "author": "Uri Shaked",
  "editor": "wokwi",
  "parts": [
    { "type": "board-esp32-devkit-c-v4", "id": "esp", "top": -38.4, "left": -100.76, "attrs": {} },
    {
      "type": "wokwi-dht22",
      "id": "dht1",
      "top": -38.1,
      "left": 42.6,
      "attrs": { "temperature": "58.2" }
    }
  ],
  "connections": [
    [ "esp:TX", "$serialMonitor:RX", "", [] ],
    [ "esp:RX", "$serialMonitor:TX", "", [] ],
    [ "dht1:VCC", "esp:3V3", "red", [ "v109.3", "h-170.36", "v-200.78" ] ],
    [ "dht1:SDA", "esp:15", "green", [ "v0" ] ],
    [ "dht1:GND", "esp:GND.1", "black", [ "v99.7", "h-189.56", "v-66.38" ] ]
  ],
  "dependencies": {}
}
```
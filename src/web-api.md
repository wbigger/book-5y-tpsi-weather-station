# Web-API

A questo punto siamo pronti per completare il nostro servizio connettendo la pagina web a Node-RED.

Creiamo un nuovo flow che chiamiamo "Web Server" e mettiamo i seguenti nodi:

![Web-server](./img/web-server.png)

Nel primo nodo (HTTP in), impostiamo come URL "quality".

Nel secondo nodo, il template, copiamo incolliamo il codice da codepen, aggiungendo il tag `<style>` dentro l'head ed il tag `<script>` subito prima della fine del body, il risultato sarà qualcosa del genere.

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Qualità aria</title>
        <style>
body {
  margin: 0;
}

nav {
  background-color: #2b57b6;
  color: white;
  padding: 0.3rem;
}

#sensor-list {
  display: flex;
  flex-direction: column;
  background-color: #f5f1ed;
}

.sensor {
  display: flex;
  flex-direction: row;
  flex-grow: 1;
}

.sensor > div {
  flex-grow: 1;
  flex-basis: 0px;
}

.good {
  background-color: orange;
}

.bad {
  background-color: red;
}

.optimal {
  background-color: green;
}

.good::after {
  content: "Buona";
}

.bad::after {
  content: "Scadente";
}

.optimal::after {
  content: "Ottima";
}

        </style>
    </head>
    <body>
        <nav>Marconi qualità aria</nav>

<div id="sensor-list">

</div>
<script>

fetch("/quality.json")
  .then((response)=>response.json())
  .then((json)=> {
  console.log(json);
  let sensorList = document.getElementById("sensor-list");
  json.forEach((sensor) => {
    sensorList.innerHTML += `<div class="sensor" data-id="${sensor.id}">
    <div class="sensor-location">${sensor.location}</div>
    <div class="sensor-quality ${sensor.quality}"></div>
  </div>`;
  });
});

</script>
    </body>
</html>
```

Attenzione: abbiamo cambiato l'URL della fetch per prendere un JSON in locale, prima di poter verificare che la pagina funzioni dobbiamo aggiungere questo URL, che in termine tecnico viene anche detto "endpoint"

## Data endpoint

Creiamo un nuovo flow e chiamiamolo "Endpoint". Qui metteremo i dati letti dal database MongoDB. Prima però di fare la connessione al DB, creiamo un endpoint statico di test per verificare che tutto funzioni.

### Endpoint statico (di test)

Aggiungiamo nuovamente i tre nodi "html in", "template" e "html response". Facciamo le seguenti modifiche:

- in "html in", inserire come URL `/quality.json`
- in "template", modificare il "Syntax Highlight" e selezionare "JSON"
- copiare il json che abbiamo già usato [qui](/web.html#rendere-la-pagina-dinamica) e inserirlo nella casella di testo
- modificare "Output as" e selezionare "Parsed JSON"

![Endpoint](./img/endpoint-0.png)

A questo punto possiamo provare se la pagina funziona andando alla pagina `http://<nodered-ip>:1880/quality`

### Endpoint reale

Per fare la richiesta al DB, usiamo la sequenza di nodi "inject" -> "function" -> "mongodb in" -> "Debug", con le seguenti configurazioni:

- in inject, cancelliamo tutti i campi (payload e topic)
- in function, diamo il nome "Find" ed inseriamo il seguente codice:

```js
msg.payload = {"timestamp": {"$exists": true}}
msg.sort = {
  timestamp: -1
}
msg.limit = 10;

return msg;
```

In questo modo troviamo solo i record che hanno un timestamp, li ordiniamo dal più recente e limitiamo la ricerca a 10

- in mongodb in, selezionare il server che avevamo già creato precedentemente, come collezione inserire "sensori", come operazione "find" e come nome "MongoDB Atlas"
- in debug, cambiamo nome e lo chiamiamo "mongodb find"

![Endpoint](./img/endpoint-1.png)
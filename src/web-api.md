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

fetch("https://gist.githubusercontent.com/<username>/<hash>/quality.json")
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

Possiamo subito provare se tutto funziona andando alla pagina `http://<nodered-ip>:1880/quality`




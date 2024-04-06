# Installazione Node-RED

Continuando il lavoro fatto nelle scorse settimane, installeremo un'istanza di Node-RED su una macchina EC2 di AWS. Seguiremo [questa](https://nodered.org/docs/getting-started/aws#running-on-elastic-beanstalk-with-high-availability) guida.

Lanciare una macchina EC2 come segue:

- Ubuntu Server
- t2.micro
- aprire la porta 1880 e per utilità anche All ICMP
- come chiave sempre `vockey.pem`

Installare Node-RED come segue (attenzione, rispetto alla guida siamo passati alla versione 21):

```sh
curl -sL https://deb.nodesource.com/setup_21.x | sudo -E bash -
sudo apt-get install -y nodejs build-essential
sudo npm install -g --unsafe-perm node-red
```

Installare inoltre i nodi che ci serviranno per MongoDB:

```sh
cd .node-red/
npm install node-red-node-mongodb
```

Infine avviare Node-RED:

```sh
npm install node-red-node-mongodb
```

Adesso è possibile accedere alla propria istanza con andando su `http://<your-instance-ip>:1880/`.

# Personalització i Desenvolupament de Scripts

En aquesta secció ajustarem la interfície d'usuari de Zabbix per millorar la revisió dels logs i alertes d'una manera més rapida, comoda i visual.

---

## Personalització de la interfície d'usuari

El primer que he fet al començar el projecte ha estat canviar la interfície al mode fosc. Això va bastant a gust de cadascú, però en la meva opinió personal fa que les alertes, gràfiques i elements de colors siguin més visibles. A més, també evita tenir la molèstia d’una pantalla molt brillant tota l’estona, sobretot quan estem revisant problemes o dades durant una bona estona. D’aquesta manera la monitorització es fa més còmoda i visualment més clara.

També he revisat l’idioma de la GUI, ja que s'ha plantejat canviar-lo a espanyol, però resulta més confús i és més fàcil trobar documentació en angles.

Un altre punt important ha estat configurar correctament la zona horària com `Europe/Madrid`, això és necessari perquè les alertes, gràfiques, esdeveniments i logs quedin registrats amb l’hora correcta. Si la zona horària no coincideix, després és molt més difícil relacionar una alerta de Zabbix amb un problema real que hagi passat a la xarxa.

![GUI config 1](<../imatges/05/1- GUI (01).png>)

També s'han deixat els hosts amb noms clars i fàcils d’identificar, per exemple, el NAS queda identificat com `NAS-Hefesto-1`, el servidor principal com a `Zabbix server` i els 3 nodes Proxmox amb la IP compartida com a un unic host anomenat `ClusterHefesto`.


![GUI config 2](<../imatges/05/1- GUI (02).png>)

---

## Desenvolupament de scripts personalitzats



---

## Integració de plugins addicionals


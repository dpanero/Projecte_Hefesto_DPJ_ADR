# Disseny de Polítiques de Monitorització

En aquest apartat configurarem les polítiques de monitorització dels diferents hosts en funció del dispositiu que són, ho treballarem a través del que ens han generat cada plantilla de les que em fet servir.
Anirem revisant un a un els 4 hosts que em creat al apartat anterior, el PfSense i Suricata els rebem a través de rsyslog pel host Zabbix i per tant ja son items d'aquest.

Primerament s'han establert els grups de hosts per tal de tenir separats els diferents tipus de dispositiu, en aquest cas només tenim un host de cada tipus.

![Host Groups](<../imatges/04/1- hostgroups.png>)

També hem activat el host discover per tal de fer autodescubriment de les maquines de la LAN, indiquem el rang de xarxa i un temps d'autodescubriment.

![Discover 1](<../imatges/04/2- discover (1).png>)

Ja tindriem la regla generada i aplicada.

![Discover 2](<../imatges/04/2- discover (2).png>)

---

## Cluster Proxmox

Lo primer que m'he trobat al cluster es que detecta com problemes que les plantilles o maquines virtuals estiguin apagades lo qual tampoc es correcte sempre, modifiquem els triggers (alertes) per tal de fer que només doni com informació el fet de que estan parades i els reinicis pero sense donar alerta greu, les greus només saltaran si algún dels nodes cau, després mostrarem com fem un dashboard visual per mostrar la informació comodament.

![Trigger Proxmox 1](<../imatges/04/3- triggers proxmox (1).png>)
![Trigger Proxmox 2](<../imatges/04/3- triggers proxmox (2).png>)

Ens assegurem que dels nodes si que reporti triggers si no estan actius, es important que només tinguem triggers importants ja que si rebem moltes alertes innecesaries perdrem control sobre aquestes.

![Trigger Proxmox 3](<../imatges/04/3- triggers proxmox (3).png>)

Ara podem veure com només rebem informacions de que s'han reiniciat maquines virtuals recentment pero son informatives i que podem acceptar o apagar més endavant en cas que ens molesti molt, de moment com que reiniciar VMs no es una cosa frequent esta bé rebre un missatge al respecte.

![Trigger Proxmox 4](<../imatges/04/3- triggers proxmox (4).png>)

---

## NAS

En el cas de les politiques per als dispositius NAS em pensat que ens interesaria saber del nostre NAS, de primeres apagarem els triggers de temperatura ja que al ser un NAS virtual rebem informació erronea.

![Trigger NAS 1](<../imatges/04/4- triggers nas (1).png>)


Depsrés d'aixó em deixat una alerta per que ens avissi si va malament de CPU ja que va una mica just en algunes ocasions.

![Trigger NAS 2](<../imatges/04/4- triggers nas (2).png>)

També verifiquem que els items de la template reben dades de la pool de discs i l'espai total d'aquesta, al tenir també el pool local del proxmox també el mostra pero principalment voldrem monitoritzar l'altre que es el que conté les VMs.

![Trigger NAS 3](<../imatges/04/4- triggers nas (3).png>)

Finalment confirmem que rebem les alertes, en aquest cas el llindar de memoria que em deixat actiu mostra que el NAS ha estat a més del 90% durant més de 5 minuts, aixó ens genera un avis greu.

![Trigger NAS 4](<../imatges/04/4- triggers nas (4).png>)

---

## Servidor WEB

Com em mostrat al apartat de `instal·lació / configutació` s'ha afegit un host per monitoritzar el servidor web. 
- [Clica aquí per veure la instal·lació](https://github.com/dpanero/Projecte_Hefesto_DPJ_ADR/blob/main/05_Monitoritzaci%C3%B3_de_xarxes_Zabbix/docs/03_instal_lacio_i_configuracio_basica.md#servidor-web)
Aquest l'administrem a través d'un script personalitzat. 
- [Clica aquí per veure la explicació del script](https://github.com/dpanero/Projecte_Hefesto_DPJ_ADR/blob/main/05_Monitoritzaci%C3%B3_de_xarxes_Zabbix/docs/05_personalitzacio_i_desenvolupament_de_scripts.md#desenvolupament-de-scripts-personalitzats)

### Plantilla personalitzada

El primer que fare sera crear una plantilla personalitzada per donar-li al servidor web. 
Per fer-ho vaig a `Data collection > Templates > Create template`.

![Trigger WEB 1](<../imatges/04/5- tweb (1).png>)

### Items

Ara dins de la plantilla creo els items necessaris per cada key de les que estem recollint amb l'script mostrat al enllaç anterior.

Aquí hem creat l’ítem `Hefesto - Apache actiu`, que utilitza la key `hefesto.web.apache`. Aquesta comprovació serveix per saber si el servei Apache està funcionant correctament al servidor web. El valor esperat és `1` quan Apache està actiu i `0` quan el servei està aturat. Aquesta key és important perquè Apache és el servei principal que permet servir la web.

![Trigger WEB 2](<../imatges/04/5- tweb (2).png>)

Aquí hem creat l’ítem `Hefesto - Codi HTTP local`, que utilitza la key `hefesto.web.http_code`. Aquesta comprovació fa una petició local a la web i retorna el codi HTTP obtingut. Si tot funciona correctament, el valor normal hauria de ser `200`. Si retorna un codi `4xx` o `5xx`, ens indicaria que la web no està responent com toca.

![Trigger WEB 3](<../imatges/04/5- tweb (3).png>)


Aquí hem creat l’ítem `Hefesto - Config Apache correcta`, que utilitza la key `hefesto.web.configtest`. Aquesta comprovació executa una validació de la configuració d’Apache per saber si els fitxers de configuració són correctes. Retorna `1` si la configuració és vàlida i `0` si hi ha algun error. Això és útil perquè un error de configuració podria impedir reiniciar o carregar Apache correctament.

![Trigger WEB 4](<../imatges/04/5- tweb (4).png>)


Aquí hem creat l’ítem `Hefesto - Errors Apache recents`, que utilitza la key `hefesto.web.errors`. Aquesta comprovació revisa els últims registres del fitxer d’errors d’Apache i compta possibles errors recents. Amb això podem detectar si la web està generant problemes encara que el servei continuï funcionant.

![Trigger WEB 5](<../imatges/04/5- tweb (5).png>)


Aquí hem creat l’ítem `Hefesto - Processos Apache`, que utilitza la key `hefesto.web.processes`. Aquesta comprovació compta quants processos d’Apache hi ha en execució. Ens serveix per veure si Apache està realment treballant i també per detectar situacions estranyes, com que no hi hagi processos o que n’hi hagi massa.

![Trigger WEB 6](<../imatges/04/5- tweb (6).png>)


Aquí hem creat l’ítem `Hefesto - Respostes HTTP 5xx`, que utilitza la key `hefesto.web.5xx`. Aquesta comprovació revisa el log d’accés d’Apache i compta les respostes de tipus `5xx`, que normalment indiquen errors del servidor. És una mètrica útil perquè pot detectar errors interns encara que la màquina segueixi encesa i Apache continuï actiu.

![Trigger WEB 7](<../imatges/04/5- tweb (7).png>)


Aquí hem creat l’ítem `Hefesto - Ús disc /var/www`, que utilitza la key `hefesto.web.disk_www`. Aquesta comprovació mira el percentatge d’ús del disc on es troba el directori `/var/www`. És important controlar aquest punt perquè si el disc s’omple, la web podria deixar de guardar fitxers, logs o funcionar correctament.

![Trigger WEB 8](<../imatges/04/5- tweb (8).png>)

Aquí hem creat l’ítem `Hefesto - Health Score Web`, que utilitza la key `hefesto.web.score`. Aquesta comprovació calcula una puntuació general de salut del servidor web a partir de diferents valors del script, com l’estat d’Apache, el codi HTTP, la configuració del servei, errors recents, respostes `5xx` i ús del disc web. El resultat és un valor numèric, on una puntuació alta indica que el servei està funcionant correctament i una puntuació baixa ens avisa que hi ha algun problema a revisar.

![Trigger WEB 9](<../imatges/04/5- tweb (9).png>)

Aquí hem creat l’ítem `Hefesto - Resum Web`, que utilitza la key `hefesto.web.summary`. Aquest ítem retorna un resum en format text amb l’estat general del servidor web. Ens serveix per veure ràpidament diferents valors del script en una sola línia, com si Apache està actiu, quin codi HTTP retorna la web, si la configuració és correcta, quants errors recents hi ha i la puntuació general del servidor. Aquest ítem no està pensat tant per generar alertes directes, sinó per tenir una vista ràpida i més llegible de l’estat del servei.

![Trigger WEB 10](<../imatges/04/5- tweb (10).png>)

Aquí podem veure ja els 9 items actius:

![Trigger WEB 11](<../imatges/04/5- tweb (11).png>)

Ara vinculem aquesta plantilla al host del servidor web que hem creat.

![Trigger WEB 12](<../imatges/04/5- tweb (12).png>)

### Triggers

Ara el següent pas es configurar els triggers de la plantilla per rebre diferents alertes.
Per fer-ho dins de la plantilla anem a `Triggers > Create trigger`.

Aquí hem creat el trigger `Hefesto Web: Apache no està actiu`. Aquest trigger comprova si el servei Apache està funcionant al servidor web. La key `hefesto.web.apache` retorna `1` quan Apache està actiu i `0` quan està aturat.

L’expressió utilitzada és `last(/web/hefesto.web.apache)=0`.

Això vol dir que si l’últim valor rebut és `0`, Zabbix interpreta que Apache no està en marxa i genera una alerta de severitat `High`, ja que si Apache cau, la web deixa de funcionar.

![Trigger Apache no està actiu](<../imatges/04/5- tweb (13).png>)

Aquí hem creat el trigger `Hefesto Web: La web no respon correctament`. Aquest trigger revisa el codi HTTP que retorna la web local. En una situació normal, la web hauria de retornar un codi `200`, indicant que respon correctament.

L’expressió utilitzada és `last(/web/hefesto.web.http_code)<200 or last(/web/hefesto.web.http_code)>=400`.

Amb aquesta expressió indiquem que si el codi HTTP és inferior a `200` o és igual o superior a `400`, Zabbix generarà una alerta. Això permet detectar errors com pàgines no trobades, errors interns del servidor o problemes de resposta de la web.

![Trigger web no respon correctament](<../imatges/04/5- tweb (14).png>)


Aquí hem creat el trigger `Hefesto Web: La configuració d'Apache no és correcta`. Aquest trigger utilitza la key `hefesto.web.configtest`, que comprova si la configuració d’Apache és vàlida.

L’expressió utilitzada és `last(/web/hefesto.web.configtest)=0`.

Si el valor és `0`, significa que la validació de configuració d’Apache ha fallat. Això no sempre vol dir que la web hagi caigut en aquell moment, però sí que pot impedir reiniciar Apache correctament o aplicar canvis nous. Per això l’hem deixat amb severitat `Average`.

![Trigger configuració Apache incorrecta](<../imatges/04/5- tweb (15).png>)


Aquí hem creat el trigger `Hefesto Web: Health score baix`. Aquest trigger revisa la puntuació general de salut del servidor web. Aquesta puntuació surt del nostre script personalitzat i resumeix diferents comprovacions, com l’estat d’Apache, el codi HTTP, la configuració, errors recents i ús del disc.

L’expressió utilitzada és `last(/web/hefesto.web.score)<70`.

Amb aquesta condició, si la puntuació baixa de `70`, Zabbix genera una alerta. Aquest trigger és útil perquè no depèn d’un únic valor, sinó que ens dona una visió general de si el servei web està començant a tenir problemes.

![Trigger health score baix](<../imatges/04/5- tweb (16).png>)

Aquí hem creat el trigger `Hefesto Web: Massa errors HTTP 5xx`. Aquest trigger comprova si la web està generant massa errors de servidor. Els codis `5xx` normalment indiquen errors interns, problemes amb Apache, amb el backend o amb alguna part del servei web.

L’expressió utilitzada és `last(/web/hefesto.web.5xx)>5`.

Això vol dir que si el nombre d’errors `5xx` recents és superior a `5`, Zabbix generarà una alerta de tipus `Warning`. No ho hem posat com a crític directament perquè pot ser un error puntual, però sí que és una senyal que s’ha de revisar.

![Trigger massa errors HTTP 5xx](<../imatges/04/5- tweb (17).png>)


Aquí hem creat el trigger `Hefesto Web: Ús alt del directori web`. Aquest trigger controla l’ús del disc on es troba el directori `/var/www`, que és on normalment es guarden els fitxers de la web.

L’expressió utilitzada és `last(/web/hefesto.web.disk_www)>80`.

Amb aquesta expressió, si l’ús del directori web supera el `80%`, Zabbix genera una alerta de severitat `Warning`. Això ens permet detectar amb temps si el servidor web comença a quedar-se sense espai abans que arribi a un punt crític.

![Trigger ús alt del directori web](<../imatges/04/5- tweb (18).png>)

Aquest es el resultat amb tots els triggers creats:

![Trigger ús alt del directori web](<../imatges/04/5- tweb (19).png>)

Ara ja puc crear els dashboards | [Clica per veure la creació dels dashboards]()

`Comprovacions al apartat final de la documentació`

---

## Servidor Zabbix

Aquest no cal canviar res ja que ve configurat correctament de manera automatica al instal·lar-se.

El canvi que nosaltres em fet es la configuració dels logs pfsense que em mostrat anteriorment. [Clica aquí per anar a l'explicació](https://github.com/dpanero/Projecte_Hefesto_DPJ_ADR/blob/main/05_Monitoritzaci%C3%B3_de_xarxes_Zabbix/docs/03_instal_lacio_i_configuracio_basica.md#pfsense-i-suricata)

![Rsyslog 6.2](<../imatges/03/4- rsyslog (6).png>)
![Rsyslog 8.2](<../imatges/03/4- rsyslog (8).png>)


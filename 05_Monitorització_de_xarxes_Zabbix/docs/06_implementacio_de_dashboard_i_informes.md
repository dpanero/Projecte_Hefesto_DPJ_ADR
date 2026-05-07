# Implementació de Dashboard i Informes

En aquest apartat es mostra com hem utilitzat els dashboards de Zabbix per centralitzar la informació més important de la infraestructura Hefesto en una sola vista. L’objectiu no és només veure dades soltes, sinó tenir una pantalla general que permeti entendre ràpidament l’estat dels serveis, detectar problemes i comprovar si els elements principals del projecte funcionen correctament.

El dashboard creat reuneix informació de diferents parts de l’entorn: l’estat general del servidor Zabbix, els problemes actius, els avisos recents, el clúster de Proxmox, l’ús de CPU i RAM dels nodes, l’estat del NAS TrueNAS i també els logs rebuts de Suricata i pfSense. D’aquesta manera podem veure en temps real tant la part de monitorització de sistemes com la part de seguretat i xarxa.

A més, aquesta vista ens ajuda a relacionar incidències entre diferents serveis. Per exemple, si apareix un problema en una màquina virtual de Proxmox, podem revisar al mateix temps l’estat del node, el consum de recursos, els logs del firewall o les alertes de Suricata. Això facilita molt la diagnosi, ja que no cal anar revisant cada host o servei per separat.

També s’ha explorat la part d’informes de Zabbix, que permet generar resums periòdics sobre l’estat de la infraestructura. Aquests informes poden servir per revisar l’evolució del sistema, comprovar si hi ha problemes repetits, veure la disponibilitat dels equips i tenir una visió més clara del funcionament general del projecte al llarg del temps.

Per tant, aquest apartat compleix la funció de convertir totes les dades recollides per Zabbix en informació útil i visual. Els dashboards permeten una supervisió ràpida del sistema en temps real, mentre que els informes periòdics ajuden a fer un seguiment més ordenat i a documentar l’estat de la infraestructura Hefesto.

---

## Dashboards per al Servidor Zabbix

Primerament tenim els dashboards predeterminats que ens dona zabbix els cuals mostren totes les alertes que Zabbix esta rebent en aquell moment, em deixat que refresqui el contingut cada minut.

![dahsboards 1.1](<../imatges/06/dashboards (1.1).png>)
![dahsboards 1.2](<../imatges/06/dashboards (1.2).png>)

Resultat:

![dahsboards 12](<../imatges/06/dashboards (12).png>)

També tenim un widget que ens retorna l'estat del Zabbix en funció a l'us de CPU d'aquest.

![dahsboards 2](<../imatges/06/dashboards (2).png>)

Resultat:

![dahsboards 13](<../imatges/06/dashboards (13).png>)

---

## Dashboards per al Cluster Proxmox

El primer widget que em creat al dashboard per al proxmox és el més important ja que em configurat de manera que depenent de si el status del node en concret és 1 o 0 ens diura si esta o no Online, fem servir el tipus "Top Host" per poder fer servir una columna per cada node.
Graciés al keepalive que em configurat abans, caigui el node que caigui sempre n'hi haura un dels 3 que reporti l'estat dels altres 2.

![dahsboards 3](<../imatges/06/dashboards (3).png>)

Resultat:

![dahsboards 14](<../imatges/06/dashboards (14).png>)

També em generat un parell més de widgets com abans pero referents a altres informacions que ens dona la plantilla de proxmox que em agregat a aquest host, el primer de tot es l'ús de CPU que esta fent servir cada node, aixó ho podem fer gracies a que tenim afegit el cluster per una unica IP i la plantilla ens dona la informació separada per nodes.

![dahsboards 4](<../imatges/06/dashboards (4).png>)

Resultat:

![dahsboards 15](<../imatges/06/dashboards (15).png>)

El mateix que abans pero per saber la RAM que fa servir cada node.

![dahsboards 5](<../imatges/06/dashboards (5).png>)

Resultat:

![dahsboards 16](<../imatges/06/dashboards (16).png>)

Ara per poder donar valor a la dada anterior em ficat un altre widget amb el total de RAM que cada node té en total

![dahsboards 6](<../imatges/06/dashboards (6).png>)

Resultat:

![dahsboards 17](<../imatges/06/dashboards (17).png>)

---

## Dashboards per al NAS

El primer widget que creem es per saber l'estat del NAS de igual manera que em fet amb Proxmox i en el mateix widget em afegit el total del espai de la pool de les maquines virtuals `poolvms` i el que ja tenim actualment en ús.

![dahsboards 7](<../imatges/06/dashboards (7).png>)

Resultat:

![dahsboards 18](<../imatges/06/dashboards (18).png>)

Ara tal com s'ha explicat amb anterioritat em generat 2 widgets de tipus `Plain text` un per mostrar els logs que rebem de Suricata i un altre per a tots els que genera PfSense.

- Logs Suricata:

![dahsboards 8](<../imatges/06/dashboards (8).png>)

Resultat:

![dahsboards 19](<../imatges/06/dashboards (19).png>)

- Logs PfSense

![dahsboards 9](<../imatges/06/dashboards (9).png>)

Resultat:

![dahsboards 20](<../imatges/06/dashboards (20).png>)

---

## Dashboards per la WEB de la DMZ

Per fer els widgets de la web aprofitarem els items que em creat amb les keys que enviem desde el script mostrat anteriorment.

El primer de tot es aprofitar l'item que em creat com `Hefesto - Apache actiu` per mostrar si esta o no encés en funció de si el script retorna 1 o 2 tal com em fet a proxmox pero en aquest cas de manera "manual".

![dahsboards 23](<../imatges/06/dashboards (23).png>)

Resultat:

![dahsboards 24](<../imatges/06/dashboards (24).png>)

Ara creem un `Item Value` per recollir la salut de la web que em processat desde el script per tenir-la visible en pantalla.

![dahsboards 25](<../imatges/06/dashboards (25).png>)

Resultat:

![dahsboards 26](<../imatges/06/dashboards (26).png>)

Ara de igual manera un altre `Item Value` per veure el % d'ús d'apache sobre el directori `/var/www`.

![dahsboards 27](<../imatges/06/dashboards (27).png>)

Resultat:

![dahsboards 28](<../imatges/06/dashboards (28).png>)

Finalment fem un de tipus `Plain text` per tal de mostrar tot el resum de dades que recollim de la web graciés al item que em creat anomenat `Hefesto - Resum Web` es mostrara de igual manera que em fet amb Suricata i PfSense.

![dahsboards 29](<../imatges/06/dashboards (29).png>)

Resultat:

![dahsboards 30](<../imatges/06/dashboards (30).png>)

Per acabar vull afegir 2 opcions interesants integrades amb el zabbix que són en primer lloc la ubicació geografica dels equips i la hora actual del servidor Zabbix.
Geolocalització maquines:

![dahsboards 10](<../imatges/06/dashboards (10).png>)

Resultat:

![dahsboards 21](<../imatges/06/dashboards (21).png>)

Hora del servidor Zabbix:

![dahsboards 11](<../imatges/06/dashboards (11).png>)

Resultat:

![dahsboards 22](<../imatges/06/dashboards (22).png>)

---

## Resultat del dashboard amb tots els widgets afegits

![dahsboards 31](<../imatges/06/dashboards (31).png>)
![dahsboards 32](<../imatges/06/dashboards (32).png>)

---

## Configuració d'Informes periòdics

En aquest apartat hem configurat la part d’informes de Zabbix per poder generar resums periòdics de l’estat de la infraestructura Hefesto. La idea no és només mirar les dades en temps real, sinó poder tenir un informe automàtic que resumeixi l’estat general del sistema durant un període concret.

Per fer-ho, primer hem creat un dashboard principal amb la informació més important del projecte: problemes actius, estat dels hosts, servidor web, NAS, Proxmox i mètriques principals. A partir d’aquest dashboard, Zabbix pot generar un informe en PDF i enviar-lo per correu electrònic de forma programada.

Per activar aquesta funcionalitat hem instal·lat el component `zabbix-web-service`, necessari per generar els informes. També hem configurat el servidor Zabbix amb els paràmetres `WebServiceURL` i `StartReportWriters`, que permeten que el servidor es comuniqui amb el servei encarregat de generar els reports.

També hem configurat la `Frontend URL`, indicant l’adreça completa de la interfície web de Zabbix. Aquest pas és important perquè el servei de reports pugui accedir al dashboard i convertir-lo en informe.

Finalment, hem creat un informe periòdic anomenat `Informe setmanal Hefesto`, configurat per generar-se setmanalment i enviar un resum de l’estat de monitorització. Amb això podem revisar l’evolució general del sistema sense haver d’entrar manualment cada vegada a totes les pantalles de Zabbix.



---

## Exploració de les capacitats de generació d'informes de l'eina


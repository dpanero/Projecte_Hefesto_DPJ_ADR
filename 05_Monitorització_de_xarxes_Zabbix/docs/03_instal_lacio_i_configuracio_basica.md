## Instal·lació i Configuració Bàsica de Zabbix

En aquesta part documentem la instal·lació inicial de Zabbix dins del projecte Hefesto i la integració dels primers dispositius importants de la infraestructura. El servidor Zabbix s’ha desplegat sobre una màquina Debian 12 situada a la xarxa LAN amb la IP `172.16.0.5`. Aquesta màquina serà el punt central des d’on revisarem l’estat dels servidors, serveis, dispositius de xarxa, NAS i nodes Proxmox.

Primerament instal·lem el sistema base de Debian 12 sobre la màquina virtual que farà de servidor Zabbix. Aquesta VM queda desplegada dins de Proxmox i serà la base sobre la qual instal·larem tots els components de monitorització.

![Zabbix 1](<../imatges/03/1- zabbix (1).png>)

Després configurem la IP estàtica del servidor Zabbix. En aquest cas deixem la interfície `ens18` amb la IP `172.16.0.5`, màscara `255.255.255.0`, porta d’enllaç `172.16.0.1` i DNS `172.16.0.1` i `8.8.8.8`. Això és important perquè el servidor de monitorització sempre tingui una adreça fixa dins la LAN.

![Zabbix 2](<../imatges/03/1- zabbix (2).png>)

Un cop tenim la xarxa preparada, fem un `apt update` per actualitzar la llista de repositoris del sistema. Així ens assegurem que Debian pot trobar els paquets necessaris i que el repositori de Zabbix quedarà carregat correctament.

![Zabbix 3](<../imatges/03/1- zabbix (3).png>)

Aquí descarreguem el paquet oficial del repositori de Zabbix per a Debian 12. Primer es veu un intent sense permisos suficients i després ja s’instal·la correctament amb `sudo dpkg -i`. Amb això afegim el repositori oficial de Zabbix al sistema.

![Zabbix 4](<../imatges/03/1- zabbix (4).png>)

Instal·lem els paquets principals que necessita Zabbix per funcionar. En aquesta ordre s’instal·la el servidor Zabbix amb suport MySQL/MariaDB, la interfície web en PHP, la configuració d’Apache, els scripts SQL, l’agent de Zabbix i MariaDB com a base de dades.

![Zabbix 5](<../imatges/03/1- zabbix (5).png>)

Entrem a MariaDB i creem la base de dades `zabbix`. També creem l’usuari `zabbix@localhost`, que serà el que utilitzarà el servidor Zabbix per connectar-se amb la base de dades. D’aquesta manera no fem servir l’usuari administrador per al funcionament normal del servei.

![Zabbix 6](<../imatges/03/1- zabbix (6).png>)

Donem permisos a l’usuari `zabbix` sobre la seva base de dades i activem temporalment el paràmetre `log_bin_trust_function_creators`. Aquest ajust és necessari per poder importar correctament l’esquema inicial de Zabbix a MariaDB.

![Zabbix 7](<../imatges/03/1- zabbix (7).png>)

Importem l’estructura inicial de la base de dades utilitzant el fitxer SQL comprimit que ve amb el paquet `zabbix-sql-scripts`. Aquest pas crea les taules internes que Zabbix necessita per guardar hosts, mètriques, alertes, usuaris, esdeveniments i configuracions.

![Zabbix 8](<../imatges/03/1- zabbix (8).png>)

Obrim el fitxer principal de configuració del servidor Zabbix, ubicat a `/etc/zabbix/zabbix_server.conf`. En aquest fitxer indicarem les dades de connexió amb MariaDB perquè el servidor pugui treballar amb la base de dades que acabem de preparar.

![Zabbix 9](<../imatges/03/1- zabbix (9).png>)

Configurem els camps principals de connexió amb la base de dades. Definim `DBName=zabbix`, `DBUser=zabbix` i la contrasenya corresponent. Aquest pas és necessari perquè el servei `zabbix-server` pugui iniciar sense errors de connexió.

![Zabbix 10](<../imatges/03/1- zabbix (10).png>)

Revisem de nou la configuració per comprovar que els paràmetres principals han quedat guardats correctament. Abans d’arrencar els serveis és important validar que el nom de la base de dades, l’usuari i la contrasenya coincideixen amb el que hem creat a MariaDB.

![Zabbix 11](<../imatges/03/1- zabbix (11).png>)

També revisem la configuració d’Apache per a Zabbix. Aquí es defineix l’àlies `/zabbix`, que permet accedir a la interfície web des del navegador, i es comproven valors de PHP com la memòria, el temps d’execució i la zona horària `Europe/Madrid`.

![Zabbix 12](<../imatges/03/1- zabbix (12).png>)

Accedim al navegador i obrim l’assistent web de Zabbix des de `172.16.0.5/zabbix/setup.php`. En aquest primer pas seleccionem l’idioma i comencem la configuració final des de la interfície web.

![Zabbix 13](<../imatges/03/1- zabbix (13).png>)

L’assistent comprova els requisits previs de Zabbix. Es valida la versió de PHP, la memòria, la mida màxima de pujada, el temps d’execució, el suport de MySQL i altres mòduls necessaris. Com tots els punts apareixen correctes, podem continuar.

![Zabbix 14](<../imatges/03/1- zabbix (14).png>)

Configurem la connexió de la interfície web amb la base de dades. Seleccionem MySQL, servidor `localhost`, base de dades `zabbix`, usuari `zabbix` i la contrasenya que hem definit abans. Amb això la part web ja queda connectada amb MariaDB.

![Zabbix 15](<../imatges/03/1- zabbix (15).png>)

Definim els ajustos finals del servidor. Posem el nom `zabbixHefesto`, seleccionem la zona horària `Europe/Madrid` i deixem el tema fosc per treballar més còmodament amb la interfície.

![Zabbix 16](<../imatges/03/1- zabbix (16).png>)

Abans d’instal·lar, revisem el resum de preinstal·lació. Aquí comprovem que la base de dades és MySQL, que el servidor és `localhost`, que la base de dades es diu `zabbix`, que l’usuari és correcte i que el servidor queda identificat com `zabbixHefesto`.

![Zabbix 17](<../imatges/03/1- zabbix (17).png>)

Finalment, l’assistent confirma que la interfície web de Zabbix s’ha instal·lat correctament i que s’ha creat el fitxer `conf/zabbix.conf.php`. Amb això ja podem entrar a Zabbix i començar a donar d’alta els dispositius de la infraestructura.

![Zabbix 18](<../imatges/03/1- zabbix (18).png>)

Com a preparació per treballar amb logs del sistema, comprovem que `rsyslog` està actiu al servidor Zabbix. Aquest servei ens servirà més endavant per poder rebre o gestionar registres d’altres equips si volem centralitzar logs o relacionar-los amb la monitorització.

![Rsyslog 1](<../imatges/03/4- rsyslog (1).png>)

Després de deixar Zabbix instal·lat, comencem amb la integració del NAS. Afegim el host de TrueNAS dins de Zabbix amb el nom `truenas` i el nom visible `NAS-Hefesto-1`. Inicialment li assignem una interfície SNMP amb la IP `172.16.0.9`, que és la IP del NAS dins la xarxa.

![NAS 1](<../imatges/03/2- nas (1).png>)

Abans de monitoritzar el NAS, comprovem des de TrueNAS que el servei SNMP està en execució. També veiem que NFS està actiu, ja que aquest NAS també forma part de l’entorn d’emmagatzematge del projecte.

![NAS 2](<../imatges/03/2- nas (2).png>)

A Zabbix intentem vincular el NAS amb una plantilla SNMP adequada. En aquest punt treballem amb plantilles preparades per TrueNAS/FreeNAS, ja que ens permeten obtenir informació del sistema sense haver de crear manualment tots els ítems.

![NAS 3](<../imatges/03/2- nas (3).png>)

Configurem el servei SNMP dins de TrueNAS. Activem el suport SNMP v3, definim l’usuari, el tipus d’autenticació SHA i el protocol de privacitat AES. Aquesta configuració és més segura que utilitzar només una comunitat simple de SNMP v2.

![NAS 4](<../imatges/03/2- nas (4).png>)

Ajustem la configuració del host NAS dins de Zabbix, deixant la plantilla `SNMP FreeNAS`, el grup `NAS`, la IP `172.16.0.9`, el port `161` i la comunitat SNMP mitjançant macro. Amb això Zabbix ja pot consultar dades del NAS per SNMP.

![NAS 5](<../imatges/03/2- nas (5).png>)

Un cop el NAS queda monitoritzat, Zabbix ja comença a generar avisos. En aquest cas apareixen alertes relacionades amb temperatures de discos i amb l’estat d’un pool. Aquestes alertes ens serveixen per comprovar que la integració funciona i que Zabbix està rebent informació real del NAS.

![NAS 6](<../imatges/03/2- nas (6).png>)

Per integrar Proxmox, importem una plantilla específica que permet obtenir mètriques de la plataforma de virtualització. Aquesta plantilla ens ajudarà a monitoritzar nodes, màquines virtuals, recursos i l’estat general de l’entorn Proxmox des de Zabbix.

![Proxmox 1](<../imatges/03/3- proxmox (1).png>)

A Proxmox preparem un token d’API perquè Zabbix pugui consultar informació sense haver d’iniciar sessió manualment. Primer provem la creació del token des de l’apartat de permisos de Proxmox.

![Proxmox 2](<../imatges/03/3- proxmox (2).png>)

Després de crear el token, Proxmox mostra el secret una única vegada. Guardem aquest valor perquè després serà necessari posar-lo a les macros del host dins de Zabbix.

![Proxmox 3](<../imatges/03/3- proxmox (3).png>)

Dins de Zabbix afegim les macros necessàries perquè la plantilla de Proxmox pugui connectar-se per API. Configurem el token, el secret, el host de Proxmox `172.16.0.10` i el port `8006`.

![Proxmox 4](<../imatges/03/3- proxmox (4).png>)

També creem un usuari específic anomenat `zabbix` dins del domini d’autenticació de Proxmox VE. La idea és no dependre sempre de l’usuari `root`, deixant una compte més clara només per a la monitorització.

![Proxmox 5](<../imatges/03/3- proxmox (5).png>)

Amb l’usuari preparat, creem un nou token associat a `zabbix@pve`. Aquest token serà el que utilitzarem finalment perquè Zabbix consulti Proxmox d’una manera més neta i separada de l’usuari administrador.

![Proxmox 6](<../imatges/03/3- proxmox (6).png>)

Finalment, Proxmox ens mostra el secret del token `zabbix@pve:zabbix`. Aquest valor el guardem per configurar-lo a Zabbix i deixar preparada la comunicació per API entre el servidor de monitorització i Proxmox.

![Proxmox 7](<../imatges/03/3- proxmox (7).png>)
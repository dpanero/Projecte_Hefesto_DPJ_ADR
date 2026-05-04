# Instal·lació i Configuració Bàsica de Zabbix

En aquesta part es documenta el procés d’instal·lació inicial del servidor Zabbix dins del projecte Hefesto. El servidor s’ha desplegat sobre una màquina Debian 12 ubicada a la xarxa LAN, amb la IP "`172.16.0.5`". Aquesta màquina actuarà com a punt central de monitorització de la infraestructura, permetent controlar servidors, serveis, dispositius de xarxa i elements del clúster Proxmox entre altres.

Primerament instal·lem el sistema base que sera un Debian 12.

![Zabbix 1](<../imatges/03/zabbix (1).png>)

Ara es realitza un "`apt update`" per actualitzar la llista de repositoris del sistema. Aquest pas és important perquè Debian reconegui els paquets disponibles i també comprovi correctament el repositori de Zabbix que s’utilitzarà durant la instal·lació.

![Zabbix 2](<../imatges/03/zabbix (2).png>)

Descarreguem el paquet oficial del repositori de Zabbix per a Debian 12, primer s’intenta instal·lar sense permisos suficients i finalment s’executa correctament amb "`sudo dpkg -i`", amb això afegim el repositori oficial de Zabbix al sistema.

![Zabbix 3](<../imatges/03/zabbix (3).png>)

En aquesta captura s’instal·len els paquets principals necessaris per fer funcionar Zabbix. S’instal·la el servidor Zabbix amb MySQL/MariaDB, la interfície web en PHP, la configuració per Apache, els scripts SQL, l’agent de Zabbix i el servidor MariaDB. Aquest pas deixa preparada la base del servei de monitorització.

![Zabbix 4](<../imatges/03/zabbix (4).png>)

Aquí s’accedeix a MariaDB i es crea la base de dades `zabbix`. També es crea l’usuari de base de dades que utilitzarà Zabbix per connectar-se. Aquesta separació és correcta perquè Zabbix no treballi directament amb l’usuari administrador de MariaDB.

![Zabbix 5](<../imatges/03/zabbix (5).png>)

En aquesta captura es concedeixen permisos a l’usuari de Zabbix sobre la seva base de dades. També s’activa temporalment el paràmetre `log_bin_trust_function_creators`, necessari per poder importar correctament l’esquema inicial de Zabbix a MariaDB.

![Zabbix 6](<../imatges/03/zabbix (6).png>)

S’importa l’estructura inicial de la base de dades de Zabbix utilitzant el fitxer SQL comprimit que proporciona el paquet `zabbix-sql-scripts`. Aquest pas crea totes les taules internes que necessita Zabbix per guardar hosts, mètriques, usuaris, alertes, esdeveniments i configuracions.

![Zabbix 7](<../imatges/03/zabbix (7).png>)

S’obre el fitxer principal de configuració del servidor Zabbix, ubicat a `/etc/zabbix/zabbix_server.conf`. Aquest fitxer és necessari per indicar a Zabbix com s’ha de connectar amb la base de dades i quins paràmetres ha d’utilitzar durant el seu funcionament.

![Zabbix 8](<../imatges/03/zabbix (8).png>)

Es mostra la configuració de connexió amb la base de dades dins del fitxer `zabbix_server.conf`. S’indica el nom de la base de dades, l’usuari i la contrasenya que utilitzarà el servidor Zabbix per accedir a MariaDB.

![Zabbix 9](<../imatges/03/zabbix (9).png>)

Es comprova de nou la configuració de la base de dades dins del fitxer de Zabbix. Es confirma que els camps principals, com `DBName`, `DBUser` i `DBPassword`, han quedat definits correctament perquè el servei pugui iniciar sense errors de connexió.

![Zabbix 10](<../imatges/03/zabbix (10).png>)

Es revisa el fitxer de configuració d’Apache per a Zabbix. Es pot veure l’àlies `/zabbix`, que permet accedir a la interfície web des del navegador, i també la configuració de PHP, incloent la zona horària `Europe/Madrid`. Això és important perquè els esdeveniments, alertes i gràfiques tinguin l’hora correcta.

![Zabbix 11](<../imatges/03/zabbix (11).png>)

S’accedeix amb el navegador a l’assistent web d’instal·lació de Zabbix mitjançant l’adreça `172.16.0.5/zabbix/setup.php`. Es selecciona l’idioma de la interfície i s’inicia la configuració final des de la part web.

![Zabbix 12](<../imatges/03/zabbix (12).png>)

Es mostra la comprovació de requisits previs de Zabbix. L’assistent valida la versió de PHP, la memòria, la mida màxima de pujada, el temps d’execució, el suport de MySQL i altres mòduls necessaris. Tots els requisits apareixen com a correctes, per tant es pot continuar amb la instal·lació.

![Zabbix 13](<../imatges/03/zabbix (13).png>)

Es configura la connexió de la interfície web de Zabbix amb la base de dades. S’indica que s’utilitzarà MySQL, el servidor `localhost`, la base de dades `zabbix`, l’usuari corresponent i la contrasenya definida anteriorment. Amb això queda enllaçada la part web amb la base de dades ja preparada.

![Zabbix 14](<../imatges/03/zabbix (14).png>)

Es configuren els ajustos finals de l’assistent d’instal·lació. Es defineix el nom del servidor com `zabbixHefesto`, se selecciona la zona horària `Europe/Madrid` i s’escull el tema fosc per a la interfície. Aquest nom ajuda a identificar clarament el servidor dins de l’entorn Hefesto.

![Zabbix 15](<../imatges/03/zabbix (15).png>)

Apareix el resum de preinstal·lació. L’assistent mostra els paràmetres principals configurats: tipus de base de dades, servidor, nom de la base de dades, usuari i nom del servidor Zabbix. Aquest pas serveix per verificar que la configuració és correcta abans de finalitzar la instal·lació.

![Zabbix 16](<../imatges/03/zabbix (16).png>)

Finalment, es mostra que la interfície web de Zabbix s’ha instal·lat correctament. L’assistent confirma que s’ha creat el fitxer de configuració `conf/zabbix.conf.php`, de manera que la instal·lació web queda finalitzada i el servidor Zabbix ja està preparat per iniciar sessió i començar la configuració dels hosts i la monitorització.

![Zabbix 17](<../imatges/03/zabbix (17).png>)
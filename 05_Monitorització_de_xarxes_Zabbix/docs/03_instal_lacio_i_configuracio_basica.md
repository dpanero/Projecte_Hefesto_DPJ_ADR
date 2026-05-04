## Instal·lació i Configuració Bàsica de Zabbix

En aquesta part es documenta el procés d’instal·lació inicial del servidor Zabbix dins del projecte Hefesto. El servidor s’ha desplegat sobre una màquina Debian 12 ubicada a la xarxa LAN, amb la IP "`172.16.0.5`". Aquesta màquina actuarà com a punt central de monitorització de la infraestructura, permetent controlar servidors, serveis, dispositius de xarxa i elements del clúster Proxmox entre altres.

Primerament instal·lem el sistema base que sera un Debian 12.

![Zabbix 1](../imatges/zabbix%20%28(1)%29.png)

Ara es realitza un "`apt update`" per actualitzar la llista de repositoris del sistema. Aquest pas és important perquè Debian reconegui els paquets disponibles i també comprovi correctament el repositori de Zabbix que s’utilitzarà durant la instal·lació.

![Zabbix 2](../imatges/zabbix%20%282%29.png)

Descarreguem el paquet oficial del repositori de Zabbix per a Debian 12, primer s’intenta instal·lar sense permisos suficients i finalment s’executa correctament amb "`sudo dpkg -i`", amb això afegim el repositori oficial de Zabbix al sistema.

![Zabbix 3](../imatges/zabbix%20%283%29.png)

En aquesta captura s’instal·len els paquets principals necessaris per fer funcionar Zabbix. S’instal·la el servidor Zabbix amb MySQL/MariaDB, la interfície web en PHP, la configuració per Apache, els scripts SQL, l’agent de Zabbix i el servidor MariaDB. Aquest pas deixa preparada la base del servei de monitorització.

![Zabbix 4](../imatges/zabbix%20%284%29.png)

Aquí s’accedeix a MariaDB i es crea la base de dades `zabbix`. També es crea l’usuari de base de dades que utilitzarà Zabbix per connectar-se. Aquesta separació és correcta perquè Zabbix no treballi directament amb l’usuari administrador de MariaDB.

![Zabbix 5](../imatges/zabbix%20%285%29.png)

En aquesta captura es concedeixen permisos a l’usuari de Zabbix sobre la seva base de dades. També s’activa temporalment el paràmetre `log_bin_trust_function_creators`, necessari para poder importar correctamente el esquema inicial de Zabbix en MariaDB.

![Zabbix 6](../imatges/zabbix%20%286%29.png)

Aquí se importa la estructura inicial de la base de datos de Zabbix usando el archivo SQL comprimido que instala el propio paquete `zabbix-sql-scripts`. Este paso crea las tablas internas que Zabbix necesita para guardar hosts, métricas, usuarios, alertas, eventos y configuración.

S’importa l’estructura inicial de la base de dades de Zabbix utilitzant el fitxer SQL comprimit que proporciona el paquet `zabbix-sql-scripts`. Aquest pas crea totes les taules internes que necessita Zabbix per guardar hosts, mètriques, usuaris, alertes, esdeveniments i configuracions.

![Zabbix 7](../imatges/zabbix%20%287%29.png)

S’obre el fitxer principal de configuració del servidor Zabbix, ubicat a `/etc/zabbix/zabbix_server.conf`. Aquest fitxer és necessari per indicar a Zabbix com s’ha de connectar amb la base de dades i quins paràmetres ha d’utilitzar durant el seu funcionament.

![Zabbix 8](../imatges/zabbix%20%288%29.png)

Es mostra la configuració de connexió amb la base de dades dins del fitxer `zabbix_server.conf`. S’indica el nom de la base de dades, l’usuari i la contrasenya que utilitzarà el servidor Zabbix per accedir a MariaDB.

![Zabbix 9](../imatges/zabbix%20%289%29.png)

Es comprova de nou la configuració de la base de dades dins del fitxer de Zabbix. Es confirma que els camps principals, com `DBName`, `DBUser` i `DBPassword`, han quedat definits correctament perquè el servei pugui iniciar sense errors de connexió.

![Zabbix 10](../imatges/zabbix%20%2810%29.png)

Es revisa el fitxer de configuració d’Apache per a Zabbix. Es pot veure l’àlies `/zabbix`, que permet accedir a la interfície web des del navegador, i també la configuració de PHP, incloent la zona horària `Europe/Madrid`. Això és important perquè els esdeveniments, alertes i gràfiques tinguin l’hora correcta.

![Zabbix 11](../imatges/zabbix%20%2811%29.png)

S’accedeix amb el navegador a l’assistent web d’instal·lació de Zabbix mitjançant l’adreça `172.16.0.5/zabbix/setup.php`. Es selecciona l’idioma de la interfície i s’inicia la configuració final des de la part web.

![Zabbix 12](../imatges/zabbix%20%2812%29.png)

Es mostra la comprovació de requisits previs de Zabbix. L’assistent valida la versió de PHP, la memòria, la mida màxima de pujada, el temps d’execució, el suport de MySQL i altres mòduls necessaris. Tots els requisits apareixen com a correctes, per tant es pot continuar amb la instal·lació.

![Zabbix 13](../imatges/zabbix%20%2813%29.png)

Es configura la connexió de la interfície web de Zabbix amb la base de dades. S’indica que s’utilitzarà MySQL, el servidor `localhost`, la base de dades `zabbix`, l’usuari corresponent i la contrasenya definida anteriorment. Amb això queda enllaçada la part web amb la base de dades ja preparada.

![Zabbix 14](../imatges/zabbix%20%2814%29.png)

Es configuren els ajustos finals de l’assistent d’instal·lació. Es defineix el nom del servidor com `zabbixHefesto`, se selecciona la zona horària `Europe/Madrid` i s’escull el tema fosc per a la interfície. Aquest nom ajuda a identificar clarament el servidor dins de l’entorn Hefesto.

![Zabbix 15](../imatges/zabbix%20%2815%29.png)

Apareix el resum de preinstal·lació. L’assistent mostra els paràmetres principals configurats: tipus de base de dades, servidor, nom de la base de dades, usuari i nom del servidor Zabbix. Aquest pas serveix per verificar que la configuració és correcta abans de finalitzar la instal·lació.

![Zabbix 16](../imatges/zabbix%20%2816%29.png)

Finalment, es mostra que la interfície web de Zabbix s’ha instal·lat correctament. L’assistent confirma que s’ha creat el fitxer de configuració `conf/zabbix.conf.php`, de manera que la instal·lació web queda finalitzada i el servidor Zabbix ja està preparat per iniciar sessió i començar la configuració dels hosts i la monitorització.

![Zabbix 17](../imatges/zabbix%20%2817%29.png)
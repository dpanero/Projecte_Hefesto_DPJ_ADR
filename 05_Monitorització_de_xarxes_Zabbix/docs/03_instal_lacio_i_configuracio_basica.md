# Instal·lació i Configuració Bàsica de Zabbix

En aquesta part documentem la instal·lació inicial de Zabbix dins del projecte Hefesto i la integració dels primers dispositius importants de la infraestructura. El servidor Zabbix s’ha desplegat sobre una màquina Debian 12 situada a la xarxa LAN amb la IP `172.16.0.5`. Aquesta màquina serà el punt central des d’on revisarem l’estat dels servidors, serveis, dispositius de xarxa, NAS i nodes Proxmox.

Primerament instal·lem el sistema base de Debian 12 sobre la màquina virtual que farà de servidor Zabbix, aquesta VM queda desplegada dins de Proxmox i serà la base sobre la qual instal·larem tots els components de monitorització.

![Zabbix 1](<../imatges/03/1- zabbix (1).png>)

En aquest cas deixem la interfície `ens18` amb la IP `172.16.0.5`, màscara `255.255.255.0`, porta d’enllaç `172.16.0.1` i DNS `172.16.0.1` i `8.8.8.8`. Això és important perquè el servidor de monitorització sempre tingui una adreça fixa dins la LAN.

![Zabbix 2](<../imatges/03/1- zabbix (2).png>)

Un cop tenim la xarxa preparada, fem un `apt update` per actualitzar la llista de repositoris del sistema. Així ens assegurem que Debian pot trobar els paquets necessaris i que el repositori de Zabbix quedarà carregat correctament.

![Zabbix 3](<../imatges/03/1- zabbix (3).png>)

Aquí descarreguem el paquet oficial del repositori de Zabbix per a Debian 12, primer es veu un intent que em realitzat sense permisos suficients i després ja s’instal·la correctament amb `sudo dpkg -i`. Amb això afegim el repositori oficial de Zabbix al sistema.

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

---

## NAS

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

---

## Cluster Proxmox

Per integrar Proxmox, importem una plantilla específica que permet obtenir mètriques de la plataforma de virtualització. Aquesta plantilla ens ajudarà a monitoritzar nodes, màquines virtuals, recursos i l’estat general de l’entorn Proxmox des de Zabbix.

### Pas previ

Ens em trobat una problematica al itnegrar Proxmox i es que al tenir 3 nodes si afegim els 3 rebem les alertes triplicades (ja que els tres nodes reporten alertes dels altres dos) i al només afegir només 1 perdem alta disponibilitat ja que al caure el principal i migrar-se tot al secundari o terciari la IP al a que apunta Zabbix ja no correspon.

La solució ha estat instalar keepalived que ens permetra tenir una IP per al cluster que els tres nodes compartiran i en cas de que el principal tingui algun problema aquesta passara automaticament a respondre per al segon node (tot aixó conservant les seves IPs individuals que portem utilitzant tot el projecte), aixó també ens permet crear un punter DNS cap a aquesta IP del keepalived i sempre tenir accés web per aquí estigui el servidor que estigui.

Procedim amb la instalació i activació del servei als 3 nodes de Proxmox:

![Keepalived 1](<../imatges/03/5- keepalived (1).png>)
![Keepalived 2](<../imatges/03/5- keepalived (2).png>)

Primer configurem el node principal com `MASTER`.

![Keepalived 3](<../imatges/03/5- keepalived (3).png>)

I els altres dos com `BACKUP`.

![Keepalived 4](<../imatges/03/5- keepalived (4).png>)
![Keepalived 5](<../imatges/03/5- keepalived (5).png>)

Amb aixó ja tenim activa la IP del keepalived, confirmem que respon:

![Keepalived 5](<../imatges/03/5- keepalived (6).png>)

Ara ja podem proseguir amb la integració del cluster sense problema de duplicació d'alertes.

### Integració cluster Proxmox

Creem un usuari específic anomenat `zabbix` dins del domini d’autenticació de Proxmox VE. La idea és no dependre sempre de l’usuari `root`, deixant una compte més clara només per a la monitorització.

![Proxmox 1](<../imatges/03/3- proxmox (1).png>)

A Proxmox preparem un token d’API perquè Zabbix pugui consultar informació sense haver d’iniciar sessió manualment. Primer provem la creació del token des de l’apartat de permisos de Proxmox.

![Proxmox 2](<../imatges/03/3- proxmox (2).png>)

Després de crear el token, Proxmox mostra el secret una única vegada. Guardem aquest valor perquè després serà necessari posar-lo a les macros del host dins de Zabbix.

![Proxmox 3](<../imatges/03/3- proxmox (3).png>)

Importem la plantilla de Proxmox que em descarregat del lloc oficial a ("https://www.zabbix.com/integrations/proxmox").

![Proxmox 4](<../imatges/03/3- proxmox (4).png>)

Ja podem crear el host per al cluster de proxmox indicant la plantilla que acabem d'importar.

![Proxmox 5](<../imatges/03/3- proxmox (5).png>)

Dins de Zabbix afegim les macros necessàries perquè la plantilla de Proxmox pugui connectar-se per API. Configurem el token, el secret, el host de Proxmox `172.16.0.4` i el port `8006`.

![Proxmox 6](<../imatges/03/3- proxmox (6).png>)

---

## PfSense i Suricata

Com a preparació per treballar amb logs del sistema, comprovem que `rsyslog` està actiu al servidor Zabbix. Aquest servei ens servirà més endavant per poder rebre o gestionar registres d’altres equips si volem centralitzar logs o relacionar-los amb la monitorització, en aquest cas ho farem servir per pfsense i Suricata.

![Rsyslog 1](<../imatges/03/4- rsyslog (1).png>)

Ens assegurem de tenir activa la recepció de logs remots al port 514 tant per TCP com per UDP.

![Rsyslog 2](<../imatges/03/4- rsyslog (2).png>)
![Rsyslog 3](<../imatges/03/4- rsyslog (3).png>)

Un cop confirmat que rsyslog esta actiu podem continuar, en primer lloc creem un arxiu a `/etc/rsyslog.d/30—pfsense.conf` on indiquem de quina IP ha de rebre els logs i on els gardara, indiquem en el nostre cas la IP del PfSense `172.16.0.1` i l'arxiu de logs `/var/log/pfsense.log`.

![Rsyslog 4](<../imatges/03/4- rsyslog (4).png>)

Amb un tail verifiquem que estem rebent els logs del pfsense i per tant també de Suricata al nostre Zabbix.

![Rsyslog 5](<../imatges/03/4- rsyslog (5).png>)

Entrem dins el host del propi servidor de Zabbix al panell per afegir un nou item.

![Rsyslog 5.2](<../imatges/03/4- rsyslog (5.2).png>)

En aquest item em indicat una "key" per tal que pugui filtrar els logs que continguin la paraula Suricata i poder separar aquests logs de la resta.

![Rsyslog 6](<../imatges/03/4- rsyslog (6).png>)

Aquí ja podem veure l'item actiu:

![Rsyslog 7](<../imatges/03/4- rsyslog (7).png>)

Creem un altre item del host zabbix per als logs generals de pfsense.

![Rsyslog 8](<../imatges/03/4- rsyslog (8).png>)

---

## Servidor WEB

Ara integrarem amb Zabbix el nostre servidor Web que tenim virtualitzat a la DMZ per tal de poder-lo també monitoritzar.

Primer de tot accedeixo per SSH al servidor web.

![sweb 1](<../imatges/03/7- wserver (1).png>)

Ara descarrego el paquet de l'agent Zabbix amb `wget https://repo.zabbix.com/zabbix/7.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_latest_7.0+ubuntu24.04_all.deb`.

![sweb 2](<../imatges/03/7- wserver (2).png>)

I finalment instal·lo l'agent de Zabbix.

![sweb 3](<../imatges/03/7- wserver (3).png>)

Configuro l'arxiu `/etc/zabbix/zabbix_agentd.conf` indicant la IP del servidor Zabbix.

![sweb 4](<../imatges/03/7- wserver (4).png>)

Activem i reiniciem el servei.

![sweb 5](<../imatges/03/7- wserver (5).png>)

Confirmem que esta actiu amb un `status`.

![sweb 6](<../imatges/03/7- wserver (6).png>)

Donem permís a mirar logs del sistema a l'usauri zabbix per tal que pugiu reportar les alertes del apache.

![sweb 7](<../imatges/03/7- wserver (7).png>)

Ara crearem un script personalitzat al directori `/usr/local/bin/` anomenat `hefesto_web_health.sh`.

[(Clica aquí per veure la explicació completa del script | Apartat de desenvolupament de Scripts)](https://github.com/dpanero/Projecte_Hefesto_DPJ_ADR/blob/main/05_Monitoritzaci%C3%B3_de_xarxes_Zabbix/docs/05_personalitzacio_i_desenvolupament_de_scripts.md#desenvolupament-de-scripts-personalitzats)

### Contingut del Script

```

#!/bin/bash

MODE="$1"

apache_service() {
    systemctl is-active --quiet apache2
    if [ $? -eq 0 ]; then
        echo 1
    else
        echo 0
    fi
}

http_code() {
    curl -s -o /dev/null -w "%{http_code}" http://127.0.0.1/ --max-time 5
}

apache_configtest() {
    apache2ctl configtest >/dev/null 2>&1
    if [ $? -eq 0 ]; then
        echo 1
    else
        echo 0
    fi
}

apache_errors() {
    if [ -f /var/log/apache2/error.log ]; then
        tail -n 200 /var/log/apache2/error.log 2>/dev/null | grep -iE "error|crit|fail" | wc -l
    else
        echo 0
    fi
}

apache_5xx() {
    if [ -f /var/log/apache2/access.log ]; then
        tail -n 200 /var/log/apache2/access.log 2>/dev/null | awk '$9 ~ /^5/ {c++} END {print c+0}'
    else
        echo 0
    fi
}

www_disk() {
    df -P /var/www 2>/dev/null | awk 'NR==2 {gsub("%","",$5); print $5}'
}

apache_processes() {
    pgrep -fc apache2
}

health_score() {
    SCORE=100

    SERVICE=$(apache_service)
    CODE=$(http_code)
    CONFIG=$(apache_configtest)
    ERRORS=$(apache_errors)
    HTTP5XX=$(apache_5xx)
    DISK=$(www_disk)

    if [ "$SERVICE" -eq 0 ]; then
        SCORE=$((SCORE-35))
    fi

    if [ "$CODE" -lt 200 ] || [ "$CODE" -ge 400 ]; then
        SCORE=$((SCORE-25))
    fi

    if [ "$CONFIG" -eq 0 ]; then
        SCORE=$((SCORE-20))
    fi

    if [ "$ERRORS" -gt 10 ]; then
        SCORE=$((SCORE-10))
    fi

    if [ "$HTTP5XX" -gt 5 ]; then
        SCORE=$((SCORE-10))
    fi

    if [ "$DISK" -gt 80 ]; then
        SCORE=$((SCORE-10))
    fi

    if [ "$SCORE" -lt 0 ]; then
        SCORE=0
    fi

    echo "$SCORE"
}

summary() {
    echo "apache=$(apache_service) http=$(http_code) config=$(apache_configtest) errors=$(apache_errors) http5xx=$(apache_5xx) disk_www=$(www_disk) processes=$(apache_processes) score=$(health_score)"
}

case "$MODE" in
    apache)
        apache_service
        ;;
    http_code)
        http_code
        ;;
    configtest)
        apache_configtest
        ;;
    errors)
        apache_errors
        ;;
    5xx)
        apache_5xx
        ;;
    disk_www)
        www_disk
        ;;
    processes)
        apache_processes
        ;;
    score)
        health_score
        ;;
    summary)
        summary
        ;;
    *)
        echo "Usage: $0 {apache|http_code|configtest|errors|5xx|disk_www|processes|score|summary}"
        exit 1
        ;;
esac
```

---

## Resultat

A les següents captures es demostren tots els hosts i els items que em creat a Zabbix actius i rebent informació:

![Comprov 1](<../imatges/03/6- comprov (1).png>)
![Comprov 2](<../imatges/03/6- comprov (2).png>)
![Comprov 3](<../imatges/03/6- comprov (3).png>)
![Comprov 4](<../imatges/03/6- comprov (4).png>)
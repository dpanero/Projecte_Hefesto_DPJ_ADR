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

### Script per monitoritzar Suricata i PfSense

Em creat un script a `/etc/rsyslog.d/30—pfsense.conf` on indiquem de quina IP ha de rebre els logs i on els gardara, indiquem en el nostre cas la IP del PfSense `172.16.0.1` i l'arxiu de logs `/var/log/pfsense.log`.
Gràcies a aquest Script podem rebre els logs que hem configurat per enviar desde pfsense/suricata al projecte d'IDS/IPS i obtenir-los com un item del host zabbix [Clica aquí per anar a l'explicació](https://github.com/dpanero/Projecte_Hefesto_DPJ_ADR/blob/main/05_Monitoritzaci%C3%B3_de_xarxes_Zabbix/docs/03_instal_lacio_i_configuracio_basica.md#pfsense-i-suricata).

![Rsyslog 4](<../imatges/03/4- rsyslog (4).png>)

### Script per monitoritzar el servidor Web de la DMZ

Per ampliar la monitorització més enllà de les plantilles per defecte, he creat un script personalitzat per al servidor web del projecte Hefesto. 
Aquest script comprova l’estat real d’Apache, el codi HTTP retornat per la web, la configuració del servei, els errors recents, les respostes HTTP 5xx, l’ús del directori `/var/www` i una puntuació general de salut del servidor.

[Clica aquí per veure com s'ha configurat el servidor web amb l'agent de Zabbix](https://github.com/dpanero/Projecte_Hefesto_DPJ_ADR/blob/main/05_Monitoritzaci%C3%B3_de_xarxes_Zabbix/docs/03_instal_lacio_i_configuracio_basica.md#servidor-web)

Contingut del script:

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

## Integració de plugins addicionals


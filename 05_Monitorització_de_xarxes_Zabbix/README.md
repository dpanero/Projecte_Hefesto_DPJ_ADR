# Monitorització de xarxa - Zabbix

<p align="center">
<img src="imatges/logo_zabbix.png" width="200">
</p>

Aquest projecte consisteix en la implementació d'un sistema de monitorització d'infraestructura.

---

# Tecnologies utilitzades

- Zabbix
- SNMP
- Scripts de monitorització

---

# Objectiu del projecte

Implementar un sistema capaç de supervisar:

- servidors
- dispositius de xarxa
- serveis
- rendiment del sistema

El sistema genera alertes i informes per detectar problemes abans que afectin el servei.

---

# Introducció a la Monitorització de Xarxa

La monitorització de xarxa consisteix en controlar de forma contínua l’estat dels equips, serveis, màquines virtuals i dispositius que formen part d’una infraestructura informàtica. L’objectiu principal és saber en tot moment si els sistemes funcionen correctament, detectar errors abans que afectin greument el servei i tenir informació útil per poder reaccionar ràpidament.

En el nostre projecte Hefesto, la monitorització és una part important perquè l’entorn no és només una xarxa simple, sinó una infraestructura formada per diversos elements: pfSense com a firewall i router principal, servidors dins la LAN i la DMZ, un clúster de Proxmox, màquines virtuals, TrueNAS, serveis interns i sistemes de seguretat com Suricata. Per aquest motiu, necessitem una eina que ens permeti veure l’estat general de la xarxa des d’un únic punt.

La monitorització ens permet comprovar diferents aspectes:

- Disponibilitat dels equips.
- Ús de CPU, memòria RAM i disc.
- Estat de les interfícies de xarxa.
- Temps de resposta dels serveis.
- Errors o caigudes de màquines virtuals.
- Estat dels nodes Proxmox.
- Estat de serveis crítics.
- Alertes generades per sistemes com pfSense o Suricata.
- Informació obtinguda per SNMP, agents o APIs.

Aquesta informació és important perquè en un entorn real no és suficient saber que “la xarxa funciona”. Cal poder saber quin equip falla, quan ha fallat, per què pot haver passat i quina gravetat té. Sense monitorització, molts errors només es detecten quan l’usuari ja nota el problema. En canvi, amb una eina de monitorització es poden generar avisos abans que el problema sigui més greu.

Un exemple seria una màquina virtual de Proxmox que comença a consumir massa RAM, un disc que s’està quedant sense espai, una interfície de pfSense amb tràfic anormal o una alerta de Suricata relacionada amb possibles escanejos o intents d’accés. Amb monitorització, tota aquesta informació es pot centralitzar i consultar des d’un dashboard.

En el projecte també és important evitar informació duplicada o poc útil. Per exemple, en el cas del clúster Proxmox, si es monitoritzen diversos nodes per separat sense criteri, poden aparèixer avisos repetits o dades duplicades. Per això es va plantejar l’ús de configuracions com Keepalived o una IP virtual en determinats casos, per tenir un punt lògic de referència i reduir repeticions en la supervisió de serveis o logs. Això ajuda a tenir una monitorització més clara i més fàcil d’interpretar.

Les eines de monitorització més comunes són Nagios, Zabbix i Cacti. Totes poden servir per controlar una xarxa, però no totes estan pensades per al mateix tipus d’ús. Nagios és molt conegut per la comprovació de serveis i alertes, Cacti destaca sobretot per les gràfiques de tràfic i rendiment amb SNMP, i Zabbix ofereix una solució més completa perquè combina monitorització, alertes, plantilles, dashboards, agents, SNMP, APIs i automatización.

En resum, la monitorització és necessària per tenir control real sobre la infraestructura. En el nostre cas, ens permet passar d’un entorn on revisem els errors manualment a un entorn on el sistema ens avisa, registra dades, mostra gràfiques i ajuda a prendre decisions tècniques.

---

# Selecció i Justificació de l'Eina

Per aquest projecte s’han comparat tres eines principals de monitorització: Nagios, Zabbix i Cacti. Les tres són eines conegudes i utilitzades en administració de sistemes, però tenen diferències importants.

## Comparació entre Nagios, Zabbix i Cacti

| Eina | Avantatges | Inconvenients | Ús principal |
|---|---|---|---|
| Nagios | Molt estable, molt conegut, bon sistema d’alertes i molts plugins disponibles. | La configuració pot ser menys còmoda, la interfície és més antiga i necessita més treball manual. | Supervisió de serveis i alertes. |
| Zabbix | Solució molt completa, interfície web moderna, agents propis, SNMP, plantilles, dashboards, alertes, gràfiques i integració amb APIs. | Pot ser més pesat que altres eines i necessita una configuració inicial més treballada. | Monitorització completa d’infraestructura. |
| Cacti | Molt útil per generar gràfiques de tràfic i rendiment, especialment amb SNMP. | Està més enfocat a gràfiques que a una monitorització completa amb alertes avançades. | Gràfiques de xarxa i rendiment. |

Nagios és una bona eina si el que es busca principalment és comprovar si els serveis estan actius o caiguts. És molt potent amb plugins i alertes, però per al nostre cas es queda una mica curt si volem dashboards més visuals, plantilles preparades, gràfiques i una gestió més centralitzada.

Cacti és útil per veure gràfiques, sobretot de xarxa, consum d’ample de banda i dades obtingudes per SNMP. Tot i això, no és l’opció més adequada com a eina principal del projecte, perquè el nostre entorn necessita més que gràfiques. Necessitem alertes, control de màquines, integració amb serveis, plantilles i una visió general de tota la infraestructura.

Zabbix és l’eina que millor encaixa amb el projecte perquè permet monitoritzar molts tipus d’elements des d’un únic lloc. Es pot utilitzar amb agent Zabbix, SNMP, ICMP, plantilles, APIs i comprovacions personalitzades. Això encaixa molt bé amb una infraestructura com Hefesto, on tenim equips diferents i serveis de xarxa diversos.

## Requisits del nostre entorn

L’eina escollida havia de complir aquests requisits:

- Poder monitoritzar màquines Linux.
- Poder controlar màquines virtuals dins de Proxmox.
- Poder obtenir informació de xarxa mitjançant SNMP.
- Poder supervisar pfSense i TrueNAS.
- Poder crear dashboards visuals.
- Poder generar alertes quan un servei cau o supera un llindar.
- Poder consultar l’estat de CPU, RAM, disc i xarxa.
- Poder integrar informació relacionada amb logs o alertes de seguretat.
- Poder créixer si s’afegeixen més servidors en el futur.
- Poder adaptar-se a una xarxa dividida en LAN, DMZ i altres segments.

Aquests requisits fan que Zabbix sigui la millor opció, perquè no només comprova si un host respon, sinó que també permet obtener métriques detallades i representar-les en gràfiques i dashboards.

## Eina seleccionada: Zabbix

L’eina seleccionada per al projecte és Zabbix.

Hem triat Zabbix perquè és una plataforma de monitorització completa i encaixa millor amb l’escenari de Hefesto. El nostre projecte no es limita a comprovar si un servidor està encès, sinó que busca tenir una visió global de tota la infraestructura.

Amb Zabbix podem monitoritzar:

- El servidor Zabbix.
- Els nodes Proxmox.
- Les màquines virtuals importants.
- pfSense.
- TrueNAS.
- Serveis interns de la LAN.
- Serveis exposats a la DMZ.
- Ús de recursos dels sistemes.
- Estat de xarxa.
- Alertes relacionades amb seguretat o disponibilitat.

Un altre punt important és que Zabbix permet utilitzar plantilles. Això facilita molt la feina, perquè en lloc de crear totes les comprobacions manualment, es poden aplicar plantilles segons el tipus d’equip o servei. Per exemple, es poden usar plantilles para Linux, SNMP, TrueNAS o serveis concrets.

També és important la parte visual. Zabbix permet crear dashboards personalitzats amb gràfiques, mapes, problemes actius i estat dels hosts. Això és útil per documentar el projecte i també per tenir una pantalla de control clara.

---

# Documentació Zabbix

| Apartat | Document |
|---|---|
| Instal·lació i Configuració Bàsica | [Instal·lació i Configuració Bàsica](docs/03_instal_lacio_i_configuracio_basica.md) |
| Disseny de Polítiques de Monitorització | [Disseny de Polítiques de Monitorització](docs/04_disseny_de_politiques_de_monitoritzacio.md) |
| Personalització i Desenvolupament de Scripts | [Personalització i Desenvolupament de Scripts](docs/05_personalitzacio_i_desenvolupament_de_scripts.md) |
| Implementació de Dashboard i Informes | [Implementació de Dashboard i Informes](docs/06_implementacio_de_dashboard_i_informes.md) |
| Proves i Validació | [Proves i Validació](docs/07_proves_i_validacio.md) |

---

# Justificació final

La selecció de Zabbix està justificada perquè és l’eina que millor cobreix les necessitats del projecte. Nagios és molt vàlid per alertes i comprovacions, però és menys còmode per una documentació visual i una monitorització completa. Cacti és molt bo per gràfiques SNMP, però no cobreix tan bé la gestió global d’alertes, hosts i serveis.

Zabbix ofereix un equilibri millor entre funcionalitat, escalabilitat i facilitat de visualització. A més, permet adaptar-se a una infraestructura com Hefesto, que està formada per diferents zones de xarxa, serveis interns, virtualització, firewall, emmagatzematge i sistemes de seguretat.

Per aquest motiu, Zabbix és l’opció escollida per implementar el sistema de monitorització de xarxa del projecte.
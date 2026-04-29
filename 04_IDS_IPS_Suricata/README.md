# IDS / IPS - Suricata

<p align="center">
<img src="imatges/logo_suricata.png" width="200">
</p>

Aquest mini projecte consisteix en la implementació d'un sistema de detecció i prevenció d'intrusions dins del laboratori.

---

# Documentació

| Document | Descripció |
|---------|------------|
| [Instal·lació](docs/installation.md) | Instal·lació i configuració del sistema IDS |

---

# Tecnologies involucrades en aquest miniprojecte
(Entorn)
- Pfsense
(Principal)
- Suricata
(Complementaries)
- Proxmox
- OpenVPN
- Zabbix
---

# Objectiu del projecte

Desplegar un sistema IDS/IPS que permeti:

- detectar tràfic sospitós
- analitzar logs de seguretat
- generar alertes davant possibles atacs
- simular atacs per validar el sistema

Aquest sistema ajuda a reforçar la seguretat de la infraestructura del laboratori.
---

# Introducció als IDS/IPS 
## Presentació dels conceptes bàsics de detecció i prevenció d'intrusions
Diferencies:
| IDS | Sistema que detecta intrusions però no les bloqueja |
| ---------- | ---------- |
| IPS | Sistema que detecta i bloqueja automàticament |
- El IDS (Intrusion Detection System) com el seu nom indica es un dispositiu que monitoritza la xarxa per tal de detectar si n’hi ha activitats malicioses o si s’ha violat alguna política de seguretat. Aquest només detecta les intrusions, això vol dir que només ens generarà una alerta però no bloquejarà res.
- El IPS (Intrusion Prevention System) en canvi si que pot bloquejar en temps real la activitat maliciosa per tant aquest va un pas més enllà que el IDS, la manera de fer-ho es bloquejar ports o tancar alguna connexió que pugui ser sospitosa.
### Explicació del paper dels IDS i IPS en la seguretat informàtica
El seu paper a la seguretat informàtica es poder actuar sobre la capa 7 (la de aplicació) ja que el Firewall filtra el trànsit per origen/destí i port (Capes 3 i 4). 
Per fer-ho el IDS/IPS realitza una Inspecció Profunda de Paquets (DPI) arribant a la capa d'Aplicació (Capa 7) aquest permet identificar patrons de malware, intents d’atac i altres anomalies que un firewall passaria de llarg.
## Discussió sobre amenaces comunes i casos d'ús típics
Explicarem una mica amenaces comuns per tenir-les en compte:
•	Escaneig de ports:
Intenten detectar serveis oberts en un sistema fent servir eines com per exemple nmap, es una fase de reconeixement previ a realitzar un atac.
Amb IDS/IPS podem detectar aquests comportaments i protegir-nos.
•	Atacs DoS/DDoS:
L’objectiu es saturar una xarxa enviant grans volums de dades fent que no pugui funcionar correctament.
Els sistemes IDS/IPS poden identificar aquests atacs detectant patrons.
•	Injecció SQL:
Són atacs dirigits a aplicacions web mitjançant l’enviament de dades malformades, permet accedir o modificar bases de dades.
Un IDS/IPS pot detectar aquestes peticions analitzant el contingut HTTP.
•	Força bruta:
Consisteix en realitzar múltiples intents d’autenticació en un sistema de manera continua fins trobar les credencials i afecta principalment serveis com SSH, VPN o panells web.
Un IDS/IPS pot detectar aquests intents repetits i bloquejar la IP atacant.


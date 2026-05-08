# Projecte Proxmox

## Índex de documents del projecte

| Apartat | Document | Format | Enllaç |
|---|---|---|---|
| Inici | README del projecte | MD | [Tornar a l'inici](../README.md) |
| 01 | Introducció | MD | [01_Introduccio.md](./01_Introduccio.md) |
| 02 | Disseny de la Infraestructura | MD | [02_Disseny_de_la_Infraestructura.md](./02_Disseny_de_la_Infraestructura.md) |
| 03 | Preparació de l'Entorn Base (VirtualBox) | PDF | [03_Preparacio_de_lEntorn_Base_VirtualBox.pdf](./03_Preparacio_de_lEntorn_Base_VirtualBox.pdf) |
| 04 | Instal·lació de Proxmox VE | PDF | [04_Installacio_de_Proxmox_VE.pdf](./04_Installacio_de_Proxmox_VE.pdf) |
| 05 | Configuració de Xarxa al Clúster | PDF | [05_Configuracio_de_Xarxa_al_Cluster.pdf](./05_Configuracio_de_Xarxa_al_Cluster.pdf) |
| 06 | Implementació d'Emmagatzematge | PDF | [06_Implementacio_dEmmagatzematge.pdf](./06_Implementacio_dEmmagatzematge.pdf) |
| 07 | Desplegament de Màquines Virtuals | PDF | [07_Desplegament_de_Maquines_Virtuals.pdf](./07_Desplegament_de_Maquines_Virtuals.pdf) |
| 08 | Configuració de Recursos | PDF | [08_Configuracio_de_Recursos.pdf](./08_Configuracio_de_Recursos.pdf) |
| 09 | Conclusions del Projecte Proxmox | PDF | [09_Conclusions_Projecte_Proxmox.pdf](./09_Conclusions_Projecte_Proxmox.pdf) |
| 10 | Integració i Optimització | PDF | [10_Integracio_i_Optimitzacio.pdf](./10_Integracio_i_Optimitzacio.pdf) |
| 11 | Optimització del Rendiment | PDF | [11_Optimitzacio_del_Rendiment.pdf](./11_Optimitzacio_del_Rendiment.pdf) |
| 12 | Conclusions del Projecte Proxmox avançat | PDF | [12_Conclusions_Projecte_Proxmox_avancat.pdf](./12_Conclusions_Projecte_Proxmox_avancat.pdf) |


## 2. Disseny de la Infraestructura

### 2.1 Visió general del projecte

Abans d’executar cap instal·lació, hem dissenyat la infraestructura sobre paper. L’objectiu del projecte és construir un entorn virtual realista que reprodueixi a petita escala el que trobaríem en una empresa real: separació clara entre xarxes (interna, exposada, emmagatzematge), serveis essencials (web, monitoratge, IA), un tallafocs central que controla tot el tràfic i un sistema d’emmagatzematge centralitzat compartit per tots els nodes.

Aquest disseny preliminar és el que ens permetrà més endavant configurar Proxmox de manera coherent: cada decisió de xarxa, de storage o de recursos respondrà a una necessitat ja identificada en aquesta fase. Saltar-se aquest pas és el principal error en projectes de virtualització.

### 2.2 Topologia de xarxa

Per establir les comunicacions entre les diferents màquines virtuals, garantir la seguretat de l’entorn i preparar el terreny per a l’Alta Disponibilitat (que veurem a la fase avançada del projecte), he dissenyat una topologia de xarxa segmentada governada per un tallafocs central (pfSense). L’entorn es divideix en quatre zones lògiques:

- **Zona LAN (172.16.0.0/24):** xarxa interna segura. Aquí resideixen les IPs de gestió dels 3 nodes de Proxmox, el NAS i les VMs de serveis interns com Zabbix i la màquina d’IA. El pfSense n’és la passarel·la (172.16.0.1).
- **Zona DMZ (10.0.0.0/24):** zona aïllada per als serveis exposats a l’exterior, com el servidor web (10.0.0.10). Si aquest servidor rep un atac, el firewall bloquejarà el pas cap a la xarxa LAN, protegint els serveis crítics.
- **Zona d’Emmagatzematge NFS (172.17.0.0/24):** xarxa dedicada exclusivament al tràfic de discos virtuals. Connecta directament els nodes de Proxmox amb el NAS mitjançant una connexió agregada (Bond) per oferir màxima velocitat i tolerància a fallades, evitant saturar la xarxa LAN amb el pes de l’emmagatzematge.
- **Zona VPN (10.8.0.0/24):** servei integrat al pfSense per permetre la connexió segura des de l’exterior cap a l’entorn de pràctiques.

### 2.3 Diagrama lògic

El següent diagrama representa visualment l’organització de zones, IPs i serveis del projecte:

![Topologia de xarxa amb pfSense, zones LAN/DMZ/NFS/VPN i les VMs]()

### 2.4 Components de la infraestructura

La infraestructura està formada pels següents elements, cadascun amb un rol clarament definit:

- **3 nodes Proxmox VE** (`proxmox`, `proxmox2`, `proxmox3`) amb IPs de gestió `172.16.0.10`, `172.16.0.8` i `172.16.0.6` respectivament. Aquests són els hipervisors que allotgen totes les VMs.
- **1 NAS amb TrueNAS Core** (`172.16.0.9` a la LAN, `172.17.0.2` a la xarxa NFS) que centralitza l’emmagatzematge dels discos virtuals i els ISOs.
- **1 tallafocs pfSense** que actua de passarel·la entre totes les zones, fa NAT cap a Internet i ofereix el servei de VPN.
- **VMs de serveis interns:** Zabbix (monitoratge, `172.16.0.5`) i una màquina dedicada a IA (`172.16.0.3`).
- **VMs de la DMZ:** servidor web (`10.0.0.10`), única VM amb exposició controlada cap a l’exterior.
- **Floating IP del clúster (`172.16.0.4`):** adreça flotant per accedir a la gestió del clúster independentment de quin node estigui actiu en cada moment.

# Proves i Validació

En aquest apartat hem fet diferents proves controlades per comprovar que la monitorització configurada a Zabbix funciona correctament. La idea ha estat provocar errors reals dins de l'entorn Hefesto i validar que Zabbix els detecta, els mostra al dashboard i genera els avisos corresponents.

Les proves principals que hem fet han estat sobre el servidor web, el clúster de Proxmox i alguns llindars de rendiment, com l'ús elevat de CPU d'una màquina virtual.

## Estat inicial abans de la caiguda

Aquí podem veure com tot es mostra com actiu tenim uns usos de CPU i RAM normals per lo que tenim en marxa i també que estem rebent correctament els logs de `PfSense` i `Suricata` correctament.

![proves 1](<../imatges/07/proves (1).png>)

---

## Prova de caiguda d'un node Proxmox

Per validar la monitorització del clúster, hem fet una prova apagant el node proxmox2 des de la interfície de Proxmox. Aquesta prova és útil perquè permet comprovar si Zabbix és capaç de detectar la pèrdua d'un node dins del clúster.

![proves 2](<../imatges/07/proves (2).png>)
![proves 3](<../imatges/07/proves (3).png>)

Un cop apagat el node, la interfície de Proxmox mostra proxmox2 amb una icona vermella, indicant que el node ja no està disponible dins del clúster. Els altres nodes, proxmox i proxmox3, continuen funcionant correctament.
També apareix un avís de severitat alta indicant que el node proxmox2 està offline. Això demostra que la integració de Proxmox amb Zabbix funciona correctament i que el sistema és capaç de detectar incidències importants dins del clúster.

![proves 4](<../imatges/07/proves (4).png>)

## Validació d'avisos i llindars de rendiment

Finalment, també hem validat que Zabbix genera avisos quan algun recurs supera els llindars configurats. En aquesta captura es pot veure un avís relacionat amb una màquina virtual de Proxmox, concretament la VM web, que la em forçat per superar el 90% d'ús de CPU.

A més, al mateix panell també es continuen veient altres avisos del clúster, com el node proxmox2 offline i reinicis recents de màquines virtuals. Això ens permet centralitzar en una sola vista els problemes més importants de l'entorn.

![proves 5](<../imatges/07/proves (5).png>)

---

## Prova de caiguda del servei web

Per comprovar tota la monitorització de la web em fet una prova aturant manualment el servei `apache2` del servidor web. Aquesta prova ens serveix per comprovar que els items i triggers creats per controlar l'estat de la web funcionen correctament.

En aquest cas, des del servidor web hem executat la comanda per aturar Apache:

```bash
sudo systemctl stop apache2.service

```

Després d'aturar el servei, Zabbix detecta que la web ja no respon correctament. Al dashboard es pot veure que l'estat del servei web passa a valor 0, indicant que la web està caiguda. També apareixen avisos relacionats amb Apache, la resposta HTTP i la salut general de la web.

![proves 6](<../imatges/07/proves (6).png>)

---

## Conclusió de les proves

Amb aquestes proves hem comprovat que la monitorització configurada és funcional i útil per detectar problemes reals dins de l'entorn Hefesto. Zabbix ha estat capaç de detectar la caiguda del servei web, la recuperació posterior, l'apagada d'un node Proxmox i l'ús elevat de CPU d'una màquina virtual.

Això confirma que els dashboards, triggers i items configurats no només mostren informació visual, sinó que també permeten reaccionar davant incidències reals de la infraestructura.
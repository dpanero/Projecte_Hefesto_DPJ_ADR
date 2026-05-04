# Disseny de Polítiques de Monitorització

En aquest apartat configurarem les polítiques de monitorització dels diferents hosts en funció del dispositiu que són, ho treballarem a través del que ens han generat cada plantilla de les que em fet servir.
Anirem revisant un a un els 3 hosts, el PfSense i Suricata els rebem a través de rsyslog pel host Zabbix i per tant ja son items d'aquest.

Primerament s'han establert els grups de hosts per tal de tenir separats els diferents tipus de dispositiu, en aquest cas només tenim un host de cada tipus.

![Host Groups](<../imatges/04/1- hostgroups.png>)

També hem activat el host discover per tal de fer autodescubriment de les maquines de la LAN, indiquem el rang de xarxa i un temps d'autodescubriment.

![Discover 1](<../imatges/04/2- discover (1).png>)

Ja tindriem la regla generada i aplicada.

![Discover 2](<../imatges/04/2- discover (2).png>)

## Cluster Proxmox

Lo primer que m'he trobat al cluster es que detecta com problemes que les plantilles o maquines virtuals estiguin apagades lo qual tampoc es correcte sempre, modifiquem els triggers (alertes) per tal de fer que només doni com informació el fet de que estan parades i els reinicis pero sense donar alerta greu, les greus només saltaran si algún dels nodes cau, després mostrarem com fem un dashboard visual per mostrar la informació comodament.

![Trigger Proxmox 1](<../imatges/04/3- triggers proxmox (1).png>)
![Trigger Proxmox 2](<../imatges/04/3- triggers proxmox (2).png>)

Ens assegurem que dels nodes si que reporti triggers si no estan actius, es important que només tinguem triggers importants ja que si rebem moltes alertes innecesaries perdrem control sobre aquestes.

![Trigger Proxmox 3](<../imatges/04/3- triggers proxmox (3).png>)

Ara podem veure com només rebem informacions de que s'han reiniciat maquines virtuals recentment pero son informatives i que podem acceptar o apagar més endavant en cas que ens molesti molt, de moment com que reiniciar VMs no es una cosa frequent esta bé rebre un missatge al respecte.

![Trigger Proxmox 4](<../imatges/04/3- triggers proxmox (4).png>)

## NAS

En el cas de les politiques per als dispositius NAS em pensat que ens interesaria saber del nostre NAS, de primeres apagarem els triggers de temperatura ja que al ser un NAS virtual rebem informació erronea.

![Trigger NAS 1](<../imatges/04/4- triggers nas (1).png>)


Depsrés d'aixó em deixat una alerta per que ens avissi si va malament de CPU ja que va una mica just en algunes ocasions.

![Trigger NAS 2](<../imatges/04/4- triggers nas (2).png>)

També verifiquem que els items de la template reben dades de la pool de discs i l'espai total d'aquesta, al tenir també el pool local del proxmox també el mostra pero principalment voldrem monitoritzar l'altre que es el que conté les VMs.

![Trigger NAS 3](<../imatges/04/4- triggers nas (3).png>)

Finalment confirmem que rebem les alertes, en aquest cas el llindar de memoria que em deixat actiu mostra que el NAS ha estat a més del 90% durant més de 5 minuts, aixó ens genera un avis greu.

![Trigger NAS 4](<../imatges/04/4- triggers nas (4).png>)

## Propi servidor Zabbix

Aquest no cal canviar res ja que ve configurat correctament de manera automatica al instal·lar-se, el canvi que nosaltres em fet es la configuració dels logs pfsense que em mostrat anteriorment.

![Rsyslog 6.2](<../imatges/03/4- rsyslog (6).png>)
![Rsyslog 8.2](<../imatges/03/4- rsyslog (8).png>)
# DIAGRAMA XARXA LABORATORI

Per realitzar tots els microprojectes em creat un entorn de laboratori simulant una xarxa real, tota la infraestructura esta situada dins un servidor fisic dins la xarxa domestica de casa.

La idea a estat aillar la xarxa domestica i només passar els paquets cap al nostre firewall pfsense per gestionar desde aquí tota la xarxa LAN i DMZ.
D'aquesta manera tot lo que arribi al nostre nom de domini asociat a la IP pública gracies al registre DDNS fem que passi al pfsense amb un port forwarding i després el pfsense gestiona que fer amb els seus forwardings interns i regles de firewall creades, per tal de poder obrir serveis de la DMZ a internet.

Esquema total de la xarxa:
<p align="center">
<img src="imatges/xarxa_hefesto_diagrama.png" width="300">
</p>

---
# DDNS I NOMINALIA

Em adquirit un domini a nominalia "hefesto.digital" per tal de poder obrir serveis a internet i poder simular una xarxa real de manera més completa, per fer-ho a nominalia em creat diversos registres DNS de tipus CNAME com per exemple vpn.hefesto.digital que estan apuntant a la nostre IP publica gracies a un DDNS.
Dins del propi pfsense decideix que fer amb els determinats CNAMEs gracies a un reverse proxy.

D'aquesta manera mantenim la infraestructura segura ja que només es accesible desde fora els ports que nosaltres decidim obrir al firewall (de la DMZ), i encara amb aixó només poden arribar a una maquina concreta de la xarxa.

---

# Open VPN

Per tal de poder treballar de manera remota en la xarxa em creat al pfsense una OpenVPN amb varis certificats que ens permeten connectar amb el pfsense desde el qual podem definir diverses regles.
En el nostra cas em permés:

- OpenVPN --> any (LAN)
- OpenVPN --> any (DMZ)

D'aquesta manera podem administrar tota la xarxa, només amb els nostres certificats de VPN comodament desde els nostres Windows host.

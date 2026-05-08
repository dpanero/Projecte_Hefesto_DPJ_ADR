# Virtualització amb Proxmox

[Tornar al inici de Hefesto](/README.md)

<p align="center">
<img src="imatges/logo_proxmox.png" width="200">
</p>

Aquest projecte consisteix en la creació d'un entorn virtual utilitzant la plataforma Proxmox.

---

## Índex de documents del projecte

| Apartat | Document |
|---|---|
| Preparació de l'Entorn Base (VirtualBox) | [03_Preparacio_de_lEntorn_Base_VirtualBox.pdf](./03_Preparacio_de_lEntorn_Base_VirtualBox.pdf) |
| Instal·lació de Proxmox VE | [04_Installacio_de_Proxmox_VE.pdf](./04_Installacio_de_Proxmox_VE.pdf) |
| Configuració de Xarxa al Clúster | [05_Configuracio_de_Xarxa_al_Cluster.pdf](./05_Configuracio_de_Xarxa_al_Cluster.pdf) |
| Implementació d'Emmagatzematge | [06_Implementacio_dEmmagatzematge.pdf](./06_Implementacio_dEmmagatzematge.pdf) |
| Desplegament de Màquines Virtuals | [07_Desplegament_de_Maquines_Virtuals.pdf](./07_Desplegament_de_Maquines_Virtuals.pdf) |
| Configuració de Recursos | [08_Configuracio_de_Recursos.pdf](./08_Configuracio_de_Recursos.pdf) |
| Conclusions del Projecte Proxmox | [09_Conclusions_Projecte_Proxmox.pdf](./09_Conclusions_Projecte_Proxmox.pdf) |
| Integració i Optimització | [10_Integracio_i_Optimitzacio.pdf](./10_Integracio_i_Optimitzacio.pdf) |
| Optimització del Rendiment | [11_Optimitzacio_del_Rendiment.pdf](./11_Optimitzacio_del_Rendiment.pdf) |
| Conclusions del Projecte Proxmox avançat | [12_Conclusions_Projecte_Proxmox_avancat.pdf](./12_Conclusions_Projecte_Proxmox_avancat.pdf) |

---

# Tecnologies utilitzades

- Proxmox VE
- Virtualització
- Xarxes virtuals
- Emmagatzematge virtual

---

# Objectiu del projecte

Crear una infraestructura virtual capaç de:

- allotjar múltiples màquines virtuals
- gestionar recursos de CPU, RAM i disc
- configurar xarxes virtuals
- desplegar serveis dins del laboratori

## 1. Introducció

### 1.1 Què és la virtualització

Els entorns de virtualització són tecnologies que permeten crear múltiples màquines simulades, conegudes com a màquines virtuals (VMs), sobre un únic equip de hardware físic. En lloc de dedicar un servidor sencer a un sol sistema operatiu, un programari especialitzat anomenat hipervisor divideix els recursos físics reals — processador, memòria RAM, emmagatzematge i xarxa — i els reparteix de manera aïllada entre els diferents entorns virtuals.

Gràcies a aquest model, és possible executar Windows, distribucions de Linux i altres sistemes operatius de manera simultània en la mateixa màquina física, mantenint cada un d’ells completament aïllat dels altres. Aquesta tecnologia optimitza enormement l’ús del hardware, redueix els costos d’energia, estalvia espai físic als data centers i facilita tasques crítiques com la creació de còpies de seguretat, la migració de sistemes en calent i el desplegament ràpid de nous serveis a partir de plantilles.

En l’entorn empresarial actual, la virtualització és la base sobre la qual es construeixen pràctiques modernes com la consolidació de servidors, la infraestructura com a codi, els entorns de proves reproduïbles i, en última instància, el núvol privat. Per això és una competència fonamental per a qualsevol administrador de sistemes.

### 1.2 Plataformes de virtualització existents

Al mercat existeixen diverses plataformes amb enfocaments diferents segons el cas d’ús i el pressupost:

- **VMware ESXi:** solució empresarial de codi tancat altament reconeguda per la seva robustesa i estabilitat. És un estàndard a les grans corporacions, però requereix llicències costoses per accedir a les funcions avançades.
- **Microsoft Hyper-V:** alternativa nativa de Microsoft, ideal per a empreses que ja tenen tota la seva infraestructura basada en Windows Server, ja que ofereix una integració perfecta. La contrapartida és la dependència total de l’ecosistema propietari de Microsoft.
- **IsardVDI:** solució de codi obert enfocada principalment a la virtualització d’escriptoris al núvol (VDI). Destaca per ser extremadament fàcil d’utilitzar per als usuaris finals en escoles o institucions, però no incorpora les eines avançades necessàries per administrar centres de dades complexos.
- **Proxmox VE:** entorn de virtualització de codi obert basat en Linux que permet gestionar tant màquines virtuals tradicionals (KVM) com contenidors lleugers (LXC) des d’una única interfície web. Destaca per la seva flexibilitat i per oferir característiques de nivell empresarial sense cost de llicència.

### 1.3 Taula comparativa

| Plataforma | Llicència | Punt fort | Desavantatge principal |
|---|---|---|---|
| VMware ESXi | Propietària (comercial) | Entorns corporatius crítics i màxima estabilitat. | Cost molt elevat de les llicències i renovacions. |
| Proxmox VE | Codi obert (gratuïta) | Gestió centralitzada de servidors virtuals i contenidors. | Corba d’aprenentatge tècnica i menys suport comercial. |
| Microsoft Hyper-V | Propietària | Integració nativa amb entorns Windows Server. | Dependència total de l’ecosistema de Microsoft. |
| IsardVDI | Codi obert (gratuïta) | Senzillesa màxima per a virtualització d’escriptoris. | Opcions limitades per a infraestructures complexes. |

### 1.4 Plataforma escollida i justificació

Per a aquest projecte, la plataforma escollida és Proxmox VE. El motiu principal d’aquesta decisió és la seva naturalesa de codi obert, que permet gaudir d’un entorn de nivell empresarial amb característiques avançades de xarxa, emmagatzematge, alta disponibilitat i còpies de seguretat sense els alts costos de llicenciament que exigeixen ESXi o Hyper-V.

A diferència d’IsardVDI, que està més limitat a entorns d’escriptori, Proxmox ofereix un control tècnic total i profund sobre la infraestructura del servidor, fet que el fa ideal per a un projecte tècnic de desplegament complet com el nostre. Com a avantatge addicional, Proxmox unifica la virtualització completa (KVM) i la basada en contenidors (LXC) en una mateixa interfície, optimitzant els recursos al màxim i permetent triar la millor tecnologia per a cada càrrega.

El principal desavantatge respecte a gegants com VMware o Microsoft és que disposa de menys suport tècnic oficial per part de fabricants de programari de tercers i requereix que l’administrador tingui coneixements sòlids de Linux per resoldre problemes complexos. Tanmateix, per a un entorn educatiu i de pràctiques aquest punt és més una oportunitat d’aprenentatge que no pas un obstacle.

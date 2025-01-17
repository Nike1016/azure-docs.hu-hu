---
title: Az Azure Virtual Machines magas rendelkezésre állása az SAP NetWeaver-on SUSE Linux Enterprise Server a Azure NetApp Files-mel | Microsoft Docs
description: Magas rendelkezésre állási útmutató az SAP NetWeaver-hez a SUSE Linux Enterprise Server Azure NetApp Files SAP-alkalmazásokhoz
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/30/2019
ms.author: radeltch
ms.openlocfilehash: c8fcf4afa5a363d355f627be95dd7fe8131203ac
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "67797970"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-with-azure-netapp-files-for-sap-applications"></a>Magas rendelkezésre állás az Azure-beli virtuális gépeken futó SAP NetWeaver-hez SUSE Linux Enterprise Serveron Azure NetApp Files SAP-alkalmazásokhoz

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]: https://launchpad.support.sap.com/#/notes/2205917
[1944799]: https://launchpad.support.sap.com/#/notes/1944799
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

Ez a cikk leírja, hogyan telepítheti a virtuális gépeket, konfigurálhatja a virtuális gépeket, telepítheti a fürtöt, és telepítheti a magasan elérhető SAP NetWeaver 7,50-rendszereket [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)használatával.
A példában a konfigurációk, a telepítési parancsok stb., a ASCS-példány a 00-as szám, az ERS-példány száma 01, az elsődleges alkalmazás példánya (PAS) 02, az Application instance (AAS) pedig 03. A rendszer az SAP rendszerazonosító QAS használja. 

Ez a cikk azt ismerteti, hogyan lehet magas rendelkezésre állást elérni az SAP NetWeaver alkalmazáshoz a Azure NetApp Files használatával. Ebben a cikkben nem szerepel részletesen az adatbázis rétege.

Először olvassa el a következő SAP-megjegyzéseket és dokumentumokat:

* [Azure NetApp Files dokumentáció][anf-azure-doc] 
* SAP-Megjegyzés [1928533], amely a következőket tartalmazta:  
  * Az SAP-szoftverek üzembe helyezéséhez támogatott Azure-beli virtuálisgép-méretek listája
  * Fontos kapacitási információk Azure-beli virtuális gépek méreteihez
  * Támogatott SAP-szoftverek és operációs rendszerek (OS) és adatbázis-kombinációk
  * A Windows és a Linux rendszerhez szükséges SAP kernel verziója Microsoft Azure
* Az SAP Note [2015553] az SAP által támogatott SAP-szoftverek Azure-beli üzembe helyezésének előfeltételeit sorolja fel.
* Az SAP Megjegyzés [2205917] ajánlott operációsrendszer-beállításokkal SUSE Linux Enterprise Server SAP-alkalmazásokhoz
* A [1944799] -es SAP-Megjegyzés SAP HANA irányelvek az SAP-alkalmazásokhoz SUSE Linux Enterprise Server
* Az [2178632] -es SAP-Megjegyzés részletes információkat tartalmaz az Azure-beli SAP-ban jelentett összes figyelési mérőszámról.
* A [2191498] -es SAP-Megjegyzés a szükséges SAP-gazdagép ügynökének verziója az Azure-ban linuxos.
* Az [2243692] -es SAP-Megjegyzés az Azure-beli Linuxon futó SAP-licenceléssel kapcsolatos információkat tartalmaz.
* Az [1984787] -es SAP-Megjegyzés általános információkat tartalmaz a SUSE Linux Enterprise Server 12.
* Az SAP Megjegyzés [1999351] további hibaelhárítási információkat tartalmaz az SAP-hez készült Azure Enhanced monitoring bővítménnyel kapcsolatban.
* Az [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) rendelkezik minden szükséges SAP-megjegyzéssel a Linux rendszerhez.
* [Azure Virtual Machines tervezése és implementálása Linux rendszeren az SAP-ban][planning-guide]
* [Azure Virtual Machines üzembe helyezés az SAP-hez Linux rendszeren][deployment-guide]
* [Azure Virtual Machines adatbázis-kezelői telepítés az SAP-hez Linux rendszeren][dbms-guide]
* [SUSE SAP ha ajánlott eljárási útmutatók][suse-ha-guide] Az útmutatók az összes szükséges információt tartalmazzák, amelyekkel beállítható a NetWeaver HA és SAP HANA rendszer-replikáció a helyszínen. Ezeket az útmutatókat általános alaptervként használhatja. Sokkal részletesebb információkat biztosítanak.
* [SUSE magas rendelkezésre állású bővítmény – 12 SP3 kibocsátási megjegyzések][suse-ha-12sp3-relnotes]
* [NetApp SAP-alkalmazások Microsoft Azure a Azure NetApp Files használatával][anf-sap-applications-azure]

## <a name="overview"></a>Áttekintés

Az SAP NetWeaver Central Services magas rendelkezésre állása (HA) megosztott tárterületet igényel.
Annak érdekében, hogy a SUSE Linuxon eddig is szükség volt egy különálló, nagy rendelkezésre állású NFS-fürt létrehozására. 

Most már lehetséges, hogy az SAP NetWeaver HA-t megosztott tároló használatával, Azure NetApp Fileson helyezi el. A megosztott tárolóhoz Azure NetApp Files használata nem igényel további NFS- [fürtöt](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs). Az SAP NetWeaver Central Services (ASCS/SCS) esetében továbbra is a pacemaker szükséges.


![SAP NetWeaver – magas rendelkezésre állás – áttekintés](./media/high-availability-guide-suse-anf/high-availability-guide-suse-anf.PNG)

Az SAP NetWeaver ASCS, az SAP NetWeaver SCS, az SAP NetWeaver ERS és a SAP HANA adatbázis virtuális gazdagépeket és virtuális IP-címeket használ. Az Azure-ban a virtuális IP-címek használatához [terheléselosztó](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) szükséges. Az alábbi lista az (A) SCS és az ERS Load Balancer konfigurációját mutatja be.

> [!IMPORTANT]
> Az SAP ASCS/ERS multi-SID fürtszolgáltatása az Azure-beli virtuális gépeken található vendég operációs rendszerként a SUSE Linux rendszerben **nem támogatott**. A többszörös SID-fürtszolgáltatás több SAP ASCS/ERS példány telepítését ismerteti különböző SID-kiszolgálókkal egy pacemaker-fürtben


### <a name="ascs"></a>(A)SCS

* Előtér-konfiguráció
  * IP-10.1.1.20
* Háttér-konfiguráció
  * Az (A) SCS/ERS-fürt részét képező összes virtuális gép elsődleges hálózati adapteréhez csatlakozik
* Mintavételi port
  * Port 620<strong>&lt;Nr&gt;</strong>
* Terheléselosztási szabályok
  * <strong>32&lt;nr&gt;</strong>  TCP
  * <strong>36&lt;nr&gt;</strong>  TCP
  * <strong>39&lt;nr&gt;</strong>  TCP
  * <strong>81&lt;nr&gt;</strong>  TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

### <a name="ers"></a>ERS

* Előtér-konfiguráció
  * IP-10.1.1.21
* Háttér-konfiguráció
  * Az (A) SCS/ERS-fürt részét képező összes virtuális gép elsődleges hálózati adapteréhez csatlakozik
* Mintavételi port
  * Port 621<strong>&lt;Nr&gt;</strong>
* Terheléselosztási szabályok
  * <strong>32&lt;nr&gt;</strong>  TCP
  * <strong>33&lt;nr&gt;</strong>  TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>A Azure NetApp Files-infrastruktúra beállítása 

Az SAP NetWeaver megosztott tárterületet igényel az átvitelhez és a profilhoz.  Mielőtt folytatná az Azure NetApp-fájlok infrastruktúrájának telepítését, ismerkedjen meg az [Azure NetApp Files dokumentációval][anf-azure-doc]. Ellenőrizze, hogy a kiválasztott Azure-régió kínál-e Azure NetApp Files. A következő hivatkozás az Azure-régió Azure NetApp Files rendelkezésre állását jeleníti meg: [Az Azure-régió Azure NetApp Files rendelkezésre állását][anf-avail-matrix].

Az Azure NetApp-fájlok több [Azure-régióban](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)is elérhetők. Azure NetApp Files üzembe helyezése előtt a bevezetést a Azure NetApp Filesra kell kérnie, az [Azure NetApp-fájlok regisztrálásával kapcsolatos utasításokat][anf-register]követve. 

### <a name="deploy-azure-netapp-files-resources"></a>Azure NetApp Files erőforrások üzembe helyezése  

A lépések azt feltételezik, hogy már telepítette az [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)-t. A Azure NetApp Files erőforrásokat és a virtuális gépeket, ahol a Azure NetApp Files erőforrásokat csatlakoztatni kell ugyanazon az Azure-Virtual Network vagy az Azure-beli virtuális hálózatokban.  

1. Ha eddig még nem tette meg, kérje [a Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)bevezetését.  

2. Hozza létre a NetApp-fiókot a kiválasztott Azure-régióban, és kövesse a [NetApp-fiók létrehozásához szükséges utasításokat](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account).  
3. Azure NetApp Files kapacitás-készlet beállítása a [Azure NetApp Files kapacitás készletének beállításához szükséges útmutatás alapján](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool).  
A jelen cikkben bemutatott SAP NetWeaver architektúra egy Azure NetApp Files kapacitási készletet, prémium SKU-t használ. Javasoljuk, hogy az Azure-ban az SAP NetWeaver alkalmazás számítási feladataihoz Azure NetApp Files Premium SKU-t.  

4. Az alhálózat delegálása az Azure NetApp-fájlokba az [alhálózat delegálása Azure NetApp Filesra](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)című részben leírtak szerint.  

5. Azure NetApp Files kötetek telepítéséhez kövesse az [utasításokat, amelyekkel Azure NetApp Files kötetet hozhat létre](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes). Telepítse a köteteket a kijelölt Azure NetApp Files [](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)alhálózatban. Ne feledje, hogy a Azure NetApp Files erőforrásoknak és az Azure-beli virtuális gépeknek ugyanabban az Azure-Virtual Networkban vagy az Azure-beli virtuális hálózatokban kell lenniük. Például a sapmnt<b>QAS</b>, a usrsap<b>QAS</b>stb. a kötetek nevei és sapmnt QAS<b></b>, usrsap<b>QAS</b>stb. a filepaths a Azure NetApp Files kötetek esetében.  

   1. kötet sapmnt<b>QAS</b> (NFS://10.1.0.4/sapmnt<b>QAS</b>)
   2. kötet usrsap<b>QAS</b> (NFS://10.1.0.4/usrsap<b>QAS</b>)
   3. kötet usrsap<b>QAS</b>(NFS://10.1.0.5/usrsap<b>QAS</b>sys)
   4. kötet usrsap<b>QAS</b>(NFS://10.1.0.4/usrsap<b>QAS</b>ERS)
   5. kötet Trans (nfs://10.1.0.4/trans)
   6. kötet usrsap<b>QAS</b>pas (NFS://10.1.0.5/usrsap<b>QAS</b>Pas)
   7. Volume usrsap<b>QAS</b>aas (NFS://10.1.0.4/usrsap<b>QAS</b>AAS)
   
Ebben a példában az összes SAP NetWeaver fájlrendszer Azure NetApp Files használták, hogy bemutassa, hogyan használható a Azure NetApp Files. Az NFS-en keresztül nem szükséges SAP-fájlrendszerek is üzembe helyezhetők az [Azure Disk Storage](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) szolgáltatásban. Ebben a példában az <b>a-e</b> Azure NetApp Files és <b>f-g</b> (azaz/usr/SAP/<b>QAS</b>/d<b>02</b>,/usr/SAP/<b>QAS</b>/d<b>03</b>) telepíthető Azure Disk Storage-ként. 

### <a name="important-considerations"></a>Fontos szempontok

Az SAP NetWeaver SUSE magas rendelkezésre állású architektúrán való Azure NetApp Filesának megfontolásakor vegye figyelembe a következő fontos szempontokat:

- A minimális kapacitási készlet 4 TiB. A kapacitási készlet méretének 4 TiB-nál több többszörösének kell lennie.
- A minimális kötet 100 GiB
- Azure NetApp Files és az összes olyan virtuális gép, amelyben Azure NetApp Files köteteket csatlakoztatni kell, ugyanabban az Azure-Virtual Network vagy egymással azonos régióban lévő [virtuális hálózatokban](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) kell lennie. A VNET-társítások ugyanazon régióban való elérésének Azure NetApp Files jelenleg támogatott. Az Azure NetApp a globális társon keresztüli hozzáférése még nem támogatott.
- A kiválasztott virtuális hálózatnak rendelkeznie kell egy, a Azure NetApp Files delegált alhálózattal.
- Azure NetApp Files jelenleg csak a NFSv3 támogatja 
- Azure NetApp Files az [exportálási szabályzatot](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy): szabályozhatja az engedélyezett ügyfeleket, a hozzáférési típust (olvasási & írás, csak olvasható stb.). 
- Azure NetApp Files a szolgáltatás még nem ismeri a zónát. Jelenleg Azure NetApp Files funkció nincs telepítve az Azure-régió összes rendelkezésre állási zónájában. Vegye figyelembe, hogy egyes Azure-régiókban lehetséges a késés következményei. 

## <a name="deploy-linux-vms-manually-via-azure-portal"></a>Linux rendszerű virtuális gépek manuális üzembe helyezése Azure Portal használatával

Először létre kell hoznia a Azure NetApp Files köteteket. Telepítse a virtuális gépeket. Ezt követően hozzon létre egy terheléselosztó-t, és használja a virtuális gépeket a háttér-készletekben.

1. Erőforráscsoport létrehozása
1. Virtual Network létrehozása
1. Rendelkezésre állási csoport létrehozása a ASCS  
   Maximális frissítési tartomány beállítása
1. 1\. virtuális gép létrehozása  
   Legalább 12 SP3 SLES4SAP, ebben a példában a SLES4SAP 12 SP3 rendszerképet használja a rendszer  
   Válassza ki a korábban létrehozott rendelkezésre állási készletet a ASCS  
1. 2\. virtuális gép létrehozása  
   Legalább 12 SP3 SLES4SAP, ebben a példában a SLES4SAP 12 SP3 rendszerképet használja a rendszer  
   Válassza ki a korábban létrehozott rendelkezésre állási készletet a ASCS  
1. Rendelkezésre állási csoport létrehozása az SAP-alkalmazás példányaihoz (PAS, AAS)    
   Maximális frissítési tartomány beállítása
1. 3\. virtuális gép létrehozása  
   Legalább 12 SP3 SLES4SAP, ebben a példában a SLES4SAP 12 SP3 rendszerképet használja a rendszer  
   Válassza ki a korábban létrehozott rendelkezésre állási készletet a PAS/AAS számára   
1. 4\. virtuális gép létrehozása  
   Legalább 12 SP3 SLES4SAP, ebben a példában a SLES4SAP 12 SP3 rendszerképet használja a rendszer  
   Válassza ki a korábban létrehozott rendelkezésre állási készletet a PAS/AAS számára  

## <a name="setting-up-ascs"></a>Az (A) SCS beállítása

Ebben a példában az erőforrásokat manuálisan telepítették a [Azure Portal](https://portal.azure.com/#home) használatával.

### <a name="deploy-azure-load-balancer-manually-via-azure-portal"></a>Azure Load Balancer manuális üzembe helyezése Azure Portal használatával

Először létre kell hoznia a Azure NetApp Files köteteket. Telepítse a virtuális gépeket. Ezt követően hozzon létre egy terheléselosztó-t, és használja a virtuális gépeket a háttér-készletekben.

1. Load Balancer létrehozása (belső)  
   1. Az előtérbeli IP-címek létrehozása
      1. A ASCS IP-10.1.1.20
         1. Nyissa meg a terheléselosztó-t, válassza a előtéri IP-készlet lehetőséget, majd kattintson a Hozzáadás gombra.
         1. Adja meg az új előtér-IP-készlet nevét (például a **frontend. QAS. ASCS**)
         1. Állítsa a hozzárendelést statikus értékre, és adja meg az IP-címet (például **10.1.1.20**).
         1. Kattintson az OK gombra
      1. A ASCS IP-10.1.1.21
         * Ismételje meg a fenti lépéseket az "a" alatt, és hozzon létre egy IP-címet az ERS számára (például **10.1.1.21** és **frontend. QAS. ERS**)
   1. A háttér-készletek létrehozása
      1. Háttérbeli készlet létrehozása a ASCS
         1. Nyissa meg a Load balancert, válassza a háttérbeli készletek elemet, majd kattintson a Hozzáadás gombra.
         1. Adja meg az új háttér-készlet nevét (például **háttér. QAS**)
         1. Kattintson a virtuális gép hozzáadása elemre.
         1. Válassza ki a ASCS korábban létrehozott rendelkezésre állási készletet 
         1. Válassza ki az (A) SCS-fürthöz tartozó virtuális gépeket.
         1. Kattintson az OK gombra
   1. Az állapot-mintavételek létrehozása
      1. A ASCS 620**00** portja
         1. Nyissa meg a terheléselosztó-t, válassza az állapot-tesztek elemet, majd kattintson a Hozzáadás gombra.
         1. Adja meg az új állapotadatok (például az **állapot) nevét. QAS. ASCS**)
         1. Válassza a TCP protokollt, a 620**00**portot, az 5. időközt és a nem megfelelő állapotú küszöbértéket 2
         1. Kattintson az OK gombra
      1. Port 621**01** ASCS-eseknél
            * A "c" alatt a fenti lépések megismétlésével hozzon létre egy állapot-mintavételt a ERS számára (például 621**01** és **Health. QAS. ERS**)
   1. Terheléselosztási szabályok
      1. 32**00** TCP a ASCS
         1. Nyissa meg a Load balancert, válassza a terheléselosztási szabályok elemet, majd kattintson a Hozzáadás gombra.
         1. Adja meg az új terheléselosztó szabály nevét (például **LB. QAS. ASCS. 3200**)
         1. Válassza ki a korábban létrehozott ASCS, háttér-készlet és állapot-mintavételi felület IP-címét (például a **frontendet). QAS. ASCS**)
         1. Tartsa meg a protokoll **TCP**-t, írja be a **3200** portot
         1. Üresjárati időkorlát 30 percre növelve
         1. **Ügyeljen arra, hogy a lebegő IP-címet engedélyezze**
         1. Kattintson az OK gombra
      1. További portok a ASCS
         * Ismételje meg a fenti lépéseket a "d" alatt a 36**00**, 39**00**, 81**00**, 5**00**13, 5**00**14, 5**00**16 és TCP ASCS
      1. További portok a ASCS-ESEK számára
         * Ismételje meg a fenti lépéseket a "d" alatt a 33**01**, 5**01**13, 5**01**14, 5 01 16 és TCP ASCS-eseknél.

> [!IMPORTANT]
> Ne engedélyezze a TCP-időbélyegeket a Azure Load Balancer mögött elhelyezett Azure-beli virtuális gépeken. A TCP-időbélyegek engedélyezése az állapot-mintavételek meghibásodását eredményezi. Állítsa a **net. IPv4. TCP** paramétert **0-ra**_timestamps. Részletekért lásd: [Load Balancer Health](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)-tesztek.

### <a name="create-pacemaker-cluster"></a>Pacemaker-fürt létrehozása

Kövesse a [pacemaker beállítása SUSE Linux Enterprise Server az Azure-ban](high-availability-guide-suse-pacemaker.md) című témakör lépéseit egy alapszintű pacemaker-fürt létrehozásához ehhez A (a) SCS-kiszolgálóhoz.

### <a name="installation"></a>Telepítés

A következő elemek van fűzve előtagként vagy **[A]** – az összes csomópont alkalmazandó **[1]** – 1. csomópont csak érvényes vagy **: [2]** – 2. csomópont csak érvényes.

1. **[A]** SUSE-összekötő telepítése

   <pre><code>sudo zypper install sap-suse-cluster-connector
   </code></pre>

   > [!NOTE]
   > A fürtcsomópontok állomásneve ne használjon kötőjeleket. Ellenkező esetben a fürt nem fog működni. Ez egy ismert korlátozás, és a SUSE a javításon dolgozik. A javítás az SAP-SUSE-Cloud-Connector csomag javításának részeként jelenik meg.

   Győződjön meg arról, hogy az SAP SUSE-fürt összekötő új verzióját telepítette. A régit sap_suse_cluster_connector hívták, az újat pedig **SAP-SUSE-cluster-Connector**néven.

   <pre><code>sudo zypper info sap-suse-cluster-connector
   
      Information for package sap-suse-cluster-connector:
   ---------------------------------------------------
   Repository     : SLE-12-SP3-SAP-Updates
   Name           : sap-suse-cluster-connector
   Version        : 3.1.0-8.1
   Arch           : noarch
   Vendor         : SUSE LLC &lt;https://www.suse.com/&gt;
   Support Level  : Level 3
   Installed Size : 45.6 KiB
   Installed      : Yes
   Status         : up-to-date
   Source package : sap-suse-cluster-connector-3.1.0-8.1.src
   Summary        : SUSE High Availability Setup for SAP Products
   </code></pre>

2. **[A]** SAP-erőforrás-ügynökök frissítése  
   
   Az ebben a cikkben ismertetett új konfiguráció használatához az erőforrás-ügynökök csomagra vonatkozó javításra van szükség. A következő paranccsal ellenőrizhető, hogy a javítás már telepítve van-e

   <pre><code>sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
   </code></pre>

   A kimenetnek a következőhöz hasonlónak kell lennie

   <pre><code>&lt;parameter name="IS_ERS" unique="0" required="0"&gt;
   </code></pre>

   Ha a GREP-parancs nem találja a IS_ERS paramétert, telepítenie kell a [SUSE letöltési oldalán](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents) található javítást.

   <pre><code># example for patch for SLES 12 SP1
   sudo zypper in -t patch SUSE-SLE-HA-12-SP1-2017-885=1
   # example for patch for SLES 12 SP2
   sudo zypper in -t patch SUSE-SLE-HA-12-SP2-2017-886=1
   </code></pre>

3. **[A]**  Állomásnév-feloldás beállítása

   DNS-kiszolgálót használjon, vagy módosítsa a Hosts az összes csomópontra. Ez a példa bemutatja, hogyan használhatja a Hosts fájlt.
   Cserélje le az IP-címet és a gazdagépet a következő parancsokra

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Helyezze be a következő sorokat Hosts. Módosítsa az IP-cím és a környezet megfelelő állomásnév   

   <pre><code>
   # IP address of cluster node 1
   <b>10.1.1.18    anftstsapcl1</b>
   # IP address of cluster node 2
   <b>10.1.1.6     anftstsapcl2</b>
   # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
   <b>10.1.1.20    anftstsapvh</b>
   # IP address of the load balancer frontend configuration for SAP Netweaver ERS
   <b>10.1.1.21    anftstsapers</b>
   </code></pre>

## <a name="prepare-for-sap-netweaver-installation"></a>Felkészülés az SAP NetWeaver telepítésére

1. **[A]** a megosztott könyvtárak létrehozása

   <pre><code>sudo mkdir -p /sapmnt/<b>QAS</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>QAS</b>/SYS
   sudo mkdir -p /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>QAS</b>/ERS<b>01</b>
   
   sudo chattr +i /sapmnt/<b>QAS</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>QAS</b>/SYS
   sudo chattr +i /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/ERS<b>01</b>
   </code></pre>

2. **[A]** AutoFS konfigurálása

   <pre><code>
   sudo vi /etc/auto.master
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Hozzon létre egy fájlt

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync 10.1.0.4:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync 10.1.0.4:/trans
   /usr/sap/<b>QAS</b>/SYS -nfsvers=3,nobind,sync 10.1.0.5:/usrsap<b>qas</b>sys
   </code></pre>
   
   > [!NOTE]
   > Jelenleg Azure NetApp Files csak a NFSv3 támogatja. Ne hagyja ki a nfsvers = 3 kapcsolót.
   
   Az `autofs` új megosztások csatlakoztatásának újraindítása
    <pre><code>
      sudo systemctl enable autofs
      sudo service autofs restart
     </code></pre>

3. **[A] A** swap-fájl konfigurálása

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Az ügynök újraindítása a módosítás aktiválásához

   <pre><code>sudo service waagent restart
   </code></pre>


### <a name="installing-sap-netweaver-ascsers"></a>Az SAP NetWeaver ASCS/ERS telepítése

1. **[1]** virtuális IP-erőforrás és állapot-mintavétel létrehozása a ASCS-példányhoz

   <pre><code>sudo crm node standby <b>anftstsapcl2</b>
   
   sudo crm configure primitive fs_<b>QAS</b>_ASCS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>' directory='/usr/sap/<b>QAS</b>/ASCS<b>00</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ASCS IPaddr2 \
     params ip=<b>10.1.1.20</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ASCS anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k 620<b>00</b>" \
     op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>QAS</b>_ASCS fs_<b>QAS</b>_ASCS nc_<b>QAS</b>_ASCS vip_<b>QAS</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   Győződjön meg arról, hogy a fürt állapota ok, és hogy az összes erőforrás el van indítva. Nem fontos, hogy az erőforrások melyik csomóponton futnak.

   <pre><code>sudo crm_mon -r
   
   # Node anftstsapcl2: standby
   # <b>Online: [ anftstsapcl1 ]</b>
   # 
   # Full list of resources:
   #
   # Resource Group: g-QAS_ASCS
   #     fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl1</b>
   #     nc_QAS_ASCS        (ocf::heartbeat:anything):      <b>Started anftstsapcl1</b>
   #     vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl1</b>
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   </code></pre>
  
2. **[1]** az SAP NetWeaver ASCS telepítése  

   Telepítse az SAP NetWeaver ASCS root-ként az első csomóponton egy olyan virtuális állomásnév használatával, amely a ASCS terheléselosztó-felületi konfigurációjának IP-címét képezi le, például a <b>anftstsapvh</b>, a <b>10.1.1.20</b> és a által használt példány számát. a terheléselosztó mintavétele, például <b>00</b>.

   A sapinst paraméter SAPINST_REMOTE_ACCESS_USER lehetővé teszi, hogy a nem root felhasználó csatlakozhasson a sapinst. A SAPINST_USE_HOSTNAME paraméter használatával telepítheti az SAP-t a virtuális állomásnév használatával.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   Ha a telepítés során nem sikerül almappát létrehozni a/usr/SAP/**QAS**/ASCS**00**-ben, próbálja meg beállítani a ASCS**00** mappa tulajdonosát és csoportját, és próbálkozzon újra. 

   <pre><code>
   chown <b>qas</b>adm /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   </code></pre>

3. **[1]** virtuális IP-erőforrás és állapot-mintavétel létrehozása az ERS-példányhoz

   <pre><code>
   sudo crm node online <b>anftstsapcl2</b>
   sudo crm node standby <b>anftstsapcl1</b>
   
   sudo crm configure primitive fs_<b>QAS</b>_ERS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>ers' directory='/usr/sap/<b>QAS</b>/ERS<b>01</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ERS IPaddr2 \
     params ip=<b>10.1.1.21</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ERS anything \
    params binfile="/usr/bin/nc" cmdline_options="-l -k 621<b>01</b>" \
    op monitor timeout=20s interval=10 depth=0
   
   # WARNING: Resources nc_QAS_ASCS,nc_QAS_ERS violate uniqueness for parameter "binfile": "/usr/bin/nc"
   # Do you still want to commit (y/n)? y
   
   sudo crm configure group g-<b>QAS</b>_ERS fs_<b>QAS</b>_ERS nc_<b>QAS</b>_ERS vip_<b>QAS</b>_ERS
   </code></pre>

   Győződjön meg arról, hogy a fürt állapota ok, és hogy az összes erőforrás el van indítva. Nem fontos, hogy az erőforrások melyik csomóponton futnak.

   <pre><code>sudo crm_mon -r
   
   # Node <b>anftstsapcl1: standby</b>
   # <b>Online: [ anftstsapcl2 ]</b>
   # 
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ASCS        (ocf::heartbeat:anything):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ERS
   #      fs_QAS_ERS (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ERS (ocf::heartbeat:anything):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ERS  (ocf::heartbeat:IPaddr2):     <b>Started anftstsapcl2</b>
   </code></pre>

4. **[2]** SAP NETWEAVER-ERS telepítése

   Telepítse az SAP NetWeaver-ket root-ként a második csomóponton egy olyan virtuális állomásnév használatával, amely a hálózati terheléselosztási felület konfigurációjának IP-címét képezi le az ERS számára, például <b>anftstsapers</b>, <b>10.1.1.21</b> és a a Load Balancer mintavétele, például <b>01</b>.

   A sapinst paraméter SAPINST_REMOTE_ACCESS_USER lehetővé teszi, hogy a nem root felhasználó csatlakozhasson a sapinst. A SAPINST_USE_HOSTNAME paraméter használatával telepítheti az SAP-t a virtuális állomásnév használatával.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   > [!NOTE]
   > Használja az SWPM SP 20 PL 05-es vagy újabb verzióját. Az alacsonyabb verziók nem tudják megfelelően beállítani az engedélyeket, és a telepítés sikertelen lesz.

   Ha a telepítés során nem sikerül almappát létrehozni a/usr/SAP/**QAS**/ERS**01**-ben, próbálja meg beállítani az ERS**01** mappa tulajdonosát és csoportját, és próbálkozzon újra.

   <pre><code>
   chown qasadm /usr/sap/<b>QAS</b>/ERS<b>01</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ERS<b>01</b>
   </code></pre>


5. **[1]** a ASCS/SCS és az ERS instance-profilok átalakítása
 
   * ASCS/SCS-profil

   <pre><code>
   sudo vi /sapmnt/<b>QAS</b>/profile/<b>QAS</b>_<b>ASCS00</b>_<b>anftstsapvh</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   </code></pre>

   * ERS-profil

   <pre><code>
   sudo vi /sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>

6. **[A] A** Keep Alive konfigurálása

   Az SAP NetWeaver Application Server és a ASCS/SCS közötti kommunikáció egy szoftveres terheléselosztó használatával irányítható át. A terheléselosztó konfigurálható időtúllépés után leválasztja az inaktív kapcsolatokat. Ennek elkerüléséhez be kell állítania egy paramétert az SAP NetWeaver ASCS/SCS profilban, és módosítania kell a Linux rendszer beállításait. További információért olvassa el az [SAP megjegyzés 1410736][1410736] .

   A enque/encni/set_so_keepalive ASCS/SCS-profil paramétere már hozzá lett adva az utolsó lépésben.

   <pre><code>
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

7. **[A]** a telepítés után KONFIGURÁLJA az SAP-felhasználókat

   <pre><code>
   # Add sidadm to the haclient group
   sudo usermod -aG haclient <b>qas</b>adm
   </code></pre>

8. **[1]** adja hozzá a ASCS és a ERS SAP-szolgáltatásokat a sapservice-fájlhoz

   Adja hozzá a ASCS-szolgáltatási bejegyzést a második csomóponthoz, és másolja az ERS szolgáltatási bejegyzést az első csomópontra.

   <pre><code>
   cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>anftstsapcl2</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>anftstsapcl2</b> "cat /usr/sap/sapservices" | grep ERS<b>01</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

9. **[1]** az SAP-fürt erőforrásainak létrehozása

Ha a sorba helyezni Server 1 architektúráját (ENSA1) használja, az erőforrásokat az alábbiak szerint határozza meg:

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ERS<b>01</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ERS<b>01</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>" AUTOMATIC_RECOVER=false IS_ERS=true \
    meta priority=1000
   
   sudo crm configure modgroup g-<b>QAS</b>_ASCS add rsc_sap_<b>QAS</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>QAS</b>_ERS add rsc_sap_<b>QAS</b>_ERS<b>01</b>
   
   sudo crm configure colocation col_sap_<b>QAS</b>_no_both -5000: g-<b>QAS</b>_ERS g-<b>QAS</b>_ASCS
   sudo crm configure location loc_sap_<b>QAS</b>_failover_to_ers rsc_sap_<b>QAS</b>_ASCS<b>00</b> rule 2000: runs_ers_<b>QAS</b> eq 1
   sudo crm configure order ord_sap_<b>QAS</b>_first_start_ascs Optional: rsc_sap_<b>QAS</b>_ASCS<b>00</b>:start rsc_sap_<b>QAS</b>_ERS<b>01</b>:stop symmetrical=false
   
   sudo crm node online <b>anftstsapcl1</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   Az SAP bevezette a 2. sorba helyezni-kiszolgáló, beleértve a replikálást, az SAP NW 7,52-támogatását. A ABAP platform 1809-től kezdődően a sorba helyezni Server 2 alapértelmezés szerint telepítve van. Lásd: SAP-Megjegyzés [2630416](https://launchpad.support.sap.com/#/notes/2630416) a sorba helyezni Server 2 támogatásához.
Ha a sorba helyezni Server 2 architektúráját ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)) használja, adja meg az erőforrásokat az alábbiak szerint:

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ERS<b>01</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ERS<b>01</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>" AUTOMATIC_RECOVER=false IS_ERS=true
   
   sudo crm configure modgroup g-<b>QAS</b>_ASCS add rsc_sap_<b>QAS</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>QAS</b>_ERS add rsc_sap_<b>QAS</b>_ERS<b>01</b>
   
   sudo crm configure colocation col_sap_<b>QAS</b>_no_both -5000: g-<b>QAS</b>_ERS g-<b>QAS</b>_ASCS
   sudo crm configure order ord_sap_<b>QAS</b>_first_start_ascs Optional: rsc_sap_<b>QAS</b>_ASCS<b>00</b>:start rsc_sap_<b>QAS</b>_ERS<b>01</b>:stop symmetrical=false
   
   sudo crm node online <b>anftstsapcl1</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   Ha egy régebbi verzióról frissít, és átvált a 2. sorba helyezni-kiszolgálóra, tekintse meg a következőt: SAP Note [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   Győződjön meg arról, hogy a fürt állapota ok, és hogy az összes erőforrás el van indítva. Nem fontos, hogy az erőforrások melyik csomóponton futnak.

   <pre><code>sudo crm_mon -r
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl1</b>
   #      nc_QAS_ASCS        (ocf::heartbeat:anything):      <b>Started anftstsapcl1</b>
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl1</b>
   #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl1</b>
   #  Resource Group: g-QAS_ERS
   #      fs_QAS_ERS (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ERS (ocf::heartbeat:anything):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl2</b>
   #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl2</b>
   </code></pre>

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Az SAP NetWeaver Application Server előkészítése 

Egyes adatbázisokhoz szükséges, hogy az adatbázis példányának telepítése egy alkalmazáskiszolgáló legyen végrehajtva. Készítse elő az alkalmazáskiszolgáló virtuális gépeket, hogy azok használni tudják őket ezekben az esetekben.

Az ordító lépések azt feltételezik, hogy az alkalmazáskiszolgáló a ASCS/SCS és HANA kiszolgálóktól eltérő kiszolgálóra van telepítve. Ellenkező esetben az alábbi lépések (például az állomásnév-feloldás konfigurálása) nem szükségesek.

A (z) **[a]** előtaggal rendelkező következő elemek a Pas és az AAS esetében egyaránt érvényesek **[P]** – csak a Pas vagy **[S]** esetében érvényesek.


1. **[A]**  Az operációs rendszer konfigurálása

   A szabálytalan gyorsítótár méretének csökkentésére. További információkért lásd: [SLES 11/12 alacsony az írási teljesítmény nagy RAM-MAL rendelkező kiszolgálók](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>
   sudo vi /etc/sysctl.conf
   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]**  Állomásnév-feloldás beállítása

   DNS-kiszolgálót használjon, vagy módosítsa a Hosts az összes csomópontra. Ez a példa bemutatja, hogyan használhatja a Hosts fájlt.
   Cserélje le az IP-címet és a gazdagépet a következő parancsokra

   ```bash
   sudo vi /etc/hosts
   ```

   Helyezze be a következő sorokat Hosts. Módosítsa az IP-cím és a környezet megfelelő állomásnév

   <pre><code>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.1.1.20 anftstsapvh</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.1.1.21 anftstsapers</b>
   # IP address of all application servers
   <b>10.1.1.15 anftstsapa01</b>
   <b>10.1.1.16 anftstsapa02</b>
   </code></pre>

1. **[A]** a sapmnt könyvtárának létrehozása

   <pre><code>
   sudo mkdir -p /sapmnt/<b>QAS</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>QAS</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. **[P]** a Pas-címtár létrehozása

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>02</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>02</b>
   </code></pre>

1. **[S]** az AAS-címtár létrehozása

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>03</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>03</b>
   </code></pre>

1. **[P]** AutoFS konfigurálása a Pas-on

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Hozzon létre egy új fájlt a

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>02</b> -nfsvers=3,nobind,sync <b>10.1.0.5</b>:/ursap<b>qas</b>pas
   </code></pre>

   Az `autofs` új megosztások csatlakoztatásának újraindítása

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[P]** AutoFS konfigurálása az AAS-on

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Hozzon létre egy új fájlt a

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>03</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/usrsap<b>qas</b>aas
   </code></pre>

   Az `autofs` új megosztások csatlakoztatásának újraindítása

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[A] A** swap-fájl konfigurálása

   <pre><code>
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Az ügynök újraindítása a módosítás aktiválásához

   <pre><code>sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Adatbázis telepítése

Ebben a példában az SAP NetWeaver SAP HANAra van telepítve. A telepítéshez minden támogatott adatbázist használhat. A SAP HANA Azure-ban való telepítésével kapcsolatos további információkért lásd: [SAP HANA magas rendelkezésre állása Azure-beli Virtual Machines (VM)][sap-hana-ha]. For a list of supported databases, see [SAP Note 1928533][1928533].

* Az SAP Database-példány telepítésének futtatása

   Telepítse az SAP NetWeaver Database-példányt root-ként egy olyan virtuális állomásnév használatával, amely az adatbázishoz tartozó terheléselosztó előtér-konfigurációjának IP-címére van leképezve.

   A sapinst paraméter SAPINST_REMOTE_ACCESS_USER lehetővé teszi, hogy a nem root felhasználó csatlakozhasson a sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>SAP NetWeaver alkalmazáskiszolgáló telepítése

Az SAP-alkalmazáskiszolgáló telepítéséhez kövesse az alábbi lépéseket.

1. **[A]** az alkalmazáskiszolgáló előkészítése az alkalmazáskiszolgáló előkészítéséhez kövesse az alábbi, az [SAP NetWeaver Application Server előkészítése](high-availability-guide-suse-netapp-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) című fejezet lépéseit.

2. **[A]** az SAP NetWeaver alkalmazáskiszolgáló telepítése telepítsen egy elsődleges vagy további SAP NetWeaver Application Servert.

   A sapinst paraméter SAPINST_REMOTE_ACCESS_USER lehetővé teszi, hogy a nem root felhasználó csatlakozhasson a sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

3. **[A]** a biztonságos tár SAP HANA frissítése

   Frissítse a SAP HANA biztonságos tárolót, hogy az SAP HANA rendszerreplikáció beállításának virtuális nevére mutasson.

   A következő parancs futtatásával sorolja fel a bejegyzéseket
   <pre><code>
   hdbuserstore List
   </code></pre>

   Ennek az összes bejegyzést fel kell sorolnia, és a következőhöz hasonlóan kell kinéznie
   <pre><code>
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.1.1.5:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>QAS</b>
   </code></pre>

   A kimenet azt mutatja, hogy az alapértelmezett bejegyzés IP-címe a virtuális gépre mutat, nem pedig a terheléselosztó IP-címére. Ezt a bejegyzést úgy kell módosítani, hogy a terheléselosztó virtuális állomásneve mutasson. Ügyeljen arra, hogy ugyanazt a portot használja (a fenti kimenetben**30313** ) és az adatbázis nevét (a fenti kimenet**QAS** )!

   <pre><code>
   su - <b>qas</b>adm
   hdbuserstore SET DEFAULT <b>qasdb:30313@QAS</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>A fürt beállításának tesztelése

A következő tesztek a tesztelési esetek egy példányát jelentik a [SUSE ajánlott eljárásokat ismertető útmutatójában][suse-ha-guide]. Ezeket a rendszer átmásolja az Ön kényelme érdekében. Mindig olvassa el az ajánlott eljárásokat ismertető útmutatót, és végezze el az esetleg hozzáadott további teszteket is.

1. HAGetFailoverConfig, HACheckConfig és HACheckFailoverConfig tesztelése

   Futtassa az alábbi parancsokat sapsid \<> adm-ként azon a csomóponton, amelyen a ASCS-példány jelenleg fut. Ha a parancsok meghiúsulnak: Nincs elég memória, a gazdagép kötőjelei okozhatja. Ez egy ismert probléma, amelyet a SUSE az SAP-SUSE-cluster-Connector csomagban fog megállapítani.

   <pre><code>
   anftstsapcl1:qasadm 52> sapcontrol -nr 00 -function HAGetFailoverConfig
   07.03.2019 20:08:59
   HAGetFailoverConfig
   OK
   HAActive: TRUE
   HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP3
   HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP3 (sap_suse_cluster_connector 3.1.0)
   HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   HAActiveNode: anftstsapcl1
   HANodes: anftstsapcl1, anftstsapcl2

   anftstsapcl1:qasadm 54> sapcontrol -nr 00 -function HACheckConfig
   07.03.2019 23:28:29
   HACheckConfig
   OK
   state, category, description, comment
   SUCCESS, SAP CONFIGURATION, Redundant ABAP instance configuration, 2 ABAP instances detected
   SUCCESS, SAP CONFIGURATION, Redundant Java instance configuration, 0 Java instances detected
   SUCCESS, SAP CONFIGURATION, Enqueue separation, All Enqueue server separated from application server
   SUCCESS, SAP CONFIGURATION, MessageServer separation, All MessageServer separated from application server
   SUCCESS, SAP CONFIGURATION, ABAP instances on multiple hosts, ABAP instances on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP SPOOL service configuration, 2 ABAP instances with SPOOL service detected
   SUCCESS, SAP STATE, Redundant ABAP SPOOL service state, 2 ABAP instances with active SPOOL service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP SPOOL service on multiple hosts, ABAP instances with active ABAP SPOOL service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP BATCH service configuration, 2 ABAP instances with BATCH service detected
   SUCCESS, SAP STATE, Redundant ABAP BATCH service state, 2 ABAP instances with active BATCH service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP BATCH service on multiple hosts, ABAP instances with active ABAP BATCH service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP DIALOG service configuration, 2 ABAP instances with DIALOG service detected
   SUCCESS, SAP STATE, Redundant ABAP DIALOG service state, 2 ABAP instances with active DIALOG service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP DIALOG service on multiple hosts, ABAP instances with active ABAP DIALOG service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP UPDATE service configuration, 2 ABAP instances with UPDATE service detected
   SUCCESS, SAP STATE, Redundant ABAP UPDATE service state, 2 ABAP instances with active UPDATE service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP UPDATE service on multiple hosts, ABAP instances with active ABAP UPDATE service on multiple hosts detected
   SUCCESS, SAP STATE, SCS instance running, SCS instance status ok
   SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version (anftstsapvh_QAS_00), SAPInstance includes is-ers patch
   SUCCESS, SAP CONFIGURATION, Enqueue replication (anftstsapvh_QAS_00), Enqueue replication enabled
   SUCCESS, SAP STATE, Enqueue replication state (anftstsapvh_QAS_00), Enqueue replication active
   
   anftstsapcl1:qasadm 55> sapcontrol -nr 00 -function HACheckFailoverConfig
   07.03.2019 23:30:48
   HACheckFailoverConfig
   OK
   state, category, description, comment
   SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   </code></pre>

2. A ASCS-példány manuális áttelepíteni

   Erőforrás állapota a teszt elindítása előtt:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rscsap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Starting anftstsapcl1
   </code></pre>

   Futtassa a következő parancsokat root-ként a ASCS-példány áttelepíteni.

   <pre><code>
   anftstsapcl1:~ # crm resource migrate rsc_sap_QAS_ASCS00 force
   INFO: Move constraint created for rsc_sap_QAS_ASCS00
   
   anftstsapcl1:~ # crm resource unmigrate rsc_sap_QAS_ASCS00
   INFO: Removed migration constraints for rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

3. HAFailoverToNode tesztelése

   Erőforrás állapota a teszt elindítása előtt:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

   Futtassa a következő parancsokat sapsid \<> adm-ként a ASCS-példány áttelepíteni.

   <pre><code>
   anftstsapcl1:qasadm 53> sapcontrol -nr 00 -host anftstsapvh -user <b>qas</b>adm &lt;password&gt; -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   # Remove migration constraints
   anftstsapcl1:~ # crm resource clear rsc_sap_QAS_ASCS00
   #INFO: Removed migration constraints for rsc_sap_QAS_ASCS00
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

4. Csomópont összeomlásának szimulálása 

   Erőforrás állapota a teszt elindítása előtt:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Futtassa a következő parancsot gyökérként azon a csomóponton, amelyen a ASCS-példány fut

   <pre><code>anftstsapcl2:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Ha SBD használ, a pacemaker nem kezdődhet automatikusan a megölt csomóponton. A csomópont újraindítása utáni állapotnak így kell kinéznie.

   <pre><code>Online:
   Online: [ anftstsapcl1 ]
   OFFLINE: [ anftstsapcl2 ]

   Full list of resources:

    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1

   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=166, status=complete, exitreason='',
    last-rc-change='Fri Mar  8 18:26:10 2019', queued=0ms, exec=0ms
   </code></pre>

   Az alábbi parancsokkal elindíthatja a pacemakert a megölt csomóponton, megtisztíthatja a SBD-üzeneteket, és megtisztíthatja a sikertelen erőforrásokat.

   <pre><code>
   # run as root
   # list the SBD device(s)
   anftstsapcl2:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405b730e31e7d5a4516a2a697dcf;/dev/disk/by-id/scsi-36001405f69d7ed91ef54461a442c676e;/dev/disk/by-id/scsi-360014058e5f335f2567488882f3a2c3a"

   anftstsapcl2:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e11 -d /dev/disk/by-id/scsi-36001405f69d7ed91ef54461a442c676e -d /dev/disk/by-id/scsi-360014058e5f335f2567488882f3a2c3a message anftstsapcl2 clear

   anftstsapcl2:~ # systemctl start pacemaker
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>
   Full list of resources:
   
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

5. A ASCS-példány manuális újraindításának tesztelése

   Erőforrás állapota a teszt elindítása előtt:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Hozzon létre egy sorba helyezni zárolást, például szerkesszen egy felhasználót a tranzakció su01. Futtassa az alábbi parancsokat < sapsid\>adm-ként azon a csomóponton, amelyen a ASCS-példány fut. A parancsok leállítják a ASCS-példányt, és újra elindítják. Ha a sorba helyezni Server 1 architektúrát használja, a sorba helyezni zárolás várhatóan el fog veszni ebben a tesztben. Ha a sorba helyezni Server 2 architektúrát használja, a rendszer megőrzi a sorba helyezni. 

   <pre><code>anftstsapcl2:qasadm 51> sapcontrol -nr 00 -function StopWait 600 2
   </code></pre>

   A ASCS-példánynak mostantól le kell tiltania a Pacemakerben

   <pre><code>  rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Stopped (disabled)
   </code></pre>

   Indítsa el újra a ASCS példányt ugyanazon a csomóponton.

   <pre><code>anftstsapcl2:qasadm 52> sapcontrol -nr 00 -function StartWait 600 2
   </code></pre>

   A su01 sorba helyezni zárolását el kell veszíteni, ha a sorba helyezni Server Replication 1 architektúrát használja, és a háttérnek alaphelyzetbe kell állítani. Erőforrás állapota a teszt után:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

6. Az üzenetküldési kiszolgáló folyamatának leölése

   Erőforrás állapota a teszt elindítása előtt:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Futtassa az alábbi parancsokat root-ként az üzenet-kiszolgáló folyamatának azonosításához és a megöléséhez.

   <pre><code>anftstsapcl2:~ # pgrep ms.sapQAS | xargs kill -9
   </code></pre>

   Ha csak egyszer fogja megölni az üzenetet, a sapstart újraindítja a kiszolgálót. Ha elég gyakran megölni, a pacemaker végül áthelyezi a ASCS-példányt a másik csomópontra. A teszt után futtassa a következő parancsokat root-ként a ASCS és az ERS-példány erőforrás-állapotának tisztításához.

   <pre><code>
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

7. Sorba helyezni-kiszolgáló folyamatának leölése

   Erőforrás állapota a teszt elindítása előtt:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

   Futtassa a következő parancsokat gyökérként azon a csomóponton, amelyen a ASCS-példány fut, hogy megöli a sorba helyezni-kiszolgálót.

   <pre><code>anftstsapcl1:~ # pgrep en.sapQAS | xargs kill -9
   </code></pre>

   A ASCS-példánynak azonnal át kell vennie a feladatátvételt a másik csomópontra. Az ERS-példánynak a ASCS-példány elindítása után is feladatátvételt kell tennie. A teszt után futtassa a következő parancsokat root-ként a ASCS és az ERS-példány erőforrás-állapotának tisztításához.

   <pre><code>
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

8. Sorba helyezni-replikációs kiszolgáló folyamatának leölése

   Erőforrás állapota a teszt elindítása előtt:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Futtassa a következő parancsot gyökérként azon a csomóponton, amelyen az ERS-példány fut, hogy megöli a sorba helyezni replikációs kiszolgáló folyamatát.

   <pre><code>anftstsapcl1:~ # pgrep er.sapQAS | xargs kill -9
   </code></pre>

   Ha csak egyszer futtatja a parancsot, `sapstart` a automatikusan újraindítja a folyamatot. Ha elég gyakran fut, a nem `sapstart` indítja újra a folyamatot, és az erőforrás leállított állapotba kerül. A teszt után futtassa a következő parancsokat root-ként az ERS-példány erőforrás-állapotának tisztításához.

   <pre><code>anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

9. Sorba helyezni sapstartsrv folyamatának leölése

   Erőforrás állapota a teszt elindítása előtt:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Futtassa a következő parancsokat gyökérként azon a csomóponton, amelyen a ASCS fut.

   <pre><code>
   anftstsapcl2:~ # pgrep -fl ASCS00.*sapstartsrv
   #67625 sapstartsrv
   
   anftstsapcl2:~ # kill -9 67625
   </code></pre>

   A sapstartsrv folyamatot mindig újra kell indítani a pacemaker erőforrás-ügynökkel. Erőforrás állapota a teszt után:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

## <a name="next-steps"></a>További lépések

* [Azure Virtual Machines az SAP tervezéséhez és megvalósításához][planning-guide]
* [Azure Virtual Machines üzembe helyezés az SAP-ban][deployment-guide]
* [Azure Virtual Machines adatbázis-kezelői telepítés az SAP-hoz][dbms-guide]
* Ismerje meg, hogyan hozhat létre magas rendelkezésre állást, és hogyan tervezze meg az SAP vész-helyreállítását 
* HANA az Azure-ban (nagyméretű példányok) a [magas rendelkezésre állást és a vész-helyreállítást az Azure-ban SAP HANA (nagyméretű példányok)](hana-overview-high-availability-disaster-recovery.md).
* A magas rendelkezésre állás és a SAP HANA Azure-beli virtuális gépeken történő vész-helyreállítási tervének megismeréséhez lásd: [Az Azure-beli SAP HANA magas rendelkezésre állása Virtual Machines (VM)][sap-hana-ha]

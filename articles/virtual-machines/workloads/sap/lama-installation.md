---
title: SAP láma-összekötő az Azure-hoz | Microsoft Docs
description: SAP-rendszerek kezelése az Azure-ban az SAP láma használatával
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/17/2018
ms.author: sedusch
ms.openlocfilehash: 28a3183114db206e55814d1b25eaef37a2819c1d
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68495024"
---
# <a name="sap-lama-connector-for-azure"></a>Az Azure SAP LaMa összekötője

[1877727]: https://launchpad.support.sap.com/#/notes/1877727
[2343511]: https://launchpad.support.sap.com/#/notes/2343511
[2350235]: https://launchpad.support.sap.com/#/notes/2350235
[2562184]: https://launchpad.support.sap.com/#/notes/2562184
[2628497]: https://launchpad.support.sap.com/#/notes/2628497
[2445033]: https://launchpad.support.sap.com/#/notes/2445033
[2815988]: https://launchpad.support.sap.com/#/notes/2815988
[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png
[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[hana-ops-guide]:hana-vm-operations.md

> [!NOTE]
> Általános támogatási nyilatkozat: Ha az SAP láma vagy az Azure Connector támogatására van szüksége, mindig nyisson meg egy, az SAP-vel kapcsolatos incidenst a BC-VCM-LVM-HYPERV összetevőben.

Az SAP LaMat számos ügyfél használja az SAP-környezetük üzemeltetéséhez és figyeléséhez. Mivel az SAP LaMa 3,0 SP05, a rendszer alapértelmezés szerint egy összekötővel is rendelkezik az Azure-hoz. Ezzel az összekötővel felszabadíthatja és elindíthatja a virtuális gépeket, átmásolhatja és áthelyezheti a felügyelt lemezeket, és törölheti a felügyelt lemezeket. Ezeknek az alapszintű műveleteknek a segítségével áthelyezheti, másolhatja, klónozott és frissítheti az SAP-rendszereket az SAP láma használatával.

Ez az útmutató leírja, hogyan állíthatja be az Azure-összekötőt az SAP láma számára, hogyan hozhat létre olyan virtuális gépeket, amelyek az adaptív SAP-rendszerek telepítéséhez és konfigurálásához használhatók.

> [!NOTE]
> Az összekötő csak az SAP láma Enterprise kiadásában érhető el

## <a name="resources"></a>További források

A következő SAP-megjegyzések az Azure-beli SAP láma témaköréhez kapcsolódnak:

| Megjegyzés száma | Beosztás |
| --- | --- |
| [2343511] |Microsoft Azure-összekötő az SAP tájkép-felügyelethez (láma) |
| [2350235] |SAP tájképi felügyelet 3,0 – Enterprise Edition |

Olvassa el az SAP [láma SAP-Súgó portálját](https://help.sap.com/viewer/p/SAP_LANDSCAPE_MANAGEMENT_ENTERPRISE)is.

## <a name="general-remarks"></a>Általános megjegyzések

* Győződjön meg arról, hogy engedélyezve van-e az *automatikus csatlakoztatási pont létrehozása* a Setup-> Settings-> Engine-ben  
  Ha az SAP LaMa egy virtuális gépen lévő SAP adaptív bővítményekkel csatlakoztat köteteket, akkor a csatlakoztatási pontnak léteznie kell, ha ez a beállítás nincs engedélyezve.

* Használjon külön alhálózatot, és ne használjon dinamikus IP-címeket, hogy megakadályozza az IP-címek "ellopását" az új virtuális gépek és az SAP-példányok üzembe helyezésekor.  
  Ha az alhálózatban dinamikus IP-cím-lefoglalást használ, amelyet az SAP láma is használ, akkor a SAP-alapú SAP-rendszer előkészítése sikertelen lehet. Ha egy SAP-rendszer felkészületlenül, az IP-címek nincsenek lefoglalva, és más virtuális gépek számára is kiosztható.

* Ha bejelentkezik a felügyelt gazdagépekre, győződjön meg arról, hogy nem tiltja le a fájlrendszerek leválasztását  
  Ha Linux rendszerű virtuális gépekre jelentkezik be, és a munkakönyvtárat egy csatlakoztatási ponton lévő könyvtárba szeretné módosítani, például a/usr/sap/AH1/ASCS00/exe, a kötet nem lehet leválasztani, és az áthelyezés vagy az előkészítés sikertelen lesz.

## <a name="set-up-azure-connector-for-sap-lama"></a>Az Azure Connector beállítása az SAP láma számára

Az Azure Connector az SAP láma 3,0 SP05 van elküldve. Javasoljuk, hogy mindig telepítse a legújabb támogatási csomagot és javítást az SAP láma 3,0-es verzióját. Az Azure Connector egy egyszerű szolgáltatásnév használatával engedélyezi a Microsoft Azure. Kövesse az alábbi lépéseket egy egyszerű szolgáltatásnév létrehozásához az SAP tájképi felügyelethez (láma).

1. Nyissa meg a következőt: https://portal.azure.com
1. Nyissa meg az Azure Active Directory panel
1. Kattintson Alkalmazásregisztrációk
1. Kattintson a Hozzáadás gombra
1. Adjon meg egy nevet, válassza az alkalmazás típusa "Web App/API" lehetőséget, és adjon meg egy bejelentkezési URL-címet\/(például http:/localhost), és kattintson a Létrehozás gombra.
1. A bejelentkezési URL-címet nem használja, és bármilyen érvényes URL-cím lehet
1. Válassza ki az új alkalmazást, és kattintson a kulcsok elemre a beállítások lapon.
1. Adja meg az új kulcs leírását, válassza a "soha nem jár le" lehetőséget, és kattintson a Save (Mentés) gombra.
1. Jegyezze fel az értéket. Az egyszerű szolgáltatás jelszavaként van használatban
1. Jegyezze fel az alkalmazás azonosítóját. Az egyszerű szolgáltatásnév felhasználóneveként van használatban

Az egyszerű szolgáltatás nem rendelkezik engedélyekkel alapértelmezés szerint az Azure-erőforrások eléréséhez. Meg kell adnia a szolgáltatáshoz való hozzáféréshez szükséges engedélyeket.

1. Nyissa meg a következőt: https://portal.azure.com
1. Az erőforráscsoportok panel megnyitása
1. Válassza ki a használni kívánt erőforráscsoportot
1. Kattintson a hozzáférés-vezérlés (IAM)
1. Kattintson a szerepkör-hozzárendelés hozzáadása elemre.
1. Válassza ki a szerepkör közreműködőjét
1. Adja meg a fent létrehozott alkalmazás neve
1. Kattintson a Save (Mentés) gombra.
1. Az SAP LaMaben használni kívánt összes erőforráscsoport esetében ismételje meg a 3 – 8. lépést

Nyissa meg az SAP láma webhelyét, és navigáljon az infrastruktúrához. Lépjen a Cloud managerek lapra, és kattintson a Hozzáadás gombra. Válassza ki a Microsoft Azure Felhőadapter majd kattintson a Tovább gombra. Adja meg a következő információkat:

* Címke: Válassza ki az összekötő-példány nevét
* Felhasználónév: Egyszerű szolgáltatás alkalmazásazonosítója
* Jelszó: Egyszerű szolgáltatásnév kulcs/jelszó
* URL: Alapértelmezett érték megtartása https://management.azure.com/
* Figyelési időköz (másodperc): Legalább 300 kell lennie
* Előfizetés azonosítója: Azure-előfizetés azonosítója
* Azure Active Directory bérlő azonosítója: A Active Directory bérlő azonosítója
* Proxy gazdagép: A proxy állomásneve, ha az SAP láma proxyra van szüksége az internethez való csatlakozáshoz
* Proxy portja: A proxy TCP-portja

A bemenet ellenőrzéséhez kattintson a konfiguráció tesztelése gombra. A következőnek kell megjelennie:

A sikeres csatlakoztatás: A Microsoft Cloud-hoz való kapcsolódás sikeres volt. 7 erőforráscsoport található (csak 10 csoport szükséges)

a webhely alján.

## <a name="provision-a-new-adaptive-sap-system"></a>Új adaptív SAP-rendszer kiépítése

Manuálisan is üzembe helyezhet egy új virtuális gépet, vagy használhatja az Azure-sablonok egyikét a [Gyorsindítás tárházban](https://github.com/Azure/azure-quickstart-templates). Az [SAP NETWEAVER ASCS](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-ascs), az [SAP NetWeaver Application Servers](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-apps)és az [adatbázis](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-database)sablonokat tartalmaz. Ezekkel a sablonokkal új gazdagépeket is kiépíthet a rendszer másolási/klónozási szolgáltatásának részeként.

Azt javasoljuk, hogy használjon külön alhálózatot az összes olyan virtuális géphez, amelyet az SAP LaMavel szeretne felügyelni, és ne használjon dinamikus IP-címeket, hogy megakadályozza az IP-címek "ellopását" az új virtuális gépek és az SAP-példányok telepítésekor.

> [!NOTE]
> Ha lehetséges, távolítsa el az összes virtuálisgép-bővítményt, mert a lemezek virtuális gépről való leválasztása hosszú futtatókörnyezetet eredményezhet.

Győződjön meg arról, \<hogy a felhasználó hanasid \<> adm, sapsid > adm és Group sapsys létezik a célszámítógépen ugyanazzal az azonosítóval és GID-mel, vagy használja az LDAP-t. Engedélyezze és indítsa el az NFS-kiszolgálót azon a virtuális gépeken, amelyeket az SAP NetWeaver (A) SCS futtatásához kíván használni.

### <a name="manual-deployment"></a>Manuális üzembe helyezés

Az SAP-láma az SAP-gazdagép ügynökével kommunikál a virtuális géppel. Ha manuálisan telepíti a virtuális gépeket, vagy nem a Azure Resource Manager sablont használja a gyors üzembe helyezési tárházból, mindenképpen telepítse a legújabb SAP-gazdagép ügynököt és az SAP adaptív bővítményeit. További információ az Azure szükséges javítási szintjeiről: SAP Note [2343511].

#### <a name="manual-deployment-of-a-linux-virtual-machine"></a>Linux rendszerű virtuális gép manuális üzembe helyezése

Hozzon létre egy új virtuális gépet a [2343511]-es SAP-megjegyzésben felsorolt támogatott operációs rendszerek egyikével. További IP-konfigurációk hozzáadása az SAP-példányokhoz. Minden példánynak legalább az IP-címen kell lennie, és virtuális állomásnév használatával kell telepíteni.

Az SAP NetWeaver ASCS-példány lemezeket igényel\<a/sapmnt/SAPSID >\<, a/usr/SAP/SAPSID >\<, a/usr/SAP/Trans és a/usr/SAP/SAPSID > adm számára. Az SAP NetWeaver alkalmazás-kiszolgálóknak nincs szükségük további lemezekre. Az SAP-példánnyal kapcsolatos összes információt a ASCS kell tárolni, és az NFS-en keresztül kell exportálni. Ellenkező esetben az SAP láma használatával jelenleg nem lehet további alkalmazásokat hozzáadni.

![SAP NetWeaver ASCS Linux rendszeren](media/lama/sap-lama-ascs-app-linux.png)

#### <a name="manual-deployment-for-sap-hana"></a>SAP HANA manuális üzembe helyezése

Hozzon létre egy új virtuális gépet a SAP HANA támogatott operációs rendszerek egyikével, az SAP Megjegyzés [2343511]. Vegyen fel egy további IP-konfigurációt SAP HANA és egy HANA-bérlőre.

SAP HANA lemezek szükségesek a/Hana/Shared, a/Hana/Backup, a/Hana/Data és a/Hana/log

![SAP HANA Linuxon](media/lama/sap-lama-db-hana.png)

#### <a name="manual-deployment-for-oracle-database-on-linux"></a>Manuális üzembe helyezés a Linux rendszerhez Oracle Database

Hozzon létre egy új virtuális gépet az Oracle-adatbázisok támogatott operációs rendszereinek egyikével az SAP Note [2343511]-ben leírtak szerint. Adjon hozzá egy további IP-konfigurációt az Oracle-adatbázishoz.

Az Oracle-adatbázis a/Oracle, a/Home/oraod1 és a/Home/Oracle lemezeit igényli

![Oracle Database Linux rendszeren](media/lama/sap-lama-db-ora-lnx.png)

#### <a name="manual-deployment-for-microsoft-sql-server"></a>Microsoft SQL Server manuális üzembe helyezése

Hozzon létre egy új virtuális gépet a Microsoft SQL Server támogatott operációs rendszerek egyikével, az SAP Megjegyzés [2343511]. Adjon hozzá egy további IP-konfigurációt a SQL Server példányhoz.

A SQL Server adatbázis-kiszolgálónak lemezekre van szüksége az adatbázis-adatfájlok és a naplófájlok és lemezek c:\usr\sap.

![Oracle Database Linux rendszeren](media/lama/sap-lama-db-sql.png)

Győződjön meg arról, hogy egy támogatott Microsoft ODBC-illesztőprogramot telepít a SQL Serverhoz egy olyan virtuális gépen, amelyet egy SAP NetWeaver-alkalmazáskiszolgáló áthelyezésére kíván használni a rendszer másolási/klónozási céljaként.

Az SAP láma nem tudja újra megkeresni SQL Server magát, így egy olyan virtuális gépet, amelyet az adatbázis-példányok áthelyezésére kíván használni a rendszermásolási/klónozási célként, SQL Server előre telepítve van.

### <a name="deploy-virtual-machine-using-an-azure-template"></a>Virtuális gép üzembe helyezése Azure-sablon használatával

Töltse le a következő legújabb elérhető archívumokat a virtuális gépek operációs rendszerének [SAP szoftveres piactérről](https://support.sap.com/swdc) :

1. SAPCAR 7,21
1. SAP-GAZDAGÉP ÜGYNÖKE 7,21
1. SAP ADAPTÍV BŐVÍTMÉNY 1,0 EXT

Az alábbi összetevőket is letöltheti a [Microsoft letöltőközpontból](https://www.microsoft.com/download) .

1. Microsoft Visual C++ 2010 Újraterjeszthető csomag (x64) (csak Windows)
1. Microsoft ODBC Driver for SQL Server (csak SQL Server)

Az összetevők a sablon üzembe helyezéséhez szükségesek. A sablon elérhetővé tételének legegyszerűbb módja, ha feltölti őket egy Azure Storage-fiókba, és létrehoz egy közös hozzáférési aláírást (SAS).

A sablonok a következő paraméterekkel rendelkeznek:

* sapSystemId: Az SAP rendszerazonosító. A lemez elrendezésének létrehozásához használatos (például/usr/SAP/\<sapsid >).

* ComputerName Az új virtuális gép számítógépneve. Ezt a paramétert az SAP láma is használja. Ha ezt a sablont használja egy új virtuális gép a rendszermásolat részeként való kiépítéséhez, az SAP láma megvárja, amíg a gazdagép nem érhető el a számítógépnévvel.

* osType: A telepíteni kívánt operációs rendszer típusa.

* dbType Az adatbázis típusa. Ezzel a paraméterrel határozható meg, hogy hány további IP-konfigurációt kell hozzáadni, és hogyan kell kinéznie a lemez elrendezésének.

* sapSystemSize: A telepíteni kívánt SAP-rendszer mérete. A virtuálisgép-példány típusának és méretének meghatározására szolgál.

* AdminUsername A virtuális gép felhasználóneve.

* AdminPassword A virtuális gép jelszava. Megadhatja az SSH nyilvános kulcsát is.

* sshKeyData: Nyilvános SSH-kulcs a virtuális gépekhez. Csak Linux operációs rendszerek esetén támogatott.

* a netI: A használni kívánt alhálózat azonosítója.

* deployEmptyTarget: Ha a virtuális gépet célként szeretné áthelyezni vagy hasonló célra használni, akkor helyezzen üzembe egy üres célt. Ebben az esetben a rendszer nem csatol további lemezeket vagy IP-konfigurációkat.

* sapcarLocation: A telepített operációs rendszernek megfelelő sapcar-alkalmazás helye. a sapcar a más paraméterekben megadott archívumok kinyerésére szolgál.

* sapHostAgentArchiveLocation: Az SAP-gazda ügynökének archiválási helye. Az SAP-gazdagép ügynöke a sablon központi telepítésének részeként van telepítve.

* sapacExtLocation: Az SAP adaptív bővítmények helye. Az SAP Megjegyzés [2343511] az Azure-hoz szükséges minimális javítási szintet sorolja fel.

* vcRedistLocation: Az SAP adaptív bővítmények telepítéséhez szükséges VC-futtatókörnyezet helye. Ez a paraméter csak Windows esetén szükséges.

* odbcDriverLocation: A telepíteni kívánt ODBC-illesztőprogram helye. Csak a SQL Server Microsoft ODBC-illesztőprogramja támogatott.

* sapadmPassword: A sapadm-felhasználó jelszava.

* sapadmId: A sapadm-felhasználó Linux-felhasználói azonosítója. A Windows rendszerhez nem szükséges.

* sapsysGid: A sapsys csoport Linux-csoportjának azonosítója. A Windows rendszerhez nem szükséges.

* _artifactsLocation: Az alap URI, ahol a sablon által igényelt összetevők találhatók. Ha a sablont a kapcsolódó parancsfájlok segítségével telepíti, a rendszer az előfizetés egy privát helyét fogja használni, és ez az érték automatikusan létrejön. Csak akkor szükséges, ha nem telepíti a sablont a GitHubról.

* _artifactsLocationSasToken: A _artifactsLocation eléréséhez szükséges sasToken. Ha a sablont a kapcsolódó parancsfájlok segítségével telepíti, a rendszer automatikusan létrehoz egy sasToken. Csak akkor szükséges, ha nem telepíti a sablont a GitHubról.

### <a name="sap-hana"></a>SAP HANA

Az alábbi példákban feltételezzük, hogy a SAP HANA a System ID HN1 és az SAP NetWeaver rendszer telepítésével telepíti a rendszerazonosító AH1. A virtuális állomásnevek a HANA-példány hn1-adatbázisa, amely az SAP NetWeaver rendszer által használt HANA-bérlő ah1-adatbázisa, a ah1-ASCs az SAP NetWeaver ASCS és a ah1-di-0 esetében az első SAP NetWeaver Application Server esetében.

#### <a name="install-sap-netweaver-ascs-for-sap-hana-using-azure-managed-disks"></a>Az SAP HANA SAP NetWeaver ASCS telepítése az Azure-Managed Disks használatával

Az SAP Software kiépítési vezető (SWPM) elindítása előtt csatlakoztatnia kell a ASCS virtuális állomásneve IP-címét. Az ajánlott módszer a sapacext használata. Ha az IP-címet az sapacext használatával csatlakoztatja, akkor az újraindítás után mindenképp csatlakoztassa újra az IP-címet.

![Linux][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-ascs -n 255.255.255.128
```

![Windows][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-ascs -n 255.255.255.128
```

Futtassa a SWPM, és használja a *ah1-ASCs* a *ASCs-példány állomásneve*számára.

![Linux][Logo_Linux] Linux  
Adja hozzá a következő profil paramétert a SAP Host Agent-profilhoz, amely a/usr/SAP/hostctrl/exe/host_profile. címen található. További információ: SAP Note [2628497].
```
acosprep/nfs_paths=/home/ah1adm,/usr/sap/trans,/sapmnt/AH1,/usr/sap/AH1
```

#### <a name="install-sap-netweaver-ascs-for-sap-hana-on-azure-netappfiles-anf-beta"></a>Az Azure NetAppFiles (ANF) BÉTAVERZIÓjának SAP HANA SAP NetWeaver ASCS telepítése

> [!NOTE]
> Ez a funkció még nem érhető el. További információ: SAP Note [2815988] (csak az előnézeti ügyfelek számára látható).
SAP-incidens megnyitása a BC-VCM-LVM-HYPERV összetevőn, és kérelem a láma Storage-adapterhez való csatlakozásra Azure NetApp Files előzetes verzió

A ANF az Azure-hoz készült NFS-t biztosít. Az SAP LaMa kontextusában ez leegyszerűsíti a ABAP központi szolgáltatások (ASCS) példányainak létrehozását és az alkalmazáskiszolgáló későbbi telepítését. Korábban a ASCS-példánynak NFS-kiszolgálóként kellett működnie, és a acosprep/nfs_paths paramétert hozzá kellett adni az SAP Hostagent host_profile.

#### <a name="anf-is-currently-available-in-these-regions"></a>A ANF jelenleg a következő régiókban érhető el:

Kelet-Ausztrália, Közép-USA, USA keleti régiója, USA 2. keleti régiója, Észak-Európa, az USA déli középső régiója, Nyugat-Európa és az USA nyugati régiója

#### <a name="network-requirements"></a>Hálózati követelmények

A ANF olyan delegált alhálózatot igényel, amelynek az SAP-kiszolgálókkal megegyező VNET kell tartoznia. Példa erre a konfigurációra.
Ez a képernyő a VNET és az első alhálózat létrehozását mutatja be:

![SAP-láma virtuális hálózat létrehozása az Azure ANF ](media/lama/sap-lama-createvn-50.png)

A következő lépés létrehozza a delegált alhálózatot a Microsoft. NetApp/kötetek számára.

![SAP-láma delegált alhálózat hozzáadása ](media/lama/sap-lama-addsubnet-50.png)

![Az alhálózatok SAP láma listája ](media/lama/sap-lama-subnets.png)

Most létre kell hozni egy NetApp-fiókot a Azure Portalon belül:

![SAP láma hozzon létre NetApp-fiókot ](media/lama/sap-lama-create-netappaccount-50.png)

![SAP láma NetApp-fiók létrehozva ](media/lama/sap-lama-netappaccount.png)

A NetApp-fiókban a kapacitási készlet meghatározza az egyes készletekhez tartozó lemezek méretét és típusát:

![SAP-láma a NetApp Capacity-készlet létrehozása ](media/lama/sap-lama-capacitypool-50.png)

![SAP láma NetApp Capacity-készlet létrehozva ](media/lama/sap-lama-capacitypool-list.png)

Az NFS-kötetek már meghatározhatók. Mivel az egyik készletben több rendszer számára is lesznek kötetek, az önmagát magyarázó elnevezési sémát kell kiválasztani. A SID hozzáadása segít a kapcsolódó kötetek csoportosításában. A ASCS és az as példány esetében a következő csatlakoztatásokra van szükség:/sapmnt/\<SID\>,/usr/SAP/\<SID\> és/Home/\<SID\>adm. A központi átviteli könyvtár nem kötelező/usr/SAP/Trans, amelyet egy adott környezet minden rendszere legalább használ.

> [!NOTE]
> A BÉTAVERZIÓs fázisban a kötetek nevének egyedinek kell lennie az előfizetésen belül.

![SAP láma hozzon létre egy kötetet 1 ](media/lama/sap-lama-createvolume-80.png)

![SAP láma hozzon létre egy kötetet 2 ](media/lama/sap-lama-createvolume2-80.png)

![SAP-láma hozzon létre egy kötetet 3 ](media/lama/sap-lama-createvolume3-80.png)

Ezeket a lépéseket a többi kötet esetében is meg kell ismételni.

![A létrehozott kötetek SAP láma listája ](media/lama/sap-lama-volumes.png)

Most ezeket a köteteket csatlakoztatni kell azokhoz a rendszerekhez, amelyeken a kezdeti telepítés az SAP-SWPM történik.

Először a csatlakoztatási pontokat kell létrehozni. Ebben az esetben a SID AN1, ezért a következő parancsokat kell végrehajtani:

```bash
mkdir -p /home/an1adm
mkdir -p /sapmnt/AN1
mkdir -p /usr/sap/AN1
mkdir -p /usr/sap/trans
```
A ANF kötetek a következő parancsokkal lesznek csatlakoztatva:

```bash
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-home-sidadm /home/an1adm
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-sapmnt-sid /sapmnt/AN1
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-usr-sap-sid /usr/sap/AN1
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/global-usr-sap-trans /usr/sap/trans
```
A csatlakoztatási parancsok a portálról is származhatnak. A helyi csatlakoztatási pontokat módosítani kell.

A DF-h parancs használatával ellenőrizze a következőt:.

![SAP láma csatlakoztatási pontok operációsrendszer-szintje ](media/lama/sap-lama-mounts.png)

Most el kell végrehajtani a telepítést a SWPM.

Ugyanezeket a lépéseket kell végrehajtani legalább egy példánynál.

A sikeres telepítést követően a rendszert az SAP LaMan belül kell felderíteni.

A csatlakoztatási pontoknak a következőhöz hasonlóan kell kinézniük a ASCS és az AS példányhoz:

![A láma ](media/lama/sap-lama-ascs.png) SAP láma csatlakoztatási pontjai (ez egy példa. Az IP-címek és az exportálási útvonal eltér a korábban használttól)


#### <a name="install-sap-hana"></a>Az SAP HANA telepítése

Ha a parancssori eszköz hdblcm telepíti a SAP HANAt, a--hostname paramétert használva adjon meg egy virtuális gazdagépet. Hozzá kell adnia az adatbázis virtuális állomásneve IP-címét egy hálózati adapterhez. Az ajánlott módszer a sapacext használata. Ha az IP-címet az sapacext használatával csatlakoztatja, akkor az újraindítás után mindenképp csatlakoztassa újra az IP-címet.

Adjon hozzá egy másik virtuális állomásnevet és IP-címet ahhoz a névhez, amelyet az alkalmazáskiszolgáló a HANA-bérlőhöz való kapcsolódáshoz használ.

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h hn1-db -n 255.255.255.128
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-db -n 255.255.255.128
```

Futtassa a SWPM adatbázis-példányának telepítését az alkalmazáskiszolgáló virtuális gépen, ne a HANA virtuális gépen. Használja az *ah1-db-* t az *adatbázis* -gazdagéphez az SAP-rendszerhez készült párbeszédablak- *adatbázisban*.

#### <a name="install-sap-netweaver-application-server-for-sap-hana"></a>Az SAP NetWeaver Application Server telepítése SAP HANA

Az SAP Software kiépítési vezető (SWPM) elindítása előtt csatlakoztatnia kell az alkalmazáskiszolgáló virtuális állomásneve IP-címét. Az ajánlott módszer a sapacext használata. Ha az IP-címet az sapacext használatával csatlakoztatja, akkor az újraindítás után mindenképp csatlakoztassa újra az IP-címet.

![Linux][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-di-0 -n 255.255.255.128
```

![Windows][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-di-0 -n 255.255.255.128
```

Javasoljuk, hogy az SAP NetWeaver Profile (adatbázisok/HDB/hdb_use_ident) használatával állítsa be azt az identitást, amely a kulcsnak a HDB-userstore való megtalálásához használatos. Ezt a paramétert manuálisan is hozzáadhatja az adatbázis-példány SWPM való telepítése után, vagy futtathatja a SWPM-t a következővel

```bash
# from https://blogs.sap.com/2015/04/14/sap-hana-client-software-different-ways-to-set-the-connectivity-data/
/sapdb/DVDs/IM_LINUX_X86_64/sapinst HDB_USE_IDENT=SYSTEM_COO
```

Ha manuálisan állítja be, új HDB-userstore-bejegyzéseket is létre kell hoznia.

```bash
# run as <sapsid>adm
/usr/sap/AH1/hdbclient/hdbuserstore LIST
# reuse the port that was listed from the command above, in this example 35041
/usr/sap/AH1/hdbclient/hdbuserstore SET DEFAULT ah1-db:35041@AH1 SAPABAP1 <password>
```

Használja az *ah1-di-0 értéket* a *Pas instance Host neveként* a párbeszédablak *elsődleges Application Server*-példányában.

#### <a name="post-installation-steps-for-sap-hana"></a>A SAP HANA telepítés utáni lépései

Készítsen biztonsági másolatot a SYSTEMDB és az összes bérlői adatbázisról, mielőtt megpróbálkozik a bérlői másolattal, a bérlő áthelyezi vagy létrehozza a rendszer replikálását.

### <a name="microsoft-sql-server"></a>Microsoft SQL Server

Az alábbi példákban feltételezzük, hogy az SAP NetWeaver rendszer telepítését a System ID AS1 kell telepíteni. A virtuális gazdagépek az SAP NetWeaver rendszer által használt SQL Server példány AS1-adatbázisa, amely az SAP NetWeaver ASCS és a AS1-di-0 esetében az első SAP NetWeaver Application Server-kiszolgáló esetében használatos.

#### <a name="install-sap-netweaver-ascs-for-sql-server"></a>Az SAP NetWeaver ASCS telepítése SQL Server

Az SAP Software kiépítési vezető (SWPM) elindítása előtt csatlakoztatnia kell a ASCS virtuális állomásneve IP-címét. Az ajánlott módszer a sapacext használata. Ha az IP-címet az sapacext használatával csatlakoztatja, akkor az újraindítás után mindenképp csatlakoztassa újra az IP-címet.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-ascs -n 255.255.255.128
```

Futtassa a SWPM, és használja a *AS1-ASCs* a *ASCs-példány állomásneve*számára.

#### <a name="install-sql-server"></a>SQL Server telepítése

Hozzá kell adnia az adatbázis virtuális állomásneve IP-címét egy hálózati adapterhez. Az ajánlott módszer a sapacext használata. Ha az IP-címet az sapacext használatával csatlakoztatja, akkor az újraindítás után mindenképp csatlakoztassa újra az IP-címet.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-db -n 255.255.255.128
```

Futtassa a SWPM adatbázis-példányának telepítését az SQL Server rendszerű virtuális gépen. A SAPINST_USE_HOSTNAME =*AS1-db* paranccsal felülbírálhatja SQL Serverhoz való kapcsolódáshoz használt állomásnevet. Ha a Azure Resource Manager sablonnal telepítette a virtuális gépet, akkor ügyeljen arra, hogy az adatbázis-adatfájlokhoz használt könyvtárat a *C:\sql\data* és az adatbázis naplófájljában adja meg a *C:\sql\log*.

Győződjön meg arról, hogy a felhasználói *NT AUTHORITY\SYSTEM* hozzáfér a SQL Serverhoz, és rendelkezik a *kiszolgálói szerepkörrel*. További információ: SAP Note [1877727] és [2562184].

#### <a name="install-sap-netweaver-application-server"></a>Az SAP NetWeaver Application Server telepítése

Az SAP Software kiépítési vezető (SWPM) elindítása előtt csatlakoztatnia kell az alkalmazáskiszolgáló virtuális állomásneve IP-címét. Az ajánlott módszer a sapacext használata. Ha az IP-címet az sapacext használatával csatlakoztatja, akkor az újraindítás után mindenképp csatlakoztassa újra az IP-címet.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-di-0 -n 255.255.255.128
```

Használja az *AS1-di-0 értéket* a *Pas instance Host neveként* a párbeszédablak *elsődleges Application Server*-példányában.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="errors-and-warnings-during-discover"></a>Hibák és figyelmeztetések a felderítés során

* A SELECT engedély megtagadva
  * Microsoft [ODBC SQL Server illesztőprogram] [SQL Server] A SELECT engedély megtagadva a következő objektumon: "log_shipping_primary_databases", adatbázis: "msdb", séma: "dbo". [SOAPFaultException]  
  A SELECT engedély megtagadva a következő objektumon: "log_shipping_primary_databases", adatbázis: "msdb", séma: "dbo".
  * Megoldás  
    Győződjön meg arról, hogy az *NT AUTHORITY\SYSTEM* el tudja érni a SQL Server. Lásd: SAP-Megjegyzés [2562184]


### <a name="errors-and-warnings-for-instance-validation"></a>A példányok érvényesítésével kapcsolatos hibák és figyelmeztetések

* Kivétel történt a HDB-userstore érvényesítése során.  
  * Lásd: a naplófájl megjelenítője  
    com.sap.nw.lm.aci.monitor.api.validation.RuntimeValidationException: Kivétel történt a (z) RuntimeHDBConnectionValidator AZONOSÍTÓval rendelkező validatorban (érvényesítés: 'VALIDATION_HDB_USERSTORE'): Nem sikerült beolvasni a hdbuserstore  
    A HANA userstore nem a megfelelő helyen található
  * Megoldás  
    Győződjön meg arról, hogy a/usr/sap/AH1/hdbclient/install/installation.ini helyes

### <a name="errors-and-warnings-during-a-system-copy"></a>Hibák és figyelmeztetések a rendszermásolás során

* Hiba történt a rendszer-üzembe helyezési lépés érvényesítésekor
  * Okozta: com. SAP. NW. LM. ACI. Engine. Base. API. util. Exception. HAOperationException Calling '/usr/SAP/hostctrl/exe/sapacext-a ShowHanaBackups-m HN1-f 50-h HN1-db-o Level =\;0 status =\;5 port = 35013 PF =/usr/SAP/hostctrl /exe/host_profile-R-T dev_lvminfo-u SYSTEM-p Hook-R ' | /usr/SAP/hostctrl/exe/sapacext-a ShowHanaBackups-m HN1-f 50-h HN1-db-o szint = 0\;állapot = 5\;port = 35013 PF =/usr/SAP/hostctrl/exe/host_profile-R-T dev_lvminfo-u rendszer-p Hook-R
  * Megoldás  
    Készítsen biztonsági másolatot az összes adatbázisról a forrás HANA-rendszeren

* Az adatbázis-példány rendszermásolási lépésének *kezdete*
  * A gazdagép ügynökének "000D3A282BC91EE8A1D76CF1F92E2944" művelete nem sikerült (OperationException). FaultCode: "127", üzenet: "A parancs végrehajtása nem sikerült. : [Microsoft] [ODBC SQL Server illesztőprogram] [SQL Server] a felhasználónak nincs engedélye az "AS2" adatbázis módosítására, az adatbázis nem létezik, vagy az adatbázis nem olyan állapotban van, amely lehetővé teszi a hozzáférés-ellenőrzéseket. "
  * Megoldás  
    Győződjön meg arról, hogy az *NT AUTHORITY\SYSTEM* el tudja érni a SQL Server. Lásd: SAP-Megjegyzés [2562184]

### <a name="errors-and-warnings-during-a-system-clone"></a>Hibák és figyelmeztetések a rendszer klónozásakor

* Hiba történt, amikor a rendszer megpróbálta regisztrálni a példány-ügynököt az alkalmazáskiszolgáló vagy a ASCS- *példány kényszerített regisztrálási és indítási példány* -ügynöke számára.
  * Hiba történt a példány-ügynök regisztrálására tett kísérlet során. (RemoteException: ' Nem sikerült betölteni a példány adatait a (\\z) AS1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0 profilból:  Cannot access profile '\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0': Nincs ilyen fájl vagy könyvtár. ')
  * Megoldás  
   Győződjön meg arról, hogy a ASCS/SCS sapmnt-megosztásának teljes hozzáférése van a SAP_AS1_GlobalAdmin

* Hiba a lépésben az *indítási védelem engedélyezése a klónozáshoz*
  * Nem sikerült megnyitni a (\\z) "AS1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0" fájlt, mert: Nincs ilyen fájl vagy könyvtár
  * Megoldás  
    Az alkalmazáskiszolgáló számítógépfiókja írási hozzáféréssel kell rendelkeznie a profilhoz

### <a name="errors-and-warnings-during-create-system-replication"></a>Hibák és figyelmeztetések a rendszerreplikáció létrehozása során

* Kivétel a rendszerreplikáció létrehozása lehetőségre való kattintáskor
  * Okozta: com. SAP. NW. LM. ACI. Engine. Base. API. util. Exception. HAOperationException Calling '/usr/SAP/hostctrl/exe/sapacext-a ShowHanaBackups-m HN1-f 50-h HN1-db-o Level =\;0 status =\;5 port = 35013 PF =/usr/SAP/hostctrl /exe/host_profile-R-T dev_lvminfo-u SYSTEM-p Hook-R ' | /usr/SAP/hostctrl/exe/sapacext-a ShowHanaBackups-m HN1-f 50-h HN1-db-o szint = 0\;állapot = 5\;port = 35013 PF =/usr/SAP/hostctrl/exe/host_profile-R-T dev_lvminfo-u rendszer-p Hook-R
  * Megoldás  
    Tesztelés, ha a sapacext végrehajtható > `<hanasid`adm-ként

* Hiba, ha a teljes másolás nincs engedélyezve a tárolási lépésben
  * Hiba történt az elérési út IStorageCopyData. storageVolumeCopyList: 1 és mező targetStorageSystemId tartozó környezeti attribútum üzenetének jelentésekor.
  * Megoldás  
    Hagyja figyelmen kívül a lépés figyelmeztetéseit, és próbálkozzon újra. Ezt a problémát az SAP láma új támogatási csomagja/javítása határozza meg.

### <a name="errors-and-warnings-during-relocate"></a>Hibák és figyelmeztetések az áthelyezés során

* Az "/usr/sap/AH1" elérési út nem engedélyezett az NFS újraexportálásához.
  * A részletekért tekintse meg az SAP Megjegyzés [2628497] .
  * Megoldás  
    ASCS-exportálás hozzáadása a ASCS HostAgent-profilhoz. Lásd: SAP-Megjegyzés [2628497]

* A függvény nincs implementálva a ASCS áthelyezése során
  * Parancs kimenete: exportfs: gazdagép:/usr/SAP/AX1: A függvény nincs implementálva
  * Megoldás  
    Győződjön meg arról, hogy az NFS-kiszolgáló szolgáltatás engedélyezve van a cél virtuális gép áthelyezéséhez

### <a name="errors-and-warnings-during-application-server-installation"></a>Hibák és figyelmeztetések az alkalmazáskiszolgáló telepítésekor

* Hiba történt a SAPinst lépés végrehajtásakor: getProfileDir
  * HIBA: (A lépés által jelentett utolsó hiba: Megfogott ESAPinstException a modulban – hívás: A következő lépés validatorja: "| NW_DI | ind | ind | ind | ind | 0 | 0 | NW_GetSidFromProfiles | ind | ind | ind | ind | getSid | 0 | NW_readProfileDir | ind | ind | ind | ind | readProfile | 0 | getProfileDir "hibát jelzett: A \\csomópont \as1-ascs\sapmnt\AS1\SYS\profile nem létezik. A probléma megoldásához interaktív módban indítsa el a SAPinst.
  * Megoldás  
    Győződjön meg arról, hogy a SWPM olyan felhasználóval fut, amely hozzáfér a profilhoz. Ez a felhasználó konfigurálható az alkalmazáskiszolgáló telepítővarázslója

* Hiba történt a SAPinst lépés végrehajtásakor: askUnicode
  * HIBA: (A lépés által jelentett utolsó hiba: Megfogott ESAPinstException a modulban – hívás: A következő lépés validatorja: "| NW_DI | ind | ind | ind | ind | 0 | 0 | NW_GetSidFromProfiles | ind | ind | ind | ind | getSid | 0 | NW_getUnicode | ind | ind | ind | ind | Unicode | 0 | askUnicode "hibát jelzett: A probléma megoldásához interaktív módban indítsa el a SAPinst.
  * Megoldás  
    Ha egy legutóbbi SAP-kernelt használ, a SWPM nem tudja megállapítani, hogy a rendszer egy Unicode rendszer-e már a ASCS lévő Message Server használatával. További részletekért lásd az SAP Megjegyzés [2445033] .  
    Ezt a problémát az SAP láma új támogatási csomagja/javítása határozza meg.  
    A probléma megkerüléséhez állítsa be a OS_UNICODE = UC profilt az SAP-rendszer alapértelmezett profiljába.

* Hiba történt a SAPinst lépés végrehajtásakor: dCheckGivenServer
  * Hiba történt a SAPinst lépés végrehajtásakor: dCheckGivenServer "version =" 1.0 "hiba: (A következő lépés által jelentett utolsó hiba \<: p > a telepítést a felhasználó megszakította. \</p>
  * Megoldás  
    Győződjön meg arról, hogy a SWPM olyan felhasználóval fut, amely hozzáfér a profilhoz. Ez a felhasználó konfigurálható az alkalmazáskiszolgáló telepítővarázslója

* Hiba történt a SAPinst lépés végrehajtásakor: checkClient
  * Hiba történt a SAPinst lépés végrehajtásakor: checkClient "version =" 1.0 "hiba: (A következő lépés által jelentett utolsó hiba \<: p > a telepítést a felhasználó megszakította. \</p>)
  * Megoldás  
    Győződjön meg arról, hogy az SQL Server Microsoft ODBC-illesztőprogramja telepítve van azon a virtuális gépen, amelyre telepíteni kívánja az alkalmazáskiszolgáló szolgáltatást

* Hiba történt a SAPinst lépés végrehajtásakor: copyScripts
  * A lépés által jelentett utolsó hiba: A rendszerhívás sikertelen. RÉSZLETEK 13. hiba (0x0000000d) (engedély megtagadva) a (z) "fopenU" rendszerhívás végrehajtásakor a következő paraméterrel: (\\\ AS1-ASCs/sapmnt/AS1/sys/exe/UC/NTAMD64/strdbs. cmd, w), vonal (494) a fájlban (\ Bas/Bas/749_REL/bc_749_REL/src/ins/SAPINST/Impl/src/syslib /filesystem/syxxcfstrm2.cpp), verem nyomkövetése:  
  CThrThread. cpp: 85: CThrThread::threadFunction()  
  CSiServiceSet.cpp: 63: CSiServiceSet::executeService()  
  CSiStepExecute.cpp: 913: CSiStepExecute::execute()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase:: Call ()  
  iaxxcfile.cpp: 183: iastring CIaOsFileConnect:: callMemberFunction (iastring CONST & neve, args_t CONST & ARG)  
  iaxxcfile.cpp: 1849: iastring CIaOsFileConnect:: newFileStream (args_t CONST & _args)  
  iaxxbfile.cpp: 773: CIaOsFile::newFileStream_impl(4)  
  syxxcfile.cpp: 233: CSyFileImpl::openStream(ISyFile::eFileOpenMode)  
  syxxcfstrm.cpp: 29 CSyFileStreamImpl::CSyFileStreamImpl(CSyFileStream*,iastring,ISyFile::eFileOpenMode)  
  syxxcfstrm.cpp: 265: CSyFileStreamImpl::open()  
  syxxcfstrm2.cpp: 58: CSyFileStream2Impl:: CSyFileStream2Impl (const CSyPath & \\\ aw1-ASCs/sapmnt/aw1/sys/exe/UC/NTAMD64/strdbs. cmd, 0x4)  
  syxxcfstrm2.cpp: 456: CSyFileStream2Impl::open()
  * Megoldás  
    Győződjön meg arról, hogy a SWPM olyan felhasználóval fut, amely hozzáfér a profilhoz. Ez a felhasználó konfigurálható az alkalmazáskiszolgáló telepítővarázslója

* Hiba történt a SAPinst lépés végrehajtásakor: askPasswords
  * A lépés által jelentett utolsó hiba: A rendszerhívás sikertelen. RÉSZLETEK 5. hiba (0x00000005) (a hozzáférés megtagadva.) a (z) "NetValidatePasswordPolicy" rendszerhívás végrehajtásakor paraméterrel (...) 359, a (z) (\ Bas/Bas/749_REL/bc_749_REL/src/ins/SAPINST/Impl/src/syslib/Account/synxcaccmg. cpp) fájlban a verem nyomkövetése:  
  CThrThread. cpp: 85: CThrThread::threadFunction()  
  CSiServiceSet.cpp: 63: CSiServiceSet::executeService()  
  CSiStepExecute.cpp: 913: CSiStepExecute::execute()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase:: Call ()  
  CSiStepExecute.cpp: 764: CSiStepExecute::invokeDialog()  
  DarkModeGuiEngine.cpp: 56: DarkModeGuiEngine::showDialogCalledByJs()  
  DarkModeDialog.cpp: 85: DarkModeDialog::submit()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase:: Call ()  
  iaxxcaccount. cpp: 107: iastring CIaOsAccountConnect:: callMemberFunction (iastring CONST & neve, args_t CONST & ARG)  
  iaxxcaccount. cpp: 1186: iastring CIaOsAccountConnect:: validatePasswordPolicy (args_t CONST & _args)  
  iaxxbaccount.cpp: 430: CIaOsAccount::validatePasswordPolicy_impl()  
  synxcaccmg.cpp: 297: ISyAccountMgt::PasswordValidationMessage CSyAccountMgtImpl::validatePasswordPolicy(saponazure,*****) const )
  * Megoldás  
    Ügyeljen arra, hogy a virtuális gép és a  tartományvezérlő közötti kommunikáció engedélyezéséhez adjon hozzá egy gazdagép-szabályt a lépés elkülönítése beállításhoz.

## <a name="next-steps"></a>További lépések
* [SAP HANA az Azure-műveletek útmutatójában][hana-ops-guide]
* [Azure Virtual Machines az SAP tervezéséhez és megvalósításához][planning-guide]
* [Azure Virtual Machines üzembe helyezés az SAP-ban][deployment-guide]
* [Azure Virtual Machines adatbázis-kezelői telepítés az SAP-hoz][dbms-guide]

---
title: Üzembe helyezési útmutató Windows SQL Server rendszerű virtuális gépekhez a Azure Portalban | Microsoft Docs
description: Ez a útmutató a Windows SQL Server 2017 rendszerű virtuális gépek Azure Portal való létrehozásának lehetőségeit ismerteti.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: 1aff691f-a40a-4de2-b6a0-def1384e086e
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 05/04/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b1fa24f919888e4454096e1ef84d2ba2948b865a
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774294"
---
# <a name="how-to-provision-a-windows-sql-server-virtual-machine-in-the-azure-portal"></a>Windows SQL Server virtuális gép kiépítése a Azure Portal

Ez az útmutató részletesen ismerteti a Windows SQL Server virtuális gép Azure Portal-ban való létrehozásakor elérhető különböző lehetőségeket. Ez a cikk több konfigurációs lehetőséget is tartalmaz, mint a [SQL Server VM](quickstart-sql-vm-create-portal.md)rövid útmutató, amely az egyik lehetséges kiépítési feladattal többet mutat be. 

Az útmutató segítségével saját SQL Server VM hozhat létre. Vagy használja hivatkozásként a Azure Portal elérhető lehetőségeire.

> [!TIP]
> Ha kérdése van az SQL Servert futtató virtuális gépek használatával kapcsolatban, tekintse meg a [gyakori kérdéseket](virtual-machines-windows-sql-server-iaas-faq.md).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a id="select"></a>SQL Server virtuálisgép-katalógus rendszerképei

SQL Server virtuális gép létrehozásakor kiválaszthatja a virtuális gépek katalógusában található számos előre konfigurált rendszerkép egyikét. A következő lépések bemutatják, hogyan választhatja ki a SQL Server 2017 rendszerképek egyikét.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) a fiók használatával.

1. Az Azure Portalon kattintson az **Erőforrás létrehozása** gombra. A portál megnyitja az **Új** ablakot.

1. Az **Új** ablakban kattintson a **Számítás**, majd az **Összes megjelenítése** elemre.

1. A keresőmezőbe írja be az **SQL Server 2017** kifejezést, majd nyomja le az ENTER billentyűt.

1. A szűrő legördülő menüben válassza a _Windows Server 2016_ lehetőséget az **operációs rendszer** számára, és válassza a _Microsoft_ lehetőséget **közzétevőként**. 

     ![Új számítás ablak](./media/virtual-machines-windows-portal-sql-server-provision/azure-new-compute-blade.png)

1. Tekintse át az elérhető SQL Server-rendszerképeket. Minden rendszerkép egy SQL Server-verziót és egy operációs rendszert azonosít.

1. Válassza ki az ingyenes **SQL Server licenc nevű rendszerképet: SQL Server 2017 fejlesztői Windows Server 2016**rendszeren.

   > [!TIP]
   > A fejlesztői kiadás ebben az útmutatóban van használatban, mivel ez az SQL Server teljes funkcionalitású, ingyenes kiadása a fejlesztési teszteléshez. Csak a virtuális gép futtatásával járó költségeket kell kifizetni. Azonban szabadon választhatja ki az útmutatóban használni kívánt rendszerképeket. Az elérhető lemezképek leírását a [SQL Server Windows Virtual Machines áttekintésében](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo)találhatja meg.

   > [!TIP]
   > A SQL Server licencelési költségei a létrehozott virtuális gép másodpercenkénti díjszabásában vannak beépítve, és a kiadás és a magok alapján változnak. A SQL Server Developer Edition azonban ingyenes fejlesztési/tesztelési célokra (éles környezetben), az SQL Express pedig ingyenesen használható a könnyű számítási feladatokhoz (kevesebb, mint 1 GB memória, kevesebb mint 10 GB tárterület). Saját licencét (BYOL) is megadhatja, és csak a virtuális gép számára kell fizetnie. Az ilyen rendszerképek nevei {BYOL} előtagot kapnak. 
   >
   > A lehetőségekkel kapcsolatos további információkért tekintse meg [az SQL Server Azure virtuális gépek díjszabási útmutatóját](virtual-machines-windows-sql-server-pricing-guidance.md).

1. Ellenőrizze, hogy a **Telepítési modell kiválasztása** alatt a **Resource Manager** van-e kiválasztva. Az új virtuális gépek esetén az ajánlott üzemi modell a Resource Manager. 

1. Kattintson a **Létrehozás** gombra.


## <a id="configure"></a>Konfigurációs beállítások

SQL Server virtuális gép konfigurálásához több lap is rendelkezésre áll. Ebben az útmutatóban a következőkre fogunk összpontosítani: 

| Lépés | Leírás |
| --- | --- |
| **Alapvető beállítások** |[Az alapvető beállítások konfigurálása](#1-configure-basic-settings) |
| **Választható funkciók** |[Választható funkciók konfigurálása](#2-configure-optional-features) |
| **Az SQL Server beállításai** |[Az SQL Server beállításainak konfigurálása](#3-configure-sql-server-settings) |
| **Felülvizsgálat + létrehozás** | [Az összefoglalás áttekintése](#4-review--create) |

## <a name="1-configure-basic-settings"></a>1. Alapvető beállítások konfigurálása


Az **alapvető beállítások** lapon adja meg a következő információkat:

* A **projekt részletei**területen ellenőrizze, hogy a megfelelő előfizetés van-e kiválasztva. 
*  Az **erőforráscsoport** szakaszban vagy válasszon ki egy meglévő erőforráscsoportot a listából, vagy válassza az **új létrehozása** lehetőséget az új erőforráscsoport létrehozásához. Az erőforráscsoportok az Azure-ban egymáshoz kapcsolódó erőforrásokból (virtuális gépekből, tárfiókokból, virtuális hálózatokból stb.) álló gyűjtemények. 

    ![Subscription](media/quickstart-sql-vm-create-portal/basics-project-details.png)

  > [!NOTE]
  > Az Azure szolgáltatásban futó SQL Server üzemelő példányok tesztelésekor vagy a velük való megismerkedéskor érdemes egy új erőforráscsoportot használni. A tesztelés befejezése után törölje az erőforráscsoportot a virtuális gép és az erőforráscsoporthoz társított összes erőforrás automatikus törléséhez. További információ az erőforráscsoportokkal kapcsolatban: [Azure Resource Manager Overview](../../../azure-resource-manager/resource-group-overview.md) (Az Azure Resource Manager áttekintése).


* A **példány részletei**területen:
    1. Adja meg a **virtuális gép egyedi nevét**.  
    1. Válasszon egy helyet a **régió**számára. 
    1. Ebben az útmutatóban hagyja, hogy a **rendelkezésre állási beállítások** ne legyenek _infrastruktúra_-redundancia megadása. További információ a rendelkezésre állási lehetőségekről: [rendelkezésre állás](../../windows/availability.md). 
    1. A **rendszerkép** listában válassza _az ingyenes SQL Server licenc: SQL Server 2017 fejlesztői Windows Server 2016_rendszeren.  
    1. A virtuális gép méretének **módosításához** válassza az **a2** alapszintű ajánlat lehetőséget. Ne felejtse el megtisztítani az erőforrásokat, ha elkészült velük, hogy elkerülje a váratlan költségeket. Az éles számítási feladatok esetében ajánlott gépméretekért és -konfigurációkért tekintse meg [az SQL Server teljesítményének Azure Virtual Machines szolgáltatásbeli növelésével kapcsolatos ajánlott eljárásokat](virtual-machines-windows-sql-performance.md).

    ![Példány részletei](media/quickstart-sql-vm-create-portal/basics-instance-details.png)

> [!IMPORTANT]
> A **Méret kiválasztása** ablakban megjelenő, becsült havi költségek nem tartalmazzák az SQL Server licencelési költségeit. Ez a becslés a virtuális gép költsége. A SQL Server expressz és fejlesztői kiadásai esetében ez a becsült összeg a teljes becsült díj. Más kiadások esetében tekintse meg a [Windows rendszerű virtuális gépek árképzését ismertető oldalt](https://azure.microsoft.com/pricing/details/virtual-machines/windows/), és válassza ki az SQL Server megfelelő kiadását. Tekintse [meg a SQL Server Azure](virtual-machines-windows-sql-server-pricing-guidance.md) -beli virtuális gépekkel és- [méretekkel kapcsolatos](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)díjszabási útmutatót is.

* A **rendszergazdai fiók**területen adja meg a felhasználónevet és a jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../../windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Rendszergazdai fiók](media/quickstart-sql-vm-create-portal/basics-administrator-account.png)

* A **bejövő portszabályok**területen válassza a **kijelölt portok engedélyezése** lehetőséget, majd válassza az **RDP (3389)** lehetőséget a legördülő menüből. 

   ![Bejövőport-szabályok](media/quickstart-sql-vm-create-portal/basics-inbound-port-rules.png)


## <a name="2-configure-optional-features"></a>2. Opcionális funkciók konfigurálása

### <a name="disks"></a>Lemezek

A **lemezek** lapon adja meg a lemez beállításait. 

* Az **operációsrendszer-lemez típusa**területen válassza ki az operációs rendszerhez használni kívánt lemez típusát a legördülő menüből. A prémium üzemi rendszerek esetében ajánlott, de alapszintű virtuális gép esetén nem érhető el. A prémium SSD kihasználása érdekében módosítsa a virtuális gép méretét. 
* A **speciális**területen válassza az **igen** lehetőséget a **Managed Disks**használata elemnél.

   > [!NOTE]
   > A Microsoft a Managed Disks használatát javasolja az SQL Server esetében. A Managed Disks szolgáltatás a háttérben kezeli a tárterületet. Emellett ha ugyanabban a rendelkezésre állási csoportban több, a Managed Diskset használó virtuális gép található, az Azure elosztja a tárolási erőforrásokat, hogy megfelelő redundanciát biztosítson. További információ: [Azure Managed Disks áttekintése] [.. /managed-disks-overview.md). A felügyelt lemezekkel kapcsolatos részletek a rendelkezésre állási csoportokban: [felügyelt lemezek használata a rendelkezésre állási csoportba tartozó virtuális gépekhez] (.. /manage-availability.md.

![Az SQL virtuális gép lemezének beállításai](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-disks.png)
  
  
### <a name="networking"></a>Hálózat

A **hálózatkezelés** lapon konfigurálja a hálózati beállításokat. 

* Hozzon létre egy új **virtuális hálózatot**, vagy használjon egy meglévő vNet a SQL Server VMhoz. Jelölje ki az alhálózatot is. 

* A **NIC hálózati biztonsági csoport**területen válassza az alapszintű biztonsági csoport vagy a speciális biztonsági csoport lehetőséget. Az alapszintű beállítás választása lehetővé teszi a bejövő portok kiválasztását a SQL Server VM számára (az alaplapon megegyező értékeket). A speciális beállítás kiválasztásával választhat egy meglévő hálózati biztonsági csoportot, vagy létrehozhat egy újat. 

* Más módosításokat is végezhet a hálózati beállításokon, vagy megtarthatja az alapértelmezett értékeket.

![Az SQL VM hálózatkezelési beállításai](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-networking.png)

#### <a name="monitoring"></a>Figyelés

A **figyelés** lapon konfigurálja a figyelést és az automatikus leállítást. 

* Az Azure alapértelmezés szerint a virtuális gép számára kijelölt Storage-fiókkal teszi lehetővé a rendszerindítási **diagnosztika** használatát. Itt módosíthatja ezeket a beállításokat, valamint engedélyezheti az **operációs rendszer vendégének diagnosztikaát**is. 
* Ezen a lapon is engedélyezheti a **rendszerhez rendelt felügyelt identitást** és automatikus leállítást. 

![SQL VM-felügyeleti beállítások](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-management.png)


## <a name="3-configure-sql-server-settings"></a>3. SQL Server beállítások konfigurálása

A **SQL Server beállítások** lapon adja meg a SQL Server adott beállításait és optimalizálási lehetőségeit. A SQL Server konfigurálható beállítások közé tartoznak a következők:



| Beállítás |
| --- |
| [Kapcsolatok](#connectivity) |
| [Hitelesítés](#authentication) |
| [Azure Key Vault-integráció](#azure-key-vault-integration) |
| [Tároló konfigurálása](#storage-configuration) |
| [Automatikus javítás](#automated-patching) |
| [Automatikus biztonsági mentés](#automated-backup) |
| [R Services (speciális analitikai)](#r-services-advanced-analytics) |


### <a name="connectivity"></a>Kapcsolat

Az **SQL kapcsolatok** alatt adja meg, milyen típusú hozzáférést szeretne az ezen a virtuális gépen futó SQL Server-példányhoz. A jelen útmutatóban válassza a **nyilvános (Internet)** lehetőséget, hogy lehetővé tegye a kapcsolódást az interneten található gépekről vagy szolgáltatásokból SQL Server. Ha ez a beállítás be van jelölve, az Azure automatikusan konfigurálja a tűzfalat és a hálózati biztonsági csoportot, hogy engedélyezze a forgalmat a kiválasztott porton.

> [!TIP]
> Alapértelmezés szerint az SQL Server a jól ismert **1433-as** portot figyeli. A nagyobb biztonság érdekében az előző párbeszédpanelen módosítsa a portot egy nem alapértelmezett, például az 1401-es portra. Ha módosítja a portot, az adott portot bármely ügyféleszközök, például a SSMS használatával kell összekötnie.

![SQL virtuális gép biztonsága](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-security.png)

Ahhoz, hogy az SQL Serverhez az interneten keresztül csatlakozni lehessen, engedélyeznie kell az SQL Server-hitelesítést is, aminek ismertetését a következő szakaszban olvashatja.

Ha nem szeretné engedélyezni az adatbázis-alrendszerhez az interneten keresztül való csatlakozást, válassza az alábbi lehetőségek valamelyikét:

* A **Helyi (csak virtuális gépen belül)** beállítással az SQL Serverhez csak a virtuális gépen belülről lehet csatlakozni.
* A **Magánjellegű (virtuális hálózaton belül)** beállítással az SQL Serverhez az azonos virtuális hálózaton található gépek és szolgáltatások csatlakozhatnak.

Általánosságban elmondható, hogy a forgatókönyv által lehetővé tett legszigorúbb kapcsolódási korlátozás kiválasztásával növelhető a biztonság. Hálózati biztonsági csoportszabályok és SQL-/Windows-hitelesítés használatával azonban mindegyik lehetőség biztosítható. A hálózati biztonsági csoportot a virtuális gép létrehozása után szerkesztheti. További információkért lásd [az SQL Server Azure-beli virtuális gépeken történő futtatásának biztonsági szempontjait](virtual-machines-windows-sql-security.md).



### <a name="authentication"></a>Authentication

Ha SQL Server hitelesítésre van szüksége, az **SQL Server beállítások** lapon kattintson az **SQL-hitelesítés** alatt az **Engedélyezés** elemre.

![SQL Server-hitelesítés](./media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-authentication.png)

> [!NOTE]
> Ha az interneten keresztül szeretné elérni SQL Server (a nyilvános kapcsolódási lehetőséggel), akkor itt kell engedélyeznie az SQL-hitelesítést. Az SQL Serverhez való nyilvános hozzáféréshez SQL-hitelesítésre van szükség.

Ha engedélyezi az SQL Server-hitelesítést, adjon meg egy **bejelentkezési nevet** és egy **jelszót**. Ez a bejelentkezési név SQL Server hitelesítési Bejelentkezési azonosítóként van konfigurálva, és tagja a **sysadmin** rögzített kiszolgálói szerepkörnek. További információkat a hitelesítési módokról [a hitelesítési mód kiválasztását leíró](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode) cikkben talál.

Ha nem engedélyezi az SQL Server-hitelesítést, akkor a helyi virtuális gépen lévő rendszergazdai fiók használatával is csatlakozhat az SQL Server-példányhoz.


### <a name="azure-key-vault-integration"></a>Azure Key Vault-integráció

Ha biztonsági titkot szeretne tárolni az Azure-ban a titkosításhoz, válassza a **SQL Server beállításokat**, majd görgessen le az **Azure Key Vault-integráció**elemre. Válassza az **Engedélyezés** lehetőséget, majd adja meg a kért információkat. 

![Azure Key Vault-integráció](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-akv.png)

A következő táblázat tartalmazza az Azure Key Vault-integráció konfigurálásához szükséges paramétereket.

| PARAMÉTER | LEÍRÁS | PÉLDA |
| --- | --- | --- |
| **Key Vault URL** |A Key Vault helye. |https:\//contosokeyvault.Vault.Azure.net/ |
| **Egyszerű név** |Az Azure Active Directory szolgáltatás egyszerű neve. Ezt a nevet ügyfél-azonosítónak is hívják. |fde2b411-33d5-4e11-af04eb07b669ccf2 |
| **Egyszerű titok** |Az Azure Active Directory szolgáltatás egyszerű titka. Ezt a titkot ügyféltitoknak is hívják. |9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM= |
| **Hitelesítő adat neve** |**Hitelesítő adat neve**: A AKV-integráció egy hitelesítő adatot hoz létre SQL Serveron belül, így a virtuális gép hozzáférhet a kulcstartóhoz. Válasszon egy nevet ennek a hitelesítő adatnak. |mycred1 |

További információkért lásd: [Configure Azure Key Vault Integration for SQL Server on Azure VMs](virtual-machines-windows-ps-sql-keyvault.md) Az Azure Key Vault-integráció konfigurálása az SQL Serverhez Azure virtuális gépeken.

### <a name="storage-configuration"></a>Tároló konfigurálása

A **SQL Server beállítások** lap **tárolási konfiguráció**területén válassza a **konfiguráció módosítása** lehetőséget a tárolási követelmények megadásához.


> [!NOTE]
> Ha manuálisan konfigurálta a virtuális gépet a Standard szintű tárolás használatára, ez a beállítás nem érhető el. Az automatikus tárolóoptimalizálás csak a prémium szintű Storage esetében érhető el.

> [!TIP]
> Az egyes csúszkák lépéseinek száma és felső határértéke a kiválasztott virtuális gép méretétől függ. A nagyobb méretű és teljesítményű virtuális gépeken nagyobb mértékű vertikális felskálázás végezhető.

Megadhat különböző követelményeket, például a kimeneti/bemeneti műveletek másodpercenkénti számát (IOPS), a MB/másodpercben megadott átviteli sebességet, illetve a tárterület teljes méretét. Ezeket az értékeket a csúszkákkal állíthatja be. Ezek a tárolóbeállítások a számítási feladatok mennyiségéhez igazodva módosíthatók. A portál ezen követelmények alapján automatikusan kiszámítja a csatlakoztatandó és konfigurálandó lemezek számát.

A **Storage optimized for** (Tároló optimalizálási módja) területen válasszon az alábbi lehetőségek közül:

* A **General** (Általános) az alapértelmezett beállítás, amely a legtöbb számítási feladatot támogatja.
* A **Transactional** (Tranzakciós) feldolgozás a tárolót az adatbázisok hagyományos OLTP számítási feladataira optimalizálja.
* A **Data warehousing** (Adatraktározás) beállítás elemzési és jelentéskészítési számítási feladatokra optimalizálja a tárolót.

![SQL virtuális gép tárolójának konfigurációja](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-storage-configuration.png)

### <a name="sql-server-license"></a>SQL Server-licenc
Ha frissítési garanciával rendelkező ügyfelet használ, használhatja a [Azure Hybrid Benefitt](https://azure.microsoft.com/pricing/hybrid-benefit/) saját SQL Server licenc használatára, és mentheti az erőforrásokat. 

![SQL VM-licenc](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-license.png)

### <a name="automated-patching"></a>Javítások automatikus telepítése

Az **Automatikus javítás** alapértelmezés szerint engedélyezve van. Az automatizált javítás lehetővé teszi, hogy az Azure automatikus javításokat alkalmazzon az SQL Serveren és az operációs rendszeren. A karbantartási időszak beállításához adja meg a hét egy napját, egy időpontot és egy időtartamot. Az Azure ebben a karbantartási időszakban végzi el a javításokat. A karbantartási időszak ütemezése a virtuális gép területi beállítása szerinti időt használja. Ha nem szeretné, hogy az Azure automatikusan alkalmazza a javításokat az SQL Serveren és az operációs rendszeren, kattintson a **Letiltás** elemre.  

![SQL virtuális gép automatikus javítása](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-automated-patching.png)

További információk: [Automated Patching for SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-automated-patching.md) (Az SQL Server automatikus javítása Azure virtuális gépeken).

### <a name="automated-backup"></a>Automatikus biztonsági mentés

Az **Automatikus biztonsági mentés** területen engedélyezheti az összes adatbázis automatikus mentését. Az automatikus biztonsági mentés alapértelmezés szerint le van tiltva.

Az SQL automatikus biztonsági mentésének engedélyezésekor konfigurálhatja a következő beállításokat:

* Biztonsági mentések megőrzési ideje (napokban)
* A biztonsági mentésekhez használt tárfiók
* Titkosítási beállítás és a biztonsági mentések jelszavas védelme
* Rendszeradatbázisok biztonsági mentése
* Biztonsági mentések ütemezésének konfigurálása

A biztonsági mentés titkosításához kattintson az **Engedélyezés** elemre. Ezután adja meg a **Jelszót**. Az Azure a biztonsági mentések titkosításához létrehoz egy tanúsítványt, amelyet a megadott jelszóval véd. Alapértelmezés szerint az ütemterv automatikusan be van állítva, de manuális ütemtervet is létrehozhat. 

![SQL virtuális gép automatikus biztonsági mentései](media/virtual-machines-windows-portal-sql-server-provision/automated-backup.png)

További információk: [Automated Backup for SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-automated-backup.md) (Az SQL Server automatikus biztonsági mentése Azure virtuális gépeken).


### <a name="r-services-advanced-analytics"></a>R Services (speciális analitikai)

Lehetősége van a [SQL Server R Services (fejlett Analitika)](/sql/advanced-analytics/r/sql-server-r-services/)engedélyezésére. Ez a beállítás lehetővé teszi a fejlett Analitika használatát a SQL Server 2017 használatával. Az **SQL Server beállítások** ablakban válassza az **Engedélyezés** lehetőséget.


## <a name="4-review--create"></a>4. Áttekintés + létrehozás

A **felülvizsgálat + létrehozás** lapon tekintse át az összegzést, és válassza a **Létrehozás** lehetőséget a virtuális géphez megadott SQL Server, erőforráscsoport és erőforrások létrehozásához.

Az üzemelő példány az Azure Portalról monitorozható. A képernyő felső részén látható **Értesítések** gomb megjeleníti az üzemelő példány állapotának alapvető információit.

> [!NOTE]
> Az üzembe helyezési idők szemléltetéséhez üzembe helyeztem egy SQL virtuális gépet az USA keleti régiójában az alapértelmezett beállításokkal. Ez az üzembe helyezési teszt hozzávetőlegesen 12 percet vett igénybe. A régiótól és a kiválasztott beállításoktól függően Ön ennél rövidebb vagy hosszabb üzembe helyezési időt is tapasztalhat.

## <a id="remotedesktop"></a> A virtuális gép megnyitása a távoli asztallal

A következő lépésekkel csatlakozzon az SQL Server virtuális géphez a távoli asztalról:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Az SQL Server virtuális géphez való csatlakozás után elindíthatja az SQL Server Management Studiót, és a helyi rendszergazdai hitelesítő adataival csatlakozhat Windows-hitelesítés használatával. Ha engedélyezte az SQL Server-hitelesítést, akkor az SQL-hitelesítésen keresztül is csatlakozhat a kiépítés során megadott SQL bejelentkezési azonosítójával és jelszavával.

A géphez való hozzáférés lehetővé teszi, hogy igény szerint közvetlenül módosítsa a gép és az SQL Server beállításait. Például konfigurálhatja a tűzfal beállításait, vagy módosíthatja az SQL Server-konfiguráció beállításait.

## <a id="connect"></a> Távoli csatlakozás az SQL Serverhez

Ebben az útmutatóban a virtuális gép **nyilvános** hozzáférését választotta, és **SQL Server a hitelesítést**. Ezek a beállítások automatikusan úgy konfigurálták a virtuális gépet, hogy az az internetről bármely ügyfél számára engedélyezi az SQL Serverhez való csatlakozást (feltéve, hogy helyes SQL-bejelentkezési névvel rendelkeznek).

> [!NOTE]
> Ha az üzembe helyezés során nem a nyilvános hozzáférést választotta, akkor az üzembe helyezés után a portálon keresztül módosíthatja az SQL csatlakozási beállításait. További információ: [Az SQL csatlakozási beállításainak módosítása](virtual-machines-windows-sql-connect.md#change).

Az alábbi fejezetek bemutatják, hogyan csatlakozhat az interneten keresztül a SQL Server VM-példányához.

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

  > [!NOTE]
  > Ez a példa a Common 1433-as portot használja. Ezt az értéket azonban módosítani kell, ha egy másik portot (például 1401) adott meg a SQL Server VM telepítése során. 


## <a name="next-steps"></a>További lépések

További információ az SQL Server használatáról az Azure-ban: [SQL Server on Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md) (SQL Server az Azure virtuális gépeken) és [Frequently Asked Questions](virtual-machines-windows-sql-server-iaas-faq.md) (Gyakran ismételt kérdések).
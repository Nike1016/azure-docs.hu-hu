---
title: Az Azure-beli Linux rendszerű virtuális gépek karbantartási értesítéseinek feldolgozása | Microsoft Docs
description: Megtekintheti az Azure-ban futó Linux rendszerű virtuális gépek karbantartási értesítéseit, és megkezdheti az önkiszolgáló karbantartást.
services: virtual-machines-linux
documentationcenter: ''
author: shants123
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/30/2019
ms.author: shants
ms.openlocfilehash: c82bacfdff1c9d939016b48735f7917e7d34b47f
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849682"
---
# <a name="handling-planned-maintenance-notifications-for-linux-virtual-machines"></a>A Linux rendszerű virtuális gépek tervezett karbantartási értesítéseinek kezeléséhez

Az Azure rendszeresen végez frissítéseket a virtuális gépeket futtató infrastruktúra megbízhatóságának, teljesítményének és biztonságának javítása érdekében. A frissítések olyan változások, mint az üzemeltetési környezet javítása vagy a hardver frissítése és leszerelése. A frissítések többsége az üzemeltetett virtuális gépekre gyakorolt hatás nélkül kerül végrehajtásra. Vannak azonban olyan esetek, amikor a frissítések hatással vannak a következőkre:

- Ha a karbantartás nem igényel újraindítást, az Azure helyben történő áttelepítés használatával szünetelteti a virtuális gépet a gazdagép frissítése közben. Ezek a nem újraindításra alkalmas karbantartási műveletek tartalék tartományt adnak a tartalék tartománynak, és a rendszer leállítja a folyamatot, ha figyelmeztetési állapot érkezik.

- Ha a karbantartás újraindítást igényel, a karbantartás megtervezése után értesítést kap. Ezekben az esetekben egy olyan időablakot kap, amely általában 30 nap, ahol elindíthatja a karbantartást, ha Ön is működik.


Az újraindítást igénylő tervezett karbantartás hullámokban van ütemezve. Minden egyes hullám különböző hatókörrel (régiókkal) rendelkezik.

- A Wave az ügyfeleknek küldött értesítésekkel kezdődik. Alapértelmezés szerint a rendszer értesítést küld az előfizetés tulajdonosának és a közös tulajdonosoknak. Hozzáadhat további címzetteket és üzenetküldési lehetőségeket, például az e-maileket, az SMS-t és a webhookokat az Azure- [Tevékenységnaplók riasztásait](../../azure-monitor/platform/activity-logs-overview.md)használó értesítésekhez.  
- Az értesítés időpontjában elérhetővé válik egy *önkiszolgáló ablak* . Ez az ablak általában 30 nap alatt megkeresi, hogy a virtuális gépek mely részét foglalja magában ebben a hullámban, és proaktív módon elkezdheti a karbantartást a saját ütemezési igényei szerint.
- Az önkiszolgáló ablak után megkezdődik az *ütemezett karbantartási* időszak. Ezen az időszakon belül az Azure ütemezni fogja a szükséges karbantartást a virtuális gépen. 

Ahhoz, hogy az Azure automatikusan megkezdje a karbantartást, a két Windows rendszernek kell megadnia a karbantartási és a virtuális gépek újraindítását.


A Azure Portal, a PowerShell, a REST API és a CLI segítségével lekérdezheti a virtuális gépek karbantartási ablakait, és elindíthatja az önkiszolgáló karbantartást.

 
## <a name="should-you-start-maintenance-using-during-the-self-service-window"></a>A karbantartást az önkiszolgáló ablakon belül kell elindítani?  

Az alábbi irányelvek segítségével eldöntheti, hogy használja-e ezt a képességet, és a karbantartást a saját időpontjában kell megkezdenie. 

> [!NOTE] 
> Előfordulhat, hogy az önkiszolgáló karbantartás nem érhető el az összes virtuális géphez. Annak megállapításához, hogy elérhető-e az előjelzéses újratelepítés a virtuális géphez, keresse meg a **Start Now** (Karbantartás) állapotot. Az önkiszolgáló karbantartás jelenleg nem érhető el Cloud Services (webes/feldolgozói szerepkör) és Service Fabrichoz.


Az önkiszolgáló karbantartás nem ajánlott a **rendelkezésre állási** csoportok használatával üzemelő példányok esetében, mivel ezek a nagy rendelkezésre állású telepítések, ahol a rendszer csak egy frissítési tartományt érint az adott időpontban. 
- Hagyja, hogy az Azure aktiválja a karbantartást. Az újraindítást igénylő karbantartáshoz vegye figyelembe, hogy a frissítés a tartomány frissítési tartománya alapján történik, hogy a frissítési tartományok nem feltétlenül kapják meg a karbantartást, és hogy a frissítési tartományok között 30 perces szünet van. 
- Ha az egyes kapacitások (1/frissítési tartományok száma) ideiglenes elvesztése aggodalomra ad okot, a karbantartási időszak alatt a további példányok kiosztásával egyszerűen kompenzálható. 
- Az újraindítást nem igénylő karbantartás esetén a frissítések a tartalék tartomány szintjén lesznek alkalmazva. 

**Ne** használja az önkiszolgáló karbantartást az alábbi helyzetekben: 
- Ha a virtuális gépeket gyakran, manuálisan, a DevTest Labs használatával, az automatikus leállítás vagy az ütemterv alapján állítja le, akkor visszaállíthatja a karbantartási állapotot, így további állásidőt eredményezhet.
- A rövid élettartamú virtuális gépeken, amelyeket tudni fog, a karbantartási hullám vége előtt törölni fogjuk. 
- Olyan számítási feladatokhoz, amelyekben a frissítéskor szükséges helyi (ideiglenes) lemezen található nagy állapotú munkaterhelések vannak tárolva. 
- Olyan esetekben, amikor gyakran lakik a virtuális gép, mert visszaállíthatja a karbantartási állapotot. 
- Ha olyan ütemezett eseményeket fogadott el, amelyek lehetővé teszik az előjelzéses feladatátvételt vagy a számítási feladatok zökkenőmentes leállítását, a karbantartási leállítás megkezdése előtt 15 perccel

**Használja** az önkiszolgáló karbantartást, ha azt tervezi, hogy a virtuális gépet az ütemezett karbantartási fázisban megszakítás nélkül futtatja, és a fent említett számlálók egyike sem alkalmazható. 

A következő esetekben ajánlott önkiszolgáló karbantartást használni:
- Pontos karbantartási időszakot kell kommunikálnia a felügyelettel vagy a végfelhasználóval. 
- A karbantartást egy adott dátummal kell végrehajtania. 
- Meg kell határoznia a karbantartási folyamatot, például a többrétegű alkalmazást a biztonságos helyreállítás biztosításához.
- A virtuális gép helyreállítási ideje több mint 30 percet vesz igénybe két frissítési tartomány (frissítési) között. A frissítési tartományok közötti idő szabályozásához a virtuális gépek karbantartását egyszerre egy frissítési tartományon (UD) kell elindítania.



## <a name="find-vms-scheduled-for-maintenance-using-cli"></a>A karbantartásra ütemezett virtuális gépek keresése a CLI használatával

A tervezett karbantartási információk az [Azure VM Get-instance-View](/cli/azure/vm?view=azure-cli-latest)használatával tekinthetők meg.
 
A karbantartási adatokat csak akkor adja vissza a rendszer, ha karbantartási terv van. Ha nincs olyan karbantartási ütemezés, amely hatással van a virtuális gépre, a parancs nem ad vissza karbantartási információt. 

```azure-cli
az vm get-instance-view -g rgName -n vmName
```

A MaintenanceRedeployStatus a következő értékeket adja vissza: 

| Érték | Leírás   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Azt jelzi, hogy most elindítható-e a virtuális gép karbantartása. |
| PreMaintenanceWindowStartTime         | A karbantartás önkiszolgáló ablakának kezdete, ha a virtuális gépen karbantartást kezdeményezhet |
| PreMaintenanceWindowEndTime           | A karbantartási önkiszolgáló ablak befejezése, ha a virtuális gép karbantartását indítja el |
| MaintenanceWindowStartTime            | A karbantartási ütemezett időszak kezdete, amelyben az Azure a virtuális gépen kezdeményezi a karbantartást |
| MaintenanceWindowEndTime              | A karbantartási ütemezett időszak vége, amelyben az Azure karbantartást kezdeményez a virtuális gépen |
| LastOperationResultCode               | A virtuális gép karbantartásának megkezdésére tett legutóbbi kísérlet eredménye |




## <a name="start-maintenance-on-your-vm-using-cli"></a>A virtuális gép karbantartásának elindítása a CLI használatával

A következő hívás kezdeményezi a karbantartást egy virtuális gépen `IsCustomerInitiatedMaintenanceAllowed` , ha a értéke TRUE (igaz).

```azure-cli
az vm perform-maintenance -g rgName -n vmName 
```

[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]

## <a name="classic-deployments"></a>Klasszikus üzembe helyezések

Ha továbbra is a klasszikus üzembe helyezési modellel telepített örökölt virtuális gépekkel rendelkezik, a klasszikus Azure CLI használatával lekérdezheti a virtuális gépeket, és megkezdheti a karbantartást.

Győződjön meg arról, hogy a megfelelő módban van, hogy a klasszikus virtuális gép használatával működjön a következő beírásával:

```
azure config mode asm
```

Egy *myVM*nevű virtuális gép karbantartási állapotának lekéréséhez írja be a következőt:

```
azure vm show myVM 
``` 

A *myVM* nevű klasszikus virtuális gép karbantartásának megkezdéséhez a *myService* szolgáltatásban és a *myDeployment* -telepítésben írja be a következőt:

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```


## <a name="faq"></a>GYIK


**K: Miért van szükség a virtuális gépek újraindítására?**

**V:** Habár az Azure platform frissítéseinek és frissítéseinek többsége nem befolyásolja a virtuális gép rendelkezésre állását, vannak olyan esetek, amikor nem tudjuk elkerülni az Azure-ban üzemeltetett virtuális gépek újraindítását. Több olyan módosítást is összegyűjtöttünk, amelyek a virtuális gépek újraindítását eredményező kiszolgálók újraindítását igénylik.

**K: Ha a rendelkezésre állási csoport segítségével követem a magas rendelkezésre állásra vonatkozó javaslataikat, akkor biztonságban vagyok?**

**V:** A rendelkezésre állási csoportban vagy virtuálisgép-méretezési csoportban üzembe helyezett virtuális gépek frissítési tartományokhoz (Update Domains, UD) tartoznak. A karbantartás végrehajtásakor az Azure tiszteletben tartja az UD korlátozást, és nem indít újra a virtuális gépeket a különböző UD-ból (ugyanazon rendelkezésre állási csoporton belül).  Az Azure a virtuális gépek következő csoportjára való áttérés előtt is legalább 30 percet vár. 

További információ a magas rendelkezésre állásról: [virtuális gépek rendelkezésre állása az Azure-ban](availability.MD).

**K: Hogyan értesítést kap a tervezett karbantartásról?**

**V:** Egy tervezett karbantartási hullám egy vagy több Azure-régióra vonatkozó ütemterv beállításával kezdődik. Hamarosan e-mailben értesítést küldünk az előfizetés tulajdonosainak (egy e-mail-cím/előfizetés). Az értesítéshez tartozó további csatornákat és címzetteket a műveletnapló riasztásai használatával lehet konfigurálni. Ha olyan régióba helyez üzembe egy virtuális gépet, ahol a tervezett karbantartás már ütemezve van, nem fogja tudni megkapni az értesítést, hanem a virtuális gép karbantartási állapotát.

**K: Nem látom a tervezett karbantartás jelét a portálon, a PowerShellben vagy a CLI-ben. mi a baj?**

**V:** A tervezett karbantartással kapcsolatos információk a tervezett karbantartási hullámokban csak azokra a virtuális gépekre vonatkozóan érhetők el, amelyeket a rendszer érint. Más szóval, ha nem jelenik meg az adatai, lehet, hogy a karbantartási hullám már befejeződött (vagy nem indult el), vagy hogy a virtuális gép már egy frissített kiszolgálón található.

**K: Van mód arra, hogy pontosan megtudjam, mikor érinti a virtuális gépet?**

**V:** Az ütemterv beállításakor több napos időablakot határozunk meg. Az ebben az ablakban található kiszolgálók (és virtuális gépek) pontos sorrendje azonban ismeretlen. Azok az ügyfelek, akik szeretnék megismerni a virtuális gépek pontos idejét, használhatnak [ütemezett eseményeket](scheduled-events.md) és lekérdezéseket a virtuális gépen, és 15 perces értesítést kapnak a virtuális gép újraindítása előtt.

**K: Mennyi időt vesz igénybe a virtuális gép újraindítása?**

**V:**  A virtuális gép méretétől függően az újraindítás akár több percet is igénybe vehet az önkiszolgáló karbantartási időszak alatt. Az ütemezett karbantartási időszakban az Azure által kezdeményezett újraindítások során az újraindítás általában körülbelül 25 percet vesz igénybe. Vegye figyelembe, hogy ha Cloud Services (webes/feldolgozói szerepkör), Virtual Machine Scale Sets vagy rendelkezésre állási csoportot használ, a rendszer az ütemezett karbantartási időszak során 30 percet kap az egyes VM-csoportok (UD) között.

**K: Mi a tapasztalata a Virtual Machine Scale Sets esetén?**

**V:** A tervezett karbantartás már elérhető a Virtual Machine Scale Sets számára. Az önkiszolgáló karbantartás elindításával kapcsolatos utasításokért tekintse meg a virtuálisgép-méretezési csoportok [tervezett karbantartását](../../virtual-machine-scale-sets/virtual-machine-scale-sets-maintenance-notifications.md) ismertető dokumentumot.

**K: Mi a tapasztalata Cloud Services (webes/feldolgozói szerepkör) és Service Fabric esetén?**

**V:** Bár a tervezett karbantartás érinti ezeket a platformokat, az ezeket használó ügyfelek biztonsága garantáltnak tekinthető, mivel egy adott időpontban kizárólag egyetlen frissítési tartomány virtuális gépeit érinti a frissítés. Az önkiszolgáló karbantartás jelenleg nem érhető el Cloud Services (webes/feldolgozói szerepkör) és Service Fabrichoz.

**K: Nem látok karbantartási információt a virtuális gépeken. Mi volt a baj?**

**V:** Több oka is van annak, hogy miért nem jelenik meg karbantartási információ a virtuális gépeken:
1.  Microsoft belsőként jelölt előfizetést használ.
2.  A virtuális gépek nincsenek karbantartásra ütemezve. Lehetséges, hogy a karbantartási hullám véget ért, megszakították vagy módosították, így a virtuális gépeket már nem érinti.
3.  Nem rendelkezik a virtuális gépek listájának nézetéhez hozzáadott **karbantartási** oszloppal. Noha ezt az oszlopot hozzáadta az alapértelmezett nézethez, a nem alapértelmezett oszlopok megjelenítésére konfigurált ügyfeleknek manuálisan kell felvenniük a **karbantartási** oszlopot a virtuálisgép-lista nézetbe.

**K: A virtuális gép másodszor is karbantartásra van ütemezve. Hogy miért?**

**V:** Több felhasználási eset van, amikor a virtuális gép a karbantartás utáni ismételt üzembe helyezés után is karbantartásra ütemezettként jelenik meg:
1.  Megszakítottuk a karbantartási hullámot, és egy másik hasznos adattartalommal újraindítottuk. Lehetséges, hogy hibás adattartalmat észlelt, ezért egyszerűen üzembe kell helyezni egy további hasznos adatot.
2.  A virtuális gép  egy hardverhiba miatt meggyógyult egy másik csomópontra.
3.  Azt választotta, hogy leállítja (felszabadítja), majd újraindítja a virtuális gépet.
4.  A virtuális gép **automatikus leállítása** be van kapcsolva.


## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan regisztrálhat a virtuális gépen lévő karbantartási eseményekre [Scheduled Events](scheduled-events.md)használatával.

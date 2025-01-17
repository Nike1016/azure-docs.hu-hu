---
title: Virtuális gépek indítása/leállítása munkaidőn kívül megoldás
description: A Virtuálisgép-felügyeleti megoldás elindítja és leállítja az Azure Resource Manager virtuális gépeit egy ütemezés szerint, és proaktív módon figyeli az Azure Monitor naplóira.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 05/21/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 39ba577580424bf8283d64198bb3068b82869c51
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476876"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Virtuális gépek indítása/leállítása munkaidőn kívül megoldás az Azure Automationben

A gépek indítása/leállítása munkaidőn kívül megoldás elindítja és leállítja az Azure-beli virtuális gépek a felhasználó által definiált ütemezés, nyújt az Azure Monitor naplóira révén és a választható e-maileket küld a [Műveletcsoportok](../azure-monitor/platform/action-groups.md). Azure Resource Manager és klasszikus virtuális gépeket is támogatja a legtöbb forgatókönyvhöz.

> [!NOTE]
> A gépek indítása/leállítása munkaidőn kívül megoldás az Azure-modulokat az Automation-fiókba a megoldás üzembe helyezésekor importált rendszerrel tesztelték. A megoldás jelenleg nem működik az Azure-modul újabb verzióit. Ez csak az Automation-fiókot, amely a virtuális gépek indítása/leállítása munkaidőn kívül megoldás futtatásához használt érinti. Továbbra is használhatja az Azure-modul újabb verzióiban a más Automation-fiókok, a leírtak szerint [az Azure Automationben az Azure PowerShell-modulok frissítése](automation-update-azure-modules.md)

Ez a megoldás a felhasználók számára, akik az saját VM-költségek optimalizálását decentralizált alacsony költségű automation lehetőséget kínál. Ezzel a megoldással a következőket teheti:

- Ütemezze a virtuális gépek indítása és leállítása.
- Virtuális gépek indítása és leállítása növekvő sorrendben (klasszikus virtuális gépek esetében nem támogatott) az Azure-címkék használatával ütemezhető.
- Remote virtuális gépek alacsony processzorhasználat alapján.

Az aktuális megoldáshoz a korlátozások a következők:

- Ez a megoldás kezeli a virtuális gépek minden olyan régióban, de csak akkor használható, az Azure Automation-fiók ugyanabban az előfizetésben.
- Ez a megoldás érhető el az Azure-ban és AzureGov minden olyan régióban, amely támogatja a Log Analytics-munkaterülettel, egy Azure Automation-fiókot és riasztások. AzureGov régiók jelenleg nem támogatja e-mail funkció.

> [!NOTE]
> Ha a megoldás a klasszikus virtuális gépeket használ, majd a virtuális gépek kerül feldolgozásra, egymás után felhőalapú szolgáltatás esetében. Virtuális gépek továbbra is feldolgozása párhuzamosan történik különböző felhőszolgáltatások között.
>
> Az Azure Cloud Solution Provider (az Azure CSP)-előfizetések támogatása csak az Azure Resource Manager modellel, nem az Azure Resource Manager - szolgáltatások nem érhetők el a programban. A indítása és leállítása megoldás futtatásakor hiba jelenhet meg, mert parancsmagok klasszikus erőforrások felügyeletére. CSP kapcsolatos további információkért lásd: [CSP-előfizetésekben elérhető szolgáltatások](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services#comments). Ha egy CSP-előfizetést használ, módosítania kell a [ **External_EnableClassicVMs** ](#variables) változó **hamis** üzembe helyezés után.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Előfeltételek

Az ebben a megoldásban-forgatókönyvek egy [Azure-beli futtató fiók](automation-create-runas-account.md). A futtatófiók az előnyben részesített hitelesítési módszer azért tanúsítványalapú hitelesítést használ, előfordulhat, hogy lejárhat vagy gyakran változhat jelszó helyett.

Javasoljuk, hogy egy önálló Automation-fiókot használja a VM indítása és leállítása megoldások. Ez azért, mert Azure modulverziók gyakran frissül, és a paraméterek változhat. Ezért nem feltétlenül egy újabb verziója az általa használt parancsmagok a VM indítása és leállítása a megoldás nincs frissítve az azonos kiadása ütemben történik. Javasoljuk, hogy a modul frissítések tesztelése egy teszt Automation-fiókban az Automation-fiók éles importálás előtt.

### <a name="permissions-needed-to-deploy"></a>Üzembe helyezéséhez szükséges engedélyek

Vannak bizonyos engedélyeket, amelyek a felhasználó a indítása és leállítása a virtuális gépek üzembe helyezése során óra solution ki kell rendelkeznie. Ezekre az engedélyekre eltérő, ha egy előre létrehozott Automation-fiók és a Log Analytics-munkaterületet használja, vagy üzembe helyezés során újakat hozna létre. Ha az előfizetés közreműködő és a egy globális rendszergazda az Azure Active Directory-bérlőben, nem kell az alábbi engedélyek beállításához. Ha nem rendelkezik, ezeket a jogokat, vagy egy egyéni szerepkör konfigurálnia kell, lásd az alábbi szükséges engedélyek.

#### <a name="pre-existing-automation-account-and-log-analytics-account"></a>Már meglévő Automation-fiók és a Log Analytics-fiók

A gépek indítása/leállítása közben óra solution ki egy Automation-fiók és a megoldás üzembe helyezése a felhasználó az alábbi engedélyekkel kell rendelkeznie a Log Analytics központi telepítése a **erőforráscsoport**. Szerepkörök kapcsolatos további információkért lásd: [egyéni szerepkörök az Azure-erőforrások](../role-based-access-control/custom-roles.md).

| Engedély | Scope|
| --- | --- |
| Microsoft.Automation/automationAccounts/read | Erőforráscsoport |
| Microsoft.Automation/automationAccounts/variables/write | Erőforráscsoport |
| Microsoft.Automation/automationAccounts/schedules/write | Erőforráscsoport |
| Microsoft.Automation/automationAccounts/runbooks/write | Erőforráscsoport |
| Microsoft.Automation/automationAccounts/connections/write | Erőforráscsoport |
| Microsoft.Automation/automationAccounts/certificates/write | Erőforráscsoport |
| Microsoft.Automation/automationAccounts/modules/write | Erőforráscsoport |
| Microsoft.Automation/automationAccounts/modules/read | Erőforráscsoport |
| Microsoft.automation/automationAccounts/jobSchedules/write | Erőforráscsoport |
| Microsoft.Automation/automationAccounts/jobs/write | Erőforráscsoport |
| Microsoft.Automation/automationAccounts/jobs/read | Erőforráscsoport |
| Microsoft.OperationsManagement/solutions/write | Erőforráscsoport |
| Microsoft.OperationalInsights/workspaces/* | Erőforráscsoport |
| Microsoft.Insights/diagnosticSettings/write | Erőforráscsoport |
| Microsoft.Insights/ActionGroups/Write | Erőforráscsoport |
| Microsoft.Insights/ActionGroups/read | Erőforráscsoport |
| Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoport |
| Microsoft.Resources/deployments/* | Erőforráscsoport |

#### <a name="new-automation-account-and-a-new-log-analytics-workspace"></a>Új Automation-fiók és a egy új Log Analytics-munkaterület

A indítása és leállítása a virtuális gépek üzembe helyezése során csúcsidőn kívüli órákra, egy új Automation-fiók és a Log Analytics-munkaterületet a megoldás üzembe helyezése a felhasználó megoldást engedélyre van szüksége a meghatározott az előző szakaszban, valamint a következő engedélyekkel:

- Társadminisztrátor előfizetéshez – ezt csak akkor van szükség a klasszikus futtató fiók létrehozása
- Része lehet a [Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md) **alkalmazásfejlesztő** szerepkör. Futtató fiókok konfigurálásával kapcsolatos további részletekért lásd: [engedélyek konfigurálása a futtató fiókok](manage-runas-account.md#permissions).
- Az előfizetés vagy a következő engedélyeket a közreműködő.

| Engedély |Scope|
| --- | --- |
| Microsoft.Authorization/Operations/read | Előfizetés|
| Microsoft.Authorization/permissions/read |Előfizetés|
| Microsoft.Authorization/roleAssignments/read | Előfizetés |
| Microsoft.Authorization/roleAssignments/write | Előfizetés |
| Microsoft.Authorization/roleAssignments/delete | Előfizetés |
| Microsoft.Automation/automationAccounts/connections/read | Erőforráscsoport |
| Microsoft.Automation/automationAccounts/certificates/read | Erőforráscsoport |
| Microsoft.Automation/automationAccounts/write | Erőforráscsoport |
| Microsoft.OperationalInsights/workspaces/write | Erőforráscsoport |

## <a name="deploy-the-solution"></a>A megoldás üzembe helyezése

Virtuális gépek indítása/leállítása munkaidőn kívül megoldás az Automation-fiók hozzáadása a következő lépésekkel, és a változók a megoldás testreszabásához konfigurálja.

1. Válasszon egy Automation-fiók **indítása és leállítása a virtuális gép** alatt **kapcsolódó erőforrások**. Itt kattintson **tudjon meg többet, és a megoldás engedélyezéséhez**. Ha már üzembe helyezett megoldás indítása és leállítása a virtuális gép, is kiválaszthatja, ehhez kattintson **kezelheti a megoldást** , és megkeresi a listában.

   ![Automation-fiók engedélyezése](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Bárhol is létrehozhat az Azure Portalon kattintva **erőforrás létrehozása**. A piactér oldalon írjon be egy kulcsszót például **Start** vagy **indítása és leállítása**. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Azt is megteheti írja be a teljes neve, a megoldás egy vagy több kulcsszót, és nyomja le az ENTER billentyűt. Válassza ki **virtuális gépek indítása/leállítása munkaidőn kívül** a keresési eredmények közül.

2. Az a **virtuális gépek indítása/leállítása munkaidőn kívül** lapon a kiválasztott megoldáshoz tartozó, olvassa el az összegzési adatokat, majd kattintson **létrehozás**.

   ![Azure Portal](media/automation-solution-vm-management/azure-portal-01.png)

3. A **megoldás hozzáadása** lap jelenik meg. Az Automation-előfizetésbe történő importálása előtt a megoldás konfigurálására kéri.

   ![Virtuális gép felügyelet – megoldás hozzáadása lap](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

4. Az a **megoldás hozzáadása** lapon jelölje be **munkaterület**. Válassza ki a Log Analytics-munkaterület, amely kapcsolódik az Azure-előfizetéshez, amely az Automation-fiókot. Ha nem rendelkezik egy munkaterületet, válassza ki a **új munkaterület létrehozása**. Az a **Log Analytics-munkaterület** lapon, a következő lépésekkel:
   - Adjon meg egy nevet az új **Log Analytics-munkaterület**, például a "ContosoLAWorkspace".
   - Válassza ki a **előfizetés** összekapcsolása a legördülő listában válassza ki, ha az alapértelmezett kiválasztás nem megfelelő.
   - A **erőforráscsoport**, hozzon létre egy új erőforráscsoportot, vagy válasszon ki egy meglévőt.
   - Válasszon ki egy **helyet**. Csak a következő helyek elérhető jelenleg **Délkelet-Ausztrália**, **közép-Kanada**, **közép-India**, **USA keleti Régiójában**, **Kelet-japán**, **Délkelet-Ázsia**, **Egyesült Királyság déli régiója**, **Nyugat-Európa**, és **USA 2. nyugati**.
   - Válasszon egy tarifacsomagot a **Tarifacsomag** területen. Válassza ki a **Gigabájtonkénti (különálló)** lehetőséget. Az Azure Monitor naplóira frissített [díjszabás](https://azure.microsoft.com/pricing/details/log-analytics/) , és a GB szinten az egyetlen lehetőség.

   > [!NOTE]
   > A megoldások engedélyezésekor csak bizonyos régiók esetén lehet összekapcsolni egy Log Analytics-munkaterületet és egy Automation-fiókot.
   >
   > A támogatott leképezés párok listáját lásd: [régió hozzárendelése Automation-fiók és a Log Analytics-munkaterület](how-to/region-mappings.md).

5. Miután megadta a szükséges adatokat a **Log Analytics-munkaterület** kattintson **létrehozás**. Nyomon követheti a folyamat állapotát **értesítések** a menüben, amely adja vissza, hogy a **megoldás hozzáadása** lapon, ha ezzel elkészült.
6. Az a **megoldás hozzáadása** lapon jelölje be **Automation-fiók**. Egy új Log Analytics-munkaterületet hoz létre, hozzon létre egy új Automation-fiókot társítja, vagy válasszon egy meglévő Automation-fiókot, amely nem már kapcsolódik egy Log Analytics-munkaterületet. Válassza ki a meglévő Automation-fiókot, vagy kattintson a **Automation-fiók létrehozása**, majd a a **Automation-fiók hozzáadása** lap, adja meg a következő információkat:
   - A **Név** mezőbe írja be az Automation-fiók nevét.

     Minden egyéb lehetőségeket vannak alapján automatikusan kitölti a kiválasztott Log Analytics-munkaterület. Ezek a beállítások nem módosíthatók. A megoldásban szereplő runbookok alapértelmezett hitelesítési módszere egy Azure-futtatófiók. Miután rákattintott **OK**, a rendszer érvényesíti a konfigurációs beállításokat, és az Automation-fiók létrehozása. Az **Értesítések** menüpont alatt nyomon követheti a folyamat előrehaladását.

7. Végül a a **megoldás hozzáadása** lapon jelölje be **konfigurációs**. A **paraméterek** lap jelenik meg.

   ![Paraméterek lap megoldás](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   Itt kéri:
   - Adja meg a **cél erőforráscsoport nevét**. Ezek az értékek az erőforráscsoportok nevei, amelyek a megoldás által felügyelendő virtuális gépeket tartalmazzák. Adjon meg egynél több nevet, és külön az egyes egy vesszőt (értékek nem számítanak különbözőnek) használatával. Helyettesítő karaktert is használhat, ha az előfizetés összes erőforráscsoportjában lévő virtuális gépeket szeretné megadni. Ez az érték a tárolódik a **External_Start_ResourceGroupNames** és **External_Stop_ResourceGroupNames** változókat.
   - Adja meg a **VM kizárási lista (karakterlánc)** . Ez az érték egy vagy több virtuális gépet a célként megadott erőforráscsoportja nevét. Adjon meg egynél több nevet, és külön az egyes egy vesszőt (értékek nem számítanak különbözőnek) használatával. Helyettesítő karakterek használatával támogatott. Ez az érték a tárolódik a **External_ExcludeVMNames** változó.
   - Válassza ki a **ütemezés**. Válassza ki a dátum és idő az ütemezés számára. Ismétlődő napi ütemezés jön létre a kiválasztott idő kezdve. Egy másik régió kiválasztásával nem érhető el. Az ütemezést, hogy az adott időzóna beállítása után a megoldás konfigurálásáról: [az indítási és leállítási ütemezés módosítása](#modify-the-startup-and-shutdown-schedules).
   - Fogadásához **E-mail-értesítések** a műveletcsoport, fogadja el alapértékként a **Igen** , és adjon meg egy érvényes e-mail címet. Ha **nem** egy későbbi időpontban döntse el, hogy szeretne email értesítéseket kapni, frissítheti, de a [műveletcsoport](../azure-monitor/platform/action-groups.md) létrehozott az érvényes e-mail címeket vesszővel elválasztva. Akkor is engedélyeznie kell a következő riasztási szabályok:

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > Az alapértelmezett érték a **cél erőforráscsoport nevét** van egy **&ast;** . Ez egy adott előfizetés összes virtuális gép célozza meg. Ha nem szeretné, hogy a megoldás az előfizetésében lévő összes virtuális gép cél ezt az értéket kell frissíteni, hogy az ütemezések engedélyezése előtt az erőforráscsoportok nevei listáját.

8. A megoldás kezdeti beállításainak konfigurálását követően kattintson **OK** gombra kattintva zárja be a **paraméterek** lapon, és válassza **létrehozás**. Miután a rendszer érvényesíti az összes beállítást, a megoldást már telepítették az előfizetéshez. A folyamat eltarthat néhány másodpercig befejeződik, és nyomon követheti a folyamat állapotát **értesítések** a menüből.

> [!NOTE]
> Ha a központi telepítés befejezése után, az Automation-fiókját az Azure Cloud Solution Provider (az Azure CSP) előfizetéssel rendelkezik, lépjen a **változók** alatt **megosztott erőforrások** és állítsa be a [ **External_EnableClassicVMs** ](#variables) változó **hamis**. Ezzel leállítja a megoldást keres a klasszikus virtuális gép erőforrásait.

## <a name="scenarios"></a>Forgatókönyvek

A megoldás három különböző forgatókönyveket tartalmaz. Ezek a forgatókönyvek a következők:

### <a name="scenario-1-startstop-vms-on-a-schedule"></a>forgatókönyv 1: Ütemezés szerint virtuális gépek indítása/leállítása

Ez a forgatókönyv az alapértelmezett konfiguráció esetén a megoldás első üzembe. Például beállíthatja, hogy minden virtuális gép leállítása előfizetésen, ha munkahelyi esténként hagyja, és indítsa el őket a reggel, amikor áll vissza a office. Az ütemezések konfigurálásakor **ütemezett-StartVM** és **ütemezett-StopVM** központi telepítése során indítsa el, és állítsa le a célként kijelölt virtuális gépek. Ez a megoldás csak a virtuális gépek leállításához konfigurálása támogatott, lásd: [indítási és leállítási ütemezés módosítása](#modify-the-startup-and-shutdown-schedules) megtudhatja, hogyan állítson be egy egyéni ütemezést.

> [!NOTE]
> Az adott időzóna kell az aktuális időzóna, ha az ütemezés időpont paraméter megadásának. Azonban ez tárolt UTC formátumban, az Azure Automationben. Nem rendelkezik bármely időzóna átalakítása tennie, mivel ez végzi az üzembe helyezés során.

Befolyásolhatja, amelyek a virtuális gépek hatókör úgy konfigurálja az alábbi változókat: **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**, és **External_ExcludeVMNames**.

A műveletet egy előfizetésben és erőforráscsoportban célzó, vagy egy adott lista virtuális gépeket, de nem mindkettőt célzó engedélyezheti.

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Az Indítás és leállítás műveletek egy előfizetésben és erőforráscsoportban csoport cél

1. Konfigurálja a **External_Stop_ResourceGroupNames** és **External_ExcludeVMNames** változókat, adja meg a cél virtuális gépekről.
2. Engedélyezze, és frissítse a **ütemezett-StartVM** és **ütemezett-StopVM** ütemezéseket.
3. Futtassa a **ScheduledStartStop_Parent** beállítása művelet paraméterrel runbook **start** és a WHATIF paraméter beállítása **igaz** a preview changes.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Az indítási és leállítási művelet a célzott Virtuálisgép-lista

1. Futtassa a **ScheduledStartStop_Parent** beállítása művelet paraméterrel runbook **start**, vesszővel tagolt listája, a virtuális gépek a *VMList* paramétert, majd állítsa be a WHATIF paraméter **igaz**. A módosítások előnézetét.
1. Konfigurálja a **External_ExcludeVMNames** paramétert a virtuális gépek (VM1, VM2, VM3) vesszővel elválasztott listáját.
1. Ebben a forgatókönyvben nem fogadja el a **External_Start_ResourceGroupNames** és **External_Stop_ResourceGroupnames** változókat. Ebben az esetben szüksége saját Automation ütemezés létrehozásához. További információkért lásd: [runbook ütemezése az Azure Automation](../automation/automation-schedules.md).

> [!NOTE]
> Az érték **cél erőforráscsoport nevét** mindkét értéket tárolt **External_Start_ResourceGroupNames** és **External_Stop_ResourceGroupNames**. A további részletességgel ezeket a változókat, amelyekre eltérő erőforráscsoportokban mindegyike módosíthatja. A kezdő művelet használja **External_Start_ResourceGroupNames**, és a leállítási művelet, használja **External_Stop_ResourceGroupNames**. Virtuális gépek automatikusan hozzáadódnak a kezdő, és állítsa le az ütemezéseket.

### <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a>2\. forgatókönyv: A címkék használatával feladatütemezési virtuális gépek indítása és leállítása

A több, elosztott számítási feladatok támogatása virtuális gépen kettő vagy több összetevőt tartalmazó környezetekben támogató összetevők indíthatók és állíthatók le, amelyben a feladatütemezési sorrendben fontos. Ebben a forgatókönyvben a következő lépések végrehajtásával végezheti el:

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Az Indítás és leállítás műveletek egy előfizetésben és erőforráscsoportban csoport cél

1. Adjon hozzá egy **sequencestart** és a egy **sequencestop** megcélzott virtuális gépre egy pozitív egész számot tartalmazó címke **External_Start_ResourceGroupNames** és  **External_Stop_ResourceGroupNames** változókat. Az Indítás és leállítás műveletek növekvő sorrendben történik. Virtuális gép címkézése kapcsolatban lásd: [Windows virtuális gép címkézése Azure-ban](../virtual-machines/windows/tag.md) és [Linux rendszerű virtuális gép címkézése Azure-ban](../virtual-machines/linux/tag.md).
1. A ütemezésének módosítása **Sequenced-StartVM** és **Sequenced-StopVM** az a dátum és idő, amelyek megfelelnek az elvárásainak, és engedélyezni az ütemezést.
1. Futtassa a **SequencedStartStop_Parent** beállítása művelet paraméterrel runbook **start** és a WHATIF paraméter beállítása **igaz** a preview changes.
1. Tekintse meg a műveletet, és végezze el a szükséges módosításokat az éles virtuális gépek elleni implementálása előtt. Amikor készen, manuálisan végrehajtja a forgatókönyvet a paraméter beállítása **hamis**, vagy lehetővé teszik az Automation ütemezési **Sequenced-StartVM** és **Sequenced-StopVM** futtatása a meghatározott ütemezés szerint automatikusan követően.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Az indítási és leállítási művelet a célzott Virtuálisgép-lista

1. Adjon hozzá egy **sequencestart** és a egy **sequencestop** virtuális gépekhez való hozzáadását tervezi egy pozitív egész számot tartalmazó címke a **VMList** paraméter.
1. Futtassa a **SequencedStartStop_Parent** beállítása művelet paraméterrel runbook **start**, vesszővel tagolt listája, a virtuális gépek a *VMList* paramétert, majd állítsa be a WHATIF paraméter **igaz**. A módosítások előnézetét.
1. Konfigurálja a **External_ExcludeVMNames** paramétert a virtuális gépek (VM1, VM2, VM3) vesszővel elválasztott listáját.
1. Ebben a forgatókönyvben nem fogadja el a **External_Start_ResourceGroupNames** és **External_Stop_ResourceGroupnames** változókat. Ebben az esetben szüksége saját Automation ütemezés létrehozásához. További információkért lásd: [runbook ütemezése az Azure Automation](../automation/automation-schedules.md).
1. Tekintse meg a műveletet, és végezze el a szükséges módosításokat az éles virtuális gépek elleni implementálása előtt. Készen, manuálisan végrehajtása közben a monitoring-és-diagnosztikai/monitoring-művelet-groupsrunbook beállítása paraméterrel **hamis**, vagy lehetővé teszik az Automation ütemezési **Sequenced-StartVM** és **Sequenced-StopVM** futtassa a következő automatikusan a a meghatározott ütemezés szerint.

### <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a>3\. forgatókönyv: CPU-kihasználtság alapján automatikusan indítása és leállítása

Ez a megoldás segítségével az előfizetésében futó virtuális gépek értékelése az Azure virtuális gépek nem használt nem csúcsidőre időszakokban például óra elteltével, és automatikusan leáll őket, ha a processzorhasználat értéke: záró x % költségének kezeléséhez.

Alapértelmezés szerint a megoldás az előre konfigurált kiértékelni a százalékos CPU metrika átlagos kihasználtság e 5 %-os vagy annál kisebb. Ebben a forgatókönyvben az alábbi változókat vezérli, és módosíthatók, ha az alapértelmezett értékek nem felelnek meg a követelményeknek:

- External_AutoStop_MetricName
- External_AutoStop_Threshold
- External_AutoStop_TimeAggregationOperator
- External_AutoStop_TimeWindow

A műveletet egy előfizetésben és erőforráscsoportban célzó, vagy egy adott lista virtuális gépeket, de nem mindkettőt célzó engedélyezheti.

#### <a name="target-the-stop-action-against-a-subscription-and-resource-group"></a>A leállítási műveletet egy előfizetésben és erőforráscsoportban csoporton cél

1. Konfigurálja a **External_Stop_ResourceGroupNames** és **External_ExcludeVMNames** változókat, adja meg a cél virtuális gépekről.
1. Engedélyezze, és frissítse a **Schedule_AutoStop_CreateAlert_Parent** ütemezés.
1. Futtassa a **AutoStop_CreateAlert_Parent** beállítása művelet paraméterrel runbook **start** és a WHATIF paraméter beállítása **igaz** a preview changes.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Az indítási és leállítási művelet a célzott Virtuálisgép-lista

1. Futtassa a **AutoStop_CreateAlert_Parent** beállítása művelet paraméterrel runbook **start**, vesszővel tagolt listája, a virtuális gépek a *VMList* paramétert, majd állítsa be a WHATIF paraméter **igaz**. A módosítások előnézetét.
1. Konfigurálja a **External_ExcludeVMNames** paramétert a virtuális gépek (VM1, VM2, VM3) vesszővel elválasztott listáját.
1. Ebben a forgatókönyvben nem fogadja el a **External_Start_ResourceGroupNames** és **External_Stop_ResourceGroupnames** változókat. Ebben az esetben szüksége saját Automation ütemezés létrehozásához. További információkért lásd: [runbook ütemezése az Azure Automation](../automation/automation-schedules.md).

Most, hogy ütemezés alapján a CPU-kihasználtság virtuális gépek leállítása, meg kell engedélyezhető a következő ütemezéseket, indítsa el őket.

- Cél indítsa el a művelet által előfizetésben és erőforráscsoportban. Olvassa el a [1. forgatókönyv](#scenario-1-startstop-vms-on-a-schedule) tesztelésére, és így **ütemezett-StartVM** ütemezéseket.
- Cél előfizetés, erőforráscsoport és a címke művelet elindításához. Olvassa el a [2. forgatókönyv](#scenario-2-startstop-vms-in-sequence-by-using-tags) tesztelésére, és így **Sequenced-StartVM** ütemezéseket.

## <a name="solution-components"></a>Megoldás-összetevők

Ez a megoldás előre konfigurált runbookok ütemezését és integráció az Azure Monitor naplóira tartalmaz, így az indításakor és leállásakor a virtuális gépeket az üzleti igényeinek megfelelően testre szabhatja.

### <a name="runbooks"></a>Runbookok

Az alábbi táblázat a megoldás által telepített az Automation-fiók runbookok. Ne módosítsa a runbook-kód. Ehelyett írja a saját új funkciók a runbook.

> [!IMPORTANT]
> Ne közvetlenül futtassa bármely olyan runbookhoz és "gyermek" jelöléssel nevére.

Minden szülő runbook közé tartozik a _WhatIf_ paraméter. Ha beállítása **igaz**, _WhatIf_ pontos viselkedésének részletező támogatja a runbook futtatása nélkül; a _WhatIf_ paraméter és érvényesíti a megfelelő folyamatban van a virtuális gépek megcélzott. A runbook csak a meghatározott műveleteket hajtja végre során a _WhatIf_ paraméter értéke **hamis**.

|Forgatókönyv | Paraméterek | Leírás|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | A szülő runbook meghívva. Ez a runbook a Remote-forgatókönyvhöz erőforrás alapon hoz létre riasztásokat.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: TRUE vagy FALSE (hamis)  | Létrehozza vagy frissíti az Azure a riasztási szabályai virtuális gépeken vagy erőforráscsoportonként célzott csoportok. <br> VMList: Virtuális gépek vesszővel tagolt listája. Ha például _vm1, vm2, vm3_.<br> *WhatIf* érvényesíti a runbook logikája végrehajtása nélkül.|
|AutoStop_Disable | Egyik sem | Letiltja a Remote-riasztások és az alapértelmezett ütemezés szerint.|
|AutoStop_StopVM_Child | WebHookData | A szülő runbook meghívva. Riasztási szabályok hívja meg a runbookot, hogy a virtuális gép leállítása.|
|Bootstrap_Main | Egyik sem | Egyszer például webhookURI, rendszer-indításkori-konfigurációk, amelyek általában nem érhetők el az Azure Resource Manager beállításához használt. Ez a forgatókönyv a sikeres telepítés automatikusan törlődnek.|
|ScheduledStartStop_Child | VMName <br> Művelet: Indítása vagy leállítása <br> ResourceGroupName | A szülő runbook meghívva. Egy ütemezett leállítását elindítása vagy leállítása műveletet hajt végre.|
|ScheduledStartStop_Parent | Művelet: Indítása vagy leállítása <br>VMList <br> WhatIf: TRUE vagy FALSE (hamis) | Ez a beállítás hatással van az előfizetésben található összes virtuális gépet. Szerkessze a **External_Start_ResourceGroupNames** és **External_Stop_ResourceGroupNames** csak végre ezeket a megcélzott csoportok. Szintén kizárhatja az adott virtuális gépek frissítésével a **External_ExcludeVMNames** változó.<br> VMList: Virtuális gépek vesszővel tagolt listája. Ha például _vm1, vm2, vm3_.<br> _WhatIf_ érvényesíti a runbook logikája végrehajtása nélkül.|
|SequencedStartStop_Parent | Művelet: Indítása vagy leállítása <br> WhatIf: TRUE vagy FALSE (hamis)<br>VMList| Nevű címkék létrehozása **sequencestart** és **sequencestop** az egyes virtuális Gépeken, amelyekhez feladatütemezési indítása/leállítása tevékenységhez. Ezek a címkenevek és nagybetűk. A címke értékének pozitív egész szám (1, 2, 3), amely annak a sorrendnek, amelyben meg szeretné elindítani vagy leállítani kell lennie. <br> VMList: Virtuális gépek vesszővel tagolt listája. Ha például _vm1, vm2, vm3_. <br> _WhatIf_ érvényesíti a runbook logikája végrehajtása nélkül. <br> **Megjegyzés**: Virtuális gépek az Azure Automation változó External_Start_ResourceGroupNames External_Stop_ResourceGroupNames és External_ExcludeVMNames részlete erőforráscsoportokon belül kell lennie. A megfelelő címkék műveletek érvénybe kell rendelkezniük.|

### <a name="variables"></a>Változók

A következő táblázat felsorolja az Automation-fiókban létrehozott változókat. Csak a következő előtaggal kezdődik változók módosításához **külső**. Változók módosítása előtaggal **belső** nemkívánatos hatásokkal okoz.

|Változó | Leírás|
|---------|------------|
|External_AutoStop_Condition | Riasztás aktiválása előtt a feltétel konfigurálásához szükséges feltételes operátort. Elfogadható értékek a következők **GreaterThan**, **GreaterThanOrEqual**, **LessThan**, és **LessThanOrEqual**.|
|External_AutoStop_Description | A riasztás a virtuális gép leállítása, ha a Processzorhasználat százalékos aránya meghaladja a küszöbértéket.|
|External_AutoStop_MetricName | A teljesítmény-mérőszám, amelyhez az Azure-riasztási szabály konfigurálását van neve.|
|External_AutoStop_Threshold | Az Azure-riasztási szabály, a változóban megadott küszöbértékét _External_AutoStop_MetricName_. Százalékos értékek között lehet 1 és 100.|
|External_AutoStop_TimeAggregationOperator | Az idő összesítési operátor, alkalmazott a kijelölt méretének Pro vyhodnocení podmínky. Elfogadható értékek a következők **átlagos**, **minimális**, **maximális**, **teljes**, és **utolsó**.|
|External_AutoStop_TimeWindow | Az ablak mérete, amely során Azure elemzi a riasztást kiváltó mód kiválasztott adatai. Ez a paraméter bemeneti timespan formátumban fogad el. Lehetséges értékek: 5 percet vagy akár 6 óráig.|
|External_EnableClassicVMs| Itt adhatja meg, hogy klasszikus virtuális gépeket a megoldás által megcélzott. Az alapértelmezett érték: igaz. Ez kell beállítani hamis értékre a CSP-előfizetésekben.|
|External_ExcludeVMNames | Adja meg, amelyet ki szeretne, egy virtuális gép neve, nevek elválasztó vessző használatával szóközök nélküli szövegláncként. Ez a korlátozott 140 virtuális gépekhez. Ha több mint 140 virtuális gépeket ad hozzá a vesszővel tagolt listája, virtuális gépek vannak beállítva, amelyet ki szeretne előfordulhat, hogy véletlenül elindítandó vagy leállítandó.|
|External_Start_ResourceGroupNames | Itt adható meg egy vagy több erőforráscsoport megadhat, az értékek egy vesszővel tagolt indítási műveleteket a megcélzott használatával.|
|External_Stop_ResourceGroupNames | Itt adható meg egy vagy több erőforráscsoport megadhat, az értékek egy vesszővel tagolt stop műveleteket a megcélzott használatával.|
|Internal_AutomationAccountName | Itt adhatja meg az Automation-fiók nevére.|
|Internal_AutoSnooze_WebhookUri | Megadja a Remote-forgatókönyvhöz nevű Webhook URI.|
|Internal_AzureSubscriptionId | Adja meg az Azure-előfizetés azonosítóját.|
|Internal_ResourceGroupName | Megadja az automatizálási fiók erőforráscsoportjának neve.|

Az összes környezetekben a **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**, és **External_ExcludeVMNames** változók szükség hajthatja végre a virtuális gépek, virtuális gépekről vesszővel tagolt listáját tartalmazó kivételével a **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent**, és  **ScheduledStartStop_Parent** runbookok. Azt jelenti a virtuális gépek kell a kezdő cél erőforráscsoportokban található, és állítsa le a műveletek. A logikai működését, hogy az előfizetés vagy az erőforrás-csoport célba, és hogy bizonyos műveletek az Azure policy hasonló öröklik az újonnan létrehozott virtuális gépek. Ezzel a módszerrel elkerülhető, hogy nem kell külön ütemezés minden virtuális gép kezelheti és kezdődik, és leállítja a méretezési csoportban.

### <a name="schedules"></a>Ütemezések

A következő táblázat felsorolja az egyes az Automation-fiókban létrehozott ütemezése. Módosítsa azokat, vagy hozzon létre saját egyéni ütemezéseket. Alapértelmezés szerint minden ütemezés le vannak tiltva az alábbiakat kivéve **Scheduled_StartVM** és **Scheduled_StopVM**.

Az ütemezést, mert előfordulhat, hogy hozzon létre egymást átfedő ütemezés műveletek, ez ne engedélyezze. Célszerű meghatározni, melyik optimalizálást szeretné végrehajtani, és ennek megfelelően módosítsa. Tekintse meg a további magyarázat az Áttekintés szakaszban szereplő példakörnyezetek.

|Ütemezés neve | Gyakoriság | Leírás|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | 8 óránként | Futtatja a AutoStop_CreateAlert_Parent runbookot 8 óránként, ami viszont a Virtuálisgép-alapú értékei External_Start_ResourceGroupNames External_Stop_ResourceGroupNames és External_ExcludeVMNames az Azure Automation változó leállítja. Megadhatja azt is megteheti, a virtuális gépek vesszővel elválasztott listáját a VMList paraméter használatával.|
|Scheduled_StopVM | Felhasználó által megadott, naponta | A Scheduled_Parent runbookot futtat egy s parametrem _leállítása_ minden nap, a megadott időpontban. Automatikusan leállítja a virtuális gépeket, amelyek megfelelnek a szabályokat, eszközintelligencia változók határozzák meg. A kapcsolódó ütemezés engedélyezése **ütemezett-StartVM**.|
|Scheduled_StartVM | Felhasználó által megadott, naponta | A Scheduled_Parent runbookot futtat egy s parametrem _Start_ minden nap, a megadott időpontban. Minden virtuális gépre, amelyek megfelelnek a szabályok határozzák meg a megfelelő változók automatikusan elindul. A kapcsolódó ütemezés engedélyezése **ütemezett-StopVM**.|
|Sequenced-StopVM | 1:00-kor (UTC), minden pénteken | A Sequenced_Parent runbookot futtat egy s parametrem _leállítása_ minden pénteken, a megadott időpontban. Egymás után (növekvő) leállítja a címkével ellátott összes virtuális gép **SequenceStop** határozzák meg a megfelelő változókat. A címkeértékeket és az eszközintelligencia változók további információkért tekintse meg a Runbookok szakaszt. A kapcsolódó ütemezés engedélyezése **Sequenced-StartVM**.|
|Sequenced-StartVM | 1:00 Órakor (UTC), minden hétfőn | A Sequenced_Parent runbookot futtat egy s parametrem _Start_ minden hétfőn, a megadott időpontban. Egymás után minden virtuális gép (csökkenő) kezdődik, egy címke **SequenceStart** határozzák meg a megfelelő változókat. A címkeértékeket és az eszközintelligencia változók további információkért tekintse meg a Runbookok szakaszt. A kapcsolódó ütemezés engedélyezése **Sequenced-StopVM**.|

## <a name="azure-monitor-logs-records"></a>Az Azure Monitor rekordok naplózása

Automation két rekordtípust hoz létre a Log Analytics-munkaterület: feladat-naplók és feladat-adatfolyamokat.

### <a name="job-logs"></a>Feladatnaplók

|Tulajdonság | Leírás|
|----------|----------|
|Caller |  A művelet kezdeményezője. Lehetséges értékek: egy e-mail-cím vagy egy ütemezett feladatokat tartalmazó rendszer.|
|Category | Az adattípus besorolása. Az Automation esetében az érték JobLogs.|
|CorrelationId | GUID, a runbook-feladat korrelációs azonosítója.|
|JobId | GUID, a runbook-feladat azonosítója.|
|operationName | Meghatározza az Azure-ban végrehajtott művelet típusát. Az Automation esetében az érték feladat.|
|resourceId | Meghatározza az Azure-ban szereplő erőforrás típusát. Az Automation esetében az érték a runbookhoz társított Automation-fiók.|
|ResourceGroup | Meghatározza a runbook-feladat erőforráscsoportjának nevét.|
|ResourceProvider | Meghatározza, hogy melyik Azure-szolgáltatás biztosítja az üzembe helyezhető és kezelhető erőforrásokat. Az Automation esetében az érték Azure Automation.|
|ResourceType | Meghatározza az Azure-ban szereplő erőforrás típusát. Az Automation esetében az érték a runbookhoz társított Automation-fiók.|
|resultType | A runbook-feladat állapota. Lehetséges értékek:<br>- Elindítva<br>- Leállítva<br>- Felfüggesztve<br>- Sikertelen<br>- Sikeres|
|resultDescription | Ismerteti a runbook-feladat eredményállapotát. Lehetséges értékek:<br>- A feladat elindult<br>- A feladat nem sikerült<br>- A feladat befejeződött|
|RunbookName | Megadja a runbook-feladat nevét.|
|SourceSystem | Megadja az elküldött adatok forrásrendszerét. Az Automation esetében az érték OpsManager|
|StreamType | Megadja az esemény típusát. Lehetséges értékek:<br>- Részletes<br>- Kimenet<br>- Hiba<br>- Figyelmeztetés|
|SubscriptionId | Megadja a feladat előfizetési azonosítóját.
|Time | A runbook-feladat végrehajtásának dátuma és időpontja.|

### <a name="job-streams"></a>Feladatstreamek

|Tulajdonság | Leírás|
|----------|----------|
|Caller |  A művelet kezdeményezője. Lehetséges értékek: egy e-mail-cím vagy egy ütemezett feladatokat tartalmazó rendszer.|
|Category | Az adattípus besorolása. Az Automation esetében az érték JobStreams.|
|JobId | GUID, a runbook-feladat azonosítója.|
|operationName | Meghatározza az Azure-ban végrehajtott művelet típusát. Az Automation esetében az érték feladat.|
|ResourceGroup | Meghatározza a runbook-feladat erőforráscsoportjának nevét.|
|resourceId | Adja meg az erőforrás-azonosító az Azure-ban. Az Automation esetében az érték a runbookhoz társított Automation-fiók.|
|ResourceProvider | Meghatározza, hogy melyik Azure-szolgáltatás biztosítja az üzembe helyezhető és kezelhető erőforrásokat. Az Automation esetében az érték Azure Automation.|
|ResourceType | Meghatározza az Azure-ban szereplő erőforrás típusát. Az Automation esetében az érték a runbookhoz társított Automation-fiók.|
|resultType | A runbook-feladat eredménye az esemény létrehozásának időpontjában. Egy lehetséges érték:<br>- Folyamatban|
|resultDescription | A runbook kimeneti streamjét tartalmazza.|
|RunbookName | A runbook neve.|
|SourceSystem | Megadja az elküldött adatok forrásrendszerét. Az Automation esetében az érték OpsManager.|
|StreamType | A feladatstream típusa. Lehetséges értékek:<br>– Folyamatban<br>- Kimenet<br>- Figyelmeztetés<br>- Hiba<br>- Hibakeresés<br>- Részletes|
|Time | A runbook-feladat végrehajtásának dátuma és időpontja.|

Kategória rekordjait visszaadó bármely Naplókeresés végrehajtásakor **JobLogs** vagy **JobStreams**, kiválaszthatja a **JobLogs** vagy **JobStreams**nézetet, amely megjeleníti a keresés által visszaadott frissítéseket összefoglaló csempék készletét.

## <a name="sample-log-searches"></a>Naplókeresési minták

A következő táblázat a megoldás által összegyűjtött feladatrekordokkal kapcsolatos naplókeresési mintákat tartalmazza.

|Lekérdezés | Leírás|
|----------|----------|
|Runbook ScheduledStartStop_Parent, amelyek sikeresen befejeződött feladatainak megkeresése | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Runbook SequencedStartStop_Parent, amelyek sikeresen befejeződött feladatainak megkeresése | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="viewing-the-solution"></a>A megoldás megtekintése

Hozzáférhet a megoldást, keresse meg az Automation-fiók kiválasztása **munkaterület** alatt **kapcsolódó erőforrások**. A log analytics oldalon válassza ki a **megoldások** alatt **általános**. Az a **megoldások** lapon, válassza ki a megoldás **Start – virtuális gépek leállítása [munkaterület]** a listából.

A megoldás kiválasztásakor megjelenik a **Start – virtuális gépek leállítása [munkaterület]** megoldás lapja. Itt áttekintheti a fontos részletek például a **StartStopVM** csempére. A Log Analytics-munkaterülethez hasonlóan ez a csempe számát és grafikus ábrázolását a runbook-feladatok a megoldáshoz, amelyek elindultak és sikeresen befejezte jeleníti meg.

![Automation Update Management megoldás lapja](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

Itt meg is további elemzéseket végezhet a feladatrekordokon a fánkdiagram csempére kattintva. A megoldás irányítópultja megjeleníti a feladatelőzményeket, és előre meghatározott naplóbeli keresési lekérdezések. Váltás a log analytics speciális portál keresése a keresési lekérdezések alapján.

## <a name="configure-email-notifications"></a>E-mail értesítések konfigurálása

A megoldás üzembe helyezése után e-mail-értesítések megváltoztatásához módosítsa a telepítés során létrehozott műveletcsoport.  

> [!NOTE]
> Az Azure Government cloud előfizetések nem támogatják az e-mail funkció, a megoldás.

Az Azure Portalon keresse meg a figyelő Műveletcsoportok ->. Válassza ki a műveletcsoport nevű **StartStop_VM_Notication**.

![Automation Update Management megoldás lapja](media/automation-solution-vm-management/azure-monitor.png)

Az a **StartStop_VM_Notification** kattintson **részleteinek szerkesztése** alatt **részletek**. Ekkor megnyílik a **e-mailek és SMS és leküldéses/Hangvétel** lapot. Az e-mail-cím frissítése, és kattintson a **OK** a módosítások mentéséhez.

![Automation Update Management megoldás lapja](media/automation-solution-vm-management/change-email.png)

Másik lehetőségként adhat hozzá további műveletek a műveletcsoport, további információ a műveletcsoportokról, lásd: [Műveletcsoportok](../azure-monitor/platform/action-groups.md)

Az alábbiakban látható egy például szolgáló e-mail érkezik, amikor a megoldás leállítja a virtuális gépeket.

![Automation Update Management megoldás lapja](media/automation-solution-vm-management/email.png)

## <a name="add-exclude-vms"></a>Virtuális gépek hozzáadása vagy kizárása

A megoldás lehetővé teszi, hogy a megoldás által célzott, vagy kifejezetten zárja ki a gépeket a megoldást a virtuális gépek biztosítja.

### <a name="add-a-vm"></a>Virtuális gép hozzáadása

Néhány módon segítségével győződjön meg arról, hogy egy virtuális gép szerepel a indítása és leállítása megoldás futtatásakor.

* A szülő mindegyike [runbookok](#runbooks) rendelkezik a megoldás egy **VMList** paraméter. Virtuális gép nevének vesszővel elválasztott listáját adhat át ehhez a paraméterhez, ha az adott helyzethez és ezek a virtuális gépek a megfelelő szülő runbook ütemezése tartalmazni fogja a megoldás futtatása.

* Válassza ki a több virtuális gép, állítsa be a **External_Start_ResourceGroupNames** és **External_Stop_ResourceGroupNames** a erőforrás-csoport nevekkel, amelyek tartalmazzák a virtuális gépek indítása vagy leállítása szeretné. És ez az érték is megadható `*`, hogy a megoldás futtatásához az előfizetés összes erőforráscsoportjában.

### <a name="exclude-a-vm"></a>Virtuális gép kizárása

Virtuális gép kizárása a megoldást, adhat hozzá, hogy a **External_ExcludeVMNames** változó. Ezt a változót az adott virtuális gépek kizárása a indítása és leállítása a megoldás a vesszővel tagolt listája. Ez a lista 140 virtuális gépek korlátozódik. Ha több mint 140 virtuális gépeket ad hozzá a vesszővel tagolt listája, virtuális gépek vannak beállítva, amelyet ki szeretne előfordulhat, hogy véletlenül elindítandó vagy leállítandó.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Az indítási és leállítási ütemezés módosítása

Ebben a megoldásban az indítási és leállítási ütemezés felügyelete lépéseket követi, ahogyan [runbook ütemezése az Azure Automation](automation-schedules.md). Szükség van egy külön elindítani és leállítani a virtuális gépek ütemezés.

A megoldás csak leállítja a virtuális gépek egy adott időpontra való konfigurálásáról használata támogatott. Ebben a forgatókönyvben csak létrehozni egy **leállítása** ütemezés és a nem megfelelő **Start** ütemezett. Ehhez a következőket kell tennie:

1. Az erőforráscsoportok, hogy állítsa le a virtuális gépek hozzá a **External_Stop_ResourceGroupNames** változó.
2. A saját az idő, állítsa le a virtuális gépeket szeretne ütemezést létrehozni.
3. Keresse meg a **ScheduledStartStop_Parent** runbook kattintson **ütemezés**. Ez lehetővé teszi, hogy válassza ki az előző lépésben létrehozott ütemezést.
4. Válassza ki **paraméterek és futtatási beállítások** és állítsa be a "Stop" művelet paramétert.
5. Kattintson az **OK** gombra a módosítások mentéséhez.

## <a name="update-the-solution"></a>A megoldás frissítése

Ha ez a megoldás egy korábbi állapotba helyezte, először törölnie kell azt a fiókból egy frissített verzió telepítése előtt. Kövesse a lépéseket a [távolítsa el a megoldás](#remove-the-solution) és kövesse a fenti lépéseket a [a megoldás üzembe helyezése](#deploy-the-solution).

## <a name="remove-the-solution"></a>A megoldás eltávolítása

Ha úgy dönt, hogy a megoldás használatához már nincs szüksége, törölheti az Automation-fiók. Csak a megoldás törlése eltávolítja a runbookokat. Az ütemezések vagy a megoldás hozzáadásakor létrehozott változókat nem törli. Azokat az eszközöket, ha nem használja őket a más forgatókönyvek manuálisan törölni kell.

Törölje a megoldást, hajtsa végre az alábbi lépéseket:

1. Az Automation-fiók alatt **kapcsolódó erőforrások**válassza **csatolt munkaterület**.
1. Válassza ki **munkaterületen**.
1. A **általános**válassza **megoldások**. 
1. Az a **megoldások** lapon, válassza ki a megoldás **Start – virtuális gépek leállítása [munkaterület]** . Az a **VMManagementSolution [munkaterület]** a menüből válassza a lap **törlése**.<br><br> ![Virtuálisgép-felügyeleti megoldás törlése](media/automation-solution-vm-management/vm-management-solution-delete.png)
1. Az a **megoldás törlése** ablakában győződjön meg arról, hogy szeretné-e törölni a megoldást.
1. Bár az adatokat a rendszer ellenőrzi, és a megoldás törlése, nyomon követheti a folyamat állapotát **értesítések** a menüből. A rendszer visszairányítja az **megoldások** lapon távolítsa el a megoldás a folyamat elindulása után.

Az Automation-fiók és a Log Analytics-munkaterület nem törlődnek a folyamat részeként. Ha nem szeretné megőrizni a Log Analytics-munkaterületet, törölje manuálisan szeretné. Ez az Azure Portalon is elvégezhető:

1. Válassza ki az Azure portál főoldalára, **Log Analytics-munkaterületek**.
1. Az a **Log Analytics-munkaterületek** lapon, válassza ki a munkaterületet.
1. Válassza ki **törlése** a menüben a munkaterület beállítások lapon.

Ha nem szeretné megőrizni az Azure Automation-fiók összetevőket, manuálisan törölheti egyes. Runbookok, a változók és a megoldás által létrehozott ütemezések listáját lásd: a [megoldás-összetevőket](#solution-components).

## <a name="next-steps"></a>További lépések

- Más keresési lekérdezéseket hozhat létre, és tekintse át az Automation feladatnaplóit Azure Monitor-naplókkal kapcsolatos további tudnivalókért lásd: [Naplókeresésekkel a naplókban az Azure Monitor](../log-analytics/log-analytics-log-searches.md).
- A runbook végrehajtásával, a runbook-feladatok figyelésével, illetve az egyéb technikai részletekkel kapcsolatos további tudnivalókat a [Runbook-feladatok nyomon követése](automation-runbook-execution.md) című rész tartalmazza.
- Az Azure Monitor naplóira és adatgyűjtési forrásokkal kapcsolatos további tudnivalókért lásd: [gyűjtése Azure-tárfiókbeli adatok az Azure monitorban naplók áttekintése](../azure-monitor/platform/collect-azure-metrics-logs.md).

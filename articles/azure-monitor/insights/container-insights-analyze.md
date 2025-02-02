---
title: AKS-fürt teljesítmény figyelése és az Azure Monitor-tárolókhoz |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan megtekintheti és a teljesítmény- és naplófájl-adatok elemzése az Azure Monitor for containers szolgáltatásban.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/22/2019
ms.author: magoedte
ms.openlocfilehash: bbfc8cc61571de8b76ef1f7f0216501ef6d2cdee
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377470"
---
# <a name="understand-aks-cluster-performance-with-azure-monitor-for-containers"></a>Az AKS fürtteljesítmény és az Azure Monitor-tárolókhoz ismertetése 
Az Azure monitorral tárolók segítségével a teljesítmény diagramokat és az állapot az Azure Kubernetes Service (AKS)-fürtök két perspektíva adatai, közvetlenül az AKS-fürt vagy az Azure-ból összes AKS-fürt az előfizetéshez, a számítási feladat figyeléséhez Ez a figyelő. Megtekintése az Azure Container Instances (ACI) esetén is lehetséges egy adott AKS-fürt monitorozására.

A cikknek a segítségével megismerheti, hogyan segít gyorsan felmérheti, kivizsgálásán és elhárításán észlelt problémák, és a két perspektíva adatai között a felhasználói élményt.

A tárolók az Azure Monitor engedélyezésével kapcsolatos információkért lásd: [előkészítése az Azure Monitor-tárolókhoz](container-insights-onboard.md).

A Azure Monitor egy több fürtből álló nézetet biztosít, amely az előfizetésekben lévő erőforráscsoportok között telepített összes, Linux és Windows Server 2019 rendszerű, figyelt AK-fürt állapotát mutatja.  AKS-fürtök észlelt, amely nem a megoldás által figyelt jeleníti meg. Azonnal képes megérteni a fürt állapotát, és innen lefúrhat a csomópont és a tartományvezérlő teljesítmény lapján, vagy keresse meg a fürt teljesítménydiagramok megtekintéséhez.  AKS fürtök felderített és a nem figyelt azonosította engedélyezheti a tetszőleges időpontban, hogy a fürt figyelése.  

A Windows Server-fürtök egy Linux-fürthöz képest Azure Monitorekkel való figyelésének fő eltérései a következők:

- A memóriabeli RSS-metrika nem érhető el a Windows-csomópontok és-tárolók esetében.
- A lemezes tárolás kapacitására vonatkozó információk nem érhetők el Windows-csomópontok esetén.
- Az élő naplók támogatása a Windows-tároló naplófájljainak kivételével érhető el.
- Csak a pod környezetek figyelhetők meg, nem pedig a Docker-környezetek.
- Az előzetes kiadásban legfeljebb 30 Windows Server-tároló támogatott. Ez a korlátozás nem vonatkozik a Linux-tárolók esetében.  

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

## <a name="multi-cluster-view-from-azure-monitor"></a>Az Azure Monitor több fürt megtekintése

Üzembe helyezett összes AKS-fürt állapotának megtekintéséhez válassza **figyelő** a bal oldali ablaktáblán, az Azure Portalon.  Alatt a **Insights** szakaszban jelölje be **tárolók**.  

![Az Azure Monitor több fürt irányítópultja – példa](./media/container-insights-analyze/azmon-containers-multiview.png)

Az a **fürtök figyelt** lapon is tudja ismerje meg a következőket:

1. Hány fürtök kritikus vagy nem megfelelő állapotú, és hány megfelelő vagy nem jelentéskészítési (néven állapota ismeretlen) vannak?
2. Összes saját [Azure Kubernetes-(AKS-motor) motor](https://github.com/Azure/aks-engine) kifogástalan üzemelő példányok?
3. Hány csomópontot, felhasználói és rendszerhüvelyt helyeznek üzembe fürtön?
4. Mekkora lemezterület áll rendelkezésre, és van kapacitási probléma?

Tartalmazza a rendszerállapot-állapotok az alábbiak: 

* **Kifogástalan állapotú** – nem problémát észlelt a virtuális gép, és szükség szerint működik-e. 
* **Kritikus fontosságú** – egy vagy több kritikus problémák észlelhetők, amely kell oldani annak érdekében, hogy a várt módon állítsa vissza a rendes működési állapot.
* **Figyelmeztetés** – egy vagy több problémák észlelhetők, amelyeket meg kell oldani, vagy az egészségügyi feltétel kritikus válhat.
* **Ismeretlen** – Ha a szolgáltatás nem tudta létesítsen kapcsolatot a csomópont- vagy pod, az állapot módosul, állapota ismeretlen.
* **Nem található** - vagy a munkaterületen, az erőforráscsoportra vagy törölték a megoldás, amely tartalmazza a munkaterület előfizetés.
* **Jogosulatlan** -felhasználó nem rendelkezik az adatok a munkaterület olvasásához szükséges engedélyekkel.
* **Hiba** -hiba történt a munkaterület adatainak olvasására tett kísérlet során.
* Helytelenül **konfigurált-Azure monitor** a tárolók nem megfelelően lettek konfigurálva a megadott munkaterületen.
* **Nincs adat** -adatok nem jelentette a munkaterülethez az elmúlt 30 percben.

Az állapot kiszámítja a fürt teljes állapotát, mivel a három állapot közül a *legrosszabb* az egyik kivétel – ha a három állam bármelyike *ismeretlen*, a teljes fürt állapota **ismeretlen**lesz.  

A következő táblázat nyújt információkat a számítás, a figyelt fürt a fürt több nézet állapotokat szabályozása.

| |status |Rendelkezésre állás |  
|-------|-------|-----------------|  
|**Felhasználói Pod**| | |  
| |Kifogástalan |100% |  
| |Figyelmeztetés |90 - 99 %-os |  
| |Kritikus |< 90 %-a |  
| |Ismeretlen |Ha az elmúlt 30 percben nem jelentettek |  
|**Rendszer-Pod**| | |  
| |Kifogástalan |100% |
| |Figyelmeztetés |– |
| |Kritikus |< 100 %-os |
| |Ismeretlen |Ha az elmúlt 30 percben nem jelentettek |
|**Node** | | |
| |Kifogástalan |> 85 % felett |
| |Figyelmeztetés |60 - 84 % |
| |Kritikus |< 60 % |
| |Ismeretlen |Ha az elmúlt 30 percben nem jelentettek |

Fürtök listájában, akkor nyissa a **fürt** lapra kattintva a fürt nevét, a **csomópontok** teljesítmény lapra kattintva a kumulatív frissítést a csomópontok a **csomópontok** oszlopot adott fürtben, vagy a feltárásához az **tartományvezérlők** teljesítmény lapon kattintson az összegző a **felhasználói podok** vagy **rendszer podok**oszlop.   

## <a name="view-performance-directly-from-an-aks-cluster"></a>AKS-fürt közvetlenül a teljesítmény megtekintése

Az Azure Monitor-tárolókhoz is elérhető közvetlenül egy AKS-fürtöt a kiválasztásával **Insights** a bal oldali panelen. Az AKS-fürt adatainak megtekintése a következő szakaszokba négy szempont:

- Fürt
- Csomópontok 
- Vezérlők  
- Containers

Az alapértelmezett oldalt megnyitni kattintva **Insights** van **fürt**, és a négy sorban teljesítménydiagramok megjelenítése a fürt fő teljesítménymutatói tartalmazza. 

![Példa teljesítménydiagramok fürt lapján](./media/container-insights-analyze/containers-cluster-perfview.png)

A teljesítmény diagramon négy teljesítmény-mérőszámait jeleníti meg:

- **&nbsp;Csomópont CPU-%kihasználtsága**: A teljes fürt CPU-kihasználtságának összesített perspektívája. Kijelölésével szűrheti az eredményeket a időtartományban **átlagos**, **Min**, **maximális**, **50**, **90**, és **95.** percentilisei-választójában jelenítse a diagram felett vagy külön-külön vagy együtt. 
- **&nbsp;Csomópont memóriájának%kihasználtsága**: A teljes fürt memória-kihasználtságának összesített perspektívája. Kijelölésével szűrheti az eredményeket a időtartományban **átlagos**, **Min**, **maximális**, **50**, **90**, és **95.** percentilisei-választójában jelenítse a diagram felett vagy külön-külön vagy együtt. 
- **Csomópontok száma**: Csomópontok száma és állapota a Kubernetes. A fürtcsomópontok jelölt állapotok a *összes*, *készen*, és *nem áll készen* és szűrt külön-külön vagy együtt a választó a diagram felett a. 
- **Activity Pod-szám**: A Kubernetes származó Pod-szám és-állapot. A podok jelöli az állapotok a *összes*, *függőben lévő*, *futó*, és *ismeretlen* és szűrt külön-külön vagy együtt a a a diagram felett választó. 

A bal/jobb nyílbillentyűk használatával a diagramon az egyes adatpontokon keresztül válthat, a fel/le nyílbillentyűk pedig a percentilis vonalakon válthatnak. Az egyik diagram jobb felső sarkában található rögzítés ikonra kattintva rögzítheti a kiválasztott diagramot az utolsó megtekintett Azure-irányítópulton. Az irányítópultról átméretezheti és áthelyezheti a diagramot. Ha kiválasztja a diagramot az irányítópultról, a rendszer átirányítja Azure Monitor a tárolók számára, és betölti a megfelelő hatókört és nézetet.

A tárolók Azure Monitor támogatja a Azure Monitor [metrikák Explorert](../platform/metrics-getting-started.md)is, ahol saját diagramokat hozhat létre, összekapcsolhatja és megvizsgálhatja a trendeket, és rögzítheti az irányítópultokat. A mérőszámok Intézőből a metrikák egy [metrika-alapú riasztási szabály](../platform/alerts-metric.md)alapján történő megjelenítéséhez beállított feltételeket is használhatja.  

## <a name="view-container-metrics-in-metrics-explorer"></a>Tároló metrikáinak megtekintése a metrikák Explorerben

A metrikák Explorerben megtekintheti az összesített csomópont-és Pod-kihasználtsági metrikákat Azure Monitor a tárolók számára. A következő táblázat összefoglalja a részleteket, amelyekkel megismerheti, Hogyan jeleníthető meg a metrikai diagramok a tároló metrikáinak megjelenítéséhez.

|Névtér | Metrika |
|----------|--------|
| insights.container/nodes | |
| | cpuUsageMillicores |
| | cpuUsagePercentage |
| | memoryRssBytes |
| | memoryRssPercentage |
| | memoryWorkingSetBytes |
| | memoryWorkingSetPercentage |
| | nodesCount |
| bepillantást nyerhet. tároló/hüvely | |
| | PodCount |

Alkalmazhat egy mérőszám [felosztását](../platform/metrics-charts.md#apply-splitting-to-a-chart) úgy, hogy megtekintse azt dimenzió alapján, és megjelenítse, hogy a különböző szegmensek hogyan hasonlítanak egymáshoz. Csomópont esetében a diagramot a *gazdagép* dimenziója alapján, a pod-ból pedig a következő dimenziók szerint szegmentálhatja:

* Vezérlő
* Kubernetes-névtér
* Csomópont
* Fázis

## <a name="analyze-nodes-controllers-and-container-health"></a>Csomópontok, vezérlők és tároló állapotának elemzése

Ha úgy vált, hogy **csomópontok**, **tartományvezérlők**, és **tárolók** lap jobb oldalán található az automatikusan megjelennek a tulajdonság panelen. Megjeleníti a kiválasztott elem tulajdonságait, beleértve a Kubernetes objektumok rendszerezéséhez definiált címkéket is. Linux-csomópont kiválasztásakor azt is megjeleníti a **helyi lemez kapacitása** rendelkezésre álló lemezterület és a csomóponton bemutatott egyes lemezek esetében használt százalék. Kattintson a **>>** összekapcsolása a panelen view\hide a panelen. 

![Példa Kubernetes perspektívák tulajdonságait tartalmazó ablaktáblán](./media/container-insights-analyze/perspectives-preview-pane-01.png)

Kibontja a hierarchia az objektumok, ahogy a Tulajdonságok panelen frissítések alapján a kiválasztott objektum. A panelen is megtekintheti az előre definiált naplókeresések Kubernetes-események kattintva a **nézet Kubernetes eseménynaplók** a panel tetején lévő hivatkozásra. Kubernetes-naplóadatok megtekintésével kapcsolatos további információkért lásd: [keresni a naplókban az adatelemzéshez](container-insights-log-search.md). A fürt erőforrásainak áttekintése közben valós időben láthatja a tároló naplóit és eseményeit. További információ erről a szolgáltatásról, valamint a hozzáférés biztosításához és vezérléséhez szükséges konfigurációról: a [naplók valós idejű megtekintése a Azure monitor for containers használatával](container-insights-live-logs.md). 

A lap felső részén található **+ szűrő hozzáadása** lehetőséggel szűrheti a nézet által megjelenített eredményeket a **szolgáltatás**, a **csomópont**, a **névtér**vagy a **csomópont-készlet** alapján, majd a szűrő hatókörének kijelölése után kiválaszthatja a következő értékek egyikét: **Válassza az érték (ek)** mezőt.  A szűrés konfigurálása után érvényes globálisan az AKS-fürt bármely szempontjából megtekintése közben.  A képlet csak az egyenlőségjel támogatja.  A találatok további szűkítéséhez az eredményeket a legelső felül további szűrőket adhat hozzá.  Ha például egy szűrő által megadott **csomópont**, a második szűrőt csak lehetővé tenné, hogy válassza ki **szolgáltatás** vagy **Namespace**.  

![Példa eredmények szűkítéséhez a szűrő használatával](./media/container-insights-analyze/add-filter-option-01.png)

Megadhat egy szűrőt egy lapon továbbra is alkalmazható, amikor kiválaszt egy másik, és törlődik, miután rákattintott a **x** mellett található a megadott szűrő.   

Váltson a **csomópontok** lapra, és a sor hierarchia követi a Kubernetes hálózatiobjektum-modellje, kezdve a fürt egyik csomópontjához. Bontsa ki a csomópontot, és a csomóponton futó egy vagy több podok is megtekintheti. Ha egynél több tároló van csoportosítva podot, jelennek meg az utolsó sorban a hierarchiában. Hány nem pod kapcsolódó számítási feladatok futnak a gazdagépen, ha a gazdagép processzort vagy a rendelkezésre álló memória mennyisége is megtekintheti.

![Kubernetes-csomópontot példahierarchia a teljesítmény nézet](./media/container-insights-analyze/containers-nodes-view.png)

A Windows Server 2019 operációs rendszert futtató Windows Server-tárolók a listában szereplő összes Linux-alapú csomópont után jelennek meg. Egy Windows Server-csomópont kibontásakor megtekintheti a csomóponton futó egy vagy több hüvelyt és tárolót. Csomópont kiválasztásakor a Tulajdonságok ablaktábla a verziószámot jeleníti meg, kivéve az ügynökök adatait, mivel a Windows Server-csomópontok nem rendelkeznek ügynökkel.  

![Példa a csomópont-hierarchiára a felsorolt Windows Server-csomópontokkal](./media/container-insights-analyze/nodes-view-windows.png) 

Az Azure Container Instances virtuális csomópontok a Linux operációs rendszert futtató a listában lévő utolsó AKS fürtcsomópont után jelennek meg.  Amikor kibővít egy ACI virtuális csomópont, egy vagy több ACI podok és tárolók a csomóponton futó megtekintheti.  Metrikák nem összegyűjtött és jelentett csomópontok, csak a podok.

![A Container Instances felsorolt példahierarchia csomópont](./media/container-insights-analyze/nodes-view-aci.png)

Egy kibontott csomópontból részletezhet a pod vagy teljesítményadatok szűrve, hogy a tartományvezérlő a tartományvezérlőre, amely a csomóponton futó tárolót. Kattintson az érték a a **vezérlő** oszlopban az adott csomópont számára.   
![Példa Lehatolás csomópontból a teljesítmény nézet-vezérlő](./media/container-insights-analyze/drill-down-node-controller.png)

Válassza ki a tartományvezérlők vagy a lap tetején lévő tárolókat, és tekintse át az állapot- és erőforrás-felhasználást azokat az objektumokat.  Ha ehelyett meg szeretné tekinteni a memóriahasználat, az a **metrika** legördülő listában válassza **memória RSS** vagy **memória-munkakészlet**. **Memória RSS** csak Kubernetes 1.8-as és újabb verziók esetében támogatott. Ellenkező esetben, tekintse meg az értékeket **Min&nbsp; %**  , *NaN&nbsp;%* , azaz egy nem definiált képviselő numerikus típus értéke vagy ábrázolható érték.

A memória-munkakészletben a rezidens memória és a virtuális memória (gyorsítótár) is látható, és az alkalmazás teljes egészében a használatos. A memória RSS csak a fő memóriát jeleníti meg, amely a rezidens memória. Ez a mérőszám a rendelkezésre álló memória tényleges kapacitását mutatja.

![Tároló csomópontok teljesítmény nézet](./media/container-insights-analyze/containers-node-metric-dropdown.png)

Alapértelmezés szerint a teljesítményadatokat az elmúlt hat órán alapul, de az ablak használatával módosíthatja a **TimeRange** lehetőséget a bal felső sarokban. Kiválasztásával is szűrheti az eredményeket időtartományban **átlagos**, **Min**, **maximális**, **50**, **90**, és **95** PERCENTILIS-választójában jelenítse. 

![Az adatok szűrésének. percentilis kiválasztása](./media/container-insights-analyze/containers-metric-percentile-filter.png)

Amikor egérmutatót alatt az oszlopdiagram a **Trend** oszlop, minden egyes sávon látható, vagy a CPU, vagy a memória kihasználtsága, attól függően, amelyek metrika van kijelölve, 15 percen belül minta. Miután kiválasztotta a trend diagramot a billentyűzeten, használhatja az ALT + PageUp vagy az ALT + PageDown billentyűkombinációt az egyes sávokon való váltáshoz, és ugyanazokat a részleteket kapja meg, mint a mouseover-en.

![Példa a trend oszlopdiagram fölé](./media/container-insights-analyze/containers-metric-trend-bar-01.png)    

A következő példában, vegye figyelembe a lista – az első node *aks-nodepool1 -* , értéke **tárolók** érték 9, amely egy összegző üzembe helyezett tárolókat teljes száma.

![A tárolók száma példája összegzése](./media/container-insights-analyze/containers-nodes-containerstotal.png)

Azt is segítenek gyorsan azonosítani, hogy van-e a tárolók a fürtben lévő csomópontok közötti megfelelő egyensúly. 

A csomópontok megtekintésekor megjelenő információkat az alábbi táblázatban olvasható:

| Oszlop | Leírás | 
|--------|-------------|
| Name (Név) | A gazdagép neve. |
| status | A csomópont állapota Kubernetes nézete. |
| Átlagos&nbsp;%, Min&nbsp;% Max&nbsp;%, az 50&nbsp;%, 90&nbsp;% | A csomópont átlagos százalékos PERCENTILIS alapján a kijelölt időszakra. |
| Avg, Min, Max, 50, 90 | Csomópontok átlagos tényleges értéket során kiválasztott töltött idő százalékos érték alapján. Az átlagos érték mérése történik egy csomópont; beállítása CPU/memória felső korlátja podok és tárolók az értéke az avg a gazdagép által jelentett. |
| Containers | A tárolók száma. |
| Hasznos üzemidő | Mivel a csomópont elindult, és újra lett indítva a idejét jelzi. |
| Vezérlők | Csak a tárolók és a podokat. Azt mutatja, hogy melyik tartományvezérlő van a hozzá tartozó. Nem minden podok egy vezérlőt, vannak, ezért néhány megjelenítheti **N/A**. | 
| Átlagos trend&nbsp;%, Min&nbsp;% Max&nbsp;%, az 50&nbsp;%, 90&nbsp;% | Oszlopdiagram trend átlagos PERCENTILIS mérőszám százalékos aránya a vezérlő jelöli. |

Válassza ki a választó **tartományvezérlők**.

![Kijelölés vezérlők megjelenítése](./media/container-insights-analyze/containers-controllers-tab.png)

Itt megtekintheti a tartományvezérlők és ACI virtuális csomópont tartományvezérlőkön vagy egy tartományvezérlő nem csatlakozik virtuális csomópont podok teljesítménybeli állapotát.

![\<Név > vezérlők teljesítmény nézete](./media/container-insights-analyze/containers-controllers-view.png)

A sor hierarchia vezérlő kezdődik, és amikor kibővít egy tartományvezérlő, megtekintheti az egy vagy több podok.  Bontsa ki a pod, és az utolsó sort jeleníti meg a tároló a pod szerint vannak csoportosítva. Egy kibontott vezérlőből származó részletes elemzését is a csomópontot, szűri az adott csomópont teljesítményadatok futtató. Egy tartományvezérlő nem csatlakozik az ACI podok utolsó szerepelnek a listában.

![A Container Instances podok felsorolt példahierarchia vezérlők](./media/container-insights-analyze/controllers-view-aci.png)

Kattintson az érték a a **csomópont** oszlopban az adott vezérlő.   

![Példa részletezés csomópontból a teljesítmény nézet-vezérlő](./media/container-insights-analyze/drill-down-controller-node.png)

A tartományvezérlők megtekintésekor megjelenő információkat az alábbi táblázatban olvasható:

| Oszlop | Leírás | 
|--------|-------------|
| Name (Név) | A vezérlő neve.|
| status | A tárolókat, ha befejeződött, például a futó állapotú, összesítő állapotát *OK*, *kilépett*, *sikertelen* *leállítva*, vagy *Szüneteltetve*. Ha a tároló fut-e, de a állapota volt, vagy nem megfelelően jelenik meg, vagy volt nem dolgozza fel az ügynök és a 30 percnél hosszabb ideig nem válaszolt, az állapot értéke *ismeretlen*. További részletek a állapot ikon az alábbi táblázatban szerepelnek.|
| Átlagos&nbsp;%, Min&nbsp;% Max&nbsp;%, az 50&nbsp;%, 90&nbsp;% | Az egyes entitások átlagos százalékos arányának összesítése a kiválasztott metrika és a percentilis esetében. |
| Avg, Min, Max, 50, 90  | A kiválasztott százalékos értékhez tartozó tároló átlagos CPU-millicore vagy memória-teljesítményének összesítése. Az átlagos érték podot beállított CPU/memória felső korlátja mérése történik. |
| Containers | A vezérlő vagy a pod tárolók száma összesen. |
| Újraindul | A tárolók újraindítási számának összesítése. |
| Hasznos üzemidő | Egy tároló indítása óta idejét jelzi. |
| Csomópont | Csak a tárolók és a podokat. Melyik, a hozzá tartozó tartományvezérlő jeleníti meg. | 
| Átlagos trend&nbsp;%, Min&nbsp;% Max&nbsp;%, az 50&nbsp;%, 90&nbsp;%| Oszlopdiagram trend a átlagos PERCENTILIS mérőszám, a vezérlő jelöli. |

Az ikonok az állapot mezőben a tárolók online állapotát jelzi:
 
| Ikon | status | 
|--------|-------------|
| ![Készen áll, futó állapot ikon](./media/container-insights-analyze/containers-ready-icon.png) | Fut (kész)|
| ![Várakozás vagy szüneteltetett állapot ikon](./media/container-insights-analyze/containers-waiting-icon.png) | Várakozás vagy fel van függesztve|
| ![Utolsó jelentett futó állapotikon](./media/container-insights-analyze/containers-grey-icon.png) | Utolsó jelentett fut, de 30 percnél hosszabb ideig nem válaszolt.|
| ![A sikeres állapot ikon](./media/container-insights-analyze/containers-green-icon.png) | Sikeresen leállt vagy nem sikerült leállítani a|

Az állapotjelző ikon alapján a pod biztosít számát jeleníti meg. A legrosszabb kétállapotú jeleníti meg, és amikor a kurzort az állapot, a tárolóban megjeleníti az összes podok egy összesítő állapotát. Ha nincs kész állapotú, az állapot értékét jeleníti meg **(0)** . 

Válassza ki a választó **tárolók**.

![Válassza ki a tárolók megtekintése](./media/container-insights-analyze/containers-containers-tab.png)

Itt megtekintheti az Azure-beli Kubernetes és az Azure Container Instances a tárolók teljesítménybeli állapotát.  

![\<Név > vezérlők teljesítmény nézete](./media/container-insights-analyze/containers-containers-view.png)

Egy tárolóban, a részletes elemzését is egy pod vagy egy csomópontot, szűri az adott objektum teljesítményadatainak megjelenítéséhez. Kattintson az érték a a **Pod** vagy **csomópont** oszlopban az adott tároló.   

![Példa részletezés csomópontból a teljesítmény nézet-vezérlő](./media/container-insights-analyze/drill-down-controller-node.png)

A tárolók megtekintésekor megjelenő információkat az alábbi táblázatban olvasható:

| Oszlop | Leírás | 
|--------|-------------|
| Name (Név) | A vezérlő neve.|
| status | A tárolók, ha van ilyen állapotát. További részletek a állapot ikon a következő táblázatban találhatók.|
| Átlagos&nbsp;%, Min&nbsp;% Max&nbsp;%, az 50&nbsp;%, 90&nbsp;% | Az átlagos százalékos aránya a kiválasztott metrika és PERCENTILIS minden entitás összegzése. |
| Avg, Min, Max, 50, 90  | Az átlagos CPU millicore vagy a memória teljesítményét a kiválasztott PERCENTILIS tárolója összegzése. Az átlagos érték podot beállított CPU/memória felső korlátja mérése történik. |
| Pod | A pod tartalmazó tároló.| 
| Csomópont |  Csomópont, amelyben a tároló található. | 
| Újraindul | Egy tároló indítása óta idejét jelzi. |
| Hasznos üzemidő | Mivel egy tároló volt elindítva vagy újraindítva idejét jelzi. |
| Átlagos trend&nbsp;%, Min&nbsp;% Max&nbsp;%, az 50&nbsp;%, 90&nbsp;% | Oszlopdiagram trend átlagos PERCENTILIS mérőszám százalékos aránya a tárolót képviseli. |

Az állapot mezőben az ikonok jelzi a podok, online válik, az alábbi táblázatban leírtak szerint:
 
| Ikon | status |  
|--------|-------------|  
| ![Készen áll, futó állapot ikon](./media/container-insights-analyze/containers-ready-icon.png) | Fut (kész)|  
| ![Várakozás vagy szüneteltetett állapot ikon](./media/container-insights-analyze/containers-waiting-icon.png) | Várakozás vagy fel van függesztve|  
| ![Utolsó jelentett futó állapotikon](./media/container-insights-analyze/containers-grey-icon.png) | Utolsó jelentett fut, de a 30 percnél hosszabb ideig nem válaszolt|  
| ![Elbocsátott állapotikon](./media/container-insights-analyze/containers-terminated-icon.png) | Sikeresen leállt vagy nem sikerült leállítani a|  
| ![Sikertelen állapotikon](./media/container-insights-analyze/containers-failed-icon.png) | Hibás állapotban |  

## <a name="workbooks"></a>Munkafüzetek

A munkafüzetek szövegeket, [naplókat](../log-query/query-language.md), [metrikákat](../platform/data-platform-metrics.md)és paramétereket egyesítenek gazdag interaktív jelentésekben. A munkafüzetek szerkeszthető más csapattagok számára, akik ugyanahhoz az Azure-erőforrásokhoz férnek hozzá.

A tárolók Azure Monitor négy munkafüzetet tartalmaznak az első lépések elkezdéséhez:

- **Lemez kapacitása**: Interaktív lemezhasználat-diagramokat jelenít meg a tárolóban a csomóponton a következő perspektívák által bemutatott lemezekhez:

    - Lemez kihasználtsága (%) az összes lemez esetében
    - Szabad lemezterület az összes lemez számára
    - Az egyes csomópontok lemezei, a felhasznált terület százalékos aránya, a felhasznált terület százalékos aránya, a szabad lemezterület (GiB) és a szabad lemezterület (GiB) trendje. Ha a táblázatban egy sor van kiválasztva, akkor a (z)% felhasznált terület és a szabad lemezterület (GiB) alább látható. 

- **Lemez i/o**: Interaktív lemez-kihasználtsági diagramokat jelenít meg a tárolóban a csomóponton a következő perspektívák által bemutatott lemezekhez:

    - Lemez I/O összegzése az összes lemezről olvasási sebesség (bájt/s), bájt/mp írás, olvasási és írási sebesség (bájt/mp) trendek 
    - Nyolc teljesítményű diagram látható a fő teljesítménymutatókkal a lemezek I/O-szűk keresztmetszetének méréséhez és azonosításához.

- **Kubelet**: A két rácsot tartalmaz, amelyek a fő csomópont működési statisztikáit mutatják:

    - A Node Grid áttekintés a teljes művelet, a teljes hibák és a sikeres műveletek összesítésével összegzi az egyes csomópontok százalékát és trendjét.
    - Az áttekintés a műveleti típus szerint összefoglalja a teljes művelet, az összes hiba és a sikeres műveletek százalékos és trend szerinti műveleteit.

- **Hálózat**: Interaktív hálózati kihasználtsági diagramokat jelenít meg az egyes csomópontok hálózati adapterei számára, valamint a fő teljesítménymutatókat bemutató rácsot a hálózati adapterek teljesítményének mérése érdekében.  

Ezeket a munkafüzeteket úgy érheti el, ha kiválasztja a **munkafüzetek megtekintése** legördülő listát.  

![Munkafüzetek megtekintése legördülő lista](./media/container-insights-analyze/view-workbooks-dropdown-list.png)

## <a name="next-steps"></a>További lépések

- Tekintse át a [teljesítménnyel kapcsolatos riasztások létrehozása a Azure monitor for containers](container-insights-alerts.md) szolgáltatással című témakört, amelyből megtudhatja, hogyan hozhat létre riasztásokat magas CPU-és memóriahasználat esetén a DevOps vagy működési folyamatok és eljárások támogatása 

- Megtekintheti a [napló lekérdezési példáit](container-insights-log-search.md#search-logs-to-analyze-data) , amelyekkel előre definiált lekérdezéseket és példákat tekinthet meg a fürtök riasztásának, megjelenítésének vagy elemzésének kiértékeléséhez és testreszabásához.

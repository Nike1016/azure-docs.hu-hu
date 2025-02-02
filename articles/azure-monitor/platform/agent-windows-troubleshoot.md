---
title: A Windows Log Analytics ügynökkel kapcsolatos problémák elhárítása | Microsoft Docs
description: A Azure Monitor Log Analytics ügynökével kapcsolatos leggyakoribb problémák tüneteinek, okainak és megoldásának leírása.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/12/2019
ms.author: magoedte
ms.openlocfilehash: 9df389b6e6a73530c9bbf5a2187d6735946e309f
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249792"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-windows"></a>A Windows Log Analytics ügynökkel kapcsolatos problémák elhárítása 

Ez a cikk segítséget nyújt a Windows rendszerhez készült Log Analytics ügynökkel kapcsolatos hibák elhárításához Azure Monitor és a lehetséges megoldások megoldására.

Ha a fentiek egyikével sem működik, a következő támogatási csatornákat is elérhetők:

* Ügyfeleink a Premier szintű támogatási kedvezményekre megnyithat egy támogatási kérést az [Premier](https://premier.microsoft.com/).
* Az Azure támogatási szerződés rendelkező ügyfelek is nyisson egy támogatási kérést [az Azure Portalon](https://manage.windowsazure.com/?getsupport=true).
* A Log Analytics visszajelzési oldalán tekintse át a beküldött ötletekre és hibák [ https://aka.ms/opinsightsfeedback ](https://aka.ms/opinsightsfeedback) vagy egy új fájlt. 

## <a name="important-troubleshooting-sources"></a>Fontos hibaelhárítási források

 A Windows Log Analytics ügynökkel kapcsolatos problémák elhárítása érdekében az ügynök az eseményeket a Windows eseménynaplóba naplózza, különösen az *alkalmazás-és a Services\Operations*-kezelőben.  

## <a name="connectivity-issues"></a>Csatlakozási problémák

Ha az ügynök egy proxykiszolgálón vagy tűzfalon keresztül kommunikál, előfordulhat, hogy korlátozásokkal jár a forrásszámítógép és a Azure Monitor szolgáltatás közötti kommunikáció megakadályozása. Ha a kommunikáció le van tiltva, helytelen a konfiguráció, a munkaterületre való regisztráció meghiúsulhat az ügynök telepítése során, a telepítés után konfigurálhatja az ügynököt, hogy egy további munkaterületre jelentsen, vagy a sikeres regisztráció után az ügynök kommunikációja meghiúsul. Ez a szakasz a Windows-ügynökkel kapcsolatos ilyen típusú problémák megoldásának módszereit ismerteti. 

Ellenőrizze, hogy a tűzfal vagy a proxy úgy van-e konfigurálva, hogy engedélyezze a következő táblázatban ismertetett portok és URL-címek használatát. Ellenőrizze azt is, hogy a HTTP-ellenőrzés nincs-e engedélyezve a webes forgalom számára, mivel ez megakadályozhatja az ügynök és a Azure Monitor közötti biztonságos TLS-csatornát.  

|Ügynök erőforrása|Portok |Irány |HTTPS-ellenőrzés kihagyása|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |443-as port |Kimenő|Igen |  
|*.oms.opinsights.azure.com |443-as port |Kimenő|Igen |  
|*.blob.core.windows.net |443-as port |Kimenő|Igen |  
|*.azure-automation.net |443-as port |Kimenő|Igen |  

A Azure Governmentához szükséges tűzfal-információk: [Azure Government Management](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). 

Több módon is ellenőrizheti, hogy az ügynök sikeresen kommunikál-e Azure Monitorával.

- Engedélyezze az [Azure Log Analytics Agent Health értékelését](../insights/solution-agenthealth.md) a munkaterületen. A Agent Health irányítópulton tekintse meg a nem **válaszoló ügynökök száma** oszlopban, hogy gyorsan megjelenjen-e az ügynök listázása.  

- A következő lekérdezés futtatásával erősítse meg, hogy az ügynök szívverést küld a munkaterületnek, amelyet a jelentéshez konfigurált. Cserélje `<ComputerName>` le a helyére a gép tényleges nevét.

    ```
    Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer 
    ```

    Ha a számítógép sikeresen kommunikál a szolgáltatással, a lekérdezésnek eredményt kell visszaadnia. Ha a lekérdezés nem adott vissza eredményt, először ellenőrizze, hogy az ügynök úgy van-e konfigurálva, hogy a megfelelő munkaterületre jelentsen. Ha megfelelően van konfigurálva, folytassa a 3. lépéssel, és keresse meg a Windows eseménynaplóját annak azonosításához, hogy az ügynök naplózza-e, hogy milyen problémát okozhat a Azure Monitorával folytatott kommunikáció.

- A kapcsolódási problémák azonosítására szolgáló másik módszer a **TestCloudConnectivity** eszköz futtatása. Az eszköz alapértelmezés szerint telepítve van a *%systemroot%\Program Files\Microsoft monitoring Agent\Agent*mappában található ügynökkel. Egy rendszergazda jogú parancssorból navigáljon a mappához, és futtassa az eszközt. Az eszköz visszaadja az eredményeket, és kiemeli, hogy a teszt sikertelen volt-e (például ha egy adott porthoz/URL-címhez kapcsolódott). 

    ![TestCloudConnection-eszköz végrehajtási eredményei](./media/agent-windows-troubleshoot/output-testcloudconnection-tool-01.png)

- Az *Operations Manager* eseménynaplójának szűrése  - az eseményforrás*állapotfigyelő szolgáltatás modulok*, a *HealthService*és a *szolgáltatás-összekötő* és a szűrés **eseményvezérelt szintű** *Figyelmeztetés* és *hiba* alapján annak megerősítéséhez, hogy az a következő táblázatból származó írásos eseményekkel rendelkezik-e. Ha vannak, tekintse át az egyes lehetséges eseményekhez tartozó megoldási lépéseket.

    |Eseményazonosító |Source |Leírás |Megoldás: |
    |---------|-------|------------|-----------|
    |2133 & 2129 |Állapotfigyelő szolgáltatás |Nem sikerült kapcsolódni a szolgáltatáshoz az ügynöktől. |Ez a hiba akkor fordulhat elő, ha az ügynök nem tud közvetlenül kommunikálni, vagy tűzfalon vagy proxykiszolgálón keresztül a Azure Monitor szolgáltatáshoz. Ellenőrizze az ügynök proxyjának beállításait, illetve azt, hogy a hálózati tűzfal/proxy lehetővé teszi a TCP-forgalmat a számítógépről a szolgáltatásba.|
    |2138 |Állapotfigyelő szolgáltatás modulok |A proxy hitelesítést igényel |Konfigurálja az ügynök proxyjának beállításait, és adja meg a proxykiszolgáló hitelesítéséhez szükséges felhasználónevet és jelszót. |
    |2129 |Állapotfigyelő szolgáltatás modulok |Sikertelen kapcsolat/sikertelen SSL-egyeztetés |Keresse meg a hálózati adapter TCP/IP-beállításait és az ügynök proxyjának beállításait.|
    |2127 |Állapotfigyelő szolgáltatás modulok |Hiba történt az adatküldés során kapott hibakód miatt |Ha ez csak időnként történik a nap folyamán, akkor csak véletlenszerű anomália lehet, amely figyelmen kívül hagyható. Figyelje meg, hogy milyen gyakran történik. Ha gyakran fordulnak elő a nap folyamán, először ellenőrizze a hálózati konfigurációt és a proxybeállításokat. Ha a leírás tartalmazza a 404-es HTTP-hibakódot, és ez az első alkalom, amikor az ügynök megpróbál elküldeni egy adatküldés a szolgáltatásnak, a rendszer 500 hibát tartalmaz egy belső 404 hibakódtal. 404 nem található, ami azt jelzi, hogy az új munkaterület tárterülete még mindig kiépítve van. A következő próbálkozáskor a rendszer az elvárt módon fogja sikeresen írni a munkaterületet. Az 403-es HTTP-hiba az engedélyt vagy a hitelesítő adatokat is jelezheti. A probléma megoldásához a 403 hibával kapcsolatos további információ található.|
    |4000 |Szolgáltatás-összekötő |A DNS-névfeloldás nem sikerült |A gép nem tudja feloldani az adatok szolgáltatásba való küldésekor használt internetcímet. Ez lehet a DNS-feloldó beállítások a gépen, helytelen proxybeállítások vagy esetleg egy ideiglenes DNS-probléma a szolgáltatónál. Ha ez rendszeres időközönként történik, a hálózattal kapcsolatos átmeneti probléma okozhatja.|
    |4001 |Szolgáltatás-összekötő |Nem sikerült kapcsolódni a szolgáltatáshoz. |Ez a hiba akkor fordulhat elő, ha az ügynök nem tud közvetlenül kommunikálni, vagy tűzfalon vagy proxykiszolgálón keresztül a Azure Monitor szolgáltatáshoz. Ellenőrizze az ügynök proxyjának beállításait, illetve azt, hogy a hálózati tűzfal/proxy lehetővé teszi a TCP-forgalmat a számítógépről a szolgáltatásba.|
    |4002 |Szolgáltatás-összekötő |A szolgáltatás a 403-as HTTP-állapotkódot adta vissza egy lekérdezésre válaszul. A szolgáltatás állapotáról érdeklődjön a szolgáltatás rendszergazdájánál. A lekérdezés később újra próbálkozik. |Ez a hiba az ügynök kezdeti regisztrációs fázisában van írva, és a következőhöz hasonló URL-címet fog látni *:\<https://munkaterület azonosítója >. OMS. opinsights. Azure. com/AgentService. SVC/AgentTopologyRequest*. Az 403-es hibakód tiltott, és a nem megfelelő típusú munkaterület-azonosító vagy-kulcs okozhatja, vagy helytelenek az adattípusok és az idő a számítógépen. Ha az idő +/-aktuális időtől számítva 15 percig, majd előkészítés sikertelen lesz. Ennek kijavítania a Windows-számítógép dátumát és/vagy időzónáját.|

## <a name="data-collection-issues"></a>Adatgyűjtés – problémák

Miután telepítette az ügynököt, és jelentések lettek a konfigurált munkaterületre vagy munkaterületekre, előfordulhat, hogy nem fogadja a konfigurációt, a teljesítmény, a naplók vagy más adatok fogadását a szolgáltatáshoz, attól függően, hogy mi az engedélyezve és a számítógép célzása. Meg kell határozni, hogy:

- Egy adott adattípus vagy az összes olyan adat, amely nem érhető el a munkaterületen?
- A megoldás által megadott vagy a munkaterület adatgyűjtési konfigurációjának részeként megadott adattípus?
- Hány számítógép érintett? Egy vagy több számítógép jelenti a munkaterületet?
- Működött, és egy adott napszakban leállt, vagy még soha nem gyűjtöttük össze? 
- A naplóbeli keresési lekérdezés szintaktikai helyességét használja? 
- Az ügynök már megkapta a konfigurációját a Azure Monitor?

A hibaelhárítás első lépése annak megállapítása, hogy a számítógép szívverési eseményt küld-e.

```
Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer
```

Ha a lekérdezés az eredményeket adja vissza, akkor meg kell határoznia, hogy egy adott adattípus nem kerül begyűjtésre és továbbításra a szolgáltatásnak. Ezt az okozhatja, hogy az ügynök nem kap frissített konfigurációt a szolgáltatástól, vagy egy másik tünet, amely megakadályozza, hogy az ügynök rendesen működik. A további hibaelhárításhoz hajtsa végre az alábbi lépéseket.

1. Nyisson meg egy rendszergazda jogú parancssort a számítógépen, és írja `net stop healthservice && net start healthservice`be a parancsot az ügynök szolgáltatás újraindításához.
2. Nyissa meg az *Operations Manager* eseménynaplót, és keressen rá a *7023, 7024, 7025, 7028* és *1210* **eseményazonosító** az **eseményforrás** *HealthService*.  Ezek az események azt jelzik, hogy az ügynök sikeresen fogadta a konfigurációt Azure Monitor és aktívan figyeli a számítógépet. Az 1210-es azonosítójú esemény leírása az utolsó sorban is megadhatja az ügynök figyelési hatókörében található összes megoldást és elemzést.  

    ![1210-es azonosítójú esemény leírása](./media/agent-windows-troubleshoot/event-id-1210-healthservice-01.png)

3. Ha néhány perc elteltével nem látja a várt információkat a lekérdezés eredményeiben vagy a vizualizációban, attól függően, hogy egy megoldásból vagy betekintésből tekinti-e meg az adatforrást, az *Operations Manager* eseménynaplóban keressen az **eseményforrás** *HealthService* és *állapotfigyelő szolgáltatás* a modulokat és a szűrést az **eseményvezérelt** *Figyelmeztetés* és a *hiba* alapján, hogy megbizonyosodjon róla, hogy az alábbi táblázatból írt események szerepelnek-e.

    |Eseményazonosító |Source |Leírás |Megoldás: |
    |---------|-------|------------|
    |8000 |HealthService |Ez az esemény azt adja meg, hogy a teljesítménygel, eseménnyel vagy más összegyűjtött adattípussal kapcsolatos munkafolyamatok nem tudnak-e továbbítani a szolgáltatásnak a munkaterületre való betöltéshez. | A forrás HealthService származó 2136-es azonosítójú esemény ezzel az eseménnyel együtt van írva, és jelezheti, hogy az ügynök nem tud kommunikálni a szolgáltatással, valószínűleg a proxy-és hitelesítési beállítások, a hálózati leállás vagy a hálózati tűzfal/ a proxy nem engedélyezi a számítógépről a szolgáltatás felé irányuló TCP-forgalmat.| 
    |10102 és 10103 |Állapotfigyelő szolgáltatás modulok |A munkafolyamat nem tudja feloldani az adatforrást. |Ez akkor fordulhat elő, ha a megadott teljesítményszámláló vagy példány nem létezik a számítógépen, vagy helytelenül van definiálva a munkaterület adatbeállításaiban. Ha ez egy felhasználó által megadott [teljesítményszámláló](data-sources-performance-counters.md#configuring-performance-counters), ellenőrizze, hogy a megadott információ a megfelelő formátumú-e, és létezik-e a célszámítógépen. |
    |26002 |Állapotfigyelő szolgáltatás modulok |A munkafolyamat nem tudja feloldani az adatforrást. |Ez akkor fordulhat elő, ha a megadott Windows-Eseménynapló nem létezik a számítógépen. Ez a hiba nyugodtan figyelmen kívül hagyható, ha a számítógép nem fogja tudni regisztrálni ezt az eseménynaplót, ellenkező esetben ha ez egy felhasználó által megadott [Eseménynapló](data-sources-windows-events.md#configuring-windows-event-logs), ellenőrizze, hogy helyesek-e a megadott adatok. |


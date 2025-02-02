---
title: Azure Log Analytics-ügynök konfigurálása Linux rendszerű hibrid számítógéphez | Microsoft Docs
description: Megtudhatja, hogyan helyezheti üzembe az Azure-on kívüli gépeken futó Linuxhoz készült Log Analytics-ügynököket, és hogyan gyűjthet adatokat a Log Analytics használatával.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 06/14/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 535b96a2bb86f1996e6ff0aba19339b55a18ee50
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515871"
---
# <a name="configure-log-analytics-agent-for-linux-computers-in-a-hybrid-environment"></a>Log Analytics-ügynök konfigurálása Linux rendszerű számítógépekhez hibrid környezetekben
Az [Azure Log Analytics](../platform/agent-windows.md) közvetlenül tud adatokat gyűjteni az Ön adatközpontjában vagy egyéb felhőalapú környezetében található fizikai vagy virtuális Linux-gépekről egy adattárba, részletes elemzés és összehasonlítás céljából. Ez a rövid útmutató bemutatja, hogyan konfigurálhatja a Linux rendszerű számítógépekről történő adatgyűjtést néhány egyszerű lépésben.  Azure-beli linuxos virtuális gépek esetén lásd [az Azure-beli virtuális gépekről történő adatgyűjtést](quick-collect-azurevm.md) ismertető cikket.  

A továbblépés előtt tekintse át a Log Analytics ügynök [áttekintése](../platform/log-analytics-agent.md) című cikket, amelyből megismerheti a támogatott konfigurációkat, a [támogatott Linux operációs rendszereket](../platform/log-analytics-agent.md#supported-linux-operating-systems) és a [hálózati tűzfal konfigurációját](../platform/log-analytics-agent.md#network-firewall-requirements). 

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen. 

## <a name="create-a-workspace"></a>Munkaterület létrehozása
1. Az Azure Portalon válassza a **Minden szolgáltatás** elemet. Az erőforrások listájában adja meg a **log Analytics**. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.

    ![Azure Portal](media/quick-collect-linux-computer/azure-portal-01.png) 

2. Válassza a **Létrehozás**lehetőséget, majd válassza ki a kívánt beállításokat a következő elemekhez:

   * Adja meg az új **Log Analytics-munkaterület** nevét, például: *DefaultLAWorkspace*. Az OMS-munkaterületeket mostantól Log Analytics-munkaterületeknek nevezzük.   
   * A legördülő listából válassza ki azt az **előfizetést**, amelyikhez kapcsolódni szeretne, ha az alapértelmezett kiválasztás nem megfelelő.
   * Az **Erőforráscsoport** beállításnál válasszon ki egy meglévő erőforráscsoportot, amely egy vagy több Azure-beli virtuális gépet tartalmaz.  
   * Válassza ki a **Helyet** a virtuális gépek üzembehelyezési céljaként.  További információkért tekintse meg [a Log Analytics által támogatott régiókat](https://azure.microsoft.com/regions/services/).  
   * Ha 2018. április 2. után létrehozott új előfizetésben hoz létre munkaterületet, az automatikusan a *GB-alapú* díjcsomagot használja, és a tarifacsomag kiválasztásának lehetősége nem érhető el.  Ha az április 2. előtt létrehozott meglévő előfizetéshez hoz létre munkaterületet, vagy pedig egy meglévő EA-regisztrációhoz kötött előfizetéshez, válassza ki a kívánt tarifacsomagot.  További információt az elérhető csomagokról [a Log Analytics részletes díjszabásában](https://azure.microsoft.com/pricing/details/log-analytics/) találhat.

        ![Log Analytics-erőforrás létrehozása panel](media/quick-collect-linux-computer/create-loganalytics-workspace-02.png)<br>  

3. Miután megadta a szükséges információkat a **log Analytics munkaterület** ablaktáblán, kattintson az **OK gombra**.  

Az **Értesítések** menüpontot kiválasztva nyomon követheti, hogyan ellenőrzi a rendszer az adatokat, és hogyan hozza létre a munkaterületet. 

## <a name="obtain-workspace-id-and-key"></a>A munkaterület-azonosító és -kulcs lekérése
A Linuxhoz készült Log Analytics-ügynök telepítése előtt szüksége lesz a Log Analytics-munkaterület azonosítójára és kulcsára.  Ezekre az adatokra az ügynök burkoló parancsfájljának lesz szüksége az ügynök konfigurálásához és a Log Analyticsszel való sikeres kommunikációjának biztosításához.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. A Azure Portal válassza a bal felső sarokban található **összes szolgáltatás** elemet. Az erőforrások listájában adja meg a **log Analytics**. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.
2. A Log Analytics-munkaterületek listájában válassza ki a korábban létrehozott *DefaultLAWorkspace* elemet.
3. Válassza ki a **Speciális beállítások** elemet.

    ![A Log Analytics speciális beállításai](media/quick-collect-linux-computer/log-analytics-advanced-settings-01.png) 
 
4. Válassza ki a **Csatlakoztatott források**, majd a **Linuxos kiszolgálók** elemet.   
5. A **Munkaterület-azonosító** és az **Elsődleges kulcs** jobb oldalán lévő érték. Másolja ki és illessze be mindkettőt a kedvenc szerkesztőjébe.   

## <a name="install-the-agent-for-linux"></a>A Linuxhoz készült ügynök telepítése
Az alábbi lépésekkel konfigurálhatóak az ügynök beállításai a Log Analytics használatához az Azure és az Azure Government felhőben.  

>[!NOTE]
>A Linuxhoz készült Log Analytics-ügynököt nem lehet úgy konfigurálni, hogy egynél több Log Analytics-munkaterületre is jelentsen.  

Ha a Linux rendszerű számítógépnek proxykiszolgálón keresztül kell kommunikálnia a Log Analytics szolgáltatással, a proxykonfiguráció a parancssorban a `-p [protocol://][user:password@]proxyhost[:port]` záradékkal adható meg.  A *proxyhost* tulajdonság a proxykiszolgáló teljes tartománynevét vagy IP-címét fogadja el. 

Például:`https://user01:password@proxy01.contoso.com:30443`

1. A linuxos számítógép a Log Analyticshez való csatlakoztatásának konfigurálásához futtassa az alábbi parancsot, és adja meg a korábban kimásolt munkaterület-azonosítót és elsődleges kulcsot. A következő parancs letölti, majd az ellenőrzőösszeg érvényesítése után telepíti az ügynököt. 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    A következő parancs a `-p` proxyparaméter mellett példaszintaxist is tartalmaz.

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. A linuxos számítógép az Azure Government felhőben futó Log Analyticshez való csatlakoztatásának konfigurálásához futtassa az alábbi parancsot, és adja meg a korábban kimásolt munkaterület-azonosítót és elsődleges kulcsot. A következő parancs letölti, majd az ellenőrzőösszeg érvényesítése után telepíti az ügynököt. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    A következő parancs a `-p` proxyparaméter mellett példaszintaxist is tartalmaz.

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Indítsa újra az ügynököt a következő parancs futtatásával: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="collect-event-and-performance-data"></a>Esemény- és teljesítményadatok gyűjtése
A Log Analytics képes az események gyűjtésére a Linux rendszernaplójából és a megadott teljesítményszámlálókból a hosszabb távú elemzések és jelentések készítéséhez, valamint műveleteket hajt végre bizonyos feltételek észlelése esetén.  A következő lépésekkel konfigurálhatja az események gyűjtését a Linux rendszernaplójából, illetve egyes gyakran használt teljesítményszámlálókból is.  

1. Válassza a **Syslog** elemet.  
2. Eseménynaplókat a nevük begépelésével adhat hozzá. Adja meg a **syslog** -t, majd **+** válassza ki a plusz jelet.  
3. A táblában törölje a **Tájékoztatás**, az **Értesítés** és a **Hibakeresés** súlyossági szint jelölését. 
4. A konfiguráció mentéséhez kattintson a **Save (Mentés** ) gombra a lap tetején.
5. Válassza a **Linuxos teljesítményadatok** elemet a teljesítményszámláló-adatok gyűjtésének Linux rendszerű gépen történő engedélyezéséhez. 
6. Amikor először konfigurálja egy új Log Analytics-munkaterület Linux-teljesítményszámlálóit, akkor több gyakran használt számlálót is gyorsan létrehozhat. Ezek mindegyike mellett egy jelölőnégyzet jelenik meg. 

    ![Alapértelmezett Windows-teljesítményszámlálók kiválasztva](media/quick-collect-linux-computer/linux-perfcounters-default.png)
    
    Válassza **a kijelölt teljesítményszámlálók hozzáadása**lehetőséget. A rendszer hozzáadja a kiválasztott számlálókat, és mindegyikhez beállít egy tíz másodperces mintagyűjtési időszakot.

7. A konfiguráció mentéséhez kattintson a **Save (Mentés** ) gombra a lap tetején.

## <a name="view-data-collected"></a>Összegyűjtött adatok megtekintése
Most, hogy engedélyezte az adatgyűjtést, futtasson le egy egyszerű naplóbeli keresést, hogy megtekinthessen néhány, a célszámtógépekről származó adatot.  

1. A Azure Portal lépjen a Log Analytics elemre, és válassza ki a korábban létrehozott munkaterületet.
2. Válassza ki a **naplóbeli keresés** csempét, és a naplók keresése ablaktáblán a lekérdezés mezőben `Perf` írja be a kifejezést, majd nyomja le az ENTER billentyűt, vagy válassza a lekérdezés mezőtől jobbra található Keresés gombot.

    ![Példa Log Analytics-naplókeresési lekérdezésre](media/quick-collect-linux-computer/log-analytics-portal-queryexample.png)

    A következő képen látható lekérdezés 735 teljesítményrekordot adott vissza.

    ![Log Analytics naplóbeli keresés eredménye](media/quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, eltávolíthatja az ügynököt a linuxos számítógépről, és törölheti a Log Analytics-munkaterületet.  

Az ügynök eltávolításához futtassa az alábbi parancsot a Linux rendszerű számítógépen. A *--purge* argumentum teljesen eltávolítja az ügynököt és annak konfigurációját.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

A munkaterület törléséhez válassza ki a korábban létrehozott Log Analytics munkaterületet, és az erőforrás lapon válassza a **Törlés**lehetőséget.

![Log Analytics-erőforrás törlése](media/quick-collect-linux-computer/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>További lépések
Most, hogy már gyűjti a működési és teljesítményadatokat a linuxos számítógépéről, könnyen nekiláthat az *ingyenesen* gyűjtött adatok felfedezésének és elemzésének, és reagálhat a kapott eredményekre.  

Az adatok megtekintésének és elemzésének ismertetéséhez lépjen tovább az útmutatóhoz.   

> [!div class="nextstepaction"]
> [Adatok megtekintése és elemzése a Log Analyticsben](../../azure-monitor/learn/tutorial-viewdata.md)

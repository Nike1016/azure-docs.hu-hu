---
title: A Windows Azure virtuális gépek architektúrájának munkafolyamata | Microsoft Docs
description: Ez a cikk a szolgáltatás központi telepítésekor a munkafolyamat folyamatainak áttekintését tartalmazza.
services: cloud-services
documentationcenter: ''
author: genlin
manager: Willchen
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 04/08/2019
ms.author: kwill
ms.openlocfilehash: 383f4d26d44871936ccc910f15575db5aec3ec8c
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945327"
---
#    <a name="workflow-of-windows-azure-classic-vm-architecture"></a>A klasszikus Windows Azure VM-architektúra munkafolyamata 
Ez a cikk áttekintést nyújt az Azure-erőforrások, például virtuális gépek üzembe helyezése vagy frissítése során felmerülő munkafolyamat-folyamatokról. 

> [!NOTE]
>Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: Resource Manager és klasszikus. Ez a cikk a klasszikus üzembehelyezési modellt ismerteti.

Az alábbi ábra az Azure-erőforrások architektúráját mutatja be.

![Azure-munkafolyamat](./media/cloud-services-workflow-process/workflow.jpg)

## <a name="workflow-basics"></a>A munkafolyamat alapjai
   
**A**. A RDFE/FFE a felhasználó és a háló közötti kommunikációs útvonal. A RDFE (RedDog Front End) a nyilvánosan elérhető API, amely a felügyeleti portál és a Service Management API, például a Visual Studio, az Azure MMC stb.  A felhasználótól érkező összes kérelem áthalad a RDFE. A FFE (Fabric előtér) az a réteg, amely lefordítja a RDFE érkező kéréseket a Fabric-parancsokra. A RDFE érkező kérések a FFE keresztül jutnak el a háló-vezérlőkhöz.

**B**. A háló vezérlő felelős az adatközpontban lévő összes erőforrás karbantartásához és figyeléséhez. A Fabric Host-ügynökökkel kommunikál a háló operációs rendszeren, például a vendég operációs rendszer verziója, a szervizcsomag, a szolgáltatás konfigurációja és a szolgáltatás állapota.

**C**. A gazdagép ügynöke a gazdagép OSsystem él, és felelős a vendég operációs rendszer beállításához és a vendég ügynökkel való kommunikációhoz (WindowsAzureGuestAgent), hogy frissítse a szerepkört a kívánt cél állapot felé, és a szívverés-ellenőrzéseket a vendég ügynökkel. Ha a gazda ügynök 10 percig nem kap szívverési választ, a gazdagép ügynöke újraindítja a vendég operációs rendszert.

**C2**. A WaAppAgent felelős a WindowsAzureGuestAgent. exe telepítéséhez, konfigurálásához és frissítéséhez.

**D**.  A WindowsAzureGuestAgent felelős a következőkért:

1. A vendég operációs rendszer konfigurálása, beleértve a tűzfalat, az ACL-eket, a LocalStorage erőforrásokat, a szervizcsomagot, a konfigurációt és a tanúsítványokat Annak a felhasználói fióknak a biztonsági azonosítójának beállítása, amelyet a szerepkör fog futni.
2. A szerepkör állapotának kommunikálása a hálóval.
3. A WaHostBootstrapper elindítása és figyelése annak biztosításához, hogy a szerepkör a cél állapotban legyen.

**E**. A WaHostBootstrapper felelős a következőkért:

1. A szerepkör konfigurációjának beolvasása, valamint az összes megfelelő feladat és folyamat elindítása a szerepkör konfigurálásához és futtatásához.
2. Az összes alárendelt folyamat figyelése.
3. A StatusCheck esemény előléptetése a szerepkör-gazdagép folyamatán.

**F**. A IISConfigurator akkor fut, ha a szerepkör teljes IIS webes szerepkörként van konfigurálva (az SDK 1,2 ÜZEMELTETHETŐ WEBMAG szerepköreihez nem fog futni). A következőkért felelős:

1. A szabványos IIS-szolgáltatások indítása
2. Az Újraírási modul konfigurálása a webes konfigurációban
3. A konfigurált szerepkör alkalmazáskészlet beállítása a szolgáltatási modellben
4. Az IIS-naplózás beállítása a DiagnosticStore LocalStorage mappára való Rámutatás céljából
5. Engedélyek és ACL-ek konfigurálása
6. A webhely a (z)% roleroot%: \sitesroot\0 helyen található, a alkalmazáskészlet pedig erre a helyre mutat az IIS futtatásához. 

**G**. Az indítási feladatokat a szerepkör-modell határozza meg, és a WaHostBootstrapper indítja el. Az indítási feladatok aszinkron módon futtathatók a háttérben, és a gazdagép bootstrapper elindítják az indítási feladatot, majd folytatják a többi indítási feladatra. Az indítási feladatok beállítható úgy is, hogy egyszerű (alapértelmezett) módban fussanak, amelyben a gazdagép bootstrapper megvárja az indítási feladat futását, és sikeres (0) kilépési kódot ad vissza a következő indítási feladat folytatása előtt.

**H**. Ezek a feladatok az SDK részét képezik, és a szerepkör szolgáltatás-definíciójában (. csdef) beépülő modulként vannak meghatározva. Az indítási feladatokra kiterjesztve a **DiagnosticsAgent** és a **RemoteAccessAgent** egyediek abban, hogy mindegyik két indítási feladatot definiál, egy normál és egy **/blockStartup** paraméterrel rendelkezőt. A normál indítási feladat háttérbeli indítási feladatként van definiálva, hogy az a háttérben is futtatható legyen, miközben maga a szerepkör fut. A **/blockStartup** indítási feladata egyszerű indítási feladatként van definiálva, így a WaHostBootstrapper megvárja, amíg a folytatás előtt kilép. A **/blockStartup** feladat megvárja a normál feladat inicializálásának befejezését, majd kilép, és lehetővé teszi a gazdagép bootstrapper folytatását. Ez azért történik, hogy a diagnosztika és az RDP-hozzáférés a szerepkör-folyamatok elindítása előtt is konfigurálható legyen (ez a/blockStartup feladat használatával történik). Ez azt is lehetővé teszi, hogy a diagnosztika és az RDP-hozzáférés továbbra is futni fog, miután a gazdagép bootstrapper befejezte az indítási feladatokat (ez a normál feladaton keresztül történik).

**I**. A WaWorkerHost a normál feldolgozói szerepkörök szabványos gazdagép-folyamata. Ez a gazdagép a szerepkör összes dll-fájlját és belépési pontjának kódját tárolja, például a OnStart és a Run műveletet.

**J**. A WaWebHost a webes szerepkörök szabványos gazdagép-folyamata, ha az SDK 1,2-kompatibilis üzemeltethető WebCore (ÜZEMELTETHETŐ WEBMAG) használatára vannak konfigurálva. A szerepkörök a ÜZEMELTETHETŐ WEBMAG módot is engedélyezhetik, ha eltávolítják az elemet a szolgáltatás definíciójában (. csdef). Ebben a módban az összes szolgáltatás kódja és dll-jei a WaWebHost folyamatból futnak. Az IIS (w3wp) nincs használatban, és nincsenek AppPools konfigurálva az IIS-kezelőben, mert az IIS a WaWebHost. exe fájlon belül található.

**K**. A WaIISHost a teljes IIS-t használó webes szerepkörökhöz tartozó szerepkör-belépési pont kódjához tartozó gazdagép folyamata. Ez a folyamat betölti az első DLL-t, amely a **RoleEntryPoint** osztályt használja, és a kódot a következő osztályból hajtja végre (OnStart, Run, OnStop). A folyamat a RoleEntryPoint osztályban létrehozott összes **RoleEnvironment** eseményt (például a StatusCheck és a módosult) is megemeli.

**L**. A W3WP a szabványos IIS-munkavégző folyamat, amelyet akkor használ a rendszer, ha a szerepkör teljes IIS használatára van konfigurálva. Ezzel futtatja a IISConfigurator-ból konfigurált alkalmazáskészlet. Ebben a folyamatban az itt létrehozott RoleEnvironment-események (például a StatusCheck és a megváltozott) jelennek meg. Vegye figyelembe, hogy a RoleEnvironment-események mindkét helyen (WaIISHost és W3wp. exe) fognak tüzet, ha mindkét folyamat eseményeire előfizetnek.

## <a name="workflow-processes"></a>Munkafolyamat-folyamatok

1. A felhasználók kéréseket tesznek elérhetővé, például a. cspkg és a. cscfg fájlok feltöltését, illetve egy adott konfiguráció leállításának vagy konfigurálásának megtételét, és így tovább. Ezt a Azure Portalon vagy a Service Management APIt használó eszközön, például a Visual Studio közzétételi funkcióján keresztül teheti meg. Ez a kérelem a RDFE, hogy elvégezze az összes előfizetéshez kapcsolódó munkát, majd továbbítja a kérést a FFE. A munkafolyamat további lépései egy új csomag üzembe helyezése és elindítása.
2. A FFE megkeresi a megfelelő számítógép-készletet (az ügyfél bemenete, például az affinitási csoport vagy a földrajzi hely, valamint a hálóból való bevitel, például a gép rendelkezésre állása), és kommunikál az adott gépcsoport Master Fabric-vezérlőjével.
3. A Fabric-vezérlő olyan gazdagépet talál, amely rendelkezik elérhető CPU-magokkal (vagy új gazdagépet indít el). A rendszer átmásolja a szervizcsomagot és a konfigurációt a gazdagépre, és a háló vezérlő kommunikál a gazdagép operációs rendszerének gazdagép-ügynökével a csomag üzembe helyezéséhez (a DIPs, a ports, a vendég operációs rendszer stb. beállítása).
4. A gazdagép ügynöke elindítja a vendég operációs rendszert, és kommunikál a vendég ügynökkel (WindowsAzureGuestAgent). A gazdagép szívveréseket küld a vendégnek, hogy megbizonyosodjon róla, hogy a szerepkör a cél állapotára törekszik.
5. A WindowsAzureGuestAgent beállítja a vendég operációs rendszert (tűzfal, ACL, LocalStorage stb.), átmásol egy új XML-konfigurációs fájlt a c:\Config-be, majd elindítja a WaHostBootstrapper folyamatot.
6. A teljes IIS-alapú webes szerepkörök esetében a WaHostBootstrapper elindítja a IISConfigurator-t, és azt jelzi, hogy az IIS-ből törli a webes szerepkör meglévő AppPools.
7. A WaHostBootstrapper beolvassa az **indítási** feladatokat a E:\RoleModel.XML, és megkezdi az indítási feladatok végrehajtását. A WaHostBootstrapper megvárja, amíg az összes egyszerű indítási feladat befejeződött, és "sikeres" üzenetet adott vissza.
8. A teljes IIS-alapú webes szerepkörök esetében a WaHostBootstrapper közli a IISConfigurator, hogy konfigurálja az IIS- `E:\Sitesroot\<index>`alkalmazáskészlet, `<index>` és a helyet a következőre mutasson, `<Sites>` ahol a 0 alapú index a szolgáltatáshoz definiált elemek számával.
9. A WaHostBootstrapper a szerepkör típusától függően elindítja a gazdagép folyamatát:
    1. **Feldolgozói szerepkör**: A WaWorkerHost. exe elindult. A WaHostBootstrapper végrehajtja a OnStart () metódust. A visszatérést követően a WaHostBootstrapper elindítja a Run () metódust, és egyszerre készként jelöli meg a szerepkört, és a terheléselosztó rotációjában (ha InputEndpoints van meghatározva). A WaHostBootsrapper ezután egy hurokba kerül a szerepkör állapotának ellenőrzéséhez.
    1. **SDK 1,2 üzemeltethető webmag webes szerepkör**: A WaWebHost elindult. A WaHostBootstrapper végrehajtja a OnStart () metódust. A visszatérést követően a WaHostBootstrapper elkezdi végrehajtani a Run () metódust, majd egyidejűleg megjelöli a szerepkört, és a terheléselosztó rotációs állapotba helyezi azt. A WaWebHost egy bemelegedési kérelmet (/do.rd_runtime_init beszerzése) bocsát ki. Az összes webes kérelem elküldése a WaWebHost. exe fájlba történik. A WaHostBootsrapper ezután egy hurokba kerül a szerepkör állapotának ellenőrzéséhez.
    1. **Teljes IIS webes szerepkör**: a aIISHost elindult. A WaHostBootstrapper végrehajtja a OnStart () metódust. A visszatérése után elindul a Run () metódus végrehajtása, majd egyidejűleg készként jelöli meg a szerepkört, és a terheléselosztó rotációs állapotba helyezi azt. A WaHostBootsrapper ezután egy hurokba kerül a szerepkör állapotának ellenőrzéséhez.
10. A teljes IIS webes szerepkörbe beérkező webes kérelmek elindítják az IIS-t a W3WP-folyamat elindításához és a kérelem kiszolgálásához, ugyanúgy, mint egy helyszíni IIS-környezetben.

## <a name="log-file-locations"></a>Naplófájl helyei

**WindowsAzureGuestAgent**

- C:\Logs\AppAgentRuntime.Log.  
Ez a napló a szolgáltatás módosításait tartalmazza, beleértve az indítást, a leállásokat és az új konfigurációkat. Ha a szolgáltatás nem változik, a naplófájlban várhatóan nagy mennyiségű idő jelenik meg.
- C:\Logs\WaAppAgent.Log.  
Ez a napló az állapot frissítéseit és a szívverési értesítéseket tartalmazza, és 2-3 másodpercenként frissül.  Ez a napló a példány állapotának egy korábbi nézetét tartalmazza, és azt jelzi, hogy a példány nem üzemkész állapotban van-e.
 
**WaHostBootstrapper**

`C:\Resources\Directory\<deploymentID>.<role>.DiagnosticStore\WaHostBootstrapper.log`
 
**WaWebHost**

`C:\Resources\Directory\<guid>.<role>\WaWebHost.log`
 
**WaIISHost**

`C:\Resources\Directory\<deploymentID>.<role>\WaIISHost.log`
 
**IISConfigurator**

`C:\Resources\Directory\<deploymentID>.<role>\IISConfigurator.log`
 
**IIS-naplók**

`C:\Resources\Directory\<guid>.<role>.DiagnosticStore\LogFiles\W3SVC1`
 
**Windows-eseménynaplók**

`D:\Windows\System32\Winevt\Logs`
 




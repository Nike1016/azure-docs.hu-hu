---
title: Azure Container Instances biztonsági megfontolások
description: Javaslatok a lemezképek és a titkos kulcsok biztonságossá tételéhez a Azure Container Instanceshoz, valamint általános biztonsági megfontolások bármilyen tároló platformhoz
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 04/29/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 618d3a901698e46760d970f6d4fbc4157c5d2ea3
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325920"
---
# <a name="security-considerations-for-azure-container-instances"></a>A Azure Container Instances biztonsági szempontjai

Ez a cikk a Azure Container Instances a Container apps futtatásához való használatának biztonsági szempontjait ismerteti. A témakörök a következők:

> [!div class="checklist"]
> * **Biztonsági javaslatok** a rendszerképek és a titkok kezeléséhez Azure Container instances
> * A tároló-ökoszisztémára vonatkozó **megfontolások** a tároló életciklusa során, bármilyen tároló platform esetében

## <a name="security-recommendations-for-azure-container-instances"></a>A Azure Container Instances biztonsági javaslatai

### <a name="use-a-private-registry"></a>Privát beállításjegyzék használata

A tárolók egy vagy több adattárban található rendszerképekből állnak össze. Ezek a adattárak egy nyilvános beállításjegyzékbe, például a [Docker hub](https://hub.docker.com)-ba vagy egy privát beállításjegyzékbe tartozhatnak. Privát beállításjegyzék pedig például a [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/2.0/), amely a helyszínen vagy virtuális magánfelhőbe is telepíthető. Használhatja továbbá a felhőalapú privát tároló beállításjegyzék-szolgáltatásait is, beleértve [](../container-registry/container-registry-intro.md)a Azure Container registryt is. 

Egy nyilvánosan elérhető tároló-rendszerkép nem garantálja A biztonságot. A tárolók képei több szoftverből állnak, és az egyes szoftveres rétegek biztonsági réseket tartalmazhatnak. A támadások fenyegetésének csökkentése érdekében a lemezképeket privát beállításjegyzékből kell tárolnia és beolvasnia, például Azure Container Registry vagy Docker megbízható beállításjegyzékből. A felügyelt privát beállításjegyzéken kívül a Azure Container Registry támogatja az egyszerű hitelesítési folyamatok Azure Active Directoryn keresztüli egyszerű [szolgáltatás-hitelesítést](../container-registry/container-registry-authentication.md) . Ez a hitelesítés szerepköralapú hozzáférést tartalmaz írásvédett (lekéréses), írási (leküldéses) és tulajdonosi engedélyekhez.

### <a name="monitor-and-scan-container-images"></a>Tároló lemezképének monitorozása és vizsgálata

Az Azure Marketplace-en keresztül elérhetők a biztonsági monitorozási és ellenőrzési megoldások, például a [Twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) és az [Aqua Security](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) . Felhasználhatja a tároló-lemezképek vizsgálatát egy privát beállításjegyzékben, és azonosíthatja a lehetséges biztonsági réseket. Fontos megérteni a különböző megoldások által nyújtott vizsgálat mélységét. 

### <a name="protect-credentials"></a>Hitelesítő adatok megóvása

A tárolók különböző fürtökön és Azure-régiókban is elterjedhetnek. Ezért a bejelentkezésekhez vagy az API-hozzáférésekhez szükséges hitelesítő adatokat, például jelszavakat vagy jogkivonatokat kell biztosítania. Győződjön meg arról, hogy csak az emelt szintű felhasználók férhetnek hozzá a tárolóhoz az átvitel és a nyugalmi állapotban. Leltárba kell venni az összes hitelesítő adatot, majd a fejlesztőknek a tároló platformokhoz tervezett, új titkokat kezelő eszközöket kell használniuk.  Győződjön meg arról, hogy a megoldás tartalmaz-e titkosított adatbázisokat, TLS-titkosítást az átvitel során, valamint a legkevésbé jogosult [szerepköralapú hozzáférés](../role-based-access-control/overview.md)-vezérlést. A [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md) egy felhőalapú szolgáltatás, amely védelmet biztosít a titkosítási kulcsok és a titkos kulcsoknak (például a tanúsítványoknak, a kapcsolatok karakterláncának és a jelszavaknak) a tároló alkalmazások számára. Mivel ezek az adatok bizalmasak és üzleti szempontból kritikus fontosságúak, biztonságos hozzáférést biztosítanak a kulcstartóhoz, így csak a jogosult alkalmazások és felhasználók férhetnek hozzájuk.

## <a name="considerations-for-the-container-ecosystem"></a>A tároló ökoszisztémájának szempontjai

Az alábbi, megfelelően megvalósított és felügyelt biztonsági intézkedések segíthetnek a tároló-ökoszisztémák biztonságossá tételében és védelmében. Ezek a mértékek a tároló életciklusa, a fejlesztés és az éles üzembe helyezés, valamint a tárolók, gazdagépek és platformok széles körére vonatkoznak. 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>A biztonsági rések kezelése a tároló fejlesztési életciklusának részeként 

A tárolók fejlesztési életciklusa során a biztonsági rések felügyeletének használatával javíthatja a biztonsági problémák azonosítását és megoldását, mielőtt azok komolyabb problémát jelentenek. 

### <a name="scan-for-vulnerabilities"></a>Biztonsági rések keresése 

A rendszer folyamatosan észleli az új biztonsági réseket, így a biztonsági rések keresése és azonosítása folyamatos folyamat. A biztonsági rések vizsgálatának beépítése a tároló életciklusa során:

* A fejlesztési folyamat utolsó beadásakor a biztonsági rések vizsgálatát kell végrehajtani a tárolókban, mielőtt a lemezképeket nyilvános vagy privát beállításjegyzékbe szeretné elküldeni. 
* Folytassa a tároló lemezképek vizsgálatát a beállításjegyzékben, és azonosítsa azokat a hibákat, amelyek a fejlesztés során valahogy kimaradtak, és a tárolói lemezképekben használt kódban található újonnan felderített biztonsági réseket.  

### <a name="map-image-vulnerabilities-to-running-containers"></a>Biztonsági rések leképezése a tárolók futtatásához 

A tároló lemezképében azonosított biztonsági rések leképezésére van szükség, így a biztonsági problémák csökkenthetők vagy feloldhatók.  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>Győződjön meg arról, hogy csak a jóváhagyott lemezképek lesznek használatban a környezetben 

A tároló ökoszisztémájában elég változás és volatilitás van, anélkül, hogy az ismeretlen tárolókat is engedélyezi. Csak a jóváhagyott tárolók lemezképének engedélyezése. A nem jóváhagyott tárolók rendszerképének figyelésére és megelőzésére szolgáló eszközökkel és folyamatokkal rendelkezik. 

A támadási felület csökkentésének hatékony módja, és annak megakadályozása, hogy a fejlesztők kritikus biztonsági hibákkal irányítsák a tárolók rendszerképeinek áramlását a fejlesztési környezetbe. Előfordulhat például, hogy egy adott Linux-disztribúciót kihelyez egy alaprendszerképként, lehetőleg egy sovány (alpesi vagy CoreOS), amely az Ubuntu helyett az egyiket, az esetleges támadások felszínének csökkentése érdekében. 

A képaláírás vagy az ujjlenyomatok olyan felügyeleti láncot biztosítanak, amely lehetővé teszi a tárolók integritásának ellenőrzését. A Azure Container Registry támogatja például a Docker [tartalmi megbízhatósági](https://docs.docker.com/engine/security/trust/content_trust) modelljét, amely lehetővé teszi a képkiadók számára, hogy a beállításjegyzékbe leküldött képeket írják alá, és a felhasználók csak az aláírt lemezképek lekérését.

### <a name="permit-only-approved-registries"></a>Csak jóváhagyott kibocsátásiegység-forgalmi jegyzékek engedélyezése 

Egy bővítmény, amely biztosítja, hogy a környezet csak a jóváhagyott lemezképeket használja, csak a jóváhagyott tároló-nyilvántartások használatát engedélyezi. A jóváhagyott tároló-nyilvántartások használatának megkövetelése csökkenti a kockázatot, ha korlátozza az ismeretlen biztonsági rések vagy biztonsági problémák bevezetésének lehetséges lehetőségét. 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>A rendszerképek integritásának biztosítása az életciklus során 

A biztonság kezelésének része a tároló életciklusa során a tároló lemezképek integritásának biztosítása a beállításjegyzékben, valamint azok módosítása vagy üzembe helyezése éles környezetben. 

* A biztonsági réseket, akár kisebbeket is tartalmazó rendszerképeket nem szabad éles környezetben futtatni. Ideális esetben az éles környezetben üzembe helyezett összes lemezképet egy kiválasztott privát beállításjegyzékbe kell menteni. Tartsa meg az éles rendszerképek számát, hogy azok hatékonyan felügyelhetők legyenek.

* Mivel nehezen lehet kijelölni a szoftver forrását egy nyilvánosan elérhető tároló-rendszerképből, lemezképeket építhet ki a forrásból a réteg eredetének megismerése érdekében. Ha a saját készítésű tárolórendszerképben jelentkezik valamilyen biztonsági rés, könnyebben található rá megoldás. Nyilvános rendszerképpel az ügyfeleknek meg kell találniuk egy nyilvános rendszerkép gyökerét, hogy kijavítsák vagy egy másik biztonságos rendszerképet kapjanak a közzétevőtől. 

* Az éles környezetben üzembe helyezett, alaposan beolvasott lemezképek nem garantáltan naprakészek az alkalmazás élettartama szempontjából. Biztonsági rések jelenhetnek meg a rendszerkép korábban nem ismert vagy az éles környezetben való üzembe helyezés után bevezetett rétegeiben. 

  Az éles környezetben üzembe helyezett rendszerképeket az elavult vagy nem frissített rendszerképek azonosítására használják fel rendszeresen. Előfordulhat, hogy a kék-zöld telepítési módszertant és a működés közbeni frissítési mechanizmusokat használja a tároló lemezképének állásidő nélküli frissítéséhez. A rendszerképeket az előző szakaszban ismertetett eszközök segítségével ellenőrizheti. 

* A folyamatos integrációs (CI) folyamat integrált biztonsági ellenőrzéssel gondoskodik a biztonságos rendszerképek létrehozásáról és a privát beállításjegyzékbe való leküldéséről. A CI megoldásba beépített biztonságirés-ellenőrzési funkció biztosítja, hogy az összes teszten megfelelő rendszerképek kerüljenek a privát beállításjegyzékbe, ahonnan az éles számítási feladatok üzembe helyezése történik. 

  A CI-folyamat meghibásodása biztosítja, hogy a sebezhető lemezképek ne legyenek leküldve az éles számítási feladatok üzembe helyezéséhez használt privát beállításjegyzékbe. Emellett automatizálja a rendszerképek biztonsági vizsgálatát is, ha jelentős számú kép van. Máskülönben a rendszerképek manuális vizsgálata rendkívül hosszadalmas és sok hibalehetőséget tartalmazó folyamat lenne. 

### <a name="enforce-least-privileges-in-runtime"></a>A legalacsonyabb jogosultságok betartatása futásidőben 

A minimális jogosultságok fogalma egy alapszintű biztonsági eljárás, amely a tárolók esetében is érvényes. Sebezhetőség kihasználása esetén általában a támadók hozzáférhetnek a feltört alkalmazáshoz vagy folyamathoz tartozó jogosultságokkal. Annak biztosítása, hogy a tárolók a lehető legalacsonyabb jogosultságokkal működjenek, és a feladatok elvégzéséhez szükséges hozzáférés csökkenti a kockázatot. 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>Csökkentse a tároló támadási felületét a szükségtelen jogosultságok eltávolításával 

A potenciális támadási felületet is csökkentheti, ha eltávolítja a nem használt vagy szükségtelen folyamatokat vagy jogosultságokat a tároló futtatókörnyezetből. A Kiemelt jogosultságú tárolók root-ként futnak. Ha egy rosszindulatú felhasználó vagy munkaterhelés kikerül egy emelt szintű tárolóba, a tároló ezt követően a rendszer gyökerét fogja futtatni.

### <a name="whitelist-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>Engedélyezési fájlok és végrehajtható fájlok, amelyeket a tároló elérhet vagy futtathat 

A változók és az ismeretlenk számának csökkentése segít fenntartani a stabil és megbízható környezetet. A tárolók korlátozása, hogy csak az előjóváhagyott vagy engedélyezett fájlok elérésére vagy futtatására legyenek jogosultak, és a végrehajtható fájlok kipróbált módon korlátozzák a kockázatot.  

Ez sokkal könnyebben kezelhető, ha a rendszer az elejéről implementálja az engedélyezési listát. Az engedélyezési lista a szabályozás és kezelhetőség mértékét mutatja be, amelyből megtudhatja, hogy az alkalmazás megfelelően működjön-e, és milyen fájlokra és végrehajtható fájlokra van szükség. 

Az engedélyezési lista nem csupán csökkenti a támadási felületet, de a rendellenességeket is megadhatja, és megelőzheti a "zajos szomszéd" és a "Container breakout" forgatókönyvek használati eseteit. 

### <a name="enforce-network-segmentation-on-running-containers"></a>Hálózati szegmentálás kényszerített futtatása a futó tárolók esetében  

Az egyik alhálózatban lévő tárolók védelmének biztosítása érdekében egy másik alhálózaton lévő biztonsági kockázatokkal kell fenntartani a hálózati szegmentálást (vagy a nano szegmentálást) vagy a futó tárolók közötti elkülönítést. A hálózati szegmentálás fenntartása a megfelelőségi megbízatások teljesítéséhez szükséges tárolók használata esetén is szükséges lehet.  

Az [Aqua](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) partneri eszköz például automatizált megközelítést biztosít a nano-szegmentáláshoz. Az Aqua figyeli a tároló hálózati tevékenységeit futtatókörnyezetben. Ez azonosítja az összes bejövő és kimenő hálózati kapcsolatot más tárolókkal, szolgáltatásokkal, IP-címekkel és a nyilvános internettel. A nano-szegmentáció automatikusan jön létre a figyelt forgalom alapján. 

### <a name="monitor-container-activity-and-user-access"></a>Tároló tevékenység és felhasználói hozzáférés figyelése 

Más informatikai környezetekhez hasonlóan a tároló-ökoszisztémával kapcsolatos tevékenység-és felhasználói hozzáférés következetes monitorozásával gyorsan azonosíthatja a gyanús vagy kártékony tevékenységeket. Az Azure tároló-figyelési megoldásokat biztosít, többek között:

* [Azure monitor a tárolók](../azure-monitor/insights/container-insights-overview.md) számára az Azure Kubernetes szolgáltatásban (ak) üzemeltetett Kubernetes környezetekben üzembe helyezett számítási feladatok teljesítményének figyeléséhez. Tárolók az Azure Monitor lehetővé teszi gyűjtését memória és a mérőszámok a processzor teljesítménye látható-e a tartományvezérlők, a csomópontok és a Kubernetes, a metrikák API-n keresztül a rendelkezésre álló tárolók. 

* Az [Azure Container monitoring megoldás](../azure-monitor/insights/containers.md) segítségével egyetlen helyen tekintheti meg és kezelheti a többi Docker-és Windows-tároló gazdagépét. Példa:

  * Tekintse meg a tárolók által használt parancsokat megjelenítő részletes naplózási információkat. 
  * A tárolók hibaelhárítása központi naplók megtekintésével és keresésével anélkül, hogy távolról kellene megtekintenie a Docker-vagy a Windows-gazdagépeket.  
  * Olyan tárolók keresése, amelyek zajosak lehetnek, és a gazdagépen felesleges erőforrásokat fogyasztanak.
  * Megtekintheti a tárolók központi CPU-, memória-, tárterület-és hálózati használati és teljesítmény-információit.  

  A megoldás támogatja a tároló-szervezőket, például a Docker Swarm, a DC/OS, a nem felügyelt Kubernetes, a Service Fabric és a Red Hat OpenShift. 

### <a name="monitor-container-resource-activity"></a>Tároló-erőforrás figyelése tevékenység 

Figyelje az erőforrás-tevékenységeket, például a fájlokat, a hálózatot és a tárolók által használt egyéb erőforrásokat. A figyelés erőforrás-tevékenység és a felhasználás egyaránt hasznos a teljesítmény monitorozása és biztonsági mértékeként. 

[Azure monitor](../azure-monitor/overview.md) lehetővé teszi az Azure-szolgáltatások alapszintű figyelését azáltal, hogy lehetővé teszi a metrikák, a tevékenységek naplói és a diagnosztikai naplók gyűjtését. A tevékenységnaplók például elárulják, hogy mikor történt az új erőforrások létrehozása vagy módosítása. 

Rendelkezésre állnak olyan metrikák, amelyek a virtuális gépek különböző erőforrásairól, sőt a virtuális gépen futó operációs rendszerről biztosítanak teljesítménystatisztikát. Az adatokat megtekintheti az Azure Portal valamelyik böngészőjében, és riasztásokat hozhat létre a metrikák alapján. Azure Monitor biztosítja a leggyorsabb metrikai folyamatot (5 percen belül 1 percet), ezért a kritikus fontosságú riasztások és értesítések esetében érdemes használni. 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>Az összes tároló rendszergazdai felhasználói hozzáférésének naplózása naplózáshoz 

A tároló ökoszisztémájának, a tároló-beállításjegyzéknek és a tároló lemezképeknek való rendszergazdai hozzáférés pontos naplózási nyomvonalának fenntartása. Előfordulhat, hogy ezek a naplók naplózási célokra szükségesek, és a biztonsági incidensek után törvényszéki bizonyítékként lesznek hasznosak. Erre a célra használhatja az [Azure Container monitoring megoldást](../azure-monitor/insights/containers.md) . 

## <a name="next-steps"></a>További lépések

* További információ a tárolók sebezhetőségének kezeléséről a [Twistlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/) és az [Aqua biztonságával](https://www.aquasec.com/solutions/azure-container-security/)kapcsolatos megoldásokkal.

* További információ a [tárolók biztonságáról az Azure-ban](https://azure.microsoft.com/resources/container-security-in-microsoft-azure/).
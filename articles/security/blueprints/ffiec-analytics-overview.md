---
title: Azure Security and Compliance Blueprint – adatelemzés a FFIEC
description: Azure Security and Compliance Blueprint – adatelemzés a FFIEC
services: security
author: meladie
ms.assetid: 31b70690-682c-4c38-9bbb-14dce162867a
ms.service: security
ms.topic: article
ms.date: 06/20/2018
ms.author: meladie
ms.openlocfilehash: 474ab3ddd6c931b17b2ece1e22e1e1d4b62f0cdb
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946790"
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-ffiec-financial-services"></a>Azure Security and Compliance Blueprint: FFIEC pénzügyi szolgáltatások elemzései

## <a name="overview"></a>Áttekintés

Ez a Azure Security and Compliance Blueprint útmutatást nyújt egy adatelemzési architektúra Azure-beli üzembe helyezéséhez, amely alkalmas a szövetségi pénzügyi intézmény vizsgálati Tanácsa által szabályozott pénzügyi adatok gyűjtésére, tárolására és lekérésére. (FFIEC).

Ez a hivatkozási architektúra, a megvalósítási útmutató és a fenyegetés modell olyan alapot biztosít az ügyfeleknek, hogy megfeleljenek a FFIEC követelményeinek. Ez a megoldás olyan alapkonfigurációt biztosít, amellyel az ügyfelek FFIEC-kompatibilis módon telepíthetik a számítási feladatokat az Azure-ba. Ez a megoldás azonban nem használható éles környezetben, mert további konfigurálásra van szükség.

A FFIEC-megfelelőség megvalósítása megköveteli, hogy a minősített ellenőrök igazolják az üzemi ügyfelek megoldásait. A naplózást a FFIEC tagjainak, többek között a Federal Reserve System (FRB), a Federal deposit Insurance Corporation (FDIC), a National Credit Union Administration (NCUA), a számvevő irodájának elnökei látják el. a pénznem (OCC) és a fogyasztói pénzügyi védelmi iroda (CFPB). Ezek a vizsgáztatók tanúsítják, hogy a naplózást az auditált intézménytől független értékelők végzik. Az ügyfelek felelősek az architektúrával létrehozott megoldások megfelelő biztonsági és megfelelőségi felmérésének elvégzéséért, mivel a követelmények az egyes ügyfelek implementációjának sajátosságai alapján változhatnak.

## <a name="architecture-diagram-and-components"></a>Architektúra ábrája és összetevői

Ez a Azure Security and Compliance Blueprint egy elemzési platformot biztosít, amely alapján az ügyfelek létrehozhatják saját elemzési eszközeiket. A hivatkozási architektúra olyan általános használati esetet vázol fel, amelyben az ügyfelek az SQL/adat-rendszergazda vagy az operatív adatok frissítésein keresztül, egy operatív felhasználón keresztül adatokat küldenek a tömeges adatimportáláshoz. A munkafolyamatok mindkét Azure Functions belefoglalják az adatAzure SQL Databaseba való adatimportálást. Az Azure Functionst az ügyfélnek kell konfigurálnia az Azure Portal segítségével, hogy az egyes ügyfelek saját elemzési igényeihez egyedi importálási feladatokat kezeljen.

Az Azure számos jelentéskészítési és elemzési szolgáltatást kínál az ügyfelek számára. Ez a megoldás Azure Machine Learning szolgáltatásokat tartalmaz, amelyek Azure SQL Database segítségével gyorsan böngészhetnek az adatkezelésen, és gyorsabb eredményeket biztosítanak az intelligens modellezéssel. Azure Machine Learning az adathalmazok közötti új kapcsolatok felfedezésével növeli a lekérdezési sebességet. Ha az adatok több statisztikai függvényen keresztül lettek kitanítva, legfeljebb 7 további lekérdezési készlet (8 összesen, beleértve az ügyfél-kiszolgálót is) szinkronizálható ugyanazzal a táblázatos modellel a lekérdezési számítási feladatok terjesztéséhez és a válaszadási idő csökkentéséhez.

A továbbfejlesztett elemzési és jelentéskészítési szolgáltatásokhoz az Azure SQL Database-adatbázisok oszlopcentrikus indexekkel konfigurálhatók. A Azure Machine Learning és az Azure SQL-adatbázisok egyaránt méretezhetők felfelé vagy lefelé, vagy kikapcsolhatók teljes mértékben az ügyfelek általi használatra adott válaszként. Az összes SQL-forgalom SSL-titkosítással rendelkezik az önaláírt tanúsítványok belefoglalásával. Az ajánlott eljárás az, hogy az Azure megbízható hitelesítésszolgáltató használatát javasolja a fokozott biztonság érdekében.

Miután a rendszer feltölti az adatfeldolgozást az Azure SQL Databaseba, és Azure Machine Learning által tanítja, azt az operatív felhasználó és az SQL/adatkezelő is megemészti a Power BI. Power BI intuitív módon jeleníti meg az adatokat, és több adatkészletben is összegyűjti az információkat, hogy jobban megtekintse a képet. A magas fokú alkalmazkodóképesség és a Azure SQL Database egyszerű integrálása biztosítja, hogy az ügyfelek az üzleti igényeknek megfelelően konfigurálják a forgatókönyvek széles körét.

A megoldás Azure Storage-fiókokat használ, amelyekkel az ügyfelek a Storage Service Encryption használatával kezelhetik az inaktív adatok titkosságát. Az Azure a rugalmasság érdekében három másolatot tárol az ügyfél kiválasztott adatközpontjában. A földrajzi redundáns tárolás biztosítja, hogy az adatok egy több száz mérföld távolságra lévő másodlagos adatközpontba replikálódnak, és ismét az adott adatközponton belül három példányban tárolódnak, ami megakadályozza, hogy az ügyfél elsődleges adatközpontjában negatív esemény maradjon adatok.

A fokozott biztonság érdekében az ebben a megoldásban lévő összes erőforrást erőforráscsoportként kezeli Azure Resource Manageron keresztül. Azure Active Directory szerepköralapú hozzáférés-vezérléssel szabályozható az üzembe helyezett erőforrásokhoz való hozzáférés, beleértve azok kulcsait is Azure Key Vault. A rendszer állapotát Azure Security Center és Azure Monitor figyeli. Az ügyfelek mindkét figyelési szolgáltatást a naplók rögzítéséhez és a rendszerállapot megjelenítéséhez egyetlen, könnyen navigálható irányítópulton.

Azure SQL Database általában SQL Server Management Studio (SSMS) használatával történik, amely egy olyan helyi gépről fut, amely a Azure SQL Database biztonságos VPN-vagy ExpressRoute-kapcsolaton keresztüli elérésére van konfigurálva. **A Microsoft javaslatot tesz a VPN-vagy ExpressRoute-kapcsolat konfigurálására a felügyeleti és az adatimportáláshoz a hivatkozási architektúra erőforráscsoporthoz**.

![A FFIEC] -referenciák architektúrájának diagramja (images/ffiec-analytics-architecture.png "A FFIEC") -referenciák architektúrájának diagramja

Ez a megoldás az alábbi Azure-szolgáltatásokat használja. Az üzembe helyezési architektúra részletei az [üzembe helyezési architektúra](#deployment-architecture) szakaszban találhatók.

- Application Insights
- Azure Active Directory
- Azure Data Catalog
- Azure Disk Encryption
- Azure Event Grid
- Azure Functions
- Azure Key Vault
- Azure Machine Learning
- Azure Monitor (naplók)
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Virtual Network
    - (1)/16 hálózat
    - (2)/24 hálózat
    - (2) hálózati biztonsági csoportok
- Power BI-irányítópult

## <a name="deployment-architecture"></a>Üzembe helyezési architektúra

A következő szakasz az üzembe helyezési és megvalósítási elemeket részletezi.

**Azure Event Grid**: [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) lehetővé teszi, hogy az ügyfelek könnyedén építsenek be alkalmazásokat eseményvezérelt architektúrákkal. A felhasználók kiválaszthatják azt az Azure-erőforrást, amelyre előfizethetnek, és az eseménykezelőt vagy a webhookot egy végponttal küldik el az eseménynek. Az ügyfelek a webhook-végpontok biztonságossá tételéhez lekérdezési paramétereket adhatnak hozzá a webhook URL-címéhez az esemény-előfizetés létrehozásakor. A Azure Event Grid csak a HTTPS webhook-végpontokat támogatja. Azure Event Grid lehetővé teszi az ügyfeleknek, hogy a különböző felhasználók számára megadott hozzáférési szintet különböző felügyeleti műveletekkel, például esemény-előfizetések listázásával, újak létrehozásával és kulcsok létrehozásával szabályozzák. A Event Grid Azure szerepköralapú hozzáférés-vezérlést használ.

**Azure functions**: [Azure functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) egy kiszolgáló nélküli számítási szolgáltatás, amely lehetővé teszi a felhasználók számára, hogy az infrastruktúra explicit kiosztása vagy kezelése nélkül futtasson kódot igény szerint. Az Azure Functions használatával különféle eseményekre reagálva futtathat szkripteket vagy kódrészleteket.

**Azure Machine learning szolgáltatás**: A [Azure Machine learning](https://docs.microsoft.com/azure/machine-learning/service/) egy adatelemzési módszer, amely lehetővé teszi a számítógépek számára a meglévő adatelemzések használatát a jövőbeli viselkedések, eredmények és trendek előrejelzéséhez.

**Azure Data Catalog**: [Data Catalog](../../data-catalog/overview.md) az adatforrásokat könnyen felderíthetővé és érthetővé teszi az adatkezelést végző felhasználók számára. A közös adatforrások regisztrálása, címkézett és pénzügyi adatként való keresése is lehetséges. Az adatok a meglévő helyükön maradnak, de a metaadatok egy példánya hozzá lett adva a Data Cataloghoz, valamint az adatforrás helyére mutató hivatkozás. A metaadatok indexelésének köszönhetően az adatforrások egy egyszerű keresés által felfedezhetővé és könnyen értelmezhetővé válnak a felhasználók számára.

### <a name="virtual-network"></a>Virtuális hálózat

Az architektúra a 10.200.0.0/16 címtartomány szerinti magánhálózati virtuális hálózatot definiálja.

**Hálózati biztonsági csoportok**: A [hálózati biztonsági csoportok](../../virtual-network/virtual-network-vnet-plan-design-arm.md) olyan hozzáférés-vezérlési listát tartalmaznak, amelyek engedélyezik vagy megtagadják a forgalmat a virtuális hálózaton belül. A hálózati biztonsági csoportok használatával biztonságossá teheti a forgalmat egy alhálózaton vagy egy adott virtuálisgép-szinten. A következő hálózati biztonsági csoportok léteznek:

  - Hálózati biztonsági csoport Active Directory
  - Hálózati biztonsági csoport a munkaterhelés számára

A hálózati biztonsági csoportok mindegyike megnyitotta a megadott portokat és protokollokat, hogy a megoldás biztonságosan és megfelelően működjön. Emellett a következő konfigurációk mindegyik hálózati biztonsági csoport esetében engedélyezve vannak:

- A [diagnosztikai naplók és események](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) engedélyezve vannak, és Storage-fiókban tárolódnak
- Azure Monitor naplók a [hálózati biztonsági csoport&#39;s diagnosztikai naplóihoz](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json) vannak csatlakoztatva

Alhálózatok: Minden alhálózat társítva van a hozzá tartozó hálózati biztonsági csoporttal.

### <a name="data-in-transit"></a>Átvitt adatok

Az Azure alapértelmezés szerint titkosítja az Azure-adatközpontok és az összes kommunikációját. Az Azure Storage-ba irányuló összes tranzakció a Azure Portal a HTTPS-en keresztül történik.

### <a name="data-at-rest"></a>Inaktív adat

Az architektúra titkosítva, adatbázis-naplózással és egyéb mértékekkel védi a nyugalmi állapotban lévő adatok védelmét.

**Azure Storage**: A titkosított adatoknak a nyugalmi követelmények teljesítése érdekében az összes [Azure Storage](https://azure.microsoft.com/services/storage/) [Storage Service encryption](../../storage/common/storage-service-encryption.md)használ. Ez segít megvédeni és megőrizni a FFIEC által meghatározott szervezeti biztonsági kötelezettségvállalások és megfelelőségi követelmények támogatásával kapcsolatos adatvédelmet.

**Azure Disk Encryption**: [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) kihasználja a Windows BitLocker szolgáltatását, hogy mennyiségi titkosítást biztosítson az adatlemezek számára. A megoldás integrálva van Azure Key Vaultekkel a lemezes titkosítási kulcsok szabályozása és kezelése érdekében.

**Azure SQL Database**: A Azure SQL Database példány a következő adatbázis-biztonsági mértékeket használja:

- [Active Directory a hitelesítés és az engedélyezés](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) lehetővé teszi az adatbázis-felhasználók és más Microsoft-szolgáltatások Identitáskezelés kezelését egy központi helyen.
- Az [SQL Database naplózása](../../sql-database/sql-database-auditing.md) nyomon követi az adatbázis eseményeit, és egy Azure Storage-fiókban lévő naplóba írja azokat.
- Azure SQL Database úgy van konfigurálva, hogy [transzparens](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)adattitkosítást használjon, amely valós idejű titkosítást és visszafejtést végez az adatbázis, a társított biztonsági másolatok és a tranzakciós naplófájlok számára, hogy megvédje az adatokat a nyugalmi állapotban. Az transzparens adattitkosítás biztosítja, hogy a tárolt adataik nem érvényesek a jogosulatlan hozzáférésre.
- A [Tűzfalszabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) megakadályozzák az adatbázis-kiszolgálók hozzáférését a megfelelő engedélyek megadása előtt. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.
- Az [SQL](../../sql-database/sql-database-threat-detection.md) -veszélyforrások észlelése lehetővé teszi az észlelést és a reagálást a potenciális fenyegetésekre, mivel ezek a hibák a gyanús adatbázis-tevékenységek, a potenciális sebezhetőségek, az SQL-injektálási támadások és a rendellenes adatbázis-hozzáférési minták esetében
- A [titkosított oszlopok](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) biztosítják, hogy a bizalmas adatok soha ne jelenjenek meg az adatbázis-rendszeren belüli egyszerű szövegként. Az adattitkosítás engedélyezése után csak az ügyfélalkalmazások vagy az alkalmazások férhetnek hozzá a kulcsokhoz.
- A [kiterjesztett tulajdonságok](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) segítségével felhagyhatja az adatalanyok feldolgozását, mivel lehetővé teszi a felhasználók számára, hogy egyéni tulajdonságokat adjanak az adatbázis-objektumokhoz, és a "megszűnt" címkével lássa el az adataikat az alkalmazás logikájának támogatásához a kapcsolódó pénzügyi folyamatok feldolgozásának megelőzése érdekében adatok.
- A [sorok szintjének biztonsága](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) lehetővé teszi a felhasználók számára, hogy szabályzatokat határozzanak meg az adathozzáférés korlátozásához a feldolgozás megszüntetéséhez.
- [SQL Database a dinamikus adatmaszkolás](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) korlátozza a bizalmas adatok megjelenítését azáltal, hogy az adatokat nem Kiemelt felhasználók vagy alkalmazások számára fedi le. A dinamikus adatmaszkolás automatikusan képes észlelni a potenciálisan bizalmas adatokat, és javaslatot tesz a megfelelő maszkok alkalmazására. Ez segít az adathozzáférés azonosításában és csökkentésében, hogy ne lépjen ki az adatbázisból jogosulatlan hozzáférés útján. Az ügyfelek feladata a dinamikus adatmaszkolási beállítások módosítása az adatbázis-sémájuk betartásához.

### <a name="identity-management"></a>Identitáskezelés

Az alábbi technológiák az Azure-környezetben tárolt adathozzáférések kezelésére szolgáló képességeket biztosítanak:

- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) a Microsoft&#39;s több-bérlős felhőalapú címtár-és Identitáskezelés-kezelő szolgáltatás. A megoldás összes felhasználója Azure Active Directoryban jön létre, beleértve a Azure SQL Databasehoz hozzáférő felhasználókat is.
- Az alkalmazáshoz való hitelesítés Azure Active Directory használatával történik. További információ: [alkalmazások integrálása a Azure Active Directorysal](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Emellett az adatbázis oszlopának titkosítása a Azure Active Directory használatával hitelesíti az alkalmazást a Azure SQL Database. További információ: a [bizalmas adatok védelme Azure SQL Databaseban](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- Az [Azure szerepköralapú hozzáférés-vezérlés](../../role-based-access-control/role-assignments-portal.md) lehetővé teszi a rendszergazdáknak, hogy részletes hozzáférési engedélyeket határozzanak meg, amelyek csak a felhasználóknak a feladataik elvégzéséhez szükséges hozzáférést biztosítják. Ahelyett, hogy minden felhasználó számára korlátlan engedélyt adna az Azure-erőforrásokhoz, a rendszergazdák csak bizonyos műveleteket végezhetnek el az adatokhoz való hozzáféréshez. Az előfizetés-hozzáférés az előfizetés rendszergazdájára korlátozódik.
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) lehetővé teszi, hogy az ügyfelek csökkentsék azon felhasználók számát, akik hozzáféréssel rendelkeznek bizonyos adatokhoz. A rendszergazdák Azure Active Directory Privileged Identity Management használhatják az emelt szintű identitások felderítését, korlátozását és figyelését, valamint az erőforrásokhoz való hozzáférésüket. Ez a funkció az igény szerinti, igény szerinti rendszergazdai hozzáférés biztosítására is használható, ha szükséges.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) észleli a szervezet&#39;s identitásait érintő lehetséges biztonsági réseket, automatikus válaszokat konfigurál a szervezet&#39;s identitásával kapcsolatos gyanús műveletekre, és megvizsgálja a gyanús incidenseket, hogy tegye meg a megfelelő lépéseket a megoldásához.

### <a name="security"></a>Biztonság

**Titkok kezelése**: A megoldás a kulcsok és titkok kezeléséhez [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) használ. Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. Az alábbi Azure Key Vault-funkciók segítenek az ügyfeleknek az ilyen jellegű adatvédelemben és hozzáférésben:

- A speciális hozzáférési szabályzatok a szükséges módon vannak konfigurálva.
- Key Vault hozzáférési házirendek minimálisan szükséges engedélyekkel vannak definiálva a kulcsokhoz és a titkokhoz.
- Key Vault összes kulcsának és titkának lejárati dátuma van.
- A Key Vault összes kulcsát speciális hardveres biztonsági modulok védik. A kulcs típusa HSM-védelemmel ellátott 2048-bites RSA-kulcs.
- Minden felhasználó és identitás a szerepköralapú hozzáférés-vezérlés használatával minimálisan szükséges engedélyeket kap.
- Key Vault diagnosztikai naplói legalább 365 napos megőrzési időtartammal engedélyezettek.
- A kulcsok számára engedélyezett titkosítási műveletek csak a szükségesek.

**Azure Security Center**: A [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)segítségével az ügyfelek központilag alkalmazhatják és kezelhetik a munkaterhelések biztonsági szabályzatait, korlátozhatja a fenyegetéseket, és észlelheti és reagálhat a támadásokra. Azure Security Center az Azure-szolgáltatások meglévő konfigurációit is elérheti, hogy konfigurációs és szolgáltatási javaslatokat nyújtson a biztonsági helyzetek és az adatvédelem javításához.

Azure Security Center különböző észlelési képességekkel figyelmezteti az ügyfeleket a környezetekhez kapcsolódó lehetséges támadásokra. Ezek a riasztások értékes információkat tartalmaznak arról, hogy mi váltotta ki a riasztást, valamint a támadás forrásáról és az általa célba vett erőforrásokról. A Azure Security Center [előre meghatározott biztonsági riasztásokkal](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)rendelkezik, amelyek egy fenyegetés vagy gyanús tevékenység bekövetkeztekor aktiválódnak. A Azure Security Center [Egyéni riasztási szabályai](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) lehetővé teszik, hogy az ügyfelek új biztonsági riasztásokat határozzanak meg a környezetből már összegyűjtött adatok alapján.

A Azure Security Center rangsorolt biztonsági riasztásokat és incidenseket biztosít, így egyszerűbbé válik az ügyfelek számára a potenciális biztonsági problémák felderítése és kezelése. Az egyes észlelt fenyegetésekkel kapcsolatos [fenyegetési intelligenciáról szóló jelentés](https://docs.microsoft.com/azure/security-center/security-center-threat-report) jön létre, amely segít az incidensek megválaszolásában a fenyegetések kivizsgálásában és szervizelését.

### <a name="logging-and-auditing"></a>Naplózás és naplózás

Az Azure-szolgáltatások széles körben naplózzák a rendszer és a felhasználó tevékenységét, valamint a rendszer állapotát:
- **Tevékenységek naplói**: A [tevékenységek naplói](../../azure-monitor/platform/activity-logs-overview.md) betekintést nyújtanak az előfizetésben lévő erőforrásokon végrehajtott műveletekre. A Tevékenységnaplók segítenek meghatározni a művelet kezdeményezőjét, az előfordulás időpontját és az állapotot.
- **Diagnosztikai naplók**: A [diagnosztikai naplók](../../azure-monitor/platform/diagnostic-logs-overview.md) az összes erőforrás által kibocsátott összes naplót tartalmazzák. Ezek a naplók a Windows-eseménynaplókat, az Azure Storage-naplókat, a Key Vault naplókat, valamint Application Gateway hozzáférési és tűzfal-naplókat tartalmaznak. Az összes diagnosztikai napló egy központi és titkosított Azure Storage-fiókba írja az archiválást. A megőrzés a felhasználó által konfigurálható, akár 730 nap, hogy megfeleljen a szervezetre vonatkozó megőrzési követelményeknek.

**Naplók Azure monitor**: Ezeket a naplókat a rendszer a feldolgozás, tárolás és irányítópult-jelentéskészítés [Azure monitor naplófájljaiban](https://azure.microsoft.com/services/log-analytics/) összesíti. Az adatgyűjtés után a rendszer külön táblákba rendezi az adatokat Log Analytics munkaterületeken belül minden adattípushoz, ami lehetővé teszi, hogy az összes adatokat együtt elemezze az eredeti forrástól függetlenül. Emellett a Azure Security Center integrálható Azure Monitor naplókkal, így az ügyfelek Kusto-lekérdezéseket használhatnak a biztonsági események adatainak eléréséhez és más szolgáltatásokból származó adatokkal való összekapcsolásához.

Az architektúra részeként az alábbi Azure- [figyelési megoldások](../../monitoring/monitoring-solutions.md) szerepelnek:
-   [Active Directory Assessment](../../azure-monitor/insights/ad-assessment.md): A Active Directory Health-ellenőrzési megoldás rendszeres időközönként kivizsgálja a kiszolgálói környezetek kockázatait és állapotát, és rangsorolja a telepített kiszolgálói infrastruktúrára jellemző ajánlásokat.
- [SQL Assessment](../../azure-monitor/insights/sql-assessment.md): Az SQL Health-ellenőrzési megoldás rendszeres időközönként kivizsgálja a kiszolgálói környezetek kockázatait és állapotát, és az ügyfelek számára az üzembe helyezett kiszolgáló-infrastruktúrára jellemző ajánlások rangsorolt listáját biztosítja.
- [Agent Health](../../monitoring/monitoring-solution-agenthealth.md): Az Agent Health-megoldás jelentést készít, hogy hány ügynök van üzembe helyezve, valamint a földrajzi eloszlásuk, valamint a nem válaszoló ügynökök száma, valamint az operatív adatküldés alatt álló ügynökök száma.
-   [Activity log Analytics](../../azure-monitor/platform/collect-activity-logs.md): Az Activity Log Analytics-megoldás segítséget nyújt az Azure-tevékenységek naplóinak elemzéséhez az ügyfelek összes Azure-előfizetése között.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) tárolja, futtatja és felügyeli a runbookok. Ebben a megoldásban a runbookok segítséget nyújt a naplók Azure SQL Databaseból való gyűjtésében. Az Automation [change Tracking](../../automation/change-tracking.md) megoldás lehetővé teszi, hogy az ügyfelek könnyedén azonosíthassák a környezet változásait.

**Azure monitor**: [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) segíti a felhasználókat a teljesítmény nyomon követésében, a biztonság fenntartásában és a trendek azonosításában azáltal, hogy lehetővé teszi a szervezetek számára, hogy naplózzák, riasztásokat hozzon létre és archiválják az adatok archiválását, beleértve az Azure

**Application Insights**: A [Application Insights](https://docs.microsoft.com/azure/application-insights/) egy bővíthető Application Performance Management-(APM-) szolgáltatás, amely több platformon is használható webfejlesztőknek. Észleli a teljesítménnyel kapcsolatos rendellenességeket, és hatékony elemzési eszközöket tartalmaz a problémák diagnosztizálásához és az alkalmazással ténylegesen felhasználható felhasználók megismeréséhez. A szolgáltatás úgy lett kialakítva, hogy a felhasználók folyamatosan javítsák a teljesítményt és a használhatóságot.

## <a name="threat-model"></a>Veszélyforrások modellje

Az ehhez a hivatkozási architektúrához tartozó Adatfolyam-diagram [letölthető](https://aka.ms/ffiec-analytics-tm) , vagy a következő címen érhető el. Ez a modell lehetővé teszi az ügyfeleknek, hogy a módosítások végrehajtása során megértsék a rendszerinfrastruktúra lehetséges kockázatait.

![Elemzés a FFIEC Threat modelhez](images/ffiec-analytics-threat-model.png "Elemzés a FFIEC Threat modelhez")

## <a name="compliance-documentation"></a>Megfelelőségi dokumentáció

A [Azure Security and Compliance Blueprint – FFIEC ügyfél-felelősségi mátrix](https://aka.ms/ffiec-crm) FELSOROLJA a FFIEC által igényelt összes célkitűzést. Ez a mátrix részletesen ismerteti, hogy az egyes célok megvalósítása a Microsoft, az ügyfél vagy a kettő közötti megosztás feladata-e.

A [Azure Security and Compliance Blueprint – FFIEC](https://aka.ms/ffiec-analytics-cim) adatelemzési implementációs mátrixa információt nyújt arról, hogy az adatelemzési architektúra milyen FFIEC célkitűzéseket céloz meg, beleértve a megvalósítás részletes leírásait is megfelel az egyes érintett célkitűzések követelményeinek.


## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok

### <a name="vpn-and-expressroute"></a>VPN-és ExpressRoute

A biztonságos VPN-alagutat vagy [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) úgy kell konfigurálni, hogy biztonságosan létesítsen kapcsolatot az adatelemzési hivatkozási architektúra részeként üzembe helyezett erőforrásokkal. A VPN-vagy ExpressRoute megfelelő beállításával az ügyfelek hozzáadhatnak egy védelmi réteget az átvitelhez.

A biztonságos VPN-alagút az Azure-nal való megvalósításával létrehozhat egy helyszíni hálózat és egy Azure-Virtual Network közötti virtuális magánhálózati kapcsolatot. Ez a kapcsolat az interneten keresztül történik, és lehetővé teszi, &quot;hogy&quot; az ügyfelek biztonságosan továbbítsák az adatokat egy&#39;titkosított kapcsolaton belül az ügyfél hálózata és az Azure között. A helyek közötti VPN egy olyan biztonságos, érett technológia, amelyet évtizedek óta minden méretben üzembe helyezett a vállalatok. Ebben a beállításban az [IPsec-alagút mód](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) titkosítási mechanizmusként van használatban.

Mivel a VPN-alagúton belüli forgalom egy helyek közötti VPN-kapcsolaton keresztül halad át az interneten, a Microsoft egy másik, még biztonságosabb kapcsolódási lehetőséget kínál. Az Azure ExpressRoute egy dedikált WAN-kapcsolat az Azure és egy helyszíni hely vagy egy Exchange-szolgáltató között. Mivel a ExpressRoute-kapcsolatok nem az interneten keresztül haladnak át, ezek a kapcsolatok megbízhatóbbak, gyorsabbak, kisebb késések és nagyobb biztonságot biztosítanak, mint a szokásos kapcsolatok az interneten keresztül. Továbbá, mivel ez az ügyfél&#39;-távközlési szolgáltató közvetlen kapcsolata, az adatforgalom nem az interneten keresztül történik, ezért nem teszi elérhetővé.

Ajánlott eljárások egy biztonságos hibrid hálózat megvalósításához, amely kiterjeszti a helyszíni hálózatot az Azure- [](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)ra.

### <a name="extract-transform-load-process"></a>Kinyerés – átalakítás – betöltési folyamat

[](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) A kinyert adatok betölthetők Azure SQL Databaseba anélkül, hogy külön kinyerési, átalakítási, betöltési vagy importálási eszközt kellene létrehoznia. A Base lehetővé teszi, hogy a T-SQL-lekérdezéseken keresztül hozzáférjen az adatokhoz. A Microsoft üzleti intelligenciával és elemzésével foglalkozó verem, valamint a SQL Server rendszerrel kompatibilis, külső gyártótól származó eszközök is használhatók.

### <a name="azure-active-directory-setup"></a>Azure Active Directory telepítő
[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) elengedhetetlen az üzembe helyezés kezeléséhez és a környezettel kommunikáló személyzethez való hozzáféréshez. Egy meglévő Windows Server-Active Directory [négy kattintással](../../active-directory/hybrid/how-to-connect-install-express.md)integrálható Azure Active Directoryba. Az ügyfelek az üzembe helyezett Active Directory infrastruktúrát (tartományvezérlőket) egy meglévő Azure Active Directory számára is megadhatják, ha a központilag telepített Active Directory infrastruktúra egy Azure Active Directory erdő altartománya.

## <a name="disclaimer"></a>Jogi nyilatkozat

 - Ez a dokumentum csak tájékoztató jellegű. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, VÉLELMEZETT VAGY TÖRVÉNYES GARANCIÁT A JELEN DOKUMENTUMBAN FOGLALT INFORMÁCIÓK ALAPJÁN. Ez a dokumentum "aktuálisként" van megadva. Az ebben a dokumentumban kifejezett információk és nézetek, beleértve az URL-címeket és az internetes webhelyek más hivatkozásait, értesítés nélkül változhatnak. A dokumentumot olvasó ügyfeleink a használatuk kockázatát viselik.
 - A jelen dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termékben vagy-megoldásban található szellemi tulajdonhoz.
 - Az ügyfelek belső referenciák szerint másolhatják és használhatják ezt a dokumentumot.
 - A jelen dokumentumban szereplő javaslatok megnövelték az adatok, a hálózat vagy a számítási erőforrások használatát az Azure-ban, és növelhetik az ügyfelek Azure-licencét vagy előfizetési költségeit.
 - Ez az architektúra arra szolgál, hogy az ügyfelek számára a konkrét követelményekhez igazodva és nem használható éles környezetben.
 - Ez a dokumentum hivatkozásként van kifejlesztve, és nem használható az összes olyan eszköz meghatározására, amelyekkel az ügyfél megfelel bizonyos megfelelőségi követelményeknek és előírásoknak. Az ügyfeleknek jóvá kell hagyniuk a szervezeten belüli jogi támogatást a jóváhagyott ügyfél-implementációkban.

---
title: Az Azure Security bemutatása | Microsoft Docs
description: Ismerkedjen meg az Azure biztonságával, szolgáltatásával és működésével.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: e87fc74b3fa989471f9074a33fc66d8cb8250aa0
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68927841"
---
# <a name="introduction-to-azure-security"></a>Az Azure Security bemutatása
## <a name="overview"></a>Áttekintés
Tudjuk, hogy a biztonság az egyik a felhőben, és mennyire fontos, hogy pontos és kellő időben tájékozódjon az Azure biztonságáról. Az Azure az alkalmazások és szolgáltatások használatának egyik legjobb oka az, hogy kiaknázza a biztonsági eszközök és képességek széles körét. Ezek az eszközök és képességek segítik a biztonságos megoldások létrehozását a biztonságos Azure platformon. Microsoft Azure biztosítja az ügyféladatok titkosságát, integritását és rendelkezésre állását, ugyanakkor lehetővé teszi az átlátható elszámoltathatóságot is.

Annak érdekében, hogy jobban megértse az ügyfelek és a Microsoft műveleti perspektíváinak Microsoft Azure belül megvalósított biztonsági vezérlők gyűjteményét, ez a tanulmány az "Azure Security bemutatása" című tanulmányt ismerteti, amely átfogó megjelenést biztosít Microsoft Azure.

### <a name="azure-platform"></a>Azure-platform
Az Azure egy nyilvános felhőalapú szolgáltatási platform, amely az operációs rendszerek, a programozási nyelvek, a keretrendszerek, az eszközök, az adatbázisok és az eszközök széles választékát támogatja. Linux-tárolókat is futtathat a Docker-integrációval; alkalmazások készítése JavaScript, Python, .NET, PHP, Java és Node. js használatával az iOS-, Android-és Windows-eszközökön is létrehozhatók háttérrendszer.

Az Azure nyilvános felhőalapú szolgáltatásai ugyanazokat a technológiákat támogatják, mint a fejlesztők és az informatikai szakemberek, akik már használják és bíznak. Ha a-ra épít, vagy áttelepíti az IT-eszközöket, egy nyilvános felhőalapú szolgáltatóra támaszkodik, amely a szervezet képességei alapján gondoskodik az alkalmazások és adatok védelméről a szolgáltatásokkal és az általuk biztosított vezérlőkkel a felhőalapú eszközök biztonságának kezeléséhez.

Az Azure infrastruktúráját olyan létesítményekből tervezték, amelyek egyszerre több millió ügyfelet üzemeltetnek, és olyan megbízható alapot biztosítanak, amely alapján a vállalatok megfelelnek a biztonsági követelményeknek.

Emellett az Azure a konfigurálható biztonsági beállítások széles körét biztosítja, és lehetővé teszi, hogy a biztonság testre szabható legyen, hogy megfeleljen a szervezete üzembe helyezésének egyedi követelményeinek. Ez a dokumentum segít megérteni, hogy az Azure biztonsági képességei hogyan segíthetnek a követelmények teljesítésében.

> [!Note]
> A dokumentum elsődleges célja az ügyfelek felé irányuló vezérlők használata, amelyek segítségével testre szabhatja és növelheti az alkalmazások és szolgáltatások biztonságát.
>
> Áttekintő információkat biztosítunk, de részletes információk arról, hogyan védi a Microsoft az Azure platformot, a [Microsoft adatvédelmi központban](https://www.microsoft.com/TrustCenter/default.aspx)található információk alapján.

### <a name="abstract"></a>Absztrakt
Kezdetben a nyilvános felhőbe való áttelepítést a költségmegtakarítás és az innováció rugalmassága vezérli. A biztonság a nyilvános felhőben való áttelepítéshez egy ideig, sőt a megjelenő is jelentős problémát jelent. A nyilvános Felhőbeli biztonság azonban jelentős aggodalmat váltott ki a felhőalapú Migrálás egyik illesztőprogramján. Ennek hátterében a nagyméretű nyilvános felhőalapú szolgáltatók kiváló képessége az alkalmazások és a felhőalapú eszközök adatainak védelme.

Az Azure infrastruktúráját úgy tervezték, hogy képes legyen ügyfelek millióit egyidejűleg kiszolgáló alkalmazásokat üzemeltetni, és olyan megbízható alapot nyújtson, amely megfelel a vállalatok biztonsági igényeinek. Emellett az Azure a konfigurálható biztonsági beállítások széles körét biztosítja, és lehetővé teszi azok vezérlését, így testre szabhatja az üzembe helyezések egyedi követelményeit, hogy megfeleljen az IT-vezérlési szabályzatoknak, és külső rendeletek.

Ez a tanulmány a Microsoft Microsoft Azure Cloud platformon belüli biztonságának megközelítését ismerteti:
* A Microsoft által megvalósított biztonsági funkciók az Azure-infrastruktúra, az ügyféladatok és az alkalmazások biztonságossá tételéhez.
* Az Azure-szolgáltatások és-biztonsági funkciók a szolgáltatások és az Azure-előfizetések adatai biztonságának kezeléséhez állnak rendelkezésére.

## <a name="summary-azure-security-capabilities"></a>Az Azure biztonsági funkcióinak összefoglalása
Az alábbi táblázat a Microsoft által az Azure-infrastruktúra, az ügyféladatok és a biztonságos alkalmazások biztonságossá tételéhez bevezetett biztonsági funkciók rövid leírását tartalmazza.
### <a name="security-features-implemented-to-secure-the-azure-platform"></a>Az Azure platform biztonságossá tételéhez megvalósított biztonsági funkciók:
Az alábbi szolgáltatások olyan funkciók, amelyekkel ellenőrizheti, hogy az Azure platform biztonságos módon van-e kezelve. További részletezést talál a Microsoft az ügyfelek megbízhatóságával kapcsolatos kérdéseivel kapcsolatban négy területen: Biztonságos platform, adatvédelem & vezérlők, megfelelőség és átláthatóság.


| [Biztonságos platform](https://www.microsoft.com/en-us/trustcenter/Security/default.aspx)  | [Adatvédelmi & vezérlők](https://www.microsoft.com/en-us/trustcenter/Privacy/default.aspx)  |[Megfelelőség](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx)   | [Átláthatóság](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx) |
| :-- | :-- | :-- | :-- |
| [Biztonsági fejlesztési ciklus](https://www.microsoft.com/en-us/sdl/), belső auditok | [Az adatkezelés minden alkalommal](https://www.microsoft.com/en-us/trustcenter/Privacy/You-own-your-data) | [Adatvédelmi központ](https://www.microsoft.com/en-us/trustcenter/default.aspx) |[Hogyan védi a Microsoft az ügyféladatokat az Azure-szolgáltatásokban](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx) |
| [Kötelező biztonsági képzés, háttérbeli ellenőrzés](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx) |  [Az adathely vezérlése](https://www.microsoft.com/en-us/trustcenter/Privacy/Where-your-data-is-located) |  [Common Controls Hub](https://www.microsoft.com/en-us/trustcenter/Common-Controls-Hub) |[Hogyan kezeli a Microsoft az adattárolási helyet az Azure-szolgáltatásokban](https://azuredatacentermap.azurewebsites.net/)|
| [Behatolási teszt](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx), [behatolás-észlelés, DDoS](https://www.microsoft.com/en-us/trustcenter/Security/ThreatManagement), [naplózás & naplózás](https://www.microsoft.com/en-us/trustcenter/Security/AuditingAndLogging) | [Adathozzáférés biztosítása a használati feltételekhez](https://www.microsoft.com/en-us/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms) |  [A Cloud Services átvilágítás ellenőrzőlista](https://www.microsoft.com/en-us/trustcenter/Compliance/Due-Diligence-Checklist) |[A Microsoft a következő feltételekkel férhet hozzá az adataihoz:](https://www.microsoft.com/en-us/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms)|
| [A Art adatközpont, a](https://www.microsoft.com/en-us/cloud-platform/global-datacenters)fizikai biztonság, a [biztonságos hálózat](network-overview.md) állapota | [Válaszadás a bűnüldözésre](https://www.microsoft.com/en-us/trustcenter/Privacy/Responding-to-govt-agency-requests-for-customer-data) |  [Megfelelőség szolgáltatás, hely & iparág szerint](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx) |[Hogyan védi a Microsoft az ügyféladatokat az Azure-szolgáltatásokban](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx)|
|  [Biztonsági incidens válasza](https://aka.ms/SecurityResponsepaper), [megosztott felelősség](https://aka.ms/sharedresponsibility) |[Szigorú adatvédelmi normák](https://www.microsoft.com/en-us/TrustCenter/Privacy/We-set-and-adhere-to-stringent-standards) |  | [Az Azure-szolgáltatások, az átláthatósági központ minősítésének áttekintése](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx)|



### <a name="security-features-offered-by-azure-to-secure-data-and-application"></a>Az Azure által kínált biztonsági funkciók az adatkezelés és az alkalmazások biztonságossá tételéhez
A Cloud Service-modelltől függően az alkalmazás vagy szolgáltatás biztonságának kezeléséért felelős személy felelőssége is fennáll. Az Azure platformon elérhető lehetőségek állnak rendelkezésre, hogy segítséget nyújtsanak a feladatoknak a beépített funkciókkal, valamint az Azure-előfizetésben üzembe helyezhető partneri megoldásokkal.

A beépített funkciók hat (6) funkcionális területen vannak rendszerezve: Műveletek, alkalmazások, tárolás, hálózatkezelés, számítás és identitás. További részletek az Azure platformon elérhető funkciókról és képességekről az alábbi hat (6) területen az összegző információk alapján.

## <a name="operations"></a>Műveletek
Ez a szakasz további információkat tartalmaz a biztonsági műveletek főbb funkcióiról, valamint az ezekkel a képességekkel kapcsolatos összefoglaló információkkal kapcsolatban.

### <a name="security-and-audit-dashboard"></a>Security and Audit irányítópult
A [Security and Audit megoldás](../../security-center/security-center-intro.md) átfogó áttekintést nyújt a szervezet informatikai biztonsági állapotáról, és [beépített keresési lekérdezésekkel](https://blogs.technet.microsoft.com/msoms/2016/01/21/easy-microsoft-operations-management-suite-search-queries/) segíti a jelentős problémák megoldását. A [Security and Audit](https://technet.microsoft.com/library/mt484091.aspx) irányítópult a Azure monitor naplókban a biztonsággal kapcsolatos összes adathoz tartozó kezdőképernyő. Magas szintű betekintést nyújt a számítógépek biztonsági állapotával. Olyan funkciót is kínál, amellyel megjeleníthető az elmúlt 24 óra, 7 nap vagy bármely más egyéni időszak összes eseménye.

Emellett beállíthatja, hogy a Security & Compliance [automatikusan végrehajtson bizonyos műveleteket](https://blogs.technet.microsoft.com/robdavies/2016/04/20/simple-look-at-oms-alert-remediation-with-runbooks-part-1/) , amikor a rendszer egy adott eseményt észlel.

### <a name="azure-resource-manager"></a>Azure Resource Manager
A [Azure Resource Manager](../../azure-resource-manager/resource-manager-deployment-model.md) lehetővé teszi, hogy csoportként működjön együtt a megoldás erőforrásaival. A megoldás összes erőforrását egyetlen, koordinált műveletben telepítheti, frissítheti vagy törölheti. Az üzembe helyezéshez [Azure Resource Manager sablont](https://blogs.technet.microsoft.com/canitpro/2015/06/29/devops-basics-infrastructure-as-code-arm-templates/) használ, és ez a sablon különböző környezetekben, például tesztelési, átmeneti és éles környezetben is működhet. A Resource Manager biztonsági, naplózási és címkézési szolgáltatásokat biztosít, hogy segítsen az erőforrások kezelésében a telepítést követően.

Azure Resource Manager sablon alapú központi telepítések segítenek az Azure-ban üzembe helyezett megoldások biztonságának javításában, mivel a szabványos biztonsági vezérlési beállítások és a szabványosított sablon alapú központi telepítések integrálására is használhatók. Ez csökkenti a manuális központi telepítések során esetlegesen felmerülő biztonsági konfigurációs hibák kockázatát.

### <a name="application-insights"></a>Application Insights
A [Application Insights](https://docs.microsoft.com/azure/application-insights/) egy bővíthető Application Performance Management (APM) szolgáltatás webes fejlesztőknek. A Application Insights segítségével figyelheti az élő webalkalmazásokat, és automatikusan észlelheti a teljesítménnyel kapcsolatos rendellenességeket. Hatékony elemzési eszközöket tartalmaz, amelyek segítségével diagnosztizálhatja a problémákat, és megtudhatja, hogy a felhasználók miként tudják ténylegesen az alkalmazásaihoz. Az alkalmazást folyamatosan figyeli az alkalmazás futása során, a tesztelés során, illetve a közzététel vagy a telepítés után.

A Application Insights diagramokat és táblákat hoz létre, amelyek megmutatják, hogy a legtöbb felhasználó mikor kapja meg a legtöbb felhasználót, hogyan reagál az alkalmazásra, és milyen jól szolgálja ki azokat a külső szolgáltatások, amelyektől függenek.

Ha összeomlik, hiba vagy teljesítménnyel kapcsolatos probléma merül fel, részletesen megkeresheti a telemetria adatokat az OK diagnosztizálásához. A szolgáltatás pedig e-maileket küld, ha az alkalmazás rendelkezésre állása és teljesítménye megváltozik. Az alkalmazás betekintése így értékes biztonsági eszközvé válik, mivel segít a titkosság, integritás és rendelkezésre állási biztonság hármasban való rendelkezésre állásában.

### <a name="azure-monitor"></a>Azure Monitor
A [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) vizualizációkat, lekérdezéseket, útválasztást, riasztásokat, automatikus méretezést és automatizálást kínál az Azure-infrastruktúrából ([műveletnapló](../../azure-monitor/platform/activity-logs-overview.md)) és minden egyes Azure-erőforrásból ([diagnosztikai naplókból](../../azure-monitor/platform/diagnostic-logs-overview.md)) származó adatokhoz. A Azure Monitor használatával riasztást kaphat az Azure-naplókban létrehozott biztonsággal kapcsolatos eseményekről.

### <a name="azure-monitor-logs"></a>Azure Monitor-naplók
[Azure monitor naplók](https://azure.microsoft.com/documentation/services/log-analytics/) – az Azure-erőforrások mellett helyszíni és harmadik féltől származó felhőalapú infrastruktúra (például AWS) számára biztosít informatikai felügyeleti megoldást. Azure Monitorból származó adatok közvetlenül átirányíthatók Azure Monitor naplókba, így egy helyen láthatja a teljes környezet mérőszámait és naplóit.

Azure Monitor naplók hasznos eszközök lehetnek a kriminalisztikai és egyéb biztonsági elemzésekben, mivel az eszköz lehetővé teszi, hogy gyorsan keressen nagy mennyiségű biztonsággal kapcsolatos bejegyzést rugalmas lekérdezési megközelítéssel. Emellett a helyszíni [tűzfal és a proxy naplói is exportálhatók az Azure-ba, és az elemzéshez Azure monitor naplók használatával is elérhetők.](../../log-analytics/log-analytics-agent-windows.md)

### <a name="azure-advisor"></a>Azure Advisor
A [Azure Advisor](../../advisor/index.yml) egy személyre szabott felhőalapú tanácsadó, amely segít az Azure-beli üzembe helyezések optimalizálásában. A program elemzi az erőforrás-konfigurációs és -használati telemetriákat, Ezután olyan megoldásokat javasol, amelyekkel javítható a [teljesítmény](../../advisor/advisor-performance-recommendations.md), a [Biztonság](../../advisor/advisor-security-recommendations.md)és az erőforrások [magas rendelkezésre állása](../../advisor/advisor-high-availability-recommendations.md) , miközben lehetőség van a [teljes Azure-ráfordítás csökkentésére](../../advisor/advisor-cost-recommendations.md). A Azure Advisor biztonsági javaslatokat tesz elérhetővé, ami jelentősen javíthatja az Azure-ban üzembe helyezett megoldások általános biztonsági állapotát. Ezek az ajánlások a Azure Security Center által végzett biztonsági elemzésből származnak [.](../../security-center/security-center-intro.md)

### <a name="azure-security-center"></a>Azure Security Center
[Az Azure Security Center](../../security-center/security-center-intro.md) az Azure-erőforrások biztonsági felügyeletének átláthatóbbá és szabályozhatóbbá tételével megkönnyíti a fenyegetések megelőzését, észlelését és elhárítását. Az ügyfél összes előfizetésére kiterjedő, integrált biztonsági monitorozást és szabályzatkezelést biztosít, megkönnyíti a nehezen észlelhető fenyegetések azonosítását, és számos biztonsági megoldással együttműködik.

Emellett a Azure Security Center a biztonsági műveleteket is segíti azáltal, hogy egyetlen irányítópultot biztosít, amely a felszínre kerülő riasztásokat és javaslatokat azonnal végrehajtja. Gyakran a Azure Security Center-konzolon egyetlen kattintással javíthatja a problémákat.
## <a name="applications"></a>Alkalmazások
A szakasz további információkat tartalmaz az alkalmazások biztonságának főbb funkcióiról, valamint az ezekkel a képességekkel kapcsolatos összegző információkkal kapcsolatban.

### <a name="web-application-vulnerability-scanning"></a>Webalkalmazások sebezhetőségének vizsgálata
Az [app Service-alkalmazás](../../app-service/overview.md) biztonsági rések tesztelésének megkezdéséhez az egyik legegyszerűbb módszer az, hogy az [integráció a sztaniol biztonsággal](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) lehetővé tegye az alkalmazáson belüli, egykattintásos biztonsági rések vizsgálatát. A teszt eredményeit egy könnyen értelmezhető jelentésben tekintheti meg, és megtudhatja, hogyan javíthatja ki az egyes biztonsági réseket részletes utasításokkal.

### <a name="penetration-testing"></a>Behatolásvizsgálat
Ha szeretné elvégezni a saját behatolási teszteket, vagy egy másik képolvasó-csomagot vagy szolgáltatót szeretne használni, kövesse az [Azure penetráció tesztelési jóváhagyási folyamatát](https://docs.microsoft.com/azure/security/fundamentals/pen-testing ) , és a kívánt penetrációs tesztek végrehajtásához előzetes jóváhagyást kell kérnie.

### <a name="web-application-firewall"></a>Webalkalmazási tűzfal
Az [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) webalkalmazási TŰZFALA (WAF) megvédi a webalkalmazásokat a gyakori webalapú támadásoktól, például az SQL-injektálástól, a helyközi parancsfájl-támadásoktól és a munkamenet-eltérítéstől. Előre konfigurálva van az [Open Web Application Security Project (OWASP) által azonosított fenyegetésekkel szemben, mint az első 10 leggyakoribb biztonsági rés](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).

### <a name="authentication-and-authorization-in-azure-app-service"></a>Hitelesítés és engedélyezés az Azure App Service-ben
[App Service hitelesítés/engedélyezés](../../app-service/overview-authentication-authorization.md) funkció lehetővé teszi, hogy az alkalmazás bejelentkezzen a felhasználókba, így nem kell módosítania a kódot az alkalmazás-háttérön. Egyszerű módszert biztosít az alkalmazás védelme és a felhasználónkénti adatmennyiség használata.

### <a name="layered-security-architecture"></a>Rétegzett biztonsági architektúra
Mivel [app Service környezetek](../../app-service/environment/app-service-app-service-environment-intro.md) egy [Azure-Virtual Network](../../virtual-network/virtual-networks-overview.md)üzembe helyezett elkülönített futtatókörnyezeti környezetet biztosítanak, a fejlesztők létrehozhatnak egy rétegzett biztonsági architektúrát, amely különböző szintű hálózati hozzáférést biztosít az egyes alkalmazások szintjeihez. Gyakori a célja, hogy elrejtse az API-t az általános Internet-hozzáféréstől, és csak a felsőbb rétegbeli webalkalmazások számára engedélyezze az API-k meghívását. A [hálózati biztonsági csoportok (NSG-k)](https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/) olyan Azure Virtual Network alhálózatokon használhatók, amelyek app Service környezetet tartalmaznak az API-alkalmazásokhoz való nyilvános hozzáférés korlátozására.

### <a name="web-server-diagnostics-and-application-diagnostics"></a>Webkiszolgáló-diagnosztika és Application Diagnostics
App Service Web Apps diagnosztikai funkciókat biztosít a webkiszolgálóról és a webalkalmazásból származó adatok naplózásához. Ezeket a rendszer logikailag elkülöníti a webkiszolgáló- [diagnosztika](../../app-service/troubleshoot-diagnostic-logs.md) és az [Application Diagnostics](https://technet.microsoft.com/library/hh530058(v=sc.12).aspx)szolgáltatásban. A webkiszolgáló két fő előrelépést tartalmaz a webhelyek és alkalmazások diagnosztizálásában és hibaelhárításában.

Az első új szolgáltatás valós idejű állapotinformációkat tartalmaz az alkalmazáskészletekről, a munkavégző folyamatokról, a helyekről, az alkalmazás-tartományokról és a futó kérelmekről. A második új előnyök azok a részletes nyomkövetési események, amelyek a kérelem nyomon követésére szolgálnak a teljes kérelem-és reagálási folyamat során.

A nyomkövetési események összegyűjtésének engedélyezéséhez az IIS 7 konfigurálható úgy, hogy automatikusan rögzítse a teljes nyomkövetési naplókat XML formátumban az eltelt idő vagy a hibakódok alapján megadott kérelmek esetében.

#### <a name="web-server-diagnostics"></a>Webkiszolgáló-diagnosztika
Engedélyezheti vagy letilthatja a következő típusú naplókat:

-   Részletes hiba naplózása – a hibát jelző HTTP-állapotkódok részletes információi (400 vagy újabb állapotkód). Ez olyan információkat tartalmazhat, amelyek segíthetnek meghatározni, hogy a kiszolgáló miért adta vissza a hibakódot.

-   Sikertelen kérelmek nyomkövetése – részletes információk a sikertelen kérésekről, beleértve a kérés feldolgozásához és az egyes összetevőkben használt IIS-összetevők nyomkövetését. Ez akkor lehet hasznos, ha a hely teljesítményének növelését vagy az adott HTTP-hiba visszaadását okozó elkülönítést próbálja meg használni.

-   Webkiszolgáló-naplózás – a W3C bővített naplófájl formátumával HTTP-tranzakciókra vonatkozó információk. Ez akkor hasznos, ha meghatározza a helyek általános metrikáit, például a kezelt kérelmek számát vagy egy adott IP-címről érkező kéréseket.

#### <a name="application-diagnostics"></a>Alkalmazásdiagnosztika
Az [Application Diagnostics](../../app-service/troubleshoot-diagnostic-logs.md) lehetővé teszi egy webalkalmazás által létrehozott információk rögzítését. A ASP.NET-alkalmazások a [System. Diagnostics. Trace](https://msdn.microsoft.com/library/system.diagnostics.trace) osztály használatával naplózzák az adatokat az Application Diagnostics-naplóba. Application Diagnostics két fő típusú esemény létezik, amelyek az alkalmazások teljesítményével és az alkalmazás hibáival és hibáival kapcsolatosak. A hibák és hibák a kapcsolati, a biztonsági és a meghibásodási problémákra is kioszthatók. A hibákkal kapcsolatos hibák általában az alkalmazás kódjával kapcsolatos problémákhoz kapcsolódnak.

Application Diagnostics a következő módokon tekintheti meg az eseményeket:

-   Összes (az összes esemény megjelenítése)
-   Alkalmazás hibái (a kivételek eseményeinek megjelenítése)
-   Teljesítmény (a teljesítmény eseményeinek megjelenítése)

## <a name="storage"></a>Storage
A szakasz további információkat tartalmaz az Azure Storage biztonságának főbb funkcióiról, valamint az ezekkel a képességekkel kapcsolatos összegző információkkal kapcsolatban.

### <a name="role-based-access-control-rbac"></a>Szerepköralapú hozzáférés-vezérlés (RBAC)
A Storage-fiókja a szerepköralapú Access Control (RBAC) segítségével biztosítható. Az adathozzáférésre vonatkozó biztonsági szabályzatokat kényszerítő szervezetek számára elengedhetetlen a hozzáférés korlátozása a [szükséges ismeret](https://en.wikipedia.org/wiki/Need_to_know) és a [legalacsonyabb jogosultsági szintű](https://en.wikipedia.org/wiki/Principle_of_least_privilege) biztonsági elvek alapján. Ezek a hozzáférési jogosultságok úgy érhetők el, hogy a megfelelő RBAC-szerepkört rendeli hozzá egy bizonyos hatókörhöz tartozó csoportokhoz és alkalmazásokhoz. A felhasználókhoz a jogosultságok hozzárendeléséhez használhatja a [beépített RBAC](../../role-based-access-control/built-in-roles.md)-szerepköröket, például a Storage-fiók közreműködőjét. A Storage-fiókok [Azure Resource Manager](../../storage/common/storage-security-guide.md) modell használatával történő elérését szerepköralapú Access Control (RBAC) segítségével lehet vezérelni.

### <a name="shared-access-signature"></a>Közös hozzáférési aláírás
A [közös hozzáférésű jogosultságkód (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) delegált hozzáférést biztosít a tárfiókon lévő erőforrásokhoz. Az SAS azt jelenti, hogy a megadott időszakra és meghatározott engedélyekkel engedélyezheti az ügyfél számára a Storage-fiókban lévő objektumokra vonatkozó korlátozott engedélyeket. Ezeket a korlátozott engedélyeket a fiók hozzáférési kulcsainak megosztása nélkül is megadhatja.

### <a name="encryption-in-transit"></a>Titkosítás átvitel közben
Az átvitel közbeni titkosítás egy olyan mechanizmus, amely az adatok védelmét a hálózatokon keresztül továbbítja. Az Azure Storage használatával az alábbiakkal védheti meg az adatvédelmet:
-   [Átviteli szintű titkosítás](../../storage/common/storage-security-guide.md), például https, ha az Azure Storage-ba vagy az-ba helyezi át az adatátvitelt.

-   [Vezetékes titkosítás](../../storage/common/storage-security-guide.md), például [SMB 3,0 titkosítás](../../storage/common/storage-security-guide.md) az [Azure-fájlmegosztás](../../storage/files/storage-dotnet-how-to-use-files.md)számára.

-   Ügyféloldali titkosítás, amely a tárterületre való átvitel előtt titkosítja az adatátvitelt, és visszafejti az adatmennyiséget a tárterületről való átadást követően.

### <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban
Számos szervezet esetében az adattitkosítás az adatok védelme, a megfelelőség és az adatok szuverenitása szempontjából kötelező lépés. Három Azure Storage-beli biztonsági funkció áll rendelkezésre, amelyek a "nyugalmi állapotban" lévő adatok titkosítását teszik lehetővé:

-   [Storage Service encryption](../../storage/common/storage-service-encryption.md) lehetővé teszi, hogy a Storage szolgáltatás automatikusan titkosítsa az adattitkosítást az Azure Storage-ba való íráskor.

-   Az [ügyféloldali titkosítás](../../storage/common/storage-client-side-encryption.md) emellett biztosítja a titkosítás nyugalmi funkcióját is.

-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) lehetővé teszi a IaaS virtuális gépek által használt operációsrendszer-lemezek és adatlemezek titkosítását.

### <a name="storage-analytics"></a>Storage Analytics
[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) naplózást végez, és metrikai adatokat biztosít egy Storage-fiókhoz. Ezekkel az adatokkal kérések követhetők nyomon, elemezhetők a használati trendek és diagnosztizálni lehet a tárfiókokkal kapcsolatos problémákat. Storage Analytics a tárolási szolgáltatás sikeres és sikertelen kéréseivel kapcsolatos részletes információkat naplózza. Ezek az információk az egyes kérések figyelésére és a tárolási szolgáltatással kapcsolatos problémák diagnosztizálására használhatók. A kéréseket a rendszer a lehető legjobb módon naplózza. A rendszer naplózza a következő típusú hitelesített kérelmeket:
-   Sikeres kérések.

-   Sikertelen kérések, beleértve az időtúllépést, a szabályozást, a hálózatot, az engedélyezést és az egyéb hibákat.

-   Közös hozzáférésű aláírást (SAS) használó kérelmek, beleértve a sikertelen és sikeres kérelmeket.

-   Elemzési adatkérések.

### <a name="enabling-browser-based-clients-using-cors"></a>Böngészőalapú ügyfelek engedélyezése a CORS használatával
Az eltérő [eredetű erőforrás-megosztás (CORS)](https://docs.microsoft.com/rest/api/storageservices/fileservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) egy olyan mechanizmus, amely lehetővé teszi, hogy a tartományok minden más engedélyt adjanak a többi erőforráshoz való hozzáféréshez. A felhasználói ügynök további fejléceket küld, így biztosítva, hogy az adott tartományból betöltött JavaScript-kód hozzáférjen egy másik tartományban található erőforrásokhoz. Az utóbbi tartomány ezután további fejlécekkel válaszol, amelyek engedélyezik vagy megtagadják az eredeti tartományhoz való hozzáférést az erőforrásokhoz.

Az Azure Storage Services mostantól támogatja a CORS-t, így ha beállította a szolgáltatás CORS-szabályait, a rendszer kiértékeli, hogy a szolgáltatásra vonatkozó megfelelő hitelesített kérést egy másik tartományon keresztül hozták-e létre.
## <a name="networking"></a>Hálózat
A szakasz további információkat tartalmaz az Azure hálózati biztonságának főbb funkcióiról, valamint az ezekkel a képességekkel kapcsolatos összegző információkkal kapcsolatban.

### <a name="network-layer-controls"></a>Hálózati réteg vezérlői
A hálózati hozzáférés-vezérlés az adott eszközök vagy alhálózatok kapcsolatának korlátozására szolgál, és a hálózati biztonság magját jelenti. A hálózati hozzáférés-vezérlés célja annak biztosítása, hogy a virtuális gépek és szolgáltatások csak azokhoz a felhasználókhoz és eszközökhöz legyenek elérhetők, amelyekhez elérhetővé szeretné tenni őket.

#### <a name="network-security-groups"></a>Network Security Groups (Hálózati biztonsági csoportok)
A [hálózati biztonsági csoport (NSG)](../../virtual-network/virtual-network-vnet-plan-design-arm.md) egy alapszintű, állapot-nyilvántartó csomagszűrő tűzfal, amely lehetővé teszi, hogy [5 rekord](https://www.techopedia.com/definition/28190/5-tuple)alapján vezérelje a hozzáférést. A NSG nem biztosítanak alkalmazás-rétegbeli ellenőrzést vagy hitelesített hozzáférés-vezérlést. Használhatók az alhálózatok közötti adatforgalom vezérlésére az Azure Virtual Network és az Azure-Virtual Network és az Internet közötti forgalom között.

#### <a name="route-control-and-forced-tunneling"></a>Irányítás és kényszerített bújtatás
Az útválasztási viselkedés az Azure-beli virtuális hálózatokon való vezérlésének lehetősége kritikus hálózati biztonsági és hozzáférés-vezérlési képesség. Ha például biztos szeretne lenni abban, hogy az Azure Virtual Network felé irányuló összes forgalom áthalad a virtuális biztonsági berendezésen, meg kell tudnia határozni és testreszabni az útválasztási viselkedést. Ezt úgy teheti meg, hogy a felhasználó által megadott útvonalakat konfigurálja az Azure-ban.

A [felhasználó által megadott útvonalak](../../virtual-network/virtual-networks-udr-overview.md) lehetővé teszik a bejövő és kimenő útvonalak testreszabását az egyes virtuális gépekre vagy alhálózatokra áthelyezett forgalom számára, hogy a lehető legbiztonságosabb útvonalat biztosítsa. A [kényszerített bújtatás](https://www.petri.com/azure-forced-tunneling) olyan mechanizmus, amellyel biztosítható, hogy a szolgáltatások ne kezdeményezzenek kapcsolatot az internethez csatlakozó eszközökhöz.

Ez különbözik a bejövő kapcsolatok fogadásának és a rájuk adott válaszoknak. Az előtér-webkiszolgálóknak válaszolnia kell az internetes gazdagépekről érkező kérésekre, és így az internetre irányuló forgalom engedélyezve van a webkiszolgálók számára, és a webkiszolgálók válaszolni tudnak.

A kényszerített bújtatás általában arra szolgál, hogy a kimenő forgalmat az internetre kényszerítse, hogy a helyszíni biztonsági proxykat és tűzfalakat is átugorjon.

#### <a name="virtual-network-security-appliances"></a>Biztonsági berendezések Virtual Network
Míg a hálózati biztonsági csoportok, a felhasználó által megadott útvonalak és a kényszerített bújtatás biztosítja a biztonságot az [OSI-modell](https://en.wikipedia.org/wiki/OSI_model)hálózati és szállítási rétegei számára, előfordulhat, hogy a verem magasabb szintjein engedélyezni szeretné a biztonságot. Ezeket a kibővített hálózati biztonsági szolgáltatásokat Azure Partner Network Security Appliance-megoldás használatával érheti el. Az Azure [Marketplace](https://azure.microsoft.com/marketplace/) -en megtekintheti a legfrissebb Azure Partner Network biztonsági megoldásokat, és megkeresheti a "biztonság" és a "hálózati biztonság" kifejezést.

### <a name="azure-virtual-network"></a>Azure Virtual Network

Az Azure virtuális hálózat (VNet) az Ön saját hálózatát jelképezi a felhőben. Ez az előfizetéshez dedikált Azure Network Fabric logikai elkülönítése. A hálózaton belül teljes mértékben irányíthatja az IP-címblokkokat, a DNS-beállításokat, a biztonsági házirendeket és az útválasztási táblázatokat. A VNet alhálózatokra is feloszthatók, és az Azure IaaS virtuális gépek (VM-EK) és/vagy [Cloud Services (Péter szerepkör példányai)](../../cloud-services/cloud-services-choose-me.md) helyezhetők üzembe az Azure Virtual Networks szolgáltatásban.

A virtuális hálózatot ezen felül a helyszíni hálózathoz is csatlakoztathatja az Azure-ban elérhető [kapcsolati lehetőségek](../../vpn-gateway/index.yml) egyikével. Lényegében kiterjesztheti a hálózatot az Azure-ra, az IP-címblokkok teljes körű irányítása és a vállalati méretű Azure által nyújtott előnyök mellett.

Az Azure Networking különböző biztonságos távelérési forgatókönyveket támogat. Ilyenek például a következők:

-   [Különálló munkaállomások összekötése egy Azure-Virtual Network](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

-   [Helyszíni hálózat összekapcsolása Azure-Virtual Network VPN-kapcsolattal](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

-   [Helyszíni hálózat csatlakoztatása Azure-Virtual Network dedikált WAN-kapcsolattal](../../expressroute/expressroute-introduction.md)

-   [Azure-beli virtuális hálózatok összekapcsolhatók egymással](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

### <a name="vpn-gateway"></a>VPN Gateway
Az Azure-Virtual Network és a helyszíni hely közötti hálózati forgalom elküldéséhez létre kell hoznia egy VPN-átjárót az Azure-Virtual Networkhoz. A [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) olyan virtuális hálózati átjáró, amely titkosított forgalmat küld nyilvános kapcsolaton keresztül. A VPN-átjárók használatával az Azure-beli virtuális hálózatok közötti adatforgalmat az Azure hálózati hálón keresztül is elküldheti.

### <a name="express-route"></a>Express Route
A Microsoft Azure [ExpressRoute](../../expressroute/expressroute-introduction.md) egy dedikált WAN-kapcsolat, amely lehetővé teszi, hogy a helyszíni hálózatait a Microsoft Cloud-ra bővítse egy olyan dedikált privát kapcsolaton keresztül, amely egy kapcsolati szolgáltató által könnyíti meg.

![Express Route](./media/overview/azure-security-fig1.png)

Az ExpressRoute használatával kapcsolatokat létesíthet a Microsoft-felhőszolgáltatásokkal, például a Microsoft Azure-ral, az Office 365-tel és a CRM Online-nal. A kapcsolatok lehetnek: bármely elemek közötti (IP VPN) hálózat, pontok közötti Ethernet-hálózat vagy egy virtuális keresztkapcsolat egy kapcsolatszolgáltatón keresztül egy közös elhelyezési létesítményben.

A ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át, így a VPN-alapú megoldásoknál biztonságosabbnak tekinthetik. Így az ExpressRoute-kapcsolatok a tipikus internetes kapcsolatoknál megbízhatóbbak, gyorsabbak, gyorsabb a válaszidejük, és biztonságosabbak.


### <a name="application-gateway"></a>Application Gateway
A Microsoft [Azure Application Gateway](../../application-gateway/overview.md) egy [alkalmazás-továbbítási vezérlőt (ADC)](https://en.wikipedia.org/wiki/Application_delivery_controller) biztosít szolgáltatásként, amely különböző 7. rétegbeli terheléselosztási funkciókat kínál az alkalmazásához.

![Application Gateway](./media/overview/azure-security-fig2.png)

Lehetővé teszi a webfarmok hatékonyságának optimalizálását azáltal, hogy kiszervezi a CPU-igényes SSL-lezárást a Application Gateway (más néven "SSL-kiszervezés" vagy "SSL-áthidaló"). Emellett további 7. rétegbeli útválasztási lehetőségeket is biztosít, beleértve a bejövő forgalom ciklikus időszeleteléses elosztását, a cookie-alapú munkamenet-affinitást, az URL-alapú útválasztást, valamint több webhely üzemeltetését egyetlen Application Gateway mögött. Az Azure Application Gateway egy 7. rétegbeli terheléselosztó.

Feladatátvételt és teljesítményalapú útválasztást biztosít a HTTP-kérelmek számára különböző kiszolgálók között, függetlenül attól, hogy a felhőben vagy a helyszínen vannak.

Az alkalmazás számos Application Delivery Controller (ADC) funkciót kínál, többek között a HTTP-terheléselosztást, a cookie-alapú munkamenet-affinitást, a [SSL (SSL)](../../application-gateway/tutorial-restrict-web-traffic-powershell.md) kiszervezését, az egyéni állapotú mintavételeket, a többhelyes és sok más támogatást.

### <a name="web-application-firewall"></a>Webalkalmazási tűzfal
A webalkalmazási tűzfal az [Azure Application Gateway](../../application-gateway/overview.md) szolgáltatása, amely védelmet nyújt az Application Gatewayt használó webalkalmazásoknak a standard Application Delivery Control (ADC) függvényekhez. A webalkalmazási tűzfal ezt úgy éri el, hogy védelmet nyújt az alkalmazásoknak az OWASP 10 leggyakoribb webes biztonsági résének többségével szemben.

![Webalkalmazási tűzfal](./media/overview/azure-security-fig1.png)

-   SQL-injektálás elleni védelem

-   Gyakori webes támadások (például parancsinjektálás, HTTP-kéréscsempészet, HTTP-válaszfelosztás és távolifájl-beszúrásos támadás) elleni védelem

-   HTTP protokoll megsértése elleni védelem

-   HTTP protokollanomáliák (például hiányzó gazdagép-felhasználói ügynök és Accept (Elfogadás) fejlécek) elleni védelem

-   Robotprogramok, webbejárók és képolvasók elleni védelem

-   Az alkalmazások gyakori konfigurációs beállításainak észlelése (vagyis Apache, IIS stb.)


A webes támadásokkal szembeni védelmet nyújtó központi webalkalmazási tűzfal egyszerűbbé teszi a biztonságfelügyeletet, és nagyobb biztonságot ad az alkalmazásnak a behatolások jelentette veszéllyel szemben. Emellett a WAF-megoldás gyorsabban képes kezelni a biztonsági fenyegetéseket azáltal, hogy kijavítja az ismert biztonsági réseket egy központi helyen, ahelyett hogy az egyes webalkalmazások védelmét biztosítaná. A meglévő alkalmazásátjárókat egyszerűen lehet átalakítani webalkalmazási tűzfallal rendelkező alkalmazásátjárókká.
### <a name="traffic-manager"></a>Traffic Manager
A Microsoft [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) segítségével szabályozható a különböző adatközpontokban lévő szolgáltatási végpontok felhasználói forgalmának elosztása. A Traffic Manager által támogatott szolgáltatási végpontok közé tartoznak az Azure-beli virtuális gépek, a Web Apps és a Cloud Services. A Traffic Manager külső, nem Azure-végpontokkal együtt is használható. Traffic Manager a tartománynévrendszer (DNS) használatával irányítja az ügyfelek kérelmeit a legmegfelelőbb végpontra a [forgalmi útválasztási módszer](../../traffic-manager/traffic-manager-routing-methods.md) és a végpontok állapota alapján.

Traffic Manager számos forgalom-útválasztási módszert kínál a különböző alkalmazási igények kielégítésére, a végpont [](../../traffic-manager/traffic-manager-monitoring.md)állapotának figyelésére és az automatikus feladatátvételre. A Traffic Manager ellenáll a meghibásodásoknak, beleértve akár egy egész Azure-régió meghibásodását is.
### <a name="azure-load-balancer"></a>Azure Load Balancer
Az [Azure Load Balancer](../../load-balancer/load-balancer-overview.md) magas rendelkezésre állást és hálózati teljesítményt biztosít alkalmazásai számára. Ez egy 4. rétegbeli (TCP, UDP) terheléselosztó, amely a bejövő forgalmat egy elosztott terhelésű készletben definiált szolgáltatások kifogástalan példányai között osztja szét. A Azure Load Balancer a következőre konfigurálható:

-   A bejövő internetes forgalom terheléselosztása a virtuális gépekre. Ezt a konfigurációt internetre irányuló [terheléselosztásnak](../../load-balancer/load-balancer-overview.md#publicloadbalancer)nevezzük.

-   A virtuális hálózatban lévő virtuális gépek, a Cloud Services-beli virtuális gépek, illetve a helyszíni számítógépek és a virtuális gépek közötti adatforgalom terheléselosztása egy telephelyi virtuális hálózaton. Ezt a konfigurációt [belső terheléselosztásnak](../../load-balancer/load-balancer-overview.md#internalloadbalancer)nevezzük. 

- Külső forgalom továbbítása egy adott virtuális géphez

### <a name="internal-dns"></a>Belső DNS
A VNet használt DNS-kiszolgálók listáját a felügyeleti portál vagy a hálózati konfigurációs fájlban kezelheti. Az ügyfél akár 12 DNS-kiszolgálót is hozzáadhat az egyes VNet. A DNS-kiszolgálók megadásakor fontos ellenőrizni, hogy az ügyfél DNS-kiszolgálóit az ügyfél környezetének megfelelő sorrendben sorolja-e fel. A DNS-kiszolgálók listája nem működik ciklikus multiplexelés esetén. Ezeket a rendszer a megadott sorrendben használja. Ha a lista első DNS-kiszolgálója képes elérni, az ügyfél ezt a DNS-kiszolgálót használja, függetlenül attól, hogy a DNS-kiszolgáló megfelelően működik-e. Ha módosítani szeretné az ügyfél virtuális hálózatához tartozó DNS-kiszolgáló sorrendjét, távolítsa el a DNS-kiszolgálókat a listából, és adja őket vissza az ügyfél által kívánt sorrendben. A DNS a "CIA" biztonsági hármas rendelkezésre állási aspektusát támogatja.

### <a name="azure-dns"></a>Azure DNS
A [tartománynévrendszer](https://technet.microsoft.com/library/bb629410.aspx)vagy a DNS a webhely vagy szolgáltatás nevének az IP-címére való fordítására (vagy feloldására) felelős. [Azure DNS](../../dns/dns-overview.md) a DNS-tartományok üzemeltetési szolgáltatása, amely a névfeloldást Microsoft Azure infrastruktúra használatával biztosítja. Ha tartományait az Azure-ban üzemelteti, DNS-rekordjait a többi Azure-szolgáltatáshoz is használt hitelesítő adatokkal, API-kkal, eszközökkel és számlázási információkkal kezelheti. A DNS a "CIA" biztonsági hármas rendelkezésre állási aspektusát támogatja.
### <a name="azure-monitor-logs-nsgs"></a>Azure Monitor naplók NSG
A következő diagnosztikai naplózási kategóriákat engedélyezheti a NSG:
-   Esemény Azokat a bejegyzéseket tartalmazza, amelyek NSG-szabályait a rendszer MAC-címen alapuló virtuális gépekre és példányokra alkalmazza. A szabályok állapotát 60 másodpercenként gyűjti a rendszer.

-   Szabályok számlálója: Azokat a bejegyzéseket tartalmazza, amelyekkel az egyes NSG-szabályok a forgalom megtagadására vagy engedélyezésére vonatkoznak.

### <a name="azure-security-center"></a>Azure Security Center
A Security Center segít megakadályozni, észlelni és reagálni a fenyegetésekre, és az Azure-erőforrások jobb láthatóságát és felügyeletét teszi lehetővé. Integrált biztonsági monitorozást és házirend-kezelést biztosít az Azure-előfizetésekben, segít észlelni azokat a fenyegetéseket, amelyek egyébként észrevétlenek lehetnek, és a biztonsági megoldások széles körű ökoszisztémával működnek. Hálózati javaslatok központ tűzfalakon, hálózati biztonsági csoportokon, a bejövő forgalmi szabályok konfigurálásán és egyebeken.

Az elérhető hálózati javaslatok a következők:

-   Új [generációs tűzfal hozzáadása](../../security-center/security-center-add-next-generation-firewall.md) Javasolja, hogy vegyen fel egy új generációs tűzfalat (NGFW) egy Microsoft-partnertől a biztonsági védelem növeléséhez

-   [Forgalom irányítása csak NGFW keresztül](../../security-center/security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only) Javasolja, hogy konfigurálja a hálózati biztonsági csoport (NSG) azon szabályait, amelyek a NGFW keresztül a virtuális gépre irányuló bejövő forgalmat kényszerítik.

-   [Hálózati biztonsági csoportok engedélyezése az alhálózatokon vagy virtuális gépeken](../../security-center/security-center-enable-network-security-groups.md) Javasolja, hogy engedélyezze a NSG az alhálózatokon vagy virtuális gépeken.

-   [Internet felé irányuló végponton keresztüli hozzáférés korlátozása](../../security-center/security-center-restrict-access-through-internet-facing-endpoints.md) Javasolja, hogy konfigurálja a NSG bejövő forgalmi szabályait.


## <a name="compute"></a>Compute

A szakasz további információkat tartalmaz ezen terület főbb funkcióiról, valamint az ezekkel a képességekkel kapcsolatos összegző információkkal kapcsolatban.

### <a name="antimalware--antivirus"></a>Antimalware & víruskereső
Az Azure IaaS olyan biztonsági gyártóktól származó kártevő szoftvereket használhat, mint például a Microsoft, a Symantec, a Trend Micro, a McAfee és a Kaspersky a virtuális gépek kártékony fájlokkal, adware-mel és más fenyegetésekkel szembeni védelméhez. A [Microsoft antimalware](antimalware.md) for Azure Cloud Services és Virtual Machines egy védelmi képesség, amely segít azonosítani és eltávolítani a vírusokat, kémprogramokat és egyéb kártevő szoftvereket. A Microsoft antimalware konfigurálható riasztásokat biztosít, amikor az ismert kártékony vagy nemkívánatos szoftverek megkísérlik telepíteni vagy futtatni magukat az Azure-rendszereken. A Microsoft antimalware szolgáltatás a Azure Security Center használatával is telepíthető

### <a name="hardware-security-module"></a>Hardveres biztonsági modul
A titkosítás és a hitelesítés nem javítja a biztonságot, kivéve, ha maguk a kulcsok védve vannak. A kritikus fontosságú titkok és kulcsok felügyeletét és biztonságát leegyszerűsítheti, ha [Azure Key Vault](../../key-vault/key-vault-whatis.md)tárolja őket. A Key Vault lehetővé teszi, hogy a kulcsokat hardveres biztonsági modulokban (HSM) tárolja, amely a FIPS 140-2 2. szintű szabványokhoz van hitelesítve. A biztonsági mentéshez vagy [transzparens](https://msdn.microsoft.com/library/bb934049.aspx) adattitkosításhoz használt SQL Server titkosítási kulcsainak mindegyike Key Vault tárolható az alkalmazásokból származó kulcsokkal vagy titkos kulcsok használatával. Az engedélyeket és a védett elemek elérését [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)kezelik.

### <a name="virtual-machine-backup"></a>Virtuális gép biztonsági mentése
[Azure Backup](../../backup/backup-overview.md) egy olyan megoldás, amely az alkalmazás adatait nulla tőkebefektetéssel és minimális működési költségekkel védi. Az alkalmazások hibái sérültek az adataikat, és az emberi hibák olyan hibákat is bevezethetnek az alkalmazásokba, amelyek biztonsági problémákat okozhatnak. A Azure Backup a Windows és a Linux rendszerű virtuális gépek védettek.

### <a name="azure-site-recovery"></a>Azure Site Recovery
A szervezete [üzletmenet-folytonossági/vész-helyreállítási (BCDR)](../../best-practices-availability-paired-regions.md) stratégiájának fontos része, hogy a vállalati számítási feladatok és alkalmazások a tervezett és nem tervezett leállások esetén is naprakészek maradjanak. [Azure site Recovery](../../site-recovery/site-recovery-overview.md) segíti a munkaterhelések és alkalmazások replikálásának, feladatátvételének és helyreállításának összehangolását, hogy azok másodlagos helyről is elérhetők legyenek, ha az elsődleges hely leáll.

### <a name="sql-vm-tde"></a>SQL VIRTUÁLIS GÉP TDE
A [transzparens adattitkosítás (TDE)](../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md) és az oszlop szintű TITKOSÍTÁS (CLE) az SQL Server titkosítási funkciói. A titkosítás ezen formája megköveteli, hogy az ügyfelek kezeljék és tárolják a titkosításhoz használt titkosítási kulcsokat.

A Azure Key Vault (AKV) szolgáltatás úgy lett kialakítva, hogy javítsa a kulcsok biztonságát és felügyeletét egy biztonságos és magasan elérhető helyen. A SQL Server Connector lehetővé teszi, hogy a SQL Server ezeket a kulcsokat a Azure Key Vault használatával használhassa.

Ha a SQL Servert a helyszíni gépekkel futtatja, akkor a helyszíni SQL Server számítógépéről elérhető lépéseket követve elérheti a Azure Key Vault. Az Azure-beli virtuális gépek SQL Server azonban időt takaríthat meg a Azure Key Vault integrációs funkciójával. Néhány Azure PowerShell parancsmaggal engedélyezheti ezt a funkciót, automatizálhatja az SQL-alapú virtuális gépekhez szükséges konfigurációt a kulcstartó eléréséhez.

### <a name="vm-disk-encryption"></a>VM-lemez titkosítása
A [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) egy új képesség, amely segít a Windows-és Linux-IaaS virtuális gépek lemezeinek titkosításában. Ez a Windows iparági szabványnak megfelelő BitLocker-szolgáltatásra és a Linux DM-Crypt szolgáltatására vonatkozik, hogy mennyiségi titkosítást biztosítson az operációs rendszer és az adatlemezek számára. A megoldás integrálva van Azure Key Vault a Key Vault-előfizetésében lévő lemezes titkosítási kulcsok és titkos kódok felügyeletéhez és kezeléséhez. A megoldás azt is biztosítja, hogy a virtuális gépek lemezein lévő összes adatok titkosítva legyenek az Azure Storage-ban.

### <a name="virtual-networking"></a>Virtuális hálózat
A virtuális gépeknek hálózati kapcsolatra van szükségük. Ezen követelmény támogatásához az Azure megköveteli, hogy a virtuális gépek egy Azure-Virtual Network kapcsolódjanak. Az Azure Virtual Network egy logikai szerkezet, amely a fizikai Azure hálózati hálóra épül. Minden egyes logikai [Azure-Virtual Network](../../virtual-network/virtual-networks-overview.md) el van különítve az összes többi Azure-beli virtuális hálózattól. Ez az elkülönítés segít biztosítani, hogy a központi telepítések hálózati forgalma ne legyen elérhető más Microsoft Azure ügyfelek számára.

### <a name="patch-updates"></a>Javítások frissítései
A javítások frissítései lehetővé teszik a lehetséges problémák megkeresését és kijavítását, valamint a szoftverfrissítés-felügyeleti folyamat egyszerűsítését, a vállalaton belül üzembe helyezni kívánt szoftverfrissítések számának csökkentésével, valamint a megfelelőség monitorozására való képesség növelésével.

### <a name="security-policy-management-and-reporting"></a>Biztonsági házirendek kezelése és jelentéskészítés
A [Azure Security Center](../../security-center/security-center-intro.md) segít megakadályozni, észlelni és reagálni a fenyegetésekre, és az Azure-erőforrások jobb láthatóságát és felügyeletét teszi lehetővé. Integrált biztonsági monitorozást és házirend-kezelést biztosít az Azure-előfizetésekben, segít észlelni azokat a fenyegetéseket, amelyek egyébként észrevétlenek lehetnek, és a biztonsági megoldások széles körű ökoszisztémával működnek.

### <a name="azure-security-center"></a>Azure Security Center
A Security Center az Azure-erőforrások biztonsági felügyeletének átláthatóbbá és szabályozhatóbbá tételével megkönnyíti a fenyegetések megelőzését, észlelését és elhárítását. Az ügyfél összes előfizetésére kiterjedő, integrált biztonsági monitorozást és szabályzatkezelést biztosít, megkönnyíti a nehezen észlelhető fenyegetések azonosítását, és számos biztonsági megoldással együttműködik.

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

A rendszerek, alkalmazások és adatok védelme az identitás-alapú hozzáférés-vezérléssel kezdődik. A Microsoft üzleti termékeibe és szolgáltatásaiba beépített identitás-és hozzáférés-kezelési funkciók segítenek a szervezeti és személyes adatok jogosulatlan hozzáférés elleni védelmében, miközben a legitim felhasználók számára, bárhol és bármikor elérhetővé teszik azokat szükség van rá.

### <a name="secure-identity"></a>Biztonságos identitás
A Microsoft több biztonsági gyakorlatot és technológiát használ termékei és szolgáltatásai között az identitás és a hozzáférés kezeléséhez.
-   [](https://azure.microsoft.com/services/multi-factor-authentication/) A többtényezős hitelesítéshez a felhasználóknak több módszert kell használniuk a helyszíni és a Felhőbeli hozzáféréshez. Erős hitelesítést biztosít számos egyszerű ellenőrzési lehetőséggel, miközben a felhasználók számára egyszerű bejelentkezési folyamattal rendelkezik.

-   A [Microsoft Authenticator](https://aka.ms/authenticator) egy felhasználóbarát, többtényezős hitelesítési élményt nyújt, amely Microsoft Azure Active Directory és Microsoft-fiókkal is működik, és támogatja a hordozható és ujjlenyomat-alapú jóváhagyásokat.

-   A [jelszóházirend kényszerítése](https://azure.microsoft.com/documentation/articles/active-directory-passwords-policy/) növeli a hagyományos jelszavak biztonságát azáltal, hogy a sikertelen hitelesítési kísérleteket követően hosszúsági és összetettségi követelmények, kényszerített időszakos rotáció és fiókzárolás miatt megterheli a fiók zárolását.

-   A [jogkivonat-alapú hitelesítés](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/) lehetővé teszi a hitelesítés Azure Active Directory használatával.

-   A [szerepköralapú hozzáférés-vezérlés (RBAC)](https://azure.microsoft.com/documentation/articles/role-based-access-built-in-roles/) lehetővé teszi a hozzáférés megadását a felhasználó hozzárendelt szerepköre alapján, így a felhasználók számára csak a feladat feladatainak elvégzéséhez szükséges hozzáférés mennyiségét biztosíthatja. A RBAC személyre szabása a szervezet üzleti modelljében és a kockázati toleranciában végezhető el.

-   Az [integrált Identitáskezelés (hibrid identitás)](https://azure.microsoft.com/documentation/articles/active-directory-hybrid-identity-design-considerations-overview/) lehetővé teszi a felhasználók hozzáférésének felügyeletét a belső adatközpontokban és a Felhőbeli platformokon, így egyetlen felhasználói identitást hozhat létre a hitelesítéshez és az összes erőforrás engedélyezéséhez.

### <a name="secure-apps-and-data"></a>Biztonságos alkalmazások és adatkezelés
A [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)egy átfogó identitás-és hozzáférés-kezelési felhőalapú megoldás, amely segít a helyszíni és Felhőbeli alkalmazásokban tárolt adatokhoz való biztonságos hozzáférésben, és leegyszerűsíti a felhasználók és csoportok felügyeletét. Az alapszintű címtárszolgáltatás, a fejlett identitás-szabályozás, a biztonság és az alkalmazás-hozzáférés kezelése révén megkönnyíti a fejlesztők számára a házirend-alapú Identitáskezelés összeállítását az alkalmazásokba. Az Azure Active Directoryt kiegészítheti fizetős képességekkel is, az Azure Active Directory Alapszintű, Prémium P1 és Prémium P2 kiadásával.

| Ingyenes/közös funkciók     | Alapszintű szolgáltatások    |Prémium P1 funkciók |Prémium P2 funkciók | Azure Active Directory JOIN – csak a Windows 10 kapcsolódó funkciói|
| :------------- | :------------- |:------------- |:------------- |:------------- |
|   [Directory-objektumok](../../active-directory/active-directory-whatis.md), [felhasználók/csoportok kezelése (Hozzáadás/frissítés/törlés)/felhasználó-alapú kiépítés, eszköz regisztrálása](../../active-directory/active-directory-whatis.md), [egyszeri bejelentkezés (SSO)](../../active-directory/active-directory-whatis.md), [a felhőalapú felhasználók](../../active-directory/active-directory-whatis.md)önkiszolgáló jelszavas változása, [Csatlakoztatás (szinkronizálási motor, amely kiterjeszti a helyszíni címtárakat Azure Active Directory)](../../active-directory/active-directory-whatis.md), a [biztonsági/használati jelentéseket](../../active-directory/active-directory-whatis.md)       |   [Csoportos hozzáférés-kezelés/](../../active-directory/active-directory-whatis.md)kiépítés, [önkiszolgáló jelszó-visszaállítás a Felhőbeli felhasználók](../../active-directory/active-directory-whatis.md)számára, [vállalati védjegyezés (bejelentkezési lapok/hozzáférési panel testreszabása)](../../active-directory/active-directory-whatis.md), [alkalmazásproxy](../../active-directory/active-directory-whatis.md), [SLA 99,9%](../../active-directory/active-directory-whatis.md) |  Önkiszolgáló [csoport és alkalmazás-kezelés/önkiszolgáló alkalmazás hozzáadása/dinamikus csoportok](../../active-directory/active-directory-whatis.md), önkiszolgáló [jelszó-visszaállítás/módosítás/feloldás](../../active-directory/active-directory-whatis.md)helyszíni visszaírással, többtényezős [hitelesítéssel (Felhőbeli és helyszíni (MFA-kiszolgáló) )](../../active-directory/active-directory-whatis.md), [](../../active-directory/active-directory-whatis.md)A ( [Cloud app Discovery](../../active-directory/active-directory-whatis.md), a [kapcsolat állapota](../../active-directory/active-directory-whatis.md), [az automatikus jelszó-átgörgetés a csoportfiókok esetében](../../active-directory/active-directory-whatis.md) )|    [Identity Protection](../../active-directory/identity-protection/overview.md), [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)|   [Eszköz csatlakoztatása az Azure ad-hez, asztali SSO, Microsoft Passport az Azure ad-hez, rendszergazdai BitLocker-helyreállítás](../../active-directory/active-directory-whatis.md), [Mdm automatikus regisztráció, önkiszolgáló BitLocker-helyreállítás, további helyi rendszergazdák a Windows 10-es eszközökhöz az Azure ad JOIN használatával](../../active-directory/active-directory-whatis.md)|


- A [Cloud app Discovery](../../active-directory/cloudappdiscovery-get-started.md) a Azure Active Directory prémium funkciója, amely lehetővé teszi a szervezet alkalmazottai által használt felhőalapú alkalmazások azonosítását.

- [Azure Active Directory Identity Protection](https://azure.microsoft.com/documentation/articles/active-directory-identityprotection/) olyan biztonsági szolgáltatás, amely Azure Active Directory anomália-észlelési képességeket használ a kockázati események és a szervezete által érintett potenciális sebezhetőségek összevont nézetének biztosításához identitások.

- [Azure Active Directory Domain Services](https://azure.microsoft.com/services/active-directory-ds/) lehetővé teszi az Azure-beli virtuális gépek tartományhoz való csatlakoztatását anélkül, hogy tartományvezérlőket kellene üzembe helyeznie. A felhasználók vállalati Active Directory hitelesítő adataikkal jelentkezhetnek be ezekre a virtuális gépekre, és zökkenőmentesen érhetik el az erőforrásokat.

- A [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) egy olyan, a felhasználók felé irányuló alkalmazások számára elérhető, globális Identitáskezelés, amely több száz millió identitásra és a mobil-és webes platformok integrálására képes. Ügyfelei az összes alkalmazásba bejelentkezhetnek a meglévő közösségi média-fiókokat használó testreszabható tapasztalatokkal, vagy új, önálló hitelesítő adatokat is létrehozhatnak.

- [Azure Active Directory B2B-együttműködés](https://aka.ms/aad-b2b-collaboration) egy biztonságos partner-integrációs megoldás, amely támogatja a több vállalatra kiterjedő kapcsolatokat azáltal, hogy lehetővé teszi a partnerek számára a vállalati alkalmazások és az adat szelektív elérését az önállóan felügyelt identitások használatával. .

- A [Azure Active Directory JOIN](https://azure.microsoft.com/documentation/articles/active-directory-azureadjoin-overview/) lehetővé teszi a Felhőbeli funkciók kiterjesztését a Windows 10-es eszközökre a központosított felügyelethez. Lehetővé teszi a felhasználók számára, hogy Azure Active Directoryon keresztül csatlakozzanak a vállalati vagy szervezeti felhőhöz, és leegyszerűsítse az alkalmazásokhoz és erőforrásokhoz való hozzáférést.

- A [Azure Active Directory Application proxy](https://azure.microsoft.com/documentation/articles/active-directory-application-proxy-get-started/) egyszeri bejelentkezést és biztonságos távoli hozzáférést biztosít a helyszínen üzemeltetett webalkalmazásokhoz.

## <a name="next-steps"></a>További lépések
- [Microsoft Azure biztonság – első lépések](./https://docs.microsoft.com/azure/security)

Azure-beli adatai és szolgáltatásai számára védelmet nyújtó Azure-szolgáltatások és funkciók

- [Azure Security Center](https://azure.microsoft.com/services/security-center/)

Fenyegetések megelőzése, észlelése és elhárítása az Azure-erőforrások jobb láthatóságával és kézben tartásával

- [Biztonsági állapot monitorozása az Azure Security Centerben](../../security-center/security-center-monitoring.md)

A Azure Security Center figyelési képességei a szabályzatoknak való megfelelés figyelésére.

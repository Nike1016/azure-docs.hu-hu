---
title: Alkalmazások áthelyezése BizTalk Servicesról Azure Logic Appsra | Microsoft Docs
description: Migrálás az Azure BizTalk Services (MABS) szolgáltatásból a Azure Logic Appsba
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: jonfancey
ms.author: jonfan
ms.reviewer: estfan, LADocs
ms.topic: article
ms.date: 05/30/2017
ms.openlocfilehash: dfc0aa4fa7c70ae91f25f97671b15dacfe991594
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68273185"
---
# <a name="migrate-from-biztalk-services-to-azure-logic-apps"></a>Áttelepítés BizTalk Servicesról Azure Logic Apps

Microsoft Azure BizTalk Services (MABS) kivonása. A MABS-integrációs megoldások [Azure Logic Appsba](../logic-apps/logic-apps-overview.md)való áthelyezéséhez kövesse az ebben a cikkben található útmutatást. 

## <a name="introduction"></a>Bevezetés

BizTalk Services két alszolgáltatásból áll:

* Microsoft BizTalk Services Hibrid kapcsolatok
* EAI és EDI-híd alapú integráció

[Azure App Service hibrid kapcsolatok](../app-service/app-service-hybrid-connections.md) lecseréli BizTalk Services hibrid kapcsolatok. Az Azure Hibrid kapcsolatok Azure App Service a Azure Portalon keresztül érhető el. Ez a szolgáltatás egy hibridkapcsolat-kezelő biztosít, így kezelheti a meglévő BizTalk Services hibrid kapcsolatokat és a portálon létrehozott új hibrid kapcsolatokat is. 

[Logic apps](../logic-apps/logic-apps-overview.md) lecseréli a EAI és az EDI híd-alapú integrációt a BizTalk Services és más funkciókkal megegyező képességekkel. Ez a szolgáltatás felhőalapú, fogyasztáson alapuló munkafolyamatokat és-előkészítési funkciókat biztosít, amelyekkel gyorsan és egyszerűen hozhat létre összetett integrációs megoldásokat böngészőn vagy a Visual studión keresztül.

Ez a táblázat a Logic Apps BizTalk Services képességeit képezi le.

| BizTalk Services   | Logic Apps            | Cél                      |
| ------------------ | --------------------- | ---------------------------- |
| Összekötő          | Összekötő             | Az adatküldés és fogadás   |
| Bridge             | Logikai alkalmazás             | Folyamat processzora           |
| Ellenőrzés fázisa     | XML-érvényesítési művelet | XML-dokumentum ellenőrzése sémán keresztül | 
| Dúsítási szakasz       | Adattokenek           | Tulajdonságok előléptetése üzenetekre vagy útválasztási döntések |
| Átalakítási fázis    | Átalakítási művelet      | XML-üzenetek konvertálása egyik formátumból a másikba |
| Dekódolási fázis       | Az egyszerű fájl dekódolási művelete | Konvertálás lapos fájlról XML-re |
| A fázis kódolása       | Egyszerű fájl kódolása művelet | Konvertálás XML-ről lapos fájllá |
| Üzenet ellenőre  | Azure Functions vagy API Apps | Egyéni kód futtatása az integrációkban |
| Útvonal művelete       | Feltétel vagy kapcsoló | Üzenetek továbbítása a megadott összekötők egyikéhez |
|||| 

## <a name="biztalk-services-artifacts"></a>BizTalk Services összetevők

BizTalk Services több fajta összetevővel rendelkezik. 

## <a name="connectors"></a>Összekötők

A BizTalk Services-összekötők segítenek az adatküldésben és a fogadásban, beleértve a kétirányú hidakat is, amelyek lehetővé teszik a HTTP-alapú kérelmek/válaszok interakcióját. Logic Apps ugyanazt a terminológiát használja, és több száz összekötőt használ, amelyek ugyanazt a célt szolgálják, és számos különböző technológiához és szolgáltatáshoz csatlakoznak. Az összekötők például elérhetők a Cloud SaaS-és a Pásti-szolgáltatásokhoz, például a OneDrive, a Office 365, a Dynamics CRM és egyebekhez, valamint a helyszíni rendszerekhez a helyszíni adatátjárón keresztül, amely felváltja a BizTalk adapter szolgáltatást a BizTalk Services számára. A BizTalk Services lévő források FTP, SFTP és Service Bus üzenetsor vagy témakör-előfizetésre korlátozódnak.

![](media/logic-apps-move-from-mabs/sources.png)

Alapértelmezés szerint minden egyes híd egy HTTP-végponttal rendelkezik, amely a futásidejű cím és a híd relatív cím tulajdonságai szerint van konfigurálva. Ha ugyanazokat az eredményeket szeretné elérni Logic Appsekkel, használja a [kérelmek és válaszok](../connectors/connectors-native-reqres.md) műveletet.

## <a name="xml-processing-and-bridges"></a>XML-feldolgozás és-hidak

BizTalk Services a hidak egy feldolgozási folyamathoz hasonlítanak. Egy híd elvégezheti az összekötőtől érkező adatok fogadását, elvégezheti a munkát az adatokkal, és elküldheti az eredményeket egy másik rendszernek. A Logic Apps ugyanazokat a folyamatokon alapuló interakciós mintákat támogatja, mint BizTalk Services, és egyéb integrációs mintákat is biztosít. Az [XML-kérelem – válasz-híd](https://msdn.microsoft.com/library/azure/hh689781.aspx) BIZTALK Services néven VETER folyamat, amely a következő feladatokat hajtja végre:

* (V) ellenőrzés
* (E) dúsítás
* (T) átalakító
* (E) dúsítás
* (R) útvonal

Ez a rendszerkép azt mutatja be, hogyan oszlik meg a feldolgozás a kérelem és válasz között, amely a kérés és a válasz elérési útjai külön-külön, például az egyes elérési utak különböző leképezéseit használja:

![](media/logic-apps-move-from-mabs/xml-request-reply.png)

Emellett az egyirányú XML-híd dekódolást és kódolási fázisokat is felhasznál a feldolgozás elején és végén. Az áteresztő híd egyetlen dúsítási szakaszt tartalmaz.

### <a name="message-processing-decoding-and-encoding"></a>Üzenetek feldolgozása, dekódolása és kódolása

BizTalk Services különböző típusú XML-üzeneteket fogadhat, és meghatározhatja a kapott üzenet megfelelő sémáját. Ezt a munkát a fogadási feldolgozási folyamat *üzenet típusai* szakaszban végezheti el. A dekódolási fázis ezután az észlelt üzenet típusát használja az üzenet dekódolásához a megadott sémával. Ha a séma egy egyszerű fájl sémája, akkor ez a fázis átalakítja a bejövő sima fájlt XML-fájlba. 

A Logic Apps hasonló képességeket biztosít. A rendszer a különböző protokollokon keresztül egy egyszerű fájlt kap a különböző összekötő-eseményindítók (fájlrendszer, FTP, HTTP stb.) használatával, és [](../logic-apps/logic-apps-enterprise-integration-flatfile.md) a lemezes dekódolási művelettel átalakítja a beérkező adatfájlokat az XML formátumba. Áthelyezheti a meglévő strukturált fájl-sémákat közvetlenül Logic Apps módosítás nélkül, majd az integrációs fiókjába is feltölthet sémákat.

### <a name="validation"></a>Ellenőrzés

A bejövő adatfájlok XML-re konvertálása után (vagy ha XML volt az üzenet formátuma), az érvényesítés futtatásával megállapíthatja, hogy az üzenet megfelel-e az XSD-sémának. A feladat Logic Appsban történő végrehajtásához használja az [XML-érvényesítési](../logic-apps/logic-apps-enterprise-integration-xml-validation.md) műveletet. A BizTalk Services a módosítások nélkül is használhatja ugyanezeket a sémákat.

### <a name="transform-messages"></a>Üzenetek átalakítása

BizTalk Services az átalakítási fázis egy XML-alapú üzenet formátumát átalakítja egy másikra. Ez a munka egy Térkép alkalmazásával hajtható végre a TRFM-alapú Mapper használatával. Logic Apps a folyamat hasonló. Az átalakítási művelet egy térképet hajt végre az integrációs fiókból. A fő különbség az, hogy az Logic Apps lévő térképek XSLT formátumban vannak. Az XSLT lehetővé teszi a már meglévő XSLT újrafelhasználását, beleértve a functoids tartalmazó BizTalk Serverhoz létrehozott térképeket is. 

### <a name="routing-rules"></a>Útválasztási szabályok

BizTalk Services a bejövő üzenetek vagy az adatküldés továbbítására szolgáló végpont vagy összekötő útválasztási döntését teszi lehetővé. Az előre konfigurált végpontokból kiválasztható lehetőség az útválasztási szűrő beállítás használatával:

![](media/logic-apps-move-from-mabs/route-filter.png)

BizTalk Services esetén, ha csak két lehetőség van, egy feltételt használva a legjobb módszer az útválasztási szűrők BizTalk Servicesban történő átalakítására. ** Ha kettőnél több van, akkor használjon egy **kapcsolót**.

A Logic Apps kifinomult logikai képességeket, valamint speciális vezérlési folyamatot és útválasztást biztosít [feltételes utasításokkal](../logic-apps/logic-apps-control-flow-conditional-statement.md) és [switch utasításokkal](../logic-apps/logic-apps-control-flow-switch-statement.md).

### <a name="enrich"></a>Bővítését

BizTalk Services feldolgozás során a dúsítási szakasz tulajdonságokat hoz létre a kapott adatmennyiséghez társított üzenet környezetében. Például egy adatbázis-kereséssel való útválasztáshoz használandó tulajdonság előléptetése, vagy egy érték kibontása egy XPath-kifejezés használatával. A Logic Apps hozzáférést biztosít az előző műveletek összes környezetfüggő adatkimenetéhez, így egyszerűvé válik az azonos viselkedés replikálása. Ha például az `Get Row` SQL-kapcsolat műveletet használja, egy SQL Server adatbázisból ad vissza egy adatforrást, és az útválasztásra vonatkozó döntési műveletben szereplő adatok használatával. Hasonlóképpen, a beérkező Service Bus várólistán lévő üzenetek egy megcímezhető, valamint az XPath-munkafolyamat-definíció nyelvét használó XPath-tulajdonságok.

### <a name="run-custom-code"></a>Egyéni kód futtatása

BizTalk Services lehetővé teszi a saját szerelvényekben feltöltött [egyéni kódok futtatását](https://msdn.microsoft.com/library/azure/dn232389.aspx) . Ezt a funkciót a [IMessageInspector](https://msdn.microsoft.com/library/microsoft.biztalk.services.imessageinspector) felület valósítja meg. A híd minden szakasza két tulajdonságot tartalmaz (a beléptetési Ellenőrnél és a kilépési Ellenőrnél), amelyek biztosítják az ezt a felületet megvalósító .NET-típust. Az egyéni kód lehetővé teszi, hogy összetettebb adatfeldolgozást végezzen az adatokon, és lehetővé teszi a meglévő kódok újrafelhasználását olyan szerelvényekben, amelyek közös üzleti logikát végeznek. 

A Logic Apps két elsődleges módszert biztosít egyéni kódok végrehajtásához: Azure Functions és API Apps. Azure Functions létrehozhatók és meghívhatók a Logic apps szolgáltatásban. Lásd: [egyéni kód hozzáadása és futtatása logikai alkalmazásokhoz Azure Functionson keresztül](../logic-apps/logic-apps-azure-functions.md). A saját eseményindítók és műveletek létrehozásához használja a API Appst, Azure App Service egy részét. További információ a [Logic Appshoz használandó egyéni API létrehozásáról](../logic-apps/logic-apps-create-api-app.md). 

Ha olyan szerelvényekben rendelkezik egyéni kóddal, amelyeket a BizTalk Services hív meg, áthelyezheti a kódot Azure Functionsre, vagy létrehozhat egyéni API-kat API Apps használatával, attól függően, hogy mit implementál. Ha például olyan kódot használ, amely egy másik szolgáltatást is tartalmaz, amelyhez Logic Apps nem rendelkezik összekötővel, hozzon létre egy API-alkalmazást, és használja az API-alkalmazás által a logikai alkalmazásban elérhető műveleteket. Ha segítő függvények vagy kódtárak vannak, akkor Azure Functions valószínűleg a legmegfelelőbb.

### <a name="edi-processing-and-trading-partner-management"></a>EDI-feldolgozási és kereskedelmi partneri felügyelet

BizTalk Services és Logic Apps az AS2 (Applicability Statement 2), a X12 és a EDIFACT támogatását támogató EDI-és B2B-feldolgozást is tartalmaz. BizTalk Services az EDI-hidak létrehozása, valamint kereskedelmi partnerek és szerződések létrehozása vagy kezelése a dedikált követési és felügyeleti portálon.
Logic Apps a funkciót a [Enterprise Integration Pack (EIP)](../logic-apps/logic-apps-enterprise-integration-overview.md)segítségével érheti el. A EIP [integrációs fiókot](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) és B2B-műveleteket biztosít az EDI és a B2B feldolgozáshoz. A [kereskedelmi partnerek](../logic-apps/logic-apps-enterprise-integration-partners.md) és [szerződések](../logic-apps/logic-apps-enterprise-integration-agreements.md)létrehozásához és kezeléséhez integrációs fiókot is használhat. Az integrációs fiók létrehozása után egy vagy több logikai alkalmazást is csatolhat a fiókhoz. Ezután B2B-műveletek használatával érheti el a kereskedelmi partner adatait a logikai alkalmazásból. A következő műveleteket kell megadnia:

* AS2-kódolás
* AS2-dekódolás
* X12 kódolása
* X12 dekódolása
* EDIFACT kódolása
* EDIFACT dekódolása

A BizTalk Servicestól eltérően ezek a műveletek az átviteli protokolloktól eltérnek. Tehát a logikai alkalmazások létrehozásakor nagyobb rugalmasságot biztosít az adatküldésre és-fogadásra használt összekötők számára. Például fogadhat X12-fájlokat mellékletként e-mailben, majd feldolgozhatja ezeket a fájlokat egy logikai alkalmazásban. 

## <a name="manage-and-monitor"></a>Kezelés és monitorozás

BizTalk Services egy dedikált portálon nyomon követheti a problémák figyelésére és elhárítására szolgáló funkciókat. A Logic Apps a Azure Portalon keresztül gazdagabb nyomon követési [](../logic-apps/logic-apps-monitor-your-logic-apps.md)és figyelési képességeket biztosít, és tartalmaz egy mobil alkalmazást, amely figyelemmel kíséri az áthelyezés során felhasználható dolgokat.

## <a name="high-availability"></a>Magas rendelkezésre állás

A BizTalk Services magas rendelkezésre állása esetén a feldolgozási terhelést egy adott régió több példányának használatával is megoszthatja. A Logic Apps külön díj nélkül biztosítható a régióban. 

BizTalk Services a B2B-feldolgozáshoz a régión kívüli vész-helyreállításhoz biztonsági mentési és visszaállítási folyamat szükséges. Az üzletmenet folytonossága érdekében Logic Apps biztosítja a régión belüli aktív/passzív [Dr képességet](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md), amely lehetővé teszi a B2B-adatokat különböző régiókban lévő integrációs fiókokon keresztül szinkronizálni.

## <a name="next-steps"></a>További lépések

* [Mi az Logic Apps?](../logic-apps/logic-apps-overview.md)
* [Az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md), vagy a használat gyors megkezdése [előre elkészített sablonokkal](../logic-apps/logic-apps-create-logic-apps-from-templates.md)  
* A Logic apps szolgáltatásban használható [összes elérhető összekötő megtekintése](../connectors/apis-list.md)
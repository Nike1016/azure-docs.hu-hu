---
title: Elemzési lekérdezések futtatása a bérlői adatbázisokon Azure SQL Data Warehouse használatával | Microsoft Docs
description: Több-bérlős elemzési lekérdezések Azure SQL Database, SQL Data Warehouse, Azure Data Factory vagy Power BIból kinyert adatok használatával.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anumjs
ms.author: anjangsh
ms.reviewer: MightyPen, sstein
ms.date: 12/18/2018
ms.openlocfilehash: b22a9cf8c79530fd931cbe944ef5bfc876a02243
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570135"
---
# <a name="explore-saas-analytics-with-azure-sql-database-sql-data-warehouse-data-factory-and-power-bi"></a>Ismerje meg a SaaS Analytics Azure SQL Database, SQL Data Warehouse, Data Factory és Power BI

Ebben az oktatóanyagban egy végpontok közötti elemzési forgatókönyvet mutatunk be. A forgatókönyv azt mutatja be, hogy a bérlői adat elemzése hogyan teheti lehetővé a szoftvergyártók számára az intelligens döntések meghozatalát. Az egyes bérlői adatbázisokból kinyert adatok felhasználásával az elemzéssel betekintést nyerhet a bérlők viselkedésére, beleértve a minta Wingtip tickets SaaS-alkalmazás használatát is. Ez a forgatókönyv három lépést foglal magában: 

1.  Az egyes bérlői adatbázisokból származó **adatok** kinyerése egy elemzési tárolóba, ebben az esetben egy SQL Data Warehouse.
2.  **Optimalizálja a** kinyert adatelemzési folyamatokat.
3.  Az **üzleti intelligencia** eszközeivel hasznos elemzéseket készíthet, amelyek útmutatást nyújtanak a döntéshozatalhoz. 

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> - Hozza létre a bérlői elemzési tárolót a betöltéshez.
> - A Azure Data Factory (ADF) használatával kinyerheti az egyes bérlői adatbázisok adatait az elemzési adattárházba.
> - Optimalizálja a kinyert adathalmazokat (egy csillag-sémába való átszervezést).
> - Az elemzési adattárház lekérdezése.
> - Az adatvizualizációk Power BI használatával kiemelheti a bérlői adattrendeket, és javaslatokat tehet a tökéletesítésekhez.

![architectureOverView](media/saas-tenancy-tenant-analytics/adf_overview.png)

## <a name="analytics-over-extracted-tenant-data"></a>A kinyert bérlői adatelemzések

Az SaaS-alkalmazások potenciálisan nagy mennyiségű bérlői adattal rendelkeznek a felhőben. Ezek az adatok az alkalmazás működésével és használatával, valamint a bérlők viselkedésével kapcsolatos részletes információforrásokat is biztosítanak. Ezek az ismeretek segítik a funkciók fejlesztését, a használhatóság javítását, valamint az alkalmazások és a platform egyéb befektetéseit.

Az összes bérlőre vonatkozó adatok elérése egyszerű, ha az összes adatok csak egy több-bérlős adatbázisban vannak. A hozzáférés azonban összetettebb, ha több ezer adatbázis között oszlik meg. Az összetettség kifejtésének egyik módja, ha az adatok kinyerése egy elemzési adatbázisba vagy egy adattárházba történik a lekérdezéshez.

Ez az oktatóanyag egy teljes körű elemzési forgatókönyvet mutat be a Wingtip tickets alkalmazáshoz. Az első, [Azure Data Factory (ADF)](../data-factory/introduction.md) az egyes bérlői adatbázisokból származó jegyek értékesítésének és kapcsolódó adatainak kinyerésére szolgáló előkészítési eszköz. Ezeket az adatkészleteket az elemzési tárban lévő előkészítési táblákba tölti be a rendszer. Az elemzési tár lehet egy SQL Database vagy egy SQL Data Warehouse. Ez az oktatóanyag [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) használja az elemzési tárolóként.

Ezután a kibontott adatmennyiség átalakítja és betöltődik a [Star-Schema](https://www.wikipedia.org/wiki/Star_schema) táblákba. A táblák egy központi tény tábla és a kapcsolódó dimenzió táblákból állnak:

- A Star-Schema központi tény táblázata a Ticket-adattartalomot tartalmazza.
- A dimenzió táblák a helyszínekkel, eseményekkel, ügyfelekkel és vásárlási dátumokkal kapcsolatos információkat tartalmaznak.

A központi és a dimenzió táblázatok együttesen teszik lehetővé a hatékony analitikai feldolgozást. Az oktatóanyagban használt csillag-séma az alábbi képen látható:
 
![architectureOverView](media/saas-tenancy-tenant-analytics/starschematables.JPG)

Végül a Star-Schema táblákat kérdezi le. A lekérdezési eredmények vizuálisan láthatók a Power BI segítségével, hogy kiemelje a bérlői viselkedést és az alkalmazás használatát. Ezzel a csillag-sémával a következő lekérdezéseket futtatja:

- Ki vásárol jegyet és melyik helyszínről.
- Minták és trendek a jegyek értékesítésében.
- Az egyes helyszínek relatív népszerűsége.

Ez az oktatóanyag alapvető példákat tartalmaz a Wingtip jegyek adatainak begyűjtésére. Annak megértése, hogy az egyes helyszínek milyen módon használják a szolgáltatást, a Wingtip jegyek gyártója a több vagy kevesebb aktív helyszínre irányuló különböző szolgáltatási csomagokra gondolhat például. 

## <a name="setup"></a>Beállítás

### <a name="prerequisites"></a>Előfeltételek

> [!NOTE]
> Ez az oktatóanyag a Azure Data Factory jelenleg korlátozott előzetes verzióban (társított szolgáltatás paraméterezés) elérhető funkcióit használja. Ha ezt az oktatóanyagot szeretné elvégezni, adja meg az előfizetés-AZONOSÍTÓját [itt](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxrVywox1_tHk9wgd5P8SVJUNlFINjNEOElTVFdMUEREMjVVUlJCUDdIRyQlQCN0PWcu). Az előfizetés engedélyezése után azonnal megerősítjük a megerősítést.

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:
- A Wingtip tickets SaaS-adatbázist bérlői alkalmazásként helyezi üzembe. Ha kevesebb mint öt perc alatt kíván üzembe helyezni, tekintse meg [a Wingtip SaaS-alkalmazás üzembe helyezése és megismerése](saas-dbpertenant-get-started-deploy.md)című témakört.
- A Wingtip jegyek SaaS-adatbázisa bérlői parancsfájlokban és az alkalmazás [forráskódja](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) a githubról tölthető le. Lásd: letöltési utasítások. Győződjön meg arról, hogy *feloldja a zip-fájl zárolását* a tartalom kicsomagolása előtt.
- A Power BI Desktop telepítve van. [Power bi Desktop letöltése](https://powerbi.microsoft.com/downloads/).
- További bérlők kötegét a bérlők kiépítése oktatóanyagban találhatja [](saas-dbpertenant-provision-and-catalog.md)meg.

### <a name="create-data-for-the-demo"></a>Adatgyűjtés a bemutatóhoz

Ez az oktatóanyag a Ticket Sales szolgáltatással kapcsolatban felderített elemzéseket ismerteti. Ebben a lépésben az összes bérlőre vonatkozó Ticket-adatkészletet fog előállítani. Egy későbbi lépésben ezeket az adatkivonatokat elemzi a rendszer. _Győződjön meg arról, hogy kiépítte a bérlők kötegét_ (a korábban leírtak szerint), hogy elegendő mennyiségű adattal rendelkezzen a jegyek különböző vásárlási mintáinak kimutatása érdekében.

1. A PowerShell ISE-ben nyissa meg a *. ..\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1*, és állítsa be a következő értéket:
    - $DemoScenario = **1** vásárlási jegyet az események minden helyszínén
2. Nyomja le az **F5** billentyűt a szkript futtatásához és a jegyek vásárlási előzményeinek létrehozásához az összes helyszín esetében. 20 bérlő esetén a szkript több tízezer jegyet generál, és akár 10 percet is igénybe vehet.

### <a name="deploy-sql-data-warehouse-data-factory-and-blob-storage"></a>SQL Data Warehouse, Data Factory és Blob Storage üzembe helyezése 
A Wingtip jegyek alkalmazásban a bérlők tranzakciós adatait számos adatbázison keresztül osztják el. Azure Data Factory (ADF) használatával összehangolhatja az adatok kinyerését, betöltését és átalakítását (ELT) az adatraktárba. Az adatok SQL Data Warehouse leghatékonyabban történő betöltéséhez az ADF kinyeri az adatok köztes blob-fájlokat, majd a [Base](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading) használatával tölti be az adattárházba.   

Ebben a lépésben üzembe helyezi az oktatóanyagban használt további erőforrásokat: egy _tenantanalytics_nevű SQL Data Warehouse, egy dbtodwload nevű Azure Data Factory és egy Azure Storage _\<\>-_ fiók nevű  _wingtipstaging\<-felhasználó\>_ . A Storage-fiók a kibontott adatfájlok blobként való ideiglenes tárolására szolgál az adatraktárba való betöltés előtt. Ez a lépés az adatraktár-sémát is üzembe helyezi, és meghatározza az ADF-folyamatokat, amelyek a ELT folyamatot hangolják össze.
1. A PowerShell ISE-ben nyissa meg a *. ..\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1* és állítsa be a következőket:
    - $DemoScenario = **2** a bérlői elemzési adattárház, a blob Storage és az adatelőállító üzembe helyezése 
1. Nyomja le az **F5** billentyűt a bemutató parancsfájl futtatásához és az Azure-erőforrások üzembe helyezéséhez. 

Most tekintse át az üzembe helyezett Azure-erőforrásokat:
#### <a name="tenant-databases-and-analytics-store"></a>Bérlői adatbázisok és Analitika-tároló
A [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) használatával kapcsolódjon a **tenants1-DPT&lt;-&gt; User** és a **Catalog-&lt;DPT&gt; -User** kiszolgálókhoz. Cserélje &lt;le&gt; a felhasználót az alkalmazás üzembe helyezésekor használt értékre. Használja a login = *Developer* és a Password = *P\@ssword1*. További útmutatásért tekintse meg a [bevezető oktatóanyagot](saas-dbpertenant-wingtip-app-overview.md) .

![Kapcsolódás SQL Database kiszolgálóhoz a SSMS-ből](media/saas-tenancy-tenant-analytics/ssmsSignIn.JPG)

A Object Explorerban:

1. Bontsa ki a *tenants1-&lt;DPT&gt; -User* kiszolgálót.
1. Bontsa ki az adatbázisok csomópontot, és tekintse meg a bérlői adatbázisok listáját.
1. Bontsa ki a *Catalog-&lt;DPT&gt; -User* kiszolgálót.
1. Ellenőrizze, hogy megjelenik-e a következő objektumokat tartalmazó elemzési tároló:
    1. A **raw_Tickets**, a **raw_Customers**, a **raw_Events** és a **raw_Venues** táblák a bérlői adatbázisokból származó nyers kinyert adatokkal rendelkeznek.
    1. A Star-Schema táblák a következők: **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**és **dim_Dates**.
    1. A tárolt eljárás, a **sp_transformExtractedData** az adatátalakításra és a Star-Schema táblákba való betöltésére szolgál.

![DWtables](media/saas-tenancy-tenant-analytics/DWtables.JPG)

#### <a name="blob-storage"></a>Blob Storage
1. Az [Azure Portalon](https://ms.portal.azure.com)navigáljon az alkalmazás üzembe helyezéséhez használt erőforráscsoporthoz. Győződjön meg arról, hogy a **wingtipstaging\<-\> felhasználó** nevű Storage-fiók lett hozzáadva.

   ![DWtables](media/saas-tenancy-tenant-analytics/adf-staging-storage.PNG)

1. A jelen lévő objektumok feltárásához kattintson a **wingtipstaging\<felhasználói\>**  Storage-fiók elemre.
1. Kattintson a Blobok csempére
1. Kattintson a tároló **engedélybeállítások**
1. Ellenőrizze, hogy a **engedélybeállítások** tartalmaz-e egy **TableConfig. JSON**nevű JSON-fájlt. Ez a fájl tartalmazza a forrás-és a céltábla nevét, az oszlopnevek és a nyomkövetési oszlop nevét.

#### <a name="azure-data-factory-adf"></a>Azure Data Factory (ADF)
Az erőforráscsoport [Azure portalján](https://ms.portal.azure.com) ellenőrizze, hogy a _\<dbtodwload-User\>_  nevű Azure Data Factory lett-e hozzáadva. 

 ![adf_portal](media/saas-tenancy-tenant-analytics/adf-data-factory-portal.png)

Ez a szakasz a létrehozott adatelőállítót vizsgálja. Az alábbi lépéseket követve indítsa el az adatgyárat:
1. A portálon kattintson a **dbtodwload\<\>** nevű adatelőállítóra.
2. Kattintson a **szerző & figyelés** csempére, hogy elindítsa a Data Factory designert egy külön lapon. 

## <a name="extract-load-and-transform-data"></a>Adatok kinyerése, betöltése és átalakítása
Azure Data Factory a rendszer az kinyerési, betöltési és átalakítási folyamatokat használja. Ebben az oktatóanyagban négy különböző SQL-nézetből származó adatok kinyerése az egyes bérlői adatbázisokból: **rawTickets**, **rawCustomers**, **rawEvents**és **rawVenues**. Ezek a nézetek tartalmazzák a helyszín azonosítóját, így az adattárház egyes helyein lévő adatok megkülönböztetését is elvégezheti. A rendszer az adatraktár megfelelő előkészítési tábláiba tölti be az adatmennyiséget: **raw_Tickets**, **raw_customers**, **raw_Events** és **raw_Venue**. A tárolt eljárások ezután átalakítja a nyers adatokat, és feltölti a csillag-séma táblákat: **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**és **dim_Dates**.

Az előző szakaszban üzembe helyezte és inicializálta a szükséges Azure-erőforrásokat, beleértve az adatelőállítót is. A központilag telepített adat-előállító a bérlői adatok kinyeréséhez, betöltéséhez és átalakításához szükséges folyamatokat, adatkészleteket, társított szolgáltatásokat stb. tartalmazza. Ismerkedjen meg ezekkel az objektumokkal, majd aktiválja a folyamatot, hogy a bérlői adatbázisok adatait az adattárházba helyezze át.

### <a name="data-factory-pipeline-overview"></a>A adatfeldolgozó-folyamat áttekintése
Ez a szakasz az adatelőállítóban létrehozott objektumokat vizsgálja. Az alábbi ábra az ebben az oktatóanyagban használt ADF-folyamat általános munkafolyamatát ismerteti. Ha később szeretné felderíteni a folyamatot, és először az eredményeket látja, ugorjon a **folyamat futtatását kiváltó**következő szakaszra.

![adf_overview](media/saas-tenancy-tenant-analytics/adf-data-factory.PNG)

Az Áttekintés oldalon váltson a **Szerző** lapra a bal oldali panelen, és figyelje meg, hogy három [folyamat](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) és három [adatkészlet](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services) lett létrehozva.
![adf_author](media/saas-tenancy-tenant-analytics/adf_author_tab.JPG)

A három beágyazott folyamat a következők: SQLDBToDW, DBCopy és TableCopy.

**1. folyamat – a SQLDBToDW** megkeresi a katalógus-adatbázisban tárolt bérlői adatbázisok nevét (tábla neve: [__ShardManagement]. [ ShardsGlobal]) és minden bérlői adatbázis esetében végrehajtja a **DBCopy** folyamatot. Befejezésekor a rendszer végrehajtja a megadott **sp_TransformExtractedData** tárolt eljárási sémát. Ez a tárolt eljárás átalakítja a betöltött adatokat az előkészítési táblákban, és feltölti a Star-Schema táblákat.

**2. folyamat – a DBCopy** a blob Storage-ban tárolt konfigurációs fájlból megkeresi a forrástábla és oszlopok nevét.  A **TableCopy** folyamat ezután a négy táblázat mindegyikéhez fut: TicketFacts, CustomerFacts, EventFacts és VenueFacts. A **[foreach](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)** tevékenység párhuzamosan fut mind a 20 adatbázis esetében. Az ADF lehetővé teszi, hogy legfeljebb 20 hurkos ismétlést futtasson párhuzamosan. Érdemes több folyamatot létrehozni több adatbázishoz.    

**3. folyamat – a TableCopy** a SQL Database (_ROWVERSION_) sor verziószámait használja a módosított vagy frissített sorok azonosításához. Ez a tevékenység megkeresi a kezdő és a befejező sor verziószámát a sorok kinyeréséhez a forrás tábláiból. Az egyes bérlői adatbázisokban tárolt **CopyTracker** táblázat az egyes futtatások minden egyes táblájából kinyert utolsó sort követi nyomon. Az új vagy módosított sorok az adatraktár megfelelő előkészítési tábláiba másolódnak: **raw_Tickets**, **raw_Customers**, **raw_Venues**és **raw_Events**. Végül a rendszer az utolsó sor verzióját menti a **CopyTracker** táblába, hogy a következő kinyeréskor a kezdeti sor verziója legyen használatban. 

Az adatelőállítót a forrás SQL-adatbázisokhoz, a cél SQL Data Warehousehoz és a köztes blob Storage-hoz kapcsolódó három paraméteres társított szolgáltatás is összeköti. A **Szerző** lapon kattintson a **kapcsolatok** elemre a társított szolgáltatások megismeréséhez, ahogy az a következő képen látható:

![adf_linkedservices](media/saas-tenancy-tenant-analytics/linkedservices.JPG)

A három társított szolgáltatásnak megfelelő három olyan adathalmaz van, amelyek a folyamat tevékenységeiben a bemenetként vagy kimenetként használt adatokra hivatkoznak. Fedezze fel az egyes adatkészleteket a kapcsolatok és a használt paraméterek megfigyeléséhez. A _AzureBlob_ a forrás-és a célként megadott táblákat és oszlopokat, valamint az egyes források nyomon követési oszlopát tartalmazó konfigurációs fájlra mutat.
  
### <a name="data-warehouse-pattern-overview"></a>Az adatraktár-minta áttekintése
A SQL Data Warehouse a bérlői adatok összesítésének elvégzésére szolgáló elemzési tárolóként szolgál. Ebben a példában a rendszer az adatoknak az SQL-adattárházba való betöltésére használatos. A nyers adatok betöltődik olyan átmeneti táblákba, amelyek rendelkeznek egy azonosító oszloppal, hogy nyomon kövessék a Star-Schema táblákba átalakított sorokat. A következő képen a betöltési minta ![látható: loadingpattern](media/saas-tenancy-tenant-analytics/loadingpattern.JPG)

Ebben a példában a lassan változó dimenzió (. SCD) Type 1 dimenziós táblázatok használatosak. Minden dimenzióhoz tartozik egy azonosító oszlop használatával meghatározott helyettes kulcs. Az ajánlott eljárás szerint a dátum dimenzió tábla előre ki van töltve, hogy időt takarítson meg. A többi dimenzió tábláinál a CREATE TABLE válassza ki a következőt:... (CTAS) utasítás egy ideiglenes tábla létrehozására szolgál, amely tartalmazza a meglévő módosított és nem módosított sorokat, valamint a helyettesítő kulcsokat. Ez a következővel történik: IDENTITY_INSERT = ON. Ezután a rendszer beszúrja az új sorokat a táblába a IDENTITY_INSERT = OFF beállítással. Az egyszerű visszaállításhoz a rendszer átnevezi a meglévő dimenzió táblát, és az ideiglenes táblát átnevezi, hogy az új dimenzió táblázat legyen. Az egyes futtatások előtt a régi dimenzió táblát törli a rendszer.

A dimenzió táblák betöltődik a egyedkapcsolat tábla előtt. Ez az előkészítés biztosítja, hogy minden egyes beérkező tény esetében az összes hivatkozott dimenzió már létezik. Mivel a rendszer betölti a tényeket, a rendszer az összes kapcsolódó dimenzióhoz tartozó üzleti kulcsot egyezteti, és az egyes tényekhez hozzáadja a megfelelő helyettesítő kulcsokat.

Az átalakítás utolsó lépése törli az előkészítési adatmennyiséget a folyamat következő végrehajtásához.
   
### <a name="trigger-the-pipeline-run"></a>A folyamat futtatásának elindítása
Kövesse az alábbi lépéseket az összes bérlői adatbázis teljes kinyerési, betöltési és átalakítási folyamatának futtatásához:
1. Az ADF felhasználói felület **Szerző** lapján válassza a **SQLDBToDW** folyamat lehetőséget a bal oldali ablaktáblán.
1. Kattintson az **aktiválás** elemre, majd a legördülő menüben kattintson az **aktiválás most**elemre. Ez a művelet azonnal futtatja a folyamatot. Éles környezetben a folyamat futtatásának ütemezését kell megadnia, hogy az ütemezés szerint frissítse az adataikat.
  ![adf_trigger](media/saas-tenancy-tenant-analytics/adf_trigger.JPG)
1. A **folyamat futtatása** lapon kattintson a **Befejezés**gombra.
 
### <a name="monitor-the-pipeline-run"></a>A folyamat futásának monitorozása
1. Az ADF felhasználói felületen váltson a **monitor** lapra a bal oldali menüben.
1. Kattintson a **frissítés** gombra, amíg a SQLDBToDW folyamat állapota **sikeres**nem lesz.
  ![adf_monitoring](media/saas-tenancy-tenant-analytics/adf_monitoring.JPG)
1. Kapcsolódjon az adattárházhoz a SSMS-szel, és kérdezze le a Star-Schema táblákat annak ellenőrzéséhez, hogy az adott táblázatban szerepelnek-e az adatgyűjtés.

A folyamat befejezését követően az egyedkapcsolat tábla az összes helyszínre vonatkozó jegyeladási adatokat tartalmazza, a dimenzió táblák pedig a megfelelő helyszínekkel, eseményekkel és ügyfelekkel lesznek feltöltve.

## <a name="data-exploration"></a>Adatelemzés

### <a name="visualize-tenant-data"></a>Bérlői adatmegjelenítés

A Star-Schema adatai az elemzéshez szükséges összes Ticket Sales-adatforgalmat biztosítják. Az adatábrázolási folyamat megkönnyíti a nagy adathalmazok trendjeinek megjelenítését. Ebben a szakaszban a **Power bi** segítségével kezelheti és megjelenítheti az adattárházban található bérlői adatkészleteket.

A következő lépésekkel csatlakozhat a Power BIhoz, és importálhatja a korábban létrehozott nézeteket:

1. Indítsa el Power BI Desktopot.
2. A Kezdőlap menüszalagon válassza az **adatok**lekérése lehetőséget, és válassza a **továbbiak...** lehetőséget. a menüből.
3. Az **adatlekérdezés** ablakban válassza a **Azure SQL Database**lehetőséget.
4. Az adatbázis-bejelentkezés ablakban adja meg a kiszolgáló nevét (**Catalog-DPT-&lt;User&gt;. database.Windows.net**). Válassza az **Importálás** adatkapcsolati **módra**lehetőséget, majd kattintson **az OK**gombra. 

    ![sign-in-to-power-bi](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. Válassza ki az **adatbázist** a bal oldali ablaktáblán, majd írja be a Felhasználónév = *fejlesztő*nevet, majd írja be a Password = *P\@ssword1*értéket. Kattintson a **Csatlakozás** gombra.  

    ![database-sign-in](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. A **navigátor** ablaktábla elemzési adatbázis területén válassza ki a csillag-séma táblákat: **fact_Tickets**, **dim_Events**, **dim_Venues**, **dim_Customers** és **dim_Dates**. Ezután válasszaa betöltés lehetőséget. 

Gratulálunk! Az adatPower BIba való betöltése sikeresen megtörtént. Most Ismerkedjen meg az érdekes vizualizációkkal, hogy betekintést nyerjen a bérlők felé. Ismerkedjen meg a Wingtip tickets Business csapatával, hogy az elemzések hogyan biztosíthatnak adatvezérelt ajánlásokat. A javaslatok segítségével optimalizálható az üzleti modell és a felhasználói élmény.

Kezdje a Ticket Sales-adatok elemzésével, és tekintse meg a különböző helyszíneken való használat változását. Válassza ki a Power BIban látható beállításokat az egyes helyszíneken eladott jegyek teljes számát ábrázoló sávdiagram ábrázolásához. (A Ticket Generator véletlenszerű variációja miatt előfordulhat, hogy az eredmények eltérőek.)
 
![TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues-DW.PNG)

Az előző ábra megerősíti, hogy az egyes helyszínek által eladott jegyek száma változó. A több jegyet értékesítő helyszínek sokkal nagyobb mértékben használják a szolgáltatást, mint a kevesebb jegyet értékesítő helyszíneken. Lehetőség van arra, hogy az erőforrás-kiosztást a különböző bérlői igényeknek megfelelően testre lehessen szabni.

Tovább elemezheti az adatelemzést, hogy megtudja, hogyan változnak a jegyek forgalma az idő múlásával. Válassza ki az alábbi képen látható beállításokat a Power BI az egyes napokon a 60 napos időszakra eladott jegyek teljes számának ábrázolásához.
 
![SaleVersusDate](./media/saas-tenancy-tenant-analytics/SaleVersusDate-DW.PNG)

Az előző diagramon látható, hogy egyes helyszíneken a Ticket Sales Spike szerepel. Ezek a tüskék erősítik azt a gondolatot, hogy egyes helyszínek aránytalanul nagy mennyiségű rendszererőforrást fogyasztanak. Eddig nem látható mintázat a tüskék előfordulásakor.

A következő lépésben vizsgáljuk meg, hogy ezek a maximális értékesítési napok milyen jelentőséggel bírnak. Mikor fordulnak elő ezek a csúcsok a jegyek eladása után? A naponta eladott jegyek megjelenítéséhez válassza ki az alábbi képen látható beállításokat Power BI.

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay-DW.PNG)

Ez a mintaterület azt mutatja, hogy egyes helyszínek nagy mennyiségű jegyet árulnak az első napján. Amint a jegyek ezen a helyszíneken kerülnek értékesítésre, úgy tűnik, hogy egy őrült rohanás. Ez a feltört tevékenység néhány helyszín esetében hatással lehet a szolgáltatásra más bérlők esetében.

Az adat részletezésével megtekintheti, hogy ez az őrült rohanás igaz-e az ezen helyszínek által üzemeltetett összes esemény esetében. A korábbi mintaterületeken látta, hogy a contoso Concert Hall sok jegyet árul, és a contoso bizonyos napokon is tartalmaz egy tüske-beosztást. A Power BI lehetőségek közül választhat a contoso Concert-csarnok összesített jegyeladásának ábrázolásához, amely az egyes események értékesítési trendjeire koncentrál. Az összes esemény ugyanazt az eladási mintát követi? Hozzon létre egy olyan ábrát, amely az alábbihoz hasonló.

![ContosoSales](media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

Ez a mintaterület a contoso Concert hallban az egyes eseményekhez tartozó összesített jegyek értékesítésének időbeli alakulását mutatja be, hogy az őrült Rush nem minden eseménynél történik. A szűrési lehetőségekkel megismerheti a más helyszínekre vonatkozó értékesítési trendeket.

A jegyek értékesítési mintáinak betekintése Wingtip jegyeket eredményezhet üzleti modelljének optimalizálásához. Az összes bérlő egyforma kitöltése helyett előfordulhat, hogy a Wingtip különböző számítási méretekkel kell bevezetni a szolgáltatási szinteket. A naponta több jegyet értékesítő nagyobb helyszínek magasabb szintű szolgáltatói szerződéssel (SLA) is elérhetők. Ezeknek a helyszíneknek az adatbázisai a készletbe helyezhetők, és az adatbázison belüli erőforrások korlátai magasabbak. Az egyes szolgáltatási szintek óránkénti értékesítési kiosztással rendelkezhetnek, és a foglalást meghaladó költségekkel számoljuk el. Az időszakos értékesítési lehetőségekkel rendelkező nagyobb helyszínek a magasabb szinteken is hasznosak lehetnek, és a Wingtip jegyek hatékonyabban növelhetik a szolgáltatást.

Néhány Wingtip-jegy arra is panaszkodik, hogy az ügyfelek nem tudnak elegendő jegyet értékesíteni, hogy igazolják a szolgáltatás költségeit. Előfordulhat, hogy ezekben az adatokban lehetősége van arra, hogy növelje a jegyek értékesítését az elvégezhető helyszíneken. A magasabb eladások növelhetik a szolgáltatás észlelt értékét. Kattintson jobb gombbal a fact_Tickets elemre, és válassza az **új mérték**lehetőséget. Adja meg a következő kifejezést a **AverageTicketsSold**nevű új mértékhez:

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

A következő vizualizációs beállítások kiválasztásával ábrázolhatja az egyes helyszíneken eladott jegyek százalékos arányát a relatív sikeresség megállapításához.

![AvgTicketsByVenues](media/saas-tenancy-tenant-analytics/AvgTicketsByVenues-DW.PNG)

A fenti ábra azt mutatja, hogy bár a legtöbb helyszín a jegyek több mint 80%-át értékesíti, néhányan több mint felet töltenek be. Az egyes helyszínekre eladott jegyek maximális vagy minimális százalékos arányának kiválasztásához az értékekkel is játszhat.

## <a name="embedding-analytics-in-your-apps"></a>Elemzések beágyazása az alkalmazásokba 
Ez az oktatóanyag olyan több-bérlős elemzésre összpontosított, amely a szoftvergyártók bérlők általi megismerésének javítására szolgál. Az elemzések a _bérlők_számára is hasznosak lehetnek, így hatékonyabban kezelhetik üzleti tevékenységüket. 

A Wingtip-jegyek példájában korábban felfedezte, hogy a jegyek értékesítései általában előre jelezhető mintákat követnek. Ez a betekintést felhasználhatja a jegyek értékesítésének növelésére. Lehetséges, hogy a gépi tanulási technikák alkalmazásával előre jelezheti a jegyek értékesítését az eseményekhez. Az árváltozások hatása is modellezhető, ami lehetővé teszi a kedvezmények előrejelzését. Az Power BI Embedded integrálható egy eseménykezelő alkalmazásba az előrejelzett eladások megjelenítéséhez, beleértve az eladott és az alacsonyan fogyó eseményekre vonatkozó árengedmények hatását is. A Power BI Embedded segítségével még a vizualizációs szolgáltatásban közvetlenül is integrálhatja a kedvezményt a jegyek áraiba.


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Olyan SQL Data Warehouse üzembe helyezése, amely a bérlői elemzéshez használható Star-sémával van feltöltve.
> * A Azure Data Factory használatával kinyerheti az egyes bérlői adatbázisok adatait az elemzési adattárházba.
> * Optimalizálja a kinyert adathalmazokat (egy csillag-sémába való átszervezést).
> * Az elemzési adattárház lekérdezése. 
> * A Power BI használatával jelenítheti meg az adattrendeket az összes bérlő között.

Gratulálunk!

## <a name="additional-resources"></a>További források

- [Az Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-alkalmazásra épülő további oktatóanyagok.

---
title: Ismerkedés az Azure SQL database naplózási szolgáltatásával |} A Microsoft Docs
description: Azure SQL database naplózási szolgáltatása segítségével követheti nyomon az adatbázissal kapcsolatos események auditálási naplóba kerülnek be.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 04/16/2019
ms.openlocfilehash: 1b3a6a18d10b9d9f6ab6456ae2911e54f5c56a71
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544089"
---
# <a name="get-started-with-sql-database-auditing"></a>Ismerkedés az SQL-adatbázis naplózási szolgáltatásával

Az Azure naplózási [SQL Database](sql-database-technical-overview.md) és [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) nyomon követi az adatbázisok eseményeit és felvezeti ezeket egy naplófájlba, az Azure storage-fiókot, OMS-munkaterület vagy az Event Hubs. A naplózás is:

- Segít a jogszabályoknak való megfelelőség, adatbázis-tevékenység megértésében, valamint betekintést nyerhet az eltéréseket és rendellenességeket, amelyek üzleti aggályokra vagy biztonsági problémákat.

- Lehetővé teszi, hogy, és megkönnyíti a megfelelőségi szabványok betartásának, bár ez nem garantálja a megfelelőség. Az Azure-ral kapcsolatos további információkat a támogatási szabványoknak való megfelelés programokat, tekintse meg a [Azure adatvédelmi központ](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) ahol megtalálhatja a legfrissebb listáját az SQL Database megfelelőségi minősítései közül is bemutat.


> [!NOTE] 
> Ez a témakör az Azure SQL Server-kiszolgálókra, valamint az Azure SQL Serveren létrehozott SQL Database- és SQL Data Warehouse-adatbázisokra vonatkozik. Az egyszerűség kedvéért a jelen témakörben az SQL Database és az SQL Data Warehouse megnevezése egyaránt SQL Database.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a id="subheading-1"></a>Az Azure SQL database naplózás – áttekintés

Használhatja az SQL database naplózási szolgáltatásán keresztül:

- **Megőrzése** auditnaplót kiválasztott események. Megadhatja, hogy az adatbázis-műveleteket naplózni kívánt kategóriáit.
- **A jelentés** az adatbázis-tevékenységeket. Előre konfigurált jelentéseket és a egy irányítópult segítségével gyorsan használatba tevékenység és az események naplózásához.
- **Elemezheti** jelentéseket. Megtalálhatja a gyanús eseményeket, a szokatlan tevékenységeket és a trendeket.

> [!IMPORTANT]
> Auditnaplók írt **hozzáfűző Blobok** az Azure Blob storage, Azure-előfizetésében.
>
> - Az összes tárolási típusú (v1, v2, blob) támogatottak.
> - Az összes tárolási replikációs konfiguráció támogatott.
> - **A Premium storage** jelenleg **nem támogatott**.
> - **A virtuális hálózatok közötti tárolási** jelenleg **nem támogatott**.
> - **Tűzfal mögött található tárolási** jelenleg **nem támogatott**

## <a id="subheading-8"></a>Adja meg a kiszolgálószintű és adatbázisszintű naplózási házirend

A naplózási házirend egy adott adatbázis vagy a kiszolgáló alapértelmezett szabályzatként adható meg:

- Az összes meglévő és újonnan létrehozott adatbázisok a kiszolgálón a kiszolgáló házirend vonatkozik.

- Ha *kiszolgálószintű blobnaplózás engedélyezve van*, azt *mindig vonatkozik, az adatbázis*. Az adatbázis naplózva lesz, függetlenül az adatbázis naplózási beállításait.

- Engedélyezi a blobnaplózás az adatbázist, sem az adattárházra vonatkozó, a kiszolgálón, egyrészt does *nem* bírálja felül, vagy módosíthatja a kiszolgálószintű blobnaplózás beállításait. Mindkét naplózások egymás mellett fog létezik. Más szóval az adatbázis kétszer párhuzamosan; rögzítése egyszer, a kiszolgáló házirend és egyszer az adatbázis-házirendet.

   > [!NOTE]
   > Kerülendő, kivéve, ha engedélyezi a kiszolgálószintű blobnaplózás és adatbázis blobnaplózás együtt:
    > - Egy másik használni kívánt *tárfiók* vagy *megőrzési időszak* egy adott adatbázis számára.
    > - Eseménytípusok vagy egy adott adatbázis, amely eltér a többi a kiszolgálón lévő adatbázisokhoz kategóriáit naplózni kívánt. Előfordulhat például, hogy tábla Beszúrások, amelyet csak egy adott adatbázishoz kell naplózni kell.
   >
   > Ellenkező esetben javasoljuk, hogy csak a kiszolgálószintű blobnaplózás engedélyezése, és hagyja meg az adatbázisszintű naplózást az összes adatbázis le van tiltva.

## <a id="subheading-2"></a>Az adatbázis naplózás beállítása

Az alábbi szakasz ismerteti a konfigurációt, a naplózás az Azure portal használatával.

1. Nyissa meg az [Azure Portal](https://portal.azure.com).
2. Navigáljon a **naplózási** az SQL-adatbázis-kiszolgáló panelen biztonsági fejléc alatt.

    <a id="auditing-screenshot"></a>![Navigációs ablaktábla][1]

3. Ha inkább a állítsa be a kiszolgáló naplózási szabályzatot, válassza a **kiszolgálóbeállítások megtekintése** az adatbázis naplózási lapon. Ezután megtekintheti vagy módosíthatja a kiszolgáló naplózási beállításait. Kiszolgáló naplózási házirendek ezen a kiszolgálón az összes meglévő és újonnan létrehozott adatbázisokra érvényesek.

    ![Navigációs ablak][2]

4. Ha szeretné engedélyezni a naplózást az adatbázisszintű, váltson **naplózási** való **ON**.

    Ha a kiszolgáló-naplózás engedélyezve van, az adatbázisszinten konfigurált naplózás egymás mellett az a kiszolgáló naplózási fog létezik.

    ![Navigációs ablak][3]

5. **Új** – most már több lehetőség áll rendelkezésére konfigurálásához ahol auditnaplók lesz írva. Naplók az Azure storage-fiók, egy Log Analytics-munkaterületet a memóriafogyasztást úgy, hogy az Azure Monitor naplóira vagy kínál az event hubs az eseményközpont írhat. Az alábbi lehetőségek közül bármelyiket is beállíthat, és minden egyes auditnaplók lesz írva.

   > [!WARNING]
   > Engedélyezve van a naplózás a Log Analytics Adatbetöltési díjait számoljuk fel alapján számítunk fel. Vegye figyelembe, ez a járulékos költsége [beállítás](https://azure.microsoft.com/pricing/details/monitor/), vagy fontolja meg a naplózási tároló Azure storage-fiókkal bejelentkezik.

    ![tárolási lehetőségek](./media/sql-database-auditing-get-started/auditing-select-destination.png)

6. Írási naplózás konfigurálása naplózza a storage-fiók, jelölje be **tárolási** , és nyissa meg **tárolási adatok**. Válassza ki az Azure storage-fiók, ahol naplók menti a rendszer, és válassza a megőrzési időtartam. Törli a régi naplókat. Ezután kattintson az **OK** gombra.

    ![tárfiók](./media/sql-database-auditing-get-started/auditing_select_storage.png)

7. Írási naplózás konfigurálása naplózza a Log Analytics-munkaterülettel, válassza ki **Log Analytics (előzetes verzió)** , és nyissa meg **Log Analytics részletes**. Válasszon vagy hozzon létre a Log Analytics-munkaterületet, ahol a naplók lesz írva, és kattintson a **OK**.

    ![Log Analytics-munkaterület](./media/sql-database-auditing-get-started/auditing_select_oms.png)

8. Írási naplózás konfigurálása naplózza az eseményközpontok felé, jelölje be **Event Hub (előzetes verzió)** , és nyissa meg **Eseményközpont részleteinek**. Válassza ki az event hubs, ahol a naplók lesz írva, és kattintson a **OK**. Győződjön meg arról, hogy az event hubs és az adatbázis és a kiszolgáló ugyanabban a régióban van.

    ![Eseményközpont](./media/sql-database-auditing-get-started/auditing_select_event_hub.png)

9. Kattintson a **Save** (Mentés) gombra.
10. Ha szeretné a naplózott események testreszabásához, ehhez keresztül [PowerShell-parancsmagok](#subheading-7) vagy a [REST API-val](#subheading-9).
11. A naplózási beállítások konfigurálása után kapcsolja be az új fenyegetés-észlelési szolgáltatást, és konfigurálja a biztonsági riasztást küld e-maileket. A fenyegetésészlelés használata esetén jelezheti a potenciális biztonsági fenyegetések rendellenes adatbázis-tevékenységek a proaktív értesítéseket kap. További információkért lásd: [fenyegetések észlelése – első lépések](sql-database-threat-detection-get-started.md).

> [!IMPORTANT]
> Az Azure SQL Data Warehouse felfüggesztett naplózásának engedélyezése esetén nem lehet. Az engedélyezéshez, un szüneteltetési az Adatraktárba.

> [!WARNING]
> Egy kiszolgálóra, amelyen az Azure SQL Data Warehouse, a naplózás engedélyezése **eredményez a Data Warehouse folyamatban folytatódik, és újra fel van függesztve újra** ami a számlázási költségeket számítanak fel.

## <a id="subheading-3"></a>Elemezheti a vizsgálati naplók és jelentések

Ha úgy döntött, hogy auditnaplók írni az Azure Monitor naplóira:

- Használja a [az Azure portal](https://portal.azure.com).  Nyissa meg a megfelelő adatbázis. Az adatbázis tetején **naplózási** kattintson **naplók megtekintése**.

    ![Naplók megtekintése](./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png)

- Kattintson a **nyissa meg az OMS-ben** felső részén a **naplórekordok** lap nyílik meg a naplók megtekintése a Log Analytics, ahol testreszabhatja az időtartományt, és a keresési lekérdezésben.

    ![Nyissa meg a Log Analyticsben](./media/sql-database-auditing-get-started/auditing_open_in_oms.png)

- Azt is megteheti is elérheti a naplófájlok a Log Analytics panelen. Nyissa meg a Log Analytics-munkaterület és a **általános** területén kattintson **naplók**. Megkezdése egy egyszerű lekérdezéssel, például: *"SQLSecurityAuditEvents" Keresés* naplózza a naplózási megtekintéséhez.
    Itt is használhatja [naplózza az Azure Monitor](../log-analytics/log-analytics-log-search.md) futtatni speciális keresést a naplózási adatokat. Az Azure Monitor naplóira azonnal elemezze a rekordok millióit, a számítási feladatok és kiszolgálók integrált keresést és egyéni irányítópultok segítségével valós idejű az operational insights biztosítja. További hasznos információkat az Azure Monitor naplók keresési nyelv és a parancsok, lásd: [Azure Monitor naplózza a keresési referencia](../log-analytics/log-analytics-log-search.md).

Ha úgy döntött, hogy auditnaplók írni az Event Hubs:

- Az Event Hubs naplózási adatok felhasználásához, szüksége lesz egy stream események felhasználásához, és a cél beállítása. További információkért lásd: [Azure Event Hubs – dokumentáció](https://docs.microsoft.com/azure/event-hubs/).
- Törzsében rögzítve lesznek a naplók az Event Hub [Apache Avro](https://avro.apache.org/) események segítségével a JSON formázását az UTF-8 kódolást és tárolhatók. A vizsgálati naplók elolvasásához használja [Avro eszközök](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview#use-avro-tools) vagy hasonló eszközt, amely ezt a formátumot.

Ha úgy döntött, hogy auditnaplók írni az Azure storage-fiók, több módon használhatja a naplók megtekintéséhez:

- Naplók a telepítés során választott fiókban vannak összesítve. Például egy eszköz használatával megvizsgálhatja auditnaplók [Azure Storage Explorer](https://storageexplorer.com/). Az Azure storage-ban naplói nevű tárolóban lévő blob fájlok kerülnek mentésre **sqldbauditlogs**. A tároló mappa a hierarchiával kapcsolatos további részletekért elnevezési konvenciók és a napló formátuma, tekintse meg a [SQL Database naplózási naplóformátum](https://go.microsoft.com/fwlink/?linkid=829599).

- Használja a [az Azure portal](https://portal.azure.com).  Nyissa meg a megfelelő adatbázis. Az adatbázis tetején **naplózási** kattintson **naplók megtekintése**.

    ![Navigációs ablak][7]

    **Naplórekordok** megnyitása, amelyen is elérheti a naplók megtekintéséhez.

  - Kattintva megtekintheti a konkrét dátumokat **szűrő** felső részén a **naplórekordok** lapot.
  - Naplózás a rekordok által létrehozott között válthat a *kiszolgáló naplózási házirend* és a *adatbázis naplózási házirend* átállításával **naplózási forrás**.
  - Csak az SQL-injektálás kapcsolódó naplórekordok ellenőrzésével megtekintheti **megjelenítése csak a naplózási bejegyzések az SQL-injektálások** jelölőnégyzetet.

       ![Navigációs ablak][8]

- A rendszer függvénnyel **sys.fn_get_audit_file** (T-SQL) a naplózási adatokat vissza a táblázatos formátumban. Ez a funkció használatáról további információkért lásd: [sys.fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Használat **naplózási fájlok egyesítése** az SQL Server Management Studio (SSMS 17-től induló):
    1. Az SSMS menüből válassza ki a **fájl** > **nyílt** > **naplózási fájlok egyesítése**.

        ![Navigációs ablak][9]
    2. A **naplózási fájlok hozzáadása** párbeszédpanel nyílik meg. Válassza ki az egyik a **Hozzáadás** beállítások számára, hogy a helyi lemezen lévő naplófájlokat egyesítéséhez, vagy importálhatja őket az Azure Storage-ból. Adja meg az Azure Storage-adatokat és a fiókkulcs szükségesek.

    3. Egyesítheti az összes fájl hozzáadása után kattintson a **OK** az egyesítési művelet végrehajtása.

    4. Az egyesített fájl megnyílik az ssms-ben, ahol meg is megtekintése és elemezhetők a, valamint exportálja az xel-fájlt vagy CSV-fájl, illetve egy táblázat.

- A Power BI használata. Megtekintheti és elemezheti a naplózási adatokat a Power bi-ban. További információ és a egy letölthető sablon eléréséhez tekintse meg a [elemzés naplózási adatokat a Power bi-ban](https://blogs.msdn.microsoft.com/azuresqldbsupport/20../../sql-azure-blob-auditing-basic-power-bi-dashboard/).
- Letölti a naplófájlokat a portálon keresztül, vagy egy eszköz használatával az Azure Storage blob-tárolóból [Azure Storage Explorer](https://storageexplorer.com/).
  - Miután letöltötte helyileg egy naplófájl, kattintson duplán a fájlra nyissa meg a, megtekintheti és elemezheti a naplókat az ssms-ben.
  - Azure Storage Explorer használatával egyszerre több fájl is letölthető. Ehhez kattintson a jobb gombbal egy adott almappát, és válassza ki **Mentés másként** egy helyi mappába menti.

- További módszereket:

  - Több fájlt vagy egy almappát, amely tartalmazza a naplófájlok a letöltés után egyesítheti őket helyileg a korábban leírt utasításokat SSMS egyesítési naplófájlokat leírtak szerint.
  - Nézet blobnaplózás programozott módon jelentkezik:

    - [Bővített események lekérdezésfájlok](https://sqlscope.wordpress.com/20../../reading-extended-event-files-using-client-side-tools-only/) PowerShell használatával.

## <a id="subheading-5"></a>Éles eljárások

<!--The description in this section refers to preceding screen captures.-->

### <a id="subheading-6">Naplózás a georeplikált adatbázis</a>

Georeplikált adatbázisokhoz amikor engedélyezi a naplózást az elsődleges adatbázis a másodlagos adatbázis lesz azonos a naplózási házirend. A másodlagos adatbázis naplózásának engedélyezésével naplózásának beállítása lehetőség arra is a **másodlagos kiszolgáló**, függetlenül az elsődleges adatbázisból.

- Kiszolgálószintű (**ajánlott**): Kapcsolja be a naplózást is a **elsődleges kiszolgáló** , valamint a **másodlagos kiszolgáló** – az elsődleges és másodlagos adatbázisok mindegyike naplózva lesz egymástól függetlenül a megfelelő kiszolgálói szintű házirend alapján.
- Adatbázisszintű: Másodlagos adatbázisok adatbázisszintű naplózása csak konfigurálható az elsődleges adatbázis naplózási beállításait.
  - Naplózás engedélyezve kell lennie a *elsődleges adatbázis*, nem a kiszolgálón.
  - Naplózás engedélyezése után az elsődleges adatbázison, akkor is válik elérhetővé, a másodlagos adatbázison.

    >[!IMPORTANT]
    >Az adatbázisszintű naplózást, a másodlagos adatbázis tárolási beállításait lesz megegyeznek az elsődleges adatbázis-régiók közti forgalom okozza. Azt javasoljuk, hogy csak a kiszolgálószintű naplózás engedélyezéséhez, és hagyja meg az adatbázisszintű naplózást az összes adatbázis le van tiltva.
    > [!WARNING]
    > Event hub vagy az Azure Monitor-naplók használatával célként az auditnaplókhoz a kiszolgáló szintjén jelenleg nem támogatott georeplikált másodlagos adatbázisok.

### <a id="subheading-6">Tárolás kulcs újragenerálása</a>

Éles környezetben, valószínűleg a tárelérési kulcsok rendszeresen frissíteni. Az Azure storage-naplók írásakor kell mentse el újra a naplózási házirend, a kulcsok frissítésekor. A folyamat a következőképpen történik:

1. Nyissa meg **tárolási adatok**. Az a **Tárelérési kulcs** jelölje ki **másodlagos**, és kattintson a **OK**. Kattintson a **mentése** a naplózási konfiguráció lap tetején.

    ![Navigációs ablak][5]
2. A tároló konfigurációs lap megnyitásához, és az elsődleges elérési kulcs újragenerálása.

    ![Navigációs ablak][6]
3. Lépjen vissza a naplózási konfiguráció lapon váltson a tárelérési kulcs az elsődleges, másodlagos, és kattintson **OK**. Kattintson a **mentése** a naplózási konfiguráció lap tetején.
4. Lépjen vissza a tárolási konfiguráció lapon, és újragenerálja a másodlagos elérési kulcsot (a frissítés során a következő kulcs előkészítése).

## <a name="additional-information"></a>További információk

- További információ a napló formátumú, a hierarchiában, a tároló mappa, és elnevezési konvenciók, tekintse meg a [Blob auditálási napló fájlformátum referenciája](https://go.microsoft.com/fwlink/?linkid=829599).

    > [!IMPORTANT]
    > Az Azure SQL Database naplózási tárolja egy naplórekordot data karaktert mezők esetében a 4000 karakternél. Ha a **utasítás** vagy a **data_sensitivity_information** naplózható művelet által visszaadott értékeket tartalmaznak legfeljebb 4000 karakterből, bármely adatait az első 4000 karakter után lesz  **csonkolva, és nem naplózott**.

- Auditnaplók írt **hozzáfűző Blobok** egy Azure Blob Storage, Azure-előfizetésében:
  - **A Premium Storage** jelenleg **nem támogatott** a hozzáfűző Blobok.
  - **A virtuális hálózatok közötti tárolási** jelenleg **nem támogatott**.

- Az alapértelmezett naplózási házirend tartalmazza az összes művelet és az alábbi Műveletcsoportok, amelyek ellenőrizni fogja a lekérdezések és az adatbázis, valamint a sikeres és sikertelen bejelentkezések ellen végrehajtott tárolt eljárások:

    BATCH_COMPLETED_GROUP<br>
    SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP<br>
    FAILED_DATABASE_AUTHENTICATION_GROUP

    A különféle műveletek és a PowerShell-lel, műveleti csoportokra naplózás leírtak szerint konfigurálhatja a [felügyelete az SQL database naplózási Azure PowerShell-lel](#subheading-7) szakaszban.

- AAD-hitelesítés használatakor a sikertelen bejelentkezések rekordok lesz *nem* jelennek meg az SQL-napló. A sikertelen bejelentkezési naplózási rekordok megtekintéséhez keresse fel kell a [Azure Active Directory portálon]( ../active-directory/reports-monitoring/reference-sign-ins-error-codes.md), amely ezek az események adatait naplózza.


## <a id="subheading-7"></a>Az SQL database naplózási Azure PowerShell-lel kezelése

**PowerShell-parancsmagok (beleértve a WHERE záradék támogatási további szűréshez)** :

- [Naplózási házirend (Set-AzSqlDatabaseAudit) adatbázis frissítése](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Létrehozni vagy frissíteni a kiszolgáló naplózási házirend (Set-AzSqlServerAudit)](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserveraudit)
- [Get Database Auditing Policy (Get-AzSqlDatabaseAudit)](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseaudit)
- [Get Server Auditing Policy (Get-AzSqlServerAudit)](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserveraudit)
- [Remove Database Auditing Policy (Remove-AzSqlDatabaseAudit)](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [Remove Server Auditing Policy (Remove-AzSqlServerAudit)](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserveraudit)

A parancsfájl példa: [PowerShell-lel, naplózás és fenyegetésészlelés konfigurálása](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a id="subheading-9"></a>Kezelheti az SQL database naplózási REST API használatával

**REST API**:

- [Adatbázis naplózási szabályzatának frissítése](https://docs.microsoft.com/rest/api/sql/database%20auditing%20settings/createorupdate)
- [Hozzon létre vagy naplózási házirend-kiszolgáló frissítése](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Adatbázis naplózási szabályzatának beolvasása](https://docs.microsoft.com/rest/api/sql/database%20auditing%20settings/get)
- [Kiszolgáló naplózási szabályzat lekérése](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/get)

AHOL záradékot támogatja a további szűréshez a kiterjesztett házirend:

- [Adatbázis frissítése *kiterjesztett* naplózási házirend](https://docs.microsoft.com/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [Hozzon létre vagy frissítési kiszolgáló *kiterjesztett* naplózási házirend](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Adatbázis beolvasása *kiterjesztett* naplózási házirend](https://docs.microsoft.com/rest/api/sql/database%20extended%20auditing%20settings/get)
- [Első kiszolgáló *kiterjesztett* naplózási házirend](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/get)

## <a id="subheading-10"></a>Kezelheti az SQL database naplózási ARM-sablonok használatával

Az Azure SQL database naplózási használata kezelheti [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) sablonok, az alábbi példákban szemléltetett módon:

- [Egy Azure SQL Server-naplózás engedélyezve van a vizsgálati naplók írni az Azure Blob storage-fiók üzembe helyezése](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Egy Azure SQL Server-naplózás engedélyezve van a vizsgálati naplók írni a Log Analytics üzembe helyezése](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Egy Azure SQL Server-naplózás engedélyezve van a vizsgálati naplók írni az Event Hubs üzembe helyezése](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> A csatolt mintákat egy külső nyilvános tárházban, és vannak megadva a ",", nélkül garancia, és bármely Microsoft támogatási program vagy szolgáltatás nem támogatja.

<!--Anchors-->
[Azure SQL Database Auditing overview]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Manage SQL database auditing using Azure PowerShell]: #subheading-7
[Blob/Table differences in Server auditing policy inheritance]: (#subheading-8)
[Manage SQL database auditing using REST API]: #subheading-9
[Manage SQL database auditing using ARM templates]: #subheading-10

<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_storage_key_regeneration.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_regenerate_key.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_blob_audit_records.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_ssms_1.png
[10]: ./media/sql-database-auditing-get-started/10_auditing_get_started_ssms_2.png

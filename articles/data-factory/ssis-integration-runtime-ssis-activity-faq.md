---
title: A csomagok végrehajtásának megoldása a SSIS Integration Runtime-ban | Microsoft Docs
description: Ez a cikk a SSIS-csomagok végrehajtásához nyújt hibaelhárítási útmutatót a SSIS Integration Runtime-ban
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/15/2019
author: wenjiefu
ms.author: wenjiefu
ms.reviewer: sawinark
manager: craigg
ms.openlocfilehash: a7ad0f3be754029c654b04d19750aab7bbcd210d
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933636"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>A csomagok végrehajtásának megoldása a SSIS Integration Runtime-ban

Ez a cikk a SQL Server Integration Services-(SSIS-) csomagok a SSIS Integration Runtime-ban való végrehajtásakor előforduló leggyakoribb hibákat tartalmazza. Ismerteti a hibák megoldásának lehetséges okait és műveleteit.

## <a name="where-to-find-logs-for-troubleshooting"></a>Hol találhatók a hibaelhárítási naplók

A SSIS-csomag végrehajtási tevékenység kimenetének vizsgálatához használja a Azure Data Factory portált. A kimenet tartalmazza a végrehajtás eredményét, a hibaüzeneteket és a művelet AZONOSÍTÓját. Részletekért lásd: [a folyamat figyelése](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline).

A SSIS-katalógus (SSISDB) segítségével keresse meg a végrehajtás részletes naplóit. Részletekért lásd: [futó csomagok és egyéb műveletek figyelése](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017).

## <a name="common-errors-causes-and-solutions"></a>Gyakori hibák, okok és megoldások

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>Hibaüzenet: "A kapcsolat időtúllépése lejárt" vagy "a szolgáltatás hibát észlelt a kérelem feldolgozásakor. Próbálkozzon újra. "

Íme a lehetséges okok és a javasolt műveletek:
* Az adatforrás vagy a cél túl van terhelve. Ellenőrizze az adatforrás vagy a cél terhelését, és nézze meg, hogy rendelkezik-e elegendő kapacitással. Ha például Azure SQL Database használ, érdemes lehet felskálázást végezni, ha az adatbázis valószínűleg időtúllépést mutat.
* A SSIS Integration Runtime és az adatforrás vagy a cél közötti hálózat instabil, különösen akkor, ha a kapcsolat régiók közötti, illetve a helyszíni és az Azure között van. Alkalmazza az újrapróbálkozási mintát a SSIS csomagban a következő lépések végrehajtásával:
  * Győződjön meg arról, hogy a SSIS-csomagok a meghibásodások nélkül is újra leállnak (például adatvesztés vagy az adatismétlődés).
  * Konfigurálja az újrapróbálkozási és újrapróbálkozási időközt az **SSIS-csomag végrehajtása** tevékenységre az **általános** lapon. ![Tulajdonságok beállítása az Általános lapon](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * Egy ADO.NET és egy OLE DB forrás vagy cél összetevő esetében állítsa be a **ConnectRetryCount** és a **CONNECTRETRYINTERVAL** elemet a SSIS-csomag vagy a SSIS tevékenység Csatlakozáskezelő területén.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>Hibaüzenet: "Az ADO NET Source nem tudta megkérni a kapcsolódást..." "hálózati vagy példány-specifikus hiba történt a SQL Server kapcsolatának létrehozásakor. A kiszolgáló nem található vagy nem érhető el. "

Ez a probléma általában azt jelenti, hogy az adatforrás vagy a cél nem érhető el az SSIS integrációs modulból. Ennek számos oka lehet. Próbálkozzon a következő műveletekkel:
* Győződjön meg arról, hogy az adatforrás vagy a cél neve/IP-címe megfelelően van átadva.
* Győződjön meg arról, hogy a tűzfal megfelelően van beállítva.
* Győződjön meg arról, hogy a virtuális hálózat megfelelően van konfigurálva, ha az adatforrás vagy a célhely a helyszínen van:
  * Megtekintheti, hogy a probléma a virtuális hálózat konfigurációjától származik-e, ha egy Azure-beli virtuális GÉPET ugyanabban a virtuális hálózaton helyez üzembe. Ezután győződjön meg arról, hogy az adatforrás vagy a cél elérhető az Azure-beli virtuális gépről.
  * A virtuális hálózatok SSIS Integration Runtime használatával történő használatáról további részleteket az [Azure-SSIS integrációs modul csatlakoztatása egy virtuális hálózathoz](join-azure-ssis-integration-runtime-virtual-network.md)című részt is talál.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>Hibaüzenet: "Az ADO NET Source nem tudta megkérni a kapcsolódást..." a "nem sikerült létrehozni a felügyelt Csatlakozáskezelőt".

Ennek lehetséges oka, hogy a csomagban használt ADO.NET-szolgáltató nincs telepítve a SSIS Integration Runtime-ban. A szolgáltatót egyéni telepítéssel is telepítheti. Az egyéni beállításokkal kapcsolatos további információkat [Az Azure-SSIS integrációs moduljának beállítások testreszabása](how-to-configure-azure-ssis-ir-custom-setup.md)címűában talál.

### <a name="error-message-the-connection--is-not-found"></a>Hibaüzenet: "A kapcsolatok"... " nem található "

Az SQL Server Management Studio (SSMS) régebbi verzióinak egy ismert hibája okozhatja ezt a hibát. Ha a csomag olyan egyéni összetevőt tartalmaz (például SSIS Azure Feature Pack vagy partnerösszetevők), amely nincs telepítve azon a gépen, amelyen az SSMS-t az üzembe helyezéshez használják, az SSMS eltávolítja az összetevőt, és hibát okoz. Frissítse a [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) a legújabb verzióra, amelyen a probléma kijavítva van.

### <a name="error-messagessis-executor-exit-code--1073741819"></a>Hibaüzenet: "SSIS végrehajtó kilépési kód:-1073741819."

* Lehetséges ok & javasolt művelet:
  * Ennek a hibának az az oka lehet, hogy az Excel-forrás és a célhely korlátozása, ha több Excel-forrás vagy cél fut párhuzamosan a több szálon. Ezt a korlátozást megkerülheti, ha módosítja az Excel-összetevőket, hogy azok sorba legyenek állítva, vagy elkülönítse őket különböző csomagokra, és a "csomag végrehajtása" művelettel aktiválja a ExecuteOutOfProcess tulajdonságot True értékre.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>Hibaüzenet: "Nincs elég hely a lemezen"

Ez a hiba azt jelenti, hogy a helyi lemez a SSIS Integration Runtime csomópontjában van használatban. Győződjön meg arról, hogy a csomag vagy az egyéni telepítő sok lemezterületet igényel:
* Ha a csomag felhasználja a lemezt, a csomag futtatása után fel lesz szabadítva.
* Ha a lemezt az egyéni telepítés felhasználja, le kell állítania a SSIS integrációs modult, módosítania kell a parancsfájlt, és újra kell indítania az integrációs modult. Az egyéni telepítéshez megadott teljes Azure BLOB-tárolót a rendszer átmásolja a SSIS Integration Runtime csomópontba, ezért győződjön meg arról, hogy az adott tárolóban felesleges tartalom van-e.

### <a name="error-message-failed-to-retrieve-resource-from-master-microsoftsqlserverintegrationservicesscalescaleoutcontractcommonmasterresponsefailedexception-code300004-descriptionload-file--failed"></a>Hibaüzenet: "Nem sikerült beolvasni az erőforrást a főkiszolgálóról. Microsoft.SqlServer.IntegrationServices.Scale.ScaleoutContract.Common.MasterResponseFailedException: Code:300004. Leírás: a (z) "* * *" fájl betöltése sikertelen. "

* Lehetséges ok & javasolt művelet:
  * Ha a SSIS tevékenység a csomagot fájlrendszerből (csomagfájl vagy projektfájl) hajtja végre, akkor ez a hiba akkor fordul elő, ha a projekt, csomag vagy konfigurációs fájl nem érhető el a SSIS tevékenységben megadott csomag-hozzáférési hitelesítő adatokkal.
    * Ha az Azure file-t használja:
      * A fájl elérési útjának \\a \<Storage-\>fiók nevével kell kezdődnie \\. file.Core.Windows.net\\\<-fájlmegosztás elérési útja\>
      * A tartománynak "Azure"-nek kell lennie
      * A felhasználónévnek a \<Storage-fiók nevét kell tartalmaznia\>
      * A jelszónak tárolási \<hozzáférési kulcsnak kell lennie\>
    * Ha a helyszíni fájlt használ, ellenőrizze, hogy a VNet, a csomag hozzáférési hitelesítő adatai és az engedélyek megfelelően vannak-e konfigurálva, hogy az Azure-SSIS Integration Runtime hozzáférhessen a helyszíni fájlmegosztás eléréséhez.

### <a name="error-message-the-file-name--specified-in-the-connection-was-not-valid"></a>Hibaüzenet: "A fájl neve"... " a kapcsolatban megadott érték érvénytelen.

* Lehetséges ok & javasolt művelet:
  * A megadott fájlnév érvénytelen.
  * Győződjön meg arról, hogy a Csatlakozáskezelőt (teljes tartománynevet) használja rövid idő helyett a Csatlakozáskezelő programban

### <a name="error-message-cannot-open-file-"></a>Hibaüzenet: "A fájl nem nyitható meg..."

Ez a hiba akkor fordul elő, ha a csomag végrehajtása nem talál fájlt a helyi lemezen a SSIS Integration Runtime-ban. Próbálkozzon a következő műveletekkel:
* Ne használja az abszolút elérési utat a SSIS Integration Runtime-ban végrehajtandó csomagban. A jelenlegi végrehajtási munkakönyvtár (.) vagy a temp mappa (% TEMP%) használata helyett.
* Ha néhány fájlt meg kell őriznie a SSIS Integration Runtime csomópontjain, készítse elő a fájlokat a [konfiguráció testreszabása](how-to-configure-azure-ssis-ir-custom-setup.md)című részben leírtak szerint. A rendszer a munkakönyvtárban lévő összes fájlt törli a végrehajtás befejezése után.
* A SSIS Integration Runtime csomópontjában a fájl tárolása helyett használja a Azure Files. Részletekért lásd: [Az Azure-fájlmegosztás használata](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares).

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>Hibaüzenet: "A" SSISDB adatbázis elérte a méretre vonatkozó kvótát "

Ennek lehetséges oka, hogy az Azure SQL-adatbázisban létrehozott SSISDB adatbázis vagy egy felügyelt példány az SSIS integrációs modul létrehozásakor elérte a kvótáját. Próbálkozzon a következő műveletekkel:
* Fontolja meg az adatbázis DTU-jának növelését. Ezzel kapcsolatban az [Azure SQL Database-kiszolgálóra vonatkozó SQL Database-erőforráskorlátokat ismertető](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server) cikkben talál további információt.
* Ellenőrizze, hogy a csomag sok naplót fog-e létrehozni. Ha igen, egy rugalmas feladatot is beállíthat ezen naplók törléséhez. További részletekért tekintse meg az [SSISDB-naplók törlése Azure-beli rugalmas adatbázis-feladatokkal](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md) című cikket.

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>Hibaüzenet: "Az adatbázisra vonatkozó kérelem korlátja... és elérte a következőt:. "

Ha több csomag fut párhuzamosan a SSIS Integration Runtime-ban, akkor ez a hiba akkor fordulhat elő, ha a SSISDB elérte a kérelem korlátját. A probléma megoldásához érdemes lehet növelni a SSISDB DTC-t. Ezzel kapcsolatban az [Azure SQL Database-kiszolgálóra vonatkozó SQL Database-erőforráskorlátokat ismertető](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server) cikkben talál további információt.

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>Hibaüzenet: "A SSIS művelet nem sikerült – váratlan műveleti állapot:..."

A hibát többnyire átmeneti probléma okozta, ezért próbálkozzon újra a csomag végrehajtásával. Alkalmazza az újrapróbálkozási mintát a SSIS csomagban a következő lépések végrehajtásával:

* Győződjön meg arról, hogy a SSIS-csomagok a meghibásodások nélkül is újra leállnak (például adatvesztés vagy az adatismétlődés).
* Konfigurálja az újrapróbálkozási és újrapróbálkozási időközt az **SSIS-csomag végrehajtása** tevékenységre az **általános** lapon. ![Tulajdonságok beállítása az Általános lapon](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* Egy ADO.NET és egy OLE DB forrás vagy cél összetevő esetében állítsa be a **ConnectRetryCount** és a **CONNECTRETRYINTERVAL** elemet a SSIS-csomag vagy a SSIS tevékenység Csatlakozáskezelő területén.

### <a name="error-message-there-is-no-active-worker"></a>Hibaüzenet: "Nincs aktív dolgozó."

Ez a hiba általában azt jelenti, hogy a SSIS Integration Runtime állapota nem kifogástalan. Az állapotot és a részletes hibákat a Azure Portalban találja. További információ: [Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime).

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>Hibaüzenet: "Az integrációs modul nem frissíthető, és végül nem fog működni, mert nem fér hozzá az egyéni telepítéshez megadott Azure Blob-tárolóhoz."

Ez a hiba akkor fordul elő, ha a SSIS Integration Runtime nem fér hozzá az egyéni telepítéshez konfigurált tárterülethez. Győződjön meg arról, hogy a megadott megosztott elérésű aláírás (SAS) URI-ja érvényes, és nem járt-e le.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>Hibaüzenet: "Microsoft OLE DB-szolgáltató Analysis Serviceshoz. HRESULT 0x80004005 leírása: " COM-hiba: COM-hiba: mscorlib; A kivételt a hívás célja okozta.

Az egyik lehetséges ok az, hogy az Azure multi-Factor Authentication szolgáltatással engedélyezett Felhasználónév vagy jelszó Azure Analysis Services hitelesítésre van konfigurálva. Ez a hitelesítés nem támogatott a SSIS Integration Runtime-ban. Próbáljon meg egy egyszerű szolgáltatásnevet használni Azure Analysis Services hitelesítéshez:
1. Készítsen elő egy egyszerű szolgáltatást az [automatizálás az egyszerű szolgáltatásokkal](https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal)című témakörben leírtak szerint.
2. A Csatlakozáskezelőben konfigurálja a **megadott Felhasználónév és jelszó használata**: **AppID** beállítása felhasználónévként és **clientSecret** jelszóként.

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>Hibaüzenet: "A ADONET forrásának nem sikerült megvásárolnia a (z) {GUID} nevű kapcsolódási üzenetet a következő hibaüzenettel: Felügyelt identitás használata esetén sikertelen volt a bejelentkezés a (z) "NT AUTHORITY \ névtelen LOGON" felhasználónál

Győződjön meg arról, hogy nem konfigurálja a Csatlakozáskezelő hitelesítési módszerét **Active Directory jelszó** -hitelesítésre, ha a *ConnectUsingManagedIdentity* paraméter értéke **true (igaz**). Ehelyett **SQL** -hitelesítésként is konfigurálható, amelyet a rendszer figyelmen kívül hagy, ha a *ConnectUsingManagedIdentity* be van állítva.

### <a name="multiple-package-executions-are-triggered-unexpectedly"></a>A csomagok végrehajtásának végrehajtása váratlanul megtörténik

* Lehetséges ok & javasolt művelet:
  * Az ADF tárolt eljárása tevékenység a SSIS-csomagok végrehajtásának elindítására szolgál. Előfordulhat, hogy a t-SQL-parancs átmeneti problémát észlelt, és elindítja az újrafuttatást, ami több csomag futtatását okozhatja.
  * Használja inkább a ExecuteSSISPackage tevékenységet, amely biztosítja, hogy a csomagok végrehajtása ne fusson újra, kivéve, ha a felhasználói újrapróbálkozások száma tevékenységben A részletek a következő címen olvashatók:[https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)

### <a name="package-execution-takes-too-long"></a>A csomag végrehajtása túl hosszú időt vesz igénybe

Íme a lehetséges okok és a javasolt műveletek:

* Túl sok csomag-végrehajtás van ütemezve a SSIS Integration Runtime-ban. Az összes ilyen végrehajtás várólistára várakozik a turnön.
  * Határozza meg a maximális értéket a következő képlet használatával:

    Párhuzamos végrehajtások maximális száma/IR = csomópontok száma * maximális párhuzamos végrehajtás/csomópont
  * Ha szeretné megtudni, hogyan állíthatja be a csomópontok számát és a párhuzamos végrehajtást a csomópontokon, tekintse meg az [Azure-SSIS integrációs modul létrehozása a Azure Data Factoryban](create-azure-ssis-integration-runtime.md)című témakört.
* A SSIS Integration Runtime leáll vagy sérült állapotú. Az SSIS Integration Runtime állapotának és hibáinak vizsgálatával kapcsolatban lásd: [Azure-SSIS Integration Runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime).

Azt is javasoljuk, hogy az **általános** lapon állítsa be az időtúllépést: ![Az Általános lapon](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)adja meg a tulajdonságokat.

### <a name="poor-performance-in-package-execution"></a>Gyenge teljesítmény a csomagok végrehajtásában

Próbálkozzon a következő műveletekkel:

* Győződjön meg arról, hogy a SSIS Integration Runtime ugyanabban a régióban található, mint az adatforrás és a cél.

* Állítsa be a csomag végrehajtásának naplózási szintjét a **teljesítmény** értékre a végrehajtásban lévő egyes összetevők időtartamának összegyűjtéséhez. Részletekért lásd: [Integration Services (SSIS) naplózása](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging).

* A Azure Portal az IR-csomópont teljesítményének ellenőrzését:
  * Az SSIS Integration Runtime figyelésével kapcsolatos további információkért lásd: [Azure-SSIS Integration Runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime).
  * A SSIS Integration Runtime CPU-/memória-előzményeit a Azure Portal adat-előállító metrikáinak megtekintésével találja.
    ![A SSIS Integration Runtime metrikáinak monitorozása](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)

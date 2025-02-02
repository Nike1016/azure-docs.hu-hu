---
title: Saját üzemeltetésű integrációs modul konfigurálása a SSIS-ben a Azure Data Factory-ben | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat saját üzemeltetésű Integration Runtime proxyként az Azure-SSIS Integration Runtimehoz.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 2f65303cd5cda50a95c3563422c059b985ecf28a
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2019
ms.locfileid: "68737562"
---
# <a name="configure-self-hosted-ir-as-a-proxy-for-azure-ssis-ir-in-adf"></a>Saját üzemeltetésű IR beállítása proxyként az Azure-SSIS IR-hez az ADF-ben
Ez a cikk azt ismerteti, hogyan futtathatók a SQL Server Integration Services (SSIS) csomagok az Azure-SSIS Integration Runtime (IR)-ben Azure Data Factory (ADF)-ben, a saját üzemeltetésű IR proxyként konfigurálva.  Ez a funkció lehetővé teszi, hogy az [Azure-SSIS IR-t egy virtuális hálózathoz való csatlakozás](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)nélkül hozzáférhessen a helyszíni adataihoz.  Ez akkor hasznos, ha a vállalati hálózat túlságosan összetett konfigurációval/korlátozó házirenddel rendelkezik az Azure-SSIS integrációs modul behelyezéséhez.

Ez a szolgáltatás a helyszíni adatforrással rendelkező adatáramlási feladatot tartalmazó csomagot két előkészítési feladatba osztja szét: a saját üzemeltetésű integrációs modul első futtatásakor először a helyszíni adatforrásból származó adatok kerülnek át az Azure-Blob Storage egy átmeneti területére, miközben Az Azure-SSIS integrációs modul második futtatása után az adatok áthelyezése az átmeneti területről a kívánt adatok célhelyére történik.

Ez a funkció más előnyöket és képességeket is biztosít, mivel lehetővé teszi a saját üzemeltetésű integrációs modul kiépítését az Azure-SSIS IR által még nem támogatott régiókban, lehetővé téve a saját üzemeltetésű IR nyilvános statikus IP-címének használatát az adatforrások tűzfalán, stb.

## <a name="prepare-self-hosted-ir"></a>Saját üzemeltetésű IR előkészítése
Ahhoz, hogy használhassa ezt a funkciót, először létre kell hoznia egy ADF-t, és az Azure-SSIS integrációs modult kell létrehoznia, ha még nem tette volna meg, kövesse az [Azure-SSIS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) integrációs modul üzembe helyezésével foglalkozó cikket.

Ezután létre kell hoznia a saját üzemeltetésű integrációs modult ugyanabban az ADF-ben, ahol az Azure-SSIS IR-t a [saját](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime) üzemeltetésű integrációs modul létrehozása című cikk alapján kell kiépíteni.

Végezetül le kell töltenie és telepítenie kell a saját üzemeltetésű integrációs modul legújabb verzióját, valamint a további illesztőprogramokat és futtatókörnyezetet a helyszíni gépen/Azure-beli virtuális gépen (VM) a következőképpen:
- Töltse le és telepítse a saját üzemeltetésű IR legújabb verzióját innen. [](https://www.microsoft.com/download/details.aspx?id=39717)
- Ha OLEDB-összekötőket használ a csomagokban, töltse le és telepítse a megfelelő OLEDB-illesztőprogramokat ugyanarra a gépre, ahol a saját üzemeltetésű IR telepítve van, ha még nem tette meg.  Ha az OLEDB-illesztőprogram korábbi verzióját használja a SQL Serverhoz (SQLNCLI), [innen](https://www.microsoft.com/download/details.aspx?id=50402)töltheti le a 64 bites verziót.  Ha az OLEDB-illesztőprogram legújabb verzióját használja a SQL Serverhoz (MSOLEDBSQL), [innen](https://www.microsoft.com/download/details.aspx?id=56730)töltheti le a 64 bites verziót.  Ha az OLEDB-illesztőprogramokat más adatbázis-rendszerekhez, például a PostgreSQL-hez, a MySQL-hez, az Oracle-hoz stb. használja, letöltheti a 64 bites verziót a saját webhelyeiről.
- Töltse le és telepítse a C++ Visual (VC) futtatókörnyezetet ugyanarra a gépre, ahol a saját üzemeltetésű IR telepítve van, ha még nem tette meg.  Az 64 bites verziót innen töltheti le. [](https://www.microsoft.com/download/details.aspx?id=40784)

## <a name="prepare-azure-blob-storage-linked-service-for-staging"></a>Az Azure Blob Storage társított szolgáltatás előkészítése átmeneti használatra
Hozzon létre egy Azure Blob Storage társított szolgáltatást ugyanabban az ADF-ben, ahol az Azure-SSIS IR-t kiosztották, ha még nem tette meg, kövesse az ADF-hez [társított szolgáltatás létrehozásával kapcsolatos](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service) cikket.  Ellenőrizze a következőket:
- Az **Azure Blob Storage** van kiválasztva az **adattárhoz**
- Az integrációs modulon **keresztüli csatlakozás** **AutoResolveIntegrationRuntime** van kiválasztva
- Vagy a **fiók kulcs**/**sas URI**/**egyszerű szolgáltatásnév** van kiválasztva a **hitelesítési módszerhez**

![Az Azure Blob Storage társított szolgáltatás előkészítése átmeneti használatra](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-azure-ssis-ir-with-self-hosted-ir-as-a-proxy"></a>Az Azure-SSIS IR konfigurálása saját üzemeltetésű IR proxyként
A saját üzemeltetésű IR-és Azure Blob Storage társított szolgáltatás előkészítésének előkészítésével mostantól konfigurálhatja az új/meglévő Azure-SSIS IR-t saját üzemeltetésű integrációs modulként az ADF-portálon/alkalmazáson keresztül.  Ha a meglévő Azure-SSIS IR-t futtatja, állítsa le, mielőtt ezt megtenné, majd indítsa újra.

A **Speciális beállítások** lapon jelölje be a saját üzemeltetésű **Integration Runtime beállítása proxyként az Azure-SSIS Integration Runtime** jelölőnégyzetet, válassza ki a saját üzemeltetésű IR és Azure Blob Storage társított szolgáltatást az előkészítéshez, és adjon meg egy blobot. az előkészítési **útvonalhoz** tartozó tároló neve, ha szeretné.

![Az Azure-SSIS IR konfigurálása saját üzemeltetésű IR proxyként](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-settings-ssisir.png)

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>SSIS-csomagok engedélyezése proxy alapján való csatlakozásra
A SSIS projects bővítménnyel a Visual studióhoz készült legújabb SSDT használatával letölthető innen vagy [](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) önálló telepítőként, amely innen tölthető le: az OLEDB [](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)-ben hozzáadott új **ConnectByProxy** tulajdonság. A Flat file-kapcsolatok kezelői.  

Amikor az OLEDB/Flat file sources használatával olyan új csomagokat tervez, amelyekben az adatbázisok/fájlok elérhetők a helyszíni adatbázisokhoz/fájlokhoz, ezt a tulajdonságot a megfelelő Csatlakozáskezelő tulajdonságok paneljének **true (igaz** ) értékre állításával engedélyezheti.

![ConnectByProxy tulajdonság engedélyezése](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

Ezt a tulajdonságot akkor is engedélyezheti, ha már meglévő csomagokat futtat, és nem kell manuálisan módosítania őket.  2 lehetőség közül választhat:
- A csomagokat tartalmazó projekt megnyitása, újraépítése és újbóli üzembe helyezése a legújabb SSDT az Azure-SSIS IR-ben való futtatáshoz: A tulajdonság ezután engedélyezhető úgy, hogy az **igaz** értékre van állítva a megfelelő ügyfélkapcsolat-kezelők számára, amelyek megjelennek a csomag előugró ablakban a SSMS származó csomagok futtatásakor.

  ![ConnectByProxy property2 engedélyezése](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  A tulajdonságot engedélyezheti úgy is, hogy **igaz** értékre állítja a [SSIS-csomag végrehajtásakor](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) a **kapcsolatkezelő** lapon megjelenő kapcsolódó ügyfélkapcsolat-kezelők számára a csomagok az ADF-folyamatokban való futtatásakor.
  
  ![ConnectByProxy property3 engedélyezése](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- A csomagokat tartalmazó projekt újbóli üzembe helyezése az SSIS IR-ben való futtatáshoz: Ezt követően a tulajdonságot engedélyezheti a tulajdonság elérési útjának `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`megadásával, és beállíthatja, hogy a tulajdonság felülbírálja a csomag előugró ablakának **speciális** lapján a csomagok SSMS való futtatásakor.

  ![ConnectByProxy property4 engedélyezése](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  A tulajdonságot a tulajdonság elérési útjának `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`megadásával is engedélyezheti, és a tulajdonság felülbírálását a tulajdonságok felülbírálása a [SSIS-csomag végrehajtása tevékenységben](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) a csomagok ADF-folyamatokban való futtatásakor.
  
  ![ConnectByProxy property5 engedélyezése](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>Az első és a második előkészítési feladat hibakeresése
A saját üzemeltetésű integrációs modulban `C:\ProgramData\SSISTelemetry` megtalálhatja a futásidejű naplókat a mappában, valamint az első előkészítési `C:\ProgramData\SSISTelemetry\ExecutionLog` feladatok végrehajtási naplóit a mappában.  A második előkészítési feladatok végrehajtási naplói a SSISDB vagy a megadott naplózási elérési utakon találhatók, attól függően, hogy a csomagokat a SSISDB, illetve a fájlrendszer/fájlmegosztás/Azure Filesban tárolja-e a rendszer.  Az első előkészítési feladatok egyedi azonosítói is megtalálhatók a második előkészítési feladatok végrehajtási naplóiban, például: 

![Az első előkészítési feladat egyedi azonosítója](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="billing-for-the-first-and-second-staging-tasks"></a>Az első és a második előkészítési feladat számlázása
A saját üzemeltetésű integrációs modulon futó első előkészítési feladatok számlázása ugyanúgy történik, mint a saját üzemeltetésű integrációs modulon futó adatáthelyezési tevékenységek számlázása az ADF-adatcsatorna [díjszabási](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) cikkében megadott módon.

Az Azure-SSIS IR-ben futó második előkészítési feladat nem kerül külön számlázásra, de a futtatott Azure-SSIS IR-t az [Azure-SSIS IR árképzési](https://azure.microsoft.com/pricing/details/data-factory/ssis/) cikkében megadott módon számítjuk fel.

## <a name="current-limitations"></a>Aktuális korlátozások
- Jelenleg csak az OLEDB/Flat file-kapcsolatok kezelői és az OLEDB/Flat file-források támogatottak.
- Jelenleg csak az Azure Blob Storage társított szolgáltatások vannak konfigurálva a **fiók kulcs**/**sas URI**/-hitelesítésével.
- Jelenleg csak a saját üzemeltetésű IR-t kell kiépíteni ugyanabban az ADF-ben, ahol az Azure-SSIS IR-t kiosztották.

## <a name="next-steps"></a>További lépések
Miután konfigurálta a saját üzemeltetésű integrációs modult az Azure-SSIS IR-hez, telepítheti és futtathatja a csomagokat, hogy a helyszínen lévő adataihoz hozzáférjenek az ADF-folyamatok végrehajtási SSIS [ ](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).
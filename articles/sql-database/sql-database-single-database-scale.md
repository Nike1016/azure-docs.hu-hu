---
title: Önálló adatbázis-erőforrások méretezése – Azure SQL Database | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan méretezhetők a számítási és tárolási erőforrások a Azure SQL Database önálló adatbázisai számára.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/26/2019
ms.openlocfilehash: e03c68854d9150c25019fe198fe855a011750844
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566551"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Önálló adatbázis-erőforrások méretezése Azure SQL Database

Ez a cikk azt ismerteti, hogyan méretezheti a számítási és tárolási erőforrásokat a kiépített számítási szinten található önálló adatbázisokhoz. Azt is megteheti, hogy a [kiszolgáló nélküli (előzetes verzió) számítási réteg](sql-database-serverless.md) a számítási feladatokhoz másodpercenként számítási automatikus skálázást és számlákat használ.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

## <a name="change-compute-size-vcores-or-dtus"></a>Számítási méret módosítása (virtuális mag vagy DTU)

A virtuális mag vagy a DTU számának kezdeti kiválasztását követően a [Azure Portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), a [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), a [PowerShell](/powershell/module/az.sql/set-azsqldatabase), az [Azure CLI](/cli/azure/sql/db#az-sql-db-update)vagy a REST API használatával dinamikusan méretezheti az önálló adatbázisokat a tényleges tapasztalatok alapján. [ ](https://docs.microsoft.com/rest/api/sql/databases/update).

A következő videó bemutatja, hogyan lehet dinamikusan módosítani a szolgáltatási szintet és a számítási méretet, hogy az elérhető DTU egyetlen adatbázishoz lehessen emelni.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

> [!IMPORTANT]
> Bizonyos körülmények között szükség lehet az adatbázis nem használt terület felszabadítását zsugorítani. További információkért lásd: [kezelése az Azure SQL Database területe](sql-database-file-space-management.md).

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>A szolgáltatási réteg módosításának vagy a számítási méret átméretezésének a következményei

Egy adott adatbázis szolgáltatási rétegének vagy számítási méretének módosítása elsősorban a következő lépéseket hajtja végre:

1. Új számítási példány létrehozása az adatbázishoz  

    A rendszer létrehoz egy új számítási példányt az adatbázishoz a kért szolgáltatási szintű és számítási mérettel. A szolgáltatási szintek és a számítási méretek bizonyos kombinációinak változása esetén az adatbázis replikáját az új számítási példányban kell létrehozni, amely az adatok másolását és a teljes késést is jelentősen befolyásolhatja. Függetlenül attól, hogy az adatbázis online állapotban marad ebben a lépésben, és a kapcsolatok továbbra is az eredeti számítási példány adatbázisára lesznek irányítva.

2. Az új számítási példánnyal létesített kapcsolatok útválasztásának váltása

    A rendszer eldobott egy meglévő kapcsolatot az adatbázissal az eredeti számítási példányban. Minden új kapcsolat létrejön az adatbázishoz az új számítási példányban. A szolgáltatási szintek és a számítási méret változásainak bizonyos kombinációi esetén az adatbázisfájlok le lesznek választva, és a kapcsoló során újra vannak csatolva.  Függetlenül attól, hogy a kapcsoló egy rövid szolgáltatási megszakítást eredményezhet, ha az adatbázis nem érhető el, és általában kevesebb, mint 30 másodpercig, és gyakran csak néhány másodpercig tart. Ha a kapcsolatok eldobásakor hosszú ideig futó tranzakciók futnak, a lépés időtartama a megszakított tranzakciók helyreállításához hosszabb időt vehet igénybe. A [gyorsított adatbázis-helyreállítás](sql-database-accelerated-database-recovery.md) csökkentheti a hosszan futó tranzakciók megszakításának hatását.

> [!IMPORTANT]
> A munkafolyamat egyik lépése sem vesz fel adatvesztést.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>A szolgáltatási réteg módosításának késése vagy a számítási méret átméretezése

A szolgáltatási réteg módosításának becsült késése vagy egy önálló adatbázis vagy rugalmas készlet számítási méretének átméretezése a következő paraméterekkel történik:

|Szolgáltatási szint|Alapszintű önálló adatbázis,</br>Standard (S0-S1)|Alapszintű rugalmas készlet,</br>Standard (S2-S12), </br>Nagy kapacitású </br>Önálló adatbázis vagy rugalmas készlet általános célú|Prémium vagy üzletileg kritikus önálló adatbázis vagy rugalmas készlet|
|:---|:---|:---|:---|
|**Alapszintű önálló</br> adatbázis, Standard (S0-S1)**|&bull;&nbsp;Állandó időbeli késés a felhasznált területtől függetlenül</br>&bull;&nbsp;Általában kevesebb, mint 5 perc|&bull;&nbsp;Az Adatmásolás miatt használt adatbázis-területtel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB felhasznált lemezterület|&bull;&nbsp;Az Adatmásolás miatt használt adatbázis-területtel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB felhasznált lemezterület|
|**Alapszintű rugalmas </br>készlet, Standard (S2-S12 </br>), </br>nagy kapacitású, általános célú önálló adatbázis vagy rugalmas készlet**|&bull;&nbsp;Az Adatmásolás miatt használt adatbázis-területtel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB felhasznált lemezterület|&bull;&nbsp;Állandó időbeli késés a felhasznált területtől függetlenül</br>&bull;&nbsp;Általában kevesebb, mint 5 perc|&bull;&nbsp;Az Adatmásolás miatt használt adatbázis-területtel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB felhasznált lemezterület|
|**Prémium vagy üzletileg kritikus önálló adatbázis vagy rugalmas készlet**|&bull;&nbsp;Az Adatmásolás miatt használt adatbázis-területtel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB felhasznált lemezterület|&bull;&nbsp;Az Adatmásolás miatt használt adatbázis-területtel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB felhasznált lemezterület|&bull;&nbsp;Az Adatmásolás miatt használt adatbázis-területtel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB felhasznált lemezterület|

> [!TIP]
> A folyamatban lévő műveletek figyeléséhez lásd: [Az SQL REST API használatával kezelheti a](https://docs.microsoft.com/rest/api/sql/operations/list)műveleteket, kezelheti a [parancssori](/cli/azure/sql/db/op)felületeket, figyelheti a [műveleteket a T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) és a két PowerShell-parancs használatával: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) and [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="cancelling-service-tier-changes-or-compute-rescaling-operations"></a>Szolgáltatási szintek módosításainak vagy számítási Felskálázási műveleteinek megszakítása

A szolgáltatási szintek változási vagy számítási átméretezési művelete megszakítható.

#### <a name="azure-portal"></a>Azure Portal

Az adatbázis-Áttekintés panelen navigáljon az **értesítésekhez** , és kattintson a csempére, amely azt jelzi, hogy folyamatban van egy művelet:

![Folyamatban lévő művelet](media/sql-database-single-database-scale/ongoing-operations.png)

Ezután kattintson a **művelet megszakítása**feliratú gombra.

![Folyamatban lévő művelet megszakítása](media/sql-database-single-database-scale/cancel-ongoing-operation.png)

#### <a name="powershell"></a>PowerShell

A PowerShell-parancssorból állítsa be a `$ResourceGroupName`, `$ServerName`a és `$DatabaseName`a parancsot, majd futtassa a következő parancsot:

```PowerShell
$OperationName = (az sql db op list --resource-group $ResourceGroupName --server $ServerName --database $DatabaseName --query "[?state=='InProgress'].name" --out tsv)
if(-not [string]::IsNullOrEmpty($OperationName))
    {
        (az sql db op cancel --resource-group $ResourceGroupName --server $ServerName --database $DatabaseName --name $OperationName)
        "Operation " + $OperationName + " has been canceled"
    }
    else
    {
        "No service tier change or compute rescaling operation found"
    }
```

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>A szolgáltatási réteg módosításának és a számítási méret átméretezésének további szempontjai

- Ha magasabb szolgáltatási szintre vagy számítási méretre frissít, az adatbázis maximális mérete nem növekszik, ha explicit módon nagyobb méretet (MaxSize) ad meg.
- Egy adatbázis visszalépéséhez az adatbázis felhasznált területének kisebbnek kell lennie, mint a cél szolgáltatási csomag maximálisan megengedett mérete és a számítási méret.
- Ha a **prémium** szintről a **standard** szintre vált, a rendszer extra tárterületet számít fel, ha mindkettő (1) az adatbázis maximális mérete támogatott a célként megadott számítási méretnél, és (2) a maximális méret meghaladja a cél számítási méretének belefoglalt tárolási mennyiségét. Ha például egy 500 GB-os maximális mérettel rendelkező P1-adatbázis az S3-as verzióra van lefoglalva, akkor a rendszer extra tárterületet biztosít, mivel az S3 támogatja a maximális 1 TB-os méretet, és a benne foglalt tárolási mennyiség csak 250 GB. Így a további tárterület mérete 500 GB – 250 GB = 250 GB. Az extra tárterület díjszabását lásd: [SQL Database díjszabása](https://azure.microsoft.com/pricing/details/sql-database/). Ha a felhasznált terület tényleges mennyisége kisebb, mint a foglalt tárterület, akkor ez az extra díj elkerülhető, ha az adatbázis maximális méretét a benne foglalt mennyiségre csökkenti.
- Ha a [geo-replikálást](sql-database-geo-replication-portal.md) engedélyező adatbázist frissít, frissítse a másodlagos adatbázisokat a kívánt szolgáltatási rétegre és számítási méretre, mielőtt frissítené az elsődleges adatbázist (általános útmutató a legjobb teljesítményhez). Ha más verzióra frissít, először a másodlagos adatbázist kell frissíteni.
- Ha a [geo-replikálást](sql-database-geo-replication-portal.md) engedélyező adatbázis visszaminősítése engedélyezve van, az elsődleges adatbázisokat a kívánt szolgáltatási rétegre és számítási méretre kell visszaminősíteni a másodlagos adatbázis visszaminősítése előtt (általános útmutató a legjobb teljesítményhez). Egy másik kiadásra való visszalépéskor először vissza kell állítani az elsődleges adatbázist.
- A visszaállítási szolgáltatásajánlatok eltérőek a különböző szolgáltatási szintek esetében. Ha az alapszintű **szintre van** leértékelve, a biztonsági mentés alacsonyabb megőrzési időszakot vesz fel. Lásd: [Azure SQL Database biztonsági mentések](sql-database-automated-backups.md).
- Az adatbázis új tulajdonságai csak akkor lesznek alkalmazva, ha a módosítások befejeződtek.

### <a name="billing-during-compute-rescaling"></a>Számlázás a számítási átméretezés során

Az adatbázis óránkénti számlázása az adott órában alkalmazott legmagasabb szolgáltatási réteg + számítási méret alapján történik, függetlenül a használattól, illetve attól, hogy az adatbázis egy óránál kevesebb ideig volt-e aktív. Ha például létrehoz egy adatbázist, és öt perccel később törli azt, akkor a számla egy adatbázis-órára vonatkozó díjat számít fel.

## <a name="change-storage-size"></a>Tárterület méretének módosítása

### <a name="vcore-based-purchasing-model"></a>Virtuálismag-alapú vásárlási modell

- A tárterület maximális mérete 1 GB-os növekmények használatával állítható be. A minimálisan konfigurálható adattárolás 5 GB
- Az önálló adatbázisok tárterületét a [Azure Portal](https://portal.azure.com), a [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), a [PowerShell](/powershell/module/az.sql/set-azsqldatabase), az [Azure CLI](/cli/azure/sql/db#az-sql-db-update)vagy a [REST API](https://docs.microsoft.com/rest/api/sql/databases/update)használatával lehet kiépíteni a maximális méret növelésével vagy csökkentésével.
- A SQL Database automatikusan lefoglalja a naplófájlok további tárhelyének 30%-át, a TempDB pedig virtuális mag 32GB-ot, de nem haladhatja meg a 384GB. A TempDB egy csatlakoztatott SSD-meghajtón található az összes szolgáltatási szinten.
- Az önálló adatbázisok tárterületének ára az adattárolás és a naplózási tárolók összege, a szolgáltatási réteg tárolási egységének díja. A TempDB díja a virtuális mag ár része. További információ az extra tárterület díjszabásáról: [SQL Database díjszabása](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Bizonyos körülmények között szükség lehet az adatbázis nem használt terület felszabadítását zsugorítani. További információkért lásd: [kezelése az Azure SQL Database területe](sql-database-file-space-management.md).

### <a name="dtu-based-purchasing-model"></a>DTU-alapú vásárlási modell

- Az önálló adatbázisok DTU ára bizonyos mennyiségű tárterületet foglal magában, többletköltség nélkül. A benne foglalt mennyiségen túli extra tárterület kiépíthető a maximális méretkorlát 250 GB-ig, 1 TB-ig, majd 256 GB-onként 1 TB-nál nagyobb mértékben. A foglalt tárolási összegek és a maximális méretkorlát [: önálló adatbázis: A tárolási méretek és a számítási](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)méretek.
- Az önálló adatbázisok extra tárterületének növeléséhez a Azure Portal, a [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), a [PowerShell](/powershell/module/az.sql/set-azsqldatabase), az [Azure CLI](/cli/azure/sql/db#az-sql-db-update)vagy a [REST API](https://docs.microsoft.com/rest/api/sql/databases/update)használatával növelheti a maximális méretet.
- Az önálló adatbázisok extra tárterületének díja a szolgáltatás rétegének extra tárolási egységével megszorzott extra tárterület. További információ az extra tárterület díjszabásáról: [SQL Database díjszabása](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Bizonyos körülmények között szükség lehet az adatbázis nem használt terület felszabadítását zsugorítani. További információkért lásd: [kezelése az Azure SQL Database területe](sql-database-file-space-management.md).

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>P11 és P15 megkötések, ha a maximális méret nagyobb, mint 1 TB

A prémium szinten több mint 1 TB tárterület érhető el az összes régióban, kivéve a következőket: Kelet-Kína, Észak-Kína, Közép-Németország, Északkelet-Németország, USA nyugati középső régiója, US DoD régiók és USA kormányzati központja. Ezekben a régiókban a prémium szinthez tartozó tárterület maximuma 1 TB. Az alábbi szempontok és korlátozások az 1 TB-nál nagyobb maximális mérettel rendelkező P11 és P15 adatbázisokra vonatkoznak:

- Ha egy P11 vagy P15-adatbázis maximális mérete 1 TB-nál nagyobb értékre lett beállítva, akkor csak visszaállítható vagy átmásolható egy P11 vagy P15-adatbázisba.  Ezt követően az adatbázis átméretezhető egy másik számítási méretre, amennyiben az átméretezési művelet idején lefoglalt terület mennyisége nem haladja meg az új számítási méret maximális méretét.
- Aktív földrajzi replikálási forgatókönyvek esetén:
  - Geo-replikációs kapcsolat beállítása: Ha az elsődleges adatbázis P11 vagy P15, akkor a másodlagos (IES) P11 vagy P15 is kell lennie. az alacsonyabb számítási méret elutasítása formátumú másodlagos zónák történik, mivel nem képesek 1 TB-nál nagyobb mértékben támogatni.
  - Az elsődleges adatbázis frissítése földrajzi replikálási kapcsolatban: Ha az elsődleges adatbázison több mint 1 TB-ra módosítja a maximális méretet, a másodlagos adatbázison ugyanezt a változást indítja el. Mindkét frissítésnek sikeresnek kell lennie ahhoz, hogy az elsődleges módosítás érvénybe lépjen. Az 1 TB-nál nagyobb területi korlátozások érvényesek. Ha a másodlagos olyan régióban található, amely nem támogatja az 1 TB-ot, az elsődleges nem frissül.
- Az import/export szolgáltatás használata az 1 TB-nál nagyobb P11-vagy P15-adatbázisok betöltéséhez nem támogatott. Az SqlPackage. exe használatával [importálhat](sql-database-import.md) és [exportálhat](sql-database-export.md) adatfájlokat.

## <a name="next-steps"></a>További lépések

A teljes erőforrás-korlátokkal kapcsolatban lásd: [SQL Database virtuális mag-alapú erőforrás-korlátok – önálló adatbázisok](sql-database-vcore-resource-limits-single-databases.md) és [SQL Database DTU-alapú erőforrás-korlátok – rugalmas készletek](sql-database-dtu-resource-limits-single-databases.md).

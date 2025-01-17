---
title: A Gen2 gyorsítótár optimalizálása |} A Microsoft Docs
description: Ismerje meg, hogyan figyelheti a Gen2-gyorsítótár az Azure portal használatával.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: performance
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 26791aecb2ca57b31358d3385d07230c73c84904
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61474419"
---
# <a name="how-to-monitor-the-gen2-cache"></a>A Gen2 cache figyelése
A tároló-architektúra Gen2 automatikusan rétegezi a leggyakrabban lekérdezett oszlopcentrikus szegmensek egy gyorsítótárban levő NVMe SSD-k alapján készült Gen2 data warehouse-adattárházak. Nagyobb teljesítmény van adatmegfelelőség, ha a lekérdezések beolvasása szegmenset, amely a gyorsítótárban levő vannak. Ez a cikk bemutatja, hogyan monitorozást és hibaelhárítást végezhet a lassú lekérdezések teljesítményének meghatározása, hogy a számítási feladatok optimális kihasználása a Gen2-gyorsítótár.  
## <a name="troubleshoot-using-the-azure-portal"></a>Hibaelhárítás az Azure portal használatával
Az Azure Monitor használatával a lekérdezési teljesítmény hibaelhárítása Gen2 gyorsítótár-metrikák megtekintése. Először nyissa meg az Azure Portalon, és kattintson a figyelése:

![Azure Monitor](./media/sql-data-warehouse-cache-portal/cache_0.png)

Válassza ki a metrikák gombot, és töltse ki a **előfizetés**, **erőforrás** **csoport**, **erőforrástípus**, és **erőforrás név** az adattárházzal.

Hibaelhárítás a Gen2 gyorsítótár szükséges fő mutatók vannak **gyorsítótárának találati százaléka** és **használt százalékos gyorsítótár**. Állítsa be az Azure metrikadiagram két metrikák megjelenítéséhez.

![Gyorsítótár-metrikák](./media/sql-data-warehouse-cache-portal/cache_1.png)


## <a name="cache-hit-and-used-percentage"></a>Gyorsítótár találati és használt százalékos aránya
A mátrix az alábbi forgatókönyvek a gyorsítótár-metrikák értékei alapján ismerteti:

|                                | **Magas gyorsítótár találati százaléka** | **Alacsony gyorsítótár találati százaléka** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Felhasznált gyorsítótár nagy százalékban** |          1\. forgatókönyv           |          2\. forgatókönyv          |
| **Felhasznált gyorsítótár alacsony százalékos aránya**  |          3\. forgatókönyv           |          4\. forgatókönyv          |

**1. forgatókönyv:** A gyorsítótár optimálisan használja. [Hibaelhárítás](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) más területeken, amely előfordulhat, hogy a lekérdezések kell lelassul.

**2. forgatókönyv:** Az aktuális adatok munkakészletének nem fér el őket a gyorsítótárhoz, amely egy alacsony gyorsítótár találati százaléka fizikai olvasások miatt. Érdemes felskálázni az teljesítményi szinthez, és futtassa újra a számítási feladatok, a gyorsítótár feltöltése miatt.

**3. forgatókönyv:** Valószínű, hogy a lekérdezés lassan fut, a gyorsítótár nem okok miatt. [Hibaelhárítás](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) más területeken, amely előfordulhat, hogy a lekérdezések kell lelassul. Emellett érdemes lehet [vertikális leskálázást a példány](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) csökkentése érdekében a gyorsítótár méretét a költségek csökkentése érdekében. 

**4. forgatókönyv:** Kellett egy ritkán használt gyorsítótár, amely lehet az oka, miért érdemes a lekérdezés lassú volt. Vegye figyelembe, hogy a lekérdezés ismételt módon működő adatkészlet most már a gyorsítótárba kerüljenek. 

**Fontos: Ha a gyorsítótár találati százaléka, vagy a számítási feladat újrafuttatása után nem frissíti a használt gyorsítótár százalékos aránya, a munkakészletének már a memóriában is található. Megjegyzés: csak fürtözött oszlopcentrikus táblák lettek gyorsítótárazva.**

## <a name="next-steps"></a>További lépések
Általános lekérdezési teljesítmény hangolása további információkért lásd: [lekérdezések futtatása figyelhető](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor#monitor-query-execution).


<!--Image references-->

<!--Article references-->
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[System views]: ./sql-data-warehouse-reference-tsql-system-views.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Investigating queries waiting for resources]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm_pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_exec_sessions]: https://msdn.microsoft.com/library/mt203883.aspx
[sys.dm_pdw_request_steps]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: https://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: https://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: https://msdn.microsoft.com/library/mt204028.aspx
[LABEL]: https://msdn.microsoft.com/library/ms190322.aspx

---
title: Jelentés Azure DevTest Labs használat több laborban és előfizetésben – Azure | Microsoft Docs
description: Megtudhatja, hogyan jelentheti Azure DevTest Labs használatát több Labs és előfizetés között.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2018
ms.author: takamath
ms.openlocfilehash: 0d930263233056d8fa74ffe6ccb176ee39429121
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68829228"
---
# <a name="report-azure-devtest-labs-usage-across-multiple-labs-and-subscriptions"></a>Jelentés Azure DevTest Labs használat több Labs és előfizetés között

A legtöbb nagy vállalat szeretné nyomon követni az erőforrás-használatot, hogy hatékonyabb legyen ezekkel az erőforrásokkal a trendek és a kiugró értékek megjelenítésével a használatban. Az erőforrás-használat alapján a labor tulajdonosai vagy kezelői testre szabhatják a laborokat az [erőforrás-használat és a költségek javítása](https://docs.microsoft.com/azure/billing/billing-getting-started)érdekében. Azure DevTest Labs az erőforrás-használatot a laborban töltheti le, így mélyebben megtekintheti a használati mintákat. Ezek a használati minták segíthetik a változások meghatározását a hatékonyság növelése érdekében. A legtöbb vállalat azt szeretné, hogy az egyéni labor-használat és az általános használat [több laboron és](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/)előfizetésen keresztül történjen. 

Ez a cikk azt ismerteti, hogyan kezelheti az erőforrás-használati adatokat több Labs és előfizetés között.

![Használat jelentése](./media/report-usage-across-multiple-labs-subscriptions/report-usage.png)

## <a name="individual-lab-usage"></a>Egyéni laborok használata

Ez a szakasz azt ismerteti, hogyan lehet exportálni az erőforrások használatát egyetlen laborban.

A DevTest Labs erőforrás-használatának exportálásához be kell állítania egy Azure Storage-fiókot, hogy a különböző fájlok tárolják a tárolt használati adatokat. Az adatexportálás két gyakori módon hajtható végre:

* [DevTest Labs REST API](https://docs.microsoft.com/rest/api/dtl/labs/exportresourceusage) 
* A PowerShell az. Resource modul [meghívja a-AzResourceAction](https://docs.microsoft.com/powershell/module/az.resources/invoke-azresourceaction?view=azps-2.5.0&viewFallbackFrom=azps-2.3.2) a művelettel `exportResourceUsage`, a labor erőforrás-azonosítóval és a szükséges paraméterekkel. 

    A [személyes adatok exportálása vagy törlése](personal-data-delete-export.md) cikkben egy minta PowerShell-parancsfájl található, amely részletes információkat tartalmaz az exportált adatokról. 

    > [!NOTE]
    > A Date paraméter nem tartalmaz időbélyeget, így az adatok a labor helyéül szolgáló időzóna alapján minden éjfélt tartalmaznak.

Az Exportálás befejezése után a blob Storage-ban több CSV-fájl lesz a különböző erőforrás-információkkal.
  
Jelenleg két CSV-fájl található:

* *virtualmachines. csv* – a laborban található virtuális gépekkel kapcsolatos információkat tartalmaz.
* *Disks. csv* – a laborban található különböző lemezekkel kapcsolatos információkat tartalmaz. 

Ezeket a fájlokat a rendszer a *labresourceusage* blob-tárolóban tárolja a labor neve, a labor egyedi azonosítója, a végrehajtás dátuma és a teljes vagy az exportálási kérelemben szereplő kezdő dátum alapján. Példa a Blobok struktúrájára:

* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD26-MM6-2019YYYY/full/virtualmachines.csv`
* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD-MM-YYYY/26-6-2019/20-6-2019<Start>DD-MM-YYYY/virtualmachines.csv`

## <a name="exporting-usage-for-all-labs"></a>Az összes labor használatának exportálása

Ha több Labs használati adatait szeretné exportálni, használja a következőt: 

* [Azure functions](https://docs.microsoft.com/azure/azure-functions/)számos nyelven elérhető, beleértve a PowerShellt, vagy 
* [Azure Automation runbook](https://docs.microsoft.com/azure/automation/), használja a PowerShellt, a Pythont vagy egy egyéni grafikus tervezőt az exportálási kód írásához.

Ezeknek a technológiáknak a használatával az egyes laborok exportálását egy adott dátummal és időponttal végezheti el az összes laborban. 

Az Azure-függvénynek az adatait a hosszú távú tárterületre kell küldenie. Több Labs adatexportálásakor az Exportálás hosszabb időt is igénybe vehet. Javasoljuk, hogy az egyes laborokat párhuzamosan hajtsa végre, hogy segítse a teljesítményt és csökkentse az adatok ismétlődésének lehetőségét. A párhuzamosság végrehajtásához aszinkron módon futtassa a Azure Functions. Kihasználhatja az Azure Functions ajánlathoz tartozó időzítő-trigger előnyeit is.

## <a name="using-a-long-term-storage"></a>Hosszú távú tárolás használata

A hosszú távú tárolás összevonja a különböző laborokból származó exportálási adatokat egyetlen adatforrásba. A hosszú távú tárolás egy másik előnye, hogy el tudja távolítani a fájlokat a Storage-fiókból, hogy csökkentse a másolást és a költségeket. 

A hosszú távú tárolás bármilyen szöveges manipulációhoz használható, például: 

* rövid nevek hozzáadása
* összetett csoportok létrehozása
* az Adategyesítés.

Néhány gyakori tárolási megoldás: [SQL Server](https://azure.microsoft.com/services/sql-database/), [Azure Data Lake](https://azure.microsoft.com/services/storage/data-lake-storage/)és [Cosmos db](https://azure.microsoft.com/services/cosmos-db/). Ha kiválasztja, hogy melyik hosszú távú tárolási megoldást választja, attól függ. Az adatmegjelenítés során érdemes lehet az eszközt attól függően kiválasztani, hogy mit kínál az interakciók rendelkezésre állása szempontjából.

## <a name="visualizing-data-and-gathering-insights"></a>Az adatmegjelenítés és az elemzések összegyűjtése

A használati adatok megjelenítéséhez és a használat hatékonyságának ellenőrzéséhez használja a kívánt adatvizualizációs eszközt a hosszú távú tároláshoz való kapcsolódáshoz. A [Power bi](https://docs.microsoft.com/power-bi/power-bi-overview) használható például a használati adatok rendszerezésére és megjelenítésére. 

A [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) használatával létrehozhat, csatolhat és kezelhet erőforrásokat egyetlen helyen lévő felületen belül. Ha nagyobb mértékű vezérlésre van szükség, az egyes erőforrások létrehozhatók egy adott erőforráscsoport keretén belül, és a Data Factory szolgáltatástól függetlenül kezelhetők.  

## <a name="next-steps"></a>További lépések

Ha a rendszer be van állítva, és az adatai a hosszú távú tárolásra kerülnek, a következő lépés az, hogy milyen kérdésekre kell válaszolnia. Példa: 

-   Mi a virtuális gép méretének használata?

    A felhasználók nagy teljesítményű (drágább) virtuálisgép-méretet választanak?
-   Milyen piactér-lemezképeket használ?

    Az Egyéni rendszerképek a leggyakoribb virtuálisgép-alap, a közös rendszerkép-tárolók, például a [megosztott](../virtual-machines/windows/shared-image-galleries.md) képkatalógus vagy a [rendszerkép-előállító](image-factory-create.md).
-   Mely egyéni lemezképeket használja vagy nem használja?

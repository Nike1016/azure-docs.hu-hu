---
title: Előre fizetés az Azure-beli fenntartott kapacitással rendelkező SQL Data Warehouse díjakhoz
description: Megtudhatja, hogyan teheti előre a fenntartott kapacitással rendelkező SQL Data Warehouse díjait a pénz megtakarítása érdekében.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: banders
ms.openlocfilehash: 6ee7a661434bb756c6cf196937229db19d06b373
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779988"
---
# <a name="prepay-for-sql-data-warehouse-charges-with-reserved-capacity"></a>Előre fizetés SQL Data Warehouse díjas, fenntartott kapacitással

A cDWU-használati idő egy vagy három évre való előfizetésével megtakarítható Azure SQL Data Warehouse. SQL Data Warehouse fenntartott kapacitás megvásárlásához ki kell választania az Azure-régiót és a kifejezést. Ezután adja hozzá a SQL Data Warehouse SKU-t a kosárhoz, és válassza ki a megvásárolni kívánt cDWU-egységek mennyiségét.

Foglalás megvásárlása esetén a foglalási attribútumoknak megfelelő SQL Data Warehouse-használatért az utólagos elszámolású csomagok díjai már nem számítanak fel díjat.

A foglalások nem fedik le a SQL Data Warehouse-használattal kapcsolatos tárolási vagy hálózati költségeket.

Ha a fenntartott kapacitás lejár, SQL Data Warehouse példányok továbbra is futni fognak, de az utólagos elszámolású díjszabás szerint számolunk fel díjat. A foglalások nem újítják meg automatikusan.

A díjszabással kapcsolatos információkért tekintse meg a [SQL Data Warehouse fenntartott kapacitás ajánlatát](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/).

A [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)Azure SQL Data Warehouse fenntartott kapacitást is vásárolhat. Fenntartott kapacitás megvásárlása:

- Legalább egy vállalati vagy utólagos elszámolású előfizetéshez rendelkeznie kell tulajdonosi szerepkörrel.
- Vállalati előfizetések esetén a **fenntartott példányok hozzáadása** beállítást engedélyezni kell az [EA portálon](https://ea.azure.com/). Ha a beállítás le van tiltva, akkor EA-rendszergazdának kell lennie.
- A Cloud Solution Provider (CSP) program esetében csak a felügyeleti ügynökök vagy értékesítési ügynökök vásárolhatják meg SQL Data Warehouse fenntartott kapacitást.

Ha többet szeretne megtudni arról, hogy a vállalati ügyfelek és az utólagos elszámolású ügyfelek milyen díjat számítanak fel a foglalások vásárlására, tekintse meg az [Azure foglalás használatának ismertetése](billing-understand-reserved-instance-usage-ea.md) a nagyvállalati regisztrációhoz és az [Azure-foglalás használatának ismertetése a ](billing-understand-reserved-instance-usage.md)Utólagos elszámolású előfizetés.

## <a name="choose-the-right-size-before-purchase"></a>A vásárlás előtt válassza ki a megfelelő méretet

A SQL Data Warehouse foglalási méretnek a felhasznált számítási adattárház-egységek (cDWU-EK) alapján kell alapulnia. A vásárlások 100 cDWU-növekményekben történnek.

Tegyük fel például, hogy a SQL Data Warehouse teljes fogyasztása DW3000c. Az összes számára fenntartott kapacitást kíván megvásárolni. Ezért a cDWU fenntartott kapacitás 30 egységét kell megvásárolnia.

## <a name="buy-sql-data-warehouse-reserved-capacity"></a>SQL Data Warehouse fenntartott kapacitás vásárlása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza **a minden szolgáltatás** > **foglalás**lehetőséget.
3. Válasszon előfizetést. Az előfizetés listával válassza ki azt az előfizetést, amelyet a fenntartott kapacitás kifizetéséhez használ. Az előfizetés fizetési módját a fenntartott kapacitás előzetes költségei alapján számítjuk fel. Az előfizetés típusának nagyvállalati szerződésnek kell lennie (ajánlati számok: MS-AZR-0017P vagy MS-AZR-0148P) vagy utólagos elszámolású (ajánlati számok: MS-AZR-0003P vagy MS-AZR-0023P).
   - Nagyvállalati előfizetésnél a díjak a regisztrációhoz tartozó keretek egyenlegeiből lesznek levonva, illetve túlhasználatként lesznek számlázva.
   - Használatalapú fizetéses előfizetéseknél a díjakat az előfizetéshez tartozó hitelkártyára terheljük vagy a számlafizetési módnak megfelelően számlázzuk.
4. Válasszon ki egy hatókört. Az előfizetési hatókör kiválasztásához használja a hatókör listát.
   - **Egy erőforráscsoport hatóköre** – a foglalási kedvezményt a kiválasztott erőforráscsoport megfelelő erőforrásaira alkalmazza.
   - **Egyszeri előfizetés hatóköre** – a foglalási kedvezményt a kiválasztott előfizetésben lévő megfelelő erőforrásokra alkalmazza.
   - **Megosztott hatókör** – a foglalási kedvezményt a számlázási környezetben található jogosult előfizetésekben lévő erőforrások egyeztetésére alkalmazza. Nagyvállalati Szerződés ügyfelek esetében a számlázási környezet a beléptetés. Az utólagos elszámolású előfizetések esetében a számlázási hatókör a fiók rendszergazdája által létrehozott összes jogosult előfizetés.
   - A nagyvállalati ügyfelek esetében a számlázási környezet az EA-regisztráció.
   - Az utólagos elszámolású ügyfelek esetében a megosztott hatókör a fiók rendszergazdája által létrehozott utólagos elszámolású előfizetések.
5. Válasszon ki egy régiót, és válassza ki azt az Azure-régiót, amelyet a fenntartott kapacitás érint.
6. Válasszon ki egy mennyiséget. Adja meg a megvásárolni kívánt 100 adatraktár-egységek (cDWU) mennyiségét.    
   A 30-as mennyiség például minden órában 3 000 cDWU-t biztosít.
7. Tekintse át a SQL Data Warehouse fenntartott kapacitás foglalásának költségeit a costs ( **költségek** ) szakaszban.
8. Válassza a **Beszerzés** lehetőséget.
9. Válassza a **foglalás megtekintése** lehetőséget a vásárlás állapotának megtekintéséhez.

## <a name="cancel-exchange-or-refund-reservations"></a>Megszakítási, Exchange-vagy visszatérítési foglalások

Bizonyos korlátozásokkal megszakíthatja, átválthatja vagy visszaállíthatja a foglalásokat. További információ: önkiszolgáló [cserék és visszatérítések Azure Reservations számára](billing-azure-reservations-self-service-exchange-and-refund.md).

A foglalási kedvezmény automatikusan érvényes azon SQL Data Warehouse példányok számára, amelyek megfelelnek a SQL Data Warehouse fenntartott kapacitás hatókörének és régiójának. A SQL Data Warehouse fenntartott kapacitás hatókörét a [Azure Portal](https://portal.azure.com/), a PowerShell, a CLI vagy az API használatával frissítheti.

## <a name="need-help-contact-us"></a>Segítség Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/).

## <a name="next-steps"></a>További lépések

- Ha többet szeretne megtudni arról, hogy a foglalási kedvezmények hogyan vonatkoznak a Azure SQL Data Warehousere, tekintse meg a [foglalási kedvezmények a Azure SQL Data Warehousere](billing-prepay-sql-data-warehouse-charges-with-reserved-capacity.md)vonatkozó témakört

- Ha többet szeretne megtudni a Azure Reservationsről, tekintse meg a következő cikkeket:
  - [Mi a Azure Reservations?](billing-save-compute-costs-reservations.md)
  - [Az Azure Reservations kezelése](billing-manage-reserved-vm-instance.md)
  - [Azure Reservations kedvezmény ismertetése](billing-understand-reservation-charges.md)
  - [Az utólagos elszámolású előfizetés foglalási használatának ismertetése](billing-understand-reserved-instance-usage.md)
  - [A nagyvállalati beléptetés foglalási használatának ismertetése](billing-understand-reserved-instance-usage-ea.md)

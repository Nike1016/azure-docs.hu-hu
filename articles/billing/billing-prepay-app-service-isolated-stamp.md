---
title: Előre fizetés Azure App Service elkülönített fizetési díjjal, fenntartott kapacitással
description: Megtudhatja, hogyan teheti előre a fenntartott kapacitással rendelkező Azure App Service elkülönített Stamp díját a pénz megtakarítása érdekében.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: banders
ms.openlocfilehash: f122ec2474c09cdd6c9ada4ddc59b1adb44f619f
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779992"
---
# <a name="prepay-for-azure-app-service-isolated-stamp-fee-with-reserved-capacity"></a>Előre fizetés Azure App Service elkülönített fizetési díjjal, fenntartott kapacitással

A bélyegzők használati idejére való előfizetéssel megtakaríthat Azure App Service elkülönített Stamp-díjakat. Az elkülönített Stamp díj fenntartott kapacitásának megvásárlásához ki kell választania azt az Azure-régiót, ahol a bélyegző üzembe lesz helyezve, és a megvásárolt bélyegzők száma.

A foglalás megvásárlása után a foglalási attribútumoknak megfelelő elkülönített Stamp díj már nem számítható fel az utólagos elszámolású díjszabásnál. A foglalás automatikusan a fenntartott kapacitás hatókörével és régiójával egyező elkülönített bélyegzők számán lesz alkalmazva. A foglalást nem kell elkülönített bélyegzőhöz rendelnie. A foglalás nem vonatkozik a feldolgozókra, így a bélyegzőhöz kapcsolódó egyéb erőforrásokra külön díjat számítunk fel.

Ha a fenntartott kapacitás lejár, az elkülönített bélyegzők továbbra is futni fognak, de az utólagos elszámolású díjszabás alapján kell fizetni. A foglalások nem újítják meg automatikusan.

## <a name="determine-the-right-reservation-to-purchase"></a>A megfelelő foglalás meghatározása a vásárláshoz

A foglalás megvásárlásával előre fizeti a fenntartott mennyiségek használatát a következő három évben. Ellenőrizze a használati adatokat, és állapítsa meg, hogy hány izolált App Service-bélyeget használ, és a jövőben is használhatja.

Emellett győződjön meg arról, hogy tisztában van azzal, hogy az elkülönített bélyegző hogyan bocsát ki Linux vagy Windows mérőszámot.

- Alapértelmezés szerint egy üres elkülönített bélyegző bocsátja ki a Windows Stamp-mérőt. Például nem üzemelő alkalmazottak üzembe helyezése. Továbbra is ezt a mérőszámot bocsátja ki, ha a Windows-feldolgozók a bélyegzőn vannak telepítve.
- Ha Linux-feldolgozót telepít, a mérő a Linux Stamp-mérőre változik.
- Abban az esetben, ha a Linux és a Windows rendszerű alkalmazottak is üzembe vannak helyezve, a bélyegző a Windows-mérőt bocsátja ki.

Így a Stamp mérőszám a bélyeg élettartama alatt változhat a Windows és a Linux között.

Windows Stamp foglalások vásárlása, ha egy vagy több Windows-feldolgozóval rendelkezik a bélyegzőn. A Linux Stamp foglalást csak akkor kell megvásárolnia, ha a bélyegen _csak_ Linux-feldolgozók vannak.

## <a name="buy-isolated-stamp-reserved-capacity"></a>Elkülönített bélyegző fenntartott kapacitásának vásárlása

Izolált bélyegzőhöz fenntartott kapacitást vásárolhat [](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D)a Azure Portalban. A fenntartott kapacitás megvásárlásához tulajdonosi szerepkörrel kell rendelkeznie legalább egy nagyvállalati előfizetéshez, vagy az utólagos elszámolású egyéni előfizetéshez.

- Vállalati előfizetések esetén a **fenntartott példányok hozzáadása** beállítást engedélyezni kell az [EA portálon](https://ea.azure.com/). Vagy ha a beállítás le van tiltva, akkor EA-rendszergazdának kell lennie.
- A Cloud Solution Provider (CSP) program esetében csak a felügyeleti ügynökök vagy értékesítési ügynökök vásárolhatják meg SQL Data Warehouse fenntartott kapacitást.

**Vásárlás:**

1. Lépjen a [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D).
1. Válasszon előfizetést. Az **előfizetés** listával válassza ki azt az előfizetést, amelyet a fenntartott kapacitás kifizetéséhez használ. Az előfizetés fizetési módját a fenntartott kapacitás előzetes költségei alapján számítjuk fel. Az előfizetés típusának nagyvállalati szerződésnek kell lennie (ajánlati számok: MS-AZR-0017P vagy MS-AZR-0148P) vagy utólagos elszámolású (ajánlati számok: MS-AZR-0003P vagy MS-AZR-0023P) vagy CSP-előfizetés.
    - Nagyvállalati előfizetésnél a díjak a regisztrációhoz tartozó keretek egyenlegeiből lesznek levonva, illetve túlhasználatként lesznek számlázva.
    - Használatalapú fizetéses előfizetéseknél a díjakat az előfizetéshez tartozó hitelkártyára terheljük vagy a számlafizetési módnak megfelelően számlázzuk.
1. Válasszon ki egy hatókört az előfizetés hatókörének kiválasztásához.
    - **Egy erőforráscsoport hatóköre** – a foglalási kedvezményt a kiválasztott erőforráscsoport megfelelő erőforrásaira alkalmazza.
    - **Egyszeri előfizetés hatóköre** – a foglalási kedvezményt a kiválasztott előfizetésben lévő megfelelő erőforrásokra alkalmazza.
    - **Megosztott hatókör** – a foglalási kedvezményt a számlázási környezetben található jogosult előfizetésekben lévő erőforrások egyeztetésére alkalmazza. Nagyvállalati Szerződés ügyfelek esetében a számlázási környezet a beléptetés. Az utólagos elszámolású előfizetések esetében a számlázási hatókör a fiók rendszergazdája által létrehozott összes jogosult előfizetés.
1. Válasszon ki egy régiót, és válassza ki azt az Azure-régiót, amelyet a fenntartott kapacitás fedez, és adja hozzá a foglalást a kosárhoz.
1. Válassza ki az elkülönített csomag típusát, majd kattintson a **kiválasztás**elemre.  
    ![Például](./media/billing-prepay-app-service-isolated-stamp/app-service-isolated-stamp-select.png)
1. Adja meg a lefoglalni izolált App Service stampek mennyiségét. A három mennyiség például három fenntartott bélyeget ad meg egy régióban. Kattintson **a Tovább gombra: Felülvizsgálat és vásárlás**.
1. Tekintse át és kattintson a **vásárlás most**lehetőségre.

A vásárlás után lépjen a [foglalások](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) pontra a vásárlás állapotának megtekintéséhez és a figyeléshez.

## <a name="cancel-exchange-or-refund-reservations"></a>Megszakítási, Exchange-vagy visszatérítési foglalások

Bizonyos korlátozásokkal megszakíthatja, átválthatja vagy visszaállíthatja a foglalásokat. További információ: önkiszolgáló [cserék és visszatérítések Azure Reservations számára](billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="discount-application-shown-in-usage-data"></a>A használati adatokban megjelenő kedvezményes alkalmazás

A használati adatok a foglalási kedvezményt lefoglaló használati díj nulla értékeként érvényesek. A használati adatok az egyes foglalásokban lévő Stamp-példányok foglalási kedvezményét jelenítik meg.

További információ arról, hogy a foglalási kedvezmény Hogyan jeleníti meg a használati adatokat: [nagyvállalati szerződés foglalási költségek és használat](billing-understand-reserved-instance-usage-ea.md) beszerzése, ha Ön NAGYVÁLLALATI szerződés (EA) ügyfél. Ellenkező esetben tekintse [meg az egyéni előfizetés Azure-foglalási használatát](billing-understand-reserved-instance-usage.md)az utólagos elszámolású díjszabással.

## <a name="next-steps"></a>További lépések

- Ha többet szeretne megtudni a Azure Reservationsről, tekintse meg a következő cikkeket:
  - [Mi a Azure Reservations?](billing-save-compute-costs-reservations.md)
  - [A Azure App Service izolált bélyegző foglalási kedvezményének megismerése](billing-reservation-discount-app-service-isolated-stamp.md)
  - [A nagyvállalati beléptetés foglalási használatának ismertetése](billing-understand-reserved-instance-usage-ea.md)

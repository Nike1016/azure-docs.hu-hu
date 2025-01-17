---
title: Cost Model a Azure NetApp Fileshoz | Microsoft Docs
description: A szolgáltatás költségeinek kezeléséhez szükséges Azure NetApp Files költség modelljét ismerteti.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: b-juche
ms.openlocfilehash: 563416418b3f387f103fddc88b3ba9ad4c93fdd4
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69030789"
---
# <a name="cost-model-for-azure-netapp-files"></a>Az Azure NetApp Files költségmodellje 

A Azure NetApp Filesi cost modell megismerése segít a szolgáltatás költségeinek kezelésében.

## <a name="calculation-of-capacity-consumption"></a>A kapacitás felhasználásának kiszámítása

Azure NetApp Files számlázása a kiépített tárolókapacitás alapján történik.  A kiépített kapacitás kapacitás-készletek létrehozásával van lefoglalva.  A kapacitási készletek számlázása a $/provisioned-GiB/month alapján, óránkénti növekedéssel történik. Egy adott kapacitású készlet minimális mérete 4 TiB, a kapacitási készletek pedig 1 TiB-os szinten bővíthetők. A kötetek a kapacitási készleteken belül jönnek létre.  Minden kötethez hozzá van rendelve egy kvóta, amely csökkenti a készletek által kiosztott kapacitást. A kötetekhez hozzárendelhető kvóta legalább 100 GiB-ig, legfeljebb 100 TiB-ig adható meg.  

Aktív kötet esetén a kapacitás-felhasználás a kvóta alapján logikai (tényleges) kapacitáson alapul.

Ha egy kötet tényleges kapacitása meghaladja a tárolási kvótáját, a kötet továbbra is növekedhet. Az írások továbbra is engedélyezve lesznek, ha a tényleges kötet mérete kisebb, mint a rendszer korlátja (100 TiB).  

A kapacitás készletben lévő teljes kapacitása a kiépített mennyiségtől a hozzárendelt kvóta vagy a készletben lévő összes kötet tényleges felhasználásának összege. 

   ![Felhasznált kapacitás teljes kiszámítása](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

Az alábbi ábra szemlélteti ezeket a fogalmakat.  
* A kapacitása 4 TiB kiosztott kapacitással rendelkezik.  A készlet három kötetet tartalmaz.  
    * Az 1. kötethez 2 TiB-os kvóta van hozzárendelve, és 800 GiB használat.  
    * A 2. kötethez 1 TiB-os kvóta van hozzárendelve, és 100 GiB használat.  
    * A 3. kötethez 500 GiB kvóta van hozzárendelve, de 800 GiB (túlhasználat).  
* A kapacitási készlet 4 TiB-kapacitásra (a kiosztott mennyiségre) van kiszámlázva.  
    3,8 TiB-kapacitás használatban van (2 TiB és 1 TiB az 1. és 2. kötetből származó kvótával, és 800 GiB a tényleges fogyasztás 3. köteten). És 200 GiB kapacitás megmarad.

   ![Kapacitási készlet három kötettel](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="overage-in-capacity-consumption"></a>Túlterhelési kapacitás-felhasználás  

Ha egy készlet teljes felhasznált kapacitása meghaladja a kiosztott kapacitást, az adatírások továbbra is engedélyezettek.  Ha a türelmi időszak (egy óra) után a készlet felhasznált kapacitása továbbra is meghaladja a kiosztott kapacitást, akkor a készlet mérete automatikusan 1 TiB-onként növekedik, amíg a kiépített kapacitás nagyobb a teljes felhasznált kapacitásnál.  Például a fenti ábrán, ha a 3. kötet továbbra is nő, és a tényleges fogyasztás eléri a 1,2 TiB-t, a türelmi időszak után a készlet automatikusan 5 TiB-ra lesz méretezve.  Ennek eredményeképpen a kiépített készlet kapacitása (5 TiB) meghaladja a felhasznált kapacitást (4,2 TiB).  

## <a name="manual-changes-of-the-pool-size"></a>A készlet méretének manuális módosítása  

Manuálisan növelheti vagy csökkentheti a készlet méretét. A következő megkötések azonban érvényesek:
* Szolgáltatás minimális és maximális korlátai  
    Tekintse meg az [erőforrás](azure-netapp-files-resource-limits.md)-korlátozásokról szóló cikket.
* 1 – TiB-növekmény a kezdeti 4 – TiB minimális vásárlás után
* Egy órás minimális számlázási növekmény
* Előfordulhat, hogy a kiépített készlet mérete nem csökkenthető a készlet teljes felhasznált kapacitásával.

## <a name="behavior-of-maximum-size-pool-overage"></a>A maximális méretet túllépő készlet viselkedése   

Egy olyan kapacitási készlet maximális mérete, amelyet létrehozhat vagy átméretezheti a 500 TiB-ra.  Ha a kapacitási készlet teljes felhasznált kapacitása meghaladja a 500 TiB-t, a következő helyzetek történnek:
* Az adatírások továbbra is engedélyezettek lesznek (ha a kötet a rendszer maximális 100 TiB-nál alacsonyabb).
* Az egyórás türelmi időszak után a rendszer automatikusan átméretezi a készletet 1 – TiB-onként, amíg a készlet kiépített kapacitása meghaladja a teljes felhasznált kapacitást.
* A további kiépített és számlázott készlet kapacitása meghaladja a 500 TiB-t, ezért nem használható mennyiségi kvóta hozzárendelésére. Emellett nem használható a teljesítmény-QoS-korlátok kibontására.  
    Lásd a [szolgáltatási szinteket](azure-netapp-files-service-levels.md) a teljesítmény korlátairól és a QoS méretezéséről.

Az alábbi ábra a következő fogalmakat szemlélteti:
* Egy prémium szintű Storage-réteggel és egy 500-TiB kapacitással rendelkező kapacitási készlettel rendelkezünk. A készlet kilenc kötetet tartalmaz.
    * Az 1 és 8 közötti kötetek a 60 TiB kvótáját kapják meg.  A teljes felhasznált kapacitás 480 TiB.  
        Minden kötethez az átviteli sebesség (60 TiB * 64 MiB/s) QoS-korlátja 3,75 GiB/s.  
    * A 9-es kötethez 20 TiB-os kvóta van rendelve.  
        A 9-es köteten az átviteli sebesség (60 TiB * 64 MiB/s) QoS-korlátja 1,25 GiB/s.
* A 9-es kötet túlhasználatos forgatókönyv. A tényleges fogyasztás 25 TiB-vel rendelkezik.  
    * Az egyórás türelmi időszak után a rendszer átméretezi a kapacitási készletet a 505 TiB-ra.  
        Ez a teljes kapacitás = 8 * 60-TiB kvóta az 1 – 8. kötethez, valamint 25 TiB a tényleges fogyasztásról a 9-es kötethez.
    * A számlázott kapacitás 505 TiB.
    * A 9-es kötethez tartozó mennyiségi kvóta nem növelhető (mivel a készlethez hozzárendelt teljes kvóta nem haladhatja meg a 500 TiB-t).
    * A további QoS-átviteli sebesség nem rendelhető hozzá (mivel a készlet teljes QoS-értéke továbbra is 500 TiB-on alapul).

   ![Kapacitási készlet kilenc kötettel](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-nine-vols.png)

## <a name="capacity-consumption-of-snapshots"></a>A pillanatképek kapacitásának felhasználása 

Azure NetApp Files a pillanatképek kapacitásának felhasználását a fölérendelt kötet kvótája alapján számítjuk fel.  Ennek eredményeképpen ugyanazzal a számlázási sebességgel osztozik, mint a kötethez tartozó kapacitási készlet.  Az aktív kötettől eltérően azonban a pillanatkép-felhasználás a felhasznált növekményes kapacitás alapján mérhető.  Azure NetApp Files Pillanatképek eltérő jellegűek. Az adatváltozási aránytól függően a pillanatképek gyakran sokkal kevesebb kapacitást használnak, mint az aktív kötet logikai kapacitása. Tegyük fel például, hogy van egy 500-GiB kötet pillanatképe, amely csak 10 GiB különbözeti adatokból áll. A pillanatképre vonatkozó mennyiségi kvóta alapján felszámított kapacitás 10 GiB, nem 500 GiB. 

## <a name="next-steps"></a>További lépések

* [Azure NetApp Files díjszabási oldala](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Azure NetApp Files szolgáltatási szintjei](azure-netapp-files-service-levels.md)
* [Az Azure NetApp Files erőforráskorlátai](azure-netapp-files-resource-limits.md)

---
title: Irányelvek & az Azure-Service Fabric megbízható gyűjteményekre vonatkozó javaslatok | Microsoft Docs
description: Irányelvek és javaslatok Service Fabric megbízható gyűjtemények használatához
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: masnider,rajak,zhol
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 12/10/2017
ms.author: atsenthi
ms.openlocfilehash: dc7d60cb846aa16f2facd41f5b6b7ce52bcc8f41
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599338"
---
# <a name="guidelines-and-recommendations-for-reliable-collections-in-azure-service-fabric"></a>Irányelvek és javaslatok az Azure-Service Fabric megbízható gyűjteményekhez
Ez a szakasz a megbízható állapot-kezelő és a megbízható gyűjtemények használatához nyújt útmutatást. A cél az, hogy segítse a felhasználókat a gyakori buktatók elkerülésében.

Az irányelvek a feltételek elvégzése, a *megfontolás*, a *elkerülés* és a *nem*megfelelő egyszerű javaslatok szerint vannak rendszerezve.

* Ne módosítsa az olvasási műveletek által visszaadott egyéni típusú objektumokat (például `TryPeekAsync` vagy `TryGetValueAsync`). A megbízható gyűjtemények, akárcsak a párhuzamos gyűjtemények, az objektumokra mutató hivatkozást adnak vissza.
* A módosítás előtt készítsen részletesen egy egyéni típusú visszaadott objektumot. Mivel a struktúrák és a beépített típusok értéke a pass-by-Value, nem kell részletes másolatot készítenie rajtuk, hacsak nem tartalmaznak hivatkozással megadott mezőket vagy tulajdonságokat, amelyeket módosítani kíván.
* Ne használja `TimeSpan.MaxValue` az időkorlátot. A holtpontok észleléséhez időtúllépést kell használni.
* Ne használjon tranzakciót a véglegesítés, a megszakított vagy a használat után.
* Ne használjon enumerálást a-ben létrehozott tranzakciós hatókörön kívül.
* Ne hozzon létre tranzakciót egy másik tranzakció `using` utasításán belül, mert a holtpontot eredményezhet.
* Ne hozzon létre megbízható `IReliableStateManager.GetOrAddAsync` állapotot, és használja a megbízható állapotot ugyanabban a tranzakcióban. Ez egy InvalidOperationException eredményez.
* Győződjön meg arról, `IComparable<TKey>` hogy a megvalósítás helyes. A rendszer az ellenőrzőpontok és `IComparable<TKey>` a sorok egyesítésének függőségét veszi igénybe.
* A frissítési zárolást akkor használja, ha olyan elemeket olvas be, amelyekkel frissíteni kívánja azt, hogy megakadályozza a holtpontok bizonyos osztályait.
* Vegye figyelembe, hogy a megbízható gyűjtemények száma nem haladhatja meg a 1000-ot. A megbízható gyűjtemények több elemmel is előnyben részesülnek, kevesebb elemmel.
* Ügyeljen arra, hogy az elemek (például a TKey + TValue megbízható szótárhoz) az 80 kilobájt alatt legyenek: kisebb a jobb. Ez csökkenti a nagyméretű objektum-halom használatának mennyiségét, valamint a lemez és a hálózat i/o-követelményeit. Gyakran csökkenti a duplikált adat replikálását, ha az érték csak egy kis részét frissíti. Ennek a megbízható szótárban való elérésének gyakori módja, ha a sorokat több sorba szeretné bontani.
* Érdemes lehet biztonsági mentési és visszaállítási funkciókat használni a vész-helyreállításhoz.
* A különböző elkülönítési szintek miatt ne keverje az egyentitásos `CreateEnumerableAsync`műveleteket és a többentitásos műveleteket (például `GetCountAsync`) ugyanabban a tranzakcióban.
* Végezze el a InvalidOperationException kezelését. A rendszer többféle okból is megszakíthatja a felhasználói tranzakciókat. Ha például a megbízható állapot-kezelő módosítja a szerepkört az elsődlegesen, vagy ha egy hosszan futó tranzakció blokkolja a tranzakciós napló csonkítása. Ilyen esetekben előfordulhat, hogy a felhasználó olyan InvalidOperationException kap, amely azt jelzi, hogy a tranzakciójuk már meg van szakítva. Feltételezve, hogy a felhasználó nem kérte a tranzakció megszüntetését, a kivétel kezelésére a legjobb módszer, ha a tranzakciót el szeretné érni, ellenőrizze, hogy a lemondási token meg lett-e jelezve (vagy a replika szerepköre megváltozott-e), és ha nem hoz létre új tranzakció, majd próbálkozzon újra.  

A következő szempontokat érdemes figyelembe venni:

* Az alapértelmezett időtúllépés négy másodperc az összes megbízható gyűjtemény API-hoz. A legtöbb felhasználónak az alapértelmezett időtúllépést kell használnia.
* Az alapértelmezett lemondási `CancellationToken.None` jogkivonat minden megbízható gyűjtemény API-ban szerepel.
* A megbízható szótár kulcs Type paraméterének (*TKey*) megfelelően kell megvalósítani `GetHashCode()` a `Equals()`-t és a-t. A kulcsok nem változtathatók meg.
* Ha magas rendelkezésre állást szeretne biztosítani a megbízható gyűjtemények számára, minden szolgáltatásnak rendelkeznie kell legalább egy célként megadott és minimális replikakészlet 3-as mérettel.
* A másodlagos olvasási műveletek a kvórumnak nem véglegesített verziókat is tartalmazhatnak.
  Ez azt jelenti, hogy az egyetlen másodlagosból beolvasott adatok egyik verziója hamis lehet.
  Az Elsődlegesből való beolvasás mindig stabil: soha nem lehet hamisan haladni.
* A megbízható gyűjteményben az alkalmazás által megőrzött adatok biztonsága/adatvédelme a saját döntése, valamint a tárolási felügyelet által biztosított védelem tárgya. I.E. Az operációs rendszer lemezének titkosítása felhasználható az inaktív adatok védelme érdekében.  

### <a name="next-steps"></a>További lépések
* [A Reliable Collections használata](service-fabric-work-with-reliable-collections.md)
* [Tranzakciók és zárolások](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Az adatkezelés
  * [Biztonsági mentés és visszaállítás](service-fabric-reliable-services-backup-restore.md)
  * [Értesítések](service-fabric-reliable-services-notifications.md)
  * [Szerializálás és frissítés](service-fabric-application-upgrade-data-serialization.md)
  * [Megbízható állapot-kezelő konfigurációja](service-fabric-reliable-services-configuration.md)
* Továbbiak
  * [Reliable Services – első lépések](service-fabric-reliable-services-quick-start.md)
  * [Fejlesztői referenciák megbízható gyűjteményekhez](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

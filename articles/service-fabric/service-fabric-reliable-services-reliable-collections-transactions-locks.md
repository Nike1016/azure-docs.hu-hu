---
title: Tranzakciók és zárolási módok az Azure-ban Service Fabric megbízható gyűjtemények | Microsoft Docs
description: Az Azure Service Fabric megbízható State Manager és megbízható gyűjtemények tranzakciója és zárolása.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: masnider,rajak
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: atsenthi
ms.openlocfilehash: 8e77e488a3c0a40a714a0e8efffba0a2947454bf
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599325"
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Tranzakciók és zárolási módok az Azure-ban Service Fabric megbízható gyűjtemények

## <a name="transaction"></a>Tranzakció
A tranzakciók egyetlen logikai egységként végrehajtott műveletek sorozatából állnak.
A tranzakciónak a következő sav-tulajdonságokat kell tartalmaznia. Lásd https://technet.microsoft.com/library/ms190612)
* **Atomi**: A tranzakciónak a Work atomi egységének kell lennie. Más szóval a rendszer minden adatmódosítást végez, vagy egyiket sem hajt végre.
* **Konzisztencia**: Ha elkészült, a tranzakciónak konzisztens állapotban kell hagynia az összes adategységet. A tranzakció végén minden belső adatstruktúrának helyesnek kell lennie.
* **Elkülönítés**: Az egyidejű tranzakciók által végrehajtott módosításokat el kell különíteni a más párhuzamos tranzakciók által végrehajtott módosításokkal. Egy ITransaction belüli művelethez használt elkülönítési szintet a műveletet végrehajtó IReliableState határozza meg.
* **Tartósság**: Egy tranzakció befejezése után a rendszer tartósan érvénybe helyezi a hatását. A módosítások rendszerhiba esetén is megmaradnak.

### <a name="isolation-levels"></a>Elkülönítési szintek
Az elkülönítési szint határozza meg, hogy a tranzakciót milyen mértékben kell elkülöníteni a más tranzakciók által végrehajtott módosításoktól.
A megbízható gyűjtemények két elkülönítési szintet támogatnak:

* **Ismételhető olvasás**: Megadja, hogy az utasítások nem tudják olvasni azokat az adatokat, amelyeket más tranzakciók még nem véglegesítettek, és hogy más tranzakciók nem módosíthatják az aktuális tranzakció által beolvasott adatokat, amíg az aktuális tranzakció be nem fejeződik. További részletek: [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).
* **Pillanatkép**: Meghatározza, hogy a tranzakció bármely utasítása által beolvasott adatok a tranzakció elején található adatok tranzakciós szempontból konzisztens verziója.
  A tranzakció csak azokat az adatmódosításokat ismeri fel, amelyeket a tranzakció elindítása előtt véglegesítettek.
  A más tranzakciók által az aktuális tranzakció kezdete után végrehajtott adatmódosítások nem láthatók az aktuális tranzakcióban végrehajtó utasításokban.
  Ennek a hatása, mintha egy tranzakció utasításai beolvassák a tranzakció elején található véglegesített adatokat.
  A pillanatképek konzisztensek a megbízható gyűjtemények között.
  További részletek: [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).

A megbízható gyűjtemények automatikusan kiválasztják az adott olvasási művelethez használt elkülönítési szintet a tranzakció létrehozásakor a művelettől és a replika szerepköreitől függően.
A következő táblázat az elkülönítési szint alapértelmezett értékeit ábrázolja a megbízható szótárak és üzenetsor-műveletek esetében.

| Művelet \ szerepkör | Elsődleges | Másodlagos |
| --- |:--- |:--- |
| Egyetlen entitás olvasása |Ismételhető olvasás |Pillanatkép |
| Számbavétel, darabszám |Pillanatkép |Pillanatkép |

> [!NOTE]
> Az egyentitásos műveletek általános példái `IReliableQueue.TryPeekAsync`a következők `IReliableDictionary.TryGetValueAsync`:.
> 

A megbízható szótár és a megbízható üzenetsor is támogatja az írások olvasását.
Ez azt jelenti, hogy a tranzakción belüli bármilyen írás látható lesz a következő olvasási műveletben, amely ugyanahhoz a tranzakcióhoz tartozik.

## <a name="locks"></a>Zárolások
Megbízható gyűjteményekben az összes tranzakció szigorú kétfázisú zárolást valósít meg: egy tranzakció nem szabadítja fel a beszerzett zárolásokat, amíg a tranzakció megszakítással vagy véglegesítéssel leáll.

A megbízható szótár minden egyentitásos műveletnél sor szintű zárolást használ.
A megbízható üzenetsor a szigorú tranzakciós FIFO-tulajdonsághoz tartozó egyidejűségen kívülről kereskedik.
A megbízható üzenetsor műveleti szintű zárolásokat használ, `TryPeekAsync` és egyszerre egy tranzakciót és/vagy `EnqueueAsync` `TryDequeueAsync` egy tranzakciót is engedélyez.
Vegye figyelembe, hogy a FIFO megőrzése érdekében `TryPeekAsync` , `TryDequeueAsync` ha egy vagy minden alkalommal megállapítja, hogy a megbízható várólista üres, akkor `EnqueueAsync`is zárolva lesznek.

Az írási műveletek mindig kizárólagos zárolást végeznek.
Olvasási műveletek esetén a zárolás néhány tényezőtől függ.
A pillanatkép-elkülönítés használatával végzett olvasási műveletek zárolása ingyenes.
Az összes ismételhető olvasási művelet alapértelmezés szerint a megosztott zárolásokat veszi igénybe.
Az ismételhető olvasást támogató olvasási műveletek esetében azonban a felhasználó a megosztott zárolás helyett a frissítési zárolást is kérheti.
A frissítési zárolás egy aszimmetrikus zárolás, amely megakadályozza a holtpont gyakori formáját, amely akkor fordul elő, ha egy későbbi időpontban több tranzakció zárolja a lehetséges frissítéseket.

A zárolási kompatibilitási mátrix a következő táblázatban található:

| Kérelem \ megadott | Nincsenek | Megosztott | frissítés | Kizárólagos |
| --- |:--- |:--- |:--- |:--- |
| Megosztott |Nincs ütközés |Nincs ütközés |Ütközés |Ütközés |
| frissítés |Nincs ütközés |Nincs ütközés |Ütközés |Ütközés |
| Kizárólagos |Nincs ütközés |Ütközés |Ütközés |Ütközés |

A megbízható gyűjtemények API-kra vonatkozó időtúllépési argumentum a holtpont észleléséhez használatos.
Például két tranzakció (T1 és T2) megkísérli olvasni és frissíteni a K1-et.
Lehetséges, hogy holtpontra válnak, mivel mindkettő a megosztott zárolással végződik.
Ebben az esetben az egyik vagy mindkét művelet időtúllépést eredményez.

Ez a holtpont-forgatókönyv nagyszerű példa arra, hogy a frissítési zárolás hogyan akadályozza meg a holtpontokat.

## <a name="next-steps"></a>További lépések
* [A Reliable Collections használata](service-fabric-work-with-reliable-collections.md)
* [Értesítések Reliable Services](service-fabric-reliable-services-notifications.md)
* [Biztonsági mentés és visszaállítás Reliable Services (vész-helyreállítás)](service-fabric-reliable-services-backup-restore.md)
* [Megbízható állapot-kezelő konfigurációja](service-fabric-reliable-services-configuration.md)
* [Fejlesztői referenciák megbízható gyűjteményekhez](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)


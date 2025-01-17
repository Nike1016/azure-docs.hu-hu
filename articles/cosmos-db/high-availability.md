---
title: Magas rendelkezésre állás a Azure Cosmos DBban
description: Ez a cikk azt ismerteti, hogy a Azure Cosmos DB hogyan biztosít magas rendelkezésre állást
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 4b039e777748499e1b9a2a120e9498d94066b735
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688283"
---
# <a name="high-availability-with-azure-cosmos-db"></a>Magas rendelkezésre állás a Azure Cosmos DB

Azure Cosmos DB transzparens módon replikálja az adatait a Cosmos-fiókjához társított összes Azure-régióban. Cosmos DB a következő képen látható módon több redundancia-réteget alkalmaz az adatokhoz:

![Fizikai particionálás](./media/high-availability/cosmosdb-data-redundancy.png)

- A Cosmos-tárolókban lévő [](partitioning-overview.md)adatkeretek horizontálisan particionálva vannak.

- Minden egyes régión belül minden partíciót egy replikával véd, és a replikák többsége által véglegesített összes írás replikálva és tartósan. A replikák több mint 10-20 tartalék tartományba vannak elosztva.

- Az összes régió összes partíciója replikálódik. Mindegyik régió egy Cosmos-tároló összes adatpartícióját tartalmazza, és írási és olvasási műveleteket is képes fogadni.  

Ha a Cosmos-fiókja *n* Azure-régióban van elosztva, az összes adatnak legalább *N* x 4 példánya lesz. Amellett, hogy alacsony késésű adatelérést és az írási/olvasási sebességet a Cosmos-fiókhoz társított régiók között, több régióban (nagyobb *N*) tovább javítja a rendelkezésre állást.  

## <a name="slas-for-availability"></a>SLA-kat a rendelkezésre álláshoz

Globálisan elosztott adatbázisként Cosmos DB átfogó SLA-kat biztosít, amelyek az átviteli sebességre, a esetek 99% percentilis, a konzisztencia és a magas rendelkezésre állásra vonatkozó késést foglalnak magukban. Az alábbi táblázat az egyes és a többrégiós fiókok Cosmos DB által biztosított magas rendelkezésre állási garanciákat mutatja be. A magas rendelkezésre állás érdekében mindig konfigurálja úgy a Cosmos-fiókokat, hogy több írási régióval rendelkezzenek.

|Művelet típusa  | Egyetlen régió |Több régió (egyrégiós írások)|Többrégiós (több régiós írások) |
|---------|---------|---------|-------|
|Írások    | 99,99    |99,99   |99,999|
|Olvasások     | 99,99    |99,999  |99,999|

> [!NOTE]
> A gyakorlatban a kötött elavulás, munkamenet, konzisztens előtag és végleges konzisztencia-modell tényleges írási rendelkezésre állása jelentősen meghaladja a közzétett SLA-kat. Az összes konzisztencia tényleges olvasási rendelkezésre állása jelentősen meghaladja a közzétett SLA-kat.

## <a name="high-availability-with-cosmos-db-in-the-event-of-regional-outages"></a>Magas rendelkezésre állás a Cosmos DB a regionális kimaradások esetén

A regionális kimaradások nem ritkák, az Azure Cosmos DB azonban gondoskodik arról, hogy az adatbázisa mindig magas rendelkezésre állású maradjon. Az alábbi részletek a Cosmos-fiók konfigurációjától függően a leállás során Cosmos DB viselkedést rögzítik:

- Ha Cosmos DB, az írási művelet elfogadását megelőzően az ügyfél számára az adatok tartósan a régióban lévő replikák kvóruma végzi el, amely elfogadja az írási műveleteket.

- A több írható régióval konfigurált többrégiós fiókok esetében az írások és olvasások egyaránt nagyon elérhetők lesznek. A regionális feladatátvételek azonnaliek, és nem igényelnek semmilyen változást az alkalmazásból.

- **Többrégiós fiókok egyetlen írási régióval (írási régió kimaradása):** 
  * Az írási régió meghibásodása esetén ezek a fiókok a beolvasáshoz is elérhetők maradnak. Ahhoz, hogy az írási kérelmek sikeresek legyenek, be kell kapcsolni az **automatikus feladatátvétel engedélyezése** beállítást az Azure Cosmos-fiókban. Ha engedélyezi ezt a beállítást, az átveszi az érintett régiót egy másik régióba az adott régió prioritásának sorrendjében. 
  * Ha a korábban érintett régió újra online állapotba került, az ütközések csatornán keresztül elérhetővé tett összes olyan írási adattal, amelyet a [](how-to-manage-conflicts.md#read-from-conflict-feed)régió meghiúsult, nem replikáltak. Az alkalmazások elolvashatják az ütközések csatornáját, elhárítják az alkalmazás-specifikus logikán alapuló ütközéseket, és szükség szerint visszaírják a frissített információt az Azure Cosmos-tárolóba. 
  * A korábban érintett írási régió helyreállítása után a rendszer automatikusan elérhetővé válik olvasási régióként. Az írási régióként visszaválthat a visszaállított régióra. A régiókat az [Azure CLI vagy a Azure Portal](how-to-manage-database-account.md#manual-failover)használatával válthat. Az írási régió és az alkalmazás továbbra is rendelkezésre áll, amíg az adatvesztés és a **rendelkezésre állás még nem** érhető el. 

- **Többrégiós fiókok egyetlen írási régióval (olvasási régió kimaradása):** 
  * Az olvasási régió meghibásodása esetén ezek a fiókok az olvasáshoz és íráshoz is elérhetők maradnak. 
  * Az érintett régió automatikusan le van választva az írási régióból, és kapcsolat nélküli állapotban lesz megjelölve. A [Azure Cosmos db SDK](sql-api-sdk-dotnet.md) -k átirányítják az olvasási hívásokat a következő elérhető régióba az előnyben részesített régiók listájában. 
  * Ha az előnyben részesített régiók listájának egyik régiója sem érhető el, a hívások automatikusan visszakerülnek az aktuális írási régióba. 
  * Az olvasási régió meghibásodásának kezeléséhez nincs szükség módosításra az alkalmazás kódjában. Végül, ha az érintett régió újra online állapotba kerül, a korábban érintett olvasási régió automatikusan szinkronizál az aktuális írási régióval, és ismét elérhető lesz az olvasási kérések kiszolgálásához. 
  * A következő olvasások a visszaállított régióba lesznek átirányítva az alkalmazás kódjának módosítása nélkül. Egy korábban sikertelen régió feladatátvétele és újracsatlakozása során a konzisztencia-garanciák továbbra is tiszteletben maradnak Cosmos DB.

- Az egyrégiós fiókok a regionális leállás után elveszíthetik a rendelkezésre állást. A magas rendelkezésre állás biztosítása érdekében mindig ajánlott **legalább két régiót** (lehetőleg legalább két írási régiót) beállítani a Cosmos-fiókkal.

- Még egy ritka és szerencsétlen esemény esetén is, ha az Azure-régió tartósan behajthatatlan, nincs adatvesztés, ha a többrégiós Cosmos-fiók az alapértelmezett konzisztencia-szinttel van *konfigurálva.* Ha tartósan letiltott írási régiót használ, a többrégiós Cosmos-fiókok határos állandósági konzisztencia-beállítása esetén a lehetséges adatvesztési időszak az elavultság ablakára korlátozódik (*K* vagy *T*). a munkamenetek, a konzisztens előtag és a végleges konzisztencia-szintek esetében a lehetséges adatvesztési időszak legfeljebb öt másodpercig tart. 

## <a name="availability-zone-support"></a>Rendelkezésre állási zóna támogatása

A Azure Cosmos DB egy globálisan elosztott, több főkiszolgálós adatbázis-szolgáltatás, amely magas rendelkezésre állást és rugalmasságot biztosít a regionális kimaradások során. A régiók közötti rugalmasság mellett mostantól engedélyezheti a **zónák redundanciát** az Azure Cosmos-adatbázishoz társítandó régió kiválasztásakor. 

A rendelkezésre állási zónák támogatásával Azure Cosmos DB biztosítja, hogy a replikák egy adott régióban több zónába kerüljenek, hogy magas rendelkezésre állást és rugalmasságot biztosítson a zónákon belüli meghibásodások során. Ebben a konfigurációban nem változnak a késés és a többi SLA. Egyetlen zóna meghibásodása esetén a Zone redundancia teljes körű adattartósságot biztosít a RPO = 0 és a rendelkezésre állás RTO = 0 segítségével való elérhetősége esetén. 

A zóna redundancia a [több főkiszolgálós replikáció](how-to-multi-master.md) funkciójának *kiegészítő funkciója* . A zónák redundancia önmagában nem lehet a regionális rugalmasság elérésére támaszkodni. Ha például regionális kimaradások vagy kis késleltetésű hozzáférés van a régiók között, azt javasoljuk, hogy több írási régióval is rendelkezzen a zóna redundancia mellett. 

Ha többrégiós írásokat konfigurál az Azure Cosmos-fiókhoz, külön díj nélkül is dönthet a zóna-redundancia szolgáltatásban. Ellenkező esetben tekintse meg az alábbi megjegyzést a zóna redundancia támogatásának díjszabását illetően. Az Azure Cosmos-fiók meglévő régiójába engedélyezheti a zóna redundanciát, ha eltávolítja a régiót, és újból hozzáadja a zóna redundancia beállítással.

Ez a funkció a következő Azure-régiókban érhető el:

* Az Egyesült Királyság déli régiója
* Délkelet-Ázsia 
* East US
* USA 2. keleti régiója 
* USA középső régiója
* Nyugat-Európa
* USA nyugati régiója, 2.

> [!NOTE] 
> Az egyetlen régióhoz tartozó Azure Cosmos-fiók Availability Zonesának engedélyezése olyan díjakat eredményez, amelyek egy további régiónak a fiókhoz való hozzáadásával egyenértékűek. A díjszabással kapcsolatos részletekért tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/cosmos-db/) és a többrégiós [költségeket Azure Cosmos db](optimize-cost-regions.md) cikkekben. 

A következő táblázat összefoglalja a különböző fiókok konfigurációinak magas rendelkezésre állási képességét: 

|KPI  |Egyetlen régió Availability Zones nélkül (nem AZ)  |Egyetlen régió Availability Zones (AZ)  |Multi-region writes with Availability Zones (AZ, 2 Regions) – a javasolt beállítás |
|---------|---------|---------|---------|
|Rendelkezésre állási SLA írása     |   99.99%      |    99.99%     |  99.999%  |
|Rendelkezésre állási SLA olvasása   |   99.99%      |   99.99%      |  99.999%       |
|Ár  |  Egyetlen régió számlázási díja |  Az önálló régió rendelkezésre állási zónájának számlázási sebessége |  Többrégiós számlázási díj       |
|Zónák hibái – adatvesztés   |  Adatvesztés  |   Nincs adatvesztés |   Nincs adatvesztés  |
|Zónák hibái – rendelkezésre állás |  Rendelkezésre állás elvesztése  | Nincs rendelkezésre állási veszteség  |  Nincs rendelkezésre állási veszteség  |
|Olvasási késés    |  Régiók közötti régió    |   Régiók közötti régió   |    Alacsony  |
|Írási késés    |   Régiók közötti régió   |  Régiók közötti régió    |   Alacsony   |
|Regionális leállás – adatvesztés    |   Adatvesztés      |  Adatvesztés       |   Adatvesztés <br/><br/> A többszörös főkiszolgálóval és több régióval rendelkező, kötött elavulás konzisztenciájának használatakor az adatvesztés a fiókon beállított korláttal van korlátozva. <br/><br/> A regionális leállás során az adatvesztés elkerülhető a több régióval való erős konzisztencia konfigurálásával. Ez a lehetőség olyan kompromisszumokat tartalmaz, amelyek befolyásolják a rendelkezésre állást és a teljesítményt.      |
|Regionális leállás – rendelkezésre állás  |  Rendelkezésre állás elvesztése       |  Rendelkezésre állás elvesztése       |  Nincs rendelkezésre állási veszteség  |
|Teljesítmény    |  X RU/s kiosztott átviteli sebesség      |  X RU/s kiosztott átviteli sebesség       |  2X RU/s kiosztott átviteli sebesség <br/><br/> Ennek a konfigurációs módnak kétszer kell megfelelnie az átviteli sebességnek, ha egyetlen régióhoz képest Availability Zones van, mert két régió van.   |

> [!NOTE] 
> Ha engedélyezni szeretné a rendelkezésre állási zónák támogatását egy többrégiós Azure Cosmos-fiókhoz, a fióknak engedélyezve kell lennie a több főkiszolgálós írásoknak


A zóna redundancia engedélyezhető, ha új vagy meglévő Azure Cosmos-fiókokhoz ad hozzá régiót. Jelenleg a Azure Portal, a PowerShell és a Azure Resource Manager sablonok használatával engedélyezhető a zóna-redundancia. Ha engedélyezni szeretné a zóna redundanciát az Azure Cosmos-fiókjában, állítsa `isZoneRedundant` be a `true` jelölőt egy adott helyre. Ezt a jelzőt a Locations (helyszínek) tulajdonságon belül állíthatja be. A következő PowerShell-kódrészlet például lehetővé teszi a zóna redundanciát a "Délkelet-ázsiai" régióban:

```powershell
$locations = @( 
    @{ "locationName"="Southeast Asia"; "failoverPriority"=0; "isZoneRedundant"= "true" }, 
    @{ "locationName"="East US"; "failoverPriority"=1 } 
) 
```

Az Azure Cosmos-fiók létrehozásakor Azure Portal használatával engedélyezheti Availability Zones. Fiók létrehozásakor ügyeljen arra, hogy engedélyezze a **geo-redundancia**, a többrégiós **írások**használatát, és válasszon egy régiót, ahol a Availability Zones támogatott: 

![Availability Zones engedélyezése a Azure Portal használatával](./media/high-availability/enable-availability-zones-using-portal.png) 

## <a name="building-highly-available-applications"></a>Magasan elérhető alkalmazások fejlesztése

- A magas írási és olvasási rendelkezésre állás biztosítása érdekében konfigurálja úgy a Cosmos-fiókot, hogy legalább két, több írási régióval rendelkező régióra legyen kiterjedni. Ez a konfiguráció biztosítja a legmagasabb rendelkezésre állást, a legalacsonyabb késést és a legjobb skálázhatóságot a SLA-kat támogató olvasások és írások számára. További információ: [a Cosmos-fiók konfigurálása több írási régióval](tutorial-global-distribution-sql-api.md).

- Az egyírásos régióval konfigurált többrégiós Cosmos-fiókok esetében engedélyezze az [automatikus feladatátvételt az Azure CLI vagy a Azure Portal használatával](how-to-manage-database-account.md#automatic-failover). Miután engedélyezte az automatikus feladatátvételt, ha regionális katasztrófa van, Cosmos DB automatikusan feladatátvételt hajt végre a fiókjában.  

- Még ha a Cosmos-fiókja is nagyon elérhető, előfordulhat, hogy az alkalmazás nem megfelelően van kialakítva, hogy továbbra is elérhető legyen. Az alkalmazás végpontok közötti magas rendelkezésre állásának teszteléséhez rendszeresen hívja meg a [manuális feladatátvételt az Azure CLI vagy a Azure Portal használatával](how-to-manage-database-account.md#manual-failover)az alkalmazás tesztelése vagy a vész-helyreállítási (Dr) részletezés részeként.

- Egy globálisan elosztott adatbázis-környezeten belül közvetlen kapcsolat áll fenn a konzisztencia szintje és az adattartósság között egy adott régióra kiterjedő leállás esetén. Az üzletmenet-folytonossági terv kidolgozása során meg kell ismernie a maximális elfogadható időtartamot, mielőtt az alkalmazás teljesen helyreállít egy zavaró esemény után. Az alkalmazás teljes helyreállításához szükséges idő a helyreállítási időre vonatkozó célkitűzés (RTO). Azt is meg kell ismernie, hogy a legutóbbi adatfrissítések maximális időtartama alatt az alkalmazás elveszítheti a zavaró események utáni helyreállítást. Az adatfrissítés-vesztés megengedhető időkorlátja a helyreállítási időkorlát (RPO). A Azure Cosmos DB RPO és RTO lásd: a [konzisztencia szintjei és](consistency-levels-tradeoffs.md#rto) az adattartósság

## <a name="next-steps"></a>További lépések

Ezután olvassa el a következő cikkeket:

* [Kompromisszumot kínál a különböző konzisztenciaszintet rendelkezésre állás és teljesítmény](consistency-levels-tradeoffs.md)
* [A kiosztott átviteli sebesség globális skálázása](scaling-throughput.md)
* [Globális terjesztés – a motorháztető alatt](global-dist-under-the-hood.md)
* [Azure Cosmos DB konzisztenciáji szintjei](consistency-levels.md)
* [Cosmos-fiók konfigurálása több írási régióval](how-to-multi-master.md)

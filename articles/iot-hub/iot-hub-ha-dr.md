---
title: Azure IoT Hub magas rendelkezésre állású és vész-helyreállítási | Microsoft Docs
description: Ismerteti azokat az Azure-és IoT Hub-funkciókat, amelyek segítségével a vész-helyreállítási képességekkel rendelkező, magasan elérhető Azure IoT-megoldásokat hozhat létre.
author: rkmanda
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/07/2018
ms.author: philmea
ms.openlocfilehash: 32caebf8ea216050427f4400102cf56ffc657b55
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875249"
---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>Magas rendelkezésre állás és vész-helyreállítás IoT Hub

A rugalmas IoT-megoldások megvalósításának első lépéseként az építészeknek, fejlesztőknek és üzlettulajdonosoknak meg kell határozniuk a kiépített megoldásokhoz szükséges üzemidői célokat. Ezeket a célokat elsősorban az egyes forgatókönyvek konkrét üzleti céljai alapján lehet meghatározni. Ebben a kontextusban az [Azure üzletmenet-folytonossági műszaki útmutatója](https://docs.microsoft.com/azure/architecture/resiliency/) egy általános keretrendszert ismertet, amely segítséget nyújt az üzletmenet folytonosságának és a vész-helyreállításnak. Az [Azure-alkalmazások vész-helyreállítási és magas rendelkezésre állása](https://msdn.microsoft.com/library/dn251004.aspx) az Azure-alkalmazások magas rendelkezésre állást (ha) és a vész-helyreállítást (Dr) biztosító stratégiákra vonatkozó architektúrával kapcsolatos útmutatást nyújt.

Ez a cikk a IoT Hub szolgáltatás által kifejezetten kínált HA és DR funkciókat ismerteti. A cikkben tárgyalt általános területek a következők:

- Régión belüli HA
- Több régió – DR
- Régiók közötti régió elérése – HA

A IoT-megoldások számára meghatározott rendelkezésre állási céloktól függően meg kell határoznia, hogy az alábbi lehetőségek közül melyik felel meg a legjobban az üzleti céloknak. Ezeknek a HA/DR alternatíváknak a IoT-megoldásba való beépítése a következők közötti kompromisszumok gondos értékelését igényli:

- A szükséges rugalmassági szint 
- A megvalósítás és a karbantartás bonyolultsága
- ELÁBÉ hatása

## <a name="intra-region-ha"></a>Régión belüli HA

A IoT Hub szolgáltatás a régión belüli e-mailben biztosítja a redundancia megvalósítását a szolgáltatás szinte minden rétegében. A [IoT hub szolgáltatás által közzétett SLA](https://azure.microsoft.com/support/legal/sla/iot-hub) -t a redundanciák használatával érheti el. Egy IoT-megoldás fejlesztőinek nincs szüksége további munkára, hogy kihasználhassa ezeket a HA funkciókat. Bár a IoT Hub ésszerűen magas idejű garanciát nyújt, az átmeneti hibák továbbra is megtekinthetők az elosztott számítástechnikai platformokkal. Ha most kezdi a megoldások felhőbe való áttelepítését egy helyszíni megoldásból, a fókusznak a "hibák közötti átlagos idő" értékről "a helyreállítás ideje" kifejezésre kell váltania. Ez azt jelenti, hogy az átmeneti hibák normálisnak számítanak, miközben a felhőben működnek a vegyesen. A [](iot-hub-reliability-features-in-sdks.md) megfelelő újrapróbálkozási házirendeket olyan összetevőkhöz kell beépíteni, amelyek egy felhőalapú alkalmazással működnek az átmeneti hibák kezeléséhez.

> [!NOTE]
> Bizonyos Azure-szolgáltatások a [Availability Zones (AZs)](../availability-zones/az-overview.md)integrálásával a régión belül további rendelkezésre állási rétegeket is biztosítanak. A IoT Hub szolgáltatás jelenleg nem támogatja a AZs.

## <a name="cross-region-dr"></a>Több régió – DR

Előfordulhat, hogy az adatközpontok olyan ritka helyzetekben fordulnak elő, amikor áramkimaradások vagy fizikai eszközöket érintő egyéb hibák miatt kibővített kimaradások vannak. Ezek az események ritkán fordulnak elő, amikor a fent ismertetett régióbeli HA-képesség nem mindig segít. IoT Hub több megoldást kínál az ilyen kibővített kimaradások helyreállításához. 

Az ilyen helyzetben lévő ügyfelek számára elérhető helyreállítási lehetőségek a következők: "Microsoft által kezdeményezett feladatátvétel" és "manuális feladatátvétel". A kettő közötti alapvető különbség az, hogy a Microsoft kezdeményezi a korábbit, és a felhasználó kezdeményezi az utóbbit. Emellett a manuális feladatátvétel a Microsoft által kezdeményezett feladatátvételi lehetőséggel összehasonlítva alacsonyabb helyreállítási időcélkitűzést (RTO) biztosít. Az egyes beállításokhoz kínált konkrét RTOs az alábbi szakaszokban tárgyaljuk. Ha a IoT hub elsődleges régiójából feladatátvételt hajt végre, akkor a hub a megfelelő [Azure-beli földrajzi régióban](../best-practices-availability-paired-regions.md)teljesen működőképes lesz.

Mindkét feladatátvételi beállítás a következő helyreállítási pontok célkitűzéseit (RPO) kínálja:

| Adattípus | Helyreállítási pontok célkitűzései (RPO) |
| --- | --- |
| Identitás-nyilvántartó |0-5 perc adatvesztés |
| Eszköz Twin-adatkészletei |0-5 perc adatvesztés |
| Felhőből az eszközre irányuló üzenetek<sup>1</sup> |0-5 perc adatvesztés |
| <sup>1</sup> . szülő-és eszköz-feladatok |0-5 perc adatvesztés |
| Az eszközről a felhőbe irányuló üzenetek |Az összes olvasatlan üzenet elvész |
| Műveletek figyelési üzenetei |Az összes olvasatlan üzenet elvész |
| A felhőből az eszközre irányuló visszajelzési üzenetek |Az összes olvasatlan üzenet elvész |

<sup>1</sup> A felhőből az eszközre irányuló üzenetek és a fölérendelt feladatok nem állíthatók helyre a manuális feladatátvétel részeként a funkció előzetes verziójában.

Miután az IoT hub feladatátvételi művelete befejeződött, az eszközről és a háttérbeli alkalmazásokról érkező összes művelet manuális beavatkozás nélkül is működni fog.

> [!CAUTION]
> - A IoT Hub beépített események végpontjának az Event hub-kompatibilis neve és végpontja módosult a feladatátvétel után. Ha az Event hub-ügyfél vagy az esemény-feldolgozó gazdagép használatával fogad telemetria üzeneteket a beépített végpontról, az [IoT hub kapcsolati karakterláncát](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) kell használnia a kapcsolat létrehozásához. Ez biztosítja, hogy a háttérbeli alkalmazások a feladatátvétel utáni manuális beavatkozás nélkül is működjenek. Ha az Event hub-kompatibilis nevet és végpontot használja közvetlenül a háttérbeli alkalmazásban, újra kell konfigurálnia az alkalmazást úgy, hogy a feladatátvételt követően beolvassa [az új Event hub-kompatibilis nevet és végpontot](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) a műveletek folytatásához.
>
> - A feladatátvételt követően a Event Gridon keresztül kibocsátott események a korábban konfigurált előfizetéseken keresztül is felhasználhatók, feltéve, hogy a Event Grid-előfizetések továbbra is elérhetők lesznek.
>
> - A blob Storage-hoz való útválasztás esetén javasoljuk a Blobok bekapcsolását és az azokhoz való iterációt, hogy minden tárolót beolvasson a partíciós feltételezések elkészítése nélkül. A partíció tartománya esetleg változhat a Microsoft által kezdeményezett feladatátvétel vagy manuális feladatátvétel során. A Blobok listájának enumerálásával kapcsolatos információkért lásd: [Útválasztás a blob Storage-](iot-hub-devguide-messages-d2c.md#azure-blob-storage)ba.

### <a name="microsoft-initiated-failover"></a>Microsoft által kezdeményezett feladatátvétel

A Microsoft által kezdeményezett feladatátvételt ritka helyzetekben a Microsoft gyakorolja, hogy az érintett régióból származó összes IoT-csomópontot a megfelelő geo-párosítású régióba lehessen átadni. Ez a folyamat egy alapértelmezett beállítás (nincs lehetőség a felhasználók letiltására), és nem igényel beavatkozást a felhasználótól. A Microsoft fenntartja a jogot arra, hogy meghatározza, hogy a rendszer mikor gyakorolja ezt a lehetőséget. Ez a mechanizmus nem tartalmaz felhasználói beleegyező engedélyt, mielőtt a felhasználó központja feladatátvételre kerül. A Microsoft által kezdeményezett feladatátvétel 2-26 óra helyreállítási időcélkitűzéssel (RTO) rendelkezik. 

A nagyméretű RTO azért van, mert a Microsoft számára a feladatátvételi műveletet az adott régióban lévő összes érintett ügyfél nevében kell végrehajtania. Ha olyan kevésbé kritikus IoT-megoldást futtat, amely nagyjából egy nap állásidőt tart fenn, akkor ez a lehetőség attól függ, hogy a IoT-megoldáshoz tartozó teljes vész-helyreállítási célokat kielégítse. A folyamat elindítása után teljes mértékben működőképes futásidejű műveletek teljes ideje a "helyreállítás ideje" című szakaszban olvasható.

### <a name="manual-failover-preview"></a>Manuális feladatátvétel (előzetes verzió)

Ha a Microsoft által kezdeményezett feladatátvétel által biztosított RTO nem teljesítik a rendelkezésre álló üzleti célokat, érdemes lehet manuális feladatátvételt használni a feladatátvételi folyamat elindításához. A kapcsolót használó RTO 10 perc és néhány óra között lehet. A RTO jelenleg a feladatátvétel során a IoT hub-példányon regisztrált eszközök számának függvénye. Számíthat arra, hogy a RTO körülbelül 100 000 eszközt üzemeltető hubhoz 15 perces stadionban kell lennie. A folyamat elindítása után teljes mértékben működőképes futásidejű műveletek teljes ideje a "helyreállítás ideje" című szakaszban olvasható.

A manuális feladatátvételi lehetőség mindig használható, függetlenül attól, hogy az elsődleges régió leállást tapasztal-e. Ezért lehetséges, hogy ez a beállítás a tervezett feladatátvételek végrehajtására használható. A tervezett feladatátvételek használata például rendszeres feladatátvételi gyakorlatokat hajt végre. A figyelmeztetés figyelmezteti, hogy a tervezett feladatátvételi művelet leállást eredményez a hub számára a RTO által meghatározott időszakra vonatkozóan, és a fenti RPO-táblázat által meghatározott adatvesztést is eredményez. Érdemes lehet úgy beállítani egy teszt IoT hub-példányt, hogy a tervezett feladatátvételi lehetőség rendszeres időközönként megszerezze a szükséges megbízhatóságot a teljes körű megoldások üzembe helyezéséhez, ha valós katasztrófa következik be.

> [!IMPORTANT]
> - A tesztelési gyakorlatokat nem kell végrehajtani az éles környezetekben használt IoT-hubokon.
>
> - A manuális feladatátvételt nem szabad olyan mechanizmusként használni, amellyel véglegesen áttelepítheti az elosztót az Azure geo párosított régiói között. Ennek hatására a rendszer nagyobb késést okoz a hub-on a régi elsődleges régióban lévő eszközökön végrehajtott műveleteknél.

### <a name="failback"></a>Feladat-visszavétel

A régi elsődleges régióba való visszalépést úgy érheti el, ha egy másik alkalommal aktiválja a feladatátvételi műveletet. Ha az eredeti feladatátvételi műveletet az eredeti elsődleges régióban lévő kibővített kimaradásból való helyreállításra hajtották végre, javasoljuk, hogy a hubot az eredeti helyre kell visszaadnia, miután a hely helyreállt a leállás állapotból.

> [!IMPORTANT]
> - A felhasználók csak két sikeres feladatátvételt és 2 sikeres feladat-visszavétel műveletet hajthatnak végre naponta.
>
> - A feladatátvételi/feladat-visszavételi műveletek nem engedélyezettek vissza. A felhasználóknak a műveletek között 1 órára kell várniuk.

### <a name="time-to-recover"></a>Helyreállítás ideje

Míg a IoT hub-példány teljes tartományneve (és így a kapcsolódási karakterlánc) változatlan marad, a mögöttes IP-cím módosul. Ezért az IoT hub-példányon végrehajtott futásidejű műveletek teljes működési ideje a feladatátvételi folyamat elindítása után a következő függvény használatával fejezhető ki.

Helyreállítás ideje = RTO [10 perc – 2 óra a manuális feladatátvételhez | 2-26 óra a Microsoft által kezdeményezett feladatátvételhez] + DNS-propagálási késleltetés + az ügyfélalkalmazás által a gyorsítótárazott IoT Hub IP-címek frissítéséhez szükséges idő.

> [!IMPORTANT]
> A IoT SDK-k nem gyorsítótárazzák az IoT hub IP-címét. Javasoljuk, hogy az SDK-k által használt felhasználói kódok ne gyorsítótárazzák az IoT hub IP-címét.

## <a name="achieve-cross-region-ha"></a>Régiót érhet el, HA

Ha a Microsoft által kezdeményezett feladatátvételi vagy kézi feladatátvételi lehetőségek nem teljesítik az üzleti üzemidő célját, érdemes megfontolnia az eszközönkénti automatikus RTO-feladatátvételi mechanizmus megvalósítását.
Az üzembe helyezési topológiák teljes kezelése a IoT-megoldásokban a jelen cikk hatókörén kívül esik. A cikk a magas rendelkezésre állás és a vész-helyreállítás érdekében ismerteti a *regionális feladatátvételi* üzembe helyezési modellt.

Egy regionális feladatátvételi modellben a megoldás háttérrendszer elsődlegesen egy adatközpont-helyen fut. A másodlagos IoT hub és a háttérrendszer egy másik adatközpont-helyen van üzembe helyezve. Ha az elsődleges régióban az IoT hub leáll, vagy az eszközről az elsődleges régióhoz való hálózati kapcsolat megszakad, az eszközök másodlagos szolgáltatási végpontot használnak. A megoldás rendelkezésre állását a régiók közötti feladatátvételi modell megvalósításával javíthatja, nem pedig egyetlen régión belül. 

Ha a regionális feladatátvételi modellt a IoT Hub használatával kívánja megvalósítani, a következő lépéseket kell elvégeznie:

* **Másodlagos IoT hub-és eszköz-útválasztási logika**: Ha az elsődleges régióban lévő szolgáltatás megszakad, az eszközöknek csatlakozniuk kell a másodlagos régióhoz. A legtöbb érintett szolgáltatás természetéből adódó jellegéből adódóan gyakori megoldás a rendszergazdák számára a régiók közötti feladatátvételi folyamat kiváltása. Az új végpont eszközeivel való kommunikációjának legjobb módja, miközben a folyamat irányításának fenntartása mellett az aktuális aktív végponthoz tartozó *concierge* -szolgáltatást rendszeresen ellenőrizni kell. A concierge szolgáltatás lehet egy olyan webalkalmazás, amelyet a rendszer a DNS-átirányítási technikák használatával replikál, és elérhetővé tart (például az [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)használatával).

   > [!NOTE]
   > Az IoT hub szolgáltatás nem támogatott végponti típus az Azure Traffic Managerban. Javasoljuk, hogy az Azure Traffic Managerrel integrálja a javasolt concierge szolgáltatást az Endpoint Health mintavételi API megvalósításával.

* **Identitás-beállításjegyzék replikációja**: Ahhoz, hogy használható legyen, a másodlagos IoT hub-nak tartalmaznia kell az összes olyan eszköz-identitást, amely képes csatlakozni a megoldáshoz. A megoldásnak meg kell őriznie az eszköz-identitások földrajzilag replikált biztonsági mentését, és fel kell töltenie őket a másodlagos IoT hubhoz, mielőtt az aktív végpontot átváltja az eszközökre. A IoT Hub eszköz-identitás exportálási funkciója hasznos ebben a környezetben. További információ: [IoT hub fejlesztői útmutató – Identity Registry](iot-hub-devguide-identity-registry.md).

* **Logikai egyesítés**: Ha az elsődleges régió újra elérhetővé válik, a másodlagos helyen létrehozott összes államot és az összes adattal át kell telepíteni az elsődleges régióba. Ez az állapot és az adatok többnyire az eszközök identitására és az alkalmazás metaadataira vonatkoznak, amelyeket egyesíteni kell az elsődleges IoT hub-vel és minden más, az elsődleges régióban lévő alkalmazásspecifikus tárolóval. 

Ennek a lépésnek a leegyszerűsítése érdekében idempotens műveleteket kell használnia. A idempotens-műveletek az események végleges eloszlása, valamint a duplikált vagy az események sorrendjében történő kézbesítésének mellékhatásait csökkenthetik. Emellett az alkalmazás logikáját úgy kell kialakítani, hogy a potenciális inkonzisztencia vagy némileg elavult állapotban legyen. Ez a helyzet akkor fordulhat elő, ha a rendszernek a helyreállítási pontok célkitűzései (RPO) alapján történő megtartásához szükséges további időt kell meggyógyítania.

## <a name="choose-the-right-hadr-option"></a>Válassza a megfelelő HA/DR lehetőséget

Íme egy összefoglaló az ebben a cikkben bemutatott HA/DR lehetőségről, amely a megoldáshoz használható megfelelő lehetőség kiválasztására szolgál.

| HA/DR beállítás | RTO | RPO | Manuális beavatkozásra van szüksége? | Implementáció bonyolultsága | További költséghatékonyság|
| --- | --- | --- | --- | --- | --- |
| Microsoft által kezdeményezett feladatátvétel |2-26 óra|Tekintse át a fenti RPO-táblázatot|Nem|Nincsenek|Nincsenek|
| Manuális feladatátvétel |10 perc – 2 óra|Tekintse át a fenti RPO-táblázatot|Igen|Nagyon alacsony. Ezt a műveletet csak a portálról kell elindítania.|None|
| Régión átívelő HA |< 1 perc|Az egyéni HA-megoldás replikációs gyakoriságának függvénye|Nem|Magas|> 1 IoT hub díja|

## <a name="next-steps"></a>További lépések

Az alábbi hivatkozásokat követve további információkat tudhat meg az Azure IoT Hubról:

* [Ismerkedés a IoT Hubokkal (gyors útmutató)](quickstart-send-telemetry-dotnet.md)
* [Mi az Azure IoT Hub?](about-iot-hub.md)
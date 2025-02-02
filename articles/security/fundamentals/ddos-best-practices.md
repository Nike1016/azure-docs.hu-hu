---
title: Azure DDoS Protection ajánlott eljárások és hivatkozási architektúrák | Microsoft Docs
description: Ismerje meg, hogy miként használhatók a naplózási információk az alkalmazással kapcsolatos mélyreható elemzésekhez.
services: security
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2018
ms.author: barclayn
ms.openlocfilehash: a5b4451a6d03cec8e100ed67c0ed9333e8a221de
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727475"
---
# <a name="azure-ddos-protection-best-practices-and-reference-architectures"></a>Azure DDoS Protection: Ajánlott eljárások és hivatkozási architektúrák

Ez a cikk az informatikai döntéshozók és a biztonsági személyzet számára készült. Elvárja, hogy már ismeri az Azure-t, a hálózatkezelést és a biztonságot.

Az elosztott szolgáltatásmegtagadás (DDoS) rugalmasságának tervezése számos különböző meghibásodási mód megtervezését és kialakítását igényli. Ez a cikk az Azure-ban a DDoS-támadások elleni rugalmasságot biztosító alkalmazások tervezésének ajánlott eljárásait ismerteti.

## <a name="types-of-attacks"></a>A támadások típusai

A DDoS olyan támadási típus, amely megpróbálja kimeríteni az alkalmazás erőforrásait. A cél az alkalmazás rendelkezésre állásának és a legitim kérelmek kezelésére való képességének a befolyásolása. A támadások egyre kifinomultabbak és nagyobbak a méret és a hatás terén. A DDoS-támadások bármilyen, az interneten keresztül nyilvánosan elérhető végpontot megcélozhatnak.

Az Azure folyamatos védelmet biztosít a DDoS-támadásokkal szemben. Ez a védelem alapértelmezés szerint és külön díj nélkül integrálva van az Azure-platformba. 

A platformon a DDoS elleni védelem mellett a [Azure DDoS Protection standard](https://azure.microsoft.com/services/ddos-protection/) fejlett DDoS-elhárítási képességeket biztosít a hálózati támadásokkal szemben. A rendszer automatikusan hangolja az adott Azure-erőforrások megóvására. A védelem egyszerűen engedélyezhető az új virtuális hálózatok létrehozása során. A létrehozás után is elvégezhető, és nem igényel alkalmazás-vagy erőforrás-módosítást.

![Azure DDoS Protection szerepe az ügyfelek és a virtuális hálózat támadók általi védelmében](./media/ddos-best-practices/image1.png)

A DDoS-támadások három kategóriába sorolhatók: térfogatmérő, protokoll és erőforrás.

### <a name="volumetric-attacks"></a>Térfogatmérő támadások

A nagy mennyiségű támadás a DDoS-támadás leggyakoribb típusa. A térfogatos támadások olyan találgatásos támadásokat okoznak, amelyek a hálózati és szállítási rétegeket célozzák meg. Megpróbálják kimeríteni az erőforrásokat, például a hálózati kapcsolatokat. 

Ezek a támadások gyakran több fertőzött rendszert használnak a látszólag megbízható adatforgalommal rendelkező hálózati rétegek elárasztása érdekében. A hálózati rétegbeli protokollokat, például a Internet Control Message Protocol (ICMP), a User Datagram Protocol (UDP) és a Transmission Control Protocol (TCP) protokollt használják.

A leggyakrabban használt hálózati szintű DDoS-támadások a TCP SYN-árvizek, az ICMP echo, az UDP-árvizek, a DNS és az NTP-erősítés elleni támadások. Ez a típusú támadás nem csupán a szolgáltatás megzavarására használható, hanem ködösítés is, amely több aljas és megcélzó hálózati behatolást okoz. A közelmúltban megjelenő mennyiségű támadás egy példa arra, hogy a kihasználatlan biztonsági [rés](https://www.wired.com/story/github-ddos-memcached/) a githubot érinti. Ez a támadás a 11211-es UDP-portot és a 1,35 TB/s-t generálta a támadási köteten.

### <a name="protocol-attacks"></a>Protokollok elleni támadások

A protokoll megtámadja a cél alkalmazás-protokollokat. Megpróbálják felhasználni az összes rendelkezésre álló erőforrást az infrastruktúra-eszközök, például a tűzfalak, az alkalmazások kiszolgálói és a terheléselosztó számára. A protokollok helytelenül formázott vagy protokoll-rendellenességeket tartalmazó csomagokat használnak. Ezek a támadások nagy számú nyílt kérelem küldésével működnek, amelyeket a kiszolgálók és más kommunikációs eszközök válaszolnak, és várniuk kell a csomagok megválaszolására. A cél megpróbál válaszolni a megnyitott kérelmekre, és végül a rendszerösszeomlást okozza.

A protokoll alapú DDoS támadás leggyakoribb példája a TCP SYN FLOOD. Ebben a támadásban a TCP SYN-kérelmek egymást követő száma megpróbál elárasztani egy célt. A cél az, hogy a cél nem válaszol. Az 2016-es DIN kimaradás az alkalmazási rétegbeli támadástól eltekintve olyan TCP SYN-árvizekből áll, amelyek a DIN DNS-kiszolgálóinak 53-es portját célozzák meg.

### <a name="resource-attacks"></a>Erőforrás-támadások

Az erőforrás-támadások célja az alkalmazás rétege. A rendszer kihasználása érdekében a háttérbeli folyamatokat indítják el. Az erőforrás-támadások a normál, de a kiszolgáló felé irányuló CPU-igényű lekérdezésekkel való visszaélést okozó forgalomhoz vezethetnek. A kipufogógáz-erőforrásokhoz szükséges forgalom mennyisége alacsonyabb, mint a más típusú támadások. Egy erőforrás-támadás forgalmát nem lehet megkülönböztetni a legitim forgalomtól, így nehezen észlelhető. A leggyakoribb erőforrás-támadások a HTTP/HTTPS-és DNS-szolgáltatásokon alapulnak.

## <a name="shared-responsibility-in-the-cloud"></a>Megosztott felelősség a felhőben

A részletes védelmi stratégia segíti a különböző támadások sokféleségének és kifinomultságának leküzdését. A biztonság az ügyfél és a Microsoft között megosztott felelősség. A Microsoft ezt a [közös felelősségi modellt](https://azure.microsoft.com/blog/microsoft-incident-response-and-shared-responsibility-for-cloud-computing/)hívja meg. Az alábbi ábra a felelősségi kört mutatja be:

![Az ügyfél és az Azure feladatai](./media/ddos-best-practices/image2.png)

Az Azure-ügyfelek igénybe vehetik a Microsoft ajánlott eljárásainak áttekintését, és olyan globálisan elosztott alkalmazások létrehozását, amelyek meghibásodásra lettek tervezve és tesztelve.

## <a name="fundamental-best-practices"></a>Alapvető ajánlott eljárások

A következő fejezetekben részletes útmutatást talál a DDoS-rugalmas szolgáltatások Azure-beli létrehozásához.

### <a name="design-for-security"></a>Biztonság – tervezés

Győződjön meg arról, hogy a biztonság az alkalmazás teljes életciklusa alatt, a tervezéstől és a megvalósítástól az üzembe helyezésig és a műveletekig prioritás. Az alkalmazások olyan hibával rendelkezhetnek, amely lehetővé teszi, hogy viszonylag alacsony mennyiségű kérést használjon az erőforrások meghívására, ami a szolgáltatás kimaradását eredményezi. 

A Microsoft Azureon futó szolgáltatások védelmének elősegítése érdekében érdemes megismernie az alkalmazás architektúráját, és a [szoftver minőségének öt pillérére](/azure/architecture/guide/pillars)kell összpontosítania.
Ismernie kell a jellemző adatforgalmi köteteket, a kapcsolati modellt az alkalmazás és más alkalmazások között, valamint a nyilvános interneten elérhető szolgáltatási végpontokat.

Fontos, hogy az alkalmazások elég rugalmasak legyenek ahhoz, hogy az alkalmazásra irányuló szolgáltatásmegtagadás ne legyen a legfontosabb. A biztonság és az adatvédelem az Azure platformra épül, a [biztonsági fejlesztési életciklussal (SDL)](https://www.microsoft.com/sdl/default.aspx)kezdve. Az SDL minden fejlesztési fázisban kezeli a biztonságot, és gondoskodik arról, hogy az Azure-t folyamatosan frissítsék, hogy még biztonságosabb legyen.

### <a name="design-for-scalability"></a>Skálázhatóság kialakítása

A méretezhetőség azt szemlélteti, hogy a rendszer milyen jól tudja kezelni a megnövekedett terhelést. Meg kell terveznie, hogy [](/azure/architecture/guide/design-principles/scale-out) az alkalmazások horizontálisan méretezhetők legyenek, hogy megfeleljenek egy felerősített terhelés igényének, különösen a DDOS-támadások esetén. Ha az alkalmazása egy szolgáltatás egyetlen példányán múlik, akkor az egyetlen meghibásodási pontot hoz létre. A több példány kiépítés révén a rendszerek rugalmasabbak és méretezhetők.

[Azure app Service](/azure/app-service/app-service-value-prop-what-is)esetén válasszon ki egy olyan [app Service tervet](/azure/app-service/overview-hosting-plans) , amely több példányt is kínál. Az Azure Cloud Services esetében konfigurálja az egyes szerepköröket [több példány](/azure/cloud-services/cloud-services-choose-me)használatára. Az [Azure Virtual Machines](/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)esetén győződjön meg arról, hogy a virtuális gép (VM) architektúrája több virtuális gépet tartalmaz, és hogy minden virtuális gép egy [rendelkezésre állási csoportba](/azure/virtual-machines/virtual-machines-windows-manage-availability)tartozik. Javasoljuk, hogy használjon [virtuálisgép-méretezési csoportokat](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview) az automatikus skálázási képességekhez.

### <a name="defense-in-depth"></a>Védelem részletesen

A védelem részletesen a különböző védelmi stratégiák használatával kezelhető a kockázatkezelés. Az alkalmazásokban a biztonsági védelem egyre csökkenti a sikeres támadás lehetőségét. Javasoljuk, hogy az Azure platform beépített képességeinek használatával hozzon létre biztonságos terveket az alkalmazásaihoz.

Például a támadás kockázata az alkalmazás méretével (felületével)nő. Az engedélyezési lista használatával csökkentheti a felületet a kihelyezett IP-címtartomány és a terheléselosztó által nem szükséges portok ([Azure Load Balancer](/azure/load-balancer/load-balancer-get-started-internet-portal) és az [Azure Application Gateway](/azure/application-gateway/application-gateway-create-probe-portal)) bezárásához. A [hálózati biztonsági csoportok (NSG)](/azure/virtual-network/security-overview) egy másik módszer a támadási felület csökkentésére.
A [szolgáltatási címkék](/azure/virtual-network/security-overview#service-tags) és az [alkalmazás-biztonsági csoportok](/azure/virtual-network/security-overview#application-security-groups) használatával csökkentheti a biztonsági szabályok létrehozásának összetettségét, és konfigurálhatja a hálózati biztonságot az alkalmazások struktúrájának természetes kiterjesztéseként.

Amikor csak lehetséges, üzembe kell helyeznie az Azure-szolgáltatásokat egy [virtuális hálózaton](/azure/virtual-network/virtual-networks-overview) . Ez a gyakorlat lehetővé teszi a szolgáltatási erőforrások számára, hogy magánhálózati IP-címeken keresztül kommunikáljanak egymással. A virtuális hálózatról származó Azure-szolgáltatási forgalom alapértelmezés szerint a nyilvános IP-címeket használja forrás IP-címként. A [szolgáltatási végpontok](/azure/virtual-network/virtual-network-service-endpoints-overview) használatával a szolgáltatás forgalmát úgy fogja váltani, hogy a virtuális hálózati magánhálózati címeket használják forrás IP-címként, amikor egy virtuális hálózatról érik el az Azure-szolgáltatást.

Gyakran tekintjük meg az ügyfelek helyszíni erőforrásait az Azure-beli erőforrásaikkal együtt. Ha helyszíni környezetet csatlakoztat az Azure-hoz, javasoljuk, hogy csökkentse a helyszíni erőforrások a nyilvános interneten való használatát. Az Azure-ban a jól ismert nyilvános entitásokat az Azure-ban üzembe helyezheti az Azure Scale és Advanced DDoS Protection képességeivel. Mivel ezek a nyilvánosan elérhető entitások gyakran a DDoS-támadások célpontja, az Azure-ban való üzembe helyezésük csökkenti a helyszíni erőforrásokra gyakorolt hatást.

## <a name="azure-offerings-for-ddos-protection"></a>Azure-ajánlatok DDoS-védelemhez

Az Azure két DDoS szolgáltatással rendelkezik, amelyek védelmet biztosítanak a hálózati támadásoktól (3. réteg és 4. réteg): Alapszintű és DDoS Protection standard DDoS Protection. 

### <a name="ddos-protection-basic"></a>Alapszintű DDoS Protection

Alapértelmezés szerint az alapszintű védelem az Azure-ba van integrálva, felár nélkül. A globálisan üzembe helyezett Azure-hálózat skálázása és kapacitása védelmet nyújt a közös hálózati rétegbeli támadásokkal szemben a folyamatos forgalom monitorozása és a valós idejű mérséklés révén. DDoS Protection alapszintű beállításhoz nincs szükség felhasználói konfigurációra vagy alkalmazás módosítására. DDoS Protection alapszintű, így biztosítható az összes Azure-szolgáltatás, beleértve a Pásti-szolgáltatásokat, mint a Azure DNS.

![Az Azure-hálózat térképes ábrázolása, a "globális DDoS-mérséklés" és a "vezető DDoS-mérséklési kapacitás" szöveggel](./media/ddos-best-practices/image3.png)

Az Azure-beli alapszintű DDoS Protection szoftveres és hardveres összetevőket is tartalmaz. A szoftveres vezérlő síkja határozza meg, hogy hol, hol és milyen típusú forgalmat kell vezérelni a támadási forgalom elemzését és eltávolítását szolgáló hardveres készülékeken keresztül. A vezérlő síkja ezt a döntést egy infrastruktúra-szintű DDoS Protection *házirend*alapján hozza meg. Ez a szabályzat statikusan van beállítva és univerzálisan alkalmazható az összes Azure-ügyfélre.

A DDoS Protection házirend például megadja, hogy a védelem milyen adatforgalom esetén *aktiválódik.* (Azaz a bérlő forgalmát át kell irányítani a kimosó berendezésekkel.) A szabályzat ezután meghatározza, hogy a kimosó berendezések milyen módon *csökkentsék* a támadást.

A Azure DDoS Protection alapszintű szolgáltatás az Azure platform infrastruktúrájának és védelmének védelmét célozza. A szolgáltatás csökkenti a forgalmat, ha az nagyobb mértékben befolyásolja a több ügyfelet több-bérlős környezetben is. Nem biztosít riasztást vagy ügyfél által testreszabott szabályzatot.

### <a name="ddos-protection-standard"></a>DDoS Protection Standard

A standard szintű védelem továbbfejlesztett DDoS-elhárítási funkciókat biztosít. A rendszer automatikusan hangolja az adott Azure-erőforrások védelmére egy virtuális hálózaton. A védelem egyszerűen engedélyezhető bármely új vagy meglévő virtuális hálózaton, és nem igényel alkalmazás-vagy erőforrás-módosítást. Az alapszintű szolgáltatás számos előnnyel jár, beleértve a naplózást, a riasztásokat és a telemetria is. A következő részekben a Azure DDoS Protection standard szolgáltatás fő funkcióit vázoljuk.

#### <a name="adaptive-real-time-tuning"></a>Adaptív valós idejű hangolás

A Azure DDoS Protection alapszintű szolgáltatás segíti az ügyfeleket, és megakadályozza a más ügyfelekkel szembeni hatásokat. Ha például egy szolgáltatás kiépítése egy olyan, a megbízható bejövő forgalomra jellemző kötetre vonatkozik, amely kisebb, mint az infrastruktúra-szintű DDoS Protection házirend kiváltási *aránya* , akkor előfordulhat, hogy az ügyfél erőforrásainak DDoS-támadása nem lesz észrevehetetlen. Általánosságban elmondható, hogy a legutóbbi támadások (például a többvektoros DDoS) összetettsége és a bérlők alkalmazásspecifikus viselkedése az ügyfélre vonatkozó, testreszabott védelmi szabályzatokat hívja meg. A szolgáltatás két bepillantással hajtja végre ezt a testreszabást:

- A 3. és a 4. rétegbeli ügyfélen belüli (/IP-) forgalom automatikus megtanulása.

- A téves pozitív érték minimalizálása, figyelembe véve, hogy az Azure mérete lehetővé teszi, hogy jelentős mennyiségű forgalmat vegyen fel.

![A DDoS Protection standard működésének diagramja a "szabályzat létrehozása" körben](./media/ddos-best-practices/image5.png)

#### <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>DDoS Protection telemetria, figyelés és riasztások

DDoS Protection a standard szintű telemetria a DDoS [](/azure/azure-monitor/overview) -támadás idejére Azure Monitorn keresztül teszi elérhetővé. A riasztásokat konfigurálhatja a DDoS Protection által használt Azure Monitor metrikák bármelyikéhez. A splunk (Azure Event Hubs), a Azure Monitor naplók és az Azure Storage szolgáltatással integrálhatja a naplózást a Azure Monitor Diagnostics felületén keresztül a speciális elemzésekhez.

##### <a name="ddos-mitigation-policies"></a>DDoS-mérséklési szabályzatok

A Azure Portal válassza a > **metrikák** **figyelése**elemet. A **metrikák** ablaktáblán válassza ki az erőforráscsoportot, válasszon ki egy **nyilvános IP-cím**típusú erőforrás-típust, és válassza ki az Azure nyilvános IP-címét. A DDoS-metrikák az **elérhető metrikák** ablaktáblán láthatók.

DDoS Protection a standard három, a védett erőforrás minden nyilvános IP-címéhez (TCP SYN, TCP és UDP) tartozó, a DDoS-t engedélyező virtuális hálózatban található, három alapszintű kockázatcsökkentő szabályzatot alkalmaz. A szabályzat küszöbértékeit úgy tekintheti meg, ha kiválasztja a metrika **bejövő csomagjait a DDoS-mérséklés**elindításához.

![Elérhető metrikák és mérőszámok diagramja](./media/ddos-best-practices/image7.png)

A házirend küszöbértékei automatikusan konfigurálva vannak a gépi tanuláson alapuló hálózati forgalom profilkészítése révén. A DDoS-megoldás csak akkor fordul elő a támadási IP-címeknél, ha túllépi a házirend küszöbértékét.

##### <a name="metric-for-an-ip-address-under-ddos-attack"></a>Egy IP-címhez tartozó metrika a DDoS-támadás alatt

Ha a nyilvános IP-cím támadás alatt áll, a rendszer a **DDoS-támadás alatt** lévő metrika értékét, vagy nem módosítja az 1 értéket, DDoS Protection a támadási forgalom mérséklését hajtja végre.

!["A DDoS-támadás vagy nem" mérőszám és diagram](./media/ddos-best-practices/image8.png)

Javasoljuk, hogy riasztást állítson be ezen a metrikán. Ekkor értesítést kap, ha a nyilvános IP-címen aktív DDoS-megoldás történik.

További információ: [Azure DDoS Protection standard kezelése a Azure Portal használatával](/azure/virtual-network/ddos-protection-manage-portal).

#### <a name="web-application-firewall-for-resource-attacks"></a>Webalkalmazási tűzfal erőforrás-támadásokhoz

Az alkalmazási rétegben az erőforrás-támadásokra adott esetben egy webalkalmazási tűzfalat (WAF) kell konfigurálnia a webalkalmazások biztonságossá tételéhez. A WAF ellenőrzi a bejövő webes forgalmat, hogy blokkolja az SQL-injektálásokat, a helyközi parancsfájlkezelést, a DDoS-t és a 7. rétegbeli támadásokat. Az Azure a [Application Gateway funkciójának](/azure/application-gateway/application-gateway-web-application-firewall-overview) köszönhetően lehetővé teszi a webalkalmazások központosított védelmét a gyakori támadások és biztonsági rések révén. Az Azure-partnerek további WAF ajánlatokat is biztosítanak, amelyek az [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1)-en keresztül megfelelőbbek lehetnek az igényeihez.

Még a webalkalmazási tűzfalak is hajlamosak a térfogatmérő és az állami kimerültség elleni támadásokra. Javasoljuk, hogy a WAF virtuális hálózatban engedélyezze a DDoS Protection standard használatát a térfogatáram-és protokoll-támadások elleni védelem érdekében. További információ: [DDoS Protection hivatkozási architektúrák](#ddos-protection-reference-architectures) szakasz.

### <a name="protection-planning"></a>Védelem tervezése

A tervezés és az előkészítés elengedhetetlen annak megismeréséhez, hogy a rendszer hogyan fog elvégezni a DDoS-támadások során. Az incidensek kezelésének megtervezése ennek a erőfeszítésnek a része.

Ha DDoS Protection standard szintű, győződjön meg arról, hogy az engedélyezve van az internetre irányuló végpontok virtuális hálózatán. A DDoS-riasztások konfigurálása segít folyamatosan figyelni az infrastruktúra esetleges támadásait. 

Az alkalmazásokat egymástól függetlenül kell figyelni. Egy alkalmazás normális viselkedésének megismerése. Felkészülés a műveletre, ha az alkalmazás a DDoS-támadás során nem a várt módon működik.

#### <a name="testing-through-simulations"></a>Tesztelés szimulációk használatával

Érdemes tesztelni a feltételezéseket arról, hogy a szolgáltatások hogyan reagálnak a támadásokra az időszakos szimulációk végrehajtásával. A tesztelés során ellenőrizze, hogy a szolgáltatások vagy alkalmazások továbbra is a várt módon működnek-e, és nincs-e fennakadás a felhasználói élményben. Azonosítsa a technológiai és a feldolgozási szempontból mutatkozó hiányosságokat, és foglalja bele őket a DDoS-válasz stratégiájába. Javasoljuk, hogy hajtsa végre az ilyen teszteket átmeneti környezetekben vagy nem csúcsidőben, hogy csökkentse az éles környezetre gyakorolt hatást.

A [BreakingPoint](https://www.ixiacom.com/products/breakingpoint-cloud) -felhővel együttműködve olyan felületet hoztunk létre, amelyben az Azure-ügyfelek a szimulációk DDoS Protection használatára képes nyilvános végpontokon keresztül hozhatnak létre forgalmat. A [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) szimulációs eszközzel a következőket végezheti el:

- Annak ellenőrzése, hogy a Azure DDoS Protection Hogyan védi az Azure-erőforrásokat a DDoS-támadásokkal szemben.

- Optimalizálja az incidensek megválaszolásának folyamatát a DDoS-támadás alatt.

- A DDoS-megfelelőség dokumentálása.

- A hálózati biztonsági csapatok betanítása.

A kiberbiztonsági folyamatos innovációt igényel a védelemben. Az Azure DDoS standard Protection egy olyan legkorszerűbb megoldás, amely hatékony megoldást kínál az egyre összetettebb DDoS-támadások enyhítésére.

## <a name="components-of-a-ddos-response-strategy"></a>A DDoS-reagálási stratégia összetevői

Az Azure-erőforrásokra irányuló DDoS-támadások általában minimális beavatkozást igényelnek a felhasználói szempontból. A DDoS-megoldásnak az incidensekre való reagálási stratégia részeként való beépítésével továbbra is csökkentheti az üzleti folytonosságra gyakorolt hatást.

### <a name="microsoft-threat-intelligence"></a>Microsoft Threat Intelligence

A Microsoft kiterjedt fenyegetést jelentő intelligenciával rendelkezik. Ez a hálózat egy olyan kiterjesztett biztonsági Közösség kollektív ismeretét használja, amely támogatja a Microsoft online szolgáltatások, a Microsoft partnerei és az internetes biztonsági Közösségen belüli kapcsolatokat. 

Kritikus infrastruktúra-szolgáltatóként a Microsoft korai figyelmeztetéseket kap a fenyegetésekkel kapcsolatban. A Microsoft a fenyegetési intelligenciát a online szolgáltatások és a globális ügyfélkörtől gyűjti. A Microsoft az összes fenyegetési intelligenciát visszahelyezi a Azure DDoS Protection termékekbe.

Emellett a Microsoft Digital Crimes Unit (DCU) támadó stratégiákat hajt végre a botneteken. A botnetek a DDoS-támadások irányításának és vezérlésének gyakori forrása.

### <a name="risk-evaluation-of-your-azure-resources"></a>Az Azure-erőforrások kockázati kiértékelése

Fontos, hogy a kockázat hatókörét a DDoS-támadásoktól folyamatosan megértse. Rendszeresen Kérdezzen rá: 
- Milyen új, nyilvánosan elérhető Azure-erőforrásokra van szükségük a védelemhez?

- Van egy meghibásodási pont a szolgáltatásban? 

- Hogyan különíthetők el a szolgáltatások a támadás hatásának korlátozásával, miközben a szolgáltatások az érvényes ügyfelek számára is elérhetővé válnak?

- Vannak olyan virtuális hálózatok, amelyeken engedélyezve van a DDoS Protection standard, de nem? 

- A szolgáltatásom aktív/aktív a több régióban feladatátvételsel?

### <a name="customer-ddos-response-team"></a>Ügyfél DDoS-válasz csapata

A DDoS-válaszok csapatának létrehozása kulcsfontosságú lépés a támadás gyors és hatékony megválaszolásában. Azonosítsa a szervezeten belüli partnereket, akik a tervezést és a végrehajtást is felügyelik. A DDoS-válasz csapatának alaposan meg kell ismernie a Azure DDoS Protection standard szolgáltatást. Győződjön meg arról, hogy a csapat azonosíthatja és enyhítheti a támadásokat a belső és külső ügyfelekkel, például a Microsoft támogatási csapatával.

Azt javasoljuk, hogy a DDoS-válasz csapata a szolgáltatás rendelkezésre állásának és a folytonosság megtervezésének szokásos részeként használja a szimulációs gyakorlatokat. A gyakorlatoknak tartalmaznia kell a méretezési teszteket.

### <a name="alerts-during-an-attack"></a>Riasztások támadás közben

Azure DDoS Protection standard azonosítja és csökkenti a DDoS-támadásokat felhasználói beavatkozás nélkül. Ha értesítést szeretne kapni, ha a védett nyilvános IP-címek esetében aktív megoldás van érvényben, beállíthatja, hogy a metrika a DDoS-támadás alatt [legyen,](/azure/virtual-network/ddos-protection-manage-portal) **vagy nem**. Dönthet úgy, hogy riasztásokat hoz létre a többi DDoS mérőszámhoz a támadás méretezésének, az eldobott forgalomnak és egyéb részleteknek a megismeréséhez.

#### <a name="when-to-contact-microsoft-support"></a>Kapcsolatfelvétel a Microsoft ügyfélszolgálatával

- A DDoS-támadások során azt tapasztalja, hogy a védett erőforrás teljesítménye súlyosan csökken, vagy az erőforrás nem érhető el.

- Úgy gondolja, hogy a DDoS Protection szolgáltatás nem a várt módon viselkedik. 

  A DDoS Protection szolgáltatás csak akkor indul el, ha a DDoS-csökkentést kiváltó metrikai **házirend (TCP/TCP SYN/UDP)** kisebb, mint a védett nyilvános IP-erőforráson fogadott forgalom.

- Olyan vírusos eseményt tervez, amely jelentősen növeli a hálózati forgalmat.

- Egy színész azzal fenyegetőzött, hogy a DDoS-támadást indította el az erőforrásaival szemben.

- Ha meg kell adnia egy IP-címet vagy egy IP-címtartományt Azure DDoS Protection standard szintről. Gyakori forgatókönyv az IP-címek engedélyezési módja, ha a forgalmat egy külső felhő WAF irányítja át az Azure-ba. 

A kritikus fontosságú üzleti hatású támadások esetében hozzon létre egy súlyossági szintű [támogatási jegyet](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

### <a name="post-attack-steps"></a>Támadás utáni lépések

Mindig jó stratégiát kell elvégeznie a támadás után, és szükség szerint módosítania kell a DDoS-reagálási stratégiát. Megfontolandó szempontok:

- Megszakadt a szolgáltatás vagy a felhasználói élmény a skálázható architektúra hiánya miatt?

- Mely alkalmazások vagy szolgáltatások lettek a leginkább elszenvedve?

- Mennyire hatékony volt a DDoS-reagálási stratégia, és hogyan lehet javítani?

Ha azt gyanítja, hogy a DDoS-támadás alá tartozik, akkor a normál Azure-támogatási csatornákon keresztül is kiterjesztheti.

## <a name="ddos-protection-reference-architectures"></a>DDoS Protection hivatkozási architektúrák

DDoS Protection standard a [virtuális hálózatban üzembe helyezett szolgáltatásokhoz](/azure/virtual-network/virtual-network-for-azure-services)lett tervezve. Más szolgáltatások esetében az alapértelmezett DDoS Protection alapszintű szolgáltatás érvényes. A következő hivatkozási architektúrákat forgatókönyvek rendezik, és az architektúra mintázatai együtt vannak csoportosítva.

### <a name="virtual-machine-windowslinux-workloads"></a>Virtuális gépek (Windows/Linux) számítási feladatok

#### <a name="application-running-on-load-balanced-vms"></a>Elosztott terhelésű virtuális gépeken futó alkalmazás

Ez a hivatkozási architektúra bevált eljárásokat mutat be több Windows rendszerű virtuális gép egy terheléselosztó mögötti méretezési csoportba való futtatásához a rendelkezésre állás és a méretezhetőség javítása érdekében. Ez az architektúra bármely állapot nélküli számítási feladathoz használható, például egy webkiszolgálóhoz.

![Elosztott terhelésű virtuális gépeken futó alkalmazás hivatkozási architektúrájának ábrája](./media/ddos-best-practices/image9.png)

Ebben az architektúrában a számítási feladatok több virtuálisgép-példányon oszlanak el. Egyetlen nyilvános IP-cím van, és az internetes forgalom a virtuális gépek számára a terheléselosztó segítségével történik. DDoS Protection standard engedélyezve van az Azure-beli (internetes) terheléselosztó virtuális hálózatán, amelyhez hozzá van rendelve a nyilvános IP-cím.

A terheléselosztó elosztja a bejövő internetes kérelmeket a virtuálisgép-példányok számára. A virtuálisgép-méretezési csoportok lehetővé teszik a virtuális gépek számának manuális méretezését vagy kikapcsolását, vagy automatikusan előre meghatározott szabályok alapján. Ez akkor fontos, ha az erőforrás DDoS-támadás alatt áll. További információ erről a hivatkozási architektúráról: [ebben a cikkben](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm).

#### <a name="application-running-on-windows-n-tier"></a>Alkalmazás Windows N szintű rendszeren

Egy N szintű architektúra számos módon implementálható. Az alábbi ábrán egy tipikus, háromrétegű webalkalmazás látható. Ez az architektúra a [elosztott terhelésű virtuális gépek a méretezhetőség és a rendelkezésre állás érdekében történő futtatását](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm)ismerteti. A webes és üzleti szintek elosztott terhelésű virtuális gépeket használnak.

![A Windows N szintű platformon futó alkalmazások hivatkozási architektúrájának ábrája](./media/ddos-best-practices/image10.png)

Ebben az architektúrában a DDoS Protection standard engedélyezve van a virtuális hálózaton. A virtuális hálózatban lévő összes nyilvános IP-cím DDoS-védelmet kap a 3. és a 4. rétegben. A 7. rétegbeli védelem esetében telepítse a Application Gatewayt a WAF SKU-ban. További információ erről a hivatkozási architektúráról: [ebben a cikkben](/azure/architecture/reference-architectures/virtual-machines-windows/n-tier).

#### <a name="paas-web-application"></a>PaaS webalkalmazás

Ez a hivatkozási architektúra egy Azure App Service alkalmazás egyetlen régióban történő futtatását mutatja be. Ez az architektúra bevált eljárásokat mutat be [Azure app Service](https://azure.microsoft.com/documentation/services/app-service/) és Azure SQL Databaset használó webalkalmazásokhoz. [](https://azure.microsoft.com/documentation/services/sql-database/)
A készenléti régió feladatátvételi forgatókönyvekhez van beállítva.

![A Pásti-webalkalmazások hivatkozási architektúrájának ábrája](./media/ddos-best-practices/image11.png)

Az Azure Traffic Manager a bejövő kérelmeket a régiók egyik régiójában Application Gateway irányítja. A normál működés során a a kérelmeket az aktív régióban lévő Application Gatewayra irányítja. Ha a régió elérhetetlenné válik, Traffic Manager átadja a feladatátvételt a készenléti régióban lévő Application Gatewaynak.

Az internetről a webalkalmazás felé irányuló összes forgalom a [Application Gateway nyilvános IP-címére](/azure/application-gateway/application-gateway-web-app-overview) van irányítva Traffic Manager használatával. Ebben az esetben az App Service (webalkalmazás) nem közvetlenül a külső felé irányul, és Application Gateway védi. 

Javasoljuk, hogy konfigurálja a Application Gateway WAF SKU-t (megakadályozza a módot) a 7. rétegbeli (HTTP/HTTPS/WebSocket) támadások elleni védelemhez. Emellett a webalkalmazások úgy vannak konfigurálva, hogy [csak a Application Gateway IP-címről érkező forgalmat fogadják el](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/) .

A hivatkozási architektúrával kapcsolatos további információkért tekintse meg [ezt a cikket](/azure/architecture/reference-architectures/app-service-web-app/multi-region).

### <a name="mitigation-for-non-web-paas-services"></a>A nem webes Pásti szolgáltatásainak enyhítése

#### <a name="hdinsight-on-azure"></a>HDInsight az Azure-ban

Ez a hivatkozási architektúra egy [Azure HDInsight-fürt](/azure/hdinsight/)DDoS Protection szabványának konfigurálását mutatja be. Győződjön meg arról, hogy a HDInsight-fürt egy virtuális hálózathoz van csatolva, és hogy a DDoS Protection engedélyezve van a virtuális hálózaton.

!["HDInsight" és "speciális beállítások" ablaktábla virtuális hálózati beállításokkal](./media/ddos-best-practices/image12.png)

![DDoS Protection engedélyezésének kiválasztása](./media/ddos-best-practices/image13.png)

Ebben az architektúrában az internetről érkező HDInsight-fürtre irányuló forgalmat a rendszer a HDInsight Gateway Load Balancerhez társított nyilvános IP-címhez irányítja. Az átjáró terheléselosztó ezt követően közvetlenül a főcsomópontokra vagy a feldolgozó csomópontokra küldi a forgalmat. Mivel DDoS Protection a standard engedélyezve van a HDInsight virtuális hálózaton, a virtuális hálózat összes nyilvános IP-címe lekéri a DDoS Protectiont a 3. és a 4. rétegben. Ez a hivatkozási architektúra kombinálható az N szintű és a többrégiós hivatkozási architektúrával is.

További információ erről a hivatkozási architektúráról: az [Azure-HDInsight kiterjesztése azure Virtual Network](/azure/hdinsight/hdinsight-extend-hadoop-virtual-network?toc=%2fazure%2fvirtual-network%2ftoc.json) -dokumentáció használatával.


> [!NOTE]
> A nyilvános IP-címmel rendelkező virtuális hálózatok PowerApps-vagy API-kezelésének Azure App Service Environmenti nem natív módon támogatottak.

## <a name="next-steps"></a>További lépések

* [Azure DDoS Protection termék lapja](https://azure.microsoft.com/services/ddos-protection/)

* [Azure DDoS Protection blog](https://aka.ms/ddosblog)

* [Azure DDoS Protection dokumentáció](/azure/virtual-network/ddos-protection-overview)

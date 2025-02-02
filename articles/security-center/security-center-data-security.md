---
title: Az Azure Security Center által nyújtott adatbiztonság | Microsoft Docs
description: Ez a dokumentum bemutatja, hogyan kezeli az Azure Security Center az adatokat, és hogyan gondoskodik a védelmükről.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 33f2c9f4-21aa-4f0c-9e5e-4cd1223e39d7
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: rkarlin
ms.openlocfilehash: cd91b83bc808d811fc50293fbf1726d609ad5b46
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65234081"
---
# <a name="azure-security-center-data-security"></a>Az Azure Security Center által nyújtott adatbiztonság
Az Azure Security Center biztonsággal kapcsolatos adatokat gyűjt és dolgoz fel (például konfigurációs információkat, metaadatokat, eseménynaplókat, összeomlási memóriaképeket és még sok mást), hogy segítsen az ügyfeleknek a fenyegetések megelőzésében, észlelésében és elhárításában. A Microsoft szigorú megfelelőségi és biztonsági szabályokat követ, a kódolástól kezdve egészen a szolgáltatások üzemeltetéséig.

Ez a cikk bemutatja, hogyan kezeli az Azure Security Center az adatokat, és hogyan gondoskodik a védelmükről.

## <a name="data-sources"></a>Adatforrások
Az Azure Security Center az alábbi forrásokból kapott adatok elemzésével biztosítja a biztonsági állapot áttekinthetőségét, azonosítja a biztonsági réseket, javításokat javasolj, valamint észleli az aktív fenyegetéseket:

- Azure-szolgáltatások: Használ, adott erőforrás-szolgáltatóval kommunikálva üzembe helyezett Azure-szolgáltatások konfigurációjával kapcsolatos információkat.
- Hálózati forgalom: Használja a hálózati forgalom metaadataiból a Microsoft, például a forrás és cél IP-portot, a csomagméretet és a hálózati protokoll mintát venni.
- Partneri megoldások: Integrált partneri megoldások, például tűzfalaktól és kártevőirtó megoldásoktól érkező biztonsági riasztásokat használja.
- A virtuális gépek és kiszolgálók: Konfigurációs és biztonsági eseményekkel, például a Windows esemény- és vizsgálati naplókat, IIS-naplók, syslog-üzeneteket és a virtuális gépekről származó összeomlási memóriaképeket kapcsolatos információkat használ. Ezenkívül a riasztások létrejöttekor az Azure Security Center pillanatfelvételt készíthet az érintett virtuálisgép-lemezről, és kinyerheti belőle a riasztással kapcsolatos gépösszetevőket (például a beállításjegyzék-fájlt vizsgálati célokra).


## <a name="data-protection"></a>Adatvédelem
**Az adatok elkülönítése**: Adatok van logikailag elkülönítve vannak tárolva a szolgáltatás egyes összetevőiben. Az összes adat szervezet szerint van megcímkézve. Ez a címkézés megmarad az adatok teljes életciklusa alatt, és a szolgáltatás minden rétegében érvényes.

**Adatelérési**: Biztonsági javaslatok és a lehetséges biztonsági fenyegetések kivizsgálása érdekében a Microsoft munkatársai hozzáférhetnek az Azure szolgáltatások, beleértve a összeomlási memóriaképek fájljait, folyamat-létrehozási események, Virtuálisgép-lemez pillanatképeihez és összetevőihez, által összegyűjtött vagy elemzett adatokat amelyek véletlenül tartalmazhatnak, ügyfél és a virtuális gépekről származó személyes adatokat. Betartjuk a [Microsoft Online Services és az adatvédelmi nyilatkozat](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) feltételeit, amelyek szerint a Microsoft nem használja fel az ügyféladatokat, és nem veszi igénybe azokat hirdetési vagy hasonló kereskedelmi célokra. Az ügyféladatokat szükség esetén csak arra használjuk, hogy biztosítsuk Önnek az Azure-szolgáltatásokat, beleértve a szolgáltatások nyújtásának megfelelő célokat is. Minden, az ügyféladatokhoz fűződő jog az Ön tulajdonában marad.

**Adatok használatával**: A Microsoft minták és több bérlő különböző bérlőknél észlelt fenyegetésekkel kapcsolatos Tudásbázis alkalmaz a használatával javíthatja a megelőzési és észlelési funkcióihoz; ismertetett adatvédelmi kötelezettségeinek megfelelően tesszük a [adatvédelmi nyilatkozat](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx).

## <a name="data-location"></a>Az adatok helye

**Saját munkaterület(ek)** : A munkaterület az alábbi Geók esetében van megadva, és az Azure virtuális gépek, beleértve az összeomlási memóriaképeket és bizonyos típusú riasztási adatokat gyűjtött adatokat a legközelebbi munkaterület vannak tárolva.

| Virtuális gép geo                              | Munkaterület geo |
|-------------------------------------|---------------|
| Egyesült Államok, Brazília, Dél-Afrika | Egyesült Államok |
| Kanada                              | Kanada        |
| Európa (kivéve az Egyesült Királyság)   | Európa        |
| Egyesült Királyság                      | Egyesült Királyság |
| Ázsia (India, japán, koreai, Kína kivételével)   | Ázsia és a Csendes-óceáni térség  |
| Korea                              | Ázsia és a Csendes-óceáni térség  |
| India                               | India         |
| Japán                               | Japán         |
| Kína                               | Kína         |
| Ausztrália                           | Ausztrália     |


A virtuálisgép-lemez pillanatképeit ugyanaz a tárfiók tárolja, mint a virtuálisgép-lemezt.

A más környezetben futó (pl. helyszíni) virtuális gépek és kiszolgálók esetében megadhatja a munkaterületet és régiót, ahol tárolni kívánja az összegyűjtött adatokat.

**Azure Security Center Storage**: Információ a biztonsági riasztásokat, beleértve a partnerriasztásokat, tárolt regionálisan függően a kapcsolódó Azure-erőforrás, mivel a biztonsági állapottal és javaslatokkal kapcsolatos információk tárolása központilag az Egyesült Államokban vagy Európa, az ügyfél helye szerint.
Az Azure Security Center ideiglenes másolatokat gyűjt az összeomlási memóriaképek fájljairól, és elemzi ezeket a biztonsági réseket kihasználó támadások és a sikeres feltörések nyomai után kutatva. Az Azure Security Center az elemzéseket ugyanazon a földrajzi helyen végzi el, ahol a munkaterület található, és az elemzés után törli az ideiglenes másolatokat.

A gépösszetevők tárolása központilag történik, ugyanabban a régióban, ahol a virtuális gép is található.


## <a name="managing-data-collection-from-virtual-machines"></a>Az adatgyűjtés kezelése a virtuális gépeken

Ha bekapcsolja a Security Centert az Azure-ban, az adatgyűjtés bekapcsolódik minden előfizetésénél. Az előfizetések adatgyűjtését az Azure Security Center Biztonsági szabályzat részén is bekapcsolhatja. Amikor az adatgyűjtés be van kapcsolva, az Azure Security Center létrehozza a Microsoft Monitoring Agentet minden meglévő és újonnan létrehozott Azure-beli támogatott virtuális gépen.
A Microsoft Monitoring Agent megkeresi a biztonsághoz kapcsolódó különböző konfigurációkat, és eseményként felveszi őket a [Windows esemény-nyomkövetés](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) nyomkövetéseibe. Az operációs rendszer emellett az eseménynaplóba írandó eseményeket hoz létre a gép futtatása során. A gyűjtött adatok például a következők: az operációs rendszer típusa és verziója, az operációs rendszer naplói (Windows-eseménynaplók), a futó folyamatok, a gép neve, az IP-címek, a bejelentkezett felhasználó és a bérlő azonosítója. A Microsoft Monitoring Agent kiolvassa az eseménynapló-bejegyzéseket és az ETW-nyomkövetéseket, és átmásolja őket az Ön munkaterületeire elemzés céljából. A Microsoft Monitoring Agent az összeomlási memóriaképeket is átmásolja a munkaterületeire, valamint engedélyezi a folyamat-létrehozási eseményeket és lehetővé teszi a parancssori naplózást.

Ha az Azure Security Center ingyenes verzióját használja, le is tilthatja a virtuális gépekről történő adatgyűjtést a biztonsági szabályzatban. A Standard szintű előfizetésekhez az adatgyűjtés kötelező. A virtuálisgép-lemez pillanatképeinek és összetevőinek gyűjtése akkor is engedélyezve lesz, ha letiltotta az adatgyűjtést.

## <a name="data-consumption"></a>Adathasználat

Az ügyfelek különböző adatstreamekből származó Security Center-adatokat használhatnak, az alábbiak szerint:

* **Azure-tevékenység**: az összes biztonsági riasztás, jóváhagyott [igény szerinti](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) Security Center-kérelmek és az [adaptív alkalmazásvezérlők](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application) által létrehozott összes riasztás.
* **Az Azure Monitor naplóira**: összes biztonsági riasztás.


> [!NOTE]
> A biztonsági javaslatok REST API-n keresztül is feldolgozhatók. További információk: [A biztonsági erőforrás-szolgáltató REST API-ja – referencia](https://msdn.microsoft.com/library/mt704034(Azure.100).aspx).

## <a name="see-also"></a>Lásd még
Ebből a dokumentumból megtudta, hogyan kezeli az Azure Security Center az adatokat, és hogyan gondoskodik azok védelméről. Az Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md) – Az Azure Security Center tervezésével, valamint a bevezetés során fontos elemekkel kapcsolatos útmutató.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md) – Útmutató az Azure-erőforrások állapotának megfigyeléséhez.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md) – Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
* [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center – gyakran ismételt kérdések) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

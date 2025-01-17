---
title: Mi az az Azure Sentinel Preview? | Microsoft Docs
description: Tudjon meg többet az Azure Sentinelről, annak főbb képességeiről és működéséről.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 10cce91a-421b-4959-acdf-7177d261f6f2
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 15cf770a372c9a1386dd0293abeac01fd3cacf63
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779749"
---
# <a name="what-is-azure-sentinel-preview"></a>Mi az az Azure Sentinel Preview?

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A Microsoft Azure Sentinel egy skálázható, Felhőbeli natív, **biztonsági információkkal kapcsolatos esemény-felügyeleti (SIEM)** és biztonsági előkészítési **(felszárnyaló)** megoldás. Az Azure Sentinel intelligens biztonsági elemzési és fenyegetésekkel kapcsolatos intelligenciát biztosít a vállalaton belül, így egyetlen megoldást kínál a riasztások észlelésére, a fenyegetések láthatóságára, a proaktív vadászatra és a fenyegetésekre való reagálásra. 

Az Azure Sentinel az egész vállalatra kiterjedő, az egyre kifinomultabb támadásokat, a riasztások mennyiségét és a hosszú távú megoldási időkereteket is csökkenti.

- Minden felhasználó, eszköz, alkalmazás és infrastruktúra, a helyszínen és több felhőben is **gyűjthet adatokat** a felhőben. 

- A **korábban nem észlelt fenyegetések észlelése**és a téves pozitív állapotok csökkentése a Microsoft Analytics és a páratlan veszélyforrások elleni intelligenciával. 

- Megvizsgálhatja a **fenyegetéseket a mesterséges intelligenciával**, és nagy mennyiségű gyanús tevékenységet is felhasználhat, és a Microsoftnál is kihasználhatja a számítógépes biztonsági munkát. 

- Az incidensekre való **gyors reagálás** a közös feladatok beépített összehangolása és automatizálása révén.


![Azure Sentinel alapképességek](./media/overview/core-capabilities.png)

A meglévő Azure-szolgáltatások teljes skáláját kiépítve az Azure Sentinel natív módon tartalmazza a bevált alapokat, például a Log Analytics és a Logic Apps. Az Azure Sentinel az AI-vel gazdagítja a nyomozást és az észlelést, és biztosítja a Microsoft fenyegetés-felderítési streamjét, és lehetővé teszi a saját veszélyforrások felderítését. 

 
## <a name="connect-to-all-your-data"></a>Kapcsolódás az összes adattal

Az Azure Sentinelhez először a [biztonsági forrásokhoz](connect-data-sources.md)kell csatlakoznia. Az Azure Sentinel számos, a Microsoft-megoldások számára elérhető összekötővel rendelkezik, és valós idejű integrációt biztosít, beleértve a Microsoft veszélyforrások elleni védelmi megoldásait és Microsoft 365 forrásait, beleértve az Office 365, az Azure AD, az Azure ATP és a Microsoft Cloud App Security és így tovább. Emellett beépített összekötők találhatók a nem Microsoft-megoldások szélesebb körű biztonsági ökoszisztémájában. Az adatforrások az Azure Sentinel szolgáltatással való összekapcsolásához használhatja a Common Event Format, a syslog vagy a REST-API-t is.  

![Adatgyűjtők](./media/collect-data/collect-data-page.png)

## <a name="dashboards"></a>Irányítópultok

Az adatforrások csatlakoztatása után kiválaszthatja az adatforrásokból felhasználható, szakszerűen [létrehozott irányítópultok](quickstart-get-visibility.md#dashboards) gyűjteményét. Az irányítópultok teljes mértékben testreszabhatók – hozzáadhat saját logikát vagy módosíthat lekérdezéseket, vagy létrehozhat egy teljesen új irányítópultot is.

Az irányítópultok interaktív vizualizációt biztosítanak a speciális elemzések segítségével, így a biztonsági elemzők könnyebben megismerhetik, hogy mi folyik a támadás során. A vizsgálati eszközök lehetővé teszik, hogy bármilyen adatmezőből, bármilyen adatokból kiépítse a fenyegetéseket, így gyorsan fejlesztheti a veszélyforrásokat. 

![Irányítópultok](./media/overview/dashboards.png)

## <a name="analytics"></a>Elemzés

A zaj csökkentése és a riasztások számának csökkentése érdekében az Azure Sentinel az elemzések segítségével összekapcsolja [](tutorial-detect-threats.md)a riasztásokat az incidensekkel. Az incidensek a kapcsolódó riasztások csoportjai, amelyek együttesen a megvizsgálható és oldható fenyegetést jelenthetnek. Használhatja a beépített korrelációs szabályokat, vagy kiindulási pontként használhatja a saját létrehozásához. Az Azure Sentinel gépi tanulási szabályokat is biztosít a hálózat működésének feltérképezéséhez, majd az erőforrások közötti rendellenességek kereséséhez. Ezek az elemzések összekapcsolják a pontokat a különböző entitások alacsony hűséggel kapcsolatos riasztásait a lehetséges magas szintű biztonsági incidensekkel.

![Esetekben](./media/overview/cases.png)

## <a name="user-analytics"></a>Felhasználói elemzések

A gépi tanulás (ML) és a [felhasználói elemzések](user-analytics.md)natív integrációja révén az Azure Sentinel gyorsan képes észlelni a fenyegetéseket. Az Azure Sentinel zökkenőmentesen integrálható az Azure komplex veszélyforrások elleni védelemmel, hogy elemezze a felhasználói viselkedést, és rangsorolja azokat a felhasználókat, akiket a riasztások alapján kell megvizsgálni, valamint a gyanús tevékenységi mintákat az Azure Sentinel és a Microsoft 365 között.

![Felhasználói elemzések](./media/overview/user-analytics.png)


## <a name="security-automation--orchestration"></a>Biztonsági automatizálás &

Automatizálhatja gyakori feladatait, és leegyszerűsítheti a [biztonsági](tutorial-respond-threats-playbook.md) előkészítést olyan forgatókönyvekkel, amelyek integrálva vannak az Azure-szolgáltatásokkal, valamint a meglévő eszközökkel. A Azure Logic Apps alapjaira épülő Azure Sentinel automatizálási és összehangoló megoldása olyan jól bővíthető architektúrát biztosít, amely lehetővé teszi a skálázható automatizálást új technológiák és fenyegetések megjelenése mellett. Ha Azure Logic Apps segítségével szeretne forgatókönyveket felépíteni, a beépített forgatókönyvek gyűjteménye közül választhat. Ezek közé tartoznak a 200-as és az Azure functions szolgáltatásokhoz [csatlakozó összekötők](https://docs.microsoft.com/azure/connectors/apis-list) . Az összekötők lehetővé teszik, hogy bármilyen egyéni logikát alkalmazzon a Code, a ServiceNow, a JIRA, a zendesk, a HTTP-kérelmek, a Microsoft Teams, a Slack, a Windows Defender ATP és a Cloud App Security használatával.

Ha például a ServiceNow jegyrendszer használatát használja, a Azure Logic Apps segítségével automatizálhatja a munkafolyamatokat, és minden alkalommal megnyithat egy jegyet a ServiceNow-ben egy adott esemény észlelésekor.

![Forgatókönyvek](./media/tutorial-respond-threats-playbook/logic-app.png)



## <a name="investigation"></a>Vizsgálat

Az Azure Sentinel [Deep vizsgálati](tutorial-investigate-cases.md) eszközei segítenek megérteni a hatókört, és megkeresni az esetleges biztonsági fenyegetések gyökerét. Kiválaszthat egy entitást az interaktív gráfon, amely egy adott entitás érdekes kérdéseit kérdezi le, és részletezi az entitást és annak kapcsolatait, hogy a fenyegetés kiváltó okát is megszerezze. 

![Vizsgálat](./media/overview/investigation.png)


## <a name="hunting"></a>Veszélyforrás-keresés

Az Azure Sentinel [hatékony vadászati keresési és lekérdezési eszközeit](hunting.md)használja a Mitre Framework alapján, amely lehetővé teszi, hogy a riasztás elindítása előtt proaktív módon vadászjon a biztonsági fenyegetésekre a szervezet adatforrásaiban. Miután felfedezte, hogy melyik vadászati lekérdezés nagy értékű betekintést nyújt a lehetséges támadásokra, létrehozhat egyéni észlelési szabályokat is a lekérdezés alapján, és felhasználhatja ezeket az elemzéseket riasztásként a biztonsági incidensek válaszadói számára. A vadászat közben könyvjelzőket hozhat létre az érdekes eseményekhez, így később visszatérhet hozzájuk, megoszthatja őket másokkal, és csoportosíthatja azokat más korrelációs eseményekkel, hogy a vizsgálat során vonzó esetet hozzon létre.

![Veszélyforrás-keresés](./media/overview/hunting.png)

## <a name="community"></a>Közösség

Az Azure Sentinel-Közösség hatékony erőforrás a fenyegetések észleléséhez és automatizálásához. A Microsoft biztonsági elemzői folyamatosan új irányítópultokat, forgatókönyveket, vadászati lekérdezéseket és egyebeket hoznak létre és vesznek fel, és a Közösségbe is felhasználhatják őket a környezetében való használatra. A Private Community GitHub-adattárból letölthető mintát készíthet [](https://aka.ms/asicommunity) egyéni irányítópultok, vadászati lekérdezések, jegyzetfüzetek és forgatókönyvek létrehozásához az Azure Sentinel számára. 

![Közösség](./media/overview/community.png)

## <a name="next-steps"></a>További lépések

- Az Azure Sentinel megkezdéséhez szüksége lesz egy előfizetésre Microsoft Azure. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes próbaverzióra](https://azure.microsoft.com/free/).
- Ismerje meg, hogyan hozhatja be [adatait az Azure sentinelbe](quickstart-onboard.md), és hogyan tekintheti [meg az adatait és a lehetséges fenyegetéseket](quickstart-get-visibility.md).

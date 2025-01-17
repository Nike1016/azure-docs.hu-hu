---
title: Szándékok – LUIS
titleSuffix: Azure Cognitive Services
description: Egyetlen szándék a felhasználó által végrehajtani kívánt feladatot vagy műveletet jelöli. Egy célra vagy a cél-ben a felhasználó utterance (kifejezés) fejezzük ki. Meghatározhatja egy adott szándékot megfelelő műveleteket hajthat végre felhasználókat szeretné állítani az alkalmazásban.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: bb7fa9d930f4c1ab3c241048804060e17fe5a8e4
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619921"
---
# <a name="concepts-about-intents-in-your-luis-app"></a>A LUIS-alkalmazásban található szándékokkal kapcsolatos fogalmak

Megjelölésű jelöli egy feladatot vagy műveletet a felhasználó szeretné végrehajtani. Egy célra vagy a felhasználó kifejezett célja [utterance (kifejezés)](luis-concept-utterance.md).

Meghatározhatja egy adott szándékot megfelelő műveleteket hajthat végre felhasználókat szeretné állítani az alkalmazásban. Például egy utazási alkalmazás számos leképezések határozza meg:

Utazás alkalmazásszándékkal   |   Példák kimondott szövegekre   | 
------|------|
 RepülőjegyFoglalás     |   Például "book nekem, Rio repülőjegyet következő hét" <br/> "Repülési nekem, Rio a 24th a" <br/> "Van szükségem egy adatsík jegyet Río de Janeiro tovább vasárnap"    |
 Üdvözlés     |   "Hi" <br/>"Hello" <br/>"Jó reggeli"  |
 CheckWeather | "Mi az az időjárás, például Bostonban?" <br/> "Show me a hétvégi vonatkozó előrejelzést" |
 None         | "Get me egy cookie-k recept"<br>"Volt a Lakers win?" |

Minden alkalmazás a "[nincs](#none-intent-is-fallback-for-app)" előre definiált szándékkal, azaz a tartalék szándékkal van ellátva. 

## <a name="prebuilt-domains-provide-intents"></a>Előre összeállított tartományok leképezések használata
Mellett szándék fog vonatkozni, Ön által meghatározott előre összeállított leképezések is használhatja az előre összeállított tartományok egyikéből. További információkért lásd: [a LUIS-alkalmazások előre összeállított tartományok használata](luis-how-to-use-prebuilt-domains.md) további információt az alkalmazásban az előre összeállított tartományok leképezések testreszabása.

## <a name="return-all-intents-scores"></a>Minden leképezések pontszámokat ad vissza
Egyetlen célja az utterance (kifejezés) rendelheti hozzá. A LUIS megkapja az utterance (kifejezés), a végponton, amikor egy felső célja az, hogy utterance (kifejezés) adja vissza. Amennyiben az utterance (kifejezés) az összes leképezések pontszámok szeretne, megadhat `verbose=true` jelzőt a lekérdezési karakterláncot az API [a szolgáltatásvégpont hívása](https://aka.ms/v1-endpoint-api-docs). 

## <a name="intent-compared-to-entity"></a>A szándék entitás képest
A leképezés művelet a csevegőrobot kell vennie a felhasználó számára, és a teljes utterance (kifejezés) alapján jelöli. Az entitás szavakat vagy kifejezéseket az utterance (kifejezés) részletsorában jelöli. Az utterance (kifejezés) pontozási szándéka csak egy top rendelkezhet, de számos entitás veheti fel. 

<a name="how-do-intents-relate-to-entities"></a>

Hozzon létre egy szándékot, ha  a felhasználó szándéka egy műveletet indít el az ügyfélalkalmazás, például a checkweather () függvény hívása. Ezután hozzon létre egy entitást képviselő a művelet végrehajtásához szükséges paramétereket. 

|A példában szándéka   | Entitás | Entitás az a példában kimondott szöveg   | 
|------------------|------------------------------|------------------------------|
| CheckWeather | {"type": "hely", "entitás": "seattle"}<br>{"type": "builtin.datetimeV2.date","entity": "holnap", "feloldás": "2018-05-23"} | Mi az az időjárás, például a `Seattle` `tomorrow`? |
| CheckWeather | {"type": "date_range", "entitás": "a hétvégi"} | Az előrejelzés megjelenítése `this weekend` | 
||||

## <a name="custom-intents"></a>Egyéni leképezések

Hasonlóképpen intentioned [beszédmódok](luis-concept-utterance.md) felel meg egyetlen szándékot. A leképezés a kimondott szöveg használhatja [entitás](luis-concept-entity-types.md) az alkalmazásban, mivel a entitások nem szándékot jellemző. 

## <a name="prebuilt-domain-intents"></a>Előre összeállított tartományban szándékok

[Előre összeállított tartományok](luis-how-to-use-prebuilt-domains.md) rendelkezik leképezések a kimondott szöveg.  

## <a name="none-intent"></a>A None szándék

A **none** szándék minden alkalmazás esetében fontos, és nem lehet nulla hosszúságú kimondott szöveg.

### <a name="none-intent-is-fallback-for-app"></a>Nincs leképezés nem tartalék alkalmazás
A **nincs** célja egy kevésbé vagy tartalék szándékot. A LUIS utterances, amelyek nem fontos alkalmazástartomány (tárgy területen), akik szolgál. A **nincs** szándékot 10 és 20 százalékát, az alkalmazás teljes megcímkézzen között kell rendelkeznie. Ne hagyja üresen a none értéket. 

### <a name="none-intent-helps-conversation-direction"></a>Nincs leképezés segít a beszélgetés iránya
Amikor az utterance (kifejezés), a nincs összegyűjtése várható szándék és vissza a csevegőrobot, az adott előrejelzési a robot további kérdéseket tehet fel vagy adja meg a menüben a felhasználó számára érvényes választás a csevegőrobot a közvetlen. 

### <a name="no-utterances-in-none-intent-skews-predictions"></a>Nincs utterances sincs szándék megdönti előrejelzések
Ha nem adja hozzá a megszólalásokat a **nincs** szándék, LUIS kényszeríti az utterance (kifejezés), amely a tartomány egy a tartományban szándékok kívül esik. Oktatási LUIS az utterance (kifejezés) a nem megfelelő leképezésének ez fog tevékenységdiagramon előrejelzési pontszámokat. 

### <a name="add-utterances-to-the-none-intent"></a>Beszédmódok hozzáadása a none szándék
A **nincs** leképezés már megtörtént, azonban kihagyva üresen. Töltse fel, hogy a tartomány kívül esnek megcímkézzen. Egy jó utterance (kifejezés) a **nincs** valami, teljes mértékben az alkalmazás, valamint az iparág az alkalmazás kívülről szolgál. Például egy utazási alkalmazás ne használja a megszólalásokat **nincs** kapcsolódó továbbítani a például a foglalásokat, Számlázás, élelmiszer, vendéglátás, rakományt, megszakít Szórakozás is. 

Milyen típusú kimondott szöveg van hátra a nincs a szándék? Indítsa el a valamilyen konkrét, hogy a robot ne válaszoljon, az ilyen "milyen dinosaur tartalmaz kék fog?" Ez a jellemző kérdés sokkal egy utazás alkalmazáson kívül. 

### <a name="none-is-a-required-intent"></a>Nincs szükség leképezés nem
A **nincs** célja a kötelező szándékot, és nem lehet törölték vagy átnevezték.

## <a name="negative-intentions"></a>Negatív céljaira 
Negatív és pozitív céljaira, például a meghatározni kívánt "szeretnék **szeretné** egy autó" és "szeretnék **nem** szeretné egy autó", hozhat létre a két szándék (egy pozitív és a egy negatív) és a megfelelő beszédmódok hozzáadása minden egyes. Vagy hozzon létre egy egyetlen célja, és jelölje meg a két különböző pozitív és negatív kifejezés egy egységként.  

## <a name="intents-and-patterns"></a>Szándékok és minták

Ha van példa hosszúságú kimondott szöveg, amely a részben vagy egészben is meghatározható reguláris kifejezésként, érdemes lehet a [reguláris kifejezéssel](luis-concept-entity-types.md#regular-expression-entity) párosítani egy mintázattal [](luis-concept-patterns.md). 

A reguláris kifejezéssel rendelkező entitások garantálják az kivonást, így a minta egyeztetése megtörténik. A minta egyeztetése garantálja a pontos szándékot. 

## <a name="intent-balance"></a>Leképezési terheléselosztása
Az alkalmazás tartományban szándékok utterances egyensúly kell minden egyes szándékot között. Nem rendelkezik egy leképezést és 10 kimondott szöveg és a egy másik leképezést és 500 kimondott szöveg. Ez nem elosztott terhelésű. Ha ez a helyzet, tekintse át az 500 utterances célja, hogy tekintse meg, ha a leképezések számos is rendezhető újra be egy [minta](luis-concept-patterns.md). 

A **nincs** leképezés nem szerepel az egyenleg. A leképezés tartalmaznia kell az alkalmazás teljes megcímkézzen 10 %-át.

## <a name="intent-limits"></a>Leképezési korlátok
Felülvizsgálat [korlátok](luis-boundaries.md#model-boundaries) hány szándék megértéséhez is hozzáadhat egy modellt. 

### <a name="if-you-need-more-than-the-maximum-number-of-intents"></a>Ha több, mint a leképezések maximális számát 
Először vegye figyelembe, hogy a rendszer használja-e túl sok szándék fog vonatkozni. 

### <a name="can-multiple-intents-be-combined-into-single-intent-with-entities"></a>Több leképezések egyesíthetők egyetlen célja az entitásokkal 
Leképezések túl hasonló teheti a LUIS megkülönböztetni őket. Szándék legyen elegendő a fő feladatoktól, a felhasználó által kért, de nem szükséges minden elérési utat a kód rögzítés rögzítése változott. Ha például BookFlight és FlightCustomerService lehet, hogy egy utazási alkalmazás külön leképezések, de BookInternationalFlight és BookDomesticFlight túl hasonló. Ha a rendszer megkülönböztetésükhöz van szüksége, használja a entitások vagy más logikai helyett leképezések. 

### <a name="dispatcher-model"></a>Diszpécser modell
További tudnivalók a LUIS és a QnA maker alkalmazások kombinálásával a [dispatch modell](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps). 

### <a name="request-help-for-apps-with-significant-number-of-intents"></a>Leképezések jelentős számú használó alkalmazásokkal kapcsolatos segítség kérése
Ha a leképezések számának csökkentése, vagy a leképezések osztani több alkalmazásokba, nem működik, forduljon az ügyfélszolgálathoz. Ha az Azure-előfizetés tartalmazza a támogatási szolgálathoz, lépjen kapcsolatba [technikai Azure-támogatás](https://azure.microsoft.com/support/options/). 



## <a name="next-steps"></a>További lépések

* Tudjon meg többet [entitások](luis-concept-entity-types.md), amelyeket fontos szavakat a szándék
* Ismerje meg, hogyan [hozzáadhatja és kezelheti a leképezések](luis-how-to-add-intents.md) a LUIS-alkalmazás található.
* Tekintse át a leképezés [ajánlott eljárások](luis-concept-best-practices.md)

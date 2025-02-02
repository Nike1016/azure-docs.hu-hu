---
title: Karakterek száma – Translator Text API
titleSuffix: Azure Cognitive Services
description: A Translator Text API karakterek megszámlálása.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: e3a16d9272e75f9a94f5381c1681c036d177e0f6
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595989"
---
# <a name="how-the-translator-text-api-counts-characters"></a>A Translator Text API karakterek megszámlálása

A Translator Text API a bemeneti szöveg minden Unicode-kódját karakterként számítja. Egy szöveg minden fordítása külön fordításként számít, még akkor is, ha a kérést egyetlen API-hívásban adták át, több nyelvre lefordítva. A válasz hossza nem számít.

Az alábbiak száma:

* A kérelem törzsében a Translator Text APInek átadott szöveg
   * `Text`Fordítási, Átbetűzés és szótári keresési módszerek használatakor
   * `Text`és `Translation` a szótár példái alapján
* Összes jelölés: HTML, XML-címkék stb. a kérelem törzsének Text (szöveg) mezőjében. A kérelem létrehozásához használt JSON-jelölés (például "text:") nem számít.
* Egyéni levél
* Absztrakt
* A szóköz, a Tab, a Markup és bármilyen típusú szóköz karakter
* Minden Unicode-ban definiált kód pont
* Ismétlődő fordítás, még akkor is, ha korábban már lefordította ugyanazt a szöveget

A ideograms (például kínai és Japán kanji) alapuló szkriptek esetében a Translator Text API továbbra is megszámolja az Unicode-kódok számát, a ideogram egy karaktert. Kivétel: Az Unicode-alapú pótlások két karakterből állhatnak.

A kérések száma, a szavak, a bájtok vagy a mondatok nem relevánsak a karakterek számában.

Az észlelési és BreakSentence metódusokra irányuló hívások nem számítanak bele a karakteres felhasználásba. Azonban azt várjuk, hogy az észlelési és a BreakSentence módszerek meghívása ésszerű arányban van a többi megszámolt függvény használatával. Ha az észlelési vagy BreakSentence hívások száma meghaladja a többi megszámolt módszer 100 alkalommal történő számát, a Microsoft fenntartja a jogot az észlelési és BreakSentence módszerek használatának korlátozására.


A Character Count szolgáltatással kapcsolatos további információk a [Microsoft Translator GYIK webhelyén](https://www.microsoft.com/en-us/translator/faq.aspx)találhatók.

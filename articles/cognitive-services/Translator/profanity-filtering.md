---
title: Káromkodás szűrése – Translator Text API
titleSuffix: Azure Cognitive Services
description: Használjon káromkodás-szűrést a Translator Text APIban.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 114dea098db5c824a1235ba1635f547383bf6743
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595192"
---
# <a name="add-profanity-filtering-with-the-translator-text-api"></a>Káromkodás-szűrés hozzáadása a Translator Text API

A fordítói szolgáltatás általában megőrzi a fordításban található forrásban lévő káromkodást. A trágárság foka és az olyan kontextus, amely a szavak káromkodását teszi lehetővé a különböző kultúrák között. Ennek eredményeképpen a megcélzott nyelvben a káromkodás mértéke felerősíthető vagy csökkenthető.

Ha nem szeretné megtekinteni a káromkodást a fordításban, akkor is, ha a forrás szövegében a káromkodás szerepel, használja a translate () metódusban elérhető káromkodás-szűrési lehetőséget. Ezzel a beállítással megadhatja, hogy szeretné-e megtekinteni a káromkodás törlését, a megfelelő címkék jelölését vagy a művelet elvégzését.

A translate () metódus a "Options" paramétert veszi fel, amely tartalmazza az új "ProfanityAction" elemet. A ProfanityAction elfogadott értékei a következők: "nincs művelet", "megjelölt" és "törölve".

## <a name="accepted-values-of-profanityaction-and-examples"></a>ProfanityAction és példák elfogadott értékei
|ProfanityAction érték | Action | Példa: Forrás – Japán | Példa: Cél – angol|
| :---|:---|:---|:---|
| NoAction | Default (Alapértelmezett): Ugyanaz, mint a beállítás beállítása. A káromkodás a forrás és a cél között halad át. | 彼は変態です 。 | Egy bunkó. |
| Megjelölve | A profán szavakat az XML-címkék \<káromkodása >... \</profanity >. | 彼は変態です 。 | Egy \<profán > jerk\</profanity >. |
| Törölve | A rendszer eltávolítja a profán szavakat a kimenetből a csere nélkül. | 彼は 。 | Ő a. |

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Káromkodás-szűrés alkalmazása a Translator API-hívással](reference/v3-0-translate.md)

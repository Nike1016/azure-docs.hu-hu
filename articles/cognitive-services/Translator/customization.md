---
title: Fordítás testreszabása – Translator Text API
titleSuffix: Azure Cognitive Services
description: A Microsoft Translator hub használatával saját gépi fordítási rendszereket hozhat létre a kívánt terminológiával és stílussal.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: e6771a0d72592f1952853642ceea196b02774ada
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595308"
---
# <a name="customize-your-text-translations"></a>A szöveges fordítások testreszabása

A Microsoft Custom Translator a Microsoft Translator Service funkciója, amely lehetővé teszi a felhasználók számára, hogy testre szabják a Microsoft Translator speciális neurális gépi fordítását, ha a Translator Text API (csak 3. verzió) használatával fordítja le a szöveget.

A funkció a beszédfelismerési fordítás testreszabására is használható [Cognitive Services Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/)használata esetén.

## <a name="custom-translator"></a>Custom Translator

Az egyéni fordítóval olyan neurális fordítási rendszereket hozhat létre, amelyek megértik a saját üzleti és iparági terminológiáját. A testreszabott fordítási rendszer ezután integrálva lesz a meglévő alkalmazásokhoz, munkafolyamatokhoz és webhelyekhez.

### <a name="how-does-it-work"></a>Hogyan működik?

A korábban lefordított dokumentumok (szórólapok, weblapok, dokumentációk stb.) használatával olyan fordítási rendszer hozható létre, amely a tartományra jellemző terminológiát és stílust tükrözi, ami jobb, mint egy általános fordítási rendszer. A felhasználók feltölthetik a TMX, a XLIFF, a TXT, a DOCX és az XLSX dokumentumokat.  

A rendszer emellett a dokumentum szintjén párhuzamosan megjelenő olyan adattípusokat is elfogadja, amelyek még nem igazodnak a mondatok szintjéhez. Ha a felhasználók több nyelven is hozzáférnek ugyanahhoz a tartalomhoz, de a különálló dokumentumok egyéni fordítója automatikusan meg tudja feleltetni a mondatokat a dokumentumok között.  A rendszerek a párhuzamos betanítási adatgyűjtést a fordítások tökéletesítése érdekében akár mindkét nyelven is használhatják.

A testreszabott rendszer ezután a Microsoft Translator Text API rendszeres hívásával érhető el a category paraméter használatával.

A megfelelő típusú és mennyiségű betanítási adat miatt nem ritka, hogy 5 és 10 közötti nyereséget vár, vagy akár több BLEU-pontot is a fordítási minőséghez egyéni Translator használatával.

Az elérhető adatok alapján történő Testreszabás különböző szintjeiről további részleteket az [Egyéni Translator felhasználói útmutatójában](https://aka.ms/CustomTranslatorDocs)találhat.


## <a name="microsoft-translator-hub"></a>Microsoft Translator Hub

> [!NOTE]
> Az örökölt Microsoft Translator hub 2019. május 17-én megszűnik. [Tekintse meg a fontos áttelepítési információkat és dátumokat](https://www.microsoft.com/translator/business/hub/).  

## <a name="custom-translator-versus-hub"></a>Eseményközpont és egyéni fordító

|   | **Hub** | **Egyéni fordító**|
|:-----|:----:|:----:|
|Testreszabási funkció állapota   | Általános rendelkezésre állás  | Általános rendelkezésre állás |
| Text API-verzió  | Csak v2   | Csak v3 |
| SMT testreszabása | Igen   | Nem |
| NMT testreszabása | Nem    | Igen |
| Új egyesített Speech services testreszabása | Nem    | Igen |
| [Nincs nyomkövetés](https://www.aka.ms/notrace) | Igen  | Igen |

## <a name="collaborative-translations-framework"></a>Együttműködésen alapuló fordítási keretrendszer

> [!NOTE]
> A 2018. február 1-től a AddTranslation () és a AddTranslationArray () már nem használható a Translator Text API 2.0-s verzióban. Ezek a metódusok sikertelenek lesznek, és semmi sem íródik. A Translator Text API V 3.0 nem támogatja ezeket a metódusokat.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Testreszabott nyelvi rendszer beállítása egyéni Translator használatával](https://aka.ms/CustomTranslatorDocs)

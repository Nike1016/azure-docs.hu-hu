---
title: A Video Indexer – Azure nyelvi modell testreszabása
titlesuffix: Azure Media Services
description: Ez a cikk áttekintést nyújt a Mi az a Video Indexer nyelvi modell, és hogyan szabható testre.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 516ecd8842e7b673201cc640b283c081a02d2b2f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65799559"
---
# <a name="customize-a-language-model-with-video-indexer"></a>A Video Indexer nyelvi modell testreszabása

A video Indexer-integráció révén a Microsoft automatikus beszédfelismerés támogatja [Custom Speech Service](https://azure.microsoft.com/services/cognitive-services/custom-speech-service/). Adaptációs szöveg, nevezetesen a tartományhoz, amelyek a motor mértékének megfelelően szeretné szószedet szöveget feltöltésével testre szabhatja a nyelvi modell. Modellje betanításához, miután a betanítás szövegben szereplő szavakat felismer, alapértelmezett írásmódja, feltéve, és ismertetjük, hogy a nyelvi modell szavak új várható sorrendje. Egyéni nyelvi modellek támogatottak angol, spanyol, francia, német, olasz, kínai (egyszerűsített), japán, spanyol, brazíliai portugál, Hindi és koreai. 

Nézzük meg, amely rendkívül specifikus, mint például a példaként (a gyorsjavítások gyökérmappájában lévő Azure Kubernetes Service szolgáltatásban), "Kubernetes" szó. Mivel a szó új a Video Indexer, mint "közösségeiben" ismerhető fel. A "Kubernetes" ismeri fel a modell betanítását kell. Más esetekben a szavakat léteznek, de a nyelvi modell, nem a várt rendezhet egy bizonyos környezetben. Például "container service" nem egy 2 – word sorozat, amelyek nem speciális nyelvi modell számára ismerősek szavak adott halmazát.

Lehetősége van szavakat anélkül, hogy a környezetben, egy listát a fájlt feltölteni. Részleges betanítás minősül. Azt is megteheti tölthet fel szöveges fájl dokumentációs vagy a jobb betanítás a tartalommal kapcsolatos mondatokat.

Használhatja a Video Indexer API-k vagy a webhelyet hozhat létre és szerkeszthet egyéni nyelvi modelljeit, témakörében ismertetett módon a [további lépések](#next-steps) című szakaszát.

## <a name="best-practices-for-custom-language-models"></a>Ajánlott eljárások az egyéni nyelvi modelljeit

A video Indexer megtanulja a word-kombinációk úgy, hogy ismerje meg a legjobban valószínűségek alapján:

* A mondatok elegendő valós példákat mutatnak be, akkor beszélt.
* Helyezzen több nem minden sorában, csak egy mondatot. Ellenkező esetben a rendszer, valószínűségek között a mondatok ismertetjük.
* Több szóból helyezzen egy mondatnál más szó növelése érdekében, hogy, de a rendszer megtanulja leginkább a teljes mondatokat.
* Amikor új szavakat és a mozaikszavak bemutatása, ha lehetséges, példákkal szemlélteti annyi használati a lehető legtöbb környezet biztosítsa a rendszer teljes mondaton belüli.
* Próbálja meg számos betanítás, és hogyan működnek az Ön számára.
* Pontosan egy mondatban ismétlésének elkerülése több alkalommal. Eltérés, szemben a többi bemenet, előfordulhat, hogy hozza létre.
* Kerülje a nem szokványos szimbólumok (~, a(z) % @ # &) módon lesz első elveti. Megjelentek a mondatok is első elveti.
* Kerülje a túl nagy bemenetei között, például a több százezer mondatokat, üzembe, mert ez így fog egészítsük kiemelési hatását.

## <a name="next-steps"></a>További lépések

[Testre szabhatja a nyelvi modell API-k használatával](customize-language-model-with-api.md)

[A webhelyen, nyelvi modell testreszabása](customize-language-model-with-website.md)

---
title: Video Indexer-fogalmak
titlesuffix: Azure Media Services
description: Ez a témakör néhány olyan fogalmat, a Video Indexer szolgáltatás.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 156eceba856bf159d4821360639a0641d3ed02be
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65799056"
---
# <a name="video-indexer-concepts"></a>Video Indexer-fogalmak
 
Ez a cikk néhány olyan fogalmat, a Video Indexer szolgáltatás ismerteti.
    
## <a name="summarized-insights"></a>Összesített insights

Összesített insights tartalmaznia egy összesített nézetét az adatok: arcok, témakörök, érzelmeket. Helyett kapcsolaton át, mindegyik időtartományok több ezer, és győződjön meg róla melyik arcok, például az összesített insights tartalmazza az összes az arcok, és mindegyik, a megjelenő időtartományok és a %-os látható.

## <a name="time-range-vs-adjusted-time-range"></a>időtartomány és a módosított időtartomány

TimeRange az időtartománya az eredeti videó. AdjustedTimeRange az időtartománya képest az aktuális listába. Különböző sornyi különböző videók lejátszási lista hozhat létre, mert 1 óra videó igénybe vehet, és használja azt, ha például 10:00-10:15 csak 1 sort. Ebben az esetben kell egy listát az 1 sort, ahol az időtartomány 10:00-10:15 azonban a adjustedTimeRange 00:00 – 00:15.
 
## <a name="blocks"></a>blokkok

Blokkok úgy van kialakítva, hogy egyszerűbb legyen halad át az adatokat. A blokkokat például aszerint lehet bontani, hogy mikor váltanak a beszélők, vagy hogy mikor tartanak hosszú szünetet.

## <a name="next-steps"></a>További lépések

Első lépések kapcsolatos információkért lásd: [regisztráljon, és az első videó feltöltése](video-indexer-get-started.md).

## <a name="see-also"></a>Lásd még

[A Video Indexer áttekintése](video-indexer-overview.md)

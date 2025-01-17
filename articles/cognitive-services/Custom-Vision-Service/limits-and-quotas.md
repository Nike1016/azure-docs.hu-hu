---
title: Korlátok és kvóták – Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Ismerje meg a Custom Vision Service korlátozásait és kvótáit.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: anroth
ms.openlocfilehash: 37921c655cc3c5de5c3c5079eda47fb7513fdf9f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560950"
---
# <a name="limits-and-quotas"></a>Korlátok és kvóták

A Custom Vision szolgáltatásnak két szintje van. A Azure Portalon keresztül regisztrálhat F0 (ingyenes) vagy S0 (standard) előfizetésre. A díjszabással és a tranzakciókkal kapcsolatos részletekért tekintse meg a megfelelő [Cognitive Services díjszabási oldalt](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) .

A betanítási lemezképek száma projektenként és címkék szerint projektenként a S0-projektek esetében várhatóan növekedni fog.

||**F0**|**S0**|
|-----|-----|-----|
|Projektek|2|100|
|Képek betanítása projektenként |5,000|100,000|
|Előrejelzések/hónap|10,000 |Korlátlan|
|Címkék/projekt|50|500|
|Ismétlések |10|10|
|Képek címkével ellátott minimális száma címkék szerint, besorolás (50 + ajánlott) |5|5|
|Képek címkével ellátott minimális száma címkék szerint, objektumok észlelése (50 + ajánlott)|15|15|
|Mennyi ideig tárolják az előrejelzési képeket|30 nap|30 nap|
|[Előrejelzési](https://go.microsoft.com/fwlink/?linkid=865445) műveletek tárolóval (tranzakció/másodperc)|2|10|
|[](https://go.microsoft.com/fwlink/?linkid=865445) Megjóslási műveletek tárterület nélkül (tranzakció/másodperc)|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (API-hívások másodpercenként)|2|10|
|[Egyéb API-hívások](https://go.microsoft.com/fwlink/?linkid=865446) (Tranzakció/másodperc)|10|10|
|Maximális képméret (betanítási képfeltöltés) |6 MB|6 MB|
|Maximális képméret (előrejelzés)|4 MB|4 MB|
|Objektumok maximális száma objektum-észlelési betanítási képen|200|200|
|Címkék maximális száma besorolási képenként|30|30|

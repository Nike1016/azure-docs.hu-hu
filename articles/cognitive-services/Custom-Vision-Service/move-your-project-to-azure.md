---
title: Korlátozott próbaverziós projekt áthelyezése az Azure-ba
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan helyezhet át egy korlátozott próbaverziós projektet az Azure-ba.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: anroth
ms.openlocfilehash: 22c3767dfac1e377890f1e01517d18263e694854
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560935"
---
# <a name="how-to-move-your-limited-trial-project-to-azure"></a>A korlátozott próbaverziós projekt áthelyezése az Azure-ba

Ahogy Custom Vision Service befejezi az Azure-ba való áttérést, az Azure-on kívüli korlátozott próbaverziós projektek támogatása véget ér. Ebből a dokumentumból megtudhatja, hogyan másolhat a korlátozott próbaverziós projektet egy Azure-erőforrásra a Custom Vision API-k használatával.

A korlátozott próbaverziós projektek megtekintésének támogatása a [Custom Vision webhelyén](https://customvision.ai) 2019. március 25-én fejeződött be. Ebből a dokumentumból megtudhatja, hogyan használhatja az Custom Vision API-kat a GitHubon [futó áttelepítési Python-parancsfájllal](https://github.com/Azure-Samples/custom-vision-move-project) ) a projekt egy Azure-erőforrásba való másolásához.

További részletekért, beleértve a korlátozott próbaidőszakos folyamatra vonatkozó főbb határidőket, tekintse meg a [kibocsátási megjegyzéseket](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/release-notes#february-25-2019) vagy a korlátozott próbaverziós projektek tulajdonosainak küldött e-mailes kommunikációt.

Az [áttelepítési parancsfájl](https://github.com/Azure-Samples/custom-vision-move-project) lehetővé teszi egy projekt újbóli létrehozását a jelenlegi iterációban található összes címke, régió és rendszerkép letöltésével, majd feltöltésével. Az új előfizetésben egy új projekttel fog elindulni, amelyet aztán betaníthat.

## <a name="prerequisites"></a>Előfeltételek

- Szüksége lesz egy érvényes Azure-előfizetésre, amely ahhoz a Microsoft-fiók vagy Azure Active Directory (HRE) fiókhoz tartozik, amelyet használni szeretne a [Custom Vision](https://customvision.ai)webhelyre való bejelentkezéshez. 
    - Ha nem rendelkezik Azure-fiókkal, [hozzon létre ingyen egy fiókot](https://azure.microsoft.com/free/) .
    - Az előfizetések és az erőforrások Azure-koncepciójának bevezetését az [Azure fejlesztői útmutatójában találja.](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#manage-your-subscriptions)
-  [Python](https://www.python.org/downloads/)
- [Pip](https://pip.pypa.io/en/stable/installing/)

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Custom Vision erőforrások létrehozása a Azure Portal

Az Azure-Custom Vision Service használatához létre kell hoznia Custom Vision képzési és előrejelzési erőforrásokat a [Azure Portal](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision). 

Több projekt is társítható egyetlen erőforráshoz. További részletek a [díjszabásról és](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/limits-and-quotas) a korlátozásokról. Ahhoz, hogy továbbra is használhassa a Custom Vision Service ingyenes használatát, kiválaszthatja a F0 szintet a Azure Portal. 

> [!NOTE]
> Amikor áthelyezi a Custom Vision projektet egy Azure-erőforrásba, az örökli az []( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) adott Azure-erőforrás mögöttes engedélyeit. Ha a szervezet más felhasználói a projekthez tartozó Azure-erőforrás tulajdonosai, akkor a projekthez hozzáférhetnek a [Custom Vision webhelyén](https://customvision.ai). Hasonlóképpen, az erőforrások törlése törli a projekteket.  

## <a name="find-your-limited-trial-project-information"></a>A korlátozott próbaverziós projekt adatainak megkeresése

A projekt áthelyezéséhez szüksége lesz az áttelepíteni kívánt projekthez tartozó _projekt_ -azonosítóra és betanítási _kulcsra_ . Ha nem rendelkezik ezekkel az információkkal, [https://limitedtrial.customvision.ai/projects](https://limitedtrial.customvision.ai/projects) látogasson el az egyes projektek azonosítójának és kulcsának beszerzéséhez. 

## <a name="use-the-python-sample-code-to-copy-your-project-to-azure"></a>A projekt másolása az Azure-ba a Python-mintakód használatával

Kövesse a [mintakód utasításait](https://github.com/Azure-Samples/custom-vision-move-project)a korlátozott próbaverziós kulcs és a projekt azonosítójának használatával a "forrás" anyagok és a "cél" néven létrehozott új Azure-erőforrás kulcsa alapján.

Alapértelmezés szerint az összes korlátozott próbaverziós projektet az USA déli középső régiójában üzemeltetjük.

## <a name="next-steps"></a>További lépések

A projekt már át lett helyezve egy Azure-erőforrásba. Az Ön által írt alkalmazásokban frissítenie kell a képzési és előrejelzési kulcsokat.

Ha a projektet a [Custom Vision webhelyén](https://customvision.ai)szeretné megtekinteni, jelentkezzen be ugyanazzal a fiókkal, amelyet a Azure Portalba való bejelentkezéshez használt. Ha nem látja a projektet, győződjön meg róla, hogy a [Custom Vision webhelyén](https://customvision.ai) található ugyanazon a címtárban található, ahol az erőforrások a Azure Portal találhatók. A Azure Portal és a CustomVision.ai is kiválaszthatja a könyvtárat a képernyő jobb felső sarkában található legördülő menü felhasználó menüjéből.
---
title: Moderálási feladatok használata a REST API-konzollal – Content Moderator
titleSuffix: Azure Cognitive Services
description: Az API-k áttekintése feladat műveleteivel teljes körű tartalom-moderálási feladatokat indíthat el a képekhez vagy szöveges tartalmakhoz az Azure Content Moderatorban.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: sajagtap
ms.openlocfilehash: af636deaafc05238c287d095e644588ed8c5f26d
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880050"
---
# <a name="define-and-use-moderation-jobs-rest"></a>Moderálási feladatok definiálása és használata (REST)

A moderálási feladatok egyfajta burkolóként szolgálnak a tartalom moderálása, a munkafolyamatok és a felülvizsgálatok működéséhez. Ez az útmutató bemutatja, hogyan kezdeményezheti és tekintheti meg a tartalom moderálási feladatait a feladat REST API-jai segítségével. Miután megértette az API-k szerkezetét, könnyedén elvégezheti ezeket a hívásokat a REST-kompatibilis platformokra.

## <a name="prerequisites"></a>Előfeltételek

- Jelentkezzen be, vagy hozzon létre egy fiókot a Content Moderator [felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com/) webhelyén.
- Választható [Definiáljon egy egyéni](./Review-Tool-User-Guide/Workflows.md) munkafolyamatot a feladatokhoz való használatra; használhatja az alapértelmezett munkafolyamatot is.

## <a name="create-a-job"></a>Feladat létrehozása

A moderálási feladatok létrehozásához nyissa meg a feladathoz tartozó API [-](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) referenciát, és válassza ki a kulcs régiójához tartozó gombot (ezt a végpont URL-címében találja a [felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com/) **hitelesítő adatok** lapján). Ezzel elindítja az API-konzolt, ahol könnyedén hozhat létre és futtathat REST API hívásokat.

![Feladatok – oldal régiójának kiválasztása](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>Adja meg a REST-hívás paramétereit

A REST-hívás létrehozásához adja meg a következő értékeket:

- **csapatnév**: A [felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com/) fiókjának beállításakor létrehozott csoport azonosítója (a felülvizsgálati eszköz hitelesítő adatai képernyő **azonosító** mezőjében található).
- **ContentType**: Ez lehet "rendszerkép", "text" vagy "Video".
- **ContentId**: Egyéni azonosító sztring. Ezt a karakterláncot a rendszer átadja az API-nak, és visszaküldi a visszahívást. Hasznos lehet belső azonosítók vagy metaadatok társítására egy moderálási feladatok eredményeivel.
- **Workflowname**: A korábban létrehozott munkafolyamat neve (vagy "default" az alapértelmezett munkafolyamathoz).
- **CallbackEndpoint**: Választható A visszahívási információk fogadására szolgáló URL-cím a felülvizsgálat befejezésekor.
- **Ocp-Apim-Subscription-Key**: Az Content Moderator kulcsa. Ez a [felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com) **Beállítások** lapján található.

### <a name="fill-in-the-request-body"></a>Töltse ki a kérelem törzsét

A REST-hívás törzse egy mezőt, **ContentValue**tartalmaz. Illessze be a RAW Text (szöveg) szöveget, ha moderálást használ, vagy adjon meg egy képet vagy videó URL-címet, ha a rendszer a képet vagy a videót is kihasználja. A következő minta képurl-címet használhatja:[https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)

![Feladatok – konzol lekérdezési paramétereinek, fejléceknek és kérelem törzsének létrehozása](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>Kérelem elküldése

Kattintson a **Küldés** gombra. Ha a művelet sikeres, a **Válasz állapota** `200 OK`, a **válasz tartalma** mező pedig a feladatokhoz tartozó azonosítót jeleníti meg. Másolja ezt az azonosítót a következő lépésekben való használatra.

![Felülvizsgálat – a konzol válaszának létrehozása mező megjeleníti a felülvizsgálati azonosítót](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>Feladatok állapotának beolvasása

Egy futó vagy Befejezett feladatok állapotának és részleteinek beszerzéséhez nyissa meg a [feladatok beolvasása – API-](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3) hivatkozás lapot, és válassza ki a régióhoz tartozó gombot (az a régió, amelyben a kulcsot felügyeli).

![Feladatokhoz tartozó régió kiválasztása](images/test-drive-region.png)

Adja meg a REST-hívás paramétereit a fenti szakaszban leírtak szerint. Ebben a lépésben a **JobId** a feladatokhoz kapott egyedi azonosító sztring. Kattintson a **Küldés** gombra. Ha a művelet sikeres, a **Válasz állapota** `200 OK`, a **válasz tartalma** pedig JSON formátumban jeleníti meg a feladatot, a következőhöz hasonlóan:

```json
{  
  "Id":"2018014caceddebfe9446fab29056fd8d31ffe",
  "TeamName":"some team name",
  "Status":"Complete",
  "WorkflowId":"OCR",
  "Type":"Image",
  "CallBackEndpoint":"",
  "ReviewId":"201801i28fc0f7cbf424447846e509af853ea54",
  "ResultMetaData":[  
    {  
      "Key":"hasText",
      "Value":"True"
    },
    {  
      "Key":"ocrText",
      "Value":"IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n"
    }
  ],
  "JobExecutionReport":[  
    {  
      "Ts":"2018-01-07T00:38:29.3238715",
      "Msg":"Posted results to the Callbackendpoint: https://requestb.in/vxke1mvx"
    },
    {  
      "Ts":"2018-01-07T00:38:29.2928416",
      "Msg":"Job marked completed and job content has been removed"
    },
    {  
      "Ts":"2018-01-07T00:38:29.0856472",
      "Msg":"Execution Complete"
    },
    {  
      "Ts":"2018-01-07T00:38:26.7714671",
      "Msg":"Successfully got hasText response from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:26.4181346",
      "Msg":"Getting hasText from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:25.5122828",
      "Msg":"Starting Execution - Try 1"
    }
  ]
}
```

![Feladatok – REST-hívási válasz](images/test-drive-job-5.png)

### <a name="examine-the-new-reviews"></a>Vizsgálja meg az új felülvizsgálat (oka) t

Ha a tartalmi feladatokban felülvizsgálatot eredményezett, azt a [felülvizsgálati eszközben](https://contentmoderator.cognitive.microsoft.com)tekintheti meg. /Válassza > a képszövegVideójának áttekintése (a használt tartalomtól függően) lehetőséget./ A tartalomnak meg kell jelennie, és készen kell állnia az emberi felülvizsgálatra. Miután egy emberi moderátor áttekinti az automatikusan hozzárendelt címkéket és az előrejelzési adatokat, és elküld egy végső moderálási döntést, a feladatok API elküldi az összes információt a kijelölt visszahívási végpont végpontjának.

## <a name="next-steps"></a>További lépések

Ebben az útmutatóban megtanulta, hogyan hozhat létre és kérhet le tartalom-moderálási feladatokat a REST API használatával. Ezután integrálja a feladatokat egy végpontok közötti moderálási forgatókönyvbe, például az [E-kereskedelmi moderálási](./ecommerce-retail-catalog-moderation.md) oktatóanyagba.
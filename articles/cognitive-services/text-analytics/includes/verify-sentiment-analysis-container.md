---
title: Az érzelmek elemzése tároló példányának ellenőrzése
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan ellenőrizheti az érzelmek elemzése tároló példányát.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 1303d753b1cbfabe7ddd3442e0880b0bffe089b3
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377393"
---
## <a name="verify-the-sentiment-analysis-container-instance"></a>Az érzelmek elemzése tároló példányának ellenőrzése

1. Válassza az **Áttekintés** lapot, és másolja ki az IP-címet.
1. Nyisson meg egy új böngésző fület, és adja meg az IP-címet. Írja be `http://<IP-address>:5000 (http://55.55.55.55:5000`például a következőt:). Megjelenik a tároló kezdőlapja, amelyből megtudhatja, hogy fut-e a tároló.

    ![A tároló kezdőlapjának megtekintése annak ellenőrzéséhez, hogy fut-e](../media/how-tos/container-instance/swagger-docs-on-container.png).

1. Válassza ki a **szolgáltatás API leírása** hivatkozást, hogy megnyissa a tároló hencegő lapját.

1. Válassza ki a **post** API-k bármelyikét, és válassza a **kipróbálás**lehetőséget. A paraméterek megjelennek, amely tartalmazza a következő bemeneti példát:

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "1",
          "text": "Hello world. This is some input text that I love."
        },
        {
          "language": "fr",
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "language": "es",
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        }
      ]
    }
    ```

1. Cserélje le a bemenetet a következő JSON-tartalomra:

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "7",
          "text": "I was fortunate to attend the KubeCon Conference in Barcelona, it is one of the best conferences I have ever attended. Great people, great sessions and I thoroughly enjoyed it!"
        }
      ]
    }
    ```

1. Állítsa a **showStats** igaz értékre.

1. A szöveg hangulatának meghatározásához válassza a **végrehajtás** lehetőséget.

    A tárolóban csomagolt modell egy 0 és 1 közötti értéket generál, ahol a 0 negatív, az 1 pedig pozitív.

    A visszaadott JSON-válasz magában foglalja a frissített szövegbeviteli adatok hangulatát:

    ```json
    {
      "documents": [
      {
        "id": "7",
        "score": 0.9826303720474243,
        "statistics": {
          "charactersCount": 176,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 1,
        "validDocumentsCount": 1,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 1
      }
    }
    ```

Most már összekapcsolhatja a `id` válasz adattartalom JSON-adatainak dokumentumát az eredeti kérelem adattartalma `id`dokumentumával. A pontszáma nagyobb, `.98` mint egy erősen pozitív hangulat.
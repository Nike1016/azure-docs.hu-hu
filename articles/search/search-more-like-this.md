---
title: az Azure Search (előzetes verzió) – Azure Search moreLikeThis
description: A moreLikeThis (előzetes verzió) szolgáltatáshoz, az Azure Search REST API-ban előzetes dokumentációjában talál.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 4d1c691e570d3cfc7e0475c02e4c60ed6ffa8440
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485355"
---
# <a name="morelikethis-in-azure-search"></a>az Azure Search moreLikeThis

> [!Note]
> moreLikeThis előzetes állapotban van, nem éles használatra szánt. A [REST API verzióját 2019-05-06-Preview](search-api-preview.md) ezt a szolgáltatást biztosít. Rendszer jelenleg nem .NET SDK-t támogatja.

`moreLikeThis=[key]` a lekérdezési paraméter a [Search API-dokumentumok](https://docs.microsoft.com/rest/api/searchservice/search-documents) , amely olyan dokumentumokat keres a dokumentumot, a dokumentum kulcsaként által megadott hasonló. Ha egy keresési kérelmet a `moreLikeThis`, egy lekérdezés jön létre keresési feltételeket az adott dokumentum kinyert legjobban leíró dokumentum. A létrehozott lekérdezést a keresés kérés majd szolgál. Alapértelmezés szerint az összes kereshető mező tartalmát minősülnek, mínusz a megadott használatával korlátozott mezőket a `searchFields` paraméter. A `moreLikeThis` paraméter nem használható a keresési paraméterrel, `search=[string]`.

Alapértelmezés szerint minden felső szintű kereshető mező tartalmát minősülnek. Ha szeretné inkább adja meg az adott mezők, használhatja a `searchFields` paraméter. 

A kereshető almező moreLikeThis nem használható egy [komplex típus](search-howto-complex-data-types.md).

## <a name="examples"></a>Példák 

Az alábbi, például egy moreLikeThis lekérdezés. A lekérdezés talál, amelynek leírás mezők leginkább hasonlítanak, a mező a forrás-dokumentum által megadott a dokumentumok a `moreLikeThis` paraméter.

```
Get /indexes/hotels/docs?moreLikeThis=1002&searchFields=description&api-version=2019-05-06-Preview
```

```
POST /indexes/hotels/docs/search?api-version=2019-05-06-Preview
    {
      "moreLikeThis": "1002",
      "searchFields": "description"
    }
```


## <a name="next-steps"></a>További lépések

Minden olyan webes tesztelési eszköz segítségével kísérletezhet a funkció.  Ehhez a gyakorlathoz Postman használatát javasoljuk.

> [!div class="nextstepaction"]
> [Ismerkedés az Azure Search REST API-k Postman](search-get-started-postman.md)
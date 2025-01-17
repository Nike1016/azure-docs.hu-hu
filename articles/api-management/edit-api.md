---
title: API szerkesztése az Azure Portal használatával  | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan lehet egy API-t szerkeszteni az API Management (APIM) szolgáltatással.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/08/2017
ms.author: apimpm
ms.openlocfilehash: 6f1a0cf6025cb3a398ab93320c6fcb69b1e62429
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60558099"
---
# <a name="edit-an-api"></a>API szerkesztése

Ez az oktatóanyag bemutatja, hogyan szerkeszthet egy API-t az API Management (APIM) szolgáltatással. 

+ Ez műveletek az APIM-példányon történő hozzáadásával, törlésével és átnevezésével történik. 
+ Az API swaggerjét is szerkesztheti.

## <a name="prerequisites"></a>Előfeltételek

+ [Azure API Management-példány létrehozása](get-started-create-service-instance.md)
+ [Az első API importálása és közzététele](import-and-publish.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="edit-an-api-in-apim"></a>API szerkesztése az APIM szolgáltatásban

![API szerkesztése](./media/edit-api/edit-api001.png)

1. Kattintson az **API-k** lapfülre.
2. Válassza ki az egyik korábban importált API-t.
3. Válassza ki a **Tervezés** fület.
4. Válassza ki a szerkeszteni kívánt műveletet.
5. A művelet átnevezéséhez az **Előtér** ablakban kattintson a **ceruza** ikonra.

## <a name="update-the-swagger"></a>Swagger frissítése

A háttérrendszer API-ját frissítheti az Azure Portalról. Ehhez tegye a következőket:

1. Válassza ki a **Minden művelet** lehetőséget.
2. Az **Előtér** ablakban kattintson a ceruza ikonra.

    ![API szerkesztése](./media/edit-api/edit-api002.png)

    Megjelenik az API swaggerje.

    ![API szerkesztése](./media/edit-api/edit-api003.png)

3. Frissítse a swaggert.
4. Kattintson a **Mentés** gombra.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [APIM-szabályzatminták](policy-samples.md)
> [Közzétett API átalakítása és védelme](transform-api.md)
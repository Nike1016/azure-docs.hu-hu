---
title: Azure rövid útmutató – üzenetsor létrehozása az Azure Storage-ban a Azure Portal használatával | Microsoft Docs
description: Ebben a rövid útmutatóban egy várólistát hoz létre a Azure Portal használatával. Ezután a Azure Portal használatával hozzáadhat egy üzenetet, megtekintheti az üzenet tulajdonságait, és elvégezheti az üzenet eltávolítását.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/06/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.reviewer: cbrooks
ms.openlocfilehash: d2f7b01c03b877a3dede496fc02f0d0cc9525cef
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721316"
---
# <a name="quickstart-create-a-queue-and-add-a-message-with-the-azure-portal"></a>Gyors útmutató: Üzenetsor létrehozása és üzenet hozzáadása a Azure Portal

Ebből a rövid útmutatóból megtudhatja, hogyan használható az [Azure Portal](https://portal.azure.com/) egy üzenetsor létrehozásához az Azure Storage-ban, valamint üzenetek hozzáadásához és eltávolításához.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="create-a-queue"></a>Üzenetsor létrehozása

Ha várólistát szeretne létrehozni a Azure Portalban, kövesse az alábbi lépéseket:

1. Az Azure Portalon lépjen az új tárfiókjára.
2. A Storage-fiók bal oldali menüjében görgessen a **Queue szolgáltatás** szakaszra, majd válassza a **várólisták**elemet.
3. Kattintson a **+ várólista** gombra.
4. Adja meg az új üzenetsor nevét. A várólista nevének kisbetűnek kell lennie, betűvel vagy számmal kell kezdődnie, és csak betűket, számokat és kötőjel (-) karaktert tartalmazhat.
6. A várólista létrehozásához kattintson **az OK gombra** .

    ![A várólista létrehozásának módját bemutató képernyőkép a Azure Portal](media/storage-quickstart-queues-portal/create-queue.png)

## <a name="add-a-message"></a>Üzenet hozzáadása

Ezután adjon hozzá egy üzenetet az új várólistához. Egy üzenet akár 64 KB méretű is lehet.

1. Válassza ki az új várólistát a Storage-fiókban található várólisták listájából.
1. Kattintson az **+ üzenet hozzáadása** gombra, és adjon hozzá egy üzenetet a várólistához. Adjon meg egy üzenetet az **üzenet szövege** mezőben. 
1. Itt adhatja meg, hogy mikor járjon le az üzenet. Az a maximális időtartam, ameddig egy üzenet maradhat a várólistában, 7 nap.
1. Jelezze, hogy Base64-ként kívánja-e kódolni az üzenetet. A bináris adatkódolási szolgáltatás használata javasolt.
1. Az üzenet hozzáadásához kattintson az **OK** gombra.

    ![Az üzenetek várólistához való felvételét bemutató képernyőkép](media/storage-quickstart-queues-portal/add-message.png)

## <a name="view-message-properties"></a>Üzenet tulajdonságainak megtekintése

Az üzenet hozzáadása után a Azure Portal megjeleníti a várólistán lévő összes üzenet listáját. Megtekintheti az üzenet AZONOSÍTÓját, az üzenet tartalmát, az üzenet beszúrási idejét és az üzenet lejárati idejét. Azt is megtudhatja, hogy az üzenet hány alkalommal lett elválasztva a sorból.

![Az üzenet tulajdonságait megjelenítő képernyőfelvétel](media/storage-quickstart-queues-portal/view-message-properties.png)

## <a name="dequeue-a-message"></a>Üzenet várólistára helyezése

A várólista elején lévő üzenet a Azure Portalból is elvégezhető. Amikor elvégez egy üzenetet, a rendszer törli az üzenetet. 

A dequeuing mindig eltávolítja a legrégebbi üzenetet a várólistában. 

![Az üzenetnek a portálról való eltávolítását bemutató képernyőkép](media/storage-quickstart-queues-portal/dequeue-message.png)

## <a name="next-steps"></a>További lépések

Ebből a rövid útmutatóból megtudhatta, hogyan hozhat létre egy üzenetsor, hogyan adhat hozzá egy üzenetet, megtekintheti az üzenet tulajdonságait, és elvégezheti az üzenetek elküldését a Azure Portal.

> [!div class="nextstepaction"]
> [Mik azok az Azure Queues?](storage-queues-introduction.md)

---
title: Az Azure Service Bus üzenetküldési entitások felfüggesztése |} A Microsoft Docs
description: Felfüggesztése és újraaktiválása Azure Service Bus-üzenet entitások.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: e2ffda3141462d19557af3af26c117ee505c40ab
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66170813"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Felfüggesztése és újraaktiválása üzenetküldési entitások (letiltva)

Üzenetsorok, témakörök és előfizetések átmenetileg felfüggeszti is. Felfüggesztés tárolási tartani minden üzenetet letiltott állapotba helyezi az entitás. Azonban üzeneteket nem lehet hozzáadott vagy eltávolított, és a megfelelő protokoll műveletek eddig is számtalan előnyét hibákat.

Egy entitás felfüggesztése sürgős felügyeleti okok miatt általában történik. A forgatókönyv egy kellene üzembe helyezett egy hibás fogadót, amely üzeneteket az üzenetsor ki nem sikerül a feldolgozást, és még nem megfelelően befejezi az üzenetek és eltávolítja azokat a. Ha ezt a viselkedést észlelnek, az üzenetsor letiltható az kap, amíg nem javított kód üzembe helyezése és további megelőzhető az adatvesztés a hibás kód okozza.

A felfüggesztés vagy Újraaktiválási is elvégezhető, a felhasználó által vagy a rendszer. A rendszer felfüggeszti az entitások, például az előfizetés a költségkeret lenyomásával súlyos felügyeleti okok csak. Rendszer – letiltva entitások nem lehet újraaktiválni a felhasználó által, de visszaállnak, ha a felfüggesztés okának elhárítása.

A portálon a **tulajdonságok** az adott entitás szakasz lehetővé teszi, hogy az állapotának módosítása; az alábbi képernyőfelvételen a váltógomb várólista:

![][1]

A portál csak lehetővé teszi a teljes körűen le az üzenetsorok. Is tiltsa le a küldési és fogadási műveletek külön-külön használatával a Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API-k, a .NET-keretrendszer SDK vagy az Azure Resource Manager-sablon Azure CLI-vel vagy az Azure Powershellen keresztül.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="suspension-states"></a>Felfüggesztés állapotok

A várólista beállítható állapotok a következők:

-   **Aktív**: Az üzenetsor még aktív.
-   **Letiltott**: A várólista fel van függesztve.
-   **SendDisabled**: A várólista részlegesen megállíthatja a receive megengedett.
-   **ReceiveDisabled**: A várólista részlegesen megállíthatja megengedett küldjön.

Az előfizetésekre és -témaköröket, csak **aktív** és **letiltott** akkor állítható be.

A [EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) enumerálás is határozza meg, amely csak állítható be a rendszer átmeneti állapotok. Tiltsa le egy várólistába a PowerShell-parancsot az alábbi példában látható. A Újraaktiválási parancs a következő egyenértékű beállítás `Status` való **aktív**.

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="next-steps"></a>További lépések

További információ a Service Bus-üzenetkezelés, tekintse meg a következő témaköröket:

* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/queue-disable.png


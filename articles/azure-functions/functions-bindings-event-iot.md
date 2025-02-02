---
title: Az Azure IoT Hub-kötések az Azure Functions szolgáltatáshoz
description: Megtudhatja, hogyan használja az IoT Hub-kötések az Azure Functions szolgáltatásban.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: az Azure functions, függvények, eseményfeldolgozás, dinamikus számítás, kiszolgáló nélküli architektúra
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: e39a7b2c29ee541a9a104d681d25335496e5625b
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67480604"
---
# <a name="azure-iot-hub-bindings-for-azure-functions"></a>Az Azure IoT Hub-kötések az Azure Functions szolgáltatáshoz

Ez a cikk ismerteti az Azure Functions-bindings az IoT Hub használatának módját. Az IoT Hub támogatási alapul a [Azure Event Hubs-kötés](functions-bindings-event-hubs.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Csomagok – 1.x függvények

Azure Functions-verzió 1.x, szerepelnek az IoT Hub-kötések a [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) NuGet-csomag verziója 2.x. A csomag forráskódja a [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs) GitHub-adattárban.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Csomagok – 2.x függvények

A Functions 2.x, használja a [Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs) csomag, a verzió 3.x. A csomag forráskódja a [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs) GitHub-adattárban.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

> [!IMPORTANT]
> A következő kód minták az Event Hub API-t használ, miközben adott szintaxisa az IoT Hub függvények érvényes.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudjon meg többet az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)

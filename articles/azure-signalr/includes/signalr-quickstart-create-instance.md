---
title: fájl belefoglalása
description: fájl belefoglalása
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 09/14/2018
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: ac77f0b6a1d90fd78db64618c1e03ade198a67c0
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882153"
---
## <a name="create-an-azure-signalr-service-instance"></a>Azure SignalR Service-példány létrehozása

Az alkalmazás egy SignalR Service-példányhoz fog csatlakozni az Azure-ban.

1. Kattintson az Azure Portal bal felső sarkában található Új gombra. Az Új képernyőn írja be a keresőmezőbe a *SignalR Service* kifejezést, majd nyomja le az Enter billentyűt.

    ![SignalR Service keresése](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-new.png)

1. A keresési eredmények között válassza ki a **SignalR Service** elemet, majd a **Létrehozás** lehetőséget.

1. Adja meg az alábbi beállításokat.

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Erőforrás neve** | Globálisan egyedi név | Az új SignalR Service-példányt azonosító név. Érvényes karakterek: `a-z`, `0-9` és `-`.  | 
    | **Előfizetés** | Az Ön előfizetése | Az új SignalR Service-példány létrehozásához használt előfizetés. | 
    | **[Erőforráscsoport](../../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Az új erőforráscsoport neve, amelyben létrehozza a SignalR Service-példányt. | 
    | **Location** | USA nyugati régiója | Válasszon egy Önhöz közeli [régiót](https://azure.microsoft.com/regions/). |
    | **Tarifacsomag** | Free | Az Azure SignalR Service-t ingyenesen is kipróbálhatja. |
    | **Egységek száma** |  Nem alkalmazható | Az egységek száma azt határozza meg, hogy a SignalR Service-példány hány kapcsolatot tud elfogadni. Ez a beállítás csak a Standard szinten konfigurálható. |
    | **Szolgáltatási mód** |  Kiszolgáló nélküli | Azure Functions vagy REST API használatával. |

    ![SignalR szolgáltatás létrehozása](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-create.png)

1. A SignalR Service-példány üzembe helyezésének megkezdéséhez kattintson a **Létrehozás** gombra.

1. A példány üzembe helyezése után nyissa meg a portálon, és keresse meg a beállítások lapot. A szolgáltatás mód beállítása csak *kiszolgáló* nélkülire módosítható, ha Azure functions kötés vagy REST API használatával használja az Azure signaler szolgáltatást. Hagyja meg *klasszikus* vagy *alapértelmezett* módon.
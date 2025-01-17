---
title: Hozzon létre a webhookok a szabályok az Azure IoT Central |} A Microsoft Docs
description: Webhookok létrehozása az Azure IoT Central más alkalmazások automatikusan értesíti, amikor szabályok aktiválódik.
author: viv-liu
ms.author: viviali
ms.date: 06/16/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: fdeefdc1ec5372d6ac17f0f985ee0c50ce902e56
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165325"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Webhook-műveletek létrehozása az Azure IoT Central szabályai

*Ez a témakör létrehozói és a rendszergazdák vonatkozik.*

Webhookok lehetővé teszi az IoT-központ alkalmazás csatlakoztatása a más alkalmazások és szolgáltatások a távoli figyelés és értesítések. Webhookok automatikus értesítéséhez egyéb alkalmazások és szolgáltatások, amikor egy szabály akkor lesz kiváltva csatlakozik az IoT-központ alkalmazásában. Az IoT Central-alkalmazást egy POST kérést küld az alkalmazás HTTP-végpontot, minden alkalommal, amikor egy szabály akkor lesz kiváltva. A hasznos eszközadatok és a szabályt az eseményindító részleteit tartalmazza.

## <a name="set-up-the-webhook"></a>Webhook beállítása

Ebben a példában, csatlakozzon a RequestBin, értesítést kaphat, amikor a szabályok aktiválódik, webhookok segítségével.

1. Nyissa meg [RequestBin](https://requestbin.net/).

1. Hozzon létre egy új RequestBin és másolása a **Bin URL-cím**.

1. Hozzon létre egy [telemetriai szabály](howto-create-telemetry-rules.md) vagy egy [esemény szabály](howto-create-event-rules.md). A szabály mentéséhez és a egy új művelet hozzáadása.

    ![Webhook-létrehozási képernyő](media/howto-create-webhooks/webhookcreate.png)

1. Válassza ki a webhook művelettel, és adjon meg egy megjelenített nevet, és illessze be a tár URL-CÍMÉT az visszahívási URL-címet.

1. A szabály mentéséhez.

A szabály akkor lesz kiváltva, amikor megjelenik egy új kérelmet RequestBin jelennek meg.

## <a name="payload"></a>hasznos adat

A szabály akkor lesz kiváltva, ha egy HTTP POST kérés jön létre a visszahívási URL-cím egy json-adattartalom a mérések, eszköz, a szabály és alkalmazásadatokat tartalmazó. A telemetriai adatok szabály a tartalom az alábbihoz hasonló:

```json
{
    "id": "ID",
    "timestamp": "date-time",
    "device" : {
        "id":"ID",
        "name":  "Refrigerator1",
        "simulated" : true,
        "deviceId": "deviceID",
        "deviceTemplate":{
            "id": "ID",
            "version":"1.0.0"
        },
        "properties":{
            "device":{
                "firmwareversion":"1.0"
            },
            "cloud":{
                "location":"One Microsoft Way"
            }
        },
        "measurements":{
            "telemetry":{
                "temperature":20,
                "pressure":10
            }
        }

    },
    "rule": {
        "id": "ID",
        "name": "High temperature alert",
        "enabled": true,
        "deviceTemplate": {
            "id":"GUID",
            "version":"1.0.0"
        }
    },
    "application": {
        "id": "ID",
        "name": "Contoso app",
        "subdomain":"contoso-app"
    }
}
```

## <a name="known-limitations"></a>Ismert korlátozások

Jelenleg nincs ezek webhookok egy API-n keresztül, az előfizetés/iratkoznak nincs programozott módon.

Ha ez a szolgáltatás továbbfejlesztésére vonatkozó ötleteit, közzététel a javaslatok a [Uservoice fórumot](https://feedback.azure.com/forums/911455-azure-iot-central).

## <a name="next-steps"></a>További lépések

Most, hogy már megtanulta, hogyan állíthatja be és webhookok használata, a javasolt következő lépésre megismeréséhez [munkafolyamatokat a Microsoft Flow-hoz](howto-add-microsoft-flow.md).

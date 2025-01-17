---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 11/06/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e93f78cf07cd4815e5b17ffd3953db121adb6535
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69558745"
---
<!-- put the ## header in the file that includes this file -->

Ebben a szakaszban egy eszköz identitását hozza létre az IoT hub identitás-beállításjegyzékében. Egy eszköz nem tud csatlakozni a központhoz, hacsak nem rendelkezik bejegyzéssel az Identity registryben. További információkért tekintse meg a [IoT hub fejlesztői útmutató](../articles/iot-hub/iot-hub-devguide-identity-registry.md)"Identity Registry" című szakaszát.

1. Az IoT hub navigációs menüjében nyissa meg a **IoT eszközöket**, majd válassza az **új** lehetőséget az eszköz hozzáadásához az IoT hub-ban.

    ![Eszköz identitásának létrehozása a portálon](./media/iot-hub-include-create-device/create-identity-portal-vs2019.png)

1. Az **eszköz létrehozása**lapon adja meg az új eszköz nevét, például **myDeviceId**, majd válassza a **Mentés**lehetőséget. Ez a művelet létrehoz egy eszköz identitását az IoT hub számára.

   ![Új eszköz felvétele](./media/iot-hub-include-create-device/create-a-device-vs2019.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Az eszköz létrehozása után nyissa meg az eszközt a **IoT-eszközök** ablaktábla listájában. Másolja az **elsődleges kapcsolódási karakterláncot** a későbbi használatra.

    ![Eszköz-csatlakoztatási karakterlánc](./media/iot-hub-include-create-device/device-details-vs2019.png)

> [!NOTE]
> Az IoT Hub-identitásjegyzék csak az IoT Hub biztonságos elérésének biztosításához tárolja az eszközidentitásokat. Az eszközazonosítókat és kulcsokat biztonsági hitelesítő adatokként tárolja, valamint tartalmaz egy engedélyezve/letiltva jelzőt, amellyel letilthatja egy adott eszköz hozzáférését. Ha az alkalmazásnak más eszközspecifikus metaadatokat kell tárolnia, egy alkalmazásspecifikus tárolót kell használnia. További információ: [IoT hub fejlesztői útmutató](../articles/iot-hub/iot-hub-devguide-identity-registry.md).

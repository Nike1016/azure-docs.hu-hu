---
title: Microsoft Azure Data Box Gateway eszköz csatlakoztatása és kezelése a Windows PowerShell felületén keresztül | Microsoft Docs
description: Ismerteti, hogyan csatlakozhat a szolgáltatáshoz, majd hogyan felügyelheti Data Box Gateway a Windows PowerShell felületén keresztül.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/02/2019
ms.author: alkohli
ms.openlocfilehash: 650777d849e172686e8b46502a84db8c519174e7
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775196"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>Azure Data Box Gateway-eszköz kezelése a Windows PowerShell használatával

Azure Data Box Gateway megoldás lehetővé teszi az adatküldés hálózaton keresztül az Azure-ba. Ez a cikk a Data Box Gateway eszköz konfigurációs és felügyeleti feladatait ismerteti. Az eszköz kezeléséhez használhatja a Azure Portal, a helyi webes felhasználói felületet vagy a Windows PowerShell felületét is.

Ez a cikk a PowerShell-felületet használó feladatokra összpontosít.

Ez a cikk az alábbi eljárásokat tartalmazza:

- Kapcsolódás a PowerShell felületéhez
- Támogatási csomag létrehozása
- Tanúsítvány feltöltése
- Rendszerindítás nem DHCP-környezetben
- Eszköz adatainak megtekintése

## <a name="connect-to-the-powershell-interface"></a>Kapcsolódás a PowerShell felületéhez

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Támogatási csomag létrehozása

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Tanúsítvány feltöltése

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>Rendszerindítás nem DHCP-környezetben

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-edge-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>Eszköz adatainak megtekintése

[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="next-steps"></a>További lépések

- Az [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) üzembe helyezése az Azure Portalon.

---
title: Azure IoT Edge futtatása Ubuntu-Virtual Machines | Microsoft Docs
description: Azure IoT Edge telepítési utasítások az Ubuntu 16,04 Azure Marketplace-en Virtual Machines
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: gregman
ms.openlocfilehash: f4bab6ab837b746c6a569cc6de95a95023bf83f4
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68986995"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Azure IoT Edge futtatása Ubuntu-Virtual Machines

Az Azure IoT Edge-futtatókörnyezet az eszköz milyen bekapcsolja az IoT Edge-eszköz. A futtatókörnyezet kis Raspberry Pi-t vagy akkora, mint egy ipari kiszolgáló eszközökön is telepíthető. Miután egy eszközt az IoT Edge-futtatókörnyezet van beállítva, a üzembe helyezése a üzleti logika, hogy a felhőben is elindítható.

Az IoT Edge-futtatókörnyezet működését, és milyen összetevők járnak kapcsolatos további információkért lásd: [megismerheti az Azure IoT Edge-futtatókörnyezet és az architektúrára](iot-edge-runtime.md).

Ez a cikk a Azure IoT Edge Runtime Ubuntu 16,04 virtuális gépen való futtatásának lépéseit sorolja fel az [Ubuntu Azure Marketplace-ajánlat](https://aka.ms/azure-iot-edge-ubuntuvm)előre konfigurált Azure IoT Edge használatával. 

Az első rendszerindítás során a Azure IoT Edge az Ubuntu rendszerű virtuális gépen előre telepíti a Azure IoT Edge Runtime legújabb verzióját. Emellett tartalmaz egy parancsfájlt a kapcsolati karakterlánc beállításához, majd újraindítja a futtatókörnyezetet, amely távolról indítható az Azure VM-portálon vagy az Azure-parancssoron keresztül, így egyszerűen konfigurálhatja és csatlakoztathatja az IoT Edge eszközt SSH vagy távoli indítás nélkül asztali munkamenet. Ez a szkript arra vár, hogy állítsa be a kapcsolódási karakterláncot egészen addig, amíg a IoT Edge-ügyfél telepítése nem történik meg, így nem kell létrehoznia az automatizálást.

## <a name="deploy-from-the-azure-marketplace"></a>Üzembe helyezés az Azure piactéren
1.  Az [Azure Marketplace](https://azuremarketplace.microsoft.com/) -en navigáljon az Ubuntu Marketplace-ajánlat [Azure IoT Edge](https://aka.ms/azure-iot-edge-ubuntuvm) vagy az "Azure IoT Edge az Ubuntuban" kifejezésre való kereséssel.
2.  Válassza a **Letöltés most** lehetőséget, majd **folytassa** a következő párbeszédablakban.
3.  A Azure Portalban kattintson a **Létrehozás** gombra, és kövesse a varázslót a virtuális gép üzembe helyezéséhez. 
    *   Ha első alkalommal próbál meg egy virtuális gépet kipróbálni, a legegyszerűbb, ha jelszót használ, és engedélyezi az SSH-t a nyilvános bejövő port menüjében. 
    *   Ha erőforrás-igényes számítási feladattal rendelkezik, a virtuális gép méretét további processzorok és/vagy memória hozzáadásával kell frissítenie.
4.  A virtuális gép üzembe helyezését követően konfigurálja úgy, hogy az IoT Hubhoz kapcsolódjon:
    1.  Másolja az eszköz-kapcsolódási karakterláncot a IoT Hub létrehozott IoT Edge eszközről (ha nem ismeri ezt a folyamatot, kövesse az [új Azure IoT Edge eszköz regisztrálása a Azure Portal](how-to-register-device-portal.md) útmutatóból útmutatót.)
    1.  Válassza ki az újonnan létrehozott virtuálisgép-erőforrást a Azure Portal, és nyissa meg a **parancs futtatása** lehetőséget.
    1.  Válassza a **RunShellScript** lehetőséget.
    1.  Futtassa az alábbi szkriptet a parancssori ablakban az eszközhöz tartozó kapcsolási karakterlánccal:`/etc/iotedge/configedge.sh “{device_connection_string}”`
    1.  **Futtatás** kiválasztása
    1.  Várjon néhány percet, és a képernyőn meg kell adni egy sikert jelző üzenetet, amely jelzi, hogy a rendszer sikeresen beállította a kapcsolatok sztringjét.


## <a name="deploy-from-the-azure-portal"></a>Üzembe helyezés a Azure Portal
A Azure Portal keressen rá a "Azure IoT Edge" kifejezésre, majd válassza az **Ubuntu Server 16,04 LTS + Azure IoT Edge Runtime** elemet a virtuális gép létrehozási munkafolyamatának megkezdéséhez. Innen hajtsa végre a fenti 3. és 4. lépést az "üzembe helyezés az Azure piactéren" című részben leírtak szerint.

## <a name="deploy-from-azure-cli"></a>Üzembe helyezés az Azure CLI-ből

1. Ha az Azure CLI-t használja az asztalon, először jelentkezzen be:

   ```azurecli-interactive
   az login
   ```
    
1. Ha több előfizetéssel rendelkezik, válassza ki azt az előfizetést, amelyet használni szeretne:
   1. Az előfizetések listázása:
    
      ```azurecli-interactive
      az account list --output table
      ```
    
   1. Másolja a használni kívánt előfizetés SubscriptionID mezőjét.

   1. Állítsa be a munkaidő-előfizetést az imént másolt AZONOSÍTÓval:
    
      ```azurecli-interactive 
      az account set -s {SubscriptionId}
      ```
    
1. Hozzon létre egy új erőforráscsoportot (vagy határozzon meg egy meglévőt a következő lépésekben):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Fogadja el a virtuális gép használati feltételeit. Ha először szeretné áttekinteni a feltételeket, kövesse az [üzembe helyezés az Azure piactéren](#deploy-from-the-azure-marketplace)című témakör lépéseit.

   ```azurecli-interactive
   az vm image accept-terms --urn microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest
   ```

1. Új virtuális gép létrehozása:

   ```azurecli-interactive
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys
   ```

1. Az eszköz kapcsolódási karakterláncának beállítása (az [új Azure IoT Edge eszköz regisztrálása az Azure CLI-vel](how-to-register-device-cli.md) – útmutató, ha nem ismeri ezt a folyamatot):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script "/etc/iotedge/configedge.sh '{device_connection_string}'"
   ```

Ha a telepítés után SSH-t szeretne használni a virtuális gépen, használja a publicIpAddress a következő paranccsal:`ssh azureuser@{publicIpAddress}`


## <a name="next-steps"></a>További lépések

Most, hogy az IoT Edge-eszköz kiosztva a modul telepítve van, [üzembe helyezése IoT Edge-modulok](how-to-deploy-modules-portal.md).

Ha problémákat tapasztal a IoT Edge futtatókörnyezet megfelelő telepítésével kapcsolatban, tekintse meg a [hibaelhárítási](troubleshoot.md) oldalt.

Ha egy meglévő telepítést szeretne frissíteni a IoT Edge legújabb verziójára, tekintse meg [a IoT Edge biztonsági démon és futtatókörnyezet frissítése](how-to-update-iot-edge.md)című témakört.

Ha meg szeretné nyitni a portokat a virtuális gép SSH-n vagy más bejövő kapcsolaton keresztüli eléréséhez, tekintse meg az Azure-beli virtuális gép dokumentációját a [portok és végpontok Linux rendszerű virtuális gépre](../virtual-machines/linux/nsg-quickstart.md) való megnyitásához

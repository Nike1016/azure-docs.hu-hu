---
title: Tárolók az ajánlat közzétételi útmutató az Azure Marketplace-en
description: Ez a cikk ismerteti a tárolók közzététele a piactéren
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: ellacroi
manager: nunoc
ms.service: marketplace
ms.topic: article
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: 41a09be36262ff09c383b8ccb64a94230a11d3f1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64937914"
---
# <a name="containers-offer-publishing-guide"></a>Tárolók az ajánlat közzétételi útmutató

Tároló ajánlatok segítenek a tárolórendszerkép az Azure piactéren közzétehető. Ez az útmutató segítségével áttekinteni a követelményeket, ezt az ajánlatot. 

Ezek a tranzakció ajánlatok, amelyek üzembe helyezve, és a Marketplace-en keresztül számlázott. Művelet, amely a felhasználó kap, a hívást a "Letöltés most."

A tároló ajánlattípusra használja, amikor a megoldás üzembe egy Kubernetes-alapú Azure tárolószolgáltatás, Docker-tároló rendszerképét.

>[!NOTE]
>Például egy Kubernetes-alapú, az Azure container service például az Azure Kubernetes Service vagy az Azure Container Instances szolgáltatásban, a kiválasztott Azure-ügyfelek egy Kubernetes-alapú tárolót modul.  

A Microsoft jelenleg támogatja az ingyenes és a bring-your-saját licenc (használata BYOL) licencmodellek szükségesek.

## <a name="containers-offer"></a>Tárolók ajánlat

| Követelmény | Részletek |  
|:--- |:--- |  
| Számlázással és méréssel | Támogatja a vagy az ingyenes vagy BYOL számlázási modellt. |  
| A docker-fájlban létrehozott rendszerképet | Tárolólemezképeket a Docker-rendszerkép specifikáció alapján kell, és a egy Docker-fájlból kell elkészíteni.<ul> <li>Docker-rendszerképek létrehozásával kapcsolatos további információkért látogasson el a használati szakaszban található [docs.docker.com/engine/reference/builder/#usage](https://docs.docker.com/engine/reference/builder/#usage).</li> </ul> |  
| Az ACR üzemeltetése | Tárolórendszerképek az Azure Container Registry (ACR) tárházban kell elhelyezni.<ul> <li>Az ACR-REL kapcsolatos további információkért látogasson el a rövid útmutató: Hozzon létre egy tároló-beállításjegyzéket az Azure portal oldalán található használatával [docs.microsoft.com/azure/container-registry/container-registry-get-started-portal](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal).</li> </ul> |  
| Képcímkézés | Tárolórendszerképek tartalmaznia kell legalább 1 címke (maximális címkék: 16).<ul> <li>Kép címkézéssel kapcsolatos további információkért látogasson el a helyen található docker-címke lap [docs.docker.com/engine/reference/commandline/tag](https://docs.docker.com/engine/reference/commandline/tag).</li> </ul> |  

## <a name="next-steps"></a>További lépések

Ha ezt még nem tette meg, 

- [Regisztráljon](https://azuremarketplace.microsoft.com/sell) a Marketplace-en.

Ha regisztrálva van és a egy új ajánlat létrehozása vagy egy meglévő, dolgozik

- [Jelentkezzen be a Cloud Partner portálra](https://cloudpartner.azure.com) létrehozni, vagy végezze el az ajánlatot.
- Lásd: [tárolók](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-containers-offer) további információt.

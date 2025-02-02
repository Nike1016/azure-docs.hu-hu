---
title: Az Azure megjelenítési referenciájának architektúrái – Azure Batch
description: A Azure Batch és más Azure-szolgáltatások használatára szolgáló architektúrák a helyszíni Render farmok kibővítéséhez a felhőbe való Betöréssel
services: batch
ms.service: batch
author: davefellows
manager: gwallace
ms.author: lahugh
ms.date: 02/07/2019
ms.topic: conceptual
ms.custom: seodec18
ms.openlocfilehash: fa2d59b2a6d2dea72276ab38a5cb1ca7bfb579a4
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323109"
---
# <a name="reference-architectures-for-azure-rendering"></a>Az Azure rendering architektúrái

Ez a cikk magas szintű architektúrát ábrázoló ábrákat mutat be a helyszíni rendering-farmok Azure-ba történő kiterjesztéséhez, vagy "burst". A példák az Azure számítási, hálózati és tárolási szolgáltatásainak különböző lehetőségeit mutatják be.

## <a name="hybrid-with-nfs-or-cfs"></a>Hibrid NFS vagy CFS

Az alábbi ábrán egy hibrid forgatókönyv látható, amely a következő Azure-szolgáltatásokat tartalmazza:

* **Számítás** – Azure batch készlet vagy virtuálisgép-méretezési csoport.

* Helyszíni **hálózat** : Azure ExpressRoute vagy VPN. Azure: Azure-VNet.

* **Storage** – bemeneti és kimeneti fájlok: Az Azure-beli virtuális gépeket használó NFS-vagy CFS-eszközök Azure File Sync vagy RSync használatával szinkronizálhatók a helyszíni tárolással. Vagylagosan A avere a helyszíni NAS-eszközökről az NFS használatával vFXT a bemeneti vagy kimeneti fájlokat.

  ![Cloud burst – hibrid NFS vagy CFS](./media/batch-rendering-architectures/hybrid-nfs-cfs-avere.png)

## <a name="hybrid-with-blobfuse"></a>Hibrid és Blobfuse

Az alábbi ábrán egy hibrid forgatókönyv látható, amely a következő Azure-szolgáltatásokat tartalmazza:

* **Számítás** – Azure batch készlet vagy virtuálisgép-méretezési csoport.

* Helyszíni **hálózat** : Azure ExpressRoute vagy VPN. Azure: Azure-VNet.

* **Storage** – bemeneti és kimeneti fájlok: BLOB Storage, amely a számítási erőforrásokhoz van csatlakoztatva az Azure Blobfuse használatával.

  ![Cloud burst – hibrid és Blobfuse](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>Hibrid számítás és tárolás

Az alábbi ábrán egy teljes mértékben csatlakoztatott hibrid forgatókönyv látható a számítási és a tárolási kapacitáshoz, és a következő Azure-szolgáltatásokat tartalmazza:

* **Számítás** – Azure batch készlet vagy virtuálisgép-méretezési csoport.

* Helyszíni **hálózat** : Azure ExpressRoute vagy VPN. Azure: Azure-VNet.

* **Tárolás** – létesítmények közötti: Avere vFXT. Helyszíni fájlok opcionális archiválása Azure Data Box használatával a blob Storage-ba vagy a helyszíni avere FXT a NAS-gyorsításhoz.

  ![Cloud burst – hibrid számítási és tárolási kapacitás](./media/batch-rendering-architectures/hybrid-compute-storage-avere.png)


## <a name="next-steps"></a>További lépések

* További információ a [Render managerek](batch-rendering-render-managers.md) Azure batch használatával történő használatáról.

* További információ az [Azure-ban történő renderelés](batch-rendering-service.md)lehetőségeiről.
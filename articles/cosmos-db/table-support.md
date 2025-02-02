---
title: Az Azure Table Storage támogatása Azure Cosmos DB
description: Ebből a cikkből megtudhatja, hogyan használható együtt az Azure Cosmos DB Table API és az Azure Storage Tables.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
ms.date: 08/05/2019
author: wmengmsft
ms.author: wmeng
ms.reviewer: sngun
ms.openlocfilehash: 71bc5d73a7b5bc83dc1ac835c80ac1b14d5113e9
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68814696"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Fejlesztés az Azure Cosmos DB Table API és az Azure Table Storage használatával

Az Azure Cosmos DB Table API és az Azure Table Storage ugyanazt a táblaadatmodellt használja, és ugyanazokat a létrehozási, törlési, frissítési és lekérdezési műveleteket teszik elérhetővé az SDK-kon keresztül. 

[!INCLUDE [storage-table-cosmos-comparison](../../includes/storage-table-cosmos-comparison.md)]

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Fejlesztés az Azure Cosmos DB Table API-val

Az [Azure Cosmos DB Table API](table-introduction.md) jelenleg négy SDK-t biztosít a fejlesztéshez: 

* [Microsoft. Azure. Cosmos. table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table): .net SDK. Ez a függvénytár a .NET szabványt célozza meg, és ugyanazokat az osztályokat és metódusokat tartalmazza, mint a nyilvános [Windows Azure Storage SDK](https://www.nuget.org/packages/WindowsAzure.Storage), de a Table API használatával is csatlakozhat Azure Cosmos db fiókokhoz. A .NET-keretrendszer [Microsoft. Azure. CosmosDB. table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/) függvénytárának felhasználói a [Microsoft. Azure. Cosmos. table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) -re való frissítéshez ajánlottak, mivel karbantartási módban vannak, és hamarosan elavultak lesznek.

* [Python SDK](table-sdk-python.md): Az új Azure Cosmos DB Python SDK az egyetlen SDK, amely támogatja az Azure Table Storage-ot a Pythonban. Ez az SDK az Azure Table Storage-hoz és az Azure Cosmos DB Table API-hoz is csatlakozik.

* [Java SDK](table-sdk-java.md): Ez az Azure Storage SDK képes Azure Cosmos DB-fiókokhoz csatlakozni a Table API használatával.

* [Node.js SDK](table-sdk-nodejs.md): Ez az Azure Storage SDK képes Azure Cosmos DB-fiókokhoz csatlakozni a Table API használatával.


A Table API használatáról a [gyakori kérdések között talál további információt: Fejlessze a Table API](faq.md#table) cikket.

## <a name="developing-with-azure-table-storage"></a>Fejlesztés az Azure Table Storage használatával

Az Azure Table Storage az alábbi SDK-kat biztosítja a fejlesztéshez:

- [WindowsAzure.Storage .NET SDK](https://www.nuget.org/packages/WindowsAzure.Storage/). Ez a kódtár lehetővé teszi a Storage Table Service használatát.
- [Python SDK](https://github.com/Azure/azure-cosmos-table-python). A Pythonhoz készült Azure Cosmos DB Table SDK támogatja a Table Storage szolgáltatást (mivel az Azure Table Storage és a Cosmos DB Table API ugyanazokkal a szolgáltatásokkal és funkciókkal rendelkeznek, és az SDK fejlesztési törekvéseinek factorize érdekében javasoljuk, hogy használja ezt az SDK-t).
- [A Java-hoz készült Azure Storage SDK](https://github.com/azure/azure-storage-java). Ez az Azure Storage SDK ügyféloldali kódtárat biztosít a Java-szolgáltatásban az Azure Table Storage használatához.
- [Node.js SDK](https://github.com/Azure/azure-storage-node). Ez az SDK egy Node.js-csomagot és egy böngészőkompatibilis JavaScript ügyféloldali kódtárat biztosít a Storage Table Service használatához.
- [AzureRmStorageTable PowerShell-modul](https://www.powershellgallery.com/packages/AzureRmStorageTable). Ez a PowerShell-modul parancsmagokat biztosít a Storage Tables használatához.
- [Az Azure Storage ügyféloldali kódtára a C++ programnyelvhez](https://github.com/Azure/azure-storage-cpp/). Ez a kódtár lehetővé teszi alkalmazások létrehozását az Azure Storage-ban.
- [Az Azure Storage Table ügyféloldali kódtára a Rubyhoz](https://github.com/azure/azure-storage-ruby/tree/master/table). Ez a projekt az Azure Storage Table Service elérést megkönnyítő Ruby-csomagot biztosít.
- [Azure Storage Table ügyféloldali PHP-kódtára](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table). Ez a projekt az Azure Storage Table Service elérést megkönnyítő ügyféloldali PHP-kódtárat biztosít.


   






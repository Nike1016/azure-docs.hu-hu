---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/18/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8d1f9ca5a964787f0ee68e6d99fe870cd7b5994b
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673369"
---
Az Azure Blob Storage a Microsoft felhőalapú objektumtárolási megoldása. A BLOB storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva. Strukturálatlan adatok olyan adat, amelyet nem követi a egy adott adatmodellhez vagy egy definíciót, például szöveg vagy bináris adatok.

## <a name="about-blob-storage"></a>Blob storage-ról

A BLOB storage készült:

* Képek vagy dokumentumok közvetlen szolgáltatása a böngészők számára.
* Fájlok tárolása megosztott hozzáféréshez.
* Video- és hangtartalom streamelése.
* Írás naplófájlokba.
* Adattárolás biztonsági mentésekhez és helyreállításhoz, vészhelyreállításhoz és archiváláshoz.
* Adattárolás helyszíni vagy az Azure-ban üzemeltetett szolgáltatásban való elemzéshez.

Felhasználók vagy ügyfélalkalmazások érhető objektumok Blob Storage-via HTTP/HTTPS, bárhol a világon. Blob Storage-objektumok keresztül érhető el a [Azure Storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell-lel](https://docs.microsoft.com/powershell/module/azure.storage), [Azure CLI-vel](https://docs.microsoft.com/cli/azure/storage), vagy egy Azure Storage ügyféloldali kódtára. Ügyfélkódtárak érhetők el a különböző nyelveken, köztük [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), [Java](https://docs.microsoft.com/java/api/overview/azure/storage/client), [Node.js](https://azure.github.io/azure-storage-node), [Python](https://docs.microsoft.com/python/azure/), [Go ](https://github.com/azure/azure-storage-blob-go/), [PHP](https://azure.github.io/azure-storage-php/), és [Ruby](https://azure.github.io/azure-storage-ruby).

## <a name="about-azure-data-lake-storage-gen2"></a>Tudnivalók az Azure Data Lake Storage Gen2

BLOB storage támogatja az Azure Data Lake Storage Gen2, a Microsoft vállalati big data analytics megoldás a felhőben. Az Azure Data Lake Storage Gen2 ajánlatok hierarchikus egy fájlt a rendszer, valamint a Blob storage, beleértve az alacsony költségű, többrétegű tárolást; előnyeit magas rendelkezésre állású; erős konzisztencia; és a vész-helyreállítási lehetőségei.

További információ a Data Lake Storage Gen2: [Bevezetés az Azure Data Lake Storage Gen2-re](../articles/storage/data-lake-storage/introduction.md).

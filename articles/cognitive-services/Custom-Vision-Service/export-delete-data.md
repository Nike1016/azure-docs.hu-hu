---
title: Exportálja vagy törölje az adatait – Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan exportálhatja vagy törölheti az adatait a Custom Vision Serviceban.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: b885f359d9416fbc5f778b094610260342a75f65
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564225"
---
# <a name="export-or-delete-user-data-in-custom-vision"></a>Felhasználói adatexportálás vagy-törlés Custom Vision

Custom Vision gyűjti a felhasználói adatokat a szolgáltatás működtetéséhez, de az ügyfelek az Custom Vision betanítási [API](https://go.microsoft.com/fwlink/?linkid=865446)-kkal teljes mértékben szabályozzák az adatok megtekintését, exportálását és törlését.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Az alábbi táblázatból megtudhatja, hogyan exportálhat és törölhet felhasználói információkat Custom Visionban.

| Data | Exportálási művelet | Törlési művelet |
| ---- | ---------------- | ---------------- |
| Fiókadatok (előfizetési kulcsok) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Törlés a Azure Portal használatával (Azure-előfizetések). Vagy használja a "fiók törlése" gombot a CustomVision.ai-beállítások lapon (Microsoft-fiók előfizetések) | 
| Iteráció részletei | [GetIteration](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Iterációs teljesítmény részletei | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Iterációk listája | [GetIterations](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Projektek és projekt részletei | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) és [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Képcímkék | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) és [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Képek | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (megadja a képletöltés URI-ját) és a [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (URI-t biztosít a rendszerképek letöltéséhez) | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Exportált modellek | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | Fiók törlésekor törölve |

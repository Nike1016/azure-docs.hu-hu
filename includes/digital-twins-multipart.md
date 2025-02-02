---
title: fájl belefoglalása
description: fájl belefoglalása
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 08/12/2019
ms.author: v-adgera
ms.custom: include file
ms.openlocfilehash: f9b1b32775393196a77bef3aa2257f6fb66afdcb
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "69012154"
---
> [!NOTE]
> A többrészes kérelmek általában három darabot igényelnek:
> * Egy **Content-Type** fejléc:
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * Egy **Content-hajlam**:
>   * `form-data; name="metadata"`
> * A feltölteni kívánt fájl tartalma
>
> A **tartalom típusa** és a tartalomra való **hajlam** a használati forgatókönyvtől függően változhat.

A többrészes kérelmek programozott módon (átmenő C#), Rest-ügyféllel vagy más eszközzel (például [Poster](https://docs.microsoft.com/azure/digital-twins/how-to-configure-postman#multi)) hozhatók létre. A REST-ügyfél eszközei eltérő szintű támogatást nyújthatnak az összetett többrészes kérelmekhez. A konfigurációs beállítások az eszköztől az eszköztől némileg eltérőek lehetnek. Ellenőrizze, hogy melyik eszköz megfelel az igényeinek.

> [!IMPORTANT]
> Az Azure digitális Twins felügyeleti API-khoz küldött többrészes kérelmek általában két részből állnak:
> * A blob metaadatainak (például a hozzá tartozó MIME-típus), amelyet a **tartalom típusa** és/vagy a **tartalom-elrendezés** deklarál
> * A feltölteni kívánt fájl strukturálatlan tartalmát tartalmazó blob tartalma
>
> A **javítási** kérelmek esetében a két rész egyikének sem kell lennie. Mindkettő szükséges a **post** vagy a Create műveletekhez.

A [Foglaltság](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/api/update.cs) gyors üzembe helyezési forráskódja olyan teljes C# példákat tartalmaz, amelyek bemutatják, hogyan lehet többrészes kérelmeket készíteni az Azure digitális Twins felügyeleti API-
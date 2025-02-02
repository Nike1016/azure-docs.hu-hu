---
title: Közös FabricClient kivételek |} A Microsoft Docs
description: Gyakori kivételek és hibák, amely alkalmazások és a fürt felügyeleti műveletek végrehajtásakor a FabricClient API-k által is hibajelzést ismerteti.
services: service-fabric
documentationcenter: .net
author: oanapl
manager: chackdan
editor: ''
ms.assetid: bb821313-b221-479f-b08e-36cf07e60a07
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/20/2018
ms.author: oanapl
ms.openlocfilehash: 5bf17f4ced6bb01d8b62b6fa40ed1aeffe6f712f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60946542"
---
# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>Gyakori kivételek és hibák a FabricClient API-k használatakor
A [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) API-k lehetővé teszik a felügyeleti feladatokat hajthat végre egy Service Fabric-alkalmazás, szolgáltatás vagy fürt a fürt és az alkalmazások rendszergazdák. Például alkalmazás központi telepítése, frissítése és eltávolítása, a fürt állapotának ellenőrzése, vagy egy szolgáltatás tesztelése. Az alkalmazásfejlesztők és a fürt rendszergazdái használhatják a FabricClient API-k fejlesztéshez a Service Fabric-fürt és az alkalmazások kezelésére szolgáló eszközök.

Nincsenek műveletek, amelyek a FabricClient használatával végezheti el számos különböző típusú.  Az egyes módszerek nagyvállalat kivételek, hibák helytelen bemenet miatt, futásidejű hibák és átmeneti infrastrukturális problémára.  Tekintse meg az API dokumentációjában található, mely kivételek egy adott módszerrel. Vannak kivételek, azonban, amely szerint számos hibajelzést is eltérő [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) API-k. A következő táblázat felsorolja a kivételeket, amelyek közösek a FabricClient API-k között.

| Kivétel | Mikor lépett fel |
| --- |:--- |
| [System.Fabric.FabricObjectClosedException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricobjectclosedexception) |A [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) objektum egy lezárt állapotban van. Tud megszabadulni a [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) objektum használ, és a egy új példányosítható [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) objektum. |
| [System.TimeoutException](https://docs.microsoft.com/dotnet/core/api/system.timeoutexception) |A művelet túllépte az időkorlátot. [OperationTimedOut](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) adja vissza, ha a művelet több mint MaxOperationTimeout befejezéséhez. |
| [System.UnauthorizedAccessException](https://docs.microsoft.com/dotnet/core/api/system.unauthorizedaccessexception) |A hozzáférés-ellenőrzés a művelet nem sikerült. E_ACCESSDENIED adja vissza. |
| [System.Fabric.FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception) |Futási hiba történt a művelet végrehajtása közben. A FabricClient módszerekkel potenciálisan nagyvállalat [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception), a [ErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception.ErrorCode) tulajdonság azt jelzi, hogy a kivétel pontos okát. Hibakódok vannak definiálva a [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) enumerálása. |
| [System.Fabric.FabricTransientException](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception) |A művelet valamilyen feltétel átmeneti hiba miatt nem sikerült. Például egy művelet meghiúsulhat, mert a kvórum replikák szolgáltatás átmenetileg nem érhető el. Sikertelen műveletek újrapróbálható átmeneti kivételek felelnek meg. |

Néhány gyakori [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) hibák, a visszaadható egy [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception):

| Hiba | Állapot |
| --- |:--- |
| CommunicationError |Kommunikációs hiba miatt a művelet sikertelen, próbálja megismételni a műveletet. |
| InvalidCredentialType |A hitelesítő adatok típusa érvénytelen. |
| InvalidX509FindType |A X509FindType je neplatná. |
| InvalidX509StoreLocation |A X509 tárolási helye érvénytelen. |
| InvalidX509StoreName |A X509 store neve érvénytelen. |
| InvalidX509Thumbprint |A X509 tanúsítvány ujjlenyomata karakterlánc érvénytelen. |
| InvalidProtectionLevel |A védelmi szint érvénytelen. |
| InvalidX509Store |A X509 nem lehet megnyitni a tanúsítványtárolót. |
| InvalidSubjectName |A tulajdonos neve érvénytelen. |
| InvalidAllowedCommonNameList |Köznapi név listában karakterlánc formátuma érvénytelen. Vesszővel tagolt listájának kell lennie. |


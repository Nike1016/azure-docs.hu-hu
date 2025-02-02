---
title: Egyidejűség-vezérlés |} Az Azure Marketplace-en
description: Egyidejűségi vezérlő stratégiák közzé API-kat, a Cloud Partner portálra.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 8cdcfd84a2f3bd4f920b97392255237db173cbf9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935597"
---
# <a name="concurrency-control"></a>Egyidejűség-vezérlés

A Cloud Partner portálra közzététele az API-k minden meghívásához explicit módon meg kell adnia milyen egyidejűségi vezérlő stratégiát kíván használni. Adja meg a hiba a **If-Match** fejléc egy HTTP 400-as számú hibaüzenetet eredményez. Egyidejűség-vezérlés két stratégiák biztosítunk.

-   **Az optimista** – a frissítés elvégzése az ügyfél ellenőrzi, hogy ha az adatok változtak-e az adatok utolsó olvasása óta.
-   **Az elmúlt egy wins** – az ügyfél közvetlenül frissíti az adatokat, függetlenül attól, hogy egy másik alkalmazás módosította az utolsó olvasás idő óta.

<a name="optimistic-concurrency-workflow"></a>Optimista párhuzamosság munkafolyamat
-------------------------------

Azt javasoljuk, hogy garantálja, hogy az erőforrások nem váratlan módosításokat végzett használatával a következő munkafolyamattal az optimista párhuzamossági stratégiát.

1.  Kérje le egy entitást, az API-k használatával. A válasz egy ETag-érték, amely azonosítja a jelenleg tárolt verzió az entitás (időben válasz) tartalmazza.
2.  A frissítés idején közé tartozik a azonos ETag-érték a kötelező **If-Match** kérés fejlécéhez.
3.  Az API-t az ETag-érték, az a entitás egy atomi tranzakció jelenlegi ETag-érték a kérésben kapott hasonlítja össze.
    *   Ha az ETag-érték nem egyezik, az API-t adja vissza egy `412 Precondition Failed` HTTP-választ. Ez a hiba azt jelzi, hogy vagy egy másik folyamat frissítette az entitást, az ügyfél utolsó lekérése óta, vagy az, hogy a kérelemben megadott ETag-érték nem megfelelő.
    *  Ha az ETag-érték azonos, vagy a **If-Match** fejléc csillag helyettesítő karaktert tartalmaz (`*`), az API-t elvégzi a kért műveletet. Az API-művelet frissíti az entitás tárolt ETag-érték is.


> [!NOTE]
> A helyettesítő karakter (*) megadásáról a **If-Match** fejléc eredményezi, az API-t a wins-utolsó-egy párhuzamossági stratégiát. Ebben az esetben az ETag összehasonlítás nem történik meg, és az erőforrás minden ellenőrzés nélkül frissül. 

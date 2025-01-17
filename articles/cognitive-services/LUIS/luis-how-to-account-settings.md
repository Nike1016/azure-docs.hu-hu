---
title: Fiók és kulcsok kezelése – LUIS
titleSuffix: Azure Cognitive Services
description: A két kulcsfontosságú adatokat egy LUIS-fiók olyan felhasználói fiók és az Authoring Tool kulcs. Bejelentkezési adatait a account.microsoft.com címen kezelheti. A szerzői kulcsot a LUIS portál beállítások lapján kezelheti.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 11a2040e674a0ee38fbce8b21097f058fe603da2
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881928"
---
# <a name="manage-account-and-authoring-key"></a>Fiók és a kulcs létrehozási kezelése

A két kulcsfontosságú adatokat egy LUIS-fiók olyan felhasználói fiók és az Authoring Tool kulcs. A bejelentkezési adatait felügyelt [account.microsoft.com](https://account.microsoft.com). A szerzői kulcsot a [Luis](luis-reference-regions.md) portál **Beállítások** lapján kezelheti.

## <a name="authoring-key"></a>Kulcs létrehozási

Ez az egyetlen, régióspecifikus szerzői kulcs a **Beállítások** lapon lehetővé teszi, hogy az összes alkalmazást a [Luis](luis-reference-regions.md) portálról, valamint a [szerzői API](https://go.microsoft.com/fwlink/?linkid=2092087)-król is elkészítse. Könnyebb áttekinthetőség érdekében győződjön meg arról, hogy az Authoring Tool kulcs engedélyezett egy [korlátozott](luis-boundaries.md) végpont száma havonta kérdezi le.

[![A LUIS-beállítások lap](./media/luis-how-to-account-settings/account-settings.png)](./media/luis-how-to-account-settings/account-settings.png#lightbox)

A szerzői műveletekhez részben kulcsot a saját alkalmazások, valamint bármely közreműködő módon legyen alkalmazások szolgál.

## <a name="authoring-key-regions"></a>Szerzői műveletek legfontosabb területek

Az Authoring Tool kulcs csak a [szerzői műveletek terület](luis-reference-regions.md#publishing-regions). A kulcs nem működik egy másik régióban.

## <a name="reset-authoring-key"></a>Szerzői kulcs visszaállítása

Ha az Authoring Tool kulcs biztonsága sérül, alaphelyzetbe állítja a kulcsot. A kulcs alaphelyzetbe áll a [Luis](luis-reference-regions.md) -portál összes alkalmazásán. Ha az Authoring Tool API-kon keresztül az alkalmazások készítésének, értékét módosítani szeretné `Ocp-Apim-Subscription-Key` az új kulccsal.

## <a name="delete-account"></a>Fiók törlése

Lásd: [adattárolási és -eltávolítási](luis-concept-data-storage.md#accounts) milyen adat törlődik, ha a fiók törlése kapcsolatos információkat.

## <a name="next-steps"></a>További lépések

Tudjon meg többet a [kulcs létrehozási](luis-concept-keys.md#authoring-key).


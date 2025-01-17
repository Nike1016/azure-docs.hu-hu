---
title: Webes API-kat meghívó mobil alkalmazás (áttérés éles környezetbe) – Microsoft Identity platform
description: Megtudhatja, hogyan hozhat létre webes API-kat meghívó mobil alkalmazást (az éles környezetbe)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 67788dd9257a0a4685313dce44c6a3dfb5e514df
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68413609"
---
# <a name="mobile-app-that-calls-web-apis---move-to-production"></a>Webes API-kat meghívó mobil alkalmazás – áttérés éles környezetbe

Ez a cikk részletesen ismerteti, hogy miként javítható az alkalmazás minősége és megbízhatósága, mielőtt az éles környezetbe helyezné őket.

## <a name="handling-errors-in-mobile-applications"></a>Hibák feldolgozása a Mobile Applications szolgáltatásban

Ezen a ponton számos hiba fordulhat elő az alkalmazásban. A kezelés fő forgatókönyvei a beavatkozás nélküli hibák és tartalékok. Más feltételek, amelyeket érdemes figyelembe venni az éles környezetben, nem hálózati helyzetek, szolgáltatási kimaradások, rendszergazdai belefoglalási követelmények és egyéb forgatókönyv-specifikus esetekben.

Minden MSAL-könyvtárhoz tartozik egy mintakód és egy wiki-tartalom, amely leírja, hogyan kell kezelni ezeket a feltételeket:

- [MSAL Android Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigating-and-investigating-issues"></a>Problémák enyhítése és kivizsgálása

Az alkalmazásban felmerülő problémák diagnosztizálásához segíti az adatok gyűjtését. További információ a gyűjtött adattípusokról: MSAL platform wikik.

- A felhasználók segítséget kérhetnek, amikor problémákba ütköznek. Az ajánlott eljárás az, hogy rögzítse és ideiglenesen tárolja a naplókat, és adjon meg egy helyet, ahol a felhasználók fel tudják tölteni őket. A MSAL naplózási bővítményeket biztosít a hitelesítés részletes adatainak rögzítéséhez.
- Ha elérhető, engedélyezze a telemetria a MSAL-n keresztül, hogy adatokat gyűjtsön arról, hogy a felhasználók hogyan jelentkeznek be az alkalmazásba.

## <a name="next-steps"></a>További lépések

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

Próbálja ki a mintákból elérhető további mintákat [| Asztali és mobil nyilvános ügyfélalkalmazások](sample-v2-code.md#desktop-and-mobile-public-client-apps)

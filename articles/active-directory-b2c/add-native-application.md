---
title: Adjon hozzá egy natív ügyfélalkalmazás – Azure Active Directory B2C |} A Microsoft Docs
description: Ismerje meg, hogyan adhat hozzá az Active Directory B2C-bérlő natív ügyfélalkalmazás.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: b4e9b95cb226aeec686816d0ed7160062e110c62
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66511824"
---
# <a name="add-a-native-client-application-to-your-azure-active-directory-b2c-tenant"></a>Egy natív ügyfélalkalmazást az Azure Active Directory B2C-bérlő hozzáadása

Natív ügyfél erőforrások a bérlőben, mielőtt az alkalmazás képes kommunikálni az Azure Active Directory B2C regisztrálni kell.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
3. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
1. Válassza ki **alkalmazások**, majd válassza ki **Hozzáadás**.
2. Adjon meg egy nevet az alkalmazásnak. Ha például *nativeapp1*.
3. A **közé tartozik a webalkalmazás vagy webes API**válassza **nem**.
4. A **natív ügyfél belefoglalása**válassza **Igen**.
5. A **átirányítási URI-t**, adjon meg egy érvényes átirányítási URI-t egy egyéni sémával. Két szempontot fontos egy átirányítási URI-JÁNAK kiválasztásakor:

    - **Egyedi** -átirányítási URI sémájának minden alkalmazás esetén egyedinek kell lennie. A példában `com.onmicrosoft.contoso.appname://redirect/path`, `com.onmicrosoft.contoso.appname` a séma. Ezt a mintát kell követnie. Ha két alkalmazás ugyanazt a sémát használja, a felhasználó a hibajelentési szolgáltatás egy alkalmazás kiválasztásához. Ha a felhasználó helytelenül választ, a bejelentkezés meghiúsul.
    - **Teljes** -átirányítási URI-JÁNAK rendelkeznie kell egy sémát és a egy elérési utat. Az elérési utat a tartomány után legalább egy perjelet tartalmaznia kell. Ha például `//contoso/` működik és `//contoso` sikertelen lesz. Győződjön meg arról, hogy az átirányítási URI-t nem tartalmazza a különleges karaktereket, például aláhúzásjeleket.

6. Kattintson a **Create** (Létrehozás) gombra.
7. A Tulajdonságok lapon jegyezze fel az Alkalmazásazonosítót, amely natív ügyfélalkalmazás konfigurálása során fogja használni.

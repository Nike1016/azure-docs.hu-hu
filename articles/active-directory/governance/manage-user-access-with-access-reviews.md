---
title: Felhasználói hozzáférés kezelése hozzáférési felülvizsgálatokkal – Azure Active Directory | Microsoft Docs
description: Megtudhatja, hogyan kezelhetők a felhasználói hozzáférések csoporttagságként vagy alkalmazáshoz való hozzárendelésként az Azure Active Directory hozzáférési felülvizsgálatokkal
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 06/21/2018
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70b6d925772548e6703b57c265ebacf6b7f3f4c9
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499645"
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>Felhasználói hozzáférés felügyelete az Azure AD hozzáférési felülvizsgálatokkal

Az Azure Active Directoryval (Azure AD) könnyedén biztosíthatja a felhasználók megfelelő hozzáférését. Megkérheti magukat a felhasználókat, esetleg egy döntéshozót, hogy vegyenek részt a hozzáférési felülvizsgálatban és hitelesítsék újból (vagy igazolják) a felhasználó hozzáférését. A felülvizsgálatot végzők az Azure AD-ből származó javaslatok alapján dönthetnek arról, hogy az egyes felhasználóknak szükségük van-e folyamatos hozzáférésre. A hozzáférési felülvizsgálat lezárása után módosításokat végezhet, és eltávolíthatja azon felhasználók hozzáférését, akiknek többé nincs rá szükségük.

> [!NOTE]
> Ha csak a vendégfelhasználók hozzáférését szeretné felülvizsgálni, akkor tekintse meg [vendégfelhasználók hozzáférésének a hozzáférési felülvizsgálatokkal történő felügyeletével](manage-guest-access-with-access-reviews.md) foglalkozó részt. Ha a rendszergazdai szerepkörrel rendelkező felhasználók (például a globális rendszergazdák) tagságát szeretné felülvizsgálni, tekintse meg a [hozzáférési felülvizsgálat az Azure AD Privileged Identity Management alkalmazásban történő indításával](../privileged-identity-management/pim-how-to-start-security-review.md) foglalkozó cikket.

## <a name="prerequisites"></a>Előfeltételek

- Azure AD Premium P2

További információ: [mely felhasználóknak kell licenceket tartalmazniuk?](access-reviews-overview.md#which-users-must-have-licenses)

## <a name="create-and-perform-an-access-review"></a>Hozzáférési felülvizsgálat létrehozása és végrehajtása

A hozzáférési felülvizsgálatokban egy vagy több felhasználó is szerepelhet felülvizsgálóként.  

1. Válasszon ki egy legalább egy taggal rendelkező csoportot az Azure AD-ben. Vagy válasszon ki egy olyan, az Azure AD-hez csatlakozó alkalmazást, amelyhez legalább egy felhasználó hozzá van rendelve. 

2. Döntse el, hogy minden felhasználó a saját hozzáférését vizsgálja-e felül, vagy inkább kijelöl egy vagy több felhasználót a többiek hozzáférésének felülvizsgálatára.

3. Globális rendszergazdaként vagy felhasználói rendszergazdaként nyissa meg az [Identity irányítás lapot](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

4. Hozza létre a hozzáférési felülvizsgálatot. További információ: [csoportok vagy alkalmazások hozzáférési felülvizsgálatának létrehozása](create-access-review.md).

5. Ha a hozzáférési felülvizsgálat elindul, kérje meg a véleményezőket, hogy adja meg a bemenetet. Alapértelmezés szerint ezek mindegyike egy, a hozzáférési panelre mutató hivatkozást tartalmazó e-mailt kap az Azure AD-től, ahol áttekintik a [csoportokhoz vagy alkalmazásokhoz való hozzáférést](perform-access-review.md).

6. Ha a felülvizsgálók nem végezték el a felülvizsgálatot, az Azure AD-n keresztül emlékeztetőt is küldhet nekik. Alapértelmezés szerint az Azure AD a rendelkezésre álló idő felénél automatikusan emlékeztetőt küld azoknak a felülvizsgálóknak, akik még nem tettek eleget a kérésnek.

7. Ha mindenki elvégezte a felülvizsgálatot, állítsa le a hozzáférési felülvizsgálatot, és alkalmazza a módosításokat. További információ: [csoportok vagy alkalmazások hozzáférési felülvizsgálatának befejezése](complete-access-review.md).


## <a name="next-steps"></a>További lépések

[Csoportok vagy alkalmazások hozzáférési felülvizsgálatának létrehozása](create-access-review.md)





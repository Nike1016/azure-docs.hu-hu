---
title: Segítség kérése a saját alkalmazások portálján – Azure Active Directory | Microsoft Docs
description: Segítség kérése a bejelentkezéshez és a gyakori feladatok elvégzéséhez a saját alkalmazások portálon.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11a7376720661e13a03be65df4ee180e0bca20c3
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383078"
---
# <a name="troubleshoot-problems-with-the-my-apps-portal"></a>A My apps portál problémáinak elhárítása

Ha problémákba ütközik a **saját alkalmazások** portálra való bejelentkezéskor vagy a használatával kapcsolatban, próbálja meg ezeket a hibaelhárítási tippeket, mielőtt az ügyfélszolgálathoz vagy a rendszergazdához fordul segítségért.

## <a name="im-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>Nem sikerül telepíteni a saját alkalmazások biztonságos bejelentkezési bővítményét

Ha problémákat tapasztal a saját alkalmazások biztonságos bejelentkezési bővítményének telepítésekor:

- Győződjön meg arról, hogy támogatott böngészőt használ, beleértve a következőket:

    - **Microsoft Edge.** Windows 10 évfordulós kiadáson vagy újabb rendszeren fut.

    - **Google Chrome.** Windows 7 vagy újabb, illetve Mac OS X vagy újabb rendszeren fut.

    - **Mozilla Firefox 26,0 vagy újabb verzió.** Windows XP SP2 vagy újabb rendszeren, valamint Mac OS X 10,6-es vagy újabb verzión fut.

    - **Internet Explorer 11.** Windows 7 vagy újabb rendszeren fut (korlátozott támogatás).

- Győződjön meg arról, hogy a böngésző bővítményének beállításai be vannak kapcsolva.

- Próbálkozzon újra a böngésző újraindításával, és jelentkezzen be újra a **saját alkalmazások** portálra.

- Próbálja meg törölni a böngésző cookie-jait, majd indítsa újra, majd jelentkezzen be újra a **saját alkalmazások** portálra.

## <a name="i-cant-sign-in-to-the-my-apps-portal"></a>Nem tudok bejelentkezni a **My apps** portálra

Ha nem sikerül bejelentkeznie a **My apps** portálra, a következőket teheti meg:

- Győződjön meg arról, hogy a megfelelő URL-címet használja. A szervezetnek https://myapps.microsoft.com vagy egy testreszabott lapnak kell lennie, https://myapps.microsoft.com/contoso.com például:.

- Győződjön meg arról, hogy a jelszó helyes, és nem járt le. További információ: [a munkahelyi vagy iskolai jelszavának](active-directory-passwords-update-your-own-password.md)alaphelyzetbe állítása.

- Győződjön meg arról, hogy az ellenőrzési adatok naprakészek és pontosak. További információ: mit jelent [Az Azure multi-Factor Authentication a számomra?](multi-factor-authentication-end-user.md) vagy [a biztonsági adatokra vonatkozó módszerek és információk módosítása](security-info-add-update-methods-overview.md).

- Adja hozzá a **My app** Portal URL-címét az **Internet Properties > Security > megbízható helyek** beállításhoz.

- Törölje a böngésző gyorsítótárát, és próbálkozzon újra a bejelentkezéssel.

## <a name="my-password-isnt-working"></a>A jelszavam nem működik

Ha elfelejtette a jelszavát, soha nem kapta meg az egyiket a szervezettől, a fiókja ki van zárva, vagy módosítani szeretné a jelszavát, lásd: [Súgó, elfelejtettem az Azure ad](active-directory-passwords-update-your-own-password.md)-jelszavam.

## <a name="i-want-to-be-able-to-reset-my-own-password"></a>Szeretném visszaállítani a saját jelszavát

A saját jelszavának alaphelyzetbe állításához a rendszergazdának először be kell kapcsolnia a szervezet szolgáltatását, majd frissítenie és ellenőriznie kell a szükséges ellenőrzési módszereket. Az ellenőrzési módszerek frissítésével kapcsolatos további információkért lásd: [regisztráció az önkiszolgáló jelszó-visszaállításhoz](active-directory-passwords-reset-register.md).

## <a name="im-getting-an-access-denied-message-when-i-start-an-app"></a>Hozzáférés-megtagadási üzenetet kapok az alkalmazás indításakor

Ha **hozzáférés** -megtagadási üzenetet kap az alkalmazásnak az **alkalmazás** -portálról történő elindítása után, a következőket teheti meg:

- Győződjön meg arról, hogy telepítette a [saját alkalmazások biztonságos bejelentkezési bővítményét](my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) , és hogy [támogatott böngészőt](my-apps-portal-end-user-access.md#supported-browsers)használ.

- Győződjön meg arról, hogy a megfelelő URL-címet használja az alkalmazáshoz, és hogy az URL-cím az **Internet tulajdonságok > biztonsági > megbízható helyek** listáján található.

- Győződjön meg arról, hogy a jelszó helyes, és nem járt le. További információ: [a munkahelyi vagy iskolai jelszavának](active-directory-passwords-update-your-own-password.md)alaphelyzetbe állítása.

- Győződjön meg arról, hogy az ellenőrzési adatok naprakészek és pontosak. További információ: mit jelent [Az Azure multi-Factor Authentication a számomra?](multi-factor-authentication-end-user.md) vagy [a biztonsági adatokra vonatkozó módszerek és információk módosítása](security-info-add-update-methods-overview.md).

- Törölje a böngésző gyorsítótárát, és próbálkozzon újra a bejelentkezéssel.

Ha az ilyen dolgok kipróbálása után sem tud hozzáférni az alkalmazáshoz, segítségért forduljon a szervezet ügyfélszolgálatához.

## <a name="next-steps"></a>További lépések

Miután bejelentkezett a **saját alkalmazások** portálra, frissítheti a profilját és a fiókadatok adatait, a csoport adatait és a hozzáférési felülvizsgálati információkat (ha van ilyen engedélye).

- [Alkalmazások elérése és használata a saját alkalmazások portálon](my-apps-portal-end-user-access.md).

- [Módosítsa a profil adatait](my-apps-portal-end-user-update-profile.md).

- [A csoportok kapcsolódó információinak megtekintése és frissítése](my-apps-portal-end-user-groups.md).

- [Saját hozzáférési felülvizsgálatok végrehajtása](my-apps-portal-end-user-access-reviews.md).

---
title: Problémák az összevont egyszeri bejelentkezés konfigurálásával az Azure AD Gallery-alkalmazásokhoz | Microsoft Docs
description: Az összevont egyszeri bejelentkezés az SAML használatával való konfigurálásakor előforduló gyakori problémák némelyike az Azure AD Application Galleryben felsorolt alkalmazások esetében
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f4e690b440604a312ddfa8cd320ac200c0f40cf
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381221"
---
# <a name="problem-configuring-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Probléma van az összevont egyszeri bejelentkezés konfigurálásával egy Azure AD Gallery-alkalmazáshoz

Ha probléma merül fel az alkalmazás konfigurálásakor. Győződjön meg arról, hogy követte az alkalmazáshoz tartozó oktatóanyag összes lépését. Az alkalmazás konfigurációjában az alkalmazás konfigurálásával kapcsolatos dokumentációban talál további tájékoztatást. Azt is megteheti, hogy elérhetővé teszi az [oktatóanyagok listáját az SaaS-alkalmazások Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) -nal való integrálásához, részletes útmutatást nyújtva.

## <a name="cant-add-another-instance-of-the-application"></a>Nem lehet felvenni az alkalmazás egy másik példányát.

Egy alkalmazás második példányának hozzáadásához a következőket kell tudnia:

-   Konfigurálja a második példány egyedi azonosítóját. Az első példányhoz használt azonosító nem konfigurálható.

-   Állítson be egy másik tanúsítványt, mint az első példánynál használt tanúsítvány.

Ha az alkalmazás nem támogatja a fentiek egyikét sem. Ezt követően nem fog tudni konfigurálni egy második példányt.

## <a name="cant-add-the-identifier-or-the-reply-url"></a>Nem adható hozzá az azonosító vagy a válasz URL-címe

Ha nem tudja konfigurálni az azonosítót vagy a válasz URL-címét, erősítse meg, hogy az azonosító és a válasz URL-értékei megegyeznek az alkalmazáshoz előre konfigurált mintákkal.

Az alkalmazáshoz előre konfigurált mintázatok ismerete:

1. Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként. Ugorjon a 7. lépésre. Ha már szerepel az Azure AD alkalmazás-konfiguráció paneljén.

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6. Válassza ki azt az alkalmazást, amelyhez az egyszeri bejelentkezést konfigurálni kívánja.

7. Az alkalmazás betöltése után kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjében.

8. Válassza ki az **SAML-alapú bejelentkezés** lehetőséget a **mód** legördülő listából.

9. Lépjen az **azonosító** vagy a **Válasz URL-címe** szövegmezőbe a **tartomány és URL-címek szakaszban.**

10. Az alkalmazás által támogatott minták háromféle módon ismertek:

    * A szövegmezőben a támogatott mintázat (ok) helyőrző példaként jelenik meg *:* <https://contoso.com>.

    * Ha a minta nem támogatott, piros felkiáltójel jelenik meg, amikor megpróbálja beírni az értéket a szövegmezőbe. Ha az egérmutatót a piros felkiáltójel fölé viszi, a támogatott minták láthatók.

    * Az alkalmazás oktatóanyagában a támogatott mintákkal kapcsolatos információkat is lekérheti. Az **Azure ad egyszeri bejelentkezés konfigurálása** szakaszban talál. Ugorjon a **tartomány és URL-címek** szakaszban található értékek konfigurálására szolgáló lépésre.

Ha az értékek nem egyeznek az Azure AD-ben előre konfigurált mintákkal. A következőket teheti:

-   Az alkalmazás gyártójának használata az Azure AD-ben előre konfigurált mintának megfelelő értékek beolvasásához

-   Vagy megadhatja az Azure ad-csapatot <aadapprequest@microsoft.com> az oktatóanyagban, vagy elhagyhat egy megjegyzést az alkalmazás által támogatott minták frissítésének igényléséhez

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Hol állítható be a EntityID (felhasználói azonosító) formátuma

Nem lehet kijelölni azt a EntityID (felhasználóazonosító), amelyet az Azure AD az alkalmazásnak a felhasználó hitelesítése utáni válaszban küld.

Az Azure AD kiválasztja a NameID attribútum (felhasználói azonosító) formátumát a kiválasztott érték vagy az alkalmazás által az SAML-AuthRequest kért formátum alapján. További információkért tekintse meg az [egyszeri bejelentkezés SAML protokollját](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) a NameIDPolicy szakaszban.

## <a name="cant-find-the-azure-ad-metadata-to-complete-the-configuration-with-the-application"></a>Nem található az Azure AD-metaadatok a konfiguráció végrehajtásához az alkalmazással

Az alkalmazás metaadatainak vagy tanúsítványának az Azure AD-ből való letöltéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6. Válassza ki azt az alkalmazást, amelyhez konfigurálta az egyszeri bejelentkezést.

7. Az alkalmazás betöltése után kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjében.

8. Nyissa meg az **SAML aláíró tanúsítvány** szakaszt, majd kattintson az oszlop értékének **letöltése** elemre. Attól függően, hogy az alkalmazásnak hogyan kell konfigurálnia az egyszeri bejelentkezést, megjelenik a metaadatok XML-fájljának vagy a tanúsítványnak a letöltésére szolgáló lehetőség.

Az Azure AD nem biztosít URL-címet a metaadatok beszerzéséhez. A metaadatokat csak XML-fájlként lehet beolvasni.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Nem tudom, hogyan szabhatja testre az alkalmazásnak eljuttatott SAML-jogcímeket

Ha meg szeretné tudni, hogyan szabhatja testre az alkalmazásnak eljuttatott SAML-attribútumok jogcímeit, tekintse meg a következő témakört: [jogcím-hozzárendelés Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

## <a name="next-steps"></a>További lépések
[Alkalmazások kezelése az Azure Active Directoryval](what-is-application-management.md)

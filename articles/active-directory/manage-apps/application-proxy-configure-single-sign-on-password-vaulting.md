---
title: Egyszeri bejelentkezés alkalmazásokhoz az Azure AD-alkalmazásproxyval |} A Microsoft Docs
description: Kapcsolja be egyszeri bejelentkezést a közzétett helyszíni alkalmazásait az Azure AD-alkalmazásproxy az Azure Portalon.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18510bd7ace6ca87278b5bf68f79b372251ac0e1
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807825"
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>Az egyszeri bejelentkezés alkalmazásproxyval való vaulting jelszó

Az Azure Active Directory Application Proxy segítségével növelje hatékonyságát azáltal, hogy a távoli alkalmazottak biztonságosan érhetik el azokat, túl a helyszíni alkalmazás-közzététel. Az Azure Portalon is állíthat be egyszeri bejelentkezéssel (SSO) ezekre az alkalmazásokra. A felhasználók csak az Azure AD-hitelesítést kell, és nem kell ismét jelentkezzen be a vállalati alkalmazás eléréséhez.

Az alkalmazásproxy támogatja több [egyszeri bejelentkezési módok](what-is-single-sign-on.md#choosing-a-single-sign-on-method). Jelszóalapú bejelentkezés célja az alkalmazásokat, amelyek egy felhasználónév – jelszó kombinációt használja a hitelesítéshez. Az alkalmazás konfigurálása a jelszóalapú bejelentkezés, a felhasználóknak kell jelentkezzen be a helyszíni alkalmazás egyszer. Ezt követően az Azure Active Directory tárolja a bejelentkezési adatait, és automatikusan átadja az alkalmazás, amikor a felhasználók távolról elérni.

Érdemes már közzétett és tesztelte az alkalmazást az alkalmazásproxy használatával. Ha nem, kövesse a lépéseket a [alkalmazások közzététele az Azure AD-alkalmazásproxy](application-proxy-add-on-premises-application.md) majd térjen vissza ide.

## <a name="set-up-password-vaulting-for-your-application"></a>Az alkalmazás vaulting jelszó beállítása

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) felületére rendszergazdaként.
1. Válassza ki **Azure Active Directory** > **vállalati alkalmazások** > **minden alkalmazás**.
1. A listában jelölje ki az alkalmazást, amelyet szeretne az egyszeri bejelentkezés beállítása.  
1. Válassza ki **egyszeri bejelentkezési**.

   ![Egyszeri bejelentkezés az alkalmazás áttekintése oldalon válassza ki](./media/application-proxy-configure-single-sign-on-password-vaulting/select-sso.png)

1. Az egyszeri bejelentkezési módot válassza **jelszóalapú bejelentkezés**.
1. A bejelentkezési URL-címet adja meg az URL-címet az oldal amelyen a felhasználók megadják felhasználónevüket és jelszavukat, jelentkezzen be az alkalmazást a vállalati hálózaton kívül. Ez lehet a külső URL-CÍMÉT, amikor az alkalmazás alkalmazásproxyn keresztül közzétett létrehozott.

   ![Válassza ki a jelszóalapú bejelentkezés, és adja meg az URL-cím](./media/application-proxy-configure-single-sign-on-password-vaulting/password-sso.png)

1. Kattintson a **Mentés** gombra.

<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Az alkalmazás tesztelése

Nyissa meg a külső URL-cím, amely az alkalmazás konfigurálva a távoli hozzáféréshez. Jelentkezzen be hitelesítő adataival, amelyet az alkalmazás (vagy egy Ön által beállított hozzáférési a teszt fiók hitelesítő adatait). Miután a bejelentkezés sikeres, hagyja meg az alkalmazást, és térjen vissza a hitelesítő adatok újbóli megadása nélkül kell lennie.

## <a name="next-steps"></a>További lépések

- Olvassa el más módokon megvalósításához [egyszeri bejelentkezés](what-is-single-sign-on.md)
- Ismerje meg [fér hozzá az alkalmazásokhoz távolról az Azure AD-alkalmazásproxy történő futtatásának biztonsági szempontjai](application-proxy-security.md)

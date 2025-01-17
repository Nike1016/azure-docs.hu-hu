---
title: A Cloud App Securityvel – az Azure Active Directory a helyszíni alkalmazások integrálása |} A Microsoft Docs
description: A helyszíni alkalmazások konfigurálása az Azure Active Directoryban, a Microsoft Cloud App Security (MCAS) működik. Használja az MCAS feltételes hozzáférést biztosító alkalmazás-vezérlő valós idejű monitorozását és ellenőrzését munkamenetek feltételes hozzáférési szabályzatok alapján. Ezek a szabályzatok a helyszíni alkalmazások az Azure Active Directoryban (Azure AD) alkalmazásproxy használó alkalmazhat.
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7b9b27a3b8329f906b1adfd48d28892a7edc4d1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108447"
---
# <a name="configure-real-time-application-access-monitoring-with-microsoft-cloud-app-security-and-azure-active-directory"></a>Valós idejű hozzáférés az alkalmazásfigyelés konfigurálása a Microsoft Cloud App Security és az Azure Active Directory
Konfigurálja a Microsoft Cloud App Security (MCAS) használata az Azure Active Directoryban (Azure AD) egy helyszíni alkalmazás valós idejű monitorozásra. MCAS használja a feltételes hozzáférést biztosító alkalmazás-vezérlő figyeléséhez, és valós idejű vezérlés munkamenetek feltételes hozzáférési szabályzatok alapján. Ezek a szabályzatok a helyszíni alkalmazások az Azure Active Directoryban (Azure AD) alkalmazásproxy használó alkalmazhat.

Íme néhány példa a különböző típusú házirendet hozhat létre az MCAS:

- Letiltása, vagy a letöltés nem felügyelt eszközökön bizalmas dokumentumok védelme.
- Figyelő, ha magas kockázatú felhasználók jelentkezzen be az alkalmazásokat, és ezután azok a műveletek a munkameneten belül. Ezekkel az információkkal elemezheti a felhasználói viselkedés határozza meg, hogyan alkalmazhatja a munkamenet-szabályzatokat.
- Ügyféltanúsítványok vagy az eszközmegfelelőség használatával nem felügyelt eszközökről való adott alkalmazások letiltására.
- Korlátozza a felhasználói munkamenetek nem céges hálózatokról. Korlátozott hozzáférést biztosíthat a felhasználók számára a vállalati hálózaton kívülről származó alkalmazáshoz való hozzáférés. Például a korlátozott hozzáférésű blokkolhatja a felhasználónak a bizalmas dokumentumok letöltése.

További információkért lásd: [alkalmazások védelme a Microsoft Cloud App Security feltételes hozzáférést alkalmazás vezérlő az](/cloud-app-security/proxy-intro-aad).

## <a name="requirements"></a>Követelmények

Licenc:

- Az EMS E5 licencet, vagy 
- Prémium P1 szintű Azure Active Directory és az MCAS önálló.

A helyszíni alkalmazás:

- A helyszíni alkalmazás a Kerberos által korlátozott delegálás (KCD) kell használnia.

Proxy konfigurálása:

- Az Azure AD Application Proxy, többek között a környezet előkészítése és az alkalmazásproxy-összekötő telepítése használatára konfigurálja. Foglalkozó oktatóanyagért lásd: [egy proxyn keresztül történő alkalmazás távoli hozzáférés a helyszíni alkalmazások hozzáadása az Azure ad-ben](application-proxy-add-on-premises-application.md). 

## <a name="add-on-premises-application-to-azure-ad"></a>Az Azure ad-hez a helyszíni alkalmazás hozzáadása

Az Azure ad-hez a helyszíni alkalmazás hozzáadása. A rövid útmutatóban talál [helyszíni alkalmazás hozzáadása az Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). Amikor az alkalmazás ad hozzá, ügyeljen arra, hogy állítsa be a következő két beállítást a **adja hozzá a helyszíni alkalmazás** panelen:

- **Előhitelesítés**: Adja meg **az Azure Active Directory**.
- **A kérelem törzsében URL-címek lefordítása**: Válasszon **Igen**.

E két beállítás a szolgáltatásalkalmazás alkalmassá tétele MCAS szükségesek.

## <a name="test-the-on-premises-application"></a>A helyszíni alkalmazás tesztelése

Miután hozzáadta az alkalmazást az Azure AD, szereplő lépések segítségével [az alkalmazás tesztelése](application-proxy-add-on-premises-application.md#test-the-application) felhasználó hozzáadása teszteléshez, és tesztelje a bejelentkezést. 

## <a name="deploy-conditional-access-app-control"></a>Feltételes hozzáférési Alkalmazásvezérlés üzembe helyezése

Az alkalmazás konfigurálása a feltételes hozzáférési alkalmazásvezérléssel, kövesse a [üzembe helyezése a feltételes hozzáférési Alkalmazásvezérlés az Azure AD-alkalmazásokhoz](/cloud-app-security/proxy-deployment-aad).


## <a name="test-conditional-access-app-control"></a>Teszt feltételes hozzáférést biztosító alkalmazás-vezérlő

Feltételes hozzáférési Alkalmazásvezérlés az Azure AD-alkalmazások központi telepítésének tesztelése, kövesse a [tesztelheti az üzembe helyezés az Azure AD-alkalmazásokhoz](/cloud-app-security/proxy-deployment-aad).






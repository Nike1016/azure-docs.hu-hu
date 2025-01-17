---
title: Egyéni tartományok az Azure AD-alkalmazásproxy |} A Microsoft Docs
description: Egyéni tartományok az Azure AD-alkalmazásproxy kezelése, úgy, hogy az alkalmazás URL-címe ugyanaz, függetlenül attól, ahol a felhasználók-e férni.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/31/2018
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82b8dcfa02d21183a06fa510adb774338e72cb4e
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68851723"
---
# <a name="working-with-custom-domains-in-azure-ad-application-proxy"></a>Egyéni tartományok használata az Azure AD-alkalmazásproxy

Amikor közzétesz egy alkalmazást az Azure Active Directory Application Proxy keresztül, létre kell hoznia egy külső URL-címet a továbblépéshez, ha azok dolgozik-e távolról a felhasználók számára. Az URL-címet lekéri az alapértelmezett tartomány *yourtenant.msappproxy.net*. Például ha közzétett alkalmazás nevű költségek és a bérlő neve Contoso, akkor a külső URL-cím `https://expenses-contoso.msappproxy.net`. Ha saját tartománynevét használja, az alkalmazás egyéni tartomány konfigurálása. 

Azt javasoljuk, hogy beállította az egyéni tartományokban az alkalmazásokhoz, amikor csak lehetséges. Egyéni tartományok előnyei a következők:

- A felhasználók férhetnek hozzá az alkalmazás ugyanazon URL-címét, belül vagy kívül a hálózat működnek-e.
- Ha az összes alkalmazás ugyanazon belső és külső URL-címeket, majd hivatkozásokat egy alkalmazás egy másik továbbra is működni a vállalati hálózaton kívül is. 
- Szabályozhatja a márkajelzési beállításokat, és az URL-címeket szeretne létrehozni. 


## <a name="configure-a-custom-domain"></a>Egyéni tartomány konfigurálása

### <a name="prerequisites"></a>Előfeltételek

Mielőtt egy egyéni tartományt állít be, győződjön meg arról, hogy rendelkezik-e előkészítve az alábbi követelményeknek: 
- A [ellenőrzött tartomány hozzáadása az Azure Active Directoryhoz](../fundamentals/add-custom-domain.md).
- Egyéni-tanúsítványt a tartomány, a PFX-fájlok formájában. 
- A helyszíni alkalmazás [alkalmazásproxyn keresztül közzétett](application-proxy-add-on-premises-application.md).

### <a name="configure-your-custom-domain"></a>Az egyéni tartomány konfigurálása

Ha készen áll három követelményekről, kövesse az alábbi lépéseket az egyéni tartomány beállításához:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Navigáljon a **Azure Active Directory** > **vállalati alkalmazások** > **minden alkalmazás** , és válassza ki a kezelni kívánt alkalmazást.
3. Válassza ki **alkalmazásproxy**. 
4. A külső URL-cím mezőben használja a legördülő listából válassza ki az egyéni tartomány. Ha nem látja a tartományt a listán, majd azt még nem lett ellenőrizni még. 
5. Válassza ki **mentése**
5. A **tanúsítvány** mező, amely le lett tiltva lesz engedélyezve. Válassza ezt a mezőt. 

   ![Kattintson ide egy tanúsítvány feltöltése](./media/application-proxy-configure-custom-domain/certificate.png)

   Ha már töltött fel egy tanúsítványt a tartományhoz, a tanúsítvány mezőjében a tanúsítvány adatait jeleníti meg. 

6. A PFX-tanúsítvány feltöltése, és adja meg a jelszót a tanúsítványhoz. 
7. Válassza ki **mentése** a módosítások mentéséhez. 
8. Adjon hozzá egy [DNS-rekord](../../dns/dns-operations-recordsets-portal.md) az új külső URL-címet, amely átirányítja a msappproxy.net tartományt.
9. Ellenőrizze, hogy a DNS-rekord megfelelően van-e konfigurálva az [nslookup](https://social.technet.microsoft.com/wiki/contents/articles/29184.nslookup-for-beginners.aspx) paranccsal annak ellenőrzéséhez, hogy a külső URL-cím elérhető-e, és hogy a msapproxy.net tartomány aliasként jelenik-e meg.

>[!TIP] 
>Csak egy egyéni tartományt egy tanúsítvány feltöltéséhez kell. Miután a tanúsítvány feltöltése, kiválaszthatja az egyéni tartomány, amikor közzétesz egy új alkalmazást, és nem kell tennie, hogy a DNS-rekord kivételével további konfigurációs. 

## <a name="manage-certificates"></a>Tanúsítványok kezelése

### <a name="certificate-format"></a>Tanúsítvány formátuma
A tanúsítvány-aláírás módszerek korlátozva van. Elliptikus görbéjű titkosítási (ECC), a tulajdonos alternatív nevére (SAN) és egyéb gyakori tanúsítványtípusok egyaránt támogatottak. 

Helyettesítő tanúsítvány mindaddig, amíg a helyettesítő karakteres megegyezik a kívánt külső URL-cím használható.

A saját nyilvános kulcsokra épülő infrastruktúrája (PKI) által kiadott tanúsítványok akkor használhatók, ha a tanúsítványlánc telepítve van az ügyféleszközök számára. Az Intune használatával ezeket a tanúsítványokat a felügyelt eszközökön is üzembe helyezheti. Nem felügyelt eszközök esetén ezeket a tanúsítványokat manuálisan kell telepíteni.

### <a name="changing-the-domain"></a>A tartomány módosítása
Összes ellenőrzött tartományt az alkalmazás a külső URL-cím legördülő listában jelennek meg. Ha módosítani szeretné a tartományhoz, csak frissítse ezt a mezőt az alkalmazás. Ha azt szeretné, a tartomány nem szerepel a listán, [, egy ellenőrzött tartomány hozzáadása](../fundamentals/add-custom-domain.md). Ha olyan tartományhoz, amely rendelkezik egy társított tanúsítvány még nem, kövesse a lépéseket a tanúsítvány hozzáadása 5 – 7. Ezután ellenőrizze a DNS-rekord az új külső URL-átirányítás frissítenie. 

### <a name="certificate-management"></a>Tanúsítványok kezelése
Használhatja ugyanazt a tanúsítványt több alkalmazáshoz, kivéve, ha az alkalmazások megosztása külső gazdagépek. 

Megjelenik egy figyelmeztetés, ha a tanúsítvány lejár, a portálon keresztül egy másik tanúsítvány feltöltése közben. A tanúsítvány visszavonása esetén a felhasználók jelenhet meg a biztonsági figyelmeztetés az alkalmazás elérésekor. A tanúsítványok visszavonási ellenőrzést nem elvégzése.  Egy adott alkalmazáshoz tartozó tanúsítvány frissítéséhez nyissa meg az alkalmazást, majd kövesse 5 – 7 az egyéni tartományok konfigurálása a közzétett alkalmazások egy új tanúsítvány feltöltéséhez. Ha a régi tanúsítvány más alkalmazások nem használja, az automatikusan törlődik. 

Minden tanúsítványkezelés jelenleg egyes webhelyei keresztül, a vonatkozó kérelmek kontextusában a tanúsítványok kezeléséhez szüksége. 

## <a name="next-steps"></a>További lépések
* [Egyszeri bejelentkezés engedélyezése](application-proxy-configure-single-sign-on-with-kcd.md) a közzétett alkalmazásokba az Azure AD-hitelesítés.
* [Feltételes hozzáférés engedélyezése](https://docs.microsoft.com/en-us/azure/active-directory/conditional-access/technical-reference#cloud-apps-assignments) a közzétett alkalmazásokhoz.
* [Az egyéni tartománynév hozzáadása az Azure ad-ben](../fundamentals/add-custom-domain.md)



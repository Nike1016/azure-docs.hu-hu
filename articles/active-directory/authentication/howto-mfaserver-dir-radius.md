---
title: RADIUS-hitelesítés és Azure MFA-kiszolgáló – az Azure Active Directory
description: A RADIUS-hitelesítés és az Azure Multi-Factor Authentication-kiszolgáló üzembe helyezése.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/31/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17e040492b1d986215aeb77ea14ebff53946f78e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056008"
---
# <a name="integrate-radius-authentication-with-azure-multi-factor-authentication-server"></a>RADIUS-hitelesítés integrálása az Azure Multi-Factor Authentication-kiszolgálóval

A RADIUS egy szabványos protokoll hitelesítési kérések fogadására és feldolgozására. Az Azure Multi-Factor Authentication-kiszolgáló RADIUS-kiszolgálóként is használható. A kétlépéses ellenőrzés hozzáadásához szúrja be a kiszolgálót a RADIUS-ügyfél (VPN-berendezés) és a hitelesítési cél közé. A hitelesítési cél Active Directory, LDAP-címtár vagy egy másik RADIUS-kiszolgáló is lehet. Az Azure Multi-Factor Authentication (MFA) működéséhez az Azure MFA-kiszolgálót úgy kell konfigurálni, hogy az ügyfélkiszolgálókkal és a hitelesítési céllal is tudjon kommunikálni. Az Azure MFA-kiszolgáló fogadja a RADIUS-ügyfél kéréseit, ellenőrzi a hitelesítő adatokat a hitelesítési célon, Azure Multi-Factor Authenticationt biztosít és választ küld a RADIUS-ügyfélnek. A hitelesítés csak akkor sikeres, ha az elsődleges hitelesítés és az Azure Multi-Factor Authentication is sikeres.

> [!IMPORTANT]
> 2019\. július 1-től a Microsoft már nem szavatolnak MFA-kiszolgáló az új üzembe helyezésekhez. Új felhasználókat szeretné a felhasználóktól többtényezős hitelesítést kell használnia a felhőalapú Azure multi-factor Authentication. Meglévő ügyfeleink, akik aktiválták az MFA-kiszolgáló előtt július 1 lesz a legújabb verzió, a jövőbeli frissítések letöltéséhez és a szokásos módon aktiváló hitelesítő adatok előállítása.

> [!NOTE]
> Az MFA-kiszolgáló csak a PAP (Password Authentication Protocol) és az MSCHAPv2 (Microsoft Challenge-Handshake Authentication Protocol) RADIUS-protokollt támogatja, ha RADIUS-kiszolgálóként működik.  Egyéb protokollok, például az EAP (Extensible Authentication Protocol), akkor használhatók, ha az MFA-kiszolgáló egy másik olyan RADIUS-kiszolgáló RADIUS-proxyjaként működik, amely támogatja az adott protokollt.
>
> Ebben a konfigurációban az egyirányú SMS és OATH tokenek nem működnek, mivel az MFA-kiszolgáló nem tud sikeres RADIUS kérdés-választ kezdeményezni más protokollok használatával.

![Az MFA-kiszolgáló RADIUS-hitelesítés](./media/howto-mfaserver-dir-radius/radius.png)

## <a name="add-a-radius-client"></a>RADIUS-ügyfél hozzáadása

A RADIUS-hitelesítés konfigurálásához telepítse az Azure Multi-Factor Authentication-kiszolgálót egy Windows-kiszolgálóra. Ha Active Directory-környezetet használ, a kiszolgálót a hálózaton belüli tartományhoz kell csatlakoztatni. Az Azure Multi-Factor Authentication-kiszolgáló konfigurálásához kövesse az alábbi eljárást:

1. Az Azure Multi-Factor Authentication-kiszolgálón kattintson a bal oldali menüben lévő RADIUS-hitelesítés ikonra.
2. Jelölje be a **RADIUS-hitelesítés engedélyezése** jelölőnégyzetet.
3. Az Ügyfelek lapon módosítsa a hitelesítési és a nyilvántartási portokat, ha az Azure MFA RADIUS szolgáltatásnak nem szabványos portokon kell figyelnie a RADIUS-kéréseket.
4. Kattintson a **Hozzáadás**lehetőségre.
5. Adja meg az Azure Multi-Factor Authentication-kiszolgálón hitelesítendő készülék/kiszolgáló IP-címét, egy alkalmazásnevet (nem kötelező) és egy közös titkos kulcsot.

   Az alkalmazásnév megjelenik a jelentésekben, illetve megjelenhet az SMS-es vagy mobilalkalmazásos hitelesítési üzenetekben.

   A közös titkos kulcsnak azonosnak kell lennie az Azure Multi-Factor Authentication-kiszolgálón és a készüléken/kiszolgálón.

6. Jelölje be a **Multi-Factor Authentication felhasználói egyeztetés megkövetelése** jelölőnégyzetet, ha az összes felhasználót importálta a kiszolgálóra, és többtényezős hitelesítést alkalmaz rajtuk. Ha jelentős számú felhasználó még nincs importálva a kiszolgálóra vagy mentesül a kétlépéses ellenőrzés alól, ne jelölje be a jelölőnégyzetet.
7. Jelölje be a **Tartalék OATH token engedélyezése** jelölőnégyzetet, ha mobil hitelesítő alkalmazásokból származó OATH PIN-kódokat szeretné biztonsági mentési módszerként használni.
8. Kattintson az **OK** gombra.

Szükség esetén a 4–8. lépés megismétlésével további RADIUS-ügyfeleket adhat hozzá.

## <a name="configure-your-radius-client"></a>RADIUS-ügyfél konfigurálása

1. Kattintson a **Cél** fülre.
   * Ha az Azure MFA-kiszolgáló Active Directory-környezetet egy tartományhoz csatlakoztatott kiszolgálón van telepítve, válassza ki a **Windows-tartomány**.
   * Ha a felhasználókat egy LDAP-címtár alapján kell hitelesíteni, válassza az **LDAP-kötést**.
      Válassza a Címtár-integráció ikont, és szerkessze úgy az LDAP-konfigurációt a Beállítások lapon, hogy a kiszolgáló kötést hozhasson létre a címtárával. Az LDAP konfigurálásával kapcsolatos utasításokat az [LDAP-proxy konfigurációs útmutatójában](howto-mfaserver-dir-ldap.md) talál.
   * Ha a felhasználók egy másik RADIUS-kiszolgálót kell hitelesíteni, válassza ki a **RADIUS-kiszolgálók**.
1. Kattintson a **Hozzáadás** gombra, és konfigurálja azt a kiszolgálót, amelynek a kiszolgáló proxykapcsolaton keresztül átadja a RADIUS-kéréseket.
1. A RADIUS-kiszolgáló hozzáadása párbeszédpanelen adja meg a RADIUS-kiszolgáló IP-címét és egy közös titkos kulcsot.

   A közös titkos kulcsnak azonosnak kell lennie az Azure Multi-Factor Authentication- és a RADIUS-kiszolgálón. Módosítsa a hitelesítési portot és a nyilvántartási portot, ha a RADIUS-kiszolgáló eltérő portokat használ.

1. Kattintson az **OK** gombra.
1. Adja hozzá az Azure MFA-kiszolgálót egy RADIUS-ügyfélként a másik RADIUS-kiszolgálón, hogy fel tudja dolgozni az Azure MFA-kiszolgálóról érkező hozzáférési kéréseket. Ugyanazt a közös titkos kulcsot használja, amelyet az Azure Multi-Factor Authentication-kiszolgálón konfigurált.

További RADIUS-kiszolgálók hozzáadásához ismételje meg ezeket a lépéseket. A **Feljebb** és **Lejjebb** gombokkal konfigurálhatja a sorrendet, amely alapján az Azure MFA-kiszolgáló meghívja a kiszolgálókat.

Sikeresen konfigurálta az Azure Multi-Factor Authentication-kiszolgálót. A kiszolgáló mostantól fogadja a konfigurált ügyfelek RADIUS hozzáférési kéréseit a beállított portokon.

## <a name="radius-client-configuration"></a>A RADIUS-ügyfél konfigurálása

A RADIUS-ügyfél konfigurálásához kövesse az alábbi útmutatást:

* Konfigurálja a RADIUS-on keresztül hitelesítendő berendezést/kiszolgálót azon Azure Multi-Factor Authentication-kiszolgáló IP-címével, amely a RADIUS-kiszolgálóként fog működni.
* Használja a korábban konfigurált közös titkos kulcsot.
* A RADIUS-időtúllépést állítsa 30–60 másodpercre, hogy elegendő idő álljon rendelkezésre a felhasználó hitelesítő adatainak ellenőrzésére, a kétlépéses hitelesítés végrehajtására, a válasz fogadására és a RADIUS hozzáférési kérés megválaszolására.

## <a name="next-steps"></a>További lépések

Ismerkedjen meg a [RADIUS-hitelesítés integrálásával](howto-mfa-nps-extension.md), ha rendelkezik Azure Multi-Factor Authenticationnel a felhőben. 

---
title: Honosítási karakterlánc-azonosítók – Azure Active Directory B2C | Microsoft Docs
description: Határozza meg az API. signuporsignin azonosítójú tartalom-definíció azonosítóit a Azure Active Directory B2C egyéni házirendjében.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: da1390de4e2eb0624032dc490416e7b6e5d61baa
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "67846837"
---
# <a name="localization-string-ids"></a>Honosítási sztringazonosítók

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A **honosítási** elem lehetővé teszi, hogy a felhasználói útvonalakhoz tartozó szabályzatban több területi beállítást vagy nyelvet támogasson. Ez a cikk a szabályzatában használható honosítási azonosítók listáját tartalmazza. A felhasználói felület honosításának megismeréséhez lásd: [honosítás](localization.md).

## <a name="sign-up-or-sign-in-page-elements"></a>Regisztrációs vagy bejelentkezési oldal elemei

A következő azonosítók szerepelnek egy AZONOSÍTÓval `api.signuporsignin`rendelkező tartalom-definícióhoz.

| id | Alapértelmezett érték |
| -- | ------------- |
| **local_intro_email** | Jelentkezzen be meglévő fiókjával |
| **logonIdentifier_email** | E-mail cím |
| **requiredField_email** | Adja meg e-mail-címét |
| **invalid_email** | Adjon meg egy érvényes e-mail-címet |
| **email_pattern** | ^[a-zA-Z0-9.!#$%&’' *+/=?^_\`{\|}~-]+@[a-zA-Z0-9-]+(?:\\.[a-zA-Z0-9-]+)* $ |
| **local_intro_username** | Jelentkezzen be a felhasználónevével |
| **logonIdentifier_username** | Felhasználónév |
| **requiredField_username** | Adja meg a felhasználónevét |
| **jelszó** | Windows 10 |
| **requiredField_password** | Írja be a jelszót |
| **invalid_password** | A beírt jelszó nem a várt formátumú. |
| **forgotpassword_link** | Elfelejtette jelszavát? |
| **createaccount_intro** | Még nincs fiókja? |
| **createaccount_link** | Regisztráció |
| **divider_title** | VAGY |
| **cancel_message** | A felhasználó elfelejtette a jelszavát |
| **button_signin** | Bejelentkezés |
| **social_intro** | Bejelentkezés a közösségi fiókjával |
  **remember_me** |Bejelentkezve szeretnék maradni|
| **unknown_error** | Hiba történt a bejelentkezés során. Próbálja újra később. |

A következő példa a felhasználói felület egyes elemeinek használatát mutatja be a regisztrációs vagy bejelentkezési oldalon:

![Regisztrációs vagy bejelentkezési oldal UX-elemek](./media/localization-string-ids/localization-susi.png)

Az Identity Providers AZONOSÍTÓját a felhasználói út **ClaimsExchange** eleme konfigurálja. Az identitás-szolgáltató címének honosítása érdekében a **ElementType** a értékre van `ClaimsProvider`állítva, míg a **elemtípusú** a (z `ClaimsExchange`) azonosítójának értéke.

```XML
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="MicrosoftExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccount" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Az alábbi példa a Facebook-identitás szolgáltatóját az Arab nyelvre honosítja:

```XML
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">فيس بوك</LocalizedString>
```

## <a name="sign-up-or-sign-in-error-messages"></a>Regisztrációs vagy bejelentkezési hibaüzenetek

| id | Alapértelmezett érték |
| -- | ------------- |
| **UserMessageIfInvalidPassword** | A jelszó helytelen. |
| **UserMessageIfClaimsPrincipalDoesNotExist** | Nem találjuk a fiókját. |
| **UserMessageIfOldPasswordUsed** | Valószínűleg régi jelszót írt be. |
| **DefaultMessage** | Érvénytelen felhasználónév vagy jelszó. |
| **UserMessageIfUserAccountDisabled** | A fiók zárolva van. A zárolás feloldásához forduljon a támogatási szolgálathoz, majd próbálkozzon újra. |
| **UserMessageIfUserAccountLocked** | A fiók átmenetileg zárolva van, hogy megakadályozza a jogosulatlan használatot. Próbálkozzon újra később. |
| **AADRequestsThrottled** | Jelenleg túl sok kérés van. Várjon egy ideig, és próbálkozzon újra. |

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>Regisztráció és saját maga által érvényesített lapok felhasználói felületi elemei

A következő a tartalom- `api.localaccountsignup` definíció azonosítóját `api.selfasserted`, illetve a-től kezdődően megjelenő tartalom-definíciót, `api.selfasserted.profileupdate` például `api.localaccountpasswordreset`és.

| id | Alapértelmezett érték |
| -- | ------------- |
| **ver_sent** | Az ellenőrzőkód elküldve ide: |
| **ver_but_default** | Alapértelmezett |
| **cancel_message** | A felhasználó megszakította az önérvényesített információk megadását |
| **preloader_alt** | Kis türelmet... |
| **ver_but_send** | Ellenőrzőkód küldése |
| **alert_yes** | Igen |
| **error_fieldIncorrect** | Egy vagy több mező kitöltése helytelen. Ellenőrizze a bejegyzéseket, és próbálkozzon újra. |
| **év** | Év |
| **verifying_blurb** | Kis türelmet, amíg a rendszer feldolgozza az adatokat. |
| **button_cancel** | Mégse |
| **ver_fail_no_retry** | Túl sok helytelen kísérlet történt. Próbálja újra később. |
| **hónap** | hónap |
| **ver_success_msg** | Az E-mail cím ellenőrizve. Most már folytathatja. |
| **months** | január, február, március, április, május, június, július, augusztus, szeptember, október, november, december |
| **ver_fail_server** | Nem sikerül ellenőrizni az e-mail-címét. Adjon meg egy érvényes e-mail-címet, és próbálkozzon újra. |
| **error_requiredFieldMissing** | Hiányzik egy kötelező mező. Töltse ki az összes kötelező mezőt, és próbálkozzon újra. |
| **initial_intro** | Adja meg a következő adatokat. |
| **ver_but_resend** | Új kódot kérek |
| **button_continue** | Hozzon létre |
| **error_passwordEntryMismatch** | A jelszó-beviteli mezők nem egyeznek. Ugyanazt a jelszót adja meg mindkét mezőben, majd próbálkozzon újra. |
| **ver_incorrect_format** | Helytelen formátumú. |
| **ver_but_edit** | E-mail módosítása |
| **ver_but_verify** | Kód ellenőrzése |
| **alert_no** | Nem |
| **ver_info_msg** | Az ellenőrző kód el lett küldve a Beérkezett üzenetek mappájába. Másolja az alábbi beviteli mezőbe. |
| **nap** | nap |
| **ver_fail_throttled** | Túl sok kérelem érkezett az e-mail-cím ellenőrzéséhez. Várjon egy kicsit, és próbálkozzon újra. |
| **helplink_text** | Mi ez? |
| **ver_fail_retry** | A kód helytelen. Kérjük, próbálkozzon újból. |
| **alert_title** | A részletek megadásának megszakítása |
| **required_field** | Az adat megadása kötelező. |
| **alert_message** | Biztosan megszakítja az adatai megadását? |
| **ver_intro_msg** | Ellenőrzés szükséges. Kattintson a Küldés gombra. |
| **ver_input** | Ellenőrzőkód |

## <a name="sign-up-and-self-asserted-pages-error-messages"></a>Bejelentkezési és saját maga által vezérelt lapok hibaüzenetei

| id | Alapértelmezett érték |
| -- | ------------- |
| **UserMessageIfClaimsPrincipalAlreadyExists** | Már létezik a megadott AZONOSÍTÓJÚ felhasználó. Válasszon másikat. |
| **UserMessageIfClaimNotVerified** | A jogcím nincs ellenőrizve:{0} |
| **UserMessageIfIncorrectPattern** | Helytelen minta a következőhöz:{0} |
| **UserMessageIfMissingRequiredElement** | Hiányzó kötelező elem:{0} |
| **UserMessageIfValidationError** | Hiba történt az érvényesítés során:{0} |
| **UserMessageIfInvalidInput** | {0}Érvénytelen bemenettel rendelkezik. |
| **ServiceThrottled** | Jelenleg túl sok kérés van. Várjon egy ideig, és próbálkozzon újra. |

A következő példa a felhasználói felület egyes elemeinek használatát mutatja be a regisztrációs oldalon:

![Regisztrációs oldal a felhasználói felületi elemek neveivel megcímkézve](./media/localization-string-ids/localization-sign-up.png)

Az alábbi példa a felhasználói felület egyes elemeinek használatát mutatja be a regisztrációs oldalon, miután a felhasználó az ellenőrző kód küldése gombra kattint:

![Regisztrációs oldal e-mail ellenőrzése UX-elemek](./media/localization-string-ids/localization-email-verification.png)


## <a name="phone-factor-authentication-page-user-interface-elements"></a>A telefonos faktor hitelesítés lapja felhasználói felület elemei

Az alábbi AZONOSÍTÓk `api.phonefactor`a tartalom-definíció azonosítóját használják.

| id | Alapértelmezett érték |
| -- | ------------- |
| **button_verify** | Hívjon fel |
| **country_code_label** | Országkód: |
| **cancel_message** | A felhasználó megszakította a többtényezős hitelesítést |
| **text_button_send_second_code** | új kód küldése |
| **code_pattern** | \\d{6} |
| **intro_mixed** | A következő számú rekordot vesszük igénybe. A hitelesítéshez SMS-ben vagy telefonon keresztül küldhetünk egy kódot. |
| **intro_mixed_p** | A következő számok szerepelnek a rekordban. Válassza ki azt a számot, amely telefonon vagy SMS-ben küldünk egy kódot a hitelesítéshez. |
| **button_verify_code** | Kód ellenőrzése |
| **requiredField_code** | Adja meg a kapott ellenőrzőkódot |
| **invalid_code** | Adja meg az általunk küldött 6 jegyű kódot |
| **button_cancel** | Mégse |
| **local_number_input_placeholder_text** | Telefonszám |
| **button_retry** | Retry |
| **alternative_text** | Nincs nálam a telefonom |
| **intro_phone_p** | A következő számok szerepelnek a rekordban. Válassza ki azt a számot, amelyet telefonon használhat a hitelesítéshez. |
| **intro_phone** | A következő számú rekordot vesszük igénybe. A rendszer telefonon hitelesíti Önt. |
| **enter_code_text_intro** | Adja meg az ellenőrzőkódot alább, vagy  |
| **intro_entry_phone** | Adjon megy alább egy telefonszámot, amin felhívhatjuk a hitelesítéshez. |
| **intro_entry_sms** | Adjon meg alább egy telefonszámot, amelyre SMS-ben kódot küldhetünk a hitelesítéshez. |
| **button_send_code** | Kódot kérek |
| **invalid_number** | Érvényes telefonszámot adjon meg |
| **intro_sms** | A következő számú rekordot vesszük igénybe. SMS-ben küldünk egy kódot a hitelesítéshez. |
| **intro_entry_mixed** | Adjon meg alább egy telefonszámot, amelyre küldhetünk egy kódot SMS-ben, vagy amelyen felhívhatjuk a hitelesítéshez. |
| **number_pattern** | ^\\+(?:[0-9][\\x20-]?){6,14}[0-9]$ |
| **intro_sms_p** |A következő számok szerepelnek a rekordban. Válassza ki azt a számot, amelynek használatával SMS-ben küldhetünk egy kódot a hitelesítéshez. |
| **requiredField_countryCode** | Válassza ki az országhívószámot |
| **requiredField_number** | Adja meg a telefonszámát |
| **country_code_input_placeholder_text** |Ország vagy régió |
| **number_label** | Telefonszám |
| **error_tryagain** | A megadott telefonszám foglalt vagy nem érhető el. Ellenőrizze a számot, és próbálkozzon újra. |
| **error_incorrect_code** | A beírt ellenőrző kód nem felel meg a rekordoknak. Próbálkozzon újra, vagy kérjen új kódot. |
| **countryList** | {\"Default\":ország/\"régió,AF\":\"Afganisztán,\"AX:Åland\"\"\"\"\" Szigetek\",\"Al:\"Albánia\",DZ\":Algéria,as\":\"\"\"\"\"\" Amerikai Szamoa\",\"ad\":Andorra\",\"Ao:\"Angola,AI\":\"\"\"\" \"Anguilla,\"AQ\":Antarktisz\",AG: Antigua ésBarbuda\",\"\"\"\"\"\"AR\":Argentína\",\"am:\"Örményország, AW:\"Aruba\"\"\"\"\" \",Au\":\"Ausztrália,\"at:Ausztria,\"\"\"\"\"\" AZ\":\"Azerbajdzsán,\"BS\":Bahama-\"szigetek,BH:\" \"\"\"\" Bahrein\",\"BD:\"Banglades\",bb\":Barbados,\"\"\"\"\" \":Fehéroroszország,\"be\" : Belgium, BZ:\"\"\"\"\"\"\" Belize\",\"BJ:\"Benin\",BM\":Bermuda,\"\"\"\"\"BT\":Bhután\",\"Bo:\"Bolívia,BQ:\"\"\"\"\"\" Bonaire\",\"ba:Bosznia\"-Hercegovina\",\"BW:Botswana\"\"\"<span class="notransla class=""></span class="notransla> Külső szigetek\",\"VI\":EgyesültÁllamok\" Virgin-\"szigetek\",\"UG:\"Uganda,\"ua:\"Ukrajna,AE\":\"\"\"\"\" Egyesült Arab Emírségek\",\"GB\":EgyesültKirályság\",USA:\"Egyesült Államok,\"uy\"\"\"\" \":Uruguay\",\"Uz:\"Üzbegisztán, Vu:Vanuatu\",\"\"\"\"\"\" \"VA:\"Vatikánváros\",ve\":Venezuela,vn\": Vietnam\"\"\"\"\"\" \",WF\":Wallisés\"Futuna,ti:\"Jemen,\"ZM\"\"\"\"\"\":Zambia,\"ZW:Zimbabwe\"}\"\"\"\" |
| **error_448** | A megadott telefonszám nem érhető el. |
| **error_449** | A felhasználó túllépte a megengedett újrapróbálkozások számát. |
| **verification_code_input_placeholder_text** | Ellenőrzőkód |

Az alábbi példa a felhasználói felület egyes elemeinek használatát mutatja be az MFA-regisztráció lapon:

![Regisztrációs oldal e-mail ellenőrzése UX-elemek](./media/localization-string-ids/localization-mfa1.png)

Az alábbi példa a felhasználói felület egyes elemeinek használatát mutatja be az MFA-érvényesítési lapon:

![Regisztrációs oldal e-mail ellenőrzése UX-elemek](./media/localization-string-ids/localization-mfa2.png)








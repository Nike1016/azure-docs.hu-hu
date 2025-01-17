---
title: Azure multi-Factor Authentication – felhasználói állapotok – Azure Active Directory
description: Ismerje meg az Azure multi-Factor Authentication felhasználói állapotait.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc83f59d51e1552d9d68089d0b9fa23d924f491c
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69561293"
---
# <a name="how-to-require-two-step-verification-for-a-user"></a>Egy felhasználó kétlépéses ellenőrzésének megkövetelése

A kétlépéses ellenőrzés megkövetelésének két módszere közül választhat: mindkettőt globális rendszergazdai fiókkal kell végrehajtania. Az első lehetőség az, hogy minden felhasználó számára engedélyezze az Azure multi-Factor Authentication (MFA) használatát. Ha a felhasználók külön engedélyezve vannak, minden bejelentkezéskor kétlépéses ellenőrzést hajtanak végre (bizonyos kivételekkel, például amikor bejelentkeznek a megbízható IP-címekről, vagy ha a megjegyezett _eszközök_ funkció be van kapcsolva). A második lehetőség egy olyan feltételes hozzáférési szabályzat beállítása, amely bizonyos körülmények között kétlépéses ellenőrzést igényel.

> [!TIP]
> Az Azure multi-Factor Authentication feltételes hozzáférési házirendek használatával történő engedélyezése az ajánlott módszer. A felhasználói állapotok módosítása már nem ajánlott, kivéve, ha a licencek nem tartalmazzák a feltételes hozzáférést, mert a felhasználóknak minden bejelentkezéskor a többtényezős hitelesítést kell végezniük.

## <a name="choose-how-to-enable"></a>Az engedélyezés módjának kiválasztása

**Engedélyezve a felhasználói állapot módosításával** – ez a hagyományos módszer a kétlépéses ellenőrzés megköveteléséhez, és a jelen cikk tárgyalja. A felhőben és az Azure MFA-kiszolgálón egyaránt működik az Azure MFA-val. Ennek a módszernek a használatával a felhasználóknak kétlépéses ellenőrzést kell végezniük **minden alkalommal, amikor** bejelentkeznek, és felülbírálják a feltételes hozzáférési szabályzatokat.

A feltételes hozzáférési házirend által engedélyezett – ez a legrugalmasabb módszer a felhasználók kétlépéses ellenőrzésének engedélyezéséhez. A feltételes hozzáférési szabályzat használatának engedélyezése csak az Azure MFA-ban működik a felhőben, és az Azure AD prémium funkciója. Az ezzel a módszerrel kapcsolatos további információkért tekintse meg a [felhőalapú Azure multi-Factor Authentication üzembe helyezése című](howto-mfa-getstarted.md)témakört.

Azure AD Identity Protection által engedélyezett – ez a módszer a Azure AD Identity Protection kockázati házirendet használja a kétlépéses ellenőrzés megkövetelésére, csak a bejelentkezési kockázat alapján minden felhőalapú alkalmazás esetében. Ehhez a módszerhez Azure Active Directory P2 licencelés szükséges. A módszerről további információ található [Azure Active Directory Identity Protection](../identity-protection/howto-sign-in-risk-policy.md)

> [!Note]
> A licencekkel és a díjszabással kapcsolatos további információkért tekintse meg](https://azure.microsoft.com/pricing/details/active-directory/
) az Azure ad és a [ [multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) díjszabási oldalát.

## <a name="enable-azure-mfa-by-changing-user-state"></a>Az Azure MFA engedélyezése a felhasználói állapot módosításával

Az Azure multi-Factor Authentication felhasználói fiókjai a következő három különböző állapottal rendelkeznek:

| Állapot | Leírás | Érintett nem böngészőbeli alkalmazások | Érintett böngészőalapú alkalmazások | A modern hitelesítés érintett |
|:---:|:---:|:---:|:--:|:--:|
| Letiltva |Az Azure MFA-ban nem regisztrált új felhasználó alapértelmezett állapota. |Nem |Nem |Nem |
| Enabled |A felhasználó regisztrálva lett az Azure MFA-ban, de nincs regisztrálva. A következő bejelentkezés alkalmával a rendszer felszólítja a regisztrálásra. |Nem.  Továbbra is működnek, amíg a regisztrációs folyamat be nem fejeződik. | Igen. A munkamenet lejárata után az Azure MFA-regisztrációra van szükség.| Igen. A hozzáférési jogkivonat lejárta után az Azure MFA-regisztrációra van szükség. |
| Érvényben van |A felhasználó regisztrálva lett, és befejezte az Azure MFA regisztrációs folyamatát. |Igen. Az alkalmazásokhoz alkalmazások jelszava szükséges. |Igen. Bejelentkezéskor az Azure MFA szükséges. | Igen. Bejelentkezéskor az Azure MFA szükséges. |

A felhasználó állapota azt jelzi, hogy egy rendszergazda regisztrálta-e őket az Azure MFA-ban, és hogy elvégezték-e a regisztrációs folyamatot.

Az összes felhasználó *le van tiltva*. Amikor felhasználókat regisztrál az Azure MFA-ban, az állapotuk *engedélyezve*értékre vált. Ha az engedélyezett felhasználók bejelentkeznek, és elvégzik a regisztrációs folyamatot, az állapotuk kényszerítve értékűre vált.  

### <a name="view-the-status-for-a-user"></a>Felhasználó állapotának megtekintése

A következő lépésekkel érheti el az oldalt, amelyen megtekintheti és kezelheti a felhasználói állapotokat:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) felületére rendszergazdaként.
2. Ugrás a **Azure Active Directory** > **felhasználók és csoportok** > **minden felhasználó**számára.
3. Válassza a többtényezős **hitelesítés**lehetőséget.
   ![Multi-Factor Authentication kiválasztása](./media/howto-mfa-userstates/selectmfa.png)
4. Megnyílik egy új oldal, amely megjeleníti a felhasználói állapotokat.
   ![a multi-Factor Authentication felhasználói állapota – képernyőfelvétel](./media/howto-mfa-userstates/userstate1.png)

### <a name="change-the-status-for-a-user"></a>Felhasználó állapotának módosítása

1. Az előző lépésekkel elérheti az Azure multi-Factor Authentication **felhasználói** lapját.
2. Keresse meg azt a felhasználót, aki számára engedélyezni szeretné az Azure MFA-t. Előfordulhat, hogy felül kell változtatnia a nézetet.
   ![Válassza ki a felhasználót az állapot módosításához a felhasználók lapon](./media/howto-mfa-userstates/enable1.png)
3. Jelölje be a neve melletti jelölőnégyzetet.
4. A jobb oldalon a **gyors lépések**területen válassza az **Engedélyezés** vagy a **Letiltás**lehetőséget.
   ![A gyors lépések menüben az engedélyezés elemre kattintva engedélyezheti a kiválasztott felhasználót.](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > Az *engedélyezett* felhasználók automatikusan *érvénybe* lettek állítva, amikor regisztrálják magukat az Azure MFA-ban. Ne módosítsa manuálisan a felhasználói állapotot *kényszerített*értékre.

5. Erősítse meg a kijelölést a megnyíló előugró ablakban.

A felhasználók engedélyezése után értesítse őket e-mailben. Mondja el nekik, hogy a következő bejelentkezés alkalmával a rendszer kérni fogja a regisztrálást. Továbbá, ha a szervezete nem böngészőbeli alkalmazásokat használ, amelyek nem támogatják a modern hitelesítést, létre kell hozniuk az alkalmazáshoz tartozó jelszavakat. Emellett az [Azure MFA végfelhasználói útmutatójának](../user-help/multi-factor-authentication-end-user.md) hivatkozását is használhatja, amely megkönnyíti az első lépéseket.

### <a name="use-powershell"></a>A PowerShell használata

Ha módosítani szeretné a felhasználói állapotot az [Azure ad PowerShell](/powershell/azure/overview)használatával `$st.State`, módosítsa a következőt:. Három lehetséges állapot létezik:

* Enabled
* Kényszerítve
* Letiltva  

Ne helyezze át a felhasználókat közvetlenül a kényszerített állapotba. Ha így tesz, a nem böngészőalapú alkalmazások nem működnek, mert a felhasználó nem ment át az Azure MFA-regisztrációval, és nem kapott meg egy [alkalmazás jelszavát](howto-mfa-mfasettings.md#app-passwords).

Először telepítse a modult a használatával:

   ```PowerShell
   Install-Module MSOnline
   ```

> [!TIP]
> Ne felejtsen el először kapcsolódni a **MsolService**

Ebben a példában a PowerShell-parancsfájl lehetővé teszi az MFA használatát az egyes felhasználók számára:

   ```PowerShell
   Import-Module MSOnline
   $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
   $st.RelyingParty = "*"
   $st.State = "Enabled"
   $sta = @($st)
   Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
   ```

A PowerShell használata jó megoldás, ha a felhasználók tömeges engedélyezésére van szükség. Például a következő szkript hurkokat mutat be a felhasználók listáján, és engedélyezi az MFA-t a fiókjaik számára:

   ```PowerShell
   $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
   foreach ($user in $users)
   {
       $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
       $st.RelyingParty = "*"
       $st.State = "Enabled"
       $sta = @($st)
       Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
   }
   ```

Az MFA letiltásához használja a következő parancsfájlt:

   ```PowerShell
   Get-MsolUser -UserPrincipalName user@domain.com | Set-MsolUser -StrongAuthenticationMethods @()
   ```

a következőket is lerövidítheti:

   ```PowerShell
   Set-MsolUser -UserPrincipalName user@domain.com -StrongAuthenticationRequirements @()
   ```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Felhasználók konvertálása felhasználónkénti MFA-ből feltételes hozzáférésen alapuló MFA-ra

A következő PowerShell segítséget nyújt a feltételes hozzáférésen alapuló Azure multi-Factor Authentication-hitelesítéshez.

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Wrapper to disable MFA with the option to keep the MFA methods (to avoid having to proof-up again later)
function Disable-Mfa {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipeline=$True)]
        $User,
        [switch] $KeepMethods
    )

    Process {

        Write-Verbose ("Disabling MFA for user '{0}'" -f $User.UserPrincipalName)
        $User | Set-MfaState -State Disabled

        if ($KeepMethods) {
            # Restore the MFA methods which got cleared when disabling MFA
            Set-MsolUser -ObjectId $User.ObjectId `
                         -StrongAuthenticationMethods $User.StrongAuthenticationMethods
        }
    }
}

# Disable MFA for all users, keeping their MFA methods intact
Get-MsolUser -All | Disable-MFA -KeepMethods
```

## <a name="next-steps"></a>További lépések

* Miért volt a felhasználó, vagy a rendszer nem kéri az MFA elvégzésére? Tekintse meg az Azure [ad-bejelentkezésekről szóló jelentést az Azure multi-Factor Authentication dokumentumban](howto-mfa-reporting.md#azure-ad-sign-ins-report)található jelentésekben.
* További beállítások, például a megbízható IP-címek, az egyéni hangüzenetek és a csalási riasztások konfigurálásához tekintse meg az [Azure multi-Factor Authentication beállításainak konfigurálása](howto-mfa-mfasettings.md) című cikket.
* Az Azure multi-Factor Authentication felhasználói beállításainak kezelésével kapcsolatos információk a következő cikkben találhatók: [felhasználói beállítások kezelése az Azure multi-Factor Authentication használatával a felhőben](howto-mfa-userdevicesettings.md)

---
title: Microsoft Graph a Azure Active Directory Identity Protectionhoz | Microsoft Docs
description: Megtudhatja, hogyan kérdezheti le Microsoft Graph a kockázati események és a kapcsolódó információk Azure Active Directoryból való lekérdezéséhez.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1640511c2f97865f5026f9f977ed0e4a9c03e338
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774376"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>A Azure Active Directory Identity Protection és a Microsoft Graph első lépései

Microsoft Graph a Microsoft Unified API-végpontja és a [Azure Active Directory Identity Protection](../active-directory-identityprotection.md) API-k otthona. A kockázatos felhasználókról és a bejelentkezésekről négy API található. Az első API ( **riskDetection**) lehetővé teszi Microsoft Graph lekérdezését a felhasználók és a bejelentkezéshez kapcsolódó kockázati észlelések, valamint az észleléssel kapcsolatos kapcsolódó információk listájának lekéréséhez. A második API, a **riskyUsers**lehetővé teszi, hogy lekérdezze Microsoft Graph a kockázatként észlelt felhasználói identitások védelmére vonatkozó információkat. A harmadik API, a **bejelentkezési**lehetővé teszi az Azure ad-bejelentkezésekre vonatkozó információk Microsoft Graph lekérdezését a kockázati állapottal, a részletekkel és a szinttel kapcsolatos konkrét tulajdonságokkal. A negyedik API ( **identityRiskEvents**) lehetővé teszi Microsoft Graph lekérdezését a [kockázati események](../reports-monitoring/concept-risk-events.md) és a kapcsolódó információk listájához. Ebből a cikkből megtudhatja, hogyan csatlakozhat a Microsoft Graphhoz, és hogyan kérdezheti le ezeket az API-kat. A részletes bevezetést, a teljes dokumentációt és a Graph Explorerrel való hozzáférést a [Microsoft Graph webhelyén](https://graph.microsoft.io/) vagy az API-k konkrét dokumentációjában találja:

* [riskDetection API](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta)
* [riskyUsers API](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta)
* [Bejelentkezési API](https://docs.microsoft.com/graph/api/resources/signin?view=graph-rest-beta)
* [identityRiskEvents API](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta)

## <a name="connect-to-microsoft-graph"></a>Kapcsolódás a Microsoft graphhoz

Az Identity Protection-adatok elérésének négy lépése van Microsoft Graph:

1. A tartománynév beolvasása.
2. Hozzon létre egy új alkalmazás-regisztrációt. 
3. Használja ezt a titkot és néhány további információt a Microsoft Graph hitelesítéséhez, ahol hitelesítési jogkivonatot kap. 
4. Ezzel a jogkivonattal kérheti az API-végpontra irányuló kéréseket, és visszanyerheti az Identity Protection adatait.

A Kezdés előtt a következőkre lesz szüksége:

* Rendszergazdai jogosultságok az alkalmazás létrehozásához az Azure AD-ben
* A bérlő tartományának neve (például contoso.onmicrosoft.com)

## <a name="retrieve-your-domain-name"></a>Tartománynév lekérése 

1. [Jelentkezzen](https://portal.azure.com) be a Azure Portal rendszergazdaként. 
1. A bal oldali navigációs panelen kattintson a **Active Directory**elemre. 

   ![Alkalmazás létrehozása](./media/graph-get-started/41.png)

1. A **kezelés** szakaszban kattintson a **Tulajdonságok**elemre.

   ![Alkalmazás létrehozása](./media/graph-get-started/42.png)

1. Copy your domain name.

## <a name="create-a-new-app-registration"></a>Hozzon létre egy új alkalmazás regisztrálása

1. A **Active Directory** oldalon, a **kezelés** szakaszban kattintson az **Alkalmazásregisztrációk**elemre.

   ![Alkalmazás létrehozása](./media/graph-get-started/42.png)

1. A felső menüben kattintson az **új alkalmazás regisztrálása**elemre.

   ![Alkalmazás létrehozása](./media/graph-get-started/43.png)

1. A **Létrehozás** oldalon hajtsa végre a következő lépéseket:

   ![Alkalmazás létrehozása](./media/graph-get-started/44.png)

   1. A **név** szövegmezőbe írja be az alkalmazás nevét (például: AADIP kockázati esemény API-alkalmazása).

   1. **Írja be**a következőt: webalkalmazás **és/vagy webes API**.

   1. A **bejelentkezési URL** szövegmezőbe írja be a következőt `http://localhost`:.

   1. Kattintson a **Create** (Létrehozás) gombra.
1. A **Beállítások** oldal megnyitásához az alkalmazások listában kattintson az újonnan létrehozott alkalmazás-regisztráció elemre. 
1. Másolja ki az **alkalmazás azonosítóját**.

## <a name="grant-your-application-permission-to-use-the-api"></a>A API használatának engedélyezése az alkalmazás számára

1. A **Beállítások** lapon kattintson a **szükséges engedélyek**elemre.

   ![Alkalmazás létrehozása](./media/graph-get-started/15.png)

1. A **szükséges engedélyek** lapon, a felső eszköztáron kattintson a **Hozzáadás**gombra.

   ![Alkalmazás létrehozása](./media/graph-get-started/16.png)

1. Az **API-hozzáférés hozzáadása** lapon kattintson **az API kiválasztása**lehetőségre.

   ![Alkalmazás létrehozása](./media/graph-get-started/17.png)

1. Az **API kiválasztása** lapon válassza a **Microsoft Graph**lehetőséget, majd kattintson a **kiválasztás**gombra.

   ![Alkalmazás létrehozása](./media/graph-get-started/18.png)

1. Az **API-hozzáférés hozzáadása** lapon kattintson az **engedélyek kiválasztása**elemre.

   ![Alkalmazás létrehozása](./media/graph-get-started/19.png)

1. A **hozzáférés engedélyezése** lapon kattintson az **összes identitás kockázati információinak olvasása**elemre, majd a **kiválasztás**elemre.

   ![Alkalmazás létrehozása](./media/graph-get-started/20.png)

1. Az **API-hozzáférés hozzáadása** lapon kattintson a **kész**gombra.

   ![Alkalmazás létrehozása](./media/graph-get-started/21.png)

1. A **szükséges engedélyek** lapon kattintson az **engedélyek megadása**elemre, majd az **Igen**gombra.

   ![Alkalmazás létrehozása](./media/graph-get-started/22.png)

## <a name="get-an-access-key"></a>Hívóbetű lekérése

1. A **Beállítások** lapon kattintson a **kulcsok**elemre.

   ![Alkalmazás létrehozása](./media/graph-get-started/23.png)

1. A **kulcsok** oldalon hajtsa végre a következő lépéseket:

   ![Alkalmazás létrehozása](./media/graph-get-started/24.png)

   1. A **kulcs leírása** szövegmezőbe írja be a leírást (például *AADIP kockázati esemény*).
   1. Az **időtartam**beállításnál válassza **az 1 év**lehetőséget.
   1. Kattintson a **Save** (Mentés) gombra.
   1. Másolja a kulcs értékét, majd illessze be egy biztonságos helyre.   
   
   > [!NOTE]
   > Ha elveszíti ezt a kulcsot, térjen vissza ehhez a szakaszhoz, és hozzon létre egy új kulcsot. Titkos kulcs megtartása: bárki, aki hozzáfér az adataihoz.
   > 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-events-api"></a>Hitelesítés Microsoft Graph és az Identity Risk Events API lekérdezése

Ezen a ponton a következőket kell tennie:

- A bérlő tartományának neve
- Az ügyfél azonosítója 
- A kulcs 

A hitelesítéshez küldjön post-kérést `https://login.microsoft.com` a következő paraméterekkel a törzsben:

- grant_type: "**client_credentials**"
- erőforrás`https://graph.microsoft.com`
- client_id: \<az ügyfél azonosítója\>
- client_secret: \<a kulcs\>

Ha ez sikeres, a rendszer egy hitelesítési jogkivonatot ad vissza.  
Az API meghívásához hozzon létre egy fejlécet a következő paraméterrel:

```
`Authorization`=”<token_type> <access_token>"
```

A hitelesítés során megkeresheti a jogkivonat típusát és a hozzáférési jogkivonatot a visszaadott jogkivonatban.

Küldje el ezt a fejlécet kérelemként a következő API URL-címre:`https://graph.microsoft.com/beta/identityRiskEvents`

A válasz, ha a sikeres, az identitás kockázati eseményeinek és a hozzájuk kapcsolódó adatoknak a OData JSON formátumban való gyűjteménye, amely a megfelelő módon elemezhető és kezelhető.

Az alábbi mintakód a PowerShell használatával történő hitelesítéshez és az API meghívásához.  
Csak adja hozzá az ügyfél-azonosítót, a titkos kulcsot és a bérlői tartományt.

```PowerShell
    $ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
    $ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
    $tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

    $loginURL       = "https://login.microsoft.com"
    $resource       = "https://graph.microsoft.com"

    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    Write-Output $oauth

    if ($oauth.access_token -ne $null) {
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.microsoft.com/beta/identityRiskEvents"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 
```

## <a name="query-the-apis"></a>API-k lekérdezése

Ez a három API számos lehetőséget kínál a kockázatos felhasználók és a céges bejelentkezések adatainak lekérésére. Ezek az API-k és a kapcsolódó mintavételi kérelmek leggyakoribb használati esetei. Ezeket a lekérdezéseket a fenti mintakód vagy a [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)használatával futtathatja.

### <a name="get-all-of-the-offline-risk-detections-riskdetection-api"></a>Az offline kockázatok észlelésének beolvasása (riskDetection API)

Az Identity Protection bejelentkezési kockázati házirendjeivel olyan feltételeket alkalmazhat, amikor a rendszer valós időben észleli a kockázatokat. Mi a helyzet a felderített észlelésekkel kapcsolatban (vagy nem valós időben)? Annak megismeréséhez, hogy milyen észlelések történtek a kapcsolat nélküli üzemmódban, és így nem váltott ki a bejelentkezési kockázati házirendet, lekérdezheti a riskDetection API-t.

```
GET https://graph.microsoft.com/beta/riskDetections?$filter=detectionTimingType eq 'offline'
```

### <a name="get-the-high-risk-and-medium-risk-events-identityriskevents-api"></a>Magas kockázatú és közepes kockázatú események (identityRiskEvents API) beszerzése

Az olyan közepes és magas kockázatú események, amelyek az Identity Protection bejelentkezési vagy felhasználói kockázati házirendjeinek kiváltására képesek lehetnek. Mivel közepes vagy nagy valószínűséggel vannak, hogy a bejelentkezni próbáló felhasználó nem a jogos identitás tulajdonosa, szervizelését kell lennie az ilyen események prioritásának. 

```
GET https://graph.microsoft.com/beta/identityRiskEvents?`$filter=riskLevel eq 'high' or riskLevel eq 'medium'" 
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>Az összes olyan felhasználó beolvasása, akik sikeresen átadtak a kockázatos bejelentkezési szabályzat által aktivált MFA-kihívásokat (riskyUsers API)

Ha meg szeretné tudni, hogy a szervezete milyen hatással van az Identity Protection kockázatalapú házirendjeire, akkor lekérdezheti az összes olyan felhasználót, aki sikeresen átadta a kockázatos bejelentkezési szabályzat által aktivált MFA-kihívásokat. Ezek az információk segíthetnek megérteni, hogy mely felhasználóknál lehet hamisan észlelni a személyazonosság védelmét, és hogy a legitim felhasználók milyen műveleteket végezhetnek el, ha a mesterséges intelligencia kockázatot jelent.

```
GET https://graph.microsoft.com/beta/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

### <a name="get-all-the-risky-sign-ins-for-a-specific-user-signin-api"></a>Egy adott felhasználó összes kockázatos bejelentkezésének beolvasása (bejelentkezési API)

Ha úgy gondolja, hogy egy felhasználó biztonsága veszélybe került, az összes kockázatos bejelentkezés beolvasásával jobban megismerheti a kockázat állapotát. 

```
https://graph.microsoft.com/beta/identityRiskEvents?`$filter=userID eq '<userID>' and riskState eq 'atRisk'
```
## <a name="next-steps"></a>További lépések

Gratulálunk, most elkészítette az első hívását Microsoft Graph!  
Most már lekérdezheti az identitás kockázati eseményeit, és felhasználhatja az adatok megjelenítését.

Ha többet szeretne megtudni a Microsoft Graphről, és hogyan hozhat létre alkalmazásokat a Graph API használatával, [](https://docs.microsoft.com/graph/overview) tekintse meg a dokumentációt, és még sok más a [Microsoft Graph webhelyen](https://developer.microsoft.com/graph). 

A kapcsolódó információk a következő témakörben találhatók:

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
- [A Azure Active Directory Identity Protection által észlelt kockázati események típusai](../reports-monitoring/concept-risk-events.md)
- [Microsoft Graph](https://developer.microsoft.com/graph/)
- [A Microsoft Graph áttekintése](https://developer.microsoft.com/graph/docs)
- [Azure AD Identity Protection szolgáltatás gyökerének](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityprotection_root)

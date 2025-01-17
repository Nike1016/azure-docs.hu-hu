---
title: Szerepköralapú hozzáférés-vezérlés előzetes – Azure Event Hubs |} A Microsoft Docs
description: Ez a cikk a szerepköralapú hozzáférés-vezérlés információkat biztosít az Azure Event Hubs.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 05/21/2019
ms.author: shvija
ms.openlocfilehash: 117b7d4adb508628ee768bb9531d0bbc52f61121
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816056"
---
# <a name="active-directory-role-based-access-control-preview"></a>Aktív Directory Role-Based hozzáférés-vezérlés (előzetes verzió)

A Microsoft Azure-erőforrások és az Azure Active Directory (Azure AD-) alapú alkalmazások integrált hozzáférés-vezérlési felügyeletet biztosít. Az Azure ad-vel vagy kezelheti felhasználói fiókok és alkalmazások kifejezetten az Azure-alapú alkalmazások a, vagy meg is összevonható az Azure AD-céges szintű single-sign-on, amely Azure-erőforrások is kiterjedő a meglévő Active Directory-infrastruktúra és az Azure-ban üzemeltetett alkalmazások. Majd hozzárendelheti ezeket az Azure AD-felhasználói és identitások globális és a szolgáltatás-specifikus szerepkörökhöz annak érdekében, hogy az Azure-erőforrásokhoz való hozzáférést.

Azure Event hubs-névterek és az összes kapcsolódó erőforrást az Azure Portalon a felügyeleti és az Azure resource management API már védett használatával a *szerepköralapú hozzáférés-vezérlés* (RBAC) modellt. Futásidejű műveletek RBAC funkciója mostantól nyilvános előzetes verzióban érhető el. 

Azure AD RBAC használó alkalmazások nem kell kezelni a SAS-szabályok és a kulcsok vagy bármilyen más konkrét és az Event Hubs hozzáférési jogkivonatok. Az ügyfélalkalmazás kommunikál az Azure AD-hitelesítési környezetet létrehozni, és a egy hozzáférési jogkivonatot szerez az Event Hubs. A tartományi felhasználói fiókokat, amelyek interaktív bejelentkezést igényelnek, az alkalmazás soha nem kezeli a hitelesítő adatokat közvetlenül.

## <a name="event-hubs-roles-and-permissions"></a>Event Hubs szerepkörök és engedélyek
Az Azure a következő beépített RBAC szerepköröket biztosítja a Event Hubs névtérhez való hozzáférés engedélyezéséhez:

Az [Azure Event Hubs adattulajdonosi (előzetes)](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner-preview) szerepkör lehetővé teszi az adathozzáférést egy Event Hubs névtérhez és annak entitásához (várólisták, témakörök, előfizetések és szűrők)

>[!IMPORTANT]
> Korábban támogatott a felügyelt identitás hozzáadása a **tulajdonoshoz** vagy a **közreműködő** szerepkörhöz. Azonban a tulajdonosi és a **közreműködő** szerepkörre vonatkozó adathozzáférési jogosultságok már nem teljesülnek. Ha a **tulajdonos** vagy **közreműködő** szerepkört használja, váltson át az **Azure Event Hubs adattulajdonos (előzetes verzió)** szerepkör használatára.


## <a name="use-event-hubs-with-an-azure-ad-domain-user-account"></a>Event Hubs használata az Azure AD tartományi felhasználói fiók

A következő szakasz azt ismerteti, hogyan hozhat létre és futtathat egy mintaalkalmazást, amely kérni fogja az interaktív Azure AD-felhasználó bejelentkezésének, hogyan lehet hozzáférést biztosítani az Event Hubs, a felhasználói fiók és az identitásukat el az Event Hubs használatával. 

Ez az oktatóanyag egy egyszerű konzolos alkalmazást ismertet, [amely a githubon található kód](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/AzureEventHubsSDK)

### <a name="create-an-active-directory-user-account"></a>Az Active Directory felhasználói fiók létrehozása

Az első lépés nem kötelező. Minden Azure-előfizetés automatikusan megfeleltetni az Azure Active Directory-bérlő, és ha rendelkezik hozzáféréssel az Azure-előfizetéssel, a felhasználói fiók már regisztrálva van. Ez azt jelenti, hogy a fiókot használhatja. 

Ha továbbra is szeretne létrehozni ebben a forgatókönyvben egy külön fiókot [kövesse az alábbi lépéseket](../automation/automation-create-aduser-account.md). Fiókok létrehozása az Azure Active Directory-bérlőjéhez, amely nem lehet nagyobb a vállalati forgatókönyvek esetében a engedéllyel kell rendelkeznie.

### <a name="create-an-event-hubs-namespace"></a>Event Hubs-névtér létrehozása

Ezután [hozzon létre egy Event Hubs névteret](event-hubs-create.md). 

Miután létrejött a névteret, lépjen a **hozzáférés-vezérlés (IAM)** lapon a portálon, és kattintson a **szerepkör-hozzárendelés hozzáadása** az Azure AD felhasználói fiók hozzáadása a tulajdonosi szerepkör. Saját felhasználói fiókját használja, és létrehozta a névteret, ha Ön már a tulajdonosi szerepkör. Egy másik fiókot ad hozzá a szerepkört, keresse meg a webalkalmazás nevére a **engedélyek hozzáadása** panel **kiválasztása** mezőben, majd kattintson a bejegyzésre. Ezután kattintson a **Save** (Mentés) gombra. A felhasználói fiókot most már hozzáférhet az Event Hubs-névtér és az event hubs korábban hozott létre.
 
### <a name="register-the-application"></a>Az alkalmazás regisztrálása

A mintaalkalmazás futtatása előtt az Azure ad-ben regisztrálja, és hagyja jóvá a beleegyezést kérő üzenetet, amely lehetővé teszi az alkalmazás eléréséhez az Event Hubs nyújtsanak a nevében. 

Mivel a mintaalkalmazás egy konzolalkalmazást, kell regisztrálni egy natív alkalmazást és API-hozzáférés biztosítása a **hátralékának** a "szükséges engedélyek" csoporthoz. Natív alkalmazások is kell egy **átirányítási URI** az Azure ad-ben, amely; azonosítót az URI-t kell lennie egy hálózati cél. Használat `https://eventhubs.microsoft.com` ebben a példában, mivel már kód a minta az URI-ra.

A részletes regisztrációs lépéseket mutatjuk be [ebben az oktatóanyagban](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Kövesse a lépéseket a regisztráció egy **natív** alkalmazást, majd kövesse a frissítés utasításokat hozzáadása a **hátralékának** API-t a szükséges engedélyekkel. A lépéseket, jegyezze fel a **TenantId** és a **ApplicationId**, mert szüksége lesz ezekre az értékekre, az alkalmazás futtatásához.

### <a name="run-the-app"></a>Az alkalmazás futtatása

A minta futtatása előtt szerkessze az App.config fájlt, és a forgatókönyvtől függően adja meg a következő értékeket:

- `tenantId`: A **TenantId** értékre van állítva.
- `clientId`: A **ApplicationId** értékre van állítva. 
- `clientSecret`: Ha az ügyfél titkos kódjával szeretne bejelentkezni, hozza létre azt az Azure AD-ben. Egy webalkalmazás vagy API-t is, használja a natív alkalmazás helyett. Adja hozzá az alkalmazás alatt **hozzáférés-vezérlés (IAM)** a korábban létrehozott névtér.
- `eventHubNamespaceFQDN`: Állítsa az újonnan létrehozott Event Hubs névtér teljesen minősített DNS-nevére; például `example.servicebus.windows.net`:.
- `eventHubName`: Állítsa be a létrehozott Event hub nevét.
- Az átirányítási URI-t az alkalmazásba az előző lépésben megadott.
 
Futtassa a konzolalkalmazást, ha kéri választása; Kattintson a **interaktív felhasználói bejelentkezési** írja be a számát, és lenyomja az ENTER BILLENTYŰT. Az alkalmazás jeleníti meg a bejelentkezési ablakban, Ön hozzájárul az Event Hubs eléréséhez kéri, és a szolgáltatás segítségével a bejelentkezési azonosító használatával küldése/fogadása forgatókönyv futtatása.

Az alkalmazás jogkivonat `ServiceAudience.EventHubsAudience` -célközönségként működik. Ha más nyelveket vagy SDK-kat használ, ahol a közönség nem érhető el állandóként, a megfelelő értéket `https://eventhubs.azure.net/`kell használnia.

## <a name="next-steps"></a>További lépések

Ha további információkat szeretne az Event Hubsról, tekintse meg az alábbi hivatkozásokat:

* Bevezetés az [Event Hubs használatába oktatóanyag](event-hubs-dotnet-standard-getstarted-send.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)
* [Az Event Hubs szolgáltatás díjszabását.](https://azure.microsoft.com/pricing/details/event-hubs/)
* [Az Event Hubsot használó mintaalkalmazások](https://github.com/Azure/azure-event-hubs/tree/master/samples)

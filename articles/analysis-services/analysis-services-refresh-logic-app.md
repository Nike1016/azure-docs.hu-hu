---
title: Frissítés a Logic Apps for Azure Analysis Services models szolgáltatással | Microsoft Docs
description: Megtudhatja, hogyan lehet az aszinkron frissítést a Azure Logic Apps használatával dekódolni.
author: chrislound
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: chlound
ms.openlocfilehash: 2234a2c6cd42be45a2b2e7784c1dd5aec8839cb9
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311741"
---
# <a name="refresh-with-logic-apps"></a>Frissítés a Logic Apps használatával

Logic Apps és REST-hívások használatával automatizált adatfrissítési műveleteket hajthat végre az Azure Analysis táblázatos modelleken, beleértve a lekérdezési felskálázáshoz tartozó írásvédett replikák szinkronizálását is.

Ha többet szeretne megtudni a REST API-k Azure Analysis Services használatával történő használatáról, tekintse meg [az aszinkron frissítés a REST APIával](analysis-services-async-refresh.md)című témakört.

## <a name="authentication"></a>Authentication

Az összes hívást érvényes Azure Active Directory (OAuth 2) jogkivonattal kell hitelesíteni.  A cikkben szereplő példák egy egyszerű szolgáltatásnév (SPN) használatával végzik el a hitelesítést Azure Analysis Services. További információ: [egyszerű szolgáltatásnév létrehozása Azure Portal használatával](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="design-the-logic-app"></a>A logikai alkalmazás megtervezése

> [!IMPORTANT]
> Az alábbi példák azt feltételezik, hogy a Azure Analysis Services tűzfal le van tiltva.  Ha a tűzfal engedélyezve van, akkor a kérelem kezdeményezője számára engedélyezni kell a nyilvános IP-címet a Azure Analysis Services tűzfalon. A logikai alkalmazások IP-címtartományok régiónként való megismeréséhez tekintse meg [a Azure Logic apps korlátozásait és konfigurációs információit](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses).

### <a name="prerequisites"></a>Előfeltételek

#### <a name="create-a-service-principal-spn"></a>Egyszerű szolgáltatásnév (SPN) létrehozása

Az egyszerű szolgáltatásnév létrehozásával kapcsolatos további tudnivalókért lásd: [egyszerű szolgáltatásnév létrehozása Azure Portal használatával](../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="configure-permissions-in-azure-analysis-services"></a>Engedélyek konfigurálása Azure Analysis Services
 
Az Ön által létrehozott egyszerű szolgáltatásnak kiszolgálói rendszergazdai engedélyekkel kell rendelkeznie a kiszolgálón. További információ: [egyszerű szolgáltatásnév hozzáadása a kiszolgálói rendszergazdai szerepkörhöz](analysis-services-addservprinc-admins.md).

### <a name="configure-the-logic-app"></a>A logikai alkalmazás konfigurálása

Ebben a példában a logikai alkalmazás úgy van kialakítva, hogy HTTP-kérelem érkezésekor aktiválódik. Ezzel a beállítással engedélyezhető a Azure Analysis Services modell frissítésének elindítására szolgáló hangvezérelt eszköz (például Azure Data Factory) használata.

Miután létrehozott egy logikai alkalmazást:

1. A Logic app Designerben válassza ki az első műveletet, ahogy **http-kérés érkezik**.

   ![HTTP-fogadott tevékenység hozzáadása](./media/analysis-services-async-refresh-logic-app/1.png)

Ez a lépés a logikai alkalmazás mentése után a HTTP POST URL-címével lesz feltöltve.

2. Adjon hozzá egy új lépést, és keressen rá a **http**kifejezésre.  

   ![HTTP-tevékenység hozzáadása](./media/analysis-services-async-refresh-logic-app/9.png)

   ![HTTP-tevékenység hozzáadása](./media/analysis-services-async-refresh-logic-app/10.png)

3. A művelet hozzáadásához válassza a **http** lehetőséget.

   ![HTTP-tevékenység hozzáadása](./media/analysis-services-async-refresh-logic-app/2.png)

Konfigurálja a HTTP-tevékenységet a következőképpen:

|Tulajdonság  |Érték  |
|---------|---------|
|**Metódus**     |POST         |
|**URI**     | https://*a*/Servers/AAS-*kiszolgáló neve*/models/*az adatbázis neve*/refreshes <br /> <br /> Például: https:\//westus.asazure.Windows.net/Servers/MyServer/models/AdventureWorks/refreshes|
|**Fejlécek**     |   Content-Type, application/json <br /> <br />  ![Fejlécek](./media/analysis-services-async-refresh-logic-app/6.png)    |
|**Törzs**     |   A kérés törzsének kialakításával kapcsolatos további tudnivalókért tekintse meg [az aszinkron frissítés a REST API utáni/refreshes](analysis-services-async-refresh.md#post-refreshes)című témakört. |
|**Hitelesítés**     |Active Directory OAuth         |
|**Bérlő**     |Töltse ki a Azure Active Directory TenantId         |
|**Célközönség**     |https://*.asazure.windows.net         |
|**Ügyfél-azonosító**     |Adja meg az egyszerű szolgáltatásnév nevét ClientID         |
|**Hitelesítő adat típusa**     |Secret         |
|**Titkos kód**     |Adja meg az egyszerű szolgáltatásnév nevét         |

Példa:

![Befejezett HTTP-tevékenység](./media/analysis-services-async-refresh-logic-app/7.png)

Most tesztelje a logikai alkalmazást.  A Logic app Designerben kattintson a **Futtatás**elemre.

![A logikai alkalmazás tesztelése](./media/analysis-services-async-refresh-logic-app/8.png)

## <a name="consume-the-logic-app-with-azure-data-factory"></a>A logikai alkalmazás felhasználása Azure Data Factory

A logikai alkalmazás mentése után tekintse át a **http-kérelem fogadása** tevékenység után, majd másolja a most létrehozott **http post URL-címet** .  Ez az az URL-cím, amelyet a Azure Data Factory használhat a logikai alkalmazás aktiválására szolgáló aszinkron hívás végrehajtásához.

Példa Azure Data Factory webes tevékenységre, amely ezt a műveletet hajtja végre.

![Webes tevékenység Data Factory](./media/analysis-services-async-refresh-logic-app/11.png)

## <a name="use-a-self-contained-logic-app"></a>Önálló logikai alkalmazás használata

Ha nem tervezi a modell frissítésének elindításához szükséges hangvezérelt eszközt (például Data Factory), beállíthatja, hogy a logikai alkalmazás egy ütemterv alapján aktiválja a frissítést.

A fenti példa használatával törölje az első tevékenységet, és cserélje le egy **ütemezett** tevékenységre.

![Ütemezett tevékenység](./media/analysis-services-async-refresh-logic-app/12.png)

![Ütemezett tevékenység](./media/analysis-services-async-refresh-logic-app/13.png)

Ez a példa az **ismétlődést**fogja használni.

Miután hozzáadta a tevékenységet, konfigurálja az intervallumot és a gyakoriságot, majd adjon hozzá egy új paramétert, és válassza ki **ezeket**az órákat.

![Ütemezett tevékenység](./media/analysis-services-async-refresh-logic-app/16.png)

Válassza ki a kívánt órát.

![Ütemezett tevékenység](./media/analysis-services-async-refresh-logic-app/15.png)

Mentse a logikai alkalmazást.

## <a name="next-steps"></a>További lépések

[Példák](analysis-services-samples.md)  
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)

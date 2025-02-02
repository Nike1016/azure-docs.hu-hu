---
title: Az Azure AD Graph API használata
description: A Azure Active Directory (Azure AD) Graph API programozott hozzáférést biztosít az Azure AD-hez a OData REST API-végpontokon keresztül. Az alkalmazások az Azure AD Graph API használatával hozhatnak létre, olvashat, frissíthetnek és törölhetnek (szifilisz) műveleteket a címtáradatokat és az objektumokat.
services: active-directory
documentationcenter: n/a
author: rwike77
manager: CelesteDG
editor: ''
tags: ''
ms.assetid: 9dc268a9-32e8-402c-a43f-02b183c295c5
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: sureshja
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85c3a1953ce34ab6bf60111715d9d8972a4682ba
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68853388"
---
# <a name="how-to-use-the-azure-ad-graph-api"></a>Útmutató: Az Azure AD Graph API használata

A Azure Active Directory (Azure AD) Graph API programozott hozzáférést biztosít az Azure AD-hez a OData REST API-végpontokon keresztül. Az alkalmazások az Azure AD Graph API használatával hozhatnak létre, olvashat, frissíthetnek és törölhetnek (szifilisz) műveleteket a címtáradatokat és az objektumokat. Az Azure AD Graph API használatával például létrehozhat egy új felhasználót, megtekintheti vagy frissítheti a felhasználó tulajdonságait, módosíthatja a felhasználó jelszavát, megtekintheti a csoporttagság a szerepköralapú hozzáférés, a Letiltás vagy a felhasználó törlése lehetőséggel. Az Azure AD Graph API szolgáltatásaival és alkalmazási helyzetével kapcsolatos további információkért lásd az [Azure ad Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) és az [Azure ad Graph API előfeltételek](https://msdn.microsoft.com/library/hh974476.aspx)című témakört.

Ez a cikk az Azure AD Graph APIre vonatkozik. A Microsoft Graph API-val kapcsolatos hasonló információk: [a Microsoft Graph API használata](https://developer.microsoft.com/graph/docs/concepts/use_the_api).

> [!IMPORTANT]
> Az Azure AD Graph API helyett ajánljuk a [Microsoft Graph](https://developer.microsoft.com/graph) használatát az Azure Active Directory erőforrásainak eléréséhez. A fejlesztési energiáinkat mostantól a Microsoft Graph-ra koncentráljuk, az Azure AD Graph API-hoz nem tervezünk további fejlesztéseket. Nagyon korlátozott azon forgatókönyvek száma, amelyeknél az Azure AD Graph API használata még elegendő. További információért tekintse meg a [Microsoft Graph vagy Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) blogbejegyzést az Office fejlesztői központjában.

## <a name="how-to-construct-a-graph-api-url"></a>Graph API URL-cím létrehozása

A Graph API a címtáradatok és objektumok (más szóval, erőforrások vagy entitások) eléréséhez, amelyeken a SZIFILISZi műveleteket végre szeretné hajtani, az URL-címeket az Open OData (nyílt adatértékek) protokoll alapján használhatja. A Graph API használt URL-címek négy fő részből állnak: a szolgáltatás gyökeréből, a bérlői azonosítóból, az erőforrás `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`elérési útjából és a lekérdezési karakterlánc beállításaiból:. Végezze el a következő URL-cím `https://graph.windows.net/contoso.com/groups?api-version=1.6`példáját:.

* **Szolgáltatás gyökere**: Az Azure AD Graph API a szolgáltatás gyökerét mindig https://graph.windows.net.
* **Bérlő azonosítója**: Ez a szakasz ellenőrizhető (regisztrált) tartománynév lehet az előző példában contoso.com. A bérlői objektum azonosítója vagy a "myorganization" vagy a "Me" alias is lehet. További információ: entitások [és műveletek kezelése az Azure ad-ban Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview).
* **Erőforrás elérési útja**: Az URL-cím ezen szakasza a (felhasználók, csoportok, egy adott felhasználó vagy egy adott csoport stb.) által felhasználható erőforrást azonosítja. A fenti példában ez a legfelső szintű "csoportok", amely az erőforrás-készletet kezeli. Egy adott entitást (például "Users/{objectId}" vagy "Users/userPrincipalName") is megadhat.
* **Lekérdezési paraméterek**: A kérdőjel (?) elválasztja a lekérdezési paraméterek szakasz erőforrás elérési útja szakaszát. Az Azure AD Graph API összes kéréséhez az "API-version" lekérdezési paraméter szükséges. Az Azure AD Graph API a következő OData-lekérdezési lehetőségeket is támogatja: **$Filter**, **$OrderBy**, **$Expand**, **$Top**és **$Format**. A következő lekérdezési lehetőségek jelenleg nem támogatottak: **$Count**, **$inlinecount**és **$skip**. További információ: [támogatott lekérdezések, szűrők és lapozási beállítások az Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options).

## <a name="graph-api-versions"></a>Graph API verziók

Graph API kérelem verzióját az "API-version" lekérdezési paraméterben kell megadni. A 1,5-es és újabb verziók esetében numerikus verziószámot kell használni; API-Version = 1.6. Korábbi verziók esetén a Date karakterláncot kell használnia, amely az éééé-hh-nn formátumba van betartva. például: API-Version = 2013-11-08. Az előzetes verziójú funkciókhoz használja a "Beta" karakterláncot; például: API-Version = Beta. Graph API verziók közötti különbségekkel kapcsolatos további információkért lásd: az [Azure AD Graph API verziószámozása](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning).

## <a name="graph-api-metadata"></a>Metaadatok Graph API

Az Azure AD Graph API metaadat-fájl visszaadásához adja hozzá a "$metadata" szakaszt az URL-címen található bérlő-azonosító után, például a következő URL-cím egy bemutató vállalat `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`metaadatait adja vissza:. A metaadatok megtekintéséhez megadhatja ezt az URL-címet a böngésző címsorába. A visszaadott CSDL metaadat-dokumentum leírja az entitásokat és az összetett típusokat, azok tulajdonságait, valamint a kért Graph API verziója által közzétett funkciókat és műveleteket. Az API-Version paraméter kihagyása a legújabb verzió metaadatait adja vissza.

## <a name="common-queries"></a>Gyakori lekérdezések

Az [Azure ad Graph API gyakori lekérdezések](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries) felsorolja az Azure ad Graph-ban használható gyakori lekérdezéseket, beleértve azokat a lekérdezéseket is, amelyek a címtár legfelső szintű erőforrásainak eléréséhez, valamint a címtárban végrehajtott műveletek végrehajtásához használhatók.

A például `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` a címtár contoso.com tartozó vállalati adatokat adja vissza.

Vagy `https://graph.windows.net/contoso.com/users?api-version=1.6` listázza az összes felhasználói objektumot a címtár contoso.com.

## <a name="using-the-azure-ad-graph-explorer"></a>Az Azure AD Graph Explorer használata
Az Azure ad Graph Explorerrel használhatja az Azure AD Graph API a címtáradatok lekérdezéséhez az alkalmazás létrehozásakor.

Az alábbi képernyőképen a kimenet látható, ha az Azure ad Graph Explorerrel navigál, jelentkezzen be, és az ENTER billentyűt `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6` megnyomva jelenítse meg a bejelentkezett felhasználó címtárában lévő összes felhasználót:

![Példa kimenet az Azure AD Graph API Explorerben](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**Töltse be az Azure ad Graph Explorert**: Az eszköz betöltéséhez navigáljon [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/)a következőhöz:. Kattintson a **Bejelentkezés** elemre, és jelentkezzen be az Azure ad-fiókja hitelesítő adataival az Azure ad Graph Explorer a bérlőn való futtatásához. Ha az Azure AD Graph Explorert a saját bérlőn futtatja, akkor Ön vagy a rendszergazdának kell beleegyeznie a bejelentkezés során. Ha rendelkezik Office 365-előfizetéssel, automatikusan rendelkezik egy Azure AD-Bérlővel. Az Office 365-be való bejelentkezéshez használt hitelesítő adatok valójában az Azure AD-fiókokat használják, és ezeket a hitelesítő adatokat az Azure AD Graph Explorerrel is használhatja.

**Lekérdezés futtatása**: A lekérdezés futtatásához írja be a lekérdezést a kérelem szövegmezőbe, majd kattintson a lekérés gombra, vagy kattintson az **ENTER** billentyűre. Az eredmények a válasz mezőben jelennek meg. Például `https://graph.windows.net/myorganization/groups?api-version=1.6` felsorolja a bejelentkezett felhasználó címtárában lévő összes csoport objektumot.

Vegye figyelembe az Azure AD Graph Explorer következő funkcióit és korlátozásait:

* Automatikus kiegészítési képesség az erőforrás-készleteken. A funkció megjelenítéséhez kattintson a kérelem szövegmezőre (ahol megjelenik a vállalati URL-cím). Kiválaszthat egy erőforrás-készletet a legördülő listából.
* Kérelmek előzményei.
* A "Me" és a "myorganization" címzési aliasokat támogatja. Például a használatával `https://graph.windows.net/me?api-version=1.6` visszaállíthatja a bejelentkezett felhasználó felhasználói objektumát, vagy `https://graph.windows.net/myorganization/users?api-version=1.6` visszaállíthatja az összes felhasználót a bejelentkezett felhasználó címtárában.
* A a `POST`, a `PATCH` és `GET` ahasználatávalteljeskörűszifilisz-műveletekettámogatasajátcímtárában.`DELETE`
* A válasz fejlécei szakasz. Ez a szakasz a lekérdezések futtatásakor felmerülő problémák elhárításához használható.
* Egy JSON-megjelenítő a válaszhoz a kibontási és összecsukási képességekkel.
* Nem támogatott a miniatűr fényképek megjelenítésének vagy feltöltésének támogatása.

## <a name="using-fiddler-to-write-to-the-directory"></a>A Hegedűs használata a címtárba való írásra

Ebben a rövid útmutatóban a Hegedűs webes hibakeresője használatával végezheti el az írási műveletek végrehajtását az Azure AD-címtárban. Lekérheti és feltöltheti például a felhasználó profiljának fényképét (ez az Azure AD Graph Explorerrel nem lehetséges). További információért és a Hegedűs telepítéséhez lásd: [https://www.telerik.com/fiddler](https://www.telerik.com/fiddler).

Az alábbi példában a Hegedűs web Debugger használatával hozzon létre egy új "MyTestGroup" biztonsági csoportot az Azure AD-címtárban.

**Hozzáférési jogkivonat**beszerzése: Az Azure AD Graph eléréséhez az ügyfeleknek sikeresen hitelesíteniük kell magukat az Azure AD-ben. További információ: [hitelesítési forgatókönyvek az Azure ad-hez](authentication-scenarios.md).

**Lekérdezés összeállítása és futtatása**: Hajtsa végre a következő lépéseket:

1. Nyissa meg a Hegedűs webes hibakeresőjét, és váltson a **zeneszerző** lapra.
2. Mivel új biztonsági csoportot szeretne létrehozni, válassza a **Közzététel** http-metódusként lehetőséget a legördülő menüből. További információ a csoport objektumainak műveleteiről és engedélyeiről: [csoport](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#group-entity) az [Azure ad Graph REST API referenciájában](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
3. A **post**következő mezőjébe írja be a következő kérelem URL-címét: `https://graph.windows.net/{mytenantdomain}/groups?api-version=1.6`.
   
   > [!NOTE]
   > A (z) {mytenantdomain} nevet a saját Azure AD-címtárának tartománynevével kell helyettesíteni.

4. Írja be a következő HTTP-fejlécet a mezőbe közvetlenül a post legördülő menüben:
   
    ```
   Host: graph.windows.net
   Authorization: Bearer <your access token>
   Content-Type: application/json
   ```
   
   > [!NOTE]
   > Helyettesítse be a hozzáférési&gt; jogkivonatát az Azure ad-címtárhoz tartozó hozzáférési jogkivonattal. &lt;

5. A **kérelem törzse** mezőben írja be a következő JSON-t:
   
    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
   ```
   
    A csoportok létrehozásával kapcsolatos további információkért lásd: [csoport létrehozása](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup).

További információ az Azure AD-entitásokról és a gráftal elvégezhető műveletekről, valamint az [Azure ad Graph REST API referenciája](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)című témakörben talál.

## <a name="next-steps"></a>További lépések

* További információ az [Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
* További információ az [Azure AD Graph API engedélyek hatóköréről](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)

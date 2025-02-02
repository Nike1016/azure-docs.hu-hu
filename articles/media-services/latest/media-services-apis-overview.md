---
title: Fejlesztés a V3 API-kkal – Azure | Microsoft Docs
description: Ez a cikk azokat a szabályokat ismerteti, amelyek az entitásokra és API-kra vonatkoznak, amikor Media Services v3-val fejlesztenek.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 26fea4322df625b2e38028a3b7121fb41f2acf81
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311860"
---
# <a name="developing-with-media-services-v3-apis"></a>Fejlesztés Media Services V3 API-kkal

Fejlesztőként Media Services [REST API](https://aka.ms/ams-v3-rest-ref) vagy ügyféloldali kódtárakat használhat, amelyek lehetővé teszik az REST API használatát az egyéni adathordozó-munkafolyamatok egyszerű létrehozásához, kezeléséhez és karbantartásához. Az [Media Services v3](https://aka.ms/ams-v3-rest-sdk) API a OpenAPI-specifikáción alapul (korábbi nevén a hencegés).

Ez a cikk azokat a szabályokat ismerteti, amelyek az entitásokra és API-kra vonatkoznak, amikor Media Services v3-val fejlesztenek.

## <a name="accessing-the-azure-media-services-api"></a>A Azure Media Services API elérése

A Media Services erőforrások és a Media Services API elérésének engedélyezéséhez először hitelesítenie kell magát. Media Services támogatja a [Azure Active Directory (Azure ad)-alapú](../../active-directory/fundamentals/active-directory-whatis.md) hitelesítést. Két gyakori hitelesítési lehetőség a következők:
 
* **Egyszerű szolgáltatás hitelesítése** – szolgáltatás hitelesítéséhez (például Web Apps, Function apps, Logic apps, API és a Service). A hitelesítési módszert gyakran használó alkalmazások olyan alkalmazások, amelyek a Daemon Services, a közepes szintű szolgáltatások vagy az ütemezett feladatok futtatására szolgálnak. Webalkalmazások esetén például mindig olyan közepes rétegnek kell lennie, amely egy egyszerű szolgáltatással csatlakozik Media Serviceshoz.
* **Felhasználói hitelesítés** – az alkalmazást használó személy hitelesítésére használható Media Services erőforrásokkal való kommunikációhoz. Az interaktív alkalmazásnak először meg kell kérnie a felhasználót a felhasználó hitelesítő adatainak megadására. Erre példa egy olyan felügyeleti konzol alkalmazás, amelyet a jogosultsággal rendelkező felhasználók a kódolási feladatok és az élő adatfolyamok figyelésére használnak.

A Media Services API megköveteli, hogy a REST API kérelmeket készítő felhasználó vagy alkalmazás hozzáférhessen a Media Services fiók erőforrásához, és használjon **közreműködői** vagy **tulajdonosi** szerepkört. Az API-t az **olvasó** szerepkörrel lehet elérni, de csak a **Get** vagy a **List**   művelet lesz elérhető. További információ: Media Services- [fiókok szerepköralapú hozzáférés-vezérlése](rbac-overview.md).

Egyszerű szolgáltatásnév létrehozása helyett érdemes lehet felügyelt identitásokat használni az Azure-erőforrásokhoz az Media Services API Azure Resource Manager használatával való eléréséhez. Az Azure-erőforrások felügyelt identitásával kapcsolatos további tudnivalókért tekintse meg a [Mi az Azure-erőforrások felügyelt identitása](../../active-directory/managed-identities-azure-resources/overview.md)című témakört.

### <a name="azure-ad-service-principal"></a>Azure AD egyszerű szolgáltatás 

Ha Azure AD-alkalmazást és egyszerű szolgáltatásnevet hoz létre, akkor az alkalmazásnak saját bérlőn kell lennie. Az alkalmazás létrehozása után adja meg az alkalmazás **közreműködői** vagy tulajdonosi **** szerepkörének hozzáférését az Media Services fiókhoz. 

Ha nem biztos abban, hogy rendelkezik-e engedéllyel Azure AD-alkalmazás létrehozásához, tekintse meg a [szükséges engedélyeket](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

A következő ábrán a számok kronológiai sorrendben jelenítik meg a kérelmek folyamatát:

![Közepes szintű alkalmazások](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. A középső rétegbeli alkalmazás olyan Azure AD hozzáférési jogkivonatot kér, amely a következő paraméterekkel rendelkezik:  

   * Azure AD-bérlői végpont.
   * Media Services erőforrás URI-ja.
   * A REST Media Services erőforrás-URI-ja.
   * Azure AD-alkalmazás értékei: az ügyfél-azonosító és az ügyfél titka.
   
   Az összes szükséges érték beszerzéséhez tekintse meg [Az Azure CLI-vel való hozzáférés Azure Media Services API-val](access-api-cli-how-to.md) című témakört.

2. Az Azure AD hozzáférési jogkivonatot a középső szinten küldik el.
4. A középső szintű kérelem küldése az Azure Media REST API az Azure AD-jogkivonattal.
5. A középső szinten a Media Services származó adatok kerülnek vissza.

### <a name="samples"></a>Példák

Tekintse meg a következő mintákat, amelyek bemutatják, hogyan csatlakozhat az Azure AD egyszerű szolgáltatásához:

* [Kapcsolódás a REST-tel](media-rest-apis-with-postman.md)  
* [Kapcsolódás Javával](configure-connect-java-howto.md)
* [Kapcsolódás .NET-tel](configure-connect-dotnet-howto.md)
* [Kapcsolódás Node.js-sel](configure-connect-nodejs-howto.md)
* [Kapcsolódás Pythonnal](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>Elnevezési konvenciók

Az Azure Media Services v3 erőforrásneveire is (pl. Adategység, Feladatok, Átalakítások) az Azure Resource Manager elnevezési korlátozásai vonatkoznak. Az Azure Resource Manager szolgáltatásnak megfelelően az erőforrásnevek mindig egyediek. Így bármilyen egyedi azonosító sztringet (pl. GUID-ok) használhat erőforrásnévként. 

A Media Services-erőforrás neve nem tartalmazhatja a következőket: "<", ">", "%", "&", ': ','&#92;','?', '/', "*", "+",".", szimpla idézőjel vagy bármely egyéb vezérlőkarakter. Minden egyéb karakter engedélyezett. Az erőforrásnév maximális hossza 260 karakter. 

A Azure Resource Manager elnevezéssel kapcsolatos további információkért lásd: [Elnevezési követelmények](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) és [elnevezési konvenciók](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="long-running-operations"></a>Hosszan futó műveletek

A Azure Media Services hencegő `x-ms-long-running-operation` fájlokban megjelölt [](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) műveletek hosszú ideig futó műveletek. 

Az aszinkron Azure-műveletek nyomon követésével kapcsolatos részletekért lásd: [aszinkron műveletek](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation).

Media Services a következő hosszan futó műveletekkel rendelkezik:

* [Élő események létrehozása](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [Élő események frissítése](https://docs.microsoft.com/rest/api/media/liveevents/update)
* [Élő esemény törlése](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [Élő esemény indítása](https://docs.microsoft.com/rest/api/media/liveevents/start)
* [LiveEvent leállítása](https://docs.microsoft.com/rest/api/media/liveevents/stop)

  `removeOutputsOnStop` A paraméter használatával törölje az összes társított élő kimenetet az esemény leállításakor.  
* [LiveEvent alaphelyzetbe állítása](https://docs.microsoft.com/rest/api/media/liveevents/reset)
* [LiveOutput létrehozása](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [LiveOutput törlése](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [Streamvégpontok létrehozása](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)
* [Streamvégpontok frissítése](https://docs.microsoft.com/rest/api/media/streamingendpoints/update)
* [Streamvégpontok törlése](https://docs.microsoft.com/rest/api/media/streamingendpoints/delete)
* [Streamvégpontok elindítása](https://docs.microsoft.com/rest/api/media/streamingendpoints/start)
* [Streamvégpontok leállítása](https://docs.microsoft.com/rest/api/media/streamingendpoints/stop)
* [Streamvégpontok skálázása](https://docs.microsoft.com/rest/api/media/streamingendpoints/scale)

A hosszú művelet sikeres beadásakor a rendszer "202 elfogadva" értéket kap, és a visszaadott művelet AZONOSÍTÓjának használatával kell lekérdezni a művelet befejezését.

Egy adott élő esemény vagy bármely hozzá tartozó élő kimenet esetében csak egy hosszan futó művelet támogatott. Az indítás után a hosszú ideig futó műveletnek meg kell felelnie, mielőtt egy későbbi, hosszan futó műveletet elindítson ugyanazon a LiveEvent vagy a kapcsolódó élő kimeneteken. Több élő kimenettel rendelkező élő események esetén várnia kell egy hosszú ideig futó művelet befejezését egy élő kimeneten, mielőtt a hosszú ideig futó műveletet aktivál egy másik élő kimeneten. 

## <a name="sdks"></a>SDK-k

> [!NOTE]
> A Azure Media Services v3 SDK-k nem garantáltak, hogy a szál biztonságos. Többszálas alkalmazások fejlesztésekor saját szál-szinkronizálási logikát kell hozzáadnia az ügyfél védeleméhez, vagy a szálon egy új AzureMediaServicesClient objektumot kell használnia. Ügyeljen arra, hogy a kód által az ügyfélnek (például egy .NET-HttpClient-példány) származó opcionális objektumok által bevezetett többszálas problémák is körültekintőek legyenek.

|SDK|Hivatkozás|
|---|---|
|[.NET SDK](https://aka.ms/ams-v3-dotnet-sdk)|[.NET-referencia](https://aka.ms/ams-v3-dotnet-ref)|
|[Java SDK](https://aka.ms/ams-v3-java-sdk)|[Java-referencia](https://aka.ms/ams-v3-java-ref)|
|[Python SDK](https://aka.ms/ams-v3-python-sdk)|[Python-referencia](https://aka.ms/ams-v3-python-ref)|
|[Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk) |[Node.js-referencia](https://aka.ms/ams-v3-nodejs-ref)| 
|[Go SDK](https://aka.ms/ams-v3-go-sdk) |[Go-referencia](https://aka.ms/ams-v3-go-ref)|
|[Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>Lásd még

- [A Media Service-eseményeket tartalmazó EventGrid .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Media Services események definíciói](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Azure Media Services Explorer

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) A (AMSE) eszköz olyan Windows-ügyfelek számára érhető el, akik szeretnének többet megtudni a Media Servicesról. A AMSE egy WinForms/C# alkalmazás, amely feltölti, letölti, kódolja, TOVÁBBÍTJA a VOD-t és az élő tartalmakat Media Services. A AMSE eszköz olyan ügyfelek számára készült, akik kód írása nélkül szeretnék tesztelni Media Services. A AMSE-kód olyan ügyfelek számára biztosít erőforrásként, akik Media Serviceskal szeretnének fejleszteni.

A AMSE egy nyílt forráskódú projekt, amely a Közösség által biztosított támogatással kapcsolatos https://github.com/Azure/Azure-Media-Services-Explorer/issues). A projekt a Microsoft nyílt forráskódú projekteket szabályozó etikai kódexe, a [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/) hatálya alá esik. További információkért tekintse meg [](https://opensource.microsoft.com/codeofconduct/faq/) a viselkedési szabályzatot, opencode@microsoft.com vagy forduljon a további kérdésekhez vagy megjegyzésekhez.

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Media Services entitások szűrése, rendezése és lapozása

Lásd: [Azure Media Services entitások szűrése, rendezése](entities-overview.md) és lapozása

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdések feltevése, visszajelzés küldése, frissítések beszerzése

Tekintse meg a [Azure Media Services közösségi](media-services-community.md) cikket, amely különböző módokon jelenítheti meg a kérdéseket, visszajelzéseket küldhet, és frissítéseket kaphat a Media Servicesról.

## <a name="see-also"></a>Lásd még

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>További lépések

* [Kapcsolódás Media Services Javával](configure-connect-java-howto.md)
* [Kapcsolódás Media Services a .NET-tel](configure-connect-dotnet-howto.md)
* [Kapcsolódás Media Services a Node. js használatával](configure-connect-nodejs-howto.md)
* [Kapcsolódás Media Services a Python használatával](configure-connect-python-howto.md)

---
title: A Media Services Operations REST API áttekintése | Microsoft Docs
description: Media Services REST API áttekintése
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: a5f1c5e7-ec52-4e26-9a44-d9ea699f68d9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.reviewer: johndeu
ms.openlocfilehash: 29b995d722cd304cc85580ac4f2f38a0b0d9cecd
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "69014853"
---
# <a name="media-services-operations-rest-api-overview"></a>Media Services Operations REST API áttekintése 

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Próbálja ki a legújabb verziót, ami a [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-from-v2-to-v3.md)

A **Media Services Operations Rest** API-val feladatok, eszközök, élő csatornák és más erőforrások létrehozására van szükség egy Media Services-fiókban. További információ: [Media Services operations REST API Reference](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).

A Media Services olyan REST API biztosít, amely a JSON vagy az Atom + pub XML formátumot is elfogadja. Media Services REST API olyan speciális HTTP-fejléceket igényel, amelyeket az egyes ügyfeleknek el kell küldeniük a Media Serviceshoz való csatlakozáskor, valamint a választható fejlécek készletében. A következő szakaszok azokat a fejléceket és HTTP-műveleteket ismertetik, amelyeket a kérelmek létrehozásakor és a Media Servicestól kapott válaszok fogadásakor használhat.

A Media Services REST API hitelesítése Azure Active Directory hitelesítésen keresztül történik, amely az [Azure ad-hitelesítés használata a Azure Media Services API Rest](media-services-rest-connect-with-aad.md) -tel való eléréséhez.

## <a name="considerations"></a>Megfontolandó szempontok

A REST használatakor a következő szempontokat kell figyelembe venni.

* Az entitások lekérdezése esetén a rendszer egy legfeljebb 1000 entitást ad vissza, mert a nyilvános REST v2 a lekérdezés eredményét 1000 eredményre korlátozza. A kihagyás és a végrehajtás (.net)/ **Top** (REST) használata szükséges a [jelen .net-Példa](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) és [a REST API példa](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities)szerint. 
* Ha JSON-t használ, és azt adja meg, hogy az **__metadata** kulcsszót használja a kérelemben (például egy csatolt objektumra való hivatkozáshoz), be kell állítania az **Accept** fejlécet a [JSON részletes formátumba](https://www.odata.org/documentation/odata-version-3-0/json-verbose-format/) (lásd a következő példát). A OData nem tudja értelmezni a kérelemben szereplő **__metadata** tulajdonságot, kivéve, ha azt részletesen beállította.  
  
        POST https://media.windows.net/API/Jobs HTTP/1.1
        Content-Type: application/json;odata=verbose
        Accept: application/json;odata=verbose
        DataServiceVersion: 3.0
        MaxDataServiceVersion: 3.0
        x-ms-version: 2.17
        Authorization: Bearer <ENCODED JWT TOKEN> 
        Host: media.windows.net
  
        {
            "Name" : "NewTestJob", 
            "InputMediaAssets" : 
                [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aba5356eb-30ff-4dc6-9e5a-41e4223540e7')"}}]
        . . . 

## <a name="standard-http-request-headers-supported-by-media-services"></a>A Media Services által támogatott szabványos HTTP-kérelmek fejlécei
A Media Servicesban megjelenő minden egyes híváshoz meg kell adni a szükséges fejléceket, amelyeket fel kell vennie a kérelembe, valamint a választható fejléceket is, amelyeket érdemes felvennie. Az alábbi táblázat felsorolja a szükséges fejléceket:

| Fejléc | Type | Value |
| --- | --- | --- |
| Authorization |Tulajdonosi |A tulajdonos az egyetlen elfogadott engedélyezési mechanizmus. Az értéknek tartalmaznia kell a Azure Active Directory által biztosított hozzáférési jogkivonatot is. |
| x-ms-version |Decimal |2,17 (vagy a legújabb verzió)|
| DataServiceVersion |Decimal |3.0 |
| MaxDataServiceVersion |Decimal |3.0 |

> [!NOTE]
> Mivel Media Services a OData használatával teszi elérhetővé a REST API-kat, a DataServiceVersion és a MaxDataServiceVersion fejléceket minden kérelembe bele kell foglalni; Ha azonban nem, akkor a jelenleg Media Services feltételezi, hogy a használatban lévő DataServiceVersion értéke 3,0.
> 
> 

A következő a választható fejlécek halmaza:

| Fejléc | Type | Value |
| --- | --- | --- |
| Date |RFC 1123 dátum |A kérés időbélyege |
| Elfogadás |Tartalom típusa |A kért tartalomtípus a válaszhoz, például a következő:<p> -application/json;odata=verbose<p> -Application/Atom + XML<p> A válaszok eltérő tartalomtípussal rendelkezhetnek, például egy blob beolvasásával, ahol a sikeres válasz a blob streamet tartalmazza hasznos adattartalomként. |
| Elfogadás – kódolás |Gzip, deflate |A GZIP és a deflate kódolás, ha alkalmazható. Megjegyzés: A nagyméretű erőforrások esetében Media Services figyelmen kívül hagyhatja ezt a fejlécet, és nem tömörített adatmennyiséget adhat vissza. |
| Elfogadás – nyelv |"en", "es" stb. |Megadja a válasz előnyben részesített nyelvét. |
| Elfogadás – charset |Karakterkészlet típusa, például "UTF-8" |Az alapértelmezett érték az UTF-8. |
| X-HTTP-Method |HTTP-metódus |Lehetővé teszi az olyan ügyfelek vagy tűzfalak használatát, amelyek nem támogatják az olyan HTTP-metódusokat, mint a PUT vagy a DELETE, ha ezeket a metódusokat szeretné használni, egy GET hívással. |
| Content-Type |Tartalom típusa |A kérelem törzsének tartalomtípusa a PUT vagy POST kérelmekben. |
| ügyfél-kérelem azonosítója |Sztring |Egy hívó által definiált érték, amely az adott kérelmet azonosítja. Ha meg van adva, a rendszer ezt az értéket fogja tartalmazni a válaszüzenetben a kérelem leképezéséhez. <p><p>**Fontos**<p>Az értékeket a 2096b (2k) értékre kell korlátozni. |

## <a name="standard-http-response-headers-supported-by-media-services"></a>A Media Services által támogatott szabványos HTTP-válasz fejlécek
A következőkben egy olyan fejléc található, amely a kért erőforrástól és a végrehajtandó művelettől függően visszatérhet Önnek.

| Fejléc | Type | Value |
| --- | --- | --- |
| kérelem azonosítója |Sztring |Az aktuális művelet egyedi azonosítója, a szolgáltatás létrehozva. |
| ügyfél-kérelem azonosítója |Sztring |A hívó által az eredeti kérelemben megadott azonosító, ha van ilyen. |
| Date |RFC 1123 dátum |A kérelem feldolgozásának dátuma és időpontja. |
| Content-Type |Változó |A válasz törzsének tartalomtípusa |
| Content-Encoding |Változó |A gzip vagy a deflate, ha szükséges. |

## <a name="standard-http-verbs-supported-by-media-services"></a>A Media Services által támogatott szabványos HTTP-műveletek
A következő lista a http-kérelmekhez használható HTTP-műveletek teljes listáját tartalmazza:

| művelet | Leírás |
| --- | --- |
| GET |Egy objektum aktuális értékét adja vissza. |
| POST |Létrehoz egy objektumot a megadott információk alapján, vagy elküld egy parancsot. |
| PUT |Lecserél egy objektumot, vagy létrehoz egy elnevezett objektumot (ha van ilyen). |
| DELETE |Töröl egy objektumot. |
| MERGE |Egy meglévő objektumot frissít, amelynek elnevezett tulajdonsága megváltozik. |
| HEAD |A GET válaszhoz tartozó objektum metaadatait adja vissza. |

## <a name="discover-and-browse-the-media-services-entity-model"></a>A Media Services entitás modell felderítése és tallózása
Media Services entitások felderíthetővé tételéhez a $metadata művelet is használható. Lehetővé teszi az összes érvényes entitás típusának, az entitás tulajdonságainak, társításának, funkcióinak, műveleteinek és így beolvasását. Ha hozzáadja a $metadata műveletet a Media Services REST API végpont végéhez, elérheti ezt a felderítési szolgáltatást.

 /api/$metadata.

Ha egy böngészőben szeretné megtekinteni a metaadatokat, a "? API-Version = 2. x" utótagot az URI végéhez kell hozzáfűzni, vagy a kérelemben ne adja meg az x-MS-Version fejlécet.

## <a name="authenticate-with-media-services-rest-using-azure-active-directory"></a>Hitelesítés Media Services REST használatával Azure Active Directory

A REST APIon végzett hitelesítés Azure Active Directoryon (HRE) keresztül történik.
A Media Services fiókjának az Azure Portalról történő megszerzésével kapcsolatos további információkért tekintse meg [a Azure Media Services API Azure ad](media-services-use-aad-auth-to-access-ams-api.md)-hitelesítéssel történő elérését ismertető témakört.

Az Azure AD-hitelesítéssel REST APIhoz kapcsolódó kódok írásához tekintse meg a következő cikket: az [Azure ad-hitelesítés használata a Azure Media Services API Rest](media-services-rest-connect-with-aad.md)-tel való eléréséhez.

## <a name="next-steps"></a>További lépések
Az Azure AD-hitelesítés Media Services REST API használatával történő használatáról további információt [Az Azure ad-hitelesítés használata a Azure Media Services API Rest-tel való eléréséhez](media-services-rest-connect-with-aad.md)című témakörben talál.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]


---
title: Blobok hozzáadása objektumokhoz az Azure Digital Twinsban | Microsoft Docs
description: Megtudhatja, hogyan adhat blobokat az objektumokhoz az Azure Digital Twinsban.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 08/09/2019
ms.author: v-adgera
ms.custom: seodec18
ms.openlocfilehash: 61c09435606612377781fb382d2d31144e96b07b
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68965952"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>Blobok hozzáadása objektumokhoz az Azure digitális Ikrekben

A Blobok a gyakori fájltípusok, például a képek és a naplók strukturálatlan ábrázolásai. A Blobok nyomon követhetik, hogy milyen típusú adatok szerepelnek a MIME-típus használatával (például: "rendszerkép/JPEG") és metaadatok (név, leírás, típus stb.).

Az Azure Digital Twins támogatja a Blobok eszközökhöz, tárhelyekhez és felhasználókhoz való csatolását. A Blobok egy felhasználó, egy eszköz fényképe, egy videó, egy Térkép, egy belső vezérlőprogram, egy JSON-adatbázis, egy napló stb.

[!INCLUDE [Digital Twins Management API familiarity](../../includes/digital-twins-familiarity.md)]

## <a name="uploading-blobs-overview"></a>Blobok feltöltése – áttekintés

A többrészes kérelmek használatával blobokat tölthet fel adott végpontokra és azok funkcionalitására.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

### <a name="blob-metadata"></a>Blob metaadatai

A **Content-Type** és a **Content-hajlam**mellett az Azure digitális Twins blob többrészes kéréseinek meg kell adniuk a helyes JSON-törzset. A beküldeni kívánt JSON-törzs az éppen végrehajtott HTTP-kérési művelettől függ.

A négy fő JSON-séma a következők:

[![JSON-sémák](media/how-to-add-blobs/blob-models-img.png)](media/how-to-add-blobs/blob-models-img.png#lightbox)

A JSON-blob metaadatai a következő modellnek felelnek meg:

```JSON
{
    "parentId": "00000000-0000-0000-0000-000000000000",
    "name": "My First Blob",
    "type": "Map",
    "subtype": "GenericMap",
    "description": "A well chosen description",
    "sharing": "None"
  }
```

| Attribútum | Type | Leírás |
| --- | --- | --- |
| **parentId** | Sztring | A blob és a (szóközök, eszközök vagy felhasználók) hozzárendelése a szülő entitáshoz |
| **name** |Sztring | A blob egy emberi felhasználóbarát neve |
| **type** | Sztring | A blob típusa – a *típus* -és *typeId* nem használható  |
| **typeId** | Egész szám | A blob típusának azonosítója – nem használható a *Type* és a *typeId* |
| **subtype** | Sztring | A blob altípusa-nem használhatja az altípust és a *subtypeId* |
| **subtypeId** | Egész szám | A blob altípusának azonosítója – nem használható *altípus* és *subtypeId* |
| **description** | Sztring | A blob testreszabott leírása |
| **sharing** | Sztring | Azt határozza meg, hogy a blob osztható-`None`e `Tree`: `Global`Enum [,,] |

A blob metaadatait mindig a **Content-Type** `application/json` vagy egy `.json` fájlként megadott első adattömbként adja meg a rendszer. A rendszer a második adattömbben adja meg a fájlhoz tartozó adatmennyiséget, és bármilyen támogatott MIME-típust tartalmazhat.

A kivágási dokumentáció részletesen ismerteti ezeket a modell-sémákat.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

Ismerje meg a dokumentáció [használatát](./how-to-use-swagger.md)a hencegés használatával.

<div id="blobModel"></div>

### <a name="blobs-response-data"></a>Blobok válaszideje

Az egyénileg visszaadott Blobok megfelelnek a következő JSON-sémának:

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "parentId": "00000000-0000-0000-0000-000000000000",
  "type": "string",
  "subtype": "string",
  "typeId": 0,
  "subtypeId": 0,
  "sharing": "None",
  "description": "string",
  "contentInfos": [
    {
      "type": "string",
      "sizeBytes": 0,
      "mD5": "string",
      "version": "string",
      "lastModifiedUtc": "2019-01-12T00:58:08.689Z",
      "metadata": {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      }
    }
  ],
  "fullName": "string",
  "spacePaths": [
    "string"
  ]
}
```

| Attribútum | Type | Leírás |
| --- | --- | --- |
| **id** | Sztring | A blob egyedi azonosítója |
| **name** |Sztring | A blob egy emberi felhasználóbarát neve |
| **parentId** | Sztring | A blob és a (szóközök, eszközök vagy felhasználók) hozzárendelése a szülő entitáshoz |
| **type** | Sztring | A blob típusa – a *típus* -és *typeId* nem használható  |
| **typeId** | Egész szám | A blob típusának azonosítója – nem használható a *Type* és a *typeId* |
| **subtype** | Sztring | A blob altípusa-nem használhatja az altípust és a *subtypeId* |
| **subtypeId** | Egész szám | A blob altípusának azonosítója – nem használható *altípus* és *subtypeId* |
| **sharing** | Sztring | Azt határozza meg, hogy a blob osztható-`None`e `Tree`: `Global`Enum [,,] |
| **description** | Sztring | A blob testreszabott leírása |
| **contentInfos** | Array | Strukturálatlan metaadatokat ad meg, beleértve a verziószámot is |
| **fullName** | Sztring | A blob teljes neve |
| **spacePaths** | Sztring | A terület elérési útja |

A blob metaadatait mindig a **Content-Type** `application/json` vagy egy `.json` fájlként megadott első adattömbként adja meg a rendszer. A rendszer a második adattömbben adja meg a fájlhoz tartozó adatmennyiséget, és bármilyen támogatott MIME-típust tartalmazhat.

### <a name="blob-multipart-request-examples"></a>BLOB többrészes kérelmekre vonatkozó példák

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Ha egy szövegfájlt blobként kíván feltölteni, és egy szóközzel társítja azt, akkor a következő módon hitelesített HTTP POST-kérelmet kell tennie:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs
```

A következő szervezethez:

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "ParentId": "54213cf5-285f-e611-80c3-000d3a320e1e",
  "Name": "My First Blob",
  "Type": "Map",
  "SubType": "GenericMap",
  "Description": "A well chosen description",
  "Sharing": "None"
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="myblob.txt"
Content-Type: text/plain

This is my blob content. In this case, some text, but I could also be uploading a picture, an JSON file, a firmware zip, etc.

--USER_DEFINED_BOUNDARY--
```

| Value | Csere erre |
| --- | --- |
| USER_DEFINED_BOUNDARY | Egy többrészes tartalom határának neve |

A következő kód ugyanazon blob-feltöltés .NET-implementációja, amely a [MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent)osztályt használja:

```csharp
//Supply your metadata in a suitable format
var multipartContent = new MultipartFormDataContent("USER_DEFINED_BOUNDARY");

var metadataContent = new StringContent(JsonConvert.SerializeObject(metaData), Encoding.UTF8, "application/json");
metadataContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json; charset=utf-8");
multipartContent.Add(metadataContent, "metadata");

//MY_BLOB.txt is the String representation of your text file
var fileContents = new StringContent("MY_BLOB.txt");
fileContents.Headers.ContentType = MediaTypeHeaderValue.Parse("text/plain");
multipartContent.Add(fileContents, "contents");

var response = await httpClient.PostAsync("spaces/blobs", multipartContent);
```

Végül a [curl](https://curl.haxx.se/) -felhasználók a többrészes űrlapos kérelmeket a következőképpen tehetik meg:

[![Eszköz Blobok](media/how-to-add-blobs/curl-img.png)](media/how-to-add-blobs/curl-img.png#lightbox)

```bash
curl
 -X POST "YOUR_MANAGEMENT_API_URL/spaces/blobs"
 -H "Authorization: Bearer YOUR_TOKEN"
 -H "Accept: application/json"
 -H "Content-Type: multipart/form-data"
 -F "meta={\"ParentId\":\"YOUR_SPACE_ID\",\"Name\":\"My CURL Blob\",\"Type\":\"Map\",\"SubType\":\"GenericMap\",\"Description\":\"A well chosen description\",\"Sharing\":\"None\"};type=application/json"
 -F "text=PATH_TO_FILE;type=text/plain"
```

| Value | Csere erre |
| --- | --- |
| YOUR_TOKEN | Az érvényes OAuth 2,0-token |
| YOUR_SPACE_ID | Annak a helynek az azonosítója, amelyhez a blob társítva van |
| PATH_TO_FILE | A szövegfájl elérési útja |

A sikeres bejegyzés visszaadja az új blob AZONOSÍTÓját (a vörös korábbi verzióban kiemelve).

## <a name="api-endpoints"></a>API-végpontok

A következő szakaszok a Blobokkal kapcsolatos alapvető API-végpontokat és azok funkcióit ismertetik.

### <a name="devices"></a>Eszközök

Blobokat csatolhat az eszközökhöz. Az alábbi képen a felügyeleti API-k felületi dokumentációja látható. Megadja az eszközökhöz kapcsolódó API-végpontokat a Blobok felhasználásához, valamint a szükséges elérésiút-paramétereket.

[![Eszköz Blobok](media/how-to-add-blobs/blobs-device-api-img.png)](media/how-to-add-blobs/blobs-device-api-img.png#lightbox)

Ha például egy blobot szeretne frissíteni vagy létrehozni, és a blobot egy eszközhöz szeretné csatlakoztatni, a következőhöz hitelesített HTTP-javítási kérelmet kell tennie:

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| Paraméter | Csere erre |
| --- | --- |
| *YOUR_BLOB_ID* | A kívánt blob-azonosító |

A sikeres kérések JSON-objektumot adnak vissza a [korábban](#blobModel)leírtaknak megfelelően.

### <a name="spaces"></a>Szóközök

Blobokat is csatolhat a szóközökhöz. Az alábbi képen a Blobok kezeléséért felelős összes szóköz API-végpont szerepel. Emellett felsorolja a végpontokra átadni kívánt elérésiút-paramétereket is.

[![Space Blobok](media/how-to-add-blobs/blobs-space-api-img.png)](media/how-to-add-blobs/blobs-space-api-img.png#lightbox)

Ha például egy tárhelyhez csatolt blobot szeretne visszaadni, akkor a következőre hitelesített HTTP GET kérelmet kell tennie:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| Paraméter | Csere erre |
| --- | --- |
| *YOUR_BLOB_ID* | A kívánt blob-azonosító |

A sikeres kérések JSON-objektumot adnak vissza a [korábban](#blobModel)leírtaknak megfelelően.

Az ugyanahhoz a végponthoz tartozó frissítési kérések metaadat-leírásai és a blob verzióinak létrehozása. A HTTP-kérés a javítási módszeren keresztül történik, valamint a szükséges meta-és többrészes űrlapadatokat is.

### <a name="users"></a>Felhasználók

Blobokat csatolhat felhasználói modellekhez (például egy profilhoz tartozó kép hozzárendeléséhez). Az alábbi képen a releváns felhasználói API-végpontok és a szükséges elérésiút-paraméterek `id`láthatók, például:

[![Felhasználói Blobok](media/how-to-add-blobs/blobs-users-api-img.png)](media/how-to-add-blobs/blobs-users-api-img.png#lightbox)

Ha például egy felhasználóhoz csatolt blobot szeretne beolvasni, hozzon végre egy hitelesített HTTP GET-kérést az összes szükséges űrlapadatokat:

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| Paraméter | Csere erre |
| --- | --- |
| *YOUR_BLOB_ID* | A kívánt blob-azonosító |

A sikeres kérések JSON-objektumot adnak vissza a [korábban](#blobModel)leírtaknak megfelelően.

## <a name="common-errors"></a>Gyakori hibák

Gyakori hiba, hogy nem adja meg a helyes fejléc-információkat:

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

A hiba megoldásához ellenőrizze, hogy a teljes kérelem megfelelő **Content-Type** fejlécet tartalmaz-e:

* `multipart/mixed`
* `multipart/form-data`

Győződjön meg arról is, hogy a többrészes adattömbök esetében szükség van egy megfelelő tartalomtípusra.

## <a name="next-steps"></a>További lépések

- Ha többet szeretne megtudni az Azure Digital Twins-hoz kapcsolódó felvágási dokumentációról, olvassa el az [Azure digitális Twins hencegés](how-to-use-swagger.md)című témakört.

- Blobok a Poster használatával történő feltöltéséhez olvassa el [a Poster konfigurálása](./how-to-configure-postman.md)című témakört.
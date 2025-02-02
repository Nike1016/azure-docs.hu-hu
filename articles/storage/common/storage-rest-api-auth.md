---
title: Azure Storage-szolgáltatások meghívása REST API műveletekkel, beleértve a hitelesítést | Microsoft Docs
description: Azure Storage-szolgáltatások meghívása REST API műveletekkel, beleértve a hitelesítést
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 2149bfb68697129680c45f15c6cce359863fbc59
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989945"
---
# <a name="using-the-azure-storage-rest-api"></a>Az Azure Storage REST API használata

Ez a cikk bemutatja, hogyan használhatja a Blob Storage Service REST API-kat, és hogyan hitelesítheti a szolgáltatás hívását. Egy olyan fejlesztő szemszögéből íródott, aki semmit sem tud a REST-ről, és nincs ötlete, hogyan lehet REST-hívást készíteni. Megvizsgáljuk a REST-hívások dokumentációját, és megtudhatjuk, hogyan lehet lefordítani a tényleges REST-hívásokra – mely mezők hol találhatók? A REST-hívások beállításának megismerése után ezt az ismeretet használhatja a többi Storage szolgáltatás REST API-jának használatára.

## <a name="prerequisites"></a>Előfeltételek 

Az alkalmazás egy Storage-fiók blob Storage tárolóit sorolja fel. A cikkben szereplő kód kipróbálásához a következő elemek szükségesek: 

* Telepítse a [Visual Studio 2019](https://www.visualstudio.com/visual-studio-homepage-vs.aspx) -et a következő számítási feladatokkal:
    - Azure-fejlesztés

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

* Általános célú Storage-fiók. Ha még nem rendelkezik Storage-fiókkal, tekintse meg [a Storage-fiók létrehozása](storage-quickstart-create-account.md)című témakört.

* A cikkben szereplő példa bemutatja, hogyan listázhatja a tárolókat egy Storage-fiókban. Ha meg szeretné tekinteni a kimenetet, vegyen fel néhány tárolót a blob Storage-ba a megkezdése előtt.

## <a name="download-the-sample-application"></a>A mintaalkalmazás letöltése

A minta alkalmazás a-ben írt konzol- C#alkalmazás.

A [git](https://git-scm.com/) használatával töltse le az alkalmazás egy másolatát a fejlesztői környezetbe. 

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-rest-api-with-auth.git
```

Ez a parancs a helyi git mappába klónozza az adattárat. A Visual Studio-megoldás megnyitásához keresse meg a Storage-DotNet-REST-API-with-Auth mappát, nyissa meg, és kattintson duplán a StorageRestApiAuth. SLN elemre. 

## <a name="what-is-rest"></a>Mi a REST?

A REST azt jelenti, hogy a reprezentációs *állapot átvitele*történik. Egy adott definíció esetében tekintse meg a [Wikipédiát](https://en.wikipedia.org/wiki/Representational_state_transfer).

A REST egy olyan architektúra, amely az API-k meghívásakor vagy az API-k elérhetővé tételének meghívásához használható. Függetlenül attól, hogy mi történik mindkét oldalon, és milyen egyéb szoftvereket használ a REST-hívások küldésekor vagy fogadásakor. Olyan alkalmazást írhat, amely Mac, Windows, Linux, Android rendszerű telefonon vagy táblaszámítógépen, iPhone-on, iPod-on vagy webhelyen fut, és ugyanazokat a REST API használhatja az összes platformon. A REST API meghívásakor az adatküldés és/vagy kimenő adatértékek adhatók át. A REST API nem érdekli, hogy milyen platformról van szó – fontos, hogy a kérelemben és a válaszban megadott adatokban milyen információk kerülnek átadásra.

A REST használatának ismerete hasznos képesség. Az Azure-termék csapata gyakran új funkciókat szabadít fel. Sokszor az új funkciók a REST felületen keresztül érhetők el. Előfordulhat azonban, hogy a szolgáltatások nem a Storage ügyféloldali kódtárai vagy a felhasználói felület (például a Azure Portal) szerint vannak felszínen. Ha mindig szeretné használni a legújabb és a legjobb, a tanulási REST követelmény. Továbbá, ha saját könyvtárat szeretne írni az Azure Storage szolgáltatással való kommunikációhoz, vagy ha olyan programozási nyelvvel szeretné elérni az Azure Storage-t, amely nem rendelkezik SDK-vagy Storage-ügyféloldali függvénytárral, használhatja a REST API.

## <a name="about-the-sample-application"></a>Tudnivalók a minta alkalmazásról

A minta alkalmazás a Storage-fiókban található tárolókat sorolja fel. Ha megértette, hogy az REST API dokumentációjában szereplő információk hogyan korrelálnak a tényleges kóddal, a többi REST-hívás könnyebben kideríthető. 

Ha megtekinti a [blob Service REST API](/rest/api/storageservices/Blob-Service-REST-API), megjelenik a blob Storage-on végrehajtható összes művelet. A Storage ügyféloldali kódtárai a REST API-k körét képezik, így egyszerűen hozzáférhet a tárolóhoz anélkül, hogy közvetlenül a REST API-kat kellene használnia. De a fentiekben leírtak szerint időnként a Storage ügyféloldali kódtár helyett a REST API kívánja használni.

## <a name="rest-api-reference-list-containers-api"></a>REST API referenciája: Tárolók listázása API

Nézzük meg az oldalt a [ListContainers](/rest/api/storageservices/List-Containers2) művelethez tartozó REST API-referenciában. Ez az információ segít megérteni, hogy az egyes mezők honnan származnak a kérelemben és a válaszban.

**Kérelem metódusa**: GET. Ez a művelet a kérési objektum tulajdonságként megadott HTTP-metódus. A művelethez tartozó egyéb értékek a meghívott API-tól függően a HEAD, a PUT és a DELETE függvényt is tartalmazzák.

**Kérelem URI-ja**: https://myaccount.blob.core.windows.net/?comp=list Ez a blob Storage-fiók végpontján `http://myaccount.blob.core.windows.net` és az erőforrás-karakterláncon `/?comp=list` jön létre.

[URI-paraméterek](/rest/api/storageservices/List-Containers2#uri-parameters): A ListContainers meghívásakor további lekérdezési paramétereket is használhat. Ezen paraméterek közül néhány időtúllépést okoz a híváshoz (másodpercben) ésaz előtaghoz, amely a szűréshez használatos.

Egy másik hasznos paraméter a *maxresults:* ha több tároló érhető el ennél az értéknél, a válasz törzse egy *NextMarker* elemet fog tartalmazni, amely a következő kérelemre való visszatérés következő tárolóját jelzi. A szolgáltatás használatához adja meg a *NextMarker* értéket az URI *jelölő* paramétereként, amikor a következő kérést hajtja végre. Ha ezt a funkciót használja, az az eredmények közötti lapozáshoz hasonlít. 

Ha további paramétereket szeretne használni, fűzze hozzá azokat az erőforrás-karakterlánchoz az értékkel, az alábbi példához hasonlóan:

```
/?comp=list&timeout=60&maxresults=100
```

[Kérések fejlécei](/rest/api/storageservices/List-Containers2#request-headers) **:** Ez a szakasz felsorolja a szükséges és választható kérelmek fejléceit. Három fejléc szükséges: egy *engedélyezési* fejléc, *x-MS-Date* (tartalmazza a kérelem UTC szerinti időpontját) és az *x-MS-version* (a használni kívánt REST API verzióját adja meg). Az *x-MS-Client-Request-ID* megadása a fejlécekben nem kötelező – ehhez a mezőhöz bármilyen értéket megadhat. a rendszer a Storage Analytics naplófájljaiba írja a naplózás engedélyezésekor.

[Kérelem törzse](/rest/api/storageservices/List-Containers2#request-body) **:** Nincs ListContainers-kérelem törzse. A kérelem törzse az összes PUT művelethez használatos Blobok feltöltésekor, valamint a SetContainerAccessPolicy, amely lehetővé teszi, hogy a rendszer a tárolt hozzáférési szabályzatok XML-listájában küldje el az alkalmazáshoz. A tárolt hozzáférési szabályzatokat a cikk a [közös hozzáférésű aláírások (SAS) használatával](storage-sas-overview.md)tárgyalja.

[Válasz állapotkód](/rest/api/storageservices/List-Containers2#status-code) **:** Minden szükséges állapotkódot megtudhat. Ebben a példában a 200-es HTTP-állapotkód rendben van. A HTTP-állapotkódok teljes listájáért tekintse meg az [állapotkód](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)-definíciókat. A tárolási REST API-kra vonatkozó hibakódok megtekintéséhez lásd: [általános REST API hibakódok](/rest/api/storageservices/common-rest-api-error-codes)

[Válasz fejlécei](/rest/api/storageservices/List-Containers2#response-headers) **:** Ilyenek például a *tartalom típusa*; *x-MS-Request-ID*, amely az átadott kérelem azonosítója; *x-MS-Version*, amely a használt BLOB Service verzióját jelzi; és a *dátum*, amely UTC szerint van megadva, és megadja, hogy a kérelem mikor történt.

[Válasz törzse](/rest/api/storageservices/List-Containers2#response-body): Ez a mező egy XML-struktúra, amely a kért adatmennyiséget biztosítja. Ebben a példában a válasz a tárolók és a tulajdonságaik listája.

## <a name="creating-the-rest-request"></a>A REST-kérelem létrehozása

Néhány megjegyzés megkezdése előtt – az éles környezetben történő futtatás esetén mindig HTTPS protokollt használjon HTTP helyett. Ebben a gyakorlatban a HTTP-t kell használnia, így megtekintheti a kérelmeket és a válaszokat. Ha meg szeretné tekinteni a kérelmek és válaszok adatait a tényleges REST-hívások között, [](https://www.telerik.com/fiddler) akkor letöltheti a hegedűst vagy egy hasonló alkalmazást. A Visual Studio-megoldásban a Storage-fiók neve és kulcsa hardcoded a osztályban. A ListContainersAsyncREST metódus átadja a Storage-fiók nevét és a Storage-fiók kulcsát a REST-kérelem különböző összetevőinek létrehozásához használt metódusokhoz. Egy valós alkalmazásban a Storage-fiók neve és kulcsa egy konfigurációs fájlban, környezeti változóban vagy egy Azure Key Vaultból kérhető le.

A mintául szolgáló projektben az engedélyezési fejléc létrehozására szolgáló kód külön osztályban található. Az elképzelés az, hogy a teljes osztályt felveheti a saját megoldásba, és használhatja azt "ahogy van". Az engedélyezési fejléc kódja az Azure Storage-hoz leginkább REST API hívásokhoz használható.

A kérelem kiépítéséhez, amely egy HttpRequestMessage objektum, nyissa meg a ListContainersAsyncREST a Program.cs-ben. A kérelem összeállításának lépései a következők: 

* Hozza létre a szolgáltatás meghívásához használandó URI-t. 
* Hozza létre a HttpRequestMessage objektumot, és állítsa be a hasznos adatokat. A hasznos adatok a ListContainersAsyncREST esetében null értékűek, mert nem adunk semmit a következőben:.
* Adja hozzá az x-MS-date és az x-MS-Version kérések fejléceit.
* Kérje le az engedélyezési fejlécet, és adja hozzá.

Néhány alapvető információra van szüksége: 

*  A ListContainers esetében a **metódus** `GET`a következő:. Ez az érték a kérelem példányának létrehozásakor van beállítva. 
*  Az **erőforrás** az URI lekérdezési része, amely jelzi, hogy melyik API-t hívják, így az érték `/?comp=list`. Ahogy korábban már említettük, az erőforrás a hivatkozási dokumentáció oldalán található, amely a [LISTCONTAINERS API](/rest/api/storageservices/List-Containers2)információit jeleníti meg.
*  Az URI-t úgy hozza létre, hogy létrehoz egy Blob service végpontot az adott Storage-fiókhoz, és összefűzi az erőforrást. A **kérelem URI** -azonosítójának értéke véget ér `http://contosorest.blob.core.windows.net/?comp=list`.
*  A ListContainers esetében a **requestBody** értéke null, és nincsenek további **fejlécek**.

A különböző API-k más paraméterekkel is rendelkezhetnek, például *ifMatch*. A PutBlob meghívásakor például a ifMatch használatára lehet szükség. Ebben az esetben a ifMatch egy eTag állítja be, és csak akkor frissíti a blobot, ha az Ön által megadott eTag megegyezik a blob aktuális eTag. Ha valaki más frissíti a blobot a eTag beolvasása óta, a módosítások nem lesznek felülbírálva. 

Először állítsa be a `uri` és a `payload`. 

```csharp
// Construct the URI. This will look like this:
//   https://myaccount.blob.core.windows.net/resource
String uri = string.Format("http://{0}.blob.core.windows.net?comp=list", storageAccountName);

// Set this to whatever payload you desire. Ours is null because 
//   we're not passing anything in.
Byte[] requestPayload = null;
```

Ezután hozza létre a kérést, állítsa be a metódust és adja meg az URI-t `GET` .

```csharp 
//Instantiate the request message with a null payload.
using (var httpRequestMessage = new HttpRequestMessage(HttpMethod.Get, uri)
{ Content = (requestPayload == null) ? null : new ByteArrayContent(requestPayload) })
{
```

Adja hozzá az x-MS-date és az x-MS-Version kérések fejléceit. Ezen a helyen a kódban további, a híváshoz szükséges további fejlécek is megadhatók. Ebben a példában nincsenek további fejlécek. Egy példa egy olyan API-ra, amely további fejléceket továbbít a SetContainerACL. A blob Storage esetében egy "x-MS-blob-Public-Access" nevű fejlécet és a hozzáférési szint értékét adja hozzá.

```csharp
    // Add the request headers for x-ms-date and x-ms-version.
    DateTime now = DateTime.UtcNow;
    httpRequestMessage.Headers.Add("x-ms-date", now.ToString("R", CultureInfo.InvariantCulture));
    httpRequestMessage.Headers.Add("x-ms-version", "2017-07-29");
    // If you need any additional headers, add them here before creating
    //   the authorization header. 
```

Hívja meg az engedélyezési fejlécet létrehozó metódust, és adja hozzá a kérések fejlécéhez. Megtudhatja, hogyan hozhatja létre az engedélyezési fejlécet a cikk későbbi részében. A metódus neve GetAuthorizationHeader, amelyet ebben a kódrészletben láthat:

```csharp
    // Get the authorization header and add it.
    httpRequestMessage.Headers.Authorization = AzureStorageAuthenticationHelper.GetAuthorizationHeader(
        storageAccountName, storageAccountKey, now, httpRequestMessage);
```

Ezen a ponton a `httpRequestMessage` Rest-kérelem az engedélyezési fejlécekkel fejeződött be. 

## <a name="call-the-rest-api-with-the-request"></a>Az REST API meghívása a kérelemmel

Most, hogy megkapta a kérést, meghívhatja a SendAsync a REST-kérelem elküldéséhez. A SendAsync meghívja az API-t, és visszaküldi a választ. Vizsgálja meg a válasz StatusCode (200 OK), majd elemezze a választ. Ebben az esetben a tárolók XML-listáját kapja meg. Nézzük meg a GetRESTRequest metódus meghívásához szükséges kódot a kérelem létrehozásához, a kérelem végrehajtásához, majd a tárolók listájának megválaszolásához.

```csharp 
    // Send the request.
    using (HttpResponseMessage httpResponseMessage = 
      await new HttpClient().SendAsync(httpRequestMessage, cancellationToken))
    {
        // If successful (status code = 200), 
        //   parse the XML response for the container names.
        if (httpResponseMessage.StatusCode == HttpStatusCode.OK)
        {
            String xmlString = await httpResponseMessage.Content.ReadAsStringAsync();
            XElement x = XElement.Parse(xmlString);
            foreach (XElement container in x.Element("Containers").Elements("Container"))
            {
                Console.WriteLine("Container name = {0}", container.Element("Name").Value);
            }
        }
    }
}
```

Ha a SendAsync meghívásakor egy hálózati Szippantó (például [Hegedűs](https://www.telerik.com/fiddler) ) fut, akkor a kérelem és a válasz információi láthatók. Vessünk egy kinézetet. A Storage-fiók neve *contosorest*.

**Kérelem**

```
GET /?comp=list HTTP/1.1
```

**Kérelem fejlécei:**

```
x-ms-date: Thu, 16 Nov 2017 23:34:04 GMT
x-ms-version: 2014-02-14
Authorization: SharedKey contosorest:1dVlYJWWJAOSHTCPGiwdX1rOS8B4fenYP/VrU0LfzQk=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**A végrehajtás után visszaadott állapotkód és válasz fejlécek:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 3e889876-001e-0039-6a3a-5f4396000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 00:23:42 GMT
Content-Length: 1511
```

**Válasz törzse (XML):** A ListContainers a tárolók és tulajdonságaik listáját jeleníti meg.

```xml  
<?xml version="1.0" encoding="utf-8"?>
<EnumerationResults 
  ServiceEndpoint="http://contosorest.blob.core.windows.net/">
  <Containers>
    <Container>
      <Name>container-1</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:39:48 GMT</Last-Modified>
        <Etag>"0x8D46CBD5A7C301D"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-2</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:40:50 GMT</Last-Modified>
        <Etag>"0x8D46CBD7F49E9BD"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-3</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:41:10 GMT</Last-Modified>
        <Etag>"0x8D46CBD8B243D68"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-4</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:41:25 GMT</Last-Modified>
        <Etag>"0x8D46CBD93FED46F"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
        </Properties>
      </Container>
      <Container>
        <Name>container-5</Name>
        <Properties>
          <Last-Modified>Thu, 16 Mar 2017 22:41:39 GMT</Last-Modified>
          <Etag>"0x8D46CBD9C762815"</Etag>
          <LeaseStatus>unlocked</LeaseStatus>
          <LeaseState>available</LeaseState>
        </Properties>
      </Container>
  </Containers>
  <NextMarker />
</EnumerationResults>
```

Most, hogy megértette, hogyan hozza létre a kérést, hívja meg a szolgáltatást, és elemezze az eredményeket, lássuk, hogyan hozhatja létre az engedélyezési fejlécet. A fejléc létrehozása bonyolult, de a jó hír az, hogy a kód működésének megkezdése után az összes tárolási szolgáltatás REST API-jának megfelelően működik.

## <a name="creating-the-authorization-header"></a>Az engedélyezési fejléc létrehozása

> [!TIP]
> Az Azure Storage mostantól támogatja a blobok és a várólisták Azure Active Directory (Azure AD) integrációját. Az Azure AD sokkal egyszerűbben használható az Azure Storage-ba irányuló kérések engedélyezéséhez. Az Azure AD a REST-műveletek engedélyezéséhez való használatáról további információt a [hitelesítés a Azure Active Directory](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory)használatával című témakörben talál. Az Azure AD-integráció Azure Storage-nal való áttekintését lásd: az [Azure Storage hozzáférésének hitelesítése Azure Active Directory használatával](storage-auth-aad.md).

Az [Azure Storage-szolgáltatások hitelesítésének](/rest/api/storageservices/Authorization-for-the-Azure-Storage-Services)elvégzésére szolgáló cikk fogalmilag (nincs kód) ismerteti.
Nézzük meg, hogy a cikk pontosan szükség van-e, és mutassa be a kódot.

Először használjon megosztott kulcsos hitelesítést. Az engedélyezési fejléc formátuma a következőképpen néz ki:

```  
Authorization="SharedKey <storage account name>:<signature>"  
```

Az aláírás mező a kérelemből létrehozott kivonat-alapú üzenethitelesítő kód (HMAC), amely a SHA256 algoritmus használatával lett kiszámítva, majd Base64 kódolással kódolva. Megkapta ezt? (Itt lefagyhat, még nem is hallotta a *kanonikus* szót.)

Ez a kódrészlet a megosztott kulcs aláírási karakterláncának formátumát mutatja:

```csharp  
StringToSign = VERB + "\n" +  
               Content-Encoding + "\n" +  
               Content-Language + "\n" +  
               Content-Length + "\n" +  
               Content-MD5 + "\n" +  
               Content-Type + "\n" +  
               Date + "\n" +  
               If-Modified-Since + "\n" +  
               If-Match + "\n" +  
               If-None-Match + "\n" +  
               If-Unmodified-Since + "\n" +  
               Range + "\n" +  
               CanonicalizedHeaders +  
               CanonicalizedResource;  
```

A mezők többsége ritkán használatos. BLOB Storage esetén a művelet, az MD5, a tartalom hossza, a kanonikus fejlécek és a kanonikus erőforrások megadása szükséges. A többi mezőt üresen hagyhatja (de `\n` azt is megteheti, hogy üresek).

Mi a CanonicalizedHeaders és a CanonicalizedResource? Jó kérdés. Valójában mit jelent a kanonikus? A Microsoft Word még nem ismeri fel szót. [A wikipedia a szabványosításról szól](https://en.wikipedia.org/wiki/Canonicalization): *A számítógép-tudományban a szabványosítás (esetenként a szabványosítás vagy a normalizálás) egy olyan folyamat, amely egynél több lehetséges ábrázolást tartalmaz a "standard", a "normál" vagy a Canonical Form értékekre.* Normális – beszéd esetén ez azt jelenti, hogy az elemek listáját (például a fejléceket a kanonikus fejlécek esetén) kell megtenni, és szabványosítani kell őket egy szükséges formátumba. A Microsoft alapvetően úgy döntött, hogy formátumot használ, és meg kell egyeznie.

Kezdjük a két kanonikus mezővel, mert az engedélyezési fejléc létrehozásához szükségesek.

**Kanonikus fejlécek**

Az érték létrehozásához kérje le azokat a fejléceket, amelyek az "x-MS-" karakterlánccal `[key:value\n]` kezdődnek, rendezze őket, majd formázza őket egy sztringbe, és összefűzi őket egy karakterlánccá. Ebben a példában a kanonikus fejlécek így néznek ki: 

```
x-ms-date:Fri, 17 Nov 2017 00:44:48 GMT\nx-ms-version:2017-07-29\n
```

Itt látható a kimenet létrehozásához használt kód:

```csharp 
private static string GetCanonicalizedHeaders(HttpRequestMessage httpRequestMessage)
{
    var headers = from kvp in httpRequestMessage.Headers
        where kvp.Key.StartsWith("x-ms-", StringComparison.OrdinalIgnoreCase)
        orderby kvp.Key
        select new { Key = kvp.Key.ToLowerInvariant(), kvp.Value };

    StringBuilder headersBuilder = new StringBuilder();

    // Create the string in the right format; this is what makes the headers "canonicalized" --
    //   it means put in a standard format. https://en.wikipedia.org/wiki/Canonicalization
    foreach (var kvp in headers)
    {
        headersBuilder.Append(kvp.Key);
        char separator = ':';

        // Get the value for each header, strip out \r\n if found, then append it with the key.
        foreach (string headerValue in kvp.Value)
        {
            string trimmedValue = headerValue.TrimStart().Replace("\r\n", string.Empty);
            headersBuilder.Append(separator).Append(trimmedValue);

            // Set this to a comma; this will only be used
            // if there are multiple values for one of the headers.
            separator = ',';
        }

        headersBuilder.Append("\n");
    }

    return headersBuilder.ToString();
}
```

**Kanonikus erőforrás**

Az aláírási karakterlánc ezen része a kérelem által megadott tárolási fiókot jelöli. Ne feledje `<http://contosorest.blob.core.windows.net/?comp=list>`, hogy a kérelem URI-ja a tényleges fióknév (`contosorest` ebben az esetben). Ebben a példában a rendszer a következőt adja vissza:

```
/contosorest/\ncomp:list
```

Ha lekérdezési paraméterekkel rendelkezik, akkor a példa ezeket a paramétereket is tartalmazza. Itt látható a kód, amely további lekérdezési paramétereket és lekérdezési paramétereket is kezel több értékkel. Ne feledje, hogy ezt a kódot a REST API-k működéséhez kell felépíteni. Az összes lehetőséget fel szeretné venni, még akkor is, ha a ListContainers metódusnak nincs szüksége rájuk.

```csharp 
private static string GetCanonicalizedResource(Uri address, string storageAccountName)
{
    // The absolute path will be "/" because for we're getting a list of containers.
    StringBuilder sb = new StringBuilder("/").Append(storageAccountName).Append(address.AbsolutePath);

    // Address.Query is the resource, such as "?comp=list".
    // This ends up with a NameValueCollection with 1 entry having key=comp, value=list.
    // It will have more entries if you have more query parameters.
    NameValueCollection values = HttpUtility.ParseQueryString(address.Query);

    foreach (var item in values.AllKeys.OrderBy(k => k))
    {
        sb.Append('\n').Append(item).Append(':').Append(values[item]);
    }

    return sb.ToString();
}
```

Most, hogy a kanonikus karakterláncok be vannak állítva, nézzük meg, hogyan hozhatja létre maga az engedélyezési fejlécet. Első lépésként hozzon létre egy karakterláncot az üzenet aláírásában a cikkben korábban megjelenő StringToSign formátumban. Ez a koncepció könnyebben elmagyarázható a kódban szereplő megjegyzések használatával, ezért itt az a végső módszer, amely visszaadja az engedélyezési fejlécet:

```csharp
internal static AuthenticationHeaderValue GetAuthorizationHeader(
    string storageAccountName, string storageAccountKey, DateTime now,
    HttpRequestMessage httpRequestMessage, string ifMatch = "", string md5 = "")
{
    // This is the raw representation of the message signature.
    HttpMethod method = httpRequestMessage.Method;
    String MessageSignature = String.Format("{0}\n\n\n{1}\n{5}\n\n\n\n{2}\n\n\n\n{3}{4}",
                method.ToString(),
                (method == HttpMethod.Get || method == HttpMethod.Head) ? String.Empty
                  : httpRequestMessage.Content.Headers.ContentLength.ToString(),
                ifMatch,
                GetCanonicalizedHeaders(httpRequestMessage),
                GetCanonicalizedResource(httpRequestMessage.RequestUri, storageAccountName),
                md5);

    // Now turn it into a byte array.
    byte[] SignatureBytes = Encoding.UTF8.GetBytes(MessageSignature);

    // Create the HMACSHA256 version of the storage key.
    HMACSHA256 SHA256 = new HMACSHA256(Convert.FromBase64String(storageAccountKey));

    // Compute the hash of the SignatureBytes and convert it to a base64 string.
    string signature = Convert.ToBase64String(SHA256.ComputeHash(SignatureBytes));

    // This is the actual header that will be added to the list of request headers.
    AuthenticationHeaderValue authHV = new AuthenticationHeaderValue("SharedKey",
        storageAccountName + ":" + Convert.ToBase64String(SHA256.ComputeHash(SignatureBytes)));
    return authHV;
}
```

A kód futtatásakor az eredményül kapott MessageSignature a következő példához hasonlóan néz ki:

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 01:07:37 GMT\nx-ms-version:2017-07-29\n/contosorest/\ncomp:list
```

A AuthorizationHeader végső értéke:

```
SharedKey contosorest:Ms5sfwkA8nqTRw7Uury4MPHqM6Rj2nfgbYNvUKOa67w=
```

A AuthorizationHeader a kérelem fejlécében elhelyezett utolsó fejléc a válasz elküldése előtt.

Ez magában foglalja mindazt, amit tudnia kell, hogy összekapcsoljon egy olyan osztályt, amellyel a Storage szolgáltatások REST API-jának hívására vonatkozó kérést hozhat létre.

## <a name="how-about-another-example"></a>Mi a helyzet egy másik példával? 

Nézzük meg, hogyan változtathatja meg a ListBlobs meghívására szolgáló kódot a Container *-1*tárolóban. Ez a kód majdnem azonos a tárolók listázásához szükséges kóddal, az egyetlen különbség, hogy az URI és hogyan elemezi a választ. 

Ha megtekinti a [ListBlobs](/rest/api/storageservices/List-Blobs)dokumentációját, a metódus a *Get* és a RequestURI:

```
https://myaccount.blob.core.windows.net/container-1?restype=container&comp=list
```

A ListContainersAsyncREST-ben módosítsa az URI-t a ListBlobs API-ra beállító kódot. A tároló neve a **Container-1**.

```csharp
String uri = 
    string.Format("http://{0}.blob.core.windows.net/container-1?restype=container&comp=list",
      storageAccountName);

```

Ezután a válasz kezeléséhez módosítsa a kódot úgy, hogy a tárolók helyett blobokat keressen.

```csharp
foreach (XElement container in x.Element("Blobs").Elements("Blob"))
{
    Console.WriteLine("Blob name = {0}", container.Element("Name").Value);
}
```

A minta futtatásakor a következőhöz hasonló eredményt kap:

**Kanonikus fejlécek:**

```
x-ms-date:Fri, 17 Nov 2017 05:16:48 GMT\nx-ms-version:2017-07-29\n
```

**Kanonikus erőforrás:**

```
/contosorest/container-1\ncomp:list\nrestype:container
```

**MessageSignature:**

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 05:16:48 GMT
  \nx-ms-version:2017-07-29\n/contosorest/container-1\ncomp:list\nrestype:container
```

**AuthorizationHeader:**

```
SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
```

A következő értékek a [hegedűstől](https://www.telerik.com/fiddler)származnak:

**Kérelem**

```
GET http://contosorest.blob.core.windows.net/container-1?restype=container&comp=list HTTP/1.1
```

**Kérelem fejlécei:**

```
x-ms-date: Fri, 17 Nov 2017 05:16:48 GMT
x-ms-version: 2017-07-29
Authorization: SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**A végrehajtás után visszaadott állapotkód és válasz fejlécek:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 7e9316da-001e-0037-4063-5faf9d000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 05:20:21 GMT
Content-Length: 1135
```

**Válasz törzse (XML):** Ez az XML-válasz a blobok és azok tulajdonságainak listáját jeleníti meg. 

```xml
<?xml version="1.0" encoding="utf-8"?>
<EnumerationResults 
    ServiceEndpoint="http://contosorest.blob.core.windows.net/" ContainerName="container-1">
    <Blobs>
        <Blob>
            <Name>DogInCatTree.png</Name>
            <Properties><Last-Modified>Fri, 17 Nov 2017 01:41:14 GMT</Last-Modified>
            <Etag>0x8D52D5C4A4C96B0</Etag>
            <Content-Length>419416</Content-Length>
            <Content-Type>image/png</Content-Type>
            <Content-Encoding />
            <Content-Language />
            <Content-MD5 />
            <Cache-Control />
            <Content-Disposition />
            <BlobType>BlockBlob</BlobType>
            <LeaseStatus>unlocked</LeaseStatus>
            <LeaseState>available</LeaseState>
            <ServerEncrypted>true</ServerEncrypted>
            </Properties>
        </Blob>
        <Blob>
            <Name>GuyEyeingOreos.png</Name>
            <Properties>
                <Last-Modified>Fri, 17 Nov 2017 01:41:14 GMT</Last-Modified>
                <Etag>0x8D52D5C4A25A6F6</Etag>
                <Content-Length>167464</Content-Length>
                <Content-Type>image/png</Content-Type>
                <Content-Encoding />
                <Content-Language />
                <Content-MD5 />
                <Cache-Control />
                <Content-Disposition />
                <BlobType>BlockBlob</BlobType>
                <LeaseStatus>unlocked</LeaseStatus>
                <LeaseState>available</LeaseState>
                <ServerEncrypted>true</ServerEncrypted>
            </Properties>
            </Blob>
        </Blobs>
    <NextMarker />
</EnumerationResults>
```

## <a name="summary"></a>Összegzés

Ebből a cikkből megtudhatta, hogyan lehet kérelmet készíteni a blob Storage-REST API. A kérelem segítségével lekérheti a tárolók listáját vagy a Blobok listáját. Megtanulta, hogyan hozhatja létre az engedélyezési aláírást a REST API híváshoz, és hogyan használhatja azt a REST-kérelemben. Végezetül megtanulta, hogyan vizsgálhatja meg a választ.

## <a name="next-steps"></a>További lépések

* [Blob Service REST API](/rest/api/storageservices/blob-service-rest-api)
* [Fájlszolgáltatások REST API](/rest/api/storageservices/file-service-rest-api)
* [Üzenetsor-szolgáltatás REST API](/rest/api/storageservices/queue-service-rest-api)

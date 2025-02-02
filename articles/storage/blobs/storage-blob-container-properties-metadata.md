---
title: BLOB-tároló tulajdonságainak és metaadatainak kezelése a .NET-Azure Storage használatával
description: Megtudhatja, hogyan állíthatja be és kérheti le a rendszertulajdonságokat, és hogyan tárolhat egyéni metaadatokat a blob-tárolókban az Azure Storage-fiókban a .NET ügyféloldali kódtár használatával
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/10/2019
ms.author: tamram
ms.openlocfilehash: 35883b0c34503ca16f2b10fc061f94af514e10a9
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68986913"
---
# <a name="manage-container-properties-and-metadata-with-net"></a>Tároló tulajdonságainak és metaadatainak kezelése a .NET-tel

A blob-tárolók támogatják a rendszertulajdonságokat és a felhasználó által definiált metaadatokat az általuk tartalmazott adatokon kívül. Ez a cikk bemutatja, hogyan kezelheti a rendszertulajdonságokat és a felhasználó által definiált metaadatokat az [Azure Storage .net-hez készült ügyféloldali kódtár](/dotnet/api/overview/azure/storage/client)szolgáltatásával.

## <a name="about-properties-and-metadata"></a>A tulajdonságok és a metaadatok

- Rendszertulajdonságok: A rendszer tulajdonságai az egyes blob Storage-erőforrásokon léteznek. Némelyikük olvasható vagy beállítható, míg mások csak olvashatók. A borítók alatt egyes Rendszertulajdonságok megfelelnek bizonyos szabványos HTTP-fejléceknek. Az Azure Storage .NET-hez készült ügyféloldali kódtára ezeket a tulajdonságokat tárolja.

- **Felhasználó által definiált metaadatok**: A felhasználó által definiált metaadatok egy vagy több, a blob Storage-erőforráshoz megadott név-érték párokból állnak. A metaadatok használatával további értékeket is tárolhat az erőforrással. A metaadatok értéke csak saját célra szolgál, és nem befolyásolja az erőforrás működésének módját.

A blob Storage-erőforrások tulajdonság-és metaadat-értékeinek beolvasása kétlépéses folyamat. Ezeknek az értékeknek a beolvasása előtt explicit módon be kell olvasnia azokat a **FetchAttributes** vagy a **FetchAttributesAsync** metódus meghívásával. A szabály alól kivételt képez, hogy a **létező** és a **ExistsAsync** metódusok a borító alatt meghívja a megfelelő **FetchAttributes** metódust. Ha meghívja az egyik módszert, nem kell meghívnia a **FetchAttributes**.

> [!IMPORTANT]
> Ha azt tapasztalja, hogy a tárolási erőforráshoz tartozó tulajdonság vagy metaadatok értéke nem lett feltöltve, ellenőrizze, hogy a kód meghívja-e a **FetchAttributes** vagy a **FetchAttributesAsync** metódust.

A metaadatok neve/értéke párok érvényes HTTP-fejlécek, ezért meg kell felelniük a HTTP-fejléceket szabályozó összes korlátozásnak. A metaadatok nevének érvényesnek kell lennie a HTTP C# -fejlécek és az érvényes azonosítók számára, csak ASCII-karaktereket tartalmazhat, és a kis-és nagybetűket nem megkülönböztetőként kell kezelni. A nem ASCII karaktereket tartalmazó metaadat-értékeknek Base64 kódolású vagy URL-kódolású kell lenniük.

## <a name="retrieve-container-properties"></a>Tároló tulajdonságainak lekérése

A tároló tulajdonságainak lekéréséhez hívja a következő módszerek egyikét:

- [FetchAttributes](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributes)
- [FetchAttributesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributesasync)

A következő mintakód egy tároló rendszertulajdonságait olvassa be, és bizonyos tulajdonságértékeket ír egy konzolablakbe:

```csharp
private static async Task ReadContainerPropertiesAsync(CloudBlobContainer container)
{
    try
    {
        // Fetch some container properties and write out their values.
        await container.FetchAttributesAsync();
        Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri);
        Console.WriteLine("Public access level: {0}", container.Properties.PublicAccess);
        Console.WriteLine("Last modified time in UTC: {0}", container.Properties.LastModified);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

## <a name="set-and-retrieve-metadata"></a>Metaadatok beállítása és lekérése

A metaadatokat egy vagy több név-érték párokkal is megadhatja blob vagy tároló erőforráson. A metaadatok beállításához adja hozzá a név-érték párokat az erőforrás **metaadat** -gyűjteményéhez, majd hívja meg az alábbi módszerek egyikét az értékek írásához:

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadataasync)

A metaadatok nevének meg kell felelnie az C# azonosítók elnevezési konvencióinak. A metaadatok nevei megőrzik azt az esetet, amellyel létrehozták őket, de a kis-és nagybetűk nem különböznek a beállítás vagy az olvasás során. Ha két vagy több, azonos nevű metaadat-fejlécet küld egy erőforráshoz, a blob Storage a 400-as HTTP-hibakódot (hibás kérés) adja vissza.

A következő kódrészlet egy tároló metaadatait állítja be. Egy érték van beállítva a gyűjtemény hozzáadási metódusának használatával. A másik érték az implicit kulcs/érték szintaxis használatával van beállítva. Mindkettő érvényes.

```csharp
public static async Task AddContainerMetadataAsync(CloudBlobContainer container)
{
    try
    {
        // Add some metadata to the container.
        container.Metadata.Add("docType", "textDocuments");
        container.Metadata["category"] = "guidance";

        // Set the container's metadata.
        await container.SetMetadataAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

A metaadatok lekéréséhez hívja meg a **FetchAttributes** vagy a **FetchAttributesAsync** metódust a blobon vagy a tárolón a **metaadatok** gyűjteményének feltöltéséhez, majd olvassa el az értékeket az alábbi példában látható módon.

```csharp
public static async Task ReadContainerMetadataAsync(CloudBlobContainer container)
{
    try
    {
        // Fetch container attributes in order to populate the container's properties and metadata.
        await container.FetchAttributesAsync();

        // Enumerate the container's metadata.
        Console.WriteLine("Container metadata:");
        foreach (var metadataItem in container.Metadata)
        {
            Console.WriteLine("\tKey: {0}", metadataItem.Key);
            Console.WriteLine("\tValue: {0}", metadataItem.Value);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Lásd még

- [Tároló tulajdonságainak beolvasása művelet](/rest/api/storageservices/get-container-properties)
- [Tároló metaadatainak beállítása művelet](/rest/api/storageservices/set-container-metadata)
- [Tároló metaadatainak beolvasása művelet](/rest/api/storageservices/set-container-metadata)

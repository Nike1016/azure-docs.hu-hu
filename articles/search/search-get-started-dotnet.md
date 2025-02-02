---
title: C#QuickStart Indexek létrehozása, betöltése és lekérdezése a .NET SDK használatával – Azure Search
description: Ismerteti, hogyan hozhat létre indexet, tölthet be és hogyan futtathat lekérdezéseket a és a Azure Search .NET SDK használatával C# .
author: heidisteen
manager: cgronlun
ms.author: heidist
tags: azure-portal
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/11/2019
ms.openlocfilehash: 6138df5b80f479a54683ec0408b832dd78bff8e4
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67847080"
---
# <a name="quickstart-create-an-azure-search-index-in-c-using-the-net-sdk"></a>Gyors útmutató: Azure Search index létrehozása a .NET C# SDK használatával
> [!div class="op_single_selector"]
> * [C#](search-get-started-dotnet.md)
> * [Portál](search-get-started-portal.md)
> * [PowerShell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)
>*

Hozzon létre egy C# olyan .net Core Console-alkalmazást, amely egy Azure Search indexet hoz létre, tölt be és kérdez le a Visual Studio és a [Azure Search .net SDK](https://aka.ms/search-sdk)használatával. Ez a cikk azt ismerteti, hogyan hozható létre az alkalmazás lépésről lépésre. Azt is megteheti, hogy [letölti és futtatja a teljes alkalmazást](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/Quickstart).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

> [!NOTE]
> A jelen cikkben szereplő bemutató kód a Azure Search .NET SDK egyszerűségének szinkron módszereit használja. Éles környezetekben azonban azt javasoljuk, hogy a saját alkalmazásaiban lévő aszinkron metódusok használatával méretezhető és rugalmas legyen. Használhatja `CreateAsync` például a és `DeleteAsync` a és a helyett a `Create` és `Delete`a értéket.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következő szolgáltatások és eszközök szükségesek.

+ A [Visual Studio](https://visualstudio.microsoft.com/downloads/)bármely kiadása. A mintakód és az utasítások tesztelése az ingyenes közösségi kiadásban történt.

+ [Hozzon létre egy Azure Search szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. Ehhez a rövid útmutatóhoz ingyenes szolgáltatást is használhat.

<a name="get-service-info"></a>

## <a name="get-a-key-and-url"></a>Kulcs és URL-cím lekérése

A szolgáltatás felé irányuló hívások URL-végpontot és hozzáférési kulcsot igényelnek minden kérelemben. Mindkettőhöz létrejön egy keresési szolgáltatás, így ha hozzáadta az előfizetéséhez az Azure Searchöt, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/), és a keresési szolgáltatás **Áttekintés** lapján töltse le az URL-címet. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

2. A **Beállítások** > **kulcsaiban**kérjen meg egy rendszergazdai kulcsot a szolgáltatásra vonatkozó összes jogosultsághoz. Az üzletmenet folytonossága érdekében két, egymással megváltoztathatatlan rendszergazdai kulcs áll rendelkezésre. Az objektumok hozzáadására, módosítására és törlésére vonatkozó kérésekhez használhatja az elsődleges vagy a másodlagos kulcsot is.

   Kérje le a lekérdezési kulcsot is. Ajánlott a lekérdezési kérelmeket csak olvasási hozzáféréssel kibocsátani.

![Http-végpont és elérési kulcs] beszerzése (media/search-get-started-postman/get-url-key.png "Http-végpont és elérési kulcs") beszerzése

Minden kérelemhez API-kulcs szükséges a szolgáltatásnak küldött összes kéréshez. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="set-up-your-environment"></a>A környezet kialakítása

Először nyissa meg a Visual studiót, és hozzon létre egy új, a .NET Core-on futtatható Console app-projektet.

### <a name="install-nuget-packages"></a>NuGet-csomagok telepítése

A [Azure Search .net SDK](https://aka.ms/search-sdk) néhány, NuGet-csomagként terjesztett ügyféloldali kódtárat tartalmaz.

Ehhez a projekthez használja a `Microsoft.Azure.Search` NuGet csomag 9-es verzióját és a legújabb `Microsoft.Extensions.Configuration.Json` NuGet-csomagot.

1. Az **eszközök** > **NuGet csomagkezelő**területén válassza a **NuGet-csomagok kezelése megoldást..** . lehetőséget. 

1. Kattintson a **Browse** (Tallózás) gombra.

1. Keresse meg `Microsoft.Azure.Search` és válassza ki a 9.0.1 vagy újabb verziót.

1. A jobb oldalon kattintson a **telepítés** gombra a szerelvény projekthez és megoldáshoz való hozzáadásához.

1. Ismételje `Microsoft.Extensions.Configuration.Json`meg a beállítást, és válassza a 2.2.0 vagy újabb verziót.


### <a name="add-azure-search-service-information"></a>Azure Search szolgáltatás adatainak hozzáadása

1. Megoldáskezelő kattintson a jobb gombbal a projektre, és ****  > válassza az**új elem hozzáadása..** . lehetőséget. 

1. Az új elem hozzáadása lapon keressen rá a "JSON" kifejezésre, és adja vissza a JSON-hez kapcsolódó elemek listáját.

1. Válassza a **JSON-fájl**nevet, nevezze el a "appSettings. JSON" fájlt, majd kattintson a **Hozzáadás**gombra. 

1. Adja hozzá a fájlt a kimeneti könyvtárhoz. Kattintson a jobb gombbal a appSettings. JSON fájlra, és válassza a **Tulajdonságok**lehetőséget. A **Másolás kimeneti könyvtárba**lapon válassza a **Másolás, ha újabb**lehetőséget.

1. Másolja a következő JSON-t az új JSON-fájlba. Cserélje le a keresési szolgáltatás nevét (a-SEARCH-SERVICE-NAME) és a felügyeleti API-kulcsot (a-ADMIN-API-KEY) érvényes értékekkel. Ha a szolgáltatás végpontja `https://mydemo.search.windows.net`, a szolgáltatás neve "mydemo" lesz.

```json
{
  "SearchServiceName": "<YOUR-SEARCH-SERVICE-NAME>",
  "SearchServiceAdminApiKey": "<YOUR-ADMIN-API-KEY>",
  "SearchIndexName": "hotels-quickstart"
}
```

### <a name="add-class-method-files-to-your-project"></a>Osztály hozzáadása ". Metódus "fájlok a projekthez

Amikor kinyomtatja az eredményeket a konzol ablakába, a Hotel objektum egyedi mezőinek karakterláncként kell szerepelnie. A [ToString ()](https://docs.microsoft.com/dotnet/api/system.object.tostring?view=netframework-4.8) használatával végrehajthatja ezt a feladatot, és a szükséges kódot két új fájlba másolhatja.

1. Adjon hozzá két üres osztály-definíciót a projekthez: Address.Methods.cs, Hotel.Methods.cs

1. A Address.Methods.cs írja felül az alapértelmezett tartalmakat a következő kóddal, a [1-32](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Address.Methods.cs/#L1-L32)-es sorokkal.

1. A Hotel.Methods.cs-ben másolja a [1-66 sorokat](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Hotel.Methods.cs/#L1-L66).


## <a name="1---create-index"></a>1 – index létrehozása

A Hotels index egyszerű és összetett mezőket tartalmaz, ahol egy egyszerű mező a "pezsgő" vagy a "Description", az összetett mezők pedig almezőket tartalmazó címek vagy szobák gyűjteménye. Ha egy index összetett típusokat tartalmaz, különítse el az összetett mezők definícióit külön osztályokban.

1. Adjon hozzá két üres osztály-definíciót a projekthez: Address.cs, Hotel.cs

1. A Address.cs írja felül az alapértelmezett tartalmakat a következő kóddal:

    ```csharp
    using System;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Newtonsoft.Json;

    namespace AzureSearchQuickstart
    {
        public partial class Address
        {
            [IsSearchable]
            public string StreetAddress { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string City { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string StateProvince { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string PostalCode { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Country { get; set; }
        }
    }
    ```

1. A Hotel.cs-ben az osztály az index általános szerkezetét határozza meg, beleértve a címek osztályára mutató hivatkozásokat is.

    ```csharp
    namespace AzureSearchQuickstart
    {
        using System;
        using Microsoft.Azure.Search;
        using Microsoft.Azure.Search.Models;
        using Newtonsoft.Json;

        public partial class Hotel
        {
            [System.ComponentModel.DataAnnotations.Key]
            [IsFilterable]
            public string HotelId { get; set; }

            [IsSearchable, IsSortable]
            public string HotelName { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
            public string Description { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.FrLucene)]
            [JsonProperty("Description_fr")]
            public string DescriptionFr { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Category { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string[] Tags { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public bool? ParkingIncluded { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public double? Rating { get; set; }

            public Address Address { get; set; }
        }
    }
    ```

    A mező attribútumai határozzák meg, hogyan használják az alkalmazásokban. Az `IsSearchable` attribútumot például minden olyan mezőhöz hozzá kell rendelni, amelynek szerepelnie kell egy teljes szöveges keresésben. 
    
    > [!NOTE]
    > A .net SDK-ban [`IsSearchable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.issearchable?view=azure-dotnet)a mezőket explicit módon [`IsFilterable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet) [`IsSortable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.issortable?view=azure-dotnet)kell megadni,, és [`IsFacetable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfacetable?view=azure-dotnet). Ez a viselkedés ellentétben áll azzal a REST APItel, amely implicit módon engedélyezi a kiosztást az adattípusok alapján (például az egyszerű karakterlánc mezők automatikusan kereshetők).

    Az indexben `string` pontosan egy mezőnek kell lennie az *egyes* dokumentumok egyedi azonosítására szolgáló kulcsmező. Ebben a sémában a kulcs `HotelId`a következő:.

    Ebben az indexben a Description (Leírás [`analyzer`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.analyzer?view=azure-dotnet) ) mezők a választható tulajdonságot használják, ha felül szeretné írni az alapértelmezett standard Lucene-elemzőt. A `description_fr` mező a francia Lucene Analyzert ([FrLucene](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.frlucene?view=azure-dotnet)) használja, mert francia nyelvű szöveget tárol. A `description` a választható Microsoft nyelvi analizátort ([EnMicrosoft](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.enmicrosoft?view=azure-dotnet)) használja.

1. A program.cs-ben hozza létre az [`SearchServiceClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient?view=azure-dotnet) osztály egy példányát a szolgáltatáshoz való kapcsolódáshoz az alkalmazás konfigurációs fájljában (appSettings. JSON) tárolt értékek alapján. 

   `SearchServiceClient`rendelkezik egy [`Indexes`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexes?view=azure-dotnet) tulajdonsággal, amely a Azure Search indexek létrehozásához, listázásához, frissítéséhez vagy törléséhez szükséges összes módszert biztosítja. 

    ```csharp
    using System;
    using System.Linq;
    using System.Threading;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Extensions.Configuration;

    namespace AzureSearchQuickstart
    {
        class Program
            // Demonstrates index delete, create, load, and query
            // Commented-out code is uncommented in later steps
            static void Main(string[] args)
            {
                IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
                IConfigurationRoot configuration = builder.Build();

                SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

                string indexName = configuration["SearchIndexName"];

                Console.WriteLine("{0}", "Deleting index...\n");
                DeleteIndexIfExists(indexName, serviceClient);

                Console.WriteLine("{0}", "Creating index...\n");
                CreateIndex(indexName, serviceClient);

                // Uncomment next 3 lines in "2 - Load documents"
                // ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
                // Console.WriteLine("{0}", "Uploading documents...\n");
                // UploadDocuments(indexClient);

                // Uncomment next 2 lines in "3 - Search an index"
                // Console.WriteLine("{0}", "Searching index...\n");
                // RunQueries(indexClient);

                Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
                Console.ReadKey();
            }

            // Create the search service client
            private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
            {
                string searchServiceName = configuration["SearchServiceName"];
                string adminApiKey = configuration["SearchServiceAdminApiKey"];

                SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
                return serviceClient;
            }

            // Delete an existing index to reuse its name
            private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
            {
                if (serviceClient.Indexes.Exists(indexName))
                {
                    serviceClient.Indexes.Delete(indexName);
                }
            }

            // Create an index whose fields correspond to the properties of the Hotel class.
            // The Address property of Hotel will be modeled as a complex field.
            // The properties of the Address class in turn correspond to sub-fields of the Address complex field.
            // The fields of the index are defined by calling the FieldBuilder.BuildForType() method.
            private static void CreateIndex(string indexName, SearchServiceClient serviceClient)
            {
                var definition = new Index()
                {
                    Name = indexName,
                    Fields = FieldBuilder.BuildForType<Hotel>()
                };

                serviceClient.Indexes.Create(definition);
            }
        }
    }    
    ```

    Ha lehetséges, ossza meg az alkalmazás egy `SearchServiceClient` példányát, hogy elkerülje a túl sok kapcsolat megnyitását. Az osztály-metódusok olyan szálon biztonságosak, amely lehetővé teszi az ilyen megosztást.

   Az osztálynak több konstruktora van. Az, amelyiket Ön szeretne, a keresőszolgáltatása nevét és egy `SearchCredentials` objektumot használ paraméterként. A `SearchCredentials` becsomagolja az API-kulcsot.

    Az index definíciójában az `Field` objektumok létrehozásának legegyszerűbb módja a `FieldBuilder.BuildForType` metódus meghívása, a Model osztály átadása a Type paraméterhez. A modellosztály olyan tulajdonságokkal rendelkezik, amelyek az index mezőire mutatnak. Ez a leképezés lehetővé teszi dokumentumok kötését a keresési indexből a modell osztály példányaira.

    > [!NOTE]
    > Ha nem tervez modellosztályt használni, közvetlenül `Field` objektumok létrehozásával is meghatározhatja az indexet. A konstruktornak megadhatja a mező nevét és az adattípust (vagy sztringmezők esetében az elemzőnek). Más tulajdonságokat `IsSearchable`is beállíthat, például:, `IsFilterable`, hogy csak néhányat említsünk.
    >

1. Nyomja le az F5 billentyűt az alkalmazás felépítéséhez és az index létrehozásához. 

    Ha a projekt sikeresen létrejön, megnyílik egy konzolablak, amely megjeleníti az állapotjelző üzeneteket a képernyőre az index törléséhez és létrehozásához. 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 – dokumentumok betöltése

Azure Search a dokumentumok olyan adatstruktúrák, amelyek mind a lekérdezések indexeléséhez, mind pedig a kimenetekhez tartoznak. Egy külső adatforrásból beszerzett módon a dokumentumok bemenetei egy adatbázisban, a blob Storage-ban vagy a lemezen található JSON-dokumentumok soraiban lehetnek. Ebben a példában a JSON-dokumentumokat a kód négy szállodájának ajánljuk. 

Dokumentumok feltöltésekor egy [`IndexBatch`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) objektumot kell használnia. Az `IndexBatch` egy [`IndexAction`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) objektumok gyűjteményét tartalmazza, amelyek mindegyike tartalmaz egy dokumentumot és egy tulajdonságot, amely közli Azure Search milyen műveletet kell végrehajtania ([feltöltés, Egyesítés, törlés és mergeOrUpload](search-what-is-data-import.md#indexing-actions)).

1. A Program.cs-ben hozzon létre egy tömböt a dokumentumok és az indexelési műveletek közül `IndexBatch`, majd adja át a tömböt a következőre:. Az alábbi dokumentumok megfelelnek a Hotel – rövid útmutató indexnek, amelyet a szálloda és a címe osztályok határoznak meg.

    ```csharp
    // Upload documents as a batch
    private static void UploadDocuments(ISearchIndexClient indexClient)
    {
        var actions = new IndexAction<Hotel>[]
        {
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "1",
                    HotelName = "Secret Point Motel",
                    Description = "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
                    DescriptionFr = "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "air conditioning", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(1970, 1, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.6,
                    Address = new Address()
                    {
                        StreetAddress = "677 5th Ave",
                        City = "New York",
                        StateProvince = "NY",
                        PostalCode = "10022",
                        Country = "USA"
                    }
                }
            ),
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "2",
                    HotelName = "Twin Dome Motel",
                    Description = "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "free wifi", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate =  new DateTimeOffset(1979, 2, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.60,
                    Address = new Address()
                    {
                        StreetAddress = "140 University Town Center Dr",
                        City = "Sarasota",
                        StateProvince = "FL",
                        PostalCode = "34243",
                        Country = "USA"
                    }
                }
            ),
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "3",
                    HotelName = "Triple Landscape Hotel",
                    Description = "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Resort and Spa",
                    Tags = new[] { "air conditioning", "bar", "continental breakfast" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(2015, 9, 20, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.80,
                    Address = new Address()
                    {
                        StreetAddress = "3393 Peachtree Rd",
                        City = "Atlanta",
                        StateProvince = "GA",
                        PostalCode = "30326",
                        Country = "USA"
                    }
                }
            ),
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "4",
                    HotelName = "Sublime Cliff Hotel",
                    Description = "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
                    DescriptionFr = "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
                    Category = "Boutique",
                    Tags = new[] { "concierge", "view", "24-hour front desk service" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1960, 2, 06, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.6,
                    Address = new Address()
                    {
                        StreetAddress = "7400 San Pedro Ave",
                        City = "San Antonio",
                        StateProvince = "TX",
                        PostalCode = "78216",
                        Country = "USA"
                    }
                }
            ),
        };

        var batch = IndexBatch.New(actions);

        try
        {
            indexClient.Documents.Index(batch);
        }
        catch (IndexBatchException e)
        {
            // When a service is under load, indexing might fail for some documents in the batch. 
            // Depending on your application, you can compensate by delaying and retrying. 
            // For this simple demo, we just log the failed document keys and continue.
            Console.WriteLine(
                "Failed to index some of the documents: {0}",
                String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
        }

        // Wait 2 seconds before starting queries 
        Console.WriteLine("Waiting for indexing...\n");
        Thread.Sleep(2000);
    }
    ```

    Miután elvégezte az`IndexBatch` objektum inicializálását, elküldheti az indexnek az [`Documents.Index`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet) [`SearchIndexClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) objektum hívásával. `Documents`a egy tulajdonsága `SearchIndexClient` , amely az indexben lévő dokumentumok hozzáadására, módosítására, törlésére vagy lekérdezésére szolgáló metódusokat biztosít.

    A metódus meghívása`Index` során az indexelési hibák megmaradnak, ami akkor fordulhat elő, ha a szolgáltatás nagy terhelés alatt áll. `try` / `catch` Az éles kódban késleltetheti, majd újra megpróbálkozhat a meghiúsult dokumentumok indexelésével, vagy a napló és a folytatás hasonló módon történő megismétlésével, vagy az alkalmazás adatkonzisztenciáji követelményeinek megfelelő más módon történő kezelésével.

    A 2 másodperces késleltetés kompenzálja az indexelést, amely aszinkron módon történik, így minden dokumentum indexelhető a lekérdezések végrehajtása előtt. A késések kódolása jellemzően csak demók, tesztek és példák esetében szükséges.

1. A Program.cs-ben a Main (fő) elemben a "2 – dokumentumok betöltése" sorok megjegyzéseit. 

    ```csharp
    // Uncomment next 3 lines in "2 - Load documents"
    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);
    ```
1. Az alkalmazás újraépítéséhez nyomja le az F5 billentyűt. 

    Ha a projekt sikeresen felépítve, megnyílik egy konzolablak, amely a dokumentumok feltöltésével kapcsolatos üzenettel jelenik meg. A Azure Portal keresési szolgáltatás **áttekintése** lapján a Hotels-rövid útmutatónak már 4 dokumentummal kell rendelkeznie.

További információ a dokumentumok feldolgozásáról: ["a .net SDK kezeli a dokumentumokat"](search-howto-dotnet-sdk.md#how-dotnet-handles-documents).

## <a name="3---search-an-index"></a>3 – Keresés az indexekben

A lekérdezési eredményeket az első dokumentum indexelése után azonnal lekérheti, de az index tényleges tesztelésének meg kell várnia, amíg az összes dokumentum indexelve van. 

Ez a szakasz két funkciót tartalmaz: a lekérdezési logikát és az eredményeket. Lekérdezésekhez használja a [ `Search` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.search?view=azure-dotnet
) metódust. Ezzel a módszerrel a keresési szöveg és más [Paraméterek](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters?view=azure-dotnet)is bekerülnek. 

Az [`DocumentsSearchResult`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1?view=azure-dotnet) osztály az eredményeket jelöli.


1. A Program.cs-ben hozzon létre egy WriteDocuments metódust, amely a keresési eredményeket a konzolra nyomtatja.

    ```csharp
    private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
    {
        foreach (SearchResult<Hotel> result in searchResults.Results)
        {
            Console.WriteLine(result.Document);
        }

        Console.WriteLine();
    }
    ```

1. Hozzon létre egy RunQueries metódust a lekérdezések végrehajtásához és az eredmények visszaadásához. Az eredmények a Hotel objektumai. A Select paraméterrel felveheti a felületet az egyes mezőkre. Ha egy mező nem szerepel a Select paraméterben, annak megfelelő szállodai tulajdonsága null értékű lesz.

    ```csharp
    private static void RunQueries(ISearchIndexClient indexClient)
    {
        SearchParameters parameters;
        DocumentSearchResult<Hotel> results;

        // Query 1 
        Console.WriteLine("Query 1: Search for term 'Atlanta' with no result trimming");
        parameters = new SearchParameters();
        results = indexClient.Documents.Search<Hotel>("Atlanta", parameters);
        WriteDocuments(results);

        // Query 2
        Console.WriteLine("Query 2: Search on the term 'Atlanta', with trimming");
        Console.WriteLine("Returning only these fields: HotelName, Tags, Address:\n");
        parameters =
            new SearchParameters()
            {
                Select = new[] { "HotelName", "Tags", "Address" },
            };
        results = indexClient.Documents.Search<Hotel>("Atlanta", parameters);
        WriteDocuments(results);

        // Query 3
        Console.WriteLine("Query 3: Search for the terms 'restaurant' and 'wifi'");
        Console.WriteLine("Return only these fields: HotelName, Description, and Tags:\n");
        parameters =
            new SearchParameters()
            {
                Select = new[] { "HotelName", "Description", "Tags" }
            };
        results = indexClient.Documents.Search<Hotel>("restaurant, wifi", parameters);
        WriteDocuments(results);

        // Query 4 -filtered query
        Console.WriteLine("Query 4: Filter on ratings greater than 4");
        Console.WriteLine("Returning only these fields: HotelName, Rating:\n");
        parameters =
            new SearchParameters()
            {
                Filter = "Rating gt 4",
                Select = new[] { "HotelName", "Rating" }
            };
        results = indexClient.Documents.Search<Hotel>("*", parameters);
        WriteDocuments(results);

        // Query 5 - top 2 results
        Console.WriteLine("Query 5: Search on term 'boutique'");
        Console.WriteLine("Sort by rating in descending order, taking the top two results");
        Console.WriteLine("Returning only these fields: HotelId, HotelName, Category, Rating:\n");
        parameters =
            new SearchParameters()
            {
                OrderBy = new[] { "Rating desc" },
                Select = new[] { "HotelId", "HotelName", "Category", "Rating" },
                Top = 2
            };
        results = indexClient.Documents.Search<Hotel>("boutique", parameters);
        WriteDocuments(results);
    }
    ```

    A kifejezéseket [](search-query-overview.md#types-of-queries)kétféleképpen lehet egyeztetni a lekérdezésekben: teljes szöveges keresés és szűrők. A teljes szöveges keresési lekérdezés egy vagy több kifejezést `IsSearchable` keres az index mezőiben. A szűrő egy logikai kifejezés, amely egy index `IsFilterable` mezőin van kiértékelve. A teljes szöveges keresést és a szűrőket együtt vagy külön is használhatja.

    A keresések és a szűrések egyaránt a `Documents.Search` módszer használatával vannak végrehajtva. Keresési lekérdezések a `searchText` paraméterben, szűrőkifejezések pedig a `SearchParameters` osztály `Filter` tulajdonságában adhatóak át. A keresés nélküli szűrés végrehajtásához a `searchText` paraméter számára a `"*"` kifejezést adja át. A szűrés nélküli keresés végrehajtásához ne állítsa be a `Filter` tulajdonságot, vagy egyáltalán ne adja át azt egy `SearchParameters`-példányban.

1. A Program.cs-ben a Main (fő) elemben a "3 – keresés" sorok megjegyzését írja vissza. 

    ```csharp
    // Uncomment next 2 lines in "3 - Search an index"
    Console.WriteLine("{0}", "Searching documents...\n");
    RunQueries(indexClient);
    ```
1. A megoldás már befejeződött. Nyomja le az F5 billentyűt az alkalmazás újraépítéséhez és a program teljes körű futtatásához. 

    A kimenet ugyanazokat az üzeneteket tartalmazza, mint a korábban, a lekérdezési adatok és eredmények hozzáadásával.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Ha saját előfizetésében dolgozik, a projekt végén érdemes megállapítani, hogy továbbra is szüksége van-e a létrehozott erőforrásokra. A már futó erőforrások pénzbe kerülnek. Az erőforrásokat egyenként is törölheti, vagy az erőforráscsoport törlésével törölheti a teljes erőforrás-készletet.

A bal oldali navigációs panelen a **minden erőforrás** vagy **erőforráscsoport** hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

Ha ingyenes szolgáltatást használ, ne feledje, hogy Ön legfeljebb három indexet, indexelő és adatforrást használhat. A portálon törölheti az egyes elemeket, hogy a korlát alatt maradjon. 

## <a name="next-steps"></a>További lépések

Ebben C# a rövid útmutatóban számos feladaton keresztül dolgozott egy index létrehozásához, a dokumentumok betöltéséhez és a lekérdezések futtatásához. Az olvashatóság és a megértés érdekében a kód leegyszerűsítése különböző fázisokban volt. Ha Ön az alapvető fogalmakkal is rendelkezik, javasoljuk, hogy a következő cikkből megtudhatja, milyen alternatív megközelítésekre és fogalmakra van szükség, amelyek elmélyítik az Ön ismereteit. 

A mintakód és az index ezen egy kibontott verziója. A következő minta egy Rooms-gyűjteményt hoz létre, különböző osztályokat és műveleteket használ, és közelebbről vizsgálja a feldolgozás működését.

> [!div class="nextstepaction"]
> [Fejlesztés a .NET-ben](search-howto-dotnet-sdk.md)
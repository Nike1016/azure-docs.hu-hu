---
title: Az Object (blob) tároló használata az Azure C++ -ból | Microsoft Docs
description: Strukturálatlan adat tárolása a felhőben az Azure Blob (Object) tárolóval.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/21/2018
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: f64856d5b0140d529e939d92d4be61b074928d6e
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726432"
---
# <a name="how-to-use-blob-storage-from-c"></a>A blob Storage használata a következővel:C++

Ez az útmutató bemutatja, hogyan hajthat végre gyakori forgatókönyveket az Azure Blob Storage használatával. A példák a Blobok feltöltését, listázását, letöltését és törlését mutatják be. A kódminták C++ nyelven íródtak, és az [Azure Storage C++ programnyelvhez készült ügyféloldali kódtárát](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) használják.   

További információ a blob Storage-ról: [Az Azure Blob Storage bemutatása](storage-blobs-introduction.md).

> [!NOTE]
> Ez az útmutató az Azure Storage C++ programnyelvhez készült ügyféloldali kódtárának 1.0.0-s és újabb verziójára vonatkozik. A Microsoft azt javasolja C++, hogy a Storage ügyféloldali kódtár legújabb verzióját használja a [NuGet](https://www.nuget.org/packages/wastorage) vagy [githubon](https://github.com/Azure/azure-storage-cpp)keresztül elérhető.

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>C++-alkalmazás létrehozása
Ebben az útmutatóban a tárolási funkciókat fogja használni, amelyek egy C++ alkalmazáson belül futtathatók.  

Ehhez telepítenie kell az Azure Storage C++ programnyelvhez készült ügyféloldali kódtárát, és létre kell hoznia egy Azure Storage-fiókot az Azure-előfizetésben.   

Az Azure Storage C++ programnyelvhez készült ügyféloldali kódtárát az alábbi módszerekkel telepítheti:

* **Linux** Kövesse az [Azure Storage ügyféloldali C++ kódtár információs](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) oldalának utasításait.  
* **Windows:** A Visual Studióban válassza a **Tools (Eszközök) > NuGet Package Manager (NuGet-csomagkezelő) > Package Manager Console (Csomagkezelő konzol)** elemet. Írja be a következő parancsot a [NuGet Package Manager konzolba](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) , majd nyomja le az **ENTER**billentyűt.  
  
     Install-Package wastorage

## <a name="configure-your-application-to-access-blob-storage"></a>Az alkalmazás konfigurálása a blob Storage eléréséhez
Adja hozzá a következő include utasításokat a C++ fájl elejéhez, ahol az Azure Storage API-kat szeretné használni a Blobok eléréséhez:  

```cpp
#include <was/storage_account.h>
#include <was/blob.h>
#include <cpprest/filestream.h>  
#include <cpprest/containerstream.h> 
```

## <a name="setup-an-azure-storage-connection-string"></a>Azure Storage-beli kapcsolatok karakterláncának beállítása
Az Azure Storage-ügyfél egy tárolási kapcsolati sztringet használ az adatkezelési szolgáltatások elérésére szolgáló végpontok és hitelesítő adatok tárolásához. Ügyfélalkalmazás esetén a tárolási kapcsolati karakterláncot a következő formátumban kell megadnia, a Storage-fiók neve és az [Azure Portalon](https://portal.azure.com) a *accountname* tartozó Storage-fiók elérési kulcsa alapján. és *AccountKey* értékeket. További információ a Storage-fiókokról és a hozzáférési kulcsokról: [Tudnivalók az Azure Storage](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)-fiókokról. Ez a példa bemutatja, hogyan deklarálhat statikus mezőt a kapcsolati sztring tárolására:  

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Az alkalmazás helyi Windows-számítógépen való teszteléséhez használhatja az [Azure SDK](https://azure.microsoft.com/downloads/)-val telepített Microsoft Azure [Storage emulatort](../storage-use-emulator.md) . A Storage Emulator egy olyan segédprogram, amely szimulálja az Azure-ban elérhető blob, üzenetsor és Table szolgáltatásokat a helyi fejlesztési gépen. A következő példa bemutatja, hogyan deklarálhat statikus mezőt a helyi Storage Emulatorhoz használható kapcsolati sztring tárolására:

```cpp
// Define the connection-string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Az Azure Storage Emulator elindításához kattintson a **Start** gombra, vagy nyomja le a **Windows** billentyűt. Kezdje el beírni az **Azure Storage emulatort**, és válassza a **Microsoft Azure Storage Emulator** lehetőséget az alkalmazások listájából.  

Az alábbi minták azt feltételezik, hogy az ezen két módszer egyikével kérte le a Storage kapcsolati sztringjét.  

## <a name="retrieve-your-storage-account"></a>A Storage-fiók beolvasása
A **cloud_storage_account** osztály használatával a Storage-fiók adatait jelenítheti meg. A Storage-fiók információit a **parse** metódussal kérheti le a Storage kapcsolati sztringjéből.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Ezután szerezzen be egy hivatkozást egy **cloud_blob_client** osztályra, mivel lehetővé teszi a blob Storage-ban tárolt tárolókat és blobokat jelképező objektumok lekérését. A következő kód létrehoz egy **cloud_blob_client** objektumot a fent lekért Storage Account objektum használatával:  

```cpp
// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  
```

## <a name="how-to-create-a-container"></a>Útmutató: Tároló létrehozása
[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

A példa bemutatja, hogyan hozhat létre tárolót, ha még nem rendelkezik vele:  

```cpp
try
{
    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Create the container if it doesn't already exist.
    container.create_if_not_exists();
}
catch (const std::exception& e)
{
    std::wcout << U("Error: ") << e.what() << std::endl;
}  
```

Alapértelmezés szerint az új tároló magánjellegű, és meg kell adnia a tároló hozzáférési kulcsát a Blobok ebből a tárolóból való letöltéséhez. Ha a tárolóban lévő fájlokat (blobokat) mindenki számára elérhetővé szeretné tenni, beállíthatja, hogy a tároló nyilvános legyen a következő kód használatával:  

```cpp
// Make the blob container publicly accessible.
azure::storage::blob_container_permissions permissions;
permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
container.upload_permissions(permissions);  
```

Az interneten bárki láthatja a blobokat egy nyilvános tárolóban, de csak akkor módosíthatja vagy törölheti őket, ha rendelkezik a megfelelő hozzáférési kulccsal.  

## <a name="how-to-upload-a-blob-into-a-container"></a>Útmutató: Blobok feltöltése a tárolóba
Az Azure Blob Storage támogatja a blobok és a Blobok blokkolását. A legtöbb esetben a blokkblobok használata javasolt.  

Fájlok blokkblobba való feltöltéséhez szerezze be a tároló hivatkozását, és annak segítségével kérje le a blokkblob hivatkozását. Ha már rendelkezik blob-hivatkozással, a **upload_from_stream** metódus meghívásával bármilyen adatfolyamot feltölthet rá. Ez az eljárás létrehozza a blobot, ha az még nem létezett, vagy felülírja, ha már igen. Az alábbi példák azt mutatják be, hogyan tölthetők fel blobok egy tárolóba, és feltételezik, hogy a tároló már létre lett hozva.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Create or overwrite the "my-blob-1" blob with contents from a local file.
concurrency::streams::istream input_stream = concurrency::streams::file_stream<uint8_t>::open_istream(U("DataFile.txt")).get();
blockBlob.upload_from_stream(input_stream);
input_stream.close().wait();

// Create or overwrite the "my-blob-2" and "my-blob-3" blobs with contents from text.
// Retrieve a reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob blob2 = container.get_block_blob_reference(U("my-blob-2"));
blob2.upload_text(U("more text"));

// Retrieve a reference to a blob named "my-blob-3".
azure::storage::cloud_block_blob blob3 = container.get_block_blob_reference(U("my-directory/my-sub-directory/my-blob-3"));
blob3.upload_text(U("other text"));  
```

Azt is megteheti, hogy a **upload_from_file** metódus használatával feltölthet egy fájlt egy blokk-blobba.

## <a name="how-to-list-the-blobs-in-a-container"></a>Útmutató: A tárolóban lévő blobok listázása
A tárolóban lévő blobok listázásához először kérje le a tároló hivatkozását. Ezután a tároló **list_blobs** metódusával lekérheti a blobokat és/vagy címtárakat. Ha a tulajdonságok és metódusok gazdag készletét szeretné elérni egy visszaadott **list_blob_item**, meg kell hívnia a **list_blob_item. as _blob** metódus egy **cloud_blob** -objektum beolvasására, vagy a **list_blob** metódus beolvasására _directory Directory-objektum. A következő kód bemutatja, hogyan kérhető le és jeleníthető meg az egyes elemek URI-ja a **saját minta-tároló** tárolóban:

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Output URI of each item.
azure::storage::list_blob_item_iterator end_of_results;
for (auto it = container.list_blobs(); it != end_of_results; ++it)
{
    if (it->is_blob())
    {
        std::wcout << U("Blob: ") << it->as_blob().uri().primary_uri().to_string() << std::endl;
    }
    else
    {
        std::wcout << U("Directory: ") << it->as_directory().uri().primary_uri().to_string() << std::endl;
    }
}
```

További részletek a listázási műveletekről: [Az Azure Storage-erőforrások C++listázása a-ben ](../storage-c-plus-plus-enumeration.md).

## <a name="how-to-download-blobs"></a>Útmutató: Blobok letöltése
A Blobok letöltéséhez először kérje le a blob-referenciát, majd hívja meg a **download_to_stream** metódust. A következő példa a **download_to_stream** metódus használatával továbbítja a blob tartalmát egy stream-objektumba, amelyet azután egy helyi fájlba is megtarthat.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Save blob contents to a file.
concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
concurrency::streams::ostream output_stream(buffer);
blockBlob.download_to_stream(output_stream);

std::ofstream outfile("DownloadBlobFile.txt", std::ofstream::binary);
std::vector<unsigned char>& data = buffer.collection();

outfile.write((char *)&data[0], buffer.size());
outfile.close();  
```

Azt is megteheti, hogy a **download_to_file** metódus használatával letölti a blob tartalmát egy fájlba.
Emellett használhatja a **download_text** metódust is a blob tartalmának szöveges karakterláncként való letöltéséhez.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob text_blob = container.get_block_blob_reference(U("my-blob-2"));

// Download the contents of a blog as a text string.
utility::string_t text = text_blob.download_text();
```

## <a name="how-to-delete-blobs"></a>Útmutató: Blobok törlése
BLOB törléséhez először szerezzen be egy blob-hivatkozást, majd hívja meg a **delete_blob** metódust.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Delete the blob.
blockBlob.delete_blob();
```

## <a name="next-steps"></a>További lépések
Most, hogy megismerte a blob Storage alapjait, az alábbi hivatkozásokat követve további információkat tudhat meg az Azure Storage-ról.  

* [A Queue Storage használataC++](../storage-c-plus-plus-how-to-use-queues.md)
* [A Table Storage használataC++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Azure Storage-erőforrások listázása itt:C++](../storage-c-plus-plus-enumeration.md)
* [A C++ Storage ügyféloldali kódtár referenciája](https://azure.github.io/azure-storage-cpp)
* [Az Azure Storage dokumentációja](https://azure.microsoft.com/documentation/services/storage/)
* [Adatátvitel az AzCopy parancssori segédprogrammal](../storage-use-azcopy.md)


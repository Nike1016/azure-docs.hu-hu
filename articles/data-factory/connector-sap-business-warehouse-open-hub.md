---
title: Adatok másolása az SAP Business Warehouse-ból az Open hub használatával Azure Data Factory | Microsoft Docs
description: Megtudhatja, hogyan másolhat adatok az SAP Business Warehouse-ból (BW) az Open hub használatával a fogadó adattárakhoz a Azure Data Factory folyamat másolási tevékenységével.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: e94c4f179174a3957aef8828687ebf1fbb299903
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967423"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>Adatok másolása az SAP Business Warehouse-ból az Open hub használatával Azure Data Factory

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok egy SAP Business Warehouse-ból (BW) az Open hub-on keresztül történő másolásához. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Az SAP Business Warehouse-ból bármilyen támogatott fogadó adattárba másolhatja az Adatmásolást. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Az SAP Business Warehouse nyitott hub-összekötője a következőt támogatja:

- Az SAP Business Warehouse **7,01-es vagy újabb verziója (az SAP támogatási csomagjának a 2015-os év után kiadott legújabb verziójában)** .
- Adatok másolása az Open hub Destination helyi táblán keresztül, amely alatt a DSO, a InfoCube, a multibiztosító, a DataSource stb. lehet.
- Adatok másolása egyszerű hitelesítés használatával.
- Kapcsolódás az alkalmazáskiszolgáló eléréséhez.

## <a name="sap-bw-open-hub-integration"></a>SAP BW Open Hub Integration 

[SAP BW Open hub szolgáltatás](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service) hatékony módszer az adatok SAP BWból való kinyerésére. Az alábbi ábra az SAP-rendszer egyik jellemző folyamatát mutatja be. ebben az esetben az SAP ECC-> PSA-> DSO-> Cube adatforgalmai.

SAP BW nyitott központ célhelye (OHD) határozza meg azt a célt, amelyhez az SAP-adattovábbítási szolgáltatás továbbítva van. Az SAP Adatátvitel Process (DTP) által támogatott összes objektum használható nyílt hub-adatforrásként, például DSO, InfoCube, DataSource stb. Nyissa meg a hub rendeltetési típusát – a továbbított adattárolók tárolására szolgáló táblák (helyi vagy távoli) és egyszerű fájlok. Ez az SAP BW Open hub-összekötő támogatja az adatok másolását a BW-ben lévő OHD helyi táblából. Ha más típusokat használ, a más összekötők használatával közvetlenül kapcsolódhat az adatbázishoz vagy a fájlrendszerhez.

![SAP BW Open Hub](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="delta-extraction-flow"></a>Különbözeti kinyerési folyamat

Az ADF SAP BW nyitott hub-összekötő két opcionális tulajdonságot `baseRequestId` kínál: `excludeLastRequest` és amely a nyitott központ Delta terhelésének kezelésére használható. 

- **excludeLastRequestId**: Azt határozza meg, hogy ki kell-e zárni a legutóbbi kérelem rekordjait. Az alapértelmezett érték TRUE (igaz). 
- **baseRequestId**: A különbözeti betöltésre vonatkozó kérelem azonosítója. Ha be van állítva, csak a tulajdonság értékénél nagyobb kérelemazonosító rendelkező adatmennyiségeket kéri le a rendszer. 

Az SAP InfoProviders Azure Data Factory (ADF)-ből való kinyerése összességében 2 lépésből áll: 

1. **SAP BW adatátvitel folyamat (DTP)** Ez a lépés átmásolja az adatait egy SAP BW InfoProvider egy SAP BW Open hub-táblába. 

1. **Az ADF** adatmásolása Ebben a lépésben az ADF-összekötő beolvassa az Open hub táblát 

![Különbözeti kinyerési folyamat](media/connector-sap-business-warehouse-open-hub/delta-extraction-flow.png)

Az első lépésben a DTP végrehajtása történik. Mindegyik végrehajtás egy új SAP-kérelem AZONOSÍTÓját hozza létre. A rendszer az Open hub táblában tárolja a kérelem AZONOSÍTÓját, amelyet az ADF-összekötő használ a különbözet azonosítására. A két lépés aszinkron módon fut: a DTP aktiválva van az SAP-n keresztül, és az ADF-adatmásolat az ADF-en keresztül aktiválódik. 

Alapértelmezés szerint az ADF nem olvassa be a legutóbbi Delta értéket az Open hub táblából (a "legutóbbi kérelem kizárása" beállítás igaz). Alulírott, az ADF-ben tárolt adatok nem 100%-kal naprakészek az Open hub táblában lévő adatokkal (az utolsó Delta hiányzik). A cserében ez az eljárás biztosítja, hogy az aszinkron kinyerés miatt ne vesszenek el a sorok. Akkor is jól működik, ha az ADF az Open hub-táblázatot olvassa, miközben a DTP még mindig ugyanabba a táblába ír. 

A legutóbb másolt kérelmek AZONOSÍTÓját általában egy átmeneti adattárba (például az Azure Blobra a fenti ábrán) tárolja. Ezért a következő futtatáskor a rendszer nem olvassa be az ADF második időpontját. Eközben vegye figyelembe, hogy az adatok nem törlődnek automatikusan az Open hub táblából.

A megfelelő Delta-kezelést illetően nem megengedett, hogy a különböző DTPs származó kérelmek azonosítói ugyanabban az Open hub-táblában legyenek. Ezért nem kell egynél több DTP-t létrehoznia minden egyes nyitott hub-célhoz (OHD). Ha az azonos InfoProvider származó teljes és különbözeti kinyerésre van szüksége, hozzon létre két OHDs ugyanahhoz a InfoProvider. 

## <a name="prerequisites"></a>Előfeltételek

Az SAP Business Warehouse nyitott hub-összekötő használatához a következőket kell tennie:

- Hozzon létre egy saját üzemeltetésű Integration Runtime a 3,13-es vagy újabb verzióval. Lásd: [helyi Integration Runtime](create-self-hosted-integration-runtime.md) részleteivel.

- Töltse le az **64 bites [SAP .net-összekötőt 3,0](https://support.sap.com/en/product/connectors/msnet.html)**  az SAP webhelyéről, és telepítse azt a saját üzemeltetésű IR-gépre. A telepítésekor a telepítéshez választható lépések ablakban válassza a **szerelvények telepítése a GAC** -ra lehetőséget, ahogy az alábbi képen is látható. 

    ![Az SAP .NET-összekötő telepítése](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- A Data Factory BW-összekötőben használt SAP-felhasználónak a következő engedélyekkel kell rendelkeznie: 

    - Az RFC és a SAP BW engedélyezése. 
    - A "S_SDSAUTH" engedélyezési objektum "EXECUTE" tevékenységének engedélyei.

- Hozzon létre egy SAP nyitott hub-célhelyet **adatbázis** -táblázatként a "technikai kulcs" beállítással.  Azt is javasoljuk, hogy az adatok törlését ne törölje a táblából, bár ez nem kötelező. Kihasználhatja a DTP-t (közvetlenül végrehajthatja vagy integrálhatja a meglévő feldolgozási láncot) az adatoknak a forrás objektumból (például adatkockából) való kiválasztásához, amelyet a nyitott hub-cél táblához választott.

## <a name="getting-started"></a>Első lépések

> [!TIP]
>
> Az SAP BW Open hub-összekötő használatával kapcsolatos útmutatóért lásd: [adatok betöltése az SAP Business Warehouse-ból (BW) a Azure Data Factory használatával](load-sap-bw-data.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek az SAP Business Warehouse nyitott hub-összekötőhöz kapcsolódó entitások definiálásához használhatók Data Factory.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az SAP Business Warehouse nyitott hub társított szolgáltatása a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot a következőre kell beállítani: **SapOpenHub** | Igen |
| server | Annak a kiszolgálónak a neve, amelyen az SAP BW-példány található. | Igen |
| systemNumber | A SAP BWrendszer rendszerszáma.<br/>Engedélyezett érték: két számjegyből álló decimális szám karakterláncként megadva. | Igen |
| clientId | A-ügyfél ügyfél-azonosítója az SAP W rendszeren.<br/>Engedélyezett érték: a háromjegyű decimális szám karakterláncként van megadva. | Igen |
| language | Az SAP-rendszer által használt nyelv. | Nem (az alapértelmezett érték az **en**)|
| userName | Az SAP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó neve. | Igen |
| password | A felhasználó jelszava. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. A saját üzemeltetésű Integration Runtime az [Előfeltételek](#prerequisites)szakaszban említettek szerint kell megadni. |Igen |

**Példa:**

```json
{
    "name": "SapBwOpenHubLinkedService",
    "properties": {
        "type": "SapOpenHub",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz a SAP BW Open hub-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Ha adatokat szeretne másolni a és a rendszerből SAP BW nyitott hubhoz, állítsa az adatkészlet Type (típus) tulajdonságát **SapOpenHubTable**értékre. A következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot **SapOpenHubTable**értékre kell beállítani.  | Igen |
| openHubDestinationName | Annak az Open hub-célhelynek a neve, amelyből az adatok másolása megtörténjen. | Igen |
| excludeLastRequest | Azt határozza meg, hogy ki kell-e zárni a legutóbbi kérelem rekordjait. | Nem (alapértelmezett érték a **true**) |
| baseRequestId | A különbözeti betöltésre vonatkozó kérelem azonosítója. Ha be van állítva, csak a tulajdonság értékénél **nagyobb** kérelemazonosító rendelkező adatmennyiségeket kéri le a rendszer.  | Nem |

>[!TIP]
>Ha a nyitott hub-tábla csak az egyszeres kérelem-azonosító által generált adatait tartalmazza, akkor a teljes terhelést és a táblázat meglévő adatait felülírja, vagy csak egyszer futtatja a DTP-tesztet, ne feledje, hogy törölje a "excludeLastRequest" lehetőséget a d ATA kimenő.

**Példa:**

```json
{
    "name": "SAPBWOpenHubDataset",
    "properties": {
        "type": "SapOpenHubTable",
        "linkedServiceName": {
            "referenceName": "<SAP BW Open Hub linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "openHubDestinationName": "<open hub destination name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a SAP BW Open hub-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="sap-bw-open-hub-as-source"></a>A hub megnyitása forrásként SAP BW

SAP BW Open hub adatainak másolásához állítsa a forrás típusát a másolás tevékenység **SapOpenHubSource**. A másolási tevékenység **forrása** szakaszban nem szükségesek további Type-specifikus tulajdonságok.

Az adatok betöltésének felgyorsításához beállíthatja [`parallelCopies`](copy-activity-performance.md#parallel-copy) , hogy a másolási tevékenység a SAP BW Open hub adatainak párhuzamosan történő betöltéséhez. Ha például a négy értékre `parallelCopies` van állítva, Data Factory egyszerre négy RFC-hívást hajt végre, és mindegyik RFC-hívás lekéri az adatok egy részét a SAP BW Open hub táblából, particionálva a DTP-kérés azonosítója és a csomag azonosítója alapján. Ez akkor érvényes, ha az egyedi DTP-kérések és-csomagok AZONOSÍTÓjának száma nagyobb, `parallelCopies`mint a értéke.

**Példa:**

```json
"activities":[
    {
        "name": "CopyFromSAPBWOpenHub",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW Open Hub input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SapOpenHubSource"
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw-open-hub"></a>Adattípusok leképezése SAP BW nyitott hubhoz

SAP BW Open hub adatainak másolása során a rendszer a következő leképezéseket használja SAP BW adattípusokból Azure Data Factory köztes adattípusokra. Lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md) megismerheti, hogyan másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó.

| SAP ABAP-típus | Data factory közbenső adattípus |
|:--- |:--- |
| C (String) | Sztring |
| I (integer) | Int32 |
| F (float) | Double |
| D (Date) | Sztring |
| T (Time) | Sztring |
| P (BCD Packed, Currency, Decimal, Qty) | Decimal |
| N (Numc) | Sztring |
| X (Binary and Raw) | Sztring |

## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).

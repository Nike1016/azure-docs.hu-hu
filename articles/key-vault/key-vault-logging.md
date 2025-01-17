---
title: Azure Key Vault naplózása | Microsoft Docs
description: Ez az oktatóanyag segít megismerkedni az Azure Key Vault naplózásával.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: fc0f259a4866f7eb2438938c7a10989db9200aa4
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976976"
---
# <a name="azure-key-vault-logging"></a>Az Azure Key Vault naplózása

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Egy vagy több kulcstartó létrehozása után valószínűleg figyelnie kell a kulcstartók elérésének módját és időpontját. Ezt úgy teheti meg, hogy engedélyezi a Azure Key Vault naplózását, amely az Ön által megadott Azure Storage-fiókban tárolja az adatokat. A megadott Storage-fiókhoz automatikusan létrejön egy elemzések – **naplók – AuditEvent** nevű új tároló. Ugyanazt a Storage-fiókot használhatja a naplók több kulcstartóhoz való gyűjtéséhez.

A naplózási adatokat a Key Vault művelet után 10 perccel (legfeljebb) érheti el. A legtöbb esetben azonban ez nem fog ennyi ideig tartani.  A tárfiók naplófájljait Önnek kell kezelnie:

* Az Azure szabványos hozzáférés-vezérlési módszereivel korlátozhatja a naplókhoz való hozzáférést, így megvédheti azokat.
* Törölje azokat a naplókat, amelyeket nem kíván megőrizni a tárfiókban.

Ez az oktatóanyag segít megismerkedni az Azure Key Vault naplózásával. Hozzon létre egy Storage-fiókot, engedélyezze a naplózást, és értelmezze az összegyűjtött napló adatait.  

> [!NOTE]
> Az oktatóanyag nem tartalmazza a kulcstárolók, a kulcsok és titkos kulcsok létrehozásának lépéseit. További információ: [Mi az Azure Key Vault?](key-vault-overview.md) Vagy a platformfüggetlen Azure CLI-utasításokért tekintse meg [ezt az egyenértékű oktatóanyagot](key-vault-manage-with-cli2.md).
>
> Ez a cikk Azure PowerShell útmutatást nyújt a diagnosztikai naplózás frissítéséhez. A diagnosztikai naplózást a Azure Portal **diagnosztikai naplók** szakaszának Azure monitor használatával is frissítheti. 
>

További információ a Key Vaultről: [Mi az Azure Key Vault?](key-vault-whatis.md). További információ a Key Vault rendelkezésre állásáról: díjszabási [oldal](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Egy meglévő kulcstároló.  
* Azure PowerShell a 1.0.0 minimális verziója. Az Azure PowerShell telepítésérről és az Azure-előfizetéssel való társításáról további információt [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása) című cikkben találhat. Ha már telepítette Azure PowerShell és nem ismeri a verziót, a Azure PowerShell konzolon írja be `$PSVersionTable.PSVersion`a következőt:.  
* A Key Vault naplóihoz elegendő tárhely az Azure-ban.

## <a id="connect"></a>Kapcsolódás a Key Vault-előfizetéshez

A legfontosabb naplózás beállításának első lépése, hogy a naplózni kívánt kulcstartóra Azure PowerShell mutasson.

Indítsa el az Azure PowerShell-munkamenetet, és jelentkezzen be az Azure-fiókjába a következő parancs használatával:  

```powershell
Connect-AzAccount
```

Az előugró böngészőablakban adja meg az Azure-fiókja felhasználónevét és jelszavát. Azure PowerShell lekéri a fiókhoz társított összes előfizetést. Alapértelmezés szerint a PowerShell az elsőt használja.

Előfordulhat, hogy meg kell adnia a kulcstartó létrehozásához használt előfizetést. Adja meg a következő parancsot a fiókhoz tartozó előfizetések megtekintéséhez:

```powershell
Get-AzSubscription
```

Ezután adja meg a naplózni kívánt kulcstartóhoz tartozó előfizetést, majd írja be a következőt:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

A PowerShell a megfelelő előfizetésre mutat, különösen akkor, ha több előfizetése van társítva a fiókjához. További információ az Azure PowerShell konfigurálásáról: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).

## <a id="storage"></a>Storage-fiók létrehozása a naplókhoz

Habár meglévő Storage-fiókot is használhat a naplókhoz, létrehozunk egy olyan Storage-fiókot, amely Key Vault naplókhoz lesz hozzárendelve. Ha később meg szeretné adni ezt a lehetőséget, a részleteket egy **sa**nevű változóban tároljuk.

A könnyebb kezelhetőség érdekében ugyanazt az erőforráscsoportot fogjuk használni, mint amely a kulcstartót is tartalmazza. Az [első lépéseket ismertető oktatóanyagból](key-vault-get-started.md)az erőforráscsoport neve **ContosoResourceGroup**, és továbbra is a Kelet-Ázsia helyet fogjuk használni. Cserélje le ezeket az értékeket a saját igényeinek megfelelően:

```powershell
 $sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'
```

> [!NOTE]
> Ha úgy dönt, hogy meglévő Storage-fiókot használ, ugyanazt az előfizetést kell használnia, mint a kulcstartó. És a klasszikus üzemi modell helyett a Azure Resource Manager telepítési modellt kell használnia.
>
>

## <a id="identify"></a>A naplók kulcstárolójának azonosítása

A [első lépések oktatóanyagban](key-vault-get-started.md)a kulcstároló neve **ContosoKeyVault**volt. Továbbra is ezt a nevet fogjuk használni, és a részleteket egy **kV**-os változóban tároljuk:

```powershell
$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'
```

## <a id="enable"></a>Naplózás engedélyezése

A Key Vault naplózásának engedélyezéséhez a **set-AzDiagnosticSetting** parancsmagot fogjuk használni az új Storage-fiókhoz és a kulcstartóhoz létrehozott változókkal együtt. Az **-enabled** jelzőt úgy is beállítjuk, hogy **$true** , és a kategóriát a **AuditEvent** (az egyetlen kategória Key Vault naplózás) értékre állítsa be:

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

A kimenet így néz ki:

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0

Ez a kimenet megerősíti, hogy a naplózás már engedélyezve van a kulcstartóban, és az adatokat a Storage-fiókjába menti.

Igény szerint megadhat egy adatmegőrzési házirendet a naplókhoz, így a régebbi naplók automatikusan törlődnek. Például állítsa be az adatmegőrzési házirendet úgy, hogy a **-RetentionEnabled** jelzőt **$true**értékre állítja, és a **-RetentionInDays** paramétert állítsa **90** -re, hogy a 90 napnál régebbi naplók automatikusan törlődjenek.

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

Mi kerül naplózásra?

* Az összes hitelesített REST API kérelem, beleértve a sikertelen kérelmeket a hozzáférési engedélyek, a rendszerhibák vagy a hibás kérelmek eredményeképpen.
* Maga a kulcstartó műveletei, beleértve a létrehozást, a törlést, a kulcstartó-hozzáférési szabályzatok beállítását és a Key Vault-attribútumok, például címkék frissítését.
* A Key vaultban lévő kulcsokkal és titkos kulcsokkal kapcsolatos műveletek, beleértve a következőket:
  * A kulcsok vagy titkos kódok létrehozása, módosítása vagy törlése.
  * A kulcsok aláírása, ellenőrzése, titkosítása, visszafejtése, becsomagolása és kicsomagolása, a titkok beolvasása és a kulcsok és titkok listázása (és azok verziói).
* A 401-es választ eredményező, nem hitelesített kérelmek. Ilyenek például azok a kérelmek, amelyek nem rendelkeznek olyan tulajdonosi jogkivonattal, amely nem formázott vagy lejárt, vagy érvénytelen tokent tartalmaz.  

## <a id="access"></a>A naplók elérése

Key Vault naplókat a megadott Storage-fiókban található elemzések – **naplók-AuditEvent** tárolóban tároljuk. A naplók megtekintéséhez le kell töltenie a blobokat.

Először hozzon létre egy változót a tároló nevéhez. Ezt a változót az útmutató többi részében fogja használni.

```powershell
$container = 'insights-logs-auditevent'
```

A tároló összes blobjának listázásához írja be a következőt:

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

A kimeneti ehhez hasonlóan néz ki:

```
Container Uri: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent

Name

---
resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json
```

Ahogy a kimenetből látható, a blobokat egy elnevezési konvenciót követi: `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

A dátum- és időértékek az UTC hivatkozási időzónát használják.

Mivel ugyanazt a Storage-fiókot használhatja több erőforráshoz tartozó naplók összegyűjtésére, a blob nevében lévő teljes erőforrás-azonosító hasznos lehet, ha csak a szükséges blobokat szeretné elérni vagy letölteni. Előtte azonban nézzük meg, hogyan tölthető le az összes blob.

Hozzon létre egy mappát a Blobok letöltéséhez. Példa:

```powershell 
New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force
```

Majd kérje le az összes blob listáját:  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Ezt a listát a **Get-AzStorageBlobContent** segítségével letölthet a Blobok a célmappába való letöltéséhez:

```powershell
$blobs | Get-AzStorageBlobContent -Destination C:\Users\username\ContosoKeyVaultLogs'
```

A második parancs futtatásakor a **/** a blob nevének elválasztó alapján hoz létre egy teljes mapparendszert a rendeltetési mappára. Ezt a struktúrát fogja használni a Blobok fájlként való letöltéséhez és tárolásához.

A blobok egyenkénti letöltéséhez használjon helyettesítő elemeket. Példa:

* Ha több kulcstárolóval rendelkezik, de csak a CONTOSOKEYVAULT3 nevűhöz szeretne naplókat letölteni:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* Ha több erőforráscsoporttal rendelkezik, de csak egyhez szeretne naplókat letölteni, használja a `-Blob '*/RESOURCEGROUPS/<resource group name>/*'` parancsot:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* Ha a 2019 januári hónapban szeretné letölteni az összes naplót, használja `-Blob '*/year=2019/m=01/*'`a következőt:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

Most már készen áll a naplók tartalmának megtekintésére. Mielőtt azonban beköltözünk erre, két további parancsot kell tudnia:

* A kulcstároló erőforrásához tartozó diagnosztikai beállítások állapotának lekérdezése:`Get-AzDiagnosticSetting -ResourceId $kv.ResourceId`
* A kulcstároló erőforrása naplózásának letiltása:`Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Category AuditEvent`

## <a id="interpret"></a>A Key Vault naplóinak értelmezése

Az egyes blobok JSON-blobként, szöveges formában vannak tárolva. Nézzük meg egy példa naplóbejegyzést. Futtassa ezt a parancsot:

```powershell
Get-AzKeyVault -VaultName 'contosokeyvault'`
```

Ez egy, a következőhöz hasonló naplóbejegyzést ad vissza:

```json
    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }
```

A következő táblázat a mezőneveket és a leírásokat tartalmazza:

| Mező neve | Leírás |
| --- | --- |
| **idő** |Dátum és idő (UTC). |
| **resourceId** |Az Azure Resource Manager-erőforrás azonosítója. Key Vault naplók esetében ez mindig a Key Vault erőforrás-azonosító. |
| **OperationName** |A művelet neve, ahogy a következő táblázat is mutatja. |
| **operationVersion** |REST API az ügyfél által kért verziót. |
| **kategória** |Az eredmény típusa. Key Vault naplók esetében a **AuditEvent** az egyetlen elérhető érték. |
| **resultType** |A REST API kérelem eredménye. |
| **resultSignature** |A HTTP-állapot. |
| **resultDescription** |Az eredmény további leírása, amennyiben elérhető. |
| **Átl** |A REST API-kérelem végrehajtásának ideje ezredmásodpercben. Ebbe nincs beleszámítva a hálózati késés, így az ügyféloldalon mért idő ettől eltérhet. |
| **callerIpAddress** |Annak az ügyfélnek az IP-címe, amely a kérelmet elvégezte. |
| **correlationId** |Egy nem kötelező GUID, amelyet az ügyfél alkalmazhat az ügyféloldali és a szolgáltatásoldali (Key Vault) naplók egyeztetéséhez. |
| **identitás** |Az REST API kérelemben bemutatott jogkivonat identitása. Ez általában a "felhasználó", "a" szolgáltatásnév "vagy" felhasználó + appId "kombinációja, amely egy Azure PowerShell-parancsmagból származó kérelem esetében van. |
| **Tulajdonságok** |A művelettől (**operationName**) függően változó információk. A legtöbb esetben ez a mező tartalmazza az ügyfél adatait (az ügyfél által átadott felhasználói ügynök sztringjét), a pontos REST API kérelem URI-JÁT és a HTTP-állapotkódot. Emellett, ha egy objektum egy kérelem eredményeképpen lett visszaadva (például a Key **create** vagy a **VaultGet**), a kulcs URI-ját ("id"), a tároló URI-JÁT vagy a titkos azonosítót is tartalmazza. |

A **OperationName** *ObjectVerb* formátumban vannak. Példa:

* A `Vault<action>` Key Vault összes műveletének formátuma, `VaultGet` például és `VaultCreate`.
* Az `Key<action>` összes kulcsfontosságú művelet formátuma, `KeySign` például és `KeyList`.
* Minden titkos művelet `Secret<action>` formátuma, `SecretGet` például és `SecretListVersions`.

A következő táblázat felsorolja a **operationName** és a hozzá tartozó REST API parancsokat:

| operationName | REST API parancs |
| --- | --- |
| **Hitelesítés** |Hitelesítés Azure Active Directory végponton keresztül |
| **VaultGet** |[Kulcstároló adatainak lekérése](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[Kulcstároló létrehozása vagy frissítése](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultDelete** |[Kulcstároló törlése](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[Kulcstároló frissítése](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultList** |[Az erőforráscsoport összes kulcstárolójának listázása](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **KeyCreate** |[Kulcs létrehozása](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[Kulcs adatainak lekérése](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **Importálás** |[Kulcs importálása egy tárolóba](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **Biztonsági mentés** |[Kulcs biztonsági mentése](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
| **KeyDelete** |[Kulcs törlése](https://msdn.microsoft.com/library/azure/dn903611.aspx) |
| **Visszaállítás** |[Kulcs helyreállítása](https://msdn.microsoft.com/library/azure/dn878106.aspx) |
| **KeySign** |[Aláírás kulccsal](https://msdn.microsoft.com/library/azure/dn878096.aspx) |
| **KeyVerify** |[Ellenőrzés kulccsal](https://msdn.microsoft.com/library/azure/dn878082.aspx) |
| **KeyWrap** |[Kulcs becsomagolása](https://msdn.microsoft.com/library/azure/dn878066.aspx) |
| **KeyUnwrap** |[Kulcs kicsomagolása](https://msdn.microsoft.com/library/azure/dn878079.aspx) |
| **Titkosítás** |[Titkosítás kulccsal](https://msdn.microsoft.com/library/azure/dn878060.aspx) |
| **KeyDecrypt** |[Visszafejtés kulccsal](https://msdn.microsoft.com/library/azure/dn878097.aspx) |
| **KeyUpdate** |[Kulcs frissítése](https://msdn.microsoft.com/library/azure/dn903616.aspx) |
| **KeyList** |[Egy tároló kulcsainak listázása](https://msdn.microsoft.com/library/azure/dn903629.aspx) |
| **KeyListVersions** |[Kulcs verzióinak listázása](https://msdn.microsoft.com/library/azure/dn986822.aspx) |
| **SecretSet** |[Titkos kulcs létrehozása](https://msdn.microsoft.com/library/azure/dn903618.aspx) |
| **SecretGet** |[Titkos kód beszerzése](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[Titkos kulcs frissítése](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[Titkos kulcs törlése](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **SecretList** |[Egy tároló titkos kulcsainak listázása](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[Titkos kulcs verzióinak listázása](https://msdn.microsoft.com/library/azure/dn986824.aspx) |

## <a id="loganalytics"></a>Azure Monitor naplók használata

Azure Monitor naplók Key Vault megoldásával áttekintheti Key Vault **AuditEvent** naplóit. Azure Monitor naplókban a naplók segítségével elemezheti az adatokat, és lekérheti a szükséges információkat. 

További információk, például a beállításának módja: [Azure Key Vault megoldás a Azure monitor naplókban](../azure-monitor/insights/azure-key-vault.md). Ez a cikk útmutatást is tartalmaz, ha át kell térnie a régi Key Vault megoldásról, amelyet a Azure Monitor naplók előzetes verziójában nyújtottak be, amikor először átirányítja a naplókat egy Azure Storage-fiókba, és konfigurálta Azure Monitor naplókat, amelyeket onnan olvas.

## <a id="next"></a>Következő lépések

A .NET-alapú webalkalmazásokban Azure Key Vaultt használó oktatóanyagért lásd: [Azure Key Vault használata](tutorial-net-create-vault-azure-web-app.md)webalkalmazásból.

Programozási hivatkozások: [Az Azure Key Vault fejlesztői útmutatója](key-vault-developers-guide.md).

Azure Key Vault Azure PowerShell 1,0-parancsmagok listáját itt tekintheti meg: [Azure Key Vault parancsmagok](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault).

A Key rotációs és a Azure Key Vault használatával történő naplózással kapcsolatos oktatóanyagért tekintse meg a [Key Vault teljes körű kulcsfontosságú rotációs és naplózási](key-vault-key-rotation-log-monitoring.md)funkciójának beállítása című témakört.

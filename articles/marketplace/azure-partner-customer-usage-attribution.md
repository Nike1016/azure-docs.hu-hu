---
title: Azure-partner és ügyfél-használati jóváírás | Azure piactér
description: Az Azure Marketplace-megoldások felhasználói használatának nyomon követése – áttekintés
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: yijenj
ms.service: marketplace
ms.topic: article
ms.date: 11/17/2018
ms.author: pabutler
ms.openlocfilehash: 98d33dd1aad726d4da8499006166dfd2c043378f
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68977154"
---
# <a name="azure-partner-customer-usage-attribution"></a>Az Azure-partnererőforrások ügyfelek általi használatának nyomon követése

Az Azure-hoz készült szoftver-partnerként a megoldásai Azure-összetevőket igényelnek, vagy közvetlenül az Azure-infrastruktúrán kell üzembe helyezni őket. Azok az ügyfelek, akik partneri megoldást telepítenek és saját Azure-erőforrásaikat helyeznek üzembe, nehezen láthatják el az üzemelő példány állapotát, és az Azure növekedésének hatására beszerezhetik az optikai eszközöket. Ha magasabb szintű láthatóságot ad hozzá, akkor a Microsoft értékesítési csapatával összhangba kerül, és a Microsoft-partneri programokhoz kreditet nyerhet.

A Microsoft mostantól lehetővé teszik a partnerek számára, hogy jobban nyomon kövessék az Azure-ban a szoftverük felhasználói telepítésének Azure-használatát. Az új metódus Azure Resource Manager az Azure-szolgáltatások üzembe helyezésének előkészítéséhez.

Microsoft-partnerként az Azure-használatot társíthatja bármely, az ügyfél nevében kiépített Azure-erőforrással. A társítást az Azure piactéren, a gyors üzembe helyezési adattáron, a privát GitHub-adattárakon és egy-egy ügyfélen keresztül is létrehozhatja. A Customer használati jóváírás három üzembe helyezési lehetőséget támogat:

- Azure Resource Manager sablonok: A partnerek Resource Manager-sablonok használatával telepíthetik az Azure-szolgáltatásokat a partner szoftverének futtatásához. A partnerek létrehozhatnak egy Resource Manager-sablont az Azure-megoldás infrastruktúrájának és konfigurációjának definiálásához. A Resource Manager-sablonok lehetővé teszik a megoldás üzembe helyezését a teljes életciklusa során. Biztos lehet abban, hogy az erőforrások konzisztens állapotban vannak telepítve.
- Azure Resource Manager API-k: A partnerek közvetlenül is meghívhatják a Resource Manager API-kat egy Resource Manager-sablon üzembe helyezéséhez, vagy az API-hívások létrehozásához az Azure-szolgáltatások közvetlen kiépítéséhez.
- Terraform A partnerek a Cloud Orchestrator, például a Terraform használatával üzembe helyezhetnek egy Resource Manager-sablont, vagy közvetlenül üzembe helyezhetik az Azure-szolgáltatásokat.

Az ügyfél-használati jóváírás az új központi telepítés, és nem támogatja a már telepített meglévő erőforrások címkézését.

Az Azure-alkalmazásban az ügyfél [](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer)-használati jóváírásra van szükség: az Azure Marketplace-en közzétett megoldás-sablon ajánlat.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-resource-manager-templates"></a>Resource Manager-sablonok használata
Számos partneri megoldás van telepítve az ügyfél előfizetéséhez Resource Manager-sablonok használatával. Ha van olyan Resource Manager-sablonja, amely elérhető az Azure Marketplace-en, a GitHubon vagy egy gyors útmutatóként, akkor a sablon módosításának folyamata legyen egyszerű.

A megoldási sablonok létrehozásával és közzétételével kapcsolatos további információkért lásd:

* [Hozza létre és telepítse az első Resource Manager-sablont](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).
* [Azure-alkalmazás ajánlata](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer).
* Videó: [Megoldási sablonok és felügyelt alkalmazások készítése az Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603)-en.


## <a name="add-a-guid-to-your-template"></a>GUID hozzáadása a sablonhoz

Globálisan egyedi azonosító (GUID) hozzáadásához egyetlen módosítást kell végeznie a fő sablonfájl számára:

1. [Hozzon létre egy GUID-azonosítót](#create-guids) a javasolt metódus használatával, és [regisztrálja a GUID azonosítót](#register-guids-and-offers).

1. Nyissa meg a Resource Manager-sablont.

1. Vegyen fel egy új erőforrást a fő sablonfájlba. Az erőforrásnak csak a **mainTemplate. JSON** vagy a **azuredeploy. JSON** fájlban kell szerepelnie, nem pedig egyetlen beágyazott vagy csatolt sablonban sem.

1. Adja meg a GUID értékét a **PID-** előtag (például: PID-eb7927c8-dd66-43e1-b0cf-c346a422063) után.

1. Keresse meg az esetleges hibákat a sablonban.

1. Tegye közzé újra a sablont a megfelelő adattárakban.

1. [Ellenőrizze a GUID sikerességét a sablon központi telepítésében](#verify-the-guid-deployment).

### <a name="sample-resource-manager-template-code"></a>Példa Resource Manager-sablon kódjára

A sablon követési erőforrásainak engedélyezéséhez hozzá kell adnia a következő további erőforrást az erőforrások szakaszban. Ügyeljen arra, hogy az alábbi mintakód a saját bemeneteit használja a fő sablonfájl hozzáadásakor.
Az erőforrást csak a **mainTemplate. JSON** vagy a **azuredeploy. JSON** fájlban kell hozzáadni, nem pedig egyetlen beágyazott vagy csatolt sablonban sem.

```
// Make sure to modify this sample code with your own inputs where applicable

{ // add this resource to the resources section in the mainTemplate.json (do not add the entire file)
    "apiVersion": "2018-02-01",
    "name": "pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", // use your generated GUID here
    "type": "Microsoft.Resources/deployments",
    "properties": {
        "mode": "Incremental",
        "template": {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "resources": []
        }
    }
} // remove all comments from the file when complete
```

## <a name="use-the-resource-manager-apis"></a>A Resource Manager API-k használata

Bizonyos esetekben előfordulhat, hogy közvetlenül a Resource Manager REST API-kon keresztül szeretne hívásokat kezdeményezni az Azure-szolgáltatások üzembe helyezéséhez. Az Azure a hívások engedélyezéséhez [több SDK](https://docs.microsoft.com/azure/#pivot=sdkstools) -t is támogat. Az SDK-k egyikét használhatja, vagy a REST API-kat közvetlenül is meghívhatja az erőforrások üzembe helyezéséhez.

Ha Resource Manager-sablont használ, a megoldást a korábban ismertetett utasítások követésével címkézheti. Ha nem használ Resource Manager-sablont, és közvetlen API-hívásokat végez, továbbra is címkézheti az üzembe helyezést az Azure-erőforrások használatának hozzárendeléséhez.

### <a name="tag-a-deployment-with-the-resource-manager-apis"></a>Központi telepítés címkézése a Resource Manager API-kkal

Ha engedélyezni szeretné az ügyfelek használati feltételeit, az API-hívások tervezésekor vegyen fel egy GUID azonosítót a kérelem felhasználói ügynök fejlécében. Adja hozzá a GUID azonosítót az egyes ajánlatokhoz vagy SKU-hoz. Formázza a karakterláncot a **PID-** előtaggal, és adja meg a partner által generált GUID azonosítóját. Íme egy példa a felhasználói ügynökbe való beszúráshoz használt GUID formátumra:

![Példa GUID formátumra](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!Note]
> A karakterlánc formátuma fontos. Ha a **PID-** előtag nem szerepel a rendszerben, nem lehet lekérdezni az adathalmazt. Különböző SDK-k nyomon követése eltérő. Ennek a módszernek a megvalósításához tekintse át az előnyben részesített Azure SDK támogatási és nyomkövetési megközelítését.

#### <a name="example-the-python-sdk"></a>Példa: A Python SDK

A Python esetében használja a **config** attribútumot. Az attribútumot csak UserAgent lehet hozzáadni. Például:

![Attribútum hozzáadása felhasználói ügynökhöz](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!Note]
> Adja hozzá az attribútumot az egyes ügyfelekhez. Nincs globális statikus konfiguráció. Előfordulhat, hogy az ügyfél-előállítót címkével látja el, hogy minden ügyfél nyomon követi a nyomkövetést. További információkért tekintse meg ezt a [Client Factory-mintát a githubon](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79).

#### <a name="tag-a-deployment-by-using-the-azure-powershell"></a>Központi telepítés címkézése a Azure PowerShell használatával

Ha Azure PowerShell segítségével telepít erőforrásokat, a következő módszer használatával fűzze hozzá a GUID azonosítóját:

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

#### <a name="tag-a-deployment-by-using-the-azure-cli"></a>Központi telepítés címkézése az Azure CLI használatával

Ha az Azure CLI-t használja a GUID-azonosító hozzáfűzéséhez, állítsa be a **AZURE_HTTP_USER_AGENT** környezeti változót. Ezt a változót egy parancsfájl hatókörén belül állíthatja be. A változót globálisan is beállíthatja a rendszerhéj hatóköréhez:

```
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```
További információ: [Go nyelvhez készült Azure SDK](https://docs.microsoft.com/azure/go/).

## <a name="use-terraform"></a>Terraform használata

A Terraform támogatása az Azure-szolgáltató 1.21.0-kiadásán keresztül érhető [https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019)el:.  Ez a támogatás minden olyan partnerre vonatkozik, amely a Terraform-n keresztül telepíti a megoldását, valamint az Azure-szolgáltató által üzembe helyezett és mért összes erőforrást (1.21.0 vagy újabb verzió).

A Terraform-hez készült Azure-szolgáltató hozzáadta a [*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id) nevű új opcionális mezőt, amely a megoldáshoz használt nyomkövetési GUID azonosítóját adja meg. A mező értéke a *ARM_PARTNER_ID* környezeti változóból is származhat.

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = “xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
Azok a partnerek, akik az ügyfél-használati jóváírás által követett Terraform szeretnék telepíteni az üzembe helyezést, a következőket kell tenniük:

* GUID létrehozása (az egyes ajánlatokhoz vagy SKU-hoz hozzá kell adni a GUID azonosítót)
* Frissítse az Azure-szolgáltatóját a *partner_id* értékének a GUID azonosítóra való beállításához (a GUID-azonosítót ne a "PID-" beállítással állítsa be, csak állítsa be a tényleges GUID azonosítóra)

## <a name="create-guids"></a>GUID-azonosítók létrehozása

A GUID egy egyedi hivatkozási szám, amelynek 32 hexadecimális számjegye van. A nyomkövetési GUID azonosítók létrehozásához GUID-generátort kell használnia. Az Azure Storage csapata létrehozta a [GUID generátort](https://aka.ms/StoragePartners) , amely a megfelelő formátumú GUID-t e-mailben küldi el, és újra felhasználható a különböző nyomkövető rendszerek között.

> [!Note]
> Erősen ajánlott az [Azure Storage GUID Generator űrlapját](https://aka.ms/StoragePartners) használni a GUID létrehozásához. További információ: [Gyakori kérdések](#faq).

Javasoljuk, hogy hozzon létre egyedi GUID azonosítót minden ajánlathoz és terjesztési csatornához az egyes termékekhez. Dönthet úgy, hogy egyetlen GUID azonosítót használ a termék több terjesztési csatornához, ha nem szeretné, hogy a jelentés szét legyen osztva.

Ha egy terméket egy sablon használatával helyez üzembe, és az Azure piactéren és a GitHubon is elérhető, két különálló GUID azonosítót hozhat létre és regisztrálhat:

*   Az A termék az Azure piactéren
*   A termék a GitHubon

A jelentéskészítést a partner értéke (Microsoft partner azonosítója) és a GUID-azonosítók végzik.

A GUID azonosítókat részletesebben is nyomon követheti, például az SKU-t, ahol az SKU egy ajánlat variánsa.

## <a name="register-guids-and-offers"></a>GUID azonosítók és ajánlatok regisztrálása

A GUID azonosítókat regisztrálni kell az ügyfelek használati feltételének engedélyezéséhez.

A sablon GUID-azonosítóinak minden regisztrációja az Azure Marketplace Cloud Partner Portal (CPP) használatával történik.

Miután hozzáadta a GUID azonosítót a sablonhoz vagy a felhasználói ügynökhöz, és regisztrálja a GUID azonosítót a CPP-ben, a rendszer az összes központi telepítést nyomon követi.

1. Jelentkezzen be az [Azure Marketplace](https://aka.ms/listonazuremarketplace) -re, és szerezzen hozzáférést a CPP-hoz.

   * A partnereknek egy profilra van szükségük a [CPP-ben](https://docs.microsoft.com/azure/marketplace/become-publisher). Javasoljuk, hogy az ajánlatot az Azure Marketplace-en vagy a AppSource sorolja fel.
   * A partnerek több GUID-azonosítót is regisztrálhatnak.
   * A partnerek regisztrálhatnak egy GUID azonosítót a nem Piactéri megoldás sablonjaihoz és ajánlatokhoz.

1. Jelentkezzen be a [Cloud Partner Portalba](https://cloudpartner.azure.com/).

1. A jobb felső sarokban válassza ki a fiók ikonját, majd válassza a **közzétevői profil**lehetőséget.

   ![Közzétevői profil kiválasztása](media/marketplace-publishers-guide/guid-image-for-lu.png)

1. A **Profil lapon**válassza a **nyomkövetési GUID hozzáadása elemet.**

   ![Válassza a nyomkövetési GUID hozzáadása elemet.](media/marketplace-publishers-guide/guid-how-to-add-tracking.png)

1. A **nyomkövetési GUID** mezőben adja meg a nyomkövetési GUID azonosítóját. A **PID** előtag nélkül adja meg a GUID azonosítót. Az **Egyéni Leírás** mezőben adja meg az ajánlat nevét vagy leírását.

   ![Profil lap](media/marketplace-publishers-guide/guid-dev-center-login.png)

   ![Adja meg a GUID azonosítót és az ajánlat leírását](media/marketplace-publishers-guide/guid-dev-center-example.png)

1. Egynél több GUID-azonosító regisztrálásához válassza ismét a **nyomkövetési GUID hozzáadása** elemet. Az oldalon további mezők jelennek meg.

   ![Válassza ismét a nyomkövetési GUID hozzáadása elemet.](media/marketplace-publishers-guide/guid-dev-center-example-add.png)

   ![Adjon meg egy másik GUID azonosítót és az ajánlat leírását](media/marketplace-publishers-guide/guid-dev-center-example-description.png)

1. Kattintson a **Mentés** gombra.

   ![Mentés kiválasztása](media/marketplace-publishers-guide/guid-dev-center-save.png)

Miután hozzáadta a GUID azonosítót a sablonhoz vagy a felhasználói ügynökhöz, és regisztrálja a GUID azonosítót a CPP-ben, a rendszer az összes központi telepítést nyomon követi.

## <a name="verify-the-guid-deployment"></a>A GUID központi telepítésének ellenőrzése

A sablon módosítása és a tesztek futtatása után futtassa a következő PowerShell-parancsfájlt a telepített és címkézett erőforrások lekéréséhez.

A parancsfájl segítségével ellenőrizheti, hogy a GUID sikeresen hozzá van-e adva a Resource Manager-sablonhoz. A parancsfájl nem vonatkozik a Resource Manager API-ra vagy a Terraform üzemelő példányokra.

Jelentkezzen be az Azure-ba. Válassza ki azt az előfizetést, amelynek a központi telepítését ellenőrizni szeretné a parancsfájl futtatása előtt. Futtassa a parancsfájlt az üzemelő példány előfizetési környezetében.

A központi telepítés **GUID-azonosítója** és **resourceGroup** neve kötelező paraméterek.

[Az eredeti szkript](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1) a githubon szerezhető be.

```powershell
Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the pid deployment

$correlationId = (Get-AzResourceGroupDeployment -ResourceGroupName
$resourceGroupName -Name "pid-$guid").correlationId

# Find all deployments with that correlationId

$deployments = Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in a deployment by name
# PowerShell doesn't surface outputResources on the deployment
# or correlationId on the deploymentOperation

foreach ($deployment in $deployments){

# Get deploymentOperations by deploymentName
# then the resourceId for any create operation

($deployment | Get-AzResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}
```

## <a name="report"></a>Jelentés

Az ügyfél-használati jóváírással kapcsolatos jelentést a partner Center elemzés irányítópultján találja. ([https://partner.microsoft.com/en-us/dashboard/mpn/analytics/CPP/MicrosoftAzure](https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure)). A jelentés megtekintéséhez a partner Center hitelesítő adatait kell használnia a bejelentkezéshez. Ha a jelentéssel vagy a bejelentkezéssel kapcsolatos problémák merülnek fel, hozzon létre egy támogatási kérést a támogatás lekérése részben.

A jelentés megtekintéséhez a partneri társítás típusa legördülő listában válassza a követett sablon lehetőséget.

![Ügyfél-használati jóváírás jelentés](media/marketplace-publishers-guide/customer-usage-attribution-report.png)

## <a name="notify-your-customers"></a>Ügyfelek értesítése

A partnereknek tájékoztatniuk kell az ügyfelet az ügyfél-használati jóváírást használó üzembe helyezésekről. A Microsoft azokat az Azure-használati adatokat jelenti, amelyek a partnerhez való központi telepítésekhez vannak társítva. Az alábbi példák olyan tartalmakat tartalmaznak, amelyekkel értesítheti ügyfeleit ezekről az üzemelő példányokról. A példákban cserélje le \<a partner >t a vállalat nevére. A partnereknek meg kell győződniük arról, hogy az értesítés igazodik az adatvédelemhez és a gyűjtési szabályzatokhoz, beleértve azokat a lehetőségeket, amelyekkel kizárható a nyomon követésből.

### <a name="notification-for-resource-manager-template-deployments"></a>Értesítés Resource Manager-sablonok üzembe helyezéséhez

A sablon központi telepítésekor a Microsoft azonosítani tudja a \<partner > szoftver telepítését az üzembe helyezett Azure-erőforrásokkal. A Microsoft képes a szoftver támogatásához használt Azure-erőforrások összekapcsolására. A Microsoft ezeket az adatokat gyűjti, hogy a lehető legjobb élményt nyújtsa termékeivel, és üzleti tevékenységét. Az adatokat a Microsoft adatvédelmi szabályzatai gyűjtik és szabályozzák, amelyek a következő címen találhatók https://www.microsoft.com/trustcenter:.

### <a name="notification-for-sdk-or-api-deployments"></a>Értesítés SDK-vagy API-telepítésekhez

A partner > \<szoftver központi telepítésekor a Microsoft azonosítani tudja a \<partner > szoftver telepítését az üzembe helyezett Azure-erőforrásokkal. A Microsoft képes a szoftver támogatásához használt Azure-erőforrások összekapcsolására. A Microsoft ezeket az adatokat gyűjti, hogy a lehető legjobb élményt nyújtsa termékeivel, és üzleti tevékenységét. Az adatokat a Microsoft adatvédelmi szabályzatai gyűjtik és szabályozzák, amelyek a következő címen találhatók https://www.microsoft.com/trustcenter:.

## <a name="get-support"></a>Támogatás kérése

A felmerülő problémáktól függően két támogatási csatorna van.

Ha bármilyen problémába ütközik a partner Centerben, például megtekinti az ügyfél-használati jóváírási jelentést vagy bejelentkezik, hozzon létre egy támogatási kérést a partner Center támogatási csapatával itt:[https://partner.microsoft.com/en-US/support](https://partner.microsoft.com/support)

![](./media/marketplace-publishers-guide/partner-center-log-in-support.png)

Ha a piactér bevezetéséhez és/vagy az ügyfelek használati feladataihoz általában segítségre van szüksége, például hogyan kell beállítani az ügyfél-használati jóváírást, kövesse az alábbi lépéseket:

1. Nyissa meg a [támogatási lapot](https://go.microsoft.com/fwlink/?linkid=844975).

1. A **probléma típusa**területen válassza a **piactér**bevezetése elemet.

1. Válassza ki a probléma kategóriáját:

   - A használati társítással kapcsolatos problémák esetén válassza az **egyéb**lehetőséget.
   - Az Azure Marketplace CPP-vel való hozzáférési problémák esetén válassza a **hozzáférési probléma**lehetőséget.

     ![A probléma kategóriájának kiválasztása](media/marketplace-publishers-guide/lu-article-incident.png)

1. Válassza a **kezdési kérelem**lehetőséget.

1. A következő lapon adja meg a szükséges értékeket. Válassza a **Folytatás** elemet.

1. A következő lapon adja meg a szükséges értékeket.

   > [!Important]
   > Az **incidens címe** mezőben adja meg az **ISV-használat nyomon követését**. A probléma részletes leírása.

   ![Az incidens címéhez adja meg az ISV használatának nyomon követését](media/marketplace-publishers-guide/guid-dev-center-help-hd%201.png)

1. Fejezze be az űrlapot, majd válassza a **Submit (Küldés**) lehetőséget.

A technikai előfizetések, a üzembe helyezési és az alkalmazás-fejlesztési forgatókönyvek technikai útmutatója a Microsoft partneri műszaki tanácsadótól az ügyfelek használati felépítésének megismeréséhez és bevezetéséhez is segítséget nyújt.

### <a name="how-to-submit-a-technical-consultation-request"></a>Technikai tanácsadási kérelem beküldése

1. Keresse [https://aka.ms/TechnicalJourney](https://aka.ms/TechnicalJourney)fel a következőt:.
1. Válassza a felhőalapú infrastruktúra és felügyelet lehetőséget, és egy új oldal nyílik meg a technikai utazás megtekintéséhez.
1. A központi telepítési szolgáltatások területen kattintson a kérelem elküldése gombra.
1. Jelentkezzen be a MSA (MPN-fiók) vagy a HRE (partner irányítópult-fiókkal). a bejelentkezési hitelesítő adatai alapján megnyílik egy online kérelem űrlap:
    * Töltse ki/tekintse át a kapcsolattartási adatokat.
    * A konzultáció részleteit előre ki lehet tölteni, vagy a legördülő listából választhat.
    * Adja meg a címet és a probléma leírását (a lehető legrészletesebben adja meg a problémát).
1. Kattintson a Küldés gombra

Részletes útmutató a képernyőképek [https://aka.ms/TechConsultInstructions](https://aka.ms/TechConsultInstructions)megtekintéséhez.

### <a name="whats-next"></a>A következő lépések

A Microsoft partner technikai tanácsadója felveszi Önnel a kapcsolatot, hogy meghívja az igényeinek megfelelő hatókört.

## <a name="faq"></a>GYIK

**Milyen előnyökkel jár a GUID hozzáadása a sablonhoz?**

A Microsoft olyan partnereket biztosít, akik a megoldásuk és a befolyásolt használatuk alapján megtekintik az ügyfelek központi telepítését. A Microsoft és a partner egyaránt használhatja ezeket az információkat az értékesítési csapatok közötti szorosabb részvételhez. A Microsoft és a partner is használhatja az adatmennyiséget, hogy az egyéni partnereknek az Azure-növekedésre gyakorolt hatásával egységes képet kapjon.

**A GUID hozzáadása után módosítható?**

Igen, az ügyfél vagy a megvalósítási partner testreszabhatja a sablont, és módosíthatja vagy eltávolíthatja a GUID azonosítót. Javasoljuk, hogy a partnerek proaktív módon írják le az erőforrás és a GUID azonosító szerepét az ügyfelek és a partnerek számára, hogy megakadályozzák a GUID eltávolítását vagy szerkesztését. A GUID módosítása csak az új, nem létező, központi telepítéseket és erőforrásokat érinti.

**Követhetem nyomon a nem Microsoft-tárházból telepített sablonokat, például a GitHubot?**

Igen, ha a sablon üzembe helyezése során a GUID szerepel, a rendszer a használatot nyomon követi. A partnereknek az Azure piactéren kívüli üzembe helyezéshez használt GUID azonosítók regisztrálásához a CPP-nek egy profilra van szükségük.

**Az ügyfél jelentéseket is fogad?**

Az ügyfelek nyomon követhetik az egyes erőforrások vagy az ügyfél által meghatározott erőforráscsoportok használatát a Azure Portalon belül.

**A következő módszer hasonló a rekord digitális partneréhez (DPOR)?**

Ez az új módszer, amellyel az üzembe helyezést és a használatot a partneri megoldáshoz csatlakoztathatja, mechanizmust biztosít a partneri megoldások Azure-beli használatra való összekapcsolásához. A DPOR egy tanácsadó (rendszerintegrátor) vagy egy felügyeleti (felügyelt szolgáltató) partner az ügyfél Azure-előfizetésével való hozzárendelésére szolgál.

**Milyen előnyökkel jár az Azure Storage GUID Generator űrlapjának használata?**

Az Azure Storage GUID Generator űrlapja garantált a szükséges formátumú GUID létrehozásához. Emellett, ha az Azure Storage adatsíkja-követési módszereit használja, a piactér-vezérlési síkok nyomon követéséhez ugyanazt a GUID-t használhatja. Ez lehetővé teszi, hogy kihasználja a partnerek egy egységes GUID azonosítóját, anélkül, hogy külön GUID azonosítókat kellene fenntartania.

**Használhatok privát, egyéni VHD-t az Azure piactéren elérhető megoldási sablonhoz?**

Nem. A virtuális gép rendszerképének az Azure Marketplace-ről kell származnia [https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines), lásd:.

Létrehozhat egy virtuálisgép-ajánlatot a piactéren az egyéni VHD használatával, és megjelölheti magánjellegűként, így senki sem láthatja. Ezután hivatkozzon erre a virtuális gépre a megoldás sablonjában.

**Nem sikerült frissíteni a fő sablon *contentVersion* tulajdonságát?**

Bizonyos esetekben valószínűleg hiba fordul elő, ha a sablon üzembe helyezése egy másik sablonból származó TemplateLink használatával történik, amely valamilyen oknál fogva a régebbi contentVersion vár. A megkerülő megoldás a metadata tulajdonság használata:

```
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "contentVersion": "1.0.1.0"
    },
    "parameters": {
```

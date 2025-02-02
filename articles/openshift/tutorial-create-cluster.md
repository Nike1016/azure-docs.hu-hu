---
title: Oktatóanyag – Azure Red Hat OpenShift-fürt létrehozása | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre Microsoft Azure Red Hat OpenShift-fürtöt az Azure CLI használatával
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.topic: tutorial
ms.service: container-service
ms.date: 05/14/2019
ms.openlocfilehash: 4c186787af08a565dc100dfbd79d166688d89d8f
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2019
ms.locfileid: "69013441"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-cluster"></a>Oktatóanyag: Azure Red Hat OpenShift-fürt létrehozása

Ez az oktatóanyag egy sorozat első része. Megtudhatja, hogyan hozhat létre Microsoft Azure Red Hat OpenShift-fürtöt az Azure CLI használatával, méretezheti, majd törölheti az erőforrások törléséhez.

A sorozat első részében az alábbiakkal fog elsajátítani:

> [!div class="checklist"]
> * Azure Red Hat OpenShift-fürt létrehozása

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Azure Red Hat OpenShift-fürt létrehozása
> * [Azure Red Hat OpenShift-fürt skálázása](tutorial-scale-cluster.md)
> * [Azure Red Hat OpenShift-fürt törlése](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Előfeltételek

> [!IMPORTANT]
> Ehhez az oktatóanyaghoz az Azure CLI verziójának 2.0.65 van szükség.
>    
> Az Azure Red Hat OpenShift használata előtt legalább 4 Azure Red Hat OpenShift fenntartott alkalmazás-csomópontot kell vásárolnia az [Azure Red Hat OpenShift fejlesztői környezet beállítása](howto-setup-environment.md#purchase-azure-red-hat-openshift-application-nodes-reserved-instances)című témakörben leírtak szerint.

Az oktatóanyag elkezdése előtt:

Győződjön meg arról, hogy [beállította a fejlesztési környezetet](howto-setup-environment.md), amely a következőket tartalmazza:
- A legújabb CLI telepítése (2.0.65 vagy újabb verzió)
- Bérlő létrehozása, ha még nem rendelkezik ilyennel
- Azure-alkalmazásobjektum létrehozása, ha még nem rendelkezik ilyennel
- Biztonsági csoport létrehozása
- Active Directory felhasználó létrehozása a fürtbe való bejelentkezéshez.

## <a name="step-1-sign-in-to-azure"></a>1\. lépés: Bejelentkezés az Azure-ba

Ha helyileg futtatja az Azure CLI- `az login` t, nyisson meg egy bash Command shellt, és jelentkezzen be az Azure-ba.

```bash
az login
```

 Ha több előfizetéshez is rendelkezik hozzáféréssel, `az account set -s {subscription ID}` futtassa `{subscription ID}` a cserét a használni kívánt előfizetéssel.

## <a name="step-2-create-an-azure-red-hat-openshift-cluster"></a>2\. lépés: Azure Red Hat OpenShift-fürt létrehozása

A bash parancssori ablakban állítsa be a következő változókat:

> [!IMPORTANT]
> Válassza ki a fürt egyedi nevét, és az összes kisbetűs vagy fürt létrehozása sikertelen lesz.

```bash
CLUSTER_NAME=<cluster name in lowercase>
```

Válasszon egy helyet a fürt létrehozásához. Az Azure-OpenShift támogató Azure-régiók listáját a [támogatott régiók](supported-resources.md#azure-regions)című részben tekintheti meg. Például: `LOCATION=eastus`.

```bash
LOCATION=<location>
```

Állítsa `APPID` az [Azure ad-alkalmazás regisztrációjának létrehozása](howto-aad-app-configuration.md#create-an-azure-ad-app-registration)5. lépésében mentett értékre.  

```bash
APPID=<app ID value>
```

Állítsa a "GROUPID" értéket az [Azure ad biztonsági csoport létrehozása](howto-aad-app-configuration.md#create-an-azure-ad-security-group)10. lépésében mentett értékre.

```bash
GROUPID=<group ID value>
```

Állítsa `SECRET` az [ügyfél titkos kulcsának létrehozása](howto-aad-app-configuration.md#create-a-client-secret)8. lépésében mentett értékre.  

```bash
SECRET=<secret value>
```

Állítsa `TENANT` be az [új bérlő létrehozásakor](howto-create-tenant.md#create-a-new-azure-ad-tenant) a 7. lépésben mentett bérlői azonosító értékét  

```bash
TENANT=<tenant ID>
```

Hozza létre a fürthöz tartozó erőforráscsoportot. Futtassa a következő parancsot ugyanabból a bash-rendszerhéjból, amelyet a fenti változók definiálásához használt:

```bash
az group create --name $CLUSTER_NAME --location $LOCATION
```

### <a name="optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network"></a>Nem kötelező: A fürt virtuális hálózatának összekötése meglévő virtuális hálózattal

Ha nem szükséges a létrehozott fürt virtuális hálózatának (VNET) összekötése egy meglévő VNET a társításon keresztül, hagyja ki ezt a lépést.

Ha az alapértelmezett előfizetésen kívüli hálózathoz csatlakozik, akkor az előfizetésben regisztrálnia kell a Microsoft. Tárolószolgáltatás szolgáltatót is. Ehhez futtassa az alábbi parancsot az előfizetésben. Ellenkező esetben, ha a VNET, amely ugyanabban az előfizetésben található, akkor kihagyhatja a regisztrálás lépést. 

`az provider register -n Microsoft.ContainerService --wait`

Először kérje le a meglévő VNET azonosítóját. Az azonosító a (z) formában jelenik `/subscriptions/{subscription id}/resourceGroups/{resource group of VNET}/providers/Microsoft.Network/virtualNetworks/{VNET name}`meg:.

Ha nem ismeri a hálózat nevét vagy az erőforráscsoportot, amelyhez a meglévő VNET tartozik, lépjen a [Virtual Networks (virtuális hálózatok](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Network%2FvirtualNetworks) ) panelre, és kattintson a virtuális hálózatra. Megjelenik a virtuális hálózat lap, és felsorolja a hálózat nevét és a hozzá tartozó erőforráscsoportot.

Definiáljon egy VNET_ID változót a következő CLI-parancs használatával egy BASH-rendszerhéjban:

```bash
VNET_ID=$(az network vnet show -n {VNET name} -g {VNET resource group} --query id -o tsv)
```

Például:`VNET_ID=$(az network vnet show -n MyVirtualNetwork -g MyResourceGroup --query id -o tsv`

### <a name="create-the-cluster"></a>A fürt létrehozása

Most már készen áll a fürt létrehozására. A következő a fürtöt hozza létre a megadott Azure AD-bérlőben, adja meg az Azure AD-alkalmazás objektumát és a titkos kulcsot, amelyet rendszerbiztonsági tagként kíván használni, valamint a biztonsági csoportot, amely a fürthöz rendszergazdai hozzáféréssel rendelkező tagokat tartalmazza.

Ha **nem** látja el a fürtöt egy virtuális hálózattal, használja a következő parancsot:

```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID
```

Ha a fürtöt egy virtuális hálózathoz szeretné felvenni, használja a következő parancsot, amely hozzáadja a `--vnet-peer` jelzőt:
 
```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID --vnet-peer $VNET_ID
```

> [!NOTE]
> Ha hibaüzenet jelenik meg, hogy az állomásnév nem érhető el, előfordulhat, hogy a fürt neve nem egyedi. Próbálja meg törölni az eredeti alkalmazás regisztrációját, és ismételje meg a lépéseket egy másik fürt nevével az [új alkalmazás regisztrációjának létrehozása](howto-aad-app-configuration.md#create-an-azure-ad-app-registration)című témakörben, és hagyja ki az új felhasználó és biztonsági csoport létrehozásának lépéseit.

Néhány perc `az openshift create` elteltével a művelet befejeződik.

### <a name="get-the-sign-in-url-for-your-cluster"></a>A fürt bejelentkezési URL-címének beolvasása

A következő parancs futtatásával szerezze be a fürtbe való bejelentkezéshez szükséges URL-címet:

```bash
az openshift show -n $CLUSTER_NAME -g $CLUSTER_NAME
```

`publicHostName` Keresse meg a következőt a kimenetben, például:`"publicHostname": "openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io"`

A fürt bejelentkezési URL-címét a rendszer az `https://` `publicHostName` érték után fogja követni.  Például: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`.  Ezt az URI-t a következő lépésben fogja használni az alkalmazás regisztrációs átirányítási URI-ja részeként.

## <a name="step-3-update-your-app-registration-redirect-uri"></a>3\. lépés: Az alkalmazás regisztrációs átirányítási URI azonosítójának frissítése

Most, hogy már rendelkezik a fürt bejelentkezési URL-címével, állítsa be az alkalmazás regisztrációjának átirányítási felhasználói felületét:

1. Nyissa meg a [Alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview)panelt.
2. Kattintson az alkalmazás regisztrációs objektumára.
3. Kattintson az **átirányítás URI hozzáadása**lehetőségre.
4. Győződjön meg arról, hogy a **típus** **webes** , és állítsa be az átirányítási `https://<public host name>/oauth2callback/Azure%20AD` **URI** -t a következő minta használatával:. Például:`https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io/oauth2callback/Azure%20AD`
5. Kattintson a **Save** (Mentés) gombra

## <a name="step-4-sign-in-to-the-openshift-console"></a>4\. lépés: Jelentkezzen be a OpenShift-konzolba

Most már készen áll arra, hogy bejelentkezzen az új fürt OpenShift-konzolján. A [OpenShift webkonzol](https://docs.openshift.com/aro/architecture/infrastructure_components/web_console.html) lehetővé teszi a OpenShift-projektek tartalmának megjelenítését, tallózását és kezelését.

Szüksége lesz egy olyan friss böngésző-példányra, amely nem gyorsítótárazta a szokásos módon a Azure Portalba való bejelentkezéshez használt identitást.

1. Nyisson meg egy *inkognitóban* -ablakot (Chrome) vagy InPrivate-ablakot (Microsoft Edge).
2. Navigáljon a fent beszerzett bejelentkezési URL-címhez, például:`https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`

Jelentkezzen be az [új Azure Active Directory-felhasználó létrehozása](howto-aad-app-configuration.md#create-a-new-azure-active-directory-user)szakasz 3. lépésében létrehozott Felhasználónév használatával.

Ekkor megjelenik A **kért engedélyek** párbeszédpanel. Kattintson a jóváhagyva elemre a **szervezet nevében** , majd kattintson az **elfogadás**gombra.

Most bejelentkezett a fürt konzolba.

![A OpenShift-fürt konzoljának képernyőképe](./media/aro-console.png)

 További információ [a OpenShift-konzol használatáról](https://docs.openshift.com/aro/getting_started/developers_console.html) és létrehozásáról a [Red Hat OpenShift](https://docs.openshift.com/aro/welcome/index.html) dokumentációjában.

## <a name="step-5-install-the-openshift-cli"></a>5\. lépés: A OpenShift parancssori felületének telepítése

A [OPENSHIFT CLI](https://docs.openshift.com/aro/cli_reference/get_started_cli.html) (vagy a *oC Tools*) parancsokat biztosít az alkalmazások és az alacsonyabb szintű segédprogramok kezeléséhez a OpenShift-fürt különböző összetevőivel való interakcióhoz.

A OpenShift-konzolon kattintson a jobb felső sarokban látható kérdőjelre a bejelentkezési nevével, és válassza a **parancssori eszközök**lehetőséget.  A **legújabb kiadási** hivatkozásra kattintva töltse le és telepítse a támogatott, a Linux, a MacOS vagy a Windows rendszerhez készült c. verziójú parancssori felületet.

> [!NOTE]
> Ha a jobb felső sarokban nem jelenik meg a kérdőjel ikon, válassza ki a *Service Catalog* vagy az *Application Console* lehetőséget a bal felső legördülő menüből.
>
> Másik lehetőségként közvetlenül is [letöltheti az oC CLI](https://www.okd.io/download.html) -t.

A **parancssori eszközök** lap az űrlap `oc login https://<your cluster name>.<azure region>.cloudapp.azure.com --token=<token value>`parancsát tartalmazza.  A parancs másolásához kattintson a *Másolás a vágólapra* gombra.  A terminál ablakban [állítsa be az elérési utat](https://docs.okd.io/latest/cli_reference/get_started_cli.html#installing-the-cli) úgy, hogy az tartalmazza az oC-eszközök helyi telepítését. Ezután jelentkezzen be a fürtbe az átmásolt oC CLI-parancs használatával.

Ha nem tudta lekérni a jogkivonat értékét a fenti lépésekkel, szerezze be a jogkivonat értékét `https://<your cluster name>.<azure region>.cloudapp.azure.com/oauth/token/request`a következőből:.

## <a name="next-steps"></a>További lépések

Az oktatóanyag jelen részében megismerkedhetett a következőkkel:

> [!div class="checklist"]
> * Azure Red Hat OpenShift-fürt létrehozása

Folytassa a következő oktatóanyaggal:
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift-fürt skálázása](tutorial-scale-cluster.md)

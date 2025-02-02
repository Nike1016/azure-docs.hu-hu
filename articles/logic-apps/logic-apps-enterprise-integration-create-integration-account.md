---
title: A B2B-megoldások integrációs fiókjainak létrehozása és kezelése – Azure Logic Apps
description: A vállalati integrációs és B2B-megoldások integrációs fiókjainak létrehozása, összekapcsolása, áthelyezése és törlése Azure Logic Apps használatával
services: logic-apps
ms.service: logic-apps
ms.workload: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: cffcfe53cf30d8fc34fdb27e50ef74e71700125a
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68607009"
---
# <a name="create-and-manage-integration-accounts-for-b2b-solutions-by-using-azure-logic-apps"></a>B2B-megoldások integrációs fiókjainak létrehozása és kezelése Azure Logic Apps használatával

Mielőtt [vállalati integrációs és B2B megoldásokat](../logic-apps/logic-apps-enterprise-integration-overview.md) építhet ki [Azure Logic apps](../logic-apps/logic-apps-overview.md)használatával, létre kell hoznia egy integrációs fiókot, amely egy különálló Azure-erőforrás, amely biztonságos, méretezhető és kezelhető tárolót biztosít a a logikai alkalmazás munkafolyamataihoz definiált és használt integrációs összetevők.

Például létrehozhat, tárolhat és kezelhet B2B-összetevőket, például kereskedelmi partnereket, szerződéseket, térképeket, sémákat, tanúsítványokat és batch-konfigurációkat. Emellett ahhoz, hogy a logikai alkalmazás használhassa ezeket az összetevőket, és a Logic Apps B2B összekötőket használja, [össze kell kapcsolnia az integrációs fiókot](#link-account) a logikai alkalmazással. Az integrációs fióknak és a logikai alkalmazásnak *ugyanabban* a helyen vagy régióban kell lennie.

> [!TIP]
> Egy integrációs [szolgáltatási környezeten](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)belüli integrációs fiók létrehozásával kapcsolatban lásd: [integrációs fiókok létrehozása ISE-ben](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment).

Ez a témakör bemutatja, hogyan hajthatja végre ezeket a feladatokat:

* Hozzon létre egy integrációs fiókot.
* Összekapcsolhatja az integrációs fiókját egy logikai alkalmazással.
* Módosítsa az integrációs fiók díjszabási szintjét.
* Az integrációs fiók összekapcsolása egy logikai alkalmazásból.
* Helyezze át integrációs fiókját egy másik Azure-erőforráscsoporthoz vagy-előfizetésbe.
* Törölje az integrációs fiókját.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

## <a name="create-integration-account"></a>Integrációs fiók létrehozása

Ehhez a feladathoz a Azure Portal az ebben a szakaszban, [Azure PowerShell](https://docs.microsoft.com//powershell/module/azurerm.logicapp/New-AzureRmIntegrationAccount)vagy [Azure CLI](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-create)-ben leírt lépéseket követve használhatja.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókja hitelesítő adataival.

1. Az Azure főmenüjében válassza az **Erőforrás létrehozása** lehetőséget. A keresőmezőbe írja be szűrőként az "integrációs fiók" kifejezést, majd válassza az **integrációs fiók**lehetőséget.

   ![Új integrációs fiók létrehozása](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

1. Az **integrációs fiók**területen válassza a **Létrehozás**elemet.

   ![Integrációs fiók létrehozásához válassza a Hozzáadás lehetőséget](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

1. Adja meg az integrációs fiókjával kapcsolatos információkat:

   ![Integrációs fiók adatainak megadása](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Tulajdonság | Kötelező | Value | Leírás |
   |----------|----------|-------|-------------|
   | **Name** | Igen | <*integration-account-name*> | Az integrációs fiók neve, például "Fabrikam-Integration" |
   | **Előfizetés** | Igen | <*Azure-előfizetés-neve*> | Az Azure-előfizetés neve |
   | **Erőforráscsoport** | Igen | <*Azure-resource-group-name*> | A kapcsolódó erőforrások rendszerezéséhez használni kívánt [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md) neve. Ebben a példában hozzon létre egy "FabrikamIntegration-RG" nevű új erőforráscsoportot. |
   | **Tarifacsomag** | Igen | <*díjszabás – szint*> | Az integrációs fiók díjszabási szintje, amelyet később módosíthat. Ebben a példában válassza az **ingyenes**lehetőséget. További információkért tekintse meg a következő témaköröket: <p>- [Logic Apps díjszabási modell](../logic-apps/logic-apps-pricing.md#integration-accounts) <p>- [Logic Apps korlátok és konfiguráció](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) <p>- [Díjszabás Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) |
   | **Location** | Igen | <*Azure-régió*> | Az integrációs fiók metaadatait tároló régió. Vagy válassza ki ugyanazt a helyet, mint a logikai alkalmazás, vagy hozza létre a logikai alkalmazásokat az integrációs fiókkal megegyező helyen. Ehhez a példához használja az "USA nyugati régiója" kifejezést. <p>**Megjegyzés**: Egy integrációs [szolgáltatási környezetben (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)belüli integrációs fiók létrehozásához válassza az ISE helyet. További információ: integrációs [fiókok létrehozása ISE-ben](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment). |
   | **Log Analytics** | Nem | Kikapcsolva | Tartsa meg a kikapcsolt beállítást ebben a példában. |
   |||||

1. Ha elkészült, válassza a **Létrehozás**lehetőséget.

   Az üzembe helyezés befejezése után az Azure megnyitja az integrációs fiókját.

   ![Az Azure integrációs fiók megnyitása](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

1. Mielőtt a logikai alkalmazás használni tudja az integrációs fiókját, kövesse a következő lépéseket az integrációs fiók és a logikai alkalmazás összekapcsolásához.

<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Hivatkozás a logikai alkalmazásra

Ahhoz, hogy a logikai alkalmazások hozzáférjenek a B2B-összetevőket tartalmazó integrációs fiókhoz, először csatolnia kell az integrációs fiókot a logikai alkalmazáshoz. Mindkét logikai alkalmazásnak és integrációs fióknak ugyanabban a régióban kell lennie. Ehhez a feladathoz az ebben a részben ismertetett lépéseket követve használhatja a Azure Portal.

1. A Azure Portal keresse meg és nyissa meg a logikai alkalmazást.

1. A [Azure Portal](https://portal.azure.com)nyisson meg egy meglévő logikai alkalmazást, vagy hozzon létre egy új logikai alkalmazást.

1. A logikai alkalmazás menüjében, a **Beállítások**területen válassza a **munkafolyamat-beállítások**elemet. Az **integrációs fiók**területen nyissa meg az **integrációs fiók kiválasztása** listát. Válassza ki a logikai alkalmazáshoz csatolni kívánt integrációs fiókot.

   ![Integrációs fiók kiválasztása](./media/logic-apps-enterprise-integration-create-integration-account/select-integration-account.png)

1. A csatolás befejezéséhez válassza a **Mentés**lehetőséget.

   ![Integrációs fiók kiválasztása](./media/logic-apps-enterprise-integration-create-integration-account/save-link.png)

   Az integrációs fiók sikeres csatolása után az Azure egy megerősítő üzenetet jelenít meg.

   ![Az Azure megerősítette a sikeres hivatkozást](./media/logic-apps-enterprise-integration-create-integration-account/link-confirmation.png)

A logikai alkalmazás most már használhatja az integrációs fiókban található összetevőket, valamint a B2B-összekötőket, például az XML-ellenőrzést és a sima fájl kódolását vagy a dekódolást.  

<a name="change-pricing-tier"></a>

## <a name="change-pricing-tier"></a>Tarifacsomag-váltás

Egy integrációs [](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) fiók korlátainak növeléséhez [frissíthet magasabb díjszabási szintre](#upgrade-pricing-tier), ha van ilyen. Frissíthet például az ingyenes szintről az alapszintű csomagra vagy a standard szintre. Ha van ilyen, [alacsonyabb szintre](#downgrade-pricing-tier)is csökkenthető. További információk a díjszabással kapcsolatban:

* [A Logic Apps díjszabása](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Logic Apps díjszabási modell](../logic-apps/logic-apps-pricing.md#integration-accounts)

<a name="upgrade-pricing-tier"></a>

### <a name="upgrade-pricing-tier"></a>A frissítés díjszabási szintje

A módosítás végrehajtásához a Azure Portal az ebben a részben vagy az [Azure CLI](#upgrade-tier-azure-cli)-ben leírt lépéseket követve használhatja.

#### <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókja hitelesítő adataival.

1. A fő Azure-Keresés mezőbe írja be szűrőként az "integrációs fiókok" kifejezést, majd válassza az **integrációs fiókok**elemet.

   ![Integrációs fiók keresése](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Az Azure megjeleníti az Azure-előfizetések összes integrációs fiókját.

1. Az **integrációs fiókok**területen válassza ki az áthelyezni kívánt integrációs fiókot. Az integrációs fiók menüben válassza az **Áttekintés**lehetőséget.

   ![Az integrációs fiók menüben válassza az "áttekintés" lehetőséget.](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Az Áttekintés ablaktáblán válassza a **frissítés díjszabása**elemet, amely felsorolja az összes elérhető magasabb szintet. Ha kiválaszt egy szintet, a módosítás azonnal érvénybe lép.

<a name="upgrade-tier-azure-cli"></a>

#### <a name="azure-cli"></a>Azure CLI

1. Ha még nem tette meg, [telepítse az Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)előfeltételeit.

1. A Azure Portal nyissa meg az Azure [**Cloud Shell**](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) környezetet.

   ![Az Azure Cloud Shell megnyitása](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. A parancssorba írja be az az [ **Resource** parancsot](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-update), és állítsa `skuName` be a kívánt magasabb szintet.

   ```Azure CLI
   az resource update --resource-group {ResourceGroupName} --resource-type Microsoft.Logic/integrationAccounts --name {IntegrationAccountName} --subscription {AzureSubscriptionID} --set sku.name={SkuName}
   ```
  
   Ha például rendelkezik az alapszintű csomaggal, a következőt `skuName` állíthatja `Standard`be:

   ```Azure CLI
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Standard
   ```

<a name="downgrade-pricing-tier"></a>

### <a name="downgrade-pricing-tier"></a>A visszalépés díjszabási szintje

A módosítás elvégzéséhez használja az [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)-t.

1. Ha még nem tette meg, [telepítse az Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)előfeltételeit.

1. A Azure Portal nyissa meg az Azure [**Cloud Shell**](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) környezetet.

   ![Az Azure Cloud Shell megnyitása](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. A parancssorba írja be az az [ **Resource** parancsot](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-update) , és állítsa `skuName` be a kívánt alsó szintet.

   ```Azure CLI
   az resource update --resource-group <resourceGroupName> --resource-type Microsoft.Logic/integrationAccounts --name <integrationAccountName> --subscription <AzureSubscriptionID> --set sku.name=<skuName>
   ```
  
   Ha például a standard szintű csomaggal rendelkezik, a következőt állíthatja `skuName` be `Basic`:

   ```Azure CLI
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Basic
   ```

## <a name="unlink-from-logic-app"></a>Kapcsolat megszüntetése a logikai alkalmazásból

Ha a logikai alkalmazást egy másik integrációs fiókhoz szeretné kapcsolni, vagy már nem használ integrációs fiókot a logikai alkalmazással, törölje a hivatkozást Azure Erőforrás-kezelő használatával.

1. Nyissa meg a böngészőablakot, és lépjen a [Azure erőforrás-kezelő (https://resources.azure.com)](https://resources.azure.com). Jelentkezzen be ugyanazzal az Azure-fiók hitelesítő adataival.

   ![Azure Resource Explorer](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

1. A keresőmezőbe írja be a logikai alkalmazás nevét, így megkeresheti és kiválaszthatja a logikai alkalmazást.

   ![Logikai alkalmazás keresése és kiválasztása](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

1. Az Explorer címsorán válassza az **írás/írás**lehetőséget.

   ![Olvasási/írási mód bekapcsolása](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-read-write.png)

1. Az **adatlapon válassza** a **Szerkesztés**lehetőséget.

   ![Az "Adatvédelem" lapon válassza a "szerkesztés" lehetőséget.](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-edit.png)

1. A szerkesztőben keresse meg az `integrationAccount` objektumot, és törölje a tulajdonságot, amelynek a formátuma a következő:

   ```json
   {
      // <other-attributes>
      "integrationAccount": {
         "name": "<integration-account-name>",
         "id": "<integration-account-resource-ID>",
         "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   Példa:

   !["IntegrationAccount" objektum keresése](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

1. Az **adatlapon válassza** a **put** lehetőséget a módosítások mentéséhez.

   ![A módosítások mentéséhez válassza a "put" lehetőséget.](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

1. A Azure Portal keresse meg és válassza ki a logikai alkalmazást. Az alkalmazás munkafolyamat- **beállításai**alatt győződjön meg róla, hogy az **integrációs fiók** tulajdonsága most üresen jelenik meg.

   ![Győződjön meg arról, hogy az integrációs fiók nincs csatolva](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Integrációs fiók áthelyezése

Az integrációs fiókját áthelyezheti egy másik Azure-erőforráscsoport vagy Azure-előfizetésbe. Az erőforrások áthelyezésekor az Azure új erőforrás-azonosítókat hoz létre, ezért ügyeljen arra, hogy az új azonosítókat használja helyette, és frissítse az áthelyezett erőforrásokhoz társított parancsfájlokat vagy eszközöket. Ha módosítani szeretné az előfizetést, meg kell adnia egy meglévő vagy egy új erőforráscsoportot is.

Ebben a feladatban a Azure Portal a jelen szakaszban vagy az [Azure CLI](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-move)-ben leírt lépéseket követve használhatja.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókja hitelesítő adataival.

1. A fő Azure-Keresés mezőbe írja be szűrőként az "integrációs fiókok" kifejezést, majd válassza az **integrációs fiókok**elemet.

   ![Integrációs fiók keresése](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Az Azure megjeleníti az Azure-előfizetések összes integrációs fiókját.

1. Az **integrációs fiókok**területen válassza ki az áthelyezni kívánt integrációs fiókot. Az integrációs fiók menüben válassza az **Áttekintés**lehetőséget.

   ![Az integrációs fiók menüben válassza az "áttekintés" lehetőséget.](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Az **erőforráscsoport** vagy az **előfizetés neve**mellett válassza a **módosítás**lehetőséget.

   ![Erőforráscsoport vagy előfizetés módosítása](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

1. Válassza ki az áthelyezni kívánt kapcsolódó erőforrásokat.

1. Az erőforráscsoport vagy előfizetés módosításához kövesse az alábbi lépéseket:

   * Erőforráscsoport: Az **erőforráscsoport** listában válassza ki a cél erőforráscsoportot. Másik erőforráscsoport létrehozásához válassza az **Új erőforráscsoport létrehozása**lehetőséget.

   * Előfizetés: Az **előfizetés** listából válassza ki a cél előfizetést. Az **erőforráscsoport** listában válassza ki a cél erőforráscsoportot. Másik erőforráscsoport létrehozásához válassza az **Új erőforráscsoport létrehozása**lehetőséget.

1. Ha tudomásul veszi, hogy az áthelyezett erőforrásokhoz társított parancsfájlok vagy eszközök nem fognak működni, amíg nem frissíti őket az új erőforrás-azonosítókkal, jelölje be a megerősítő mezőt, majd kattintson az **OK gombra**.

1. A befejezést követően győződjön meg arról, hogy az összes parancsfájlt frissíti az áthelyezett erőforrásokhoz tartozó új erőforrás-azonosítóval.  

## <a name="delete-integration-account"></a>Integrációs fiók törlése

Ehhez a feladathoz az alábbi, az [Azure CLI](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-delete)vagy a [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.logicapp/Remove-AzureRmIntegrationAccount)című szakasz lépéseit követve használhatja a Azure Portal.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókja hitelesítő adataival.

1. A fő Azure-Keresés mezőbe írja be szűrőként az "integrációs fiókok" kifejezést, majd válassza az **integrációs fiókok**elemet.

   ![Integrációs fiók keresése](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Az Azure megjeleníti az Azure-előfizetések összes integrációs fiókját.

1. Az **integrációs fiókok**területen válassza ki a törölni kívánt integrációs fiókot. Az integrációs fiók menüben válassza az **Áttekintés**lehetőséget.

   ![Az integrációs fiók menüben válassza az "áttekintés" lehetőséget.](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Az Áttekintés panelen válassza a **Törlés**lehetőséget.

   ![Az "áttekintés" panelen válassza a "Törlés" lehetőséget.](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

1. Az integrációs fiók törlésének megerősítéséhez válassza az **Igen**lehetőséget.

   ![A törlés megerősítéséhez válassza az Igen lehetőséget.](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>További lépések

* [Kereskedelmi partnerek létrehozása az integrációs fiókban](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Szerződések létrehozása a partnerek között az integrációs fiókban](../logic-apps/logic-apps-enterprise-integration-agreements.md)

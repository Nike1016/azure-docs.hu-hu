---
title: Logikai alkalmazás-sablonok létrehozása az üzembe helyezéshez – Azure Logic Apps
description: Megtudhatja, hogyan hozhat létre Azure Resource Manager-sablonokat az üzembe helyezés automatizálásához Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 07/26/2019
ms.openlocfilehash: 9e62dd25c3ff16e280eda1ad11053ef520a85e4d
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706526"
---
# <a name="create-azure-resource-manager-templates-to-automate-deployment-for-azure-logic-apps"></a>Azure Resource Manager-sablonok létrehozása a Azure Logic Apps üzembe helyezésének automatizálásához

A logikai alkalmazás létrehozásának és üzembe helyezésének automatizálása érdekében ez a cikk bemutatja, hogyan hozhat létre [Azure Resource Manager sablont](../azure-resource-manager/resource-group-overview.md) a logikai alkalmazáshoz. A munkafolyamat-definíciót és az üzembe helyezéshez szükséges egyéb erőforrásokat tartalmazó sablon szerkezetének és szintaxisának áttekintését lásd [: Áttekintés: A Logic apps üzembe helyezésének automatizálása](logic-apps-azure-resource-manager-templates-overview.md)Azure Resource Manager-sablonokkal.

A Azure Logic Apps egy [előre elkészített Logic app-Azure Resource Manager sablont](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) biztosít, amely nem csak a Logic apps létrehozásához használható, hanem az üzembe helyezéshez használt erőforrások és paraméterek meghatározására is. Ezt a sablont használhatja saját üzleti céljaihoz, vagy testreszabhatja a sablont az igényeinek megfelelően.

> [!IMPORTANT]
> Győződjön meg arról, hogy a sablonban lévő kapcsolatok ugyanazt az Azure-erőforráscsoportot és-helyet használják, mint a logikai alkalmazás.

A Azure Resource Manager-sablonokkal kapcsolatos további információkért tekintse meg a következő témaköröket:

* [Azure Resource Manager sablon szerkezete és szintaxisa](../azure-resource-manager/resource-group-authoring-templates.md)
* [Azure Resource Manager sablonok szerzője](../azure-resource-manager/resource-group-authoring-templates.md)
* [Azure Resource Manager-sablonok fejlesztése felhőkonzisztenciához](../azure-resource-manager/templates-cloud-consistency.md)

<a name="visual-studio"></a>

## <a name="create-templates-with-visual-studio"></a>Sablonok létrehozása a Visual Studióval

A legegyszerűbben az üzembe helyezésre kész, a Visual Studio (ingyenes Community Edition vagy újabb verzió) és a Visual studióhoz készült Azure Logic Apps Tools segítségével hozhatja létre az érvényes paraméteres logikai alkalmazások sablonját. Ezután [létrehozhatja a logikai alkalmazást a Visual Studióban](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) , vagy megkeresheti [és letöltheti a Azure Portal meglévő logikai alkalmazást a Visual studióba](../logic-apps/manage-logic-apps-with-visual-studio.md).

A logikai alkalmazás letöltésével olyan sablont kap, amely tartalmazza a logikai alkalmazás és más erőforrások, például a kapcsolatok definícióit. A sablon emellett *felparaméterezi*vagy paramétereket is definiál, a logikai alkalmazás és az egyéb erőforrások üzembe helyezéséhez használt értékeket. A paraméterek értékeit egy külön Parameters fájlban adhatja meg. Így könnyebben módosíthatja ezeket az értékeket a telepítési igények alapján. További információkért tekintse meg a következő témaköröket:

* [Logikai alkalmazások létrehozása a Visual Studióval](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Logikai alkalmazások kezelése a Visual Studióval](../logic-apps/manage-logic-apps-with-visual-studio.md)

<a name="azure-powershell"></a>

## <a name="create-templates-with-azure-powershell"></a>Sablonok létrehozása Azure PowerShell

A Resource Manager-sablonokat a [LogicAppTemplate modullal](https://github.com/jeffhollan/LogicAppTemplateCreator)Azure PowerShell használatával is létrehozhatja. Ez a nyílt forráskódú modul először kiértékeli a logikai alkalmazást és a logikai alkalmazás által használt kapcsolatokat. A modul ezután létrehozza a sablon erőforrásait az üzembe helyezéshez szükséges paraméterekkel.

Tegyük fel például, hogy van egy logikai alkalmazás, amely egy Azure Service Bus üzenetsor üzenetét fogadja, és feltölti az adatait egy Azure SQL Database-adatbázisba. A modul megőrzi az összes összehangoló logikát, és felparaméterezi az SQL és Service Bus kapcsolódási karakterláncokat, így a telepítési igények alapján megadhatja és módosíthatja ezeket az értékeket.

Ezek a minták azt mutatják be, hogyan hozhat létre és helyezhet üzembe logikai alkalmazásokat Azure Resource Manager sablonok, Azure-folyamatok Azure DevOps és Azure PowerShell használatával:

* [Minta: Kapcsolódás Azure Service Bus várólistákhoz Azure Logic Apps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Minta: Kapcsolódás Azure Storage-fiókokhoz Azure Logic Apps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Minta: Function app-művelet beállítása Azure Logic Appshoz](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Minta: Csatlakozás egy integrációs fiókhoz Azure Logic Apps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

### <a name="install-powershell-modules"></a>PowerShell-modulok telepítése

1. Ha még nem tette meg, telepítse a [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

1. A LogicAppTemplate modul PowerShell-galériaból történő telepítésének legegyszerűbb módja a [](https://www.powershellgallery.com/packages/LogicAppTemplate)következő parancs futtatása:

   ```text
   PS> Install-Module -Name LogicAppTemplate
   ```

   A legújabb verzióra való frissítéshez futtassa a következő parancsot:

   ```text
   PS> Update-Module -Name LogicAppTemplate
   ```

A manuális telepítéshez kövesse a következő témakör lépéseit: GitHub for [Logic app template Creator](https://github.com/jeffhollan/LogicAppTemplateCreator).

### <a name="install-azure-resource-manager-client"></a>Azure Resource Manager-ügyfél telepítése

Ahhoz, hogy a LogicAppTemplate modul bármilyen Azure-Bérlővel és előfizetési hozzáférési jogkivonattal működjön, telepítse az [Azure Resource Manager-ügyfél eszközt](https://github.com/projectkudu/ARMClient), amely egy egyszerű parancssori eszköz, amely meghívja a Azure Resource Manager API-t.

Ha ezzel az eszközzel `Get-LogicAppTemplate` futtatja a parancsot, a parancs először egy hozzáférési jogkivonatot kap a ARMClient eszközön keresztül, a tokent a PowerShell-parancsfájlba, majd a sablont JSON-fájlként hozza létre. Az eszközzel kapcsolatos további információkért tekintse meg ezt [a cikket a Azure Resource Manager-ügyfél eszközről](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html).

### <a name="generate-template-with-powershell"></a>Sablon készítése a PowerShell-lel

A sablon létrehozásához a LogicAppTemplate modul telepítése után futtassa ezt a PowerShell-parancsot:

```text
PS> Get-LogicAppTemplate
```

Ha követni szeretné a [Azure Resource Manager-ügyfél eszközén](https://github.com/projectkudu/ARMClient)található adatcsövekre vonatkozó javaslatot, futtassa ezt a parancsot `$SubscriptionId` ahelyett, hogy az Azure-előfizetés azonosítója:

```text
PS> armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp <logic-app-name> -ResourceGroup <Azure-resource-group-name> -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json
```

A kinyerés után a következő parancs futtatásával létrehozhat egy paramétereket tartalmazó fájlt a sablonból:

```text
PS> Get-ParameterTemplate -TemplateFile $filename | Out-File '<parameters-file-name>.json'
```

Azure Key Vault hivatkozásokkal történő kinyeréshez (csak statikus) futtassa ezt a parancsot:

```text
PS> Get-ParameterTemplate -TemplateFile $filename -KeyVault Static | Out-File $fileNameParameter
```

| Paraméterek | Kötelező | Leírás |
|------------|----------|-------------|
| TemplateFile | Igen | A sablon fájljának elérési útja |
| KeyVault | Nem | Egy felsorolás, amely leírja, hogyan kell kezelni a lehetséges kulcstároló-értékeket. A mező alapértelmezett értéke: `None`. |
||||

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Logic app-sablonok üzembe helyezése](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)

---
title: Logikai alkalmazások erőforrásainak áthelyezése előfizetések, erőforráscsoportok vagy régiók között – Azure Logic Apps
description: Logikai alkalmazások vagy integrációs fiókok áttelepíthetők más Azure-előfizetésekre,-erőforráscsoportok vagy-helyszínekre (régiók)
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: e7c201004be6c4d39f482cc288824cba74e302cb
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2019
ms.locfileid: "68737497"
---
# <a name="migrate-logic-app-resources-to-other-azure-subscriptions-resource-groups-or-regions"></a>Logic apps-erőforrások áttelepíthetők más Azure-előfizetésekre,-erőforráscsoportok vagy-régiókba

Ha át szeretné helyezni a logikai alkalmazást vagy a kapcsolódó erőforrásokat egy másik Azure-előfizetésbe, erőforráscsoporthoz vagy régióba, többféleképpen is elvégezheti ezeket a feladatokat, például a Azure Portal, az Azure PowerShell, az Azure CLI és a REST API. Az erőforrások áthelyezése előtt tekintse át a következő szempontokat: 

* Az Azure-erőforráscsoportok és-előfizetések között csak [bizonyos logikai alkalmazás-erőforrástípusok](../azure-resource-manager/move-support-resources.md#microsoftlogic) helyezhetők át.

* Tekintse [](../logic-apps/logic-apps-limits-and-config.md) meg az Azure-előfizetésében és az egyes Azure-régiókban elérhető logikai alkalmazások erőforrásainak számát. Ezek a korlátozások hatással vannak arra, hogy adott erőforrástípusok áthelyezhetők-e, ha a régió az előfizetések és az erőforráscsoportok esetében is ugyanaz marad. Az egyes Azure-előfizetésekben például csak egy ingyenes rétegbeli integrációs fiók lehet.

* Az erőforrások áthelyezésekor az Azure új erőforrás-azonosítókat hoz létre. Ezért ügyeljen arra, hogy az új azonosítókat használja helyette, és frissítse az áthelyezett erőforrásokhoz társított parancsfájlokat vagy eszközöket. Miután áthelyezte a logikai alkalmazásokat az előfizetések, erőforráscsoportok vagy régiók között, újra létre kell hoznia vagy újra engedélyeznie kell a OAuth-alapú kapcsolatokat.

## <a name="prerequisites"></a>Előfeltételek

* Ugyanazt az Azure-előfizetést, amelyet az áthelyezni kívánt logikai alkalmazás vagy integrációs fiók létrehozásához használt.

* Erőforrás-tulajdonos engedélyei a kívánt erőforrások áthelyezéséhez és beállításához. További információ a [szerepköralapú hozzáférés-vezérlésről (RBAC)](../role-based-access-control/built-in-roles.md#owner).

<a name="move-subscription"></a>

## <a name="move-resources-between-subscriptions"></a>Erőforrások áthelyezése az előfizetések között

Ha egy erőforrást, például logikai alkalmazást vagy integrációs fiókot szeretne áthelyezni egy másik Azure-előfizetésbe, használhatja a Azure Portal, Azure PowerShell, az Azure CLI vagy a REST API. Ezek a lépések lefedik a Azure Portal, amelyeket akkor használhat, ha az erőforrás régiója ugyanaz marad. További lépések és általános felkészülés: [erőforrások áthelyezése új erőforráscsoporthoz vagy](../azure-resource-manager/resource-group-move-resources.md)előfizetésbe.

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki az áthelyezni kívánt logikai alkalmazás erőforrását.

1. Az erőforrás **Áttekintés** lapján az **előfizetés**elem mellett válassza a **módosítás** hivatkozást.

1. Az **erőforrások áthelyezése** lapon válassza ki a logikai alkalmazás erőforrását és az áthelyezni kívánt kapcsolódó erőforrásokat.

1. Az **előfizetés** listából válassza ki a cél előfizetést.

1. Az **erőforráscsoport** listában válassza ki a cél erőforráscsoportot. Másik erőforráscsoport létrehozásához válassza az **új csoport létrehozása**lehetőséget.

1. Ha szeretné megerősíteni, hogy az áthelyezett erőforrásokhoz társított parancsfájlok vagy eszközök nem fognak működni, amíg nem frissíti őket az új erőforrás-azonosítókkal, jelölje be a megerősítő mezőt, majd kattintson az **OK gombra**.

<a name="move-resource-group"></a>

## <a name="move-resources-between-resource-groups"></a>Erőforrások áthelyezése az erőforráscsoportok között

Ha egy erőforrást, például logikai alkalmazást vagy integrációs fiókot szeretne áthelyezni egy másik Azure-erőforráscsoporthoz, használhatja a Azure Portal, Azure PowerShell, az Azure CLI vagy a REST API. Ezek a lépések lefedik a Azure Portal, amelyeket akkor használhat, ha az erőforrás régiója ugyanaz marad. További lépések és általános felkészülés: [erőforrások áthelyezése új erőforráscsoporthoz vagy](../azure-resource-manager/resource-group-move-resources.md)előfizetésbe.

Mielőtt ténylegesen áthelyezi az erőforrásokat a csoportok között, tesztelheti, hogy sikeresen át tudja-e helyezni az erőforrást egy másik csoportba. További információ: [az áthelyezés ellenőrzése](../azure-resource-manager/resource-group-move-resources.md#validate-move).

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki az áthelyezni kívánt logikai alkalmazás erőforrását.

1. Az erőforrás **Áttekintés** lapján, az **erőforráscsoport**mellett válassza a **módosítás** hivatkozást.

1. Az **erőforrások áthelyezése** lapon válassza ki a logikai alkalmazás erőforrását és az áthelyezni kívánt kapcsolódó erőforrásokat.

1. Az **erőforráscsoport** listában válassza ki a cél erőforráscsoportot. Másik erőforráscsoport létrehozásához válassza az **új csoport létrehozása**lehetőséget.

1. Ha szeretné megerősíteni, hogy az áthelyezett erőforrásokhoz társított parancsfájlok vagy eszközök nem fognak működni, amíg nem frissíti őket az új erőforrás-azonosítókkal, jelölje be a megerősítő mezőt, majd kattintson az **OK gombra**.

<a name="move-location"></a>

## <a name="move-resources-between-regions"></a>Erőforrások áthelyezése régiók között

Ha egy logikai alkalmazást egy másik régióba szeretne áthelyezni, a lehetőségek attól függnek, hogy milyen módon hozta létre a logikai alkalmazást. A választott lehetőség alapján újra létre kell hoznia vagy újra engedélyeznie kell a logikai alkalmazásban lévő kapcsolatokat.

* A Azure Portal hozza létre újra a logikai alkalmazást az új régióban, és konfigurálja újra a munkafolyamat beállításait. Időt takaríthat meg, ha átmásolja a mögöttes munkafolyamat-definíciót és a forrás alkalmazásban lévő kapcsolatokat a cél alkalmazásba. A "kód" logikai alkalmazás mögötti megtekintéséhez a Logic app Designer eszköztárán válassza a **kód nézet**lehetőséget.

* A Visual Studióval és a Visual studióhoz készült Azure Logic Apps eszközökkel megnyithatja [és letöltheti a logikai alkalmazást](../logic-apps/manage-logic-apps-with-visual-studio.md) a Azure Portalból [Azure Resource Manager sablonként](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md). Ez a sablon többnyire készen áll az üzembe helyezésre, és tartalmazza a logikai alkalmazáshoz tartozó erőforrás-definíciókat, beleértve a munkafolyamatot és a kapcsolatokat. A sablon a telepítéskor használandó értékek paramétereit is deklarálja. Így könnyebben megváltoztathatja, hogy hol és hogyan helyezi üzembe a logikai alkalmazást az igényei alapján. A hely és a telepítéshez szükséges egyéb információk megadásához külön paraméter-fájlt használhat.

* Ha a logikai alkalmazást a folyamatos integráció (CI) és a folyamatos továbbítási (CD) eszközök, például az Azure DevOps használatával hozta létre és telepítette, az alkalmazást az eszközök használatával egy másik régióba helyezheti.

A Logic apps üzembe helyezési sablonjaival kapcsolatos további információkért tekintse meg a következő témaköröket:

* [Áttekintés Üzembe helyezés automatizálása Azure Logic Appshez Azure Resource Manager sablonok használatával](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [A logikai alkalmazás megkeresése, megnyitása és letöltése a Azure Portalból a Visual studióba](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [Azure Resource Manager-sablonok létrehozása a Azure Logic Appshoz](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Azure Logic Apps Azure Resource Manager sablonjainak üzembe helyezése](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)

### <a name="related-resources"></a>Kapcsolódó erőforrások

Egyes Azure-erőforrások, például a helyszíni adatátjáró-erőforrások az Azure-ban olyan régióban létezhetnek, amely eltér az erőforrásokat használó logikai alkalmazástól. A többi Azure-erőforrásnak, például a társított integrációs fiókoknak azonban ugyanabban a régióban kell lenniük, mint a logikai alkalmazásoknak. A forgatókönyv alapján győződjön meg arról, hogy a logikai alkalmazások el tudják érni azokat az erőforrásokat, amelyeknek az alkalmazásai várhatóan ugyanabban a régióban vannak.

Ahhoz például, hogy egy logikai alkalmazást egy integrációs fiókhoz lehessen kapcsolni, mindkét erőforrásnak ugyanabban a régióban kell lennie. Olyan helyzetekben, mint például a vész-helyreállítás, általában olyan integrációs fiókokra van szükség, amelyek azonos konfigurációval és összetevőkkel rendelkeznek. Más esetekben előfordulhat, hogy különböző konfigurációkkal és összetevőkkel rendelkező integrációs fiókokra van szükség.

A Azure Logic Appsban lévő egyéni összekötők láthatók a szerzők és a felhasználók számára, akik ugyanazzal az Azure-előfizetéssel és azonos Azure Active Directory Bérlővel rendelkeznek. Ezek az összekötők ugyanabban a régióban érhetők el, ahol a Logic apps telepítve van. További információért lásd az [egyéni összekötők vállalaton belüli megosztását](https://docs.microsoft.com/connectors/custom-connectors/share) ismertető cikket.

A Visual studióból beolvasott sablon csak a logikai alkalmazás erőforrás-definícióit és annak kapcsolatait tartalmazza. Tehát ha a logikai alkalmazás más erőforrásokat használ, például egy integrációs fiókot és egy B2B-összetevőt, például a partnereket, a szerződéseket és a sémákat, akkor a Azure Portal használatával kell exportálnia az integrációs fiók sablonját. Ez a sablon az integrációs fiók és az összetevők erőforrás-definícióit tartalmazza. A sablon azonban nem teljes mértékben paraméteres. Ezért manuálisan kell parametrizálja az üzembe helyezéshez használni kívánt értékeket.

### <a name="export-templates-for-integration-accounts"></a>Sablonok exportálása integrációs fiókokhoz

1. A [Azure Portal](https://portal.azure.com)keresse meg és nyissa meg az integrációs fiókját.

1. Az integrációs fiók menüjének **Beállítások**területén válassza a **sablon exportálása**lehetőséget.

1. Az eszköztáron válassza a **Letöltés**lehetőséget, és mentse a sablont.

1. Nyissa meg és szerkessze a sablont, hogy parametrizálja a szükséges értékeket az üzembe helyezéshez.

## <a name="next-steps"></a>További lépések

[Azure-erőforrások áthelyezése új erőforráscsoportok vagy előfizetések](../azure-resource-manager/resource-group-move-resources.md)
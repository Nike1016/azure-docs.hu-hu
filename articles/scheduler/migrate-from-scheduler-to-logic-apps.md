---
title: Áttelepítés az Azure Schedulerből a Azure Logic Appsba
description: Megtudhatja, hogyan cserélheti le a feladatokat az Azure Scheduler szolgáltatásban, Azure Logic Apps
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/20/2018
ms.openlocfilehash: 0225a9f34e016a4b1de51c06ba982d384e41007c
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302077"
---
# <a name="migrate-azure-scheduler-jobs-to-azure-logic-apps"></a>Azure Scheduler-feladatok migrálása a Azure Logic Appsba

> [!IMPORTANT]
> Azure Logic Apps az Azure Scheduler cseréje folyamatban van. A feladatok elvégzéséhez kövesse ezt a cikket, ha inkább a Azure Logic Apps szeretné áthelyezni.

Ez a cikk bemutatja, hogyan ütemezhet egyszeri és ismétlődő feladatokat úgy, hogy az Azure Scheduler helyett Azure Logic Apps használatával automatizált munkafolyamatokat hoz létre. Ha Logic Apps-val ütemezett feladatokat hoz létre, a következő előnyöket kapja:

* Nem kell aggódnia a *feladatütemezés* fogalmával kapcsolatban, mert minden logikai alkalmazás egy különálló Azure-erőforrás.

* Egyetlen logikai alkalmazás használatával több egyszeri feladatot is futtathat.

* A Azure Logic Apps szolgáltatás az időzónát és a nyári időszámítást (DST) is támogatja.

További információ: [What is Azure Logic apps?](../logic-apps/logic-apps-overview.md) vagy próbálja meg létrehozni az első logikai alkalmazást ebben a rövid útmutatóban: [Hozza létre az első logikai alkalmazását](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>.

* A logikai alkalmazás HTTP-kérelmek küldésével történő elindításához használjon olyan eszközt, mint például a [Poster Desktop alkalmazás](https://www.getpostman.com/apps).

## <a name="schedule-one-time-jobs"></a>Egyszeri feladatok beütemezett feladatai

Egyetlen logikai alkalmazás létrehozásával több egyszeri feladatot is futtathat. 

### <a name="create-your-logic-app"></a>A logikai alkalmazás létrehozása

1. A [Azure Portal](https://portal.azure.com)hozzon létre egy üres logikai alkalmazást a Logic app Designerben. 

   Az alapszintű lépések végrehajtásához kövesse az alábbi [lépéseket: Hozza létre az első logikai](../logic-apps/quickstart-create-first-logic-app-workflow.md)alkalmazását.

1. A keresőmezőbe írja be a "http-kérelem" kifejezést a szűrőként. Az eseményindítók listából válassza ki a következő eseményindítót: **HTTP-kérés fogadásakor** 

   !["Kérelem" trigger hozzáadása](./media/migrate-from-scheduler-to-logic-apps/request-trigger.png)

1. A kérelem-trigger esetében megadhat egy JSON-sémát is, amely segít a Logic app designernek megérteni a bejövő kérés bemeneteit, és egyszerűbbé teszi a kimenetek kijelölését a munkafolyamat későbbi részében.

   Séma megadásához írja be a sémát a **kérelem törzse JSON** -sémája mezőbe, például: 

   ![Kérelem sémája](./media/migrate-from-scheduler-to-logic-apps/request-schema.png)

   Ha nem rendelkezik sémával, de JSON formátumú minta-adattartalommal rendelkezik, létrehozhat egy sémát ebből a hasznos adatból.

   1. A kérelem triggerben válassza a **minta hasznos adatok használata a séma létrehozásához**lehetőséget.

   1. Az **írja be vagy illessze be a minta JSON**-adattartalmat területen adja meg a minta hasznos adatait, majd válassza a **kész**lehetőséget, például:

      ![Minta hasznos adat](./media/migrate-from-scheduler-to-logic-apps/sample-payload.png)

1. A trigger alatt válassza a **következő lépés**lehetőséget. 

1. A keresőmezőbe írja be a "delay to ig" kifejezést a szűrőként. A műveletek listában válassza ki ezt a műveletet: **Késleltetési idő**

   Ez a művelet a logikai alkalmazás munkafolyamatát szünetelteti a megadott dátumig és időpontig.

   !["Késleltetési idő" művelet hozzáadása](./media/migrate-from-scheduler-to-logic-apps/delay-until.png)

1. Adja meg az időbélyeget, amikor el szeretné indítani a logikai alkalmazás munkafolyamatát. 

   Ha az **időbélyegző** mezőbe kattint, megjelenik a dinamikus tartalom lista, így kiválaszthatja az trigger kimenetét is.

   ![Adja meg a "késleltetési idő" részleteit](./media/migrate-from-scheduler-to-logic-apps/delay-until-details.png)

1. Adja meg a futtatni kívánt egyéb műveleteket [több száz használatra kész összekötő](../connectors/apis-list.md)közül. 

   Megadhat például egy HTTP-műveletet, amely egy kérést küld egy URL-címre, vagy olyan műveleteket, amelyek a tárolási várólistákkal, Service Bus várólistákkal vagy Service Bus témakörökkel működnek: 

   ![HTTP-művelet](./media/migrate-from-scheduler-to-logic-apps/request-http-action.png)

1. Ha elkészült, mentse a logikai alkalmazást.

   ![A logikai alkalmazás mentése](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

   Amikor első alkalommal menti a logikai alkalmazást, a logikai alkalmazás kérelem-triggeréhez tartozó végpont URL-címe megjelenik a **http post URL-címe** mezőben. 
   Ha meg szeretné hívni a logikai alkalmazást, és adatokat szeretne küldeni a logikai alkalmazásnak feldolgozásra, használja ezt az URL-címet a hívás célhelyként.

   ![Kérelem-indító végpont URL-címének mentése](./media/migrate-from-scheduler-to-logic-apps/request-endpoint-url.png)

1. Másolja és mentse ezt a végponti URL-címet, hogy később a logikai alkalmazást indító manuális kérést küldjön. 

## <a name="start-a-one-time-job"></a>Egyszeri feladattípus indítása

Egyszeri feladatok manuális futtatásához vagy elindításához küldje el a logikai alkalmazás kérelem-triggerének végponti URL-címét. Ebben a hívásban adja meg a küldeni kívánt bemenetet vagy adattartalmat, amelyet korábban a séma megadásával ismertetett. 

Ha például a Poster alkalmazást használja, létrehozhat egy POST-kérelmet a mintához hasonló beállításokkal, majd a **Küldés** gombra kattintva elvégezheti a kérést.

| Kérelem metódusa | URL | Törzs | Fejlécek |
|----------------|-----|------|---------| 
| **POST** | <*végpont – URL*> | **nyers** <p>**JSON(application/json)** <p>A **nyers** mezőben adja meg a kérelemben elküldeni kívánt adattartalmat. <p>**Megjegyzés**: Ez a beállítás automatikusan beállítja a **fejlécek** értékeit. | **Kulcs**: Content-Type <br>**Érték**: alkalmazás/JSON
 |||| 

![Kérelem küldése a logikai alkalmazás manuális elindításához](./media/migrate-from-scheduler-to-logic-apps/postman-send-post-request.png)

A hívás elküldése után a logikai alkalmazás válasza megjelenik a **törzs** lap **nyers** mezőjében. 

<a name="workflow-run-id"></a>

> [!IMPORTANT]
>
> Ha később szeretné megszakítani a feladatot, válassza a **fejlécek** lapot. Keresse meg és másolja az **x-MS-workflow-Run-ID** fejléc értékét a válaszban. 
>
> ![Válasz](./media/migrate-from-scheduler-to-logic-apps/postman-response.png)

## <a name="cancel-a-one-time-job"></a>Egyszeri feladat megszakítása

Logic Apps minden egyszeri feladatot egyetlen logikai alkalmazás futtatási példányként hajt végre. Egyszeri feladat megszakításához használhatja a Logic Apps REST APIban a [munkafolyamat-futtatások megszakítását](https://docs.microsoft.com/rest/api/logic/workflowruns/cancel) . Ha elküld egy hívást az triggernek, adja meg a [munkafolyamat futtatási azonosítóját](#workflow-run-id).

## <a name="schedule-recurring-jobs"></a>Ismétlődő feladatok ütemezése

### <a name="create-your-logic-app"></a>A logikai alkalmazás létrehozása

1. A [Azure Portal](https://portal.azure.com)hozzon létre egy üres logikai alkalmazást a Logic app Designerben. 

   Az alapszintű lépések végrehajtásához kövesse az alábbi [lépéseket: Hozza létre az első logikai](../logic-apps/quickstart-create-first-logic-app-workflow.md)alkalmazását.

1. A keresőmezőbe írja be a "Ismétlődés" kifejezést a szűrőként. Az eseményindítók listából válassza ki a következő eseményindítót: **Ismétlődés** 

   !["Ismétlődés" eseményindító hozzáadása](./media/migrate-from-scheduler-to-logic-apps/recurrence-trigger.png)

1. Ha szeretné, állítsa be a fejlettebb ütemtervet.

   ![Speciális ütemterv](./media/migrate-from-scheduler-to-logic-apps/recurrence-advanced-schedule.png)

   További információ a speciális ütemezési lehetőségekről: [ismétlődő feladatok és munkafolyamatok létrehozása és futtatása a Azure Logic apps](../connectors/connectors-native-recurrence.md)

1. További műveleteket is hozzáadhat, ha [több száz használatra kész](../connectors/apis-list.md)alkalmazást választ ki. A trigger alatt válassza a **következő lépés**lehetőséget. Keresse meg és válassza ki a kívánt műveleteket.

   Megadhat például egy HTTP-műveletet, amely egy kérést küld egy URL-címre, vagy olyan műveleteket, amelyek a tárolási várólistákkal, Service Bus várólistákkal vagy Service Bus témakörökkel működnek: 

   ![HTTP-művelet](./media/migrate-from-scheduler-to-logic-apps/recurrence-http-action.png)

1. Ha elkészült, mentse a logikai alkalmazást.

   ![A logikai alkalmazás mentése](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

## <a name="advanced-setup"></a>Speciális beállítás

Az alábbi módokon testreszabhatja a feladatait.

### <a name="retry-policy"></a>Újrapróbálkozási szabályzat

Annak szabályozásához, hogy egy művelet megpróbáljon újrafutni a logikai alkalmazásban az időszakos hibák bekövetkezésekor, beállíthatja az újrapróbálkozási [szabályzatot](../logic-apps/logic-apps-exception-handling.md#retry-policies) minden egyes művelet beállításaiban, például:

1. Nyissa meg a művelet ( **..** .) menüt, és válassza a **Beállítások**lehetőséget.

   ![Művelet beállításainak megnyitása](./media/migrate-from-scheduler-to-logic-apps/action-settings.png)

1. Válassza ki a kívánt újrapróbálkozási szabályzatot. Az egyes szabályzatokról további információt az újrapróbálkozási [szabályzatok](../logic-apps/logic-apps-exception-handling.md#retry-policies)című témakörben talál.

   ![Újrapróbálkozási házirend kiválasztása](./media/migrate-from-scheduler-to-logic-apps/retry-policy.png)

## <a name="handle-exceptions-and-errors"></a>Kivételek és hibák kezelése

Ha az Azure Schedulerben nem sikerül futtatni az alapértelmezett műveletet, futtathat egy vértisztító műveletet, amely a hiba feltételét tárgyalja. A Azure Logic Appsban ugyanezt a feladatot is végrehajthatja.

1. A Logic app Designerben a kezelni kívánt művelet felett vigye az egérmutatót a lépések közötti nyíl fölé, és válassza ki és **adja hozzá a párhuzamos ágat**. 

   ![Párhuzamos ág hozzáadása](./media/migrate-from-scheduler-to-logic-apps/add-parallel-branch.png)

1. Keresse meg és válassza ki a futtatni kívánt műveletet az alternatív művelet helyett.

   ![Párhuzamos művelet hozzáadása](./media/migrate-from-scheduler-to-logic-apps/add-parallel-action.png)

1. Az alternatív műveletnél nyissa meg a ( **..** .) menüt, majd válassza a **Futtatás konfigurálás után**lehetőséget.

   ![Futtatás konfigurálása a következő után](./media/migrate-from-scheduler-to-logic-apps/configure-run-after.png)

1. Törölje a **sikeres** tulajdonság mezőjét. Válassza ki a következő tulajdonságokat: **nem sikerült**, ki **van hagyva**, és túllépte az időkorlátot

   ![A "Futtatás utáni" tulajdonságok beállítása](./media/migrate-from-scheduler-to-logic-apps/select-run-after-properties.png)

1. Ha elkészült, válassza a **Kész** lehetőséget.

További információ a kivételek kezeléséről: [hibák és kivételek kezelése – RunAfter tulajdonság](../logic-apps/logic-apps-exception-handling.md#catch-and-handle-failures-with-the-runafter-property).

## <a name="faq"></a>GYIK

<a name="retire-date"></a> 

**K**: Mikor kell kivonulni az Azure Scheduler? <br>
**A**: Az Azure Scheduler 2019 szeptember 30-ig van ütemezve.

**K**: Mi történik a Scheduler-feladatok gyűjteményével és feladataival a szolgáltatás újragumiabroncsa után? <br>
**A**: A rendszer törli az összes Scheduler-feladat gyűjteményét és feladatát.

**K**: Szükség van-e biztonsági mentésre vagy egyéb feladatok elvégzésére, mielőtt áttelepíti a Feladatütemező feladatait a Logic Apps? <br>
**A**: Ajánlott eljárásként mindig készítse elő a munkáját. Győződjön meg arról, hogy a létrehozott logikai alkalmazások a várt módon futnak a Scheduler-feladatok törlése vagy letiltása előtt. 

**K**: Van olyan eszköz, amely segíthet a feladatok áttelepíthetők a feladatütemezőből a Logic Appsba? <br>
**A**: Az egyes ütemező feladatok egyediek, így nem létezik egy egyméretű eszköz. Azonban különböző szkriptek lesznek elérhetők az igényeinek megfelelően. A parancsfájlok rendelkezésre állása esetén térjen vissza később.

**K**: Hol kaphatok támogatást a Scheduler-feladatok áttelepítéséhez? <br>
**A**: Íme néhány lehetőség a támogatás beszerzésére: 

**Azure Portal**

Ha az Azure-előfizetése fizetős támogatási csomaggal rendelkezik, technikai támogatási kérést hozhat létre a Azure Portal. Ellenkező esetben választhat másik támogatási lehetőséget is.

1. A [Azure Portal](https://portal.azure.com) Főmenüben válassza a **Súgó + támogatás**lehetőséget.

1. A **támogatás**területen válassza az **új támogatási kérelem**lehetőséget. Adja meg az alábbi adatokat a kérelméhez:

   | Beállítás | Value |
   |---------|-------|
   | **Probléma típusa** | **Technikai** | 
   | **Előfizetés** | <*your-Azure-subscription*> | 
   | **Szolgáltatás** | A **figyelés &** felügyelet területen válassza az ütemező lehetőséget. | 
   ||| 

1. Válassza ki a kívánt támogatási lehetőséget. Ha fizetős támogatási csomaggal rendelkezik, válassza a **tovább**lehetőséget.

**Közösségi**

* [Azure Logic Apps fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-scheduler)

## <a name="next-steps"></a>További lépések

* [Rendszeresen futó feladatok és munkafolyamatok létrehozása Azure Logic Apps](../connectors/connectors-native-recurrence.md)
* [Oktatóanyag: A forgalom ellenőrzését egy ütemezett logikai alkalmazással](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

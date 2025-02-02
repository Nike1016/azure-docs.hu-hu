---
title: Hozzon létre egy fiókot a Azure Portal-Azure Batchban | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre Azure Batch-fiókot az Azure Portalon nagyméretű párhuzamos számítási feladatok futtatásához a felhőben
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6e1543d578e6812fd270bd76ec18bdfe8fe5ba6a
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68324030"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Batch-fiók létrehozása az Azure Portalon

Megtudhatja, hogyan hozhat létre Azure Batch fiókot [][azure_portal]a Azure Portalban, és válassza ki a számítási forgatókönyvnek megfelelő fiók tulajdonságait. Megtudhatja, hol találja a fontos fióktulajdonságokat, például a hozzáférési kulcsokat és a fiókok URL-címeit.

További ismereteket a Batch-fiókokról és -forgatókönyvekről a [funkciók áttekintésében](batch-api-basics.md) találhat.

## <a name="create-a-batch-account"></a>Batch-fiók létrehozása

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. Jelentkezzen be az [Azure Portalra][azure_portal].

1. Válassza az **Erőforrás létrehozása** > **Számítás** > **Batch szolgáltatás** lehetőséget.

    ![Batch a Piactéren][marketplace_portal]

1. Adja meg az **Új Batch-fiók** beállításait. Lásd az alábbi részleteket.

    ![Batch-fiók létrehozása][account_portal]

    a. **Előfizetés**: Az előfizetés, amelyben létre kívánja hozni a Batch-fiókot. Ha csak egy előfizetéssel rendelkezik, ez alapértelmezés szerint be van jelölve.

    b. **Erőforráscsoport**: Válasszon ki egy meglévő erőforráscsoportot az új batch-fiókhoz, vagy opcionálisan hozzon létre egy újat.

    c. **Fiók neve**: A választott névnek egyedinek kell lennie abban az Azure-régióban, ahol a fiók létrejön (lásd az alábbi **helyet** ). A fiók neve csak kisbetűket vagy számokat tartalmazhat, és 3–24 karakter hosszúnak kell lennie.

    d. **Hely**: Az az Azure-régió, amelyben létre kívánja hozni a Batch-fiókot. Csak az előfizetése és az erőforráscsoportja által támogatott régiók jelennek meg lehetőségként.

    e. **Storage-fiók**: Egy opcionális Azure Storage-fiók, amelyet a Batch-fiókhoz társít. A legjobb teljesítmény érdekében ajánlott az általános célú v2 Storage-fiók használata. A Batch összes Storage-fiókjának beállításairól a [Batch funkcióinak áttekintése](batch-api-basics.md#azure-storage-account)című témakörben talál további információt. A portálon válasszon ki egy meglévő Storage-fiókot, vagy hozzon létre egy újat.

      ![Tárfiók létrehozása][storage_account]

    f. **Készlet lefoglalási módja**: A **speciális** beállítások lapon megadhatja a készlet lefoglalási módját **Batch szolgáltatásként** vagy felhasználói előfizetésként. **** A legtöbb esetben fogadja el az alapértelmezett **Batch szolgáltatást**.

      ![Batch-készlet lefoglalási módja][pool_allocation]

1. A fiók létrehozásához kattintson a **Létrehozás** gombra.

## <a name="view-batch-account-properties"></a>Batch-fiók tulajdonságainak megtekintése

A fiók létrehozása után jelölje ki azt a beállításai és tulajdonságai eléréséhez. Az összes fiókbeállítást és tulajdonságot elérheti a bal oldali menüből.

![A Batch-fiók lap az Azure Portalon][account_blade]

* A **Batch-fiók neve, URL-címe és kulcsa**: Ha a [Batch API](batch-apis-tools.md#azure-accounts-for-batch-development)-kkal fejleszt egy alkalmazást, szüksége lesz egy fiók URL-címére és kulcsra a Batch-erőforrások eléréséhez. (A Batch az Azure Active Directory-hitelesítés használatát is támogatja.)

    A Batch-fiók hozzáférési információnak megtekintéséhez kattintson a **Kulcsok** lehetőségre.

    ![A Batch-fiók kulcsai az Azure Portalon][account_keys]

* A Batch-fiókkal társított tárfiók nevének és kulcsainak megtekintéséhez kattintson a **Storage-fiók** elemre.

* A Batch-fiókra vonatkozó erőforráskvóták megtekintéséhez kattintson a **Kvóták** elemre. További részletek a [Batch szolgáltatás kvótáit és korlátozásait](batch-quota-limit.md) ismertető cikkben tekinthet meg.

## <a name="additional-configuration-for-user-subscription-mode"></a>További konfiguráció a felhasználói előfizetés módhoz

Ha felhasználói előfizetési módban szeretne létrehozni Batch-fiókot, végezze el az alábbi kiegészítő lépéseket a fiók létrehozása előtt.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Az előfizetés elérésének engedélyezése az Azure Batch számára (egyszeri művelet)

Amikor először hoz létre Batch-fiókot felhasználói előfizetés módban, regisztrálja az előfizetését a Batch szolgáltatásban. (Ha korábban már regisztrált, ugorjon a következő szakaszra.)

1. Jelentkezzen be az [Azure Portalra][azure_portal].

1. Kattintson **Az összes szolgáltatás** > **Előfizetések** elemre, majd a Batch-fiókhoz használni kívánt előfizetésre.

1. Az **Előfizetés** lapon válassza az **Erőforrás-szolgáltatók** elemet, majd keressen rá a **Microsoft.Batch** kifejezésre. Ellenőrizze, hogy a **Microsoft.Batch** erőforrás-szolgáltató regisztrálva van-e az előfizetésben. Ha az előfizetés nincs regisztrálva, kattintson a **Regisztrálás** hivatkozásra.

    ![A Microsoft.Batch szolgáltató regisztrálása][register_provider]

1. Az **előfizetés** lapon válassza a **hozzáférés-vezérlés (iam)**  > szerepkör-**hozzárendelések** > **szerepkör-hozzárendelés hozzáadása**elemet.

    ![Az előfizetéshez való hozzáférés vezérlése][subscription_access]

1. A **szerepkör-hozzárendelés hozzáadása** lapon válassza ki a **közreműködő** szerepkört, és keressen rá a Batch API kifejezésre. Keressen rá az egyes sztringekre, addig amíg meg nem találja az API-t:
    1. **MicrosoftAzureBatch**.
    1. **Microsoft Azure Batch**. Újabb Azure AD-bérlők ezt a nevet használhatják.
    1. A **ddbf3205-c6bd-46ae-8127-60eb93363864** a Batch API azonosítója.

1. Miután megtalálta a Batch API-t, jelölje ki, majd kattintson a **Mentés** gombra.

    ![Batch-engedélyek hozzáadása][add_permission]

### <a name="create-a-key-vault"></a>Kulcstartó létrehozása

Felhasználói előfizetés módban olyan Azure Key Vault szükséges, amely ugyanahhoz az erőforráscsoporthoz tartozik, mint a létrehozandó Batch-fiók. Győződjön meg arról, hogy az erőforráscsoport olyan régióban található, amelyben [elérhető](https://azure.microsoft.com/regions/services/) a Batch szolgáltatás, és amelyet támogat az előfizetése.

1. A [Azure Portal][azure_portal]válassza az **új** > **biztonsági** > **Key Vault**lehetőséget.

1. A **Kulcstartó létrehozása** lapon adja meg a kulcstartó nevét, és hozzon létre a régióban egy erőforráscsoportot a Batch-fiókhoz. A többi beállításnál hagyja meg az alapértelmezett értékeket, majd válassza a **Létrehozás** elemre.

A Batch-fiók felhasználói előfizetési módban való létrehozásakor használja a kulcstartóhoz tartozó erőforráscsoportot. Adja meg a **felhasználói** előfizetést készlet-kiosztási módban, jelölje ki a kulcstárolót, és jelölje be a jelölőnégyzetet, hogy Azure batch hozzáférést biztosítson a kulcstartóhoz. 

Ha szeretné manuálisan megadni a kulcstartóhoz való hozzáférést, nyissa meg a Key Vault **hozzáférési szabályzatok** szakaszát, és válassza a **Microsoft Azure batch**lehetőséget. Állítsa be a **titkos engedélyeket** a legördülő menü használatával. Azure Batch legalább a **Get**, a **List**, a **set**és a **delete** engedélyeket kell megadni.

![A Azure Batchhoz tartozó titkos engedélyek](./media/batch-account-create-portal/secret-permissions.png)

### <a name="configure-subscription-quotas"></a>Előfizetési kvóták konfigurálása

Alapértelmezés szerint a felhasználói előfizetés batch-fiókjaiban nincsenek alapszintű kvóták beállítva. Az alapszintű kvótákat manuálisan kell beállítani, mert a standard batch alapkvótái nem érvényesek felhasználói előfizetési módban lévő fiókokra.

1. A [Azure Portal][azure_portal]válassza ki a felhasználói előfizetés mód batch-fiókját a beállítások és tulajdonságok megjelenítéséhez.

1. A bal oldali menüben válassza a **kvóták** lehetőséget a Batch-fiókhoz társított alapvető kvóták megtekintéséhez és konfigurálásához.

Tekintse át a [Batch szolgáltatás kvótáit és korlátozásait](batch-quota-limit.md) a felhasználói előfizetés üzemmódjának alapkvótái alapján kapcsolatos további információkért.

## <a name="other-batch-account-management-options"></a>Egyéb Batch-fiókkezelési lehetőségek

Az Azure Portal használata mellett a többek között a következő eszközökkel is létrehozhat és kezelhet Batch-fiókokat:

* [Batch – PowerShell-parancsmagok](batch-powershell-cmdlets-get-started.md)
* [Azure CLI](batch-cli-get-started.md)
* [Batch Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>További lépések

* A Batch szolgáltatás fogalmairól és funkcióiról további információt [a Batch funkcióinak áttekintésében](batch-api-basics.md) talál. A cikk az elsődleges Batch-erőforrásokat tárgyalja, például a készleteket, a számítási csomópontokat, a feladatokat, a tevékenységeket, és áttekintést nyújt a szolgáltatás nagyméretű számítási feladatokkal kapcsolatos funkcióiról.
* Megismerheti a Batch-kompatibilis alkalmazások [Batch .NET ügyfélkönyvtárral](quick-run-dotnet.md) vagy [Python](quick-run-python.md) segítségével való fejlesztésének alapjait. Ezek a rövid útmutatók végigvezetik egy mintaalkalmazáson, amely a Batch szolgáltatással futtat egy számítási feladatot több számítási csomóponton, és az Azure Storage szolgáltatást is használja a számítási feladatok fájljainak előkészítéséhez és lekéréséhez.

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[marketplace_portal]: ./media/batch-account-create-portal/marketplace-batch.png
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch-account-portal.png
[pool_allocation]: ./media/batch-account-create-portal/batch-pool-allocation.png
[account_keys]: ./media/batch-account-create-portal/batch-account-keys.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png
[register_provider]: ./media/batch-account-create-portal/register_provider.png

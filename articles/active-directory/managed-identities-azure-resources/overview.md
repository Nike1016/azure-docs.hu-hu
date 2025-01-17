---
title: Azure-erőforrások felügyelt identitásai
description: Az Azure-erőforrások felügyelt identitásainak áttekintése.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 06/19/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c4f670f3bb14610e7f29a9201b357e73dacf09b
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67293223"
---
# <a name="what-is-managed-identities-for-azure-resources"></a>Mit kell tudni az Azure-erőforrások felügyelt identitásairól?

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

A felhőalapú alkalmazások készítése során általános kihívást jelenti a hitelesítő adatok a kódban való kezelése, amelyekkel az alkalmazás magát a felhőalapú szolgáltatásokban hitelesíti. A hitelesítő adatok biztonságának megőrzése fontos feladat. Ideális esetben a hitelesítő adatok soha nem jelennek meg a fejlesztői munkaállomásokon, és a verziókövetési rendszerbe sem kerülnek be. Az Azure Key Vault módot kínál a hitelesítő adatok, titkos kódok és egyéb kulcsok biztonságos tárolására, azonban a kódnak hitelesítenie kell magát a Key Vaultban az adatok lekéréséhez. 

Az Azure Active Directory (Azure AD) Azure-erőforrások felügyelt identitásai szolgáltatása megoldást kínál erre a problémára. A szolgáltatás automatikusan felügyelt identitást biztosít az Azure-szolgáltatások számára az Azure AD-ben. Ezzel az identitással bármely, az Azure AD-hitelesítést támogató szolgáltatásban, többek között a Key Vaultban is elvégezheti a hitelesítést anélkül, hogy a hitelesítő adatok a kódban szerepelnének.

Az Azure-erőforrások felügyelt identitásai szolgáltatás ingyenesen használható az Azure Active Directoryval az Azure-előfizetésekben. Nincs további költség.

> [!NOTE]
> Az Azure-erőforrások felügyelt identitásai a Managed Service Identity (MSI) szolgáltatás új neve.

## <a name="terminology"></a>Terminológia

Az alábbi feltételek során a felügyelt identitások Azure-erőforrások dokumentációkészlet használhatók:

- **Ügyfél-azonosító** – Azure ad-ben, amely a kezdeti kiépítés során kötve egy alkalmazás és egyszerű szolgáltatás által létrehozott egyedi azonosítója.
- **Résztvevő-azonosító** – a felügyelt identitás, amellyel egy Azure-erőforrás szerepköralapú hozzáférést a szolgáltatásnév-objektum Objektumazonosítója.
- **Az Azure példány metaadat szolgáltatás (IMDS)** – minden IaaS virtuális gépek számára elérhető egy REST-végpont létrehozása az Azure Resource Manageren keresztül. A végpont egy jól ismert nem átirányítható IP-címen (169.254.169.254), amely csak a virtuális gépen érhető el.

## Hogyan működik az Azure-erőforrások számára a felügyelt identitásokból?<a name="how-does-it-work"></a>

A felügyelt identitásoknak két típusa létezik:

- A **rendszer által hozzárendelt felügyelt identitás** közvetlenül egy Azure-beli szolgáltatáspéldányon van engedélyezve. Az identitás engedélyezésekor az Azure létrehoz egy identitást a példány számára a példány előfizetése által megbízhatónak tekintett Azure AD-bérlőn. Az identitás létrehozása után a rendszer hozzárendeli a hitelesítő adatokat a példányon. A rendszer által hozzárendelt identitás életciklusa közvetlenül kötődik ahhoz az Azure-beli szolgáltatáspéldányhoz, amelyen engedélyezve van. A példány törlésekor az Azure automatikusan törli a hitelesítő adatokat és az identitást az Azure AD-ben.
- A **felhasználó által hozzárendelt felügyelt identitás** különálló Azure-erőforrásként jön létre. Egy létrehozási folyamaton keresztül az Azure létrehoz egy identitást a használt előfizetés által megbízhatónak tekintett Azure AD-bérlőn. Az identitás a létrehozását követően hozzárendelhető egy vagy több Azure-beli szolgáltatáspéldányhoz. A felhasználó által hozzárendelt identitások életciklusa külön van kezelve azon Azure-beli szolgáltatáspéldányokétól, amelyekhez hozzá lettek rendelve.

Belsőleg a felügyelt identitásokból a szolgáltatásnevek olyan speciális típusú, amely csak az Azure-erőforrásokkal való használható zárolva vannak. A felügyelt identitás törlése esetén a rendszer automatikusan eltávolítja a megfelelő egyszerű szolgáltatást. 

A kód a felügyelt identitások használatával hozzáférési jogkivonatokat igényelhet az Azure AD-hitelesítést támogató szolgáltatásokhoz. Az Azure gondoskodik a szolgáltatáspéldány által használt hitelesítő adatok biztosításáról. 

Az alábbi ábrán a felügyelszolgáltatás-identitások az Azure-beli virtuális gépekkel (VM) való működése látható:

![Felügyeltszolgáltatás-identitások és Azure-beli virtuális gépek](media/overview/msi-vm-vmextension-imds-example.png)

|  Tulajdonság    | Rendszer által hozzárendelt felügyelt identitás | felhasználó által hozzárendelt felügyelt identitás |
|------|----------------------------------|--------------------------------|
| Létrehozás |  Létrehozott egy Azure-erőforrás (például az Azure virtuális gépként vagy Azure App Service-ben) | Létrehozott egy önálló Azure-erőforrás |
| Életciklus | Életciklus megosztva a felügyelt identitás létrehozása az Azure-erőforrást. <br/> A szülőerőforrás törlése esetén a felügyelt identitást is törlődik. | Független-életciklusának. <br/> Kifejezetten törölni kell. |
| Különböző Azure-erőforrások megosztása | Nem lehet megosztani. <br/> Csak lehet társítva egyetlen Azure-erőforrás. | Megoszthatók <br/> Az ugyanazon felhasználóhoz felügyelt identitásnak társítható egynél több Azure-erőforrás. |
| Gyakori használati helyzetek | Egyetlen Azure-erőforrás egységen számítási feladatokhoz <br/> Független identitások igénylő munkaterhelések. <br/> Ha például egyetlen virtuális gépen futó alkalmazást | A számítási feladatok, amelyek több erőforrásokon futnak, és amelyek is olyan egyetlen identitás. <br/> A kiépítési folyamat részeként egy biztonságos erőforráshoz előtti engedélyezési igénylő munkaterhelések. <br/> Számítási feladatokhoz, ahol erőforrásokat újrahasznosítására gyakran, de engedélyek konzisztens maradjanak. <br/> Például, ha a több virtuális gép eléréséhez ugyanarra az erőforrásra kell munkaterhelés | 

### <a name="how-a-system-assigned-managed-identity-works-with-an-azure-vm"></a>Hogyan működnek együtt a rendszer által hozzárendelt felügyelt identitások az Azure-beli virtuális gépekkel?

1. Az Azure Resource Managerbe érkezik egy kérés a rendszer által hozzárendelt felügyelt identitás virtuális gépen történő engedélyezésére.
2. Az Azure Resource Manager létrehoz egy szolgáltatásnevet az Azure AD-ben a virtuális gép identitása számára. A szolgáltatásnév az előfizetés által megbízhatónak tekintett Azure AD-bérlőn jön létre.
3. Az Azure Resource Manager konfigurálja az identitást a virtuális gépen:
    1. Frissíti az Azure Instance Metadata szolgáltatás identitásvégpontját a szolgáltatásnév ügyfél-azonosítójával és tanúsítványával.
    1. Létrehozza a VM-bővítményt (tervezett elavulás: 2019. január), és hozzáadja a szolgáltatásnév ügyfél-azonosítóját és tanúsítványát. (Ez a lépés hamarosan elavul majd.)
4. Miután a virtuális gép már rendelkezik identitással, a szolgáltatásnév-adatok használatával biztosítson hozzáférést a virtuális gépnek az Azure-erőforrásokhoz. Az Azure Resource Manager meghívásához szerepköralapú hozzáférés-vezérlés (RBAC) használatával rendelje hozzá a megfelelő szerepkört a virtuális gép szolgáltatásnevéhez az Azure AD-ben. A Key Vault meghívásához adjon hozzáférést a kódnak az adott titkos kódhoz vagy kulcshoz a Key Vaultban.
5. A kód, amely a virtuális gép fut-e az Azure Instance Metadata szolgáltatás végpontjának, csak hozzáférhetők-e a virtuális gép is jogkivonatot kérhet: `http://169.254.169.254/metadata/identity/oauth2/token`
    - A resource (erőforrás) paraméter határozza meg azt a szolgáltatást, amelynek a jogkivonatot meg kell küldeni. Az Azure Resource Manager hitelesítéséhez használja a `resource=https://management.azure.com/` paramétert.
    - Az API version (API-verzió) paraméter adja meg az IMDS-verziót – használja az api-version=2018-02-01, vagy egy nagyobb értéket.

> [!NOTE]
> A kódot is kérheti egy jogkivonatot a Virtuálisgép-bővítmény végpont, de ez elavulásának hamarosan tervezünk. A Virtuálisgép-bővítménnyel kapcsolatos további információkért lásd: [áttelepítése a Virtuálisgép-bővítmény az Azure IMDS hitelesítéshez való](howto-migrate-vm-extension.md).

6. A rendszer egy hívást intéz az Azure AD-re, és egy hozzáférési jogkivonatot igényel (az 5. lépésben leírtak szerint) a 3. lépésben konfigurált ügyfél-azonosító és tanúsítvány használatával. Az Azure AD egy JSON Web Token (JWT) formátumú hozzáférési jogkivonatot ad vissza.
7. A kód elküldi a hozzáférési jogkivonatot egy hívásban egy olyan szolgáltatásnak, amely támogatja az Azure AD-hitelesítést.

### <a name="how-a-user-assigned-managed-identity-works-with-an-azure-vm"></a>Hogyan működnek együtt a felhasználó által hozzárendelt felügyelt identitások az Azure-beli virtuális gépekkel?

1. Az Azure Resource Managerbe érkezik egy kérés egy felhasználó által hozzárendelt felügyelt identitás létrehozására.
2. Az Azure Resource Manager létrehoz egy szolgáltatásnevet az Azure AD-ben a felhasználó által hozzárendelt felügyelt identitás számára. A szolgáltatásnév az előfizetés által megbízhatónak tekintett Azure AD-bérlőn jön létre.
3. Az Azure Resource Managerbe érkezik egy kérés a felhasználó által hozzárendelt felügyelt identitás virtuális gépen történő konfigurálására:
    1. Frissíti az Azure Instance Metadata szolgáltatás identitásvégpontját a felhasználó által hozzárendelt felügyelt identitás szolgáltatásnevéhez tartozó ügyfél-azonosítóval és -tanúsítvánnyal.
    1. Létrehozza a VM-bővítményt, és hozzáadja a felhasználó által hozzárendelt felügyelt identitás szolgáltatásnevéhez tartozó ügyfél-azonosítót és -tanúsítványt. (Ez a lépés hamarosan elavul majd.)
4. Miután a felhasználó által hozzárendelt felügyelt identitás létrejött, a szolgáltatásnév-adatok használatával biztosítson hozzáférést az identitásnak az Azure-erőforrásokhoz. Az Azure Resource Manager meghívásához szerepköralapú hozzáférés-vezérlés (RBAC) használatával rendelje hozzá a megfelelő szerepkört a felhasználó által hozzárendelt identitás szolgáltatásnevéhez az Azure AD-ben. A Key Vault meghívásához adjon hozzáférést a kódnak az adott titkos kódhoz vagy kulcshoz a Key Vaultban.

   > [!Note]
   > Ezt a lépést a 3. lépés előtt is végrehajthatja.

5. A kód, amely a virtuális gép fut-e egy jogkivonatot kérhet az Azure Instance Metadata szolgáltatás identitás-végpontból érkező, csak hozzáférhetők-e a virtuális gép: `http://169.254.169.254/metadata/identity/oauth2/token`
    - A resource (erőforrás) paraméter határozza meg azt a szolgáltatást, amelynek a jogkivonatot meg kell küldeni. Az Azure Resource Manager hitelesítéséhez használja a `resource=https://management.azure.com/` paramétert.
    - A client ID (ügyfél-azonosító) paraméter határozza meg azt az identitást, amely számára a hozzáférési jogkivonat kérvényezve lett. Ez az érték akkor szükséges egyértelműsítés céljából, ha egyazon virtuális gépen több felhasználó által hozzárendelt identitás is létezik.
    - Az API version (API verziója) paraméter az Azure Instance Metadata szolgáltatás verzióját adja meg. Használja a(z) `api-version=2018-02-01` vagy újabb verziót.

> [!NOTE]
> A kódot is kérheti egy jogkivonatot a Virtuálisgép-bővítmény végpont, de ez elavulásának hamarosan tervezünk. A Virtuálisgép-bővítménnyel kapcsolatos további információkért lásd: [áttelepítése a Virtuálisgép-bővítmény az Azure IMDS hitelesítéshez való](howto-migrate-vm-extension.md).

6. A rendszer egy hívást intéz az Azure AD-re, és egy hozzáférési jogkivonatot igényel (az 5. lépésben leírtak szerint) a 3. lépésben konfigurált ügyfél-azonosító és tanúsítvány használatával. Az Azure AD egy JSON Web Token (JWT) formátumú hozzáférési jogkivonatot ad vissza.
7. A kód elküldi a hozzáférési jogkivonatot egy hívásban egy olyan szolgáltatásnak, amely támogatja az Azure AD-hitelesítést.

## <a name="how-can-i-use-managed-identities-for-azure-resources"></a>Hogyan használhatom az Azure-erőforrások felügyelt identitásait?

A különféle Azure-erőforrások felügyelt identitással való elérésének megismeréséért tekintse át az alábbi oktatóanyagokat.

> [!NOTE]
> Tekintse meg a [végrehajtási felügyelt identitásainak a Microsoft Azure-erőforrások](https://www.pluralsight.com/courses/microsoft-azure-resources-managed-identities-implementing) további információ a felügyelt identitásokból, beleértve a videó forgatókönyvek a több támogatott forgatókönyvek részletes tanfolyam.

Útmutató a felügyelt identitások használatához Windows rendszerű virtuális gépeken:

* [Az Azure Data Lake Store elérése](tutorial-windows-vm-access-datalake.md)
* [Az Azure Resource Manager elérése](tutorial-windows-vm-access-arm.md)
* [Az Azure SQL elérése](tutorial-windows-vm-access-sql.md)
* [Az Azure Storage elérése hozzáférési kulcs használatával](tutorial-windows-vm-access-storage.md)
* [Az Azure Storage elérése közös hozzáférésű jogosultságkódok használatával](tutorial-windows-vm-access-storage-sas.md)
* [Nem Azure AD-erőforrások elérése az Azure Key Vaulttal](tutorial-windows-vm-access-nonaad.md)

Útmutató a felügyelt identitások használatához Linux rendszerű virtuális gépeken:

* [Az Azure Data Lake Store elérése](tutorial-linux-vm-access-datalake.md)
* [Az Azure Resource Manager elérése](tutorial-linux-vm-access-arm.md)
* [Az Azure Storage elérése hozzáférési kulcs használatával](tutorial-linux-vm-access-storage.md)
* [Az Azure Storage elérése közös hozzáférésű jogosultságkódok használatával](tutorial-linux-vm-access-storage-sas.md)
* [Nem Azure AD-erőforrások elérése az Azure Key Vaulttal](tutorial-linux-vm-access-nonaad.md)
* [Hozzáférés az Azure Container Registrybe](../../container-registry/container-registry-authentication-managed-identity.md)

Útmutató a felügyelt identitások használatához egyéb Azure-szolgáltatásokban:

* [Azure App Service](/azure/app-service/overview-managed-identity)
* [Azure Functions](/azure/app-service/overview-managed-identity)
* [Azure Logic Apps](/azure/logic-apps/create-managed-service-identity)
* [Azure Service Bus](../../service-bus-messaging/service-bus-managed-service-identity.md)
* [Azure Event Hubs](../../event-hubs/event-hubs-managed-service-identity.md)
* [Azure API Management](../../api-management/api-management-howto-use-managed-service-identity.md)
* [Azure Container Instances](../../container-instances/container-instances-managed-identity.md)
* [Az Azure Container Registry-feladatok](../../container-registry/container-registry-tasks-authentication-managed-identity.md)

## Mely Azure-szolgáltatások támogatják a szolgáltatást?<a name="which-azure-services-support-managed-identity"></a>

Az Azure-erőforrások felügyelt identitásai használatával hitelesítést végezhet az Azure AD-hitelesítést támogató szolgáltatásokban. Az Azure-erőforrások felügyelt identitásai szolgáltatást támogató Azure-szolgáltatások listájáért lásd [az Azure-erőforrások felügyelt identitásait támogató szolgáltatásokkal](services-support-msi.md) foglalkozó részt.

## <a name="next-steps"></a>További lépések

Ismerkedjen meg az Azure-erőforrások felügyelt identitásai szolgáltatással a következő rövid útmutatók segítségével:

* [Hozzáférés a Resource Managerhez egy Windows VM-beli, rendszer által hozzárendelt felügyelt identitással](tutorial-windows-vm-access-arm.md)
* [Hozzáférés a Resource Managerhez egy Linux VM-beli, rendszer által hozzárendelt felügyelt identitással](tutorial-linux-vm-access-arm.md)

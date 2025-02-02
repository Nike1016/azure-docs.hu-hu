---
title: A virtuális gép tartalmának naplózása
description: Ismerje meg, hogyan Azure Policy segítségével Vendég konfigurációs naplózási beállítások egy Azure virtuális gépen belül.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/18/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 74e36d944450e1ce2c61481b2cb7e345860212af
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326886"
---
# <a name="understand-azure-policys-guest-configuration"></a>Az Azure Policy Vendég konfiguráció ismertetése

Az Azure-erőforrások naplózása és [szervizelését](../how-to/remediate-resources.md) mellett Azure Policy a virtuális gépen belül is naplózhatja a beállításokat. A Vendég Configuration bővítmény és az ügyfél az érvényesítést végzi. A bővítményt, az ügyfélen, érvényesíti a konfigurációt az operációs rendszer, alkalmazás-konfigurációs vagy jelenléte, környezeti beállítások és beállításait.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="extension-and-client"></a>Bővítmény és az ügyfél

A naplózási beállítások egy virtuális gépen egy [virtuálisgép-bővítmény](../../../virtual-machines/extensions/overview.md) engedélyezve van. A bővítmény letölti a megfelelő szabályzat-hozzárendelés és a megfelelő konfiguráció definíciója.

### <a name="register-guest-configuration-resource-provider"></a>Vendég-konfigurációs erőforrás-szolgáltató regisztrálása

Vendég-konfiguráció használata előtt regisztrálnia kell az erőforrás-szolgáltató. A portálon keresztül vagy a Powershellen keresztül lehet regisztrálni. Az erőforrás-szolgáltató automatikusan regisztrálva van, ha a vendég konfigurációs szabályzatának hozzárendelése a portálon történik.

#### <a name="registration---portal"></a>Regisztráció – portál

Az erőforrás-szolgáltató regisztrálása Vendég konfiguráció az Azure Portalon keresztül, kövesse az alábbi lépéseket:

1. Indítsa el az Azure Portalon, és kattintson a **minden szolgáltatás**. Keresse meg és válassza **előfizetések**.

1. Keresse meg és kattintson arra az előfizetésre, amely engedélyezi a Vendég konfigurációját.

1. A bal oldali menüben lévő a **előfizetés** kattintson **erőforrás-szolgáltatók**.

1. Állítson be szűrőt, és görgessen, amíg meg nem találja **Microsoft.GuestConfiguration**, majd kattintson a **regisztrálása** ugyanabban a sorban.

#### <a name="registration---powershell"></a>Regisztráció – PowerShell

Vendég konfigurációs PowerShell-lel az erőforrás-szolgáltató regisztrálásához futtassa a következő parancsot:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

### <a name="validation-tools"></a>Érvényesítési eszközök

A virtuális gépen belül a Vendég konfigurációs ügyfélnél a helyi eszközök futtatásához a naplózási.

Az alábbi táblázat az egyes támogatott operációs rendszeren használja a helyi eszközök listáját:

|Operációs rendszer|Fürtérvényesítési eszköz|Megjegyzések|
|-|-|-|
|Windows|[A Microsoft Desired State Configuration](/powershell/dsc) v2| |
|Linux|[A Chef InSpec](https://www.chef.io/inspec/)| Ruby és Python telepíti a Vendég Configuration bővítményt. |

### <a name="validation-frequency"></a>Ellenőrzés gyakorisága

A vendég konfigurációs ügyfél 5 percenként keres új tartalmat. A vendég-hozzárendelés fogadása után a rendszer 15 percenként ellenőrzi a beállításokat. A rendszer a naplózás befejeződése után azonnal elküldi az eredményeket a vendég konfiguráció erőforrás-szolgáltatójának. A szabályzatok [](../how-to/get-compliance-data.md#evaluation-triggers) kiértékelésének bekövetkeztekor a számítógép állapota a vendég konfiguráció erőforrás-szolgáltatóba íródik. Ennek hatására a Azure Policy kiértékeli a Azure Resource Manager tulajdonságait. Az igény szerinti Azure Policy kiértékelése a vendég konfiguráció erőforrás-szolgáltató legújabb értékét kérdezi le. Azonban nem indítja el a virtuális gépen belüli konfiguráció új naplózását.

### <a name="supported-client-types"></a>Támogatott ügyfél típusú

Az alábbi táblázat az Azure-rendszerképek támogatott operációs rendszerek listája látható:

|Közzétevő|Name (Név)|Verziók|
|-|-|-|
|Canonical|Ubuntu Server|14.04-es, 16.04, 18.04|
|credativ|Debian|8, 9|
|Microsoft|Windows Server|2012 Datacenter, 2012 R2 Datacenter, 2016 Datacenter, 2019 Datacenter|
|Microsoft|Windows-ügyfél|Windows 10|
|OpenLogic|CentOS|7.3, 7.4, 7.5|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5|
|SUSE|SLES|12 SP3|

> [!IMPORTANT]
> A vendég konfigurációja képes a támogatott operációs rendszert futtató csomópontok naplózására. Ha egyéni rendszerképet használó virtuális gépeket szeretne naplózni, duplikálnia kell a **DeployIfNotExists** -definíciót, és módosítania kell az **IF** szakaszt a rendszerkép tulajdonságainak belefoglalásához.

### <a name="unsupported-client-types"></a>Nem támogatott ügyfélalkalmazás típusa

A Windows Server Nano Server semmilyen verzióban nem támogatott.

### <a name="guest-configuration-extension-network-requirements"></a>A vendég konfigurációs bővítmény hálózati követelményei

Az Azure-beli vendég-konfigurációs erőforrás-szolgáltatóval való kommunikációhoz a virtuális gépeknek kimenő hozzáférésre van szükségük az Azure-adatközpontokhoz az **443**-as porton Ha az Azure-ban privát virtuális hálózatot használ, és nem engedélyezi a kimenő forgalmat, a kivételeket a [hálózati biztonsági csoport](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) szabályainak megfelelően kell konfigurálni. Jelenleg nem létezik szolgáltatási címke Azure Policy vendég konfigurációhoz.

Az IP-címlisták esetében letöltheti [Microsoft Azure adatközpont IP-tartományait](https://www.microsoft.com/download/details.aspx?id=41653). A fájl hetente frissül, és a jelenleg üzembe helyezett tartományokat és az IP-címtartományok közelgő változásait tartalmazza. Csak a virtuális gépeket üzembe helyező régiókban lévő IP-címekhez kell engedélyeznie a kimenő hozzáférést.

> [!NOTE]
> Az Azure Datacenter IP-cím XML-fájlja felsorolja az Microsoft Azure adatközpontokban használt IP-címtartományt. A fájl a számítási, az SQL-és a tárolási tartományokat tartalmazza. A frissített fájlok hetente kerülnek közzétételre. A fájl a jelenleg telepített tartományokat és az IP-címtartományok közelgő változásait tükrözi. A fájlban megjelenő új tartományok legalább egy hétig nem használhatók az adatközpontokban. Érdemes minden héten letölteni az új XML-fájlt. Ezután frissítse a webhelyet az Azure-ban futó szolgáltatások megfelelő azonosításához. Az Azure ExpressRoute felhasználói számára fontos megjegyezni, hogy ez a fájl az Azure Space Border Gateway Protocol (BGP) hirdetményének frissítésére szolgál minden hónap első hetében.

## <a name="guest-configuration-definition-requirements"></a>Vendég konfigurációkra definíciója

Minden vendég konfigurációhoz tartozó naplózási szolgáltatásnak két házirend-definícióra, egy **DeployIfNotExists** -definícióra és egy **naplózási** definícióra van szüksége. A **DeployIfNotExists** definíciója a virtuális gép és a vendég konfigurációs ügynök és más összetevők előkészítésére szolgál az [ellenőrzési eszközök](#validation-tools)támogatásához.

A **DeployIfNotExists** szabályzatdefiníció ellenőrzi és javítja a következő elemek:

- Ellenőrizze a virtuális gép egy konfigurációs kiértékelheti, hogy hozzá lett rendelve. Ha nincs hozzárendelés nem található, a hozzárendelés beolvasása, és készítse elő a virtuális gép által:
  - A virtuális gép történő hitelesítése egy [identitás](../../../active-directory/managed-identities-azure-resources/overview.md)
  - A legújabb verziójának telepítése a **Microsoft.GuestConfiguration** bővítmény
  - Telepítés [érvényesítési eszközök](#validation-tools) és függőségei, szükség esetén

Ha a **DeployIfNotExists** -hozzárendelés nem megfelelő, akkor a rendszer [szervizelési feladatot](../how-to/remediate-resources.md#create-a-remediation-task) is felhasználhat.

Ha a **DeployIfNotExists** -hozzárendelés megfelelő, a **naplózási** házirend-hozzárendelés a helyi ellenőrzési eszközök használatával határozza meg, hogy a konfigurációs hozzárendelés megfelelő vagy nem megfelelő-e.
A fürtérvényesítési eszköz biztosít a Vendég konfigurációs ügyfél az eredményeket. Az ügyfél a Vendég a bővítmény elérhetővé teszi azokat a Vendég-konfigurációs erőforrás-szolgáltatón keresztül továbbítja az eredményeket.

Az Azure Policy használja a Vendég-konfigurációs erőforrás-szolgáltatók **complianceStatus** való megfelelőség jelentéséhez a tulajdonság a **megfelelőségi** csomópont. További információkért lásd: [megfelelőségi adatok](../how-to/getting-compliance-data.md).

> [!NOTE]
> A naplózási házirendnek az eredmények **** visszaküldéséhez a **DeployIfNotExists** szabályzat szükséges.
> A **DeployIfNotExists**nélkül a **naplózási** házirend a "0/0" erőforrást jeleníti meg állapotként.

Beépített Vendég konfigurációs szabályzatainak csoportra vonatkozó definíciókat használja a hozzárendelések kezdeményezések szerepelnek. A [Preview] nevű *beépített kezdeményezés: A jelszó biztonsági beállításainak naplózása Linux és Windows rendszerű virtuális gépeken* 18 szabályzatot tartalmaz. Hat **DeployIfNotExists** és naplózási **** pár van a Windowshoz és három pár a Linux rendszerhez. Minden esetben a logika belül a definíció érvényesíti csak a cél operációs rendszer alapján értékeli ki a [felügyeletiházirend-szabálya](definition-structure.md#policy-rule) definíciója.

## <a name="multiple-assignments"></a>Több hozzárendelés

A vendég-konfigurációs házirendek jelenleg csak egyszer támogatják ugyanazt a vendég-hozzárendelést virtuális gépenként, még akkor is, ha a házirend-hozzárendelés eltérő paramétereket használ.

## <a name="client-log-files"></a>Ügyfél naplófájljai

A vendég konfigurációs bővítmény naplófájlokat ír a következő helyszínekre:

Windows: `C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\<version>\dsc\logs\dsc.log`

Linux: `/var/lib/waagent/Microsoft.GuestConfiguration.ConfigurationforLinux-<version>/GCAgent/logs/dsc.log`

Ahol `<version>` az aktuális verziószámra hivatkozik.

## <a name="guest-configuration-samples"></a>Vendég konfigurációs minták

A házirend vendég konfigurációjának mintái a következő helyszíneken érhetők el:

- [Minták indexe – vendég konfigurációja](../samples/index.md#guest-configuration)
- [Azure Policy minták GitHub](https://github.com/Azure/azure-policy/tree/master/samples/GuestConfiguration)-tárháza.

## <a name="next-steps"></a>További lépések

- Tekintse át a példákat [Azure Policy mintákon](../samples/index.md).
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](effects.md).
- Megtudhatja, hogyan [hozhat létre programozott](../how-to/programmatically-create.md)módon házirendeket.
- Ismerje meg, hogyan kérheti le a [megfelelőségi információkat](../how-to/getting-compliance-data.md).
- Ismerje meg, hogyan javíthatja a [nem megfelelő erőforrásokat](../how-to/remediate-resources.md).
- Tekintse át, hogy a felügyeleti csoport hogyan [rendezi az erőforrásokat az Azure felügyeleti csoportjaival](../../management-groups/index.md).

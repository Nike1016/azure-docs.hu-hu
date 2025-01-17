---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 5c90d7b5eefdf8357c0f6f8efb619bd69af12b06
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68843224"
---
Fontos, hogy a virtuális gép (VM) biztonságos legyen a futtatott alkalmazások számára. A virtuális gépek biztonságossá tételéhez tartozhat egy vagy több olyan Azure-szolgáltatás és szolgáltatás, amely a virtuális gépek biztonságos elérését és az adatai biztonságos tárolását is magában foglalja. Ez a cikk a virtuális gép és az alkalmazások védelmét lehetővé tevő információkat tartalmaz.

## <a name="antimalware"></a>Kártevőirtó

A Felhőbeli környezetek modern fenyegetési tájképe dinamikus, és a megfelelőségi és biztonsági követelmények teljesítése érdekében egyre nagyobb nyomást biztosít a hatékony védelem fenntartásához. Az Azure-hoz készült [Microsoft antimalware](../articles/security/fundamentals/antimalware.md) egy ingyenes, valós idejű védelmi képesség, amely segít azonosítani és eltávolítani a vírusokat, kémprogramokat és egyéb kártevő szoftvereket. A riasztások úgy konfigurálhatók, hogy tájékoztassanak arról, ha az ismert kártékony vagy nemkívánatos szoftverek megkísérlik telepíteni vagy futtatni magukat a virtuális gépen. A Linux vagy a Windows Server 2008 rendszerű virtuális gépeken nem támogatott.

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../articles/security-center/security-center-intro.md) segít megakadályozni, észlelni és reagálni a virtuális gépekkel kapcsolatos fenyegetésekre. A Security Center az Azure-előfizetések integrált biztonsági monitorozását és felügyeletét teszi lehetővé, segít felderíteni azokat a fenyegetéseket, amelyek egyébként észrevétlenek lehetnek, és a biztonsági megoldások széles körű ökoszisztémával működnek.

Security Center az igény szerinti hozzáférés a virtuális gépek üzembe helyezésével zárolható az Azure-beli virtuális gépek felé irányuló bejövő forgalom zárolása érdekében, így csökkenthető a támadásoknak való kitettség, miközben könnyű hozzáférést biztosít a virtuális gépekhez, ha szükséges. Ha az igény szerinti hozzáférés engedélyezve van, és a felhasználó hozzáférést kér egy virtuális géphez, Security Center ellenőrzi, hogy a felhasználó milyen engedélyekkel rendelkezik a virtuális géphez. Ha rendelkeznek a megfelelő engedélyekkel, a rendszer jóváhagyja a kérést, és Security Center automatikusan konfigurálja a hálózati biztonsági csoportokat (NSG), hogy a kijelölt portokra korlátozott ideig engedélyezze a bejövő forgalmat. Az idő lejárta után Security Center visszaállítja a NSG az előző állapotokra. 

## <a name="encryption"></a>Encryption

A továbbfejlesztett [Windowsos](../articles/virtual-machines/windows/encrypt-disks.md) virtuális gépek és a [linuxos](../articles/virtual-machines/linux/encrypt-disks.md) virtuális gépek biztonsága és megfelelősége érdekében az Azure-ban található virtuális lemezek titkosíthatók. A Windows rendszerű virtuális gépeken futó virtuális lemezek a BitLocker használatával titkosítva vannak. A Linux rendszerű virtuális gépeken futó virtuális lemezek a dm-crypt használatával titkosítva vannak. 

A virtuális lemezek titkosítása az Azure-ban díjmentes. A titkosítási kulcsokat a szoftveres védelem Azure Key Vault tárolja, vagy importálhatja vagy létrehozhatja a kulcsokat az FIPS 140-2 2. szintű standard minősítésű hardveres biztonsági modulokban (HSM). Ezek a titkosítási kulcsok a virtuális GÉPHEZ csatolt virtuális lemezek titkosítására és visszafejtésére szolgálnak. Megőrzi a titkosítási kulcsok felügyeletét, és naplózhatja a használatukat. Az Azure Active Directory egyszerű szolgáltatás biztonságos mechanizmust biztosít a titkosítási kulcsok kiadására, mivel a virtuális gépek be-és kikapcsolva vannak.

## <a name="key-vault-and-ssh-keys"></a>Key Vault és SSH-kulcsok

A titkokat és a tanúsítványokat erőforrásként modellezheti, és [Key Vault](../articles/key-vault/key-vault-whatis.md)biztosíthatja. A Azure PowerShell használatával kulcstartókat hozhat létre [Windows rendszerű virtuális gépekhez](../articles/virtual-machines/windows/key-vault-setup.md) és az Azure CLI [Linux rendszerű virtuális gépekhez](../articles/virtual-machines/linux/key-vault-setup.md). Emellett kulcsokat is létrehozhat a titkosításhoz.

A Key Vault hozzáférési szabályzatai külön engedélyeket biztosítanak a kulcsok, a titkos kulcsokhoz és a tanúsítványokhoz. Egy adott felhasználó számára hozzáférést engedélyezhet például kizárólag a kulcsokhoz, de a titkos kulcsokhoz nem. A kulcsok, titkos kulcsok és tanúsítványok hozzáférése ugyanakkor tárolószinten engedélyezett. Más szóval a [Key Vault hozzáférési szabályzata](../articles/key-vault/key-vault-secure-your-key-vault.md) nem támogatja az objektum szintű engedélyeket.

A virtuális gépekhez való csatlakozáskor nyilvános kulcsú titkosítást kell használnia, hogy biztonságosabban lehessen bejelentkezni. Ennek a folyamatnak része egy nyilvános és titkos kulcscsere, amely a Secure Shell (SSH) parancs használatával hitelesíti magát a Felhasználónév és a jelszó helyett. A jelszavak kimaradnak a találgatásos támadásokkal szemben, különösen az internetre irányuló virtuális gépeken, például webkiszolgálókon. A Secure Shell (SSH) kulcspár használatával olyan [linuxos virtuális gépet](../articles/virtual-machines/linux/mac-create-ssh-keys.md) hozhat létre, amely SSH-kulcsokat használ a hitelesítéshez, így nincs szükség a bejelentkezéshez szükséges jelszavakra. Az SSH-kulcsok használatával a [Windows rendszerű virtuális](../articles/virtual-machines/linux/ssh-from-windows.md) gépekről is csatlakozhat Linux RENDSZERű virtuális gépekhez.

## <a name="managed-identities-for-azure-resources"></a>Azure-erőforrások felügyelt identitásai

A felhőalapú alkalmazások készítése során általános kihívást jelenti a hitelesítő adatok a kódban való kezelése, amelyekkel az alkalmazás magát a felhőalapú szolgáltatásokban hitelesíti. A hitelesítő adatok biztonságának megőrzése fontos feladat. Ideális esetben a hitelesítő adatok soha nem jelennek meg a fejlesztői munkaállomásokon, és a verziókövetési rendszerbe sem kerülnek be. Az Azure Key Vault módot kínál a hitelesítő adatok, titkos kódok és egyéb kulcsok biztonságos tárolására, azonban a kódnak hitelesítenie kell magát a Key Vaultban az adatok lekéréséhez. 

Az Azure Active Directory (Azure AD) Azure-erőforrások felügyelt identitásai szolgáltatása megoldást kínál erre a problémára. A szolgáltatás automatikusan felügyelt identitást biztosít az Azure-szolgáltatások számára az Azure AD-ben. Ezzel az identitással bármely, az Azure AD-hitelesítést támogató szolgáltatásban, többek között a Key Vaultban is elvégezheti a hitelesítést anélkül, hogy a hitelesítő adatok a kódban szerepelnének.  A virtuális gépen futó kód két végpontból igényelhet jogkivonatot, amely csak a virtuális gépről elérhető. A szolgáltatással kapcsolatos részletesebb információkért tekintse át a [felügyelt identitások az Azure-erőforrások](../articles/active-directory/managed-identities-azure-resources/overview.md) áttekintéséhez lapot.   

## <a name="policies"></a>Házirendek

Az [Azure](../articles/azure-policy/azure-policy-introduction.md) -szabályzatok segítségével meghatározhatja a szervezet Windows-és Linux- [alapú](../articles/virtual-machines/windows/policy.md) [virtuális](../articles/virtual-machines/linux/policy.md)gépei kívánt viselkedését. A házirendek használatával a szervezetek különböző egyezményeket és szabályokat alkalmazhatnak a vállalaton belül. A kívánt viselkedés kényszerítésével csökkentheti a kockázatokat, miközben hozzájárul a szervezet sikeréhez.

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

A [szerepköralapú hozzáférés-vezérlés (RBAC)](../articles/role-based-access-control/overview.md)használatával elkülönítheti a feladatait a csapaton belül, és csak a virtuális gépen lévő felhasználók számára biztosíthatja a feladataik elvégzéséhez szükséges hozzáférést. Ahelyett, hogy mindenki számára nem korlátozott engedélyeket adna a virtuális géphez, csak bizonyos műveleteket engedélyezhet. A virtuális gép hozzáférés-vezérlését az [Azure CLI](https://docs.microsoft.com/cli/azure/role)vagy a[Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md)használatával konfigurálhatja a [Azure Portal](../articles/role-based-access-control/role-assignments-portal.md).


## <a name="next-steps"></a>További lépések
- Megtudhatja, hogyan figyelheti a virtuális gépek biztonságát a [Linux](../articles/security/fundamentals/overview.md) vagy a [Windows](../articles/virtual-machines/windows/tutorial-azure-security.md)Azure Security Center használatával.

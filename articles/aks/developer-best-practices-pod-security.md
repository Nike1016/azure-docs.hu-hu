---
title: Ajánlott eljárások Developer - Pod biztonság az Azure Kubernetes-szolgáltatások (AKS)
description: Ismerje meg, hogyan védheti meg az Azure Kubernetes Service (AKS) podok fejlesztői ajánlott eljárásai
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: zarhoads
ms.openlocfilehash: f9d49d143b31b0b9e73d8a147605935cd88d412b
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "65073971"
---
# <a name="best-practices-for-pod-security-in-azure-kubernetes-service-aks"></a>Az Azure Kubernetes Service (AKS) pod biztonsági védelmének bevált gyakorlata

Fejlesztés és alkalmazások futtatásához az Azure Kubernetes Service (AKS), a podok biztonságát kulcs veszi figyelembe. Az alkalmazásokat úgy kell megtervezni a minimális száma, szükséges jogosultságok elvét. Felső részén szem előtt az ügyfeleknek titkos adatok biztonságos kezelése mellett. Nem kívánt hitelesítő adatokat, például adatbázis-kapcsolati karakterláncok, kulcsok vagy titkos kulcsok és tanúsítványok a külvilág, ahol a támadó sikerült kihasználhatja ezeket a titkos kódok és rosszindulatú célokra vannak kitéve. Nem hozzáadhatja őket a kód, vagy a tárolólemezképek beágyazása őket. Ezt a módszert akkor hozzon létre egy kockázatát mérik a különböző és elforgatásának ezeket a hitelesítő adatokat, mint a tárolórendszerképeket építhető újra kell korlátozni.

Ajánlott eljárások a cikkben az aks-ben mennyire vannak biztonságban podok összpontosít. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Folyamatok és a szolgáltatások vagy a jogosultságok eszkalálását való hozzáférés korlátozására használja a pod biztonsági környezet
> * Hitelesítés a többi Azure-erőforrások pod felügyelt identitások használatával
> * Kérelem és lekérni egy digitális például az Azure Key Vault-tárolót a hitelesítő adatokat

Ajánlott eljárást is olvashatja [fürt biztonsági] [ best-practices-cluster-security] és a [kép tárolókezelés][best-practices-container-image-management].

## <a name="secure-pod-access-to-resources"></a>Podok erőforrásokhoz való hozzáférés biztonságossá tétele

**Ajánlott eljárásokkal kapcsolatos útmutatás** – az a mögöttes csomópont folyamatok és-szolgáltatások egy másik felhasználó vagy csoport és a limit hozzáférés futtató podok biztonsági környezet beállítás megadása. Rendelje hozzá a legkevésbé szükséges jogosultságok száma.

Az alkalmazások megfelelő működéséhez, a podok egy meghatározott felhasználó vagy csoport fusson, és nem *legfelső szintű*. A `securityContext` egy pod vagy a tároló lehetővé teszi például-beállítások megadása a *felhasználó* vagy *fsGroup* számára, hogy a megfelelő engedélyekkel. Csak rendelni a szükséges felhasználói vagy biztonságicsoport-engedélyeit, és ne használja a biztonsági környezet eszközeként tegyük fel, további engedélyeket. A *felhasználó*, jogosultságok eszkalálását és egyéb Linux képességek beállítások érhetők el csak a Linux-csomópontok és a podokat.

Futtatásakor nem legfelső szintű felhasználóként, tárolók nem köthető a rendszerjogosultságú portokat az 1024. Ebben a forgatókönyvben a Kubernetes-szolgáltatás segítségével álcázza a tény, hogy egy alkalmazás fut, egy adott portot.

A pod biztonsági környezet is megadhatja, további funkciókat, vagy a folyamatok és szolgáltatások eléréséhez szükséges engedélyekkel. A következő gyakori biztonsági környezet definíciókat adhatja meg:

* **allowPrivilegeEscalation** meghatározza, hogy a pod feltételezheti, hogy *legfelső szintű* jogosultságokkal. Megterveznie az alkalmazásokat, így ez a beállítás értéke mindig *hamis*.
* **Linux-képességek** lehetővé teszik a pod alapul szolgáló csomópont folyamatok elérésére. A hozzárendelés, ezek a képességek gondoskodunk. Rendelje hozzá a legkevésbé száma szükséges jogosultságokkal. További információkért lásd: [Linux képességek][linux-capabilities].
* **SELinux címkék** egy Linux kernel biztonsági modul, amely lehetővé teszi a szolgáltatások, folyamatok és fájlrendszer hozzáférés hozzáférési házirendeket definiálhat. Újbóli hozzárendelése a legkevésbé száma szükséges jogosultságokkal. További információkért lásd: [SELinux lehetőségek a Kubernetesben][selinux-labels]

A következő példa pod YAML-jegyzékfájl állítja be a biztonsági környezet beállítások meghatározásához:

* Podot futtat felhasználói azonosítóként *1000* és Csoportazonosító része *2000*
* Nem lehet eszkalálni a jogosultságokat használata `root`
* Lehetővé teszi, hogy a hálózati adapterek és a gazdagép valós idejű (hardver) óra Linux képességek

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  containers:
    - name: security-context-demo
      image: nginx:1.15.5
    securityContext:
      runAsUser: 1000
      fsGroup: 2000
      allowPrivilegeEscalation: false
      capabilities:
        add: ["NET_ADMIN", "SYS_TIME"]
```

Milyen biztonsági környezet beállításokat kell meghatározni a fürt operátor működnek. Próbálja meg minimálisra csökkentése érdekében további engedélyekkel és hozzáférési a pod igényel az alkalmazások tervezéséhez. Nincsenek további biztonsági funkciókat korlátozni AppArmor és a seccompot (biztonságos computing), amely a fürt operátorok úgy implementálható. További információkért lásd: [tároló erőforrásokhoz való hozzáférés biztonságossá][apparmor-seccomp].

## <a name="limit-credential-exposure"></a>Hitelesítő adatok felfedésével korlát

**Ajánlott eljárásokkal kapcsolatos útmutatás** -nem ad meg hitelesítő adatait az alkalmazás kódjában. Az Azure-erőforrások felügyelt identitások használatával lehetővé teszik a pod kérelem hozzáférés az erőforrásokhoz. Egy digitális tároló tartalmazza, például az Azure Key Vaultban kell is használható, tárolására és beolvasására, a digitális kulcsokat és a hitelesítő adatokat. A pod felügyelt identitások Linux podok és csak a tárolórendszerképek való használatra szolgál.

És a kockázatok hitelesítő adatok elérhetővé váljon az alkalmazás kódjában, kerülje a rögzített vagy megosztott hitelesítő adatokat használjanak. Hitelesítő adatokhoz vagy kulcsokhoz nem tartalmaz közvetlenül a kódba. Ezek a hitelesítő adatok érhetők el, ha az alkalmazásnak kell frissíteni, és áttelepült. Jobb módszer, hogy adjon a podok saját identitás- és hitelesíteni magukat, vagy automatikusan lekérni egy digitális tárolót a hitelesítő adatokat.

A következő [társított AKS nyílt forráskódú projektek] [ aks-associated-projects] lehetővé teszik a podok vagy a kérelem hitelesítő adatok és a egy digitális vault-kulcsok automatikusan hitelesítéséhez:

* Felügyelt identitások az Azure-erőforrásokhoz, és
* Az Azure Key Vault FlexVol illesztőprogram

Társított AKS nyílt forráskódú projektek nem támogatottak az Azure műszaki támogatást. A biztosított gyűjthet visszajelzéseket és a hibák kapcsolódóan a Közösség részéről. Ezek a projektek nem ajánlottak éles környezetben való használatra.

### <a name="use-pod-managed-identities"></a>Használat pod felügyelt identitások

Az Azure-erőforrások egy felügyelt identitás lehetővé teszi, hogy hitelesítse magát az Azure-ban, amely támogatja azt például a Storage, SQL bármely szolgáltatáshoz podot. A pod hozzá van rendelve egy Azure-identitás, amely lehetővé teszi, hogy azokat az Azure Active Directory hitelesítést és a digitális tokent kaphatnak. A digitális token megjeleníthetők az egyéb Azure-szolgáltatások, ellenőrizze, hogy jogosult-e a pod hozzáférhessen a szolgáltatáshoz, és végezze el a szükséges műveleteket. Ez a megközelítés azt jelenti, hogy nincs titkos kódok szükséges adatbázis-kapcsolati karakterláncok, például. A pod felügyelt identitás egyszerűsített munkafolyamatot az alábbi ábrán látható:

![A pod egyszerűsített munkafolyamat felügyelt identitás az Azure-ban](media/developer-best-practices-pod-security/basic-pod-identity.png)

Felügyelt identitással az alkalmazás kódja nem kell tartalmaznia egy szolgáltatáshoz, például az Azure Storage eléréséhez szükséges hitelesítő adatokat. Mivel minden egyes pod hitelesíti magát a saját identitással, így is naplózása és hozzáférés felülvizsgálata. Ha az alkalmazás kapcsolódik az Azure-szolgáltatásokat, használja a felügyelt identitásokból korlát hitelesítő adatok újrafelhasználása és kitettség.

A pod identitások kapcsolatos további információkért lásd: [pod által felügyelt identitásokat használ egy AKS-fürt konfigurálása és az alkalmazásokkal][aad-pod-identity]

### <a name="use-azure-key-vault-with-flexvol"></a>Az Azure Key Vault használata FlexVol

Felügyelt pod identitások hitelesítése támogató Azure-szolgáltatások remekül működik. Saját szolgáltatások vagy alkalmazások felügyelt identitások az Azure-erőforrások anélkül hogy továbbra is hitelesítést végezni a hitelesítő adatokhoz vagy kulcsokhoz. Egy digitális tárolót ezen hitelesítő adatok tárolására használható.

Amikor az alkalmazásoknak a hitelesítő adatokat tudnak kommunikálni a digitális tárolóhoz, a legújabb hitelesítő adatok lekéréséhez, és kapcsolódjon a szükséges szolgáltatás. Az Azure Key Vault lehet a digitális tárban. A hitelesítő adatok lekérését az Azure Key Vault pod felügyelt identitások használatával egyszerűsített munkafolyamata az alábbi ábrán látható:

![A hitelesítő adatokat beolvasni a Key Vault használata egy pod egyszerűsített munkafolyamat felügyelt identitás](media/developer-best-practices-pod-security/basic-key-vault-flexvol.png)

A Key Vault tárolja, és rendszeresen elforgatása a például hitelesítő adatokat, a tárfiók kulcsait vagy a tanúsítványok titkos kulcsok. Az Azure Key Vault integrálható egy FlexVolume használó egy AKS-fürtöt. A FlexVolume illesztőprogram lehetővé teszi, hogy a natív módon olvashatók be hitelesítő adatok a Key Vaultból és biztonságosan azokat csak a kérelmező pod az AKS-fürtöt. A Key Vault FlexVol illesztőprogram be az AKS-csomópontok üzembe helyezéséhez a fürt operátor működnek. A pod felügyelt identitás használatával Key Vault hozzáférés kérése és a hitelesítő adatokat a FlexVolume illesztőprogram keresztül kell lekérni.

Az Azure Key Vault-FlexVol célja a Linux-podok és -csomópontokon futó alkalmazásokhoz és szolgáltatásokhoz való használatra.

## <a name="next-steps"></a>További lépések

Ez a cikk a podok biztonságossá összpontosít. Néhány ilyen területet végrehajtásához a következő cikkekben talál:

* [Felügyelt identitások használata az Azure-erőforrásokhoz az aks-sel][aad-pod-identity]
* [Az Azure Key Vault integrálhatja az aks-sel][aks-keyvault-flexvol]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity#demo-pod
[aks-keyvault-flexvol]: https://github.com/Azure/kubernetes-keyvault-flexvol
[linux-capabilities]: http://man7.org/linux/man-pages/man7/capabilities.7.html
[selinux-labels]: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.12/#selinuxoptions-v1-core
[aks-associated-projects]: https://github.com/Azure/AKS/blob/master/previews.md#associated-projects

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[aks-pod-identities]: operator-best-practices-identity.md#use-pod-identities
[apparmor-seccomp]: operator-best-practices-cluster-security.md#secure-container-access-to-resources

---
title: Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtök
description: Ismerje meg, hogyan használható az Azure Data Lake Storage Gen2 Azure HDInsight-fürtökkel.
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: hrasheed
ms.openlocfilehash: dd639ae7e05309ab4528eb460ce38550db4cffe1
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670764"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtök

Az Azure Data Lake Storage Gen2 egy felhős társzolgáltatás, a big data-analitika, Azure Blob storage épülő dedikált. Data Lake Storage Gen2 kombinálja az Azure Blob storage és az Azure Data Lake Storage Gen1 képességeit. Az eredményül kapott service szolgáltatást kínál az Azure Data Lake Storage Gen1, például a fájlrendszer szemantikáját, a címtár-szintű és fájlszintű biztonsági és a méretezhetőség, az alacsony költségű, többrétegű tárolást, magas rendelkezésre állású és vész-helyreállítási képességeit az Azure Blob storage.

## <a name="data-lake-storage-gen2-availability"></a>Data Lake Storage Gen2 rendelkezésre állása

Data Lake Storage Gen2 alapértelmezett és a egy tárfiókot, szinte az összes Azure HDInsight-fürttípusok a tárolási beállításként érhető el. A HBase, azonban csak egy Data Lake Storage Gen2-fiókot is rendelkezik.

> [!Note]  
> Miután kiválasztotta a Data Lake Storage Gen2, mint a **elsődleges tárolási típusok**, nem választhatja ki a Data Lake Storage Gen1 fiók kiegészítő tárolóként.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>Data Lake Storage Gen2-fürt létrehozása az Azure Portalon keresztül

Hozzon létre egy HDInsight-fürtön, amely a Data Lake Storage Gen2 használ a tároláshoz, kövesse ezeket a lépéseket a Data Lake Storage Gen2-fiókok konfigurálása.

### <a name="create-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása

Felhasználó által hozzárendelt felügyelt identitás, létrehozása, ha még nem rendelkezik. Lásd: [létrehozása, list, delete vagy egy az Azure portal használatával felügyelt felhasználó által hozzárendelt identitások szerepkör hozzárendelése](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity). Felügyelt identitások munka az Azure HDInsight további információkért lásd: [által felügyelt identitásokat az Azure HDInsight](hdinsight-managed-identities.md).

![Felhasználó által hozzárendelt felügyelt identitás létrehozása](./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>A Data Lake Storage Gen2-fiók létrehozása

Hozzon létre egy Azure Data Lake Storage Gen2 tárfiókot. Győződjön meg arról, hogy a **hierarchikus névtér** beállítás engedélyezve van. További információkért lásd: [a rövid útmutató: Hozzon létre egy Azure Data Lake Storage Gen2 tárfiókot](../storage/blobs/data-lake-storage-quickstart-create-account.md).

![Képernyőfelvétel: a tárfiók létrehozása az Azure Portalon](./media/hdinsight-hadoop-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>A felügyelt identitás a Data Lake Storage Gen2 fiók engedélyeinek beállítása

A felügyelt identitás hozzárendelése a **Storage Blob adatok tulajdonosa** szerepkör a tárfiókban. További információkért lásd: [kezelés hozzáférési jogosultságok RBAC (előzetes verzió) az adatok Azure Blob és üzenetsor](../storage/common/storage-auth-aad-rbac.md).

1. Az a [az Azure portal](https://portal.azure.com), lépjen a tárfiókhoz.
1. Válassza ki a tárfiókot, majd válassza ki **hozzáférés-vezérlés (IAM)** a fiókhoz tartozó hozzáférés-vezérlési beállítások megjelenítéséhez. Válassza ki a **szerepkör-hozzárendelések** lapján megtekintheti a szerepkör-hozzárendelések listáját.
    
    ![Képernyőfelvétel: a tárolási hozzáférés-vezérlési beállításokkal](./media/hdinsight-hadoop-data-lake-storage-gen2/portal-access-control.png)
    
1. Válassza ki a **+ szerepkör-hozzárendelés hozzáadása** gombra kattintva adhat hozzá egy új szerepkör.
1. Az a **szerepkör-hozzárendelés hozzáadása** ablakban válassza ki a **Storage Blob adatok tulajdonosa** szerepkör. Ezután válassza ki a felügyelt identitás- és storage-fiókot tartalmazó előfizetést. Ezután keresse meg a felhasználó által hozzárendelt felügyelt identitás, amelyet korábban hozott létre. Végül válassza ki a felügyelt identitást, és alatt helyezkednek el **kiválasztott tagok**.
    
    ![Képernyőfelvétel: hogyan kell egy RBAC szerepkör hozzárendelése](./media/hdinsight-hadoop-data-lake-storage-gen2/add-rbac-role3.png)
    
1. Kattintson a **Mentés** gombra. A kiválasztott felhasználó által hozzárendelt identitás megjelenik a kiválasztott szerepkör alatt.
1. A kezdeti telepítés befejezése után létrehozhat egy fürtöt a portálon keresztül. A fürt a tárfiók Azure ugyanabban a régióban kell lennie. Az a **tárolási** szakasz a fürt létrehozási menü, válassza ki a következő beállításokat:
        
    * A **elsődleges tárolási típusok**válassza **Azure Data Lake Storage Gen2**.
    * A **válassza ki a tárfiókot**, keresse meg és válassza ki a Data Lake Storage Gen2 újonnan létrehozott tárfiókot.
        
        ![Az Azure HDInsight használata a Data Lake Storage Gen2-tárolási beállításai](./media/hdinsight-hadoop-data-lake-storage-gen2/primary-storage-type-adls-gen2.png)
    
    * A **identitás**, válassza ki a megfelelő előfizetést, és az újonnan létrehozott felhasználó által hozzárendelt identitás.
        
        ![A Data Lake Storage Gen2 használata Azure HDInsight identitás](./media/hdinsight-hadoop-data-lake-storage-gen2/managed-identity-cluster-creation.png)
        
> [!Note]
> Adjon hozzá egy másodlagos Data Lake Storage Gen2-fiókot, a storage-fiók szintjén, egyszerűen rendelje hozzá a korábban létrehozott új Data Lake Storage Gen2 storage-fiókhoz hozzáadni kívánt felügyelt identitás. Meg kell azt javasoljuk, hogy a HDInsight ad hozzá egy másodlagos Data Lake Storage Gen2-fiókot a "további tárfiókok" panelen keresztül nem támogatott. 

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>A fürt létrehozása a Data Lake Storage Gen2 az Azure CLI-n keresztül

Is [sablon mintafájl letöltése](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) és [paraméterek mintafájl letöltése](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). Mielőtt a sablon és az alábbi Azure CLI-vel kódrészlet használatával, cserélje le a helyőrzőket megfelelő értékekre:

| Helyőrző | Leírás |
|---|---|
| `<SUBSCRIPTION_ID>` | Az Azure-előfizetés azonosítója |
| `<RESOURCEGROUPNAME>` | Az erőforráscsoport, ahol azt szeretné, hogy az új fürthöz és a tárolási fiók létrehozva. |
| `<MANAGEDIDENTITYNAME>` | A felügyelt identitás, amely engedélyeket kap az Azure Data Lake Storage Gen2-fiókja nevére. |
| `<STORAGEACCOUNTNAME>` | Az új Azure Data Lake Storage Gen2-fiókot létrehozni. |
| `<CLUSTERNAME>` | A HDInsight-fürt nevére. |
| `<PASSWORD>` | A választott jelszó a fürthöz az SSH, valamint az Ambari irányítópulton való bejelentkezéshez. |

Az alábbi kódrészlet hajtja végre a következő lépések:

1. Naplók az Azure-fiókjába.
1. Állítja be az aktív előfizetést, ahol a létrehozás műveletek történik.
1. Az új központi telepítési tevékenységek egy új erőforráscsoport jön létre. 
1. Egy felhasználó által hozzárendelt felügyelt eszközidentitást hoz létre.
1. Az Azure CLI-funkciók használata a Data Lake Storage Gen2 hozzáad egy bővítményt.
1. Létrehoz egy új Data Lake Storage Gen2-fiók használatával a `--hierarchical-namespace true` jelzőt. 

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus

# Create managed identity
az identity create -g <RESOURCEGROUPNAME> -n <MANAGEDIDENTITYNAME>

az extension add --name storage-preview

az storage account create --name <STORAGEACCOUNTNAME> \
    --resource-group <RESOURCEGROUPNAME> \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

Ezután jelentkezzen be a portálra. Adja hozzá az új felhasználóhoz felügyelt identitásnak a **Storage-Blobadatok Közreműködője** szerepkör a tárfiókban, a 3. lépésben leírtak szerint [az Azure portal használatával](hdinsight-hadoop-use-data-lake-storage-gen2.md).

Miután hozzárendelte a szerepkört a felhasználó által hozzárendelt felügyelt identitás, a sablon üzembe helyezése a következő kódrészlet használatával.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Hozzáférés-vezérlés a Data Lake Storage Gen2 a HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Milyen típusú engedélyeket támogatja a Data Lake Storage Gen2?

Data Lake Storage Gen2-hozzáférés-vezérlési modellt, amely támogatja a szerepköralapú hozzáférés-vezérlés (RBAC) és a POSIX-hoz hasonló hozzáférés-vezérlési listák (ACL) használja. A Data Lake Storage Gen1 támogatja a hozzáférés-vezérlési listák csak az adatokhoz való hozzáférés szabályozása.

RBAC szerepkör-hozzárendelések használja a alkalmazni az engedélyek beállítása a felhasználók, csoportok és az egyszerű szolgáltatások Azure-erőforrások. Általában ezek az Azure előrébb a legfelső szintű erőforrásokhoz (például az Azure storage-fiókok). Az Azure Storage, és emellett Data Lake Storage Gen2 Ez a mechanizmus most már elérhető a fájl rendszererőforrás.

 Az RBAC fájl engedélyekkel kapcsolatos további információkért lásd: [Azure szerepköralapú hozzáférés-vezérlés (RBAC)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac).

Hozzáférés-vezérlési fájl engedélyekkel kapcsolatos további információkért lásd: [hozzáférés-vezérlési listák a fájlok és könyvtárak](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Hogyan szabályozhatja a hozzáférést az adatokhoz, a Data Lake Storage Gen2?

A HDInsight-fürt lehetővé teszi fájlok elérése a Data Lake Storage Gen2 szabályozott felügyelt identitások használatával. Egy felügyelt identitás regisztrálva az Azure Active Directory (Azure AD) hitelesítő adatokkal rendelkező Azure által felügyelt identitást. A felügyelt identitásokból nem kell regisztrálni az egyszerű szolgáltatások Azure AD-ben vagy a hitelesítő adatok, például a tanúsítványok kezelése.

Azure-szolgáltatások felügyelt identitások két típusa van: rendszer-, felhasználó hozzárendelve. HDInsight Data Lake Storage Gen2 hozzáférés felügyelt identitások felhasználó által hozzárendelt használ. Egy felhasználó által hozzárendelt felügyelt identitás egy önálló Azure-erőforrás jön létre. Egy létrehozási folyamaton keresztül az Azure létrehoz egy identitást a használt előfizetés által megbízhatónak tekintett Azure AD-bérlőn. Az identitás a létrehozását követően hozzárendelhető egy vagy több Azure-beli szolgáltatáspéldányhoz.

A felhasználó által hozzárendelt identitások életciklusa külön van kezelve azon Azure-beli szolgáltatáspéldányokétól, amelyekhez hozzá lettek rendelve. Felügyelt identitások kapcsolatos további információkért lásd: [hogyan hajtsa végre a felügyelt identitások Azure-erőforrások munka?](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Hogyan állíthatom be az Azure AD-felhasználók engedélyek használatával adatokat lekérdezni a Data Lake Storage Gen2 Hive vagy az egyéb szolgáltatások segítségével?

Felhasználók engedélyeinek használatával adatokat lekérdezni, használja az Azure AD biztonsági csoportok, a hozzárendelt rendszerbiztonsági tag ACL-ek. Közvetlenül nem fájl-hozzáférési engedélyek hozzárendelése egyes felhasználókhoz vagy az egyszerű szolgáltatások. Ha az Azure AD biztonsági csoportok segítségével engedélyeket szabályozásához, hozzáadhat és felhasználók vagy egyszerű szolgáltatások eltávolítása nélkül, egy teljes könyvtárstruktúrát ACL-ek újbóli. Csak akkor kell hozzáadni vagy eltávolítani a felhasználókat a megfelelő Azure AD biztonsági csoportot. Hozzáférés-vezérlési listák nem örökölt, így újbóli ACL-eket frissíteni kell az az ACL-t minden fájl és alkönyvtárat.

## <a name="next-steps"></a>További lépések

* [Az Azure HDInsight-integrációja az Data Lake Storage Gen2 előzetes verzió – hozzáférés-szabályozási és biztonsági frissítése](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Bevezetés az Azure Data Lake Storage Gen2-re](../storage/blobs/data-lake-storage-introduction.md)

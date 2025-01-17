---
title: Egy Azure Data Explorer fürt és adatbázis létrehozása az Azure CLI-vel
description: Ismerje meg, hogyan hozhat létre Azure Data Explorer fürt és adatbázis az Azure CLI-vel
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: e771def95db00b5de8c27011641a628560952970
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66494792"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-azure-cli"></a>Egy Azure Data Explorer fürt és adatbázis létrehozása az Azure CLI-vel

> [!div class="op_single_selector"]
> * [Portál](create-cluster-database-portal.md)
> * [Parancssori felület](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>

Az Azure Data Explorer egy gyors, teljes mértékben felügyelt adatelemző szolgáltatás, amellyel valós idejű elemzést végezhet többek között alkalmazások, webhelyek és IoT-eszközök nagy mennyiségű adatfolyamain. Az Azure Data Explorer használatához, először hozzon létre egy fürtöt, és az adott fürt egy vagy több adatbázis létrehozása. Ezután (betöltés) adatokat az adatbázisba fogadására, így vonatkozóan, lekérdezéseket futtathat. Ebben a cikkben létrehozhat egy fürt és a egy adatbázis az Azure parancssori felület használatával.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk végrehajtásához Azure-előfizetés szükséges. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha helyi telepítése és használata az Azure CLI, ehhez a cikkhez az Azure CLI 2.0.4-es vagy újabb. Futtassa az `az --version` parancsot a verzió ellenőrzéséhez. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli?view=azure-cli-latest) ismertető cikket.

## <a name="configure-the-cli-parameters"></a>A CLI paramétereinek konfigurálása

A következő lépéseket nem szükséges, ha a parancsok az Azure Cloud Shellben futtatja. Ha helyileg futtatja a parancssori Felületet, kövesse az alábbi lépéseket, jelentkezzen be az Azure-ba, és állítsa be a jelenlegi előfizetésében:

1. Az alábbi parancs futtatásával jelentkezzen be az Azure-ba:

    ```azurecli-interactive
    az login
    ```

1. Ha azt szeretné, hogy a fürt létrehozása az előfizetés beállításához. Cserélje le `MyAzureSub` használni kívánt Azure-előfizetés nevére:

    ```azurecli-interactive
    az account set --subscription MyAzureSub
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Az Azure Data Explorer-fürt létrehozása

1. A fürt létrehozásához a következő paranccsal:

    ```azurecli-interactive
    az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
    ```

   |**Beállítás** | **Ajánlott érték** | **Mező leírása**|
   |---|---|---|
   | name | *azureclitest* | A fürt kívánt nevét.|
   | sku | *D13_v2* | A Termékváltozat a fürthöz használt. |
   | resource-group | *testrg* | Az erőforrás csoport neve, ahol a fürt létrejön. |

    Nincsenek további nem kötelező paraméterek, amelyet használhat, például a fürt kapacitásának.

1. A következő paranccsal ellenőrizze, hogy a fürt létrehozása sikeresen megtörtént:

    ```azurecli-interactive
    az kusto cluster show --name azureclitest --resource-group testrg
    ```

Ha az eredmény tartalmazza `provisioningState` együtt a `Succeeded` érték, akkor a fürt sikeresen létrejött.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Az adatbázis létrehozása az Azure Data Explorer fürtben

1. Az adatbázis létrehozása a következő paranccsal:

    ```azurecli-interactive
    az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period P365D --hot-cache-period P31D
    ```

   |**Beállítás** | **Ajánlott érték** | **Mező leírása**|
   |---|---|---|
   | cluster-name | *azureclitest* | A fürt, ahol létrejön az adatbázis neve.|
   | name | *clidatabase* | Az adatbázis neve.|
   | resource-group | *testrg* | Az erőforrás csoport neve, ahol a fürt létrejön. |
   | soft-delete-period | *P365D* | Azt jelzi, hogy mennyi ideig megtartott adatok lekérdezéséhez érhető el. Lásd: [adatmegőrzési](/azure/kusto/concepts/retentionpolicy) további információt. |
   | hot-cache-period | *P31D* | Azt jelzi, hogy mennyi ideig megtartott adatok a gyorsítótárban. Lásd: [házirend gyorsítótár](/azure/kusto/concepts/cachepolicy) további információt. |

1. A következő paranccsal tekintse meg az Ön által létrehozott adatbázist:

    ```azurecli-interactive
    az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
    ```

Most már egy fürt és a egy adatbázist.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

* Ha azt tervezi, hajtsa végre a más cikkeket, megtarthatja a létrehozott erőforrásokat.
* Erőforrások törléséhez törölje a fürtöt. Ha töröl egy fürtöt, benne az adatbázisokat is törli. A következő paranccsal törölje a fürtöt:

    ```azurecli-interactive
    az kusto cluster delete --name azureclitest --resource-group testrg
    ```

## <a name="next-steps"></a>További lépések

* [Az Azure Data Explorer Python-kódtár használata az adatok betöltése](python-ingest-data.md)

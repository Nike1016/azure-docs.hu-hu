---
title: Rövid Azure-útmutató – Titkos kulcs beállítása és lekérése a Key Vaultból az Azure Portal használatával | Microsoft Docs
description: Rövid útmutató, amely bemutatja a titkos kulcsok beállítását és lekérését az Azure Key Vaultból az Azure Portal használatával
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: barclayn
ms.openlocfilehash: e9b86a5fb0d9e24618cafffb5ca12e22d5394294
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64730182"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-the-azure-portal"></a>Gyors útmutató: Beállítása és lekérése a titkos kulcs Azure Key vault az Azure portal használatával

Az Azure Key Vault egy olyan felhőszolgáltatás, amely a titkos kulcsok biztonságos tárolására szolgál. Biztonságosan tárolhatja kulcsait, jelszavait, tanúsítványait és egyéb titkos adatait. Az Azure-kulcstartók létrehozhatók és kezelhetők az Azure Portal segítségével is. Ebben a rövid útmutatóban egy kulcstartót hoz létre, majd eltárol benne egy titkos kulcsot. A Key Vaulttal kapcsolatosan további információt az [Áttekintés](key-vault-overview.md) szakaszban talál.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-vault"></a>Tároló létrehozása

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra

    ![Kimenet a Key Vault létrehozási parancsának befejeződése után](./media/quick-create-portal/search-services.png)
2. A keresőmezőbe írja be a **Key Vault** kifejezést.
3. Az eredmények listájában válassza a **Key Vault** lehetőséget.
4. A Key Vault szakaszban kattintson a **Létrehozás** gombra.
5. A **Kulcstartó létrehozása** szakaszban adja meg a következő információkat:
    - **Név**: Egy egyedi nevét kötelező megadni. Ehhez a rövid útmutatóhoz a **Contoso-vault2** nevet használjuk. 
    - **Előfizetés**: Válasszon előfizetést.
    - Az **Erőforráscsoport** területen válassza az **Új létrehozása** lehetőséget, és írja be az erőforráscsoport nevét.
    - A **Hely** legördülő menüből válassza ki a helyet.
    - A többi beállítást hagyja az alapértelmezett értéken.
6. A fenti adatok megadása után válassza a **Létrehozás** elemet.

Jegyezze fel az alábbi két tulajdonságot:

* **Tároló neve**: A példában ez a **Contoso-Vault2**. Ezt a nevet fogja majd más lépésekben is használni.
* **Tároló URI-ja**: A példában ez a https://contoso-vault2.vault.azure.net/. A tárolót a REST API-ján keresztül használó alkalmazásoknak ezt az URI-t kell használniuk.

Jelenleg csak az Azure-fiókja jogosult arra, hogy műveleteket végezzen ezen az új kulcstartón.

![Kimenet a Key Vault létrehozási parancsának befejeződése után](./media/quick-create-portal/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Titkos kulcs hozzáadása a Key Vaulthoz

Titkos kód a tárolóhoz való hozzáadásához csak néhány további lépést kell végrehajtania. Ebben az esetben egy alkalmazás által használható jelszót fogunk megadni. A jelszó neve **ExamplePassword** és az értéket tároljuk **hVFkk965BuUv** benne.

1. A Key Vault-tulajdonságok lapján válassza a **Titkos kódok** lehetőséget.
2. Kattintson a **Létrehozás/Importálás** gombra.
3. A **Titkos kód létrehozása** képernyőn válassza az alábbi értékeket:
    - **Feltöltési beállítások**: Manuális.
    - **Név**: ExamplePassword.
    - **Érték**: hVFkk965BuUv
    - A többi értéket hagyja az alapértelmezett értéken. Kattintson a **Create** (Létrehozás) gombra.

Miután megérkezett az üzenet arról, hogy a titkos kulcs sikeresen létrejött, kattintson rá a listában. Ezután megjelenik néhány tulajdonság. Ha a jelenlegi verzióra kattint, láthatja az előző lépésben megadott értéket.

![Titkos kulcs tulajdonságai](./media/quick-create-portal/current-version-hidden.png)

A jobb oldali "Titkos érték megjelenítése" gombra kattintva megtekintheti a rejtett érték. 

![A titkos értéket jelent meg.](./media/quick-create-portal/current-version-shown.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Erre a rövid útmutatóra egyéb Key Vault-útmutatók és oktatóanyagok is épülnek. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, ne törölje ezeket az erőforrásokat.
Ha már nincs rá szükség, törölje az erőforráscsoportot. Ezzel törli a kulcstartót és a kapcsolódó erőforrásokat is. Az erőforráscsoport törlése a Portalon keresztül:

1. Írja be az erőforráscsoport nevét a Portal tetején található keresőmezőbe. Amikor az eredmények listájában megjelenik az ebben a rövid útmutatóban használt erőforráscsoport, jelölje ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe írja be az erőforráscsoport nevét, és válassza a **Törlés** lehetőséget.


## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy kulcstartót, és elhelyezett benne egy titkos kulcsot. Ha bővebb információra van szüksége a Key Vaultról és arról, hogyan használhatja az alkalmazásaival, lépjen tovább a Key Vaulttal használható webalkalmazásokat bemutató oktatóanyagra.

> [!div class="nextstepaction"]
> Ha meg szeretné tudni, hogyan olvashatja be a Key Vault titkos kulcsait egy, az Azure-erőforrások felügyelt identitásait használó webalkalmazásból, lépjen tovább a következő, az [Azure-webalkalmazások a Key Vault titkos kulcsainak olvasásához történő konfigurálását](quick-create-net.md) ismertető oktatóanyagra.

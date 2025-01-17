---
title: Felügyelheti az Azure IoT Central alkalmazáshoz |} A Microsoft Docs
description: A rendszergazdák az Azure IoT központi alkalmazás felügyelete
author: viv-liu
ms.author: viviali
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 87ed31836fcda922b085ec951eb6d9d14542db6a
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65467563"
---
# <a name="administer-your-iot-central-application"></a>Az IoT-központ alkalmazás felügyelete

Miután létrehozott egy IoT-központ alkalmazás, nyissa meg a **felügyeleti** szakaszt:

- Alkalmazásbeállítások kezelése
- Felhasználók kezelése
- Szerepkörök kezelése
- A számla megtekintése
- A próbaidőszak váltson használatalapú fizetésre
- Adatok exportálása
- Eszköz kapcsolat kezelése
- Hozzáférési kódok használata a fejlesztői eszközök
- Az alkalmazás a felhasználói felület testreszabása
- Súgóhivatkozások az alkalmazás testreszabása
- IoT-központ programozással felügyelheti

Hogy eléri és használja a **felügyeleti** szakaszban kell lennie a a **rendszergazda** szerepkört az Azure IoT Central alkalmazáshoz. Azure IoT Central alkalmazást hoz létre, ha automatikusan kapott a **rendszergazda** szerepkör az adott alkalmazáshoz. A [felhasználók kezelése](#manage-users) szakasz ebben a cikkben a hozzárendelése több ismerteti a **rendszergazda** szerepkör más felhasználók számára.

## <a name="manage-application-settings"></a>Alkalmazásbeállítások kezelése

### <a name="change-application-name-and-url"></a>Alkalmazás nevének módosítása és URL-címe
Az a **Alkalmazásbeállítások** lapon módosítsa a nevét és az alkalmazás URL-címet, majd válassza ki **mentése**.

![Alkalmazás-Beállítások lap](media/howto-administer/image0-a.png)

Ha a rendszergazda létrehoz egy egyéni téma az alkalmazás, ezen a lapon beállítással rendelkezik, amely elrejtése a **alkalmazásnév** a felhasználói felületen. Ez akkor hasznos, ha az alkalmazás emblémája, az egyéni témában tartalmazza az alkalmazás nevét. További információkért lásd: [testreszabása az Azure IoT Central felhasználói felület](./howto-customize-ui.md).

> [!Note]
> Ha megváltoztatja az URL-CÍMÉT, a régi URL-CÍMÉT egy másik Azure IoT Central ügyfél elvégezhet. Ha ez történik, már nem érhető el, amelyet használhat. Ha megváltoztatja az URL-CÍMÉT, a régi URL-cím már nem működik, és értesíti a felhasználókat arról, hogy az új URL-cím használatára kell.

### <a name="prepare-and-upload-image"></a>Kép előkészítése és feltöltése
Ha módosítani szeretné az alkalmazás rendszerképét, lásd: [előkészítése és a feltöltés rendszerképek az Azure IoT Central alkalmazásnak](howto-prepare-images.md).

### <a name="copy-an-application"></a>Alkalmazás másolása
Minden olyan alkalmazás, bármely eszköz példányok, az adatok eszközelőzmények és a felhasználói adatok mínusz egy másolatát is létrehozhat. A példány egy használatalapú fizetéses alkalmazást, amely akkor kell fizetnie. Ezzel a módszerrel egy próba-alkalmazás nem hozható létre.

Válassza ki **másolási**. A párbeszédpanelen adja meg, hogy az új használatalapú fizetéses alkalmazás. Válassza ki **másolási** annak ellenőrzéséhez, hogy szeretné-e továbbra is. További információ a mezőket az űrlapon található [hozzon létre egy alkalmazást](quick-deploy-iot-central.md) rövid.

![Alkalmazás-Beállítások lap](media/howto-administer/appcopy2.png)

Miután az alkalmazás másolási művelet sikeres, az új alkalmazást, a hivatkozás használatával navigálhat.

![Alkalmazás-Beállítások lap](media/howto-administer/appcopy3a.png)

> [!Note]
> Alkalmazás másolása is átmásolja a szabályok és műveletek definíciója. De az eredeti alkalmazáshoz hozzáféréssel rendelkező felhasználók nem másolja át a másolt alkalmazást, mert a felhasználók manuális hozzáadása műveleteket, például e-mail, amelyhez felhasználók előfeltétele. Általában célszerű a szabályokat és műveleteket, győződjön meg arról, hogy azok az új alkalmazás naprakész, ellenőrizze, hogy legyen.

### <a name="delete-an-application"></a>Alkalmazás törlése

> [!Note]
> Egy alkalmazás törléséhez, jogosultnak kell lennie is törölje az erőforrást az Azure-előfizetésében választotta, az alkalmazás létrehozásakor. További tudnivalókért lásd: [az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése szerepköralapú hozzáférés-vezérlés használatával](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

Használja a **törlése** gombra kattintva véglegesen törli az IoT Central alkalmazáshoz. Ez a művelet véglegesen törli az alkalmazáshoz tartozó összes adatot.

## <a name="manage-users"></a>Felhasználók kezelése

### <a name="add-users"></a>Felhasználók hozzáadása

Minden rendelkezniük kell egy felhasználói fiókkal jelentkezzen be, és hozzáférést az Azure IoT Central alkalmazáshoz. A Microsoft Accounts (msa-k) és az Azure Active Directory (Azure AD) az Azure IoT Central támogatottak. Az Azure Active Directory-csoportok jelenleg nem támogatottak az Azure IoT Central.

További információkért lásd: [Microsoft-fiók súgó](https://support.microsoft.com/products/microsoft-account?category=manage-account) és [a rövid útmutató: Új felhasználók hozzáadása az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Egy felhasználó IoT Central alkalmazáshoz való hozzáadásához nyissa meg a **felhasználók** lapját a **felügyeleti** szakaszban.

    ![Felhasználók listája](media/howto-administer/image1.png)

1. A felhasználó hozzáadásához a **felhasználók** lapon a **+ Hozzáadás felhasználó**.

1. Válassza ki a felhasználót egy szerepkört a **szerepkör** legördülő menüből. További információ a szerepkörökről az [szerepkörök kezelése](#manage-roles) című szakaszát.

    ![Szerepkör kiválasztása](media/howto-administer/image3.png)

    > [!NOTE]
    >  Felhasználók tömeges hozzáadása, a felhasználó az összes olyan a hozzáadni kívánt felhasználó azonosítóját adja meg pontosvesszővel elválasztva. A munkakör kiválasztása a **szerepkör** legördülő menüből. Ezután válassza a **Save** (Mentés) lehetőséget.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>A felhasználókhoz rendelt szerepkörök szerkesztése

Szerepkörök nem módosítható, miután hozzá vannak rendelve. A felhasználóhoz hozzárendelt szerepkör módosítása, törölje a felhasználót, és adja hozzá a felhasználót újra egy másik szerepkörrel rendelkező.

### <a name="delete-users"></a>Felhasználók törlése

Törli a felhasználókat, válassza ki egy vagy több jelölőnégyzetet az **felhasználók** lap. Ezután válassza a **Törlés** elemet.

## <a name="manage-roles"></a>Szerepkörök kezelése

Szerepkörök lehetővé teszi annak vezérléséhez a szervezeten belül az IoT-központ különböző feladatok végrehajtására. Nincsenek három szerepkört hozzárendelheti az alkalmazást.

### <a name="administrator"></a>Rendszergazda

A felhasználók a **rendszergazda** szerepkör hozzáférése az összes funkciója egy alkalmazásban.

A felhasználó, aki alkalmazást hoz létre automatikusan hozzá van rendelve a **rendszergazda** szerepkör. Mindig kell legalább egy felhasználója a **rendszergazda** szerepkör.

### <a name="application-builder"></a>Alkalmazáskészítő

A felhasználók a **alkalmazás Builder** szerepkör műveletek mindegyikét egy alkalmazásban, kivéve az alkalmazás felügyeletét. Sikerei is létrehozása, szerkesztése, és törölje eszközsablonok és az eszközök, eszköz készletek felügyelete és analytics és a feladatok futtatásához. Kapcsolat építői többé nem fér hozzá a **felügyeleti** szakaszban az alkalmazás.

### <a name="application-operator"></a>Alkalmazásoperátor

A felhasználók a **alkalmazást üzemeltető** szerepkör nem hajthat végre változtatásokat eszközsablonok és az alkalmazás nem tudja felügyelni. Operátorok hozzáadása és eszközök, eszköz készletek felügyelete, és analytics és a feladatok futtatásához. Operátorok többé nem fér hozzá a **alkalmazás Builder** és **felügyeleti** oldalakat.

## <a name="view-your-bill"></a>A számla megtekintése

A számla megtekintéséhez nyissa meg a **számlázási** lapját a **felügyeleti** szakaszban. Az Azure számlázási oldalán, ahol megtekintheti a számla minden, az Azure IoT Central alkalmazások egy új lapon nyílik meg.

### <a name="convert-your-trial-to-pay-as-you-go"></a>A próbaidőszak váltson használatalapú fizetésre

Átválthat egy használatalapú fizetéses alkalmazás próbaverzió alkalmazását. Az alábbiakban az ilyen típusú alkalmazások közötti különbségeket.

- **Próbaverzió** alkalmazások ingyenesek az hét napig járnak. A lejárat előtt bármikor átalakíthatók használatalapú fizetéses alkalmazássá.
- **Használatalapú fizetés** alkalmazások után felszámított díjak eszközt, az első öt ingyenes eszközökkel.

További információk a díjszabásról az [Azure IoT Central díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/iot-central/) találhatók.

Az önkiszolgáló folyamat befejezéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a **számlázási** lapját a **felügyeleti** szakaszban.

    ![Próbaverziós állapota](media/howto-administer/freetrialbilling.png)

1. Válassza ki **utólagos elszámolású előfizetésre átváltani**.

    ![Átalakítás próba](media/howto-administer/convert.png)

1. Válassza ki a megfelelő Azure Active Directoryban, majd az Azure-előfizetés az utólagos elszámolású alkalmazás használatához.

1. Miután kiválasztotta **konvertálása**, az alkalmazás most már egy használatalapú fizetéses alkalmazást, és számlázandó megkezdése.

## <a name="export-data"></a>Adatok exportálása

Engedélyezheti a **folyamatos adatexportálás** mérések, eszközök és eszközadatok sablonok exportálása az Azure Blob storage-fiókot. Ismerje meg, hogyan [exportálhatja az adatokat](howto-export-data.md).

## <a name="manage-device-connection"></a>Eszköz kapcsolat kezelése

Csatlakozás az alkalmazásban, a kulcsok és tanúsítványok ide használatával nagy mennyiségű eszközt. Tudjon meg többet [eszközök csatlakoztatásáról](concepts-connectivity.md).

## <a name="use-access-tokens"></a>Hozzáférési kódok használata

A fejlesztői eszközök használandó hozzáférési jogkivonatokat hoz létre. A csak fejlesztői eszköz, amely jelenleg az IoT-központ explorer figyelési eszközüzenetek és tulajdonságokat és beállításokat a módosításokat. Tudjon meg többet a [IoT-központ explorer](howto-use-iotc-explorer.md).

## <a name="customize-your-application"></a>Az alkalmazás testreszabása

Színek és az alkalmazás az ikonok megváltoztatásával kapcsolatos további információkért lásd: [testreszabása az Azure IoT Central felhasználói felület](./howto-customize-ui.md).

## <a name="customize-help"></a>A súgó testreszabása

Az alkalmazás egyéni súgóhivatkozások hozzáadásával kapcsolatos további információkért lásd: [testreszabása az Azure IoT Central felhasználói felület](./howto-customize-ui.md).

## <a name="manage-programatically"></a>Amellyel programozott módon kezelése

A Node, Python, C#, Ruby, a Java és Go IoT Central Azure Resource Manager SDK-csomagok érhetők el. Ezek a csomagok segítségével létrehozása, listázása, frissíteni vagy törölje az IoT Central-alkalmazást. A csomagok tartalmazzák a hitelesítés és a hibakezelés kezelését segítő.

Példa bemutatja, hogyan használhatja az Azure Resource Manager SDK-k, annak [ https://github.com/emgarten/iotcentral-arm-sdk-examples ](https://github.com/emgarten/iotcentral-arm-sdk-examples).

További tudnivalókért tekintse meg az alábbi GitHub-adattárak és a csomagok:

| Nyelv | Adattár | Csomag |
| ---------| ---------- | ------- |
| Csomópont | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Ugrás | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>További lépések

Most, hogy megismerte az Azure IoT központi alkalmazás felügyelete, Íme a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [Az eszköz-sablon beállítása](howto-set-up-template.md)

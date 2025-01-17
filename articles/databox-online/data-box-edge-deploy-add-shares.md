---
title: Oktatóanyag való adatátvitel megosztásokat az Azure Data Box Edge |} A Microsoft Docs
description: Megtudhatja, hogyan adhat hozzá megosztásokat a Data Box Edge-eszközön, és hogyan csatlakozhat hozzájuk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to shares on Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 701ae5e70612b89c28e3092571e26e4f06389af0
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64924614"
---
# <a name="tutorial-transfer-data-with-azure-data-box-edge"></a>Oktatóanyag: Adatok áthelyezése az Azure Data Box Edge

Ez az oktatóanyag leírja, hogyan adhat hozzá, és csatlakozhat a Data Box Edge-eszközön megosztások. Miután hozzáadta a megosztásokat, a Data Box Edge küldhetnek adatokat az Azure-bA.

A folyamat elvégzése körülbelül 10 percet vesz igénybe.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Megosztás hozzáadása
> * Csatlakozás a megosztáshoz

 
## <a name="prerequisites"></a>Előfeltételek

Mielőtt megosztások hozzá Data Box Edge, ellenőrizze, hogy:

- Ismertetett módon, hogy telepítette a fizikai eszköz [telepítse az Azure Data Box Edge](data-box-edge-deploy-install.md).

- Leírtak szerint, hogy aktiválta a fizikai eszköz [kapcsolódás beállítása, és aktiválja az Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="add-a-share"></a>Megosztás hozzáadása

Megosztás létrehozásához hajtsa végre az alábbi eljárást:

1. Az a [az Azure portal](https://portal.azure.com/), válassza ki a Data Box Edge erőforrást, és keresse meg a **áttekintése**. Az eszköz online állapotúnak kell lennie.

   ![Az eszköz online](./media/data-box-edge-deploy-add-shares/device-online-1.png)

2. Válassza ki **+ Hozzáadás megosztás** az eszköz parancssávon.

   ![Megosztás hozzáadása](./media/data-box-edge-deploy-add-shares/select-add-share-1.png)

3. Az a **Hozzáadás megosztás** panelen hajtsa végre az alábbi eljárást:

    a. Az a **neve** mezőben adjon meg egy egyedi nevet a megosztáshoz.  
    A megosztás neve nem lehet csak kisbetűket, számokat és kötőjeleket tartalmazhat. 3 – 63 karakter között lehet kell, és betűvel vagy egy szám előtaggal kell kezdődnie. Kötőjel előtt legyen, és betűvel vagy egy szám követ.
    
    b. Válassza ki a megosztás **típusát**.  
    A típus **SMB** vagy **NFS** lehet. Az alapértelmezett érték az SMB. Ez a szokásos típus Windows-ügyfelekhez, míg az NFS a Linux rendszerű ügyfelekhez használatos.  
    Függően választotta-e az SMB vagy NFS-megosztások, a többi beállítást kissé eltérő lehet. 

    c. Adja meg a megosztást tároló tárfiók. 

    
    d. Az a **társzolgáltatás** legördülő listában válassza **Blokkblob**, **Lapblob**, vagy **fájlok**.  
    A kiválasztott szolgáltatásokat típusa attól függ, a formátumot szeretné az adatokat, az Azure-ban. Ebben a példában, mivel az adatok tárolásához Azure-ban blokkblobként kiválasztjuk **Blokkblob**. Ha **Lapblob**, ügyeljen arra, hogy az adatok igazítva 512 bájt. A VHDX például mindig 512 bájtos igazítású.

    e. Hozzon létre egy új blob-tárolót, vagy használjon egy meglévőt a legördülő listából. Ha egy blob-tárolót létrehozni, adja meg a tároló nevét. Ha egy tároló nem létezik, az újonnan létrehozott megosztás neve az a tárfiók létrejön.
   
    f. Attól függően, hogy létrehozott egy SMB-megosztás vagy egy NFS-megosztás tegye a következőket: 
     
    - **SMB-megosztás**: Alatt **jogosultság az összes helyi felhasználó**válassza **új létrehozása** vagy **meglévő**. Ha létrehoz egy új helyi felhasználót, adja meg a felhasználónevet és jelszót, és erősítse meg a jelszót. Ez a művelet rendel a helyi felhasználói engedélyeket. Miután az engedélyek itt társított, fájlkezelő segítségével módosíthatja őket.

        Ha a **csak olvasási műveletek engedélyezése** jelölőnégyzetet a megosztás adataihoz, megadhatja a csak olvasható felhasználók.

        ![SMB-megosztás hozzáadása](./media/data-box-edge-deploy-add-shares/add-share-smb-1.png)
   
    - **Az NFS-megosztások**: Adja meg az IP-címek engedélyezett ügyfelek elérhetik a megosztásokat.

        ![NFS-megosztás hozzáadása](./media/data-box-edge-deploy-add-shares/add-share-nfs-1.png)
   
4. Válassza ki **létrehozás** a megosztás létrehozásához.
    
    Értesítést kap arról, hogy a megosztás létrehozása folyamatban van. A megadott beállításokkal a megosztás létrehozása után a **megosztások** csempére, hogy az új megosztásban frissítéseket.
    

## <a name="connect-to-the-share"></a>Csatlakozás a megosztáshoz

Mostantól csatlakozhat egy vagy több, amely az előző lépésben létrehozott megosztás. Attól függően, hogy van-e az SMB vagy NFS-megosztás esetén, a lépések eltérőek lehetnek.

### <a name="connect-to-an-smb-share"></a>Csatlakozás SMB-megosztáshoz

A Windows Server-ügyfél csatlakozik a Data Box peremhálózati eszköz a parancsok beírásával csatlakozzon egy SMB-megosztás:


1. Egy parancsablakban írja be:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

2. Amikor a rendszer erre kéri, adja meg a jelszót a fájlmegosztás.  
   Az alábbiakban látható egy példa a parancs kimenetére.

    ```powershell
    Microsoft Windows [Version 10.0.16299.192)
    (c) 2017 Microsoft Corporation. All rights reserved.
    
    C: \Users\DataBoxEdgeUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60':
    The command completed successfully.
    
    C: \Users\DataBoxEdgeUser>
    ```   


3. Válassza ki a billentyűzet Windows + r billentyűkombinációt.

4. Az a **futtatása** ablakban adja meg a `\\<device IP address>`, majd válassza ki **OK**.  
   Megnyílik a Fájlkezelőben. Most már lehet a megosztások, mappák létrehozott megtekintheti. A Fájlkezelőben kattintson duplán egy megosztási (mappa), a tartalom megtekintéséhez.
 
    ![Csatlakozás SMB-megosztáshoz](./media/data-box-edge-deploy-add-shares/connect-to-share2.png)

    Az adatokat azok előállításakor a rendszer a megosztásba írja, majd az eszköz továbbítja az adatokat a felhőbe.

### <a name="connect-to-an-nfs-share"></a>Csatlakozás NFS-megosztáshoz

A Linux-ügyfél a Data Box peremhálózati eszköz csatlakozik hajtsa végre az alábbi eljárást:

1. Győződjön meg arról, hogy az ügyfél rendelkezik-e az NFSv4 ügyfél telepítve van. Ha nincs, az NFS-ügyfél telepítéséhez használja az alábbi parancsot:

   `sudo apt-get install nfs-common`

    További információkat az [NFSv4-ügyfél telepítését ismertető](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client) cikkben találhat.

2. Az NFS-ügyfél telepítése után csatlakoztassa az NFS-megosztás, amely a következő parancs használatával hozta létre a Data Box Edge-eszközön:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    > [!IMPORTANT]
    > Felhasználása `sync` beállítás megosztások csatlakoztatása közben növeli az átviteli sebességre, a nagy méretű fájlok.
    > Mielőtt csatlakoztatja a megosztást, ellenőrizze, hogy a rendszer már létrehozott a könyvtárakhoz, akkor a helyi számítógépen csatlakozási fog működni. Ezek a könyvtárak nem tartalmazhat bármilyen fájl vagy almappa.

    Az alábbi példa bemutatja az NFS-en keresztüli csatlakozást egy Data Box Edge-eszközön található megosztáshoz. Az eszköz IP-címe: `10.10.10.60`. A `mylinuxshare2` megosztás az ubuntuVM virtuális géphez csatlakozik. A megosztás csatlakoztatási pontja: `/home/databoxubuntuhost/edge`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/Edge`

> [!NOTE] 
> Az alábbi korlátozásokkal erre a kiadásra vonatkoznak:
> - A megosztásban található fájl létrehozása után a fájl átnevezése nem támogatott. 
> - Egy megosztásban található fájl törlése nem törli a tárfiókot a bejegyzést.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megismerkedett a következő Data Box Edge témakörök:

> [!div class="checklist"]
> * Megosztás hozzáadása
> * Csatlakozás megosztáshoz

Megtudhatja, hogyan átalakítja az adatokat a Data Box Edge használatával, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Adatok átalakítása a Data Box Edge segítségével](./data-box-edge-deploy-configure-compute.md)



---
title: Oktatóanyag a kicsomagoláshoz, kapcsolódáshoz, zárolás feloldásához Azure Data Box Disk | Microsoft Docs
description: Az oktatóanyag az Azure Data Box Disk üzembe helyezését mutatja be
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 49f3f608ff34847905b219047af843db00da78c4
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68480049"
---
::: zone target="docs"

# <a name="tutorial-unpack-connect-and-unlock-azure-data-box-disk"></a>Oktatóanyag: Azure Data Box Disk kicsomagolása, összekötése és feloldása

Az oktatóanyag az Azure Data Box Disk kicsomagolását, csatlakoztatását és a zárolás feloldását mutatja be.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A Data Box Disk kicsomagolása.
> * Csatlakozás lemezekhez és a hozzáférési kulcs lekérése
> * Lemezek zárolásának feloldása Windows-ügyfélen
> * Lemezek zárolásának feloldása Linux-ügyfélen

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Elvégezte az [oktatóanyagot: Megrendelés Azure Data Box Disk](data-box-disk-deploy-ordered.md).
2. Megkapta a meghajtókat, és a portálon a feldolgozás **Kézbesítve** állapotra frissül.
3. Rendelkezik egy ügyfélszámítógéppel, amelyen telepítheti a Data Box Disk lemezzárolás-feloldó eszköz. Az ügyfélszámítógépen:
    - egy [támogatott operációs rendszernek](data-box-disk-system-requirements.md#supported-operating-systems-for-clients) kell futnia;
    - Windows-ügyfél esetén további [szükséges szoftvereknek](data-box-disk-system-requirements.md#other-required-software-for-windows-clients) telepítve kell lennie.  

## <a name="unpack-your-disks"></a>A meghajtók kicsomagolása

 A meghajtók kicsomagolásához kövesse az alábbi lépéseket.

1. A Data Box Disk-meghajtókat kisméretű dobozokban küldjük el. Nyissa ki a dobozt, és vegye ki a tartalmát. Ellenőrizze, hogy a doboz tartalmazza-e az 1–5 SSD-meghajtót, valamint meghajtónként egy USB-csatlakozókábelt. Vizsgálja meg, hogy a dobozon nincsenek-e illetéktelen felnyitás nyomai vagy látható sérülések. 

    ![Data Box Disk szállítási csomag](media/data-box-disk-deploy-set-up/data-box-disk-ship-package1.png)

2. Ha a csomagot illetéktelenül kinyitották vagy jelentős sérülések láthatók rajta, ne nyissa ki. Vegye fel a kapcsolatot a Microsoft támogatási szolgálatával, ahol szakembereink segíthetnek felmérni, hogy a meghajtók működőképes állapotban vannak-e, vagy esetleg küldenünk kell egy újabb csomagot.
3. Ellenőrizze, hogy a dobozon van-e egy átlátszó tartó egy fuvarlevélcímkével (az aktuális címke alatt) a visszaküldéshez. Ha a címke elveszett vagy sérült, az Azure Portalról letöltheti és kinyomtathatja. 

    ![Data Box Disk-fuvarlevélcímke](media/data-box-disk-deploy-set-up/data-box-disk-package-ship-label.png)

4. Őrizze meg a dobozt és a térkitöltő anyagot a meghajtók visszaküldéséhez.

## <a name="connect-to-disks-and-get-the-passkey"></a>Csatlakozás lemezekhez és a hozzáférési kulcs lekérése 

1. A csomagban foglalt kábellel csatlakoztassa a lemezt egy támogatott operációsrendszer-verziót futtató ügyfélszámítógéphez (lásd az előfeltételeket). 

    ![A Data Box Disk csatlakoztatása](media/data-box-disk-deploy-set-up/data-box-disk-connect-unlock.png)    
    
2. Az Azure Portalon lépjen az **Általános > Eszköz adatai** menüpontra. A hozzáférési kulcs másolásához kattintson a másolás ikonra. Ez a hozzáférési kulcs oldja fel a lemezek zárolását.

    ![A Data Box Disk zárolás feloldására szolgáló hozzáférési kulcsa](media/data-box-disk-deploy-set-up/data-box-disk-get-passkey.png) 

Attól függően, hogy Windows- vagy Linux-ügyfélhez csatlakozik, a lemezek zárolása feloldásának lépései eltérőek.

## <a name="unlock-disks-on-windows-client"></a>Lemezek zárolásának feloldása Windows-ügyfélen

A meghajtók csatlakoztatásához és a zárolás feloldásához kövesse az alábbi lépéseket.
     
1. Az Azure Portalon lépjen az **Általános > Eszköz adatai** menüpontra. 
2. Töltse le a Data Box Disk Windows-ügyfélhez tartozó eszközkészletét. Ez az eszközkészlet 3 eszközt tartalmaz: Data Box Disk-feloldási eszköz, Data Box Disk ellenőrző eszköz és Data Box Disk felosztott másolási eszköz. 

    Ezúttal csak a Data Box Disk lemezzárolás-feloldó eszközét fogja használni. A két további eszköz később kap szerepet.

    > [!div class="nextstepaction"]
    > [Data Box Disk eszközkészlet letöltése Windowsra](https://aka.ms/databoxdisktoolswin)         

3. Csomagolja ki az eszközkészletet ugyanarra a számítógépre, amelyet az adatok másolásához fog használni. 
4. Nyisson meg egy parancssori ablakot, vagy futtassa a Windows PowerShellt rendszergazdai jogosultsággal ugyanazon a számítógépen.
5. (Nem kötelező) Annak érdekében, hogy meggyőződhessen róla, hogy a meghajtó zárolásának feloldásához használt számítógép teljesíti az operációs rendszerre vonatkozó követelményeket, futtassa a rendszerellenőrzési parancsot. Az alábbiakban egy példa látható a kimenetre. 

    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /SystemCheck
    Successfully verified that the system can run the tool.
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ``` 

6. Futtassa a `DataBoxDiskUnlock.exe` parancsot, és adja meg a hozzáférési kulcsot, amelyet a [Csatlakozás lemezekhez és a hozzáférési kulcs lekérése](#connect-to-disks-and-get-the-passkey) részben szerzett meg. Megjelenik a meghajtóhoz rendelt betűjel. Az alábbiakban egy példa látható a kimenetre.

    ```powershell
    PS C:\WINDOWS\system32> cd C:\DataBoxDiskUnlockTool\DiskUnlock
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe
    Enter the passkey :
    testpasskey1
    
    Following volumes are unlocked and verified.
    Volume drive letters: D:
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```

7. Ismételje meg a zárolás feloldásának lépéseit minden alkalommal, amikor új lemezt kell behelyezni. Amennyiben segítségre van szüksége a Data Box Disk lemezzárolás-feloldó eszköz használatához, használja a `help` parancsot.   

    ```powershell
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /help
    USAGE:
    DataBoxUnlock /PassKey:<passkey_from_Azure_portal>
    
    Example: DataBoxUnlock /PassKey:<your passkey>
    Example: DataBoxUnlock /SystemCheck
    Example: DataBoxUnlock /Help
    
    /PassKey:        Get this passkey from Azure DataBox Disk order. The passkey unlocks your disks.
    /SystemCheck:    This option checks if your system meets the requirements to run the tool.
    /Help:           This option provides help on cmdlet usage and examples.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```  
8. A meghajtó zárolásának feloldása után megtekintheti annak tartalmát.    

    ![A Data Box Disk tartalma](media/data-box-disk-deploy-set-up/data-box-disk-content.png)

Ha a lemezek feloldása során problémákba ütközik, tekintse meg a feloldási [problémák elhárítása](data-box-disk-troubleshoot-unlock.md)című témakört. 

## <a name="unlock-disks-on-linux-client"></a>Lemezek zárolásának feloldása Linux-ügyfélen

1. Az Azure Portalon lépjen az **Általános > Eszköz adatai** menüpontra. 
2. Töltse le a Data Box Disk Linux-ügyfélhez tartozó eszközkészletét.  

    > [!div class="nextstepaction"]
    > [Data Box Disk eszközkészlet letöltése Linuxra](https://aka.ms/databoxdisktoolslinux) 

3. Nyisson meg egy terminált a Linux-ügyfélen. Lépjen abba a mappába, amelybe letöltötte a szoftvert. Módosítsa a fájlengedélyeket, hogy futtathassa a fájlokat. Írja be a következő parancsot: 

    `chmod +x DataBoxDiskUnlock_x86_64` 
    
    `chmod +x DataBoxDiskUnlock_Prep.sh` 
 
    Az alábbiakban egy példa látható a kimenetre. Miután a chmod parancs futott, az `ls` parancs futtatásával ellenőrizheti, hogy a fájl engedélyei módosultak-e. 
 
    ```
        [user@localhost Downloads]$ chmod +x DataBoxDiskUnlock_x86_64  
        [user@localhost Downloads]$ chmod +x DataBoxDiskUnlock_Prep.sh   
        [user@localhost Downloads]$ ls -l  
        -rwxrwxr-x. 1 user user 1152664 Aug 10 17:26 DataBoxDiskUnlock_x86_64  
        -rwxrwxr-x. 1 user user 795 Aug 5 23:26 DataBoxDiskUnlock_Prep.sh
    ```
4. Futtassa a szkriptet a Data Box Disk lemezzárolás-feloldó szoftverhez szükséges bináris fájlok telepítéséhez. A parancs rootként való futtatásához használja a következőt: `sudo`. A bináris fájlok sikeres telepítéséről egy üzenet értesíti a terminálon.

    `sudo ./DataBoxDiskUnlock_Prep.sh`

    A szkript először ellenőrzi, hogy az ügyfélszámítógép támogatott operációs rendszert futtat-e. Az alábbiakban egy példa látható a kimenetre. 
 
    ```
    [user@localhost Documents]$ sudo ./DataBoxDiskUnlock_Prep.sh 
        OS = CentOS Version = 6.9 
        Release = CentOS release 6.9 (Final) 
        Architecture = x64 
    
        The script will install the following packages and dependencies. 
        epel-release 
        dislocker 
        ntfs-3g 
        fuse-dislocker 
        Do you wish to continue? y|n :|
    ```
    
 
5. A telepítés folytatásához írja be a következőt: `y`. A szkript az alábbi csomagokat telepíti: 
   - **epel-release** – Adattár, amely a következő három csomagot tartalmazza. 
   - a **zárolás és a biztosíték-kioldó** – ezek a segédprogramok segítenek a BitLocker által titkosított lemezek visszafejtésében. 
   - **ntfs-3g** – Ez a csomag segít csatlakoztatni az NTFS-köteket. 
 
     A csomagok sikeres telepítéséről a terminálon megjelenő értesítés tájékoztatja.     
     ```
     Dependency Installed: compat-readline5.x86 64 0:5.2-17.I.el6 dislocker-libs.x86 64 0:0.7.1-8.el6 mbedtls.x86 64 0:2.7.4-l.el6        ruby.x86 64 0:1.8.7.374-5.el6 
     ruby-libs.x86 64 0:1.8.7.374-5.el6 
     Complete! 
     Loaded plugins: fastestmirror, refresh-packagekit, security 
     Setting up Remove Process 
     Resolving Dependencies 
     --> Running transaction check 
     ---> Package epel-release.noarch 0:6-8 will be erased --> Finished Dependency Resolution 
     Dependencies Resolved 
     Package        Architecture        Version        Repository        Size 
     Removing:  epel-release        noarch         6-8        @extras        22 k 
     Transaction Summary                                 
     Remove        1 Package(s) 
     Installed size: 22 k 
     Downloading Packages: 
     Running rpmcheckdebug 
     Running Transaction Test 
     Transaction Test Succeeded 
     Running Transaction 
     Erasing : epel-release-6-8.noarch 
     Verifying : epel-release-6-8.noarch 
     Removed: 
     epel-release.noarch 0:6-8 
     Complete! 
     Dislocker is installed by the script. 
     OpenSSL is already installed.
     ```

6. Futtassa a Data Box Disk lemezzárolás-feloldó eszközt. Adja meg a hozzáférési kulcsot az Azure Portalról, amelyet a [Csatlakozás lemezekhez és a hozzáférési kulcs lekérése](#connect-to-disks-and-get-the-passkey) részben szerzett meg. Ha kívánja, megadhatja azon BitLocker által titkosított kötetek listáját, amelyek zárolását fel szeretné oldani. A hozzáférési kulcsot és a kötetlistát egyszeres idézőjelek között kell megadni. 

    Írja be a következő parancsot.
 
    `sudo ./DataBoxDiskUnlock_x86_64 /PassKey:’<Your passkey from Azure portal>’          

    Az alábbiakban látható a mintakimenet. 
 
    ```
    [user@localhost Downloads]$ sudo ./DataBoxDiskUnlock_x86_64 /Passkey:’qwerqwerqwer’  
    
    START: Mon Aug 13 14:25:49 2018 
    Volumes: /dev/sdbl 
    Passkey: qwerqwerqwer 
    
    Volumes for data copy : 
    /dev/sdbl: /mnt/DataBoxDisk/mountVoll/ 
    END: Mon Aug 13 14:26:02 2018
    ```
    Megjelenik azon kötet csatlakozási pontja, amelyre az adatok másolhatók.

7. Ismételje meg a zárolás feloldásának lépéseit minden alkalommal, amikor új lemezt kell behelyezni. Amennyiben segítségre van szüksége a Data Box Disk lemezzárolás-feloldó eszköz használatához, használja a `help` parancsot. 
    
    `sudo ./DataBoxDiskUnlock_x86_64 /Help` 

    Az alábbiakban látható a mintakimenet. 
 
    ```
    [user@localhost Downloads]$ sudo ./DataBoxDiskUnlock_x86_64 /Help  
    START: Mon Aug 13 14:29:20 2018 
    USAGE: 
    sudo DataBoxDiskUnlock /PassKey:’<passkey from Azure_portal>’ 
    
    Example: sudo DataBoxDiskUnlock /PassKey:’passkey’ 
    Example: sudo DataBoxDiskUnlock /PassKey:’passkey’ /Volumes:’/dev/sdbl’ 
    Example: sudo DataBoxDiskUnlock /Help Example: sudo DataBoxDiskUnlock /Clean 
    
    /PassKey: This option takes a passkey as input and unlocks all of your disks. 
    Get the passkey from your Data Box Disk order in Azure portal. 
    /Volumes: This option is used to input a list of BitLocker encrypted volumes. 
    /Help: This option provides help on the tool usage and examples. 
    /Unmount: This option unmounts all the volumes mounted by this tool. 
   
    END: Mon Aug 13 14:29:20 2018 [user@localhost Downloads]$
    ```
    
8. A lemez zárolásának feloldása után megtekintheti annak tartalmát a csatlakozási ponton. Most már készen áll az adatok másolására a *BlockBlob* vagy a *PageBlob* mappába. 

    ![A Data Box Disk tartalma](media/data-box-disk-deploy-set-up/data-box-disk-content-linux.png)


Ha a lemezek feloldása során problémákba ütközik, tekintse meg a feloldási [problémák elhárítása](data-box-disk-troubleshoot-unlock.md)című témakört. 

::: zone-end

::: zone target="chromeless"

1. Csomagolja ki a lemezeket, és a mellékelt kábel használatával csatlakoztassa a lemezt az ügyfélszámítógéphez.
2. Töltse le és csomagolja ki a Data Box Disk eszközkészletet ugyanarra a számítógépre, amelyet az adatok másolásához használni fog.

    > [!div class="nextstepaction"]
    > [Data Box Disk eszközkészlet letöltése Windowsra](https://aka.ms/databoxdisktoolswin)

    vagy
    > [!div class="nextstepaction"]
    > [Data Box Disk eszközkészlet letöltése Linuxra](https://aka.ms/databoxdisktoolslinux) 

3. A Windows-ügyfélen lévő lemezek zárolásának feloldásához nyisson meg egy parancssorablakot, vagy futtassa a Windows PowerShellt rendszergazdaként ugyanazon a számítógépen:

    - Írja be a következő parancsot ugyanabban a mappában, ahol a Data Box Disk-feloldási eszköz telepítve van.

        ``` 
        .\DataBoxDiskUnlock.exe
        ```
    -  Adja meg a Azure Portal **általános > eszközének adataiból** beszerzett hozzáférési kulcsot. Megjelenik a meghajtóhoz rendelt betűjel. 
4. A Linux-ügyfélen lévő lemezek zárolásának feloldásához nyisson meg egy terminált. Lépjen arra a mappára, ahová letöltötte a szoftvert. Írja be a következő parancsokat a fájlok engedélyeinek módosításához, hogy végre tudja hajtani ezeket a fájlokat: 

    ```
    chmod +x DataBoxDiskUnlock_x86_64
    chmod +x DataBoxDiskUnlock_Prep.sh
    ``` 
    Futtassa a parancsfájlt az összes szükséges bináris fájl telepítéséhez.

    ```
    sudo ./DataBoxDiskUnlock_Prep.sh
    ```
    Futtassa a Data Box Disk lemezzárolás-feloldó eszközt. Adja meg a hitelesítő adatokat a Azure Portal az **általános > eszköz részleteivel**. Nem kötelezően megadhatja a BitLocker által titkosított kötetek listáját egy idézőjelek között a zárolás feloldásához.

    ```
    sudo ./DataBoxDiskUnlock_x86_64 /PassKey:’<Your passkey from Azure portal>’
    ```      
5. Ismételje meg a zárolás feloldásának lépéseit minden alkalommal, amikor új lemezt kell behelyezni. Amennyiben segítségre van szüksége a Data Box Disk zárolásának feloldására szolgáló eszköz használatához, használja a súgó parancsot.

A lemez zárolásának feloldása után megtekintheti a lemez tartalmát.

A lemezek beállításával és feloldásával kapcsolatos további információkért nyissa [meg az oktatóanyagot: Azure Data Box Disk](data-box-disk-deploy-set-up.md)kicsomagolása, összekötése és feloldása.

::: zone-end

::: zone target="docs"

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box Diskkel kapcsolatos alábbi témakörökkel ismerkedett meg:

> [!div class="checklist"]
> * A Data Box Disk kicsomagolása.
> * Csatlakozás lemezekhez és a hozzáférési kulcs lekérése
> * Lemezek zárolásának feloldása Windows-ügyfélen
> * Lemezek zárolásának feloldása Linux-ügyfélen


A következő oktatóanyag azt mutatja be, hogyan másolhat adatokat a Data Box Diskre.

> [!div class="nextstepaction"]
> [Adatok másolása a Data Box Diskre](./data-box-disk-deploy-copy-data.md)

::: zone-end


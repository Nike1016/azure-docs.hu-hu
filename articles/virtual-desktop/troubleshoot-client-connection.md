---
title: Ügyfélkapcsolatok Távoli asztal a Windows rendszerű virtuális asztalon – Azure
description: Az ügyfélkapcsolatok Windows virtuális asztali bérlői környezetben való beállításakor felmerülő problémák megoldása.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
ms.openlocfilehash: 9cd754b1810595c3ae82a7e4edfd9a3abe145b3f
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816411"
---
# <a name="remote-desktop-client-connections"></a>Távoli asztali ügyfélkapcsolatok

Ez a cikk a Windows rendszerű virtuális asztali ügyfélkapcsolatokkal kapcsolatos problémák megoldásához használható.

## <a name="provide-feedback"></a>Visszajelzés küldése

Jelenleg nem veszünk fel támogatási eseteket, amíg a Windows rendszerű virtuális asztal előzetes verzióban érhető el. Látogasson el a [Windows rendszerű virtuális asztali technikai Közösségbe](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) , és beszéljen a Windows Virtual Desktop szolgáltatásról a termék csapatával és az aktív közösség tagjaival.

## <a name="you-cant-open-a-web-client"></a>Webes ügyfél nem nyitható meg

Erősítse meg az internetkapcsolatot egy másik webhely megnyitásával; például: [www.Bing.com](https://www.bing.com).

Az **nslookup** használatával ellenőrizze, hogy a DNS képes-e a teljes tartománynév feloldására:

```cmd
nslookup rdweb.wvd.microsoft.com
```

Próbáljon meg csatlakozni egy másik ügyfélhez, például Távoli asztal Windows 7 vagy Windows 10 rendszerű ügyfélhez, és ellenőrizze, hogy meg tudja-e nyitni a webes ügyfelet.

### <a name="error-opening-another-site-fails"></a>Hiba: Egy másik hely megnyitása sikertelen

**Okozhat** Hálózati problémák és/vagy kimaradások.

**Javítsa ki** Forduljon a hálózat támogatási szolgálatához.

### <a name="error-nslookup-cannot-resolve-the-name"></a>Hiba: Az nslookup nem tudja feloldani a nevet

**Okozhat** Hálózati problémák és/vagy kimaradások.

**Javítsa ki** Kapcsolatfelvétel a hálózati ügyfélszolgálattal

### <a name="error-you-cant-connect-but-other-clients-can-connect"></a>Hiba: Nem lehet kapcsolatot létesíteni, de más ügyfelek is csatlakozhatnak

**Okozhat** A böngésző nem a várt módon működik, és leállt.

**Javítsa ki** Kövesse ezeket az utasításokat a böngésző hibakereséséhez.

1. Indítsa újra a böngészőt.
2. Böngészőbeli cookie-k törlése. Lásd: [cookie-fájlok törlése az Internet Explorerben](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
3. Törölje a böngésző-gyorsítótár tartalmát. Lásd: [böngésző gyorsítótárának törlése](https://binged.it/2RKyfdU)a böngészőben.
4. Nyissa meg a böngészőt privát módban.

## <a name="web-client-stops-responding-or-disconnects"></a>A webes ügyfél nem válaszol vagy bontja a kapcsolatot

Próbáljon meg csatlakozni egy másik böngésző vagy ügyfél használatával.

### <a name="error-other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Hiba: Más böngészők és ügyfelek is meghibásodnak vagy nem nyithatók meg

**Okozhat** Hálózati és/vagy operációs rendszerrel kapcsolatos problémák vagy kimaradások

**Javítsa ki** Forduljon a támogatási csapathoz.

## <a name="web-client-keeps-prompting-for-credentials"></a>A webes ügyfél megkéri a hitelesítő adatok megadását

Ha a webes ügyfél a hitelesítő adatok megadását kéri, kövesse az alábbi utasításokat.

1. Ellenőrizze, hogy helyes-e a webes ügyfél URL-címe.
2. Győződjön meg arról, hogy a hitelesítő adatok az URL-címhez kötött Windowsos virtuális asztali környezethez tartoznak.
3. Böngészőbeli cookie-k törlése. Lásd: [cookie-fájlok törlése az Internet Explorerben](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
4. Törölje a böngésző-gyorsítótár tartalmát. Lásd: [böngésző gyorsítótárának törlése](https://binged.it/2RKyfdU)a böngészőben.
5. Nyissa meg a böngészőt privát módban.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>A Windows 7 vagy Windows 10 rendszerhez készült Távoli asztal-ügyfél nem válaszol, vagy nem nyitható meg

A következő PowerShell-parancsmagokkal törölheti a sávon kívüli (OOB) ügyfelek beállításjegyzékét.

```PowerShell
Remove-ItemProperty 'HKCU:\Software\Microsoft\Terminal Server Client\Default' - Name FeedURLs

#Remove RdClientRadc registry key
Remove-Item 'HKCU:\Software\Microsoft\RdClientRadc' -Recurse

#Remove all files under %appdata%\RdClientRadc
Remove-Item C:\Users\pavithir\AppData\Roaming\RdClientRadc\* -Recurse
```

Navigáljon a **%AppData%\RdClientRadc** , és törölje az összes tartalmat.

Távolítsa el és telepítse újra Távoli asztal ügyfelet a Windows 7 és a Windows 10 rendszerhez.

## <a name="troubleshooting-end-user-connectivity"></a>Végfelhasználói kapcsolat hibaelhárítása

Bizonyos esetekben a felhasználók hozzáférhetnek a hírcsatornához és a helyi erőforrásokhoz, de továbbra is rendelkeznek olyan konfigurációval, rendelkezésre állással vagy teljesítménnyel kapcsolatos problémákkal, amelyek megakadályozzák a távoli erőforrások elérését. Ezekben az esetekben a felhasználó a következőhöz hasonló üzeneteket kap:

![Távoli asztali kapcsolat hibaüzenet.](media/eb76b666808bddb611448dfb621152ce.png)

![Nem lehet csatlakozni az átjáróhoz tartozó hibaüzenethez.](media/a8fbb9910d4672147335550affe58481.png)

Kövesse az alábbi általános hibaelhárítási útmutatót az ügyfélkapcsolati hibák kódjaival kapcsolatban.

1. Erősítse meg a Felhasználónév és az idő, amikor a probléma megtörtént.
2. Nyissa meg a **PowerShellt** , és hozzon létre kapcsolatot a Windows rendszerű virtuális asztali Bérlővel, ahol a problémát jelentették.
3. Győződjön meg arról, hogy a megfelelő bérlőhöz csatlakozik a **Get-RdsTenant.**
4. A **Get-RdsHostPool** és a **Get-RdsSessionHost** parancsmagok használatával ellenőrizze, hogy a hibaelhárítás a megfelelő gazdagépen történik-e.
5. Hajtsa végre az alábbi parancsot az összes sikertelen kapcsolódási tevékenység listájának lekéréséhez a megadott időszakra vonatkozóan:

    ```PowerShell
     Get-RdsDiagnosticActivities -TenantName <TenantName> -username <UPN> -StartTime
     "11/21/2018 1:07:03 PM" -EndTime "11/21/2018 1:27:03 PM" -Outcome Failure -ActivityType Connection
    ```

6. Az előző parancsmag kimenetének **tevékenységazonosító** használatával futtassa az alábbi parancsot:

    ```PowerShell
    (Get-RdsDiagnosticActivities -TenantName $tenant -ActivityId <ActivityId> -Detailed).Errors
    ```

7. A parancs az alább látható kimenethez hasonló kimenetet hoz létre. A **ErrorCodeSymbolic** és a **errorMessage** használatával hárítsa el a kiváltó okot.

    ```PowerShell
    ErrorSource       : <Source>
    ErrorOperation    : <Operation>
    ErrorCode         : <Error code>
    ErrorCodeSymbolic : <Error code string>
    ErrorMessage      : <Error code message>
    ErrorInternal     : <Internal for the OS>
    ReportedBy        : <Reported by component>
    Time              : <Timestampt>
    ```

### <a name="error-o_add_user_to_group_failed--failed-to-add-user--username-to-group--remote-desktop-users-reason-win32error_no_such_member"></a>Hiba: O_ADD_USER_TO_GROUP_FAILED/nem sikerült hozzáadni a felhasználót = ≤ Felhasználónév ≥ a Group = Távoli asztal felhasználókhoz. Indoklás: Win32.ERROR_NO_SUCH_MEMBER

**Okozhat** A virtuális gép nincs csatlakoztatva ahhoz a tartományhoz, ahol a felhasználói objektum található.

**Javítsa ki** Adja hozzá a virtuális gépet a megfelelő tartományhoz. Lásd: [Windows Server rendszerű virtuális gép csatlakoztatása felügyelt tartományhoz](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal).

### <a name="error-nslookup-cannot-resolve-the-name"></a>Hiba: Az nslookup nem tudja feloldani a nevet

**Okozhat** Hálózati problémák vagy kimaradások.

**Javítsa ki** Kapcsolatfelvétel a hálózati ügyfélszolgálattal

### <a name="error-connectionfailedclientprotocolerror"></a>Hiba: ConnectionFailedClientProtocolError

**Okozhat** Azok a virtuális gépek, amelyekhez a felhasználó megpróbál csatlakozni, nincs tartományhoz csatlakoztatva.

**Javítsa ki** Csatlakoztassa a gazdagép részét képező összes virtuális gépet a tartományvezérlőhöz.

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>A felhasználó csatlakozik, de semmi nem jelenik meg (nincs hírcsatorna)

A felhasználók elindíthatják Távoli asztal-ügyfeleket, és képesek hitelesíteni magukat, azonban a felhasználó nem lát ikonokat a webes felderítési hírcsatornában.

Győződjön meg arról, hogy a problémát jelentő felhasználó a következő parancssor használatával van hozzárendelve az alkalmazási csoportokhoz:

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Győződjön meg arról, hogy a felhasználó a megfelelő hitelesítő adatokkal jelentkezik be.

Ha a webes ügyfél használatban van, ellenőrizze, hogy nincsenek-e gyorsítótárazott hitelesítő adatok.

## <a name="next-steps"></a>További lépések

- A Windows rendszerű virtuális asztalok és a eszkalációs sávok hibaelhárításával kapcsolatban lásd: [Hibaelhárítás – áttekintés, visszajelzés és támogatás](troubleshoot-set-up-overview.md).
- A bérlők és a gazdagépek Windows rendszerű virtuális asztali környezetben való létrehozásakor felmerülő problémák elhárításához tekintse meg a [bérlői és az alkalmazáskészletek létrehozását](troubleshoot-set-up-issues.md)ismertető részt.
- A virtuális gép (VM) Windows rendszerű virtuális asztali gépen való konfigurálása során felmerülő problémák elhárításával kapcsolatban lásd: a [munkamenet-gazdagép virtuális gép konfigurálása](troubleshoot-vm-configuration.md).
- A PowerShell és a Windows virtuális asztal használatával kapcsolatos problémák elhárításához tekintse meg a [Windows rendszerű virtuális asztali PowerShell](troubleshoot-powershell.md)című témakört.
- Az előzetes verziójú szolgáltatással kapcsolatos további tudnivalókért tekintse meg a [Windows asztali előnézet környezetét](https://docs.microsoft.com/azure/virtual-desktop/environment-setup?)ismertető témakört.
- A következő témakörben talál útmutatást [a hibakereséshez: oktatóanyag: Resource Manager-sablonok központi telepítésének](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)hibája.
- További információ a naplózási műveletekről: [műveletek naplózása a Resource Managerrel](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Az üzembe helyezés során felmerülő hibák meghatározásával kapcsolatos további tudnivalókért lásd: [telepítési műveletek megtekintése](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).

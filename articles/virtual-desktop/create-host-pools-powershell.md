---
title: Windows rendszerű virtuális asztali előnézet-címkészlet létrehozása a PowerShell-Azure használatával
description: Állomáslista létrehozása a Windows rendszerű virtuális asztali előnézet PowerShell-parancsmagokkal.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: helohr
ms.openlocfilehash: 1c365790e1633a74be9f5baf41098e7511f99a7d
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563287"
---
# <a name="create-a-host-pool-with-powershell"></a>Gazdagépcsoport létrehozása a PowerShell-lel

A gazdagép-készletek egy vagy több azonos virtuális gép gyűjteményei a Windows rendszerű virtuális asztali előzetes verziójú bérlői környezetekben. Mindegyik gazdagép tartalmazhatja azt az alkalmazáscsoport-csoportot, amelyet a felhasználók a fizikai asztalon lévők használatával kezelhetnek.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>A PowerShell-ügyfél használata a gazdagépek létrehozásához

Először [töltse le és importálja a](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) PowerShell-munkamenetben használni kívánt Windows virtuális asztali PowerShell-modult, ha még nem tette meg.

Futtassa a következő parancsmagot a Windows rendszerű virtuális asztali környezetbe való bejelentkezéshez

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
```

Ezután futtassa ezt a parancsmagot egy új címkészlet létrehozásához a Windows rendszerű virtuális asztali bérlőben:

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

A következő parancsmag futtatásával hozzon létre egy regisztrációs jogkivonatot, amely engedélyezi, hogy a munkamenet-állomás csatlakozzon a gazdagéphez, és mentse a helyi számítógép egy új fájljába. Megadhatja, hogy a regisztrációs jogkivonat mennyi ideig érvényes a-ExpirationHours paraméter használatával.

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token > <PathToRegFile>
```

Ezután futtassa ezt a parancsmagot, hogy Azure Active Directory felhasználókat adjon hozzá az alapértelmezett asztali alkalmazás csoporthoz a gazdagéphez.

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

Az **Add-RdsAppGroupUser** parancsmag nem támogatja a biztonsági csoportok hozzáadását, és egyszerre csak egy felhasználót ad hozzá az alkalmazás csoporthoz. Ha több felhasználót szeretne hozzáadni az alkalmazás csoportjához, futtassa újra a parancsmagot a megfelelő egyszerű felhasználónevek használatával.

Futtassa a következő parancsmagot a regisztrációs jogkivonat egy változóba való exportálásához, amelyet később a [virtuális gépek a Windows rendszerű virtuális asztali készletbe való regisztrálásához](#register-the-virtual-machines-to-the-windows-virtual-desktop-preview-host-pool)fog használni.

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Virtuális gépek létrehozása a gazdagéphez

Most létrehozhat egy Azure-beli virtuális gépet, amely csatlakoztatható a Windows rendszerű virtuális asztali készlethez.

Több módon is létrehozhat egy virtuális gépet:

- [Virtuális gép létrehozása Azure Gallery-rendszerképből](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [Virtuális gép létrehozása felügyelt rendszerképből](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [Virtuális gép létrehozása nem felügyelt rendszerképből](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

A munkamenet-gazdagép virtuális gépei létrehozása után Windows- [licencet alkalmazhat egy munkamenet-gazda](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) virtuális gépre, hogy a Windows vagy a Windows Server rendszerű virtuális gépeket egy másik licenc kifizetése nélkül futtassa. 

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-preview-agent-installations"></a>A virtuális gépek előkészítése a Windows Virtual Desktop előzetes verziójú ügynökének telepítéséhez

A virtuális gépek előkészítéséhez a következő műveleteket kell elvégeznie, mielőtt telepítené a Windows rendszerű virtuális asztali ügynököket, és regisztrálja a virtuális gépeket a Windows rendszerű virtuális asztali alkalmazáskészletbe:

- A számítógépnek tartományhoz kell csatlakoznia. Ez lehetővé teszi, hogy a bejövő Windows rendszerű virtuális asztali felhasználók a Azure Active Directory fiókjából leképezhetők legyenek Active Directory-fiókjába, és sikeresen hozzáférhessenek a virtuális géphez.
- Ha a virtuális gép Windows Server operációs rendszert futtat, akkor telepítenie kell a Távoli asztal munkamenet-gazdagép (RDSH) szerepkört. A RDSH szerepkör lehetővé teszi a Windows rendszerű virtuális asztali ügynökök megfelelő telepítését.

A tartományhoz való csatlakozás sikeres végrehajtásához tegye a következőket az egyes virtuális gépeken:

1. [Kapcsolódjon a virtuális géphez](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) a virtuális gép létrehozásakor megadott hitelesítő adatokkal.
2. A virtuális gépen indítsa el a **Vezérlőpultot** , és válassza a **rendszer**elemet.
3. Válassza ki a **számítógép nevét**, válassza a **beállítások módosítása**lehetőséget, majd válassza a **módosítás...** lehetőséget.
4. Válassza a **tartomány** lehetőséget, majd adja meg a Active Directory tartományt a virtuális hálózaton.
5. A hitelesítést olyan tartományi fiókkal végezze el, amely jogosultságokkal rendelkezik a tartományhoz csatlakozó gépekhez.

    >[!NOTE]
    > Ha Azure AD Domain Services-környezethez csatlakoztatja a virtuális gépeket, győződjön meg arról, hogy a tartományhoz való csatlakozás felhasználója a [HRE DC-rendszergazdák csoport](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-admingroup#task-3-configure-administrative-group)tagja is.

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-preview-host-pool"></a>A virtuális gépek regisztrálása a Windows rendszerű virtuális asztali előzetes verziójú gazdagép-készletben

A virtuális gépek egy Windows rendszerű virtuális asztali készletbe való regisztrálása olyan egyszerű, mint a Windows rendszerű virtuális asztali ügynökök telepítése.

A Windows rendszerű virtuális asztali ügynökök regisztrálásához tegye a következőket az egyes virtuális gépeken:

1. [Kapcsolódjon a virtuális géphez](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) a virtuális gép létrehozásakor megadott hitelesítő adatokkal.
2. Töltse le és telepítse a Windows rendszerű virtuális asztali ügynököt.
   - Töltse le a [Windows rendszerű virtuális asztali ügynököt](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Kattintson a jobb gombbal a letöltött telepítőre, válassza a **Tulajdonságok**, majd a **Tiltás feloldása**elemet, majd kattintson **az OK gombra**. Ez lehetővé teszi a rendszer számára, hogy megbízzon a telepítőben.
   - Futtassa a telepítőt. Ha a telepítő megkérdezi a regisztrációs jogkivonatot, adja meg az **export-RdsRegistrationInfo** parancsmagból kapott értéket.
3. Töltse le és telepítse a Windows rendszerű virtuális asztali ügynök rendszerbetöltőjét.
   - Töltse le a [Windows rendszerű virtuális asztali ügynök](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH)rendszerbetöltőjét.
   - Kattintson a jobb gombbal a letöltött telepítőre, válassza a **Tulajdonságok**, majd a **Tiltás feloldása**elemet, majd kattintson **az OK gombra**. Ez lehetővé teszi a rendszer számára, hogy megbízzon a telepítőben.
   - Futtassa a telepítőt.

>[!IMPORTANT]
>A Windows rendszerű virtuális asztali környezet biztonságossá tételéhez az Azure-ban javasoljuk, hogy ne nyissa meg a 3389-es bejövő portot a virtuális gépeken. A Windows rendszerű virtuális asztal nem igényel olyan nyitott bejövő portot 3389, amellyel a felhasználók hozzáférhetnek a gazdagép-készlet virtuális gépei számára. Ha hibaelhárítási célból meg kell nyitnia a 3389-as portot, javasoljuk, hogy használja a [virtuális](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)gépek igény szerinti elérését.

## <a name="next-steps"></a>További lépések

Most, hogy létrehozott egy gazdagépet, feltöltheti azt a RemoteApps szolgáltatással. Ha többet szeretne megtudni az alkalmazások kezeléséről a Windows Virtual Desktopban, tekintse meg az alkalmazáscsoport kezelése oktatóanyagot.

> [!div class="nextstepaction"]
> [Alkalmazás-csoportok kezelése – oktatóanyag](./manage-app-groups.md)

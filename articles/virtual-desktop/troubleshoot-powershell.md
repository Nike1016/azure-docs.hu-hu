---
title: Windows rendszerű virtuális asztali PowerShell – Azure
description: A PowerShell hibáinak elhárítása a Windows rendszerű virtuális asztali bérlői környezet beállításakor.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
ms.openlocfilehash: 7c5a15790c3a88e9aa3921d6cc2626576aedaa64
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816375"
---
# <a name="windows-virtual-desktop-powershell"></a>Windows Virtual Desktop – PowerShell

Ez a cikk a PowerShell és a Windows rendszerű virtuális asztal használatával kapcsolatos hibák és problémák megoldására használható. Távoli asztali szolgáltatások PowerShell-lel kapcsolatos további információkért lásd: [Windows virtuális asztali PowerShell](https://docs.microsoft.com/powershell/module/windowsvirtualdesktop/).

## <a name="provide-feedback"></a>Visszajelzés küldése

Jelenleg nem veszünk fel támogatási eseteket, amíg a Windows rendszerű virtuális asztal előzetes verzióban érhető el. Látogasson el a [Windows rendszerű virtuális asztali technikai Közösségbe](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) , és beszéljen a Windows Virtual Desktop szolgáltatásról a termék csapatával és az aktív közösség tagjaival.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>A Windows rendszerű virtuális asztali telepítés során használt PowerShell-parancsok

Ez a szakasz azokat a PowerShell-parancsokat sorolja fel, amelyek a Windows rendszerű virtuális asztali környezet beállításakor használatosak, és módot biztosít a használat közben felmerülő problémák megoldására.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>Hiba: Add-RdsAppGroupUser parancs – a megadott UserPrincipalName már hozzá van rendelve egy RemoteApp-alkalmazáshoz a megadott gazdagép-készletben

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**Okozhat** A használt Felhasználónév már hozzá van rendelve egy másik típusú alkalmazás csoportjához. A felhasználók nem rendelhetők hozzá egyszerre egy távoli asztali és távoli alkalmazáscsoport ugyanahhoz a munkamenet-készlethez.

**Javítsa ki** Ha a felhasználónak mind a távoli, mind a távoli asztal szolgáltatásra van szüksége, hozzon létre különböző gazdagép-készleteket, vagy engedélyezzen felhasználói hozzáférést a távoli asztalhoz, amely lehetővé teszi bármely alkalmazás használatát a munkamenet-gazda virtuális gépen.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>Hiba: Add-RdsAppGroupUser parancs – a megadott UserPrincipalName nem létezik a Távoli asztal bérlőhöz társított Azure Active Directory

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName “Desktop Application Group” -UserPrincipalName <UserPrincipalName>
```

**Okozhat** A-UserPrincipalName által megadott felhasználó nem található a Windows rendszerű virtuális asztali bérlőhöz kötött Azure Active Directoryban.

**Javítsa ki** Erősítse meg az alábbi listán szereplő elemeket.

- A felhasználó Azure Active Directoryra van szinkronizálva.
- A felhasználó nincs a vállalati (B2C) vagy a vállalatközi (B2B) kereskedelemhez kötve.
- A Windows rendszerű virtuális asztali bérlő helyes Azure Active Directoryhoz van kötve.

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>Hiba: Get-RdsDiagnosticActivities – a felhasználó nem rendelkezik jogosultsággal a kezelési szolgáltatás lekérdezéséhez

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**OK:** -TenantName paraméter

**Javítsa ki** Probléma a Get-RdsDiagnosticActivities és a \<-TenantName TenantName >ával.

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>Hiba: Get-RdsDiagnosticActivities – a felhasználó nem rendelkezik jogosultsággal a kezelési szolgáltatás lekérdezéséhez

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**Okozhat** A-Deployment kapcsoló használata.

**Javítás:** – a központi telepítési kapcsolót csak a telepítési rendszergazdák használhatják. Ezek a rendszergazdák általában a Távoli asztali szolgáltatások/Windows virtuális asztali csapat tagjai. Cserélje le a-Deployment kapcsolót a \<-TenantName TenantName >.

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>Hiba: New-RdsRoleAssignment – a felhasználó nem rendelkezik jogosultsággal a kezelési szolgáltatás lekérdezéséhez

**1. ok:** A használt fióknak nincs Távoli asztali szolgáltatások tulajdonosi engedélye a bérlőn.

**1. javítás:** Távoli asztali szolgáltatások tulajdonosi engedélyekkel rendelkező felhasználónak végre kell hajtania a szerepkör-hozzárendelést.

**2. ok:** A használt fiók Távoli asztali szolgáltatások tulajdonosi engedélyekkel rendelkezik, de nem része a bérlő Azure Active Directorynak, vagy nem rendelkezik engedéllyel a felhasználó helye Azure Active Directory lekérdezéséhez.

**2. javítás:** Active Directory engedélyekkel rendelkező felhasználónak végre kell hajtania a szerepkör-hozzárendelést.

>[!Note]
>A New-RdsRoleAssignment nem tud engedélyeket adni olyan felhasználónak, aki nem létezik a Azure Active Directoryban (AD).

## <a name="next-steps"></a>További lépések

- A Windows rendszerű virtuális asztalok és a eszkalációs sávok hibaelhárításával kapcsolatban lásd: [Hibaelhárítás – áttekintés, visszajelzés és támogatás](troubleshoot-set-up-overview.md).
- A bérlők és a gazdagépek Windows rendszerű virtuális asztali környezetben való létrehozásakor felmerülő problémák elhárításához tekintse meg a [bérlői és az alkalmazáskészletek létrehozását](troubleshoot-set-up-issues.md)ismertető részt.
- A virtuális gép (VM) Windows rendszerű virtuális asztali gépen való konfigurálása során felmerülő problémák elhárításával kapcsolatban lásd: a [munkamenet-gazdagép virtuális gép konfigurálása](troubleshoot-vm-configuration.md).
- A Windows rendszerű virtuális asztali ügyfélkapcsolatokkal kapcsolatos problémák elhárításához lásd: [Távoli asztal ügyfélkapcsolatok](troubleshoot-client-connection.md).
- Az előzetes verziójú szolgáltatással kapcsolatos további tudnivalókért tekintse meg a [Windows asztali előnézet környezetét](https://docs.microsoft.com/azure/virtual-desktop/environment-setup)ismertető témakört.
- A következő témakörben talál útmutatást [a hibakereséshez: oktatóanyag: Resource Manager-sablonok központi telepítésének](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)hibája.
- További információ a naplózási műveletekről: [műveletek naplózása a Resource Managerrel](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Az üzembe helyezés során felmerülő hibák meghatározásával kapcsolatos további tudnivalókért lásd: [telepítési műveletek megtekintése](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).

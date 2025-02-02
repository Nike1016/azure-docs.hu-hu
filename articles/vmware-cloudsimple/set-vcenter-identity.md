---
title: Azure VMware-megoldás CloudSimple – vCenter-identitások beállítása a privát felhőben
description: Ismerteti, hogyan állítható be a saját Felhőbeli vCenter a vCenter való Active Directory hitelesítéshez a VMware-rendszergazdák számára a eléréséhez
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d6229e78958fdc4995153d99ad02a15804330f1b
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69544564"
---
# <a name="set-up-vcenter-identity-sources-to-use-active-directory"></a>VCenter-identitások beállítása a Active Directory használatára

## <a name="about-vmware-vcenter-identity-sources"></a>A VMware vCenter Identity sources

A VMware vCenter a vCenter-hez hozzáférő felhasználók hitelesítéséhez különböző identitás-forrásokat támogat.  A CloudSimple saját Felhőbeli vCenter úgy is beállítható, Active Directory hogy a VMware-rendszergazdák hozzáférhessenek a vCenter. Ha a telepítés befejeződött, a **cloudowner** felhasználó hozzáadhat felhasználókat az Identity forrásból a vCenter.  

A Active Directory tartománya és tartományvezérlői a következő módokon állíthatók be:

* A helyszínen futó tartomány-és tartományvezérlők Active Directory
* Az Azure-ban virtuális gépekként működő tartomány-és tartományvezérlők Active Directory Azure-előfizetésében
* A saját felhőben futó új Active Directory tartomány és tartományvezérlők
* Azure Active Directory szolgáltatás

Ez az útmutató ismerteti azokat a feladatokat, amelyekkel Active Directory tartomány és tartományvezérlők állíthatók be a helyszíni vagy virtuális gépeken az előfizetésekben.  Ha az Azure AD-t identitás forrásaként szeretné használni, tekintse meg az Azure AD-t identitás-szolgáltatóként a [CloudSimple privát felhőben történő vCenter](azure-ad.md) című témakört, amely részletes útmutatást nyújt az Identity Source beállításához.

[Az Identity forrás hozzáadása](#add-an-identity-source-on-vcenter)előtt átmenetileg megnövelheti [a vCenter](escalate-private-cloud-privileges.md)-jogosultságokat.

## <a name="identity-source-options"></a>Személyazonossági forrás beállításai

* [Helyszíni Active Directory hozzáadása egyszeri bejelentkezési identitás forrásaként](#add-on-premises-active-directory-as-a-single-sign-on-identity-source)
* [Új Active Directory beállítása privát felhőben](#set-up-new-active-directory-on-a-private-cloud)
* [Active Directory beállítása az Azure-ban](#set-up-active-directory-on-azure)

## <a name="add-on-premises-active-directory-as-a-single-sign-on-identity-source"></a>Helyszíni Active Directory hozzáadása egyszeri bejelentkezési identitás forrásaként

Ha a helyszíni Active Directory egyszeri bejelentkezési identitás forrásaként szeretné beállítani, a következőkre lesz szüksége:

* [Helyek közötti VPN-kapcsolat](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) a helyszíni adatközpontból a saját felhőbe.
* A helyszíni DNS-kiszolgáló IP-címe a vCenter és a platform Services-vezérlőhöz (PSC) lett hozzáadva.

A Active Directory tartományának beállításakor használja az alábbi táblázatban szereplő információkat.

| **Beállítás** | **Leírás** |
|------------|-----------------|
| **Name** | Az Identity forrás neve. |
| **A felhasználók alapszintű megkülönböztető neve** | A felhasználók alapszintű megkülönböztető neve. |
| **Tartománynév** | A tartomány FQDN, például example.com. Ne adjon meg IP-címet ebben a szövegmezőben. |
| **Tartomány aliasa** | A tartomány NetBIOS-neve. Adja hozzá a Active Directory tartomány NetBIOS-nevét az Identity forrás aliasként, ha az SSPI-hitelesítést használja. |
| **A csoportok alapszintű megkülönböztető neve** | A csoportok alapszintű megkülönböztető neve. |
| **Elsődleges kiszolgáló URL-címe** | A tartomány elsődleges tartományvezérlője LDAP-kiszolgálója.<br><br>A formátumot `ldap://hostname:port` használja. `ldaps://hostname:port` A port általában a 389 LDAP-kapcsolatokhoz és 636 for LDAPs-kapcsolatokhoz. Active Directory többtartományos tartományvezérlő üzembe helyezése esetén a port általában az LDAP-hez és a 3269-hoz 3268.<br><br>Az elsődleges vagy másodlagos LDAP URL-cím használata `ldaps://` esetén olyan tanúsítványra van szükség, amely a Active Directory kiszolgáló LDAPS végpontjának megbízhatóságát hozza létre. |
| **Másodlagos kiszolgáló URL-címe** | A feladatátvételhez használt másodlagos tartományvezérlői LDAP-kiszolgáló címe. |
| **Tanúsítvány kiválasztása** | Ha LDAPS-t szeretne használni a Active Directory LDAP-kiszolgálóval vagy a OpenLDAP-kiszolgáló identitásával, akkor az URL-cím `ldaps://`szövegmezőbe való beírása után a tanúsítvány választása gomb jelenik meg. Másodlagos URL-cím megadása nem kötelező. |
| **Felhasználónév** | Azon felhasználó azonosítója, aki legalább olvasási hozzáféréssel rendelkezik a felhasználók és csoportok alapszintű DN-hez. |
| **Jelszó** | A Felhasználónév által megadott felhasználó jelszava. |

Ha az előző táblázatban szereplő adatokkal rendelkezik, a helyszíni Active Directory egyszeri bejelentkezési identitásként adhatja hozzá a vCenter-on.

> [!TIP]
> A <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.psc.doc/GUID-B23B1360-8838-4FF2-B074-71643C4CB040.html" target="_blank">VMware dokumentációs oldalán</a>találhat további információt az egyszeri bejelentkezési azonosítók forrásairól.

## <a name="set-up-new-active-directory-on-a-private-cloud"></a>Új Active Directory beállítása privát felhőben

Beállíthat egy új Active Directory tartományt a saját felhőben, és használhatja az egyszeri bejelentkezéshez használt identitási forrásként.  A Active Directory tartomány lehet egy meglévő Active Directory erdő része, vagy önálló erdőként is beállítható.

### <a name="new-active-directory-forest-and-domain"></a>Új Active Directory erdő és tartomány

Új Active Directory erdő és tartomány beállításához a következők szükségesek:

* Egy vagy több, a Microsoft Windows Servert futtató virtuális gép tartományvezérlőként való használatára az új Active Directory erdő és tartomány számára.
* Egy vagy több DNS-szolgáltatást futtató virtuális gép névfeloldáshoz.

A részletes lépésekért lásd: [új Windows Server 2012 Active Directory erdő telepítése](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-forest--level-200-) .

> [!TIP]
> A szolgáltatások magas rendelkezésre állása érdekében ajánlott több tartományvezérlő és DNS-kiszolgáló beállítása.

A Active Directory erdő és tartomány beállítása után az új Active Directoryhoz [hozzáadhat egy vCenter](#add-an-identity-source-on-vcenter) az azonosítóhoz.

### <a name="new-active-directory-domain-in-an-existing-active-directory-forest"></a>Új Active Directory-tartomány egy meglévő Active Directory erdőben

Új Active Directory tartomány meglévő Active Directory erdőben való beállításához a következőkre lesz szüksége:

* Helyek közötti VPN-kapcsolat a Active Directory erdő helyére.
* DNS-kiszolgáló a meglévő Active Directory erdő nevének feloldásához.

A részletes lépésekért lásd: [új Windows Server 2012 Active Directory gyermek-vagy Fatartomány telepítése](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-child-or-tree-domain--level-200-) .

A Active Directory tartomány beállítása után az új Active Directoryhoz [hozzáadhat egy vCenter az identitás forrásához](#add-an-identity-source-on-vcenter) .

## <a name="set-up-active-directory-on-azure"></a>Active Directory beállítása az Azure-ban

Az Azure-on futó Active Directory hasonló a helyileg futó Active Directoryhoz.  Ha az Azure-ban futó Active Directoryt egyszeri bejelentkezési vCenter szeretné beállítani, akkor a vCenter-kiszolgálónak és a PSC-nek hálózati kapcsolattal kell rendelkeznie az Azure Virtual Network, ahol Active Directory szolgáltatások futnak.  Ezt a kapcsolatot az [azure Virtual Network kapcsolattal](azure-expressroute-connection.md) is létrehozhatja az Azure Virtual Network ExpressRoute használatával, ahol Active Directory szolgáltatások futnak a CloudSimple.

A hálózati kapcsolatok létrejötte után kövesse a helyszíni [Active Directory hozzáadása egyszeri bejelentkezési identitásként](#add-on-premises-active-directory-as-a-single-sign-on-identity-source) című témakör lépéseit, és adja hozzá az azonosító forrásként.  

## <a name="add-an-identity-source-on-vcenter"></a>Identity forrás hozzáadása a vCenter

1. [](escalate-private-cloud-privileges.md) Adja meg a jogosultságokat a saját felhőben.

2. Jelentkezzen be a vCenter a saját felhőbe.

3. Válassza a **kezdőlap > felügyelet**lehetőséget.

    ![Felügyelet](media/OnPremAD01.png)

4. Válassza **az egyszeri bejelentkezés > konfiguráció**lehetőséget.

    ![Egyszeri bejelentkezés](media/OnPremAD02.png)

5. Nyissa meg az **Identity** sources **+** fület, és kattintson rá egy új Identity forrás hozzáadásához.

    ![Azonosító források](media/OnPremAD03.png)

6. Válassza ki a **Active Directory LDAP** -kiszolgálóként, majd kattintson a **tovább**gombra.

    ![Active Directory](media/OnPremAD04.png)

7. Adja meg a környezet Identity Source paramétereit, majd kattintson a **tovább**gombra.

    ![Active Directory](media/OnPremAD05.png)

8. Tekintse át a beállításokat, majd kattintson a **Befejezés**gombra.

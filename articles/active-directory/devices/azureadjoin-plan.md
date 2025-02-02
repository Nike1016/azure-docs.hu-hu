---
title: A Azure Active Directory (Azure AD) JOIN implementációjának megtervezése | Microsoft Docs
description: Ismerteti azokat a lépéseket, amelyek szükségesek az Azure AD-hez csatlakoztatott eszközök megvalósításához a környezetben.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: c739e827589a9fd6adeb10255f869acef29a4f16
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69562205"
---
# <a name="how-to-plan-your-azure-ad-join-implementation"></a>Útmutató: Az Azure AD-csatlakozás implementálásának megtervezése

Az Azure AD JOIN lehetővé teszi, hogy közvetlenül az Azure AD-hez csatlakozzon, anélkül, hogy a helyszíni Active Directoryhoz kellene csatlakoznia, miközben a felhasználók produktív és biztonságos módon tartanak. Az Azure AD JOIN a nagyvállalati szintű és a hatókörön belüli üzemelő példányok esetében is készen áll.   

Ez a cikk az Azure AD JOIN megvalósításának megtervezéséhez szükséges információkat tartalmazza.
 
## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy ismeri a [Azure Active Directory eszköz-felügyeletének](../device-management-introduction.md)bevezetését.

## <a name="plan-your-implementation"></a>A megvalósítás tervezése

Az Azure AD JOIN megvalósításának megtervezéséhez Ismerkedjen meg a következővel:

|   |   |
|---|---|
|![Ellenőrzés][1]|Forgatókönyvek áttekintése|
|![Ellenőrzés][1]|Az identitás-infrastruktúra áttekintése|
|![Ellenőrzés][1]|Az eszközök kezelésének felmérése|
|![Ellenőrzés][1]|Az alkalmazásokkal és erőforrásokkal kapcsolatos megfontolások ismertetése|
|![Ellenőrzés][1]|A kiépítési lehetőségek megismerése|
|![Ellenőrzés][1]|Vállalati állapot barangolásának konfigurálása|
|![Ellenőrzés][1]|Feltételes hozzáférés konfigurálása|

## <a name="review-your-scenarios"></a>Forgatókönyvek áttekintése 

Előfordulhat, hogy a hibrid Azure AD-csatlakozás bizonyos helyzetekben előnyben részesített, és lehetővé teszi, hogy az Azure AD JOIN segítségével áttérjen a Windowsos Felhőbeli első modellre. Ha azt tervezi, hogy modernizálja az eszközök felügyeletét, és csökkenti az eszközhöz kapcsolódó informatikai költségeket, az Azure AD JOIN nagyszerű alapot biztosít a célkitűzések megvalósításához.  
 
Ha a célok a következő feltételekkel vannak összehangolva, érdemes megfontolnia az Azure AD Joint:

- A felhasználók számára a termelékenységi csomag Microsoft 365t alkalmazza.
- Felhőalapú eszközkezelés megoldással szeretné felügyelni az eszközöket.
- Szeretné leegyszerűsíteni az eszközök kiosztását a földrajzilag elosztott felhasználók számára.
- Azt tervezi, hogy modernizálja az alkalmazás-infrastruktúrát.

## <a name="review-your-identity-infrastructure"></a>Az identitás-infrastruktúra áttekintése  

Az Azure AD JOIN mindkét, felügyelt és összevont környezettel működik.  

### <a name="managed-environment"></a>Felügyelt környezet

A felügyelt környezetek jelszó-kivonatos [szinkronizálással](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) vagy a zökkenőmentes egyszeri bejelentkezéses [hitelesítéssel](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) is üzembe helyezhetők.

Ezekhez a forgatókönyvekhez nem szükséges összevonási kiszolgálót konfigurálni a hitelesítéshez.

### <a name="federated-environment"></a>Összevont környezet

Egy összevont környezetnek rendelkeznie kell egy olyan identitás-szolgáltatóval, amely támogatja a WS-Trust és a WS-fed protokollokat:

- **WS-Fed:** Ez a protokoll az eszköz Azure AD-hez való csatlakoztatásához szükséges.
- **WS-Trust:** Ez a protokoll egy Azure AD-hez csatlakoztatott eszközre való bejelentkezéshez szükséges.

AD FS használatakor engedélyeznie kell a következő WS-Trust végpontokat:`/adfs/services/trust/2005/usernamemixed`
 `/adfs/services/trust/13/usernamemixed`
 `/adfs/services/trust/2005/certificatemixed`
 `/adfs/services/trust/13/certificatemixed`

Ha az identitás-szolgáltató nem támogatja ezeket a protokollokat, az Azure AD JOIN nem működik natív módon. A Windows 10 1809-től kezdve a felhasználók az SAML-alapú identitás-szolgáltatóval bejelentkezhetnek egy Azure AD-hez csatlakoztatott eszközre a [Windows 10-es webes bejelentkezés](https://docs.microsoft.com/windows/whats-new/whats-new-windows-10-version-1809#web-sign-in-to-windows-10)használatával. Jelenleg a webes bejelentkezés előzetes verziójú funkció, ezért nem ajánlott éles környezetekben üzemelő példányokhoz.

>[!NOTE]
> Az Azure AD JOIN jelenleg nem működik, [mert a külső hitelesítő szolgáltatókkal konfigurált AD FS 2019 elsődleges hitelesítési módszer](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/additional-authentication-methods-ad-fs#enable-external-authentication-methods-as-primary). Az Azure AD-hez való csatlakozás alapértelmezett értéke a jelszó-hitelesítés elsődleges módszerként, amely hitelesítési hibákat eredményez ebben a forgatókönyvben


### <a name="smartcards-and-certificate-based-authentication"></a>Intelligens kártyák és tanúsítványalapú hitelesítés

Az eszközök Azure AD-hez való csatlakoztatásához nem használhat intelligens kártyát vagy tanúsítványalapú hitelesítést. Ha azonban AD FS van konfigurálva, az intelligens kártyák használatával bejelentkezhet az Azure AD-hez csatlakoztatott eszközökre.

**Ajánlás** A Windows Hello for Business implementálása erős, jelszó nélküli hitelesítésre Windows 10-es eszközökre.

### <a name="user-configuration"></a>Felhasználói konfiguráció

Ha a következőket hozza létre a felhasználók számára:

- Helyszíni **Active Directory a** [Azure ad Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)használatával szinkronizálnia kell őket az Azure ad-vel. 
- Az **Azure ad**-ben nincs szükség további beállításra.

Az Azure AD UPN-től eltérő helyszíni UPN-ket az Azure AD-hez csatlakoztatott eszközök nem támogatják. Ha a felhasználók helyszíni UPN-t használnak, érdemes megtervezni, hogy az elsődleges UPN-t használják az Azure AD-ben.

## <a name="assess-your-device-management"></a>Az eszközök kezelésének felmérése

### <a name="supported-devices"></a>Támogatott eszközök

Azure AD-csatlakozás:

- Csak Windows 10-es eszközökre alkalmazható. 
- Nem alkalmazható a Windows korábbi verzióira vagy más operációs rendszerekre. Windows 7/8.1 rendszerű eszközök esetén az Azure AD JOIN üzembe helyezéséhez frissítenie kell a Windows 10-es verzióra.
- Nem támogatott a TPM-sel rendelkező eszközökön FIPS módban.
 
**Ajánlás** A frissített funkciók kihasználásához mindig a legújabb Windows 10-es verziót használja.

### <a name="management-platform"></a>Felügyeleti platform

Az Azure AD-hez csatlakoztatott eszközök MDM platformon, például az Intune-on és a MDM-beli kriptográfiai szolgáltatásokon alapulnak. A Windows 10 tartalmaz egy beépített MDM-ügynököt, amely minden kompatibilis MDM-megoldással működik.

> [!NOTE]
> A csoportházirendek nem támogatottak az Azure AD-hez csatlakoztatott eszközökön, mivel azok nem csatlakoznak a helyszíni Active Directoryhoz. Az Azure AD-hez csatlakoztatott eszközök kezelése csak a MDM keresztül lehetséges

Az Azure AD-hez csatlakoztatott eszközök kezelésének két módja van:

- **Csak Mdm** – az eszközöket kizárólag olyan Mdm-szolgáltatók felügyelik, mint az Intune. Az összes szabályzatot a MDM regisztrációs folyamatának részeként továbbítja a rendszer. Prémium szintű Azure AD-vagy EMS-ügyfelek esetén a MDM egy olyan automatizált lépés, amely egy Azure AD-csatlakozás részét képezi.
- **Közös felügyelet** – az eszközt egy Mdm-szolgáltató és egy SCCM kezeli. Ebben a megközelítésben a SCCM-ügynök egy MDM által felügyelt eszközre van telepítve bizonyos szempontok felügyeletéhez.

Ha csoportházirendeket használ, értékelje ki a MDM-szabályzat paritását a [Mdm Migration Analysis Tool (MMAT)](https://github.com/WindowsDeviceManagement/MMAT)használatával. 

A támogatott és nem támogatott házirendek áttekintésével állapítsa meg, hogy csoportházirendek helyett használhat-e MDM-megoldást. Nem támogatott házirendek esetén vegye figyelembe a következőket:

- Az Azure AD-hez csatlakoztatott eszközökhöz vagy felhasználókhoz nem támogatott házirendek szükségesek?
- A felhőalapú üzemelő példányokban nem támogatott szabályzatok alkalmazhatók?

Ha a MDM-megoldás nem érhető el az Azure AD-alkalmazás-katalóguson keresztül, akkor a MDM-integrációs szolgáltatásban ismertetett eljárást követve adhatja hozzá a következőhöz: [Azure Active Directory](https://docs.microsoft.com/windows/client-management/mdm/azure-active-directory-integration-with-mdm). 

A közös felügyelet révén a SCCM segítségével kezelheti az eszközök bizonyos aspektusait, miközben a szabályzatokat a MDM platformon keresztül továbbítjuk. A Microsoft Intune lehetővé teszi a SCCM való közös felügyeletet. További információ: [a Windows 10-es eszközök közös felügyelete](https://docs.microsoft.com/sccm/core/clients/manage/co-management-overview). Ha az Intune-tól eltérő MDM-terméket használ, kérjük, tekintse meg a MDM-szolgáltatót a megfelelő közös felügyeleti forgatókönyvekben.

**Ajánlás** A MDM csak az Azure AD-hez csatlakoztatott eszközök felügyeletét érdemes figyelembe venni.

## <a name="understand-considerations-for-applications-and-resources"></a>Az alkalmazásokkal és erőforrásokkal kapcsolatos megfontolások ismertetése

Javasoljuk, hogy jobb felhasználói élményt és hozzáférés-vezérlést biztosítson a helyszíni és a Felhőbeli alkalmazások áttelepítéséhez. Az Azure AD-hez csatlakoztatott eszközök azonban zökkenőmentesen biztosíthatnak hozzáférést mind a helyszíni, mind a felhőalapú alkalmazásokhoz. További információ: Hogyan működik az SSO a helyszíni erőforrásokkal az [Azure ad-hez csatlakoztatott eszközökön](azuread-join-sso.md).

Az alábbi részekben a különböző típusú alkalmazásokra és erőforrásokra vonatkozó szempontok szerepelnek.

### <a name="cloud-based-applications"></a>Felhőalapú alkalmazások

Ha egy alkalmazás hozzá van adva az Azure AD App Galleryhez, a felhasználók egyszeri bejelentkezést kapnak az Azure AD-hez csatlakoztatott eszközökön. Nincs szükség további konfigurációra. A felhasználók egyszeri bejelentkezést kapnak a Microsoft Edge és a Chrome böngészőben is. A Chrome esetében telepítenie kell a [Windows 10 fiókok bővítményt](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). 

Minden Win32-alkalmazás, amely:

- Hivatkozhat a web Account Manager (WAM) szolgáltatásra a jogkivonat-kérelmek esetében, ha az Azure AD-hez csatlakoztatott eszközökön egyszeri bejelentkezést is kap. 
- A WAM nem támaszkodik a felhasználók hitelesítésére. 

### <a name="on-premises-web-applications"></a>Helyszíni webalkalmazások

Ha az alkalmazásai egyéni és/vagy helyszíni környezetben találhatók, akkor a böngésző megbízható helyeihez hozzá kell adnia azokat a következőkhöz:

- A Windows beépített hitelesítésének engedélyezése a működéshez 
- Adjon meg egy nem gyors egyszeri bejelentkezéses felhasználói élményt a felhasználóknak. 

Ha AD FS használ, tekintse [meg az egyszeri bejelentkezés ellenőrzése és kezelése a AD FS](https://docs.microsoft.com/previous-versions/azure/azure-services/jj151809(v%3dazure.100))használatával című témakört. 

**Ajánlás** Vegye fontolóra a felhőben való üzemeltetést (például az Azure-t) és az Azure AD-val való integrációt a jobb felhasználói élmény érdekében.

### <a name="on-premises-applications-relying-on-legacy-protocols"></a>Örökölt protokollokra támaszkodó helyszíni alkalmazások

A felhasználók egyszeri bejelentkezést kapnak az Azure AD-hez csatlakoztatott eszközökről, ha az eszköz hozzáfér egy tartományvezérlőhöz. 

**Ajánlás** [Azure ad alkalmazás proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) üzembe helyezésével engedélyezheti ezeknek az alkalmazásoknak a biztonságos elérését.

### <a name="on-premises-network-shares"></a>Helyszíni hálózati megosztások

A felhasználók egyszeri bejelentkezéssel rendelkeznek az Azure AD-hez csatlakoztatott eszközökről, amikor egy eszköz hozzáfér egy helyszíni tartományvezérlőhöz.

### <a name="printers"></a>Nyomtatók

A nyomtatók esetében telepítenie kell a [hibrid Felhőbeli nyomtatást](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy) a nyomtatók Azure ad-hez csatlakoztatott eszközökön való felfedéséhez. 

Habár a nyomtatók nem észlelhetők automatikusan csak Felhőbeli környezetben, a felhasználók a nyomtatók UNC elérési útját is használhatják közvetlenül a hozzáadáshoz. 

### <a name="on-premises-applications-relying-on-machine-authentication"></a>Számítógép-hitelesítésre támaszkodó helyszíni alkalmazások

Az Azure AD-hez csatlakoztatott eszközök nem támogatják a számítógép-hitelesítésre támaszkodó helyszíni alkalmazásokat. 

**Ajánlás** Vegye fontolóra az alkalmazások kivonását és a modern alternatívákra való áttérést.

### <a name="remote-desktop-services"></a>Távoli asztali szolgáltatások

Az Azure AD-hez csatlakoztatott eszközökhöz való távoli asztali kapcsolathoz a gazdagépnek Azure AD-hez csatlakoztatott vagy hibrid Azure AD-csatlakozásra van szüksége. A távoli asztal nem csatlakoztatott vagy nem Windows rendszerű eszközről nem támogatott. További információ: [Csatlakozás távoli Azure ad-hez csatlakoztatott számítógéphez](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc)

## <a name="understand-your-provisioning-options"></a>A kiépítési lehetőségek megismerése

Az Azure AD JOIN a következő módszerekkel építhető ki:

- Önkiszolgáló **az Oobe/Settings** -ben – önkiszolgáló módban a felhasználók az Azure ad JOIN folyamaton keresztül érhetik el a Windows beépített felhasználói élmény (OOBE) vagy a Windows beállításai között. További információ: [a munkahelyi eszköz csatlakoztatása a szervezet hálózatához](https://docs.microsoft.com/azure/active-directory/user-help/user-help-join-device-on-network). 
- **Windows Autopilot** – a Windows Autopilot lehetővé teszi az eszközök előzetes konfigurálását az Oobe zökkenőmentesebb működéséhez az Azure ad-csatlakozás végrehajtásához. További információ: a [Windows Autopilot áttekintése](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot). 
- **Tömeges beléptetés** – a tömeges regisztráció lehetővé teszi, hogy a rendszergazda egy tömeges kiépítési eszközzel konfigurálja az eszközöket az Azure ad-hez. További információ: [csoportos regisztráció a Windows](https://docs.microsoft.com/intune/windows-bulk-enroll)-eszközökhöz.
 
A következő három megközelítés összehasonlítása 
 
|   | Önkiszolgáló telepítés | Windows Autopilot | Tömeges regisztrálás |
| --- | --- | --- | --- |
| Felhasználói beavatkozás szükséges a beállításhoz | Igen | Igen | Nem |
| INFORMATIKAI erőfeszítés megkövetelése | Nem | Igen | Igen |
| Alkalmazható folyamatok | OOBE & beállításai | Csak OOBE | Csak OOBE |
| Helyi rendszergazdai jogosultságok az elsődleges felhasználóhoz | Igen, alapértelmezés szerint | Konfigurálható | Nem |
| Eszköz OEM-támogatásának megkövetelése | Nem | Igen | Nem |
| Támogatott verziók | 1511+ | 1709 + | 1703 + |
 
Válassza ki az üzembe helyezési módszert vagy megközelítéseket a fenti táblázat áttekintésével, és tekintse át a következő szempontokat az egyik megközelítés elfogadásához:  

- A felhasználók a technikai hozzáértést használják a telepítésre? 
   - Az önkiszolgáló szolgáltatás a legjobban használható a felhasználók számára. Gondolja át a Windows autopilott a felhasználói élmény fokozása érdekében.  
- A felhasználók távolról vagy a vállalati helyszínen találhatók? 
   - Az önkiszolgáló vagy az Autopilot a legjobb megoldás a távoli felhasználók számára a problémamentes telepítéshez. 
- Inkább felhasználó által vezérelt vagy rendszergazda által felügyelt konfigurációt szeretne? 
   - A csoportos regisztráció hatékonyabban működik a rendszergazda által vezérelt üzembe helyezéshez az eszközök felhasználóknak való átadása előtt.     
- Vásárolja meg az 1-2-es SZÁMÍTÓGÉPGYÁRTÓKtól származó eszközöket, vagy az OEM-eszközök széles körben terjeszthető?  
   - Ha olyan korlátozott számítógépgyártóktól vásárol, akik az Autopilot-t is támogatják, az Autopilot szorosabb integrációját veheti igénybe. 

## <a name="configure-your-device-settings"></a>Az eszközbeállítások konfigurálása

A Azure Portal segítségével szabályozható az Azure AD-hez csatlakoztatott eszközök üzembe helyezése a szervezetben. A kapcsolódó beállítások konfigurálásához a **Azure Active Directory lapon**válassza a elemet `Devices > Device settings`.

### <a name="users-may-join-devices-to-azure-ad"></a>A felhasználók eszközöket csatlakoztathatnak az Azure AD-hez

Ezt a beállítást állítsa be a telepítés hatóköre alapján, és **válassza** ki az Azure ad-hez csatlakoztatott eszköz beállítását. 

![A felhasználók eszközöket csatlakoztathatnak az Azure AD-hez](./media/azureadjoin-plan/01.png)

### <a name="additional-local-administrators-on-azure-ad-joined-devices"></a>További helyi rendszergazdák az Azure AD-tartományhoz csatlakozott eszközökön

Válassza a **kijelölés** lehetőséget, és jelölje ki azokat a felhasználókat, akiket hozzá szeretne adni a helyi rendszergazdák csoportjához az összes Azure ad-hez csatlakoztatott eszközön. 

![További helyi rendszergazdák az Azure AD-tartományhoz csatlakozott eszközökön](./media/azureadjoin-plan/02.png)

### <a name="require-multi-factor-auth-to-join-devices"></a>Multi-Factor Auth megkövetelése eszközök csatlakoztatásához

Válassza az **Igen** lehetőséget, ha azt szeretné, hogy a felhasználók a MFA-t az eszközök Azure ad-hez való csatlakoztatása közben végezzék Az eszközöket az Azure AD-be az MFA használatával összekötő felhasználók esetében maga az eszköz második tényezővé válik.

![Multi-Factor Auth megkövetelése eszközök csatlakoztatásához](./media/azureadjoin-plan/03.png)

## <a name="configure-your-mobility-settings"></a>A mobilitási beállítások konfigurálása

A mobilitási beállítások konfigurálása előtt előfordulhat, hogy előbb hozzá kell adnia egy MDM-szolgáltatót.

**Mdm-szolgáltató hozzáadása**:

1. A **Azure Active Directory lap** **kezelés** szakaszában kattintson a elemre `Mobility (MDM and MAM)`. 
1. Kattintson az **alkalmazás hozzáadása**lehetőségre.
1. Válassza ki a MDM-szolgáltatót a listából.

   ![Alkalmazás felvétele](./media/azureadjoin-plan/04.png)

Válassza ki a MDM szolgáltatóját a kapcsolódó beállítások konfigurálásához. 

### <a name="mdm-user-scope"></a>MDM-felhasználói hatókör

A telepítés hatóköre alapján válasszon ki **egy** vagy **mindet** . 

![MDM-felhasználói hatókör](./media/azureadjoin-plan/05.png)

A hatókör alapján a következők egyike történik: 

- **A felhasználó Mdm hatókörben van**: Ha prémium szintű Azure AD-előfizetéssel rendelkezik, a MDM-regisztráció az Azure AD JOIN szolgáltatással együtt automatizálható. Az összes hatókörrel rendelkező felhasználónak rendelkeznie kell egy megfelelő licenccel a MDM. Ha ebben a forgatókönyvben a MDM-regisztráció meghiúsul, az Azure AD JOIN is vissza lesz állítva.
- **A felhasználó nincs Mdm hatókörben**: Ha a felhasználók nincsenek a MDM-hatókörben, az Azure AD JOIN MDM-regisztráció nélkül fejeződik be. Ez egy nem felügyelt eszköz eredményét eredményezi.

### <a name="mdm-urls"></a>MDM URL-címek

A MDM-konfigurációhoz három URL-cím kapcsolódik:

- MDM használati feltételeinek URL-címe
- MDM-felderítési URL-cím 
- MDM megfelelőségi URL-címe

![Alkalmazás felvétele](./media/azureadjoin-plan/06.png)

Minden URL-cím előre definiált alapértelmezett értékkel rendelkezik. Ha ezek a mezők üresek, további információért forduljon a MDM-szolgáltatóhoz.

### <a name="mam-settings"></a>MAM-beállítások

A MAM nem vonatkozik az Azure AD JOIN szolgáltatásra. 

## <a name="configure-enterprise-state-roaming"></a>Vállalati állapot barangolásának konfigurálása

Ha engedélyezni szeretné az állapot-barangolást az Azure AD-ben, hogy a felhasználók szinkronizálják a beállításait az eszközök között, tekintse [meg a Enterprise State roaming engedélyezése a Azure Active Directoryban](enterprise-state-roaming-enable.md)című témakört. 

**Javaslat**: Engedélyezze ezt a beállítást még a hibrid Azure AD-hez csatlakoztatott eszközök esetében is.

## <a name="configure-conditional-access"></a>Feltételes hozzáférés konfigurálása

Ha rendelkezik egy, az Azure AD-hez csatlakoztatott eszközökhöz konfigurált MDM-szolgáltatóval, a szolgáltató az eszközt megfelelőként megjelöli, amint az eszköz felügyelete alatt áll. 

![Szabályzatnak megfelelő eszköz](./media/azureadjoin-plan/46.png)

Ezzel a megvalósítással [feltételes hozzáféréssel rendelkező felügyelt eszközöket igényelhet a Cloud app Access számára](../conditional-access/require-managed-devices.md).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egy új Windows 10-es eszköz csatlakoztatása az Azure ad-vel az első futtatáskor](azuread-joined-devices-frx.md)
> a[munkahelyi eszköz csatlakoztatása a szervezet hálózatához](https://docs.microsoft.com/azure/active-directory/user-help/user-help-join-device-on-network)

<!--Image references-->
[1]: ./media/azureadjoin-plan/12.png

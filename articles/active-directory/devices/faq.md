---
title: Azure Active Directory eszközkezelés – GYIK | Microsoft Docs
description: Azure Active Directory eszközkezelés – gyakori kérdések.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbba3f1b753738de57aa311387e522bae1b7b523
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499796"
---
# <a name="azure-active-directory-device-management-faq"></a>Azure Active Directory eszközkezelés – gyakori kérdések

### <a name="q-i-registered-the-device-recently-why-cant-i-see-the-device-under-my-user-info-in-the-azure-portal-or-why-is-the-device-owner-marked-as-na-for-hybrid-azure-active-directory-azure-ad-joined-devices"></a>K: Nemrég regisztráltam az eszközt. Miért nem látom az eszközt a felhasználói adatok között a Azure Portal? Vagy a hibrid Azure Active Directory (Azure AD) csatlakoztatott eszközökhöz tartozó N/A-ként jelölt eszköz tulajdonosa

**V:** A hibrid Azure AD-hez csatlakoztatott Windows 10-es eszközök nem jelennek meg a **felhasználói eszközök**területen.
Használja a Azure Portal **minden eszköz** nézetét. Használhat egy PowerShell [Get-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) parancsmagot is.

Csak a következő eszközök szerepelnek a **felhasználói eszközök**területen:

- Minden olyan személyes eszköz, amely nem rendelkezik hibrid Azure AD-csatlakozással. 
- Az összes nem Windows 10 vagy Windows Server 2016 rendszerű eszköz.
- Az összes nem Windows rendszerű eszköz. 

---

### <a name="q-how-do-i-know-what-the-device-registration-state-of-the-client-is"></a>K: Hogyan tudni, hogy mi az ügyfél az eszköz regisztrációs állapota?

**V:** A Azure Portal lépjen a **minden eszköz**elemre. Keresse meg az eszközt az eszköz azonosítója alapján. Tekintse meg az értéket az illesztés típusa oszlopban. Előfordulhat, hogy az eszköz alaphelyzetbe áll vagy alaphelyzetbe áll. Ezért fontos, hogy az eszköz regisztrációs állapotát is ellenőrizzék:

- Windows 10 és Windows Server 2016 vagy újabb rendszerű eszközök esetén `dsregcmd.exe /status`futtassa a parancsot.
- A régebbi verziójú operációsrendszer-verziók esetében futtassa `%programFiles%\Microsoft Workplace Join\autoworkplace.exe`a parancsot.

---

### <a name="q-i-see-the-device-record-under-the-user-info-in-the-azure-portal-and-i-see-the-state-as-registered-on-the-device-am-i-set-up-correctly-to-use-conditional-access"></a>K: Megjelenik az eszköz rekordja a Azure Portal felhasználói információi között. Az állapotot pedig regisztráltként látom az eszközön. Helyesen állítottam be a feltételes hozzáférés használatát?

**V:** Az eszköz csatlakoztatási állapota, amelyet a **deviceID**mutat, meg kell egyeznie az Azure ad-beli állapottal, és meg kell felelnie a feltételes hozzáférésre vonatkozó értékelési feltételeknek. További információkért lásd: [felügyelt eszközök megkövetelése a Cloud app Accesshez feltételes hozzáféréssel](../conditional-access/require-managed-devices.md).

---

### <a name="q-why-do-my-users-see-an-error-message-saying-your-organization-has-deleted-the-device-or-your-organization-has-disabled-the-device-on-their-windows-10-devices-"></a>K: A felhasználók miért látnak hibaüzenetet arról, hogy "a szervezet törölte az eszközt" vagy "a szervezete letiltotta az eszközt" a Windows 10-es eszközökön?

**V:** Az Azure AD-hez csatlakoztatott vagy regisztrált Windows 10-es eszközökön a felhasználóknak egy [elsődleges frissítési tokent (PRT)](concept-primary-refresh-token.md) kell kiállítaniuk, amely lehetővé teszi az egyszeri bejelentkezést. A PRT érvényessége az eszköz validaity alapul. Ez az üzenet akkor jelenik meg, ha az eszköz törölve van vagy le van tiltva az Azure AD-ben anélkül, hogy magáról az eszközről kezdeményezze a műveletet. Egy eszköz törölhető vagy letiltható az Azure AD-ben az alábbi forgatókönyvek egyikével: 

- A felhasználó letiltja az eszközt a saját alkalmazások portálján. 
- A rendszergazda (vagy felhasználó) törli vagy letiltja az eszközt a Azure Portal vagy a PowerShell használatával
- Csak hibrid Azure AD-csatlakozás: A rendszergazda eltávolítja az eszközök szervezeti egységen kívüli hatókörét, ami azt eredményezi, hogy az eszközök törlődnek az Azure AD-ből

A műveletek kijavításának menetét alább találja.

---

### <a name="q-i-disabled-or-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell-but-the-local-state-on-the-device-says-its-still-registered-what-should-i-do"></a>K: Letiltottam vagy Töröltem az eszközt a Azure Portal vagy a Windows PowerShell használatával. Az eszköz helyi állapota azonban még regisztrálva van. Mit tegyek?

**V:** Ez a művelet kialakítás szerint történik. Ebben az esetben az eszköz nem fér hozzá a felhőben található erőforrásokhoz. A rendszergazdák elvégezhetik ezt a műveletet elavult, elveszett vagy ellopott eszközökön a jogosulatlan hozzáférés megakadályozása érdekében. Ha a művelet szándékosan lett elvégezve, újra engedélyeznie kell vagy újra regisztrálnia kell az eszközt az alább leírtak szerint.

- Ha az eszköz le lett tiltva az Azure AD-ben, akkor a megfelelő jogosultságokkal rendelkező rendszergazda engedélyezheti az Azure AD-portálon  

 - Ha az eszköz törlődik az Azure AD-ben, újra regisztrálnia kell az eszközt. Az újraregisztráláshoz manuális műveletet kell végrehajtani az eszközön. Az eszköz állapotán alapuló ismételt regisztrációra vonatkozó utasításokért lásd alább. 

      A hibrid Azure AD-hez csatlakoztatott Windows 10-és Windows Server 2016/2019-eszközök újbóli regisztrálásához hajtsa végre a következő lépéseket:

      1. Nyissa meg a parancssort rendszergazdaként.
      1. Írja be a `dsregcmd.exe /debug /leave` (igen) kifejezést.
      1. Jelentkezzen ki, majd jelentkezzen be az eszközt az Azure AD-vel újra regisztráló ütemezett feladat elindításához. 

      Az Azure AD-hez csatlakoztatott, régebbi verziójú Windows operációsrendszer-verziók esetén hajtsa végre a következő lépéseket:

      1. Nyissa meg a parancssort rendszergazdaként.
      1. Írja be a `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"` (igen) kifejezést.
      1. Írja be a `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"` (igen) kifejezést.

      Az Azure AD-hez csatlakoztatott eszközök Windows 10-es eszközeihez hajtsa végre a következő lépéseket:

      1. Nyissa meg a parancssort rendszergazdaként
      1. ENTER `dsregcmd /forcerecovery` (Megjegyzés: A művelet végrehajtásához rendszergazdának kell lennie.
      1. A megnyíló párbeszédpanelen kattintson a Bejelentkezés gombra, és folytassa a bejelentkezés folyamatával.
      1. Jelentkezzen ki, majd jelentkezzen be újra az eszközre a helyreállítás befejezéséhez.

      Az Azure AD-ban regisztrált Windows 10-es eszközök esetén hajtsa végre a következő lépéseket:

      1. Lépjen a **Beállítások** > **fiókok** > **hozzáférés munkahelyi vagy iskolai**rendszerhez elemre. 
      1. Válassza ki a fiókot, és válassza a leválasztás lehetőséget.
      1. Kattintson a "+ Csatlakoztatás" gombra, és regisztrálja újra az eszközt a bejelentkezési folyamaton keresztül.

---

### <a name="q-why-do-i-see-duplicate-device-entries-in-the-azure-portal"></a>K: Miért jelenik meg ismétlődő eszközök bejegyzései a Azure Portal?

**V:**

- A Windows 10 és a Windows Server 2016 esetében az ismétlődő próbálkozások megismétlésével és az ugyanahhoz az eszközhöz való újracsatlakozással ismétlődő bejegyzések is előfordulhatnak. 
- Minden olyan Windows-felhasználó, aki a **munkahelyi vagy iskolai fiók hozzáadása** szolgáltatást használja, létrehoz egy új, azonos eszköznév nevű eszközt.
- A helyi Azure címtár-tartományhoz csatlakozó, régebbi verziójú Windows operációsrendszer-verziók esetében az automatikus regisztráció egy új eszközt hoz létre ugyanazzal az eszköznév, mint minden olyan tartományi felhasználó, aki bejelentkezik az eszközre. 
- Egy olyan Azure AD-hez csatlakoztatott számítógép, amely törölve lett, újratelepült és újra csatlakozik ugyanazzal a névvel, egy másik, azonos nevű rekordként jelenik meg.

---

### <a name="q-does-windows-10-device-registration-in-azure-ad-support-tpms-in-fips-mode"></a>K: Támogatja a Windows 10-es eszközök regisztrációját az Azure AD-ben a TPM FIPS módban?

**V:** Nem, jelenleg a Windows 10-es eszközök regisztrálása az összes eszköz állapotához – hibrid Azure AD-csatlakozás, Azure AD-csatlakozás és Azure AD-regisztráció – nem támogatja a TPM FIPS módban. Az Azure AD-hez való sikeres csatlakozáshoz vagy regisztráláshoz az FIPS-módot ki kell kapcsolni az eszközök TPM.

---

**K: Miért férhet hozzá egy felhasználó a Azure Portal letiltott eszköz erőforrásaihoz?**

**V:** A visszavonás után egy órát vesz igénybe.

>[!NOTE] 
>A regisztrált eszközök esetében ajánlott törölni az eszközt, hogy a felhasználók ne férhessenek hozzá az erőforrásokhoz. További információ: [Mi az az eszköz beléptetése?](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune). 

---

## <a name="azure-ad-join-faq"></a>Azure AD-csatlakozás – gyakori kérdések

### <a name="q-how-do-i-unjoin-an-azure-ad-joined-device-locally-on-the-device"></a>K: Hogyan egy Azure AD-hez csatlakoztatott eszköz csatlakoztatását helyileg az eszközön?

**V:** 
- A hibrid Azure AD-hez csatlakoztatott eszközök esetében ügyeljen arra, hogy kikapcsolja az automatikus regisztrációt. Ezután az ütemezett feladat nem regisztrálja újra az eszközt. Ezután nyisson meg egy parancssort rendszergazdaként, és írja `dsregcmd.exe /debug /leave`be a következőt:. Vagy futtathatja ezt a parancsot parancsfájlként több eszközön a tömeges kikapcsoláshoz.
- A tiszta Azure AD-hez csatlakoztatott eszközökhöz ellenőrizze, hogy van-e offline helyi rendszergazdai fiókja, vagy hozzon létre egyet. Nem tud bejelentkezni bármilyen Azure AD-felhasználó hitelesítő adataival. Ezután lépjen a **Beállítások** > **fiókok** > **hozzáférés munkahelyi vagy iskolai**rendszerhez. Válassza ki a fiókját , és válassza a leválasztás lehetőséget. Ha a rendszer kéri, kövesse az utasításokat, és adja meg a helyi rendszergazdai hitelesítő adatokat. Indítsa újra az eszközt a leválasztási folyamat befejezéséhez.

---

### <a name="q-can-my-users-sign-in-to-azure-ad-joined-devices-that-are-deleted-or-disabled-in-azure-ad"></a>K: Bejelentkezhetnek a felhasználók az Azure ad-hez csatlakoztatott eszközökre, amelyeket törölnek vagy letiltanak az Azure AD-ben?

**V:** Igen. A Windows gyorsítótárazott felhasználónevet és jelszót biztosít, amely lehetővé teszi, hogy a korábban bejelentkezett felhasználók a hálózati kapcsolat nélkül is gyorsan hozzáférjenek az asztalhoz. 

Ha egy eszközt törölnek vagy letiltanak az Azure AD-ben, azt a Windows-eszköz nem ismeri. Így a korábban bejelentkezett felhasználók továbbra is hozzáférhetnek az asztalhoz a gyorsítótárazott felhasználónévvel és jelszóval. Ha azonban az eszközt törölték vagy letiltották, a felhasználók nem férhetnek hozzá az eszközön alapuló feltételes hozzáférés által védett erőforrásokhoz. 

A korábban nem bejelentkezett felhasználók nem férhetnek hozzá az eszközhöz. Nincs engedélyezve a gyorsítótárazott Felhasználónév és jelszó. 

---

### <a name="q-can-a-disabled-or-deleted-user-sign-in-to-an-azure-ad-joined-devices"></a>K: Letiltott vagy törölt felhasználói bejelentkezés egy Azure AD-hez csatlakoztatott eszközökre

**V:** Igen, de csak korlátozott ideig. Ha egy felhasználót törölnek vagy letiltanak az Azure AD-ben, azt a Windows-eszköz nem ismeri azonnal. A korábban bejelentkezett felhasználók a gyorsítótárazott felhasználónévvel és jelszóval érhetik el az asztalt. 

Az eszköz általában kevesebb, mint négy óra alatt ismeri a felhasználói állapotot. Ezt követően a Windows letiltja a felhasználók hozzáférését az asztalhoz. Mivel a felhasználó törölve lett, vagy le van tiltva az Azure AD-ben, az összes token visszavonásra kerül. Így nem férhetnek hozzá egyetlen erőforráshoz sem. 

A korábban nem bejelentkezett felhasználók törlése vagy letiltása nem fér hozzá az eszközhöz. Nincs engedélyezve a gyorsítótárazott Felhasználónév és jelszó. 

---

### <a name="q-why-do-my-users-have-issues-on-azure-ad-joined-devices-after-changing-their-upn"></a>K: Miért van a felhasználói problémák az Azure AD-hez csatlakoztatott eszközökön az egyszerű felhasználónév módosítása után?

**V:** Jelenleg az UPN-módosítások nem teljes mértékben támogatottak az Azure AD-hez csatlakoztatott eszközökön. Így az Azure AD-vel való hitelesítés az UPN módosítása után meghiúsul. Ennek eredményeképpen a felhasználóknak egyszeri bejelentkezéses és feltételes hozzáférési problémájuk van az eszközön. Jelenleg a felhasználóknak be kell jelentkezniük a Windowsba az új UPN használatával a probléma megoldásához. Jelenleg dolgozunk a probléma megoldásán. A vállalati Windows Hello szolgáltatással bejelentkezett felhasználók azonban nem szembesülnek ezzel a problémával. 

---

### <a name="q-my-users-cant-search-printers-from-azure-ad-joined-devices-how-can-i-enable-printing-from-those-devices"></a>K: A felhasználók nem kereshetnek nyomtatókat az Azure AD-hez csatlakoztatott eszközökről. Hogyan engedélyezhető a nyomtatás az eszközökről?

**V:** A nyomtatók Azure AD-hez csatlakoztatott eszközökön való üzembe helyezéséről lásd: [a Windows Server Hybrid Cloud Print üzembe helyezése előzetes hitelesítéssel](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy). A hibrid Felhőbeli nyomtatás üzembe helyezéséhez helyszíni Windows Serverre van szükség. Jelenleg a felhőalapú nyomtatási szolgáltatás nem érhető el. 

---

### <a name="q-how-do-i-connect-to-a-remote-azure-ad-joined-device"></a>K: Hogyan csatlakozni egy távoli Azure AD-hez csatlakoztatott eszközhöz?

**V:** Lásd: [Csatlakozás távoli Azure Active Directoryhoz csatlakoztatott számítógép](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc).

---

### <a name="q-why-do-my-users-see-you-cant-get-there-from-here"></a>K: Miért látom a felhasználók *, hogy itt nem tud*innen bejutni?

**V:** Konfigurált bizonyos feltételes hozzáférési szabályokat egy adott eszköz állapotának megköveteléséhez? Ha az eszköz nem felel meg a feltételeknek, a rendszer letiltja a felhasználókat, és látja ezt az üzenetet. Értékelje ki a feltételes hozzáférési szabályzat szabályait. Győződjön meg arról, hogy az eszköz megfelel a feltételeknek, hogy elkerülje az üzenetet.

---

### <a name="q-why-dont-some-of-my-users-get-azure-multi-factor-authentication-prompts-on-azure-ad-joined-devices"></a>K: Miért nem az Azure multi-Factor Authentication az Azure AD-hez csatlakoztatott eszközökön kéri a felhasználóknak?

**V:** A felhasználók a többtényezős hitelesítés használatával csatlakozhatnak vagy regisztrálhatnak egy eszközt az Azure AD-ben. Az eszköz maga is megbízható második tényezővé válik az adott felhasználó számára. Ha ugyanaz a felhasználó bejelentkezik az eszközre, és egy alkalmazáshoz fér hozzá, az Azure AD az eszközt második tényezőnek tekinti. Lehetővé teszi, hogy a felhasználó zökkenőmentesen hozzáférjen az alkalmazásokhoz további multi-Factor Authentication kérések nélkül. 

Ez a viselkedés:

- Az Azure AD-hez csatlakoztatott és az Azure AD-ban regisztrált eszközökre vonatkozik – a hibrid Azure AD-hez csatlakoztatott eszközök esetében nem.
- Nem alkalmazható minden olyan felhasználóra, aki bejelentkezik az adott eszközre. Így minden más felhasználó, aki hozzáfér az eszközhöz, egy multi-Factor Authentication-kihívást kap. Ezután hozzáférhetnek a többtényezős hitelesítést igénylő alkalmazásokhoz.

---

### <a name="q-why-do-i-get-a-username-or-password-is-incorrect-message-for-a-device-i-just-joined-to-azure-ad"></a>K: Miért nem *megfelelő a Felhasználónév vagy a jelszó az* Azure ad-hez csatlakoztatott eszközhöz?

**V:** A forgatókönyv gyakori okai a következők:

- A felhasználói hitelesítő adatai már nem érvényesek.
- A számítógép nem tud kommunikálni Azure Active Directoryokkal. Keresse meg a hálózati kapcsolattal kapcsolatos problémákat.
- Az összevont bejelentkezésekhez szükséges, hogy az összevonási kiszolgáló támogassa a WS-Trust végpontokat, amelyek engedélyezve vannak és elérhetők. 
- Engedélyezte az átmenő hitelesítést. Ezért az ideiglenes jelszót módosítani kell a bejelentkezéskor.

---

### <a name="q-why-do-i-see-the-oops-an-error-occurred-dialog-when-i-try-to-azure-ad-join-my-pc"></a>K: Miért látom a *Hoppá... Hiba történt.* párbeszédpanel, amikor megpróbálok csatlakozni a számítógéphez az Azure AD-vel?

**V:** Ez a hiba akkor fordul elő, amikor beállítja Azure Active Directory az Intune-ban való regisztrálást. Győződjön meg arról, hogy az Azure AD JOIN-t próbáló felhasználó a megfelelő Intune-licenccel van hozzárendelve. További információ: [a Windows-eszközök regisztrálásának beállítása](https://docs.microsoft.com/intune/windows-enroll).  

---

### <a name="q-why-did-my-attempt-to-azure-ad-join-a-pc-fail-although-i-didnt-get-any-error-information"></a>K: Miért nem sikerül az Azure AD-hez csatlakozni egy számítógéphez, de nem kaptam hibaüzenetet?

**V:** A probléma valószínű oka az, hogy a helyi beépített rendszergazdai fiók használatával jelentkezett be az eszközre. A telepítés befejezéséhez hozzon létre egy másik helyi fiókot a Azure Active Directory csatlakoztatása előtt. 

---

### <a name="qwhat-are-the-ms-organization-p2p-access-certificates-present-on-our-windows-10-devices"></a>A K:mi a Windows 10-es eszközökön megtalálható MS-Organization-P2P-Access tanúsítványok?

**V:** Az MS-Organization-P2P-Access tanúsítványokat az Azure AD állítja be mind az Azure AD-hez csatlakoztatott, mind a hibrid Azure AD-hez csatlakoztatott eszközökhöz. Ezek a tanúsítványok lehetővé teszik az azonos bérlőben lévő eszközök közötti megbízhatóságot a távoli asztali környezetekben. A rendszer egy tanúsítványt állít ki az eszköz számára, és egy másikat bocsát ki a felhasználó számára. Az eszköz tanúsítványa megtalálható a `Local Computer\Personal\Certificates` -ben, és egy napig érvényes. Ez a tanúsítvány megújul (új tanúsítvány kiállításával), ha az eszköz még aktív az Azure AD-ben. A felhasználói tanúsítvány megtalálható a- `Current User\Personal\Certificates` ben, és ez a tanúsítvány egy napig érvényes, de igény szerint van kiadva, ha a felhasználó egy távoli asztali munkamenetet próbál egy másik Azure ad-hoz csatlakoztatott eszközhöz csatlakozni. Nem újítható meg lejáratkor. Mindkét tanúsítvány a-ban található `Local Computer\AAD Token Issuer\Certificates`MS-Organization-P2P-Access tanúsítvány használatával lett kibocsátva. Ezt a tanúsítványt az Azure AD állítja ki az eszköz regisztrálása során. 

---

### <a name="qwhy-do-i-see-multiple-expired-certificates-issued-by-ms-organization-p2p-access-on-our-windows-10-devices-how-can-i-delete-them"></a>Q:Why az MS-Organization-P2P-Access által kiállított több lejárt tanúsítvány a Windows 10-es eszközökön? Hogyan törölhetem őket?

**V:** Probléma merült fel a Windows 10 1709-es verziójában, illetve alacsonyabb, ahol a lejárt MS-Organization-P2P-Access tanúsítványok továbbra is léteznek a számítógép-tárolóban a titkosítási problémák miatt. A felhasználók a hálózati kapcsolattal kapcsolatos problémákat okozhatnak, ha olyan VPN-ügyfeleket használ (például Cisco AnyConnect), amelyek nem képesek a nagy számú lejárt tanúsítvány kezelésére. Ezt a problémát a Windows 10 1803 kiadásban rögzítették, hogy automatikusan törölje a lejárt MS-Organization-P2P-Access tanúsítványokat. A probléma megoldásához frissítse az eszközöket a Windows 10 1803-re. Ha nem tudja frissíteni, törölheti ezeket a tanúsítványokat a negatív hatás nélkül.  

---

## <a name="hybrid-azure-ad-join-faq"></a>Hibrid Azure AD-csatlakozás – gyakori kérdések

### <a name="q-where-can-i-find-troubleshooting-information-to-diagnose-hybrid-azure-ad-join-failures"></a>K: Hol találhatok hibaelhárítási információkat a hibrid Azure AD-csatlakozási hibák diagnosztizálásához?

**V:** Hibaelhárítási információkért tekintse meg a következő cikkeket:

- [Hibrid Azure Active Directory csatlakoztatott Windows 10 és Windows Server 2016 rendszerű eszközök hibaelhárítása](troubleshoot-hybrid-join-windows-current.md)
- [A hibrid Azure Active Directory csatlakoztatása a régebbi verziójú eszközökhöz](troubleshoot-hybrid-join-windows-legacy.md)
 
### <a name="q-why-do-i-see-a-duplicate-azure-ad-registered-record-for-my-windows-10-hybrid-azure-ad-joined-device-in-the-azure-ad-devices-list"></a>K: Miért jelenik meg egy duplikált Azure AD-beli regisztrált rekord a Windows 10-es hibrid Azure AD-hez csatlakoztatott eszközhöz az Azure AD-eszközök listájában?

**V:** Ha a felhasználók fiókokat vesznek fel egy tartományhoz csatlakoztatott eszközön lévő alkalmazásokba, a rendszer a **fiók hozzáadása** a Windowshoz lehetőséget kér? Ha **Igen** értéket ad meg a parancssorban, az eszköz regisztrálva van az Azure ad-ben. A megbízhatósági kapcsolat típusa az Azure AD-ban regisztráltként van megjelölve. Miután engedélyezte a hibrid Azure AD-csatlakozást a szervezetben, az eszköz a hibrid Azure AD-hez is csatlakoztatva lesz. Ezután két eszköz állapot jelenik meg ugyanarra az eszközre. 

A hibrid Azure AD-csatlakozás elsőbbséget élvez az Azure AD regisztrált állapotával szemben. Így az eszköz minden hitelesítési és feltételes hozzáférési kiértékeléshez csatlakoztatva lesz a hibrid Azure AD-hez. Biztonságosan törölheti az Azure AD-beli regisztrált eszköz rekordját az Azure AD-portálról. Ebből a [kettős állapotból elkerülheti a Windows 10 rendszerű gépen, vagy](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan#review-things-you-should-know)megtisztíthatja azt. 

---

### <a name="q-why-do-my-users-have-issues-on-windows-10-hybrid-azure-ad-joined-devices-after-changing-their-upn"></a>K: Miért vannak problémák a felhasználók számára a Windows 10 hibrid Azure AD-hez csatlakoztatott eszközökön az egyszerű felhasználónév módosítása után?

**V:** A hibrid Azure AD-hez csatlakoztatott eszközök jelenleg nem támogatják teljes mértékben az UPN-módosításokat. Habár a felhasználók bejelentkezhetnek az eszközre, és hozzáférhetnek a helyi alkalmazásokhoz, az Azure AD-vel való hitelesítés az egyszerű felhasználónév módosítása után meghiúsul. Ennek eredményeképpen a felhasználóknak egyszeri bejelentkezéses és feltételes hozzáférési problémájuk van az eszközön. Ekkor meg kell szüntetnie az eszköz csatlakoztatását az Azure AD-ből (futtassa a "dsregcmd/Leave" parancsot emelt szintű jogosultságokkal), és csatlakozzon újra (automatikusan megtörténik) a probléma megoldásához. Jelenleg dolgozunk a probléma megoldásán. A vállalati Windows Hello szolgáltatással bejelentkezett felhasználók azonban nem szembesülnek ezzel a problémával. 

---

### <a name="q-do-windows-10-hybrid-azure-ad-joined-devices-require-line-of-sight-to-the-domain-controller-to-get-access-to-cloud-resources"></a>K: A Windows 10 hibrid Azure AD-hez csatlakoztatott eszközök megkívánják a tartományvezérlőnek a felhőalapú erőforrásokhoz való hozzáférés megszerzését?

**V:** Nem, kivéve, ha a felhasználó jelszava megváltozik. Ha a Windows 10 hibrid Azure AD-csatlakozás befejeződött, és a felhasználó legalább egyszer bejelentkezett, az eszközön nincs szükség a tartományvezérlőre a felhőalapú erőforrások eléréséhez. A Windows 10 az Azure AD-alkalmazásokhoz bárhonnan, internetkapcsolattal jelentkezhet be egyszeri bejelentkezésre, kivéve a jelszó módosításakor. Azok a felhasználók, akik bejelentkeznek a vállalati Windows Hello szolgáltatásba, továbbra is egyszeri bejelentkezést kaphatnak az Azure AD-alkalmazásokhoz még a jelszó módosítása után is, még akkor is, ha nem látják a tartományvezérlőt. 

---

### <a name="q-what-happens-if-a-user-changes-their-password-and-tries-to-login-to-their-windows-10-hybrid-azure-ad-joined-device-outside-the-corporate-network"></a>K: Mi történik, ha egy felhasználó megváltoztatja a jelszavát, és megpróbál bejelentkezni a Windows 10 hibrid Azure AD-hez csatlakoztatott eszközére a vállalati hálózaton kívül?

**V:** Ha a jelszó a vállalati hálózaton kívülre módosul (például az Azure AD-SSPR használatával), akkor a felhasználó nem fog tudni bejelentkezni az új jelszóval. A hibrid Azure AD-hez csatlakoztatott eszközök esetében a helyszíni Active Directory az elsődleges szolgáltató. Ha egy eszköz nem rendelkezik a tartományvezérlőre mutató vonallal, nem tudja érvényesíteni az új jelszót. Ezért a felhasználónak létre kell hoznia a kapcsolatot a tartományvezérlővel (VPN-en keresztül vagy a vállalati hálózaton keresztül), mielőtt be tudja jelentkezni az eszközre az új jelszóval. Ellenkező esetben csak a régi jelszavas bejelentkezést lehet bejelentkezni a Windows gyorsítótárazott bejelentkezési funkciója miatt. A régi jelszót azonban az Azure AD érvényteleníti a jogkivonat-kérelmek során, ezért megakadályozza az egyszeri bejelentkezést, és az eszközön alapuló feltételes hozzáférési szabályzatok meghibásodását. Ez a probléma nem lép fel, ha a vállalati Windows Hello-t használja. 

---

## <a name="azure-ad-register-faq"></a>Azure AD-regisztráció – gyakori kérdések

### <a name="q-can-i-register-android-or-ios-byod-devices"></a>K: Regisztrálhatok Android vagy iOS rendszerű BYOD-eszközöket?

**V:** Igen, de csak az Azure-eszköz regisztrációs szolgáltatásával és a hibrid ügyfeleknél. Active Directory összevonási szolgáltatások (AD FS) (AD FS) helyszíni eszköz regisztrációs szolgáltatása nem támogatja.

### <a name="q-how-can-i-register-a-macos-device"></a>K: Hogyan regisztrálhatok macOS-eszközt?

**V:** Hajtsa végre a következő lépéseket:

1.  [Megfelelőségi szabályzat létrehozása](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
1.  [Feltételes hozzáférési szabályzat definiálása macOS-eszközökhöz](../active-directory-conditional-access-azure-portal.md) 

**Megjegyzéseket tartalmazó**

- A feltételes hozzáférési szabályzatban szereplő felhasználóknak a macOS- [hez készült Office támogatott verziójára](../conditional-access/technical-reference.md#client-apps-condition) van szükségük az erőforrások eléréséhez. 
- Az első hozzáférés kipróbálásakor a rendszer felszólítja a felhasználókat, hogy regisztrálják az eszközt a vállalati portál használatával.

## <a name="next-steps"></a>További lépések

- További információ az [Azure ad regisztrált eszközeiről](concept-azure-ad-register.md)
- További információ az [Azure ad-hez csatlakoztatott eszközökről](concept-azure-ad-join.md)
- További információ a [hibrid Azure ad-hez csatlakoztatott eszközökről](concept-azure-ad-join-hybrid.md)

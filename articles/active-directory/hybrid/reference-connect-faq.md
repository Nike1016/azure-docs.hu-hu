---
title: Azure Active Directory Connect GYIK – | Microsoft Docs
description: Ez a cikk a Azure AD Connectával kapcsolatos gyakori kérdésekre ad választ.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/03/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6b551ee9a0a9c7ef9a8f5ff1bd7452a24dc04b7
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2019
ms.locfileid: "69014200"
---
# <a name="azure-active-directory-connect-faq"></a>Azure Active Directory Connect – gyakori kérdések

## <a name="general-installation"></a>Általános telepítés

**K: Hogyan lehet megerősíteni a Azure AD Connect-kiszolgálót a biztonsági támadási felület csökkentése érdekében?**

A Microsoft azt javasolja, hogy a Azure AD Connect kiszolgáló megerősítse a biztonsági támadási felületet az IT-környezet ezen kritikus összetevője számára.  Az alábbi ajánlásokat követve csökkentheti a szervezete biztonsági kockázatait.

* Azure AD Connect üzembe helyezése egy tartományhoz csatlakoztatott kiszolgálón, és a rendszergazdai hozzáférés korlátozása a tartományi rendszergazdák vagy más szigorúan ellenőrzött biztonsági csoportok számára

További tudnivalókért lásd: 

* [Rendszergazdák csoportok biztonságossá tétele](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [Beépített rendszergazdai fiókok biztonságossá tétele](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [Biztonsági fejlesztés és fenntartás a támadási felületek csökkentésével](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [A Active Directory támadási felület csökkentése](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

**K: A telepítés akkor működik, ha a Azure Active Directory (Azure AD) globális rendszergazdája kétfaktoros hitelesítést (2FA) engedélyez?**  
A február 2016-es verziótól kezdve ez a forgatókönyv támogatott.

**K: Van lehetőség felügyelet nélküli telepítésre Azure AD Connect?**  
Azure AD Connect telepítése csak a telepítővarázsló használata esetén támogatott. Felügyelet nélküli, csendes telepítés nem támogatott.

**K: Olyan erdővel rendelkezem, amelyben nem lehet kapcsolatba lépni az egyik tartománnyal. Hogyan telepíteni a Azure AD Connectt?**  
A február 2016-es verziótól kezdve ez a forgatókönyv támogatott.

**K: Működik a Azure Active Directory Domain Services (Azure AD DS) Health Agent a Server Core-ban?**  
Igen. Az ügynök telepítése után a következő PowerShell-parancsmag használatával végezheti el a regisztrációs folyamatot: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**K: Támogatja Azure AD Connect a két tartományból az Azure AD-be való szinkronizálást?**  
Igen, ez a forgatókönyv támogatott. Tekintse át a [több tartományt](how-to-connect-install-multiple-domains.md).
 
**K: Több összekötő is lehet ugyanahhoz a Active Directory tartományhoz Azure AD Connectban?**  
Nem, a több összekötő ugyanahhoz az AD-tartományhoz nem támogatott. 

**K: Áthelyezhetem a Azure AD Connect adatbázist a helyi adatbázisból egy távoli SQL Server-példányba?**    
Igen, a következő lépések általános útmutatást nyújtanak ennek végrehajtásához. Jelenleg egy részletesebb dokumentumon dolgozunk.
1. A LocalDB ADSync-adatbázis biztonsági mentése.
Ennek a legegyszerűbb módja, ha a SQL Server Management Studio telepítve van ugyanarra a gépre, mint Azure AD Connect. Kapcsolódjon a *(LocalDb) .\ADSync*, és végezze el a AdSync-adatbázis biztonsági mentését.

2. Állítsa vissza a ADSync-adatbázist a távoli SQL Server-példányra.

3. Telepítse a Azure AD Connectt a meglévő [távoli SQL](how-to-connect-install-existing-database.md)-adatbázison.
   A cikk bemutatja, hogyan lehet migrálni egy helyi SQL-adatbázis használatával. Ha egy távoli SQL Database-adatbázis használatával végez áttelepítést, a folyamat 5. lépésében meg kell adnia egy meglévő szolgáltatásfiókot is, amelyet a Windows Sync szolgáltatás fog futni. Ez a szinkronizálási motor szolgáltatás fiókja a következő módon van leírva:
   
      **Meglévő szolgáltatásfiók használata**: Alapértelmezés szerint a Azure AD Connect egy virtuális szolgáltatásfiókot használ a szinkronizálási szolgáltatásokhoz. Ha távoli SQL Server példányt használ, vagy egy hitelesítést igénylő proxyt használ, egy felügyelt szolgáltatásfiókot vagy egy szolgáltatásfiókot kell használnia a tartományban, és ismernie kell a jelszót. Ezekben az esetekben adja meg a használni kívánt fiókot. Győződjön meg arról, hogy a telepítést futtató felhasználók a rendszergazdák az SQL-ben, hogy a szolgáltatásfiók bejelentkezési hitelesítő adatai is létrehozhatók. További információ: [Azure ad Connect-fiókok és-engedélyek](reference-connect-accounts-permissions.md#adsync-service-account). 
   
      A legújabb buildben az SQL-rendszergazda sávon kívül kiépítheti az adatbázist, majd az Azure AD Connect-rendszergazda adatbázis-tulajdonosi jogosultságokkal telepítheti. További információkért lásd: [Azure ad Connect telepítése SQL meghatalmazott rendszergazdai engedélyekkel](how-to-connect-install-sql-delegation.md).

A dolgok egyszerű megtartása érdekében javasoljuk, hogy az SQL-ben rendszergazdaként telepítse a Azure AD Connectt telepítő felhasználókat. A legutóbbi buildek azonban mostantól használhatnak delegált SQL-rendszergazdákat a [Azure ad Connect telepítése SQL meghatalmazott rendszergazdai engedélyekkel](how-to-connect-install-sql-delegation.md)című témakörben leírtak szerint.

**K: Mik a legjobb gyakorlatok a mezőből?**  

A következő egy tájékoztató dokumentum, amely bemutatja a mérnöki, támogatási és tanácsadóink által az évek során kifejlesztett ajánlott eljárásokat.  Ez egy olyan listajeles listában jelenik meg, amelyet gyorsan lehet hivatkozni.  Bár ez a lista átfogóan próbálkozik, előfordulhat, hogy további ajánlott eljárások is léteznek, amelyek még nem tették meg a listán.

- Ha teljes SQL-t használ, akkor továbbra is a helyi és a távoli
    - Kevesebb ugrás
    - Könnyebben elhárítható a hibák
    - Kevésbé összetett
    - Erőforrásokat kell kijelölnie az SQL-hez, és lehetővé kell tennie a Azure AD Connect és az operációs rendszer terhelését
- Proxy kihagyása, ha az összes lehetséges, ha nem tudja megkerülni a proxyt, akkor biztosítania kell, hogy az időkorlát 5 percnél nagyobb legyen.
- Ha proxyra van szükség, akkor hozzá kell adnia a proxyt a Machine. config fájlhoz.
- Vegye figyelembe a helyi SQL-feladatokat és-karbantartást, valamint azt, hogy ezek milyen hatással lesznek Azure AD Connect – különösen újraindexelésre
- Külső megoldás biztosítása, mint a DNS
- Győződjön meg arról, hogy a [kiszolgáló specifikációi](how-to-connect-install-prerequisites.md#hardware-requirements-for-azure-ad-connect) a fizikai vagy virtuális kiszolgálók használatát ajánlják
- Győződjön meg arról, hogy ha olyan virtuális kiszolgálót használ, amelyet a szükséges erőforrások dedikáltak
- Győződjön meg arról, hogy a lemez és a lemez konfigurációja megfelel a SQL Server ajánlott eljárásainak
- Azure AD Connect Health telepítése és konfigurálása figyeléshez
- Használja a Azure AD Connect beépített törlési küszöbértéket.
- Körültekintően tekintse át a kiadási frissítéseket, hogy készüljön fel az összes módosításra és az új attribútumokra, amelyeket fel lehet venni
- Biztonsági mentés minden
    - Biztonsági mentési kulcsok
    - Biztonsági másolatok szinkronizálási szabályai
    - Biztonsági mentési kiszolgáló konfigurációja
    - Biztonsági másolat SQL Database
- Győződjön meg arról, hogy nincs olyan külső biztonsági mentési ügynök, amely az SQL VSS-író nélkül készít biztonsági mentést az SQL-kiszolgálóról (közös a virtuális kiszolgálókon, külső pillanatképekkel)
- A bonyolultságuk hozzáadásakor használt egyéni szinkronizálási szabályok korlátozása
- Azure AD Connect-kiszolgálók kezelése 0. szintű kiszolgálóként
- A felhő-szinkronizálási szabályok módosításának furfangos a hatás és a megfelelő üzleti illesztőprogramok megismerése nélkül
- Győződjön meg arról, hogy a megfelelő URL-cím és tűzfal portok nyitva vannak Azure AD Connect és Azure AD Connect Health támogatásához
- A felhőben szűrt attribútum kihasználása a látszólagos objektumok hibakereséséhez és megelőzéséhez
- Az átmeneti kiszolgálóval gondoskodjon arról, hogy a kiszolgálók közötti konzisztenciát a Azure AD Connect konfigurációs dokumentáló használja
- Az átmeneti kiszolgálóknak különálló adatközpontokban kell lenniük (fizikai helyük
- Az átmeneti kiszolgálók nem magas rendelkezésre állású megoldásnak számítanak, de több átmeneti kiszolgálóval is rendelkezhet
- A "késés" átmeneti kiszolgálók bevezetése miatt előfordulhat, hogy hiba esetén lehetséges az állásidő.
- Az előkészítési kiszolgáló összes frissítésének tesztelése és ellenőrzése először
- Mindig ellenőrizze az exportálást, mielőtt átvált az átmeneti kiszolgálóra a teljes importáláshoz és a teljes szinkronizáláshoz az üzleti hatás csökkentése érdekében serverLeverage
- A lehető legnagyobb mértékben megtarthatja a verziók közötti konzisztenciát Azure AD Connect-kiszolgálók között 

**K: Engedélyezhető Azure AD Connect az Azure AD Connector-fiók létrehozása a munkacsoporthoz tartozó számítógépen?**
Nem.  Ahhoz, hogy az Azure AD Connect automatikusan létre lehessen hozni az Azure AD Connector-fiókot, a gépnek tartományhoz kell tartoznia.  

## <a name="network"></a>Network (Hálózat)
**K: Tűzfallal, hálózati eszközzel vagy mással is rendelkezem, amely korlátozza, hogy a kapcsolatok mikor maradhatnak nyitva a hálózaton. Mire használható az ügyféloldali időtúllépési küszöbérték a Azure AD Connect használatakor?**  
Minden hálózati szoftver, fizikai eszköz vagy bármi más, ami korlátozza a kapcsolatok nyitva maradható maximális időtartamát, legalább öt percet (300 másodperc) kell használnia ahhoz, hogy a kiszolgáló, amelyen a Azure AD Connect-ügyfél telepítve van és Azure Active Directory. Ez a javaslat a korábban megjelent Microsoft identitásszinkronizálás-eszközökre is vonatkozik.

**K: Támogatottak-e egycímkés tartományok (SLDs-EK)?**  
Habár erősen ajánljuk ezt a hálózati konfigurációt ([lásd a cikket](https://support.microsoft.com/help/2269810/microsoft-support-for-single-label-domains)), Azure ad Connect szinkronizálást egyetlen címkéző tartománnyal használva támogatott, feltéve, hogy az egyszintű tartomány hálózati konfigurációja megfelelően működik.

**K: Támogatottak-e a különálló AD-tartománnyal rendelkező erdők?**  
Nem, Azure AD Connect nem támogatja a különálló névtereket tartalmazó helyszíni erdőket.

**K: Támogatottak-e a "pontozott" NetBIOS-nevek?**  
Nem, Azure AD Connect nem támogatja a helyszíni erdőket és tartományokat, amelyekben a NetBIOS-név egy pontot (.) tartalmaz.

**K: Támogatott-e a tiszta IPv6-környezet?**  
Nem, Azure AD Connect nem támogatja a tiszta IPv6-környezetet.

**Q:I több erdőből álló környezettel és a két erdő közötti hálózattal a NAT (hálózati címfordítás) van használatban. A két erdő között Azure AD Connect használ?**</br>
Nem, Azure AD Connect használata NAT-kapcsolaton keresztül nem támogatott. 

## <a name="federation"></a>Összevonás
**K: Mi a teendő, ha olyan e-mailt kapok, amely megkéri az Office 365-tanúsítvány megújítását?**  
A tanúsítvány megújításával kapcsolatos útmutatásért lásd: [tanúsítványok](how-to-connect-fed-o365-certs.md)megújítása.

**K: "A függő entitás automatikus frissítése" van beállítva az Office 365 függő entitáshoz. Kell-e bármilyen műveletet végrehajtani, ha a jogkivonat-aláíró tanúsítványa automatikusan áthalad?**  
Használja a [tanúsítványok](how-to-connect-fed-o365-certs.md)megújítása című cikkben ismertetett útmutatást.

## <a name="environment"></a>Környezet
**K: Támogatott a kiszolgáló átnevezése Azure AD Connect telepítése után?**  
Nem. A kiszolgálónév módosítása azt eredményezi, hogy a Szinkronizáló motor nem tud csatlakozni az SQL Database-példányhoz, és a szolgáltatás nem indítható el.

**K: Támogatja a következő generációs titkosítási (NGC) szinkronizálási szabályokat egy FIPS-kompatibilis gépen?**  
Nem.  Nem támogatottak.

**Q. Ha Letiltottam egy szinkronizált eszközt (például: HAADJ) a Azure Portal, miért van újra engedélyezve?**<br>
A szinkronizált eszközöket lehet létrehozni vagy elsajátítani a helyszínen. Ha egy szinkronizált eszköz van engedélyezve a helyszínen, akkor a Azure Portal még akkor is újra engedélyezheti, ha korábban egy rendszergazda letiltotta. Egy szinkronizált eszköz letiltásához használja a helyszíni Active Directory a számítógépfiók letiltásához.

**Q. Ha letiltom a felhasználói bejelentkezést az Office 365 vagy az Azure AD portálon a szinkronizált felhasználók számára, miért nem blokkolja a rendszer az újbóli bejelentkezést?**<br>
A szinkronizált felhasználókat a helyszínen lehet létrehozni vagy elsajátítani. Ha a fiók engedélyezve van a helyszínen, akkor feloldja a rendszergazda által elhelyezett bejelentkezési blokk blokkolását.

## <a name="identity-data"></a>Azonosító adatok
**K: Miért nem felel meg a helyszíni UPN-nek az userPrincipalName (UPN) attribútum az Azure AD-ben?**  
További információt a következő cikkekben talál:

* [Az Office 365-ben, az Azure-ban vagy az Intune-ban lévő felhasználónevek nem egyeznek meg a helyszíni egyszerű felhasználónévvel vagy a másodlagos bejelentkezési AZONOSÍTÓval](https://support.microsoft.com/kb/2523192)
* [A Azure Active Directory Sync Tool nem szinkronizálja a módosításokat, miután a felhasználói fiók egyszerű felhasználónevét más összevont tartomány használatára változtatta](https://support.microsoft.com/kb/2669550)

Az Azure AD-t úgy is beállíthatja, hogy a szinkronizálási motor frissítse az egyszerű felhasználónevet a [Azure ad Connect Sync szolgáltatás szolgáltatásai](how-to-connect-syncservice-features.md)című témakörben leírtak szerint.

**K: Támogatja a helyszíni Azure AD-csoport vagy a kapcsolatfelvételi objektum egy meglévő Azure AD-csoporttal vagy kapcsolatfelvételi objektummal való megegyezését?**  
Igen, ez a puha egyezés a proxyAddress alapul. A Soft Matching nem támogatja a levelezésre nem alkalmas csoportok használatát.

**K: Támogatott-e manuálisan beállítani a ImmutableId attribútumot egy meglévő Azure AD-csoporton vagy a Contact objektumon, hogy az megfeleljen egy helyszíni Azure AD-csoportnak vagy egy Contact objektumnak?**  
Nem, manuálisan állítja be a ImmutableId attribútumot egy meglévő Azure AD-csoporton vagy egy kapcsolattartási objektumon, hogy az megfeleljen. ez jelenleg nem támogatott.

## <a name="custom-configuration"></a>Egyéni konfiguráció
**K: Hol vannak a Azure AD Connect PowerShell-parancsmagok dokumentálva?**  
Az ezen a helyen dokumentált parancsmagok kivételével a Azure AD Connectban található további PowerShell-parancsmagok nem támogatottak az ügyfelek általi használatra.

**K: Használhatom a "kiszolgálói exportálás/kiszolgáló Importálás" lehetőséget, amely Synchronization Service Manager található a kiszolgálók közötti konfiguráció áthelyezéséhez?**  
Nem. Ez a beállítás nem kéri le az összes konfigurációs beállítást, és nem használható. Ehelyett a varázslóval hozza létre az alapkonfigurációt a második kiszolgálón, majd a szinkronizálási szabálygyűjtemény használatával PowerShell-parancsfájlokat hozhat létre a kiszolgálók közötti egyéni szabályok áthelyezéséhez. További információ: [swing Migration](how-to-upgrade-previous-version.md#swing-migration).

**K: A jelszavak gyorsítótárazva lesznek az Azure bejelentkezési oldalán, és a gyorsítótárazás megakadályozható, mert az *automatikus kiegészítés = "false"* attribútummal rendelkező Password input elemet tartalmaz?**  
Jelenleg a **jelszó** mező HTML-attribútumainak módosítása, beleértve az automatikus kiegészítés címkét, nem támogatott. Jelenleg olyan szolgáltatáson dolgozunk, amely lehetővé teszi az egyéni JavaScript használatát, amely lehetővé teszi bármely attribútum hozzáadását a **jelszó** mezőhöz.

**K: Az Azure bejelentkezési oldala azon felhasználók felhasználónevét jeleníti meg, akik korábban sikeresen bejelentkezett. Ki lehet kapcsolni ezt a viselkedést?**  
Jelenleg a **jelszó** beviteli mező HTML-attribútumainak módosítása, beleértve az automatikus kiegészítés címkét, nem támogatott. Jelenleg olyan szolgáltatáson dolgozunk, amely lehetővé teszi az egyéni JavaScript használatát, amely lehetővé teszi bármely attribútum hozzáadását a **jelszó** mezőhöz.

**K: Van lehetőség az egyidejű munkamenetek megelőzésére?**  
Nem.

## <a name="auto-upgrade"></a>Automatikus frissítés

**K: Mik az automatikus frissítés használatának előnyei és következményei?**  
Javasoljuk, hogy az összes ügyfél számára engedélyezze a Azure AD Connect telepítésének automatikus frissítését. Ennek előnye, hogy mindig megkapja a legújabb javításokat, beleértve a Azure AD Connectban található biztonsági rések biztonsági frissítéseit is. A frissítési folyamat fájdalommentes, és automatikusan megtörténik, amint új verzió érhető el. Több ezer Azure AD Connect ügyfél használja az automatikus frissítést minden új kiadással.

Az automatikus frissítési folyamat mindig először megállapítja, hogy a telepítés jogosult-e az automatikus frissítésre. Ha ez jogosult, a verziófrissítést a rendszer elvégzi és teszteli. A folyamat emellett a szabályok és az adott környezeti tényezők egyéni módosításait is keresi. Ha a tesztek azt mutatják, hogy a frissítés sikertelen, a rendszer automatikusan visszaállítja az előző verziót.

A folyamat a környezet méretétől függően több órát is igénybe vehet. A frissítés folyamatban van, a Windows Server Active Directory és az Azure AD közötti szinkronizálás nem történik meg.

**K: Kaptam egy e-mailt, amely arról tájékoztat, hogy az automatikus frissítés nem működik, és új verziót kell telepíteni. Miért van erre szükség?**  
A múlt évben a Azure AD Connect egy olyan verzióját adtuk ki, amely bizonyos esetekben letiltotta az automatikus verziófrissítés funkciót a kiszolgálón. Kijavítottuk a problémát Azure AD Connect 1.1.750.0-verzióban. Ha a probléma befolyásolta a problémát, a megoldáshoz PowerShell-parancsfájl futtatásával vagy a Azure AD Connect legújabb verziójára történő manuális frissítéssel csökkentheti. 

A PowerShell-szkript futtatásához [töltse le a parancsfájlt](https://aka.ms/repairaadconnect) , majd futtassa a Azure ad Connect-kiszolgálón egy felügyeleti PowerShell-ablakban. A szkript futtatásának megismeréséhez tekintse meg [ezt a rövid videót](https://aka.ms/repairaadcau).

A manuális frissítéshez le kell töltenie és futtatnia kell a AADConnect. msi fájl legújabb verzióját.
 
-  Ha a jelenlegi verziója régebbi, mint a 1.1.750.0, [töltse le és frissítse a legújabb verzióra](https://www.microsoft.com/download/details.aspx?id=47594).
- Ha a Azure AD Connect verziója 1.1.750.0 vagy újabb, nincs szükség további műveletre. Már használja az automatikus frissítés javítását tartalmazó verziót. 

**K: Kaptam egy e-mailt, amely azt jelzi, hogy a legújabb verzióra kell frissíteni az automatikus frissítés újbóli engedélyezéséhez. A 1.1.654.0 verziót használom. Frissíteni kell?**  
Igen, az automatikus frissítés újbóli engedélyezéséhez frissítenie kell a 1.1.750.0 vagy újabb verzióra. [Töltse le és frissítse a legújabb verzióra](https://www.microsoft.com/download/details.aspx?id=47594).

**K: Kaptam egy e-mailt, amely azt jelzi, hogy a legújabb verzióra kell frissíteni az automatikus frissítés újbóli engedélyezéséhez. Ha az automatikus frissítés engedélyezéséhez a PowerShellt használta, továbbra is telepíteni kell a legújabb verziót?**  
Igen, továbbra is frissítenie kell a 1.1.750.0 vagy újabb verzióra. Az automatikus frissítés szolgáltatás a PowerShell-lel való engedélyezése nem csökkenti a verziókban a 1.1.750.0 előtt talált automatikus frissítési problémát.

**K: Egy újabb verzióra szeretnék frissíteni, de nem biztos benne, hogy Azure AD Connect telepítettem, és nem rendelkezünk a felhasználónévvel és a jelszóval. Erre van szükségünk?**
Nem kell tudnia a Azure AD Connect frissítéséhez eredetileg használt felhasználónevet és jelszót. Használja a globális rendszergazdai szerepkörrel rendelkező bármely Azure AD-fiókot.

**K: Hogyan találhatom meg, hogy a Azure AD Connect melyik verzióját használom?**  
Annak ellenőrzéséhez, hogy a Azure ad Connect melyik verziója van telepítve a kiszolgálón, lépjen a Vezérlőpult elemre, és keresse meg Microsoft Azure ad kapcsolódás telepített verzióját a **programok** > **programok és szolgáltatások**elem kiválasztásával, ahogy az itt látható:

![Azure AD Connect verziója a Vezérlőpulton](./media/reference-connect-faq/faq1.png)

**K: Hogyan frissíteni a Azure AD Connect legújabb verziójára?**  
A legújabb verzióra való frissítéssel kapcsolatos további információkért tekintse [meg a Azure ad Connect: Frissítés egy előző verzióról a legújabbra](how-to-upgrade-previous-version.md). 

**K: Már frissítettük az elmúlt év Azure AD Connect legújabb verziójára. Újra kell frissíteni?**  
A Azure AD Connect csapat gyakori frissítéseket tesz elérhetővé a szolgáltatásban. A hibajavítások és a biztonsági frissítések, valamint az új funkciók kihasználása érdekében fontos a kiszolgáló naprakészen tartása a legújabb verzióval. Ha engedélyezi az automatikus frissítést, a szoftver verziója automatikusan frissül. Azure ad Connect verziószámának megkereséséhez tekintse [meg a Azure ad Connect: verziókiadásainak előzményeit](reference-connect-version-history.md) ismertető cikket.

**K: Mennyi ideig tart a frissítés végrehajtása, és milyen hatással van a felhasználókra?**  
A frissítéshez szükséges idő a bérlő méretétől függ. Nagyobb szervezetek esetén érdemes lehet az esti vagy hétvégi frissítést elvégezni. A frissítés során nem zajlik szinkronizálási tevékenység.

**K: Úgy vélem, hogy Frissítettem a Azure AD Connectre, de az Office Portal továbbra is megemlíti a következőt: az rSync. Miért van ez?**  
Az Office-csapat működik az Office-portál frissítéseinek beszerzésére, hogy tükrözze az aktuális terméknév nevét. Nem tükrözi, hogy melyik szinkronizáló eszközt használja.

**K: Az automatikus verziófrissítés állapota: "Felfüggesztve". Miért van felfüggesztve? Engedélyezem?**  
Egy korábbi verzióban egy hiba lett bevezetve, amely bizonyos körülmények között az automatikus frissítési állapotot "Felfüggesztve" értékre állítja. A manuális engedélyezése technikailag lehetséges, de számos összetett lépést igényel. A legjobb megoldás, ha a Azure AD Connect legújabb verzióját telepíti.

**K: A vállalatom szigorú változás-felügyeleti követelményeket támaszt, és azt szeretném ellenőrizni, hogy mikor van leküldve. Szabályozható, hogy mikor induljon el az automatikus frissítés?**  
Nem, jelenleg nincs ilyen funkció. A szolgáltatás kiértékelése egy későbbi kiadásra történik.

**K: E-mailt kapok, ha az automatikus frissítés sikertelen? Honnan tudhatom, hogy sikeres volt?**  
A frissítés eredményét nem fogja értesíteni. A szolgáltatás kiértékelése egy későbbi kiadásra történik.

**K: Tesz közzé ütemtervet az automatikus frissítések kiküldésének tervezésekor?**  
Az automatikus frissítés az újabb verzió kiadási folyamatának első lépése. Ha új kiadás van, a rendszer automatikusan leküldi a frissítéseket. A Azure AD Connect újabb verziói előre be vannak jelentve az [Azure ad](../fundamentals/whats-new.md)-ütemtervben.

**K: Az automatikus frissítés is frissíti a Azure AD Connect Health?**  
Igen, az automatikus frissítés frissíti a Azure AD Connect Healtht is.

**K: Az Azure AD Connect-kiszolgálók automatikus frissítése átmeneti módban is megtörténjen?**  
Igen, egy átmeneti módban lévő Azure AD Connect-kiszolgáló automatikus frissítése is végezhető.

**K: Ha az automatikus frissítés meghiúsul, és a Azure AD Connect-kiszolgáló nem indul el, mi a teendő?**  
Ritka esetekben a Azure AD Connect szolgáltatás nem indul el a frissítés végrehajtása után. Ezekben az esetekben a kiszolgáló újraindítása általában kijavítja a problémát. Ha a Azure AD Connect szolgáltatás még nem indul el, nyisson meg egy támogatási jegyet. További információ: [Service-kérelem létrehozása az Office 365-támogatással való kapcsolatfelvételhez](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/). 

**K: Nem tudom, mi a kockázat, amikor a Azure AD Connect újabb verziójára frissítek. Meg tud hívni nekem, hogy segítsenek a frissítésben?**  
Ha segítségre van szüksége a Azure AD Connect újabb verziójára való frissítéshez, nyisson meg egy támogatási jegyet az [Office 365-támogatással való kapcsolatfelvételhez szükséges szolgáltatási kérelem létrehozásához](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/).

## <a name="troubleshooting"></a>Hibaelhárítás
**K: Hogyan Kérhetek segítséget a Azure AD Connect?**

[Keresés a Microsoft Tudásbázisban (KB)](https://www.microsoft.com/en-us/search/result.aspx?q=azure+active+directory+connect)

* Az Azure AD Connect támogatásával kapcsolatos gyakori hibajavítási hibákért keresse meg a KB-os technikai megoldásokat.

[Azure Active Directory fórumok](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* [Az Azure ad](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required)-Közösségre kattintva technikai kérdéseket és válaszokat kereshet, vagy megteheti a saját kérdéseit.

[Támogatás kérése az Azure AD-hez](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)

---
title: Helyszíni Azure AD jelszavas védelem – gyakori kérdések – Azure Active Directory
description: Helyszíni Azure AD jelszavas védelem – gyakori kérdések
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ece7f93b5397db16e03c1eab1d2dc1e568113d9
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879264"
---
# <a name="azure-ad-password-protection-on-premises---frequently-asked-questions"></a>Helyszíni Azure AD jelszavas védelem – gyakori kérdések

Ez a szakasz az Azure AD jelszavas védelmével kapcsolatos gyakran feltett kérdésekre ad választ.

## <a name="general-questions"></a>Általános kérdések

**K: Milyen útmutatást kell adni a felhasználóknak a biztonságos jelszó kiválasztásához?**

A jelen témakörben a Microsoft aktuális útmutatója a következő hivatkozásra kattintva érhető el:

[Microsoft-jelszó – útmutató](https://www.microsoft.com/research/publication/password-guidance)

**K: Támogatja a helyszíni Azure AD jelszavas védelmet a nem nyilvános felhőkben?**

A helyszíni Azure AD-alapú jelszavas védelem csak a nyilvános felhőben támogatott. Nem jelentettek be dátumot a nem nyilvános Felhőbeli rendelkezésre álláshoz.

Az Azure AD-portál lehetővé teszi, hogy a nem nyilvános felhőkben még a helyi "jelszavas védelem a Windows Server-Active Directory" konfigurációjában is módosítsa a beállításokat. ezeket a módosításokat megőrzi a rendszer, de más esetben soha nem lép érvénybe. A helyszíni proxy ügynökök vagy erdők regisztrálása nem támogatott, ha a rendszer nem nyilvános Felhőbeli hitelesítő adatokat használ, és az ilyen regisztrációs kísérletek mindig sikertelenek lesznek.

**K: Hogyan alkalmazhatom az Azure AD jelszavas védelem előnyeit a helyszíni felhasználók egy részhalmazára?**

Nem támogatott. A üzembe helyezés és az engedélyezés után az Azure AD jelszavas védelme nem tesz különbséget – az összes felhasználó egyenlő biztonsági előnyöket kap.

**K: Mi a különbség a jelszó módosítása és a jelszó beállítása (vagy alaphelyzetbe állítás) között?**

A jelszó módosítása akkor történik meg, amikor egy felhasználó új jelszót választ, miután igazolta, hogy ismeri a régi jelszót. A jelszó módosítása például az, hogy mi történik, amikor egy felhasználó bejelentkezik a Windowsba, és a rendszer kéri, hogy válasszon ki egy új jelszót.

A jelszó beállítása (más néven jelszó-visszaállítás) az, amikor egy rendszergazda új jelszóval helyettesíti egy fiók jelszavát, például a Active Directory felhasználók és számítógépek felügyeleti eszköz használatával. Ehhez a művelethez magas szintű jogosultság szükséges (általában tartományi rendszergazda), és a műveletet végző személy általában nem ismeri a régi jelszót. Az ügyfélszolgálati forgatókönyvek gyakran jelszavas készleteket végeznek, például olyan felhasználók számára, akik elfelejtették a jelszavukat. A jelszó-megadási eseményeket is látni fogja, ha új felhasználói fiókot hoz létre első alkalommal egy jelszóval.

A jelszó-ellenőrzési házirend ugyanúgy viselkedik, függetlenül attól, hogy megtörtént-e a jelszó módosítása vagy beállítása. Az Azure AD Password Protection DC Agent szolgáltatás különböző eseményeket naplóz, hogy megtudja, van-e jelszó-módosítási vagy-beállítási művelet.  Lásd: [Az Azure ad jelszavas védelem figyelése és naplózása](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-monitor).

**K: Miért történik a duplikált jelszó-elutasítási események naplózása, amikor gyenge jelszót próbál beállítani a Active Directory felhasználók és számítógépek kezelése beépülő modullal?**

A Active Directory felhasználók és számítógépek kezelése beépülő modul először megpróbálja beállítani az új jelszót a Kerberos protokoll használatával. Ha hiba történik, a beépülő modul egy második kísérletet tesz a jelszó megadására egy örökölt (SAM RPC) protokoll használatával (a használt protokollok nem fontosak). Ha az új jelszót az Azure AD jelszavas védelme gyengenak tekinti, ez a beépülő modul viselkedése két, a jelszó-visszaállítási elutasítási esemény naplózása után következik be.

**K: Miért jelentkeznek be az Azure AD jelszavas védelem jelszavas védelme nevű jelszó-ellenőrzési események egy üres felhasználónévvel?**

A Active Directory támogatja a jelszavak tesztelését, így ellenőrizheti, hogy a tartomány aktuális jelszó-összetettségi követelményeit adja-e át, például a [NetValidatePasswordPolicy](https://docs.microsoft.com/windows/win32/api/lmaccess/nf-lmaccess-netvalidatepasswordpolicy) API használatával. Ha a rendszer ily módon érvényesíti a jelszót, a tesztelés a jelszó-szűrő-DLL-alapú termékek (például az Azure AD jelszavas védelem) érvényesítését is magában foglalja, de a jelszó-szűrési dll-hez átadott felhasználónevek üresek lesznek. Ebben az esetben az Azure AD jelszavas védelme a jelenleg érvényben lévő jelszóházirend használatával továbbra is érvényesíti a jelszót, és egy eseménynapló-üzenetet ad ki az eredmény rögzítéséhez, azonban az Eseménynapló-üzenetben üres lesz a Felhasználónév mező.

**K: Támogatott az Azure AD jelszavas védelem telepítése egymás mellett más jelszó-szűrő alapú termékekkel?**

Igen. A több regisztrált jelszó-szűrési dll-fájl támogatása egy alapvető Windows-szolgáltatás, amely nem jellemző az Azure AD jelszavas védelmére. A jelszó fogadása előtt minden regisztrált jelszó-szűrő dll-fájlnak meg kell egyeznie.

**K: Hogyan helyezhetem üzembe és konfigurálható az Azure AD jelszavas védelem az Active Directory-környezetben az Azure használata nélkül?**

Nem támogatott. Az Azure AD jelszavas védelme egy olyan Azure-szolgáltatás, amely támogatja a helyszíni Active Directory környezetbe való kiterjesztését.

**K: Hogyan változtathatom meg a szabályzat tartalmát a Active Directory szinten?**

Nem támogatott. A szabályzat csak az Azure AD-portál használatával felügyelhető. Lásd még az előző kérdést is.

**K: Miért szükséges a DFSR a SYSVOL-replikációhoz?**

A fájlreplikációs szolgáltatás (a DFSR megelőző technológia) számos ismert problémát tartalmaz, és a Windows Server újabb verzióiban nem támogatott. Active Directory. Az Azure AD jelszavas védelem zéró tesztelését a fájlreplikációs szolgáltatás által konfigurált tartományokon hajtja végre.

További információt a következő cikkekben talál:

[A SYSVOL-replikáció DFSR való áttelepítésének esete](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[A Befejezés közel van a fájlreplikációs szolgáltatáshoz](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

**K: Mekkora lemezterületre van szükség a szolgáltatás a tartomány SYSVOL-megosztásán?**

A pontos lemezterület-használat változhat, mivel ez olyan tényezőktől függ, mint a tiltott tokenek száma és hossza a Microsoft globálisan tiltott listájában, valamint a bérlői egyéni listán, valamint a titkosítási terhelést is. Ennek a listának a tartalma valószínűleg növekedni fog a jövőben. Ennek szem előtt tartásával ésszerű elvárás, hogy a szolgáltatásnak legalább öt (5) megabájt lemezterülettel kell rendelkeznie a tartomány SYSVOL-megosztásán.

**K: Miért van szükség újraindításra a DC-ügynök szoftverének telepítéséhez vagy frissítéséhez?**

Ezt a követelményt az alapvető Windows-viselkedés okozza.

**K: Van mód arra, hogy a TARTOMÁNYVEZÉRLŐi ügynököt egy adott proxykiszolgáló használatára konfigurálja?**

Nem. Mivel a proxykiszolgáló állapota nem megfelelő, nem fontos, hogy a rendszer mely konkrét proxykiszolgálót használja.

**K: Rendben van az Azure AD-beli jelszavas védelem proxy szolgáltatásának üzembe helyezése más szolgáltatásokkal, például Azure AD Connectokkal?**

Igen. Az Azure AD jelszavas védelmi proxy szolgáltatás és Azure AD Connect soha nem ütköznek közvetlenül egymással.

**K: Milyen sorrendben kell telepíteni és regisztrálni a DC-ügynököket és-proxykat?**

A proxy Agent telepítésének, a DC-ügynök telepítésének, az erdő regisztrálásának és a proxy regisztrációnak a rendezése támogatott.

**K: Aggódom a tartományvezérlők a szolgáltatás üzembe helyezésével kapcsolatos teljesítménybeli találatok esetén?**

Az Azure AD jelszavas védelem-tartományvezérlő ügynöke nem befolyásolja jelentős mértékben a tartományvezérlő teljesítményét egy meglévő kifogástalan állapotú Active Directory üzemelő példányban.

A legtöbb Active Directory központi telepítéshez tartozó jelszó-módosítási művelet a teljes munkaterhelés kis hányada az adott tartományvezérlőn. Tegyük fel például, hogy egy Active Directory tartomány 10000 felhasználói fiókkal és egy 30 napos MaxPasswordAge-házirenddel van beállítva. Ez a tartomány átlagosan 10 000/30 = ~ 333 jelszó-módosítási műveletet lát el naponta, ami egy kis számú művelet akár egyetlen tartományvezérlő esetében is. Tekintse át a lehetséges legrosszabb esetet: tegyük fel, hogy a ~ 333 jelszó módosításait egyetlen TARTOMÁNYVEZÉRLŐn hajtották végre egy órán belül. Ez a forgatókönyv például akkor fordulhat elő, ha sok alkalmazott dolgozik hétfőn reggel. Még ebben az esetben is a ~ 333/60 perc = hat jelszó módosítása percenként, ami nem jelentős terhelés.

Ha azonban az aktuális tartományvezérlők már csak teljesítmény-korlátozott szinten futnak (például maxed a CPU-ra, lemezterületre, lemezes I/O-re stb.), ajánlatos további tartományvezérlőket hozzáadni vagy bővíteni a rendelkezésre álló lemezterületet. a szolgáltatás telepítése. Lásd még a fenti, SYSVOL lemezterület-használatról szóló kérdést.

**K: Szeretném tesztelni az Azure AD jelszavas védelmet a saját tartományában található néhány tartományvezérlőn. Lehetséges a felhasználói jelszó módosításának kényszerítése az adott tartományvezérlők használatára?**

Nem. A Windows ügyfél operációs rendszere határozza meg, hogy melyik tartományvezérlőt használja a rendszer, amikor a felhasználó megváltoztatja a jelszavát. A tartományvezérlő kiválasztására olyan tényezők alapján van kiválasztva, mint például a Active Directory hely és az alhálózati hozzárendelések, a környezet-specifikus hálózati konfiguráció stb. Az Azure AD jelszavas védelme nem szabályozza ezeket a tényezőket, és nem befolyásolhatja, hogy melyik tartományvezérlő van kiválasztva a felhasználó jelszavának módosításához.

A cél részleges elérésének egyik módja az Azure AD jelszavas védelem üzembe helyezése az adott Active Directory-hely összes tartományvezérlőjén. Ez a módszer ésszerű lefedettséget biztosít a helyhez hozzárendelt Windows-ügyfelek számára, ezért az ügyfeleknek bejelentkezett felhasználókra és a jelszavuk módosítására is érvényes lesz.

**K: Ha az Azure AD jelszavas védelem DC Agent szolgáltatást csak az elsődleges tartományvezérlőn (PDC) telepíti, a rendszer a tartomány összes többi tartományvezérlőjét is védeni fogja?**

Nem. Ha egy felhasználó jelszava megváltozik egy adott nem PDC-alapú tartományvezérlőn, a rendszer soha nem továbbítja a tiszta szöveges jelszót az elsődleges tartományvezérlőnek (ez az ötlet egy gyakori helytelen érzékelés). Miután elfogadták az új jelszót egy adott TARTOMÁNYVEZÉRLŐn, a tartományvezérlő ezt a jelszót használja az adott jelszó különböző hitelesítési protokoll-specifikus kivonatának létrehozásához, majd megőrzi ezeket a kivonatokat a címtárban. A tiszta szöveges jelszó nem marad meg. A frissített kivonatok ezután replikálódnak az elsődleges tartományvezérlőre. Bizonyos esetekben előfordulhat, hogy a felhasználói jelszavakat közvetlenül az elsődleges tartományvezérlőn változtatják meg, a különböző tényezőktől, például a hálózati topológiától és a Active Directory hely kialakítástól függően. (Lásd az előző kérdést.)

Összefoglalva, az Azure AD jelszavas védelem DC Agent szolgáltatásának az elsődleges tartományvezérlőn való üzembe helyezéséhez szükség van a szolgáltatás 100%-os biztonsági lefedettségének elérésére a tartományon belül. A szolgáltatás csak az elsődleges tartományvezérlőn való telepítése nem biztosít Azure AD jelszavas védelmet a tartomány bármely más tartományvezérlőjén.

**K: Miért nem működik az egyéni intelligens zárolás, még azután sem, hogy az ügynököket a helyszíni Active Directory környezetbe telepítették?**

Az egyéni intelligens zárolás csak az Azure AD-ben támogatott. Az Azure AD-portál egyéni intelligens zárolási beállításainak módosításai nincsenek hatással a helyszíni Active Directory környezetre, még a telepített ügynökökkel is.

**K: Elérhető az Azure AD jelszavas védelem System Center Operations Manager felügyeleti csomagja?**

Nem.

**K: Az Azure AD miért is elutasítja a gyenge jelszavakat, bár úgy konfiguráltam, hogy a házirend naplózási módban legyen?**

A naplózási mód csak a helyszíni Active Directory környezetekben támogatott. Az Azure AD implicit módon mindig "kényszerítve" módban van, amikor kiértékeli a jelszavakat.

## <a name="additional-content"></a>További tartalom

Az alábbi hivatkozások nem részei az Azure AD jelszavas védelem alapszintű dokumentációjának, de hasznos információforrásnak számítanak a szolgáltatással kapcsolatos további információk.

[Az Azure AD jelszavas védelme már általánosan elérhető!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[E-mailes adathalászat elleni védelem útmutatója – 15. rész: A Microsoft Azure AD jelszavas védelmi szolgáltatás implementálása (helyszíni is)](https://blogs.technet.microsoft.com/cloudready/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[Az Azure AD jelszavas védelme és az intelligens zárolás mostantól nyilvános előzetes verzióban érhető el.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="microsoft-premierunified-support-training-available"></a>Microsoft Premier\Unified-támogatás érhető el

Ha többet szeretne megtudni az Azure AD jelszavas védelméről, és üzembe helyezi azt a környezetében, kihasználhatja a Microsoft proaktív szolgáltatásának előnyeit a Premier vagy Unified támogatási szerződéssel rendelkező ügyfelek számára. A szolgáltatás neve Azure Active Directory: Jelszavas védelem. További információért forduljon a technikai fiók kezelőjéhez.

## <a name="next-steps"></a>További lépések

Ha van olyan helyszíni Azure AD-beli jelszavas védelem kérdése, amely itt nem válaszol, küldje el az alábbi visszajelzési elemeket – Köszönjük!

[Azure AD jelszóvédelem üzembe helyezése](howto-password-ban-bad-on-premises-deploy.md)

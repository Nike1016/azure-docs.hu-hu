---
title: Az Azure AD Connect Health használata az AD FS szolgáltatással | Microsoft Docs
description: Ez az Azure AD Connect Health oldal bemutatja, hogyan figyelheti helyszíni AD FS infrastruktúráját.
services: active-directory
documentationcenter: ''
ms.reviewer: zhiweiwangmsft
author: billmath
manager: daveba
editor: curtand
ms.assetid: dc0e53d8-403e-462a-9543-164eaa7dd8b3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92825a9ef84edc30b6b34aa875f8a207c70c8511
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60350443"
---
# <a name="monitor-ad-fs-using-azure-ad-connect-health"></a>Az AD FS monitorozása az Azure AD Connect Health használatával
Az alábbi dokumentáció az AD FS infrastruktúra Azure AD Connect Health használatával végzett figyelésére vonatkozik. Az Azure AD Connect (szinkronizálási szolgáltatás) az Azure AD Connect Health használatával történő megfigyelésével kapcsolatos információkat [Az Azure AD Connect Health szinkronizálási szolgáltatás használata](how-to-connect-health-sync.md) című témakörben tekintheti meg. Az Active Directory tartományi szolgáltatások az Azure AD Connect Health használatával történő megfigyelésével kapcsolatos információkat pedig a [Using Azure AD Connect Health with AD DS](how-to-connect-health-adds.md) (Az Azure AD Connect Health használata az AD DS szolgáltatással) című témakörben találja.

## <a name="alerts-for-ad-fs"></a>AD FS-riasztások
Az aktív riasztások listáját az Azure AD Connect Health-riasztások szakaszban láthatja. Minden egyes riasztás tartalmazza a fontos információkat, a megoldás lépéseit, valamint a kapcsolódó dokumentációra mutató hivatkozásokat.

Egy aktív vagy egy megoldott riasztásra duplán kattintva egy új panel nyílik meg, amely további információkat, a riasztás megoldását segítő lépéseket és a fontosabb dokumentációkra mutató hivatkozásokat tartalmaz. A már megoldott riasztások esetében az előzményadatokat is megtekintheti.

![Az Azure AD Connect Health portál](./media/how-to-connect-health-adfs/alert2.png)

## <a name="usage-analytics-for-ad-fs"></a>Használatelemzés az AD FS szolgáltatáshoz
Az Azure AD Connect Health Használatelemzés elemzi az összevonási kiszolgálók hitelesítési forgalmát. A használatelemzés mezőre duplán kattintva megnyílik az elemzés panel, amelyen számos metrika és csoportosítás jelenik meg.

> [!NOTE]
> A használatelemzés AD FS szolgáltatással való használatához győződjön meg róla, hogy az AD FS-naplózás engedélyezve van. További információkért tekintse meg az [AD FS-naplózás engedélyezését](how-to-connect-health-agent-install.md#enable-auditing-for-ad-fs).
>
>

![Az Azure AD Connect Health portál](./media/how-to-connect-health-adfs/report1.png)

További metrikák kiválasztásához adjon meg egy időtartományt, vagy a csoportosítás módosításához kattintson a jobb gombbal a használatelemzés diagramra, és válassza a Diagram szerkesztése lehetőséget. Ezt követően megadhatja az időtartományt, kiválaszthat egy másik metrikát, illetve módosíthatja a csoportosítást. A hitelesítési forgalom eloszlását különféle „metrikák” alapján tekintheti meg, és mindegyik metrikát csoportosíthatja a következő szakaszban ismertetett vonatkozó „csoportosítási szempont” paraméterek használatával:

**Metrika: Kérések összesen** – az összes AD FS-kiszolgálók által feldolgozott kérelmek száma.

|Csoportosítási szempont | Mit jelöl az adott csoportosítás, és miért hasznos? |
| --- | --- |
| Összes | Az összes AD FS-kiszolgáló által feldolgozott kérelmek teljes számát jeleníti meg.|
| Alkalmazás | Az összes kérelmet a célzott függő entitások alapján csoportosítja. Ez a csoportosítás akkor hasznos, ha szeretné megtudni, melyik alkalmazás hány százalékát fogadja a teljes forgalomnak. |
|  Kiszolgáló |Az összes kérelmet az azokat feldolgozó kiszolgálók alapján csoportosítja. Ez a csoportosítás akkor hasznos, ha szeretné megtudni a teljes forgalom terheléseloszlását.
| Munkahelyi csatlakoztatás |Az összes kérelmet az alapján csoportosítja, hogy azok a munkahelyhez csatlakoztatott (ismert) eszközökről érkeznek-e. Ez a csoportosítás akkor hasznos, ha szeretné megtudni, hogy erőforrásaihoz hozzáférnek-e az identitás-infrastruktúra számára ismeretlen eszközökről származó kérelmek. |
|  Hitelesítési módszer | Az összes kérelmet a hitelesítés során alkalmazott hitelesítési módszer alapján csoportosítja. Ez a csoportosítás akkor hasznos, ha szeretné megtudni a hitelesítéshez leggyakrabban használt hitelesítési módszereket. A lehetséges hitelesítési módszerek az alábbiak <ol> <li>Integrált Windows-hitelesítés (Windows)</li> <li>Űrlapalapú hitelesítés (Űrlapok)</li> <li>Egyszeri bejelentkezés (SSO)</li> <li>X509 tanúsítványalapú hitelesítés (Tanúsítvány)</li> <br>Ha az összevonási kiszolgálók a kérelmet SSO cookie-val kapják meg, a rendszer az adott kérelmet egyszeri bejelentkezésesnek (SSO) veszi. Ilyen esetekben, ha a cookie érvényes, a rendszer nem kér hitelesítő adatokat a felhasználótól, és zökkenőmentes hozzáférést biztosít az alkalmazáshoz. Ez a viselkedés gyakori, ha több, az összevonási kiszolgálók által védett jogcímfelhasználó alkalmazással rendelkezik. |
| Hálózati hely | Az összes kérelmet a felhasználó hálózati helye alapján csoportosítja. Ez lehet intranet vagy extranet. Ez a csoportosítás akkor hasznos, ha szeretné megtudni, hogy a forgalom hány százaléka érkezik az intranetről vagy az extranetről. |


**Metrika: Összes sikertelen kérelem** – sikertelen az összevonási szolgáltatás által feldolgozott kérelmek teljes száma. (Ez a metrika csak az AD FS for Windows Server 2012 R2 szolgáltatás esetében elérhető)

|Csoportosítási szempont | Mit jelöl az adott csoportosítás, és miért hasznos? |
| --- | --- |
| Hibatípus | A hibák számát jeleníti meg előre meghatározott hibatípusok szerint. Ez a csoportosítás akkor hasznos, ha szeretné megismerni a gyakori hibatípusokat. <ul><li>Helytelen felhasználónév vagy jelszó: Hibák miatt a helytelen felhasználónév vagy jelszó.</li> <li>"Extranet Lockout": Hiba oka, hogy a kérelem az extranetről érkező lett zárolva felhasználó </li><li> "A jelszó lejárt": A hiba oka, hogy a felhasználó lejárt jelszóval jelentkezett be.</li><li>"Disabled Account": A hiba oka, hogy a felhasználó letiltott fiókkal naplózás.</li><li>"Device Authentication": A hiba oka, hogy a felhasználók hitelesítése sikertelen.</li><li>"Felhasználói tanúsítvány hitelesítéséhez": A hiba oka, hogy a felhasználók való hitelesítése érvénytelen tanúsítvány miatt sikertelen volt.</li><li>"MFA": A hiba oka, hogy a felhasználó többtényezős hitelesítéssel való hitelesítése sikertelen volt.</li><li>"Más Credential": "Issuance Authorization": Sikertelen hitelesítési problémák okozta hibák.</li><li>"Issuance Delegation": Kiállítási hibák miatt sikertelen.</li><li>"Token elfogadása": Hiba oka, hogy a jogkivonat egy külső identitásszolgáltatótól az AD FS.</li><li>"Protocol": Protokollproblémák okozta hibák.</li><li>"Ismeretlen": Minden más. Bármely egyéb olyan hiba, amely nem illik a meghatározott kategóriákba.</li> |
| Kiszolgáló | A hibákat a kiszolgálók alapján csoportosítja. Ez a csoportosítás akkor hasznos, ha szeretné megismerni a hibák eloszlását a kiszolgálók között. Az egyenetlen eloszlás esetleg jelezheti azt, hogy valamelyik kiszolgáló meghibásodott. |
| Hálózati hely | A hibákat a kérelmek hálózati helye (intranet vagy extranet) alapján csoportosítja. Ez a csoportosítás akkor hasznos, ha szeretné megismerni a sikertelen kérelemtípusokat. |
|  Alkalmazás | A hibákat a célzott alkalmazás (jogcímfelhasználó alkalmazás) alapján csoportosítja. Ez a csoportosítás akkor hasznos, ha szeretné megtudni, hogy melyik célzott alkalmazás kapja a legtöbb hibát. |

**Metrika: Felhasználók száma** – átlagos aktívan az AD FS segítségével történő hitelesítésről egyedi felhasználók száma

|Csoportosítási szempont | Mit jelöl az adott csoportosítás, és miért hasznos? |
| --- | --- |
|Összes |Ez a mérőszám az összevonási szolgáltatást egy adott időszeletben használó felhasználók átlagos darabszámát adja meg. A felhasználók nincsenek csoportosítva. <br>Az átlag a választott időszelet függvénye. |
| Alkalmazás |Az átlagos felhasználószámot a célzott alkalmazás (jogcímfelhasználó alkalmazás) alapján csoportosítja. Ez a csoportosítás akkor hasznos, ha szeretné megtudni, melyik alkalmazást hány felhasználó használja. |

## <a name="performance-monitoring-for-ad-fs"></a>Teljesítményfigyelés az AD FS szolgáltatáshoz
Az Azure AD Connect Health-teljesítményfigyelés a metrikákkal kapcsolatos figyelési információkat biztosít. A Figyelés mező kijelölésével megnyílik egy új panel, amely részletes információkat tartalmaz a metrikákra vonatkozóan.

![Az Azure AD Connect Health portál](./media/how-to-connect-health-adfs/perf1.png)

A panel tetején lévő Szűrővel kiszolgálónként szűrheti az adatokat, és megtekintheti az egyes különálló kiszolgálók metrikáit. A metrika módosításához a jobb gombbal kattintson a figyelési diagramra a figyelés panel alatt, és válassza a Diagram szerkesztése lehetőséget (vagy a Diagram szerkesztése gombot). A megnyíló új panelen a legördülő menüből kiválaszthatja a további metrikákat, és megadhat egy időtartományt a vonatkozó teljesítményadatok megtekintéséhez.

## <a name="top-50-users-with-failed-usernamepassword-logins"></a>A felhasználónevet/jelszót leggyakrabban sikertelenül megadó 50 felhasználó
A sikertelen hitelesítési kérelmek leggyakoribb oka az AD FS-kiszolgálókon, hogy a kérelem érvénytelen hitelesítő adatokkal, azaz rossz felhasználónévvel vagy jelszóval rendelkezik. Általában akkor történik meg, ha a felhasználók bonyolult jelszavakat használnak, elfelejtik vagy elgépelik a jelszavakat.

Azonban más okokból is váratlan számú kérelmet kell kezelniük az AD FS-kiszolgálók, például előfordulhat, hogy: Egy alkalmazás gyorsítótárak felhasználói hitelesítő adatok és a hitelesítő adatok lejárnak, vagy egy rosszindulatú felhasználó megpróbál bejelentkezni a fiók jól ismert jelszavak sorozatával. Ez a két példa érvényes oka lehet annak, hogy a kérelmek száma hirtelen megnő.

Az AD FS szolgáltatáshoz elérhető Azure AD Connect Health jelentést készít a rossz felhasználónév/jelszó megadásával leggyakrabban sikertelenül bejelentkezni próbáló 50 felhasználóról. Ez a jelentés a farmokon lévő összes AD FS-kiszolgáló által létrehozott összes naplózási esemény feldolgozásával készíthető el.

![Az Azure AD Connect Health portál](./media/how-to-connect-health-adfs/report1a.png)

A jelentésen belül könnyen elérhetőek az alábbi információk:

* A rossz felhasználónévvel/jelszóval rendelkező meghiúsult kérelmek teljes száma az elmúlt 30 napban
* A rossz felhasználónévvel/jelszóval sikertelenül bejelentkezni próbáló felhasználók átlagos száma naponta.

Erre a részre kattintva megjelenik a fő jelentéspanel, amelyen további részletek láthatóak. Ezen a panelen többek között egy trendekkel kapcsolatos információkat tartalmazó diagram is látható, amellyel meghatározható a rossz felhasználónévvel vagy jelszóval leadott kérelmek alapmetrikája. Megjeleníti továbbá az elmúlt héten sikertelen bejelentkezéssel leggyakrabban próbálkozó 50 felhasználó listáját. Az elmúlt heti 50 felhasználó megtekintésével azonosíthatóak a helytelen jelszóval végzett bejelentkezési kísérletekben jelentkező kiugró értékek.  

A diagramon az alábbi információk találhatóak meg:

* A rossz felhasználónév/jelszó miatt meghiúsult bejelentkezések teljes száma napi szinten.
* A meghiúsult bejelentkezéseket megkísérlő egyedi felhasználók teljes száma napi szinten.
* A legutóbbi kérelmet leadó ügyfél IP-címe

![Az Azure AD Connect Health portál](./media/how-to-connect-health-adfs/report3a.png)

A jelentésben az alábbi információk találhatók:

| Jelentéselem | Leírás |
| --- | --- |
| Felhasználóazonosító |A használt felhasználóazonosítót mutatja. Ez a felhasználó által begépelt érték, ami bizonyos esetekben a rossz felhasználóazonosító. |
| Sikertelen próbálkozások |A sikertelen bejelentkezések teljes számát mutatja az adott felhasználóazonosítóhoz. A táblázat a sikertelen bejelentkezések mennyisége alapján van rendezve csökkenő sorrendben. |
| Utolsó sikertelen kísérlet |Az utolsó sikertelen kísérlet időpontjának időbélyegét mutatja. |
| Legutóbbi sikertelen kísérlet IP-címe |A legutóbbi sikertelen kérelemhez tartozó IP-címet jeleníti meg. Ha ebben az értékben egynél több IP-címet lát, lehet, hogy az érték magában foglalja a továbbított ügyfél IP-címét és a felhasználó utoljára megkísérelt kéréséhez tartozó IP-címet.  |

> [!NOTE]
> A jelentés 12 óránként automatikusan frissül az ez idő alatt gyűjtött új információkkal. Ezért az utolsó 12 órában gyűjtött bejelentkezési kísérletek esetleg még nem szerepelnek a jelentésben.

## <a name="related-links"></a>Kapcsolódó hivatkozások
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Az Azure AD Connect Health-ügynök telepítése](how-to-connect-health-agent-install.md)
* [Kockázatos IP jelentés](how-to-connect-health-adfs-risky-ip.md)


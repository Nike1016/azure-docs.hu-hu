---
title: Jelszókivonat-szinkronizálás és az Azure AD Connect-szinkronizálás megvalósítása |} A Microsoft Docs
description: A Jelszókivonat-szinkronizálás működését, és hogyan állítható be arról nyújt tájékoztatást.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/02/2019
ms.subservice: hybrid
ms.author: billmath
search.appverid:
- MET150
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98101973627750f87fd06d3f617a1af764a837ee
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774238"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Jelszókivonat-szinkronizálás és az Azure AD Connect-szinkronizálás megvalósítása
A cikk ismerteti, hogy az egy felhőalapú Azure Active Directory (Azure AD) példány egy helyszíni Active Directory-példányból származó felhasználói jelszavakat szinkronizálja szükséges információkat.

## <a name="how-password-hash-synchronization-works"></a>A Jelszókivonat-szinkronizálás működése
Az Active Directory tartományi szolgáltatások tárolja a jelszavak kivonat értéke, a tényleges felhasználói jelszó jelképeként formájában. A Jelszókivonat értéke egy egyirányú matematikai függvény eredménye (a *kivonatoló algoritmus*). Az egyirányú függvény eredménye semmilyen módszerrel nem fejthető vissza a jelszó egyszerű szöveges verziójára. 

A jelszó szinkronizálása, az Azure AD Connect szinkronizálása a Jelszókivonat kigyűjti a helyszíni Active Directory-példányból. További biztonsági feldolgozási alkalmazza a rendszer a Jelszókivonat előtt, a rendszer szinkronizálja az Azure Active Directory hitelesítési szolgáltatás. Jelszavak szinkronizálódnak, felhasználónkénti alapon és időrendi sorrendben.

A tényleges adatok folyamatot, a jelszó Jelszókivonat-szinkronizálási folyamat nagyon hasonlít a felhasználói adatok szinkronizálását. Azonban jelszavak gyakrabban, mint a standard szintű könyvtár szinkronizálási időszak más attribútumok vannak szinkronizálva. A jelszó Jelszókivonat-szinkronizálási folyamat 2 percenként fut. Ez a folyamat gyakoriságának nem módosítható. Jelszó szinkronizálásakor felülírja a meglévő cloud jelszót.

A jelszó Jelszókivonat szinkronizálása funkció engedélyezéséhez először az összes érintett felhasználót a jelszavak újbóli használatának kezdeti szinkronizálást végez. Nem lehet explicit módon meghatározni egy szinkronizálni kívánt felhasználói jelszavak részét.

Amikor módosítja egy a helyszíni jelszót, a módosított jelszó szinkronizált, leggyakrabban a percek alatt.
A jelszó Jelszókivonat-szinkronizálási szolgáltatás automatikusan újrapróbálkozik a sikertelen szinkronizálási kísérlet. Hiba esetén egy jelszó szinkronizálása tett kísérlet során hiba az eseménynaplóban naplózza.

A jelszó szinkronizálása nem befolyásolja az aktuálisan bejelentkezett felhasználó.
Az aktuális munkamenet a felhőalapú szolgáltatás azonnal nem érinti, amely akkor fordul elő, amikor be van jelentkezve, felhőalapú szolgáltatásként szinkronizált jelszómódosítás. Azonban a felhőszolgáltatás újra hitelesíteni kell, amikor meg kell adnia az új jelszót.

A felhasználónak meg kell adnia vállalati hitelesítői adataikkal másodszor az Azure Active Directory, függetlenül attól, hogy azok jelentkezett be, hogy a vállalati hálózaton való hitelesítésére. Ez a minta minimalizálható, azonban, ha a felhasználó a maradjak bejelentkezve megtartása bejelentkezéskor (KMSI) jelölőnégyzetet. Ez a beállítás egy munkamenetcookie-t, amely 180 napig hitelesítési megkerüli állítja be. KMSI viselkedés engedélyezhető vagy az Azure AD-rendszergazda letiltotta. Ezenfelül csökkentheti a jelszó utasításokat bekapcsolásával [közvetlen egyszeri bejelentkezés](how-to-connect-sso.md), amely automatikusan aláírja a felhasználók mikor legyenek a vállalati eszközeiket a vállalati hálózathoz csatlakozik.

> [!NOTE]
> Jelszó-szinkronizálás csak a támogatott a objektum típusa felhasználót az Active Directoryban. A iNetOrgPerson objektum típus nem támogatott.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>Részletes leírása, hogyan működik a Jelszókivonat-szinkronizálás
Az alábbi szakasz ismerteti, részletes, az Active Directory és az Azure AD között Jelszókivonat-szinkronizálás működését.

![A folyamat részletes jelszó](./media/how-to-connect-password-hash-synchronization/arch3b.png)


1. Két percenként, a jelszó kivonatoló szinkronizálási ügynököt az AD Connect kiszolgálói kérelmek tárolja a jelszókivonatokat (az unicodePwd attribútum) tartományvezérlőt.  A standard keresztül van ehhez a kérelemhez [MS-DRSR](https://msdn.microsoft.com/library/cc228086.aspx) használt szinkronizálni a tartományvezérlők közötti replikációs protokoll. A szolgáltatás fiók a beszerzése a jelszót a kivonatok Címtárváltozások replikálása és replikálása összes AD jogosultságaitól (alapértelmezés szerint a telepítési) kell rendelkeznie.
2. Küldés előtt, a tartományvezérlő az MD4 Jelszókivonat titkosítja, amely egy kulcs használatával egy [MD5](https://www.rfc-editor.org/rfc/rfc1321.txt) az RPC munkamenetkulcs és a egy salt kivonatát. Majd elküldi az eredményt a jelszó Jelszókivonat-szinkronizálási ügynök RPC-n keresztül. A tartományvezérlő is továbbítja a védőérték a szinkronizálási ügynöknek a tartományvezérlő replikációs protokoll használatával, így az ügynök tudja majd visszafejteni a boríték.
3.  A jelszó Jelszókivonat-szinkronizálási ügynök, amelyet a titkosított boríték, követően használja [MD5CryptoServiceProvider](https://msdn.microsoft.com/library/System.Security.Cryptography.MD5CryptoServiceProvider.aspx) és a egy vissza az eredeti MD4 formátumára a fogadott adatok visszafejtéséhez kulcs létrehozásához. A jelszó Jelszókivonat-szinkronizálási ügynök soha nem rendelkezik hozzáféréssel a tiszta szöveges jelszó. A jelszó kivonatoló szinkronizálási ügynök használatát MD5 szigorúan replikációs protokollal kompatibilis-e a tartományvezérlő, és csak használja a helyszínen, a tartományvezérlő és a jelszó Jelszókivonat-szinkronizálási ügynök között.
4.  A jelszó Jelszókivonat-szinkronizálási ügynök bontja ki a 16 bájtos bináris Jelszókivonat 64 bájt átalakításával első UTF-16 kódolással bináris biztonsági 32 bájtos hexadecimális karakterlánc, majd átalakítja a következő karakterláncot a kivonatot.
5.  A jelszó Jelszókivonat-szinkronizálási ügynök ad hozzá egy felhasználói Salt érték, egy 10 bájtos hossza Salt érték, a 64 bájtos bináris fokozott védelme érdekében az eredeti Jelszókivonat álló száma.
6.  A jelszó Jelszókivonat-szinkronizálási ügynök, majd egyesíti az MD4 kivonatoló és a egy felhasználó Salt érték, és bemenetekben be azt a [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt) függvény. 1000 ismétlésének a [HMAC-SHA256](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) kulcsalgoritmus kivonatoló algoritmust használ. 
7.  A jelszó Jelszókivonat-szinkronizálási ügynök vesz igénybe az eredményül kapott 32 bájtos kivonat, mindkettő fűz össze a felhasználói Salt érték és az SHA256 számának ismétlések rá (használata az Azure AD), majd továbbítja a karakterláncot, az Azure AD Connect, az Azure ad-hez SSL-en keresztül.</br> 
8.  Ha egy felhasználó megpróbál bejelentkezni az Azure ad-hez, és beírja a jelszavát, a jelszó útján az azonos MD4 + védőérték + PBKDF2 + HMAC-SHA256 folyamat fut le. Az eredményül kapott ujjlenyomat megegyezik az Azure AD-ben tárolt kivonat, ha a felhasználó beírta a helyes jelszót, és van hitelesítve. 

>[!Note] 
>Az eredeti MD4 kivonatoló az Azure AD nem lesznek továbbítva. Ehelyett továbbítása az eredeti MD4 kivonatoló SHA256-kivonatát. Ennek eredményeképpen az Azure AD-ben tárolt hash nyerik, ha azt nem használható a helyszínen a pass-the-hash típusú támadás.

### <a name="security-considerations"></a>Biztonsági szempontok
Amikor jelszó-szinkronizálás, a jelszót egyszerű szöveges verziójában nem lesz közzétéve a jelszó kivonatoló szinkronizálási szolgáltatás az Azure AD vagy bármely olyan társított szolgáltatásokat.

Felhasználói hitelesítés történik, Azure AD-val, nem pedig a szervezet saját Active Directory-példányból ellen. Az SHA256-jelszó az Azure ad-ben – tárolt adatok az eredeti MD4 kivonatoló--kivonatát a biztonságosabb, mint az Active Directoryban tárolja. További az SHA256-kivonat nem lehet visszafejteni, mert azt nem kell a szervezet Active Directory-környezetet állapotba és mutatják be, a pass-the-hash típusú támadások érvényes felhasználói jelszó.

### <a name="password-policy-considerations"></a>Jelszó házirenddel kapcsolatos megfontolások
Jelszókivonat-szinkronizálás engedélyezése által érintett jelszóházirendek két típusa van:

* Jelszó bonyolultsága házirend
* Jelszó-elévülési szabályzatának

#### <a name="password-complexity-policy"></a>Jelszó bonyolultsága házirend  
Ha a Jelszókivonat-szinkronizálás engedélyezve van, a jelszó összetettségi szabályzatok a helyszíni Active Directory-példányában felülbírálása összetettséget házirendek a szinkronizált felhasználók számára a felhőben. Használhatja az összes érvényes jelszavak a helyszíni Active Directory-példánynak az Azure AD-szolgáltatások eléréséhez.

> [!NOTE]
> Közvetlenül a felhőben létrehozott felhasználó jelszavát vonatkozik továbbra is jelszóházirendet a felhőben meghatározottak szerint.

#### <a name="password-expiration-policy"></a>Jelszó-elévülési szabályzatának  
Ha egy felhasználó a Jelszókivonat-szinkronizálás hatókörébe esik, a felhő-fiók jelszavát értéke *soha ne járjon le*.

Továbbra is a helyszíni környezetben lejárt szinkronizált jelszó használatával jelentkezzen be a cloud services. A felhő jelszó módosítja a jelszavát a helyszíni környezetben a következő alkalommal frissül.

#### <a name="account-expiration"></a>Fiók lejárati
Ha a szervezet a accountExpires attribútum felhasználóifiók-kezelés részeként használja, ennek az attribútumnak nincs szinkronizálva az Azure ad-hez. Ennek eredményeképpen egy lejárt Active Directory-fiókot konfigurálni a Jelszókivonat-szinkronizálás környezetben továbbra is aktív lesz az Azure ad-ben. Azt javasoljuk, hogy ha a fiók lejárt, egy munkafolyamat-művelet indíthat el egy PowerShell-parancsprogram, amely letiltja a felhasználó Azure AD-fiókot (használja a [Set-AzureADUser](https://docs.microsoft.com/powershell/module/azuread/set-azureaduser?view=azureadps-2.0) parancsmag). Ezzel szemben ha a fiók be van kapcsolva, az Azure AD-példányt kell bekapcsolni.

### <a name="overwrite-synchronized-passwords"></a>Szinkronizált jelszavak felülírása
A rendszergazda Windows PowerShell használatával manuálisan alaphelyzetbe állíthassa jelszavát.

Ebben az esetben az új jelszó felülírja a szinkronizált jelszavát, és a felhőben definiált összes jelszóházirendet érvényesek az új jelszót.

Ha módosítja a helyszíni jelszót újra, az új jelszó szinkronizálása a felhőbe, és ez a beállítás felülbírálja a manuálisan frissített jelszót.

A jelszó szinkronizálása nem befolyásolja a az Azure-felhasználó, aki be van jelentkezve. Az aktuális munkamenet a felhőalapú szolgáltatás azonnal nem érinti a szinkronizált jelszómódosítás, amíg egy felhőszolgáltatáshoz van bejelentkezve. KMSI kibővíti ezt a különbséget időtartamára. Ha a felhőszolgáltatás újra-hitelesítést igényel, meg kell adnia az új jelszót.

### <a name="additional-advantages"></a>További előnyök

- Általában a Jelszókivonat-szinkronizálás megvalósítása egyszerűbb, mint egy összevonási szolgáltatás. Nincs szükség semmilyen további kiszolgálókat, és kiküszöböli a felhasználók hitelesítése a magas rendelkezésre állású összevonási szolgáltatás függés.
- A Jelszókivonat-szinkronizálás összevonási mellett is engedélyezhető. Azt is használható tartalékként Ha az összevonási szolgáltatás szolgáltatáskimaradás következik be.

## <a name="password-hash-sync-process-for-azure-ad-domain-services"></a>Jelszó-kivonat szinkronizálási folyamata Azure AD Domain Services

Ha a Azure AD Domain Services segítségével örökölt hitelesítést biztosít olyan alkalmazásokhoz és szolgáltatásokhoz, amelyeknek Keberos, LDAP vagy NTLM protokollt kell használniuk, néhány további folyamat a jelszó-kivonat szinkronizációs folyamatának részét képezi. Azure AD Connect a következő folyamattal szinkronizálja a jelszó-kivonatokat az Azure AD-be a Azure AD Domain Servicesban való használathoz:

> [!IMPORTANT]
> Azure AD Connect csak a régi jelszavakat szinkronizálja, amikor engedélyezi az Azure AD DSt az Azure AD-bérlő számára. A következő lépések nem használhatók, ha a helyszíni AD DS-környezet Azure AD-vel való szinkronizálásához csak Azure AD Connect használ.
>
> Ha az örökölt alkalmazásai nem használnak NTLM-hitelesítést vagy LDAP egyszerű kötéseket, javasoljuk, hogy tiltsa le az NTLM-jelszó kivonatának szinkronizálását az Azure AD DS. További információ: a [gyenge titkosítási csomagok letiltása és az NTLM hitelesítő adatok kivonatának szinkronizálása](../../active-directory-domain-services/secure-your-domain.md).

1. Azure AD Connect lekéri a bérlő Azure AD Domain Services példányának nyilvános kulcsát.
1. Amikor egy felhasználó megváltoztatja a jelszavát, a helyszíni tartományvezérlő két attribútumban tárolja a jelszó-módosítás (kivonatok) eredményét:
    * *unicodePwd* az NTLM-jelszó kivonatához.
    * *supplementalCredentials* a Kerberos-jelszó kivonatához.
1. Azure AD Connect észleli a jelszó-módosításokat a címtár-replikációs csatornán keresztül (az attribútum módosításait más tartományvezérlőkre kell replikálni).
1. A Azure AD Connect a következő lépéseket hajtja végre minden olyan felhasználónál, akinek a jelszava módosult:
    * Véletlenszerűen generált AES 256-bites szimmetrikus kulcsot hoz létre.
    * Létrehoz egy véletlenszerű inicializálási vektort, amely az első kör titkosításához szükséges.
    * A *supplementalCredentials* attribútumaiból Kinyeri a Kerberos-jelszó kivonatait.
    * Ellenőrzi a Azure AD Domain Services biztonsági konfiguráció *SyncNtlmPasswords* -beállítását.
        * Ha ez a beállítás le van tiltva, a létrehoz egy véletlenszerű, nagy entrópia-alapú NTLM-kivonatot (amely eltér a felhasználó jelszavával). Ezt a kivonatot ezután a *supplementalCrendetials* attribútum által a pontosan megadott Kerberos-jelszó kivonatokkal kombinálva egyetlen adatstruktúrába.
        * Ha engedélyezve van, a a *unicodePwd* attribútum értékét kombinálja a kinyert Kerberos-jelszó kivonatokkal a *supplementalCredentials* attribútumból egy adatstruktúrába.
    * Titkosítja az egyetlen adatszerkezetet az AES szimmetrikus kulcs használatával.
    * Titkosítja az AES szimmetrikus kulcsot a bérlő Azure AD Domain Services nyilvános kulcsa alapján.
1. Azure AD Connect továbbítja a titkosított AES szimmetrikus kulcsot, a jelszó-kivonatokat tartalmazó titkosított adatstruktúrát, valamint az inicializálási vektort az Azure AD-nek.
1. Az Azure AD tárolja a titkosított AES szimmetrikus kulcsot, a titkosított adatstruktúrát és a felhasználó inicializálási vektorát.
1. Az Azure AD leküldi a titkosított AES szimmetrikus kulcsot, a titkosított adatstruktúrát és az inicializálási vektort egy belső szinkronizálási mechanizmus használatával, amely egy titkosított HTTP-munkameneten keresztül Azure AD Domain Services.
1. Azure AD Domain Services lekéri a bérlő példányának titkos kulcsát az Azure Key vaultból.
1. Az egyes titkosított adatkészletek esetében (amely az egyetlen felhasználó jelszavainak módosítását jelenti) Azure AD Domain Services ezután végrehajtja a következő lépéseket:
    * A titkos kulcsát használja az AES szimmetrikus kulcs visszafejtéséhez.
    * Az AES szimmetrikus kulcsot használja az inicializálási vektor használatával a jelszó-kivonatokat tartalmazó titkosított adatstruktúra visszafejtéséhez.
    * A Kerberos-jelszó kivonatait írja a Azure AD Domain Services tartományvezérlőnek. A rendszer menti a kivonatokat a felhasználói objektum *supplementalCredentials* attribútumában, amely a Azure ad Domain Services tartományvezérlő nyilvános kulcsára van titkosítva.
    * Azure AD Domain Services írja be a Azure AD Domain Services tartományvezérlőnek fogadott NTLM-jelszó kivonatát. A rendszer menti a kivonatot a felhasználói objektum *unicodePwd* attribútumában, amely a Azure ad Domain Services tartományvezérlő nyilvános kulcsához van titkosítva.

## <a name="enable-password-hash-synchronization"></a>Jelszókivonat-szinkronizálás engedélyezése

>[!IMPORTANT]
>A Jelszókivonat-szinkronizálást az AD FS (vagy más összevonási technológiákkal) telepít át, ha kifejezetten ajánljuk, hogy kövesse a részletes üzembe helyezési útmutató, közzétett [Itt](https://aka.ms/adfstophsdpdownload).

Amikor telepíti az Azure AD Connect használatával a **Gyorsbeállítások** , a Jelszókivonat-szinkronizálás automatikusan engedélyezve van. További információkért lásd: [Ismerkedés az Azure AD Connecttel a gyorsbeállítások használatával](how-to-connect-install-express.md).

Ha az Azure AD Connect telepítése egyéni beállításokat használja, ha a felhasználó bejelentkezési oldalán a Jelszókivonat-szinkronizálás érhető el. További információkért lásd: [az Azure AD Connect egyéni telepítési](how-to-connect-install-custom.md).

![Jelszókivonat-szinkronizálás engedélyezése](./media/how-to-connect-password-hash-synchronization/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>A Jelszókivonat-szinkronizálás és a FIPS
A kiszolgáló zárolva lett megfelelően Federal információk Processing Standard (FIPS), ha MD5-tel is le van tiltva.

**A Jelszókivonat-szinkronizálás MD5 engedélyezéséhez hajtsa végre az alábbi lépéseket:**

1. Nyissa meg az AD %programfiles%\Azure Sync\Bin.
2. Open miiserver.exe.config.
3. Nyissa meg a fájl végén a konfiguráció-és futásidő-csomópont.
4. Adja hozzá a következő csomópont: `<enforceFIPSPolicy enabled="false"/>`
5. Mentse a módosításokat.

Ez a kódrészlet hivatkozásként, hogy milyen hasonlóan kell kinéznie:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

További információ a biztonsági és FIPS: [Jelszókivonat-szinkronizálás Azure ad-ben, a titkosítás és a FIPS megfelelőségi](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/).

## <a name="troubleshoot-password-hash-synchronization"></a>A Jelszókivonat-szinkronizálás hibaelhárítása
Ha problémába ütközik a Jelszókivonat-szinkronizálás, [Jelszókivonat-szinkronizálás hibaelhárítása](tshoot-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>További lépések
* [Az Azure AD Connect szinkronizálása: Szinkronizálási beállítások testreszabása](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
* [A részletes telepítési terv beolvasása az AD FS-ről történő Jelszókivonat-szinkronizálás](https://aka.ms/authenticationDeploymentPlan)

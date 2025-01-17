---
title: Azure Active Directory Auth – Azure SQL | Microsoft Docs
description: Ismerje meg, hogyan használható a Azure Active Directory a hitelesítéshez SQL Database, felügyelt példánnyal és SQL Data Warehouse
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 02/20/2019
ms.openlocfilehash: b99dbd403de0de948527fbe74b7e1205316822c0
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019679"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-sql"></a>Azure Active Directory hitelesítés használata SQL-alapú hitelesítéshez

Azure Active Directory a hitelesítés az Azure-SQL Databasehoz, [](sql-database-technical-overview.md)a [felügyelt példányokhoz](sql-database-managed-instance.md)és a [SQL Data Warehousehoz](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) való kapcsolódás egyik mechanizmusa Azure Active Directory (Azure ad) identitások használatával. 

> [!NOTE]
> Ez a témakör az Azure SQL Server-kiszolgálókra, valamint az Azure SQL Serveren létrehozott SQL Database- és SQL Data Warehouse-adatbázisokra vonatkozik. Az egyszerűség kedvéért a jelen témakörben az SQL Database és az SQL Data Warehouse megnevezése egyaránt SQL Database.

Az Azure Active Directory-hitelesítéssel lehetővé teszi adatbázis-felhasználók és más Microsoft-szolgáltatások identitásainak központi kezelését. A központi AZONOSÍTÓk kezelése egyetlen helyet biztosít az adatbázis-felhasználók felügyeletéhez, és egyszerűsíti az engedélyek kezelését. Ez az alábbi előnyökkel jár:

- Alternatívát biztosít a SQL Server hitelesítéshez.
- Segít leállítani a felhasználói identitások elterjedését az adatbázis-kiszolgálókon.
- Lehetővé teszi a jelszó elforgatását egyetlen helyen.
- Az ügyfelek külső (Azure AD-) csoportok használatával kezelhetik az adatbázis-engedélyeket.
- Az integrált Windows-hitelesítés és a Azure Active Directory által támogatott hitelesítés más formáinak engedélyezésével megtörölheti a jelszavak tárolását.
- Az Azure AD-hitelesítés a tárolt adatbázis-felhasználók használatával hitelesíti az identitásokat az adatbázis szintjén.
- Az Azure AD a SQL Databasehoz csatlakozó alkalmazások jogkivonat-alapú hitelesítését támogatja.
- Az Azure AD-hitelesítés támogatja az ADFS (tartományi összevonás) vagy a natív felhasználói/jelszavas hitelesítést egy helyi Azure Active Directory tartományi szinkronizálás nélkül.
- Az Azure AD támogatja az SQL Server Management Studióból érkező azon kapcsolatokat, amelyek az Active Directory univerzális hitelesítést használják, amelynek része a többtényezős hitelesítés (MFA) is.  A számos egyszerű ellenőrzési lehetőségnek (telefonhívás, SMS, intelligens kártya PIN-kóddal vagy mobilalkalmazásos értesítés) köszönhetően az MFA erős hitelesítést kínál. További információért tekintse meg az [Azure AD MFA és az SQL Database, valamint az SQL Data Warehouse együttes SSMS-támogatását](sql-database-ssms-mfa-authentication.md) ismertető cikket.
- Az Azure AD támogatja az SQL Server Data Toolsból (SSDT) érkező, az Active Directory interaktív hitelesítést használó hasonló kapcsolatokat. További információért tekintse meg [az Azure Active Directory SQL Server Data Tools (SSDT) általi támogatását](/sql/ssdt/azure-active-directory) ismertető cikket.

> [!NOTE]  
> Az Azure-beli virtuális gépen futó SQL Serverhoz való csatlakozás Azure Active Directory-fiók használata esetén nem támogatott. Ehelyett használjon tartományi Active Directory fiókot.  

A konfigurációs lépések a következő eljárásokat ismertetik Azure Active Directory hitelesítés konfigurálásához és használatához.

1. Hozza létre és töltse fel az Azure AD-t.
2. Nem kötelező: Társítsa vagy módosítsa az Azure-előfizetéshez jelenleg társított Active Directoryt.
3. Hozzon létre egy Azure Active Directory rendszergazdát a Azure SQL Database-kiszolgálóhoz, a felügyelt [](https://azure.microsoft.com/services/sql-data-warehouse/)példányhoz vagy a Azure SQL Data Warehousehoz.
4. Konfigurálja az ügyfélszámítógépeket.
5. Hozzon létre tárolt adatbázis-felhasználókat az adatbázisban az Azure AD-identitásokhoz rendelve.
6. Kapcsolódjon az adatbázishoz az Azure AD-identitások használatával.

> [!NOTE]
> Az Azure ad létrehozásával és feltöltésével, majd az Azure AD Azure SQL Database, felügyelt példánnyal és SQL Data Warehousesal való konfigurálásával kapcsolatban lásd: [Az Azure ad konfigurálása a Azure SQL Database](sql-database-aad-authentication-configure.md)használatával.

## <a name="trust-architecture"></a>Megbízhatósági architektúra

A következő magas szintű diagram összefoglalja az Azure AD-hitelesítés Azure SQL Database használatával történő használatának megoldási architektúráját. Ugyanezek a fogalmak érvényesek SQL Data Warehousera. Az Azure AD natív felhasználói jelszavának támogatásához csak a felhő részét és az Azure AD-t/Azure SQL Database kell figyelembe venni. Az összevont hitelesítés (vagy a Windows rendszerbeli hitelesítő adatok felhasználó/jelszó) támogatásához az ADFS-blokktal folytatott kommunikáció szükséges. A nyilak a kommunikációs útvonalakat jelzik.

![HRE-hitelesítési diagram][1]

A következő ábra azokat az összevonási, megbízhatósági és üzemeltetési kapcsolatokat mutatja be, amelyek lehetővé teszik az ügyfél számára, hogy jogkivonat elküldésével csatlakozhasson egy adatbázishoz. A tokent egy Azure AD hitelesíti, és az adatbázis megbízhatónak tartja. Az 1. ügyfél a natív felhasználókkal vagy egy összevont felhasználókkal rendelkező Azure AD-val rendelkező Azure Active Directoryt jelenthet. A 2. ügyfél egy lehetséges megoldást jelent, beleértve az importált felhasználókat is; Ebben a példában egy összevont Azure Active Directory, amellyel az ADFS szinkronizálva van Azure Active Directoryval. Fontos tisztában lenni azzal, hogy az Azure AD-hitelesítést használó adatbázisokhoz való hozzáféréshez szükséges, hogy az üzemeltetési előfizetés az Azure AD-hez legyen társítva. Ugyanezt az előfizetést kell használni a Azure SQL Databaset vagy SQL Data Warehouset üzemeltető SQL Server létrehozásához.

![előfizetés kapcsolata][2]

## <a name="administrator-structure"></a>Rendszergazdai struktúra

Az Azure AD-hitelesítés használatakor két rendszergazdai fiók van a SQL Database-kiszolgálóhoz és a felügyelt példányhoz. az eredeti SQL Server rendszergazdája és az Azure AD rendszergazdája. Ugyanezek a fogalmak érvényesek SQL Data Warehousera. Csak az Azure AD-fiókon alapuló rendszergazda hozhatja létre az első Azure AD-beli adatbázis-felhasználót egy felhasználói adatbázisban. Az Azure AD rendszergazdai bejelentkezés egy Azure AD-felhasználó vagy egy Azure AD-csoport lehet. Ha a rendszergazda egy csoportfiók, bármely csoporttag használhatja, így több Azure AD-rendszergazda is engedélyezhető a SQL Server példányhoz. A csoportfiókok rendszergazdaként való használata javítja a kezelhetőséget azáltal, hogy lehetővé teszi a csoporttagok központi hozzáadását és eltávolítását az Azure AD-ben anélkül, hogy módosítani kellene a felhasználókat vagy az engedélyeket a SQL Databaseban. Egyszerre csak egy Azure AD-rendszergazda (felhasználó vagy csoport) konfigurálható.

![felügyeleti struktúra][3]

## <a name="permissions"></a>Engedélyek

Új felhasználók létrehozásához `ALTER ANY USER` engedéllyel kell rendelkeznie az adatbázisban. Az `ALTER ANY USER` engedélyek bármelyik adatbázis-felhasználó számára megadhatók. Az `ALTER ANY USER` engedélyt a kiszolgálói rendszergazdai fiókok, valamint az `CONTROL ON DATABASE` adatbázis-felhasználók vagy `ALTER ON DATABASE` az adatbázis-szerepkör tagjai `db_owner` is megtartják.

A Azure SQL Database, felügyelt példányban vagy SQL Data Warehouseban tárolt adatbázis-felhasználó létrehozásához az Azure AD-identitás használatával csatlakoznia kell az adatbázishoz vagy a példányhoz. Az első tárolt adatbázis-felhasználó létrehozásához az adatbázishoz kell csatlakoznia egy Azure AD-rendszergazda használatával (aki az adatbázis tulajdonosa). Ez a [Azure Active Directory hitelesítés konfigurálása és kezelése SQL Database vagy SQL Data Warehouse](sql-database-aad-authentication-configure.md)segítségével. Az Azure AD-hitelesítés csak akkor lehetséges, ha az Azure AD-rendszergazda Azure SQL Database vagy SQL Data Warehouse kiszolgálóhoz lett létrehozva. Ha a Azure Active Directory-rendszergazda el lett távolítva a kiszolgálóról, a SQL Server korábban létrehozott Azure Active Directory felhasználók már nem tudnak csatlakozni az adatbázishoz a Azure Active Directory hitelesítő adataik használatával.

## <a name="azure-ad-features-and-limitations"></a>Azure AD-funkciók és korlátozások

- A következő Azure AD-tagok üzembe helyezhetők az Azure SQL Serverben vagy SQL Data Warehouseban:

  - Natív tagok: Az Azure AD-ben létrehozott tag a felügyelt tartományban vagy egy ügyfél tartományában. További információ: [saját tartománynév hozzáadása az Azure ad](../active-directory/active-directory-domains-add-azure-portal.md)-hez.
  - Összevont tartomány tagjai: Egy összevont tartománnyal létrehozott tag az Azure AD-ben. További információ: [Microsoft Azure mostantól támogatja a Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)való összevonást.
  - A többi Azure AD-ből származó, natív vagy összevont tartományi tagokból importált tagok.
  - Active Directory biztonsági csoportként létrehozott csoportokat.

- A kiszolgálói szerepkörrel rendelkező `db_owner` csoportok részét képező Azure ad-felhasználók nem használhatják az adatbázis-hatókörrel rendelkező hitelesítő **[adatok létrehozása](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)** Azure SQL Database és Azure SQL Data Warehouse. A következő hibaüzenet jelenik meg:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Adja meg `db_owner` a szerepkört közvetlenül az egyes Azure ad-felhasználók számára az **adatbázis-hatókörrel rendelkező hitelesítő adatok létrehozásakor** felmerülő problémák enyhítése érdekében.

- Ezek a rendszerfunkciók NULL értéket adnak vissza, amikor az Azure AD-rendszerbiztonsági tag alatt hajtja végre:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

### <a name="managed-instances"></a>Felügyelt példányok

- Az Azure AD-kiszolgálói rendszerbiztonsági tag (bejelentkezések) és a felhasználók előzetes verziójú szolgáltatásként támogatottak a [felügyelt példányok](sql-database-managed-instance.md)számára.
- A felügyelt példányok nem támogatják az olyan Azure ad-kiszolgálói rendszerbiztonsági azonosítók (bejelentkezések) beállítását [](sql-database-managed-instance.md), amelyek adatbázis-tulajdonosként vannak leképezve.
    - Ennek az az előnye, hogy ha egy csoportot a `dbcreator` kiszolgálói szerepkör részeként vesznek fel, a csoportba tartozó felhasználók csatlakozhatnak a felügyelt példányhoz, és új adatbázisokat hozhatnak létre, de nem fognak tudni hozzáférni az adatbázishoz. Ennek az az oka, hogy az új adatbázis-tulajdonos a SA, nem pedig az Azure AD-felhasználó. Ez a probléma nem jelenik meg, ha az adott felhasználó hozzá van `dbcreator` adva a kiszolgálói szerepkörhöz.
- Az SQL-ügynök kezelése és a feladatok végrehajtása az Azure AD Server-rendszerbiztonsági tag (bejelentkezések) esetében támogatott.
- Az adatbázis biztonsági mentési és visszaállítási műveleteit az Azure AD-kiszolgáló résztvevői (bejelentkezések) hajtják végre.
- Az Azure AD-kiszolgáló rendszertagjaihoz (bejelentkezésekhez) és a hitelesítési eseményekhez kapcsolódó összes utasítás naplózása támogatott.
- A rendszergazdai szerepkörrel rendelkező Azure AD-kiszolgálói rendszerbiztonsági tagok (bejelentkezések) dedikált rendszergazdai kapcsolatai támogatottak.
    - Támogatott a SQLCMD segédprogram és a SQL Server Management Studio használatával.
- A bejelentkezési eseményindítók az Azure AD Server-rendszerbiztonsági tag (bejelentkezések) felől érkező bejelentkezési események esetén támogatottak.
- Az Service Broker-és adatbázis-levelezést egy Azure AD-kiszolgáló rendszerbiztonsági tag (login) használatával lehet beállítani.


## <a name="connecting-using-azure-ad-identities"></a>Csatlakozás az Azure AD-identitások használatával

Azure Active Directory hitelesítés a következő módszereket támogatja az adatbázishoz való kapcsolódáshoz az Azure AD-identitások használatával:

- Integrált Windows-hitelesítés használata
- Azure AD egyszerű név és jelszó használata
- Alkalmazás-jogkivonat hitelesítésének használata

Az Azure AD Server-rendszerbiztonsági tag (Logins) (**nyilvános előzetes**verzió) esetében az alábbi hitelesítési módszerek támogatottak:

- Azure Active Directory jelszó
- Integrált Azure Active Directory
- Univerzális Azure Active Directory MFA-val
- Interaktív Azure Active Directory


### <a name="additional-considerations"></a>Néhány fontos megjegyzés

- A kezelhetőség növelése érdekében javasoljuk, hogy hozzon létre egy dedikált Azure AD-csoportot rendszergazdaként.   
- Egyszerre csak egy Azure AD-rendszergazda (felhasználó vagy csoport) konfigurálható Azure SQL Database-kiszolgálóhoz vagy Azure SQL Data Warehousehoz.
  - A felügyelt példányokhoz (**nyilvános előzetes**verzió) tartozó Azure ad Server-rendszerbiztonsági tag (Logins) hozzáadása lehetővé teszi több Azure ad-kiszolgálói rendszerbiztonsági tag (bejelentkezés) létrehozását, amelyek hozzáadhatók a `sysadmin` szerepkörhöz.
- SQL Server csak egy Azure AD-rendszergazda csatlakozhat a Azure SQL Database kiszolgálóhoz, a felügyelt példányhoz vagy a Azure SQL Data Warehousehoz Azure Active Directory fiók használatával. A Active Directory rendszergazda konfigurálhatja a következő Azure AD-adatbázis felhasználóit.   
- Javasoljuk, hogy a kapcsolat időtúllépését 30 másodpercre állítsa be.   
- SQL Server 2016 Management Studio és SQL Server Data Tools a Visual Studio 2015 (14.0.60311.1 április 2016-ös vagy újabb verziója) támogatja a Azure Active Directory hitelesítést. (Az Azure AD-hitelesítést a **.NET-keretrendszer SQLServer**-adatszolgáltatója támogatja; legalább a .NET-keretrendszer 4,6-es verziója). Ezért az eszközök és az adatrétegbeli alkalmazások legújabb verziói (DAC és. A BACPAC) az Azure AD-hitelesítést is használhatja.   
- A 15.0.1 verziótól kezdődően a [Sqlcmd segédprogram](/sql/tools/sqlcmd-utility) és a [BCP segédprogram](/sql/tools/bcp-utility) támogatja a többtényezős hitelesítés (MFA) Active Directoryét.
- A Visual Studio 2015 SQL Server Data Tools használatához legalább az Adateszközök (14.0.60311.1-verzió) április 2016-es verziója szükséges. Az Azure AD-felhasználók jelenleg nem jelennek meg a SSDT Object Explorerban. Megkerülő megoldásként tekintse meg a [sys. database_principals](https://msdn.microsoft.com/library/ms187328.aspx)felhasználóit.   
- [A SQL Server rendszerhez készült Microsoft JDBC-6,0 illesztőprogram támogatja az](https://www.microsoft.com/download/details.aspx?id=11774) Azure ad-hitelesítést. Lásd még: [a kapcsolatok tulajdonságainak beállítása](https://msdn.microsoft.com/library/ms378988.aspx).   
- Az Azure AD-hitelesítéssel nem végezhető el a hitelesítés.   
- Az Azure AD-hitelesítést a Azure Portal **importálási adatbázis** és az adatbázis-lapok **exportálása** SQL Database támogatja. Az Azure AD-hitelesítéssel történő Importálás és exportálás is támogatott a PowerShell-parancsban.   
- Az Azure AD-hitelesítés SQL Database, felügyelt példányok és SQL Data Warehouse parancssori felület használatával támogatott. További információ: [Azure Active Directory hitelesítés konfigurálása és kezelése SQL Database vagy SQL Data Warehouse](sql-database-aad-authentication-configure.md) és [SQL Server – az SQL Server](https://docs.microsoft.com/cli/azure/sql/server)segítségével.

## <a name="next-steps"></a>További lépések

- Ha szeretné megtudni, hogyan hozhat létre és tölthet fel Azure AD-t, és hogyan konfigurálhatja az Azure AD-t Azure SQL Database vagy Azure SQL Data Warehousesal, tekintse meg a [Azure Active Directory hitelesítés konfigurálása és kezelése SQL Database, felügyelt példány vagy SQL Data Warehouse](sql-database-aad-authentication-configure.md)használatával című témakört.
- Az Azure AD-kiszolgálói rendszerbiztonsági tag (bejelentkezések) felügyelt példányokkal való használatával kapcsolatos oktatóanyagért lásd: az [Azure ad Server rendszerbiztonsági tagjai (bejelentkezések) felügyelt példányokkal](sql-database-managed-instance-aad-security-tutorial.md)
- Az SQL Database hozzáféréseinek és felügyeletének áttekintéséről az [SQL Database-hozzáférés és -felügyelet](sql-database-control-access.md) részben olvashat.
- Az SQL Database bejelentkezéseinek, felhasználóinak és adatbázis-szerepköreinek áttekintését a [Bejelentkezések, felhasználók és adatbázis-szerepkörök](sql-database-manage-logins.md) részben találja.
- További információ az adatbázis résztvevőivel kapcsolatban: [Résztvevők](https://msdn.microsoft.com/library/ms181127.aspx).
- További információ az adatbázis-szerepkörökkel kapcsolatban: [Adatbázis-szerepkörök](https://msdn.microsoft.com/library/ms189121.aspx).
- A felügyelt példányokhoz tartozó Azure AD-kiszolgálói rendszerbiztonsági tag (bejelentkezések) létrehozásával kapcsolatos szintaxisért lásd: [create login (bejelentkezés létrehozása](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)).
- További információ az SQL Database tűzfalszabályaival kapcsolatban: [SQL Database tűzfalszabályok](sql-database-firewall-configure.md).

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[9]: ./media/sql-database-aad-authentication/9ad-settings.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/11connect-using-int-auth.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db.png

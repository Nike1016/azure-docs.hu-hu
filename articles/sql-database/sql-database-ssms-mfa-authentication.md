---
title: Multi-Factor HRE hitelesítés használata a Azure SQL Database és a Azure SQL Data Warehouse használatával | Microsoft Docs
description: Azure SQL Database és Azure SQL Data Warehouse támogatja a SQL Server Management Studio (SSMS) kapcsolatait Active Directory univerzális hitelesítés használatával.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/08/2018
ms.openlocfilehash: 7add55380f2f7b3ef70db0603fe2c26127db8a78
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566442"
---
# <a name="using-multi-factor-aad-authentication-with-azure-sql-database-and-azure-sql-data-warehouse-ssms-support-for-mfa"></a>A multi-Factor HRE hitelesítés használata a Azure SQL Database és a Azure SQL Data Warehouse használatával (az MFA támogatásának SSMS)
Azure SQL Database és Azure SQL Data Warehouse támogatja a SQL Server Management Studio (SSMS) kapcsolatait *Active Directory univerzális hitelesítés*használatával. Ez a cikk a különböző hitelesítési lehetőségek közötti különbségeket ismerteti, valamint az univerzális hitelesítéssel kapcsolatos korlátozásokat is. 

**Töltse le a legújabb SSMS** -t az ügyfélszámítógépen, töltse le a SSMS legújabb verzióját a [Download SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)webhelyről. 


Az ebben a cikkben tárgyalt összes szolgáltatáshoz használja a 17,2-es vagy újabb 2017-es verziót.  A legutóbbi kapcsolódás párbeszédpanelnek az alábbi képhez hasonlóan kell kinéznie:
 
  ![1mfa – univerzális – kapcsolat](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png "Befejezi a Felhasználónév mezőt.")  

## <a name="the-five-authentication-options"></a>Az öt hitelesítési lehetőség  

Active Directory univerzális hitelesítés támogatja a két nem interaktív hitelesítési módszert:
    - `Active Directory - Password`hitelesítés
    - `Active Directory - Integrated`hitelesítés

Két nem interaktív hitelesítési modell is létezik, amelyek számos különböző alkalmazásban (ADO.NET, JDCB, ODC stb.) is használhatók. Ez a két módszer soha nem eredményez előugró párbeszédpanelt: 
- `Active Directory - Password` 
- `Active Directory - Integrated` 

Az interaktív módszer az, hogy az Azure multi-Factor Authentication (MFA) is támogatja a következőt: 
- `Active Directory - Universal with MFA` 


Az Azure MFA segíti az adatok és alkalmazások védelmét az illetéktelen hozzáférésekkel szemben, miközben a felhasználói igényeknek megfelelő, egyszerű bejelentkezési folyamat használatát teszi lehetővé. Erős hitelesítést biztosít számos egyszerű ellenőrzési lehetőséggel (telefonhívás, szöveges üzenet, PIN-kóddal ellátott intelligens kártyák vagy mobil alkalmazások értesítése), így a felhasználók kiválaszthatják a kívánt módszert. Az interaktív MFA az Azure AD-vel az ellenőrzés előugró párbeszédpanelét eredményezheti.

A többtényezős hitelesítés leírását lásd: többtényezős [hitelesítés](../active-directory/authentication/multi-factor-authentication.md).
A konfigurációs lépésekért lásd: [Azure SQL Database multi-Factor Authentication konfigurálása SQL Server Management studiohoz](sql-database-ssms-mfa-authentication-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD-tartománynév vagy bérlői azonosító paraméter   

A [SSMS 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)-es verziójától kezdve a felhasználók, akik az aktuális Active Directoryba importálnak a vendég felhasználóként más Azure Active Directory-címtárból, megadhatják az Azure ad-tartománynevet vagy a bérlő azonosítóját a csatlakozáskor. A vendég felhasználók a más Azure-hirdetéseket, például a outlook.com, a hotmail.com, a live.com vagy más, például a gmail.com fiókokat meghívó felhasználókat is tartalmazhatnak. Ez az információ lehetővé teszi, hogy az **MFA-hitelesítéssel rendelkező Active Directory univerzálisan** azonosítsa a megfelelő hitelesítő szolgáltatót. Ez a beállítás a Microsoft-fiókok (MSA), például a outlook.com, a hotmail.com, a live.com és a nem MSA fiókok támogatásához is szükséges. Az univerzális hitelesítés használatával hitelesíteni kívánt felhasználóknak meg kell adniuk az Azure AD-tartománynevet vagy a bérlői azonosítót. Ez a paraméter az aktuális Azure AD-tartománynevet/bérlői azonosítót jelöli, amelyhez az Azure-kiszolgáló társítva van. Ha például az Azure Server társítva van az Azure ad- `contosotest.onmicrosoft.com` tartományhoz `joe@contosodev.onmicrosoft.com` , ahol a felhasználó az Azure ad-tartományból `contosodev.onmicrosoft.com`importált felhasználóként fut, a felhasználó `contosotest.onmicrosoft.com`hitelesítéséhez szükséges tartománynév. Ha a felhasználó az Azure AD-hez társított natív felhasználó, és nem MSA-fiók, nincs szükség tartománynévre vagy bérlői AZONOSÍTÓra. A (17,2-es SSMS-es verziótól kezdődő) paraméter megadásához a **Kapcsolódás** az adatbázishoz párbeszédpanelen végezze el a párbeszédpanelt, válassza a **Active Directory-Universal az MFA-** hitelesítéssel lehetőséget, majd kattintson a **Beállítások**elemre, és töltse ki a **felhasználónevet** mezőben, majd kattintson a **kapcsolatok tulajdonságai** fülre. Ellenőrizze az **ad-tartomány nevét vagy a bérlő azonosítóját** , és adja meg a hitelesítő hatóságot, például a tartománynevet (**contosotest.onmicrosoft.com**) vagy a bérlő azonosítójának GUID azonosítóját.  
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)   

### <a name="azure-ad-business-to-business-support"></a>Azure AD Business – üzleti támogatás   
Az Azure ad B2B-forgatókönyvek vendég felhasználói számára támogatott Azure AD-felhasználók (lásd: [Mi az az Azure B2B Collaboration](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)) tud csatlakozni a SQL Databasehoz, és SQL Data Warehouse csak az aktuális Azure ad-ben létrehozott csoport tagjainak részeként, és manuálisan a Transact-SQL használatával van leképezve `CREATE USER` utasítás egy adott adatbázisban. Ha `steve@gmail.com` például meghívja az Azure ad `contosotest` -t (az Azure ad-tartománnyal `contosotest.onmicrosoft.com`) `usergroup` , egy Azure ad-csoportot, például a `steve@gmail.com` tagot tartalmazó Azure ad-ben kell létrehoznia. Ezután ezt a csoportot egy Transact-SQL `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` -utasítás végrehajtásával létre kell hozni egy adott adatbázishoz (azaz MyDatabase) az Azure ad SQL-rendszergazda vagy az Azure ad dbo. Az adatbázis-felhasználó létrehozása után a felhasználó `steve@gmail.com` bejelentkezhet `MyDatabase` a SSMS-hitelesítési lehetőség `Active Directory – Universal with MFA support`használatára. Alapértelmezés szerint a csoporthoz csak a kapcsolódási engedély és minden további adathozzáférés szükséges, amelyet a szokásos módon kell megadni. Vegye figyelembe, `steve@gmail.com` hogy a felhasználónak, mint vendég felhasználónak be kell jelölnie a jelölőnégyzetet `contosotest.onmicrosoft.com` , és fel kell vennie az ad-tartománynevet a SSMS- **kapcsolatok tulajdonságai** párbeszédpanelen. Az **Active Directory Domain Name vagy a bérlői azonosító** lehetőség csak az univerzális és MFA-kapcsolatok esetén támogatott, ellenkező esetben szürkén jelenik meg.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>Az SQL Database és SQL Data Warehouse univerzális hitelesítési korlátai
- A SSMS és a SqlPackage. exe az egyetlen olyan eszköz, amely jelenleg engedélyezve van az MFA számára Active Directory univerzális hitelesítéssel.
- A SSMS 17,2-es verziója támogatja a többfelhasználós hozzáférést az MFA-val való univerzális hitelesítéssel. A 17,0-es és a 17,1-es verzióban a SSMS egy példányára való bejelentkezés korlátozott, egyetlen Azure Active Directory fiókra. Ha másik Azure AD-fiókkal szeretne bejelentkezni, a SSMS egy másik példányát kell használnia. (Ez a korlátozás Active Directory univerzális hitelesítésre korlátozódik, Active Directory jelszó-hitelesítéssel, Active Directory integrált hitelesítéssel vagy SQL Server hitelesítéssel is bejelentkezhet a különböző kiszolgálókra.
- A SSMS támogatja a Object Explorer, a lekérdezés-szerkesztő és a lekérdezés-áruház vizualizációjának Active Directory univerzális hitelesítését.
- A SSMS 17,2-es verziója a DacFx varázsló támogatását biztosítja az adatok exportálásához/kinyeréséhez/üzembe helyezéséhez. Ha egy adott felhasználó hitelesítése az univerzális hitelesítés használatával történik a kezdeti hitelesítési párbeszédpanelen, a DacFx varázsló ugyanúgy működik, mint az összes többi hitelesítési módszer.
- A SSMS Táblatervező nem támogatja az univerzális hitelesítést.
- Az univerzális hitelesítéshez nincs szükség további szoftverre Active Directory, kivéve, ha a SSMS támogatott verzióját kell használnia.  
- Az univerzális hitelesítés Active Directory-hitelesítési tár (ADAL) verziója frissítve lett a legújabb ADAL. dll 3.13.9 elérhető verzióra. Lásd: [Active Directory-hitelesítési tár 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  


## <a name="next-steps"></a>További lépések

- A konfigurációs lépésekért lásd: [Azure SQL Database multi-Factor Authentication konfigurálása SQL Server Management studiohoz](sql-database-ssms-mfa-authentication-configure.md).
- Mások hozzáférésének biztosítása az adatbázishoz: [SQL Database hitelesítés és engedélyezés: Hozzáférés biztosítása](sql-database-manage-logins.md)  
- Győződjön meg arról, hogy mások kapcsolódhatnak a tűzfalon keresztül: [Azure SQL Database kiszolgáló szintű tűzfalszabály konfigurálása a Azure Portal használatával](sql-database-configure-firewall-settings.md)  
- [Azure Active Directory-hitelesítés konfigurálása és kezelése az SQL Database vagy az SQL Data Warehouse használatával](sql-database-aad-authentication-configure.md)  
- [Microsoft SQL Server adatrétegbeli alkalmazás-keretrendszer (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage)  
- [BACPAC-fájl importálása új Azure SQL-adatbázisba](../sql-database/sql-database-import.md)  
- [Azure SQL-adatbázis exportálása BACPAC-fájlba](../sql-database/sql-database-export.md)  
- C#interfész [IUniversalAuthProvider felülete](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- A **Active Directory-Universal és az MFA-** hitelesítés használatakor a ADAL nyomkövetés a [SSMS 17,3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)-es verziójától kezdődően érhető el. Alapértelmezés szerint kikapcsolhatja a ADAL nyomkövetését az **eszközök**, beállítások menü, az **Azure-szolgáltatások**, az **Azure-felhő**, a **ADAL kimeneti ablak nyomkövetési szintje** **lehetőséggel** , majd a **kimenet** lehetőséget a **nézet** menüben engedélyezheti. A Nyomkövetések a kimeneti ablakban érhetők el **Azure Active Directory lehetőség**kiválasztásakor.  

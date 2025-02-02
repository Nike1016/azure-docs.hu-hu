---
title: Multi-Factor Authentication konfigurálása – Azure SQL | Microsoft Docs
description: Ismerje meg, hogyan használható a többtényezős hitelesítés a SSMS SQL Database és SQL Data Warehouse.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 09/25/2018
ms.openlocfilehash: 1bccfd8ac363b21053c45ed489e943a1b488f41f
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68566510"
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>Többtényezős hitelesítés konfigurálása SQL Server Management Studio és az Azure AD-hez

Ez a témakör bemutatja, hogyan használható Azure Active Directory multi-Factor Authentication (MFA) a SQL Server Management Studio használatával. Az Azure AD MFA használható a SSMS vagy a SqlPackage. exe Azure- [SQL Database](sql-database-technical-overview.md) és [SQL Data Warehousehoz](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)való csatlakoztatásakor. A többtényezős hitelesítés Azure SQL Database áttekintését lásd: az [univerzális hitelesítés SQL Database és SQL Data Warehouse (SSMS-támogatás az MFA-hoz)](sql-database-ssms-mfa-authentication.md).

> [!NOTE]
> Ez a témakör az Azure SQL Server-kiszolgálókra, valamint az Azure SQL Serveren létrehozott SQL Database- és SQL Data Warehouse-adatbázisokra vonatkozik. Az egyszerűség kedvéért a jelen témakörben az SQL Database és az SQL Data Warehouse megnevezése egyaránt SQL Database.

## <a name="configuration-steps"></a>Konfigurációs lépések

1. **Azure Active Directory konfigurálása** – további információ: [Az Azure ad-címtár felügyelete](https://msdn.microsoft.com/library/azure/hh967611.aspx), [a](../active-directory/hybrid/whatis-hybrid-identity.md)helyszíni identitások integrálása a Azure Active Directoryekkel, [saját tartománynév hozzáadása az Azure ad](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/) [-hez, A Microsoft Azure mostantól támogatja a Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)összevonását és az [Azure ad felügyeletét a Windows PowerShell használatával](https://msdn.microsoft.com/library/azure/jj151815.aspx).
2. Az **MFA konfigurálása** – részletes útmutatásért lásd: [Mi az az Azure multi-Factor Authentication?](../active-directory/authentication/multi-factor-authentication.md), [a feltételes hozzáférés (MFA) Azure SQL Database és az](sql-database-conditional-access.md)adatraktárral. (A teljes feltételes hozzáféréshez prémium szintű Azure Active Directory (Azure AD) szükséges. A korlátozott MFA standard szintű Azure AD-vel érhető el.)
3. **SQL Database vagy SQL Data Warehouse konfigurálása az Azure ad** -hitelesítéshez – lépésenkénti útmutatásért lásd: [Csatlakozás SQL Databasehoz vagy SQL Data Warehouse Azure Active Directory hitelesítés használatával](sql-database-aad-authentication.md).
4. **Töltse le a SSMS** -t az ügyfélszámítógépen, töltse le a legújabb SSMS a [Download SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)webhelyről. Az ebben a témakörben található összes szolgáltatáshoz használja a 17,2-es vagy újabb 2017-es verziót.  

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Csatlakozás a SSMS-sel való univerzális hitelesítéssel

A következő lépések bemutatják, hogyan csatlakozhat a SQL Databasehoz, vagy SQL Data Warehouse a legújabb SSMS használatával.

1. Az univerzális hitelesítés használatával történő kapcsolódáshoz a **Kapcsolódás a kiszolgálóhoz** párbeszédpanelen válassza a **Active Directory-Universal lehetőséget MFA-támogatással**. (Ha **Active Directory univerzális hitelesítést** látja, akkor nem a SSMS legújabb verziójával rendelkezik.)  
   ![1mfa-universal-connect][1]  
2. Töltse ki a **Felhasználónév** mezőt a Azure Active Directory hitelesítő adataival a (z `user_name@domain.com`) formátumban.  
   ![1mfa-universal-connect-user](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect-user.png)   
3. Ha vendég felhasználóként csatlakozik, a **Beállítások**elemre kell kattintania, majd a **kapcsolat tulajdonság** párbeszédpanelen el kell végeznie az **ad-tartománynév vagy a bérlői azonosító** mező bejelölését. További információ: az [univerzális hitelesítés SQL Database és SQL Data Warehouse (SSMS-támogatás az MFA-hoz)](sql-database-ssms-mfa-authentication.md).
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)   
4. A SQL Database és SQL Data Warehouse esetén a szokásos módon kattintson a **Beállítások** elemre, és a **Beállítások** párbeszédpanelen meg kell adnia az adatbázist. (Ha a csatlakoztatott felhasználó egy vendég felhasználó (azaz joe@outlook.com), jelölje be a jelölőnégyzetet, és adja hozzá az aktuális ad-tartománynevet vagy a bérlő azonosítóját a beállítások részeként. Lásd: [univerzális hitelesítés SQL Database és SQL Data Warehouse (SSMS-támogatás az MFA-hoz)](sql-database-ssms-mfa-authentication.md). Ezután kattintson a **Csatlakozás** gombra.  
5. Amikor megjelenik a **Bejelentkezés** a fiókba párbeszédpanel, adja meg a Azure Active Directory identitásának fiókját és jelszavát. Nincs szükség jelszóra, ha a felhasználó az Azure AD-vel összevont tartomány tagja.  
   ![2mfa-sign-in][2]  

   > [!NOTE]
   > Ha az univerzális hitelesítéshez olyan fiókra van szükség, amely nem igényel MFA-t, akkor ezen a ponton kapcsolódhat. Az MFA-t igénylő felhasználók esetében folytassa a következő lépésekkel:
   >  
   
6. Előfordulhat, hogy két MFA beállítási párbeszédpanel jelenik meg. Ez az egyszeri művelet az MFA rendszergazdai beállításától függ, ezért opcionális lehet. Az MFA-kompatibilis tartományokban ez a lépés néha előre definiálva van (például a tartomány megköveteli, hogy a felhasználók intelligens kártyát és PIN-kódot használjanak).  
   ![3mfa – telepítés][3]  
7. A második lehetőség egy alkalommal párbeszédpanel segítségével kiválaszthatja a hitelesítési módszer részleteit. A rendszergazda konfigurálja a lehetséges beállításokat.  
   ![4mfa-verify-1][4]  
8. A Azure Active Directory elküldi a megerősítési adatokat. Amikor megkapja az ellenőrző kódot, adja meg az **ENTER ellenőrző kód megadása** mezőbe, és kattintson a **Bejelentkezés**elemre.  
   ![5mfa-verify-2][5]  

Az ellenőrzés befejezésekor a SSMS általában feltételezi az érvényes hitelesítő adatokat és a tűzfal elérését.

## <a name="next-steps"></a>További lépések

- A többtényezős hitelesítés Azure SQL Database áttekintését lásd: az univerzális hitelesítés [SQL Database és SQL Data Warehouse (SSMS-támogatás az MFA-hoz)](sql-database-ssms-mfa-authentication.md).  
- Mások hozzáférésének biztosítása az adatbázishoz: [SQL Database hitelesítés és engedélyezés: Hozzáférés biztosítása](sql-database-manage-logins.md)  
- Győződjön meg arról, hogy mások kapcsolódhatnak a tűzfalon keresztül: [Azure SQL Database kiszolgáló szintű tűzfalszabály konfigurálása a Azure Portal használatával](sql-database-configure-firewall-settings.md)  
- A **Active Directory-Universal és az MFA-** hitelesítés használatakor a ADAL nyomkövetés a [SSMS 17,3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)-es verziójától kezdődően érhető el. Alapértelmezés szerint kikapcsolhatja a ADAL nyomkövetését az **eszközök**, beállítások menü, az **Azure-szolgáltatások**, az **Azure-felhő**, a **ADAL kimeneti ablak nyomkövetési szintje** **lehetőséggel** , majd a **kimenet** lehetőséget a **nézet** menüben engedélyezheti. A Nyomkövetések a kimeneti ablakban érhetők el **Azure Active Directory lehetőség**kiválasztásakor.   


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png


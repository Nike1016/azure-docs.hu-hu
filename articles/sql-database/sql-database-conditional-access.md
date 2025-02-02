---
title: Feltételes hozzáférés – Azure SQL Database és adattárház | Microsoft doc
description: Megtudhatja, hogyan konfigurálhatja a Azure SQL Database és az adatraktár feltételes hozzáférését.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sql-data-warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 03/29/2019
ms.openlocfilehash: 1b7000138c4dfc42b774969c1b971d969064b78f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569302"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-data-warehouse"></a>Feltételes hozzáférés (MFA) a Azure SQL Database és az adatraktárral  

Az Azure [SQL Database](sql-database-technical-overview.md), [felügyelt példány](sql-database-managed-instance.md)és [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) támogatja a Microsoft feltételes hozzáférését. 

> [!NOTE]
> Ez a témakör az Azure SQL Server-kiszolgálókra, valamint az Azure SQL Serveren létrehozott SQL Database- és SQL Data Warehouse-adatbázisokra vonatkozik. Az egyszerűség kedvéért a jelen témakörben az SQL Database és az SQL Data Warehouse megnevezése egyaránt SQL Database.

A következő lépések bemutatják, hogyan konfigurálhatja a SQL Databaset a feltételes hozzáférési szabályzat érvénybe léptetéséhez.  

## <a name="prerequisites"></a>Előfeltételek  
- Azure Active Directory hitelesítés támogatásához konfigurálnia kell a SQL Database vagy SQL Data Warehouse. Konkrét lépések: [Azure Active Directory hitelesítés konfigurálása és kezelése SQL Database vagy SQL Data Warehouse](sql-database-aad-authentication-configure.md).  
- Ha a többtényezős hitelesítés engedélyezve van, akkor a-t a támogatott eszközzel kell összekapcsolni, például a legújabb SSMS. További információ: [Azure SQL Database multi-Factor Authentication konfigurálása SQL Server Management studiohoz](sql-database-ssms-mfa-authentication-configure.md).  

## <a name="configure-ca-for-azure-sql-dbdw"></a>HITELESÍTÉSSZOLGÁLTATÓ konfigurálása az Azure SQL DB/DW számára  
1. Jelentkezzen be a portálra, válassza a **Azure Active Directory**lehetőséget, majd válassza a **feltételes hozzáférés**lehetőséget. További információ: [Azure Active Directory feltételes hozzáférés technikai útmutatója](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
   ![Feltételes hozzáférés panel](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2. A **feltételes hozzáférés – szabályzatok** panelen kattintson az **új házirend**elemre, adjon meg egy nevet, majd kattintson a **szabályok konfigurálása**elemre.  
3. A **hozzárendelések**területen válassza a **felhasználók és csoportok**, majd a **felhasználók és csoportok kiválasztása**lehetőséget, majd a feltételes hozzáféréshez válassza ki a felhasználót vagy a csoportot. Kattintson a kiválasztás elemre **, majd**kattintson a **kész** gombra a kijelölés elfogadásához.  
   ![felhasználók és csoportok kiválasztása](./media/sql-database-conditional-access/select-users-and-groups.png)  

4. Válassza a **Cloud apps**lehetőséget, majd kattintson az **alkalmazások kiválasztása**lehetőségre. A feltételes hozzáféréshez elérhető összes alkalmazás megjelenik. Válassza a **Azure SQL Database**lehetőséget, alul kattintson a **kiválasztás**elemre, majd kattintson a **kész**gombra.  
   ![select SQL Database](./media/sql-database-conditional-access/select-sql-database.png)  
   Ha nem találja az alábbi harmadik képernyőképen felsorolt **Azure SQL Database** , hajtsa végre a következő lépéseket:   
   - Jelentkezzen be az Azure SQL DB/DW-példányba a SSMS használatával egy HRE-rendszergazdai fiókkal.  
   - Végrehajtás `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
   - Jelentkezzen be a HRE-be, és ellenőrizze, hogy a Azure SQL Database és az adatraktár szerepel-e az alkalmazásokban a saját HRE.  

5. Válassza a **hozzáférés-vezérlés**lehetőséget, válassza a **támogatás**lehetőséget, majd jelölje ki az alkalmazni kívánt szabályzatot. Ebben a példában a többtényezős **hitelesítés**megkövetelése lehetőséget választjuk.  
   ![Válassza a hozzáférés engedélyezése lehetőséget](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Összegzés  
A kiválasztott alkalmazás (Azure SQL Database), amely lehetővé teszi az Azure SQL DB/DW-hez való kapcsolódást a prémium szintű Azure AD használatával, most már kikényszeríti a kiválasztott feltételes hozzáférési szabályzatot, a **szükséges multi-Factor Authentication hitelesítést.**  
A többtényezős hitelesítéssel kapcsolatos Azure SQL Database és adattárház kérdéseivel kapcsolatban MFAforSQLDB@microsoft.comvegye fel a kapcsolatot a következővel:.  

## <a name="next-steps"></a>További lépések  

Oktatóanyagért lásd: [a Azure SQL Database biztonságossá tétele](sql-database-security-tutorial.md).

---
title: '1433-nál nem nagyobb portok a következőhöz: SQL Database | Microsoft Docs'
description: A ADO.NET és a Azure SQL Database közötti ügyfélkapcsolatok megkerülhetik a proxyt, és közvetlenül a 1433-es portokon keresztül kommunikálnak az adatbázissal.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
ms.date: 04/03/2019
ms.openlocfilehash: a39cfd1981041c807a91a08c198378d238f0846e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568918"
---
# <a name="ports-beyond-1433-for-adonet-45"></a>1433-nál nem nagyobb portok ADO.NET 4,5

Ez a témakör a ADO.NET 4,5-et vagy újabb verziót használó ügyfelekhez Azure SQL Database kapcsolatok viselkedését ismerteti.

> [!IMPORTANT]
> További információ a kapcsolati architektúráról: [Azure SQL Database kapcsolati architektúra](sql-database-connectivity-architecture.md).
>

## <a name="outside-vs-inside"></a>Kívül és belül

A Azure SQL Databasehoz való kapcsolódáshoz először azt kell megkérdezni, hogy az ügyfélalkalmazás az Azure Cloud határán *kívül* vagy *belül* fut-e. Az alszakaszok két gyakori forgatókönyvet tárgyalnak.

### <a name="outside-client-runs-on-your-desktop-computer"></a>*Kívül* Ügyfél fut az asztali számítógépen

A 1433-as port az egyetlen olyan port, amelyet a SQL Database ügyfélalkalmazás gazdagépét futtató asztali számítógépen kell megnyitni.

### <a name="inside-client-runs-on-azure"></a>*Belül* Az ügyfél az Azure-ban fut

Ha az ügyfél az Azure-felhő határain belül fut, az azt használja, hogy a SQL Database-kiszolgálóval való kommunikációhoz egy *közvetlen útvonalat* hívjon fel. A kapcsolat létrejötte után az ügyfél és az adatbázis közötti további interakciók nem tartalmaznak Azure SQL Database átjárót.

A folyamat a következő:

1. A ADO.NET 4,5 (vagy újabb) egy rövid interakciót kezdeményez az Azure-felhővel, és egy dinamikusan azonosított portszámot kap.

   * A dinamikusan azonosított portszám a 11000-11999 tartományba esik.
2. A ADO.NET ezután közvetlenül a SQL Database-kiszolgálóhoz csatlakozik, és köztes köztes közbenső nem.
3. A rendszer közvetlenül az adatbázisba küldi a lekérdezéseket, és az eredményeket közvetlenül az ügyfélnek adja vissza.

Győződjön meg arról, hogy az Azure-ügyfélszámítógépen a 11000-11999-es porttartomány elérhető a ADO.NET 4,5-ügyfél interakciója SQL Database.

* A tartomány portjainak a többi kimenő blokktól mentesnek kell lenniük.
* Az Azure-beli virtuális gépen a **fokozott biztonságú Windows tűzfal** vezérli a portbeállítások beállításait.
  
  * A [tűzfal felhasználói felületének](https://msdn.microsoft.com/library/cc646023.aspx) használatával hozzáadhat egy olyan szabályt, amelyhez a **TCP** protokollt a porttartomány és a **11000-11999**-as szintaxissal együtt adja meg.

## <a name="version-clarifications"></a>Verziók pontosítása

Ez a szakasz a termék verziójára hivatkozó monikereket pontosítja. Emellett felsorolja a termékek között a verziók néhány párosítását is.

### <a name="adonet"></a>ADO.NET

* A ADO.NET 4,0 támogatja a TDS 7,3 protokollt, de nem 7,4.
* A ADO.NET 4,5-es és újabb verziói támogatják a TDS 7,4 protokollt.

### <a name="odbc"></a>ODBC

* Microsoft SQL Server ODBC 11 vagy újabb verzió

### <a name="jdbc"></a>JDBC

* Microsoft SQL Server JDBC 4,2 vagy újabb verzió (a JDBC 4,0 ténylegesen támogatja a TDS 7,4-et, de nem implementálja az "átirányítás")

## <a name="related-links"></a>Kapcsolódó hivatkozások

* A ADO.NET 4,6 2015. július 20-án jelent meg. A .NET-csapatról szóló blogbejegyzés [itt](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-net-framework-4-6.aspx)érhető el.
* A ADO.NET 4,5-es verziójának kiadása: 2012. augusztus 15. A .NET-csapatról szóló blogbejegyzés [itt](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx)érhető el.
  * Az ADO.NET 4.5.1-re vonatkozó blogbejegyzés [itt](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-the-net-framework-4-5-1-preview.aspx)érhető el.

* Microsoft® ODBC-illesztő 17 SQL Server®-Windows, Linux, & macOS https://www.microsoft.com/download/details.aspx?id=56567

* Kapcsolódás Azure SQL Database V12-hez átirányítás útján https://techcommunity.microsoft.com/t5/DataCAT/Connect-to-Azure-SQL-Database-V12-via-Redirection/ba-p/305362

* [TDS protokoll verzióinak listája](https://www.freetds.org/userguide/tdshistory.htm)
* [SQL Database fejlesztés áttekintése](sql-database-develop-overview.md)
* [Azure SQL Database tűzfal](sql-database-firewall-configure.md)
* [Útmutató: A tűzfal beállításainak konfigurálása az SQL Database szolgáltatásban](sql-database-configure-firewall-settings.md)



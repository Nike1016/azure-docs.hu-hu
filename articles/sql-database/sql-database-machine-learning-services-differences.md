---
title: Fontos különbség az Azure SQL Database, Machine Learning Services (előzetes verzió)
description: Ez a témakör az Azure SQL Database Machine Learning-szolgáltatások (az r nyelv) és az SQL Server Machine Learning-szolgáltatások közötti fő különbségeket ismerteti.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 03/01/2019
ms.openlocfilehash: ee92b598625b1346cf87c661d1867cc1cb012b60
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485993"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>Machine Learning-szolgáltatások az Azure SQL Database (előzetes verzió) és az SQL Server közötti fő különbségeket

Az Azure SQL Database Machine Learning-szolgáltatások (az r nyelv) (előzetes verzió) funkciója hasonló [SQL Server Machine Learning-szolgáltatások](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Az alábbiakban néhány fontos eltérés.

> [!IMPORTANT]
> Az Azure SQL Database Machine Learning-szolgáltatások jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="language-support"></a>Nyelvi támogatás

Az SQL Server R és Python használatával támogatással rendelkezik a [bővíthetőségi keretrendszerét](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework). Az SQL Database nem támogatja a mindkét nyelven. A legfontosabb különbségek a következők:

- Az R a SQL Database-ben az egyetlen támogatott nyelv. A Python jelenleg nem támogatott.
- Az R 3.4.4 verziószáma.
- Nem kell konfigurálása `external scripts enabled` keresztül `sp_configure`. Ha már [regisztrált](sql-database-machine-learning-services-overview.md#signup), machine learning-engedélyezve van az SQL-adatbázis.

## <a name="package-management"></a>Csomagkezelés

R csomag kezelés és telepítés működik különböző SQL Database és SQL Server között. Ezek a különbségek a következők:

- R-csomagok települnek keresztül [sqlmlutils](https://github.com/Microsoft/sqlmlutils) vagy [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).
- Csomagok nem hajtható végre a kimenő hálózati hívások. Ezt a korlátozást hasonlít a [tűzfalszabályok alapértéke a Machine Learning-szolgáltatások](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) az SQL-kiszolgálón, de nem lehet módosítani az SQL Database-ben.
- A rendszer nem támogatja, a csomagok telepítést vagy a használati függ (például Java) külső modulok vagy az operációs rendszer API-k hozzáférést.

## <a name="writing-to-a-temporary-table"></a>Egy ideiglenes tábla írása

RODBC használja Azure SQL Database-ben, akkor azt nem lehet írni egy ideiglenes táblát e belül vagy kívül létrehozása a `sp_execute_external_script` munkamenet. Az áthidaló megoldás [RxOdbcData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxodbcdata) és [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep) (a felülírás = FALSE, és a hozzáfűző = "sor") előtt létrehozott globális ideiglenes táblát írni a `sp_execute_external_script` lekérdezés.

## <a name="resource-governance"></a>Erőforrások szabályozása

Már nem R erőforrásai a korlátozásához [erőforrás-vezérlő](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) és külső erőforrás-készletek.

A nyilvános előzetes során R erőforrások vannak beállítva, hogy egy legfeljebb 20 %-át az SQL Database-erőforrásokat, és függő melyik szolgáltatási szintet választja. További információkért lásd: [vásárlási modellek az Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).
### <a name="insufficient-memory-error"></a>Nincs elég memória hiba

Ha nincs elegendő memória áll rendelkezésre az R, hibaüzenetet kap. Gyakori hibaüzenetek a következők:

- Nem sikerült a kérelem azonosítója "R" szkript futtatókörnyezetével: x. Ellenőrizze az "R" futtatókörnyezet követelményeit
- "R" parancsfájlhiba történt az "sp_execute_external_script", HRESULT: 0x80004004 végrehajtása közben. ... külső parancsfájlhiba történt: ".. nem sikerült memóriát (0 Mb) a C funkciót "R_AllocStringBuffer" "
- Külső parancsfájlhiba történt: Hiba: nem foglalható le méretű vektort.

Használati attól függ, hogy mennyi memóriát használja az R-szkriptek és a lekérdezések párhuzamos végrehajtása számát. Ha a fenti hibákat kap, magasabb szolgáltatási szintre a probléma megoldásához az adatbázis is méretezhető.

## <a name="next-steps"></a>További lépések

- Az áttekintésben [Azure SQL Database Machine Learning Services (előzetes verzió) R-rel](sql-database-machine-learning-services-overview.md).
- R lekérdezni az Azure SQL Database, Machine Learning Services (előzetes verzió) használatával kapcsolatban lásd: a [a rövid útmutató](sql-database-connect-query-r.md).
- Néhány egyszerű R-szkriptek használatának megkezdéséhez lásd [létrehozás és Futtatás egyszerű R-szkriptek az Azure SQL Database, Machine Learning Services (előzetes verzió)](sql-database-quickstart-r-create-script.md).

---
title: A Visual Studio 2019 telepítése a SQL Data Warehousehoz | Microsoft Docs
description: A Visual Studio és az SQL Server Development Tools (SSDT) telepítése az Azure SQL Data Warehouse-hoz
services: sql-data-warehouse
ms.custom: vs-azure
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 08/15/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 02caa7154ef9cb8419b533f0cb2d0fd57e86bf9f
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563327"
---
# <a name="getting-started-with-visual-studio-2019-for-sql-data-warehouse"></a>Első lépések a Visual Studio 2019 for SQL Data Warehouse
A Visual Studio **2019** SQL Server Data Tools (SSDT) egyetlen eszköz, amely lehetővé teszi a következőket:

- Alkalmazások összekötése, lekérdezése és fejlesztése SQL Data Warehouse 
- Az Object Explorer használatával vizuálisan feltárhatja az adatmodellben lévő összes objektumot, beleértve a táblákat, a nézeteket, a tárolt eljárásokat és az egyéb műveleteket.
- T-SQL adatdefiníciós nyelvi (DDL) parancsfájlok létrehozása az objektumokhoz
- Az adattárház fejlesztése SSDT adatbázis-projektekkel rendelkező, állapot-alapú megközelítés használatával
- Az adatbázis-projekt integrálása verziókövetés-rendszerekkel, mint például a git és az Azure DevOps Repos
- Folyamatos integrációs és üzembe helyezési folyamatokat állíthat be automatizálási kiszolgálókkal, például az Azure DevOps-szel [hamarosan elérhető]

> [!NOTE]
> A Visual Studio SSDT adatbázis-projektjei jelenleg előzetes verzióban érhetők el. A szolgáltatás rendszeres frissítéseinek fogadásához szavazzon a [UserVoice-on].

## <a name="install-visual-studio-2019"></a>A Visual Studio 2019 telepítése
A Visual Studio letöltéséhez és telepítéséhez tekintse meg a [A Visual Studio 2019 letöltése][] ismertető témakört. A telepítés során válassza ki az adattárolási és-feldolgozási munkaterhelést. A Visual Studio 2019-es verziójában már nem szükséges a különálló SSDT telepítése.

## <a name="reporting-issues-with-ssdt-visual-studio-2019-preview"></a>Jelentéskészítési problémák a SSDT Visual Studio 2019 (előzetes verzió) szolgáltatással

Ha a SSDT és a SQL Data Warehouse használatával szeretne problémákat jelenteni, e-mailben küldje el a következő e-mail-terjesztési listát:<sqldwssdtpreview@service.microsoft.com>

## <a name="next-steps"></a>További lépések

Most, hogy a SSDT legújabb verzióját használja, készen áll a SQL Data Warehouse [][connect] való kapcsolódásra.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[A Visual Studio 2019 letöltése]: https://visualstudio.microsoft.com/vs/preview/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice-on]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu

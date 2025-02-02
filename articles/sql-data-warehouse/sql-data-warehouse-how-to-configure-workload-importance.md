---
title: Számítási feladatok fontossági konfigurálása az Azure SQL Data Warehouse |} A Microsoft Docs
description: Ismerje meg, hogyan állítsa be a kérelem szintű fontosság.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: e4d410f32068b4d3035dcab0c61b7b9205103690
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67588686"
---
# <a name="configure-workload-importance-in-azure-sql-data-warehouse"></a>Az Azure SQL Data Warehouse számítási feladatok fontossági konfigurálása

Fontosság beállítása az SQL Data Warehouse lehetővé teszi a befolyásolhatják a lekérdezések vezénylését. A magasabb fontossági lekérdezések futtatása előtt lekérdezések, az alacsonyabb fontossági lesz ütemezve. Fontosság hozzárendelése a lekérdezéseket, szüksége egy számítási feladat osztályozó létrehozása.

## <a name="create-a-workload-classifier-with-importance"></a>Hozzon létre egy számítási feladat osztályozó fontosság

Gyakran egy adatraktár-forgatókönyvet a felhasználóval rendelkezik, akiknek szükség van a lekérdezések gyorsan futhassanak.  A felhasználó lehet a vállalat vezetők, akik jelentéseket vagy a felhasználó lehet egy elemző, az ad hoc ad hoc lekérdezések futtatása. A számítási feladatok osztályozó fontosság hozzárendelése egy lekérdezést hoz létre.  Az alábbi példák az új használata [munkaterhelés-osztályozó létrehozása](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) szintaxisa két osztályozó létrehozása.  Membername lehet egy felhasználó vagy csoport. Egyéni felhasználói besorolások elsőbbséget élveznek a szerepkör besorolásokat. A meglévő data warehouse felhasználók megkereséséhez futtassa:

```sql
Select name from sys.sysusers
```

Hozzon létre egy számítási feladat osztályozó, magasabb fontossági rendelkező futtassa:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  above_normal);  

```

Alacsonyabb fontossági futtassa az ad hoc ad hoc lekérdezéseket futtatnak a számítási feladatok besorolás létrehozása:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  below_normal);  
```

## <a name="next-steps"></a>További lépések
- Számítási feladatok kezelésével kapcsolatos további információkért lásd: [munkaterhelés besorolás](sql-data-warehouse-workload-classification.md)
- Fontosság további információkért lásd: [számítási feladatok fontossági](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Ugrás a kezelés és számítási feladatok fontossági figyelése](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)

---
title: Felügyeleti API-referenciák Azure SQL Database felügyelt példányhoz | Microsoft Docs
description: Tudnivalók Azure SQL Database felügyelt példányok létrehozásáról és kezeléséről.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 6bbba3910315be39d9fca81b95083f32ebd60e64
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567597"
---
# <a name="managed-api-reference-for-azure-sql-database-managed-instances"></a>Felügyelt API-referenciák Azure SQL Database felügyelt példányokhoz

Azure SQL Database felügyelt példányokat a Azure Portal, a PowerShell, az Azure CLI, a REST API és a Transact-SQL használatával hozhatja létre és kezelheti. Ebben a cikkben a felügyelt példányok létrehozásához és konfigurálásához használható függvények és API-k áttekintését találhatja meg.

## <a name="azure-portal-create-a-managed-instance"></a>Azure Portal: Felügyelt példány létrehozása

A Azure SQL Database felügyelt példányok [létrehozását bemutató rövid útmutató: gyors üzembe helyezés Azure SQL Database felügyelt példány](sql-database-managed-instance-get-started.md)létrehozása.

## <a name="powershell-create-and-manage-managed-instances"></a>PowerShell: Felügyelt példányok létrehozása és kezelése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

Felügyelt példányok Azure PowerShell használatával történő létrehozásához és kezeléséhez használja a következő PowerShell-parancsmagokat. Ha telepítenie vagy frissítenie kell a PowerShellt, olvassa el a [Azure PowerShell modul telepítése](/powershell/azure/install-az-ps)című témakört.

> [!TIP]
> A PowerShell-parancsfájlok például a [következő témakörben találhatók: gyors üzembe helyezési parancsfájl: Az Azure SQL felügyelt példányának létrehozása](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../quick-start-script-create-azure-sql-managed-instance-using-powershell/)a PowerShell-kódtár használatával.

| A parancsmag | Leírás |
| --- | --- |
|[New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)|Létrehoz egy Azure SQL Database felügyelt példányt |
|[Get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstance)|A felügyelt Azure SQL-példányra vonatkozó adatokat adja vissza.|
|[Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)|Azure SQL Database felügyelt példány tulajdonságainak beállítása|
|[Remove-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance)|Azure SQL felügyelt adatbázis-példány eltávolítása|
|[New-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstancedatabase)|Létrehoz egy Azure SQL Database felügyelt példány-adatbázist|
|[Get-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabase)|Az Azure SQL felügyelt példány adatbázisával kapcsolatos információkat ad vissza.|
|[Remove-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabase)|Egy Azure SQL felügyelt adatbázis-példány adatbázisának eltávolítása|
|[Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)|Egy Azure SQL felügyelt adatbázis-példány adatbázisának visszaállítása|

## <a name="azure-cli-create-and-manage-managed-instances"></a>Azure CLI: Felügyelt példányok létrehozása és kezelése

Felügyelt példányok [Azure CLI](/cli/azure)-vel való létrehozásához és kezeléséhez használja az alábbi [Azure CLI SQL felügyelt példányok](/cli/azure/sql/mi) parancsait. A [Cloud Shell-lel](/azure/cloud-shell/overview) futtassa a parancssori felületet a böngészőben, vagy [telepítse](/cli/azure/install-azure-cli) macOS, Linux, illetve Windows rendszeren.

> [!TIP]
> Azure CLI-gyors útmutató: az [SQL felügyelt példány használata az Azure CLI használatával](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

| A parancsmag | Leírás |
| --- | --- |
|[az SQL mi Create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) |Felügyelt példány létrehozása|
|[az SQL mi List](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-list)|Az elérhető felügyelt példányok listája|
|[az SQL mi show](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-show)|Felügyelt példány részleteinek beolvasása|
|[az SQL mi Update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)|Felügyelt példány frissítése|
|[az sql mi delete](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-delete)|Felügyelt példány eltávolítása|
|[az SQL MidB Create](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-create) |Felügyelt adatbázis létrehozása|
|[az SQL MidB List](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-list)|Az elérhető felügyelt adatbázisok listája|
|[az SQL MidB Restore](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-restore)|Felügyelt adatbázis visszaállítása|
|[az sql midb delete](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-delete)|Felügyelt adatbázis eltávolítása|

## <a name="transact-sql-create-and-manage-instance-databases"></a>Transact-SQL: Példány-adatbázisok létrehozása és kezelése

Példány-adatbázis létrehozásához és kezeléséhez a felügyelt példány létrehozása után használja az alábbi T-SQL-parancsokat. Ezeket a parancsokat a Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio) [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is)használatával adhatja ki. A [Visual Studio Code](https://code.visualstudio.com/docs)vagy bármely más olyan program, amely csatlakozhat egy Azure SQL Database-kiszolgálóhoz, és hogyan adhat át Transact-SQL-parancsokat.

> [!TIP]
> A Microsoft Windows SQL Server Management Studio használatával felügyelt példányok konfigurálását és kapcsolódását bemutató rövid útmutatók: gyors útmutató [: Az Azure virtuális gép konfigurálása Azure SQL Database felügyelt példányhoz](sql-database-managed-instance-configure-vm.md) és [gyors útmutatóhoz való kapcsolódáshoz: Pont – hely kapcsolat konfigurálása egy Azure SQL Database felügyelt példányhoz a helyszínen](sql-database-managed-instance-configure-p2s.md).
> [!IMPORTANT]
> Felügyelt példány nem hozható létre és nem törölhető a Transact-SQL használatával.

| Parancs | Leírás |
| --- | --- |
|[CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|Létrehoz egy új felügyelt példány-adatbázist. Új adatbázis létrehozásához csatlakoznia kell a Master adatbázishoz.|
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |Módosít egy Azure SQL felügyelt példány-adatbázist.|

## <a name="rest-api-create-and-manage-managed-instances"></a>REST API: Felügyelt példányok létrehozása és kezelése

Felügyelt példányok létrehozásához és kezeléséhez használja ezeket a REST API kérelmeket.

| Parancs | Leírás |
| --- | --- |
|[Felügyelt példányok – létrehozás vagy frissítés](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|Felügyelt példány létrehozása vagy frissítése.|
|[Felügyelt példányok – törlés](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|Felügyelt példány törlése.|
|[Felügyelt példányok – Get](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|Felügyelt példány beolvasása.|
|[Felügyelt példányok – lista](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|Egy előfizetésben lévő felügyelt példányok listáját adja vissza.|
|[Felügyelt példányok – erőforráslista szerint csoportosítva](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|Egy erőforráscsoport felügyelt példányainak listáját adja vissza.|
|[Felügyelt példányok – frissítés](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|Felügyelt példány frissítése.|

## <a name="next-steps"></a>További lépések

- A SQL Server-adatbázisok Azure-ba történő áttelepítésével kapcsolatos információkért lásd: [áttelepítés Azure SQL Databasere](sql-database-single-database-migrate.md).
- A támogatott funkciókkal kapcsolatos tudnivalókat lásd: [Funkciók](sql-database-features.md).

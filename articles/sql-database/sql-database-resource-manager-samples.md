---
title: SQL Database Azure Resource Manager sablonjai | Microsoft Docs
description: Azure SQL Database létrehozásához és konfigurálásához használjon Azure Resource Manager sablonokat.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples
ms.devlang: ''
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/04/2019
ms.openlocfilehash: 81f3e4beb29f21f1b752a876827e9b35856c713a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566885"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database"></a>Azure SQL Database Azure Resource Manager sablonjai

Azure Resource Manager sablonok lehetővé teszik az infrastruktúra kódként való definiálását és a megoldások üzembe helyezését az Azure-felhőben.

## <a name="single-database--elastic-pool"></a>Önálló adatbázis & rugalmas készlet

Az alábbi táblázat a Azure SQL Database Azure Resource Manager sablonjaira mutató hivatkozásokat tartalmaz.

| |  |
|---|---|
| [Önálló adatbázis](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | Ez a Azure Resource Manager sablon egyetlen Azure SQL Database hoz létre a logikai kiszolgálóval, és konfigurálja a tűzfalszabályok beállításait. |
| [Logikai kiszolgáló](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | Ez a Azure Resource Manager-sablon logikai kiszolgálót hoz létre a Azure SQL Databasehoz. |
| [Rugalmas készlet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | Ez a sablon lehetővé teszi egy új rugalmas készlet üzembe helyezését az új társított SQL Server és az új SQL-adatbázisok hozzárendelésével. |
| [Feladatátvételi csoportok](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | Ez a sablon két Azure SQL logikai kiszolgálót, egy SQL-adatbázist és egy feladatátvételi csoportot hoz létre.|
| [Fenyegetések észlelése](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | Ez a sablon lehetővé teszi egy Azure SQL logikai kiszolgáló és egy olyan Azure SQL Database-adatbázis üzembe helyezését, amelyen engedélyezve van a veszélyforrások észlelése, és e-mail-címmel kell rendelkeznie az egyes adatbázisokra vonatkozó riasztásokhoz. A veszélyforrások észlelése az SQL Advanced Threat Protection (ATP) ajánlat része, és olyan biztonsági réteget biztosít, amely az SQL-kiszolgálókon és-adatbázisokon keresztül lehetséges fenyegetésekre reagál.|
| [Naplózás az Azure Blob Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | Ez a sablon lehetővé teszi, hogy olyan Azure SQL logikai kiszolgálót helyezzen üzembe, amelyen engedélyezve van a naplózás, hogy a naplók egy blob Storage-tárolóba írjanak. A Azure SQL Database naplózása nyomon követi az adatbázis eseményeit, és az Azure Storage-fiókba, OMS-munkaterületbe vagy Event Hubsba helyezhető naplófájlba írja azokat.|
| [Naplózás az Azure Event hub-ba](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | Ez a sablon lehetővé teszi, hogy egy olyan Azure SQL Servert helyezzen üzembe, amelyen engedélyezve van a naplózás, hogy naplókat írjon a kilépő Event hubhoz. A naplózási események az Event hubhoz való küldéséhez állítsa be a naplózási beállításokat `IsAzureMonitorTargetEnabled` a `true` `Enabled` `State` és a beállításként. Továbbá konfigurálja a diagnosztikai beállításokat `SQLSecurityAuditEvents` a diagnosztikai naplók kategóriával az `master` adatbázisban (a kiszolgálás szintjének naplózásához). A Azure SQL Database és SQL Data Warehouse naplózása nyomon követi az adatbázis-eseményeket, és az Azure Storage-fiókba, a OMS-munkaterületbe vagy a Event Hubsba helyezhető naplófájlba írja azokat.|
| [Azure-webalkalmazás és SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | Ez a példa egy ingyenes Azure-webalkalmazást hoz létre, és SQL Database az "alapszintű" szolgáltatási szinten.|
| [Azure-webalkalmazás és Redis Cache a következővel SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | Ez a sablon létrehoz egy webalkalmazást, Redis Cache és SQL Database ugyanabban az erőforráscsoportban, és két kapcsolati karakterláncot hoz létre a webalkalmazásban a SQL Database és Redis Cache.|
| [Adatok importálása a blob Storage-ból az ADF V2 használatával](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | Ez a Azure Resource Manager sablon Azure Data Factory v2-et hoz létre, amely az Blob Storage Azure-ból származó adatok SQL Databaseba való másolását végzi.|
| [HDInsight-fürt SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | Ez a sablon lehetővé teszi egy HDInsight-fürt, egy SQL Database-kiszolgáló, egy SQL Database és két tábla létrehozását. Ezt a sablont [használja a Sqoop használata a Hadoop-ben a HDInsight cikkben](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-use-sqoop) |
| [Az Azure logikai alkalmazás, amely egy ütemezett SQL tárolt eljárást futtat](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | Ez a sablon lehetővé teszi, hogy olyan logikai alkalmazást hozzon létre, amely egy ütemezett SQL tárolt eljárást fog futtatni. Az eljáráshoz tartozó argumentumok a sablon törzs szakaszába helyezhetők.|

## <a name="managed-instance"></a>Felügyelt példány

Az alábbi táblázat a Azure SQL Database által felügyelt példányokra Azure Resource Manager sablonokra mutató hivatkozásokat tartalmaz.

| |  |
|---|---|
| [Felügyelt példány egy új VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | Ez a Azure Resource Manager sablon létrehoz egy új, konfigurált Azure VNet és felügyelt példányt a VNet. |
| [A felügyelt példány hálózati környezete](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | Ez az üzembe helyezés egy konfigurált Azure-Virtual Network hoz létre két alhálózattal – az egyiket a felügyelt példányok számára, és egy másikat, ahol más erőforrásokat (például virtuális gépeket, App Service környezeteket stb.) helyezhet el. Ez a sablon egy megfelelően konfigurált hálózati környezetet hoz létre, ahol felügyelt példányokat helyezhet üzembe. |
| [Felügyelt példány P2S-kapcsolatban](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | Ez az üzembe helyezés egy Azure `ManagedInstance` -Virtual Network hoz létre két alhálózattal és. `GatewaySubnet` A felügyelt példány a ManagedInstance-alhálózatban lesz telepítve. A rendszer létrehozza a virtuális hálózati átjárót az `GatewaySubnet` alhálózatban, és a pont – hely VPN-kapcsolathoz van konfigurálva. |
| [Felügyelt példány virtuális géppel](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | Ez az üzembe helyezés egy Azure `ManagedInstance` -Virtual Network hoz létre két alhálózattal és. `Management` A felügyelt példány az `ManagedInstance` alhálózaton lesz telepítve. A SQL Server Management Studio (SSMS) legújabb verzióját tartalmazó virtuális gép az `Management` alhálózaton lesz telepítve. |

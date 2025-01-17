---
title: A Azure SQL Database konfigurálása – egyetlen | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat és kezelhet Azure SQL Database-önálló adatbázist
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
ms.date: 02/08/2019
ms.openlocfilehash: 2117a811f977230dd9c9eecf6ea09b9b7deda3be
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568053"
---
# <a name="how-to-use-a-single-database-in-azure-sql-database"></a>Egyetlen adatbázis használata Azure SQL Database

Ebben a szakaszban különféle útmutatókat, parancsfájlokat és magyarázatokat talál, amelyek segítségével kezelheti és konfigurálhatja az önálló adatbázist Azure SQL Database

## <a name="migrate"></a>Migrate (Áttelepítés)

- [Migrálás SQL Databasere](sql-database-single-database-migrate.md) – az ajánlott áttelepítési folyamat és a felügyelt példányra való áttelepítéshez szükséges eszközök ismertetése.
- Ismerje meg, hogyan kezelheti az SQL Database-t az [áttelepítés után](sql-database-manage-after-migration.md).

## <a name="configure-features"></a>Funkciók konfigurálása

- [Konfigurálja](replication-to-sql-database.md) a tranzakciós replikálást az adatbázisok közötti dátum replikálásához.
- A [veszélyforrások észlelésének konfigurálásával](sql-database-threat-detection.md) Azure SQL Database azonosíthatók a gyanús tevékenységek, például az SQL-injektálás vagy a gyanús helyekről való hozzáférés.
- A bizalmas adatok védelme érdekében [konfigurálja a dinamikus](sql-database-dynamic-data-masking-get-started-portal.md) adatmaszkolást.
- A [biztonsági másolatok megőrzésének konfigurálása](sql-database-long-term-backup-retention-configure.md) egy adatbázis számára, hogy a biztonsági mentések megmaradjanak az Azure Blob Storage-on. Alternatív megoldásként konfigurálhatja a [biztonsági másolatok megőrzését az Azure-tároló (elavult) használatával](sql-database-long-term-backup-retention-configure-vault.md) .
- [Konfigurálja a Geo-replikálást](sql-database-geo-replication-portal.md) , hogy az adatbázis egy másik régióban maradjon.
- [Konfigurálja a Geo-replikák biztonságát](sql-database-geo-replication-security-config.md).

## <a name="monitor-and-tune-your-database"></a>Az adatbázis monitorozása és finomhangolása

- [Engedélyezze az automatikus hangolást](sql-database-automatic-tuning-enable.md) , hogy Azure SQL Database optimalizálja a számítási feladatok teljesítményét.
- Az [automatikus hangolással kapcsolatos e-mail-értesítések engedélyezése](sql-database-automatic-tuning-email-notifications.md) a hangolási javaslatokkal kapcsolatos információk beszerzéséhez.
- [Teljesítménnyel kapcsolatos javaslatok alkalmazása](sql-database-advisor-portal.md) és az adatbázis optimalizálása.
- [Riasztásokat hozhat létre](sql-database-insights-alerts-portal.md) Azure SQL Databaseről érkező értesítések beszerzéséhez.
- [Kapcsolódási hibák elhárítása](sql-database-troubleshoot-common-connection-issues.md) , ha az alkalmazások és az adatbázis közötti kapcsolódási problémákat tapasztal. [A kapcsolódási problémákhoz Resource Health](sql-database-resource-health.md)is használhatja.
- [Kezelheti](sql-database-file-space-management.md) a tárhelyet a tárterület-használat figyeléséhez az adatbázisban.

## <a name="query-distributed-data"></a>Lekérdezések elosztott adatai

- [](sql-database-elastic-query-getting-started-vertical.md) A vertikálisan particionált adatforrásokat több adatbázis között kérdezheti le.
- A kibővített adatrétegek [közötti jelentés](sql-database-elastic-query-horizontal-partitioning.md).
- [Lekérdezés különböző sémákkal rendelkező táblák között](sql-database-elastic-query-vertical-partitioning.md).

## <a name="elastic-database-jobs"></a>Rugalmas adatbázis-feladatok

- [Létrehozás és kezelés](elastic-jobs-powershell.md) Elastic Database feladatok a PowerShell használatával.
- [Létrehozás és kezelés](elastic-jobs-tsql.md) Feladatok Elastic Database a Transact-SQL használatával.
- [Migrálás a régi rugalmas](elastic-jobs-migrate.md)feladatokból.

## <a name="database-sharding"></a>Adatbázis horizontális skálázása

- [Rugalmas adatbázis-ügyfél függvénytárának frissítése](sql-database-elastic-scale-upgrade-client-library.md).
- [Szilánkokra osztott alkalmazás létrehozása](sql-database-elastic-scale-get-started.md).
- [Horizontálisan szilánkokra](sql-database-elastic-query-getting-started.md)bontott adathalmazok lekérdezése
- [Több szegmensből álló lekérdezések](sql-database-elastic-scale-multishard-querying.md)futtatása.
- [Helyezze át](sql-database-elastic-scale-configure-deploy-split-and-merge.md)a szilánkokra osztott adatátvitelt.
- [Konfigurálja a biztonságot](sql-database-elastic-scale-split-merge-security-configuration.md) az adatbázis-szegmensekben.
- [Adjon hozzá egy](sql-database-elastic-scale-add-a-shard.md) szegmenst az aktuális adatbázis-szegmensek jelenlegi készletéhez.
- [](sql-database-elastic-database-recovery-manager.md)Kijavítottuk a szilánkok térképének problémáit.
- A szilánkokra osztott [adatbázis migrálása](sql-database-elastic-convert-to-use-elastic-tools.md).
- [Számlálók létrehozása](sql-database-elastic-database-perf-counters.md)
- Az [Entity Framework használatával](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) lekérdezheti a szilánkokra osztott adatkereteket.
- A felhasználható [keretrendszer használatával](sql-database-elastic-scale-working-with-dapper.md) lekérdezheti a szilánkokra bontott adatkereteket.

## <a name="next-steps"></a>További lépések
- További információ a [felügyelt példányokkal](sql-database-howto-managed-instance.md) kapcsolatos útmutatókról

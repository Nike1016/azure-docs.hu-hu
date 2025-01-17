---
title: Teljesítmény figyelése Azure SQL Database DMV használatával | Microsoft Docs
description: Megtudhatja, hogyan észlelheti és diagnosztizálhatja a gyakori teljesítménnyel kapcsolatos problémákat a Microsoft Azure SQL Database figyelésére szolgáló dinamikus felügyeleti nézetek használatával.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 12/19/2018
ms.openlocfilehash: a630ceb1748f38dc169a4ebabcbb4e021de4273c
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881563"
---
# <a name="monitoring-performance-azure-sql-database-using-dynamic-management-views"></a>Teljesítmény monitorozása a dinamikus felügyeleti nézetek használatával Azure SQL Database

Microsoft Azure SQL Database lehetővé teszi a dinamikus felügyeleti nézetek egy részhalmazát a teljesítménnyel kapcsolatos problémák diagnosztizálására, ami a blokkolt vagy hosszan futó lekérdezések, az erőforrás szűk keresztmetszetei, a rossz lekérdezési csomagok és így tovább okozhat. Ez a témakör arról nyújt tájékoztatást, hogyan észlelhetők a gyakori teljesítményproblémák a dinamikus felügyeleti nézetek használatával.

SQL Database részben a dinamikus felügyeleti nézetek három kategóriáját támogatja:

- Adatbázissal kapcsolatos dinamikus felügyeleti nézetek.
- Végrehajtással kapcsolatos dinamikus felügyeleti nézetek.
- Tranzakcióval kapcsolatos dinamikus felügyeleti nézetek.

A dinamikus felügyeleti nézetekkel kapcsolatos részletes információkért lásd: a [dinamikus felügyeleti nézetek és függvények (Transact-SQL)](https://msdn.microsoft.com/library/ms188754.aspx) SQL Server Books Online-ban. 

## <a name="permissions"></a>Engedélyek

SQL Database a dinamikus felügyeleti nézet lekérdezéséhez **adatbázis** -állapotra vonatkozó engedélyeket kell megtekintenie. Az **adatbázis megtekintése állapot** engedély az aktuális adatbázisban lévő összes objektumra vonatkozó adatokat adja vissza.
Ha meg szeretné adni az **adatbázis megtekintése** engedélyt egy adott adatbázis-felhasználónak, futtassa a következő lekérdezést:

```sql
GRANT VIEW DATABASE STATE TO database_user;
```

A helyszíni SQL Server egy példányában a dinamikus felügyeleti nézetek a kiszolgáló állapotával kapcsolatos információkat adnak vissza. SQL Database a csak az aktuális logikai adatbázisra vonatkozó adatokat adja vissza.

## <a name="identify-cpu-performance-issues"></a>CPU-teljesítménnyel kapcsolatos problémák azonosítása

Ha a CPU-felhasználás hosszabb ideig 80%-nál nagyobb, vegye figyelembe a következő hibaelhárítási lépéseket:

### <a name="the-cpu-issue-is-occurring-now"></a>A CPU-probléma most következik be

Ha a probléma most következik be, két lehetséges forgatókönyv van:

#### <a name="many-individual-queries-that-cumulatively-consume-high-cpu"></a>Sok egyéni lekérdezés, amely összesíti a magas CPU-t

Használja a következő lekérdezést a leggyakoribb lekérdezési kivonatok azonosításához:

```sql
PRINT '-- top 10 Active CPU Consuming Queries (aggregated)--';
SELECT TOP 10 GETDATE() runtime, *
FROM(SELECT query_stats.query_hash, SUM(query_stats.cpu_time) 'Total_Request_Cpu_Time_Ms', SUM(logical_reads) 'Total_Request_Logical_Reads', MIN(start_time) 'Earliest_Request_start_Time', COUNT(*) 'Number_Of_Requests', SUBSTRING(REPLACE(REPLACE(MIN(query_stats.statement_text), CHAR(10), ' '), CHAR(13), ' '), 1, 256) AS "Statement_Text"
     FROM(SELECT req.*, SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1) AS statement_text
          FROM sys.dm_exec_requests AS req
               CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST ) AS query_stats
     GROUP BY query_hash) AS t
ORDER BY Total_Request_Cpu_Time_Ms DESC;
```

#### <a name="long-running-queries-that-consume-cpu-are-still-running"></a>A CPU-t használó hosszan futó lekérdezések továbbra is futnak

A következő lekérdezés segítségével azonosíthatja a lekérdezéseket:

```sql
PRINT '--top 10 Active CPU Consuming Queries by sessions--';
SELECT TOP 10 req.session_id, req.start_time, cpu_time 'cpu_time_ms', OBJECT_NAME(ST.objectid, ST.dbid) 'ObjectName', SUBSTRING(REPLACE(REPLACE(SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1), CHAR(10), ' '), CHAR(13), ' '), 1, 512) AS statement_text
FROM sys.dm_exec_requests AS req
     CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST
ORDER BY cpu_time DESC;
GO
```

### <a name="the-cpu-issue-occurred-in-the-past"></a>A CPU-probléma a múltban történt

Ha a probléma a múltban történt, és a kiváltó okok elemzését szeretné elvégezni, használja a [query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)-t. Az adatbázis-hozzáféréssel rendelkező felhasználók a T-SQL használatával tudják lekérdezni a lekérdezési adattárolási adattárakat.  A Query Store alapértelmezett konfigurációi 1 órás részletességet használnak.  A következő lekérdezéssel tekintheti meg a magas CPU-fogyasztást igénylő lekérdezések tevékenységeit. Ez a lekérdezés az első 15 CPU-fogyasztási lekérdezést adja vissza.  Ne felejtse `rsi.start_time >= DATEADD(hour, -2, GETUTCDATE()`el megváltoztatni a következőt:

```sql
-- Top 15 CPU consuming queries by query hash
-- note that a query  hash can have many query id if not parameterized or not parameterized properly
-- it grabs a sample query text by min
WITH AggregatedCPU AS (SELECT q.query_hash, SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec, SUM(count_executions * avg_cpu_time / 1000.0)/ SUM(count_executions) AS avg_cpu_millisec, MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec, MAX(max_logical_io_reads) max_logical_reads, COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans, COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids, SUM(CASE WHEN rs.execution_type_desc='Aborted' THEN count_executions ELSE 0 END) AS Aborted_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Regular' THEN count_executions ELSE 0 END) AS Regular_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Exception' THEN count_executions ELSE 0 END) AS Exception_Execution_Count, SUM(count_executions) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text
                       FROM sys.query_store_query_text AS qt
                            JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                            JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                            JOIN sys.query_store_runtime_stats AS rs ON rs.plan_id=p.plan_id
                            JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=rs.runtime_stats_interval_id
                       WHERE rs.execution_type_desc IN ('Regular', 'Aborted', 'Exception')AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                       GROUP BY q.query_hash), OrderedCPU AS (SELECT query_hash, total_cpu_millisec, avg_cpu_millisec, max_cpu_millisec, max_logical_reads, number_of_distinct_plans, number_of_distinct_query_ids, total_executions, Aborted_Execution_Count, Regular_Execution_Count, Exception_Execution_Count, sampled_query_text, ROW_NUMBER() OVER (ORDER BY total_cpu_millisec DESC, query_hash ASC) AS RN
                                                              FROM AggregatedCPU)
SELECT OD.query_hash, OD.total_cpu_millisec, OD.avg_cpu_millisec, OD.max_cpu_millisec, OD.max_logical_reads, OD.number_of_distinct_plans, OD.number_of_distinct_query_ids, OD.total_executions, OD.Aborted_Execution_Count, OD.Regular_Execution_Count, OD.Exception_Execution_Count, OD.sampled_query_text, OD.RN
FROM OrderedCPU AS OD
WHERE OD.RN<=15
ORDER BY total_cpu_millisec DESC;
```

A problémás lekérdezések azonosítása után itt az ideje, hogy ezeket a lekérdezéseket a CPU-kihasználtság csökkentése érdekében állítsa be.  Ha nincs ideje a lekérdezések finomhangolására, a probléma megoldásához is dönthet úgy, hogy az adatbázis SLO-át frissíti.

## <a name="identify-io-performance-issues"></a>IO-teljesítménnyel kapcsolatos problémák azonosítása

Az IO-teljesítménnyel kapcsolatos problémák azonosításakor az IO-problémákhoz társított legfelső szintű várakozási típusok a következők:

- `PAGEIOLATCH_*`

  Adatfájl IO-problémái ( `PAGEIOLATCH_SH`beleértve `PAGEIOLATCH_EX`a `PAGEIOLATCH_UP`,,).  Ha a várakozási típus neve **i/o** értékkel rendelkezik, az IO-hibára mutat. Ha a lap zárolási várakozási neve nem rendelkezik **IO** -névvel, a probléma egy másik típusú problémára mutat (például tempdb-tartalom).

- `WRITE_LOG`

  Tranzakciós napló IO-problémái esetén.

### <a name="if-the-io-issue-is-occurring-right-now"></a>Ha az IO-probléma most következik be

A és a [sys. DM _exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) vagy [sys. DM _os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) használatával tekintheti `wait_time`meg a és a `wait_type` következőt:.

#### <a name="identify-data-and-log-io-usage"></a>Adatok és naplók IO-használatának azonosítása

A következő lekérdezés használatával azonosíthatja az adatokat és a napló IO-használatát. Ha az adatforgalom vagy a naplózási IO 80% fölött van, az azt jelenti, hogy a felhasználók a rendelkezésre álló IO-t használták az SQL DB szolgáltatási szintjéhez.

```sql
SELECT end_time, avg_data_io_percent, avg_log_write_percent
FROM sys.dm_db_resource_stats
ORDER BY end_time DESC;
```

Ha elérte az IO-korlátot, két lehetőség közül választhat:

- 1\. lehetőség: A számítási méret vagy a szolgáltatási szintek frissítése
- 2\. lehetőség: Azonosítsa és hangolja a legtöbb i/o-t használó lekérdezéseket.

#### <a name="view-buffer-related-io-using-the-query-store"></a>Pufferrel kapcsolatos IO megtekintése a lekérdezési tároló használatával

A 2. lehetőség esetében a következő lekérdezést használhatja a lekérdezési tárolóban a pufferekkel kapcsolatos IO-hoz a nyomon követett tevékenység utolsó két órájának megtekintéséhez:

```sql
-- top queries that waited on buffer
-- note these are finished queries
WITH Aggregated AS (SELECT q.query_hash, SUM(total_query_wait_time_ms) total_wait_time_ms, SUM(total_query_wait_time_ms / avg_query_wait_time_ms) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text, MIN(wait_category_desc) AS wait_category_desc
                    FROM sys.query_store_query_text AS qt
                         JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                         JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                         JOIN sys.query_store_wait_stats AS waits ON waits.plan_id=p.plan_id
                         JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=waits.runtime_stats_interval_id
                    WHERE wait_category_desc='Buffer IO' AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                    GROUP BY q.query_hash), Ordered AS (SELECT query_hash, total_executions, total_wait_time_ms, sampled_query_text, wait_category_desc, ROW_NUMBER() OVER (ORDER BY total_wait_time_ms DESC, query_hash ASC) AS RN
                                                        FROM Aggregated)
SELECT OD.query_hash, OD.total_executions, OD.total_wait_time_ms, OD.sampled_query_text, OD.wait_category_desc, OD.RN
FROM Ordered AS OD
WHERE OD.RN<=15
ORDER BY total_wait_time_ms DESC;
GO
```

#### <a name="view-total-log-io-for-writelog-waits"></a>A WRITELOG-várakozások összes naplózási i/o-értékének megtekintése

Ha a várakozás típusa `WRITELOG`a, a következő lekérdezéssel tekintheti meg az összes log IO by utasítást:

```sql
-- Top transaction log consumers
-- Adjust the time window by changing
-- rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
WITH AggregatedLogUsed
AS (SELECT q.query_hash,
           SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec,
           SUM(count_executions * avg_cpu_time / 1000.0) / SUM(count_executions) AS avg_cpu_millisec,
           SUM(count_executions * avg_log_bytes_used) AS total_log_bytes_used,
           MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec,
           MAX(max_logical_io_reads) max_logical_reads,
           COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans,
           COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Aborted' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Aborted_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Regular' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Regular_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Exception' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Exception_Execution_Count,
           SUM(count_executions) AS total_executions,
           MIN(qt.query_sql_text) AS sampled_query_text
    FROM sys.query_store_query_text AS qt
        JOIN sys.query_store_query AS q
            ON qt.query_text_id = q.query_text_id
        JOIN sys.query_store_plan AS p
            ON q.query_id = p.query_id
        JOIN sys.query_store_runtime_stats AS rs
            ON rs.plan_id = p.plan_id
        JOIN sys.query_store_runtime_stats_interval AS rsi
            ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
    WHERE rs.execution_type_desc IN ( 'Regular', 'Aborted', 'Exception' )
          AND rsi.start_time >= DATEADD(HOUR, -2, GETUTCDATE())
    GROUP BY q.query_hash),
     OrderedLogUsed
AS (SELECT query_hash,
           total_log_bytes_used,
           number_of_distinct_plans,
           number_of_distinct_query_ids,
           total_executions,
           Aborted_Execution_Count,
           Regular_Execution_Count,
           Exception_Execution_Count,
           sampled_query_text,
           ROW_NUMBER() OVER (ORDER BY total_log_bytes_used DESC, query_hash ASC) AS RN
    FROM AggregatedLogUsed)
SELECT OD.total_log_bytes_used,
       OD.number_of_distinct_plans,
       OD.number_of_distinct_query_ids,
       OD.total_executions,
       OD.Aborted_Execution_Count,
       OD.Regular_Execution_Count,
       OD.Exception_Execution_Count,
       OD.sampled_query_text,
       OD.RN
FROM OrderedLogUsed AS OD
WHERE OD.RN <= 15
ORDER BY total_log_bytes_used DESC;
GO
```

## <a name="identify-tempdb-performance-issues"></a>Teljesítménnyel `tempdb` kapcsolatos problémák azonosítása

Az IO-teljesítménnyel kapcsolatos problémák azonosításakor a `tempdb` `PAGELATCH_*` problémákhoz társított legfelső szintű várakozási típusok (nem `PAGEIOLATCH_*`). Azonban a `PAGELATCH_*` várakozások nem mindig azt jelentik, hogy `tempdb` van-e a tartalom.  Ez a várakozás azt is jelentheti, hogy a felhasználói objektum adatlapjainak tartalma az egyazon adatoldalra irányuló egyidejű kérelmek miatt van. A versengés `tempdb` további megerősítéséhez használja a [sys. DM _exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) annak ellenőrzéséhez, hogy a `2:x:y` wait_resource értéke kezdődik-e, ahol a 2 `x` `tempdb` a az adatbázis-azonosító, `y` a fájl azonosítója, és a lap azonosítója.  

A tempdb-tartalom esetében a gyakori módszer a-t használó `tempdb`alkalmazás kódjának csökkentése vagy újbóli írása.  A `tempdb` gyakori felhasználási területek a következők:

- Ideiglenes táblák
- Táblázat változói
- Tábla értékű paraméterek
- Version Store-használat (kifejezetten a hosszú ideig futó tranzakciókhoz társítva)
- Rendezéseket, kivonatoló illesztéseket és orsókat használó lekérdezési tervekkel rendelkező lekérdezések

### <a name="top-queries-that-use-table-variables-and-temporary-tables"></a>A táblázat változóit és az ideiglenes táblákat használó leggyakoribb lekérdezések

A következő lekérdezéssel azonosíthatja azokat a leggyakoribb lekérdezéseket, amelyek a táblázat változóit és az ideiglenes táblákat használják:

```sql
SELECT plan_handle, execution_count, query_plan
INTO #tmpPlan
FROM sys.dm_exec_query_stats
     CROSS APPLY sys.dm_exec_query_plan(plan_handle);
GO

WITH XMLNAMESPACES('http://schemas.microsoft.com/sqlserver/2004/07/showplan' AS sp)
SELECT plan_handle, stmt.stmt_details.value('@Database', 'varchar(max)') 'Database', stmt.stmt_details.value('@Schema', 'varchar(max)') 'Schema', stmt.stmt_details.value('@Table', 'varchar(max)') 'table'
INTO #tmp2
FROM(SELECT CAST(query_plan AS XML) sqlplan, plan_handle FROM #tmpPlan) AS p
    CROSS APPLY sqlplan.nodes('//sp:Object') AS stmt(stmt_details);
GO

SELECT t.plan_handle, [Database], [Schema], [table], execution_count
FROM(SELECT DISTINCT plan_handle, [Database], [Schema], [table]
     FROM #tmp2
     WHERE [table] LIKE '%@%' OR [table] LIKE '%#%') AS t
    JOIN #tmpPlan AS t2 ON t.plan_handle=t2.plan_handle;
```

### <a name="identify-long-running-transactions"></a>Hosszú ideig futó tranzakciók azonosítása

A hosszú ideig futó tranzakciók azonosításához használja a következő lekérdezést. A hosszú ideig futó tranzakciók megakadályozzák a verziók tárolási karbantartását.

```sql
SELECT DB_NAME(dtr.database_id) 'database_name',
       sess.session_id,
       atr.name AS 'tran_name',
       atr.transaction_id,
       transaction_type,
       transaction_begin_time,
       database_transaction_begin_time transaction_state,
       is_user_transaction,
       sess.open_transaction_count,
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   txt.text,
                                                                   (req.statement_start_offset / 2) + 1,
                                                                   ((CASE req.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(txt.text)
                                                                         ELSE
                                                                             req.statement_end_offset
                                                                     END - req.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) Running_stmt_text,
       recenttxt.text 'MostRecentSQLText'
FROM sys.dm_tran_active_transactions AS atr
    INNER JOIN sys.dm_tran_database_transactions AS dtr
        ON dtr.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_tran_session_transactions AS sess
        ON sess.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_exec_requests AS req
        ON req.session_id = sess.session_id
           AND req.transaction_id = sess.transaction_id
    LEFT JOIN sys.dm_exec_connections AS conn
        ON sess.session_id = conn.session_id
    OUTER APPLY sys.dm_exec_sql_text(req.sql_handle) AS txt
    OUTER APPLY sys.dm_exec_sql_text(conn.most_recent_sql_handle) AS recenttxt
WHERE atr.transaction_type != 2
      AND sess.session_id != @@spid
ORDER BY start_time ASC;
```

## <a name="identify-memory-grant-wait-performance-issues"></a>A memória-engedélyezés várakozási teljesítményével kapcsolatos problémák azonosítása

Ha a legfelső szintű várakozási típus `RESOURCE_SEMAHPORE` , és nem rendelkezik magas CPU-használati problémákkal, előfordulhat, hogy a memória-hozzáférés várakozási ideje van.

### <a name="determine-if-a-resource_semahpore-wait-is-a-top-wait"></a>Annak megállapítása `RESOURCE_SEMAHPORE` , hogy a várakozás egy felső várakozás

A következő lekérdezéssel megállapíthatja, hogy `RESOURCE_SEMAHPORE` a várakozás egy felső várakozás

```sql
SELECT wait_type,
       SUM(wait_time) AS total_wait_time_ms
FROM sys.dm_exec_requests AS req
    JOIN sys.dm_exec_sessions AS sess
        ON req.session_id = sess.session_id
WHERE is_user_process = 1
GROUP BY wait_type
ORDER BY SUM(wait_time) DESC;
```

### <a name="identity-high-memory-consuming-statements"></a>Identitás magas memória-felhasználására vonatkozó utasítások

A nagy mennyiségű memóriát használó utasítások azonosításához használja a következő lekérdezést:

```sql
SELECT IDENTITY(INT, 1, 1) rowId,
    CAST(query_plan AS XML) query_plan,
    p.query_id
INTO #tmp
FROM sys.query_store_plan AS p
    JOIN sys.query_store_runtime_stats AS r
        ON p.plan_id = r.plan_id
    JOIN sys.query_store_runtime_stats_interval AS i
        ON r.runtime_stats_interval_id = i.runtime_stats_interval_id
WHERE start_time > '2018-10-11 14:00:00.0000000'
      AND end_time < '2018-10-17 20:00:00.0000000';
GO
;WITH cte
AS (SELECT query_id,
        query_plan,
        m.c.value('@SerialDesiredMemory', 'INT') AS SerialDesiredMemory
    FROM #tmp AS t
        CROSS APPLY t.query_plan.nodes('//*:MemoryGrantInfo[@SerialDesiredMemory[. > 0]]') AS m(c) )
SELECT TOP 50
    cte.query_id,
    t.query_sql_text,
    cte.query_plan,
    CAST(SerialDesiredMemory / 1024. AS DECIMAL(10, 2)) SerialDesiredMemory_MB
FROM cte
    JOIN sys.query_store_query AS q
        ON cte.query_id = q.query_id
    JOIN sys.query_store_query_text AS t
        ON q.query_text_id = t.query_text_id
ORDER BY SerialDesiredMemory DESC;
```

### <a name="identify-the-top-10-active-memory-grants"></a>Az első 10 aktív memória-támogatás azonosítása

A következő lekérdezéssel azonosíthatja a 10 legfontosabb aktív memória-támogatást:

```sql
SELECT TOP 10
    CONVERT(VARCHAR(30), GETDATE(), 121) AS runtime,
       r.session_id,
       r.blocking_session_id,
       r.cpu_time,
       r.total_elapsed_time,
       r.reads,
       r.writes,
       r.logical_reads,
       r.row_count,
       wait_time,
       wait_type,
       r.command,
       OBJECT_NAME(txt.objectid, txt.dbid) 'Object_Name',
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   text,
                                                                   (r.statement_start_offset / 2) + 1,
                                                                   ((CASE r.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(text)
                                                                         ELSE
                                                                             r.statement_end_offset
                                                                     END - r.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) stmt_text,
       mg.dop,                                               --Degree of parallelism
       mg.request_time,                                      --Date and time when this query requested the memory grant.
       mg.grant_time,                                        --NULL means memory has not been granted
       mg.requested_memory_kb / 1024.0 requested_memory_mb,  --Total requested amount of memory in megabytes
       mg.granted_memory_kb / 1024.0 AS granted_memory_mb,   --Total amount of memory actually granted in megabytes. NULL if not granted
       mg.required_memory_kb / 1024.0 AS required_memory_mb, --Minimum memory required to run this query in megabytes.
       max_used_memory_kb / 1024.0 AS max_used_memory_mb,
       mg.query_cost,                                        --Estimated query cost.
       mg.timeout_sec,                                       --Time-out in seconds before this query gives up the memory grant request.
       mg.resource_semaphore_id,                             --Non-unique ID of the resource semaphore on which this query is waiting.
       mg.wait_time_ms,                                      --Wait time in milliseconds. NULL if the memory is already granted.
       CASE mg.is_next_candidate --Is this process the next candidate for a memory grant
           WHEN 1 THEN
               'Yes'
           WHEN 0 THEN
               'No'
           ELSE
               'Memory has been granted'
       END AS 'Next Candidate for Memory Grant',
       qp.query_plan
FROM sys.dm_exec_requests AS r
    JOIN sys.dm_exec_query_memory_grants AS mg
        ON r.session_id = mg.session_id
           AND r.request_id = mg.request_id
    CROSS APPLY sys.dm_exec_sql_text(mg.sql_handle) AS txt
    CROSS APPLY sys.dm_exec_query_plan(r.plan_handle) AS qp
ORDER BY mg.granted_memory_kb DESC;
```

## <a name="calculating-database-and-objects-sizes"></a>Az adatbázis és az objektumok méretének kiszámítása

A következő lekérdezés az adatbázis méretét adja vissza (megabájtban):

```sql
-- Calculates the size of the database.
SELECT SUM(CAST(FILEPROPERTY(name, 'SpaceUsed') AS bigint) * 8192.) / 1024 / 1024 AS DatabaseSizeInMB
FROM sys.database_files
WHERE type_desc = 'ROWS';
GO
```

A következő lekérdezés az adatbázis egyes objektumainak méretét adja vissza (megabájtban):

```sql
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>Kapcsolatok figyelése

A [sys. DM _exec_connections](https://msdn.microsoft.com/library/ms181509.aspx) nézet segítségével lekérheti az adott Azure SQL Database-kiszolgálóval létesített kapcsolatokra vonatkozó információkat, valamint az egyes kapcsolatok részleteit. Emellett a [sys. DM _exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx) nézet is hasznos lehet az összes aktív felhasználói kapcsolattal és belső feladatokkal kapcsolatos információk beolvasása során.
A következő lekérdezés adatokat kér le az aktuális kapcsolatban:

```sql
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [!NOTE]
> Ha a **sys. DM _exec_requests** és a **sys. DM _exec_sessions nézeteket**hajtja végre, ha az adatbázis- **állapot** megtekintésére engedélyt kapott az adatbázisban, akkor az adatbázison az összes végrehajtó munkamenet látható. Ellenkező esetben csak az aktuális munkamenet jelenik meg.

## <a name="monitor-resource-use"></a>Erőforrás-használat figyelése

A [SQL Database lekérdezési terheléselemző](sql-database-query-performance.md) és a [lekérdezési tároló](https://msdn.microsoft.com/library/dn817826.aspx)használatával figyelheti az erőforrás-használatot.

Ezt a két nézetet is használhatja a használat figyeléséhez:

- [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
- [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

### <a name="sysdm_db_resource_stats"></a>sys.dm_db_resource_stats

A [sys. DM _db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) nézetet minden SQL-adatbázisban használhatja. A **sys. DM _db_resource_stats** nézet a legutóbbi erőforrás-használati adatmennyiséget jeleníti meg a szolgáltatási szintjéhez képest. A CPU, az adatio, a log writes és a memória átlagos százalékos arányát 15 másodpercenként rögzíti a rendszer, és 1 órára tartja karban.

Mivel ez a nézet részletesebben megtekinti az erőforrás-használatot, a **sys. DM _db_resource_stats** először a jelenlegi állapotú elemzésekhez vagy hibaelhárításhoz használhatja. Ez a lekérdezés például az aktuális adatbázis átlagos és maximális erőforrás-felhasználását mutatja az elmúlt órában:

```sql
SELECT  
    AVG(avg_cpu_percent) AS 'Average CPU use in percent',
    MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
    AVG(avg_data_io_percent) AS 'Average data IO in percent',
    MAX(avg_data_io_percent) AS 'Maximum data IO in percent',
    AVG(avg_log_write_percent) AS 'Average log write use in percent',
    MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
    AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
    MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
FROM sys.dm_db_resource_stats;  
```

További lekérdezésekért tekintse meg a [sys. DM _db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)található példákat.

### <a name="sysresource_stats"></a>sys.resource_stats

A **Master** adatbázis [sys. resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) nézete további információkat tartalmaz, amelyek segítségével figyelheti az SQL-adatbázis teljesítményét az adott szolgáltatási rétegben és a számítási méretekben. Az adatok gyűjtése 5 percenként történik, és körülbelül 14 napig tart fenn. Ez a nézet hasznos lehet a hosszú távú múltbeli elemzésekhez, hogy az SQL-adatbázis hogyan használja az erőforrásokat.

A következő gráf a prémium szintű adatbázisok CPU-erőforrásait mutatja be a P2 számítási mérettel a hét minden órájában. Ez a gráf hétfőn kezdődik, 5 munkanapot mutat, majd egy hétvégét jeleníti meg, ha sokkal kevesebb történik az alkalmazásnál.

![SQL Database-erőforrások használata](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

Az adatokból ez az adatbázis jelenleg egy maximális CPU-terheléssel rendelkezik, amely a P2 számítási mérethez képest körülbelül 50%-os CPU-használatot tartalmaz (keddenként délig). Ha a CPU a domináns tényező az alkalmazás erőforrás-profiljában, akkor dönthet úgy, hogy a P2 a megfelelő számítási méret, amely garantálja, hogy a munkaterhelés mindig megfelel. Ha az alkalmazás várhatóan idővel növekszik, érdemes lehet egy extra erőforrás-puffert megadni, hogy az alkalmazás ne érje el a teljesítmény-szintű korlátot. A számítási méret növelésével elkerülhető az ügyfelek által látható hibák, amelyek akkor fordulnak elő, ha egy adatbázis nem rendelkezik elegendő teljesítménnyel a kérelmek hatékony feldolgozásához, különösen a késésre érzékeny környezetekben. Ilyen például egy olyan adatbázis, amely támogatja az adatbázis-hívások eredményei alapján weblapokat festő alkalmazásokat.

Más típusú alkalmazások esetében eltérő lehet a gráf értelmezése. Ha például egy alkalmazás minden nap feldolgozza a bérszámfejtési adatok mennyiségét, és ugyanazzal a diagrammal rendelkezik, akkor előfordulhat, hogy az ilyen "batch-feladatok" modell a P1 számítási méretnél is megfelelő. A P1 számítási méret 100 DTU, a P2 számítási mérethez képest 200 DTU. A P1 számítási méret a P2 számítási méret felét biztosítja. Így a 50%-os CPU-használat a P1-ben egyenlő a 100 százalékos CPU-használattal. Ha az alkalmazás nem rendelkezik időtúllépéssel, akkor előfordulhat, hogy nem számít, hogy a feladatok 2 órát vagy 2,5 órát vesznek igénybe, ha még ma végeznek. Az ebben a kategóriában lévő alkalmazások valószínűleg P1 számítási méretet használhatnak. Kihasználhatja azt a tényt, hogy az erőforrás-használatnál a nap folyamán időszakok vannak, így a "nagy csúcsok" a nap folyamán később a vályúba kerülhetnek. A P1 számítási méret hasznos lehet az adott típusú alkalmazáshoz (és pénzt takaríthat meg), feltéve, hogy a feladatok minden nap időben befejeződik.

Azure SQL Database az egyes kiszolgálókon lévő összes aktív adatbázishoz felhasználható erőforrás-információkat tesz elérhetővé a főadatbázis **sys. resource_stats** nézetében. A táblázatban szereplő adatokat 5 percenként összesíti a rendszer. Az alapszintű, a standard és a prémium szintű szolgáltatási csomaggal az adatok több mint 5 percet vehetnek fel a táblázatban, így az adatok nem közel valós idejű elemzéshez használhatók a korábbi elemzésekhez. A **sys. resource_stats** nézet lekérdezésével megtekintheti egy adatbázis közelmúltbeli előzményeit, és ellenőrizheti, hogy a kiválasztott foglalás a szükséges teljesítményt választotta-e.

> [!NOTE]
> Az alábbi példákban a **sys. resource_stats** lekérdezéséhez csatlakoznia kell a SQL Database-kiszolgáló **Master** adatbázisához.

Ez a példa bemutatja, hogyan jelennek meg az ebben a nézetben látható adatterületek:

```sql
SELECT TOP 10 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC
```

![A sys. resource_stats katalógus nézete](./media/sql-database-performance-guidance/sys_resource_stats.png)

A következő példa különböző módokon mutatja be, hogy a **sys. resource_stats** katalógus nézettel hogyan kérhet információt az SQL-adatbázis erőforrásainak használatáról:

1. A következő lekérdezés futtatásával megtekintheti a múlt heti erőforrás-használatot az adatbázis userdb1:

    ```sql
    SELECT *
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND
        start_time > DATEADD(day, -7, GETDATE())
    ORDER BY start_time DESC;
    ```

2. Annak kiértékeléséhez, hogy a munkaterhelés milyen mértékben illeszkedik a számítási mérethez, meg kell vizsgálnia az erőforrás-metrikák egyes aspektusait: CPU, olvasás, írás, feldolgozók száma és munkamenetek száma. Itt egy átdolgozott lekérdezés a **sys. resource_stats** használatával a következő erőforrás-metrikák átlagos és maximális értékének jelentéséhez:

    ```sql
    SELECT
        avg(avg_cpu_percent) AS 'Average CPU use in percent',
        max(avg_cpu_percent) AS 'Maximum CPU use in percent',
        avg(avg_data_io_percent) AS 'Average physical data IO use in percent',
        max(avg_data_io_percent) AS 'Maximum physical data IO use in percent',
        avg(avg_log_write_percent) AS 'Average log write use in percent',
        max(avg_log_write_percent) AS 'Maximum log write use in percent',
        avg(max_session_percent) AS 'Average % of sessions',
        max(max_session_percent) AS 'Maximum % of sessions',
        avg(max_worker_percent) AS 'Average % of workers',
        max(max_worker_percent) AS 'Maximum % of workers'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

3. Az egyes erőforrás-metrikák átlagos és maximális értékeivel kapcsolatos információk alapján kiértékelheti, hogy a munkaterhelés milyen mértékben illeszkedik a kiválasztott számítási mérethez. A **sys. resource_stats** átlagos értékei általában jó alapkonfigurációt biztosítanak a célként megadott mérethez. Az elsődleges mérési sticknek kell lennie. Előfordulhat például, hogy a standard szolgáltatási szintet S2 számítási mérettel használja. A CPU és az i/o-olvasások és írások átlagos kihasználtsági aránya 40% alatti, a feldolgozók átlagos száma pedig 50, a munkamenetek átlagos száma pedig 200. A munkaterhelés az S1 számítási méretéhez igazodhat. Könnyen megtekinthető, hogy az adatbázis megfelel-e a munkavégző és a munkamenet korlátainak. Ha szeretné megtekinteni, hogy egy adatbázis kisebb számítási mérettel rendelkezik-e a CPU-ra, az olvasásra és az írásra vonatkozóan, ossza meg az alacsonyabb számítási méret DTU számát az aktuális számítási méret DTU számával, majd szorozza meg az eredményt a 100 értékkel:

    ```S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40```

    Az eredmény a két számítási méret közötti relatív teljesítménybeli különbség a százalékban kifejezve. Ha az erőforrás-használat nem haladja meg ezt az összeget, a munkaterhelés az alacsonyabb számítási mérethez fér. Azonban meg kell vizsgálnia az erőforrás-használati értékek minden tartományát, és meg kell határoznia, hogy az adatbázis-munkaterhelés milyen gyakran fér hozzá az alacsonyabb számítási mérethez. Az alábbi lekérdezés az erőforrás-dimenziók százalékos arányát adja vissza az ebben a példában kiszámított 40 százalékos küszöbérték alapján:

   ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Az adatbázis-szolgáltatási szintje alapján eldöntheti, hogy a számítási feladat megfelel-e az alacsonyabb számítási méretnek. Ha az adatbázis számítási feladatának célja 99,9 százalék, és az előző lekérdezés mindhárom erőforrás-dimenziónál meghaladja a 99,9 százalék értéket, a munkaterhelés valószínűleg az alacsonyabb számítási mérethez fog illeszkedni.

    Az illeszkedő százalékos arány áttekintésével betekintést nyerhet, hogy a következő nagyobb számítási méretre kell-e lépnie, hogy megfeleljen a célnak. A userdb1 például a következő CPU-használatot jeleníti meg az elmúlt héten:

   | Átlagos CPU-százalék | CPU maximális százaléka |
   | --- | --- |
   | 24.5 |100.00 |

    Az átlagos processzor a számítási méret korlátjának egy negyedét jelenti, ami jól illeszkedik az adatbázis számítási méretéhez. A maximális érték azonban azt mutatja, hogy az adatbázis eléri a számítási méret korlátját. A következő nagyobb számítási méretre kell lépnie? Tekintse meg, hogy a munkaterhelés hányszor éri el a 100 százalékot, majd hasonlítsa össze az adatbázis-munkaterhelés célkitűzéssel.

    ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Ha a lekérdezés a három erőforrás-dimenzió esetében 99,9 százaléknál kisebb értéket ad vissza, akkor a következő nagyobb számítási méretre kell áthelyeznie, vagy az alkalmazás-hangolási technikákat kell használnia az SQL-adatbázis terhelésének csökkentése érdekében.

4. Ez a gyakorlat azt is figyelembe veszi, hogy a tervezett számítási feladatok egyre nagyobb mértékben növekednek a jövőben.

Rugalmas készletek esetén az ebben a szakaszban leírt technikákkal az adatbáziskészlet egyes adatbázisait is figyelheti. De ugyanígy figyelheti az adatbáziskészlet egészét is. További információkért lásd: [Rugalmas készlet figyelése és kezelése](sql-database-elastic-pool-manage-portal.md).

### <a name="maximum-concurrent-requests"></a>Egyidejű kérelmek maximális száma

Az egyidejű kérelmek számának megtekintéséhez futtassa ezt a Transact-SQL-lekérdezést az SQL-adatbázison:

    ```sql
    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R;
    ```

Egy helyszíni SQL Server adatbázis munkaterhelésének elemzéséhez módosítsa ezt a lekérdezést az elemezni kívánt adatbázis szűréséhez. Ha például egy MyDatabase nevű helyszíni adatbázissal rendelkezik, akkor ez a Transact-SQL-lekérdezés az adatbázisban egyidejű kérelmek számát adja vissza:

    ```sql
    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R
    INNER JOIN sys.databases D ON D.database_id = R.database_id
    AND D.name = 'MyDatabase';
    ```

Ez csak egy pillanatkép egy adott időpontban. Ha jobban meg szeretné ismerni a munkaterhelést és az egyidejű kérelmekre vonatkozó követelményeket, sok mintát kell gyűjtenie az idő múlásával.

### <a name="maximum-concurrent-logins"></a>Egyidejű bejelentkezések maximális száma

A felhasználói és az alkalmazási mintákat elemezheti a bejelentkezések gyakoriságának megismeréséhez. A valós terheléseket is futtathatja tesztkörnyezetben, így biztos lehet benne, hogy nem üti ezt a cikket, vagy más korlátozásokat sem. Nincs egyetlen lekérdezési vagy dinamikus felügyeleti nézet (DMV), amely megjeleníti az egyidejű bejelentkezések számát vagy előzményeit.

Ha több ügyfél ugyanazt a csatlakoztatási karakterláncot használja, akkor a szolgáltatás minden bejelentkezést hitelesít. Ha 10 felhasználó egyidejűleg csatlakozik egy adatbázishoz ugyanazzal a felhasználónévvel és jelszóval, a rendszer 10 egyidejű bejelentkezést használ. Ez a korlát csak a bejelentkezés és a hitelesítés időtartamára vonatkozik. Ha ugyanaz a 10 felhasználó egymás után csatlakozik az adatbázishoz, az egyidejű bejelentkezések száma soha nem lehet nagyobb, mint 1.

> [!NOTE]
> Ez a korlát jelenleg nem vonatkozik a rugalmas készletekben található adatbázisokra.

### <a name="maximum-sessions"></a>Munkamenetek maximális száma

Az aktuális aktív munkamenetek számának megtekintéséhez futtassa ezt a Transact-SQL-lekérdezést az SQL-adatbázison:

    SELECT COUNT(*) AS [Sessions]
    FROM sys.dm_exec_connections

Ha helyszíni SQL Server munkaterhelést elemez, módosítsa a lekérdezést úgy, hogy az egy adott adatbázisra összpontosítsanak. Ez a lekérdezés segít meghatározni a lehetséges munkamenet-igényeket az adatbázishoz, ha azt tervezi, hogy áthelyezi a Azure SQL Databaseba.

    SELECT COUNT(*)  AS [Sessions]
    FROM sys.dm_exec_connections C
    INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
    INNER JOIN sys.databases D ON (D.database_id = S.database_id)
    WHERE D.name = 'MyDatabase'

Ezek a lekérdezések ismét egy időponthoz tartozó értéket adnak vissza. Ha az idő múlásával több mintát gyűjt, akkor a munkamenet-használat legjobb ismerete lesz.

SQL Database elemzéshez a munkamenetek korábbi statisztikáit a [sys. resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) nézet lekérdezésével és a **active_session_count** oszlop áttekintésével érheti el.

## <a name="monitoring-query-performance"></a>A lekérdezés teljesítményének figyelése

A lassú vagy hosszú ideig futó lekérdezések jelentős rendszererőforrásokat használhatnak. Ez a szakasz bemutatja, hogyan használhatók a dinamikus felügyeleti nézetek néhány gyakori lekérdezési teljesítménnyel kapcsolatos probléma észlelésére. Egy régebbi, de továbbra is hasznos útmutató a hibaelhárításhoz [: SQL Server 2008-](https://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) cikkben a Microsoft TechNet webhelyén található teljesítményproblémák hibaelhárítása.

### <a name="finding-top-n-queries"></a>Legfontosabb N lekérdezések keresése

Az alábbi példa az első öt lekérdezés adatait adja vissza átlagos CPU-idő szerint rangsorolva. Ez a példa a lekérdezési kivonat alapján összesíti a lekérdezéseket, így a logikailag egyenértékű lekérdezések a halmozott erőforrás-felhasználás szerint vannak csoportosítva.

    ```sql
    SELECT TOP 5 query_stats.query_hash AS "Query Hash",
       SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
       MIN(query_stats.statement_text) AS "Statement Text"
    FROM
       (SELECT QS.*,
        SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
        ((CASE statement_end_offset
           WHEN -1 THEN DATALENGTH(ST.text)
           ELSE QS.statement_end_offset END
           - QS.statement_start_offset)/2) + 1) AS statement_text
    FROM sys.dm_exec_query_stats AS QS
    CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
    GROUP BY query_stats.query_hash
    ORDER BY 2 DESC;
    ```

### <a name="monitoring-blocked-queries"></a>Letiltott lekérdezések figyelése

A lassú vagy hosszan futó lekérdezések hozzájárulhatnak a túlzott erőforrás-használathoz, és a letiltott lekérdezések következményeként is. A blokkolás oka lehet gyenge az alkalmazások megtervezése, a rossz lekérdezési csomagok, a hasznos indexek hiánya stb. A sys. DM _tran_locks nézet segítségével lekérheti a Azure SQL Database aktuális zárolási tevékenységével kapcsolatos információkat. Például a kód: [sys. DM _tran_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx) SQL Server Books Online.

### <a name="monitoring-query-plans"></a>A lekérdezési csomagok figyelése

A nem hatékony lekérdezési terv is növelheti a CPU-felhasználást. Az alábbi példa a [sys. DM _exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) nézetet használja annak meghatározásához, hogy melyik lekérdezés használja a legátfogóbb CPU-t.

    ```sql
    SELECT
       highest_cpu_queries.plan_handle,
       highest_cpu_queries.total_worker_time,
       q.dbid,
       q.objectid,
       q.number,
       q.encrypted,
       q.[text]
    FROM
       (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
    ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
    CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
    ORDER BY highest_cpu_queries.total_worker_time DESC;
    ```

## <a name="see-also"></a>Lásd még

[Bevezetés a SQL Databaseba](sql-database-technical-overview.md)

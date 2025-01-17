---
title: Azure Database for MySQL-hez korlátozásai
description: Ez a cikk ismerteti a korlátozások az Azure Database for MySQL-hez, például kapcsolat és a tárolási motort lehetőségek számát.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/6/2018
ms.openlocfilehash: 9088e9ad98633b46dc3a7f0ee9002a0dd9fc5a55
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65551877"
---
# <a name="limitations-in-azure-database-for-mysql"></a>Azure Database for MySQL-hez korlátozásai
A következő szakaszok ismertetik a kapacitást, motor támogatása, jogosultság támogatja, adatok adatkezelési utasítást, és az adatbázis-szolgáltatás működési korlátai. További tájékoztatás [általános korlátozások](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) a MySQL-adatbázismotor alkalmazható.

## <a name="maximum-connections"></a>Kapcsolatok maximális száma
Tarifacsomag és virtuális magok száma kapcsolatok maximális száma a következők: 

|**Tarifacsomag**|**vCore(s)**| **Kapcsolatok maximális száma**|
|---|---|---|
|Alapszintű| 1| 50|
|Alapszintű| 2| 100|
|Általános rendeltetés| 2| 300|
|Általános rendeltetés| 4| 625|
|Általános rendeltetés| 8| 1250|
|Általános rendeltetés| 16| 2500|
|Általános rendeltetés| 32| 5000|
|Általános rendeltetés| 64| 10000|
|Memóriára optimalizált| 2| 600|
|Memóriára optimalizált| 4| 1250|
|Memóriára optimalizált| 8| 2500|
|Memóriára optimalizált| 16| 5000|
|Memóriára optimalizált| 32| 10000|

Amikor kapcsolatokat meghaladják a korlátot, a következő hiba jelenhet meg:
> ERROR 1040 (08004): Túl sok kapcsolat

## <a name="storage-engine-support"></a>Storage engine-támogatás

### <a name="supported"></a>Támogatott
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MEMÓRIA](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Nem támogatott
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARCHÍVUM](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [ÖSSZEVONT](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>Jogosultság-támogatás

### <a name="unsupported"></a>Nem támogatott
- Adatbázis-szerepkör: Számos kiszolgálói paraméterek és beállítások véletlenül rontják a kiszolgáló teljesítményét vagy amelyet tagadni kell a Célrendszerben ACID tulajdonságait. Emiatt a szolgáltatás integritás és SLA-t, a termék szintjén fenntartására, ezt a szolgáltatást nem biztosít az adatbázis-szerepkör. Az alapértelmezett felhasználói fiók, amely jön létre egy új adatbázis-példány létrehozásakor, lehetővé teszi, hogy a felhasználó hajtani a legtöbb DDL és DML utasítást, a felügyelt adatbázis példányában. 
- FELÜGYELŐI jogosultság: Hasonlóképpen [FELÜGYELŐI jogosultságok](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) is korlátozva.
- DEFINER: Hozhat létre felügyelői jogosultságokat igényel, és korlátozott. Ha egy biztonsági mentési adatokat importál, távolítsa el a `CREATE DEFINER` parancsok manuális vagy a `--skip-definer` parancsot a mysqldump végrehajtásakor.

## <a name="data-manipulation-statement-support"></a>Adatok adatkezelési utasítás támogatása

### <a name="supported"></a>Támogatott
- `LOAD DATA INFILE` támogatott, de a `[LOCAL]` paramétert kell a megadott és átirányítva, UNC elérési út (az Azure storage csatlakoztatása az SMB-n keresztül).

### <a name="unsupported"></a>Nem támogatott
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>Működési korlátai

### <a name="scale-operations"></a>A skálázási műveletek
- Dinamikus méretezés, és onnan az alapszintű tarifacsomagban is kapható jelenleg nem támogatott.
- Kiszolgáló-tároló méretének csökkentése nem támogatott.

### <a name="server-version-upgrades"></a>Kiszolgáló verziófrissítések
- Fő database engine verziói között az automatikus migrálási jelenleg nem támogatott. Ha szeretné, a következő fő verzióra való frissítéshez, egy [memóriakép és visszaállítás](./concepts-migrate-dump-restore.md) , hogy egy kiszolgálót, amely az új motor verziójával lett létrehozva.

### <a name="point-in-time-restore"></a>Időponthoz kötött visszaállítás
- A PITR a funkció használatakor az ugyanezzel a konfigurációval, mint a kiszolgáló-alapú, az új kiszolgáló jön létre.
- Törölt kiszolgáló visszaállítása nem támogatott.

### <a name="vnet-service-endpoints"></a>Virtuális hálózati Szolgáltatásvégpontok
- Virtuális hálózati Szolgáltatásvégpontok támogatása csak az általános célú és memóriahasználatra optimalizált kiszolgálók esetében érhető el.

### <a name="storage-size"></a>Tároló mérete
- Tekintse meg [tarifacsomagok](concepts-pricing-tiers.md) a tároló mérete vonatkozó korlátok tarifacsomag.

## <a name="current-known-issues"></a>Jelenlegi ismert problémák
- MySQL server-példány a megfelelő verziója a kapcsolat létrejötte után jeleníti meg. A megfelelő kiszolgáló példány motor verziója, amelyet a `select version();` parancsot.

## <a name="next-steps"></a>További lépések
- [Az egyes szolgáltatásszinteken elérhető](concepts-pricing-tiers.md)
- [MySQL-adatbázis támogatott verziók](concepts-supported-versions.md)

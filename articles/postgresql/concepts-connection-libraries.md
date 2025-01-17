---
title: Az Azure Database for PostgreSQL – egyetlen kiszolgáló csatlakozási kódtárak
description: Ez a cikk azt ismerteti, több kódtárak és illesztőprogramokat, hogy a fejlesztők mikor a kódolási történő csatlakozásról és lekérdezésről, Azure Database for PostgreSQL – egyetlen kiszolgáló alkalmazások.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 85110126f9bdec225b1644860814cd89832132a1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65073589"
---
# <a name="connection-libraries-for-azure-database-for-postgresql---single-server"></a>Az Azure Database for PostgreSQL – egyetlen kiszolgáló csatlakozási kódtárak
Ez a cikk felsorolja a kódtárak és illesztőprogramokat, amelyek a fejlesztők a csatlakozhat, és a PostgreSQL-hez készült Azure Database-adatbázis lekérdezéséhez alkalmazások fejlesztéséhez.

## <a name="client-interfaces"></a>Ügyféloldali felületei
PostgreSQL-kiszolgálóhoz való kapcsolódáshoz használt legtöbb nyelvi klienskódtárak külső projektek és egymástól függetlenül vannak osztva. A felsorolt kódtárak használata támogatott a Windows, Linux és Mac platformokon, a PostgreSQL-hez készült Azure-adatbázishoz szeretne csatlakozni. A következő lépések szakasz néhány rövid példa láthatók.

| **Nyelv** | **Ügyféloldali felület** | **További információ** | **Letöltés** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | DB API 2.0-kompatibilis | [Letöltés](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://secure.php.net/manual/en/book.pgsql.php) | Adatbázis-bővítmény | [Telepítés](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [PG npm-csomag](https://www.npmjs.com/package/pg) | Tiszta JavaScript nem blokkoló ügyfél | [Telepítés](https://www.npmjs.com/package/pg) |
| Java | [JDBC](https://jdbc.postgresql.org/) | 4\. típus JDBC-illesztőprogram | [Letöltés](https://jdbc.postgresql.org/download.html)  |
| Ruby | [PG gem](https://deveiate.org/code/pg/) | Ruby-kapcsolat | [Letöltés](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Indítás | [Package pq](https://godoc.org/github.com/lib/pq) | Pure Go postgres illesztőprogramot | [Telepítés](https://github.com/lib/pq/blob/master/README.md) |
| C\#ÉS .NET | [Npgsql](https://www.npgsql.org/) | Az ADO.NET-szolgáltató | [Letöltés](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | ODBC-illesztő | [Letöltés](https://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Elsődleges C nyelv felület | Tartalmazza |
| C++ | [libpqxx](http://pqxx.org/) | Új stílusú C++ felület | [Letöltés](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>További lépések
Olvassa el az alábbi gyorsútmutatókkal a történő csatlakoztatása és lekérdezése az Azure Database for postgresql-hez tetszőleges nyelven használatával:

[Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md)  |  [.NET (C#)](./connect-csharp.md) | [go](./connect-go.md)

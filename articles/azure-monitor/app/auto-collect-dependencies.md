---
title: Azure Application Insights – függőségi automatikus adatgyűjtés | Microsoft Docs
description: A függőségek automatikus összegyűjtése és megjelenítése Application Insights
services: application-insights
documentationcenter: .net
author: nikmd23
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 04/29/2019
ms.reviewer: mbullwin
ms.author: nimolnar
ms.openlocfilehash: 839ab291a99de646053b638520ce43f459d5c41f
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297014"
---
# <a name="dependency-auto-collection"></a>Függőségek automatikus összegyűjtése

Az alábbiakban látható a függőségi hívások jelenleg támogatott listája, amelyeket az alkalmazás kódjának további módosítása nélkül észlel a rendszer. Ezek a függőségek a Application Insights [Application Map](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) és a [Transaction Diagnostics](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) nézeteiben láthatók. Ha a függőség nem szerepel az alábbi listán, akkor is manuálisan nyomon követheti a függőségi [hívással](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

## <a name="net"></a>.NET

| Alkalmazás-keretrendszerek| Verziók |
| ------------------------|----------|
| ASP.NET webűrlapok | 4.5 + |
| ASP.NET, MVC | 4+ |
| ASP.NET WebAPI | 4.5 + |
| ASP.NET-mag | 1.1 + |
| <b>Kommunikációs kódtárak</b> |
| [HttpClient](https://www.microsoft.com/net/) | 4.5 +, .NET Core 1.1 + |
| [SqlClient](https://www.nuget.org/packages/System.Data.SqlClient) | .NET Core 1.0 +, NuGet 4.3.0 |
| [EventHubs Client SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1.1.0 |
| [ServiceBus Client SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) | 3.0.0 |
| <b>Storage-ügyfelek</b>|  |
| ADO.NET | 4.5 + |

## <a name="java"></a>Java
| Alkalmazás-kiszolgálók | Verziók |
|-------------|----------|
| [Tomcat](https://tomcat.apache.org/) | 7, 8 | 
| [JBoss EAP](https://developers.redhat.com/products/eap/download/) | 6, 7 |
| [Jetty](https://www.eclipse.org/jetty/) | 9 |
| <b>Alkalmazás-keretrendszerek</b> |  |
| [Spring](https://spring.io/) | 3.0 |
| [Spring boot](https://spring.io/projects/spring-boot) | 1.5.9 +<sup>*</sup> |
| Java servlet | 3.1 + |
| <b>Kommunikációs kódtárak</b> |  |
| [Apache HTTP-ügyfél](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) | 4.3 +<sup>†</sup> |
| <b>Storage-ügyfelek</b> | |
| [SQL Server]( https://mvnrepository.com/artifact/com.microsoft.sqlserver/mssql-jdbc) | 1+<sup>†</sup> |
| [PostgreSQL (bétaverziós támogatás)](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/CHANGELOG.md#version-240-beta) | |
| [Oracle]( https://www.oracle.com/technetwork/database/application-development/jdbc/downloads/index.html) | 1+<sup>†</sup> |
| [MySql]( https://mvnrepository.com/artifact/mysql/mysql-connector-java) | 1+<sup>†</sup> |
| <b>Naplózási kódtárak</b> | |
| [Logback](https://logback.qos.ch/) | 1+ |
| [Log4j](https://logging.apache.org/log4j/) | 1,2 + |
| <b>Metrikák kódtárai</b> |  |
| JMX | 1.0 + |

> [!NOTE]
> \* Az újraaktív program támogatásának kivételével.
> <br>† A JVM- [ügynök](https://docs.microsoft.com/azure/application-insights/app-insights-java-agent#install-the-application-insights-agent-for-java)telepítését igényli.

## <a name="nodejs"></a>Node.js

| Kommunikációs kódtárak | Verziók |
| ------------------------|----------|
| [HTTP](https://nodejs.org/api/http.html), [HTTPS](https://nodejs.org/api/https.html) | 0,10 + |
| <b>Storage-ügyfelek</b> | |
| [Redis](https://www.npmjs.com/package/redis) | 2. x |
| [MongoDb](https://www.npmjs.com/package/mongodb); [MongoDb mag](https://www.npmjs.com/package/mongodb-core) | 2. x-3. x |
| [MySQL](https://www.npmjs.com/package/mysql) | 2.0.0-2.16. x |
| [PostgreSql](https://www.npmjs.com/package/pg); | 6.x - 7.x |
| [pg-pool](https://www.npmjs.com/package/pg-pool) | 1. x-2. x |
| <b>Naplózási kódtárak</b> | |
| [konzol](https://nodejs.org/api/console.html) | 0,10 + |
| [Bunyan](https://www.npmjs.com/package/bunyan) | 1. x |
| [Winston](https://www.npmjs.com/package/winston) | 2. x-3. x |

## <a name="javascript"></a>JavaScript

| Kommunikációs kódtárak | Verziók |
| ------------------------|----------|
| [XMLHttpRequest](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) | Összes |

## <a name="next-steps"></a>További lépések

- Egyéni függőségi követés beállítása a [.net](../../azure-monitor/app/asp-net-dependencies.md)-hez.
- Egyéni függőség-követés beállítása a [Javához](../../azure-monitor/app/java-agent.md).
- [Egyéni függőségi telemetria írása](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- Lásd [](../../azure-monitor/app/data-model.md) : adatmodell Application Insights típusokhoz és adatmodellekhez.
- Tekintse [](../../azure-monitor/app/platforms.md) meg Application Insights által támogatott platformokat.

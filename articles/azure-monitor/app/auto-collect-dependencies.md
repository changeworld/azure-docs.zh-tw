---
title: Azure Application Insights - 相依性自動收集 | Microsoft Docs
description: Application Insights 會自動收集相依項目，並將其視覺化
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: reference
author: nikmd23
ms.author: nimolnar
ms.date: 04/29/2019
ms.reviewer: mbullwin
ms.openlocfilehash: e774690fae2314b8db6e14f19a67c7ae1ce3a203
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064269"
---
# <a name="dependency-auto-collection"></a>相依性自動收集

以下是目前支援的相依性呼叫清單，系統會自動將這些呼叫偵測為相依性，而且不需要修改應用程式的任何其他程式碼。 這些相依項目會在 Application Insights 中的[應用程式對應](https://docs.microsoft.com/azure/application-insights/app-insights-app-map)和[交易診斷](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics)檢視中加以視覺化。 如果您的相依性不在以下清單中，您仍可以透過[追蹤相依性呼叫](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency)，以手動方式進行追蹤。

## <a name="net"></a>.NET

| 應用程式架構| 版本 |
| ------------------------|----------|
| ASP.NET WebForm | 4.5+ |
| ASP.NET MVC | 4+ |
| ASP.NET WebAPI | 4.5+ |
| ASP.NET Core | 1.1+ |
| <b> 通訊程式庫</b> |
| [HttpClient](https://www.microsoft.com/net/) | 4.5+、.NET Core 1.1+ |
| [SqlClient](https://www.nuget.org/packages/System.Data.SqlClient) | .NET Core 1.0+、NuGet 4.3.0 |
| [EventHubs 用戶端 SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1.1.0 |
| [ServiceBus 用戶端 SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) | 3.0.0 |
| <b>儲存體用戶端</b>|  |
| ADO.NET | 4.5+ |

## <a name="java"></a>Java
| 應用程式伺服器 | 版本 |
|-------------|----------|
| [Tomcat](https://tomcat.apache.org/) | 7、8 | 
| [JBoss EAP](https://developers.redhat.com/products/eap/download/) | 6、7 |
| [Jetty](https://www.eclipse.org/jetty/) | 9 |
| <b>應用程式架構</b> |  |
| [Spring](https://spring.io/) | 3.0 |
| [Spring Boot](https://spring.io/projects/spring-boot) | 1.5.9+<sup>*</sup> |
| Java Servlet | 3.1+ |
| <b>通訊程式庫</b> |  |
| [Apache HTTP 用戶端](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) | 4.3+<sup>†</sup> |
| <b>儲存體用戶端</b> | |
| [SQL Server]( https://mvnrepository.com/artifact/com.microsoft.sqlserver/mssql-jdbc) | 1+<sup>†</sup> |
| [于 postgresql （搶鮮版（Beta）支援）](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/CHANGELOG.md#version-240-beta) | |
| [Oracle]( https://www.oracle.com/technetwork/database/application-development/jdbc/downloads/index.html) | 1+<sup>†</sup> |
| [MySql]( https://mvnrepository.com/artifact/mysql/mysql-connector-java) | 1+<sup>†</sup> |
| <b>記錄程式庫</b> | |
| [Logback](https://logback.qos.ch/) | 1+ |
| [Log4j](https://logging.apache.org/log4j/) | 1.2+ |
| <b>計量程式庫</b> |  |
| JMX | 1.0+ |

> [!NOTE]
> *不包含回應式程式設計支援。
> <br>†需要安裝 [JVM 代理程式](https://docs.microsoft.com/azure/application-insights/app-insights-java-agent#install-the-application-insights-agent-for-java)。

## <a name="nodejs"></a>Node.js

| 通訊程式庫 | 版本 |
| ------------------------|----------|
| [HTTP](https://nodejs.org/api/http.html)、[HTTPS](https://nodejs.org/api/https.html) | 0.10+ |
| <b>儲存體用戶端</b> | |
| [Redis](https://www.npmjs.com/package/redis) | 2.x |
| [MongoDb](https://www.npmjs.com/package/mongodb)、[MongoDb Core](https://www.npmjs.com/package/mongodb-core) | 2.x-3。x |
| [MySQL](https://www.npmjs.com/package/mysql) | 2.0.0-2.16. x |
| [PostgreSql](https://www.npmjs.com/package/pg); | 6.x-7. x |
| [pg-pool](https://www.npmjs.com/package/pg-pool) | 1.x-2. x |
| <b>記錄程式庫</b> | |
| [console](https://nodejs.org/api/console.html) | 0.10+ |
| [Bunyan](https://www.npmjs.com/package/bunyan) | 1.x |
| [Winston](https://www.npmjs.com/package/winston) | 2.x-3。x |

## <a name="javascript"></a>JavaScript

| 通訊程式庫 | 版本 |
| ------------------------|----------|
| [XMLHttpRequest](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) | 全部 |

## <a name="next-steps"></a>後續步驟

- 設定 [.NET](../../azure-monitor/app/asp-net-dependencies.md) 的自訂相依性追蹤。
- 設定 [Java](../../azure-monitor/app/java-agent.md) 的自訂相依性追蹤。
- 設定[OpenCensus Python](../../azure-monitor/app/opencensus-python-dependency.md)的自訂相依性追蹤。
- [撰寫自訂相依性遙測](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- 如需 Application Insights 類型和資料模型，請參閱[資料模型](../../azure-monitor/app/data-model.md)。
- 查看 Application Insights 支援的[平台](../../azure-monitor/app/platforms.md)。

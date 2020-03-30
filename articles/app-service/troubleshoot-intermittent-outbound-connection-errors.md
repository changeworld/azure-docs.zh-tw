---
title: 解決 Azure 應用服務中的間歇性出站連接錯誤
description: 解決 Azure 應用服務中的間歇性連接錯誤和相關性能問題
author: v-miegge
manager: barbkess
ms.topic: troubleshooting
ms.date: 03/24/2020
ms.author: ramakoni
ms.custom: security-recommendations
ms.openlocfilehash: 028ddccdb989d35710e387081b08a3b973d75bdc
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80367547"
---
# <a name="troubleshooting-intermittent-outbound-connection-errors-in-azure-app-service"></a>解決 Azure 應用服務中的間歇性出站連接錯誤

本文可説明您解決[Azure 應用服務](https://docs.microsoft.com/azure/app-service/overview)中的間歇性連接錯誤和相關性能問題。 本主題將提供有關源位址網路轉換 （SNAT） 埠耗盡的詳細資訊和故障排除方法。 如果本文中的任何一點都需要更多説明，請與[MSDN Azure 和堆疊溢位論壇中的](https://azure.microsoft.com/support/forums/)Azure 專家聯繫。 或者，提交 Azure 支援事件。 轉到[Azure 支援網站](https://azure.microsoft.com/support/options/)並選擇 **"獲取支援**"。

## <a name="symptoms"></a>徵狀

Azure 應用服務上託管的應用程式和函數可能會表現出以下一個或多個症狀：

* 服務方案中所有或某些實例的回應時間變慢。
* 間歇性 5xx 或**錯誤閘道**錯誤
* 逾時錯誤消息
* 無法連接到外部終結點（如 SQLDB、服務結構、其他應用服務等）

## <a name="cause"></a>原因

這些症狀的一個主要原因是應用程式實例無法打開到外部終結點的新連接，因為它已達到以下限制之一：

* TCP 連接：可以進行的出站連接數有限制。 這與所使用的工作人員的大小相關聯。
* SNAT 埠：如[Azure 中的出站連接中](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)所述，Azure 使用源網路位址轉譯 （SNAT） 和負載等化器（不向客戶公開）在公共 IP 位址空間中與 Azure 外部的終結點進行通信。 Azure 應用服務中的每個實例最初都會獲得預分配的**128**個 SNAT 埠數。 該限制會影響打開連接到同一主機和埠組合的連接。 如果應用創建到位址和埠組合的連接，則不會佔用 SNAT 埠。 重複調用同一位址和埠組合時，SNAT 埠將用用。 釋放埠後，該埠可根據需要重複使用。 Azure 網路負載等化器僅在等待 4 分鐘後從已關閉的連接中回收 SNAT 埠。

當應用程式或函數快速打開新連接時，它們會快速耗盡其預分配的 128 個埠配額。 然後，通過動態分配其他 SNAT 埠或重新回收的 SNAT 埠，阻止它們，直到新的 SNAT 埠可用。 由於無法創建新連接而被阻止的應用程式或函數將開始遇到本文"**症狀**"部分中描述的一個或多個問題。

## <a name="avoiding-the-problem"></a>避免問題

避免 SNAT 埠問題意味著避免重複創建與同一主機和埠的新連接。

Azure 文檔**的出站連接**[的問題解決部分](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#problemsolving)討論了緩解 SNAT 埠耗盡的一般策略。 在這些策略中，以下策略適用于 Azure 應用服務上託管的應用和功能。

### <a name="modify-the-application-to-use-connection-pooling"></a>將應用程式修改成使用連線共用

* 要池 HTTP 連接，請查看[使用 HttpClientFactory](https://docs.microsoft.com/aspnet/core/performance/performance-best-practices#pool-http-connections-with-httpclientfactory)的池 HTTP 連接。
* 有關 SQL Server 連接池的資訊，請查看[SQL 伺服器連接池 （ADO.NET）](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)。
* 要實現與實體框架應用程式池，請查看[DbCoNtext 池](https://docs.microsoft.com/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)。

下面是用於通過不同解決方案堆疊實現連接池的連結集合。

#### <a name="node"></a>節點

預設情況下，NodeJS 的連接不會保持活動狀態。 下面是用於連接池的熱門資料庫和包，其中包含如何實現它們的示例。

* [Mysql](https://github.com/mysqljs/mysql#pooling-connections)
* [Mongodb](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [後格雷SQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools)

HTTP 保持活動狀態

* [代理保存](https://www.npmjs.com/package/agentkeepalive)
* [Node.js v13.9.0 文檔](https://nodejs.org/api/http.html)

#### <a name="java"></a>Java

下面是用於 JDBC 連接池的熱門庫，其中包含如何實現它們的示例：JDBC 連接池。

* [湯姆卡特 8](https://tomcat.apache.org/tomcat-8.0-doc/jdbc-pool.html)
* [C3p0](https://github.com/swaldman/c3p0)
* [希卡裡CP](https://github.com/brettwooldridge/HikariCP)
* [阿帕奇 DBCP](https://commons.apache.org/proper/commons-dbcp/)

HTTP 連接池

* [阿帕奇連接管理](https://hc.apache.org/httpcomponents-client-ga/tutorial/html/connmgmt.html)
* [類池Http用戶端連線管理員](http://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/org/apache/http/impl/conn/PoolingHttpClientConnectionManager.html)

#### <a name="php"></a>PHP

儘管 PHP 不支援連接池，但您可以嘗試使用與後端伺服器的持久資料庫連接。
 
* MySQL 伺服器

   * 較新版本的[MySQLi 連接](https://www.php.net/manual/mysqli.quickstart.connections.php)
   * [mysql_pconnect](https://www.php.net/manual/function.mysql-pconnect.php)舊版本的 PHP

* 其他資料來源

   * [PHP連接管理](https://www.php.net/manual/en/pdo.connections.php)

#### <a name="python"></a>Python

* [Mysql](https://github.com/mysqljs/mysql#pooling-connections)
* [Mongodb](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [後格雷SQL](https://node-postgres.com/features/pooling)
* [SQL 伺服器](https://github.com/tediousjs/node-mssql#connection-pools)（注意：SQLAlchemy 可用於除 MicrosoftSQL Server 之外的其他資料庫）
* [HTTP 保持活動](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive)狀態（使用[會話物件](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive)時，保持活動是自動的）。

對於其他環境，請查看提供程式或特定于驅動程式的文檔，以便在應用程式中實現連接池。

### <a name="modify-the-application-to-reuse-connections"></a>將應用程式修改成重複使用連線

*  有關在 Azure 函數中管理連接的其他指標和示例，請查看[Azure 函數中的管理連接](https://docs.microsoft.com/azure/azure-functions/manage-connections)。

### <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a>將應用程式修改成使用較不積極的重試邏輯

* 有關其他指導和示例，請查看[重試模式](https://docs.microsoft.com/azure/architecture/patterns/retry)。

### <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a>使用 Keepalive 來重設輸出閒置逾時

* 要實現 Node.js 應用的保持計畫，請查看[我的節點應用程式正在進行過多的出站調用](https://docs.microsoft.com/azure/app-service/app-service-web-nodejs-best-practices-and-troubleshoot-guide#my-node-application-is-making-excessive-outbound-calls)。

### <a name="additional-guidance-specific-to-app-service"></a>特定于應用服務的其他指南：

* [負載測試](https://docs.microsoft.com/azure/devops/test/load-test/app-service-web-app-performance-test)應以穩定的饋送速度類比真實世界資料。 在真實壓力下測試應用和功能可以提前識別和解決 SNAT 埠耗盡問題。
* 確保後端服務能夠快速返回回應。 要解決 Azure SQL 資料庫的性能問題，請查看[使用智慧見解解決 Azure SQL 資料庫性能問題](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-troubleshoot-performance#recommended-troubleshooting-flow)。
* 將應用服務方案擴展到更多實例。 如需有關調整的詳細資訊，請參閱[在 Azure App Service 中調整應用程式規模](https://docs.microsoft.com/azure/app-service/manage-scale-up)。 應用服務方案中的每個輔助角色實例都分配了多個 SNAT 埠。 如果將使用方式分散到更多實例中，則每個實例的 SNAT 埠使用量可能會低於建議限制的 100 個出站連接，每個唯一的遠端終結點。
* 請考慮遷移到[應用服務環境 （ASE），](https://docs.microsoft.com/azure/app-service/environment/using-an-ase)其中分配給您單個出站 IP 位址，並且連接和 SNAT 埠的限制要高得多。

避免出站 TCP 限制更容易解決，因為限制由工作人員的大小設置。 您可以在[沙箱交叉 VM 數位限制 - TCP 連接](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)中看到限制

|限制名稱|描述|小 （A1）|中等 （A2）|大 （A3）|隔離層 （ASE）|
|---|---|---|---|---|---|
|連接|跨整個 VM 的連接數|1920|3968|8064|16,000|

為了避免出站 TCP 限制，可以增加輔助物件的大小，也可以橫向橫向橫向橫向擴展。

## <a name="troubleshooting"></a>疑難排解

瞭解兩種類型的出站連接限制以及你的應用的作用，應該更容易疑難排解。 如果您知道你的應用對同一存儲帳戶進行多次調用，您可能會懷疑 SNAT 限制。 如果你的應用在互聯網上創建了大量對終結點的調用，你會懷疑您達到了 VM 限制。

如果您沒有足夠的應用程式行為來快速確定原因，應用服務中可以使用一些工具和技術來説明確定。

### <a name="find-snat-port-allocation-information"></a>查找 SNAT 埠分配資訊

您可以使用[應用服務診斷](https://docs.microsoft.com/azure/app-service/overview-diagnostics)查找 SNAT 埠分配資訊，並遵守應用服務網站的 SNAT 埠分配指標。 要查找 SNAT 埠分配資訊，請按照以下步驟操作：

1. 要訪問應用服務診斷，請導航到[Azure 門戶](https://portal.azure.com/)中的應用服務 Web 應用或應用服務環境。 在左側導航中，選擇 **"診斷並解決問題**"。
2. 選擇可用性和性能類別
3. 在類別下的可用磁貼清單中選擇 SNAT 埠耗盡磁貼。 其做法是將其保持在 128 以下。
如果您確實需要它，您仍然可以打開支援票證，支援工程師將從後端為您獲取指標。

請注意，由於 SNAT 埠使用方式不能作為指標提供，因此無法基於 SNAT 埠使用方式自動縮放，也不能根據 SNAT 埠分配指標配置自動縮放。

### <a name="tcp-connections-and-snat-ports"></a>TCP 連接和 SNAT 埠

TCP 連接和 SNAT 埠沒有直接關係。 任何應用服務網站的"診斷和解決問題"邊欄選項卡中都包含 TCP 連接使用檢測器。 搜索短語"TCP 連接"以查找它。

* SNAT 埠僅用於外部網路流，而總 TCP 連接包括本地回環連接。
* 如果流在協定、IP 位址或埠中不同，則可以由不同的流共用 SNAT 埠。 TCP 連接指標對每個 TCP 連接進行計數。
* TCP 連接限制發生在輔助角色實例級別。 Azure 網路出站負載平衡不使用 TCP 連接指標進行 SNAT 埠限制。
* TCP 連接限制在[沙箱交叉 VM 數位限制 - TCP 連接](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)中描述

|限制名稱|描述|小 （A1）|中等 （A2）|大 （A3）|隔離層 （ASE）|
|---|---|---|---|---|---|
|連接|跨整個 VM 的連接數|1920|3968|8064|16,000|

### <a name="webjobs-and-database-connections"></a>Web 作業和資料庫連接
 
如果 SNAT 埠已耗盡，Web 作業無法連接到 Azure SQL 資料庫，則沒有指標顯示每個單獨的 Web 應用程式進程打開的連接數。 要查找有問題的 WebJob，將多個 Web 作業移出另一個應用服務方案，以查看情況是否有所改善，或者其中一個計畫中是否仍然存在問題。 重複此過程，直到發現有問題的 WebJob。

### <a name="using-snat-ports-sooner"></a>更快地使用 SNAT 埠

您不能更改任何 Azure 設置以更快地釋放已使用的 SNAT 埠，因為所有 SNAT 埠都將根據以下條件發佈，並且行為是設計。
 
* 如果伺服器或用戶端發送[FINACK，SNAT 埠將在](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#tcp-snat-port-release)240 秒後釋放。
* 如果看到 RST，SNAT 埠將在 15 秒後釋放。
* 如果已達到空閒超時，則釋放埠。
 
## <a name="additional-information"></a>其他資訊

* [具有應用服務的 SNAT](https://4lowtherabbit.github.io/blogs/2019/10/SNAT/)
* [針對 Azure App Service 中應用程式效能變慢的問題進行疑難排解](https://docs.microsoft.com/azure/app-service/troubleshoot-performance-degradation)

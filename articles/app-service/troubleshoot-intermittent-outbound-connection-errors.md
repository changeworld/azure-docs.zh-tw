---
title: 疑難排解 Azure App Service 中的間歇輸出連接錯誤
description: 針對間歇連接錯誤和 Azure App Service 中的相關效能問題進行疑難排解
author: v-miegge
manager: barbkess
ms.topic: troubleshooting
ms.date: 07/24/2020
ms.author: ramakoni
ms.custom: security-recommendations
ms.openlocfilehash: 5e1f2108c5607917c77330f362952f960e57e03a
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2020
ms.locfileid: "87447908"
---
# <a name="troubleshooting-intermittent-outbound-connection-errors-in-azure-app-service"></a>疑難排解 Azure App Service 中的間歇輸出連接錯誤

本文可協助您針對間歇連線錯誤和[Azure App Service](https://docs.microsoft.com/azure/app-service/overview)中的相關效能問題進行疑難排解。 本主題將提供來源位址網路轉譯（SNAT）埠耗盡的詳細資訊，以及疑難排解的方法。 如果您在本文中有任何需要協助的地方，請洽詢[MSDN azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 Azure 專家。 或者，將 Azure 支援事件提出。 請移至[Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [**取得支援**]。

## <a name="symptoms"></a>徵狀

裝載于 Azure App 服務上的應用程式和函式可能會出現下列一個或多個徵兆：

* 服務方案中所有或部分實例的回應時間緩慢。
* 間歇5xx 或不**正確的閘道**錯誤
* 逾時錯誤訊息
* 無法連接到外部端點（例如 SQLDB、Service Fabric、其他應用程式服務等）

## <a name="cause"></a>原因

這些徵兆的主要原因是應用程式實例無法開啟連至外部端點的新連線，因為它已達到下列其中一個限制：

* TCP 連線：可以進行的輸出連線數目有限制。 這會與所使用的工作者大小相關聯。
* SNAT 埠：如同 Azure 中的[輸出](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)連線所述，azure 會使用來源網路位址轉譯（SNAT）和 Load Balancer （不會向客戶公開），在公用 IP 位址空間中與 Azure 外部的端點進行通訊。 Azure App 服務上的每個實例一開始都會指定**128** SNAT 埠的預先配置數目。 該限制會影響到相同主機和埠組合的開啟連接。 如果您的應用程式會建立混合位址和埠組合的連線，您將不會使用 SNAT 埠。 當您重複呼叫相同的位址和埠組合時，會使用 SNAT 埠。 連接埠一經釋放，即可視需要重複使用。 Azure 網路負載平衡器只會在等待4分鐘後，才從已關閉的連線中回收 SNAT 埠。

當應用程式或函式快速開啟新的連線時，他們可以快速耗盡其預先配置的128埠配額。 然後，它們會被封鎖，直到新的 SNAT 埠可供使用為止，不論是透過動態配置額外的 SNAT 埠，或重複使用已回收的 SNAT 埠。 因為無法建立新連線而遭到封鎖的應用程式或函式，將會開始遇到本文的**徵兆**一節中所述的一或多個問題。

## <a name="avoiding-the-problem"></a>避免此問題

如果您的目的地是支援服務端點的 Azure 服務，您可以使用[區域 VNet 整合](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)和服務端點或私人端點來避免 SNAT 埠耗盡問題。 當您使用區域 VNet 整合並將服務端點放在整合子網上時，您的應用程式輸出流量到這些服務將不會有輸出 SNAT 埠限制。 同樣地，如果您使用區域 VNet 整合和私人端點，則該目的地不會有任何輸出 SNAT 埠問題。 

避免 SNAT 埠問題，表示避免重複建立新連線到相同的主機和埠。

**Azure 檔輸出**連線的[問題解決一節](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#problemsolving)中會討論減少 SNAT 埠耗盡的一般策略。 在這些策略中，下列適用于 Azure App 服務上所裝載的應用程式和功能。

### <a name="modify-the-application-to-use-connection-pooling"></a>將應用程式修改成使用連線共用

* 針對共用 HTTP 連線，請參閱[使用 HttpClientFactory 的集區 HTTP](https://docs.microsoft.com/aspnet/core/performance/performance-best-practices#pool-http-connections-with-httpclientfactory)連線。
* 如需 SQL Server 連接共用的詳細資訊，請參閱[SQL Server 連接共用（ADO.NET）](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)。
* 如需使用 entity framework 應用程式執行共用，請參閱[DbCoNtext](https://docs.microsoft.com/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)共用。

以下是不同解決方案堆疊用來執行連接共用的連結集合。

#### <a name="node"></a>節點

根據預設，NodeJS 的連接不會保持運作。 以下是連接共用的熱門資料庫和封裝，其中包含如何執行這些工作的範例。

* [MySQL](https://github.com/mysqljs/mysql#pooling-connections)
* [MongoDB](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools)

HTTP Keep-alive

* [agentkeepalive](https://www.npmjs.com/package/agentkeepalive)
* [Node.js v 13.9.0 檔](https://nodejs.org/api/http.html)

#### <a name="java"></a>Java

以下是用於 JDBC 連接共用的熱門程式庫，其中包含如何執行這些工作的範例： JDBC Connection Pooling。

* [Tomcat 8](https://tomcat.apache.org/tomcat-8.0-doc/jdbc-pool.html)
* [C3p0](https://github.com/swaldman/c3p0)
* [HikariCP](https://github.com/brettwooldridge/HikariCP)
* [Apache DBCP](https://commons.apache.org/proper/commons-dbcp/)

HTTP 連接共用

* [Apache 連接管理](https://hc.apache.org/httpcomponents-client-ga/tutorial/html/connmgmt.html)
* [類別 PoolingHttpClientConnectionManager](http://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/org/apache/http/impl/conn/PoolingHttpClientConnectionManager.html)

#### <a name="php"></a>PHP

雖然 PHP 不支援連接共用，但您可以嘗試對後端伺服器使用持續性資料庫連接。
 
* MySQL 伺服器

   * 較新版本的[MySQLi 連接](https://www.php.net/manual/mysqli.quickstart.connections.php)
   * 舊版 PHP 的[mysql_pconnect](https://www.php.net/manual/function.mysql-pconnect.php)

* 其他資料來源

   * [PHP 連接管理](https://www.php.net/manual/en/pdo.connections.php)

#### <a name="python"></a>Python

* [MySQL](https://github.com/mysqljs/mysql#pooling-connections)
* [MongoDB](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools) （注意： SQLAlchemy 可與 MicrosoftSQL 伺服器以外的其他資料庫搭配使用）
* [HTTP keep-alive](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive)（使用會話[會話物件](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive)時保持運作的狀態是自動的）。

針對其他環境，請參閱提供者或驅動程式特定的檔，以在您的應用程式中執行連接共用。

### <a name="modify-the-application-to-reuse-connections"></a>將應用程式修改成重複使用連線

*  如需在 Azure 函式中管理連線的其他指標和範例，請參閱[管理 Azure Functions 中的連接](https://docs.microsoft.com/azure/azure-functions/manage-connections)。

### <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a>將應用程式修改成使用較不積極的重試邏輯

* 如需其他指引和範例，請參閱[重試模式](https://docs.microsoft.com/azure/architecture/patterns/retry)。

### <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a>使用 Keepalive 來重設輸出閒置逾時

* 若要針對 Node.js 應用程式執行 keepalive，請檢查[我的節點應用程式是否進行過多的輸出呼叫](https://docs.microsoft.com/azure/app-service/app-service-web-nodejs-best-practices-and-troubleshoot-guide#my-node-application-is-making-excessive-outbound-calls)。

### <a name="additional-guidance-specific-to-app-service"></a>App Service 特定的其他指導方針：

* [負載測試](https://docs.microsoft.com/azure/devops/test/load-test/app-service-web-app-performance-test)應以穩定的進紙速度來模擬真實世界的資料。 在真實世界壓力下測試應用程式和功能，可以提早識別及解決 SNAT 埠耗盡問題。
* 請確定後端服務可以快速傳迴響應。 如需 Azure SQL Database 的效能問題疑難排解，請參閱[疑難排解 Intelligent Insights 的 Azure SQL Database 效能問題](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-troubleshoot-performance#recommended-troubleshooting-flow)。
* 將 App Service 方案相應放大為更多實例。 如需有關調整的詳細資訊，請參閱[在 Azure App Service 中調整應用程式規模](https://docs.microsoft.com/azure/app-service/manage-scale-up)。 App service 方案中的每個背景工作實例都會配置一些 SNAT 埠。 如果您將使用量分散到多個實例，您可能會取得每個實例的 SNAT 埠使用量，低於每個唯一遠端端點的建議限制100輸出連線。
* 請考慮移至[App Service 環境（ASE）](https://docs.microsoft.com/azure/app-service/environment/using-an-ase)，您會在其中配置單一輸出 IP 位址，而連線和 SNAT 埠的限制會更高。

避免輸出 TCP 限制比較容易解決，因為限制是由背景工作角色的大小所設定。 您可以查看[沙箱跨 VM 數值限制的限制-TCP](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)連線

|限制名稱|說明|小型（A1）|中（A2）|大型（A3）|隔離層（ASE）|
|---|---|---|---|---|---|
|連接|跨整個 VM 的連線數目|1920|3968|8064|16,000|

若要避免輸出 TCP 限制，您可以增加背景工作角色的大小，或水準相應放大。

## <a name="troubleshooting"></a>疑難排解

瞭解這兩種類型的輸出連線限制，以及您的應用程式執行的作業，應可讓您更輕鬆地進行疑難排解。 如果您知道您的應用程式對相同的儲存體帳戶進行多次呼叫，您可能會懷疑 SNAT 限制。 如果您的應用程式透過網際網路對端點建立了很多的呼叫，您會懷疑達到 VM 的限制。

如果您不知道應用程式行為是否能快速判斷原因，App Service 中有一些工具和技術可協助進行判斷。

### <a name="find-snat-port-allocation-information"></a>尋找 SNAT 埠配置資訊

您可以使用[App Service 診斷](https://docs.microsoft.com/azure/app-service/overview-diagnostics)來尋找 snat 埠配置資訊，並觀察 App Service 網站的 snat 埠配置度量。 若要尋找 SNAT 埠配置資訊，請遵循下列步驟：

1. 若要存取 App Service 診斷，請流覽至您的 App Service web 應用程式，或[Azure 入口網站](https://portal.azure.com/)中的 App Service 環境。 在左側導覽中，選取 [**診斷並解決問題**]。
2. 選取可用性和效能類別
3. 在類別底下的可用磚清單中，選取 [SNAT 埠耗盡] 磚。 做法是將它保持在128以下。
如果您需要它，您仍然可以開啟支援票證，而支援工程師會為您取得來自後端的計量。

請注意，由於 SNAT 埠的使用不是計量，因此不可能根據 SNAT 埠使用方式自動調整，或根據 SNAT 埠配置計量來設定自動調整。

### <a name="tcp-connections-and-snat-ports"></a>TCP 連線和 SNAT 埠

TCP 連線和 SNAT 埠並不直接相關。 任何 App Service 網站的 [診斷並解決問題] 分頁中都會包含 TCP 連線使用偵測器。 搜尋片語 "TCP connections" 以尋找它。

* SNAT 埠只會用於外部網路流程，而 TCP 連線總數則包括本機回送連接。
* 如果通訊協定、IP 位址或埠中的流程不同，則可以由不同的流程共用 SNAT 埠。 TCP 連接計量會計算每個 TCP 連線。
* TCP 連接限制會發生在背景工作角色實例層級。 Azure 網路輸出負載平衡不會使用適用于 SNAT 埠限制的 TCP 連線計量。
* [沙箱跨 VM 數值限制-tcp](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)連線中描述 tcp 連線限制

|限制名稱|說明|小型（A1）|中（A2）|大型（A3）|隔離層（ASE）|
|---|---|---|---|---|---|
|連接|跨整個 VM 的連線數目|1920|3968|8064|16,000|

### <a name="webjobs-and-database-connections"></a>Webjob 和資料庫連接
 
如果 SNAT 埠已耗盡，而 Webjob 無法連接到 SQL Database，則沒有計量可顯示每個個別 web 應用程式進程所開啟的連線數目。 若要找出有問題的 WebJob，請將數個 Webjob 移至另一個 App Service 計畫，以查看情況是否改善，或是否有問題仍然存在於其中一個方案中。 重複此程式，直到您找到有問題的 WebJob 為止。

### <a name="using-snat-ports-sooner"></a>更快使用 SNAT 埠

您無法將任何 Azure 設定變更為較早釋放已使用的 SNAT 埠，因為所有 SNAT 埠都會根據下列條件釋放，而且行為是依設計而成。
 
* 如果伺服器或用戶端傳送 FINACK，則會在240秒後[釋放 SNAT 埠](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#tcp-snat-port-release)。
* 如果看到 RST，則會在15秒後釋放 SNAT 埠。
* 如果已達到閒置超時，則會釋放埠。
 
## <a name="additional-information"></a>其他資訊

* [具有 App Service 的 SNAT](https://4lowtherabbit.github.io/blogs/2019/10/SNAT/)
* [針對 Azure App Service 中應用程式效能變慢的問題進行疑難排解](https://docs.microsoft.com/azure/app-service/troubleshoot-performance-degradation)

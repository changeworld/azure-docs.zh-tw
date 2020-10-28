---
title: 針對 Azure App Service 中間歇性的輸出連線錯誤進行疑難排解
description: 針對間歇性連接錯誤和 Azure App Service 中的相關效能問題進行疑難排解
author: v-miegge
manager: barbkess
ms.topic: troubleshooting
ms.date: 07/24/2020
ms.author: ramakoni
ms.custom: security-recommendations,fasttrack-edit
ms.openlocfilehash: 76b4408b2f8c631453281ecf6f214d49318252a3
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92785046"
---
# <a name="troubleshooting-intermittent-outbound-connection-errors-in-azure-app-service"></a>針對 Azure App Service 中間歇性的輸出連線錯誤進行疑難排解

本文可協助您針對間歇性連接錯誤和 [Azure App Service](./overview.md)中的相關效能問題進行疑難排解。 本主題將提供有關來源位址網路轉譯 (SNAT) 埠耗盡的詳細資訊和疑難排解方法。 如果您在本文的任何時間點需要更多協助，請洽詢 [MSDN azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)的 Azure 專家。 或者，提出 Azure 支援事件。 前往 [Azure 支援網站](https://azure.microsoft.com/support/options/) ，然後選取 [ **取得支援** ]。

## <a name="symptoms"></a>徵兆

Azure App 服務上裝載的應用程式和函式可能會出現下列一或多個徵兆：

* 服務方案中所有或部分實例的回應時間很慢。
* 間歇的5xx 或 **閘道不正確** 錯誤
* 逾時錯誤訊息
* 無法連接到外部端點 (例如 SQLDB、Service Fabric、其他應用程式服務等。 ) 

## <a name="cause"></a>原因

這些徵兆的主要原因是應用程式實例無法開啟與外部端點的新連接，因為它已達到下列其中一項限制：

* TCP 連接：可以進行的輸出連接數目有限制。 這會與使用的背景工作角色大小相關聯。
* SNAT 埠：如同 [azure 中的輸出](../load-balancer/load-balancer-outbound-connections.md)連線所討論，azure 會使用來源網路位址轉譯 (SNAT) 和 Load Balancer (不會向客戶公開，) 在公用 IP 位址空間中與 azure 外部的端點進行通訊，以及不利用服務/私用端點的 azure 內部端點。 Azure App 服務上的每個實例一開始都會獲得預先配置的 **128** SNAT 埠數目。 該限制會影響對相同主機和埠組合的開啟連接。 如果您的應用程式建立混合位址和埠組合的連線，則不會使用 SNAT 埠。 當您重複呼叫相同的位址和埠組合時，就會使用 SNAT 埠。 連接埠一經釋放，即可視需要重複使用。 Azure 網路負載平衡器只會在等候4分鐘後，從關閉的連線回收 SNAT 埠。

當應用程式或函式快速開啟新的連接時，它們可以快速耗盡其預先配置的128埠配額。 然後會封鎖它們，直到新的 SNAT 埠變成可用為止（透過動態配置額外的 SNAT 埠），或重複使用已回收的 SNAT 埠。 因為無法建立新連線而遭到封鎖的應用程式或函式，將會開始遇到本文的 **徵兆** 一節中所述的一或多個問題。

## <a name="avoiding-the-problem"></a>避免問題

如果您的目的地是支援服務端點的 Azure 服務，您可以使用 [區域 VNet 整合](./web-sites-integrate-with-vnet.md) 和服務端點或私人端點來避免 SNAT 埠耗盡問題。 當您使用區域 VNet 整合並將服務端點放置於整合子網上時，對這些服務的應用程式輸出流量將不會有輸出 SNAT 埠限制。 同樣地，如果您使用區域 VNet 整合和私人端點，則不會有該目的地的任何輸出 SNAT 埠問題。 

避免 SNAT 埠問題，表示避免重複建立與相同主機和埠的新連接。

**Azure 檔輸出** 連線的 [問題解決一節](../load-balancer/load-balancer-outbound-connections.md)中會討論緩和 SNAT 埠耗盡的一般策略。 這些策略的下列各項適用于 Azure App service 上裝載的應用程式和功能。

### <a name="modify-the-application-to-use-connection-pooling"></a>將應用程式修改成使用連線共用

* 針對共用 HTTP 連線，請參閱 [使用 HttpClientFactory 的集區 HTTP](/aspnet/core/performance/performance-best-practices#pool-http-connections-with-httpclientfactory)連線。
* 如需 SQL Server 連接共用的詳細資訊，請參閱 [SQL Server 連接共用 (ADO.NET) ](/dotnet/framework/data/adonet/sql-server-connection-pooling)。
* 若要使用 entity framework 應用程式來執行共用，請參閱 [DbCoNtext](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)共用。

以下是透過不同的解決方案堆疊來執行連接共用的連結集合。

#### <a name="node"></a>節點

依預設，NodeJS 的連接不會保持運作。 以下是連接共用的熱門資料庫和封裝，其中包含如何執行這些專案的範例。

* [MySQL](https://github.com/mysqljs/mysql#pooling-connections)
* [MongoDB](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools)

HTTP 保持運作

* [agentkeepalive](https://www.npmjs.com/package/agentkeepalive)
* [Node.js v 13.9.0 檔](https://nodejs.org/api/http.html)

#### <a name="java"></a>Java

以下是用於 JDBC 連接共用的熱門程式庫，其中包含如何執行它們的範例： JDBC 連接共用。

* [Tomcat 8](https://tomcat.apache.org/tomcat-8.0-doc/jdbc-pool.html)
* [C3p0](https://github.com/swaldman/c3p0)
* [HikariCP](https://github.com/brettwooldridge/HikariCP)
* [Apache DBCP](https://commons.apache.org/proper/commons-dbcp/)

HTTP 連接共用

* [Apache 連接管理](https://hc.apache.org/httpcomponents-client-ga/tutorial/html/connmgmt.html)
* [類別 PoolingHttpClientConnectionManager](http://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/org/apache/http/impl/conn/PoolingHttpClientConnectionManager.html)

#### <a name="php"></a>PHP

雖然 PHP 不支援連接共用，但是您可以嘗試對後端伺服器使用持續性資料庫連接。
 
* MySQL 伺服器

   * 適用于較新版本的[MySQLi 連接](https://www.php.net/manual/mysqli.quickstart.connections.php)
   * 舊版 PHP 的[mysql_pconnect](https://www.php.net/manual/function.mysql-pconnect.php)

* 其他資料來源

   * [PHP 連接管理](https://www.php.net/manual/en/pdo.connections.php)

### <a name="modify-the-application-to-reuse-connections"></a>將應用程式修改成重複使用連線

*  如需在 Azure 函式中管理連線的其他指標和範例，請參閱 [Azure Functions 中的管理連接](../azure-functions/manage-connections.md)。

### <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a>將應用程式修改成使用較不積極的重試邏輯

* 如需其他指引和範例，請參閱 [重試模式](/azure/architecture/patterns/retry)。

### <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a>使用 Keepalive 來重設輸出閒置逾時

* 若要為 Node.js 應用程式執行 keepalive，請參閱 [我的 node 應用程式是否發出過多的輸出呼叫](./app-service-web-nodejs-best-practices-and-troubleshoot-guide.md#my-node-application-is-making-excessive-outbound-calls)。

### <a name="additional-guidance-specific-to-app-service"></a>App Service 特定的其他指導方針：

* [負載測試](/azure/devops/test/load-test/app-service-web-app-performance-test)應以穩定的饋送速度模擬真實世界的資料。 在真實世界的壓力下測試應用程式和功能，可以事先找出並解決 SNAT 埠耗盡問題。
* 確定後端服務可以快速傳迴響應。 若要針對 Azure SQL Database 的效能問題進行疑難排解，請參閱 [Intelligent Insights 的 Azure SQL Database 效能問題疑難排解](../azure-sql/database/intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow)。
* 將 App Service 方案擴充到更多實例。 如需有關調整的詳細資訊，請參閱[在 Azure App Service 中調整應用程式規模](./manage-scale-up.md)。 App service 方案中的每個背景工作實例都會配置一些 SNAT 埠。 如果您將使用量分散到更多實例，您可能會在每個實例下取得每個實例的 SNAT 埠使用量低於每個唯一遠端端點的100輸出連線建議限制。
* 請考慮移至 [App Service 環境 (ASE) ](./environment/using-an-ase.md)（您會在其中配置單一輸出 IP 位址），而且連線和 SNAT 埠的限制會高出許多。 在 ASE 中，每個實例的 SNAT 埠數目是以 [Azure 負載平衡器預先配置資料表](../load-balancer/load-balancer-outbound-connections.md#snatporttable) 為基礎，例如，具有1-50 背景工作實例的 ase 會為每個實例提供1024個預先配置的埠，而具有51-100 工作者實例的 ase 則會為每個實例配置512的埠。

避免輸出 TCP 限制比較容易解決，因為限制是由背景工作的大小所設定。 您可以看到沙箱中的限制 [跨 VM 的數值限制-TCP 連接](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)

|限制名稱|描述|Small (A1) |中型 (A2) |大型 (A3) | (ASE 的隔離層) |
|---|---|---|---|---|---|
|連接|整個 VM 的連接數目|1920|3968|8064|16,000|

若要避免輸出 TCP 限制，您可以增加背景工作的大小，或水準相應放大。

## <a name="troubleshooting"></a>疑難排解

瞭解輸出連線限制的這兩種類型，以及您的應用程式執行的作業，應該可讓您更輕鬆地進行疑難排解。 如果您知道您的應用程式對相同的儲存體帳戶進行了許多呼叫，您可能會懷疑有 SNAT 限制。 如果您的應用程式在網際網路上建立對端點的很多呼叫，您可能會懷疑您已達到 VM 的限制。

如果您不知道應用程式行為是否夠快判斷出原因，App Service 中有一些工具和技巧可協助您進行這項判斷。

### <a name="find-snat-port-allocation-information"></a>尋找 SNAT 埠配置資訊

您可以使用 [App Service 診斷](./overview-diagnostics.md) 來尋找 snat 埠配置資訊，並觀察 App Service 網站的 snat 埠配置度量。 若要尋找 SNAT 埠配置資訊，請遵循下列步驟：

1. 若要存取 App Service 診斷，請流覽至您的 App Service web 應用程式或 [Azure 入口網站](https://portal.azure.com/)中的 App Service 環境。 在左側導覽中，選取 [ **診斷並解決問題** ]。
2. 選取可用性和效能類別
3. 在類別底下的可用磚清單中，選取 [SNAT 埠耗盡] 磚。 做法是將其保留在128以下。
如果您需要，您仍然可以開啟支援票證，而且支援工程師會為您取得來自後端的度量。

請注意，因為 SNAT 埠的使用方式無法作為計量，所以無法根據 SNAT 埠使用量自動調整，或根據 SNAT 埠配置度量設定自動調整。

### <a name="tcp-connections-and-snat-ports"></a>TCP 連接和 SNAT 埠

TCP 連接和 SNAT 埠不會直接相關。 TCP 連接使用偵測器包含在任何 App Service 網站的診斷和解決問題分頁中。 搜尋「TCP 連接」片語以找出它。

* SNAT 埠僅適用于外部網路流量，而 TCP 連線總數則包含本機回送連線。
* 如果通訊協定、IP 位址或埠不同，則不同的流程可以共用 SNAT 埠。 TCP 連接計量會計算每個 TCP 連線。
* TCP 連接限制發生在背景工作角色實例層級。 Azure 網路輸出負載平衡不會使用適用于 SNAT 埠限制的 TCP 連接度量。
* TCP 連接限制會以沙箱的[跨 VM 數值限制（tcp 連線）](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)說明

|限制名稱|描述|Small (A1) |中型 (A2) |大型 (A3) | (ASE 的隔離層) |
|---|---|---|---|---|---|
|連接|整個 VM 的連接數目|1920|3968|8064|16,000|

### <a name="webjobs-and-database-connections"></a>Webjob 和資料庫連接
 
如果 SNAT 埠耗盡，而 Webjob 無法連線到 SQL Database，則不會顯示每個個別 web 應用程式進程所開啟的連接數目。 若要找出有問題的 WebJob，請將多個 Webjob 移至另一個 App Service 計畫，以查看情況是否改善，或是否有問題仍在其中一個方案中。 重複此程式，直到您找到有問題的 WebJob。

### <a name="using-snat-ports-sooner"></a>更快使用 SNAT 埠

您無法變更任何 Azure 設定，以更快釋出已使用的 SNAT 埠，因為所有的 SNAT 埠都會依下列條件釋出，而行為則是依設計來發行。
 
* 如果伺服器或用戶端傳送 FINACK，則會在240秒後釋出 [SNAT 埠](../load-balancer/load-balancer-outbound-connections.md) 。
* 如果看到 RST，則會在15秒後釋出 SNAT 埠。
* 如果已達到閒置超時，則會釋放埠。
 
## <a name="additional-information"></a>其他資訊

* [具有 App Service 的 SNAT](https://4lowtherabbit.github.io/blogs/2019/10/SNAT/)
* [針對 Azure App Service 中應用程式效能變慢的問題進行疑難排解](./troubleshoot-performance-degradation.md)

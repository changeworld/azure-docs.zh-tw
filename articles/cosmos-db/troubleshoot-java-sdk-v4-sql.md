---
title: 診斷 Azure Cosmos DB Java SDK v4 並進行疑難排解
description: 使用像是用戶端記錄的功能及其他協力廠商工具，在 Java SDK v4 中，針對 Azure Cosmos DB 問題進行識別、診斷及疑難排解。
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 06/11/2020
ms.author: anfeldma
ms.devlang: java
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.custom: devx-track-java
ms.openlocfilehash: b714c2c1c9524741d2dc0b7e8ff3ed082cadd106
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93079167"
---
# <a name="troubleshoot-issues-when-you-use-azure-cosmos-db-java-sdk-v4-with-sql-api-accounts"></a>針對搭配使用 Azure Cosmos DB Java SDK v4 和 SQL API 帳戶時所發生的問題進行疑難排解
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK v4](troubleshoot-java-sdk-v4-sql.md)
> * [非同步 Java SDK v2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

> [!IMPORTANT]
> 本文只涵蓋適用於 Azure Cosmos DB Java SDK v4 的疑難排解。 如需詳細資訊，請參閱 Azure Cosmos DB Java SDK v4 [版本資訊](sql-api-sdk-java-v4.md)、[Maven 存放庫](https://mvnrepository.com/artifact/com.azure/azure-cosmos)，以及[效能提示](performance-tips-java-sdk-v4-sql.md)。 如果您目前使用的版本比 v4 舊，請參閱[遷移至 Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) 指南，以取得升級至 v4 的協助。
>

此文章涵蓋搭配使用 Azure Cosmos DB Java SDK v4 和 Azure Cosmos DB SQL API 帳戶時的常見問題、因應措施、診斷步驟與工具。
Azure Cosmos DB Java SDK v4 提供用戶端邏輯表示法來存取 Azure Cosmos DB SQL API。 此文章所說明的工具和方法，可以在您遇到任何問題時提供協助。

從此清單開始：

* 查看此文章的[常見問題和因應措施]一節。
* 查看 Azure Cosmos DB 中央存放庫中的 Java SDK，其可從 [GitHub 上的開放原始碼](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos)取得。 它有受到主動監視的[議題區段](https://github.com/Azure/azure-sdk-for-java/issues)。 查看您是否有任何含有已提出因應措施的類似問題。 其中一個有用的秘訣是依 *cosmos:v4-item* 標籤篩選問題。
* 為 Azure Cosmos DB Java SDK v4 檢閱[效能秘訣](performance-tips-java-sdk-v4-sql.md)，並遵循建議的做法。
* 如果找不到解決方案，請閱讀本文的其餘部分。 然後提出 [GitHub 問題](https://github.com/Azure/azure-sdk-for-java/issues)。 如果有選項可將標籤新增至您的 GitHub 問題，請新增 *cosmos:v4-item* 標籤。

## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>常見問題和因應措施

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>網路問題, Netty 讀取逾時失敗, 低輸送量, 高延遲

#### <a name="general-suggestions"></a>一般建議
若要獲得最佳效能︰
* 確定應用程式會在與您 Azure Cosmos DB 帳戶相同的區域上執行。 
* 在應用程式執行所在的主機上檢查 CPU 使用量。 如果 CPU 使用量為 50% 或更高，請在具有更高組態的主機上執行您的應用程式。 或者，您可將負載分散於更多電腦上。
    * 如果您是在 Azure Kubernetes Service 上執行應用程式，您可以[使用 Azure 監視器來監視 CPU 使用率](../azure-monitor/insights/container-insights-analyze.md)。

#### <a name="connection-throttling"></a>連線節流
連線節流的發生原因可能是因為[主機電腦上的連線限制]或 [Azure SNAT (PAT) 連接埠耗盡]。

##### <a name="connection-limit-on-a-host-machine"></a><a name="connection-limit-on-host"></a>主機電腦上的連線限制
某些 Linux 系統 (例如 Red Hat) 具有開啟檔案的總數上限。 Linux 中的通訊端會實作為檔案，因此，這個數字也會限制連線總數。
執行下列命令。

```bash
ulimit -a
```
允許的開啟檔案數目上限 (識別為 "nofile") 必須至少是連線集區大小的兩倍。 如需詳細資訊，請參閱 Azure Cosmos DB Java SDK v4 [效能秘訣](performance-tips-java-sdk-v4-sql.md)。

##### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Azure SNAT (PAT) 連接埠耗盡

如果您的應用程式部署於不具公用 IP 位址的 Azure 虛擬機器上，則 [Azure SNAT 連接埠](../load-balancer/load-balancer-outbound-connections.md#preallocatedports)預設會建立與您 VM 外部任何端點的連線。 從 VM 到 Azure Cosmos DB 端點所允許的連線數目會受到 [Azure SNAT 設定](../load-balancer/load-balancer-outbound-connections.md#preallocatedports)所限制。

 只有在您的 VM 具有私人 IP 位址，而且來自 VM 的程序嘗試連線到公用 IP 位址時，才會使用 Azure SNAT 連接埠。 有兩種因應措施可避免 Azure SNAT 限制：

* 將 Azure Cosmos DB 服務端點新增至您的 Azure 虛擬機器虛擬網路。 如需詳細資訊，請參閱 [Azure 虛擬網路服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)。 

    啟用服務端點時，要求不再會從公用 IP 傳送到 Azure Cosmos DB。 改為傳送虛擬網路和子網路身分識別。 如果只允許公用 IP，此變更可能會導致防火牆卸除。 如果您使用防火牆，當您啟用服務端點時，請使用[虛擬網路 ACL](/previous-versions/azure/virtual-network/virtual-networks-acl) 將子網路新增至防火牆。
* 將公用 IP 指派給您的 Azure VM。

##### <a name="cant-reach-the-service---firewall"></a><a name="cant-connect"></a>無法連線服務 - 防火牆
``ConnectTimeoutException`` 表示 SDK 無法連線服務。
使用直接模式時，您可能會收到如下所示的錯誤：
```
GoneException{error=null, resourceAddress='https://cdb-ms-prod-westus-fd4.documents.azure.com:14940/apps/e41242a5-2d71-5acb-2e00-5e5f744b12de/services/d8aa21a5-340b-21d4-b1a2-4a5333e7ed8a/partitions/ed028254-b613-4c2a-bf3c-14bd5eb64500/replicas/131298754052060051p//', statusCode=410, message=Message: The requested resource is no longer available at the server., getCauseInfo=[class: class io.netty.channel.ConnectTimeoutException, message: connection timed out: cdb-ms-prod-westus-fd4.documents.azure.com/101.13.12.5:14940]
```

如果在應用程式機器上已執行防火牆，請開啟直接模式使用的連接埠範圍 10,000 到 20,000。
另請遵循[主機電腦的連線限制](#connection-limit-on-host)。

#### <a name="http-proxy"></a>HTTP Proxy

如果您使用 HTTP Proxy，請確定它可以支援在 SDK `ConnectionPolicy` 中設定的連線數目。
否則就會遇到連線問題。

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>程式碼編寫模式無效：封鎖 Netty IO 執行緒

SDK 會使用 [Netty](https://netty.io/) IO 程式庫來與 Azure Cosmos DB 通訊。 SDK 具有 Async API，而且會使用 Netty 的非封鎖 IO API。 SDK 的 IO 工作會在 IO Netty 執行緒上執行。 IO Netty 執行緒的數目會設定為與應用程式電腦的 CPU 核心數目相同。 

Netty IO 執行緒僅適用於非封鎖的 Netty IO 工作。 SDK 會將其中一個 Netty IO 執行緒上的 API 引動過程結果傳回給應用程式的程式碼。 如果應用程式於 Netty 執行緒上收到結果之後，執行長時間持續的作業，SDK 可能沒有足夠的 IO 執行緒可執行其內部 IO 工作。 這類應用程式的程式碼編寫可能導致低輸送量、高延遲及 `io.netty.handler.timeout.ReadTimeoutException` 失敗。 因應措施是在您知道作業將很費時之後切換執行緒。

例如，看看以下將項目新增至容器的程式碼片段 (如需設定資料庫和容器的指引，請參閱[這裡](create-sql-api-java.md)。)您可能在 Netty 執行緒上執行需要超過幾毫秒的長時間持續性工作。 若是如此，您最終會進入以下狀態：沒有任何可處理 IO 工作的 Netty IO 執行緒。 因此，您會收到 ReadTimeoutException 失敗。

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-readtimeout"></a>Java SDK V4 (Maven com.azure::azure-cosmos) 非同步 API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TroubleshootNeedsSchedulerAsync)]

因應措施是變更要在其上執行費時工作的執行緒。 為您的應用程式定義排程器的單一執行個體。

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-scheduler"></a>Java SDK V4 (Maven com.azure::azure-cosmos) 非同步 API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TroubleshootCustomSchedulerAsync)]

您可能需要執行費時的工作，比方說，耗用大量運算資源的工作或封鎖 IO。 在此情況下，使用 `.publishOn(customScheduler)` API，將執行緒切換至 `customScheduler` 所提供的背景工作角色。

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-apply-custom-scheduler"></a>Java SDK V4 (Maven com.azure::azure-cosmos) 非同步 API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TroubleshootPublishOnSchedulerAsync)]

使用 `publishOn(customScheduler)`，您就能釋放 Netty IO 執行緒，並切換至您自己由 customScheduler 所提供的自訂執行緒。 這項修改可解決問題。 您不會再發生 `io.netty.handler.timeout.ReadTimeoutException` 失敗。

### <a name="request-rate-too-large"></a>要求率太大
此失敗為伺服器端失敗。 其指出您已取用您佈建的輸送量。 稍後重試。 如果您經常發生此失敗，請考慮增加集合輸送量。

* **依 getRetryAfterInMilliseconds 間隔實作輪詢**

    在進行效能測試期間，您應該增加負載，直到系統對小部分要求進行節流處理為止。 如果進行節流處理，用戶端應用程式應該降速，且持續時間達伺服器指定的重試間隔。 採用降速可確保您在重試之間花費最少的等待時間。

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>無法連線至 Azure Cosmos DB 模擬器

Azure Cosmos DB 模擬器的 HTTPS 憑證是自我簽署的。 針對要與模擬器搭配運作的 SDK，將模擬器憑證匯入到 Java TrustStore。 如需詳細資訊，請參閱[匯出 Azure Cosmos DB 模擬器憑證](local-emulator-export-ssl-certificates.md)。

### <a name="dependency-conflict-issues"></a>相依性衝突問題

Azure Cosmos DB Java SDK 會提取許多相依性；一般來說，如果您的專案相依性樹狀結構包含 Azure Cosmos DB Java SDK 相依的舊版成品，這可能會導致您在執行應用程式時產生未預期的錯誤。 如果您要對應用程式非預期地擲出例外狀況的原因進行偵錯，最好再次檢查您的相依性樹狀結構是否意外地提取舊版的一或多個 Azure Cosmos DB Java SDK 相依性。

這類問題的因應措施是找出哪些專案相依性會帶入舊版本，並排除舊版的可轉移相依性，並允許 Azure Cosmos DB Java SDK 帶入較新的版本。

若要識別哪些專案相依性會帶入 Azure Cosmos DB Java SDK 相依的舊版本，請針對您的專案 pom.xml 檔案執行下列命令：
```bash
mvn dependency:tree
```
如需詳細資訊，請參閱 [maven 相依性樹狀結構指南](https://maven.apache.org/plugins/maven-dependency-plugin/examples/resolving-conflicts-using-the-dependency-tree.html)。

一旦知道哪個專案相依性相依於舊版本，即可修改 POM 檔案中的相依性，並排除可轉移相依性，請遵循下列範例 (假設 *reactor-core* 是過時的相依性)：

```xml
<dependency>
  <groupId>${groupid-of-lib-which-brings-in-reactor}</groupId>
  <artifactId>${artifactId-of-lib-which-brings-in-reactor}</artifactId>
  <version>${version-of-lib-which-brings-in-reactor}</version>
  <exclusions>
    <exclusion>
      <groupId>io.projectreactor</groupId>
      <artifactId>reactor-core</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```

如需詳細資訊，請參閱[排除可轉移相依性指南](https://maven.apache.org/guides/introduction/introduction-to-optional-and-excludes-dependencies.html)。


## <a name="enable-client-sdk-logging"></a><a name="enable-client-sice-logging"></a>啟用用戶端 SDK 記錄

Azure Cosmos DB Java SDK v4 會使用 SLF4j 做為記錄外觀，以支援登入到 log4j 和 logback 等熱門記錄架構。

例如，如果您想要使用 log4j 作為記錄架構，請在您的 Java 類別路徑中新增下列程式庫。

```xml
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
  <version>${slf4j.version}</version>
</dependency>
<dependency>
  <groupId>log4j</groupId>
  <artifactId>log4j</artifactId>
  <version>${log4j.version}</version>
</dependency>
```

此外，也會新增 log4j 設定。
```
# this is a sample log4j configuration

# Set root logger level to DEBUG and its only appender to A1.
log4j.rootLogger=INFO, A1

log4j.category.com.azure.cosmos=DEBUG
#log4j.category.io.netty=INFO
#log4j.category.io.projectreactor=INFO
# A1 is set to be a ConsoleAppender.
log4j.appender.A1=org.apache.log4j.ConsoleAppender

# A1 uses PatternLayout.
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%d %5X{pid} [%t] %-5p %c - %m%n
```

如需詳細資訊，請參閱 [sfl4j 記錄手冊](https://www.slf4j.org/manual.html)。

## <a name="os-network-statistics"></a><a name="netstats"></a>OS 網路統計資料
執行 netstat 命令，以了解有多少個連線處於 `ESTABLISHED` 和 `CLOSE_WAIT` 狀態。

在 Linux上，您可以執行下列命令。
```bash
netstat -nap
```

在 Windows 上，您可以使用不同的引數旗標來執行相同的命令：
```bash
netstat -abn
```

只篩選出與 Azure Cosmos DB 端點連線的結果。

處於 `ESTABLISHED` 狀態且與 Azure Cosmos DB 端點連線的數目不能大於您所設定的連線集區大小。

與 Azure Cosmos DB 端點的許多連線可能處於 `CLOSE_WAIT` 狀態。 可能有超過 1,000 個。 很高的數字表示連線已建立且快速地中斷。 這種情況可能會造成問題。 如需詳細資訊，請參閱[常見問題和因應措施]一節。

 <!--Anchors-->
[常見問題和因應措施]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[主機電腦上的連線限制]: #connection-limit-on-host
[Azure SNAT (PAT) 連接埠耗盡]: #snat
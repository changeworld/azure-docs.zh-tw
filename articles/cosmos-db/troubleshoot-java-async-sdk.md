---
title: 診斷 Azure Cosmos DB 非同步 Java SDK v2 並進行疑難排解
description: 使用像是用戶端記錄的功能及其他協力廠商工具，在非同步 Java SDK v2 中，針對 Azure Cosmos DB 問題進行識別、診斷及疑難排解。
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 05/11/2020
ms.author: anfeldma
ms.devlang: java
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-java
ms.openlocfilehash: 9d3bb6925e11579deff914a930116d8ef464e39f
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92476887"
---
# <a name="troubleshoot-issues-when-you-use-the-azure-cosmos-db-async-java-sdk-v2-with-sql-api-accounts"></a>針對搭配使用 Azure Cosmos DB 非同步 Java SDK v2 和 SQL API 帳戶時所發生的問題進行疑難排解

> [!div class="op_single_selector"]
> * [Java SDK v4](troubleshoot-java-sdk-v4-sql.md)
> * [非同步 Java SDK v2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

> [!IMPORTANT]
> 這「不是」適用於 Azure Cosmos DB 的最新 Java SDK！ 您應該將專案升級到 [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md)，然後閱讀 Azure Cosmos DB Java SDK v4 [疑難排解指南](troubleshoot-java-sdk-v4-sql.md)。 遵循[移轉到 Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) 指南及 [Reactor 與 RxJava 之間的比較](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) \(英文\) 指南來升級。 
>
> 本文只涵蓋適用於 Azure Cosmos DB 非同步 Java SDK v2 的疑難排解。 如需詳細資訊，請參閱 Azure Cosmos DB 非同步 Java SDK v2 [版本資訊](sql-api-sdk-async-java.md)、[Maven 存放庫](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)，以及[效能提示](performance-tips-async-java.md)。
>

此文章涵蓋搭配 Azure Cosmos DB SQL API 帳戶使用 [Java Async SDK](sql-api-sdk-async-java.md) 時的常見問題、因應措施、診斷步驟與工具。
Java Async SDK 提供用戶端邏輯表示法來存取 Azure Cosmos DB SQL API。 此文章所說明的工具和方法，可以在您遇到任何問題時提供協助。

從此清單開始：

* 查看此文章的[常見問題和因應措施]一節。
* 查看 SDK，這是 [GitHub 上可用的開放原始碼](https://github.com/Azure/azure-cosmosdb-java)。 它有受到主動監視的[議題區段](https://github.com/Azure/azure-cosmosdb-java/issues)。 查看您是否有任何含有已提出因應措施的類似問題。
* 檢閱[效能祕訣](performance-tips-async-java.md)並遵循建議的做法。
* 如果找不到解決方案，請閱讀本文的其餘部分。 然後提出 [GitHub 問題](https://github.com/Azure/azure-cosmosdb-java/issues)。

## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>常見問題和因應措施

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>網路問題, Netty 讀取逾時失敗, 低輸送量, 高延遲

#### <a name="general-suggestions"></a>一般建議
* 確定應用程式會在與您 Azure Cosmos DB 帳戶相同的區域上執行。 
* 在應用程式執行所在的主機上檢查 CPU 使用量。 如果 CPU 使用量為 90% 或更高，請在具有更高組態的主機上執行您的應用程式。 或者，您可將負載分散於更多電腦上。

#### <a name="connection-throttling"></a>連線節流
連線節流的發生原因可能是因為[主機電腦上的連線限制]或 [Azure SNAT (PAT) 連接埠耗盡]。

##### <a name="connection-limit-on-a-host-machine"></a><a name="connection-limit-on-host"></a>主機電腦上的連線限制
某些 Linux 系統 (例如 Red Hat) 具有開啟檔案的總數上限。 Linux 中的通訊端會實作為檔案，因此，這個數字也會限制連線總數。
執行下列命令。

```bash
ulimit -a
```
允許的開啟檔案數目上限 (識別為 "nofile") 必須至少是連線集區大小的兩倍。 如需詳細資訊，請參閱[效能祕訣](performance-tips-async-java.md)。

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

例如，讓我們看看下列程式碼片段。 您可能在 Netty 執行緒上執行需要超過幾毫秒的長時間持續性工作。 若是如此，您最終會進入以下狀態：沒有任何可處理 IO 工作的 Netty IO 執行緒。 因此，您會收到 ReadTimeoutException 失敗。

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-readtimeout"></a>非同步 Java SDK v2 (Maven com.microsoft.azure::azure-cosmosdb)

```java
@Test
public void badCodeWithReadTimeoutException() throws Exception {
    int requestTimeoutInSeconds = 10;

    ConnectionPolicy policy = new ConnectionPolicy();
    policy.setRequestTimeoutInMillis(requestTimeoutInSeconds * 1000);

    AsyncDocumentClient testClient = new AsyncDocumentClient.Builder()
            .withServiceEndpoint(TestConfigurations.HOST)
            .withMasterKeyOrResourceToken(TestConfigurations.MASTER_KEY)
            .withConnectionPolicy(policy)
            .build();

    int numberOfCpuCores = Runtime.getRuntime().availableProcessors();
    int numberOfConcurrentWork = numberOfCpuCores + 1;
    CountDownLatch latch = new CountDownLatch(numberOfConcurrentWork);
    AtomicInteger failureCount = new AtomicInteger();

    for (int i = 0; i < numberOfConcurrentWork; i++) {
        Document docDefinition = getDocumentDefinition();
        Observable<ResourceResponse<Document>> createObservable = testClient
                .createDocument(getCollectionLink(), docDefinition, null, false);
        createObservable.subscribe(r -> {
                    try {
                        // Time-consuming work is, for example,
                        // writing to a file, computationally heavy work, or just sleep.
                        // Basically, it's anything that takes more than a few milliseconds.
                        // Doing such operations on the IO Netty thread
                        // without a proper scheduler will cause problems.
                        // The subscriber will get a ReadTimeoutException failure.
                        TimeUnit.SECONDS.sleep(2 * requestTimeoutInSeconds);
                    } catch (Exception e) {
                    }
                },

                exception -> {
                    //It will be io.netty.handler.timeout.ReadTimeoutException.
                    exception.printStackTrace();
                    failureCount.incrementAndGet();
                    latch.countDown();
                },
                () -> {
                    latch.countDown();
                });
    }

    latch.await();
    assertThat(failureCount.get()).isGreaterThan(0);
}
```
因應措施是變更要在其上執行費時工作的執行緒。 為您的應用程式定義排程器的單一執行個體。

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-scheduler"></a>非同步 Java SDK v2 (Maven com.microsoft.azure::azure-cosmosdb)

```java
// Have a singleton instance of an executor and a scheduler.
ExecutorService ex  = Executors.newFixedThreadPool(30);
Scheduler customScheduler = rx.schedulers.Schedulers.from(ex);
```
您可能需要執行費時的工作，比方說，耗用大量運算資源的工作或封鎖 IO。 在此情況下，使用 `.observeOn(customScheduler)` API，將執行緒切換至 `customScheduler` 所提供的背景工作角色。

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-applycustomscheduler"></a>非同步 Java SDK v2 (Maven com.microsoft.azure::azure-cosmosdb)

```java
Observable<ResourceResponse<Document>> createObservable = client
        .createDocument(getCollectionLink(), docDefinition, null, false);

createObservable
        .observeOn(customScheduler) // Switches the thread.
        .subscribe(
            // ...
        );
```
使用 `observeOn(customScheduler)`，您就能釋放 Netty IO 執行緒，並切換至您自己由 customScheduler 所提供的自訂執行緒。 這項修改可解決問題。 您不會再發生 `io.netty.handler.timeout.ReadTimeoutException` 失敗。

### <a name="connection-pool-exhausted-issue"></a>連線集區耗盡的問題

`PoolExhaustedException` 是一個用戶端失敗。 此失敗表示您的應用程式工作負載比 SDK 連線集區可提供的工作負載還要高。 增加連線集區大小或將負載散發到多個應用程式。

### <a name="request-rate-too-large"></a>要求率太大
此失敗為伺服器端失敗。 其指出您已取用您佈建的輸送量。 稍後重試。 如果您經常發生此失敗，請考慮增加集合輸送量。

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>無法連線至 Azure Cosmos DB 模擬器

Azure Cosmos DB 模擬器的 HTTPS 憑證是自我簽署的。 針對要與模擬器搭配運作的 SDK，將模擬器憑證匯入到 Java TrustStore。 如需詳細資訊，請參閱[匯出 Azure Cosmos DB 模擬器憑證](local-emulator-export-ssl-certificates.md)。

### <a name="dependency-conflict-issues"></a>相依性衝突問題

```console
Exception in thread "main" java.lang.NoSuchMethodError: rx.Observable.toSingle()Lrx/Single;
```

上述例外狀況意味著您相依於舊版的 RxJava 程式庫 (例如 1.2.2)。 我們的 SDK 依賴 RxJava 1.3.8，其具有舊版 RxJava 未提供的 API。 

這類問題的因應措施是識別哪些其他相依性會帶入 RxJava-1.2.2，並在 RxJava-1.2.2 上排除可轉移的相依性，然後允許 CosmosDB SDK 引進較新的版本。

若要識別哪一個程式庫會帶入 RxJava-1.2.2，請在您的專案 pom.xml 檔案旁執行下列命令：
```bash
mvn dependency:tree
```
如需詳細資訊，請參閱 [maven 相依性樹狀結構指南](https://maven.apache.org/plugins/maven-dependency-plugin/examples/resolving-conflicts-using-the-dependency-tree.html)。

一旦您識別出 RxJava-1.2.2 是您專案哪個其他相依性的可轉移相依性，您就可以在 pom 檔案中修改該程式庫的相依性，並排除 RxJava 可轉移相依性：

```xml
<dependency>
  <groupId>${groupid-of-lib-which-brings-in-rxjava1.2.2}</groupId>
  <artifactId>${artifactId-of-lib-which-brings-in-rxjava1.2.2}</artifactId>
  <version>${version-of-lib-which-brings-in-rxjava1.2.2}</version>
  <exclusions>
    <exclusion>
      <groupId>io.reactivex</groupId>
      <artifactId>rxjava</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```

如需詳細資訊，請參閱[排除可轉移相依性指南](https://maven.apache.org/guides/introduction/introduction-to-optional-and-excludes-dependencies.html)。


## <a name="enable-client-sdk-logging"></a><a name="enable-client-sice-logging"></a>啟用用戶端 SDK 記錄

Java Async SDK 會使用 SLF4j 作為記錄外觀，以支援登入到 log4j 和 logback 等熱門記錄架構。

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

log4j.category.com.microsoft.azure.cosmosdb=DEBUG
#log4j.category.io.netty=INFO
#log4j.category.io.reactivex=INFO
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
只篩選出與 Azure Cosmos DB 端點連線的結果。

處於 `ESTABLISHED` 狀態且與 Azure Cosmos DB 端點連線的數目不能大於您所設定的連線集區大小。

與 Azure Cosmos DB 端點的許多連線可能處於 `CLOSE_WAIT` 狀態。 可能有超過 1,000 個。 很高的數字表示連線已建立且快速地中斷。 這種情況可能會造成問題。 如需詳細資訊，請參閱[常見問題和因應措施]一節。

 <!--Anchors-->
[常見問題和因應措施]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[主機電腦上的連線限制]: #connection-limit-on-host
[Azure SNAT (PAT) 連接埠耗盡]: #snat
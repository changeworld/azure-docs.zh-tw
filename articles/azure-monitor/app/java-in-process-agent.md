---
title: Azure 監視器 Application Insights JAVA
description: 針對在任何環境中執行的 JAVA 應用程式進行應用程式效能監視，而不需要修改程式碼。 分散式追蹤和應用程式對應。
ms.topic: conceptual
ms.date: 03/29/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 3cab22c2271fd5874b4b094be65c36f5b5f3a22d
ms.sourcegitcommit: 287c20509c4cf21d20eea4619bbef0746a5cd46e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97371878"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights"></a>JAVA 無程式碼應用程式監視 Azure 監視器 Application Insights

監視 Java 無程式碼應用程式的重點在於簡單明瞭：不需進行任何程式碼變更，只要進行數個設定變更，就能啟用該 Java 代理程式。

 JAVA 代理程式可在任何環境中運作，並可讓您監視所有的 JAVA 應用程式。 換句話說，無論您是在內部部署的 Vm 上執行 JAVA 應用程式，在內部部署的 AKS，在 Windows 上，您可以將它命名為，JAVA 3.0 代理程式將會監視您的應用程式。

不再需要將 Application Insights JAVA SDK 新增至您的應用程式，因為3.0 代理程式會自動收集要求、相依性和記錄檔。

您仍然可以從應用程式傳送自訂遙測。 3.0 代理程式將會追蹤並將其與所有自動收集的遙測相互關聯。

3.0 代理程式支援 JAVA 8 和更新版本。

## <a name="quickstart"></a>快速入門

**1. 下載代理程式**

> [!WARNING]
> **如果您要從 3.0 Preview 升級**
>
> 請仔細檢查所有的設定 [選項](./java-standalone-config.md) ，因為 json 結構已完全變更，除了檔案名本身的所有小寫。

下載 [applicationinsights-agent-3.0.0 .jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0/applicationinsights-agent-3.0.0.jar)

**2. 將 JVM 指向代理程式**

新增 `-javaagent:path/to/applicationinsights-agent-3.0.0.jar` 至您應用程式的 JVM 引數

一般 JVM 引數包含 `-Xmx512m` 和 `-XX:+UseG1GC` 。 如果您知道要在何處加入這些，則您已經知道要在哪裡加入。

如需設定應用程式 JVM 引數的其他說明，請參閱 [更新 jvm 引數的秘訣](./java-standalone-arguments.md)。

**3. 將代理程式指向您的 Application Insights 資源**

如果您還沒有 Application Insights 資源，您可以遵循 [資源建立指南](./create-new-resource.md)中的步驟來建立新的資源。

藉由設定環境變數，將代理程式指向您的 Application Insights 資源：

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=...
```

或者，建立名為的設定檔 `applicationinsights.json` ，並將它放在與相同的目錄中 `applicationinsights-agent-3.0.0.jar` ，並包含下列內容：

```json
{
  "connectionString": "InstrumentationKey=..."
}
```

您可以在 Application Insights 資源中找到您的連接字串：

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights 連接字串":::

**4. 這樣就大功告成了！**

現在啟動您的應用程式，並移至 Azure 入口網站中的 Application Insights 資源，以查看您的監視資料。

> [!NOTE]
> 您的監視資料可能需要幾分鐘的時間才會顯示在入口網站中。


## <a name="configuration-options"></a>設定選項

在檔案中 `applicationinsights.json` ，您可以另外設定：

* 雲端角色名稱
* 雲端角色執行個體
* 取樣
* JMX 計量
* 自訂維度
*  (預覽版的遙測處理器) 
* 自動收集的記錄
* 自動收集的 Micrometer 計量 (包括彈簧開機傳動標準) 
* 活動訊號
* HTTP Proxy
* 自我診斷

如需完整的詳細資訊，請參閱設定 [選項](./java-standalone-config.md) 。

## <a name="auto-collected-requests-dependencies-logs-and-metrics"></a>自動收集的要求、相依性、記錄和計量

### <a name="requests"></a>Requests

* JMS 取用者
* Kafka 取用者
* Netty/WebFlux
* Servlet
* 春季排程

### <a name="dependencies-with-distributed-trace-propagation"></a>分散式追蹤傳播的相依性

* Apache HttpClient 和 HttpAsyncClient
* gRPC
* HttpURLConnection
* JMS
* Kafka
* Netty 用戶端
* OkHttp

### <a name="other-dependencies"></a>其他相依性

* Cassandra
* JDBC
* MongoDB (async 和 sync) 
* Redis (萵苣和 Jedis) 

### <a name="logs"></a>記錄

* util。記錄
* Log4j (包括 MDC 屬性) 
* SLF4J/Logback (包括 MDC 屬性) 

### <a name="metrics"></a>計量

* Micrometer (包括彈簧開機傳動標準) 
* JMX 計量

## <a name="send-custom-telemetry-from-your-application"></a>從您的應用程式傳送自訂遙測

我們在 3.0 + 中的目標是要讓您使用標準 Api 來傳送自訂遙測。

我們目前支援 Micrometer、熱門的記錄架構，以及目前的 JAVA 2.x SDK Application Insights。
Application Insights JAVA 3.0 會自動捕獲透過這些 Api 傳送的遙測資料，並將其與自動收集的遙測相互關聯。

### <a name="supported-custom-telemetry"></a>支援的自訂遙測

下表表示目前支援的自訂遙測類型，可讓您用來補充 JAVA 3.0 代理程式。 總而言之，透過 micrometer 支援自訂計量、自訂例外狀況和追蹤可透過記錄架構啟用，而任何類型的自訂遙測都可透過 [Application Insights JAVA 2.X SDK](#send-custom-telemetry-using-application-insights-java-2x-sdk)來支援。

|                     | Micrometer | Log4j、logback、七月 | 2.x SDK |
|---------------------|------------|---------------------|---------|
| **自訂事件**   |            |                     |  是    |
| **自訂計量**  |  是       |                     |  是    |
| **Dependencies** (相依性)    |            |                     |  是    |
| **例外狀況**      |            |  是                |  是    |
| **頁面流覽**      |            |                     |  是    |
| **要求**        |            |                     |  是    |
| **追蹤**          |            |  是                |  是    |

我們目前未規劃 Application Insights 3.0 發行 SDK。

Application Insights JAVA 3.0 已在接聽傳送至 Application Insights JAVA 2.x SDK 的遙測。 這項功能是現有2.x 使用者升級案例中很重要的一部分，它會在 OpenTelemetry API 正式運作之前，在自訂遙測支援中填滿重要的間隔。

### <a name="send-custom-metrics-using-micrometer"></a>使用 Micrometer 傳送自訂計量

將 Micrometer 新增至您的應用程式：

```xml
<dependency>
  <groupId>io.micrometer</groupId>
  <artifactId>micrometer-core</artifactId>
  <version>1.6.1</version>
</dependency>
```

使用 Micrometer [global registry](https://micrometer.io/docs/concepts#_global_registry) 建立計量：

```java
static final Counter counter = Metrics.counter("test_counter");
```

並使用該記錄計量來記錄計量：

```java
counter.increment();
```

### <a name="send-custom-traces-and-exceptions-using-your-favorite-logging-framework"></a>使用您最愛的記錄架構傳送自訂追蹤和例外狀況

Log4j、Logback 和 util 會自動檢測記錄，而透過這些記錄架構執行的記錄會自動收集為追蹤和例外狀況遙測。

依預設，只有在資訊層級或更高版本執行記錄時，才會收集記錄。
請參閱如何變更此層級的設定 [選項](./java-standalone-config.md#auto-collected-logging) 。

如果您想要將自訂維度附加至您的記錄，您可以使用 [Log4j 1.2 mdc](https://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/MDC.html)、 [Log4j 2 MDC](https://logging.apache.org/log4j/2.x/manual/thread-context.html)或 [Logback mdc](http://logback.qos.ch/manual/mdc.html)，Application Insights JAVA 3.0 會自動將這些 MDC 屬性作為追蹤和例外狀況遙測的自訂維度來捕捉。

### <a name="send-custom-telemetry-using-application-insights-java-2x-sdk"></a>使用 JAVA 2.x SDK Application Insights 傳送自訂遙測

新增 `applicationinsights-core-2.6.2.jar` 至您的應用程式 (Application Insights JAVA 3.0 支援所有2.x 版，但如果您有選擇) ，則值得使用最新版本：

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-core</artifactId>
  <version>2.6.2</version>
</dependency>
```

建立 TelemetryClient：

  ```java
static final TelemetryClient telemetryClient = new TelemetryClient();
```

並使用它來傳送自訂遙測：

##### <a name="events"></a>事件

```java
telemetryClient.trackEvent("WinGame");
```

##### <a name="metrics"></a>計量

```java
telemetryClient.trackMetric("queueLength", 42.0);
```

##### <a name="dependencies"></a>相依性

```java
boolean success = false;
long startTime = System.currentTimeMillis();
try {
    success = dependency.call();
} finally {
    long endTime = System.currentTimeMillis();
    RemoteDependencyTelemetry telemetry = new RemoteDependencyTelemetry();
    telemetry.setTimestamp(new Date(startTime));
    telemetry.setDuration(new Duration(endTime - startTime));
    telemetryClient.trackDependency(telemetry);
}
```

##### <a name="logs"></a>記錄

```java
telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

##### <a name="exceptions"></a>例外狀況

```java
try {
    ...
} catch (Exception e) {
    telemetryClient.trackException(e);
}
```

---
title: Azure 監視器 Application Insights JAVA
description: 針對在任何環境中執行的 JAVA 應用程式進行應用程式效能監視，而不需要修改程式碼。 分散式追蹤和應用程式對應。
ms.topic: conceptual
ms.date: 03/29/2020
ms.openlocfilehash: 07be6a4ff08700ee9407fbf39946b7c24abbc01a
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93377032"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights"></a>JAVA 無程式碼應用程式監視 Azure 監視器 Application Insights

監視 Java 無程式碼應用程式的重點在於簡單明瞭：不需進行任何程式碼變更，只要進行數個設定變更，就能啟用該 Java 代理程式。

 JAVA 代理程式可在任何環境中運作，並可讓您監視所有的 JAVA 應用程式。 換句話說，無論您是在內部部署的 Vm 上執行 JAVA 應用程式，在內部部署的 AKS，在 Windows 上，您可以將它命名為，JAVA 3.0 代理程式將會監視您的應用程式。

不再需要將 Application Insights JAVA SDK 新增至您的應用程式，因為3.0 代理程式會 autocollects 要求、相依性和記錄檔。

您仍然可以從應用程式傳送自訂遙測。 3.0 代理程式將會追蹤並將其與所有實驗自動收集遙測相互關聯。

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
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=00000000-0000-0000-0000-000000000000
```

或者，建立名為的設定檔 `applicationinsights.json` ，並將它放在與相同的目錄中 `applicationinsights-agent-3.0.0.jar` ，並包含下列內容：

```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000"
  }
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
* 遙測處理器
* 自動收集的記錄
* 自動收集的 Micrometer 計量 (包括彈簧開機傳動標準) 
* 活動訊號
* HTTP Proxy
* 自我診斷

如需完整的詳細資訊，請參閱設定 [選項](./java-standalone-config.md) 。

## <a name="autocollected-requests-dependencies-logs-and-metrics"></a>實驗自動收集要求、相依性、記錄和計量

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

## <a name="sending-custom-telemetry-from-your-application"></a>從您的應用程式傳送自訂遙測

我們在 3.0 + 中的目標是要讓您使用標準 Api 來傳送自訂遙測。

我們支援 Micrometer、OpenTelemetry API 和熱門的記錄架構。 Application Insights JAVA 3.0 會自動捕獲遙測資料，並將其與所有實驗自動收集遙測相互關聯。

### <a name="supported-custom-telemetry"></a>支援的自訂遙測

下表表示目前支援的自訂遙測類型，可讓您用來補充 JAVA 3.0 代理程式。 總而言之，透過 micrometer 支援自訂計量、自訂例外狀況和追蹤可透過記錄架構啟用，而任何類型的自訂遙測都可透過 [Application Insights JAVA 2.X SDK](#sending-custom-telemetry-using-application-insights-java-sdk-2x)來支援。 

|                     | Micrometer | Log4j、logback、七月 | 2.x SDK |
|---------------------|------------|---------------------|---------|
| **自訂事件**   |            |                     |  是    |
| **自訂計量**  |  是       |                     |  是    |
| **Dependencies** (相依性)    |            |                     |  是    |
| **例外狀況**      |            |  是                |  是    |
| **頁面檢視**      |            |                     |  是    |
| **要求**        |            |                     |  是    |
| **追蹤**          |            |  是                |  是    |

我們目前未規劃 Application Insights 3.0 發行 SDK。

Application Insights JAVA 3.0 已在接聽傳送至 Application Insights JAVA SDK 2.x 的遙測。 這項功能是現有2.x 使用者升級案例中很重要的一部分，它會在 OpenTelemetry API 正式運作之前，在自訂遙測支援中填滿重要的間隔。

## <a name="sending-custom-telemetry-using-application-insights-java-sdk-2x"></a>使用 JAVA SDK 2.x Application Insights 傳送自訂遙測

新增 `applicationinsights-core-2.6.0.jar` 至您的應用程式 (Application Insights JAVA 3.0 支援所有2.x 版，但如果您有選擇) ，則值得使用最新版本：

```xml
  <dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>applicationinsights-core</artifactId>
    <version>2.6.0</version>
  </dependency>
```

建立 TelemetryClient：

  ```java
private static final TelemetryClient telemetryClient = new TelemetryClient();
```

並使用它來傳送自訂遙測。

### <a name="events"></a>事件

  ```java
telemetryClient.trackEvent("WinGame");
```
### <a name="metrics"></a>計量

您可以透過 [Micrometer](https://micrometer.io)傳送度量遙測：

```java
  Counter counter = Metrics.counter("test_counter");
  counter.increment();
```

或者，您也可以使用 Application Insights JAVA SDK 2.x：

```java
  telemetryClient.trackMetric("queueLength", 42.0);
```

### <a name="dependencies"></a>相依性

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

### <a name="logs"></a>記錄
您可以透過您最愛的記錄架構來傳送自訂記錄檔遙測。

或者，您也可以使用 Application Insights JAVA SDK 2.x：

```java
  telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

### <a name="exceptions"></a>例外狀況
您可以透過您最愛的記錄架構來傳送自訂例外狀況遙測。

或者，您也可以使用 Application Insights JAVA SDK 2.x：

```java
  try {
      ...
  } catch (Exception e) {
      telemetryClient.trackException(e);
  }
```

## <a name="upgrading-from-application-insights-java-sdk-2x"></a>從 Application Insights JAVA SDK 2.x 升級

如果您已經在應用程式中使用 Application Insights JAVA SDK 2.x，則不需要將它移除。
JAVA 3.0 代理程式會偵測到它，並將您透過 JAVA SDK 2.x 傳送的任何自訂遙測進行抓取和相互關聯，同時隱藏 JAVA SDK 2.x 所執行的任何自動收集，以避免重複的遙測。

如果您使用 Application Insights 2.x 代理程式，則必須移除 `-javaagent:` 指向2.x 代理程式的 JVM 參數。

> [!NOTE]
> 使用3.0 代理程式時，將不會執行 JAVA SDK 2.x TelemetryInitializers 和 TelemetryProcessors。
> 許多先前需要這些案例的使用案例，可以藉由設定 [自訂維度](./java-standalone-config.md#custom-dimensions) 或設定 [遙測處理器](./java-standalone-telemetry-processors.md)來在3.0 中解決。

> [!NOTE]
> 3.0 在單一 JVM 中尚不支援多個檢測金鑰。

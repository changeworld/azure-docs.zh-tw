---
title: 在任何環境中監視 Java 應用程式 - Azure 監視器應用程式見解
description: 應用程式效能監視,適用於在任何環境中運行的 Java 應用程式,而無需檢測應用程式。 分散式跟蹤和應用程式映射。
ms.topic: conceptual
ms.date: 03/29/2020
ms.openlocfilehash: 5706d5a74bd6850a237f7418b1a86a8e9c7762e1
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604584"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights---public-preview"></a>Java 無程式應用程式監視 Azure 監視器應用程式見解 ─ 一般預覽

Java 無程式應用程式監視是簡單性的 - 沒有代碼更改,JAva代理只能通過幾個配置更改來啟用。

 Java 代理在任何環境中工作,並允許您監視所有 Java 應用程式。 換句話說,無論您是在 VM 上、本地還是 AKS、Windows、Linux 上運行 Java 應用,JAva 3.0 代理都將監視你的應用。

不再需要將應用程式見解 Java SDK 添加到應用程式,因為 3.0 代理自行收集請求、依賴項和日誌。

您仍然可以從應用程式發送自定義遙測。 3.0 代理將追蹤它與所有自動收集的遙測數據並關聯。

## <a name="quickstart"></a>快速入門

**1. 下載代理程式**

下載[應用程式見解-代理-3.0.0-PREVIEW.3.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.3/applicationinsights-agent-3.0.0-PREVIEW.3.jar)

**2. 將 JVM 指向代理**

新增到`-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.3.jar`應用程式的 JVM args

典型的 JVM args 包括`-Xmx512m`與`-XX:+UseG1GC`。 因此,如果您知道要在哪裡添加這些,那麼您已經知道在哪裡添加此。

有關設定應用程式的 JVM args 的其他說明,請參閱[3.0 預覽:更新 JVM args 的提示](https://github.com/microsoft/ApplicationInsights-Java/wiki/3.0-Preview:-Tips-for-updating-your-JVM-args)。

**3. 將代理指向您的應用程式見解資源**

如果還沒有應用程式見解資源,則可以按照[資源創建指南](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)中的步驟創建新的資源。

透過設定環境變數,將代理指向您的應用程式見解資源:

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=00000000-0000-0000-0000-000000000000
```

或建立名為的`ApplicationInsights.json`設定檔,並將其放置在`applicationinsights-agent-3.0.0-PREVIEW.3.jar`與的目錄中,包含以下內容:

```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000"
  }
}
```

您可以在應用程式見解資源中找到連接字串:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="應用程式見解連線字串":::

**4. 就這個了!**

現在啟動應用程式,然後轉到 Azure 門戶中的應用程式見解資源以查看監視數據。

> [!NOTE]
> 監視數據可能需要幾分鐘才能顯示在門戶中。


## <a name="configuration-options"></a>設定選項

在檔案中`ApplicationInsights.json`,您還可以另外設定:

* 雲角色名稱
* 雲端角色執行個體
* 應用程式記錄擷取
* JMX 指標
* Micrometer
* Heartbeat
* 取樣
* HTTP 代理
* 自我診斷

請參考[3.0 公共預覽版的詳細資訊:設定選項](https://github.com/microsoft/ApplicationInsights-Java/wiki/3.0-Preview:-Configuration-Options)。

## <a name="autocollected-requests-dependencies-logs-and-metrics"></a>自動收集的請求、依賴項、日誌和指標

### <a name="requests"></a>Requests

* JMS 消費者
* 卡夫卡消費者
* 淨/網路流
* Servlet
* 春季計劃

### <a name="dependencies-with-distributed-trace-propagation"></a>具有分散式追蹤傳播的依賴項

* 阿帕契 HTTPClient 和 HttpAsync 用戶端
* gRPC
* java.net.HTTPURL連接
* JMS
* Kafka
* 淨用戶端
* OkHTTP

### <a name="other-dependencies"></a>其他依賴項

* Cassandra
* JDBC
* 蒙戈DB(非同步)
* 雷迪斯(萊圖斯和絕地)

### <a name="logs"></a>記錄

* java.util.日誌記錄
* Log4j
* SLF4J/紀錄

### <a name="metrics"></a>計量

* Micrometer
* JMX 指標

## <a name="sending-custom-telemetry-from-your-application"></a>從應用程式傳送自訂遙測

3.0+ 的目標是允許您使用標準 API 發送自定義遙測數據。

我們支援微數、開放遙測 API 和流行的日誌記錄框架。 應用程式見解 Java 3.0 將自動捕獲遙測數據,並將其與所有自動收集的遙測數據相關聯。

因此,我們不打算此時發佈具有應用程式見解 3.0 的 SDK。

應用程式見解 Java 3.0 已經在偵聽發送到應用程式見解 Java SDK 2.x 的遙測數據。 此功能是現有 2.x 用戶升級故事的重要組成部分,它填補了我們自定義遙測支援中的一個重要空白,直到 Open遙測 API 成為 GA。

## <a name="sending-custom-telemetry-using-application-insights-java-sdk-2x"></a>使用應用程式見解 Java SDK 2.x 傳送自訂遙測

新增到`applicationinsights-core-2.6.0.jar`應用程式(應用程式見解 Java 3.0 支援所有 2.x 版本,但如果您有選擇,則值得使用最新版本):

```xml
  <dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>applicationinsights-core</artifactId>
    <version>2.6.0</version>
  </dependency>
```

建立遙測客戶端:

  ```java
private static final TelemetryClient telemetryClient = new TelemetryClient();
```

並將其用於發送自定義遙測。

### <a name="events"></a>事件

  ```java
telemetryClient.trackEvent("WinGame");
```
### <a name="metrics"></a>計量

您可以通過[縮數計](https://micrometer.io)傳送指標遙測 :

```java
  Counter counter = Metrics.counter("test_counter");
  counter.increment();
```

或者您也可以使用應用程式見解 Java SDK 2.x:

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
您可以通過您最喜愛的日誌記錄框架發送自定義日誌遙測。

或者您也可以使用應用程式見解 Java SDK 2.x:

```java
  telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

### <a name="exceptions"></a>例外狀況
您可以通過您最喜愛的日誌記錄框架發送自定義異常遙測。

或者您也可以使用應用程式見解 Java SDK 2.x:

```java
  try {
      ...
  } catch (Exception e) {
      telemetryClient.trackException(e);
  }
```

## <a name="upgrading-from-application-insights-java-sdk-2x"></a>從應用程式洞察 Java SDK 2.x 升級

如果您已經在應用程式中使用應用程式見解 Java SDK 2.x,則無需將其刪除。 Java 3.0 代理將檢測它,並捕獲並關聯您透過 Java SDK 2.x 發送的任何自定義遙測數據,同時禁止 JAVA SDK 2.x 執行的任何自動集合以防止重複捕獲。

> [!NOTE]
> 注意:使用 3.0 代理時,將不會運行 Java SDK 2.x 遙測初始化器和遙測處理器。

---
title: 快速入門:具有 Azure 應用程式見解的 Java Web 應用分析
description: '使用 Application Insights 針對 Java Web 應用程式進行應用程式效能監視。 '
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 05/24/2019
ms.openlocfilehash: 2355cb7ab995cab3060c7a94c9e7ea344bd9e92b
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984635"
---
# <a name="quickstart-get-started-with-application-insights-in-a-java-web-project"></a>快速入門:在 Java Web 專案中開始應用程式見解

在此快速入門中,您可以使用應用程式見解自動檢測請求、跟蹤依賴項和收集性能計數器、診斷性能問題和異常,並編寫代碼來跟蹤使用者對應用執行哪些操作。

Application Insights 是一項 Web 開發人員可延伸的分析服務，可幫助您了解即時應用程式的效能和使用情形。 Application Insights 支援 Linux、Unix 或 Windows 上執行的 Java 應用程式。

## <a name="prerequisites"></a>Prerequisites

* 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* 一個功能正常的JAVA應用程式。

## <a name="get-an-application-insights-instrumentation-key"></a>取得 Application Insights 檢測金鑰

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在 Azure 入口網站中，建立 Application Insights 資源。 將應用程式類型設定為 Java Web 應用程式。

3. 尋找新資源的檢測金鑰。 您很快需要將此金鑰貼到程式碼專案中。

    ![在新資源概觀中，按一下 [屬性] 並複製檢測金鑰](./media/java-get-started/instrumentation-key-001.png)

## <a name="add-the-application-insights-sdk-for-java-to-your-project"></a>將 Java 適用的 Application Insights SDK 加入至專案

*選擇項目類型。*

# <a name="maven"></a>[Maven](#tab/maven)

如果專案已設置為使用 Maven 進行生成,請將以下代碼合併到*pom.xml*檔中。

然後重新整理專案相依性，以下載程式庫。

```XML
    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web-auto</artifactId>
        <!-- or applicationinsights-web for manual web filter registration -->
        <!-- or applicationinsights-core for bare API -->
        <version>2.5.0</version>
      </dependency>
    </dependencies>
```

# <a name="gradle"></a>[Gradle](#tab/gradle)

如果專案已設置為使用 Gradle 進行生成,請將以下代碼合併到*build.Gradle*檔中。

然後重新整理專案相依性，以下載程式庫。

```gradle
    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web-auto', version: '2.5.0'
      // or applicationinsights-web for manual web filter registration
      // or applicationinsights-core for bare API
    }
```

# <a name="other-types"></a>[其他類型](#tab/other)

下載[最新版本](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest)並將所需的檔案複製到您的專案中，並取代任何先前的版本。

---

### <a name="questions"></a>問題
* *`-web-auto``-web`和`-core`元件之間的關係是什麼?*
  * `applicationinsights-web-auto`通過在運行時自動註冊應用程式見解 servlet 篩選器,為您提供追蹤 HHt tcritor 的指標。
  * `applicationinsights-web`還為您提供追蹤 HTHServlet 請求計數和回應時間的指標,但需要在應用程式中手動註冊應用程式見解 servlet 篩選器。
  * `applicationinsights-core`例如,如果應用程式不是基於 servlet 的,則僅為您提供裸機 API。
  
* 如果將 SDK 升級為最新版本？**
  * 如果你使用的是格雷德或馬文...
    * 更新產生檔以指定最新版本。
  * 如果您手動管理相依項...
    * 下載最新的 [Application Insights SDK for Java](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) 並取代舊的。 [SDK 版本資訊](https://github.com/Microsoft/ApplicationInsights-Java#release-notes)中會說明變更內容。

## <a name="add-an-applicationinsightsxml-file"></a>新增*應用程式的音效檔.xml*檔案
將*ApplicationInsights.xml*添加到專案中的資源資料夾中,或確保將其添加到專案的部署類路徑中。 將下列 XML 複製到其中。

將檢測密鑰替換為從 Azure 門戶獲得的檢測密鑰。

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">

   <!-- The key from the portal: -->
   <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>

   <!-- HTTP request component (not required for bare API) -->
   <TelemetryModules>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
   </TelemetryModules>

   <!-- Events correlation (not required for bare API) -->
   <!-- These initializers add context data to each event -->
   <TelemetryInitializers>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
   </TelemetryInitializers>

</ApplicationInsights>
```

或者,配置檔可以位於應用程式可存取的任何位置。  系統屬性`-Dapplicationinsights.configurationDirectory`指定包含*應用程式 Insights.xml*的目錄。 例如，位於 `E:\myconfigs\appinsights\ApplicationInsights.xml` 的組態檔是使用屬性 `-Dapplicationinsights.configurationDirectory="E:\myconfigs\appinsights"` 來設定。

* 檢測金鑰會隨著遙測的每個項目傳送，並告知 Application Insights 在您的資源中顯示它。
* HTTP 要求元件是選用的。 它會自動將要求和回應時間的遙測傳送到入口網站。
* 事件相互關聯是 HTTP 要求元件的補充。 它將識別碼分配給伺服器收到的每個請求。 然後,它將此標識符作為屬性添加到遙測的每個項,作為屬性"Operation.Id"。 它可讓您相互關聯與每個要求關聯的遙測，方法是在 [診斷搜尋][diagnostic]中設定篩選器。

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>設定檢測金鑰的替代方法
Application Insights SDK 會依此順序尋找此金鑰︰

1. 系統屬性: -DAPPINSIGHTS_INSTRUMENTATIONKEY=your_ikey
2. 環境變數:APPINSIGHTS_INSTRUMENTATIONKEY
3. 設定檔:*應用程式見解.xml*

您也可以 [在程式碼中設定](../../azure-monitor/app/api-custom-events-metrics.md#ikey)：

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="add-agent"></a>新增代理

[安裝 Java 代理](java-agent.md)以捕獲傳出 HTTP 呼叫、JDBC 查詢、應用程式紀錄記錄和更好的作業命名。

## <a name="run-your-application"></a>執行您的應用程式
在您的開發電腦上以偵錯模式執行應用程式，或發佈至您的伺服器。

## <a name="view-your-telemetry-in-application-insights"></a>在 Application Insights 中檢視遙測
返回 [Microsoft Azure 入口網站](https://portal.azure.com)中的 Application Insights 資源。

[概觀] 刀鋒視窗上會顯示 HTTP 要求資料。 (如果沒有出現，請稍等片刻，然後按一下 [重新整理]。)

![概觀範例資料的螢幕擷取畫面](./media/java-get-started/overview-graphs.png)

[瞭解有關指標的更多詳細資訊。][metrics]

按一下任何圖表以查看詳細彙總度量。

![應用程式見解失敗窗格與圖表](./media/java-get-started/006-barcharts.png)

<!--
[TODO update image with 2.5.0 operation naming provided by agent]
-->

### <a name="instance-data"></a>執行個體資料
點選特定要求類型以查看個別執行個體。

![鑽取特定範例檢視](./media/java-get-started/007-instance.png)

### <a name="analytics-powerful-query-language"></a>分析︰功能強大的查詢語言
當您累積更多資料時，您就可以執行查詢以彙總資料並找出個別執行個體。  [分析](../../azure-monitor/app/analytics.md) 是一項強大的工具，既可了解效能和使用情況，也可進行診斷。

![分析的範例](./media/java-get-started/0025.png)

## <a name="install-your-app-on-the-server"></a>在伺服器上安裝您的 App
現在將您的應用程式發佈至伺服器供人使用，然後查看入口網站顯示的遙測。

* 請確定您的防火牆允許應用程式將遙測傳送至這些連接埠：

  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443

* 如果連出流量必須透過防火牆路由傳送，定義系統屬性 `http.proxyHost` 和 `http.proxyPort`。

* 在 Windows 伺服器上，安裝：

  * [Microsoft Visual C++ 可轉散發套件](https://www.microsoft.com/download/details.aspx?id=40784)

    (此元件會啟用效能計數器。)

## <a name="azure-app-service-config-spring-boot"></a>Azure 應用服務設定(彈簧啟動)

在 Windows 上執行的 Spring Boot 應用需要在 Azure 應用服務上運行其他配置。 變更**Web.config**並新增以下設定:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <system.webServer>
        <handlers>
            <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified"/>
        </handlers>
        <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\AzureWebAppExample-0.0.1-SNAPSHOT.jar&quot;">
        </httpPlatform>
    </system.webServer>
</configuration>
```

## <a name="exceptions-and-request-failures"></a>例外狀況與要求失敗
應用程式見解 Web 篩選器會自動收集未處理的異常和請求失敗。

若要收集有關其他異常的資料,可以在[程式碼中插入用於追蹤Exception()的呼叫][apiexceptions]。

## <a name="monitor-method-calls-and-external-dependencies"></a>監視方法呼叫和外部相依性
[安裝 Java 代理程式](java-agent.md) 以記錄指定的內部方法和透過 JDBC 發出的呼叫與計時資料。

對於自動操作命名。

## <a name="w3c-distributed-tracing"></a>W3C 分散式追蹤

Application Insights Java SDK 現在支援 [W3C 分散式追蹤](https://w3c.github.io/trace-context/)。

我們的[相互關聯](correlation.md)文章會進一步說明內送 SDK 組態。

外寄 SDK 組態則定義於 [AI-Agent.xml](java-agent.md) 檔案中。

## <a name="performance-counters"></a>效能計數器
開啟**調查**,**指標**, 查看一系列效能計數器.

![選擇行程專用位元組的指標窗格螢幕擷取](./media/java-get-started/011-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>自訂效能計數器集合
要關閉標準效能計數器集的集合,請在*應用程式 Insights.xml*檔案的根節點下新增以下代碼:

```XML
    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>
```

### <a name="collect-additional-performance-counters"></a>收集其他效能計數器
您可以指定要收集的其他效能計數器。

#### <a name="jmx-counters-exposed-by-the-java-virtual-machine"></a>JMX 計數器 (由虛擬機器公開)

```XML
    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>
```

* `displayName` – Application Insights 入口網站中顯示的名稱。
* `objectName` – JMX 物件名稱。
* `attribute` – 要提取的 JMX 物件名稱的屬性
* `type`( 選擇性的) - JMX 物件的屬性類型:
  * 預設值：簡易類型，例如 int 或 long。
  * `composite`：效能計數器資料的格式為 'Attribute.Data'
  * `tabular`：效能計數器資料的格式為資料表列

#### <a name="windows-performance-counters"></a>Windows 效能計數器
每個 [Windows 效能計數器](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) 是類別的成員 (以欄位是類別成員的相同方式)。 類別可以是全域，或可以有一定數量或指定的執行個體。

```XML
    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>
```

* displayName – Application Insights 入口網站中顯示的名稱。
* categoryName – 與此效能計數器有關聯的效能計數器類別 (效能物件)。
* counterName – 效能計數器的名稱。
* instanceName – 效能計數器類別執行個體的名稱，或如果類別包含單一執行個體，則為空白字串 ("")。 如果 categoryName 為 Process，而您要收集的效能計數器來自應用程式執行所在的目前 JVM 處理程序，請指定 `"__SELF__"`。

### <a name="unix-performance-counters"></a>Unix 效能計數器
* [使用 Application Insights 外掛程式安裝 collectd](java-collectd.md) ，來取得各種不同的系統和網路資料。

## <a name="get-user-and-session-data"></a>取得使用者與工作階段資料
好了，您現在正在從 Web 服務傳送遙測資料。 現在若要取得應用程式的完整 360 度檢視，可以加入多個監視：

* [將遙測加入至您的網頁][usage] ，即可監視頁面檢視和使用者度量。
* [設定 Web 測試][availability] ，以確認應用程式處於線上狀態且能夠回應。

## <a name="send-your-own-telemetry"></a>傳送您自己的遙測
既然您已安裝 SDK，您可以使用 API 來傳送自己的遙測。

* [追蹤自訂事件和度量][api]，以了解使用者正對應用程式進行的動作。
* [搜尋事件和記錄][diagnostic]以協助診斷問題。

## <a name="availability-web-tests"></a>可用性 Web 測試
Application Insights 可讓您定期測試網站，以檢查網站運作中且正常回應。

[詳細瞭解如何設置可用性 Web 測試。][availability]

## <a name="questions-problems"></a>有疑問嗎？ 有問題嗎？
[疑難排解 Java](java-troubleshoot.md)

## <a name="next-steps"></a>後續步驟
* [監視相依性呼叫](java-agent.md)
* [監視 Unix 效能計數器](java-collectd.md)
* 新增[對網頁的監視](javascript.md)，以監視頁面載入時間、AJAX 呼叫、瀏覽器例外狀況。
* 撰寫[自訂遙測](../../azure-monitor/app/api-custom-events-metrics.md)，以追蹤瀏覽器中或在伺服器上的使用情況。
* 使用[Analytics(分析)](../../azure-monitor/app/analytics.md)對來自應用的遙測進行強大的查詢
* 如需詳細資訊，請瀏覽[適用於 Java 開發人員的 Azure](/java/azure)。

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#trackexception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[usage]: javascript.md

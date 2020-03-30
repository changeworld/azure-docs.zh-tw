---
title: JAVA Web 應用效能監控 - Azure 應用程式見解
description: 使用 Application Insights 延伸 Java 網站的效能和使用量監視。
ms.topic: conceptual
ms.date: 01/10/2019
ms.openlocfilehash: b29618179d22eac97a07bf41906465aba1fd7929
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657022"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>監視 JAVA Web 應用中的依賴項、捕獲的異常和方法執行時間


如果您已[使用 Application Insights 檢測您的 Java Web 應用程式][java]，您可以使用 Java 代理程式獲得更深入的見解，而不需變更任何程式碼：

* **相依性** ：您的應用程式對其他元件呼叫的相關資料，包括：
  * 通過 Apache HttpClient、OkHttp 進行傳出`java.net.HttpURLConnection`HTTP**調用**並捕獲。
  * 通過絕地用戶端撥打**的雷迪斯電話**被捕獲。
  * **JDBC 查詢**- 對於 MySQL 和 PostgreSQL，如果調用時間超過 10 秒，代理將報告查詢計劃。

* **應用程式日誌記錄：** 捕獲應用程式日誌並將其與 HTTP 要求和其他遙測資料相關聯
  * **日誌4j 1.2**
  * **日誌4j2**
  * **日誌**

* **更好的操作命名：（** 用於聚合門戶中的請求）
  * **彈**簧 -`@RequestMapping`基於 。
  * **JAX-RS** -`@Path`基於 。 

若要使用 Java 代理程式，您要在伺服器上安裝它。 您必須使用 [Application Insights Java SDK][java] 檢測您的 Web 應用程式。 

## <a name="install-the-application-insights-agent-for-java"></a>安裝 Java 的 Application Insights 代理程式
1. 在執行 Java 伺服器的電腦上[下載代理程式](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest)。 請確定下載的 Java 代理程式版本與 Application Insights Java SDK 核心和網頁套件相同。
2. 編輯應用程式伺服器啟動腳本，並添加以下 JVM 參數：
   
    `-javaagent:<full path to the agent JAR file>`
   
    例如，在 Linux 機器上的 Tomcat 中：
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. 重新啟動您的應用程式伺服器。

## <a name="configure-the-agent"></a>設定代理程式
建立名為 `AI-Agent.xml` 的檔案，並將它放在代理程式 JAR 檔案所在的同一資料夾中。

設定 XML 檔案的內容。 編輯下列範例以包含或省略您要的功能。

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn enabled="true">

         <!-- capture logging via Log4j 1.2, Log4j2, and Logback, default is true -->
         <Logging enabled="true" />

         <!-- capture outgoing HTTP calls performed through Apache HttpClient, OkHttp,
              and java.net.HttpURLConnection, default is true -->
         <HTTP enabled="true" />

         <!-- capture JDBC queries, default is true -->
         <JDBC enabled="true" />

         <!-- capture Redis calls, default is true -->
         <Jedis enabled="true" />

         <!-- capture query plans for JDBC queries that exceed this value (MySQL, PostgreSQL),
              default is 10000 milliseconds -->
         <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>

      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

## <a name="additional-config-spring-boot"></a>附加配置（彈簧啟動）

`java -javaagent:/path/to/agent.jar -jar path/to/TestApp.jar`

對於 Azure 應用服務，執行以下操作：

* 選取 [設定] > [應用程式設定]
* 在 [應用程式設定] 之下，新增索引鍵值組︰

鍵：`JAVA_OPTS`值：`-javaagent:D:/home/site/wwwroot/applicationinsights-agent-2.5.0.jar`

有關最新版本的 JAVA 代理，請查看[此處](https://github.com/Microsoft/ApplicationInsights-Java/releases
)的版本。 

代理必須打包為專案中的資源，以便它最終位於 D：/home/網站/wwwroot/目錄中。 您可以通過訪問**開發工具** > **高級工具** > **調試主控台**並檢查網站目錄的內容來確認代理位於正確的應用服務目錄中。    

* 儲存設定並重新啟動您的應用程式。 （這些步驟僅適用于在 Windows 上運行的應用服務。

> [!NOTE]
> AI-Agent.xml 與代理程式 jar 檔案應在同一個資料夾中。 它們通常一起放在專案的 `/resources` 資料夾中。  

#### <a name="enable-w3c-distributed-tracing"></a>啟用 W3C 分散式追蹤

將下列程式碼新增至 AI-Agent.xml：

```xml
<Instrumentation>
   <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
   </BuiltIn>
</Instrumentation>
```

> [!NOTE]
> 回溯相容模式預設為啟用，enableW3CBackCompat 參數為選擇性，且應僅用於您想要將它關閉時。 

理想的狀況是，所有的服務已更新至支援 W3C 通訊協定的新版 SDK。 強烈建議儘快移至具備 W3C 支援的新版 SDK。

確定**[傳入](correlation.md#enable-w3c-distributed-tracing-support-for-java-apps)與傳出 (代理程式) 設定兩者**完全相同。

## <a name="view-the-data"></a>檢視資料
在"應用程式見解"資源中，聚合的遠端依賴項和方法執行時間將顯示在[性能磁貼下][metrics]。

若要搜尋相依性、例外狀況及方法報告的個別執行個體，請開啟[搜尋][diagnostic]。

[診斷相依性問題 - 深入了解](../../azure-monitor/app/asp-net-dependencies.md#diagnosis)。

## <a name="questions-problems"></a>有疑問嗎？ 有問題嗎？
* 沒有資料？ [設定防火牆例外狀況](../../azure-monitor/app/ip-addresses.md)
* [疑難排解 Java](java-troubleshoot.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md

---
title: JAVA web apps 效能監視-Azure 應用程式見解
description: 使用 Application Insights 延伸 Java 網站的效能和使用量監視。
ms.topic: conceptual
ms.date: 01/10/2019
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 299e9010b74c8363cacd1c20044d183dc1def6a6
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2020
ms.locfileid: "96601283"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>在 JAVA web 應用程式中監視相依性、攔截到例外狀況和方法執行時間

> [!IMPORTANT]
> 監視 JAVA 應用程式的建議方法是使用自動檢測，而不需要變更程式碼。 請遵循 [Application Insights JAVA 3.0 代理程式](./java-in-process-agent.md)的指導方針。

如果您已 [使用 APPLICATION INSIGHTS SDK 來檢測您的 JAVA web 應用程式][java]，您可以使用 JAVA 代理程式來取得更深入的見解，而不需要變更任何程式碼：

* **相依性** ：您的應用程式對其他元件呼叫的相關資料，包括：
  * 透過 Apache HttpClient、OkHttp 和發出的連 **出 HTTP 呼叫** `java.net.HttpURLConnection` 會被捕捉。
  * 系統會捕捉透過 Jedis 用戶端所提出的 **Redis 呼叫**。
  * **JDBC 查詢** -針對 MySQL 和于 postgresql，如果呼叫花費的時間超過10秒，則代理程式會報告查詢計劃。

* **應用程式記錄：** 使用 HTTP 要求和其他遙測來捕捉您的應用程式記錄並使其相互關聯
  * **Log4j 1。2**
  * **Log4j2**
  * **Logback**

* **更好的作業命名：** (用於入口網站中的要求匯總) 
  * 以 **春季** 為基礎 `@RequestMapping` 。
  * **JAX-RS** 為基礎 `@Path` 。 

若要使用 Java 代理程式，您要在伺服器上安裝它。 您必須使用 [Application Insights Java SDK][java] 檢測您的 Web 應用程式。 

## <a name="install-the-application-insights-agent-for-java"></a>安裝 Java 的 Application Insights 代理程式
1. 在執行 Java 伺服器的電腦上[下載代理程式](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest)。 請確定下載的 Java 代理程式版本與 Application Insights Java SDK 核心和網頁套件相同。
2. 編輯應用程式伺服器啟動腳本，並加入下列 JVM 引數：
   
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

## <a name="additional-config-spring-boot"></a>其他設定 (彈簧開機) 

`java -javaagent:/path/to/agent.jar -jar path/to/TestApp.jar`

針對 Azure App 服務，請執行下列動作：

* 選取 [設定] > [應用程式設定]
* 在 [應用程式設定] 之下，新增索引鍵值組︰

機碼： `JAVA_OPTS` 值： `-javaagent:D:/home/site/wwwroot/applicationinsights-agent-2.5.0.jar`

如需最新版本的 JAVA 代理程式，請參閱 [這裡](https://github.com/Microsoft/ApplicationInsights-Java/releases
)的版本。 

代理程式必須封裝為專案中的資源，使其最後位於 D：/home/site/wwwroot/目錄中。 您可以前往 [**開發工具**] 的 [  >  **Advanced tools**]  >  **偵錯主控台** 和檢查網站目錄的內容，確認您的代理程式位於正確的 App Service 目錄。    

* 儲存設定並重新啟動您的應用程式。  (這些步驟僅適用于在 Windows 上執行的應用程式服務。 ) 

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

確定 **[傳入](correlation.md#enable-w3c-distributed-tracing-support-for-java-apps)與傳出 (代理程式) 設定兩者** 完全相同。

## <a name="view-the-data"></a>檢視資料
在 Application Insights 資源中，匯總的遠端相依性和方法執行時間會出現 [在 [效能] 圖][metrics]格底下。

若要搜尋相依性、例外狀況及方法報告的個別執行個體，請開啟[搜尋][diagnostic]。

[診斷相依性問題 - 深入了解](./asp-net-dependencies.md#diagnosis)。

## <a name="questions-problems"></a>有任何問題嗎？ 有問題嗎？
* 沒有資料？ [設定防火牆例外狀況](./ip-addresses.md)
* [疑難排解 Java](java-troubleshoot.md)

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[apiexceptions]: ./api-custom-events-metrics.md#track-exception
[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../platform/metrics-charts.md


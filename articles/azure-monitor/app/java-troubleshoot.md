---
title: 疑難排解 Java Web 專案中的 Application Insights
description: 疑難排解指南 - 使用 Application Insights 監視即時的 Java 應用程式。
ms.topic: conceptual
ms.date: 03/14/2019
ms.custom: devx-track-java
ms.openlocfilehash: 8d11eaa76f2133b9fdc66ba4e6bc91e2cc38c51c
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93379531"
---
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java-sdk"></a>適用于 JAVA SDK 的 Application Insights 疑難排解和問與答

> [!IMPORTANT]
> 監視 JAVA 應用程式的建議方法是使用自動檢測，而不需要變更程式碼。 請遵循 [Application Insights JAVA 3.0 代理程式](./java-in-process-agent.md)的指導方針。

[Java 中的 Azure Application Insights][java] 疑問或問題？ 以下是一些秘訣。

## <a name="build-errors"></a>建置錯誤
**在 Eclipse 或 Intellij Idea 中，透過 Maven 或 Gradle 加入 Application Insights SDK 時，我收到建置或總和檢查碼驗證錯誤。**

* 如果相依性 `<version>` 元素使用具有萬用字元的模式 (例如 (Maven) `<version>[2.0,)</version>` 或 (Gradle) `version:'2.0.+'`)，請嘗試改為指定特定版本 (例如 `2.0.1`)。 請參閱最新版本的 [版本資訊](https://github.com/Microsoft/ApplicationInsights-Java/releases) 。

## <a name="no-data"></a>無資料
**我已成功加入 Application Insights 並執行我的應用程式，但在入口網站中從未看到資料。**

* 請稍等片刻，然後按一下 [重新整理]。 圖表會定期自行重新整理，但您也可以手動重新整理。 重新整理間隔取決於圖表的時間範圍。
* 檢查 ApplicationInsights.xml 檔案 (位於專案的 resources 資料夾) 中是否已定義檢測金鑰，或已將檢測金鑰設為環境變數。
* 確認 xml 檔案中沒有 `<DisableTelemetry>true</DisableTelemetry>` 節點。
* 在防火牆中，您可能必須開啟 TCP 連接埠 80 和 443，以允許連出流量送往 dc.services.visualstudio.com。 請參閱 [完整的防火牆例外狀況清單](./ip-addresses.md)
* 在 Microsoft Azure 開始面板中，查看服務狀態對應。 如果看到一些警示指示，請等待它們恢復 [正常]，然後關閉再重新開啟 Application Insights 應用程式刀鋒視窗。
* [Turn on logging](#debug-data-from-the-sdk)藉由在專案) 中的 [資源] 資料夾中，于 [資源] 資料夾內的 [ `<SDKLogger />` ApplicationInsights.xml 檔 (的根節點下加入專案，然後檢查前面加上 AI： INFO/警告/錯誤的專案，以開啟記錄。 
* 藉由查看主控台的輸出訊息「已成功找到組態檔」陳述式，確定 Java SDK 已成功載入正確的 ApplicationInsights.xml 檔案。
* 如果找不到組態檔，請檢查輸出訊息以查看在何處搜尋組態檔，並確定 ApplicationInsights.xml 位在這些搜尋位置中的其中一個位置。 根據經驗法則，您可以將組態檔置於 Application Insights SDK JAR 附近。 例如：在 Tomcat 中，這可能表示 WEB-INF/classes 資料夾。 在開發過程中，您可以將 ApplicationInsights.xml 放在您 Web 專案的 resources 資料夾中。
* 另外也請查看 [GitHub 問題頁面](https://github.com/Microsoft/ApplicationInsights-Java/issues) \(英文\) 以了解 SDK 的已知問題。
* 請務必使用相同版本的 Application Insights 核心、Web、代理程式和記錄附加器，以避免任何版本衝突問題。

#### <a name="i-used-to-see-data-but-it-has-stopped"></a>我曾經看到資料，但是已停止
* 您有達到資料點的每月配額嗎？ 開啟 [設定/配額和定價] 以找出。若是如此，您可以升級您的方案，或支付額外容量。 請參閱 [定價配置](https://azure.microsoft.com/pricing/details/application-insights/)。
* 您最近是否升級了 SDK？ 請確定專案目錄中只有「唯一」的 SDK jar。 不應該有兩個不同版本的 SDK 存在。
* 正在尋找適當的 AI 資源嗎？ 請比對應用程式的 iKey 與您預期遙測的資源。 它們應該相同。

#### <a name="i-dont-see-all-the-data-im-expecting"></a>我並沒有看到預期的所有資料
* 開啟 [使用量和估計成本] 頁面，並檢查[取樣](./sampling.md)是否正在運作。  (100% 傳輸表示取樣不在運作中。 ) Application Insights 服務可以設定為只接受來自您應用程式的一小部分遙測。 這有助於您維持在每月的遙測配額內。
* 您是否已開啟 SDK 取樣？ 如果是，則應該會針對所有適用的類型以指定的速率進行資料取樣。
* 您正在執行較舊版本的 Java SDK？ 從 2.0.1 版開始，我們引進容錯機制來處理間歇性的網路和後端失敗，以及本機磁碟上的資料持續性。
* 是否因為過多的遙測而遭到節流？ 如果您開啟 INFO 記錄，將會看到記錄訊息「應用程式已節流」。 我們目前的限制為每秒 32,000 個遙測項目。

### <a name="java-agent-cannot-capture-dependency-data"></a>Java 代理程式無法擷取相依性資料
* 您是否已依下列的[設定 Java 代理程式](java-agent.md)設定 Java 代理程式？
* 請確定 Java 代理程式 jar 與 AI-Agent.xml 檔案放在相同的資料夾中。
* 請確定您嘗試要自動收集的相依性支援自動收集。 目前我們只支援 MySQL、MsSQL、Oracle DB 和 Azure Cache for Redis 相依性集合。

## <a name="no-usage-data"></a>沒有使用狀況資料
**我看到要求和回應時間的相關資料，但沒有看到頁面檢視、瀏覽器或使用者資料的相關資料。**

您已成功設定應用程式從伺服器傳送遙測。 現在，您的下一步是[設定網頁，以從網頁瀏覽器傳送遙測][usage]。

或者，如果您的用戶端是[手機或其他裝置][platforms]中的應用程式，則可以從該處傳送遙測。

使用相同的檢測機碼來設定用戶端和伺服器遙測。 資料會出現在相同的 Application Insights 資源中，而且您可以相互關聯來自用戶端和伺服器的事件。


## <a name="disabling-telemetry"></a>停用遙測
**如何停用遙測收集？**

在程式碼中：

```Java

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);
```

**Or**

更新 ApplicationInsights.xml (位於專案的 resources 資料夾)。 在根節點下加入下列程式碼：

```XML

    <DisableTelemetry>true</DisableTelemetry>
```

如果使用 XML 方法，則必須在變更值時重新啟動應用程式。

## <a name="changing-the-target"></a>變更目標
**如何變更我的專案將資料傳送到哪一個 Azure 資源？**

* [取得新資源的檢測金鑰。][java]
* 如果您已使用 Azure Toolkit for Eclipse 將 Application Insights 新增到您的專案，請在 Web 專案上按一下滑鼠右鍵，依序選取 [Azure] 和 [設定 Application Insights]，然後變更金鑰。
* 如果您已將檢測金鑰設定為環境變數，請使用新的 iKey 更新環境變數的值。
* 否則，請更新專案之 resources 資料夾的 ApplicationInsights.xml 中的機碼。

## <a name="debug-data-from-the-sdk"></a>從 SDK 進行資料偵錯

**如何找出 SDK 正在做什麼？**

若要取得有關 API 中所進行作業的詳細資訊，請在 ApplicationInsights.xml 組態檔的根節點底下新增 `<SDKLogger/>`。

### <a name="applicationinsightsxml"></a>ApplicationInsights.xml

您也可以指示記錄器輸出至檔案：

```XML
  <SDKLogger type="FILE"><!-- or "CONSOLE" to print to stderr -->
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AISDK</BaseFolderPath>
</SDKLogger>
```

### <a name="spring-boot-starter"></a>春季開機 Starter

若要使用 Application Insights 春季 Boot Starter 來啟用具有春季 Boot 應用程式的 SDK 記錄，請將下列內容新增至檔案 `application.properties` ：

```yaml
azure.application-insights.logger.type=file
azure.application-insights.logger.base-folder-path=C:/agent/AISDK
azure.application-insights.logger.level=trace
```

或列印到標準錯誤：

```yaml
azure.application-insights.logger.type=console
azure.application-insights.logger.level=trace
```

### <a name="java-agent"></a>Java 代理程式

若要啟用 JVM 代理程式記錄，請更新 [AI-Agent.xml](java-agent.md)檔案：

```xml
<AgentLogger type="FILE"><!-- or "CONSOLE" to print to stderr -->
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AIAGENT</BaseFolderPath>
</AgentLogger>
```

### <a name="java-command-line-properties"></a>JAVA 命令列屬性
_因為版本2.4。0_

若要使用命令列選項來啟用記錄，而不需要變更設定檔：

```
java -Dapplicationinsights.logger.file.level=trace -Dapplicationinsights.logger.file.uniquePrefix=AI -Dapplicationinsights.logger.baseFolderPath="C:/my/log/dir" -jar MyApp.jar
```

或列印到標準錯誤：

```
java -Dapplicationinsights.logger.console.level=trace -jar MyApp.jar
```

## <a name="the-azure-start-screen"></a>Azure 開始畫面
**我正在查看 [Azure 入口網站](https://portal.azure.com)。地圖會告訴我我的應用程式有什麼關係？**

否，它會顯示世界各地 Azure 伺服器的健全狀況。

*從 Azure 開始面板 (主畫面) 中，如何找到我應用程式的相關資料？*

假設您已[設定 Application Insights 的應用程式][java]，請按一下 [瀏覽]，選取 [Application Insights]，然後選取您為應用程式建立的應用程式資源。 日後若要更快速地從該處開始，您可以將應用程式釘選至開始面板。

## <a name="intranet-servers"></a>內部網路伺服器
**可以在我的內部網路上監視伺服器嗎？**

是，前提是您的伺服器可以透過公用網際網路將遙測傳送至 Application Insights 入口網站。

在防火牆中，您可能必須開啟 TCP 連接埠 80 和 443，以允許連出流量送往 dc.services.visualstudio.com 和 f5.services.visualstudio.com。

## <a name="data-retention"></a>資料保留
**資料保留在入口網站多久的時間？ 是否安全？**

請參閱[資料保留和隱私權][data]。

## <a name="debug-logging"></a>偵錯記錄
Application insights 會使用 `org.apache.http`。 這會重新配置在 Application Insights 核心 Jar 內的 `com.microsoft.applicationinsights.core.dependencies.http` 命名空間底下。 這可讓 Application Insights 處理相同 `org.apache.http` 的不同版本都存在於一個核心程式碼基底中的情況。

>[!NOTE]
>如果您針對應用程式中的所有命名空間啟用 DEBUG 層級的記錄功能，它將適用於所有執行中的模組，包括已重新命名為 `com.microsoft.applicationinsights.core.dependencies.http` 的 `org.apache.http`。 Application Insights 將無法針對這些呼叫套用篩選，因為正在由 Apache 程式庫發出記錄呼叫。 DEBUG 層級記錄會產生相當大量的記錄資料，因此不建議用於即時生產環境執行個體。


## <a name="next-steps"></a>後續步驟
**設定 Java 伺服器應用程式的 Application Insights。我還可以做什麼？**

* [監視網頁可用性][availability]
* [監視網頁使用狀況][usage]
* [追蹤使用狀況並診斷裝置應用程式中的問題][platforms]
* [撰寫程式碼以追蹤應用程式使用狀況][track]
* [擷取診斷記錄][javalogs]

## <a name="get-help"></a>取得說明
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* [在 GitHub 上提出問題](https://github.com/Microsoft/ApplicationInsights-Java/issues)

<!--Link references-->

[availability]: ./monitor-web-app-availability.md
[data]: ./data-retention-privacy.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[platforms]: ./platforms.md
[track]: ./api-custom-events-metrics.md
[usage]: javascript.md


---
title: Azure Application Insights 中的資料保留和儲存 | Microsoft Docs
description: 保留和隱私權原則聲明
ms.topic: conceptual
ms.date: 09/29/2019
ms.openlocfilehash: 30878eecf795c85713b9f09b8325b326416022b8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79275993"
---
# <a name="data-collection-retention-and-storage-in-application-insights"></a>Application Insights 中的資料收集、保留和儲存

當您在應用程式中安裝 [Azure Application Insights][start] SDK 時，它會將您應用程式的相關遙測傳送到雲端。 當然，負責任的開發人員會想要確切得知所傳送的資料為何、資料的後續情況，以及如何控制資料。 特別是，是否可能傳送敏感資料？資料儲存於何處？其安全性為何？ 

首先提供簡短的答案：

* 「現成可用」的標準遙測模組不太可能將敏感資料傳送至服務。 遙測會考量負載、效能和使用度量、例外狀況報告和其他診斷資料。 診斷報告中顯示的主要使用者資料的 URL；但是，您的應用程式在任何情況下都應該不會將敏感資料以純文字形式放在 URL 中。
* 您可以撰寫會傳送其他自訂遙測的程式碼，以利診斷與監視使用情形。 （此擴充性是 Application Insights 的絕佳功能）。您可以不小心撰寫此程式碼，使其包含個人和其他敏感性資料。 如果您的應用程式可以使用這類資料，您應該將完整的審查流程套用至您撰寫的所有程式碼。
* 在開發及測試您的應用程式時，可以輕易地檢查由 SDK 傳送的內容。 資料會出現在 IDE 和瀏覽器的偵錯輸出視窗中。 
* 資料會保存在美國或歐洲的 [Microsoft Azure](https://azure.com) 伺服器中。 （但是您的應用程式可以在任何地方執行）。Azure 具備[強大的安全性程式，並符合各種合規性標準](https://azure.microsoft.com/support/trust-center/)。 只有您和您指定的小組可以存取您的資料。 Microsoft 工作人員只有在您知情的特定有限情況下，才具有其限定存取權。 它會在傳輸中和待用時加密。
*   請檢查所收集的資料，因為這可能包含某些情況下允許的資料，而不是其他情況。  裝置名稱就是一個很好的例子。 伺服器的裝置名稱不會影響隱私權，而且很有用，但來自電話或膝上型電腦的裝置名稱可能會影響隱私權，而且較不實用。 SDK 主要是針對目標伺服器而開發，預設會收集裝置名稱，而這可能需要在一般事件和例外狀況中覆寫。

本文的其餘部分將詳細說明上述問題的答案。 本文設計為自助式，以便您可以將其顯示給不屬於您直屬小組的同事。

## <a name="what-is-application-insights"></a>什麼是 Application Insights？
[Azure Application Insights][start] 是 Microsoft 所提供的一項服務，可協助您改進即時應用程式的效能和可用性。 它會在您的應用程式執行時全程加以監視，包括測試期間，以及您加以發佈或部署之後。 Application Insights 會建立圖表和資料表為您顯示多種資訊，例如，您在一天中的哪些時間有最多使用者、應用程式的回應性如何，以及它所依存的任何外部服務是否順暢地為其提供服務。 如果有當機、失敗或效能問題，您可以搜尋詳細的遙測資料，以診斷原因。 此外，如果應用程式的可用性和效能有任何變更，服務將會傳送電子郵件給您。

若要取得這項功能，您必須在應用程式中安裝 Application Insights SDK，這會成為其程式碼的一部分。 當您的應用程式執行時，SDK 會監視其作業，並將遙測傳送至 Application Insights 服務。 這是由 [Microsoft Azure](https://azure.com)裝載的雲端服務。 （但 Application Insights 適用于任何應用程式，而不只是裝載于 Azure 中的應用程式）。

Application Insights 服務會儲存並分析遙測。 若要查看分析或搜尋已儲存的遙測，您可以登入您的 Azure 帳戶，並開啟您的 Application Insights 資源。 您也可以與小組的其他成員或指定的 Azure 訂閱者共用資料的存取權。

您可以從 Application Insights 服務匯出資料，例如，匯出至資料庫或外部工具。 您必須為每項工具提供您從服務取得的特殊金鑰。 如有必要，可以撤銷此金鑰。 

Application Insights SDK 可用於多種應用程式類型：裝載於您自己的 Java EE 或 ASP.NET 伺服器或是 Azure 中的 Web 服務；Web 用戶端 - 也就是在網頁上執行的程式碼；桌面應用程式和服務；裝置應用程式，例如 Windows Phone、iOS 和 Android。 它們都會將遙測傳送至相同的服務。

## <a name="what-data-does-it-collect"></a>它會收集哪些資料？
來源資料共有三種：

* SDK - 可在[開發](../../azure-monitor/app/asp-net.md)或[執行階段](../../azure-monitor/app/monitor-performance-live-website-now.md)與您的應用程式整合。 不同的應用程式類型有不同的 SDK。 此外，還有[網頁的 SDK](../../azure-monitor/app/javascript.md)，會連同頁面載入至使用者的瀏覽器。
  
  * 每個 SDK 各有多種 [模組](../../azure-monitor/app/configuration-with-applicationinsights-config.md)，可使用不同的技術來收集不同類型的遙測。
  * 如果您在開發環境中安裝 SDK，則您可以使用其 API，在標準模組以外傳送您自己的遙測。 此自訂遙測可包含您想要傳送的任何資料。
* 在某些網頁伺服器中，也有與應用程式一起執行，並傳送關於 CPU、記憶體和網路佔用量之遙測的代理程式。 例如，Azure VM、Docker 主機和 [Java EE 伺服器](../../azure-monitor/app/java-agent.md) 都可能有這類代理程式。
* [可用性測試](../../azure-monitor/app/monitor-web-app-availability.md) 是 Microsoft 所執行程序，會定期將要求傳送至您的 Web 應用程式。 結果會傳送至 Application Insights 服務。

### <a name="what-kinds-of-data-are-collected"></a>會收集哪些類型的資料？
主要類別如下：

* [Web 伺服器遙測](../../azure-monitor/app/asp-net.md) - HTTP 要求。  URI、處理要求所花費的時間、回應碼、用戶端 IP 位址。 `Session id`.
* [網頁](../../azure-monitor/app/javascript.md) - 頁面、使用者和工作階段計數。 頁面載入時間。 例外狀況。 Ajax 呼叫。
* 效能計數器 - 記憶體、CPU、IO、網路佔用量。
* 用戶端和伺服器內容 - OS、地區設定、裝置類型、瀏覽器和螢幕解析度。
* [例外](../../azure-monitor/app/asp-net-exceptions.md)狀況和損**stack dumps**毀-堆疊`build id`傾印、、CPU 類型。 
* [相依性](../../azure-monitor/app/asp-net-dependencies.md) - 對外部服務的呼叫，例如 REST、SQL、AJAX。 URI 或連接字串、持續時間、成功、命令。
* [可用性測試](../../azure-monitor/app/monitor-web-app-availability.md) - 測試的持續時間、步驟、回應。
* [追蹤記錄](../../azure-monitor/app/asp-net-trace-logs.md)和[自訂遙測](../../azure-monitor/app/api-custom-events-metrics.md) - **任何您以程式碼撰寫到記錄或遙測中的項目**。

[詳細資訊](#data-sent-by-application-insights)。

## <a name="how-can-i-verify-whats-being-collected"></a>如何確認收集到什麼？
如果您使用 Visual Studio 開發應用程式，請在偵錯模式 (F5) 中執行應用程式。 遙測會出現在 [輸出] 視窗中。 在這裡，您可以將其複製並格式化為 JSON，以便進行檢查。 

![](./media/data-retention-privacy/06-vs.png)

在 [診斷] 視窗中還有更容易閱讀的檢視。

針對網頁，請開啟瀏覽器的偵錯視窗。

![按 F12 鍵，然後開啟 [網路] 索引標籤。](./media/data-retention-privacy/08-browser.png)

### <a name="can-i-write-code-to-filter-the-telemetry-before-it-is-sent"></a>是否可以撰寫程式碼來篩選遙測，然後才傳送出去?
這可以藉由撰寫 [遙測處理器外掛程式](../../azure-monitor/app/api-filtering-sampling.md)來達成。

## <a name="how-long-is-the-data-kept"></a>資料保留多久？
原始資料點（也就是您可以在 Analytics 中查詢並在搜尋中檢查的專案）會保留最多730天。 您可以選取30、60、90、120、180、270、365、550或730天的[保留期間](https://docs.microsoft.com/azure/azure-monitor/app/pricing#change-the-data-retention-period)。 如果您需要保留超過730天的資料，您可以使用[連續匯出](../../azure-monitor/app/export-telemetry.md)，在資料內嵌期間將它複製到儲存體帳戶。 

保留時間超過90天的資料會產生額外費用。 若要深入瞭解 Application Insights 定價，請到[Azure 監視器定價頁面](https://azure.microsoft.com/pricing/details/monitor/)。

彙總的資料 (也就是您在計量瀏覽器中看到的計數、平均和其他統計資料) 在 1 分鐘的資料粒度中保存 90 天。

「[偵錯工具快照](../../azure-monitor/app/snapshot-debugger.md)集」會儲存15天。 此保留原則會就個別的應用程式而設定。 如果您需要增加此值，您可以在 Azure 入口網站中建立支援案例，以提出增加的要求。

## <a name="who-can-access-the-data"></a>誰可以存取資料？
您和您的小組成員 (如果您有組織帳戶) 可以看到資料。 

它可以由您和您的小組成員匯出，也可以複製到其他位置，並傳遞給其他人員。

#### <a name="what-does-microsoft-do-with-the-information-my-app-sends-to-application-insights"></a>Microsoft 如何處理我的應用程式傳送至 Application Insights 的資訊？
Microsoft 只會使用這項資料，以將服務提供給您。

## <a name="where-is-the-data-held"></a>資料存放在哪裡？
* 建立新的 Application Insights 時，您可以選取位置。 請[在這裡](https://azure.microsoft.com/global-infrastructure/services/?products=all)深入瞭解每個區域的 Application Insights 可用性。

#### <a name="does-that-mean-my-app-has-to-be-hosted-in-the-usa-europe-or-southeast-asia"></a>這是否表示我的應用程式必須裝載在美國、歐洲或東南亞？
* 否。 您的應用程式可以在任何地方執行，在您自己的內部部署主機上或雲端中皆可。

## <a name="how-secure-is-my-data"></a>我的資料有多安全？
Application Insights 是一項 Azure 服務。 如需安全性原則的相關說明，請參閱[ Azure 安全性、隱私權及法規遵循白皮書](https://go.microsoft.com/fwlink/?linkid=392408)。

資料會儲存在 Microsoft Azure 伺服器中。 如需 Azure 入口網站中的帳戶，請參閱[Azure 安全性、隱私權和合規性檔](https://go.microsoft.com/fwlink/?linkid=392408)中的帳戶限制。

Microsoft 人員對您的資料存取會受到限制。 我們只有在獲得您的許可及為了支援您使用 Application Insights 而有必要時，才會存取您的資料。 

跨所有客戶應用程式 (例如資料速率和平均追蹤大小) 的彙總資料用於改善 Application Insights。

#### <a name="could-someone-elses-telemetry-interfere-with-my-application-insights-data"></a>其他人的遙測是否會干擾我的 Application Insights 資料？
他們可以使用檢測金鑰，將額外的遙測傳送至您的帳戶，而檢測金鑰位於您網頁的程式碼中。 有足夠的額外資料，您的計量將不會正確地代表您應用程式的效能和使用方式。

如果您與其他專案共用程式碼，請務必移除您的檢測金鑰。

## <a name="is-the-data-encrypted"></a>資料是否會加密？
所有資料都會在待用時加密，而且會在資料中心之間移動。

#### <a name="is-the-data-encrypted-in-transit-from-my-application-to-application-insights-servers"></a>將資料從我的應用程式傳輸到 Application Insights 伺服器時是否進行加密？
是，我們使用 HTTPs 從幾乎所有的 Sdk （包括網頁伺服器、裝置和 HTTPS 網頁）將資料傳送至入口網站。 

## <a name="does-the-sdk-create-temporary-local-storage"></a>SDK 是否會建立暫存本機儲存體？

是，在無法連線到端點時，特定遙測通道會將資料保存在本機。 請檢閱下列內容，以了解哪些架構和遙測通道會受到影響。

使用本機儲存體的遙測通道會在 TEMP 或 APPDATA 目錄中建立暫存檔案，這些檔案僅限於執行您應用程式的特定帳戶。 當端點暫時無法使用或已達到節流限制時，就可能發生此狀況。 此問題解決後，遙測通道就會繼續傳送所有新的和保存的資料。

此保存的資料不會在本機加密。 如果這是問題，請檢查資料並限制私用資料的收集。 （如需詳細資訊，請參閱[如何匯出及刪除私人資料](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data#how-to-export-and-delete-private-data)）。

如果客戶需要設定具有特定安全性需求的此目錄，可以針對每個架構進行設定。 請確定執行應用程式的程序具有此目錄的寫入權限，但也請確定此目錄會受到保護，以避免非預期的使用者讀取遙測資料。

### <a name="java"></a>Java

`C:\Users\username\AppData\Local\Temp` 用來保存資料。 此位置不可設定從 config 目錄設定，且存取此資料夾的權限僅限定於具有必要認證的特定使用者。 （如需詳細資訊，請參閱[執行](https://github.com/Microsoft/ApplicationInsights-Java/blob/40809cb6857231e572309a5901e1227305c27c1a/core/src/main/java/com/microsoft/applicationinsights/internal/util/LocalFileSystemUtils.java#L48-L72)）。

###  <a name="net"></a>.Net

根據預設，`ServerTelemetryChannel` 會使用現行使用者的本機應用程式資料的資料夾 `%localAppData%\Microsoft\ApplicationInsights` 或暫存資料夾 `%TMP%`。 (請參閱此處的[實作](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84))。


透過組態檔：
```xml
<TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel,   Microsoft.AI.ServerTelemetryChannel">
    <StorageFolder>D:\NewTestFolder</StorageFolder>
</TelemetryChannel>
```

透過程式碼：

- 從組態檔中移除 ServerTelemetryChannel
- 將下列程式碼片段新增至您的設定：
  ```csharp
  ServerTelemetryChannel channel = new ServerTelemetryChannel();
  channel.StorageFolder = @"D:\NewTestFolder";
  channel.Initialize(TelemetryConfiguration.Active);
  TelemetryConfiguration.Active.TelemetryChannel = channel;
  ```

### <a name="netcore"></a>NetCore

根據預設，`ServerTelemetryChannel` 會使用現行使用者的本機應用程式資料的資料夾 `%localAppData%\Microsoft\ApplicationInsights` 或暫存資料夾 `%TMP%`。 （請參閱這裡的[實](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84)作為）。在 Linux 環境中，除非指定了儲存體資料夾，否則本機儲存體將會停用。

下列程式碼片段說明如何在 `Startup.cs` 類別的 `ConfigureServices()` 方法中設定 `ServerTelemetryChannel.StorageFolder`：

```csharp
services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
```

（如需詳細資訊，請參閱[AspNetCore Custom Configuration](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration)）。

### <a name="nodejs"></a>Node.js

依預設會使用 `%TEMP%/appInsights-node{INSTRUMENTATION KEY}` 來保存資料。 存取此資料夾的權限僅限定於目前的使用者和系統管理員。 (請參閱此處的[實作](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Sender.ts))。

您可以在 [Sender.ts](https://github.com/Microsoft/ApplicationInsights-node.js/blob/7a1ecb91da5ea0febf5ceab13d6a4bf01a63933d/Library/Sender.ts#L384) 中變更靜態變數 `Sender.TEMPDIR_PREFIX` 的執行階段值，以覆寫資料夾前置詞 `appInsights-node`。

### <a name="javascript-browser"></a>JavaScript （瀏覽器）

[HTML5 會話儲存體](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage)用來保存資料。 會使用兩個不同的`AI_buffer`緩衝區`AI_sent_buffer`：和。 已批次處理並等候傳送的遙測資料會儲存在中`AI_buffer`。 剛傳送的遙測資料會放入`AI_sent_buffer`中，直到內嵌伺服器回應已成功接收為止。 成功收到遙測時，就會從所有緩衝區中移除它。 在暫時性失敗（例如，使用者遺失網路連線）上，遙測會一直保持`AI_buffer`在狀態，直到成功收到遙測，或讓伺服器回應遙測無效（例如，不正確的架構或太舊）。

藉由將設定[`enableSessionStorageBuffer`](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/legacy/JavaScript/JavaScriptSDK.Interfaces/IConfig.ts#L31)為， `false`即可停用遙測緩衝區。 當會話儲存體關閉時，會改為使用本機陣列做為持續性儲存體。 由於 JavaScript SDK 會在用戶端裝置上執行，因此使用者可以透過其瀏覽器的開發人員工具存取此儲存位置。

### <a name="opencensus-python"></a>OpenCensus Python

根據預設，OpenCensus Python SDK 會使用目前的`%username%/.opencensus/.azure/`使用者資料夾。 存取此資料夾的權限僅限定於目前的使用者和系統管理員。 （請參閱這裡的[實](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/storage.py)作為）。具有保存資料的資料夾將會在產生遙測的 Python 檔案之後命名。

您可以藉由在您所使用之匯出工具的函`storage_path`式中傳入參數，來變更儲存體檔案的位置。

```python
AzureLogHandler(
  connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000',
  storage_path='<your-path-here>',
)
```

## <a name="how-do-i-send-data-to-application-insights-using-tls-12"></a>如何使用 TLS 1.2 將資料傳送至 Application Insights？

為了確保傳送至 Application Insights 端點時的資料安全性，我們強烈建議客戶至少使用傳輸層安全性 (TLS) 1.2 來設定他們的應用程式。 我們已發現較舊版本的 TLS/安全通訊端層 (SSL) 較易受到攻擊，而且在其目前的運作中仍允許回溯相容性，因此並**不建議使用**這些版本，很快地，業界也會捨棄這些舊版通訊協定的支援。 

[PCI 安全性標準委員會](https://www.pcisecuritystandards.org/)已設定[2018 年6月30日的期限](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf)，以停用舊版的 TLS/SSL，並升級為更安全的通訊協定。 當 Azure 捨棄舊版的支援後，如果您的應用程式/用戶端無法透過 TLS 1.2 (至少) 進行通訊，您就無法將資料傳送至 Application Insights。 用來測試及驗證您應用程式 TLS 支援的方法，會因為作業系統/平台及應用程式所用的語言/架構不同而有所差異。

除非必要，否則我們不建議您將應用程式明確設定為只使用 TLS 1.2，因為這可能會中斷平台層級的安全性功能，讓您可以自動偵測並利用較新的安全通訊協定（例如 TLS 1.3）。 我們建議您對應用程式的程式碼執行全面稽核，以檢查特定 TLS/SSL 版本的硬式編碼。

### <a name="platformlanguage-specific-guidance"></a>平台/語言的特定指引

|平台/語言 | 支援 | 相關資訊 |
| --- | --- | --- |
| Azure App Service  | 支援，可能需要設定。 | 已在 2018 年 4 月宣告支援。 請參閱公告以了解[設定的詳細資訊](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/)。  |
| Azure 函式應用程式 | 支援，可能需要設定。 | 已在 2018 年 4 月宣告支援。 請參閱公告以了解[設定的詳細資訊](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/)。 |
|.NET | 支援，設定會因版本不同而有所差異。 | 如需 .NET 4.7 和舊版的詳細設定資訊，請參閱[這些指示](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12)。  |
|狀態監視器 | 支援，需要設定 | 狀態監視器依賴[OS](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) + 設定[.net](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12)設定來支援 TLS 1.2。
|Node.js |  支援，可能需要對 v10.5.0 進行設定。 | 針對任何應用程式特定的設定，請使用[官方的 NODE.JS TLS/SSL 檔](https://nodejs.org/api/tls.html)。 |
|Java | 支援，[JDK 6 更新 121](https://www.oracle.com/technetwork/java/javase/overview-156328.html#R160_121) 和 [JDK 7](https://www.oracle.com/technetwork/java/javase/7u131-relnotes-3338543.html) 中已新增 TLS 1.2 的 JDK 支援。 | JDK 8 會使用[預設的 TLS 1.2](https://blogs.oracle.com/java-platform-group/jdk-8-will-use-tls-12-as-default)。  |
|Linux | Linux 發行版本通常會依賴 [OpenSSL](https://www.openssl.org) 來取得 TLS 1.2 支援。  | 請檢查 [OpenSSL 變更記錄](https://www.openssl.org/news/changelog.html)來確認支援的 OpenSSL 版本。|
| Windows 8.0 - 10 | 支援，而且已預設為啟用。 | 請確認您仍在使用[預設設定](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings)。  |
| Windows Server 2012 - 2016 | 支援，而且已預設為啟用。 | 確認您仍在使用[預設設定](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 和 Windows Server 2008 R2 SP1 | 支援，但預設為不啟用。 | 請參閱[傳輸層安全性 (TLS) 登錄設定](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings)頁面，了解詳細的啟用方式。  |
| Windows Server 2008 SP2 | TLS 1.2 支援需要更新。 | 請在 Windows Server 2008 SP2 中參閱[新增 TLS 1.2 支援的更新](https://support.microsoft.com/help/4019276/update-to-add-support-for-tls-1-1-and-tls-1-2-in-windows-server-2008-s)。 |
|Windows Vista | 不支援。 | N/A

### <a name="check-what-version-of-openssl-your-linux-distribution-is-running"></a>請檢查您 Linux 發行版本所執行的 OpenSSL 版本

若要檢查您已安裝的 OpenSSL 是哪個版本，請開啟終端機並執行：

```terminal
openssl version -a
```

### <a name="run-a-test-tls-12-transaction-on-linux"></a>在 Linux 上執行用於測試的 TLS 1.2 交易

若要執行初步測試，以查看您的 Linux 系統是否可以透過 TLS 1.2 進行通訊。請開啟終端機並執行：

```terminal
openssl s_client -connect bing.com:443 -tls1_2
```

## <a name="personal-data-stored-in-application-insights"></a>儲存在 Application Insights 中的個人資料

我們的 [Application Insights 個人資料文章](../../azure-monitor/platform/personal-data-mgmt.md)會深入討論此議題。

#### <a name="can-my-users-turn-off-application-insights"></a>我的使用者是否可以關閉 Application Insights？
無法直接進行。 我們不提供您的使用者可以操作來關閉 Application Insights 的參數。

不過，您可以在應用程式中實作這類功能。 所有 SDK 都包括關閉遙測收集的 API 設定。 

## <a name="data-sent-by-application-insights"></a>Application Insights 所傳送的資料
不同的平台會有不同的 SDK，而且有數個可安裝的元件。 （請參閱[Application Insights-總覽][start]）。每個元件都會傳送不同的資料。

#### <a name="classes-of-data-sent-in-different-scenarios"></a>不同情況下所傳送資料的類別

| 您的動作 | 收集的資料類別 (請參閱下一個資料表) |
| --- | --- |
| [將 Application Insights SDK 新增至 .NET Web 專案][greenbrown] |ServerContext<br/>推斷<br/>效能計數器<br/>Requests<br/>**例外狀況**<br/>工作階段<br/>users |
| [在 IIS 上安裝狀態監視器][redfield] |相依性<br/>ServerContext<br/>推斷<br/>效能計數器 |
| [將 Application Insights SDK 新增至 Java Web 應用程式][java] |ServerContext<br/>推斷<br/>要求<br/>工作階段<br/>users |
| [將 JavaScript SDK 新增至網頁][client] |ClientContext  <br/>推斷<br/>頁面<br/>ClientPerf<br/>Ajax |
| [定義預設屬性][apiproperties] |**屬性** |
| [呼叫 TrackMetric][api] |數字值<br/>**屬性** |
| [呼叫 Track*][api] |事件名稱<br/>**屬性** |
| [呼叫 TrackException][api] |**例外狀況**<br/>堆疊傾印<br/>**屬性** |
| SDK 無法收集資料。 例如： <br/> - 無法存取效能計數器<br/> - 遙測初始設定式中發生例外狀況 |SDK 診斷 |

如需[適用於其他平台的 SDK][platforms]，請參閱其文件。

#### <a name="the-classes-of-collected-data"></a>所收集資料的類別

| 收集的資料類別 | 包含 (不是詳盡的清單) |
| --- | --- |
| **屬性** |**任何資料 - 取決於您的程式碼** |
| DeviceContext |`Id`，IP，地區設定，裝置型號，網路，網路類型，OEM 名稱，螢幕解析度，角色實例，角色名稱，裝置類型 |
| ClientContext  |作業系統、地區設定、語言、網路、視窗解析度 |
| 工作階段 |`session id` |
| ServerContext |電腦名稱、地區設定、作業系統、裝置、使用者工作階段、使用者內容、作業 |
| 推斷 |從 IP 位址的地區位置、時間戳記、作業系統、瀏覽器 |
| 計量 |計量名稱和值 |
| 事件 |事件名稱和值 |
| PageViews |URL 和頁面名稱或螢幕名稱 |
| 用戶端效能 |URL/頁面名稱、瀏覽器載入時間 |
| Ajax |從網頁向伺服器發出的 HTTP 呼叫 |
| Requests |URL、持續時間、回應碼 |
| 相依性 |Type （SQL，HTTP，...），連接字串，或 URI，同步/非同步，持續時間，成功，SQL 語句（含狀態監視器） |
| **例外狀況** |類型、**訊息**、呼叫堆疊、原始程式檔、行號、`thread id` |
| 損毀 |`Process id`, `parent process id`, `crash thread id`;應用程式修補`id`，，組建; 例外狀況類型、位址、原因;模糊符號和暫存器、二進位開始和結束位址、二進位檔名稱和路徑、cpu 類型 |
| 追蹤 |**訊息** 和嚴重性層級 |
| 效能計數器 |處理器時間、可用記憶體、要求率、例外狀況率、處理序私用位元組、IO 率、要求持續期間、要求佇列長度 |
| 可用性 |Web 測試回應程式碼、每個測試步驟的持續時間、測試名稱、時間戳記、成功、回應時間、測試位置 |
| SDK 診斷 |追蹤訊息或例外狀況 |

您可以[編輯 ApplicationInsights.config 來關閉某些資料][config]

> [!NOTE]
> 用戶端 IP 會用來推斷地理位置，但預設不會再儲存 IP 資料，而且所有的零會寫入相關聯的欄位。 若要深入了解個人資料的處理，建議您閱讀這篇[文章](../../azure-monitor/platform/personal-data-mgmt.md#application-data)。 如果您需要儲存 IP 位址資料，我們的[ip 位址集合文章](https://docs.microsoft.com/azure/azure-monitor/app/ip-collection)會引導您完成選項。

## <a name="credits"></a>學分
本產品包含 MaxMind 所建立的 GeoLite2 資料（可[https://www.maxmind.com](https://www.maxmind.com)從取得）。



<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiproperties]: ../../azure-monitor/app/api-custom-events-metrics.md#properties
[client]: ../../azure-monitor/app/javascript.md
[config]: ../../azure-monitor/app/configuration-with-applicationinsights-config.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[java]: ../../azure-monitor/app/java-get-started.md
[platforms]: ../../azure-monitor/app/platforms.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md

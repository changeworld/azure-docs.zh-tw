---
title: ApplicationInsights 的版本資訊-Microsoft.applicationinsights.snapshotcollector NuGet 套件-Application Insights
description: Application Insights 快照偵錯工具所使用的 ApplicationInsights Microsoft.applicationinsights.snapshotcollector NuGet 套件版本資訊。
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 11/10/2020
ms.openlocfilehash: 73fea1e1928cf4e1bd5342aa0a4c885ccb5cf137
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96548166"
---
# <a name="release-notes-for-microsoftapplicationinsightssnapshotcollector"></a>ApplicationInsights. Microsoft.applicationinsights.snapshotcollector 的版本資訊

本文包含適用于 .NET 應用程式之 ApplicationInsights. Microsoft.applicationinsights.snapshotcollector NuGet 套件的版本資訊，Application Insights 快照偵錯工具使用此套件。

[深入瞭解](./snapshot-debugger.md) .net 應用程式的 Application Insights 快照偵錯工具。

關於 bug 報告和意見反應，請在 GitHub 上開啟問題： https://github.com/microsoft/ApplicationInsights-dotnet

## <a name="release-notes"></a>版本資訊

## <a name="1374"></a>[1.3.7.4](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.4)
點發行，可解決在測試 Azure App Service 的無程式碼附加案例中發現的問題。
### <a name="changes"></a>變更
- Netcoreapp 3.0 目標現在相依于 ApplicationInsights 之前的 AspNetCore >= 2.1.1 (之前 >= 2.1.2) 。

## <a name="1373"></a>[1.3.7.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.3)
點釋出，可解決一些高度影響的問題。
### <a name="bug-fixes"></a>Bug 修正
- 修正 wwwroot/bin 資料夾中的 PDB 探索，當我們在1.3.6 中變更符號搜尋演算法時，這會中斷。
- 修正遙測中的雜訊 ExtractWasCalledMultipleTimesException。

## <a name="137"></a>[1.3.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7)
### <a name="changes"></a>變更
- Microsoft.applicationinsights.snapshotcollector 的 netcoreapp 2.0 目標相依于 ApplicationInsights。 AspNetCore >= 2.1.1 (再次) 。 這會將行為還原為1.3.5 之前的行為。 我們嘗試在1.3.6 中進行升級，但中斷了某些 Azure App Service 案例。
### <a name="new-features"></a>新功能
- Snapshot Collector 從 APPLICATIONINSIGHTS_CONNECTION_STRING 環境變數或從 TelemetryConfiguration 中讀取和剖析 ConnectionString。 這主要是用來設定連接到快照集服務的端點。 如需詳細資訊，請參閱 [連接字串檔](./sdk-connection-string.md)。
### <a name="bug-fixes"></a>Bug 修正
- 切換為針對 net45 以外的所有目標使用 HttpClient，因為在某些環境中因為不相容的 SecurityProtocol 而導致 WebRequest 失敗 (需要 TLS 1.2) 。

## <a name="136"></a>[1.3.6](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.6)
### <a name="changes"></a>變更
- Microsoft.applicationinsights.snapshotcollector 現在相依于 Microsoft ApplicationInsights >= 2.5.1 適用于所有目標 framework。 如果您的應用程式相依于較舊版本的 ApplicationInsights SDK，這可能是一項重大變更。
- 移除快照集上載中的 TLS 1.0 和1.1 支援。
- PDB 掃描期間現在會預設為24小時，而不是15分鐘。 可透過 SnapshotCollectorConfiguration 上的 PdbRescanInterval 設定。
- PDB 掃描只搜尋最上層資料夾，而不是遞迴。 如果您的符號位於二進位檔案夾的子資料夾中，這可能是一項重大變更。
### <a name="new-features"></a>新功能
- 在 Snapshotuploader.exe 中進行記錄輪替，以避免將記錄檔資料夾填入舊檔案。
- 取消優化支援在 .NET Core 3.0 應用程式的附加) 上透過 ReJIT 進行 (。
- 將符號新增至 NuGet 套件。
- 上傳小型傾印時，請設定其他中繼資料。
- 已將初始化的屬性新增至 SnapshotCollectorTelemetryProcessor。 這是 CancellationToken，當 Snapshot Collector 完全初始化並連接至服務端點時，將會取消此工作。
- 現在可以針對動態產生的方法中的例外狀況來捕捉快照集。 例如，Entity Framework 查詢所產生的已編譯運算式樹狀架構。
### <a name="bug-fixes"></a>Bug 修正
- AmbiguousMatchException 載入 Snapshot Collector，因為狀態監視器。
- GetSnapshotCollector 擴充方法現在會搜尋所有 TelemetrySinks。
- 請勿在不支援的平臺上啟動快照集上載。
- 在 deoptimizing 動態方法 (時處理 InvalidOperationException，例如 Entity Framework) 

## <a name="135"></a>[1.3.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.5)
- 新增對主權雲端的支援 (舊版版本無法在主權雲端中運作) 
- 使用 AddSnapshotCollector ( # A1 來新增快照集收集器變得更容易。 您可以在[這裡](./snapshot-debugger-appservice.md)找到詳細資訊。
- 使用 FISMA MD5 設定來驗證 blob 區塊。 這可避免預設的 .NET MD5 密碼編譯演算法，此演算法在作業系統設定為符合 FIPS 規範的模式時無法使用。
- 在 deoptimizing 函式呼叫時，忽略 .NET Framework 框架。 此行為可透過 DeoptimizeIgnoredModules 設定來控制。
- 新增 `DeoptimizeMethodCount` 允許取消優化多個函式呼叫的設定。 如需詳細資訊，請參閱這裡。

## <a name="134"></a>[1.3.4](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.4)
- 允許結構化檢測金鑰。
- 提高 Snapshotuploader.exe 穩定性-即使無法移動舊的上載者記錄，仍繼續啟動。
- 當 SnapshotUploader.exe 立即結束時，請重新啟用報告其他遙測 (已在 1.3.3) 中停用。
- 簡化內部遙測。
- _實驗性功能_：快照點收集計畫：新增 "snapshotOnFirstOccurence"。 詳細資訊可從[這裡](https://gist.github.com/alexaloni/5b4d069d17de0dabe384ea30e3f21dfe)取得。

## <a name="133"></a>[1.3.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.3)
- 已修正導致 SnapshotUploader.exe 停止回應，且不會上傳 .NET Core 應用程式快照集的 bug。

## <a name="132"></a>[1.3.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.2)
- _實驗性功能_：快照點收集計畫。 詳細資訊可從[這裡](https://gist.github.com/alexaloni/5b4d069d17de0dabe384ea30e3f21dfe)取得。
- SnapshotUploader.exe 將會在執行時間卸載載入 Microsoft.applicationinsights.snapshotcollector 的 AppDomain 時結束，而不是等候進程結束。 這可改善收集器在 IIS 中裝載時的可靠性。
- 新增設定，以允許使用相同檢測金鑰的多個 Microsoft.applicationinsights.snapshotcollector 實例共用相同的 Snapshotuploader.exe 流程： ShareUploaderProcess (預設為 `true`) 。
- 當 SnapshotUploader.exe 立即結束時，報告其他遙測資料。
- 減少 SnapshotUploader.exe 寫入磁片所需的支援檔案數目。

## <a name="131"></a>[1.3.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.1)
- 移除使用 RtlCloneUserProcess API 收集快照集的支援，且僅支援 PssCaptureSnapshots API。
- 增加從1到3的10分鐘內可以捕獲多少快照集的預設限制。
- 允許 SnapshotUploader.exe 協商 TLS 1.1 和1。2
- 在 Snapshotuploader.exe 記錄警告或錯誤時報告其他遙測
- 當後端服務回報每天達到每日配額 (50 快照集時，停止拍攝快照集) 
- 在 SnapshotUploader.exe 中新增額外的檢查，不允許在相同的時間執行兩個實例。

## <a name="130"></a>[1.3.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.0)
### <a name="changes"></a>變更
- 針對以 .NET Framework 為目標的應用程式，Snapshot Collector 現在相依于 ApplicationInsights 版本2.3.0 或更新版本。
它是用來2.2.0 或更高的版本。
我們相信大部分的應用程式都不會造成問題，但請讓我們知道此變更是否會導致您無法使用最新的 Snapshot Collector。
- 在重試失敗的上傳時，使用快照集上載中的指數輪詢延遲。
- 如果有可用的) ，請使用 ServerTelemetryChannel (，以更可靠地報告遙測。
- 在快照偵錯工具服務的初始連接上使用 ' SdkInternalOperationsMonitor '，以供相依性追蹤略過。
- 改善快照偵錯工具服務初始連線的遙測。
- 報告其他遙測：
  - Azure App Service 版本。
  - Azure 計算實例。
  - 容器。
  - Azure 函數應用程式。
### <a name="bug-fixes"></a>Bug 修正
- 當問題計數器重設間隔設定為24天時，請將其解釋為24小時。
- 修正當處置快照集發生例外狀況時，快照集上傳程式會停止處理新快照集的 bug。

## <a name="123"></a>[1.2.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.3)
- 使用快照集上載程式二進位檔修正強式名稱簽署。

## <a name="122"></a>[1.2.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.2)
### <a name="changes"></a>變更
- Snapshotuploader.exe (64) 所需的檔案現在會內嵌為主要 DLL 中的資源。 這表示已不再建立 SnapshotCollectorFiles 資料夾，可簡化組建和部署，並減少方案總管中的雜亂。 升級以檢查檔案中的變更時，請務必小心 `.csproj` 。 不再 `Microsoft.ApplicationInsights.SnapshotCollector.targets` 需要該檔案。
- 即使 ProvideAnonymousTelemetry 設為 false，仍會將遙測記錄到您的 Application Insights 資源。 因此我們可以在 Azure 入口網站中執行健康情況檢查功能。 ProvideAnonymousTelemetry 只會影響傳送給 Microsoft 的遙測資料以進行產品支援和改進。
- 將 TempFolder 或 ShadowCopyFolder 重新導向至環境變數時，請讓收集器保持閒置，直到設定這些環境變數為止。
- 對於透過 proxy 伺服器連線到網際網路的應用程式，Snapshot Collector 現在將會自動偵測任何 proxy 設定，並將其傳遞給 SnapshotUploader.exe。
- 將 SnapshotUplaoder 流程的優先順序降低 (可能的) 。 您可以透過 IsLowPrioirtySnapshotUploader 選項覆寫此優先順序。
- 針對您想要以程式設計方式設定 Snapshot Collector 的案例，在 TelemetryConfiguration 上加入 GetSnapshotCollector 擴充方法。
- 將 Application Insights SDK 版本設定 (，而不是在客戶面向的遙測中) 應用程式版本。
- 在兩分鐘之後傳送第一個訊號事件。
### <a name="bug-fixes"></a>Bug 修正
- 修正例外狀況有 null 或不可變的資料字典時的 NullReferenceException。
- 在上傳中，如果發生共用違規，請重試比對幾次的 PDB。
- 當一個以上的執行緒在啟動時呼叫遙測管線時，修正重複的遙測。

## <a name="121"></a>[1.2.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.1)
### <a name="changes"></a>變更
- XML 檔批註檔案現在包含在 NuGet 套件中。
- `System.Exception`針對您知道有雜訊的例外狀況，而且想要避免為其建立快照集的案例，新增了 ExcludeFromSnapshotting 擴充方法。
- 已新增 IsEnabledWhenProfiling 設定屬性，預設為 true。 這是先前版本的變更，如果 Application Insights Profiler 正在執行詳細的集合，則會暫時停用快照集建立。 將此屬性設定為 false，即可復原舊行為。
### <a name="bug-fixes"></a>Bug 修正
- 簽署 SnapshotUploader64.exe 正確。
- 保護遙測處理器的雙重初始化。
- 防止在具有多個管線的應用程式中重複記錄遙測資料。
- 使用收集計畫的到期時間來修正 bug，這可能會在24小時後防止快照集。

## <a name="120"></a>[1.2.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.0)
此版本中最大的變更 (因此，移至新的次要版本號碼) 會重寫快照集建立和處理管線。 在舊版中，這項功能是以機器碼 (ProductionBreakpoints *.dll 和 SnapshotHolder*) 來執行。 新的實作為具有 P/Invoke 的所有 managed 程式碼。 在使用新管線的第一個版本中，我們沒有從原始的行為 strayed。 新的執行功能可提供更好的錯誤報表，並將我們設定為未來的改進。

### <a name="other-changes-in-this-version"></a>此版本中的其他變更
- MinidumpUploader.exe 已重新命名為 SnapshotUploader.exe (或 SnapshotUploader64.exe) 。
- 已將計時遙測新增至將/重新重新優化要求。
- 已新增適用于小型傾印上傳的 gzip 壓縮。
- 修正 Pdb 已鎖定而無法進行網站升級的問題。
- 記錄陰影複製時 (SnapshotCollectorFiles) 的原始檔案夾名稱。
- 調整64位進程的記憶體限制，以防止網站因為 OOM 而重新開機。
- 修正即使在停用之後仍會收集快照集的問題。
- 將心跳事件記錄至客戶的 AI 資源。
- 從問題識別碼移除「來源」，以改善快照集速度。

## <a name="112"></a>[1.1.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.1.2)
### <a name="changes"></a>變更
增強的使用量遙測
- 偵測和報告 .NET 版本和 OS
- 偵測及報告其他 Azure 環境 (雲端服務、Service Fabric) 
- 記錄和報告例外狀況計量 (第一次可能發生的例外狀況，以及在心跳遙測中) 的 TrackException 呼叫數目。
### <a name="bug-fixes"></a>Bug 修正
- 不會擲回內部例外狀況 (Win32Exception) 的 SqlException 正確處理。
- 修剪符號資料夾上的尾端空格，這會導致不正確地剖析 Minidumpuploader.exe 的命令列引數。
- 防止對快照偵錯工具代理程式端點的失敗連接進行無限次重試。

## <a name="110"></a>[1.1.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.1.0)
### <a name="changes"></a>變更
- 已新增主機記憶體保護。 這項功能可降低對主機電腦記憶體的影響。
- 改善 Azure 入口網站快照集的觀賞體驗。
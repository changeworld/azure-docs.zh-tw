---
title: 檢視 Azure Application Insights 應用程式資料 | Microsoft Docs
description: 您可以使用 Applications Insights Connector 解決方案來診斷效能問題，以及了解使用者如何使用 Application Insights 監視您的應用程式。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/13/2019
ms.openlocfilehash: c143d8aa24d3479f4619ea2c220d4a0c593f9cb1
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77665148"
---
# <a name="application-insights-connector-management-solution-deprecated"></a>Application Insights Connector 管理解決方案 (取代)

![Application Insights 符號](./media/app-insights-connector/app-insights-connector-symbol.png)

>[!NOTE]
> 因為[跨資源查詢](../../azure-monitor/log-query/cross-workspace-query.md)的支援，已不再需要 Application Insights Connector 管理解決方案。 此方案已被取代，並從 Azure Marketplace 中移除，連同 Azure 商業雲端的 OMS 入口網站也於 2019 年 1 月 15 日正式被取代。 Azure 美國政府雲端將於 2019 年 3 月 30 日淘汰該服務。
>
>現有的連線將繼續運作，直到 2019 年 6 月 30 日為止。  隨著 OMS 入口網站被淘汰，將無法從入口網站設定及移除現有連線。 請參閱下方的[使用 PowerShell 移除連接器](#removing-the-connector-with-powershell)，了解使用 PowerShell 來移除現有連線的指令碼。
>
>如需查詢多個應用程式的 Application Insights 記錄資料的指引，請參閱[整合多個 Azure 監視器 Application Insights 資源](../log-query/unify-app-resource-data.md)。 如需 OMS 入口網站被取代的詳細資訊，請參閱[移至 Azure 的 OMS 入口網站](../../azure-monitor/platform/oms-portal-transition.md)。
>
> 

Applications Insights Connector 解決方案可協助您診斷效能問題，以及了解使用者如何使用 [Application Insights](../../azure-monitor/app/app-insights-overview.md) 監視您的應用程式。 在 Log Analytics 中可使用開發人員在 Application Insights 中看見的相同應用程式遙測檢視。 不過，在整合 Application Insights 應用程式與 Log Analytics 時，將作業與應用程式資料放在一個地方可提高您應用程式的可見性。 具有相同的檢視，可協助您與您的應用程式開發人員共同作業。 常見的檢視可協助減少偵測及解決應用程式和平台問題的時間。

當您使用解決方案時，您可以：

- 在一個地方檢視所有 Application Insights 應用程式，即使它們位於不同的 Azure 訂用帳戶中
- 讓基礎結構資料與應用程式資料相互關聯
- 在記錄搜尋中以檢視方塊將應用程式資料視覺化
- 在 Azure 入口網站中將 Log Analytics 資料轉換到 Application Insights 應用程式


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connected-sources"></a>連接的來源

不同於大部分其他 Log Analytics 解決方案，代理程式不會收集 Application Insights Connector 的資料。 解決方案使用的所有資料直接來自於 Azure。

| 連接的來源 | 支援 | 描述 |
| --- | --- | --- |
| [Windows 代理程式](../../azure-monitor/platform/agent-windows.md) | 否 | 解決方案不會收集來自 Windows 代理程式的資訊。 |
| [Linux 代理程式](../../azure-monitor/learn/quick-collect-linux-computer.md) | 否 | 解決方案不會收集來自 Linux 代理程式的資訊。 |
| [SCOM 管理群組](../../azure-monitor/platform/om-agents.md) | 否 | 解決方案不會收集來自連線 SCOM 管理群組的代理程式之中的資訊。 |
| [Azure 儲存體帳戶](collect-azure-metrics-logs.md) | 否 | 解決方案不會收集來自 Azure 儲存體的資訊。 |

## <a name="prerequisites"></a>必要條件

- 若要存取 Application Insights Connector 資訊，您必須具有 Azure 訂用帳戶
- 您必須至少有一個已設定的 Application Insights 資源。
- 您必須是 Application Insights 資源的擁有者或參與者。

## <a name="configuration"></a>組態

1. 從 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AppInsights?tab=Overview) 或使用[從方案庫新增 Log Analytics 方案](../../azure-monitor/insights/solutions.md)中所述的程序，啟用 Azure Web Apps 分析解決方案。
2. 瀏覽至 [Azure 入口網站](https://portal.azure.com)。 選取 [所有服務] 來開啟 Application Insights。 然後，搜尋 Application Insights。 
3. 在 [訂用帳戶] 之下，選取擁有 Application Insights 資源的訂用帳戶，然後在 [名稱] 之下，選取一或多個應用程式。
4. Haga clic en **Guardar**.

大約 30 分鐘內，資料就會變成可用，而且 Application Insights 圖格資料會更新，如下圖所示：

![Application Insights 圖格](./media/app-insights-connector/app-insights-tile.png)

要牢記在心的其他幾點：

- 您只可以將 Application Insights 應用程式連結至一個 Log Analytics 工作區。
- 您只可以將 [Basic 或 Enterprise Application Insights 資源](https://azure.microsoft.com/pricing/details/application-insights)連結至 Log Analytics。 不過，您可以使用 Log Analytics 的免費層。

## <a name="management-packs"></a>管理組件

此解決方案不會在已連線的管理群組中安裝任何管理組件。

## <a name="use-the-solution"></a>使用解決方案

下列各節說明如何使用 Application Insights 儀表板中顯示的刀鋒視窗，檢視和處理您應用程式的資料。

### <a name="view-application-insights-connector-information"></a>檢視 Application Insights Connector 資訊

按一下 [Application Insights] 圖格，以開啟 [Application Insights] 儀表板，請參閱下列刀鋒視窗。

![Application Insights 儀表板](./media/app-insights-connector/app-insights-dash01.png)

![Application Insights 儀表板](./media/app-insights-connector/app-insights-dash02.png)

此儀表板包含下表所示的刀鋒視窗。 每個刀鋒視窗最多會列出 10 個與該刀鋒視窗中指定範圍和時間範圍的準則相符的項目。 當您按一下刀鋒視窗底部的 [查看全部]，或按一下刀鋒視窗標頭時，您可以執行記錄搜尋來傳回所有記錄。


| **資料行** | **說明** |
| --- | --- |
| 應用程式 - 應用程式的數目 | 顯示 [應用程式] 資源中的應用程式數目。 也會列出應用程式名稱以及各自的應用程式記錄計數。 按一下此數字可執行記錄搜尋，以搜尋 <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName</code> <br><br>  按一下應用程式名稱，可執行應用程式的記錄搜尋，以顯示每個主機的應用程式記錄、各遙測類型的記錄，以及各類型的所有資料 (以最後一天為基礎)。 |
| 資料量 - 傳送資料的主機 | 顯示傳送資料的電腦主機數目。 也會列出電腦主機和每部主機的記錄計數。 按一下此數字可執行記錄搜尋，以搜尋 <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by Host</code> <br><br> 按一下電腦名稱，可執行主機的記錄搜尋，以顯示每個主機的應用程式記錄、各遙測類型的記錄，以及各類型的所有資料 (以最後一天為基礎)。 |
| 可用性 – Webtest 結果 | 顯示 Web 測試結果的環圈圖，指出成功或失敗。 按一下該圖表可執行記錄搜尋，以搜尋 <code>ApplicationInsights &#124; where TelemetryType == "Availability" &#124; summarize AggregatedValue = sum(SampledCount) by AvailabilityResult</code> <br><br> 結果會顯示所有測試的通過和失敗數目。 它會顯示過去一分鐘有流量的所有 Web Apps。 按一下應用程式名稱，以檢視可顯示失敗 Web 測試詳細資料的記錄搜尋。 |
| 伺服器要求 – 每小時的要求 | 顯示各種應用程式每小時的伺服器要求折線圖。 將滑鼠移至圖表中的一條線上，可查看在某個時間點接收最多要求的前 3 個應用程式。 也會顯示在所選期間內接收要求的應用程式清單和要求數目。 <br><br>按一下圖表可執行記錄搜尋，以搜尋 <code>ApplicationInsights &#124; where TelemetryType == "Request" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> 結果會顯示各種應用程式每小時的伺服器要求折線圖。 <br><br> 按一下清單中的應用程式可執行記錄搜尋，以搜尋 <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> 結果會顯示要求清單、不同時間和要求期間的要求圖表，以及要求回應碼的清單。   |
| 失敗 – 每小時失敗的要求 | 顯示每小時失敗的應用程式要求折線圖。 將滑鼠移至圖表上，可查看在某個時間點具有最多失敗要求的前 3 個應用程式。 也會顯示詳列失敗要求數目的應用程式清單。 按一下圖表可執行記錄搜尋，以搜尋 <code>ApplicationInsights &#124; where TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> 結果會顯示更詳細的失敗應用程式要求折線圖。 <br><br>按一下清單中的項目可執行記錄搜尋，以搜尋 <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> 結果會顯示失敗的要求、不同時間和要求期間的失敗要求圖表，以及失敗要求回應碼的清單。 |
| 例外狀況 – 每小時的例外狀況 | 顯示每小時的例外狀況折線圖。 將滑鼠移至圖表上，可查看在某個時間點具有最多例外狀況的前 3 個應用程式。 也會顯示詳列例外狀況數目的應用程式清單。 按一下圖表可執行記錄搜尋，以搜尋 <code>ApplicationInsights &#124; where TelemetryType == "Exception" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> 結果會顯示更詳細的例外狀況折線圖。 <br><br>按一下清單中的項目可執行記錄搜尋，以搜尋 <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Exception"</code> 結果會顯示例外狀況清單、不同時間的例外狀況和失敗要求圖表，以及例外狀況類型的清單。  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>透過記錄搜尋來檢視 Application Insights 檢視方塊

當您按一下儀表板中的任何項目時，您會看到搜尋中顯示的 Application Insights 檢視方塊。 此檢視方塊會根據所選的遙測類型提供延伸視覺效果。 因此，視覺效果內容會隨著不同的遙測類型而改變。

當您按一下 [應用程式] 刀鋒視窗中的任意處時，您會看到預設 [應用程式] 檢視方塊。

![Application Insights 應用程式檢視方塊](./media/app-insights-connector/applications-blade-drill-search.png)

此檢視方塊會顯示您所選取的應用程式概觀。

[可用性] 刀鋒視窗會顯示不同的檢視方塊，您可以在其中查看 Web 測試結果和相關的失敗要求。

![Application Insights 可用性檢視方塊](./media/app-insights-connector/availability-blade-drill-search.png)

當您按一下 [伺服器要求] 或 [失敗] 刀鋒視窗中的任意處時，檢視方塊元件會變更以提供要求的相關視覺效果。

![Application Insights 失敗刀鋒視窗](./media/app-insights-connector/server-requests-failures-drill-search.png)

當您按一下 [例外狀況] 刀鋒視窗中的任意處時，您可看見針對例外狀況量身打造的視覺效果。

![Application Insights 例外狀況刀鋒視窗](./media/app-insights-connector/exceptions-blade-drill-search.png)

不論您按一下 [Application Insights Connector] 儀表板上的某個項目，或按一下 [搜尋] 頁面本身，任何傳回 Application Insights 資料的查詢都會顯示 Application Insights 檢視方塊。 例如，如果您正在檢視 Application Insights 資料， **&#42;** 查詢也會顯示如下圖所示的檢視方塊索引標籤：

![Application Insights](./media/app-insights-connector/app-insights-search.png)

檢視方塊元件會根據搜尋查詢進行更新。 這表示您可以使用任何搜尋欄位來篩選結果，以便您查看下列各項的資料：

- 所有應用程式
- 單一選取的應用程式
- 應用程式群組

### <a name="pivot-to-an-app-in-the-azure-portal"></a>在 Azure 入口網站中轉換至應用程式

當您使用 Azure 入口網站時，Application Insights Connector 刀鋒視窗的設計可讓您轉換到所選的 Application Insights 應用程式。 您可以使用此解決方案作為高階監視平台，協助您針對應用程式進行疑難排解。 當您在任何已連線的應用程式中看到潛在問題時，您可以在 Log Analytics 搜尋中深入探詢，也可以直接轉換至 Application Insights 應用程式。

若要轉換，按一下出現在每行結尾的省略符號 ( **...** )，然後選取 [在 Application Insights 中開啟]。

>[!NOTE]
>[在 Application Insights 中開啟] 無法使用於 Azure 入口網站。

![開啟 Application Insights](./media/app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>取樣更正資料

Application Insights 提供 *[取樣更正](../../azure-monitor/app/sampling.md)* 來協助減少遙測流量。 當您在 Application Insights 應用程式上啟用取樣功能時，您取得之 Application Insights 和 Log Analytics 中儲存的項目數會減少。 雖然資料一致性會保留在 [Application Insights Connector] 頁面和檢視方塊中，您應針對您的自訂查詢手動更正取樣的資料。

在記錄搜尋查詢中取樣更正的範例如下：

```
ApplicationInsights | summarize AggregatedValue = sum(SampledCount) by TelemetryType
```

[取樣計數] 欄位會存在所有項目中，並且顯示項目所代表的資料點數目。 如果您開啟 Application Insights 應用程式的取樣功能，[取樣計數] 會大於 1。 若要計算應用程式所產生的實際項目數，請加總 [取樣計數] 欄位。

取樣只會影響應用程式產生的項目總數。 您不需要更正 **RequestDuration** 或 **AvailabilityDuration** 等欄位的取樣，因為這些欄位會顯示代表性項目的平均值。

## <a name="input-data"></a>輸入資料

解決方案會從已連線的 Application Insights 應用程式收到下列類型的遙測資料：

- 可用性
- 例外狀況
- 要求
- 頁面檢視 – 為了讓工作區接收頁面檢視，您必須設定您的應用程式來收集該資訊。 如需詳細資訊，請參閱 [PageViews](../../azure-monitor/app/api-custom-events-metrics.md#page-views)。
- 自訂事件 – 為了讓工作區接收訂事件，您必須設定您的應用程式來收集該資訊。 如需詳細資訊，請參閱 [TrackEvent](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)。

資料可用時，是由 Log Analytics 從 Application Insights 接收。

## <a name="output-data"></a>輸出資料

對於每種類型的輸入資料，系統會建立「類型」 為 ApplicationInsights 的記錄。 ApplicationInsights 記錄有下列各節所示的屬性：

### <a name="generic-fields"></a>一般欄位

| 屬性 | 描述 |
| --- | --- |
| 類型 | ApplicationInsights |
| ClientIP |   |
| TimeGenerated | 記錄的時間 |
| ApplicationId | Application Insights 應用程式的檢測金鑰 |
| ApplicationName | Application Insights 應用程式的名稱 |
| RoleInstance | 伺服器主機的識別碼 |
| DeviceType | 用戶端裝置 |
| ScreenResolution |   |
| Continent | 要求的起源洲 |
| Country | 要求的來源國家/地區 |
| Province | 要求的起源省、州或地區設定 |
| 城市 | 要求的起源城市或鄉鎮 |
| isSynthetic | 指出要求是由使用者或自動化方法建立。 True = 自動化方法或 false = 使用者產生 |
| SamplingRate | 由傳送至入口網站之 SDK 所產生的遙測百分比。 範圍 0.0-100.0。 |
| SampledCount | 100/(SamplingRate)。 例如，4 =&gt; 25% |
| IsAuthenticated | True 或 False |
| OperationID | 具有相同作業識別碼的項目會在入口網站中顯示為相關項目。 通常要求識別碼 |
| ParentOperationID | 父代作業的識別碼 |
| OperationName |   |
| SessionId | 用來唯一識別建立要求之工作階段的 GUID |
| SourceSystem | ApplicationInsights |

### <a name="availability-specific-fields"></a>可用性專屬欄位

| 屬性 | 描述 |
| --- | --- |
| TelemetryType | 可用性 |
| AvailabilityTestName | Web 測試的名稱 |
| AvailabilityRunLocation | http 要求的地理區域來源 |
| AvailabilityResult | 指出 Web 測試的成功結果 |
| AvailabilityMessage | 附加至 Web 測試的訊息 |
| AvailabilityCount | 100/(Sampling Rate)。 例如，4 =&gt; 25% |
| DataSizeMetricValue | 1.0 或 0.0 |
| DataSizeMetricCount | 100/(Sampling Rate)。 例如，4 =&gt; 25% |
| AvailabilityDuration | Web 測試的持續時間 (毫秒) |
| AvailabilityDurationCount | 100/(Sampling Rate)。 例如，4 =&gt; 25% |
| AvailabilityValue |   |
| AvailabilityMetricCount |   |
| AvailabilityTestId | Web 測試的唯一 GUID |
| AvailabilityTimestamp | 可用性測試的精確時間戳記 |
| AvailabilityDurationMin | 對於取樣記錄，此欄位會顯示代表性資料點的最小 Web 測試持續時間 (毫秒) |
| AvailabilityDurationMax | 對於取樣記錄，此欄位會顯示代表性資料點的最大 Web 測試持續時間 (毫秒) |
| AvailabilityDurationStdDev | 對於取樣記錄，此欄位會顯示代表性資料點的所有 Web 測試持續時間 (毫秒) 之間的標準差 |
| AvailabilityMin |   |
| AvailabilityMax |   |
| AvailabilityStdDev | &nbsp;  |

### <a name="exception-specific-fields"></a>例外狀況專屬欄位

| 類型 | ApplicationInsights |
| --- | --- |
| TelemetryType | 例外狀況 |
| ExceptionType | 例外狀況的類型 |
| ExceptionMethod | 建立例外狀況的方法 |
| ExceptionAssembly | 組件包含架構和版本，以及公開金鑰權杖 |
| ExceptionGroup | 例外狀況的類型 |
| ExceptionHandledAt | 指出處理此例外狀況的層級 |
| ExceptionCount | 100/(Sampling Rate)。 例如，4 =&gt; 25% |
| ExceptionMessage | 例外狀況的訊息 |
| ExceptionStack | 例外狀況的完整堆疊 |
| ExceptionHasStack | 如果例外狀況有一個堆疊，則為 True |



### <a name="request-specific-fields"></a>要求專屬欄位

| 屬性 | 描述 |
| --- | --- |
| 類型 | ApplicationInsights |
| TelemetryType | 要求 |
| ResponseCode | 傳送至用戶端的 HTTP 回應 |
| RequestSuccess | 指出成功或失敗。 True 或 False。 |
| RequestID | 用以唯一識別要求的識別碼 |
| RequestName | GET/POST + URL 基底 |
| RequestDuration | 要求的持續時間 (秒) |
| URL | 要求 URL (不包括主機) |
| Host | Web 伺服器主機 |
| URLBase | 要求的完整 URL |
| ApplicationProtocol | 應用程式所使用的通訊協定類型 |
| RequestCount | 100/(Sampling Rate)。 例如，4 =&gt; 25% |
| RequestDurationCount | 100/(Sampling Rate)。 例如，4 =&gt; 25% |
| RequestDurationMin | 對於取樣記錄，此欄位會顯示代表性資料點的最小要求持續時間 (毫秒)。 |
| RequestDurationMax | 對於取樣記錄，此欄位會顯示代表性資料點的最大要求持續時間 (毫秒) |
| RequestDurationStdDev | 對於取樣記錄，此欄位會顯示代表性資料點的所有要求持續時間 (毫秒) 之間的標準差 |

## <a name="sample-log-searches"></a>記錄搜尋範例

此解決方案並沒有一組顯示在儀表板上的範例記錄搜尋。 不過，[檢視 Application Insights Connector 資訊](#view-application-insights-connector-information)一節會顯示範例記錄搜尋查詢及說明。

## <a name="removing-the-connector-with-powershell"></a>使用 PowerShell 移除連接器
隨著 OMS 入口網站被淘汰，將無法從入口網站設定及移除現有連線。 您可以使用下列 PowerShell 指令碼來移除現有連線。 您必須是擁有者或工作區的參與者和 Application Insights 資源的讀者，才能執行此作業。

```powershell
$Subscription_app = "App Subscription Name"
$ResourceGroup_app = "App ResourceGroup"
$Application = "Application Name"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"

Connect-AzAccount
Set-AzContext -SubscriptionId $Subscription_app
$AIApp = Get-AzApplicationInsights -ResourceGroupName $ResourceGroup_app -Name $Application 
Set-AzContext -SubscriptionId $Subscription_workspace
Remove-AzOperationalInsightsDataSource -WorkspaceName $Workspace -ResourceGroupName $ResourceGroup_workspace -Name $AIApp.Id
```

您可以使用下列 PowerShell 指令碼叫用 REST API 呼叫以擷取應用程式清單。 

```powershell
Connect-AzAccount
$Tenant = "TenantId"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"
$AccessToken = "AAD Authentication Token" 

Set-AzContext -SubscriptionId $Subscription_workspace
$LAWorkspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $ResourceGroup_workspace -Name $Workspace

$Headers = @{
    "Authorization" = "Bearer $($AccessToken)"
    "x-ms-client-tenant-id" = $Tenant
}

$Connections = Invoke-RestMethod -Method "GET" -Uri "https://management.azure.com$($LAWorkspace.ResourceId)/dataSources/?%24filter=kind%20eq%20'ApplicationInsights'&api-version=2015-11-01-preview" -Headers $Headers
$ConnectionsJson = $Connections | ConvertTo-Json
```
此指令碼需要持有人驗證權杖，以便對 Azure Active Directory 進行驗證。 擷取此權杖的其中一個方法是使用 [REST API 文件網站](https://docs.microsoft.com/rest/api/loganalytics/datasources/createorupdate)中的發行項。 按一下 [試用] 並登入您的 Azure 訂用帳戶。 您可以從 [要求預覽] 複製持有人權杖，如下圖所示。


![持有人權杖](media/app-insights-connector/bearer-token.png)


您也可以擷取使用記錄查詢的應用程式清單：

```Kusto
ApplicationInsights | summarize by ApplicationName
```

## <a name="next-steps"></a>後續步驟

- 使用[記錄搜尋](../../azure-monitor/log-query/log-query-overview.md)來檢視 Application Insights 應用程式的詳細資訊。

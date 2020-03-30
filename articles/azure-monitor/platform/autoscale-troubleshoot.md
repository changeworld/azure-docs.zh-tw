---
title: 故障排除 Azure 自動縮放
description: 跟蹤服務結構、虛擬機器、Web 應用和雲服務中使用的 Azure 自動縮放問題。
ms.topic: conceptual
ms.date: 11/4/2019
ms.subservice: autoscale
ms.openlocfilehash: 9780cf88070110c4efc13c477d65307aa3985fe5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75751343"
---
# <a name="troubleshooting-azure-autoscale"></a>故障排除 Azure 自動縮放
 
Azure 監視器自動縮放可説明您運行適當數量的資源來處理應用程式中的負載。 它使您能夠添加資源來處理負載的增加，並通過刪除閒置的資源來節省資金。 您可以根據您選擇的計畫、固定日期時間或資源指標進行縮放。 有關詳細資訊，請參閱[自動縮放概述](autoscale-overview.md)。

自動縮放服務為您提供指標和日誌，以瞭解發生了哪些縮放操作，以及評估導致這些操作的條件。 您可以找到問題的答案，例如：

- 為什麼我的服務正在擴展或擴展？
- 為什麼我的服務沒有擴展？
- 為什麼自動縮放操作失敗？
- 為什麼自動縮放操作需要時間進行縮放？
  
## <a name="autoscale-metrics"></a>自動縮放指標

自動縮放為您提供了[四個指標](metrics-supported.md#microsoftinsightsautoscalesettings)來瞭解其操作。 

- **觀察指標值**- 選擇對其執行縮放操作的指標值，如自動縮放引擎看到或計算的那樣。 由於單個自動縮放設置可以有多個規則，因此有多個指標源，因此可以使用"指標源"作為維度進行篩選。
- **公制閾值**- 您設置為執行縮放操作的閾值。 由於單個自動縮放設置可以有多個規則，因此有多個指標源，因此可以使用"度量規則"作為維度進行篩選。
- **觀測到的容量**- 自動縮放引擎所看到的目標資源的活動實例數。
- **已起始的調整動作** - 自動調整引擎所起始的相應放大和相應縮小動作數目。 您可以按橫向擴展與縮放來篩選操作。

您可以使用[指標資源管理器](metrics-getting-started.md)在一個位置繪製上述指標。 圖表應顯示：

  - 實際指標
  - 自動縮放引擎看到的/計算的指標
  - 縮放操作的閾值
  - 容量的變化 

## <a name="example-1---analyzing-a-simple-autoscale-rule"></a>示例 1 - 分析簡單的自動縮放規則 

我們為虛擬機器縮放集提供了一個簡單的自動縮放設置，該設置：

- 當集的平均 CPU 百分比大於 70% 時，10 分鐘內擴展 
- 當集的 CPU 百分比小於 5%時，在 10 分鐘內縮放。 

讓我們查看自動縮放服務的指標。
 
![虛擬機器規模設置百分比 CPU 示例](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-1.png)

![虛擬機器規模設置百分比 CPU 示例](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-2.png)

***圖 1a - 虛擬機器規模集的 CPU 百分比指標和自動縮放設置的觀察指標值指標***

![公制閾值和觀察到的容量](media/autoscale-troubleshoot/autoscale-metric-threshold-capacity-ex-full.png)

***圖 1b - 公制閾值和觀測容量***

在圖 1b 中，橫向擴展規則的**公制閾值**（淺藍線）為 70。  **"觀察容量**"（深藍色線）顯示活動實例的數量，當前為 3。 

> [!NOTE]
> 您需要按指標觸發器規則維度橫向橫向擴展（增加）規則篩選**指標閾值**，以查看橫向擴展閾值和規則中的刻度（減少）。 

## <a name="example-2---advanced-autoscaling-for-a-virtual-machine-scale-set"></a>示例 2 - 虛擬機器規模集的高級自動縮放

我們有一個自動縮放設置，允許虛擬機器縮放集資源根據其自己的指標**出站流**進行橫向擴展。 請注意，檢查指標閾值的**除法指標（按實例計數**選項）。 

縮放操作規則為： 

如果**每個實例的出站流**值大於 10，則自動縮放服務應擴展 1 個實例。 

在這種情況下，自動縮放引擎的觀測指標值計算為實際指標值除以實例數。 如果觀察到的指標值小於閾值，則不啟動橫向擴展操作。 
 
![虛擬機器規模集自動縮放指標圖表示例](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-1.png)

![虛擬機器規模集自動縮放指標圖表示例](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-2.png)

***圖 2 - 虛擬機器規模集自動縮放指標圖表示例***

在圖 2 中，您可以看到兩個指標圖表。 

頂部的圖表顯示**出站流**指標的實際值。 實際值為 6。 

底部的圖表顯示幾個值。 
 - **觀測指標值**（淺藍色）為 3，因為有 2 個活動實例，6 除以 2 是 3。 
 - **"觀測容量**"（紫色）顯示自動縮放引擎看到的實例計數。 
 - **公制閾值**（淺綠色）設置為 10。 

如果存在多個縮放操作規則，則可以使用"指標資源管理器"圖表中的拆分或**添加篩選器**選項按特定源或規則查看指標。 有關拆分指標圖表的詳細資訊，請參閱[指標圖表的高級功能 - 拆分](metrics-charts.md#apply-splitting-to-a-chart)

## <a name="example-3---understanding-autoscale-events"></a>示例 3 - 瞭解自動縮放事件

在自動縮放設置螢幕中，轉到 **"執行歷程記錄**"選項卡以查看最新的縮放操作。 該選項卡還顯示**觀察容量**隨時間的變化。 要查找有關所有自動縮放操作（包括更新/刪除自動縮放設置等操作）的更多詳細資訊，請查看活動日誌，並通過自動縮放操作進行篩選。

![自動縮放設置執行歷程記錄](media/autoscale-troubleshoot/autoscale-setting-run-history-smaller.png)

## <a name="autoscale-resource-logs"></a>自動縮放資源日誌

與任何其他 Azure 資源相同，自動縮放服務提供[資源日誌](platform-logs-overview.md)。 日誌分為兩類。

- **自動縮放評估**- 自動縮放引擎記錄每次進行檢查時每個狀態評估的日誌條目。  該條目包括指標的觀察值、評估的規則以及評估是否導致縮放操作的詳細資訊。

- **自動縮放縮放操作**- 引擎記錄自動縮放服務啟動的操作事件以及這些縮放操作的結果（成功、失敗以及自動縮放服務所看到的縮放量）。

與任何 Azure 監視器支援的服務一樣，您可以使用["診斷設置"](diagnostic-settings.md)路由這些日誌：

- 到日誌分析工作區進行詳細分析
- 到事件中心，然後到非 Azure 工具
- 到 Azure 存儲帳戶進行存檔  

![自動縮放診斷設置](media/autoscale-troubleshoot/diagnostic-settings.png)

上圖顯示了 Azure 門戶自動縮放的診斷設置。 在那裡，您可以選擇診斷/資源日誌選項卡，並啟用日誌收集和路由。 您還可以使用 REST API、CLI、PowerShell、資源管理器範本執行相同的操作，用於診斷設置，選擇與*Microsoft.Insights/自動縮放設置相同的*資源類型。 

## <a name="troubleshooting-using-autoscale-logs"></a>使用自動縮放日誌進行故障排除 

為了獲得最佳故障排除體驗，我們建議您在創建自動縮放設置時通過工作區將日誌路由到 Azure 監視器日誌（日誌分析）。 此過程顯示在上一節中的圖片中。 您可以使用日誌分析更好地驗證評估並縮放操作。

將自動縮放日誌配置為發送到日誌分析工作區後，可以執行以下查詢以檢查日誌。 

要開始，請嘗試此查詢以查看最新的自動縮放評估日誌：

```Kusto
AutoscaleEvaluationsLog
| limit 50
```

或者嘗試以下查詢以查看最新的縮放動作記錄：

```Kusto
AutoscaleScaleActionsLog
| limit 50
```

使用以下部分來解決這些問題。 

## <a name="a-scale-action-occurred-that-i-didnt-expect"></a>發生了一個我沒想到的縮放操作

首先執行縮放操作的查詢，以查找您感興趣的縮放操作。 如果是最新的縮放操作，請使用以下查詢：

```Kusto
AutoscaleScaleActionsLog
| take 1
```

從縮放動作記錄中選擇"關聯 Id"欄位。 使用"關聯 Id"查找正確的評估日誌。 執行以下查詢將顯示評估的所有規則和條件，從而導致該縮放操作。

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId>"
```

## <a name="what-profile-caused-a-scale-action"></a>什麼設定檔導致了縮放操作？

發生了縮放的操作，但規則和設定檔重疊，需要跟蹤導致該操作的原因。 

查找縮放操作的相關性 Id（如示例 1 中所述），然後在評估日誌上執行查詢以瞭解有關設定檔的更多資訊。

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId_Guid>"
| where ProfileSelected == true
| project ProfileEvaluationTime, Profile, ProfileSelected, EvaluationResult
```

使用以下查詢，還可以更好地理解整個設定檔評估

```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName contains == "profileEvaluation"
| project OperationName, Profile, ProfileEvaluationTime, ProfileSelected, EvaluationResult
```

## <a name="a-scale-action-did-not-occur"></a>未發生縮放操作

我期望一個規模行動，它沒有發生。 可能沒有縮放操作事件或日誌。

如果使用基於指標的比例規則，請查看自動縮放指標。 "**觀察"指標值**或 **"觀察到的容量**"可能不是您期望的，因此比例規則沒有觸發。 您仍然會看到評估，但不會看到橫向擴展規則。 冷卻時間也可能阻止縮放操作發生。 

 查看預期發生縮放操作的時間段內的自動縮放評估日誌。 審查它所做的所有評估，以及為什麼它決定不觸發規模行動。


```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName == "MetricEvaluation" or OperationName == "ScaleRuleEvaluation"
| project OperationName, MetricData, ObservedValue, Threshold, EstimateScaleResult
```

## <a name="scale-action-failed"></a>縮放操作失敗

可能存在自動縮放服務執行規模操作，但系統決定不縮放或未能完成縮放操作的情況。 使用此查詢可以查找失敗的縮放操作。

```Kusto
AutoscaleScaleActionsLog
| where ResultType == "Failed"
| project ResultDescription
```

創建警報規則以收到自動縮放操作或失敗的通知。 您還可以創建警報規則以獲取有關自動縮放事件的通知。

## <a name="schema-of-autoscale-resource-logs"></a>自動縮放資源日誌的架構

有關詳細資訊，請參閱[自動縮放資源日誌](autoscale-resource-log-schema.md)

## <a name="next-steps"></a>後續步驟
閱讀有關[自動縮放最佳實踐](autoscale-best-practices.md)的資訊。 

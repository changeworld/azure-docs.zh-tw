---
title: 針對 Azure 自動調整進行疑難排解
description: 追蹤在 Service Fabric、虛擬機器、Web Apps 和雲端服務中使用的 Azure 自動調整問題。
ms.topic: conceptual
ms.date: 11/4/2019
ms.subservice: autoscale
ms.openlocfilehash: 9780cf88070110c4efc13c477d65307aa3985fe5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75751343"
---
# <a name="troubleshooting-azure-autoscale"></a>針對 Azure 自動調整進行疑難排解
 
Azure 監視器自動調整可協助您讓適當的資源量執行，以處理應用程式的負載。 它可讓您新增資源來處理增加的負載，也可以藉由移除閒置的資源來節省成本。 您可以根據您選擇的排程、固定的日期時間或資源計量來進行調整。 如需詳細資訊，請參閱[自動調整總覽](autoscale-overview.md)。

自動調整服務會提供計量和記錄，以瞭解已發生哪些規模動作，以及評估導致這些動作的條件。 您可以找到下列問題的答案：

- 為什麼我的服務相應放大或縮小？
- 為什麼我的服務無法調整？
- 為什麼自動調整動作失敗？
- 為什麼自動調整規模動作需要時間進行調整？
  
## <a name="autoscale-metrics"></a>自動調整計量

自動調整會為您提供[四個計量](metrics-supported.md#microsoftinsightsautoscalesettings)，以瞭解其運作方式。 

- **觀察**的計量值-您選擇用來進行調整規模動作的計量值，如自動調整引擎所見或計算的度量。 因為單一自動調整設定可以有多個規則，因此有多個計量來源，所以您可以使用「計量來源」做為維度來進行篩選。
- 計量**閾值**-您設定來採取調整動作的閾值。 因為單一自動調整設定可以有多個規則，因此有多個計量來源，所以您可以使用「計量規則」做為維度來進行篩選。
- **觀察到的容量**-自動調整引擎所見的目標資源實例數目上限。
- **已起始的調整動作** - 自動調整引擎所起始的相應放大和相應縮小動作數目。 您可以依相應放大和相應縮小動作進行篩選。

您可以使用[計量瀏覽器](metrics-getting-started.md)，在同一個位置繪製上述計量的圖表。 圖表應該會顯示：

  - 實際度量
  - 自動調整引擎所查看/計算的度量
  - 調整動作的閾值
  - 容量變更 

## <a name="example-1---analyzing-a-simple-autoscale-rule"></a>範例 1-分析簡單的自動調整規則 

針對虛擬機器擴展集，我們有一個簡單的自動調整設定：

- 當集合的平均 CPU 百分比大於 70% 10 分鐘時相應放大 
- 當集合的 CPU 百分比小於5% 時，相應縮小超過10分鐘。 

讓我們來回顧自動調整服務的計量。
 
![虛擬機器擴展集百分比 CPU 範例](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-1.png)

![虛擬機器擴展集百分比 CPU 範例](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-2.png)

***圖 1a-虛擬機器擴展集的 CPU 計量百分比和自動調整設定觀察到的計量值度量***

![計量閾值和觀察的容量](media/autoscale-troubleshoot/autoscale-metric-threshold-capacity-ex-full.png)

***圖 1b-計量閾值和觀察的容量***

在圖1b 中，相應放大規則的計量**閾值**（淺藍色線）為70。  觀察到的**容量**（深色藍線）會顯示作用中的實例數目，目前為3。 

> [!NOTE]
> 您必須依據計量觸發程式規則維度相應放大（增加）規則來篩選計量**臨界值**，以查看相應放大臨界值和依相應縮小規則（減少）。 

## <a name="example-2---advanced-autoscaling-for-a-virtual-machine-scale-set"></a>範例 2-虛擬機器擴展集的先進自動調整

我們有一個自動調整設定，可讓虛擬機器擴展集資源根據自己的計量**輸出流量**向外延展。 請注意，會核取 [度量] 閾值的 [**依實例計數的分割度量**] 選項。 

調整動作規則為： 

如果**每個實例的輸出流量**值大於10，則自動調整服務應由1個實例相應放大。 

在此情況下，自動調整引擎觀察到的計量值是以實際的計量值除以實例數目來計算。 如果觀察到的度量值小於閾值，就不會起始相應放大動作。 
 
![虛擬機器擴展集自動調整計量圖表範例](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-1.png)

![虛擬機器擴展集自動調整計量圖表範例](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-2.png)

***圖 2-虛擬機器擴展集自動調整計量圖表範例***

在 [圖 2] 中，您可以看到兩個度量圖表。 

最上方的圖表會顯示**輸出流量**計量的實際值。 實際的值為6。 

底部的圖表會顯示幾個值。 
 - **觀察到**的計量值（淺藍色）是3，因為有2個作用中的實例，而6個除以2則是3。 
 - 觀察到的**容量**（紫色）顯示自動調整引擎看到的實例計數。 
 - [計量**閾值**（淺綠色）] 設定為10。 

如果有多個調整動作規則，您可以使用 [計量瀏覽器] 圖表中的 [分割] 或 [**新增篩選**] 選項，依特定的來源或規則查看度量。 如需分割計量圖表的詳細資訊，請參閱度量圖表的[先進功能-分割](metrics-charts.md#apply-splitting-to-a-chart)

## <a name="example-3---understanding-autoscale-events"></a>範例 3-瞭解自動調整事件

在 [自動調整規模設定] 畫面中，移至 [**執行歷程記錄**] 索引標籤，以查看最新的縮放動作。 此索引標籤也會顯示經過一段時間後，**觀察到的產能**變更。 若要尋找所有自動調整動作的詳細資料，包括像是更新/刪除自動調整設定等作業，請查看活動記錄，並依自動調整作業進行篩選。

![自動調整設定執行歷程記錄](media/autoscale-troubleshoot/autoscale-setting-run-history-smaller.png)

## <a name="autoscale-resource-logs"></a>自動調整資源記錄

與任何其他 Azure 資源相同，自動調整服務會提供[資源記錄](platform-logs-overview.md)。 有兩種記錄類別。

- **自動調整評估**-自動調整引擎會在每次進行檢查時，記錄每個單一條件評估的記錄專案。  此專案包含計量之觀察值的詳細資料、評估的規則，以及評估是否導致調整動作。

- **自動調整規模動作**-引擎會記錄自動調整服務所起始的規模動作事件，以及這些調整動作的結果（成功、失敗，以及自動調整服務所看到的相應增加大小）。

如同任何 Azure 監視器支援的服務，您可以使用[診斷設定](diagnostic-settings.md)來路由傳送這些記錄：

- 至您的 Log Analytics 工作區以進行詳細分析
- 事件中樞，然後再到非 Azure 工具
- 至您的 Azure 儲存體帳戶進行封存  

![自動調整診斷設定](media/autoscale-troubleshoot/diagnostic-settings.png)

上圖顯示 Azure 入口網站自動調整的診斷設定。 您可以在這裡選取 [診斷/資源記錄] 索引標籤，並啟用記錄收集和路由。 您也可以使用 REST API、CLI、PowerShell Resource Manager 範本來執行相同的動作，方法是選擇 [資源類型] 做為 [ *Microsoft Insights/AutoscaleSettings*]。 

## <a name="troubleshooting-using-autoscale-logs"></a>使用自動調整記錄進行疑難排解 

若要獲得最佳的疑難排解體驗，建議您在建立自動調整設定時，透過工作區將記錄路由至 Azure 監視器記錄（Log Analytics）。 此程式會顯示在上一節的圖片中。 您可以使用 Log Analytics，以更好的程度驗證評估和調整動作。

當您設定自動調整記錄傳送到 Log Analytics 工作區之後，您可以執行下列查詢來檢查記錄。 

若要開始使用，請嘗試此查詢來查看最新的自動調整評估記錄：

```Kusto
AutoscaleEvaluationsLog
| limit 50
```

或嘗試下列查詢來查看最新的調整動作記錄：

```Kusto
AutoscaleScaleActionsLog
| limit 50
```

請使用下列各節來解決這些問題。 

## <a name="a-scale-action-occurred-that-i-didnt-expect"></a>發生未預期的調整動作

首先執行 [調整規模的查詢] 動作，以尋找您感興趣的調整動作。 如果它是最新的調整動作，請使用下列查詢：

```Kusto
AutoscaleScaleActionsLog
| take 1
```

從 [調整動作] 記錄中選取 [CorrelationId] 欄位。 使用 CorrelationId 來尋找正確的評估記錄。 執行下列查詢將會顯示所有評估為該調整動作的規則和條件。

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId>"
```

## <a name="what-profile-caused-a-scale-action"></a>哪一個設定檔造成調整動作？

發生調整的動作，但您有重迭的規則和設定檔，而且需要追蹤造成動作的問題。 

尋找調整動作的 correlationId （如範例1中所述），然後對評估記錄執行查詢，以深入瞭解設定檔。

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId_Guid>"
| where ProfileSelected == true
| project ProfileEvaluationTime, Profile, ProfileSelected, EvaluationResult
```

您也可以使用下列查詢，更進一步瞭解整個設定檔評估

```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName contains == "profileEvaluation"
| project OperationName, Profile, ProfileEvaluationTime, ProfileSelected, EvaluationResult
```

## <a name="a-scale-action-did-not-occur"></a>未發生調整動作

我預期調整規模動作並不會發生。 可能不會有任何調整動作事件或記錄。

如果您使用以度量為基礎的調整規則，請檢查自動調整計量。 觀察到的計量**值**或**觀察的容量**可能不是您預期的，因此不會引發調整規則。 您仍會看到評估，但不會顯示相應放大規則。 此外，冷卻時間也可能會保留調整動作。 

 在您預期調整動作發生的期間，檢查自動調整評估記錄。 檢查它所做的所有評估，以及為什麼它決定不觸發調整動作。


```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName == "MetricEvaluation" or OperationName == "ScaleRuleEvaluation"
| project OperationName, MetricData, ObservedValue, Threshold, EstimateScaleResult
```

## <a name="scale-action-failed"></a>調整動作失敗

在某些情況下，自動調整服務會採取調整動作，但系統決定不調整或無法完成調整動作。 使用此查詢來尋找失敗的調整動作。

```Kusto
AutoscaleScaleActionsLog
| where ResultType == "Failed"
| project ResultDescription
```

建立警示規則，以取得自動調整動作或失敗的通知。 您也可以建立警示規則，以取得自動調整事件的通知。

## <a name="schema-of-autoscale-resource-logs"></a>自動調整資源記錄的架構

如需詳細資訊，請參閱[自動調整資源記錄](autoscale-resource-log-schema.md)

## <a name="next-steps"></a>後續步驟
閱讀自動調整[最佳做法](autoscale-best-practices.md)的資訊。 

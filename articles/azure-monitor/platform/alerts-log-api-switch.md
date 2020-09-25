---
title: 升級至目前的 Azure 監視器記錄警示 API
description: 瞭解如何切換至記錄警示 ScheduledQueryRules API
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 05/30/2019
ms.subservice: alerts
ms.openlocfilehash: 868a8eb6cf38d471eb9dc1f47c903404d05ffc0c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91294507"
---
# <a name="upgrade-to-the-current-log-alerts-api-from-legacy-log-analytics-alert-api"></a>從舊版 Log Analytics 警示 API 升級至目前的記錄警示 API

> [!NOTE]
> 本文僅與 Azure 公用 (**不** 會 Azure Government 或 Azure 中國雲端) 相關。

> [!NOTE]
> 一旦使用者選擇切換至目前 [SCHEDULEDQUERYRULES API](/rest/api/monitor/scheduledqueryrules) 的喜好設定，就無法還原回舊版的 [舊版 LOG Analytics 警示 API](api-alerts.md)。

在過去，使用者使用 [舊版 Log Analytics 警示 API](api-alerts.md) 來記錄管理警示規則。 目前的工作區使用 [SCHEDULEDQUERYRULES API](/rest/api/monitor/scheduledqueryrules)。 本文說明從舊版 API 切換至目前 API 的優點和程式。

## <a name="benefits"></a>優點

- 建立警示規則的單一範本 (先前需要三個不同的範本) 。
- 適用于 Log Analytics 工作區或 Application Insights 資源的單一 API。
- [PowerShell Cmdlet 支援](alerts-log.md#managing-log-alerts-using-powershell)。
- 嚴重性與所有其他警示類型的對齊方式。
- 能夠建立跨多個外部資源（例如 Log Analytics 工作區或 Application Insights 資源）的 [跨工作區記錄警示](../log-query/cross-workspace-query.md) 。
- 使用者可以使用 [匯總依據] 參數來指定要分割警示的維度。
- 記錄警示的最多可有兩天的資料 (之前僅限一天) 。

## <a name="impact"></a>影響

- 所有新規則都必須使用目前的 API 來建立/編輯。 請參閱透過 [Azure 資源範本使用範例](alerts-log-create-templates.md) 和透過 [PowerShell 的範例使用](alerts-log.md#managing-log-alerts-using-powershell)。
- 當規則變成目前 API 中 Azure Resource Manager 追蹤的資源，且必須是唯一的，規則資源識別碼將會變更為此結構： `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` 。 警示規則的顯示名稱將保持不變。

## <a name="process"></a>Process

切換的程式並不是互動式程式，在大部分情況下都不需要手動步驟。 在切換期間或之後，您的警示規則不會停止或停止。
進行此呼叫，以切換與特定 Log Analytics 工作區相關聯的所有警示規則：

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

使用包含下列 JSON 的要求主體：

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

以下是使用 [ARMClient](https://github.com/projectkudu/ARMClient)的開放原始碼命令列工具，可簡化叫用上述 API 呼叫的範例：

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

如果參數成功，回應會是：

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

## <a name="check-switching-status-of-workspace"></a>檢查工作區的切換狀態

您也可以使用此 API 呼叫來檢查切換狀態：

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

您也可以使用 [ARMClient](https://github.com/projectkudu/ARMClient) 工具：

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

如果 Log Analytics 工作區已切換至 [SCHEDULEDQUERYRULES API](/rest/api/monitor/scheduledqueryrules)，則回應為：

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
如果未切換 Log Analytics 工作區，回應為：

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>後續步驟

- 了解 [Azure 監視器 - 記錄警示](alerts-unified-log.md)。
- 瞭解如何 [使用 API 管理您的記錄警示](alerts-log-create-templates.md)。
- 瞭解如何 [使用 PowerShell 記錄管理警示](alerts-log.md#managing-log-alerts-using-powershell)。
- 深入了解 [Azure 警示體驗](./alerts-overview.md)。

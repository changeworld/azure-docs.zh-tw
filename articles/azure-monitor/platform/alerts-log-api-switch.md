---
title: 從舊版 Log Analytics 警示 API 切換至新的 Azure 警示 API
description: 基於舊版保存的日誌分析警報 API 和將警報規則切換到新的計畫查詢規則 API 的過程概述，其中詳細介紹了常見的客戶問題。
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 05/30/2019
ms.subservice: alerts
ms.openlocfilehash: c1ca85ba9c79f828b5ddcf0d640cfe7f8b6e097c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249434"
---
# <a name="switch-api-preference-for-log-alerts"></a>切換記錄警示的 API 喜好設定

> [!NOTE]
> 聲明的內容僅適用于使用者 Azure 公共雲，**不適用於**Azure 政府或 Azure 中國雲。  

> [!NOTE]
> 一旦使用者選擇將首選項切換到新的[計畫查詢規則 API，](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)就不可能恢復為使用舊[版舊日誌分析警報 API](api-alerts.md)。

直到最近，您都還是在 Microsoft Operations Management Suite 入口網站中管理警示規則。 新的警示體驗已與 Microsoft Azure 中的各種服務 (包括 Log Analytics) 整合，我們要求[您將警示規則從 OMS 入口網站延伸至 Azure](alerts-extend.md)。 但為了確保能盡可能縮短客戶的中斷時間，此過程並未改變其供使用的程式設計介面 - 以 SavedSearch 為基礎的 [Log Analytics 警示 API](api-alerts.md)。

不過，您現在為 Log Analytics 警示使用者發佈了真正的 Azure 程式設計替代工具 - [Azure 監視器 - ScheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)；這也會反映在您的 [Azure 計費 - 記錄警示](alerts-unified-log.md#pricing-and-billing-of-log-alerts)中。 要瞭解有關如何使用 API 管理日誌警報的更多資訊，請參閱使用 Azure[資源範本管理日誌警報](alerts-log.md#managing-log-alerts-using-azure-resource-template)[，並使用 PowerShell 管理日誌警報](alerts-log.md#managing-log-alerts-using-powershell)。

## <a name="benefits-of-switching-to-new-azure-api"></a>切換至新 Azure API 的好處

使用 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) 來建立和管理警示有幾項[舊版 Log Analytics 警示 API](api-alerts.md) 無法提供的好處；以下列出部分最主要的優點：

- 能夠在警示規則中[跨工作區進行記錄搜尋](../log-query/cross-workspace-query.md)，並且可延伸到外部資源，例如 Log Analytics 工作區甚或 Application Insights 應用程式
- 使用多個欄位在查詢中進行分組時，使用者可利用 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) 來指定要在 Azure 入口網站中彙總哪些欄位
- 記錄警示若是使用 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) 來建立的，可定義的期間最長為 48 小時，且擷取資料的期間比以前長
- 在同一作業中將警示規則建立為單一資源，而不需要像[舊版 Log Analytics 警示 API](api-alerts.md) 一樣建立三個層級的資源
- 在 Azure 中各種以查詢為基礎的記錄警示全都使用同一個程式設計介面 - 新的 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) 可用來管理 Log Analytics 和 Application Insights 的規則
- 使用[Powershell Cmdlet](alerts-log.md#managing-log-alerts-using-powershell)管理日誌警報
- 所有新的記錄警示功能和未來的開發都將只能透過新的 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) 來使用和進行

## <a name="process-of-switching-from-legacy-log-alerts-api"></a>從舊版記錄警示 API 切換的程序

使用者可以自行選擇使用[舊版 Log Analytics 警示 API](api-alerts.md) 或新的 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)。 無論以哪個 API 建立的警示規則，都將*只能以相同的 API 來管理* - 以及從 Azure 入口網站來管理。 預設情況下，Azure 監視器將繼續使用[舊日誌分析警報 API](api-alerts.md)從 Azure 門戶為日誌分析的現有工作區創建任何新的警報規則。 正如[在 2019 年 6 月 1 日或之後創建的新 Log 工作區所宣佈](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/)的那樣，預設情況下（包括在 Azure 門戶中）將自動使用新的[計畫查詢規則 API。](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

喜好設定切換至 scheduledQueryRules API 的影響如下所述：

- 為了透過程式設計介面來管理記錄警示所做的所有互動，現在都必須改用 [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) 來完成。 有關詳細資訊，請參閱[通過 Azure 資源範本進行示例使用](alerts-log.md#managing-log-alerts-using-azure-resource-template)，[並通過 PowerShell 進行示例使用](alerts-log.md#managing-log-alerts-using-powershell)
- 任何在 Azure 入口網站中建立的新記錄警示規則，都將使用 [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) 來建立，並且也允許使用者透過 Azure 入口網站使用[新 API 的其他功能](#benefits-of-switching-to-new-azure-api)
- 日誌警報規則的嚴重性將從：*嚴重、警告&資訊"* 轉移到*嚴重性值 0、1 & 2*。 以及創建/更新嚴重性 3 和 4 的警報規則的選項。

從[舊版 Log Analytics 警示 API](api-alerts.md) 移轉警示規則的程序，不包含以任何方式變更您的警示定義、查詢或設定。 您的警報規則和監視不受影響，警報不會在切換期間或之後停止或停止。 唯一的更改是：

- 更改 API 首選項並通過新 API 訪問規則。
- 已修改的警報規則資源 URI，其中包含[舊日誌分析警報 API](api-alerts.md)中使用的 ED，而不是此結構`<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>`中的警報規則名稱。 警報規則的顯示名稱將保持不變。

任何自願切換至新的 [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)，並停止使用[舊版 Log Analytics 警示 API](api-alerts.md) 的客戶，都可在下列 API 執行 PUT 呼叫，以切換所有與特定 Log Analytics 工作區相關聯的警示規則，而達到此目的。

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

使用包含下列 JSON 的要求本文。

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

存取此 API 時，也可以從 PowerShell 命令列使用 [ARMClient](https://github.com/projectkudu/ARMClient) (一個可簡化 Azure Resource Manager API 叫用流程的開放原始碼命令列工具) 來存取。 如下所示，在範例 PUT 呼叫中，是使用 ARMclient 工具來切換與特定 Log Analytics 工作區相關聯的所有警示規則。

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

如果 Log Analytics 工作區中的所有警示規則成功切換為使用新的 [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)，系統將提供下列回應。

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

使用者也可以查看 Log Analytics 工作區的目前狀態，並確認工作區是否已切換為僅使用 [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)。 若要加以確認，使用者可以在下列 API 執行 GET 呼叫。

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

若要使用 PowerShell 命令列以 [ARMClient](https://github.com/projectkudu/ARMClient) 工具執行上述程式碼，請參閱下方範例。

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

如果指定的 Log Analytics 工作區已切換為僅使用 [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)，回應 JSON 將如下所示。

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
反之，如果指定的 Log Analytics 工作區尚未切換為僅使用 [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)，回應 JSON 將如下所示。

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>後續步驟

- 了解 [Azure 監視器 - 記錄警示](alerts-unified-log.md)。
- 了解如何建立 [Azure 警示中的記錄警示](alerts-log.md)。
- 深入了解 [Azure 警示體驗](../../azure-monitor/platform/alerts-overview.md)。

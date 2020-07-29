---
title: 切換至新的 Azure 警示 API
description: 概述以 savedSearch 為基礎的舊版 Log Analytics 警示 API，以及將警示規則切換至新 ScheduledQueryRules API 的程式，其中詳細說明如何解決常見的客戶考慮。
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 05/30/2019
ms.subservice: alerts
ms.openlocfilehash: 7e1073817013d45558a9679a4f70db0c002cfaa9
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87324075"
---
# <a name="switch-api-preference-for-log-alerts"></a>切換記錄警示的 API 喜好設定

> [!NOTE]
> 僅適用于使用者 Azure 公用雲端，而**不適**用於 Azure Government 或 Azure 中國雲端的內容。  

> [!NOTE]
> 使用者選擇將喜好設定切換至新的[SCHEDULEDQUERYRULES API](/rest/api/monitor/scheduledqueryrules)之後，就無法使用舊版的[舊版 LOG Analytics 警示 API](api-alerts.md)來還原為。

直到最近，您都還是在 Microsoft Operations Management Suite 入口網站中管理警示規則。 新的警示體驗已與 Microsoft Azure 中的各種服務 (包括 Log Analytics) 整合，我們要求[您將警示規則從 OMS 入口網站延伸至 Azure](./alerts-unified-log.md)。 但為了確保能盡可能縮短客戶的中斷時間，此過程並未改變其供使用的程式設計介面 - 以 SavedSearch 為基礎的 [Log Analytics 警示 API](api-alerts.md)。

不過，您現在為 Log Analytics 警示使用者發佈了真正的 Azure 程式設計替代工具 - [Azure 監視器 - ScheduledQueryRules API](/rest/api/monitor/scheduledqueryrules)；這也會反映在您的 [Azure 計費 - 記錄警示](alerts-unified-log.md#pricing-and-billing-of-log-alerts)中。 若要深入瞭解如何使用 API 記錄管理警示，請參閱[使用 Azure 資源範本記錄管理警示](alerts-log.md#managing-log-alerts-using-azure-resource-template)和[使用 PowerShell 記錄管理警示](alerts-log.md#managing-log-alerts-using-powershell)。

## <a name="benefits-of-switching-to-new-azure-api"></a>切換至新 Azure API 的好處

使用 [scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules) 來建立和管理警示有幾項[舊版 Log Analytics 警示 API](api-alerts.md) 無法提供的好處；以下列出部分最主要的優點：

- 能夠在警示規則中[跨工作區進行記錄搜尋](../log-query/cross-workspace-query.md)，並且可延伸到外部資源，例如 Log Analytics 工作區甚或 Application Insights 應用程式
- 使用多個欄位在查詢中進行分組時，使用者可利用 [scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules) 來指定要在 Azure 入口網站中彙總哪些欄位
- 記錄警示若是使用 [scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules) 來建立的，可定義的期間最長為 48 小時，且擷取資料的期間比以前長
- 在同一作業中將警示規則建立為單一資源，而不需要像[舊版 Log Analytics 警示 API](api-alerts.md) 一樣建立三個層級的資源
- 在 Azure 中各種以查詢為基礎的記錄警示全都使用同一個程式設計介面 - 新的 [scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules) 可用來管理 Log Analytics 和 Application Insights 的規則
- 使用[Powershell Cmdlet](alerts-log.md#managing-log-alerts-using-powershell)管理您的記錄警示
- 所有新的記錄警示功能和未來的開發都將只能透過新的 [scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules) 來使用和進行

## <a name="process-of-switching-from-legacy-log-alerts-api"></a>從舊版記錄警示 API 切換的程序

使用者可以自行選擇使用[舊版 Log Analytics 警示 API](api-alerts.md) 或新的 [scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules)。 無論以哪個 API 建立的警示規則，都將*只能以相同的 API 來管理* - 以及從 Azure 入口網站來管理。 根據預設，Azure 監視器會繼續使用[舊版 Log Analytics 警示 API](api-alerts.md) ，針對 Log Analytics 現有工作區的 Azure 入口網站建立任何新的警示規則。 如在[2019 年6月1日或之後所建立的新記錄工作區所公告，](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/)預設會自動使用新的[scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules) ，包括 Azure 入口網站中的。

喜好設定切換至 scheduledQueryRules API 的影響如下所述：

- 為了透過程式設計介面來管理記錄警示所做的所有互動，現在都必須改用 [scheduledQueryRules](/rest/api/monitor/scheduledqueryrules) 來完成。 如需詳細資訊，請參閱透過[Azure 資源範本使用範例](alerts-log.md#managing-log-alerts-using-azure-resource-template)和透過[PowerShell 使用範例](alerts-log.md#managing-log-alerts-using-powershell)
- 任何在 Azure 入口網站中建立的新記錄警示規則，都將使用 [scheduledQueryRules](/rest/api/monitor/scheduledqueryrules) 來建立，並且也允許使用者透過 Azure 入口網站使用[新 API 的其他功能](#benefits-of-switching-to-new-azure-api)
- 記錄警示規則的嚴重性將從：*重大、警告 & 資訊*轉移到*嚴重性值0、1 & 2*。 以及建立/更新嚴重性3和4之警示規則的選項。

從[舊版 Log Analytics 警示 API](api-alerts.md) 移轉警示規則的程序，不包含以任何方式變更您的警示定義、查詢或設定。 您的警示規則和監視不會受到影響，而且在切換期間或之後，警示將不會停止或停用。 唯一的變更為：

- 變更 API 的喜好設定，以及透過新的 API 存取您的規則。
- 已修改的警示規則資源 URI，其中包含用於[舊版 Log Analytics 警示 API](api-alerts.md)的識別碼，而不是此結構中的警示規則名稱 `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` 。 警示規則的顯示名稱會維持不變。

任何自願切換至新的 [scheduledQueryRules](/rest/api/monitor/scheduledqueryrules)，並停止使用[舊版 Log Analytics 警示 API](api-alerts.md) 的客戶，都可在下列 API 執行 PUT 呼叫，以切換所有與特定 Log Analytics 工作區相關聯的警示規則，而達到此目的。

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

如果 Log Analytics 工作區中的所有警示規則成功切換為使用新的 [scheduledQueryRules](/rest/api/monitor/scheduledqueryrules)，系統將提供下列回應。

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

使用者也可以查看 Log Analytics 工作區的目前狀態，並確認工作區是否已切換為僅使用 [scheduledQueryRules](/rest/api/monitor/scheduledqueryrules)。 若要加以確認，使用者可以在下列 API 執行 GET 呼叫。

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

若要使用 PowerShell 命令列以 [ARMClient](https://github.com/projectkudu/ARMClient) 工具執行上述程式碼，請參閱下方範例。

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

如果指定的 Log Analytics 工作區已切換為僅使用 [scheduledQueryRules](/rest/api/monitor/scheduledqueryrules)，回應 JSON 將如下所示。

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
反之，如果指定的 Log Analytics 工作區尚未切換為僅使用 [scheduledQueryRules](/rest/api/monitor/scheduledqueryrules)，回應 JSON 將如下所示。

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>後續步驟

- 了解 [Azure 監視器 - 記錄警示](alerts-unified-log.md)。
- 了解如何建立 [Azure 警示中的記錄警示](alerts-log.md)。
- 深入了解 [Azure 警示體驗](./alerts-overview.md)。


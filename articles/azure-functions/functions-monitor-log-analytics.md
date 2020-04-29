---
title: 使用 Azure 監視器記錄監視 Azure Functions
description: 瞭解如何搭配 Azure Functions 使用 Azure 監視器記錄來監視函數執行。
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: cshoe
ms.openlocfilehash: 13c72a1cf8a0dd4a1124e51b9ceee04ae04bf261
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77649869"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>使用 Azure 監視器記錄監視 Azure Functions

Azure Functions 提供與[Azure 監視器記錄](../azure-monitor/platform/data-platform-logs.md)的整合，以監視功能。 本文說明如何設定 Azure Functions，以便將系統產生和使用者產生的記錄檔傳送至 Azure 監視器記錄。

Azure 監視器記錄可讓您將不同資源的記錄合併在同一個工作區中，以便利用[查詢](../azure-monitor/log-query/log-query-overview.md)進行分析，以快速取得、合併和分析所收集的資料。  您可以在 Azure 入口網站中使用 [Log Analytics](../azure-monitor/log-query/portals.md) 來建立和測試查詢，然後使用這些工具直接分析資料，或儲存查詢以便搭配[視覺效果](../azure-monitor/visualizations.md)或[警示規則](../azure-monitor/platform/alerts-overview.md)使用。

Azure 監視器使用 Azure 資料總管使用的 [Kusto 查詢語言](/azure/kusto/query/)版本，適合用於簡單的記錄查詢，但也包含進階的功能，例如彙總、聯結和智慧分析。 您可以使用[多個課程](../azure-monitor/log-query/get-started-queries.md)，快速了解查詢語言。

> [!NOTE]
> 在 Windows 耗用量、Premium 和專用主機方案上執行的函式應用程式，與 Azure 監視器記錄的整合目前為公開預覽狀態。

## <a name="setting-up"></a>設定

從 [**監視**] 區段中，選取 [**診斷設定**]，然後按一下 [**新增診斷設定**]。

![新增診斷設定](media/functions-monitor-log-analytics/diagnostic-settings-add.png)

在 [**診斷設定**] 頁面中，選擇 [**傳送至 log analytics**]，然後選取您的 log analytics 工作區。 在 [**記錄**] 下選擇**FunctionAppLogs**，此資料表包含所需的記錄檔。

![新增診斷設定](media/functions-monitor-log-analytics/choose-table.png)

## <a name="user-generated-logs"></a>使用者產生的記錄檔

若要產生自訂記錄檔，您可以使用特定的記錄語句，視您的語言而定，以下是範例程式碼片段：


# <a name="c"></a>[C#](#tab/csharp)

```csharp
log.LogInformation("My app logs here.");
```

# <a name="java"></a>[Java](#tab/java)

```java
context.getLogger().info("My app logs here.");
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
context.log('My app logs here.');
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Write-Host "My app logs here."
```

# <a name="python"></a>[Python](#tab/python)

```python
logging.info('My app logs here.')
```

---

## <a name="querying-the-logs"></a>查詢記錄

若要查詢產生的記錄，請移至您設定用來傳送函式記錄的 Log Analytics 工作區，然後按一下 [**記錄**]。

![LA 工作區中的查詢視窗](media/functions-monitor-log-analytics/querying.png)

Azure Functions 將所有記錄寫入至**FunctionAppLogs**資料表，以下是一些範例查詢。

### <a name="all-logs"></a>所有記錄

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="a-specific-function-logs"></a>特定的函數記錄

```

FunctionAppLogs
| where FunctionName == "<Function name>" 

```

### <a name="exceptions"></a>例外狀況

```

FunctionAppLogs
| where ExceptionDetails != ""  
| order by TimeGenerated asc

```

## <a name="next-steps"></a>後續步驟

- 回顧[Azure Functions 總覽](functions-overview.md)
- 深入瞭解[Azure 監視器記錄](../azure-monitor/platform/data-platform-logs.md)
- 深入瞭解[查詢語言](../azure-monitor/log-query/get-started-queries.md)。

---
title: 使用 Azure 監視器記錄監視 Azure Functions
description: 瞭解如何搭配 Azure Functions 使用 Azure 監視器記錄來監視函式執行。
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 51c611b2565ae0a5a054a45f0aedcb039351b46b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88208366"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>使用 Azure 監視器記錄監視 Azure Functions

Azure Functions 提供與 [Azure 監視器記錄](../azure-monitor/platform/data-platform-logs.md) 的整合來監視函式。 本文說明如何設定 Azure Functions，以將系統產生和使用者產生的記錄檔傳送至 Azure 監視器記錄檔。

Azure 監視器記錄檔可讓您從相同工作區中的不同資源合併記錄，在此可利用 [查詢](../azure-monitor/log-query/log-query-overview.md) 進行分析，以快速取得、合併和分析所收集的資料。  您可以在 Azure 入口網站中使用 [Log Analytics](../azure-monitor/log-query/log-query-overview.md) 來建立和測試查詢，然後使用這些工具直接分析資料，或儲存查詢以便搭配[視覺效果](../azure-monitor/visualizations.md)或[警示規則](../azure-monitor/platform/alerts-overview.md)使用。

Azure 監視器使用 Azure 資料總管使用的 [Kusto 查詢語言](/azure/kusto/query/)版本，適合用於簡單的記錄查詢，但也包含進階的功能，例如彙總、聯結和智慧分析。 您可以使用[多個課程](../azure-monitor/log-query/get-started-queries.md)，快速了解查詢語言。

> [!NOTE]
> 針對在 Windows 耗用量、高階和專用主機方案上執行的函式應用程式，與 Azure 監視器記錄檔的整合目前處於公開預覽狀態。

## <a name="setting-up"></a>設定

1. 從[Azure 入口網站](https://portal.azure.com)中函數應用程式的 [**監視**] 區段中，選取 [**診斷設定**]，然後選取 [**新增診斷設定**]。

   :::image type="content" source="media/functions-monitor-log-analytics/diagnostic-settings-add.png" alt-text="選取診斷設定":::

1. 在 [ **診斷設定** ] 頁面的 [ **類別目錄詳細資料** 和 **記錄**檔] 底下，選擇 [ **FunctionAppLogs**]。

   **FunctionAppLogs**資料表包含所需的記錄。

1. 在 [ **目的地詳細資料**] 底下，選擇 [ **傳送至 log analytics**]，然後選取您的 **log analytics 工作區**。 

1. 輸入 **診斷設定名稱**，然後選取 [ **儲存**]。

   :::image type="content" source="media/functions-monitor-log-analytics/choose-table.png" alt-text="選取診斷設定":::

## <a name="user-generated-logs"></a>使用者產生的記錄

若要產生自訂記錄檔，請使用您的語言特有的記錄語句。 以下是範例程式碼片段：


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

若要查詢產生的記錄：
 
1. 從您的函數應用程式選取 [ **診斷設定**]。 

1. 從 [ **診斷設定** ] 清單中，選取您設定來傳送函數記錄檔的 Log Analytics 工作區。 

1. 從 **Log Analytics 工作區** 頁面中，選取 [ **記錄**]。

   Azure Functions 會將所有記錄寫入**LogManagement**下的**FunctionAppLogs**資料表。 

   :::image type="content" source="media/functions-monitor-log-analytics/querying.png" alt-text="選取診斷設定":::

以下是一些範例查詢：

### <a name="all-logs"></a>所有記錄

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="specific-function-logs"></a>特定函數記錄

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

- 請參閱 [Azure Functions 總覽](functions-overview.md)。
- 深入瞭解 [Azure 監視器記錄](../azure-monitor/platform/data-platform-logs.md)。
- 深入了解[查詢語言](../azure-monitor/log-query/get-started-queries.md)。

---
title: 使用 Azure 監視日誌監視 Azure 函數
description: 瞭解如何將 Azure 監視器日誌與 Azure 函數一起監視函數執行。
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: cshoe
ms.openlocfilehash: 13c72a1cf8a0dd4a1124e51b9ceee04ae04bf261
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649869"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>使用 Azure 監視日誌監視 Azure 函數

Azure 函數提供與[Azure 監視器日誌](../azure-monitor/platform/data-platform-logs.md)的集成，以監視函數。 本文介紹如何配置 Azure 函數，將系統生成的和使用者生成的日誌發送到 Azure 監視器日誌。

Azure 監視器日誌使您能夠在同一工作區中合併來自不同資源的日誌，通過[查詢](../azure-monitor/log-query/log-query-overview.md)對其進行分析，以快速檢索、合併和分析收集的資料。  您可以在 Azure 入口網站中使用 [Log Analytics](../azure-monitor/log-query/portals.md) 來建立和測試查詢，然後使用這些工具直接分析資料，或儲存查詢以便搭配[視覺效果](../azure-monitor/visualizations.md)或[警示規則](../azure-monitor/platform/alerts-overview.md)使用。

Azure 監視器使用 Azure 資料總管使用的 [Kusto 查詢語言](/azure/kusto/query/)版本，適合用於簡單的記錄查詢，但也包含進階的功能，例如彙總、聯結和智慧分析。 您可以使用[多個課程](../azure-monitor/log-query/get-started-queries.md)，快速了解查詢語言。

> [!NOTE]
> 與 Azure 監視器日誌的集成當前處於在 Windows 消費、高級和專用託管計畫上運行的功能應用的公共預覽版中。

## <a name="setting-up"></a>設定

在 **"監視**"部分中，選擇 **"診斷設置**"，然後按一下"**添加診斷設置**"。

![添加診斷設置](media/functions-monitor-log-analytics/diagnostic-settings-add.png)

在 **"診斷設置"** 頁中，選擇 **"發送到日誌分析**"，然後選擇日誌分析工作區。 在**日誌**下選擇**函數AppLogs，** 此表包含所需的日誌。

![添加診斷設置](media/functions-monitor-log-analytics/choose-table.png)

## <a name="user-generated-logs"></a>使用者生成的日誌

要生成自訂日誌，可以使用特定的日誌記錄語句，具體取決於您的語言，下面是示例程式碼片段：


# <a name="c"></a>[C#](#tab/csharp)

```csharp
log.LogInformation("My app logs here.");
```

# <a name="java"></a>[JAVA](#tab/java)

```java
context.getLogger().info("My app logs here.");
```

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

```javascript
context.log('My app logs here.');
```

# <a name="powershell"></a>[電源外殼](#tab/powershell)

```powershell
Write-Host "My app logs here."
```

# <a name="python"></a>[Python](#tab/python)

```python
logging.info('My app logs here.')
```

---

## <a name="querying-the-logs"></a>查詢日誌

要查詢生成的日誌，請轉到配置為將函數日誌發送到的日誌分析工作區，然後按一下 **"日誌**"。

![洛杉磯工作區中的查詢視窗](media/functions-monitor-log-analytics/querying.png)

Azure 函數將所有日誌寫入**函數AppLogs**表，下面是一些依例查詢。

### <a name="all-logs"></a>所有日誌

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="a-specific-function-logs"></a>特定函數日誌

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

- 查看[Azure 函數概述](functions-overview.md)
- 瞭解有關[Azure 監視器日誌的更多資訊](../azure-monitor/platform/data-platform-logs.md)
- 瞭解有關[查詢語言](../azure-monitor/log-query/get-started-queries.md)的更多詳細資訊。

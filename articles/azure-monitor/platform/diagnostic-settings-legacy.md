---
title: 使用診斷設置收集 Azure 活動日誌 - Azure 監視器 |微軟文檔
description: 使用診斷設置將 Azure 活動日誌轉發到 Azure 監視器日誌、Azure 存儲或 Azure 事件中心。
author: bwren
ms.subservice: logs
ms.topic: conceptual
ms.author: bwren
ms.date: 02/04/2020
ms.openlocfilehash: 6d4c724c7cfb4c1779f0fc6592a7e61e060755b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096903"
---
# <a name="update-to-azure-activity-log-collection-and-export"></a>更新到 Azure 活動日誌集合和匯出
[Azure 活動日誌](platform-logs-overview.md)是一個[平臺日誌](platform-logs-overview.md)，用於深入瞭解 Azure 中發生的訂閱級事件。 將活動日誌條目發送到[事件中心或存儲帳戶](activity-log-export.md)或[日誌分析工作區](activity-log-collect.md)的方法已更改為使用[診斷設置](diagnostic-settings.md)。 本文介紹了方法之間的區別以及如何清除舊版設置，以準備更改為診斷設置。


## <a name="differences-between-methods"></a>方法之間的差異

### <a name="advantages"></a>優點
與當前方法不同，使用診斷設置具有以下優點：

- 收集所有平臺日誌的一致方法。
- 跨多個訂閱和租戶收集活動日誌。
- 篩選集合以僅收集特定類別的日誌。
- 收集所有活動日誌類別。 某些類別不會使用舊方法收集。
- 日誌引入的延遲更快。 前一種方法的延遲約為 15 分鐘，而診斷設置僅添加約 1 分鐘。

### <a name="considerations"></a>考量
在啟用此功能之前，請考慮使用診斷設置收集活動日誌的以下詳細資訊。

- 將活動日誌收集到 Azure 存儲的保留設置已被刪除，這意味著資料將無限期存儲，直到您刪除它。
- 目前，您只能使用 Azure 門戶創建訂閱級別的診斷設置。 要使用其他方法（如 PowerShell 或 CLI），可以創建資源管理器範本。


### <a name="differences-in-data"></a>資料差異
診斷設置收集的資料與以前用於收集活動日誌的方法相同的資料，但當前存在差異：

已刪除以下列。 這些列的替換採用不同的格式，因此您可能需要修改使用它們的日誌查詢。 您仍然可以在架構中看到已刪除的列，但它們不會被資料填充。

| 刪除的列 | 替換列 |
|:---|:---|
| 活動狀態    | ActivityStatusValue    |
| 活動子級狀態 | 活動子狀態值 |
| OperationName     | 操作名稱值     |
| ResourceProvider  | 資源提供者價值  |

已添加以下列：

- Authorization_d
- Claims_d
- Properties_d

> [!IMPORTANT]
> 在某些情況下，這些列中的值可能在所有大寫中。 如果查詢包含這些列，則應使用[* 運算子](https://docs.microsoft.com/azure/kusto/query/datatypes-string-operators)執行不區分大小寫的比較。

## <a name="work-with-legacy-settings"></a>使用舊版設置
如果不選擇使用診斷設置替換，則用於收集活動日誌的舊設置將繼續工作。 使用以下方法管理訂閱的日誌設定檔。

1. 從 Azure 門戶中的**Azure 監視器**功能表中，選擇**活動日誌**。
3. 按一下 **"診斷設置**"。

   ![診斷設定](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. 按一下紫色橫幅即可獲得舊體驗。

    ![傳統體驗](media/diagnostic-settings-subscription/legacy-experience.png)


有關使用舊集合方法的詳細資訊，請參閱以下文章。

- [在 Azure 監視器中的日誌分析工作區中收集和分析 Azure 活動日誌](activity-log-collect.md)
- [跨 Azure 活動目錄租戶將 Azure 活動日誌收集到 Azure 監視器中](activity-log-collect-tenants.md)
- [將 Azure 活動日誌匯出到存儲或 Azure 事件中心](activity-log-export.md)

## <a name="disable-existing-settings"></a>禁用現有設置
在使用診斷設置啟用活動之前，應禁用活動的現有集合。 同時啟用這兩個功能可能會導致資料重複。

### <a name="disable-collection-into-log-analytics-workspace"></a>禁用日誌分析工作區中的集合

1. 在 Azure 門戶中打開**日誌分析工作區**功能表，然後選擇工作區以收集活動日誌。
2. 在工作區功能表的**工作區資料來源**部分中，選擇**Azure 活動日誌**。
3. 按一下要斷開連接的訂閱。
4. 當被要求確認您的選擇時，按一下 **"斷開連接****"，** 然後按一下"是"。

### <a name="disable-log-profile"></a>禁用日誌設定檔

1. 使用[舊版設置](#work-with-legacy-settings)中描述的過程打開舊版設置。
2. 禁用存儲或事件中心的任何當前集合。



## <a name="activity-log-monitoring-solution"></a>活動日誌監視解決方案
Azure 日誌分析監視解決方案包括多個日誌查詢和視圖，用於分析日誌分析工作區中的活動日誌記錄。 此解決方案使用日誌分析工作區中收集的日誌資料，如果您使用診斷設置收集活動日誌，則將繼續工作，無需進行任何更改。 有關此解決方案的詳細資訊，請參閱[活動日誌分析監視解決方案](activity-log-collect.md#activity-logs-analytics-monitoring-solution)。

## <a name="next-steps"></a>後續步驟

* [瞭解有關活動日誌的更多內容](../../azure-resource-manager/management/view-activity-logs.md)
* [瞭解有關診斷設置的更多資訊](diagnostic-settings.md)

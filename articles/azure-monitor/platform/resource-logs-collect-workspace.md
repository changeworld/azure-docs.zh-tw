---
title: 在 Log Analytics 工作區中收集 Azure 資源記錄
description: 瞭解如何將 Azure 資源日誌資料流到 Azure 監視器中的日誌分析工作區。
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 36bd464624118b7671a3879bcc1d34114bba9ce3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248589"
---
# <a name="collect-azure-platform-logs-in-log-analytics-workspace-in-azure-monitor"></a>在 Azure 監視器中的日誌分析工作區中收集 Azure 平臺日誌
Azure 中[的平臺日誌](platform-logs-overview.md)（包括 Azure 活動日誌和資源日誌）為 Azure 資源和它們所依賴的 Azure 平臺提供了詳細的診斷和審核資訊。 本文介紹在日誌分析工作區中收集資源日誌，允許您使用強大的日誌查詢使用 Azure 監視器日誌中收集的其他監視資料進行分析，並利用其他 Azure 監視器功能（如警報和可視 化。 


## <a name="what-you-can-do-with-platform-logs-in-a-workspace"></a>在工作區中使用平臺日誌可以執行哪些操作
將平臺日誌收集到日誌分析工作區中，可以一起分析所有 Azure 資源的日誌，並利用[Azure 監視器日誌](data-platform-logs.md)提供的所有功能，其中包括：

* **日誌查詢**- 使用強大的查詢語言創建[日誌查詢](../log-query/log-query-overview.md)，以快速分析和獲取對診斷資料的見解，並使用 Azure 監視器中從其他來源收集的資料對其進行分析。
* **警報**- 使用[Azure 監視器 中的日誌警報](alerts-log.md)，主動通知資源日誌中標識的關鍵條件和模式。
* **視覺化**- 將日誌查詢的結果固定到 Azure 儀表板，或將其作為互動式報表的一部分包含在活頁簿中。

## <a name="prerequisites"></a>Prerequisites
如果還沒有[一個新的工作區，則需要創建新工作區](../learn/quick-create-workspace.md)。 只要配置該設置的使用者對兩個訂閱具有適當的 RBAC 存取權限，工作區就不必與資源發送日誌位於同一訂閱中。

## <a name="create-a-diagnostic-setting"></a>建立診斷設定
通過為 Azure 資源創建診斷設置，將平臺日誌發送到日誌分析工作區和其他目標。 有關詳細資訊[，請參閱創建診斷設置以在 Azure 中收集日誌和指標](diagnostic-settings.md)。


## <a name="activity-log-collection"></a>活動日誌集合
您可以將活動日誌從任何單個訂閱發送到最多五個日誌分析工作區。 日誌分析工作區中收集的資源日誌資料存儲在**AzureActivity**表中。 

## <a name="resource-log-collection-mode"></a>資源日誌收集模式
在日誌分析工作區中收集的資源日誌資料存儲在表中，如 Azure[監視器日誌結構](../log-query/logs-structure.md)中所述。 資源日誌使用的表取決於資源正在使用的集合類型：

- Azure 診斷 - 寫入的所有資料都寫入_Azure 診斷_表。
- 特定于資源 - 資料將寫入資源的每個類別的單個表。

### <a name="azure-diagnostics-mode"></a>Azure 診斷模式 
在此模式下，任何[診斷設置](diagnostic-settings.md)中的所有資料都將在_Azure 診斷_表中收集。 這是大多數 Azure 服務目前使用的舊方法。

由於多個資源類型將資料發送到同一表，因此其架構是所收集的所有不同資料類型的架構的超集合。

請考慮以下示例，其中診斷設置正在相同的工作區中收集以下資料類型：

- 審核服務 1 的日誌（具有由 A 列、B 列和 C 組成的架構）  
- 服務 1 的錯誤日誌（具有由 D 列、E 列和 F 組成的架構）  
- 審核服務 2 的日誌（具有由列 G、H 和 I 組成的架構）  

Azure 診斷表如下所示：  

| ResourceProvider    | 類別     | A  | B  | C  | D  | E  | F  | G  | H  | I  |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| 微軟.服務1 | AuditLogs    | x1 | y1 | z1 |    |    |    |    |    |    |
| 微軟.服務1 | 錯誤日誌    |    |    |    | q1 | w1 | e1 |    |    |    |
| 微軟.服務2 | AuditLogs    |    |    |    |    |    |    | j1 | k1 | l1 |
| 微軟.服務1 | 錯誤日誌    |    |    |    | q2 | w2 | e2 |    |    |    |
| 微軟.服務2 | AuditLogs    |    |    |    |    |    |    | j3 | k3 | l3 |
| 微軟.服務1 | AuditLogs    | x5 | y5 | z5 |    |    |    |    |    |    |
| ... |

### <a name="resource-specific"></a>特定于資源
在此模式下，為診斷設置中選擇的每個類別創建所選工作區中的單個表。 建議使用此方法，因為它使處理日誌查詢中的資料變得更加容易，提供了更好的架構及其結構的可發現性，提高了在引入延遲和查詢時間方面的性能，以及授予 RBAC 許可權的能力。特定表。 所有 Azure 服務最終將遷移到特定于資源的模式。 

上面的示例將導致創建三個表：
 
- 表*服務1稽核記錄*如下：

    | 資源提供者 | 類別 | A | B | C |
    | -- | -- | -- | -- | -- |
    | Service1 | AuditLogs | x1 | y1 | z1 |
    | Service1 | AuditLogs | x5 | y5 | z5 |
    | ... |

- 表*服務1錯誤日誌*如下：  

    | 資源提供者 | 類別 | D | E | F |
    | -- | -- | -- | -- | -- | 
    | Service1 | 錯誤日誌 |  q1 | w1 | e1 |
    | Service1 | 錯誤日誌 |  q2 | w2 | e2 |
    | ... |

- 表*Service2 稽核記錄*，如下所示：  

    | 資源提供者 | 類別 | G | H | I |
    | -- | -- | -- | -- | -- |
    | 服務2 | AuditLogs | j1 | k1 | l1|
    | 服務2 | AuditLogs | j3 | k3 | l3|
    | ... |



### <a name="select-the-collection-mode"></a>選擇收集模式
大多數 Azure 資源都將以**Azure 診斷**或**特定于資源模式**將資料寫入工作區，而無需為您提供選擇。 有關使用哪種模式的詳細資訊，請參閱[每個服務的文檔](diagnostic-logs-schema.md)。 所有 Azure 服務最終將使用特定于資源的模式。 作為此轉換的一部分，某些資源將允許您在診斷設置中選擇模式。 您應該為任何新的診斷設置指定特定于資源的模式，因為這會使資料更易於管理，並可以説明您避免以後的複雜遷移。
  
   ![診斷設置模式選擇器](media/resource-logs-collect-workspace/diagnostic-settings-mode-selector.png)




> [!NOTE]
> 目前，只能在 Azure 門戶中配置診斷設置時選擇**Azure 診斷**和**特定于資源的**模式。 如果使用 CLI、PowerShell 或 Rest API 配置設置，它將預設為**Azure 診斷**。

您可以將現有診斷設置修改為特定于資源的模式。 在這種情況下，已收集的資料將保留在_Azure 診斷_表中，直到根據工作區的保留設置將其刪除。 新資料將收集到專用表中。 使用[聯合](https://docs.microsoft.com/azure/kusto/query/unionoperator)運算子查詢兩個表上的資料。

繼續觀看[Azure 更新](https://azure.microsoft.com/updates/)博客，瞭解有關支援特定于資源的 Azure 服務的公告。

### <a name="column-limit-in-azurediagnostics"></a>Azure 診斷中的列限制
Azure 監視器日誌中的任何表都有 500 個屬性限制。 達到此限制後，包含具有前 500 之外任何屬性的資料的任何行都將在引入時刪除。 *Azure 診斷*表特別容易受到此限制的影響，因為它包含寫入它的所有 Azure 服務的屬性。

如果要從多個服務收集資源日誌 _，Azure 診斷_可能會超過此限制，並且將錯過資料。 在所有 Azure 服務都支援特定于資源的模式之前，應配置資源以寫入多個工作區，以減少達到 500 列限制的可能性。

### <a name="azure-data-factory"></a>Azure Data Factory
Azure 資料工廠由於一組非常詳細的日誌，因此已知可以編寫大量列並可能導致_Azure 診斷_超過其限制的服務。 對於啟用特定于資源模式之前配置的任何診斷設置，將針對任何活動為每個唯一命名的使用者參數創建新列。 由於活動輸入和輸出的詳細性質，將創建更多列。
 
應儘快遷移日誌以使用特定于資源的模式。 如果無法立即執行此操作，臨時替代方法是將 Azure 資料工廠日誌隔離到其自己的工作區中，以儘量減少這些日誌影響工作區中收集的其他日誌類型的可能性。


## <a name="next-steps"></a>後續步驟

* [閱讀有關資源日誌的更多內容](platform-logs-overview.md)。
* [創建診斷設置以在 Azure 中收集日誌和指標](diagnostic-settings.md)。

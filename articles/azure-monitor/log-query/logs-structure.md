---
title: Azure 監視器日誌的結構 |微軟文檔
description: 您需要記錄查詢來從 Azure 監視器擷取記錄資料。  本文描述新的記錄查詢在 Azure 監視器中的使用方式，並且提供在建立之前需要了解的概念。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/22/2019
ms.openlocfilehash: 1d647ba7e8d4f0e29252dfff95099e39bab87895
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662071"
---
# <a name="structure-of-azure-monitor-logs"></a>Azure 監視器日誌的結構
使用[日誌查詢](log-query-overview.md)快速獲取資料見解的能力是 Azure 監視器的強大功能。 要創建高效且有用的查詢，應瞭解一些基本概念，例如所需資料的位置及其結構方式。 本文提供了入門所需的基本概念。

## <a name="overview"></a>總覽
Azure 監視器日誌中的資料存儲在日誌分析工作區或應用程式見解應用程式中。 兩者都由[Azure 資料資源管理器](/azure/data-explorer/)提供支援，這意味著它們利用其強大的資料引擎和查詢語言。

工作區和應用程式中的資料都組織到表中，每個表存儲不同類型的資料，並具有自己獨特的屬性集。 大多數[資料來源](../platform/data-sources.md)將寫入日誌分析工作區中的自己的表，而應用程式見解將寫入應用程式見解應用程式中的預定義表集。 日誌查詢非常靈活，允許您輕鬆組合來自多個表的資料，甚至使用跨資源查詢合併多個工作區中表中的資料，或編寫組合工作區和應用程式資料的查詢。

下圖顯示了寫入依例查詢中使用的不同表的資料來源的示例。

![資料表](media/logs-structure/queries-tables.png)

## <a name="log-analytics-workspace"></a>Log Analytics 工作區
Azure 監視器日誌收集的所有資料（應用程式見解除外）都存儲在[日誌分析工作區](../platform/manage-access.md)中。 您可以根據特定要求創建一個或多個工作區。 [資料來源](../platform/data-sources.md)（如 Azure 資源的活動日誌和資源日誌、虛擬機器上的代理以及見解和監視解決方案中的資料）將將資料寫入您作為載入的一部分配置的一個或多個工作區。 其他服務（如[Azure 安全中心和](/azure/security-center/) [Azure Sentinel）](/azure/sentinel/)也使用日誌分析工作區來存儲其資料，以便可以使用日誌查詢以及來自其他源的監視資料進行分析。

不同類型的資料存儲在工作區中的不同表中，並且每個表都有一組唯一的屬性。 創建一組標準表時，將添加到工作區中，並在新表上板時為不同的資料來源、解決方案和服務添加新表。 您還可以使用[資料收集器 API](../platform/data-collector-api.md)創建自訂表格。

您可以在工作區的"日誌分析"中的**架構**選項卡中流覽工作區中的表及其架構。

![工作區架構](media/scope/workspace-schema.png)

使用以下查詢列出工作區中的表以及前一天收集到每個記錄中的記錄數。 

```Kusto
union withsource = table * 
| where TimeGenerated > ago(1d)
| summarize count() by table
| sort by table asc
```
有關它們創建的表的詳細資訊，請參閱每個資料來源的文檔。 示例包括[代理資料來源](../platform/agent-data-sources.md)、[資源日誌](../platform/diagnostic-logs-schema.md)和[監視解決方案](../insights/solutions-inventory.md)的文章。

### <a name="workspace-permissions"></a>工作區許可權
請參閱[設計 Azure 監視器日誌部署](../platform/design-logs-deployment.md)以瞭解存取控制策略和建議，以便對工作區中的資料進行訪問。 除了授予對工作區本身的存取權限外，您還可以使用[表級 RBAC](../platform/manage-access.md#table-level-rbac)限制對各個表的訪問。

## <a name="application-insights-application"></a>應用程式洞察應用程式
在應用程式見解中創建應用程式時，將自動在 Azure 監視器日誌中創建相應的應用程式。 收集資料不需要配置，應用程式將自動寫入監視資料，如網頁檢視、請求和異常。

與日誌分析工作區不同，應用程式見解應用程式具有一組固定的表。 無法將其他資料來源配置為寫入應用程式，因此無法創建其他表。 

| Table | 描述 | 
|:---|:---|
| 可用性結果 | 來自可用性測試的摘要資料。 |
| 瀏覽器計時      | 有關用戶端性能的資料，例如處理傳入資料所佔用的時間。 |
| 自訂事件        | 由應用程式創建的自訂事件。 |
| 自訂指標       | 由應用程式創建的自訂指標。 |
| 相依性        | 從應用程式到外部元件的調用。 |
| 例外狀況          | 應用程式運行時引發的異常。 |
| 網頁檢視           | 有關每個網站視圖的資料，以及瀏覽器資訊。 |
| performanceCounters | 支援應用程式的計算資源的性能測量值。 |
| requests            | 每個應用程式請求的詳細資訊。  |
| traces              | 分散式跟蹤的結果。 |

您可以在應用程式的日誌分析中的架構選項卡中查看每個表的**架構**。

![應用程式架構](media/scope/application-schema.png)

## <a name="standard-properties"></a>標準屬性
雖然 Azure 監視器日誌中的每個表都有自己的架構，但所有表都共用標準屬性。 有關每個屬性的詳細資訊，請參閱[Azure 監視器日誌中的標準屬性](../platform/log-standard-properties.md)。

| Log Analytics 工作區 | 應用程式洞察應用程式 | 描述 |
|:---|:---|:---|
| TimeGenerated | timestamp  | 創建記錄的日期和時間。 |
| 類型          | itemType   | 從檢索記錄的表的名稱。 |
| _ResourceId   |            | 記錄關聯的資源的唯一識別碼。 |
| _IsBillable   |            | 指定引入的資料是否可計費。 |
| _BilledSize   |            | 指定要計費的資料的大小（以位元組為單位）。 |

## <a name="next-steps"></a>後續步驟
- 瞭解如何使用[日誌分析創建和編輯日誌搜索](../log-query/portals.md)。
- 請參閱使用新的查詢語言[撰寫查詢的教學課程](../log-query/get-started-queries.md)。

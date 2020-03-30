---
title: 收集&分析資源日誌
description: 記錄和分析 Azure 容器註冊表的資源日誌事件，如身份驗證、映射推送和圖像提取。
ms.topic: article
ms.date: 01/03/2020
ms.openlocfilehash: 00f9468721126bd166051df47cec1596356e9b54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409638"
---
# <a name="azure-container-registry-logs-for-diagnostic-evaluation-and-auditing"></a>用於診斷評估和審核的 Azure 容器註冊表日誌

本文介紹如何使用[Azure 監視器](../azure-monitor/overview.md)的功能為 Azure 容器註冊表收集日誌資料。 Azure 監視器收集注冊表中使用者驅動事件[的資源日誌](../azure-monitor/platform/platform-logs-overview.md)（以前稱為*診斷日誌*）。 收集和使用此資料以滿足需求，例如：

* 審核註冊表身份驗證事件，以確保安全性和合規性 

* 在登錄機碼目（如拉取和拉取事件）上提供完整的活動跟蹤，以便您可以診斷註冊表的操作問題 

使用 Azure 監視器收集資源日誌資料可能會產生額外費用。 請參閱[Azure 監視器定價](https://azure.microsoft.com/pricing/details/monitor/)。 

## <a name="repository-events"></a>存儲庫事件

目前記錄以下映射和其他工件的存儲庫級事件：

* **推送事件**
* **拉取事件**
* **解除標記事件**
* **刪除事件**（包括存儲庫刪除事件）

當前未記錄的存儲庫級事件：清除事件。

## <a name="registry-resource-logs"></a>註冊表資源日誌

資源日誌包含 Azure 資源發出的描述其內部操作的資訊。 對於 Azure 容器註冊表，日誌包含存儲在下表中的身份驗證和存儲庫級事件。 

* **容器註冊登錄事件**- 註冊表身份驗證事件和狀態，包括傳入的標識和 IP 位址
* **容器註冊表存儲庫事件**- 操作，如推送和拉取註冊表存儲庫中的圖像和其他工件
* **AzureMetrics** - [容器註冊表指標](../azure-monitor/platform/metrics-supported.md#microsoftcontainerregistryregistries)，如聚合推送和拉取計數。

對於操作，日誌資料包括：
  * 成功或失敗狀態
  * 開始和結束時間戳記

除了資源日誌之外，Azure 還提供[活動日誌](../azure-monitor/platform/platform-logs-overview.md)，Azure 管理事件的單個訂閱級記錄，如創建或刪除容器註冊表。

## <a name="enable-collection-of-resource-logs"></a>啟用資源日誌集合

預設情況下，不會啟用容器註冊表的資源日誌集合。 顯式啟用要監視的每個註冊表的診斷設置。 有關啟用診斷設置的選項，請參閱[創建診斷設置以在 Azure 中收集平臺日誌和指標](../azure-monitor/platform/diagnostic-settings.md)。

例如，要在 Azure 監視器中近乎即時地查看容器註冊表的日誌和指標，請收集日誌分析工作區中的資源日誌。 要使用 Azure 門戶啟用此診斷設置，請使用以下因素：

1. 如果還沒有工作區，請使用[Azure 門戶](../azure-monitor/learn/quick-create-workspace.md)創建工作區。 要最大程度地減少資料收集中的延遲，請確保工作區與容器註冊表**位於同一區域**。
1. 在門戶中，選擇註冊表，然後選擇 **"監視>診斷設置>添加診斷設置**。
1. 輸入設置的名稱，然後選擇 **"發送到日誌分析**"。
1. 選擇註冊表診斷日誌的工作區。
1. 選擇要收集的日誌資料，然後按一下"**保存**"。

下圖顯示了使用門戶為註冊表創建的診斷設置。

![啟用診斷設定](media/container-registry-diagnostics-audit-logs/diagnostic-settings.png)

> [!TIP]
> 僅收集所需的資料，平衡成本和監控需求。 例如，如果您只需要審核身份驗證事件，請僅選擇**容器註冊登錄事件**日誌。 

## <a name="view-data-in-azure-monitor"></a>在 Azure 監視器中查看資料

在日誌分析中啟用診斷日誌集合後，資料可能需要幾分鐘才能顯示在 Azure 監視器中。 要查看門戶中的資料，請選擇註冊表，然後選擇 **"監視>日誌**。 選擇包含註冊表資料的表之一。 

執行查詢以查看資料。 提供了幾個依例查詢，或運行您自己的查詢。 例如，以下查詢從**容器註冊表存儲庫事件**表中檢索最近 24 小時的資料：

```Kusto
ContainerRegistryRepositoryEvents
| where TimeGenerated > ago(1d) 
```

下圖顯示了示例輸出：

![查詢記錄檔資料](media/container-registry-diagnostics-audit-logs/azure-monitor-query.png)

有關在 Azure 門戶中使用日誌分析的教程，請參閱[使用 Azure 監視器日誌分析入門](../azure-monitor/log-query/get-started-portal.md)，或嘗試日誌分析[演示環境](https://portal.loganalytics.io/demo)。 

有關日誌查詢的詳細資訊，請參閱 Azure[監視器 中的日誌查詢概述](../azure-monitor/log-query/log-query-overview.md)。

### <a name="additional-query-examples"></a>其他查詢示例

#### <a name="100-most-recent-registry-events"></a>100 個最近的註冊表事件

```Kusto
ContainerRegistryRepositoryEvents
| union ContainerRegistryLoginEvents
| top 100 by TimeGenerated
| project TimeGenerated, LoginServer , OperationName , Identity , Repository , DurationMs , Region , ResultType
```

## <a name="additional-log-destinations"></a>其他日誌目標

除了將日誌發送到日誌分析或作為替代方法之外，常見方案是選擇 Azure 存儲帳戶作為日誌目標。 要在 Azure 存儲中存檔日誌，請先創建存儲帳戶，然後再通過診斷設置啟用存檔。

還可以將診斷日誌事件資料流到[Azure 事件中心](../event-hubs/event-hubs-what-is-event-hubs.md)。 事件中樞每秒可輸入數百萬個事件，您可以使用任何即時分析提供者來轉換和儲存。 

## <a name="next-steps"></a>後續步驟

* 瞭解有關使用[日誌分析和](../azure-monitor/log-query/get-started-portal.md)創建[日誌查詢的更多資訊](../azure-monitor/log-query/get-started-queries.md)。
* 請參閱[Azure 平臺日誌概述](../azure-monitor/platform/platform-logs-overview.md)，以瞭解 Azure 不同層可用的平臺日誌。


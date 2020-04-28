---
title: 收集 & 分析資源記錄
description: 記錄和分析資源記錄檔事件以進行 Azure Container Registry，例如驗證、影像推送和影像提取。
ms.topic: article
ms.date: 01/03/2020
ms.openlocfilehash: 00f9468721126bd166051df47cec1596356e9b54
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79409638"
---
# <a name="azure-container-registry-logs-for-diagnostic-evaluation-and-auditing"></a>用於診斷評估和審核的 Azure Container Registry 記錄

本文說明如何使用[Azure 監視器](../azure-monitor/overview.md)的功能來收集 Azure container registry 的記錄資料。 Azure 監視器會針對登錄中的使用者驅動事件收集[資源記錄](../azure-monitor/platform/platform-logs-overview.md)（先前稱為*診斷記錄*）。 收集並取用此資料以符合需求，例如：

* 審核登錄驗證事件以確保安全性與合規性 

* 在登錄成品（例如提取和提取事件）上提供完整的活動記錄，以便您可以診斷登錄的操作問題 

使用 Azure 監視器收集資源記錄資料可能會產生額外的成本。 請參閱[Azure 監視器定價](https://azure.microsoft.com/pricing/details/monitor/)。 

## <a name="repository-events"></a>儲存機制事件

目前已記錄影像和其他成品的下列存放庫層級事件：

* **推送事件**
* **提取事件**
* **Untag 事件**
* **刪除事件**（包括存放庫刪除事件）

目前未記錄的存放庫層級事件：清除事件。

## <a name="registry-resource-logs"></a>登錄資源記錄

資源記錄包含 Azure 資源所發出的資訊，以描述其內部作業。 針對 Azure container registry，記錄包含下表中儲存的驗證和存放庫層級事件。 

* **ContainerRegistryLoginEvents** -登錄驗證事件和狀態，包括傳入身分識別和 IP 位址
* **ContainerRegistryRepositoryEvents** -在登錄存放庫中進行映射和其他構件的推送和提取作業
* **AzureMetrics** - [容器](../azure-monitor/platform/metrics-supported.md#microsoftcontainerregistryregistries)登錄的計量，例如匯總的推送和提取計數。

對於作業，記錄資料包含：
  * 成功或失敗狀態
  * 開始和結束時間戳記

除了資源記錄之外，Azure 還提供「[活動記錄](../azure-monitor/platform/platform-logs-overview.md)」，這是 azure 管理事件的單一訂用帳戶層級記錄，例如建立或刪除容器登錄。

## <a name="enable-collection-of-resource-logs"></a>啟用資源記錄的收集

預設不會啟用容器登錄的資源記錄檔收集。 針對您要監視的每個登錄明確啟用診斷設定。 如需啟用診斷設定的選項，請參閱[建立診斷設定以收集 Azure 中的平臺記錄和計量](../azure-monitor/platform/diagnostic-settings.md)。

例如，若要以近乎即時的方式在 Azure 監視器中查看容器登錄的記錄和計量，請在 Log Analytics 工作區中收集資源記錄。 若要使用 Azure 入口網站啟用此診斷設定：

1. 如果您還沒有工作區，請使用[Azure 入口網站](../azure-monitor/learn/quick-create-workspace.md)建立工作區。 若要將資料收集中的延遲降至最低，請確定工作區位於與容器登錄**相同的區域**中。
1. 在入口網站中，選取登錄，然後選取 [**監視] > [診斷設定] > 新增診斷設定**]。
1. 輸入設定的名稱，然後選取 [**傳送至 Log Analytics**]。
1. 選取登錄診斷記錄的工作區。
1. 選取您要收集的記錄資料，然後按一下 [**儲存**]。

下圖顯示如何使用入口網站建立登錄的診斷設定。

![啟用診斷設定](media/container-registry-diagnostics-audit-logs/diagnostic-settings.png)

> [!TIP]
> 只收集您所需的資料，平衡成本和您的監視需求。 例如，如果您只需要審核驗證事件，請只選取**ContainerRegistryLoginEvents**記錄。 

## <a name="view-data-in-azure-monitor"></a>查看 Azure 監視器中的資料

在 Log Analytics 中啟用診斷記錄的收集之後，可能需要幾分鐘的時間，資料才會出現在 Azure 監視器中。 若要在入口網站中查看資料，請選取登錄，然後選取 [**監視 > 記錄**]。 選取其中一個包含登錄資料的資料表。 

執行查詢以查看資料。 提供數個範例查詢，或執行您自己的查詢。 例如，下列查詢會從**ContainerRegistryRepositoryEvents**資料表中取出最近24小時的資料：

```Kusto
ContainerRegistryRepositoryEvents
| where TimeGenerated > ago(1d) 
```

下圖顯示範例輸出：

![查詢記錄檔資料](media/container-registry-diagnostics-audit-logs/azure-monitor-query.png)

如需在 Azure 入口網站中使用 Log Analytics 的教學課程，請參閱[開始使用 Azure 監視器 Log analytics](../azure-monitor/log-query/get-started-portal.md)，或嘗試 Log analytics[示範環境](https://portal.loganalytics.io/demo)。 

如需記錄查詢的詳細資訊，請參閱[Azure 監視器中的記錄查詢總覽](../azure-monitor/log-query/log-query-overview.md)。

### <a name="additional-query-examples"></a>其他查詢範例

#### <a name="100-most-recent-registry-events"></a>100最新的登錄事件

```Kusto
ContainerRegistryRepositoryEvents
| union ContainerRegistryLoginEvents
| top 100 by TimeGenerated
| project TimeGenerated, LoginServer , OperationName , Identity , Repository , DurationMs , Region , ResultType
```

## <a name="additional-log-destinations"></a>其他記錄目的地

除了將記錄傳送至 Log Analytics，或作為替代方案，常見的案例是選取 Azure 儲存體帳戶作為記錄目的地。 若要在 Azure 儲存體中封存記錄，請先建立儲存體帳戶，然後再透過診斷設定啟用保存。

您也可以將診斷記錄事件串流至[Azure 事件中樞](../event-hubs/event-hubs-what-is-event-hubs.md)。 事件中樞每秒可輸入數百萬個事件，您可以使用任何即時分析提供者來轉換和儲存。 

## <a name="next-steps"></a>後續步驟

* 深入瞭解如何使用[Log Analytics](../azure-monitor/log-query/get-started-portal.md)和建立[記錄查詢](../azure-monitor/log-query/get-started-queries.md)。
* 請參閱[azure 平臺記錄的總覽](../azure-monitor/platform/platform-logs-overview.md)，以瞭解可在不同 Azure 層級使用的平臺記錄。


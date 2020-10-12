---
title: 收集 & 分析資源記錄
description: 記錄並分析 Azure Container Registry 的資源記錄檔事件，例如驗證、映射推送和影像提取。
ms.topic: article
ms.date: 06/01/2020
ms.openlocfilehash: 63ccb944b9c3de9941acf55ca5ea85fda70a7008
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87553371"
---
# <a name="azure-container-registry-logs-for-diagnostic-evaluation-and-auditing"></a>用於診斷評估和審核的 Azure Container Registry 記錄

本文說明如何使用 [Azure 監視器](../azure-monitor/overview.md)的功能，收集 Azure container registry 的記錄資料。 Azure 監視器會針對您登錄中的使用者驅動事件，收集先前稱為*診斷記錄*) 的[資源記錄](../azure-monitor/platform/platform-logs-overview.md)檔 (。 收集和使用此資料以符合需求，例如：

* Audit registry authentication 事件以確保安全性與合規性 

* 提供登錄構件（例如提取和提取事件）的完整活動記錄，讓您可以診斷登錄中的操作問題 

使用 Azure 監視器收集資源記錄資料可能會產生額外的成本。 請參閱 [Azure 監視器定價](https://azure.microsoft.com/pricing/details/monitor/)。 

## <a name="repository-events"></a>存放庫事件

下列是目前記錄的映射和其他成品的存放庫層級事件：

* **推送**
* **提取**
* **Untag**
* **刪除** (包括存放庫刪除事件) 
* **清除標記** 和 **清除資訊清單**

> [!NOTE]
> 只有在設定登錄 [保留原則](container-registry-retention-policy.md) 時，才會記錄清除事件。

## <a name="registry-resource-logs"></a>登錄資源記錄檔

資源記錄包含 Azure 資源所發出的資訊，這些資訊會描述其內部作業。 針對 Azure container registry，記錄包含下表中所儲存的驗證和儲存機制層級事件。 

* **ContainerRegistryLoginEvents**  -登錄驗證事件和狀態，包括傳入的身分識別和 IP 位址
* **ContainerRegistryRepositoryEvents** -在登錄存放庫中進行映射和其他構件的推送和提取等作業
* **AzureMetrics**  - [容器登錄度量](../azure-monitor/platform/metrics-supported.md#microsoftcontainerregistryregistries)，例如匯總的推送和提取計數。

針對作業，記錄資料包含：
  * 成功或失敗狀態
  * 開始和結束時間戳記

除了資源記錄，Azure 還提供 [活動記錄](../azure-monitor/platform/platform-logs-overview.md)，這是 azure 管理事件（例如建立或刪除容器登錄）的單一訂用帳戶層級記錄。

## <a name="enable-collection-of-resource-logs"></a>啟用收集資源記錄

預設不會啟用容器登錄的資源記錄收集。 針對您要監視的每個登錄明確啟用診斷設定。 如需啟用診斷設定的選項，請參閱 [建立診斷設定以收集 Azure 中的平臺記錄和計量](../azure-monitor/platform/diagnostic-settings.md)。

例如，若要在 Azure 監視器中近乎即時地查看容器登錄的記錄和計量，請收集 Log Analytics 工作區中的資源記錄。 若要使用 Azure 入口網站來啟用此診斷設定：

1. 如果您還沒有工作區，請使用 [Azure 入口網站](../azure-monitor/learn/quick-create-workspace.md)建立工作區。 若要將資料收集的延遲降至最低，請確定工作區與您的容器登錄位於 **相同的區域** 。
1. 在入口網站中，選取登錄，然後選取 [ **監視] > 診斷設定 > 新增診斷設定**。
1. 輸入設定的名稱，然後選取 [ **傳送至 Log Analytics**]。
1. 選取登錄診斷記錄的工作區。
1. 選取您要收集的記錄資料，然後按一下 [ **儲存**]。

下圖顯示如何使用入口網站建立登錄的診斷設定。

![啟用診斷設定](media/container-registry-diagnostics-audit-logs/diagnostic-settings.png)

> [!TIP]
> 只收集您需要的資料，平衡成本和您的監視需求。 例如，如果您只需要審核驗證事件，請只選取 **ContainerRegistryLoginEvents** 記錄檔。 

## <a name="view-data-in-azure-monitor"></a>在 Azure 監視器中查看資料

在 Log Analytics 中啟用診斷記錄的收集之後，可能需要幾分鐘的時間，資料才會出現在 Azure 監視器中。 若要在入口網站中查看資料，請選取登錄，然後選取 [ **監視] > 記錄**檔。 選取其中一個包含登錄資料的資料表。 

執行查詢以查看資料。 提供數個範例查詢，或執行您自己的查詢。 例如，下列查詢會從 **ContainerRegistryRepositoryEvents** 資料表中取出最近24小時的資料：

```Kusto
ContainerRegistryRepositoryEvents
| where TimeGenerated > ago(1d) 
```

下圖顯示範例輸出：

![查詢記錄檔資料](media/container-registry-diagnostics-audit-logs/azure-monitor-query.png)

如需在 Azure 入口網站中使用 Log Analytics 的教學課程，請參閱 [開始使用 Azure 監視器 Log analytics](../azure-monitor/log-query/get-started-portal.md)，或嘗試使用 Log analytics [示範環境](https://portal.loganalytics.io/demo)。 

如需有關記錄查詢的詳細資訊，請參閱 [Azure 監視器中的記錄查詢總覽](../azure-monitor/log-query/log-query-overview.md)。

## <a name="query-examples"></a>查詢範例

### <a name="error-events-from-the-last-hour"></a>過去一小時內的錯誤事件

```Kusto
union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
| where TimeGenerated > ago(1h)
| where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
```

### <a name="100-most-recent-registry-events"></a>100最近的登錄事件

```Kusto
ContainerRegistryRepositoryEvents
| union ContainerRegistryLoginEvents
| top 100 by TimeGenerated
| project TimeGenerated, LoginServer, OperationName, Identity, Repository, DurationMs, Region , ResultType
```

### <a name="identity-of-user-or-object-that-deleted-repository"></a>已刪除存放庫的使用者或物件的身分識別

```Kusto
ContainerRegistryRepositoryEvents
| where OperationName contains "Delete"
| project LoginServer, OperationName, Repository, Identity, CallerIpAddress
```

### <a name="identity-of-user-or-object-that-deleted-tag"></a>已刪除標記的使用者或物件的身分識別

```Kusto
ContainerRegistryRepositoryEvents
| where OperationName contains "Untag"
| project LoginServer, OperationName, Repository, Tag, Identity, CallerIpAddress
```

### <a name="repository-level-operation-failures"></a>存放庫層級作業失敗

```kusto
ContainerRegistryRepositoryEvents 
| where ResultDescription contains "40"
| project TimeGenerated, OperationName, Repository, Tag, ResultDescription
```

### <a name="registry-authentication-failures"></a>登錄驗證失敗

```kusto
ContainerRegistryLoginEvents 
| where ResultDescription != "200"
| project TimeGenerated, Identity, CallerIpAddress, ResultDescription
```


## <a name="additional-log-destinations"></a>其他記錄目的地

除了將記錄傳送至 Log Analytics，或另一種方式，常見的案例是選取 Azure 儲存體帳戶作為記錄目的地。 若要封存 Azure 儲存體中的記錄，請先建立儲存體帳戶，然後再透過診斷設定來啟用保存。

您也可以將診斷記錄事件串流至 [Azure 事件中樞](../event-hubs/event-hubs-about.md)。 事件中樞每秒可輸入數百萬個事件，您可以使用任何即時分析提供者來轉換和儲存。 

## <a name="next-steps"></a>後續步驟

* 深入瞭解如何使用 [Log Analytics](../azure-monitor/log-query/get-started-portal.md) 和建立 [記錄查詢](../azure-monitor/log-query/get-started-queries.md)。
* 請參閱 [azure 平臺記錄的總覽](../azure-monitor/platform/platform-logs-overview.md) ，以瞭解不同 azure 層級可用的平臺記錄。

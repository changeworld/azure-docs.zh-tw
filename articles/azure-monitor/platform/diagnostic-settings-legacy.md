---
title: 使用診斷設定收集 Azure 活動記錄檔（預覽）-Azure 監視器 |Microsoft Docs
description: 使用診斷設定，將 Azure 活動記錄轉送至 Azure 監視器記錄、Azure 儲存體或 Azure 事件中樞。
author: bwren
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
ms.author: bwren
ms.date: 02/04/2020
ms.openlocfilehash: fcdcef5d63163b24fe5de0f547dc2dde00cd674f
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016250"
---
# <a name="update-to-azure-activity-log-collection-and-export"></a>更新至 Azure 活動記錄收集和匯出
[Azure 活動記錄](platform-logs-overview.md)是一個[平臺記錄](platform-logs-overview.md)，可讓您深入瞭解 Azure 中發生的訂用帳戶層級事件。 將活動記錄專案傳送到[事件中樞或儲存體帳戶](activity-log-export.md)或[log Analytics 工作區](activity-log-collect.md)的方法，已變更為使用[診斷設定](diagnostic-settings.md)。 本文說明方法之間的差異，以及如何在準備變更為診斷設定時清除舊版設定。


## <a name="differences-between-methods"></a>方法之間的差異

### <a name="advantages"></a>優點
使用診斷設定與目前的方法相比有下列優點：

- 收集所有平臺記錄的一致方法。
- 跨多個訂用帳戶和租使用者收集活動記錄。
- 篩選集合，只收集特定類別的記錄。
- 收集所有活動記錄類別。 某些分類不會使用舊版方法收集。
- 記錄內嵌的延遲較快。 先前的方法大約會有15分鐘的延遲，而診斷設定只會加上1分鐘。

### <a name="considerations"></a>考量
啟用這項功能之前，請先考慮使用診斷設定的下列活動記錄收集詳細資料。

- 已移除將活動記錄收集至 Azure 儲存體的保留設定，這表示資料會無限期地儲存，直到您將其移除為止。
- 目前，您只能使用 Azure 入口網站來建立訂用帳戶層級的診斷設定。 若要使用其他方法（例如 PowerShell 或 CLI），您可以建立 Resource Manager 範本。


### <a name="differences-in-data"></a>資料的差異
診斷設定會收集與先前用來收集活動記錄的方法相同的資料，但目前的差異如下：

已移除下列資料行。 這些資料行的取代格式不同，因此您可能需要修改使用它們的記錄查詢。 您可能還是會看到 [已移除] 架構中的資料行，但不會填入資料。

| 已移除資料行 | 取代資料行 |
|:---|:---|
| ActivityStatus    | ActivityStatusValue    |
| ActivitySubstatus | ActivitySubstatusValue |
| OperationName     | OperationNameValue     |
| ResourceProvider  | ResourceProviderValue  |

已加入下列資料行：

- Authorization_d
- Claims_d
- Properties_d

> [!IMPORTANT]
> 在某些情況下，這些資料行中的值可能全部大寫。 如果您有包含這些資料行的查詢，您應該使用[= ~ 運算子](https://docs.microsoft.com/azure/kusto/query/datatypes-string-operators)來執行不區分大小寫的比較。

## <a name="work-with-legacy-settings"></a>使用舊版設定
如果您不選擇將取代為診斷設定，則收集活動記錄的舊版設定會繼續工作。 使用下列方法來管理訂用帳戶的記錄檔設定檔。

1. 從 Azure 入口網站的 [ **Azure 監視器**] 功能表中，選取 [**活動記錄**]。
3. 按一下 [診斷設定]。

   ![診斷設定](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. 按一下 [紫色] 橫幅以取得舊版體驗。

    ![舊版體驗](media/diagnostic-settings-subscription/legacy-experience.png)


如需使用舊版收集方法的詳細資訊，請參閱下列文章。

- [在 Azure 監視器的 Log Analytics 工作區中收集並分析 Azure 活動記錄](activity-log-collect.md)
- [將 Azure 活動記錄收集到 Azure Active Directory 租使用者之間的 Azure 監視器](activity-log-collect-tenants.md)
- [將 Azure 活動記錄匯出至儲存體或 Azure 事件中樞](activity-log-export.md)

## <a name="disable-existing-settings"></a>停用現有的設定
您應該停用現有的活動集合，再使用診斷設定加以啟用。 啟用這兩個可能會導致重複的資料。

### <a name="disable-collection-into-log-analytics-workspace"></a>停用收集到 Log Analytics 工作區

1. 開啟 Azure 入口網站中的  **Log Analytics 工作區** 功能表，然後選取要收集活動記錄的工作區。
2. 在工作區功能表的 [**工作區資料來源**] 區段中，選取 [ **Azure 活動記錄**]。
3. 按一下您要中斷連線的訂用帳戶。
4. 按一下 **[中斷連線]** ，**然後在系統**要求您確認您的選擇時才會出現。

### <a name="disable-log-profile"></a>停用記錄檔設定檔

1. 使用 [使用[舊版設定](#work-with-legacy-settings)] 中所述的程式來開啟舊版設定。
2. 停用儲存體或事件中樞的任何目前集合。



## <a name="activity-log-monitoring-solution"></a>活動記錄監視解決方案
Azure Log Analytics 監視解決方案包含多個記錄查詢和視圖，可用於分析 Log Analytics 工作區中的活動記錄檔記錄。 此解決方案會使用 Log Analytics 工作區中收集的記錄資料，如果您使用診斷設定收集活動記錄，則不需要任何變更即可繼續工作。 如需此解決方案的詳細資訊，請參閱[活動記錄分析監視解決方案](activity-log-collect.md#activity-logs-analytics-monitoring-solution)。

## <a name="next-steps"></a>後續步驟

* [深入了解活動記錄](../../azure-resource-manager/management/view-activity-logs.md)
* [深入瞭解診斷設定](diagnostic-settings.md)

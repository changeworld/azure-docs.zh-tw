---
title: 使用 Azure Sentinel 時的實用資源 |Microsoft Docs
description: 本檔提供您使用 Azure Sentinel 時的實用資源清單。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/02/2019
ms.author: yelevin
ms.openlocfilehash: 2465cf74dbf4fbf074aed8f8e4097a236e628425
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660639"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>使用 Azure Sentinel 的實用資源



本文列出的資源可協助您取得有關使用 Azure Sentinel 的詳細資訊。

Azure Logic Apps 連接器： <https://docs.microsoft.com/connectors/>


## <a name="auditing-and-reporting"></a>稽核與報告
Azure Sentinel 的 Audit 記錄會保留在 [Azure 活動記錄](../azure-monitor/platform/platform-logs-overview.md)中。

可以審核下列支援的作業。

|作業名稱|    資源類型|
|----|----|
|建立或更新活頁簿  |Microsoft Insights/活頁簿|
|刪除活頁簿    |Microsoft Insights/活頁簿|
|設定工作流程   |Microsoft.Logic/workflows|
|刪除工作流程    |Microsoft.Logic/workflows|
|建立已儲存的搜尋    |OperationalInsights/workspace/savedSearches|
|刪除儲存的搜尋    |OperationalInsights/workspace/savedSearches|
|更新警示規則 |SecurityInsights/alertRules|
|刪除警示規則 |SecurityInsights/alertRules|
|更新警示規則回應動作 |SecurityInsights/alertRules/actions|
|刪除警示規則回應動作 |SecurityInsights/alertRules/actions|
|更新書簽   |SecurityInsights/書簽|
|刪除書簽   |SecurityInsights/書簽|
|更新案例   |SecurityInsights/案例|
|更新案例調查  |SecurityInsights/案例/調查|
|建立案例批註   |SecurityInsights/案例/評論|
|更新資料連線器 |SecurityInsights/dataConnectors|
|刪除資料連線器 |SecurityInsights/dataConnectors|
|更新設定    |Microsoft. SecurityInsights/settings|

### <a name="view-audit-and-reporting-data-in-azure-sentinel"></a>在 Azure Sentinel 中查看審核和報告資料

您可以從 Azure 活動記錄將資料串流至 Azure Sentinel，然後對其執行研究和分析，以查看此資料。

1. 連接 [Azure 活動](connect-azure-activity.md) 資料來源。 這麼做之後，就會在名為 AzureActivity 的 **記錄** 畫面中，將 audit 事件串流至新的資料表。

1. 然後，使用 KQL 查詢資料，就像使用任何其他資料表一樣。

    例如，若要找出誰是最後一個編輯特定分析規則的使用者，請使用下列查詢 (取代 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 為您想要檢查) 之規則的規則識別碼：

    ```kusto
    AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS/ALERTRULES/WRITE"
    | where Properties contains "alertRules/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    | project Caller , TimeGenerated , Properties
    ```



## <a name="vendor-documentation"></a>廠商檔

| **廠商**  | **在 Azure Sentinel 中使用事件** | **連結**|
|----|----|----|
| GitHub| 用來存取 [社區] 頁面| <https://github.com/Azure/Azure-Sentinel> |
| PaloAlto| 設定 CEF| <https://www.paloaltonetworks.com/documentation/misc/cef.html>|
| PluralSight | Kusto 查詢語言課程| [https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch](https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch)|

## <a name="blogs-and-forums"></a>Blog 和論壇

在 [>techcommunity 空間](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel) 張貼您的問題以進行 Azure Sentinel。

從 [>techcommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog) 和 [Microsoft Azure](https://azure.microsoft.com/blog/tag/azure-sentinel/)觀看 Azure Sentinel 的 blog 文章。


## <a name="next-steps"></a>後續步驟
在本檔中，您會看到一份資源清單，當您使用 Azure Sentinel 時很有用。 您可以在 [Microsoft Azure 的安全性與合規性 blog](/archive/blogs/azuresecurity/)中找到有關 Azure 安全性與合規性的其他資訊。
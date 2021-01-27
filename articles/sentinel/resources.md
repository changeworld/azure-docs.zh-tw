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
ms.date: 01/27/2021
ms.author: yelevin
ms.openlocfilehash: 11c5c77623fcc693210d5a42bf94c968e884fec3
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920015"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>使用 Azure Sentinel 的實用資源



本文列出的資源可協助您取得有關使用 Azure Sentinel 的詳細資訊。

- **Azure Logic Apps 連接器**： <https://docs.microsoft.com/connectors/>


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

我們愛聽取使用者的意見！

- 在 [>techcommunity 空間](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel)**張貼您的問題** 以進行 Azure Sentinel。 

- 透過我們的 [使用者心聲](https://feedback.azure.com/forums/920458-azure-sentinel)程式 **傳送建議的改進**。

- **查看並批註** Azure Sentinel 的 blog 文章：

    - [>techcommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog) 
    - [Microsoft Azure](https://azure.microsoft.com/blog/tag/azure-sentinel/)

如需有關 Azure 安全性與合規性的詳細資訊，請參閱 [Microsoft Azure 安全性與合規性 blog](https://techcommunity.microsoft.com/t5/microsoft-security-and/bg-p/MicrosoftSecurityandCompliance)。


## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [取得認證！](/learn/paths/security-ops-sentinel/)

> [!div class="nextstepaction"]
> [閱讀客戶使用案例案例](https://customers.microsoft.com/en-us/search?sq=%22Azure%20Sentinel%20%22&ff=&p=0&so=story_publish_date%20desc)


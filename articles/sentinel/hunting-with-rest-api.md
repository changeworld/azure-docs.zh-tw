---
title: 使用 REST API 管理 Azure Sentinel 中的搜尋和即時資料流查詢 |Microsoft Docs
description: 本文說明 Azure Sentinel 搜尋功能如何讓您利用 Log Analytics 的 REST API 來管理搜尋和即時資料流查詢。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/06/2020
ms.author: yelevin
ms.openlocfilehash: ca60b9350171cee55462c9df28915c811e1cfd25
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2020
ms.locfileid: "94377674"
---
# <a name="manage-hunting-and-livestream-queries-in-azure-sentinel-using-rest-api"></a>使用 REST API 管理 Azure Sentinel 中的搜尋和即時資料流查詢

Azure Sentinel，在 Azure 監視器 Log Analytics 上內建的部分，可讓您使用 Log Analytics 的 REST API 來管理搜尋和即時資料流查詢。 本檔說明如何使用 REST API 建立和管理搜尋查詢。  以這種方式建立的查詢會顯示在 Azure Sentinel UI 中。

如需 [已儲存搜尋 API](https://docs.microsoft.com/rest/api/loganalytics/savedsearches)的詳細資訊，請參閱最終的 REST API 參考。

## <a name="api-examples"></a>API 範例

在下列範例中，使用下表中指定的取代取代這些預留位置：

| 預留位置 | 更換為 |
|-|-|
| **SubscriptionId** | 您要套用搜尋或即時資料流查詢的訂用帳戶名稱。 |
| **ResourceGroupName** | 您要套用搜尋或即時資料流查詢的資源組名。 |
| **{savedSearchId}** | 每個搜尋查詢 (GUID) 的唯一識別碼。 |
| **WorkspaceName** | 作為查詢目標之 Log Analytics 工作區的名稱。 |
| **DisplayName** | 您所選擇的查詢顯示名稱。 |
| **產品介紹** | 搜尋或即時資料流查詢的描述。 |
| **技巧** | 相關的 MITRE ATT&適用于查詢的 CK 策略。 |
| **查詢** | 查詢的查詢運算式。 |
|  

### <a name="example-1"></a>範例 1

此範例說明如何建立或更新指定 Azure Sentinel 工作區的搜尋查詢。  針對即時資料流查詢，請在 **要求主體** 中將 " *category"： "搜尋查詢"* 取代為 *"category"： "即時資料流 query"* ： 

#### <a name="request-header"></a>要求標頭

```http
PUT https://management.azure.com/subscriptions/{subscriptionId} _
    /resourcegroups/{resourceGroupName} _
    /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
    /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

#### <a name="request-body"></a>Request body

```json
{
"properties": {
    “Category”: “Hunting Queries”,
    "DisplayName": "HuntingRule02",
    "Query": "SecurityEvent | where EventID == \"4688\" | where CommandLine contains \"-noni -ep bypass $\"",
    “Tags”: [
        { 
        “Name”: “Description”,
        “Value”: “Test Hunting Query”
        },
        { 
        “Name”: “Tactics”,
        “Value”: “Execution, Discovery”
        }
        ]        
    }
}
```

### <a name="example-2"></a>範例 2

此範例說明如何刪除指定 Azure Sentinel 工作區的搜尋或即時資料流查詢：

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId} _
       /resourcegroups/{resourceGroupName} _
       /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
       /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

### <a name="example-3"></a>範例 3

此範例會示範如何針對指定的工作區取出搜尋或即時資料流查詢：

```http
GET https://management.azure.com/subscriptions/{subscriptionId} _
    /resourcegroups/{resourceGroupName} _
    /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
    /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何使用 Log Analytics API 來管理 Azure Sentinel 中的搜尋和即時資料流查詢。 若要深入了解 Azure Sentinel，請參閱下列文章：

- [主動搜捕威脅](hunting.md)
- [使用筆記本來執行自動化搜尋活動](notebooks.md)

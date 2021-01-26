---
title: 使用 REST API 管理 Azure Sentinel 中的 watchlists |Microsoft Docs
description: 本文說明如何使用 Log Analytics REST API 來管理 Azure Sentinel watchlists，以建立、修改和刪除 watchlists 及其專案。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: reference
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/11/2021
ms.author: yelevin
ms.openlocfilehash: ea571f9b033ba82709a13c6d32649f3228ee04b1
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98798404"
---
# <a name="manage-watchlists-in-azure-sentinel-using-rest-api"></a>使用 REST API 管理 Azure Sentinel 中的 watchlists

> [!IMPORTANT]
> Watchlists 功能目前為 **預覽** 狀態。 請參閱 [Microsoft Azure 預覽的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ，以取得適用于 Azure 功能（Beta、預覽或尚未發行正式運作）的其他法律條款。

Azure Sentinel，內建 Azure 監視器 Log Analytics 的一部分，可讓您使用 Log Analytics 的 REST API 來管理 watchlists。 本檔說明如何使用 REST API 建立、修改和刪除 watchlists 和其專案。  以這種方式建立的 Watchlists 會顯示在 Azure Sentinel UI 中。

## <a name="common-uri-parameters"></a>一般 URI 參數

以下是所有關注清單 API 命令的通用 URI 參數：

| 名稱 | 位於 | 必要 | 類型 | 描述 |
|-|-|-|-|-|
| **SubscriptionId** | 路徑 | 是 | GUID | Azure 訂用帳戶識別碼 |
| **ResourceGroupName** | 路徑 | 是 | 字串 | 訂用帳戶中的資源組名 |
| **WorkspaceName** | 路徑 | 是 | 字串 | Log Analytics 工作區的名稱 |
| **{watchlistAlias}** | 路徑 | 是* | 字串 | 給定關注清單的名稱<br>\* 在抓取所有 watchlists 時不需要 |
| **{watchlistItemId}** | 路徑 | 是 * * | GUID | 要在關注清單中建立、新增或刪除之專案的識別碼。<br>\*\* 只有關注清單專案命令才需要 |
| **{api 版本}** | 查詢 | 是 | 字串 | 用來提出此要求的通訊協定版本。 從2020年10月29日起，關注清單 API 版本為 *2019-01-01-preview* |
| **>bearertoken** | 授權 | 是 | token | 持有人授權權杖 |
|  

## <a name="retrieve-all-watchlists"></a>取出所有 watchlists

此命令會抓取與工作區相關聯的所有 watchlists，而不包含其專案。

### <a name="request-uri"></a>要求 URI
 (URI 是一行，可讓您輕鬆閱讀) 

| 方法 | 要求 URI |
|-|-|
| GET | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists?api-version={{api-version}}` |
|

### <a name="responses"></a>回應

| 狀態碼 | 回應本文 | 描述 |
|-|-|-|
| 200/確定 | 現有 watchlists 的清單，如果找不到關注清單，則為空白 |  |
| 400/不正確的要求 |  | 格式不正確的要求語法，要求參數無效 .。。 |
|

## <a name="look-up-a-watchlist-by-name"></a>依名稱查閱關注清單

此命令會抓取與工作區相關聯的特定關注清單，而不包含其專案。

### <a name="request-uri"></a>要求 URI
 (URI 是一行，可讓您輕鬆閱讀) 

| 方法 | 要求 URI |
|-|-|
| GET | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="responses"></a>回應

| 狀態碼 | 回應本文 | 描述 |
|-|-|-|
| 200/確定 | 要求的關注清單 |  |
| 400/不正確的要求 |  | 格式不正確的要求語法，要求參數無效 .。。 |
| 404/找不到 |  | 找不到具有所要求名稱的關注清單 |
|

## <a name="create-a-watchlist"></a>建立關注清單

此命令會建立關注清單，並在其中新增專案。 它會對此端點進行兩個呼叫來建立關注清單和其專案：第一個會建立關注清單 (父) ，而第二個呼叫則會新增專案。

### <a name="request-uri"></a>要求 URI
 (URI 是一行，可讓您輕鬆閱讀) 

| 方法 | 要求 URI |
|-|-|
| PUT | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="request-body"></a>要求本文

以下是關注清單 create 要求的要求主體範例：

```json
{
    "properties": {
        "displayName": "High Value Assets Watchlist",
        "source": "Local file",
        "provider": "Microsoft",
        "numberOfLinesToSkip":"1",
        "rawContent": "metadata line\nheader1, header2\nval1, val2",
        "contentType": "text/csv"
    }
}
```

### <a name="responses"></a>回應

| 狀態碼 | 回應本文 | 描述 |
|-|-|-|
| 200/確定 | 要求所建立的關注清單，不含專案 |  |
| 400/不正確的要求 |  | 格式不正確的要求語法，要求參數無效 .。。 |
| 409/衝突 |  | 作業失敗，有相同別名的現有關注清單 |
|

## <a name="delete-a-watchlist"></a>刪除關注清單

此命令會刪除關注清單和其專案。

### <a name="request-uri"></a>要求 URI
 (URI 是一行，可讓您輕鬆閱讀) 

| 方法 | 要求 URI |
|-|-|
| DELETE | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="responses"></a>回應

| 狀態碼 | 回應本文 | 描述 |
|-|-|-|
| 200/確定 | 空白回應主體 |  |
| 204/沒有內容 | 空白回應主體 | 未刪除任何專案 |
| 400/不正確的要求 |  | 格式不正確的要求語法，要求參數無效 .。。 |
|

## <a name="add-or-update-a-watchlist-item"></a>新增或更新關注清單專案

在 (GUID) 的現有 *watchlisItemId* 上執行此命令時，將會使用要求本文中提供的資料來更新專案。 否則，將會建立新專案。

### <a name="request-uri"></a>要求 URI
 (URI 是一行，可讓您輕鬆閱讀) 

| 方法 | 要求 URI |
|-|-|
| PUT | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}/`<br>`watchlistitems/{{watchlistItemId}}?api-version={{api-version}}` |
|

### <a name="request-body"></a>要求本文

以下是關注清單專案新增/更新要求的要求主體範例：

```json
{
    "properties": {
      "itemsKeyValue": {
           "Gateway subnet": "10.0.255.224/27",
           "Web Tier": "10.0.1.0/24",
           "Business tier": "10.0.2.0/24",
           "Private DMZ in": "10.0.0.0/27",
           "Public DMZ out": "10.0.0.96/27"
      }
}
}
```

### <a name="responses"></a>回應

| 狀態碼 | 回應本文 | 描述 |
|-|-|-|
| 200/確定 | 要求所建立或更新的關注清單專案 |  |
| 400/不正確的要求 |  | 格式不正確的要求語法，要求參數無效 .。。 |
| 409/衝突 |  | 作業失敗，有相同別名的現有關注清單 |
|

## <a name="delete-a-watchlist-item"></a>刪除關注清單專案

此命令會刪除現有的關注清單專案。

### <a name="request-uri"></a>要求 URI
 (URI 是一行，可讓您輕鬆閱讀) 

| 方法 | 要求 URI |
|-|-|
| DELETE | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}/`<br>`watchlistitems/{{watchlistItemId}}?api-version={{api-version}}` |
|

### <a name="responses"></a>回應

| 狀態碼 | 回應本文 | 描述 |
|-|-|-|
| 200/確定 | 空白回應主體 |  |
| 204/沒有內容 | 空白回應主體 | 未刪除任何專案 |
| 400/不正確的要求 |  | 格式不正確的要求語法，要求參數無效 .。。 |
|

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何使用 Log Analytics API 在 Azure Sentinel 中管理 watchlists 和其專案。 若要深入了解 Azure Sentinel，請參閱下列文章：

- 深入瞭解 [watchlists](watchlists.md)
- 探索[AZURE SENTINEL API](/rest/api/securityinsights/)的其他用途
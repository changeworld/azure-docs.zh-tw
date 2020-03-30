---
title: 去現場 |Azure 應用商店
description: Go Live API 會起始供應項目即時上市程序。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: bf7bebf6e72e373811879a311d70255c29988ed6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288575"
---
<a name="go-live"></a>Go Live
=======

此 API 會啟動將應用程式推送至生產環境的程序。 此作業是通常是長時間執行。 此呼叫會使用 [Publish](./cloud-partner-portal-api-publish-offer.md) API 作業中的通知電子郵件清單。

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

<a name="uri-parameters"></a>URI 參數
--------------

|  **名稱**      |   **描述**                                                           | **資料類型** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | 要擷取之供應項目的發行者識別碼，例如 `contoso`       |  String       |
| offerId        | 要擷取之供應項目的供應項目識別碼                                   |  String       |
| api-version    | API 的最新版本                                                   |  Date         |
|  |  |  |


<a name="header"></a>頁首
------

|  **名稱**       |     **價值**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| 授權   | `Bearer YOUR_TOKEN` |
|  |  |


<a name="body-example"></a>本文範例
------------

### <a name="response"></a>回應

`Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>回應標頭

|  **名稱**             |      **價值**                                                            |
|  --------             |      ----------                                                           |
| Operation-Location    |  URL，可進行查詢以判斷作業的目前狀態            |
|  |  |


### <a name="response-status-codes"></a>回應狀態碼

| **代碼** |  **描述**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted`：已順利接受要求。 回應會包含用來追蹤作業狀態的位置。 |
|  400     | `Bad/Malformed request` - 在回應本文中找到其他錯誤資訊。 |
|  404     |  `Not found` - 指定的實體不存在。                                       |
|  |  |

---
title: 參考 - Azure API 管理資源記錄
description: Azure API 管理 GatewayLogs 資源記錄的結構描述參考
services: api-management
author: dlepow
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: apimpm
ms.openlocfilehash: 064ac21e01239f7be3ddca9e48089ce880c6af58
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93379810"
---
# <a name="reference-api-management-resource-log-schema"></a>參考：API 管理資源記錄結構描述

本文提供 Azure API 管理 GatewayLogs 資源記錄的結構描述參考。 記錄項目也包含 [最上層通用結構描述](../azure-monitor/platform/resource-logs-schema.md#top-level-common-schema)中的欄位。

若要在 API 管理中啟用資源記錄收集，請參閱[監視已發佈的 API](api-management-howto-use-azure-monitor.md#resource-logs)。

## <a name="gatewaylogs-schema"></a>GatewayLogs 結構描述

系統會針對每個 API 要求記錄下列屬性。

| 屬性  | 類型 | 描述 |
| ------------- | ------------- | ------------- |
| method | 字串 | 連入要求的 HTTP 方法 |
| url | 字串 | 連入要求的 URL |
| responseCode | integer | 傳送至用戶端之 HTTP 回應的狀態碼 |
| responseSize | integer | 在要求處理期間傳送至用戶端的位元組數目 | 
| 快取 | 字串 | 要求處理中 API 管理快取參與的狀態 (符合、遺漏、無) | 
| apiId | 字串 | 目前要求的 API 實體識別碼 | 
| operationId | 字串 | 目前要求的作業實體識別碼 | 
| clientProtocol | 字串 | 連入要求的 HTTP 通訊協定版本 |
| clientTime | integer | 整體用戶端 IO (連線、傳送及接收位元組) 所耗費的毫秒數 | 
| apiRevision | 字串 | 目前要求的 API 修訂 | 
| clientTlsVersion| 字串 | 用戶端傳送要求所使用的 TLS 版本 |
| lastError | 物件 (object) | 如果是失敗的要求，則為上次要求處理錯誤的詳細資料 | 
| backendMethod | 字串 | 傳送至後端之要求的 HTTP 方法 |
| backendUrl | 字串 | 傳送至後端之要求的 URL |
| backendResponseCode | integer | 從後端接收之 HTTP 回應的代碼 |
| backedProtocol | 字串 | 傳送至後端之要求的 HTTP 通訊協定版本 |
| backendTime | integer | 整體後端 IO (連線、傳送及接收位元組) 所耗費的毫秒數 | 


## <a name="next-steps"></a>後續步驟

* 如需有關在 API 管理中監視 API 的詳細資訊，請參閱[監視已發佈的 API](api-management-howto-use-azure-monitor.md)
* 深入了解 [Azure 資源記錄的通用和服務特定結構描述](../azure-monitor/platform/resource-logs-schema.md)


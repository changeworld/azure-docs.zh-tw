---
title: 包含檔案
description: 包含檔案
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.topic: include
ms.date: 05/09/2019
ms.author: sharadag
ms.custom: include file
ms.openlocfilehash: 148ec3eccce71ab7a4a6c1391c0fa4753c248bd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335088"
---
| 資源 | 限制 |
| --- | --- |
| 每個訂閱的 Azure 前門資源 | 100 |
| 前端主機，包括每個資源的自訂域 | 500 |
| 每個資源的路由規則 | 500 |
| 每個資源的後端池 | 50 |
| 每個後端池的後端 | 100 |
| 要符合路由規則的路徑模式 | 25 |
| 單個緩存清除調用中的 URL | 100 |
| 每個原則的自訂 Web 應用程式防火牆規則 | 100 |
| 每個訂閱的 Web 應用程式防火牆策略 | 100 |
| Web 應用程式防火牆匹配每個自訂規則的條件 | 10 |
| Web 應用程式防火牆 IP 位址範圍每個匹配條件 | 600 |
| Web 應用程式防火牆字串匹配每個匹配條件的值 | 10 |
| Web 應用程式防火牆字串匹配值長度 | 256 |
| Web 應用程式防火牆 POST 正文參數名稱長度 | 256 |
| Web 應用程式防火牆 HTTP 標頭名稱長度 | 256 |
| Web 應用程式防火牆 Cookie 名稱長度 | 256 |
| Web 應用程式防火牆 HTTP 要求正文大小已檢查 | 128 KB |
| Web 應用程式防火牆自訂回應正文長度 | 2 KB |

### <a name="timeout-values"></a>逾時值
#### <a name="client-to-front-door"></a>用戶端至 Front Door
* Front Door 的閒置 TCP 連線逾時為 61 秒。

#### <a name="front-door-to-application-back-end"></a>前門到應用程式後端
* 如果回應是塊狀回應，則在接收第一個區塊時返回 200。
* 將 HTTP 要求轉發到後端後，前門將等待後端的第一個資料包 30 秒。 然後，它將 503 錯誤返回給用戶端。 此值可通過 API 中的欄位"發送 RecvTimeout 秒"進行配置。
    * 對於緩存方案，此超時是不可配置的，因此，如果緩存了請求，並且從前門或後端的第一個資料包需要 30 多秒的時間，則將 504 錯誤返回到用戶端。 
* 從後端接收第一個資料包後，前門在空閒超時中等待 30 秒。 然後，它將 503 錯誤返回給用戶端。 此超時值不可配置。
* 前端 TCP 會話超時為 90 秒。

### <a name="upload-and-download-data-limit"></a>上傳和下載資料限制

|  | 帶分塊傳輸編碼 （CTE） | 無 HTTP 分塊 |
| ---- | ------- | ------- |
| **下載** | 下載大小沒有限制。 | 下載大小沒有限制。 |
| **上傳** |    只要每個 CTE 上載小於 2 GB，就沒有限制。 | 大小不能大於 2 GB。 |

### <a name="other-limits"></a>其他限制
* 最大 URL 大小 - 8，192 位元組 - 指定原始 URL 的最大長度（方案 + 主機名稱 + 埠 + 路徑 + URL 的查詢字串）
* 最大查詢字串大小 - 4，096 位元組 - 指定查詢字串的最大長度（以位元組為單位）。
* 來自運行狀況探測 URL 的最大 HTTP 回應標頭大小 - 4，096 位元組 - 指定運行狀況探測的所有回應標頭的最大長度。 

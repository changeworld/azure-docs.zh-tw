---
title: 包含檔案
description: 包含檔案
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: include
ms.date: 05/09/2019
ms.author: sharadag
ms.custom: include file
ms.openlocfilehash: 361fb18574e90fc46e45bff8914c51ee1afa2f96
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89411744"
---
| 資源 | 限制 |
| --- | --- |
| 每個訂用帳戶的 Azure Front Door 資源 | 100 |
| 每個資源的前端主機 (包括自訂網域) | 500 |
| 每個資源的路由規則 | 500 |
| 每個資源的後端集區 | 50 |
| 每個後端集區的後端 | 100 |
| 要符合路由規則的路徑模式 | 25 |
| 單一快取清除呼叫中的 URL | 100 |
| 每個原則的自訂 Web 應用程式防火牆規則 | 100 |
| 每個訂用帳戶的 Web 應用程式防火牆原則 | 100 |
| 每個自訂規則的 Web 應用程式防火牆比對條件 | 10 |
| 每個比對條件的 Web 應用程式防火牆 IP 位址範圍 | 600 |
| 每個比對條件的 Web 應用程式防火牆字串比對值 | 10 |
| Web 應用程式防火牆字串比對值長度 | 256 |
| Web 應用程式防火牆 POST 主體參數名稱長度 | 256 |
| Web 應用程式防火牆 HTTP 標頭名稱長度 | 256 |
| Web 應用程式防火牆 Cookie 名稱長度 | 256 |
| Web 應用程式防火牆 HTTP 要求本文大小已檢查 | 128 KB |
| Web 應用程式防火牆自訂回應主體長度 | 2 KB |

### <a name="timeout-values"></a>逾時值
#### <a name="client-to-front-door"></a>用戶端至 Front Door
* Front Door 的閒置 TCP 連線逾時為 61 秒。

#### <a name="front-door-to-application-back-end"></a>Front Door 至應用程式後端
* 如果回應是區塊式回應，則若在收到第一個區塊時會傳回 200。
* 將 HTTP 要求轉送至後端之後，Front Door 會對後端的第一個封包等待 30 秒。 然後會將 503 錯誤傳回給用戶端。 此值可以透過 API 中的 sendRecvTimeoutSeconds 欄位來設定。
    * 針對快取案例，此逾時無法設定，因此如果快取要求，而且來自 Front Door 或後端的第一個封包耗時超過 30 秒，則會將 504 錯誤傳回給用戶端。 
* 後端收到第一個封包後，Front Door 會等候 30 秒 (閒置逾時)。 然後會將 503 錯誤傳回給用戶端。 此逾時值無法設定。
* Front Door 至後端 TCP 工作階段的逾時為 90 秒鐘。

### <a name="upload-and-download-data-limit"></a>上傳及下載資料限制

|  | 使用區塊傳輸編碼 (CTE) | 不使用 HTTP 區塊處理 |
| ---- | ------- | ------- |
| **下載** | 下載大小沒有限制。 | 下載大小沒有限制。 |
| **上傳** |    只要每個 CTE 上傳小於 2 GB 就沒有限制。 | 大小不能大於 2 GB。 |

### <a name="other-limits"></a>其他限制
* URL 大小上限 - 8192 個位元組 - 指定原始 URL 的長度上限 (配置 + 主機名稱 + 連接埠 + 路徑 + URL 的查詢字串)
* 查詢字串大小上限 - 4096 個位元組 - 指定查詢字串的長度上限 (以位元組為單位)。
* 來自健全狀態探查 URL 的 HTTP 回應標頭大小上限 - 4096 個位元組 - 指定健全狀態探查所有回應標頭的長度上限。 

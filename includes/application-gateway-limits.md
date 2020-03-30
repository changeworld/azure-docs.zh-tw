---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: a3fb3dbf026a696b9d472efcba139c371ff1e587
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334929"
---
| 資源 | 限制 | 附註 |
| --- | --- | --- |
| Azure 應用程式閘道 |每個訂閱 1，000 個 | |
| 前端 IP 配置 |2 |公用 1 個和私用 1 個 |
| 前端連接埠 |100<sup>1</sup> | |
| 後端位址集區 |100<sup>1</sup> | |
| 每個集區的後端伺服器 |1,200 | |
| HTTP 接聽程式 |200<sup>1</sup> |限制為路由流量的 100 個活動攔截器。 活動攔截器 = 攔截器總數 - 未活動的攔截器。<br>如果路由規則中的預設配置設置為路由流量（例如，它有一個攔截器、後端池和 HTTP 設置），則該配置也算作攔截器。|
| HTTP 負載平衡規則 |100<sup>1</sup> | |
| 後端 HTTP 設定 |100<sup>1</sup> | |
| 每個閘道的執行個體 |V1 SKU - 32<br>V2 SKU - 125 | |
| SSL 憑證 |100<sup>1</sup> |每個 HTTP 攔截器 1 個 |
| 最大 SSL 憑證大小 |V1 SKU - 10 KB<br>V2 SKU - 16 KB| |
| 驗證憑證 |100 | |
| 受信任的根憑證 |100 | |
| 請求超時最小值 |1 秒 | |
| 請求超時最大值 |24 小時 | |
| 站台數目 |100<sup>1</sup> |每個 HTTP 攔截器 1 個 |
| 每個接聽程式的 URL 對應 |1 | |
| 每個 URL 對應的路徑型規則數上限|100||
| 重新導向組態 |100<sup>1</sup>| |
| 並行的 WebSocket 連線 |中型閘道 20k<br> 大型閘道 50k| |
| URL 長度上限|32KB| |
| HTTP/2 的最大標頭大小 |4KB| |
| 最大檔上傳大小，標準 |2 GB | |
| WAF 檔案上傳大小上限 |V1 中型 WAF 閘道，100 MB<br>V1 大型 WAF 閘道，500 MB<br>V2 WAF，750 MB| |
| WAF 體大小限制，無檔|128 KB||
| 最大 WAF 自訂規則|100||
| 最大 WAF 排除|100||

<sup>1</sup>在啟用 WAF 的 SKU 的情況下，我們建議您將資源數限制為 40，以實現最佳性能。

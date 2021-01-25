---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: 4c7c68bf690097004f2f3310a8d89ce954d7f87f
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98612907"
---
| 資源 | 限制 | 注意 |
| --- | --- | --- |
| Azure 應用程式閘道 |每個訂用帳戶 1,000 個 | |
| 前端 IP 設定 |2 |公用 1 個和私用 1 個 |
| 前端連接埠 |100<sup>1</sup> | |
| 後端位址集區 |100<sup>1</sup> | |
| 每個集區的後端伺服器 |1,200 | |
| HTTP 接聽程式 |200<sup>1</sup> |限制為 100 個可路由流量的使用中接聽程式。 使用中接聽程式 = 接聽程式總數 - 非使用中接聽程式。<br>如果路由規則內的預設設定是設為路由流量 (例如，其中具有接聽程式、後端集區和 HTTP 設定)，則也會將其視為接聽程式。|
| HTTP 負載平衡規則 |100<sup>1</sup> | |
| 後端 HTTP 設定 |100<sup>1</sup> | |
| 每個閘道的執行個體 |V1 SKU - 32<br>V2 SKU - 125 | |
| SSL 憑證 |100<sup>1</sup> |每個 HTTP 接聽程式 1 個 |
| SSL 憑證大小上限 |V1 SKU - 10 KB<br>V2 SKU - 16 KB| |
| 驗證憑證 |100 | |
| 受信任的根憑證 |100 | |
| 要求逾時下限 |1 秒 | |
| 要求逾時上限 |24 小時 | |
| 站台數目 |100<sup>1</sup> |每個 HTTP 接聽程式 1 個 |
| 每個接聽程式的 URL 對應 |1 | |
| 每個 URL 對應的路徑型規則數上限|100||
| 重新導向組態 |100<sup>1</sup>| |
| 重新產生規則集的數目 |400| |
| 每個重新產生規則集的標頭或 URL 設定數目|40| |
| 每個重新產生規則集的條件數目|40| |
| 並行的 WebSocket 連線 |中型閘道 20k<br> 大型閘道 50k| |
| URL 長度上限|32KB| |
| HTTP/2 的標頭大小上限 |4KB| |
| 標準檔案上傳大小上限 |2 GB | |
| WAF 檔案上傳大小上限 |V1 中型 WAF 閘道：100 MB<br>V1 大型 WAF 閘道：500 MB<br>V2 WAF：750 MB| |
| WAF 主體大小上限 (不含檔案)|128 KB||
| WAF 自訂規則上限|100||
| 每個應用程式閘道的 WAF 排除項目上限|40||

<sup>1</sup> 若 SKU 啟用了 WAF，您必須將資源數限制為 40。

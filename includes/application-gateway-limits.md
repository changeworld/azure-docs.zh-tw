---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: 6ab6c4c2051ccd2fbb22c383b9ca0af53ceb13d3
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2020
ms.locfileid: "77054900"
---
| 資源 | 預設/最大限制 | 附註 |
| --- | --- | --- |
| Azure 應用程式閘道 |每個訂用帳戶1000 | |
| 前端 IP 設定 |2 |公用 1 個和私用 1 個 |
| 前端連接埠 |100<sup>1</sup> | |
| 後端位址集區 |100<sup>1</sup> | |
| 每個集區的後端伺服器 |1,200 | |
| HTTP 接聽程式 |100<sup>1</sup> | |
| HTTP 負載平衡規則 |100<sup>1</sup> | |
| 後端 HTTP 設定 |100<sup>1</sup> | |
| 每個閘道的執行個體 |V1 SKU-32<br>V2 SKU-125 | |
| SSL 憑證 |100<sup>1</sup> |每個 HTTP 接聽程式1個 |
| SSL 憑證大小上限 |V1 SKU-10 KB<br>V2 SKU-16 KB| |
| 驗證憑證 |100 | |
| 受信任的根憑證 |100 | |
| 要求超時下限 |1 秒 | |
| 要求超時上限 |24 小時 | |
| 站台數目 |100<sup>1</sup> |每個 HTTP 接聽程式1個 |
| 每個接聽程式的 URL 對應 |1 | |
| 每個 URL 對應的路徑型規則數上限|100||
| 重新導向組態 |100<sup>1</sup>| |
| 並行的 WebSocket 連線 |中型閘道20k<br> 大型閘道50k| |
| URL 長度上限|32KB| |
| HTTP/2 的標頭大小上限 |4KB| |
| 檔案上傳大小上限，標準 |2 GB | |
| WAF 檔案上傳大小上限 |v1 中型 WAF 閘道，100 MB<br>v1 大型 WAF 閘道，500 MB<br>v2 WAF，750 MB| |
| WAF 的主體大小限制，不含檔案|128 KB||
| 最大 WAF 自訂規則|100||
| 最大 WAF 排除專案|100||

<sup>1</sup>如果已啟用 WAF 的 sku，我們建議您將資源數目限制為40，以獲得最佳效能。

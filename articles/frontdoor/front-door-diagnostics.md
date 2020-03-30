---
title: Azure 前門中的監視指標和日誌*微軟文檔
description: 本文介紹 Azure 前門支援的不同指標和訪問日誌
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sharadag
ms.openlocfilehash: b935355cce36a6e26b168db286ab40248f8f0f68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471722"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door"></a>監視 Azure 前門中的指標和日誌

通過使用 Azure 前門，可以通過以下方式監視資源：

- **指標**. Azure 前門目前有七個指標用於查看效能計數器。
- **日誌**。 活動和診斷日誌允許從資源中保存或使用性能、訪問和其他資料，以便進行監視。

### <a name="metrics"></a>計量

指標是某些 Azure 資源的一項功能，允許您在門戶中查看效能計數器。 以下是可用的前門指標：

| 計量 | 計量顯示名稱 | 單位 | 維度 | 描述 |
| --- | --- | --- | --- | --- |
| RequestCount | 要求計數 | Count | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 由 Front Door 提供服務的用戶端要求數。  |
| RequestSize | 要求大小 | 位元組 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 從用戶端傳送到 Front Door 之要求的位元組數。 |
| ResponseSize | 回應大小 | 位元組 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 從 Front Door 傳送到用戶端之回應的位元組數。 |
| TotalLatency | 延遲總計 | 毫秒 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 從前門收到的用戶端請求計算的時間，直到用戶端從前門確認最後一個回應位元組。 |
| BackendRequestCount | 後端要求計數 | Count | HttpStatus</br>HttpStatusGroup</br>後端 | 從 Front Door 傳送至後端的要求數。 |
| BackendRequestLatency | 後端要求延遲 | 毫秒 | 後端 | 從 Front Door 傳送要求到後端一直到 Front Door 從後端接收最後回應位元組所經歷的時間。 |
| BackendHealthPercentage | 後端健康情況百分比 | 百分比 | 後端</br>BackendPool | 從 Front Door 到後端的健康情況探查成功百分比。 |
| WebApplicationFirewallRequestCount | Web 應用程式防火牆要求計數 | Count | PolicyName</br>RuleName</br>動作 | Front Door 應用程式層安全性處理的用戶端要求數。 |

## <a name="activity-logs"></a><a name="activity-log"></a>活動日誌

活動日誌提供有關前門上執行的操作的資訊。 它們還確定前門上執行的任何寫入操作（放置、發佈或刪除）的內容、人員以及時間。

>[!NOTE]
>活動日誌不包括讀取（獲取）操作。 它們也不包括使用 Azure 門戶或原始管理 API 執行的操作。

訪問前門的活動日誌或 Azure 監視器中 Azure 資源的所有日誌。 檢視活動記錄：

1. 選擇前門實例。
2. 選擇**活動日誌**。

    ![活動記錄檔](./media/front-door-diagnostics/activity-log.png)

3. 選擇篩選範圍，然後選擇 **"應用**"。

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>診斷日誌
診斷日誌提供有關對審核和故障排除非常重要的操作和錯誤的豐富資訊。 診斷日誌與活動日誌不同。

活動日誌提供對 Azure 資源執行操作的見解。 診斷日誌提供對資源執行的操作的見解。 有關詳細資訊，請參閱[Azure 監視器診斷日誌](../azure-monitor/platform/platform-logs-overview.md)。

![診斷記錄](./media/front-door-diagnostics/diagnostic-log.png)

要配置前門的診斷日誌：：

1. 選擇 Azure 前門。

2. 選擇**診斷設置**。

3. 選取 [開啟診斷]****。 將診斷日誌和指標以及指標存檔到存儲帳戶，將它們資料流到事件中心，或將它們發送到 Azure 監視器日誌。

前門當前提供診斷日誌（每小時批次處理）。 診斷日誌提供單個 API 請求，每個條目具有以下架構：

| 屬性  | 描述 |
| ------------- | ------------- |
| 後端主機名稱 | 如果請求被轉發到後端，則此欄位表示後端的主機名稱。 如果請求重定向或轉發到區域緩存（為路由規則啟用緩存時），此欄位將為空。 |
| 緩存狀態 | 對於緩存方案，此欄位定義 POP 處的緩存命中/錯過 |
| ClientIp | 發出要求之用戶端的 IP 位址。 如果請求中有 X-前轉 -For 標頭，則從同一標頭中選取用戶端 IP。 |
| 用戶端埠 | 發出請求的用戶端的 IP 埠。 |
| HttpMethod | 要求使用的 HTTP 方法。 |
| HttpStatusCode | 從代理返回的 HTTP 狀態碼。 |
| HttpStatus 詳細資訊 | 在請求上生成狀態。 可在狀態引用表中找到此字串值的含義。 |
| HttpVersion | 請求或連接的類型。 |
| POP | 請求著陸邊緣的簡短名稱。 |
| 請求位元組 | HTTP 要求消息的大小（以位元組為單位），包括請求標頭和請求正文。 |
| 請求庫裡 | 接收之要求的 URI。 |
| 回應位元組 | 後端伺服器作為回應發送的位元組。  |
| 路由規則名稱 | 請求匹配的路由規則的名稱。 |
| 安全協定 | 請求使用的 TLS/SSL 協定版本，如果沒有加密，則為 null。 |
| 已發送到原始盾 | 布林欄位表示第一個環境中是否存在緩存未命中，並且請求已發送到區域緩存。 如果路由規則是重定向或未啟用緩存時，請忽略此欄位。 |
| TimeTaken | 操作所花的時間長度（以毫秒為單位）。 |
| 跟蹤參考 | 標識前門提供的請求的唯一引用字串，也作為 X-Azure-Ref 標頭髮送到用戶端。 需要搜索特定請求的訪問日誌中的詳細資訊。 |
| UserAgent | 用戶端使用的瀏覽器類型。 |

**注：** 對於各種路由配置和流量行為，某些欄位（如後端主機名稱、緩存狀態、發送到原體遮罩和 POP 欄位）可能會以不同的值回應。 下表解釋了不同的值，這些欄位將具有各種方案：

| 案例 | 日誌條目計數 | POP | 後端主機名稱 | 已發送到原始盾 | 緩存狀態 |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| 未啟用緩存的路由規則 | 1 | 邊緣 POP 代碼 | 轉發請求的後端 | False | CONFIG_NOCACHE |
| 啟用緩存的路由規則。 緩存命中邊緣 POP | 1 | 邊緣 POP 代碼 | 空白 | False | 打 |
| 啟用緩存的路由規則。 在邊緣 POP 處緩存未命中，但在父緩存 POP 處命中緩存 | 2 | 1. 邊緣 POP 代碼</br>2. 父緩存 POP 代碼 | 1. 父緩存 POP 主機名稱</br>2. 空 | 1. 真實</br>2. 假 | 1. 小姐</br>2. PARTIAL_HIT |
| 啟用緩存的路由規則。 邊緣緩存和父緩存 POP 的緩存錯過 | 2 | 1. 邊緣 POP 代碼</br>2. 父緩存 POP 代碼 | 1. 父緩存 POP 主機名稱</br>2. 有助於填充緩存的後端 | 1. 真實</br>2. 假 | 1. 小姐</br>2. 小姐 |


## <a name="next-steps"></a>後續步驟

- [建立 Front Door 設定檔](quickstart-create-front-door.md)
- [前門的工作原理](front-door-routing-architecture.md)

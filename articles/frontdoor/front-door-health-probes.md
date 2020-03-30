---
title: Azure 前門 - 後端運行狀況監控 |微軟文檔
description: 本文可説明您瞭解 Azure 前門如何監視後端的運行狀況
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: e2e656c395f1a31c1f5ebbd46d5a18a046f854f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471569"
---
# <a name="health-probes"></a>健康狀態探查

為了確定每個後端從給定的前門環境的運行狀況和鄰近性，每個前門環境都會定期向每個配置的後端發送綜合 HTTP/HTTPS 請求。 接著，Front Door 會使用來自這些探查的回應，來判斷它應該要路由傳送實際用戶端要求的「最佳」後端。 

> [!WARNING]
> 由於前門在全球有許多邊緣環境，因此運行狀況探測器向後端請求的音量可能相當高 - 根據配置的運行狀況探測頻率，每分鐘從 25 個請求到每分鐘高達 1200 個請求。 預設探測頻率為 30 秒，後端上的探頭體積應約為每分鐘 200 個請求。

## <a name="supported-protocols"></a>支援的通訊協定

Front Door 支援透過 HTTP 或 HTTPS 通訊協定傳送探查。 這些探查透過設定用於路由傳送用戶端要求之相同的 TCP 連接埠傳送，並且無法覆寫。

## <a name="supported-http-methods-for-health-probes"></a>支援運行狀況探測器的 HTTP 方法

前門支援以下用於發送運行狀況探測器的 HTTP 方法：

1. **獲取：** GET 方法意味著檢索請求 URI 標識的任何資訊（以實體的形式）。
2. **頭：** HEAD 方法與 GET 相同，只是伺服器不得在回應中返回郵件內文。 預設情況下，對於新的前門設定檔，探頭方法設置為 HEAD。

> [!NOTE]
> 為了降低後端的負載和成本，前門建議使用 HEAD 請求進行運行狀況探頭。

## <a name="health-probe-responses"></a>健全狀況探查回應

| 回應  | 描述 | 
| ------------- | ------------- |
| 判斷健全狀況  |  200 OK 狀態碼表示後端狀況良好。 所有其他項目會視為失敗。 如果基於任何原因 (包括網路失敗) 未收到有效的 HTTP 探查回應，則探查將會視為失敗。|
| 測量延遲  | 延遲是從目前我們傳送探查要求的那一刻起，到我們收到回應的最後一個位元組之前測量的時鐘時間。 我們針對每個要求使用新的 TCP 連線，因此該度量不會偏向使用現有連線的後端。  |

## <a name="how-front-door-determines-backend-health"></a>Front Door 如何判斷後端的健康狀態

Azure 前門在所有演算法中使用相同的三步過程來確定運行狀況。

1. 排除已停用後端。

2. 排除有健康情況探查錯誤的後端：
    * 藉由查看最後 _n_ 個健全狀況探查回應來完成此選取。 如果至少 _x_ 狀況良好，則後端會視為狀況良好。

    * _n_通過在負載平衡設置中更改 SampleSize 屬性進行配置。

    * _x_是通過在負載平衡設置中更改成功採樣必需屬性來配置的。

3. 在後端集區中狀況良好的集合中，Front Door 還會測量並維護每個後端的延遲 (來回時間)。


## <a name="complete-health-probe-failure"></a>完整的健全狀況探查失敗

如果後端集區中的每個後端的健全狀況探查失敗，那麼 Front Door 會將所有的後端視為狀況良好，並在所有後端的循環配置資源散發中路由傳送流量。

一旦任何後端恢復到正常狀態，前門將恢復正常的負載平衡演算法。

## <a name="disabling-health-probes"></a>禁用運行狀況探測器

如果後端池中只有一個後端，則可以選擇禁用運行狀況探測器，以減少應用程式後端的負載。 即使您在後端池中有多個後端，但只有一個後端處於啟用狀態，您也可以禁用運行狀況探測。

## <a name="next-steps"></a>後續步驟

- 了解如何[建立 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的運作方式](front-door-routing-architecture.md)。

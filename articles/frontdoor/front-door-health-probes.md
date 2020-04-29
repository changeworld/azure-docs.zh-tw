---
title: Azure Front 門板-後端健康情況監視 |Microsoft Docs
description: 本文可協助您瞭解 Azure Front 門板如何監視後端的健康情況
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79471569"
---
# <a name="health-probes"></a>健康狀態探查

為了從指定的前端環境判斷每個後端的健全狀況和鄰近性，每個 Front 門環境都會定期將綜合 HTTP/HTTPS 要求傳送給每個已設定的後端。 接著，Front Door 會使用來自這些探查的回應，來判斷它應該要路由傳送實際用戶端要求的「最佳」後端。 

> [!WARNING]
> 由於前端有許多邊緣環境，因此後端的健康情況探查要求量可能非常高，範圍從25個要求到每分鐘的最高可達1200個要求，視所設定的健康情況探查頻率而定。 預設探查頻率為30秒，後端上的探查磁片區應為每分鐘大約200個要求。

## <a name="supported-protocols"></a>支援的通訊協定

Front Door 支援透過 HTTP 或 HTTPS 通訊協定傳送探查。 這些探查透過設定用於路由傳送用戶端要求之相同的 TCP 連接埠傳送，並且無法覆寫。

## <a name="supported-http-methods-for-health-probes"></a>健康情況探查支援的 HTTP 方法

Front 門支援下列傳送健康情況探查的 HTTP 方法：

1. **GET：** GET 方法表示會以要求 URI 識別任何資訊（以實體的形式）。
2. **HEAD：** HEAD 方法與 GET 相同，不同之處在于伺服器不能傳迴響應中的訊息內文。 針對新的 Front 門板設定檔，探查方法預設會設定為 HEAD。

> [!NOTE]
> 針對較低的後端負載和成本，Front 門建議使用健康狀態探查的 HEAD 要求。

## <a name="health-probe-responses"></a>健全狀況探查回應

| 回應  | 描述 | 
| ------------- | ------------- |
| 判斷健全狀況  |  200 OK 狀態碼表示後端狀況良好。 所有其他項目會視為失敗。 如果基於任何原因 (包括網路失敗) 未收到有效的 HTTP 探查回應，則探查將會視為失敗。|
| 測量延遲  | 延遲是從目前我們傳送探查要求的那一刻起，到我們收到回應的最後一個位元組之前測量的時鐘時間。 我們針對每個要求使用新的 TCP 連線，因此該度量不會偏向使用現有連線的後端。  |

## <a name="how-front-door-determines-backend-health"></a>Front Door 如何判斷後端的健康狀態

Azure Front 門板會在所有演算法上使用相同的三步驟程式來判斷健全狀況。

1. 排除已停用後端。

2. 排除有健康情況探查錯誤的後端：
    * 藉由查看最後 _n_ 個健全狀況探查回應來完成此選取。 如果至少 _x_ 狀況良好，則後端會視為狀況良好。

    * _n_是藉由變更負載平衡設定中的 SampleSize 屬性來設定。

    * _x_是藉由變更負載平衡設定中的 SuccessfulSamplesRequired 屬性來設定。

3. 在後端集區中狀況良好的集合中，Front Door 還會測量並維護每個後端的延遲 (來回時間)。


## <a name="complete-health-probe-failure"></a>完整的健全狀況探查失敗

如果後端集區中的每個後端的健全狀況探查失敗，那麼 Front Door 會將所有的後端視為狀況良好，並在所有後端的循環配置資源散發中路由傳送流量。

一旦任何後端回到狀況良好狀態，Front 門板就會繼續正常的負載平衡演算法。

## <a name="disabling-health-probes"></a>停用健全狀況探查

如果您的後端集區中有單一後端，您可以選擇停用健全狀況探查以減少應用程式後端的負載。 即使您在後端集區中有多個後端，但只有其中一個處於 [已啟用] 狀態，您也可以停用健康情況探查。

## <a name="next-steps"></a>後續步驟

- 了解如何[建立 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的運作方式](front-door-routing-architecture.md)。

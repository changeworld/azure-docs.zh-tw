---
title: Azure Front Door 後端健康情況監視 |Microsoft Docs
description: 本文可協助您瞭解 Azure Front Door 如何監視後端的健康情況
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 4cbeea8ad20d41daff3d4ad086a36df5e988991f
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449250"
---
# <a name="health-probes"></a>健康狀態探查

為了判斷特定 Front Door 環境的每個後端的健全狀況和鄰近性，每個 Front Door 環境都會定期將綜合 HTTP/HTTPS 要求傳送至每個已設定的後端。 Front Door 接著會使用探查中的這些回應，來判斷用來路由傳送用戶端要求的「最佳」後端資源。 

> [!WARNING]
> 由於 Front Door 有全球許多邊緣環境，因此後端的健康情況探查磁片區可能相當高-範圍從25個要求，每分鐘最高可達每分鐘1200個要求，視設定的健康情況探查頻率而定。 如果預設探查頻率為30秒，則您後端的探查磁片區應大約每分鐘200個要求。

## <a name="supported-protocols"></a>支援的通訊協定

Front Door 支援透過 HTTP 或 HTTPS 通訊協定傳送探查。 這些探查透過設定用於路由傳送用戶端要求之相同的 TCP 連接埠傳送，並且無法覆寫。

## <a name="supported-http-methods-for-health-probes"></a>健康情況探查支援的 HTTP 方法

Front Door 支援下列 HTTP 方法來傳送健康情況探查：

1. **取得：** GET 方法表示以實體的形式取得任何 (的資訊，) 由要求 URI 所識別。
2. **HEAD：** HEAD 方法與 GET 相同，不同之處在于伺服器不能在回應中傳回訊息主體。 針對新的 Front Door 設定檔，探查方法預設會設定為 HEAD。

> [!NOTE]
> 針對較低的負載和後端成本，Front Door 建議使用健康情況探查的 HEAD 要求。

## <a name="health-probe-responses"></a>健全狀況探查回應

| 回應  | 描述 | 
| ------------- | ------------- |
| 判斷健全狀況  |  200 OK 狀態碼表示後端狀況良好。 所有其他項目會視為失敗。 如果基於任何原因 (包括網路失敗) 不會收到探查的有效 HTTP 回應，則會將探查視為失敗。|
| 測量延遲  | 延遲是從目前我們傳送探查要求的那一刻起，到我們收到回應的最後一個位元組之前測量的時鐘時間。 我們會針對每個要求使用新的 TCP 連線，因此這項測量不會因為現有的暖連線而偏向後端。  |

## <a name="how-front-door-determines-backend-health"></a>Front Door 如何判斷後端的健康狀態

Azure Front Door 在所有演算法中使用相同的三步驟程式來判斷健全狀況。

1. 排除已停用後端。

2. 排除有健康情況探查錯誤的後端：
    * 藉由查看最後 _n_ 個健全狀況探查回應來完成此選取。 如果至少 _x_ 狀況良好，則後端會視為狀況良好。

    * _n_ 是藉由變更負載平衡設定中的 SampleSize 屬性來設定。

    * _x_ 的設定方式是變更負載平衡設定中的 SuccessfulSamplesRequired 屬性。

3. 針對後端集區中狀況良好的後端集，Front Door 額外的量值，並維護每個後端 (來回時間) 的延遲。


## <a name="complete-health-probe-failure"></a>完整的健全狀況探查失敗

如果後端集區中的每個後端的健全狀況探查失敗，那麼 Front Door 會將所有的後端視為狀況良好，並在所有後端的循環配置資源散發中路由傳送流量。

一旦任何後端恢復狀況良好狀態，Front Door 將會恢復正常的負載平衡演算法。

## <a name="disabling-health-probes"></a>停用健康情況探查

如果您的後端集區中有單一後端，您可以選擇停用健康情況探查，減少應用程式後端的負載。 即使您在後端集區中有多個後端，但只有其中一個處於啟用狀態時，您可以停用健康情況探查。

## <a name="next-steps"></a>後續步驟

- 了解如何[建立 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的運作方式](front-door-routing-architecture.md)。

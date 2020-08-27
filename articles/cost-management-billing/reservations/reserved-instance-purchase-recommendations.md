---
title: Azure 保留建議
description: 了解 Azure 保留建議。
author: banders
ms.author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 813cf91195a43a2fc68236febe114c67eb5a3378
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684604"
---
# <a name="reservation-recommendations"></a>保留建議

Azure 保留執行個體 (RI) 購買建議是透過 Azure 耗用量[保留建議 API](/rest/api/consumption/reservationrecommendations)、[Azure Advisor](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)，以及 Azure 入口網站中的保留購買體驗來提供的。

下列步驟定義了計算建議的方法：

1. 建議引擎會針對您在過去 7 天、30 天以及 60 天指定範圍內，評估資源的每小時使用量。
2. 根據使用量資料，引擎會在使用和不使用保留的情況下來模擬您的成本。
3. 系統會針對不同的數量來模擬成本，並建議節省最多成本的數量。
4. 如果您的資源會定期關閉，則模擬找不到任何節省成本，因此不會提供任何購買建議。

## <a name="recommendations-in-the-azure-portal"></a>Azure 入口網站中的建議

保留購買建議也會顯示在 Azure 入口網站的購買體驗中。 建議會連同 [建議的數量] 一起顯示。 購買時，Azure 建議的數量將可為您省下最多成本。 雖然您可以購買任何想要的數量，但如果您購買不同的數量，就無法省下最多成本。

我們將透過一些範例了解其原因。

下列範例影像顯示選取的建議，我們可以看到，Azure 建議的購買數量為 6。

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" alt-text="顯示保留購買建議的範例" lightbox="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" :::

當您選取 [查看詳細資料] 連結時，會顯示關於建議的詳細資訊。 下圖顯示建議的詳細資料。 建議的數量會根據您的歷史使用量，計算出最高的可能使用量。 如果您的使用量不一致，則建議可能不會有 100% 的使用率。 在範例中請注意，使用率是隨時間而波動的。 其中顯示保留的成本、可節省的成本和使用率百分比。

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details.png" alt-text="顯示保留購買建議詳細資料的範例" :::

當您增加或減少超出建議的保留數量時，圖表和估計值會隨之變更。 增加保留數量時，您的節省金額將會降低，因為您的保留使用率會下降。 換句話說，您將需要為未完全使用的保留付費。

如果您減少保留數量，您的節省金額也會降低。 雖然您會有較高的使用率，但在某些期間可能會有無法完全涵蓋使用量的保留。 使用超過保留數量的使用量，將會使用較昂貴的隨用隨付資源。 下列範例影像說明該要點。 我們已將保留數量手動縮減為 4。 保留使用率會增加，但整體節省金額會降低，因為有隨用隨付成本存在。

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details-changed.png" alt-text="顯示已變更保留購買建議詳細資料的範例" :::

若要讓保留提供最大的成本節省金額，請盡可能依據建議購買保留。

## <a name="recommendations-in-azure-advisor"></a>Azure Advisor 中的建議

Azure Advisor 會提供保留購買建議。 請記住下列幾點：

- Advisor 只具有單一訂用帳戶範圍建議。
- 建議是根據過去 30 天的使用趨勢而計算得出的。
- 建議數量和節省金額以 3 年期的保留為準。 如果服務未銷售 3 年期的保留，則會使用 1 年期的保留價格來計算建議。
- 建議會將您的隨選使用費率可能有的任何特殊折扣納入考量。
- 如果您購買共用範圍的保留，Advisor 保留購買建議最多可能需要 30 天才會消失。

## <a name="other-expected-api-behavior"></a>其他預期的 API 行為

- 如果您使用 7 天的回顧期間，當 VM 關閉超過一天時，您可能無法取得建議。

## <a name="next-steps"></a>後續步驟

- 了解 [Azure 保留折扣如何套用至虛擬機器](../manage/understand-vm-reservation-charges.md)。

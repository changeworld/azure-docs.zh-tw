---
title: Azure 保留建議
description: 了解 Azure 保留建議。
author: banders
ms.author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 4f6187ccb143f065fed236495128add7a2ab1ee4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98928539"
---
# <a name="reservation-recommendations"></a>保留建議

Azure 保留執行個體 (RI) 購買建議是透過 Azure 耗用量[保留建議 API](/rest/api/consumption/reservationrecommendations)、[Azure Advisor](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)，以及 Azure 入口網站中的保留購買體驗來提供的。

下列步驟定義了計算建議的方法：

1. 建議引擎會針對您在過去 7 天、30 天以及 60 天指定範圍內，評估資源的每小時使用量。
2. 根據使用量資料，引擎會在使用和不使用保留的情況下來模擬您的成本。
3. 系統會針對不同的數量來模擬成本，並建議節省最多成本的數量。
4. 如果您的資源會定期關閉，則模擬找不到任何節省成本，因此不會提供任何購買建議。
5. 建議計算包含任何您可能依需求使用費率的特殊折扣。

## <a name="recommendations-in-the-azure-portal"></a>Azure 入口網站中的建議

保留購買建議也會顯示在 Azure 入口網站的購買體驗中。 建議會連同 [建議的數量] 一起顯示。 購買時，Azure 建議的數量將可為您省下最多成本。 雖然您可以購買任何您想要的數量，但如果您購買不同的數量，節省的金額將不會是最佳的。

我們將透過一些範例了解其原因。

下列範例影像顯示選取的建議，我們可以看到，Azure 建議的購買數量為 6。

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" alt-text="顯示保留購買建議的範例" lightbox="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" :::

當您選取 [ **查看詳細資料**] 時，會顯示有關建議的詳細資訊。 下圖顯示建議的詳細資料。 建議的數量是以可能的最高使用量來計算，而且會根據您的歷程記錄使用量來計算。 如果您的使用量不一致，則建議可能不會有 100% 的使用率。 在此範例中，請注意使用率 fluctuated 一段時間。 其中顯示保留的成本、可節省的成本和使用率百分比。

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details.png" alt-text="顯示保留購買建議詳細資料的範例 " :::

當您增加建議的數量時，圖表和估計值會變更。 藉由增加保留數量，您的節省量將會降低，因為您的保留使用量將會降低。 換句話說，您將需要為未完全使用的保留付費。

如果您降低保留數量，也會減少節省量。 雖然您會有較高的使用率，但在某些期間可能會有無法完全涵蓋使用量的保留。 使用超過保留數量的使用量，將會使用較昂貴的隨用隨付資源。 下列範例影像說明該要點。 我們已將保留數量手動縮減為 4。 保留使用量會增加，但整體節省量會降低，因為有隨用隨付成本。

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details-changed.png" alt-text="顯示已變更保留購買建議詳細資料的範例" :::

若要讓保留提供最大的成本節省金額，請盡可能依據建議購買保留。

## <a name="recommendations-in-azure-advisor"></a>Azure Advisor 中的建議

Azure Advisor 會提供保留購買建議。 請記住下列幾點：

- Advisor 只具有單一訂用帳戶範圍建議。 如果您想要查看整個計費範圍的建議 (計費帳戶或帳單設定檔) ，然後：
  -  在 [Azure 入口網站中，流覽至 [**保留**  >  **新增**]，然後選取您想要查看建議的類型。
- Advisor 中提供的建議可考慮您過去30天的使用趨勢。
- 建議數量和節省期適用于三年的保留（如果有的話）。 如果服務未銷售三年的保留期，則會使用一年保留價格來計算建議。
- 建議計算包含任何您可能依需求使用費率的特殊折扣。
- 如果您購買共用範圍的保留，建議程式保留購買建議最多可能需要五天的時間才會消失。

## <a name="other-expected-api-behavior"></a>其他預期的 API 行為

- 如果您使用 7 天的回顧期間，當 VM 關閉超過一天時，您可能無法取得建議。

## <a name="next-steps"></a>後續步驟

- 了解 [Azure 保留折扣如何套用至虛擬機器](../manage/understand-vm-reservation-charges.md)。

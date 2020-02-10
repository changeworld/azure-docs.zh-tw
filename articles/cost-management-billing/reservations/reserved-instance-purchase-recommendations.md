---
title: Azure 保留建議的建立方式
description: 了解如何針對虛擬機器建立 Azure 保留建議。
author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/28/2020
ms.author: banders
ms.openlocfilehash: a43e0c4d86bd47c953b50ab9fb1fd8df00e7df3d
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76851346"
---
# <a name="how-reservation-recommendations-are-created"></a>建立保留建議的方式

Azure 保留執行個體 (RI) 的購買建議是由 Azure 耗用量[保留建議 API](/rest/api/consumption/reservationrecommendations) 產生。 [Azure Advisor](../..//advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs) 也會使用此 API 的建議。 Advisor 會在 Azure 入口網站中顯示建議。

如果您有 VM 正在 Azure 中執行，您可以利用 RI 的折扣價格，並預付您的 VM 費用。 Microsoft 耗用量建議 API 會評估您的 7 天、30 天、60 天使用量，並建議適用於 RI 的最佳設定。 它會計算您在沒有 RI 的情況下要支付的費用，以及計算您利用 RI 節省成本所要支付的費用。

Azure Advisor 顯示的建議是以 30 天為一個期間。

為了簡單起見，以下範例顯示 7 天期間建議所發生的計算。 計算 30 天或 60 天建議時，亦是套用相同的方法。

## <a name="calculation-method-example"></a>計算方法範例

假設特定 SKU 和區域在這 7 天 (168小時) 中的每小時 Windows VM 使用量各不相同。 7 天中的最小使用量是 65 單位，最大使用量是 127 單位。 在此範例中，第 79 小時使用了80 個 VM，而您購買了 75 個 RI。

如果您購買 75 個 RI，則需為第 79 小時支付下列費用：

- 75 個保留執行個體。 當您購買 RI 時，需預先支付費用。
- 保留執行個體涵蓋執行 VM 的硬體費用，因此您將只需支付 75 小時的軟體價格。
- 第 79 小時的使用量為 80，因此您將支付 5 小時的 Windows，再加上硬體組合的計價。 組合價格是以您的 Enterprise 合約 (EA) 或隨用隨付費率為依據。

如果您購買 75 個 RI，可以加上前述的每小時費用來計算總費用。 您也可以使用您的費率來計算目前的費用。 這兩個金額之間的差異是您在此範例中的 7 天節省金額。

API 會為每個特定使用點進行計算。 然後傳回節省最多的建議數量。 下列範例的圖表顯示節省的尖峰為 68。 之後節省的費用會減少，因此 API 建議為 68。

![圖表顯示節省尖峰](./media/reserved-instance-purchase-recommendations/peak-savings.png)

## <a name="other-expected-api-behavior"></a>其他預期的 API 行為

- 當使用 Windows [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) 時，API 會顯示此權益的可能節省成本。 如未使用此權益，則 API 建議是以核心 Windows 成本為依據。 若您有 Azure Hybrid Benefit，請考慮使用來增加節省的成本。
- 如果您使用 7 天的回顧期間，當 VM 關閉超過一天時，您可能無法取得建議。

## <a name="next-steps"></a>後續步驟
- 了解 [Azure 保留折扣如何套用至虛擬機器](../manage/understand-vm-reservation-charges.md)。

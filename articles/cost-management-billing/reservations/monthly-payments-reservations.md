---
title: 購買預先付款或每月付款的 Azure 保留
description: 了解如何購買預先付款或每月付款的 Azure 保留。
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 2689bdeb003cec0050141374165142a62f9ea337
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200396"
---
# <a name="purchase-reservations-with-monthly-payments"></a>購買或每月付款的 Azure 保留

到目前為止，Azure 保留皆必須預先付款。 現在您可以透過每月付款的方式購買 Azure 保留。 不同於需支付全額的預先購買，每月付款選項會將保留的總費用平均分配至期限中的每一個月份。 預付和每月付款的保留總費用是相同的，當您選擇按月支付時，您不需要支付任何額外費用。

您的月付金額可能會依當地貨幣的當月市場匯率而異。

每月付款適用於：

- 虛擬機器
- Azure 儲存體
- SQL Database
- SQL 資料倉儲
- Cosmos DB
- App Service 戳記費用
- 受控磁碟
- Azure 資料總管
- 適用於 MariaDB、MySQL 與 PostgreSQL 的 Azure 資料庫
- 由 CloudSimple 提供的 Azure VMware 解決方案


在 [Azure 入口網站](https://portal.azure.com/?Microsoft_Azure_Reservations_EnableMultiCart=true&amp;paymentPlan=true#blade/Microsoft_Azure_Reservations/CreateBlade)中購買 Azure 保留。

![顯示保留購買的範例](./media/monthly-payments-reservations/purchase-reservation.png)

在購買保留時，您可以檢視付款排程。 按一下 [檢視完整付款排程]  。

![顯示保留付款排程的範例](./media/monthly-payments-reservations/prepurchase-schedule.png)

若要在購買後檢視付款排程，請選取保留，並按一下 [保留訂單識別碼]  ，然後按一下 [付款]  索引標籤。

## <a name="view-payments-made"></a>檢視已支付的款項

您可以使用 API、使用量資料和成本分析來檢視已支付的款項。 針對每月付款的保留，使用量資料和保留費用 API 中的頻率值會顯示為**定期**。 針對預先付款的保留，此值會顯示為**一次性**。

成本分析會在預設檢視中顯示每月的購買。 在 [費用類型]  上套用 [購買]  ，以及在 [頻率]  上套用 [定期]  來進行篩選，即可查看所有購買。 若只要檢視保留，請篩選 [保留]  。

![在成本分析中顯示保留購買費用的範例](./media/monthly-payments-reservations/cost-analysis.png)

## <a name="switch-to-monthly-payments-at-renewal"></a>在續約時切換到每月付款

當您更新保留時，您可以將計費頻率變更為每月。

## <a name="exchange-and-refunds"></a>交換和退款

就像其他保留一樣，您可以針對以每月計費購買的保留進行退款或交換。 目前，針對以每月計費購買的保留，您可以提交支援要求來開始進行交換或退款。

當您交換每月付款的保留時，新購買項目的總存留期費用應大於退回保留而取消的剩餘款項。 交換並不沒有其他限制或費用。 您可以交換預先付款的保留，改為購買每月計費的新保留。 不過，新保留的存留期值應該大於退回保留的比例分配值。

如果取消每月付款的保留，Microsoft 可能會對一併取消的未來承諾付款收取取消費用。 剩餘承諾付款會朝 $50,000 美元的退款限制累積。

如需有關交換和退款的詳細資訊，請參閱 [Azure 保留的自助式交換和退款](exchange-and-refund-azure-reservations.md)。

## <a name="next-steps"></a>後續步驟

- 若要深入了解保留，請參閱[什麼是 Azure 保留？](save-compute-costs-reservations.md)
- 若要了解購買保留之前應完成的工作，請參閱[購買之前，請先決定正確的 VM 大小](../../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy)

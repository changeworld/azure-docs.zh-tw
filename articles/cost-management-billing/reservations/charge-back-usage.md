---
title: 針對 Azure 保留成本進行退費
description: 了解如何針對退款檢視 Azure 保留成本。
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: aba6ea467788c51d179ef9377243efb6035b6f98
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148364"
---
# <a name="charge-back-azure-reservation-costs"></a>針對 Azure 保留成本進行退費

Enterprise 合約及 Microsoft 客戶合約帳單讀者可以檢視保留的分攤成本資料。 他們可以使用成本資料，將訂用帳戶、資源群組、資源或標籤的幣值退費給其合作夥伴。 在分攤的資料中，有效的價格是按比例計算的每小時保留成本。 成本是該日資源的保留使用量總成本。

具有個別訂用帳戶的使用者可以從其使用量檔案取得分攤的成本資料。 當資源取得保留折扣時，使用量檔案中的 AdditionalInfo 區段會包含保留詳細資料。 如需詳細資訊，請參閱[從 Azure 入口網站下載使用量](../understand/download-azure-daily-usage.md#download-usage-from-the-azure-portal-csv)。

## <a name="get-reservation-charge-back-data-for-chargeback"></a>取得保留退費資料以進行退款

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 瀏覽至 [成本管理 + 帳單]。
1. 在 [實際成本] 底下，選取 [分攤成本] 計量。
1. 若要查看保留所使用的資源，請套用 [保留] 的篩選，然後選取 [保留]。
1. 將 [細微性] 設定為 [每月] 或 [每日]。
1. 將圖表類型設定為 [資料表]。
1. 將 [群組依據] 選項設定為 [資源]。

[![顯示可用於退款的保留資源成本範例](./media/charge-back-usage/amortized-reservation-costs.png)](./media/charge-back-usage/amortized-reservation-costs.png#lightbox)

以下影片說明如何在 Azure 入口網站中檢視保留使用量成本。

 > [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4sQOw] 

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>後續步驟

- 若要了解如何管理保留項目，請參閱[管理 Azure 保留項目](manage-reserved-vm-instance.md)。
- 若要深入了解 Azure 保留項目，請參閱下列文章：
  - [什麼是 Azure 保留項目？](save-compute-costs-reservations.md)
  - [管理 Azure 中的保留](manage-reserved-vm-instance.md)
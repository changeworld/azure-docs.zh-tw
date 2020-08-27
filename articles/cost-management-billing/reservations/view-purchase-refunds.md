---
title: 檢視 Azure 保留購買和退款交易
description: 了解如何查看 Azure 保留購買和退款交易。
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: d9e5269468f7cd4571e7ae686af7f1ef159b4ef3
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88681697"
---
# <a name="view-reservation-purchase-and-refund-transactions"></a>檢視保留購買和退款交易

有幾種不同的方式可查看保留購買和退款交易。 您可以使用 Azure 入口網站、Power BI 和 REST API。

## <a name="view-reservation-transactions-in-the-azure-portal"></a>在 Azure 入口網站中檢視保留交易

Enterprise 註冊或 Microsoft 客戶合約計費管理員可以在成本管理和計費中檢視保留交易。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋 [成本管理 + 帳單]。
1. 選取 [保留交易]。
1. 若要篩選結果，請選取 [時間範圍]、[類型] 或 [描述]。
1. 選取 [套用]。

[![顯示 Azure 入口網站中保留交易的螢幕擷取畫面](./media/view-purchase-refunds/azure-portal-reservation-transactions.png)](./media/view-purchase-refunds/azure-portal-reservation-transactions.png#lightbox)

## <a name="view-reservation-transactions-in-power-bi"></a>檢視 Power BI 中的保留交易

Enterprise 註冊或 Microsoft 客戶合約計費管理員可以使用成本管理 Power BI 應用程式來檢視保留交易。

1. 取得[成本管理 Power BI 應用程式](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp)。
1. 導覽至 RI 採購報告。

[![顯示保留交易的範例](./media/view-purchase-refunds/power-bi-reservation-transactions.png)](./media/view-purchase-refunds/power-bi-reservation-transactions.png#lightbox)

若要深入了解，請參閱[適用於 Enterprise 合約的 Azure 成本管理 Power BI 應用程式](https://docs.microsoft.com/azure/cost-management-billing/costs/analyze-cost-data-azure-cost-management-power-bi-template-app)。

## <a name="use-apis-to-get-reservation-transactions"></a>使用 API 來取得保留交易

Enterprise 合約 (EA) 和 Microsoft 客戶合約使用者可以使用[保留交易 - 列出 API](https://docs.microsoft.com/rest/api/consumption/reservationtransactions/list) 來取得保留交易資料。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>後續步驟

- 若要了解如何管理保留項目，請參閱[管理 Azure 保留項目](manage-reserved-vm-instance.md)。
- 若要深入了解 Azure 保留項目，請參閱下列文章：
  - [什麼是 Azure 保留項目？](save-compute-costs-reservations.md)
  - [管理 Azure 中的保留](manage-reserved-vm-instance.md)

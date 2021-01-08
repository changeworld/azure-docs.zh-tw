---
title: 如何支付 Microsoft Azure 的帳單
description: 了解如何在 Azure 入口網站中支付帳單。 您必須是帳單設定檔擁有者、參與者或發票管理員，才能在入口網站中付款。
keywords: 帳單, 逾期, 餘額, 立即支付,
author: banders
ms.reviewer: judupont
tags: billing, past due, pay now, bill, invoice, pay
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 12/17/2020
ms.author: banders
ms.openlocfilehash: 2983f870d6ab20dac4da487ba03ebf84905f8dcc
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680321"
---
# <a name="how-to-pay-your-bill-for-microsoft-azure"></a>如何支付 Microsoft Azure 帳單

本文適用於具有 Microsoft 客戶合約 (MCA) 的客戶。

[檢查您對 Microsoft 客戶合約的存取權](#check-access-to-a-microsoft-customer-agreement)。

有兩種方式可以支付您的 Azure 帳單。 您可以使用帳單設定檔的預設付款方式來支付費用，也可以進行名為 **立即付費** 的一次性付款。

如果您有 Azure 點數，這些點數會在每個計費週期自動套用至您的發票。

## <a name="pay-by-default-payment-method"></a>依預設付款方式付費

帳單設定檔的預設付款方式可以是信用卡或轉帳卡，或是支票/電匯。

### <a name="credit-or-debit-card"></a>信用卡或轉帳卡

如果您帳單設定檔的預設付款方式是信用卡或轉帳卡，則會按每個計費週期自動收費。

如果您的自動信用卡或轉帳卡繳費因故遭到拒絕，您可以使用 **立即付費**，在 Azure 入口網站中使用信用卡或轉帳卡進行一次性付款。

如果您想要了解如何將預設付款方式變更為支票或電匯，請參閱[如何按發票付款](../manage/pay-by-invoice.md)。

### <a name="check-or-wire-transfer"></a>支票或電匯

如果您的帳單設定檔的預設付款方式是支票或電匯，請依照發票 PDF 檔案中顯示的付款指示操作。

或者，如果您的發票金額低於您貨幣的閾值金額，您可以使用 **立即付費**，在 Azure 入口網站中使用信用卡或轉帳卡進行一次性付款。 如果您的發票金額超過閾值，則無法使用信用卡或轉帳卡來支付發票費用。 選取 [立即付費] 後，您可在 Azure 入口網站中找到貨幣的閾值金額。

## <a name="pay-now-in-the-azure-portal"></a>立即在 Azure 入口網站中付款

若要在 Azure 入口網站中支付發票費用，您必須擁有 [MCA 權限](../manage/understand-mca-roles.md)，或是計費帳戶管理員。帳單帳戶管理員是一位原始註冊 MCA 帳戶的使用者。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 針對 [成本管理 + 帳單] 進行搜尋。
1. 在左側功能表中，選取 [計費]  之下的 [發票]  。
1. 如果您的任何發票已到期或逾期，您會看到該發票的 **立即付費** 連結 (以藍色顯示)。 請選取 [立即付費]。
1. 在 [立即付費] 視窗中，選取 [選取付款方式] 以選擇現有信用卡，或新增一個信用卡。
1. 選取付款方式之後，請選取 [立即付費]。

發票狀態會在 24 小時內顯示為「已付」。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>檢查 Microsoft 客戶合約的存取
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="next-steps"></a>下一步

- 若要獲得以支票/電匯來支付的資格，請參閱[如何依發票支付](../manage/pay-by-invoice.md)
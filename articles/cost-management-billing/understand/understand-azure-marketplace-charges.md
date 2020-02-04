---
title: 了解您的 Azure 外部服務費用 | Microsoft Docs
description: 了解 Azure 中外部服務 (先前稱為 Marketplace) 費用的計費方式。
author: jureid
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f94b79e03cf97275ec4dacf17e097e1678e81d10
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "75989419"
---
# <a name="understand-your-azure-external-services-charges"></a>了解您的 Azure 外部服務費用
外部服務是由 Azure Marketplace 中的第三方軟體廠商發佈。 比方說，SendGrid 是您可以在 Azure 中購買的外部服務，但不是由 Microsoft 所發佈。 有些 Microsoft 產品也會透過 Azure Marketplace 銷售。

## <a name="how-external-services-are-billed"></a>如何對外部服務計費

- 如果您有 Microsoft 客戶合約 (MCA) 或 Microsoft 合作夥伴合約，您的第三方服務就會與其餘的 Azure 服務一起計費。 [檢查您的計費帳戶類型 ](#check-billing-account-type)，以查看您是否有 MCA 或 MPA 的存取權。
- 如果您沒有 MCA 或 MPA，則外部服務會與 Azure 服務分開計費。
- 每項外部服務都有各自的計費模式。 有些服務採用隨用隨付的方式計費，有些則有每月固定費用。
- 外部服務不適用於每個月的免費信用額度。 如果您使用含有[免費額度](https://azure.microsoft.com/pricing/spending-limits/)的 Azure 訂用帳戶，這些額度無法套用至外部服務。 當您佈建新的外部服務或資源時，系統會顯示警告︰

    ![Marketplace 購買警告](./media/understand-azure-marketplace-charges/credit-warning.png)

<!-- ## View external service spending and history in the Azure portal
You can view a list of the external services that are on each subscription within the [Azure portal](https://portal.azure.com/):

1. Sign in to the [Azure portal](https://portal.azure.com/) as the account administrator.
2. In the Hub menu, select **Subscriptions**.

    ![Select Subscriptions in the Hub menu](./media/understand-azure-marketplace-charges/sub-button.png)
3. In the **Subscriptions** blade, select the subscription that you want to view, and then select **External services**.

    ![Select a subscription in the billing blade](./media/understand-azure-marketplace-charges/select-sub-external-services.png)
4. You should see each of your external service orders, the publisher name, service tier you bought, name you gave the resource, and the current order status. To see past bills, select an external service.

    ![Select an external service](./media/understand-azure-marketplace-charges/external-service-blade2.png)
5. From here, you can view past bill amounts including the tax breakdown.

    ![View external services billing history](./media/understand-azure-marketplace-charges/billing-overview-blade.png) -->

## <a name="view-and-download-invoices-for-external-services"></a>檢視及下載外部服務的發票

如果您有 Microsoft 客戶合約 (MCA) 或 Microsoft 合作夥伴合約，您的第三方服務就會與其餘的 Azure 服務一起計費。 [檢查您的計費帳戶類型 ](#check-billing-account-type)，以查看您是否有 MCA 或 MPA 的存取權。 如果您有存取權，請參閱[在 Azure 入口網站中檢視及下載發票](download-azure-invoice.md)，以查看第三方費用。

如果您沒有 MCA 或 MPA，則第三方費用會有不同的發票。 您可以遵循下列步驟，從 Azure 入口網站檢視及下載您的 Azure Marketplace 發票：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋 [成本管理 + 帳單]  。
1. 在左側功能表中，選取 [發票]  。
1. 按一下 [Azure Marketplace 與保留]  索引標籤。![Azure Marketplace 與保留索引標籤的圖片](./media/understand-azure-marketplace-charges/invoice-tabs.png)
1. 在訂用帳戶下拉式清單中，選取包含您要查看發票之外部服務的訂用帳戶。

## <a name="external-spending-for-ea-customers"></a>EA 客戶的外部費用

EA 客戶可以在 EA 入口網站看到外部服務消費並下載報告。 請參閱 [EA 客戶的 Azure Marketplace](https://ea.azure.com/helpdocs/azureMarketplace) 以便開始使用。

## <a name="manage-payment-for-external-services"></a>管理外部服務的付款

購買外部服務時，您可以選擇資源的 Azure 訂用帳戶。 所選 Azure 訂用帳戶的付款方式會成為外部服務的付款方式。 若要變更外部服務的付款方式，您必須[變更外部服務所繫結 Azure 訂用帳戶的付款方式](../manage/change-credit-card.md)。 您可以遵循下列步驟，找出外部服務訂單所繫結的訂用帳戶：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 按一下左側導覽功能表中的 [所有資源]  。
     ![所有資源功能表項目的螢幕擷取畫面](./media/understand-azure-marketplace-charges/all-resources.png)
1. 搜尋您的外部服務。
1. 在 [訂用帳戶]  資料行中尋找訂用帳戶的名稱。
    ![資源訂用帳戶名稱的螢幕擷取畫面](./media/understand-azure-marketplace-charges/sub-selected.png)
1. 按一下訂用帳戶名稱並[更新有效付款方式](../manage/change-credit-card.md)。

<!-- Update your payment methods for external service orders from the [Account Center](https://account.windowsazure.com/).

> [!NOTE]
> If you purchased your subscription with a Work or School account, [contact support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) to make changes to your payment method.

1. Sign in to the [Account Center](https://account.windowsazure.com/) and [navigate to the **marketplace** tab](https://account.windowsazure.com/Store)

    ![Select marketplace in the account center](./media/understand-azure-marketplace-charges/select-marketplace.png)
2. Select the external service you want to manage

    ![Select the external service you want to manage](./media/understand-azure-marketplace-charges/select-ext-service.png)
3. Click **Change payment method** on the right side of the page. This link brings you to a different portal to manage your payment method.

    ![Order summary](./media/understand-azure-marketplace-charges/change-payment.PNG)
4. Click **Edit info** and follow instructions to update your payment information.

    ![Select edit info](./media/understand-azure-marketplace-charges/edit-info.png) -->

## <a name="cancel-an-external-service-order"></a>取消外部服務訂單
如果想要取消外部服務訂單，請在 [Azure 入口網站](https://portal.azure.com)中刪除該資源。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 按一下左側導覽功能表中的 [所有資源]  。
    ![所有資源功能表項目的螢幕擷取畫面](./media/understand-azure-marketplace-charges/all-resources.png)
1. 搜尋您的外部服務。
1. 勾選要刪除的資源旁邊的方塊。
1. 在命令列中選取 [刪除]  。
    ![刪除按鈕的螢幕擷取畫面](./media/understand-azure-marketplace-charges/delete-button.png)
1. 在確認刀鋒視窗中輸入 [Yes]  。
    ![刪除資源](./media/understand-azure-marketplace-charges/delete-resource.PNG)
1. 按一下 **[刪除]** 。

## <a name="check-billing-account-type"></a>檢查計費帳戶類型
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟
- [開始分析成本](../costs/quick-acm-cost-analysis.md)

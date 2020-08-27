---
title: 來自 Azure 的逾期未付帳款電子郵件
description: 描述如果 Azure 訂用帳戶有逾期未付帳款要如何付款
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 8c792ae1b6ac43ead15fd4f27d2468c517153354
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684808"
---
# <a name="resolve-past-due-balance-for-your-azure-subscription"></a>解決 Azure 訂用帳戶的逾期未付帳款

本文適用於使用信用卡註冊線上 Azure 並擁有 Microsoft Online Services 方案計費帳戶的客戶。 了解如何[查看您的計費帳戶類型](#check-the-type-of-your-account)。 如果您有 Microsoft 客戶合約計費帳戶，請改為參閱[支付 Microsoft Azure 的帳單](../understand/pay-bill.md)。

如果未收到您的付款或無法處理您的款項，您可能會收到電子郵件，並在 Azure 入口網站中看到警示，指出您的訂用帳戶有逾期未付帳款。 如果您的預設付款方式是信用卡，則[帳戶管理員](billing-subscription-transfer.md#whoisaa)可以在 Azure 入口網站中結算未付的費用。 如果您是按照發票付款 (支票/電匯)，請將您的款項匯至發票底部所列的位置。

> [!IMPORTANT]
> * 如果您有多個訂用帳戶使用同張信用卡，而這些訂用帳戶全都逾期，您就必須一次支付整個未付餘額。
> * 對於所有曾使用失敗付款方式的訂用帳戶，您用來結算未付費用的信用卡，將成為其新的預設付款方式。

## <a name="resolve-past-due-balance-in-the-azure-portal"></a>在 Azure 入口網站中解決逾期餘額

1. 以帳戶管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋 [成本管理 + 帳單]  。
1. 從 [概觀]  頁面中選取逾期的訂用帳戶。
1. 在 [訂用帳戶概觀]  頁面中，按一下紅色的逾期橫幅以結算未付帳款。
    > [!NOTE]
    > 如果您不是帳戶管理員，您將無法結算未付帳款。
1. 在 [結算帳款]  頁面上，按一下 [選取付款方法]  。
    ![顯示 [選取付款方式] 連結的螢幕擷取畫面](./media/resolve-past-due-balance/settle-balance-screen.png)

1. 在右側的新分頁中，從下拉式選單選取信用卡，或按一下藍色的 [新增付款方式]  連結來新增信用卡。 針對目前使用失敗付款方式的所有訂用帳戶，此信用卡會成為其有效付款方式。
     > [!NOTE]
     > * 未付總餘額會使用失敗的付款方式反映所有 Microsoft 服務的未付費用。
     > * 如果選取的付款方式也有 Microsoft 服務的未付費用，這會反映於未付的總餘額。 您也必須支付那些未付費用。
1. 按一下 [付款]  。

## <a name="troubleshoot-declined-credit-card"></a>針對遭拒的信用卡進行疑難排解

如果信用卡遭到金融機構拒絕，請連絡您的金融機構來解決此問題。 請洽詢您的銀行以確定：
- 卡片已啟用國際交易。
- 卡片具有足夠的信用額度或資金來結清餘額。
- 卡片已啟用定期付款。

## <a name="not-getting-billing-email-notifications"></a>未收到帳單電子郵件通知嗎？

如果您是帳戶管理員，請[確認用於接收通知的電子郵件地址](change-azure-account-profile.md)。 建議您使用您會定期收信的電子郵件地址。 如果電子郵件正確，請查看垃圾郵件資料夾。

## <a name="if-i-forget-to-pay-what-happens"></a>如果忘記付款會如何？

服務遭到取消，您的資源便無法使用。 服務終止 90 天後，就會刪除您的 Azure 資料。 若要深入了解，請參閱 [Microsoft 信任中心 - 我們如何管理您的資料](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409) \(英文\)。

如果您已處理款項，但您的訂用帳戶狀態仍為停用，請連絡 [Azure 支援服務](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

## <a name="check-the-type-of-your-account"></a>檢查帳戶的類型
[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

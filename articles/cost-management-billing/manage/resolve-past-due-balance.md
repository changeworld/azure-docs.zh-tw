---
title: 來自 Azure 的逾期未付帳款電子郵件
description: 描述如果 Azure 訂用帳戶有逾期未付帳款要如何付款
author: genlin
manager: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: banders
ms.openlocfilehash: 7216af00413b1f8022957ac134f67a5c27b6cc78
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "75987898"
---
# <a name="resolve-past-due-balance-for-your-azure-subscription"></a>解決 Azure 訂用帳戶的逾期未付帳款

本文適用於具有 Microsoft Online Services 方案帳戶的客戶。

如果未收到您的付款或無法處理您的款項，您可能會收到電子郵件，或是在 Azure 入口網站或帳戶中心看到警示。
如果您是[帳戶管理員](billing-subscription-transfer.md#whoisaa)，您可以在 [Azure 入口網站](https://portal.azure.com)中結算未付的費用。 如果您使用發票付款模式，將您的付款寄至您的發票底部所列的地址。

> [!IMPORTANT]
> * 如果您有多個訂用帳戶使用同張信用卡，而這些訂用帳戶全都逾期，您就必須一次支付整個未付餘額。
> * 對於所有曾使用失敗付款方式的訂用帳戶，您用來結算未付費用的付款方式，將成為其新的有效付款方式。

## <a name="resolve-past-due-balance-in-the-azure-portal"></a>在 Azure 入口網站中解決逾期餘額

1. 以帳戶管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋 [成本管理 + 帳單]  。
1. 在 [概觀] 頁面中，您會看到訂用帳戶清單。 如果您的訂用帳戶狀態為逾期，請按一下 [結算餘額]  連結。
    ![顯示結算餘額連結的螢幕擷取畫面](./media/resolve-past-due-balance/settle-balance-entry-point.png)
1. 未付總餘額會使用失敗的付款方式反映所有 Microsoft 服務的未付費用。
1. 選取付款方式來支付餘額。 針對目前使用失敗付款方式的所有訂用帳戶，此付款方式會成為其有效付款方式。
    ![顯示 [選取付款方式] 連結的螢幕擷取畫面](./media/resolve-past-due-balance/settle-balance-screen.png)
1. 如果選取的付款方式也有 Microsoft 服務的未付費用，這會反映於未付的總餘額。 您也必須支付那些未付費用。
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


## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

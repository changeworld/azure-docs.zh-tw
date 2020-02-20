---
title: 針對 Azure 訂用帳戶登入問題進行疑難排解
description: 協助解決無法登入 Azure 入口網站或 Azure 帳戶中心的問題。
author: v-miegge
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: v-miegge
ms.openlocfilehash: 40a645fe4b74043d84dc770f15d5e3c3ec907f02
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199478"
---
# <a name="troubleshoot-azure-subscription-sign-in-issues"></a>針對 Azure 訂用帳戶登入問題進行疑難排解

本指南可協助解決無法登入 Azure 入口網站或 Azure 帳戶中心的問題。

## <a name="issues"></a>問題

### <a name="page-hangs-in-the-loading-status"></a>頁面在載入狀態停止回應

如果您的網際網路瀏覽器頁面停滯，請嘗試下列每個步驟，直到您可以進入 Azure 入口網站為止。

- 重新整理頁面。
- 使用另一個網際網路瀏覽器。
- 使用您瀏覽器的私密瀏覽模式。 若為 Internet Explorer：按一下 [工具]   > [安全性]   > [InPrivate 瀏覽]  ，然後瀏覽並登入 [Azure 入口網站](https://portal.azure.com/)或 [Azure 帳戶中心](https://account.azure.com/Subscriptions)。

### <a name="you-are-automatically-signed-in-as-a-different-user"></a>自動將您登入為不同的使用者

如果您在網際網路瀏覽器中使用多個使用者帳戶，就可能會發生此問題。

若要解決此問題，請嘗試下列其中一個方法：

- 清除快取並刪除網際網路 Cookie。 在 Internet Explorer 中，按一下 [工具]   > [網際網路選項]   > [刪除]  。 確定已選取暫存檔、Cookie、密碼及瀏覽歷程記錄的核取方塊，然後按一下 [刪除]。
- 重設 Internet Explorer 設定來還原您所做的任何個人設定。 按一下 [工具]   > [網際網路選項]   > [進階]  > 選取 [刪除個人設定]  方塊 > [重設]  。
- 使用您瀏覽器的私密瀏覽模式。 若為 Internet Explorer：按一下 [工具]   > [安全性]   > [InPrivate 瀏覽]  ，然後瀏覽並登入 [Azure 入口網站](https://portal.azure.com/)或 [Azure 帳戶中心](https://account.azure.com/Subscriptions)。

### <a name="i-can-sign-in-but-i-see-no-subscriptions-found"></a>我可以登入但收到「找不到訂用帳戶」  錯誤訊息

如果您選取錯誤的目錄或帳戶沒有足夠的權限，就會發生此問題。

**案例 1：** 在 [Azure 入口網站](https://portal.azure.com/)中收到錯誤訊息

若要修正此問題：

- 確定您已選取正確的 Azure 目錄，方法是按一下右上角的帳戶。
- 若已選取正確的 Azure 目錄，但您仍收到錯誤訊息，請[將您的帳戶新增為擁有者](add-change-subscription-administrator.md)。

**案例 2：** 在 [Azure 帳戶中心](https://account.windowsazure.com/Subscriptions)收到錯誤訊息

請檢查您所使用的帳戶是否為帳戶管理員。 若要確認誰是帳戶管理員，請依照下列步驟操作︰

1. 登入 [Azure 入口網站中的訂用帳戶檢視](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。

2. 選取您想要檢查的訂用帳戶，然後查看 [設定]  。

3. 選取 [屬性]  。 該訂用帳戶的帳戶管理員會顯示在 [帳戶管理員]  方塊中。

## <a name="additional-help-resources"></a>其他說明資源

Azure 計費和訂用帳戶的其他疑難排解文章

- [卡片遭拒](troubleshoot-declined-card.md)
- [訂用帳戶註冊問題](troubleshoot-azure-sign-up.md)
- [找不到訂用帳戶](no-subscriptions-found.md)
- [已停用企業成本檢視](enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>如需協助，請與我們連絡

如果您有問題或需要協助，請[建立支援要求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>後續步驟

- [Azure 計費文件](../../billing/index.md)

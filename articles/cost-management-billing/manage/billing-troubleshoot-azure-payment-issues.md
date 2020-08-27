---
title: 針對 Azure 付款問題進行疑難排解
description: 解決在 Microsoft Azure 入口網站或帳戶中心更新付款資訊時的問題。
author: v-miegge
ms.reviewerr: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 08/20/2020
ms.author: jaserano
ms.openlocfilehash: adc3874ba211de270589d8ea36a04b9431e9e12a
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684910"
---
# <a name="troubleshoot-azure-payment-issues"></a>針對 Azure 付款問題進行疑難排解

您嘗試在 Microsoft Azure 入口網站或 Azure 帳戶中心更新付款資訊帳戶時，可能會遇到某些問題或錯誤。

若要解決您的問題，請選取下列與您的錯誤最相似的主題。

## <a name="my-credit-card-was-declined-when-i-tried-to-sign-up-for-azure"></a>嘗試註冊 Azure 時，信用卡遭到拒

若要針對遭拒卡片的相關問題進行疑難排解，請參閱[針對在 Azure 註冊時遭拒的卡片進行疑難排解](troubleshoot-declined-card.md)。

## <a name="unable-to-see-subscriptions-under-my-account-to-update-the-payment-method"></a>無法查看我帳號底下的訂用帳戶來更新付款方式

您使用的電子郵件識別碼可能與訂用帳戶所使用的不同。

若要針對此問題進行疑難排解，請參閱 [Azure 入口網站或 Azure 帳戶中心的「找不到訂用帳戶」登入錯誤](no-subscriptions-found.md)。

## <a name="unable-to-use-a-virtual-or-prepaid-credit-or-debit-card-as-a-payment-method"></a>無法使用虛擬或預付信用卡或轉帳卡作為付款方式。

*   虛擬卡或預付信用卡無法作為 Azure 訂用帳戶的付款方式。
*   轉帳卡無法作為 Azure 訂用帳戶的付款方式。

如需詳細資訊，請參閱[針對在 Azure 註冊時遭拒的卡片進行疑難排解](troubleshoot-declined-card.md)。

## <a name="unable-to-remove-a-credit-card-from-a-saved-billing-payment-method"></a>無法從已儲存的帳單付款方式中移除信用卡

根據設計，您無法從作用中的訂用帳戶移除信用卡。

如果要刪除現有的卡片，必須將新的卡片新增至訂用帳戶，才能成功刪除舊的付款方式，或者您必須取消訂用帳戶。 這會永久刪除訂用帳戶並移除卡片。

## <a name="unable-to-delete-an-old-payment-method-after-adding-a-new-payment-method"></a>加入新的付款方式之後，無法刪除舊的付款方式

新的付款方式可能無法與訂用帳戶建立關聯。 如需將付款方式與訂用帳戶建立關聯的協助，請參閱[新增、更新或移除用於 Azure 的信用卡](change-credit-card.md)。

## <a name="unable-to-delete-a-payment-method-because-of-cannot-delete-payment-method-error"></a>無法刪除付款方式，因為出現「無法刪除付款方式」  錯誤

這是因為未付餘額所造成。 請先清除任何未付餘額，再刪除付款方式。

## <a name="unable-to-make-payment-for-a-subscription"></a>無法對訂用帳戶進行付款

如果您收到錯誤訊息：*付款已逾期。您的付款方式有問題*或*我們很抱歉，資訊無法儲存。請關閉瀏覽器，然後再試一次。* 這可能是因為卡片上有待付款項，因為卡片遭到您金融機構的拒絕。

請確認信用卡有足夠的餘額可付款。 如果沒有，請使用另一張卡片來進行付款，或與您的金融機構聯繫以解決問題。

請洽詢您的銀行以釐清下列問題：

- 未啟用國際交易。
- 卡片具有信用額度限制，而且必須付清餘額。
- 卡片上已啟用定期付款。

## <a name="unable-to-change-payment-method-because-of-browser-issues-browser-does-not-respond-does-not-load-and-so-on"></a>因為瀏覽器問題而無法變更付款方式 (瀏覽器沒有回應、未載入等等)

登出所有作用中的 Azure 工作階段，然後依照[在 Microsoft Edge 中進行 InPrivate 瀏覽](https://support.microsoft.com/help/4026200/microsoft-edge-browse-inprivate)一文中的步驟，在 Microsoft Edge 或 Internet Explorer 中啟動 InPrivate 工作階段。

在私密工作階段中，依照[如何變更信用卡](change-credit-card.md)中的步驟，更新或變更信用卡資訊。

您也可以嘗試執行下列作業︰

- 重新整理您的瀏覽器
- 使用另一個瀏覽器
- 刪除快取的 Cookie

## <a name="my-subscription-is-still-disabled-after-updating-the-payment-method"></a>更新付款方式後，我的訂用帳戶仍無法使用。

這是未付餘額所造成的問題。 請先清除任何未付餘額，再刪除付款方式。

## <a name="unable-to-change-payment-method-because-of-an-xml-error-response-page"></a>無法變更付款方式，因為出現 XML 錯誤回應頁面

如果您使用 [Azure 入口網站](https://portal.azure.com/)新增信用卡，就會收到此訊息。

若要新增信用卡詳細資料，請使用帳戶管理員的電子郵件地址登入 Azure 帳戶入口網站。

## <a name="additional-help-resources"></a>其他說明資源

Azure 計費和訂用帳戶的其他疑難排解文章

- [卡片遭拒](troubleshoot-declined-card.md)
- [訂用帳戶登入問題](troubleshoot-sign-in-issue.md)
- [找不到訂用帳戶](no-subscriptions-found.md)
- [已停用企業成本檢視](enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>如需協助，請與我們連絡

如果您有問題或需要協助，請[建立支援要求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>後續步驟

- [Azure 計費文件](../../billing/index.md)

---
title: 針對在 Azure 入口網站或 Azure 帳戶中心註冊新帳戶時所發生的問題進行疑難排解
description: 解決在 Microsoft Azure 入口網站的帳戶中心註冊新帳戶時，可能會遇到某些問題。
services: cost-management-billing
author: v-miegge
manager: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: v-miegge
ms.openlocfilehash: 7bcf3197ae218c469983200814cecf551dc42a6b
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133174"
---
# <a name="troubleshoot-issues-when-you-sign-up-for-a-new-account-in-azure-portal-or-azure-account-center"></a>針對在 Azure 入口網站或 Azure 帳戶中心註冊新帳戶時所發生的問題進行疑難排解

您嘗試在 Microsoft Azure 入口網站或 Azure 帳戶中心註冊新帳戶時，可能會遇到某些問題。 在進行問題的疑難排解之前，請先確認下列事項：

- 您為 Azure 帳戶設定檔 (包括連絡人電子郵件、地址和電話號碼) 提供的資訊正確無誤。
- 信用卡資訊正確。
- 您沒有具有相同資訊的 Microsoft 帳戶。

> [!NOTE]
> 如果您已經有現有的帳戶，而且正在尋找針對登入問題進行疑難排解的指引，請參閱[針對 Azure 訂用帳戶登入問題進行疑難排解](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-sign-in-issue)。

## <a name="resolutions"></a>解決方式

若要解決任何錯誤，請選取您在嘗試註冊 Azure 時所遇到的問題。

### <a name="error-we-cannot-proceed-with-sign-up-due-to-an-issue-with-your-account-please-contact-billing-support"></a>錯誤：*您的帳戶出現問題，導致註冊無法繼續。請連絡帳務支援。*

若要解決此問題，請依照下列步驟執行︰

1. 使用帳戶管理員認證登入 [Azure 帳戶中心](https://account.azure.com/Profile)。

2. 選取 [編輯詳細資料]  。

3. 確定所有地址欄位皆已填寫完畢且正確無誤。

4. 註冊 Azure 訂用帳戶時，確認信用卡註冊的帳單地址和您銀行記錄的地址相同。

如果仍收到錯誤訊息，請嘗試用其他瀏覽器進行註冊。

### <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>[依據卡片進行身分識別驗證]  區段中的進度列停止回應。

若要使用卡片完成身分識別驗證，您必須允許瀏覽器接受第三方 Cookie。

![依據卡片進行身分識別驗證](./media/troubleshoot-azure-sign-up/identify-verification-by-card.png)

使用下列步驟來更新瀏覽器的 Cookie 設定。

1. 如果您是使用 Chrome，請選取 [設定]   > [顯示進階設定]   > [隱私權]   > [內容設定]  。 清除 [封鎖第三方 Cookie 和網站資料]  。

2. 如果您使用 Microsoft Edge，請選取 [設定]   > [檢視進階設定]   > [Cookie]   > [不要封鎖 Cookie]  。

3. 重新整理 Azure 登入頁面，然後檢查問題是否已獲得解決。

4. 如果重新整理並未解決問題，請結束瀏覽器並重新啟動，然後再試一次。

### <a name="credit-card-form-doesnt-support-my-billing-address"></a>信用卡表單不支援我的帳單地址

您的帳單地址必須位於您在 [關於您]  一節所選的國家/地區。 請確定您選取正確的國家/地區。

### <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>註冊帳戶驗證期間沒有文字訊息或呼叫

雖然此程序通常很快速，但傳遞驗證碼可能需要最多四分鐘的時間。 您針對驗證所輸入的電話號碼，並不會儲存為帳戶的連絡電話。

以下是一些其他的祕訣：

- Voice-over-IP (VoiP) 電話號碼無法用於電話驗證程序。
- 再次確認您輸入的電話號碼，包括您在下拉式功能表中選取的國碼 (地區碼)。
- 如果您的手機沒有收到簡訊 (SMS)，請嘗試 [撥號給我]  選項。
- 請確定您的電話可以接收來自美國電話號碼的來電或簡訊。

當您收到簡訊或來電時，請在文字方塊中輸入您收到的代碼。

### <a name="credit-card-declined-or-not-accepted"></a>信用卡被拒絕或不被接受

虛擬卡、預付信用卡或金融卡不被視為 Azure 訂用帳戶的付款方式。 若要查看其他造成卡片被拒絕的可能原因，請參閱[在 Azure 註冊時信用卡或金融卡遭到拒絕](https://support.microsoft.com/help/4042960)。

### <a name="free-trial-is-not-available"></a>無法使用免費試用版

過去曾使用 Azure 訂用帳戶嗎？ Azure 使用規定協議限制免費試用啟動僅適用 Azure 的新使用者。 如果您有任何其他類型的 Azure 訂用帳戶，會無法啟用免費試用。 請考慮註冊[隨用隨付訂用帳戶](https://azure.microsoft.com/offers/ms-azr-0003p/)。

### <a name="i-saw-a-charge-on-my-free-trial-account"></a>我的免費試用帳戶收到一筆費用

您在註冊後可能會在信用卡帳戶上看到一筆小額度的驗證保留費用。 這會在三到五天內移除。 如果您擔心管理成本，請深入了解[如何避免非預期的成本](getting-started.md)。

### <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>無法啟用 MSDN、BizSpark、BizSparkPlus 或 MPN 之類的 Azure 權益方案

請確定您是使用正確的登入認證。 然後檢查權益方案，以確認您符合資格。

- MSDN
  - 請在您的 [MSDN 帳戶頁面](https://msdn.microsoft.com/subscriptions/manage/default.aspx)驗證自己的資格狀態。
  - 如果您無法驗證自己的狀態，請連絡 [MSDN 訂閱客服中心](https://msdn.microsoft.com/library/aa493452.aspx)。
- Microsoft for Startups
  - 登入 [Microsoft for Startups 入口網站](https://startups.microsoft.com/#start-two)，以驗證您的資格狀態是否適用於 Microsoft for Startups。
  - 如果您無法驗證自己的狀態，您可以在 [Microsoft for Startups 論壇](https://www.microsoftpartnercommunity.com/t5/Microsoft-for-Startups/ct-p/Microsoft_Startups)上取得協助。
- MPN
  - 登入 [MPN 入口網站](https://mspartner.microsoft.com/Pages/Locale.aspx)，並驗證您的資格狀態。 如有適當的[雲端平台能力](https://mspartner.microsoft.com/pages/membership/cloud-platform-competency.aspx)，您或許能夠得到額外的好處。
  - 如果您無法驗證自己的狀態，請連絡 [MPN 支援](https://mspartner.microsoft.com/Pages/Support/Premium/contact-support.aspx)。

### <a name="cant-activate-new-azure-in-open-subscription"></a>無法啟用新的 Azure in Open 訂用帳戶

若要建立 Azure in Open 訂用帳戶，您必須具有有效的線上服務啟用 (OSA) 金鑰與至少一個相關聯的 Azure in Open 權杖。 如果您沒有 OSA 金鑰，請連絡 [Microsoft Pinpoint](https://pinpoint.microsoft.com/) 中列出的其中一個 Microsoft 合作夥伴。

### <a name="error-you-are-not-eligible-for-an-azure-subscription"></a>錯誤：您不符合 Azure 訂用帳戶的資格 

若要解決此問題，請再次檢查下列項目是否成立：

- 您為 Azure 帳戶設定檔 (包括連絡人電子郵件、地址和電話號碼) 提供的資訊正確無誤。
- 信用卡資訊正確。
- 您沒有使用相同資訊的 Microsoft 帳戶。

### <a name="error-your-current-account-type-is-not-supported"></a>錯誤：不支援您目前的帳戶類型 

如果帳戶是在[非受控 Azure AD 目錄](../../active-directory/users-groups-roles/directory-self-service-signup.md)中註冊，而且不在您組織的 Azure AD 目錄中註冊，就會發生此問題。

若要解決此問題，請使用另一個帳戶來註冊 Azure 帳戶，或接管非受控 AD 目錄。 如需詳細資訊，請參閱[如何以系統管理員身分接管 Azure Active Directory 中非受控目錄](../../active-directory/users-groups-roles/domains-admin-takeover.md)。

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

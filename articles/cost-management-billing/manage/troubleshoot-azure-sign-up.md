---
title: 針對在 Azure 入口網站或 Azure 帳戶中心註冊新帳戶時所發生的問題進行疑難排解
description: 解決在 Microsoft Azure 入口網站的帳戶中心註冊新帳戶時，可能會遇到某些問題。
services: cost-management-billing
author: v-miegge
manager: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 08/20/2020
ms.author: v-miegge
ms.openlocfilehash: 86f9f92810cff241f816dadfc6e1e8ca120f1f4a
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686644"
---
# <a name="troubleshoot-issues-when-you-sign-up-for-a-new-account-in-azure-portal-or-azure-account-center"></a>針對在 Azure 入口網站或 Azure 帳戶中心註冊新帳戶時所發生的問題進行疑難排解

您嘗試在 Microsoft Azure 入口網站或 Azure 帳戶中心註冊新帳戶時，可能會遇到某些問題。 這份簡短的指南將逐步引導您完成註冊程序，並討論每個步驟的一些常見問題。

> [!NOTE]
> 如果您已經有現有的帳戶，而且正在尋找針對登入問題進行疑難排解的指引，請參閱[針對 Azure 訂用帳戶登入問題進行疑難排解](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-sign-in-issue)。

## <a name="before-you-begin"></a>開始之前

開始註冊之前，請先確認下列事項：

- 您 Azure 帳戶設定檔 (包括連絡人電子郵件地址、街道地址與電話號碼) 的資訊正確無誤。
- 您的信用卡資訊正確無誤。
- 您沒有具有相同資訊的 Microsoft 帳戶。

## <a name="guided-walkthrough-of-azure-sign-up"></a>Azure 註冊的引導式逐步解說

Azure 註冊體驗包含四個區段：

- 關於您
- 依據手機進行身分識別驗證
- 依據卡片進行身分識別驗證
- 合約

此逐步解說提供註冊 Azure 帳戶的正確資訊範例。 每個區段也包含一些常見問題與解決方式。

## <a name="about-you"></a>關於您

![關於您](./media/troubleshoot-azure-sign-up/1.png)
 
### <a name="common-issues-and-solutions"></a>常見問題與解決方案

#### <a name="you-see-the-message-we-cannot-proceed-with-sign-up-due-to-an-issue-with-your-account-please-contact-billing-support"></a>您看到「您的帳戶出現問題，導致註冊無法繼續。 請連絡帳務支援」訊息。

若要解決此錯誤，請依照下列步驟執行︰

1.  使用帳戶管理員認證登入 [Azure 帳戶中心](https://account.azure.com/Profile)。
1.  選取 [編輯詳細資料]。
1.  確定所有地址欄位皆已填寫完畢且真實有效。
1.  註冊 Azure 訂用帳戶時，請確認信用卡註冊的帳單地址與您銀行記錄的地址相同。

如果您持續收到訊息，請嘗試使用其他瀏覽器進行註冊。

是否能使用 InPrivate 瀏覽？

#### <a name="free-trial-is-not-available"></a>不提供免費試用版

過去曾使用 Azure 訂用帳戶嗎？ Azure 使用規定協議限制免費試用啟動僅適用 Azure 的新使用者。 如果您有任何其他類型的 Azure 訂用帳戶，會無法啟用免費試用。 請考慮註冊[隨用隨付訂用帳戶](https://azure.microsoft.com/offers/ms-azr-0003p/)。

#### <a name="you-see-the-message-you-are-not-eligible-for-an-azure-subscription"></a>您看到「您不具 Azure 訂用帳戶的資格」訊息

若要解決此問題，請再次檢查下列項目是否成立：

- 您為 Azure 帳戶設定檔 (包括連絡人電子郵件、地址和電話號碼) 提供的資訊正確無誤。
- 信用卡資訊正確。
- 您沒有使用相同資訊的 Microsoft 帳戶。

#### <a name="you-see-the-message-your-current-account-type-is-not-supported"></a>您看到「不支援您目前的帳戶類型」訊息

如果帳戶是在[非受控 Azure AD 目錄](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup)中註冊，而且不在您組織的 Azure AD 目錄中註冊，就會發生此問題。
若要解決此問題，請使用另一個帳戶來註冊 Azure 帳戶，或接管非受控 AD 目錄。 如需詳細資訊，請參閱[如何以系統管理員身分接管 Azure Active Directory 中非受控目錄](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover)。

## <a name="identity-verification-by-phone"></a>依據手機進行身分識別驗證

![依據手機進行身分識別驗證](./media/troubleshoot-azure-sign-up/2.png)
 
當您收到簡訊或來電時，請在文字方塊中輸入您收到的代碼。

### <a name="common-issues-and-solutions"></a>常見問題與解決方案

#### <a name="no-verification-text-message-or-phone-call"></a>沒有驗證簡訊或來電

雖然註冊驗證程序通常很快速，但傳遞驗證碼最多可能需要四分鐘的時間。

以下是一些其他的祕訣：

- 只要符合需求，您就可以使用任何電話號碼進行驗證。 您針對驗證所輸入的電話號碼，並不會儲存為帳戶的連絡電話。
  - Voice-over-IP (VoiP) 電話號碼無法用於電話驗證程序。
  - 檢查您的電話是否可以接收來自美國電話號碼的來電或簡訊。
- 再次確認您輸入的電話號碼，包括您在下拉式功能表中選取的國碼 (地區碼)。
- 如果您的手機沒有收到簡訊 (SMS)，請嘗試 [撥號給我] 選項。

## <a name="identity-verification-by-card"></a>依據卡片進行身分識別驗證

![依據卡片進行身分識別驗證](./media/troubleshoot-azure-sign-up/3.png)
 
### <a name="common-issues-and-solutions"></a>常見問題與解決方案

#### <a name="credit-card-declined-or-not-accepted"></a>信用卡被拒絕或不被接受

虛擬卡、預付信用卡或金融卡不被視為 Azure 訂用帳戶的付款方式。 若要查看其他可能造成您的卡片遭到拒絕的原因，請參閱[針對 Azure 註冊上遭拒的卡片進行疑難排解](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-declined-card)。

#### <a name="credit-card-form-doesnt-support-my-billing-address"></a>信用卡表單不支援我的帳單地址

您的帳單地址必須位於您在 [關於您] 區段中所選的國家/地區。 確認您已經選取正確的國家/地區。

#### <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>[依據卡片進行身分識別驗證] 區段中的進度列停止回應

若要使用卡片完成身分識別驗證，您必須允許瀏覽器接受第三方 Cookie。

使用下列步驟來更新瀏覽器的 Cookie 設定。

1. 更新 Cookie 設定。
   - 如果您是使用 **Chrome**：
     - 請選取 [設定] > [顯示進階設定] > [隱私權] > [內容設定]。 清除 [封鎖第三方 Cookie 和網站資料]。

   - 如果您是使用 **Microsoft Edge**：
     - 請選取 [設定] > [檢視進階設定] > [Cookie] > [不要封鎖 Cookie]。

1. 重新整理 Azure 註冊頁面，然後檢查問題是否已獲得解決。
1. 如果重新整理並未解決問題，請結束瀏覽器並重新啟動，然後再試一次。

### <a name="i-saw-a-charge-on-my-free-trial-account"></a>我的免費試用帳戶收到一筆費用

您在註冊後可能會在信用卡帳戶上看到一筆小額度且暫時的驗證保留費用。 此保留會在三到五天內移除。 如果您擔心管理成本，請深入了解[如何避免非預期的成本](https://docs.microsoft.com/azure/cost-management-billing/manage/getting-started)。

## <a name="agreement"></a>合約

完成合約。

## <a name="other-issues"></a>其他問題

### <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>無法啟用 MSDN、BizSpark、BizSparkPlus 或 MPN 之類的 Azure 權益方案

請檢查您是否使用正確的登入認證。 然後，請檢查權益方案並確認您符合資格。
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

## <a name="additional-help-resources"></a>其他說明資源

Azure 計費和訂用帳戶的其他疑難排解文章

- [卡片遭拒](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-declined-card)
- [訂用帳戶登入問題](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-sign-in-issue)
- [找不到訂用帳戶](https://docs.microsoft.com/azure/cost-management-billing/manage/no-subscriptions-found)
- [已停用企業成本檢視](https://docs.microsoft.com/azure/cost-management-billing/manage/enterprise-mgmt-grp-troubleshoot-cost-view)

## <a name="contact-us-for-help"></a>如需協助，請與我們連絡

如果您有問題或需要協助，請[建立支援要求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="find-out-more-about-azure-cost-management"></a>深入了解 Azure 成本管理

- [Azure 成本管理和計費文件](https://docs.microsoft.com/azure/cost-management-billing)

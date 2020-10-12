---
title: 帳戶雙因素驗證的常見問題 - Azure AD
description: 一些較常見雙因素驗證問題和您的公司或學校帳戶的解決方案。
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 09/01/2020
ms.author: curtand
ms.reviewer: kexia
metadata ms.custom: contperfq1
ms.openlocfilehash: 3d95ad4aa100b0a185bde015dfe34d747fc77ec9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89322597"
---
# <a name="common-problems-with-two-factor-verification-and-your-work-or-school-account"></a>雙因素驗證和您的公司或學校帳戶常見問題

有一些常見的雙因素驗證問題，似乎會比任何人想要的還要頻繁發生。 我們彙集了本文來說明最常見問題的修正程式。

您的 Azure Active Directory (Azure AD) 組織可以為您的帳戶開啟雙因素驗證。 當雙因素驗證開啟時，您的帳戶登入需要下列資料的組合：

- 您的使用者名稱
- 您的密碼
- 行動裝置或電話

雙因素驗證比單純密碼更安全，因為雙因素驗證需要您 _知道_ 的某些東西，以及您 _擁有_的東西。 沒有任何駭客都有您的實體電話。

>[!Important]
>如果您是系統管理員，可以在 [Azure AD 文件](../index.yml)中找到更多關於如何設定和管理 Azure AD 環境的資訊。

此內容可協助您使用工作或學校帳戶，也就是您組織提供給您的帳戶 (例如 dritan@contoso.com) 。 如果您在個人 Microsoft 帳戶上遇到雙因素驗證的問題，也就是您自行設定的帳戶 (例如 danielle@outlook.com) ，請參閱開啟 [或關閉 Microsoft 帳戶的雙因素驗證](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)。

## <a name="i-dont-have-my-mobile-device-with-me"></a>我的行動裝置不在身邊

總是會發生。 您離開了行動裝置，現在無法使用手機來驗證您的身分。 或許您先前已新增另一種方法來登入您的帳戶，例如透過辦公室電話。 若是如此，您現在可以使用這個替代方法。 如果您從未新增替代的驗證方法，您可以洽詢組織的技術支援人員尋求協助。

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>若要使用其他驗證方法登入您的公司或學校帳戶

1. 登入您的帳戶，但是在 [雙因素驗證] 頁面上選取 [以另一種方式登入] 連結。

    ![變更登入驗證方法](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    如果您沒有看到 [以另一種方式登入] 的連結，表示您尚未設定任何其他的驗證方法。 您必須連絡系統管理員，請他協助您登入帳戶。

2. 選擇您的替代驗證方法，並繼續雙因素驗證的程序。

## <a name="i-cant-turn-two-factor-verification-off"></a>我無法關閉雙因素驗證

- 如果您使用雙因素驗證搭配 Microsoft 服務的個人帳戶（例如 alain@outlook.com ），則可以 [開啟或關閉此功能](https://account.live.com/proofs/Manage)。

- 如果您在工作或學校帳戶使用雙因素驗證，最可能的原因就是您的組織決定您必須使用這項新增的安全性功能。 您無法個別將其關閉。

如果您無法關閉雙因素驗證，也有可能是因為在組織層級套用了安全性預設值。 如需安全性預設值的詳細資訊，請參閱[什麼是安全性預設值？](../fundamentals/concept-fundamentals-security-defaults.md)

## <a name="my-device-was-lost-or-stolen"></a>我的裝置遺失或遭竊

如果您遺失或行動裝置遭竊，您可以採取下列其中一個動作：

- 使用不同的方法登入。
- 要求組織的支援人員清除您的設定。

強烈建議您讓貴組織的技術支援人員知道您的電話是否遺失或遭竊。 技術支援人員可以對您的帳戶進行適當的更新。 清除您的設定之後，下次登入時，系統會提示您重新[註冊雙因素驗證](multi-factor-authentication-end-user-first-time.md)。

## <a name="im-not-receiving-the-verification-code-sent-to-my-mobile-device"></a>我未收到傳送至行動裝置的驗證碼

未接收您的驗證碼是常見的問題。 問題通常與您的行動裝置及其設定相關。 以下是您可以嘗試的一些動作。

試試看 | 指引資訊
--------- | ------------
重新啟動您的行動裝置 | 有時您的裝置只是需要重新整理。 當您重新開機裝置時，所有背景進程和服務都會結束。 重新開機也會關閉您裝置的核心元件。 當您重新開機裝置時，任何服務或元件都會重新整理。
確認您的安全性資訊正確 | 請確定您的安全性驗證方法資訊正確，尤其是您的電話號碼。 如果您輸入錯誤的電話號碼，您所有的警示將會發送至不正確的號碼。 幸運的是，該使用者無法對警示執行任何動作，但是也無法協助您登入您的帳戶。 若要確保您的資訊正確無誤，請參閱[管理雙因素驗證方法設定](multi-factor-authentication-end-user-manage-settings.md)一文中的指示。
確認您的通知已開啟 | 請確定您的行動裝置已開啟通知。 確定允許下列通知模式： <br/><br/> &bull; 電話 <br/> &bull; 您的驗證應用程式 <br/> &bull; 您的文字訊息應用程式 <br/><br/> 確定這些模式會建立可在您的裝置上 _看到_ 的警示。
請確定您有裝置信號和網際網路連線 | 請確定您的電話來電和文字簡訊會通過您的行動裝置。 讓朋友打電話給您以及傳送文字簡訊給您，以確保您兩者都能收到。 如果您未收到通話或文字，請先檢查以確定您的行動裝置已開啟。 如果您的裝置已開啟，但您仍未收到通話或文字，可能是您的網路有問題。 您必須與您的提供者交談。 如果您經常有訊號相關問題，建議您在行動裝置上使用 [Microsoft Authenticator 應用程式](user-help-auth-app-download-install.md)。 驗證器應用程式可以產生用於登入的隨機安全驗證碼，無須任何手機訊號或網際網路連線。
關閉請勿打擾 | 請確定您的行動裝置未開啟 [請勿打擾] 功能。 開啟這項功能時，不允許通知在您的行動裝置上發出警示。 如需如何關閉此功能的指示，請參閱行動裝置的手冊。
解除封鎖電話號碼 | 在美國，Microsoft 的語音電話有下列號碼：+1 (866) 539 4191、+1 (855) 330 8653 和 +1 (877) 668 6536。
檢查您的電池相關設定 | 如果您將電池優化設定為停止在背景中剩餘使用中的應用程式不常使用，您的通知系統可能會受到影響。 請嘗試為您的驗證應用程式和訊息應用程式關閉電池優化。 然後再次嘗試登入您的帳戶。
停用第三方安全性應用程式 | 某些電話安全性應用程式會封鎖來自討厭未知來電者的文字訊息和通話。 安全性應用程式可能會讓您的電話無法接收驗證碼。 嘗試停用手機上的任何協力廠商安全性應用程式，然後要求傳送另一個驗證碼。

## <a name="im-not-being-prompted-for-my-second-verification-information"></a>我不會收到第二次驗證資訊的提示

您可以使用您的使用者名稱和密碼登入公司或學校帳戶。 接下來，系統應該會提示您輸入額外的安全性驗證資訊。 如果未出現提示，可能是您尚未設定裝置。 您的行動裝置必須設定為使用您的特定額外安全性驗證方法。

也許您還沒設定過您的裝置。 您的行動裝置必須設定為使用特定的額外安全性驗證方法。 如需讓行動裝置可與您的驗證方法搭配使用的步驟，請參閱 [管理您的雙因素驗證方法設定](multi-factor-authentication-end-user-manage-settings.md)。 如果您知道尚未設定您的裝置或帳戶，您可以依照「 [設定我的帳戶進行雙步驟驗證」一](multi-factor-authentication-end-user-first-time.md) 文中的步驟進行。

## <a name="i-have-a-new-phone-number-and-i-want-to-add-it"></a>我有新的電話號碼，我想要新增它

如果您有新的電話號碼，則必須更新您的安全性驗證方法詳細資料。 這可讓您的驗證提示移至正確的位置。 若要更新您的驗證方法，請依照 [[管理您的雙因素驗證方法設定](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number)] 文章中的 [**新增或變更您的電話號碼**] 一節中的步驟進行。

## <a name="i-have-a-new-mobile-device-and-i-want-to-add-it"></a>我有新的行動裝置，我想要新增它

如果您有新的行動裝置，則必須將它設定為使用雙因素驗證。 這是一個多步驟解決方案：

1. 遵循「 [設定我的帳戶中的雙步驟驗證](multi-factor-authentication-end-user-first-time.md) 」一文中的步驟，設定您的裝置以使用您的帳戶。

1. 在 [ **其他安全性驗證** ] 頁面中更新您的帳戶和裝置資訊。 藉由刪除舊裝置並新增新的裝置來執行更新。 如需詳細資訊，請參閱[管理您的雙因素驗證方法設定](multi-factor-authentication-end-user-manage-settings.md)一文。

選擇性步驟：

- 遵循 [下載和安裝 Microsoft Authenticator 應用程式](user-help-auth-app-download-install.md) 文章中的步驟，在您的行動裝置上安裝 Microsoft Authenticator 應用程式。

- 依照[管理您的雙因素驗證方法設定](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device)一文中**在受信任的裝置上開啟雙因素驗證提示**一節的步驟，開啟受信任裝置的雙因素驗證。

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>我在旅行時登入行動裝置遇到問題

當您在國外時，您可能會發現使用行動裝置相關驗證方法 (例如文字簡訊) 更加困難。 您的行動裝置可能會導致產生漫遊費用。 在此情況下，建議您使用 Microsoft Authenticator 應用程式，並使用連線到 Wi-Fi 作用點的選項。 如需如何在行動裝置上設定 Microsoft Authenticator 應用程式的詳細資訊，請參閱 [下載並安裝 Microsoft Authenticator 應用程式](user-help-auth-app-download-install.md) 文章。

## <a name="i-cant-get-my-app-passwords-to-work"></a>我無法讓應用程式密碼正常執行

針對不支援雙因素驗證的較舊桌面應用程式，應用程式密碼會取代您的一般密碼。 請先確定您輸入的密碼正確。 如果無法修正此問題，請嘗試為應用程式建立新的應用程式密碼。 請依照「[管理雙步驟驗證的應用程式密碼](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page)」一文中的「**使用我的應用程式入口網站來建立和刪除應用程式密碼**」一節中的步驟執行。

## <a name="i-cant-turn-off-two-factor-verification"></a>我無法關閉雙因素驗證

如果您搭配公司或學校帳戶使用雙因素驗證 (例如 alain@contoso.com)，很有可能是貴組織已決定您必須使用這項額外安全性功能。 因為您的組織已決定您必須使用此功能，所以您無法個別關閉此功能。 不過，如果您搭配個人帳戶使用雙因素驗證 (例如 alain@outlook.com)，您就能夠開啟和關閉此功能。 如需如何針對您的個人帳戶控制雙因素驗證的指示，請參閱[開啟或關閉 Microsoft 帳戶的雙因素驗證](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)。

如果您無法關閉雙因素驗證，也有可能是因為在組織層級套用了安全性預設值。 如需安全性預設值的詳細資訊，請參閱[什麼是安全性預設值？](../fundamentals/concept-fundamentals-security-defaults.md)

## <a name="i-didnt-find-an-answer-to-my-problem"></a>我找不到我的問題的解答

如果您已嘗試過這些步驟，但仍遭遇問題時，請連絡貴組織的技術支援中心，以取得協助。

## <a name="related-articles"></a>相關文章

- [管理您的雙因素驗證方法設定](multi-factor-authentication-end-user-manage-settings.md)

- [對我的帳戶進行雙步驟驗證設定](multi-factor-authentication-end-user-first-time.md)

- [Microsoft Authenticator 應用程式常見問題集](user-help-auth-app-faq.md)
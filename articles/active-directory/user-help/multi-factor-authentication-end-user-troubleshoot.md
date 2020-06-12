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
ms.date: 04/02/2020
ms.author: curtand
ms.reviewer: kexia
ms.openlocfilehash: 8d2e34bcfd180dfeb814dace2a496f3ac593c5bf
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83738603"
---
# <a name="common-problems-with-two-factor-verification-and-your-work-or-school-account"></a>雙因素驗證和您的公司或學校帳戶常見問題

當您的 Azure Active Directory (Azure AD) 組織開啟雙因素驗證時，您的公司或學校帳戶登入需要您的使用者名稱、密碼與行動裝置或手機的組合。 比只是使用密碼更安全，因為它依賴兩種形式的驗證：您知道和您擁有的東西。 雙因素驗證有助於阻止惡意駭客偽裝成您，因為即使他們有您的密碼，他們沒有您的裝置。

<center>

![概念性驗證方法圖](../authentication/media/concept-mfa-howitworks/methods.png)</center>

有一些常見的雙因素驗證問題，似乎會比任何人想要的還要頻繁發生。 我們已將這篇文章彙整在一起，以解決最常見的問題和一些可能的修正。

>[!Important]
>如果您是系統管理員，可以在 [Azure AD 文件](https://docs.microsoft.com/azure/active-directory)中找到更多關於如何設定和管理 Azure AD 環境的資訊。
>
>此內容也僅適用於您的公司或學校帳戶，這是貴組織提供給您的帳戶 (例如 alain@contoso.com)。 如果您遇到雙因素驗證和您自行設定的個人 Microsoft 帳戶 (例如 danielle@outlook.com) 問題，請參閱[開啟或關閉 Microsoft 帳戶的雙因素驗證](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)。

## <a name="i-dont-have-my-mobile-device-with-me"></a>我的行動裝置不在身邊

總是會發生。 您將行動裝置遺留在家，現在無法使用您的手機來驗證您的身分。 如果您先前新增了另一個方法來登入您的帳戶 (例如辦公室電話)，您應該能夠立即使用該方法。 如果您從未新增額外的驗證方法，就必須洽詢貴組織的技術支援中心，讓他們協助您取回帳戶。

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>若要使用其他驗證方法登入您的公司或學校帳戶

1. 登入您的帳戶，但是在 [雙因素驗證] 頁面上選取 [以另一種方式登入] 連結。

    ![變更登入驗證方法](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    如果您沒有看到 [以另一種方式登入] 的連結，表示您尚未設定任何其他的驗證方法。 您必須連絡系統管理員，請他協助您登入帳戶。

2. 選擇您的替代驗證方法，並繼續雙因素驗證的程序。

## <a name="i-lost-my-mobile-device-or-it-was-stolen"></a>我遺失了我的行動裝置或是遭竊

如果您遺失行動裝置或遭竊，您可以使用不同的方法登入，也可以要求貴組織的技術支援中心清除您的設定。 我們強烈建議您讓貴組織的技術支援中心知道您的手機已遺失或遭竊，這樣才能對您的帳戶進行適當更新。 清除您的設定之後，下次登入時，系統會提示您重新[註冊雙因素驗證](multi-factor-authentication-end-user-first-time.md)。

## <a name="im-not-getting-the-verification-code-sent-to-my-mobile-device"></a>我未取得傳送至行動裝置的驗證碼

未取得您的驗證碼是常見問題，通常與您的行動裝置及其設定相關。 可以嘗試的一些可行動作如下：

試試看 | 指引資訊
--------- | ------------
重新啟動您的行動裝置 | 有時您的裝置只是需要重新整理。 重新啟動裝置會結束目前正在執行而且可能會造成問題的任何背景處理程序或服務，並且重新整理裝置的核心元件，請重新啟動以防在某個時間點損毀。
確認您的安全性資訊正確 | 請確定您的安全性驗證方法資訊正確，尤其是您的電話號碼。 如果您輸入錯誤的電話號碼，您所有的警示將會發送至不正確的號碼。 幸運的是，該使用者無法對警示執行任何動作，但是也無法協助您登入您的帳戶。 若要確保您的資訊正確無誤，請參閱[管理雙因素驗證方法設定](multi-factor-authentication-end-user-manage-settings.md)一文中的指示。
確認您的通知已開啟 | 請確定您的行動裝置已開啟通知，而且您已選取允許電話來電、驗證應用程式和訊息應用程式 (用於文字簡訊) 的通知方法，將可見的警示通知傳送到您的行動裝置。
請確定您有裝置信號和網際網路連線 | 請確定您的電話來電和文字簡訊會通過您的行動裝置。 讓朋友打電話給您以及傳送文字簡訊給您，以確保您兩者都能收到。 如果收不到，請先檢查以確定您的行動裝置已開啟。 如果您的裝置已開啟，但是您仍未收到來電或簡訊，很可能是您的網路有問題，您必須洽詢您的提供者。 如果您經常有訊號相關問題，建議您在行動裝置上使用 [Microsoft Authenticator 應用程式](user-help-auth-app-download-install.md)。 驗證器應用程式可以產生用於登入的隨機安全驗證碼，無須任何手機訊號或網際網路連線。
關閉請勿打擾 | 請確定您的行動裝置未開啟 [請勿打擾] 功能。 開啟這項功能時，不允許通知在您的行動裝置上發出警示。 如需如何關閉此功能的指示，請參閱行動裝置的手冊。
解除封鎖電話號碼 | 在美國，Microsoft 的語音電話有下列號碼：+1 (866) 539 4191、+1 (855) 330 8653 和 +1 (877) 668 6536。
檢查您的電池相關設定 | 這一點表面上看起來有點奇怪，但是如果您已設定電池最佳化，以停止較少使用的應用程式在背景維持作用中，您的通知系統很有可能會受到影響。 若要嘗試修正此問題，請關閉驗證應用程式和訊息應用程式的電池最佳化，然後再次嘗試登入您的帳戶。
停用第三方安全性應用程式 | 如果您有應用程式會保護文字簡訊或電話來電，以將未知來電者的情形降至最低，則可能會導致無法收到驗證碼。 嘗試在您的電話上停用任何第三方安全性應用程式，然後要求傳送另一個驗證碼。

## <a name="im-not-getting-prompted-for-my-second-verification-information"></a>我未收到第二個驗證資訊的提示

如果您已使用您的使用者名稱和密碼登入公司或學校帳戶，但是未收到額外安全性驗證資訊的提示，可能是您尚未設定您的裝置。 您的行動裝置必須設定為使用您的特定額外安全性驗證方法。 若要確定您已開啟行動裝置，且其可與您的驗證方法搭配使用，請參閱[管理您的雙因素驗證方法設定](multi-factor-authentication-end-user-manage-settings.md)一文。 如果您知道您尚未設定您的裝置或帳戶，您可以遵循[對我的帳戶進行雙步驟驗證設定](multi-factor-authentication-end-user-first-time.md)一文中的步驟來進行。

## <a name="i-got-a-new-phone-number-and-i-want-to-add-it"></a>我有新的電話號碼而且想要新增

如果您有新的電話號碼，則必須更新您的安全性驗證方法詳細資料，讓您的驗證提示發送至正確的位置。 若要更新您的驗證方法，請遵循[管理您的雙因素驗證方法設定](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number)一文中**新增或變更您的電話號碼**一節的步驟。

## <a name="i-got-a-new-mobile-device-and-i-want-to-add-it"></a>我有新的行動裝置而且想要新增

如果您有新的行動裝置，則必須加以設定才能使用雙因素驗證。 這是一個多步驟解決方案：

1. 依照[對我的帳戶進行雙步驟驗證設定](multi-factor-authentication-end-user-first-time.md)一文中的步驟，設定您的裝置以使用您的公司或學校帳戶。

1. 在 [額外安全性驗證] 頁面中更新您的帳戶和裝置資訊，刪除舊的裝置並新增新的裝置。 如需詳細資訊，請參閱[管理您的雙因素驗證方法設定](multi-factor-authentication-end-user-manage-settings.md)一文。

選擇性步驟：

- 遵循[下載並安裝 Microsoft Authenticator 應用程式](user-help-auth-app-download-install.md)一文中的步驟，在行動裝置上下載、安裝及設定 Microsoft Authenticator 應用程式。

- 依照[管理您的雙因素驗證方法設定](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device)一文中**在受信任的裝置上開啟雙因素驗證提示**一節的步驟，開啟受信任裝置的雙因素驗證。

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>我在旅行時登入行動裝置遇到問題

當您在國外時，您可能會發現使用行動裝置相關驗證方法 (例如文字簡訊) 更加困難。 您的行動裝置可能會導致產生漫遊費用。 針對這種情況，建議您使用 Microsoft Authenticator 應用程式，並選擇連線到 Wi-Fi 熱點。 如需關於如何在行動裝置上下載、安裝及設定 Microsoft Authenticator 應用程式的詳細資訊，請參閱[下載並安裝 Microsoft Authenticator 應用程式](user-help-auth-app-download-install.md)一文。

## <a name="i-cant-get-my-app-passwords-to-work"></a>我無法讓應用程式密碼正常執行

針對不支援雙因素驗證的較舊桌面應用程式，應用程式密碼會取代您的一般密碼。 請先確定您輸入的密碼正確。 如果無法修正此問題，請嘗試遵循[管理適用於雙步驟驗證的應用程式密碼](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page)一文中**使用我的應用程式入口網站建立和刪除應用程式密碼**一節的步驟，為應用程式建立新的應用程式密碼。

## <a name="i-cant-turn-two-factor-verification-off"></a>我無法關閉雙因素驗證

如果您搭配公司或學校帳戶使用雙因素驗證 (例如 alain@contoso.com)，很有可能是貴組織已決定您必須使用這項額外安全性功能。 因為您的組織已決定您必須使用此功能，所以您無法個別關閉此功能。 不過，如果您搭配個人帳戶使用雙因素驗證 (例如 alain@outlook.com)，您就能夠開啟和關閉此功能。 如需如何針對您的個人帳戶控制雙因素驗證的指示，請參閱[開啟或關閉 Microsoft 帳戶的雙因素驗證](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)。

如果您無法關閉雙因素驗證，也有可能是因為在組織層級套用了安全性預設值。 如需安全性預設值的詳細資訊，請參閱[什麼是安全性預設值？](../fundamentals/concept-fundamentals-security-defaults.md)

## <a name="i-didnt-find-an-answer-to-my-problem"></a>我找不到我的問題的解答

如果您已嘗試過這些步驟，但仍遭遇問題時，請連絡貴組織的技術支援中心，以取得協助。

## <a name="related-articles"></a>相關文章

- [管理您的雙因素驗證方法設定](multi-factor-authentication-end-user-manage-settings.md)

- [對我的帳戶進行雙步驟驗證設定](multi-factor-authentication-end-user-first-time.md)

- [Microsoft Authenticator 應用程式常見問題集](user-help-auth-app-faq.md)

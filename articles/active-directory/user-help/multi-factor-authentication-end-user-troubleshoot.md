---
title: 帳戶雙因素驗證的常見問題-Azure AD
description: 解決一些常見的雙因素驗證問題，以及您的公司或學校帳戶的解決方案。
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: curtand
ms.reviewer: kexia
ms.openlocfilehash: c28b63749cfdbcd16b94cbd3ca7dd4023f46a351
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897734"
---
# <a name="common-problems-with-two-factor-verification-and-your-work-or-school-account"></a>雙因素驗證和您的工作或學校帳戶的常見問題

當您的 Azure Active Directory （Azure AD）組織開啟雙因素驗證時，您的工作或學校帳戶登入需要結合您的使用者名稱、密碼和行動裝置或電話。 它比密碼更安全，依賴兩種驗證形式：您知道的東西，以及您所擁有的東西。 雙因素驗證有助於防止惡意駭客偽裝成您，因為即使他們有您的密碼，也有可能不會有您的裝置。

<center>

![的概念驗證方法影像](../authentication/media/concept-mfa-howitworks/methods.png)</center>

有一些常見的雙因素驗證問題，似乎會比任何人想要的還要頻繁。 我們已將這篇文章放在一起，以解決最常見的問題和一些可能的修正。

>[!Important]
>如果您是系統管理員，您可以在[Azure AD 檔](https://docs.microsoft.com/azure/active-directory)中找到有關如何設定和管理您的 Azure AD 環境的詳細資訊。
>
>此內容也僅適用于您的公司或學校帳戶，這是您的組織提供給您的帳戶（例如 alain@contoso.com）。 如果您遇到雙因素驗證和個人 Microsoft 帳戶的問題，也就是您自行設定的帳戶（例如 danielle@outlook.com），請參閱[為您的 Microsoft 帳戶開啟或關閉雙因素驗證](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)。

## <a name="i-dont-have-my-mobile-device-with-me"></a>我沒有我的行動裝置

就會發生這種情況。 您的行動裝置已離開家裡，現在無法使用您的電話來驗證您的身分。 如果您先前新增了另一個方法來登入您的帳戶（例如辦公室電話），您應該能夠立即使用該方法。 如果您從未新增額外的驗證方法，就必須洽詢貴組織的技術支援人員，讓他們協助您取回帳戶。

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>使用其他驗證方法登入您的公司或學校帳戶

1. 登入您的帳戶，但選取 [**雙因素驗證**] 頁面上的 [**以另一種方式登入**] 連結。

    ![變更登入驗證方法](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    如果您沒有看到 [以另一種方式登入] 的連結，表示您尚未設定任何其他的驗證方法。 您必須連絡系統管理員，請他協助您登入帳戶。

2. 選擇您的替代驗證方法，並繼續進行雙因素驗證程式。

## <a name="i-lost-my-mobile-device-or-it-was-stolen"></a>我的行動裝置遺失或遭竊

如果您遺失或行動裝置遭竊，您可以使用不同的方法登入，也可以要求貴組織的技術支援人員清除您的設定。 我們強烈建議您讓組織的技術支援人員知道您的手機遺失或遭竊，因此可以對您的帳戶進行適當的更新。 清除您的設定之後，系統會提示您在下一次登入時[註冊雙因素驗證](multi-factor-authentication-end-user-first-time.md)。

## <a name="im-not-getting-the-verification-code-sent-to-my-mobile-device"></a>我未取得傳送至行動裝置的驗證碼

未取得您的驗證碼是常見的問題，而且通常與您的行動裝置及其設定相關。 一些可能的嘗試事項：

試試這個 | 指引資訊
--------- | ------------
重新開機您的行動裝置 | 有時您的裝置只需要重新整理。 重新開機裝置會結束目前正在執行的任何背景處理常式或服務，而且可能會造成問題，以及重新整理裝置的核心元件，以在某個時間點損毀的情況下重新開機。
確認您的安全性資訊正確 | 請確定您的安全性驗證方法資訊正確，尤其是您的電話號碼。 如果您放入錯誤的電話號碼，您所有的警示將會移至不正確的數位。 幸運的是，該使用者將無法對警示執行任何動作，但它也無法協助您登入您的帳戶。 若要確保您的資訊正確無誤，請參閱[管理雙因素驗證方法設定](multi-factor-authentication-end-user-manage-settings.md)一文中的指示。
確認您的通知已開啟 | 請確定您的行動裝置已開啟通知，而且您已選取允許電話、驗證應用程式和訊息應用程式（用於文字訊息）的通知方法，將顯示的警示通知傳送到您的行動裝置。
請確定您有裝置信號和網際網路連線 | 請確定您的電話和文字訊息會通過您的行動裝置。 讓朋友打電話給您，並傳送文字訊息給您，以確保您同時收到這兩者。 如果您沒有這麼做，請先檢查以確定您的行動裝置已開啟。 如果您的裝置已開啟，但您仍未收到電話或文字，很可能是您的網路有問題，而且您必須與您的提供者交談。 如果您經常遇到信號相關問題，建議您在行動裝置上安裝並使用[Microsoft Authenticator 應用程式](user-help-auth-app-download-install.md)。 驗證器應用程式可以產生用於登入的隨機安全程式碼，而不需要任何資料格信號或網際網路連線。
關閉請勿打擾 | 請確定您未針對您的行動裝置開啟 [**請勿打擾**] 功能。 開啟這項功能時，不允許通知在您的行動裝置上發出警示。 如需如何關閉此功能的指示，請參閱行動裝置的手冊。
解除封鎖電話號碼 | 在美國，來自 Microsoft 的語音電話來自下列數位： + 1 （866） 539 4191、+ 1 （855） 330 8653 和 + 1 （877） 668 6536。
檢查您的電池相關設定 | 此介面在表面上看起來有點奇怪，但如果您已設定電池優化，以停止在背景中從剩餘作用中使用的低應用程式，您的通知系統可能會受到影響。 若要嘗試修正此問題，請關閉驗證應用程式和訊息應用程式的電池優化，然後再次嘗試登入您的帳戶。

## <a name="im-not-getting-prompted-for-my-second-verification-information"></a>我不會收到第二個驗證資訊的提示

如果您已使用使用者名稱和密碼登入公司或學校帳戶，但未收到其他安全性驗證資訊的提示，可能是您尚未設定您的裝置。 您的行動裝置必須設定為使用您的特定額外安全性驗證方法。 若要確定您已開啟行動裝置，並可與您的驗證方法搭配使用，請參閱[管理您的雙因素驗證方法設定](multi-factor-authentication-end-user-manage-settings.md)一文。 如果您知道您尚未設定您的裝置或帳戶，您可以遵循[針對雙步驟驗證設定我的帳戶一](multi-factor-authentication-end-user-first-time.md)文中的步驟來完成。

## <a name="i-got-a-new-phone-number-and-i-want-to-add-it"></a>我收到新的電話號碼，我想要將它新增

如果您已取得新的電話號碼，則必須更新您的安全性驗證方法詳細資料，讓您的驗證提示會移至正確的位置。 若要更新您的驗證方法，請遵循[管理您的雙因素驗證方法設定](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number)一文中的**新增或變更您的電話號碼**一節中的步驟。

## <a name="i-got-a-new-mobile-device-and-i-want-to-add-it"></a>我收到新的行動裝置，我想要將它新增

如果您已取得新的行動裝置，則必須將其設定為使用雙因素驗證。 這是一個多步驟解決方案：

1. 依照[針對雙步驟驗證設定我的帳戶](multi-factor-authentication-end-user-first-time.md)一文中的步驟，設定您的裝置以使用您的工作或學校帳戶。

1. 在 [**其他安全性驗證**] 頁面中更新您的帳戶和裝置資訊，並刪除舊的裝置並新增新的裝置。 如需詳細資訊，請參閱[管理您的雙因素驗證方法設定](multi-factor-authentication-end-user-manage-settings.md)一文。

選擇性步驟：

- 遵循[下載並安裝 Microsoft Authenticator 應用程式](user-help-auth-app-download-install.md)文章中的步驟，在您的行動裝置上下載、安裝和設定 Microsoft Authenticator 應用程式。

- 遵循[管理您的雙因素驗證方法設定](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device)一文中的在**信任的裝置上開啟雙因素驗證提示**一節中的步驟，為信任的裝置開啟雙因素驗證。

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>我在旅行時登入行動裝置時遇到問題

當您在國際位置時，您可能會發現，使用行動裝置相關的驗證方法（例如文字訊息）就比較不容易。 您的行動裝置也可能會導致您產生漫遊費用。 在此情況下，建議您使用 Microsoft Authenticator 應用程式，並選擇連接到 Wi-fi 熱點。 如需有關如何在您的行動裝置上下載、安裝及設定 Microsoft Authenticator 應用程式的詳細資訊，請參閱[下載並安裝 Microsoft Authenticator 應用程式](user-help-auth-app-download-install.md)一文。

## <a name="i-cant-get-my-app-passwords-to-work"></a>我無法讓應用程式密碼正常執行

應用程式密碼會針對不支援雙因素驗證的舊版桌面應用程式，取代您的一般密碼。 請先確定您輸入的密碼正確。 如果無法修正此問題，請嘗試我的應用程式使用[管理應用程式密碼以進行雙步驟驗證](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page)一文中的**建立和刪除應用程式**密碼一節中的步驟，為應用程式建立新的應用程式密碼。

## <a name="i-cant-turn-two-factor-verification-off"></a>我無法關閉雙因素驗證

如果您使用與公司或學校帳戶的雙因素驗證（例如 alain@contoso.com），則最有可能表示您的組織已決定您必須使用這項新增的安全性功能。 因為您的組織已決定您必須使用此功能，所以您無法個別關閉此功能。 不過，如果您使用具有個人帳戶的雙因素驗證（例如 alain@outlook.com），您就能夠開啟和關閉此功能。 如需如何為您的個人帳戶控制雙因素驗證的指示，請參閱開啟[或關閉 Microsoft 帳戶的雙因素驗證](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)。

## <a name="i-didnt-find-an-answer-to-my-problem"></a>我找不到我的問題的解答

如果您嘗試了這些步驟，但仍遇到問題，請洽詢貴組織的技術支援人員以尋求協助。

## <a name="related-articles"></a>相關文章

- [管理您的雙因素驗證方法設定](multi-factor-authentication-end-user-manage-settings.md)

- [設定我的帳戶以進行雙步驟驗證](multi-factor-authentication-end-user-first-time.md)

- [Microsoft Authenticator 應用程式常見問題集](user-help-auth-app-faq.md)

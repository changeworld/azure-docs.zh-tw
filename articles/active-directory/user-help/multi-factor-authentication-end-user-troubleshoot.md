---
title: 帳戶雙重身份驗證的常見問題 - Azure AD
description: 解決一些更常見的雙因素驗證問題和您的工作或學校帳戶。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78897734"
---
# <a name="common-problems-with-two-factor-verification-and-your-work-or-school-account"></a>雙重驗證和您的工作或學校帳戶的常見問題

當 Azure 活動目錄 （Azure AD） 組織啟用雙重驗證時，工作或學校帳戶登錄需要將使用者名、密碼和行動裝置或電話組合在一起。 它比密碼更安全，它依賴于兩種身份驗證形式：您知道的，以及您擁有的東西。 雙重驗證可以説明阻止惡意駭客假裝是您，因為即使他們有您的密碼，他們也沒有您的設備的可能性。

<center>

![概念性驗證方法圖](../authentication/media/concept-mfa-howitworks/methods.png)</center>

有一些常見的雙因素驗證問題似乎比我們任何人希望更頻繁地發生。 我們整理了本文，以解決最常見的問題和一些可能的解決方法。

>[!Important]
>如果您是管理員，則可以在[Azure AD 文檔中](https://docs.microsoft.com/azure/active-directory)找到有關如何設置和管理 Azure AD 環境的詳細資訊。
>
>此內容也僅用於您的工作或學校帳戶，這是您的組織提供給您的帳戶（例如。 alain@contoso.com 如果您在雙因素驗證和個人 Microsoft 帳戶（例如danielle@outlook.com），您自己設置的帳戶（例如），遇到問題，請參閱為 Microsoft[帳戶打開或關閉雙因素驗證](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)。

## <a name="i-dont-have-my-mobile-device-with-me"></a>我沒有我的行動裝置

它發生。 您將行動裝置留在家中，現在無法使用手機驗證您是誰。 如果您以前添加了另一種方法來登錄到您的帳戶（如辦公室電話），則現在應該能夠使用該方法。 如果您從未添加過其他驗證方法，您必須聯繫組織的説明台，並讓他們説明您重新進入您的帳戶。

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>使用其他驗證方法登錄到您的工作或學校帳戶

1. 登錄到您的帳戶，但在 **"雙因素驗證**"頁上選擇 **"以其他方式登錄"** 連結。

    ![變更登入驗證方法](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    如果您沒有看到 [以另一種方式登入]**** 的連結，表示您尚未設定任何其他的驗證方法。 您必須連絡系統管理員，請他協助您登入帳戶。

2. 選擇替代驗證方法，然後繼續雙因素驗證過程。

## <a name="i-lost-my-mobile-device-or-it-was-stolen"></a>我的行動裝置丟失或被盜

如果您丟失或行動裝置被盜，則可以使用其他方法登錄，也可以要求組織的説明台清除設置。 我們強烈建議讓組織的説明台知道您的手機是否丟失或被盜，以便對您的帳戶進行適當的更新。 清除設置後，系統將提示您下次登錄時[註冊雙因素驗證](multi-factor-authentication-end-user-first-time.md)。

## <a name="im-not-getting-the-verification-code-sent-to-my-mobile-device"></a>我沒有將驗證碼發送到我的行動裝置

未獲取驗證碼是一個常見問題，它通常與您的行動裝置及其設置有關。 一些可能嘗試的事情：

試試這個 | 指導資訊
--------- | ------------
重新開機行動裝置 | 有時您的設備只需要刷新。 重新開機設備將結束當前正在運行的任何後臺進程或服務，並可能導致問題，同時刷新設備的核心元件，重新開機它們以防它們在某個時候崩潰。
驗證您的安全資訊是否正確 | 確保您的安全驗證方法資訊準確無誤，尤其是電話號碼。 如果您輸入了錯誤的電話號碼，則所有警報都將轉到該不正確的號碼。 幸運的是，該使用者無法對警報執行任何操作，但它也無法説明您登錄您的帳戶。 要確保資訊正確，請參閱[管理雙因素驗證方法設置](multi-factor-authentication-end-user-manage-settings.md)一文中的說明。
驗證通知已打開 | 確保行動裝置已打開通知，並且已選擇允許撥打電話、身份驗證應用和消息應用（用於短信）的通知方法向行動裝置發送可見警報通知。
確保您有設備信號和互聯網連接 | 確保您的電話和短信正在發送到您的行動裝置。 讓朋友打電話給你，並向您發送一條短信，以確保您同時收到這兩個消息。 如果沒有，請先檢查以確保行動裝置已打開。 如果設備已打開，但您仍未收到呼叫或短信，則很可能是網路問題，您需要與供應商聯繫。 如果您經常遇到與信號相關的問題，我們建議您在行動裝置上安裝和使用 Microsoft[身份驗證器應用](user-help-auth-app-download-install.md)。 身份驗證器應用可以為登錄生成隨機安全代碼，而無需任何單元信號或互聯網連接。
關閉 請勿打擾 | 請確保您尚未打開行動裝置的 **"請勿打擾**"功能。 啟用此功能後，不允許通知在行動裝置上提醒您。 有關如何關閉此功能的說明，請參閱行動裝置手冊。
取消阻止電話號碼 | 在美國，來自 Microsoft 的語音電話來自以下號碼：+1 （866） 539 4191、+1 （855） 330 8653 和 +1 （877） 668 6536。
檢查電池相關設置 | 從表面上看，這一點似乎有點奇怪，但如果您設置了電池優化，以阻止使用較少的應用在後臺保持活動狀態，則通知系統很可能受到影響。 要嘗試解決此問題，請關閉身份驗證應用和消息應用的電池優化，然後嘗試再次登錄到您的帳戶。

## <a name="im-not-getting-prompted-for-my-second-verification-information"></a>系統不會提示我提供第二個驗證資訊

如果您已使用使用者名和密碼登錄到工作或學校帳戶，但未提示有關您的其他安全驗證資訊，則可能尚未設置設備。 必須設置行動裝置才能使用特定的附加安全驗證方法。 為了確保已打開行動裝置，並且可以使用驗證方法，請參閱[管理雙因素驗證方法設置](multi-factor-authentication-end-user-manage-settings.md)一文。 如果您知道尚未設置設備或帳戶，則現在可以通過按照["設置我的帳戶進行兩步驗證](multi-factor-authentication-end-user-first-time.md)"一文中的步驟進行設置。

## <a name="i-got-a-new-phone-number-and-i-want-to-add-it"></a>我有一個新的電話號碼，我想添加它

如果您獲得了新電話號碼，則需要更新安全驗證方法詳細資訊，以便驗證提示轉到正確的位置。 要更新驗證方法，請按照"**添加"或"**[更改雙因素驗證方法設置](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number)"一文中的電話號碼部分的步驟進行操作。

## <a name="i-got-a-new-mobile-device-and-i-want-to-add-it"></a>我有一個新的行動裝置，我想添加它

如果您獲得了新的行動裝置，則需要將其設置為使用雙重驗證。 這是一個多步驟解決方案：

1. 按照["設置我的帳戶進行兩步驗證](multi-factor-authentication-end-user-first-time.md)"一文中的步驟，將設備設置為與工作或學校帳戶一起工作。

1. 在 **"其他安全驗證**"頁中更新您的帳戶和設備資訊，刪除舊設備並添加新設備。 有關詳細資訊，請參閱[管理雙因素驗證方法設置](multi-factor-authentication-end-user-manage-settings.md)一文。

選擇性步驟：

- 按照["下載並安裝 Microsoft 身份驗證器"應用](user-help-auth-app-download-install.md)文章中的步驟，在行動裝置上下載、安裝和設置 Microsoft 身份驗證器應用。

- 按照"打開雙因素驗證[方法設置](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device)"一節中的 **"打開雙因素驗證提示"** 中的步驟，為受信任的設備打開雙因素驗證。

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>旅行時，我在行動裝置上登錄時遇到問題

當您處於國際位置時，您可能會發現使用行動裝置相關的驗證方法（如短信）更加困難。 您的行動裝置還可能導致您產生漫遊費。 對於這種情況，我們建議您使用 Microsoft 身份驗證器應用，並選擇連接到 Wi-Fi 熱點。 有關如何在行動裝置上下載、安裝和設置 Microsoft 身份驗證器應用的詳細資訊，請參閱[下載並安裝 Microsoft 身份驗證器應用](user-help-auth-app-download-install.md)文章。

## <a name="i-cant-get-my-app-passwords-to-work"></a>我無法使應用密碼正常工作

應用密碼將替換不支援雙重驗證的舊桌面應用程式的正常密碼。 請先確定您輸入的密碼正確。 如果這不能修復它，請嘗試使用["管理應用密碼"的"管理應用密碼"](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page)部分，為應用創建**和刪除應用密碼**，以執行兩步驗證文章的步驟，從而嘗試為應用創建新的應用密碼。

## <a name="i-cant-turn-two-factor-verification-off"></a>我無法關閉雙因素驗證

如果您對工作或學校帳戶（例如alain@contoso.com），使用雙因素驗證，則很可能意味著您的組織已決定必須使用此添加的安全功能。 因為您的組織已決定您必須使用此功能，所以您無法個別關閉此功能。 但是，如果您對個人帳戶（如alain@outlook.com）使用雙重驗證，則可以打開和關閉該功能。 有關如何控制個人帳戶的雙因素驗證的說明，請參閱[為 Microsoft 帳戶打開或關閉雙因素驗證](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)。

## <a name="i-didnt-find-an-answer-to-my-problem"></a>我找不到我的問題的解答

如果您已嘗試這些步驟，但仍遇到問題，請與組織的説明台聯繫以獲得説明。

## <a name="related-articles"></a>相關文章

- [管理您的雙因素驗證方法設定](multi-factor-authentication-end-user-manage-settings.md)

- [對我的帳戶進行雙步驟驗證設定](multi-factor-authentication-end-user-first-time.md)

- [Microsoft Authenticator 應用程式常見問題集](user-help-auth-app-faq.md)

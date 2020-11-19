---
title: 電話驗證方法-Azure Active Directory
description: 瞭解如何在 Azure Active Directory 中使用電話驗證方法來協助改善和保護登入事件
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4510dac833d0576273472b6f925d546b55257614
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886799"
---
# <a name="authentication-methods-in-azure-active-directory---phone-options"></a>Azure Active Directory-phone 選項中的驗證方法

若要使用簡訊進行直接驗證，您可以[為使用者設定和啟用 SMS 型驗證 (預覽)](howto-authentication-sms-signin.md)。 SMS 型登入很適合第一線工作者使用。 使用 SMS 型登入，使用者不需要知道使用者名稱和密碼，即可存取應用程式和服務。 使用者會改為輸入其已註冊的行動電話號碼、接收包含驗證碼的簡訊，然後在登入介面中輸入驗證碼。

使用者也可以使用行動電話或辦公室電話，作為在 Azure AD Multi-Factor Authentication 或 (SSPR) 的自助式密碼重設期間所使用的次要驗證形式來驗證自己。

為了正確運作，電話號碼的格式必須是：+國碼 電話號碼，例如 +1 4251234567。

> [!NOTE]
> 國碼/區域碼和電話號碼之間需要空格。
>
> 密碼重設不支援電話分機。 即使採用 +1 4251234567X12345 格式，撥號之前都會移除分機號碼。

## <a name="mobile-phone-verification"></a>行動電話驗證

針對 Azure AD Multi-Factor Authentication 或 SSPR，使用者可以選擇接收包含驗證碼的文字訊息，以便在登入介面中輸入或接收電話。

如果使用者不想在目錄中顯示其行動電話號碼，但想要用於密碼重設，系統管理員便不應將電話號碼填入目錄。 使用者反而應該透過合併安全性資訊註冊 (位於 [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo))，填入其 **驗證電話** 屬性。 管理員可以在使用者的設定檔中看到此資訊，但該資訊不會發佈在其他地方。

:::image type="content" source="media/concept-authentication-methods/user-authentication-methods.png" alt-text="Azure 入口網站的螢幕擷取畫面，其中顯示已填入電話號碼的驗證方法":::

Microsoft 不保證一致的 SMS 或以語音為基礎的 Azure AD Multi-Factor Authentication 提示傳遞相同的號碼。 為了使用者的利益，我們可能會在調整路由來改善 SMS 傳遞能力時，隨時新增或移除簡短代碼。 Microsoft 不支援美國和加拿大以外之國家/區域的簡短代碼。

### <a name="text-message-verification"></a>簡訊驗證

在 SSPR 或 Azure AD Multi-Factor Authentication 期間驗證文字訊息時，系統會將 SMS 傳送至包含驗證碼的行動電話號碼。 為了完成登入程序，會在登入介面中輸入所提供的驗證碼。

### <a name="phone-call-verification"></a>電話驗證

在 SSPR 或 Azure AD Multi-Factor Authentication 期間進行通話驗證時，系統會對使用者註冊的電話號碼進行自動語音通話。 若要完成登入程式，系統會提示使用者在其鍵盤上按 #。

## <a name="office-phone-verification"></a>辦公室電話驗證

在 SSPR 或 Azure AD Multi-Factor Authentication 期間進行通話驗證時，系統會對使用者註冊的電話號碼進行自動語音通話。 若要完成登入程式，系統會提示使用者在其鍵盤上按 #。

## <a name="troubleshooting-phone-options"></a>電話選項的疑難排解

如果 Azure AD 的電話驗證有問題，請檢閱下列疑難排解步驟：

* 「在登入期間，您已達到對驗證呼叫的限制」或「您已達到文字驗證碼的限制」錯誤訊息
   * Microsoft 可能會在短時間內限制相同使用者所執行的重複驗證嘗試。 此限制不適用於 Microsoft Authenticator 或驗證碼。 如果您已達到這些限制，您可以使用驗證器應用程式、驗證碼，或在幾分鐘內嘗試重新登入。
* 在單一裝置上封鎖了呼叫者識別碼。
   * 檢閱裝置上設定的任何已封鎖號碼。
* 錯誤的電話號碼或不正確的國家/區域碼，或個人電話號碼與公司電話號碼混淆不清。
   * 針對使用者物件和已設定的驗證方法進行疑難排解。 請確定已註冊正確的電話號碼。
* 輸入了錯誤的 PIN。
   * 確認使用者已使用正確的 PIN 來註冊其帳戶 (MFA Server 使用者僅) 。
* 電話轉接到語音信箱。
   * 確保使用者電話已開機，且該服務已可在其區域中使用，或使用替代方法。
* 使用者遭到封鎖
   * 讓 Azure AD 系統管理員在 Azure 入口網站中將使用者解除封鎖。
* 未在裝置上訂閱 SMS。
   * 讓使用者變更方法，或在裝置上啟用 SMS。
* 不完善的電信業者，例如未偵測到任何電話輸入、遺漏 DTMF 音調問題、在多個裝置上封鎖了來電者識別碼，或封鎖了多個裝置的 SMS。
   * Microsoft 會使用多家電信業者來發送電話和 SMS 簡訊，以進行驗證。 如果您看到上述任何問題，請讓使用者在 5 分鐘內嘗試使用此方法至少五次，並且在聯絡 Microsoft 支援服務時提供該使用者的資訊。

## <a name="next-steps"></a>後續步驟

若要開始使用，請參閱 [自助式密碼重設的教學課程 (SSPR) ][tutorial-sspr] 和 [Azure AD Multi-Factor Authentication][tutorial-azure-mfa]。

若要深入了解 SSPR 概念，請參閱 [Azure AD 自助式密碼重設的運作方式][concept-sspr]。

若要深入瞭解 MFA 概念，請參閱 [Azure AD Multi-Factor Authentication 的運作方式][concept-mfa]。

深入瞭解如何使用 [Microsoft Graph REST API Beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true)來設定驗證方法。

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md

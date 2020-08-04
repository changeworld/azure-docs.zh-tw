---
title: 驗證方法和功能 - Azure Active Directory
description: 了解 Azure Active Directory 中可用的各種驗證方法和功能，協助改善和保護登入事件
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: 05283c02c2e5f95e22beb6fbeaad7a99e42ee6aa
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87540830"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Azure Active Directory 中有哪些可用的驗證方法？

在 Azure Active Directory (Azure AD) 中的帳戶登入體驗中，使用者有不同的方式可自行驗證。 使用者名稱和密碼是使用者過去提供認證的最常見方式。 在 Azure AD 的新式驗證和安全性功能中，可以其他驗證方法來補充或取代基本密碼。

Azure AD 中的使用者可以選擇使用下列其中一種驗證方法進行驗證：

* 傳統使用者名稱和密碼
* Microsoft Authenticator 應用程式無密碼登入
* OATH 硬體權杖或 FIDO2 安全性金鑰
* SMS 型無密碼登入

Azure AD 中有許多帳戶已啟用自助式密碼重設 (SSPR) 和 Azure Multi-Factor Authentication。 這些功能包括其他驗證方法，例如電話或安全性問題。 建議您要求使用者註冊多種驗證方法。 使用者若無法使用其中一種方法，即可選擇使用其他方法進行驗證。

下表概述主要或次要驗證有哪些方法可供使用：

| 方法 | 主要驗證 | 次要驗證 |
| --- | --- | --- |
| [密碼](#password) | 是 | |
| [Microsoft Authenticator 應用程式](#microsoft-authenticator-app) | 是 (預覽) | MFA 和 SSPR |
| [FIDO2 安全性金鑰 (預覽)](#fido2-security-keys) | 是 | 僅限 MFA |
| [OATH 軟體權杖](#oath-software-tokens) | 否 | MFA |
| [OATH 硬體權杖 (預覽)](#oath-hardware-tokens-preview) | 是 | MFA |
| [SMS](#phone-options) | 是 (預覽) | MFA 和 SSPR |
| [語音通話](#phone-options) | 否 | MFA 和 SSPR |
| [安全性問題](#security-questions) | 否 | 僅限 SSPR |
| [電子郵件地址](#email-address) | 否 | 僅限 SSPR |
| [應用程式密碼](#app-passwords) | 否 | 只有在某些情況下的 MFA |

本文概述 Azure AD 中各種可用的驗證方法，以及任何特定的限制。

![在 [登入] 畫面使用中的驗證方法](media/concept-authentication-methods/overview-login.png)

## <a name="password"></a>密碼

Azure AD 密碼通常是其中一種主要驗證方法。 您無法停用密碼驗證方法。

當使用者未使用其密碼進行簽署時，即使您使用驗證方法 (例如 [SMS 型登入](howto-authentication-sms-signin.md))，密碼仍會保留為可用的驗證方法。

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator 應用程式

Authenticator 應用程式可為您的 Azure AD 公司或學校帳戶或 Microsoft 帳戶提供額外一層安全性，而且適用於 [Android](https://go.microsoft.com/fwlink/?linkid=866594)、[iOS](https://go.microsoft.com/fwlink/?linkid=866594) 和 [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)。 使用 Microsoft Authenticator 應用程式，使用者可以在登入期間以無密碼方式進行驗證，或在自助式密碼重設 (SSPR) 或 Azure 多重要素驗證事件期間作為額外的驗證選項。

使用者可透過行動應用程式收到通知，以供進行核准或拒絕，或使用 Authenticator 應用程式來產生可在登入介面中輸入的 OATH 驗證碼。 如果您同時啟用通知和驗證碼，則註冊 Authenticator 應用程式的使用者可使用任一種方法來驗證其身分識別。

若要在登入提示字元中使用 Authenticator 應用程式，而不是使用者名稱和密碼組合，請參閱[使用 Microsoft Authenticator 應用程式 (預覽) 啟用無密碼登入](howto-authentication-passwordless-phone.md)。

> [!NOTE]
> 使用者在啟用 SSPR 時，沒有註冊其行動應用程式的選項。 相反地，使用者可以在 [https://aka.ms/mfasetup](https://aka.ms/mfasetup)，或在 [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) 的合併安全性資訊註冊中，註冊其行動應用程式。

### <a name="notification-through-mobile-app"></a>行動應用程式的通知

Authenticator 應用程式可協助防止未經授權即存取帳戶，並藉由推播通知到您的手機或平板電腦來停止詐騙交易。 使用者需檢視通知，如果合法，則選取 [驗證]。 否則，他們可以選取 [拒絕]。

![範例網頁瀏覽器提示的螢幕擷取畫面，其中顯示用以完成登入程序的 Authenticator 應用程式通知](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

> [!NOTE]
> 如果您的組織有員工參與或前往中國，透過 Android 裝置上的行動裝置*應用程式*方法的通知將無法在該國家/地區運作，因為 Google play 服務（包括推播通知）會在區域中遭到封鎖。 不過，iOS 通知會正常執行。 針對 Android 裝置，應讓這些使用者使用替代的驗證方法。

### <a name="verification-code-from-mobile-app"></a>行動應用程式傳回的驗證碼

Authenticator 應用程式可以作為軟體權杖來產生 OATH 驗證碼。 輸入您的使用者名稱和密碼之後，請在登入介面中輸入 Authenticator 應用程式所提供的驗證碼。 驗證碼提供第二種形式的驗證。

使用者可設定最多 5 個 OATH 硬體權杖或驗證器應用程式 (例如 Microsoft Authenticator 應用程式) 的組合，以在任何時間點使用。

> [!WARNING]
> 當重設只需要一種方法時，若要確保自助式密碼重設的最高層級安全性，驗證程式碼是使用者唯一可用的選項。
>
> 需要兩種方法時，除了任何其他已啟用的方法之外，使用者將可使用通知或驗證碼進行重設。

## <a name="fido2-security-keys"></a>FIDO2 安全性金鑰

FIDO (Fast IDentity Online) Alliance 有助於提升開放式驗證標準，並以一種驗證形式減少密碼的使用者。 FIDO2 是併入 Web 驗證 (WebAuthn) 標準的最新標準。

若要在登入提示字元中使用 FIDO2 安全性金鑰 (而不是使用者名稱和密碼組合)，請參閱[啟用無密碼 FIDO2 安全性金鑰登入 (預覽)](howto-authentication-passwordless-security-key.md)。

使用者可以註冊，然後在登入介面選取 FIDO2 安全性金鑰，昨為其主要的驗證方法。 這些 FIDO2 的安全性金鑰通常是 USB 裝置，但也可使用藍牙或 NFC。 使用可處理驗證的硬體裝置時，由於沒有可公開或猜到的密碼，因而提高帳戶的安全性。

Azure AD 中的 FIDO2 安全性金鑰目前為預覽狀態。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="oath-tokens"></a>OATH 權杖

OATH TOTP (時間型單次密碼) 是一項開放標準，可指定單次密碼 (OTP) 程式碼的產生方式。 OATH TOTP 可使用軟體或硬體來實作，以產生程式碼。 Azure AD 不支援 OATH HOTP，這是不同的程式碼產生標準。

### <a name="oath-software-tokens"></a>OATH 軟體權杖

軟體 OATH 權杖通常是應用程式，例如 Microsoft Authenticator 應用程式和其他驗證器應用程式。 Azure AD 會產生秘密金鑰或種子，其會輸入至應用程式並用來產生每個 OTP。

在準備進行推播通知時，Authenticator 應用程式會自動產生代碼，讓使用者即使在其裝置沒有連線能力的情況下也有備份。 也可以使用利用 OATH TOTP 來產生代碼的第三方應用程式。

有些 OATH TOTP 硬體權杖可程式化，這表示其不會隨附預先編寫的秘密金鑰或種子。 您可使用從軟體權杖安裝流程取得的秘密金鑰或種子來設定這些可程式化的硬體權杖。 客戶可以向他們所選的廠商購買這些權杖，並在其廠商的設定程序中使用秘密金鑰或種子。

### <a name="oath-hardware-tokens-preview"></a>OATH 硬體權杖 (預覽)

Azure AD 支援使用 OATH-TOTP SHA-1 權杖，其每隔 30 或 60 秒重新整理一次程式碼。 客戶可以向他們所選的廠商購買這些權杖。

OATH TOTP 硬體權杖通常會隨附在權杖中預先編寫的秘密金鑰或種子。 如下列步驟所述，這些金鑰必須輸入到 Azure AD 中。 祕密金鑰限制為 128 個字元，可能會與所有權杖不相容。 秘密金鑰只能包含字元 *a-z* 或 *A-Z* 和數字 *1-7*，而且必須以 *Base32* 編碼。

在軟體權杖設定流程中也可使用 Azure AD 來設定可重新植入的可程式化 OATH TOTP 硬體權杖。

OATH 硬體權杖已作為公開預覽的一部分支援。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

![將 OATH 權杖上傳到 MFA OATH 權杖刀鋒視窗](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

取得權杖後，必須使用逗號分隔值 (CSV) 檔案格式加以上傳，包括 UPN、序號、祕密金鑰、時間間隔、製造商和模型，如下列範例所示：

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> 請確定您在 CSV 檔案中包含標頭資料列。

將格式正確設為 CSV 檔案後，系統管理員可接著登入 Azure 入口網站，瀏覽至 [Azure Active Directory] > [安全性] > [MFA] > [OATH 權杖]，並上傳所產生的 CSV 檔案。

視 CSV 檔案的大小而定，可能需要數分鐘的時間來處理。 選取 [重新整理] 按鈕來取得目前的狀態。 如果檔案中有任何錯誤，您可下載 CSV 檔案，其中列出您需要解決的任何錯誤。 所下載 CSV 檔案中的欄位名稱與上傳的版本不同。

一旦解決任何錯誤後，系統管理員可接著針對權杖選取 [啟動] 並輸入權杖上顯示的 OTP，以啟動每個金鑰。

使用者可設定最多 5 個 OATH 硬體權杖或驗證器應用程式 (例如 Microsoft Authenticator 應用程式) 的組合，以在任何時間點使用。

## <a name="phone-options"></a>電話選項

若要使用簡訊進行直接驗證，您可以[為使用者設定和啟用 SMS 型驗證 (預覽)](howto-authentication-sms-signin.md)。 SMS 型登入很適合第一線工作者使用。 使用 SMS 型登入，使用者不需要知道使用者名稱和密碼，即可存取應用程式和服務。 使用者會改為輸入其已註冊的行動電話號碼、接收包含驗證碼的簡訊，然後在登入介面中輸入驗證碼。

使用者也可使用行動電話或辦公室電話作為 Azure Multi-Factor Authentication 或自助式密碼重設 (SSPR) 期間所使用的次要驗證形式，以此驗證自己。

為了正確運作，電話號碼的格式必須是：+國碼 電話號碼，例如 +1 4251234567。

> [!NOTE]
> 國碼/區域碼和電話號碼之間需要空格。
>
> 密碼重設不支援電話分機。 即使採用 +1 4251234567X12345 格式，撥號之前都會移除分機號碼。

### <a name="mobile-phone-verification"></a>行動電話驗證

針對 Azure Multi-Factor Authentication 或 SSPR，使用者可選擇接收含驗證碼的簡訊以在登入介面中輸入，或接到電話並提示他們輸入已定義的 pin 碼。

如果使用者不想在目錄中顯示其行動電話號碼，但想要用於密碼重設，系統管理員便不應將電話號碼填入目錄。 使用者反而應該透過合併安全性資訊註冊 (位於 [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo))，填入其**驗證電話**屬性。 管理員可以在使用者的設定檔中看到此資訊，但該資訊不會發佈在其他地方。

![Azure 入口網站的螢幕擷取畫面，其中顯示已填入電話號碼的驗證方法](media/concept-authentication-methods/user-authentication-methods.png)

Microsoft 不保證相同號碼會提供一致的 SMS 或語音型 Azure Multi-Factor Authentication 即時交付。 為了使用者的利益，我們可能會在調整路由來改善 SMS 傳遞能力時，隨時新增或移除簡短代碼。 Microsoft 不支援美國和加拿大以外之國家/區域的簡短代碼。

#### <a name="text-message-verification"></a>簡訊驗證

在 SSPR 或 Azure Multi-Factor Authentication 使用簡訊驗證，SMS 會傳送至包含驗證碼的行動電話號碼。 為了完成登入程序，會在登入介面中輸入所提供的驗證碼。

#### <a name="phone-call-verification"></a>電話驗證

在 SSPR 或 Azure Multi-Factor Authentication 期間使用電話驗證，系統會對使用者註冊的電話號碼進行自動語音通話。 為了完成登入程序，系統會提示使用者輸入其 pin 碼，接著輸入其鍵台上的 #。

### <a name="office-phone-verification"></a>辦公室電話驗證

辦公室電話屬性是由 Azure AD 系統管理員所管理，不能由使用者自行註冊。

在 SSPR 或 Azure Multi-Factor Authentication 期間使用電話驗證，系統會對使用者註冊的電話號碼進行自動語音通話。 為了完成登入程序，系統會提示使用者輸入其 pin 碼，接著輸入其鍵台上的 #。

### <a name="troubleshooting-phone-options"></a>電話選項的疑難排解

如果 Azure AD 的電話驗證有問題，請檢閱下列疑難排解步驟：

* 在單一裝置上封鎖了呼叫者識別碼。
   * 檢閱裝置上設定的任何已封鎖號碼。
* 錯誤的電話號碼或不正確的國家/區域碼，或個人電話號碼與公司電話號碼混淆不清。
   * 針對使用者物件和已設定的驗證方法進行疑難排解。 請確定已註冊正確的電話號碼。
* 輸入了錯誤的 PIN。
   * 確認使用者已使用正確的 PIN 來註冊其帳戶。
* 電話轉接到語音信箱。
   * 確保使用者電話已開機，且該服務已可在其區域中使用，或使用替代方法。
* 使用者遭到封鎖
   * 讓 Azure AD 系統管理員在 Azure 入口網站中將使用者解除封鎖。
* 未在裝置上訂閱 SMS。
   * 讓使用者變更方法，或在裝置上啟用 SMS。
* 不完善的電信業者，例如未偵測到任何電話輸入、遺漏 DTMF 音調問題、在多個裝置上封鎖了來電者識別碼，或封鎖了多個裝置的 SMS。
   * Microsoft 會使用多家電信業者來發送電話和 SMS 簡訊，以進行驗證。 如果您看到上述任何問題，請讓使用者在 5 分鐘內嘗試使用此方法至少五次，並且在聯絡 Microsoft 支援服務時提供該使用者的資訊。

## <a name="security-questions"></a>安全性問題

在登入事件期間，安全性問題不會作為驗證方法。 您反而可在自助式密碼重設 (SSPR) 程序期間，使用安全性問題來確認您的身分。 系統管理員帳戶無法使用安全性問題作為搭配 SSPR 運作的驗證方法。

當使用者註冊 SSPR 時，系統會提示他們選擇要使用的驗證方法。 如果選擇使用安全性問題，他們會從一組提示問題中挑選，然後提供自己的答案。

![Azure 入口網站的螢幕擷取畫面，其中顯示安全性問題的驗證方法和選項](media/concept-authentication-methods/security-questions-authentication-method.png)

> [!NOTE]
> 安全性問題會私密且安全地儲存在目錄中的使用者物件上，只能在註冊期間由使用者回答。 系統管理員無法讀取或修改使用者問題或答案。

安全性問題可能會比其他方法更不安全，因為有些人可能會知道其他使用者問題的答案。 如果您使用安全性問題搭配 SSPR，建議結合另一個方法共同使用。 系統會提示使用者使用 Microsoft Authenticator 應用程式或電話驗證，在 SSPR 過程中驗證其身分識別，且只有在他們沒有電話或已註冊的裝置時，才選擇安全性問題。

### <a name="predefined-questions"></a>預先定義的問題

下列預先定義的安全性問題可作為搭配 SSPR 使用的驗證方法。 所有安全性問題會依據使用者的瀏覽器地區設定，翻譯並當地語系化成完整的 O365 語言集：

* 您在哪個城市遇見第一個配偶/伴侶？
* 您的父母在哪個城市相遇？
* 您最親近的手足住在哪個城市？
* 您的父親在哪個城市出生？
* 您的第一份工作是在哪個城市？
* 您的母親在哪個城市出生？
* 您在哪個城市渡過 2000 年的新年？
* 您最喜愛的高中老師姓什麼？
* 您已申請但未就讀的大專名稱為何？
* 您舉辦第一次婚宴的地點名稱為何？
* 您父親的中間名是什麼？
* 您最愛的食物是什麼？
* 您外婆的姓名為何？
* 您母親的中間名是什麼？
* 您最年長手足的生日月份和年度為何？ (例如，1985 年 11 月)
* 您最年長手足的中間名是什麼？
* 您祖父的姓名為何？
* 您最年幼手足的中間名稱是什麼？
* 您六年級時就讀哪間學校？
* 您童年最要好朋友的姓名為何？
* 您第一個密友的姓名為何？
* 您最喜愛的小學老師姓什麼？
* 第一輛汽車或機車的製造商和型號為何？
* 您就讀的第一所學校名稱為何？
* 您出生的醫院名稱為何？
* 您兒時第一個家的街道名稱為何？
* 您的童年英雄名稱為何？
* 您最喜愛的娃娃名稱為何？
* 您第一隻寵物的名稱為何？
* 您童年時期的綽號是什麼？
* 您中學最喜愛的運動是什麼？
* 您的第一份工作是什麼？
* 您兒時電話號碼的末四碼是什麼？
* 在您年輕時，您長大想做什麼？
* 您遇過最有名的人物是誰？

### <a name="custom-security-questions"></a>自訂安全性問題

如需更多彈性，您可定義自己的自訂安全性問題。 自訂安全性問題的長度上限為 200 個字元。

自訂安全性問題不會像預設安全性問題一樣自動當地語系化。 所有自訂問題會以您在系統管理使用者介面中輸入它們的語言顯示，即使使用者的瀏覽器地區設定不同。 如果您需要已當地語系化的問題，請使用預先定義的問題。

### <a name="security-question-requirements"></a>安全性問題需求

預設和自訂安全性問題適用下列需求和限制：

* 答案字元限制下限為三個字元。
* 答案字元限制上限為 40 個字元。
* 使用者不能回答相同的問題一次以上。
* 使用者不能對一個以上的問題提供相同的答案。
* 可以使用任何字元集來定義問題和答案 (包括 Unicode 字元)。
* 定義的問題數目必須大於或等於註冊所需的問題數目。

## <a name="email-address"></a>電子郵件地址

電子郵件地址不能作為直接驗證方法。 電子郵件地址只可作為自助式密碼重設 (SSPR) 的驗證選項。 若在 SSPR 期間選取電子郵件地址，系統會將電子郵件傳送給使用者，以完成驗證程序。

在 SSPR 註冊期間，使用者會提供要使用的電子郵件地址。 建議使用與公司帳戶不同的電子郵件帳戶，以確保他們可以在 SSPR 期間存取帳戶。

## <a name="app-passwords"></a>應用程式密碼

某些較舊的非瀏覽器應用程式不了解驗證程序中的暫停或中斷。 如果使用者已啟用多重要素驗證，並嘗試使用其中一個較舊的非瀏覽器應用程式，他們通常無法成功進行驗證。 應用程式密碼可讓使用者繼續以較舊的非瀏覽器應用程式成功驗證，而不會中斷。

根據預設，使用者無法建立應用程式密碼。 如果您需要允許使用者建立應用程式密碼，請針對使用者的 Azure Multi-Factor Authentication 屬性，選取 [服務設定] 之下的 [允許使用者建立應用程式密碼以登入非瀏覽器應用程式]。

![Azure 入口網站的螢幕擷取畫面，其中顯示多重要素驗證的服務設定，以允許使用者建立應用程式密碼](media/concept-authentication-methods/app-password-authentication-method.png)

如果您使用條件式存取原則來強制執行 Azure Multi-Factor Authentication (而不是透過每個使用者的 MFA)，則無法建立應用程式密碼。 使用條件式存取原則來控制存取權的新式應用程式不需要應用程式密碼。

如果貴組織針對單一登入 (SSO) 與 Azure AD 同盟，且您使用 Azure Multi-Factor Authentication，請考慮下列事項：

* 應用程式密碼由 Azure AD 驗證，因此會略過同盟。 唯有在設定應用程式密碼時才會使用同盟。 對於同盟 (SSO) 使用者，密碼會儲存在組織識別碼中。 如果使用者離開公司，這些資訊必須使用 DirSync 流向組織識別碼。 帳戶停用或刪除事件最多可能需要三個小時才能完成同步處理，而導致 Azure AD 中停用/刪除應用程式密碼時延遲。
* 應用程式密碼不會遵守內部部署用戶端存取控制設定。
* 應用程式密碼不適用內部部署驗證記錄或稽核功能。
* 某些進階架構設計在使用多重要素驗證時，可能需要搭配使用組織使用者名稱和密碼及應用程式密碼，視其驗證的位置而定。
    * 對於根據內部部署基礎結構進行驗證的用戶端，您可以使用組織使用者名稱和密碼。
    * 對於根據 Azure AD 進行驗證的用戶端，您需要使用應用程式密碼。

## <a name="next-steps"></a>後續步驟

若要開始使用，請參閱[自助式密碼重設 (SSPR) 的教學課程][tutorial-sspr]和 [Azure Multi-Factor Authentication][tutorial-azure-mfa]。

若要深入了解 SSPR 概念，請參閱 [Azure AD 自助式密碼重設的運作方式][concept-sspr]。

若要深入了解 MFA 概念，請參閱 [Azure Multi-Factor Authentication 的運作方式][concept-mfa]。

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md

---
title: 驗證方法和功能-Azure Active Directory
description: 瞭解 Azure Active Directory 中可用的不同驗證方法和功能，以協助改善和保護登入事件
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: 3947bf0dcad598bf52a742c790a2f99538d6facb
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83116364"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Azure Active Directory 中可用的驗證和驗證方法為何？

在 Azure Active Directory （Azure AD）中的帳戶登入體驗中，有不同的方式可供使用者自行驗證。 使用者名稱和密碼是使用者過去提供認證的最常見方式。 在 Azure AD 的新式驗證和安全性功能中，可以將基本密碼補充或取代為其他驗證方法。

Azure AD 中的使用者可以選擇使用下列其中一種驗證方法來進行驗證：

* 傳統使用者名稱和密碼
* Microsoft Authenticator 應用程式無密碼登入
* OATH 硬體權杖或 FIDO2 安全性金鑰
* 以 SMS 為基礎的無密碼登入

Azure AD 中的許多帳戶都已啟用自助式密碼重設（SSPR）或 Azure 多因素驗證。 這些功能包括其他驗證方法，例如通話或安全性問題。 建議您要求使用者註冊多個驗證方法。 當使用者無法使用其中一種方法時，他們可以選擇使用其他方法進行驗證。

下表列出適用于不同案例的驗證或驗證方法：

| 方法 | 在登入時使用 | 在驗證期間使用 |
| --- | --- | --- |
| [密碼](#password) | 是 | MFA 和 SSPR |
| [Microsoft Authenticator 應用程式](#microsoft-authenticator-app) | 是 (預覽) | MFA 和 SSPR |
| [FIDO2 安全性金鑰（預覽）](#fido2-security-keys) | 是 | 僅限 MFA |
| [OATH 硬體權杖（預覽）](#oath-hardware-tokens) | 是 | SSPR 和 MFA |
| [sms](#phone-options) | 是 (預覽) | MFA 和 SSPR |
| [語音通話](#phone-options) | 否 | MFA 和 SSPR |
| [安全性問題](#security-questions) | 否 | 僅限 SSPR |
| [電子郵件地址](#email-address) | 否 | 僅限 SSPR |
| [應用程式密碼](#app-passwords) | 否 | 只有在某些情況下的 MFA |

本文概述 Azure AD 中可用的各種驗證和驗證方法，以及任何特定的限制或限制。

![在 [登入] 畫面使用中的驗證方法](media/concept-authentication-methods/overview-login.png)

## <a name="password"></a>密碼

Azure AD 密碼通常是其中一個主要驗證方法。 您無法停用密碼驗證方法。

即使當使用者未使用密碼來簽署時，如果您使用驗證方法（例如[SMS 登入](howto-authentication-sms-signin.md)），密碼仍會保留為可用的驗證方法。

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator 應用程式

驗證器應用程式為您的 Azure AD 工作或學校帳戶或 Microsoft 帳戶提供了額外的安全性等級，適用于[Android](https://go.microsoft.com/fwlink/?linkid=866594)、 [iOS](https://go.microsoft.com/fwlink/?linkid=866594)及[Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)。 有了 Microsoft Authenticator 應用程式，使用者可以在登入期間以無密碼的方式進行驗證，或在自助式密碼重設（SSPR）或 Azure 多因素驗證事件期間做為額外的驗證選項。

使用者可以透過行動應用程式收到通知，供他們核准或拒絕，或使用驗證器應用程式來產生可在登入介面中輸入的 OATH 驗證碼。 如果您同時啟用通知和驗證碼，則註冊驗證器應用程式的使用者可以使用任一種方法來驗證其身分識別。

若要在登入提示字元中使用驗證器應用程式，而不是使用者名稱和密碼組合，請參閱使用[Microsoft Authenticator 應用程式（預覽）啟用無密碼登入](howto-authentication-passwordless-phone.md)。

> [!NOTE]
> 使用者在啟用 SSPR 時，不會有註冊其行動應用程式的選項。 相反地，使用者可以註冊其行動裝置應用程式， [https://aka.ms/mfasetup](https://aka.ms/mfasetup) 或做為結合的安全性資訊註冊的一部分 [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) 。

### <a name="notification-through-mobile-app"></a>行動應用程式的通知

驗證器應用程式可透過將通知推送至 smartphone 或平板電腦，協助防止未經授權的帳戶存取，並停止詐騙交易。 使用者會看到通知，如果合法，請選取 [**驗證**]。 否則，他們可以選取 [**拒絕**]。

![完成登入程式的驗證器代理程式更新範例的螢幕擷取畫面](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

> [!NOTE]
> 如果您的組織有員工從事或前往中國，則在 Android 裝置上透過行動裝置*應用程式*方法的通知無法在該國家/地區運作。 這些使用者應該可以使用替代的驗證方法。

### <a name="verification-code-from-mobile-app"></a>行動應用程式傳回的驗證碼

驗證器應用程式可以用來作為軟體權杖，以產生 OATH 驗證碼。 輸入您的使用者名稱和密碼之後，您可以在登入介面中輸入驗證器應用程式所提供的代碼。 驗證碼提供第二種形式的驗證。

使用者最多可以有五個 OATH 硬體權杖或驗證器應用程式（例如 Microsoft Authenticator 應用程式）的組合，並設定為在任何時間使用。

> [!WARNING]
> 若要確保在重設時只需要一個方法時，自助式密碼重設的最高安全性層級，驗證碼是使用者唯一可用的選項。
>
> 需要兩種方法時，除了其他任何已啟用的方法之外，使用者也可以使用通知或驗證碼進行重設。

## <a name="fido2-security-keys"></a>FIDO2 安全性金鑰

FIDO （快速身分識別線上）聯盟有助於提升開放式驗證標準，並以一種驗證形式減少密碼的使用者。 FIDO2 是結合 web 驗證（WebAuthn）標準的最新標準。

若要在登入提示字元中使用 FIDO2 安全性金鑰，而不是使用者名稱和密碼組合，請參閱[啟用無密碼 FIDO2 安全性金鑰登入（預覽）](howto-authentication-passwordless-security-key.md)。

使用者可以在登入介面上註冊並選取 FIDO2 安全性金鑰，做為其主要的驗證方法。 這些 FIDO2 的安全性金鑰通常是 USB 裝置，但也可以使用藍牙或 NFC。 使用處理驗證的硬體裝置時，會增加帳戶的安全性，因為沒有可公開或猜到的密碼。

Azure AD 中的 FIDO2 安全性金鑰目前為預覽狀態。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="oath-hardware-tokens"></a>OATH 硬體權杖

OATH 是一項開放標準，可指定單次密碼 (OTP) 程式碼的產生方式。 Azure AD 支援使用30秒或 60-第二個不同的 OATH TOTP SHA-1 權杖。 客戶可以從他們選擇的廠商購買這些權杖。

秘密金鑰限制為128個字元，但可能與所有權杖不相容。 秘密金鑰只能*包含字元 a-z*或 a-z 和數位*1-7*，*而且必須*以*Base32*編碼。

Azure AD 中的 OATH 硬體權杖目前為預覽狀態。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

![將 OATH 權杖上傳至 [MFA OATH 權杖] 視窗](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

取得權杖之後，必須以逗號分隔值（CSV）檔案格式上傳，包括 UPN、序號、秘密金鑰、時間間隔、製造商和型號，如下列範例所示：

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> 請確定您在 CSV 檔案中包含標頭資料列。

一旦正確格式化為 CSV 檔案之後，系統管理員就可以登入 Azure 入口網站，流覽至**Azure Active Directory**  >  **安全性**  >  **MFA**  >  **OATH 權杖**，並上傳產生的 CSV 檔案。

視 CSV 檔案的大小而定，可能需要數分鐘的時間來處理。 選取 [重新整理 **] 按鈕以**取得目前的狀態。 如果檔案中有任何錯誤，您可以下載 CSV 檔案，其中列出您要解決的任何錯誤。 下載的 CSV 檔案中的功能變數名稱與上傳的版本不同。

解決任何錯誤之後，系統管理員就可以針對權杖選取 [**啟用**]，並輸入權杖上顯示的 OTP，藉此啟動每個金鑰。

使用者最多可以有五個 OATH 硬體權杖或驗證器應用程式（例如 Microsoft Authenticator 應用程式）的組合，並設定為在任何時間使用。

## <a name="phone-options"></a>電話選項

若要使用文字訊息進行直接驗證，您可以[設定並讓使用者進行 SMS 型驗證（預覽）](howto-authentication-sms-signin.md)。 以 SMS 為基礎的登入適用于前端背景工作角色。 使用 SMS 登入，使用者就不需要知道使用者名稱和密碼，就能存取應用程式和服務。 使用者會改為輸入其已註冊的行動電話號碼、接收包含驗證碼的文字訊息，然後在登入介面中輸入。

使用者也可以使用行動電話或辦公室電話驗證自己，做為 Azure 多因素驗證或自助式密碼重設（SSPR）期間所使用的次要驗證形式。

若要正常運作，電話號碼的格式必須是 *+ CountryCode PhoneNumber*，例如 *+ 1 4251234567*。

> [!NOTE]
> 國碼 (地區碼) 和電話號碼之間需要空格。
>
> 密碼重設不支援電話分機。 即使在 *+ 1 4251234567X12345*格式中，也會在呼叫之前移除延伸模組。

### <a name="mobile-phone-verification"></a>行動電話驗證

針對 Azure 多重要素驗證或 SSPR，使用者可以選擇接收包含驗證碼的文字訊息，以在登入介面中輸入，或接收來電並輸入其定義的 pin 碼。

如果使用者不想要在目錄中看到其行動電話號碼，但想要使用它來重設密碼，系統管理員就不應在目錄中填入電話號碼。 相反地，使用者應該透過中的組合安全性資訊註冊來填入其**驗證電話**屬性 [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) 。 管理員可以在使用者的設定檔中看到此資訊，但該資訊不會發佈在其他地方。

![顯示已填入電話號碼之驗證方法的 Azure 入口網站螢幕擷取畫面](media/concept-authentication-methods/user-authentication-methods.png)

Microsoft 不保證以相同的號碼來進行一致的 SMS 或 voice 型 Azure 多因素驗證提示傳遞。 隨著我們的使用者的興趣，我們可以在進行路由調整以改善 SMS 傳遞能力時，隨時新增或移除簡短的代碼。 除了美國和加拿大以外的國家/地區，Microsoft 不支援簡短代碼。

#### <a name="text-message-verification"></a>文字訊息驗證

在 SSPR 或 Azure 多重要素驗證期間使用文字訊息驗證，會將 SMS 傳送至包含驗證碼的行動電話號碼。 為了完成登入程式，會在登入介面中輸入所提供的驗證碼。

#### <a name="phone-call-verification"></a>通話驗證

透過在 SSPR 或 Azure 多重要素驗證期間進行通話驗證，系統會對使用者註冊的電話號碼進行自動語音通話。 若要完成登入程式，系統會提示使用者輸入其 pin 碼，並在其小數位數中加上 #。

### <a name="office-phone-verification"></a>辦公室電話驗證

「辦公室電話」屬性是由 Azure AD 系統管理員所管理，而且不能由使用者自行註冊。

透過在 SSPR 或 Azure 多重要素驗證期間進行通話驗證，系統會對使用者註冊的電話號碼進行自動語音通話。 若要完成登入程式，系統會提示使用者輸入其 pin 碼，並在其小數位數中加上 #。

### <a name="troubleshooting-phone-options"></a>手機選項的疑難排解

如果 Azure AD 的電話驗證有問題，請參閱下列疑難排解步驟：

* 單一裝置上封鎖的呼叫者識別碼。
   * 檢查裝置上設定的任何封鎖數位。
* 錯誤的電話號碼或不正確的國家/地區代碼，或個人電話號碼與公司電話號碼之間的混淆。
   * 針對使用者物件和已設定的驗證方法進行疑難排解。 請確定已註冊正確的電話號碼。
* 輸入錯誤的 PIN。
   * 確認使用者已使用正確的 PIN 來註冊其帳戶。
* 呼叫轉送到語音信箱。
   * 請確定使用者已開啟電話，且該服務已在其區域中提供，或使用其他方法。
* 使用者遭到封鎖
   * 讓 Azure AD 系統管理員將 Azure 入口網站中的使用者解除封鎖。
* 未在裝置上訂閱 SMS。
   * 讓使用者變更方法，或在裝置上啟用 SMS。
* 錯誤的電信提供者，例如未偵測到任何電話輸入、遺失 DTMF 音調問題、在多個裝置上封鎖了呼叫者識別碼，或跨多個裝置封鎖了 SMS。
   * Microsoft 會使用多個電信提供者來路由通話和 SMS 訊息以進行驗證。 如果您看到上述任何問題，請讓使用者在5分鐘內嘗試使用此方法至少五次，並在聯繫 Microsoft 支援服務時，提供該使用者的資訊。

## <a name="security-questions"></a>安全性問題

在登入事件期間，安全性問題不會用來做為驗證方法。 相反地，您可以在自助式密碼重設（SSPR）程式期間使用安全性問題，以確認您的身分。 系統管理員帳戶無法使用安全性問題做為 SSPR 的驗證方法。

當使用者註冊 SSPR 時，系統會提示他們選擇要使用的驗證方法。 如果他們選擇使用安全性問題，他們會從一組問題中挑選提示，然後提供自己的答案。

![Azure 入口網站的螢幕擷取畫面，顯示安全性問題的驗證方法和選項](media/concept-authentication-methods/security-questions-authentication-method.png)

> [!NOTE]
> 安全性問題會私密且安全地儲存在目錄中的使用者物件上，只能在註冊期間由使用者回答。 系統管理員無法讀取或修改使用者的問題或解答。

安全性問題可能會比其他方法更不安全，因為有些人可能會知道其他使用者問題的答案。 如果您使用 SSPR 的安全性問題，建議您將它們與另一個方法搭配使用。 系統會提示使用者使用 Microsoft Authenticator 應用程式或電話驗證，在 SSPR 過程中驗證其身分識別，並只有在他們沒有電話或已註冊的裝置時，才選擇安全性問題。

### <a name="predefined-questions"></a>預先定義的問題

下列預先定義的安全性問題可做為使用 SSPR 的驗證方法。 所有這些安全性問題都會根據使用者的瀏覽器地區設定，轉譯並當地語系化成一組完整的 Office 365 語言：

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
* 您最年長手足的生日月份和年度為何？ （例如，1985年11月）
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

如需更多彈性，您可以定義自己的自訂安全性問題。 自訂安全性問題的長度上限為 200 個字元。

自訂安全性問題不會因為預設的安全性問題而自動當地語系化。 所有自訂問題都會以系統管理使用者介面中輸入的相同語言顯示，即使使用者的瀏覽器地區設定不同也一樣。 如果您需要已當地語系化的問題，請使用預先定義的問題。

### <a name="security-question-requirements"></a>安全性問題需求

針對預設和自訂安全性問題，適用下列需求和限制：

* 答案字元限制下限為三個字元。
* 答案字元限制上限為 40 個字元。
* 使用者不能回答相同的問題一次以上。
* 使用者不能對一個以上的問題提供相同的答案。
* 可以使用任何字元集來定義問題和答案 (包括 Unicode 字元)。
* 定義的問題數目必須大於或等於註冊所需的問題數目。

## <a name="email-address"></a>電子郵件地址

電子郵件地址不能當做直接驗證方法使用。 電子郵件地址只可作為自助式密碼重設（SSPR）的驗證選項。 在 SSPR 期間選取電子郵件地址時，系統會將電子郵件傳送給使用者，以完成驗證/驗證程式。

在註冊 SSPR 期間，使用者會提供要使用的電子郵件地址。 建議使用與公司帳戶不同的電子郵件帳戶，以確保他們可以在 SSPR 期間存取它。

## <a name="app-passwords"></a>應用程式密碼

某些較舊的非瀏覽器應用程式不了解在驗證程式中暫停或中斷。 如果使用者已啟用多重要素驗證，並嘗試使用其中一個較舊的非瀏覽器應用程式，他們通常無法成功進行驗證。 應用程式密碼可讓使用者繼續以較舊的非瀏覽器應用程式成功驗證，而不會中斷。

根據預設，使用者無法建立應用程式密碼。 如果您需要允許使用者建立應用程式密碼，請選取 [**允許使用者建立應用程式密碼以登入非瀏覽器應用程式** *]，以取得使用者*的 Azure 多因素驗證屬性。

![Azure 入口網站的螢幕擷取畫面，顯示多重要素驗證的服務設定，以允許使用者的應用程式密碼](media/concept-authentication-methods/app-password-authentication-method.png)

如果您使用條件式存取原則來強制執行 Azure 多重要素驗證，而不是透過每個使用者的 MFA，則無法建立應用程式密碼。 使用條件式存取原則來控制存取的現代化應用程式不需要應用程式密碼。

如果您的組織已同盟搭配 Azure AD 的單一登入（SSO），而且您使用 Azure 多重要素驗證，則適用下列考慮事項：

* 應用程式密碼是由 Azure AD 驗證，因此會略過同盟。 唯有在設定應用程式密碼時才會使用同盟。 對於同盟 (SSO) 使用者，密碼會儲存在組織識別碼中。 如果使用者離開公司，這些資訊必須使用 DirSync 流向組織識別碼。 帳戶停用或刪除事件最多可能需要三個小時才能同步處理，這會延遲 Azure AD 中的應用程式密碼停用/刪除。
* 應用程式密碼不會遵守內部部署用戶端存取控制設定。
* 應用程式密碼不提供內部部署驗證記錄或審核功能。
* 某些先進的架構設計可能需要在使用多重要素驗證時，使用組織使用者名稱和密碼和應用程式密碼的組合，視其驗證的位置而定。
    * 對於根據內部部署基礎結構進行驗證的用戶端，您可以使用組織使用者名稱和密碼。
    * 對於根據 Azure AD 進行驗證的用戶端，您需要使用應用程式密碼。

## <a name="next-steps"></a>後續步驟

若要開始使用，請參閱[自助式密碼重設 (SSPR) 的教學課程][tutorial-sspr]和 [Azure Multi-Factor Authentication][tutorial-azure-mfa]。

若要深入瞭解 SSPR 概念，請參閱[Azure AD 自助式密碼重設如何運作][concept-sspr]。

若要深入瞭解 MFA 概念，請參閱[Azure 多重要素驗證的運作方式][concept-mfa]。

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
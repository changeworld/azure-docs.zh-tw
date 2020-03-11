---
title: 驗證方法-Azure Active Directory
description: MFA 和 SSPR 的 Azure AD 中可用的驗證方法
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a82c69575e82a7cf397955f08c3f114e449ba6b
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "78968780"
---
# <a name="what-are-authentication-methods"></a>驗證方法有哪些？

身為系統管理員，選擇 Azure 多因素驗證和自助式密碼重設（SSPR）的驗證方法，建議您要求使用者註冊多個驗證方法。 當使用者無法使用驗證方法時，可以選擇使用其他方法進行驗證。

系統管理員可以定義在原則中可用於 SSPR 和 MFA 使用者的驗證方法。 某些驗證方法可能不適用於所有功能。 如需設定原則的詳細資訊，請參閱[如何成功推出自助式密碼重設](howto-sspr-deployment.md)和[規劃以雲端為基礎的 Azure 多因素驗證](howto-mfa-getstarted.md)文章

Microsoft 強烈建議系統管理員讓使用者可選取多於必要驗證方法數目下限，以免使用者無法存取其中一個。

|驗證方法|使用量|
| --- | --- |
| 密碼 | MFA 和 SSPR |
| 安全性問題 | 僅 SSPR |
| 電子郵件地址 | 僅 SSPR |
| Microsoft Authenticator 應用程式 | MFA 和 SSPR |
| OATH 硬體權杖 | MFA 和 SSPR 的公開預覽版 |
| sms | MFA 和 SSPR |
| 語音通話 | MFA 和 SSPR |
| 應用程式密碼 | 只有在某些情況下的 MFA |

![在 [登入] 畫面使用中的驗證方法](media/concept-authentication-methods/overview-login.png)

|     |
| --- |
| 適用于 MFA 和 SSPR 的 OATH 硬體權杖是 Azure Active Directory 的公開預覽功能。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="password"></a>密碼

您的 Azure AD 密碼會視為一種驗證方法。 它是一種**無法停用**的方法。

## <a name="security-questions"></a>安全性問題

安全性問題**只可在 Azure AD 自助式密碼重設中**用於非系統管理員帳戶。

如果您使用安全性問題，建議結合另一個方法共同使用。 安全性問題可能會比其他方法更不安全，因為有些人可能會知道其他使用者問題的答案。

> [!NOTE]
> 安全性問題會私密且安全地儲存在目錄中的使用者物件上，只能在註冊期間由使用者回答。 系統管理員無法讀取或修改使用者問題或答案。
>

### <a name="predefined-questions"></a>預先定義的問題

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
* 您最年長手足的生日月份和年度為何？ (例如 1985 年 11 月)
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

所有預先定義的安全性問題會依據使用者的瀏覽器地區設定，翻譯並當地語系化成完整的 O365 語言集。

### <a name="custom-security-questions"></a>自訂安全性問題

自訂安全性問題不會進行當地語系化。 所有自訂問題會以您在系統管理使用者介面中輸入它們的語言顯示，即使使用者的瀏覽器地區設定不同。 如果您需要已當地語系化的問題，請使用預先定義的問題。

自訂安全性問題的長度上限為 200 個字元。

### <a name="security-question-requirements"></a>安全性問題需求

* 答案字元限制下限為三個字元。
* 答案字元限制上限為 40 個字元。
* 使用者不能回答相同的問題一次以上。
* 使用者不能對一個以上的問題提供相同的答案。
* 可以使用任何字元集來定義問題和答案 (包括 Unicode 字元)。
* 定義的問題數目必須大於或等於註冊所需的問題數目。

## <a name="email-address"></a>電子郵件地址

電子郵件地址只可**在 Azure AD 自助式密碼重設**中使用。

Microsoft 建議使用不需要使用者的 Azure AD 密碼進行存取的電子郵件帳戶。

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator 應用程式

Microsoft Authenticator 應用程式可以為您的 Azure AD 公司或學校帳戶或 Microsoft 帳戶提供額外一層安全性。

Microsoft Authenticator 應用程式適用於 [Android](https://go.microsoft.com/fwlink/?linkid=866594)、[iOS](https://go.microsoft.com/fwlink/?linkid=866594) 和 [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)。

> [!NOTE]
> 使用者在註冊自助式密碼重設時，不可選擇註冊其行動應用程式。 相反地，使用者可以在 [https://aka.ms/mfasetup](https://aka.ms/mfasetup)，或在 [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) 的安全性資訊註冊預覽版中，註冊其行動應用程式。
>

### <a name="notification-through-mobile-app"></a>行動應用程式的通知

Microsoft Authenticator 應用程式可協助防止未經授權即存取帳戶，並藉由推播通知到您的手機或平板電腦來停止詐騙交易。 使用者需檢視通知，如果合法，則選取 [驗證]。 否則，他們可以選取 [拒絕]。

> [!WARNING]
> 對於自助式密碼重設，當重設只需要一個方法時，驗證程式碼是使用者可**確保最高層級安全性**的唯一可用選項。
>
> 需要兩種方法時，使用者除了任何其他已啟用的方法之外，將能夠使用通知**或**驗證碼。
>

如果您允許使用透過行動裝置應用程式的通知和來自行動裝置應用程式的驗證碼，使用通知註冊 Microsoft Authenticator 應用程式的使用者，就能使用通知和驗證碼來確認其身分識別。

> [!NOTE]
> 如果您的組織有員工從事或前往中國，則在**Android 裝置**上透過行動裝置**應用程式**方法的通知無法在該國家/地區運作。 其他方法應可供這些使用者使用。

### <a name="verification-code-from-mobile-app"></a>行動應用程式傳回的驗證碼

Microsoft Authenticator 應用程式或其他第三方應用程式可以作為軟體權杖來產生 OATH 驗證碼。 在輸入您的使用者名稱和密碼後，必須在登入畫面出現提示時輸入應用程式提供的驗證碼。 驗證碼提供第二種形式的驗證。

> [!WARNING]
> 對於自助式密碼重設，當重設只需要一個方法時，驗證程式碼是使用者可**確保最高層級安全性**的唯一可用選項。
>

使用者可能會有最多五個 OATH 硬體權杖或驗證器應用程式的組合，例如設定為在任何時間使用的 Microsoft Authenticator 應用程式。

## <a name="oath-hardware-tokens-public-preview"></a>OATH 硬體權杖 (公開預覽)

OATH 是一項開放標準，可指定單次密碼 (OTP) 程式碼的產生方式。 Azure AD 將會支援使用每 30 秒或 60 秒變換一次的 OATH-TOTP SHA-1 權杖。 客戶可以從他們選擇的廠商購買這些權杖。 秘密金鑰限制為128個字元，但可能與所有權杖不相容。 秘密金鑰只能*包含字元 a-z*或 a-z 和數位*1-7*，*而且必須*以 Base32 編碼。

![將 OATH 權杖上傳到 MFA OATH 權杖分頁](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

公開預覽中支援 OATH 硬體權杖。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

取得權杖之後，必須以逗號分隔值（CSV）檔案格式上傳，包括 UPN、序號、秘密金鑰、時間間隔、製造商和型號，如下列範例所示：

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> 請確定您在 CSV 檔案中包含標頭資料列。

一旦正確格式化為 CSV 檔案之後，系統管理員就可以登入 Azure 入口網站，流覽至**Azure Active Directory** > **安全性** > **MFA** > **OATH 權杖**，然後上傳產生的 CSV 檔案。

視 CSV 檔案的大小而定，可能需要數分鐘的時間來處理。 按一下 [重新整理] 按鈕來取得目前的狀態。 如果檔案中有任何錯誤，您可以選擇下載 CSV 檔案，當中會列出需要您解決的任何錯誤。 下載的 CSV 檔案中的功能變數名稱與上傳的版本不同。

一旦已解決任何錯誤後，系統管理員可以接著按一下 [啟動] 來啟動每個金鑰，以便啟動權杖及輸入權杖上顯示的 OTP。

使用者可能會有最多五個 OATH 硬體權杖或驗證器應用程式的組合，例如設定為在任何時間使用的 Microsoft Authenticator 應用程式。

## <a name="phone-options"></a>電話選項

### <a name="mobile-phone"></a>行動電話

有兩個選項可供使用行動電話的使用者使用。

如果使用者不想在目錄中顯示其行動電話號碼，但仍想要用於密碼重設，管理員便不應將該號碼填入目錄。 使用者應該透過**密碼重設註冊入口網站**，填妥其[驗證電話](https://aka.ms/ssprsetup)屬性。 管理員可以在使用者的設定檔中看到此資訊，但該資訊不會發佈在其他地方。

為了正確運作，電話號碼的格式必須是：+國碼 電話號碼，例如 +1 4255551234。

> [!NOTE]
> 國碼 (地區碼) 和電話號碼之間需要空格。
>
> 密碼重設不支援電話分機。 即使是 +1 4255551234X12345 格式，撥號之前都會移除分機號碼。

Microsoft 不保證相同號碼會提供一致的 SMS 或以語音為基礎的多重要素驗證即時交付。 為了使用者的利益，Microsoft 可能會在調整路由來改善 SMS 傳遞能力時，隨時新增或移除簡短程式碼。 除了美國和加拿大以外的國家/地區，Microsoft 不支援簡短代碼。

#### <a name="text-message"></a>簡訊

包含驗證碼的簡訊會傳送到行動電話號碼。 輸入登入介面中提供的驗證碼以繼續。

#### <a name="phone-call"></a>撥打電話

撥打自動語音電話給您所提供的電話號碼。 接聽電話並按電話鍵盤上的 # 進行驗證

> [!IMPORTANT]
> 自2019年3月起，Azure AD 租使用者的免費/試用版中，將無法使用通話選項進行 MFA 和 SSPR。 這項變更不會影響 SMS 訊息。 付費 Azure AD 租使用者中的使用者將可繼續使用通話。 這種變更只會影響 Azure AD 租使用者的免費/試用版。

### <a name="office-phone"></a>辦公室電話

撥打自動語音電話給您所提供的電話號碼。 接聽電話並按電話鍵盤上的 # 進行驗證。

為了正確運作，電話號碼的格式必須是：+國碼 電話號碼，例如 +1 4255551234。

辦公室電話屬性是由您的系統管理員管理。

> [!IMPORTANT]
> 自2019年3月起，Azure AD 租使用者的免費/試用版中，將無法使用通話選項進行 MFA 和 SSPR。 這項變更不會影響 SMS 訊息。 付費 Azure AD 租使用者中的使用者將可繼續使用通話。 這種變更只會影響 Azure AD 租使用者的免費/試用版。

> [!NOTE]
> 國碼 (地區碼) 和電話號碼之間需要空格。
>
> 密碼重設不支援電話分機。 即使是 +1 4255551234X12345 格式，撥號之前都會移除分機號碼。

### <a name="troubleshooting-phone-options"></a>疑難排解電話選項

使用電話號碼之驗證方法的相關常見問題：

* 單一裝置上封鎖的呼叫者識別碼
   * 裝置疑難排解
* 電話號碼錯誤、國家/地區代碼不正確、住家電話號碼與公司電話號碼
   * 針對使用者物件和已設定的驗證方法進行疑難排解。 請確定已註冊正確的電話號碼。
* 輸入的 PIN 錯誤
   * 確認使用者已使用在 Azure MFA Server 中註冊的正確 PIN。
* 呼叫轉送到語音信箱
   * 請確定使用者已開啟電話，且該服務已在其區域中提供，或使用其他方法。
* 使用者遭到封鎖
   * 讓系統管理員在 Azure 入口網站中解除封鎖使用者。
* 未在裝置上訂閱 SMS
   * 讓使用者變更方法，或在裝置上啟用 SMS。
* 錯誤的電信提供者（未偵測到電話輸入、遺失 DTMF 音調問題、多個裝置上封鎖的呼叫者識別碼，或跨多個裝置封鎖的 SMS）
   * Microsoft 會使用多個電信提供者來路由通話和 SMS 訊息以進行驗證。 如果您看到上述任何問題，則使用者在5分鐘內至少會嘗試使用此方法5次，並在聯繫 Microsoft 支援服務時提供該使用者的資訊。

## <a name="app-passwords"></a>應用程式密碼

某些非瀏覽器應用程式不支援多重要素驗證，如果使用者已啟用多重要素驗證，並嘗試使用非瀏覽器應用程式，則無法進行驗證。 應用程式密碼可讓使用者繼續進行驗證

如果您透過條件式存取原則強制執行 Multi-Factor Authentication (而不是透過每個使用者的 MFA)，則無法建立應用程式密碼。 使用條件式存取原則來控制存取權的應用程式不需要應用程式密碼。

如果您的組織使用 SSO 與 Azure AD 同盟，而且您想要使用 Azure MFA 時，請注意下列細節：

* 應用程式密碼由 Azure AD 驗證，因此會略過同盟。 唯有在設定應用程式密碼時才會使用同盟。 對於同盟 (SSO) 使用者，密碼會儲存在組織識別碼中。 如果使用者離開公司，這些資訊必須使用 DirSync 流向組織識別碼。 停用/刪除帳戶可能需要長達三個小時才能完成同步處理，導致 Azure AD 中停用/刪除應用程式密碼時延遲。
* 應用程式密碼不會遵守內部部署用戶端存取控制設定。
* 應用程式密碼不適用內部部署驗證記錄/稽核功能。
* 某些進階架構設計在使用雙步驟驗證時，可能需要搭配使用組織使用者名稱和密碼及應用程式密碼，需視驗證的位置而定。 對於根據內部部署基礎結構進行驗證的用戶端，您可以使用組織使用者名稱和密碼。 對於根據 Azure AD 進行驗證的用戶端，您需要使用應用程式密碼。
* 根據預設，使用者無法建立應用程式密碼。 如果您需要允許使用者建立應用程式密碼，請選取服務設定下的 [允許使用者建立應用程式密碼以登入非瀏覽器應用程式] 選項。

## <a name="next-steps"></a>後續步驟

[啟用貴組織的自助密碼重設](quickstart-sspr.md)

[啟用貴組織的 Azure 多重要素驗證](howto-mfa-getstarted.md)

[啟用租使用者中的合併註冊](howto-registration-mfa-sspr-combined.md)

[使用者驗證方法設定文件](https://aka.ms/securityinfoguide)

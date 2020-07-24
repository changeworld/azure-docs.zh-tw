---
title: Azure Active Directory 無密碼登入（預覽）
description: 瞭解使用 FIDO2 安全性金鑰或 Microsoft Authenticator 應用程式無密碼登入 Azure Active Directory 的選項
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 105f911b97e01a4b05673fc67b51c677df15eb89
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87051289"
---
# <a name="passwordless-authentication-options-for-azure-active-directory"></a>Azure Active Directory 的無密碼 authentication 選項

多重要素驗證（MFA）之類的功能是保護組織安全的絕佳方法，但是使用者通常會在必須記住密碼的情況下，對額外的安全性層感到沮喪。 無密碼驗證方法更方便，因為密碼已移除，並以您擁有的內容取代，再加上您所知的東西。

| 驗證  | 您擁有的內容 | 您或知道的東西 |
| --- | --- | --- |
| 無密碼 | Windows 10 裝置、電話或安全性金鑰 | 生物識別或 PIN |

在驗證方面，每個組織都有不同的需求。 Microsoft 提供下列三種與 Azure Active Directory 整合的無密碼 authentication 選項（Azure AD）：

- Windows Hello 企業版
- Microsoft Authenticator 應用程式
- FIDO2 安全性金鑰

![驗證：安全性與便利性](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>Windows Hello 企業版

Windows Hello 企業版適用于擁有自己指定 Windows 電腦的資訊工作者。 生物識別和 PIN 認證會直接系結至使用者的電腦，以防止擁有者以外的任何人進行存取。 使用公開金鑰基礎結構（PKI）整合和單一登入（SSO）的內建支援，Windows Hello 企業版提供便利的方法，讓您順暢地存取內部部署和雲端中的公司資源。

![使用 Windows Hello 企業版的使用者登入範例](./media/concept-authentication-passwordless/windows-hellow-sign-in.jpeg)

下列步驟顯示登入程式如何與 Azure AD 搭配運作：

![概述使用 Windows Hello 企業版進行使用者登入所需步驟的圖表](./media/concept-authentication-passwordless/windows-hello-flow.png)

1. 使用者使用生物特徵辨識或 PIN 手勢登入 Windows。 筆勢會解除鎖定 Windows Hello 企業版的私密金鑰，並傳送至雲端驗證安全性支援提供者，稱為*雲端 AP 提供者*。
1. 雲端 AP 提供者會向 Azure AD 要求 nonce。
1. Azure AD 會傳回有效期限為5分鐘的 nonce。
1. 雲端 AP 提供者會使用使用者的私密金鑰來簽署 nonce，並將簽署的 nonce 傳回到 Azure AD。
1. Azure AD 會使用使用者的安全註冊公開金鑰，來驗證簽署的 nonce 是否符合 nonce 簽章。 驗證簽章之後，Azure AD 接著會驗證傳回的簽署 nonce。 當 nonce 通過驗證時，Azure AD 會建立主要重新整理權杖（PRT），並將工作階段金鑰加密為裝置的傳輸金鑰，並將它傳回給雲端 AP 提供者。
1. 雲端 AP 提供者會使用工作階段金鑰接收加密的 PRT。 使用裝置的私用傳輸金鑰，雲端 AP 提供者會解密工作階段金鑰，並使用裝置的可信賴平臺模組（TPM）來保護工作階段金鑰。
1. 雲端 AP 提供者會將成功的驗證回應傳回給 Windows。 使用者接著可以存取 Windows 以及雲端和內部部署應用程式，而不需要再次驗證（SSO）。

Windows Hello 企業版[規劃指南](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide)可用來協助您決定要使用的 Windows Hello 企業版部署類型，以及您需要考慮的選項。

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator 應用程式

您也可以讓員工的電話成為無密碼的驗證方法。 除了密碼以外，您可能已經使用 Microsoft Authenticator 應用程式作為便利的多重要素驗證選項。 您也可以使用驗證器應用程式作為無密碼選項。

![使用 Microsoft Authenticator 應用程式登入 Microsoft Edge](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

驗證器應用程式會將任何 iOS 或 Android 手機轉換成強式的無密碼認證。 使用者可以登入任何平臺或瀏覽器，方法是取得電話的通知，比對螢幕上顯示的數位與電話上的號碼，然後使用其生物識別（觸控或臉部）或 PIN 來確認。 如需安裝詳細資料，請參閱[下載並安裝 Microsoft Authenticator 應用程式](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install)。

使用驗證器應用程式的無密碼 authentication 遵循與 Windows Hello 企業版相同的基本模式。 因為需要識別使用者，讓 Azure AD 可以找到所使用的 Microsoft Authenticator 應用程式版本，所以稍微複雜一點：

![概述使用者使用 Microsoft Authenticator 應用程式登入所需步驟的圖表](./media/concept-authentication-passwordless/authenticator-app-flow.png)

1. 使用者輸入其使用者名稱。
1. Azure AD 偵測到使用者具有強式認證，並啟動強式認證流程。
1. 系統會透過 iOS 裝置上的 Apple Push Notification Service （APNS），或透過 Android 裝置上的 Firebase 雲端通訊（FCM），將通知傳送至應用程式。
1. 使用者會收到推播通知，並開啟應用程式。
1. 應用程式會呼叫 Azure AD 並接收證明的挑戰和 nonce。
1. 使用者藉由輸入其生物識別或 PIN 碼來解除鎖定私密金鑰，來完成這項挑戰。
1. Nonce 是以私密金鑰簽署，並傳送回 Azure AD。
1. Azure AD 會執行公開/私密金鑰驗證，並傳回權杖。

若要開始使用無密碼登入，請完成下列操作說明：

> [!div class="nextstepaction"]
> [使用驗證器應用程式啟用無密碼 sign](howto-authentication-passwordless-phone.md)

## <a name="fido2-security-keys"></a>FIDO2 安全性金鑰

FIDO2 安全性金鑰是一種以 unphishable 標準為基礎的無密碼驗證方法，可採用任何形式的規格。 快速身分識別線上（FIDO）是無密碼 authentication 的開放標準。 FIDO 可讓使用者和組織利用外部安全性金鑰或裝置內建的平臺金鑰，使用標準來登入其資源，而不需要使用者名稱或密碼。

員工可以使用安全性金鑰來登入其 Azure AD 或混合式 Azure AD 已加入 Windows 10 裝置，並取得單一登入其雲端和內部部署資源。 使用者也可以登入支援的瀏覽器。 對於安全性敏感的企業，或有不願意或無法使用其電話作為第二個因素的案例，FIDO2 安全性金鑰是很好的選擇。

以 FIDO2 安全性金鑰登入 Azure AD 目前為預覽狀態。

![使用安全性金鑰登入 Microsoft Edge](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

當使用者以 FIDO2 安全性金鑰登入時，會使用下列處理常式：

![概述使用者以 FIDO2 安全性金鑰登入所需步驟的圖表](./media/concept-authentication-passwordless/fido2-security-key-flow.png)

1. 使用者會將 FIDO2 安全性金鑰插入電腦中。
2. Windows 會偵測 FIDO2 安全性金鑰。
3. Windows 會傳送驗證要求。
4. Azure AD 會傳回 nonce。
5. 使用者完成其手勢，將儲存在 FIDO2 安全性金鑰安全記憶體保護區中的私密金鑰解除鎖定。
6. FIDO2 安全性金鑰會使用私密金鑰來簽署 nonce。
7. 具有簽署 nonce 的主要重新整理權杖（PRT）權杖要求會傳送至 Azure AD。
8. Azure AD 使用 FIDO2 公開金鑰來驗證簽署的 nonce。
9. Azure AD 會傳回 PRT，以啟用內部部署資源的存取權。

雖然 FIDO 聯盟有許多 FIDO2 認證的金鑰，但 Microsoft 還是需要由廠商實行的 FIDO2 用戶端對驗證器通訊協定（CTAP）規格的一些選用延伸模組，以確保最高的安全性和最佳體驗。

安全性金鑰**必須**將下列功能和延伸模組從 FIDO2 CTAP 通訊協定實作為 Microsoft 相容：

| # | 功能/延伸模組信任 | 為什麼需要這項功能或延伸模組？ |
| --- | --- | --- |
| 1 | 常駐金鑰 | 這項功能可讓安全性金鑰成為可移植的，您的認證會儲存在安全性金鑰上。 |
| 2 | 用戶端 pin | 這項功能可讓您使用第二個因素來保護您的認證，並套用至沒有使用者介面的安全性金鑰。 |
| 3 | hmac-秘密 | 此延伸模組可確保您可以在裝置離線或在飛機模式時，登入您的裝置。 |
| 4 | 每個 RP 的多個帳戶 | 這項功能可確保您可以在多個服務（例如 Microsoft 帳戶和 Azure Active Directory）上使用相同的安全性金鑰。 |

下列提供者提供 FIDO2 的安全性金鑰，這些是已知與無密碼體驗相容的不同外型規格。 我們建議您透過聯繫廠商以及 FIDO 聯盟，來評估這些金鑰的安全性屬性。

| 提供者 | Contact |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| TrustKey 解決方案 | [https://www.trustkeysolutions.com/security-keys/](https://www.trustkeysolutions.com/security-keys/) |
| AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |
| Gemalto （Thales 群組） | [https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/](https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/) |
| OneSpan Inc。 | [https://www.onespan.com/products/fido](https://www.onespan.com/products/fido) |
| IDmelon 技術 Inc。 | [https://www.idmelon.com/#idmelon](https://www.idmelon.com/#idmelon) | 

> [!NOTE]
> 如果您購買並計畫使用 NFC 型安全性金鑰，您需要有支援的 NFC 讀取器來取得安全性金鑰。 NFC 讀取器不是 Azure 需求或限制。 請洽詢廠商以取得您的 NFC 型安全性金鑰，以取得支援的 NFC 讀取器清單。

如果您是廠商，而且想要在這份支援的裝置清單上取得您的裝置，請聯絡 [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com) 。

若要開始使用 FIDO2 安全性金鑰，請完成下列操作說明：

> [!div class="nextstepaction"]
> [使用 FIDO2 安全性金鑰啟用無密碼 sign](howto-authentication-passwordless-security-key.md)


## <a name="what-scenarios-work-with-the-preview"></a>哪些案例適用于預覽版？

Azure AD 無密碼登入功能目前為預覽狀態。 您必須考量下列事項：

- 系統管理員可以為其租使用者啟用無密碼 authentication 方法
- 系統管理員可以將所有使用者設為目標，或選取其租使用者中每個方法的使用者/群組
- 使用者可以在其帳戶入口網站中註冊和管理這些無密碼驗證方法
- 終端使用者可以使用這些無密碼的驗證方法登入
   - Microsoft Authenticator 應用程式：適用于使用 Azure AD 驗證的案例，包括跨所有瀏覽器、在 Windows 10 全新（OOBE）安裝期間，以及任何作業系統上的整合式行動應用程式。
   - 安全性金鑰：在支援的瀏覽器（如 Microsoft Edge）（舊版和新邊緣）中，在 Windows 10 和 web 的鎖定畫面上工作。

## <a name="choose-a-passwordless-method"></a>選擇無密碼方法

這三個無密碼選項之間的選擇取決於您公司的安全性、平臺和應用程式需求。

以下是您在選擇 Microsoft 無密碼技術時要考慮的一些因素：

||**Windows Hello 企業版**|**無密碼使用 Microsoft Authenticator 應用程式登入**|**FIDO2 安全性金鑰**|
|:-|:-|:-|:-|
|**先決條件**| Windows 10，版本 1809 或更新版本<br>Azure Active Directory| Microsoft Authenticator 應用程式<br>電話（執行 Android 6.0 或更新版本的 iOS 和 Android 裝置）。|Windows 10，版本 1809 或更新版本<br>Azure Active Directory|
|**Mode**|平台|軟體|硬體|
|**系統和裝置**|具有內建信賴平臺模組（TPM）的電腦<br>PIN 和生物識別辨識 |電話上的 PIN 和生物識別辨識|FIDO2 與 Microsoft 相容的安全性裝置|
|**使用者體驗**|使用 PIN 或生物識別辨識（臉部、鳶尾花或指紋）來登入 Windows 裝置。<br>Windows Hello 驗證會系結至裝置;使用者需要裝置和登入元件（例如 PIN 或生物特徵辨識因素）來存取公司資源。|使用具有指紋掃描、臉部或鳶尾花辨識或 PIN 的行動電話登入。<br>使用者從他們的電腦或行動電話登入公司或個人帳戶。|使用 FIDO2 安全性裝置（生物識別、PIN 和 NFC）登入<br>使用者可以根據組織控制來存取裝置，並根據 PIN 進行驗證、使用 USB 安全性金鑰和具備 NFC 功能的智慧卡、金鑰或穿戴式裝置等裝置進行生物識別。|
|**啟用的案例**| Windows 裝置的密碼較少體驗。<br>適用于具有單一登入裝置和應用程式功能的專用工作電腦。|使用行動電話的無密碼位置解決方案。<br>適用于從任何裝置存取 web 上的工作或個人應用程式。|使用生物識別、PIN 和 NFC 之背景工作角色的密碼較少體驗。<br>適用于共用的電腦，且行動電話不是可行的選項（例如技術支援人員、公用 kiosk 或醫院團隊）|

使用下表來選擇哪些方法將支援您的需求和使用者。

|生活|案例|環境|無密碼技術|
|:-|:-|:-|:-|
|**管理員**|保護裝置的存取權以進行管理工作|指派的 Windows 10 裝置|Windows Hello 企業版和（或） FIDO2 安全性金鑰|
|**管理員**|非 Windows 裝置上的管理工作| 行動或非 windows 裝置|無密碼使用 Microsoft Authenticator 應用程式登入|
|**資訊工作者**|生產力工作|指派的 Windows 10 裝置|Windows Hello 企業版和（或） FIDO2 安全性金鑰|
|**資訊工作者**|生產力工作| 行動或非 windows 裝置|無密碼使用 Microsoft Authenticator 應用程式登入|
|**第一線背景工作**|工廠、工廠、零售或資料輸入中的 kiosk|共用的 Windows 10 裝置|FIDO2 安全性金鑰|

## <a name="next-steps"></a>後續步驟

若要開始在 Azure AD 中使用無密碼，請完成下列其中一個操作說明：

* [啟用 FIDO2 安全性金鑰無密碼登入](howto-authentication-passwordless-security-key.md)
* [使用驗證器應用程式啟用以電話為基礎的無密碼登入](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>外部連結

* [FIDO 聯盟](https://fidoalliance.org/)
* [FIDO2 CTAP 規格](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)

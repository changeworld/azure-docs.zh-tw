---
title: 'Azure Active Directory 無密碼登入 (preview) '
description: 瞭解使用 FIDO2 安全性金鑰或 Microsoft Authenticator 應用程式無密碼登入 Azure Active Directory 的選項
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 352956c1f55de5cfff3ee114394e86a75d55765d
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91965534"
---
# <a name="passwordless-authentication-options-for-azure-active-directory"></a>Azure Active Directory 的無密碼 authentication 選項

多重要素驗證 (MFA) 這類功能是保護您組織的絕佳方法，但使用者通常會在必須記住其密碼的情況下，讓額外的安全性層級感到挫折。 無密碼 authentication 方法更為方便，因為密碼已移除，並取代為您擁有的內容，以及您或您所知的東西。

| 驗證  | 您有的項目 | 您或知道的事項 |
| --- | --- | --- |
| 無密碼 | Windows 10 裝置、電話或安全性金鑰 | 生物特徵辨識或 PIN |

在驗證時，每個組織都有不同的需求。 Microsoft 提供下列三種無密碼驗證選項，可與 Azure Active Directory (Azure AD) 整合：

- Windows Hello 企業版
- Microsoft Authenticator 應用程式
- FIDO2 安全性金鑰

![驗證：安全性與便利性](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>Windows Hello 企業版

Windows Hello 企業版適用于擁有專屬 Windows 電腦的資訊工作者。 生物識別和 PIN 認證會直接系結至使用者的電腦，以防止擁有者以外的任何人存取。 使用公開金鑰基礎結構 (PKI) 整合和內建的單一登入支援 (SSO) ，Windows Hello 企業版提供便利的方法，讓您無縫地存取內部部署和雲端中的公司資源。

![使用 Windows Hello 企業版的使用者登入範例](./media/concept-authentication-passwordless/windows-hellow-sign-in.jpeg)

下列步驟顯示登入程式如何與 Azure AD 搭配運作：

![概述使用者使用 Windows Hello 企業版登入所需之步驟的圖表](./media/concept-authentication-passwordless/windows-hello-flow.png)

1. 使用者使用生物特徵辨識或 PIN 手勢登入 Windows。 手勢會解除鎖定 Windows Hello 企業版的私密金鑰，並傳送給雲端驗證安全性支援提供者，稱為「 *雲端 AP 提供者*」。
1. 雲端 AP 提供者要求 nonce (隨機任一數字，只要從 Azure AD) ，就可以使用該任一數字。
1. Azure AD 會傳回有效期限為5分鐘的 nonce。
1. 雲端 AP 提供者會使用使用者的私密金鑰來簽署 nonce，並將簽署的 nonce 傳回 Azure AD。
1. Azure AD 會對 nonce 簽章使用使用者安全註冊的公開金鑰來驗證已簽署的 nonce。 驗證簽章之後，Azure AD 接著會驗證傳回的已簽署 nonce。 驗證 nonce 之後，Azure AD 會 (PRT) 建立主要重新整理權杖，並將工作階段金鑰加密為裝置的傳輸金鑰，並將其傳回給雲端 AP 提供者。
1. 雲端 AP 提供者會使用工作階段金鑰接收加密的 PRT。 使用裝置的私人傳輸金鑰時，雲端 AP 提供者會使用裝置的可信賴平臺模組 (TPM) 來解密工作階段金鑰，並保護工作階段金鑰。
1. 雲端 AP 提供者會傳回成功的 Windows 驗證回應。 使用者接著可以存取 Windows 以及雲端和內部部署應用程式，而不需要再次驗證 (SSO) 。

Windows Hello 企業版 [規劃指南](/windows/security/identity-protection/hello-for-business/hello-planning-guide) 可以用來協助您決定 Windows Hello 企業版部署的類型，以及您需要考慮的選項。

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator 應用程式

您也可以讓員工的電話成為無密碼的驗證方法。 除了密碼以外，您可能已經使用 Microsoft Authenticator 應用程式做為方便的多重要素驗證選項。 您也可以使用驗證器應用程式作為無密碼選項。

![使用 Microsoft Authenticator 應用程式登入 Microsoft Edge](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

驗證器應用程式會將任何 iOS 或 Android 手機變成強式無密碼認證。 使用者可以登入任何平臺或瀏覽器，方法是取得電話的通知、比對畫面上顯示的數位與手機上的數位，然後使用其生物特徵辨識 (觸控或臉部) 或 PIN 來確認。 如需安裝詳細資料，請參閱 [下載並安裝 Microsoft Authenticator 應用程式](../user-help/user-help-auth-app-download-install.md) 。

無密碼使用 Microsoft Authenticator 應用程式登入 Azure AD 目前為預覽狀態。 使用 Microsoft Authenticator 應用程式進行 Azure Multi-Factor Authentication 的次要驗證、自助式密碼重設 (SSPR) 或 OATH 軟體權杖正式運作。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

使用驗證器應用程式的無密碼 authentication，會遵循與 Windows Hello 企業版相同的基本模式。 由於必須識別使用者，讓 Azure AD 可以找到所使用的 Microsoft Authenticator 應用程式版本，因此會稍微複雜一點：

![概述使用者使用 Microsoft Authenticator 應用程式登入所需之步驟的圖表](./media/concept-authentication-passwordless/authenticator-app-flow.png)

1. 使用者輸入其使用者名稱。
1. Azure AD 會偵測到使用者有強式認證，並啟動強式認證流程。
1. 系統會透過 iOS 裝置上的 Apple Push Notification Service (APNS) 或在 Android 裝置上透過 Firebase 雲端通訊 (FCM) ，將通知傳送至應用程式。
1. 使用者會收到推播通知，並開啟應用程式。
1. 應用程式會呼叫 Azure AD，並接收目前狀態證明的挑戰和 nonce。
1. 使用者藉由輸入其生物特徵辨識或 PIN 碼來解除鎖定私密金鑰，來完成此項挑戰。
1. Nonce 會以私密金鑰簽署並傳送回 Azure AD。
1. Azure AD 會執行公開/私密金鑰驗證並傳回權杖。

若要開始使用無密碼登入，請完成下列操作說明：

> [!div class="nextstepaction"]
> [使用驗證器應用程式啟用無密碼 sign](howto-authentication-passwordless-phone.md)

## <a name="fido2-security-keys"></a>FIDO2 安全性金鑰

FIDO (快速身分識別線上) 聯盟有助於推廣開放式驗證標準，並減少使用密碼作為驗證形式。 FIDO2 是併入 Web 驗證 (WebAuthn) 標準的最新標準。

FIDO2 安全性金鑰是以 unphishable 標準為基礎的無密碼驗證方法，可採用任何外型規格。 快速身分識別線上 (FIDO) 是無密碼 authentication 的開放標準。 FIDO 可讓使用者和組織利用標準，在沒有使用者名稱或密碼的情況下登入其資源，而不需使用外部安全性金鑰或裝置內建的平臺金鑰。

使用者可以註冊，然後在登入介面選取 FIDO2 安全性金鑰，昨為其主要的驗證方法。 這些 FIDO2 的安全性金鑰通常是 USB 裝置，但也可使用藍牙或 NFC。 使用可處理驗證的硬體裝置時，由於沒有可公開或猜到的密碼，因而提高帳戶的安全性。

您可以使用 FIDO2 安全性金鑰來登入其 Azure AD 或加入混合式 Azure AD 加入 Windows 10 裝置，並取得其雲端和內部部署資源的單一登入。 使用者也可以登入支援的瀏覽器。 針對安全性敏感的企業，或有不願意或無法以其電話作為第二個因素的案例或員工，FIDO2 安全性金鑰是絕佳的選擇。

使用 FIDO2 安全性金鑰登入 Azure AD 目前為預覽狀態。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

![使用安全性金鑰登入 Microsoft Edge](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

當使用者登入 FIDO2 安全性金鑰時，會使用下列處理常式：

![概述使用者以 FIDO2 安全性金鑰登入所需之步驟的圖表](./media/concept-authentication-passwordless/fido2-security-key-flow.png)

1. 使用者將 FIDO2 安全性金鑰插入他們的電腦。
2. Windows 偵測到 FIDO2 安全性金鑰。
3. Windows 傳送驗證要求。
4. Azure AD 傳回 nonce。
5. 使用者完成其手勢來解除鎖定儲存在 FIDO2 安全性金鑰安全記憶體保護區中的私密金鑰。
6. FIDO2 安全性金鑰會使用私密金鑰來簽署 nonce。
7. 主要重新整理權杖 (具有簽署 nonce 的 PRT) 權杖要求會傳送至 Azure AD。
8. Azure AD 使用 FIDO2 公開金鑰來驗證已簽署的 nonce。
9. Azure AD 會傳回 PRT，以啟用對內部部署資源的存取。

雖然有許多 FIDO2 由 FIDO 聯盟認證的金鑰，但 Microsoft 仍需要有一些選用的 FIDO2 用戶端對驗證者通訊協定延伸模組， (CTAP) 規格才能由廠商執行，以確保最高的安全性和最佳體驗。

安全性金鑰 **必須** 從 FIDO2 的 CTAP protocol 執行下列功能和延伸模組，才能與 Microsoft 相容：

| # | 功能/延伸模組信任 | 為何需要這項功能或延伸模組？ |
| --- | --- | --- |
| 1 | 常駐金鑰 | 這項功能可讓安全性金鑰成為可移植的，您的認證會儲存在安全性金鑰上。 |
| 2 | 用戶端 pin | 這項功能可讓您使用第二個因素保護您的認證，並套用至沒有使用者介面的安全性金鑰。 |
| 3 | hmac-秘密 | 此延伸模組可確保您可以在離線或飛機模式時登入您的裝置。 |
| 4 | 每個 RP 有多個帳戶 | 這項功能可確保您可以在多個服務（例如 Microsoft 帳戶和 Azure Active Directory）上使用相同的安全性金鑰。 |

### <a name="fido2-security-key-providers"></a>FIDO2 安全性金鑰提供者

下列提供者提供 FIDO2 的安全性金鑰，這些是已知與無密碼體驗相容的不同外型規格。 我們建議您藉由聯絡廠商和 FIDO 聯盟來評估這些金鑰的安全性屬性。

| 提供者 | Contact |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| 飛天 | [https://shop.ftsafe.us/pages/microsoft](https://shop.ftsafe.us/pages/microsoft) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| TrustKey 解決方案 | [https://www.trustkeysolutions.com/security-keys/](https://www.trustkeysolutions.com/security-keys/) |
| AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |
| Gemalto (Thales 群組)  | [https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/](https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/) |
| OneSpan Inc。 | [https://www.onespan.com/products/fido](https://www.onespan.com/products/fido) |
| IDmelon 技術 Inc。 | [https://www.idmelon.com/#idmelon](https://www.idmelon.com/#idmelon) | 

> [!NOTE]
> 如果您購買並計畫使用以 NFC 為基礎的安全性金鑰，則需要安全性金鑰支援的 NFC 讀取程式。 NFC 讀取器不是 Azure 需求或限制。 請洽詢廠商以取得以 NFC 為基礎的安全性金鑰，以取得支援的 NFC 讀取器清單。

如果您是廠商，並想要讓您的裝置在這份支援的裝置清單上，請聯絡 [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com) 。

若要開始使用 FIDO2 安全性金鑰，請完成下列操作說明：

> [!div class="nextstepaction"]
> [啟用使用 FIDO2 安全性金鑰的無密碼 sign](howto-authentication-passwordless-security-key.md)

## <a name="what-scenarios-work-with-the-preview"></a>哪些案例適用于預覽？

Azure AD 無密碼登入功能目前為預覽狀態。 您必須考量下列事項：

- 系統管理員可以為其租使用者啟用無密碼 authentication 方法
- 系統管理員可以將所有使用者設為目標，或針對每個方法選取其租使用者中的使用者/群組
- 終端使用者可以在他們的帳戶入口網站中註冊及管理這些無密碼 authentication 方法
- 終端使用者可以使用這些無密碼 authentication 方法登入
   - Microsoft Authenticator 應用程式：適用于使用 Azure AD 驗證的案例，包括所有瀏覽器、Windows 10 現成可用 (OOBE) 安裝程式，以及任何作業系統上的整合式行動應用程式。
   - 安全性金鑰：在 Windows 10 的鎖定畫面上工作，並在支援的瀏覽器（例如 Microsoft Edge (舊版和新的 Edge) ）中工作。

## <a name="choose-a-passwordless-method"></a>選擇無密碼方法

這三個無密碼選項之間的選擇取決於您公司的安全性、平臺和應用程式需求。

以下是您在選擇 Microsoft 無密碼技術時應考慮的一些因素：

||**Windows Hello 企業版**|**使用 Microsoft Authenticator 應用程式無密碼登入**|**FIDO2 安全性金鑰**|
|:-|:-|:-|:-|
|**先決條件**| Windows 10，版本 1809 或更新版本<br>Azure Active Directory| Microsoft Authenticator 應用程式<br>手機 (執行 Android 6.0 或更新版本的 iOS 和 Android 裝置。 ) |Windows 10，1903版或更新版本<br>Azure Active Directory|
|**Mode**|平台|軟體|硬體|
|**系統和裝置**|具有內建信賴平臺模組 (TPM) 的電腦<br>PIN 和生物識別辨識 |手機上的 PIN 和生物識別辨識|FIDO2 Microsoft 相容的安全性裝置|
|**使用者體驗**|使用 PIN 或生物識別辨識進行登入， (臉部、鳶尾花或指紋) 與 Windows 裝置。<br>Windows Hello authentication 會系結至裝置;使用者需要裝置和登入元件（例如 PIN 或生物特徵辨識因素）來存取公司資源。|使用具有指紋掃描、臉部或鳶尾花辨識或 PIN 的行動電話進行登入。<br>使用者可以從他們的電腦或行動電話登入工作或個人帳戶。|使用 FIDO2 安全性裝置登入 (生物識別、PIN 和 NFC) <br>使用者可以使用裝置來存取裝置，並根據 PIN、使用 USB 安全性金鑰和啟用 NFC 的智慧卡、金鑰或穿戴式裝置等裝置來進行生物特徵辨識。|
|**啟用的案例**| Windows 裝置的無密碼體驗。<br>適用于具有單一登入裝置和應用程式能力的專用工作電腦。|使用行動電話的無密碼隨處解決方案。<br>適用于從任何裝置存取網站上的工作或個人應用程式。|使用生物識別、PIN 和 NFC 之工作者的無密碼體驗。<br>適用于共用電腦，而且行動電話不是可行的選項 (例如支援服務中心人員、公共 kiosk 或醫院團隊) |

使用下表來選擇可支援您的需求和使用者的方法。

|角色|案例|環境|無密碼技術|
|:-|:-|:-|:-|
|**管理員**|安全存取裝置以進行管理工作|指派 Windows 10 裝置|Windows Hello 企業版及/或 FIDO2 安全性金鑰|
|**管理員**|非 Windows 裝置上的管理工作| 行動裝置或非 windows 裝置|使用 Microsoft Authenticator 應用程式無密碼登入|
|**資訊工作者**|生產力工作|指派 Windows 10 裝置|Windows Hello 企業版及/或 FIDO2 安全性金鑰|
|**資訊工作者**|生產力工作| 行動裝置或非 windows 裝置|使用 Microsoft Authenticator 應用程式無密碼登入|
|**Frontline 背景工作**|工廠、工廠、零售或資料輸入的 kiosk|共用的 Windows 10 裝置|FIDO2 安全性金鑰|

## <a name="next-steps"></a>後續步驟

若要開始使用 Azure AD 中的無密碼，請完成下列其中一個作法：

* [啟用 FIDO2 安全性金鑰無密碼登入](howto-authentication-passwordless-security-key.md)
* [使用驗證器應用程式啟用以電話為基礎的無密碼登入](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>外部連結

* [FIDO 聯盟](https://fidoalliance.org/)
* [FIDO2 CTAP 規格](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)

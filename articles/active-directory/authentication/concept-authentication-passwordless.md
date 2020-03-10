---
title: Azure Active Directory 無密碼登入（預覽）
description: 瞭解使用 FIDO2 安全性金鑰或 Microsoft Authenticator 應用程式無密碼登入 Azure Active Directory 的選項
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: ffe3d359dffea3278337de00bea42e0b959e10f6
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78378195"
---
# <a name="passwordless-authentication-options-for-azure-active-directory"></a>Azure Active Directory 的無密碼 authentication 選項

多重要素驗證（MFA）是保護組織安全的好方法，但是使用者通常會在必須記住密碼的情況下，使額外的安全性層感到沮喪。 無密碼驗證方法更方便，因為密碼已移除，並以您擁有的內容取代，再加上您所知的東西。

|   | 您擁有的內容 | 您或知道的東西 |
| --- | --- | --- |
| 無密碼 | Windows 10 裝置、電話或安全性金鑰 | 生物識別或 PIN |

在驗證方面，每個組織都有不同的需求。 Microsoft 提供下列三種無密碼驗證選項：

- Windows Hello 企業版
- Microsoft Authenticator 應用程式
- FIDO2 安全性金鑰

![驗證：安全性與便利性](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>Windows Hello 企業版

Windows Hello 企業版適用于擁有自己指定 Windows 電腦的資訊工作者。 生物識別和 PIN 會直接系結至使用者的電腦，以防止擁有者以外的任何人進行存取。 使用公開金鑰基礎結構（PKI）整合和單一登入（SSO）的內建支援，Windows Hello 企業版提供便利的方法，讓您順暢地存取內部部署和雲端中的公司資源。

Windows Hello 企業版[規劃指南](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide)可用來協助您決定要使用的 Windows Hello 企業版部署類型，以及您需要考慮的選項。

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator 應用程式

允許員工的電話成為無密碼的驗證方法。 除了密碼以外，您可能已經使用 Microsoft Authenticator 應用程式作為便利的多重要素驗證選項。 您也可以使用驗證器應用程式作為無密碼選項。

![使用 Microsoft Authenticator 應用程式登入 Microsoft Edge](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

驗證器應用程式會將任何 iOS 或 Android 手機轉換成強式的無密碼認證。 使用者可以登入任何平臺或瀏覽器，方法是取得電話的通知，比對螢幕上顯示的數位與電話上的號碼，然後使用其生物識別（觸控或臉部）或 PIN 來確認。

## <a name="fido2-security-keys"></a>FIDO2 安全性金鑰

FIDO2 安全性金鑰是一種以 unphishable 標準為基礎的無密碼驗證方法，可採用任何形式的規格。 快速身分識別線上（FIDO）是無密碼 authentication 的開放標準。 FIDO 可讓使用者和組織利用外部安全性金鑰或裝置內建的平臺金鑰，使用標準來登入其資源，而不需要使用者名稱或密碼。

對於公開預覽，員工可以使用安全性金鑰來登入其 Azure AD 或混合式 Azure AD 加入的 Windows 10 裝置，並取得單一登入其雲端和內部部署資源。 使用者也可以登入支援的瀏覽器。 對於安全性敏感的企業，或有不願意或無法使用其電話作為第二個因素的案例，FIDO2 安全性金鑰是很好的選擇。

![使用安全性金鑰登入 Microsoft Edge](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

雖然 FIDO 聯盟有許多 FIDO2 認證的金鑰，但 Microsoft 還是需要由廠商實行的 FIDO2 用戶端對驗證器通訊協定（CTAP）規格的一些選用延伸模組，以確保最高的安全性和最佳遇到.

安全性金鑰**必須**將下列功能和延伸模組從 FIDO2 CTAP 通訊協定實作為 Microsoft 相容：

| # | 功能/延伸模組信任 | 為什麼需要這項功能或延伸模組？ |
| --- | --- | --- |
| 1 | 常駐金鑰 | 這項功能可讓安全性金鑰成為可移植的，您的認證會儲存在安全性金鑰上。 |
| 2 | 用戶端 pin | 這項功能可讓您使用第二個因素來保護您的認證，並套用至沒有使用者介面的安全性金鑰。 |
| 3 | hmac-secret | 此延伸模組可確保您可以在裝置離線或在飛機模式時，登入您的裝置。 |
| 4 | 每個 RP 的多個帳戶 | 這項功能可確保您可以在多個服務（例如 Microsoft 帳戶和 Azure Active Directory）上使用相同的安全性金鑰。 |

下列提供者提供 FIDO2 的安全性金鑰，這些是已知與無密碼體驗相容的不同外型規格。 我們建議您透過聯繫廠商以及 FIDO 聯盟，來評估這些金鑰的安全性屬性。

| 提供者 | 連絡人 |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| eWBM | [https://www.ewbm.com/support](https://www.ewbm.com/support) |
| AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |

> [!NOTE]
> 如果您購買並計畫使用 NFC 型安全性金鑰，您需要有支援的 NFC 讀取器來取得安全性金鑰。 NFC 讀取器不是 Azure 需求或限制。 請洽詢廠商以取得您的 NFC 型安全性金鑰，以取得支援的 NFC 讀取器清單。

如果您是廠商，而且想要在這份支援的裝置清單中取得您的裝置，請聯絡[Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com)。

## <a name="what-scenarios-work-with-the-preview"></a>哪些案例適用于預覽版？

- 系統管理員可以為其租使用者啟用無密碼 authentication 方法
- 系統管理員可以將所有使用者設為目標，或選取其租使用者中每個方法的使用者/群組
- 使用者可以在其帳戶入口網站中註冊和管理這些無密碼驗證方法
- 終端使用者可以使用這些無密碼的驗證方法登入
   - Microsoft Authenticator 應用程式：適用于使用 Azure AD 驗證的案例，包括跨所有瀏覽器、在 Windows 10 全新（OOBE）安裝期間，以及任何作業系統上的整合式行動應用程式。
   - 安全性金鑰：在支援的瀏覽器（如 Microsoft Edge）（舊版和新邊緣）中，在 Windows 10 和 web 的鎖定畫面上工作。

## <a name="next-steps"></a>後續步驟

[在您的組織中啟用 FIDO2 安全性金鑰 passwordlesss 選項](howto-authentication-passwordless-security-key.md)

[在您的組織中啟用以電話為基礎的無密碼選項](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>外部連結

[FIDO 聯盟](https://fidoalliance.org/)

[FIDO2 CTAP 規格](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)

---
title: Azure 活動目錄無密碼登錄（預覽）
description: 使用 FIDO2 安全金鑰或 Microsoft 身份驗證器應用瞭解無密碼登錄 Azure 活動目錄的選項
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07ba38a5d7e8e8a89ba122efb1734c1f13a94d48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332176"
---
# <a name="passwordless-authentication-options-for-azure-active-directory"></a>Azure 活動目錄的無密碼身份驗證選項

多重要素驗證 （MFA） 是保護組織安全的好方法，但使用者通常對必須記住其密碼的附加安全層感到沮喪。 無密碼身份驗證方法更方便，因為密碼被刪除，並替換為您擁有的東西，以及您知道的東西。

|   | 你擁有的東西 | 你是什麼人或知道的 |
| --- | --- | --- |
| 無密碼 | Windows 10 設備、電話或安全金鑰 | 生物識別或 PIN |

在身份驗證方面，每個組織都有不同的需求。 Microsoft 提供了以下三個與 Azure 活動目錄 （Azure AD） 集成的無密碼身份驗證選項：

- Windows Hello 企業版
- Microsoft Authenticator 應用程式
- FIDO2 安全金鑰

![身份驗證：安全性與便利性](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>Windows Hello 企業版

適用于具有自己指定的 Windows PC 的資訊工作者，Windows Hello 適合企業。 生物識別和 PIN 直接與使用者的 PC 相關聯，這可以防止擁有者以外的任何人訪問。 借助公開金鑰基礎結構 （PKI） 集成和對單一登入 （SSO） 的內置支援，Windows Hello For Business 提供了一種方便的方法，用於在本地和雲中無縫訪問公司資源。

![使用適用于企業的 Windows Hello 使用者登錄的示例](./media/concept-authentication-passwordless/windows-hellow-sign-in.jpeg)

以下步驟顯示了登錄過程如何與 Azure 活動目錄配合使用。

![圖表，概述了使用 Windows Hello 企業登錄的使用者參與的步驟](./media/concept-authentication-passwordless/windows-hello-flow.png)

1. 使用者使用生物識別或 PIN 手勢登錄到 Windows。 該手勢解鎖了適用于企業的 Windows Hello 私密金鑰，併發送到雲身份驗證安全支援供應商（稱為*雲 AP 供應商*）。
1. 雲 AP 提供程式從 Azure AD 請求一次 nonce。
1. Azure AD 返回有效期為 5 分鐘的 nonce。
1. 雲 AP 提供程式使用使用者的私密金鑰對 nonce 進行簽名，並將簽名的 nonce 返回到 Azure AD。
1. Azure AD 使用使用者的安全註冊公開金鑰對 nonce 簽名驗證已簽名的 nonce。 驗證簽名後，Azure AD 將驗證返回的已簽名 nonce。 驗證 nonce 後，Azure AD 將創建一個主刷新權杖 （PRT），該權杖的工作階段金鑰已加密到設備的傳輸金鑰，並將其返回到雲 AP 提供程式。
1. 雲 AP 供應商使用工作階段金鑰接收加密 PRT。 使用設備的專用傳輸金鑰，Cloud AP 供應商會解密工作階段金鑰，並使用設備的可信平臺模組 （TPM） 保護工作階段金鑰。
1. 雲 AP 提供程式向 Windows 返回成功的身份驗證回應。 然後，使用者能夠訪問 Windows 以及雲和本地應用程式，而無需再次進行身份驗證 （SSO）。

"適合企業 Windows [Hello"計劃指南](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide)可用於説明您就適用于企業部署的 Windows Hello 類型和需要考慮的選項做出決策。

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator 應用程式

允許員工的電話成為無密碼身份驗證方法。 除了密碼之外，您可能已經將 Microsoft 身份驗證器應用用作方便的多重要素驗證選項。 您還可以使用身份驗證器應用作為無密碼選項。

![使用 Microsoft 身份驗證器應用登錄到 Microsoft 邊緣](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

身份驗證器應用程式將任何 iOS 或 Android 手機轉換為強大的無密碼憑據。 使用者可以登錄到任何平臺或瀏覽器，通過收到通知到他們的手機，匹配螢幕上顯示的號碼到他們的手機，然後使用他們的生物識別（觸摸或面部）或PIN確認。 有關安裝詳細資訊[，請參閱下載並安裝 Microsoft 身份驗證器應用](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install)。

使用身份驗證器應用的無密碼身份驗證遵循與適用于企業的 Windows Hello 相同的基本模式。 由於需要識別使用者，以便 Azure AD 可以找到正在使用的 Microsoft 身份驗證器應用版本，因此它稍微複雜一些：

![概述使用 Microsoft 身份驗證器應用的使用者登錄所涉及的步驟的圖表](./media/concept-authentication-passwordless/authenticator-app-flow.png)

1. 使用者輸入其使用者名。
1. Azure AD 檢測使用者具有強憑據並啟動強憑據流。
1. 通知通過 iOS 設備上的 Apple 推送通知服務 （APNS） 或通過 Android 設備上的 Firebase 雲消息 （FCM） 發送到應用。
1. 使用者收到推送通知並打開應用。
1. 該應用程式調用 Azure AD 並接收存在驗證質詢和 nonce。
1. 使用者通過輸入其生物識別或 PIN 來解鎖私密金鑰來完成挑戰。
1. nonce 使用私密金鑰簽名併發送回 Azure AD。
1. Azure AD 執行公開金鑰/私密金鑰驗證並返回權杖。

## <a name="fido2-security-keys"></a>FIDO2 安全金鑰

FIDO2 安全金鑰是一種不可仿冒標準的無密碼身份驗證方法，可以採用任何形式因素。 快速線上身份 （FIDO） 是無密碼身份驗證的開放標準。 FIDO 允許使用者和組織使用內置於設備中的外部安全金鑰或平臺金鑰，利用標準登錄其資源，而無需使用使用者名或密碼。

對於公共預覽，員工可以使用安全金鑰登錄到其 Azure AD 或混合 Azure AD 加入的 Windows 10 設備，並單簽名到其雲和本地資源。 使用者還可以登錄到受支援的瀏覽器。 對於對安全性非常敏感或方案或員工不願意或無法使用手機作為第二個因素的企業來說，FIDO2 安全金鑰是一個不錯的選擇。

![使用安全金鑰登錄到 Microsoft 邊緣](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

當使用者使用 FIDO2 安全金鑰登錄時，將使用以下過程：

![使用 FIDO2 安全金鑰概述使用者登錄所涉及的步驟的圖表](./media/concept-authentication-passwordless/fido2-security-key-flow.png)

1. 使用者將 FIDO2 安全金鑰插入其電腦。
2. Windows 檢測到 FIDO2 安全金鑰。
3. Windows 發送身份驗證請求。
4. Azure AD 會發送回一個 nonce。
5. 使用者完成其手勢以解鎖存儲在 FIDO2 安全金鑰安全安全區中的私密金鑰。
6. FIDO2 安全金鑰使用私密金鑰標記 nonce。
7. 主刷新權杖 （PRT） 權杖請求與已簽名的 nonce 將發送到 Azure AD。
8. Azure AD 使用 FIDO2 公開金鑰驗證已簽名的 nonce。
9. Azure AD 返回 PRT 以啟用對本地資源的訪問。

雖然有許多金鑰由 FIDO 聯盟認證 FIDO2，但 Microsoft 需要供應商實施 FIDO2 用戶端到身份驗證器協定 （CTAP） 規範的一些可選擴展，以確保最大的安全性和最佳經驗。

安全金鑰**必須**實現 FIDO2 CTAP 協定中的以下功能和擴展，才能與 Microsoft 相容：

| # | 功能/擴展信任 | 為什麼需要此功能或擴展？ |
| --- | --- | --- |
| 1 | 駐留金鑰 | 此功能使安全金鑰可移植，其中憑據存儲在安全金鑰上。 |
| 2 | 用戶端引腳 | 此功能使您能夠使用第二個因素保護憑據，並應用於沒有使用者介面的安全金鑰。 |
| 3 | 赫馬克-秘密 | 此擴展可確保您可以在設備處於線路關閉或飛安模式時登錄。 |
| 4 | 每個 RP 的多個帳戶 | 此功能可確保可以在多個服務（如 Microsoft 帳戶和 Azure 活動目錄）中使用相同的安全金鑰。 |

以下提供程式提供已知與無密碼體驗相容的不同外形規格的 FIDO2 安全金鑰。 我們鼓勵您通過聯繫供應商和 FIDO 聯盟來評估這些金鑰的安全屬性。

| 提供者 | 連絡人 |
| --- | --- |
| 尤比科 | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| 飛天 | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| 恩貞 | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| eWBM | [https://www.ewbm.com/support](https://www.ewbm.com/support) |
| 奧森趨勢 | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |
| 金雅拓（泰萊斯集團） | [https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/](https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/) |
| OneSpan公司 | [https://www.onespan.com/sites/default/files/2019-08/Digipass-SecureClick_datasheet.pdf](https://www.onespan.com/sites/default/files/2019-08/Digipass-SecureClick_datasheet.pdf) |
| 伊德梅隆科技公司 | [https://www.idmelon.com/#idmelon](https://www.idmelon.com/#idmelon) | 

> [!NOTE]
> 如果您購買並計畫使用基於 NFC 的安全金鑰，則需要支援的安全金鑰 NFC 讀取器。 NFC 讀取器不是 Azure 要求或限制。 有關支援的 NFC 讀取器清單，請與供應商聯繫以獲取基於 NFC 的安全金鑰。

如果您是供應商，並希望將設備放在受支援設備清單中，請聯繫[Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com)。

## <a name="what-scenarios-work-with-the-preview"></a>哪些方案與預覽一起使用？

- 管理員可以為租戶啟用無密碼身份驗證方法
- 管理員可以針對所有使用者或選擇其租戶中的每個方法的使用者/組
- 最終使用者可以在其帳戶門戶中註冊和管理這些無密碼身份驗證方法
- 最終使用者可以使用這些無密碼身份驗證方法登錄
   - Microsoft 身份驗證器應用：適用于使用 Azure AD 身份驗證的方案，包括所有瀏覽器、Windows 10 開箱即用 （OOBE） 設置期間以及任何作業系統上的集成移動應用。
   - 安全金鑰：在 Microsoft 邊緣（舊版和全新邊緣）等受支援的瀏覽器中，在 Windows 10 和 Web 的鎖屏介面上工作。

## <a name="choose-a-passwordless-method"></a>選擇無密碼方法

這三個無密碼選項之間的選擇取決於公司的安全性、平臺和應用要求。

在選擇 Microsoft 無密碼技術時，需要考慮以下一些因素：

||**Windows Hello 企業版**|**使用 Microsoft 身份驗證器應用進行無密碼登錄**|**FIDO2 安全金鑰**|
|:-|:-|:-|:-|
|**先決條件**| Windows 10，版本 1809 或更新版本<br>Azure Active Directory| Microsoft Authenticator 應用程式<br>手機（iOS 和運行 Android 6.0 或以上 Android 的設備。|Windows 10，版本 1809 或更新版本<br>Azure Active Directory|
|**Mode**|Platform|軟體|硬體|
|**系統和設備**|具有內置可信平臺模組 （TPM） 的 PC<br>PIN 和生物識別識別 |手機上的 PIN 和生物識別識別|相容微軟的 FIDO2 安全設備|
|**使用者體驗**|使用 PIN 或生物識別（面部、虹膜或指紋）與 Windows 設備登錄。<br>Windows Hello 身份驗證綁定到設備;因此，Windows Hello 身份驗證與設備相關聯。使用者需要設備和登錄元件（如 PIN 或生物識別因數）來訪問公司資源。|使用帶有指紋掃描、面部或虹膜識別或 PIN 的手機登錄。<br>使用者通過電腦或手機登錄工作或個人帳戶。|使用 FIDO2 安全設備（生物識別、PIN 和 NFC）登錄<br>使用者可以基於組織控制訪問設備，並根據 PIN、生物識別技術（如 USB 安全金鑰和支援 NFC 的智慧卡、金鑰或可穿戴設備）進行身份驗證。|
|**已啟用的方案**| Windows 設備的無密碼體驗。<br>適用于專用工作 PC，能夠單次登錄設備和應用程式。|使用手機的任何地方無密碼解決方案。<br>適用于從任何設備訪問 Web 上的工作或個人應用程式。|使用生物識別、PIN 和 NFC 的員工無密碼體驗。<br>適用于共用 PC 和行動電話不可行的選項（例如，對於服務台人員、公共資訊亭或醫院團隊）|

使用下表選擇哪種方法將支援您的要求和使用者。

|角色|狀況|環境|無密碼技術|
|:-|:-|:-|:-|
|**管理員**|安全訪問設備以執行管理工作|分配的 Windows 10 設備|適用于企業和/或 FIDO2 安全金鑰的 Windows 您好|
|**管理員**|非 Windows 設備上的管理工作| 移動或非視窗設備|使用 Microsoft 身份驗證器應用進行無密碼登錄|
|**資訊工作者**|生產力工作|分配的 Windows 10 設備|適用于企業和/或 FIDO2 安全金鑰的 Windows 您好|
|**資訊工作者**|生產力工作| 移動或非視窗設備|使用 Microsoft 身份驗證器應用進行無密碼登錄|
|**前線工人**|工廠、工廠、零售或資料輸入中的展臺|共用 Windows 10 設備|FIDO2 安全金鑰|

## <a name="next-steps"></a>後續步驟

[在組織中啟用 FIDO2 安全金鑰無密碼選項](howto-authentication-passwordless-security-key.md)

[在組織中啟用基於電話的無密碼選項](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>外部連結

[FIDO 聯盟](https://fidoalliance.org/)

[FIDO2 CTAP 規範](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)

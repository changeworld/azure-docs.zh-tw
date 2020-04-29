---
title: IOS 上以憑證為基礎的驗證-Azure Active Directory
description: 瞭解支援的案例和針對使用 iOS 裝置之解決方案中的 Azure Active Directory 設定以憑證為基礎之驗證的需求
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ede7ddb81bae69d92983e787e779ee9d410bd87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82144074"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>iOS 上的 Azure Active Directory 憑證式驗證

為了提升安全性，iOS 裝置可以使用憑證型驗證（CBA），在連接到下列應用程式或服務時，使用裝置上的用戶端憑證來驗證 Azure Active Directory （Azure AD）：

* Office 行動應用程式，例如 Microsoft Outlook 與 Microsoft Word
* Exchange ActiveSync (EAS) 用戶端

使用憑證就不需要輸入使用者名稱和密碼組合到特定郵件，並在您的行動裝置上 Microsoft Office 應用程式。

本文詳細說明在 iOS 裝置上設定 CBA 的需求和支援案例。 適用于 iOS 的 CBA 可跨 Azure 公用雲端、Microsoft 政府雲端、Microsoft Cloud 德國和 Microsoft Azure 中國世紀提供。

## <a name="microsoft-mobile-applications-support"></a>Microsoft 行動應用程式支援

| 應用程式 | 支援 |
| --- | --- |
| Azure 資訊保護應用程式 |![核取記號表示此應用程式的支援][1] |
| Intune 公司入口網站 |![核取記號表示此應用程式的支援][1] |
| Microsoft Teams |![核取記號表示此應用程式的支援][1] |
| Office （行動裝置） |![核取記號表示此應用程式的支援][1] |
| OneNote |![核取記號表示此應用程式的支援][1] |
| OneDrive |![核取記號表示此應用程式的支援][1] |
| Outlook |![核取記號表示此應用程式的支援][1] |
| Power BI |![核取記號表示此應用程式的支援][1] |
| 商務用 Skype |![核取記號表示此應用程式的支援][1] |
| Word / Excel / PowerPoint |![核取記號表示此應用程式的支援][1] |
| Yammer |![核取記號表示此應用程式的支援][1] |

## <a name="requirements"></a>需求

若要使用 CBA 搭配 iOS，請遵循下列需求和考慮：

* 裝置作業系統版本必須是 iOS 9 或更新版本。
* iOS 上的 Office 應用程式都需要 Microsoft Authenticator。
* 必須在包含 ADFS 伺服器驗證 URL 的 macOS Keychain 中建立身分識別喜好設定。 如需詳細資訊，請參閱[在 Mac 上的 Keychain 存取中建立身分識別喜好](https://support.apple.com/guide/keychain-access/create-an-identity-preference-kyca6343b6c9/mac)設定。

下列 Active Directory 同盟服務（ADFS）需求和考慮適用于：

* ADFS 伺服器必須啟用憑證驗證，並使用同盟驗證。
* 憑證必須使用增強金鑰使用方法（EKU），並在*主體別名（NT 主體名稱）* 中包含使用者的 UPN。

## <a name="configure-adfs"></a>設定 ADFS

若要讓 Azure AD 撤銷用戶端憑證，ADFS 權杖必須具有下列宣告。 Azure AD 會將這些宣告新增至可在 ADFS 權杖（或任何其他 SAML 權杖）中使用的重新整理權杖。 需要驗證重新整理權杖時，會使用此資訊來檢查撤銷：

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`-新增您用戶端憑證的序號
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`-新增用戶端憑證的簽發者字串

最佳做法是，您也應該使用下列資訊來更新組織的 ADFS 錯誤頁面：

* 在 iOS 上安裝 Microsoft Authenticator 的需求。
* 如何取得使用者憑證的指示。

如需詳細資訊，請參閱[自訂 AD FS 登入頁面](https://technet.microsoft.com/library/dn280950.aspx)。

## <a name="use-modern-authentication-with-office-apps"></a>搭配 Office 應用程式使用新式驗證

某些已啟用新式驗證的 Office 應用`prompt=login`程式會在其要求中傳送至 Azure AD。 根據預設，Azure AD `prompt=login`會將對 ADFS 的要求轉譯`wauth=usernamepassworduri`為（要求 Adfs 進行 U/P 驗證）和`wfresh=0` （要求 adfs 忽略 SSO 狀態並進行全新驗證）。 如果您想要為這些應用程式啟用以憑證為基礎的驗證，請修改預設的 Azure AD 行為。

若要更新預設行為，請將同盟網域設定中的 [*PromptLoginBehavior*] 設定為 [*停用*]。 您可以使用[set-msoldomainfederationsettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) Cmdlet 來執行這項工作，如下列範例所示：

```powershell
Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled
```

## <a name="support-for-exchange-activesync-clients"></a>支援 Exchange ActiveSync 用戶端

iOS 9 或更新版本支援原生 iOS 郵件用戶端。 若要判斷是否支援所有其他 Exchange ActiveSync 應用程式的這項功能，請洽詢您的應用程式開發人員。

## <a name="next-steps"></a>後續步驟

若要在您的環境中設定以憑證為基礎的驗證，請參閱[開始使用以憑證為基礎的驗證](active-directory-certificate-based-authentication-get-started.md)以取得指示。

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png

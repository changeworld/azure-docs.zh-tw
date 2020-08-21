---
title: IOS 上以憑證為基礎的驗證-Azure Active Directory
description: 瞭解支援的案例，以及在 iOS 裝置的解決方案中針對 Azure Active Directory 設定憑證型驗證的需求
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 584fb5c370fa0be629d057eb94dc4c2a8b9edc15
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2020
ms.locfileid: "88716430"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>iOS 上的 Azure Active Directory 憑證式驗證

為了改善安全性，iOS 裝置可以使用憑證型驗證 (CBA) ，以在連線到下列應用程式或服務時，在其裝置上使用用戶端憑證來驗證 Azure Active Directory (Azure AD) ：

* Office 行動應用程式，例如 Microsoft Outlook 與 Microsoft Word
* Exchange ActiveSync (EAS) 用戶端

使用憑證不需要在您的行動裝置上輸入使用者名稱和密碼組合，就能在特定郵件和 Microsoft Office 的應用程式中輸入。

本文詳述在 iOS 裝置上設定 CBA 的需求和支援案例。 適用于 iOS 的 CBA 適用于 Azure 公用雲端、Microsoft 政府雲端、Microsoft 雲端德國和 Microsoft Azure 中國世紀的各項功能。

## <a name="microsoft-mobile-applications-support"></a>Microsoft 行動應用程式支援

| 應用程式 | 支援 |
| --- | --- |
| Azure 資訊保護應用程式 |![核取記號表示此應用程式的支援][1] |
| Intune 公司入口網站 |![核取記號表示此應用程式的支援][1] |
| Microsoft Teams |![核取記號表示此應用程式的支援][1] |
| Office (mobile)  |![核取記號表示此應用程式的支援][1] |
| OneNote |![核取記號表示此應用程式的支援][1] |
| OneDrive |![核取記號表示此應用程式的支援][1] |
| Outlook |![核取記號表示此應用程式的支援][1] |
| Power BI |![核取記號表示此應用程式的支援][1] |
| 商務用 Skype |![核取記號表示此應用程式的支援][1] |
| Word / Excel / PowerPoint |![核取記號表示此應用程式的支援][1] |
| Yammer |![核取記號表示此應用程式的支援][1] |

## <a name="requirements"></a>需求

若要搭配使用 CBA 與 iOS，適用下列需求和考慮：

* 裝置作業系統版本必須是 iOS 9 或更新版本。
* iOS 上的 Office 應用程式都需要 Microsoft Authenticator。
* 您必須在 macOS Keychain 中建立身分識別喜好設定，其中包含 ADFS 伺服器的驗證 URL。 如需詳細資訊，請參閱 [在 Mac 的 Keychain 存取中建立身分識別喜好](https://support.apple.com/guide/keychain-access/create-an-identity-preference-kyca6343b6c9/mac)設定。

下列 Active Directory 同盟服務 (ADFS) 需求與考慮：

* ADFS 伺服器必須啟用憑證驗證並使用同盟驗證。
* 憑證必須使用增強金鑰使用方法 (EKU) ，並且在主體別名中包含使用者的 UPN * (NT 主體名稱) *。

## <a name="configure-adfs"></a>設定 ADFS

針對 Azure AD 撤銷用戶端憑證，ADFS 權杖必須有下列宣告。 Azure AD 將這些宣告新增至重新整理權杖（如果 ADFS 權杖中有提供） (或任何其他 SAML 權杖) 。 需要驗證重新整理權杖時，會使用此資訊來檢查撤銷：

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` -新增用戶端憑證的序號
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` -新增用戶端憑證的簽發者字串

最佳做法是，您也應該使用下列資訊來更新您組織的 ADFS 錯誤頁面：

* 在 iOS 上安裝 Microsoft Authenticator 的需求。
* 如何取得使用者憑證的指示。

如需詳細資訊，請參閱 [自訂 AD FS 登入頁面](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn280950(v=ws.11))。

## <a name="use-modern-authentication-with-office-apps"></a>使用 Office 應用程式的新式驗證

啟用新式驗證的某些 Office 應用程式會 `prompt=login` 在其要求中傳送至 Azure AD。 根據預設，Azure AD 會 `prompt=login` 將要求轉譯為 adfs，因為 `wauth=usernamepassworduri` (會要求 Adfs 進行 U/P 驗證) 並 `wfresh=0` (要求 adfs 忽略 SSO 狀態並進行全新驗證) 。 如果您想要為這些應用程式啟用以憑證為基礎的驗證，請修改預設的 Azure AD 行為。

若要更新預設行為，請將同盟網域設定中的 '*PromptLoginBehavior*' 設定為 *停用*。 您可以使用 [get-msoldomainfederationsettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) Cmdlet 來執行這項工作，如下列範例所示：

```powershell
Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled
```

## <a name="support-for-exchange-activesync-clients"></a>Exchange ActiveSync 用戶端的支援

iOS 9 或更新版本支援原生 iOS 郵件用戶端。 若要判斷所有其他 Exchange ActiveSync 應用程式是否支援這項功能，請洽詢您的應用程式開發人員。

## <a name="next-steps"></a>後續步驟

若要在您的環境中設定以憑證為基礎的驗證，請參閱以 [憑證為基礎的驗證入門](active-directory-certificate-based-authentication-get-started.md) 以取得指示。

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
---
title: iOS 上的驗證憑證的認證 ─ Azure 的動作目錄
description: 瞭解受支援的專案以及使用 iOS 裝置的解決方案中設定 Azure 活動目錄的驗證要求
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76c5e18a0bf84e96476eafd7ff35398049f1a492
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639621"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>iOS 上的 Azure Active Directory 憑證式驗證

為了提高安全性,iOS 設備在連接到以下應用程式或服務時,可以使用基於證書的身份驗證 (CBA) 使用其設備上的用戶端證書對 Azure 活動目錄 (Azure AD) 進行身份驗證:

* Office 行動應用程式，例如 Microsoft Outlook 與 Microsoft Word
* Exchange ActiveSync (EAS) 用戶端

使用證書無需在行動裝置上的某些郵件和Microsoft Office應用程式中輸入使用者名和密碼組合。

本文詳細介紹了在 iOS 設備上配置 CBA 的要求和支援的方案。 適用於 iOS 的 CBA 可在 Azure 公共雲、微軟政府雲、微軟雲德國和微軟 Azure 中國 21Vianet 之間使用。

## <a name="microsoft-mobile-applications-support"></a>Microsoft 行動應用程式支援

| 應用程式 | 支援 |
| --- | --- |
| Azure 資訊保護應用程式 |![標記表示對此應用程式的支援][1] |
| Intune 公司入口網站 |![標記表示對此應用程式的支援][1] |
| Microsoft Teams |![標記表示對此應用程式的支援][1] |
| OneNote |![標記表示對此應用程式的支援][1] |
| OneDrive |![標記表示對此應用程式的支援][1] |
| Outlook |![標記表示對此應用程式的支援][1] |
| Power BI |![標記表示對此應用程式的支援][1] |
| 商務用 Skype |![標記表示對此應用程式的支援][1] |
| Word / Excel / PowerPoint |![標記表示對此應用程式的支援][1] |
| Yammer |![標記表示對此應用程式的支援][1] |

## <a name="requirements"></a>需求

要將 CBA 與 iOS 一起使用,請執行以下要求和注意事項:

* 設備作業系統版本必須為 iOS 9 或以上。
* iOS 上的 Office 應用程式都需要 Microsoft Authenticator。
* 必須在包含 ADFS 伺服器身份驗證 URL 的 macOS 鑰匙串中創建標識首選項。 關於詳細資訊,請參閱在[Mac 上的鑰匙串存取建立識別首選項](https://support.apple.com/guide/keychain-access/create-an-identity-preference-kyca6343b6c9/mac)。

以下活動目錄來聯合服務 (ADFS) 要求與注意事項適用:

* 必須啟用 ADFS 伺服器進行憑證身份驗證並使用聯合身份驗證。
* 憑證必須使用增強金鑰使用 (EKU) 並在*主題替代名稱 (NT 主體名稱)* 中包含使用者的 UPN。

## <a name="configure-adfs"></a>設定 ADFS

對於 Azure AD 以撤銷用戶端證書,ADFS 權杖必須具有以下聲明。 Azure AD 如果這些聲明在 ADFS 權杖(或任何其他 SAML 權杖)中可用,則它們將這些聲明添加到刷新權杖中。 當需要驗證刷新權杖時,此資訊用於檢查吊銷:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`- 新增客戶端憑證的序列號
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`- 新增客戶端憑證簽發者字串

最佳做法是,您還應使用以下資訊更新組織的 ADFS 錯誤頁:

* 在 iOS 上安裝 Microsoft 身份驗證器的要求。
* 如何取得使用者憑證的指示。

有關詳細資訊,請參閱自訂[AD FS 登入頁](https://technet.microsoft.com/library/dn280950.aspx)。

## <a name="use-modern-authentication-with-office-apps"></a>將現代身份驗證與 Office 應用一起使用

啟用了新身份驗證的某些`prompt=login`Office 應用在其請求中發送到 Azure AD。 預設情況下,Azure AD`prompt=login`將 請求中轉換為`wauth=usernamepassworduri`ADFS( 要求 ADFS 執行`wfresh=0`U/P Auth)和 (要求 ADFS 忽略 SSO 狀態並執行新的身份驗證)。 如果要為這些應用啟用基於證書的身份驗證,請修改預設的 Azure AD 行為。

要更新預設行為,請將聯合域設置中的「*提示登錄行為*」設置為 *「已禁用*」。。 您可以使用[MSOLDomain 聯邦設定](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0)cmdlet 執行此工作,如以下範例所示:

```powershell
Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled
```

## <a name="support-for-exchange-activesync-clients"></a>支援 Exchange ActiveSync 用戶端

iOS 9 或更新版本支援原生 iOS 郵件用戶端。 要確定所有其他 Exchange ActiveSync 應用程式是否支援此功能,請與應用程式開發人員聯繫。

## <a name="next-steps"></a>後續步驟

要在環境中配置基於證書的身份驗證,請參閱[開始使用基於證書的身份驗證](active-directory-certificate-based-authentication-get-started.md)獲取說明。

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png

---
title: 預先填入連絡人資訊以進行自助式密碼重設-Azure Active Directory
description: 瞭解如何為 Azure Active Directory 自助式密碼重設 (SSPR) 的使用者預先填入連絡人資訊，讓他們可以使用此功能，而不需要完成註冊程式。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22037468c47de45513351e1d6126d3f3e5fccadc
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2020
ms.locfileid: "88717790"
---
# <a name="pre-populate-user-authentication-contact-information-for-azure-active-directory-self-service-password-reset-sspr"></a>預先填入使用者驗證連絡人資訊，以 Azure Active Directory 自助式密碼重設 (SSPR) 

若要使用 Azure Active Directory (Azure AD) 自助式密碼重設 (SSPR) ，使用者的驗證連絡人資訊必須存在。 有些組織會讓使用者自行註冊驗證資料。 其他組織則偏好從已經存在於 Active Directory Domain Services (AD DS) 中的驗證資料進行同步處理。 此同步處理的資料可供 Azure AD 和 SSPR，而不需要使用者互動。 當使用者需要變更或重設其密碼時，即使他們先前未註冊其連絡人資訊，也可以這麼做。

如果您符合下列需求，您可以預先填入驗證連絡人資訊：

* 您已正確格式化內部部署目錄中的資料。
* 您已為您的 Azure AD 租使用者設定 [Azure AD Connect](../hybrid/how-to-connect-install-express.md) 。

電話號碼的格式必須是 *+ CountryCode PhoneNumber*，例如 *+ 1 4251234567*。

> [!NOTE]
> 國家/地區代碼與電話號碼之間必須有一個空格。
>
> 密碼重設不支援電話分機。 即使採用 +1 4251234567X12345 格式，撥號之前都會移除分機號碼。

## <a name="fields-populated"></a>填入的欄位

如果您使用 Azure AD Connect 中的預設設定，則會進行下列對應，以填入 SSPR 的驗證連絡人資訊：

| 內部部署 Active Directory | Azure AD     |
|------------------------------|--------------|
| telephoneNumber              | 辦公室電話 |
| mobile                       | 行動電話 |

使用者確認其行動電話號碼之後，Azure AD 中 [**驗證連絡人資訊**] 下的 [*電話*] 欄位也會填入該號碼。

## <a name="authentication-contact-info"></a>驗證聯絡資訊

在 Azure 入口網站中 Azure AD 使用者的 [ **驗證方法** ] 頁面上，全域管理員可以手動設定驗證連絡人資訊，如下列範例螢幕擷取畫面所示：

![Azure AD 中的使用者驗證連絡人資訊][Contact]

下列考慮適用于此驗證連絡人資訊：

* 如果 [ *電話* ] 欄位已填入，且已在 SSPR 原則中啟用 *行動電話* ，則使用者會在密碼重設註冊頁面上及密碼重設工作流程期間看到該號碼。
* *替代電話*欄位不會用來重設密碼。
* 如果 [ *電子郵件* ] 欄位已填入，且已在 SSPR 原則中啟用 *電子郵件* ，則使用者會在密碼重設註冊頁面上及密碼重設工作流程期間看到該電子郵件。
* 如果 [ *替代電子郵件* ] 欄位已填入，且 SSPR 原則中的 *電子郵件* 已啟用，則使用者將不會在密碼重設註冊頁面上看到該電子郵件，但會在密碼重設工作流程中看到此電子郵件。

## <a name="security-questions-and-answers"></a>安全性問題和答案

安全性問題和答案會安全地儲存在 Azure AD 租用戶中，且僅供使用者透過 [SSPR 註冊入口網站](https://aka.ms/ssprsetup)存取。 系統管理員無法看到、設定或修改其他使用者的問題和答案內容。

## <a name="what-happens-when-a-user-registers"></a>使用者註冊時的情況

當使用者註冊時，註冊頁面會設定下列欄位：

* **驗證電話**
* **驗證電子郵件**
* **安全性問題和答案**

如果您提供 *行動電話* 或 *備用電子郵件*的值，使用者可以立即使用這些值來重設其密碼，即使他們尚未註冊服務也一樣。

使用者在第一次註冊時，也會看到這些值，如果需要，也可以修改它們。 成功註冊之後，這些值會分別保存在 [ *驗證電話* ] 和 [ *驗證電子郵件* ] 欄位中。

## <a name="set-and-read-the-authentication-data-through-powershell"></a>透過 PowerShell 設定和讀取驗證資料

以下是可透過 PowerShell 設定的欄位：

* *替代電子郵件*
* *行動電話*
* *辦公室電話*
    * 只有在您未與內部部署目錄進行同步處理時，才能設定。

> [!IMPORTANT]
> PowerShell v1 與 PowerShell v2 之間的命令功能有已知的同位檢查。 [驗證方法的 Microsoft Graph REST API (搶鮮版（Beta）) ](/graph/api/resources/authenticationmethods-overview)是提供新式互動的目前工程重心。

### <a name="use-powershell-version-1"></a>使用 PowerShell 第 1 版

若要開始使用，請 [下載並安裝 Azure AD PowerShell 模組](/previous-versions/azure/jj151815(v=azure.100)#bkmk_installmodule)。 安裝之後，請使用下列步驟來設定每個欄位。

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>使用 PowerShell 第 1 版來設定驗證資料

```PowerShell
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 4251234567"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 4252345678"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 4251234567" -PhoneNumber "+1 4252345678"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>使用 PowerShell 第 1 版來讀取驗證資料

```PowerShell
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>讀取 [驗證電話] 和 [驗證電子郵件] 選項

使用 PowerShell 第 1 版時，若要讀取 [驗證電話]**** 和 [驗證電子郵件]****，請使用下列命令：

```PowerShell
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>使用 PowerShell 第 2 版

若要開始使用，請 [下載並安裝 Azure AD 第2版 PowerShell 模組](/powershell/module/azuread/?view=azureadps-2.0)。

若要從支援的最新 PowerShell 版本快速安裝 `Install-Module` ，請執行下列命令。 第一行會檢查是否已安裝模組：

```PowerShell
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

安裝模組之後，請使用下列步驟來設定每個欄位。

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>使用 PowerShell 第 2 版來設定驗證資料

```PowerShell
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 4251234567"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 4252345678"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 4251234567" -TelephoneNumber "+1 4252345678"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>使用 PowerShell 第 2 版來讀取驗證資料

```PowerShell
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>後續步驟

為使用者預先填入驗證連絡人資訊之後，請完成下列教學課程以啟用自助式密碼重設：

> [!div class="nextstepaction"]
> [啟用 Azure AD 自助式密碼重設](tutorial-enable-sspr.md)

[Contact]: ./media/howto-sspr-authenticationdata/user-authentication-contact-info.png "全域系統管理員可以修改使用者的驗證連絡人資訊"
---
title: Azure AD SSPR 資料要求 - Azure 活動目錄
description: Azure AD 自助式密碼重設的資料需求和如何滿足這些需求
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 12/09/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42f7e120745357d3bd5735cca568bdd6971ea061
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652365"
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>部署密碼重設而不需要使用者註冊

若要部署 Azure Active Directory (Azure AD) 自助式密碼重設 (SSPR)，必須要有驗證資料。 有些組織會讓其使用者自行輸入自己的驗證資料。 其他組織更喜歡與活動目錄中已存在的數據同步。 如果滿足以下要求,此同步數據可供 Azure AD 和 SSPR 使用,而無需使用者交互:

* 將您內部部署目錄中的資料正確格式化。
* [使用快速設定來設定 Azure AD Connect](../hybrid/how-to-connect-install-express.md)。

為了正確運作，電話號碼的格式必須是：+國碼 電話號碼**，例如 +1 4255551234。

> [!NOTE]
> 國碼 (地區碼) 和電話號碼之間需要空格。
>
> 密碼重設不支援電話分機。 即使是 +1 4255551234X12345 格式，撥號之前都會移除分機號碼。

## <a name="fields-populated"></a>填入的欄位

如果您使用 Azure AD Connect 的預設設定，將會建立下列對應：

| 內部部署 Active Directory | Azure AD |
| --- | --- |
| telephoneNumber | 辦公室電話 |
| mobile | 行動電話 |

使用者驗證其手機號碼後,Azure AD 中的 **「身份驗證聯絡資訊**」下的 *「電話」* 欄位也會填充該號碼。

## <a name="authentication-contact-info"></a>驗證聯絡資訊

在 Azure 門戶中的 Azure AD 使用者的**認證方法**頁上,全域管理員可以手動設定身份驗證聯絡資訊,如以下範例螢幕截圖所示:

![在 Azure AD 中驗證使用者的聯絡資訊][Contact]

* 如果在 SSPR 策略中填充了 **「電話」** 欄位並啟用了**行動電話**,則使用者在密碼重置註冊頁上和密碼重置工作流期間看到該號碼。
* **備用電話**欄位不用於密碼重置。
* 如果電子**郵件**「欄位已填充,並在 SSPR 策略中啟用**了電子郵件**,則使用者在密碼重置註冊頁上和密碼重置工作流期間看到該電子郵件。
* 如果填充了 **「備用電子郵件**」欄位並在 SSPR 策略中啟用**了電子郵件**,則使用者**不會在**密碼重置註冊頁上看到該電子郵件,但他們在密碼重置工作流期間會看到該電子郵件。

## <a name="security-questions-and-answers"></a>安全性問題和答案

安全性問題和答案會安全地儲存在 Azure AD 租用戶中，且僅供使用者透過 [SSPR 註冊入口網站](https://aka.ms/ssprsetup)存取。 系統管理員無法看到、設定或修改其他使用者的問題和答案內容。

## <a name="what-happens-when-a-user-registers"></a>使用者註冊時的情況

當使用者註冊時，註冊頁面會設定下列欄位：

* **認證電話**
* **驗證電子郵件**
* **安全性問題和答案**

如果您為**行動電話**或**備用電子郵件**提供了值,則用戶可以立即使用這些值重置其密碼,即使他們尚未註冊該服務。 此外，使用者會在第一次註冊時看到這些值，並可視需要加以修改。 成功註冊後,這些值將分別保留在 **「身份驗證電話**」和「**身份驗證電子郵件**」欄位中。

## <a name="set-and-read-the-authentication-data-through-powershell"></a>透過 PowerShell 設定和讀取驗證資料

以下是可透過 PowerShell 設定的欄位：

* **備用電子郵件**
* **行動電話**
* **辦公室電話**：只有在未與內部部署目錄同步處理時才能設定

### <a name="use-powershell-version-1"></a>使用 PowerShell 第 1 版

首先，您必須[下載並安裝 Azure AD PowerShell 模組](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule)。 安裝該模組之後，即可使用下列步驟來設定每個欄位。

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>使用 PowerShell 第 1 版來設定驗證資料

```PowerShell
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
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

若要開始進行，您必須[下載並安裝 Azure AD 第 2 版 PowerShell 模組](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0) \(英文\)。 安裝該模組之後，即可使用下列步驟來設定每個欄位。

若要從支援 Install-Module 的最新 PowerShell 版本快速安裝，請執行下列命令。 (第一行會檢查是否已經安裝模組)。

```PowerShell
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>使用 PowerShell 第 2 版來設定驗證資料

```PowerShell
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
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

* [如何完成 SSPR 成功首度發行？](howto-sspr-deployment.md)
* [重設或變更密碼](../user-help/active-directory-passwords-update-your-own-password.md)
* [註冊進行自助服務密碼重置](../user-help/active-directory-passwords-reset-register.md)
* [您是否有許可問題?](concept-sspr-licensing.md)
* [哪些驗證方法可供使用者使用？](concept-sspr-howitworks.md#authentication-methods)
* [使用 SSPR 的原則選項有哪些？](concept-sspr-policy.md)
* [什麼是密碼回寫，且為什麼我需要了解它？](howto-sspr-writeback.md)
* [如何回報 SSPR 中的活動？](howto-sspr-reporting.md)
* [SSPR 中的所有選項有哪些，以及它們有何意義？](concept-sspr-howitworks.md)
* [我覺得有些東西壞了。如何對 SSPR 進行故障排除?](active-directory-passwords-troubleshoot.md)
* [在其他某處並未涵蓋我的問題](active-directory-passwords-faq.md)

[Contact]: ./media/howto-sspr-authenticationdata/user-authentication-contact-info.png "全域系統管理員可以修改使用者的驗證連絡人資訊"

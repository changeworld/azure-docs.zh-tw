---
title: 自助式密碼重設原則 - Azure Active Directory
description: 瞭解不同的 Azure Active Directory 自助式密碼重設原則選項
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/27/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: b0684735b32e03abe525b19dce6d9d887afe513b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84194071"
---
# <a name="password-policies-and-account-restrictions-in-azure-active-directory"></a>Azure Active Directory 中的密碼原則和帳戶限制

在 Azure Active Directory （Azure AD）中，有一個密碼原則會定義密碼複雜性、長度或年齡等設定。 另外還有一個原則，定義可接受的字元和使用者名稱的長度。

使用自助式密碼重設（SSPR）在 Azure AD 中變更或重設密碼時，會檢查密碼原則。 如果密碼不符合原則需求，則會提示使用者再試一次。 Azure 系統管理員對於使用與一般使用者帳戶不同的 SSPR 有一些限制。

本文說明與 Azure AD 租使用者中的使用者帳戶相關聯的密碼原則設定和複雜性需求，以及您可以如何使用 PowerShell 來檢查或設定密碼到期設定。

## <a name="username-policies"></a><a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>使用者名稱原則

每個登入 Azure AD 的帳戶都必須具有與其帳戶相關聯的唯一使用者主體名稱（UPN）屬性值。 在內部部署 Active Directory Domain Services （AD DS）環境與使用 Azure AD Connect 同步處理 Azure AD 的混合式環境中，根據預設，Azure AD UPN 會設定為內部部署 UPN。

下表概述適用于內部部署 AD DS 帳戶的使用者名稱原則，同步處理到 Azure AD，以及直接在 Azure AD 中建立的僅限雲端使用者帳戶：

| 屬性 | UserPrincipalName 需求 |
| --- | --- |
| 允許的字元 |<ul> <li>A – Z</li> <li>a - z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| 不允許的字元 |<ul> <li>任何不是用來分隔使用者名稱和網域的 "\@\" 字元。</li> <li>"\@\" 符號前面不可包含緊鄰的句點字元 "."</li></ul> |
| 長度限制 |<ul> <li>總長度不得超過 113 個字元</li><li>"\@\" 符號前面最多可以有 64 個字元</li><li>"\@\" 符號後面最多可以有 48 個字元</li></ul> |

## <a name="azure-ad-password-policies"></a><a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Azure AD 密碼原則

密碼原則會套用至直接在 Azure AD 中建立和管理的所有使用者帳戶。 此密碼原則無法修改，不過您可以[設定自訂的禁用密碼以 Azure AD 密碼保護](tutorial-configure-custom-password-protection.md)。

密碼原則不適用於使用 Azure AD Connect 從內部部署 AD DS 環境同步處理的使用者帳戶。

下列是已定義的密碼原則選項：

| 屬性 | 規格需求 |
| --- | --- |
| 允許的字元 |<ul><li>A – Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [] {} &#124; \： '，。 ? / \`~ " ( ) ;</li> <li>空格</li></ul> |
| 不允許的字元 | Unicode 字元。 |
| 密碼限制 |<ul><li>最少8個字元，最多256個字元。</li><li>需要下列四種字元中的三種：<ul><li>小寫字元。</li><li>大寫字元。</li><li>數字 (0-9)。</li><li>符號 (請參閱先前的密碼限制)。</li></ul></li></ul> |
| 密碼到期期限（密碼最長使用期限） |<ul><li>預設值： **90**天。</li><li>可透過適用於 Windows PowerShell 的 Azure Active Directory 模組使用 `Set-MsolPasswordPolicy` Cmdlet 設定該值。</li></ul> |
| 密碼到期通知（當使用者收到密碼到期的通知時） |<ul><li>預設值： **14**天（密碼到期之前）。</li><li>可使用 `Set-MsolPasswordPolicy` Cmdlet 設定此值。</li></ul> |
| 密碼到期（讓密碼永不過期） |<ul><li>預設值： **false** （表示密碼的到期日）。</li><li>可以使用 `Set-MsolUser` Cmdlet 針對個別使用者帳戶設定該值。</li></ul> |
| 密碼變更歷程記錄 | 當使用者變更密碼時，*不能*重複使用上次的密碼。 |
| 密碼重設歷程記錄 | 當使用者重設忘記密碼時，*不能*重複使用上次的密碼。 |
| 帳戶鎖定 | 使用錯誤的密碼於 10 次失敗的登入嘗試之後，使用者會被封鎖一分鐘。 後續錯誤的登入嘗試會增加使用者被封鎖的時間。 [智慧鎖定](howto-password-smart-lockout.md)會追蹤最後三個不正確的密碼雜湊，以避免因為相同密碼而累計鎖定計數器。 如果有人多次輸入相同的錯誤密碼，此行為將不會造成帳戶鎖定。 |

## <a name="administrator-reset-policy-differences"></a>系統管理員重設原則差異

Microsoft 會針對任何 Azure 系統管理員角色強制執行強*式預設雙閘道*密碼重設原則。 此原則可能與您為使用者定義的原則不同，且無法變更此原則。 請一律要以未指派任何 Azure 系統管理員角色的使用者身分測試密碼重設功能。

利用兩個閘道原則，系統管理員不需有能力使用安全性問題。

兩個閘道原則需要兩項驗證資料，例如電子郵件地址、驗證器應用程式或電話號碼。 兩個閘道原則適用於下列情況：

* 下列 Azure 系統管理員角色會受到影響：
  * 服務台管理員
  * 服務支援管理員
  * 計費管理員
  * 合作夥伴第 1 層支援
  * 合作夥伴第 2 層支援
  * Exchange 系統管理員
  * 商務用 Skype 的管理員
  * 使用者管理員
  * 目錄寫入器
  * 全域管理員或公司系統管理員
  * SharePoint 管理員
  * 規範管理員
  * 應用程式管理員
  * 安全性系統管理員
  * 特殊權限角色管理員
  * Intune 管理員
  * 應用程式 Proxy 服務管理員
  * Dynamics 365 管理員
  * Power BI 服務管理員
  * 驗證系統管理員
  * 具有特殊許可權的驗證管理員

* 如果試用版訂用帳戶已經過 30 天；或
* 已為您的 Azure AD 租使用者設定自訂網域，例如*contoso.com*;或
* Azure AD Connect 正在同步處理內部部署目錄中的身分識別

### <a name="exceptions"></a>例外狀況

一個閘道原則需要一項驗證資料，例如電子郵件地址或電話號碼。 一個閘道原則適用於下列情況：

* 處於試用訂用帳戶的第一個 30 天內；或
* 尚未為您的 Azure AD 租使用者設定自訂網域，因此會使用預設的 **. onmicrosoft.com*。 不建議在生產環境中使用預設的 **. onmicrosoft.com*網域;和
* Azure AD Connect 未同步身分識別

## <a name="password-expiration-policies"></a><a name="set-password-expiration-policies-in-azure-ad"></a>密碼到期原則

*全域管理員*或*使用者系統管理員*可以使用[適用於 Windows PowerShell 的 Microsoft Azure AD 模組](/powershell/module/Azuread/?view=azureadps-2.0)將使用者密碼設定為不過期。

您也可以使用 PowerShell Cmdlet 移除永不過期的設定，或查看哪些使用者密碼設為永不過期。

本指引適用於其他提供者 (例如 Intune 和 Office 365)，它們也仰賴 Azure AD 提供身分識別和目錄服務。 密碼到期是原則中唯一可變更的部分。

> [!NOTE]
> 只有未透過 Azure AD Connect 同步處理的使用者帳戶密碼可以設定為不過期。 如需目錄同步作業的詳細資訊，請參閱 [Connect AD 與 Azure AD](../hybrid/whatis-hybrid-identity.md)。

### <a name="set-or-check-the-password-policies-by-using-powershell"></a>使用 PowerShell 設定或檢查密碼原則

若要開始使用，請[下載並安裝 Azure AD PowerShell 模組](/powershell/module/Azuread/?view=azureadps-2.0)，並[將它連線到您的 Azure AD 租使用者](/powershell/module/azuread/connect-azuread?view=azureadps-2.0#examples)。

安裝模組之後，請使用下列步驟，視需要完成每項工作。

### <a name="check-the-expiration-policy-for-a-password"></a>檢查密碼的到期原則

1. 開啟 PowerShell 提示字元，並使用*全域管理員*或*使用者系統管理員*帳戶[連接到您的 Azure AD 租](/powershell/module/azuread/connect-azuread?view=azureadps-2.0#examples)使用者。
1. 針對個別使用者或所有使用者執行下列其中一個命令：

   * 若要查看單一使用者的密碼是否設定為永不過期，請執行下列 Cmdlet。 將取代為 `<user ID>` 您想要檢查之使用者的使用者識別碼，例如*driley \@ contoso.onmicrosoft.com*：

       ```powershell
       Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
       ```

   * 若要查看所有使用者的**密碼永久有效**設定，請執行下列 Cmdlet：

       ```powershell
       Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
       ```

### <a name="set-a-password-to-expire"></a>設定密碼到期

1. 開啟 PowerShell 提示字元，並使用*全域管理員*或*使用者系統管理員*帳戶[連接到您的 Azure AD 租](/powershell/module/azuread/connect-azuread?view=azureadps-2.0#examples)使用者。
1. 針對個別使用者或所有使用者執行下列其中一個命令：

   * 若要設定某位使用者的密碼，使密碼過期，請執行下列 Cmdlet。 將取代為 `<user ID>` 您想要檢查之使用者的使用者識別碼，例如*driley \@ contoso.onmicrosoft.com*

       ```powershell
       Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
       ```

   * 若要將組織中所有使用者的密碼設為會到期，請使用下列 Cmdlet：

       ```powershell
       Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
       ```

### <a name="set-a-password-to-never-expire"></a>設定密碼為永久有效

1. 開啟 PowerShell 提示字元，並使用*全域管理員*或*使用者系統管理員*帳戶[連接到您的 Azure AD 租](/powershell/module/azuread/connect-azuread?view=azureadps-2.0#examples)使用者。
1. 針對個別使用者或所有使用者執行下列其中一個命令：

   * 若要將某位使用者的密碼設定為永不過期，請執行下列 Cmdlet。 將取代為 `<user ID>` 您想要檢查之使用者的使用者識別碼，例如*driley \@ contoso.onmicrosoft.com*

       ```powershell
       Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration
       ```

   * 若要將組織中所有使用者的密碼設定為永不過期，請執行下列指令程式：

       ```powershell
       Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration
       ```

   > [!WARNING]
   > 設定為 `-PasswordPolicies DisablePasswordExpiration` 的密碼仍然會根據 `pwdLastSet` 屬性計算時效。 根據 `pwdLastSet` 屬性，如果您將到期變更為 `-PasswordPolicies None`，`pwdLastSet` 早於 90 天的所有密碼會要求使用者在下次登入時變更密碼。 這項變更可能會影響大量使用者。

## <a name="next-steps"></a>後續步驟

若要開始使用 SSPR，請參閱[教學課程：讓使用者使用 Azure Active Directory 自助式密碼重設來解除鎖定其帳戶或重設密碼](tutorial-enable-sspr.md)。

如果您或使用者有 SSPR 的問題，請參閱針對[自助式密碼重設進行疑難排解](active-directory-passwords-troubleshoot.md)

---
title: 自助式密碼重設原則 - Azure Active Directory
description: 瞭解不同 Azure Active Directory 自助式密碼重設原則選項
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: 80be5ca22f3dfb673f09327108e66fccc9de6ddd
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98918038"
---
# <a name="password-policies-and-account-restrictions-in-azure-active-directory"></a>Azure Active Directory 中的密碼原則和帳戶限制

在 Azure Active Directory (Azure AD) 中，有一個密碼原則定義了密碼複雜性、長度或年齡等設定。 此外，也有原則可定義使用者名稱的可接受字元和長度。

當自助式密碼重設 (SSPR) 用來在 Azure AD 中變更或重設密碼時，會檢查密碼原則。 如果密碼不符合原則需求，則會提示使用者再試一次。 Azure 系統管理員對使用與一般使用者帳戶不同的 SSPR 有一些限制。

本文說明與 Azure AD 租使用者中的使用者帳戶相關聯的密碼原則設定和複雜性需求，以及您可以如何使用 PowerShell 來檢查或設定密碼到期設定。

## <a name="username-policies"></a><a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>使用者名稱原則

登入 Azure AD 的每個帳戶都必須有唯一的使用者主體名稱 (UPN) 與其帳戶相關聯的屬性值。 在具有內部部署 Active Directory Domain Services (AD DS) 環境使用 Azure AD 同步至 Azure AD Connect 的混合式環境中，根據預設，Azure AD UPN 會設定為內部部署 UPN。

下表概述適用于同步處理至 Azure AD 的內部部署 AD DS 帳戶，以及直接在 Azure AD 中建立之僅限雲端使用者帳戶的使用者名稱原則：

| 屬性 | UserPrincipalName 需求 |
| --- | --- |
| 允許的字元 |<ul> <li>A – Z</li> <li>a - z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| 不允許的字元 |<ul> <li>任何不是用來分隔使用者名稱和網域的 "\@\" 字元。</li> <li>"\@\" 符號前面不可包含緊鄰的句點字元 "."</li></ul> |
| 長度限制 |<ul> <li>總長度不得超過 113 個字元</li><li>"\@\" 符號前面最多可以有 64 個字元</li><li>"\@\" 符號後面最多可以有 48 個字元</li></ul> |

## <a name="azure-ad-password-policies"></a><a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Azure AD 密碼原則

密碼原則會套用至直接在 Azure AD 中建立和管理的所有使用者帳戶。 您可以設定 Azure AD 密碼保護或帳戶鎖定參數 [的自訂禁用密碼](tutorial-configure-custom-password-protection.md) ，但無法修改其中某些密碼原則設定。

依預設，帳戶會在10次失敗的登入嘗試次數錯誤的情況下鎖定。 使用者會被鎖定一分鐘。 後續錯誤的登入嘗試會增加使用者被封鎖的時間。 [智慧鎖定](howto-password-smart-lockout.md)會追蹤最後三個不正確的密碼雜湊，以避免因為相同密碼而累計鎖定計數器。 如果有人多次輸入相同的錯誤密碼，此行為不會造成帳戶鎖定。您可以定義智慧鎖定閾值和持續時間。

除非您啟用 *EnforceCloudPasswordPolicyForPasswordSyncedUsers*，否則不會將 Azure AD 密碼原則套用至使用 Azure AD Connect 從內部部署 AD DS 環境同步處理的使用者帳戶。

已定義下列 Azure AD 密碼原則選項。 除非另有說明，否則您無法變更這些設定：

| 屬性 | 需求 |
| --- | --- |
| 允許的字元 |<ul><li>A – Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [] {} &#124; \： '，。 ? / \` ~ " ( ) ;</li> <li>空白</li></ul> |
| 不允許的字元 | Unicode 字元。 |
| 密碼限制 |<ul><li>最少8個字元，最多256個字元。</li><li>需要下列四種字元中的三種：<ul><li>小寫字元。</li><li>大寫字元。</li><li>數字 (0-9)。</li><li>符號 (請參閱先前的密碼限制)。</li></ul></li></ul> |
| 密碼到期期間 (密碼最長使用期限)  |<ul><li>預設值： **90** 天。</li><li>可透過適用於 Windows PowerShell 的 Azure Active Directory 模組使用 `Set-MsolPasswordPolicy` Cmdlet 設定該值。</li></ul> |
| 通知使用者密碼到期時的密碼到期通知 ()  |<ul><li>預設值：在密碼到期前 **14** 天 () 。</li><li>可使用 `Set-MsolPasswordPolicy` Cmdlet 設定此值。</li></ul> |
| 密碼到期 (讓密碼永遠不會過期)  |<ul><li>預設值： **false** (表示密碼的到期日) 。</li><li>可以使用 `Set-MsolUser` Cmdlet 針對個別使用者帳戶設定該值。</li></ul> |
| 密碼變更歷程記錄 | 當使用者變更密碼時，*不能* 重複使用上次的密碼。 |
| 密碼重設歷程記錄 | 當使用者重設忘記密碼時，*不能* 重複使用上次的密碼。 |

## <a name="administrator-reset-policy-differences"></a>系統管理員重設原則差異

根據預設，系統會針對自助式密碼重設啟用系統管理員帳戶，並強制執行強式預設 *雙閘道* 密碼重設原則。 這項原則可能與您為使用者所定義的原則不同，且此原則無法變更。 請一律要以未指派任何 Azure 系統管理員角色的使用者身分測試密碼重設功能。

利用兩個閘道原則，系統管理員不需有能力使用安全性問題。

兩個閘道原則需要兩項驗證資料，例如電子郵件地址、驗證器應用程式或電話號碼。 兩個閘道原則適用於下列情況：

* 下列 Azure 系統管理員角色會受到影響：
  * 服務台管理員
  * 服務支援管理員
  * 計費管理員
  * 合作夥伴第 1 層支援
  * 合作夥伴第 2 層支援
  * Exchange 系統管理員
  * 信箱管理員
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
  * Azure AD 加入的裝置本機系統管理員
  * 應用程式 Proxy 服務管理員
  * Dynamics 365 管理員
  * Power BI 服務管理員
  * 驗證系統管理員
  * 密碼管理員
  * 特殊許可權驗證管理員

* 如果試用版訂用帳戶已經過 30 天；或
* 已針對您的 Azure AD 租使用者設定自訂網域，例如 *contoso.com*;或
* Azure AD Connect 正在同步處理內部部署目錄中的身分識別

您可以使用 [Set-msolcompanysettings](/powershell/module/msonline/set-msolcompanysettings) PowerShell Cmdlet 來停用 SSPR 的系統管理員帳戶。 參數會停用系統 `-SelfServePasswordResetEnabled $False` 管理員的 SSPR。

### <a name="exceptions"></a>例外狀況

一個閘道原則需要一項驗證資料，例如電子郵件地址或電話號碼。 一個閘道原則適用於下列情況：

* 處於試用訂用帳戶的第一個 30 天內；或
* 尚未針對您的 Azure AD 租使用者設定自訂網域，因此會使用預設的 **. onmicrosoft.com*。 預設的 **. onmicrosoft.com* 網域不建議用於實際執行環境;和
* Azure AD Connect 未同步身分識別

## <a name="password-expiration-policies"></a><a name="set-password-expiration-policies-in-azure-ad"></a>密碼到期原則

*全域管理員* 或 *使用者系統管理員* 可以使用 [適用於 Windows PowerShell 的 Microsoft Azure AD 模組](/powershell/module/Azuread/)，將使用者密碼設為不會過期。

您也可以使用 PowerShell Cmdlet 來移除永不過期的設定，或查看哪些使用者密碼設定為永不過期。

本指引適用于其他提供者（例如 Intune 和 Microsoft 365），這也依賴身分識別和目錄服務的 Azure AD。 密碼到期是原則中唯一可變更的部分。

> [!NOTE]
> 只有未透過 Azure AD Connect 同步處理的使用者帳戶密碼，才可設定為不會過期。 如需目錄同步作業的詳細資訊，請參閱 [Connect AD 與 Azure AD](../hybrid/whatis-hybrid-identity.md)。

### <a name="set-or-check-the-password-policies-by-using-powershell"></a>使用 PowerShell 設定或檢查密碼原則

若要開始使用，請 [下載並安裝 Azure AD PowerShell 模組](/powershell/module/Azuread/) ，並 [將它連線到您的 Azure AD 租使用者](/powershell/module/azuread/connect-azuread#examples)。

安裝模組之後，請使用下列步驟，視需要完成每項工作。

### <a name="check-the-expiration-policy-for-a-password"></a>檢查密碼的到期原則

1. 開啟 PowerShell 提示字元，並使用 *全域管理員* 或 *使用者系統管理員* 帳戶 [連接到您的 Azure AD 租](/powershell/module/azuread/connect-azuread#examples)使用者。
1. 針對個別使用者或所有使用者，執行下列其中一個命令：

   * 若要查看單一使用者的密碼是否設定為永不過期，請執行下列 Cmdlet。 取代 `<user ID>` 為您想要檢查之使用者的使用者識別碼，例如 *driley \@ contoso.onmicrosoft.com*：

       ```powershell
       Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
       ```

   * 若要查看所有使用者的 **密碼永久有效** 設定，請執行下列 Cmdlet：

       ```powershell
       Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
       ```

### <a name="set-a-password-to-expire"></a>設定密碼到期

1. 開啟 PowerShell 提示字元，並使用 *全域管理員* 或 *使用者系統管理員* 帳戶 [連接到您的 Azure AD 租](/powershell/module/azuread/connect-azuread#examples)使用者。
1. 針對個別使用者或所有使用者，執行下列其中一個命令：

   * 若要設定某位使用者的密碼，讓密碼過期，請執行下列 Cmdlet。 取代 `<user ID>` 為您想要檢查之使用者的使用者識別碼，例如 *driley \@ contoso.onmicrosoft.com*

       ```powershell
       Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
       ```

   * 若要將組織中所有使用者的密碼設為會到期，請使用下列 Cmdlet：

       ```powershell
       Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
       ```

### <a name="set-a-password-to-never-expire"></a>設定密碼為永久有效

1. 開啟 PowerShell 提示字元，並使用 *全域管理員* 或 *使用者系統管理員* 帳戶 [連接到您的 Azure AD 租](/powershell/module/azuread/connect-azuread#examples)使用者。
1. 針對個別使用者或所有使用者，執行下列其中一個命令：

   * 若要將某位使用者的密碼設定為永不過期，請執行下列 Cmdlet。 取代 `<user ID>` 為您想要檢查之使用者的使用者識別碼，例如 *driley \@ contoso.onmicrosoft.com*

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

若要開始使用 SSPR，請參閱 [教學課程：讓使用者使用 Azure Active Directory 自助式密碼重設來解除鎖定其帳戶或重設密碼](tutorial-enable-sspr.md)。

如果您或使用者遇到 SSPR 的問題，請參閱 [疑難排解自助式密碼重設](./troubleshoot-sspr.md)

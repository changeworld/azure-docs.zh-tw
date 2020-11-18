---
title: 啟用個別使用者的 Multi-Factor Authentication - Azure Active Directory
description: 瞭解如何藉由變更使用者狀態來啟用每位使用者 Azure AD Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 08/17/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 240fb04068f255128f33e79748762305e4d6b704
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94838769"
---
# <a name="enable-per-user-azure-ad-multi-factor-authentication-to-secure-sign-in-events"></a>啟用每個使用者 Azure AD Multi-Factor Authentication 來保護登入事件

若要保護 Azure AD 中的使用者登入事件，您可以要求 (MFA) 的多重要素驗證。 使用條件式存取原則來啟用 Azure AD Multi-Factor Authentication，是保護使用者的建議方法。 條件式存取是 Azure AD Premium P1 或 P2 功能，可讓您套用規則，以在某些情況下需要 MFA。 若要開始使用條件式存取，請參閱 [教學課程：使用 Azure AD Multi-Factor Authentication 保護使用者登入事件](tutorial-enable-azure-mfa.md)。

針對 Azure AD 免費的租使用者，而不使用條件式存取，您可以 [使用安全性預設值保護使用者](../fundamentals/concept-fundamentals-security-defaults.md)。 系統會視需要提示使用者進行 MFA，但您無法定義自己的規則來控制行為。

如有需要，您可以改為針對每個使用者 Azure AD Multi-Factor Authentication 啟用每個帳戶。 當使用者個別啟用時，他們會在每次登入時執行多重要素驗證 (但有一些例外狀況，例如當他們從受信任的 IP 位址登入時，或開啟了 [在 _信任的裝置上記住 MFA_ ] 功能時) 。

除非您的 Azure AD 授權未包含條件式存取，而且您不想要使用安全性預設值，否則不建議變更使用者狀態。 如需有關啟用 MFA 之不同方式的詳細資訊，請參閱 [Azure AD Multi-Factor Authentication 的功能和授權](concept-mfa-licensing.md)。

> [!IMPORTANT]
>
> 本文將詳細說明如何查看和變更每個使用者 Azure AD Multi-Factor Authentication 的狀態。 如果您使用條件式存取或安全性預設值，則不會使用這些步驟來檢查或啟用使用者帳戶。
>
> 透過條件式存取原則啟用 Azure AD Multi-Factor Authentication 不會變更使用者的狀態。 如果使用者顯示為已停用，請不要驚慌。 條件式存取不會變更狀態。
>
> **如果您使用條件式存取原則，請不要啟用或強制執行每個使用者 Azure AD Multi-Factor Authentication。**

## <a name="azure-ad-multi-factor-authentication-user-states"></a>Azure AD Multi-Factor Authentication 使用者狀態

使用者的狀態會反映系統管理員是否已在每位使用者的 Azure AD Multi-Factor Authentication 中註冊它們。 Azure AD Multi-Factor Authentication 中的使用者帳戶具有下列三種不同的狀態：

| State | 描述 | 受影響的舊版驗證 | 受影響的瀏覽器應用程式 | 受影響的新式驗證 |
|:---:| --- |:---:|:--:|:--:|
| 已停用 | 未在每位使用者 Azure AD Multi-Factor Authentication 註冊之使用者的預設狀態。 | 否 | 否 | 否 |
| 啟用 | 使用者會在每個使用者 Azure AD Multi-Factor Authentication 中註冊，但仍可使用其密碼進行舊版驗證。 如果使用者尚未註冊 MFA 驗證方法，則會在下一次使用新式驗證 (（例如透過網頁瀏覽器) ）登入時收到註冊的提示。 | 否。 舊版驗證會繼續運作，直到註冊程式完成為止。 | 是。 會話到期之後，需要 Azure AD Multi-Factor Authentication 註冊。| 是。 存取權杖到期之後，需要 Azure AD Multi-Factor Authentication 註冊。 |
| 已強制 | 使用者會在 Azure AD Multi-Factor Authentication 中向每位使用者註冊。 如果使用者尚未註冊驗證方法，則會在下一次使用新式驗證 (（例如透過網頁瀏覽器) ）登入時收到註冊的提示。 在 *啟用* 狀態下完成註冊的使用者會自動移至 *強制執行* 的狀態。 | 是。 應用程式需要應用程式密碼。 | 是。 登入時需要 Azure AD Multi-Factor Authentication。 | 是。 登入時需要 Azure AD Multi-Factor Authentication。 |

所有使用者一開始都是「已停用」狀態。 當您以每位使用者的 Azure AD Multi-Factor Authentication 註冊使用者時，他們的狀態會變更為 [ *已啟用*]。 當已啟用的使用者登入並完成註冊程序之後，他們的狀態就會變更為「已強制」。 系統管理員可以在狀態之間移動使用者，包括從 *強制執行* 到 *已啟用* 或 *已停用*。

> [!NOTE]
> 如果在使用者上重新啟用個別使用者 MFA，且使用者不會重新註冊，其 MFA 狀態不會從 *啟用* 轉換成在 MFA 管理 UI 中 *強制執行* 。 系統管理員必須直接將使用者移至 [ *強制*]。

## <a name="view-the-status-for-a-user"></a>檢視使用者的狀態

若要查看及管理使用者狀態，請完成下列步驟以存取 Azure 入口網站頁面：

1. 以系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋並選取 [Azure Active Directory]，然後選取 [使用者] > [所有使用者]。
1. 選取 [多重要素驗證]。 您可能必須捲動到右邊才能看到此功能表選項。 選取下方的範例螢幕擷取畫面，以查看完整的 Azure 入口網站視窗和功能表位置： [ ![ 從 Azure AD 的 [使用者] 視窗中選取 [Multi-Factor Authentication]。](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. 隨即開啟新的頁面，其中顯示使用者狀態，如下列範例所示。
   ![顯示 Azure AD Multi-Factor Authentication 範例使用者狀態資訊的螢幕擷取畫面](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>變更使用者的狀態

若要變更使用者的每個使用者 Azure AD Multi-Factor Authentication 狀態，請完成下列步驟：

1. 使用先前的步驟來 [查看使用者的狀態](#view-the-status-for-a-user) ，以取得 Azure AD Multi-Factor Authentication **使用者** ] 頁面。
1. 尋找您想要為每個使用者 Azure AD Multi-Factor Authentication 啟用的使用者。 您可能需要將頂端的檢視變更為 [使用者]。
   ![從 [使用者] 索引標籤選取要變更其狀態的使用者](./media/howto-mfa-userstates/enable1.png)
1. 核取要變更其狀態的使用者名稱旁方塊。
1. 在右側的 [快速步驟] 下，選擇 [啟用] 或 [停用]。 在下列範例中，使用者 *John Smith* 的名稱旁邊有核取方塊，並將予以啟用：![按一下 [快速步驟] 功能表中的 [啟用]，以啟用選取的使用者](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > 當 *已啟用* 的使用者註冊 Azure AD Multi-Factor Authentication 時，會自動切換為 *強制執行*。 請不要手動將使用者狀態變更為 *強制執行* ，除非使用者已註冊，或可接受使用者在連線至舊版驗證通訊協定時遇到中斷的情況。

1. 在開啟的快顯視窗中確認您的選取項目。

當您啟用使用者之後，請透過電子郵件通知他們。 告訴使用者系統會顯示提示來要求其在下一次登入時註冊。 此外，如果您的組織使用不支援新式驗證的非瀏覽器應用程式，他們就需要建立應用程式密碼。 如需詳細資訊，請參閱 [Azure AD Multi-Factor Authentication 的終端使用者指南](../user-help/multi-factor-authentication-end-user-first-time.md) ，以協助他們開始使用。

## <a name="change-state-using-powershell"></a>使用 PowerShell 變更狀態

若要使用 [Azure AD PowerShell](/powershell/azure/) 來變更使用者狀態，您可變更使用者帳戶的 `$st.State` 參數。 使用者帳戶有三個可能的狀態：

* *已啟用*
* 已強制
* *Disabled*  

一般情況下，除非已註冊 MFA，否則請勿將使用者直接移至 *強制執行* 的狀態。 如果您這樣做，舊版驗證應用程式將會停止運作，因為使用者未通過 Azure AD Multi-Factor Authentication 註冊並取得 [應用程式密碼](howto-mfa-app-passwords.md)。 在某些情況下，可能會需要這種行為，但會影響使用者體驗，直到使用者註冊為止。

若要開始進行，請使用 [Install-Module](/powershell/module/powershellget/install-module) 安裝 *MSOnline* 模組，如下所示：

```PowerShell
Install-Module MSOnline
```

接下來，使用 [Connect-MsolService](/powershell/module/msonline/connect-msolservice) 連線：

```PowerShell
Connect-MsolService
```

下列範例 PowerShell 指令碼會為名為 *bsimon@contoso.com* 的個別使用者啟用 MFA：

```PowerShell
$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
$st.RelyingParty = "*"
$st.State = "Enabled"
$sta = @($st)

# Change the following UserPrincipalName to the user you wish to change state
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
```

當您需要大量啟用使用者時，使用 PowerShell 是一個不錯的選項。 下列指令碼會對使用者清單執行迴圈，並針對其帳戶啟用 MFA。 在第一行中定義針對 `$users` 設定的使用者帳戶，如下所示：

   ```PowerShell
   # Define your list of users to update state in bulk
   $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"

   foreach ($user in $users)
   {
       $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
       $st.RelyingParty = "*"
       $st.State = "Enabled"
       $sta = @($st)
       Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
   }
   ```

若要停用 MFA，下列範例使用 [Get-MsolUser](/powershell/module/msonline/get-msoluser) 來取得使用者，然後使用 [Set-MsolUser](/powershell/module/msonline/set-msoluser) 來移除針對已定義使用者所設定的任何 *StrongAuthenticationRequirements*：

```PowerShell
Get-MsolUser -UserPrincipalName bsimon@contoso.com | Set-MsolUser -StrongAuthenticationRequirements @()
```

您也可以使用 [Set-MsolUser](/powershell/module/msonline/set-msoluser) 來直接停用使用者的 MFA，如下所示：

```PowerShell
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements @()
```

## <a name="convert-users-from-per-user-mfa-to-conditional-access"></a>將使用者從每一使用者 MFA 轉換成條件式存取

下列 PowerShell 可協助您根據 Azure AD Multi-Factor Authentication 來轉換成條件式存取。

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> 如果在使用者上重新啟用 MFA，且使用者不會重新註冊，其 MFA 狀態不會從 *啟用* 轉換為在 MFA 管理 UI 中 *強制執行* 。 在此情況下，系統管理員必須直接將使用者移至 [ *強制*]。

## <a name="next-steps"></a>後續步驟

若要設定 Azure AD Multi-Factor Authentication 設定，請參閱設定  [Azure AD Multi-Factor Authentication 設定](howto-mfa-mfasettings.md)。

若要管理 Azure AD Multi-Factor Authentication 的使用者設定，請參閱 [使用 Azure AD Multi-Factor Authentication 管理使用者設定](howto-mfa-userdevicesettings.md)。

若要瞭解為何系統提示使用者執行 MFA，請參閱 [Azure AD Multi-Factor Authentication 報表](howto-mfa-reporting.md)。

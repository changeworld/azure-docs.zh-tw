---
title: 啟用個別使用者的 Multi-Factor Authentication - Azure Active Directory
description: 了解如何藉由變更使用者狀態來啟用個別使用者的 Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2bd688a9e488c1206b0c8531698b061f650e2afe
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87417925"
---
# <a name="enable-per-user-azure-multi-factor-authentication-to-secure-sign-in-events"></a>啟用個別使用者的 Azure Multi-Factor Authentication 以保護登入事件

有兩種方式可在 Azure AD 中要求使用多重要素驗證來保護使用者登入事件。 同時為偏好的第一個選項是設定條件式存取原則，以在特定情況下要求使用多重要素驗證。 第二個選項是為每個使用者啟用 Azure Multi-Factor Authentication。 當個別為使用者啟用時，這些使用者就會在每次登入時執行多重要素驗證 (但有一些例外，例如當其從信任的 IP 位址登入時，或開啟了「已記住裝置」功能時)。

> [!NOTE]
> 建議使用條件式存取原則來啟用 Azure Multi-Factor Authentication。 除非授權不包含條件式存取，否則不再建議變更使用者狀態，因為這會要求使用者在每次登入時執行 MFA。 若要開始使用條件式存取，請參閱[教學課程：使用 Azure Multi-Factor Authentication 來保護使用者登入事件](tutorial-enable-azure-mfa.md)。
>
> 針對 Azure AD 沒有條件式存取的免費租使用者，您可以[使用安全性預設值來保護使用者](../fundamentals/concept-fundamentals-security-defaults.md)。

## <a name="azure-multi-factor-authentication-user-states"></a>Azure Multi-Factor Authentication 使用者狀態

Azure Multi-Factor Authentication 中的使用者帳戶具有下列三種不同狀態：

> [!IMPORTANT]
> 透過條件式存取原則啟用 Azure Multi-Factor Authentication 不會變更使用者的狀態。 如果使用者顯示為已停用，請不要驚慌。 條件式存取不會變更狀態。
>
> **您不應該在使用條件式存取原則時啟用或強制使用者。**

| 狀態 | 描述 | 受影響的非瀏覽器應用程式 | 受影響的瀏覽器應用程式 | 受影響的新式驗證 |
|:---:| --- |:---:|:--:|:--:|
| 已停用 | 未在 Azure Multi-Factor Authentication 中註冊的新使用者預設狀態。 | 否 | 否 | 否 |
| 啟用 | 使用者已在 Azure 多因素驗證中註冊，但尚未註冊驗證方法。 系統將在他們下一次登入時提示他們註冊。 | 否。  它們會繼續運作，直到註冊程序完成為止。 | 是。 工作階段到期之後，必須進行 Azure Multi-Factor Authentication 註冊。| 是。 存取權杖到期之後，必須進行 Azure Multi-Factor Authentication 註冊。 |
| 已強制 | 使用者已註冊，且已完成 Azure Multi-Factor Authentication 的註冊程序。 | 是。 應用程式需要應用程式密碼。 | 是。 登入時必須進行 Azure Multi-Factor Authentication。 | 是。 登入時必須進行 Azure Multi-Factor Authentication。 |

使用者的狀態會反映系統管理員是否已在 Azure Multi-Factor Authentication 中加以註冊，以及其是否已完成註冊程序。

所有使用者一開始都是「已停用」狀態。 當在 Azure Multi-Factor Authentication 中註冊使用者時，其狀態會變更為「已啟用」。 當已啟用的使用者登入並完成註冊程序之後，他們的狀態就會變更為「已強制」。

> [!NOTE]
> 如果在已有註冊詳細資料 (例如電話號碼或電子郵件) 的使用者物件上重新啟用 MFA，則系統管理員必須讓該使用者透過 Azure 入口網站或 PowerShell 重新註冊 MFA。 如果使用者未重新註冊，則在 MFA 管理 UI 中，其 MFA 狀態不會從「已啟用」轉換為「已強制」。

## <a name="view-the-status-for-a-user"></a>檢視使用者的狀態

使用下列步驟來存取可在其中檢視並管理使用者狀態的 Azure 入口網站頁面：

1. 以系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋並選取 [Azure Active Directory]，然後選取 [使用者] > [所有使用者]。
1. 選取 [多重要素驗證]。 您可能必須捲動到右邊才能看到此功能表選項。 選取以下的範例螢幕擷取畫面，以查看完整的 Azure 入口網站視窗和功能表位置： [ ![ 從 Azure AD 的 [使用者] 視窗中選取 [多重要素驗證]。](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. 隨即開啟新的頁面，其中顯示使用者狀態，如下列範例所示。
   ![顯示 Azure Multi-Factor Authentication 中範例使用者狀態資訊的螢幕擷取畫面](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>變更使用者的狀態

若要變更使用者的 Azure Multi-Factor Authentication 狀態，請完成下列步驟：

1. 使用上述步驟來取得 Azure Multi-Factor Authentication **使用者**頁面。
1. 找出要啟用 Azure Multi-Factor Authentication 的使用者。 您可能需要將頂端的檢視變更為 [使用者]。
   ![從 [使用者] 索引標籤選取要變更其狀態的使用者](./media/howto-mfa-userstates/enable1.png)
1. 核取要變更其狀態的使用者名稱旁方塊。
1. 在右側的 [快速步驟] 下，選擇 [啟用] 或 [停用]。 在下列範例中，使用者 *John Smith* 的名稱旁邊有核取方塊，並將予以啟用：![按一下 [快速步驟] 功能表中的 [啟用]，以啟用選取的使用者](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > 「已啟用」的使用者會在註冊 Azure Multi-Factor Authentication 時自動切換為「已強制」。 請勿手動將使用者狀態變更為「已強制」。

1. 在開啟的快顯視窗中確認您的選取項目。

當您啟用使用者之後，請透過電子郵件通知他們。 告訴使用者系統會顯示提示來要求其在下一次登入時註冊。 此外，如果您的組織使用不支援新式驗證的非瀏覽器應用程式，他們就需要建立應用程式密碼。 如需詳細資訊，請參閱 [Azure Multi-Factor Authentication 終端使用者指南](../user-help/multi-factor-authentication-end-user.md)以協助其開始使用。

## <a name="change-state-using-powershell"></a>使用 PowerShell 變更狀態

若要使用 [Azure AD PowerShell](/powershell/azure/) 來變更使用者狀態，您可變更使用者帳戶的 `$st.State` 參數。 使用者帳戶有三個可能的狀態：

* *已啟用*
* 已強制
* *Disabled*  

請勿直接將使用者移至「已強制」狀態。 若您這樣做，則非瀏覽器型的應用程式會停止運作，因為使用者未通過 Azure Multi-Factor Authentication 註冊且未取得[應用程式密碼](howto-mfa-app-passwords.md)。

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

## <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>將使用者從個別使用者 MFA 轉換成條件式存取形式的 MFA

下列 PowerShell 可協助轉換成條件式存取形式的 Azure Multi-Factor Authentication。

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
> 如果在已有註冊詳細資料 (例如電話號碼或電子郵件) 的使用者物件上重新啟用 MFA，則系統管理員必須讓該使用者透過 Azure 入口網站或 PowerShell 來重新註冊 MFA。 如果使用者未重新註冊，則在 MFA 管理 UI 中，其 MFA 狀態不會從「已啟用」轉換為「已強制」。

## <a name="next-steps"></a>後續步驟

若要設定 Azure 多因素驗證設定，請參閱[設定 Azure 多因素驗證設定](howto-mfa-mfasettings.md)。

若要管理 Azure Multi-Factor Authentication 的使用者設定，請參閱[使用 Azure Multi-Factor Authentication 管理使用者設定](howto-mfa-userdevicesettings.md)。

若要了解系統提示或不提示使用者執行 MFA 的原因，請參閱 [Azure Multi-Factor Authentication 報告](howto-mfa-reporting.md)。

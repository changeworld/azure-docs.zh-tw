---
title: 啟用每個使用者的多重要素驗證-Azure Active Directory
description: 瞭解如何藉由變更使用者狀態來啟用每位使用者的 Azure 多重要素驗證
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e8ceaf13324864c7ec3df731c3e710815b0eba9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81309789"
---
# <a name="enable-per-user-azure-multi-factor-authentication-to-secure-sign-in-events"></a>啟用每位使用者的 Azure 多因素驗證以保護登入事件

有兩種方式可以在 Azure AD 中要求多重要素驗證，以保護使用者登入事件。 第一個和慣用的選項是設定條件式存取原則，在某些情況下需要多重要素驗證。 第二個選項是為每個使用者啟用 Azure 多重要素驗證。 個別啟用使用者時，他們會在每次登入時執行多重要素驗證（但有一些例外，例如當他們從受信任的 IP 位址登入，或開啟已_記住的裝置_功能時）。

> [!NOTE]
> 使用條件式存取原則來啟用 Azure 多重要素驗證是建議的方法。 除非您的授權不包含條件式存取，否則不建議變更使用者狀態，因為它會要求使用者在每次登入時執行 MFA。
>
> 若要開始使用條件式存取，請參閱[教學課程：使用 Azure 多重要素驗證保護使用者登入事件](tutorial-enable-azure-mfa.md)。

## <a name="azure-multi-factor-authentication-user-states"></a>Azure 多重要素驗證使用者狀態

Azure Multi-Factor Authentication 中的使用者帳戶具有下列三種不同狀態：

> [!IMPORTANT]
> 透過條件式存取原則啟用 Azure 多重要素驗證並不會變更使用者的狀態。 如果使用者顯示為已停用，請不要驚慌。 條件式存取不會變更狀態。
>
> **如果您使用的是條件式存取原則，則不應啟用或強制執行使用者。**

| 狀態 | 描述 | 受影響的非瀏覽器應用程式 | 受影響的瀏覽器應用程式 | 受影響的新式驗證 |
|:---:| --- |:---:|:--:|:--:|
| 停用 | 未在 Azure 多因素驗證中註冊之新使用者的預設狀態。 | 否 | 否 | 否 |
| 啟用 | 使用者已在 Azure 多因素驗證中註冊，但尚未註冊。 系統將在他們下一次登入時提示他們註冊。 | 不可以。  它們會繼續運作，直到註冊程序完成為止。 | 是。 會話到期之後，就必須註冊 Azure 多因素驗證。| 是。 存取權杖到期後，就必須註冊 Azure 多因素驗證。 |
| 已強制 | 使用者已註冊，並已完成 Azure 多重要素驗證的註冊程式。 | 是。 應用程式需要應用程式密碼。 | 是。 登入時需要 Azure 多重要素驗證。 | 是。 登入時需要 Azure 多重要素驗證。 |

使用者的狀態會反映系統管理員是否已在 Azure 多因素驗證中註冊他們，以及是否已完成註冊程式。

所有使用者一開始都是「已停用」** 狀態。 當您在 Azure 多因素驗證中註冊使用者時，其狀態會變更為 [*已啟用*]。 當已啟用的使用者登入並完成註冊程序之後，他們的狀態就會變更為「已強制」**。

> [!NOTE]
> 如果在已有註冊詳細資料（例如電話或電子郵件）的使用者物件上重新啟用 MFA，則系統管理員必須讓該使用者透過 Azure 入口網站或 PowerShell 重新註冊 MFA。 如果使用者未重新註冊，其 MFA 狀態不會從 [*已啟用*] 轉換為 [mfa 管理] UI 中的 [*強制*]。

## <a name="view-the-status-for-a-user"></a>檢視使用者的狀態

使用下列步驟來存取 [Azure 入口網站] 頁面，您可以在其中查看和管理使用者狀態：

1. 以系統管理員身分登入[Azure 入口網站](https://portal.azure.com)。
1. 搜尋並選取 [ *Azure Active Directory*]，然後選取 [**使用者** > ] [**所有使用者**]。
1. 選取 [**多重要素驗證**]。 您可能需要向右移動，才能看到此功能表選項。 選取以下的範例螢幕擷取畫面，以查看完整的 Azure 入口網站視窗和功能表位置：[![](media/howto-mfa-userstates/selectmfa-cropped.png "從 Azure AD 的 [使用者] 視窗中選取 [多重要素驗證]")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. 隨即開啟新的頁面，其中會顯示使用者狀態，如下列範例所示。
   ![顯示 Azure 多重要素驗證範例使用者狀態資訊的螢幕擷取畫面](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>變更使用者的狀態

若要變更使用者的 Azure 多重要素驗證狀態，請完成下列步驟：

1. 使用上述步驟來取得 Azure Multi-Factor Authentication **使用者**頁面。
1. 尋找您想要為 Azure 多因素驗證啟用的使用者。 您可能需要將頂端的視圖變更為 [**使用者**]。
   ![從 [使用者] 索引標籤選取要變更狀態的使用者](./media/howto-mfa-userstates/enable1.png)
1. 核取使用者名稱旁的方塊，以變更的狀態。
1. 在右手邊的 [**快速步驟**] 底下，選擇 [**啟用**] 或 [**停**用]。 在下列範例中，使用者*John Smith*在其名稱旁邊有核取，並已啟用使用： ![按一下 [快速步驟] 功能表上的 [啟用]，以啟用選取的使用者](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > *已啟用*的使用者在註冊 Azure 多重要素驗證時，會自動切換為*強制執行*。 請勿以手動方式將使用者狀態變更為 [*強制*]。

1. 在開啟的快顯視窗中確認您的選取項目。

當您啟用使用者之後，請透過電子郵件通知他們。 告訴使用者出現提示，要求他們在下一次登入時註冊。 此外，如果您的組織使用不支援新式驗證的非瀏覽器應用程式，他們就需要建立應用程式密碼。 如需詳細資訊，請參閱[Azure 多因素驗證終端使用者指南](../user-help/multi-factor-authentication-end-user.md)，以協助他們開始使用。

## <a name="change-state-using-powershell"></a>使用 PowerShell 變更狀態

若要使用[Azure AD PowerShell](/powershell/azure/overview)來變更使用者狀態，您可以變更`$st.State`使用者帳戶的參數。 使用者帳戶有三種可能的狀態：

* *已啟用*
* *已強制*
* *已停用*  

請勿直接將使用者移至「已強制」** 狀態。 如果您這樣做，非瀏覽器型應用程式會停止運作，因為使用者未通過 Azure 多重要素驗證註冊並取得[應用程式密碼](howto-mfa-mfasettings.md#app-passwords)。

若要開始，請使用[Install 模組](/powershell/module/powershellget/install-module)來安裝*MSOnline*模組，如下所示：

```PowerShell
Install-Module MSOnline
```

接下來，使用[connect-msolservice](/powershell/module/msonline/connect-msolservice)來連接：

```PowerShell
Connect-MsolService
```

下列 PowerShell 腳本範例會針對名為*bsimon@contoso.com*的個別使用者啟用 MFA：

```PowerShell
$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
$st.RelyingParty = "*"
$st.State = "Enabled"
$sta = @($st)

# Change the following UserPrincipalName to the user you wish to change state
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
```

當您需要大量啟用使用者時，使用 PowerShell 是一個不錯的選項。 下列腳本會對使用者清單執行迴圈，並在其帳戶上啟用 MFA。 定義使用者帳戶在第一行`$users`設定它，如下所示：

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

若要停用 MFA，下列範例會取得具有[set-msoluser](/powershell/module/msonline/get-msoluser)的使用者，然後使用[set-set-msoluser](/powershell/module/msonline/set-msoluser)移除已定義使用者的任何*StrongAuthenticationRequirements*集：

```PowerShell
Get-MsolUser -UserPrincipalName bsimon@contoso.com | Set-MsolUser -StrongAuthenticationRequirements @()
```

您也可以使用[set-msoluser](/powershell/module/msonline/set-msoluser) ，直接停用使用者的 MFA，如下所示：

```PowerShell
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements @()
```

## <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>將使用者從每位使用者 MFA 轉換成以條件式存取為基礎的 MFA

下列 PowerShell 可協助您轉換成以條件式存取為基礎的 Azure 多重要素驗證。

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
> 我們最近已變更此行為和此 PowerShell 腳本。 先前，腳本會儲存在 MFA 方法中，停用 MFA，並還原方法。 現在已不再需要，因為 disable 的預設行為並不會清除方法。
>
> 如果在已有註冊詳細資料（例如電話或電子郵件）的使用者物件上重新啟用 MFA，則系統管理員必須讓該使用者透過 Azure 入口網站或 PowerShell 重新註冊 MFA。 如果使用者未重新註冊，其 MFA 狀態不會從 [*已啟用*] 轉換為 [mfa 管理] UI 中的 [*強制*]。

## <a name="next-steps"></a>後續步驟

若要設定 Azure 多重要素驗證設定（例如信任的 Ip、自訂語音訊息和詐騙警示），請參閱[設定 Azure 多因素驗證設定](howto-mfa-mfasettings.md)。 若要管理 Azure 多因素驗證的使用者設定，請參閱[使用 Azure 多因素驗證來管理使用者設定](howto-mfa-userdevicesettings.md)。

若要瞭解為什麼會提示使用者執行 MFA，請參閱[Azure 多因素驗證報告](howto-mfa-reporting.md#azure-ad-sign-ins-report)。

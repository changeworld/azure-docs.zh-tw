---
title: 開啟每個使用者多重身份認證 ─ Azure 的動作目錄
description: 瞭解如何透過變更使用者狀態來啟用每個使用者的 Azure 多重身份驗證
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
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309789"
---
# <a name="enable-per-user-azure-multi-factor-authentication-to-secure-sign-in-events"></a>啟用每個使用者的 Azure 多重身份驗證,以確保登入事件

通過在 Azure AD 中要求多重身份驗證,有兩種方法可以保護使用者登錄事件。 第一個選項是設置條件訪問策略,該策略需要在特定條件下進行多重身份驗證。 第二個選項是啟用每個用戶進行 Azure 多重身份驗證。 當使用者單獨啟用時,他們每次登錄時都會執行多重身份驗證(但有一些例外情況,例如,當他們從受信任的 IP 位址登錄時或_啟用記住的設備_功能時)。

> [!NOTE]
> 建議使用條件訪問策略啟用 Azure 多重身份驗證。 不再建議更改使用者狀態,除非您的許可證不包含條件訪問,因為它要求使用者在每次登錄時執行 MFA。
>
> 要開始使用條件存取,請參閱[教學:使用 Azure 多重身份驗證保護使用者登入事件](tutorial-enable-azure-mfa.md)。

## <a name="azure-multi-factor-authentication-user-states"></a>Azure 多重身份驗證使用者狀態

Azure Multi-Factor Authentication 中的使用者帳戶具有下列三種不同狀態：

> [!IMPORTANT]
> 通過條件存取策略啟用 Azure 多重身份驗證不會更改使用者的狀態。 如果用戶顯示為禁用,請不要驚慌。 條件訪問不會更改狀態。
>
> **如果使用條件訪問策略,則不應啟用或強制使用者。**

| 狀態 | 描述 | 受影響的非瀏覽器應用程式 | 受影響的瀏覽器應用程式 | 受影響的新式驗證 |
|:---:| --- |:---:|:--:|:--:|
| 已停用 | 未在 Azure 多重身份驗證中註冊的新用戶的默認狀態。 | 否 | 否 | 否 |
| 啟用 | 用戶已註冊 Azure 多重身份驗證,但尚未註冊。 系統將在他們下一次登入時提示他們註冊。 | 否。  它們會繼續運作，直到註冊程序完成為止。 | 是。 會話過期后,需要 Azure 多重身份驗證註冊。| 是。 訪問權杖過期後,需要 Azure 多重身份驗證註冊。 |
| 已強制 | 用戶已註冊並已完成 Azure 多重身份驗證的註冊過程。 | 是。 應用程式需要應用程式密碼。 | 是。 登錄時需要 Azure 多重身份驗證。 | 是。 登錄時需要 Azure 多重身份驗證。 |

使用者的狀態反映管理員是否已在 Azure 多重身份驗證中註冊它們,以及他們是否已完成註冊過程。

所有使用者一開始都是「已停用」** 狀態。 在 Azure 多重身份驗證中註冊使用者時,其狀態將更改為*啟用*。 當已啟用的使用者登入並完成註冊程序之後，他們的狀態就會變更為「已強制」**。

> [!NOTE]
> 如果在已具有註冊詳細資訊(如電話或電子郵件)的用戶物件上重新啟用 MFA,則管理員需要讓該使用者通過 Azure 門戶或 PowerShell 重新註冊 MFA。 如果使用者不重新註冊,則其 MFA 狀態不會在 MFA 管理 UI 中從 *「已啟用」* 轉換到*強制*。

## <a name="view-the-status-for-a-user"></a>檢視使用者的狀態

使用以下步驟存取 Azure 門戶頁,您可以在其中查看和管理使用者狀態:

1. 以管理者登入[Azure 門戶](https://portal.azure.com)。
1. 搜尋並選擇*Azure 活動目錄*,然後選擇 **「所有使用者」。**  >  ** **
1. 選擇**多重身份認證**。 您可能需要向右滾動才能看到此功能表選項。 選擇下面的範例螢幕擷取以檢視完整的 Azure 門戶視窗和選單位置:[![](media/howto-mfa-userstates/selectmfa-cropped.png "從 Azure AD 中的「使用者」視窗選擇多重身份驗證")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. 將打開一個新頁面,顯示用戶狀態,如以下示例所示。
   ![顯示 Azure 多重身份驗證的範例使用者狀態資訊的螢幕截圖](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>變更使用者的狀態

要更改使用者的 Azure 多重身份驗證狀態,完成以下步驟:

1. 使用上述步驟來取得 Azure Multi-Factor Authentication **使用者**頁面。
1. 尋找要為 Azure 多重身份驗證啟用的使用者。 您可能需要將頂端的檢視變更為**使用者**。
   ![選擇您要變更的狀態的使用者](./media/howto-mfa-userstates/enable1.png)
1. 選中用戶名稱旁邊的複選框,以更改 其狀態。
1. 在右方,在**快速步驟**下,選擇**啟用**或**關閉**。 在下面的範例中,使用者*John Smith*在其名稱旁邊進行了檢查,並且正在啟用![該檢查: 透過單擊「快速步驟選單上的啟用」啟用選定的使用者](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > *啟用*的使用者在註冊 Azure 多重身份驗證時會自動切換到 *「已強制*」。 不要手動將用戶狀態更改為 *「已執行*」。

1. 在開啟的快顯視窗中確認您的選取項目。

當您啟用使用者之後，請透過電子郵件通知他們。 告訴用戶顯示提示,要求他們在下次登錄時註冊。 此外，如果您的組織使用不支援新式驗證的非瀏覽器應用程式，他們就需要建立應用程式密碼。 有關詳細資訊,請參閱 Azure[多重身份驗證最終使用者指南](../user-help/multi-factor-authentication-end-user.md),以幫助他們開始。

## <a name="change-state-using-powershell"></a>使用 PowerShell 變更狀態

要使用 Azure AD [PowerShell](/powershell/azure/overview)更改`$st.State`使用者狀態,可以更改使用者帳戶的參數。 使用者帳號有三種可能狀態:

* *啟用*
* *已強制*
* *已停用*  

請勿直接將使用者移至「已強制」** 狀態。 如果這樣做,非基於瀏覽器的應用將停止工作,因為使用者尚未通過 Azure 多重身份驗證註冊並獲得[應用密碼](howto-mfa-mfasettings.md#app-passwords)。

要開始使用[安裝模組](/powershell/module/powershellget/install-module)安裝*MSOnline*模組,如下所示:

```PowerShell
Install-Module MSOnline
```

接下來,使用[連線 Msol 服務連線](/powershell/module/msonline/connect-msolservice):

```PowerShell
Connect-MsolService
```

下面的範例 PowerShell 文稿為*bsimon@contoso.com*名為的單個使用者啟用 MFA:

```PowerShell
$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
$st.RelyingParty = "*"
$st.State = "Enabled"
$sta = @($st)

# Change the following UserPrincipalName to the user you wish to change state
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
```

當您需要大量啟用使用者時，使用 PowerShell 是一個不錯的選項。 以下文本迴圈訪問使用者清單,並在其帳戶上啟用 MFA。 定義使用者帳戶在第一行中設定它,如下所示`$users`:

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

要關閉 MFA,以下範例取得有[Get-MsolUser](/powershell/module/msonline/get-msoluser)的使用者,然後移除使用[Set-MsolUser](/powershell/module/msonline/set-msoluser)為定義的使用者設定的任何*強身份驗證要求*:

```PowerShell
Get-MsolUser -UserPrincipalName bsimon@contoso.com | Set-MsolUser -StrongAuthenticationRequirements @()
```

您還可以使用[Set-MsolUser](/powershell/module/msonline/set-msoluser)直接關閉使用者的 MFA,如下所示:

```PowerShell
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements @()
```

## <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>將使用者從每個使用者 MFA 轉換為基於條件存取的 MFA

以下 PowerShell 可以説明您轉換為基於條件訪問的 Azure 多重身份驗證。

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
> 我們最近更改了行為和此 PowerShell 腳本。 以前,腳本保存了 MFA 方法、禁用 MFA 並還原了這些方法。 現在,禁用的默認行為未清除方法,這不再需要。
>
> 如果在已具有註冊詳細資訊(如電話或電子郵件)的用戶物件上重新啟用 MFA,則管理員需要讓該使用者通過 Azure 門戶或 PowerShell 重新註冊 MFA。 如果使用者不重新註冊,則其 MFA 狀態不會在 MFA 管理 UI 中從 *「已啟用」* 轉換到*強制*。

## <a name="next-steps"></a>後續步驟

要設定 Azure 多重身份認證設定 (如信任的 IP、自訂語音郵件和欺詐警報),請參閱[設定 Azure 多重身份驗證設定](howto-mfa-mfasettings.md)。 要管理 Azure 多重身份驗證的使用者設定,請參閱[使用 Azure 多重身份驗證管理使用者設定](howto-mfa-userdevicesettings.md)。

要瞭解提示使用者或未提示使用者執行 MFA 的原因,請參閱[Azure 多重身份驗證報告](howto-mfa-reporting.md#azure-ad-sign-ins-report)。

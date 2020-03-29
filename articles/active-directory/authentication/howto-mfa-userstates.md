---
title: 每使用者多重要素驗證 - Azure 活動目錄
description: 通過更改 Azure 多重要素驗證中的使用者狀態啟用 MFA。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 61d7227c57422cfe2228002750ec29bffa385d44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756756"
---
# <a name="how-to-require-two-step-verification-for-a-user"></a>如何要求使用者使用雙步驟驗證

您可以採取下列方法之一來要求使用雙步驟驗證，這兩者都需要使用全域管理員帳戶。 第一種是為每個使用者啟用 Azure Multi-Factor Authentication (MFA)。 當您分別為每位使用者進行啟用時，這些使用者在每次登入時都會執行雙步驟驗證 (但有一些例外，例如當他們從受信任的 IP 位址登入時，或開啟了 _已記住裝置_ 功能)。 第二個選項是設置條件訪問策略，該策略需要在特定條件下進行兩步驗證。

> [!TIP]
> 建議使用條件訪問策略啟用 Azure 多重要素驗證。 不再建議更改使用者狀態，除非您的許可證不包含條件訪問，因為它要求使用者在每次登錄時執行 MFA。

## <a name="choose-how-to-enable"></a>選擇啟用方式

**藉由變更使用者狀態來啟用** - 這是要求使用雙步驟驗證的傳統方法，本文會加以討論。 這種方法適用於雲端 Azure MFA 和 Azure MFA Server。 使用此方法要求使用者**在每次**登錄並覆蓋條件訪問策略時執行兩步驗證。

**由條件訪問策略啟用**- 這是為使用者啟用兩步驗證的最靈活方法。 啟用"使用條件訪問"策略僅適用于雲中的 Azure MFA，是 Azure AD 的高級功能。 如需這個方法的詳細資訊，請參閱[部署雲端式 Azure Multi-Factor Authentication](howto-mfa-getstarted.md)。

**由 Azure AD Identity Protection 啟用** - 這個方法使用 Azure AD Identity Protection 風險原則，要求所有雲端應用程式進行只根據登入風險的雙步驟驗證。 這個方法需要 Azure Active Directory P2 授權。 有關此方法的詳細資訊，請參閱[Azure 活動目錄標識保護](../identity-protection/howto-sign-in-risk-policy.md)

> [!Note]
> 如需授權和定價的詳細資訊，請參閱 [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/
) 和 [Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) 定價頁面。

## <a name="enable-azure-mfa-by-changing-user-state"></a>藉由變更使用者狀態來啟用 Azure MFA

Azure Multi-Factor Authentication 中的使用者帳戶具有下列三種不同狀態：

> [!IMPORTANT]
> 通過條件訪問策略啟用 Azure MFA 不會更改使用者的狀態。 不要驚慌使用者顯示為禁用。 條件訪問不會更改狀態。 **如果使用者正在使用條件訪問策略，則組織不應啟用或強制使用者。**

| 狀態 | 描述 | 受影響的非瀏覽器應用程式 | 受影響的瀏覽器應用程式 | 受影響的新式驗證 |
|:---:| --- |:---:|:--:|:--:|
| 已停用 | 未註冊 Azure MFA 之新使用者的預設狀態。 | 否 | 否 | 否 |
| 啟用 | 已在 Azure MFA 中註冊使用者，但使用者尚未註冊。 系統將在他們下一次登入時提示他們註冊。 | 否。  它們會繼續運作，直到註冊程序完成為止。 | 是。 工作階段到期之後，必須進行 Azure MFA 註冊。| 是。 存取權杖到期之後，必須進行 Azure MFA 註冊。 |
| 已強制 | 已註冊使用者，而且使用者已完成 Azure MFA 的註冊程序。 | 是。 應用程式需要應用程式密碼。 | 是。 在登入時需要使用 Azure MFA。 | 是。 在登入時需要使用 Azure MFA。 |

使用者的狀態會反映系統管理員是否已在 Azure MFA 中註冊他們，以及他們是否已完成註冊程序。

所有使用者一開始都是「已停用」** 狀態。 當您在 Azure MFA 中註冊使用者時，他們的狀態會變更為「已啟用」**。 當已啟用的使用者登入並完成註冊程序之後，他們的狀態就會變更為「已強制」**。

> [!NOTE]
> 如果在已具有註冊詳細資訊（如電話或電子郵件）的使用者物件上重新啟用 MFA，則管理員需要讓該使用者通過 Azure 門戶或 PowerShell 重新註冊 MFA。 如果使用者不重新註冊，則其 MFA 狀態不會在 MFA 管理 UI 中從 *"已啟用"* 轉換到*強制*。

### <a name="view-the-status-for-a-user"></a>檢視使用者的狀態

使用下列步驟來存取您可在其中檢視並管理使用者狀態的頁面：

1. 以管理員身份登錄到[Azure 門戶](https://portal.azure.com)。
2. 搜尋並選取 [Azure Active Directory]**。 選擇**所有使用者** > **所有使用者**。
3. 選擇**多重要素驗證**。 您可能需要向右滾動才能看到此功能表選項。 選擇下面的示例螢幕截圖以查看完整的 Azure 門戶視窗和功能表位置：[![](media/howto-mfa-userstates/selectmfa-cropped.png "從 Azure AD 中的"使用者"視窗選擇多重要素驗證")](media/howto-mfa-userstates/selectmfa.png#lightbox)
4. 隨即開啟新的頁面，以顯示使用者狀態。
   ![Multi-Factor Authentication 使用者狀態 - 螢幕擷取畫面](./media/howto-mfa-userstates/userstate1.png)

### <a name="change-the-status-for-a-user"></a>變更使用者的狀態

1. 使用上述步驟來取得 Azure Multi-Factor Authentication **使用者**頁面。
2. 尋找您想要啟用 Azure MFA 的使用者。 建議您在頂端變更檢視方式。
   ![從使用者選項卡中選擇要更改的狀態的使用者](./media/howto-mfa-userstates/enable1.png)
3. 勾選其名稱旁的方塊。
4. 在右邊的**快速步驟**下，選擇 [啟用]**** 或 [停用]****。
   ![按一下"快速步驟"功能表上的"啟用"啟用所選使用者](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > 「已啟用」** 的使用者會在註冊 Azure MFA 時自動切換為「已強制」**。 請勿手動將使用者狀態變更為「已強制」**。

5. 在開啟的快顯視窗中確認您的選取項目。

當您啟用使用者之後，請透過電子郵件通知他們。 告訴他們系統會在他們下一次登入時要求其進行註冊。 此外，如果您的組織使用不支援新式驗證的非瀏覽器應用程式，他們就需要建立應用程式密碼。 您也可以納入 [Azure MFA 使用者指南](../user-help/multi-factor-authentication-end-user.md)的連結，以協助他們開始使用。

### <a name="use-powershell"></a>使用 PowerShell

若要使用 [Azure AD PowerShell](/powershell/azure/overview) 來變更使用者狀態，請變更 `$st.State`。 有三個可能的狀態︰

* 啟用
* 已強制
* 已停用  

請勿直接將使用者移至「已強制」** 狀態。 若這樣做，非瀏覽器型的應用程式會停止運作，因為使用者未通過 Azure MFA 註冊且未取得[應用程式密碼](howto-mfa-mfasettings.md#app-passwords)。

請先安裝模組，使用：

   ```PowerShell
   Install-Module MSOnline
   ```

> [!TIP]
> 別忘了先使用 **Connect-MsolService** 連線

   ```PowerShell
   Connect-MsolService
   ```

這個範例 PowerShell 指令碼會為個別使用者啟用 MFA：

   ```PowerShell
   Import-Module MSOnline
   Connect-MsolService
   $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
   $st.RelyingParty = "*"
   $st.State = "Enabled"
   $sta = @($st)
   Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
   ```

當您需要大量啟用使用者時，使用 PowerShell 是一個不錯的選項。 例如，下列指令碼會對使用者清單執行迴圈，並針對使用者的帳戶啟用 MFA：

   ```PowerShell
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

若要停用 MFA，請使用此指令碼：

   ```PowerShell
   Get-MsolUser -UserPrincipalName user@domain.com | Set-MsolUser -StrongAuthenticationRequirements @()
   ```

這也可以縮短為：

   ```PowerShell
   Set-MsolUser -UserPrincipalName user@domain.com -StrongAuthenticationRequirements @()
   ```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>將使用者從每個使用者 MFA 轉換為基於條件訪問的 MFA

以下 PowerShell 可以説明您轉換為基於條件訪問的 Azure 多重要素驗證。

在 ISE 視窗中運行此 PowerShell 或另存為 。要在本地運行的 PS1 檔。

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
> 我們最近相應地更改了上述行為和 PowerShell 腳本。 以前，腳本保存了 MFA 方法、禁用 MFA 並還原了這些方法。 現在，禁用的預設行為未清除方法，這不再需要。
>
> 如果在已具有註冊詳細資訊（如電話或電子郵件）的使用者物件上重新啟用 MFA，則管理員需要讓該使用者通過 Azure 門戶或 PowerShell 重新註冊 MFA。 如果使用者不重新註冊，則其 MFA 狀態不會在 MFA 管理 UI 中從 *"已啟用"* 轉換到*強制*。

## <a name="next-steps"></a>後續步驟

* 系統會還是不會提示使用者執行 MFA 的原因？ 請參閱＜Azure Multi-Factor Authentication 中的報告＞文件中的 [Azure AD 登入報告](howto-mfa-reporting.md#azure-ad-sign-ins-report)一節。
* 若要設定信任的 IP、自訂語音訊息及詐騙警示等額外設定，請參閱[設定 Azure Multi-Factor Authentication 設定](howto-mfa-mfasettings.md)
* 如需管理 Azure Multi-Factor Authentication 使用者設定的詳細資訊，請參閱[在雲端中管理 Azure Multi-Factor Authentication 的使用者設定](howto-mfa-userdevicesettings.md)一文

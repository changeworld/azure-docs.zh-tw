---
title: B2B 來賓使用者的一次性密碼驗證 - Azure AD
description: 如何無需 Microsoft 帳戶而使用電子郵件的單次密碼來驗證 B2B 來賓使用者。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 714e4484c71b995bee186a2d94dc45c7ff82c50d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87908192"
---
# <a name="email-one-time-passcode-authentication-preview"></a>電子郵件單次密碼驗證 (預覽版)

> [!NOTE]
> 電子郵件單次密碼是 Azure Active Directory 的公開預覽功能。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文說明如何為 B2B 來賓使用者啟用電子郵件一次性密碼驗證。 當無法透過如 Azure AD、Microsoft 帳戶 (MSA) 或 Google 同盟等方式驗證 B2B 來賓使用者時，可使用電子郵件單次密碼功能來驗證。 使用單次密碼驗證時，不需要建立 Microsoft 帳戶。 當來賓使用者兌換邀請或存取共用資源時，他們可以要求一個暫時性驗證碼，此驗證碼會傳送到他們的電子郵件地址。 之後，他們便可輸入此驗證碼繼續登入。

此功能目前可供預覽 (請參閱下方的[選擇加入預覽版](#opting-in-to-the-preview))。 預覽結束後，這項功能預設會對所有租用戶開啟。

> [!NOTE]
> 單次密碼使用者必須使用包含租用戶內容的連結 (例如 `https://myapps.microsoft.com/?tenantid=<tenant id>` 或 `https://portal.azure.com/<tenant id>`，如果是已驗證的網域，則為 `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`) 來登入。 應用程式和資源的直接連結只要包含租用戶內容，也可有同樣作用。 來賓使用者目前無法使用沒有租用戶內容的端點來登入。 例如，使用 `https://myapps.microsoft.com`、`https://portal.azure.com` 或小組通用端點將會導致錯誤。 

## <a name="user-experience-for-one-time-passcode-guest-users"></a>單次密碼來賓使用者的使用者體驗
使用單次密碼驗證時，來賓使用者可以兌換您的邀請，方法是按一下直接連結，或使用邀請電子郵件。 無論是哪一種方法，瀏覽器中會顯示訊息，指出驗證碼將傳送到來賓使用者的電子郵件地址。 來賓使用者可選取 [傳送驗證碼]：
 
   ![顯示 [傳送驗證碼] 按鈕的螢幕擷取畫面](media/one-time-passcode/otp-send-code.png)
 
密碼會傳送到使用者的電子郵件地址。 使用者可從電子郵件中擷取該密碼，並在瀏覽器視窗中輸入：
 
   ![顯示 [輸入驗證碼] 頁面的螢幕擷取畫面](media/one-time-passcode/otp-enter-code.png)
 
來賓使用者現在已通過驗證，可看見共用資源或繼續登入。 

> [!NOTE]
> 單次密碼的有效時間為 30 分鐘。 30 分鐘之後，該特定的單次密碼不再有效，使用者必須要求新的密碼。 使用者工作階段會在 24 小時後過期。 在此之後，來賓使用者在存取資源時會收到新密碼。 工作階段到期能夠增加安全性，尤其是在來賓使用者離開公司或不再需要存取時。

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>來賓使用者何時會收到單次密碼？

在下列情況下，當來賓使用者兌換邀請，或使用已與他們共用的資源連結時，便會收到單次密碼：
- 他們沒有 Azure AD 帳戶 
- 他們沒有 Microsoft 帳戶 
- 邀請方租用戶未為 @gmail.com 和 @googlemail.com 使用者設定 Google 同盟 

邀請時，不會指出您邀請的使用者將使用單次密碼驗證。 但當來賓使用者登入時，如果沒有其他驗證方法可使用，單次密碼驗證將作為後援方法。 

您可移至 [Azure Active Directory] > [使用者]，以檢視在 Azure 入口網站中使用一次性密碼進行驗證的來賓使用者。

![螢幕擷取畫面，其中顯示具有 OTP 來源值的一次性密碼使用者](media/one-time-passcode/otp-users.png)

> [!NOTE]
> 當使用者兌換單次密碼，並稍後取得 MSA、Azure AD 帳戶或其他同盟帳戶時，系統仍會繼續使用單次密碼進行驗證。 如果您想要更新驗證方法，您可以刪除來賓使用者帳戶，然後重新邀請他們。

### <a name="example"></a>範例
邀請來賓使用者 alexdoe@gmail.com 到 Fabrikam，該使用者尚未設定 Google 同盟。 Alex 沒有 Microsoft 帳戶。 他們會收到一次性密碼進行驗證。

## <a name="opting-in-to-the-preview"></a>選擇加入預覽版 
選擇加入可能需要數分鐘的時間才會生效。 生效後，只有剛獲邀且符合上述條件的使用者可使用單次密碼驗證。 先前已兌換邀請的來賓使用者將繼續使用其相同的驗證方法。

### <a name="to-opt-in-using-the-azure-ad-portal"></a>使用 Azure AD 入口網站選擇加入
1.  以 Azure AD 全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
2.  在導覽窗格中，選取 [Azure Active Directory]。
3.  選取 [外部身分識別] > [外部共同作業設定]。
5.  在 [為來賓啟用電子郵件單次密碼 (預覽版)] 下方，選取 [是]。
 
### <a name="to-opt-in-using-powershell"></a>使用 PowerShell 選擇加入

首先，您必須安裝最新版的 Azure AD PowerShell for Graph 模組 (AzureADPreview)。 然後，您須判定 B2B 原則是否已經存在，並執行適當的命令。

#### <a name="prerequisite-install-the-latest-azureadpreview-module"></a>必要條件：安裝最新的 AzureADPreview 模組
首先，檢查您已安裝哪些模組。 請以已提高權限的使用者身分 (以系統管理員身分執行) 開啟 Windows PowerShell，然後執行下列命令：
 
```powershell  
Get-Module -ListAvailable AzureAD*
```

如果顯示 AzureADPreview 模組，但沒有訊息指出有更高版本，則表示您已就緒。 否則，根據輸出，執行下列其中一個動作︰

- 如果未傳回任何結果，請執行下列命令來安裝 AzureADPreview 模組：
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- 如果結果中僅顯示 AzureAD 模組，請執行下列命令來安裝 AzureADPreview 模組： 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- 如果結果中僅顯示 AzureADPreview 模組，但您收到指出有更新版本的訊息，請執行下列命令來更新該模組： 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
  ```

您可能會收到提示，指出您正從未受信任的存放庫安裝模組。 如果您先前未將 PSGallery 存放庫設定為受信任的存放庫，就會發生此情況。 請按 **Y** 以安裝模組。

#### <a name="check-for-existing-policies-and-opt-in"></a>檢查現有原則並選擇加入

接著，執行以下內容以檢查 B2BManagementPolicy 目前是否存在：

```powershell 
$currentpolicy =  Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
$currentpolicy -ne $null
```
- 如果輸出結果為 False，表示原則不存在。 請建立新 B2BManagementPolicy，並執行下列內容以選擇加入預覽版：

   ```powershell 
   $policyValue=@("{`"B2BManagementPolicy`":{`"PreviewPolicy`":{`"Features`":[`"OneTimePasscode`"]}}}")
   New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true
   ```

- 如果輸出結果為 True，則表示 B2BManagementPolicy 原則目前存在。 若要更新原則並選擇加入預覽版，請執行下列內容：
  
   ```powershell 
   $policy = $currentpolicy.Definition | ConvertFrom-Json
   $features=[PSCustomObject]@{'Features'=@('OneTimePasscode')}; $policy.B2BManagementPolicy | Add-Member 'PreviewPolicy' $features -Force; $policy.B2BManagementPolicy
   $updatedPolicy = $policy | ConvertTo-Json -Depth 3
   Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
   ```

## <a name="opting-out-of-the-preview-after-opting-in"></a>選擇加入後選擇退出預覽版
選擇退出動作可能需要數分鐘的時間才會生效。 如果您關閉預覽版，則任何已兌換單次密碼的來賓使用者將無法登入。 您可以刪除來賓使用者，然後使用另一個驗證方法重新邀請使用者以允許他們登入。

### <a name="to-turn-off-the-preview-using-the-azure-ad-portal"></a>使用 Azure AD 入口網站關閉預覽版
1.  以 Azure AD 全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
2.  在導覽窗格中，選取 [Azure Active Directory]。
3.  選取 [外部身分識別] > [外部共同作業設定]。
5.  在 [為來賓啟用電子郵件單次密碼 (預覽版)] 下方，選取 [否]。

### <a name="to-turn-off-the-preview-using-powershell"></a>使用 PowerShell 關閉預覽版
如果您還未安裝最新版的 AzureADPreview 模組，請安裝 (請參閱前述的[必要條件：安裝最新的 AzureADPreview 模組](#prerequisite-install-the-latest-azureadpreview-module))。 接著，執行下列內容確認單次密碼預覽原則目前存在：

```powershell 
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
($currentPolicy -ne $null) -and ($currentPolicy.Definition -like "*OneTimePasscode*")
```

如果輸出結果為 True，請執行下列內容選擇退出預覽版：

```powershell 
$policy = $currentpolicy.Definition | ConvertFrom-Json
$policy.B2BManagementPolicy.PreviewPolicy.Features = $policy.B2BManagementPolicy.PreviewPolicy.Features.Where({$_ -ne "OneTimePasscode"})
$updatedPolicy = $policy | ConvertTo-Json -Depth 3
Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
```


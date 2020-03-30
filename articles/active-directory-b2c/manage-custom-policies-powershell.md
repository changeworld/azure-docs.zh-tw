---
title: 使用 PowerShell 管理自訂策略
titleSuffix: Azure AD B2C
description: 使用 Azure 活動目錄 （Azure AD） PowerShell Cmdlet 對 Azure AD B2C 自訂策略進行程式設計管理。 使用 PowerShell 創建、讀取、更新和刪除自訂策略。
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ebf0cfffa410d8dfe2f0e0b42a0fee0c16106fde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187401"
---
# <a name="manage-azure-ad-b2c-custom-policies-with-azure-powershell"></a>使用 Azure PowerShell 管理 Azure AD B2C 自訂策略

Azure PowerShell 為 Azure AD B2C 租戶中的命令列和腳本自訂策略管理提供了多個 Cmdlet。 瞭解如何使用 Azure AD PowerShell 模組：

* 在 Azure AD B2C 租戶中列出自訂策略
* 從租戶下載策略
* 通過覆蓋現有策略的內容更新現有策略
* 將新策略上載到 Azure AD B2C 租戶
* 從租戶中刪除自訂策略

## <a name="prerequisites"></a>Prerequisites

* [Azure AD B2C 租戶](tutorial-create-tenant.md)，以及具有[B2C IEF 策略管理員](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator)角色的目錄中使用者的憑據
* 上載到租戶的[自訂策略](custom-policy-get-started.md)
* [用於圖形**預覽模組**的 Azure AD 電源外殼](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)

## <a name="connect-powershell-session-to-b2c-tenant"></a>將 PowerShell 會話連接到 B2C 租戶

要使用 Azure AD B2C 租戶中的自訂策略，首先需要使用[Connect-AzureAD][Connect-AzureAD]命令將 PowerShell 會話連接到租戶。

執行以下命令，`{b2c-tenant-name}`用 Azure AD B2C 租戶的名稱替換。 使用在目錄中分配[了 B2C IEF 策略管理員](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator)角色的帳戶登錄。

```PowerShell
Connect-AzureAD -Tenant "{b2c-tenant-name}.onmicrosoft.com"
```

顯示成功登錄的示例命令輸出：

```Console
PS C:\> Connect-AzureAD -Tenant "contosob2c.onmicrosoft.com"

Account               Environment TenantId                             TenantDomain                 AccountType
-------               ----------- --------                             ------------                 -----------
azureuser@contoso.com AzureCloud  00000000-0000-0000-0000-000000000000 contosob2c.onmicrosoft.com   User
```

## <a name="list-all-custom-policies-in-the-tenant"></a>列出租戶中的所有自訂策略

發現自訂策略允許 Azure AD B2C 管理員查看、管理和將業務邏輯添加到其操作中。 使用[獲取 AzureADMSTrustFrameworkPolicy 命令][Get-AzureADMSTrustFrameworkPolicy]返回 Azure AD B2C 租戶中自訂策略的 ID 清單。

```PowerShell
Get-AzureADMSTrustFrameworkPolicy
```

命令輸出示例：

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy

Id
--
B2C_1A_TrustFrameworkBase
B2C_1A_TrustFrameworkExtensions
B2C_1A_signup_signin
B2C_1A_ProfileEdit
B2C_1A_PasswordReset
```

## <a name="download-a-policy"></a>下載策略

查看策略識別欄位表後，可以使用[Get-AzureMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy]定位特定策略以下載其內容。

```PowerShell
Get-AzureADMSTrustFrameworkPolicy [-Id <policyId>]
```

在此示例中，將下載具有 ID *B2C_1A_signup_signin*的策略：

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0" TenantId="contosob2c.onmicrosoft.com" PolicyId="B2C_1A_signup_signin" PublicPolicyUri="http://contosob2c.onmicrosoft.com/B2C_1A_signup_signin" TenantObjectId="00000000-0000-0000-0000-000000000000">
  <BasePolicy>
    <TenantId>contosob2c.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

要在本地編輯策略內容，請將命令輸出管道到帶有`-OutputFilePath`參數的檔，然後在您最喜愛的編輯器中打開該檔。

將輸出發送到檔的示例命令：

```PowerShell
# Download and send policy output to a file
Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -OutputFilePath C:\RPPolicy.xml
```

## <a name="update-an-existing-policy"></a>更新現有策略

編輯已創建或下載的策略檔後，可以使用["設置-AzureADMSTrustFrameworkPolicy"][Set-AzureADMSTrustFrameworkPolicy]命令將更新的策略發佈到 Azure AD B2C。

如果使用 Azure `Set-AzureADMSTrustFrameworkPolicy` AD B2C 租戶中已存在的策略 ID 發出命令，則該策略的內容將被覆蓋。

```PowerShell
Set-AzureADMSTrustFrameworkPolicy [-Id <policyId>] -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

範例命令：

```PowerShell
# Update an existing policy from file
Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
```

有關其他示例，請參閱[集 AzureADMSTrustFramework策略][Set-AzureADMSTrustFrameworkPolicy]策略策略引用。

## <a name="upload-a-new-policy"></a>上傳新策略

當您對在生產中運行的自訂策略進行更改時，您可能希望發佈多個版本的策略，以進行回退或 A/B 測試方案。 或者，您可能希望複製現有策略，修改它，進行一些小更改，然後將其上載為新策略供其他應用程式使用。

使用["新建 AzureMSTrustFrameworkPolicy"][New-AzureADMSTrustFrameworkPolicy]命令上載新策略：

```PowerShell
New-AzureADMSTrustFrameworkPolicy -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

範例命令：

```PowerShell
# Add new policy from file
New-AzureADMSTrustFrameworkPolicy -InputFilePath C:\SignUpOrSignInv2.xml
```

## <a name="delete-a-custom-policy"></a>刪除自訂策略

為了維護乾淨的操作生命週期，我們建議您定期刪除未使用的自訂策略。 例如，在執行對一組新策略的遷移並驗證新策略的功能後，您可能希望刪除舊策略版本。 此外，如果您嘗試發佈一組自訂策略並收到錯誤，則刪除作為失敗版本的一部分創建的策略可能有意義。

使用["刪除 AzureADMSTrustFrameworkPolicy"命令][Remove-AzureADMSTrustFrameworkPolicy]從租戶中刪除策略。

```PowerShell
Remove-AzureADMSTrustFrameworkPolicy -Id <policyId>
```

範例命令：

```PowerShell
# Delete an existing policy
Remove-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
```

## <a name="troubleshoot-policy-upload"></a>排除策略上載故障

當您嘗試發佈新的自訂策略或更新現有策略時，不正確的 XML 格式和策略檔繼承鏈中的錯誤可能會導致驗證失敗。

例如，下面是嘗試更新包含格式錯誤的 XML 的內容的策略（為了簡潔起見，輸出被截斷）：

```Console
PS C:\> Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
Set-AzureADMSTrustFrameworkPolicy : Error occurred while executing PutTrustFrameworkPolicy
Code: AADB2C
Message: Validation failed: 1 validation error(s) found in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com".Schema validation error found at line
14 col 55 in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com": The element 'OutputClaims' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06' cannot contain text. List of possible elements expected: 'OutputClaim' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06'.
...
```

有關對自訂策略進行故障排除的資訊，請參閱[對 Azure AD B2C 自訂策略和標識體驗框架進行故障排除](active-directory-b2c-guide-troubleshooting-custom.md)。

## <a name="next-steps"></a>後續步驟

有關使用 PowerShell 作為持續集成/持續傳遞 （CI/CD） 管道的一部分部署自訂策略的資訊，請參閱[從 Azure DevOps 管道部署自訂策略](deploy-custom-policies-devops.md)。

<!-- LINKS - External -->
[Connect-AzureAD]: https://docs.microsoft.com/powershell/module/azuread/get-azureadmstrustframeworkpolicy
[Get-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/get-azureadmstrustframeworkpolicy
[New-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/new-azureadmstrustframeworkpolicy
[Remove-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/remove-azureadmstrustframeworkpolicy
[Set-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/set-azureadmstrustframeworkpolicy

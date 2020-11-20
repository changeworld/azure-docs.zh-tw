---
title: 使用 PowerShell 管理自訂原則
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory (Azure AD) PowerShell Cmdlet，以程式設計方式管理您的 Azure AD B2C 自訂原則。 使用 PowerShell 建立、讀取、更新和刪除自訂原則。
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 29db2214ddabeb6df2aa937c4ccd1f9938143969
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94949440"
---
# <a name="manage-azure-ad-b2c-custom-policies-with-azure-powershell"></a>使用 Azure PowerShell 管理 Azure AD B2C 自訂原則

Azure PowerShell 在您的 Azure AD B2C 租使用者中提供數個命令列和腳本型自訂原則管理的 Cmdlet。 瞭解如何使用 Azure AD PowerShell 模組：

* 列出 Azure AD B2C 租使用者中的自訂原則
* 從租使用者下載原則
* 藉由覆寫內容來更新現有的原則
* 將新的原則上傳至您的 Azure AD B2C 租使用者
* 從租使用者刪除自訂原則

## <a name="prerequisites"></a>Prerequisites

* [Azure AD B2C 租](tutorial-create-tenant.md)使用者，以及具有 [B2C IEF 原則系統管理員](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator) 角色之目錄中的使用者認證
* 已上傳至您租使用者的[自訂原則](custom-policy-get-started.md)
* [適用于 Graph preview 的 Azure AD PowerShell **模組**](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)

## <a name="connect-powershell-session-to-b2c-tenant"></a>將 PowerShell 會話連線至 B2C 租使用者

若要在 Azure AD B2C 租使用者中使用自訂原則，您必須先使用 [AzureAD][Connect-AzureAD] 命令將您的 PowerShell 會話連線至租使用者。

執行下列命令， `{b2c-tenant-name}` 並以您 Azure AD B2C 租使用者的名稱取代。 使用已在目錄中指派 [B2C IEF 原則系統管理員](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator) 角色的帳戶登入。

```PowerShell
Connect-AzureAD -Tenant "{b2c-tenant-name}.onmicrosoft.com"
```

顯示成功登入的範例命令輸出：

```Console
PS C:\> Connect-AzureAD -Tenant "contosob2c.onmicrosoft.com"

Account               Environment TenantId                             TenantDomain                 AccountType
-------               ----------- --------                             ------------                 -----------
azureuser@contoso.com AzureCloud  00000000-0000-0000-0000-000000000000 contosob2c.onmicrosoft.com   User
```

## <a name="list-all-custom-policies-in-the-tenant"></a>列出租使用者中的所有自訂原則

探索自訂原則可讓 Azure AD B2C 系統管理員在其作業中檢查、管理和新增商務邏輯。 使用 [AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] 命令，傳回 Azure AD B2C 租使用者中自訂原則的識別碼清單。

```PowerShell
Get-AzureADMSTrustFrameworkPolicy
```

範例命令輸出：

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

## <a name="download-a-policy"></a>下載原則

檢查原則識別碼清單之後，您可以使用 [AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] 來設定特定原則的目標，以下載其內容。

```PowerShell
Get-AzureADMSTrustFrameworkPolicy [-Id <policyId>]
```

在此範例中，會下載識別碼為 *B2C_1A_signup_signin* 的原則：

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

若要在本機編輯原則內容，請使用管線將命令輸出傳送到檔案 `-OutputFilePath` ，然後在您慣用的編輯器中開啟該檔案。

將輸出傳送至檔案的範例命令：

```PowerShell
# Download and send policy output to a file
Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -OutputFilePath C:\RPPolicy.xml
```

## <a name="update-an-existing-policy"></a>更新現有的原則

在編輯您所建立或下載的原則檔之後，您可以使用 [AzureADMSTrustFrameworkPolicy][Set-AzureADMSTrustFrameworkPolicy] 命令將更新的原則發佈至 Azure AD B2C。

如果您 `Set-AzureADMSTrustFrameworkPolicy` 使用 Azure AD B2C 租使用者中已存在的原則識別碼發出命令，則會覆寫該原則的內容。

```PowerShell
Set-AzureADMSTrustFrameworkPolicy [-Id <policyId>] -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

範例命令：

```PowerShell
# Update an existing policy from file
Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
```

如需其他範例，請參閱 [AzureADMSTrustFrameworkPolicy][Set-AzureADMSTrustFrameworkPolicy] 命令參考。

## <a name="upload-a-new-policy"></a>上傳新的原則

當您變更在生產環境中執行的自訂原則時，您可能會想要針對 fallback 或 A/B 測試案例發行多個版本的原則。 或者，您可能會想要複製現有的原則、使用一些小變更加以修改，然後將它上傳為新的原則，以供不同的應用程式使用。

使用 [AzureADMSTrustFrameworkPolicy][New-AzureADMSTrustFrameworkPolicy] 命令上傳新的原則：

```PowerShell
New-AzureADMSTrustFrameworkPolicy -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

範例命令：

```PowerShell
# Add new policy from file
New-AzureADMSTrustFrameworkPolicy -InputFilePath C:\SignUpOrSignInv2.xml
```

## <a name="delete-a-custom-policy"></a>刪除自訂原則

為了維持乾淨的作業生命週期，我們建議您定期移除未使用的自訂原則。 例如，您可能想要在執行遷移至一組新的原則，並確認新原則的功能之後，移除舊的原則版本。 此外，如果您嘗試發行一組自訂原則並收到錯誤，則移除在失敗的版本中建立的原則可能會有意義。

使用 [AzureADMSTrustFrameworkPolicy][Remove-AzureADMSTrustFrameworkPolicy] 命令來刪除租使用者中的原則。

```PowerShell
Remove-AzureADMSTrustFrameworkPolicy -Id <policyId>
```

範例命令：

```PowerShell
# Delete an existing policy
Remove-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
```

## <a name="troubleshoot-policy-upload"></a>針對原則上傳進行疑難排解

當您嘗試發佈新的自訂原則或更新現有的原則時，原則檔案繼承鏈中不當的 XML 格式和錯誤可能會導致驗證失敗。

例如，以下嘗試以包含格式錯誤 XML (輸出的內容來更新原則，以求簡潔) ：

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

如需針對自訂原則進行疑難排解的詳細資訊，請參閱 [Azure AD B2C 自訂原則和 Identity Experience Framework 的疑難排解](./troubleshoot-custom-policies.md)。

## <a name="next-steps"></a>後續步驟

如需有關使用 PowerShell 將自訂原則部署為持續整合/持續傳遞的一部分 (CI/CD) 管線的詳細資訊，請參閱 [從 Azure DevOps 管線部署自訂原則](deploy-custom-policies-devops.md)。

<!-- LINKS - External -->
[Connect-AzureAD]: /powershell/module/azuread/get-azureadmstrustframeworkpolicy
[Get-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/get-azureadmstrustframeworkpolicy
[New-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/new-azureadmstrustframeworkpolicy
[Remove-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/remove-azureadmstrustframeworkpolicy
[Set-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/set-azureadmstrustframeworkpolicy
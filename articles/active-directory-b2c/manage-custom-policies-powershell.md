---
title: 使用 PowerShell 管理自訂原則
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory （Azure AD） PowerShell Cmdlet，以程式設計方式管理您的 Azure AD B2C 自訂原則。 使用 PowerShell 建立、讀取、更新和刪除自訂原則。
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e29bb245ed0fc79a6f72688dc6e4d044f2828c45
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2020
ms.locfileid: "77463124"
---
# <a name="manage-azure-ad-b2c-custom-policies-with-azure-powershell"></a>使用 Azure PowerShell 管理 Azure AD B2C 自訂原則

Azure PowerShell 為您的 Azure AD B2C 租使用者中的命令列和腳本型自訂原則管理提供了數個 Cmdlet。 瞭解如何使用 Azure AD PowerShell 模組來執行下列動作：

* 列出 Azure AD B2C 租使用者中的自訂原則
* 從租使用者下載原則
* 藉由覆寫其內容來更新現有的原則
* 將新的原則上傳至您的 Azure AD B2C 租使用者
* 從租使用者刪除自訂原則

## <a name="prerequisites"></a>Prerequisites

* [Azure AD B2C 租](tutorial-create-tenant.md)使用者，以及具有[B2C IEF 原則系統管理員](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator)角色之目錄中使用者的認證
* 已上傳至您租使用者的[自訂原則](custom-policy-get-started.md)
* [Azure AD 適用于圖形**預覽模組**的 PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)

## <a name="connect-powershell-session-to-b2c-tenant"></a>將 PowerShell 會話連線至 B2C 租使用者

若要在您的 Azure AD B2C 租使用者中使用自訂原則，您必須先使用[AzureAD][Connect-AzureAD]命令，將您的 PowerShell 會話連線到租使用者。

執行下列命令，並以您的 Azure AD B2C 租使用者名稱取代 `{b2c-tenant-name}`。 使用在目錄中已獲指派[B2C IEF 原則系統管理員](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator)角色的帳戶登入。

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

探索自訂原則可讓 Azure AD B2C 系統管理員審查、管理及新增商務邏輯至其作業。 使用[AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy]命令，以傳回 Azure AD B2C 租使用者中自訂原則的識別碼清單。

```PowerShell
Get-AzureADMSTrustFrameworkPolicy
```

命令輸出範例：

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

查看原則識別碼清單之後，您可以使用 AzureADMSTrustFrameworkPolicy 來下載其內容，以[取得][Get-AzureADMSTrustFrameworkPolicy]特定原則的目標。

```PowerShell
Get-AzureADMSTrustFrameworkPolicy [-Id <policyId>]
```

在此範例中，會下載識別碼為*B2C_1A_signup_signin*的原則：

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

若要在本機編輯原則內容，請使用管線將命令輸出傳送至具有 `-OutputFilePath` 引數的檔案，然後在您慣用的編輯器中開啟該檔案。

將輸出傳送至檔案的範例命令：

```PowerShell
# Download and send policy output to a file
Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -OutputFilePath C:\RPPolicy.xml
```

## <a name="update-an-existing-policy"></a>更新現有的原則

編輯您已建立或下載的原則檔案之後，您可以使用[AzureADMSTrustFrameworkPolicy][Set-AzureADMSTrustFrameworkPolicy]命令，將更新的原則發佈到 Azure AD B2C。

如果您使用已存在於 Azure AD B2C 租使用者中的原則識別碼發出 `Set-AzureADMSTrustFrameworkPolicy` 命令，則會覆寫該原則的內容。

```PowerShell
Set-AzureADMSTrustFrameworkPolicy [-Id <policyId>] -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

範例命令：

```PowerShell
# Update an existing policy from file
Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
```

如需其他範例，請參閱[AzureADMSTrustFrameworkPolicy][Set-AzureADMSTrustFrameworkPolicy]命令參考。

## <a name="upload-a-new-policy"></a>上傳新的原則

當您變更在生產環境中執行的自訂原則時，您可能會想要針對 fallback 或 A/B 測試案例發佈多個版本的原則。 或者，您可能想要建立現有原則的複本，使用幾個小變更來修改它，然後將它上傳成為新的原則，供不同的應用程式使用。

使用[AzureADMSTrustFrameworkPolicy][New-AzureADMSTrustFrameworkPolicy]命令上傳新的原則：

```PowerShell
New-AzureADMSTrustFrameworkPolicy -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

範例命令：

```PowerShell
# Add new policy from file
New-AzureADMSTrustFrameworkPolicy -InputFilePath C:\SignUpOrSignInv2.xml
```

## <a name="delete-a-custom-policy"></a>刪除自訂原則

若要維持乾淨的作業生命週期，建議您定期移除未使用的自訂原則。 例如，您可能會想要在執行一組新原則的遷移，並驗證新的原則功能之後，移除舊的原則版本。 此外，如果您嘗試發行一組自訂原則，並收到錯誤，則移除在失敗版本中建立的原則可能會有意義。

使用[AzureADMSTrustFrameworkPolicy][Remove-AzureADMSTrustFrameworkPolicy]命令，從您的租使用者中刪除原則。

```PowerShell
Remove-AzureADMSTrustFrameworkPolicy -Id <policyId>
```

範例命令：

```PowerShell
# Delete an existing policy
Remove-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
```

## <a name="troubleshoot-policy-upload"></a>針對原則上傳進行疑難排解

當您嘗試發佈新的自訂原則或更新現有的原則時，原則檔案繼承鏈中不正確的 XML 格式和錯誤可能會導致驗證失敗。

例如，以下是嘗試更新包含格式不正確之 XML 內容的原則（為了簡潔起見，輸出已截斷）：

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

如需疑難排解自訂原則的詳細資訊，請參閱針對[Azure AD B2C 自訂原則和 Identity Experience Framework 進行疑難排解](active-directory-b2c-guide-troubleshooting-custom.md)。

## <a name="next-steps"></a>後續步驟

如需有關使用 PowerShell 將自訂原則部署為持續整合/持續傳遞（CI/CD）管線一部分的詳細資訊，請參閱[從 Azure DevOps 管線部署自訂原則](deploy-custom-policies-devops.md)。

<!-- LINKS - External -->
[Connect-AzureAD]: https://docs.microsoft.com/powershell/module/azuread/get-azureadmstrustframeworkpolicy
[Get-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/get-azureadmstrustframeworkpolicy
[New-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/new-azureadmstrustframeworkpolicy
[Remove-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/remove-azureadmstrustframeworkpolicy
[Set-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/set-azureadmstrustframeworkpolicy

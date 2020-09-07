---
title: 使用 Azure PowerShell 設定 Azure 證明
description: 如何使用 Azure PowerShell 安裝和設定證明提供者。
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 70e86e01a9d37a27620d451bcd5d035dfcb4573d
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89236887"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-powershell"></a>快速入門：使用 Azure PowerShell 設定 Azure 證明

請遵循下列步驟，使用 Azure PowerShell 建立及設定證明提供者。 如需如何安裝和執行 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 概觀](/powershell/azure/?view=azps-2.8.0&viewFallbackFrom=azps-2.4.0)。

請注意，PowerShell 資源庫已不再支援傳輸層安全性 (TLS) 1.0 和 1.1 版。 建議使用 TLS 1.2 或更新版本。 因此，您可能收到下列錯誤：

- 警告：無法解析套件來源 'https://www.powershellgallery.com/api/v2 '
- PackageManagement\Install-Package：找不到與指定搜尋條件和模組名稱相符的項目 

若要繼續與 PowerShell 資源庫互動，請在 Install-Module 命令之前執行下列命令

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
```

## <a name="install-azattestation-powershell-module"></a>安裝 Az.Attestation PowerShell 模組

在具有 Azure PowerShell 的電腦上，安裝 Az.Attestation PowerShell 模組，其中包含適用於 Azure 證明的 Cmdlet。  

### <a name="initial-installation"></a>初始安裝

終止所有現有的 PowerShell 視窗。

若要為「目前使用者」進行安裝，請啟動未提高權限的 PowerShell 視窗，並執行：

```powershell
Install-Module -Name Az.Attestation -AllowClobber -Scope CurrentUser
```

若要為「所有使用者」進行安裝，請啟動已提高權限的 PowerShell 視窗，並執行：

```powershell
Install-Module -Name Az.Attestation -AllowClobber -Scope AllUsers
```

關閉已提高權限的 PowerShell 主控台。

### <a name="update-the-installation"></a>更新安裝

終止所有現有的 PowerShell 視窗。

若要為「目前使用者」進行更新，請啟動未提高權限的 PowerShell 視窗，並執行：

```powershell
Update-Module -Name Az.Attestation
```

若要為「所有使用者」進行更新，請啟動已提高權限的 PowerShell 視窗，並執行：

```powershell
Update-Module -Name Az.Attestation
```

關閉已提高權限的 PowerShell 主控台。

### <a name="get-installed-modules"></a>取得已安裝的模組

支援證明作業所需的 Az 模組最低版本：
- Az 4.5.0
- Az.Accounts 1.9.2
- Az.Attestation 0.1.8

執行下列命令來確認已安裝的所有 Az 模組版本 

```powershell
Get-InstalledModule
```
如果版本不符合最低需求，請執行 Update-Module 命令。

## <a name="sign-in-to-azure"></a>登入 Azure

在 PowerShell 主控台中登入 Azure (未提高存取權限)。

```powershell
Connect-AzAccount
```

如有需要，請切換至要用於 Azure 證明的訂用帳戶。

```powershell
Set-AzContext -Subscription <subscription id>  
```

## <a name="register-microsoftattestation-resource-provider"></a>註冊 Microsoft.Attestation 資源提供者

在訂用帳戶中註冊 Microsoft.Attestation 資源提供者。 如需有關 Azure 資源提供者以及如何設定和管理資源提供者的詳細資訊，請參閱 [Azure 資源提供者和類型](../azure-resource-manager/management/resource-providers-and-types.md)。 請注意，一個訂用帳戶只需要註冊資源提供者一次。

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Attestation
```
## <a name="regional-availability-of-azure-attestation"></a>Azure 證明的區域可用性

```powershell
(Get-AzResourceProvider -ProviderNamespace Microsoft.Attestation)[0].Locations
```

## <a name="create-an-azure-resource-group"></a>建立 Azure 資源群組

為證明提供者建立資源群組。 請注意，您可以將其他 Azure 資源 (包括具有用戶端應用程式執行個體的虛擬機器) 放在相同的資源群組中。

```powershell
$location = "uksouth" 
$attestationResourceGroup = "<attestation provider resource group name>"
New-AzResourceGroup -Name $attestationResourceGroup -Location $location 
```

## <a name="create-and-manage-an-attestation-provider"></a>建立和管理證明提供者

New-AzAttestation 會建立證明提供者。

```powershell
$attestationProvider = "<attestation provider name>" 
New-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Location $location
```

PolicySignerCertificateFile 是用來指定一組可信簽署金鑰的檔案。 如果針對 PolicySignerCertificateFile 參數指定檔案名稱，則只能使用已簽署 JWT 格式的原則來設定證明提供者。 除此之外，您可以使用文字或未簽署的 JWT 格式來設定原則。

```powershell
New-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Location $location -PolicySignersCertificateFile "C:\test\policySignersCertificates.pem"
```

如需 PolicySignersCertificateFile 範例，請參閱[原則簽署者憑證的範例](policy-signer-examples.md)。

Get-AzAttestation 會擷取證明提供者屬性，例如 status 和 AttestURI。 請記下 AttestURI，因為稍後需要用到。

```azurepowershell
Get-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup  
```

上述命令應該會產生如下所示的輸出： 

```
Id:/subscriptions/MySubscriptionID/resourceGroups/MyResourceGroup/providers/Microsoft.Attestation/attestationProviders/MyAttestationProvider
Location: MyLocation
ResourceGroupName: MyResourceGroup
Name: MyAttestationProvider
Status: Ready
TrustModel: AAD
AttestUri: https://MyAttestationProvider.us.attest.azure.net 
Tags: 
TagsTable: 
```

您可以使用 Remove-AzAttestation Cmdlet 來刪除證明提供者。  

``azurepowershell Remove-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup
```

## Policy management

In order to manage policies, an Azure AD user requires the following permissions for "Actions":
- Microsoft.Attestation/attestationProviders/attestation/read
- Microsoft.Attestation/attestationProviders/attestation/write
- Microsoft.Attestation/attestationProviders/attestation/delete

These permissions can be assigned to an AD user through a role such as "Owner" (wildcard permissions), "Contributor" (wildcard permissions) or "Attestation Contributor" (specific permissions for Azure Attestation only).  

In order to read policies, an Azure AD user requires the following permission for "Actions":
- Microsoft.Attestation/attestationProviders/attestation/read

This permission can be assigned to an AD user through a role such as "Reader" (wildcard permissions) or "Attestation Reader" (specific permissions for Azure Attestation only).

Below PowerShell cmdlets provide policy management for an attestation provider (one TEE at a time).

Get-AzAttestationPolicy returns the current policy for the specified TEE. The cmdlet displays policy in both text and JWT format of the policy.

```powershell
$teeType = "<tee Type>"
Get-AzAttestationPolicy   -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType 
```

支援的 TEE 類型為 "sgxenclave" 和 "vbsenclave"。

Set-AttestationPolicy 會為指定的 TEE 設定新原則。 此 Cmdlet 會接受文字或 JWT 格式的原則，而且會由 PolicyFormat 參數所控制。 "Text" 是 PolicyFormat 的預設值。 

```powershell
$policyFormat = "<policy format>"
$policy=Get-Content -path "C:\test\policy.txt" -Raw
Set-AzAttestationPolicy   -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType -Policy $policy -PolicyFormat $policyFormat 
```

如果在建立證明提供者期間提供 PolicySignerCertificateFile，則只能使用已簽署的 JWT 格式來設定原則。 除此之外，您可以使用文字或未簽署的 JWT 格式來設定原則。

JWT 格式的證明原則必須包含名為 "AttestationPolicy" 的宣告。 針對已簽署的原則，JWT 必須使用與任何現有原則簽署者憑證對應的私密金鑰進行簽署。

如需原則範例，請參閱[證明原則的範例](policy-examples.md)。

Reset-AzAttestationPolicy 會將指定 TEE 的原則重設為預設值。

```powershell
Reset-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType 
```

## <a name="policy-signer-certificates-management"></a>原則簽署者憑證管理

以下 PowerShell Cmdlet 會為證明提供者提供原則簽署者憑證管理：

```powershell
Get-AzAttestationPolicySigners -Name $attestationProvider -ResourceGroupName $attestationResourceGroup

Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Signer <signer>

Remove-AzAttestationPolicySigner -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Signer <signer>
```

原則簽署者憑證是已簽署的 JWT，具有名為 "maa-policyCertificate" 的宣告。 宣告的值是 JWK，其中包含要新增的可信任簽署金鑰。 JWT 必須使用與任何現有原則簽署者憑證對應的私密金鑰進行簽署。

請注意，原則簽署者憑證的所有語義操作都必須在 PowerShell 外部完成。 就 PowerShell 而言，這是一個簡單的字串。

如需原則簽署者憑證範例，請參閱[原則簽署者憑證的範例](policy-signer-examples.md)。

如需 Cmdlet 和其參數的詳細資訊，請參閱 [Azure 證明 PowerShell Cmdlet](/powershell/module/az.attestation/?view=azps-4.3.0#attestation) 

## <a name="next-steps"></a>後續步驟

- [如何撰寫和簽署證明原則](author-sign-policy.md)
- [使用程式碼範例證明 SGX 記憶體保護區](https://docs.microsoft.com/samples/browse/?expanded=azure&terms=attestation)

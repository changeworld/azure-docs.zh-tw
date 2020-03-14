---
title: 建立 Azure 應用程式身分識別（PowerShell） |Azure
titleSuffix: Microsoft identity platform
description: 描述如何使用 Azure PowerShell 建立 Azure Active Directory 應用程式和服務主體，並透過角色型存取控制將存取權授與資源。 它示範如何使用憑證來驗證應用程式。
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: d2caf121-9fbe-4f00-bf9d-8f3d1f00a6ff
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.date: 10/10/2019
ms.author: ryanwi
ms.reviewer: tomfitz
ms.openlocfilehash: 8e428732fb49d27e3991071b87abee53b6e375b2
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262954"
---
# <a name="how-to-use-azure-powershell-to-create-a-service-principal-with-a-certificate"></a>操作說明：使用 Azure PowerShell 建立具有憑證的服務主體

當您有 App 或指令碼需要存取資源時，可以設定 App 的身分識別，並使用 App 自己的認證進行驗證。 此身分識別就是所謂的服務主體。 這種方法可讓您︰

* 將權限指派給不同於您自己權限的 App 身分識別。 一般而言，這些權限只會限制為 App 必須執行的確切權限。
* 使用憑證在執行無人看管的指令碼時進行驗證。

> [!IMPORTANT]
> 若不要建立服務主體，可考慮使用 Azure 資源的受控識別作為應用程式識別碼。 若您的程式碼在支援受控識別的服務上執行，且存取的資源支援 Azure Active Directory (Azure AD) 驗證，則受控識別會是更好的選擇。 若要深入了解 Azure 資源的受控識別，包含目前哪些服務支援該功能，請參閱[什麼是適用於 Azure 資源的受控識別？](../managed-identities-azure-resources/overview.md)。

本文會示範如何建立可透過憑證進行驗證的服務主體。 若要使用密碼設定服務主體，請參閱[使用 Azure PowerShell 建立 Azure 服務主體](/powershell/azure/create-azure-service-principal-azureps)。

您必須擁有[最新版](/powershell/azure/install-az-ps)的 PowerShell 以符合本文需求。

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="required-permissions"></a>所需的權限

若要完成本文，您必須在 Azure AD 和 Azure 訂用帳戶中有足夠的權限。 具體來說，您必須能夠在 Azure AD 中建立應用程式，並將服務主體指派給角色。

檢查您的帳戶是否具有足夠的權限，最簡單的方式是透過入口網站。 請參閱[檢查必要的權限](howto-create-service-principal-portal.md#required-permissions)。

## <a name="assign-the-application-to-a-role"></a>指派角色給應用程式
若要存取您的訂用帳戶中的資源，您必須將應用程式指派給角色。 決定哪個角色可提供應用程式的適當權限。 若要深入了解可用的角色，請參閱 [RBAC：內建角色](/azure/role-based-access-control/built-in-roles)。

您可以針對訂用帳戶、資源群組或資源的層級設定範圍。 較低的範圍層級會繼承較高層級的權限。 例如，將應用程式新增至資源群組的*讀取*者角色，表示它可以讀取資源群組及其包含的任何資源。 若要允許應用程式執行如 [重新開機]、[啟動和停止實例] 等動作，請選取 [*參與者*] 角色。

## <a name="create-service-principal-with-self-signed-certificate"></a>使用自我簽署憑證建立服務主體

下列範例涵蓋簡單的案例。 它會使用[new-azadserviceprincipal](/powershell/module/az.resources/new-azadserviceprincipal)來建立具有自我簽署憑證的服務主體，並使用[New-azroleassignment](/powershell/module/az.resources/new-azroleassignment)將「[讀取](/azure/role-based-access-control/built-in-roles#reader)者」角色指派給服務主體。 角色指派的範圍僅限於您目前所選的 Azure 訂用帳戶。 若要選取不同的訂用帳戶，請使用 [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext)。

> [!NOTE]
> PowerShell Core 目前不支援 SelfSignedCertificate Cmdlet 和 PKI 模組。 

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" `
  -Subject "CN=exampleappScriptCert" `
  -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

$sp = New-AzADServicePrincipal -DisplayName exampleapp `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
Sleep 20
New-AzRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $sp.ApplicationId
```

範例會休眠 20 秒，留一些時間讓新的服務主體在整個 Azure AD 中傳播。 如果您的指令碼等得不夠久，您會看到錯誤指出：「主體 {ID} 不存在於目錄 {DIR-ID} 中」。 若要解決這個錯誤，請稍待片刻後，再次執行 **New-AzRoleAssignment** 命令。

您可以使用 **ResourceGroupName** 參數將角色指派的範圍限定為特定的資源群組。 您也可以使用 **ResourceType** 與 **ResourceName** 將範圍限制為特定的資源。 

如果您**不是執行 Windows 10 或 Windows Server 2016**，則必須從 Microsoft 指令碼中心下載[自我簽署憑證產生器](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/)。 將其內容解壓縮，並匯入您需要的 Cmdlet。

```powershell
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```

在指令碼中，取代下列兩行來產生憑證。

```powershell
New-SelfSignedCertificateEx -StoreLocation CurrentUser `
  -Subject "CN=exampleapp" `
  -KeySpec "Exchange" `
  -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>透過自動化的 PowerShell 指令碼提供憑證

每當您以服務主體的形式登入時，都需要提供 AD 應用程式目錄的租用戶識別碼。 租用戶是 Azure AD 的執行個體。

```powershell
$TenantId = (Get-AzSubscription -SubscriptionName "Contoso Default").TenantId
$ApplicationId = (Get-AzADApplication -DisplayNameStartWith exampleapp).ApplicationId

 $Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -eq "CN=exampleappScriptCert" }).Thumbprint
 Connect-AzAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>使用憑證授權中心的憑證來建立服務主體

下列範例使用憑證授權中心所發行的憑證來建立服務主體。 指派範圍僅限於指定的 Azure 訂用帳戶。 它會將服務主體新增至 [[讀取](../../role-based-access-control/built-in-roles.md#reader)者] 角色。 如果角色指派期間發生錯誤，則指派會重試。

```powershell
Param (
 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword
 )

 Connect-AzAccount
 Import-Module Az.Resources
 Set-AzContext -Subscription $SubscriptionId
 
 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force

 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

 $ServicePrincipal = New-AzADServicePrincipal -DisplayName $ApplicationDisplayName
 New-AzADSpCredential -ObjectId $ServicePrincipal.Id -CertValue $KeyValue -StartDate $PFXCert.NotBefore -EndDate $PFXCert.NotAfter
 Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $ServicePrincipal.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
 
 $NewRole
```

### <a name="provide-certificate-through-automated-powershell-script"></a>透過自動化的 PowerShell 指令碼提供憑證
每當您以服務主體的形式登入時，都需要提供 AD 應用程式目錄的租用戶識別碼。 租用戶是 Azure AD 的執行個體。

```powershell
Param (
 
 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword,
 
 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
 $PFXCert = New-Object `
  -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 `
  -ArgumentList @($CertPath, $CertPassword)
 $Thumbprint = $PFXCert.Thumbprint

 Connect-AzAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

應用程式識別碼和租用戶識別碼不區分大小寫，因此您可以直接將它們內嵌在您的指令碼中。 如果您要擷取租用戶識別碼，請使用︰

```powershell
(Get-AzSubscription -SubscriptionName "Contoso Default").TenantId
```

如果您要擷取應用程式識別碼，請使用︰

```powershell
(Get-AzADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="change-credentials"></a>管理認證

若要變更 AD App 的認證，不管是因為安全性危害或認證過期，請使用 [Remove-AzADAppCredential](/powershell/module/az.resources/remove-azadappcredential) 和 [New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential) Cmdlet。

若要移除應用程式的所有認證，使用︰

```powershell
Get-AzADApplication -DisplayName exampleapp | Remove-AzADAppCredential
```

若要新增憑證值，請建立自我簽署的憑證，如本文中所述。 然後，使用︰

```powershell
Get-AzADApplication -DisplayName exampleapp | New-AzADAppCredential `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
```

## <a name="debug"></a>偵錯

建立服務主體時，您可能會遇到下列錯誤︰

* **Authentication_Unauthorized」** 或 **「在內容中找不到訂用帳戶。」** - 當您的帳戶在 Azure AD 上未具備註冊應用程式的[必要權限](#required-permissions)時，您就會看到此錯誤。 通常，只有在 Azure Active Directory 中的系統管理使用者可以註冊應用程式，且您的帳戶不是系統管理員時，您才會看到此錯誤。請要求您的系統管理員將您指派給系統管理員角色，或讓使用者註冊應用程式。

* 您的帳戶 **「沒有在範圍 '/subscriptions/{guid}' 中執行 'Microsoft.Authorization/roleAssignments/write' 動作的權限。」** - 當您的帳戶沒有足夠權限可將角色指派給身分識別時，您就會看到此錯誤。 要求訂用帳戶管理員將您新增至「使用者存取系統管理員」角色。

## <a name="next-steps"></a>後續步驟

* 若要使用密碼設定服務主體，請參閱[使用 Azure PowerShell 建立 Azure 服務主體](/powershell/azure/create-azure-service-principal-azureps)。
* 如需應用程式和服務主體的詳細說明，請參閱[應用程式物件和服務主體物件](app-objects-and-service-principals.md)。
* 如需 Azure AD 驗證的詳細資訊，請參閱 [Azure AD 的驗證案例](authentication-scenarios.md)。

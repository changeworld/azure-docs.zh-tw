---
title: 管理 Azure 自動化執行身分帳戶
description: 本文說明如何使用 PowerShell 或從 Azure 入口網站管理您的執行身分帳戶。
services: automation
ms.subservice: shared-capabilities
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: d85eac7af71f03be384b8fa9d9603525c0807dac
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83770783"
---
# <a name="manage-an-azure-automation-run-as-account"></a>管理 Azure 自動化執行身分帳戶

Azure 自動化中的執行身分帳戶可提供驗證，以使用 Azure Cmdlet 來管理 Azure 中的資源。 當您建立執行身分帳戶時，其會在 Azure Active Directory (AD) 中建立新的服務主體使用者，並在訂用帳戶層級將參與者角色指派給這位使用者。

## <a name="types-of-run-as-accounts"></a>執行身分帳戶的類型

Azure 自動化使用兩種類型的執行身分帳戶：

* Azure 執行身分帳戶
* Azure 傳統執行身分帳戶

>[!NOTE]
>Azure 雲端方案提供者 (CSP) 訂用帳戶僅支援 Azure Resource Manager 模型。 非 Azure Resource Manager 服務不在方案中。 當您使用 CSP 訂用帳戶時，並不會建立 Azure 傳統執行身分帳戶，但會建立 Azure 執行身分帳戶。 若要深入了解 CSP 訂用帳戶，請參閱 [CSP 訂用帳戶中可用的服務](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services)。

執行身分帳戶的服務主體依預設並沒有讀取 Azure AD 的權限。 如果您想要新增讀取或管理 Azure AD 的權限，您必須在 [API 權限] 下授與服務主體的權限。 若要深入了解，請參閱[新增存取 Web API 的權限](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)。

### <a name="run-as-account"></a>執行身分帳戶

執行身分帳戶會管理 [Resource Manager 部署模型](../azure-resource-manager/management/deployment-models.md)資源。 其會執行下列步驟。

* 建立可使用自我簽署憑證或企業憑證的公開金鑰進行匯出的 Azure AD 應用程式、建立此應用程式在 Azure AD 中的服務主體帳戶，並在目前的訂用帳戶中為此帳戶指派參與者角色。 您可以將憑證設定變更為擁有者或任何其他角色。 如需詳細資訊，請參閱 [Azure 自動化中的角色型存取控制](automation-role-based-access-control.md)。
  
* 在指定的自動化帳戶中，建立名為 `AzureRunAsCertificate` 的自動化憑證資產。 憑證資產會保存 Azure AD 應用程式所使用的憑證私密金鑰。
  
* 在指定的自動化帳戶中，建立名為 `AzureRunAsConnection` 的自動化連線資產。 連線資產會保存應用程式識別碼、租用戶識別碼、訂閱識別碼，以及憑證指紋。

### <a name="azure-classic-run-as-account"></a>Azure 傳統執行身分帳戶

Azure 傳統執行身分帳戶會管理[傳統部署模型](../azure-resource-manager/management/deployment-models.md)資源。 您必須是訂用帳戶的共同管理員，才能建立或更新這類型的帳戶。

Azure 傳統執行身分帳戶會執行下列工作。

  * 在訂用帳戶中建立管理憑證。

  * 在指定的自動化帳戶中，建立名為 `AzureClassicRunAsCertificate` 的自動化憑證資產。 憑證資產會保存管理憑證所使用的憑證私密金鑰。

  * 在指定的自動化帳戶中，建立名為 `AzureClassicRunAsConnection` 的自動化連線資產。 連線資產會保存訂用帳戶名稱、訂用帳戶識別碼，以及憑證資產名稱。

>[!NOTE]
>當您建立自動化帳戶時，依預設並不會建立 Azure 傳統執行身分帳戶。 此帳戶會依照本文稍後說明的步驟個別建立。

## <a name="obtain-run-as-account-permissions"></a><a name="permissions"></a>取得執行身分帳戶權限

本節將定義一般執行身分帳戶和傳統執行身分帳戶的權限。

### <a name="get-permissions-to-configure-run-as-accounts"></a>取得設定執行身分帳戶的權限

若要建立或更新執行身分帳戶，您必須擁有特定的權限和使用權限。 Azure Active Directory 中的應用程式系統管理員和訂用帳戶中的擁有者可以完成所有工作。 針對有劃分職責的情況，下表顯示工作、對等的 Cmdlet 及所需權限的清單：

|Task|Cmdlet  |最低權限  |設定權限的位置|
|---|---------|---------|---|
|建立 Azure AD 應用程式|[New-AzADApplication](https://docs.microsoft.com/powershell/module/az.resources/new-azadapplication)     | 應用程式開發人員角色<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>首頁 > Azure AD > 應用程式註冊 |
|將認證新增至應用程式。|[New-AzADAppCredential](https://docs.microsoft.com/powershell/module/az.resources/new-azadappcredential)     | 應用程式系統管理員或全域管理員<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>首頁 > Azure AD > 應用程式註冊|
|建立並取得 Azure AD 服務主體|[New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal)     | 應用程式系統管理員或全域管理員<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>首頁 > Azure AD > 應用程式註冊|
|指派或取得指定主體的 RBAC 角色|[New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](https://docs.microsoft.com/powershell/module/Az.Resources/Get-AzRoleAssignment)      | 使用者存取系統管理員或擁有者具有下列權限：</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [訂用帳戶](../role-based-access-control/role-assignments-portal.md)</br>[首頁] > [訂用帳戶] > [\<訂用帳戶名稱\> - 存取控制 (IAM)]|
|建立或移除自動化憑證|[New-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationcertificate)     | 資源群組的參與者         |自動化帳戶資源群組|
|建立或移除自動化連線|[New-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationconnection)|資源群組的參與者 |自動化帳戶資源群組|

<sup>1</sup> 如果 Azure AD 租用戶在 [使用者設定] 頁面中的 [使用者可以註冊應用程式] 選項設定為 [是]，Azure AD 租用戶中的非管理使用者就可以[註冊 AD 應用程式](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)。 如果應用程式註冊設定 [否]，則執行此動作的使用者必須如此表中所定義。

如果您在被新增至訂用帳戶的全域管理員角色之前，並非訂用帳戶 Active Directory 執行個體的成員，系統就會將您新增為來賓。 在此情況下，您會在 [新增自動化帳戶] 頁面上收到 `You do not have permissions to create…` 警告。 

在指派全域管理員角色時，如果您是訂用帳戶 Active Directory 執行個體的成員，您也可能在 [新增自動化帳戶] 頁面上收到 `You do not have permissions to create…` 警告。 在此情況下，您可以要求從訂用帳戶的 Active Directory 執行個體中移除，然後要求重新新增，以便成為 Active Directory 的完整使用者。

若要確認產生錯誤訊息的情況已修正：

1. 在 Azure 入口網站的 [Azure Active Directory] 窗格中，選取 [使用者和群組]。 
2. 選取 [所有使用者]。
3. 選擇您的名稱，然後選取 [設定檔]。 
4. 確定您的使用者設定檔下的 [使用者類型] 屬性值未設定為 [來賓]。

### <a name="get-permissions-to-configure-classic-run-as-accounts"></a><a name="permissions-classic"></a>取得設定傳統執行身分帳戶的權限

若要設定或更新傳統執行身分帳戶，您必須具有訂用帳戶層級的共同管理員角色。 若要深入了解傳統訂用帳戶權限，請參閱 [Azure 傳統訂用帳戶管理員](../role-based-access-control/classic-administrators.md#add-a-co-administrator)。

## <a name="create-a-run-as-account-in-azure-portal"></a>在 Azure 入口網站中建立執行身分帳戶

執行下列步驟以在 Azure 入口網站更新 Azure 自動化帳戶。 個別建立「執行身分帳戶」和「傳統執行身分帳戶」。 如果您不需要管理傳統資源，則可以只建立 Azure 執行身分帳戶。

1. 您必須以訂用帳戶管理員角色成員和訂用帳戶共同管理員的帳戶登入 Azure 入口網站。
2. 搜尋並選取 [自動化帳戶]。
3. 在 [自動化帳戶] 頁面上，從清單中選取您的自動化帳戶。
4. 在左窗格的 [帳戶設定] 區段中，選取 [執行身分帳戶]。
5. 根據您所需的帳戶，選取 [Azure 執行身分帳戶] 或 [Azure 傳統執行身分帳戶]。 
6. 根據涉及的帳戶，使用 [新增 Azure 執行身分] 或 [新增 Azure 傳統執行身分帳戶] 窗格。 在檢閱概觀資訊後，按一下 [建立]。
6. 在 Azure 建立執行身分帳戶時，您可以在功能表的 [通知]  底下追蹤進度。 橫幅也會顯示說明帳戶正在建立。 此程序需要數分鐘的時間完成。

## <a name="create-a-run-as-account-using-powershell"></a>使用 PowerShell 建立執行身分帳戶

下列清單提供在 PowerShell 中建立執行身分帳戶的需求。 這些需求適用於這兩種類型的執行身分帳戶。

* 具有 Azure Resource Manager 模組 3.4.1 和更新版本的 Windows 10 或 Windows Server 2016。 PowerShell 指令碼不支援舊版 Windows。
* Azure PowerShell 1.0 和更新版本。 如需有關 PowerShell 1.0 版本的資訊，請參閱[如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs)。
* 自動化帳戶，系統會將其參照為 `AutomationAccountName` 和 `ApplicationDisplayName` 參數的值。
* 權限相當於[設定執行身分帳戶的必要權限](#permissions)中所列的權限。

若要取得 `SubscriptionId`、`ResourceGroupName` 等項目的值 (這是 PowerShell 指令碼的必要參數)，請完成後續步驟。

1. 在 Azure 入口網站中，選取 [自動化帳戶]。
1. 在 [自動化帳戶] 頁面上，選取 [自動化帳戶]。
1. 在 [帳戶設定] 區段中，選取 [屬性]。
1. 記下 [屬性] 頁面上的**名稱**、**訂用帳戶識別碼**和**資源群組**的值。 這些值分別對應至 `AutomationAccountName`、`SubscriptionId` 和 `ResourceGroupName` PowerShell 指令碼參數的值。

   ![自動化帳戶的屬性頁面](media/manage-runas-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>用來建立執行身分帳戶的 PowerShell 指令碼

本節提供用來建立執行身分帳戶的 PowerShell 指令碼。 此指令碼包含幾項設定的支援。

* 使用自我簽署憑證建立執行身分帳戶。
* 使用自我簽署憑證建立執行身分帳戶和傳統執行身分帳戶。
* 使用企業憑證授權單位 (CA) 所核發的憑證，來建立執行身分帳戶和傳統執行身分帳戶。
* 在 Azure Government 雲端中，使用自我簽署憑證建立執行身分帳戶和傳統執行身分帳戶。

此指令碼會使用多個 Azure Resource Manager Cmdlet 來建立資源。 如需 Cmdlet 和其所需權限的相關資訊，請參閱[取得設定執行身分帳戶的權限](#get-permissions-to-configure-run-as-accounts)。

使用檔案名稱 **New-RunAsAccount.ps1** 將指令碼儲存在電腦上。

```powershell
#Requires -RunAsAdministrator
Param (
    [Parameter(Mandatory = $true)]
    [String] $ResourceGroup,

    [Parameter(Mandatory = $true)]
    [String] $AutomationAccountName,

    [Parameter(Mandatory = $true)]
    [String] $ApplicationDisplayName,

    [Parameter(Mandatory = $true)]
    [String] $SubscriptionId,

    [Parameter(Mandatory = $true)]
    [Boolean] $CreateClassicRunAsAccount,

    [Parameter(Mandatory = $true)]
    [String] $SelfSignedCertPlainPassword,

    [Parameter(Mandatory = $false)]
    [string] $EnterpriseCertPathForRunAsAccount,

    [Parameter(Mandatory = $false)]
    [String] $EnterpriseCertPlainPasswordForRunAsAccount,

    [Parameter(Mandatory = $false)]
    [String] $EnterpriseCertPathForClassicRunAsAccount,

    [Parameter(Mandatory = $false)]
    [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,

    [Parameter(Mandatory = $false)]
    [ValidateSet("AzureCloud", "AzureUSGovernment")]
    [string]$EnvironmentName = "AzureCloud",

    [Parameter(Mandatory = $false)]
    [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
)

function CreateSelfSignedCertificate([string] $certificateName, [string] $selfSignedCertPlainPassword,
    [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
    $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
        -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
        -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired) -HashAlgorithm SHA256

    $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
    Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
    Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose
}

function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {
    $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
    $keyId = (New-Guid).Guid

    # Create an Azure AD application, AD App Credential, AD ServicePrincipal

    # Requires Application Developer Role, but works with Application administrator or GLOBAL ADMIN
    $Application = New-AzADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $keyId)
    # Requires Application administrator or GLOBAL ADMIN
    $ApplicationCredential = New-AzADAppCredential -ApplicationId $Application.ApplicationId -CertValue $keyValue -StartDate $PfxCert.NotBefore -EndDate $PfxCert.NotAfter
    # Requires Application administrator or GLOBAL ADMIN
    $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $Application.ApplicationId
    $GetServicePrincipal = Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id

    # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
    Sleep -s 15
    # Requires User Access Administrator or Owner.
    $NewRole = New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
    $Retries = 0;
    While ($NewRole -eq $null -and $Retries -le 6) {
        Sleep -s 10
        New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
        $NewRole = Get-AzRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries++;
    }
    return $Application.ApplicationId.ToString();
}

function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName, [string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
    $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force
    Remove-AzAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
    New-AzAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
}

function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
    Remove-AzAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
    New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
}

Import-Module AzureRm.Profile
Import-Module AzureRm.Resources

$AureRmProfileVersion = (Get-Module AzureRm.Profile).Version
if (!(($AzureRmProfileVersion.Major -ge 3 -and $AzureRmProfileVersion.Minor -ge 4) -or ($AzureRmProfileVersion.Major -gt 3))) {
    Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
    return
}

# To use the new Az modules to create your Run As accounts, please uncomment the following lines and ensure you comment out the previous 8 lines that import the AzureRM modules to avoid any issues. To learn about about using Az modules in your Automation account see https://docs.microsoft.com/azure/automation/az-modules.

# Import-Module Az.Automation
# Enable-AzureRmAlias


Connect-AzAccount -Environment $EnvironmentName
$Subscription = Get-AzSubscription -SubscriptionId $SubscriptionId | Set-AzContext

# Create a Run As account by using a service principal
$CertifcateAssetName = "AzureRunAsCertificate"
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionTypeName = "AzureServicePrincipal"

if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
    $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
    $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
}
else {
    $CertificateName = $AutomationAccountName + $CertifcateAssetName
    $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
    $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
    $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
    CreateSelfSignedCertificate $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
}

# Create a service principal
$PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
$ApplicationId = CreateServicePrincipal $PfxCert $ApplicationDisplayName

# Create the Automation certificate asset
CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

# Populate the ConnectionFieldValues
$SubscriptionInfo = Get-AzSubscription -SubscriptionId $SubscriptionId
$TenantID = $SubscriptionInfo | Select TenantId -First 1
$Thumbprint = $PfxCert.Thumbprint
$ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId}

# Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

if ($CreateClassicRunAsAccount) {
    # Create a Run As account by using a service principal
    $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
    $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
    $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
    $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
    "Log in to the Microsoft Azure portal (https://portal.azure.com) and select Subscriptions -> Management Certificates." + [Environment]::NewLine +
    "Then click Upload and upload the .cer format of #CERT#"

    if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
        $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
        $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
        $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
    }
    else {
        $ClassicRunAsAccountCertificateName = $AutomationAccountName + $ClassicRunAsAccountCertifcateAssetName
        $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
        $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
        $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
        $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
        CreateSelfSignedCertificate $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
    }
    
    # Create the Automation certificate asset
    CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

    # Populate the ConnectionFieldValues
    $SubscriptionName = $subscription.Name
    $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

    # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
    CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName   $ClassicRunAsAccountConnectionFieldValues

    Write-Host -ForegroundColor red       $UploadMessage
}
```

>[!NOTE]
>`Add-AzAccount` 和 `Add-AzureRMAccount` 是 [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) 的別名。 您可使用這些 Cmdlet，也可以在您的自動化帳戶中[將您的模組更新](automation-update-azure-modules.md)為最新版本。 即使您才剛建立新的自動化帳戶，可能還是需要更新您的模組。

### <a name="execute-the-powershell-script"></a>執行 PowerShell 指令碼

1. 在電腦上以提高的使用者權限從 [開始] 畫面啟動 **Windows PowerShell**。
1. 從提高權限的命令列殼層，移至包含您的指令碼所在的資料夾。
1. 使用所需設定的參數值來執行指令碼。
1. 如果您建立傳統執行方式帳戶，在指令碼執行之後，請將公開憑證 ( **.cer** 副檔名) 上傳至自動化帳戶建立所在之訂用帳戶的管理存放區中。

指令碼執行之後，系統會提您向 Azure 進行驗證。 請以訂用帳戶管理員角色成員和訂用帳戶共同管理員的帳戶登入。

#### <a name="create-a-run-as-account-by-using-a-self-signed-certificate"></a>使用自我簽署憑證建立執行身分帳戶

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate"></a>使用自我簽署憑證建立執行身分帳戶和傳統執行身分帳戶

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-an-enterprise-certificate"></a>使用企業憑證建立執行身分帳戶和傳統執行身分帳戶

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
```

如果您使用企業公開憑證 ( **.cer** 檔案) 建立了傳統執行身分帳戶，請使用此憑證。 請參閱[將管理 API 憑證上傳至 Azure 入口網站](../azure-api-management-certs.md)。

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate-in-the-azure-government-cloud"></a>在 Azure Government 雲端中，使用自我簽署憑證建立執行身分帳戶和傳統執行身分帳戶

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
```

如果您使用自我簽署的公開憑證 ( **.cer** 檔案) 建立了傳統執行身分帳戶，指令碼會建立該檔案，並將其儲存在電腦上的暫存檔案資料夾中。 您可以在用來執行 PowerShell 工作階段的使用者設定檔 `%USERPROFILE%\AppData\Local\Temp` 中找到該檔案。

## <a name="delete-a-run-as-or-classic-run-as-account"></a>刪除執行身分或傳統執行身分帳戶

本節說明如何刪除執行身分或傳統執行身分帳戶。 當您執行此動作時，系統不會保留自動化帳戶。 刪除帳戶後，您可以在 Azure 入口網站中重新加以建立。

1. 在 Azure 入口網站中，開啟自動化帳戶。

2. 在左窗格的 [帳戶設定] 區段中，選取 [執行身分帳戶]。

3. 在 [執行身分帳戶] 屬性頁面中，選取您想要刪除的執行身分帳戶或傳統執行身分帳戶。 

4. 在所選帳戶的 [屬性] 窗格上，按一下 [刪除]。

   ![刪除執行身分帳戶](media/manage-runas-account/automation-account-delete-runas.png)

5. 刪除帳戶時，您可以在功能表的 [通知] 底下追蹤進度。

6. 帳戶刪除之後，您可以在 [執行身分帳戶] 屬性頁面中，選取建立選項 [Azure 執行身分帳戶] 重新加以建立。

   ![重新建立自動化執行身分帳戶](media/manage-runas-account/automation-account-create-runas.png)

## <a name="renew-a-self-signed-certificate"></a><a name="cert-renewal"></a>更新自我簽署憑證

您為執行身分帳戶建立的自我簽署憑證，會在建立日起算一年後到期。 有時您必須在執行身分帳戶到期前更新憑證。 您可以在該憑證到期前隨時更新憑證。 

當您更新自我簽署憑證時，系統會保留目前的有效憑證，以確保已排入佇列或正在執行以及使用該「執行身分」帳戶進行驗證的所有 Runbook，都不會受到負面影響。 憑證在到期日之前會保持有效。

>[!NOTE]
>如果您認為執行身分帳戶遭到盜用，您可以刪除自我簽署憑證並重新加以建立。

>[!NOTE]
>如果您已將執行身分帳戶設定成使用您的企業憑證授權單位所核發的憑證，且您使用此選項來更新自我簽署憑證選項，則該企業憑證會由自我簽署憑證所取代。

使用下列步驟來更新自我簽署憑證。

1. 在 Azure 入口網站中，開啟自動化帳戶。

1. 在 [帳戶設定] 區段中，選取 [執行身分帳戶]。

    ![自動化帳戶的屬性窗格](media/manage-runas-account/automation-account-properties-pane.png)

1. 在 [執行身分帳戶] 屬性頁面中，選取要更新憑證的執行身分帳戶或傳統執行身分帳戶。

1. 在所選帳戶的 [屬性] 窗格上，按一下 [更新憑證]。

    ![更新執行身分帳戶的憑證](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. 更新憑證時，您可以在功能表的 [通知] 底下追蹤進度。

## <a name="set-up-automatic-certificate-renewal-with-an-automation-runbook"></a><a name="auto-cert-renewal"></a>使用自動化 Runbook 設定自動憑證更新

若要自動更新憑證，您可以使用自動化 Runbook。 [GitHub](https://github.com/ikanni/PowerShellScripts/blob/master/AzureAutomation/RunAsAccount/GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1) 上的這個指令碼會在您的自動化帳戶中啟用這項功能。

>[!NOTE]
>您必須是 Azure AD 中的全域管理員或公司系統管理員，才能執行此指令碼。

此指令碼會建立每週排程，以更新執行身分帳戶憑證。 此指令碼會將 **Update-AutomationRunAsCredential** Runbook 新增至您的自動化帳戶。 您可以在 GitHub 上的指令碼 [Update-AutomationRunAsCredential.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AutomationRunAsCredential.ps1) 中檢視 Runbook 程式碼。 您可以視需要使用檔案中的 PowerShell 程式碼來手動更新憑證。

使用下列步驟可立即測試更新程序。

1. 編輯 **Update-AutomationRunAsCredential** Runbook，並在第 122 行上的 **Exit(1)** 命令前面放置註解字元 (#)。

   ```powershell
   #Exit(1)
   ```

2. 發佈 Runbook。
3. 啟動 Runbook。
4. 使用下列程式碼確認更新成功：

   ```powershell
   (Get-AzAutomationCertificate -AutomationAccountName TestAA
                                -Name AzureRunAsCertificate
                                -ResourceGroupName TestAutomation).ExpiryTime.DateTime
   ```
    輸出：

   ```Output
   Thursday, November 7, 2019 7:00:00 PM
   ```

5. 測試完成後，請編輯 Runbook，並移除您在步驟 1 中新增的註解字元。
6. 發佈 Runbook。

## <a name="limit-run-as-account-permissions"></a>限制執行身分帳戶權限

若要對 Azure 中的資源控制自動化的目標，您可以執行 [Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8) 指令碼。 此指令碼會變更您現有的執行身分帳戶服務主體，以建立及使用自訂角色定義。 此角色具有所有資源的權限 ([Key Vault](https://docs.microsoft.com/azure/key-vault/) 除外)。

>[!IMPORTANT]
>執行 **Update-AutomationRunAsAccountRoleAssignments.ps1** 指令碼之後，使用執行身分帳戶存取 Key Vault 的 Runbook 即無法再運作。 執行指令碼之前，您應先檢閱帳戶中用來呼叫 Azure Key Vault 的 Runbook。 若要啟用 Azure 自動化 Runbook 對 Key Vault 的存取，您必須[將執行身分帳戶新增至 Key Vault 的權限](#add-permissions-to-key-vault)。

如果您需要限制執行身分服務主體可執行的進一步動作，您可以將其他資源類型新增至自訂角色定義的 `NotActions` 元素。 下列範例會限制對 `Microsoft.Compute/*` 的存取。 如果您將此資源類型新增至角色定義的 `NotActions`，該角色即無法存取任何計算資源。 若要深入了解角色定義，請參閱[了解 Azure 資源的角色定義](../role-based-access-control/role-definitions.md)。

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

您可以確認您的執行身分帳戶所使用的服務主體是在參與者角色定義還是自訂角色中。 

1. 移至您的自動化帳戶，然後選取 [帳戶設定] 區段中的 [執行身分帳戶]。
2. 選取 [Azure 執行身分帳戶]。 
3. 選取 [角色]，以找出正在使用的角色定義。

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

您也可以針對多個訂用帳戶或自動化帳戶，確認執行身分帳戶所使用的角色定義。 此動作可使用 PowerShell 資源庫中的 [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) 指令碼來完成。

### <a name="add-permissions-to-key-vault"></a>將權限新增至 Key Vault

您可以允許 Azure 自動化確認 Key Vault 和您的執行身分帳戶服務主體是否使用自訂角色定義。 您必須：

* 將權限授與 Key Vault。
* 設定存取原則。

您可以使用 PowerShell 資源庫中的 [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) 指令碼，將您的執行身分帳戶權限授與 Key Vault。 如需在 Key Vault 上設定權限的詳細資訊，請參閱[授與應用程式對金鑰保存庫的存取權](../key-vault/general/group-permissions-for-apps.md)。

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>解決執行身分帳戶設定錯誤的問題

在初始設定期間，可能會不當刪除或建立要讓執行身分或傳統執行身分帳戶正常運作所需的某些設定項目。 可能的設定錯誤實例包括：

* 憑證資產
* 連線資產
* 從參與者角色中移除了執行身分帳戶
* Azure AD 中的服務主體或應用程式

在這類設定錯誤實例中，自動化帳戶會偵測到變更，並在帳戶的 [執行身分帳戶] 屬性頁面上顯示 [不完整] 狀態。

![不完整的執行身分帳戶設定狀態](media/manage-runas-account/automation-account-runas-incomplete-config.png)

當您選取執行身分帳戶時，帳戶的 [屬性] 窗格會顯示下列錯誤訊息：

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

您可以藉由刪除並重新建立這些執行身分帳戶，來快速解決帳戶的問題。

## <a name="next-steps"></a>後續步驟

* [應用程式物件和服務主體物件](../active-directory/develop/app-objects-and-service-principals.md)。
* [Azure 雲端服務的憑證概觀](../cloud-services/cloud-services-certs-create.md)。

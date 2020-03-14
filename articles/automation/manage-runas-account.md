---
title: 管理 Azure 自動化執行身分帳戶
description: 本文說明如何使用 PowerShell 或從入口網站管理您的執行身分帳戶。
services: automation
ms.subservice: shared-capabilities
ms.date: 05/24/2019
ms.topic: conceptual
ms.openlocfilehash: d3d58765aafcaa15491a30ecc8d3e7da6a78662d
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/14/2020
ms.locfileid: "79366933"
---
# <a name="manage-azure-automation-run-as-accounts"></a>管理 Azure 自動化執行身分帳戶

中的執行身分帳戶 Azure 自動化提供使用 Azure Cmdlet 在 Azure 中管理資源的驗證。 當您建立執行身分帳戶時，它會在 Azure Active Directory （AD）中建立新的服務主體使用者，並在訂用帳戶層級將參與者角色指派給此使用者。 對於在 Azure 虛擬機器上使用混合式 Runbook 背景工作角色的 Runbook，您可以使用 [Azure 資源的受控識別](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)來向 Azure 資源進行驗證，而非使用執行身分帳戶。

執行身分帳戶的服務主體預設沒有讀取 Azure AD 的許可權。 如果您想要新增讀取或管理 Azure AD 的許可權，您必須在 [ **API 許可權**] 下授與服務主體的許可權。 若要深入瞭解，請參閱[新增存取 Web api 的許可權](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需混合式 Runbook 背景工作角色上的 Az module 安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="types-of-run-as-accounts"></a>執行身分帳戶的類型

Azure 自動化使用兩種類型的執行身分帳戶：

* Azure 執行身分帳戶
* Azure 傳統執行身分帳戶

>[!NOTE]
>Azure 雲端解決方案提供者（CSP）訂用帳戶僅支援 Azure Resource Manager 模型。 程式中無法使用非 Azure Resource Manager 的服務。 當您使用 CSP 訂用帳戶時，並不會建立 Azure 傳統執行身分帳戶，但會建立 Azure 執行身分帳戶。 若要深入了解 CSP 訂用帳戶，請參閱 [CSP 訂用帳戶中可用的服務](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services)。

### <a name="run-as-account"></a>執行身分帳戶

執行身分帳戶會管理[Resource Manager 部署模型](../azure-resource-manager/management/deployment-models.md)資源。 它會執行下列工作。

* 建立可使用自我簽署憑證或企業憑證的公開金鑰進行匯出的 Azure AD 應用程式、建立此應用程式在 Azure AD 中的服務主體帳戶，並在目前的訂用帳戶中為此帳戶指派參與者角色。 您可以將憑證設定變更為擁有者或任何其他角色。 如需詳細資訊，請參閱 [Azure 自動化中的角色型存取控制](automation-role-based-access-control.md)。
  
* 在指定的自動化帳戶中，建立名為 `AzureRunAsCertificate` 的自動化憑證資產。 憑證資產會保存 Azure AD 應用程式所使用的憑證私密金鑰。
  
* 在指定的自動化帳戶中，建立名為 `AzureRunAsConnection` 的自動化連接資產。 連接資產會保存應用程式識別碼、租使用者識別碼、訂用帳戶識別碼和憑證指紋。

### <a name="azure-classic-run-as-account"></a>Azure 傳統執行身分帳戶

Azure 傳統執行身分帳戶會管理[傳統部署模型](../azure-resource-manager/management/deployment-models.md)資源。 您必須是訂用帳戶的共同管理員，才能建立或更新這種類型的帳戶。

Azure 傳統執行身分帳戶會執行下列工作。

  * 在訂用帳戶中建立管理憑證。

  * 在指定的自動化帳戶中，建立名為 `AzureClassicRunAsCertificate` 的自動化憑證資產。 憑證資產會保存管理憑證所使用的憑證私密金鑰。

  * 在指定的自動化帳戶中，建立名為 `AzureClassicRunAsConnection` 的自動化連接資產。 連接資產會保存訂用帳戶名稱、訂用帳戶識別碼和憑證資產名稱。

## <a name="permissions"></a>執行身分帳戶許可權

本節定義一般執行身分帳戶和傳統執行身分帳戶的許可權。

### <a name="permissions-to-configure-run-as-accounts"></a>設定執行身分帳戶的許可權

若要建立或更新執行身分帳戶，您必須擁有特定的權限和使用權限。 Azure Active Directory 中的應用程式系統管理員，以及訂用帳戶中的擁有者可以完成所有工作。 如果您有責任區隔的情況，下表顯示工作、對等的 Cmdlet 及所需許可權的清單：

|Task|Cmdlet  |最低權限  |設定權限的位置|
|---|---------|---------|---|
|建立 Azure AD 應用程式|[新增-AzADApplication](/https://docs.microsoft.com/powershell/module/az.resources/new-azadapplication?view=azps-3.5.0)     | 應用程式開發人員角色<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>家用 > Azure AD > 應用程式註冊 |
|將認證新增至應用程式。|[新增-AzADAppCredential](https://docs.microsoft.com/powershell/module/az.resources/new-azadappcredential?view=azps-3.5.0)     | 應用程式系統管理員或全域管理員<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>家用 > Azure AD > 應用程式註冊|
|建立並取得 Azure AD 的服務主體|[New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-3.5.0)</br>[New-azadserviceprincipal](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal?view=azps-3.5.0)     | 應用程式系統管理員或全域管理員<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>家用 > Azure AD > 應用程式註冊|
|指派或取得指定主體的 RBAC 角色|[New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment?view=azps-3.5.0)</br>[Get-AzRoleAssignment](https://docs.microsoft.com/powershell/module/Az.Resources/Get-AzRoleAssignment?view=azps-3.5.0)      | 使用者存取系統管理員或擁有者，或擁有下列許可權：</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [訂用帳戶](../role-based-access-control/role-assignments-portal.md)</br>[首頁] > [訂用帳戶] > [\<訂用帳戶名稱\> - 存取控制 (IAM)]|
|建立或移除自動化憑證|[新增-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.5.0)</br>[移除-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationcertificate?view=azps-3.5.0)     | 資源群組的參與者         |自動化帳戶資源群組|
|建立或移除自動化連線|[新增-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.5.0)</br>[移除-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationconnection?view=azps-3.5.0)|資源群組的參與者 |自動化帳戶資源群組|

<sup>1</sup>如果 [使用者設定] 頁面上的 [Azure AD 租**使用者的使用者可以註冊應用程式**] 選項設定為 **[是]** ，則 Azure AD 租使用者中的非系統管理員使用者可以[註冊 AD 應用程式](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)。 如果應用程式註冊設定為 [**否**]，則執行此動作的使用者必須如本表中所定義。

如果您在新增至訂用帳戶的全域管理員角色之前，不是訂閱 Active Directory 實例的成員，則會將您新增為來賓。 在此情況下，您會在 [新增自動化帳戶] 頁面上收到 `You do not have permissions to create…` 警告。 

當指派全域管理員角色時，如果您是訂用帳戶 Active Directory 實例的成員，您也可以在 [新增自動化帳戶] 頁面上收到 `You do not have permissions to create…` 警告。 在此情況下，您可以要求從訂用帳戶的 Active Directory 實例中移除，然後要求重新加入，讓您成為 Active Directory 的完整使用者。

若要確認產生錯誤訊息的情況已補救：

1. 從 Azure 入口網站的 [Azure Active Directory] 窗格中，選取 [**使用者和群組**]。 
2. 選取 [所有使用者]。
3. 選擇您的名稱，然後選取 [**設定檔**]。 
4. 請確定您使用者設定檔下的 [**使用者類型**] 屬性值未設定為 [**來賓**]。

### <a name="permissions-classic"></a>設定傳統執行身分帳戶的許可權

若要設定或更新傳統執行身分帳戶，您必須具有訂用帳戶層級的共同管理員角色。 若要深入瞭解傳統訂用帳戶許可權，請參閱[Azure 傳統訂](../role-based-access-control/classic-administrators.md#add-a-co-administrator)用帳戶管理員。

## <a name="creating-a-run-as-account-in-azure-portal"></a>在 Azure 入口網站中建立執行身分帳戶

請執行下列步驟，以在 Azure 入口網站中更新您的 Azure 自動化帳戶。 個別建立執行身分和傳統執行身分帳戶。 如果您不需要管理傳統資源，則可以只建立 Azure 執行身分帳戶。

1. 您必須以訂用帳戶管理員角色成員和訂用帳戶共同管理員的帳戶登入 Azure 入口網站。
2. 搜尋並選取 [**自動化帳戶**]。
3. 在 [自動化帳戶] 頁面上，從清單中選取您的自動化帳戶。
4. 在左窗格中，選取 [帳戶設定] 區段中的 [**執行身分帳戶**]。
5. 根據您所需的帳戶，選取 [Azure 執行身分帳戶] 或 [Azure 傳統執行身分帳戶]。 
6. 視感的帳戶而定，使用 [**新增 Azure 執行**身分] 或 [**新增 azure 傳統執行身分帳戶**] 窗格。 查看總覽資訊之後，按一下 [**建立**]。
6. 在 Azure 建立執行身分帳戶時，您可以在功能表的 [通知] 底下追蹤進度。 也會顯示橫幅，說明正在建立帳戶。 此程式可能需要幾分鐘的時間才能完成。

## <a name="creating-a-run-as-account-using-powershell"></a>使用 PowerShell 建立執行身分帳戶

下列清單提供在 PowerShell 中建立執行身分帳戶的需求。 這些需求適用于這兩種類型的執行身分帳戶。

* 具有 Azure Resource Manager 模組 3.4.1 和更新版本的 Windows 10 或 Windows Server 2016。 PowerShell 腳本不支援舊版的 Windows。
* Azure PowerShell 1.0 和更新版本。 如需有關 PowerShell 1.0 版本的資訊，請參閱[如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs)。
* 自動化帳戶，其參考為 `AutomationAccountName` 和 `ApplicationDisplayName` 參數的值。
* 許可權，等同于[設定執行身分帳戶所需的許可權](#permissions)中所列的許可權。

若要取得 `SubscriptionId`、`ResourceGroupName`和（這是 PowerShell 腳本的必要參數）的值，請完成後續步驟。

1. 在 Azure 入口網站中，選取 **自動化帳戶**。
1. 在 [自動化帳戶] 頁面上，選取您的自動化帳戶。
1. 在 [帳戶設定] 區段中，選取 [**屬性**]。
1. 請注意 [屬性] 頁面上的 [**名稱**]、[訂用帳戶**識別碼**] 和 [**資源群組**] 的值。 這些值分別對應至 `AutomationAccountName`、`SubscriptionId`和 `ResourceGroupName` PowerShell 腳本參數的值。

   ![自動化帳戶屬性頁面](media/manage-runas-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>用來建立執行身分帳戶的 PowerShell 腳本

本節提供 PowerShell 腳本來建立執行身分帳戶。 此腳本包含數個設定的支援。

* 使用自我簽署憑證建立執行身分帳戶。
* 使用自我簽署憑證建立執行身分帳戶和傳統執行身分帳戶。
* 使用企業憑證授權單位 (CA) 所核發的憑證，來建立執行身分帳戶和傳統執行身分帳戶。
* 在 Azure Government 雲端中，使用自我簽署憑證建立執行身分帳戶和傳統執行身分帳戶。

此指令碼會使用多個 Azure Resource Manager Cmdlet 來建立資源。 如需 Cmdlet 和其所需的許可權，請參閱[設定執行身分帳戶的許可權](#permissions-to-configure-run-as-accounts)。

使用檔案名**New-RunAsAccount**將腳本儲存在您的電腦上。

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
    $Subscription = Get-AzSubscription -SubscriptionId $SubscriptionId

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
        $SubscriptionName = $subscription.Subscription.Name
        $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName   $ClassicRunAsAccountConnectionFieldValues

        Write-Host -ForegroundColor red       $UploadMessage
    }
```

>[!NOTE]
>`Add-AzAccount` 和 `Add-AzureRMAccount` 是[disconnect-azaccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0)的別名。 您可以使用這些 Cmdlet，也可以將自動化帳戶中的[模組更新](automation-update-azure-modules.md)為最新版本。 即使您剛建立新的自動化帳戶，也可能需要更新您的模組。

### <a name="execute-the-powershell-script"></a>執行 PowerShell 腳本

1. 在電腦上以提高的使用者權限從 [開始] 畫面啟動 **Windows PowerShell**。
1. 從提高許可權的命令列 shell，移至包含您腳本的資料夾。
1. 針對您需要的設定，使用參數值來執行腳本。
1. 如果要建立傳統執行身分帳戶，在執行腳本之後，請將公開憑證（ **.cer**副檔名）上傳至已建立自動化帳戶之訂用帳戶的管理存放區。

執行腳本之後，系統會提示您向 Azure 進行驗證。 使用屬於訂用帳戶管理員角色成員和訂用帳戶共同管理員的帳戶登入。

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

如果您已建立具有企業公開憑證（ **.cer**檔案）的傳統執行身分帳戶，請使用此憑證。 請參閱將[管理 API 憑證上傳至 Azure 入口網站](../azure-api-management-certs.md)。

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate-in-the-azure-government-cloud"></a>在 Azure Government 雲端中使用自我簽署憑證，建立執行身分帳戶和傳統執行身分帳戶

```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
```

如果您已建立具有自我簽署公開憑證（ **.cer**檔案）的傳統執行身分帳戶，腳本會建立並將它儲存到您電腦上的暫存檔案資料夾中。 它可以在您用來執行 PowerShell 會話的使用者設定檔 `%USERPROFILE%\AppData\Local\Temp`中找到。

## <a name="deleting-a-run-as-or-classic-run-as-account"></a>刪除執行身分或傳統執行身分帳戶

本節說明如何刪除執行身分或傳統執行身分帳戶。 當您執行此動作時，系統不會保留自動化帳戶。 刪除帳戶之後，您可以在 Azure 入口網站中重新建立它。

1. 在 Azure 入口網站中，開啟自動化帳戶。

2. 在左窗格中，選取 [帳戶設定] 區段中的 [**執行身分帳戶**]。

3. 在 [執行身分帳戶] 屬性頁面上，選取您想要刪除的執行身分帳戶或傳統執行身分帳戶。 

4. 在所選帳戶的 [屬性] 窗格上，按一下 [**刪除**]。

   ![刪除執行身分帳戶](media/manage-runas-account/automation-account-delete-runas.png)

5. 刪除帳戶時，您可以在功能表的 [通知] 底下追蹤進度。

6. 刪除帳戶之後，您可以選取 [建立] 選項 [ **Azure 執行身分帳戶**]，在 [執行身分帳戶] 屬性頁面上重新加以建立。

   ![重新建立自動化執行身分帳戶](media/manage-runas-account/automation-account-create-runas.png)

## <a name="cert-renewal"></a>更新自我簽署憑證

您為執行身分帳戶建立的自我簽署憑證，會從建立日期起算一年後到期。 在執行身分帳戶到期之前的某個時間點，您必須更新憑證。 您可以在到期前隨時更新。 

當您更新自我簽署憑證時，會保留目前的有效憑證，以確保任何已排入佇列或正在執行的 runbook，以及使用「執行身分」帳戶進行驗證的 runbook 不會受到負面影響。 憑證在到期日之前會保持有效。

>[!NOTE]
>如果您認為執行身分帳戶已遭入侵，您可以刪除並重新建立自我簽署憑證。

>[!NOTE]
>如果您已將執行身分帳戶設定為使用企業憑證授權單位單位所發行的憑證，而且您使用 [更新自我簽署憑證] 選項，則會以自我簽署憑證取代企業憑證。

使用下列步驟來更新自我簽署憑證。

1. 在 Azure 入口網站中，開啟自動化帳戶。

1. 在 [帳戶設定] 區段中選取 [**執行身分帳戶**]。

    ![自動化帳戶的屬性窗格](media/manage-runas-account/automation-account-properties-pane.png)

1. 在 [執行身分帳戶] 屬性頁面上，選取要更新憑證的執行身分帳戶或傳統執行身分帳戶。

1. 在所選帳戶的 [屬性] 窗格上，按一下 [**更新憑證**]。

    ![更新執行身分帳戶的憑證](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. 更新憑證時，您可以在功能表的 [通知] 底下追蹤進度。

## <a name="auto-cert-renewal"></a>使用自動化 runbook 設定自動憑證更新

若要自動更新憑證，您可以使用自動化 runbook。 [GitHub](https://github.com/ikanni/PowerShellScripts/blob/master/AzureAutomation/RunAsAccount/GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1)上的此腳本可在您的自動化帳戶中啟用這項功能。

>[!NOTE]
>您必須是 Azure AD 中的全域管理員或公司系統管理員，才能執行腳本。

此腳本會建立每週排程，以更新執行身分帳戶憑證。 它會將**AutomationRunAsCredential** runbook 新增至您的自動化帳戶。 您可以在 GitHub 上的腳本[Update-AutomationRunAsCredential](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AutomationRunAsCredential.ps1)中查看 runbook 程式碼。 您可以視需要使用檔案中的 PowerShell 程式碼來手動更新憑證。

請使用下列步驟立即測試更新程式。

1. 編輯**AutomationRunAsCredential** runbook，並在結束 **（1）** 命令前方的第122行上放置批註字元（#）。

   ```powershell
   #Exit(1)
   ```

2. 發佈 runbook。
3. 啟動 runbook。
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

5. 測試完成之後，請編輯 runbook，並移除您在步驟1中新增的批註字元。
6. 發佈 runbook。

## <a name="limiting-run-as-account-permissions"></a>限制執行身分帳戶許可權

若要針對 Azure 中的資源控制自動化的目標，您可以執行[Update-AutomationRunAsAccountRoleAssignments](https://aka.ms/AA5hug8)腳本。 此腳本會變更您現有的執行身分帳戶服務主體，以建立及使用自訂角色定義。 除了[Key Vault](https://docs.microsoft.com/azure/key-vault/)以外，角色具有所有資源的許可權。

>[!IMPORTANT]
>當您執行**Update-AutomationRunAsAccountRoleAssignments**腳本之後，透過使用執行身分帳戶來存取 Key Vault 的 runbook 將無法再運作。 執行腳本之前，您應該先參閱帳戶中的 runbook，以取得 Azure Key Vault 的呼叫。 若要啟用從 Azure 自動化 runbook 存取 Key Vault，您必須[將執行身分帳戶新增至 Key Vault 的許可權](#add-permissions-to-key-vault)。

如果您需要限制執行身分服務主體可執行檔動作，您可以將其他資源類型新增至自訂角色定義的 `NotActions` 元素。 下列範例會限制 `Microsoft.Compute/*`的存取。 如果您將此資源類型新增至角色定義的 `NotActions`，此角色將無法存取任何計算資源。 若要深入瞭解角色定義，請參閱[瞭解 Azure 資源的角色定義](../role-based-access-control/role-definitions.md)。

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

您可以判斷您的執行身分帳戶所使用的服務主體是在參與者角色定義或自訂中。 

1. 移至您的自動化帳戶，然後選取 [帳戶設定] 區段中的 [**執行身分帳戶**]。
2. 選取 [ **Azure 執行身分帳戶**]。 
3. 選取 [**角色**] 以找出正在使用的角色定義。

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

您也可以針對多個訂用帳戶或自動化帳戶，判斷執行身分帳戶所使用的角色定義。 若要這麼做，請使用 PowerShell 資源庫中的[Check-AutomationRunAsAccountRoleAssignments](https://aka.ms/AA5hug5)腳本。

### <a name="add-permissions-to-key-vault"></a>將許可權新增至 Key Vault

您可以允許 Azure 自動化驗證 Key Vault 和您的執行身分帳戶服務主體是否使用自訂角色定義。 您必須：

* 授與 Key Vault 的許可權。
* 設定存取原則。

您可以使用 PowerShell 資源庫中的[Extend-AutomationRunAsAccountRoleAssignmentToKeyVault](https://aka.ms/AA5hugb)腳本，將您的執行身分帳戶許可權授與 Key Vault。 如需有關在 Key Vault 上設定許可權的詳細資訊，請參閱[授與存取金鑰保存庫的應用程式](../key-vault/key-vault-group-permissions-for-apps.md)。

## <a name="resolving-misconfiguration-issues-for-run-as-accounts"></a>解決執行身分帳戶的設定不正確問題

執行身分或傳統執行身分帳戶所需的某些設定專案，在初始安裝期間可能已刪除或不正確地建立。 可能的錯誤設定實例包括：

* 憑證資產
* 連線資產
* 從參與者角色中移除的執行身分帳戶
* Azure AD 中的服務主體或應用程式

針對這類設定錯誤的實例，自動化帳戶會偵測到變更，並在帳戶的 [執行身分帳戶] 屬性窗格上顯示 `Incomplete` 的狀態。

![不完整的執行身分帳戶設定狀態](media/manage-runas-account/automation-account-runas-incomplete-config.png)

當您選取執行身分帳戶時，[帳戶屬性] 窗格會顯示下列錯誤訊息：

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

您可以藉由刪除並重新建立這些執行身分帳戶，來快速解決帳戶的問題。

## <a name="next-steps"></a>後續步驟

* 如需服務主體的詳細資訊，請參閱[應用程式物件和服務主體物件](../active-directory/develop/app-objects-and-service-principals.md)。
* 如需有關憑證和 Azure 服務的詳細資訊，請參閱 [Azure 雲端服務的憑證概觀](../cloud-services/cloud-services-certs-create.md)。

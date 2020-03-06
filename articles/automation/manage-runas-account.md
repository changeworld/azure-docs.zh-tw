---
title: 管理 Azure 自動化執行身分帳戶
description: 本文說明如何使用 PowerShell 或從入口網站管理您的執行身分帳戶。
services: automation
ms.subservice: shared-capabilities
ms.date: 05/24/2019
ms.topic: conceptual
ms.openlocfilehash: a1761eebe51c5ccb4d30b93ad4122dfc185d216e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373222"
---
# <a name="manage-azure-automation-run-as-accounts"></a>管理 Azure 自動化執行身分帳戶

Azure 自動化中的執行身分帳戶可用來提供驗證，以使用 Azure Cmdlet 來管理 Azure 中的資源。

當您建立執行身分帳戶時，它會在 Azure Active Directory 中建立新的服務主體使用者，並在訂用帳戶層級將參與者角色指派給這位使用者。 對於在 Azure 虛擬機器上使用混合式 Runbook 背景工作角色的 Runbook，您可以使用 [Azure 資源的受控識別](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)來向 Azure 資源進行驗證，而非使用執行身分帳戶。

執行身分帳戶分為兩種類型：

* **Azure 執行身分帳戶**-此帳戶可用來管理[Resource Manager 部署模型](../azure-resource-manager/management/deployment-models.md)資源。
  * 建立可使用自我簽署憑證或企業憑證的公開金鑰進行匯出的 Azure AD 應用程式、建立此應用程式在 Azure AD 中的服務主體帳戶，並在目前的訂用帳戶中為此帳戶指派參與者角色。 您可以將此設定變更為擁有者或任何其他角色。 如需詳細資訊，請參閱 [Azure 自動化中的角色型存取控制](automation-role-based-access-control.md)。
  * 在指定的自動化帳戶中，建立名為 AzureRunAsCertificate 的自動化憑證資產。 憑證資產會保存 Azure AD 應用程式所使用的憑證私密金鑰。
  * 在指定的自動化帳戶中，建立名為 AzureRunAsConnection 的自動化連線資產。 連線資產會保存 applicationId、tenantId、subscriptionId 和憑證指紋。

* **Azure 傳統執行身分帳戶**-此帳戶可用來管理[傳統部署模型](../azure-resource-manager/management/deployment-models.md)資源。
  * 在訂用帳戶中建立管理憑證
  * 在指定的自動化帳戶中，建立名為 AzureClassicRunAsCertificate 的自動化憑證資產。 憑證資產會保存管理憑證所使用的憑證私密金鑰。
  * 在指定的自動化帳戶中，建立名為 AzureClassicRunAsConnection 的自動化連線資產。 連線資產會保存訂用帳戶名稱、subscriptionId 和憑證資產名稱。
  * 必須是訂用帳戶的共同管理員，才能建立或續訂

  > [!NOTE]
  > 「Azure 雲端解決方案提供者」(Azure CSP) 訂用帳戶僅支援 Azure Resource Manager 模型，因此本方案未提供非 Azure Resource Manager 服務。 使用 CSP 訂用帳戶時，不會建立「Azure 傳統執行身分帳戶」。 但仍然會建立「Azure 執行身分帳戶」。 若要深入了解 CSP 訂用帳戶，請參閱 [CSP 訂用帳戶中可用的服務](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services)。

  > [!NOTE]
  > 執行身分帳戶的服務主體預設沒有讀取 Azure Active Directory 的許可權。 如果您想要新增讀取或管理 Azure Active directory 的許可權，您必須在 [ **API 許可權**] 下授與服務主體的該許可權。 若要深入瞭解，請參閱[新增存取 Web api 的許可權](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)。

## <a name="permissions"></a>設定執行身分帳戶的權限

若要建立或更新執行身分帳戶，您必須擁有特定的權限和使用權限。 Azure Active Directory 中的應用程式系統管理員，以及訂用帳戶中的擁有者可以完成所有工作。 針對有劃分職責的情況，下表顯示工作、對等的 Cmdlet 及所需權限的清單：

|工作|Cmdlet  |最低權限  |設定權限的位置|
|---|---------|---------|---|
|建立 Azure AD 應用程式|[New-AzureRmADApplication](/powershell/module/azurerm.resources/new-azurermadapplication)     | 應用程式開發人員角色<sup>1</sup>        |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>[首頁] > [Azure Active Directory] > [應用程式註冊] |
|將認證新增至應用程式。|[New-AzureRmADAppCredential](/powershell/module/AzureRM.Resources/New-AzureRmADAppCredential)     | 應用程式系統管理員或全域管理員<sup>1</sup>         |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>[首頁] > [Azure Active Directory] > [應用程式註冊]|
|建立並取得 Azure AD 服務主體|[New-AzureRMADServicePrincipal](/powershell/module/AzureRM.Resources/New-AzureRmADServicePrincipal)</br>[Get-AzureRmADServicePrincipal](/powershell/module/AzureRM.Resources/Get-AzureRmADServicePrincipal)     | 應用程式系統管理員或全域管理員<sup>1</sup>        |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>[首頁] > [Azure Active Directory] > [應用程式註冊]|
|指派或取得指定主體的 RBAC 角色|[New-AzureRMRoleAssignment](/powershell/module/AzureRM.Resources/New-AzureRmRoleAssignment)</br>[Get-AzureRMRoleAssignment](/powershell/module/AzureRM.Resources/Get-AzureRmRoleAssignment)      | 您必須具有下列權限：</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br>或為：</br></br>使用者存取系統管理員或擁有者        | [訂用帳戶](../role-based-access-control/role-assignments-portal.md)</br>[首頁] > [訂用帳戶] > [\<訂用帳戶名稱\> - 存取控制 (IAM)]|
|建立或移除自動化憑證|[New-AzureRmAutomationCertificate](/powershell/module/AzureRM.Automation/New-AzureRmAutomationCertificate)</br>[Remove-AzureRmAutomationCertificate](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationCertificate)     | 資源群組的參與者         |自動化帳戶資源群組|
|建立或移除自動化連線|[New-AzureRmAutomationConnection](/powershell/module/AzureRM.Automation/New-AzureRmAutomationConnection)</br>[Remove-AzureRmAutomationConnection](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationConnection)|資源群組的參與者 |自動化帳戶資源群組|

<sup>1</sup> 如果 Azure AD 租用戶在 [使用者設定] 頁面中的 [使用者可以註冊應用程式] 選項設定為 [是]，Azure AD 租用戶中的非管理使用者就可以[註冊 AD 應用程式](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)。 如果 [應用程式註冊] 設定設為 [**否**]，則執行此動作的使用者必須是上表中所定義的。

如果您在新增至訂用**帳戶的全域管理員**角色之前，不是訂閱 Active Directory 實例的成員，則會將您新增為來賓。 在此情況下，您會在 [加入自動化帳戶] 頁面上收到 `You do not have permissions to create…` 警告。 先新增至**全域管理員**角色的使用者，可以從訂用帳戶的 Active Directory 實例中移除並重新新增，使其成為 Active Directory 的完整使用者。 若要確認這種情況，請從 Azure 入口網站的 [Azure Active Directory] 窗格，選取 [使用者和群組]、選取 [所有使用者]，然後在選取特定使用者之後，選取 [設定檔]。 使用者設定檔之下 [使用者類型] 屬性的值不得等於 [來賓]。

## <a name="permissions-classic"></a>設定傳統執行身分帳戶的許可權

若要設定或更新傳統執行身分帳戶，您必須具有訂用帳戶層級的**共同管理員**角色。 若要深入瞭解傳統許可權，請參閱[Azure 傳統訂](../role-based-access-control/classic-administrators.md#add-a-co-administrator)用帳戶管理員。

## <a name="create-a-run-as-account-in-the-portal"></a>在 Azure 入口網站中建立執行身分帳戶

在本節中，執行下列步驟以在 Azure 入口網站更新 Azure 自動化帳戶。 您可以個別建立「執行身分帳戶」和「傳統執行身分帳戶」。 如果您不需要管理傳統資源，則可以只建立 Azure 執行身分帳戶。

1. 以訂用帳戶管理員角色成員和訂用帳戶共同管理員的帳戶登入 Azure 入口網站。
2. 在 Azure 入口網站中，搜尋並選取 [**自動化帳戶**]。
3. 在 [自動化帳戶] 頁面上，從自動化帳戶清單中選取您的自動化帳戶。
4. 在左側窗格中，選取 [帳戶設定] 區段下的 [執行身分帳戶]。
5. 根據您所需的帳戶，選取 [Azure 執行身分帳戶] 或 [Azure 傳統執行身分帳戶]。 選取 [新增 Azure 執行身分] 之後或 [新增 Azure 傳統執行身分帳戶] 窗格出現之後，並檢閱概觀資訊之後，請按一下 [建立] 繼續建立執行身分帳戶。
6. 在 Azure 建立執行身分帳戶時，您可以在功能表的 [通知] 底下追蹤進度。 橫幅也會顯示說明帳戶正在建立。 此程序需要數分鐘的時間完成。

## <a name="create-run-as-account-using-powershell"></a>使用 PowerShell 建立執行身分帳戶

## <a name="prerequisites"></a>必要條件

下列清單提供在 PowerShell 中建立執行身分帳戶的需求：

* 具有 Azure Resource Manager 模組 3.4.1 和更新版本的 Windows 10 或 Windows Server 2016。 PowerShell 指令碼不支援舊版 Windows。
* Azure PowerShell 1.0 和更新版本。 如需有關 PowerShell 1.0 版本的資訊，請參閱[如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs)。
* 自動化帳戶，系統會將其參照為 –AutomationAccountName 和 -ApplicationDisplayName 參數的值。
* 權限相當於[設定執行身分帳戶的必要權限](#permissions)中所列的權限

若要取得指令碼所需參數 *SubscriptionID*、*ResourceGroup* 及 *AutomationAccountName* 的值，請完成下列步驟︰

1. 在 Azure 入口網站中，搜尋並選取 [**自動化帳戶**]。
1. 在自動化帳戶頁面上選取您的自動化帳戶，然後在 [帳戶設定] 下選取 [屬性]。
1. 記下 [屬性] 頁面上的**訂用帳戶識別碼**、**名稱**和**資源群組**值。

   ![自動化帳戶的 [屬性] 頁面](media/manage-runas-account/automation-account-properties.png)

這個 PowerShell 指令碼包含下列組態的支援︰

* 使用自我簽署憑證建立執行身分帳戶。
* 使用自我簽署憑證建立執行身分帳戶和傳統執行身分帳戶。
* 使用企業憑證授權單位 (CA) 所核發的憑證，來建立執行身分帳戶和傳統執行身分帳戶。
* 在 Azure Government 雲端中，使用自我簽署憑證建立執行身分帳戶和傳統執行身分帳戶。

>[!NOTE]
> 如果您選取任一選項來建立傳統執行方式帳戶，在指令碼執行之後，請將公開憑證 (.cer 副檔名) 上傳至自動化帳戶建立所在之訂用帳戶的管理存放區中。

1. 將下列指令碼儲存到電腦。 在此範例中，請以檔案名稱 *New-RunAsAccount.ps1*進行儲存。

   此指令碼會使用多個 Azure Resource Manager Cmdlet 來建立資源。 上述[許可權](#permissions)資料表會顯示 Cmdlet 及其所需的許可權。

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
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $keyId)
        # Requires Application administrator or GLOBAL ADMIN
        $ApplicationCredential = New-AzureRmADAppCredential -ApplicationId $Application.ApplicationId -CertValue $keyValue -StartDate $PfxCert.NotBefore -EndDate $PfxCert.NotAfter
        # Requires Application administrator or GLOBAL ADMIN
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id

        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
        # Requires User Access Administrator or Owner.
        $NewRole = New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6) {
            Sleep -s 10
            New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
            $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
            $Retries++;
        }
        return $Application.ApplicationId.ToString();
    }

    function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName, [string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
        $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force
        Remove-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
        New-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
    }

    function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
        Remove-AzureRmAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
    }

    Import-Module AzureRM.Profile
    Import-Module AzureRM.Resources

    $AzureRMProfileVersion = (Get-Module AzureRM.Profile).Version
    if (!(($AzureRMProfileVersion.Major -ge 3 -and $AzureRMProfileVersion.Minor -ge 4) -or ($AzureRMProfileVersion.Major -gt 3))) {
        Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
        return
    }

    # To use the new Az modules to create your Run As accounts please uncomment the following lines and ensure you comment out the previous 8 lines that import the AzureRM modules to avoid any issues. To learn about about using Az modules in your Automation Account see https://docs.microsoft.com/azure/automation/az-modules

    # Import-Module Az.Automation
    # Enable-AzureRmAlias


    Connect-AzureRmAccount -Environment $EnvironmentName
    $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId

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
    $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
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

    > [!IMPORTANT]
    > **Add-AzureRmAccount** 現在是 **Connect-AzureRMAccount** 的別名。 搜尋您的程式庫項目時，如果沒看到 **Connect-AzureRMAccount**，便可以使用 **Add-AzureRmAccount**，或是在自動化帳戶中[更新模組](automation-update-azure-modules.md)。

1. 在電腦上以提高的使用者權限從 [開始] 畫面啟動 **Windows PowerShell**。
1. 從提高權限的命令列殼層，移至包含您在步驟 1 中建立的指令碼的資料夾。
1. 使用所需設定的參數值來執行指令碼。

    **使用自我簽署憑證建立執行身分帳戶**

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
    ```

    **使用自我簽署憑證建立執行身分帳戶和傳統執行身分帳戶**

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
    ```

    **使用企業憑證建立執行身分帳戶和傳統執行身分帳戶**

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
    ```

    **在 Azure Government 雲端中，使用自我簽署憑證建立執行身分帳戶和傳統執行身分帳戶**

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
    ```

    > [!NOTE]
    > 指令碼執行之後，您會收到向 Azure 進行驗證的提示。 請以訂用帳戶管理員角色成員和訂用帳戶共同管理員的帳戶登入。

指令碼執行成功之後，請注意下列事項︰

* 如果您使用自我簽署的公開憑證 (.cer 檔案) 建立了傳統執行身分帳戶，指令碼會建立該帳戶並將其儲存在電腦上的暫存檔案資料夾中，用來執行 PowerShell 工作階段的使用者設定檔 %USERPROFILE%\AppData\Local\Temp 底下。

* 如果您使用企業公開憑證 (.cer 檔案) 建立了傳統執行身分帳戶，請使用此憑證。 請依照[將管理 API 憑證上傳至 Azure 入口網站](../azure-api-management-certs.md)的指示進行操作。

## <a name="delete-a-run-as-or-classic-run-as-account"></a>刪除執行身分或傳統執行身分帳戶

本節說明如何刪除並重新建立執行身分或傳統執行身分帳戶。 當您執行此動作時，系統不會保留自動化帳戶。 刪除執行身分或傳統執行身分帳戶之後，您可以在 Azure 入口網站中重新建立它。

1. 在 Azure 入口網站中，開啟自動化帳戶。

2. 在 [自動化帳戶] 頁面上，選取 [執行身分帳戶]。

3. 在 [執行身分帳戶] 屬性頁面中，選取您想要刪除的執行身分帳戶或傳統執行身分帳戶。 然後，在所選帳戶的 [屬性] 窗格上，按一下 [刪除]。

   ![刪除執行身分帳戶](media/manage-runas-account/automation-account-delete-runas.png)

1. 刪除帳戶時，您可以在功能表的 [通知] 底下追蹤進度。

1. 帳戶刪除之後，您可以在 [執行身分帳戶] 屬性頁面中，選取建立選項 [Azure 執行身分帳戶]來重新建立它。

   ![重新建立自動化執行身分帳戶](media/manage-runas-account/automation-account-create-runas.png)

## <a name="cert-renewal"></a>自我簽署憑證更新

有時您必須在執行身分帳戶到期前更新憑證。 如果您認為執行身分帳戶遭到盜用，您可加以刪除並重新建立。 本節會討論如何執行這些作業。

您為執行身分帳戶建立的自我簽署憑證，會在建立日起算一年後到期。 您可以在該憑證到期前隨時更新憑證。 當您更新憑證時，系統會保留目前的有效憑證，以確保已排入佇列或正在執行以及使用該「執行身分」帳戶進行驗證的所有 Runbook，都不會受到負面影響。 憑證在到期日之前會保持有效。

> [!NOTE]
> 如果您已設定您的自動化執行身分帳戶以使用您的企業憑證授權單位所核發的憑證，而且您使用此選項，該企業憑證會由自我簽署憑證所取代。

若要更新憑證，請執行下列步驟︰

1. 在 Azure 入口網站中，開啟自動化帳戶。

1. 選取 [帳戶設定] 下方的 [執行身分帳戶]。

    ![自動化帳戶的屬性窗格](media/manage-runas-account/automation-account-properties-pane.png)

1. 在 [執行身分帳戶] 屬性頁面中，選取您想要更新憑證的執行身分帳戶或傳統執行身分帳戶。

1. 在所選帳戶的 [屬性] 窗格上，按一下 [更新憑證]。

    ![更新執行身分帳戶的憑證](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. 更新憑證時，您可以在功能表的 [通知] 底下追蹤進度。

## <a name="auto-cert-renewal"></a>使用自動化 runbook 設定自動憑證更新

若要自動更新憑證，您可以使用自動化 runbook。 [GitHub](https://github.com/ikanni/PowerShellScripts/blob/master/AzureAutomation/RunAsAccount/GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1)上的下列腳本會在您的自動化帳戶中啟用這項功能。

- `GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1` 腳本會建立每週排程，以更新執行身分帳戶憑證。
- 此腳本會將**AutomationRunAsCredential** runbook 新增至您的自動化帳戶。
  - 您也可以在 GitHub 上的腳本中查看 runbook 程式碼：[Update-AutomationRunAsCredential. ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AutomationRunAsCredential.ps1)。
  - 您也可以使用檔案中的 PowerShell 程式碼，視需要手動更新憑證。

若要立即測試更新程式，請使用下列步驟：

1. 編輯**AutomationRunAsCredential** runbook，並將批註字元（`#`）放在122行前面的 `Exit(1)` 命令之前，如下所示。

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

   ```Output
   Thursday, November 7, 2019 7:00:00 PM
   ```

5. 測試完成之後，請編輯 runbook，並移除您在**步驟 1**中新增的批註字元。
6. **發佈**runbook。

> [!NOTE]
> 您必須是 Azure Active Directory 中的**全域管理員**或**公司系統管理員**，才能執行腳本。

## <a name="limiting-run-as-account-permissions"></a>限制執行身分帳戶許可權

若要針對 Azure 中的資源控制自動化的目標，您可以在 PowerShell 資源庫中執行[Update-AutomationRunAsAccountRoleAssignments](https://aka.ms/AA5hug8)腳本，以變更現有的執行身分帳戶服務主體，以建立及使用自訂角色定義。 除了[Key Vault](https://docs.microsoft.com/azure/key-vault/)以外，此角色將具有所有資源的許可權。

> [!IMPORTANT]
> 執行 `Update-AutomationRunAsAccountRoleAssignments.ps1` 腳本之後，透過使用 RunAs 帳戶來存取 KeyVault 的 runbook 將無法再運作。 您應該參閱帳戶中的 runbook，以進行 Azure KeyVault 的呼叫。
>
> 若要從 Azure 自動化 runbook 啟用 KeyVault 的存取權，您必須[將 RunAs 帳戶新增至 KeyVault 的許可權](#add-permissions-to-key-vault)。

如果您需要限制 RunAs 服務主體可以進一步執行的動作，您可以將其他資源類型新增至自訂角色定義的 `NotActions`。 下列範例會限制 `Microsoft.Compute`的存取。 如果您將此新增至角色定義的**NotActions** ，此角色將無法存取任何計算資源。 若要深入瞭解角色定義，請參閱[瞭解 Azure 資源的角色定義](../role-based-access-control/role-definitions.md)。

```powershell
$roleDefinition = Get-AzureRmRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzureRMRoleDefinition
```

若要判斷您的執行身分帳戶所使用的服務主體是否在「**參與者**」或「自訂角色定義」中，請移至您的自動化帳戶，然後在 [**帳戶設定**] 底下，選取 [**執行身分帳戶**] > **Azure 執行身分帳戶**。 在 [**角色**] 底下，您會找到正在使用的角色定義。

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

若要針對多個訂用帳戶或自動化帳戶判斷自動化執行身分帳戶所使用的角色定義，您可以使用 PowerShell 資源庫中的[Check-AutomationRunAsAccountRoleAssignments](https://aka.ms/AA5hug5)腳本。

### <a name="add-permissions-to-key-vault"></a>將許可權新增至 Key Vault

如果您想要允許 Azure 自動化管理 Key Vault，而您的執行身分帳戶服務主體正在使用自訂角色定義，則您必須採取額外的步驟來允許此行為：

* 授與許可權給 Key Vault
* 設定存取原則

您可以使用 PowerShell 資源庫中的[Extend-AutomationRunAsAccountRoleAssignmentToKeyVault](https://aka.ms/AA5hugb)腳本，將您的執行身分帳戶許可權授與 KeyVault，或造訪[授與應用程式存取金鑰保存庫](../key-vault/key-vault-group-permissions-for-apps.md)的詳細資料，以取得 KeyVault 上設定許可權的詳細資訊。

## <a name="misconfiguration"></a>設定錯誤

在初始設定期間，您可能會以不正確的方式刪除或建立要讓執行身分或傳統執行身分帳戶正常運作所需的某些設定項目。 這些項目包括︰

* 憑證資產
* 連線資產
* 已從參與者角色移除了執行身分帳戶
* Azure AD 中的服務主體或應用程式

在上述設定錯誤和其他這類情況中，自動化帳戶會偵測到變更，並在帳戶的 [執行身分帳戶] 屬性頁面上顯示 [不完整] 狀態。

![不完整的執行身分帳戶設定狀態](media/manage-runas-account/automation-account-runas-incomplete-config.png)

當您選取執行身分帳戶時，帳戶的 [屬性] 窗格會顯示下列錯誤訊息：

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

您可以藉由刪除並重新建立這些執行身分帳戶，來快速解決帳戶的問題。

## <a name="next-steps"></a>後續步驟

* 如需服務主體的詳細資訊，請參閱[應用程式物件和服務主體物件](../active-directory/develop/app-objects-and-service-principals.md)。
* 如需有關憑證和 Azure 服務的詳細資訊，請參閱 [Azure 雲端服務的憑證概觀](../cloud-services/cloud-services-certs-create.md)。

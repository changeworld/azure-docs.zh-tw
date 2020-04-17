---
title: 管理 Azure 自動化執行身分帳戶
description: 本文說明如何使用 PowerShell 或從入口網站管理您的執行身分帳戶。
services: automation
ms.subservice: shared-capabilities
ms.date: 05/24/2019
ms.topic: conceptual
ms.openlocfilehash: 4a043bcc2f81214b68b166d443baead6cd230184
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457496"
---
# <a name="manage-azure-automation-run-as-accounts"></a>管理 Azure 自動化執行身分帳戶

在 Azure 自動化中執行為帳戶提供使用 Azure cmdlet 管理 Azure 中的資源的身份驗證。 創建「運行為「帳戶時,它會在 Azure 活動目錄 (AD) 中創建新的服務主體使用者,並在訂閱級別將參與者角色分配給此使用者。 對於在 Azure 虛擬機器上使用混合式 Runbook 背景工作角色的 Runbook，您可以使用 [Azure 資源的受控識別](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)來向 Azure 資源進行驗證，而非使用執行身分帳戶。

默認情況下,「作為帳戶運行」的服務主體沒有讀取 Azure AD 的許可權。 如果要添加讀取或管理 Azure AD 的許可權,則需要在**API 許可權**下授予服務主體的許可權。 要瞭解更多資訊,請參閱[新增存取 Web API 的權限](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有關混合 Runbook 輔助角色上的 Az 模組安裝說明,請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 對於自動化帳戶,可以使用[「如何更新 Azure 自動化 中的 Azure PowerShell」模組](automation-update-azure-modules.md)將模組更新到最新版本。

## <a name="types-of-run-as-accounts"></a>此帳號執行的類型

Azure 自動化使用兩種類型的「以帳戶形式執行」:

* Azure 作為帳戶執行
* Azure 經典作為帳戶執行

>[!NOTE]
>Azure 雲端解決方案提供者 (CSP) 訂閱僅支援 Azure 資源管理器模型。 非 Azure 資源管理員服務在程式中不可用。 使用 CSP 訂閱時,不會創建 Azure 經典運行為帳戶,而是創建 Azure 運行作為帳戶。 若要深入了解 CSP 訂用帳戶，請參閱 [CSP 訂用帳戶中可用的服務](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services)。

### <a name="run-as-account"></a>執行身分帳戶

"執行為帳戶"管理[資源管理員部署模型](../azure-resource-manager/management/deployment-models.md)資源。 它執行以下任務。

* 建立可使用自我簽署憑證或企業憑證的公開金鑰進行匯出的 Azure AD 應用程式、建立此應用程式在 Azure AD 中的服務主體帳戶，並在目前的訂用帳戶中為此帳戶指派參與者角色。 您可以將證書設置更改為擁有者或任何其他角色。 有關詳細資訊,請參閱[Azure 自動化 中的基於角色的存取控制](automation-role-based-access-control.md)。
  
* 創建指定自動化帳戶中指定的`AzureRunAsCertificate`自動化證書資產。 憑證資產包含 Azure AD 應用程式使用的證書私鑰。
  
* 創建指定自動化帳戶中指定的`AzureRunAsConnection`自動化連接資產。 連接資產包含應用程式 ID、租戶 ID、訂閱 ID 和證書指紋。

### <a name="azure-classic-run-as-account"></a>Azure 傳統執行身分帳戶

Azure 經典運行作為帳戶管理[經典部署模型](../azure-resource-manager/management/deployment-models.md)資源。 您必須是訂閱上的共同管理員才能創建或續訂此類帳戶。

Azure 經典運行為帳戶執行以下任務。

  * 在訂閱中創建管理證書。

  * 創建指定自動化帳戶中指定的`AzureClassicRunAsCertificate`自動化證書資產。 憑證資產會保存管理憑證所使用的憑證私密金鑰。

  * 創建指定自動化帳戶中指定的`AzureClassicRunAsConnection`自動化連接資產。 連接資產包含訂閱名稱、訂閱ID和證書資產名稱。

## <a name="run-as-account-permissions"></a><a name="permissions"></a>此帳號權限執行

本節定義常規"以帳戶身份運行"和"經典作為帳戶運行"的許可權。

### <a name="permissions-to-configure-run-as-accounts"></a>設定執行身分帳戶的權限

若要建立或更新執行身分帳戶，您必須擁有特定的權限和使用權限。 Azure 活動目錄中的應用程式管理員和訂閱中的所有者可以完成所有任務。 在具有職責分離的情況下,下表將顯示任務列表、等效 cmdlet 和所需的許可權:

|Task|Cmdlet  |最低權限  |設定權限的位置|
|---|---------|---------|---|
|建立 Azure AD 應用程式|[新阿達應用程式](https://docs.microsoft.com/powershell/module/az.resources/new-azadapplication?view=azps-3.5.0)     | 應用程式開發人員角色<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>主頁 > Azure AD >应用注册 |
|將認證新增至應用程式。|[新阿達帕西](https://docs.microsoft.com/powershell/module/az.resources/new-azadappcredential?view=azps-3.5.0)     | 應用程式管理員或全域管理員<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>主頁 > Azure AD >应用注册|
|建立與取得 Azure AD 服務主體|[New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-3.5.0)</br>[取得 Aad 服務原則](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal?view=azps-3.5.0)     | 應用程式管理員或全域管理員<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>主頁 > Azure AD >应用注册|
|指派或取得指定主體的 RBAC 角色|[New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment?view=azps-3.5.0)</br>[Get-AzRoleAssignment](https://docs.microsoft.com/powershell/module/Az.Resources/Get-AzRoleAssignment?view=azps-3.5.0)      | 使用者存取管理員或擁有者,或具有以下許可權:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [訂用帳戶](../role-based-access-control/role-assignments-portal.md)</br>[首頁] > [訂用帳戶] > [\<訂用帳戶名稱\> - 存取控制 (IAM)]|
|建立或移除自動化憑證|[新阿茲自動化證書](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.5.0)</br>[刪除-阿茲自動化證書](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationcertificate?view=azps-3.5.0)     | 資源群組的參與者         |自動化帳戶資源群組|
|建立或移除自動化連線|[新-阿茲自動化連線](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.5.0)</br>[刪除-阿茲自動化連線](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationconnection?view=azps-3.5.0)|資源群組的參與者 |自動化帳戶資源群組|

<sup>1</sup>如果 Azure AD 租戶的使用者可以在「使用者設定」 頁上**註冊應用程式**選項設定為 **「是**」,則 Azure AD 租戶中的 1 個非管理員使用者可以[註冊 AD 應用程式](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)。 如果應用程式註冊設置**為"否**",則執行此操作的用戶必須如此表中的定義。

如果在添加到訂閱的全域管理員角色之前,您不是訂閱的 Active Directory 實例的成員,則將添加為來賓。 在此情況下，您會在 [加入自動化帳戶] 頁面上收到 `You do not have permissions to create…` 警告。 

如果在分配全域管理員角色時是訂閱的活動目錄實例的成員,您還可以在「添加自動化帳戶」頁上收到`You do not have permissions to create…`警告。 在這種情況下,您可以請求從訂閱的 Active Directory 實例中刪除,然後請求重新添加,以便成為 Active Directory 中的完整使用者。

要驗證產生錯誤訊息的情況已得到糾正:

1. 從 Azure 門戶中的 Azure 活動目錄窗格中,選擇 **「使用者」和「群組」。** 
2. 選擇**所有使用者**。
3. 選擇您的姓名,然後選擇**設定檔**。 
4. 確保使用者設定檔下**的使用者類型**屬性的值未設定為**來賓**。

### <a name="permissions-to-configure-classic-run-as-accounts"></a><a name="permissions-classic"></a>設定傳統「作為帳戶」的權限

要配置或續訂經典運行作為帳戶,您必須在訂閱級別具有共同管理員角色。 要瞭解有關經典訂閱許可權的更多資訊,請參閱[Azure 經典訂閱管理員](../role-based-access-control/classic-administrators.md#add-a-co-administrator)。

## <a name="creating-a-run-as-account-in-azure-portal"></a>在 Azure 門戶中建立「運行為帳戶」帳戶

執行以下步驟以在 Azure 門戶中更新 Azure 自動化帳戶。 單獨創建"以"身份運行"和"經典運行為帳戶"。 如果您不需要管理傳統資源，則可以只建立 Azure 執行身分帳戶。

1. 您必須以訂用帳戶管理員角色成員和訂用帳戶共同管理員的帳戶登入 Azure 入口網站。
2. 搜尋並選擇**自動化帳戶**。
3. 在"自動化帳戶"頁上,從清單中選擇您的自動化帳戶。
4. 在左側窗格中,在「帳戶設置」部分中選擇「**以帳戶身份運行**」。。
5. 根據您所需的帳戶，選取 [Azure 執行身分帳戶]**** 或 [Azure 傳統執行身分帳戶]****。 
6. 根據感興趣的帳戶,使用 **「添加 Azure 運行為」** 或 **「新增 Azure 經典運行帳戶」** 窗格。 查看概述資訊后,按一下「**創建**」。。
6. 在 Azure 建立執行身分帳戶時，您可以在功能表的 [通知] **** 底下追蹤進度。 還會顯示一個橫幅,說明正在創建帳戶。 此過程可能需要幾分鐘才能完成。

## <a name="creating-a-run-as-account-using-powershell"></a>使用 PowerShell 建立「帳號」

以下清單提供了在 PowerShell 中創建執行為帳戶」。 這些要求適用於這兩種類型的「運行為帳戶」。

* 具有 Azure Resource Manager 模組 3.4.1 和更新版本的 Windows 10 或 Windows Server 2016。 PowerShell 文稿不支援早期版本的 Windows。
* Azure PowerShell 1.0 和更新版本。 如需有關 PowerShell 1.0 版本的資訊，請參閱[如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs)。
* 自動化帳戶,該帳戶被引用為`AutomationAccountName`和`ApplicationDisplayName`參數的值。
* 權限等效於[「必需權限」 中列的設定為執行帳戶的權限](#permissions)。

要取得`SubscriptionId`PowerShell`ResourceGroupName`文稿所需的參數的值, 完成後續步驟。

1. 在 Azure 門戶中,選擇 **「自動化帳戶**」。。
1. 在"自動化帳戶"頁上,選擇您的自動化帳戶。
1. 在「帳戶設置」部分中,選擇 **「屬性**」。。
1. 請注意「屬性」**名稱**,**訂閱 ID**和**資源群組**的值。 這些值分別對應於`AutomationAccountName`、`SubscriptionId``ResourceGroupName`PowerShell 文本參數的值。

   ![自動化帳戶屬性頁](media/manage-runas-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>建立「執行作為「帳戶的 PowerShell 文稿

本節提供一個 PowerShell 腳本來創建"運行即"帳戶。 該腳本包括對多種配置的支援。

* 使用自我簽署憑證建立執行身分帳戶。
* 使用自我簽署憑證建立執行身分帳戶和傳統執行身分帳戶。
* 使用企業憑證授權單位 (CA) 所核發的憑證，來建立執行身分帳戶和傳統執行身分帳戶。
* 在 Azure Government 雲端中，使用自我簽署憑證建立執行身分帳戶和傳統執行身分帳戶。

此指令碼會使用多個 Azure Resource Manager Cmdlet 來建立資源。 有關 cmdlet 及其所需的權限,請參考[設定「作為帳戶執行」的權限](#permissions-to-configure-run-as-accounts)。

使用檔案名**New-RunAsAccount.ps1**將文本保存在電腦上。

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
>`Add-AzAccount`是`Add-AzureRMAccount` [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0)的別名 。 您可以使用這些 cmdlet,也可以將自動化帳戶中的[模組更新](automation-update-azure-modules.md)到最新版本。 即使您剛剛創建了新的自動化帳戶,您也可能需要更新模組。

### <a name="execute-the-powershell-script"></a>執行 PowerShell 文稿

1. 在電腦上以提高的使用者權限從 [開始]**** 畫面啟動 **Windows PowerShell**。
1. 從提升的命令列 shell 轉到包含文稿的資料夾。
1. 使用所需的配置的參數值執行腳本。
1. 如果創建經典運行為帳戶,則在執行腳本後,請將公共證書 **(.cer**檔名擴展名)上傳到創建自動化帳戶的訂閱的管理存儲。

文稿執行後,系統將提示您使用 Azure 進行身份驗證。 使用訂閱管理員角色的成員和訂閱的共同管理員的帳戶登錄。

#### <a name="create-a-run-as-account-by-using-a-self-signed-certificate"></a>使用自簽署憑證建立「管理帳號

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

如果您已使用企業公共證書 **(.cer**檔案)創建了經典運行作為帳戶,請使用此證書。 請參考[API 憑證載入 Azure 門戶](../azure-api-management-certs.md)。

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate-in-the-azure-government-cloud"></a>在 Azure Government 雲端中，使用自我簽署憑證建立執行身分帳戶和傳統執行身分帳戶

```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
```

如果您已創建具有自簽名公共證書 **(.cer**檔案) 的經典運行帳戶,文稿將創建該帳戶並將其保存到電腦上的暫存檔案資料夾中。 它可以在使用者配置檔`%USERPROFILE%\AppData\Local\Temp`中找到 ,用於執行 PowerShell 作業階段。

## <a name="deleting-a-run-as-or-classic-run-as-account"></a>刪除"運行為"或"經典運行為帳戶"

本節介紹如何刪除"運行為"或"經典運行為帳戶"。 當您執行此動作時，系統不會保留自動化帳戶。 刪除帳戶後,可以在 Azure 門戶中重新創建它。

1. 在 Azure 入口網站中，開啟自動化帳戶。

2. 在左側窗格中,在「帳戶設置」部分中選擇「**以帳戶身份運行**」。。

3. 在 [執行身分帳戶] 屬性頁面中，選取您想要刪除的執行身分帳戶或傳統執行身分帳戶。 

4. 在所選帳戶的"屬性"窗格上,按一下"**刪除**"

   ![刪除執行身分帳戶](media/manage-runas-account/automation-account-delete-runas.png)

5. 刪除帳戶時，您可以在功能表的 [通知]**** 底下追蹤進度。

6. 帳戶刪除之後，您可以在 [執行身分帳戶] 屬性頁面中，選取建立選項 [Azure 執行身分帳戶]**** 來重新建立它。

   ![重新建立自動化執行身分帳戶](media/manage-runas-account/automation-account-create-runas.png)

## <a name="renewing-a-self-signed-certificate"></a><a name="cert-renewal"></a>續訂自簽署憑證

您為「執行」帳戶創建的自簽名證書自創建之日起一年後過期。 在"運行作為"帳戶過期之前的某個時間點,必須續訂證書。 您可以隨時續訂它。 

續訂自簽名證書時,將保留當前有效證書,以確保排隊或主動運行以及使用 Run As 帳戶進行身份驗證的任何 Runbook 不會受到負面影響。 憑證在到期日之前會保持有效。

>[!NOTE]
>如果您認為「執行」帳戶已洩露,則可以刪除並重新創建自簽名證書。

>[!NOTE]
>如果已將 Run As 帳戶配置為使用企業證書頒發機構頒發的證書,並且使用該選項續訂自簽名證書選項,則企業證書將被自簽名證書替換。

使用以下步驟續訂自簽名證書。

1. 在 Azure 入口網站中，開啟自動化帳戶。

1. 在「帳戶設定」部分中選擇 **「以帳戶身份運行**」 。

    ![自動化帳戶的屬性窗格](media/manage-runas-account/automation-account-properties-pane.png)

1. 在"以帳戶身份運行"屬性頁上,選擇"以"身份運行"帳戶或續訂證書的經典"作為"帳戶"。

1. 在所選帳戶的屬性窗格上,按一下 **「續訂證書**」。

    ![更新執行身分帳戶的憑證](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. 更新憑證時，您可以在功能表的 [通知]**** 底下追蹤進度。

## <a name="setting-up-automatic-certificate-renewal-with-an-automation-runbook"></a><a name="auto-cert-renewal"></a>使用自動化 Runbook 設定自動憑證續訂

要自動續訂證書,可以使用自動化 Runbook。 [GitHub](https://github.com/ikanni/PowerShellScripts/blob/master/AzureAutomation/RunAsAccount/GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1)上的此文本可在您的自動化帳戶中啟用此功能。

>[!NOTE]
>您必須是 Azure AD 中的全域管理員或公司管理員才能執行文稿。

此腳本創建每周計劃以續訂"運行為帳戶"證書。 它將**更新-自動化運行證書**執行簿添加到您的自動化帳戶。 您可以在 GitHub 上查看執行簿代碼,在腳本[更新-自動化RunAsCredential.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AutomationRunAsCredential.ps1)中查看。 您可以根據需要使用檔案中的 PowerShell 程式碼手動續訂證書。

使用以下步驟立即測試續訂過程。

1. 編輯**更新-自動化RunAsCredential**執行簿,並在 **「退出(1)」** 命令的前面第 122 行放置註釋字元 (#)。

   ```powershell
   #Exit(1)
   ```

2. 發佈 Runbook。
3. 啟動 Runbook。
4. 使用以下代碼驗證成功續訂:

   ```powershell
   (Get-AzAutomationCertificate -AutomationAccountName TestAA
                                -Name AzureRunAsCertificate
                                -ResourceGroupName TestAutomation).ExpiryTime.DateTime
   ```
    輸出：

   ```Output
   Thursday, November 7, 2019 7:00:00 PM
   ```

5. 測試後,編輯 Runbook,並刪除在步驟 1 中添加的註釋字元。
6. 發佈 Runbook。

## <a name="limiting-run-as-account-permissions"></a><a name="limiting-run-as-account-permissions"></a>限制執行身分帳戶的權限

要控制針對 Azure 中的資源的自動化目標,可以運行[更新-自動化RunAsAccountRole任務.ps1](https://aka.ms/AA5hug8)腳本。 此文本更改現有的「運行為帳戶服務主體」以創建和使用自定義角色定義。 該角色具有除[密鑰保管庫](https://docs.microsoft.com/azure/key-vault/)之外的所有資源的許可權。

>[!IMPORTANT]
>運行**更新-自動化運行運行會計角色分配.ps1**腳本後,使用"運行為帳戶"訪問金鑰保管庫的 Runbook 將不再工作。 在運行文稿之前,應查看帳戶中的 Runbook 以造訪 Azure 密鑰保管庫。 要開啟從 Azure 自動化執行簿存取金鑰保管庫,必須[將「執行為「帳戶新增到金鑰保管庫的權限](#add-permissions-to-key-vault)。

如果需要限制運行為服務主體可以執行的其他操作,可以將其他資源類型添加到自定義角色定義`NotActions`的元素。 下面的示例限制對`Microsoft.Compute/*`的訪問。 如果`NotActions`為此資源類型添加到角色定義中,該角色將無法訪問任何計算資源。 要瞭解有關角色定義的更多資訊,請參閱[瞭解 Azure 資源的角色定義](../role-based-access-control/role-definitions.md)。

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

您可以確定 Run As 帳戶使用的服務主體是「參與者角色」定義還是自定義角色定義。 

1. 移到您的自動化帳戶,並在帳戶設置部分中選擇以**帳戶身份執行**"。
2. 選擇**Azure 以帳戶身份執行**。 
3. 選擇**角色**以查找正在使用的角色定義。

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

您還可以確定「運行為多個訂閱或自動化帳戶運行為帳戶」使用的角色定義。 通過使用 PowerShell 庫中的[「檢查-自動化運行帳戶角色分配.ps1」](https://aka.ms/AA5hug5)腳本來執行此操作。

### <a name="add-permissions-to-key-vault"></a>加入金鑰保存

您可以允許 Azure 自動化驗證金鑰保管庫和運行為帳戶服務主體是否使用自定義角色定義。 您必須：

* 向金鑰保管庫授予許可權。
* 設置訪問策略。

您可以使用 PowerShell 庫中的[擴充-自動化執行帳戶分配給 KeyVault.ps1](https://aka.ms/AA5hugb)文稿,將您的"作為執行「帳戶權限授予金鑰保管庫。 有關在密鑰保管庫上設置許可權的更多詳細資訊,請參閱[授予應用程式對密鑰保管庫](../key-vault/general/group-permissions-for-apps.md)的訪問。

## <a name="resolving-misconfiguration-issues-for-run-as-accounts"></a>解決「以帳戶身份執行」的配置錯誤問題

在初始設置期間,運行為"作為"或"經典運行為帳戶"帳戶所需的某些配置項可能已刪除或創建不當。 可能設定錯誤的情況包括:

* 憑證資產
* 連線資產
* 從"參與者"角色中刪除"作為帳戶運行"
* Azure AD 中的服務主體或應用程式

對於此類配置錯誤實例,自動化帳戶檢測更改,並在帳戶的"運行為帳戶"`Incomplete`屬性窗格中顯示 狀態。

![不完整的執行身分帳戶設定狀態](media/manage-runas-account/automation-account-runas-incomplete-config.png)

選擇「按身份執行」帳戶時,帳戶屬性窗格將顯示以下錯誤訊息:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

您可以藉由刪除並重新建立這些執行身分帳戶，來快速解決帳戶的問題。

## <a name="next-steps"></a>後續步驟

* 有關服務主體的詳細資訊,請參閱[應用程式物件和服務主體物件](../active-directory/develop/app-objects-and-service-principals.md)。
* 如需有關憑證和 Azure 服務的詳細資訊，請參閱 [Azure 雲端服務的憑證概觀](../cloud-services/cloud-services-certs-create.md)。

---
title: 在 Azure 自動化混合式 Runbook 背景工作角色上執行 Runbook
description: 本文提供有關使用混合式 Runbook 背景工作角色在本機資料中心或雲端提供者中的電腦上執行 runbook 的資訊。
services: automation
ms.subservice: process-automation
ms.date: 01/29/2019
ms.topic: conceptual
ms.openlocfilehash: a86139c7becaae996e343166088b416dd8d6404f
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855645"
---
# <a name="run-runbooks-on-a-hybrid-runbook-worker"></a>在混合式 Runbook 背景工作上啟動 Runbook

以混合式 Runbook 背景工作角色為目標的 runbook 通常會管理本機電腦上的資源，或針對部署背景工作的本機環境中的資源。 Azure 自動化中的 Runbook 通常會管理 Azure 雲端中的資源。 雖然它們的使用方式不同，但在 Azure 自動化中執行的 runbook 和在混合式 Runbook 背景工作角色上執行的 runbook 在結構中都相同。

當您撰寫要在混合式 Runbook 背景工作角色上執行的 runbook 時，您應該在裝載背景工作的電腦上編輯和測試 runbook。 主機電腦具有管理和存取本機資源所需的所有 PowerShell 模組和網路存取權。 一旦在混合式 Runbook 背景工作角色機器上測試 runbook 之後，您就可以將它上傳至 Azure 自動化環境，以便在背景工作角色上執行。 

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需有關混合式 Runbook 背景工作角色的 Az 模組安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="plan-runbook-job-behavior"></a>規劃 runbook 作業行為

Azure 自動化在混合式 Runbook 背景工作角色上處理工作，與在 Azure 沙箱中執行的作業有點不同。 如果您有長時間執行的 runbook，請確定它可以復原以重新開機。 如需作業行為的詳細資訊，請參閱[混合式 Runbook 背景工作角色作業](automation-hybrid-runbook-worker.md#hybrid-runbook-worker-jobs)。

請記住，混合式 Runbook 背景工作角色的工作是在 Windows 上的本機**系統**帳戶或 Linux 上的**nxautomation**帳戶下執行。 針對 Linux，請確定**nxautomation**帳戶具有儲存 runbook 模組之位置的存取權。 當您使用[Install 模組](/powershell/module/powershellget/install-module)Cmdlet 時，請務必為`Scope`參數指定 AllUsers，以確保**nxautomation**帳戶具有存取權。 如需 Linux 上 PowerShell 的詳細資訊，請參閱[非 Windows 平臺上的 Powershell 已知問題](https://docs.microsoft.com/powershell/scripting/whats-new/known-issues-ps6?view=powershell-6#known-issues-for-powershell-on-non-windows-platforms)。

## <a name="set-up-runbook-permissions"></a>設定 runbook 許可權

您可以透過下列方式，定義 runbook 在混合式 Runbook Manager 上執行的許可權：

* 讓 runbook 提供自己的驗證給本機資源。
* 使用[Azure 資源的受控識別來](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)設定驗證。 
* 指定執行身分帳戶以提供所有 runbook 的使用者內容。

## <a name="use-runbook-authentication-to-local-resources"></a>對本機資源使用 runbook 驗證

如果準備可提供自己的資源驗證的 runbook，請在您的 runbook 中[使用認證和](automation-certificates.md)[憑證](automation-credentials.md)資產。 有數個 Cmdlet 可讓您指定認證，讓 runbook 可以向不同的資源進行驗證。 下列範例顯示會重新啟動電腦的 Runbook 的一部分。 它會從認證資產中抓取認證，並從變數資產抓取電腦的名稱，然後使用這些值搭配`Restart-Computer` Cmdlet。

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

您也可以使用[InlineScript](automation-powershell-workflow.md#inlinescript)活動。 `InlineScript`可讓您使用[PSCredential 一般參數](/powershell/module/psworkflow/about/about_workflowcommonparameters)指定的認證，在另一部電腦上執行程式碼區塊。

## <a name="use-runbook-authentication-with-managed-identities"></a><a name="runbook-auth-managed-identities"></a>使用 runbook 驗證搭配受控識別

Azure 虛擬機器上的混合式 Runbook 背景工作角色可以使用 Azure 資源的受控識別來向 Azure 資源進行驗證。 使用 Azure 資源的受控識別而非執行身分帳戶會提供優點，因為您不需要：

* 匯出執行身分憑證，然後將它匯入到混合式 Runbook 背景工作角色。
* 更新執行身分帳戶所使用的憑證。
* 處理 runbook 程式碼中的執行身分連線物件。

遵循後續步驟，在混合式 Runbook 背景工作角色上使用 Azure 資源的受控識別。

1. 建立 Azure VM。
2. 在 VM 上設定 Azure 資源的受控識別。 請參閱[使用 Azure 入口網站在 VM 上設定 Azure 資源的受控](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)識別。
3. 在 Resource Manager 中，為 VM 提供資源群組的存取權。 請參閱[使用 WINDOWS VM 系統指派的受控識別來存取 Resource Manager](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)。
4. 在 VM 上安裝混合式 Runbook 背景工作角色。 請參閱[部署 Windows 混合式 Runbook 背景工作角色](automation-windows-hrw-install.md)。
5. 將 runbook 更新為使用[disconnect-azaccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) Cmdlet 搭配`Identity`參數，以向 Azure 資源進行驗證。 此設定可減少使用執行身分帳戶的需求，並執行相關聯的帳戶管理。

```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
```

> [!NOTE]
> `Connect-AzAccount -Identity`適用于混合式 Runbook 背景工作角色，使用系統指派的身分識別和單一使用者指派的身分識別。 如果您在混合式 Runbook 背景工作角色上使用多個使用者指派的身分識別， `AccountId`您的`Connect-AzAccount` Runbook 必須指定的參數，才能選取特定使用者指派的身分識別。

## <a name="use-runbook-authentication-with-run-as-account"></a>搭配執行身分帳戶使用 runbook 驗證

您可以指定混合式 Runbook 背景工作角色群組的執行身分帳戶，而不是讓 runbook 對本機資源提供自己的驗證。 若要這樣做，您必須定義具有本機資源存取權的[認證資產](automation-credentials.md)。 這些資源包括憑證存放區，以及在群組中的混合式 Runbook 背景工作角色上，以這些認證執行的所有 runbook。

認證的使用者名稱必須是下列格式之一：

* 網域\使用者名稱
* username@domain
* 使用者名稱 (適用於內部部署機器的本機帳戶)

請使用下列程式來指定混合式 Runbook 背景工作角色群組的執行身分帳戶。

1. 建立具有本機資源存取權的 [認證資產](automation-credentials.md) 。
2. 在 Azure 入口網站中，開啟自動化帳戶。
3. 選取 [Hybrid Worker 群組] **** 圖格，然後選取群組。
4. 選取 [**所有設定**]，後面接著 [混合式背景**工作角色群組設定**]。
5. 將 [**執行**身分] 的值從 [**預設**] 變更為 [**自訂**]。
6. 選取認證，然後按一下 [儲存] ****。

### <a name="install-run-as-account-certificate"></a><a name="runas-script"></a>安裝執行身分帳戶憑證

在 Azure 中部署資源的自動化組建程式中，您可能需要存取內部部署系統，以支援部署序列中的一項工作或一組步驟。 若要使用執行身分帳戶向 Azure 提供驗證，您必須安裝執行身分帳戶憑證。

下列 PowerShell runbook （稱為**匯出 export-runascertificatetohybridworker**）會從您的 Azure 自動化帳戶匯出執行身分憑證。 Runbook 會下載憑證，並將其匯入至已連線至相同帳戶之混合式 Runbook 背景工作角色上的本機電腦憑證存放區。 完成該步驟之後，runbook 就會確認背景工作角色可以使用執行身分帳戶成功向 Azure 進行驗證。

```azurepowershell-interactive
<#PSScriptInfo
.VERSION 1.0
.GUID 3a796b9a-623d-499d-86c8-c249f10a6986
.AUTHOR Azure Automation Team
.COMPANYNAME Microsoft
.COPYRIGHT
.TAGS Azure Automation
.LICENSEURI
.PROJECTURI
.ICONURI
.EXTERNALMODULEDEPENDENCIES
.REQUIREDSCRIPTS
.EXTERNALSCRIPTDEPENDENCIES
.RELEASENOTES
#>

<#
.SYNOPSIS
Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.

.DESCRIPTION
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account. Run this runbook on the hybrid worker where you want the certificate installed. This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running on the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
LASTEDIT: 2016.10.13
#>

# Generate the password used for this certificate
Add-Type -AssemblyName System.Web -ErrorAction SilentlyContinue | Out-Null
$Password = [System.Web.Security.Membership]::GeneratePassword(25, 10)

# Stop on errors
$ErrorActionPreference = 'stop'

# Get the management certificate that will be used to make calls into Azure Service Management resources
$RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"

# location to store temporary certificate in the Automation service host
$CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"

# Save the certificate
$Cert = $RunAsCert.Export("pfx",$Password)
Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose

Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
$SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

# Test to see if authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzContext -Subscription $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm that Azure Resource Manager calls are working
Get-AzAutomationAccount | Select-Object AutomationAccountName
```

>[!NOTE]
>針對 PowerShell Runbook，`Add-AzAccount` 和 `Add-AzureRMAccount` 是 `Connect-AzAccount` 的別名。 搜尋您的程式庫專案時，如果看不`Connect-AzAccount`到，您可以`Add-AzAccount`使用，或者可以在您的自動化帳戶中更新您的模組。

若要完成準備執行身分帳戶：

1. 將**匯出-export-runascertificatetohybridworker** runbook 儲存到您的電腦，副檔名為**ps1** 。
2. 將它匯入您的自動化帳戶。
3. 編輯 runbook，將`Password`變數的值變更為您自己的密碼。 
4. 發佈 runbook。
5. 執行 runbook，以使用執行身分帳戶執行和驗證 runbook 的混合式 Runbook 背景工作角色群組為目標。 
6. 檢查作業資料流程，以確認它會回報將憑證匯入本機電腦存放區的嘗試，並在後面加上多行。 此行為取決於您在訂閱中定義的自動化帳戶數目，以及驗證的成功程度。

## <a name="start-a-runbook-on-a-hybrid-runbook-worker"></a>在混合式 Runbook 背景工作角色上啟動 runbook

[在 Azure 自動化中啟動 runbook 說明用](start-runbooks.md)來啟動 runbook 的不同方法。 混合式 Runbook 背景工作角色上的 runbook 啟動會使用 [**執行于**] 選項，讓您指定混合式 Runbook 背景工作角色群組的名稱。 當指定群組時，該群組中的其中一個背景工作會抓取並執行 runbook。 如果您的 runbook 未指定此選項，Azure 自動化會如往常般執行 runbook。

當您在 Azure 入口網站中啟動 runbook 時，您會看到 [**執行于**] 選項，您可以選取 [ **Azure** ] 或 [混合式背景**工作角色**]。 如果您選取 [混合式背景**工作角色**]，可以從下拉式清單中選擇 [混合式 Runbook 背景工作角色群組]。

使用`RunOn`參數搭配[AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0) Cmdlet。 下列範例會使用 Windows PowerShell，在名為 Test-runbook 的混合式 Runbook 背景工作角色群組上啟動名為**Test-runbook**的 runbook。

```azurepowershell-interactive
Start-AzAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> 如果您已安裝[最新的 PowerShell 版本](https://azure.microsoft.com/downloads/)，您應該下載它。 請只在您從 PowerShell 啟動 runbook 的工作站上安裝此版本。 您不需要將它安裝在混合式 Runbook 背景工作電腦上，除非您想要從這部電腦啟動 runbook。

## <a name="work-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>在 Windows 混合式 Runbook 背景工作角色上使用已簽署的 runbook

您可以將 Windows 混合式 Runbook 背景工作角色設定為只執行已簽署的 runbook。

> [!IMPORTANT]
> 一旦將混合式 Runbook 背景工作角色設定為只能執行已簽署的 Runbook，尚未經過簽署的 Runbook 將無法在背景工作角色上執行。

### <a name="create-signing-certificate"></a>建立簽署憑證

下列範例會建立可用於簽署 Runbook 的自我簽署憑證。 此程式碼會建立憑證，並將它匯出，讓混合式 Runbook 背景工作角色可以稍後再將它匯入。 也會傳回指紋，供稍後用於參考憑證。

```powershell
# Create a self-signed certificate that can be used for code signing
$SigningCert = New-SelfSignedCertificate -CertStoreLocation cert:\LocalMachine\my `
                                        -Subject "CN=contoso.com" `
                                        -KeyAlgorithm RSA `
                                        -KeyLength 2048 `
                                        -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
                                        -KeyExportPolicy Exportable `
                                        -KeyUsage DigitalSignature `
                                        -Type CodeSigningCert


# Export the certificate so that it can be imported to the hybrid workers
Export-Certificate -Cert $SigningCert -FilePath .\hybridworkersigningcertificate.cer

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Retrieve the thumbprint for later use
$SigningCert.Thumbprint
```

### <a name="import-certificate-and-configure-workers-for-signature-validation"></a>匯入憑證並設定簽章驗證的背景工作

將您建立的憑證複製到群組中的每個混合式 Runbook 背景工作角色。 執行下列腳本以匯入憑證，並設定背景工作角色在 runbook 上使用簽章驗證。

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>使用憑證簽署您的 runbook

當混合式 Runbook 背景工作角色設定為僅使用已簽署的 runbook 時，您必須簽署要在混合式 Runbook 背景工作角色上使用的 runbook。 使用下列範例 PowerShell 程式碼來簽署這些 runbook。

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

簽署 runbook 之後，您必須將它匯入到您的自動化帳戶，並使用簽章區塊發佈它。 若要瞭解如何匯入 runbook，請參閱匯[入 runbook](manage-runbooks.md#import-a-runbook)。

## <a name="work-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>在 Linux 混合式 Runbook 背景工作角色上使用已簽署的 runbook

若要能夠使用已簽署的 runbook，Linux 混合式 Runbook 背景工作角色必須具有本機電腦上的[GPG](https://gnupg.org/index.html)可執行檔。

> [!IMPORTANT]
> 一旦將混合式 Runbook 背景工作角色設定為只能執行已簽署的 Runbook，尚未經過簽署的 Runbook 將無法在背景工作角色上執行。

### <a name="create-a-gpg-keyring-and-keypair"></a>建立 GPG Keyring 和金鑰組

若要建立 GPG keyring 和金鑰組，請使用混合式 Runbook 背景工作**nxautomation**帳戶。

1. 使用 sudo 應用程式，以**nxautomation**帳戶的身分登入。

    ```bash
    sudo su – nxautomation
    ```

2. 一旦您使用**nxautomation**，請產生 GPG 金鑰組。 GPG 會引導您完成這些步驟。 您必須提供名稱、電子郵件地址、到期時間和複雜密碼。 然後您會等到機器上有足夠的熵來產生金鑰。

    ```bash
    sudo gpg --generate-key
    ```

3. 由於 GPG 目錄是使用 sudo 產生的，因此您必須使用下列命令，將其擁有者變更為**nxautomation** 。

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>將 keyring 提供給混合式 Runbook 背景工作角色

建立 keyring 之後，請將它提供給混合式 Runbook 背景工作角色。 修改設定檔 **/var/opt/microsoft/omsagent/state/automationworker/diy/worker.conf** ，以在 [檔案] 區段`[worker-optional]`底下包含下列範例程式碼。

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>確認簽章驗證已開啟

如果電腦上已停用簽章驗證，您必須執行下列 sudo 命令來開啟它。 將`<LogAnalyticsworkspaceId>`取代為您的工作區識別碼。

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>簽署 Runbook

一旦您已設定簽章驗證，請使用下列 GPG 命令來簽署 runbook。

```bash
gpg –-clear-sign <runbook name>
```

已簽署的 runbook 稱為** <runbook name>. asc**。

您現在可以將已簽署的 runbook 上傳至 Azure 自動化，並以一般 runbook 的形式執行。

## <a name="next-steps"></a>後續步驟

* 若要瞭解如何在 Azure 自動化中使用文字式編輯器來處理 PowerShell runbook，請參閱[在 Azure 自動化中編輯 Runbook](automation-edit-textual-runbook.md)。
* 如果您的 runbook 未順利完成，請參閱[runbook 執行失敗](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails)的疑難排解指南。
* 如需 PowerShell 的詳細資訊 (包括語言參考和學習模組)，請參閱 [PowerShell 文件](https://docs.microsoft.com/powershell/scripting/overview)。
* 如需 PowerShell Cmdlet 參考，請參閱 [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)。

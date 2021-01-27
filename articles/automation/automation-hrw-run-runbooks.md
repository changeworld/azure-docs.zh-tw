---
title: 在混合式 Runbook 背景工作角色上執行 Azure 自動化 Runbook
description: 本文說明如何使用混合式 Runbook 背景工作角色，在您的本機資料中心或其他雲端提供者的機器上執行 runbook。
services: automation
ms.subservice: process-automation
ms.date: 10/06/2020
ms.topic: conceptual
ms.openlocfilehash: 4a080505f780e724bfd2ab997f5c823e467c4bec
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896964"
---
# <a name="run-runbooks-on-a-hybrid-runbook-worker"></a>在混合式 Runbook 背景工作上啟動 Runbook

在[混合式 Runbook 背景工作角色](automation-hybrid-runbook-worker.md)上執行的 Runbook 通常會管理本機電腦上的資源，或管理部署背景工作角色所在的本機環境之中的資源。 Azure 自動化中的 Runbook 通常會管理 Azure 雲端中的資源。 對於 Azure 自動化和混合式 Runbook 背景工作上執行的 Runbook，雖然使用方法不同，不過結構都相同。

當您撰寫要在混合式 Runbook 背景工作角色上執行的 Runbook 時，應該在裝載背景工作角色的機器內編輯並測試 Runbook。 主機電腦具有您管理本機資源所需要的所有 PowerShell 模組和網路存取。 一旦在混合式 Runbook 背景工作角色機器上測試 Runbook，接著可以將其上傳至 Azure 自動化環境，其可供您在背景工作中執行。

## <a name="plan-runbook-job-behavior"></a>規劃 Runbook 作業行為

Azure 自動化處理混合式 Runbook 背景工作角色上的作業，與在 Azure 沙箱中執行的作業不同。 如果有長時間執行的 Runbook，請確定能夠接受可能重新啟動。 如需作業行為的詳細資訊，請參閱[混合式 Runbook 背景工作角色作業](automation-hybrid-runbook-worker.md#hybrid-runbook-worker-jobs)。

混合式 Runbook 背景工作角色的作業會在 Windows 上的本機 **系統** 帳戶或 Linux 上的 **>nxautomation** 帳戶下執行。 若為 Linux，請確認 **>nxautomation** 帳戶可以存取儲存 runbook 模組的位置。 您使用 [Install 模組](/powershell/module/powershellget/install-module) Cmdlet 時，請務必為 `Scope` 參數指定 AllUsers，以確保 **nxautomation** 帳戶具有存取權。 如需 Linux 上的 PowerShell 有關的詳細資訊，請參閱[非 Windows 平台上的 PowerShell 已知問題](/powershell/scripting/whats-new/what-s-new-in-powershell-70)。

## <a name="configure-runbook-permissions"></a>設定 runbook 許可權

以下列方式定義 Runbook 在混合式 Runbook 背景工作角色上執行的權限：

* 讓 Runbook 提供自己的驗證給本機資源。
* 使用 [Azure 資源的受控識別驗證](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)設定驗證。
* 指定執行身分帳戶以對所有 Runbook 提供使用者內容。

### <a name="use-runbook-authentication-to-local-resources"></a>對本機資源使用 Runbook 驗證

如果準備可提供自己對資源驗證的 Runbook，請在 Runbook 中使用[認證](./shared-resources/credentials.md)和[憑證](./shared-resources/certificates.md)資產。 有數個 Cmdlet 可讓您指定認證，讓 Runbook 可以向不同的資源進行驗證。 下列範例顯示會重新啟動電腦的 Runbook 的一部分。 它會從認證資產擷取認證和從變數資產擷取電腦的名稱，然後使用這些值搭配 `Restart-Computer` Cmdlet。

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

您也可以使用 [InlineScript](automation-powershell-workflow.md#use-inlinescript) 活動。 `InlineScript` 可讓您在具有認證的另一部電腦上執行程式碼區塊。

### <a name="use-runbook-authentication-with-managed-identities"></a><a name="runbook-auth-managed-identities"></a>使用 Runbook 驗證搭配受控識別

在 Azure 虛擬機器上的混合式 Runbook 背景工作角色，可以使用受控識別向 Azure 資源進行驗證。 使用 Azure 資源的受控識別而非執行身分帳戶的好處是您不需要：

* 匯出執行身分憑證，然後將其匯入混合式 Runbook 背景工作角色。
* 更新執行身分帳戶所使用的憑證。
* 處理 Runbook 程式碼中的執行身分連線物件。

按照下列步驟，在混合式 Runbook 背景工作角色上使用 Azure 資源的受控識別：

1. 建立 Azure VM。
2. 在 VM 上設定 Azure 資源的受控識別。 請參閱[使用 Azure 入口網站在虛擬機器上設定 Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)。
3. 在 Resource Manager 中將您的 VM 存取權授與資源群組。 請參閱[使用 Windows VM 系統指派的受控識別來存取 Resource Manager](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)。
4. 在 VM 上安裝混合式 Runbook 背景工作角色。 請參閱[部署 Windows 混合式 Runbook 背景工作角色](automation-windows-hrw-install.md)或[部署 Linux 混合式 Runbook 背景工作角色](automation-linux-hrw-install.md)。
5. 更新 Runbook 以搭配使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) Cmdlet 與 `Identity` 參數，以向 Azure 資源進行驗證。 此組態可減少使用執行身分帳戶及執行相關聯的帳戶管理。

    ```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
    ```

    > [!NOTE]
    > `Connect-AzAccount -Identity` 適用於混合式 Runbook 背景工作角色 (使用系統指派的身分識別和單一使用者指派的身分識別)。 如果您在混合式 Runbook 背景工作角色上使用多個使用者指派的身分識別，您的 Runbook 必須指定 `Connect-AzAccount` 的 `AccountId` 參數，才能選取特定使用者指派的身分識別。

### <a name="use-runbook-authentication-with-run-as-account"></a>搭配執行身分帳戶使用 Runbook 驗證

您不需要讓 Runbook 提供自己的驗證給本機資源，您可以針對混合式 Runbook 背景工作角色群組指定執行身分帳戶。 若要指定執行身分帳戶，您必須定義可存取本機資源的 [認證資產](./shared-resources/credentials.md) 。 這些資源包括憑證存放區，以及在群組中以混合式 Runbook 背景工作角色的這些認證執行的所有 Runbook。

認證的使用者名稱必須是下列格式之一：

* 網域\使用者名稱
* username@domain
* 使用者名稱 (適用於內部部署機器的本機帳戶)

使用下列程序以針對混合式 Runbook 背景工作角色群組指定執行身分帳戶：

1. 建立具有本機資源存取權的 [認證資產](./shared-resources/credentials.md) 。
2. 在 Azure 入口網站中，開啟自動化帳戶。
3. 選取 [Hybrid Worker 群組]，然後選取特定群組。
4. 選取 [所有設定]，然後選取 [Hybrid 背景工作角色群組設定]。
5. 將 [執行身分] 的值從 [預設] 變更為 [自訂]。
6. 選取認證，然後按一下 [儲存] 。

## <a name="install-run-as-account-certificate"></a><a name="runas-script"></a>安裝「執行身分」帳戶的憑證

在 Azure 中部署資源的自動化建置流程中，您可能需要存取內部部署系統來支援部署序列中的工作或一組步驟。 若要提供使用「執行身分」帳戶向 Azure 驗證，您需要安裝「執行身分」帳戶憑證。

下列 PowerShell Runbook (稱為 **Export-RunAsCertificateToHybridWorker**) 會從您的 Azure 自動化帳戶匯出執行身分憑證。 Runbook 會下載憑證，並將其匯入至已連線至相同帳戶的混合式 Runbook 背景工作角色上具有的本機電腦憑證存放區。 完成該步驟後，Runbook 會確認背景工作角色可以使用執行身分帳戶成功向 Azure 驗證。

>[!NOTE]
>此 PowerShell runbook 未設計或無法在您的自動化帳戶外部執行，做為目的電腦上的腳本。
>

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
>針對 PowerShell Runbook，`Add-AzAccount` 和 `Add-AzureRMAccount` 是 `Connect-AzAccount` 的別名。 搜尋您的程式庫項目時，如果沒看到 `Connect-AzAccount`，您可以使用 `Add-AzAccount`，或是在自動化帳戶中更新模組。

若要完成準備執行身分帳戶：

1. 將 **Export-RunAsCertificateToHybridWorker** Runbook 以 **.ps1** 副檔名儲存至您的電腦。
2. 將它匯入您的自動化帳戶。
3. 編輯 Runbook，將 `Password` 變數的值變更為您自己的密碼。
4. 發佈 Runbook。
5. 以使用「執行身分」帳戶執行和驗證 Runbook 的混合式 Runbook 背景工作角色群組為對象，執行 Runbook。 
6. 檢查作業串流，確認報告將憑證匯入本機電腦存放區的嘗試，後面還會出現幾行。 此行為根據訂用帳戶中定義多少自動化帳戶和驗證的成功程度。

## <a name="work-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>在 Windows 混合式 Runbook 背景工作角色上使用已簽署的 Runbook

您可以將 Windows 混合式 Runbook 背景工作角色設定為只執行已簽署的 Runbook。

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

將建立的憑證複製到群組中的每個混合式 Runbook 背景工作角色。 執行下列指令碼來匯入憑證並設定背景工作角色，以便在 Runbook 中使用簽章驗證。

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>使用憑證簽署您的 Runbook

將混合式 Runbook 背景工作角色設定為僅使用已簽署的 Runbook，您必須簽署要在混合式 Runbook 背景工作角色上使用的 Runbook。 請使用下列範例 PowerShell 程式碼來簽署這些 Runbook。

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

簽署 Runbook 後，您必須將它匯入到您的自動化帳戶，並與簽章區塊一起發佈。 若要瞭解如何匯入 Runbook，請參閱[匯入 Runbook](manage-runbooks.md#import-a-runbook)。

## <a name="work-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>在 Linux 混合式 Runbook 背景工作角色上使用已簽署的 Runbook

為了能夠使用已簽署的 Runbook，Linux 混合式 Runbook 背景工作角色必須具有本機電腦上的 [GPG](https://gnupg.org/index.html) 可執行檔。

> [!IMPORTANT]
> 一旦將混合式 Runbook 背景工作角色設定為只能執行已簽署的 Runbook，尚未經過簽署的 Runbook 將無法在背景工作角色上執行。

您將執行下列步驟來完成此設定：

* 建立 GPG Keyring 和金鑰組
* 使 Keyring 可供混合式 Runbook 背景工作角色使用
* 確認簽章驗證已開啟
* 簽署 Runbook

### <a name="create-a-gpg-keyring-and-keypair"></a>建立 GPG Keyring 和金鑰組

若要建立 GPG Keyring 和金鑰組，請使用混合式 Runbook 背景工作角色 [nxautomation 帳戶](automation-runbook-execution.md#log-analytics-agent-for-linux)。

1. 使用 sudo 應用程式，以 **nxautomation** 帳戶登入。

    ```bash
    sudo su – nxautomation
    ```

2. 在您使用 **nxautomation** 時，請產生 GPG 金鑰組。 GPG 會指導您逐步執行步驟。 您必須提供名稱、電子郵件地址、到期時間和複雜密碼。 接下來請等到機器上有足夠的熵來產生金鑰。

    ```bash
    sudo gpg --generate-key
    ```

3. 由於 GPG 目錄是使用 sudo 所產生的，因此，您必須使用下列命令將其擁有者變更為 **nxautomation**。

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>使 Keyring 可供混合式 Runbook 背景工作角色使用

建立 Keyring 之後，使它可供混合式 Runbook 背景工作角色使用。 修改設定檔的 **home/>nxautomation/state/worker. 會議** ，以將下列範例程式碼包含在 file 區段下 `[worker-optional]` 。

```bash
gpg_public_keyring_path = /home/nxautomation/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>確認簽章驗證已開啟

如果電腦上已停用簽章驗證，您必須執行下列 sudo 命令來開啟它。 使用您的工作區識別碼來取代 `<LogAnalyticsworkspaceId>`。

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>簽署 Runbook

設定簽章驗證之後，使用下列命令來簽署 Runbook。

```bash
gpg –-clear-sign <runbook name>
```

已簽署的 Runbook 稱為 **<runbook name>.asc**。

您現在即可將已簽署的 Runbook 上傳至 Azure 自動化，而且像一般 Runbook 一樣執行。

## <a name="start-a-runbook-on-a-hybrid-runbook-worker"></a>在混合式 Runbook 背景工作角色上啟動 Runbook

[在 Azure 自動化中啟動 Runbook](start-runbooks.md) 描述啟動 Runbook 的不同方法。 在混合式 Runbook 背景工作角色上啟動 runbook 時，會使用 [ **執行于** ] 選項，可讓您指定混合式 Runbook 背景工作角色群組的名稱。 指定群組時，該群組中的其中一個背景工作角色會擷取並執行 Runbook。 如果您的 Runbook 未指定此選項，Azure 自動化會如往常般執行 Runbook。

在 Azure 入口網站中啟動 Runbook 時，您會看到 [執行於] 選項，您可以選取 [Azure] 或 [Hybrid Worker]。 如果您選取 [混合式背景工作角色]，您可以從下拉式清單中選擇混合式 Runbook 背景工作角色群組。

使用 PowerShell 啟動 Runbook 時，請使用 `RunOn` 參數搭配 [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) Cmdlet。 下列範例使用 Windows PowerShell 在混合式 Runbook 背景工作角色群組上啟動名為 **Test-Runbook** 的 Runbook。

```azurepowershell-interactive
Start-AzAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

## <a name="logging"></a>記錄

為了協助您針對在混合式 runbook 背景工作角色上執行的 runbook 問題進行疑難排解，記錄會儲存在本機的下列位置：

* 在上的 Windows 中 `C:\ProgramData\Microsoft\System Center\Orchestrator\<version>\SMA\Sandboxes` ，以取得詳細的作業執行時間進程記錄。 高階 runbook 作業狀態事件會寫入至 **應用程式和服務 Logs\Microsoft-Automation\Operations** 事件記錄檔。

* 在 Linux 上，您可以在中找到使用者混合式背景工作記錄，也可以在中 `/home/nxautomation/run/worker.log` 找到系統 runbook 背景工作記錄檔 `/var/opt/microsoft/omsagent/run/automationworker/worker.log` 。

## <a name="next-steps"></a>後續步驟

* 如果您的 Runbook 未順利完成，請檢閱 [Runbook 執行失敗](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails)中的疑難排解指南。
* 如需 PowerShell 的詳細資訊，包括語言參考和學習模組，請參閱 [powershell](/powershell/scripting/overview)檔。
* 如需 PowerShell Cmdlet 參考，請參閱 [Az.Automation](/powershell/module/az.automation)。

---
title: 在 Azure 自動化混合式 Runbook 背景工作角色上執行 Runbook
description: 本文章提供在您的本機資料中心的電腦上執行 Runbook，或使用混合式 Runbook 背景工作角色之雲端提供者的相關資訊。
services: automation
ms.subservice: process-automation
ms.date: 01/29/2019
ms.topic: conceptual
ms.openlocfilehash: b65c72e0c65cf9aa84cb614478fbdf78258f3054
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405833"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>在混合式 Runbook 背景工作角色上執行 Runbook

以混合 Runbook 工作線程為目標的 Runbook 通常管理本地電腦上的資源,或針對部署該輔助角色的本地環境中的資源。 Azure 自動化中的 Runbook 通常會管理 Azure 雲端中的資源。 儘管它們使用方式不同,但在 Azure 自動化中運行的 Runbook 和在混合 Runbook 工作線程上運行的 Runbook 在結構上是相同的。

當您創作要在混合 Runbook 工作線程上運行的 Runbook 時,應在承載該輔助角色的電腦上編輯和測試 Runbook。 主機具有管理和訪問本地資源所需的所有 PowerShell 模組和網路訪問。 在混合 Runbook 工作電腦上測試 Runbook 后,可以將其上傳到 Azure 自動化環境,可在該工作線程上運行。 

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有關混合 Runbook 輔助角色上的 Az 模組安裝說明,請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 對於自動化帳戶,可以使用[「如何更新 Azure 自動化 中的 Azure PowerShell」模組](automation-update-azure-modules.md)將模組更新到最新版本。

## <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>混合 Runbook 工作人員的 Runbook 權限

由於它們正在造訪非 Azure 資源,因此在混合 Runbook 工作線程上運行的 Runbook 無法使用 Runbook 身份驗證到 Azure 資源通常使用的身份驗證機制。 Runbook 要麼向本地資源提供自己的身份驗證,要麼使用[Azure 資源的託管標識](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)配置身份驗證。 您還可以指定「執行為「帳戶,以便為所有 Runbook 提供使用者上下文。

### <a name="runbook-authentication"></a>Runbook 驗證

默認情況下,運行簿在本地電腦上運行。 對於 Windows,它們在本地**系統**帳戶的上下文中運行。 對於 Linux,它們在特殊使用者帳戶**nx 自動化**的上下文中運行。 在這兩種情況下,Runbook 都必須向它們訪問的資源提供自己的身份驗證。

您可以在 Runbook 中使用[認證](automation-credentials.md)和[憑證](automation-certificates.md)資產,其中可以使用 cmdlet,以便 runbook 可以對不同的資源進行身份驗證。 下列範例顯示會重新啟動電腦的 Runbook 的一部分。 它將從憑據資產檢索憑據和計算機名稱從變數資產中檢索,然後將這些值與`Restart-Computer`cmdlet 一起使用。

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

您還可以使用[內聯腳本](automation-powershell-workflow.md#inlinescript)活動。 `InlineScript`允許您在另一台電腦上運行代碼塊,其憑據由[PS憑據通用參數](/powershell/module/psworkflow/about/about_workflowcommonparameters)指定。

### <a name="run-as-account"></a>執行身分帳戶

您可以為混合 Runbook 工作組指定「執行為帳戶」,而不是讓 Runbook 向本地資源提供自己的身份驗證。 為此,必須定義有權訪問本地資源的[認證資產](automation-credentials.md)。 這些資源包括憑證存儲和在組中的混合 Runbook 輔助角色上在這些認證下運行的所有 Runbook。

認證的使用者名稱必須是下列格式之一：

* 網域\使用者名稱
* username@domain
* 使用者名稱 (適用於內部部署機器的本機帳戶)

使用以下過程為混合 Runbook 工作組指定「運行為帳戶」 科目。

1. 建立具有本機資源存取權的 [認證資產](automation-credentials.md) 。
2. 在 Azure 入口網站中，開啟自動化帳戶。
3. 選取 [Hybrid Worker 群組] **** 圖格，然後選取群組。
4. 選擇 **'所有'設定**,後跟**混合輔助角色群組設定**。
5. 將 **「執行為預設**」的值**更改為****「自訂**」 。
6. 選取認證，然後按一下 [儲存] ****。

### <a name="managed-identities-for-azure-resources"></a><a name="managed-identities-for-azure-resources"></a>Azure 資源的託管識別

Azure 虛擬機上的混合 Runbook 工作人員可以使用 Azure 資源的託管標識對 Azure 資源進行身份驗證。 對 Azure 資源使用託管識別而不是執行「作為帳戶」可提供好處,因為您不需要:

* 匯出"運行為證書",然後將其導入混合 Runbook 輔助角色。
* 續訂"運行為"帳戶使用的證書。
* 在 Runbook 代碼中處理"作為連接" 執行物件。

按照後續步驟在混合 Runbook 工作線程上為 Azure 資源使用託管標識。

1. 創建 Azure VM。
2. 為 VM 上的 Azure 資源配置託管標識。 請參考[Azure 門戶 在 VM 上設定 Azure 資源的託管識別](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)。
3. 授予 VM 對資源管理器中的資源組的訪問許可權。 請參考[使用 Windows VM 系統分配的託管識別存取資源管理員](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)。
4. 在 VM 上安裝混合 Runbook 工作線程。 請參考[部署 Windows 混合 Runbook 工作線程式](automation-windows-hrw-install.md)。
5. 更新 Runbook 以使用帶有參數`Identity`的連接[-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) cmdlet 對 Azure 資源進行身份驗證。 此配置減少了使用"運行為"帳戶並執行關聯帳戶管理的需要。

```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
```

> [!NOTE]
> `Connect-AzAccount -Identity`使用系統分配的標識和單個使用者分配的標識為混合 Runbook 工作線程工作。 如果在混合 Runbook 工作線程上使用多個使用者分配標識,則 Runbook 必須指定`AccountId`用於`Connect-AzAccount`選擇特定使用者分配的標識的參數。

### <a name="automation-run-as-account"></a><a name="runas-script"></a>自動化執行身分帳戶

作為在 Azure 中部署資源的自動生成過程的一部分,您可能需要訪問本地系統以支援部署序列中的任務或步驟集。 要使用"運行為"帳戶針對 Azure 提供身份驗證,必須安裝"運行為帳戶"證書。

以下 PowerShell 執行簿稱為 **「匯出-運行證書到混合輔助角色**」,從 Azure 自動化帳戶匯出「作為運行證書」。 Runbook 會下載證書並將其導入連接到同一帳戶的混合 Runbook 輔助器上的本地電腦證書儲存。 完成此步驟后,Runbook 將驗證工作人員是否可以使用"運行 As"帳戶成功向 Azure 進行身份驗證。

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
>對於 PowerShell`Add-AzAccount``Add-AzureRMAccount`執行簿`Connect-AzAccount`,並且是 的別名。 搜尋庫專案時,如果看不到`Connect-AzAccount`,則可以`Add-AzAccount`使用 ,也可以在自動化帳戶中更新模組。

要完成「運行為帳戶」的準備:

1. 使用 **.ps1**擴展將**匯出運行憑證儲存到混合輔助程式**執行簿到您的電腦。
2. 將其導入您的自動化帳戶。
3. 編輯 Runbook,`Password`將 變數的值更改為您自己的密碼。 
4. 發佈 Runbook。
5. 運行 Runbook,以使用「運行即」帳戶運行和驗證 Runbook 的混合 Runbook 工作組為目標。 
6. 檢查作業流以查看它報告嘗試將證書導入本地電腦存儲,並遵循多行。 此行為取決於您在訂閱中定義的自動化帳戶數以及身份驗證的成功程度。

## <a name="job-behavior-on-hybrid-runbook-workers"></a>混合 Runbook 工作簿上的作業行為

Azure 自動化處理混合 Runbook 工作簿上的作業與 Azure 沙箱中運行的作業略有不同。 一個關鍵區別是 Runbook 工作人員的作業持續時間沒有限制。 由於[共用公平](automation-runbook-execution.md#fair-share),在 Azure 沙箱中運行的 Runbook 限制為三個小時。

對於長時間運行的運行手冊,您希望確保它具有對可能重新啟動的彈性,例如,如果承載輔助角色的電腦重新啟動。 如果混合 Runbook 工作線程主機重新啟動,則任何正在運行的 Runbook 作業將從頭開始重新啟動,或從 PowerShell 工作流執行簿的最後一個檢查點重新啟動。 運行簿作業重新啟動三次以上后,它將掛起。

請記住,混合 Runbook 輔助角色的工作在 Windows 上的本地系統帳戶或 Linux 上的**nx 自動化**帳戶下運行。 對於 Linux,必須確保 nx**自動化**帳戶有權訪問 Runbook 模組的儲存位置。 使用[安裝模組](/powershell/module/powershellget/install-module)cmdlet 時,請`Scope`確保為 參數指定 AllUser,以確保 nx**自動化**帳戶具有訪問許可權。 有關 Linux 上的 PowerShell 的詳細資訊,請參閱[非 Windows 平臺上的 PowerShell 已知問題](https://docs.microsoft.com/powershell/scripting/whats-new/known-issues-ps6?view=powershell-6#known-issues-for-powershell-on-non-windows-platforms)。

## <a name="starting-a-runbook-on-a-hybrid-runbook-worker"></a>在混合 Runbook 工作線程上啟動 Runbook

[在 Azure 自動化中啟動 Runbook](automation-starting-a-runbook.md)描述了啟動 Runbook 的不同方法。 在混合 Runbook 工作線程上啟動 Runbook 使用「**執行」** 選項,該選項允許您指定混合 Runbook 工作群體的名稱。 指定組時,該組中的一個工作人員將檢索並運行 Runbook。 如果 Runbook 未指定此選項,Azure 自動化將照常運行 Runbook。

在 Azure 門戶中啟動 Runbook 時,會顯示 **「 在上執行**」選項,並為此選擇**Azure**或**混合輔助角色**。 如果選擇**混合輔助角色**,則可以從下拉清單中選擇混合 Runbook 工作群體。

將`RunOn`參數與[「開始-阿茲自動化 Runbook」cmdlet](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0)一起使用。 下面的範例使用 Windows PowerShell 在名為 MyHybridGroup 的混合 Runbook 工作組上啟動名為 **「測試執行簿」的 Runbook。**

```azurepowershell-interactive
Start-AzAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> 如果您安裝了較早的版本[,則應下載最新的 PowerShell 版本](https://azure.microsoft.com/downloads/)。 僅在從 PowerShell 啟動 Runbook 的工作站上安裝此版本。 除非打算從該計算機開始運行手冊,否則無需將其安裝在混合 Runbook 輔助電腦電腦上。

## <a name="working-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>在 Windows 混合 Runbook 工作線程式上使用簽名的 Runbook

您可以將 Windows 混合 Runbook 輔助角色設定為僅執行已簽名的 Runbook。

> [!IMPORTANT]
> 一旦將混合式 Runbook 背景工作角色設定為只能執行已簽署的 Runbook，尚未經過簽署的 Runbook 將無法在背景工作角色上執行。

### <a name="create-signing-certificate"></a>建立簽署憑證

下列範例會建立可用於簽署 Runbook 的自我簽署憑證。 此代碼創建證書並匯出證書,以便混合 Runbook 輔助角色以後可以導入它。 還會返回指紋,以便以後用於引用證書。

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

### <a name="import-certificate-and-configure-workers-for-signature-validation"></a>匯入憑證並設定簽章驗證的輔助人員

將創建的證書複製到組中的每個混合 Runbook 工作人員。 運行以下文本以導入證書並將輔助程式配置為在 Runbook 上使用簽名驗證。

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>使用憑證對 Runbook 簽署

將混合 Runbook 輔助角色配置為僅使用已簽名的 Runbook 時,必須對要在混合 Runbook 輔助角色上使用的 Runbook 進行簽名。 使用以下範例 PowerShell 代碼對這些 Runbook 進行簽名。

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

簽署 Runbook 後,必須將其導入自動化帳戶,然後使用簽名塊進行發佈。 若要了解如何匯入 Runbook，請參閱[將 Runbook 從檔案匯入 Azure 自動化](manage-runbooks.md#importing-a-runbook)。

## <a name="working-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>Linux 混合 Runbook 工作線程式上使用簽名的 Runbook

為了能夠使用簽名的 Runbook,Linux 混合 Runbook 工作線程必須在本地電腦上具有[GPG](https://gnupg.org/index.html)可執行檔。

> [!IMPORTANT]
> 一旦將混合式 Runbook 背景工作角色設定為只能執行已簽署的 Runbook，尚未經過簽署的 Runbook 將無法在背景工作角色上執行。

### <a name="create-a-gpg-keyring-and-keypair"></a>建立 GPG Keyring 和金鑰組

要創建 GPG 金鑰環和密鑰對,請使用混合 Runbook 輔助角色**nx 自動化**帳戶。

1. 使用 sudo 應用程式作為 nx**自動化**帳戶登錄。

    ```bash
    sudo su – nxautomation
    ```

2. 使用 nx**自動化**後,生成 GPG 金鑰對。 GPG 將指導您完成這些步驟。 您必須提供姓名、電子郵件地址、過期時間和密碼。 然後等待,直到機器上有足夠的熵來生成密鑰。

    ```bash
    sudo gpg --generate-key
    ```

3. 由於 GPG 目錄是使用 sudo 產生的,因此必須使用以下指令將其所有人改變為**nx 自動化**。

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>使金鑰環可供混合 Runbook 工作線程式使用

建立金鑰環後,使其可供混合 Runbook 輔助角色使用。 修改設定檔 **/var/opt/microsoft/omsagent/狀態/自動化工作人員/diy/worker.conf,** 以在`[worker-optional]`檔部分 下包括以下示例代碼。

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>驗證簽章認證是否開啟

如果電腦上禁用了簽名驗證,則必須通過運行以下 sudo 命令將其打開。 替換為`<LogAnalyticsworkspaceId>`工作區 ID。

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>簽署 Runbook

配置簽名驗證後,請使用以下 GPG 命令對 Runbook 進行簽名。

```bash
gpg –-clear-sign <runbook name>
```

簽章的執行手冊稱為**<runbook name>.asc**。

現在,您可以將簽名的 Runbook 上傳到 Azure 自動化,並像一般 Runbook 一樣執行它。

## <a name="next-steps"></a>後續步驟

* 要瞭解有關啟動 Runbook 的方法的更多資訊,請參閱[在 Azure 自動化 中啟動 Runbook。](automation-starting-a-runbook.md)
* 要瞭解如何使用文字編輯器在 Azure 自動化中使用 PowerShell Runbook,請參閱在[Azure 自動化中編輯 Runbook](automation-edit-textual-runbook.md)。
* 如果 Runbook 未成功完成,請查看[Runbook 執行失敗的](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails)故障排除指南。
* 有關 PowerShell 的更多資訊(包括語言參考和學習模組),請參閱[PowerShell 文件](https://docs.microsoft.com/powershell/scripting/overview)。
* 有關 PowerShell cmdlet 引用,請參閱[Az.自動化](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)。

---
title: 管理 Azure 自動化中的憑證
description: Azure 自動化安全地儲存憑證，讓 runbook 或 DSC 設定可以存取它們，以對 Azure 和協力廠商資源進行驗證。 本文說明憑證的詳細資料，以及如何在文字和圖形化編寫中使用它們。
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2793679fb4588d00ea4e37340b19183398cb9d90
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864312"
---
# <a name="manage-certificates-in-azure-automation"></a>管理 Azure 自動化中的憑證

Azure 自動化會使用 Azure Resource Manager 資源的[AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) Cmdlet，安全地儲存憑證以供 RUNBOOK 和 DSC 設定存取。 安全憑證儲存體可讓您建立使用憑證進行驗證的 runbook 和 DSC 設定，或將它們新增至 Azure 或協力廠商資源。

>[!NOTE]
>Azure 自動化中的安全資產包括認證、憑證、連接和加密的變數。 這些資產會使用針對每個自動化帳戶產生的唯一金鑰，以加密並儲存在自動化中。 自動化會將金鑰儲存在系統管理的 Key Vault 服務中。 儲存安全資產之前，自動化會從 Key Vault 載入金鑰，然後使用它來加密資產。 

>[!NOTE]
>本文說明如何使用 Azure PowerShell Az 模組。 您仍然可以使用 AzureRM 模組。 若要深入瞭解 Az 模組和 AzureRM 相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需混合式 Runbook 背景工作角色上的 Az module 安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](../automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="powershell-cmdlets-to-access-certificates"></a>用來存取憑證的 PowerShell Cmdlet

下表中的 Cmdlet 會使用 PowerShell 建立和管理自動化憑證。 它們會隨附于[Az 模組](modules.md#az-modules)中。

|Cmdlet |描述|
| --- | ---|
|[AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0)|擷取要在 Runbook 或 DSC 組態中使用的憑證相關資訊。 您只能使用 internal `Get-AutomationCertificate` Cmdlet 來捕獲憑證本身。|
|[新增-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.7.0)|在自動化中建立新的憑證。|
|[移除-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationCertificate?view=azps-3.7.0)|從自動化中移除憑證。|
|[設定-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationCertificate?view=azps-3.7.0)|設定現有憑證的屬性，包括上傳憑證檔案和設定 **.pfx**檔案的密碼。|

[Azurecertificate 為指令程式](/powershell/module/servicemanagement/azure/add-azurecertificate)也可以用來上傳指定之雲端服務的服務憑證。

## <a name="internal-cmdlets-to-access-certificates"></a>存取憑證的內部 Cmdlet

下表中的內部 Cmdlet 是用來存取 runbook 中的憑證。 此 Cmdlet 隨附于全域模組`Orchestrator.AssetManagement.Cmdlets`。 如需詳細資訊，請參閱[內部 Cmdlet](modules.md#internal-cmdlets)。

| Internal Cmdlet | 描述 |
|:---|:---|
|`Get-AutomationCertificate`|取得要在 Runbook 或 DSC 組態中使用的憑證。 傳回 [System.Security.Cryptography.X509Certificates.X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) 物件。|

> [!NOTE] 
> 在 runbook 或 DSC 設定中， `Name`您應該避免在的參數中使用變數。 `Get-AutomationCertificate` 這類變數可能會在設計階段將 runbook 或 DSC 設定與自動化變數之間的相依性探索變得複雜。

## <a name="python-2-functions-to-access-certificates"></a>用於存取憑證的 Python 2 功能

使用下表中的函式來存取 Python 2 runbook 中的憑證。

| 函式 | 說明 |
|:---|:---|
| `automationassets.get_automation_certificate` | 擷取憑證資產的相關資訊。 |

> [!NOTE]
> 您必須在 Python `automationassets` runbook 的開頭匯入模組，才能存取資產功能。

## <a name="create-a-new-certificate"></a>建立新的憑證

當您建立新的憑證時，您會將 .cer 或 .pfx 檔案上傳至自動化。 如果您將憑證標示為可匯出，則您可以將它從「自動化」憑證存放區傳出。 如果無法匯出，則它只能用於在 runbook 或 DSC 設定中進行簽署。 自動化要求憑證必須有提供者**Microsoft 增強的 RSA 和 AES 密碼編譯提供者**。

### <a name="create-a-new-certificate-with-the-azure-portal"></a>使用 Azure 入口網站建立新的憑證

1. 從您的自動化帳戶中，選取 [**資產** > ] [**憑證** > ] [**新增憑證**]。
1. 在 [**名稱**] 欄位中，輸入憑證的名稱。
1. 若要流覽 **.cer**或 **.pfx**檔案，請在 [**上傳憑證**檔案] 下，選擇 [**選取**檔案]。 如果您選取 **.pfx**檔案，請指定密碼，並指出是否可以匯出。
1. 選取 [**建立**] 以儲存新的憑證資產。

### <a name="create-a-new-certificate-with-powershell"></a>使用 PowerShell 建立新的憑證

下列範例示範如何建立新的自動化憑證，並將其標示為可匯出。 這個範例會匯入現有的 **.pfx**檔案。

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-a-resource-manager-template"></a>建立具有 Resource Manager 範本的新憑證

下列範例示範如何透過 PowerShell 使用 Resource Manager 範本，將憑證部署至您的自動化帳戶：

```powershell-interactive
$AutomationAccountName = "<automation account name>"
$PfxCertPath = '<PFX cert path'
$CertificatePassword = '<password>'
$certificateName = '<certificate name>'
$AutomationAccountName = '<automation account name>'
$flags = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::PersistKeySet `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::MachineKeySet
# Load the certificate into memory
$PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPath, $CertificatePassword, $flags)
# Export the certificate and convert into base 64 string
$Base64Value = [System.Convert]::ToBase64String($PfxCert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12))
$Thumbprint = $PfxCert.Thumbprint


$json = @"
{
    '`$schema': 'https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#',
    'contentVersion': '1.0.0.0',
    'resources': [
        {
            'name': '$AutomationAccountName/$certificateName',
            'type': 'Microsoft.Automation/automationAccounts/certificates',
            'apiVersion': '2015-10-31',
            'properties': {
                'base64Value': '$Base64Value',
                'thumbprint': '$Thumbprint',
                'isExportable': true
            }
        }
    ]
}
"@

$json | out-file .\template.json
New-AzResourceGroupDeployment -Name NewCert -ResourceGroupName TestAzureAuto -TemplateFile .\template.json
```

## <a name="get-a-certificate"></a>取得憑證

若要取得憑證，請使用內部`Get-AutomationCertificate` Cmdlet。 您不能使用[AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) Cmdlet，因為它會傳回憑證資產的相關資訊，但不會傳回憑證本身。

### <a name="textual-runbook-example"></a>文字式 runbook 範例

下列範例顯示如何將憑證新增至 runbook 中的雲端服務。 在此範例中，密碼是擷取自加密的自動化變數。

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-example"></a>圖形化 runbook 範例

在 [程式庫] 窗格`Get-AutomationCertificate`中的憑證上按一下滑鼠右鍵，然後選取 [**新增至畫布**]，即可將內部 Cmdlet 的活動新增至圖形化 runbook。

![將憑證新增至畫布的螢幕擷取畫面](../media/certificates/automation-certificate-add-to-canvas.png)

下圖顯示在圖形化 Runbook 中使用憑證的範例。 

![圖形化撰寫範例的螢幕擷取畫面](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python-2-example"></a>Python 2 範例

下列範例說明如何存取 Python 2 runbook 中的憑證。

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>後續步驟

* 若要深入瞭解用來存取憑證的 Cmdlet，請參閱[管理 Azure 自動化中的模組](modules.md)。
* 如需 runbook 的一般資訊，請參閱[Azure 自動化中的 runbook 執行](../automation-runbook-execution.md)。
* 如需 DSC 設定的詳細資訊，請參閱[狀態設定總覽](../automation-dsc-overview.md)。 

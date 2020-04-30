---
title: 管理 Azure 自動化中的憑證
description: 憑證會安全地儲存在 Azure 自動化中，讓 runbook 或 DSC 設定可以存取它們，以對 Azure 和協力廠商資源進行驗證。 這篇文章說明憑證的詳細資料，以及如何以文字和圖形化編寫形式加以使用。
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 69bc1a0333365e15452c6d3b253266d37d99b608
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732833"
---
# <a name="manage-certificates-in-azure-automation"></a>管理 Azure 自動化中的憑證

憑證會安全地儲存在 Azure 自動化中，以供 runbook 或 DSC 設定使用 Azure Resource Manager 資源的[AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0)活動來存取。 安全憑證儲存體可讓您建立使用憑證進行驗證的 runbook 和 DSC 設定，或將它們新增至 Azure 或協力廠商資源。

Azure 自動化中的安全資產包括認證、憑證、連接和加密的變數。 這些資產都會經過加密，並使用為每個自動化帳戶產生的唯一金鑰儲存在 Azure 自動化中。 這個金鑰會儲存在系統管理的 Key Vault 中。 在儲存安全資產之前，系統會從 Key Vault 載入金鑰，然後用來加密資產。 此程序由 Azure 自動化所管理。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需有關混合式 Runbook 背景工作角色的 Az 模組安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](../automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="az-powershell-cmdlets"></a>Az PowerShell Cmdlet

針對 Az，下表中的 Cmdlet 是用來透過 Windows PowerShell 建立和管理自動化認證資產。 它們會隨附于[Az. automation 模組](/powershell/azure/overview)中，可在自動化 RUNBOOK 和 DSC 設定中使用。

|Cmdlet |描述|
| --- | ---|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|上傳指定雲端服務的服務憑證。|
|[AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0)|擷取要在 Runbook 或 DSC 組態中使用的憑證相關資訊。 您只能使用`Get-AutomationCertificate`活動來捕獲憑證本身。|
|[新增-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.7.0)|在 Azure 自動化中建立新的憑證。|
|[移除-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationCertificate?view=azps-3.7.0)|從 Azure 自動化中移除憑證。|
|[設定-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationCertificate?view=azps-3.7.0)|設定現有憑證的屬性，包括上傳憑證檔案和設定 **.pfx**檔案的密碼。|

## <a name="activities"></a>活動

下表中的活動是用來存取 Runbook 和 DSC 設定中的憑證。

| 活動 | 描述 |
|:---|:---|
|`Get-AutomationCertificate`|取得要在 Runbook 或 DSC 組態中使用的憑證。 傳回 [System.Security.Cryptography.X509Certificates.X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) 物件。|

> [!NOTE] 
> 在 runbook 或 DSC 設定中， `Name`您應該避免在的參數中使用變數。 `Get-AutomationCertificate` 在此參數中使用變數，會使 runbook 或 DSC 設定與自動化變數之間的相依性在設計階段變得複雜。

## <a name="python-2-functions"></a>Python 2 函式

下表中的函數可用來存取 Python 2 runbook 中的憑證。

| 函式 | 描述 |
|:---|:---|
| `automationassets.get_automation_certificate` | 擷取憑證資產的相關資訊。 |

> [!NOTE]
> 您必須在 Python `automationassets` runbook 的開頭匯入模組，才能存取資產功能。

## <a name="creating-a-new-certificate"></a>建立新憑證

建立新憑證時，您會將 cer 或 pfx 檔案上傳到 Azure 自動化。 如果您將憑證標示為可匯出，那麼您可以將它傳送到 Azure 自動化憑證存放區外部。 如果無法匯出，則它只能用於在 runbook 或 DSC 設定中進行簽署。 Azure 自動化要求憑證必須有提供者**Microsoft 增強的 RSA 和 AES 密碼編譯提供者**。

### <a name="create-a-new-certificate-with-the-azure-portal"></a>使用 Azure 入口網站建立新的憑證

1. 從您的自動化帳戶，按一下 [**資產**] 以開啟 [資產] 頁面。
2. 選取 [**憑證**] 以開啟 [憑證] 頁面。
3. 按一下頁面頂端的 [**新增憑證**]。
4. 在 [**名稱**] 欄位中輸入憑證的名稱。
5. 若要流覽 **.cer**或 **.pfx**檔案，請按一下 **[上傳憑證**檔案] 底下**的 [選取**檔案]。 如果您選取 **.pfx**檔案，請指定密碼，並指出是否可以匯出。
6. 按一下 [ **建立** ] 以儲存新的憑證資產。

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

## <a name="using-a-certificate"></a>使用憑證

若要使用憑證，請使用`Get-AutomationCertificate`活動。 您不能使用[AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) Cmdlet，因為它會傳回憑證資產的相關資訊，但不會傳回憑證本身。

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

在 [ `Get-AutomationCertificate`程式庫] 窗格中的憑證上按一下滑鼠右鍵，然後選取 [**新增至畫布**]，即可將活動新增至圖形化 runbook。

![將憑證新增至畫布](../media/certificates/automation-certificate-add-to-canvas.png)

下圖顯示在圖形化 Runbook 中使用憑證的範例。 這與上述範例相同，說明如何從文字 runbook 將憑證新增至雲端服務。

![範例圖形化編寫](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python-2-example"></a>Python 2 範例

下列範例說明如何存取 Python2 runbook 中的憑證。

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>後續步驟

- 若要深入瞭解如何使用連結來控制 runbook 執行之活動的邏輯流程，請參閱[圖形化編寫中的連結](../automation-graphical-authoring-intro.md#links-and-workflow)。 

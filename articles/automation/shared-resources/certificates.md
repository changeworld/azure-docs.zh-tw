---
title: 管理 Azure 自動化中的憑證
description: 本文說明如何處理憑證以供 Runbook 和 DSC 設定存取。
services: automation
ms.subservice: shared-capabilities
ms.date: 09/10/2020
ms.topic: conceptual
ms.openlocfilehash: b6220cfb5649995e54338f245b4cb62511b89a2c
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "90004692"
---
# <a name="manage-certificates-in-azure-automation"></a>管理 Azure 自動化中的憑證

Azure 自動化使用 Azure Resource Manager 資源的 [Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate) Cmdlet，安全地儲存憑證以供 Runbook 和 DSC 設定存取。 安全憑證存放區可供建立使用憑證進行驗證的 Runbook 和 DSC 設定，或將其新增至 Azure 或協力廠商資源。

>[!NOTE]
>Azure 自動化中的安全資產包括認證、憑證、連接和加密的變數。 這些資產都會使用為每個自動化帳戶產生的唯一金鑰來進行加密並儲存在自動化中。 自動化會將金鑰儲存在系統管理的 Key Vault 服務中。 在儲存安全資產之前，自動化會從 Key Vault 載入金鑰，然後將其用來加密資產。 

## <a name="powershell-cmdlets-to-access-certificates"></a>用來存取憑證的 PowerShell Cmdlet

下表中的 Cmdlet 會使用 PowerShell 來建立及管理自動化憑證。 其會隨附於 [Az 模組](modules.md#az-modules)。

|Cmdlet |描述|
| --- | ---|
|[Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate)|擷取要在 Runbook 或 DSC 組態中使用的憑證相關資訊。 您只能使用內部 `Get-AutomationCertificate` Cmdlet 來擷取憑證本身。|
|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)|在自動化中建立新的憑證。|
|[Remove-AzAutomationCertificate](/powershell/module/Az.Automation/Remove-AzAutomationCertificate)|從自動化中移除憑證。|
|[Set-AzAutomationCertificate](/powershell/module/Az.Automation/Set-AzAutomationCertificate)|設定現有憑證的屬性，包括上傳憑證檔案和設定 **.pfx** 檔案的密碼。|

您也可以使用 [Add-AzureCertificate](/powershell/module/servicemanagement/azure.service/add-azurecertificate) Cmdlet 來上傳指定雲端服務的服務憑證。

## <a name="internal-cmdlets-to-access-certificates"></a>用來存取憑證的內部 Cmdlet

下表中內部 Cmdlet 可用來存取 Runbook 中的憑證。 此 Cmdlet 隨附於全域模組 `Orchestrator.AssetManagement.Cmdlets`。 如需詳細資訊，請參閱[內部 Cmdlet](modules.md#internal-cmdlets)。

| 內部 Cmdlet | 描述 |
|:---|:---|
|`Get-AutomationCertificate`|取得要在 Runbook 或 DSC 組態中使用的憑證。 傳回 [System.Security.Cryptography.X509Certificates.X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) 物件。|

> [!NOTE] 
> 您應該避免在 Runbook 或 DSC 設定中 `Get-AutomationCertificate` 的 `Name` 參數內使用變數。 這類變數可能會在設計階段將 Runbook 或 DSC 設定與自動化變數之間的相依性探索複雜化。

## <a name="python-2-functions-to-access-certificates"></a>用來存取憑證的 Python 2 函式

使用下表中函式來存取 Python 2 Runbook 中的憑證。

| 函式 | 描述 |
|:---|:---|
| `automationassets.get_automation_certificate` | 擷取憑證資產的相關資訊。 |

> [!NOTE]
> 您必須在 Python Runbook 的開頭匯入 `automationassets` 模組，才能存取資產函式。

## <a name="create-a-new-certificate"></a>建立新的憑證

當建立新的憑證時，您會將 .cer 或 .pfx 檔案上傳到自動化。 如果您將憑證標示為可匯出，則可將其傳送到自動化憑證存放區外部。 如果不可匯出，則只能將其用於 Runbook 或 DSC 設定內的簽署。 自動化要求憑證具有提供者：**Microsoft 增強的 RSA 與 AES 密碼編譯提供者**。

### <a name="create-a-new-certificate-with-the-azure-portal"></a>使用 Azure 入口網站建立新的憑證

1. 從您的自動化帳戶，在左窗格中選取 [**共用資源**] 底下的 [**憑證**]。
1. 在 [ **憑證** ] 頁面上，選取 [ **新增憑證**]。
1. 在 [名稱] 欄位中，鍵入憑證的名稱。
1. 若要瀏覽 **.cer** 或 **.pfx** 檔案，請在 [上傳憑證檔案] 下，選擇 [選取檔案]。 如果您選取 **.pfx** 檔案，請指定密碼並指出其是否可匯出。
1. 選取 [建立] 以儲存新的憑證資產。

### <a name="create-a-new-certificate-with-powershell"></a>使用 PowerShell 建立新的憑證

下列範例示範如何建立新的自動化憑證，並將其標示為可匯出。 此範例會匯入現有的 **.pfx** 檔案。

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-a-resource-manager-template"></a>使用 Resource Manager 範本建立新的憑證

下列範例示範如何使用 Resource Manager 範本，以透過 PowerShell 將憑證部署到自動化帳戶：

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

若要擷取憑證，請使用內部 `Get-AutomationCertificate` Cmdlet。 您無法使用 [Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate) Cmdlet，因為這會傳回憑證資產的資訊，而不是憑證本身。

### <a name="textual-runbook-example"></a>文字式 Runbook 範例

下列範例示範如何將憑證新增至 Runbook 中的雲端服務。 在此範例中，密碼是擷取自加密的自動化變數。

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-example"></a>圖形化 Runbook 範例

將一個代表內部 `Get-AutomationCertificate` Cmdlet 的活動新增至圖形化 Runbook，方法是以滑鼠右鍵按一下 [程式庫] 窗格中的 [憑證]，然後選取 [新增至畫布]。

![將憑證新增至畫布的螢幕擷取畫面](../media/certificates/automation-certificate-add-to-canvas.png)

下圖顯示在圖形化 Runbook 中使用憑證的範例。

![圖形化編寫範例的螢幕擷取畫面](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python-2-example"></a>Python 2 範例

下列範例示範如何存取 Python 2 Runbook 中的憑證。

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>後續步驟

* 若要深入了解用來存取憑證的 Cmdlet，請參閱[管理 Azure 自動化中的模組](modules.md)。
* 如需 Runbook 的一般資訊，請參閱 [Azure 自動化中的 Runbook 執行](../automation-runbook-execution.md)。
* 如需 DSC 設定的詳細資料，請參閱 [Azure 自動化狀態設定概觀](../automation-dsc-overview.md)。
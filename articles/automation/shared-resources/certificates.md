---
title: 在 Azure 自動化中管理憑證
description: 憑證安全地存儲在 Azure 自動化中,以便 Runbook 或 DSC 配置可以訪問它們,以便針對 Azure 和第三方資源進行身份驗證。 這篇文章說明憑證的詳細資料，以及如何以文字和圖形化編寫形式加以使用。
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 69bc1a0333365e15452c6d3b253266d37d99b608
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732833"
---
# <a name="manage-certificates-in-azure-automation"></a>在 Azure 自動化中管理憑證

憑證安全地儲存在 Azure 自動化中,因此可以使用 Azure 資源管理器資源的[獲取-Az自動化證書](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0)活動通過 Runbook 或 DSC 配置存取證書。 安全憑證儲存允許您建立使用證書進行身份驗證或將其添加到 Azure 或第三方資源的 Runbook 和 DSC 配置。

Azure 自動化中的安全資產包括認證、憑證、連接和加密的變數。 這些資產都會經過加密，並使用為每個自動化帳戶產生的唯一金鑰儲存在 Azure 自動化中。 此密鑰存儲在系統管理的關鍵保管庫中。 在儲存安全資產之前，系統會從 Key Vault 載入金鑰，然後用來加密資產。 此程序由 Azure 自動化所管理。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有關混合 Runbook 輔助角色上的 Az 模組安裝說明,請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 對於自動化帳戶,可以使用[「如何更新 Azure 自動化 中的 Azure PowerShell」模組](../automation-update-azure-modules.md)將模組更新到最新版本。

## <a name="az-powershell-cmdlets"></a>阿茲電源外殼 cmdlet

對於 Az,下表中的 cmdlet 用於使用 Windows PowerShell 創建和管理自動化認證資產。 它們作為[Az.自動化模組](/powershell/azure/overview)的一部分發貨,可用於自動化手冊和 DSC 配置。

|Cmdlet |描述|
| --- | ---|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|上傳指定雲端服務的服務憑證。|
|[取得阿茲自動化憑證](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0)|擷取要在 Runbook 或 DSC 組態中使用的憑證相關資訊。 只能使用`Get-AutomationCertificate`活動檢索證書本身。|
|[新阿茲自動化證書](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.7.0)|在 Azure 自動化中創建新證書。|
|[刪除-阿茲自動化證書](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationCertificate?view=azps-3.7.0)|從 Azure 自動化中移除憑證。|
|[集-阿茲自動化證書](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationCertificate?view=azps-3.7.0)|設置現有證書的屬性,包括上載證書檔和設置 **.pfx**文件的密碼。|

## <a name="activities"></a>活動

下表中的活動是用來存取 Runbook 和 DSC 設定中的憑證。

| 活動 | 描述 |
|:---|:---|
|`Get-AutomationCertificate`|取得要在 Runbook 或 DSC 組態中使用的憑證。 傳回 [System.Security.Cryptography.X509Certificates.X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) 物件。|

> [!NOTE] 
> 應避免在`Name`Runbook 或`Get-AutomationCertificate`DSC 配置中使用 參數中的變數。 在此參數中使用變數會使在設計時 Runbook 或 DSC 配置和自動化變數之間的依賴項發現複雜化。

## <a name="python-2-functions"></a>Python 2 函數

下表中的函數用於造訪 Python 2 執行簿中的憑證。

| 函式 | 描述 |
|:---|:---|
| `automationassets.get_automation_certificate` | 擷取憑證資產的相關資訊。 |

> [!NOTE]
> 您必須在`automationassets`Python Runbook 的開頭導入模組才能存取資產函數。

## <a name="creating-a-new-certificate"></a>建立新憑證

建立新憑證時，您會將 cer 或 pfx 檔案上傳到 Azure 自動化。 如果您將憑證標示為可匯出，那麼您可以將它傳送到 Azure 自動化憑證存放區外部。 如果不可匯出,則它只能用於在 Runbook 或 DSC 配置中簽名。 Azure 自動化要求憑證具有提供程式**Microsoft 增強 RSA 和 AES 加密提供者**。

### <a name="create-a-new-certificate-with-the-azure-portal"></a>使用 Azure 門戶建立新憑證

1. 從自動化帳戶中,單擊 **「資產**」以打開「資產」頁。
2. 選擇 **『證書**』以開啟「證書」 頁。
3. 按下**在頁面端加入憑證**。
4. 在 **「名稱」** 欄位中鍵入憑證的名稱。
5. 要瀏覽 **.cer**或 **.pfx**檔,請按下 **「 上傳憑證檔**」**的檔案**。 如果選擇 **.pfx**檔,請指定密碼並指示是否可以匯出密碼。
6. 按一下 [ **建立** ] 以儲存新的憑證資產。

### <a name="create-a-new-certificate-with-powershell"></a>使用 PowerShell 建立新憑證

下列範例示範如何建立新的自動化憑證，並將其標示為可匯出。 本示例導入現有的 **.pfx**檔。

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-a-resource-manager-template"></a>使用資源管理員樣本建立新憑證

以下範例展示如何透過 PowerShell 使用資源管理員樣本將憑證部署到自動化帳戶:

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

要使用憑證,請使用作用`Get-AutomationCertificate`。 不能使用[Get-AzAutomation 證書](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0)cmdlet,因為它返回有關證書資產的資訊,但不能返回證書本身。

### <a name="textual-runbook-example"></a>文字執行簿範例

下面的範例展示如何在 Runbook 中向雲端服務添加證書。 在此範例中，密碼是擷取自加密的自動化變數。

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-example"></a>圖像圖形執行簿範例

以右`Get-AutomationCertificate`鍵按下「庫」窗格中的憑證並選擇「**新增到畫布**」,將活動添加到圖形 Runbook。

![將憑證新增至畫布](../media/certificates/automation-certificate-add-to-canvas.png)

下圖顯示在圖形化 Runbook 中使用憑證的範例。 這與前面的示例相同,該示例演示如何從文本 Runbook 向雲服務添加證書。

![範例圖形化編寫](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python-2-example"></a>Python 2 範例

下面的範例展示如何存取 Python2 Runbook 中的憑證。

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>後續步驟

- 要瞭解有關使用連結來控制 Runbook 執行的活動的邏輯串流的更多內容,請參考[圖形創作中的連結](../automation-graphical-authoring-intro.md#links-and-workflow)。 

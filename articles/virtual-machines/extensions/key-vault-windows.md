---
title: 適用于 Windows 的 Azure Key Vault VM 擴充功能
description: 在虛擬機器上，使用虛擬機器擴充功能部署執行自動重新整理金鑰保存庫祕密的代理程式。
services: virtual-machines-windows
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines-windows
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: c41207bedbea96c76f0a9a6ffd45ea6efa09138b
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88641767"
---
# <a name="key-vault-virtual-machine-extension-for-windows"></a>適用於 Windows 的金鑰保存庫虛擬機器擴充功能

金鑰保存庫 VM 擴充功能可自動重新整理儲存在 Azure 金鑰保存庫中的憑證。 具體來說，此擴充功能會監視儲存在金鑰保存庫中觀察之憑證的清單，並在偵測到變更之後，擷取並安裝對應的憑證。 本文件詳述適用於 Windows 的金鑰保存庫 VM 擴充功能所支援的平台、組態和部署選項。 

### <a name="operating-system"></a>作業系統

Key Vault VM 擴充功能支援下列 Windows 版本：

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012

### <a name="supported-certificate-content-types"></a>支援的憑證內容類型

- PKCS #12
- PEM

## <a name="extension-schema"></a>擴充功能結構描述

下列 JSON 顯示金鑰保存庫 VM 擴充功能的結構描述。 延伸模組不需要受保護的設定，所有的設定都會被視為公用資訊。 延伸模組需要受監視的憑證、輪詢頻率和目的地憑證存放區的清單。 具體來說：  

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "linkOnRenewal": <Only Windows. This feature enables auto-rotation of SSL certificates, without necessitating a re-deployment or binding.  e.g.: false>,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        },
        "authenticationSettings": {
                "msiEndpoint":  <Optional MSI endpoint e.g.: "http://169.254.169.254/metadata/identity">,
                "msiClientId":  <Optional MSI identity e.g.: "c7373ae5-91c2-4165-8ab6-7381d6e75619">
        }
       }
      }
    }
```

> [!NOTE]
> 您觀察到的憑證 URL 格式應該是 `https://myVaultName.vault.azure.net/secrets/myCertName`。
> 
> 這是因為 `/secrets` 路徑會傳回完整憑證，私密金鑰也包括在內，`/certificates` 路徑則不會。 可以在這裡找到憑證的詳細資訊：[Key Vault 憑證](../../key-vault/general/about-keys-secrets-certificates.md)

> [!IMPORTANT]
> 只有具有**使用者指派**身分識別的 vm 才**需要**' authenticationsettings.instance.setsecretkey ' 屬性。
> 它會指定要用於驗證 Key Vault 的身分識別。


### <a name="property-values"></a>屬性值

| 名稱 | 值 / 範例 | 資料類型 |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| publisher | Microsoft.Azure.KeyVault | 字串 |
| type | KeyVaultForWindows | 字串 |
| typeHandlerVersion | 1.0 | int |
| pollingIntervalInS | 3600 | 字串 |
| certificateStoreName | MY | 字串 |
| linkOnRenewal | false | boolean |
| certificateStoreLocation  | LocalMachine 或 CurrentUser (區分大小寫)  | 字串 |
| requiredInitialSync | true | boolean |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate"] | 字串陣列
| msiEndpoint | http://169.254.169.254/metadata/identity | 字串 |
| msiClientId | c7373ae5-91c2-4165-8ab6-7381d6e75619 | 字串 |


## <a name="template-deployment"></a>範本部署

也可以使用 Azure Resource Manager 範本部署 Azure VM 擴充功能。 部署一或多部需要部署後重新整理憑證的虛擬機器時，很適合使用範本。 此擴充功能可以部署到個別的 VM 或虛擬機器擴展集。 結構描述與組態對於這兩種範本類型都是通用的。 

虛擬機器擴充功能的 JSON 組態必須在內嵌在範本的虛擬機器資源片段，具體來說，就是虛擬機器範本的 `"resources": []` 物件，而若是虛擬機器擴展集，則會在 `"virtualMachineProfile":"extensionProfile":{"extensions" :[]` 物件下。

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KeyVaultForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        }      
      }
      }
    }
```


## <a name="azure-powershell-deployment"></a>Azure PowerShell 部署

Azure PowerShell 可以用來將金鑰保存庫 VM 擴充功能部署到現有的虛擬機器或虛擬機器擴展集。 

* 若要在 VM 上部署擴充功能：
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName =  "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
       
    
        # Start the deployment
        Set-AzVmExtension -TypeHandlerVersion "1.0" -ResourceGroupName <ResourceGroupName> -Location <Location> -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* 若要在虛擬機器擴展集上部署擴充功能：

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
        
        # Add Extension to VMSS
        $vmss = Get-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName>
        Add-AzVmssExtension -VirtualMachineScaleSet $vmss  -Name $extName -Publisher $extPublisher -Type $extType -TypeHandlerVersion "1.0" -Setting $settings

        # Start the deployment
        Update-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName> -VirtualMachineScaleSet $vmss 
    
    ```

## <a name="azure-cli-deployment"></a>Azure CLI 部署

Azure CLI 可以用來將金鑰保存庫 VM 擴充功能部署到現有的虛擬機器或虛擬機器擴展集。 
 
* 若要在 VM 上部署擴充功能：
    
    ```azurecli
       # Start the deployment
         az vm extension set -n "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

* 若要在虛擬機器擴展集上部署擴充功能：

   ```azurecli
        # Start the deployment
        az vmss extension set -n "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vmss-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

請留意下列限制/需求：
- 金鑰保存庫限制：
  - 部署時必須存在 
  - Key Vault 使用 MSI 為 VM/VMSS 身分識別設定的存取原則


## <a name="troubleshoot-and-support"></a>疑難排解與支援

### <a name="troubleshoot"></a>疑難排解

使用 Azure PowerShell，就可以從 Azure 入口網站擷取有關擴充功能部署狀態的資料。 若要查看所指定 VM 的擴充功能部署狀態，請使用 Azure PowerShell 執行下列命令。

## <a name="azure-powershell"></a>Azure PowerShell
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

## <a name="azure-cli"></a>Azure CLI
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```

擴充功能執行輸出會記錄至下列檔案︰

```
%windrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.KeyVault.KeyVaultForWindows\<version>\akvvm_service_<date>.log
```


### <a name="support"></a>支援

如果您在本文中有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 Azure 專家。 或者，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。 如需使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。

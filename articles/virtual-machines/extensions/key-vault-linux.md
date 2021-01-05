---
title: 適用於 Linux 的 Azure Key Vault VM 擴充功能
description: 在虛擬機器上，使用虛擬機器擴充功能部署執行自動重新整理金鑰保存庫憑證的代理程式。
services: virtual-machines-linux
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 0558513d88eb5ffb03484e9d3bd8e37b2c9a0dcf
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97895014"
---
# <a name="key-vault-virtual-machine-extension-for-linux"></a>適用於 Linux 的金鑰保存庫虛擬機器擴充功能

金鑰保存庫 VM 擴充功能可自動重新整理儲存在 Azure 金鑰保存庫中的憑證。 具體而言，此擴充功能會監視儲存於金鑰保存庫的觀察憑證清單。  在偵測到變更時，擴充功能會擷取並安裝對應的憑證。 Microsoft 目前會在 Linux VM 上發行金鑰保存庫 VM 擴充功能並提供支援。 本文件詳述適用於 Linux 的金鑰保存庫 VM 擴充功能所支援的平台、組態和部署選項。 

### <a name="operating-system"></a>作業系統

Key Vault VM 擴充功能支援下列 Linux 發行版本：

- Ubuntu-1604
- Ubuntu-1804
- Debian-9
- Suse-15 

### <a name="supported-certificate-content-types"></a>支援的憑證內容類型

- PKCS #12
- PEM

## <a name="prerequisities"></a>必要條件
  - 具有憑證的 Key Vault 實例。 請參閱 [建立 Key Vault](../../key-vault/general/quick-create-portal.md)
  - VM/VMSS 必須已指派 [受控識別](../../active-directory/managed-identities-azure-resources/overview.md)
  - Key Vault 存取原則必須設定為 `get` `list` VM/VMSS 受控識別的秘密和許可權，才能抓取秘密的憑證部分。 瞭解 [如何驗證 Key Vault](../../key-vault/general/authentication.md) 並 [指派 Key Vault 存取原則](../../key-vault/general/assign-access-policy-cli.md)。
  -  VMSS 應該具有下列身分識別設定： ` 
  "identity": {
  "type": "UserAssigned",
  "userAssignedIdentities": {
  "[parameters('userAssignedIdentityResourceId')]": {}
  }
  }
  `
  
 - AKV 延伸模組應該具有此設定： `
                 "authenticationSettings": {
                    "msiEndpoint": "[parameters('userAssignedIdentityEndpoint')]",
                    "msiClientId": "[reference(parameters('userAssignedIdentityResourceId'), variables('msiApiVersion')).clientId]"
                  }
   `

## <a name="extension-schema"></a>擴充功能結構描述

下列 JSON 顯示金鑰保存庫 VM 擴充功能的結構描述。 此擴充功能不需要受保護的設定，其所有設定都會被視為沒有安全性影響的資訊。 此擴充功能需要有受監視的祕密、輪詢頻率和目的地憑證存放區的清單。 具體來說：  
```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForLinux",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <It is ignored on Linux>,
          "linkOnRenewal": <Not available on Linux e.g.: false>,
          "certificateStoreLocation": <disk path where certificate is stored, default: "/var/lib/waagent/Microsoft.Azure.KeyVault">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: ["https://myvault.vault.azure.net/secrets/mycertificate", "https://myvault.vault.azure.net/secrets/mycertificate2"]>
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
> 只有具有 **使用者指派** 身分識別的 vm 才 **需要**' authenticationsettings.instance.setsecretkey ' 屬性。
> 它會指定要用於驗證 Key Vault 的身分識別。


### <a name="property-values"></a>屬性值

| 名稱 | 值 / 範例 | 資料類型 |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| publisher | Microsoft.Azure.KeyVault | 字串 |
| type | KeyVaultForLinux | 字串 |
| typeHandlerVersion | 1.0 | int |
| pollingIntervalInS | 3600 | 字串 |
| certificateStoreName | 它會在 Linux 上忽略 | 字串 |
| linkOnRenewal | false | boolean |
| certificateStoreLocation  | /var/lib/waagent/Microsoft.Azure.KeyVault | 字串 |
| requireInitialSync | true | boolean |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate", "https://myvault.vault.azure.net/secrets/mycertificate2"] | 字串陣列
| msiEndpoint | http://169.254.169.254/metadata/identity | 字串 |
| msiClientId | c7373ae5-91c2-4165-8ab6-7381d6e75619 | 字串 |


## <a name="template-deployment"></a>範本部署

也可以使用 Azure Resource Manager 範本部署 Azure VM 擴充功能。 部署一或多部需要部署後重新整理憑證的虛擬機器時，很適合使用範本。 此擴充功能可以部署到個別的 VM 或虛擬機器擴展集。 結構描述與組態對於這兩種範本類型都是通用的。 

虛擬機器擴充功能的 JSON 組態必須在內嵌在範本的虛擬機器資源片段，具體來說，就是虛擬機器範本的 `"resources": []` 物件，而若是虛擬機器擴展集，則會在 `"virtualMachineProfile":"extensionProfile":{"extensions" :[]` 物件下。

 > [!NOTE]
> VM 延伸模組需要指派系統或使用者受控識別，才能向金鑰保存庫進行驗證。  瞭解 [如何驗證 Key Vault 並指派 Key Vault 存取原則。](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
> 

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KeyVaultForLinux",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
          "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <ingnored on linux>,
          "certificateStoreLocation": <disk path where certificate is stored, default: "/var/lib/waagent/Microsoft.Azure.KeyVault">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        }      
      }
      }
    }
```

### <a name="extension-dependency-ordering"></a>擴充功能相依性順序
如果已設定，Key Vault VM 擴充功能支援延伸模組排序。 根據預設，擴充功能會報告它已在開始輪詢後立即成功開始。 不過，您可以將它設定為等到成功下載完整的憑證清單之後，才能回報成功開始。 如果其他延伸模組相依于在啟動前安裝一組完整的憑證，則啟用此設定可讓這些擴充功能宣告 Key Vault 擴充功能的相依性。 這會讓這些擴充功能無法啟動，直到所有相依的憑證都已安裝為止。 延伸模組將會無限期地重試初始下載並保持 `Transitioning` 狀態。

若要開啟此功能，請設定下列各項：
```
"secretsManagementSettings": {
    "requireInitialSync": true,
    ...
}
```
> 記使用這項功能與建立系統指派的身分識別並以該身分識別更新 Key Vault 存取原則的 ARM 範本不相容。 這麼做會導致鎖死，因為保存庫存取原則必須等到所有延伸模組都啟動之後才會更新。 您應改為使用 *單一使用者指派的 MSI 身分識別* ，並在部署之前先使用該身分識別來預先 ACL 您的保存庫。

## <a name="azure-powershell-deployment"></a>Azure PowerShell 部署
> [!WARNING]
> PowerShell 用戶端通常 `\` `"` 會在 settings.js中新增，這會導致 akvvm_service 失敗，並出現錯誤： `[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.`

Azure PowerShell 可以用來將金鑰保存庫 VM 擴充功能部署到現有的虛擬機器或虛擬機器擴展集。 

* 若要在 VM 上部署擴充功能：
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCert1> + '","' + <observedCert2> + '"] } }'
        $extName =  "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
       
    
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
        '", "observedCertificates": ["' + <observedCert1> + '","' + <observedCert2> + '"] } }'
        $extName = "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
        
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
         az vm extension set -n "KeyVaultForLinux" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```

* 若要在虛擬機器擴展集上部署擴充功能：

   ```azurecli
        # Start the deployment
        az vmss extension set -n "KeyVaultForLinux" `
        --publisher Microsoft.Azure.KeyVault `
        -g "<resourcegroup>" `
        --vm-name "<vmName>" `
        --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```
請留意下列限制/需求：
- 金鑰保存庫限制：
  - 部署時必須存在 
  - 您必須使用受控識別來設定 VM/VMSS 身分識別的 Key Vault 存取原則。 瞭解 [如何驗證 Key Vault](../../key-vault/general/authentication.md) 並 [指派 Key Vault 存取原則](../../key-vault/general/assign-access-policy-cli.md)。

### <a name="frequently-asked-questions"></a>常見問題集

* 您可以設定的 observedCertificates 數目是否有限制？
  否，Key Vault VM 擴充功能沒有 observedCertificates 數目的限制。


### <a name="troubleshoot"></a>疑難排解

使用 Azure PowerShell，就可以從 Azure 入口網站擷取有關擴充功能部署狀態的資料。 若要查看所指定 VM 的擴充功能部署狀態，請使用 Azure PowerShell 執行下列命令。

**Azure PowerShell**
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

**Azure CLI**
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```
#### <a name="logs-and-configuration"></a>記錄和設定

```
/var/log/waagent.log
/var/log/azure/Microsoft.Azure.KeyVault.KeyVaultForLinux/*
/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-<most recent version>/config/*
```
### <a name="using-symlink"></a>使用符號符號

符號連結或符號連結基本上是先進的快捷方式。 若要避免監視資料夾並自動取得最新的憑證，您可以使用此符號 `([VaultName].[CertificateName])` 來取得 Linux 上的最新憑證版本。

### <a name="frequently-asked-questions"></a>常見問題集

* 您可以設定的 observedCertificates 數目是否有限制？
  否，Key Vault VM 擴充功能沒有 observedCertificates 數目的限制。

### <a name="support"></a>支援

如果您在本文中有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 Azure 專家。 或者，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。 如需使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。

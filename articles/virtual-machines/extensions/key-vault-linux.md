---
title: 適用於 Linux 的 Azure 金鑰保管庫 VM 擴充
description: 使用虛擬機器擴展在虛擬機器上部署執行金鑰保管庫證書自動刷新的代理。
services: virtual-machines-linux
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: add2d515e4f8e8c56a98a7292e137e601332d10c
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410874"
---
# <a name="key-vault-virtual-machine-extension-for-linux"></a>Linux 金鑰保管庫虛擬機器擴展

金鑰保管庫 VM 擴展提供存儲在 Azure 密鑰保管庫中的證書的自動刷新。 具體而言,擴展監視存儲在密鑰保管庫中的觀察證書的清單。  檢測到更改後,擴展將檢索並安裝相應的證書。 密鑰保管庫 VM 擴展由 Microsoft 發佈和支援,目前在 Linux VM 上。 本文件詳細介紹了 Linux 密鑰保管庫 VM 擴展的支援平臺、配置和部署選項。 

### <a name="operating-system"></a>作業系統

金鑰保存庫 VM 延伸支援以下 Linux 發行版:

- 烏本圖-1604
- 烏本圖-1804
- Debian-9
- 蘇塞-15 

### <a name="supported-certificate-content-types"></a>受支援的憑證內容類型

- PKCS #12
- Pem

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
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "linkOnRenewal": <Not available on Linux e.g.: false>,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        }      
      }
      }
    }
```

> [!NOTE]
> 觀察到的憑證網址 應`https://myVaultName.vault.azure.net/secrets/myCertName`為形式 。
> 
> 這是因為`/secrets`路徑返回完整證書(包括私鑰),`/certificates`而路徑不返回。 有關憑證的詳細資訊,請參閱此處:[金鑰保管庫證書](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-certificates)


### <a name="property-values"></a>屬性值

| 名稱 | 值 / 範例 | 資料類型 |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| publisher | Microsoft.Azure.KeyVault | 字串 |
| type | 金鑰庫福Linux | 字串 |
| typeHandlerVersion | 1.0 | int |
| pollingIntervalInS | 3600 | 字串 |
| certificateStoreName | MY | 字串 |
| 連結On更新 | false | boolean |
| certificateStoreLocation  | LocalMachine | 字串 |
| 必要的初始同步 | true | boolean |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate"] | 字串陣列


## <a name="template-deployment"></a>範本部署

也可以使用 Azure Resource Manager 範本部署 Azure VM 擴充功能。 部署一或多部需要部署後重新整理憑證的虛擬機器時，很適合使用範本。 擴展可以部署到單個 VM 或虛擬機器規模集。 結構描述與組態對於這兩種範本類型都是通用的。 

虛擬機器擴展的 JSON 配置必須嵌套在範本的虛擬機器資源片段中`"resources": []`,特別是虛擬機器範本的物件,以及設置物件`"virtualMachineProfile":"extensionProfile":{"extensions" :[]`下的虛擬機器縮放的情況。

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
        $extName =  "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
       
    
        # Start the deployment
        Set-AzVmExtension -TypeHandlerVersion "1.0" -ResourceGroupName <ResourceGroupName> -Location <Location> -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* 要在虛擬機器規模集中部署延伸:

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCerts> + '"] } }'
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

Azure CLI 可用於將密鑰保管庫 VM 擴展部署到現有虛擬機器或虛擬機器規模集。 
 
* 若要在 VM 上部署擴充功能：
    
    ```azurecli
       # Start the deployment
         az vm extension set -n "KeyVaultForLinux" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

* 要在虛擬機器規模集中部署延伸:

   ```azurecli
        # Start the deployment
        az vmss extension set -n "KeyVaultForLinux" `
        --publisher Microsoft.Azure.KeyVault `
        -g "<resourcegroup>" `
        --vm-name "<vmName>" `
        --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

請留意下列限制/需求：
- 金鑰保存庫限制：
  - 部署時必須存在 
  - 使用 MSI 為 VM/VMSS 識別設定金鑰保存庫存取原則


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

### <a name="support"></a>支援

如果本文中的任何一點都需要更多説明,則可以在[MSDN Azure 和堆疊溢出論壇](https://azure.microsoft.com/support/forums/)上聯繫 Azure 專家。 或者，您可以提出 Azure 支援事件。 轉到[Azure 支援網站](https://azure.microsoft.com/support/options/)並選擇「獲取支援」。 如需使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。

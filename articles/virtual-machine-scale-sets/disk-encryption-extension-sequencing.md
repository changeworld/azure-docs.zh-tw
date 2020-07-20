---
title: Azure 磁碟加密和 Azure 虛擬機器擴展集擴充功能排序
description: 本文提供啟用 Linux IaaS VM 適用的 Microsoft Azure 磁碟加密的指示。
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 1c93359486379ecfc8bf6df1f29978ba369f551a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "83117252"
---
# <a name="use-azure-disk-encryption-with-virtual-machine-scale-set-extension-sequencing"></a>使用搭配虛擬機器擴展集擴充功能排序的 Azure 磁碟加密

Azure 磁片加密等擴充功能可以依照指定的順序新增至 Azure 虛擬機器擴展集。 若要這麼做，請使用[擴充功能排序](virtual-machine-scale-sets-extension-sequencing.md)。 

一般來說，加密應該套用到磁片：

- 在準備磁片或磁片區的延伸模組或自訂腳本之後。
- 在延伸模組或自訂腳本之前，可存取或取用加密磁片或磁片區上的資料。

不論是哪一種情況， `provisionAfterExtensions` 屬性都會指定稍後要在序列中新增哪一個延伸模組。

## <a name="sample-azure-templates"></a>範例 Azure 範本

如果您想要在另一個擴充功能之後套用 Azure 磁碟加密，請將 `provisionAfterExtensions` 屬性放在 AzureDiskEncryption 延伸模組區塊中。 

以下是使用 "CustomScriptExtension" 的範例，這是一個 Powershell 腳本，它會初始化並格式化 Windows 磁片，後面接著 "AzureDiskEncryption"：

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "CustomScriptExtension",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Compute",
          "type": "CustomScriptExtension",
          "typeHandlerVersion": "1.9",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "fileUris": [
              "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/ade-vmss/FormatMBRDisk.ps1"
            ]
          },
          "protectedSettings": {
           "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File FormatMBRDisk.ps1"
          }
        }
      },
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "AzureDiskEncryption",
        "location": "[resourceGroup().location]",
        "properties": {
          "provisionAfterExtensions": [
            "CustomScriptExtension"
          ],
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
    ]
  }
}
```

如果您想要在另一個擴充功能之前套用 Azure 磁碟加密，請將 `provisionAfterExtensions` 屬性放在擴充功能的區塊中以遵循。

以下範例使用 "AzureDiskEncryption"，後面接著 "VMDiagnosticsSettings"，這是在 Windows 型 Azure VM 上提供監視和診斷功能的延伸模組：


```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "AzureDiskEncryption",
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
      { 
        "name": "Microsoft.Insights.VMDiagnosticsSettings", 
        "type": "extensions", 
        "location": "[resourceGroup().location]", 
        "apiVersion": "2016-03-30", 
        "dependsOn": [ 
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
        ], 
        "properties": { 
          "provisionAfterExtensions": [
            "AzureDiskEncryption"
          ],
        "publisher": "Microsoft.Azure.Diagnostics", 
          "type": "IaaSDiagnostics", 
          "typeHandlerVersion": "1.5", 
          "autoUpgradeMinorVersion": true, 
          "settings": { 
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
            variables('wadmetricsresourceid'), 
            concat('myVM', copyindex()),
            variables('wadcfgxend')))]", 
            "storageAccount": "[variables('storageName')]" 
          }, 
          "protectedSettings": { 
            "storageAccountName": "[variables('storageName')]", 
            "storageAccountKey": "[listkeys(variables('accountid'), 
              '2015-06-15').key1]", 
            "storageAccountEndPoint": "https://core.windows.net" 
          } 
        } 
      },
    ]
  }
}
```

如需更深入的範本，請參閱：
* 在格式化磁片（Linux）的自訂 shell 腳本之後套用 Azure 磁碟加密延伸模組： [deploy-extseq-linux-ADE-after-customscript.js于](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-linux-ADE-after-customscript.json)


## <a name="next-steps"></a>後續步驟
- 深入瞭解擴充功能排序：[虛擬機器擴展集中的序列延伸](virtual-machine-scale-sets-extension-sequencing.md)模組布建。
- 深入瞭解 `provisionAfterExtensions` 屬性： [Microsoft. 計算 virtualMachineScaleSets/擴充功能範本參考](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)。
- [適用於虛擬機器擴展集的 Azure 磁碟加密](disk-encryption-overview.md)
- [使用 Azure CLI 將虛擬機器擴展集加密](disk-encryption-cli.md)
- [使用 Azure PowerShell 將虛擬機器擴展集加密](disk-encryption-powershell.md)
- [針對 Azure 磁碟加密建立及設定金鑰保存庫](disk-encryption-key-vault.md)

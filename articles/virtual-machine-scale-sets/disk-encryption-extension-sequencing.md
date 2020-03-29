---
title: Azure 磁片加密和 Azure 虛擬機器擴展程式集擴展順序
description: 本文提供啟用 Linux IaaS VM 適用的 Microsoft Azure 磁碟加密的指示。
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 10/10/2019
ms.openlocfilehash: aa638b86b0788b8c274f9dcb3c04c1fc385b4ae1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279018"
---
# <a name="use-azure-disk-encryption-with-virtual-machine-scale-set-extension-sequencing"></a>使用搭配虛擬機器擴展集擴充功能排序的 Azure 磁碟加密

可以按指定順序將 Azure 虛擬機器縮放集（如 Azure 磁片加密）添加到擴展。 為此，請使用[擴展排序](virtual-machine-scale-sets-extension-sequencing.md)。 

通常，加密應用於磁片：

- 在準備磁片或卷的擴展或自訂腳本之後。
- 在訪問或使用加密磁片或卷上的資料的擴展或自訂腳本之前。

在這兩種情況下，`provisionAfterExtensions`屬性指定應在序列中稍後添加哪個擴展。

## <a name="sample-azure-templates"></a>示例 Azure 範本

如果希望在另一個擴展後應用 Azure 磁片加密，則`provisionAfterExtensions`將該屬性放在 AzureDisk加密擴展塊中。 

下面是使用"自訂腳本擴展"的示例，該腳本是初始化和格式化 Windows 磁片的電源外殼腳本，後跟"AzureDisk加密"：

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

如果希望在另一個擴展之前應用 Azure 磁片加密，請`provisionAfterExtensions`將該屬性放在要遵循的擴展塊中。

下面是一個使用"Azure 磁片加密"的示例，然後是"VM診斷設置"，該擴展在基於 Windows 的 Azure VM 上提供監視和診斷功能：


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

有關更深入的範本，請參閱：
* 在設置磁片 （Linux） 格式的自訂 shell 腳本後應用 Azure 磁片加密擴展：[部署-extseq-linux-ADE 自訂腳本後.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-linux-ADE-after-customscript.json)


## <a name="next-steps"></a>後續步驟
- 瞭解有關擴展排序的更多：[虛擬機器縮放集中的序列擴展預配](virtual-machine-scale-sets-extension-sequencing.md)。
- 瞭解有關`provisionAfterExtensions`屬性的更多[：Microsoft.計算虛擬機器規模集/擴展範本引用](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)。
- [適用於虛擬機器擴展集的 Azure 磁碟加密](disk-encryption-overview.md)
- [使用 Azure CLI 將虛擬機器擴展集加密](disk-encryption-cli.md)
- [使用 Azure PowerShell 將虛擬機器擴展集加密](disk-encryption-powershell.md)
- [針對 Azure 磁碟加密建立及設定金鑰保存庫](disk-encryption-key-vault.md)

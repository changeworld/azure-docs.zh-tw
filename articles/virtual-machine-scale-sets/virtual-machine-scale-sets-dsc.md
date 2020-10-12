---
title: 使用 Desired State Configuration 搭配虛擬機器擴展集
description: 使用虛擬機器擴展集搭配 Azure Desired State Configuration 擴充功能來設定虛擬機器。
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 6/25/2020
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 20e5bff87d5cd0d6e0a35a558462bb5598bfe3f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87080483"
---
# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>搭配 Azure DSC 擴充功能使用虛擬機器擴展集
[虛擬機器擴展集](./overview.md)可以搭配 [Azure 期望狀態設定 (DSC)](../virtual-machines/extensions/dsc-overview.md?toc=/azure/virtual-machines/windows/toc.json) 擴充功能處理常式來使用。 虛擬機器擴展集提供一個部署及管理大量虛擬機器的方式，可以因應負載情況來彈性地相應縮小和放大。 當 VM 上線時，可使用 DSC 來設定它們，因為它們正在執行生產環境的軟體。

## <a name="differences-between-deploying-to-virtual-machines-and-virtual-machine-scale-sets"></a>部署至虛擬機器與部署至虛擬機器擴展集之間的差異
適用於虛擬機器擴展集的基礎範本結構與單一 VM 有些微不同。 具體來說，單一 VM 會在 "virtualMachines" 節點下部署擴充功能。 有一個類型為 "extensions" 且已將 DSC 加入範本的項目

```
"resources": [
          {
              "name": "Microsoft.Powershell.DSC",
              "type": "extensions",
              "location": "[resourceGroup().location]",
              "apiVersion": "2015-06-15",
              "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "tags": {
                  "displayName": "dscExtension"
              },
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": false,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "DscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }
              }
          }
      ]
```

虛擬機器擴展集節點具有 "properties" 區段以及 "VirtualMachineProfile"、"extensionProfile" 屬性。 已將 DSC 加入至 "extensions" 下方

```
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": false,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
```

## <a name="behavior-for-a-virtual-machine-scale-set"></a>虛擬機器擴展集的行為
虛擬機器擴展集的行為與單一 VM 的行為完全相同。 建立新的 VM 時，會使用 DSC 擴充功能自動進行佈建。 如果擴充功能需要較新版本的 WMF，VM 會在上線之前重新開機。 一旦上線之後，它就會下載 DSC 組態 .zip，並在 VM 上佈建它。 您可以在 [Azure DSC 擴充功能概觀](../virtual-machines/extensions/dsc-overview.md?toc=/azure/virtual-machines/windows/toc.json)中找到更多詳細資料。

## <a name="next-steps"></a>接下來的步驟
查看 [適用於 DSC 擴充功能的 Azure Resource Manager 範本](../virtual-machines/extensions/dsc-template.md?toc=/azure/virtual-machines/windows/toc.json)。

了解 [DSC 擴充功能如何安全地處理認證](../virtual-machines/extensions/dsc-credentials.md?toc=/azure/virtual-machines/windows/toc.json)。 

如需有關 Azure DSC 擴充功能處理常式的詳細資訊，請參閱 [Azure 期望狀態組態擴充功能處理常式簡介](../virtual-machines/extensions/dsc-overview.md?toc=/azure/virtual-machines/windows/toc.json)。 

如需有關 PowerShell DSC 的詳細資訊，請 [瀏覽 PowerShell 文件中心](/powershell/scripting/dsc/overview/overview)。 

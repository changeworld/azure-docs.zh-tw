---
title: '將虛擬機器擴展集延伸模組新增至 Service Fabric 受控叢集節點類型 (preview) '
description: 以下說明如何將虛擬機器擴展集擴充功能新增 Service Fabric 受控叢集節點類型
ms.topic: article
ms.date: 09/28/2020
ms.openlocfilehash: 10487bad4fce41c68b4e2cb90c311b986d709eee
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/28/2020
ms.locfileid: "91410296"
---
# <a name="add-a-virtual-machine-scale-set-extension-to-a-service-fabric-managed-cluster-node-type-preview"></a>將虛擬機器擴展集延伸模組新增至 Service Fabric 受控叢集節點類型 (preview) 

Service Fabric 受控叢集中的每個節點類型都是由虛擬機器擴展集支援。 這可讓您將 [虛擬機器擴展集延伸](../virtual-machines/extensions/overview.md) 模組新增至 Service Fabric 受控叢集節點類型。

您可以使用 [AzServiceFabricManagedNodeTypeVMExtension](https://docs.microsoft.com/powershell/module/az.servicefabric/add-azservicefabricmanagednodetypevmextension?view=azps-4.7.0&preserve-view=true) PowerShell 命令，將虛擬機器擴展集延伸模組新增至節點類型。

或者，您可以在 Azure Resource Manager 範本中，Service Fabric 受控叢集節點類型上的虛擬機器擴展集擴充功能，例如：

```json
{
    "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
    "apiVersion": "[variables('sfApiVersion')]",
    "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
    "dependsOn": [
        "[concat('Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'))]"
    ],
    "location": "[resourceGroup().location]",
    "properties": {
        "isPrimary": true,
        "vmInstanceCount": 3,
        "dataDiskSizeGB": 100,
        "vmSize": "Standard_D2",
        "vmImagePublisher": "MicrosoftWindowsServer",
        "vmImageOffer": "WindowsServer",
        "vmImageSku": "2019-Datacenter",
        "vmImageVersion": "latest",
        "vmExtensions": [{
            "name": "ExtensionA",
            "properties": {
                "publisher": "ExtensionA.Publisher",
                "type": "KeyVaultForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                }
            }
        }]
    }
}
```

如需設定受管理叢集節點類型 Service Fabric 的詳細資訊，請參閱 [受控叢集節點類型](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2020-01-01-preview/managedclusters/nodetypes)。

## <a name="next-steps"></a>後續步驟

若要深入瞭解 Service Fabric 受控叢集，請參閱：

> [!div class="nextstepaction"]
> [Service Fabric 受控叢集的常見問題](./faq-managed-cluster.md)

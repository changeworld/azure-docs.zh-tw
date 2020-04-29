---
title: Azure 虛擬機器擴展集連接的資料磁片
description: 瞭解如何透過特定使用案例的概述，使用連接的資料磁片搭配虛擬機器擴展集。
author: avirishuv
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 4/25/2017
ms.author: avverma
ms.openlocfilehash: 6e39a8ffb24b0cca720890e3d00a55d1e58fadc2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80123365"
---
# <a name="azure-virtual-machine-scale-sets-and-attached-data-disks"></a>Azure 虛擬機器擴展集和連結的資料磁碟
若要擴充可用的儲存體，Azure [虛擬機器擴展集](/azure/virtual-machine-scale-sets/)支援 VM 執行個體連結資料磁碟。 您可以在擴展集建立時連結資料磁碟，或將資料磁碟連結至現有擴展集。

> [!NOTE]
> 當您建立具有連結資料磁碟的擴展集時，仍需掛接和格式化 VM 內的磁碟才能加以使用 (就如同獨立 Azure VM)。 完成此程序的方便作法是使用「自訂指令碼擴充功能」，該擴充功能可呼叫指令碼來分割及格式化 VM 上的所有資料磁碟。 如需此作業的範例，請參閱 [Azure CLI](tutorial-use-disks-cli.md#prepare-the-data-disks) [Azure PowerShell](tutorial-use-disks-powershell.md#prepare-the-data-disks)。


## <a name="create-and-manage-disks-in-a-scale-set"></a>建立及管理擴展集中的磁碟
如需深入了解如何建立具有連結資料磁碟的擴展集、準備和格式化，或新增和移除資料磁碟，請參閱下列其中一個教學課程：

- [Azure CLI](tutorial-use-disks-cli.md)
- [Azure PowerShell](tutorial-use-disks-powershell.md)

本文的其餘部分將概述特定使用案例，例如需要資料磁碟的 Service Fabric 叢集，或是將包含內容的現有資料磁碟連結至擴展集。


## <a name="create-a-service-fabric-cluster-with-attached-data-disks"></a>使用連結的資料磁碟建立 Service Fabric 叢集
在 Azure 中執行之[Service Fabric](/azure/service-fabric)叢集中的每個[節點類型](../service-fabric/service-fabric-cluster-nodetypes.md)都是由虛擬機器擴展集所支援。 使用 Azure Resource Manager 範本，您可以將資料磁碟連結到構成 Service Fabric 叢集的擴展集。 您可使用[現有範本](https://github.com/Azure-Samples/service-fabric-cluster-templates)作為起點。 在範本中，於 Microsoft.Compute/virtualMachineScaleSets__ 資源的 storageProfile__ 中包含 dataDisks__ 區段並部署範本。 下列範例會連結 128 GB 資料磁碟：

```json
"dataDisks": [
    {
    "diskSizeGB": 128,
    "lun": 0,
    "createOption": "Empty"
    }
]
```

您可以在部署叢集時自動分割、格式化及掛接資料磁碟。 將自訂指令碼擴充新增至擴展集之 virtualMachineProfile__ 的 extensionProfile__。

若要在 Windows 叢集中自動準備資料磁碟，請新增下列內容：

```json
{
    "name": "customScript",
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.8",
        "autoUpgradeMinorVersion": true,
        "settings": {
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.ps1"
        ],
        "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File prepare_vm_disks.ps1"
        }
    }
}
```
若要在 Linux 叢集中自動準備資料磁碟，請新增下列內容：
```json
{
    "name": "lapextension",
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"
        ],
        "commandToExecute": "bash prepare_vm_disks.sh"
        }
    }
}
```


## <a name="adding-pre-populated-data-disks-to-an-existing-scale-set"></a>將預先填入的資料磁碟新增至現存的擴展集
在擴展集模型中指定的資料磁碟一律是空的。 不過，您可以將現有資料磁碟連結至擴展集中的特定 VM。 如果您想要將資料傳播到擴展集中的所有 Vm，您可以複製您的資料磁片，並將其連結至擴展集中的每個 VM，或建立包含資料的自訂映射，並從這個自訂映射布建擴展集，或者您可以使用 Azure 檔案儲存體或類似的資料儲存體供應專案。


## <a name="additional-notes"></a>其他注意事項
API 版本 [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/compute/resource-manager/Microsoft.Compute/preview/2016-04-30-preview/compute.json) 或更新版本的 Microsoft.Compute API 中提供 Azure 受控磁碟和擴展集連結資料磁碟的支援。

擴展集中連接資料磁片的 Azure 入口網站支援受到限制。 視您的需求而定，您可以使用 Azure 範本、CLI、PowerShell、SDK 和 REST API 來管理連結磁碟。



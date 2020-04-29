---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0df74b82c847c9738d97d2001573666714c17672
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81008320"
---
## <a name="limitations"></a>限制

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>磁碟大小

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-shared-disks"></a>部署共用磁片

### <a name="deploy-a-premium-ssd-as-a-shared-disk"></a>將 premium SSD 部署為共用磁片

若要部署已啟用共用磁片功能的受控磁片，請使用新的`maxShares`屬性並定義大於1的值。 這可讓您跨多個 Vm 共用該磁片。

> [!IMPORTANT]
> 只有當磁片`maxShares`從所有 vm 卸載時，才可以設定或變更的值。 如需的允許值，請參閱[磁片大小](#disk-sizes) `maxShares`。

使用下列範本之前，請將`[parameters('dataDiskName')]`、 `[resourceGroup().location]`、 `[parameters('dataDiskSizeGB')]`和`[parameters('maxShares')]`取代為您自己的值。

```json
{ 
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "dataDiskName": {
      "type": "string",
      "defaultValue": "mySharedDisk"
    },
    "dataDiskSizeGB": {
      "type": "int",
      "defaultValue": 1024
    },
    "maxShares": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/disks",
      "name": "[parameters('dataDiskName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-07-01",
      "sku": {
        "name": "Premium_LRS"
      },
      "properties": {
        "creationData": {
          "createOption": "Empty"
        },
        "diskSizeGB": "[parameters('dataDiskSizeGB')]",
        "maxShares": "[parameters('maxShares')]"
      }
    }
  ] 
}
```

### <a name="deploy-an-ultra-disk-as-a-shared-disk"></a>將 ultra 磁片部署為共用磁片

#### <a name="cli"></a>CLI

若要部署已啟用共用磁片功能的受控磁片，請將`maxShares`參數變更為大於1的值。 這可讓您跨多個 Vm 共用該磁片。

> [!IMPORTANT]
> 只有當磁片`maxShares`從所有 vm 卸載時，才可以設定或變更的值。 如需的允許值，請參閱[磁片大小](#disk-sizes) `maxShares`。

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

#### <a name="azure-resource-manager"></a>Azure Resource Manager

若要部署已啟用共用磁片功能的受控磁片，請使用屬性`maxShares`並定義大於1的值。 這可讓您跨多個 Vm 共用該磁片。

> [!IMPORTANT]
> 只有當磁片`maxShares`從所有 vm 卸載時，才可以設定或變更的值。 如需的允許值，請參閱[磁片大小](#disk-sizes) `maxShares`。

使用下列範本之前，請將`[parameters('dataDiskName')]`、 `[resourceGroup().location]`、 `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` `[parameters('diskIOPSReadWrite')]` `[parameters('diskMBpsReadWrite')]` `[parameters('diskIOPSReadOnly')]`、、、、和`[parameters('diskMBpsReadOnly')]`取代為您自己的值。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "uShared30"
    },
    "location": {
        "type": "string",
        "defaultValue": "westus",
        "metadata": {
                "description": "Location for all resources."
        }
    },
    "dataDiskSizeGB": {
      "type": "int",
      "defaultValue": 1024
    },
    "maxShares": {
      "type": "int",
      "defaultValue": 2
    },
    "diskIOPSReadWrite": {
      "type": "int",
      "defaultValue": 2048
    },
    "diskMBpsReadWrite": {
      "type": "int",
      "defaultValue": 20
    },    
    "diskIOPSReadOnly": {
      "type": "int",
      "defaultValue": 100
    },
    "diskMBpsReadOnly": {
      "type": "int",
      "defaultValue": 1
    }    
  }, 
  "resources": [
    {
        "type": "Microsoft.Compute/disks",
        "name": "[parameters('diskName')]",
        "location": "[parameters('location')]",
        "apiVersion": "2019-07-01",
        "sku": {
            "name": "UltraSSD_LRS"
        },
        "properties": {
            "creationData": {
                "createOption": "Empty"
            },
            "diskSizeGB": "[parameters('dataDiskSizeGB')]",
            "maxShares": "[parameters('maxShares')]",
            "diskIOPSReadWrite": "[parameters('diskIOPSReadWrite')]",
            "diskMBpsReadWrite": "[parameters('diskMBpsReadWrite')]",
            "diskIOPSReadOnly": "[parameters('diskIOPSReadOnly')]",
            "diskMBpsReadOnly": "[parameters('diskMBpsReadOnly')]"
        }
    }
  ]
}
```

### <a name="using-azure-shared-disks-with-your-vms"></a>將 Azure 共用磁片與您的 Vm 搭配使用

當您使用`maxShares>1`部署共用磁片之後，就可以將該磁片掛接到一或多個 vm。

> [!IMPORTANT]
> 所有共用磁片的 Vm 都必須部署在相同的[鄰近放置群組](../articles/virtual-machines/windows/proximity-placement-groups.md)中。

```azurepowershell-interactive

$resourceGroup = "myResourceGroup"
$location = "WestCentralUS"
$ppgName = "myPPG"
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard

$vm = New-AzVm -ResourceGroupName $resourceGroup -Name "myVM" -Location $location -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress" -ProximityPlacementGroup $ppg.Id

$dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup -DiskName "mySharedDisk"

$vm = Add-AzVMDataDisk -VM $vm -Name "mySharedDisk" -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0

update-AzVm -VM $vm -ResourceGroupName $resourceGroup
```

## <a name="supported-scsi-pr-commands"></a>支援的 SCSI PR 命令

將共用磁片掛接到叢集中的 Vm 之後，您可以使用 SCSI PR 來建立仲裁併讀取/寫入至磁片。 使用 Azure 共用磁片時，可使用下列 PR 命令：

若要與磁片互動，請從持續保留-動作清單開始：

```
PR_REGISTER_KEY 

PR_REGISTER_AND_IGNORE 

PR_GET_CONFIGURATION 

PR_RESERVE 

PR_PREEMPT_RESERVATION 

PR_CLEAR_RESERVATION 

PR_RELEASE_RESERVATION 
```

使用 PR_RESERVE、PR_PREEMPT_RESER加值稅ION 或 PR_RELEASE_RESER加值稅ION 時，請提供下列其中一種持續性保留類型：

```
PR_NONE 

PR_WRITE_EXCLUSIVE 

PR_EXCLUSIVE_ACCESS 

PR_WRITE_EXCLUSIVE_REGISTRANTS_ONLY 

PR_EXCLUSIVE_ACCESS_REGISTRANTS_ONLY 

PR_WRITE_EXCLUSIVE_ALL_REGISTRANTS 

PR_EXCLUSIVE_ACCESS_ALL_REGISTRANTS 
```

使用 PR_RESERVE、PR_REGISTER_AND_IGNORE、PR_REGISTER_KEY、PR_PREEMPT_RESER加值稅ION、PR_CLEAR_RESER加值稅ION 或 PR_RELEASE 保留時，您也需要提供持續保留金鑰。


## <a name="next-steps"></a>後續步驟

如果您想要試用共用磁片，請[註冊我們的預覽版](https://aka.ms/AzureSharedDiskPreviewSignUp)。
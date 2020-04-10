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
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008320"
---
## <a name="limitations"></a>限制

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>磁碟大小

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-shared-disks"></a>部署分享磁碟

### <a name="deploy-a-premium-ssd-as-a-shared-disk"></a>將進階 SSD 部署為分享磁碟

要部署啟用共用磁碟功能的託管磁碟,請使用新屬性`maxShares`並定義大於 1 的值。 這使得磁碟可跨多個 VM 共用。

> [!IMPORTANT]
> 僅當從所有`maxShares`VM 卸載磁碟時,才能設置或更改的值。 有關 的允許值,請參閱[磁碟大小](#disk-sizes)`maxShares`。

在使用以下範本之前,請`[parameters('dataDiskName')]``[parameters('maxShares')]`用`[resourceGroup().location]`您自己的`[parameters('dataDiskSizeGB')]`值替換、和。

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

### <a name="deploy-an-ultra-disk-as-a-shared-disk"></a>將超磁碟部署為共用磁碟

#### <a name="cli"></a>CLI

要部署啟用共用磁碟功能的託管磁碟,請將`maxShares`參數更改為大於 1 的值。 這使得磁碟可跨多個 VM 共用。

> [!IMPORTANT]
> 僅當從所有`maxShares`VM 卸載磁碟時,才能設置或更改的值。 有關 的允許值,請參閱[磁碟大小](#disk-sizes)`maxShares`。

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

#### <a name="azure-resource-manager"></a>Azure Resource Manager

要部署啟用共用磁碟功能的託管磁碟,請使用 該屬性`maxShares`並定義大於 1 的值。 這使得磁碟可跨多個 VM 共用。

> [!IMPORTANT]
> 僅當從所有`maxShares`VM 卸載磁碟時,才能設置或更改的值。 有關 的允許值,請參閱[磁碟大小](#disk-sizes)`maxShares`。

在使用以下`[parameters('dataDiskName')]`樣本之前,`[resourceGroup().location]``[parameters('dataDiskSizeGB')]``[parameters('maxShares')]``[parameters('diskIOPSReadWrite')]``[parameters('diskMBpsReadWrite')]``[parameters('diskIOPSReadOnly')]`請使用您自己的值取代、 、 、 、 `[parameters('diskMBpsReadOnly')]` 、 、 、 、 、

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

### <a name="using-azure-shared-disks-with-your-vms"></a>將 Azure 分享磁碟與 VM 一起使用

使用`maxShares>1`中部署了共用磁碟后,可以將磁碟裝載到一個或多個 VM。

> [!IMPORTANT]
> 共用磁碟的所有 VM 都必須部署在同一[接近放置群組中](../articles/virtual-machines/windows/proximity-placement-groups.md)。

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

將共用磁碟安裝到群集中的 VM 後,可以使用 SCSI PR 建立仲裁並讀取/寫入磁碟。 使用 Azure 共用磁碟時,可以使用以下 PR 命令:

要與磁碟進行互動,從持久保留操作清單開始:

```
PR_REGISTER_KEY 

PR_REGISTER_AND_IGNORE 

PR_GET_CONFIGURATION 

PR_RESERVE 

PR_PREEMPT_RESERVATION 

PR_CLEAR_RESERVATION 

PR_RELEASE_RESERVATION 
```

使用PR_RESERVE、PR_PREEMPT_RESERVATION或PR_RELEASE_RESERVATION時,提供以下持久保留類型之一:

```
PR_NONE 

PR_WRITE_EXCLUSIVE 

PR_EXCLUSIVE_ACCESS 

PR_WRITE_EXCLUSIVE_REGISTRANTS_ONLY 

PR_EXCLUSIVE_ACCESS_REGISTRANTS_ONLY 

PR_WRITE_EXCLUSIVE_ALL_REGISTRANTS 

PR_EXCLUSIVE_ACCESS_ALL_REGISTRANTS 
```

使用PR_RESERVE、PR_REGISTER_AND_IGNORE、PR_REGISTER_KEY、PR_PREEMPT_RESERVATION、PR_CLEAR_RESERVATION或PR_RELEASE-保留時,還需要提供持久保留密鑰。


## <a name="next-steps"></a>後續步驟

如果您有興趣嘗試共用磁碟,[請註冊我們的預覽](https://aka.ms/AzureSharedDiskPreviewSignUp)版。
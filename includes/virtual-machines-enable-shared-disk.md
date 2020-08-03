---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/30/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 77c21dab8c1a4c2643db0a56b5052f33243f2f56
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87460046"
---
## <a name="limitations"></a>限制

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="supported-operating-systems"></a>支援的作業系統

共用磁片支援數個作業系統。 請參閱概念性文章的[Windows](../articles/virtual-machines/windows/disks-shared.md#windows)和[Linux](../articles/virtual-machines/linux/disks-shared.md#linux)小節，以瞭解支援的作業系統。

## <a name="disk-sizes"></a>磁碟大小

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-shared-disks"></a>部署共用磁片

### <a name="deploy-a-premium-ssd-as-a-shared-disk"></a>將 premium SSD 部署為共用磁片

若要部署已啟用共用磁片功能的受控磁片，請使用新的屬性 `maxShares` 並定義大於1的值。 這可讓您跨多個 Vm 共用該磁片。

> [!IMPORTANT]
> `maxShares`只有當磁片從所有 vm 卸載時，才可以設定或變更的值。 如需的允許值，請參閱[磁片大小](#disk-sizes) `maxShares` 。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az disk create -g myResourceGroup -n mySharedDisk --size-gb 1024 -l westcentralus --sku PremiumSSD_LRS --max-shares 2
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$dataDiskConfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType PremiumSSD_LRS -CreateOption Empty -MaxSharesCount 2

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $dataDiskConfig
```

# <a name="resource-manager-template"></a>[Resource Manager 範本](#tab/azure-resource-manager)

使用下列範本之前，請 `[parameters('dataDiskName')]` 將、 `[resourceGroup().location]` 、 `[parameters('dataDiskSizeGB')]` 和取代 `[parameters('maxShares')]` 為您自己的值。

[進階 SSD 共用磁片範本](https://aka.ms/SharedPremiumDiskARMtemplate)

---

### <a name="deploy-an-ultra-disk-as-a-shared-disk"></a>將 ultra 磁片部署為共用磁片

若要部署已啟用共用磁片功能的受控磁片，請將 `maxShares` 參數變更為大於1的值。 這可讓您跨多個 Vm 共用該磁片。

> [!IMPORTANT]
> `maxShares`只有當磁片從所有 vm 卸載時，才可以設定或變更的值。 如需的允許值，請參閱[磁片大小](#disk-sizes) `maxShares` 。


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

##### <a name="regional-disk-example"></a>區域磁片範例

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

##### <a name="zonal-disk-example"></a>區域性磁片範例

這個範例幾乎與先前的相同，不同之處在于它會在可用性區域1中建立磁片。

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1 --zone 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

##### <a name="regional-disk-example"></a>區域磁片範例

```azurepowershell-interactive
$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1 -MaxSharesCount 5

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig
```

##### <a name="zonal-disk-example"></a>區域性磁片範例

這個範例幾乎與先前的相同，不同之處在于它會在可用性區域1中建立磁片。

```azurepowershell-interactive
$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1 -MaxSharesCount 5 -Zone 1

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig
```

# <a name="resource-manager-template"></a>[Resource Manager 範本](#tab/azure-resource-manager)

##### <a name="regional-disk-example"></a>區域磁片範例

使用下列範本之前，請將、、、、、、 `[parameters('dataDiskName')]` `[resourceGroup().location]` 和取代 `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` `[parameters('diskIOPSReadWrite')]` `[parameters('diskMBpsReadWrite')]` `[parameters('diskIOPSReadOnly')]` `[parameters('diskMBpsReadOnly')]` 為您自己的值。

[區域共用的 ultra 磁片範本](https://aka.ms/SharedUltraDiskARMtemplateRegional)

##### <a name="zonal-disk-example"></a>區域性磁片範例

使用下列範本之前，請將、、、、、、 `[parameters('dataDiskName')]` `[resourceGroup().location]` 和取代 `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` `[parameters('diskIOPSReadWrite')]` `[parameters('diskMBpsReadWrite')]` `[parameters('diskIOPSReadOnly')]` `[parameters('diskMBpsReadOnly')]` 為您自己的值。

[區域性共用的 ultra 磁片範本](https://aka.ms/SharedUltraDiskARMtemplateZonal)

---

## <a name="using-azure-shared-disks-with-your-vms"></a>將 Azure 共用磁片與您的 Vm 搭配使用

當您使用部署共用磁片之後 `maxShares>1` ，就可以將該磁片掛接到一或多個 vm。

> [!NOTE]
> 如果您要部署 ultra 磁片，請確定它符合必要的需求。 如需詳細資訊，請參閱 ultra 磁片一文的[PowerShell](../articles/virtual-machines/windows/disks-enable-ultra-ssd.md#enable-ultra-disk-compatibility-on-an-existing-vm-1)或[CLI](../articles/virtual-machines/linux/disks-enable-ultra-ssd.md#enable-ultra-disk-compatibility-on-an-existing-vm)一節。

```azurepowershell-interactive

$resourceGroup = "myResourceGroup"
$location = "WestCentralUS"

$vm = New-AzVm -ResourceGroupName $resourceGroup -Name "myVM" -Location $location -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress"

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

如果您想要使用 Azure Resource Manager 範本來部署您的磁片，則可使用下列範例範本：
- [進階 SSD](https://aka.ms/SharedPremiumDiskARMtemplate)
- [區域 ultra 磁片](https://aka.ms/SharedUltraDiskARMtemplateRegional)
- [區域性 ultra 磁片](https://aka.ms/SharedUltraDiskARMtemplateZonal)
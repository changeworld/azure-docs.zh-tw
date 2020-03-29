---
title: 在標準和高級 SSD 之間轉換託管磁片存儲
description: 如何使用 Azure CLI 將 Azure 託管磁片存儲從標準轉換為高級或高級到標準。
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: cd9bb92b3ed86c3a57b5fc70411a4593335acedb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431496"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>將 Azure 託管磁片存儲從"標準"轉換為高級磁片或高級磁片存儲

Azure 託管磁片有四種磁片類型：Azure 超 SSD（預覽）、高級 SSD、標準 SSD 和標準 HDD。 您可以根據您的性能需求在三種 GA 磁片類型（高級 SSD、標準 SSD 和標準硬碟）之間進行切換。 您還不能從超級 SSD 切換到超級 SSD，必須部署新的 SSD。

非託管磁片不支援此功能。 但是，您可以輕鬆地[將非託管磁片轉換為託管磁片](convert-unmanaged-to-managed-disks.md)，以便能夠在磁片類型之間切換。

本文演示如何使用 Azure CLI 將託管磁片從"標準磁片"轉換為高級磁片或高級磁片。 要安裝或升級該工具，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="before-you-begin"></a>開始之前

* 磁片轉換需要重新開機虛擬機器 （VM），因此請安排磁片存儲在預先存在的維護時段內遷移。
* 對於非託管磁片，首先[轉換為託管磁片，](convert-unmanaged-to-managed-disks.md)以便在存儲選項之間切換。


## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>在高級版和標準版之間切換 VM 的所有託管磁片

此示例演示如何將 VM 的所有磁片從標準存儲轉換為高級存儲或從高級存儲轉換為標準存儲。 若要使用進階受控磁碟，VM 必須使用可支援進階儲存體的 [VM 大小](sizes.md)。 此示例還切換到支援高級存儲的大小。

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the SKU of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the SKU of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>在標準磁片和高級磁片之間切換單個託管磁片

對於開發/測試工作負載，您可能希望混合使用標準磁片和高級磁片來降低成本。 您可以選擇僅升級那些需要更好性能的磁片。 此示例演示如何將單個 VM 磁片從標準存儲轉換為高級存儲或從高級存儲轉換為標準存儲。 若要使用進階受控磁碟，VM 必須使用可支援進階儲存體的 [VM 大小](sizes.md)。 此示例還切換到支援高級存儲的大小。

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --ids $vmId --size $size

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>在標準硬碟和標準 SSD 之間切換託管磁片

此示例演示如何將單個 VM 磁片從標準硬碟轉換為標準 SSD 或從標準 SSD 轉換為標準硬碟。

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Choose between Standard_LRS and StandardSSD_LRS based on your scenario
sku='StandardSSD_LRS'

#Get the parent VM ID 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the disk type
az vm deallocate --ids $vmId 

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-between-standard-and-premium-in-azure-portal"></a>在 Azure 門戶中在標準版和高級磁片之間切換託管磁片

請遵循下列步驟：

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 從**虛擬機器**清單中選擇 VM。
3. 如果 VM 未停止，請選擇"VM**概述"** 窗格頂部的 **"停止"，** 然後等待 VM 停止。
4. 在 VM 的窗格中，從功能表中選擇 **"磁片**"。
5. 選擇要轉換的磁片。
6. 從功能表中選擇 **"配置**"。
7. 將**帳戶類型**從**標準硬碟**更改為高級**SSD**或從**高級 SSD**更改為**標準硬碟**。
8. 選擇 **"保存**"並關閉磁片窗格。

磁片類型的更新是即時的。 轉換後可以重新開機 VM。

## <a name="next-steps"></a>後續步驟

使用[快照](snapshot-copy-managed-disk.md)創建 VM 的唯讀副本。

---
title: 轉換 standard 和 premium SSD 之間的受控磁片儲存體
description: 如何使用 Azure CLI，將 Azure 受控磁片儲存體從標準轉換至高階或高階至標準版。
author: roygara
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 07/12/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: bf16ee0af1c5816822f6a201ad1d5a70127082db
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87825373"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>將 Azure 受控磁片儲存體從標準轉換至 Premium 或 Premium 至 Standard

Azure 受控磁片有四種磁片類型： Azure ultra Ssd (preview) 、premium SSD、標準 SSD 和標準 HDD。 根據您的效能需求，您可以在三種 GA 磁片類型 (premium SSD、標準 SSD 和標準 HDD) 之間切換。 您還無法從或到 ultra SSD 進行切換，您必須部署一個新的。

非受控磁片不支援此功能。 但是，您可以輕鬆地[將非受控磁片轉換為受控磁片](convert-unmanaged-to-managed-disks.md)，以便在磁片類型之間切換。

本文說明如何使用 Azure CLI，將受控磁片從 Standard 轉換為 Premium 或 Premium。 若要安裝或升級此工具，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="before-you-begin"></a>開始之前

* 磁片轉換需要重新開機虛擬機器 (VM) ，因此請在預先存在的維護期間排定磁片儲存體的遷移。
* 若是非受控磁片，請先[轉換為受控磁片](convert-unmanaged-to-managed-disks.md)，以便在儲存體選項之間切換。


## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>在 Premium 與 Standard 之間切換 VM 的所有受控磁片

此範例示範如何將 VM 的所有磁片從標準轉換至高階儲存體，或從 Premium 轉換至標準儲存體。 若要使用進階受控磁碟，VM 必須使用可支援進階儲存體的 [VM 大小](../sizes.md)。 此範例也會切換成支援高階儲存體的大小。

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
## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>在 Standard 和 Premium 之間切換個別受控磁片

針對您的開發/測試工作負載，您可能會想要混合使用標準和 Premium 磁片，以降低成本。 您可以選擇只升級需要較佳效能的磁片。 此範例示範如何將單一 VM 磁片從標準轉換成 Premium 儲存體，或從 Premium 轉換成標準儲存體。 若要使用進階受控磁碟，VM 必須使用可支援進階儲存體的 [VM 大小](../sizes.md)。 此範例也會切換成支援高階儲存體的大小。

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

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>在標準 HDD 和標準 SSD 之間切換受控磁片

此範例示範如何將單一 VM 磁片從標準 HDD 轉換成標準 SSD 或從標準 SSD 到標準 HDD。

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

## <a name="switch-managed-disks-between-standard-and-premium-in-azure-portal"></a>在 Azure 入口網站中切換 Standard 和 Premium 之間的受控磁片

請遵循這些步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 從**虛擬機器**清單中選取 VM。
3. 如果 VM 未停止，請選取 [VM**總覽**] 窗格頂端的 [**停止**]，並等候 vm 停止。
4. 在 VM 的窗格中，從功能表中選取 [**磁片**]。
5. 選取您要轉換的磁片。
6. 從功能表**中選取 [** 設定]。
7. 將**帳戶類型**從 [**標準 HDD** ] 變更為 [**進階 SSD** ] 或 [從**進階 SSD**到**標準 HDD**。
8. 選取 [**儲存**]，然後關閉 [磁片] 窗格。

磁片類型的更新為瞬間。 您可以在轉換之後重新開機 VM。

## <a name="next-steps"></a>後續步驟

使用[快照](snapshot-copy-managed-disk.md)集建立 VM 的唯讀複本。
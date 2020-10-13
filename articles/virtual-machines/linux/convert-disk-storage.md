---
title: 轉換標準和 premium SSD 之間的受控磁片儲存體
description: 如何使用 Azure CLI 將 Azure 受控磁片儲存體從 standard 轉換為 premium 或 premium 轉換成標準。
author: roygara
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 07/12/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 10674d15a7515b01b0df6cf37bce89f153cb9b0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88870680"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>將 Azure 受控磁片儲存體從標準轉換至 Premium 或 Premium 轉換成標準

Azure 受控磁片有四種磁片類型： Azure ultra 磁片、premium SSD、標準 SSD 和標準 HDD。 您可以根據您的效能需求，切換三個 GA 磁片類型 (premium SSD、標準 SSD 和標準 HDD) 。 您還無法從 ultra 磁片切換，也必須部署一個新的磁片。

非受控磁片不支援此功能。 但是，您可以輕鬆地 [將非受控磁片轉換成受控磁片](convert-unmanaged-to-managed-disks.md) ，以便在磁片類型之間切換。

本文說明如何使用 Azure CLI，將受控磁片從 Standard 轉換為 Premium 或 Premium 轉換為 Standard。 若要安裝或升級工具，請參閱 [安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="before-you-begin"></a>開始之前

* 磁片轉換需要重新開機 (VM) 的虛擬機器，因此請在預先存在的維護期間排程磁片儲存體的遷移。
* 針對非受控磁片，請先 [轉換成受控磁片](convert-unmanaged-to-managed-disks.md) ，以便您可以在儲存體選項之間切換。


## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>在 Premium 和 Standard 之間切換 VM 的所有受控磁片

此範例示範如何將 VM 的所有磁片從標準轉換至 Premium 儲存體，或從 Premium 轉換成標準儲存體。 若要使用進階受控磁碟，VM 必須使用可支援進階儲存體的 [VM 大小](../sizes.md)。 此範例也會切換至支援 Premium 儲存體的大小。

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
## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>在 Standard 與 Premium 之間切換個別的受控磁片

針對您的開發/測試工作負載，您可能會想要混用標準和 Premium 磁片，以降低成本。 您可以選擇只升級需要更佳效能的磁片。 此範例示範如何將單一 VM 磁片從標準轉換至 Premium 儲存體，或從 Premium 轉換成標準儲存體。 若要使用進階受控磁碟，VM 必須使用可支援進階儲存體的 [VM 大小](../sizes.md)。 此範例也會切換至支援 Premium 儲存體的大小。

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

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>切換標準 HDD 與標準 SSD 之間的受控磁片

此範例示範如何將單一 VM 磁片從標準 HDD 轉換為標準 SSD，或從標準 SSD 轉換為標準 HDD。

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

## <a name="switch-managed-disks-between-standard-and-premium-in-azure-portal"></a>在 Azure 入口網站中的 Standard 與 Premium 之間切換受控磁片

請遵循這些步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 從 **虛擬機器**清單中選取 VM。
3. 如果 VM 未停止，請選取 VM 總覽窗格頂端的 **[** **停止**]，並等候 vm 停止。
4. 在 VM 的窗格中，從功能表中選取 [ **磁片** ]。
5. 選取您要轉換的磁片。
6. 從 **功能表** 選取 [設定]。
7. 將 **帳戶類型** 從 **標準 HDD** 變更為 **進階 SSD** 或從 **進階 SSD** 變更為 **標準 HDD**。
8. 選取 [ **儲存**]，然後關閉 [磁片] 窗格。

磁片類型的更新會立即生效。 您可以在轉換後重新開機 VM。

## <a name="next-steps"></a>接下來的步驟

使用 [快照](snapshot-copy-managed-disk.md)集建立 VM 的唯讀複本。
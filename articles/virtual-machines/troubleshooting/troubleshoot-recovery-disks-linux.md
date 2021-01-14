---
title: 透過 Azure CLI 使用 Linux 針對 VM 進行疑難排解 | Microsoft Docs
description: 了解如何使用 Azure CLI 將 OS 磁碟連接至復原 VM，以針對 Linux VM 問題進行疑難排解
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/16/2017
ms.author: genli
ms.openlocfilehash: 70855f7956e42d815e0b8135ccfe6e374853d8aa
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98197518"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli"></a>透過 Azure CLI 將 OS 磁碟連結到復原 VM，以對 Linux VM 進行疑難排解
如果 Linux 虛擬機器 (VM) 發生開機或磁碟錯誤，您可能需要對虛擬硬碟本身執行疑難排解步驟。 常見的例子是 `/etc/fstab` 中的項目無效，導致 VM 無法成功開機。 本文詳細說明如何使用 Azure CLI 將虛擬硬碟連接至另一個 Linux VM，以修正任何錯誤，然後重新建立原始 VM。 

## <a name="recovery-process-overview"></a>復原程序概觀
疑難排解程序如下所示︰

1. 停止受影響的 VM。
1. 從 VM 的 OS 磁片取得快照。
1. 從 OS 磁碟快照集建立磁碟。
1. 將新的 OS 磁片連接並掛接至另一個 Linux VM，以供疑難排解之用。
1. 連接至疑難排解 VM。 編輯檔案或執行任何工具來修正新 OS 磁片上的問題。
1. 從疑難排解 VM 卸載並卸離新的 OS 磁片。
1. 變更受影響 VM 的 OS 磁碟。

若要執行這些疑難排解步驟，您需要安裝最新的 [Azure CLI](/cli/azure/install-az-cli2)，並且使用 [az login](/cli/azure/reference-index) 登入 Azure 帳戶。

您可以使用 VM repair 命令將步驟1、2、3、4、6和7自動化。 如需詳細的檔和指示，請參閱 [使用 Azure 虛擬機器修復命令修復 LINUX VM](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)。

> [!Important]
> 本文中的指令碼只適用於使用[受控磁碟](../managed-disks-overview.md)的 VM。 

在下列範例中，將參數名稱取代為您自己的值，例如 `myResourceGroup` 和 `myVM` 。

## <a name="determine-boot-issues"></a>判斷開機問題
檢查序列輸出來判斷 VM 為何無法正常開機。 常見的例子是 `/etc/fstab` 中的項目無效，或因為刪除或移動基礎虛擬硬碟。

使用 [az vm boot-diagnostics get-boot-log](/cli/azure/vm/boot-diagnostics) 取得與開機記錄。 下列範例會從資源群組 `myResourceGroup` 中的 VM `myVM` 取得序列輸出：

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

檢閱序列輸出來判斷 VM 為何無法開機。 如果序列輸出未提供任何指示，您可能需要將虛擬硬碟連接至疑難排解 VM，然後檢閱 `/var/log` 中的記錄檔。

## <a name="stop-the-vm"></a>停止 VM

下列範例會從資源群組 `myResourceGroup` 中停止 VM `myVM`：

```azurecli
az vm stop --resource-group MyResourceGroup --name MyVm
```
## <a name="take-a-snapshot-from-the-os-disk-of-the-affected-vm"></a>從受影響 VM 的 OS 磁片取得快照集

快照集是完整的 VHD 唯讀複本。 無法將它連結至 VM。 在下一個步驟中，我們將從此快照集建立磁碟。 下列範例會從 'myVM' VM 的 OS 磁碟建立名為 `mySnapshot` 的快照集。 

```azurecli
#Get the OS disk Id 
$osdiskid=(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)

#creates a snapshot of the disk
az snapshot create --resource-group myResourceGroupDisk --source "$osdiskid" --name mySnapshot
```
## <a name="create-a-disk-from-the-snapshot"></a>從快照集建立磁碟

此指令碼會從快照集 `mySnapshot` 建立名為 `myOSDisk` 的受控磁碟。  

```azurecli
#Provide the name of your resource group
$resourceGroup="myResourceGroup"

#Provide the name of the snapshot that will be used to create Managed Disks
$snapshot="mySnapshot"

#Provide the name of the Managed Disk
$osDisk="myNewOSDisk"

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize=128

#Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
$storageType="Premium_LRS"

#Provide the OS type
$osType="linux"

#Get the snapshot Id 
$snapshotId=(az snapshot show --name $snapshot --resource-group $resourceGroup --query id -o tsv)

# Create a new Managed Disks using the snapshot Id.

az disk create --resource-group $resourceGroup --name $osDisk --sku $storageType --size-gb $diskSize --source $snapshotId

```

如果資源群組和來源快照集不在相同的區域中，當您執行時，將會收到「找不到資源」錯誤 `az disk create` 。 在此情況下，您必須指定 `--location <region>` 將磁片建立為與來源快照集相同的區域。

您現在有原始 OS 磁碟的複本。 您可以將這個新磁片掛接至另一個 Windows VM，以進行疑難排解。

## <a name="attach-the-new-virtual-hard-disk-to-another-vm"></a>將新的虛擬硬碟連接至另一個 VM
在接下來幾個步驟中，您將使用另一個 VM 進行疑難排解。 您將磁片連接到這個疑難排解 VM，以流覽和編輯磁片的內容。 此程式可讓您更正任何設定錯誤，或查看其他應用程式或系統記錄檔。

此腳本會將磁片連結 `myNewOSDisk` 至 VM `MyTroubleshootVM` ：

```azurecli
# Get ID of the OS disk that you just created.
$myNewOSDiskid=(az disk show -g $resourceGroup -n $osDisk --query id -o tsv)

# Attach the disk to the troubleshooting VM
az vm disk attach --disk $myNewOSDiskid --resource-group $resourceGroup --size-gb $diskSize --sku $storageType --vm-name MyTroubleshootVM
```
## <a name="mount-the-attached-data-disk"></a>掛接已連結的資料磁碟

> [!NOTE]
> 下列範例詳細說明 Ubuntu VM 上所需的步驟。 如果您使用不同的 Linux 發行版本，例如 Red Hat Enterprise Linux 或 SUSE，則記錄檔位置和 `mount` 命令可能稍微不同。 請參閱您的特定發行版本的文件，以了解命令中相應的變更。

1. 使用適當的認證以 SSH 登入疑難排解 VM。 如果此磁碟是第一個連結至疑難排解 VM 的資料磁碟，則磁碟很可能連結至 `/dev/sdc`。 使用 `dmesg` 來檢視已連結的磁碟︰

    ```bash
    dmesg | grep SCSI
    ```

    輸出類似於下列範例：

    ```bash
    [    0.294784] SCSI subsystem initialized
    [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
    [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
    [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
    [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
    ```

    在上述範例中，OS 磁碟位於 `/dev/sda`，而提供給每個 VM 的暫存磁碟位於 `/dev/sdb`。 如果您有多個資料磁碟，它們應該是位於 `/dev/sdd`、`/dev/sde`，依此類推。

2. 建立目錄來掛接現有的虛擬硬碟。 下列範例會建立名為 `troubleshootingdisk` 的目錄：

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. 如果您在現有的虛擬硬碟上有多個磁碟分割，請掛接所需的磁碟分割。 下列範例會將第一個主要磁碟分割掛接在 `/dev/sdc1`：

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > 最佳做法是使用虛擬硬碟的通用唯一識別碼 (UUID)，將資料磁碟掛接在 Azure 中的 VM。 在這個簡短的疑難排解案例中，不需要使用 UUID 來掛接虛擬硬碟。 但在正常使用情況下，如果編輯 `/etc/fstab` 來使用裝置名稱掛接虛擬硬碟，而不是使用 UUID，可能會造成 VM 無法開機。


## <a name="fix-issues-on-the-new-os-disk"></a>修正新 OS 磁片上的問題
已掛接現有的虛擬硬碟掛，您現在可以視需要執行任何維護和疑難排解步驟。 解決問題之後，請繼續進行下列步驟。


## <a name="unmount-and-detach-the-new-os-disk"></a>卸載並卸離新的 OS 磁片
一旦解決錯誤，您就要從疑難排解 VM 卸載中斷連結並現有的虛擬硬碟。 直到將虛擬硬碟連結至疑難排解 VM 的租用釋放，您才能將虛擬硬碟用於其他任何 VM。

1. 從疑難排解 VM 的 SSH 工作階段，卸載現有的虛擬硬碟。 首先離開掛接點的上層目錄︰

    ```bash
    cd /
    ```

    現在卸載現有的虛擬硬碟。 下列範例會卸載位於 `/dev/sdc1` 的裝置：

    ```bash
    sudo umount /dev/sdc1
    ```

2. 現在從 VM 中斷連結虛擬硬碟。 結束您疑難排解 VM 的 SSH 會話：

    ```azurecli
    az vm disk detach -g MyResourceGroup --vm-name MyTroubleShootVm --name myNewOSDisk
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>變更受影響 VM 的 OS 磁碟

您可以使用 Azure CLI 來交換 OS 磁片。 您不需要刪除及重新建立虛擬機器。

此範例會停止名為 `myVM` 的 VM，並將名為 `myNewOSDisk` 的磁碟指派為新的 OS 磁碟。

```azurecli
# Stop the affected VM
az vm stop -n myVM -g myResourceGroup

# Get ID of the OS disk that is repaired.
$myNewOSDiskid=(az vm show -g $resourceGroup -n $osDisk --query id -o tsv)

# Change the OS disk of the affected VM to "myNewOSDisk"
az vm update -g myResourceGroup -n myVM --os-disk $myNewOSDiskid

# Start the VM
az vm start -n myVM -g myResourceGroup
```

## <a name="next-steps"></a>後續步驟
如果連接至 VM 時發生問題，請參閱[針對 Azure VM 的 SSH 連接進行疑難排解](troubleshoot-ssh-connection.md)。 如果存取 VM 上執行的應用程式時發生問題，請參閱[針對 Linux VM 上的應用程式連線問題進行疑難排解](troubleshoot-app-connection.md)。


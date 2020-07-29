---
title: 擴充 Linux VM 上的虛擬硬碟
description: 瞭解如何使用 Azure CLI 擴充 Linux VM 上的虛擬硬碟。
author: roygara
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 10/15/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 27c9a7c2e526a33875402827e2eee2c63943e058
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84659733"
---
# <a name="expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>使用 Azure CLI 擴充 Linux VM 上的虛擬硬碟

本文將說明如何使用 Azure CLI 來擴充 Linux 虛擬機器 (VM) 的受控磁碟。 您可以[新增資料磁碟](add-disk.md)來提供更多儲存空間，而您也可以擴充既有的資料磁碟。 在 Azure 中，Linux VM 上作業系統 (OS) 的預設虛擬硬碟大小通常是 30 GB。 

> [!WARNING]
> 請務必確定您的檔案系統處於狀況良好的狀態，您的磁碟分割資料表類型將會支援新的大小，並確保您的資料會在執行磁片調整大小作業之前進行備份。 如需詳細資訊，請參閱[在 Azure 中備份 Linux 虛擬機器](tutorial-backup-vms.md)。 

## <a name="expand-an-azure-managed-disk"></a>擴充 Azure 受控磁碟
確定您已安裝最新的 [Azure CLI](/cli/azure/install-az-cli2)，並且已使用 [az login](/cli/azure/reference-index#az-login) 登入 Azure 帳戶。

本文需要 Azure 中存有一個虛擬機器，且該虛擬機器至少掛載一個已備妥使用的資料磁碟。 如果您還沒有可使用的虛擬機器，請參閱[建立並準備掛載有資料磁碟的虛擬機器](tutorial-manage-disks.md#create-and-attach-disks)。

在下列範例中，以您自己的值取代範例參數名稱，例如 *myResourceGroup* 和 *myVM*。

1. 當 VM 正在執行時，無法對虛擬硬碟執行作業。 使用 [az vm deallocate](/cli/azure/vm#az-vm-deallocate) 解除配置您的 VM。 下列範例會將名為*myResourceGroup*的資源群組中名為*myVM*的 VM 解除配置：

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > 必須解除配置 VM，才能擴充虛擬硬碟。 使用 `az vm stop` 停止 VM，不會釋放計算資源。 若要釋放計算資源，請使用 `az vm deallocate`。

1. 使用 [az disk list](/cli/azure/disk#az-disk-list) 來檢視資源群組中的受控磁碟清單。 下列範例會顯示名為 myResourceGroup** 之資源群組中的受控磁碟清單：

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    使用 [az disk update](/cli/azure/disk#az-disk-update) 擴充所需的磁碟。 下列範例會將名為 *myDataDisk* 的受控磁碟擴充為 *200* GB：

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > 當您擴充受控磁碟時，會將更新的大小向上調整為最接近的受控磁碟大小。 如需可用受控磁碟大小和階層的表格，請參閱 [Azure 受控磁碟概觀 - 價格和計費](../windows/managed-disks-overview.md)。

1. 使用 [az vm create](/cli/azure/vm#az-vm-start) 啟動 VM。 下列範例會啟動名為 myResourceGroup** 資源群組中名為 myVM** 的 VM：

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```


## <a name="expand-a-disk-partition-and-filesystem"></a>擴充磁碟分割與檔案系統
若要使用擴充的硬碟，請擴充底層磁碟分割與檔案系統。

1. 使用適當的認證以 SSH 登入 VM。 您可以使用 [az vm show](/cli/azure/vm#az-vm-show) 來查看 VM 的公用 IP 位址：

    ```azurecli
    az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
    ```

1. 擴充底層磁碟分割與檔案系統。

    a. 如果已經裝載磁碟，即會加以卸載：

    ```bash
    sudo umount /dev/sdc1
    ```

    b. 使用 `parted` 來檢視磁碟資訊與調整分割區大小：

    ```bash
    sudo parted /dev/sdc
    ```

    使用 `print` 來檢視既有磁碟分割配置的資訊。 輸出類似下列範例，其顯示底層磁碟為 215 GB：

    ```bash
    GNU Parted 3.2
    Using /dev/sdc1
    Welcome to GNU Parted! Type 'help' to view a list of commands.
    (parted) print
    Model: Unknown Msft Virtual Disk (scsi)
    Disk /dev/sdc1: 215GB
    Sector size (logical/physical): 512B/4096B
    Partition Table: loop
    Disk Flags:
    
    Number  Start  End    Size   File system  Flags
        1      0.00B  107GB  107GB  ext4
    ```

    c. 使用 `resizepart` 來展開分割區。 輸入分割區編號 *1* 以及新分割區的大小：

    ```bash
    (parted) resizepart
    Partition number? 1
    End?  [107GB]? 215GB
    ```

    d. 若要結束，請輸入 `quit`。

1. 分割區調整大小後，使用 `e2fsck` 來確認分割區的一致性：

    ```bash
    sudo e2fsck -f /dev/sdc1
    ```

1. 使用 `resize2fs` 來調整檔案系統大小：

    ```bash
    sudo resize2fs /dev/sdc1
    ```

1. 將分割區掛載至所需位置，像是 `/datadrive`：

    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```

1. 若要確認資料磁片已調整大小，請使用 `df -h` 。 下列範例輸出顯示資料磁碟機 */dev/sdc1* 現在是 200 GB：

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        197G   60M   187G   1% /datadrive
    ```

## <a name="next-steps"></a>後續步驟
* 如果您需要更多儲存空間，您也可以[將資料磁碟新增至 Linux VM](add-disk.md)。 
* 如需磁片加密的詳細資訊，請參閱[Linux vm 的 Azure 磁碟加密](disk-encryption-overview.md)。

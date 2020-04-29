---
title: 將 Linux VM 從非受控磁片轉換為受控磁片
description: 如何使用 Azure CLI，將 Linux VM 從非受控磁片轉換為受控磁片。
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 12/15/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 2774dcbd5fc5b01627b965c2c02d870412c8bf77
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "78969700"
---
# <a name="convert-a-linux-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>將 Linux 虛擬機器從非受控磁碟轉換成受控磁碟

如果您現有的 Linux 虛擬機器 (VM) 使用非受控磁碟，您可以將 VM 轉換為使用 [Azure 受控磁碟](../linux/managed-disks-overview.md)。 此程序會轉換 OS 磁碟和任何附加的資料磁碟。

本文說明如何使用 Azure CLI 來轉換 VM。 如果您需要安裝或升級 Azure CLI，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。 

## <a name="before-you-begin"></a>開始之前
* 檢閱[關於移轉至受控磁碟的常見問題](faq-for-disks.md#migrate-to-managed-disks)。

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

* VM 在傳換前使用的原始 VHD 和儲存體帳戶不會遭到刪除。 這些項目會繼續產生費用。 若要避免為這些成果支付費用，請在確認轉換完成之後，刪除原始的 VHD Blob。 如果您需要尋找這些未連結的磁片以刪除它們，請參閱[尋找及刪除未連結的 Azure 受控和非受控磁片](find-unattached-disks.md)一文。

## <a name="convert-single-instance-vms"></a>轉換單一執行個體 VM
本節說明如何將單一執行個體 Azure VM 從非受控磁碟轉換為受控磁碟。 （如果您的 Vm 位於可用性設定組中，請參閱下一節）。您可以使用此程式將虛擬機器從 premium （SSD）非受控磁片轉換為高階受控磁片，或從標準（HDD）非受控磁片轉換為標準受控磁片。

1. 使用 [az vm deallocate](/cli/azure/vm) 將 VM 解除配置。 下列範例會解除配置 `myResourceGroup` 資源群組中名為 `myVM` 的 VM：

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. 使用 [az vm convert](/cli/azure/vm) 將 VM 轉換成受控磁碟。 下列程序會轉換名為 `myVM` 的 VM，包括 OS 磁碟和任何資料磁碟︰

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. 轉換成受控磁碟之後，使用 [az vm start](/cli/azure/vm) 來啟動 VM。 下列範例會啟動 `myResourceGroup` 資源群組中名為 `myVM` 的 VM。

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>轉換可用性設定組中的 VM

如果您想要轉換為受控磁碟的 VM 位於可用性設定組中，您必須先將此可用性設定組轉換為受控可用性設定組。

轉換可用性設定組之前，必須先解除配置可用性設定組中的所有 VM。 在可用性設定組本身轉換成受控可用性設定組之後，請規劃將所有 VM 轉換成受控磁碟。 然後，啟動所有 VM 並繼續像平常一樣運作。

1. 使用 [az vm availability-set list](/cli/azure/vm/availability-set) 來列出可用性設定組中的所有 VM。 下列範例會列出 `myResourceGroup` 資源群組中名為 `myAvailabilitySet` 的可用性設定組中的所有 VM：

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. 使用 [az vm deallocate](/cli/azure/vm) 將所有 VM 解除配置。 下列範例會解除配置 `myResourceGroup` 資源群組中名為 `myVM` 的 VM：

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. 使用 [az vm availability-set convert](/cli/azure/vm/availability-set) 來轉換可用性設定組。 下列範例會轉換 `myResourceGroup` 資源群組中名為 `myAvailabilitySet` 的可用性設定組：

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. 使用 [az vm convert](/cli/azure/vm) 將所有 VM 轉換成受控磁碟。 下列程序會轉換名為 `myVM` 的 VM，包括 OS 磁碟和任何資料磁碟︰

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. 轉換成受控磁碟之後，使用 [az vm start](/cli/azure/vm) 來啟動所有 VM。 下列範例會啟動 `myResourceGroup` 資源群組中名為 `myVM` 的 VM：

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-using-the-azure-portal"></a>使用 Azure 入口網站進行轉換

您也可以使用 Azure 入口網站將非受控磁碟轉換為受控磁碟。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 從入口網站的 VM 清單中選取 VM。
3. 在 VM 刀鋒視窗中，從功能表選取 [磁碟]****。
4. 在 [磁碟]**** 刀鋒視窗頂端，選取 [遷移至受控磁碟]****。
5. 如果您的 VM 位於可用性設定組中，[遷移至受控磁碟]**** 刀鋒視窗上會出現警告，您需要先轉換可用性設定組。 此警告應有一個連結，您可以按一下該連結來轉換可用性設定組。 轉換可用性設定組後，或者如果您的 VM 不在可用性設定組中，請按一下 [遷移]**** 開始將磁碟遷移至受控磁碟的程序。

VM 將會停止，並且在移轉完成後重新啟動。

## <a name="next-steps"></a>後續步驟

如需儲存體選項的詳細資訊，請參閱[Azure 受控磁碟總覽](../windows/managed-disks-overview.md)。
